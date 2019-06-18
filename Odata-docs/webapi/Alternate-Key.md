---
title: "Alternate keys in webapi v7"
ms.date: 08/11/2015
---
# Alternate keys
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Alternate keys is supported in Web API OData V5.7. For detail information about alternate keys, please refer to [here](https://github.com/OData/vocabularies/blob/master/OData.Community.Keys.V1.md)

The related sample codes can be found [here](https://github.com/OData/ODataSamples/tree/master/WebApi/v4/ODataAlternateKeySamples)

### Enable Alternate key 

Users can enable alternate key in the global configuration.
```C#
HttpConfiguration config = ...
config.EnableAlternateKeys(true);
config.MapODataServiceRoute(...)
```

### Model builder

So far, an Edm model with alternate keys can be built by ODL APIs. 
```C#
EdmEntityType customer = new EdmEntityType("NS", "Customer"); 
customer.AddKeys(customer.AddStructuralProperty("ID", EdmPrimitiveTypeKind.Int32)); 
customer.AddStructuralProperty("Name", EdmPrimitiveTypeKind.String); 
var ssn = customer.AddStructuralProperty("SSN", EdmPrimitiveTypeKind.String); 
model.AddAlternateKeyAnnotation(customer, new Dictionary<string, IEdmProperty> 
{ 
    {"SSN", ssn} 
}); 
model.AddElement(customer); 
```
So, SSN is an alternate key.

### Routing for alternate key

In OData controller, Users can use the attribute routing to route the alternate key. The Uri template is similar to function parameter. For example:
```C#
[HttpGet] 
[ODataRoute("Customers(SSN={ssn})")] 
public IHttpActionResult GetCustomerBySSN([FromODataUri]string ssn)
{
   ...
}
```
