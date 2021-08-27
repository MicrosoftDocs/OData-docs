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


To add revisions to an entity set for example. use the code below:
```csharp
var builder = new ODataConventionModelBuilder();
var customerConfiguration = builder.EntitySet<Customer>("Customers").HasRevisions(a => a.HasVersion("v1.2").HasKind(RevisionKind.Added).HasDescription("Added a new entity set"));
```

Use this structure to add revisions to all the other supported model elements above. 

$metadata

```xml
<?xml version="1.0" encoding="UTF-8"?>
<edmx:Edmx xmlns:edmx="http://docs.oasis-open.org/odata/ns/edmx" Version="4.0">
    <edmx:DataServices>
        <Schema xmlns="http://docs.oasis-open.org/odata/ns/edm" Namespace="Default">
            <EntityType Name="Customer">
                <Key>
                    <PropertyRef Name="CustomerId" />
                </Key>
                <Property Name="CustomerId" Type="Edm.Int32" Nullable="false" />
                <Property Name="Name" Type="Edm.String" />
            </EntityType>
            <EntityContainer Name="Container">
                <EntitySet Name="Customers" EntityType="Default.Customer" />
            </EntityContainer>
            <Annotations Target="Default.Container/Customers">
                <Annotation Term="Org.OData.Core.V1.Revisions">
                    <Collection>
                        <Record>
                            <PropertyValue Property="Version" String="v1.2" />
                            <PropertyValue Property="Kind">
                                <EnumMember>Org.OData.Core.V1.RevisionKind/Added</EnumMember>
                            </PropertyValue>
                            <PropertyValue Property="Description" String="desc" />
                        </Record>
                    </Collection>
                </Annotation>
            </Annotations>
        </Schema>
    </edmx:DataServices>
</edmx:Edmx>
```
To add a revision, you must provide the following properties: 
1. RevisionKind - the kind of revision you are making. Added, Modified or Deprecated.
1. Description - a description describing the revision you are making.
1. 
You can add also dynamic properties to provide more information for the model elements' revisions. Below is an example on how to add extra properties to a revision.  

```csharp

var builder = new ODataConventionModelBuilder();
var config = builder.EntitySet<Customer>("Customers").HasRevisions(a => a.HasVersion("v1.2").HasKind(RevisionKind.Deprecated).HasDescription("The M").HasDynamicProperty("Date", new DateTime(2021,11,11)).HasDynamicProperty("RemovalDate", new DateTime(2022,12,12)));

```
$metadata

```xml
<?xml version="1.0" encoding="UTF-8"?>
<edmx:Edmx xmlns:edmx="http://docs.oasis-open.org/odata/ns/edmx" Version="4.0">
    <edmx:DataServices>
        <Schema xmlns="http://docs.oasis-open.org/odata/ns/edm" Namespace="CoreCapabilities">
            <EntityType Name="Customer">
                <Key>
                    <PropertyRef Name="CustomerId" />
                </Key>
                <Property Name="CustomerId" Type="Edm.Int32" Nullable="false" />
                <Property Name="Name" Type="Edm.String" />
            </EntityType>
            <EntityContainer Name="Container">
                <EntitySet Name="Customers" EntityType="CoreCapabilities.Customer" />
            </EntityContainer>
            <Annotations Target="Default.Container/Customers">
                <Annotation Term="Org.OData.Core.V1.Revisions">
                    <Collection>
                        <Record>
                            <PropertyValue Property="Version" String="v1.2" />
                            <PropertyValue Property="Kind">
                                <EnumMember>Org.OData.Core.V1.RevisionKind/Deprecated</EnumMember>
                            </PropertyValue>
                            <PropertyValue Property="Description" String="The M" />
                            <PropertyValue Property="Date" DateTimeOffset="2021-11-11T00:00:00+03:00" />
                            <PropertyValue Property="RemovalDate" DateTimeOffset="2022-12-12T00:00:00+03:00" />
                        </Record>
                    </Collection>
                </Annotation>
            </Annotations>
        </Schema>
        <Schema xmlns="http://docs.oasis-open.org/odata/ns/edm" Namespace="Default">
         <EntityContainer Name="Container">
            <EntitySet Name="Customers" EntityType="CoreCapabilities.Customer" />
         </EntityContainer>
      </Schema>
   </edmx:DataServices>
</edmx:Edmx>

```





