---
title: "6.5 Customize unsupported types"
description: ""
category: "6. Customization"
ms.date: 09/11/2017
---
# 6.5 Customize unsupported types

ODataLib has a lot of its primitive types mapping to C# built-in types, for example, `System.String` maps to `Edm.String`, `System.Guid` maps to `Edm.Guid`.
Web API OData adds supporting for some unsupported types in ODataLib, for example: `unsigned int`, `unsigned long`, etc.
 
The mapping list for the unsupported types are:

![](../assets/06-05-typemapping.png)

