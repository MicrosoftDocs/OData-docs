---
title: "Ignore query option"
description: Learn how to ignore a query option using OData WebApi v7.
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
---
# Ignore query option
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

In OData WebApi 5.7, we can ignore some query options when calling `ODataQueryOption` `ApplyTo` method, this is helpful when your OData service is integrate with other service that may already applied those query options. 

### Customize 

```C#
public class MyEnableQueryAttribute : EnableQueryAttribute
{
    public override IQueryable ApplyQuery(IQueryable queryable, ODataQueryOptions queryOptions)
    {
       // Don't apply Skip and Top.
       var ignoreQueryOptions = AllowedQueryOptions.Skip | AllowedQueryOptions.Top;
       return queryOptions.ApplyTo(queryable, ignoreQueryOptions);
    }
}
```

### Controller

```C#
[MyEnableQuery]
public IHttpActionResult Get()
{
    return Ok(_products);
}
```

### Result
Then your queryOption won't apply Top and Skip. 
