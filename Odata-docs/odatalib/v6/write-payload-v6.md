---
title: " Write OData Payload"
description: Describes how to write OData payload using OData Core APIs.
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---

# Write OData payload(ODL V6.x)
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v6.md)]

There are several kinds of OData payload, includes service document, model metadata, feed, entry, entity references(s), complex value(s), primitive value(s). OData Core library is designed to write and read all these payloads.

We'll go through each kind of payload here. But first, we'll set up the necessary code that is common to all kind of payload.

Class ODataMessageWriter is the entrance class to write the OData Payload.

To construct an ODataMessageWriter instance, you'll need to provide an IODataResponseMessage, or IODataRequestMessage, depends on if you are writing a response or a request.

OData Core library provides no implementation of these two interfaces, because it is different in different scenario.

In this tutorial, we'll use the [InMemoryMessage.cs](https://github.com/OData/odata.net/blob/master/test/FunctionalTests/Microsoft.OData.Core.Tests/InMemoryMessage.cs).

We'll use the model set up in the EDMLIB section.

``` csharp
IEdmModel model = builder
                .BuildAddressType()
                .BuildCategoryType()
                .BuildCustomerType()
                .BuildDefaultContainer()
                .BuildCustomerSet()
                .GetModel();
```

Then set up the message to write the payload.

``` csharp
MemoryStream stream = new MemoryStream();
InMemoryMessage message = new InMemoryMessage() {Stream = stream};
```

Create the settings:

``` csharp
ODataMessageWriterSettings settings = new ODataMessageWriterSettings();
```

Now we are ready to create the ODataMessageWriter instance:

``` csharp
ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage) message, settings, model);
```

After we write the payload, we can inspect into the memory stream wrapped in InMemoryMessage to check what is written.

``` csharp
string output =Encoding.UTF8.GetString(stream.ToArray());
            Console.WriteLine(output);
            Console.Read();
```

Here is the whole program that use SampleModelBuilder and InMemoryMessage to write metadata payload:

``` csharp
IEdmModel model = builder
                .BuildAddressType()
                .BuildCategoryType()
                .BuildCustomerType()
                .BuildDefaultContainer()
                .BuildCustomerSet()
                .GetModel();

            MemoryStream stream = new MemoryStream();
            InMemoryMessage message = new InMemoryMessage() {Stream = stream};

            ODataMessageWriterSettings settings = new ODataMessageWriterSettings();

            ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage) message, settings, model);
            writer.WriteMetadataDocument();

            string output =Encoding.UTF8.GetString(stream.ToArray());
            Console.WriteLine(output);
```

Now we'll go through on each kind of payload.

## Write metadata

Write metadata is simple, just use WriteMetadataDocument method in ODataMessageWriter.

``` csharp
 writer.WriteMetadataDocument();
```

Please be noticed that this API only works when:
1. Writing response message, that means when constructing the ODataMessageWriter, you must supply IODataRequestMessage.
2. A model is supplied when constructing ODataMessageWriter.

So the following two examples won't work.

``` csharp
ODataMessageWriter writer = new ODataMessageWriter((IODataRequestMessage) message, settings, model);
            writer.WriteMetadataDocument();
```

``` csharp
ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage) message);
            writer.WriteMetadataDocument();
```

## Write service document

To write a service document, first create a ODataServiceDocument instance, which will contains all the necessary information in a service document, that include, entity set, singleton and function import.

In this example, we create a service document that contains two entity sets, one singleton and one function import.

``` csharp
ODataServiceDocument serviceDocument = new ODataServiceDocument();
            serviceDocument.EntitySets = new []
            {
                new ODataEntitySetInfo
                {
                    Name = "Customers",
                    Title = "Customers",
                    Url = new Uri("Customers", UriKind.Relative),
                },
                new ODataEntitySetInfo
                {
                    Name = "Orders",
                    Title = "Orders",
                    Url = new Uri("Orders", UriKind.Relative),
                },
            };

            serviceDocument.Singletons = new[]
            {
                new ODataSingletonInfo
                {
                    Name = "Company",
                    Title = "Company",
                    Url = new Uri("Company", UriKind.Relative),
                },
            };

            serviceDocument.FunctionImports = new[]
            {
                new ODataFunctionImportInfo
                {
                    Name = "GetOutOfDateOrders",
                    Title = "GetOutOfDateOrders",
                    Url = new Uri("GetOutOfDateOrders", UriKind.Relative),
                },
            };
```

Then let's call WriteServiceDocument method to write it.

``` csharp
writer.WriteServiceDocument(serviceDocument);
```

However, this would not work. An ODataException will threw up said that "The ServiceRoot property in ODataMessageWriterSettings.ODataUri must be set when writing a payload." This is because a valid service document will contains a context URL reference to the metadata URL, which need to be told in ODataMessageWriterSettings.

This service root information is provided in ODataUri.ServiceRoot, as this code shows.

``` csharp
ODataMessageWriterSettings settings = new ODataMessageWriterSettings();
            settings.ODataUri = new ODataUri()
            {
                ServiceRoot = new Uri("https://services.odata.org/V4/OData/OData.svc/")
            };

            ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage) message, settings);
writer.WriteServiceDocument(serviceDocument);
```

As you can see, you don't need to provide model to write service document.

It is a little work to instantiate the service document instance and set up the entity sets, singletons and function imports. Actually, the EdmLib provided a useful API which can generate a service document instance from model. The API is named GenerateServiceDocument, and defined as an extension method on IEdmModel. 

``` csharp
ODataServiceDocument serviceDocument = model.GenerateServiceDocument();
            writer.WriteServiceDocument(serviceDocument);

```

All the entity sets, singletons and function imports whose IncludeInServiceDocument attribute is set to true in the model will be in the generated service document. And according to the spec, only those function import without any parameter should set its IncludeInServiceDocument attribute to true.

And as WriteMetadata API, WriteServiceDocument works only when it is writing a response message.

Besides API WriteServiceDocument, there is another API called WriteServiceDocumentAsync in ODataMessageWriter class. It is an async version of WriteServiceDocument, so you can call it in async way.

``` csharp
await writer.WriteServiceDocumentAsync(serviceDocument);
```

A lot of API in writer and reader provides async version of API, they all work as a async complement of the API that without Async suffix.

## Write Feed
Collection of entities is called feed in OData Core Library.
Unlike metadata or service document, you must create another writer on ODatMessageWriter to write the feed. The library is designed to write feed in an streaming way, which means the entry is written one by one. 

Feed is represented by ODataFeed class. To write a feed, following information are needed:

1. The service root, which is defined by ODataUri.
2. The model, as construct parameter of ODataMessageWriter.
3. Entity set and entity type information.

Here is how to write an empty feed.

``` csharp
ODataMessageWriterSettings settings = new ODataMessageWriterSettings();
            settings.ODataUri = new ODataUri()
            {
                ServiceRoot = new Uri("https://services.odata.org/V4/OData/OData.svc/")
            };

            ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage)message, settings, model);

            IEdmEntitySet entitySet = model.FindDeclaredEntitySet("Customers");
            ODataWriter odataWriter = writer.CreateODataFeedWriter(entitySet);

            ODataFeed feed = new ODataFeed();
            odataWriter.WriteStart(feed);
            odataWriter.WriteEnd();
```

Line 4 give the service root, line 6 give the model, and line 10 give the entity set and entity type information.

The output of it looks like this.

``` json
{"@odata.context":"https://services.odata.org/V4/OData/OData.svc/$metadata#Customers","value":[]}
```

The output contains a context URL in the output, which is based on the service root you provided in ODataUri, and the entity set name. There is also a value which is an empty collection, where will hold the entities if there is any.

There is another way to provide the entity set and entity type information, through ODataFeedAndEntrySerializationInfo, and in this no model is needed.

``` csharp
ODataMessageWriterSettings settings = new ODataMessageWriterSettings();
            settings.ODataUri = new ODataUri()
            {
                ServiceRoot = new Uri("https://services.odata.org/V4/OData/OData.svc/")
            };

            ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage)message, settings);

            ODataWriter odataWriter = writer.CreateODataFeedWriter();

            ODataFeed feed = new ODataFeed();
            feed.SetSerializationInfo(new ODataFeedAndEntrySerializationInfo()
            {
                NavigationSourceName = "Customers",
                NavigationSourceEntityTypeName = "Customer"
            });
            odataWriter.WriteStart(feed);
            odataWriter.WriteEnd();
```

When writing feed, you can provide a next page, which is used in server driven paging.

``` csharp
ODataFeed feed = new ODataFeed();
            feed.NextPageLink = new Uri("Customers?next", UriKind.Relative);
            odataWriter.WriteStart(feed);
            odataWriter.WriteEnd();
```

The output will contains a next link before the value collection.

```json
{"@odata.context":"https://services.odata.org/V4/OData/OData.svc/$metadata#Customers","@odata.nextLink":"Customers?next","value":[]}
```

If you want the next link to be appear after the value collection, you can set the next link after the WriteStart call, before the WriteEnd call.

``` csharp
ODataFeed feed = new ODataFeed();
            odataWriter.WriteStart(feed);
            feed.NextPageLink = new Uri("Customers?next", UriKind.Relative);
            odataWriter.WriteEnd();
```

```json
{"@odata.context":"https://services.odata.org/V4/OData/OData.svc/$metadata#Customers","value":[],"@odata.nextLink":"Customers?next"}
```

There is no rule on next link, as long as it is a valid URL.

To write entry in the feed, create the ODataEntry instance and call WriteStart and WriteEnd on it between the WriteStart and WriteEnd call of feed.

``` csharp
ODataFeed feed = new ODataFeed();
            odataWriter.WriteStart(feed);

            ODataEntry entry = new ODataEntry()
            {
                Properties = new[]
                {
                    new ODataProperty()
                    {
                        Name = "Id",
                        Value = 1,
                    },
                    new ODataProperty()
                    {
                        Name = "Name",
                        Value = "Tom",
                    }
                }
            };

            odataWriter.WriteStart(entry);
            odataWriter.WriteEnd();
            odataWriter.WriteEnd();
```

```json
{"@odata.context":"https://services.odata.org/V4/OData/OData.svc/$metadata#Customers","value":[{"Id":1,"Name":"Tom"}]}
```

We'll introduce more details on witting entry in next section.

## Write Entry

Entry can be written in several places:

1. As the top level entry.
2. As the entry in a feed.
3. As the entry expanded an other entry.

To write a top level entry, use ODataMessageWriter.CreateEntryWriter.

``` csharp
ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage)message, settings, model);

            IEdmEntitySet entitySet = model.FindDeclaredEntitySet("Customers");
            ODataWriter odataWriter = writer.CreateODataEntryWriter(entitySet);

            ODataEntry entry = new ODataEntry()
            {
                Properties = new[]
                {
                    new ODataProperty()
                    {
                        Name = "Id",
                        Value = 1,
                    },
                    new ODataProperty()
                    {
                        Name = "Name",
                        Value = "Tom",
                    }
                }
            };

            odataWriter.WriteStart(entry);
            odataWriter.WriteEnd();
```

```json
{"@odata.context":"https://services.odata.org/V4/OData/OData.svc/$metadata#Customers/$entity","Id":1,"Name":"Tom"}
```

We've already introduced how to write entry in a feed in last section, now we'll look at how to write entry expanded in another entry.

``` csharp
ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage)message, settings, model);

            IEdmEntitySet entitySet = model.FindDeclaredEntitySet("Customers");
            ODataWriter odataWriter = writer.CreateODataEntryWriter(entitySet);

            ODataEntry entry = new ODataEntry()
            {
                Properties = new[]
                {
                    new ODataProperty()
                    {
                        Name = "Id",
                        Value = 1,
                    },
                    new ODataProperty()
                    {
                        Name = "Name",
                        Value = "Tom",
                    }
                }
            };

            ODataEntry orderEntry = new ODataEntry()
            {
                Properties = new[]
                {
                    new ODataProperty()
                    {
                        Name = "Id",
                        Value = 1,
                    },
                    new ODataProperty()
                    {
                        Name = "Price",
                        Value = new decimal(3.14)
                    }
                }
            };

            odataWriter.WriteStart(entry);
            odataWriter.WriteStart(new ODataNavigationLink()
            {
                Name = "Purchases",
                IsCollection = true
            });
            odataWriter.WriteStart(new ODataFeed());
            odataWriter.WriteStart(orderEntry);
            odataWriter.WriteEnd();
            odataWriter.WriteEnd();
            odataWriter.WriteEnd();
            odataWriter.WriteEnd();
```

The output will contains order entity inside the customer entity.

```json
{"@odata.context":"https://services.odata.org/V4/OData/OData.svc/$metadata#Customers/$entity","Id":1,"Name":"Tom","Purchases":[{"Id":1,"Price":3.14}]}
```
