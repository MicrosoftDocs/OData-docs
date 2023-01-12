---
title:  "Server-driven paging in ASP.NET Core OData 8"
description: Server-driven paging in ASP.NET Core OData 8.
date:   2023-01-11
ms.date: 1/11/2023
author: habbes
ms.author: clhabins
---

# Server-driven paging in ASP.NET Core OData 8
**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

In [server-driven paging](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part1-protocol.html#sec_ServerDrivenPaging), the server returns the first page of results. If total number of results is greater than the page size, the server returns the first page along with a "next link" that can be used to fetch the next page of results. Each subsequent page should include a next link except for the last page. Using this approach, the client needs to fetch pages sequentially. It cannot jump to an arbitrary page. The next link is included in the response using the [`@odata.nextLink`](http://docs.oasis-open.org/odata/odata-json-format/v4.01/odata-json-format-v4.01.html#sec_ControlInformationnextLinkodatanextL) annotation.

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

The format of the next link is opaque to the client. This means that the client should not try to decode the link, modify it or make any assumptions about how it is generated or what its components mean. It can only assume that it's a valid URL that will return the next page. This allows the server freedom in how it chooses to generate and interpret the next link. It allows the server to change the underlying implementation of the next link without affecting the client.

## Configuring paging using `PageSize`

ASP.NET Core OData allow you to configure the page size on the server side using the [`PageSize`](/dotnet/api/microsoft.aspnetcore.odata.query.enablequeryattribute.pagesize) property of the [`[EnableQuery]`](/dotnet/api/microsoft.aspnetcore.odata.query.enablequeryattribute) attribute. This will automatically limit the number of items in the response and include an `@odata.nextLink` property.

The following code snippet demonstrates how to configure the `PageSize` on a controller action:

```c#
public class ProductsController : ODataController
{
    [EnableQuery(PageSize = 2)]
    public IQueryable<Product> Get()
    {
        return productsCollection;
    }
}
```

This sets the number of items per page to 2. Let's assume the collection has a total of 5 products. To fetch the first page, simply fetch the entity set:

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

## Combining both `PageSize` and `$top`

`PageSize` automatically limits the number of items in the response. The client can still apply `$top` to the request. In this case, `$top` will limit the total number of results rather than the number of items per page.

If the client-requested `$top` is greater than `PageSize`, then the service should return the first page of results, with a next link that fetches the next page of results up to the maximum specified in the client's `$top`. If the `$top` is less that then page size, then server will return the number of items specified in the `$top` query option with no next link.

To illustrate this, let's use our collection of 5 products as an example, with `PageSize` set to 2. The client makes a request with `$top=3`

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
This is now the last page. The server returns only item and no next link.