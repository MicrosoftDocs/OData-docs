---
title: "Support multi-NavigationPropertyBindings for a single navigation property"
description: ""
author: madansr7
ms.author: saumadan
ms.date: 02/19/2019
ms.topic: article
ms.service: multiple
---
# Multibinding support

According to the spec, a navigation property can be used in multiple bindings with different path. It makes a lot of sense for navigation property under complex and containment. From ODataLib V7.0, we are able to support it.
The valid format of a binding path is:
```C#
[ ( qualifiedEntityTypeName / qualifiedComplexTypeName ) "/" ] 
                    *( ( complexProperty / complexColProperty) "/" [ qualifiedComplexTypeName "/" ] ) 
                    *( ( containmentNav / colContainmentNav ) "/" [ qualifiedEntityTypeName "/" ] )
                    navigationProperty
```

For example, we have containment in [Trippin service](https://services.odata.org/V4/(S(qqntzoewadope25a3bh2d5bi))/TripPinServiceRW/$metadata)：

Person

```html
|---- Trips (containment)
    |---- PlanItems (containment)
        |---- NS.Flight (derived type of PlanItem)
            |---- Airline (navigation property)
```

Now we bind entityset `Airlines` to the property `Airline`. Since for navigation property binding, we need start from non-containment entityset, then route to navigation property with binding path. So we should have binding:

```C#
<EntitySet Name="People" EntityType="NS.Person">
    <NavigationPropertyBinding Path="Trips/PlanItems/NS.Flight/Airline" Target="Airlines"/>
</EntitySet>
```

If we have `InternationalTrips` under `Person` which has type `Trip` as well, we can have binding `<NavigationPropertyBinding Path="InternationalTrips/PlanItems/NS.Flight/Airline" Target="Airlines"/>` under `People` as well.
Please note that current Trippin implementation for this part does not have strict compliance with spec due to prohibition of breaking changes.

Let's have another example of complex which have multi bindings. `City` is a navigation property of complex type `Address`, and `Person` has `HomeAddress`, `CompanyAddress` which are both `Address` type. Then we can have binding:

```C#
<EntitySet Name="People" EntityType="Sample.Person">
    <NavigationPropertyBinding Path="HomeAddress/City" Target="Cities1" />
    <NavigationPropertyBinding Path="CompanyAddress/City" Target="Cities2" />
</EntitySet>
```

For single binding, binding path is just the navigation property name or type cast appending navigation property name. But for multiple bindings, binding path becomes an essential info to create a binding.
As a result, following APIs are added:

### EDM ###
`public EdmNavigationPropertyBinding (IEdmNavigationProperty navigationProperty, IEdmNavigationSource target, IEdmPathExpression bindingPath)`

Use this API to create EdmNavigationPropertyBinding instance if the bindingpath is not navigation property name.

 `public void AddNavigationTarget (IEdmNavigationProperty navigationProperty, IEdmNavigationSource target, IEdmPathExpression bindingPath)`

Add a navigation property binding and specify the whole binding path.

`public virtual Microsoft.OData.Edm.IEdmNavigationSource FindNavigationTarget (IEdmNavigationProperty navigationProperty, IEdmPathExpression bindingPath)`

Find navigation property with its binding path.

### ODL ###
`public ODataQueryOptionParser (IEdmModel model, ODataPath odataPath, String queryOptions)`

`public ODataQueryOptionParser(IEdmModel model, ODataPath odataPath, IDictionary<string, string> queryOptions, IServiceProvider container)`

Possibly need ODataPath to resolve navigation target of segments in query option if the navigation property binding path is included in both path and query option. Refer: [Navigation property under complex type](https://luoyan0517.github.io/odata.net/v7/#06-18-navigation-under-complex).

Take the above complex scenario for example. For generating this kind of model, we need use the new AddNavigationTarget API, and different navigation target can be specified:

```C#
people.AddNavigationTarget(cityOfAddress, cities1, new EdmPathExpression("HomeAddress/City"));
people.AddNavigationTarget(cityOfAddress, cities2, new EdmPathExpression("Addresses/City"));
```

`cityOfAddress` is the variable to present navigation property `City` under `Address`, and `cities1` and `cities2` are different entityset based on entity type `City`.

To achieve the navigation target, the new FindNavigationTarget API can be used:

```C#
IEdmNavigationSource navigationTarget = people.FindNavigationTarget(cityOfAddress, new EdmPathExpression("HomeAddress/City"));
```
