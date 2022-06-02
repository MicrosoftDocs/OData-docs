---
title : "4.13 Delta Feed Support"
description: Describes how to create a serialized Delta Feed.
ms.date: 7/1/2019
author: madansr7
---
# Delta Feed Support
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

## Serialization Support for Delta Feed

This sample will introduce how to create a [Delta Feed](https://docs.oasis-open.org/odata/odata-json-format/v4.0/errata02/os/odata-json-format-v4.0-errata02-os-complete.html#_Toc403940644) which is serialized into a Delta Response in Web API OData V4.

Similar to [EdmEntityObjectCollection](https://github.com/OData/WebApi/blob/master/src/Microsoft.AspNet.OData.Shared/EdmEntityCollectionObject.cs), [Web API OData V5.6](https://www.nuget.org/packages/Microsoft.AspNet.OData/5.6.0-beta1) now has an [EdmChangedObjectCollection](https://github.com/OData/WebApi/blob/master/src/Microsoft.AspNet.OData.Shared/EdmChangedObjectCollection.cs) to represent a collection of objects which can be a part of the ***Delta Feed***.
A delta response can contain <i>[new/changed entities](https://docs.oasis-open.org/odata/odata-json-format/v4.0/errata02/os/odata-json-format-v4.0-errata02-os-complete.html#_Toc403940645)</i>, <i>[deleted entities](https://docs.oasis-open.org/odata/odata-json-format/v4.0/errata02/os/odata-json-format-v4.0-errata02-os-complete.html#_Toc403940646)</i>, <i>[new links](https://docs.oasis-open.org/odata/odata-json-format/v4.0/errata02/os/odata-json-format-v4.0-errata02-os-complete.html#_Toc403940647)</i> or <i>[deleted links](https://docs.oasis-open.org/odata/odata-json-format/v4.0/errata02/os/odata-json-format-v4.0-errata02-os-complete.html#_Toc403940648)</i>.

WebAPI OData V4 now has [EdmDeltaEntityObject](https://github.com/OData/WebApi/blob/master/src/Microsoft.AspNet.OData.Shared/EdmDeltaEntityObject.cs), [EdmDeltaDeletedEntityObject](https://github.com/OData/WebApi/blob/master/src/Microsoft.AspNet.OData.Shared/EdmDeltaDeletedEntityObject.cs), [EdmDeltaLink](https://github.com/OData/WebApi/blob/master/src/Microsoft.AspNet.OData.Shared/EdmDeltaLink.cs) and [EdmDeltaDeletedLink](https://github.com/OData/WebApi/blob/master/src/Microsoft.AspNet.OData.Shared/EdmDeltaDeletedLink.cs) respectively for the objects that can be a part of the Delta response.
All the above objects implement the [IEdmChangedObject](https://github.com/OData/WebApi/blob/master/src/Microsoft.AspNet.OData.Shared/IEdmChangedObject.cs) interface, while the `EdmChangedObjectCollection` is a collection of `IEdmChangedObject`.

For example, if user defines a model as:

```C#
public class Customer
{
  public int Id { get; set; }
  public string Name { get; set; }
  public virtual IList<Order> Orders { get; set; }
}
public class Order
{
  public int Id { get; set; }
  public string ShippingAddress { get; set; }
}
private static IEdmModel GetEdmModel()
{
  ODataModelBuilder builder = new ODataConventionModelBuilder();
  var customers = builder.EntitySet<Customer>("Customers");
  var orders = builder.EntitySet<Order>("Orders");
  return builder.GetEdmModel();
}
```

The `EdmChangedObjectCollection` collection for Customer entity will be created as follows:
```C#
EdmChangedObjectCollection changedCollection = new EdmChangedObjectCollection(CustomerType); //IEdmEntityType of Customer
```

Changed or Modified objects are added as `EdmDeltaEntityObject`s:
```C#
EdmDeltaEntityObject Customer = new EdmDeltaEntityObject(CustomerType); 
Customer.Id = 123;
Customer.Name = "Added Customer";
changedCollection.Add(Customer);
```

Deleted objects are added as `EdmDeltaDeletedObject`s:
```C#
EdmDeltaDeletedEntityObject Customer = new EdmDeltaDeletedEntityObject(CustomerType);
Customer.Id = 123;
Customer.Reason = DeltaDeletedEntryReason.Deleted;
changedCollection.Add(Customer);
```

Delta Link is added corresponding to a $expand in the initial request, these are added as `EdmDeltaLink`s:
```C#
EdmDeltaLink CustomerOrderLink = new EdmDeltaLink(CustomerType);
CustomerOrderLink.Relationship = "Orders";
CustomerOrderLink.Source = new Uri(ServiceBaseUri, "Customers(123)");	
CustomerOrderLink.Target = new Uri(ServiceBaseUri, "Orders(10)");
changedCollection.Add(CustomerOrderLink);
```

Deleted Links is added for each deleted link that corresponds to a $expand path in the initial request, these are added as `EdmDeltaDeletedLink`s:
```C#
EdmDeltaDeletedLink CustomerOrderDeletedLink = new EdmDeltaDeletedLink(CustomerType);
CustomerOrderDeletedLink.Relationship = "Orders";
CustomerOrderDeletedLink.Source = new Uri(ServiceBaseUri, "Customers(123)");
CustomerOrderDeletedLink.Target = new Uri(ServiceBaseUri, "Orders(10)");
changedCollection.Add(CustomerOrderDeletedLink);
```

### Sample for Delta Feed
Let's create a controller to return a Delta Feed:
```C#
public class CustomersController : ODataController
{
  public IHttpActionResult Get()
  {
    EdmChangedObjectCollection changedCollection = new EdmChangedObjectCollection(CustomerType);
    EdmDeltaEntityObject Customer = new EdmDeltaEntityObject(CustomerType); 
    Customer.Id = 123;
    Customer.Name = "Added Customer";
    changedCollection.Add(Customer);
    
    EdmDeltaDeletedEntityObject Customer = new EdmDeltaDeletedEntityObject(CustomerType);
    Customer.Id = 124;
    Customer.Reason = DeltaDeletedEntryReason.Deleted;
    changedCollection.Add(Customer);

    EdmDeltaLink CustomerOrderLink = new EdmDeltaLink(CustomerType);
    CustomerOrderLink.Relationship = "Orders";
    CustomerOrderLink.Source = new Uri(ServiceBaseUri, "Customers(123)");
    CustomerOrderLink.Target = new Uri(ServiceBaseUri, "Orders(10)");
    changedCollection.Add(CustomerOrderLink);
    
    EdmDeltaDeletedLink CustomerOrderDeletedLink = new EdmDeltaDeletedLink(CustomerType);
    CustomerOrderDeletedLink.Relationship = "Orders";
    CustomerOrderDeletedLink.Source = new Uri(ServiceBaseUri, "Customers(123)");
    CustomerOrderDeletedLink.Target = new Uri(ServiceBaseUri, "Orders(11)");
    changedCollection.Add(CustomerOrderDeletedLink);
    return Ok(changedCollection);
  }
}
```

Now, user can issue a ***GET*** request as:

```html
https://localhost/odata/Customers?$expand=Orders&$deltatoken=abc
```

The corresponding payload will has the following contents:
```JSON
{
  "@odata.context":"https://localhost/odata/$metadata#Customers",
  "value": [
    {
      "Id":123,
      "Name":"Added Customer"
    },
    {
      "@odata.context":"https://localhost/odata/$metadata#Customers/$deletedEntity",
      "Id": 124
      "Reason":"Deleted"
    },
    {
      "@odata.context":"https://localhost/odata/$metadata#Customers/$link",
      "source":"Customers(123)",
      "relationship":"Orders",
      "target":"Orders(10)"
    },
    {
     	"@odata.context":"https://localhost/odata/$metadata#Customers/$deletedLink",
     	"source":"Customers(123)",
     	"relationship":"Orders",
     	"target":"Orders(11)"
    }
  ]
}
```
