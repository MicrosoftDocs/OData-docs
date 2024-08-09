---
title:  "What's new in OData .NET 8"
description: Get an overview of new features and changes in OData.NET 8 and guides to help you migrate from OData .NET 7.
date:   2024-08-05
ms.date: 08/05/2024
author: habbes
ms.author: clhabins
---

# What's new in OData .NET 8.0
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-odatalib-v8.md)] [!INCLUDE[appliesto-webapi](../includes/appliesto-odataclient-v8.md)]

## Target Framework

OData .NET libraries target .NET 8. Applications targeting older .NET, .NET Core and .NET Framework versions will need to update to target .NET 8.

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

### Removed `ODataSimplifiedOptions` class

In OData .NET 7, an instance of `ODataSimplifiedOptions` could be injected in the service provider to configure certain behaviour when parsing URLs, and when reading and writing payloads. This led to confusion because we already have other settings classes like `ODataMessageReaderSettings`, `ODataMessageWriterSettings` and `ODataUriParsing`.
In OData .NET 8, the `ODataSimplifiedOptions` class has been removed and its settings properties and methods have been moved to the other classes:

- `EnableReadingKeyAsSegment` and `EnableReadingODataAnnotationWithoutPrefix` properties moved to `ODataMessageReaderSettings` class.
- `EnableWritingKeyAsSegment` property moved to `ODataMessageWriterSettings` class.
- `SetOmitODataPrefix(bool)`, `SetOmitODataPrefix(bool, ODataVersion)`, `GetOmitODataPrefix()`, and `GetOmitODataPrefix(ODataVersion)` methods moved to `ODataMessageWriterSettings` class.
- `EnableParsingKeyAsSegment` property moved to `ODataUriParserSettings` class.

### Removed `IContainerBuilder` and replaced with `IServiceProvider`

OData .NET 7 used a custom interface called `IContainerBuilder` for dependency injection. This meant that developers who would need to inject custom services would have to create a custom implementation. Most custom implementations, including the one used in ASP.NET Core OData were basic wrappers around `Microsoft.Extensions.DepencyInjection`'s `IServiceProvider` and `ServiceCollection` APIs.

OData .NET 8 has removed the `IContainerBuilder` interface and now uses `IServiceProvider` for dependency injection:

- `AddDefaultODataServices(IServiceCollection, ODataVersion, Action<ODataMessageReaderSettings>, Action<ODataMessageWriterSettings>, Action<ODataUriParserSettings>)` extension method introduced for the purpose of registering OData services.
- `IContainerProvider` interface replaced by `IServiceCollectionProvider` interface. It's a provider for the `IServiceProvider` IoC container.
- `ODataBatchOperationRequestMessage` now implements `IServiceCollectionProvider` instead of `IContainerProvider`.
- `ODataBatchOperationResponseMessage` now implements `IServiceCollectionProvider` instead of `IContainerProvider`.

### Changed `ODataError` and related classes

The implementation of `ODataError` in OData .NET 7 did not adhere to [OData specification and guidelines](https://github.com/OData/odata.net/issues/1421#issuecomment-1309673556), for example it exposed an `ODataError.ErrorCode` property instead of `ODataError.Code` and other issues.

In OData .NET 8, changes have been made to `ODataError` to better align with the specification and developer expectation:

- `ODataError.ErrorCode` property has been replaced with `ODataError.Code`
- In OData .NET 8, empty fields (e.g. `code`, `type`, `stacktrace`) are not skipped, but serialized as fields with empty string values.
- In OData .NET 7, `ODataInnerError`'s collection of key-value pairs could be incorrectly serialized into invalid JSON if it containted dates, byte arrays, GUIDs, etc. This has been fixed in OData .NET 8.
- In OData .NET 8, nested inner errors are correctly serialized.
- OData .NET 8 renamed `ToJson()` method to `ToJsonString()`.
- When serializing an `ODataErrorDetail` instance, the OData serializers would write the optional target property ahead of the required message property. In OData .NET 8, we switched the order such that the required message property is written first.
- In OData .NET 7, `ODataError.ToString()` and `ODataJsonWriterUtils.WriteError(ODataError)` could produce different outputs in some cases. In OData .NET 8, they return the same output.
- In OData .NET 8, if a value cannot be serialized, an error is written in place of value for the corresponding property, instead of throwing an exception.

Here's a sample `ODataError` instance:

```csharp
var odataError = new ODataError
{
    Details = new Collection<ODataErrorDetail>
    {
        new ODataErrorDetail { Code = "OEDC1", Message = "OEDM1", Target = "OEDT1" },
        new ODataErrorDetail { Code = "OEDC1", Message = "OEDM1" },
        new ODataErrorDetail {}
    }
};
```

Here's how it would be written in OData .NET 7 with `ODataJsonWriterUtils.WriteError(odataError, /* other parameters */)`:

```json
{
    "error": {
        "code": "",
        "message": "",
        "details": [
            {
                "code": "OEDC1",
                "target": "OEDT1",
                "message": "OEDM1"
            },
            {
                "code": "OEDC1",
                "message": "OEDM1"
            },
            {
                "code": "",
                "message": ""
            }
        ]
    }
}
```

Here's how it would be written in OData .NET 7 with `odataError.ToString()`:

```json
{
  "error": {
    "code": "",
    "message": "",
    "target": "",
    "details": [
      {
        "errorcode": "OEDC1",
        "message": "OEDM1",
        "target": "OEDT1"
      },
      {
        "errorcode": "OEDC1",
        "message": "OEDM1",
        "target": ""
      },
      {
        "errorcode": "",
        "message": "",
        "target": ""
      }
    ],
    "innererror": {}
  }
}
```

Here's how it would be serialized in OData .NET 8 with either `ODataJsonWriterUtils.WriteError(odataError, /* other parameters */)` and `odataError.ToString()`:

```json
{
  "error": {
    "code": "",
    "message": "",
    "details": [
      {
        "code": "OEDC1",
        "message": "OEDM1",
        "target": "OEDT1"
      },
      {
        "code": "OEDC1",
        "message": "OEDM1"
      },
      {
        "code": "",
        "message": ""
      }
    ]
  }
}
```

### Changed `ODataLibraryCompatibility` into a flags enum

OData .NET 8 changed the `ODataLibraryCompatibility` enum into a flags enum, where each bit represents a different compatibility setting that can enable some legacy serialization behavior.

In OData .NET 7, the enum had values like `ODataLibraryCompatibility.Version6`, `Version7` and `Latest`. These values were used to enable or disable certain behaviour based on desired compatibility with a certain library version after migration to a new version. In OData .NET 8, the we moved to a more granular option where you can set various compatibility options individually using bit flags, for example `ODataLibraryCompatibility.UseLegacyVariableCasing`, `WriteTopLevelODataNullAnnotation`, etc. This allows you to retain some legacy behaviour in the serializers when migrating to new versions. The enum still has `Version6` and `Version7` values, which now combine specific flags that are replicate the behaviour that was on by default for that specific version.

### Serialize `variable` scale in lowercase

When writing the `Scale` attribute in XML CSDL, use `variable` in lowercase instead of `Variable`. An enum flag, `ODataLibraryCompatibility.UseLegacyVariableCasing`, was added to support the legacy behavior if desired.

### Changed JSON reader and writer interfaces

This is relevant if you implemented custom JSON reader or JSON writer interfaces.

In OData .NET 7, there were many interfaces required to implement a custom JSON reader or write because the synchronous, asynchronous and streaming APIs were split.
In OData .NET 8 these different APIs have been consolidated. There's now only one `IJsonWriter` interface and only one `IJsonReader` interfaces that a customer writer or reader
would need to implement.

OData .NET 7 also had two interfaces for JsonWriter factories, `IJsonWriterFactory` which accepted a `TextWriter` and `IStreamBasedJsonWriterFactory` accepted a `Stream`.
OData .NET 8 exposes a single factory interface, `IJsonWriterFactory` which accepts a `Stream`.

### Added `ODataMessageWriterSettings.ShouldIncludeAnnotation` for custom instance annotations

In OData .NET 7, custom instance annotations would not be included in the written output if they were not inluded in the `@odata.include-annotations` preference token in preference header.
However, in some cases, customers want to add custom instance annotations to the payload that should always be written regardless of end-user preference.

In OData .NET 8, we introduced a `ShouldIncludeAnnotation` proeprty to the `ODataMessageWriterSettings` that allows developers to force a custom instance annotation to be written. It works as follows:

- The property is a `Func<string, bool>` that receives an annotation name and returns true if the annotation should be written
- If the writer was going to skip a certain annotation (e.g. because it was not part of the annotations filter), it will check this delegate before skipping the annotation. If the delegate returns `true`, then the annotation will not be skipped.

It is important to note that returning false from the delegate does not guarantee that the annotation will not be written. For example, if an annotation was included in the preference header annotations filter, it may still be written even when the delegate returns false.

```csharp
writerSettings.ShouldIncludeAnnotation == (name) => name.StartsWith("important.");
```

### Changed `ODataBinaryStreamValue` to close underlying stream by default

In OData .NET 7, when `ODataBinaryStreamValue` class is initialized using the `ODataBinaryStreamValue(Stream)` constructor, the stream is left open by default upon the object being disposed.

In OData .NET 8, the stream is closed by default when the object objects is disposed. `The ODataBinaryStreamValue(Stream, bool)` constructor overload may be used when leaving the stream open is intended.

### Added `INavigationSourceSegment` interface

`INavigationSourceSegment` interface introduced. The purpose of this new interface is to reduce casting when determining the navigation source associated with the segment. `EntitySetSegment`, `SingletonSegment` and `NavigationPropertySegment` implement this new interface.

### Changed `ODataUrlValidationContext.Messages` property type

In OData .NET 7, the `ODataUrlValidationContext.Messages` property is of type `List<ODataUrlValidationMessage>`.

In OData .NET 8, the property's type has been chaged to `IReadOnlyList<ODataUrlValidationMessage>` to discourage undesired changes to the list.
List<ODataUrlValidationMessage> Messages property defined in ODataUrlValidationContext class has changed to IReadOnlyList<ODataUrlValidationMessage> Messages.
Also, and `AddMessage(ODataUrlValidationMessage)` overload has been added to the `ODataUrlValidationContext` class.

### Deprecated support for JSONP

[JSONP](https://en.wikipedia.org/wiki/JSONP) is a legacy technique for requesting data using JavaScript and  bypass the same-origin policy in web applications. This technique has long been superseded by the adoption of CORS (cross-origin resource sharing), It also poses security concerns. In OData .NET 8, JSONP support in is marked as deprecated, all methods and properties related to JSONP have been marked as obselete and will be removed in the next major version.
