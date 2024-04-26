---
title: "OData lib 8.x changelog"
description: OData lib 8.x changelog
author: gathogojr
ms.author: jogathog
ms.date: 4/25/2024
ms.topic: article
---

# OData lib 8.x changelog

OData lib is loosely used to refer to the following group of OData libraries available on the [Nuget gallery](https://www.nuget.org/packages):
- [Microsoft.OData.Core](https://www.nuget.org/packages/Microsoft.OData.Core)
- [Microsoft.OData.Edm](https://www.nuget.org/packages/Microsoft.OData.Edm)
- [Microsoft.Spatial](https://www.nuget.org/packages/Microsoft.Spatial)
- [Microsoft.OData.Client](https://www.nuget.org/packages/Microsoft.OData.Client)

You can install or update any of the NuGet packages for OData lib using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).


## [8.0.0-preview.1](https://github.com/OData/odata.net/releases/tag/8.0.0-preview.1)

Starting version 8, OData lib will only target .NET 8 or later.

### [Microsoft.OData.Core](https://www.nuget.org/packages/Microsoft.OData.Core/8.0.0-preview.1)
- `IJsonReaderAsync` interface has been merged into `IJsonReader` interface.
  - Any implementation of `IJsonReader` needs to implement methods previously defined in `IJsonReaderAsync` interfaces.
  - The `Value` property in `IJsonReader` interface is replaced by `GetValue` method.
- `IJsonReaderFactoryAsync` interface has been dropped.
- `IJsonStreamWriter`, `IJsonWriterAsync` and `IJsonStreamWriterAsync` interfaces have been merged into `IJsonWriter` interface.
  - Any implementation of `IJsonWriter` needs to implement methods previously defined in `IJsonStreamWriter`, `IJsonWriterAsync` and `IJsonStreamWriterAsync` interfaces.
- `IStreamBasedJsonWriterFactory` and `IJsonWriterFactoryAsync` interfaces has been dropped.
- `DefaultStreamBasedJsonWriterFactory` class has been renamed to `ODataUtf8JsonWriterFactory`.
- `CreateJsonWriter(TextReader, bool)` defined in `IJsonWriterFactory` has changed to `CreateJsonWriter(Stream, bool, Encoding)`. The method now accepts a `Stream` rather than a `TextReader`.
- `List<ODataUrlValidationMessage> Messages` property defined in `ODataUrlValidationContext` class has changed to `IReadOnlyList<ODataUrlValidationMessage> Messages`.
  - `AddMessage(ODataUrlValidationMessage)` overload introduced in `ODataUrlValidationContext`.
- `INavigationSourceSegment` interface introduced. The purpose of this new interface is to reduce casting when determining the navigation source associated with the segment. `EntitySetSegment`, `SingletonSegment` and `NavigationPropertySegment` implement this new interface.
- Deprecated support for JSONP callback. Feature to be removed in ODL 9.
  - `JsonPCallback` property defined in `ODataMessageWriterSettings` class marked as obsolete.
  - `StartPaddingFunctionScope` method defined in `IJsonWriter` interface marked as obsolete.
  - `EndPaddingFunctionScope` method defined in `IJsonWriter` interface marked as obsolete.
  - `WritePaddingFunctionName` method defined in `IJsonWriter` interface marked as obsolete.
  - `StartPaddingFunctionScopeAsync` method defined in `IJsonWriter` interface marked as obsolete.
  - `EndPaddingFunctionScopeAsync` method defined in `IJsonWriter` interface marked as obsolete.
  - `WritePaddingFunctionNameAsync` method defined in `IJsonWriter` interface marked as obsolete.
- `ODataSimplifiedOptions` class was dropped. This class would be injected into the DI container and the settings used to control behaviour when parsing URLs, and when writing and reading payloads. In ODL 8, `ODataMessageReaderSettings`, `ODataMessageWriterSettings`, and `ODataUriParserSettings` may variously be used to accomplish the same purpose.
  - `EnableReadingKeyAsSegment` and `EnableReadingODataAnnotationWithoutPrefix` properties moved to `ODataMessageReaderSettings` class.
  - `EnableWritingKeyAsSegment` property moved to `ODataMessageWriterSettings` class.
  - `SetOmitODataPrefix(bool)`, `SetOmitODataPrefix(bool, ODataVersion)`, `GetOmitODataPrefix()`, and `GetOmitODataPrefix(ODataVersion)` methods moved to `ODataMessageWriterSettings` class.
  - `EnableParsingKeyAsSegment` property moved to `ODataUriParserSettings` class.
- In ODL 7, when `ODataBinaryStreamValue` class is initialized using the `ODataBinaryStreamValue(Stream)` constructor, the stream is left open by default upon the object being disposed. In ODL 8, the stream is closed by default the object objects is disposed. The `ODataBinaryStreamValue(Stream, bool)` constructor overload may be used where leaving the stream open is intended.
- `Func<string, bool> ShouldIncludeAnnotation` property introduced in `ODataMessageWriterSettings`. This property makes it possible for developers to force a custom instance annotation to be written even if it's not include in the optional `@odata.include-annotations` preference token in `Prefer` request header.
- `IContainerBuilder` interface used when registering OData services was dropped. Use `Microsoft.Extensions.DependencyInjection` library instead.
  - `AddDefaultODataServices(IServiceCollection, ODataVersion, Action<ODataMessageReaderSettings>, Action<ODataMessageWriterSettings>, Action<ODataUriParserSettings>)` extension method introduced for the purpose of registering OData services.
  - `IContainerProvider` interface replaced by `IServiceCollectionProvider` interface. It's a provider for the `IServiceProvider` IoC container.
  - `ODataBatchOperationRequestMessage` now implements `IServiceCollectionProvider` instead of `IContainerProvider`.
  - `ODataBatchOperationResponseMessage` now implements `IServiceCollectionProvider` instead of `IContainerProvider`.

### [Microsoft.OData.Client](https://www.nuget.org/packages/Microsoft.OData.Client/8.0.0-preview.1)
- `HttpWebRequestMessage` class has been dropped - effectively dropping support for `HttpWebRequest`. Use `HttpClientRequestMessage` class instead.
- `IHttpClientHandlerProvider` interface used to provide `HttpClientHandler` for use with `DataServiceContext` has been dropped.
- `HttpClientHandlerProvider` property defined in `DataServiceClientRequestMessageArgs` class and used for providing `HttpClientHandler` substituted with `HttpClientFactory` property that accomplishes the same purpose.
- `HttpClientHandlerProvider` property defined in `DataServiceContext` class and used for providing `HttpClientHandler` substituted with `HttpClientFactory` property that accomplishes the same purpose.
- Obsolete `Credentials` property dropped from `DataServiceClientRequestMessage` abstract class. The recommended way to configure credentials is through `HttpClientHandler` that can be provided using `IHttpClientFactory`.
- Obsolete `Credentials` property dropped from `HttpClientRequestMessage` class. The recommended way to configure credentials is through `HttpClientHandler` that can be provided using `IHttpClientFactory`.
- Obsolete `Credentials` property dropped from `DataServiceContext` class. The recommended way to configure credentials is through `HttpClientHandler` that can be provided using `IHttpClientFactory`.
- Obsolete `ReadWriteTimeout` property dropped from `DataServiceClientRequestMessage` abstract class. This property would be used with `HttpWebRequestMessage`. `Timeout` property may be used instead.
- Obsolete `ReadWriteTimeout` property dropped from `HttpClientRequestMessage` class. This property would be used with `HttpWebRequestMessage`. `Timeout` property may be used instead.
- In `DataServiceClientRequestMessageArgs` class, the `DataServiceClientRequestMessageArgs(string, Uri, bool, bool, IDictionary<string, string>)` constructor has changed to `DataServiceClientRequestMessageArgs(string, Uri, bool, IDictionary<string, string>)`. The boolean `useDefaultCredentials` parameter is no longer supported.
- In `DataServiceClientRequestMessageArgs` class, the `DataServiceClientRequestMessageArgs(string, Uri, bool, bool, IDictionary<string, string>, IHttpClientHandlerProvider)` constructor has changed to `DataServiceClientRequestMessageArgs(string, Uri, bool, IDictionary<string, string>, IHttpClientFactory)`. The boolean `useDefaultCredentials` parameter is no longer supported.
- In `DataServiceClientRequestMessageArgs` class, the `UseDefaultCredentials` property dropped from `DataServiceClientRequestMessageArgs` class. The recommended way to configure credentials is through `HttpClientHandler` that can be provided using `IHttpClientFactory`.
- `HttpRequestTransportMode` enum property was dropped from `DataServiceContext`. This property was used to switch between `HttpClient` and `HttpWebRequest` that was dropped.
- `KeyComparisonGeneratesFilterQuery` flag defined in `DataServiceContext` class marked as deprecated. Flag will be removed in ODL 9.
  - Default value for `keyComparisonGeneratesFilterQuery` flag set to true such that a `Where` expression with only the key property in the predicate is translated into a `$filter` query rather a resouce URL for requesting a single entity.
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

### [Microsoft.OData.Edm](https://www.nuget.org/packages/Microsoft.OData.Edm/8.0.0-preview.1)
- `IEdmEntityType EntityType` property introduced in `IEdmNavigationSource` interface.
  - Any implementation of `IEdmNavigationSource` needs to implement the `EntityType` property.
  - The public `EntityType(IEdmNavigationSource)` static method has been marked as obsolete and will be removed in ODL 9.
