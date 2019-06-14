---
title: " ConcurrencyMode and ETag"
description: ""


ms.date: 04/21/2015
---
# ConcurrencyMode and ETag
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

In OData V3 protocol, concurrencyMode is a special facet that can be applied to any primitive Entity Data Model (EDM) type. Possible values are `None`, which is the default, and `Fixed`. When used on an EntityType property, `ConcurrencyMode` specifies that the value of that declared property should be used for optimistic concurrency checks. In the metadata, concurrencyMode will be shown as following:

```XML
<EntityType Name="Order">
    <Key>
        <PropertyRef Name="Id" />
    </Key>
    <Property Name="Id" Type="Edm.Int32" Nullable="false" />
    <Property Name="ConcurrencyCheckRow" Type="Edm.String" Nullable="false" ConcurrencyMode="Fixed" /> />  
</EntityType>
```

There are two approaches to set concurrencyMode for a primitive property:
Using ConcurrencyCheck attribute:

```C#
public class Order
{
    public int Id { get; set; }
    
    [ConcurrencyCheck]
    public string ConcurrencyCheckRow { get; set; }
}
```

Using API call

```C#
ODataModelBuilder builder = new ODataModelBuilder();
builder.Entity<Order>().Property(c => c.ConcurrencyCheckRow).IsConcurrencyToken;
```
