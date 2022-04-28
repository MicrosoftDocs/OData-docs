---
title : "4.9 Query by dynamic properties"
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019

---
# Query by dynamic properties
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Since Web API OData V5.5, it supports filter, select and orderby on dynamic properties.

Let's see a sample about this feature.

### CLR Model

First of all, we create the following CLR classes as our model:

```C#
public class SimpleOpenCustomer
{
    [Key]
    public int CustomerId { get; set; }
    public string Name { get; set; }
    public string Website { get; set; }
    public IDictionary<string, object> CustomerProperties { get; set; }
}

```

### Build Edm Model

Now, we can build the Edm Model as:

```C#
private static IEdmModel GetEdmModel()
{ 
    ODataModelBuilder builder = new ODataConventionModelBuilder();
    builder.EntitySet<SimpleOpenCustomer>("SimpleOpenCustomers");
    return builder.GetEdmModel();
}
```

### Use filter, orderby, select on dynamic property

#### Routing
In the `SimpleOpenCustomersController`, add the following method:

```C#
[EnableQuery]
public IQueryable<SimpleOpenCustomer> Get()
{
    return CreateCustomers().AsQueryable();
}
```

#### Request Samples
We can query like:

```C#
~/odata/SimpleOpenCustomers?$orderby=Token desc&$filter=Token ne null
~/odata/SimpleOpenCustomers?$select=Token
```
