---
title:  "What's new in OData .NET 8"
description: Get an overview of new features and changes in OData.NET 8 and guides to help you migrate from OData .NET 7.
date:   2024-08-05
ms.date: 8/5/2024
author: habbes
ms.author: clhabins
---

# What's new in OData .NET 8.0

**Applies To**:[!INCLUDE[appliesto-odatalib](../includes/appliesto-odatalib-v8.md)] [!INCLUDE[appliesto-webapi](../includes/appliesto-odataclient-v8.md)]

## Target Framework

OData .NET libraries target .NET 8. Applications targeting older .NET, .NET Core and .NET Framework versions will need to update to target .NET 8.
To learn more about supported OData libraries and .NET frameworks, visit out [support policy](/odata/support/support-policy).

## Changes in `Microsoft.OData.Core`

### Changed default JSON writer

OData .NET 8 default JSON writer is now based on `Utf8JsonWriter`. This JSON writer was already available as an option in OData .NET 7 but was not the default.
This writer improves performance and reduces memory usage compared to the previous default, especially when performing serialization using the async API.

in OData .NET 8 the `JavaScriptEncoder.UnsafeRelaxedJsonEscaping` option is used by default. This option provides better consistency with ASP.NET Core applications and can
result in better performance since fewer characters need to be escaped.

This writer has some notable changes in how some values are handled during serialization:

#### String Escaping

The default writer in version 8 uses uppercase letters for unicode code points, the default in version 7 uses lowercase letters

- OData.NET v7 default writer: "Cust1 \ud800\udc05 \u00e4"
- OData .NET v8 default writer: "Cust1 \uD800\uDC05 \u00E4"

Both versions are valid and equivalent, compliant clients should handle them the same way.

#### Number handling

`float.PositiveInfinity` and `double.PositiveInfinity` are written as "INF". `float.NegativeInfinity` and `double.NegativeInfinity` are written as `"-INF"`. This is consistent
with OData .NET 7, but is different from how the values are handled by `Utf8JsonWriter` or `JsonSerializer` by default.

OData .NET 8 serializes a decimal value like `1.0M` as `1.0`, but OData .NET 7 serializes it as `1`.

#### DateTimeOffset

`DateTimeOffset` values where the offset is 0 use `Z` to indicate the timezone, e.g. `2022-11-09T09:42:30Z`. This is consistent with OData .NET 7, but is different from the default
behaviour of `Utf8JsonWriter` which uses `+00:00` like `2022-11-09T09:42:30+00:00`.

#### Switching to the old JSON writer

You can change the JSON writer used by `ODataMessageWriter` and by injecting an implementation of `IJsonWriterFactory` to the service provider attached to the `IODataRequestMessage` or `IODataResponseMessage`. The implementation that corresponds to the old default writer is called `ODataJsonWriterFactory`:

```csharp
services.AddService<IJsonWriterFactory>(ODataJsonWriterFactory.Default);
```

#### Changing `JavaScriptEncoder`

If you want to change which characters are escaped or encoded you can change the `JavaScriptEncoder` used by the underlying `Utf8JsonWriter`. You can do so by injecting an instance of `ODataUtf8JsonWriter` to the service provider
and specifying the instance of `JavaScriptEncoder` to be used:

```csharp
services.AddService<IJsonWriterFactory>(new ODataUtf8JsonWriterFactory(JavaScriptEncoder.Default));
```

### Changed `ODataResource.Properties` property type

In OData .NET 7, the `ODataResource.Properties` property is of type `IEnumerable<ODataProperty>`.

In OData .NET 8, the `ODataResource.Properties`'s type has been changed to `IEnumerable<ODataPropertyInfo>`.

`ODataPropertyInfo` is the base class of `ODataProperty`. The main difference between the two is that `ODataProperty` has a value. The purpose of this change was to make it easier to represent properties in the payloads that don't have a value. Sometimes we need to write metadata about a property that is not included in the payload. For example, in the following payload, the `password` property is missing but the payload includes metadata about the property in the form of `security.omitted` annotation.

```json
{
    "@context":"users/$entity",
    "id": 1,
    …
    "password@security.omitted": “password is read-only”,
    …
    "hobbies@count":10,
}
```

The liberty to add `ODataPropertyInfo` instances in the `ODataResource.Properties` collection makes it possible to read and write metadata of properties that should be exluded from the payload.

One consequence of this change is that in cases where we want to write properties with values, we should filter out properties that don't have value. We can do this by attempting a cast to `ODataProperty` before writing the value:

```csharp
void WriteProperties(ODataResource resource)
{
    foreach (ODataPropertyInfo propertyInfo in resource.Properties)
    {
        if (propertyInfo is ODataProperty property)
        {
            WriteValue(property.Value)
        }
    }
}
```
