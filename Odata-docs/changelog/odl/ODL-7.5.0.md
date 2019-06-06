---
title: "ODataLib 7.5.0"
description: "ODataLib 7.5.0 release notes"

---

## Changes in ODataLib 7.5.0 Release ##

> [!NOTE]
> ODataLib 7.5.0 includes the following items: IN operator, Entity set aggregation, various bug fixes, and performance improvement.

## Features ##

[[#757](https://github.com/OData/odata.net/pull/757)] Entity set aggregations

[[#1165](https://github.com/OData/odata.net/pull/1165)] [Feature] IN operator


## Fixed Bugs ##

[[#513](https://github.com/OData/odata.net/issues/513)] $select with complexCol/prop should be supported

[[#985](https://github.com/OData/odata.net/issues/985)] VS 2017 15.4.2 Broken client

[[#1043](https://github.com/OData/odata.net/issues/1043)] Thread safety of context item collections

[[#1044](https://github.com/OData/odata.net/issues/1044)] Code generation from T4 template - IgnoreUnexpectedElementsAndAttributes doesn't seem to work

[[#1087](https://github.com/OData/odata.net/issues/1187)] Batch is broken by load balancer (proxy)

[[#1148](https://github.com/OData/odata.net/issues/1148)] OData Code Generator does not install on VS2015

[[#1123](https://github.com/OData/odata.net/pull/1123)] Include dots in parseIdentifier for annotation parsing

[[#1130](https://github.com/OData/odata.net/pull/1130)] Add StringComparison in String.Equal() method call

[[#1142](https://github.com/OData/odata.net/pull/1142)] fix write document metadata properties for apply ComputeTransformationNode

[[#1143](https://github.com/OData/odata.net/pull/1143)] fix build uri from groupby navigation property with many child structural properties

[[#1145](https://github.com/OData/odata.net/issues/1145)] Memory Leak in Library?

[[#1151](https://github.com/OData/odata.net/pull/1151)] Port client-side DateTime support fix to 7.x

[[#1153](https://github.com/OData/odata.net/issues/1153)] UriParser should throw exception for mismatched keys count when UnqualifiedODataUriResolver is configured

[[#1155](https://github.com/OData/odata.net/issues/1155)] FunctionCallBinder issue when case-insensitive is enabled

[[#1157](https://github.com/OData/odata.net/issues/1157)] ODataMessageReader doesn't honor the absence of ValidationKinds.ThrowIfTypeConflictsWithMetadata flag

[[#1159](https://github.com/OData/odata.net/pull/1159)] remove the Document element from Edm lib

[[#1182](https://github.com/OData/odata.net/pull/1182)] Fix a wrong if condition

[[#1191](https://github.com/OData/odata.net/issues/1191)] Avoid String allocations when writing strings that require special character handling

---

This release delivers OData core libraries including ODataLib, EdmLib, Spatial and Client.