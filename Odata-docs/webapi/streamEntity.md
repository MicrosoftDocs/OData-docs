---
title : "6.4 Custom Stream Entity"


ms.date: 09/16/2015
---
# 6.4 Custom Stream Entity

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
IEdmModel modle = builder.GetEdmModel();
```
