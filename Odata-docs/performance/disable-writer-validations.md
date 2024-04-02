---
title:  "Disable writer validations"
description: A guide on how to improve performance by disabling writer validations.
date:   2024-01-23
ms.date: 1/23/2023
author: habbes
ms.author: clhabins
---

# Disable writer validations

**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v8.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-odatalib](../includes/appliesto-odatalib-v7.md)]

During serialization, the OData writer performs various validations to ensure that the payload is valid (e.g. no duplicate properties) and conforms to the schema (e.g. property type matches the entity type's definition). These validations introduce some overhead to the serialization process. If you know ahead of time that the payload you're writing is correct, you can disable one or more validations to improve performance.

Use the [`ODataMessageWriterSettings.Validations`](/dotnet/api/microsoft.odata.odatamessagewritersettings.validations) property to configure which validations you want to enable or disable.

You can disable all validations using [`ValidationKinds.None`](/dotnet/api/microsoft.odata.validationkinds):

```csharp
messageWriterSettings.Validations = ValidationKinds.None;
```

You can selectively enable validations using the `&` operator:

```csharp
messageWriterSettings.Validations = ValidationKinds.ThrowOnDuplicatePropertyNames & ValidationKinds.ThrowIfTypeConflictsWithMetadata
```

You can enable all validations using [`ValidationKinds.All`](/dotnet/api/microsoft.odata.validationkinds):

```csharp
messageWriterSettings.Validations = ValidationKinds.All;
```

To learn more about different validation options, consult the [`ValidationKinds` enum documentation](/dotnet/api/microsoft.odata.validationkinds).

## Disabling writer validations when using `Microsoft.OData.Core` library directly

When using the core library directly, you pass the [`ODataMessageWriterSettings`](/dotnet/api/microsoft.odata.odatamessagewritersettings) to the [`ODataMessageWriter`](/dotnet/api/microsoft.odata.odatamessagewriter) constructor.

```csharp
ODataMessageWriterSettings settings = new ODataMessageWriterSettings
{
    ODataUri = new ODataUri() { /* ... */ },
    Validations = ValidationKinds.None
};

ODataMessageWriter writer = new ODataMessageWriter(odataMessage, settings, edmModel);

// ...
```

## Disabling writer validations when using `Microsoft.AspNetCore.OData` 8 library

In `Microsoft.AspNetCore.OData` 8, you can pass a service configuration action as the third argument of `AddRouteComponents`. Use
this to inject a request-scoped instance of `ODataMessageWriterSettings` - with the desired options - to the service container:

```csharp
services
.AddControllers()
.AddOData(options =>
    options.AddRouteComponents(
        routePrefix: "routePrefix",
        model: edmModel,
        configurateServices: container =>
        {
            container.AddScoped<ODataMessageWriterSettings>(_ => new ODataMessageWriterSettings
            {
                Validations = ValidationKinds.None
            })
        }));
```

## Disabling writer validations when using `Microsoft.AspNetCore.OData` 7 library

In `Microsoft.AspNetCore.OData` 7, you can pass a service configuration action to the `MapODataRoute` and `MapODataServiceRoute` methods.
Use this to inject a request-scoped instance of `ODataMessageWriterSettings` to the service container.

### Using endpoint routing

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{

    // ...

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapODataRoute(
            routeName: "odata",
            routePrefix: “odata”,
            configureAction: container =>
            {
                container.AddService<ODataMessageWriterSettings>(Microsoft.OData.ServiceLifetime.Scoped, _ => new ODataMessageWriterSettings
                {
                    Validations = ValidationKinds.None
                });

                container.AddService(Microsoft.OData.ServiceLifetime.Singleton, _ => model);
                container.AddService<IEnumerable<IODataRoutingConvention>>(Microsoft.OData.ServiceLifetime.Singleton,
                    sp => ODataRoutingConventions.CreateDefaultWithAttributeRouting("odata", sp));
            });
    });

    // ...
}
```

### Using MVC routing

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    IEdmModel model = GetEdmModel();

    app.UseMvc(builder =>
    {
        builder.Select().Expand().Filter().OrderBy().MaxTop(100).Count();

        builder.MapODataServiceRoute(
            routeName: "odata",
            routePrefix: "odata",
            configureAction: container =>
            {
                container.AddService<ODataMessageWriterSettings>(Microsoft.OData.ServiceLifetime.Scope, _ => new ODataMessageWriterSettings
                {
                    Validations = ValidationKinds.None
                });
    
                container.AddService(Microsoft.OData.ServiceLifetime.Singleton, _ => model)
                    .AddService<IEnumerable<IODataRoutingConvention>>(Microsoft.OData.ServiceLifetime.Singleton, _ =>
                        ODataRoutingConventions.CreateDefaultWithAttributeRouting("odata", builder));
            });
    });
}
```
