---
title : "OData tools"
ms.date: 06/14/2019
---

# Tools

The following tools aid development of an OData REST api service or helps consume an existing OData capable endpoint.

## OData client

OData client is a LINQ-enabled client API for issuing OData queries and consuming OData JSON payloads. It supports OData v4 protocol and available for target frameworks .NET 4.5 and .NET Platform Standard 1.1. OData client is available as a [nuget package](https://www.nuget.org/packages/Microsoft.OData.Client/).

## OData connected service

OData Connected Service is a tool that generates code to facilitate consumption of OData services. This tool will generate a DataServiceContext and classes for each of the entity types and complex types found in the service description. It is functionally equivalent to the Add Service Reference for OData V3 service. Connected service is available via visual studio marketplace at [OData connected service](https://marketplace.visualstudio.com/items?itemName=laylaliu.ODataConnectedService)

## OData code gen

This extension contains a T4 item template which is used to generate C# and VB .NET client-side proxy classes for [OData protocol version 4.0](http://docs.oasis-open.org/odata/odata/v4.0/cs02/part1-protocol/odata-v4.0-cs02-part1-protocol.doc) services. The generated classes work with the rebranded []OData Client](http://www.nuget.org/packages/Microsoft.OData.Client) library for .NET.

## Postman tutorials

The Postman tutorial will walk you through more than 20 real OData requests and responses. The tutorial is designed to use the TripPin reference service but again, the principles here can be applied to any OData API.

## OData for Visual Studio code

OData for Visual Studio Code is a Visual Studio Code extension that adds rich support for the OData query language.
You can download the extension from the [visual studio marketplace](https://marketplace.visualstudio.com/items?itemName=stansw.vscode-odata).

## XOData

XOData is a tool that helps to explore an API by visualizing the schema  available. It has an interactive query builder and data explorer. The tools is available at [odata.org](https://pragmatiqa.com/xodata/)

## Trippin service

This OData V4 sample service is built with [Restier](https://github.com/odata/restier) which is a turn-key library for building RESTful services, it covers most V4 features and its source code is located at this link.
