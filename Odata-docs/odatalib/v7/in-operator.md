---
title: "IN Operator filter function"
description: "IN Operator"
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---

# IN operator
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

The IN operator is a [new feature](https://docs.oasis-open.org/odata/new-in-odata/v4.01/cn01/new-in-odata-v4.01-cn01.html#_Toc485385090 "new feature") in OData 4.01 that enables a shorthand way of writing multiple EQ expressions joined by OR. For example,

`GET /service/Products?$filter=Name eq 'Milk' or Name eq 'Cheese' or Name eq 'Donut'`

can become

`GET /service/Products?$filter=Name in ('Milk', 'Cheese', 'Donut')`

Of the binary expression invoking IN, the left operand must be a single value and the right operand must be a comma-separated list of primitive values or a single expression that resolves to a collection; the expression returns true if the left operand is a member of the right operand.

## Usage

The `IN` operator is used in expressions that resolve to a Boolean. Common use would be with `$filter` and it can also be used for `$orderby`. See [test cases](https://github.com/OData/odata.net/blob/ed68ebd17a78a9d63ef0ff6d4d7e680d778d5728/test/FunctionalTests/Microsoft.OData.Core.Tests/ScenarioTests/UriParser/FilterAndOrderByFunctionalTests.cs#L1668) for examples of supported scenarios.

## Remarks

Having successfully parsed the `$filter` expression, the `FilterClause` will have an `Expression` member which can be casted to an `InNode` object. The members of InNode `Left` and `Right` represent `SingleValueNode` and `CollectionNode` respectively. From there, it is up to the user to do whatever they would like with the parsed information from the `InNode`. As shown in the code samples, it may be in the user's best interest to downcast the `SingleValueNode` and `CollectionNode` to subclasses to access additional properties.