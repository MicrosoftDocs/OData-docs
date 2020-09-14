---
title : "Getting Started"
description: "This tutorial takes you through how to use the OData Web API authorization library in an OData Web API application"

author: habbes
ms.author: clhabins
ms.date: 8/23/2020
---
# Tutorial: Getting started with OData WebApi Authorization
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)]

This tutorial shows you how to use WebApi Authorization to authorization to your OData WebApi application based on the permission restrictions defined in your OData model.

## Creating the API

- Create an ASP.NET Core 3.1 web application, using the API template. Let's call the application **ODataAuthorizationDemo**
- Install the following NuGet packages:
  - `Microsoft.AspNetCore.OData` (7.4 or later)
  - `Microsoft.EntityFrameworkCore` (we'll use EF Core for interacting with a database)
  - `Microsoft.EntityFrameworkCore.InMemory` (we'll use an in-memory database for this demo)
  - `Microsoft.OData.ModelBuilder` (1.0.3 or later) (we'll use to create the OData model and specify the permission restrictions)
  - `Microsoft.AspNetCore.OData.Authorization` (the WebApi Authorization library) (available in pre-release nightly builds, see install instructions below)

Since `Microsoft.AspNetCore.OData.Authorization` is still a pre-release version, it is currently only available through pre-release nightly builds
on the OData Nightly MyGet feed: https://www.myget.org/F/odatanightly/api/v3/index.json

To install the nightly build, first add the OData Nightly feed to your NuGet package manager sources.

In Visual Studio go to **Tools** -> **Options** -> **NuGet Package Manager** -> **Package Sources**.
Click the Add button and add the following source:
- Name: odatanightly (The name could be anything)
- Source: https://www.myget.org/F/odatanightly/api/v3/index.json

Then go to **Manage NuGet Packages** as you would normally when installing packages. Make sure to check the **Include prerelease** checkbox, then under **Package source** select `odatanightly`.
You should now be able to search for `Microsoft.AspNetCore.OData.Authorization`.

### Create the DB Context and model classes

For demo purposes, we are only going to create one entity: `Product`.

Create a folder called `Models`. Add the following class file to that folder and call it `Product.cs`:

```c#
using System.ComponentModel.DataAnnotations;

namespace ODataAuthorizationDemo.Models
{
    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public int Price { get; set; }
    }
}
```

Next, let's create our EF Core database context. Add an `AppDbContext.cs` file to the `Models` folder with the following code:

```c#
using Microsoft.EntityFrameworkCore;

namespace ODataAuthorizationDemo.Models
{
    public class AppDbContext : DbContext
    {
        public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)
        {
        }

        public DbSet<Product> Products { get; set; }
    }
}
```

### Create the OData Model

For this demo, we'll use the OData ModelBuilder package to create an OData model based on our C# model classes. We'll also use the model builder to add permission restrictions.

Let's add an `AppEdmModel.cs` in the `Models` folder with the following code:

```c#
using Microsoft.OData.Edm;
using Microsoft.OData.ModelBuilder;

namespace ODataAuthorizationDemo.Models
{
    public static class AppEdmModel
    {
        public static IEdmModel GetModel()
        {
            var builder = new ODataConventionModelBuilder();
            var products = builder.EntitySet<Product>("Products");

            products.HasReadRestrictions()
                .HasPermissions(p =>
                    p.HasSchemeName("Scheme").HasScopes(s => s.HasScope("Product.Read")))
                .HasReadByKeyRestrictions(r => r.HasPermissions(p =>
                    p.HasSchemeName("Scheme").HasScopes(s => s.HasScope("Product.ReadByKey"))));

            products.HasInsertRestrictions()
                .HasPermissions(p => p.HasSchemeName("Scheme").HasScopes(s => s.HasScope("Product.Create")));

            products.HasUpdateRestrictions()
                .HasPermissions(p => p.HasSchemeName("Scheme").HasScopes(s => s.HasScope("Product.Update")));

            products.HasDeleteRestrictions()
                .HasPermissions(p => p.HasSchemeName("Scheme").HasScopes(s => s.HasScope("Product.Delete")));

            return builder.GetEdmModel();
        }
    }
}
```

This creates a model with a `Products` entity set based on the `Product` entity type. It adds permission restrictions for different CRUD operations on that entity set, specifying which scopes are required to execute those operations.

- Reading products (`GET /Products`) requires the user to have the scope `Product.Read`
- Reading a single product by its key (`GET /Products(1)`) can also be access with the scope `Product.ReadByKey` in case the user does not have the `Products.Read` scope.
- Creating a new product (`POST /Products(1)`) requires the scope `Product.Create`
- Updating a product (`PATCH /Products(1)`) requires `Product.Update`
- Deleting a product (`DELETE /Products(1)` requires `Product.Delete`)

These restrictions are added as [capability annotations](https://github.com/oasis-tcs/odata-vocabularies/blob/master/vocabularies/Org.OData.Capabilities.V1.md) to the OData model. The authorization middleware will read these annotations to extract permission scopes required for different requests. An operation for which no restrictions have been defined will be allowed by default.


### Configure Startup services

Now let's configure the different services and the app builder in the `Startup.cs` file.

Let's modify the `ConfgiureServices` method so that it looks like this:

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<AppDbContext>(opt => opt.UseInMemoryDatabase("ODataAuthDemo"));

    services.AddOData();

    services.AddRouting();
}
```

And the `Configure` method:

```c#
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.Expand().Filter().Count().OrderBy();
        endpoints.MapODataRoute("odata", "odata", AppEdmModel.GetModel());
    });
}
```

You may need to add the following `using` statements:
```c#
using Microsoft.EntityFrameworkCore;
using Microsoft.AspNet.OData.Extensions;
using ODataAuthorizationDemo.Models;

```

Now if we run the project and visit the `GET /odata/$metadata` endpoint in a tool like Postman, we should see the generated EDM model. The model should contain annotations for the different restriction types that we specified: `ReadRestrictions`, `InsertRestrictions`, `UpdateRestrictions` and `DeleteRestrictions`. Each of these restriction annotations should a `Permissions` property with the scopes that we defined.

Here's an excerpt of the generated annotations:


```xml
 <Annotations Target="Default.Container/Products">
    <Annotation Term="Org.OData.Capabilities.V1.ReadRestrictions">
        <Record>
            <PropertyValue Property="Permissions">
                <Collection>
                    <Record>
                        <PropertyValue Property="SchemeName" String="Scheme" />
                        <PropertyValue Property="Scopes">
                            <Collection>
                                <Record>
                                    <PropertyValue Property="Scope" String="Product.Read" />
                                </Record>
                            </Collection>
                        </PropertyValue>
                    </Record>
                </Collection>
            </PropertyValue>
            <PropertyValue Property="ReadByKeyRestrictions">
                <Record>
                    <PropertyValue Property="Permissions">
                        <Collection>
                            <Record>
                                <PropertyValue Property="SchemeName" String="Scheme" />
                                <PropertyValue Property="Scopes">
                                    <Collection>
                                        <Record>
                                            <PropertyValue Property="Scope" String="Product.ReadByKey" />
                                        </Record>
                                    </Collection>
                                </PropertyValue>
                            </Record>
                        </Collection>
                    </PropertyValue>
                </Record>
            </PropertyValue>
        </Record>
    </Annotation>
...
</Annotations>
```

### Adding the controller

Let's create a `ProductsController` inside the `Controllers` folder to implement our CRUD operations:

```c#
using System.Threading.Tasks;
using Microsoft.AspNet.OData;
using Microsoft.AspNetCore.Mvc;
using ODataAuthorizationDemo.Models;

namespace ODataAuthorizationDemo.Controllers
{
    public class ProductsController: ODataController
    {
        private AppDbContext _dbContext;

        public ProductsController(AppDbContext dbContext)
        {
            _dbContext = dbContext;
        }

        public IActionResult Get()
        {
            return Ok(_dbContext.Products);
        }

        public IActionResult Get(int key)
        {
            return Ok(_dbContext.Products.Find(key));
        }

        public async Task<IActionResult> Post([FromBody] Product product)
        {
            _dbContext.Products.Add(product);
            await _dbContext.SaveChangesAsync();
            return Ok(product);
        }

        public async Task<IActionResult> Update(int key, [FromBody] Delta<Product> delta)
        {
            var product = await _dbContext.Products.FindAsync(key);
            delta.Patch(product);
            _dbContext.Products.Update(product);
            await _dbContext.SaveChangesAsync();
            return Ok(product);
        }

        public async Task<IActionResult> Delete(int key)
        {
            var product = await _dbContext.Products.FindAsync(key);
            _dbContext.Products.Remove(product);
            await _dbContext.SaveChangesAsync();
            return Ok(product);
        }
    }
}
```

At this point you should be able to perform CRUD operations on the `odata/Products` endpoint.
The permission restrictions that are defined in the metadata do not automatically apply and all requests should still be authorized.
To apply these permissions, we'll need to configure the WebApi Authorization middleware. But before we can do that, we'll need to set up authentication.

## Setting up Authentication

While setting up authentication is required for the authorization system to work, WebApi Authorization does not depend on any specific authentication implementation or scheme. For this demo we are going to use a simple cookie-based authentication flow that will make it easy for us to test different scopes and scenarios.

### Configuring Cookie authenticaton

Add the following statement in the `ConfigureServices` method in `Startup.cs` to add authentication to the app:

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie();
```

You may need the following using statements:

```c#
using using Microsoft.AspNetCore.Authentication.Cookies;
```

Add the authentication middleware to in the `Configure` method between `app.UseRouting()` and `app.UseEndpoints` (the order matters):

```c#
app.UseRouting();

app.UseAuthentication();
            
app.UseEndpoints(endpoints => /* ... */)
```

### Authentication controller

Let's create an authentication controller to handle our sign-in and sign-out flows.

To make things easy for the demo, we will not require username/password credentials. The login endpoint will allow the user to specify the scopes
that they want via JSON and add those scopes as claims to the user principal. This will allow us to test how different scopes will be handled for different requests by the authorization middleware.

Our JSON body will look like:

```json
{
    "RequestedScopes": ["Product.Read", "Product.Insert"]
}
```

Let's create a model class to represent such a payload. In the `Models` folder, create a class `LoginData` with the following code:

```c#
namespace ODataAuthorizationDemo.Models
{
    public class LoginData
    {
        public string[] RequestedScopes { get; set; }
    }
}
```

Then, in the `Controllers` folder, create the following `AuthController` class:

```c#
using System.Linq;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using ODataAuthorizationDemo.Models;

namespace ODataAuthorizationDemo.Controllers
{
    [Route("[controller]")]
    [ApiController]
    public class AuthController : ControllerBase
    {
        [HttpPost]
        [Route("login")]
        public async Task<IActionResult> Login([FromBody] LoginData data)
        {
            // create a claim for each requested scope
            var claims = data.RequestedScopes.Select(s => new Claim("Scope", s));

            var claimsIdentity = new ClaimsIdentity(
                claims, CookieAuthenticationDefaults.AuthenticationScheme);

            var user = new ClaimsPrincipal(claimsIdentity);

            await HttpContext.SignInAsync(
                CookieAuthenticationDefaults.AuthenticationScheme,
                user);

            return Ok();
        }

        [HttpPost]
        [Route("logout")]
        public async Task<IActionResult> Logout()
        {
            await HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);

            return Ok();
        }
    }
}
```

To test our login flow, we make the following POST request to the login endpoint using a tool like Postman:

```
POST /auth/login

{
   "RequestedScopes": ["Product.Read", "Product.Delete"]
}
```
This will create the auth cookie.

To log out, we make the following POST request without a body:
```
POST /auth/logout
```

Now that we have authentication set up, we can finally set up authorization.

## Adding authorization

The authorization middleware will compare the scopes that the authenticated user has to the ones required for the current request based on the model's restriction annotations.
Since there are many ways in which the scopes could be stored, we need to tell the middleware how to extract the scopes from the current user.

Modify `ConfigureServices` to match the following:

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<AppDbContext>(opt => opt.UseInMemoryDatabase("ODataAuthDemo"));

    services.AddOData()
        .AddAuthorization(options =>
        {
            options.ScopesFinder = context =>
            {
                var userScopes = context.User.FindAll("Scope").Select(claim => claim.Value);
                return Task.FromResult(userScopes);
            };

            options.ConfigureAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie();
        });

    services.AddRouting();
}
```

In the above code, we add a call to `.AddAuthorization()` to the odata builder in order to add the WebApi Authorization services. We configure it such that it will extract the scopes from the user claims that were added by the authentication system. We do this by providing a handler method to the `options.ScopesFinder` property. We can also configure authentication directly using `options.ConfigureAuthentication`.

We also need to add `app.UseODataAuthorization()` after `app.UseAuthentication()` in the `Configure()` in order to add the middleware:

```c#
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseRouting();

    app.UseAuthentication();

    app.UseODataAuthorization();
    

    app.UseEndpoints(endpoints =>
    {
        endpoints.Expand().Filter().Count().OrderBy();
        endpoints.MapODataRoute("odata", "odata", AppEdmModel.GetModel());
    });
}
```


## Testing

Let's start off by logging in without any scopes:

```
POST /auth/login

{ "RequestedScopes": [] }
```

If we attempt any of the CRUD requests to `/odata/Products`, we should got a `403 Forbidden` response because we don't have the required scopes to access these endpoints.

Now let's logout:
```
POST /auth/logout
```

And request `Product.Read` and `Product.Create` scopes:
```
POST /auth/login

{
    "RequestedScopes": ["Product.Read", "Product.Create"]
}
```

Now we should be able to access `GET /odata/Products`, `GET /odata/Products({key})` and `POST /odata/Products`.

We should get an error if we try to access `DELETE /odata/Products({key})` or `PATCH/PUT /odata/Products({key})`

**Note**: Normally a `403 Forbidden` error response would be returned, but in this sample it might return a `404` error instead. This
is because the cookie authentication handler attempts to redirect to a login page by default when authorization fails. And since this
page does not exist in our sample application, a `404 Not found` error is returned.
