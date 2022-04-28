---
title : "4.2 Referential constraint"
description: Learn how to use the referential constraint in OData WebApi. 
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
---
# Referential constraint
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

The following sample codes can be used for Web API OData V3 & V4 with a little bit function name changing.

### Define Referential Constraint Using Attribute

There is an attribute named “ForeignKeyAttribute” which can be place on:

1.the foreign key property and specify the associated navigation property name, for example: 

```C#

public class Order
{
    public int OrderId { get; set; }

    [ForeignKey("Customer")]
    public int MyCustomerId { get; set; }

    public Customer Customer { get; set; }
}

```

2.a navigation property and specify the associated foreign key name, for example:

```C#

public class Order
{
    public int OrderId { get; set; }

    public int CustId1 { get; set; }
    public string CustId2 { get; set; }

    [ForeignKey("CustId1,CustId2")]
    public Customer Customer { get; set; }
}

```
*Where*, *Customer* has two keys.

Now, you can build the Edm Model by convention model builder as:

```C#

public IEdmModel GetEdmModel()
{            
    ODataConventionModelBuilder builder = new ODataConventionModelBuilder();
    builder.EntitySet<Customer>("Customers");
    builder.EntitySet<Order>("Orders");
    return builder.GetEdmModel();
}

```

#### Define Referential Constraint Using Convention

If user doesn’t add *any* referential constraint, Web API will try to help user to discovery the foreign key automatically. There are two conventions as follows:
1.With same property type and same type name plus key name. For example:
   
```C#

public class Customer
{ 
   [Key]
   public string Id {get;set;}
   public IList<Order> Orders {get;set;}
}
public class Order
{
    public int OrderId { get; set; }
    public string CustomerId {get;set;}
    public Customer Customer { get; set; }
}

```
*Where*, *Customer* type name "Customer" plus key name "Id" equals the property "CustomerId" in the *Order*.

2.With same property type and same property name. For example:
   
```C#

public class Customer
{ 
   [Key]
   public string CustomerId {get;set;}
   public IList<Order> Orders {get;set;}
}

public class Order
{
    public int OrderId { get; set; }
    public string CustomerId {get;set;}
    public Customer Customer { get; set; }
}

```
*Where*, Property (key) "CustomerId" in the *Customer* equals the property "CustomerId" in the *Order*.

Now, you can build the Edm Model using convention model builder same as above section.

### Define Referential Constraint Programmatically
You can call the new added Public APIs (HasRequired, HasOptional) to define the referential constraint when defining a navigation property. For example:

```C#

public class Customer
{
    public int Id { get; set; }
       
    public IList<Order> Orders { get; set; }
}

public class Order
{
    public int OrderId { get; set; }
 
    public int CustomerId { get; set; }         

    public Customer Customer { get; set; }
}

ODataModelBuilder builder = new ODataModelBuilder();
builder.EntityType<Customer>().HasKey(c => c.Id).HasMany(c => c.Orders);
builder.EntityType<Order>().HasKey(c => c.OrderId)
    .HasRequired(o => o.Customer, (o, c) => o.CustomerId == c.Id);
    .CascadeOnDelete();
    
```

It also supports to define multiple referential constraints, for example:
```C#

builder.EntityType<Order>()
    .HasKey(o => o.OrderId)
    .HasRequired(o => o.Customer, (o,c) => o.Key1 == c.Id1 && o.Key2 == c.Id2);
    
```

#### Define Nullable Referential Constraint Using Convention

Currently, it doesn't suppport to define nullable referential constraint from attribute and convention method. However, you can do it by Programmatically by calling `HasOptional()` method:

For example:

```C#

public class Product
{
    [Key]
    public int ProductId { get; set; }

    public int SupplierId { get; set; }

    public Supplier Supplier { get; set; }
}

public class Category
{
    [Key]
    public int CategoryId { get; set; }
}

ODataModelBuilder builder = new ODataModelBuilder();
builder.EntitySet<Product>("Product");
var product = builder.EntityType<Product>().HasOptional(p => p.Supplier, (p, s) => p.SupplierId == s.SupplierId);
    
```

Then you can get the following result:

```XML
<EntityType Name="Product">
    <Key>
      <PropertyRef Name="ProductId" />
    </Key>
    <Property Name="SupplierId" Type="Edm.Int32" />
    <Property Name="ProductId" Type="Edm.Int32" Nullable="false" />
    <Property Name="CategoryId" Type="Edm.Int32" />
    <NavigationProperty Name="Supplier" Type="WebApiService.Supplier">
      <ReferentialConstraint Property="SupplierId" ReferencedProperty="SupplierId" />
    </NavigationProperty>
    <NavigationProperty Name="Category" Type="WebApiService.Category" />
</EntityType>
```

Where, `CategoryId` is nullable while navigation property `Supplier` is nullable too.