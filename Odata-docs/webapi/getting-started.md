---
title:  "1.2 Write a simple OData V4 service"
date:   2015-03-30 16:54:10

ms.date: 04/01/2015
---
# Write a simple OData V4 service
**Applies To**: [!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)]
[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Let's get started by creating a simple OData V4 service. It has one entity set `Products`, one entity type `Product`. `Product` has two properties `ID` and `Name`, with `ID` being an integer and `Name` being a string. The service is read only. The only data clients can get besides the service document and metadata document, is the `Products` entity set.

### a. Create the Visual Studio project

In Visual Studio, create a new C# project from the **ASP.NET Web Application** template. Name the project "ODataService".

![](https://i1.asp.net/media/4929282/odata01.PNG?cdn_id=2015-02-04-001)

In the **New Project** dialog, select the **Empty** template. Under "Add folders and core references...", click **Web API**. Click **OK**.

![](https://i3.asp.net/media/4929288/odata02.PNG?cdn_id=2015-02-04-001)

### b. Install the OData packages

In the NuGet Package Manager, install `Microsoft.AspNet.OData` and all it's dependencies.

### c. Add a model class

Add a C# class to the **Models** folder:

```C#
namespace ODataService.Models
{
    public class Product
    {
        public int ID { get; set; }
        public string Name { get; set; }
    }
}
```

### d. Add a controller class

Add a C# class to the **Controllers** folder:

```C#
namespace ODataService.Controllers
{
    public class ProductsController : ODataController
    {
        private List<Product> products = new List<Product>()
        {
            new Product()
            {
                ID = 1,
                Name = "Bread",
            }
        };

        public List<Product> Get()
        {
            return products;
        }
    }
}
```

In the controller, we defined a `List<Product>` object which has one product element. It's considered as a in-memory storage of the data of the OData service.

We also defined a `Get` method that returns the list of products. The method refers to the handling of HTTP GET requests. We'll cover that in the sections about routing.

### e. Configure the OData Endpoint

Open the file App_Start/WebApiConfig.cs. Replace the existing `Register` method with the following code:

```C#
public static void Register(HttpConfiguration config)
{
    var builder = new ODataConventionModelBuilder();

    builder.EntitySet<Product>("Products");

    config.MapODataServiceRoute("ODataRoute", null, builder.GetEdmModel());
}
```

### f. Start the OData service

Start the OData service by running the project and open a browser to consume it. You should be able to get access to the service document at `https://host/service/` in which `https://host/service/` is the root path of your service. The metadata document can be accessed at `GET https://host/service/$metadata` and the products at `GET https://host/service/Products`.