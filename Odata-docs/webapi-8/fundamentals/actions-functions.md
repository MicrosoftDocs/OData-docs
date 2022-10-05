---
title: "Actions and functions"
description: "This documentation describes how to add actions and functions."

author: KenitoInc
ms.author: kemunga

ms.date: 9/15/2022
---
# Actions and functions
**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

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

We will update the `BooksController` as follows.

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
Unbound functions don’t bound to any type and they are called as static operations. All unbound function overloads MUST have same return type.

In the Edm Model, we modify the code as follows.

```csharp
private static IEdmModel GetEdmModel()
{
    var builder = new ODataConventionModelBuilder();
    builder.EntitySet<Book>("books");
    builder.EntityType<Book>().Collection
        .Function("mostRecent")
        .Returns<string>();
    builder.Function("returnAllForKidsBooks").ReturnsFromEntitySet<Book>("books");
    var model = builder.GetEdmModel();
    return model;
}
```

In the Edm Model above, we are adding a `returnAllForKidsBooks` function. The function returns a collection of books.

We will update the `BooksController` as follows.

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

    [HttpGet("odata/ReturnAllForKidsBooks")]
    public IActionResult ReturnAllForKidsBooks()
    {
        var forKidsBooks = DataSource.Instance.Books.Where(m => m.ForKids == true);
        return Ok(forKidsBooks);
    }
}
```

If we invoke `GET odata/ReturnAllForKidsBooks`
We get the response below:

```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#books",
    "value": [
        {
            "id": "2",
            "isbn": "BB0011",
            "title": "Book 2",
            "year": 2001,
            "forKids": true
        },
        {
            "id": "4",
            "isbn": "DD0011",
            "title": "Book 4",
            "year": 2003,
            "forKids": true
        },
        {
            "id": "5",
            "isbn": "EE0011",
            "title": "Book 5",
            "year": 2004,
            "forKids": true
        },
        {
            "id": "6",
            "isbn": "FF0011",
            "title": "Book 6",
            "year": 2005,
            "forKids": true
        },
        {
            "id": "7",
            "isbn": "GG0011",
            "title": "Book 7",
            "year": 2006,
            "forKids": true
        }
    ]
}
```

## Bound Action
Similar to bound fucntions, bound actions can be bound to entity type or collection of entity type.
However, the overload of bound actions are different. Bound actions can be overloaded, but overload must happen by different binding parameter. For one binding parameter, there can be only one bound action.

In the Edm Model, we modify the code as follows.

```csharp
private static IEdmModel GetEdmModel()
{
    var builder = new ODataConventionModelBuilder();
    builder.EntitySet<Book>("books");
    builder.EntityType<Book>().Collection
        .Function("mostRecent")
        .Returns<string>();
    builder.Function("returnAllForKidsBooks").ReturnsFromEntitySet<Book>("books");
    builder.EntityType<Book>()
        .Action("rate")
        .Parameter<int>("rating");
    var model = builder.GetEdmModel();
    return model;
}
```

In the Edm Model above, we are adding a `rate` action which is bound to a `Book` entity type. The action takes a `rating` parameter of type `int`. 

We will update the `BooksController` as follows.

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

    [HttpGet("odata/ReturnAllForKidsBooks")]
    public IActionResult ReturnAllForKidsBooks()
    {
        var forKidsBooks = DataSource.Instance.Books.Where(m => m.ForKids == true);
        return Ok(forKidsBooks);
    }

    [HttpPost("odata/Books({key})/Rate")]
    public IActionResult Rate([FromODataUri] string key, ODataActionParameters parameters)
    {
        if (!ModelState.IsValid)
        {
            return BadRequest();
        }

        int rating = (int)parameters["rating"];

        if (rating < 0)
        {
            return BadRequest();
        }

        return Ok(new BookRating() { BookID = key, Rating = rating });
    }
}
```

If we invoke `POST odata/Books('1')/Rate {"rating": 7}`
We get the response below:

```json
{
    "id": null,
    "rating": 7,
    "bookID": "1"
}
```