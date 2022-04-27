---
title: "Use HttpRequestMessage Extension Methods"
description: Learn how to use HttpRequestMessage extension methods for services that don't use LINQ or ODataQueryOptions.ApplyTo().
ms.date: 7/1/2019
author: madansr7
---
# Use HttpRequestMessage Extension Methods
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

In Microsoft.AspNet.OData, set of HttpRequestMessage extension methods are provided through HttpRequestMessageExtensions. For services that don't use LINQ or ODataQueryOptions.ApplyTo(), those extension methods can offer lots of help.

In Microsoft.AspNetCore.OData, which supports ASP.NET Core, set of HttpRequest extension methods are also provided through HttpRequestExtensions.

They are pretty much symmetrical, and the differences will be noted in the examples below. For further details, please refer to `Microsoft.AspNet.OData.Extensions.HttpRequestMessageExtensions` and `Microsoft.AspNet.OData.Extensions.HttpRequestExtensions`.

## ODataProperties/IODataFeature
OData methods and properties can be GET/SET through:
- ODataProperties (for Microsoft.AspNet.OData) from httpRequestMessage.ODataProperties()
- IODataFeature (for Microsoft.AspNetCore.OData) from httpRequest.ODataFeature()

Each of them includes the followings:

***Path***
The ODataPath of the request.

***PathHandler***
Return DefaultODataPathHandler by default.

***RouteName***
The Route name for generating OData links.

***SelectExpandClause***
The parsed the OData SelectExpandClause of the request.

***NextLink*** 
Next page link of the results, can be set through GetNextPageLink.

For example, we may need generate service root when querying ref link of a navigation property. 
```C#
private string GetServiceRootUri()
{
  var routeName = Request.ODataProperties().RouteName;
  ODataRoute odataRoute = Configuration.Routes[routeName] as ODataRoute;
  var prefixName = odataRoute.RoutePrefix;
  var requestUri = Request.RequestUri.ToString();
  var serviceRootUri = requestUri.Substring(0, requestUri.IndexOf(prefixName) + prefixName.Length);
  return serviceRootUri;
}
```

## GetModel
For Microsoft.AspNet.OData only, get the EDM model associated with the request.
```C#
IEdmModel model = this.Request.GetModel();
```

## GetNextPageLink
Create a link for the next page of results, can be used as the value of `@odata.nextLink`.
For example, the request Url is `https://localhost/Customers/?$select=Name`.
```C#
Uri nextlink = this.Request.GetNextPageLink(pageSize:10);
this.Request.ODataProperties().NextLink = nextlink;
```
Then the nextlink generated is `https://localhost/Customers/?$select=Name&$skip=10`.

## GetETag
Get the etag for the given request.
```C#
EntityTagHeaderValue etagHeaderValue = this.Request.Headers.IfMatch.SingleOrDefault();
ETag etag = this.Request.GetETag(etagHeaderValue);
```

## CreateErrorResponse
For Microsoft.AspNet.OData only, create a HttpResponseMessage to represent an error.
```C#
public HttpResponseMessage Post()
{
  ODataError error = new ODataError()
  {
    ErrorCode = "36",
    Message = "Bad stuff",
    InnerError = new ODataInnerError()
    {
      Message = "Exception message"
    }
  };

  return this.Request.CreateErrorResponse(HttpStatusCode.BadRequest, error);
}
```

Then payload would be like:
```json

    {
      "error":{
      "code":"36",
      "message":"Bad stuff",
      "innererror":{
        "message":"Exception message",
        "type":"",
        "stacktrace":""
      }
    }
```
