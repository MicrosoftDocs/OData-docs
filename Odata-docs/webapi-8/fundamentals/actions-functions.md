---
title: "Actions and Functions"
description: "This documentation describes how to add actions and functions."

author: KenitoInc
ms.author: kemunga

ms.date: 9/15/2022
---
# Actions and functions
**Applies To**:[!INCLUDE[appliesto-webapi](../../includes/appliesto-webapi-v8.md)]

In OData, actions and functions are a way to add server-side behavior that is not easily defined as CRUD operations (Create-Read-Update-Delete) on entities. This documentation shows how to add actions and functions to an OData v4 endpoint, using ASP.NET Core OData v8.x.

## What are actions and functions?
- `Functions` are operations exposed by an OData service that MUST return data and MUST have no observable side effects. They may support further composition. Functions are invoked by using HTTP GET requests.
- `Actions` are operations exposed by an OData service that MAY have side effects when invoked. Actions have a side effect on the server, so they are invoked by using HTTP POST requests. They cannot be further composed in order to avoid non-deterministic behavior.

Side effects means that actions results in a change in the server; either a resource is updated, deleted or a new resource is created.

## Bound and unbound operations.
From OData V4 spec, functions and actions can be either bound to a type or unbound.
Bound operations are bound to an entity type, primitive type, complex type, or a collection.
Unbound operations are invoked as static operations on the service.

## URI patterns for calling OData actions and functions
In the examples below `odata` is the route prefix.

### Bound function
The function is after an entity, entityset, singleton. `Books` and `Graphs` are entity sets.

```
GET ~/odata/Books/mostRecent()
GET ~/odata/Graphs/Default.GetShapeCount()
GET ~/odata/Graphs/Default.GetShapeCount(shapeType=FunctionActionBlog.ShapeType’Circle’)
```

`GetShapeCount` function has an overload that accepts a `ShapeType` parameter.

We can bind to a single entity.

`GET ~/odata/Books(1)/isChildBook()`

We can also bind to a singleton

`GET ~/odata/Me/isMyCalendarBlocked()`

### Unbound function
Function is after the route prefix.

```
GET ~/odata/ReturnAllForKidsBooks
GET ~/odata/GetSalesTaxRate(10)
```
`ReturnAllForKidsBooks` doesn't have a parameter, while `GetSalesTaxRate` accepts one integer parameter.

If `GetSalesTaxRate` had more than one parameter, the call would be

`GET ~/odata/GetSalesTaxRate(10, "US")`

Parameter-less functions can work with or without parenthesis `()` after function names.
This is how we configure parameter-less functions to work without parenthesis.

```csharp
builder.Services.AddControllers().AddOData(opt =>
{
    opt.AddRouteComponents("odata", EdmModel.GetEdmModel()).Count().OrderBy().Filter().Select().Expand();
    opt.RouteOptions.EnableNonParenthesisForEmptyParameterFunction = true;
});
```

### Bound Action
Actions are invoked via `POST` requests therefore we can pass a payload in the body.

```json
POST ~/odata/Books('1')/Rate
{
    "rating": 7
}
```

### Unbound action
```json
POST ~/odata/incrementBookYear
{
    "increment": 7,
    "id": "1"
}
```

## Practical examples
We will add detailed examples on how to configure bound and unbound functions and actions.
We will start by setting up the ASP.NET Core OData v8.x project.

### Data Model classes
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
    // -------------
    // Existing code
    // -------------

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
    // -------------
    // Existing code
    // -------------

    [HttpGet("odata/Books/mostRecent()")]
    public IActionResult MostRecent()
    {
        var maxBookId = DataSource.Instance.Books.Max(x => x.ID);
        return Ok(maxBookId);
    }
}
```

If we invoke `GET odata/Books/mostRecent()`
We get the response below:

```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#Edm.String",
    "value": "8"
}
```

## Unbound function
Unbound functions don’t bind to any type and they are invoked as static operations. All unbound function overloads MUST have same return type.

In the Edm Model, we modify the code as follows.

```csharp
private static IEdmModel GetEdmModel()
{
    var builder = new ODataConventionModelBuilder();
    // -------------
    // Existing code
    // -------------

    builder.Function("returnAllForKidsBooks").ReturnsFromEntitySet<Book>("books");
    var model = builder.GetEdmModel();
    return model;
}
```

In the Edm Model above, we are adding a `returnAllForKidsBooks` function. The function returns a collection of books.
For parameterless functions, we can ignore the parenthesis.

We will update the `BooksController` as follows.

```csharp
public class BooksController : ODataController
{
    // -------------
    // Existing code
    // -------------

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
Similar to bound functions, bound actions can be bound to entity type or collection of entity type.
However, the overload of bound actions are different. Bound actions can be overloaded, but overload must happen by different binding parameter. For one binding parameter, there can be only one bound action.

In the Edm Model, we modify the code as follows.

```csharp
private static IEdmModel GetEdmModel()
{
    var builder = new ODataConventionModelBuilder();
    // -------------
    // Existing code
    // -------------

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
    // -------------
    // Existing code
    // -------------

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

If we invoke

```json
POST odata/Books('1')/Rate
{"rating": 7}
```
We get the response below:

```json
{
    "id": null,
    "rating": 7,
    "bookID": "1"
}
```

## Unbound Action
Unbound actions don’t bind to any type and they are invoked as static operations same as unbound functions. However, unbound actions do not allow overloading.

In the Edm Model, we modify the code as follows.

```csharp
private static IEdmModel GetEdmModel()
{
    var builder = new ODataConventionModelBuilder();
    // -------------
    // Existing code
    // -------------

    var action = builder.Action("incrementBookYear").ReturnsFromEntitySet<Book>("books");
        action.Parameter<int>("increment");
        action.Parameter<string>("id");
    var model = builder.GetEdmModel();
    return model;
}
```

In the Edm Model above, we are adding an `incrementBookYear` action. The action takes an `increment` parameter of type `int` and
an `id` parameter of type `string`.

We will update the `BooksController` as follows.

```csharp
public class BooksController : ODataController
{
    // -------------
    // Existing code
    // -------------

    [HttpPost("odata/incrementBookYear")]
    public IActionResult IncrementBookYear(ODataActionParameters parameters)
    {
        if (!ModelState.IsValid)
        {
            return BadRequest();
        }

        int increment = (int)parameters["increment"];
        string bookId = (string)parameters["id"];

        var book = DataSource.Instance.Books.Where(m => m.ID == bookId).FirstOrDefault();

        if (book != null)
        {
            book.Year = book.Year + increment;
        }

        return Ok(book);
    }
}
```

If we invoke
```json
POST odata/incrementBookYear
{
    "increment": 7,
    "id": "1"
}
```

We get the response below:

```json
{
    "@odata.context": "http://localhost:5000/odata/$metadata#books/$entity",
    "id": "1",
    "isbn": "AA0011",
    "title": "Book 1",
    "year": 2002,
    "forKids": false
}
```

## Conclusion
In our examples above, we have provided very basic use cases for actions and functions. In real-world usage, actions and functions may contain complex logic to modify and get data across multiple entities.