---
title: "Basic CRUD Operations"
description: "This tutorial describes how to use basic crud operations on the OData Client."

author: mumbi-o
ms.author: mowambug
ms.date: 7/1/2019
ms.topic: article
 
---
# Basic CRUD Operations

**Applies To**: [!INCLUDE[appliesto-odataclient](../includes/appliesto-odataclient-v7.md)]

## Add an entity
```csharp
var context = new DefaultContainer(new Uri("https://services.odata.org/V4/(S(y5tuj04bxbfsxzimbxbnauqg))/TripPinServiceRW/"));
    var person = Person.CreatePerson("russell", "Russell", "Whyte", new long());
    context.AddToPeople(person);

    context.SaveChanges();
```

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
or use the `Where()` with `First()` or `Single()` API call:

```c#
var person = context.People.Where(c => c.UserName == "russellwhyte").First();
var person = context.People.Where(c => c.UserName == "russellwhyte").Single();
```
Note that `First()` will return one object, even if the lambda expression matches multiple objects.
`Single()` always return one object and will throw an exception if the lambda expression matches multiple objects.

Both `First()` and `Single()` will throw an exception if no person with `UserName` as `russelwhyte` exists.

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

## Handling Exceptions in OData Client
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
     
     if(odataErrorException != null)
     {
       Console.WriteLine(odataErrorException.Message);
     }  
 }
```
