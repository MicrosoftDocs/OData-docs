---
title: "OData OpenAPI changelog"
description: "OData OpenAPI changelog"
author: Sam Xu
ms.author: saxu
ms.date: 2/5/2021
ms.topic: article
---

# Open API  OData changelog

# OpenAPI OData changelog

Open API OData reader is available on the [Nuget gallery](https://www.nuget.org/packages/Microsoft.OpenApi.OData)

You can install or update the NuGet package for ASP.NET Core OData using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).

---

## [1.0.7](https://www.nuget.org/packages/Microsoft.OpenApi.OData/1.0.8)
 
 * [[#104](https://github.com/microsoft/OpenAPI.NET.OData/pull/104)] Describes path operations description from vocabulary annotations
  
 * Add support for EdmTypeKind.TypeDefinition

 * Add async/await to make UI more response

---

## [1.0.7](https://www.nuget.org/packages/Microsoft.OpenApi.OData/1.0.7)

 * [[#98](https://github.com/microsoft/OpenAPI.NET.OData/pull/98)] Fixes potential duplicate operationIds in action/function paths
 
 * [[#92](https://github.com/microsoft/OpenAPI.NET.OData/pull/92)] Removes example property in path parameter objects
 
 * Add config for ShowMsDocGroupPath
 
 ---
 
## [1.0.6](https://www.nuget.org/packages/Microsoft.OpenApi.OData/1.0.6)

 * Add Path provider and path prefix provider into setting
 
 * Fix paths when operation is bound to a derived type
 
 * Add metadata, segment and related path handler and operation handler
 
 * Add key and operation parameter mapping
 
 ---

## [1.0.5](https://www.nuget.org/packages/Microsoft.OpenApi.OData/1.0.5)

 * Add model reference support to library and command line utility
 
 * [#86](https://github.com/microsoft/OpenAPI.NET.OData/pull/86): Push changes from Microsoft Graph
 
---

## [1.0.4](https://www.nuget.org/packages/Microsoft.OpenApi.OData/1.0.4)

 * [#78](https://github.com/microsoft/OpenAPI.NET.OData/pull/78): Fix casing of OpenAPI type "String" for RefPatchOperationHandler and RefPostOperationHandler 

 * [#73](https://github.com/microsoft/OpenAPI.NET.OData/pull/73): Allows schema examples to be optional

 * Output XML file into nuget package

 ---

## [1.0.2](https://www.nuget.org/packages/Microsoft.OpenApi.OData/1.0.2)

  * [#57](https://github.com/microsoft/OpenAPI.NET.OData/pull/57): Add Links to entity set get response object

  * [#55](https://github.com/microsoft/OpenAPI.NET.OData/pull/55): Add support for replacing base type references with their derived types

  * [#54](https://github.com/microsoft/OpenAPI.NET.OData/pull/54): Add discriminator support

  * Add simple PathPrefix to ConvertSettings

---

## [1.0.1](https://www.nuget.org/packages/Microsoft.OpenApi.OData/1.0.1)

* Enable Uri escape function call

---

## [1.0.0](https://www.nuget.org/packages/Microsoft.OpenApi.OData/1.0.0)

  * [#38](https://github.com/microsoft/OpenAPI.NET.OData/pull/38): Change encryption for fips compliance

  * [#34](https://github.com/microsoft/OpenAPI.NET.OData/pull/34): Users/mispeer/serialize nullable reference for v2

 * Implement the project

 --- 
