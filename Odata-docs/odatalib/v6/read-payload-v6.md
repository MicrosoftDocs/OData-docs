---
title: " Read OData Payload"
description: "Read OData payload using OData Core APIs"

author: saumadan
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Read OData payload(ODL V6.x)
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v6.md)]


The reader API is almost like the writer API, so you can expect the symmetry here.

First, we'll set up the necessary code that is common to all kind of payload.

Class ODataMessageReader is the entrance class to read the OData Payload.

To construct an ODataMessageReader instance, you'll need to provide an IODataResponseMessage, or IODataRequestMessage, depends on if you are reading a response or a request. 

OData Core library provides no implementation of these two interfaces, because it is different in different scenario.

In this tutorial, we'll still use the [InMemoryMessage.cs](https://github.com/OData/odata.net/blob/master/test/FunctionalTests/Microsoft.OData.Core.Tests/InMemoryMessage.cs).

We'll still use the model set up in the EDMLIB section.

```c#
IEdmModel model = builder
                .BuildAddressType()
                .BuildCategoryType()
                .BuildCustomerType()
                .BuildDefaultContainer()
                .BuildCustomerSet()
                .GetModel();
```

Then set up the message to read the payload.

```c#
MemoryStream stream = new MemoryStream();
InMemoryMessage message = new InMemoryMessage() {Stream = stream};
```

Create the settings:

```c#
ODataMessageReaderSettings settings = new ODataMessageReaderSettings();
```

Now we are ready to create the ODataMessageReader instance:

```c#
ODataMessageReader reader = new ODataMessageReader((IODataResponseMessage) message, settings);
```

We'll use the code in the first part to write the payload, and in this section use the reader to read the payload. After write the payload, we should set the Position of MemoryStream to zero.

```c#
stream.Position = 0;
```

Here is the whole program that use SampleModelBuilder and InMemoryMessage to first write then read metadata payload:

```c#
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
            InMemoryMessage message = new InMemoryMessage() { Stream = stream };

            ODataMessageWriterSettings writerSettings = new ODataMessageWriterSettings();

            ODataMessageWriter writer = new ODataMessageWriter((IODataResponseMessage)message, writerSettings, model);

            writer.WriteMetadataDocument();
            stream.Position = 0;

            ODataMessageReaderSettings settings = new ODataMessageReaderSettings();
            
            ODataMessageReader reader = new ODataMessageReader((IODataResponseMessage)message, settings);

            IEdmModel modelFromReader = reader.ReadMetadataDocument();
```

Now we'll go through on each kind of payload.

### Read metadata
Read metadata is simple, just use ReadMetadataDocument method in ODataMessageReader.


```c#

 reader.ReadMetadataDocument();
```

Just like writing metadata, this API only works when reading response message, that means when constructing the ODataMessageReader, you must supply IODataResponseMessage.

### Read service document
Read service document is through the ReadServiceDocument API.



```c#
ODataMessageReaderSettings readerSettings = new ODataMessageReaderSettings();

            ODataMessageReader reader = new ODataMessageReader((IODataResponseMessage)message, readerSettings, model);

            ODataServiceDocument serviceDocumentFromReader = reader.ReadServiceDocument();
```

And as ReadMetadata API, ReadServiceDocument works only when it is reading a response message.

Besides API ReadServiceDocument, there is another API called ReadServiceDocumentAsync in ODataMessageReader class. It is an async version of ReadServiceDocument, so you can call it in async way.


```c#
ODataServiceDocument serviceDocument = await reader.ReadServiceDocumentAsync();
```

### Read Feed
To read a feed, you must create another reader on ODataFeedReader to read the feed. The library is designed to read feed in an streaming way, which means the entry is read one by one. 

Here is how to read a feed.

```c#
ODataMessageReader reader = new ODataMessageReader((IODataResponseMessage)message, readerSettings, model);
            ODataReader feedReader = reader.CreateODataFeedReader(entitySet, entitySet.EntityType());
            while (feedReader.Read())
            {
                switch (feedReader.State)
                {
                    case ODataReaderState.FeedEnd:
                        ODataFeed feedFromReader = (ODataFeed)feedReader.Item;
                        break;
                    case ODataReaderState.EntryEnd:
                        ODataEntry entryFromReader = (ODataEntry)feedReader.Item;
                        break;
                }
            }
```

### Read Entry
To read a top level entry, use ODataMessageReader.CreateEntryReader.
Other than that, there is no different compared to read feed.

```c#
ODataMessageReader reader = new ODataMessageReader((IODataResponseMessage)message, readerSettings, model);
            ODataReader feedReader = reader.CreateODataEntryReader(entitySet, entitySet.EntityType());
            while (feedReader.Read())
            {
                switch (feedReader.State)
                {
                    case ODataReaderState.FeedEnd:
                        ODataFeed feedFromReader = (ODataFeed)feedReader.Item;
                        break;
                    case ODataReaderState.EntryEnd:
                        ODataEntry entryFromReader = (ODataEntry)feedReader.Item;
                        break;
                }
            }
```