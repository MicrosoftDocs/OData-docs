---
title: "Using Utf8JsonWriter to improve Web API's serialization performance"
description: Learn how to configure WebApi to use Utf8JsonWriter to improve serialization performance. 
author: habbes
ms.author: clhabins
ms.date: 8/16/2022

---

# Using Utf8JsonWriter to improve Web API's serialization performance

**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)]

Microsoft.OData.Core version 7.12.2 introduced a new JSON writer that’s based on .NET’s [`Utf8JsonWriter`](/dotnet/api/system.text.json.utf8jsonwriter). This writer is more performant than the default JSON writer used in `Microsoft.OData.Core`. To learn more about the `Utf8JsonWriter`-based writer, [visit this page](/odata/odatalib/using-utf8jsonwriter-for-better-performance). In this article, we are going to show you how to configure your OData Web API application to use the `Utf8JsonWriter`-based writer.

This feature is available in OData Web API starting from v7.5.17, and only available in .NET Core 3.1 and later (i.e. .NET 6+). It is not available on .NET Framework.

There are different methods to configure custom OData services depending on whether you’re using endpoint routing or classic MVC routing.

## Configuration with Endpoint Routing

If you’re using Endpoint routing, then you configure the OData route using `endpoints.MapODataRoute` in the action passed to `app.UseEndpoints` in the `Startup.Configure` method. There’s an overload of `MapODataRoute` that allows you to configure custom OData services. We’ll use this to inject the new factory class into the internal service container. We’ll also need to manually register the default routing conventions since this overload of `MapODataRoute` does not handle that automatically, otherwise routing will not work properly.

Example:

Ensure you have the following using statements to the `Startup.cs` file

```c#
using System.Collections.Generic;
using Microsoft.AspNet.OData.Routing.Conventions;
using Microsoft.OData;
using Microsoft.OData.Json;
```

Then update your `Startup.Configure` method as follows:

```c#
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{

    IEdmModel model = GetEdmModel();

    // Please add "UseODataBatching()" before "UseRouting()" to support OData $batch.
    app.UseODataBatching();

    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapODataRoute(
            "odata", "odata",
            container =>
            {
                container.AddService(Microsoft.OData.ServiceLifetime.Singleton, sp => model);
                container.AddService<IStreamBasedJsonWriterFactory>(Microsoft.OData.ServiceLifetime.Singleton, sp => DefaultStreamBasedJsonWriterFactory.Default);
                container.AddService<IEnumerable<IODataRoutingConvention>>(Microsoft.OData.ServiceLifetime.Singleton,
                    sp => ODataRoutingConventions.CreateDefaultWithAttributeRouting("nullPrefix", endpoints.ServiceProvider));
            });
    });
}
```

## Configuration with MVC routing

If you’re using MVC routing, you can configure custom OData services inside the `Startup.Configure` method by passing configuration action to the `MapODataServiceRoute` method. Using this overload of `MapODataServiceRoute` will also require you to manually register the default routing conventions, otherwise routing will not work properly.

Example:

Add the following using statements to the `Startup.cs` file.

```c#
using System.Collections.Generic;
using Microsoft.AspNet.OData.Routing.Conventions;
using Microsoft.OData;
using Microsoft.OData.Json;
```

Then update the `Startup.Configure` method as follows:

```c#
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    IEdmModel model = GetEdmModel();

    app.UseMvc(builder =>
    {
        builder.Select().Expand().Filter().OrderBy().MaxTop(100).Count();

        builder.MapODataServiceRoute("odata", "odata", container =>
        {
            container.AddService(Microsoft.OData.ServiceLifetime.Singleton, sp => model)
                .AddService<IStreamBasedJsonWriterFactory>(Microsoft.OData.ServiceLifetime.Singleton, sp => DefaultStreamBasedJsonWriterFactory.Default)
                .AddService<IEnumerable<IODataRoutingConvention>>(Microsoft.OData.ServiceLifetime.Singleton, sp =>
                    ODataRoutingConventions.CreateDefaultWithAttributeRouting("odata", builder));
        });
    });
}

```
