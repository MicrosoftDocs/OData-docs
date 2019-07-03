---
title: "Define referential constraints-ODL V6"
description: "Define referential constraints using EdmLib APIs-ODL V6"

author: madansr7
ms.author: madansr7
ms.date: 7/1/2019
ms.topic: article
 
---
# Define referential constraints(ODL V6.x)
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v6.md)]

Referential constraints ensure that entities being referenced (principal entities) always exist. In OData, having one or more referential constraints defined for a partner navigation property on a dependent entity type also enables users to address the related dependent entities from principal entities using shortened key predicates (see [[OData-URL]](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part2-url-conventions/odata-v4.0-errata02-os-part2-url-conventions-complete.html#_Toc406398079)). A referential constraint in OData consists of one **principal property** (the ID property of the entity being referenced) and one **dependent property** (the ID property to reference another entity). This section shows how to define referential constraints on a partner navigation property.

### Sample
Create an entity type `Test.Customer` with a key property `id` of `Edm.String`.

``` csharp
var model = new EdmModel();

var customer = new EdmEntityType("Test", "Customer", null, false, true);
var customerId = customer.AddStructuralProperty("id", EdmPrimitiveTypeKind.String, false);
customer.AddKeys(customerId);
model.AddElement(customer);
```

Create an entity type `Test.Order` with a composite key consisting of two key properties `customerId` and `orderId` both of `Edm.String`.

``` csharp
var order = new EdmEntityType("Test", "Order", null, false, true);
var orderCustomerId = order.AddStructuralProperty("customerId", EdmPrimitiveTypeKind.String, true);
var orderOrderId = order.AddStructuralProperty("orderId", EdmPrimitiveTypeKind.String, true);
order.AddKeys(orderCustomerId, orderOrderId);
model.AddElement(order);
```

`Customer.id` is the principal property while `Order.customerId` is the dependent property. Create a navigation property `orders` on the principal entity type `customer`.

``` csharp
var customerOrders = customer.AddUnidirectionalNavigation(new EdmNavigationPropertyInfo
{
    ContainsTarget = true,
    Name = "orders",
    Target = order,
    TargetMultiplicity = EdmMultiplicity.Many
});
```

Then, create its corresponding partner navigation property on the dependent entity type `order` with referential constraint.

``` csharp
var orderCustomer = order.AddUnidirectionalNavigation(new EdmNavigationPropertyInfo
{
    ContainsTarget = false,
    Name = "customer",
    Target = customer,
    TargetMultiplicity = EdmMultiplicity.One,
    DependentProperties = new[] { orderCustomerId },
    PrincipalProperties = new[] { customerId }
});
```

Create an entity type `Test.Detail` with a composite key consisting of three key properties `customerId` of `Edm.String`, `orderId` of `Edm.String` and `id` of `Edm.Int32`.

``` csharp
var detail = new EdmEntityType("Test", "Detail");
var detailCustomerId = detail.AddStructuralProperty("customerId", EdmPrimitiveTypeKind.String);
var detailOrderId = detail.AddStructuralProperty("orderId", EdmPrimitiveTypeKind.String);
detail.AddKeys(detailCustomerId, detailOrderId, detail.AddStructuralProperty("id", EdmPrimitiveTypeKind.Int32, false));
model.AddElement(detail);
```

Create an entity type `Test.DetailedOrder` which is a derived type of `Test.Order`. We will use this type to illustrate type casting in between multiple navigation properties.

``` csharp
var detailedOrder = new EdmEntityType("Test", "DetailedOrder", order);
```

Come back to the type `Test.Detail`. There are two referential constraints here:

 - `DetailedOrder.orderId` is the principal property while `Detail.orderId` is the dependent property.
 - `DetailedOrder.customerId` is the principal property while `Detail.customerId` is the dependent property.

Create a navigation property `details`.

``` csharp
var detailedOrderDetails = detailedOrder.AddUnidirectionalNavigation(
    new EdmNavigationPropertyInfo
    {
        ContainsTarget = true,
        Name = "details"
        Target = detail,
        TargetMultiplicity = EdmMultiplicity.Many
    });
model.AddElement(detailedOrder);
```

Then, create its corresponding partner navigation property on the dependent entity type `detail` with referential constraint.

```c#
var detailDetailedOrder = detail.AddUnidirectionalNavigation(
    new EdmNavigationPropertyInfo
    {
        ContainsTarget = false,
        Name = "detailedOrder"
        Target = detailedOrder,
        TargetMultiplicity = EdmMultiplicity.One,
        DependentProperties = new[] { detailOrderId, detailCustomerId },
        PrincipalProperties = new[] { orderOrderId, orderCustomerId }
    });
```

Please note that you should **NOT** specify `Customer.id` as the principal property because the association (represented by the navigation property `details`) is from `DetailedOrder` to `Detail` rather than from `Customer` to `Detail`. And those properties must be specified **in the same order**.

Then you can query the `details` either by a full key predicate

```html
https://host/customers('customerId')/orders(customerId='customerId',orderId='orderId')/Test.DetailedOrder/details(customerId='customerId',orderId='orderId',id=1)
```

or a shortened key predicate.

```html
https://host/customers('customerId')/orders('orderId')/Test.DetailedOrder/details(1)
```

Key-as-segment convention is also supported.

```html
https://host/customers/customerId/orders/orderId/Test.DetailedOrder/details/1
```