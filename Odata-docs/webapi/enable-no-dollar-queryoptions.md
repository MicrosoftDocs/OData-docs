---
title : "Simplified optional-$-prefix for OData query option - WebAPI queries"
description: "7.x WebAPI query parser use optional-$-prefix for OData query option"

ms.date: 7/1/2019
---
# Simplified optional-$-prefix query option 
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Since ODL-6.x, **OData Core Library** supports query option with optional-$-prefix as described in [this docs](/odata/odatalib/di-support).

Corresponding support on **WebAPI** layer is available starting WebAPI-7.4.

As result, WebAPI is able to process OData system query with optional $-prefix, as in "GET ~/?filter=id eq 33" with injected dependency setting:
```c#
    ODataUriResolver.EnableNoDollarQueryOptions = true.
```

## ODL Enhancement

A public Boolean attribute EnableNoDollarQueryOptions is added to ODataUriResolver. Public attribute is needed for dependency injection on the WebAPI layer.
```c#
    public class ODataUriResolver
    {
        ...
        public virtual bool EnableNoDollarQueryOptions { get; set; }
        ...
    }
```

### WebAPI optional-$-prefix Setting using Dependency Injection
WebAPI service injects the setting using the ODataUriResolver during service initialization:
Builder of service provider container sets the instantiated ODataUriResolver config using dependency injection.

```c#
            ODataUriResolver resolver = new ODataUriResolver
            {
                EnableNoDollarQueryOptions = true,
                EnableCaseInsensitive = enableCaseInsensitive
            };
            spContainerBuilder.AddService(ServiceLifetime.Singleton, sp => resolver));
```

Note that UriResolver is typically a singleton for the service instance, since each instance should follow the same Uri convention. In case of other injected dependencies that are configurable per request, scoped dependency should be used.

## WebAPI Internal Processing of optional-$-prefix Setting

1. WebAPI EnableQuery attribute processing instantiates WebAPI's ODataQueryOptions object for incoming request.
2. The ODataQueryOptions constructor pins down the optional-$-prefix setting (see _enableNoDollarSignQueryOptions) from the injected ODataUriResolver.
3. Based on the optional-$-prefix setting, ODataQueryOptions parses the request Uri in WebAPI layer accordingly.
