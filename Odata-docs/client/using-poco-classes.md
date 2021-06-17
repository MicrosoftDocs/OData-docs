---
title: "Using POCO classes"
description: "This tutorial describes how to use POCO classes in the OData Client."

author: mumbi-o
ms.author: mowambug
ms.date: 7/1/2019
ms.topic: article
 
---
# Using POCO classes

**Applies To**: [!INCLUDE[appliesto-odataclient](../includes/appliesto-odataclient-v7.md)]

## Using with POCO's

While developing, you may want to use the OData Client without a code generation tool like _ODataConnectedService_. In this case, one can still use the OData Client to access their service.

In order to do this you only need to extend the `DataServiceContext` class in your code and add some convenience methods as shown below:

```csharp
// Person class with Odata annotations
[Key('Id')]
public class Person
{
        public string Id { get; set; }
        public string UserName { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
}

// Dataservice context
 public class Container : DataServiceContext
{
    public Container(Uri serviceRoot) : base(serviceRoot)
    {
              this.People = base.CreateQuery<Person>("People");
    }

    public DataServiceQuery<Person> People { get; }

    public void AddToPeople(Person person)
    {
       base.AddObject("People", person);
    }
}

```

To add new objects to the `People` EntitySet use `context.AddObject("People", person)` or create a convenience method in the class to do the same.

### Loading the service model

This example allows you to use the client while using the current metadata which is fetched from the network. In some cases this is not convenient and to override this behavior you need to change the constructor of the DataService context to include an action that can be used to fetch the model. Codegen tools do this by downloading the metadata and storing it in a verbatim string or in a separate file which is then read upon instantiation of the context.

To provide the same for our code we need to do the following.

```csharp
    // ctor from above
    public Container(Uri serviceRoot) : base(serviceRoot)
    {
        this.People = base.CreateQuery<Person>("People");
        // add the following lines
        this.Format.LoadServiceModel = () => util.GetEdmModel()  /* user action that returns a valid IEdmModel instance */  
        this.Format.UseJson(); /* this instruction causes the model to be loaded instantly else the model is loaded lazily and cached when it's needed */
    }

```

Using the above method one can switch the model they want to use especially when writing tests.

**Note**: Using the OData Connected Service is the recommended way to utilize all the new features in the OData Client.

### Using OriginalNameAttribute
The [OriginalNameAttribute](/dotnet/api/microsoft.odata.client.originalnameattribute) allows one to define an entity model with different property names from the OData service model.

The [OriginalName](/dotnet/api/microsoft.odata.client.originalnameattribute.originalname) property denotes the original name of a property defined in the OData service metadata.

In the example below, our OData service defines a property `IataCode`, but our Model has the property `AirportCode`. We use the `OriginalNameAttribute` to map `AirportCode` to `IataCode`.

```csharp
[Key("Id")]
public class Airport
{
    public string Id { get; set; }

    [OriginalNameAttribute(originalName: "Name")]
    public string AirportName { get; set; }

    [OriginalNameAttribute(originalName: "IataCode")]
    public string AirportCode { get; set; }

}
```
