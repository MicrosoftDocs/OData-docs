---
title: "Prefer odata.include-annotations"
description: Learn how to use odata.include-annotations for OData WebApi v7. 
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019

---
# Prefer odata.include-annotations
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Since OData WebApi V5.6, it supports ***[odata.include-annotations](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part1-protocol/odata-v4.0-errata02-os-part1-protocol-complete.html#_Toc406398237)***.

### odata.include-annotations

It supports the following four templates:

1. odata.include-annotations="*"  // all annotations
2. odata.include-annotations="-*"  // no annotations
3. odata.include-annotations="display.*" // only annotations under "display" namespace
4. odata.include-annotations="display.subject" // only annotation with term name "display.subject"

Let's have examples:

#### odata.include-annotations=*

We can use the following codes to request all annotations:
```C#
HttpRequestMessage request = new HttpRequestMessage(...);
request.Headers.Add("Prefer", "odata.include-annotations=*");
HttpResponseMessage response = client.SendAsync(request).Result;
...
```

The response will have all annotations:

```C#
{  
  "@odata.context":"https://localhost:8081/$metadata#People/$entity",
  "@odata.id":"https://localhost:8081/People(2)",
  "Entry.GuidAnnotation@odata.type":"#Guid",
  "@Entry.GuidAnnotation":"a6e07eac-ad49-4bf7-a06e-203ff4d4b0d8",
  "@Hello.World":"Hello World.",
  "PerId":2,
  "Property.BirthdayAnnotation@odata.type":"#DateTimeOffset",
  "Age@Property.BirthdayAnnotation":"2010-01-02T00:00:00+08:00",
  "Age":10,
  "MyGuid":"f99080c0-2f9e-472e-8c72-1a8ecd9f902d",
  "Name":"Asha",
  "FavoriteColor":"Red, Green",
  "Order":{  
    "OrderAmount":235342,"OrderName":"FirstOrder"  
  }  
}
```

#### odata.include-annotations=Entry.*

We can use the following codes to request specify annotations:

```C#
HttpRequestMessage request = new HttpRequestMessage(...);
request.Headers.Add("Prefer", "odata.include-annotations=Entry.*");
HttpResponseMessage response = client.SendAsync(request).Result;
...
```

The response will only have annotations in "Entry" namespace:

```C#
{  
  "@odata.context":"https://localhost:8081/$metadata#People/$entity",
  "@odata.id":"https://localhost:8081/People(2)",
  "Entry.GuidAnnotation@odata.type":"#Guid",
  "@Entry.GuidAnnotation":"a6e07eac-ad49-4bf7-a06e-203ff4d4b0d8",
  "PerId":2,
  "Age":10,
  "MyGuid":"f99080c0-2f9e-472e-8c72-1a8ecd9f902d",
  "Name":"Asha",
  "FavoriteColor":"Red, Green",
  "Order":{  
    "OrderAmount":235342,"OrderName":"FirstOrder"  
  }  
} 
```
  
