---
title: "Data Modification - UPDATE"
description: "Data Modification - UPDATE basics"
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Update an Entity

The OData services SHOULD support PATCH as the preferred means of updating an entity. But also services MAY additionally support PUT. The request below update the ***Emails*** of a person using PATCH.

```json
    PATCH serviceRoot/People('russellwhyte')
    OData-Version: 4.0
    Content-Type: application/json;odata.metadata=minimal
    Accept: application/json

    {
    "@odata.type" : "Microsoft.OData.SampleService.Models.TripPin.Person",
    "Emails" :  ["Russell@example.com", "Russell@contoso.com", "newRussell@contoso.com"]
    }
```

Response Payload

```json
HTTP/1.1 204 No Content
```
