---
title: "Set schema for out-of-line annotations"
description: "Set schema for out-of-line annotations"
author: gathogojr
ms.author: jogathog
ms.date: 10/9/2023
ms.topic: article
 
---
# Set schema for out-of-line annotations
**Applies To**: [!INCLUDE[appliesto-odataclient](../../../includes/appliesto-odatalib-v7.md)]

The Edm library lets you [define annotations](/odata/odatalib/edm/define-annotations). Annotations are used to define additional characteristics or capabilities of model elements. They can be expressed in the schema document in two ways: **inline** as child elements of the annotated element, or **out-of-line**/**externally** as children of an `Annotations` element that targets the model element to be annotated using a path expression that uniquely identifies the target model element.

This page shows how to define an out-of-line annotation in a simple Edm model and specify the schema it should appear in.

## Create an ASP.NET Core application
Create an ASP.NET Core application based on the ASP.NET Core Empty template and import the `Microsoft.AspNetCore.OData` nuget package:

---

# [Visual Studio](#tab/visual-studio)

In the Visual Studio **Package Manager Console**:
```powershell
Install-Package Microsoft.AspNetCore.OData
```

# [.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.AspNetCore.OData
```

---

## Configure the OData service and build the Edm model 
Replace the contents of _Program.cs_ file with the following code:

```csharp
using Microsoft.AspNetCore.OData;
using Microsoft.OData.Edm;
using Microsoft.OData.Edm.Vocabularies;

var builder = WebApplication.CreateBuilder(args);

var model = new EdmModel();

// Add Employee entity to Ns namespace
var employeeEntityType = new EdmEntityType("Ns", "Employee");
employeeEntityType.AddKeys(
    employeeEntityType.AddStructuralProperty("Id", EdmPrimitiveTypeKind.Int32, isNullable: false));
employeeEntityType.AddStructuralProperty("Name", EdmPrimitiveTypeKind.String, isNullable: true);
employeeEntityType.AddStructuralProperty("Salary", EdmPrimitiveTypeKind.Decimal, isNullable: false);
model.AddElement(employeeEntityType);

// Add Entity Container to Default namespace
var defaultEntityContainer = new EdmEntityContainer("Default", "Container");
model.AddElement(defaultEntityContainer);

// Add Employee entity set to the entity container
var employeesEntitySet = defaultEntityContainer.AddEntitySet("Employees", employeeEntityType);

// Add out-of-line vocabulary annotation
var notSortableVocabularyAnnotation = new EdmVocabularyAnnotation(
    employeesEntitySet,
    new EdmTerm("Org.OData.Capabilities.V1", "SortRestrictions", new EdmEntityTypeReference(employeeEntityType, isNullable: false)),
    new EdmRecordExpression(
        new EdmPropertyConstructor("Sortable", new EdmBooleanConstant(true)),
        new EdmPropertyConstructor("AscendingOnlyProperties", new EdmCollectionExpression()),
        new EdmPropertyConstructor("DescendingOnlyProperties", new EdmCollectionExpression()),
        new EdmPropertyConstructor("NonSortableProperties", new EdmCollectionExpression(
            new EdmPropertyPathExpression("Salary")))));
model.AddVocabularyAnnotation(notSortableVocabularyAnnotation);

// Configure OData service
builder.Services.AddControllers().AddOData(
    options => options.AddRouteComponents(
        model));

var app = builder.Build();

app.UseRouting();
app.UseEndpoints(endpoints => endpoints.MapControllers());

app.Run();
```

## Run the service
Run the application and query the service metadata:
```http
GET http://localhost:5000/$metadata
```

Response:
```xml
<?xml version="1.0" encoding="utf-8"?>
<edmx:Edmx Version="4.0" xmlns:edmx="http://docs.oasis-open.org/odata/ns/edmx">
    <edmx:DataServices>
        <Schema Namespace="Ns" xmlns="http://docs.oasis-open.org/odata/ns/edm">
            <EntityType Name="Employee">
                <Key>
                    <PropertyRef Name="Id" />
                </Key>
                <Property Name="Id" Type="Edm.Int32" Nullable="false" />
                <Property Name="Name" Type="Edm.String" />
                <Property Name="Salary" Type="Edm.Decimal" Nullable="false" Scale="Variable" />
            </EntityType>
            <Annotations Target="Default.Container/Employees">
                <Annotation Term="Org.OData.Capabilities.V1.SortRestrictions">
                    <Record>
                        <PropertyValue Property="Sortable" Bool="true" />
                        <PropertyValue Property="AscendingOnlyProperties">
                            <Collection />
                        </PropertyValue>
                        <PropertyValue Property="DescendingOnlyProperties">
                            <Collection />
                        </PropertyValue>
                        <PropertyValue Property="NonSortableProperties">
                            <Collection>
                                <PropertyPath>Salary</PropertyPath>
                            </Collection>
                        </PropertyValue>
                    </Record>
                </Annotation>
            </Annotations>
        </Schema>
        <Schema Namespace="Default" xmlns="http://docs.oasis-open.org/odata/ns/edm">
            <EntityContainer Name="Container">
                <EntitySet Name="Employees" EntityType="Ns.Employee" />
            </EntityContainer>
        </Schema>
    </edmx:DataServices>
</edmx:Edmx>
```

The `Org.OData.Capabilities.V1.SortRestrictions` annotation appears under an `Annotations` element in the `Ns` namespace.

## Set the schema the annotation should appear in
The following two steps show how to specify the schema the annotation should appear in:
1. Import `Microsoft.OData.Edm.Csdl` namespace.
2. Use the `SetSchemaNamespace` to set the schema namespace:
    ```csharp
    // ... rest of the Edm model definition code
    model.AddVocabularyAnnotation(notSortableVocabularyAnnotation);
    notSortableVocabularyAnnotation.SetSchemaNamespace(model, "Default");
    ```

## Rerun the service
Rerun the application and query the service metadata. This time round the `Annotations` element is in the `Default` namespace:
```xml
<?xml version="1.0" encoding="utf-8"?>
<edmx:Edmx Version="4.0" xmlns:edmx="http://docs.oasis-open.org/odata/ns/edmx">
    <edmx:DataServices>
        <Schema Namespace="Ns" xmlns="http://docs.oasis-open.org/odata/ns/edm">
            <EntityType Name="Employee">
                <Key>
                    <PropertyRef Name="Id" />
                </Key>
                <Property Name="Id" Type="Edm.Int32" Nullable="false" />
                <Property Name="Name" Type="Edm.String" />
                <Property Name="Salary" Type="Edm.Decimal" Nullable="false" Scale="Variable" />
            </EntityType>
        </Schema>
        <Schema Namespace="Default" xmlns="http://docs.oasis-open.org/odata/ns/edm">
            <EntityContainer Name="Container">
                <EntitySet Name="Employees" EntityType="Ns.Employee" />
            </EntityContainer>
            <Annotations Target="Default.Container/Employees">
                <Annotation Term="Org.OData.Capabilities.V1.SortRestrictions">
                    <Record>
                        <PropertyValue Property="Sortable" Bool="true" />
                        <PropertyValue Property="AscendingOnlyProperties">
                            <Collection />
                        </PropertyValue>
                        <PropertyValue Property="DescendingOnlyProperties">
                            <Collection />
                        </PropertyValue>
                        <PropertyValue Property="NonSortableProperties">
                            <Collection>
                                <PropertyPath>Salary</PropertyPath>
                            </Collection>
                        </PropertyValue>
                    </Record>
                </Annotation>
            </Annotations>
        </Schema>
    </edmx:DataServices>
</edmx:Edmx>
```