---
title: "OData ConnectedService changes"
description: "OData ConnectedService changelog"
author: saxu
ms.author: saxu
ms.date: 03/24/2020
ms.topic: article
---

# OData Connected Service changelog

OData Connected Service extension is available on [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=marketplace.ODataConnectedService).

## OData Connected Service 0.12.1

### Improvements and Bug Fixes:
* [ [#202](https://github.com/OData/ODataConnectedService/pull/202) ] Fix invalid escaping of double quotes when generating VB.NET client proxy
* [ [#203](https://github.com/OData/ODataConnectedService/pull/203) ] Fix uncompilable code emitted when the option to generate multiple files is checked and the model contains 2 types with the same name but in different namespace
* [ [#204](https://github.com/OData/ODataConnectedService/pull/204) ] Fix invalid namespace emitted when generating client proxy with custom namespace supplied
* [ [#209](https://github.com/OData/ODataConnectedService/pull/209) ] Restore namespace in the metadata document as the default namespace applied in the client proxy

## OData Connected Service 0.12.0

### Improvements and Bug Fixes:
* [ [#178](https://github.com/OData/ODataConnectedService/pull/178) ] Fixes the following 3 issues:
  - Error when generating code for V1-V3 services
  - Error caused by UI updates not running in UI thread
  - Improve error message when CLR type is not found
* [ [#183](https://github.com/OData/ODataConnectedService/pull/183) ] Add support for reference model with relative URI
* [ [#187](https://github.com/OData/ODataConnectedService/pull/187) ] Set more logical default settings
* [ [#197](https://github.com/OData/ODataConnectedService/pull/197) ] Fix warnings displayed due to empty metadata file created when when the user opts to emit the T4 template on the client project

## OData Connected Service 0.11.1

* [ [#173](https://github.com/OData/ODataConnectedService/pull/173) ] Fixes the signing and strong name issue that caused the Connected Service v0.11.0 not to be loaded by Visual Studio

## OData Connected Service 0.11.0

### New Features:
* [ [#145](https://github.com/OData/ODataConnectedService/pull/145) ] Display the number of selected operation imports
* [ [#161](https://github.com/OData/ODataConnectedService/pull/161) ] Adds search box for filtering entities to add on the Schema Types selection page
* [ [#168](https://github.com/OData/ODataConnectedService/pull/168) ] Generate doc comments in generated code from description annotations in the OData schema

### Improvements and Bug Fixes:
* [ [#133](https://github.com/OData/ODataConnectedService/pull/133) ] Fix issue where `false` is generated as `False` (uppercase F) in C# code, causing errors
* [ [#135](https://github.com/OData/ODataConnectedService/pull/135) ] Include enum types from enum collection properties in generated code to avoid compilation errors
* [ [#148](https://github.com/OData/ODataConnectedService/pull/148) ] Rename generated parameters source and keys to avoid name collisions with similar names in the model
* [ [#154](https://github.com/OData/ODataConnectedService/pull/154) ] Fix issue causing undefined Edmx reference to appear in generated code
* [ [#160](https://github.com/OData/ODataConnectedService/pull/160) ] Fix build error caused by incompatible `System.Text.Json` version with new versions of OData client

## OData Connected Service 0.10.0

### New Features:
* [ [#83](https://github.com/OData/ODataConnectedService/pull/83) ] Emit dynamic properties container property for open types

### Improvements and Bug Fixes:
* [ [#116](https://github.com/OData/ODataConnectedService/pull/116) ] Properly escapes metadata path in the local filesystem when generating T4 templates
* [ [#117](https://github.com/OData/ODataConnectedService/pull/117) ] Fix issue where unselecting an operation import auto-selects schema types that the operation depends on
* [ [#121](https://github.com/OData/ODataConnectedService/pull/121) ] Fix issue loading generated metadata file when you have multiple files generated in the client project

## OData Connected Service 0.9.1

### New Features:
* [ [#87](https://github.com/OData/ODataConnectedService/pull/87) ] Auto-selects the last metadata endpoint used in generating proxy classes

### Improvements and Bug Fixes:
* [ [#95](https://github.com/OData/ODataConnectedService/pull/95) ] Enables generation of multiple files using design-time T4 templates
* [ [#107](https://github.com/OData/ODataConnectedService/pull/107) ] Fix issue with wizard state persistence when updating an OData Connected Service project

## OData Connected Service 0.9.0

### New Features:
* [ [#86](https://github.com/OData/ODataConnectedService/pull/86) ] Support emitting of service metadata to an XML file
* [ [#100](https://github.com/OData/ODataConnectedService/pull/100) ] Support for excluding schema types from being emitted onto the Connected Service proxy class

### Improvements and Bug Fixes:
* [ [#66](https://github.com/OData/ODataConnectedService/pull/66) ] Use _var_ where the type of variable is clear from the context
* [ [#97](https://github.com/OData/ODataConnectedService/pull/97) ] Fix multiple prompts when updating Connected Service proxy split into multiple files
* [ [#99](https://github.com/OData/ODataConnectedService/pull/99) & [#102](https://github.com/OData/ODataConnectedService/pull/102) ] Fix loading of saved configuration options when updating Connected Service
* [ [#101](https://github.com/OData/ODataConnectedService/pull/101) ] Fix caption on Connected Service wizard

## OData Connected Service 0.8.0

### New Features:
* [ [#50](https://github.com/OData/ODataConnectedService/pull/50) ] Support loading of configuration settings from a file
* [ [#69](https://github.com/OData/ODataConnectedService/pull/69) ] Support saving of configuration wizard state when navigating through the pages

## OData Connected Service 0.7.1

### Bug Fixes:
* [ [#77](https://github.com/OData/ODataConnectedService/pull/77) ] Fix bug causing service name to be reset to default value when updating Connected Service

## OData Connected Service 0.7.0

### New Features:
* [ [#31](https://github.com/OData/ODataConnectedService/pull/31) ] Support for web proxy and credentials when fetching metadata behind a network proxy
* [ [#38](https://github.com/OData/ODataConnectedService/pull/38) ] Support generation of Connected Service proxy for VB.NET
* [ [#45](https://github.com/OData/ODataConnectedService/pull/45) ] Support for excluding  operation imports from being emitted onto the Connected Service proxy class
* [ [#68](https://github.com/OData/ODataConnectedService/pull/68) ] Support for loading of service metadata from local file

### Improvements and Bug Fixes:
* [ [#70](https://github.com/OData/ODataConnectedService/pull/70) ] Add Transform Tool paths for both Visual Studio 2017 and 2019

## OData Connected Service 0.6.1

### Improvements and Bug Fixes:

* [ [#55](https://github.com/OData/ODataConnectedService/pull/55) ] Revert change for supporting emitting of service metadata to XML file to fix a regression

## OData Connected Service 0.6.0

### New Features:
* [ [#27](https://github.com/OData/ODataConnectedService/pull/27) & [#51](https://github.com/OData/ODataConnectedService/pull/51) ] Support custom Http headers (e.g. authorization headers) when retrieving service metadata
* [ [#30](https://github.com/OData/ODataConnectedService/pull/30) ] Support splitting of Connected Service proxy into multiple files
* [ [#46](https://github.com/OData/ODataConnectedService/pull/46) ] Support emitting of service metadata to an XML file

## OData Connected Service 0.5.0

### Breaking Changes:
* [ [#24](https://github.com/OData/ODataConnectedService/pull/24) ] Update Microsoft.OData.Client dependency to version 7.6.3

### New Features:
* [ [#23](https://github.com/OData/ODataConnectedService/pull/23) ] Make it possible to customize the built-in vocabulary models
* [ [#26](https://github.com/OData/ODataConnectedService/pull/26) ] Support opening of generated Connected Service proxy files in IDE at completion
* [ [#32](https://github.com/OData/ODataConnectedService/pull/32) ] Support mocking by making public methods `virtual`

### Improvements and Bug Fixes:
* [ [#22](https://github.com/OData/ODataConnectedService/pull/22) ] Swap `CsdlReader.Parse()` with `CsdlReader.TryParse()`
* [ [#25](https://github.com/OData/ODataConnectedService/pull/25) ] Use underlying types when generating type definitions
* [ [#28](https://github.com/OData/ODataConnectedService/pull/28) ] Swap `Dictionary` with `IDictionary` in declarations
* [ [#33](https://github.com/OData/ODataConnectedService/pull/33) ] Drop Microsoft WCF ToolKit dependency
* [ [#36](https://github.com/OData/ODataConnectedService/pull/36) ] Update issue templates
* [ [#37](https://github.com/OData/ODataConnectedService/pull/37) ] Update README, licences and pull-request templates

## OData Connected Service 0.4.0

### New Features:
* Allows user to make generated types internal

### Improvements and fixes:
* Adds support for Visual Studio 2019
* [ [#81](https://github.com/OData/lab/issues/81) ] "OData Connected Service" templates are completely broken right now

## OData Connected Service 0.3.1

* [ [#155](https://github.com/OData/ODataConnectedService/pull/155) ] Legacy OData Connected Service version that supports previous versions of OData client (versions 6.x). It has been updated to support both VS 2017 and VS 2019
