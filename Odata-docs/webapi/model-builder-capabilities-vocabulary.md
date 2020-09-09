---
title: "Adding Capability Annotations with the OData Model Builder"
description: "capabilities vocabulary"
author: g2mula
ms.author: ginjira
ms.date: 8/17/2020
---

# Adding Capability Annotations with the OData Model Builder

**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

The model builder now allows you to configure the [capabilities of a service](https://github.com/oasis-tcs/odata-vocabularies/blob/master/vocabularies/Org.OData.Capabilities.V1.md). Please [follow the link](https://github.com/oasis-tcs/odata-vocabularies/blob/master/vocabularies/Org.OData.Capabilities.V1.md) for more information on [capabilities vocabulary](https://github.com/oasis-tcs/odata-vocabularies/blob/master/vocabularies/Org.OData.Capabilities.V1.md).

It is available on the [Nuget gallery](https://www.nuget.org/packages/Microsoft.OData.ModelBuilder)

You can install or update the NuGet package for OData Migration Extensions v1.0.0 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```PowerShell
PM> Install-Package Microsoft.OData.ModelBuilder
```

## Examples

Find below some sample usage...

Unless otherwise specified, examples will be based on the model below.

```csharp
public class Customer
{
  public int CustomerId { get; set; }
  public string Name { get; set; }
}

var builder = new ODataConventionModelBuilder();
var customerConfiguration = builder.EntitySet<Customer>("Customers");
```

which produces the $metadata below

```xml
<Schema Namespace="Default" xmlns="http://docs.oasis-open.org/odata/ns/edm">
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
</Schema>
```

### [CountRestrictions](https://github.com/oasis-tcs/odata-vocabularies/blob/master/vocabularies/Org.OData.Capabilities.V1.xml#L221)

Restrictions on /$count path suffix and $count=true system query option

```csharp
_ = customerConfiguration.HasCountRestrictions().IsCountable(false);
```

$metadata

```xml
<Schema Namespace="Default" xmlns="http://docs.oasis-open.org/odata/ns/edm">
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
    <Annotation Term="Org.OData.Capabilities.V1.CountRestrictions">
      <Record>
        <PropertyValue Property="Countable" Bool="false" />
      </Record>
    </Annotation>
  </Annotations>
</Schema>
```

### [SelectSupport](https://github.com/oasis-tcs/odata-vocabularies/blob/master/vocabularies/Org.OData.Capabilities.V1.xml#L335)

Support for $select and nested query options within $select

```csharp
_ = customerConfiguration.HasSelectSupport()
    .IsSupported(true)  // Supports $select
    .IsInstanceAnnotationsSupported(false)  // Does *not* support instance annotations in $select list
    .IsExpandable(false)  // $expand within $select is *not* supported
    .IsFilterable(true)  // $filter within $select is supported
    .IsSearchable(false)  // $search within $select is *not* supported
    .IsTopSupported(true)  // $top within $select is supported
    .IsSkipSupported(false)  // $skip within $select is *not* supported
    .IsComputeSupported(false)  // $compute within $select is *not* supported
    .IsCountable(true)  // $count within $select is supported
    .IsSortable(false);  // $orderby within $select is *not* supported
```

$metadata

```xml
<Schema Namespace="Default" xmlns="http://docs.oasis-open.org/odata/ns/edm">
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
    <Annotation Term="Org.OData.Capabilities.V1.SelectSupport">
      <Record>
        <PropertyValue Property="Supported" Bool="true" />
        <PropertyValue Property="InstanceAnnotationsSupported" Bool="false" />
        <PropertyValue Property="Expandable" Bool="false" />
        <PropertyValue Property="Filterable" Bool="true" />
        <PropertyValue Property="Searchable" Bool="false" />
        <PropertyValue Property="TopSupported" Bool="true" />
        <PropertyValue Property="SkipSupported" Bool="false" />
        <PropertyValue Property="ComputeSupported" Bool="false" />
        <PropertyValue Property="Countable" Bool="true" />
        <PropertyValue Property="Sortable" Bool="false" />
      </Record>
    </Annotation>
  </Annotations>
</Schema>
```

Note that only specified properties will be output eg

```csharp
_ = customerConfiguration.HasSelectSupport()
    .IsSupported(true)  // Supports $select
    .IsCountable(true)  // $count within $select is supported
    .IsSortable(false); // $orderby within $select is *not* supported
```

$metadata

```xml
<Schema Namespace="Default" xmlns="http://docs.oasis-open.org/odata/ns/edm">
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
    <Annotation Term="Org.OData.Capabilities.V1.SelectSupport">
      <Record>
        <PropertyValue Property="Supported" Bool="true" />
        <PropertyValue Property="Countable" Bool="true" />
        <PropertyValue Property="Sortable" Bool="false" />
      </Record>
    </Annotation>
  </Annotations>
</Schema>
```

### [ReadRestrictions](https://github.com/oasis-tcs/odata-vocabularies/blob/master/vocabularies/Org.OData.Capabilities.V1.xml#L901)

Restrictions for retrieving a collection of entities, retrieving a singleton instance.

```csharp
_ = customerConfiguration
    .HasReadRestrictions()
    .IsReadable(true)
    .HasDescription("Permissions Required To Access Resource")
    .HasPermissions(permission =>
        permission.HasSchemeName("Application")
            .HasScopes(scope => scope.HasScope("Customers.ReadWrite"))
            .HasScopes(scope => scope.HasScope("Customers.ReadAll")))
    .HasPermissions(permission =>
        permission.HasSchemeName("Delegated")
            .HasScopes(scope => scope.HasScope("Customers.ReadWrite"))
            .HasScopes(scope => scope.HasScope("Customers.ReadAll")));
```

$metadata

```xml
<Schema Namespace="Default" xmlns="http://docs.oasis-open.org/odata/ns/edm">
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
    <Annotation Term="Org.OData.Capabilities.V1.ReadRestrictions">
      <Record>
        <PropertyValue Property="Readable" Bool="true" />
        <PropertyValue Property="Permissions">
          <Collection>
            <Record>
              <PropertyValue Property="SchemeName" String="Application" />
              <PropertyValue Property="Scopes">
                <Collection>
                  <Record>
                    <PropertyValue Property="Scope" String="Customers.ReadWrite" />
                  </Record>
                  <Record>
                    <PropertyValue Property="Scope" String="Customers.ReadAll" />
                  </Record>
                </Collection>
              </PropertyValue>
            </Record>
            <Record>
              <PropertyValue Property="SchemeName" String="Delegated" />
              <PropertyValue Property="Scopes">
                <Collection>
                  <Record>
                    <PropertyValue Property="Scope" String="Customers.ReadWrite" />
                  </Record>
                  <Record>
                    <PropertyValue Property="Scope" String="Customers.ReadAll" />
                  </Record>
                </Collection>
              </PropertyValue>
            </Record>
          </Collection>
        </PropertyValue>
        <PropertyValue Property="Description" String="Permissions Required To Access Resource" />
      </Record>
    </Annotation>
  </Annotations>
</Schema>
```

### [DeleteRestrictions](https://github.com/oasis-tcs/odata-vocabularies/blob/master/vocabularies/Org.OData.Capabilities.V1.xml#L763)

Restrictions on delete operations

```csharp
_ = customerConfiguration
    .HasDeleteRestrictions()
    .IsDeletable(false)
    .HasDescription("Delete not allowed")
    .HasLongDescription("Configuration could be done as shown in the read restrictions above, this is just for demo purposes");
```

$metadata

```xml
<Schema Namespace="Default" xmlns="http://docs.oasis-open.org/odata/ns/edm">
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
    <Annotation Term="Org.OData.Capabilities.V1.DeleteRestrictions">
      <Record>
        <PropertyValue Property="Deletable" Bool="false" />
        <PropertyValue Property="Description" String="Delete not allowed" />
        <PropertyValue Property="LongDescription" String="Configuration could be done as shown in the read restrictions above, this is just for demo purposes" />
      </Record>
    </Annotation>
  </Annotations>
</Schema>
```

### Sample Project

A sample project can be found in the [ODataSamples repository on github](https://github.com/OData/ODataSamples/tree/master/WebApiCore/ODataCapabilitiesVocabularySample)
