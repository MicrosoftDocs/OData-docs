---
title: "OData Extensions changes"
description: "OData Extensions (OData Client Factory) 1.x changelog"
author: chuanboz
ms.author: chuanboz
ms.date: 10/10/2019
ms.topic: article
---

# OData Extensions changelog

## Client Extensions 1.0.1 Release

This release relaxes the version upper bound on the dependencies so the extensions can be used in .NetCore 3 projects.

There are separate packages for OData [V3](https://www.nuget.org/packages/Microsoft.OData.Extensions.V3Client/) and [V4](https://www.nuget.org/packages/Microsoft.OData.Extensions.Client/) clients.

## Migration Extensions 1.0.2 Release

This release fixes a bug in request/response body translation (between OData V3 and V4) where the extension fails to replace the body with the translated one.

## Migration Extensions 1.0.0 Release

The NuGet packages for OData Migration Extensions are available on the [NuGet gallery](https://www.nuget.org/packages/Microsoft.OData.Extensions.Client/).

You can install or update the NuGet packages for OData Migration Extensions v1.0.0 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```PowerShell

PM> Install-Package Microsoft.OData.Extensions.Migration -Version 1.0.0

```

The new OData Migration extensions for ASP.NET Core 2.2+ enables an OData V4 service to handle OData V3 requests, allowing the migration of the service to OData V4 without changing its OData V3 clients.
To get started, refer to [OData Migration extension usage and architecture](/odata/extensions/migration).

## Client Extensions 1.0.0 Release

The NuGet packages for OData Client Extensions are available on the [NuGet gallery](https://www.nuget.org/packages/Microsoft.OData.Extensions.Client/).

You can install or update the NuGet packages for OData Client Extensions v1.0.0 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```PowerShell

PM> Install-Package Microsoft.OData.Extensions.Client -Version 1.0.0

```

## Client Extensions 1.0.0-Beta0

The NuGet packages for OData Client Extensions are available on the [NuGet gallery](https://www.nuget.org/packages/Microsoft.OData.Extensions.Client/).

You can install or update the NuGet packages for OData Client Extensions v1.0.0-Beta0 using the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):

```PowerShell

PM> Install-Package Microsoft.OData.Extensions.Client -Version 1.0.0-Beta0

```

**OData Client Extensions Beta0** includes 2 new packages [Microsoft.OData.Extensions.Client.Abstractions](https://www.nuget.org/packages/Microsoft.OData.Extensions.Client.Abstractions/) and [Microsoft.OData.Extensions.Client](https://www.nuget.org/packages/Microsoft.OData.Extensions.Client/) for ASP.NET Core 2.x.

Both packages depends on [OData Client 7.x](https://www.nuget.org/packages/Microsoft.OData.Client/7.5.1), the code for the packages can be found [here](https://github.com/OData/Extensions/tree/master/src/Microsoft.OData.Extensions.Client), the namespace for both packages is Microsoft.OData.Extensions.Client.

The new OData client extensions for ASP.NET Core 2.x package introduced OData Client Factory to supports the extensions model and have default extensions to integrate with Http client factory. To get started, see [Use Extensions in OData Client](/odata/client/using-extensions) that works naturally with ASP.NET Core dependency injection. You can learn more about ASP.NET Core from the [documentation](/aspnet/core/).
