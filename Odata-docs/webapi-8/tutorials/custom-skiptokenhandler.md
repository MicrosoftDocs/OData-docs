---
title:  "Customizing skip tokens with SkipTokenHandler"
description: A tutorial on how to customize generated skip token whith a custom SkipTokenHandler class.
date:   2023-01-19
ms.date: 01/19/2023
author: habbes
ms.author: clhabins
---

# Customizing skip tokens with SkipTokenHandler
**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

Skip tokens are used in server-side paging to keep track of the last record that was sent the client so that it can generate the next page of results. The skip token is opaque to the client, this means that the server has freedom to decide what the contents of the skip token should be and how to interpret them. ASP.NET Core OData generates by default the skip token by combining the fields and values of the the a reference record. However, you can override this behaviour by providing your own custom logic for generating and processing skip tokens.

In this tutorial you'll learn how to customize the skip token logic using a custom `SkipTokenHandler`.

This tutorial assumes a basic understanding of how to create and run an ASP.NET Core OData 8 application. If you're unfamiliar with ASP.NET Core OData 8, you may want to go through the [Getting started](/odata/webapi-8/getting-started) tutorial.

To demonstrated how to override the default `SkipTokenHandler`, let's build a sample OData service.

## Prerequisites

[!INCLUDE[](../../includes/appliesto-webapi-v8-net-prereqs-vs.md)]

## Packages

[!INCLUDE[](../../includes/appliesto-webapi-v8-pkg-install.md)]

## Models

For this exercise, we only create a single model class:

```c#
namespace CustomSkipTokenSample
{
    public class Customer
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }
}
```

## EDM model and Configuration

The logic for building the Edm model and configuring the OData service is as follows:

# [.NET 6.0](#tab/net60)

```csharp
// Program.cs
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.OData;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.OData.ModelBuilder;
using CustomSkipTokenSample.Models;

var builder = WebApplication.CreateBuilder(args);

var modelBuilder = new ODataConventionModelBuilder();
modelBuilder.EntitySet<Customer>("Customers");

builder.Services.AddControllers().AddOData(
    options => options.SkipToken().AddRouteComponents(
        "odata",
        modelBuilder.GetEdmModel()));

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
using RefRouting.Models;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        var modelBuilder = new ODataConventionModelBuilder();
        modelBuilder.EntitySet<Customer>("Customers");

        services.AddControllers().AddOData(
            options => options.SkipToken().AddRouteComponents(
                "odata",
                modelBuilder.GetEdmModel()));
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();
        app.UseEndpoints(endpoints => endpoints.MapControllers());
    }
}
```

---

The important thing to note here is that we enable the `$skiptoken` query option by calling the `options.SkipToken()` method (we can also enable all query feaures using `options.EnableQueryFeatures(null)`).

## Controllers

We create a simple controller for the `Customers` entity set. We include some dummy data in the controller for simplicity:

```c#
public class CustomersController : ODataController
{
    private static List<Customer> customers = new List<Customer>
    {
        new Customer
        {
            Id = 1,
            Name = "Customer 1"
        },
        new Customer
        {
            Id = 2,
            Name = "Customer 2"
        },
        new Customer
        {
            Id = 3,
            Name = "Customer 3"
        },
        new Customer
        {
            Id = 4,
            Name = "Customer 4"
        },
        new Customer
        {
            Id = 5,
            Name = "Customer 5"
        }
    };

    [EnableQuery(PageSize = 2)]
    public ActionResult Get()
    {
        return customers;
    }
}
```

The important thing to note here is that applied `[EnableQuery]` attribute to our controller's `Get` method and set the `PageSize` property to `2`. This means that the server will send up to two items per response page for requests made to `GET /odata/Customers`.

## Pagination using the default skip token handler

By default, ASP.NET.Core OData uses a default implementation of `SkipTokenHandler` called `DefaultSkipTokenHandler`. This generates skip tokens by encoding keys and values of the last resource in the response.

Let's execute the following request to see an example:

```http
GET /odata/Customers
```

**Response**:

```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Customers",
    "value": [
        {
            "Id": 1,
            "Name": "Customer 1"
        },
        {
            "Id": 2,
            "Name": "Customer 2"
        }
    ],
    "@odata.nextLink": "http://localhost:5000/odata/Customers?$skiptoken=Id-2"
}
```

Notice the skip token `Id-2`, this was generated by the `DefaultSkipTokenHandler` based on the last customer in the resource. It encodes the field `Id` and value `2`. This assumes that the `Id` field has unique value for each customer, it also assumes the collection has a natural order.

If you order the results by a different field, this will also be encoded in the skip token.

For example, let's order the results by name, in descending order:

```http
GET /odata/Customers?$orderby=Name desc
```

> ![NOTE]
> Ensure you enable the `$orderby` query option in your service configuration using `options.OrderBy()` or `options.EnableQueryFeatures(null)`


**Response:**

```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Customers",
    "value": [
        {
            "Id": 5,
            "Name": "Customer 5"
        },
        {
            "Id": 4,
            "Name": "Customer 4"
        }
    ],
    "@odata.nextLink": "http://localhost:5000/odata/Customers?$orderby=Customer%20desc$skiptoken=Name-%27Customer%205%27,Id-4"
}
```

In this case, the skip token encodes both the name and Id of the last customer in the response. It will use the name to determine which customer should appear first in the next page, and if there are customers with the same name, it will use the ID as a tie-breaker.

If you wish to change how skip tokens are generated or how they're processed, you can replace the `DefaultSkipTokenHandler` with your own custom implementation of `SkipTokenHandler`.

## Creating a custom `SkipTokenHandler`

The `SkipTokenHandler` is response for generating the *next link* in an OData response when using server-driven paging. It's also responsible for applying the `$skiptoken` to the results. `SkipTokenHandler` is an abstract exploses and it defines the following methods to be implemented by child classes:

- [`GenerateNextPageLink`](/dotnet/api/microsoft.aspnet.odata.query.skiptokenhandler.generatenextpagelink): generates the `Uri` that's used as the next link in the response
- [`ApplyTo`](/dotnet/api/microsoft.aspnet.odata.query.skiptokenhandler.applyto): applies the `$skiptoken` to an `IQueryable` collection. This should transform the query to perform the pagination. It has two overloads, a generic `ApplyTo<T>` and non-generic `ApplyTo` to handle the generic `IQueryable<T>` and non-generic `IQueryable` respectively.

As an example, let's say we want to represent the skip token using base-64 encoding. This may be useful if we want to "hide" the implementation details and encoding from clients. This is a good use case for a custom `SkipTokenHandler`. We can create our handler from scratch by extending `SkipTokenHandler` directly, or we could extend `DefaultSkipTokenHandler`. The latter is usually more convenient as it allows us to re-use existing logic in `DefaultSkipTokenHandler`. That's the option we'll go with for this sample application.

### Creating and registering the custom handler.

Let's create a class called `CustomSkipTokenHandler` that extends `DefaultSkipTokenHandler`. You can put the class anywhere in the project where it makes sense to you.

```c#
public class CustomSkipTokenHandler : DefaultSkipTokenHandler
{
}
```
To configure our service to use the `CustomSkipTokenHandler`, we have to inject it in the service container. To do this, we'll make use of an [`AddRouteComponents`](/dotnet/api/microsoft.aspnetcore.odata.odataoptions.addroutecomponents) overload that takes an action that configures services.

Let's rewrite our service configuration to look as follows:

```c#
options.SkipToken().AddRouteComponents(
    "odata",
    modelBuilder.GetEdmModel(),
    services => services.AddSingleton<SkipTokenHandler, CustomSkipTokenHandler>())
```

This tells the service to use `CustomSkipTokenHandler` where a `SkipTokenHandler` is required.

With this configuration, requests that require a `$skiptoken` will go through our `CustomSkipTokenHandler`. But this handler behaves exactly like the `DefaultSkipTokenHandler` because we have not overriden any of its method. Let's start by base64-encoding the skip token when generating the next link>

### Customizing the skip token with `GenerateNextLink`
