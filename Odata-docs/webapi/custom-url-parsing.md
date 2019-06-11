---
title : "6.1 Custom URL parsing"


ms.date: 01/16/2015
---
# Custom URL parsing
**Applies To**: [!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)]
[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Let's show how to extend the default OData Uri Parser behavior:

### Basic Case Insensitive Support
User can configure as below to support basic case-insensitive parser behavior.

```C#
HttpConfiguration config = …
config.EnableCaseInsensitive(caseInsensitive: true);
config.MapODataServiceRoute("odata", "odata", edmModel);
```
**Note**: Case insensitive flag enables both for metadata and key-words, not only on path segment, but also on query option.

For example:

* ~/odata/$metaDaTa
* ~/odata/cusTomers
...

### Unqualified function/action call
User can configure as below to support basic unqualified function/action call. 

```C#
HttpConfiguration config = …
config.EnableUnqualifiedNameCall(unqualifiedNameCall: true);
config.MapODataServiceRoute("odata", "odata", edmModel);
```

For example:

Original call:
* ~/odata/Customers(112)/Default.GetOrdersCount(factor=1)

Now, you can call as:
* ~/odata/Customers(112)/GetOrdersCount(factor=1)

Since Web API OData 5.6, it enables unqualified function in attribute routing. So, Users can add the unqualified function template. For example:
```C#
[HttpGet]  
ODataRoute("Customers({key})/GetOrdersCount(factor={factor})")]  
public IHttpActionResult GetOrdersCount(int key, [FromODataUri]int factor)
{
 ...
 }
```

#### Enum prefix free
User can configure as below to support basic string as enum parser behavior.

```C#
HttpConfiguration config = …
config.EnableEnumPrefixFree(enumPrefixFree: true);
config.MapODataServiceRoute("odata", "odata", edmModel);
```

For example:

Origin call:

```C#
* ~/odata/Customers/Default.GetCustomerByGender(gender=System.Web.OData.TestCommon.Models.Gender'Male')
```
Now, you can call as:

```C#
* ~/odata/Customers/Default.GetCustomerByGender(gender='Male')
```
#### Advance Usage
User can configure as below to support case insensitive & unqualified function call & Enum Prefix free:

```C#
HttpConfiguration config = …
config.EnableCaseInsensitive(caseInsensitive: true);
config.EnableUnqualifiedNameCall(unqualifiedNameCall: true);
config.EnableEnumPrefixFree(enumPrefixFree: true);

config.MapODataServiceRoute("odata", "odata", edmModel);
```
