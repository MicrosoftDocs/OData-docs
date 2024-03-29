---
title : "Batch Support"
description: Learn how to use batch requests to optimize and improve scalability.
ms.date: 7/1/2019
author: madansr7
---
# Batch Support

**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

Batch requests allow grouping multiple operations into a single HTTP request payload and the service will return a single HTTP response with the response to all operations in the requests. This way, the client can optimize calls to the server and improve the scalability of its service.

Please refer to [OData Protocol](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part1-protocol/odata-v4.0-errata02-os-part1-protocol-complete.html#_Toc406398359) for more detail about batch, and [Batch in ODL](/odata/client/batch-operations) for batch in ODL client.

## Enable Batch in Web API OData Service

It is very easy to enable batch in an OData service which is built by Web API OData.

### Add Batch Handler

```C#
public static void Register(HttpConfiguration config)
{
    var odataBatchHandler = new DefaultODataBatchHandler(GlobalConfiguration.DefaultServer);
    config.MapODataServiceRoute("odata", "odata", GetModel(), odataBatchHandler);
}
```

As above, we only need to create a new batch handler and pass it when mapping routing for OData service. Batch will be enabled. 

For testing, we can POST a request with batch body to the baseurl/$batch:

```html
    POST https://localhost:14409/odata/$batch HTTP/1.1
    User-Agent: Fiddler
    Host: localhost:14409
    Content-Length: 1244
    Content-Type: multipart/mixed;boundary=batch_d3bcb804-ee77-4921-9a45-761f98d32029
    
    --batch_d3bcb804-ee77-4921-9a45-761f98d32029
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    GET https://localhost:14409/odata/Products(0)  HTTP/1.1
    OData-Version: 4.0
    OData-MaxVersion: 4.0
    Accept: application/json;odata.metadata=minimal
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    
    --batch_d3bcb804-ee77-4921-9a45-761f98d32029
    Content-Type: multipart/mixed;boundary=changeset_77162fcd-b8da-41ac-a9f8-9357efbbd
    
    --changeset_77162fcd-b8da-41ac-a9f8-9357efbbd 
    Content-Type: application/http 
    Content-Transfer-Encoding: binary 
    Content-ID: 1
    
    DELETE https://localhost:14409/odata/Products(0) HTTP/1.1
    OData-Version: 4.0
    OData-MaxVersion: 4.0
    Accept: application/json;odata.metadata=minimal
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    
    --changeset_77162fcd-b8da-41ac-a9f8-9357efbbd--
    --batch_d3bcb804-ee77-4921-9a45-761f98d32029
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    GET https://localhost:14409/odata/Products  HTTP/1.1
    OData-Version: 4.0
    OData-MaxVersion: 4.0
    Accept: application/json;odata.metadata=minimal
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    
    --batch_d3bcb804-ee77-4921-9a45-761f98d32029--
```

And the response should be:

```html
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Type: multipart/mixed; boundary=batchresponse_5667121d-ca2f-458d-9bae-172f04cdd411
    Expires: -1
    Server: Microsoft-IIS/8.0
    OData-Version: 4.0
    X-AspNet-Version: 4.0.30319
    X-SourceFiles: =?UTF-8?B?YzpcdXNlcnNcbGlhbndcZG9jdW1lbnRzXHZpc3VhbCBzdHVkaW8gMjAxM1xQcm9qZWN0c1xUZXN0V2ViQVBJUmVsZWFzZVxUZXN0V2ViQVBJUmVsZWFzZVxvZGF0YVwkYmF0Y2g=?=
    X-Powered-By: ASP.NET
    Date: Wed, 06 May 2015 07:34:29 GMT
    Content-Length: 1449
    
    --batchresponse_5667121d-ca2f-458d-9bae-172f04cdd411
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    HTTP/1.1 200 OK
    Content-Type: application/json; odata.metadata=minimal; charset=utf-8
    OData-Version: 4.0
    
    {
      "@odata.context":"https://localhost:14409/odata/$metadata#Products/$entity","ID":0,"Name":"0Name"
    }
    --batchresponse_5667121d-ca2f-458d-9bae-172f04cdd411
    Content-Type: multipart/mixed; boundary=changesetresponse_e2f20275-a425-404a-8f01-c9818aa63610
    
    --changesetresponse_e2f20275-a425-404a-8f01-c9818aa63610
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    Content-ID: 1
    
    HTTP/1.1 204 No Content
    
    
    --changesetresponse_e2f20275-a425-404a-8f01-c9818aa63610--
    --batchresponse_5667121d-ca2f-458d-9bae-172f04cdd411
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    HTTP/1.1 200 OK
    Content-Type: application/json; odata.metadata=minimal; charset=utf-8
    OData-Version: 4.0
    
    {
      "@odata.context":"https://localhost:14409/odata/$metadata#Products","value":[
        {
          "ID":1,"Name":"1Name"
        },{
          "ID":2,"Name":"2Name"
        },{
          "ID":3,"Name":"3Name"
        },{
          "ID":4,"Name":"4Name"
        },{
          "ID":5,"Name":"5Name"
        },{
          "ID":6,"Name":"6Name"
        },{
          "ID":7,"Name":"7Name"
        },{
          "ID":8,"Name":"8Name"
        },{
          "ID":9,"Name":"9Name"
        }
      ]
    }
    --batchresponse_5667121d-ca2f-458d-9bae-172f04cdd411--
```

### Setting Batch Quotas
DefaultODataBatchHandler contains some configuration, which can be set by customers, to customize the handler. For example, the following code will only allow a maximum of 8 requests per batch and 5 operations per ChangeSet.

```C#
var odataBatchHandler = new DefaultODataBatchHandler(GlobalConfiguration.DefaultServer);
odataBatchHandler.MessageQuotas.MaxPartsPerBatch = 8;
odataBatchHandler.MessageQuotas.MaxOperationsPerChangeset = 5;
```

## Enable/Disable continue-on-error in Batch Request

We can handle the behavior upon encountering a request within the batch that returns an error by preference `odata.continue-on-error`, which is specified by [OData V4 spec](https://docs.oasis-open.org/odata/odata/v4.0/errata02/os/complete/part1-protocol/odata-v4.0-errata02-os-part1-protocol-complete.html#_Toc406398236).

### Enable Preference `odata.continue-on-error`

Preference `odata.continue-on-error` makes no sense by default, and service returns the error for that request and **continue processing additional requests within the batch as default behavior**.

To enable `odata.continue-on-error`, please refer to section [4.20 Prefer odata.continue-on-error](/odata/webapi/continue-on-error) for details.

### Request Without Preference `odata.continue-on-error`

For testing, we can POST a batch request without Preference `odata.continue-on-error`:

```html

	POST https://localhost:9001/DefaultBatch/$batch HTTP/1.1
	Accept: multipart/mixed
	Content-Type: multipart/mixed; boundary=batch_abbe2e6f-e45b-4458-9555-5fc70e3aebe0
	Host: localhost:9001
	Content-Length: 633
	Expect: 100-continue
	Connection: Keep-Alive

	--batch_abbe2e6f-e45b-4458-9555-5fc70e3aebe0
	Content-Type: application/http
	Content-Transfer-Encoding: binary

	GET https://localhost:9001/DefaultBatch/DefaultBatchCustomer(0) HTTP/1.1

	--batch_abbe2e6f-e45b-4458-9555-5fc70e3aebe0
	Content-Type: application/http
	Content-Transfer-Encoding: binary

	GET https://localhost:9001/DefaultBatch/DefaultBatchCustomerfoo HTTP/1.1

	--batch_abbe2e6f-e45b-4458-9555-5fc70e3aebe0
	Content-Type: application/http
	Content-Transfer-Encoding: binary

	GET https://localhost:9001/DefaultBatch/DefaultBatchCustomer(1) HTTP/1.1

	--batch_abbe2e6f-e45b-4458-9555-5fc70e3aebe0--

The response should be:

	HTTP/1.1 200 OK
	Content-Length: 820
	Content-Type: multipart/mixed; boundary=batchresponse_b49114d7-62f7-450a-8064-e27ef9562eda
	Server: Microsoft-HTTPAPI/2.0
	OData-Version: 4.0
	Date: Wed, 12 Aug 2015 02:23:10 GMT

	--batchresponse_b49114d7-62f7-450a-8064-e27ef9562eda
	Content-Type: application/http
	Content-Transfer-Encoding: binary

	HTTP/1.1 200 OK
	Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
	OData-Version: 4.0

	{
	  "@odata.context":"https://localhost:9001/DefaultBatch/$metadata#DefaultBatchCustomer/$entity","Id":0,"Name":"Name 0"
	}
	--batchresponse_b49114d7-62f7-450a-8064-e27ef9562eda
	Content-Type: application/http
	Content-Transfer-Encoding: binary

	HTTP/1.1 404 Not Found
	Content-Type: application/json; charset=utf-8

	{"Message":"No HTTP resource was found that matches the request URI 'https://localhost:9001/DefaultBatch/DefaultBatchCustomerfoo'.","MessageDetail":"No route data was found for this request."}
	--batchresponse_b49114d7-62f7-450a-8064-e27ef9562eda--
```
Service returned error and stop processing.

### Request With Preference `odata.continue-on-error`

Now POST a batch request with Preference `odata.continue-on-error`:

```html

	POST https://localhost:9001/DefaultBatch/$batch HTTP/1.1
	Accept: multipart/mixed
	prefer: odata.continue-on-error
	Content-Type: multipart/mixed; boundary=batch_abbe2e6f-e45b-4458-9555-5fc70e3aebe0
	Host: localhost:9001
	Content-Length: 633
	Expect: 100-continue
	Connection: Keep-Alive

	--batch_abbe2e6f-e45b-4458-9555-5fc70e3aebe0
	Content-Type: application/http
	Content-Transfer-Encoding: binary

	GET https://localhost:9001/DefaultBatch/DefaultBatchCustomer(0) HTTP/1.1

	--batch_abbe2e6f-e45b-4458-9555-5fc70e3aebe0
	Content-Type: application/http
	Content-Transfer-Encoding: binary

	GET https://localhost:9001/DefaultBatch/DefaultBatchCustomerfoo HTTP/1.1

	--batch_abbe2e6f-e45b-4458-9555-5fc70e3aebe0
	Content-Type: application/http
	Content-Transfer-Encoding: binary

	GET https://localhost:9001/DefaultBatch/DefaultBatchCustomer(1) HTTP/1.1

	--batch_abbe2e6f-e45b-4458-9555-5fc70e3aebe0--

Service returns the error for that request and continue processing additional requests within the batch:

	HTTP/1.1 200 OK
	Content-Length: 1190
	Content-Type: multipart/mixed; boundary=batchresponse_60fec4c2-3ce7-4900-a05a-93f180629a11
	Server: Microsoft-HTTPAPI/2.0
	OData-Version: 4.0
	Date: Wed, 12 Aug 2015 02:27:45 GMT

	--batchresponse_60fec4c2-3ce7-4900-a05a-93f180629a11
	Content-Type: application/http
	Content-Transfer-Encoding: binary

	HTTP/1.1 200 OK
	Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
	OData-Version: 4.0

	{
	  "@odata.context":"https://localhost:9001/DefaultBatch/$metadata#DefaultBatchCustomer/$entity","Id":0,"Name":"Name 0"
	}
	--batchresponse_60fec4c2-3ce7-4900-a05a-93f180629a11
	Content-Type: application/http
	Content-Transfer-Encoding: binary

	HTTP/1.1 404 Not Found
	Content-Type: application/json; charset=utf-8

	{"Message":"No HTTP resource was found that matches the request URI 'https://localhost:9001/DefaultBatch/DefaultBatchCustomerfoo'.","MessageDetail":"No route data was found for this request."}
	--batchresponse_60fec4c2-3ce7-4900-a05a-93f180629a11
	Content-Type: application/http
	Content-Transfer-Encoding: binary

	HTTP/1.1 200 OK
	Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
	OData-Version: 4.0

	{
	  "@odata.context":"https://localhost:9001/DefaultBatch/$metadata#DefaultBatchCustomer/$entity","Id":1,"Name":"Name 1"
	}
	--batchresponse_60fec4c2-3ce7-4900-a05a-93f180629a11--
```
