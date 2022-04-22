---
title: " Write OData payload"
description: "This section describes how to write OData payloads including service document, model metadata, entity set, entity, entity references, complex values, primitive values using OData Core APIs."
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Writing payload
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

There are several kinds of OData payloads, including service document, model metadata, entity set, entity, entity reference(s), complex value(s), primitive value(s). OData Core library is designed to write and read all these payloads.

We'll go through each kind of payload here. But first, let's set up the necessary code that is common to all kinds of payloads.

Class `ODataMessageWriter` is the entrance class to write OData payloads.

To construct an `ODataMessageWriter` instance, you'll need to provide an `IODataResponseMessage`, or `IODataRequestMessage`, depending on if you are writing a response or a request. 

OData Core library provides no implementation of these two interfaces, because it is different in different scenarios.

In this tutorial, we'll use the [InMemoryMessage.cs](https://github.com/OData/odata.net/blob/ODataV4-7.x/test/FunctionalTests/Microsoft.OData.Core.Tests/InMemoryMessage.cs).

We'll use the model set up in the EDMLIB section.

```C#
IEdmModel model = builder
                  .BuildAddressType()
                  .BuildCategoryType()
                  .BuildCustomerType()
                  .BuildDefaultContainer()
                  .BuildCustomerSet()
                  .GetModel();
```

Then set up the message to write the payload to.

```C#
MemoryStream stream = new MemoryStream();
InMemoryMessage message = new InMemoryMessage { Stream = stream };
```

Create the settings:

```C#
ODataMessageWriterSettings settings = new ODataMessageWriterSettings();
```

Now we are ready to create the `ODataMessageWriter` instance:

```C#
ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage)message, settings, model);
```

After we have written the payload, we can inspect the memory stream wrapped in `InMemoryMessage` to check what has been written.

```C#
string output = Encoding.UTF8.GetString(stream.ToArray());
Console.WriteLine(output);
Console.Read();
```

Here is the complete program that uses `SampleModelBuilder` and `InMemoryMessage` to write metadata payload:

```C#
IEdmModel model = builder
                  .BuildAddressType()
                  .BuildCategoryType()
                  .BuildCustomerType()
                  .BuildDefaultContainer()
                  .BuildCustomerSet()
                  .GetModel();

MemoryStream stream = new MemoryStream();
InMemoryMessage message = new InMemoryMessage { Stream = stream };

ODataMessageWriterSettings settings = new ODataMessageWriterSettings();

ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage)message, settings, model);
writer.WriteMetadataDocument();

string output = Encoding.UTF8.GetString(stream.ToArray());
Console.WriteLine(output);
```

Now we'll go through writing each kind of payload.

### Write metadata document
Writing metadata is simple, just use `ODataMessageWriter.WriteMetadataDocument()`.

```C#
writer.WriteMetadataDocument();
```

Please notice that this API only works when:
1. Writing a response message, i.e., when constructing `ODataMessageWriter`, you must supply `IODataResponseMessage`.
2. A model is supplied when constructing `ODataMessageWriter`.

So the following two examples won't work.

```C#
ODataMessageWriter writer = new ODataMessageWriter((IODataRequestMessage)message, settings, model);
writer.WriteMetadataDocument();
```

```C#
ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage)message, settings);
writer.WriteMetadataDocument();
```

### Write service document
To write a service document, first create an `ODataServiceDocument` instance, which encapsulates all the necessary information in a service document, which includes entity sets, singletons, and function imports.

In this example, we create a service document that contains two entity sets, one singleton, and one function import.

```C#
ODataServiceDocument serviceDocument = new ODataServiceDocument();
serviceDocument.EntitySets = new[]
{
    new ODataEntitySetInfo
    {
        Name = "Customers",
        Title = "Customers",
        Url = new Uri("Customers", UriKind.Relative)
    },
    new ODataEntitySetInfo
    {
        Name = "Orders",
        Title = "Orders",
        Url = new Uri("Orders", UriKind.Relative)
    }
};
serviceDocument.Singletons = new[]
{
    new ODataSingletonInfo
    {
        Name = "Company",
        Title = "Company",
        Url = new Uri("Company", UriKind.Relative)
    }
};
serviceDocument.FunctionImports = new[]
{
    new ODataFunctionImportInfo
    {
        Name = "GetOutOfDateOrders",
        Title = "GetOutOfDateOrders",
        Url = new Uri("GetOutOfDateOrders", UriKind.Relative)
    }
};
```

Then let's call `WriteServiceDocument()` to write it.

```C#
writer.WriteServiceDocument(serviceDocument);
```

However, this would not work. An `ODataException` will be thrown saying that "The ServiceRoot property in ODataMessageWriterSettings.ODataUri must be set when writing a payload." This is because a valid service document will contain a context URL referencing the metadata document URL which needs to be provided in `ODataMessageWriterSettings`.

The service root information is provided in `ODataUri.ServiceRoot`:

```C#
ODataMessageWriterSettings settings = new ODataMessageWriterSettings();
settings.ODataUri = new ODataUri
{
    ServiceRoot = new Uri("https://services.odata.org/V4/OData/OData.svc/")
};
ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage)message, settings);
writer.WriteServiceDocument(serviceDocument);            
```

As you can see, you don't need to provide a model to write a service document.

It takes efforts to instantiate a service document instance and set up the entity sets, singletons, and function imports. Actually, EdmLib provides a useful API which can generate an appropriately-filled service document instance from model. The API is `GenerateServiceDocument()` defined as an extension method on `IEdmModel`: 

```C#
ODataServiceDocument serviceDocument = model.GenerateServiceDocument();
writer.WriteServiceDocument(serviceDocument);
```

All the entity sets, singletons and function imports whose `IncludeInServiceDocument` attribute is set to true in the model will appear in the generated service document. And according to the spec, only those function imports without any parameters should set their `IncludeInServiceDocument` attribute to true.

As with `WriteMetadataDocument()`, `WriteServiceDocument()` works only when writing response messages.

Besides `WriteServiceDocument()`, there is also `WriteServiceDocumentAsync()` in `ODataMessageWriter`. It is the async version of `WriteServiceDocument()`, so you can call it in an async way:

```C#
await writer.WriteServiceDocumentAsync(serviceDocument);
```

A lot of APIs in writer and reader provide async versions. They work as async counterparts to the APIs without the `Async` suffix.

### Write entity set
An entity set is a collection of entities.
Unlike metadata or service document, you must create another writer from `ODataMessageWriter` to write an entity set. The library is designed to write entity set in a streaming/progressive way, which means the entities are written one by one.

Entity set is represented by the `ODataResourceSet` class. To write an entity set, the following information needs to be provided:
1. The service root which is defined by `ODataUri`.
2. The model, as provided when constructing the `ODataMessageWriter` instance.
3. Entity set and entity type information.

Here is how to write an empty entity set using the old `WriteStart()/WriteEnd()` API (for the new writer API, see [here](/odata/odatalib/fluent-writer-api)).

```C#
ODataMessageWriterSettings settings = new ODataMessageWriterSettings();
settings.ODataUri = new ODataUri
{
    ServiceRoot = new Uri("https://services.odata.org/V4/OData/OData.svc/")
};
ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage)message, settings, model);
IEdmEntitySet entitySet = model.FindDeclaredEntitySet("Customers");
ODataWriter odataWriter = writer.CreateODataResourceSetWriter(entitySet);
ODataResourceSet set = new ODataResourceSet();
odataWriter.WriteStart(set);
odataWriter.WriteEnd();
```

Line 4 gives the service root, line 6 gives the model, and line 10 gives the entity set and entity type information.

The output looks like
```JSON
{"@odata.context":"https://services.odata.org/V4/OData/OData.svc/$metadata#Customers","value":[]}
```

The output contains a context URL which is based on the service root provided in `ODataUri` and the entity set name. There is also a value which turns out to be an empty collection. It will hold the entities if there is any.

There is another way to provide the entity set and entity type information, through `ODataResourceSerializationInfo`. This also eliminates the need to provide a model.

```C#
ODataMessageWriterSettings settings = new ODataMessageWriterSettings();
settings.ODataUri = new ODataUri
{
    ServiceRoot = new Uri("https://services.odata.org/V4/OData/OData.svc/")
};
ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage)message, settings);
ODataWriter odataWriter = writer.CreateODataResourceSetWriter();
ODataResourceSet set = new ODataResourceSet();            
set.SetSerializationInfo(new ODataResourceSerializationInfo
{
    NavigationSourceName = "Customers",
    NavigationSourceEntityTypeName = "Customer"
});
odataWriter.WriteStart(set);
odataWriter.WriteEnd();
```

When writing entity set, you can provide a next page used in server driven paging. 

```C#
ODataResourceSet set = new ODataResourceSet();
set.NextPageLink = new Uri("Customers?next", UriKind.Relative);
odataWriter.WriteStart(set);
odataWriter.WriteEnd();
```

The output will then contain a next link before the value collection.

```JSON
{"@odata.context":"https://services.odata.org/V4/OData/OData.svc/$metadata#Customers","@odata.nextLink":"Customers?next","value":[]}
```

If you want the next link to appear after the value collection, you can set the next link after the `WriteStart()` call, but before the `WriteEnd()` call.

```C#
ODataResourceSet set = new ODataResourceSet();
odataWriter.WriteStart(set);
set.NextPageLink = new Uri("Customers?next", UriKind.Relative);
odataWriter.WriteEnd();
```

```JSON
{"@odata.context":"https://services.odata.org/V4/OData/OData.svc/$metadata#Customers","value":[],"@odata.nextLink":"Customers?next"}
```

There is no additional requirement on next link, as long as it is a valid URL.

To write an entity in an entity set, create an `ODataResource` instance and call `WriteStart()/WriteEnd()` on it in-between the `WriteStart()/WriteEnd()` calls on the entity set.

```C#
ODataResourceSet set = new ODataResourceSet();
odataWriter.WriteStart(set);
ODataResource entity = new ODataResource
{
    Properties = new[]
    {
        new ODataProperty
        {
            Name = "Id",
            Value = 1
        },
        new ODataProperty
        {
            Name = "Name",
            Value = "Tom"
        }
    }
};
odataWriter.WriteStart(entity);
odataWriter.WriteEnd();
odataWriter.WriteEnd();
```

```JSON
{"@odata.context":"https://services.odata.org/V4/OData/OData.svc/$metadata#Customers","value":[{"Id":1,"Name":"Tom"}]}
```

We'll introduce more details on writing entities in the next section.

### Write entity
Entities can be written in several places:
1. As a top level entity.
2. As an entity in an entity set.
3. As an entity expanded within another entity.

To write a top level entity, use `ODataMessageWriter.CreateODataResourceWriter()`.

```C#
ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage)message, settings, model);
IEdmEntitySet entitySet = model.FindDeclaredEntitySet("Customers");
ODataWriter odataWriter = writer.CreateODataResourceWriter(entitySet);
ODataResource entity = new ODataResource
{
    Properties = new[]
    {
        new ODataProperty
        {
            Name = "Id",
            Value = 1
        },
        new ODataProperty
        {
            Name = "Name",
            Value = "Tom"
        }
    }
};
odataWriter.WriteStart(entity);
odataWriter.WriteEnd();
```

```JSON
{"@odata.context":"https://services.odata.org/V4/OData/OData.svc/$metadata#Customers/$entity","Id":1,"Name":"Tom"}
```

We've already introduced in the previous section how to write entities in an entity set. Now we'll look at how to write an entity expanded within another entity.

```C#
ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage)message, settings, model);
IEdmEntitySet entitySet = model.FindDeclaredEntitySet("Customers");
ODataWriter odataWriter = writer.CreateODataResourceWriter(entitySet);
ODataResource customerEntity = new ODataResource
{
    Properties = new[]
    {
        new ODataProperty
        {
            Name = "Id",
            Value = 1
        },
        new ODataProperty
        {
            Name = "Name",
            Value = "Tom"
        }
    }
};
ODataResource orderEntity = new ODataResource
{
    Properties = new[]
    {
        new ODataProperty
        {
            Name = "Id",
            Value = 1
        },
        new ODataProperty
        {
            Name = "Price",
            Value = 3.14M
        }
    }
};
odataWriter.WriteStart(customerEntity);
odataWriter.WriteStart(new ODataNestedResourceInfo
{
    Name = "Purchases",
    IsCollection = true
});
odataWriter.WriteStart(new ODataResourceSet());
odataWriter.WriteStart(orderEntity);
odataWriter.WriteEnd();
odataWriter.WriteEnd();
odataWriter.WriteEnd();
odataWriter.WriteEnd();
```

The output will contain an order entity nested within a customer entity.
```JSON
{"@odata.context":"https://services.odata.org/V4/OData/OData.svc/$metadata#Customers/$entity","Id":1,"Name":"Tom","Purchases":[{"Id":1,"Price":3.14}]}
```
