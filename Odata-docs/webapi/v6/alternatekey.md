---
title: "Alternate Key"
description: ""

author: madansr7
ms.author: madansr7
ms.date: 02/19/2019
ms.topic: article
ms.service: multiple
---
#Alternate key
**Applies To**: [!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

From ODataLib 6.13.0, it supports the alternate key. For detail information about alternate keys, please refer to [here](https://github.com/OData/vocabularies/blob/master/OData.Community.Keys.V1.md).

The related Web API sample codes can be found [here](https://github.com/OData/ODataSamples/tree/master/WebApi/v4/ODataAlternateKeySamples).

## Single alternate key

### Edm Model builder
The following codes can be used to build the single alternate key:

``` csharp
EdmEntityType customer = new EdmEntityType("NS", "Customer"); 
customer.AddKeys(customer.AddStructuralProperty("ID", EdmPrimitiveTypeKind.Int32)); 
customer.AddStructuralProperty("Name", EdmPrimitiveTypeKind.String); 
var ssn = customer.AddStructuralProperty("SSN", EdmPrimitiveTypeKind.String); 
model.AddAlternateKeyAnnotation(customer, new Dictionary<string, IEdmProperty> 
{ 
    {"SSN", ssn} 
}); 
model.AddElement(customer);
```

### Related Metadata

The following is the related metadata:

``` csharp
<EntityType Name="Customer">
  <Key>  
    <PropertyRef Name="ID" />  
  </Key>
  <Property Name="ID" Type="Edm.Int32" Nullable="false" />
  <Property Name="Name" Type="Edm.String" />
  <Property Name="SSN" Type="Edm.String" />
  <Annotation Term="OData.Community.Keys.V1.AlternateKeys">
    <Collection>
     <Record Type="OData.Community.Keys.V1.AlternateKey">
      <PropertyValue Property="Key">
        <Collection>
          <Record Type="OData.Community.Keys.V1.PropertyRef">
            <PropertyValue Property="Alias" String="SocialSN" /> 
            <PropertyValue Property="Name" PropertyPath="SSN" />
          </Record>
        </Collection>
      </PropertyValue>
    </Record>
  </Annotation>
</EntityType>
```

## Multiple alternate keys

### Edm Model builder
The following codes can be used to build the multiple alternate keys:

``` csharp
EdmEntityType order = new EdmEntityType("NS", "Order");
order.AddKeys(order.AddStructuralProperty("OrderId", EdmPrimitiveTypeKind.Int32));
var orderName = order.AddStructuralProperty("Name", EdmPrimitiveTypeKind.String);
var orderToken = order.AddStructuralProperty("Token", EdmPrimitiveTypeKind.Guid);
order.AddStructuralProperty("Amount", EdmPrimitiveTypeKind.Int32);
model.AddAlternateKeyAnnotation(order, new Dictionary<string, IEdmProperty>
{
  {"Name", orderName}
});

model.AddAlternateKeyAnnotation(order, new Dictionary<string, IEdmProperty>
{
  {"Token", orderToken}
});

model.AddElement(order);
```

### Related Metadata

The following is the related metadata:

``` csharp
<EntityType Name="Order">
<Key>
  <PropertyRef Name="OrderId" />
</Key>
<Property Name="OrderId" Type="Edm.Int32" />
<Property Name="Name" Type="Edm.String" />
<Property Name="Token" Type="Edm.Guid" />
<Property Name="Amount" Type="Edm.Int32" />
<Annotation Term="OData.Community.Keys.V1.AlternateKeys">
  <Collection>
	<Record Type="OData.Community.Keys.V1.AlternateKey">
	  <PropertyValue Property="Key">
		<Collection>
		  <Record Type="OData.Community.Keys.V1.PropertyRef">
			<PropertyValue Property="Alias" String="Name" />
			<PropertyValue Property="Name" PropertyPath="Name" />
		  </Record>
		</Collection>
	  </PropertyValue>
	</Record>
	<Record Type="OData.Community.Keys.V1.AlternateKey">
	  <PropertyValue Property="Key">
		<Collection>
		  <Record Type="OData.Community.Keys.V1.PropertyRef">
			<PropertyValue Property="Alias" String="Token" />
			<PropertyValue Property="Name" PropertyPath="Token" />
		  </Record>
		</Collection>
	  </PropertyValue>
	</Record>
  </Collection>
</Annotation>
</EntityType>
```

## Composed alternate keys

### Edm Model builder
The following codes can be used to build the multiple alternate keys:

``` csharp
EdmEntityType person = new EdmEntityType("NS", "Person");
person.AddKeys(person.AddStructuralProperty("ID", EdmPrimitiveTypeKind.Int32));
var country = person.AddStructuralProperty("Country", EdmPrimitiveTypeKind.String);
var passport = person.AddStructuralProperty("Passport", EdmPrimitiveTypeKind.String);
model.AddAlternateKeyAnnotation(person, new Dictionary<string, IEdmProperty>
{
	{"Country", country},
	{"Passport", passport}
});
model.AddElement(person);
```

### Related Metadata

The following is the related metadata:

``` csharp
<EntityType Name="Person">
<Key>
  <PropertyRef Name="ID" />
</Key>
<Property Name="ID" Type="Edm.Int32" />
<Property Name="Country" Type="Edm.String" />
<Property Name="Passport" Type="Edm.String" />
<Annotation Term="OData.Community.Keys.V1.AlternateKeys">
  <Collection>
	<Record Type="OData.Community.Keys.V1.AlternateKey">
	  <PropertyValue Property="Key">
		<Collection>
		  <Record Type="OData.Community.Keys.V1.PropertyRef">
			<PropertyValue Property="Alias" String="Country" />
			<PropertyValue Property="Name" PropertyPath="Country" />
		  </Record>
		  <Record Type="OData.Community.Keys.V1.PropertyRef">
			<PropertyValue Property="Alias" String="Passport" />
			<PropertyValue Property="Name" PropertyPath="Passport" />
		  </Record>
		</Collection>
	  </PropertyValue>
	</Record>
  </Collection>
</Annotation>
</EntityType>
```

## Uri parser

Enable the alternate keys parser extension via the Uri resolver `AlternateKeysODataUriResolver`.

``` csharp
var parser = new ODataUriParser(model, new Uri("https://host"), new Uri("https://host/People(SocialSN = \'1\')"))
{
    Resolver = new AlternateKeysODataUriResolver(model)
};
```
