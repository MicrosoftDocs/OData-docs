---
title: " Non-convention model builder with capabilities vocabulary"
description: "capabilities vocabulary"
author: g2mula
ms.author: g2mula
ms.date: 8/17/2020
---
# Non-convention model builder with capabilities vocabulary
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

The model builder now allows you to configure the [capabilities of a service](https://github.com/oasis-tcs/odata-vocabularies/blob/master/vocabularies/Org.OData.Capabilities.V1.md). Please follow the link for more information on capabilities vocabulary.

### Examples
Find below some sample usage...

Unless otherwise specified, examples will be based on the model below.
```csharp
public class Customer
{
  public int CustomerId { get; set; }
  public string Name { get; set; }
}

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
    .IsInstanceAnnotationsSupported(false)  // Supports instance annotations in $select list
    .IsExpandable(false)  // $expand within $select is supported
    .IsFilterable(true)  // $filter within $select is supported
    .IsSearchable(false)  // $search within $select is supported
    .IsTopSupported(true)  // $top within $select is supported
    .IsSkipSupported(false)  // $skip within $select is supported
    .IsComputeSupported(false)  // $compute within $select is supported
    .IsCountable(true)  // $count within $select is supported
    .IsSortable(false);  // $orderby within $select is supported
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
Note that if you do not specify any property, then these will not be output eg
```csharp
_ = customerConfiguration.HasSelectSupport()
    .IsSupported(true)  // Supports $select
    .IsCountable(true)  // $count within $select is supported
    .IsSortable(false); // $orderby within $select is supported
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
