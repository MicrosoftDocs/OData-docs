---
title: "Set schema for out-of-line annotations"
description: "Set schema for out-of-line annotations"
author: kenitoinc
ms.author: kemunga
ms.date: 05/2/2023
ms.topic: article
 
---
# Use IEdmTargetPath for out-of-line annotations target
**Applies To**: [!INCLUDE[appliesto-odataclient](../../../includes/appliesto-odatalib-v7.md)]

The Edm library lets you [define annotations](/odata/odatalib/edm/define-annotations).

[This](/odata/odatalib/edm/set-annotations-schema) documentation describes how to define **out-of-line** annotations that targets a model element.

## Background

Below is a simple Edm model with an out-of-line annotation that targets a model element.

```xml
<?xml version="1.0" encoding="utf-8"?>
<edmx:Edmx Version="4.0" xmlns:edmx="http://docs.oasis-open.org/odata/ns/edmx">
    <edmx:DataServices>
        <!-- Model elements -->
        <Schema Namespace="Default" xmlns="http://docs.oasis-open.org/odata/ns/edm">
            <EntityContainer Name="Container">
                <EntitySet Name="Employees" EntityType="Ns.Employee" />
            </EntityContainer>
            <Annotations Target="Ns.Employee">
                <Annotation Term="Ns.MyTerm" String="Name OutOfLine MyTerm Value" />
            </Annotations>
        </Schema>
    </edmx:DataServices>
</edmx:Edmx>
```

`IEdmTargetPath` allows us to define out-of-line annotations that target a path to a model element.

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

Below is a simple Edm model with an out-of-line annotation that targets a path to a model element.

```xml
<?xml version="1.0" encoding="utf-8"?>
<edmx:Edmx Version="4.0" xmlns:edmx="http://docs.oasis-open.org/odata/ns/edmx">
    <edmx:DataServices>
        <!-- Model elements -->
        <Schema Namespace="Default" xmlns="http://docs.oasis-open.org/odata/ns/edm">
            <EntityContainer Name="Container">
                <EntitySet Name="Employees" EntityType="Ns.Employee" />
            </EntityContainer>
            <Annotations Target="Default.Container/Employees/Ns.Manager/EmployeeId">
                <Annotation Term="Ns.MyTerm" String="Name OutOfLine MyTerm Value" />
            </Annotations>
        </Schema>
    </edmx:DataServices>
</edmx:Edmx>
```

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

## Configure the Edm model
Add a _SampleModel.cs_ and replace the code with the following code

```csharp
using Microsoft.OData.Edm;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace AnnotationsTarget
{
    internal class SampleModel
    {
        public SampleModel()
        {
            this.Model = new EdmModel();
            this.Address = new EdmComplexType("NS", "Address");
            this.Address.AddStructuralProperty("Road", EdmCoreModel.Instance.GetString(false));
            this.Model.AddElement(this.Address);

            this.DerivedAddress = new EdmComplexType("NS", "DerivedAddress", this.Address);
            this.DerivedAddress.AddStructuralProperty("MyRoad", EdmCoreModel.Instance.GetString(false));
            this.Model.AddElement(this.DerivedAddress);

            this.Customer = new EdmEntityType("NS", "Customer");
            this.Customer.AddKeys(this.Customer.AddStructuralProperty("Id", EdmCoreModel.Instance.GetInt32(false)));
            this.Customer.AddStructuralProperty("Name", EdmPrimitiveTypeKind.String, isNullable: false);
            this.Customer.AddStructuralProperty("Address", new EdmComplexTypeReference(this.Address, false));
            this.Model.AddElement(this.Customer);

            this.VipCustomer = new EdmEntityType("NS", "VipCustomer", this.Customer);
            this.Model.AddElement(this.VipCustomer);
            this.City = new EdmEntityType("NS", "City");
            this.City.AddStructuralProperty("Name", EdmPrimitiveTypeKind.String, isNullable: false);
            this.Model.AddElement(this.City);

            this.NavUnderComplex = this.Address.AddUnidirectionalNavigation(
                new EdmNavigationPropertyInfo()
                {
                    Name = "City",
                    Target = this.City,
                    TargetMultiplicity = EdmMultiplicity.One,
                });

            this.NavUnderCustomer = this.Customer.AddUnidirectionalNavigation(
                new EdmNavigationPropertyInfo()
                {
                    Name = "Cities",
                    Target = this.City,
                    TargetMultiplicity = EdmMultiplicity.Many,
                });

            this.NameProperty = this.Customer.DeclaredProperties.Where(x => x.Name == "Name").FirstOrDefault();
            this.AddressProperty = this.Customer.DeclaredProperties.Where(x => x.Name == "Address").FirstOrDefault();

            this.Container = new EdmEntityContainer("NS", "Default");
            this.EntitySet = new EdmEntitySet(this.Container, "Customers", this.Customer);
            this.Singleton = new EdmSingleton(this.Container, "Me", this.Customer);
            this.Container.AddElement(this.EntitySet);
            this.Container.AddElement(this.Singleton);

            this.Model.AddElement(this.Container);
        }

        public EdmModel Model { get; private set; }
        public EdmEntityContainer Container { get; private set; }
        public EdmEntitySet EntitySet { get; private set; }
        public EdmSingleton Singleton { get; private set; }
        public EdmEntityType Customer { get; private set; }
        public EdmEntityType VipCustomer { get; private set; }
        public EdmEntityType City { get; private set; }
        public EdmComplexType Address { get; private set; }
        public EdmComplexType DerivedAddress { get; private set; }
        public IEdmProperty NameProperty { get; private set; }
        public IEdmProperty AddressProperty { get; private set; }
        public EdmNavigationProperty NavUnderComplex { get; private set; }
        public EdmNavigationProperty NavUnderCustomer { get; private set; }
    }
}

```

## Write out-of-line annotations

Replace the contents of _Program.cs_ file with the following code:

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
            SampleModel sampleModel = new SampleModel();
            EdmModel model = sampleModel.Model;

            // Write Annotations
            EdmTargetPath targetPath = new EdmTargetPath(sampleModel.Container, sampleModel.EntitySet, sampleModel.NameProperty);
            EdmTerm term = new EdmTerm("NS", "MyTerm", EdmCoreModel.Instance.GetString(true));
            model.AddElement(term);
            EdmVocabularyAnnotation annotation = new EdmVocabularyAnnotation(targetPath, term, new EdmStringConstant("Name OutOfLine MyTerm Value"));
            annotation.SetSerializationLocation(model, EdmVocabularyAnnotationSerializationLocation.OutOfLine);
            model.SetVocabularyAnnotation(annotation);

            WriteXml(model);
        }

        private static void WriteXml(IEdmModel model, CsdlTarget target = CsdlTarget.OData)
        {
            using (StringWriter sw = new StringWriter())
            {
                XmlWriterSettings settings = new XmlWriterSettings();
                settings.Encoding = System.Text.Encoding.UTF8;
                settings.Indent = true;

                using (XmlWriter xw = XmlWriter.Create(sw, settings))
                {
                    IEnumerable<EdmError> errors;
                    CsdlWriter.TryWriteCsdl(model, xw, target, out errors);
                    xw.Flush();
                }

                string output = sw.ToString();
                Console.Write(output);
            }
        }
    }
}
```
Run the console application.

The output on the console should be as below:

```xml
<?xml version="1.0" encoding="utf-16"?>
<edmx:Edmx Version="4.0" xmlns:edmx="http://docs.oasis-open.org/odata/ns/edmx">
  <edmx:DataServices>
    <Schema Namespace="NS" xmlns="http://docs.oasis-open.org/odata/ns/edm">
      <ComplexType Name="Address">
        <Property Name="Road" Type="Edm.String" Nullable="false" />
        <NavigationProperty Name="City" Type="NS.City" Nullable="false" />
      </ComplexType>
      <ComplexType Name="DerivedAddress" BaseType="NS.Address">
        <Property Name="MyRoad" Type="Edm.String" Nullable="false" />
      </ComplexType>
      <EntityType Name="Customer">
        <Key>
          <PropertyRef Name="Id" />
        </Key>
        <Property Name="Id" Type="Edm.Int32" Nullable="false" />
        <Property Name="Name" Type="Edm.String" Nullable="false" />
        <Property Name="Address" Type="NS.Address" Nullable="false" />
        <NavigationProperty Name="Cities" Type="Collection(NS.City)" />
      </EntityType>
      <EntityType Name="VipCustomer" BaseType="NS.Customer" />
      <EntityType Name="City">
        <Property Name="Name" Type="Edm.String" Nullable="false" />
      </EntityType>
      <Term Name="MyTerm" Type="Edm.String" />
      <EntityContainer Name="Default">
        <EntitySet Name="Customers" EntityType="NS.Customer" />
        <Singleton Name="Me" Type="NS.Customer" />
      </EntityContainer>
      <Annotations Target="NS.Default/Customers/Name">
        <Annotation Term="NS.MyTerm" String="Name OutOfLine MyTerm Value" />
      </Annotations>
    </Schema>
  </edmx:DataServices>
</edmx:Edmx>
```

## Read out-of-line annotation
Replace the contents of _Program.cs_ file with the following code:

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
            // Read Annotations
            string csdl = @"<?xml version=""1.0"" encoding=""UTF-8""?>
<edmx:Edmx xmlns:edmx=""http://docs.oasis-open.org/odata/ns/edmx"" Version=""4.0"">
	<edmx:DataServices>
		<Schema xmlns=""http://docs.oasis-open.org/odata/ns/edm"" Namespace=""NS"">
			<ComplexType Name=""Address"">
				<Property Name=""Road"" Type=""Edm.String"" Nullable=""false"" />
				<NavigationProperty Name=""City"" Type=""NS.City"" Nullable=""false"" />
			</ComplexType>
			<ComplexType Name=""DerivedAddress"" BaseType=""NS.Address"">
				<Property Name=""MyRoad"" Type=""Edm.String"" Nullable=""false"" />
			</ComplexType>
			<EntityType Name=""Customer"">
				<Key>
					<PropertyRef Name=""Id""/>
				</Key>
				<Property Name=""Id"" Type=""Edm.Int32"" Nullable=""false"" />
				<Property Name=""Name"" Type=""Edm.String"" Nullable=""false"" />
				<Property Name=""Address"" Type=""NS.Address"" Nullable=""false"" />
				<NavigationProperty Name=""Cities"" Type=""Collection(NS.City)"" />
			</EntityType>
			<EntityType Name=""VipCustomer"" BaseType=""NS.Customer"" />
			<EntityType Name=""City"">
				<Property Name=""Name"" Type=""Edm.String"" Nullable=""false"" />
			</EntityType>
			<Term Name=""MyTerm"" Type=""Edm.String"" />
			<EntityContainer Name=""Default"">
				<EntitySet Name=""Customers"" EntityType=""NS.Customer"" />
				<Singleton Name=""Me"" Type=""NS.Customer"" />
			</EntityContainer>
			<Annotations Target=""NS.Default/Customers/Name"">
				<Annotation Term=""NS.MyTerm"" String=""Name OutOfLine MyTerm Value"" />
			</Annotations>
		</Schema>
	</edmx:DataServices>
</edmx:Edmx>"
            ;

            IEdmModel parsedModel;
            IEnumerable<EdmError> errors;
            CsdlReader.TryParse(XElement.Parse(csdl).CreateReader(), out parsedModel, out errors);
            IEdmTargetPath tgtPath = parsedModel.GetTargetPath("NS.Default/Customers/Name");
            IEdmTerm fooBarTerm = parsedModel.FindDeclaredTerm("NS.MyTerm");
            string nameAnnotation = GetAnnotation(parsedModel, tgtPath, fooBarTerm, EdmVocabularyAnnotationSerializationLocation.OutOfLine);
            Console.WriteLine(nameAnnotation);
        }

        private static string GetAnnotation(IEdmModel model, IEdmVocabularyAnnotatable target, IEdmTerm term, EdmVocabularyAnnotationSerializationLocation location)
        {
            IEdmVocabularyAnnotation annotation = model.FindVocabularyAnnotations<IEdmVocabularyAnnotation>(target, term).FirstOrDefault();
            if (annotation != null)
            {
                IEdmStringConstantExpression stringConstant = annotation.Value as IEdmStringConstantExpression;
                if (stringConstant != null)
                {
                    return stringConstant.Value;
                }
            }

            return null;
        }
    }
}
```

Run the console application.

The output on the console should be as below:

`Name OutOfLine MyTerm Value`

## Read all annotations with a specific annotation target
We use the `Model.FindVocabularyAnnotations(IEdmTargetPath)` extension method.
Replace the contents of _Program.cs_ file with the following code:

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
            // Read Annotations
            string csdl = @"<?xml version=""1.0"" encoding=""UTF-8""?>
<edmx:Edmx xmlns:edmx=""http://docs.oasis-open.org/odata/ns/edmx"" Version=""4.0"">
	<edmx:DataServices>
		<Schema xmlns=""http://docs.oasis-open.org/odata/ns/edm"" Namespace=""NS"">
			<ComplexType Name=""Address"">
				<Property Name=""Road"" Type=""Edm.String"" Nullable=""false"" />
				<NavigationProperty Name=""City"" Type=""NS.City"" Nullable=""false"" />
			</ComplexType>
			<ComplexType Name=""DerivedAddress"" BaseType=""NS.Address"">
				<Property Name=""MyRoad"" Type=""Edm.String"" Nullable=""false"" />
			</ComplexType>
			<EntityType Name=""Customer"">
				<Key>
					<PropertyRef Name=""Id""/>
				</Key>
				<Property Name=""Id"" Type=""Edm.Int32"" Nullable=""false"" />
				<Property Name=""Name"" Type=""Edm.String"" Nullable=""false"" />
				<Property Name=""Address"" Type=""NS.Address"" Nullable=""false"" />
				<NavigationProperty Name=""Cities"" Type=""Collection(NS.City)"" />
			</EntityType>
			<EntityType Name=""VipCustomer"" BaseType=""NS.Customer"" />
			<EntityType Name=""City"">
				<Property Name=""Name"" Type=""Edm.String"" Nullable=""false"" />
			</EntityType>
			<Term Name=""MyTerm"" Type=""Edm.String"" />
			<Term Name=""MyTerm2"" Type=""Edm.String"" />
			<EntityContainer Name=""Default"">
				<EntitySet Name=""Customers"" EntityType=""NS.Customer"" />
				<Singleton Name=""Me"" Type=""NS.Customer"" />
			</EntityContainer>
			<Annotations Target=""NS.Default/Customers/Name"">
				<Annotation Term=""NS.MyTerm"" String=""Name OutOfLine MyTerm Value"" />
			</Annotations>
            <Annotations Target=""NS.Default/Customers/Name"">
				<Annotation Term=""NS.MyTerm2"" String=""Name OutOfLine MyTerm Value 2"" />
			</Annotations>
		</Schema>
	</edmx:DataServices>
</edmx:Edmx>"
            ;

            IEdmModel parsedModel;
            IEnumerable<EdmError> errors;
            CsdlReader.TryParse(XElement.Parse(csdl).CreateReader(), out parsedModel, out errors);
            IEdmTargetPath tgtPath = parsedModel.GetTargetPath("NS.Default/Customers/Name");
            IEdmTerm fooBarTerm = parsedModel.FindDeclaredTerm("NS.MyTerm");

            List<IEdmVocabularyAnnotation> annotations = parsedModel.FindVocabularyAnnotations(tgtPath).ToList();
            IEdmVocabularyAnnotation annotation1 = annotations[0];
            IEdmVocabularyAnnotation annotation2 = annotations[1];
            IEdmStringConstantExpression stringConstant1 = annotation1.Value as IEdmStringConstantExpression;
            IEdmStringConstantExpression stringConstant2 = annotation2.Value as IEdmStringConstantExpression;
            IEdmTargetPath targetPath1 = annotation1.Target as IEdmTargetPath;
            IEdmTargetPath targetPath2 = annotation2.Target as IEdmTargetPath;

            Console.WriteLine($"Annotations count: {annotations.Count}");
            Console.WriteLine($"Annotations target 1: {targetPath1.Path}");
            Console.WriteLine($"Annotations target 2: {targetPath2.Path}");
            Console.WriteLine($"Annotations value 1: {stringConstant1.Value}");
            Console.WriteLine($"Annotations value 2: {stringConstant2.Value}");
        }
    }
}
```

Run the console application.

The output on the console should be as below:

```
Annotations count: 2
Annotations target 1: NS.Default/Customers/Name
Annotations target 2: NS.Default/Customers/Name
Annotations value 1: Name OutOfLine MyTerm Value
Annotations value 2: Name OutOfLine MyTerm Value 2
```


