---
title: "Adding Revisions Annotations with the OData Model Builder"
description: "core capabilities vocabulary"
author: ElizabethOkerio
ms.author: elokerio
ms.date: 7/22/2021
---

# Adding Revisions Annotations with the OData Model Builder

**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

The model builder now allows you to add [revisions annotations](https://github.com/oasis-tcs/odata-vocabularies/blob/main/vocabularies/Org.OData.Core.V1.xml#L77) to your model elements. Please [follow the link](https://github.com/oasis-tcs/odata-vocabularies/blob/main/vocabularies/Org.OData.Core.V1.md) for more information on [core capabilities vocabulary](https://github.com/oasis-tcs/odata-vocabularies/blob/main/vocabularies/Org.OData.Core.V1.md).

It is available on the [Nuget gallery](https://www.nuget.org/packages/Microsoft.OData.ModelBuilder)

You can install or update the NuGet package for OData Migration Extensions v1.0.0 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```PowerShell
PM> Install-Package Microsoft.OData.ModelBuilder
```
Then Add the following using to your project class.
```csharp
using Microsoft.OData.ModelBuilder.Vocabularies
```
## Examples

Find below some sample usages...

Unless otherwise specified, examples will be based on the model below.

```csharp
public class Customer
{
  public int CustomerId { get; set; }
  public string Name { get; set; }
}

public class Address
{
    public string City {get;set;}
}

public enum Color
{
    Red,
    Green,
    Blue
}
```
Revisions can be added to all model elements. So far, the OData Model Builder supports revisions to the following model elements:

1. EntitySets
1. Actions and Action Imports
1. Functions and Function Imports
1. Entity Types
1. Complex Types
1. Enum Types
1. Enum Members
1. Properties

There are three kinds of revisions that can be made to any of the model elements above: 

```csharp
enum RevisionKind
{
    //when a model element is added
    Added,

    //when a model element is modified
    Modified,

    //when a model elemeny is deprecated
    Deprecated
}
```


To add deprecated revisions to the entity set. use the code below:
```csharp
var builder = new ODataConventionModelBuilder();
var customerConfiguration = builder.EntitySet<Customer>("Customers").HasRevisions("Deprecated", "v1.1.0", RevisionKind.Deprecated, new DateTime(2021,8,8), new DateTime(2022, 12,12)");
```
The following is a short description of the <code>HasRevisions()</code> method's parameters:

Deprecated is a description of why the entity set or any other model element is being deprecated.

v.1.1.0 is the model version the deprecation was made

RevisionKind.Deprecated is the kind of revision being done. There are 3 kinds of revisions that can be made to a model element. 

```csharp
enum RevisionKind
{
    //when a model element is added
    Added,

    //when a model element is modified
    Modified,

    //when a model elemeny is deprecated
    Deprecated
}
```


