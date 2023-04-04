---
title: "OData ASP.NET Core OData changelog"
description: AspNetCoreOData 8.x changelog
author: xuzhg
ms.author: saxu
ms.date: 2/5/2021
ms.topic: article
---

# ASP.NET Core OData 8.x changelog

ASP.NET Core OData 8.x is available on the [Nuget gallery](https://www.nuget.org/packages/Microsoft.AspNetCore.OData)

You can install or update the NuGet package for ASP.NET Core OData using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).


* Blogs

    * [ASP.NET Core OData 8.0 Preview for .NET 5](https://devblogs.microsoft.com/odata/asp-net-odata-8-0-preview-for-net-5/)
    * [Routing in ASP.NET Core OData 8.0 Preview](https://devblogs.microsoft.com/odata/routing-in-asp-net-core-8-0-preview/)
    * [API versioning extension with ASP.NET Core OData 8](https://devblogs.microsoft.com/odata/api-versioning-extension-with-asp-net-core-odata-8/)
    * [Build formatter extensions in ASP.NET Core OData 8 and hooks in ODataConnectedService](https://devblogs.microsoft.com/odata/build-formatter-extensions-in-asp-net-core-odata-8-and-hooks-in-odataconnectedservice/)

* Samples:
    * [ODataRoutingSample](https://github.com/OData/AspNetCoreOData/tree/main/sample/ODataRoutingSample)

---

## [8.1.1](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.1.1)

See details at: https://github.com/OData/AspNetCoreOData/releases/tag/8.1.1

## [8.1.0](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.1.0)

See details at: https://github.com/OData/AspNetCoreOData/releases/tag/8.1.0

## [8.0.12](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.12)

See details at: https://github.com/OData/AspNetCoreOData/releases/tag/8.0.12

## [8.0.11](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.11)

See details at: https://github.com/OData/AspNetCoreOData/releases/tag/8.0.11

## [8.0.10](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.10)

See details at: https://github.com/OData/AspNetCoreOData/releases/tag/8.0.10

## [8.0.9](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.9)

See details at: https://github.com/OData/AspNetCoreOData/releases/tag/8.0.9

## [8.0.8](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.8)

Starting from 8.0.8, we will use the release note on github.

See details at: https://github.com/OData/AspNetCoreOData/releases/tag/8.0.8


## [8.0.7](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.7)

* Support DateOnly and TimeOnly for .NET 6
* Simpilify Nullable and non nullable type mapping registration
* Unmapped properties in Delta
* Change query validation to throw ODataException
* Create type annotation only has the type name
* No dollar sign query option setting to resolver

---

## [8.0.6](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.6)

* Make TryGetNestedPropertyValue internal
* Fix 'none OData' -> 'non-OData' typo

---

## [8.0.5](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.5)

 * Query option binder refactor.
 * Enable $compute query option.
 * Enable $search query option, developer needs to implement the ISearchBinder.
 * Migrate OData/WebApi#2512 to AspNetCoreOData.
 * Trim forward slashes from route prefix.
 * Fixed overwriting of the ODataUriResolver, received upstream from DI (e.g. AlternateKeysODataUriResolver).
 * Add TryGetNestedPropertyValue in DeltaOfT
     Be noted: TryGetNestedPropertyValue has been added accidentally as public, it will be internal in the next version.
               Please use TryGetPropertyValue except TryGetNestedPropertyValue.
 * Update the model builder dependency to 1.0.8
 * Move the timezone setting for ODataQuerySetting to EnableQuery executing
 * Add EnablePropertyNameCaseInsensitive for Conventional routing
 * Bump to ODL 7.9.4, Enable property name case-insensitive

---

## [8.0.4](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.4)

**be noted**:
 We are still working on refactoring ISelectExpandBinder and other query binders.
 Be aware of any changes in the next release.

 * Fix auto select and expand when baseentity type is null
 * Prevent error from duplicating attribute
 * Add possibility to bind params of custsom type
 * Add IODataTypeMapper
 * Bump ODL dependency to 7.9.4, and enable property name case-insensitive by default
 * Fix Enum.TryParse exception when using in .NET 6
 * Enable case-insensitive in query option by default
 * Add AddODataQueryFilter extension methods
 
## [8.0.3](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.3)

 * Enable auto expand navigation property on complex type property
 * Enable nested resource case insensitive when reading
 * Fix behaviour for Preflight requests on the ODataBatchMiddleware
 * URedesign SelectExpandBinder to allow customizations
 * Bump ODL dependency to 7.9.2
 * Refactor ExpressionBinderBase
 * Convert.ChangeType invalid cast from System.String to System.Guid
 * Fixed issue DateTime Created with too Much Decimal Precision Causing SQL Query to Fail

## [8.0.2](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.2)

 * DeltaOfT out of sync with WebAPI: missing UpdatableProperties
 * Update model builder dependency to 1.0.7
 * move the ReadUntypedAsString=false to setting
 * Fix issue: filter binder does not use the timezone info from settings
 * Fix bug in Filter Any within Expand
 * Fix incorrect GetProperties method logic
 * Change public virtual to protected virtual for ValidateCountNode
 * Add support for $count segment in $filter
 
## [8.0.1](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.1)

 * Make methods as virtual in ODataSerializerProvider
 * Make methods as virtual in ODataDeserializerProvider

## [8.0.0](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.0)

 * Change `AddModel()` to `AddRouteComponents`, Add `EnableQueryFeatures()` in ODataOptions
 * Remove `IODataBuilder`, `DefaultODataBuilder`, Eliminate `BuilderFactory` and the need for external `ContainerBuilder`
 * Rename `ODataRoutingAttribute` to `ODataAttributeRoutingAttribute`
 * Rename `ODataModelAttribute` to `ODataRouteComponentAttribute`
 * Remove `NonODataControllerAttribute` and `NonODataActionAttribute` and replace them using `ODataIgnoredAttribute`
 * Rename `PrefixName` to `RoutePrefix`, Rename `SubServiceProvider` to `Services` in IODataFeature
 * Add `ODataOptions()` in HttpContextExtensions and HttpRequestExtensions
 * Rename `{Get|Create|Delete}SubServiceProvider` to `{Get|Create|Delete}RouteServices` in HttpRequestExtensions
 * Improvement to (de)serialization code, using interface for (de)serializer
 * Improve OData debug route and its pattern
 * Enable $this query option
 * Update dependency to ODL 7.9, ModelBuilder 1.0.6
 * Fix AmbiguousMatchException for properties with new modifier
 * Add bunch of test cases to increase the code coverage
 * Other code clean up, for example, remove `IODataTypeMappingProvider` etc

## [8.0.0-rc3](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.0-rc3)

 * **Be noted** Breaking changes in ODataSegmentTemplate
 * Use ActionName in conventional routing
 * Make CreateRef, DeleteRef, GetRef for generic navigation property working
 * Support alternate key in attribute routing
 * ClrTypeCache has memory leak owing to IEdmTypeReference
 * Change ODataOptions by removing unnecessary APIs
 * Remove EdmDeltaKind
 * Change EdmDeltaLink, EdmDeltaDeletedLink, etc
 * Add a config for non-parenthesis of empty parameter function
 * Add a config for controller name case insensitive
 * Enable string without single quote in key as segment

## [8.0.0-rc2](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.0-rc2)

 * **Be noted** Move AddOData() to IMvcBuilder and IMvcCoreBuilder
 * Move routing conventions to ODataOptions & use options setup
 * Add the API for navigation source, navigation property, operation link builder
 * Enable payload property case-insensitive
 * Remove ContentLength check, support chunked request
 * Add SingleResult JSON value converter
 * Improve the "EnumTryParse" reflect for .NET 6
 * Support long query URL pattern using $query
 * Add a OData routing debug middleware. Call `app.UseODataRouteDebug()` to enable it
 * Count in filter throws not supported
 * Retrieve the default query setting
 * Fix issue about absolute route template with two selector models
 * Reuse WriteObjectInlineAsyc in ODataDeltaResourceSetSerializer
 * Create selector model if we have route template on controller
 * Improve ConvertPrimtiveValue assert

---

## [8.0.0-rc](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.0-rc)

 * **Attribute routing**: Remove `ODataRouteAttribute`, `ODataRoutePrefix`, use `RouteAttribute`, `HttpGet`, `HttpPost`,... for attribute routing
 * **Delta resource set**: Add DeltaSetOfT, DeltaT, DeltaLinkT and related interfaces
 * **Entity Reference**: Support odata.bind and odata.id link
 * Fix the TimeZone issue when deserializing
 * Stream property serialization & stream property query
 * Make basic non-Edm scenario working
 * Add Json value converter for PageResult, SelectExpandWrapper
 * Add Dynamic type wrapper type converter
 * Rename IsUntyped to IsNoClrType in Deserializer context
 * Rename ODataDeltaFeedSerializer to ODataDeltaResourceSetSerializer
 * Retrieve the returned entity set for function from annotation
 * Add / prefix for OData route template to prevent mergeing routing template from controller
 * Use metadata writer async, change other WriteObjectAsync
 * Change the Translate in segment template to TryTranslate
 * Remove CompatibilityOptions and code clean
 * Add request and timezone to the nested deserializer context
 * Change PathTemplateSegment, DynamicSegment
 * Add the OData version to reader settings when specified in the request

---

## [8.0.0-beta](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.0-beta)

 * Fix the TimeZone issue when serializing
 * Add the key/value pair parser
 * Set ReadAsUntypedAsString=false
 * Fix the query information concurrency problem. [#72](https://github.com/OData/AspNetCoreOData/pull/72)
 * Remove model container and inject Edm model into query wrapper
 * Fix the navigation property routing convention http method
 * Enhance batch accept header descision
 * ResourceContext.ResourceInstance throws exception
 * Fix child collection nextlink
 * Enable model query in select and expand binder
 * Fix invalid location header generated when key property for model contains unicode chars

---

## [8.0.0-preview3](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.0-preview3)

 * Enable CSDL-JSON metadata, use the following ways:
    - Query: $format=application/json
    - Query: $format=json
    - Header: Accept=application/json

 * Add all attributes to new selector model. [#34](https://github.com/OData/AspNetCoreOData/issues/34)
  * Change the Order value for Entity and OperationImport routing convention
  * Add null check for the model
  * Remove OmitNullDynanicProperty setting
  * Remove CompatibilityOptions setting
  * Change CreatedODataResult and UpdatedODataResult
  * EnableQuery should perform checks before controller execution. [#51](https://github.com/OData/AspNetCoreOData/issues/51)
  * Full serialization and deserialization async 

---

## [8.0.0-preview2](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.0-preview2)

 * Improve key and function segment template
 * Remove the GuidCompare

---

## [8.0.0-preview](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.0-preview)

 * Implement the new routing convention, see [ASP.NET Core OData 8.0 Preview for .NET 5](https://devblogs.microsoft.com/odata/asp-net-odata-8-0-preview-for-net-5/)
 * Separate the model builder into new [OData.ModelBuilder](https://www.nuget.org/packages/Microsoft.OData.ModelBuilder/)

 ---
