---
title : "4.25 Bind Custom UriFunctions to CLR Methods"
description: Learn how to use Bind custom urifunctions to CLR methods and built-in functions.
author: madansr7
ms.date: 7/1/2019
---
# Bind Custom UriFunctions to CLR Methods
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Since [Web API OData V5.9.0](https://www.nuget.org/packages/Microsoft.AspNet.OData/5.9.0), it supports to bind the custom UriFunctions to CLR methods now, so user can add,modify or override the existing pre defined built-in functions.

Let's see how to use this feature.

```C#
FunctionSignatureWithReturnType padrightStringEdmFunction =
                     new FunctionSignatureWithReturnType(
                    EdmCoreModel.Instance.GetString(true),
                    EdmCoreModel.Instance.GetString(true),
                    EdmCoreModel.Instance.GetInt32(false));
 
MethodInfo padRightStringMethodInfo = typeof(string).GetMethod("PadRight", new Type[] { typeof(int) });
const string padrightMethodName = "padright";
ODataUriFunctions.AddCustomUriFunction(padrightMethodName, padrightStringEdmFunction, padRightStringMethodInfo);
```

Then you can use filter function like `$filter=padright(ProductName, 5) eq 'Abcd'`.

Related Issue [#612](https://github.com/OData/WebApi/issues/612).
