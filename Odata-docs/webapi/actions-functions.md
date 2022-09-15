---
title: "Actions and functions"
description: "This documentation describes how to add actions and functions."

author: KenitoInc
ms.author: kemunga

ms.date: 9/15/2022
---
# Actions and functions
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

In OData, actions and functions are a way to add server-side behaviors that are not easily defined as CRUD operations on entities. This documentation shows how to add actions and functions to an OData v4 endpoint, using Web API v8.x.

## What are actions and functions?
- Functions are operations exposed by an OData service that MUST return data and MUST have no observable side effects. They may support further composition. Functions are invoked by using HTTP GET requests.
- Actions are operations exposed by an OData service that MAY have side effects when invoked. Actions have a side effect on the server, so they are invoked by using HTTP POST requests. They cannot be further composed in order to avoid non-deterministic behavior.

## Difference between bound and unbound operations.
From OData V4 spec, functions and actions can be either bound to a type or unbound.
Bound operations are bound to an entity type, primitive type, complex type, or a collection.
Unbound operations are invoked as static operations on the service.

## Practical examples
We will add detailed examples on how to configure bound and unbound functions and actions.
We will start by setting up the OData WebAPI 8.x project.

### CLR classes
```csharp
public class Book
{
    public string ID { get; set; }
    public string Title { get; set; }
    public bool ForKids { get; set; }
}

public class BookRating
{
    public string ID { get; set; }
    public int Rating { get; set; }
    public string BookID { get; set; }
}
```

### Controller
```csharp
public class BooksController : ODataController
{
    // Get ~/Books
    [EnableQuery]
    public IActionResult Get()
    {
        return Ok(DataSource.Instance.Books);
    }
}
```

### Build Edm Model
```csharp
private static IEdmModel GetEdmModel()
{
    var builder = new ODataConventionModelBuilder();
    builder.EntitySet<Book>("books");
    var model = builder.GetEdmModel();
    return model;
}
```

## Bound Function
Bound functions can be bound to an entity type or a collection of entity type.
In our example model above, the bound function can be bound to a single book or a collection of books.
We will demonstrated how to add a function that is bound to a collection.

In the Edm Model, we modify the code as follows.

```csharp
private static IEdmModel GetEdmModel()
{
    var builder = new ODataConventionModelBuilder();
    builder.EntitySet<Book>("books");
    builder.EntityType<Book>().Collection
        .Function("mostRecent")
        .Returns<string>();
    var model = builder.GetEdmModel();
    return model;
}
```
In the Edm Model above, we are adding a `mostRecent` function bound to the books collection. The function returns the `ID` of the most recent book. The `ID` is of `string` type.

We will add the controller method below to the `BooksController`.
```csharp
public class BooksController : ODataController
{
    // Get ~/Books
    [EnableQuery]
    public IActionResult Get()
    {
        return Ok(DataSource.Instance.Books);
    }

    [HttpGet("odata/Books/MostRecent()")]
    public IActionResult MostRecent()
    {
        var maxBookId = DataSource.Instance.Books.Max(x => x.ID);
        return Ok(maxBookId);
    }
}
```

If we invoke `GET odata/Books/MostRecent()`
We get the response below:

```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Edm.String",
    "value": "8"
}
```

## Unbound function
