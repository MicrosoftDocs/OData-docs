---
title:  "Customizing Skip Tokens with SkipTokenHandler"
description: A tutorial on how to customize generated skip token with a custom SkipTokenHandler class.
date:   2023-01-19
ms.date: 01/19/2023
author: habbes
ms.author: clhabins
---

# Customizing skip tokens with `SkipTokenHandler`

**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

Skip tokens are used in server-side paging to keep track of the last record that was sent the client so that it can generate the next page of results. The skip token is opaque to the client, this means that the server has freedom to decide what the contents of the skip token should be and how to interpret them. ASP.NET Core OData generates by default the skip token by combining the fields and values of the the a reference record. However, you can override this behavior by providing your own custom logic for generating and processing skip tokens.

This tutorial shows how to customize the skip token logic using a custom `SkipTokenHandler`.

You'll learn:
:white_check_mark: How ASP.NET Core OData uses `SkipTokenHandler` to generate and handle next links.  
:white_check_mark: How to create a custom `SkipTokenHandler` that extends the `DefaultSkipTokenHandler`
:white_check_mark: How to configure an ASP.NET Core OData application to use your custom handler

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

The important thing to note here is that we enable the `$skiptoken` query option by calling the `options.SkipToken()` method (we can also enable all query features using `options.EnableQueryFeatures(null)`).

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

> [!NOTE]
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

> [!IMPORTANT]
> You should not rely on the `DefaultSkipTokenHandler` generating the skip token in a specific format. We make no guarantee that this format may not change between library versions.

If you wish to change how skip tokens are generated or how they're processed, you can replace the `DefaultSkipTokenHandler` with your own custom implementation of `SkipTokenHandler`.

## Creating a custom `SkipTokenHandler`

The `SkipTokenHandler` generates the *next link* in an OData response when using server-driven paging. It's also responsible for applying the `$skiptoken` to the results. `SkipTokenHandler` is an abstract class that defines the following methods to be implemented by child classes:

- [`GenerateNextPageLink`](/dotnet/api/microsoft.aspnet.odata.query.skiptokenhandler.generatenextpagelink): generates the `Uri` that's used as the next link in the response
- [`ApplyTo`](/dotnet/api/microsoft.aspnet.odata.query.skiptokenhandler.applyto): applies the `$skiptoken` to an `IQueryable` collection. This should transform the query to perform the pagination. It has two overloads, a generic `ApplyTo<T>` and non-generic `ApplyTo` to handle the generic `IQueryable<T>` and non-generic `IQueryable` respectively.

As an example, let's say we want to represent the skip token using base-64 encoding. This may be useful if we want to "hide" the implementation details and encoding from clients. This is a good use case for a custom `SkipTokenHandler`. We can create our handler from scratch by extending `SkipTokenHandler` directly, or we could extend `DefaultSkipTokenHandler`. The latter is usually more convenient as it allows us to re-use existing logic in `DefaultSkipTokenHandler`. That's the option we'll go with for this sample application.

### Creating and registering the custom handler

Let's create a class called `CustomSkipTokenHandler` that extends `DefaultSkipTokenHandler`. You can put the class anywhere in the project where it makes sense to you.

```c#
using Microsoft.AspNetCore.OData.Query;

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

With this configuration, requests that require a `$skiptoken` will go through our `CustomSkipTokenHandler`. But this handler behaves exactly like the `DefaultSkipTokenHandler` because we have not overridden any of its method. Let's start by base64-encoding the skip token when generating the next link.

### Customizing the skip token with `GenerateNextPageLink`

We'll need to override the `GenerateNextPageLink` method. This method returns a `Uri` that will be used as the next link. The URI should contain the `$skiptoken` if applicable. Since we only want to base-64 encode the default skip token, we can leverage the `GenerateNextPageLink` of the parent `DefaultSkipTokenHandler` class to generate the URI, then extract the skip token and encode it.

We can implement that with the following code:

```c#

public override Uri GenerateNextPageLink(
    Uri baseUri, 
    int pageSize, 
    object instance, 
    ODataSerializerContext context)
{
    var uri = base.GenerateNextPageLink(baseUri, pageSize, instance, context);

    if (uri == null)
    {
        return null;
    }
    
    var queryOptions = HttpUtility.ParseQueryString(uri.Query);
    string skipToken = queryOptions.Get("$skiptoken");
    if (skipToken == null)
    {
        return uri;
    }

    string base64SkipToken = Convert.ToBase64String(Encoding.UTF8.GetBytes(skipToken));
    queryOptions.Set("$skiptoken", base64SkipToken);
    
    string encodedQuery = queryOptions.ToString();
    UriBuilder builder = new UriBuilder(uri);
    builder.Query = encodedQuery;
    return builder.Uri;
}
```

Now let's explain what this code does.

The `GenerateNextPageLink` takes the following arguments:

- `baseUri`: This is the request URL. This is the URL we want to add a `$skiptoken` to. It's possible that it contains the `$skiptoken` from the previous page.
- `pageSize`: The maximum number of items per page. This is the same value that was passed to the `PageSize` property.
- `instance`: The instance based on which to generate the skip token. It corresponds to the last item in the previous page.
- `context`: Allows you to access information about the current serialization pipeline, such as the request instance, the EDM model, etc.

The method first calls the `GenerateNextPageLink` method of the parent class (`DefaultSkipTokenHandler`) to generate the next link. The method returns `null` if we're on the last page.

```c#
var uri = base.GenerateNextPageLink(baseUri, pageSize, instance, context);

if (uri == null)
{
    return null;
}
```

The next block of code extracts the value of the `$skiptoken` query option from the generated URI. We use the `HttpUtility` class from the `System.Web` namespace to parse the query options.

```c#
var queryOptions = HttpUtility.ParseQueryString(uri.Query);
string skipToken = queryOptions.Get("$skiptoken");
if (skipToken == null)
{
    return uri;
}
```

The next block of code generates a base64 encoded version of the skip token to replace the original value.

```c#
string base64SkipToken = Convert.ToBase64String(Encoding.UTF8.GetBytes(skipToken));
queryOptions.Set("$skiptoken", base64SkipToken);
```

Finally, we create a new URI by replacing the original query options with the updated version:

```c#
string encodedQuery = queryOptions.ToString();
UriBuilder builder = new UriBuilder(uri);
builder.Query = encodedQuery;
return builder.Uri;
```

### Testing the new next link

Let's make the following request to see the new skip tokens in action:

```http
GET /odata/Customers
```

**Response**:

```json
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
    "@odata.nextLink": "http://localhost:5000/odata/Customers?$skiptoken=SWQtMg%3d%3d"
}
```

The `$skiptoken` is now `SWQtMg%3d%3d` instead of `Id-2`.

> [!NOTE]
> The actual base64 encoding of `Id-2` is `SWQtMg==`, but the final form eventually gets URL encoded to `SWQtMg%3d%3d`.

Let's follow the next link to fetch the next page:

```http
GET http://localhost:5000/odata/Customers?$skiptoken=SWQtMg%3d%3d
```

This returns an error response like the following:

```json
{
    "error": {
        "code": "",
        "message": "The query specified in the URI is not valid. Unable to parse the skiptoken value 'SWQtMg=='. Skiptoken value should always be server generated.",
    }
}
```

The reason we get the error is because our custom handler was not able to process the skip token. The skip token is processed in the `ApplyTo` method. At the moment, we are still using the `ApplyTo` method in the `DefaultSkipTokenHandler` class because we have not defined our own. The base `ApplyTo` expects the skip token to be in a specific format, it cannot parse the base64 encoded value.

### Processing the skip token with `ApplyTo`

To fix the error in the previous section we have to decode the skip token first then pass the decoded version to the `DefaultSkipTokenHandler`.

We can achieve that by adding the following methods to our `CustomSkipTokenHandler` class:

```c#
public override IQueryable<T> ApplyTo<T>(IQueryable<T> query, SkipTokenQueryOption skipTokenQueryOption, ODataQuerySettings querySettings, ODataQueryOptions queryOptions)
{
    var decodedSkipTokenQueryOption = DecodeSkipToken(skipTokenQueryOption);
    return base.ApplyTo(query, decodedSkipTokenQueryOption, querySettings, queryOptions);
}

public override IQueryable ApplyTo(IQueryable query, SkipTokenQueryOption skipTokenQueryOption, ODataQuerySettings querySettings, ODataQueryOptions queryOptions)
{
    var decodedSkipTokenQueryOption = DecodeSkipToken(skipTokenQueryOption);
    return base.ApplyTo(query, decodedSkipTokenQueryOption, querySettings, queryOptions);
}

private static SkipTokenQueryOption DecodeSkipToken(SkipTokenQueryOption skipTokenQueryOption)
{
    string encodedSkipToken = skipTokenQueryOption.RawValue;
    string decodedSkipToken = Encoding.UTF8.GetString(Convert.FromBase64String(encodedSkipToken));
    return new SkipTokenQueryOption(decodedSkipToken, skipTokenQueryOption.Context);
}
```

We override the `ApplyTo` methods with our custom logic. We override both the generic `ApplyTo` and generic `ApplyTo<T>` overloads. The methods accept the following parameters:

- `query`: The `IQueryable` or `IQueryable<T>` that represents the query that will fetch the results.
- `skipTokenQueryOption`: Contains information about the `$skiptoken` query option.
- `querySettings`: The query settings to use while applying the `$skiptoken` (e.g. the page size).
- `queryOptions`: Contains information about the other query options in the request.

The `ApplyTo` method should transform the `query` and return a new query that implements the pagination logic, i.e. ensures that we only return records from where we left off in the last page, and that we do not exceed the page size. Luckily, the `DefaultSkipTokenHandler` already takes care of this logic.

Our implementation simply decodes the query option and lets the base class take care of the rest:

```c#
var decodedSkipTokenQueryOption = DecodeSkipToken(skipTokenQueryOption);
return base.ApplyTo(query, decodedSkipTokenQueryOption, querySettings, queryOptions);
```

To decode the skip token, we created a private helper method `DecodeSkipToken`. This method extracts the value of the `$skiptoken`, decodes it from its base64 encoding then creates a new `SkipTokenQueryOption` instance with the decoded value:

```c#
string encodedSkipToken = skipTokenQueryOption.RawValue;
string decodedSkipToken = Encoding.UTF8.GetString(Convert.FromBase64String(encodedSkipToken));
return new SkipTokenQueryOption(decodedSkipToken, skipTokenQueryOption.Context);
```

Now let's try to fetch the second page again:

```http
GET http://localhost:5000/odata/Customers?$skiptoken=SWQtMg%3d%3d
```

**Response:**

```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Customers",
    "value": [
        {
            "Id": 3,
            "Name": "Customer 3"
        },
        {
            "Id": 4,
            "Name": "Customer 4"
        }
    ],
    "@odata.nextLink": "http://localhost:5000/odata/Customers?$skiptoken=SWQtNA%3d%3d"
}
```

Now we get a successful response!

### Complete `CustomSkipTokenHandler` source code

Here's the full source code for our `CustomSkipTokenHandler`:

```c#
using System;
using System.Linq;
using System.Text;
using System.Web;
using Microsoft.AspNetCore.OData.Formatter.Serialization;
using Microsoft.AspNetCore.OData.Query;

namespace ODataRoutingSample
{
    public class CustomSkipTokenHandler : DefaultSkipTokenHandler
    {
        public override IQueryable<T> ApplyTo<T>(IQueryable<T> query, SkipTokenQueryOption skipTokenQueryOption, ODataQuerySettings querySettings, ODataQueryOptions queryOptions)
        {
            var decodedSkipTokenQueryOption = DecodeSkipToken(skipTokenQueryOption);
            return base.ApplyTo(query, decodedSkipTokenQueryOption, querySettings, queryOptions);
        }

        public override IQueryable ApplyTo(IQueryable query, SkipTokenQueryOption skipTokenQueryOption, ODataQuerySettings querySettings, ODataQueryOptions queryOptions)
        {
            var decodedSkipTokenQueryOption = DecodeSkipToken(skipTokenQueryOption);
            return base.ApplyTo(query, decodedSkipTokenQueryOption, querySettings, queryOptions);
        }

        public override Uri GenerateNextPageLink(
            Uri baseUri,
            int pageSize,
            object instance,
            ODataSerializerContext context)
        {
            var uri = base.GenerateNextPageLink(baseUri, pageSize, instance, context);

            if (uri == null)
            {
                return null;
            }

            var queryOptions = HttpUtility.ParseQueryString(uri.Query);
            string skipToken = queryOptions.Get("$skiptoken");
            if (skipToken == null)
            {
                return uri;
            }

            string base64SkipToken = Convert.ToBase64String(Encoding.UTF8.GetBytes(skipToken));
            queryOptions.Set("$skiptoken", base64SkipToken);

            string encodedQuery = queryOptions.ToString();
            UriBuilder builder = new UriBuilder(uri);
            builder.Query = encodedQuery;
            return builder.Uri;
        }

        private static SkipTokenQueryOption DecodeSkipToken(SkipTokenQueryOption skipTokenQueryOption)
        {
            string encodedSkipToken = skipTokenQueryOption.RawValue;
            string decodedSkipToken = Encoding.UTF8.GetString(Convert.FromBase64String(encodedSkipToken));
            return new SkipTokenQueryOption(decodedSkipToken, skipTokenQueryOption.Context);
        }
    }
}
```
