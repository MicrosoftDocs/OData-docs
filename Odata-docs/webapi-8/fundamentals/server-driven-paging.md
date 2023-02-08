---
title:  "Server-driven Paging in ASP.NET Core OData 8"
description: Server-driven paging in ASP.NET Core OData 8.
date:   2023-01-11
ms.date: 1/11/2023
author: habbes
ms.author: clhabins
---

# Server-driven Paging in ASP.NET Core OData 8

**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

In [server-driven paging](http://docs.oasis-open.org/odata/odata/v4.0/errata03/os/complete/part1-protocol/odata-v4.0-errata03-os-part1-protocol-complete.html#_Server-Driven_Paging), the server returns the first page of results. If total number of results is greater than the page size, the server returns the first page along with a "next link" that can be used to fetch the next page of results. Each subsequent page should include a next link except for the last page. Using this approach, the client needs to fetch pages sequentially. It cannot jump to an arbitrary page. The next link is included in the response using the [`@odata.nextLink`](http://docs.oasis-open.org/odata/odata-json-format/v4.0/errata03/os/odata-json-format-v4.0-errata03-os-complete.html#odataNext) annotation.

Here's an example of response:

```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Products",
    "value": [
        {
            "Id": 1,
            "Name": "Product 1" 
        },
        {
            "Id": 2,
            "Name": "Product 2" 
        },
    ],
    "@odata.nextLink": "http://localhost:5000/odata/Products?$skiptoken=foobar"
}
```

In this example, the client would make a request to `GET http://localhost:5000/odata/Products?$skiptoken=foobar` to retrieve the next page.

The format of the next link is opaque to the client. This means that the client should not try to decode the link, modify it or make any assumptions about how it is generated or what its components mean. It can only assume that it's a valid URL that will return the next page. This allows the server freedom in how it chooses to generate and interpret the next link. It also allows the server to change the underlying implementation of the next link without affecting the client.

## Configuring paging using `PageSize`

ASP.NET Core OData allow you to configure the page size on the server side using the [`PageSize`](/dotnet/api/microsoft.aspnetcore.odata.query.enablequeryattribute.pagesize) property of the [`[EnableQuery]`](/dotnet/api/microsoft.aspnetcore.odata.query.enablequeryattribute) attribute. This will automatically limit the number of items in the response and include an `@odata.nextLink` property.

The following code snippet demonstrates how to configure the `PageSize` on a controller action:

```csharp
public class ProductsController : ODataController
{
    [EnableQuery(PageSize = 2)]
    public IQueryable<Product> Get()
    {
        return productsCollection;
    }
}
```

This sets the number of items per page to 2. Let's assume the entity set has a total of 5 products. To fetch the first page, simply fetch the entity set:

```http
GET http://localhost:5000/Products
```

**Response**:

```json
{
    "@odata.context": "http://localhost:5000/$metadata#Products",
    "value": [
        {
            "Id": 1,
            "Name": "Product 1"
        },
        {
            "Id": 2,
            "Name": "Product 2"
        }
    ],
    "@odata.nextLink": "http://localhost:5000/Products?$skip=2"
}
```

The next link will fetch the second page:

```http
GET http://localhost:5000/Products?$skip=2
```

```json
{
    "@odata.context": "http://localhost:5000/$metadata#Products",
    "value": [
        {
            "Id": 3,
            "Name": "Product 3"
        },
        {
            "Id": 4,
            "Name": "Product 4"
        }
    ],
    "@odata.nextLink": "http://localhost:5000/Products?$skip=4"
}
```

And finally, the last page:

```http
GET http://localhost:5000/Products?$skip=4
```

```json
{
    "@odata.context": "http://localhost:5000/$metadata#Products",
    "value": [
        {
            "Id": 3,
            "Name": "Product 3"
        }
    ]
}
```

The last page does not include a next link. This is how the client knows that it has reached the end of the collection.

If the request contains query options, they will also be included in the generated next link. Let's take the following request for example:

```http
GET http://localhost:5000/Products?$filter=Price gt 1000&$select=Name
```

The generated next link will look like:

```
http://localhost:5000/Products?$filter=Price%20gt%201000&$select=Name&$skip=2
```

## Combining `PageSize` and `$top`

`PageSize` automatically limits the number of items in the response. The client can still apply `$top` to the request. In this case, `$top` will limit the total number of results rather than the number of items per page.

If the client-requested `$top` is greater than `PageSize`, then the service should return the first page of results, with a next link that fetches the next page of results up to the maximum specified in the client's `$top`. If the `$top` is less that then page size, then server will return the number of items specified in the `$top` query option with no next link.

To illustrate this, let's use our entity set of 5 products as an example, with `PageSize` set to 2. The client makes a request with `$top=3`

```http
GET http://localhost:5000/Products?$top=3
```
**Response:**

```json
{
    "@odata.context": "http://localhost:5000/$metadata#Products",
    "value": [
        {
            "Id": 1,
            "Name": "Product 1"
        },
        {
            "Id": 2,
            "Name": "Product 2"
        }
    ],
    "@odata.nextLink": "http://localhost:5000/Products?$top=1&$skip=2"
}
```

The response contains 2 products and a next link to the next page. The client fetches the next page using the next link:

```http
GET http://localhost:5000/Products?$top=1&$skip=2
```

**Response:**

```json
{
    "@odata.context": "http://localhost:5000/$metadata#Products",
    "value": [
        {
            "Id": 3,
            "Name": "Product 3"
        }
    ]
}
```

This is now the last page. The server returns only one item and no next link.

## Improving paging with `$skiptoken`

You may have noticed that ASP.NET Core OData generates next links using `$skip`. Using `$skip` on large collections may lead to performance degradation in some databases. For more information about the drawbaks of `$skip`, visit the [client-driven paging](/odata/webapi-8/fundamentals/client-driven-paging#drawbacks) article.

OData provides a [`$skiptoken`](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part1-protocol.html#sec_ServerDrivenPaging) query option that can be used in server-driven paging to encode information about the next page of a result. The content of `$skiptoken` is opaque and service-specific. The client should not try to interpret the value or rely on its format. OData clients must not use `$skiptoken` when constructing requests.

ASP.NET Core OData 8 provides support for paging based on `$skiptoken`, but it's not enabled by default. You can enable it using the [`ODataOptions.SkipToken()`](/dotnet/api/microsoft.aspnetcore.odata.odataoptions.skiptoken) method when configuring OData services in your application:

```csharp
services.AddControllers().AddOData(options =>
    options.SetMaxTop(null).SkipToken());
```

Assuming we have the same products entity set as in the previous section and a `PageSize` of 2, here's what the first page would look like:

```http
GET http://localhost:5000/Products
```

**Response**

```json
{
    "@odata.context": "http://localhost:5000/$metadata#Products",
    "value": [
        {
            "Id": 1,
            "Name": "Product 1"
        },
        {
            "Id": 2,
            "Name": "Product 2"
        }
    ],
    "@odata.nextLink": "http://localhost:5000/Products?$skiptoken=Id-2"
}
```

The next link is now generated based on `$skiptoken` rather than `$skip`. This is what the second page would look like:

```http
GET http://localhost:5000/Products?$skiptoken=Id-2
```

```json
{
    "@odata.context": "http://localhost:5000/$metadata#Products",
    "value": [
        {
            "Id": 3,
            "Name": "Product 3"
        },
        {
            "Id": 4,
            "Name": "Product 4"
        }
    ],
    "@odata.nextLink": "http://localhost:5000/Products?$skiptoken=Id-4"
}
```

The `$skiptoken` value is generated using an implementation of [`SkipTokenHandler`](/dotnet/api/microsoft.aspnetcore.odata.query.skiptokenhandler). By default, the built-in [`DefaultSkipTokenHandler`](/dotnet/api/microsoft.aspnetcore.odata.query.defaultskiptokenhandler) class generates the `$skiptoken` based on the values of the key fields and fields in the `$orderby` query option if present. It encodes the key of the last value in the response in the `$skiptoken`'s value. When fetching the next page, it will use this value to determine where it left off and generate a query that's conceptually similar to:

```sql
SELECT * FROM products
WHERE id > 4
LIMIT 2;
```

This query is more efficient and scalable than using `OFFSET` (assuming the `id` field is properly indexed).

You can customize the `$skiptoken` by providing your own implementation of `SkipTokenHandler` and injecting it into the OData service dependency injection (DI) container:

```csharp
services.AddControllers().AddOData(options =>
    options.SetMaxTop(null).SkipToken()
    .AddRouteComponents("", edmModel, routeServices =>
    {
        routeServices.AddSingleton<SkipTokenHandler, CustomSkipTokenHandler>();
    }));
```

To learn more about customizing the `SkipTokenHandler`, visit [this tutorial](/odata/webapi-8/tutorials/custom-skiptokenhandler).
