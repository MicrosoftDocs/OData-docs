---
title: "Basic CRUD Operations"
description: ""

author: madansr7
ms.author: madansr7
ms.date: 7/1/2019
ms.topic: article
 
---
# Basic CRUD operations

**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odataclient-v6.md)]

## Request an entity set

```csharp
var context = new DefaultContainer(new Uri("https://services.odata.org/v4/(S(lqbvtwide0ngdev54adgc0lu))/TripPinServiceRW/"));

var people = context.People.Execute();
```

The `Execute()` API call will return an `IEnumerable<Person>`.

## Request an individual entity

Either use the `Where()` API call:

```c#
var people = context.People.Where(c => c.UserName == "russellwhyte");
foreach (var person in people)
{
    // business logic
}
```

or use the `ByKey()` API:

```C#
var person = context.People.ByKey(userName: "russellwhyte").GetValue();
```

or

```C#
var person = context.People.ByKey(new Dictionary<string, object>() {{"UserName", "russellwhyte"}}).GetValue();
```

The `person` object returned are all of the type `Person`.

## Update an entity

``` csharp
var context = new DefaultContainer(new Uri("https://services.odata.org/v4/(S(lqbvtwide0ngdev54adgc0lu))/TripPinServiceRW/"));

var person = context.People.ByKey(userName: "russellwhyte").GetValue(); // get an entity
person.FirstName = "Ross"; // change its property
context.UpdateObject(person); // create an update request

context.SaveChanges(); // send the request
```

Please be noted that the request is not sent until you call the `SaveChanges()` API. The `context` will track all the changes you make to the entities attached to it (by getting `person` from the service you attached it to the `context`) and will send requests for the changes when `SaveChanges` is called.

The sample above will send a `PATCH` request to the service in which the body is the whole `Person` containing properties that are unchanged. There is also a way to track the changes on the property level to only send changed properties in an update request. It will be introduced in later posts.

## Delete an entity

``` csharp
var context = new DefaultContainer(new Uri("https://services.odata.org/v4/(S(lqbvtwide0ngdev54adgc0lu))/TripPinServiceRW/"));

var person = context.People.ByKey(userName: "russellwhyte").GetValue(); // get an entity
context.DeleteObject(person); // create a delete request

context.SaveChanges(); // send the request
```

## Handle Client Exceptions
OData Client has implemented a number of `Exception` classes including `DataServiceQueryException` and `DataServiceClientException`. Below is an example of how `Exception`s can be handled while using OData Client.

``` csharp
 try
 {
    //Code that throws exception from the service
 }
 catch (DataServiceQueryException ex)
 {
     //Client level Exception message
     Console.WriteLine(ex.Message);

     //The InnerException of DataServiceQueryException contains DataServiceClientException
     DataServiceClientException dataServiceClientException = ex.InnerException as DataServiceClientException;
     Console.WriteLine(dataServiceClientException.Message);

     // You can get ODataErrorException from dataServiceClientException.InnerException
     // This object holds Exception as thrown from the service
     // ODataErrorException contains odataErrorException.Message contains a message string that conforms to dotnet
     // Exception.Message standards
     ODataErrorException odataErrorException = dataServiceClientException.InnerException as ODataErrorException;
     Console.WriteLine(odataErrorException.Message);
 }
```
