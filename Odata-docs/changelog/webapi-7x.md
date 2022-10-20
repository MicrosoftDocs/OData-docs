---
title: "OData WebApi changelog"
description: OData WebApi 7.x changelog Improvements and Fixes
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
---

# OData WebApi changelog

## WebAPI 7.6.0

### Improvements and Fixes:

* [ [#2697](https://github.com/OData/WebApi/pull/2697) ] Support bulk operations serialization.

* [ [#2686](https://github.com/OData/WebApi/pull/2686) ] Support bulk operations deserialization.

## WebAPI 7.5.17

### Improvements and Fixes:

* [ [#2699](https://github.com/OData/WebApi/pull/2699) ] Add virtual method for complex property serialization.

* Fix the NewtonSoft.JSON package version reference problem.

## WebAPI 7.5.16

**Warning**: This version had [issues](https://github.com/OData/WebApi/issues/2700) and was unlisted from NuGet.

### Improvements and Fixes:

* [ [#2668](https://github.com/OData/WebApi/pull/2668) ] Update Newtonsoft.Json version to 13.0.1

* [ [#2652](https://github.com/OData/WebApi/pull/2668) ] Add settings flag to avoid swallowing serialization errors when ILogger is registered

* [ [#2692](https://github.com/OData/WebApi/pull/2668) ] Update ODL version to 7.12.2

## WebAPI 7.5.15

### Improvements and Fixes:

* [ [#2650](https://github.com/OData/WebApi/pull/2650) ] Argument null exception in containerbuilder on .net 6

* [ [#2420](https://github.com/OData/WebApi/pull/2420) ] Scale is not working on decimal property


## WebAPI 7.5.14

### Improvements and Fixes:

* [ [#2601](https://github.com/OData/WebApi/pull/2601) ] Update Patch to Return TStructuralType and Handle Patch for Derived Complex type

* [ [#2621](https://github.com/OData/WebApi/pull/2621) ] Update ODL version to Latest 7.10 in WebAPi projects

* [ [#2622](https://github.com/OData/WebApi/pull/2622) ] Create type annotation only with type name

* [ [#2626](https://github.com/OData/WebApi/pull/2626) ] Support $id references across entire batch


## WebAPI 7.5.12

### Improvements and Fixes:

* [ [#2339](https://github.com/OData/WebApi/pull/2339) ] Ensure that untyped values escaped in ODL are unescaped in OData WebApi during deserialization

* [ [#2544](https://github.com/OData/WebApi/pull/2544) ] Fix to ignore Unmapped properties in Delta\<T\>

* [ [#2575](https://github.com/OData/WebApi/pull/2575) ] Fixing Patching Complex type when using one derived type over another

* [ [#2582](https://github.com/OData/WebApi/pull/2582) ] Enable Auto expand on complex type

## WebAPI 7.5.11

### Improvements and Fixes:

* [ [#2559](https://github.com/OData/WebApi/pull/2559) ] Fix attribute routing error when action parameters don't match route keys

* [ [#2565](https://github.com/OData/WebApi/pull/2565) ] Consider the operation segment when determining the target structured type of an action or function

* Fix a code signing issue affecting 7.5.10

## WebAPI 7.5.10

### Improvements and Fixes:

* [ [#2483](https://github.com/OData/WebApi/pull/2483) ] Support changing of updatable properties when handling delta updates

* Fix an issue with Microsoft.OData.Core 7.9.0 dependency

---

## WebAPI 7.5.9

### New Features:

* [ [#2421](https://github.com/OData/WebApi/pull/2421) ] Add support for $this

* [ [#2456](https://github.com/OData/WebApi/pull/2456) ] Built-in support for deeply nested paths

* [ [#2521](https://github.com/OData/WebApi/pull/2521) ] Add support for $count segment in filter

### Improvements and Fixes:

* [ [#2470](https://github.com/OData/WebApi/pull/2470) ] Fix casing issue preventing project from compiling under case-sensitive OS like Linux

* [ [#2491](https://github.com/OData/WebApi/pull/2491) ] Improve logic around dependency injection and services registration

* [ [#2492](https://github.com/OData/WebApi/pull/2492) ] Fix ClrTypeCache memory leak

* [ [#2494](https://github.com/OData/WebApi/pull/2494) ] Fix API Explorer usage with OData

* [ [#2497](https://github.com/OData/WebApi/pull/2497) ] Upgrade Microsoft.OData.Core, Microsoft.OData.Edm and Microsoft.Spatial dependencies to v7.9.0

* [ [#2512](https://github.com/OData/WebApi/pull/2512) ] Fix behaviour on status code results (for example error response from a controller action decorated with EnableQuery attribute)

* [ [#2515](https://github.com/OData/WebApi/pull/2515) ] Enable constant parameterization in DateTime constants

* [ [#2525](https://github.com/OData/WebApi/pull/2525) ] Fixed Enum TryParse issue

* [ [#2530](https://github.com/OData/WebApi/pull/2530) ] Fix bug in filter=any(...) within expand

* [ [#2538](https://github.com/OData/WebApi/pull/2538)/[#2539](https://github.com/OData/WebApi/pull/2539)/[#2540](https://github.com/OData/WebApi/pull/2540) ] Bump System.Text.RegularExpressions from 4.3.0 to 4.3.1

---

## WebAPI 7.5.8

### Breaking Changes:

### New Features:

* N/A

### Improvements and fixes:

* [ [#2426](https://github.com/OData/WebApi/pull/2426) ] Multiple orderby level.

* [ [#2457](https://github.com/OData/WebApi/pull/2457) ] Filter is not working with date function

* [ [#2482](https://github.com/OData/WebApi/pull/2482) ] Revert "inconsistencies in query options when_returnscollection is used on collection entity. [#2398](https://github.com/OData/WebApi/pull/2398) ]" 

---

## WebAPI 7.5.7

### Breaking Changes:

### New Features:

* N/A

### Improvements and fixes:

* [ [#2450](https://github.com/OData/WebApi/pull/2450) ] Add the OData version to reader settings when specified in the request

* [ [#2445](https://github.com/OData/WebApi/pull/2445) ] ODataResourceDeserializer Preserves Request on ODataDeserializerContext for Nested Resource

* [ [#2438](https://github.com/OData/WebApi/pull/2438) ] Extend runtime Type methods of OperationConfiguration

* [ [#2436](https://github.com/OData/WebApi/pull/2436) ] Excessive memory in ODataConventionModelBuilder BuildDerivedTypesMapping

* [ [#2431](https://github.com/OData/WebApi/pull/2431) ] Using Metadata writer async

* [ [#2418](https://github.com/OData/WebApi/pull/2418) ] Check for empty Namespace

* [ [#2398](https://github.com/OData/WebApi/pull/2398) ] Inconsistencies in query options when returnscollection is used on collection of entity types

---

## WebAPI 7.5.6

### Breaking Changes:

### New Features:

* [ [#2401](https://github.com/OData/WebApi/pull/2401) ] Support write resource with stream property and Support query stream property

### Improvements and fixes:

* Add source link for source code debugging. To debug (at least):
  - Enable Nuget.org Symbol Server
  - Enable Source Link support

---

## WebAPI 7.5.5

### Breaking Changes:

### New Features:

### Improvements and fixes:

* Fix the ODL package version dependency issue

* [ [#2397](https://github.com/OData/WebApi/pull/2397) ] Moving EnableQuery request level query data storage to HttpContext

---

## WebAPI 7.5.4

### Breaking Changes:
* [ [#2341](https://github.com/OData/WebApi/pull/2341) ] Create ODataError in Non-Success responses

If your code has custom implementations of the methods below, you may encounter a breaking change.
``` csharp
BadRequest(string message);
Conflict(string message);
Unauthorized(string message);
NotFound(string message);
UnprocessableEntity(string message);
```

For example, in your custom implementation of `BadRequest(string message)`, you may be inheriting from `BadRequestObjectResult`, yet in this fix, we are inheriting from `BadRequestResult`.

### New Features:

* [ [#2361](https://github.com/OData/WebApi/pull/2361) ] Enable JSON Metadata
  You can use one of the following to return the JSON metadata:
  - $format=application/json
  - $format=json
  - Accept=application/json

* [ [#2341](https://github.com/OData/WebApi/pull/2341) ] Create ODataError in Non-Success responses

* [ [#2311](https://github.com/OData/WebApi/pull/2311) ] Enable case insensitive request binding


### Improvements and fixes:

* [ [#2379](https://github.com/OData/WebApi/pull/2379) ] Fix duplicate batch Cookie header

* [ [#2377](https://github.com/OData/WebApi/pull/2377) ] Fix MergeIndividualAndBatchPreferences method

* [ [#2368](https://github.com/OData/WebApi/pull/2368) ] Fix bug in attribute convention route values

* [ [#2362](https://github.com/OData/WebApi/pull/2362) ] Enabling query validation EnableQuery before action execution

* [ [#2356](https://github.com/OData/WebApi/pull/2356) ] Fix behaviour for Preflight requests on the ODataBatchMiddleware

* [ [#2332](https://github.com/OData/WebApi/pull/2332) ] Fix invalid next page link in nested collections

---

## WebAPI 7.5.2

### New Features:

### Improvements and fixes:

* [ [#2342](https://github.com/OData/WebApi/pull/2342) ] Remove GuidCompare method in ExpressionBinderBase.cs

  It's breaking change since 'GuidCompare' was a public API. Please use Guid.CompareTo(Guid) method directly.

* [ [#2336](https://github.com/OData/WebApi/pull/2336) ] Full async reader & writer support

  This change makes the call stack within ASP.NET Core OData completely asynchronous,
  enabling ASP.NET Core applications to use OData without requiring `AsynchronousIO=true`.
  The change also wraps the stream given to OData Library to ensure that all calls made
  from OData Library to the stream are through asychnronous APIs.

* [ [#2332](https://github.com/OData/WebApi/pull/2332) ] Fix invalid next page link in nested collections

---

## WebAPI 7.5.1

### New Features:

### Improvements and fixes:

* [ [#2299](https://github.com/OData/WebApi/pull/2299) ] Create the correct expression when casting entity/complex type to derived type

* [ [#2302](https://github.com/OData/WebApi/pull/2302) ] Group by on date throws exceptions

* [ [#2306](https://github.com/OData/WebApi/pull/2306) ] Throw exception if the function call without providing the parameter value

* [ [#2310](https://github.com/OData/WebApi/pull/2310) ] Fix buggy behaviour when addressing derived types

* [ [#2313](https://github.com/OData/WebApi/pull/2313) ] Add support for parameters defined with type

* [ [#2315](https://github.com/OData/WebApi/pull/2315) ] Fix routing regression causing 404 on actions with [FromQuery]

* [ [#2320](https://github.com/OData/WebApi/pull/2320) ] Select a complex with null value fails

* [ [#2323](https://github.com/OData/WebApi/pull/2323) ] Fix regression affecting usage of "in" expression with primitive collection property on RHS

---

## WebAPI 7.5.0

The NuGet packages for ASP.NET Web API OData v7.5.0 are available on the [NuGet gallery](https://www.nuget.org/).

The corresponding symbols now can load from the nuget symbol server.

### New Features:

* [ [#2252](https://github.com/OData/WebApi/pull/2252) ] Add HEAD Support for Attribute Routing

* [ [#2219](https://github.com/OData/WebApi/pull/2219) ] Support intance annotation

* [ [#2195](https://github.com/OData/WebApi/pull/2195) ] Add cast operation to FilterBinder.BindIn

* [ [#2191](https://github.com/OData/WebApi/pull/2191) ] support long URL pattern

* [ [#2189](https://github.com/OData/WebApi/pull/2189) ] Enable convention routing for operation import

* [ [#2167](https://github.com/OData/WebApi/pull/2167) ] support fine grained cors access control


### Improvements and fixes:

* [ [#2232](https://github.com/OData/WebApi/pull/2232) ] AspNetCore Resolving of Batch ContentId for AddRelatedObject

* [ [#2231](https://github.com/OData/WebApi/pull/2231) ] Enhance $batch accept header decision

* [ [#2288](https://github.com/OData/WebApi/pull/2288) ] ResourceContext.ResourceInstance throws exception if there's a select or expand

* [ [#2287](https://github.com/OData/WebApi/pull/2287) ] Invalid partial JSON response happens expanding an entity

* [ [#2279](https://github.com/OData/WebApi/pull/2279) ] Fix incorrect child collection nextlink

* [ [#2267](https://github.com/OData/WebApi/pull/2267) ] enable enum member alias for query options

* [ [#2248](https://github.com/OData/WebApi/pull/2248) ] Fix invalid location header generated when key property for a model contains unicode chars

* [ [#2246](https://github.com/OData/WebApi/pull/2246) ] Fix Delta\<TStructuralType\>.GetInstance() method

* [ [#2200](https://github.com/OData/WebApi/pull/2200) ] Avoid NullReferenceException on TruncatedCollection\<T\>

* [ [#2197](https://github.com/OData/WebApi/pull/2197) ] Fix skip token encoding

* [ [#2168](https://github.com/OData/WebApi/pull/2168) ] Improve action selector routing

* [ [#2142](https://github.com/OData/WebApi/pull/2142) ] Remove the null check for classic EF

* [ [#2060](https://github.com/OData/WebApi/pull/2060) ] opertation returns path for the return entity set or singleton

---

## WebAPI 7.4.1

### Improvements and fixes:

* [ [#2053](https://github.com/OData/WebApi/pull/2053) ] Fix client-side evaluation for string comparison queries

* [ [#2147](https://github.com/OData/WebApi/pull/2147) ] Fix [#2141](https://github.com/OData/WebApi/issues/2141) and [#2144](https://github.com/OData/WebApi/issues/2144): the context Uri and location wrong with PathBase setting

* [ [#2149](https://github.com/OData/WebApi/pull/2149) ] Remove sync calls within async batch processing

* [ [#2154](https://github.com/OData/WebApi/pull/2154) ] SetDefaultODataOptions doesn't work

* [ [#2160](https://github.com/OData/WebApi/pull/2160) ] Fix logic for patching structured type with complex type dynamic property

* [ [#2169](https://github.com/OData/WebApi/pull/2169) ] Copy query settings correctly

* [ [#2172](https://github.com/OData/WebApi/pull/2172) ] Fix EfCore memory leak when query contains collection constant

---

## WebAPI 7.4.0

### New Features:

* [ [#1977](https://github.com/OData/WebApi/pull/1977) ] Add support for derived type constraint annotation.

---

## WebAPI 7.4.0-beta2

### Improvements and fixes:

* Revert the default `HandleNullPropagationOption` to `HandleNullPropagationOption.False` by default for EF Core.

* [ [#2055](https://github.com/OData/WebApi/issues/2055) ] Batch request copies content headers to embedded requests without content.

---

## WebAPI 7.4.0-beta

The NuGet packages for ASP.NET Web API OData v7.4.0 are available on the [NuGet gallery](https://www.nuget.org/).

### EF Core 3.x breaking changes risk

Assessed EF Core 3.x aggregation support

Aggregation support was introduced in EF Core 2.0, however it was implemented as client-side execution. Query translation was rewritten in EF Core 3.0 and client-side evaluation was removed to leverage database side execution and achieve great performance. However, not all LINQ queries translated to appropriate SQL queries. As a result, $apply isn’t compatible with EF Core 3.0 and 3.1. We will work with EF Core team to extend aggregation support in next versions of EF Core. Meanwhile, we recommend using Entity Framework 6. Starting with EF 6.3 it works with .NET Core 3 on all supported platform.

### New Features:

* [ [#2035](https://github.com/OData/WebApi/pull/2035) ] Support Endpoint routing.

* [ [#1877](https://github.com/OData/WebApi/pull/1877) ] Add supports for compute() transformation in $apply

### Improvements and fixes:

* [ [#2014](https://github.com/OData/WebApi/pull/2014) ] Fix performance drop when using a big model.

* [ [#2048](https://github.com/OData/WebApi/pull/2048) ] Fix multiple batch issues

* [ [#1962](https://github.com/OData/WebApi/pull/1962) ] Fix cast issue

* [ [#1953](https://github.com/OData/WebApi/pull/1953) ] Fix Skiptoken value with DateTime

* [ [#1948](https://github.com/OData/WebApi/pull/1948) ] Use ODataValueProvider

* [ [#1933](https://github.com/OData/WebApi/pull/1933) ] Ensures nullable enums are handled for In operator
---

## WebAPI 7.3.0

The NuGet packages for ASP.NET Web API OData v7.3.0 are available on the [NuGet gallery](https://www.nuget.org/).

You can install or update the NuGet packages for OData Web API v7.3.0 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```PowerShell

PM> Install-Package Microsoft.AspNetCore.OData -Version 7.3.0

```

or

```PowerShell

PM> Install-Package Microsoft.AspNet.OData -Version 7.3.0

```

### New Features:

* [ [#1748](https://github.com/OData/WebApi/issues/1748) ] Add .NET Core 3.x supporting.

  Be noted: v7.3.0 includes .NET Core 3.1 supporting, meanwhile v7.3.0-beta includes .NET Core 3.0 supporting.

### Improvements and fixes:

* [ [#1992)](https://github.com/OData/WebApi/issues/1992)) ] Missing Null check in SelectExpandNode class

* [ [#1989](https://github.com/OData/WebApi/issues/1989) ] Fix error message to state that value must be 1 or greater, instead of zero or greater

* [ [#1984](https://github.com/OData/WebApi/issues/1984) ] $select on property of type byte[] not working since 7.2.3

---

## WebAPI 7.2.3

The NuGet packages for ASP.NET Web API OData v7.2.3 are available on the [NuGet gallery](https://www.nuget.org/).

You can install or update the NuGet packages for OData Web API v7.2.3 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```PowerShell

PM> Install-Package Microsoft.AspNetCore.OData -Version 7.2.3
```

or

```PowerShell

PM> Install-Package Microsoft.AspNet.OData -Version 7.2.3

```
### New Features:

* [ [#1941](https://github.com/OData/WebApi/pull/1941) ] Enable $select improvement with path and nested query options


### Improvements and fixes:

* [ [#1965](https://github.com/OData/WebApi/issues/1965) ] Inconsistent behavior on 'key' parameter convention for OData routes

---

## WebAPI 7.2.2

The NuGet packages for ASP.NET Web API OData v7.2.2 are available on the [NuGet gallery](https://www.nuget.org/).

You can install or update the NuGet packages for OData Web API v7.2.2 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```PowerShell

PM> Install-Package Microsoft.AspNetCore.OData -Version 7.2.2
```

or

```PowerShell

PM> Install-Package Microsoft.AspNet.OData -Version 7.2.2

```
### New Features:

* [ [#1920](https://github.com/OData/WebApi/pull/1920) ] Add a compatibility flag for generating next link.

This provides a flag that can revert the breaking change introduced in the last release. You can use this flag to generate the incorrect nextlink to avoid a null nextlink. 

* [ [#1839](https://github.com/OData/WebApi/pull/1839) ] Support groupby with property path length greater than 2
* [ [#1738](https://github.com/OData/WebApi/pull/1738) ] Support navigation properties on complex types

### Improvements and fixes:
* [ [#1919)](https://github.com/OData/WebApi/pull/1919)) ] Update to ODL 7.6.1. 
* [ [#1910](https://github.com/OData/WebApi/pull/1910) ] Allow IN operator for enums
* [ [#1907](https://github.com/OData/WebApi/pull/1907) ] AmbiguousMatchException in ApplyTo method
* [ [#1895](https://github.com/OData/WebApi/pull/1895) ] Ensure generated skip token respects casing conventions
* [ [#1884](https://github.com/OData/WebApi/issues/1872) ] Ensure that all expand item options preserved when translating SELECT *
* [ [#1882](https://github.com/OData/WebApi/issues/1882) ] ODataQuerySettings missing copy EnableCorrelatedSubqueryBuffering in ODataQuerySettings .CopyFrom function.

* [ [#1814](https://github.com/OData/WebApi/issues/1814) ] CreateODataResult.Entity made public
---

## WebAPI 7.2.1

The NuGet packages for ASP.NET Web API OData v7.2.0 are available on the [NuGet gallery](https://www.nuget.org/).

You can install or update the NuGet packages for OData Web API v7.2.0 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```PowerShell

PM> Install-Package Microsoft.AspNetCore.OData -Version 7.2.0 
```

or

```PowerShell

PM> Install-Package Microsoft.AspNet.OData -Version 7.2.0

```
### Breaking Changes:
* [ [#1724](https://github.com/OData/WebApi/pull/1724) ] Custom NextLink generation with default SkipToken implementation

If your code is using the request extension method to generate the nextlink, you may encounter a breaking change. Previously, if the page size was greater than $top query option value, the nextlink generated would have contained negative $top value. Now, we return null if the page size is larger than the $top value.  

### New Features:

* [ [#1844](https://github.com/OData/WebApi/pull/1844) ] Support count in orderby and filter query options

* [ [#1839](https://github.com/OData/WebApi/pull/1839) ] Support 4.01 OData version

* [ [#1797](https://github.com/OData/WebApi/pull/1797) ] Enable $ref after $expand

* [ [#1728](https://github.com/OData/WebApi/pull/1728) ] Support $apply for EF Core

* [ [#1724](https://github.com/OData/WebApi/pull/1724) ] Custom NextLink generation with default SkipToken implementation


### Improvements and fixes:

* [ [#1867](https://github.com/OData/WebApi/pull/1867) ] Fix ordering the keys according to the ColumnAttribute.Order property
Update to OData Library v7.6 1825
* [ [#1855](https://github.com/OData/WebApi/pull/1855) ] Flatten properties before using in aggregation to avoid nested querie…

* [ [#1854](https://github.com/OData/WebApi/pull/1854) ] Fix error response in ValidateODataBatchRequest

* [ [#1851](https://github.com/OData/WebApi/pull/1851) ] Support default value for primitive and enum property

* [ [#1849](https://github.com/OData/WebApi/pull/1849) ] Improvement support continue-on-error in batch request

* [ [#1825](https://github.com/OData/WebApi/pull/1825) ] Update to OData Library v7.6

* [ [#1823](https://github.com/OData/WebApi/pull/1823) ] Support leading and trailing spaces in query options

* [ [#1802](https://github.com/OData/WebApi/issues/1802) ] ODataEnumDeserializer.ReadInline fails with InvalidCastException when edmType comes from CsdlParser

* [ [#1742](https://github.com/OData/WebApi/pull/1715) ]  Fixes and coverage for primitives in URL parameters.

* [ [#1725](https://github.com/OData/WebApi/issues/1802) ]
No coercion operator is defined between types 'Microsoft.OData.Edm.Date' and 'System.DateTimeOffset'.
* [ [#1715](https://github.com/OData/WebApi/pull/1715) ] Fix for Nav/any(f:false) conversion

* [ [#1693](https://github.com/OData/WebApi/pull/1693) ] Fix assignment of CollectionExpandedProperty.TotalCount when using EF

* [ [#1653](https://github.com/OData/WebApi/pull/1653) ] Fixing N+1 query bug on expands

* [ [#1542](https://github.com/OData/WebApi/pull/1542) ] Fix SelectExpandBinder to take into account constant parameterization

---

## WebAPI 7.1.0

The NuGet packages for ASP.NET Web API OData v7.1.0 are available on the [NuGet gallery](https://www.nuget.org/).

You can install or update the NuGet packages for OData Web API v7.1.0 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```PowerShell

PM> Install-Package Microsoft.AspNetCore.OData -Version 7.1.0 
```

or

```PowerShell

PM> Install-Package Microsoft.AspNet.OData -Version 7.1.0

```

>[!Notes]:
>[issue #1591](https://github.com/OData/WebApi/issues/1591) fixes an issue where types created by the ODataModelBuilder did not respect the namespace of the >ModelBuilder and instead used the namespace of the CLR type. With [PR #1592](https://github.com/OData/WebApi/pull/1592), OData WebAPI 7.1.0 now correctly uses the namespace on the ModelBuilder, if it has been explicitly set. In order to retain the old behavior of using the namespace of the CLR type, do not set the namespace on the ModelBuilder, or set the namespace on the ModelBuilder to null or to the desired namespace of the CLR type.


### New Features:

* [ [#1631](https://github.com/OData/WebApi/issues/1631) ] Don't require keys for singleton instances

* [ [#1628](https://github.com/OData/WebApi/pull/1628) ] Allow adding new members to a collection through a POST request

* [ [#1591](https://github.com/OData/WebApi/issues/1591) ] Support namespaces in OData ModelBuilder

* [ [#1656](https://github.com/OData/WebApi/issues/1656) ] Allowing the definition of partner relationships


### Improvements and fixes:

* [ [#1543](https://github.com/OData/WebApi/issues/1543) ] Json batch response body if 404

* [ [#1555](https://github.com/OData/WebApi/issues/1555) ] aspnetcore ETagMessageHandler throws then ClrType property name and EdmModel property name differs

* [ [#1559](https://github.com/OData/WebApi/issues/1559) ] Don't assume port 80 for nextLink when request was HTTPS

* [ [#1579](https://github.com/OData/WebApi/issues/1579) ] Star select ( $select= * ) not returning dynamic properties

* [ [#1588](https://github.com/OData/WebApi/pull/1588) ] Null check on ODataQueryParameterBindingAttribute for Net Core

* [ [#736](https://github.com/OData/WebApi/issues/736) ] EDMX returned from $metadata endpoint has incorrect "Unicode" attributes

* [ [#850](https://github.com/OData/WebApi/issues/850) ] ODataConventionModelBuilder takes into account [EnumMember] on enum members, but ODataPrimitiveSerializer (?) does not, causing mismatch in the payload.

* [ [#1612](https://github.com/OData/WebApi/pull/1612) ] Add the iconUrl to the nuget spec

* [ [#1615](https://github.com/OData/WebApi/pull/1615) ] fix compile error in sample project

* [ [#1421](https://github.com/OData/WebApi/issues/1421) ] Improve error message when structured type is received and properties are in incorrect case

* [ [#1658](https://github.com/OData/WebApi/pull/1658) ] Fix the security vulnerabilities in project dependencies

* [ [#1637](https://github.com/OData/WebApi/issues/1637) ] Change Request: Remove null check on 'ODataFeature().TotalCount' property

* [ [#1673](https://github.com/OData/WebApi/pull/1673) ] Respect preference header for paging

* [ [#1600](https://github.com/OData/WebApi/issues/1600) ] ActionDescriptorExtensions is not thread safe

* [ [#1617](https://github.com/OData/WebApi/issues/1617) ] Take and Top query use wrong Provider.CreateQuery Method

* [ [#1659](https://github.com/OData/WebApi/issues/1659) ] OData V4 group by with Top and skip not working

* [ [#1679](https://github.com/OData/WebApi/issues/1679) ] Fix typo: routePrerix updated to routePrefix

---

## WebAPI 7.0.1

The NuGet packages for ASP.NET Web API OData v7.0.1 are available on the [NuGet gallery](https://www.nuget.org/).

You can install or update the NuGet packages for OData Web API v7.0.1 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```powershell
PM> Install-Package Microsoft.AspNetCore.OData -Version 7.0.1 
```

or

```powershell
PM> Install-Package Microsoft.AspNet.OData -Version 7.0.1
```


### New Features:

* [ [Web API issue #1488](https://github.com/OData/WebApi/issues/1488) ] Support optional parameters

* [ [Web API PR #1527](https://github.com/OData/WebApi/pull/1527) ] Add UseOData() extension methods

### Improvements and fixes:

* [ [Web API issue #1518](https://github.com/OData/WebApi/issues/1518) ] Fix the Newtonsoft.Json dependency problem in classic version

* [ [Web API issue #1529](https://github.com/OData/WebApi/issues/1529) ] Fix ETag missing on derived Entity Set

* [ [Web API issue #1532](https://github.com/OData/WebApi/issues/1532) ] Fix JSON batch response content type

---

## WebAPI 7.0

We're happy to announce the release of ASP.NET Web API OData 7.0 on the [NuGet gallery](https://www.nuget.org/)!

ASP.NET Web API OData 7.0 is available in two packages:

1. [Microsoft.AspNetCore.OData](https://www.nuget.org/packages/Microsoft.AspNetCore.OData) is based on ASP.NET Core 2.0.
2. [Microsoft.AspNet.OData](https://www.nuget.org/packages/Microsoft.AspNet.OData) is based on ASP.NET Web API.

[Get started](https://github.com/OData/ODataSamples/tree/master/WebApiCore/ODataAspnetCore7xSample) with ASP.NET Web API OData 7.0 today!

### Download this release

You can install or update the NuGet packages for OData Web API v7.0.0 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```powershell
PM> Install-Package Microsoft.AspNetCore.OData
```
or
```powershell
PM> Install-Package Microsoft.AspNet.OData
```


### ASP.NET Core 2.0 support

 See main issues from:

 * [ [Web API issue #939](https://github.com/OData/WebApi/issues/939) ] .NET Core Support.
  
 * [ [Web API issue #772](https://github.com/OData/WebApi/issues/772) ] Roadmap for OData WebAPI run on ASP.NET Core.
  
 * [ [Web API issue #229](https://github.com/OData/WebApi/issues/229) ] Port ASP.NET Web API OData to ASP.NET 5/MVC 6.
  
 Getting started ASP.NET Core OData from [here](https://github.com/OData/WebApi/blob/gh-pages/_posts/2017-12-21-14-01-netcore-beta1.md), get samples from [here](https://github.com/OData/ODataSamples/tree/master/WebApi/v7.x).

### New features
 
 * [ [PR #1497](https://github.com/OData/WebApi/issues/1497) ] Support `In` operator.
  
 * [ [PR #1409](https://github.com/OData/WebApi/pull/1409) ] Set default to Unqualified-function/action call and case insensitive.

 * [ [PR #1393](https://github.com/OData/WebApi/pull/1393) ] Set default to enable KeyAsSegment.
 
 * [ [Issue #1503](https://github.com/OData/WebApi/issues/1503) ] Enable support for Json Batch.
 
 * [ [Issue #1386](https://github.com/OData/WebApi/issues/1386) ] Use TemplateMatcher for ODataBatchPathMapping.
  
 * [ [Issue #1248](https://github.com/OData/WebApi/issues/1248) ] Allow recursive complex types in OData model builder.
 
 * [ [Issue #1225](https://github.com/OData/WebApi/issues/1225) ] Support optional dollar sign.
 
 * [ [Issue #893](https://github.com/OData/WebApi/issues/893) ] Support aggregation of Entity Set.

### Breaking changes
 
 * Both [Microsoft.AspNetCore.OData](https://www.nuget.org/packages/Microsoft.AspNetCore.OData) and [Microsoft.AspNet.OData](https://www.nuget.org/packages/Microsoft.AspNet.OData) are using "**Microsoft.AspNet.OData**" namespace.
 
 * [ [PR #1489](https://github.com/OData/WebApi/pull/1489) ] Change the OptionalReturn to ReturnNullable in OperationConfiguration.
  
 * [ [PR #1353](https://github.com/OData/WebApi/pull/1353) ] Change OptionalParameter as Nullable .

### Improvements & Fixes

* [ [Issue #1510](https://github.com/OData/WebApi/issues/1510) ] move $top & $skip execute as late as possible.
 
* [ [Issue #1489](https://github.com/OData/WebApi/issues/1489) ] Return Task<> from method of controller doesn't pick up OData output formatter (Core).
 
* [ [Issue #1407](https://github.com/OData/WebApi/issues/1407) ] Fix the BaseAddressFactory in ODataMediaTypeFormatter .
  
* [ [Issue #1471](https://github.com/OData/WebApi/issues/1471) ] Issue in non-odata routing with DataContact & DataMember.
 
* [ [Issue #1434](https://github.com/OData/WebApi/issues/1434) ] Add OData-Version into the No-Content response header.

* [ [Issue #1398](https://github.com/OData/WebApi/issues/1398) ] Expose underlying semantic OData path.

* [ [Issue #1388](https://github.com/OData/WebApi/issues/1388) ] Make Match as virtual in ODataPathRouteConstraint.
 
* [ [Issue #1387](https://github.com/OData/WebApi/issues/1387) ] Move Instance to select all.

* [ [Issue #1313](https://github.com/OData/WebApi/issues/1313) ] Batch requests are incorrectly routed when ASP.NET Core OData web application has any BasePath.

* [ [Issue #1263](https://github.com/OData/WebApi/issues/1263) ] Patch nested structural resources.
 
* [ [Issue #1247](https://github.com/OData/WebApi/issues/1247) ] Fix Spatial post/update problem.
 
* [ [Issue #1113](https://github.com/OData/WebApi/issues/1113) ] ODataResourceDeserializer no longer supports null ComplexType values.
 
* [ [Issue #822](https://github.com/OData/WebApi/issues/822) ] Fix for memory leak in EdmLibHelpers.
 
---

## WebAPI 7.0 beta

**Web API OData Beta1, Beta2 & Beta4** includes a new package for [WebApi OData V7.0.0 for ASP.NET Core 2.x](https://www.nuget.org/packages/Microsoft.AspNetCore.OData/).
The nightly version of this package is available from this [nightly url](https://www.myget.org/F/webapinetcore/api/v3/index.json).

There is also a new [WebApi OData V7.0.0 for ASP.NET Framework](https://www.nuget.org/packages/Microsoft.AspNet.OData/7.0.0-beta4). 
The nightly version of this package is available from this [nightly url](https://www.myget.org/F/webapinetcore/api/v3/index.json). 
The APIs are the same as those in WebApi OData V6.x, except for the new namespace `Microsoft.AspNet.OData` for V7, which is changed from `System.Web.OData`.

Both packages depends on [OData Lib 7.0.0](https://www.nuget.org/packages/Microsoft.OData.Core/7.0.0).

The code for the packages can be found [here](https://github.com/OData/WebApi/tree/master)

### Known Issues

**Web API OData for ASP.NET Core Beta1**, has following limitations which are known issues:
* Batching is not fully supported
* Using EnableQuery in an HTTP route, i.e. non-OData route, is not fully functional
* [#1175](https://github.com/OData/WebApi/issues/1175) - When you first start your service under a debugger, the project app URL will
  likely make a request on a non-OData route. This will fail with an exception `Value cannot be null. Parameter name: routeName`. You
   can work around this issue by adding `routes.EnableDependencyInjection();` in `UseMvc()` lambda in `Configure`. You can configure
   the default startup request in **Project properties**, **Debug**, **App URL**.

**Web API OData for ASP.NET**, there are no known issues.

### OData V7.0.0 for ASP.NET Core 2.x

The new OData V7.0.0 for ASP.NET Core 2.x package supports the same features set as Web API OData V6.0.0 but works with ASP.NET Core.
You can learn more about ASP.NET Core from the [documentation](/aspnet/core/).

To get started with OData V7.0.0 for ASP.NET Core 2.x, you can use code that is very similar to Web API OData V6.0.0. All of the
documentation in [Writing a simple OData V4 service](/odata/webapi/getting-started) is correct except for
configuring the OData endpoint. Instead of using the Register() method, you'll follow the new [service + route configuration model](/aspnet/core/fundamentals/startup)
used in ASP.NET Core.

The namespace for both Web API OData packages is Microsoft.AspNet.OData.

#### a. Create the Visual Studio project

In Visual Studio 2017, create a new C# project from the **ASP.NET Core Web Application** template. Name the project "ODataService".

In the New Project dialog, select **ASP.NET Core 2.0** and select the **WebApi** template. Click **OK**.

#### b. Install the OData packages

In the Nuget Package Manager, install `Microsoft.AspNetCore.OData` and all it's dependencies.

#### c. Add a model class

Add a C# class to the **Models** folder:

```C#
namespace ODataService.Models
{
    public class Product
    {
        public int ID { get; set; }
        public string Name { get; set; }
    }
}
```

#### d. Add a controller class

Add a C# class to the **Controllers** folder:

```C#
namespace ODataService.Controllers
{
    public class ProductsController : ODataController
    {
        private List<Product> products = new List<Product>()
        {
            new Product()
            {
                ID = 1,
                Name = "Bread",
            }
        };

        [EnableQuery]
        public List<Product> Get()
        {
            return products;
        }
    }
}
```

In the controller, we defined a `List<Product>` object which has one product element. It's considered as an in-memory storage
of the data of the OData service.

We also defined a `Get` method that returns the list of products. The method refers to the handling of HTTP GET requests. We'll
cover that in the sections about routing.

This `Get` method is decorated with `EnableQueryAttribute`, which in turns supports OData query options, for example `$expand, $filter` etc.

#### e. Configure the OData Endpoint

Open the file Startup.cs. Replace the existing `ConfigureServices` and `Configure` methods with the
following code:

```C#
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.AddOData();
}

public void Configure(IApplicationBuilder app)
{
    var builder = new ODataConventionModelBuilder(app.ApplicationServices);

    builder.EntitySet<Product>("Products");

    app.UseMvc(routeBuilder =>
    {
        // and this line to enable OData query option, for example $filter
        routeBuilder.Select().Expand().Filter().OrderBy().MaxTop(100).Count();

        routeBuilder.MapODataServiceRoute("ODataRoute", "odata", builder.GetEdmModel());

        // uncomment the following line to Work-around for #1175 in beta1
        // routeBuilder.EnableDependencyInjection();
    });
}
```

#### f. Start the OData service

Start the OData service by running the project and open a browser to consume it. You should be able to get access to the service
document at `https://host/odata/` in which `https://host/odata/` is the root path of your service. The metadata document
can be accessed at `GET https://host:port/odata/$metadata` and the products at `GET https://host:port/odata/Products` where
`host:port` is the host and port of your service, usually something like `localhost:1234`.

#### g. Explore

As mentioned earlier, most of the samples for Web API OData V6.0.0 apply to Web API OData V7.0.0. One of the design goals was to keep
the API between the two as similar as possible. While the APIs are similar, they are not identical due to differences between
ASP.NET and ASP.NET Core, such as HttpRequestMessage is now HttpRequest.

##### ODataRoutingConvention
Both `Microsoft.AspNet.OData` and `Microsoft.AspNetCore.OData` packages support same set of OData routing conventions, including default built-in routing conventions and attribute routing convention, so that each request can be routed to matching controller for processing. All routing conventions implement the interface `IODataRoutingConvention`, however, with different definitions, as ``` csharp below, for the two packages due to different route matching implementations based on ASP.NET Framework and ASP.NET Core:

For ASP.NET Framework:

```C#
namespace Microsoft.AspNet.OData.Routing.Conventions
{
    /// <summary>
    /// Provides an abstraction for selecting a controller and an action for OData requests.
    /// </summary>
    public interface IODataRoutingConvention
    {
        /// <summary>
        /// Selects the controller for OData requests.
        /// </summary>
        string SelectController(ODataPath odataPath, HttpRequestMessage request);

        /// <summary>
        /// Selects the action for OData requests.
        /// </summary>
        string SelectAction(ODataPath odataPath, HttpControllerContext controllerContext, ILookup<string, HttpActionDescriptor> actionMap);
    }
}
```

For ASP.NET Core 2.x:
  
```C#
namespace Microsoft.AspNet.OData.Routing.Conventions
{
    /// <summary>
    /// Provides an abstraction for selecting a controller and an action for OData requests.
    /// </summary>
    public interface IODataRoutingConvention
    {
        /// <summary>
        /// Selects the controller and action for OData requests.
        /// </summary>        
        IEnumerable<ControllerActionDescriptor> SelectAction(RouteContext routeContext);
    }
}
```

Specific routing convention, e.g. MetadataRoutingConvention, typically implements the package-specific interface, provides package-specific implementation, and shares common logic for both platform in the `Microsoft.AspNet.OData.Shared` shared project.
