---
title: "OData Migration Library"
description: ""
author: aayc
ms.author: aayc
ms.date: 8/8/2019
ms.topic: article
 
---
# OData Migration Library
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

The OData Migration library provides ASP.NET Core OData V4 services with the capability of handling and responding to OData V3 requests.  In other words, an OData V4 service that uses the OData Migration Library can appear to an OData V3 client as a V3 service.  This may be useful to you if you have migrated your service from OData V3 to OData V4, but wish to bridge the gap between your newly migrated V4 service and your OData V3 clients.

OData Migration (Microsoft.Extensions.OData.Migration) is available as a NuGet package through Nuget.org, and can be installed like any other NuGet package.