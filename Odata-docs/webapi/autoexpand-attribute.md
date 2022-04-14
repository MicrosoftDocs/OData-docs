---
title: "AutoExpand attribute"
description: ""

ms.date: 7/1/2019
author: madansr7
---
# AutoExpand attribute
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

In OData WebApi 5.7, we can put `AutoExpand` attribute on navigation property to make it automatically expand without `expand` query option, or can put this attribute on class to make all Navigation Property on this class automatically expand.

### Model

```C#

public class Product
{
    public int Id { get; set; }
    [AutoExpand]
    public Category Category { get; set; }
}

public class Category
{
    public int Id { get; set; }
    [AutoExpand]
    public Customer Customer{ get; set; }
}
```

### Result
If you call return Product in response, Category will automatically expand and Customer will expand too. It works the same if you put `[AutoExpand]`on Class if you have more navigation properties to expand.
