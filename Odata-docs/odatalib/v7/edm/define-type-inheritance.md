---
title: " Define type inheritance-ODL V7"
description: "Define type inheritance using EdmLib APIs-ODL V7"
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
---
# Define type inheritance
**Applies To**: [!INCLUDE[appliesto-odataclient](../../../includes/appliesto-odatalib-v7.md)]

Type inheritance means defining a type by **deriving from another type**. EdmLib supports defining both **derived entity types** and **derived complex types**. Adding a derived entity (complex) type is almost identical to adding a normal entity (complex) type except that an additional **base type** needs to be specified.

This section shows how to define derived entity (complex) types using EdmLib APIs. We will use and extend the sample from the previous section.

## Add derived entity type *UrgentOrder*

In the **SampleModelBuilder.cs** file, add the following code into the `SampleModelBuilder` class:

```C#
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        private EdmEntityType _urgentOrderType;
        ...
        public SampleModelBuilder BuildUrgentOrderType()
        {
            _urgentOrderType = new EdmEntityType("Sample.NS", "UrgentOrder", _orderType);
            _urgentOrderType.AddStructuralProperty("Deadline", EdmPrimitiveTypeKind.Date);
            _model.AddElement(_urgentOrderType);
            return this;
        }
        ...
    }
}
```

Then in the **Program.cs** file, insert the following code into the `Main()` method:

```C#
namespace EdmLibSample
{
    class Program
    {
        public static void Main(string[] args)
        {
            var builder = new SampleModelBuilder();
            var model = builder
                ...
                .BuildOrderType()
#region         !!!INSERT THE CODE BELOW!!!
                .BuildUrgentOrderType()
#endregion
                ...
                .GetModel();
            WriteModelToCsdl(model, "csdl.xml");
        }
    }
}
```

This code:

- Defines the **derived entity type** `UrgentOrder` within the namespace `Sample.NS`, whose base type is `Sample.NS.Order`;
- Adds a structural property `Deadline` of type `Edm.Date`;
- Adds the derived entity type to the entity data model.

## Add derived complex type *WorkAddress*

In the **SampleModelBuilder.cs** file, add the following code into the `SampleModelBuilder` class:

```C#
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        private EdmComplexType _workAddressType;
        ...
        public SampleModelBuilder BuildWorkAddressType()
        {
            _workAddressType = new EdmComplexType("Sample.NS", "WorkAddress", _addressType);
            _workAddressType.AddStructuralProperty("Company", EdmPrimitiveTypeKind.String);
            _model.AddElement(_workAddressType);
            return this;
        }
        ...
    }
}
```

Then in the **Program.cs** file, insert the following code into the `Main()` method:

```C#
namespace EdmLibSample
{
    class Program
    {
        public static void Main(string[] args)
        {
            var builder = new SampleModelBuilder();
            var model = builder
                .BuildAddressType()
#region         !!!INSERT THE CODE BELOW!!!
                .BuildWorkAddressType()
#endregion
                ...
                .GetModel();
            WriteModelToCsdl(model, "csdl.xml");
        }
    }
}
```

This code:

- Defines the **derived complex type** `WorkAddress` within the namespace `Sample.NS`, whose base type is `Sample.NS.Address`;
- Adds a structural property `Company` of type `Edm.String`;
- Adds the derived complex type to the entity data model.

## Run the sample

Build and run the sample. Then open the file **csdl.xml** under the **output directory**. The content should look like the following:

![csdl](/odata/assets/2015-04-19-csdl.png)
