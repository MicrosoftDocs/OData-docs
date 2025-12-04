---
title: " Attribute Routing"
description: Learn how to use and gain control over attribute routing.
author: madansr7
ms.date: 7/1/2019
---
# Attribute Routing
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Same as Web API, Web API OData supports a new type of routing called **attribute routing**. It uses two *Attributes* to find **controller** and **action**. One is `ODataPrefixAttribute`, the other is `ODataRouteAttribute`.

You can use **attribute routing** to define more complex routes and put more control over the routing. Most important, it can extend the coverage of convention routing. For example, you can easily use attribute routing to route the following Uri:

```C#
~/odata/Customers(1)/Orders/Price
```

In Web API OData, **attribute routing** is combined with **convention routing** by default.

### Enabling Attribute Routing

`ODataRoutingConventions` provides two methods to register routing conventions:

```C#
public static IList<IODataRoutingConvention> CreateDefaultWithAttributeRouting(HttpConfiguration configuration, IEdmModel model)

public static IList<IODataRoutingConvention> CreateDefault()
```

As the name implies, the first one creates a mutable list of the default OData routing conventions with attribute routing enabled, while the second one only includes convention routing.

In fact, when you call the basic `MapODataServiceRoute`, it enables the attribute routing by default as:
```C#
public static ODataRoute MapODataServiceRoute(this HttpConfiguration configuration, string routeName, string routePrefix, IEdmModel model, ODataBatchHandler batchHandler)
{
    return MapODataServiceRoute(configuration, routeName, routePrefix, model, new DefaultODataPathHandler(),
        ODataRoutingConventions.CreateDefaultWithAttributeRouting(configuration, model), batchHandler);
}
```

However, you can call other version of `MapODataServiceRoute` to customize your own routing conventions. For example:

```C#
public static ODataRoute MapODataServiceRoute(this HttpConfiguration configuration, string routeName, string routePrefix, IEdmModel model, IODataPathHandler pathHandler, IEnumerable<IODataRoutingConvention> routingConventions)
```

### ODataRouteAttribute

`ODataRouteAttribute` is an attribute that can, and only can be placed on an action of an OData controller to specify the OData URLs that the action handles.

Here is an example of an action defined using an `ODataRouteAttribute`:

```C#
public class MyController : ODataController
{
    [HttpGet]
    [ODataRoute("Customers({id})/Address/City")]
    public string GetCityOfACustomer([FromODataUri]int id)
    {
        ......
    }
}
```

With this attribute, Web API OData tries to match the request Uri with `Customers({id})/Address/City` routing template to  `GetCityOfACustomer()` function in `MyController`. For example, the following request Uri will invoke `GetCityOfACustomer`:

```C#
~/odata/Customers(1)/Address/City
~/odata/Customers(2)/Address/City
~/odata/Customers(301)/Address/City
```

For the above request Uri, `id` in the function will have `1`, `2` and `301` value respectively.

However, for the following request Uri, it can't match to `GetCityOfACustomer()`:
```C#
~/odata/Customers
~/odata/Customers(1)/Address
```

Web API OData supports to put multiple `ODataRouteAttribute` on the same OData action. For example, 

```C#
public class MyController : ODataController
{
    [HttpGet]
    [ODataRoute("Customers({id})/Address/City")]
    [ODataRoute("Products({id})/Address/City")]
    public string GetCityOfACustomer([FromODataUri]int id)
    {
        ......
    }
}
```

### ODataRoutePrefixAttribute

`ODataRoutePrefixAttribute` is an attribute that can, and only can be placed on an *OData controller* to specify the prefix that will be used for all actions of that controller.

`ODataRoutePrefixAttribute` is used to reduce the routing template in `ODataRouteAttribute` if all routing template in the controller start with the same prefix. For example:

```C#
public class MyController : ODataController
{
    [ODataRoute("Customers({id})/Address")]
    public IHttpActionResult GetAddress(int id)
    {
        ......
    }

    [ODataRoute("Customers({id})/Address/City")]
    public IHttpActionResult GetCity(int id)
    {
        ......
    }

    [ODataRoute("Customers({id})/Order")]
    public IHttpActionResult GetOrder(int id)
    {
        ......
    }
}
```

Then you can use `ODataRoutePrefixAttribute` attribute on the controller to set a common prefix.

```C#
[ODataRoutePrefix("Customers({id})")]
public class MyController : ODataController
{
    [ODataRoute("Address")]
    public IHttpActionResult GetAddress(int id)
    {
        ......
    }

    [ODataRoute("Address/City")]
    public IHttpActionResult GetCity(int id)
    {
        ......
    }

    [ODataRoute("/Order")]
    public IHttpActionResult GetOrder(int id)
    {
        ......
    }
}
```

Now, Web API OData supports to put multiple `ODataRoutePrefixAttribute` on the same OData controller. For example, 

```C#
[ODataRoutePrefix("Customers({key})")]  
[ODataRoutePrefix("VipCustomer")]  
public class ODataControllerWithMultiplePrefixes : ODataController  
{
    ......  
}
```

### Route template

The route template is the route combined with `ODataRoutePrefixAttribute` and `ODataRouteAttribute`. So, for the following example:

```C#
[ODataRoutePrefix("Customers")]  
public class MyController : ODataController  
{
    [ODataRoute("({id})/Address")]
    public IHttpActionResult GetAddress(int id)
    {
        ......
    }
}
```

The `GetAddress` matches to `Customers({id})/Address` route template. It's called key template because there's a template `{id}`. So far in Web API OData, it supports two kind of templates:

1. key template, for example: 

```C#
[ODataRoute("({id})/Address")]
[ODataRoute("Clients({clientId})/MyOrders({orderId})/OrderLines")]
```    
   
2. function parameter template, for example: 

```C#
[ODataRoute("Customers({id})/NS.MyFunction(city={city})")]
[ODataRoute("Customers/Default.BoundFunction(SimpleEnum={p1})")]
```    

Web API OData team also works to add the third template, that is the dynamic property template. It's planed to ship in next release.

You can refer to [this blog](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) for attribute routing in Web API 2.
