---
title: "Override payload serialization and deserialization in odatalib"
description: ""
author: madansr7
ms.author: madansr7
ms.date: 02/19/2019
ms.topic: article
 
---
# Override primitive serialization
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

Since ODataLib 6.12.0, it supports to customize the payload value converter to override the primitive serialization and deserialization of payload.

### New public API

The new class `ODataPayloadValueConverter` provides a default implementation for value conversion, and also allows developer to override by implemementing `ConvertFromPayloadValue` and `ConvertToPayloadValue`.

```C#
public class Microsoft.OData.ODataPayloadValueConverter {
	public ODataPayloadValueConverter ()

	public virtual object ConvertFromPayloadValue (object value, Microsoft.OData.Edm.IEdmTypeReference edmTypeReference)
	public virtual object ConvertToPayloadValue (object value, Microsoft.OData.Edm.IEdmTypeReference edmTypeReference)
}
```

And in ODataLib 7.0, a custom converter is registered through [DI](/odata/odatalib/di-support).

### Sample

Here we are trying to override the default converter to support the "R" format of date and time.   

***1. Define DataTimeOffset converter***

```C#
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

***2. Register new converter to DI container***

```C#
ContainerBuilderHelper.BuildContainer(
	    builder => builder.AddService<ODataPayloadValueConverter, DateTimeOffsetCustomFormatPrimitivePayloadValueConverter>(ServiceLifetime.Singleton))
```

Please refer [here](/odata/odatalib/di-support) about DI details.

Then `DateTimeOffset` can be serialized to `Thu, 12 Apr 2012 18:43:10 GMT`, and payload like `Thu, 12 Apr 2012 18:43:10 GMT` can be deserialized back to `DateTimeOffset`.
