---
title: "Use ODataUriParser in odatalib v6"
description: Describes how to use the ODataUriParser in odatalib v6.
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# OData Uri Parser(ODL V6.x)
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v6.md)]

This post is intended to guide you through the UriParser for OData V4, which is released within ODataLib V6.0 and later.

You may have already read the following posts about OData UriParser in ODataLib V5.x:

- [Parsing $filter and $orderby using the ODataUriParser](/archive/blogs/alexj/parsing-filter-and-orderby-using-the-odatauriparser)
- [Parsing OData Paths, $select and $expand using the ODataUriParser](/archive/blogs/alexj/parsing-odata-paths-select-and-expand-using-the-odatauriparser)
Some parts of the articles still apply to V4 UriParser, such as introduction for ODataPath and QueryNode hierarchy. In this post, we will deal with API changes and features newly introduced.

## UriParser Overview
The main reference document for UriParser is the URL Conventions specification. The ODataUriParser class is its main implementation in ODataLib.

The ODataUriParser class has two main functionalities:

* Parse resource path
* Parse query options


We’ve also introduced the new ODataQueryOptionParser class in ODataLib 6.2+, in case you do not have the full resource path and only want to parse the query options only. The ODataQueryOptionParser shares the same API signature for parsing query options, you can find more information below.

## Using ODataUriParser
The use of ODataUriParser class is easy and straightforward, as we mentioned, we do not support static methods now, we will begin from creating an ODataUriParser instance.

ODataUriParser has only one constructor:

```c#
public ODataUriParser(IEdmModel model, Uri serviceRoot, Uri fullUri);
```

Parameters:

model is the Edm model the UriParser will refer to;
serviceRoot is the base Uri for the service, which could be a constant for certain service. Note that serviceRoot must be an absolute Uri;
fullUri is the full request Uri including query options. When it is an absolute Uri, it must be based on the serviceRoot, or it can be a relative Uri.
In the following demo we will use the model from OData V4 demo service , and create an ODataUriParser instance.

```c#
Uri serviceRoot = new Uri("https://services.odata.org/V4/OData/OData.svc");
IEdmModel model = EdmxReader.Parse(XmlReader.Create(serviceRoot + "/$metadata"));
Uri fullUri = new Uri("https://services.odata.org/V4/OData/OData.svc/Products");
ODataUriParser parser = new ODataUriParser(model, serviceRoot, fullUri);
```

## Parsing Resource Path
You can use the following API to parse resource path:

```c#
Uri fullUri = new Uri("https://services.odata.org/V4/OData/OData.svc/Products(1)");
ODataUriParser parser = new ODataUriParser(model, serviceRoot, fullUri);
ODataPath path = parser.ParsePath();
```

You don’t need to pass in resource path as parameter here, because the constructor has taken the full Uri.

The ODataPath holds the enumeration of path segments for resource path. All path segments are represented by classes derived from ODataPathSegment.

In our demo, the resource Path in the full Uri is Products(1), then the result ODataPath would contain two segments: one EntitySetSegment for EntitySet named Products, and the other KeySegment for key with integer value “1” .

## Parsing Query Options
ODataUriParser supports parsing following query options: $select, $expand, $filter, $orderby, $search, $top, $skip, and $count.

For the first five, the parsing result is an instance of class XXXClause, which represents the query option as an Abstract Syntax Tree (with semantic information bound). Note that $select and $expand query options are merged together in one SelectExpandClause class. The latter three all have primitive type value, and the parsing result is the corresponding primitive type wrapped by Nullable class.

For all query option parsing results, the Null value indicates the corresponding query option is not specified in the request URL.

Here is a demo for parsing the Uri with all kinds of query options (please notice that value of skip would be null as it is not specified in the request Uri) :

```c#
Uri fullUri = new Uri("Products?$select=ID&$expand=ProductDetail" +
    "&$filter=Categories/any(d:d/ID%20gt%201)&$orderby=ID%20desc" +
    "&$top=1&$count=true&$search=tom", UriKind.Relative);
ODataUriParser parser = new ODataUriParser(model, serviceRoot, fullUri);
SelectExpandClause expand = 
    parser.ParseSelectAndExpand();              //parse $select, $expand
FilterClause filter = parser.ParseFilter();     // parse $filter
OrderByClause orderby = parser.ParseOrderBy();  // parse $orderby
SearchClause search = parser.ParseSearch();     // parse $search
long? top = parser.ParseTop();                  // parse $top
long? skip = parser.ParseSkip();                // parse $skip
bool? count = parser.ParseCount();              // parse $count
```
 
The data structure for SelectExpandClause, FilterClause, OrdeyByClause have already been presented in the two previous articles mentioned at the top of this post. Here I’d like to talk about the newly introduced SearchClause.

SearchClause contains tree representation of the $search query. The detailed rule of $search query option can be found here. In general, the search query string can contain search terms combined with logic keywords: AND, OR and NOT.

All search terms are represented by SearchTermNode, which is derived from SingleValueNode. SearchTermNode has one property named Text, which contains the original word or phrases.

SearchClause’s Expression property holds the tree structure for $search. If the $search contains single word, the Expression would be set to that SearchTermNode. But when $search is a combination of various term and logic keywords, the Expression would also contains nested BinaryOperatorNode and UnaryOperatorNode.

For example, if the query option $search has the value “a AND b”, the result expression (syntax tree) would have the following structure:

```c#
SearchQueryOption
    Expression = BinaryOperatorNode
                   OperationKind = BinaryOperatorKind.And
                   Left           = SearchTermNode
                                    Text = a
                   Right          = SearchTermNode
                                    Text = b
```

## Using ODataQueryOption Parser
There may be some cases that you already know the query context information but does not have the full request Uri. The ODataUriParser does not seems to be available as it will always require the full Uri, then the user would have to fake one.

In ODataLib 6.2 we shipped a new Uri parser that targets at query options only, it requires the model and type information be provided through its constructor, then it could be used for query options parsing as same as ODataUriParser.

The constructor looks like this:

```c#
public ODataQueryOptionParser(IEdmModel model, IEdmType targetEdmType, IEdmNavigationSource targetNavigationSource, IDictionary<string, string> queryOptions);
```

Parameters (here the target object indicates what resource path was addressed, see spec):

model is the model the UriParser will refer to;
targetEdmType is the type query options apply to, it is the type of target object;
targetNavigationSource is the EntitySet or Singleton where the target comes from, it is usually the NavigationSource of the target object;
queryOptions is the dictionary containing the key-value pairs for query options.
 

Here is the demo for its usage, it is almost the same as the ODataUriParser:

```c#
Dictionary<string, string> options = new Dictionary<string, string>()
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
ODataQueryOptionParser parser 
    = new ODataQueryOptionParser(model, type, source, options);
SelectExpandClause selectExpand = 
    parser.ParseSelectAndExpand();              //parse $select, $expand
FilterClause filter = parser.ParseFilter();     // parse $filter
OrderByClause orderby = parser.ParseOrderBy();  // parse $orderby
SearchClause search = parser.ParseSearch();     // parse $search
long? top = parser.ParseTop();                  // parse $top
long? skip = parser.ParseSkip();                // parse $skip (null)
bool? count = parser.ParseCount();              // parse $count

```
