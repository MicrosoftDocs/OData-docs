---
title:  "OData Web API 8 Overview"
description: A high-level overview of OData Web API, its benefits and common use cases.
date:   2022-09-05
ms.date: 9/5/2022
author: habbes
ms.author: clhabins
---

# Overview
**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

OData Web API is a .NET library for building REST API services that conform to the OData protocol. The OData protocol defines best practices for consistent and strongly-typed REST APIs by specifying the format of requests and responses, type definition, and service description as well as features like filtering, querying related data, pagination, etc. OData Web API library is a set of services that extend [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core) to provide OData-based routing, request and response serialization, query-handling, and more. To learn more about OData, visit [the OData docs](/odata/overview).

Here are some of the features provided by the library:
- Exposes a metadata endpoint that returns a document describing the API service' model, the data types it defines and the endpoints and capabilities it exposes. This document makes it easier for clients to consume the service. It can also be used by code generation tools to automatically generate service proxy on the clients to facilitate interaction with the service (e.g. [OData CLI](/odata/odatacli/getting-started), [OData Connected Service](/odata/connectedservice/getting-started)), allowing the user to focus on business logic.
- Convention-based routing: Requests are routed to controllers and actions based on naming and URL conventions. Fine-grained control over routing can be achieved using ASP.NET Core route template attributes - `Route`, `HttpGet`, `HttpPost`, `HttpPut`, `HttpPatch`, `HttpDelete`, and `HttpHead` - also referred to as attribute routing.
- Handles serialization and deserialization of requests and responses
- Validation of requests and responses based on the types defined in the OData model
- Support for batch requests, which allow you to execute multiple operations in a single API request
- Handles [OData query options](/odata/concepts/queryoptions-overview) out of the box by transforming queries to your LINQ-enabled data source:
  - `$filter` allows you to filter results based on specific conditions: `/Products?$filter=Price lt 1000 and Category eq 'Electronics'` will only return electronic products whose price is less than 1,000.
  - `$select` allows you to limit the fields that are included in the response: `/Products?$select=Name,Category,DateCreated` will only include the `Name`, `Category`, `DateCreated` fields in the response
  - `$expand` allows you to join and include related data in the response: `/OrderItems?$expand=Product` will return order items and include the related product in the response
  - `$skip` and `$top` allow you to limit the number of records in the response and can be used for client-driven pagination: `/Products$top=10&$skip=20&$orderby=Name` returns the third page of 10 products sorted by name.
  - `$apply` allows you to apply aggregations: `/Orders?$apply=groupby(Product/Name, aggregate($count as Count))` returns the number of orders per product name.


## Why choose OData Web API:

Here are some of the benefits and common use cases for using OData Web API:
- Creating a standards-compliant Web API with support for filtering, selecting, expanding among other capabilities.
- Interoperability with OpenAPI and Swagger
- Type-safety
- Ability to generate client code from OData model description
- Expose a REST API layer to your data source for analytics functions.
- Add advanced querying capabilities via OData query options to your REST API even when it's not based on OData.

## NuGet packages

The library is shipped on NuGet under the package name: `Microsoft.AspNetCore.OData`.

We also ship a separate library for building OData models from .NET model classes. This is available under the package name: `Microsoft.OData.ModelBuilder`.

## Choosing the right version

There are two major versions of OData Web API under active maintenance and support:
- OData Web API 8: `Microsoft.AspNetCore.OData` 8.x
- OData Web API 7: `Microsoft.AspNetCore.OData` 7.x and `Microsoft.AspNet.OData` 7.x

For new development of OData services on .NET, OData Web API 8 is recommended. However, OData Web API 7 is actively maintained. There are also features in OData Web API 7 that have not been ported to OData Web API 8, e.g., [automatic support for deeply nested paths](/odata/webapi/automatic-nested-paths-with-enable-nested-paths).

OData Web API 8 leverages ASP.NET Core features and is only available for .NET Core 3.1 and newer versions of .NET. It does not support .NET Framework. OData Web API 7 supports ASP.NET classic and .NET Framework through the `Microsoft.AspNet.OData` package.