---

title: "2.3 Non-convention model builder"
description: "convention model builder"
category: "2. Defining the model"
---
# 2.3 Non-convention model builder

To build an Edm model using non-convention model builder is to create an `IEdmModel` object by directly call fluent APIs of `ODataModelBuilder`. The developer should take all responsibility to add all Edm types, operations, associations, etc into the data model one by one.
Let's see how to build the Ccustomer-Order* business model by `ODataModelBuilder`.

### CLR Models

Non-convention model builder is based on CLR classes to build the Edm Model. The *Customer-Order* business CLR classes are present in abstract section.

### Enum Type

The following codes are used to add an Enum type **`Color`**:
```C#
var color = builder.EnumType<Color>();
color.Member(Color.Red);
color.Member(Color.Blue);
color.Member(Color.Green);
```

It will generate the below metadata document:
```XML
<EnumType Name="Color">
   <Member Name="Red" Value="0" />
   <Member Name="Blue" Value="1" />
   <Member Name="Green" Value="2" />
</EnumType>
```

### Complex Type

#### Basic Complex Type
The following codes are used to add a complex type **`Address`**:
```C#
var address = builder.ComplexType<Address>();
address.Property(a => a.Country);
address.Property(a => a.City);
```

It will generate the below metadata document:
```XML
<ComplexType Name="Address">
  <Property Name="Country" Type="Edm.String" />
  <Property Name="City" Type="Edm.String" />
</ComplexType>
```

#### Derived Complex type

The following codes are used to add a derived complex type **`SubAddress`**:
```C#
var subAddress = builder.ComplexType<SubAddress>().DerivesFrom<Address>();
subAddress.Property(s => s.Street);
```

It will generate the below metadata document:
```XML
<ComplexType Name="SubAddress" BaseType="WebApiDocNS.Address">
  <Property Name="Street" Type="Edm.String" />
</ComplexType>
```

#### Abstract Complex type

The following codes are used to add an abstract complex type:
```C#
builder.ComplexType<Address>().Abstract();
......
```

It will generate the below metadata document:
```XML
<ComplexType Name="Address" Abstract="true">
  ......
</ComplexType>
```

#### Open Complex type

In order to build an open complex type, you should change the CLR class by adding an `IDictionary<string, object>` property, the property name can be any name. For example:

```C#
public class Address
{
    public string Country { get; set; }
    public string City { get; set; }
    public IDictionary<string, object> Dynamics { get; set; }
}
```

Then you can build the open complex type by call `HasDynamicProperties()`:
```C#
var address = builder.ComplexType<Address>();
address.Property(a => a.Country);
address.Property(a => a.City);
address.HasDynamicProperties(a => a.Dynamics);
```

It will generate the below metadata document:
```XML
<ComplexType Name="Address" OpenType="true">
  <Property Name="Country" Type="Edm.String" />
  <Property Name="City" Type="Edm.String" />
</ComplexType>
```
You can find that the complex type **`Address`** only has two properties, while it has `OpenType="true"` attribute.

### Entity Type

#### Basic Entity Type

The following codes are used to add two entity types **`Customer` & `Order`**:
```C#
var customer = builder.EntityType<Customer>();
customer.HasKey(c => c.CustomerId);
customer.ComplexProperty(c => c.Location);
customer.HasMany(c => c.Orders);

var order = builder.EntityType<Order>();
order.HasKey(o => o.OrderId);
order.Property(o => o.Token);
```

It will generate the below metadata document:
```XML
<EntityType Name="Customer">
    <Key>
        <PropertyRef Name="CustomerId" />
    </Key>
    <Property Name="CustomerId" Type="Edm.Int32" Nullable="false" />
    <Property Name="Location" Type="WebApiDocNS.Address" Nullable="false" />
    <NavigationProperty Name="Orders" Type="Collection(WebApiDocNS.Order)" />
</EntityType>
<EntityType Name="Order">
    <Key>
        <PropertyRef Name="OrderId" />
    </Key>
    <Property Name="OrderId" Type="Edm.Int32" Nullable="false" />
    <Property Name="Token" Type="Edm.Guid" Nullable="false" />
</EntityType>
```

#### Abstract Open type

The following codes are used to add an abstract entity type:
```C#
builder.EntityType<Customer>().Abstract();
......
```

It will generate the below metadata document:
```XML
<EntityType Name="Customer" Abstract="true">
  ......
</EntityType>
```


#### Open Entity type

In order to build an open entity type, you should change the CLR class by adding an `IDictionary<string, object>` property, while the property name can be any name. For example:

```C#
public class Customer
{
    public int CustomerId { get; set; }
    public Address Location { get; set; }
    public IList<Order> Orders { get; set; }
    public IDictionary<string, object> Dynamics { get; set; }
}
```

Then you can build the open entity type as:
```C#
var customer = builder.EntityType<Customer>();
customer.HasKey(c => c.CustomerId);
customer.ComplexProperty(c => c.Location);
customer.HasMany(c => c.Orders);
customer.HasDynamicProperties(c => c.Dynamics);
```

It will generate the below metadata document:
```XML
<EntityType Name="Customer" OpenType="true">
    <Key>
        <PropertyRef Name="CustomerId" />
    </Key>
    <Property Name="CustomerId" Type="Edm.Int32" Nullable="false" />
    <Property Name="Location" Type="WebApiDocNS.Address" Nullable="false" />
    <NavigationProperty Name="Orders" Type="Collection(WebApiDocNS.Order)" />
</EntityType>
```
You can find that the entity type **`Customer`** only has three properties, while it has `OpenType="true"` attribute.

### Entity Container

Non-convention model builder will build the default entity container automatically. However, you should build your own entity sets as:
```C#
builder.EntitySet<Customer>("Customers");
builder.EntitySet<Order>("Orders");
```

It will generate the below metadata document:
```XML
<Schema Namespace="Default" xmlns="http://docs.oasis-open.org/odata/ns/edm">
    <EntityContainer Name="Container">
        <EntitySet Name="Customers" EntityType="WebApiDocNS.Customer">
          <NavigationPropertyBinding Path="Orders" Target="Orders" />
        </EntitySet>
        <EntitySet Name="Orders" EntityType="WebApiDocNS.Order" />
    </EntityContainer>
</Schema>
```

Besides, you can call `Singleton<T>()` to add singleton into entity container.

### Function

It's very simple to build **function (bound & unbound)** in Web API OData. The following codes define two functions. The first is bind to **`Customer`**, the second is unbound.
```C#
var function = customer.Function("BoundFunction").Returns<string>();
function.Parameter<int>("value");
function.Parameter<Address>("address");

function = builder.Function("UnBoundFunction").Returns<int>();
function.Parameter<Color>("color");
function.EntityParameter<Order>("order");
```

It will generate the below metadata document:
```XML
<Function Name="BoundFunction" IsBound="true">
   <Parameter Name="bindingParameter" Type="WebApiDocNS.Customer" />
   <Parameter Name="value" Type="Edm.Int32" Nullable="false" />
   <Parameter Name="address" Type="WebApiDocNS.Address" />
   <ReturnType Type="Edm.String" Unicode="false" />
</Function>
<Function Name="UnBoundFunction">
   <Parameter Name="color" Type="WebApiDocNS.Color" Nullable="false" />
   <Parameter Name="order" Type="WebApiDocNS.Order" />
   <ReturnType Type="Edm.Int32" Nullable="false" />
</Function>
```

Besides, Web API OData will automatically add function imports for all unbound functions. So, the metadata document should has:
```XML
<FunctionImport Name="UnBoundFunction" Function="Default.UnBoundFunction" IncludeInServiceDocument="true" />
```

### Action

Same as function, it's also very simple to build **action (bound & unbound)** in Web API OData. The following codes define two actions. The first is bind to collection of **`Customer`**, the second is unbound.
```C#
var action = customer.Collection.Action("BoundAction");
action.Parameter<int>("value");
action.CollectionParameter<Address>("addresses");

action = builder.Action("UnBoundAction").Returns<int>();
action.Parameter<Color>("color");
action.CollectionEntityParameter<Order>("orders");
```

It will generate the below metadata document:
```XML
<Action Name="BoundAction" IsBound="true">
    <Parameter Name="bindingParameter" Type="Collection(WebApiDocNS.Customer)" />
    <Parameter Name="value" Type="Edm.Int32" Nullable="false" />
    <Parameter Name="addresses" Type="Collection(WebApiDocNS.Address)" />
</Action>
<Action Name="UnBoundAction">
    <Parameter Name="color" Type="WebApiDocNS.Color" Nullable="false" />
    <Parameter Name="orders" Type="Collection(WebApiDocNS.Order)" />
    <ReturnType Type="Edm.Int32" Nullable="false" />
</Action>
```

Same as function, Web API OData will automatically add action imports for all unbound actions. So, the metadata document should has:
```XML
 <ActionImport Name="UnBoundAction" Action="Default.UnBoundAction" />
```

### Summary

Let's put all codes together:
```C#
public static IEdmModel GetEdmModel()
{
    var builder = new ODataModelBuilder();

    // enum type
    var color = builder.EnumType<Color>();
    color.Member(Color.Red);
    color.Member(Color.Blue);
    color.Member(Color.Green);

    // complex type
    // var address = builder.ComplexType<Address>().Abstract();
    var address = builder.ComplexType<Address>();
    address.Property(a => a.Country);
    address.Property(a => a.City);
    // address.HasDynamicProperties(a => a.Dynamics);

    var subAddress = builder.ComplexType<SubAddress>().DerivesFrom<Address>();
    subAddress.Property(s => s.Street);

    // entity type
    // var customer = builder.EntityType<Customer>().Abstract();
    var customer = builder.EntityType<Customer>();
    customer.HasKey(c => c.CustomerId);
    customer.ComplexProperty(c => c.Location);
    customer.HasMany(c => c.Orders);
    // customer.HasDynamicProperties(c => c.Dynamics);

    var order = builder.EntityType<Order>();
    order.HasKey(o => o.OrderId);
    order.Property(o => o.Token);

    // entity set
    builder.EntitySet<Customer>("Customers");
    builder.EntitySet<Order>("Orders");

    // function
    var function = customer.Function("BoundFunction").Returns<string>();
    function.Parameter<int>("value");
    function.Parameter<Address>("address");

    function = builder.Function("UnBoundFunction").Returns<int>();
    function.Parameter<Color>("color");
    function.EntityParameter<Order>("order");

    // action
    var action = customer.Collection.Action("BoundAction");
    action.Parameter<int>("value");
    action.CollectionParameter<Address>("addresses");

    action = builder.Action("UnBoundAction").Returns<int>();
    action.Parameter<Color>("color");
    action.CollectionEntityParameter<Order>("orders");

    return builder.GetEdmModel();
}
```

And the final XML will be:
```XML
<?xml version="1.0" encoding="utf-8"?>
<edmx:Edmx Version="4.0" xmlns:edmx="http://docs.oasis-open.org/odata/ns/edmx">
  <edmx:DataServices>
    <Schema Namespace="WebApiDocNS" xmlns="http://docs.oasis-open.org/odata/ns/edm">
      <ComplexType Name="Address">
        <Property Name="Country" Type="Edm.String" />
        <Property Name="City" Type="Edm.String" />
      </ComplexType>
      <ComplexType Name="SubAddress" BaseType="WebApiDocNS.Address">
        <Property Name="Street" Type="Edm.String" />
      </ComplexType>
      <EntityType Name="Customer" OpenType="true">
        <Key>
          <PropertyRef Name="CustomerId" />
        </Key>
        <Property Name="CustomerId" Type="Edm.Int32" Nullable="false" />
        <Property Name="Location" Type="WebApiDocNS.Address" Nullable="false" />
        <NavigationProperty Name="Orders" Type="Collection(WebApiDocNS.Order)" />
      </EntityType>
      <EntityType Name="Order">
        <Key>
          <PropertyRef Name="OrderId" />
        </Key>
        <Property Name="OrderId" Type="Edm.Int32" Nullable="false" />
        <Property Name="Token" Type="Edm.Guid" Nullable="false" />
      </EntityType>
      <EnumType Name="Color">
        <Member Name="Red" Value="0" />
        <Member Name="Blue" Value="1" />
        <Member Name="Green" Value="2" />
      </EnumType>
    </Schema>
    <Schema Namespace="Default" xmlns="http://docs.oasis-open.org/odata/ns/edm">
      <Function Name="BoundFunction" IsBound="true">
        <Parameter Name="bindingParameter" Type="WebApiDocNS.Customer" />
        <Parameter Name="value" Type="Edm.Int32" Nullable="false" />
        <Parameter Name="address" Type="WebApiDocNS.Address" />
        <ReturnType Type="Edm.String" Unicode="false" />
      </Function>
      <Function Name="UnBoundFunction">
        <Parameter Name="color" Type="WebApiDocNS.Color" Nullable="false" />
        <Parameter Name="order" Type="WebApiDocNS.Order" />
        <ReturnType Type="Edm.Int32" Nullable="false" />
      </Function>
      <Action Name="BoundAction" IsBound="true">
        <Parameter Name="bindingParameter" Type="Collection(WebApiDocNS.Customer)" />
        <Parameter Name="value" Type="Edm.Int32" Nullable="false" />
        <Parameter Name="addresses" Type="Collection(WebApiDocNS.Address)" />
      </Action>
      <Action Name="UnBoundAction">
        <Parameter Name="color" Type="WebApiDocNS.Color" Nullable="false" />
        <Parameter Name="orders" Type="Collection(WebApiDocNS.Order)" />
        <ReturnType Type="Edm.Int32" Nullable="false" />
      </Action>
      <EntityContainer Name="Container">
        <EntitySet Name="Customers" EntityType="WebApiDocNS.Customer">
          <NavigationPropertyBinding Path="Orders" Target="Orders" />
        </EntitySet>
        <EntitySet Name="Orders" EntityType="WebApiDocNS.Order" />
        <FunctionImport Name="UnBoundFunction" Function="Default.UnBoundFunction" IncludeInServiceDocument="true" />
        <ActionImport Name="UnBoundAction" Action="Default.UnBoundAction" />
      </EntityContainer>
    </Schema>
  </edmx:DataServices>
</edmx:Edmx>
```
