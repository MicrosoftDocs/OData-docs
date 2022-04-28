---
title: "OData Simplified Uri convention"
description: Learn how to use the OData Simplified Uri convention for OData WebApi. 
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019

---
# OData Simplified Uri convention
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

OData v4 Web API [5.8 RC](https://www.nuget.org/packages/Microsoft.AspNet.OData/5.8.0-rc) 
introduces a new OData Simplified Uri convention that supports key-as-segment and default OData Uri convention side-by-side.

```Text
~/odata/Customers/0
~/odata/Customers(0)
```

To enable the ODataSimplified Uri convention, in `WebApiConfig.cs`:
```C#
// ...
var model = builder.GetEdmModel();

// Set Uri convention to ODataSimplified
config.SetUrlConventions(ODataUrlConventions.ODataSimplified);
config.MapODataServiceRoute("odata", "odata", model);
```
