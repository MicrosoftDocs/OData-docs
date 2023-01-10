---
title: "ASP.NET Core OData 8 fundamentals overview"
description: "This documentation introduces the fundamental concepts of ASP.NET Core OData library."

author: habbes
ms.author: clhabins

ms.date: 1/5/2023
---
# Fundamentals overview
**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

This article provides an overview of the fundamental concepts for building ASP.NET Core OData apps.

ASP.NET Core OData is a .NET library that sits on top of ASP.NET Core to help you build OData-based web APIs. The ASP.NET Core OData library consists of a set of services and middleware that hook into ASP.NET Core's request pipeline to provide features like routing, query handling and serialization based on OData specifications and conventions. All these concepts will be covered in depth in separate articles, this article will provide an overview.

To build an ASP.NET Core OData app, you start with an ASP.NET Core application, then install the [`Microsoft.AspNetCore.OData`](https://www.nuget.org/packages/Microsoft.AspNetCore.OData) package as a dependency from NuGet. ASP.NET Core OData 8 supports .NET Core 3.1 and .NET Core 6.0 and above.

> [!NOTE]
> ASP.NET COre OData 8 supports .NET Core 3.1 and .NET Core 6.0 and above.

# [Visual Studio](#tab/visual-studio)

In the Visual Studio **Package Manager Console**:

```powershell
Install-Package Microsoft.AspNetCore.OData
```

# [.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.AspNetCore.OData
```

---

Internally, ASP.NET Core OData depends on core .NET libraries for OData, including [`Microsoft.OData.Core`](https://www.nuget.org/packages/Microsoft.OData.Core) that provides features like reading and writing OData payloads, parsing OData URIs and query options and [`Microsoft.OData.Edm`](https://www.nuget.org/packages/Microsoft.OData.Edm) that provides support for working with OData schemas, also referred to as EDM (Entity Data Model) model.

The following code snippet demonstrates how you would add OData support to your ASP.NET Core application using ASP.NET Core OData in .NET Core 3.1 and .NET 6.0.

# [.NET 6.0](#tab/net60)

```csharp
// Program.cs
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.OData;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.OData.ModelBuilder;

var builder = WebApplication.CreateBuilder(args);

var modelBuilder = new ODataConventionModelBuilder();
modelBuilder.EntitySet<Customer>("Customers");
var edmModel = modelBuilder.GetEdmModel();

services.AddControllers().AddOData(
    options => options.AddRouteComponents(edmModel));

var app = builder.Build();

app.UseRouting();
app.UseEndpoints(endpoints => endpoints.MapControllers());
app.Run();
```

# [.NET Core 3.1](#tab/netcoreapp31)

```csharp
// Startup.cs
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.OData;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.OData.ModelBuilder;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        var modelBuilder = new ODataConventionModelBuilder();
        modelBuilder.EntitySet<Customer>("Customers");
        var edmModel = modelBuilder.GetEdmModel();

        services.AddControllers().AddOData(
            options => options.AddRouteComponents(edmModel));
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseRouting();
        app.UseEndpoints(endpoints => endpoints.MapControllers());
    }
}
```

---

We register OData services using the `services.AddControllers().AddOData()` extension method. The `AddOData()` method accepts a callback action that allows us to configure different aspects of the OData service using the options argument (which is an instance of [`ODataOptions`](/dotnet/api/microsoft.aspnetcore.odata.odataoptions)).

In this sample we're calling the `options.AddRouteComponents()` method which is used to register and configure and the components and services that make up our OData API. We pass an EDM model that describes the service schema to the `options.AddRouteComponents()`.

The EDM model is created with the lines:

```c#
var modelBuilder = new ODataConventionModelBuilder();
modelBuilder.EntitySet<Customer>("Customers");
var edmModel = modelBuilder.GetEdmModel();
```

This uses a model builder from the [`Microsoft.OData.ModelBuilder`](https://www.nuget.org/packages/Microsoft.OData.ModelBuilder) package to create an OData model with a single entity set called `Customers` that represents a collection of `Customer` entities.

You can install the `Microsoft.OData.ModelBuilder` package as follows:

# [Visual Studio](#tab/visual-studio)

In the Visual Studio **Package Manager Console**:

```powershell
Install-Package Microsoft.OData.ModelBuilder
```

# [.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.OData.ModelBuilder
```

---

## EDM model

The EDM model refers to the in-memory representation of the OData schema. It defines the data types and endpoints exposed by the OData service. This allows clients to consume the API in a predictable, type-safe manner. The EDM model is represented by the [`IEdmModel`](/dotnet/api/microsoft.odata.edm.iedmmodel) interface defined in the `Microsoft.OData.Edm` package. When you create an OData service, you need specify the `IEdmModel` that describes the service. This interface exposes methods for retrieving the data types, metadata and other information defined in the schema. You specify the EDM model by passing an instance of `IEdmModel` to the `ODataOptions.AddRouteComponents` method:

```c#
services.AddControllers().AddOData(options => options.AddRouteComponents(edmModel));
```

There are a number of ways to create an `IEdmModel` instance:
- You can use the `CsdlReader` class to parse an `IEdmModel` from a stream that contains the OData schema in [CSDL XML](https://docs.oasis-open.org/odata/odata-csdl-xml/v4.01/odata-csdl-xml-v4.01.html) or [CSDL JSON](https://docs.oasis-open.org/odata/odata-csdl-json/v4.01/odata-csdl-json-v4.01.html) format. The `CsdlReader` class is defined in the `Microsoft.OData.Edm` package. To learn more, [visit this artcile](/odata/odatalib/edm/read-write-model).
- Your can manually construct a model by creating an instance of the [`EdmModel`](/dotnet/api/microsoft.odata.edm.edmmodel) (which implements `IEdmModel`). The `EdmModel` class is also part of the `Microsoft.OData.Edm` package. To learn more about creating EDM models manually, visit the [EDM documentation](/odata/odatalib/edm/build-basic-model).
- You can use the `Microsoft.OData.ModelBuilder` package to easily create an `IEdmModel` based on CLR classes. The package provides 2 main classes:
    - `ODataConventionModelBuilder` used to quickly create models from CLR classes based on opinionated conventions (for example, it can infer key properties based on conventional names like `Id`). This is the easiest method and what we use in most of the samples in this documentation. For example usage, [visit this article](/odata/webapi/convention-model-builder).
    - `ODataModelBuilder` gives you more control to specify things explicitly but requires more work to use. For example usage, [visit this article](/odata/webapi/model-builder-nonconvention).

> [!NOTE]
> In ASP.NET Core OData 7, the model builder was built-in to the library. It was extracted into a standalone package `Microsoft.OData.Modelbuilder`. The two work mostly the same way and the 7.x model builder documentation still applies to the standalone `Microsoft.OData.ModelBuilder`.

Here are examples of things that can be defined in an EDM model:
- Entity types
- Complex types
- Enums
- Entity Sets
- Singletons
- Functions
- Actions

The EDM model also supports type inheritance and relationships between types.

Entity sets, singletons, functions and actions can be exposed as endpoints that you can make request to. Entity types, complex types and enums define types of objects that can be used as inputs or outputs of those endpoints.

To learn more about the OData data model, [visit this article](/odata/concepts/data-model).

By default, the OData service exposes the schema on the `GET /$metadata` endpoint. Here's an example of what a response from this endpoint looks like:

```xml
<?xml version="1.0" encoding="utf-8"?>
<edmx:Edmx Version="4.0" xmlns:edmx="http://docs.oasis-open.org/odata/ns/edmx">
    <edmx:DataServices>
        <Schema Namespace="Lab01.Models" xmlns="http://docs.oasis-open.org/odata/ns/edm">
            <EntityType Name="Order">
                <Key>
                    <PropertyRef Name="Id" />
                </Key>
                <Property Name="Id" Type="Edm.Int32" Nullable="false" />
                <Property Name="Amount" Type="Edm.Decimal" Nullable="false" Scale="Variable" />
            </EntityType>
            <EntityType Name="Customer">
                <Key>
                    <PropertyRef Name="Id" />
                </Key>
                <Property Name="Id" Type="Edm.Int32" Nullable="false" />
                <Property Name="Name" Type="Edm.String" />
                <NavigationProperty Name="Orders" Type="Collection(Lab01.Models.Order)" />
            </EntityType>
        </Schema>
        <Schema Namespace="Default" xmlns="http://docs.oasis-open.org/odata/ns/edm">
            <EntityContainer Name="Container">
                <EntitySet Name="Customers" EntityType="Lab01.Models.Customer" />
            </EntityContainer>
        </Schema>
    </edmx:DataServices>
</edmx:Edmx>
```


Clients can consume this metadata to know what types and endpoints the service exposes. Many OData clients use this metadata to generate strongly-typed client code. Tools like the [OData Connected Service](/odata/connectedservice/getting-started) and [OData CLI](/odata/odatacli/getting-started) can generate client code based on an OData schema.

## Registering OData services

It is possible to service multiple OData APIs in the same ASP.NET Core application. You register an OData API using the `options.AddRouteComponents` method. This method registers the EDM model, base route and components for a specific OData service.

In the example above, we register a single OData service. This is hosted on the "default" route, i.e. on the root path `/`. This means that the paths on this OData service look like `/Customers`, `/Products`, `/$metadata`.

You can also specify a **route prefix** when registering route components. This is a string that will prefix all the paths on this API. There's an `AddRouteComponents` overload that accepts a route prefix as the first argument:

```c#
services.AddControllers().AddOdata(
    options => options.AddRouteComponents("odata", edmModel));
```
In this case, paths will have the `odata` prefix, e.g. `/odata/Customers`, `/odata/Products`, `/odata/$metadata`.

The application's base URL together with the route prefix form the **service URL** of the OData service. For example, if the application's base URL is `https://service.example.org` and the route prefix is `odata`, then the service URL for this OData service will be `https://service.example.org/odata`. If the route prefix is empty (i.e. default route), then the service URL will be `https://service.example.org`.

You can call the `AddRouteComponents()` method multiple times with different prefixes (and possibly different `IEdmModel` instances) to serve multiple OData APIs in the same application:

```c#
services.AddControllers().AddOData(
    options => options.AddRouteComponents("api1", edmModel1).AddRouteComponents("api2", edmModel2));
```

This will create OData services with the following url:
- `<baseUrl>/api1` using the schema defined by `edmModel1`.
- `<baseUrl>/api2` using the schema defined by `edmModel2`.

## Routing

> [!IMPORTANT]
> ASP.NET Core OData routing is based on controllers and therefore does not support [minimal APIs](/aspnet/core/fundamentals/minimal-apis/overview).

The OData specification defines a number of conventions for routing and endpoints for accessing various resources based on REST best-practices. For example, if you have an entity set called `Customers` based on the `Customer` entity type, the service will be expected to expose a `GET /Customers` endpoint that returns a collection of `Customer` entities, it will also be expected to expose a `GET /Customers({id})` endpoint that returns a customer based on ID.

The ASP.NET Core OData library makes it easy to create endpoints that match OData's routing conventions. Routing in ASP.NET Core OData is based on controllers. For example, here's what a controller for the `Customers` endpoint could look like:

```c#
public class CustomersController : ODataController
{
    DbContext _db;

    public CustomersController(DbContext db)
    {
        _db = db;
    }

    public ActionResult<IQueryable<Customer>> Get()
    {
        return Ok(_db.Customers);
    }

    public ActionResult<Customer> Get(int id)
    {
        return _db.Customers.Find(id);
    }

    public ActionResult<Customer> Post(Customer customer)
    {
        _db.Customers.Add(customer);
        _db.SaveChanges();
        return Created(customer);
    }
}
```

- The `Get()` controller action will be mapped to the `GET /Customers` endpoint.
- The `Get(int id)` controller action will be mapped to the `GET /Customers({id})` and `GET /Customers/{id}` endpoints
- The `Post(Customer customer)` controller action will be mapped to the `POST /Customers` endpoint.

It is up to the developer to implement the actual logic to perform the expected operation and return the expected response. For example, it is up to the developer to ensure that the `Get()` method returns a collection of `Customer` objects, the library does not enforce this. This means that it's possible for an application written with ASP.NET Core OData not to be compliant with OData specifications or convnetions.

The library supports routing for common OData endpoints like entity sets, singletons, actions, functions, nested properties, etc. You can specify an endpoint for a specific controller action explicitly using ASP.NET Core `[Route]` attributes. You can also define your own routing conventions to extend the built-in conventions or define custom routing logic.

Visit the [routing documentation](/odata/webapi-8/fundamentals/routing-overview) to learn more about routing.

## Query handling

OData defines various query options that clients can add to an OData request URL to query and transform the response OData. Here are some of the common query options:
- `$filter` allows you to filter results based on specific conditions: `/Products?$filter=Price lt 1000 and Category eq 'Electronics'` will only return electronic products whose price is less than 1,000.
- `$select` allows you to limit the fields that are included in the response: `/Products?$select=Name,Category,DateCreated` will only include the `Name`, `Category`, `DateCreated` fields in the response
- `$expand` allows you to join and include related data in the response: `/OrderItems?$expand=Product` will return order items and include the related product in the response
- `$skip` and `$top` allow you to limit the number of records in the response and can be used for client-driven pagination: `/Products$top=10&$skip=20&$orderby=Name` returns the third page of 10 products sorted by name.
- `$apply` allows you to apply aggregations: `/Orders?$apply=groupby(Product/Name, aggregate($count as Count))` returns the number of orders per product name.

These query options are not enabled by default. You can enable and configure them individual through the options passed to the `AddOData()` method:

```c#
services.AddControllers().AddOData(
    options => options.Select().Filter().Expand().SetMaxTop(100).AddRouteComponents(edmModel));
```
In this example, we only enable the `$select`, `$filter`, `$expand`, and `$top` query options and we're setting the max allowable value for `$top` to be 100. If the client tries to use an OData query option that's not enabled, the service will return an error.

You can enable all supported query options in one go using the `EnableQueryFeatures` method:

```c#
services.AddControllers().AddOData(
    options => options.EnableQueryFeatures(100).AddRouteComponents(edmModel));
```

The argument passed to `EnableQueryFeatures` is the max top value. You can set it to `null` if you don't want to impose a limit on the value passed to `$top`.

After enabling query options on the service, you also have to enable it on specific endpoints where you want to support query options. You do this by adding the [`[EnableQuery]`](/dotnet/api/microsoft.aspnetcore.odata.query.enablequeryattribute) attribute on the corresponding controller actions. Without specifying `[EnableQuery]` explicitly, OData query options will be ignored for that endpoint.

```c#
public class ProductsController : ODataController
{
    [EnableQuery]
    public IQueryable<Product> Get()
    {
        return _db.Products;
    }
}
```

Now if a client makes a request like `GET /Products?$select=Name`, the Product entities in the response will only include the `Name` property.

```json
{
    "@odata.context": "http://localhost:5000/$metadata#Products(Name)",
    "value": [
        {
            "Name": "Product 1"
        },
        {
            "Name": "Product 2"
        },
        {
            "Name": "Product 3"
        }
    ]
}
```

ASP.NET Core OData transforms the query option into an [ODataQueryOptions](/dotnet/api/microsoft.aspnetcore.odata.query.odataqueryoptions) object that can convert the query options into [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) expressions. `[EnableQuery]` uses the `ODataQueryOptions` object to transform the result from the controller action. If the result is an [`IEnumerable<T>`](/dotnet/api/system.collections.generic.ienumerable-1) collection (for example an in-memory `List<T>`), the data filtering and transformations will be performed in-memory. If the result is an [`IQueryable<T>`](/dotnet/api/system.linq.iqueryable-1) collection, the query options are transformed into [LINQ expression trees](/dotnet/csharp/programming-guide/concepts/expression-trees/) that are passed to the underlying [query provider](/dotnet/api/system.linq.iqueryprovider). The underlying provider can translate the expression trees into native database queries that are executed directly on the database.

## Request and response format

ASP.NET Core OData uses JSON format for its requests and responses (the `$metadata` endpoint returns the schema in XML format by default). The OData JSON format may include metadata in the payload such as type information that makes it possible for clients and server to know how to handle the payload. This metadata is usually expressed in the form of "annotations" which are extra fields in the payload that start with `@odata.` prefix.

For example. Let's say we have an OData service that has a `Customers` entity set which returns entities of type `Customer`.

A request like `GET /Customers`, we would a get a response like

```json
{
    "@odata.context": "http://localhost:5000/$metadata#Customers",
    "value": [
        {
            "Id": 3,
            "Name": "Customer 3"
        },
        {
            "Id": 2,
            "Name": "Customer 2"
        },
        {
            "Id": 1,
            "Name": "Customer 1"
        }
    ]
}
```

Notice that this response does not just return an array of `Customer` object, it returns an object with an `@odata.context` and `value` fields. The `value` field's value is the array that contains the actual data. The `@odata.context` field is an annotation that gives that client information about where this data comes from. The `http://localhost:5000/odata/$metadata#Customers` is called the context URL, in this case it shows that this payload comes from the `Customers` entity set. The client can find out more information about the `Customers` entity set from the schema, which it can retrieve from `http://localhost:5000/$metadata`. Using both the schema and the context URL, the client can tell that the payload value is a an array of `Customer` objects, and it knows what properties the `Customer` entity type has and what the types of these properties are.

Now let's add a query option to the request to include only the `Name` field: `GET /Customers?$select=Name`.

The response would look like:

```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Customers(Name)",
    "value": [
        {
            "Name": "Customer 1"
        },
        {
            "Name": "Customer 2"
        },
        {
            "Name": "Customer 3"
        }
    ]
}
```

Notice that the context URL has changed. Now the last part looks like `Customers(Name)` which means that the payload is from the `Customers` entity set (and therefore of the `Customer` type), but only includes the `Name` field. So we see that the response type does not only depend on the types defined in the model, but also the data transformations based on the request. The context URL and other annotations help the client figure out how to handle such dynamic payloads in a predictable and type-safe manner.

To achieve this, ASP.NET Core OData uses customer input and output formatters to handle serialization and deserialization. These formatters know how to handle the OData-specific JSON format. It may also perform some validation to ensure the payloads match OData's specification. For example, it may validate that the properties in the request and response are actually defined in the OData model.

> [!IMPORTANT]
> ASP.NET Core OData **does not** use `System.Text.Json` or `Newtonsoft.JSON` for JSON serialization by default. Instead, it uses [`ODataMessageWriter`](/odata/odatalib/write-payload) and [`ODataMessageReader`](/odata/odatalib/read-payload) from the `Microsoft.OData.Core` package.