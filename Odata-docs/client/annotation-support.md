---
title: "Client Annotation Support"
description: "This tutorial describes how to get annotations on client side"

author: mumbi-o
ms.author: mowambug
ms.date: 7/1/2019
ms.topic: article
 
---

# Client Annotation Support
**Applies To**: [!INCLUDE[appliesto-odataclient](../includes/appliesto-odataclient-v7.md)]

Before ODataLib 6.10.0, OData core library supported metadata annotations for elements in the model and instance annotations for particular instances in the payload. But on client side, there wasn’t a good way to access these annotations. ODataLib 6.10.0 introduced several APIs to enable users to access annotations on client side. Basically, OData client follows the rules defined in [OData V4.0 protocol](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part1-protocol/odata-v4.0-errata02-os-part1-protocol-complete.html) (see [6.4 Vocabulary Extensibility](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part1-protocol/odata-v4.0-errata02-os-part1-protocol-complete.html#_Toc406398214)) to access instance or metadata annotations.

## How to get annotations on client side 

All client CLR types in this tutorial are generated using the OData Connected Service extension. Before we dive into this tutorial, check out the [OData Connected Service](/odata/connectedservice/getting-started) tutorial on how to generate the client side proxy classes.

OData Client provides the following APIs for accessing annotations in `DataServiceContext` class.

```c#
    public bool TryGetAnnotation<TResult>(object source, string term, string qualifier, out TResult annotation) 
    public bool TryGetAnnotation<TResult>(object source, string term, out TResult annotation)
    public bool TryGetAnnotation<TFunc, TResult>(Expression<TFunc> expression, string term, string qualifier, out TResult annotation)
    public bool TryGetAnnotation<TFunc, TResult>(Expression<TFunc> expression, string term, out TResult annotation)
```

The first two APIs are for getting annotations associated with a specified object. The last two APIs are for getting annotations for a property, a navigation property, an entity set, a singleton, an operation or an operation import.

In these APIs, *term* is the full qualified name of term, *qualifier* should be provided if an annotation contains qualifier, which means, if the annotation defines qualifier, but user use null as qualifier, then these APIs will return false.

In following part, we will give some examples for what we have supported in ODataLib 6.10.0. But for other elements that we didn’t mentioned, they haven’t been supported yet. 

### Request preference odata.include-annotations 

To get instance annotations, we need to set odata.include-annotations preference in request to specify the set of annotations the client requests to be included.

```c#
    public static void Main(string[] args)
    {
        DefaultContainer dsc = new DefaultContainer(new Uri("https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/"));
        dsc.SendingRequest2 += (sender, eventArgs) =>
        {
            eventArgs.RequestMessage.SetHeader("Prefer", "odata.include-annotations=\"*\"");
        };
    }
```
Please refer to [8.2.8.4 Preference odata.include-annotations](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part1-protocol/odata-v4.0-errata02-os-part1-protocol-complete.html#_Toc406398237) for the rules of this preference.

### Get annotations Code Sample 

#### Get an instance annotation for a feed 

```c#
    var personQueryResponse = dsc.People.Execute();
    personQueryResponse.ToList();
    dsc.TryGetAnnotation(personQueryResponse, fullQualifiedTermName, null /*qualifier*/, out annotation);
```

Please note the first parameter should be an `QueryOperationResponse<Person\>`.

For feed, we have to enumerate the response to materialize the annotation. So in this code block, we call `personQueryResponse.ToList()` to enumerate the response, and then we call `public bool TryGetAnnotation<TResult>(object source, string term, string qualifier, out TResult annotation)` to get the instance annotations for the
 feed.

Currently, qualifier is not supported for instance annotation. So we pass in null for qualifier parameter. Also, we  can use `public bool TryGetAnnotation<TResult>(object source, string term, out TResult annotation)` which doesn't contain qualifier parameter.

Please note, if you want to get a metadata annotation for an entity set, you should use the last two APIs, which will be mentioned later.
 
#### Get an annotation for an entity
Below is a snippet of the `Photo` entity type in the `Trippin Service` metadata.

```xml
<EntityType Name="Photo" HasStream="true">
   <Key>
      <PropertyRef Name="Id" />
   </Key>
   <Property Name="Id" Type="Edm.Int64" Nullable="false">
      <Annotation Term="Org.OData.Core.V1.Permissions">
         <EnumMember>Org.OData.Core.V1.Permission/Read</EnumMember>
      </Annotation>
   </Property>
   <Property Name="Name" Type="Edm.String" />
   <Annotation Term="Org.OData.Core.V1.AcceptableMediaTypes">
      <Collection>
         <String>image/jpeg</String>
      </Collection>
   </Annotation>
</EntityType>
```

```c#
    string fullQualifiedTermName = "Org.OData.Core.V1.AcceptableMediaTypes";
    var photo = dsc.Photos.ByKey(1).GetValue();
    dsc.TryGetAnnotation(photo, fullQualifiedTermName, null, out ICollection<string> annotations);
    foreach(string annotation in annotations)
    {
        Console.WriteLine($"Annotation : {annotation}");
    }
```

Please note the first parameter should be the Clr object. This API will firstly try to get the instance annotation of the `fullQualifiedTermName` and `qualifier`. If the instance annotation doesn't exist, it will try to get the metadata annotation of the `fullQualifiedTermName` and `qualifier`. 

#### Get an annotation for a property in an entity or a navigation property

```c#
    var person = dsc.People.ByKey("russellwhyte").GetValue();
    string fullQualifiedTermName = "Org.OData.Core.V1.Computed";

    // Try to get an annotation for a property
    dsc.TryGetAnnotation<Func<long>, bool>(() => person.Concurrency, fullQualifiedTermName, null, out bool annotation);
    Console.WriteLine($"Annotation : {annotation}");

    // Try to get an annotation for a navigation property
    dsc.TryGetAnnotation<Func<Photo>, string>(() => person.Photo, fullQualifiedTermName, qualifier, out annotation);
```

The first parameter is the closure lambda expression which is to access the property. The API will firstly try to get the instance annotation, if it doesn't exist, it will try to get the metadata annotation for the property.

Below is a snippet of the `Person` entity type in the `Trippin Service` metadata.

```xml
<EntityType Name="Person" OpenType="true">
   <Key>
      <PropertyRef Name="UserName" />
   </Key>
   <Property Name="UserName" Type="Edm.String" Nullable="false">
      <Annotation Term="Org.OData.Core.V1.Permissions">
         <EnumMember>Org.OData.Core.V1.Permission/Read</EnumMember>
      </Annotation>
   </Property>
   <Property Name="FirstName" Type="Edm.String" Nullable="false" />
   <Property Name="LastName" Type="Edm.String" Nullable="false" />
   <Property Name="Emails" Type="Collection(Edm.String)" />
   <Property Name="AddressInfo" Type="Collection(Microsoft.OData.SampleService.Models.TripPin.Location)" />
   <Property Name="Gender" Type="Microsoft.OData.SampleService.Models.TripPin.PersonGender" />
   <Property Name="Concurrency" Type="Edm.Int64" Nullable="false">
      <Annotation Term="Org.OData.Core.V1.Computed" Bool="true" />
   </Property>
   <NavigationProperty Name="Friends" Type="Collection(Microsoft.OData.SampleService.Models.TripPin.Person)" />
   <NavigationProperty Name="Trips" Type="Collection(Microsoft.OData.SampleService.Models.TripPin.Trip)" ContainsTarget="true" />
   <NavigationProperty Name="Photo" Type="Microsoft.OData.SampleService.Models.TripPin.Photo" />
</EntityType>
```

#### Get annotation for a complex value 

```c#
    var address = dsc.People.ByKey("russellwhyte").Select(p => p.AddressInfo).GetValue();
    dsc.TryGetAnnotation(address, fullQualifiedTermName, qualifier, out annotation);
```

The first parameter is the Clr instance of a complex type. This API will firstly try to get the instance annotation of this complex value, if it doesn't exit, it will try to get the metadata annotation for the complex type of the instance.

#### Get metadata annotation for an entity set
Below is a snippet of the `People` entity set in the `Trippin Service` metadata.
```xml
<EntitySet Name="People" EntityType="Microsoft.OData.SampleService.Models.TripPin.Person">
   <NavigationPropertyBinding Path="Friends" Target="People" />
   <NavigationPropertyBinding Path="Microsoft.OData.SampleService.Models.TripPin.Flight/Airline" Target="Airlines" />
   <NavigationPropertyBinding Path="Microsoft.OData.SampleService.Models.TripPin.Flight/From" Target="Airports" />
   <NavigationPropertyBinding Path="Microsoft.OData.SampleService.Models.TripPin.Flight/To" Target="Airports" />
   <NavigationPropertyBinding Path="Photo" Target="Photos" />
   <NavigationPropertyBinding Path="Microsoft.OData.SampleService.Models.TripPin.Trip/Photos" Target="Photos" />
   <Annotation Term="Org.OData.Core.V1.OptimisticConcurrency">
      <Collection>
         <PropertyPath>Concurrency</PropertyPath>
      </Collection>
   </Annotation>
   <Annotation Term="Org.OData.Core.V1.ResourcePath" String="People" />
</EntitySet>
```

If we want to get the value of the `Org.OData.Core.V1.ResourcePath` annotation term, we run the code below:

``` csharp
string fullQualifiedTermName = "Org.OData.Core.V1.ResourcePath";
dsc.TryGetAnnotation<Func<DataServiceQuery<Person>>, string>(() => dsc.People, fullQualifiedTermName, /*qualifier*/ null, out string annotation);
Console.WriteLine($"Annotation : {annotation}");
```

#### Get annotation for a singleton
Below is a snippet of the `Me` singleton in the `Trippin Service` metadata.
```xml
<Singleton Name="Me" Type="Microsoft.OData.SampleService.Models.TripPin.Person">
   <NavigationPropertyBinding Path="Friends" Target="People" />
   <NavigationPropertyBinding Path="Microsoft.OData.SampleService.Models.TripPin.Flight/Airline" Target="Airlines" />
   <NavigationPropertyBinding Path="Microsoft.OData.SampleService.Models.TripPin.Flight/From" Target="Airports" />
   <NavigationPropertyBinding Path="Microsoft.OData.SampleService.Models.TripPin.Flight/To" Target="Airports" />
   <NavigationPropertyBinding Path="Photo" Target="Photos" />
   <NavigationPropertyBinding Path="Microsoft.OData.SampleService.Models.TripPin.Trip/Photos" Target="Photos" />
   <Annotation Term="Org.OData.Core.V1.ResourcePath" String="Me" />
</Singleton>
```

If we want to get the value of the `Org.OData.Core.V1.ResourcePath` annotation term, we run the code below:

``` csharp
string fullQualifiedTermName = "Org.OData.Core.V1.ResourcePath";
dsc.TryGetAnnotation<Func<PersonSingle>, string>(() => dsc.Me, fullQualifiedTermName, /*qualifier*/ null, out string annotation);
Console.WriteLine($"Annotation : {annotation}");
```

#### Get annotation for a function import
Below is a snippet of the `GetNearestAirport` FunctionImport in the `Trippin Service` metadata.
```xml
<FunctionImport Name="GetNearestAirport" Function="Microsoft.OData.SampleService.Models.TripPin.GetNearestAirport" EntitySet="Airports" IncludeInServiceDocument="true">
   <Annotation Term="Org.OData.Core.V1.ResourcePath" String="Microsoft.OData.SampleService.Models.TripPin.GetNearestAirport" />
</FunctionImport>
```

If we want to get the value of the `Org.OData.Core.V1.ResourcePath` annotation term, we run the code below:

``` csharp
string fullQualifiedTermName = "Org.OData.Core.V1.ResourcePath";
dsc.TryGetAnnotation<Func<double, double, AirportSingle>, string>((lat, lon) => dsc.GetNearestAirport(lat, lon), fullQualifiedTermName, /*qualifier*/ null, out string annotation);
Console.WriteLine($"Annotation : {annotation}");
```

#### Get annotation for an entity container
Below is a snippet of the `DefaultContainer` in the `Trippin Service` metadata.
```xml
<EntityContainer Name="DefaultContainer">
    ....
    ....
   <Annotation Term="Org.OData.Core.V1.Description" String="TripPin service is a sample service for OData V4."/>
</EntityContainer>
```

If we want to get the value of the `Org.OData.Core.V1.Description` annotation term, we run the code below:

``` csharp
string fullQualifiedTermName = "Org.OData.Core.V1.Description";
dsc.TryGetAnnotation(dsc, fullQualifiedTermName, out string annotation);
Console.WriteLine($"Annotation : {annotation}");
```

#### Get metadata annotation for a function/action/action import

In section "Get an instance annotation for a feed", we know that to get metadata annotation for an entity set, we should use the last two APIs. This rule is also apply to singleton, function, function import, action and action import.

```c#
    // Try to get a metadata annotation for a function bound to a person
    var person = dsc.People.ByKey("russellwhyte").GetValue();
    dsc.TryGetAnnotation<Func<AirlineSingle>, string>(()=>person.GetFavoriteAirline(),  fullQualifiedTermName, qualifier, out annotation);

    // Try to get a metadata annotaiton for an action bound to a person
    dsc.TryGetAnnotation<Func<string, int, DataServiceActionQuery>, string>((userName, tripId) => person.ShareTrip(userName, tripId), fullQualifiedTermName, qualifier, out annotation);

    // Try to get a metadata annotation for an action import
    dsc.TryGetAnnotation<Func<DataServiceActionQuery>, string>(() => dsc.ResetDataSource(), fullQualifiedTermName, qualifier, out annotation);
```
