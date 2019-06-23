---
title: "OData WebApi changelog"
description: "OData WebApi 7.x changelog"
author: madansr7
ms.author: madansr7
ms.date: 02/19/2019
ms.topic: article
---

# What's new in WebApi lib

## WebAPI 7.0

**Web API OData Beta1, Beta2 & Beta4** includes a new package for [WebApi OData V7.0.0 for ASP.NET Core 2.x](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/).
The nightly version of this package is available from this [nightly url](https://www.myget.org/F/webapinetcore/api/v3/index.json).

There is also a new [WebApi OData V7.0.0 for ASP.NET Framework](https://www.nuget.org/packages/Microsoft.AspNet.OData/7.0.0-beta4). 
The nightly version of this package is available from this [nightly url](https://www.myget.org/F/webapinetcore/api/v3/index.json). 
The APIs are the same as those in WebApi OData V6.x, except for the new namespace `Microsoft.AspNet.OData` for V7, which is changed from `System.Web.OData`.

Both packages depends on [OData Lib 7.0.0](https://www.nuget.org/packages/Microsoft.OData.Core/7.0.0).

The code for the packages can be found [here](https://github.com/OData/WebApi/tree/master)

### Known Issues

**Web API OData for ASP.NET Core Beta1**, has following limitations which are known issues:
* Batching is not fully supported
* Using EnableQuery in an HTTP route, i.e. non-OData route, is not fully functional
* [#1175](https://github.com/OData/WebApi/issues/1175) - When you first start your service under a debugger, the project app URL will
  likely make a request on a non-OData route. This will fail with an exception `Value cannot be null. Parameter name: routeName`. You
   can work around this issue by adding `routes.EnableDependencyInjection();` in `UseMvc()` lambda in `Configure`. You can configure
   the default startup request in **Project properties**, **Debug**, **App URL**.

**Web API OData for ASP.NET**, there are no known issues.

### OData V7.0.0 for ASP.NET Core 2.x

The new OData V7.0.0 for ASP.NET Core 2.x package supports the same features set as Web API OData V6.0.0 but works with ASP.NET Core.
You can learn more about ASP.NET Core from the [documentation](https://docs.microsoft.com/en-us/aspnet/core/).

To get started with OData V7.0.0 for ASP.NET Core 2.x, you can use code that is very similar to Web API OData V6.0.0. All of the
documentation in [Writing a simple OData V4 service](/odata/webapi/getting-started) is correct except for
configuring the OData endpoint. Instead of using the Register() method, you'll follow the new [service + route configuration model](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/startup)
used in ASP.NET Core.

The namespace for both Web API OData packages is Microsoft.AspNet.OData.

#### a. Create the Visual Studio project

In Visual Studio 2017, create a new C# project from the **ASP.NET Core Web Application** template. Name the project "ODataService".

In the New Project dialog, select **ASP.NET Core 2.0** and select the **WebApi** template. Click **OK**.

#### b. Install the OData packages

In the Nuget Package Manager, install `Microsoft.AspNetCore.OData` and all it's dependencies.

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
Both `Microsoft.AspNet.OData` and `Microsoft.AspNetCore.OData` packages support same set of OData routing conventions, including default built-in routing conventions and attribute routing convention, so that each request can be routed to matching controller for processing. All routing conventions implement the interface `IODataRoutingConvention`, however, with different definitions, as ``` csharp below, for the two packages due to different route matching implementations based on ASP.NET Framework and ASP.NET Core:

For ASP.NET Framework:

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

For ASP.NET Core 2.x:
  
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

Specific routing convention, e.g. MetadataRoutingConvention, typically implements the package-specific interface, provides package-specific implementation, and shares common logic for both platform in the `Microsoft.AspNet.OData.Shared` shared project.
## WebAPI 7.0 (.NET Core and .NET Classic)

We're happy to announce the release of ASP.NET Web API OData 7.0 on the [NuGet gallery](https://www.nuget.org/)!

ASP.NET Web API OData 7.0 is available in two packages:

1. [Microsoft.AspNetCore.OData](https://www.nuget.org/packages/Microsoft.AspNetCore.OData) is based on ASP.NET Core 2.0.
2. [Microsoft.AspNet.OData](https://www.nuget.org/packages/Microsoft.AspNet.OData) is based on ASP.NET Web API.

[Get started](https://github.com/OData/ODataSamples/tree/master/WebApi/v7.x) with ASP.NET Web API OData 7.0 today!

### Download this release

You can install or update the NuGet packages for OData Web API v7.0.0 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```powershell
PM> Install-Package Microsoft.AspNetCore.OData
```
or
```powershell
PM> Install-Package Microsoft.AspNet.OData
```


### ASP.NET Core 2.0 support

 See main issues from:

 * [ [Web API issue #939](https://github.com/OData/WebApi/issues/939) ] .NET Core Support.
  
 * [ [Web API issue #772](https://github.com/OData/WebApi/issues/772) ] Roadmap for OData WebAPI run on ASP.NET Core.
  
 * [ [Web API issue #229](https://github.com/OData/WebApi/issues/229) ] Port ASP.NET Web API OData to ASP.NET 5/MVC 6.
  
 Getting started ASP.NET Core OData from [here](https://github.com/OData/WebApi/blob/gh-pages/_posts/2017-12-21-14-01-netcore-beta1.md), get samples from [here](https://github.com/OData/ODataSamples/tree/master/WebApi/v7.x).

### New features
 
 * [ [PR #1497](https://github.com/OData/WebApi/issues/1497) ] Support `In` operator.
  
 * [ [PR #1409](https://github.com/OData/WebApi/pull/1409) ] Set default to Unqualified-function/action call and case insensitive.

 * [ [PR #1393](https://github.com/OData/WebApi/pull/1393) ] Set default to enable KeyAsSegment.
 
 * [ [Issue #1503](https://github.com/OData/WebApi/issues/1503) ] Enable support for Json Batch.
 
 * [ [Issue #1386](https://github.com/OData/WebApi/issues/1386) ] Use TemplateMatcher for ODataBatchPathMapping.
  
 * [ [Issue #1248](https://github.com/OData/WebApi/issues/1248) ] Allow recursive complex types in OData model builder.
 
 * [ [Issue #1225](https://github.com/OData/WebApi/issues/1225) ] Support optional dollar sign.
 
 * [ [Issue #893](https://github.com/OData/WebApi/issues/893) ] Support aggregation of Entity Set.

### Breaking changes
 
 * Both [Microsoft.AspNetCore.OData](https://www.nuget.org/packages/Microsoft.AspNetCore.OData) and [Microsoft.AspNet.OData](https://www.nuget.org/packages/Microsoft.AspNet.OData) are using "**Microsoft.AspNet.OData**" namespace.
 
 * [ [PR #1489](https://github.com/OData/WebApi/pull/1489) ] Change the OptionalReturn to ReturnNullable in OperationConfiguration.
  
 * [ [PR #1353](https://github.com/OData/WebApi/pull/1353) ] Change OptionalParameter as Nullable .

### Improvements & Fixes

* [ [Issue #1510](https://github.com/OData/WebApi/issues/1510) ] move $top & $skip execute as late as possible.
 
* [ [Issue #1489](https://github.com/OData/WebApi/issues/1489) ] Return Task<> from method of controller doesn't pick up OData output formatter (Core).
 
* [ [Issue #1407](https://github.com/OData/WebApi/issues/1407) ] Fix the BaseAddressFactory in ODataMediaTypeFormatter .
  
* [ [Issue #1471](https://github.com/OData/WebApi/issues/1471) ] Issue in non-odata routing with DataContact & DataMember.
 
* [ [Issue #1434](https://github.com/OData/WebApi/issues/1434) ] Add OData-Version into the No-Content response header.

* [ [Issue #1398](https://github.com/OData/WebApi/issues/1398) ] Expose underlying semantic OData path.

* [ [Issue #1388](https://github.com/OData/WebApi/issues/1388) ] Make Match as virtual in ODataPathRouteConstraint.
 
* [ [Issue #1387](https://github.com/OData/WebApi/issues/1387) ] Move Instance to select all.

* [ [Issue #1313](https://github.com/OData/WebApi/issues/1313) ] Batch requests are incorrectly routed when ASP.NET Core OData web application has any BasePath.

* [ [Issue #1263](https://github.com/OData/WebApi/issues/1263) ] Patch nested structural resources.
 
* [ [Issue #1247](https://github.com/OData/WebApi/issues/1247) ] Fix Spatial post/update problem.
 
* [ [Issue #1113](https://github.com/OData/WebApi/issues/1113) ] ODataResourceDeserializer no longer supports null ComplexType values.
 
* [ [Issue #822](https://github.com/OData/WebApi/issues/822) ] Fix for memory leak in EdmLibHelpers.
 
---

## WebAPI 7.0.1 (.NET Core and .NET Classic)

The NuGet packages for ASP.NET Web API OData v7.0.1 are available on the [NuGet gallery](https://www.nuget.org/).

You can install or update the NuGet packages for OData Web API v7.0.1 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```powershell
PM> Install-Package Microsoft.AspNetCore.OData -Version 7.0.1 
```

or

```powershell
PM> Install-Package Microsoft.AspNet.OData -Version 7.0.1
```


### New Features:

* [ [Web API issue #1488](https://github.com/OData/WebApi/issues/1488) ] Support optional parameters

* [ [Web API PR #1527](https://github.com/OData/WebApi/pull/1527) ] Add UseOData() extension methods

### Improvements and fixes:

* [ [Web API issue #1518](https://github.com/OData/WebApi/issues/1518) ] Fix the Newtonsoft.Json dependency problem in classic version

* [ [Web API issue #1529](https://github.com/OData/WebApi/issues/1529) ] Fix ETag missing on derived Entity Set

* [ [Web API issue #1532](https://github.com/OData/WebApi/issues/1532) ] Fix JSON batch repsonse content type

---

## WebAPI 7.1.0 (.NET Core and .NET Classic)

The NuGet packages for ASP.NET Web API OData v7.1.0 are available on the [NuGet gallery](https://www.nuget.org/).

You can install or update the NuGet packages for OData Web API v7.1.0 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```PowerShell

PM> Install-Package Microsoft.AspNetCore.OData -Version 7.1.0 
```

or

```PowerShell

PM> Install-Package Microsoft.AspNet.OData -Version 7.1.0

```

>[!Notes]:
>[issue #1591](https://github.com/OData/WebApi/issues/1591) fixes an issue where types created by the ODataModelBuilder did not respect the namespace of the >ModelBuilder and instead used the namespace of the CLR type. With [PR #1592](https://github.com/OData/WebApi/pull/1592), OData WebAPI 7.1.0 now correctly uses the namespace on the ModelBuilder, if it has been explicitly set. In order to retain the old behavior of using the namespace of the CLR type, do not set the namespace on the ModelBuilder, or set the namespace on the ModelBuilder to null or to the desired namespace of the CLR type.


### New Features:

* [ [#1631](https://github.com/OData/WebApi/issues/1631) ] Don't require keys for singleton instances

* [ [#1628](https://github.com/OData/WebApi/pull/1628) ] Allow adding new members to a collection through a POST request

* [ [#1591](https://github.com/OData/WebApi/issues/1591) ] Support namespaces in OData ModelBuilder

* [ [#1656](https://github.com/OData/WebApi/issues/1656) ] Allowing the definition of partner relationships


### Improvements and fixes:

* [ [#1543](https://github.com/OData/WebApi/issues/1543) ] Json batch response body if 404

* [ [#1555](https://github.com/OData/WebApi/issues/1555) ] aspnetcore ETagMessageHandler throws then ClrType property name and EdmModel property name differs

* [ [#1559](https://github.com/OData/WebApi/issues/1559) ] Don't assume port 80 for nextLink when request was HTTPS

* [ [#1579](https://github.com/OData/WebApi/issues/1579) ] Star select ( $select= * ) not returning dynamic properties

* [ [#1588](https://github.com/OData/WebApi/pull/1588) ] Null check on ODataQueryParameterBindingAttribute for Net Core

* [ [#736](https://github.com/OData/WebApi/issues/736) ] EDMX returned from $metadata endpoint has incorrect "Unicode" attributes

* [ [#850](https://github.com/OData/WebApi/issues/850) ] ODataConventionModelBuilder takes into account [EnumMember] on enum members, but ODataPrimitiveSerializer (?) does not, causing mismatch in the payload.

* [ [#1612](https://github.com/OData/WebApi/pull/1612) ] Add the iconUrl to the nuget spec

* [ [#1615](https://github.com/OData/WebApi/pull/1615) ] fix compile error in sample project

* [ [#1421](https://github.com/OData/WebApi/issues/1421) ] Improve error message when structured type is received and properties are in incorrect case

* [ [#1658](https://github.com/OData/WebApi/pull/1658) ] Fix the security vulnerabilities in project dependencies

* [ [#1637](https://github.com/OData/WebApi/issues/1637) ] Change Request: Remove null check on 'ODataFeature().TotalCount' property

* [ [#1673](https://github.com/OData/WebApi/pull/1673) ] Respect preference header for paging

* [ [#1600](https://github.com/OData/WebApi/issues/1600) ] ActionDescriptorExtensions is not thread safe

* [ [#1617](https://github.com/OData/WebApi/issues/1617) ] Take and Top query use wrong Provider.CreateQuery Method

* [ [#1659](https://github.com/OData/WebApi/issues/1659) ] Odata V4 group by with Top and skip not working

* [ [#1679](https://github.com/OData/WebApi/issues/1679) ] Fix typo routePrerix

---
