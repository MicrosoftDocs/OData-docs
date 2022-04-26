---
title: " Fluent functional-style writer API"
description: "This section describes how to use the fluent functional-style API."
author: madansr7
ms.author: madansr7
ms.date: 7/1/2019
ms.topic: article
 
---
# Fluent functional style writer API
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

In the previous section, paired `WriteStart()`/`WriteEnd()` calls have been made to write payloads. In this version, a new set of fluent functional-style API has been introduced as an improvement over the previous API which is rather primitive, requiring paired `WriteStart()`/`WriteEnd()` calls.

The new API replaces paired `WriteStart()`/`WriteEnd()` calls with a single `Write()` call. `Write()` comes in two flavors. The first flavor takes a single argument which is the thing you want to write. For example, `writer.Write(entry);` is equivalent to

```csharp
writer.WriteStart(entry);
writer.WriteEnd();
```

The second flavor takes two arguments. The first argument is same as before. The second argument is an <xref:System.Action> delegate which is to be invoked in-between writing the first argument. For instance,

```csharp
writer.Write(outer, () => writer
    .Write(inner1)
    .Write(inner2));
```

is equivalent to

```csharp
writer.WriteStart(outer);
    writer.WriteStart(inner1);
    writer.WriteEnd();
    writer.WriteStart(inner2);
    writer.WriteEnd();
writer.WriteEnd();
```

In general, this new API should be preferred to the previous one.
