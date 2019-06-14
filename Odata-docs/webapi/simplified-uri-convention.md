---
title: "OData Simplified Uri convention"
description: ""

ms.date: 12/20/2015
---
# OData Simplified Uri convention

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
