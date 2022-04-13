---
title: "Specify type facets for type definitions"
description: "Specify type facets for type definitions"
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Type facets
**Applies To**: [!INCLUDE[appliesto-odataclient](../../../includes/appliesto-odatalib-v7.md)]

Users can specify various type facets for references to type definitions. The only constraint is that the type facets specified should be applicable to the underlying type definition.

If you want to specify type facets, you need to call this constructor:

```C#
public EdmTypeDefinitionReference(
    IEdmTypeDefinition typeDefinition,
    bool isNullable,
    bool isUnbounded,
    int? maxLength,
    bool? isUnicode,
    int? precision,
    int? scale,
    int? spatialReferenceIdentifier);
```

Here is sample code to create an EDM type definition reference with type facets

```C#
IEdmTypeDefinition typeDefinition = new EdmTypeDefinition("MyNS", "Title", EdmPrimitiveTypeKind.String);
IEdmTypeDefinitionReference reference = new EdmTypeDefinitionReference(
    typeDefinition,
    isNullable: true,
    isUnbounded: false,
    maxLength: 10,
    isUnicode: true,
    precision: null,
    scale: null,
    spatialReferenceIdentifier: null);
```

- `isNullable`: `true` to allow `null` values; `false` otherwise.
- `isUnbounded`: `true` to indicate `MaxLength="max"`; `false` to indicate that the `MaxLength` is a bounded value.
- `maxLength`: `null` for unspecified; other values for specified lengths. Invalid if `isUnbounded` is `true`.
- `isUnicode`: `true` if the encoding is Unicode; `false` for non-Unicode encoding; `null` for unspecified.
- `precision`: `null` for unspecified; other values for specified precisions; MUST be non-negative.
- `scale`: `null` to indicate `Scale="variable"`; other values for specified scales; MUST be non-negative.
- `spatialReferenceIdentifier`: `null` to indicate `SRID="variable"`; other values for specified SRIDs; MUST be non-negative.

It's worth mentioning that if you call the overloaded constructor taking two parameters, all the type facets will be auto-computed according to the OData protocol. For example, the default value of `SRID` is `0` for geometry types and `4326` for geography types.

```C#
public EdmTypeDefinitionReference(
    IEdmTypeDefinition typeDefinition,
    bool isNullable);
```
