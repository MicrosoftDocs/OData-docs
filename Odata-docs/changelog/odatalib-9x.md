---
title: "OData lib 9.x changelog"
description: OData lib 9.x changelog
author: gathogojr
ms.author: jogathog
ms.date: 7/10/2025
ms.topic: article
---

# OData lib 9.x changelog

OData lib is loosely used to refer to the following group of OData libraries available on the [Nuget gallery](https://www.nuget.org/packages):

- [Microsoft.OData.Core](https://www.nuget.org/packages/Microsoft.OData.Core)
- [Microsoft.OData.Edm](https://www.nuget.org/packages/Microsoft.OData.Edm)
- [Microsoft.Spatial](https://www.nuget.org/packages/Microsoft.Spatial)
- [Microsoft.OData.Client](https://www.nuget.org/packages/Microsoft.OData.Client)

You can install or update any of the NuGet packages for OData lib using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).

## [9.0.0-preview.1](https://github.com/OData/odata.net/releases/tag/9.0.0-preview.1)

### [Microsoft.OData.Core](https://www.nuget.org/packages/Microsoft.OData.Core/9.0.0-preview.1)

* Target .NET 10 Preview [#3206](https://github.com/OData/odata.net/pull/3206)
* Remove JSONP support [#3245](https://github.com/OData/odata.net/pull/3245)
* Default `EnableCaseInsensitive` to `true` when initializing a new instance of `ODataUriResolver` [#3265](https://github.com/OData/odata.net/pull/3265)

### [Microsoft.OData.Edm](https://www.nuget.org/packages/Microsoft.OData.Edm/9.0.0-preview.1)
* Fix CSDL type name validation [#3268](https://github.com/OData/odata.net/pull/3268)

### [Microsoft.OData.Client](https://www.nuget.org/packages/Microsoft.OData.Client/9.0.0-preview.1)
* Remove `DataServiceContext.KeyComparisonGeneratesFilterQuery` flag [#3274](https://github.com/OData/odata.net/pull/3274)
* Avoid unnecessary inclusion of `@odata.type` annotations for declared properties in OData client payloads [#3281](https://github.com/OData/odata.net/pull/3281)
