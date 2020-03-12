---
title: "Basic Queries Options"
description: ""

author: madansr7
ms.author: madansr7
ms.date: 7/1/2019
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

For `GET https://host/service/EntitySet?$skip=3:

``` csharp
var people = context.People.Skip(3);
```

## $top

For `GET https://host/service/EntitySet?$top=3:

``` csharp
var people = context.People.Take(3);
```

## $expand

For `GET https://host/service/EntitySet?$expand=Trips:

``` csharp
var people = context.People.Expand(c => c.Trips);
```

## $select

For `GET https://host/service/EntitySet // selects all columns by default
OR
For `GET https://host/service/EntitySet?$select=*: // same as above

``` csharp
var people = context.People;
```

For `GET https://host/service/EntitySet?$select=FirstName,LastName:

``` csharp
var people = context.People.Select(c => new {c.FirstName, c.LastName});
```

## A simple combined query combined

For `GET https://host/service/EntitySet?$expand=Trips&$filter=FirstName eq 'Peter'&$orderby=Firstname asc&$skip=3&$top=3

``` csharp
var people =
    context.People
        .Expand(c => c.Trips)
        .Where(c => c.FirstName == "Peter")
        .OrderBy(c => c.FirstName)
        .Skip(3)
        .Take(3);
```

The order of the query options matters.

Based on the Query Options described above, there are mainly 2 ways to add Query Options to a DataServiceQuery
1. Using strongly typed C# LINQ query methods.
2. Using AddQueryOption method.

## LINQ Query Methods
In the example below, we are creating a linq query expression that returns only Females and orders the result by UserName. We are selecting specific columns

```csharp
DefaultContainer dsc = new DefaultContainer(new Uri("https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/"));

var query = dsc.People.Where(p => p.Gender == PersonGender.Female)
    .OrderBy(p => p.UserName)
    .Select(p => new { p.FirstName, p.LastName, p.UserName, p.Gender });

foreach(var person in query)
{
    Console.WriteLine($"Username: {person.UserName} First Name: {person.FirstName} Gender: {person.Gender}");
}
```

## AddQueryOption Method
The following example shows how to use to the AddQueryOption method to create a DataServiceQuery<TElement>

```csharp
DataServiceQuery peopleQuery = dsc.People
    .AddQueryOption("$filter", "gender eq Female")
    .AddQueryOption("$skip", "3")
    .AddQueryOption("$orderby", "UserName desc");

foreach (Person person in peopleQuery)
{
    Console.WriteLine($"Username: {person.UserName} First Name: {person.FirstName} Gender: {person.Gender}");
}
```