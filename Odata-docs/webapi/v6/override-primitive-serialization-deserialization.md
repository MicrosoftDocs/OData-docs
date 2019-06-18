---
title: "Override primitive serialization and deserialization of payload in webapi"
description: ""

author: madansr7
ms.author: madansr7
ms.date: 02/19/2019
ms.topic: article
 
---
# Override primitive serialization and deserialization of payload
**Applies To**: [!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v6.md)]

Since ODataLib 6.12.0, it supports to customize the payload value converter to override the primitive serialization and deserialization of payload.

## New public API

The new class `ODataPayloadValueConverter` provides a default implementation for value conversion, and also allows developer to override by implementing `ConvertFromPayloadValue` and `ConvertToPayloadValue`.

``` csharp
public class Microsoft.OData.Core.ODataPayloadValueConverter {
	public ODataPayloadValueConverter ()

	public virtual object ConvertFromPayloadValue (object value, Microsoft.OData.Edm.IEdmTypeReference edmTypeReference)
	public virtual object ConvertToPayloadValue (object value, Microsoft.OData.Edm.IEdmTypeReference edmTypeReference)
}
```

New helper functions to set converter to model so the new converter can be applied:

``` csharp
  public static void SetPayloadValueConverter (Microsoft.OData.Edm.IEdmModel model, Microsoft.OData.Core.ODataPayloadValueConverter converter)
  public static Microsoft.OData.Core.ODataPayloadValueConverter GetPayloadValueConverter (Microsoft.OData.Edm.IEdmModel model)
}
```

## Sample

Here we are trying to override the default converter to support the "R" format of date and time. It is quite simple. 

***1. Define DataTimeOffset converter***

``` csharp
internal class DateTimeOffsetCustomFormatPrimitivePayloadValueConverter : ODataPayloadValueConverter
{
    public override object ConvertToPayloadValue(object value, IEdmTypeReference edmTypeReference)
    {
        if (value is DateTimeOffset)
        {
            return ((DateTimeOffset)value).ToString("R", CultureInfo.InvariantCulture);
        }

        return base.ConvertToPayloadValue(value, edmTypeReference);
    }

    public override object ConvertFromPayloadValue(object value, IEdmTypeReference edmTypeReference)
    {
        if (edmTypeReference.IsDateTimeOffset() && value is string)
        {
            return DateTimeOffset.Parse((string)value, CultureInfo.InvariantCulture);
        }

        return base.ConvertFromPayloadValue(value, edmTypeReference);
    }
}
```

***2. Set converter to model***

``` csharp
model.SetPayloadValueConverter(new DateTimeOffsetCustomFormatPrimitivePayloadValueConverter());
```

Then `DateTimeOffset` can be serialized to `Thu, 12 Apr 2012 18:43:10 GMT`, and payload like `Thu, 12 Apr 2012 18:43:10 GMT` can be deserialized back to DateTimeOffset.
