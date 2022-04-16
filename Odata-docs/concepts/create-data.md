---
title: "Data Modification- POST"
description: "Data Modification- POST basics"
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Create an Entity

To create an entity in a collection, the client sends a POST request to that collection's URL. The POST body MUST contain a single valid entity representation. The request below creates a ***Person*** which contains complex type and collection property. 

```JSON
    POST serviceRoot/People
    OData-Version: 4.0
    Content-Type: application/json;odata.metadata=minimal
    Accept: application/json

    {
    "@odata.type" : "Microsoft.OData.SampleService.Models.TripPin.Person",
    "UserName": "teresa",
    "FirstName" : "Teresa",
    "LastName" : "Gilbert",
    "Gender" : "Female",
    "Emails" : ["teresa@example.com", "teresa@contoso.com"],
    "AddressInfo" : [
    {
    "Address" : "1 Suffolk Ln.",
    "City" :
    {
    "CountryRegion" : "United States",
    "Name" : "Boise",
    "Region" : "ID"
    }
    }]
    }
```

Response Payload

```JSON

HTTP/1.1 201 Created
Content-Length: 468
Content-Type: application/json;odata.metadata=minimal;odata.streaming=true;IEEE754Compatible=false;charset=utf-8
Location: serviceRoot/People('teresa')
OData-Version: 4.0
{
    "@odata.context":"serviceRoot/$metadata#People/$entity",
    "@odata.id":"serviceRoot/People('teresa')",
    "@odata.editLink":"serviceRoot/People('teresa')",
    "UserName":"teresa",
    "FirstName":"Teresa",
    "LastName":"Gilbert",
    "Emails":["teresa@example.com","teresa@contoso.com"],
    "AddressInfo":[{"Address":"1 Suffolk Ln.","City":{"CountryRegion":"United States","Name":"Boise","Region":"ID"}}],
    "Gender":"Female"
}
```
