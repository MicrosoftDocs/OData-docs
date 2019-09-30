---
title: "Nested query options in select query"
description: "Nested query options in select query"
author: saxu
ms.author: saxu
ms.date: 9/17/2019
ms.topic: article
 
---

# Nested query options in $select query

**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

OData v4 specification says:

*Query options can be applied to a selected property by appending a semicolon-separated list of query options, enclosed in parentheses, to the property. Allowed system query options are $select and $expand, plus $filter, $search, $count, $orderby, $skip, and $top for collection-valued properties.*

Starting ODataLib 7.6.1.beta, OData query options parser supports parsing the following nested query options in $select:

- $filter
- $orderby
- $top
- $skip
- $count
- $search
- $select
- $compute

[!Note] OData spec allows $expand in $select. However, the $expand in $select can be achieved using the $expand and $select. For example:  

```html
`$select=1($expand=2)` <==> `$select=1&$expand=1/2`
```

So, in this implementation, $expand in $select is not enabled.

## Enabling $select support

The main changes are in class `PathSelectItem`. Now, the developer can query the nested query options from this class for a certain $select clause:

```C#
class PathSelectItem
{
    (get)FilterOption
    (get)OrderByClause
    (get)TopOption
    (get)SkipOption
    (get)CountOption
    (get)SearchOption
    (get)SelectExpandClause
    (get)ComputeClause
}
```

## Usage

Let's have an example:

```C#
Uri ServiceRoot = new Uri("http://host")
IEdmModel model = ...;
Uri requestUri = new Uri(@"http://host/People?$select={YourSelectClause}");
ODataUriParser parser = new ODataUriParser(Model, ServiceRoot, uri);
SelectExpandClause parsedSelectExpand = parser.ParseSelectAndExpand();
```

### $filter

Let's have the following nested query option:

```html
`$select=Addresses($filter=title eq 'abc')`
```

The parsed select and expand clause has the following result:

```html
parsedSelectExpand
    AllSelected = false
    SelectedItems
        PathSelectItem
            SelectedPath[(Property: Addresses)]
            FilterClause (title eq 'abc')
```

### $orderby

Let's have the following nested query option:

```html
`$select=Addresses($orderby=title)`
```

The parsed select and expand clause has the following result:

```html
parsedSelectExpand
    AllSelected = false
    SelectedItems
        PathSelectItem
            SelectedPath[(Property: Addresses)]
            OrderByClause (title)
```

### $search

Let's have the following nested query option:

`$select=Addresses($search='abc')`

The parsed select and expand clause has the following result:

```html
parsedSelectExpand
    AllSelected = false
    SelectedItems
        PathSelectItem
            SelectedPath[(Property: Addresses)]
            SearchClause ('abc')
```

### $top & $skip & $count

Let's have the following nested query option:

`$select=Addresses($top=2;$skip=3;$count=true)`

The parsed select and expand clause has the following result:

```html
parsedSelectExpand
    AllSelected = false
    SelectedItems
        PathSelectItem
            SelectedPath[(Property: Addresses)]
            TopOption (2)
            SkipOption (3)
            CountOption (true)
```

### $select

Let's have the following nested query option:

`$select=Addresses($select=City($select=ZipCode))`

The parsed select and expand clause has the following result:

```html
parsedSelectExpand
    AllSelected = false
    SelectedItems
        PathSelectItem
            SelectedPath[(Property: Addresses)]
            SelectExpandClause
                AllSelected = false
                SelectedItems
                    PathSelectItem
                    SelectedPath[(Property: City)]
                        SelectExpandClause
                            AllSelected = false
                            SelectedItems
                                PathSelectItem
                                SelectedPath[(Property: ZipCode)]
```

### $compute

Let's have the following $compute nested query option:

`$select=Addresses($compute=tolower(Street) as lowStreet;$select=lowStreet)`

The parsed select and expand clause has the following result:

```html
parsedSelectExpand
    AllSelected = false
    SelectedItems
        PathSelectItem
            SelectedPath[(Property: Addresses)]
            ComputeClause
            SelectExpandClause
                AllSelected = False
                SelectedItems
                    PathSelectItem
                    SelectedPath[(dynamic property: lowStreet)]
```
