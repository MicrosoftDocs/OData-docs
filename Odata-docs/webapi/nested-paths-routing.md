---
title: "Nested paths routing"
description: "Nested paths routing with [EnableNestedPaths]"
date: 2021-08-09 07:49:00
author: habbes
ms.author: clhabins
ms.date: 8/09/2021
---
# Nested paths routing
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-core-v7.6.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.6.md)]

Since OData WebApi v7.6, you can use the `[EnableNestedPaths]` attribute to handle routing of nested `GET` requests using a single `Get()` controller action, and the query transformations needed to fetch the data requesed in the path will be applied automatically. Visit [Automatic nested paths](/automatic-nested-paths-with-enable-nested-paths) guide to learn more about the feature.