---
title:  "Using ODataUtf8JsonWriter to improve serialization performance"
description: Learn how to configure ASP.NET Core OData 8 to use Utf8JsonWriter to improve serialization performance. 
date:   2023-08-11
ms.date: 08/11/2023
author: habbes
ms.author: clhabins
---

# Using ODataUtf8JsonWriter to improve serialization performance

**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

`Microsoft.OData.Core` version 7.12.2 introduced a new JSON writer that writes to a `Stream` instead of a `TextWriter`. It also introduced a default implementation of this "stream-based" JSON writer that’s based on .NET’s [`Utf8JsonWriter`](/dotnet/api/system.text.json.utf8jsonwriter). Internally, it's referred to as `ODataUtf8JsonWriter`. This writer is more performant than the default JSON writer used in `Microsoft.OData.Core`. To learn more about the stream-based writer, [visit this page](../../odatalib/v7/using-utf8jsonwriter-for-better-performance.md). In this article, we are going to show you how to configure your ASP.NET Core OData application to use the `ODataUtf8JsonWriter`.

This feature is available in ASP.NET Core OData 8 starting from v8.0.11, and only available in .NET Core 3.1 and later (i.e. .NET 6+). It is not available on .NET Framework.

You can configure custom services when registering new OData route components using the `AddRouteComponents` method. In this case, you need to
inject a service that implements `IStreamBasedJsonWriterFactory`. The `Microsoft.OData.Core` library provides a default implementation of this interface: `DefaultStreamBasedJsonWriterFactory.Default`.

Here's an example of how to add this to your application:

Add the following using statement to the `Startup.cs` file, or the file where you configure application services:

```c#
using Microsoft.OData.Json;
```

Then register the new factory inside the Startup.ConfigureServices method or the method where you’re configuring application services if not Startup.ConfigureServices:

```c#
services.AddControllers()
    .AddOData(options =>
    options.AddRouteCompontents("odata", model, services =>
    {
        services.AddSingleton<IStreamBasedJsonWriterFactory>(_ => DefaultStreamBasedJsonWriterFactory.Default);
    });
```

That's it. This is all the configuration that's needed to start using the new writer.
