---
title: "OData ASP.NET Core OData changelog"
description: "OData WebApi 7.x changelog"
author: Sam Xu
ms.author: saxu
ms.date: 2/5/2021
ms.topic: article
---

# ASP.NET Core OData 8.x changelog

ASP.NET Core OData 8.x is available on the [Nuget gallery](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/8.0.0-beta)

You can install or update the NuGet package for ASP.NET Core OData using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).


* Blogs

    * [ASP.NET Core OData 8.0 Preview for .NET 5](https://devblogs.microsoft.com/odata/asp-net-odata-8-0-preview-for-net-5/)
    * [Routing in ASP.NET Core OData 8.0 Preview](https://devblogs.microsoft.com/odata/routing-in-asp-net-core-8-0-preview/)

* Samples:
    * [ODataRoutingSample](https://github.com/OData/AspNetCoreOData/tree/master/sample/ODataRoutingSample)

---

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