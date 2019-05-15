---
title : "4.6 Function parameter support"

ms.date: 03/25/2015
---
# Function parameter support

Since [Web API OData V5.5-beta](https://www.nuget.org/packages/Microsoft.AspNet.OData/5.5.0-beta), it supports the following types as function parameter:

1. Primitive
2. Enum
3. Complex
4. Entity
5. Entity Reference
6. Collection of above

Let's see how to build and use the above types in function.

### CLR Model

First of all, we create the following CLR classes as our model:

```C#
public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class SubCustomer : Customer
{
    public double Price { get; set; }
}

public class Address
{
    public string City { get; set; }
}

public class SubAddress : Address
{
    public string Street { get; set; }
}

public enum Color
{
    Red,
    Blue,
    Green
}
```


### Build Edm Model

Now, we can build the Edm Model as:
```C#
private static IEdmModel GetEdmModel()
{
    var builder = new ODataConventionModelBuilder();
    builder.EntitySet<Customer>("Customers");
    builder.ComplexType<Address>();
    builder.EnumType<Color>();

    BuildFunction(builder);
    return builder.GetEdmModel();
}
```

where, *BuildFunction()* is a helper function in which functions can be built.

### Primitive and Collection of Primitive parameter

#### Configuration
In *BuildFunction()*, we can configure a function with `Primitive` and collection of `Primitive` parameters:
```C#
var function = builder.EntityType<Customer>().Collection.Function("PrimtiveFunction").Returns<string>();
function.Parameter<int>("p1");
function.Parameter<int?>("p2");
function.CollectionParameter<int>("p3");
```

#### Routing
In the `CustomersController`, add the following method:
```C#
[HttpGet]
public string PrimtiveFunction(int p1, int? p2, [FromODataUri]IEnumerable<int> p3)
{
   ...
}
```

#### Request Samples
We can invoke the function as:
```C#
~/odata/Customers/Default.PrimitiveFunction(p1=1,p2=9,p3=[1,3,5,7,9])
~/odata/Customers/Default.PrimitiveFunction(p1=2,p2=null,p3=[1,3,5,7,9])
~/odata/Customers/Default.PrimitiveFunction(p1=3,p2=null,p3=@p)?@p=[2,4,6,8]
```

### Enum and Collection of Enum parameter

#### Configuration
In *BuildFunction()*, we can configure a function with `Enum` and collection of `Enum` parameters:
```C#
var function = builder.EntityType<Customer>().Collection.Function("EnumFunction").Returns<string>();
function.Parameter<Color>("e1");
function.Parameter<Color?>("e2"); // nullable
function.CollectionParameter<Color?>("e3"); // Collection of nullable
```

#### Routing
In the `CustomersController`, add the following method :
```C#
[HttpGet]
public string EnumFunction(Color e1, Color? e2, [FromODataUri]IEnumerable<Color?> e3)
{
  ...
}
```

#### Request Samples
We can invoke the Enum function as:
```C#
~/odata/Customers/Default.EnumFunction(e1=NS.Color'Red',e2=NS.Color'Green',e3=['Red', null, 'Blue'])
~/odata/Customers/Default.EnumFunction(e1=NS.Color'Blue',e2=null,e3=['Red', null, 'Blue'])
~/odata/Customers/Default.EnumFunction(e1=NS.Color'Blue',e2=null,e3=@p)?@p=['Red', null, 'Blue']
```

### Complex and Collection of Complex parameter

#### Configuration
In *BuildFunction()*, we can configure a function with `Complex` and collection of `Complex` parameters:
```C#
var function = builder.EntityType<Customer>().Collection.Function("ComplexFunction").Returns<string>();
function.Parameter<Address>("c1");
function.CollectionParameter<Address>("c2");
```

#### Routing
In the `CustomersController`, add the following method :
```C#
[HttpGet]
public string ComplexFunction([FromODataUri]Address c1, [FromODataUri]IEnumerable<Address> c2)
{
  ...
}
```

#### Request Samples
We can invoke the complex function as:
```C#
~/odata/Customers/Default.ComplexFunction(c1=@x,c2=@y)?@x={\"@odata.type\":\"%23NS.Address\",\"City\":\"Redmond\"}&@y=[{\"@odata.type\":\"%23NS.Address\",\"City\":\"Redmond\"},{\"@odata.type\":\"%23NS.SubAddress\",\"City\":\"Shanghai\", \"Street\":\"Zi Xing Rd\"}]
~/odata/Customers/Default.ComplexFunction(c1={\"@odata.type\":\"%23NS.Address\",\"City\":\"Redmond\"},c2=[{\"@odata.type\":\"%23NS.Address\",\"City\":\"Redmond\"},{\"@odata.type\":\"%23NS.SubAddress\",\"City\":\"Shanghai\", \"Street\":\"Zi Xing Rd\"}])
~/odata/Customers/Default.ComplexFunction(c1=null,c2=@p)?@p=[null,{\"@odata.type\":\"%23NS.SubAddress\",\"City\":\"Shanghai\", \"Street\":\"Zi Xing Rd\"}]
```

### Entity and Collection of Entity parameter

#### Configuration
In *BuildFunction()*, we can configure a function with `Entity` and collection of `Entity` parameters:
```C#
var function = builder.EntityType<Customer>().Collection.Function("EntityFunction").Returns<string>();
function.EntityParameter<Customer>("a1");
function.CollectionEntityParameter<Customer>("a2"); 
```
It's better to call `EntityParameter<T>` and `CollectionEntityParameter<T>` to define entity and collection of entity parameter.

#### Routing
In the `CustomersController`, add the following method :
```C#
[HttpGet]
public string EntityFunction([FromODataUri]Customer a1, [FromODataUri]IEnumerable<Customer> a2)
{
  ...
}
```

#### Request Samples
We can invoke the entity function as:
```C#
~/odata/Customers/Default.EntityFunction(a1=@x,a2=@y)?@x={\"@odata.type\":\"%23NS.Customer\",\"Id\":1,\"Name\":\"John\"}&@y={\"value\":[{\"@odata.type\":\"%23NS.Customer\",\"Id\":2, \"Name\":\"Mike\"},{\"@odata.type\":\"%23NS.SubCustomer\",\"Id\":3,\"Name\":\"Tony\", \"Price\":9.9}]}
~/odata/Customers/Default.EntityFunction(a1=@x,a2=@y)?@x=null&@y={\"value\":[]}
```
However, only parameter alias is supported for entity.

### Entity Reference and collection of Entity Reference parameter
In fact, we can't build a function with entity reference as parameter. However, we can call the function with entity parameter using entity reference value. So, without any change for the `EntityFunction`, we can call as:
```C#
~/odata/Customers/Default.EntityFunction(a1=@x,a2=@y)?@x={\"@odata.id\":\"https://localhost/odata/Customers(2)\"}&@y={\"value\":[{\"@odata.id\":\"https://localhost/odata/Customers(2)\"},{\"@odata.id\":\"https://localhost/odata/Customers(3)\"}]}
```

### FromODataUri

'[FromODataUri]' is mandatory for complex, entity and all collection. However, it is optional for Primitive & Enum. But for string primitive type, the value will contain single quotes without '[FromODataUri]'.

Thanks.

For un-typed scenario, please refer to [untyped page](https://odata.github.io/WebApi/Function-Action-Parameter-In-Untyped-Scenario/).
