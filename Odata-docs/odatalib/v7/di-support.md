---
title: " Dependency Injection Support"
description: "This section describes how we can use Dependency Injection to increase the extensibility of the library."
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---

# Dependency injection
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

In ODataLib v7.0, we introduced [Dependency Injection](https://en.wikipedia.org/wiki/Dependency_injection) (or "DI" in short) support to dramatically increase the extensibility of the library where users can plug in their custom implementations and policies in an elegant way. Introduction of DI can also simplify the API and implementation of ODataLib by eliminating redundant function parameters and class properties. Since ODataLib is a reusable library, we don't take direct dependency on any existing DI framework. Instead we build and rely on an abstraction layer including several simple interfaces that decouples ODataLib from any concrete DI implementation. Users of ODataLib will be free to choose whatever DI framework they like to work with ODataLib.

## Introduction to DI
For a complete understanding of the concept of DI and how it works in a typical ASP.NET Web application, please refer to the [introduction](https://docs.asp.net/en/latest/fundamentals/dependency-injection.html) from ASP.NET Core.

To make DI work properly with ODataLib, basically there are several things you have to do within your application:

 - Implement your container builder based on your DI framework.
 - Register the required services from both ODataLib and your application.
 - Build and use the container (to retrieve the services) in ODataLib.

## Implement Your Container Builder
Since ODataLib is based on .NET, we use the interface `IServiceProvider` from .NET Framework as the abstraction of "container". The container itself is read-only (as you can see, `IServiceProvider` only has a `GetService` method) so we designed another interface `IContainerBuilder` in ODataLib to build the container. Below is the [source](https://github.com/OData/odata.net/blob/ODataV4-7.x/src/Microsoft.OData.Core/IContainerBuilder.cs) of `IContainerBuilder`:

```C#
public interface IContainerBuilder
{
    IContainerBuilder AddService(
        ServiceLifetime lifetime,
        Type serviceType,
        Type implementationType);

    IContainerBuilder AddService(
        ServiceLifetime lifetime,
        Type serviceType,
        Func<IServiceProvider, object> implementationFactory);

    IServiceProvider BuildContainer();
}
```

The first `AddService` method registers a service by its implementation type while the second one registers using a factory method. The `BuildContainer` method is used to build a container that implements `IServiceProvider` which contains all the services registered. The first parameter of `AddService` indicates the lifetime of the service you register. Below is the [source](https://raw.githubusercontent.com/OData/odata.net/ODataV4-7.x/src/Microsoft.OData.Core/ServiceLifetime.cs) of `ServiceLifetime`. For the meaning of each member, please refer to the [doc](https://docs.asp.net/en/latest/fundamentals/dependency-injection.html?csharp=dependency#service-lifetimes-and-registration-options) from ASP.NET Core.

```C#
public enum ServiceLifetime
{
    Singleton,
    Scoped,
    Transient
}
```

Once you have determined a specific DI framework to use in your application, you need implement a container builder from `IContainerBuilder` based on the DI framework you choose. In this tutorial, we will use the [Microsoft DI Framework](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection/) (the default DI implementation for ASP.NET Core) as an example. The implementation of the container builder should more or less look like below:

```C#
using System;
using System.Diagnostics;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.OData;
using ServiceLifetime = Microsoft.Extensions.DependencyInjection.ServiceLifetime;

public class TestContainerBuilder : IContainerBuilder
{
    private readonly IServiceCollection services = new ServiceCollection();

    public IContainerBuilder AddService(
        Microsoft.OData.ServiceLifetime lifetime,
        Type serviceType,
        Type implementationType)
    {
        Debug.Assert(serviceType != null, "serviceType != null");
        Debug.Assert(implementationType != null, "implementationType != null");

        services.Add(new ServiceDescriptor(
            serviceType, implementationType, TranslateServiceLifetime(lifetime)));

        return this;
    }

    public IContainerBuilder AddService(
        Microsoft.OData.ServiceLifetime lifetime,
        Type serviceType,
        Func<IServiceProvider, object> implementationFactory)
    {
        Debug.Assert(serviceType != null, "serviceType != null");
        Debug.Assert(implementationFactory != null, "implementationFactory != null");

        services.Add(new ServiceDescriptor(
            serviceType, implementationFactory, TranslateServiceLifetime(lifetime)));

        return this;
    }

    public IServiceProvider BuildContainer()
    {
        return services.BuildServiceProvider();
    }

    private static ServiceLifetime TranslateServiceLifetime(
        Microsoft.OData.ServiceLifetime lifetime)
    {
        switch (lifetime)
        {
            case Microsoft.OData.ServiceLifetime.Scoped:
                return ServiceLifetime.Scoped;
            case Microsoft.OData.ServiceLifetime.Singleton:
                return ServiceLifetime.Singleton;
            default:
                return ServiceLifetime.Transient;
        }
    }
}
```

Basically what the `TestContainerBuilder` does is delegating the service registrations to the inner `ServiceCollection` which comes from the Microsoft DI Framework. By the way, this is the exact implementation of `IContainerBuilder` we use in Web API OData v6.x :-)

Of course, the APIs of `IContainerBuilder` are kind of "primitive" thus they are not so convenient when directly used to register services. That's what we are going to address in the next section.

### Register the Required Services
Once you have your container builder, the next step is to register the required services into the container. We defined many extension methods in [`ContainerBuilderExtensions`](https://github.com/OData/odata.net/blob/ODataV4-7.x/src/Microsoft.OData.Core/ContainerBuilderExtensions.cs) to `IContainerBuilder` to ease the service registration. Below are the signatures of the extension methods and their corresponding examples.

```C#
public static class ContainerBuilderExtensions
{
    // Examples:
    //   builder.AddService<ITestService, TestService>(ServiceLifetime.Singleton);
    //   builder.AddService<TestService, DerivedTestService>(ServiceLifetime.Scoped);
    //
    // The following will NOT work. TImplementation MUST be a concrete class:
    //   builder.AddService<ITestService, ITestService>(ServiceLifetime.Scoped);
    public static IContainerBuilder AddService<TService, TImplementation>(
        this IContainerBuilder builder,
        ServiceLifetime lifetime)
        where TService : class
        where TImplementation : class, TService

    // Examples:
    //   builder.AddService(ServiceLifetime.Transient, typeof(TestService));
    //
    // The following will NOT work. serviceType MUST be a concret class:
    //   builder.AddService(ServiceLifetime.Transient, typeof(ITestService));
    public static IContainerBuilder AddService(
        this IContainerBuilder builder,
        ServiceLifetime lifetime,
        Type serviceType);

    // Examples:
    //   builder.AddService<TestService>(ServiceLifetime.Transient);
    // which is equivalent to:
    //   builder.AddService<TestService, TestService>(ServiceLifetime.Transient);
    //
    // The following will NOT work. TService MUST be a concret class:
    //   builder.AddService<ITestService>(ServiceLifetime.Transient);
    public static IContainerBuilder AddService<TService>(
        this IContainerBuilder builder,
        ServiceLifetime lifetime)
        where TService : class

    // Examples:
    //   builder.AddService(ServiceLifetime.Singleton, sp => TestService.Instance);
    //   builder.AddService<ITestService>(ServiceLifetime.Scoped, sp => new TestService());
    //   builder.AddService(ServiceLifetime.Singleton, sp => new TestService(sp.GetRequiredService<DependentService>()));
    public static IContainerBuilder AddService<TService>(
        this IContainerBuilder builder,
        ServiceLifetime lifetime,
        Func<IServiceProvider, TService> implementationFactory)
        where TService : class

    // Examples (currently we only support the following service prototypes):
    //   builder.AddServicePrototype(new ODataMessageReaderSettings { ... });
    //   builder.AddServicePrototype(new ODataMessageWriterSettings { ... });
    //   builder.AddServicePrototype(new ODataSimplifiedOptions { ... });
    public static IContainerBuilder AddServicePrototype<TService>(
        this IContainerBuilder builder,
        TService instance);

    // Examples:
    //   builder.AddDefaultODataServices();
    public static IContainerBuilder AddDefaultODataServices(this IContainerBuilder builder);
}
```

For the usage of the `AddService` overloads, please see the comments for examples. For `AddServicePrototype`, we currently only support the following service types: `ODataMessageReaderSettings`, `ODataMessageWriterSettings` and `ODataSimplifiedOptions`. This design follows the [Prototype Pattern](https://en.wikipedia.org/wiki/Prototype_pattern) where you can register a globally singleton instance (as the prototype) for each service type then you will get an individual clone per scope/request. Modifying that clone will not affect the singleton instance as well as the subsequent clones. That is to say now you don't need to clone a writer setting before editing it with the request-related information just feel safe to modify it for any specific request. 

The `AddDefaultODataServices` method registers a set of service types with default implementations that come from ODataLib. Typically you MUST call this method first on your container builder before registering any custom service. Please note that the order of registration matters! ODataLib will always use the last service implementation registered for a specific service type.

Currently the default services provided by ODataLib and expected to be overridden by users are:

|Service|Default Implementation|Lifetime|Prototype?|
|-------|---------------------|--------|----------|
|IJsonReaderFactory|DefaultJsonReaderFactory|Singleton|N|
|IJsonWriterFactory|DefaultJsonWriterFactory|Singleton|N|
|IJsonWriterFactoryAsync|DefaultJsonWriterFactory|Singleton|N|
|IStreamBasedJsonWriterFactory|N/A|Singleton|N|
|ODataMediaTypeResolver|ODataMediaTypeResolver|Singleton|N|
|ODataMessageReaderSettings|ODataMessageReaderSettings|Scoped|Y|
|ODataMessageWriterSettings|ODataMessageWriterSettings|Scoped|Y|
|ODataPayloadValueConverter|ODataPayloadValueConverter|Singleton|N|
|IEdmModel|EdmCoreModel.Instance|Singleton|N|
|ODataUriResolver|ODataUriResolver|Singleton|N|
|UriPathParser|UriPathParser|Scoped|N|
|ODataSimplifiedOptions|ODataSimplifiedOptions|Scoped|Y|

<br/>

### Build and Use the Container in ODataLib
After you have registered all the required services into the container builder, you can finally build a container from it by calling `BuildContainer` on your container builder. You will then get a container instance that implements `IServiceProvider`.

In order for ODataLib to use the registered services, the container must be passed into ODataLib through some entry points. Currently entry points in ODataLib are `ODataMessageReader`, `ODataMessageWriter` and `ODataUriParser`, which will be covered in the next two subsections.

#### Part I: Serialization and Deserialization
The way of passing container into `ODataMessageReader` and `ODataMessageWriter` is exactly the same which is through request and response messages. We are still using the interfaces `IODataRequestMessage` and `IODataResponseMessage` but now the actual implementation class (e.g., `ODataMessageWrapper` in Web API OData) must also implement `IContainerProvider`. Below is an excerpt of the `ODataMessageWrapper` class as an example if you are building an OData service directly using ODataLib.

```C#
class ODataMessageWrapper : IODataRequestMessage, IODataResponseMessage, IContainerProvider, ...
{
    ...
    public IServiceProvider Container { get; set; }
    ...
}

// Use ODataMessageWrapper to pass the container into ODataLib.
// The request container will be automatically used in ODataLib.
ODataMessageWrapper responseMessage = new ODataMessageWrapper();
responseMessage.Container = Request.GetRequestContainer();
ODataMessageWriter writer = new ODataMessageWriter(responseMessage);
// Use the writer to write the response payload.
```

After that, the container will be stored in the `Container` properties of `ODataMessageInfo`, `ODataInputContext` and `ODataOutputContext` (and their subclasses). If you are implementing a custom media type (like Avro, VCard, etc.), you can access the container through those properties. This is a very advanced and complicated scenario thus we will omit the sample here for now.

If you fail to set the `Container` in `IContainerProvider`, it will remain `null`. In this case, ODataLib will not fail internally but all services will have their default implementations and there would be NO way to replace them with custom ones. That said, if you want extensibility, please use DI :-)

##### Considerations for injecting custom JSON writers

For `ODataMessageWriter` to work correctly in both synchronous and asynchronous scenarios, it must have a single instance of an internal JSON writer that implements both `IJsonWriter` and `IJsonWriterAsync` interfaces.

This means that if you inject your own custom JSON factory (either `IJsonWriterFactory`, `IJsonWriterFactoryAsync` or `IStreamBasedJsonWriterFactory`), the returned instance must implement both `IJsonWriter` and `IJsonWriterAsync`.

The factories built-in to the library, i.e. `DefaultJsonWriterFactory` and `DefaultStreamBasedJsonWriterFactory` return writer instances that implement both `IJsonWriter` and `IJsonWriterAsync` interfaces.

If you inject a custom `IStreamBasedJsonWriterFactory` that does not implement both synchronous and async interfaces, `ODataMessageWriter` will throw an exception informing you that both interfaces must be implemented.

For backwards compatibility, we allow you to inject a custom `IJsonWriterFactory` that returns `IJsonWriter` implementation without implementing `IJsonWriterAsync`. In this case, asynchronous writing will not be supported, so you should not use the `Write***Async` methods. Bear in mind, that calling the [`container.AddDefaultODataServices()`](/dotnet/api/microsoft.odata.containerbuilderextensions.adddefaultodataservices) automatically injects a default `IJsonWriterFactoryAsync` implementation. So if you're only providing a synchronous `IJsonWriter` via a custom `IJsonWriterFactory`, then you should not call the `AddDefaultODataServices()`, otherwise `ODataMessageWriter` will end up creating two separate JSON writers (one that you injected, and the other from the default `IJsonWriterFactoryAsync` implementation) and this will also throw an exception.

#### Part II: URI Parsing
The way of passing container into URI parsers is a little bit different. You must use the constructor overloads (see below) of `ODataUriParser` that take a parameter `container` of `IServiceProvider` to do so. Using the other constructors will otherwise disable the DI support in URI parsers.

```C#
public sealed class ODataUriParser
{
    ...
    public ODataUriParser(IEdmModel model, Uri serviceRoot, Uri uri, IServiceProvider container);
    ...
    public ODataUriParser(IEdmModel model, Uri relativeUri, IServiceProvider container);
    ...
}
```

Then the container will be stored in `ODataUriParserConfiguration` and used in URI parsers. Currently `ODataUriResolver`, `UriPathParser` and `ODataSimplifiedOptions` can be overridden and will affect the behavior of URI parsers.

### Design ODataLib Features for DI
In the future, we may encounter the need in ODataLib to either move existing classes into DI container, or design new classes that work with DI. Based on the past experience about incorporating DI into ODataLib, here are some tips:

 - Eliminate constructor parameters that are of primitive types because they CANNOT be injected. If they have to be there anyway, consider injecting a factory class instead of the class itself (e.g, `IJsonReader` and `IJsonReaderFactory`).
 - Move those types (they are called *dependencies*) of the remaining constructor parameters into DI container (if they are not in it already) so that they can be injected by the DI framework automatically. If some types cannot be placed in DI container anyway, consider converting the constructors parameters of those types to class properties and using property assignment during initialization.
 - Of course it's best to use empty constructors.
 - Carefully consider the lifetime of the service. We rarely use `Transient` as it will degrade the runtime performance of GC. If you want that service to have an individual instance per request, use `Scoped`. If only one instance is required during the application lifecycle, use `Singleton`. Please also pay attention to the lifetime of your dependencies!
 - Add the service into `AddDefaultODataServices` of the `ContainerBuilderExtensions` class.

<br/>

### Adapt to Breaking Changes for DI
After upgrading to ODataLib v7.x, you might find that some parameters or properties in public APIs are missing. Don't panic! Mostly you will find it in the list (see above) of the services registered in the container. And you will also find the request container in the context. Then it's very easy to access the missing objects by calling `IServiceProvider.GetService`. Sometimes retrieving a service every time from the container might look like a performance concern though the actual cost of the DI framework is typically very low (for example, the MS DI Framework uses compiled lambda to optimize for performance). In this case, you might want to cache it in some place but please be cautious that improper caching may break the lifetime policy of the services.
