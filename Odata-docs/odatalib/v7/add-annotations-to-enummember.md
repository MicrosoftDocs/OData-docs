---
title: "Add vocabulary annotations to EdmEnumMember in odatalib v7"
description: ""
author: saumadan
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Add vocabulary annotations
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

From ODataLib 6.11.0, it supports to add vocabulary annotations to EdmEnumMember.

## Create Model

```C#
EdmModel model = new EdmModel();

EdmEntityContainer container = new EdmEntityContainer("DefaultNamespace", "Container");
model.AddElement(container);

EdmEntityType carType = new EdmEntityType("DefaultNamespace", "Car");
EdmStructuralProperty carOwnerId = carType.AddStructuralProperty("OwnerId", EdmCoreModel.Instance.GetInt32(false));
carType.AddKeys(carOwnerId);

var colorType = new EdmEnumType("DefaultNamespace", "Color", true);
model.AddElement(colorType);
colorType.AddMember("Cyan", new EdmEnumMemberValue(1));
colorType.AddMember("Blue", new EdmEnumMemberValue(2));

EdmEnumMember enumMember = new EdmEnumMember(colorType, "Red", new EdmEnumMemberValue(3));
colorType.AddMember(enumMember);

EdmTerm stringTerm = new EdmTerm("DefaultNamespace", "StringTerm", EdmCoreModel.Instance.GetString(true));
model.AddElement(stringTerm);

var annotation = new EdmVocabularyAnnotation(
    enumMember,
    stringTerm,
    "q1",
    new EdmStringConstant("Hello world!"));
    model.AddVocabularyAnnotation(annotation);

carType.AddStructuralProperty("Color", new EdmEnumTypeReference(colorType, false));

container.AddEntitySet("Cars", carType);
```

## Output

```xml
    <?xml version="1.0" encoding="utf-8"?>
      <edmx:Edmx Version="4.0" xmlns:edmx="https://docs.oasis-open.org/odata/ns/edmx">
        <edmx:DataServices>
          <Schema Namespace="DefaultNamespace" xmlns="https://docs.oasis-open.org/odata/ns/edm">
            <EnumType Name="Color" IsFlags="true">
              <Member Name="Cyan" Value="1" />
              <Member Name="Blue" Value="2" />
              <Member Name="Red" Value="3">
                <Annotation Term="DefaultNamespace.StringTerm" Qualifier="q1" String="Hello world!" />
              </Member>
            </EnumType>
            <Term Name="StringTerm" Type="Edm.String" />
            <EntityContainer Name="Container">
                <EntitySet Name="Cars" EntityType="DefaultNamespace.Car" />
            </EntityContainer>
          </Schema>
        </edmx:DataServices>
      </edmx:Edmx>
```
