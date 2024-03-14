---
title:  "Mark the EDM model as immutable"
description: A guide on how to improve performance by marking the EDM model as immutable.
date:   2023-08-11
ms.date: 8/11/2023
author: habbes
ms.author: clhabins
---

# Mark the EDM model as immutable

**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v8.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-odatalib](../includes/appliesto-odatalib-v7.md)]

OData libraries at times perform multiple lookups in the EDM model when processing a request. Some of these lookups may be expensive, especially for large models with a lot of schema elements.
We have some optimizations in place to reduce the performance overhead of these internal lookups, but some of these optimizations are only applicable if the library knows that the EDM model will not change. You can enable these optimizations by explicitly marking your EDM as immutable by calling the `MarkAsImmutable` method, which is an extension method in the `Microsoft.OData.Edm` namespace.

```csharp
using Microsoft.OData.Edm;

IEdmModel GetEdmModel()
{
    var modelBuilder = new ODataConventionModelBuilder();
    modelBuilder.EntitySet<Person>("People");
    IEdmModel model = modelBuilder.GetEdmModel();
    model.MarkAsImmutable();
    return model;
}
```

It is recommended to mark the EDM model as immutable since most applications do not modify the model after it has been constructed.

It is however important to note that the library makes no effort to ensure or verify that the model doesn't change after calling this method. It is the responsibility of the developer to ensure that the model is not modified after the `MarkAsImmutable` method is called. Modifying the model after `MarkAsImmutable` method has been called may lead to unexpected behavior.

The following code snippet shows an example of incorrect usage of this method:

```csharp
using Microsoft.OData.Edm;

IEdmModel GetEdmModel()
{
    var model = new EdmModel();
    var personType = new EdmEntityType("Sample.NS", "Person");
    personType.AddKeys(personType.AddStructuralProperty("Id", EdmPrimitiveTypeKind.Int32, isNullable: false));
    model.MarkAsImmutable();

    var addressType = new EdmComplexType("Sample.NS", "Address");
    // DO NOT DO THIS! Modifying the model after calling MarkAsImmutable
    // can lead to unexpected behavior.
    model.AddElement(addressType);
    return model;
}
```
