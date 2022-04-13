---
title: "Centralized control for OData Simplified Options"
description: "From ODataV7.0, we add a centralized control class for them, which is ODataSimplifiedOptions."
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Enable Simplified options
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

In previous OData library, the control of writing/reading/parsing key-as-segment uri path is separated in ODataMessageWriterSettings/ODataMessageReaderSettings/ODataUriParser. The same as writing/reading OData annotation without prefix. From ODataV7.0, we add a centralized control class for them, which is ODataSimplifiedOptions.

The following API can be used in ODataSimplifiedOptions:

```C#
public class ODataSimplifiedOptions
{
    ...
    public bool EnableParsingKeyAsSegmentUrl { get; set; }
    public bool EnableReadingKeyAsSegment { get; set; }
    public bool EnableReadingODataAnnotationWithoutPrefix { get; set; }
    public bool EnableWritingKeyAsSegment { get; set; }
    public bool EnableWritingODataAnnotationWithoutPrefix { get; set; }
    ...
}
```

ODataSimplifiedOptions is registered by DI with singleton ServiceLifetime (Please refer to  [Dependency Injection support](../core libraries/2016-08-30-01-05-di-support)).