---
title : "Supported library version lifecycle"
author: gdebruin
ms.date: 9/25/2023
---

# Accidental breaking change releases

Nuget packages published by the [OData nuget profile](https://www.nuget.org/profiles/OData) will have the following actions taken to mitigate the accidental release of a library version that introduces a breaking change:
1. The package will be marked deprecated.
2. The deprecated package will specify an alternate version to install (usually `Latest`) with a message indicating what the breaking change was.
3. The package will be unlisted.
