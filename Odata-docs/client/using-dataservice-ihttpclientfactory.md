---
title: "Using DataserviceContext IHttpClientFactory in Microsoft.OData.Client"
description: "This tutorial provides guidance on using IHttpClientFactory with OData Client in a .NET application."

author: WanjohiSammy
ms.author: swanjohi
ms.date: 11/29/2024
ms.topic: article
 
---

# Using DataserviceContext IHttpClientFactory in Microsoft.OData.Client

Ever wanted to use `HttpClient` with [`Microsoft.OData.Client`](/odata/client/getting-started)? Now you can! In this post, we'll show you how to do it easily and highlight the benefits.

## Why Use IHttpClientFactory?

[`IHttpClientFactory`](/dotnet/core/extensions/httpclient-factory) is a feature in .NET Core that helps you manage `HttpClient` instances. Here are some benefits:
- **Automatic Management**: It handles the lifecycle of HttpClient instances, preventing issues like socket exhaustion.
- **Configuration**: You can set up policies like retries and timeouts.
- **Dependency Injection**: It integrates with .NET Core's DI system, making it easy to use in your services.

## Getting Started

To use **HttpClient** with `Microsoft.OData.Client`, you can use the `HttpClientFactory` property of `DataServiceContext`. This lets you inject your custom HttpClient instance.

### 1. Using Dependency Injection

You can use the [`IHttpClientFactory with .NET`](/dotnet/core/extensions/dependency-injection) to configure and inject HttpClient instances. 

> **Note that we use an `empty string` here since `OData Client` does not detect `named clients`.**

Here's how:

```cs
var services = new ServiceCollection();

services.AddHttpClient("", client => // We should use an empty string here since OData Client does not detect named clients.
{
    client.Timeout = TimeSpan.FromSeconds(160);
});

var serviceProvider = services.BuildServiceProvider();
var httpClientFactory = serviceProvider.GetRequiredService();

var context = new Container(new Uri("https://localhost:7214/odata"))
{
    HttpClientFactory = httpClientFactory
};
```

### 2. Create a Custom HttpClientFactory

Alternatively, you can create a custom HttpClientFactory:

```cs
public class CustomHttpClientFactory : IHttpClientFactory
{
    private readonly HttpClient _httpClient;

    public CustomHttpClientFactory(HttpClient httpClient)
    {
        _httpClient = httpClient;
    }

    public HttpClient CreateClient(string name)
    {
        return _httpClient;
    }
}
```

Next, create an HttpClient instance and set the timeout:

```cs
var httpClient = new HttpClient
{
    Timeout = TimeSpan.FromSeconds(160)
};

var httpClientFactory = new CustomHttpClientFactory(httpClient);

var context = new Container(new Uri("https://localhost:7214/odata"))
{
    HttpClientFactory = httpClientFactory
};
```

## Additional Resources

For more details, check out:
- [Breaking changes in Microsoft.OData.Client](https://devblogs.microsoft.com/odata/odata-net-8-preview-release/#breaking-changes-in-microsoft.odata.client)
- [Use HttpClient in OData Client](/odata/client/using-httpclient)

## Conclusion
Using IHttpClientFactory with Microsoft.OData.Client makes managing HttpClient instances easier and more efficient. Whether you create a custom HttpClientFactory or use the DI system, integrating HttpClient with your OData client is straightforward.

Happy coding!