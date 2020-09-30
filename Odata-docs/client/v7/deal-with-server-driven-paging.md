---
title: "Deal with server-driven paging"
description: "This tutorial describes how to deal with server driven paging"

author: mumbi-o
ms.author: mowambug
ms.date: 7/1/2019
ms.topic: article
 
---
# Server driven paging
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odataclient-v7.md)]

The OData Client for .NET deals with server-driven paging with the help of `DataServiceQueryContinuation` and `DataServiceQueryContinuation<T>`. They are classes that contain the next link of the partial set of items.

Example:

``` csharp
var context = new DefaultContainer(new Uri("https://services.odata.org/v4/TripPinServiceRW/"));

// DataServiceQueryContinuation<T> contains the next link
DataServiceQueryContinuation<Person> token = null;

// Get the first page
var response = await context.People.ExecuteAsync() as QueryOperationResponse<Person>;

// Loop if there is a next link
while ((token = response.GetContinuation()) != null)
{
    // Get the next page
    response = await context.ExecuteAsync<Person>(token) as QueryOperationResponse<Person>;
}
```
