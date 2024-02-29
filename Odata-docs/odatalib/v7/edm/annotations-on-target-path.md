---
title: "Enable annotations on target path"
description: "Enable annotations on target path"
author: kenitoinc
ms.author: kemunga
ms.date: 05/2/2023
ms.topic: article
 
---
# Enable annotations on target path
**Applies To**: [!INCLUDE[appliesto-odataclient](../../../includes/appliesto-odatalib-v7.md)]

The Edm library lets you [define annotations](/odata/odatalib/edm/define-annotations). When annotations relevant to a particular model element are expressed **out-of-line/externally** in an `Annotations` element, a path expression that uniquely identifies the target element must be specified. [This](/odata/odatalib/edm/set-annotations-schema) page demonstrates how to set the target for **out-of-line** annotations.

## Introduction

In the previous versions of `Microsoft.OData.Edm` library, we could annotate model elements, however we couldn't annotate paths to a model element. Consider the follow sample metadata describing a service where a model element Employee defined in the `NS` namespace has annotations defined out-of-line in the Default namespace:

```xml
<?xml version="1.0" encoding="utf-8"?>
<edmx:Edmx Version="4.0" xmlns:edmx="http://docs.oasis-open.org/odata/ns/edmx">
    <edmx:DataServices>
        <Schema Namespace="NS" xmlns="http://docs.oasis-open.org/odata/ns/edm">
            <EntityType Name="Employee">
                <Key>
                    <PropertyRef Name="EmployeeId"/>
                </Key>
                <Property Name="EmployeeId" Type="Edm.String" Nullable="false"/>
                <Property Name="FirstName" Type="Edm.String" Nullable="false"/>
                <Property Name="LastName" Type="Edm.String" MaxLength="26"/>
            </EntityType>
            <Term Name="MyTerm" Type="Edm.String" Nullable="false" />
            <EntityContainer Name="Container">
                <EntitySet Name="Employees" EntityType="NS.Employee" />
            </EntityContainer>
            <Annotations Target="NS.Employee">
                <Annotation Term="NS.MyTerm" String="Name OutOfLine MyTerm Value" />
            </Annotations>
        </Schema>
    </edmx:DataServices>
</edmx:Edmx>
```

Take note of the **Target** attribute on the `Annotations` element. It specifies the path expression to the target model element, in this case the fully qualified name of the element comprising of the namespace and the element name.

There are scenarios where we need to annotate on a path. For example, a customer can annotate different permissions to a path.
Users could have permissions to access path `/Me/Inbox` but could not have permissions to access path `/Admin/Inbox`.

[Target Path](https://docs.oasis-open.org/odata/odata-csdl-xml/v4.01/odata-csdl-xml-v4.01.html#sec_TargetPath) is a path to a model element. The allowed path expressions are:

- The qualified name of an entity container, followed by a forward slash and the name of a container child element
- The target path of a container child followed by a forward slash and one or more forward-slash separated property, navigation property, or type-cast segments

Below are examples of paths to a model element.

```
MySchema.MyEntityContainer/MyEntitySet/MyProperty
MySchema.MyEntityContainer/MyEntitySet/MyNavigationProperty
MySchema.MyEntityContainer/MyEntitySet/MySchema.MyEntityType/MyProperty
MySchema.MyEntityContainer/MyEntitySet/MySchema.MyEntityType/MyNavProperty
MySchema.MyEntityContainer/MyEntitySet/MyComplexProperty/MyProperty
MySchema.MyEntityContainer/MyEntitySet/MyComplexProperty/MyNavigationProperty
MySchema.MyEntityContainer/MySingleton/MyComplexProperty/MyNavigationProperty
```

[IEdmTargetPath](https://github.com/OData/odata.net/blob/57572612f1ff833d395b44e6b2c498d7d0d31890/src/Microsoft.OData.Edm/Schema/Interfaces/IEdmTargetPath.cs#L15) is an interface in the `Microsoft.OData.Edm` that allows us to define a target a path.

[EdmTargetPath](https://github.com/OData/odata.net/blob/57572612f1ff833d395b44e6b2c498d7d0d31890/src/Microsoft.OData.Edm/Schema/EdmTargetPath.cs#L14) is a class that implements the `IEdmTargetPath` interface. Below is an example of how we use the `EdmTargetPath` class to define a target path.

```csharp
EdmTargetPath targetPath = new EdmTargetPath(sampleModel.Container, sampleModel.EntitySet, sampleModel.NameProperty);
```

Below is a simple Edm model with an out-of-line annotation that targets a path to a model element.

```xml
<?xml version="1.0" encoding="utf-8"?>
<edmx:Edmx Version="4.0" xmlns:edmx="http://docs.oasis-open.org/odata/ns/edmx">
    <edmx:DataServices>
        <Schema Namespace="NS" xmlns="http://docs.oasis-open.org/odata/ns/edm">
            <EntityType Name="Employee">
                <Key>
                    <PropertyRef Name="EmployeeId"/>
                </Key>
                <Property Name="EmployeeId" Type="Edm.String" Nullable="false"/>
                <Property Name="FirstName" Type="Edm.String" Nullable="false"/>
                <Property Name="LastName" Type="Edm.String" MaxLength="26"/>
            </EntityType>
            <EntityType Name="Manager" BaseType="NS.Employee">
                <Property Name="Budget" Type="Edm.Int64" Nullable="false"/>
                <NavigationProperty Name="DirectReports" Type="Collection(NS.Person)"/>
            </EntityType>
            <Term Name="MyTerm" Type="Edm.String" Nullable="false" />
            <EntityContainer Name="Container">
                <EntitySet Name="Employees" EntityType="NS.Employee" />
            </EntityContainer>
            <Annotations Target="NS.Container/Employees/NS.Manager/EmployeeId">
                <Annotation Term="NS.MyTerm" String="Name OutOfLine MyTerm Value" />
            </Annotations>
        </Schema>
    </edmx:DataServices>
</edmx:Edmx>
```

We will look into implementations of reading and writing out-of-line annotations in sections below.

## Create a .NET Core console application
Create a .NET Core console application and import the `Microsoft.OData.Edm` nuget package:

---

# [Visual Studio](#tab/visual-studio)

In the Visual Studio **Package Manager Console**:
```powershell
Install-Package Microsoft.OData.Edm
```

# [.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.OData.Edm
```

---

## Define the Edm model
In the _Program.cs_ file, add the GetEdmModel method.

```csharp
namespace AnnotationsTarget
{
    internal class Program
    {
        static void Main(string[] args)
        {
        }

        private static EdmModel GetEdmModel()
        {
            EdmModel model = new EdmModel();

            var customer = new EdmEntityType("NS", "Customer");
            customer.AddKeys(customer.AddStructuralProperty("Id", EdmCoreModel.Instance.GetInt32(false)));
            customer.AddStructuralProperty("Name", EdmPrimitiveTypeKind.String, isNullable: false);
            model.AddElement(customer);

            var container = new EdmEntityContainer("NS", "Default");
            var entitySet = new EdmEntitySet(container, "Customers", customer);
            container.AddElement(entitySet);

            model.AddElement(container);

            return model;
        }
    }
}

```

The method above creates an `IEdmModel` that we will use in writing annotations.

## Write out-of-line annotations
Add the `WriteAnnotations` method to the _Program.cs_ file.

This is what the `WriteAnnotations` method does:
- Call GetEdmModel method to create an `EdmModel`
- Create a target path
- Define a `WritePermission` Edm term
- Create an annotation with the target path
- Write the xml metadata to the console

We are setting the `Write permission` to the path `NS.Default/Customers/Name` as `false`.

```csharp
using Microsoft.OData.Edm.Csdl;
using Microsoft.OData.Edm.Vocabularies;
using Microsoft.OData.Edm;
using System.Reflection;
using System.ComponentModel;
using Microsoft.OData.Edm.Validation;
using System.Xml;
using static System.Runtime.InteropServices.JavaScript.JSType;
using System.Xml.Linq;

namespace AnnotationsTarget
{
    internal class Program
    {
        static void Main(string[] args)
        {
            WriteAnnotations();
        }

        // Other existing method(s)

        internal static void WriteAnnotations()
        {
            EdmModel model = GetEdmModel();
            var container = model.EntityContainer;
            var entitySet = model.FindDeclaredEntitySet("Customers");
            var customer = model.FindDeclaredType("NS.Customer") as IEdmStructuredType;
            var nameProperty = customer.DeclaredProperties.Where(x => x.Name == "Name").FirstOrDefault();

            EdmTargetPath targetPath = new EdmTargetPath(container, entitySet, nameProperty);
            EdmTerm term = new EdmTerm("NS", "WritePermission", EdmCoreModel.Instance.GetBoolean(false));
            model.AddElement(term);
            EdmVocabularyAnnotation annotation = new EdmVocabularyAnnotation(targetPath, term, new EdmStringConstant("Write Permission on Customer Name."));
            annotation.SetSerializationLocation(model, EdmVocabularyAnnotationSerializationLocation.OutOfLine);
            model.SetVocabularyAnnotation(annotation);

            using (StringWriter sw = new StringWriter())
            {
                XmlWriterSettings settings = new XmlWriterSettings();
                settings.Encoding = System.Text.Encoding.UTF8;
                settings.Indent = true;

                using (XmlWriter xw = XmlWriter.Create(sw, settings))
                {
                    IEnumerable<EdmError> errors;
                    CsdlWriter.TryWriteCsdl(model, xw, CsdlTarget.OData, out errors);
                    xw.Flush();
                }

                string output = sw.ToString();
                Console.Write(output);
            }
        }
    }
}
```

The output on the console should be as below:

```xml
<?xml version="1.0" encoding="utf-16"?>
<edmx:Edmx Version="4.0" xmlns:edmx="http://docs.oasis-open.org/odata/ns/edmx">
  <edmx:DataServices>
    <Schema Namespace="NS" xmlns="http://docs.oasis-open.org/odata/ns/edm">
      <EntityType Name="Customer">
        <Key>
          <PropertyRef Name="Id" />
        </Key>
        <Property Name="Id" Type="Edm.Int32" Nullable="false" />
        <Property Name="Name" Type="Edm.String" Nullable="false" />
      </EntityType>
      <Term Name="WritePermission" Type="Edm.Boolean" Nullable="false" />
      <EntityContainer Name="Default">
        <EntitySet Name="Customers" EntityType="NS.Customer" />
      </EntityContainer>
      <Annotations Target="NS.Default/Customers/Name">
        <Annotation Term="NS.WritePermission" Bool="false" />
      </Annotations>
    </Schema>
  </edmx:DataServices>
</edmx:Edmx>
```

## Read out-of-line annotation
Add the `ReadAnnotations` method to the _Program.cs_ file.

The `ReadAnnotations` method does the following
- Parse the metadata to an edm model
- Use the model's `GetTargetPath` extension method to get the target path
- Use the model's `FindDeclaredTerm` extension method to get the edm term
- Use the model's `FindVocabularyAnnotations(IEdmTargetPath, IEdmTerm)` extension method to get the annotation
- Write the annotation value to console


```csharp
namespace AnnotationsTarget
{
    internal class Program
    {
        static void Main(string[] args)
        {
            // Other codes
            ReadAnnotations();
        }

        // Other methods

        internal static void ReadAnnotations()
        {
            string csdl = @"<edmx:Edmx Version=""4.0"" xmlns:edmx=""http://docs.oasis-open.org/odata/ns/edmx"">
  <edmx:DataServices>
    <Schema Namespace=""NS"" xmlns=""http://docs.oasis-open.org/odata/ns/edm"">
      <EntityType Name=""Customer"">
        <Key>
          <PropertyRef Name=""Id"" />
        </Key>
        <Property Name=""Id"" Type=""Edm.Int32"" Nullable=""false"" />
        <Property Name=""Name"" Type=""Edm.String"" Nullable=""false"" />
      </EntityType>
      <Term Name=""WritePermission"" Type=""Edm.Boolean"" Nullable=""false"" />
      <Term Name=""DeletePermission"" Type=""Edm.Boolean"" Nullable=""false"" />
      <EntityContainer Name=""Default"">
        <EntitySet Name=""Customers"" EntityType=""NS.Customer"" />
      </EntityContainer>
      <Annotations Target=""NS.Default/Customers/Name"">
        <Annotation Term=""NS.WritePermission"" Bool=""false"" />
        <Annotation Term=""NS.DeletePermission"" Bool=""false"" />
      </Annotations>
    </Schema>
  </edmx:DataServices>
</edmx:Edmx>";

            IEdmModel model;
            IEnumerable<EdmError> errors;
            CsdlReader.TryParse(XElement.Parse(csdl).CreateReader(), out model, out errors);
            IEdmTargetPath targetPath = model.GetTargetPath("NS.Default/Customers/Name");
            IEdmTerm deletePermissionTerm = model.FindDeclaredTerm("NS.DeletePermission");

            // Get annotation for a specific target path and term.
            IEdmVocabularyAnnotation annotation = model.FindVocabularyAnnotations<IEdmVocabularyAnnotation>(targetPath, deletePermissionTerm).FirstOrDefault();
            if (annotation != null)
            {
                IEdmBooleanConstantExpression boolConstant = annotation.Value as IEdmBooleanConstantExpression;
                if (boolConstant != null)
                {
                    Console.WriteLine(boolConstant.Value);
                }
            }
        }
    }
}
```

The output on the console should be as below:

`False`

## Read all annotations with a specific annotation target
We use the `Model.FindVocabularyAnnotations(IEdmTargetPath)` extension method to get all the annotations that target a specific target path

Add the following contents to the `ReadAnnotations` method:

```csharp

namespace AnnotationsTarget
{
    internal class Program
    {
        static void Main(string[] args)
        {
            // Other codes
        }

        // Other methods

        internal static void ReadAnnotations()
        {
            // Other codes

            // Get all annotations for a certain target path.
            List<IEdmVocabularyAnnotation> annotations = model.FindVocabularyAnnotations(targetPath).ToList();
            IEdmVocabularyAnnotation annotation1 = annotations[0];
            IEdmVocabularyAnnotation annotation2 = annotations[1];
            IEdmBooleanConstantExpression boolConstant1 = annotation1.Value as IEdmBooleanConstantExpression;
            IEdmBooleanConstantExpression boolConstant2 = annotation2.Value as IEdmBooleanConstantExpression;
            IEdmTargetPath targetPath1 = annotation1.Target as IEdmTargetPath;
            IEdmTargetPath targetPath2 = annotation2.Target as IEdmTargetPath;

            Console.WriteLine($"Annotations count: {annotations.Count}");
            Console.WriteLine($"Annotations target 1: {targetPath1.Path}");
            Console.WriteLine($"Annotations target 2: {targetPath2.Path}");
            Console.WriteLine($"Annotations value 1: {boolConstant1.Value}");
            Console.WriteLine($"Annotations value 2: {boolConstant2.Value}");
        }
    }
}
```

The output on the console should be as below:

```
Delete Permission on Customer Name.
Annotations count: 2
Annotations target 1: NS.Default/Customers/Name
Annotations target 2: NS.Default/Customers/Name
Annotations value 1: False
Annotations value 2: False
```
