---
title: "OData Query Options in ASP.NET Core OData 8"
description: "This documentation describes how to use the supported query options."

author: KenitoInc
ms.author: kemunga

ms.date: 12/16/2022
---
# OData Query Options in ASP.NET Core OData 8
**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

ASP.NET Core OData 8.x provides out of the box querying capabilities.

A query option is basically requesting that a service perform a set of transformations such as filtering, sorting, etc. to its data before returning the results. 

There are various types of query options:
- `System query options` - Query options that are defined by OData. System query options are prefixed with the dollar ($) character, which is optional in OData 4.01. Example is `$filter`.
- `Parameter aliases` - Parameter aliases can be used in place of literal values in entity keys, function parameters, or within a $compute, $filter or $orderby expression. Example `http://host/service.svc/Employees?$filter=Region eq @p1&@p1='WA'`.
- `Custom query options` - Custom query options are not defined in the OData specification. They are defined by the users. They MUST NOT begin with the `$` or `@` character and MUST NOT conflict with any OData-defined system query options. Example `http://host/service/Products?debug-mode=true`.

See more information on the types of query options [here](/odata/concepts/queryoptions-overview).

This tutorial demonstrates how to use query options in ASP.NET Core OData 8.x.

## Model classes
We will define our model as follows:

```csharp
public class Customer
{
    [Key]
    public int Id { get; set; }
    public String Name { get; set; }
    public int Age { get; set; }
    public List<Order> Orders { get; set; }
}

public class Order
{
    [Key]
    public int Id { get; set; }
    public int Price { get; set; }
    public int Quantity { get; set; }
}
```

We have a `Customer` entity with `Orders` navigation property. We define a one to many relationship between Customer and Order entities. One `Customer` can have one or more `Orders`. Read more on [OData data model](/odata/concepts/data-model) and [Entity relations](/odata/odatalib/edm/define-entity-relations).

## Registering OData services

# [.NET 6.0](#tab/net60)

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

var modelBuilder = new ODataConventionModelBuilder();
modelBuilder.EntitySet<Customer>("Customers");

builder.Services.AddControllers().AddOData(
    options => options.Select().Filter().OrderBy().Expand().Count().SetMaxTop(null).AddRouteComponents(
        routePrefix: "odata",
        model: modelBuilder.GetEdmModel()));

var app = builder.Build();
```

# [.NET Core 3.1](#tab/netcoreapp31)

```csharp
// Startup.cs
public void ConfigureServices(IServiceCollection services)
{
    var modelBuilder = new ODataConventionModelBuilder();
    modelBuilder.EntitySet<Customer>("Customers");

    services.AddControllers().AddOData(
            options => options.Select().Filter().OrderBy().Expand().Count().SetMaxTop(null).AddRouteComponents(
                "odata",
                modelBuilder.GetEdmModel()));
}
```

---

In the above configuration, `options` is an instance of `ODataOptions`.
- [Select()](/dotnet/api/microsoft.aspnetcore.odata.odataoptions.select) enables `$select` query option.
- [Filter()](/dotnet/api/microsoft.aspnetcore.odata.odataoptions.filter) enables `$filter` query option.
- [OrderBy()](/dotnet/api/microsoft.aspnetcore.odata.odataoptions.orderby) enables `$orderby` query option.
- [Expand()](/dotnet/api/microsoft.aspnetcore.odata.odataoptions.expand) enables `$expand` query option.
- [Count()](/dotnet/api/microsoft.aspnetcore.odata.odataoptions.count) enables `$count` query option.
- [SetMaxTop()](/dotnet/api/microsoft.aspnetcore.odata.odataoptions.setmaxtop) sets the maximum value of `$top` that a client can request. Read more in the [client-driven paging documentation](/odata/webapi-8/fundamentals/client-driven-paging)

If you want to enable all query options at once, you would call `options.EnableQueryFeatures()`.

## Controller

```csharp
public class CustomersController : ODataController
{
    private static List<Order> Orders = new List<Order>
    {
        new Order { Id = "1001", Price = 10, Quantity = 10 },
        new Order { Id = "1002", Price = 35, Quantity = 2 },
        new Order { Id = "1003", Price = 70, Quantity = 5 },
        new Order { Id = "1004", Price = 20, Quantity = 20 },
        new Order { Id = "1005", Price = 40, Quantity = 15 },
        new Order { Id = "1006", Price = 15, Quantity = 50 },
    };

    private static List<Customer> Customers = new List<Customer>
    {
        new Customer { Id = 1, Name = "Customer 1", Age = 31, Orders = new List<Order>(){Orders[0], Orders[1]} },
        new Customer { Id = 2, Name = "Customer 2", Age = 32, Orders = new List<Order>(){Orders[2], Orders[3]} },
        new Customer { Id = 3, Name = "Customer 3", Age = 33, Orders = new List<Order>(){Orders[4], Orders[5]} }
    };

    [EnableQuery]
    public IActionResult Get()
    {
        return Ok(Customers);
    }
}
```

In the preceding code, the `Get` controller method is decorated with the `EnableQuery` attribute.
The `EnableQuery` attribute is an action filter that parses, validates, and applies the query. The filter converts the query options into a [LINQ (Language-Integrated Query)](/dotnet/csharp/programming-guide/concepts/linq) expression. When the controller returns an `IQueryable` or `IActionResult` type, the LINQ provider converts the LINQ expression into a query, e.g., Entity Framework (EF) Core will convert the LINQ expression into an SQL statement.

## Basic queries
We can select a specific property or properties in an entity:

```http
GET http://localhost:6285/odata/Customers?$select=Name
```

```json
{
    "@odata.context": "http://localhost:6285/odata/$metadata#Customers(Name)",
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

We can return `Customers` with their related `Orders`. In this case we use `$expand`:

```http
GET http://localhost:6285/odata/Customers?$expand=Orders
```

```json
{
    "@odata.context": "http://localhost:6285/odata/$metadata#Customers(Orders())",
    "value": [
        {
            "Id": 1,
            "Name": "Customer 1",
            "Age": 31,
            "Orders": [
                {
                    "Id": 1002,
                    "Price": 35,
                    "Quantity": 2
                },
                {
                    "Id": 1001,
                    "Price": 10,
                    "Quantity": 10
                }
            ]
        },
        {
            "Id": 2,
            "Name": "Customer 2",
            "Age": 32,
            "Orders": [
                {
                    "Id": 1004,
                    "Price": 20,
                    "Quantity": 20
                },
                {
                    "Id": 1003,
                    "Price": 70,
                    "Quantity": 5
                }
            ]
        },
        {
            "Id": 3,
            "Name": "Customer 3",
            "Age": 33,
            "Orders": [
                {
                    "Id": 1006,
                    "Price": 15,
                    "Quantity": 50
                },
                {
                    "Id": 1005,
                    "Price": 40,
                    "Quantity": 15
                }
            ]
        }
    ]
}
```

We can combine various query options.
In the request below:
- Select only the `Name` property from `Customer` entity.
- Expand the related `Orders`.
- Filter the `Orders` and return only the ones whose `Id` is greater than `1004`.
- Order the `Customers` by `Name` in descending order.

```http
http://localhost:6285/odata/Customers?$select=Name&$expand=Orders($filter=Id gt 1004)&$orderby=Name desc
```

```json
{
    "@odata.context": "http://localhost:6285/odata/$metadata#Customers(Name,Orders())",
    "value": [
        {
            "Name": "Customer 3",
            "Orders": [
                {
                    "Id": 1006,
                    "Price": 15,
                    "Quantity": 50
                },
                {
                    "Id": 1005,
                    "Price": 40,
                    "Quantity": 15
                }
            ]
        },
        {
            "Name": "Customer 2",
            "Orders": []
        },
        {
            "Name": "Customer 1",
            "Orders": []
        }
    ]
}
```

### Limiting query options
We can limit the type of query option that can be used while calling a certain API.

```csharp
public class CustomersController : ODataController
{
    [EnableQuery(AllowedQueryOptions = AllowedQueryOptions.Filter)]
    public IActionResult Get()
    {
        return Ok(Customers);
    }
}
```

In the controller method above, we only allow `$filter` query option. If we use another query option e.g `$select`, we will get an error.

```http
GET http://localhost:6285/odata/Customers?$select=Name
```

```json
{
    "error": {
        "code": "",
        "message": "The query specified in the URI is not valid. Query option 'Select' is not allowed. To allow it, set the 'AllowedQueryOptions' property on EnableQueryAttribute or QueryValidationSettings.",
        "details": [],
        "innererror": {}
    }
}
```

We can combine multiple query options as shown below:

```csharp
[EnableQuery(AllowedQueryOptions = AllowedQueryOptions.Filter | AllowedQueryOptions.OrderBy)]
public IQueryable<Customer> Get()
{
    return Customers.AsQueryable<Customer>();
}
```

## Configured vs allowed query options
When registering OData services, we add query options configurations as shown below:

```csharp
.AddOData(
    options => options.Select().OrderBy().Expand().Count().SetMaxTop(null)
)
```

These are global configurations. If a query option is not enabled here, it cannot be enabled in the controller.

In the above example, we did not enable the `$filter` query option. If we configure a controller as shown below, the `$filter` will not be applied since it's not enabled in the global configuration.

```csharp
[EnableQuery(AllowedQueryOptions = AllowedQueryOptions.Filter)]
public IActionResult Get()
{
    return Ok(Customers);
}
```

## Applying query options directly
There are scenarios where we may be unable to use `EnableQuery` attribute. For example, if our controller fetches data from multiple data sources and not just a single `IQueryable`, we may want to control how the query options are applied and process the data before it's returned by the controller.

We use the [ODataQueryOptions.ApplyTo](/dotnet/api/microsoft.aspnet.odata.query.odataqueryoptions.applyto) method to apply the required query options.

[ODataQueryOptions<`TEntity`>](/dotnet/api/microsoft.aspnet.odata.query.odataqueryoptions-1) can be used an argument in a controller method:

```csharp
public IQueryable<Customer> Get(ODataQueryOptions<Customer> options)
{
    IQueryable results = options.ApplyTo(Customers.AsQueryable());

    return results as IQueryable<Customer>;
}
```

In the example above, the [ApplyTo](/dotnet/api/microsoft.aspnet.odata.query.odataqueryoptions.applyto) method applies to all query options.

We can call `ApplyTo` on individual query options as shown in the subsections below.

### SelectExpand query
We can call `ApplyTo` on the `SelectExpand` property of `ODataQueryOptions` class as follows:

```csharp
public IQueryable<Customer> Get(ODataQueryOptions<Customer> options)
{
    IQueryable results = options.SelectExpand.ApplyTo(Customers.AsQueryable(), new ODataQuerySettings());

    return results as IQueryable<Customer>;
}
```

`SelectExpand` handles both `$select` and `$expand` query options.

The `options.SelectExpand` property is an instance of `SelectExpandQueryOption`. It will be set when we make the request below:

```http
GET http://localhost:6285/odata/Customers?$expand=Orders
```

If we don't want to pass the query option in the request, but we want the `Orders` property to be expanded, we can initialize the `SelectExpandQueryOption` and set it in the request URL.

```csharp
public IQueryable<Customer> Get(ODataQueryOptions<Customer> options)
{
    options.Request.ODataFeature().SelectExpandClause = new SelectExpandQueryOption(null, "Orders", options.Context,
        new ODataQueryOptionParser(
            model: options.Context.Model,
            targetEdmType: options.Context.NavigationSource.EntityType(),
            targetNavigationSource: options.Context.NavigationSource,
            queryOptions: new Dictionary<string, string> { { "$expand", "Orders" } },
            container: options.Context.RequestContainer)).SelectExpandClause;

    IQueryable results = options.ApplyTo(Customers.AsQueryable());

    return results as IQueryable<Customer>;
}
```

When making the request, we don't need to include the `$expand` query option.

```http
GET http://localhost:6285/odata/Customers
```

Below will be the output:

```json
{
    "@odata.context": "http://localhost:6285/odata/$metadata#Customers(Orders())",
    "value": [
        {
            "Id": 1,
            "Name": "Customer 1",
            "Age": 31,
            "Orders": [
                {
                    "Id": 1001,
                    "Price": 10,
                    "Quantity": 10
                },
                {
                    "Id": 1002,
                    "Price": 35,
                    "Quantity": 2
                }
            ]
        },
        ...
        ...
    ]
}
```

### Filter query
We can call `ApplyTo` on the `Filter` property of `ODataQueryOptions` class as follows:

```csharp
public IQueryable<Customer> Get(ODataQueryOptions<Customer> options)
{
    IQueryable results = options.Filter.ApplyTo(Customers.AsQueryable(), new ODataQuerySettings());

    return results as IQueryable<Customer>;
}
```

The `options.Filter` property is an instance of `FilterQueryOption`. It will be set when we make the request below:

```http
GET http://localhost:6285/odata/Customers?$filter=Id eq 1
```

If we don't want to pass the filter query option in the request URL, but we want a filter to be applied, we can initialize the `FilterQueryOption` and call `ApplyTo` to apply the filter query to the data.

```csharp
public IQueryable<Customer> Get(ODataQueryOptions<Customer> options)
{
    var filter = new FilterQueryOption("Id eq 1", options.Context,
        new ODataQueryOptionParser(
            model: options.Context.Model,
            targetEdmType: options.Context.NavigationSource.EntityType(),
            targetNavigationSource: options.Context.NavigationSource,
            queryOptions: new Dictionary<string, string> { { "$filter", "Id eq 1" } },
            container: options.Context.RequestContainer));

    IQueryable results = filter.ApplyTo(Customers.AsQueryable(), new ODataQuerySettings());

    return results as IQueryable<Customer>;
}
```
When making the request, we don't need to include the `$filter` query option.

```http
GET http://localhost:6285/odata/Customers
```

Below will be the output:

```json
{
    "@odata.context": "http://localhost:6285/odata/$metadata#Customers",
    "value": [
        {
            "Id": 1,
            "Name": "Customer 1",
            "Age": 31
        }
    ]
}
```

### Apply query
The `$apply` query option is used in grouping and aggregating data.

We can call `ApplyTo` on the `Apply` property of `ODataQueryOptions` class as follows:

```csharp
public IQueryable<Customer> Get(ODataQueryOptions<Customer> options)
{
    IQueryable results = options.Apply.ApplyTo(Customers.AsQueryable(), new ODataQuerySettings());

    return results as IQueryable<Customer>;
}
```

The `options.Apply` property is an instance of `ApplyQueryOption`. It will be set when we make the request below:

```http
GET http://localhost:6285/odata/Customers?$apply=aggregate(Age with max as MaxAge)
```

If we don't want to pass the query option in the request, but we want to aggregate the results, we can initialize the `ApplyQueryOption` and set it in the request.

```csharp
public IQueryable<Customer> Get(ODataQueryOptions<Customer> options)
{
    var queryOptionParser = new ODataQueryOptionParser(
            model: options.Context.Model,
            targetEdmType: options.Context.NavigationSource.EntityType(),
            targetNavigationSource: options.Context.NavigationSource,
            queryOptions: new Dictionary<string, string> { { "$apply", "aggregate(Age with max as MaxAge)" } },
            container: options.Context.RequestContainer);

    options.Request.ODataFeature().ApplyClause = new ApplyQueryOption("aggregate(Age with max as MaxAge)", options.Context, queryOptionParser).ApplyClause;

    IQueryable results = options.ApplyTo(Customers.AsQueryable());

    return results as IQueryable<Customer>;
}
```

## Extending the EnableQueryAttribute class
The `EnableQueryAttribute` class can be extended.

`EnableQueryAttribute` class has several public virtual methods that can be overridden.

- `ValidateQuery` - performs query validation before query execution. In this method, we call each query option's `Validate` method and use the appropriate validator class to perform validation.
- `ApplyQuery` - triggers `queryOption.ApplyTo()`.
- `GetModel` - returns an `IEdmModel`.

```csharp
public class CustomEnableQueryAttribute : EnableQueryAttribute
{
    public override void ValidateQuery(HttpRequestMessage request, ODataQueryOptions queryOptions)
    {
        if (queryOptions.Filter != null)
        {
            queryOptions.Filter.Validator = new MyFilterValidator();
        }
        base.ValidateQuery(request, queryOptions);
    }
}

public class MyFilterValidator : FilterQueryValidator
{
    public override void Validate(FilterQueryOption filterOption, ODataValidationSettings validationSettings)
    {
        ValidateRangeVariable(filterOption.FilterClause.RangeVariable, validationSettings);

        base.Validate(filterOption, validationSettings);
    }

    public override void ValidateRangeVariable(RangeVariable rangeVariable, ODataValidationSettings settings)
    {
        // Add your custom logic to Validate RangeVariable
    }
}
```

The `CustomEnableQuery` attribute can be applied to a controller action as follows:

```csharp
[CustomEnableQuery]
public IQueryable<Customer> Get()
{
    return Customers.AsQueryable<Customer>();
}
```

The `MyFilterValidator` will only be used in `$filter` queries. In other queries, the default validators will be used.
