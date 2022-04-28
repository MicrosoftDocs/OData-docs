---
title: "Unit Test and E2E Test"
description: ""
ms.date: 7/1/2019
ms.author: saumadan
author: madansr7
---
# Unit Test and E2E Test
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

In OData WebApi, there are unit tests, e2e tests for V3 and V4, those [test cases](https://github.com/OData/WebApi/tree/master/OData/test) are to verify that features and bug fixes work as intended, and also to make sure they do not break existing functionality.

### Unit Tests
Every class in OData WebApi has its own unit test class, for example:
OData/src/System.Web.OData/OData/Builder/ActionLinkBuilder.cs 's test class is 
OData/test/UnitTest/System.Web.OData.Test/OData/Builder/ActionLinkBuilderTests.cs

You can find that the structures under the `System.Web.OData` folder and the`System.Web.OData.Test` folder are the same, and the same goes for V3 `System.Web.Http.OData.Test`. So if your pull request contains any class addition/change, you should add/change (here "change" means adding test cases) the corresponding unit test file.

#### How To Add Unit Test
* Try to avoid other dependency, use moq.
* Make sure you add/change the right class (V4 or V3 or both).
* Can add functional test for complicated scenarios, but E2E test cases are better.


### E2E Tests
E2E test are complete test for user scenarios, always begin with client request and end with server response. If your unit test in pull request can't cover all scenario well or you have a big pull request, please add E2E test for it.

#### How To Add E2E Test
* Add test cases in existing test class that are related to your pull request.
* Add new folder and test class for your own scenario.
* If the test has any kind of state that is preserved between requests, it should be the only test defined in the test class in order to avoid conflicts when executed along other tests.
* Try to test with both in-memory data and DB data.
* Keep the test folder and class style consistent with existing test folders and classes.


#### Test Sample

```C#
[NuwaFramework]
public class MyTest
{

    [NuwaBaseAddress]
    public string BaseAddress { get; set; }

    [NuwaHttpClient]
    public HttpClient Client { get; set; }

    [NuwaConfiguration]
    public static void UpdateConfiguration(HttpConfiguration config)
    {
        config.Routes.MapODataRoute("odata", "odata", GetModel());
    }     

    private static IEdmModel GetModel()
    {
        ODataModelBuilder builder = new ODataConventionModelBuilder();
        var customers = builder.EntitySet<Customer>("Customers");
        var orders = builder.EntitySet<Order>("Orders");
        return builder.GetEdmModel();
    }

    [Fact]
    public void GetCustomersWork()
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get,BaseAddress + "/odata/Customers");
        HttpResponseMessage response = Client.SendAsync(request).Result;
        Assert.Equal(HttpStatusCode.OK,response.StatusCode);
    }
}

[NuwaFramework]
public class MyTest2
{
    [NuwaBaseAddress]
    public string BaseAddress { get; set; }

    [NuwaHttpClient]
    public HttpClient Client { get; set; }

    [NuwaConfiguration]
    public static void UpdateConfiguration(HttpConfiguration config)
    {
        config.Routes.MapODataRoute("odata", "odata", GetModel());
    }

    private static IEdmModel GetModel()
    {
        ODataModelBuilder builder = new ODataConventionModelBuilder();
        var customers = builder.EntitySet<Customer>("Customers");
        var orders = builder.EntitySet<Order>("Orders");
        return builder.GetEdmModel();
    }

    [Fact]
    public void GetCustomersWork()
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, BaseAddress + "/odata/Customers");
        HttpResponseMessage response = Client.SendAsync(request).Result;
        Assert.Equal(HttpStatusCode.OK, response.StatusCode);
    }
}

public class CustomersController : ODataController
{
    [Queryable(PageSize = 3)]
    public IHttpActionResult Get()
    {
        return Ok(Enumerable.Range(0, 10).Select(i => new Customer
        {
            Id = i,
            Name = "Name " + i
        }));
    }

    public IHttpActionResult Post(Customer customer)
    {
        return Created(customer);
    }
}

public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    public IList<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public DateTime PurchaseDate { get; set; }
}
```
