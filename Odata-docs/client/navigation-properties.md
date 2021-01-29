---
title: "Navigation Properties"
description: "This tutorial describes how to use navigation properties on client side"

author: mumbi-o
ms.author: mowambug
ms.date: 7/1/2019
ms.topic: article
 
---
# Navigation Properties
**Applies To**: [!INCLUDE[appliesto-odataclient](../includes/appliesto-odataclient-v7.md)]

When you execute a query, only entities in the addressed entity set are returned. For example, when a query against the Trippin service returns `People` entities, by default the related `Trips` entities are not returned, even though there is a relationship between `People` and `Trips`. There are two ways to load related entities:
1. Eager Loading.
2. Explicit Loading.

## Eager Loading
You can use the `$expand` query option to request that the response include related entities of the entity set requested for.

On OData client you can use the [Expand](/dotnet/api/microsoft.odata.client.dataservicequery-1.expand) method of [DataServiceQuery&lt;TElement&gt;](/dotnet/api/microsoft.odata.client.dataservicequery-1) to add the `$expand` query option to the request that is sent to the data service. You can request multiple related entity sets by separating them using a comma, as shown in the example below. All entities requested by the query are returned in a single response. The following example returns `Trips` and `Friends` along with the `People` entity set:

``` csharp
DefaultContainer context = new DefaultContainer(new Uri("https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/"));
DataServiceQuery<Person> query = context.People.Expand("Trips,Friends");

foreach(Person person in query)
{
    Console.WriteLine($"First Name: {person.FirstName} Last Name: {person.FirstName}");
    Console.WriteLine($"***{person.FirstName}'s Trips***");
    foreach(Trip trip in person.Trips)
    {
        Console.WriteLine($"Trip Name: {trip.Name}");
    }
    Console.WriteLine($"***{person.FirstName}'s Friends***");
    foreach (Person friend in person.Friends)
    {
        Console.WriteLine($"Friend UserName: {friend.UserName}");
    }
    Console.WriteLine("\n\n");
}
```

Another way is to use the `AddQueryOption` method like in the example below. 

```csharp
DefaultContainer context = new DefaultContainer(new Uri("https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/"));
DataServiceQuery<Person> peopleQuery = context.People
    .AddQueryOption("$expand", "Trips,Friends");

foreach (Person person in peopleQuery)
{
    Console.WriteLine($"First Name: {person.FirstName} Last Name: {person.FirstName}");
    Console.WriteLine($"***{person.FirstName}'s Trips***");
    foreach (Trip trip in person.Trips)
    {
        Console.WriteLine($"Trip Name: {trip.Name}");
    }
    Console.WriteLine($"***{person.FirstName}'s Friends***");
    foreach (Person friend in person.Friends)
    {
        Console.WriteLine($"Friend UserName: {friend.UserName}");
    }
    Console.WriteLine("\n\n");
}
```

## Explicit Loading
In explicit loading, we call the [LoadProperty](/dotnet/api/microsoft.odata.client.dataservicecontext.loadproperty) method on the [DataServiceContext](/dotnet/api/microsoft.odata.client.dataservicecontext) instance to explicitly load related entities. Each call to the `LoadProperty` method makes a separate request to the data service. 

The following example shows how to explicitly load the `Trips` that are related to each returned `Person` instance.

``` csharp
DefaultContainer context = new DefaultContainer(new Uri("https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/"));

foreach (Person person in context.People)
{
    // Explicitly load the Trips for each person.
    context.LoadProperty(person, "Trips");

    // Write out person and trips information.
    Console.WriteLine($"First Name: {person.FirstName} Last Name: {person.FirstName}");
    Console.WriteLine($"***{person.FirstName}'s Trips***");
    foreach (Trip trip in person.Trips)
    {
        Console.WriteLine($"Trip Name: {trip.Name}");
    }
    Console.WriteLine("\n\n");
}
```

[!NOTE]
When you consider which option to use, realize that there is a tradeoff between the number of requests to the data service and the amount of data that is returned in a single response. Use `eager loading` when your application requires associated objects and you want to avoid the added latency of additional requests to explicitly retrieve them. However, if there are cases when the application only needs the data for specific related entity instances, you should consider `explicitly loading` those entities by calling the `LoadProperty` method.
