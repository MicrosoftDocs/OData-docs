---
title: "Set namespace for operations in model builder"
description: Learn how to set namespace for operations in model builder with OData WebApi v7.
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019

---
# Set namespace for operations in model builder
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Since OData Web API V5.7, it allows to ***set a custom namespace for individual function and action in model builder***.

### Set namespace for function and action in model builder

```C#
ODataModelBuilder builder = new ODataModelBuilder();
builder.Namespace = "Default";
builder.ContainerName = "DefaultContainer";
ActionConfiguration action = builder.Action("MyAction");
action.Namespace = "MyNamespace";
FunctionConfiguration function = builder.Function("MyFunction");
function.Namespace = "MyNamespace";
```

The setting works for ***ODataConventionModelBuilder*** as well.
