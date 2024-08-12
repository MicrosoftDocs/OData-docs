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

For example, if you want to add custom configurations to OData services, you'd follow these steps:

Create implementation of `IODataRequestMessage` or `IODataResponseMessage` that also implements `IServiceCollectionProvider`

```csharp
public class ODataMessage : IODataRequestMessage, IODataResponseMessage, IServiceCollectionProvider
{
    // ...
    public ODataMessage(..., IServiceProvider services)
    {
        ServiceProvider = services;
    }
    public IServiceProvider ServiceProvider { get; private set; }
}
```

Create service collection and add default OData services:

```csharp
using Microsoft.Extensions.DependencyInjection;

var serviceCollection = new ServiceCollection();
serviceCollection.AddDefaultODataServices();
```

Configure custom services:

```csharp
serviceCollecton.AddSingleton<IJsonWriterFactory>(ODataJsonWriterFactory.Default);
```

Build service provider:

```csharp
IServiceProvider services = serviceCollection.BuildServiceProvider();
```

Add service provider to message writer or reader:

```csharp
var message = new ODataMessage(..., services);
var messageWriter = new ODataMessageWriter(message, messageWriterSettings, model);
```

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

In OData .NET 7, the enum had values like `ODataLibraryCompatibility.Version6`, `Version7` and `Latest`. These values were used to enable or disable certain behaviour based on desired compatibility with a certain library version after migration to a new version. In OData .NET 8, we moved to a more granular option where you can set various compatibility options individually using bit flags, for example `ODataLibraryCompatibility.UseLegacyVariableCasing`, `WriteTopLevelODataNullAnnotation`, etc. This allows you to retain some legacy behaviour in the serializers when migrating to new versions. The enum still has `Version6` and `Version7` values, which now combine specific flags that replicate the behaviour that was on by default for that specific version.

### Serialize `variable` scale in lowercase

When writing the `Scale` attribute in XML CSDL, use `variable` in lowercase instead of `Variable`. An enum flag, `ODataLibraryCompatibility.UseLegacyVariableCasing`, was added to support the legacy behavior if desired.

### Changed JSON reader and writer interfaces

This is relevant if you implemented custom JSON reader or JSON writer interfaces.

In OData .NET 7, there were many interfaces required to implement a custom JSON reader or writer because the synchronous, asynchronous and streaming APIs were split.
In OData .NET 8 these different APIs have been consolidated. There's now only one `IJsonWriter` interface and only one `IJsonReader` interface that a custom writer or reader
would need to implement.

OData .NET 7 also had two interfaces for JsonWriter factories, `IJsonWriterFactory` which accepted a `TextWriter` and `IStreamBasedJsonWriterFactory` accepted a `Stream`.
OData .NET 8 exposes a single factory interface, `IJsonWriterFactory` which accepts a `Stream`.

### Added `ODataMessageWriterSettings.ShouldIncludeAnnotation` for custom instance annotations

In OData .NET 7, custom instance annotations would not be included in the written output if they were not inluded in the `@odata.include-annotations` preference token in preference header.
However, in some cases, customers want to add custom instance annotations to the payload that should always be written regardless of end-user preference.

In OData .NET 8, we introduced a `ShouldIncludeAnnotation` property to the `ODataMessageWriterSettings` that allows developers to force a custom instance annotation to be written. It works as follows:

- The property is a `Func<string, bool>` that receives an annotation name and returns true if the annotation should be written
- If the writer was going to skip a certain annotation (e.g. because it was not part of the annotations filter), it will check this delegate before skipping the annotation. If the delegate returns `true`, then the annotation will not be skipped.

It is important to note that returning false from the delegate does not guarantee that the annotation will not be written. For example, if an annotation was included in the preference header annotations filter, it may still be written even when the delegate returns false.

```csharp
writerSettings.ShouldIncludeAnnotation == (name) => name.StartsWith("important.");
```

### Changed `ODataBinaryStreamValue` to close underlying stream by default

In OData .NET 7, when `ODataBinaryStreamValue` class is initialized using the `ODataBinaryStreamValue(Stream)` constructor, the stream is left open by default upon the object being disposed.

In OData .NET 8, the stream is closed by default when the object is disposed. `The ODataBinaryStreamValue(Stream, bool)` constructor overload may be used when leaving the stream open is intended.

### Added `INavigationSourceSegment` interface

`INavigationSourceSegment` interface introduced. The purpose of this new interface is to reduce casting when determining the navigation source associated with the segment. `EntitySetSegment`, `SingletonSegment` and `NavigationPropertySegment` implement this new interface.

### Changed `ODataUrlValidationContext.Messages` property type

In OData .NET 7, the `ODataUrlValidationContext.Messages` property is of type `List<ODataUrlValidationMessage>`.

In OData .NET 8, the property's type has been chaged to `IReadOnlyList<ODataUrlValidationMessage>` to discourage undesired changes to the list.
An `AddMessage(ODataUrlValidationMessage)` overload has been added to the `ODataUrlValidationContext` class to make it possible to add validation messages.

### Deprecated support for JSONP

[JSONP](https://en.wikipedia.org/wiki/JSONP) is a legacy technique for requesting data using JavaScript and  bypass the same-origin policy in web applications. This technique has long been superseded by the adoption of CORS (cross-origin resource sharing), It also poses security concerns. In OData .NET 8, JSONP support in is marked as deprecated, all methods and properties related to JSONP have been marked as obselete and will be removed in the next major version.

## Changes in `Microsoft.OData.Client`

### Added support for `IHttpClientFactory`

In OData .NET 7, the `DataServiceContext` class had a `HttpClientHandlerProvider` that allowed developer to provide custom instances of `HttpClientHandler` for making requests. The `HttpClientHandlerProvider` was based on a custom `IHttpClientHandlerProvider` interface. Customers requested support for the familiar `IHttpClientFactory` interface, but we could not support because it would result in breaking changes.

In OData .NET 8, we introduced support for [`IHttpClientFactory`](/dotnet/core/extensions/httpclient-factory) `DataServiceContext` now has a `HttpClientFactory` property of type `IHttpClientFactory`. This allows developers to provide custom `HttpClient` instances and control their lifetimes. Subsequently, we have removed the `IHttpClientHandlerProvider` interface and the `DataServiceContext.HttpClientProvider` property.

Here's a basic code sample of using injecting an `IHttpClientFactory` instance to OData Client's `DataServiceContext`. In the following examples, we make use of the following packages:

- [`Microsoft.Extensions.DependencyInjection`](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection)
- [`Microsoft.Extension.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)

We also assume the following `using` statement exists at the top of the soure code:

```c#
using Microsoft.Extensions.DependencyInjection
```

We can obtain an instance of `IHttpClientFactory` by injecting in a `ServiceCollection` instance:

```c#
using Microsoft.Extensions.DependencyInjection

ServiceCollection serviceCollection = new ServiceCollection();
serviceCollection.AddHttpClient();
IServiceProvider services = serviceCollection.BuildServiceProvider();

// Create a new DataServiceContext instance that points to our OData service
var client = new DefaultContainer(new Uri("https://services.odata.org/V4/TripPinServiceRW/"));

// Configure te IHttpClientFactory to use
client.HttpClientFactory = services.GetRequiredService<IHttpClientFactory>();

// Make a request
var people = await client.People.ExecuteAsync();

foreach (var p in people)
{
    Console.WriteLine($"{p.FirstName} {p.LastName}");
}
```

Let's look at another example. This time we'll configure the underlying `HttpMessageHandler` with custom credentials and lifetime. To configure the handler, we need
to use a named client by using the `AddHttpClient` overload that accepts a string. Since OData Client isn't aware of custom client names, we'll use an empty string as the name.

```csharp
using Microsoft.Extensions.DependencyInjection

ServiceCollection serviceCollection = new ServiceCollection();

serviceCollection.AddHttpClient("")
.ConfigurePrimaryHttpMessageHandler(_ =>
{
    // configure credentials
    return new HttpClientHandler
    {
        Credentials = new NetworkCredential(secureUsername, securePassword)
    };
})
// configure lifetime
.SetHandlerLifetime(TimeSpan.FromMinutes(5));

client.HttpClientFactory = services.GetRequiredService<IHttpClientFactory>();

// Make a request
var people = await client.People.ExecuteAsync();

foreach (var p in people)
{
    Console.WriteLine($"{p.FirstName} {p.LastName}");
}
```

### Renamed `IBaseEntityType.Context` to `DataServiceContext`

In OData .NET 7, the `IBaseEntityType` has property called `Context` of type `DataServiceContext`. The `IBaseEntityType` is a base type used for all entity type classes generated by our code-generation tools (OData Connected Service and OData CLI). We had many reports the inherited `Context` properties would conflict with entity types which also had a property called `Context`. To avoid this occurrence, OData .NET 8 has renamed the property to `DataServiceContext`, which has fewer chances of collisions.

### Changed key lookup in `Where` clause to use `$filter` by default

In OData .NET 7, running a LINQ query that performed a key look up, like `context.People.Where(p => p.Id == 10)` would result in a request URL that fetches an entity by ID, i.e. `/People(10)`. In all other cases, the LINQ `Where` clause maps to a `$filter` query. This inconsistent handling of the `Where` clause would sometimes cause confusions and unexpected results. OData .NET 7 introduced a setting `DataServiceContext.KeyComparisonGeneratesFilterQuery` to configure this behavior. When set to true, `Where` clause that performs a key lookup would generated a `$filter` query that performs a key lookup, i.e. `$filter=Id eq 10`.

In OData .NET 8, `Where` clause generates a `$filter` query even for key lookups by default. The `DataServiceContext.KeyComparisonGeneratesFilterQuery` setting is still available. It now defaults to `true`. You can restore the old behaviour by setting this flag to `false`. However, this setting has been marked as deprecated and could be removed in a future major version.

The recommended way to fetch an entity by ID is to use the `ByKey` method, e.g. `context.People.ByKey(10)`. This generates a URL to retrieve the entity by id (`/People(10)`). We encourage developers that use `Where` for key-lookups to switch to using `ByKey()` instead.

Using `$filter` when you intend to fetch an entity by key can lead to surprising results. For example, consider the following snippet of code:

```csharp
var context = new Container(new Uri("https://services.odata.org/TripPinRESTierService/"));
var friend = context.People.Where(p => p.UserName == "russellwhyte").Select(p => p.BestFriend);

Console.WriteLine(friend.FirstName);
```

In OData .NET 7, the following OData URL would be generated by default: `https://services.odata.org/TripPinRESTierService/People('russellwhyte')/BestFriend`. Notice that the selected `BestFriend` is added as a navigation property segment to the URL. This request returns the entity corresponding to the best friend at the top level of the response:

```json
{
    "@odata.context": "https://services.odata.org/TripPinRESTierService/(S(q5f0myjzjtfondm5l1a5qsbw))/$metadata#People/$entity",
    "UserName": "scottketchum",
    "FirstName": "Scott",
    "LastName": "Ketchum",
    ...
}
```

However, in OData .NET 8, this code would generate the following URL: `https://services.odata.org/TripPinRESTierService/People?$filter=UserName eq 'russellwhyte'&$expand=BestFriend`. Notice that the request filters the entity set collection and also includes the navigation property using the `$expand` query. This means the result will be a collection containing a single entity, which has an expanded `BestFriend` property:

```csharp
{
    "@odata.context": "https://services.odata.org/TripPinRESTierService/(S(pbicrumqvat4igher2mbadb3))/$metadata#People(BestFriend())",
    "value": [
        {
            "UserName": "russellwhyte",
            "FirstName": "Russell",
            "LastName": "Whyte",
            ...
            "BestFriend": {
                "UserName": "scottketchum",
                "FirstName": "Scott",
                "LastName": "Ketchum",
                ...
            }
        }
    ]
}
```

This means that trying to access the result with `friend.FirstName` will fail and throw an exception, because the result is not the friend entity, but a collection that contains the filtered entity with the `BestFriend` property attached to it. Therefore in OData .NET 8 we would have to adjust the code as follows:

```csharp
// fetch people with expanded navigation property using OData URI People?$expand=BestFriend
var people = await context.People.Expand('BestFriend').ExecuteAsync();
// perform a local LINQ transform on the result data
var bestFriends = people.Select(p => p.BestFriend);

Console.WriteLine(bestFriends.First().FirstName);
```

Alternatively we could use the `ByKey` method:

```csharp
var friend = await ctx.People.ByKey("russelwhyte").Select(p => p.BestFriend).GetValueAsync();
Console.WriteLine(friend.FirstName);
```

Alternatively, we could set `DataServiceContext.KeyComparisonGeneratesFilterQuery` to false, but it's no longer recommended:

```csharp
var context = new Container(new Uri("https://services.odata.org/TripPinRESTierService/"));
context.KeyComparisonGeneratesFilterQuery = false;

var friend = context.People.Where(p => p.UserName == "russellwhyte").Select(p => p.BestFriend);

Console.WriteLine(friend.FirstName);
```

### Dropped support for `HttpWebRequest`

OData .NET 8 removed support for `HttpWebRequest`, `HttpClient` should be used instead.

Subsequently the following related APIs have also been removed:

- The `HttpWebRequestMessage` class was removed.
- The `HttpRequestTransportMode` enum was removed.
- The `DataServiceContext.HttpRequestTransportMode` property was removed.

### Other obsolete APIs that have been removed

- Obsolete `Credentials` property dropped from `DataServiceClientRequestMessage` abstract class. The recommended way to configure credentials is through `HttpClientHandler` that can be provided using `IHttpClientFactory`.
- Obsolete `Credentials` property dropped from `HttpClientRequestMessage` class. The recommended way to configure credentials is through `HttpClientHandler` that can be provided using `IHttpClientFactory`.
- Obsolete `Credentials` property dropped from `DataServiceContext` class. The recommended way to configure credentials is through `HttpClientHandler` that can be provided using `IHttpClientFactory`.
- Obsolete `ReadWriteTimeout` property dropped from `DataServiceClientRequestMessage` abstract class. This property would be used with `HttpWebRequestMessage`. The `Timeout` property should be used instead.
- Obsolete `ReadWriteTimeout` property dropped from `HttpClientRequestMessage` class. This property would be used with `HttpWebRequestMessage`. The `Timeout` property should be used instead.
- In `DataServiceClientRequestMessageArgs` class, the `DataServiceClientRequestMessageArgs(string, Uri, bool, bool, IDictionary<string, string>)` constructor has changed to `DataServiceClientRequestMessageArgs(string, Uri, bool, IDictionary<string, string>)`. The boolean `useDefaultCredentials` parameter is no longer supported.
- In `DataServiceClientRequestMessageArgs` class, the `DataServiceClientRequestMessageArgs(string, Uri, bool, bool, IDictionary<string, string>, IHttpClientHandlerProvider)` constructor has changed to `DataServiceClientRequestMessageArgs(string, Uri, bool, IDictionary<string, string>, IHttpClientFactory)`. The boolean `useDefaultCredentials` parameter is no longer supported.
- In `DataServiceClientRequestMessageArgs` class, the `UseDefaultCredentials` property dropped from `DataServiceClientRequestMessageArgs` class. The recommended way to configure credentials is through `HttpClientHandler` that can be provided using `IHttpClientFactory`.
- Obsolete `IncludeTotalCount()` method was dropped from `DataServiceQuery<TElement>` class. Use `IncludeCount()` method.
- Obsolete `IncludeTotalCount(bool)` method was dropped from `DataServiceQuery<TElement>` class: Use `IncludeCount(bool)` the method.
- Obsolete `TotalCount` property was dropped from `QueryOperationResponse` class. Use `Count` property.
- Obsolete `TotalCount` property was dropped from `QueryOperationResponse<T>` class. Use `Count` property.
- Obsolete `CreateODataDeltaReader(IEdmEntitySetBase, IEdmEntityType)` method dropped from `ODataMessageReader` class. Use `CreateODataDeltaResourceSetReader(IEdmEntitySetBase, IEdmStructuredType)` method.
- Obsolete `CreateODataDeltaReaderAsync(IEdmEntitySetBase, IEdmEntityType)` method dropped from `ODataMessageReader` class. Use `CreateODataDeltaResourceSetReader(IEdmEntitySetBase, IEdmStructuredType)` method.
- Obsolete `CreateODataDeltaWriter(IEdmEntitySetBase, IEdmEntityType)` method dropped from `ODataMessageReader` class. Use `CreateODataDeltaResourceSetWriter(IEdmEntitySetBase, IEdmStructuredType)` method.
- Obsolete `CreateODataDeltaWriterAsync(IEdmEntitySetBase, IEdmEntityType)` method dropped from `ODataMessageReader` class. Use `CreateODataDeltaResourceSetWriterAsync(IEdmEntitySetBase, IEdmStructuredType)` method.
- Obsolete `Expressions` property dropped from `AggregateToken` class. Use `AggregateExpressions` property.
- Obsolete `Expressions` property dropped from `AggregateTransformationNode` class. Use `AggregateExpressions` property.
- Obsolete `EntityTypeInvalidKeyKeyDefinedInBaseClass` validation rule dropped from `ValidationRules` class. Use `EntityTypeInvalidKeyKeyDefinedInAncestor` validation rule.
- Obsolete `EntityTypeKeyMissingOnEntityType` validation rule dropped from `ValidationRules` class. Use `NavigationSourceTypeHasNoKeys` validation rule.

## Changes in `Microsoft.OData.Edm`

### Added `UseDefault` property to `IEdmVocabularyAnnotation` interface

Added `UsesDefault` property to `IEdmVocabularyAnnotation` to support creating vocabulary annotations with default values. We do not write an `EdmVocabularyAnnotation`'s value to the serialized CSDL if it has a default value. When creating an `EdmVocabularyAnnotation` if the value is not provided, we check whether the `EdmTerm` has a default value or not. If the term has a default value and no value was provided, we set the `UsesDefault` to `true`, otherwise `false`.

We also added new extension method, `CreateVocabularyAnnotation` for creating an `EdmVocabularyAnnotation` when a value is provided. OData .NET 7 already had an extension method for creating an `EdmVocabularyAnnotation` when a value is not provided.

### Added `EntityType` property to `IEdmNavigationSource` interface

OData .NET 8 added a property called `EntityType` of type `IEdmEntityType` to the `IEdmNavigationSource` interface. Existing implementation of that interface have been updated to implement that property. This makes it possible to get the entity type of a navigation source without having to perform a cast. Subsequently, the `EntityType(IEdmNavigationSource)` static helper method is no longer recommended. It has been marked as obsolete and may be removed in a future major version.
