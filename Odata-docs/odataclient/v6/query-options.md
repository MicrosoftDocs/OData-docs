---
title: "Basic Queries Options"
description: ""

author: madansr7
ms.author: madansr7
ms.date: 02/19/2019
ms.topic: article
 
---
# Query options
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odataclient-v6.md)]

## $filter

For `GET https://host/service/EntitySet?$filter=Prop eq value`:

``` csharp
var people = context.People.Where(c => c.FirstName == "Peter");
```

For `GET https://host/service/EntitySet?$filter=endswith(Prop, value)`:

``` csharp
var people = context.People.Where(c => c.FirstName.EndsWith("Peter"));
```

For `GET https://host/service/EntitySet?$filter=PropCol/$count eq value`:

``` csharp
var people = context.People.Where(c => c.Trips.Count == 2);
```


For `GET https://host/service/EntitySet?$filter=PropCol/any(d:d/Prop gt value)`:

``` csharp
var people = context.People.Where(c => c.Trips.Any(d => d.Budget > 6000));
```


## $count

For `GET https://host/service/EntitySet/$count`:

``` csharp
var count = context.People.Count();
```

For `GET https://host/service/EntitySet?$count=true`:

``` csharp
var people = context.People.IncludeTotalCount();
```

## $orderby

For `GET https://host/service/EntitySet?$orderby=Prop`:

``` csharp
var people = context.People.OrderBy(c => c.FirstName);
```

For `GET https://host/service/EntitySet?$orderby=Prop desc`:

``` csharp
var people = context.People.OrderByDescending(c => c.FirstName);
```

For `GET https://host/service/EntitySet?$orderby=PropCol/$count`:

``` csharp
var people = context.People.OrderBy(c => c.Trips.Count);
```

## $skip

``` csharp
var people = context.People.Skip(3);
```

## $top

``` csharp
var people = context.People.Take(3);
```

## $expand

``` csharp
var people = context.People.Expand(c => c.Trips);
```

## $select

``` csharp
var people = context.People.Select(c => new {c.FirstName, c.LastName});
```

## A simple combined query combined

``` csharp
var people =
    context.People.IncludeTotalCount()
        .Expand(c => c.Trips)
        .Where(c => c.FirstName == "Peter")
        .OrderBy(c => c.FirstName)
        .Skip(3)
        .Take(3);
```

The order of the query options matters.