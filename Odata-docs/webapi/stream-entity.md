---
title : "6.4 Custom Stream Entity"
description: Learn how to use the Custom Stream entity for OData WebApi. 
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019

---
# Custom Stream Entity
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Since Web API OData V5.7, it supports to customize entity as stream.

### Fluent API

Users can call fluent API to configure the stream entity. For example,

```C#
var builder = new ODataModelBuilder();
builder.EntityType<Vehicle>().MediaType();
...
IEdmModel model = builder.GetEdmModel();
```

### Convention model builder

Users can put [MediaType] attribute on a CLR class to configure the stream entity. For example,

```C#
[MediaType]  
public class Vehicle
{
  [Key]
  public int Id {get;set;}
  ...
}

var builder = new ODataConventionModelBuilder();
builder.EntityType<Vehicle>();
IEdmModel model = builder.GetEdmModel();
```
