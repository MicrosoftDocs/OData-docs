---
title: "Abstract entity type support in .NET client"
description: ""

author: madansr7
ms.author: madansr7
ms.date: 7/1/2019
ms.topic: article
 
---
# Abstract entity type support in .NET client
**Applies To**: [!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v6.md)]

OData Client for .NET supports abstract entity type without key from ODataLib 6.11.0.

## Create model with abstract entity type

``` csharp
var abstractType = new EdmEntityType("DefaultNS", "AbstractEntity", null, true, false);
model.AddElement(abstractType);

var orderType = new EdmEntityType("DefaultNS", "Order", abstractType);
var orderIdProperty = new EdmStructuralProperty(orderType, "OrderID", EdmCoreModel.Instance.GetInt32(false));
orderType.AddProperty(orderIdProperty);
orderType.AddKeys(orderIdProperty);
model.AddElement(orderType);
```

## Output model
```xml
    <EntityType Name="AbstractEntity" Abstract="true" />
    <EntityType Name="Order" BaseType="DefaultNS.AbstractEntity">
      <Key>
        <PropertyRef Name="OrderID" />
      </Key>
    </EntityType>
```

## Client generated proxy file
T4 would auto-generate code for abstract entity type like:

``` csharp
[global::Microsoft.OData.Client.EntityType()]
public abstract partial class AbstractEntity : global::Microsoft.OData.Client.BaseEntityType, global::System.ComponentModel.INotifyPropertyChanged
{
 ...
}

[global::Microsoft.OData.Client.Key("OrderID")]
[global::Microsoft.OData.Client.EntitySet("Orders")]
public partial class Order : AbstractEntity
{
 ...
}
```
