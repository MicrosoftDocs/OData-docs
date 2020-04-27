---
title: "Batch Operations"
description: ""

author: madansr7
ms.author: madansr7
ms.date: 7/1/2019
ms.topic: article
 
---
# Batch operations
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odataclient-v6.md)]

OData Client for .NET supports batch processing of requests to an OData service. This ensures that all operations in the batch are sent to the data service in a single HTTP request, enables the server to process the operations atomically, and reduces the number of round trips to the service.

OData Client for .NET doesn't support sending both query and change in one batch request.
 
## Batch Query 

To execute multiple queries in a single batch, you must create each query in the batch as a separate instance of the `DataServiceRequest<TElement>` class. The batched query requests are sent to the data service when the `ExecuteBatch` method is called. It contains the query request objects. 

This method accepts an array of `DataServiceRequest` as parameters. It returns a `DataServiceResponse` object, which is a collection of `QueryOperationResponse<T>` objects that represent responses to individual queries in the batch, each of which contains either a collection of objects returned by the query or error information. When any single query operation in the batch fails, error information is returned in the `QueryOperationResponse<T>` object for the operation that failed and the remaining operations are still executed. 

```c#

    DefaultContainer dsc = new DefaultContainer(new Uri("https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/"));
    public void BatchQuery()
    {
        var peopleQuery = dsc.People;
        var airlinesQuery = dsc.Airlines;

        var batchResponse = dsc.ExecuteBatch(peopleQuery, airlinesQuery);
        foreach(var r in batchResponse)
        {
            var people = r as QueryOperationResponse<Person>;
            if (people != null)
            {
                foreach (Person p in people)
                {
                    Console.WriteLine(p.UserName);
                }
            }

            var airlines = r as QueryOperationResponse<Airline>;
            if (airlines != null)
            {
                foreach (var airline in airlines)
                {
                    Console.WriteLine(airline.Name);
                }
            }
        }
    }

```

`ExecuteBatch` will send a "POST" request to `https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/$batch`. Each internal request contains its own http method "GET".

The payload of the request is as following:
```html

	--batch_d3bcb804-ee77-4921-9a45-761f98d32029
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	
	GET https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/People HTTP/1.1
	OData-Version: 4.0
	OData-MaxVersion: 4.0
	Accept: application/json;odata.metadata=minimal
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	
	--batch_d3bcb804-ee77-4921-9a45-761f98d32029
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	
	GET https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/Airlines HTTP/1.1
	OData-Version: 4.0
	OData-MaxVersion: 4.0
	Accept: application/json;odata.metadata=minimal
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	
	--batch_d3bcb804-ee77-4921-9a45-761f98d32029--
```
## Batch Modification 

In order to batch a set of changes to the server, `ODataServiceContext` provides `SaveChangesOptions.BatchWithSingleChangeset` and `SaveChangesOptions.BatchWithIndependentOperations` when `SaveChanges`.

`SaveChangesOptions.BatchWithSingleChangeset` will save changes in a single change set in a batch request.

`SaveChangesOptions.BatchWithIndependentOperations` will save each change independently in a batch request. 

You can refer to [odata v4.0 protocol 11.7](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part1-protocol/odata-v4.0-errata02-os-part1-protocol-complete.html#_Toc406398359) to get more details about batch request and whether requests should be contained in one change set or not.

```c#

    DefaultContainer dsc = new DefaultContainer(new Uri("https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/"));
    public void BatchModify()
    {
        dsc.MergeOption = MergeOption.PreserveChanges;
        var me = dsc.Me.GetValue();
        var myTrip = dsc.Me.Trips.First();

        me.LastName = "Test";
        myTrip.Description = "Updated Trip";

        dsc.UpdateObject(me);
        dsc.UpdateObject(myTrip);

        dsc.SaveChanges(SaveChangesOptions.BatchWithSingleChangeset);

        Console.WriteLine(me.LastName);
        Console.WriteLine(myTrip.Description);
    }
```

The payload for all requests in one change set is like following

This will send request with URL https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/$batch.

The request headers contain following two headers:
```html

	Content-Type: multipart/mixed; boundary=batch_06d8a02a-854a-4a21-8e5c-f737bbd2dea8
	Accept: multipart/mixed
```
The request Payload is as following:

```html

	--batch_06d8a02a-854a-4a21-8e5c-f737bbd2dea8
	Content-Type: multipart/mixed; boundary=changeset_b98a784d-af07-4723-9d5c-4722801f4c4d
	
	--changeset_b98a784d-af07-4723-9d5c-4722801f4c4d
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	Content-ID: 3
	
	PATCH https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/Me HTTP/1.1
	OData-Version: 4.0
	OData-MaxVersion: 4.0
	Content-Type: application/json;odata.metadata=minimal
	If-Match: W/"08D24EFA2E435C91"
	Accept: application/json;odata.metadata=minimal
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	
	{"@odata.type":"#Microsoft.OData.SampleService.Models.TripPin.Person","AddressInfo@odata.type":"#Collection(Microsoft.OData.SampleService.Models.TripPin.Location)","AddressInfo":[{"@odata.type":"#Microsoft.OData.SampleService.Models.TripPin.Location","Address":"P.O. Box 555","City":{"@odata.type":"#Microsoft.OData.SampleService.Models.TripPin.City","CountryRegion":"United States","Name":"Lander","Region":"WY"}}],"Concurrency":635657333837618321,"Emails@odata.type":"#Collection(String)","Emails":["April@example.com","April@contoso.com"],"FirstName":"April","Gender@odata.type":"#Microsoft.OData.SampleService.Models.TripPin.PersonGender","Gender":"Female","LastName":"Test","UserName":"aprilcline"}
	--changeset_b98a784d-af07-4723-9d5c-4722801f4c4d
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	Content-ID: 4
	
	PATCH https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/Me/Trips(1001) HTTP/1.1
	OData-Version: 4.0
	OData-MaxVersion: 4.0
	Content-Type: application/json;odata.metadata=minimal
	Accept: application/json;odata.metadata=minimal
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	
	{"@odata.type":"#Microsoft.OData.SampleService.Models.TripPin.Trip","Budget":3000,"Description":"Updated Trip","EndsAt":"2014-01-04T00:00:00Z","Name":"Trip in US","ShareId":"9d9b2fa0-efbf-490e-a5e3-bac8f7d47354","StartsAt":"2014-01-01T00:00:00Z","Tags@odata.type":"#Collection(String)","Tags":["Trip in New York","business","sightseeing"],"TripId":1001}
	--changeset_b98a784d-af07-4723-9d5c-4722801f4c4d--
	--batch_06d8a02a-854a-4a21-8e5c-f737bbd2dea8--
```

## Relative URIs in Batch Requests
There are instances where an odata service is behind a load balancer which usually rewrites the outer URI but not the internal URIs such that the request going out from the client looks like:

```html
	POST https://myservice.com/$batch HTTP/1.1

	--batch_xyz
	GET https://myservice.com/People HTTP/1.1
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	OData-Version: 4.0
```

BUT when it hits the load balancer it's rewritten to
```html
	POST https://loadbalancer/$batch HTTP/1.1 // changed to loadbalancer uri

	--batch_xyz
	GET https://myservice.com/People HTTP/1.1 // uri in the body remains unchanged
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	OData-Version: 4.0
```

While the odata service actually expected
```html
	POST https://loadbalancer/$batch HTTP/1.1

	--batch_xyz
	GET https://loadbalancer/People HTTP/1.1
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	OData-Version: 4.0
```

But since we cant change the internal batch uri to the load balancer uri, we instead get around the problem by using relative uri's to fix this as follows
```html
	POST https://loadbalancer/$batch HTTP/1.1

	--batch_xyz
	GET /People HTTP/1.1 // uses relative uri
	Content-Type: application/http
	Content-Transfer-Encoding: binary
	OData-Version: 4.0
```

`ExecuteBatch` and `SaveChanges` defined in `DataServiceContext` accept `SaveChangesOptions.UseRelativeUri` option which allows individual requests in batch to use Relative Uris. In this case, they will rely on the Base Uri of the top level request.

### Relative URIs in Batch Queries
We can pass the `SaveChangesOptions.UseRelativeUri` option along with other batch options to `ExecuteBatch` method as follows.

```
var batchResponse = dsc.ExecuteBatch(SaveChangesOptions.BatchWithIndependentOperations | SaveChangesOptions.UseRelativeUri, peopleQuery, airlinesQuery);
```
The same options apply to `ExecuteBatchAsync` method.

### Relative URIs in Batch Modification
We can pass the `SaveChangesOptions.UseRelativeUri` option along with other batch options to `SaveChanges` method as follows.

```
dsc.SaveChanges(SaveChangesOptions.BatchWithSingleChangeset | SaveChangesOptions.UseRelativeUri);
```
The same options apply to `SaveChangesAsync` method.

A truncated request Payload will look like this:

```html

	--batch_06d8a02a-854a-4a21-8e5c-f737bbd2dea8
	Content-Type: multipart/mixed; boundary=changeset_b98a784d-af07-4723-9d5c-4722801f4c4d
	.....
	.....
	.....

	GET /People HTTP/1.1 // Relative URI is used
	OData-Version: 4.0
	OData-MaxVersion: 4.0
	Accept: application/json;odata.metadata=minimal

	.....
	.....
	
	PATCH /Me HTTP/1.1 // Relative URI is used
	OData-Version: 4.0
	OData-MaxVersion: 4.0
	Content-Type: application/json;odata.metadata=minimal
	.....
	.....
	.....

	PATCH /Me/Trips(1001) HTTP/1.1 // Relative URI is used
	OData-Version: 4.0
	OData-MaxVersion: 4.0
	Content-Type: application/json;odata.metadata=minimal
	......
	......
	......
```
