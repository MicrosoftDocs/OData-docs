---
title: "Add additional prefer header"
description: ""

author: madansr7
ms.author: madansr7
ms.date: 02/19/2019
ms.topic: article
ms.service: multiple
---

# Create request message with prefer header
**Applies To**: [!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

``` csharp
var requestMessage = new HttpWebRequestMessage(new Uri("https://example.com", UriKind.Absolute));
requestMessage.PreferHeader().ContinueOnError = true;
requestMessage.PreferHeader().MaxPageSize = 1024;
requestMessage.PreferHeader().TrackChanges = true;
```

Then in the http request header, we will have:
`Prefer: odata.continue-on-error,odata.maxpagesize=1024,odata.track-changes`

## Additional information

[odata.track-changes](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part1-protocol/odata-v4.0-errata02-os-part1-protocol-complete.html#_Toc406398239), [odata.maxpagesize](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part1-protocol/odata-v4.0-errata02-os-part1-protocol-complete.html#_Toc406398238), [odata.maxpagesize](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part1-protocol/odata-v4.0-errata02-os-part1-protocol-complete.html#_Toc406398236) are supported to add in prefer header since ODataLib 6.11.0.
