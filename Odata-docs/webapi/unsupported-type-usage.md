---
title: " Customize unsupported types"
description: ""
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019

---
# Customize unsupported types
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

ODataLib has a lot of its primitive types mapping to C# built-in types, for example, `System.String` maps to `Edm.String`, `System.Guid` maps to `Edm.Guid`.
Web API OData adds supporting for some unsupported types in ODataLib, for example: `unsigned int`, `unsigned long`, etc.
 
The mapping list for the unsupported types are:

![typemapping](/odata/assets/06-05-typemapping.png)
