---
title: "Using Utf8JsonWriter for better performance"
description: "This section describes how to configer the OData writer to use a Utf8JsonWriter-based writer for better performance."
author: habbes
ms.author: clhabins
ms.date: 7/27/2022
ms.topic: article
 
---

# Using Utf8JsonWriter for better performance

**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

The `ODataMessageWriter` internally uses an implementation of [`IJsonWriter`](/dotnet/api/microsoft.odata.json.ijsonwriter) (and [`IJsonWriterAsync`](/dotnet/api/microsoft.odata.json.ijsonwriterasync)) to write JSON output to the destination stream. This is a low-level JSON writer that exposes methods for writing individual properties and values. Before v7.12.2, `Microsoft.OData.Core` shipped a single default implementation (internally called `JsonWriter`) that can be constructed using the [`DefaultJsonWriterFactory`](/dotnet/api/microsoft.odata.json.defaultjsonwriterfactory).

It's possible to replace the writer with a custom implementation by injecting your own implementation of [`IJsonWriterFactory`](/dotnet/api/microsoft.odata.json.ijsonwriterfactory) into the dependency-injection container. For more information about configuring dependency injection in OData core library, [see this article](/odata/odatalib/di-support).

Microsoft.OData.Core 7.12.2 introduced a [`Utf8JsonWriter`](/dotnet/api/system.text.json.utf8jsonwriter) wrapper that implements `IJsonWriter` and `IJsonWriterAsync`. This writer is faster and uses less memory than the default `JsonWriter`.

Since the existing `IJsonWriterFactory` is tightly coupled to `TextWriter`, we introduced a new interface `IStreamBasedJsonWriterFactory` which accepts the destination `Stream` as input instead of `TextWriter`. The default implementation of this interface is `DefaultStreamBasedJsonWriterFactory`, which creates instances of the `Utf8JsonWriter`-based writer.

Therefore, in order to use `Utf8JsonWriter`, you have to inject an instance of `DefaultStreamBasedJsonWriterFactory` to the dependency-injection container.

Here's an example:

```csharp
using Microsoft.OData;
using Microsoft.OData.Json;
```

```c#
IContainerBuilder builder = new TestContainerBuilder();
builder.AddDefaultODataServices();
builder.AddService<IStreamBasedJsonWriterFactory>(sp => DefaultStreamBasedJsonWriterFactory.Default);
```

## Limitations and other consideration

### No support for streaming writes

The default `JsonWriter` implements the `IJsonStreamWriter` and `IJsonStreamWriterAsync` interfaces. These interfaces allow you to pipe data directly from an input stream to the output destination. This may be useful when writing the contents of large binary file into base-64 output without buffering everything in memory first. The `Utf8JsonWriter`-based writer does not implement these interfaces yet, which means input from a stream will be buffered entirely in memory before being written out to the destination. If your use case requires writing from a stream input, consider using the default `JsonWriter`. Follow [this issue](https://github.com/OData/odata.net/issues/2422) to find out when this will be resolved.

### Supported .NET versions

This feature is only available with Microsoft.OData.Core on .NET Core 3.1 and .NET 5 and above. It is not supported on Microsoft.OData.Core versions running on .NET Framework.

### Choosing a JavaScriptEncoder

The `DefaultStreamBasedJsonWriterFactory.Default` instance uses the default [JavaScriptEncoder](/dotnet/api/system.text.encodings.web.javascriptencoder.default) used by `Utf8JsonWriter` to encode and escape output text. The default encoder escapes control characters, non-ASCII characters and HTML-sensitive characters like `<`. This differs from OData’s default behavior which does not escape HTML-sensitive characters.

You can provide a different `JavaScriptEncoder`. Instead of injecting `DefaultStreamBasedJsonWriterFactory.Default`, you can create a new instance of the factory and pass the encoder to the constructor. The following snippet uses the [JavaScriptEncoder.UnsafeRelaxedJsonEscaping](/dotnet/api/system.text.encodings.web.javascriptencoder.unsaferelaxedjsonescaping) which does not escape HTML-sensitive characters. This is the default encoder used by ASP.NET Core.

```c#
IStreamBasedJsonWriterFactory factory = new DefaultStreamBasedJsonWriterFactory(JavaScriptEncoder.UnsafeRelaxedJsonEscaping);
```

Note that there’s no built-in JavaScriptEncoder that exactly matches the escaping rules of `Microsoft.OData.Core`’s default `JsonWriter`. The raw output of the two writers will be different. But if the client is [standards-compliant](https://www.ietf.org/rfc/rfc4627.txt), this should not be a problem. Nevertheless, this is something you should take into consideration before adopting the new writer. In general, you should not be highly dependent on a particular escaping output because the internal implementation may change from one version of .NET to another (See relevant discussions [here](https://github.com/dotnet/runtime/issues/70419), [here](https://github.com/dotnet/runtime/issues/54193) and [here](https://github.com/dotnet/runtime/issues/1564#issuecomment-504780719)).