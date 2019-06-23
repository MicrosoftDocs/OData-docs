---
title: "Query options overview"
description: "Query options overview"
author: madansr7
ms.author: madansr7
ms.date: 02/19/2019
ms.topic: article
 
---
# Query options overview

Querying entails two types of operations

1. Making a request using `GET` Http verb.

    **Talk about examples in section 4.3 - 4.15 of URL conventions**

2. Manipulating response data using query options.

A query option is a set of query string parameters applied to a resource that can help control the amount of data being returned for the resource in the URL. A query option is basically requesting that a service perform a set of filtering or other transformations to its data, then return the results. A query option can be applied to every verb except `DELETE` operations.

[!Note]

A request to a resource using Http verbs `GET`, `PATCH` or `PUT` follow these conventions:

- Resource paths identifying a single entity, a complex type instance, a collection of entities, or a collection of complex type instances allow $compute, $expand and $select.
- Resource paths identifying a collection allow $filter, $search, $count, $orderby, $skip, and $top.
- Resource paths ending in /$count allow $filter and $search.
- Resource paths not ending in /$count or /$batch allow $format.
- Query options for a `POST` operations may differ due to the type of object being returned in the response. If a POST returns a single entity vs collection of entities, it will impact the query options that are applicable to the request.
