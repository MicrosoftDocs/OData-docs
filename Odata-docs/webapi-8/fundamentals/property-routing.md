---
title:  "Property Routing in ASP.NET Core OData 8"
description: Property Routing in ASP.NET Core OData 8.
date:   2022-12-07
ms.date: 12/7/2022
author: gathogojr
ms.author: jogathog
---

# Property Routing in ASP.NET Core OData 8
**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

This tutorial shows how ASP.NET Core OData 8 supports property routing. An understanding of routing fundamentals in ASP.NET Core OData 8 is assumed. If you're unfamiliar with routing in ASP.NET Core OData 8, you may want to go through the [routing overview](/odata/webapi-8/fundamentals/routing-overview) tutorial.

## Introduction
OData property routing convention supports the following route templates:

| Request Method | Route Template |
|----------------|----------------|
| `GET` \| `PUT` | `~/{entityset}/{key}/{property}` |
| `GET` \| `PUT` | `~/{entityset}/{key}/{cast}/{property}` |
| `GET` \| `PUT` | `~/{entityset}/{key}/{property}/{cast}` |
| `GET` \| `PUT` | `~/{entityset}/{key}/{cast}/{property}/{cast}` |
| `GET` \| `PUT` | `~/{singleton}/{property}` |
| `GET` \| `PUT` | `~/{singleton}/{cast}/{property}` |
| `GET` \| `PUT` | `~/{singleton}/{property}/{cast}` |
| `GET` \| `PUT` | `~/{singleton}/{cast}/{property}/{cast}` |
| `GET` | `~/{entityset}/{key}/{primitiveproperty}/$value` |
| `GET` | `~/{entityset}/{key}/{cast}/{primitiveproperty}/$value` |
| `GET` | `~/{entityset}/{key}/{collectionvaluedproperty}/$count` |
| `GET` | `~/{entityset}/{key}/{cast}/{collectionvaluedproperty}/$count` |
| `GET` | `~/{singleton}/{primitiveproperty}/$value` |
| `GET` | `~/{singleton}/{cast}/{primitiveproperty}/$value` |
| `GET` | `~/{singleton}/{collectionvaluedproperty}/$count` |
| `GET` | `~/{singleton}/{cast}/{collectionvaluedproperty}/$count` |
| `POST` | `~/{entityset}/{key}/{collectionvaluedproperty}` |
| `POST` | `~/{entityset}/{key}/{cast}/{collectionvaluedproperty}` |
| `POST` | `~/{singleton}/{collectionvaluedproperty}` |
| `POST` | `~/{singleton}/{cast}/{collectionvaluedproperty}` |
| `PATCH` | `~/{entityset}/{key}/{singlevaluedproperty}` |
| `PATCH` | `~/{entityset}/{key}/{cast}/{singlevaluedproperty}` |
| `PATCH` | `~/{entityset}/{key}/{singlevaluedproperty}/{cast}` |
| `PATCH` | `~/{entityset}/{key}/{cast}/{singlevaluedproperty}/{cast}` |
| `PATCH` | `~/{singleton}/{singlevaluedproperty}` |
| `PATCH` | `~/{singleton}/{cast}/{singlevaluedproperty}` |
| `PATCH` | `~/{singleton}/{singlevaluedproperty}/{cast}` |
| `PATCH` | `~/{singleton}/{cast}/{singlevaluedproperty}/{cast}` |
| `DELETE` | `~/{entityset}/{key}/{nullableproperty}` |
| `DELETE` | `~/{entityset}/{key}/{cast}/{nullableproperty}` |
| `DELETE` | `~/{singleton}/{nullableproperty}` |
| `DELETE` | `~/{singleton}/{cast}/{nullableproperty}` |

**Notes:**
1. OData routing supports canonical parentheses-style key (e.g. `~/Customers(1)`) in addition to key-as-segment (e.g. `~/Customers/1`). Currently, ASP.NET Core OData 8 does not support key-as-segment convention in multi-part keys scenarios
2. `{cast}` is a placeholder for the fully-qualified name for a derived type

To illustrate property routing convention, let's build a sample OData service.

## Prerequisites

[!INCLUDE[](../../includes/appliesto-webapi-v8-net-prereqs-vs.md)]

## Packages

[!INCLUDE[](../../includes/appliesto-webapi-v8-pkg-install.md)]

## Models
The following are the models for the OData service:

**`Customer` class**
```csharp
namespace PropertyRouting.Models
{
    using System.Collections.Generic;

    public class Customer
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public Address BillingAddress { get; set; }
        public List<string> ContactPhones { get; set; } = new List<string>();
    }
}
```

**`EnterpriseCustomer` class**
```csharp
namespace PropertyRouting.Models
{
    using System.Collections.Generic;

    public class EnterpriseCustomer : Customer
    {
        public decimal CreditLimit { get; set; }
        public Address RegisteredAddress { get; set; }
        public List<Address> ShippingAddresses { get; set; } = new List<Address>();
    }
}
```

**`Address` class**
```csharp
namespace PropertyRouting.Models
{
    public class Address
    {
        public string City { get; set; }
    }
}
```

**`PostalAddress`** class
```csharp
namespace PropertyRouting.Models
{
    public class PostalAddress : Address
    {
        public string PostalCode { get; set; }
    }
}
```

## Edm model and service configuration
The logic for building the Edm model and configuring the OData service is as follows:

# [.NET 6.0](#tab/net60)

```csharp
// Program.cs
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.OData;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.OData.ModelBuilder;
using ActionRouting.Models;

var builder = WebApplication.CreateBuilder(args);

var modelBuilder = new ODataConventionModelBuilder();
modelBuilder.EntitySet<Customer>("Customers");

services.AddControllers().AddOData(
    options => options.EnableQueryFeatures(null).AddRouteComponents(
        routePrefix: "odata",
        model: modelBuilder.GetEdmModel()));

var app = builder.Build();

app.UseODataRouteDebug();
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

        services.AddControllers().AddOData(
            options => options.EnableQueryFeatures(null).AddRouteComponents(
                routePrefix: "odata",
                model: modelBuilder.GetEdmModel()));
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseODataRouteDebug();
        app.UseRouting();
        app.UseEndpoints(endpoints => endpoints.MapControllers());
    }
}
```

---

In the above block of code, we define an entity set named `Customers`. Implicitly, `Customer` and `EnterpriseCustomer` get included in the Edm model as entity types. `Address` and `PostalAddress` also get included as complex types because they do not have key properties.

## Controller
The partial structure of the controller for the OData service is as follows:

```csharp
using System.Collections.Generic;
using System.Linq;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.OData.Deltas;
using Microsoft.AspNetCore.OData.Query;
using Microsoft.AspNetCore.OData.Routing.Controllers;
using PropertyRouting.Models;

public class CustomersController : ODataController
{
    private static List<Customer> customers = new List<Customer>
    {
        new Customer
        {
            Id = 1,
            Name = "Customer 1",
            ContactPhones = new List<string> { "761-116-1865" },
            BillingAddress = new Address { Street = "Street 1A" }
        },
        new Customer
        {
            Id = 2,
            Name = "Customer 2",
            ContactPhones = new List<string> { "835-791-8257" },
            BillingAddress = new PostalAddress { Street = "2A", PostalCode = "14030" }
        },
        new EnterpriseCustomer
        {
            Id = 3,
            Name = "Customer 3",
            ContactPhones = new List<string> { "157-575-6005" },
            BillingAddress = new Address { Street = "Street 3A" },
            CreditLimit = 4200,
            RegisteredAddress = new Address { Street = "Street 3B" },
            ShippingAddresses = new List<Address>
            {
                new Address { Street = "Street 3C" }
            }
        },
        new EnterpriseCustomer
        {
            Id = 4,
            Name = "Customer 4",
            ContactPhones = new List<string> { "724-096-6719" },
            BillingAddress = new Address { Street = "Street 4A" },
            CreditLimit = 3700,
            RegisteredAddress = new PostalAddress { Street = "Street 4B", PostalCode = "22109" },
            ShippingAddresses = new List<Address>
            {
                new Address { Street = "Street 4C" }
            }
        }
    };
}
```

## Routing conventions for properties
In this section we cover the conventions for property routing and the controller actions (endpoints) required for the request to be routed successfully.

### Retrieving a property
The route templates for this request are:
- `GET ~/{entityset}({key})/{property}`
- `GET ~/{entityset}/{key}/{property}`
- `GET ~/{singleton}/{property}`

The following request returns `BillingAddress` property on customer 1:
```http
GET http://localhost:5000/odata/Customers(1)/BillingAddress
```

For the above request to be conventionally-routed, a controller action named `GetBillingAddress` that accepts the key parameter is expected:
```csharp
public ActionResult<Address> GetBillingAddress([FromRoute] int key)
{
    var customer = customers.SingleOrDefault(d => d.Id.Equals(key));

    if (customer == null)
    {
        return NotFound();
    }

    return customer.BillingAddress;
}
```

The following JSON payload shows the expected response:
```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Customers(1)/BillingAddress",
    "Street": "Street 1A"
}
```

### Retrieving a property set to an instance of a derived type
The route templates for this request are:
- `GET ~/{entityset}({key})/{property}/{cast}`
- `GET ~/{entityset}/{key}/{property}/{cast}`
- `GET ~/{singleton}/{property}/{cast}`

The following request returns `BillingAddress` property on customer 2. The property value is an instance of `PostalAddress` - derived from `Address`:
```http
GET http://localhost:5000/odata/Customers(2)/BillingAddress/PropertyRouting.Models.PostalAddress
```

The cast segment (e.g. `/PropertyRouting.Models.PostalAddress`) serves the purpose of explicitly specifying the type of instance expected on the property. If the instance is not of the expected type a [`404 Not Found`](http://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html#sec_AddressingDerivedTypes) response should be returned.

For the above request to be conventionally-routed, a controller action named `GetBillingAddressOfPostalAddress` that accepts the key parameter is expected:
```csharp
public ActionResult<PostalAddress> GetBillingAddressOfPostalAddress([FromRoute] int key)
{
    var customer = customers.SingleOrDefault(d => d.Id.Equals(key));

    if (!(customer?.BillingAddress is PostalAddress billingAddress))
    {
        return NotFound();
    }

    return billingAddress;
}
```

The following JSON payload shows the expected response:
```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Customers(2)/BillingAddress/PropertyRouting.Models.PostalAddress",
    "Street": "2A",
    "PostalCode": "14030"
}
```

Note that the controller action from [retrieving a property](#retrieving-a-property) section can adequately handle request for `BillingAddress` property that is set to an instance of `PostalAddress` derived type. The request would look as follows:
```http
GET http://localhost:5000/odata/Customers(2)/BillingAddress
```

The following JSON payload shows the expected response. Note the difference between the value of `@odata.context` property in the payload below versus the one in preceding payload. In addition, there's an extra `@odata.type` annotation property that serves to specify that the payload is a `PostalAddress`:
```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Customers(2)/BillingAddress",
    "@odata.type": "#PropertyRouting.Models.PostalAddress",
    "Street": "2A",
    "PostalCode": "14030"
}
```

### Retrieving a property declared on a derived type
The route templates for this request are:
- `GET ~/{entityset}({key})/{cast}/{property}`
- `GET ~/{entityset}/{key}/{cast}/{property}`
- `GET ~/{singleton}/{cast}/{property}`

The following request returns `RegisteredAddress` property on enterprise customer 3. The `RegisteredAddress` property is declared on the `EnterpriseCustomer` derived type:
```http
GET http://localhost:5000/odata/Customers(3)/PropertyRouting.Models.EnterpriseCustomer/RegisteredAddress
```

For the above request to be conventionally-routed, a controller action named `GetRegisteredAddressFromEnterpriseCustomer` that accepts the key parameter is expected:
```csharp
public ActionResult<Address> GetRegisteredAddressFromEnterpriseCustomer([FromRoute] int key)
{
    var enterpriseCustomer = customers.OfType<EnterpriseCustomer>().SingleOrDefault(d => d.Id.Equals(key));

    if (enterpriseCustomer == null)
    {
        return NotFound();
    }

    return enterpriseCustomer.RegisteredAddress;
}
```

The following JSON payload shows the expected response:
```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Customers(3)/PropertyRouting.Models.EnterpriseCustomer/RegisteredAddress",
    "Street": "Street 3B"
}
```

### Retrieving a property declared on a derived type and set to an instance of a derived type
The route templates for this request are:
- `GET ~/{entityset}({key})/{cast}/{property}/{cast}`
- `GET ~/{entityset}/{key}/{cast}/{property}/{cast}`
- `GET ~/{singleton}/{cast}/{property}/{cast}`

The following request returns `RegisteredAddress` property  on enterprise customer 4. The property value is an instance of `PostalAddress` derived type:
```http
GET http://localhost:5000/odata/Customers(4)/PropertyRouting.Models.EnterpriseCustomer/RegisteredAddress/PropertyRouting.Models.PostalAddress
```

For the above request to be conventionally-routed, a controller action named `GetRegisteredAddressOfPostalAddressFromEnterpriseCustomer` that accepts the key parameter is expected:
```csharp
public ActionResult<PostalAddress> GetRegisteredAddressOfPostalAddressFromEnterpriseCustomer([FromRoute] int key)
{
    var enterpriseCustomer = customers.OfType<EnterpriseCustomer>().SingleOrDefault(d => d.Id.Equals(key));

    if (!(enterpriseCustomer?.RegisteredAddress is PostalAddress registeredAddress))
    {
        return NotFound();
    }

    return registeredAddress;
}
```

The following JSON payload shows the expected response:
```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Customers(4)/PropertyRouting.Models.EnterpriseCustomer/RegisteredAddress/PropertyRouting.Models.PostalAddress",
    "Street": "Street 4B",
    "PostalCode": "22109"
}
```

Note again that the controller action from [retrieving a property declared on a derived type](#retrieving-a-property-declared-on-a-derived-type) section can adequately handle request for `RegisteredAddress` property that is set to an instance of `PostalAddress` derived type. The request would look as follows:
```http
GET http://localhost:5000/odata/Customers(4)/PropertyRouting.Models.EnterpriseCustomer/RegisteredAddress
```

The following JSON payload shows the expected response. Take note of the value of `@odata.context` property in addition to the extra `@odata.type` annotation property:
```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Customers(4)/PropertyRouting.Models.EnterpriseCustomer/RegisteredAddress",
    "@odata.type": "#PropertyRouting.Models.PostalAddress",
    "Street": "Street 4B",
    "PostalCode": "22109"
}
```

### Retrieving the raw value of a primitive property
The route templates for this request are:
- `GET ~/{entityset}({key})/{primitiveproperty}/$value`
- `GET ~/{entityset}/{key}/{primitiveproperty}/$value`
- `GET ~/{singleton}/{primitiveproperty}/$value`

The result of [retrieving a property](#retrieving-a-property) is a JSON object. For instance, the following request returns the `CreditLimit` primitive property on enterprise customer 3. Note that the `CreditLimit` property is declared on the `EnterpriseCustomer` derived type:
```http
GET http://localhost:5000/odata/Customers(3)/PropertyRouting.Models.EnterpriseCustomer/CreditLimit
```

The response is as follows:
```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Customers(3)/PropertyRouting.Models.EnterpriseCustomer/CreditLimit",
    "value": 4200
}
```

To retrieve just the raw value of the primitive property, append `/$value` to that property's URL.

The following request returns the raw value of the `CreditLimit` primitive property on enterprise customer 3:
```http
GET http://localhost:5000/odata/Customers(3)/PropertyRouting.Models.EnterpriseCustomer/CreditLimit/$value
```

For the above request to be conventionally-routed, a controller action named `GetCreditLimitFromEnterpriseCustomer` that accepts the key parameter is expected:
```csharp
public ActionResult<decimal> GetCreditLimitFromEnterpriseCustomer([FromRoute] int key)
{
    var enterpriseCustomer = customers.OfType<EnterpriseCustomer>().SingleOrDefault(d => d.Id.Equals(key));

    if (enterpriseCustomer == null)
    {
        return NotFound();
    }

    return enterpriseCustomer.CreditLimit;
}
```

The expected response is shown below:
```console
4200
```

### Retrieving the raw value of the number of items in a collection-valued property
To retrieve the raw value of the number of items in a collection-valued property, append `/$count` to that property's URL.

The route templates for this request are:
- `GET ~/{entityset}({key})/{collectionvaluedproperty}/$count`
- `GET ~/{entityset}/{key}/{collectionvaluedproperty}/$count`
- `GET ~/{singleton}/{collectionvaluedproperty}/$count`

The following request returns the raw value of the number of items in the `ContactPhones` collection-valued property on customer 1:
```http
GET http://localhost:5000/odata/Customers(1)/ContactPhones/$count
```

For the above request to be conventionally-routed, a controller action named `GetContactPhones` that accepts the key parameter is expected. The action should be decorated with `EnableQuery` attribute. The `EnableQuery` attribute is responsible for generating the relevant query for determining the number of items:
```csharp
[EnableQuery]
public ActionResult<IEnumerable<string>> GetContactPhones([FromRoute] int key)
{
    var customer = customers.SingleOrDefault(d => d.Id.Equals(key));

    if (customer == null)
    {
        return NotFound();
    }

    return customer.ContactPhones;
}
```

The expected response is shown below:
```console
1
```

### Add an item to a collection-valued property
The route templates for this request are:
- `POST ~/{entityset}({key})/{collectionvaluedproperty}`
- `POST ~/{entityset}/{key}/{collectionvaluedproperty}`
- `POST ~/{singleton}/{collectionvaluedproperty}`

The following `POST` request adds an item to the `ContactPhones` primitive collection-valued property on customer 1:
```http
POST http://localhost:5000/odata/Customers(1)/ContactPhones
```

Here's the request body:
```json
{
    "value": "798-507-2014"
}
```

The payload for adding an item to a primitive collection-valued property should be a JSON object containing a single property named `value`.

For the above request to be conventionally-routed, a controller action named `PostToContactPhones` is expected. The controller action should accept two parameters - the first is the key parameter and the second a parameter of type `string` decorated with `FromBody` attribute:
```csharp
public ActionResult PostToContactPhones([FromRoute] int key, [FromBody] string contactPhone)
{
    var customer = customers.SingleOrDefault(d => d.Id.Equals(key));

    if (customer == null)
    {
        return NotFound();
    }

    customer.ContactPhones.Add(contactPhone);

    return Accepted();
}
```

The response status code should be `201 Created`. You can query for the entity to confirm the result of the `POST` operation.

### Adding an item to a collection-valued property declared on a derived type
The route templates for this request are:
- `POST ~/{entityset}({key})/{cast}/{collectionvaluedproperty}`
- `POST ~/{entityset}/{key}/{cast}/{collectionvaluedproperty}`
- `POST ~/{singleton}/{cast}/{collectionvaluedproperty}`

The following `POST` request adds an item to the `ShippingAddresses` complex collection-valued property on enterprise customer 3. The `ShippingAddresses` property is declared on the `EnterpriseCustomer` derived type:
```http
POST http://localhost:5000/odata/Customers(3)/PropertyRouting.Models.EnterpriseCustomer/ShippingAddresses
```

Here's the request body:
```json
{
    "Street": "One Microsoft Way"
}
```

Unlike [adding an item to a primitive collection-valued property](#add-an-item-to-a-collection-valued-property), the payload for adding an item to a complex collection-valued property is a JSON object of the complex type.

For the above request to be conventionally-routed, a controller action named `PostToShippingAddressesFromEnterpriseCustomer` is expected. The controller action should accept two parameters - the first is the key parameter and the second a parameter of type `Address` decorated with `FromBody` attribute:
```csharp
public ActionResult PostToShippingAddressesFromEnterpriseCustomer([FromRoute] int key, [FromBody] Address address)
{
    var enterpriseCustomer = customers.OfType<EnterpriseCustomer>().SingleOrDefault(d => d.Id.Equals(key));

    if (enterpriseCustomer == null)
    {
        return NotFound();
    }

    enterpriseCustomer.ShippingAddresses.Add(address);

    return Accepted();
}
```

The response status code should be `201 Created`. You can query for the entity to confirm the result of the `POST` operation.

### Updating a primitive property
The route templates for this request are:
- `PUT ~/{entityset}({key})/{property}`
- `PUT ~/{entityset}/{key}/{property}`
- `PUT ~/{singleton}/{property}`

The following `PUT` request updates the `Name` primitive property on customer 1:
```http
PUT http://localhost:5000/odata/Customers(1)/Name
```

Here's the request body:
```json
{
    "value": "Sue"
}
```

The payload for updating a primitive property should be a JSON object containing a single property named `value`.

For the above request to be conventionally-routed, a controller action named `PutToName` is expected. The controller action should accept two parameters - the first is the key parameter and the second a parameter of type `string` decorated with `FromBody` attribute:
```csharp
public ActionResult PutToName([FromRoute] int key, [FromBody] string name)
{
    var customer = customers.SingleOrDefault(d => d.Id.Equals(key));

    if (customer == null)
    {
        return NotFound();
    }

    customer.Name = name;

    return Ok();
}
```

The response status code should be `200 OK`. You can query for the entity to confirm the result of the `PUT` operation.

### Updating a complex property
The route templates for this request are:
- `PUT ~/{entityset}({key})/{property}`
- `PUT ~/{entityset}/{key}/{property}`
- `PUT ~/{singleton}/{property}`

The following `PUT` request updates the `BillingAddress` complex property on customer 1:
```http
PUT http://localhost:5000/odata/Customers(1)/BillingAddress
```

Here's the request body:
```json
{
    "Street": "One Microsoft Way"
}
```

Unlike [updating a primitive property](#updating-a-primitive-property), the payload for updating a complex property is a JSON object of the complex type.

For the above request to be conventionally-routed, a controller action named `PutToBillingAddress` is expected. The controller action should accept two parameters - the first is the key parameter and the second a parameter of type `Address` decorated with `FromBody` attribute:
```csharp
public ActionResult PutToBillingAddress([FromRoute] int key, [FromBody] Address address)
{
    var customer = customers.SingleOrDefault(d => d.Id.Equals(key));

    if (customer == null)
    {
        return NotFound();
    }

    customer.BillingAddress = address;

    return Ok();
}
```

The response status code should be `200 OK`. You can query for the entity to confirm the result of the `PUT` operation.

### Updating a collection-valued property
The route templates for this request are:
- `PUT ~/{entityset}({key})/{property}`
- `PUT ~/{entityset}/{key}/{property}`
- `PUT ~/{singleton}/{property}`

The following `PUT` request updates the `ContactPhones` collection-valued property on customer 1:
```http
PUT http://localhost:5000/odata/Customers(1)/ContactPhones
```

Here's the request body:
```json
{
    "value": ["804-855-4049", "491-9198-476"]
}
```

For the above request to be conventionally-routed, a controller action named `PutToContactPhones` is expected. The controller action should accept two parameters - the first is the key parameter and the second a parameter of type `IEnumerable<string>` decorated with `FromBody` attribute:
```csharp
public ActionResult PutToContactPhones([FromRoute] int key, [FromBody] IEnumerable<string> contactPhones)
{
    var customer = customers.SingleOrDefault(d => d.Id.Equals(key));

    if (customer == null)
    {
        return NotFound();
    }

    customer.ContactPhones = contactPhones?.ToList();

    return Ok();
}
```

The use of `IEnumerable<T>` as the parameter type is not accidental; other concrete generic types like `List` or `Collection` will cause an `InvalidCastException` to be thrown.

The response status code should be `200 OK`. You can query for the entity to confirm the result of the `PUT` operation.

### Updating a property declared on a derived type
The route templates for this request are:
- `PUT ~/{entityset}({key})/{cast}/{property}`
- `PUT ~/{entityset}/{key}/{cast}/{property}`
- `PUT ~/{singleton}/{cast}/{property}`

The following `PUT` request updates the `RegisteredAddress` property on enterprise customer 3:
```http
PUT http://localhost:5000/odata/Customers(3)/PropertyRouting.Models.EnterpriseCustomer/RegisteredAddress
```

Here's the request body:
```json
{
    "Street": "One Microsoft Way"
}
```

For the above request to be conventionally-routed, a controller action named `PutToRegisteredAddressFromEnterpriseCustomer` is expected. The controller action should accept two parameters - the first is the key parameter and the second a parameter of type `Address` decorated with `FromBody` attribute:
```csharp
public ActionResult PutToRegisteredAddressFromEnterpriseCustomer([FromRoute] int key, [FromBody] Address address)
{
    var enterpriseCustomer = customers.OfType<EnterpriseCustomer>().SingleOrDefault(d => d.Id.Equals(key));

    if (enterpriseCustomer == null)
    {
        return NotFound();
    }

    enterpriseCustomer.RegisteredAddress = address;

    return Ok();
}
```

The response status code should be `200 OK`. You can query for the entity to confirm the result of the `PUT` operation.

### Updating a property declared on a derived type and set to an instance of a derived type
The route templates for this request are:
- `PUT ~/{entityset}({key})/{cast}/{property}/{cast}`
- `PUT ~/{entityset}/{key}/{cast}/{property}/{cast}`
- `PUT ~/{singleton}/{cast}/{property}/{cast}`

The following `PUT` request updates the `RegisteredAddress` property on enterprise customer 4. The property value is an instance of `PostalAddress` derived type:
```http
PUT http://localhost:5000/odata/Customers(4)/PropertyRouting.Models.EnterpriseCustomer/RegisteredAddress/PropertyRouting.Models.PostalAddress
```

Here's the request body:
```json
{
    "Street": "One Microsoft Way",
    "PostalCode": "98052"
}
```

For the above request to be conventionally-routed, a controller action named `PutToRegisteredAddressOfPostalAddressFromEnterpriseCustomer` is expected. The controller action should accept two parameters - the first is the key parameter and the second a parameter of type `PostalAddress` decorated with `FromBody` attribute:
```csharp
public ActionResult PutToRegisteredAddressOfPostalAddressFromEnterpriseCustomer([FromRoute] int key, [FromBody] PostalAddress registeredAddress)
{
    var enterpriseCustomer = customers.OfType<EnterpriseCustomer>().SingleOrDefault(d => d.Id.Equals(key));

    if (enterpriseCustomer == null)
    {
        return NotFound();
    }

    enterpriseCustomer.RegisteredAddress = registeredAddress;

    return Ok();
}
```

The response status code should be `200 OK`. You can query for the entity to confirm the result of the `PUT` operation.

Alternatively, the `PutToRegisteredAddressFromEnterpriseCustomer` controller action defined in [updating a property declared on a derived type](#updating-a-property-declared-on-a-derived-type) section can be used to achieve the same objective. The request below would be routed to `PutToRegisteredAddressFromEnterpriseCustomer` controller action:
```http
PUT http://localhost:5000/odata/Customers(3)/PropertyRouting.Models.EnterpriseCustomer/RegisteredAddress
```

Here's the request body. The `@odata.type` annotation is used to specify that the payload is a `PostalAddress`:
```json
{
    "@odata.type": "#PropertyRouting.Models.PostalAddress",
    "Street": "One Microsoft Way",
    "PostalCode": "98052"
}
```

### Patching a complex property
You can only patch single-valued complex properties.

The route templates for this request are:
- `PATCH ~/{entityset}({key})/{singlevaluedproperty}`
- `PATCH ~/{entityset}/{key}/{singlevaluedproperty}`
- `PATCH ~/{singleton}/{singlevaluedproperty}`

The following `PATCH` request patches the `BillingAddress` property on customer 1:
```http
PATCH http://localhost:5000/odata/Customers(1)/BillingAddress
```

Here's the request body:
```json
{
    "Street": "One Microsoft Way"
}
```

For the above request to be conventionally-routed, a controller action named `PatchToBillingAddress` is expected. The controller action should accept two parameters - the first is the key parameter and the second a parameter of type `Delta<Address>` decorated with `FromBody` attribute:
```csharp
public ActionResult PatchToBillingAddress([FromRoute] int key, [FromBody] Delta<Address> delta)
{
    var customer = customers.SingleOrDefault(d => d.Id.Equals(key));

    if (customer == null)
    {
        return NotFound();
    }

    delta.Patch(customer.BillingAddress);

    return Ok();
}
```

The response status code should be `200 OK`. You can query for the entity to confirm the result of the `PATCH` operation.

### Patching a complex property set to an instance of a derived type
The route templates for this request are:
- `PATCH ~/{entityset}({key})/{singlevaluedproperty}/{cast}`
- `PATCH ~/{entityset}/{key}/{singlevaluedproperty}/{cast}`
- `PATCH ~/{singleton}/{singlevaluedproperty}/{cast}`

The following `PATCH` request patches the `BillingAddress` property on customer 2. The property value is an instance of `PostalAddress` derived type:
```http
PATCH http://localhost:5000/odata/Customers(2)/BillingAddress/PropertyRouting.Models.PostalAddress
```

Here's the request body:
```json
{
    "Street": "One Microsoft Way",
    "PostalCode": "98052"
}
```

For the above request to be conventionally-routed, a controller action named `PatchToBillingAddressOfPostalAddress` is expected. The controller action should accept two parameters - the first is the key parameter and the second a parameter of type `Delta<PostalAddress>` decorated with `FromBody` attribute:
```csharp
public ActionResult PatchToBillingAddressOfPostalAddress([FromRoute] int key, [FromBody] Delta<PostalAddress> delta)
{
    var customer = customers.SingleOrDefault(d => d.Id.Equals(key));

    if (!(customer?.BillingAddress is PostalAddress billingAddress))
    {
        return NotFound();
    }

    delta.Patch(billingAddress);

    return Ok();
}
```

The response status code should be `200 OK`. You can query for the entity to confirm the result of the `PATCH` operation.

Alternatively, the `PatchToBillingAddress` controller action defined in [patching a complex property](#patching-a-complex-property) section can be used to achieve the same objective. The request below would be routed to `PatchToBillingAddress` controller action:
```http
PATCH http://localhost:5000/odata/Customers(2)/BillingAddress
```

Here's the request body. The `@odata.type` annotation is used to specify that the payload is a `PostalAddress`:
```json
{
    "@odata.type": "#PropertyRouting.Models.PostalAddress",
    "Street": "One Microsoft Way",
    "PostalCode": "98052"
}
```

### Patching a complex property declared on a derived type
The route templates for this request are:
- `PATCH ~/{entityset}({key})/{cast}/{singlevaluedproperty}`
- `PATCH ~/{entityset}/{key}/{cast}/{singlevaluedproperty}`
- `PATCH ~/{singleton}/{cast}/{singlevaluedproperty}`

The following `PATCH` request patches the `RegisteredAddress` property on enterprise customer 3:
```http
PATCH http://localhost:5000/odata/Customers(3)/PropertyRouting.Models.EnterpriseCustomer/RegisteredAddress
```

Here's the request body:
```json
{
    "Street": "One Microsoft Way"
}
```

For the above request to be conventionally-routed, a controller action named `PatchToRegisteredAddressFromEnterpriseCustomer` is expected. The controller action should accept two parameters - the first is the key parameter and the second a parameter of type `Delta<Address>` decorated with `FromBody` attribute:
```csharp
public ActionResult PatchToRegisteredAddressFromEnterpriseCustomer([FromRoute] int key, [FromBody] Delta<Address> delta)
{
    var enterpriseCustomer = customers.OfType<EnterpriseCustomer>().SingleOrDefault(d => d.Id.Equals(key));

    if (enterpriseCustomer == null)
    {
        return NotFound();
    }

    delta.Patch(enterpriseCustomer.RegisteredAddress);

    return Ok();
}
```

The response status code should be `200 OK`. You can query for the entity to confirm the result of the `PATCH` operation.

### Patching a complex property declared on a derived type and set to an instance of a derived type
The route templates for this request are:
- `PATCH ~/{entityset}({key})/{cast}/{singlevaluedproperty}/{cast}`
- `PATCH ~/{entityset}/{key}/{cast}/{singlevaluedproperty}/{cast}`
- `PATCH ~/{singleton}/{cast}/{singlevaluedproperty}/{cast}`

The following `PATCH` request patches the `RegisteredAddress` property on enterprise customer 4. The property value is an instance of `PostalAddress` derived type:
```http
PATCH http://localhost:5000/odata/Customers(4)/PropertyRouting.Models.EnterpriseCustomer/RegisteredAddress/PropertyRouting.Models.PostalAddress
```

Here's the request body:
```json
{
    "Street": "One Microsoft Way",
    "PostalCode": "98052"
}
```

For the above request to be conventionally-routed, a controller action named `PatchToRegisteredAddressOfPostalAddressFromEnterpriseCustomer` is expected. The controller action should accept two parameters - the first is the key parameter and the second a parameter of type `Delta<PostalAddress>` decorated with `FromBody` attribute:
```csharp
public ActionResult PatchToRegisteredAddressOfPostalAddressFromEnterpriseCustomer([FromRoute] int key, [FromBody] Delta<PostalAddress> delta)
{
    var enterpriseCustomer = customers.OfType<EnterpriseCustomer>().SingleOrDefault(d => d.Id.Equals(key));

    if (!(enterpriseCustomer?.RegisteredAddress is PostalAddress registeredAddress))
    {
        return NotFound();
    }

    delta.Patch(registeredAddress);

    return Ok();
}
```

The response status code should be `200 OK`. You can query for the entity to confirm the result of the `PATCH` operation.

Alternatively, the `PatchToRegisteredAddressFromEnterpriseCustomer` controller action defined in [patching a complex property declared on a derived type](#patching-a-complex-property-declared-on-a-derived-type) section can be used to achieve the same objective. The request below would be routed to `PatchToRegisteredAddressFromEnterpriseCustomer` controller action:
```http
PATCH http://localhost:5000/odata/Customers(4)/PropertyRouting.Models.EnterpriseCustomer/RegisteredAddress
```

Here's the request body. The `@odata.type` annotation is used to specify that the payload is a `PostalAddress`:
```json
{
    "@odata.type": "#PropertyRouting.Models.PostalAddress",
    "Street": "One Microsoft Way",
    "PostalCode": "98052"
}
```

### Deleting a nullable property
You can only delete a nullable primitive or complex property. Deleting a property involves setting its value to `null`.

The route templates for this request are:
- `DELETE ~/{entityset}({key})/{nullableproperty}`
- `DELETE ~/{entityset}/{key}/{nullableproperty}`
- `DELETE ~/{singleton}/{nullableproperty}`

The following `DELETE` request deletes the `BillingAddress` property on customer 1:
```http
DELETE http://localhost:5000/odata/Customers(1)/BillingAddress
```

The request body is empty.

For the above request to be conventionally-routed, a controller action named `DeleteToBillingAddress` that accepts the key parameter is expected:
```csharp
public ActionResult DeleteToBillingAddress([FromRoute] int key)
{
    var customer = customers.SingleOrDefault(d => d.Id.Equals(key));

    if (customer == null)
    {
        return NotFound();
    }

    customer.BillingAddress = null;

    return NoContent();
}
```

The response status code should be `204`. You can query for the entity to confirm the result of the `DELETE` operation.

### Deleting a nullable property declared on a derived type
The route templates for this request are:
- `DELETE ~/{entityset}({key})/{cast}/{nullableproperty}`
- `DELETE ~/{entityset}/{key}/{cast}/{nullableproperty}`
- `DELETE ~/{singleton}/{cast}/{nullableproperty}`

The following `DELETE` request deletes the `RegisteredAddress` property on customer 3:
```http
DELETE http://localhost:5000/odata/Customers(3)/PropertyRouting.Models.EnterpriseCustomer/RegisteredAddress
```

The request body is empty.

For the above request to be conventionally-routed, a controller action named `DeleteToRegisteredAddressFromEnterpriseCustomer` that accepts the key parameter is expected:
```csharp
public ActionResult DeleteToRegisteredAddressFromEnterpriseCustomer([FromRoute] int key)
{
    var enterpriseCustomer = customers.OfType<EnterpriseCustomer>().SingleOrDefault(d => d.Id.Equals(key));

    if (enterpriseCustomer == null)
    {
        return NotFound();
    }

    enterpriseCustomer.RegisteredAddress = null;

    return NoContent();
}
```

The response status code should be `204`. You can query for the entity to confirm the result of the `DELETE` operation.

## Property routing endpoint mappings
If you went through this tutorial and implemented the logic in an OData service, you can run the application and visit the `$odata` endpoint (http://localhost:5000/$odata) to view the endpoint mappings.
