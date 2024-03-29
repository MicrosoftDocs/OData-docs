---
title : 7.0 Beta for ASP.NET and ASP.NET Core
description: "7.0 Beta for ASP.NET and ASP.NET Core"
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
---
# WebAPI 7.0

**Web API OData Beta1, Beta2 & Beta4** includes a new package for [WebApi OData V7.0.0 for ASP.NET Core 2.x](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/).
The nightly version of this package is available from this [nightly url](https://www.myget.org/F/webapinetcore/api/v3/index.json).

There is also a new [WebApi OData V7.0.0 for ASP.NET Framework](https://www.nuget.org/packages/Microsoft.AspNet.OData/7.0.0-beta4). 
The nightly version of this package is available from this [nightly url](https://www.myget.org/F/webapinetcore/api/v3/index.json). 
The APIs are the same as those in WebApi OData V6.x, except for the new namespace `Microsoft.AspNet.OData` for V7, which is changed from `System.Web.OData`.

Both packages depends on [OData Lib 7.0.0](https://www.nuget.org/packages/Microsoft.OData.Core/7.0.0).

The code for the packages can be found [here](https://github.com/OData/WebApi/tree/master)

### Known Issues

<!-- ASP.NET Core Beta1 is ancient, can we delete?
**Web API OData for ASP.NET Core Beta1**, has following limitations which are known issues:
* Batching is not fully supported
* Using EnableQuery in an HTTP route, i.e. non-OData route, is not fully functional
* [#1175](https://github.com/OData/WebApi/issues/1175) - When you first start your service under a debugger, the project app URL will
  likely make a request on a non-OData route. This will fail with an exception `Value cannot be null. Parameter name: routeName`. You
   can work around this issue by adding `routes.EnableDependencyInjection();` in `UseMvc()` lambda in `Configure`. You can configure
   the default startup request in **Project properties**, **Debug**, **App URL**.
-->

**Web API OData for ASP.NET**, there are no known issues.

### OData V7.0.0 for ASP.NET Core 2.x

The new OData V7.0.0 for ASP.NET Core 2.x package supports the same features set as Web API OData V6.0.0 but works with ASP.NET Core.
You can learn more about ASP.NET Core from the [documentation](/aspnet/core/).

To get started with OData V7.0.0 for ASP.NET Core 2.x, you can use code that is very similar to Web API OData V6.0.0. All of the
documentation in [Writing a simple OData V4 service](/odata/webapi/getting-started) is correct except for
configuring the OData endpoint. Instead of using the Register() method, you'll follow the new [service + route configuration model](/aspnet/core/fundamentals/startup)
used in ASP.NET Core.

The namespace for both Web API OData packages is Microsoft.AspNet.OData.

#### a. Create the Visual Studio project

In Visual Studio 2017, create a new C# project from the **ASP.NET Core Web Application** template. Name the project "ODataService".

In the New Project dialog, select **ASP.NET Core 2.0** and select the **WebApi** template. Click **OK**.

#### b. Install the OData packages

In the Nuget Package Manager, install `Microsoft.AspNetCore.OData` and all its dependencies.

#### c. Add a model class

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

#### d. Add a controller class

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

        [EnableQuery]
        public List<Product> Get()
        {
            return products;
        }
    }
}
```

In the controller, we defined a `List<Product>` object which has one product element. It's considered as an in-memory storage
of the data of the OData service.

We also defined a `Get` method that returns the list of products. The method refers to the handling of HTTP GET requests. We'll
cover that in the sections about routing.

This `Get` method is decorated with `EnableQueryAttribute`, which in turns supports OData query options, for example `$expand, $filter` etc.

#### e. Configure the OData Endpoint

Open the file Startup.cs. Replace the existing `ConfigureServices` and `Configure` methods with the
following code:

```C#
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.AddOData();
}

public void Configure(IApplicationBuilder app)
{
    var builder = new ODataConventionModelBuilder(app.ApplicationServices);

    builder.EntitySet<Product>("Products");

    app.UseMvc(routeBuilder =>
    {
        // and this line to enable OData query option, for example $filter
        routeBuilder.Select().Expand().Filter().OrderBy().MaxTop(100).Count();

        routeBuilder.MapODataServiceRoute("ODataRoute", "odata", builder.GetEdmModel());

        // uncomment the following line to Work-around for #1175 in beta1
        // routeBuilder.EnableDependencyInjection();
    });
}
```

#### f. Start the OData service

Start the OData service by running the project and open a browser to consume it. You should be able to get access to the service
document at `https://host/odata/` in which `https://host/odata/` is the root path of your service. The metadata document
can be accessed at `GET https://host:port/odata/$metadata` and the products at `GET https://host:port/odata/Products` where
`host:port` is the host and port of your service, usually something like `localhost:1234`.

#### g. Explore

As mentioned earlier, most of the samples for Web API OData V6.0.0 apply to Web API OData V7.0.0. One of the design goals was to keep
the API between the two as similar as possible. While the APIs are similar, they are not identical due to differences between
ASP.NET and ASP.NET Core, such as HttpRequestMessage is now HttpRequest.

##### ODataRoutingConvention
Both `Microsoft.AspNet.OData` and `Microsoft.AspNetCore.OData` packages support same set of OData routing conventions, including default built-in routing conventions and attribute rounting convention, so that each request can be routed to matching controller for processing. All routing conventions implement the interface `IODataRoutingConvention`, however, with different definitions for the two packages due to different route matching implementations based on ASP.NET Framework and ASP.NET Core:
- For ASP.NET Framework:
```C#
namespace Microsoft.AspNet.OData.Routing.Conventions
{
    /// <summary>
    /// Provides an abstraction for selecting a controller and an action for OData requests.
    /// </summary>
    public interface IODataRoutingConvention
    {
        /// <summary>
        /// Selects the controller for OData requests.
        /// </summary>
        string SelectController(ODataPath odataPath, HttpRequestMessage request);

        /// <summary>
        /// Selects the action for OData requests.
        /// </summary>
        string SelectAction(ODataPath odataPath, HttpControllerContext controllerContext, ILookup<string, HttpActionDescriptor> actionMap);
    }
}
```

- For ASP.NET Core 2.x:
  
```C#
namespace Microsoft.AspNet.OData.Routing.Conventions
{
    /// <summary>
    /// Provides an abstraction for selecting a controller and an action for OData requests.
    /// </summary>
    public interface IODataRoutingConvention
    {
        /// <summary>
        /// Selects the controller and action for OData requests.
        /// </summary>        
        IEnumerable<ControllerActionDescriptor> SelectAction(RouteContext routeContext);
    }
}
```

Specific routing convention, e.g. `MetadataRoutingConvention`, typically implements the package-specific interface, provides package-specific implementation, and shares common logic for both platform in the `Microsoft.AspNet.OData.Shared` shared project.
