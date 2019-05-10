---
title: "7.8 OData Web API 6.0.0 alpha1"
description: ""
category: "7. Release notes"
permalink: "/6.0.0 alpha1"
ms.date: 06/05/2015
---
# 7.8 OData Web API 6.0.0 alpha1

The NuGet packages for OData Web API v6.0.0 alpha1 are now available on the [myget](https://www.myget.org/F/odatavnext/api/v2).

### Configure package source
You can configure the NuGet package source for OData Web API v6.0.0 preview releases in the [Package Manager Settings](https://docs.nuget.org/Consume/Package-Manager-Dialog):

![](../assets/07-06-package-src.png)

### Download this release
You can install or update the NuGet packages for OData Web API v6.0.0 alpha1 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```
PM> Install-Package Microsoft.AspNet.OData -Version 6.0.0-alpha1 -Pre
```

### What’s in this release?
This release contains the first preview of the next version of OData Web API which is built on [ASP.NET 5 and MVC 6](https://www.asp.net/vnext). This preview includes the basic support of:

 - Querying service metadata
 - Querying entity sets
 - CRUD of single entity
 - Querying structural or navigation property
 - $filter query option
 
OData Web API v6.0.0 alpha1 package has a dependency on [ODataLib 6.12](https://www.nuget.org/packages/Microsoft.OData.Core/6.12.0).

### Where's the sample service?
You can take a look at [a basic sample service](https://github.com/OData/WebApi/tree/v6.0.0-alpha1/vNext/samples/ODataSample.Web) built by this library.

Now the sample service can support (but not limit to) the following requests:

 - Metadata. `GET https://localhost:9091/odata/$metadata`
 - EntitySet. `GET https://localhost:9091/odata/Products`
 - Entity. `GET https://localhost:9091/odata/Products(1)`
 - Structural property. `GET https://localhost:9091/odata/Customers(1)/FirstName`
 - Navigation property. `GET https://localhost:9091/odata/Customers(1)/Products`
 - $filter. `GET https://localhost:9091/odata/Products?$filter=ProductId%20gt%201`
 - Create. `POST https://localhost:9091/odata/Products`
 - Delete. `DELETE https://localhost:9091/odata/Products(2)`
 - Full update. `PUT https://localhost:9091/odata/Products(2)`

### Where's the source code?
You can view the source code of this library at our [OData Web API repository](https://github.com/OData/WebApi/tree/v6.0.0-alpha1/vNext). We warmly welcome any feedback, proposition and contribution from you!