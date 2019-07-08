---
title: "$skiptoken & $deltatoken-ODL v7"
description: "$skiptoken & $deltatoken-ODL v7"
author: madansr7
ms.author: madansr7
ms.date: 7/1/2019
ms.topic: article
 
---
# Skiptoken and Delta token 
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

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
