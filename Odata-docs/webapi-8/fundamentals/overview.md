---
title: "ASP.NET Core OData 8 Fundamentals Overview"
description: "This document introduces the fundamental concepts of ASP.NET Core OData library."

author: habbes
ms.author: clhabins

ms.date: 1/5/2023
---
# Fundamentals Overview
**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

This article provides an overview of the fundamental concepts for building ASP.NET Core OData services.

ASP.NET Core OData is a .NET library built on top of ASP.NET Core to help you build OData REST APIs. The ASP.NET Core OData library consists of a set of services and middleware that hook into ASP.NET Core's request pipeline to provide features like routing, query handling and serialization based on OData specifications and conventions. All these concepts will be covered in depth in separate articles, this article just provides an overview.

To build an ASP.NET Core OData app, you start with an ASP.NET Core application, then install the [`Microsoft.AspNetCore.OData`](https://www.nuget.org/packages/Microsoft.AspNetCore.OData) NuGet package as a dependency. ASP.NET Core OData 8 supports .NET Core 3.1 and .NET Core 6.0 and above.

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

Internally, ASP.NET Core OData depends on the following .NET libraries for OData: 
- [`Microsoft.OData.Core`](https://www.nuget.org/packages/Microsoft.OData.Core), which provides features like reading and writing OData payloads, parsing OData URIs and query options
- [`Microsoft.OData.Edm`](https://www.nuget.org/packages/Microsoft.OData.Edm), which provides support for working with OData schemas, also referred to as EDM (Entity Data Model) model.
- [`Microsoft.Spatial`](https://www.nuget.org/packages/Microsoft.Spatial), which provides support for geospatial data types.

The following code snippet demonstrates how you would add OData support to your ASP.NET Core application using ASP.NET Core OData in .NET Core 3.1 and .NET 6.0.

# [.NET 6.0](#tab/net60)

```csharp
// Program.cs
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.DependencyInjection;
// OData namespaces
using Microsoft.AspNetCore.OData;
using Microsoft.OData.ModelBuilder;

var builder = WebApplication.CreateBuilder(args);

// Create the service EDM model.
var modelBuilder = new ODataConventionModelBuilder();
modelBuilder.EntitySet<Customer>("Customers");
var edmModel = modelBuilder.GetEdmModel();

// Register OData service.
builder.Services.AddControllers().AddOData(
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
using Microsoft.Extensions.DependencyInjection;
// OData namespaces
using Microsoft.AspNetCore.OData;
using Microsoft.OData.ModelBuilder;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        // Create the service EDM model.
        var modelBuilder = new ODataConventionModelBuilder();
        modelBuilder.EntitySet<Customer>("Customers");
        var edmModel = modelBuilder.GetEdmModel();

        // Register OData service.
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

We register OData services using the [`AddOData`](/dotnet/api/microsoft.aspnetcore.odata.odatamvcbuilderextensions.addodata) extension method. The `AddOData` method accepts a callback action that allows us to configure different aspects of the OData service using the options argument (which is an instance of [`ODataOptions`](/dotnet/api/microsoft.aspnetcore.odata.odataoptions)).

In this sample we're calling the [`AddRouteComponents`](/dotnet/api/microsoft.aspnetcore.odata.odataoptions.addroutecomponents) method which is used to register and configure the components and services that make up our OData API. We pass an EDM model that describes the service schema to the `AddRouteComponents`.

The EDM model is built using the following logic:

```c#
var modelBuilder = new ODataConventionModelBuilder();
modelBuilder.EntitySet<Customer>("Customers");
var edmModel = modelBuilder.GetEdmModel();
```

In the preceding block of code, the conventional model builder from the [`Microsoft.OData.ModelBuilder`](https://www.nuget.org/packages/Microsoft.OData.ModelBuilder) package is used to build an OData model with a single entity set called `Customers` that represents a collection of `Customer` entities.

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

The EDM model refers to the in-memory representation of the OData schema. It defines the data types and endpoints exposed by the OData service. This allows clients to consume the API in a predictable, type-safe manner. The EDM model is represented by the [`IEdmModel`](/dotnet/api/microsoft.odata.edm.iedmmodel) interface defined in the `Microsoft.OData.Edm` package. When you create an OData service, you need specify the `IEdmModel` that describes the service. This interface exposes methods for retrieving the data types, metadata and other information defined in the schema. You specify the EDM model by passing an instance of `IEdmModel` to the `AddRouteComponents` method:

```c#
AddOData(options => options.AddRouteComponents(edmModel));
```

There are a number of ways to create an `IEdmModel` instance:
- Using the `CsdlReader` class to parse an `IEdmModel` from a stream that contains the OData schema in [CSDL XML](https://docs.oasis-open.org/odata/odata-csdl-xml/v4.01/odata-csdl-xml-v4.01.html) or [CSDL JSON](https://docs.oasis-open.org/odata/odata-csdl-json/v4.01/odata-csdl-json-v4.01.html) format. The `CsdlReader` class is defined in the `Microsoft.OData.Edm` package. To learn more, [visit this article](/odata/odatalib/edm/read-write-model).
- Manually constructing a model by creating an instance of the [`EdmModel`](/dotnet/api/microsoft.odata.edm.edmmodel) class (which implements `IEdmModel`). The `EdmModel` class is also part of the `Microsoft.OData.Edm` package. To learn more about creating EDM models manually, visit the [EDM documentation](/odata/odatalib/edm/build-basic-model).
- Using the `Microsoft.OData.ModelBuilder` package to easily create an `IEdmModel` based on CLR classes. The package provides 2 main classes:
    - `ODataConventionModelBuilder`, used to quickly create models from CLR classes based on opinionated conventions (for example, it can infer key properties based on conventional names like `Id`). This is the easiest method and what we use in most of the samples in this documentation. For example usage, [visit this article](/odata/webapi/convention-model-builder).
    - `ODataModelBuilder`, that gives you more control to specify things explicitly but requires more work to use. For example usage, [visit this article](/odata/webapi/model-builder-nonconvention).

> [!NOTE]
> In ASP.NET Core OData 7, the model builder was built-in to the library. It was extracted into a standalone package `Microsoft.OData.Modelbuilder`. The two work the same mostly and the 7.x model builder documentation still applies to the standalone `Microsoft.OData.ModelBuilder`.

Here are examples of things that can be defined in an EDM model:
- Entity types
- Complex types
- Enums
- Entity sets
- Singletons
- Functions
- Actions

The EDM model also supports type inheritance and relationships between types.

Entity sets, singletons, functions and actions can be exposed as endpoints that you can make requests to. Entity types, complex types and enum types define types of objects that can be used as inputs or outputs of those endpoints.

To learn more about the OData data model, [visit this article](/odata/concepts/data-model).

By default, an OData service exposes the schema on the `GET /$metadata` endpoint. Here's an example of what a response from this endpoint looks like:

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

It is possible to serve multiple OData APIs in the same ASP.NET Core application.

The first sample code in this document registers a single OData service using `AddRouteComponents(IEdmModel)`. This is hosted on the "default" route, i.e. on the root path `/`. This means that the endpoints on the OData service look like :
- `/Customers`
- `/Products`
- `/$metadata`.

You can also specify a **route prefix** when registering route components. This is a string that will prefix all the paths on this API. There's an `AddRouteComponents` overload that accepts a route prefix as the first argument:

```c#
AddOdata(
    options => options.AddRouteComponents("odata", edmModel));
```
In this case, paths will be prefixed with `odata`:
- `/odata/Customers`, 
- `/odata/Products`, 
- `/odata/$metadata`

The application's base URL together with the route prefix form the **service root** of the OData service. For example, if the application's base URL is `https://service.example.org` and the route prefix is `odata`, then the service root for this OData service will be `https://service.example.org/odata`. If the route prefix is empty (i.e. default route), then the service root will be `https://service.example.org`.

You can call the `AddRouteComponents()` method multiple times with different prefixes (and possibly different `IEdmModel` instances) to serve multiple OData APIs in the same application:

```c#
AddOData(
    options => options.AddRouteComponents("api1", edmModel1).AddRouteComponents("api2", edmModel2));
```

This will create OData services with the following url:
- `<baseUrl>/api1` using the schema defined by `edmModel1`.
- `<baseUrl>/api2` using the schema defined by `edmModel2`.

> [!IMPORTANT]
> The more specific route should be registered ahead of the more generic one.

If you register two routes where one is more generic than the other, do it such that the more specific route is registered ahead of the generic one. For example, where `odata` is more generic than `odata/v1` you should register the routes as follows:
```csharp
AddOData(options => options
    .AddRouteComponents("odata/v1", edmModel1)
    .AddRouteComponents("odata", edmModel2);
```

## Routing

> [!IMPORTANT]
> ASP.NET Core OData routing is based on controllers and currently does not support [minimal APIs](/aspnet/core/fundamentals/minimal-apis/overview).

The OData specification defines a number of conventions for routing and accessing various resources based on REST best-practices. For example, if you have an entity set called `Customers` based on the `Customer` entity type, the convention is to expose a `GET /Customers` endpoint that returns a collection of `Customer` entities, and a `GET /Customers({id})` endpoint that returns a customer based on ID.

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
        return Ok(_db.Customers.Find(id));
    }

    public ActionResult<Customer> Post([FromBody] Customer customer)
    {
        _db.Customers.Add(customer);
        _db.SaveChanges();
        return Created(customer);
    }
}
```

- The `Get()` controller action will be mapped to the `GET /Customers` endpoint.
- The `Get(int id)` controller action will be mapped to the `GET /Customers({id})` and `GET /Customers/{id}` endpoints.
- The `Post(Customer customer)` controller action will be mapped to the `POST /Customers` endpoint.

It is up to the developer to implement the actual logic for performing the expected operation and returning the expected response. For example, it is up to the developer to ensure that the `Get()` method returns a collection of `Customer` objects, the library does not enforce this. This means that it's possible for an application written with ASP.NET Core OData not to be compliant with OData specifications or conventions.

The library supports routing for common OData endpoints like entity sets, singletons, actions, functions, properties, etc. You can specify an endpoint for a specific controller action explicitly using ASP.NET Core's [attribute routing](/aspnet/core/mvc/controllers/routing#attribute-routing-for-rest-apis). You can also define your own routing conventions to extend the built-in conventions or define custom routing logic.

Visit the [routing documentation](/odata/webapi-8/fundamentals/routing-overview) to learn more about OData routing.

## Query handling

OData defines various query options that clients can add to an OData request URL to transform or limit the query results. Here are some of the common query options:
- `$filter` allows you to filter results based on specific conditions: `/Products?$filter=Price lt 1000 and Category eq 'Electronics'` will only return electronic products whose price is less than 1,000.
- `$select` allows you to limit the fields that are included in the response: `/Products?$select=Name,Category,DateCreated` will only include the `Name`, `Category`, `DateCreated` fields in the response
- `$expand` allows you to join and include related data in the response: `/OrderItems?$expand=Product` will return order items and include the related product in the response
- `$skip` and `$top` allow you to limit the number of records in the response and can be used for client-driven pagination: `/Products$top=10&$skip=20` returns the third page of 10 products.
- `$orderby` allows you to sort the results: `/Products?$orderby=DateCreated desc` will sort the products by date in descending order (most recent first).
- `$apply` allows you to group and aggregate results: `/Orders?$apply=groupby((Product/Name), aggregate($count as Count))` returns the number of orders per product name.

These query options are not enabled by default. You can enable and configure them individually through the options parameter passed to the `AddOData` method:

```c#
AddOData(
    options => options.Select().Filter().Expand().OrderBy().SetMaxTop(100).AddRouteComponents(edmModel));
```
In this example, we only enable the `$select`, `$filter`, `$expand`, `$orderby` and `$top` query options and we're setting the max allowable value for `$top` to be 100. If the client tries to use an OData query option that's not enabled, the service will return an error.

You can enable all supported query options in one go using the `EnableQueryFeatures` method:

```c#
AddOData(
    options => options.EnableQueryFeatures(100).AddRouteComponents(edmModel));
```

The argument passed to `EnableQueryFeatures` is the maximum allowed value for `$top` query option. You can set it to `null` if you don't want to impose a limit to the value specified in the `$top` query option.

After enabling query options on the service, you also need to enable querying on specific endpoints where you want to support query options. You can do this by adding the [`[EnableQuery]`](/dotnet/api/microsoft.aspnetcore.odata.query.enablequeryattribute) attribute to the corresponding controller actions. The `EnableQuery` attribute automatically applies the query options to the results returned by the controller action.

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

ASP.NET Core OData transforms the query options into an [ODataQueryOptions](/dotnet/api/microsoft.aspnetcore.odata.query.odataqueryoptions) object that converts the query options into [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) expressions. `[EnableQuery]` uses the `ODataQueryOptions` object to transform the result from the controller action. If the result is an [`IEnumerable<T>`](/dotnet/api/system.collections.generic.ienumerable-1) collection (for example an in-memory `List<T>`), the data filtering and transformations will be performed in-memory. If the result is an [`IQueryable<T>`](/dotnet/api/system.linq.iqueryable-1) collection, the query options are transformed into [LINQ expression trees](/dotnet/csharp/programming-guide/concepts/expression-trees/) that are passed to the underlying [query provider](/dotnet/api/system.linq.iqueryprovider). The underlying provider can translate the expression trees into native database queries that are executed directly on the database.

## Request and response format

ASP.NET Core OData uses JSON format for its requests and responses (the `$metadata` endpoint returns the schema in XML format by default). The OData JSON format may include metadata in the payload such as type information that makes it possible for clients and server to know how to handle the payload. This metadata is usually expressed in the form of "annotations" which are extra fields in the payload that start with `@odata.` prefix.

For example. Let's say we have an OData service that has a `Customers` entity set which returns entities of type `Customer`.

For a request like `GET /Customers`, we would a get a response like:

```json
{
    "@odata.context": "http://localhost:5000/$metadata#Customers",
    "value": [
        {
            "Id": 1,
            "Name": "Customer 1"
        },
        {
            "Id": 2,
            "Name": "Customer 2"
        },
        {
            "Id": 3,
            "Name": "Customer 3"
        }
    ]
}
```

Notice that this response does not just return an array of `Customer` object, it returns an object comprising of `@odata.context` and `value` properties. The `value` property's value is the array that contains the actual data. The `@odata.context` property is an annotation that gives that client information about where this data comes from. The `http://localhost:5000/$metadata#Customers` is called the context URL, in this case it shows that this payload comes from the `Customers` entity set. The client can find out more information about the `Customers` entity set from the schema, which can be retrieved from the service metadata endpoint - `http://localhost:5000/$metadata`. Using both the schema and the context URL, the client can tell that the payload value is a an array of `Customer` objects, and it can determine the properties contained in the `Customer` entity type and their respective types.

Let's add a query option to the request to include only the `Name` field: `GET /Customers?$select=Name`.

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

Notice that the context URL is different in this case. Now the last part looks like `Customers(Name)` which means that the payload is from the `Customers` entity set (and therefore of the `Customer` type), but only includes the `Name` field. So we see that the response type does not only depend on the types defined in the model, but also the data transformations based on the request. The context URL and other annotations help the client figure out how to handle such dynamic payloads in a predictable and type-safe manner.

To achieve this, ASP.NET Core OData uses custom input and output formatters to handle serialization and deserialization. These formatters know how to handle the OData-specific JSON format. It may also perform some validation to ensure the payloads match OData's specification. For example, it may validate that the properties in the request and response are actually defined in the EDM model.

## Batch requests

OData allows you to group multiple operations in a single HTTP request, called a batch request. The response to this batch request contains the responses to the individual operations that were in the batch request. Batch requests allow you to reduce the number of round trips between client and server and can improve the performance and scalability of a service.

The ASP.NET Core OData batch requests feature is not enabled by default. To enable batch requests, you need to provide an OData batch handler and enable the OData batch middleware. The OData batch handler is the service that is responsible for processing a batch request. The OData batch middleware intercepts batch requests and routes them to the batch handler instead of conventional routing. The ASP.NET Core OData library provides a default batch handler to make batch support easy.

# [.NET 6.0](#tab/net60)

```csharp
// Program.cs
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.OData;
using Microsoft.OData.ModelBuilder;

var builder = WebApplication.CreateBuilder(args);

var modelBuilder = new ODataConventionModelBuilder();
modelBuilder.EntitySet<Customer>("Customers");
var edmModel = modelBuilder.GetEdmModel();

// Register OData service with support for batch requests.
builder.Services.AddControllers().AddOData(
    options => options.AddRouteComponents(edmModel, new DefaultODataBatchHandler()));

var app = builder.Build();

// Enable OData batching middleware
app.UseODataBatching();

app.UseRouting();
app.UseEndpoints(endpoints => endpoints.MapControllers());
app.Run();
```

# [.NET Core 3.1](#tab/netcoreapp31)

```csharp
// Startup.cs
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.OData;
using Microsoft.OData.ModelBuilder;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        var modelBuilder = new ODataConventionModelBuilder();
        modelBuilder.EntitySet<Customer>("Customers");
        var edmModel = modelBuilder.GetEdmModel();

        // Register OData service with support for batch requests.
        services.AddControllers().AddOData(
            options => options.AddRouteComponents(edmModel, new DefaultODataBatchHandler()));
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        // Enable OData batching middleware
        app.UseODataBatching();

        app.UseRouting();
        app.UseEndpoints(endpoints => endpoints.MapControllers());
    }
}
```

---

In the preceding block of code, we add an instance of the [`DefaultODataBatchHandler`](/dotnet/api/microsoft.aspnetcore.odata.batch.defaultodatabatchhandler) as an argument to the the `AddRouteComponents` method. We also register the batching middleware using `UseODataBatching`. 

> [!NOTE]
> The order of middleware matters. It is important that `app.UseODataBatching()` is called before `app.UseRouting()`.

With this setup, the service can now handle batch requests. OData batch requests are POST requests made to the `/$batch` endpoint. The request body contains the individual operations. These operations can be independent or related `GET`, `PUT`, `PATCH`, `POST`, `DELETE` requests or function and action invocations. The body of the batch request supports two formats: JSON and multipart mixed. The format is specified in the `Content-Type` header: `application/json` for JSON and `multipart/mixed` for multipart mixed. The following sample shows an example of a JSON batch request:

**Request**

The body contains an array of separate requests, each specifying the HTTP method, URL, headers and body where applicable and an ID - the ID is optional and defaults to `null`.

```json
POST http://localhost:5000/$batch
Content-Type: application/json

{
    "requests": [
        {
            "method": "GET",
            "url": "http://localhost:5000/Products/1",
            "id": "r1"
        },
        {
            "method": "GET",
            "url": "http://localhost:5000/People?$select=FirstName&$top=2",
            "id": "r2"
        },
        {
            "method": "PATCH",
            "url": "http://localhost:5000/Products/2",
            "id": "r3",
            "headers": {
                "content-type": "application/json"
            },
            "body": {
                "Category": "Electronics"
            }
        }
    ]
}

```

**Response**

The response body contains an array of responses, one for each request. Each response includes the HTTP status code, headers, body and other metadata that allows the client to process the response.

```json
{
    "responses": [
        {
            "id": "r1",
            "status": 200,
            "headers": {
                "content-type": "application/json; odata.metadata=minimal; odata.streaming=true; charset=utf-8",
                "odata-version": "4.0"
            },
            "body": {
                "@odata.context": "http://localhost:5000/$metadata#Products/$entity",
                "Id": 1,
                "Category": "Goods",
                "Color": "Red",
                "CreatedDate": "2001-04-15T16:24:08-08:00",
                "UpdatedDate": "2011-02-15T16:24:08-08:00"
            }
        },
        {
            "id": "r2",
            "status": 200,
            "headers": {
                "content-type": "application/json; odata.metadata=minimal; odata.streaming=true; charset=utf-8",
                "odata-version": "4.0"
            },
            "body": {
                "@odata.context": "http://localhost:5000/$metadata#People(FirstName)",
                "value": [
                    {
                        "FirstName": "Fiction"
                    },
                    {
                        "FirstName": "Goods"
                    }
                ]
            }
        },
        {
            "id": "r3",
            "status": 200,
            "headers": {
                "content-type": "application/json; odata.metadata=minimal; odata.streaming=true; charset=utf-8",
                "odata-version": "4.0"
            },
            "body": {
                "@odata.context": "http://localhost:5000/$metadata#Products/$entity",
                "Id": 2,
                "Category": "Electronics",
                "Color": "Blue",
                "CreatedDate": "2021-12-27T09:12:08-08:00",
                "UpdatedDate": null
            }
        }
    ]
}
```

The batch handler breaks down the batch request into separate requests, executes them and combines them in a single batch response. Each request in the batch is routed and handled as if it had been made directly from the client. This means that each request will automatically be routed to the relevant controller action. If there are any dependencies between requests (e.g. one request relies on the response of another), the batch handler will ensure they are executed in the right order.

For more information about JSON batching, [visit this article](/odata/odatalib/json-batch).
For examples using the `multipart/mixed` format, [visit this article](/odata/webapi/batch).
<!-- TODO: replace these links with a link to an upto-date article dedicated to OData batching in ASP.NET Core OData 8 once that article has been written (see work item 1980957)-->