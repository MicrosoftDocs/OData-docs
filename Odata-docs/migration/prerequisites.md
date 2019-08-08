---
title: "Prerequisites for using OData Migration"
description: ""
author: aayc
ms.author: aayc
ms.date: 8/8/2019
ms.topic: article
 
---
# Prerequisites for using OData Migration
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

The OData Migration library applies to:

1. OData V4 services that wish to support V3 clients
2. ASP.NET Core 2.2+ OData V4 services
3. JSON formatted requests and responses

## Not tested or supported
| OData Feature | Tested | Example|
|---|---|---|
| OData XML/Atom format | Not supported | See [Atom and XML formats](https://www.odata.org/documentation/odata-version-3-0/atom-format/)
| Instance annotations beyond basic types in request/response payloads| Not tested or supported | Example of entity reference link via annotation: `"@odata.id" : "serviceRoot/People('vincentcalabrese')"`   |
| Navigation Links | Not tested | `http://localhost/v3/Customer(1)/$links/Orders` |
| Nested/mixed action return payloads | Not tested | `{ mixed_payload: [{ "name": "entity", "id": 3}, 4, 5, [{"name: "other_entity", "price": 9}]] }`|
| Automatic conversion between Edm.Time and Edm.Duration/Edm.TimeOfDay | Not tested | - |
| JSON Verbose Format | Not supported | -  |

In addition, all features that are [new in OData V4](http://docs.oasis-open.org/odata/new-in-odata/v4.0/new-in-odata-v4.0.html) but not present in OData V3 are not supported by this extension.