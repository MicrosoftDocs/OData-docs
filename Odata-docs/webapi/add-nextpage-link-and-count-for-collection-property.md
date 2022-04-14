---
title: "Add NextPageLink and $count for collection property"
description: ""
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
---
# Add NextPageLink and $count for collection property
**Applies To**: [!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

In OData WebApi V5.7, it supports to add the NextPageLink and $count for collection property.

### Enable NextPageLink and $count

It's easy to enable the NextPageLink and $count for collection property in controller. Users can only put the [EnableQuery(PageSize=x)] on the action of the controller.
For example:
```C#
[EnableQuery(PageSize = 2)]  
public IHttpActionResult GetColors(int key)  
{  
  IList<Color> colors = new[] {Color.Blue, Color.Green, Color.Red};  
  return Ok(colors);
}  
```

### Sample Requests & Response

Request: ***GET*** https://localhost/Customers(5)/Colors?$count=true

Response content:

```C#
{  
  "@odata.context":"https://localhost/$metadata#Collection(NS.Color)",
  "@odata.count": 3,  
  "@odata.nextLink":"https://localhost/Customers(5)/Colors?$count=true&$skip=2",
  "value": [  
    "Blue",  
    "Green"  
  ]  
} 
```
