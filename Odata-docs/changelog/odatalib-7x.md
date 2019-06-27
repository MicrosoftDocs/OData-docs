---
title: "ODataLib changelog"
description: "ODataLib 7.x"
author: madansr7
ms.author: madansr7
ms.date: 02/19/2019
ms.topic: article
---
# OData lib changelog

## Change terminology

Breaking changes it the library fall into one of four categories:

- Improved Performance

We will get better writer performance across the board.

- Introducing Dependency Injection

This feature will substantially increase extensibility, like allowing customers to replace entire components such as the `UriPathParser` with their own implementation. Introducing DI make it much easier to use the same reader/writer settings across the board.

- Removed Legacy Code

There was a lot of vestigial code left around from the OData v1-3 days that we’ve removed.

- Improved API Design

Most of our API improvements fall into the category of namespace simplifications or updating verbiage. The single most impactful change that we made was deciding to merge entity type and complex type in ODataLib. We did this because complex type and entity type are becoming more and more similar in the protocol, but we continue to pay overhead to make things work for both of them.

RSS feed: Get notified when this page is updated by copying and pasting the following URL into your feed reader: [https://docs.microsoft.com/api/search/rss?search=%22What%27s+new+in+odata+lib&locale=en-us](https://docs.microsoft.com/api/search/rss?search=%22What%27s+new+in+odata+lib&locale=en-us)

## ODataLib 7.5.4 Release

> [!NOTE]
> ODataLib 7.5.4 includes the following new features, bug fixes and improvements:

***Features***

[#1376](https://github.com/OData/odata.net/pull/1376) Enable reading validation for derived type constraint annotation.

[#1285](https://github.com/OData/odata.net/issues/1285) Support customizing the built-in vocabulary models.

[#1404](https://github.com/OData/odata.net/pull/1404) Support reading/writing delta request payload.

***Fixed Bugs***

[#1368](https://github.com/OData/odata.net/issues/1368) Make aliases created in compute() transformation visible for following transforms/query options.

[#1373](https://github.com/OData/odata.net/issues/1373) **IN** operator not working with **null** value on nullable properties.

[#1385](https://github.com/OData/odata.net/issues/1385) & [#1164](https://github.com/OData/odata.net/issues/1164) Support parentheses and brackets in a CollectionConstantNode for **IN** operator.

[#1390](https://github.com/OData/odata.net/issues/1390) ODataUriParser can't parse for function with all omitted optional parameters.

[#1391](https://github.com/OData/odata.net/pull/1391) Fix build Uri problem with filter by Enum.

***Improvements***

[#1024](https://github.com/OData/odata.net/issues/1024) Improve the JSON reader buffer.

This release delivers OData core libraries including ODataLib, EdmLib, Spatial and Client.

## ODataLib 7.5.3 Release

> [!NOTE]
> ODataLib 7.5.3 includes the following new features, bug fixes and improvements:

***Features***

[#1295](https://github.com/OData/odata.net/pull/1295) Enable derived type validation in Uri parsing

[#1341](https://github.com/OData/odata.net/issues/1341) Support read/write Edm.PrimitiveType, Edm.ComplexType, Edm.EntityType

[#1344](https://github.com/OData/odata.net/issues/1344) Enable expand transformation in $apply

[#1346](https://github.com/OData/odata.net/pull/1346) Introduce new **ODataResourceValue** class and support read/write **ODataResourceValue**

[#1358](https://github.com/OData/odata.net/pull/1358) Enable derived type validation in writing

***Fixed Bugs***

[#1323](https://github.com/OData/odata.net/issues/1323) Fix the escaped single-quote and Guid literals for **IN** operator

[#1359](https://github.com/OData/odata.net/pull/1359) Fix SelectExpandNode for navigation properties on derived/complex types

***Improvements***

[#1349](https://github.com/OData/odata.net/issues/1349) Remove locks from Uri parser

[#1357](https://github.com/OData/odata.net/pull/1357) Use **TryParse** API instead of **Parse** for date to avoiding throwing exceptions

This release delivers OData core libraries including ODataLib, EdmLib, Spatial and Client.

## ODataLib 7.5.2 Release

> [!NOTE]
> ODataLib 7.5.2 includes the following new features, bug fixes and improvements:

***Features***

[#1272](https://github.com/OData/odata.net/issues/1272) Support $index query option

[#1275](https://github.com/OData/odata.net/issues/1275) Support $apply in $expand

[#855](https://github.com/OData/odata.net/issues/855) Support OptionalParameter as an external annotation

[#1262](https://github.com/OData/odata.net/issues/1262) OutOfLine Annotation for EnumMember throw exception when write to CSDL

[#1276](https://github.com/OData/odata.net/issues/1276) OData NuGet package description should change to reflect the latest status

[#1287](https://github.com/OData/odata.net/issues/1287) Add Validation vocabulary annotation

[#1110](https://github.com/OData/odata.net/pull/1110) Set-based operations

[#1293](https://github.com/OData/odata.net/pull/1293) Update the Core vocabulary xml

[#1286](https://github.com/OData/odata.net/pull/1286) Context URL for expand

[#1299](https://github.com/OData/odata.net/issues/1299) Enable write navigation property binding for containment target

[#1302](https://github.com/OData/odata.net/pull/1302) Update the DerivedTypeConstraint term in Validation vocabulary

[#1310](https://github.com/OData/odata.net/pull/1310) Add AllowedTerms/MinItems/MaxItems to Validation vocabulary

[#1308](https://github.com/OData/odata.net/pull/1308) Add LocalDateTime type definition into Core vocabulary

[#1312](https://github.com/OData/odata.net/issues/1312) Edm model validation for structural property type has wrong rule for property with type-definition type

[#1314](https://github.com/OData/odata.net/pull/1314) Update DerivedTypeConstraint appliesTo

[#1073](https://github.com/OData/odata.net/issues/1073) Better story for creating complex spatial types

[#1296](https://github.com/OData/odata.net/issues/1296) Remove Contentions from Microsoft.OData.Edm

[#1307](https://github.com/OData/odata.net/pull/1307) Add annotation term for Org.OData.Community.URLEscape.V1.URLEscapeFunction

This release delivers OData core libraries including ODataLib, EdmLib, Spatial and Client.

## ODataLib 7.5.1 Release

> [!NOTE]
> ODataLib 7.5.1 includes the following bug fixes:

***Fixed Bugs***

[#1181](https://github.com/OData/odata.net/issues/1181) Refactor the JSON string escape in JSON writer

[#1216](https://github.com/OData/odata.net/issues/1216) Read parameter should not throw exception if missing optional parameter

[#1220](https://github.com/OData/odata.net/issues/1220) Combined length of user strings exceeds allowed limit in OData V4 Code Generator

[#1226](https://github.com/OData/odata.net/issues/1226) Cache the Edm full name for the Edm types

[#1232](https://github.com/OData/odata.net/issues/1232) Fix lexer bug that breaks functions named 'in'

This release delivers OData core libraries including ODataLib, EdmLib, Spatial and Client.

## ODataLib 7.5.0 Release

> [!NOTE]
> ODataLib 7.5.0 includes the following items: IN operator, Entity set aggregation, various bug fixes, and performance improvement.

***Features***

[#757](https://github.com/OData/odata.net/pull/757) Entity set aggregations

[#1165](https://github.com/OData/odata.net/pull/1165) [Feature] IN operator

***Fixed Bugs***

[#513](https://github.com/OData/odata.net/issues/513) $select with complexCol/prop should be supported

[#985](https://github.com/OData/odata.net/issues/985) VS 2017 15.4.2 Broken client

[#1043](https://github.com/OData/odata.net/issues/1043) Thread safety of context item collections

[#1044](https://github.com/OData/odata.net/issues/1044) Code generation from T4 template - IgnoreUnexpectedElementsAndAttributes doesn't seem to work

[#1087](https://github.com/OData/odata.net/issues/1187) Batch is broken by load balancer (proxy)

[#1148](https://github.com/OData/odata.net/issues/1148) OData Code Generator does not install on VS2015

[#1123](https://github.com/OData/odata.net/pull/1123) Include dots in parseIdentifier for annotation parsing

[#1130](https://github.com/OData/odata.net/pull/1130) Add StringComparison in String.Equal() method call

[#1142](https://github.com/OData/odata.net/pull/1142) fix write document metadata properties for apply ComputeTransformationNode

[#1143](https://github.com/OData/odata.net/pull/1143) fix build Uri from groupby navigation property with many child structural properties

[#1145](https://github.com/OData/odata.net/issues/1145) Memory Leak in Library?

[#1151](https://github.com/OData/odata.net/pull/1151) Port client-side DateTime support fix to 7.x

[#1153](https://github.com/OData/odata.net/issues/1153) UriParser should throw exception for mismatched keys count when UnqualifiedODataUriResolver is configured

[#1155](https://github.com/OData/odata.net/issues/1155) FunctionCallBinder issue when case-insensitive is enabled

[#1157](https://github.com/OData/odata.net/issues/1157) ODataMessageReader doesn't honor the absence of ValidationKinds.ThrowIfTypeConflictsWithMetadata flag

[#1159](https://github.com/OData/odata.net/pull/1159) remove the Document element from Edm lib

[#1182](https://github.com/OData/odata.net/pull/1182) Fix a wrong if condition

[#1191](https://github.com/OData/odata.net/issues/1191) Avoid String allocations when writing strings that require special character handling

This release delivers OData core libraries including ODataLib, EdmLib, Spatial and Client.

## ODataLib 7.4.4 Release

> [!NOTE]
>
> ODataLib 7.4.4 fixes a potential concurrency issue with an internal dictionary used for tracking navigation property mappings and adds code to make sure that navigation property bindings are never written for containment navigation properties.

***Fixed Bugs***

[Issue #137](https://github.com/OData/odata.net/issues/1137) Possible contention issues in navigationPropertyMappings dictionary.
[Issue #138](https://github.com/OData/odata.net/issues/1138) Containment navigation properties shouldn't define NavigationPropertyBindings to non-containment sets

This release delivers OData core libraries including ODataLib, EdmLib, Spatial and Client.

## ODataLib 7.4.3 Release

> [!NOTE]
> ODataLib 7.4.3 fixes a minor bug introduced in 7.4.2 in which the path for a contained entity set was computed incorrectly.

***Fixed Bugs***

[Issue #1121](https://github.com/OData/odata.net/issues/1121) Incorrect path calculated for contained entity sets.

[Issue #1086](https://github.com/OData/odata.net/issues/1086) Enable writing type annotations for collections in full metadata.

This release delivers OData core libraries including ODataLib, EdmLib, Spatial and Client.

## ODataLib 7.4.2 Release

> [!NOTE]
> ODataLib 7.4.2 includes the following items: support for "Authorization" vocabularies to align with the Open API specification, enabling support for containment paths in navigation property bindings by addressing a bug, and various other fixes.

***Features***

[#1070](https://github.com/OData/odata.net/pull/1070) Add the Authorization vocabularies annotation into core Edm model

[#1109](https://github.com/OData/odata.net/pull/1109) Fix support for containment paths in nav prop bindings

[#1112](https://github.com/OData/odata.net/pull/1112) Bug fix: Throw exception for an invalid Enum value

***Fixed Bugs***

[Issue #645](https://github.com/OData/odata.net/issues/645) Enable updating top-level properties to null.

[Issue #1045](https://github.com/OData/odata.net/issues/1045) ODataUriExtensions.BuildUri ignores $apply

[Issue #1084](https://github.com/OData/odata.net/issues/1084) Updating package ID to match the existing VSIX ID in the marketplace

[Issue #1085](https://github.com/OData/odata.net/issues/1085) ExpressionLexer fix for parameter alias token in dotted expression

[Issue #1092](https://github.com/OData/odata.net/issues/1092) Address StyleCop warnings

This release delivers OData core libraries including ODataLib, EdmLib, Spatial and Client.

## ODataLib 7.4.1 Release

> [!NOTE]
> ODataLib 7.4.1 includes the following items: a new OData Client Code Gen extension for VS2017 using the latest version of the libraries, built-in abstract types for Edm models, KeyAsSegmentSupported Boolean to the capabilities vocabulary, added validation rules to abstract types, support for AnnotationSegment, NuGet package testing, and various bug fixes.

***Features***

[[#987](https://github.com/OData/odata.net/pull/987) Adding new OData Client Code Gen for VS2017

[[#1042](https://github.com/OData/odata.net/pull/1042) Remove the NavigationPropertyEntityMustNotIndirectlyContainItself rule

[[#1051](https://github.com/OData/odata.net/pull/1051) Add the build-in abstract type into Edm core model - Edm Type Part.

[[#1055](https://github.com/OData/odata.net/pull/1055) OptionalDollarSign: Small test update and expose API for DI option setter/getter

[[#1056](https://github.com/OData/odata.net/pull/1056) Add KeyAsSegmentSupported annotation term to Capabilities vocabulary

[[#1058](https://github.com/OData/odata.net/pull/1058) Add the validation rules to the abstract types

[[#1075](https://github.com/OData/odata.net/pull/1075) Add support for AnnotationSegment to PathSegmentHandler.

[[#1080](https://github.com/OData/odata.net/pull/1080) Add NuGet package testing.

***Fixed Bugs***

[Issue #530](https://github.com/OData/odata.net/issues/530) LINQ query generation with Date functions produces weird URLs

[Issue #1027](https://github.com/OData/odata.net/issues/1027) Edm.NavigationPropertyPath not supported

[Issue #1040](https://github.com/OData/odata.net/issues/1040) Need to update batch changeset ID to boundary value

[Issue #1046](https://github.com/OData/odata.net/issues/1046) OData Edm lib issue with vocabulary

This release delivers OData core libraries including ODataLib, EdmLib, Spatial and Client.

## ODataLib 7.4.0 Release

> [!NOTE]
> ODataLib 7.4.0 has the following beta releases:

### ODataLib 7.4.0.b

***Features***

[Issue #103](https://github.com/OData/odata.net/issues/103) OData v4: Deserialize client unknown properties into OData Object Model.

[Issue #801](https://github.com/OData/odata.net/issues/801) Support Primitive Type Casts.

[Issue #988](https://github.com/OData/odata.net/issues/988) Add support for 4.01 Delta Format

***Fixed Bugs***

[Issue #698](https://github.com/OData/odata.net/issues/698) DataServiceQuerySingle<T>.GetValueAsync inconsistent with GetValue in support for GET returning 404.

[Issue #800](https://github.com/OData/odata.net/issues/800) Need for parsing Open types using OData.NET v7.X.

[Issue #949](https://github.com/OData/odata.net/issues/949) ODataUriExtensions.BuildUri ignores $skiptoken.

[PR #961](https://github.com/OData/odata.net/pull/961) Add support for Enum keys.

[Issue #965](https://github.com/OData/odata.net/issues/965) Parsing encoded character with special meaning in ODataJsonLightContextUriParser.

### ODataLib 7.4.0.beta

***Features***

[Issue #226](https://github.com/OData/odata.net/issues/226) Support a JSON serialization for $batch.

[Issue #866](https://github.com/OData/odata.net/issues/866) "Microsoft.OData.Client" support for ,NET Core.

***Fixed Bugs***

[PR #980](https://github.com/OData/odata.net/pull/980) Support Enum to string comparison.

[PR #995](https://github.com/OData/odata.net/pull/995) Use ConcurrentDictionary in all platforms to make client EDM model thread safe.

[Issue #1008](https://github.com/OData/odata.net/issues/1008) & [Issue #1009](https://github.com/OData/odata.net/issues/1009) Fix property validation issue.

[Issue #1101](https://github.com/OData/odata.net/pull/1011) Microsoft.OData.Client 7.0+ needs to support GetValue like the older OData Client.

### ODataLib 7.4.0.be

***Features***

[PR #1020](https://github.com/OData/odata.net/pull/1020) DependsOn Ids for Multipart/Mixed Batch

***Fixed Bugs***

[Issue #1022](https://github.com/OData/odata.net/issues/1022) Calculate correct context URI with Operation path segment.

[Issue #1193](https://github.com/OData/WebApi/issues/1193) Add the extension function back to derived class.

[Issue #1028](https://github.com/OData/odata.net/issues/1028) Add the Enum member expression into validation and don't return the error message.

### ODataLib 7.4.0

***Features***

[Issue #1037](https://github.com/OData/odata.net/issues/1037) Support reading/writing OData 4.01 compatible JSON payloads.

This release delivers OData core libraries including ODataLib, EdmLib, Spatial and Client.

## ODataLib 7.3.1 Release

> [!NOTE]
> ODataLib 7.3.1 addresses an issue where $compute parsing was not triggered by a global call to ParseUri.

***Fixed Bugs***

[Issue #927](https://github.com/OData/odata.net/issues/927) Add Compute to ParseUri.

This release delivers OData core libraries including ODataLib, EdmLib and Spatial. OData Client for .NET is not published in this release.

## ODataLib 7.3.0 Release

> [!NOTE]
> ODataLib 7.3.0 adds support key features including optional function parameters, parser support for the $compute clause, primitive type casts, and the ability to read and write untyped data as structured values.

***Features***

[[#760](https://github.com/OData/odata.net/issues/760) Aggregation not supported for dynamic properties

[#782](https://github.com/OData/odata.net/issues/782) Add support for optional parameters in function

[#799](https://github.com/OData/odata.net/issues/799) Support for $compute in $select and $filter

[#800](https://github.com/OData/odata.net/issues/800) Support for structured reading/writing of untyped values

[#801](https://github.com/OData/odata.net/issues/801) Supporting Primitive Type Casts

***Fixed Bugs***

[Issue #747](https://github.com/OData/odata.net/issues/747) Cannot select dynamic property of a dynamic property

[Issue #814](https://github.com/OData/odata.net/issues/814) Support writing Enum-valued Annotations

[Issue #856](https://github.com/OData/odata.net/issues/856) Raw Value serializer output JSON string for Spatial type

This release delivers OData core libraries including ODataLib, EdmLib and Spatial. OData Client for .NET is not published in this release.

## ODataLib 7.2.0 Release

> [!NOTE]
> 7.2.0 re-introduces .NET Standard 1.1 libraries of ODataLib (OData.Core, OData.Edm, and Microsoft.Spatial). The PCL versions remain in the packages and are shipped alongside the new .NET Standard libraries. Bug fixes and additional test validations are also included in this release.

***Features***

[Commit 0b54111ee7909e71263b83fc60268de0de817986](https://github.com/OData/odata.net/commit/0b54111ee7909e71263b83fc60268de0de817986) Expose UriQueryExpressionParser.ParseFilter as a public API [#805](https://github.com/OData/odata.net/issues/805)

***Fixed Bugs***

[Issue #789](https://github.com/OData/odata.net/issues/789) BUG? Exception when create EdmModel V7.1.0

***Improvements***

[Commit 072f6f7c9bc4c739e553f7fa0996618c621a6589](https://github.com/OData/odata.net/commit/072f6f7c9bc4c739e553f7fa0996618c621a6589) Adding FxCop exclusion for CA3053:UseXmlSecureResolver in code that compiles under .Net portable framework.

[Commit 272c74afd1dda7a4a8e562c45dd9da9a9d74dd8b](https://github.com/OData/odata.net/commit/272c74afd1dda7a4a8e562c45dd9da9a9d74dd8b) Fix suppression for CA3053 to reference proper category and checkid.

[Commit 170827a01f9141649a848aefb13531163dedd65e](https://github.com/OData/odata.net/commit/170827a01f9141649a848aefb13531163dedd65e) This change fixes test failures in both local machine and in the lab

[Commit 66738741dbecad8e0dc32fa787a9f98898a2beda](https://github.com/OData/odata.net/commit/66738741dbecad8e0dc32fa787a9f98898a2beda) Adding .NET Core unit tests that integrate .NET Standard Libraries (#803)

This release delivers OData core libraries including ODataLib, EdmLib and Spatial. OData Client for .NET is not published in this release.

## ODataLib 7.1.1 Release

> [!NOTE]
> 7.1.1 is a re-release of 7.1.0 without the NetStandard version of ODataLib, EdmLib and Spatial due to an issue found in the NetStandard versions of those  binaries. The PCL versions of those binaries are unaffected.
> This release delivers OData core libraries including ODataLib, EdmLib and Spatial. OData Client for .NET is not published in this release.

## ODataLib 7.1.0 Release

### Migration to .NET Standard 1.1

[Commit a2af8c6c19f104f46b442df7f57f624ed77d82fc](https://github.com/OData/odata.net/commit/a2af8c6c19f104f46b442df7f57f624ed77d82fc) Update profile111 to .netstandard1.1.

[Commit 6f746cd0cd9d62a5bc57e345a910a6f9d8d4dc1b](https://github.com/OData/odata.net/commit/6f746cd0cd9d62a5bc57e345a910a6f9d8d4dc1b) Adding new projects and solution for .NET Standard version of ODL.

Creating a new solution file for .NET Standard projects. Adding .NET
Standard versions of Microsoft.Spatial, Microsoft.OData.Edm,
Microsoft.OData.Core, and Microsoft.OData.Client. MSBuild doesn't
pre-process the dependency graph provided by the csproj files, so
explicitly spoon feeding the chain in the Microsoft.Test.OData.DotNetStandard.sln
file.

Update build script to default to VS 2015 for .NET Standard,
removing NuGetPackage test project and deprecating
it, adding E2E .NET Standard project

Remove duplicate file: IEdmReferentialConstraint.cs.

Change version to 7.1.0

[!Note]: "nuget restore" needs to be run manually on the new project
files for them to compile.

***Features***

[Commit c0c6006a5a8683507c38623144a145de128851c6](https://github.com/OData/odata.net/commit/c0c6006a5a8683507c38623144a145de128851c6) Adding support and tests for virtual property count.

[Commit d064a1c6358130c581bb9d5bb32e774f8e921992](https://github.com/OData/odata.net/commit/d064a1c6358130c581bb9d5bb32e774f8e921992) Adding support and tests for custom aggregation methods.

[Commit 8e965e9f89951dbfea510e67cbbe89e4f75fa69b](https://github.com/OData/odata.net/commit/8e965e9f89951dbfea510e67cbbe89e4f75fa69b) Add support for operations with no bindings.

***Fixed Bugs***

[Issue #525](https://github.com/OData/odata.net/issues/525) Support for AnnotationPath.

[Issue #526](https://github.com/OData/odata.net/issues/526) Support for IncludeInServiceDocument.

[Issue #680](https://github.com/OData/odata.net/issues/680) Text "Date" will be parsed as "EDM.Date" type segment in URL parser.

[Issue #687](https://github.com/OData/odata.net/issues/687) Null exception when HttpHeaderValueElement.Value is not set.

[Issue #706](https://github.com/OData/odata.net/issues/706) Serialization exception when 2 subtypes define a property with the same name but different types.

[Issue #758](https://github.com/OData/odata.net/issues/758) countdistinct and $count are returning Edm.Int64 which is not spec compliant.

[Issue #776](https://github.com/OData/odata.net/issues/776) Fix ContextUrl generation for operations in path.

[Issue #777](https://github.com/OData/odata.net/issues/777) Fix trailing whitespace on empty line.

[Issue #778](https://github.com/OData/odata.net/issues/778) Fix tests and code for reading nested results from operations.

***Improvements***

[Commit 6e2dee52b37e620926cd0535f40d5537ba839c05](https://github.com/OData/odata.net/commit/6e2dee52b37e620926cd0535f40d5537ba839c05) Add Test solutions for WP WindowsStore Portable.

[Commit d695d6ded6d44fa3fdb7abbd5f8dc19c29330e10](https://github.com/OData/odata.net/commit/d695d6ded6d44fa3fdb7abbd5f8dc19c29330e10) Update license.

[Commit 8521d38405351f789134d8945a696a18eec929d3](https://github.com/OData/odata.net/commit/8521d38405351f789134d8945a696a18eec929d3) Fix Phone Project.

[Commit dc9632b686e47dba8a0bbeffb5cc8c5850e27c8b](https://github.com/OData/odata.net/commit/dc9632b686e47dba8a0bbeffb5cc8c5850e27c8b) Fix fxcop issue.

[Commit 3ea2d70d14c97344f43383d867a9edd81eb407a3](https://github.com/OData/odata.net/commit/3ea2d70d14c97344f43383d867a9edd81eb407a3) Add test cases for DotNetCore.

[Commit 2ef3fc921ad4b557ef67cd17ce95eb08b33fa14e](https://github.com/OData/odata.net/commit/2ef3fc921ad4b557ef67cd17ce95eb08b33fa14e) Update nuget.exe to 3.5.0 to resolve build issue with xunit.

[Commit 0dc70678f05201ebc48c83219f6b4450078d0693](https://github.com/OData/odata.net/commit/0dc70678f05201ebc48c83219f6b4450078d0693) Reordering comments to avoid compiler warnings.

[Commit bbd9ede1e73de3538af6f1a223bcc7fe689688f4](https://github.com/OData/odata.net/commit/bbd9ede1e73de3538af6f1a223bcc7fe689688f4) Update test project to copy the new and old version of the EntityFramework.nuspec file.

[Commit 6eb8a4b5ecff1d83d883a95162d187ee3a44f935](https://github.com/OData/odata.net/commit/6eb8a4b5ecff1d83d883a95162d187ee3a44f935) Remove use of StringBuilder.Clear() for .NET 3.5 support.

[Commit c1edd714bf58ddd2876e10d31f0bd9ad81e25664](https://github.com/OData/odata.net/commit/c1edd714bf58ddd2876e10d31f0bd9ad81e25664) Added metadata to tests for new bound function.

> [!NOTE]
> This release delivers OData core libraries including ODataLib, EdmLib and Spatial. OData Client for .NET is not published in this release.

## ODataLib 7.0 Release

***Features***

[Issue #245](https://github.com/OData/odata.net/issues/245) Support duplicate non-OData query options.

[Issue #248](https://github.com/OData/odata.net/issues/248) Support untyped JSON.

- This feature will allow customers to extend their OData payloads with arbitrary JSON. In the extreme, it’s theoretically possible for the whole response to be untyped.

[Issue #271](https://github.com/OData/odata.net/issues/271) Support writing relative URIs in OData batch operations.

- Add an Enum type `BatchPayloadUriOption` to support three URI formats in batch payload.

[Issue #366](https://github.com/OData/odata.net/issues/366) Support collection of mixed primitive types and Edm.Untyped.

[Issue #501](https://github.com/OData/odata.net/issues/501) Integrate dependency injection (DI).

- We introduced an abstraction layer consisting of two interfaces `IContainerBuilder` and `IContainerProvider` so that ODataLib is decoupled from any concrete implementation of DI framework.
Now we support the following services to be replaced by users:

- JSON reader via `IJsonReaderFactory`
- JSON writer via `IJsonWriterFactory`
- `ODataMediaTypeResolver`
- `ODataPayloadValueConverter`
- `ODataUriResolver`
- `UriPathParser`

We also support prototype services. For each prototype service, you can specify a globally singleton prototype instance. Then for each request, a cloned instance will be created from that prototype which can isolate the modification within the request boundary. Currently we support three prototype services:

- `ODataMessageReaderSettings`
- `ODataMessageWriterSettings`
- `ODataSimplifiedOptions`

[Issue #502](https://github.com/OData/odata.net/issues/502) [Support URI path syntax customization](../odata features/2016-08-23-11-08-uri-parser-path-extensibility).

- Expose `UriPathParser.ParsePathIntoSegments` to support customizing how to separate a Uri into segments
- Provide`ParseDynamicPathSegmentFunc` for customizing how to parse a dynamic path segment.

[Issue #613](https://github.com/OData/odata.net/issues/613) Support type facets when referencing `TypeDefinition` types.

[Issue #622](https://github.com/OData/odata.net/issues/622) [Support navigation property on complex types.](/odata/odatalib/navigation-under-complex)

- EdmLib supports adding navigation property on complex type in model.
- ODataUriParser support parsing related Uri path or query expressions.
- ODataLib support reading and writing navigation properties on complex type.

[Issue #629](https://github.com/OData/odata.net/issues/629) [Support multi-NavigationPropertyBindings for a single navigation property by using different paths](https://odata.github.io/odata.net/v7/#06-21-Multi-Binding)

- Navigation property used in multi bindings with different path is supported for navigation under containment and complex.

[Issue #631](https://github.com/OData/odata.net/issues/631) Support [fluent writer API](https://odata.github.io/odata.net/v7/#01-02-fluent-writer-api).

- In previous version, paired `WriteStart` and `WriteEnd` calls are used in writing payloads. This syntax is error-prone, and soon gets unmanageable with complex and deeply nested payloads. In this new release, you can instead write payloads using the neat fluent syntax.

[Issue #635](https://github.com/OData/odata.net/issues/635) Support collection of nullable values for dynamic properties.

[Issue #637](https://github.com/OData/odata.net/issues/637) Support system query options without $ prefix.

- Expose `ODataUriParser.EnableNoDollarQueryOptions` flag to enable user to support '$' system query prefix optional.

Add `ODataSimplifiedOptions` class for simplified reader, writer, URL parsing options.

Support duplicate custom instance annotations.

***Fixed Bugs***

[Issue #104](https://github.com/OData/odata.net/issues/104) Function imports with parameters are included in service document.

[Issue #498](https://github.com/OData/odata.net/issues/498) Typos in namespace/class names.

[Issue #508](https://github.com/OData/odata.net/issues/508) YYYY-MM-DD in URI should be parsed into `Date`, not `DataTimeOffset`.

[Issue #556](https://github.com/OData/odata.net/issues/556) URI template parser doesn't work correctly if key is of an Enum type.

[Issue #573](https://github.com/OData/odata.net/issues/573) `CollectionCount` is not publicly accessible.

[Issue #592](https://github.com/OData/odata.net/issues/592) `ODataUriParser.ParsePath()` doesn’t work correctly if `EntitySetPath` is not specified in model.

[Issue #628](https://github.com/OData/odata.net/issues/628) Dynamic complex (collection) property is annotated with association and navigation links.

[Issue #638](https://github.com/OData/odata.net/issues/638) Null value at the first position in a complex collection cannot be read.

[Issue #658](https://github.com/OData/odata.net/issues/658) `ODataValueUtils.ToODataValue` doesn’t work with `System.Enum` objects.

### Legacy Code Clean-up

[Issue #385](https://github.com/OData/odata.net/issues/385) Remove junk code to improve stability and reduce assembly size.

- Deprecated build constants
- Code in ODataLib that has duplication in EdmLib
- Platform-dependent code (e.g., redefinition of TypeCode and BindingFlags, Silverlight code, etc.)
- Deprecated classes like `EntitySetNode`

[Issue #500](https://github.com/OData/odata.net/issues/500) Remove deprecated NuGet profiles

- Removed support for:
  - Desktop and Profile328 for .NET 4.0
  - Profile259 for .NET 4.5
  - dnxcore50 and dnx451 for ASP.NET 5.0 (deprecated)
- What we have now:
  - Profile111 for .NET 4.5 which is compatible with most .NET 4.5+ applications, UWP, Xamarin/Mono, etc.
  - .NET 3.5 for Office (will not be publicly available)

[Issue #510](https://github.com/OData/odata.net/issues/510) Remove Atom support

[Issue #511](https://github.com/OData/odata.net/issues/511) Clean property and method in `ODataMessageReaderSettings` and `ODataMessageWriterSettings`

- Remove API to enable default, service and client settings
- Move `ODataSimplified` and `UseKeyAsSegment` to `ODataSimplifiedOptions`
- Rename DisableXXX to EnableXXX
- Remove base class of reader and writer settings

[Issue #548](https://github.com/OData/odata.net/issues/548) Rename "value term" and "value annotation" in EdmLib.

- "Value term" and "value annotation" are concepts of ODataV3, In ODataV4 we remove/rename obsolete interfaces and merge some interfaces with their base interfaces so as to make APIs clearer and more compact.

[Issue #565](https://github.com/OData/odata.net/issues/565) Update CoreVocabularies.xml to the new version and related APIs.

[Issue #564](https://github.com/OData/odata.net/issues/564) Remove `Edm.ConcurrencyMode` attribute from Property.

[Issue #606](https://github.com/OData/odata.net/issues/606) Remove V3 vocabulary expressions

- Interfaces removed: `IEdmEnumMemberReferenceExpression`, `IEdmEnumMemberReferenceExpression`, `IEdmEntitySetReferenceExpression`, `IEdmPropertyReferenceExpression`, `IEdmParameterReferenceExpression` and `IEdmOperationReferenceExpression`
- For the previous `IEdmEntitySetReferenceExpression`, please use `IEdmPathExpression` where users need to provide a path (a list of strings) to a navigation property with which we can resolve the target entity set from the navigation source.
- For the previous `IEdmOperationReferenceExpression`, please use `IEdmFunction` because the `Edm.Apply` only accepts an EDM function in OData V4. This also simplifies the structure of `Edm.Apply` expression.

[Issue #618](https://github.com/OData/odata.net/issues/618) Remove deprecated validation rules

- `ComplexTypeInvalidAbstractComplexType`
- `ComplexTypeInvalidPolymorphicComplexType`
- `ComplexTypeMustContainProperties`
- `OnlyEntityTypesCanBeOpen`

### Public API Simplification

[Issue #504](https://github.com/OData/odata.net/issues/504) [Unify entity and complex (collection) type serialization/deserialization API](../odata features/2016-08-23-10-42-merge-complex-and-entity).

- Merge the reading/writing behavior for complex and entity, collection of complex and collection of entity.
  - Change `ODataEntry` to `ODataResource` and remove `ODataComplexValue` to support complex and entity.
  - Change `ODataFeed` to `ODataResourceSet` to support feed and complex collection.
  - Change `ODataNavigationLink` to `ODataNestedResourceInfo` to support navigation property and complex property or complex collection property.
  - Change reader/writer APIs to support `IEdmStructuredType`.
- We don’t merge entity type/complex type in EdmLib since they are OData concepts.

[Issue #491](https://github.com/OData/odata.net/issues/491) Simplified namespaces.

[Issue #517](https://github.com/OData/odata.net/issues/517) Centralized reader/writer validation. [Breaking Changes](https://odata.github.io/odata.net/v7/#04-02-Validations-Breaking)

- Add an Enum `ValidationKinds` to represent all validation kinds in reader and writer.
- Add Validations property in `ODataMessageWriterSettings`/`ODataMessageReaderSettings` to control validations.
- Remove some APIs.

[Issue #571](https://github.com/OData/odata.net/issues/571) Rename `ODataUrlConvention` to `ODataUrlKeyDelimiter`

- Rename `ODataUrlConvention` to `ODataUrlKeyDelimiter`.
- Use `ODataUrlKeyDelimiter.Slash` instead of `ODataUrlConvention.Simplified` or `ODataUrlConvention.KeyAsSegment`
- Use `ODataUrlKeyDelimiter.Parentheses` instead of `ODataUrlConvention.Default`

[Issue #614](https://github.com/OData/odata.net/issues/614) Improve API design around `ODataAnnotatable`

- Merge `SerializationTypeNameAnnotation` into `ODataTypeAnnotation`
- Refactor `ODataTypeAnnotation` to contain only the type name
- Remove unnecessary inheritance from `ODataAnnotatable` in URI parser and simplify the API of `ODataAnnotatble`
- `GetAnnotation<T>()` and `SetAnnotation<T>()` will no longer be available because `ODataAnnotatable` should only be responsible for managing instance annotations.

### Public API Enhancement

[Issue #484](https://github.com/OData/odata.net/issues/484) Preference header extensibility

- Public class `HttpHeaderValueElement`, which represents http header value element
- Remove sealed from public class `ODataPreferenceHeader`

[Issue #544](https://github.com/OData/odata.net/issues/544) Change Enum member value type from `IEdmPrimitiveValue` to a more specific type.

- Add interface `IEdmEnumMemberValue` and class `EdmEnumMemberValue` to represent Enum member value specifically. `AddMember()` under `EnumType` now accepts `IEdmEnumMemberValue` instead of `IEdmPrimitiveValue` as member value.

[Issue #621](https://github.com/OData/odata.net/issues/621) Make reader able to read contained entity/entityset without context URL.

[Issue #640](https://github.com/OData/odata.net/issues/640) More sensible type, namely `IEnumerable<object>`, for ODataCollectionValue.Items.

[Issue #643](https://github.com/OData/odata.net/issues/643) Adjust query node kinds in Uri Parser in order to support navigation under complex. [Breaking Changes](https://odata.github.io/odata.net/v7/#04-01-Query-Nodes-Breaking)

Improved standard-compliance by forbidding duplicate property names.

Writer throws more accurate and descriptive exceptions.

***Improvements***

[Issue #493](https://github.com/OData/odata.net/issues/493) Replace `ThrowOnUndeclaredProperty` with the more accurate `ThrowOnUndeclaredPropertyForNonOpenType`.

[Issue #551](https://github.com/OData/odata.net/issues/551) Change the type of `EdmReference.Uri` to `System.Uri`.

[Issue #558](https://github.com/OData/odata.net/issues/558),
[Issue #611](https://github.com/OData/odata.net/issues/611) Improve writer performance. Up to 25% improvements compared to ODL 6.15 are achieved depending on scenario.

[Issue #632](https://github.com/OData/odata.net/issues/632) Rename CsdlXXX to SchemaXXX, and EdmxXXX to CsdlXXX.

The original naming is confusing. According to the [CSDL spec](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part3-csdl/odata-v4.0-errata02-os-part3-csdl-complete.html#_Toc406397921):

> An XML document using these namespaces and having an edmx:Edmx root element will be called a CSDL document.

So, Edmx is only part of a valid CSDL document. In previous version, `CsdlReader` is actually unable to read a CSDL document. It's only able to read the Schema part of it. `EdmxReader`, on the other hand, is able to read a whole CSDL document. To clear up the concepts, the following renaming has been done:

1. `CsdlReader/Writer` to `SchemaReader/Writer`;
2. `EdmxReader/Writer` to `CsdlReader/Writer`;
3. `EdmxReaderSettings` to `CsdlReaderSettings`;
4. `EdmxTarget` to `CsdlTarget`.

> [!NOTE]
> This release delivers OData core libraries including ODataLib, EdmLib and Spatial. OData Client for .NET is not  published in this release.
