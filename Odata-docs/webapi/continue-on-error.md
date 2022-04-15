---
title: "Prefer odata.continue-on-error"
description: Learn how to enable the OData continue-on-error method.
ms.date: 7/1/2019
author: madansr7
---
# Prefer odata.continue-on-error
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Since OData Web API V5.7, it supports ***[odata.continue-on-error](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part1-protocol/odata-v4.0-errata02-os-part1-protocol-complete.html#_Toc406398236)***.

### Enable odata.continue-on-error

Users should call the following API to enable continue on error

- For Microsoft.AspNet.OData (supporting classic ASP.NET Framework):

```C#

        var configuration = new HttpConfiguration();
        configuration.EnableContinueOnErrorHeader();
```

- For Microsoft.AspNetCore.OData (supporting ASP.NET Core):

   It can be enabled in the service's HTTP request pipeline configuration method `Configure(IApplicationBuilder app, IHostingEnvironment env)` of the typical `Startup` class:

```C#

        app.UseMvc(routeBuilder =>
        {
           routeBuilder.Select().Expand().Filter().OrderBy().MaxTop(100).Count()
                        .EnableContinueOnErrorHeader();  // Additional configuration to enable continue on error.
           routeBuilder.MapODataServiceRoute("ODataRoute", "odata", builder.GetEdmModel());
       });
```

#### Prefer odata.continue-on-error

We can use the following codes to prefer continue on error

```C#

HttpRequestMessage request = new HttpRequestMessage(...);
request.Headers.Add("Prefer", "odata.continue-on-error");
request.Content = new StringContent(...);
request.Headers.ContentType = MediaTypeHeaderValue.Parse("multipart/mixed; boundary=batch_abbe2e6f-e45b-4458-9555-5fc70e3aebe0");
HttpResponseMessage response = client.SendAsync(request).Result;
...
```

The response will have all responses, includes the error responses.
