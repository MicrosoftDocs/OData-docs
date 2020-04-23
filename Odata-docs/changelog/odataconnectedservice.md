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

### New Features
* [ [#86](https://github.com/OData/ODataConnectedService/pull/86) ] Support for emitting service metadata to an XML file
* [ [#100](https://github.com/OData/ODataConnectedService/pull/100) ] Support for excluding schema types from being emitted onto the Connected Service proxy class

### Improvements and Bug Fixes
* [ [#66](https://github.com/OData/ODataConnectedService/pull/66) ] Use _var_ where the type of variable is clear from the context
* [ [#97](https://github.com/OData/ODataConnectedService/pull/97) ] Fix multiple prompts when updating Connected Service with proxy split into multiple files
* [ [#99](https://github.com/OData/ODataConnectedService/pull/99) & [#102](https://github.com/OData/ODataConnectedService/pull/102) ] Fix loading of saved configuration options when updating Connected Service
* [ [#101](https://github.com/OData/ODataConnectedService/pull/101) ] Fix caption on Connected Service wizard

## OData Connected Service 0.8.0

### New Features
* [ [#50](https://github.com/OData/ODataConnectedService/pull/50) ] Support for loading configuration settings from a file
* [ [#69](https://github.com/OData/ODataConnectedService/pull/69) ] Support for saving the configuration wizard state when navigating through the pages

## OData Connected Service 0.7.1

### Bug Fixes
* [ [#77](https://github.com/OData/ODataConnectedService/pull/77) ] Fix bug causing service name to be reset to default value when updating Connected Service

## OData Connected Service 0.7.0

### New Features
* [ [#31](https://github.com/OData/ODataConnectedService/pull/31) ] Support for web proxy and credentials when fetching metadata behind a network proxy
* [ [#38](https://github.com/OData/ODataConnectedService/pull/38) ] Support for generating Connected Service proxy for VB.NET
* [ [#45](https://github.com/OData/ODataConnectedService/pull/45) ] Support for excluding  operation imports from being emitted onto the Connected Service proxy class
* [ [#68](https://github.com/OData/ODataConnectedService/pull/68) ] Support for loading of service metadata from local file

### Improvements and Bug Fixes
* [ [#70](https://github.com/OData/ODataConnectedService/pull/70) ] Add Transform Tool paths for both Visual Studio 2017 and 2019

## OData Connected Service 0.6.1

### Improvements and fixes:

* Bug fix on adding option for a separate metadata file.

## OData Connected Service 0.6.0

### New Features:

* Custom Http headers. While generating code you can now pass headers including authorization headers to read your Edmx. 
* Generation of multiple files. You can now opt to generate code in multiple files.

## OData Connected Service 0.5.0

OData Connected Service version 0.5.0 requires ```Microsoft.OData.client version 7.6.3``` or above.

With this release, all the features and fixes available in [OData Client Code Generator](https://marketplace.visualstudio.com/items?itemName=bingl.ODatav4ClientCodeGenerator) have been migrated to OData Conneted service. 
It is therefore advisable to the users of [OData Client Code Generator](https://marketplace.visualstudio.com/items?itemName=bingl.ODatav4ClientCodeGenerator) to start using ```OData Connected Service``` instead.


### New Features:

* Upgrade Microsoft.OData.Client to version 7.6.3
* Added a feature to open the generated files in visual studio after code generation.
* Enable generated functions to be mockable

### Improvements and fixes:

* Remove Microsoft WCF ToolKit dependency
* Generate type definitions using their underlying types
* Migrated all features and fixes from OData Client Code Generator to OData Connected Service

---

## OData Connected Service 0.4.0

### New Features:
* Allows user to make generated types internal

### Improvements and fixes:
* Adds support for Visual Studio 2019
* [ [#81](https://github.com/OData/lab/issues/81) ] "OData Connected Service" templates are completely broken right now
