---
title: "Define operations-ODL V7"
description: "Define operations and operation imports using EdmLib APIs-ODL V7"
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Define operations
**Applies To**: [!INCLUDE[appliesto-odataclient](../../../includes/appliesto-odatalib-v7.md)]

EdmLib supports defining all types of operations (**actions** and **functions**) and operation imports (**action imports** or **function imports**). Putting aide the conceptual differences between actions and functions, the way to define them could actually be shared between actions and functions.

This section shows how to define operations and operation imports using EdmLib APIs. We will use and extend the sample from the previous section.

### Add bound action *Rate*
In the **SampleModelBuilder.cs** file, add the following code into the `SampleModelBuilder` class:

```C#
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        private EdmAction _rateAction;
        ...
        public SampleModelBuilder BuildRateAction()
        {
            _rateAction = new EdmAction("Sample.NS", "Rate",
                returnType: null, isBound: true, entitySetPathExpression: null);
            _rateAction.AddParameter("customer", new EdmEntityTypeReference(_customerType, false));
            _rateAction.AddParameter("rating", EdmCoreModel.Instance.GetInt32(false));
            _model.AddElement(_rateAction);
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
                .BuildCustomerType()
#region         !!!INSERT THE CODE BELOW!!!
                .BuildRateAction()
#endregion
                ...
                .GetModel();
            WriteModelToCsdl(model, "csdl.xml");
        }
    }
}
```

This code:

- Defines a **bound action** `Rate` within the namespace `Sample.NS`, which has **no return value**;
- Adds a **binding parameter** `customer` of type `Sample.NS.Customer`;
- Adds a parameter `rating` of type `Edm.Int32`;
- Adds the bound action to the model.

### Add an unbound function *MostExpensive*
In the **SampleModelBuilder.cs** file, add the following code into the `SampleModelBuilder` class:

```C#
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        private EdmFunction _mostExpensiveFunction;
        ...
        public SampleModelBuilder BuildMostExpensiveFunction()
        {
            _mostExpensiveFunction = new EdmFunction("Sample.NS", "MostExpensive",
                new EdmEntityTypeReference(_orderType, true), isBound: false, entitySetPathExpression: null, isComposable: true);
            _model.AddElement(_mostExpensiveFunction);
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
                .BuildRateAction()
#region         !!!INSERT THE CODE BELOW!!!
                .BuildMostExpensiveFunction()
#endregion
                ...
                .GetModel();
            WriteModelToCsdl(model, "csdl.xml");
        }
    }
}
```

This code:

- Defines an **unbound parameterless composable function** `MostExpensive` within the namespace `Sample.NS`;
- Adds the function to the model.

### Add function import *MostValuable*
In the **SampleModelBuilder.cs** file, add the following code into the `SampleModelBuilder` class:

```C#
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        private EdmFunctionImport _mostValuableFunctionImport;
        ...
        public SampleModelBuilder BuildMostValuableFunctionImport()
        {
            _mostValuableFunctionImport = _defaultContainer.AddFunctionImport("MostValuable", _mostExpensiveFunction, new EdmPathExpression("Orders"));
            return this;
        }
        ...
    }
}
```

And in the **Program.cs** file, insert the following code into the `Main()` method:

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
                .BuildVipCustomer()
#region         !!!INSERT THE CODE BELOW!!!
                .BuildMostValuableFunctionImport()
#endregion
                .GetModel();
            WriteModelToCsdl(model, "csdl.xml");
        }
    }
}
```

This code:

- Directly adds a **function import** `MostValuable` to the default container;
- Have the function import return a `Sample.NS.Order` entity from and **only from** the entity set `Orders`.
 
The `Sample.NS.MostValuable` function import is actually the `Sample.NS.MostExpensive` function exposed in the entity container with a **different name** (could be **any valid name**).

### Run the sample
Build and run the sample. Then open the file **csdl.xml** under the **output directory**. The content should look like the following:

![csdl](/odata/assets/2015-04-20-csdl.png)

