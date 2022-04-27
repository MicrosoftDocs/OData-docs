---
title: "Write NextPageLink/Count for collection - odatalib"
description: "This section describes how to write the NextPageLink and Count instance annotation in top-level collection payload."
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Writing next page links
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

From ODataLib 6.13.0, it supports to write the NextPageLink/Count instance annotation in top-level collection payload. Let's have an example:

When you want to serialize a collection instance, you should first create an object of `ODataCollectionStart`, in which you can set the next page link and the count value.

```C#

ODataMessageWriter messageWriter = new ODataMessageWriter(...);
IEdmTypeReference elementType = ...;
ODataCollectionWriter writer = messageWriter.CreateODataCollectionWriter(elementType);

ODataCollectionStart collectionStart = new ODataCollectionStart();

collectionStart.NextPageLink = new Uri("https://any");
collectionStart.Count = 5;

writer.WriteStart(collectionStart);

ODataCollectionValue collectionValue = ...;
if (collectionValue != null)
{
    foreach (object item in collectionValue.Items)
    {
        writer.WriteItem(item);
    }
}
writer.WriteEnd();
```

The payload looks like:

```C#
{
  "@odata.context":".../$metadata#Collection(...)",
  "@odata.count":5,
  "@odata.nextLink":"https://any",
  "value":[
    ...
  ]
}
```
