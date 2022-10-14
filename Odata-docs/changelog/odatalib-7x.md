---
title: "ODataLib changelog"
description: "ODataLib 7.x"
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
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

## ODataLib 7.12.3 Release

***Fixed Bugs***

[[#2487]](https://github.com/OData/odata.net/pull/2487) Fixed bug in `LoadProperty` for complex properties and complex collection properties

[[#2497]](https://github.com/OData/odata.net/pull/2497) Fixed bug causing JSON batch responses in WebAPI to be written out of order when using `DefaultStreamBasedJsonWriterFactory`.

***Improvements***

[[#2489]](https://github.com/OData/odata.net/pull/2489) Perf improvement in the internal `ODataPath` cloning constructor.

[[#2506]](https://github.com/OData/odata.net/pull/2506) Reduce memory usage in OData Client by eliminating static `ConditionalWeakTable`.

## ODataLib 7.12.2 Release

***Features***

[[#2466]](https://github.com/OData/odata.net/pull/2466) Implement `ODataUtf8JsonWriter` async API

[[#2157]](https://github.com/OData/odata.net/pull/2157) Add extension method to get all navigation property bindings: `GetNavigationPropertyBindings(this IEdmEntityContainer container)`

[[#2470]](https://github.com/OData/odata.net/pull/2470) Add support for `Core.AlternateKeys`

## ODataLib 7.12.1 Release

***Features***

[[2425]](https://github.com/OData/odata.net/pull/2425) Make `ODataPathExtensions` public

[[2416]](https://github.com/OData/odata.net/pull/2416) Create `Utf8JsonWriter`-based implementation of `IJsonWriter` and introduced `IStreamBasedJsonWriterFactory` interfaces

[[1925]](https://github.com/OData/odata.net/pull/1925) Add support for `$apply/groupby` in OData Client

***Fixed Bugs***

[[2461]](https://github.com/OData/odata.net/pull/2461) Fix `DisposeAsync` implementation for .NET Core 3.1 and higher

[[2459]](https://github.com/OData/odata.net/pull/2459) Fix issues causing synchronous I/O to occur in asynchronous code. This was in turn causing exceptions when using Kestrel web server which by default does not allow synchronous I/O.

## ODataLib 7.12.0 Release

***Features***

[[#2431]](https://github.com/OData/odata.net/pull/2431) Allow user to provide HttpClientHandler instance in OData Client

***Fixed Bugs***

N/A

***Improvements***

N/A

## ODataLib 7.11.1 Release

***Features***

N/A

***Fixed Bugs***

N/A

***Improvements***

[[#2407]](https://github.com/OData/odata.net/pull/2407) Encode special chars in array elements in Contains expression

## ODataLib 7.11.0 Release

***Features***

[[#2267]](https://github.com/OData/odata.net/pull/2267) Implement asynchronous support in ODataJsonLightDeserializer, ODataJsonLightPropertyAndValueDeserializer and ODataJsonLightResourceDeserializer 

[[#2301]](https://github.com/OData/odata.net/pull/2301) Implement asynchronous support in ODataJsonLightEntityReferenceLinkDeserializer  

[[#2305]](https://github.com/OData/odata.net/pull/2305) Implement asynchronous support in ODataJsonLightErrorDeserializer 

[[#2306]](https://github.com/OData/odata.net/pull/2306) Implement asynchronous support ODataJsonLightCollectionDeserializer 

[[#2308]](https://github.com/OData/odata.net/pull/2308) Implement asynchronous support in ODataJsonLightPayloadKindDetectionDeserializer 

[[#2311]](https://github.com/OData/odata.net/pull/2311) Implement asynchronous support in ODataJsonLightParameterDeserializer 

[[#2316]](https://github.com/OData/odata.net/pull/2316) Implement asynchronous support in ReorderingJsonReader 

[[#2322]](https://github.com/OData/odata.net/pull/2322) Implement asynchronous support in ODataJsonLightServiceDocumentDeserializer 

[[#2324]](https://github.com/OData/odata.net/pull/2324) Implement asynchronous support in ODataNotificationReader 

[[#2327]](https://github.com/OData/odata.net/pull/2327) Implement asynchronous support in ODataJsonLightCollectionReader 

[[#2335]](https://github.com/OData/odata.net/pull/2335) Implement asynchronous support in ODataJsonLightReader 

[[#2351]](https://github.com/OData/odata.net/pull/2351) Implement asynchronous support in ODataJsonLightDeltaReader 

[[#2353]](https://github.com/OData/odata.net/pull/2353) Implement asynchronous support in ODataJsonLightParameterReader 

[[#2359]](https://github.com/OData/odata.net/pull/2359) Implement asynchronous support in ODataJsonLightBatchReader 

[[#2364]](https://github.com/OData/odata.net/pull/2364) Implement asynchronous support in ODataRawInputContext 

***Fixed Bugs***

[[#2299]](https://github.com/OData/odata.net/pull/2299) fix filter using in operator with open types 

[[#2300]](https://github.com/OData/odata.net/pull/2300) Fix cast in expand 

[[#2313]](https://github.com/OData/odata.net/pull/2313) #1208 Support relative context URI starting with "$metadata" 

[[#2341]](https://github.com/OData/odata.net/pull/2341) Validate key not defined multiple times in type hierarchy 

[[#2346]](https://github.com/OData/odata.net/pull/2346) Set default scale to null 

***Improvements***

[[#2222]](https://github.com/OData/odata.net/pull/2222) Replaced the AsyncBufferedStream with a simple BufferedStream  

[[#2303]](https://github.com/OData/odata.net/pull/2303) Bump System.Text.RegularExpressions

[[#2304]](https://github.com/OData/odata.net/pull/2304) Bump System.Net.Http 

[[#2328]](https://github.com/OData/odata.net/pull/2328) Add DuplicatePropertyNameChecker to ObjectPool 

[[#2363]](https://github.com/OData/odata.net/pull/2363) Avoid zero-length array allocations 

[[#2366]](https://github.com/OData/odata.net/pull/2366) ODataJsonLightCollectionReader improvements to reduce delegate allocations 

[[#2367]](https://github.com/OData/odata.net/pull/2367) Fix unnecessary delegate allocations across OData writers 

## ODataLib 7.10.0 Release

***Features***

[[#2248]](https://github.com/OData/odata.net/pull/2248) Implement asynchronous support in JsonReaderExtensions

[[#2255]](https://github.com/OData/odata.net/pull/2255) Implement asynchronous support in ODataJsonReaderCoreUtils

[[#2264]](https://github.com/OData/odata.net/pull/2264) Implement asynchronous support in BufferingJsonReader

***Fixed Bugs***

[[#2191]](https://github.com/OData/odata.net/pull/2191) Compute navigation path should return null if navigation source kind is none in context uri info

[[#2216]](https://github.com/OData/odata.net/pull/2216) Relative URIs for @oData.context are not supported

[[#2231]](https://github.com/OData/odata.net/pull/2231) Remove single quotes from the type in a cast function

[[#2250]](https://github.com/OData/odata.net/pull/2250) Support writing nested delta resource sets with non-delta writer

[[#2257]](https://github.com/OData/odata.net/pull/2257) FunctionCallBinder promotes arguments without checking return type

[[#2275]](https://github.com/OData/odata.net/pull/2275) Names starting with underscore must be allowed

[[#2277]](https://github.com/OData/odata.net/pull/2277) Should use for @type for serializing record in CSDL JSON

[[#2279]](https://github.com/OData/odata.net/pull/2279) Default to using entityType for incomplete serializationInfo

***Improvements***

[[#2204]](https://github.com/OData/odata.net/pull/2204) Perf: Optimize collections and enumerators

[[#2215]](https://github.com/OData/odata.net/pull/2215) Performance improvements

[[#2247]](https://github.com/OData/odata.net/pull/2247) Fix memory leak

[[#2256]](https://github.com/OData/odata.net/pull/2256) Refactor alternate key resolver to avoid relying on exceptions

[[#2288]](https://github.com/OData/odata.net/pull/2288) Cache Parent Scope

[[7dcad7]](https://github.com/OData/odata.net/commit/7dcad74478debcfe54e93c63f47b7cb57f2d2e67) Add publicapi analyzer, clean up the warnings

## ODataLib 7.9.4 Release

***Features***

[[#2159]](https://github.com/OData/odata.net/pull/2159) Update namespaces to use aliases if aliases for the namespaces are set

[[#2173]](https://github.com/OData/odata.net/pull/2173) EdmLib vocabulary annotation default values

[[#2199]](https://github.com/OData/odata.net/pull/2199) Implement asynchronous support in ODataTextStreamReader class

[[#2202]](https://github.com/OData/odata.net/pull/2202) Enable default value for vocabulary annotation

[[#2212]](https://github.com/OData/odata.net/pull/2212) Implement asynchronous support in ODataBinaryStreamReader

[[#2217]](https://github.com/OData/odata.net/pull/2217) Implement asynchronous support in JsonReader

[[#2232]](https://github.com/OData/odata.net/pull/2232) Enable property case insensitive reading

***Fixed Bugs***

[[#2239]](https://github.com/OData/odata.net/pull/2239) Add direct reference to Microsoft.Bcl.AsyncInterfaces package to fix missing reference issue observed in dependent libraries

***Improvements***

[[#2223]](https://github.com/OData/odata.net/pull/2223) Performance fix for FindAcrossModels

## ODataLib 7.9.3 Release

**[Be NOTED]**
To support gradual introduction of asynchronous cleanup of managed resources, a dependency on Microsoft.Bcl.Interfaces was added. If you update the OData core libraries packages in your project to 7.9.1 or higher, please add Microsoft.Bcl.Interfaces package as well.

***Features***

N/A

***Fixed Bugs***

[[#2130]](https://github.com/OData/odata.net/pull/2130) Fix NullReferenceException thrown during materialization of lower-camel-cased payload

[[#2160]](https://github.com/OData/odata.net/pull/2160) Expand fully qualified type

[[#2179]](https://github.com/OData/odata.net/pull/2179) Fix the issue when read nested resource untyped in the request

***Improvements***

[[#2178]](https://github.com/OData/odata.net/pull/2178) Clone reader settings when creating it

[[#2188]](https://github.com/OData/odata.net/pull/2188) Avoid creating unused instances of ParamaterAliasValueAccessor

[[#2193]](https://github.com/OData/odata.net/pull/2193) Remove delegate allocation in ODataWriterCore CheckForNestedResourceInfoWithContent method

[[#2197]](https://github.com/OData/odata.net/pull/2197) Reduce memory allocations from closures and lambdas

[[#2209]](https://github.com/OData/odata.net/pull/2209) Provide option for alternative form of delete link request Uri

[[#2210]](https://github.com/OData/odata.net/pull/2210) Cache vocabulary annotations if model is immutable

## ODataLib 7.9.2 Release

**[Be NOTED]**
To support gradual introduction of asynchronous cleanup of managed resources, a dependency on Microsoft.Bcl.Interfaces was added. If you update the OData core libraries packages in your project to 7.9.1 or higher, please add Microsoft.Bcl.Interfaces package as well.

***Features***

N/A

***Fixed Bugs***

[[#2188]](https://github.com/OData/odata.net/pull/2188) Avoid creating unused objects when calling ODataUri.Clone()

[[#2179]](https://github.com/OData/odata.net/pull/2179) Fix the issue when read nested resource untyped in the request

[[#2178]](https://github.com/OData/odata.net/pull/2178) Clone reader settings when creating it

[[#2130]](https://github.com/OData/odata.net/pull/2130) Fix NullReferenceException thrown during materialization of lower-camel-cased payload

***Improvements***

N/A

## ODataLib 7.9.1 Release

**[Be NOTED]**
To support gradual introduction of asynchronous cleanup of managed resources, a dependency on Microsoft.Bcl.Interfaces was added. If you update the OData core libraries packages in your project to 7.9.1 or higher, please add Microsoft.Bcl.Interfaces package as well.

***Features***

[[#2041]](https://github.com/OData/odata.net/pull/2041) Remember the SelectItem instances that are thrown away because of the existence of a WildcardSelectItem 

[[#2059]](https://github.com/OData/odata.net/pull/2059) Implement asynchronous support in ODataJsonLightCollectionWriter

[[#2078]](https://github.com/OData/odata.net/pull/2078) Implement asynchronous support in ODataJsonLightWriter

[[#2079]](https://github.com/OData/odata.net/pull/2079) Support asynchronous invocation of IODataStreamListener.StreamDisposedAsync method from ODataNotificationWriter class

[[#2080]](https://github.com/OData/odata.net/pull/2080) Support asynchronous invocation of IODataStreamListener.StreamDisposedAsync method from ODataNotificationStream 

[[#2082]](https://github.com/OData/odata.net/pull/2082) Implement asynchronous support in ODataJsonLightDeltaWriter

[[#2096]](https://github.com/OData/odata.net/pull/2096) Implement asynchronous support for ODataJsonLightParameterWriter

[[#2109]](https://github.com/OData/odata.net/pull/2109) Implement asynchronous support in ODataWriteStream

[[#2111]](https://github.com/OData/odata.net/pull/2111) Implement asynchronous support in ODataJsonLightBatchWriter

[[#2114]](https://github.com/OData/odata.net/pull/2114) Implement asynchronous support in ODataJsonLightOutputContext

[[#2121]](https://github.com/OData/odata.net/pull/2121) Implement asynchronous support in RawValueWriter

[[#2123]](https://github.com/OData/odata.net/pull/2123) Fixed issue with caching of enumerable instead of list

[[#2124]](https://github.com/OData/odata.net/pull/2124) Implement asynchronous support in ODataAsynchronousWriter

[[#2125]](https://github.com/OData/odata.net/pull/2125) Implement asynchronous support in ODataRawOutputContext

[[#2126]](https://github.com/OData/odata.net/pull/2126) Implement asynchronous support in ODataMultipartMixedBatchWriter

[[#2143]](https://github.com/OData/odata.net/pull/2143) Support empty collection with IN operator

***Fixed Bugs***

[[#2021]](https://github.com/OData/odata.net/pull/2021) HttpClientRequestMessage: Fix UnwrapAggregateException

[[#2072]](https://github.com/OData/odata.net/pull/2072) No error message when reading an incorrect structure of a collection of entities

[[#2090]](https://github.com/OData/odata.net/pull/2090) Entity tracker equality comparer

[[#2097]](https://github.com/OData/odata.net/pull/2097) Add cancellation token to ExecuteAsync in DataServiceActionQuery and DataServiceActionQueryofT

[[#2110]](https://github.com/OData/odata.net/pull/2110) ODataNotificationWriter and ODataNotificationStream wrapper classes should not dispose the passed TextWriter and Stream respectively

[[#2131]](https://github.com/OData/odata.net/pull/2131) Fix issue with async reader for Parsing the Context Uri

[[#2139]](https://github.com/OData/odata.net/pull/2139) Fix escape function binding type constraint

[[#2145]](https://github.com/OData/odata.net/pull/2145) Allow filter in empty string

[[#2155]](https://github.com/OData/odata.net/pull/2155) Fix AggregateException thrown by OData writers async API

***Improvements***

[[#2091]](https://github.com/OData/odata.net/pull/2091) Setup crank for performance tests

[[#2093]](https://github.com/OData/odata.net/pull/2093) Consistently use CharArrayPool in JsonWriter to cut cost of initializing char arrays

[[#2133]](https://github.com/OData/odata.net/pull/2133) Cache annotation full qualified name in FindDeclaredVocabularyAnnotations

[[#2144]](https://github.com/OData/odata.net/pull/2144) Skip string allocation for keys of PropertyCache

[[#2154]](https://github.com/OData/odata.net/pull/2154) Skip lambda allocation in FindTransientAnnotation

[[#2156]](https://github.com/OData/odata.net/pull/2156) Fix string allocation performance issue for IsQualifiedName

[[#2158]](https://github.com/OData/odata.net/pull/2158) Avoid delegate allocations calling InterceptException

## ODataLib 7.9.0 Release

***Features***

[[#1806]](https://github.com/OData/odata.net/pull/1806) Sequencing Json Batch requests with the dependsOn property in OData Client

[[#1998]](https://github.com/OData/odata.net/pull/1998) Add support for $this

[[#2002]](https://github.com/OData/odata.net/pull/2002) Expand TextWriterWrapper abstract class with equivalent async method

[[#2009]](https://github.com/OData/odata.net/pull/2009) Enable EdmReference, EdmInclude vocabulary annotation

[[#2014]](https://github.com/OData/odata.net/pull/2014) Implement asynchronous support in JsonWriter

[[#2030]](https://github.com/OData/odata.net/pull/2030) Implement asynchronous support in ODataJsonLightSerializer

[[#2031]](https://github.com/OData/odata.net/pull/2031) Implement asynchronous support in ODataJsonWriterUtils

[[#2032]](https://github.com/OData/odata.net/pull/2032) Implement asynchronous support in ODataJsonLightWriterUtils

[[#2038]](https://github.com/OData/odata.net/pull/2038) Implement asynchronous support in JsonLightODataAnnotationWriter

[[#2046]](https://github.com/OData/odata.net/pull/2046) Implement asynchronous support in ODataJsonLightCollectionSerializer

[[#2048]](https://github.com/OData/odata.net/pull/2048) Implement asynchronous support in ODataJsonLightServiceDocumentSerializer

[[#2050]](https://github.com/OData/odata.net/pull/2050) Implement asynchronous support in ODataJsonLightEntityReferenceLinkSerializer

[[#2051]](https://github.com/OData/odata.net/pull/2051) Add support for $count segment within $expand

[[#2052]](https://github.com/OData/odata.net/pull/2052) Implement asynchronous support in ODataJsonLightResourceSerializer

[[#2055]](https://github.com/OData/odata.net/pull/2055) Add support for Singleton's in VocabularyAnnotationInaccessibleTarget

[[#2058]](https://github.com/OData/odata.net/pull/2058) Modify IODataOutputInStreamErrorListener interface to support asynchronous notifications when an in-stream error is to be written

[[#2069]](https://github.com/OData/odata.net/pull/2069) Add support for $search and $filter within $count segment of $filter collection properties

[[#2075]](https://github.com/OData/odata.net/pull/2075) Modify IODataStreamListener interface to support asynchronous notifications when the content stream of an operation has been disposed

***Fixed Bugs***

[[#2017]](https://github.com/OData/odata.net/pull/2017) Fix IN operator precedence

[[#2018]](https://github.com/OData/odata.net/pull/2018) Make sure we pass the no-dollar flag through

[[#2022]](https://github.com/OData/odata.net/pull/2022) Translate PathSelectItem in ODataUriExtensions.BuildUri

[[#2035]](https://github.com/OData/odata.net/pull/2035) Fix IN operator not working with null Guid values

[[#2068]](https://github.com/OData/odata.net/pull/2068) Fix issue where OData.Core lib fails to parse IN operator with Date, DateTimeOffset and Time parameters

[[#2087]](https://github.com/OData/odata.net/pull/2087) Translate ExpandedCountSelectItem in BuildUri

***Improvements***

[[#1741]](https://github.com/OData/odata.net/pull/1741) Convert BaseEntityType to interface

[[#1992]](https://github.com/OData/odata.net/pull/1992) Client Perf improvement

[[#1993]](https://github.com/OData/odata.net/pull/1993) Perf fixes for ODL- Linq

[[#2024]](https://github.com/OData/odata.net/pull/2024) Performance Fix for swallowed exception in AppendKeySegment

[[#2039]](https://github.com/OData/odata.net/pull/2039) Update Capabilities Vocabulary

[[#2053]](https://github.com/OData/odata.net/pull/2053) Elide async and await in pass-through asynchronous methods

## ODataLib 7.8.3 Release

***Features***

N/A

***Fixed Bugs***

[[#2004]](https://github.com/OData/odata.net/pull/2004) Fix ODataUriExtensions.BuildUri produce invalid URI

[[#1987]](https://github.com/OData/odata.net/pull/1987) Import vocabulary annotations declared in external referenced models

***Improvements***

N/A

## ODataLib 7.8.2 Release

***Features***

[[#1995](https://github.com/OData/odata.net/pull/1995)] Retrieve the content type for stream property from vocabulary annotation by convention

[[#1991](https://github.com/OData/odata.net/pull/1991)] Add Dollar it in BuildUri response

[[#1984](https://github.com/OData/odata.net/pull/1984)] Enable nullable setting for EdmUntypedTypeReference

[[#1966](https://github.com/OData/odata.net/pull/1966)] Add WriteMetadataAsync API to enable write CSDL async

***Fixed Bugs***

[[#1985](https://github.com/OData/odata.net/pull/1985)] Fix reading untyped collections containing values with @type specified

[[#1980](https://github.com/OData/odata.net/pull/1980)] Enable serialize and deseriaze the property with Untyped type

***Improvements***

N/A

## ODataLib 7.8.1 Release

Following are the changes done since 7.7.3.

***Features***

[[#1868](https://github.com/OData/odata.net/pull/1868)] Add IN operator support to client

[[#1921](https://github.com/OData/odata.net/pull/1921)] Support for $apply/aggregate


***Fixed Bugs***

[[#1906](https://github.com/OData/odata.net/pull/1906)] Make $it in filters inside select/expand reference the resource path

[[#1932](https://github.com/OData/odata.net/pull/1932)] Fix reading OData error at synchronous operations for .NET Core.

[[#1933](https://github.com/OData/odata.net/pull/1933)] Fix deserialization of decimal in exponential notation

[[#1934](https://github.com/OData/odata.net/pull/1934)] Fix location header missing for preflight requests

[[#1939](https://github.com/OData/odata.net/pull/1939)] Make AuthorizationVocabularyModel public and add MeasuresVocabularyModel

[[#1942](https://github.com/OData/odata.net/pull/1942)] ConvertFromUriLiteral can't work for short data type

[[#1944](https://github.com/OData/odata.net/pull/1944)] Fix null dynamic property materialization

[[#1946](https://github.com/OData/odata.net/pull/1946)] Added writer setting to add type annotations

[[#1949](https://github.com/OData/odata.net/pull/1949)] Key attribute with one property throws exception

[[#1950](https://github.com/OData/odata.net/pull/1950)] Fix the navigation property binding target path issue

[[#1952](https://github.com/OData/odata.net/pull/1952)] Add CoreVocabularyModel.RevisionsTerm

[[#1953](https://github.com/OData/odata.net/pull/1953)] Add HttpClient implementation of the DataServiceClientRequestMessage.cs

[[#1957](https://github.com/OData/odata.net/pull/1957)] Remove reference to ODataRuleSets file


***Improvements***

[[#1930](https://github.com/OData/odata.net/pull/1930)] Address perf issue with Appending Cast Segment

[[#1954](https://github.com/OData/odata.net/pull/1954)] Performance Fixes


## ODataLib 7.7.3 Release

Following are the changes done since 7.7.2.

***Features***

N/A

***Fixed Bugs***

[[#1913](https://github.com/OData/odata.net/pull/1913)] Enable JSON metadata reader, writer in OData Core 

[[#1915](https://github.com/OData/odata.net/pull/1915)] Issue #1904: GmlReader ignores srsDimension attribute in the posList 

[[#1916](https://github.com/OData/odata.net/pull/1916)] Apply same backslash escaping to string literal within "IN" clause as Equals


***Improvements***

[[#1917](https://github.com/OData/odata.net/pull/1917)] Improve UrlValidation Performance

[[#1924](https://github.com/OData/odata.net/pull/1924)] Add System.Text.Json dependency into the Nuspec




## ODataLib 7.7.2 Release

Following are the changes done since 7.7.1.

***Features***

N/A

***Fixed Bugs***

[[#1880](https://github.com/OData/odata.net/pull/1880)] Downgrade the System.Text.Json package reference version 

[[#1886](https://github.com/OData/odata.net/pull/1886)] Http headers should be case insensitive

[[#1891](https://github.com/OData/odata.net/pull/1891)] Fix logic to validate URL segment by type


***Improvements***

[[#1873](https://github.com/OData/odata.net/pull/1873)] Azure Pipeline update to include Symbols in snupkg

[[#1887](https://github.com/OData/odata.net/pull/1887)] Fix issue #1883, MultipartMixed grows cache indefinitely

[[#1888](https://github.com/OData/odata.net/pull/1888)] Performance Fix: OData.metadata=full is very CPU intensive



## ODataLib 7.7.1 Release

Following are the changes done since 7.7.0.

***Features***

N/A

***Fixed Bugs***

[[#1762](https://github.com/OData/odata.net/pull/1762)] Enable Where clause to generate $filter query options for key predicates

[[#1793](https://github.com/OData/odata.net/pull/1793)] Different null validation messages for complex and primitive collections

[[#1831](https://github.com/OData/odata.net/pull/1831)] Fix bug on atomicityGroup as a dependsOn value in Odata v4

[[#1848](https://github.com/OData/odata.net/pull/1848)] Fix platform not supported exception

[[#1855](https://github.com/OData/odata.net/pull/1855)] Hotfix for the infinite loop if structured type has property which type is the declaring type

[[#1865](https://github.com/OData/odata.net/pull/1865)] Fix Enum as string used with Has Issue


***Improvements***

[[#1779](https://github.com/OData/odata.net/pull/1779)] Build cmd and additional updates for VS upgrade

[[#1795](https://github.com/OData/odata.net/pull/1795)] Migrate FxCop Legacy (Legacy analysis) to FxCop Analyzers (Source analysis)

[[#1840](https://github.com/OData/odata.net/pull/1840)] Update client E2E xunit tests

[[#1849](https://github.com/OData/odata.net/pull/1849)] Add "RemovalDate" property to deprecation annotation

[[#1853](https://github.com/OData/odata.net/pull/1853)] Removed the unnecessary string allocations from hot path in JsonReader

[[#1854](https://github.com/OData/odata.net/pull/1854)] OData path fixes



## ODataLib 7.7.0 Release

Following are the changes done since 7.6.4.

**[Be NOTED]**
One of the main change includes upgrading OData package platform to output libraries for .net4.5, .netstandard1.1 and .netstandard2.0, However for client package .netstandard1.1 is removed

***Features***

[[#1775](https://github.com/OData/odata.net/pull/1775)] Json metadata writer and reader using System.Text.Json

[[#1769](https://github.com/OData/odata.net/pull/1769)] Rule-based ODataUrlValidation engine

[[#1749](https://github.com/OData/odata.net/pull/1749)] Add support for Json Batch Requests in Odata Client

[[#1743](https://github.com/OData/odata.net/pull/1743)] Support Navigation Property on complex types on Client 

[[#1740](https://github.com/OData/odata.net/pull/1740)] Add support for relative and absolute uris in json batch requests

[[#1712](https://github.com/OData/odata.net/pull/1712)] Implement client support for dynamic properties dictionary

***Fixed Bugs***

[[#1811](https://github.com/OData/odata.net/pull/1811)] Fix parsing of floats in $apply/aggregate/average expression

[[#1783](https://github.com/OData/odata.net/pull/1783)] Fix decoding single quote within double-quoted string in action/function parameter alias

[[#1778](https://github.com/OData/odata.net/pull/1778)] CSDL Serialization: NullReferenceException when serializing 1:n NavigationProperties

[[#1770](https://github.com/OData/odata.net/pull/1770)] Fix how composite async methods invoke lower level methods.

[[#1763](https://github.com/OData/odata.net/pull/1763)] Set IEEE754Compatible parameter from the batch request

[[#1760](https://github.com/OData/odata.net/pull/1760)] Fix IN operator double quotes issue 

[[#1756](https://github.com/OData/odata.net/pull/1756)] Fix Reading OData Error Response in OData Client

[[#1731](https://github.com/OData/odata.net/pull/1731)] Support for abstract complex types with no properties

[[#1727](https://github.com/OData/odata.net/pull/1727)] Fix bug where open types are not identified as such during serialization

[[#1659](https://github.com/OData/odata.net/pull/1659)] Enable OData client to send IEEE754Compatible parameter in the request

[[#1656](https://github.com/OData/odata.net/pull/1656)] Avoid carrying over Count query option to the next query.

[[#1607](https://github.com/OData/odata.net/pull/1607)] Fix Select Expand Issue of selecting all navigation properties for Full Metadata request and fixing Odata Context

***Improvements***

[[#1738](https://github.com/OData/odata.net/pull/1738)] Support for Media Link entities with no tracking

[[#1796](https://github.com/OData/odata.net/pull/1796)] Update read write timeout value when timeout value is set

[[#1808](https://github.com/OData/odata.net/pull/1808)] Reduce warnings across projects

[[#1823](https://github.com/OData/odata.net/pull/1823)] Continue to Fix ODL build warnings

[[#1759](https://github.com/OData/odata.net/pull/1759)] Migrate odata client tests to xUnit

[[#1824](https://github.com/OData/odata.net/pull/1824)] Add public api test into pipeline 

[[#1828](https://github.com/OData/odata.net/pull/1828)] Rename AddError to AddMessage 

[[#1815](https://github.com/OData/odata.net/pull/1815)] Validate and throw exception for invalid (empty) collections 

[[#1758](https://github.com/OData/odata.net/pull/1758)] Added Severity enum to EdmError

[[#1729](https://github.com/OData/odata.net/pull/1729)] Improve error message when adding unsupported query option

[[#1695](https://github.com/OData/odata.net/pull/1695)] Support Mocking in OData Client

During this release, we moved over the build setup to azure pipelines and updated the library to support Visual Studio 2019 which will make it easier for the community to contribute. 

---

## ODataLib 7.7.0-beta Release

***Features***

[[#1775](https://github.com/OData/odata.net/pull/1775)] Json metadata writer and reader using System.Text.Json

[[#1769](https://github.com/OData/odata.net/pull/1769)] Rule-based ODataUrlValidation engine

[[#1749](https://github.com/OData/odata.net/pull/1749)] Add support for Json Batch Requests in Odata Client

[[#1743](https://github.com/OData/odata.net/pull/1743)] Support Navigation Property on complex types on Client 

[[#1740](https://github.com/OData/odata.net/pull/1740)] Add support for relative and absolute uris in json batch requests

[[#1712](https://github.com/OData/odata.net/pull/1712)] Implement client support for dynamic properties dictionary

***Fixed Bugs***

[[#1783](https://github.com/OData/odata.net/pull/1783)] Fix decoding single quote within double-quoted string in action/function parameter alias

[[#1778](https://github.com/OData/odata.net/pull/1778)] CSDL Serialization: NullReferenceException when serializing 1:n NavigationProperties

[[#1770](https://github.com/OData/odata.net/pull/1770)] Fix how composite async methods invoke lower level methods.

[[#1763](https://github.com/OData/odata.net/pull/1763)] Set IEEE754Compatible parameter from the batch request

[[#1760](https://github.com/OData/odata.net/pull/1760)] Fix IN operator double quotes issue 

[[#1756](https://github.com/OData/odata.net/pull/1756)] Fix Reading OData Error Response in OData Client

[[#1731](https://github.com/OData/odata.net/pull/1731)] Support for abstract complex types with no properties

[[#1727](https://github.com/OData/odata.net/pull/1727)] Fix bug where open types are not identified as such during serialization

[[#1659](https://github.com/OData/odata.net/pull/1659)] Enable OData client to send IEEE754Compatible parameter in the request

[[#1656](https://github.com/OData/odata.net/pull/1656)] Avoid carrying over Count query option to the next query.

[[#1607](https://github.com/OData/odata.net/pull/1607)] Fix Select Expand Issue of selecting all navigation properties for Full Metadata request and fixing Odata Context

***Improvements***

[[#1758](https://github.com/OData/odata.net/pull/1758)] Added Severity enum to EdmError

[[#1729](https://github.com/OData/odata.net/pull/1729)] Improve error message when adding unsupported query option

[[#1695](https://github.com/OData/odata.net/pull/1695)] Support Mocking in OData Client

During this release, we moved over the build setup to azure pipelines and updated the library to support Visual Studio 2019 which will make it easier for the community to contribute. 

---


## ODataLib 7.6.4 Release

***Features***

[[#1713](https://github.com/OData/odata.net/pull/1713)] Annotating an element using AnnotationPath

***Fixed Bugs***

[[#1700](https://github.com/OData/odata.net/pull/1700)] Fix null reference error when matching binding type 

[[#1676](https://github.com/OData/odata.net/issues/1676)] Adding an EntitySet to a model using the includeServiceDocument throws exception

[[#1671](https://github.com/OData/odata.net/issues/1671)] ArgumentNullException on Any in ODataUriResolver

[[#1614](https://github.com/OData/odata.net/pull/1614)] DataServiceContext can't work with POCO's due to Missing IEdmModel

[[#1334](https://github.com/OData/odata.net/issues/1334)] Missing overload of 'IncludeTotalCount' that takes a 'bool'

[[#1332](https://github.com/OData/odata.net/issues/1332)] LINQ 'Select()' call not producing expected '$select' query option on expression

[[#543](https://github.com/OData/odata.net/issues/543)] OData Client Requires Microsoft.OData.Client.Key attribute for the entity, does not support the common key

***Improvements***

[[#1633](https://github.com/OData/odata.net/pull/1633)] Change version date code to allow dates that don't fit Int16

[[#1305](https://github.com/OData/odata.net/issues/1305)] Improve error message - Unable to call action with complex parameter in complex parameter. 

[[#843](https://github.com/OData/odata.net/issues/843)] DataServiceClientException message improvement

---

## ODataLib 7.6.3 Release

***Fixed Bugs***

[[#1653](https://github.com/OData/odata.net/pull/1653)] Combine Dispose methods on JsonWriter

[[#1651](https://github.com/OData/odata.net/issues/1651)] $filter in (null) does not working

[[#1643](https://github.com/OData/odata.net/issues/1643)] Allow Microsoft.OData.Client.Serializer.GetKeyString to receive an IDictionary Object

[[#1623](https://github.com/OData/odata.net/issues/1623)] IN operator fails on strings with commas in them

[[#1621](https://github.com/OData/odata.net/pull/1621)] Fixes for supporting escape function and key values terminating in colon

[[#11576](https://github.com/OData/odata.net/issues/1576)] Fixes for OData.net Client throwing InvalidOperationException when calling 'Move' on a DataServiceCollection 

***Improvements***

[[#1603](https://github.com/OData/odata.net/pull/1603)] Save ODataPath ToList for improved performance

[[#1602](https://github.com/OData/odata.net/pull/1602)] Performance improvements in FunctionOverloadResolver

---

## ODataLib 7.6.2 Release

***Features***

[[#1585](https://github.com/OData/odata.net/pull/1585)] Support deep updates

***Fixed Bugs***

[[#1598](https://github.com/OData/odata.net/pull/1598)] Support the namespace alias for operation in Uri parser

[[#1586](https://github.com/OData/odata.net/pull/1586)] Fixes for Csdl.GetNamespaceAlias extension method

[[#1583](https://github.com/OData/odata.net/pull/1583)] Allow duplicate properties in $select with no select options

[[#1564](https://github.com/OData/odata.net/pull/1564)] Enable TLS 1.2 for package testing

[[#1558](https://github.com/OData/odata.net/pull/1558)] Update style cop to not require net 3.5 installation

***Improvements***

[[#1595](https://github.com/OData/odata.net/pull/1595)] Update Clone() to copy array pool

---

## ODataLib 7.6.1 Release

***Features***

* N/A

***Fixed Bugs***

[[#1547](https://github.com/OData/odata.net/issues/1547)] Remove the prefix for SrsName in GmlWriter.

***Improvements***

[[#1550](https://github.com/OData/odata.net/pull/1550)] Change naming of DataModification* terms and types in vocabularies.

[[#1553](https://github.com/OData/odata.net/pull/1553)] Change the AssemblyVersionAttribute without the build revision.


---

This release delivers OData core libraries including ODataLib, EdmLib, Spatial and Client.

## ODataLib 7.6.1.beta Release

***Features***

[[#1488](https://github.com/OData/odata.net/pull/1488)] & [[#1532](https://github.com/OData/odata.net/pull/1532)] Support nested query options ($filter, $orderby, $top, $skip, $count, $search, $select, $compute) in $select clause.

[[#1494](https://github.com/OData/odata.net/pull/1494)] Support metadata select exposure control.

***Fixed Bugs***

[[#1395](https://github.com/OData/odata.net/issues/1395)] Fix the problem when defining custom uri function with name 'contains'.

[[#1493](https://github.com/OData/odata.net/issues/1493)] Enable $expand=* after the complex type.

[[#1506](https://github.com/OData/odata.net/pull/1506)] Support groupby with property path length greater than 2.

[[#1516](https://github.com/OData/odata.net/pull/1516)] Do not enforc ContentID Uniqueness outside ChangeSet in MultipartMixed.

[[#1517](https://github.com/OData/odata.net/pull/1517)] Fix async message write.

[[#1526](https://github.com/OData/odata.net/pull/1526)] Correct @odata.context for combination of $select/$apply/$compute/$expand.

[[#1530](https://github.com/OData/odata.net/issues/1530)] Fix TypeSegment class constructor and property setting mismatch.

***Improvements***

[[#1522](https://github.com/OData/odata.net/pull/1522)] Update Authorization, Core, Capabilites vocabularies annotation.

[[#1523](https://github.com/OData/odata.net/pull/1523)] Change ODataInnerError to compliance OData spec.

---

This release delivers OData core libraries including ODataLib, EdmLib, Spatial and Client.

## ODataLib 7.6.0 Release

***Features***

[[#1440](https://github.com/OData/odata.net/pull/1440)] Add the Example Term into Core vocabulary.

[[#1454](https://github.com/OData/odata.net/pull/1454)] Support versioned preferences for reading/writing OData prefix.

[[#1459](https://github.com/OData/odata.net/pull/1459)] Write nested entity reference link(s) in request/response.

[[#1464](https://github.com/OData/odata.net/pull/1464)] Enable to write the nextlink for the collection of entity reference links.

[[#1476](https://github.com/OData/odata.net/pull/1476)] Support reading/writing EDMX with Version=4.01.

***Fixed Bugs***

[[#1318](https://github.com/OData/odata.net/issues/1318)] Unescaped colons in relative Uri cause Invalid URI exception.

[[#1451](https://github.com/OData/odata.net/issues/1451)] FindType doesn't work with alias-qualified names.

[[#1455](https://github.com/OData/odata.net/issues/1455)] Remove bogus validation error for EntitySetPath.

[[#1463](https://github.com/OData/odata.net/pull/1463)] Validate the resource type and resource set type in the same inheritance tree.

[[#1465](https://github.com/OData/odata.net/issues/1465)] Support out of line annotations can't target ENUM member.

[[#1467](https://github.com/OData/odata.net/issues/1467)] Support ENUM parameters for Uri function.

[[#1469](https://github.com/OData/odata.net/pull/1469)] Add a validation rule about target of the annotation should be allowed in the AppliesTo of the term.

***Improvements***

[[#1448](https://github.com/OData/odata.net/pull/1448)] Refactor & Improve the ODataMediaTypeResolver.

[[#1458](https://github.com/OData/odata.net/pull/1458)] Align resource template and generated code.

[[#1473](https://github.com/OData/odata.net/pull/1473)] Reduce ToList calls in operation overload resolver.

[[#1474](https://github.com/OData/odata.net/pull/1474)] Fix tests disabled for large object streaming pull request.
 
---

This release delivers OData core libraries including ODataLib, EdmLib, Spatial and Client.

## ODataLib 7.6.beta Release

***Features***

[[#1204](https://github.com/OData/odata.net/pull/1204)] Support Large object stream (reader & writer).

[[#1400](https://github.com/OData/odata.net/pull/1400)] Enable Uri parser to parse escape function.

[[#1414](https://github.com/OData/odata.net/pull/1414)] Add the IEdmOperationReturn interface and enable annotation on return type of operation.

[[#1422](https://github.com/OData/odata.net/pull/1422)] & [[#1428](https://github.com/OData/odata.net/pull/1428)] Update capabilities, validation & authorization vocabularies.

[[#1426](https://github.com/OData/odata.net/issues/1426)] Properties defined in $compute and $apply could be used in a following query options ($select, $compute, $filter or $orderby).

***Fixed Bugs***

[[#1260](https://github.com/OData/odata.net/issues/1260)] Throw error when null value passed for collection of non-nullable complex type.

[[#1409](https://github.com/OData/odata.net/pull/1409)] Ensure that we could use aliases created in compute() in groupby.

[[#1415](https://github.com/OData/odata.net/issues/1415)] Build filter with "any" and "or" fails on keeping operations priority.

***Improvements***

[[#1418](https://github.com/OData/odata.net/pull/1418)] Use buffer when writing binary or byte array.

[[#1420](https://github.com/OData/odata.net/pull/1420)] Use array pool in JSON writer.

---

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

[Issue #698](https://github.com/OData/odata.net/issues/698) DataServiceQuerySingle\<T\>.GetValueAsync inconsistent with GetValue in support for GET returning 404.

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

## Breaking changes-Validation settings

We used to have lots of validation related members/flags in `ODataMessageReaderSettings` and `ODataMessageWriterSettings`. In OData 7.0, we cleaned up the out-dated flags and put the remained flags together and keep them be considered in a consistent way.

### Removed APIs

| ODataMessageWriterSettings            | ODataMessageReaderSettings            |
|---------------------------------------|---------------------------------------|
| EnableFullValidation                  | EnableFullValidation                  |
| EnableDefaultBehavior()               | EnableDefaultBehavior()               |
| EnableODataServerBehavior()           | EnableODataServerBehavior()           |
| EnableWcfDataServicesClientBehavior() | EnableWcfDataServicesClientBehavior() |
|                                       | UndeclaredPropertyBehaviorKinds       |
|                                       | DisablePrimitiveTypeConversion        |
|                                       | DisableStrictMetadataValidation       |

The EnablexxxBehavior() in writer and reader settings actually wrapped few flags.

| ODataWriterBehavior                         | ODataReaderBehavior         |
|---------------------------------------------|-----------------------------|
| AllowDuplicatePropertyNames                 | AllowDuplicatePropertyNames |
| AllowNullValuesForNonNullablePrimitiveTypes |                             |

Those flags are all removed, and an Enum type would represent all the settings instead.

### New API

A flag Enum type ValidationKinds to represent all validation kinds in reader and writer:

```C#
/// <summary>
/// Validation kinds used in ODataMessageReaderSettings and ODataMessageWriterSettings.
/// </summary>
[Flags]
public enum ValidationKinds
{
    /// <summary>
    /// No validations.
    /// </summary>
    None = 0,

    /// <summary>
    /// Disallow duplicate properties in ODataResource (i.e., properties with the same name).
    /// If no duplication can be guaranteed, this flag can be turned off for better performance.
    /// </summary>
    ThrowOnDuplicatePropertyNames = 1,

    /// <summary>
    /// Do not support for undeclared property for non open type.
    /// </summary>
    ThrowOnUndeclaredPropertyForNonOpenType = 2,

    /// <summary>
    /// Validates that the type in input must exactly match the model.
    /// If the input can be guaranteed to be valid, this flag can be turned off for better performance.
    /// </summary>
    ThrowIfTypeConflictsWithMetadata = 4,

    /// <summary>
    /// Enable all validations.
    /// </summary>
    All = ~0
}
```

Writer: Add member Validations which accepts all the combinations of ValidationKinds

```C#
/// <summary>
/// Configuration settings for OData message writers.
/// </summary>
public sealed class ODataMessageWriterSettings
{

/// <summary>
/// Gets or sets Validations in writer.
/// </summary>
public ValidationKinds Validations { get; set; }

}
```

Reader: Add member Validations which accepts all the combinations of ValidationKinds

```C#

/// <summary>
/// Configuration settings for OData message readers.
/// </summary>
public sealed class ODataMessageReaderSettings
{

/// <summary>
/// Gets or sets Validations in reader.
/// </summary>
public ValidationKinds Validations { get; set; }

}
```

### Sample Usage

`writerSettings.Validations = ValidationKinds.All`
Equal to:
`writerSettings.EnableFullValidation = true`

`readerSettings.Validations |= ValidationKinds.ThrowIfTypeConflictsWithMetadata`
Equal to:
`readerSettings.DisableStrictMetadataValidation = false`

Same applies for the reader settings.

## Breaking change-Change in Query Nodes

The expression of `$filter` and `$orderby` will be parsed to multiple query nodes. Each node has particular representation, for example, a navigation property access will be interpreted as `SingleNavigationNode` and collection of navigation property access will be interpreted as `CollectionNavigationNode`.

Since we have merged complex type and entity type in OData Core lib, complex have more similarity with entity other than primitive property. Also in order to support navigation property under complex, the query nodes' type and hierarchy are changed and adjusted to make it more reasonable.

### Nodes Change

- None

### Nodes Added

- SingleComplexNode
- CollectionComplexNode

### Nodes Renamed

| Old                                 | New                                   |
|-------------------------------------|---------------------------------------|
| NonentityRangeVariable              | NonResourceRangeVariable              |
| EntityRangeVariable                 | ResourceRangeVariable                 |
| NonentityRangeVariableReferenceNode | NonResourceRangeVariableReferenceNode |
| EntityRangeVariableReferenceNode    | ResourceRangeVariableReferenceNode    |
| EntityCollectionCastNode            | CollectionResourceCastNode            |
| EntityCollectionFunctionCallNode    | CollectionResourceFunctionCallNode    |
| SingleEntityCastNode                | SingleResourceCastNode                |
| SingleEntityFunctionCallNode        | SingleResourceFunctionCallNode        |

### Nodes Removed

- CollectionPropertyCast
- SingleValueCast

### API Change

1. Add SingleResourceNode as the  base class of SingleEntityNode and SingleComplexNode

2. SingleNavigationNode and CollectionNavigationNode accepts SingleResourceNode as parent node and also accepts bindingpath in the constructor.The parameter order is also adjusted.

    Take SingleNavigationNode for example:

```C#
public SingleNavigationNode(IEdmNavigationProperty navigationProperty, SingleEntityNode source)
```

Changed to:

```C#
public SingleNavigationNode(SingleResourceNode source, IEdmNavigationProperty navigationProperty, IEdmPathExpression bindingPath)
```

### Behavior Change for complex type nodes

Complex property used to share nodes with primitive property, now it shares most nodes with entity.
Here lists the nodes that complex used before and now.

| Before                          | Now                                |
|---------------------------------|------------------------------------|
| NonentityRangeVariable          | ResourceRangeVariable              |
| NonentityRangeVariableReference | ResourceRangeVariableReference     |
| SingleValuePropertyAccessNode   | SingleComplexNode                  |
| SingleValueCastNode             | SingleResourceCastNode             |
| SingleValueFunctionCallNode     | SingleResourceFunctionCallNode     |
| CollectionPropertyAccessNode    | CollectionComplexNode              |
| CollectionPropertyCastNode      | CollectionResourceCastNode         |
| CollectionFunctionCallNode      | CollectionResourceFunctionCallNode |

## Breaking change-Merged entity & complex types

This page will describes the Public API changes for "Merge entity and complex". The basic idea is that we named both an entity and a complex instance as an `ODataResource`, and named a collection of entity or a collection of complex as an `ODataResourceSet`.

### API Changes

Following is difference of public APIs between ODataLib 7.0 and ODataLib 6.15.

|Enumeration|ODataLib 6.15|ODataLib 7.0|
|---|---|---|
||ODataEntry|ODataResource|
||ODataComplexValue|ODataResource|
||ODataFeed|ODataResourceSet|
||ODataCollectionValue for Complex|ODataResourceSet|
||ODataNavigationLink|ODataNestedResourceInfo|
||||
|ODataPayloadKind|Entry|Resource|
||Feed|ResourceSet|
||||
|ODataReaderState|EntryStart|ResourceStart|
||EntryEnd|ResourceEnd|
||FeedStart|ResourceSetStart|
||FeedEnd|ResourceSetEnd|
||NavigationLinkStart|NestedResourceInfoStart|
||NavigationLinkEnd|NestedResourceInfoEnd|
||||
|ODataParameterReaderState  |Entry|Resource|
||Feed|ResourceSet|
||||
|ODataInputContext|ODataReader CreateEntryReader (IEdmNavigationSource navigationSource, IEdmEntityType expectedEntityType)|ODataReader CreateResourceReader(IEdmNavigationSource navigationSource, IEdmStructuredType expectedResourceType)|
||ODataReader CreateFeedReader (IEdmEntitySetBase entitySet, IEdmEntityType expectedBaseEntityType)|ODataReader CreateResourceSetReader(IEdmEntitySetBase entitySet, IEdmStructuredType expectedResourceType)|
||||
|ODataOutputContext|ODataWriter CreateODataEntryWriter (IEdmNavigationSource navigationSource, IEdmEntityType entityType)|ODataWriter CreateODataResourceWriter(IEdmNavigationSource navigationSource, IEdmStructuredType resourceType)|
||ODataWriter CreateODataFeedWriter (IEdmEntitySetBase entitySet, IEdmEntityType entityType)|ODataWriter CreateODataResourceSetWriter(IEdmEntitySetBase entitySet, IEdmStructuredType resourceType)|
||||
|ODataParameterReader|ODataReader CreateEntryReader ()|ODataReader CreateResourceReader ()|
||ODataReader CreateFeedReader ()|ODataReader CreateResourceSetReader ()|
||||
|ODataParameterWriter|ODataWriter CreateEntryWriter (string parameterName)|ODataWriter CreateResourceWriter (string parameterName)|
||ODataWriter CreateFeedWriter (string parameterName)|ODataWriter CreateResourceSetWriter (string parameterName)|
||||
|ODataMessageReader|public Microsoft.OData.Core.ODataReader CreateODataEntryReader ()|public Microsoft.OData.ODataReader CreateODataResourceReader ()|
||public Microsoft.OData.Core.ODataReader CreateODataEntryReader (IEdmEntityType entityType)|public Microsoft.OData.ODataReader CreateODataResourceReader (IEdmStructuredType resourceType)|
||public Microsoft.OData.Core.ODataReader CreateODataEntryReader (IEdmNavigationSource navigationSource, IEdmEntityType entityType)|public Microsoft.OData.ODataReader CreateODataResourceReader (IEdmNavigationSource navigationSource, IEdmStructuredType resourceType)|
||public Microsoft.OData.Core.ODataReader CreateODataFeedReader ()|public Microsoft.OData.ODataReader CreateODataResourceSetReader ()|
||public Microsoft.OData.Core.ODataReader CreateODataFeedReader (IEdmEntityType expectedBaseEntityType)|public Microsoft.OData.ODataReader CreateODataResourceSetReader (IEdmStructuredType expectedResourceType)|
||public Microsoft.OData.Core.ODataReader CreateODataFeedReader (IEdmEntitySetBase entitySet, IEdmEntityType expectedBaseEntityType|public Microsoft.OData.ODataReader CreateODataResourceSetReader (IEdmEntitySetBase entitySet, IEdmStructuredType expectedResourceType)|
|||public ODataReader CreateODataUriParameterResourceSetReader(IEdmEntitySetBase entitySet, IEdmStructuredType expectedResourceType)|
|||public ODataReader CreateODataUriParameterResourceReader(IEdmNavigationSource navigationSource, IEdmStructuredType expectedResourceType)|
||||
|ODataMessageWriter|public Microsoft.OData.Core.ODataWriter CreateODataEntryWriter ()|public Microsoft.OData.ODataWriter CreateODataResourceSetWriter ()|
||public Microsoft.OData.Core.ODataWriter CreateODataEntryWriter (IEdmNavigationSource navigationSource)|public Microsoft.OData.ODataWriter CreateODataResourceSetWriter (IEdmEntitySetBase entitySet)|
||public Microsoft.OData.Core.ODataWriter CreateODataEntryWriter (IEdmNavigationSource navigationSource, IEdmEntityType entityType)|public Microsoft.OData.ODataWriter CreateODataResourceSetWriter (IEdmEntitySetBase entitySet, IEdmStructuredType resourceType)|
||public Microsoft.OData.Core.ODataWriter CreateODataFeedWriter ()|public Microsoft.OData.ODataWriter CreateODataResourceWriter ()|
||public Microsoft.OData.Core.ODataWriter CreateODataFeedWriter (IEdmEntitySetBase entitySet)|public Microsoft.OData.ODataWriter CreateODataResourceWriter (IEdmNavigationSource navigationSource)|
||public Microsoft.OData.Core.ODataWriter CreateODataFeedWriter (IEdmEntitySetBase entitySet, IEdmEntityType entityType)|public Microsoft.OData.ODataWriter CreateODataResourceWriter (IEdmNavigationSource navigationSource, IEdmStructuredType resourceType)|
|||public ODataWriter CreateODataUriParameterResourceWriter(IEdmNavigationSource navigationSource, IEdmStructuredType resourceType)|
|||public ODataWriter CreateODataUriParameterResourceSetWriter(IEdmEntitySetBase entitySetBase, IEdmStructuredType resourceType)|

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

[Issue #629](https://github.com/OData/odata.net/issues/629) [Support multi-NavigationPropertyBindings for a single navigation property by using different paths](/odata/odatalib/multi-binding)

- Navigation property used in multi bindings with different path is supported for navigation under containment and complex.

[Issue #631](https://github.com/OData/odata.net/issues/631) Support [fluent writer API](/odata/odatalib/fluent-writer-api).

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

[Issue #517](https://github.com/OData/odata.net/issues/517) Centralized reader/writer validation. [Breaking Changes](#breaking-changes-validation-settings)

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
- `GetAnnotation\<T\>()` and `SetAnnotation\<T\>()` will no longer be available because `ODataAnnotatable` should only be responsible for managing instance annotations.

### Public API Enhancement

[Issue #484](https://github.com/OData/odata.net/issues/484) Preference header extensibility

- Public class `HttpHeaderValueElement`, which represents http header value element
- Remove sealed from public class `ODataPreferenceHeader`

[Issue #544](https://github.com/OData/odata.net/issues/544) Change Enum member value type from `IEdmPrimitiveValue` to a more specific type.

- Add interface `IEdmEnumMemberValue` and class `EdmEnumMemberValue` to represent Enum member value specifically. `AddMember()` under `EnumType` now accepts `IEdmEnumMemberValue` instead of `IEdmPrimitiveValue` as member value.

[Issue #621](https://github.com/OData/odata.net/issues/621) Make reader able to read contained entity/entityset without context URL.

[Issue #640](https://github.com/OData/odata.net/issues/640) More sensible type, namely `IEnumerable<object>`, for ODataCollectionValue.Items.

[Issue #643](https://github.com/OData/odata.net/issues/643) Adjust query node kinds in Uri Parser in order to support navigation under complex. [Breaking Changes](#breaking-change-change-in-query-nodes)

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

