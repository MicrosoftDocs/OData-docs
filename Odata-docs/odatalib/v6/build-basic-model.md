---
title: " Build a basic model ODL V6"
description: "Build a basic entity data model using EdmLib APIs - ODL.v6"
author: madansr7
ms.author: madansr7
ms.date: 02/19/2019
ms.topic: article
 
---
# Build a basic model (ODL V6.x)
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v6.md)]

The *EDM* (Entity Data Model) library (*abbr*. EdmLib) primarily contains APIs to **build** an entity data model that conforms to *CSDL* (Common Schema Definition Language) as well as APIs to **read** (or **write**) an entity data model **from** (or **to**) a CSDL document.

This section shows how to build a basic entity data model using EdmLib APIs.

## Software Versions Used in the Tutorial

 - [Visual Studio 2013 Update 4](https://go.microsoft.com/fwlink/?LinkId=517284)
 - [Microsoft.OData.Edm 6.11.0](https://www.nuget.org/packages/Microsoft.OData.Edm)
 - .NET 4.5

## Create the Visual Studio Project

In Visual Studio, from the **File** menu, select **New > Project**.

Expand **Installed > Templates > Visual C# > Windows Desktop**, and select the **Console Application** template. Name the project **EdmLibSample**. Click **OK**.

![image](/odata/assets/2015-04-16-new-project.png)

## Install the EdmLib Package

From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**. In the Package Manager Console window, type:

```csharp
Install-Package Microsoft.OData.Edm
```

This command configures the solution to enable NuGet restore and installs the latest EdmLib package.

## Add the SampleModelBuilder Class

The `SampleModelBuilder` class is used to build and return an entity data model instance at runtime.

In Solution Explorer, right-click the project **EdmLibSample**. From the context menu, select **Add > Class**. Name the class **SampleModelBuilder**.

![image](/odata/assets/2015-04-16-add-class.png)

In the **SampleModelBuilder.cs** file, add the following `using` clauses to introduce the EDM definitions:

``` csharp
using Microsoft.OData.Edm;
using Microsoft.OData.Edm.Library;
```

Then replace the boilerplate code with the following:

``` csharp
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        private readonly EdmModel _model = new EdmModel();
        public IEdmModel GetModel()
        {
            return _model;
        }
    }
}
```

## Add a Complex Type *Address*

In the **SampleModelBuilder.cs** file, add the following code into the `SampleModelBuilder` class:

``` csharp
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        private EdmComplexType _addressType;
        ...
        public SampleModelBuilder BuildAddressType()
        {
            _addressType = new EdmComplexType("Sample.NS", "Address");
            _addressType.AddStructuralProperty("Street", EdmPrimitiveTypeKind.String);
            _addressType.AddStructuralProperty("City", EdmPrimitiveTypeKind.String);
            _addressType.AddStructuralProperty("PostalCode", EdmPrimitiveTypeKind.Int32);
            _model.AddElement(_addressType);
            return this;
        }
        ...
    }
}
```

This code:

- Defines a **keyless** complex type `Address` within the namespace `Sample.NS`;
- Adds three structural properties `Street`, `City` and `PostalCode`;
- Adds the `Sample.NS.Address` type to the model.

## Add an Enumeration Type *Category*

In the **SampleModelBuilder.cs** file, add the following code into the `SampleModelBuilder` class:

``` csharp
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        private EdmEnumType _categoryType;
        ...
        public SampleModelBuilder BuildCategoryType()
        {
            _categoryType = new EdmEnumType("Sample.NS", "Category", EdmPrimitiveTypeKind.Int64, isFlags: true);
            _categoryType.AddMember("Books", new EdmIntegerConstant(1L));
            _categoryType.AddMember("Dresses", new EdmIntegerConstant(2L));
            _categoryType.AddMember("Sports", new EdmIntegerConstant(4L));
            _model.AddElement(_categoryType);
            return this;
        }
        ...
    }
}
```

This code:

- Defines an enumeration type `Category` based on `Edm.Int64` within the namespace `Sample.NS`;
- Sets the attribute `IsFlags` to `true` so multiple members can be selected simultaneously;
- Adds three enumeration members `Books`, `Dresses` and `Sports`;
- Adds the `Sample.NS.Category` type to the model.

## Add an Entity Type *Customer*

In the **SampleModelBuilder.cs** file, add the following code into the `SampleModelBuilder` class:

``` csharp
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        private EdmEntityType _customerType;
        ...
        public SampleModelBuilder BuildCustomerType()
        {
            _customerType = new EdmEntityType("Sample.NS", "Customer");
            _customerType.AddKeys(_customerType.AddStructuralProperty("Id", EdmPrimitiveTypeKind.Int32, isNullable: false));
            _customerType.AddStructuralProperty("Name", EdmPrimitiveTypeKind.String, isNullable: false);
            _customerType.AddStructuralProperty("Credits",
                new EdmCollectionTypeReference(new EdmCollectionType(EdmCoreModel.Instance.GetInt64(isNullable: true))));
            _customerType.AddStructuralProperty("Interests", new EdmEnumTypeReference(_categoryType, isNullable: true));
            _customerType.AddStructuralProperty("Address", new EdmComplexTypeReference(_addressType, isNullable: false));
            _model.AddElement(_customerType);
            return this;
        }
        ...
    }
}
```

This code:

- Defines an entity type `Customer` within the namespace `Sample.NS`;
- Adds a **non-nullable** property `Id` as the key of the entity type;
- Adds a **non-nullable** property `Name`;
- Adds a property `Credits` of the type `Collection(Edm.Int64)`;
- Adds a **nullable** property `Interests` of the type `Sample.NS.Category`;
- Adds a **non-nullable** property `Address` of the type `Sample.NS.Address`;
- Adds the `Sample.NS.Customer` type to the model.

## Add the Default Entity Container

In the **SampleModelBuilder.cs** file, add the following code into the `SampleModelBuilder` class:

``` csharp
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        private EdmEntityContainer _defaultContainer;
        ...
        public SampleModelBuilder BuildDefaultContainer()
        {
            _defaultContainer = new EdmEntityContainer("Sample.NS", "DefaultContainer");
            _model.AddElement(_defaultContainer);
            return this;
        }
        ...
    }
}
```

This code:

- Defines an entity container `DefaultContainer` of the namespace `Sample.NS`;
- Adds the container to the model.
 
[!Note] that each model **MUST** define exactly one entity container (*aka*. the `DefaultContainer`) which can be referenced later by the `_model.EntityContainer` property.

## Add an Entity Set *Customers*

In the **SampleModelBuilder.cs** file, add the following code into the `SampleModelBuilder` class:

``` csharp
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        private EdmEntitySet _customerSet;
        ...
        public SampleModelBuilder BuildCustomerSet()
        {
            _customerSet = _defaultContainer.AddEntitySet("Customers", _customerType);
            return this;
        }
        ...
    }
}
```

This code directly adds a new entity set `Customers` to the default container.

## Write the Model to a CSDL Document

Congratulations! You now have a working entity data model! In order to show the model in an intuitive way, we would write it to a CSDL document.

In the **Program.cs** file, add the following `using` clauses:

``` csharp
using System.Collections.Generic;
using System.IO;
using System.Xml;
using Microsoft.OData.Edm;
using Microsoft.OData.Edm.Csdl;
using Microsoft.OData.Edm.Validation;
```

Then replace the boilerplate `Program` class with the following:

``` csharp
namespace EdmLibSample
{
    class Program
    {
        public static void Main(string[] args)
        {
            var builder = new SampleModelBuilder();
            var model = builder
                .BuildAddressType()
                .BuildCategoryType()
                .BuildCustomerType()
                .BuildDefaultContainer()
                .BuildCustomerSet()
                .GetModel();
            WriteModelToCsdl(model, "csdl.xml");
        }
        private static void WriteModelToCsdl(IEdmModel model, string fileName)
        {
            using (var writer = XmlWriter.Create(fileName))
            {
                IEnumerable<EdmError> errors;
                model.TryWriteCsdl(writer, out errors);
            }
        }
    }
}
```

For now, there is no need to understand how the model is being written as CSDL. The details will be explained in the following section.

## Run the Sample

From the **DEBUG** menu, click **Start Debugging** to build and run the sample. The console window should appear and then disappear in a flash.

![image](/odata/assets/2015-04-17-debug.png)

Open the **csdl.xml** file under the **output directory** with Internet Explorer (or other XML viewer if you like). The content should look similar to the following:

![image](/odata/assets/2015-04-17-csdl.png)

As you can see, the document contains all the elements we have built so far.

## References

[Use Enumeration types in OData](https://blogs.msdn.com/b/odatateam/archive/2014/03/18/use-enumeration-types-in-odata.aspx)