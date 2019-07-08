---
title: "Add additional prefer header webapi v7"
description: ""
author: madansr7
ms.author: madansr7
ms.date: 7/1/2019
ms.topic: article
 
---
# Add additional prefer header in ODL
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

[odata.track-changes](https://docs.oasis-open.org/odata/odata/v4.0/errata03/os/complete/part1-protocol/odata-v4.0-errata03-os-part1-protocol-complete.html#_Preference_odata.track-changes),

[odata.maxpagesize](https://docs.oasis-open.org/odata/odata/v4.0/errata03/os/complete/part1-protocol/odata-v4.0-errata03-os-part1-protocol-complete.html#_The_odata.maxpagesize_Preference),

[odata.ContinueOnError](https://docs.oasis-open.org/odata/odata/v4.0/errata03/os/complete/part1-protocol/odata-v4.0-errata03-os-part1-protocol-complete.html#_Preference_odata.continue-on-error) are supported to add in prefer header since ODataLib 6.11.0.

## Create request message with prefer header

```C#
var requestMessage = new HttpWebRequestMessage(new Uri("https://example.com", UriKind.Absolute));
requestMessage.PreferHeader().ContinueOnError = true;
requestMessage.PreferHeader().MaxPageSize = 1024;
requestMessage.PreferHeader().TrackChanges = true;
```

Then in the http request header, we will have:
`Prefer: odata.continue-on-error,odata.maxpagesize=1024,odata.track-changes`
