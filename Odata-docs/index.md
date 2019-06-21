---
title : "OData overview"
description: "OData overview."
ms.date: 06/17/2019
---

# Welcome to OData

OData (Open Data Protocol) is an ISO/IEC approved, OASIS standard that defines a set of best practices for building and consuming REST APIs.
It enables creation of REST-based services which allow resources identified using Uniform Resource Locators (URLs) and defined in a data model, to be published and edited by Web clients using simple HTTP messages.

OData helps applications to focus on business logic without worrying about the various API approaches to define request and response headers, status codes, HTTP methods, URL conventions, media types, payload formats, query options, etc.
It provides guidance for tracking changes, defining functions/actions for reusable procedures, and sending asynchronous/batch requests.

## Protocol

The OData Protocol is an application-level protocol for interacting with data via RESTful interfaces. It supports the description of data models, editing and querying of data according to those models. REST APIs that are based on OData are easy to discover and consume due to the OData metadata, a machine-readable description of the data model which renders in a human readable format and enables the creation of powerful generic client proxies and tools.

OData improves semantic interoperability between systems and follows these design principles:

- Follow REST principles.
- Keep it simple. Address the common cases and provide extensibility where necessary.
- Build incrementally. A very basic, compliant service should be easy to build, with additional work necessary only to support additional capabilities.
- Extensibility is important. Services should be able to support extended functionality without breaking clients unaware of those extensions.
- Prefer mechanisms that work on a variety of data sources. In particular, do not assume a relational data model.

The OData Protocol is different from other REST-based web service approaches in that it provides a uniform way to describe both the data and the data model. This improves semantic interoperability between systems and allows an ecosystem to emerge. It follows these design principles:

The following image shows how different libraries can be used for server and client side implementations.
![libraries](/odata/assets/library-relationship.png)

You can find more information on OData specification at [OData.org](https://www.odata.org/).
