---
title: "Query options overview"
description: "Query options overview"
author: madansr7
ms.author: madansr7
ms.date: 7/1/2019
ms.topic: article
 
---
# Query options overview

A query option is a set of query string parameters applied to a resource that can help control the amount of data being returned for the resource in the URL. A query option is basically requesting that a service perform a set of transformations such as filtering, sorting, etc. to its data before returning the results. A query option can be applied to every verb except `DELETE` operations.

The query options part of an OData URL specifies three types of information: `System query options`, `Custom query options`, and `Parameter aliases`.

## System query options

System query options are query string parameters that control the amount and order of the data returned for the resource identified by the URL. The names of all system query options are optionally prefixed with a dollar ($) character.
OData Protocol V4.01 based services support case-insensitive system query option names specified with or without the $ prefix. Clients that want to work with 4.0 services must use lower case names and specify the $ prefix.

System query options are $filter, $select, $orderby, $count, $top, $skip and $expand.

### Filter

The $filter system query option allows clients to filter a collection of resources that are addressed by a request URL. The expression specified with $filter is evaluated for each resource in the collection, and only items where the expression evaluates to true are included in the response. Resources for which the expression evaluates to false or to null, or which reference properties that are unavailable due to permissions, are omitted from the response. You can find details on filter specification in the [OData spec filter options](http://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#sec_SystemQueryOptionfilter) section.

**Examples:**

- All products with a Name equal to 'Milk': `http://host/service/Products?$filter=Name eq 'Milk'`

- All products with a Name not equal to 'Milk' : `http://host/service/Products?$filter=Name ne 'Milk'`

- All products with a Name greater than 'Milk': `http://host/service/Products?$filter=Name gt 'Milk'`

- All products with a Name greater than or equal to 'Milk': `http://host/service/Products?$filter=Name ge 'Milk'`

- All products with a Name less than 'Milk': `http://host/service/Products?$filter=Name lt 'Milk'`

- All products with a Name less than or equal to 'Milk': `http://host/service/Products?$filter=Name le 'Milk'`

- All products with the Name 'Milk' that also have a Price less than 2.55: `http://host/service/Products?$filter=Name eq 'Milk' and Price lt 2.55`

- All products that either have the Name 'Milk' or have a Price less than 2.55: `http://host/service/Products?$filter=Name eq 'Milk' or Price lt 2.55`

- All products that do not have a Name that ends with 'ilk': `http://host/service/Products?$filter=not endswith(Name,'ilk')`

- All products whose style value includes Yellow: `http://host/service/Products?$filter=style has Sales.Pattern'Yellow'`

- All products whose name value is ‘Milk’ or ‘Cheese’: `http://host/service/Products?$filter=Name in ('Milk', 'Cheese')`

### Expand

The $expand system query option specifies the related resources or media streams to be included in line with retrieved resources. Each expandItem is evaluated relative to the entity containing the navigation or stream property being expanded.

**Examples:**

- Expand a navigation property of an entity type: `http://host/service/Products?$expand=Category`

- Expand a navigation property of a complex type: `http://host/service/Customers?$expand=Addresses/Country`

Query options can be applied to an expanded navigation property by appending a semicolon-separated list of query options, enclosed in parentheses, to the navigation property name. Allowed system query options are $filter, $select, $orderby, $skip, $top, $count, $search, and $expand.

- All categories and for each category all related products with a discontinued date equal to null

    `http://host/service/Categories?$expand=Products($filter=DiscontinuedDate eq null)`

### Select

The $select system query option allows clients to request a specific set of properties for each entity or complex type.

The $select query option is often used in conjunction with the $expand system query option, to define the extent of the resource graph to return ($expand) and then specify a subset of properties for each resource in the graph ($select).

**Examples:**

- Rating and release date of all products : `http://host/service/Products?$select=Rating,ReleaseDate`

It is also possible to request all declared and dynamic structural properties using a star (*).

- All structural properties of all products: `http://host/service/Products?$select=*`

### OrderBy

The $orderby system query option allows clients to request resources in a particular order.

- Return all Products ordered by release date in ascending order, then by rating in descending order

    `GET http://host/service/Products?$orderby=ReleaseDate asc, Rating desc`

Related entities may be ordered by specifying $orderby within the $expand clause.

- Return all Categories, and their Products ordered according to release date and in descending order of rating

    `GET http://host/service/Categories?   $expand=Products($orderby=ReleaseDate asc, Rating desc)`

$count may be used within a $orderby expression to order the returned items according to the exact count of related entities or items within a collection-valued property.

- Return all Categories ordered by the number of Products within each category

    `GET http://host/service/Categories?$orderby=Products/$count`

### Top and Skip

The $top system query option requests the number of items in the queried collection to be included in the result. The $skip query option requests the number of items in the queried collection that are to be skipped and not included in the result. A client can request a particular page of items by combining $top and $skip.

**Examples:**

- Get top 10 items

    `http://host/service/Products?$top=10`

- Skip first 10 items

    `http://host/service/Products?$skip=10`

### Count

The $count system query option allows clients to request a count of the matching resources included with the resources in the response. The $count query option has a Boolean value of true or false.

**Examples:**

- Return, along with the results, the total number of products in the collection

    `http://host/service/Products?$count=true`

- The count of related entities can be requested by specifying the $count query option within the $expand clause.

    `http://host/service/Categories?$expand=Products($count=true)`

### Search

The $search system query option allows clients to request items within a collection matching a free-text search expression.

The $search query option can be applied to a URL representing a collection of entity, complex, or primitive typed instances, to return all matching items within the collection. Applying the $search query option to the $all resource requests all matching entities in the service.

If both $search and $filter are applied to the same request, the results include only those items that match both criteria.

**Example:**

- All products that are blue or green. It is up to the service to decide what makes a product blue or green.

    `http://host/service/Products?$search=blue OR green`

## Custom query options

Custom query options provide an extensible mechanism for service-specific information to be placed in a URL query string.
Custom query options MUST NOT begin with a $ or @ character.

**Examples:**

- Service-specific custom query option debug-mode

`http://host/service/Products?debug-mode=true`

## Parameter aliases

Parameter aliases can be used in place of literal values in entity keys, function parameters, or within a $filter or $orderby expression.
Parameter aliases MUST start with an @ character.

**Examples:**

- Filter movies that have the word Super in its title: `http://host/service/Movies?$filter=contains(@word,Title)&@word='Super'`

- Filter movies that have the title 'Wizard of Oz': `http://host/service/Movies?$filter=Title eq @title&@title='Wizard of Oz'`

- JSON array of strings as parameter alias value – note that [, ], and " need to be percent-encoded in real URLs, the clear-text representation used here is just for readability : `http://host/service/Products/Model.WithIngredients(Ingredients=@i)?@i=["Carrots","Ginger","Oranges"]`

## Conventions

A request to a resource using Http verbs `GET`, `PATCH` or `PUT` follow these conventions:

- Resource paths identifying a single entity, a complex type instance, a collection of entities, or a collection of complex type instances allow $compute, $expand and $select.
- Resource paths identifying a collection allow $filter, $search, $count, $orderby, $skip, and $top.
- Resource paths ending in /$count allow $filter and $search.
- Resource paths not ending in /$count or /$batch allow $format.
- Query options for a `POST` operations may differ due to the type of object being returned in the response. If a POST returns a single entity vs collection of entities, it will impact the query options that are applicable to the request.
