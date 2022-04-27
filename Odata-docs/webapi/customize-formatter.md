---
title : "6.3 Customize OData Formatter"
description: "Customize OData Formatter"
category: [6. Customization]
ms.date: 7/1/2019
author: madansr7
---
# Customize OData Formatter
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

This page illustrates how to use sensibility points in the ODataFormatter and plugin custom OData serializers/deserializers, gives a sample extends the ODataFormatter to add support of OData instance annotations.

Let's see this sample.

### CLR Model

First of all, we create the following CLR classes as our model:

```C#
public class Document
{
        public Document()
        {
        }
        public Document(Document d)
        {
            ID = d.ID;
            Name = d.Name;
            Content = d.Content;
        }
        public int ID { get; set; }
        public string Name { get; set; }
        public string Content { get; set; }
        [NotMapped]
        public double Score { get; set; }
}
```

If I search for documents by sending the search query, in the result, I'd like have a score for the match for each document, as the score is dependent on the in coming query, it cannot be modeled as a property on response's document, it should be modeled as an annotation on the document. Let's do that.

### Build Edm Model

Now, we can build a pretty simple Edm Model as:

```C#
private static IEdmModel GetEdmModel()
{
    ODataConventionModelBuilder builder = new ODataConventionModelBuilder();
    builder.EntitySet<Document>("Documents");
    return builder.GetEdmModel();
}
```

### Customize OData Formatter

#### A custom entity serializer

This entity serializer is to add the score annotation (org.northwind.search.score) to document entries.

```C#
public class AnnotatingEntitySerializer : ODataEntityTypeSerializer
{
    public AnnotatingEntitySerializer(ODataSerializerProvider serializerProvider)
           : base(serializerProvider)
    {
    }
    public override ODataEntry CreateEntry(SelectExpandNode selectExpandNode, EntityInstanceContext entityInstanceContext)
    {
        ODataEntry entry = base.CreateEntry(selectExpandNode, entityInstanceContext);
        Document document = entityInstanceContext.EntityInstance as Document;
        if (entry != null && document != null)
        {
            // annotate the document with the score.
            entry.InstanceAnnotations.Add(new ODataInstanceAnnotation("org.northwind.search.score", new ODataPrimitiveValue(document.Score)));
        }
        return entry;
    }
}
```

#### A custom serializer provider

This serializer provider is to inject the AnnotationEntitySerializer.

```C#
public class CustomODataSerializerProvider : DefaultODataSerializerProvider
{
    private AnnotatingEntitySerializer _annotatingEntitySerializer;
    public CustomODataSerializerProvider()
    {
        _annotatingEntitySerializer = new AnnotatingEntitySerializer(this);
    }
    public override ODataEdmTypeSerializer GetEdmTypeSerializer(IEdmTypeReference edmType)
    {
        if (edmType.IsEntity())
        {
            return _annotatingEntitySerializer;
        }
        return base.GetEdmTypeSerializer(edmType);
    }
}
```

#### Setup configuration with customized ODataFormatter

Create the formatters with the custom serializer provider and use them in the configuration.

```C#
[HttpPost]
public void Configuration(IAppBuilder appBuilder)
{
    HttpConfiguration config = new HttpConfiguration();
    config.MapODataServiceRoute("odata", "odata", GetEdmModel());
    var odataFormatters = ODataMediaTypeFormatters.Create(new CustomODataSerializerProvider(), new DefaultODataDeserializerProvider());
    config.Formatters.InsertRange(0, odataFormatters);
    appBuilder.UseWebApi(config);
}
```

#### Controller Samples

You should add a score to result documents.

```C#
public IEnumerable<Document> GetDocuments(string search)
{
    var results = FindDocument(search);
    return results.Select(d => new Document(d) { Score = ... });
}
```

#### Request Samples

Add prefer header and send request.

```C#
HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, baseAddress + "odata/Documents?search=test");
request.Headers.TryAddWithoutValidation("Prefer", "odata.include-annotations=\"*\"");
var response = client.SendAsync(request).Result;
```

#### Response Samples

Annotation is supported in newest night build, 5.6.0-beta1.

```C#
{
    "@odata.context":"https://localhost:9000/odata/$metadata#Documents","value":
    [
        {
            "@org.northwind.search.score":1.0,"ID":1,"Name":"Another.txt","Content":"test"
        }
    ]
}
```

### Conclusion

The sample has a custom entity type serializer, AnnotatingEntitySerializer, that adds the instance annotation to ODataEntry by overriding the CreateEntry method. It defines a custom ODataSerializerProvider to provide AnnotatingEntitySerializer instead of ODataEntityTypeSerializer. Then creates the OData formatters using this serializer provider and uses those formatters in the configuration.
