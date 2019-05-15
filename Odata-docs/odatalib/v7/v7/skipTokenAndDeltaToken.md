---
title: "$skiptoken & $deltatoken"
description: ""

---

From ODataLib 6.12.0, it supports to parse $skiptoken & $deltatoken in query options.

# $skiptoken

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

# $deltatoken

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
