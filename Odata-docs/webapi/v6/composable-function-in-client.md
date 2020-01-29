---
title: "Composable function in Client"
description: ""

author: madansr7
ms.author: madansr7
ms.date: 7/1/2019
ms.topic: article
 
---
# Composable function in client
**Applies To**: [!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v6.md)]

Composable function (function import) can have additional path segments and query options as appropriate for the returned type.

## Unbound composable function

For example, we have model:

```C#

<Function Name="GetAllProducts" IsComposable="true">
  <ReturnType Type="Collection(NS.Product)" Nullable="false" />
</Function>
<Action Name="Discount" IsBound="true" EntitySetPath="products">
  <Parameter Name="products" Type="Collection(NS.Product)" Nullable="false" />
  <Parameter Name="percentage" Type="Edm.Int32" Nullable="false" />
  <ReturnType Type="Collection(NS.Product)" Nullable="false" />
</Action>
...
<FunctionImport Name="GetAllProducts" Function="NS.GetAllProducts" EntitySet="Products" IncludeInServiceDocument="true" />
```

`GetAllProducts` is a function import and it is composable. And since action `Discount` accepts what `GetAllProducts` returns, we can query `Discount` after `GetAllProducts`.

1. Create function query

``` c#
var products = Context.CreateFunctionQuery<Product>("", "GetAllProducts", true).Execute();
```

And we can append query option to the function. For example:

``` c#
var products = Context.CreateFunctionQuery<ProductPlus>("", "GetAllProducts", true).AddQueryOption("$select", "Name").Execute();
```

The actual query would be:
```html

GET https://localhost/GetAllProducts()?$select=Name
```

2. With codegen

With [OData client generator](https://devblogs.microsoft.com/odata/tutorial-sample-how-to-use-odata-client-code-generator-to-generate-client-side-proxy-class/), proxy class for function and action would be auto generated.
For example:

``` c#
var getAllProductsFunction = Context.GetAllProductsPlus();
var products = getAllProductsFunction.Execute();   // Get products 
var discdProducts = getAllProductsFunction.DiscountPlus(50).Execute();   // Call action on function
var filteredProducts = getAllProductsFunction.Where(p => p.SkinColorPlus == ColorPlus.RedPlus).Execute();   //Add query option 
```

## Bound composable function

Bound composable function has similiar usage, except that it is tied to a resource.

For example, we have model:
``` c#

<Function Name="GetSeniorEmployees" IsBound="true" EntitySetPath="People" IsComposable="true">
    <Parameter Name="employees" Type="Collection(NS.Employee)" Nullable="false" />
    <ReturnType Type="NS.Employee" />
</Function>
<Function Name="GetHomeAddress" IsBound="true" IsComposable="true">
    <Parameter Name="person" Type="NS.Person" Nullable="false" />
    <ReturnType Type="NS.HomeAddress" Nullable="false" />
</Function>
```

Person is the base type of Employee.
Then a sample query is:
``` c#
(Context.People.OfType<Employee>() as DataServiceQuery<Employee>).GetSeniorEmployees().GetHomeAddress().GetValue();
```
