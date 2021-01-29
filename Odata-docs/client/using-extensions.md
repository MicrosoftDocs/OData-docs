---
title: "Use Extensions in OData Client"
description: "This tutorial describes how to use Extensions in OData Client"

author: mumbi-o
ms.author: mowambug
ms.date: 10/10/2019
ms.topic: article
 
---
# Use Extensions in OData Client

**Applies To**: [!INCLUDE[appliesto-odataclient](../includes/appliesto-odataclient-v7.md)]

An IODataClientFactory can be registered and used to configure and create OData Client instances in an app. It offers the following benefits:

* Provides a central location for naming and configuring logical OData Client instances.
* Introduced middleware via delegating handlers in OData Client and provides default extensions for Http Client Factory to take advantage of that.
* Separated concern of configuration and usage of IODataClientFactory, which allowed library owner to only depends on IODataClientFactory abstraction while deferred the actual configuration to service owner.
* Adds a configurable logging experience (via ILogger) for all requests sent through clients created by the default factory with HttpClient bridge.

In this session, we will dive into how to use Extensions in OData client request. It leverage the generated client entity, and internally it used the same hook mechanism in OData client which has been introduced in [Client Hooks in OData Client](/odata/client/v7/using-hooks), exposed it via OData client handler with a built-in handler to bridge to asp.net core HttpClientFactory.

## Prerequisites

### Client Extensions Nuget
Library owner that uses OData client factory require installation of the [Microsoft.OData.Extensions.Client.Abstractions](https://www.nuget.org/packages/Microsoft.OData.Extensions.Client.Abstractions/) NuGet package.

Service owner that also wants to configure the client factory needs to additionally include [Microsoft.OData.Extensions.Client](https://www.nuget.org/packages/Microsoft.OData.Extensions.Client/) NuGet package.

### Generated Client Proxy

OData client factory assume the client proxy is used to do communication, see [Client code gen tool](/odata/client/v7/code-generation-tool) for details on how to generate client proxy file for an OData Service.

## Basic Usage

The IODataClientFactory can be registered by calling the AddODataClient extension method on the IServiceCollection, inside the Startup.ConfigureServices method.

``` csharp
services.AddODataClient();
```

Once registered, code can accept an IODataClientFactory anywhere services can be injected with dependency injection (DI). The IODataClientFactory can be used to create a generated client proxy instance, then follow [Basic CRUD Operations](/odata/client/v7/basic-crud-operations) to use client proxy in business logic:

``` csharp
public class PeopleController : ODataController
{
    private readonly IODataClientFactory _clientFactory;

    public PeopleController(IODataClientFactory clientFactory)
    {
        _clientFactory = clientFactory;
    }

    [EnableQuery]
    public IEnumerable<Person> Get()
    {
        var client = _clientFactory.CreateClient<DefaultContainer>();
        var people = client.People.Execute();
        return people;
    }
}

```

Using IODataClientFactory in this fashion is a good way to refactor an existing app. It has no impact on the way generated client proxy is used. In places where client proxy instances are currently created, replace those occurrences with a call to CreateClient together with the actual client proxy type.

## Named clients

If an app requires many distinct uses of OData client proxy, each with a different configuration, an option is to use named clients. Configuration for a named client can be specified during registration in Startup.ConfigureServices.

``` csharp
services.AddODataClient("TripPin")
    .ConfigureODataClient(dsc =>
    {
        dsc.BaseUri = new Uri("https://services.odata.org/v4/(S(lqbvtwide0ngdev54adgc0lu))/TripPinServiceRW/");

        // Github API versioning
        dsc.Configurations.Properties.Add("User-Agent", "ODataClientFactory-Sample"));
    });
```

In the preceding code, AddODataClient is called, providing the name client for TripPin. This client has some default configuration applied—namely the base address and one headers.

Each time CreateClient is called, a new instance of OData client proxy is created and the configuration action is called.

To consume a named client, a string parameter can be passed to CreateClient. Specify the name of the client to be created:

``` csharp
public class PeopleController : ODataController
{
    private readonly IODataClientFactory _clientFactory;

    public PeopleController(IODataClientFactory clientFactory)
    {
        _clientFactory = clientFactory;
    }

    [EnableQuery]
    public IEnumerable<Person> Get()
    {
        var client = _clientFactory.CreateClient<DefaultContainer>("TripPin");
        var people = client.People.Execute();
        return people;
    }
}
```

## OData Client Handler

OData client handler is the new way of extend OData client function for library owner, it has only one method of OnClientCreated where you could get the logical name of the client and the instance of client proxy.

With the access to the instance of client proxy, you could then modify many aspect of the client behavior, especially through Configurations property of DataServiceClientConfigurations type to hook up with internals of OData client. See [Client Hooks in OData Client](/odata/client/v7/using-hooks) for more details on available hook point.

``` csharp
    /// <summary>
    /// A single handler that can alter the behavior of odata client
    /// </summary>
    public interface IODataClientHandler
    {
        /// <summary>
        /// Called after IODataClientFactory.CreateClient(string)
        /// If multiple handlers are registered, then they are called in reverse order of registration.
        /// </summary>
        /// <param name="args">the new instance of client proxy generated.</param>
        void OnClientCreated(ClientCreatedArgs args);
    }
```

Here is a sample handler that output the version information and count the request.

``` csharp
    public class VerificationODataClientHandler : IODataClientHandler
    {
        private int counter;

        public VerificationODataClientHandler()
        {
            this.counter = 0;
        }

        public void OnClientCreated(ClientCreatedArgs args)
        {
            var client = args.ODataClient;
            Console.WriteLine($"MaxProtocolVersion = {client.MaxProtocolVersion}");

            // set header and serialization type
            client.SendingRequest2 += (s, e) =>
            {
                client.Format.UseJson();
            };

            client.ReceivingResponse += (sender, eventArgs) =>
            {
                Console.WriteLine(eventArgs.ResponseMessage.GetHeader("OData-Version"));
            };

            Interlocked.Increment(ref this.counter);
        }
    }
```

And here is the code to register it in Asp.net Core Startup.ConfigureServices.

``` csharp
services.AddODataClient("TripPin")
    .AddODataClientHandler<VerificationODataClientHandler>();
```

## Bridge to IHttpClientFactory

OData client enables developers to customize request message, and use it in `DataServiceContext.Configurations.RequestPipeline.OnMessageCreating`. This function will be triggered when creating request message. It will return an `IODataRequestMessage`, See [Use HttpClient in OData Client](/odata/client/v7/using-httpclient)

Build on top of that and OData Client Handler, the OData Client Extensions now provides a default integration with IHttpClientFactory that is working out of box when you use Microsoft.OData.Extensions.Client NuGet package.

here is the code to register it in Startup.ConfigureServices to use HttpClient as the communication mechanism.

``` csharp
services.AddODataClient("TripPin")
    .AddHttpClient();
```

AddHttpClient extensions method return a IHttpClientBuilder instance, which you could use to further chain other handler. For example, you could then use following to integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly) to add retry logic.

``` csharp
services.AddODataClient("TripPin")
    .AddHttpClient()
    .AddTransientHttpErrorPolicy(p => 
        p.WaitAndRetryAsync(3, _ => TimeSpan.FromMilliseconds(600)));
```

In the preceding code, a WaitAndRetryAsync policy is defined. Failed requests are retried up to three times with a delay of 600 ms between attempts.

## Unit Test Support

With the introduction of OData client factory and associated bridge to IHttpClientFactory, now it's possible to mock the OData cient request without real network call.

``` csharp
using Moq;
using Moq.Protected;

[TestMethod]
public void TestGetHappyCase()
{
    var mockHttpMessageHandler = new Mock<HttpMessageHandler>();
            mockHttpMessageHandler.Protected()
                .Setup<Task<HttpResponseMessage>>("SendAsync", ItExpr.IsAny<HttpRequestMessage>(), ItExpr.IsAny<CancellationToken>())
                .Returns(new HttpResponseMessage()
            {
                StatusCode = HttpStatusCode.OK,
                Content = new StringContent(@"{
  ""@odata.context"": ""http://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/$metadata#People"",
  ""@odata.nextLink"": ""https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/People?%24skiptoken=8"",
  ""value"": [
    {
      ""@odata.id"": ""http://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/People('keithpinckney')"",
      ""@odata.etag"": ""W/\""08D751CC35005AA0\"""",
      ""@odata.editLink"": ""http://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/People('keithpinckney')"",
      ""UserName"": ""keithpinckney"",
      ""FirstName"": ""Keith"",
      ""LastName"": ""Pinckney"",
      ""Emails"": [ ""Keith@example.com"", ""Keith@contoso.com"" ],
      ""AddressInfo"": [],
      ""Gender"": ""Male"",
      ""Concurrency"": 637067809800608416
    }
  ]
}")});

    var sc = new ServiceCollection();
    sc.
    .AddODataClient("TripPin")
    .AddHttpClient()
    .ConfigurePrimaryHttpHandler(() => mockHttpMessageHandler.Object);

    var sp = sc.Build();

    var controller = new PeopleController(sp.GetService<IODataClientFactory>());
    var people = controller.Get().ToList();
    people.Count.Should().Be(1);
    people[0].UserName.Should().Be("keithpinckney");
}

```

In the above example, it demonstrated a unit test that simulate a happy case scenario where the OData client call returned 1 item.

You could also use similar approach to test other unhappy cases, for example, use below code to test the behavior when the dependency OData service is unavailable.
``` csharp
    var mockHttpMessageHandler = new Mock<HttpMessageHandler>();
            mockHttpMessageHandler.Protected()
                .Setup<Task<HttpResponseMessage>>("SendAsync", ItExpr.IsAny<HttpRequestMessage>(), ItExpr.IsAny<CancellationToken>())
                .Returns(new HttpResponseMessage()
            {
                StatusCode = HttpStatusCode.ServiceUnavailable
            )});

```
