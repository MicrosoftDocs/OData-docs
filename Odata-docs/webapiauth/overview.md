---
title : "Overview"
description: "This articles gives an overview of what the OData WebApi Authorization library is"

author: habbes
ms.author: clhabins
ms.date: 8/23/2020
---
# Overview of OData WebApi Authorization
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)]


The OData WebApi Authorization library allows you to apply permission restrictions defined in your OData model to your WebApi endpoints. The library is open-source and available on [GitHub](https://github.com/OData/WebApiAuthorization).

WebApi Authorization is built on top of the ASP.NET Core's built-in [Authentication and Authorization](https://docs.microsoft.com/aspnet/core/security) systems. This means it can be integrated with your existing authentication setup and can work along side existing authorization policies that may be implemented on your controller actions.

WebApi Authorization introduces a middleware that reads the OData EdmModel to determine which permissions should be applied to the each OData endpoint. It compares these to the permission scopes owned by the currently authenticated user to determine whether or not to authorize the request. This does not require you to add use the `Authorize` attribute or configure your own authorization policies.

Check out the [Getting Started](./getting-started) guide for a tutorial that takes you through using the library to add authorization to your WebApi application.

## Supported OData and ASP.NET libraries

WebApi Authorization currently only supports OData WebApi 7.x, AspNet.Core 3.1 with endpoint routing.