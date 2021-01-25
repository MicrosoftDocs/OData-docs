---
title: "OData ModelBuilder changes"
description: "OData ModelBuilder changelog"
author: g2mula
ms.author: ginjira
ms.date: 09/09/2020
ms.topic: article
---

# OData ModelBuilder changelog

OData ModelBuilder available on the [Nuget gallery](https://www.nuget.org/packages/Microsoft.OData.ModelBuilder)

You can install or update the NuGet package for OData ModelBuilder using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```PowerShell
PM> Install-Package Microsoft.OData.ModelBuilder
```

## OData ModelBuilder 1.0.6

* Update the OData.Lib dependency lib version to 7.7.3 and remove the explict `System.Text.Json` dependency.

* Map System.Object to Edm.Untyped and Collection Of System.Object to Collection(Edm.Untyped)

```C#
pubic class Customer
{
    public object Info {get;set;}

    public object[] Data {get;set;}
}
```
With the model builder, 
* The Edm type of `Info` property is `Edm.Untyped`
* The Edm type of `Data` property is `Collection(Edm.Untyped)`

---

## OData ModelBuilder 1.0.5

* ClrTypeAnnotation can accept null
* Relax the system component package version dependency

## OData ModelBuilder 1.0.4

* Add instance annotation container

## OData ModelBuilder 1.0.3

### New Features

* [ [#9](https://github.com/OData/ModelBuilder/pull/9) ] Enable Capabilities Vocabulary Configuration

### Improvements and Bug fixes

N/A

## OData ModelBuilder 1.0.1-beta

Extracted ModelBuilder from OData WebApi
