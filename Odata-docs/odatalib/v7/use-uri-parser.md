---
title: "Use ODataUriParser in odatalib v7"
description: "This section describes how to parse OData uri using the ODataUriParser."
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Using Uri Parser
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

This post is intended to guide you through the URI parser for OData V4, which is released with ODataLib V6.0 and later.

You may have already read the following posts about OData URI parser in ODataLib V5.x:

- [Parsing $filter and $orderby using the ODataUriParser](/archive/blogs/alexj/parsing-filter-and-orderby-using-the-odatauriparser)
- [Parsing OData Paths, $select and $expand using the ODataUriParser](/archive/blogs/alexj/parsing-odata-paths-select-and-expand-using-the-odatauriparser)

Parts of those articles, e.g., introductions to `ODataPath` and `QueryNode` hierarchy, still apply to the V4 URI parser. In this post, we will deal with API changes and newly-added features.

### Overview
The main reference document for using URI parser is the [URL Conventions specification](https://docs.oasis-open.org/odata/odata/v4.0/odata-v4.0-part2-url-conventions.html). The `ODataUriParser` class is the main part of its implementation in ODataLib.

The responsibility of `ODataUriParser` is two-fold:

* Parse resource path
* Parse query options

We’ve also introduced the new `ODataQueryOptionParser` class in ODataLib V6.2+ to deal with the scenario where you do not have the full resource path and only want to parse the query options. The `ODataQueryOptionParser` shares the same API signatures for parsing query options. You can find more information below.

### Using ODataUriParser
The use of `ODataUriParser` is easy and straightforward. As already mentioned, we do not support static methods now, so we will begin by creating an `ODataUriParser` instance.

One `ODataUriParser` constructor is:

```C#
public ODataUriParser(IEdmModel model, Uri serviceRoot, Uri uri);
```

Parameters:

`model` is the data model the parser will refer to; `serviceRoot` is the base URI for the service, which is constant for a particular service. Note that `serviceRoot` must be an absolute URI; `uri` is the request URI to be parsed, including any query options. When it is an absolute URI, it must be based on `serviceRoot`, or it can be a relative URI. In the following example, we use the model from OData V4 demo service, and create an `ODataUriParser` instance based on it.

```C#
Uri serviceRoot = new Uri("https://services.odata.org/V4/OData/OData.svc");
IEdmModel model = CsdlReader.Parse(XmlReader.Create(serviceRoot + "/$metadata"));
Uri requestUri = new Uri("https://services.odata.org/V4/OData/OData.svc/Products");
ODataUriParser parser = new ODataUriParser(model, serviceRoot, requestUri);
```

### Parsing resource path
You can use the following API to parse resource path:

```C#
Uri requestUri = new Uri("https://services.odata.org/V4/OData/OData.svc/Products(1)");
ODataUriParser parser = new ODataUriParser(model, serviceRoot, requestUri);
ODataPath path = parser.ParsePath();
```

You don’t need to pass in resource path as a parameter to `ParsePath()`, because it has already been provided when constructing the `ODataUriParser` instance.

`ODataPath` holds an enumeration of path segments for the resource path. All path segments are represented by classes derived from `ODataPathSegment`.

In the example, the resource path in the request URI is `Products(1)`, so the resulting `ODataPath` will contain two segments: an `EntitySetSegment` for the entity set `Products`, and a `KeySegment` for the key with integer value `1`.

### Parsing query options
`ODataUriParser` supports parsing following query options: `$select`, `$expand`, `$filter`, `$orderby`, `$search`, `$top`, `$skip`, and `$count`.

For the first five, the parsing result is represented by an instance of class `XXXClause` which presents the query option as an Abstract Syntax Tree (with semantic information bound). Note that `$select` and `$expand` query options are handled together by the `SelectExpandClause` class. The latter three all have primitive type values, and the parsing results are represented by the corresponding nullable primitive types.

For all query option parsing results, a null value indicates that the corresponding query option is not present in the request URI.

Here is an example for parsing the request URI with different kinds of query options (please notice that the value of `skip` would be null, since the skip query option is not present in the request URI):

```C#
Uri requestUri = new Uri("Products?$select=ID&$expand=ProductDetail" +
                         "&$filter=Categories/any(d:d/ID%20gt%201)&$orderby=ID%20desc" +
                         "&$top=1&$count=true&$search=tom",
                         UriKind.Relative);
ODataUriParser parser = new ODataUriParser(model, serviceRoot, requestUri);
SelectExpandClause expand = parser.ParseSelectAndExpand(); // parse $select, $expand
FilterClause filter = parser.ParseFilter();                // parse $filter
OrderByClause orderby = parser.ParseOrderBy();             // parse $orderby
SearchClause search = parser.ParseSearch();                // parse $search
long? top = parser.ParseTop();                             // parse $top
long? skip = parser.ParseSkip();                           // parse $skip
bool? count = parser.ParseCount();                         // parse $count
```
 
The data structures for `SelectExpandClause`, `FilterClause`, `OrdeyByClause` have already been presented in two previous articles mentioned in the beginning of this post. Here I’d like to talk about the newly-added `SearchClause`.

`SearchClause` contains a tree representation of the `$search` query. The detailed rules of `$search` query option can be found [here](https://docs.oasis-open.org/odata/odata/v4.0/errata03/os/complete/part2-url-conventions/odata-v4.0-errata03-os-part2-url-conventions-complete.html#_Toc453752364). In general, the search query string can contain search terms combined with logic operators: AND, OR and NOT.

All search terms are represented by `SearchTermNode` which is derived from `SingleValueNode`. `SearchTermNode` has a `Text` property containing the original word or phrase.

The `SearchClause.Expression` property holds the tree structure for `$search`. If `$search` contains a single word, the `Expression` would be a single `SearchTermNode`. But when `$search` contains a combination of various terms and logic operators, `Expression` would also contain `BinaryOperatorNode` and `UnaryOperatorNode`.

For example, if the query option has the value `a AND b`, the result expression (syntax tree) would have the following structure:

```C#
SearchQueryOption
    Expression = BinaryOperatorNode
                 OperationKind = BinaryOperatorKind.And
                 Left          = SearchTermNode
                                 Text = a
                 Right         = SearchTermNode
                                 Text = b
```

### Using ODataQueryOptionParser
There may be cases where you already know the query context information, and does not have the full request URI. The `ODataUriParser` will not be available at this time, as it requires a full URI. The user would have to fake one.

In ODataLib 6.2 we shipped a new URI parser that targets query options only. It requires the model and type information to be provided through its constructor, and then it could be used for query options parsing just as `ODataUriParser`.

One of its constructors looks like this:

```C#
public ODataQueryOptionParser(
    IEdmModel model,
    IEdmType targetEdmType,
    IEdmNavigationSource targetNavigationSource,
    IDictionary<string, string> queryOptions);
```

Parameters (here the target object refers to what resource path is addressed, see spec):

`model` is the model the parser will refer to;
`targetEdmType` is the type of the target object, to which the query options apply;
`targetNavigationSource` is the entity set or singleton where the target comes from, and it is usually the navigation source of the target object;
`queryOptions` is a dictionary containing the key-value pairs for query options.
 
Here is an example demonstrating its use. It is almost identical to that of `ODataUriParser`:

```C#
Dictionary<string, string> options = new Dictionary<string, string>
{
    {"$select"  , "ID"                          },
    {"$expand"  , "ProductDetail"               },
    {"$filter"  , "Categories/any(d:d/ID gt 1)" },
    {"$orderby" , "ID desc"                     },
    {"$top"     , "1"                           },
    {"$count"   , "true"                        },
    {"$search"  , "tom"                         },
};
IEdmType type = model.FindDeclaredType("ODataDemo.Product");
IEdmNavigationSource source = model.FindDeclaredEntitySet("Products");
ODataQueryOptionParser parser = new ODataQueryOptionParser(model, type, source, options);
SelectExpandClause selectExpand = parser.ParseSelectAndExpand(); //parse $select, $expand
FilterClause filter = parser.ParseFilter();                      // parse $filter
OrderByClause orderby = parser.ParseOrderBy();                   // parse $orderby
SearchClause search = parser.ParseSearch();                      // parse $search
long? top = parser.ParseTop();                                   // parse $top
long? skip = parser.ParseSkip();                                 // parse $skip (null)
bool? count = parser.ParseCount();                               // parse $count
```
