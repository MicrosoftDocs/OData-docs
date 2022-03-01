---
title: "PageSize, Top and MaxTop"
description: ""

ms.date: 3/1/2022
---
# PageSize, Top and MaxTop
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

In OData WebApi we support both client driven and client side paginations.

### Client driven pagination
In client-driven paging, we request the server to return the specified number of results. There is no `nextLink` that is returned.

The OData WebApi deals with client-driven paging using `$skip` and `$top` query options.

The `$top` query option requests the number of items in the queried collection to be included in the result.

The `$skip` query option requests the number of items in the queried collection that are to be skipped and not included in the result.

The `MaxTop()` sets the maximum value that can be set for the `$top` query parameter. It does not directly limit the number of items returned per request.

In `Microsoft.AspNetCore.OData v7.x`, we configure `MaxTop()` as follows:

```csharp
app.UseEndpoints(endpoints =>
{
                endpoints.Count().Expand().Select().OrderBy().Filter().MaxTop(100);
                MapVersionedODataRoutes<TModelConfiguration>(...);
});
```

`MaxTop()` only impacts the scenarios in which the request Uri contains $top. If the `$top` value exceeds the `MaxTop` value, you will get an error message.

For example, is we set MaxTop value as 20 and then pass $top query parameter value as 100, you will get an error message as shown below:

```json
{
  "error":{
    "code":"","message":"The query specified in the URI is not valid. The limit of '20' for Top query has been exceeded. The value from the incoming request is '100'."
  }
}
```

### Server driven pagination
In Server-driven paging, the server returns the first page of results. If total number of results is greater than the page size, the server returns the first page along with a nextlink that can be used to fetch the next page of results.

We set in the `PageSize` in a controller method as follows:

```csharp
[EnableQuery(PageSize =5)]
public IQueryable<Book> Get()
{
    return _db.Books.AsQueryable<Book>();
}
```

Sample response:
```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Books",
    "value": [
        {
            "Id": 1,
            "ISBN": "978-0-321-87758-1",
            "Title": "Essential C#5.0",
            "Author": "Mark Michaelis"
        },
        {
            "Id": 2,
            "ISBN": "063-6-920-02371-5",
            "Title": "Enterprise Games",
            "Author": "Michael Hugos",
        }
    ],
    "@odata.nextLink": "http://localhost:5000/odata/Books?$skip=2"
}
```

### Configuring both PageSize and $top
`PageSize` does not need to be dependent on `$top` -- the two are composable. If the client-requested `$top` is greater than `PageSize`, then the service should return the first page of results, with a next link that fetches the next page of results up to the maximum specified in the client's `$top`.

For example, if there are 1000 rows but the client specifies `$top=100` and the service has a `PageSize` of 50, the service would return the first 50 records with a `nextLink` to return a result containing the remaining 50 of the 100 requested rows with no nextlink.