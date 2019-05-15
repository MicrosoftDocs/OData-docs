---

title: " Nested $filter in $expand"
description: ""

ms.date: 03/20/2015
---
# Nested $filter in $expand

[OData Web API](https://github.com/OData/WebApi) v[5.5](https://www.nuget.org/packages/Microsoft.AspNet.OData/5.5.0-beta) supports nested $filter in $expand, e.g.:
`.../Customers?$expand=Orders($filter=Id eq 10)`

POCO classes:
```C#
public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    public IEnumerable<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

With Edm model built as follows:
```C#
var builder = new ODataConventionModelBuilder(config);
builder.EntitySet<Customer>("Customers");
var model = builder.GetEdmModel();
```

To Map route,
- For Microsoft.AspNet.OData, e.g., in `WebApiConfig.cs`:
```C#
config.MapODataServiceRoute("orest", "orest", model);
```

- For Microsoft.AsnNetCore.OData, e.g., in `Startup.Configure((IApplicationBuilder app, IHostingEnvironment env)` method:
```C#
app.UseMvc(routeBuilder => 
    {
        routeBuilder.Select().Expand().Filter().OrderBy().MaxTop(null).Count();
        routeBuilder.MapODataServiceRoute("orest", "orest", model);
    });
```

Controller:
```C#
public class CustomersController : ODataController
{
    private Customer[] _customers =
    {
        new Customer
        {
            Id = 0,
            Name = "abc",
            Orders = new[]
            {
                new Order { Id = 10, Name = "xyz" },
                new Order { Id = 11, Name = "def" },
            }
        }
    };

    [EnableQuery]
    public IHttpActionResult Get()
    {
        return Ok(_customers.AsQueryable());
    }
}
```

Request:
`https://localhost:port_number/orest/Customers?$expand=Orders($filter=Id eq 10)`

Response:
```JSON
{
    "@odata.context": "https://localhost:52953/orest/$metadata#Customers",
    "value": [
        {
            "Id": 0,
            "Name": "abc",
            "Orders": [
                {
                    "Id": 10,
                    "Name": "xyz"
                }
            ]
        }
    ]
}
```
