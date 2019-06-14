---
title: " Relax version constraints"
description: ""


ms.date: 01/16/2015
---
# Relax version constraints
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

For both Web API OData V3 and V4, a flag `IsRelaxedMatch` is introduced to relax the version constraint. With `IsRelaxedMatch = true`, ODataVersionConstraint will allow OData request to contain both V3 and V4 max version headers (V3: `MaxDataServiceVersion`, V4: `OData-MaxVersion`). Otherwise, the service will return response with status code 400. The default value of `IsRelaxdMatch` is false.

```C#
public class ODataVersionConstraint : IHttpRouteConstraint
{
  ......
  public bool IsRelaxedMatch { get; set; }
  ......
}
```

To set this flag, API HasRelaxedODataVersionConstraint() under ODataRoute can be used as following:

```C#
ODataRoute odataRoute = new ODataRoute(routePrefix: null, pathConstraint: null).HasRelaxedODataVersionConstraint();
```
