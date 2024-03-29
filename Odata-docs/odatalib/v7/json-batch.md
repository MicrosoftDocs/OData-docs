---
title: "Json batch format in ODataLib"
description: "This section describes how to use batch operations in the OData core library."
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# JSON Batching 
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

## Overview
Similar to Windows batch command, OData supports grouping multiple requests together, sending a single HTTP request to OData service, and grouping corresponding responses together before sending back as a single HTTP response. The major benefits for request batching are to reduce client/server round trips and to support atomic operation.

The batch format supported by OData core libraries is multipart/mime for OData protocol up to v4.0. To accommodate the need for a more developer-friendly format, the new JSON format batching support is added to the latest version of OData protocol v4.01. The JSON format batching format also brings another major benefit of allowing requests inside a batch to be executed in specified orders.

The OData v4.01 protocol specification can be found on OASIS site. The most current version is: https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part1-protocol.html#_Toc499805328.

Details of the JSON Batch format can be found in the OData JSON Format v4.01 specification on the OASIS site..  The most current version is: https://docs.oasis-open.org/odata/odata-json-format/v4.01/odata-json-format-v4.01.html#_Toc499716905

## Sample Batch Request in JSON Format
Here is one sample batch request in JSON format (unnecessary details are omitted for sake of brevity):

```json 
	https://localhost:9000/$batch
	User-Agent: Fiddler
	Authorization: <authz token>
	Content-Type: application/json
	Accept: application/json
	Host: localhost:9000
	Content-Length: 1234

	{
	  "requests": [
		{
		  "method": "POST",
		  "atomicityGroup": "g1",
		  "url": "https://localhost:9000/users",
		  "headers": {
			"content-type": "application/json; odata.metadata=minimal; odata.streaming=true",
			"odata-version": "4.0"
		  },
		  "id": "g1-r1",
		  "body": {"upn": "u1@domain.com", "givenName": "Jon", "surname": "Doe"}
		},
		{
		  "id": "r2",
		  "method": "PATCH",
		  "url": "https://localhost:9000/users('u2@domain.com')",
		  "headers": {
			"content-type": "application/json; odata.metadata=minimal; odata.streaming=true",
			"odata-version": "4.0"
		  },
		  "body": {"givenName":"Jonx"}
		},
		{
		  "id": "g2-r5",
		  "atomicityGroup": "g2",
		  "method": "POST",
		  "url": "https://localhost:9000/users",
		  "headers": {
			"content-yype": "application/json; odata.metadata=minimal; odata.streaming=true",
			"odata-version": "4.0"
		  },
		  "body": {"upn": "u5@domain.com", "givenName": "Jon", "surname": "Doe"}
		},
		{
		  "id": "g2-r6",
		  "atomicityGroup": "g2",
		  "method": "POST",
		  "url": "https://localhost:9000/users",
		  "headers": {
			"content-type": "application/json; odata.metadata=minimal; odata.streaming=true",
			"odata-version": "4.0"
		  },
		  "body": {"upn": "u6@domain.com", "givenName": "Jon", "surname": "Doe"}
		},
		{
		  "id": "r8",
		  "method": "get",
		  "url": "https://localhost:9000/users",
		  "headers": {
			"content-type": "application/json; odata.metadata=minimal; odata.streaming=true",
			"odata-version": "4.0"
		  },
		  "dependsOn": ["g1", "g2", "r2"]
		}
	  ]
	}
```

Significant attributes for the JSON batch request are as follow:

* payload is in JSON format, an expansion to the original multipart/mixed format.
* A JSON batch request has only one top level property named `requests`, whose value is an array containing multiple requests and multiple atomic groups, each of which can contain multiple requests.
* Request headers `Content-Type` and `Accept` designates the batch request and response format to be JSON batching. For comparison, in `multipart/mime` format these two headers are` Multipart/Mixed` with optional content type parameters. These are the header values driving the batch processing while achieving ODL compatibility of two batch formats. Typical use case is that request & response are of the same format, but it should not be a limitation for the ODL Core library batching design, as long as the header values are consistent with the payload content in the request & response.
* `Content-Length` header of the batch request specifies the content length of the JSON batch request payload.
* Requests belonged to same atomic group are denoted by the `atomicityGroup` attribute having the same value, e.g. "g2". JSON batch format requires that all requests belonged to same group must be adjacent in the `requests` array.
* The `dependsOn` attribute can be used to specify an array of request prerequisites of this request. Here are the important rules for `dependsOn` derived from the spec for JSON Batch semantics:
    * If request URL references an entity from another request, id of the corresponding request must also be included;
	* Forward referencing is not allowed;
	* Referencing id of request that is part of a group is not allowed, with the exception that the referenced requests are in the same group of this request. 

In the example above, with JSON batch format, this request specifies the following:
* Contains two atomic groups `g1` and `g2`;
* Request `g1-r1` needs to be executed before `g2-r6`, which needs to be before `r8`;
* Request `r2` can be executed in arbitrary order;
* Request `g2-r5` can be executed either before or after `g1`, but needs to be executed before `r8`. Additionally, since `g2-r5` and `g2-6r` belong to the same atomic group `g2`, execution results need to be roll-backed if either of them failed (service specific implementation).

## Typical JSON Batch Request Creation using OData-Core library
With user-defined EDM model, user can use ODL-Core library to create JSON batch request directly, similar to Multipart/Mixed batch creation. For example:

```C#  
		// The following code snippet generates JSON batch payload into a memory stream. 
		MemoryStream stream = new MemoryStream();
		IODataRequestMessage requestMessage = CreateRequestMessage(stream);
		requestMessage.SetHeader("Content-Type", "application/json");
		ODataMessageWriterSettings settings = new ODataMessageWriterSettings();
	
		using (ODataMessageWriter messageWriter = new ODataMessageWriter(requestMessage, settings))
		{
			ODataBatchWriter batchWriter = messageWriter.CreateODataBatchWriter();
			batchWriter.WriteStartBatch();
			string createRequestId = "contentId-1";
	                
			batchWriter.WriteStartChangeset();
	                
			ODataBatchOperationRequestMessage createOperationMessage = batchWriter.CreateOperationRequestMessage(
				"POST",
				new Uri(string.Format(CultureInfo.InvariantCulture, "{0}/{1}", serviceDocumentUri, "MySingleton")),
				createRequestId);
			using (ODataMessageWriter operationMessageWriter = new ODataMessageWriter(createOperationMessage))
			{
				ODataWriter entryWriter = operationMessageWriter.CreateODataResourceWriter();
				ODataResource entry = new ODataResource();
	                    
				// skipped customized data population for initialization here... 
	                    
				entryWriter.WriteStart(entry);
				entryWriter.WriteEnd();
			}
	
			// A PATCH operation that depends on the preceding POST operation.
			List<string> dependsOnIds = new List<string> { createRequestId };
			ODataBatchOperationRequestMessage updateOperationMessage = batchWriter.CreateOperationRequestMessage(
				"PATCH",
				new Uri(string.Format(CultureInfo.InvariantCulture, "{0}/{1}", serviceDocumentUri, "$" + createRequestId)),
				"updateReuqestId-1",
				BatchPayloadUriOption.AbsoluteUri,
				dependsOnIds);
			using (ODataMessageWriter operationMessageWriter = new ODataMessageWriter(updateOperationMessage))
			{
				var entryWriter = operationMessageWriter.CreateODataResourceWriter();
				var entry = new ODataResource();
	                    
				// skipped customized data population for update here... 
	                    
				entryWriter.WriteStart(entry);
				entryWriter.WriteEnd();
			}
	
			batchWriter.WriteEndChangeset();
	
			ODataBatchOperationRequestMessage queryOperationMessage = batchWriter.CreateOperationRequestMessage(
				"GET",
				new Uri(string.Format(CultureInfo.InvariantCulture, "{0}/{1}", serviceDocumentUri, "MySingleton")),
				"readOperationId-1");
			queryOperationMessage.SetHeader("Accept", "application/json;odata.metadata=full");
	
			batchWriter.WriteEndBatch();
		}
		// stream contains the JSON batch request payload.
```

Note that in JSON batch, `dependsOnIds` needs to include the request Id being referenced in the request's URL, as required by the JSON batch semantics.

## Typical JSON Batch Request/Response Processing by an OData Service

An `ODataBatchReader` can be instantiated to process the batch request as follows:

```C#

    ODataMessageReader odataMessageReader = new ODataMessageReader(odataRequestMessage, messageReaderSettings, model);
    ODataBatchReader odataBatchReader = odataMessageReader.CreateODataBatchReader();
```

Note that batch format is detected during the instantiation of `ODataBatchReader` from the batch request header. For Content-Type of `application/json` with optional parameters, an instance of `ODataJsonLightBatchReader` is created, while an `ODataMultipartMixedBatchReader` object is created for `Multipart/Mixed` content type with optional parameters.
    
```C#    
    ODataMessageWriter batchResponseMessageWriter = new ODataMessageWriter(odataResponseMessage, odataMessageWriterSettings, model);
    ODataBatchWriter batchWriter = batchResponseMessageWriter.CreateODataBatchWriter();
```    

Similar to the request message writer, the response message writer above also sets up the proper `ODataPayloadKind` for Batch processing.

Now, start the basic batch processing with request reading / response writing. For batch processing, the request processing (including reading, dispatching) and response writing (including processing responses for individual request, writing batch response) are interleaving and the whole process is driven by OData service using specific call patterns as described below (common for both formats).

Typically ODataBatchReader is started with initial state, so we can start with creating the response envelop, followed by readings & writings.

```C#
    
        batchWriter.WriteStartBatch();

        while (batchReader.Read())
        {
            switch (batchReader.State)
            {
                case ODataBatchReaderState.Operation:

                    // Create operation request message
                    ODataBatchOperationRequestMessage operationRequestMessage = batchReader.CreateOperationRequestMessage();

                    // Create operation response message
                    IODataResponseMessage responseMessage = batchWriter.CreateOperationResponseMessage(operationRequestMessage.currentContentId);    

                    // Request processing begins: including request body reader creation, request parsing & execution according to dependencies specified etc. belonged
                    // to implementation of specific services.
                    if (operationMessage.Method == "PUT")
                    {
                        // Processing request payload.
                        using (ODataMessageReader operationMessageReader = new ODataMessageReader(
                            operationMessage, new ODataMessageReaderSettings(), this.userModel))
                        {
                            ODataReader reader = operationMessageReader.CreateODataResourceReader();
								
                        // Reader reads data from request payload and kick off service backend processing.
                            ...
                        }

                        // Query various attributes for this operation request such as request dependencies and atomic group associated
                        // and combine with specific processing of the service.
                        MyProcessingForExecutionOrders(operationMessage.DependsOnIds);
                        MyProcessingForAtomicTransaction(operationMessage.GroupId);
                        // ...
                        
                        // Response processing begins: including response writing (depends on whether it is associated with an atomic group) etc. to 
                        // write back to the HTTP response belonged to implementation of specific services.

                        response.StatusCode = 201;
                        response.SetHeader("Content-Type", "application/json;odata.metadata=none");
                        ...
                        // Response processing ends.
                    }
                    else if (operationMessage.Method == "PATCH")
                    {
                        // Process flow of PATCH method similar to PUT method above.
                        // ...
                    }
                    // additional else if blocks for other HTTP method processs flow come here...

                break;

                case ODataBatchReaderState.ChangesetStart:

                    // Request's GroupId can be passed to response writer for correlation of groupId between request & response.												
                    batchWriter.WriteStartChangeset(batchReader.CurrentGroupId);

                    // Handle atomic group starting here as needed by implementation of specific services.
                    ...
                    // End of atomic group start processing.
                    break;

                case ODataBatchReaderState.ChangesetEnd:

                    batchWriter.WriteEndChangeset();

                    // Handle atomic group ending here as needed by implementation of specific services.
                    ...
                    // End of atomic group end processing.
                    break;
            }
        }
        
        batchWriter.WriteEndBatch();
```

With the introduction of JSON batch, the following public APIs / attributes are available:
* `ODataBatchWriter`:
    * `public ODataBatchOperationRequestMessage CreateOperationRequestMessage(string method, System.Uri uri, string contentId, BatchPayloadUriOption payloadUriOption, IEnumerable<string> dependsOnIds)`: This new overload can be used to create operation request message with the additional dependsOnIds that explicitly specify operation dependencies in a JSON batch request. This new overload can also be used for Multipart/Mixed to validate the dependencies. Calling an overload without dependencyIDs in Multipart/Mixed validates any referenced statement results are valid according to the protocol.
    * `public void WriteStartChangeset(string)`: This new overload can be used to specify the atomic group Id when the operation request is created, or the operation response's atomic group Id correlating to value in the request as required by the OData JSON Batch semantics.
* `ODataBatchReader`:
	* `public string CurrentGroupId`: This returns the current group Id when the atomic group is created. It can be used to create the same group Id on the batch response corresponding to that in the incoming request, as required by the OData JSON Batch semantics. Please note that this is not the case for Multipart/Mixed format where the changeset boundary string in request and response are not correlated.
* `ODataBatchOperationRequestMessage`:
	* `public string GroupId`: This returns the operation request's group Id. For JSON batch, this is the value specified by the user; For Multipart/Mixed, this is the Id string used to generated the boundary parameter value of the changeset.
	* `public List<string> DependsOnIds`: This returns a list of prerequisite request ids of this operation request. 
      * For JSON batch, if the user-specified DependsOn data contains atomic group Id, the atomic group Id will be flattened into its contained required Ids. 
      * For Multipart/Mixed batch, the list is default to be derived implicitly per the protocol for Multipart/Mixed format in the original APIs of `ODataBatchWriter.CreateOperationRequestMessage`, but it can also be specified explicitly by the user using the new overloaded `ODataBatchWriter.CreateOperationRequestMessage` API above. 

Notes: 
* Only APIs of synchronous flavor are listed above. For async flavor please refer to the corresponding synchronous API.
* Atomic group in JSON batch corresponds to changeset in Multipart/Mixed batch.

## Typical JSON Batch Response Processing using OData-Core library
The processing of JSON batch response is similar to that of Multipart/Mixed batch, as shown in the following code snippet:

```C#
		ODataBatchOperationResponseMessage operationMessage = batchReader.CreateOperationResponseMessage();
		using (ODataMessageReader innerMessageReader = new ODataMessageReader(
			operationMessage, new ODataMessageReaderSettings(), this.userModel))
		{
			ODataReader reader = innerMessageReader.CreateODataResourceReader();

			while (reader.Read()) 
			{
				// resource data processing here...
			}
		}
```

 Note the using block for inner message reader, which helps disposal of the body content stream of the request created during the response reading. The creation of the response body content stream during response processing, similar to creation of the request body content stream during request processing, enables the parallel processing or dispatching of responses or requests of JSON batch.
