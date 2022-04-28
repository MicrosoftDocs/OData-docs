---
title: " Introduction Routing"
description: "Routing Conventions"
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
---
# Introduction Routing
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

In Web API, **Routing** is how it matches a request URI to an action in a controller. The **Routing** of Web API OData is derived from Web API **Routing** and adds more extensions.
In Web API OData, an *OData controller* (not *API controller*) serves as the request handler to handle HTTP requests, while the public methods (called action methods) in the controller are invoked to execute the business logic.
So, when the client issues a request to an OData service, the Web API OData framework will map the request to an action in the *OData controller*. Such mapping is based on pre-registered **Routes** in global configuration.

### Register the Web API OData Routes

In Web API, developers can use the following code to register a Web API route into the routing table:

```C#
configuration.routes.MapHttpRoute(
    name: "myRoute",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { id = ... }
);
```

Web API OData re-uses the Web API routing table to register the Web OData Routes. However, it provides its own extension method called `MapODataServiceRoute` to register the OData route. `MapODataServiceRoute` has many versions, 
here's the basic usage:

```C#
HttpConfiguration configuration = new HttpConfiguration();
configuration.MapODataServiceRoute(routeName:"myRoute", routePrefix:"odata", model: GetEdmModel()));
```

The code above registers an OData route named "myRoute", uses "odata" as prefix and calls `GetEdmModel()` to set up the Edm model.

After registering the Web OData routes, we define an OData route template in the routing table. The route template has the following syntax:
```C#
~/odata/~
```

Now, the Web API OData framework can handle the HTTP request. It tries to match the request Uri against one of the route templates in the routing table. Basically, the following URIs match the OData route:

```C#
~/odata/Customers
~/odata/Customers(1)
~/odata/Customers/Default.MyFunction()
```

Where, **Customers** is the entity set name.

However, the following URI does not match the OData route, because it doesn't match "odata"  prefix segment:
```C#
~/myodata/Customers(1)
```

### Routing Convention

Once the OData route is found, Web API OData will parse the request Uri to get the path segments. Web API OData first uses the **[ODatalib](https://www.nuget.org/packages/Microsoft.OData.Core/)** to parse the request Uri to get the ODL path segments, then convert the ODL path segments to Web API OData path segments.
Once the Uri Parse is finished, Web API OData will try to find the corresponding OData controller and action. The process used to find the controller and action are the main part of the **Routing Convention**.
Basically, there are two parts of the **Routing Convention**:

1. Convention Routing

   It is also called built-in routing conventions. It uses a set of pre-defined rules to find the **controller** and **action**.
   
2. Attribute Routing

   It uses two Attributes to find the **controller** and **action**. One is `ODataRoutePrefixAttribute`, the other is `ODataRouteAttribute`.

