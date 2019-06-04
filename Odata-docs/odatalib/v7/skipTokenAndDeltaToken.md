---
title: "$skiptoken & $deltatoken"
description: ""
author: madansr7
ms.author: saumadan
ms.date: 02/19/2019
ms.topic: article
ms.service: multiple
---
# Skiptoken and Delta token 

## $skiptoken

Let's have an example:

```C#
~/Customers?$skiptoken=abc
```

We can do as follows to parse:

```C#
var uriParser = new ODataUriParser(...);
string token = parser.ParseSkipToken();
Assert.Equal("abc", token);
```

## $deltatoken

Let's have an example:

```C#
~/Customers?$deltaToken=def
```

We can do as follows to parse:

```C#
var uriParser = new ODataUriParser(...);
string token = parser.ParseDeltaToken();
Assert.Equal("def", token);
```
