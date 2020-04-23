---
title: "OData ConnectedService changes"
description: "OData ConnectedService changelog"
author: saxu
ms.author: saxu
ms.date: 03/24/2020
ms.topic: article
---

# OData Connected Service changelog

OData Connected Service extension is available on [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=laylaliu.ODataConnectedService).

## OData Connected Service 0.9.0

### New Features:
* [ [#86](https://github.com/OData/ODataConnectedService/pull/86) ] Support emitting of service metadata to an XML file
* [ [#100](https://github.com/OData/ODataConnectedService/pull/100) ] Support for excluding schema types from being emitted onto the Connected Service proxy class

### Improvements and Bug Fixes:
* [ [#66](https://github.com/OData/ODataConnectedService/pull/66) ] Use _var_ where the type of variable is clear from the context
* [ [#97](https://github.com/OData/ODataConnectedService/pull/97) ] Fix multiple prompts when updating Connected Service with proxy split into multiple files
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
* [ [#46](https://github.com/OData/ODataConnectedService/pull/46) ] Support emitting of service metadata to an XML file
* [ [#27](https://github.com/OData/ODataConnectedService/pull/27) & [#51](https://github.com/OData/ODataConnectedService/pull/51) ] Support custom Http headers (e.g. authorization headers) when retrieving service metadata
* [ [#30](https://github.com/OData/ODataConnectedService/pull/30) ] Support splitting of Connected Service proxy into multiple files

## OData Connected Service 0.5.0

### Breaking Changes:
* [ [#24](https://github.com/OData/ODataConnectedService/pull/24) ] Update Microsoft.OData.Client dependency to version 7.6.3

### New Features:
* [ [#23](https://github.com/OData/ODataConnectedService/pull/23) ] Add built-in vocabulary models
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
