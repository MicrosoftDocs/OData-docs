---
title : "4.9 Query by dynamic properties"
layout: post
category: "4. OData features"
---

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

### Use filter, orferby, select on dynamic property

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
