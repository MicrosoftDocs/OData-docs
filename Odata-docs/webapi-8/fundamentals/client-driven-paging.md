---
title:  "Client-driven paging in ASP.NET Core OData 8"
description: Client-driven paging in ASP.NET Core OData 8.
date:   2023-01-10
ms.date: 1/10/2023
author: habbes
ms.author: clhabins
---

# Client-driven paging in ASP.NET Core OData 8
**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

The client can implement paging by telling the OData service how many items to return for a given request. The `$top` query option is used to limit the number of results and the `skip` query option used to specify the offset. For example `GET /Customers?$skip=3$top=5` will return up to 5 items starting from the 4th item in the collection (i.e. after skipping the first 3).

Using these two query options the client can divide a large collection into separate pages and fetch the pages in separate requests. For example, if our collection has 25 items, and we want each page to have 10 items, then we can fetch the different page as follows:
- First page: `$top=10`
- Second page: `$skip=10&$top=10`
- Third page: `$skip=20&top=10` (The last page will return only 5 items)

By default, ASP.NET Core OData 8 limits the maximum value for `$top` to 0. So if you add `$top=2` to your request, you would get an error with a message like:

```json
{
    "error": {
        "code": "",
        "message": "The query specified in the URI is not valid. The limit of '0' for Top query has been exceeded. The value from the incoming request is '2'."
}
```

You can configure the maximum top value using the [`ODataOptions.SetMaxTop()`](/dotnet/api/microsoft.aspnetcore.odata.odataoptions.setmaxtop) when adding OData services to your application. The following snippet would set the maximum allowed `$top` value to 100.

```c#
services.AddOData(options =>
    options.SetMaxTop(100)
    //...
    );
```

Now this will support values of up to 100, but return an error if `$top` exceeds 100.

You can also remove the limit by setting it to `null`:

```c#
services.AddOData(options =>
    options.SetMaxTop(null)
    // ...
    );
```

Next, we add the [`EnableQuery`](/dotnet/api/microsoft.aspnetcore.odata.query.enablequeryattribute) attribute to the controller action that handles the request. This attribute automatically applies the OData query options in the request to the results returned by the controller action.

```c#
public class ProductsController : ODataController
{
    [EnableQuery]
    public IQueryable<Product> Get()
    {
        return productsCollection;
    }
}
```

## Benefits of this approach

This client-driven paging approach has a number of benefits:
- It's relatively easy to implement
- It allows the client to fetch pages independently and to jump to an arbitrary page. For example, the client can fetch page 5 without having fetched page 4 previously.
- If the client knows the total size of the collection (e.g. using the `$count` query option), the client can compute the total number of pages as well as generate links for each page in advance.

## Drawbacks

Paging based on `$top` and `$skip` has a number of drawbacks that may make unsuitable in some scenarios.

This approach is sensitive to changes in that data source which may lead the page contents to be unstable. To illustrate this, let's assume we have a data source with the following 5 items and we want to fetch 2 items per page:
```json
[
    { "Id": 1, "Name": "Product 1" },
    { "Id": 2, "Name": "Product 2" },
    { "Id": 3, "Name": "Product 3" },
    { "Id": 4, "Name": "Product 4" },
    { "Id": 5, "Name": "Product 5" }
]
```
The client can fetch the first page with 

```http
GET /Products?$top=2
```
This returns the following result:

```json
[
    { "Id": 1, "Name": "Product 1" },
    { "Id": 2, "Name": "Product 2" }
]
```

Let's say that product 2 is removed from the data source before the client fetches the second page. The data source looks like:
```json
[
    { "Id": 1, "Name": "Product 1" },
    { "Id": 3, "Name": "Product 3" },
    { "Id": 4, "Name": "Product 4" },
    { "Id": 5, "Name": "Product 5" }
]
```

The client can fetch the second page with

```http
GET /Products?$top=2&$skip=2
```

This returns the following result:

```json
[
    { "Id": 4, "Name": "Product 4" },
    { "Id": 5, "Name": "Product 5" }
]
```

Notice the client did not see product 3 because it was removed before it fetched page 2. In a similar way, if new products were added to the data source before product 2, then the client would see product 2 again in page 2 or later pages.

Using `$skip` can also lead to performance issues in large datasets in some data source implementation. In many SQL-based database systems `$skip` is implemented using `OFFSET`. A request like `/Products?$skip=50000&$top=100` could be translated in the following SQL query:

```sql
SELECT * FROM products
LIMIT 100, OFFSET 50000;
```
This query will return 100 rows, from row 50,001 to row 50,100. However it will scan through all the first 50,100 rows in the table, then drop the first 50,000. The larger the offset, the longer the query will take.

This makes `$skip`-based pagination unsuitable for large datasets in many database systems. An alternative approach would be to keep track of where you left off using a unique ordered field. In our example, we can use the `Id` field to keep track of the last product we saw. using this technique, the client can fetch the first page with the following request:

```http
GET /Products?$top=2
```
This will return the following result:

```json
[
    { "Id": 1, "Name": "Product 1" },
    { "Id": 2, "Name": "Product 2" }
]
```
When the client gets this ersult, it records that the last product it saw was the product with ID 2. So, to fetch the next page, it makes the following request:
```http
GET /Products?$filter=Id gt 2&$top=2
```
This fetches only products with id greater than 2.

This roughly translate to the following SQL query:
```sql
SELECT * FROM products
WHERE id > 2
LIMIT 2;
```
If the database has an ordered index on the `id` column, the this query will scale well and perform significantly better on larger datasets and pages than if it wa using `OFFSET`.

This approach also makes the pages more resilient to concurrent changes in the data source. But we lose some features like being able to easily compute the total number pages or to jump to a random page. We are limited to visit the pages sequentially.

This approach requires the client to keep track of the last fetched record. It also needs to know which field(s) to use as the "cursor". A more robust approach would be to let the server handle these implementation details and provide a link in the response that the client can use to fetch the next page. To learn more about how you can implement this in OData, visit the [server-driven paging documentation](/odata/webapi-8/fundamentals/server-driven-paging).
