---
title: " Define singleton"
description: "Define singleton using EdmLib APIs"

author: madansr7
ms.author: madansr7
ms.date: 7/1/2019
ms.topic: article
 
---
# Define singleton(ODL V6.x)
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v6.md)]

Defining a singleton in the entity container shares the same simple way as defining an entity set.

This section shows how to define singletons using EdmLib APIs. We will continue to use and extend the sample from the previous sections.

### Add a Singleton *VipCustomer*
In the **SampleModelBuilder.cs** file, add the following code into the `SampleModelBuilder` class:

``` csharp
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        private EdmSingleton _vipCustomer;
        ...
        public SampleModelBuilder BuildVipCustomer()
        {
            _vipCustomer = _defaultContainer.AddSingleton("VipCustomer", _customerType);
            return this;
        }
        ...
    }
}
```

This code directly adds a new singleton `VipCustomer` to the default container.

In the **Program.cs** file, insert the following code into the `Main` method:

``` csharp
namespace EdmLibSample
{
    class Program
    {
        public static void Main(string[] args)
        {
            var builder = new SampleModelBuilder();
            var model = builder
                ...
                .BuildCustomerSet()
#region         !!!INSERT THE CODE BELOW!!!
                .BuildVipCustomer()
#endregion
                .GetModel();
            WriteModelToCsdl(model, "csdl.xml");
        }
    }
}
```

### Run the Sample
Build and run the sample. Then open the **csdl.xml** file under the **output directory**. The content of **csdl.xml** should look like the following:

![image](/odata/assets/2015-04-18-csdl1.png)

### References
[[Tutorial & Sample] Use Singleton to define your special entity](https://devblogs.microsoft.com/odata/tutorial-sample-use-singleton-to-define-your-special-entity/).