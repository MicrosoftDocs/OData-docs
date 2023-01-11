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

This client-driven paging approach has a number of benefits:
- It's relatively easy to implement
- It allows the client to fetch pages independently. For example, the client can fetch page 5 without having fetched page 4 previously.
- If the client knows the total size of the collection (e.g. using the `$count` query option), the client can compute all the available page numbers and their offset ranges in advance.
