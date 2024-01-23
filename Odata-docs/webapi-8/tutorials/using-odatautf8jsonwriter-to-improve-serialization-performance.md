---
title:  "Using ODataUtf8JsonWriter to improve serialization performance"
description: Learn how to configure ASP.NET Core OData 8 to use ODataUtf8JsonWriter to improve serialization performance. 
date:   2023-08-11
ms.date: 08/11/2023
author: habbes
ms.author: clhabins
---

# Using ODataUtf8JsonWriter to improve serialization performance

**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

`Microsoft.OData.Core` version 7.12.2 introduced a new JSON writer that writes directly to a `Stream` rather than using a `TextWriter`. It also introduced a default implementation of this "stream-based" JSON writer that’s based on .NET’s [`Utf8JsonWriter`](/dotnet/api/system.text.json.utf8jsonwriter). Internally, it's referred to as `ODataUtf8JsonWriter`. This writer offers better performance than the default JSON writer used in `Microsoft.OData.Core`. To learn more about the `ODataUtf8JsonWriter`, [visit this page](../../odatalib/v7/using-utf8jsonwriter-for-better-performance.md). In this article, we are going to show you how to configure your ASP.NET Core OData application to use the `ODataUtf8JsonWriter`.

This feature is available in ASP.NET Core OData version 8.0.11 or later.

You can register OData services through use of `AddRouteComponents` method. To use the `ODataUtfJsonWriter`, register the `IStreamBasedJsonWriterFactory` service. The `Microsoft.OData.Core` library provides a default implementation of this interface - `DefaultStreamBasedJsonWriterFactory.Default`.

Here's how you can do this in your application:

In your application startup code, import the `Microsoft.OData.Json` namespace and register the `IStreamBasedJsonWriterFactory` service as follows:

```c#
services.AddControllers()
    .AddOData(options =>
    options.AddRouteCompontents("odata", model, services =>
    {
        services.AddSingleton<IStreamBasedJsonWriterFactory>(_ => DefaultStreamBasedJsonWriterFactory.Default);
    });
```

That is all that's required to substitute the default JSON writer with the `ODataUtf8JsonWriter`.
