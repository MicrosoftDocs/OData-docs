---
title : "4.7 Action parameter support"

ms.date: 03/30/2015
---
# Action parameter support
**Applies To**:
[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)]
[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Since [Web API OData V5.5-beta](https://www.nuget.org/packages/Microsoft.AspNet.OData/5.5.0-beta), it supports the following types as action parameter:

1. Primitive
2. Enum
3. Complex
4. Entity
5. Collection of above

Let's see how to build and use the above types in action.

### CLR Model

Re-use the CLR models in [function sample](https://odata.github.io/WebApi/Complex-Entity-As-Function-Parameter/).

### Build Edm Model

Same as build Edm Model in [function sample](https://odata.github.io/WebApi/Complex-Entity-As-Function-Parameter/), but change the helper function as *BuildAction()*.

### Primitive and Collection of Primitive parameter

#### Configuration
In *BuildAction()*, we can configure an action with `Primitive` and collection of `Primitive` parameters:
```C#
var action = builder.EntityType<Customer>().Collection.Action("PrimtiveAction");
action.Parameter<int>("p1");
action.Parameter<int?>("p2");
action.CollectionParameter<int>("p3");
```

#### Routing
In the `CustomersController`, add the following method:

```C#
[HttpPost]
public IHttpActionResult PrimitiveAction(ODataActionParameters parameters)
{
   ...
}
```

#### Request Samples
We can invoke the action by issuing a Post on `~/odata/Customers/Default.PrimitiveAction` with the following request body:

```JSON
{
  "p1":7,
  "p2":9,
  "p3":[1,3,5,7,9]
}
```

### Enum and Collection of Enum parameter

#### Configuration
In *BuildAction()*, we can configure an action with `Enum` and collection of `Enum` parameters:
```C#
var action = builder.EntityType<Customer>().Collection.Action("EnumAction");
action.Parameter<Color>("e1");
action.Parameter<Color?>("e2"); // nullable
action.CollectionParameter<Color?>("e3"); // Collection of nullable
```

#### Routing
In the `CustomersController`, add the following method :
```C#
[HttpPost]
public IHttpActionResult EnumAction(ODataActionParameters parameters)
{
  ...
}
```

#### Request Samples
We can invoke the action by issuing a Post on `~/odata/Customers/Default.EnumAction` with the following request body:
```JSON
{
  "e1":"Red",
  "e2":"Green",
  "e3":["Red", null, "Blue"]
}
```

### Complex and Collection of Complex parameter

#### Configuration
In *BuildAction()*, we can configure an action with `Complex` and collection of `Complex` parameters:
```C#
var action = builder.EntityType<Customer>().Collection.Action("ComplexAction").Returns<string>();
action.Parameter<Address>("address");
action.CollectionParameter<Address>("addresses");
```

#### Routing
In the `CustomersController`, add the following method :
```C#
[HttpPost]
public IHttpActionResult ComplexAction(ODataActionParameters parameters)
{
  ...
}
```

#### Request Samples
We can invoke the action by issuing a Post on `~/odata/Customers/Default.ComplexAction` with the following request body:
```JSON
{
  "address":{"City":"Redmond"},
  "addresses":[{"@odata.type":"#NS.Address","City":"Redmond"},{"@odata.type":"#NS.SubAddress","City":"Shanghai","Street":"Zi Xing Rd"}]
}
```

### Entity and Collection of Entity parameter

#### Configuration
In *BuildAction()*, we can configure an action with `Entity` and collection of `Entity` parameters:
```C#
var action = builder.EntityType<Customer>().Collection.Action("EntityAction").Returns<string>();
action.EntityParameter<Customer>("customer");
action.CollectionEntityParameter<Customer>("customers"); 
```
It's better to call `EntityParameter<T>` and `CollectionEntityParameter<T>` to define entity and collection of entity parameter.

#### Routing
In the `CustomersController`, add the following method :
```C#
[HttpPost]
public IHttpActionResult EntityAction(ODataActionParameters parameters)
{
  ...
}
```

#### Request Samples
We can invoke the action by issuing a Post on `~/odata/Customers/Default.EntityAction` with the following request body:

```C#
{
  "customer":{\"@odata.type\":\"#NS.Customer\",\"Id\":1,\"Name\":\"John\"},
  "customers":[
    {\"@odata.type\":\"#NS.Customer\",\"Id\":2, \"Name\":\"Mike\"},
    {\"@odata.type\":\"#NS.SubCustomer\",\"Id\":3,\"Name\":\"Tony\", \"Price\":9.9}
  ]
}
```


### Know issues
1. It doesn't work if "null" value in the collection of entity in the payload. See detail in [#100](https://github.com/OData/odata.net/issues/100).

2. It doesn't work if anything else follows up the collection of entity in the payload. See detail in [#65](https://github.com/OData/odata.net/issues/65)

### Null value

If you invoke an action with a 'null' action parameter value, please don't add the parameter (for example, `"p1":null`) in the payload and leave it un-specified. However, for collection, you should always specify it even the collection is an empty collection (for example, `"p1":[]`).

For un-typed scenario, please refer to [untyped page](https://odata.github.io/WebApi/Function-Action-Parameter-In-Untyped-Scenario/).
