---
title: "Get Response Content for Data Modification Requests"
description: "This tutorial describes how to get response content on client side"

author: mumbi-o
ms.author: mowambug
ms.date: 7/1/2019
ms.topic: article
 
---
# Get Response Content
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odataclient-v7.md)]

## Data modification response
We get a `Data Modification Response` after calling cache updating functions like `UpdateObject` and then `SaveChanges()` or  `SaveChangesAsync()`.

Changes are tracked in the [DataServiceContext](/dotnet/api/microsoft.odata.client.dataservicecontext) instance but not sent to the server immediately. After you are finished with the required changes for a specified activity, call [SaveChanges](/dotnet/api/microsoft.odata.client.dataservicecontext.savechanges) to submit all the changes to the data service. `SaveChanges` will send a either a "POST", “PUT”, "PATCH" or “DELETE” request. 

A [DataServiceResponse](/dotnet/api/microsoft.odata.client.dataserviceresponse) object is returned after the `SaveChanges` operation is complete. The `DataServiceResponse` object includes a sequence of OperationResponse objects that, in turn, contain a sequence of `EntityDescriptor` or `LinkDescriptor` instances that represent the changes persisted or attempted. When an entity is created or modified in the data service, the EntityDescriptor includes a reference to the updated entity, including any server-generated property values, such as the generated ID value.

When the service doesn't respond with `204 No Content` to data modification requests, the response contains a non-empty body. The code below helps to retrieve the body content:

``` csharp
static void Main(string[] args)
{
    var context = new DefaultContainer(new Uri("https://services.odata.org/v4/TripPinServiceRW/"));

    var person = Person.CreatePerson("russell", "Russell", "Whyte", new long());

    context.AddToPeople(person);

    var responses = context.SaveChanges();

    foreach (var response in responses)
    {
        var changeResponse = (ChangeOperationResponse) response;
        var entityDescriptor = (EntityDescriptor) changeResponse.Descriptor;
        var personCreated = (Person) entityDescriptor.Entity; // the person created on the service
    }
}
```

## Query Responses
We get a `Data Query Response` after calling `Execute()` or `ExecuteAsync()` on `DataServiceQueries`.
`Execute()` will send a "GET" request.

When executed, the [DataServiceQuery&lt;TElement&gt;](/dotnet/api/microsoft.odata.client.dataservicequery-1) returns an `IEnumerable<T>` of the requested entity type. This query result can be cast to a [QueryOperationResponse&lt;T&gt;](/dotnet/api/microsoft.odata.client.queryoperationresponse-1)object, as in the following example:
```csharp
var context = new DefaultContainer(new Uri("https://services.odata.org/v4/TripPinServiceRW/"));
DataServiceQuery<Person> query = context.People;
// Execute the query for all customers and get the response object.
QueryOperationResponse<Person> response =
    query.Execute() as QueryOperationResponse<Person>;
```

The entity type instances that represent entities in the data service are created on the client by a process called object materialization. `The QueryOperationResponse<T>` object implements `IEnumerable<T>` to provide access to the results of the query.

The `QueryOperationResponse<T>` also has the following members that enable you to access additional information about a query result:

- [Error](/dotnet/api/microsoft.odata.client.operationresponse.error) - gets an error thrown by the operation, if any has occurred.
- [Headers](/dotnet/api/microsoft.odata.client.operationresponse.headers)- contains the collection of HTTP response headers associated with the query response.
- [Query](/dotnet/api/microsoft.odata.client.queryoperationresponse.query) - gets the original `DataServiceQuery<TElement>` that generated the `QueryOperationResponse<T>`.
- [StatusCode](/dotnet/api/microsoft.odata.client.operationresponse.statuscode) - gets the HTTP response code for the query response.
- [TotalCount](/dotnet/api/microsoft.odata.client.queryoperationresponse.totalcount) - gets the total number of entities in the entity set when the `IncludeTotalCount` method was called on the `DataServiceQuery<TElement>`.
- [GetContinuation](/dotnet/api/microsoft.odata.client.queryoperationresponse.getcontinuation) - returns a `DataServiceQueryContinuation` object that contains the URI of the next page of results.