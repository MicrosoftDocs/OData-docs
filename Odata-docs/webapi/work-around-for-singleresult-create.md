---
title : "10.2 Work around for SingleResult.Create an empty result"
description: Learn about the work around that addresses when SingleResult.Create returns an empty result.
author: madansr7
ms.date: 7/1/2019
---
# Work around for SingleResult.Create an empty result
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

[!Note]: This work around is for https://github.com/OData/WebApi/issues/170, which is not applicable for Microsoft.AspNetCore.OData v7.x.**

When SingleResult.Create takes in a query that returns an empty result, a SerializationException is being thrown.

Let's see a work-around about this [issue](https://github.com/OData/WebApi/issues/170).

### NullEntityTypeSerializer

First of all, we define the NullEntityTypeSerializer to handle null value:

```C#
public class NullEntityTypeSerializer : ODataEntityTypeSerializer
{
    public NullEntityTypeSerializer(ODataSerializerProvider serializerProvider)
        : base(serializerProvider)
    { }

    public override void WriteObjectInline(object graph, IEdmTypeReference expectedType, ODataWriter writer, ODataSerializerContext writeContext)
    {
        if (graph != null)
        {
            base.WriteObjectInline(graph, expectedType, writer, writeContext);
        }
    }
}
```

### NullSerializerProvider

Now, we can define a NullSerializerProvider, we need to avoid the situation of function,action call:

```C#
public class NullSerializerProvider : DefaultODataSerializerProvider
{
    private readonly NullEntityTypeSerializer _nullEntityTypeSerializer;

    public NullSerializerProvider()
    {
        _nullEntityTypeSerializer = new NullEntityTypeSerializer(this);
    }

    public override ODataSerializer GetODataPayloadSerializer(IEdmModel model, Type type, HttpRequestMessage request)
    {
        var serializer = base.GetODataPayloadSerializer(model, type, request);
        if (serializer == null)
        {
			var functions = model.SchemaElements.Where(s => s.SchemaElementKind == EdmSchemaElementKind.Function
                                                            || s.SchemaElementKind == EdmSchemaElementKind.Action);
            var isFunctionCall = false;
            foreach (var f in functions)
            {
                var fname = string.Format("{0}.{1}", f.Namespace, f.Name);
                if (request.RequestUri.OriginalString.Contains(fname))
                {
                    isFunctionCall = true;
                    break;
                }
            }

            // only, if it is not a function call
            if (!isFunctionCall)
            {
                var response = request.GetOwinContext().Response;
                response.OnSendingHeaders(state =>
                {
                    ((IOwinResponse)state).StatusCode = (int)HttpStatusCode.NotFound;
                }, response);
                
                // in case you are NOT using Owin, uncomment the following and comment everything above
                // HttpContext.Current.Response.StatusCode = (int)HttpStatusCode.NotFound;
            }
            return _nullEntityTypeSerializer;
        }
        return serializer;
    }
}
```

### Formatters

Add NullSerializerProvider in ODataMediaTypeFormatters:

```C#
var odataFormatters = ODataMediaTypeFormatters.Create(new NullSerializerProvider(), new DefaultODataDeserializerProvider());
config.Formatters.InsertRange(0, odataFormatters);
```
