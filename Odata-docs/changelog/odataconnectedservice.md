---
title: "OData ConnectedService changes"
description: "OData ConnectedService changelog"
author: saxu
ms.author: saxu
ms.date: 03/24/2020
ms.topic: article
---

# OData Connected Service changelog

## version 0.6.1 Release

### Improvements and fixes:

* Bug fix on adding option for a separate metadata file.

## version 0.6.0 Release

### New Features:

* Custom Http headers. While generating code you can now pass headers including authorization headers to read your Edmx. 
* Generation of multiple files. You can now opt to generate code in multiple files.

## version 0.5.0 Release

OData Connected Service extension v0.5.0 is available on the [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=laylaliu.ODataConnectedService).

You can install or update the extension for v0.5.0 using Visual Studio Extension manager.

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

## version 0.4.0 Release

### New Features:
* Allows user to make generated types internal

### Improvements and fixes:
* Adds support for Visual Studio 2019
* [ [#81](https://github.com/OData/lab/issues/81) ] "OData Connected Service" templates are completely broken right now
