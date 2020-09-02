---
title: " Build Edm Model Explicitly"
description: "convention model builder"

ms.date: 7/1/2019
---
# Build Edm Model Explicitly
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

As mentioned in previous section, to build Edm model explicitly is to create an `IEdmModel` object directly using **[ODatalib](https://www.nuget.org/packages/Microsoft.OData.Core/)** API. The Edm model built by this method is called **type-less model**, or **week type model**, or just **un-typed model**.

Let's see how to build the *Customer-Order* business model.

### Enum Type

We can use `EdmEnumType` to define an Enum type **`Color`** as:
```C#
EdmEnumType color = new EdmEnumType("WebApiDocNS", "Color");
color.AddMember(new EdmEnumMember(color, "Red", new EdmIntegerConstant(0)));
color.AddMember(new EdmEnumMember(color, "Blue", new EdmIntegerConstant(1)));
color.AddMember(new EdmEnumMember(color, "Green", new EdmIntegerConstant(2)));
model.AddElement(color);
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

We can use `EdmComplexType` to define a complex type **`Address`** as:

```C#
EdmComplexType address = new EdmComplexType("WebApiDocNS", "Address");
address.AddStructuralProperty("Country", EdmPrimitiveTypeKind.String);
address.AddStructuralProperty("City", EdmPrimitiveTypeKind.String);
model.AddElement(address);
```

It will generate the below metadata document:

```XML
<ComplexType Name="Address">
  <Property Name="Country" Type="Edm.String" />
  <Property Name="City" Type="Edm.String" />
</ComplexType>
```

#### Derived Complex type

We can set the base type in construct to define a derived complex type **`SubAddress`** as:

```C#
EdmComplexType subAddress = new EdmComplexType("WebApiDocNS", "SubAddress", address);
subAddress.AddStructuralProperty("Street", EdmPrimitiveTypeKind.String);
model.AddElement(subAddress);
```

It will generate the below metadata document:

```XML
<ComplexType Name="SubAddress" BaseType="WebApiDocNS.Address">
  <Property Name="Street" Type="Edm.String" />
</ComplexType>
```

#### Other Complex Types

We can call the following construct to set a complex type whether it is abstract or open.

```C#
public EdmComplexType(string namespaceName, string name, IEdmComplexType baseType, bool isAbstract, bool isOpen);
```

For example:

```C#
EdmComplexType address = new EdmComplexType("WebApiDocNS", "Address", baseType: null, isAbstract: true, isOpen: true);
model.AddElement(address);
```

It will generate the below metadata document:

```XML
<ComplexType Name="Address" Abstract="true" OpenType="true" />
```

### Entity Type

#### Basic Entity Type

We can use `EdmEntityType` to define two entity types **`Customer` & `Order`** as:

```C#
EdmEntityType customer = new EdmEntityType("WebApiDocNS", "Customer");
customer.AddKeys(customer.AddStructuralProperty("CustomerId", EdmPrimitiveTypeKind.Int32));
customer.AddStructuralProperty("Location", new EdmComplexTypeReference(address, isNullable: true));
model.AddElement(customer);

EdmEntityType order = new EdmEntityType("WebApiDocNS", "Order");
order.AddKeys(order.AddStructuralProperty("OrderId", EdmPrimitiveTypeKind.Int32));
order.AddStructuralProperty("Token", EdmPrimitiveTypeKind.Guid);
model.AddElement(order);
```

It will generate the below metadata document:

```XML
<EntityType Name="Customer">
  <Key>
    <PropertyRef Name="CustomerId" />
  </Key>
  <Property Name="CustomerId" Type="Edm.Int32" />
  <Property Name="Location" Type="WebApiDocNS.Address" />
</EntityType>
<EntityType Name="Order">
  <Key>
    <PropertyRef Name="OrderId" />
  </Key>
  <Property Name="OrderId" Type="Edm.Int32" />
  <Property Name="Token" Type="Edm.Guid" />
</EntityType>
```

#### Derived Entity type

We can set the base type in construct to define a derived entity type **`VipCustomer`** as:

```C#
EdmEntityType vipCustomer = new EdmEntityType("WebApiDocNS", "VipCustomer", customer);
vipCustomer.AddStructuralProperty("FavoriteColor", new EdmEnumTypeReference(color, isNullable: false));
model.AddElement(vipCustomer);
```

It will generate the below metadata document:

```XML
<EntityType Name="VipCustomer" BaseType="WebApiDocNS.Customer">
    <Property Name="FavoriteColor" Type="WebApiDocNS.Color" Nullable="false" />
</EntityType>
```

#### Other Entity Types

We can call the following construct to set an entity type whether it is abstract or open.

```C#
public EdmEntityType(string namespaceName, string name, IEdmEntityType baseType, bool isAbstract, bool isOpen);
```

For example:

```C#
EdmEntityType customer = new EdmEntityType("WebApiDocNS", "Customer", baseType: null, isAbstract: true, isOpen: true);
model.AddElement(customer);
```

It will generate the below metadata document:

```XML
<EntityType Name="Customer" Abstract="true" OpenType="true" />
```

### Default Entity Container

Each model MUST define at most one entity container, in which entity sets, singletons and operation imports are defined. For example:

```C#
EdmEntityContainer container = new EdmEntityContainer("WebApiDocNS", "Container");
EdmEntitySet customers = container.AddEntitySet("Customers", customer);
EdmEntitySet orders = container.AddEntitySet("Orders", order);
model.AddElement(container);
```

It will generate the below metadata document:

```XML
<EntityContainer Name="Container">
   <EntitySet Name="Customers" EntityType="WebApiDocNS.Customer" />
   <EntitySet Name="Orders" EntityType="WebApiDocNS.Order" />
</EntityContainer>
```

### Singleton

We can also add singleton into entity container. For example:

```C#
EdmSingleton mary = container.AddSingleton("Mary", customer);
```

It will generate the below metadata document:

```XML
<Singleton Name="Mary" Type="WebApiDocNS.Customer" />
```

### Navigation Property

Now, we can add navigation property to **Customer**. For example:

```C#
EdmNavigationProperty ordersNavProp = customer.AddUnidirectionalNavigation(
    new EdmNavigationPropertyInfo
    {
        Name = "Orders",
        TargetMultiplicity = EdmMultiplicity.Many,
        Target = order
    });
customers.AddNavigationTarget(ordersNavProp, orders);
```

It will generate the below metadata document:

First, it will add a new item in the entity type as:

```XML
<EntityType Name="Customer">
    ...
    <NavigationProperty Name="Orders" Type="Collection(WebApiDocNS.Order)" />
</EntityType>
```

Second, it will add a new item in the entity container for **Customers** entity set as:

```XML
<EntitySet Name="Customers" EntityType="WebApiDocNS.Customer">
  <NavigationPropertyBinding Path="Orders" Target="Orders" />
</EntitySet>
```

### Function

Let's define two functions. One is bound, the other is unbound as:

```C#
IEdmTypeReference stringType = EdmCoreModel.Instance.GetPrimitive(EdmPrimitiveTypeKind.String, isNullable: false);
IEdmTypeReference intType = EdmCoreModel.Instance.GetPrimitive(EdmPrimitiveTypeKind.Int32, isNullable: false);
// Bound
EdmFunction getFirstName = new EdmFunction("WebApiDocNS", "GetFirstName", stringType, isBound: true, entitySetPathExpression: null, isComposable: false);
getFirstName.AddParameter("entity", new EdmEntityTypeReference(customer, false));
model.AddElement(getFirstName);

// Unbound
EdmFunction getNumber = new EdmFunction("WebApiDocNS", "GetOrderCount", intType, isBound: false, entitySetPathExpression: null, isComposable: false);
model.AddElement(getNumber);
```

It will generate the below metadata document:

```XML
<Function Name="GetFirstName" IsBound="true">
   <Parameter Name="entity" Type="WebApiDocNS.Customer" Nullable="false" />
   <ReturnType Type="Edm.String" Nullable="false" />
</Function>
<Function Name="GetOrderCount">
   <ReturnType Type="Edm.Int32" Nullable="false" />
</Function>
```

### Action

Let's define two actions. One is bound, the other is unbound as:

```C#

// Bound
EdmAction calculate = new EdmAction("WebApiDocNS", "CalculateOrderPrice", returnType: null, isBound: true, entitySetPathExpression: null);
calculate.AddParameter("entity", new EdmEntityTypeReference(customer, false));
model.AddElement(calculate);

// Unbound
EdmAction change = new EdmAction("WebApiDocNS", "ChangeCustomerById", returnType: null, isBound: false, entitySetPathExpression: null);
change.AddParameter("Id", intType);
model.AddElement(change);
```

It will generate the below metadata document:
```XML
<Action Name="CalculateOrderPrice" IsBound="true">
  <Parameter Name="entity" Type="WebApiDocNS.Customer" Nullable="false" />
</Action>
<Action Name="ChangeCustomerById">
  <Parameter Name="Id" Type="Edm.Int32" Nullable="false" />
</Action>
```

### Function Import

Unbound function can be called through function import. The following codes are used to build a function import:

```C#
container.AddFunctionImport("GetOrderCount", getNumber);
```

It will generate the below metadata document:

```XML
<FunctionImport Name="GetOrderCount" Function="WebApiDocNS.GetOrderCount" />
```

### Action Import

Unbound action can be called through action import. The following codes are used to build an action import:

```C#
container.AddActionImport("ChangeCustomerById", change);
```

It will generate the below metadata document:

```XML
<ActionImport Name="ChangeCustomerById" Action="WebApiDocNS.ChangeCustomerById" />
```

### Summary

Let's put all codes together:

```C#
public static IEdmModel GetEdmModel()
{
    EdmModel model = new EdmModel();

    // Complex Type
    EdmComplexType address = new EdmComplexType("WebApiDocNS", "Address");
    address.AddStructuralProperty("Country", EdmPrimitiveTypeKind.String);
    address.AddStructuralProperty("City", EdmPrimitiveTypeKind.String);
    model.AddElement(address);

    EdmComplexType subAddress = new EdmComplexType("WebApiDocNS", "SubAddress", address);
    subAddress.AddStructuralProperty("Street", EdmPrimitiveTypeKind.String);
    model.AddElement(subAddress);

    // Enum type
    EdmEnumType color = new EdmEnumType("WebApiDocNS", "Color");
    color.AddMember(new EdmEnumMember(color, "Red", new EdmIntegerConstant(0)));
    color.AddMember(new EdmEnumMember(color, "Blue", new EdmIntegerConstant(1)));
    color.AddMember(new EdmEnumMember(color, "Green", new EdmIntegerConstant(2)));
    model.AddElement(color);

    // Entity type
    EdmEntityType customer = new EdmEntityType("WebApiDocNS", "Customer");
    customer.AddKeys(customer.AddStructuralProperty("CustomerId", EdmPrimitiveTypeKind.Int32));
    customer.AddStructuralProperty("Location", new EdmComplexTypeReference(address, isNullable: true));
    model.AddElement(customer);

    EdmEntityType vipCustomer = new EdmEntityType("WebApiDocNS", "VipCustomer", customer);
    vipCustomer.AddStructuralProperty("FavoriteColor", new EdmEnumTypeReference(color, isNullable: false));
    model.AddElement(vipCustomer);

    EdmEntityType order = new EdmEntityType("WebApiDocNS", "Order");
    order.AddKeys(order.AddStructuralProperty("OrderId", EdmPrimitiveTypeKind.Int32));
    order.AddStructuralProperty("Token", EdmPrimitiveTypeKind.Guid);
    model.AddElement(order);

    EdmEntityContainer container = new EdmEntityContainer("WebApiDocNS", "Container");
    EdmEntitySet customers = container.AddEntitySet("Customers", customer);
    EdmEntitySet orders = container.AddEntitySet("Orders", order);
    model.AddElement(container);

    // EdmSingleton mary = container.AddSingleton("Mary", customer);

    // navigation properties
    EdmNavigationProperty ordersNavProp = customer.AddUnidirectionalNavigation(
        new EdmNavigationPropertyInfo
        {
            Name = "Orders",
            TargetMultiplicity = EdmMultiplicity.Many,
            Target = order
        });
    customers.AddNavigationTarget(ordersNavProp, orders);
	
    // function
    IEdmTypeReference stringType = EdmCoreModel.Instance.GetPrimitive(EdmPrimitiveTypeKind.String, isNullable: false);
    IEdmTypeReference intType = EdmCoreModel.Instance.GetPrimitive(EdmPrimitiveTypeKind.Int32, isNullable: false);

    EdmFunction getFirstName = new EdmFunction("WebApiDocNS", "GetFirstName", stringType, isBound: true, entitySetPathExpression: null, isComposable: false);
    getFirstName.AddParameter("entity", new EdmEntityTypeReference(customer, false));
    model.AddElement(getFirstName);

    EdmFunction getNumber = new EdmFunction("WebApiDocNS", "GetOrderCount", intType, isBound: false, entitySetPathExpression: null, isComposable: false);
    model.AddElement(getNumber);
    container.AddFunctionImport("GetOrderCount", getNumber);

    // action
    EdmAction calculate = new EdmAction("WebApiDocNS", "CalculateOrderPrice", returnType: null, isBound: true, entitySetPathExpression: null);
    calculate.AddParameter("entity", new EdmEntityTypeReference(customer, false));
    model.AddElement(calculate);

    EdmAction change = new EdmAction("WebApiDocNS", "ChangeCustomerById", returnType: null, isBound: false, entitySetPathExpression: null);
    change.AddParameter("Id", intType);
    model.AddElement(change);
    container.AddActionImport("ChangeCustomerById", change);

    return model;
}
```

And the final XML will be:

```XML
<?xml version="1.0" encoding="utf-8"?>
<edmx:Edmx Version="4.0" xmlns:edmx="https://docs.oasis-open.org/odata/ns/edmx">
  <edmx:DataServices>
    <Schema Namespace="WebApiDocNS" xmlns="https://docs.oasis-open.org/odata/ns/edm">
      <ComplexType Name="Address">
        <Property Name="Country" Type="Edm.String" />
        <Property Name="City" Type="Edm.String" />
      </ComplexType>
      <ComplexType Name="SubAddress" BaseType="WebApiDocNS.Address">
        <Property Name="Street" Type="Edm.String" />
      </ComplexType>
      <EnumType Name="Color">
        <Member Name="Red" Value="0" />
        <Member Name="Blue" Value="1" />
        <Member Name="Green" Value="2" />
      </EnumType>
      <EntityType Name="Customer">
        <Key>
          <PropertyRef Name="CustomerId" />
        </Key>
        <Property Name="CustomerId" Type="Edm.Int32" />
        <Property Name="Location" Type="WebApiDocNS.Address" />
        <NavigationProperty Name="Orders" Type="Collection(WebApiDocNS.Order)" />
      </EntityType>
      <EntityType Name="VipCustomer" BaseType="WebApiDocNS.Customer">
        <Property Name="FavoriteColor" Type="WebApiDocNS.Color" Nullable="false" />
      </EntityType>
      <EntityType Name="Order">
        <Key>
          <PropertyRef Name="OrderId" />
        </Key>
        <Property Name="OrderId" Type="Edm.Int32" />
        <Property Name="Token" Type="Edm.Guid" />
      </EntityType>
      <Function Name="GetFirstName" IsBound="true">
        <Parameter Name="entity" Type="WebApiDocNS.Customer" Nullable="false" />
        <ReturnType Type="Edm.String" Nullable="false" />
      </Function>
      <Function Name="GetOrderCount">
        <ReturnType Type="Edm.Int32" Nullable="false" />
      </Function>
      <Action Name="CalculateOrderPrice" IsBound="true">
        <Parameter Name="entity" Type="WebApiDocNS.Customer" Nullable="false" />
      </Action>
      <Action Name="ChangeCustomerById">
        <Parameter Name="Id" Type="Edm.Int32" Nullable="false" />
      </Action>
      <EntityContainer Name="Container">
        <EntitySet Name="Customers" EntityType="WebApiDocNS.Customer">
          <NavigationPropertyBinding Path="Orders" Target="Orders" />
        </EntitySet>
        <EntitySet Name="Orders" EntityType="WebApiDocNS.Order" />
        <FunctionImport Name="GetOrderCount" Function="WebApiDocNS.GetOrderCount" />
        <ActionImport Name="ChangeCustomerById" Action="WebApiDocNS.ChangeCustomerById" />
      </EntityContainer>
    </Schema>
  </edmx:DataServices>
</edmx:Edmx>
```
