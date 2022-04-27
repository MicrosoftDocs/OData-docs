---
title: "Define type inheritance in ODL v6"
description: "Define type inheritance using EdmLib APIs-ODL V6"
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
---
# Define type inhertiance(ODL V6.x)
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v6.md)]

Type inheritance means **defining derived types**. EdmLib supports defining both **derived entity types** and **derived complex types**. Adding a derived entity (complex) type is almost the same as adding an normal entity (complex) except that an additional **base type** needs to be provided.

This section shows how to define entity (complex) type inheritance using EdmLib APIs. We will continue to use and extend the sample from the previous sections.

## Add a Derived Entity Type *UrgentOrder*

In the **SampleModelBuilder.cs** file, add the following code into the `SampleModelBuilder` class:

```c#
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

Then in the **Program.cs** file, insert the following code into the `Main` method:

```c#
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

- Defines a **derived entity type** `UrgentOrder` within the namespace `Sample.NS` whose base type is `Sample.NS.Order`;
- Adds a structural property `Deadline` of type `Edm.Date`;
- Adds the `Sample.NS.UrgentOrder` type to the entity data model.

## Add a Derived Complex Type *WorkAddress*

In the **SampleModelBuilder.cs** file, add the following code into the `SampleModelBuilder` class:

```c#
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

Then in the **Program.cs** file, insert the following code into the `Main` method:

```c#
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

- Defines a **derived complex type** `WorkAddress` within the namespace `Sample.NS` whose base type is `Sample.NS.Address`;
- Adds a structural property `Company` of type `Edm.String`;
- Adds the `Sample.NS.WorkAddress` type to the entity data model.

## Run the Sample

Build and run the sample. Then open the **csdl.xml** file under the **output directory**. The content of **csdl.xml** should look like the following:

![image](/odata/assets/2015-04-19-csdl.png)