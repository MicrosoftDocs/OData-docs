---
title: "Automatic support for nested paths withs [EnableNestedPaths]"
description: "Describes how the [EnableNestedPaths] attribute can be used to supported nested paths"
date: 2021-08-09 07:49:00
author: habbes
ms.author: clhabins
ms.date: 8/09/2021
---
# Automatic support for nested paths with [EnableNestedPaths]
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-core-v7.5.md)]

Usually, if you want to handle requests to nested resources, like `GET Customers(1)`, `GET Customers(1)/Orders`, `GET Customers(1)/Orders(1)`, you would need to implemen a separate controller action for each of them. Sometimes, the logic for retrieving an item by key or or a navigation property is as simple as making the corresponding LINQ query. In such cases, it would be handy to have an automated way of handling such requests.

OData WebApi 7.5.9 introduced the `[EnableNestedPaths]` attribute which allows you to handle different `GET` requests that have a common navigation source (i.e. entity set or singleton) using only a single controller action.

## How it works

To use this feature, you implement a `Get()` method in your controller that should return the collection corresponding to the navigation source being requested. For example, if you have an entity set called `Customers` you would implement a `Get()` method (or `GetCustomers()`) inside the `CustomersController` and return the `IQueryable` (or `IEnumerable`) representing the customers collection:

```c#
public class CustomersController {
    [EnableNestedPaths]
    public IQueryable<Customer> Get() {
        return _dbContext.Customers;
    }
}

```

Now this `Get()` methods will handle `GET` requests that start with `Customers/` as the navigation source, e.e.g: `GET Customers`, `GET Customers(1)`, `GET Customers(1)/Orders`, `GET Customers(1)/Orders(1)`, `GET Customers(1)/Orders/$count`, etc.

Under the hood, `[EnableNestedPaths]` will apply LINQ query transformations to the returned collection matching the requested path. For example, if the path contains a key segment, then a where clause will be applied to the query to filter the collection by the specified key. In the case of a navigation property segment, a select clause will be applied. If your returned collection is an `IQueryable` then the resulting query will be handled by the backend query provider, if it's an `IEnumerable` then the query will be evaluated in-memory.

## Support for singletons

In the case of a singleton, you would wrap it with a [`SingleResult<T>`](/dotnet/api/microsoft.aspnet.odata.singleresult-1):

```c#
public class TopCustomerController {
    [EnableNestedPaths]
    public IQueryable<Customer> Get() {
        return new SingleResult<Customer>(new [] { this.topCustomer });
    }
}
```

## Compatibility with `[EnableQuery]`

You can use both `[EnableNestedPaths]` and `[EnableQuery]` on the same controller action:

```c#
public class CustomersController {
    [EnableQuery]
    [EnableNestedPaths]
    public IQueryable<Customer> Get() { /* ... */ }
}
```

 `[EnableNestedPaths]` will process the result first. `[EnableQuery]` will apply the query options to the result returned by `[EnableNestedPaths]`.

## Conflict resolution with other routing conventions

If your controller has other actions that handle specific requests like `Get(int key)`, `GetOrders(int key)`, etc., then those actions will take precedence. The `[EnableNestedPaths]` attribute only handles requests for which no other controller action is set to handle. To illustrate this point, let assume we have the following controller:

```c#
public class CustomersController {
    [EnableNestedpaths]
    public IQueryable<Customer> Get() { /* ... */ }

    public IQueryable<Customer> Get(int key) { /* ... */ }

    public IQueryable<Order> GetOrders(int key) { /* ... */ }
}
```

The following table shows which controller action different requests would be routed to:

Path |  Actions
-----|---------
`/Customers` | `Get()`
`/Customers(1)` | `Get(int key)`
`/Customers(1)/Orders` | `GetOrders(int key)`
`/Customers(1)/Name` | `Get()`
`/Customers(1)/Orders(2)/Name` | `Get()`

## Additional notes and limitations

- The name of the controller action should be either `Get()` or `Get{NavigationSource}`
- `[EnableNestedPaths]` currently does not accept any further configurations. In particular, you can not limit how deeply nested paths can be, you can limit which properties or navigation properties can be accessed, etc.
- `[EnableNestedPaths]` only handles `GET` requests with entity set or singleton navigation sources. It does not handle functions or actions.
- `[EnableNestedPaths]` does not handle $ref requests (i.e. `GET /Customers/1/Orders/1/$ref` will not be routed to the `Get()` method with `[EnableNestedPaths]`)
- This feature is currently only supported on .NET Core (`Microsoft.AspNetCore.OData`)
- This feature is not yet available in `Microsoft.AspNetCore.OData` 8.x