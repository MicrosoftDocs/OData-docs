---
title:  "Pagination in ASP.NET Core OData 8"
description: Pagination in ASP.NET Core OData 8.
date:   2023-01-10
ms.date: 1/10/2023
author: habbes
ms.author: clhabins
---

# Pagination in ASP.NET Core OData 8
**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

Loading large datasets can be slow. Services and clients often rely on pagination to load data in chunks or pages to improve the response times and user experience. ASP.NET Core OData provides support for client-drive and server-driven paging models.

In client-driven paging, the client uses the `$top` and `$skip` query options to request a specific page of content from the server. In server-driven paging, the server limits the number of items per response page and includes a **next link** in the response that the client can use to request the next page.
