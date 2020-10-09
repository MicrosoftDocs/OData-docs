---
title: "Custom OData query options parser"
description: "Describes how to implement and plug in a custom query options parser"
date: 2020-10-06 06:00:00
author: gathogojr
ms.author: jogathog
ms.date: 10/06/2020
---
# Custom OData query options parser
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-core-v7.5.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.5.md)]

OData WebApi V7.5 introduced support for passing [query options in the request body](/odata/webapi/query-options-in-request-body). In this page, we look at how you can implement and plug in your own custom OData query options parser.  
The first step towards that is implementing the `IODataQueryOptionsParser` interface. This interface contains two methods that you need to implement:
- `bool CanParse(HttpRequest)` (or `bool CanParse(HttpRequestMessage)` in .NET FX)
- `Task<string> ParseAsync(Stream)`

The logic for analyzing the request to determine if the parser can parse the content goes into `CanParse(HttpRequest)` method. The parsing logic on the other hand goes into the `ParseAsync(Stream)` method. The return value for the method is a string containing the query options part of the URL.

In the rest of this document, we go through the motions of implementing a simple parser that reads and extracts query options from a POST with `text/xml` content. To keep things brief, let us start from the premise that we have a ready ASP.NET Core Web Application project and `Microsoft.AspNetCore.OData` 7.5.0 or higher package installed.

### 1. Implement `IODataQueryOptionsParser` interface
```c#
public class TextXmlODataQueryOptionsParser : IODataQueryOptionsParser
{
    private static MediaTypeHeaderValue SupportedMediaType =
            MediaTypeHeaderValue.Parse("text/xml");

    public bool CanParse(Microsoft.AspNetCore.Http.HttpRequest request)
    {
        return request.ContentType?.StartsWith(SupportedMediaType.MediaType,
            StringComparison.Ordinal) == true ? true : false;
    }

    public async Task<string> ParseAsync(Stream requestStream)
    {
        using (var reader = new StreamReader(
                requestStream,
                encoding: Encoding.UTF8,
                detectEncodingFromByteOrderMarks: false,
                bufferSize: 1024,
                leaveOpen: true))
        {
            var content = await reader.ReadToEndAsync();
            var document = XDocument.Parse(content);
            var queryOptions = document.Descendants("QueryOption").Select(d =>
            new {
                Option = d.Attribute("Option").Value,
                d.Attribute("Value").Value
            });

            return string.Join("&", queryOptions.Select(d => d.Option + "=" + d.Value));
        }
    }
}

```

### 2. Plug in custom query options parser into the dependency injection container
In _Startup.cs_, we plug in the custom query options parser into the DI container as demonstrated below. Kindly note that the snippet does not comprise the entire code required to bootstrap an OData service.
```c#
var queryOptionsParsers = ODataQueryOptionsParserFactory.Create();
queryOptionsParsers.Insert(0, new TextXmlODataQueryOptionsParser());

// ...
app.UseMvc(routeBuilder =>
{
    // ...
    routeBuilder.MapODataServiceRoute(
        routeName: "ROUTENAME",
        routePrefix: "ROUTEPREFIX",
        configureAction: containerBuilder => containerBuilder
            .AddService(Microsoft.OData.ServiceLifetime.Singleton,
                typeof(IEdmModel),
                _ => modelBuilder.GetEdmModel()) // YOUR EDM MODEL HERE
            .AddService(Microsoft.OData.ServiceLifetime.Singleton,
                typeof(IODataPathHandler),
                _ => new DefaultODataPathHandler())
            .AddService(Microsoft.OData.ServiceLifetime.Singleton,
                typeof(IEnumerable<IODataRoutingConvention>),
                _ => ODataRoutingConventions.CreateDefault())
            .AddService(Microsoft.OData.ServiceLifetime.Singleton,
                typeof(IEnumerable<IODataQueryOptionsParser>),
                _ => queryOptionsParsers));
});
```

### 3. Sample request
POST: `http://localhost:PORT/Movies/$query`  
Content-Type: `text/xml`  
Body:
```xml
<QueryOptions>
<QueryOption Option="$select" Value="Id,Name,Classification,RunningTime"/>
<QueryOption Option="$filter" Value="contains(Name,'li')"/>
<QueryOption Option="$orderby" Value="Name desc"/>
</QueryOptions>
```