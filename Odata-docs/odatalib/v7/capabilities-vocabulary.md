---
title: "Capabilities vocabulary support in odatalib v7"
description: "This section describes how to use capabilities vocabulary in the Edm Model."
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Capabilities vocabulary support in ODL
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

From ODataLib 6.13.0, it supports the capabilities vocabulary. For detail information about capabilities vocabulary, please refer to [here](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/vocabularies/Org.OData.Capabilities.V1.xml).

## Enable capabilities vocabulary

If you build the Edm model from the following codes:

```C#
IEdmModel model = new EdmModel();
```

The capabilities vocabulary is enabled as a reference model in the Edm Model.

## How to use capabilities vocabulary

ODL doesn't provide a set of API to add capabilites, but it provides an unified API to add all vocabularies:
 
```C#
SetVocabularyAnnotation
```

Let's have an example to illustrate how to use capabilities vocabulary:

```C#
IEnumerable<IEdmProperty> requiredProperties = ...
IEnumerable<IEdmProperty> nonFilterableProperties = ...
requiredProperties = requiredProperties ?? EmptyStructuralProperties;  
nonFilterableProperties = nonFilterableProperties ?? EmptyStructuralProperties;  

IList<IEdmPropertyConstructor> properties = new List<IEdmPropertyConstructor>  
{  
  new EdmPropertyConstructor(CapabilitiesVocabularyConstants.FilterRestrictionsFilterable, new EdmBooleanConstant(isFilterable)),
  new EdmPropertyConstructor(CapabilitiesVocabularyConstants.FilterRestrictionsRequiresFilter, new EdmBooleanConstant(isRequiresFilter)),
  new EdmPropertyConstructor(CapabilitiesVocabularyConstants.FilterRestrictionsRequiredProperties, new EdmCollectionExpression(
	requiredProperties.Select(p => new EdmPropertyPathExpression(p.Name)).ToArray())),
  new EdmPropertyConstructor(CapabilitiesVocabularyConstants.FilterRestrictionsNonFilterableProperties, new EdmCollectionExpression(
    nonFilterableProperties.Select(p => new EdmPropertyPathExpression(p.Name)).ToArray()))
};

IEdmTerm term = model.FindTerm("Org.OData.Capabilities.V1.FilterRestrictions");
if (term != null)  
{  
  IEdmRecordExpression record = new EdmRecordExpression(properties);  
  EdmVocabularyAnnotation annotation = new EdmVocabularyAnnotation(target, term, record);
  annotation.SetSerializationLocation(model, EdmVocabularyAnnotationSerializationLocation.Inline);
  model.SetVocabularyAnnotation(annotation);
}  
```

### The related metadata

The corresponding metadata can be as follows:

```xml
<EntitySet Name="Customers" EntityType="NS">
	<Annotation Term="Org.OData.Capabilities.V1.FilterRestrictions">
		<Record>
			<PropertyValue Property="Filterable" Bool="true" />
			<PropertyValue Property="RequiresFilter" Bool="true" />
			<PropertyValue Property="RequiredProperties">
				<Collection />
			</PropertyValue>
			<PropertyValue Property="NonFilterableProperties">
				<Collection>
					<PropertyPath>Name</PropertyPath>
					<PropertyPath>Orders</PropertyPath>
					<PropertyPath>NotFilterableNotSortableLastName</PropertyPath>
					<PropertyPath>NonFilterableUnsortableLastName</PropertyPath>
				</Collection>
			</PropertyValue>
		</Record>
	</Annotation>
```
