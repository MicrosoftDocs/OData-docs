---
title : "OData IN Operator filter function"
description: Learn how to use the IN operator that enables a shorthand way of writing multiple EQ expressions joined by OR.
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019

---

# IN Operator
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Starting in WebAPI OData V7.0.0 [[ASP.NET](https://www.nuget.org/packages/Microsoft.AspNet.OData/7.0.0) | [ASP.NET Core](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/)], the IN operator is a [supported feature](https://docs.oasis-open.org/odata/new-in-odata/v4.01/cn01/new-in-odata-v4.01-cn01.html#_Toc485385090) that enables a shorthand way of writing multiple EQ expressions joined by OR. For example,

`GET /service/Products?$filter=Name eq 'Milk' or Name eq 'Cheese' or Name eq 'Donut'`

can become

`GET /service/Products?$filter=Name in ('Milk', 'Cheese', 'Donut')`

Of the binary expression invoking IN, the left operand must be a single value and the right operand must be a comma-separated list of primitive values or a single expression that resolves to a collection; the expression returns true if the left operand is a member of the right operand.

## Usage

IN operator is supported only for $filter at the moment and hardcoded collections are supported with parentheses. See examples below.

```html
~/Products?$filter=Name in ('Milk', 'Cheese')
~/Products?$filter=Name in RelevantProductNames
~/Products?$filter=ShipToAddress/CountryCode in MyShippers/Regions
~/Products?$filter=Name in Fully.Qualified.Namespace.MostPopularItemNames
```
