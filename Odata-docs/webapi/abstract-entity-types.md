---
title : "Abstract entity types"
ms.date: 03/24/2015
---
# Abstract entity types
**Applies To**: [!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)]
[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Since [Web API OData V5.5-beta](https://www.nuget.org/packages/Microsoft.AspNet.OData/5.5.0-beta), it is allowed to:

1. define abstract entity types without keys.
2. define abstract type (entity & complex) without any properties.
3. define derived entity types with their own keys.

Let's see some examples:

## Entity type example

The CLR model is shown as below:

```C#
public abstract class Animal
{
}

public class Dog : Animal
{
  public int DogId { get; set; }
}

public class Pig : Animal
{
  public int PigId { get; set; }
}
```

We can use the following codes to build Edm Model:

```C#
  var builder = new ODataConventionModelBuilder();
  builder.EntityType<Animal>();
  builder.EntitySet<Dog>("Dogs");
  builder.EntitySet<Pig>("Pigs");
  IEdmModel model = builder.GetEdmModel()
```

Then, we can get the metadata document for *Animal* as:

```XML
<EntityType Name="Animal" Abstract="true" />
<EntityType Name="Dog" BaseType="NS.Animal">
    <Key>
        <PropertyRef Name="DogId" />
    </Key>
    <Property Name="DogId" Type="Edm.Int32" Nullable="false" />
</EntityType>
<EntityType Name="Pig" BaseType="NS.Animal">
    <Key>
        <PropertyRef Name="PigId" />
    </Key>
    <Property Name="PigId" Type="Edm.Int32" Nullable="false" />
</EntityType>
```

Note:

1. *Animal* is an abstract entity type without any keys and any properties
2. *Dog* & *Pig* are two sub entity types derived from *Animal* with own keys. 

However, it's obvious that abstract entity type without keys can't be used to define any navigation sources (entity set or singleton). 
So, if you try to:

```C#
builder.EntitySet<Animal>("Animals");
```

you will get the following exception:

```C#
System.InvalidOperationException: The entity set or singlet on 'Animals' is based on type 'NS.Animal' that has no keys defined.
```

## Complex type example

Let's see a complex example. The CLR model is shown as below:

```C#
public abstract class Graph
{ }

public class Point : Graph
{
  public int X { get; set; }
  public int Y { get; set; }
}

public class Line : Graph
{
  public IList<Point> Vertexes { get; set; }
}
```    

We can use the following codes to build Edm Model:

```C#
  var builder = new ODataConventionModelBuilder();
  builder.ComplexType<Graph>();
  IEdmModel model = builder.GetEdmModel()
```

Then, we can get the metadata document for *Graph* as:

```XML
<ComplexType Name="Graph" Abstract="true" />
<ComplexType Name="Point" BaseType="NS.Graph">
    <Property Name="X" Type="Edm.Int32" Nullable="false" />
    <Property Name="Y" Type="Edm.Int32" Nullable="false" />
</ComplexType>
<ComplexType Name="Line" BaseType="NS.Graph">
    <Property Name="Vertexes" Type="Collection(NS.Point)" />
</ComplexType>
```

Where, *Graph* is an abstract complex type without any properties.