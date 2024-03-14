---
title:  "Use Utf8JsonWriter"
description: A guide on how to improve performance using Utf8JsonWriter.
date:   2024-01-23
ms.date: 1/23/2024
author: habbes
ms.author: clhabins
---

# Use `Utf8JsonWriter`

**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v8.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-odatalib](../includes/appliesto-odatalib-v7.md)]

The OData core library implements a wrapper on top of the `Utf8JsonWriter` to complement the default JSON writer. This writer offers better performance and fewer memory allocations compared to using the JSON writer that's used by default in `Microsoft.OData.Core`.

To enable `Utf8JsonWriter`, check out any of the following guides depending on the library you are using in your application:

- [Enable `Ut8fJsonWriter` when using `Microsoft.OData.Core` library directly](/odata/odatalib/using-utf8jsonwriter-for-better-performance)
- [Enable `Utf8JsonWriter` when using `Microsoft.AspNetCore.OData` 8.x library](/odata/webapi-8/tutorials/using-utf8jsonwriter-to-improve-serialization-performance)
- [Enable `Ut8fJsonWriter` when using `Microsoft.AspNetCore.OData` 7.x library](/odata/webapi/using-utf8jsonwriter-to-improve-serialization-performance)

When using `Utf8JsonWriter`, consider using the [`UnsafeRelaxedJsonEscaping`](/dotnet/api/system.text.encodings.web.javascriptencoder.unsaferelaxedjsonescaping) encoder to perform escaping as described [here](/odata/odatalib/using-utf8jsonwriter-for-better-performance#choosing-a-javascriptencoder). This encoder is more relaxed and escapes fewer characters. This can improve performance over the default encoder which escapes more characters. Frequent escaping of large strings may result in additional performance and memory overhead.
