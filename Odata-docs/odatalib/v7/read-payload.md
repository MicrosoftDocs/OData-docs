---
title: " Read OData payload"
description: "This section describes how to read OData payload using OData Core APIs."
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Reading a payload
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

The reader API is similar to the writer API, and you can expect symmetry here.

First, we'll set up the necessary code that is common to all kinds of payloads.

Class `ODataMessageReader` is the entrance class to read OData payloads.

To construct an `ODataMessageReader` instance, you'll need to provide an `IODataResponseMessage` or `IODataRequestMessage`, depending on if you are reading a response or a request.

OData Core library provides no implementation of these two interfaces, because it is different in different scenarios.

In this tutorial, we'll still use the [InMemoryMessage.cs](https://github.com/OData/odata.net/blob/ODataV4-7.x/test/FunctionalTests/Microsoft.OData.Core.Tests/InMemoryMessage.cs).

We'll still use the model set up in the EDMLIB section.

```C#
IEdmModel model = builder
                  .BuildAddressType()
                  .BuildCategoryType()
                  .BuildCustomerType()
                  .BuildDefaultContainer()
                  .BuildCustomerSet()
                  .GetModel();
```

Then set up the message to read the payload from.

```C#
MemoryStream stream = new MemoryStream();
InMemoryMessage message = new InMemoryMessage { Stream = stream };
```

Create the settings:

```C#
ODataMessageReaderSettings settings = new ODataMessageReaderSettings();
```

Now we are ready to create an `ODataMessageReader` instance:

```C#
ODataMessageReader reader = new ODataMessageReader((IODataResponseMessage)message, settings);
```

We'll use the code in the previous section to write the payloads, and in this section use the reader to read them. After writing the payloads, we should set `MemoryStream.Position` to zero.

```C#
stream.Position = 0;
```

Here is the complete program that uses `SampleModelBuilder` and `InMemoryMessage` to write and then read a metadata document:

```C#
IEdmModel model = builder
                  .BuildAddressType()
                  .BuildCategoryType()
                  .BuildOrderType()
                  .BuildCustomerType()
                  .BuildDefaultContainer()
                  .BuildOrderSet()
                  .BuildCustomerSet()
                  .GetModel();
MemoryStream stream = new MemoryStream();
InMemoryMessage message = new InMemoryMessage { Stream = stream };
ODataMessageWriterSettings writerSettings = new ODataMessageWriterSettings();
ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage)message, writerSettings, model);
writer.WriteMetadataDocument();
stream.Position = 0;
ODataMessageReaderSettings settings = new ODataMessageReaderSettings();
ODataMessageReader reader = new ODataMessageReader((IODataResponseMessage)message, settings);
IEdmModel modelFromReader = reader.ReadMetadataDocument();
```

Now we'll go through each kind of payload.

### Read metadata document
Reading metadata is simple, just use `ODataMessageReader.ReadMetadataDocument()`.

```C#
reader.ReadMetadataDocument();
```

Similar to writing a metadata document, this API only works when reading a response message, i.e., when constructing the `ODataMessageReader`, you must supply `IODataResponseMessage`.

### Read service document
Reading service document is through the `ODataMessageReader.ReadServiceDocument()` API.

```C#
ODataMessageReaderSettings readerSettings = new ODataMessageReaderSettings();
ODataMessageReader reader = new ODataMessageReader((IODataResponseMessage)message, readerSettings, model);
ODataServiceDocument serviceDocumentFromReader = reader.ReadServiceDocument();
```

This works only when reading a response message.

There is another API `ODataMessageReader.ReadServiceDocumentAsync()`. It is the async version of `ReadServiceDocument()`, and you can call it in an async way:

```C#
ODataServiceDocument serviceDocument = await reader.ReadServiceDocumentAsync();
```

### Read entity set
To read an entity set, you must create another reader `ODataResourceSetReader`. The library is designed to read entity set in a streaming/progressive way, which means the entities are read one by one.

Here is how to read an entity set.

```C#
ODataMessageReader reader = new ODataMessageReader((IODataResponseMessage)message, readerSettings, model);
ODataReader setReader = reader.CreateODataResourceSetReader(entitySet, entitySet.EntityType());
while (setReader.Read())
{
    switch (setReader.State)
    {
        case ODataReaderState.ResourceSetEnd:
            ODataResourceSet setFromReader = (ODataResourceSet)setReader.Item;
            break;
        case ODataReaderState.ResourceEnd:
            ODataResource entityFromReader = (ODataResource)setReader.Item;
            break;
    }
}
```

### Read entity
To read a top level entity, use `ODataMessageReader.CreateODataResourceReader()`.
Except that, there is no difference compared to reading an entity set.

```C#
ODataMessageReader reader = new ODataMessageReader((IODataResponseMessage)message, readerSettings, model);
ODataReader entityReader = reader.CreateODataResourceReader(entitySet, entitySet.EntityType());
while (entityReader.Read())
{
    switch (entityReader.State)
    {
        case ODataReaderState.ResourceSetEnd:
            ODataResourceSet setFromReader = (ODataResourceSet)entityReader.Item;
            break;
        case ODataReaderState.ResourceEnd:
            ODataResource entityFromReader = (ODataResource)entityReader.Item;
            break;
    }
}
```
