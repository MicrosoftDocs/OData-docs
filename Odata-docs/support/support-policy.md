---
title:  "OData libraries support policy"
description: Support policy of the official OData libraries by Microsoft.
date:   2024-01-23
ms.date: 1/23/2023
author: habbes
ms.author: clhabins
---

# Support policy for official .NET OData libraries

**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v8.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-odatalib](../includes/appliesto-odatalib-v7.md)]

This guide documents official support policy for official OData libraries for .NET distributed by Microsoft.

Supported releases are patched for security and other critical bugs and may receive new features. Only the latest minor and patch of a given release is supported. For example, for Microsoft.OData.Core 7 release, only the latest 7.x.y release is supported. Customers are encouraged to always use the latest patch of a given release. In case issues are encountered, the first step should generally be to update to the latest version of that major release.

Throughout this document, the use of the term *release* is tied to the major version. When we say that we support this release, it means we can ship changes to this package under the same major version, but potentially different minor or patch version. That is to say, if version 7 of a library is actively supported, and the latest version is 7.2.1, we can ship new version such as 7.2.2 or 7.3.0. If it is out of support, it means we'll not release a new 7.x version.

Major version updates (for example from Microsoft.OData.Core 7 to Microsoft.OData.Core 8) often have breaking changes. Thorough testing is advised when updating across major versions. Use the relevant migration guides for guidance on dealing with breaking changes.

Minor version updates do not typically contain breaking changes. However, thorough testing is still advised since new features can introduce regressions. When regressions are detected, they may be rolled back in a subsequent minor or patch release. In the event of an accidental breaking change, we will deprecate the package version, specify an alternate version, and unlist the package version from NuGet.

## Support phases

A major release will go through the following support phases:

- **Preview mode**: Before a new major version is officially released, pre-release versions are published to public to allow people to assess the product and share feedback or point out flaws. Pre-release software may contain critical bugs. Additionally, we can make breaking changes during the preview phase of a release. As a result, preview releases are not recommended in production. This phase includes preview releases, alpha releases, beta releases and release candidates.
- **Active support**: The release is in active development, receives security patches, bug fixes and feature updates.
- **Maintenance mode**: The release will receive security patches and critical bug fixes only . A release will be in maintenance mode at least 6 months before entering End of Life.
- **End of Life**: The release is out of support. While it may be available for download, it will not receive any new updates, including security patches. Microsoft will provide a minimum of 12 months notification before an OData library goes out of support.

## OData.NET libraries

OData.NET libraries in this context refer to `Microsoft.OData.Core`, `Microsoft.OData.Edm`, `Microsoft.Spatial` and `Microsoft.OData.Client`.

| Version     | Original release date | Latest patch version  | Latest patch release date | Support phase | End of support |
| ------------|-----------------------|-----------------------|---------------------------|---------------|----------------|
| 8.x | 2024-08-12 | 8.0.0 | 2024-08-12 | Active | TBD |
| 7.x | 2016-08-22 |7.21.3 |2024-06-03 | Active | TBD |
| 6.x | 2013-12-09 | 6.19.0 | 2017-11-14 | Maintenance Mode | 2025-07-15 |
| 5.x | 2012-11-07 | 5.8.5 | 2021-11-12 | Maintenace Mode | TBD |

### OData.NET 8.x

OData.NET 8 is the next major release of the OData.NET libraries, including `Microsoft.OData.Core` 8, `Microsoft.OData.Edm` 8, `Microsoft.Spatial` 8 and `Microsoft.OData.Client` 8.
These libraries will only support .NET 8 and later.

OData.NET 8 will support the following OData protocal versions:

- OData version 4.0
- OData version 4.01

### OData.NET 7.x

OData.NET 7.x refers to 7.x versions of OData.NET libraries.

The libraries are actively supported but will enter maintenance mode 6 months after the OData.NET 8 release.

OData.NET 7 supports the following .NET versions:

- .NET Core 3.1 and later
- .NET Standard 1.1 and later
- .NET Framework 4.5 and later

OData.NET 7 supports the following OData protocol versions:

- OData version 4.0
- OData version 4.01

### OData.NET 6.x

OData.NET 6.x refers to 6.x versions of OData.NET libraries.

The library is currently in maintenance mode and will go out of support on July 15, 2025.

OData.NET 6 supports OData protocol version 4.0.

### OData.NET 5.x

OData.NET 5.x refers to 5.x versions of the following libraries:

- Microsoft.Data.OData
- Microsoft.Data.Edm
- System.Spatial
- Microsoft.Data.Services.Client
- Microsoft.Data.Services

The libraries are currently in maintenance mode. There are no current plans to end support. There will be an official announcements at least 12 months before official support ends.

The libraries support .NET Framework 4.0 and later.

The libraries support OData proctocol versions 1 to 3.

## AspNetCore OData

This refers to the `Microsoft.AspNetCore.OData`  library which adds OData integration to ASP.NET Core applications.

| Version     | OData.NET version | Original release date | Latest patch version  | Latest patch release date | Support phase | End of support |
| ------------|-------------------|-----------------------|-----------------------|---------------------------|---------------|----------------|
| 9.x | 8.x | TBD | 9.0.0-rc.1 | 2024-06-05 | Preview | TBD |
| 8.x | 7.x | 2021-07-03 | 8.2.5 |2023-03-01 | Active | TBD |
| 7.x | 7.x | 2018-06-29 | 7.7.6 | 2024-07-12 | Active | TBD |

Microsoft.AspNetCore.OData 9.x will be the next major release. This version will only support .NET 8 and later and OData.NET 8 and later.

Microsoft.AspNetCore 8.x is currently actively supported. It supports .NET 5 and later and OData.NET 7.x.

Microsoft.AspNetCore 7.x is currently actively supported but will enter maintenance mode soon. It supports .NET Framework 4.5 and later and .NET 5 and later. It supports OData.NET 7.x.

## AspNet OData

This refers to the `Microsoft.AspNet.OData` library which adds OData integration to ASP.NET 4.x on .NET Framework.

| Version     | OData.NET version | Original release date | Latest patch version  | Latest patch release date | Support phase | End of support |
| ------------|-------------------|-----------------------|-----------------------|---------------------------|---------------|----------------|
| 7.x | 7.x | 2018-06-29 | 7.7.6 | 2024-07-12 | Active | TBD |

Microsoft.AspNet.OData 7.x is currently actively support but will enter maintenance mode soon. This version supports .NET Framework 4.5 and later. It supports OData.NET 7.x.

## OData ModelBuilder

The `Microsoft.OData.ModelBuilder` library provides APIs that make it easier to create an IEdmModel from CLR (common language runtime) classes.

| Version     | OData.NET version | Original release date | Latest patch version  | Latest patch release date | Support phase | End of support |
| ------------|-------------------|-----------------------|-----------------------|---------------------------|---------------|----------------|
| 1.x | 7.x and 8.x | 2020-09-02 | 1.0.9 | 2022-06-10 | Active | TBD |

## OData Connected Service

OData Connected Service is a Visual Studio (VS) extension that allows you to generate C# and VB.NET client code for an OData service from an OData CSDL schema.
OData Connected Service is distributed via the Visual Studio Marketplace. We support two variants of the extension:

- [OData Connected Service 2022+](https://marketplace.visualstudio.com/items?itemName=marketplace.ODataConnectedService2022) for Visual Studio 2022 and later
- [OData Connected Service](https://marketplace.visualstudio.com/items?itemName=marketplace.ODataConnectedService) for Visual studio 2017 and 2019

| Variant | Supported VS editions | Version | Latest patch version  | Latest patch release date | Support phase | End of support |
| --------|-----------------------|---------|-----------------------|---------------------------|---------------|----------------|
| OData Connected Service 2022+ | 2022 | 1.x | 1.1.0 | 2024-06-18 | Active | TBD |
| OData Conencted Service | 2017, 2019 | 1.x | 1.1.0 | 2024-06-18 | Active | TBD |

## OData CLI

OData CLI is a cross-platform command-line tool for generating C# and VB.NET client code for an OData service based on the CSDL schema.

| Version     | OData.NET version | Original release date | Latest patch version  | Latest patch release date | Support phase | End of support |
| ------------|-------------------|-----------------------|-----------------------|---------------------------|---------------|----------------|
| 0.x | 7.x and 8.x | 2022-03-30 | 0.3.1 | 2024-06-19 | Active | TBD |

OData CLI supports .NET 6 and later.
