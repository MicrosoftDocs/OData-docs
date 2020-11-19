---
title: "Getting data"
description: "Getting data basics"
author: madansr7
ms.author: madansr7
ms.date: 7/1/2019
ms.topic: article
 
---
# Requesting Data

OData services support requests for data via HTTP `GET` requests.

## Requesting Entity Collections

The request below returns the the collection of Person ***People***.

`GET serviceRoot/People`

Response Payload

```json
{
    "@odata.context": "serviceRoot/$metadata#People",
    "@odata.nextLink": "serviceRoot/People?%24skiptoken=8",
    "value": [
        {
            "@odata.id": "serviceRoot/People('russellwhyte')",
            "@odata.etag": "W/\"08D1694BD49A0F11\"",
            "@odata.editLink": "serviceRoot/People('russellwhyte')",
            "UserName": "russellwhyte",
            "FirstName": "Russell",
            "LastName": "Whyte",
            "Emails": [
                "Russell@example.com",
                "Russell@contoso.com"
            ],
            "AddressInfo": [
                {
                    "Address": "187 Suffolk Ln.",
                    "City": {
                        "CountryRegion": "United States",
                        "Name": "Boise",
                        "Region": "ID"
                    }
                }
            ],
            "Gender": "Male",
            "Concurrency": 635404796846280400
        },
        ......
        ,
        {
            "@odata.id": "serviceRoot/People('keithpinckney')",
            "@odata.etag": "W/\"08D1694BD49A0F11\"",
            "@odata.editLink": "serviceRoot/People('keithpinckney')",
            "UserName": "keithpinckney",
            "FirstName": "Keith",
            "LastName": "Pinckney",
            "Emails": [
                "Keith@example.com",
                "Keith@contoso.com"
            ],
            "AddressInfo": [],
            "Gender": "Male",
            "Concurrency": 635404796846280400
        }
    ]
}
```

## Requesting an Individual Entity by ID

The request below returns an individual entity of type ***Person*** by the given UserName "russellwhyte"
`GET serviceRoot/People('russellwhyte') `

Response Payload

```json
{
    "@odata.context": "serviceRoot/$metadata#People/$entity",
    "@odata.id": "serviceRoot/People('russellwhyte')",
    "@odata.etag": "W/\"08D1694BF26D2BC9\"",
    "@odata.editLink": "serviceRoot/People('russellwhyte')",
    "UserName": "russellwhyte",
    "FirstName": "Russell",
    "LastName": "Whyte",
    "Emails": [
        "Russell@example.com",
        "Russell@contoso.com"
    ],
    "AddressInfo": [
        {
            "Address": "187 Suffolk Ln.",
            "City": {
                "CountryRegion": "United States",
                "Name": "Boise",
                "Region": "ID"
            }
        }
    ],
    "Gender": "Male",
    "Concurrency": 635404797346655200
}
```


## Requesting an Individual Property

To address an entity property clients append a path segment containing property name to the URL of the entity. If the property has a complex type, properties of that value can be addressed by further property name composition.
First let's take a look at how to get a simple property. The request below returns the ***Name*** property of an ***Airport***.

`GET serviceRoot/Airports('KSFO')/Name`

Response Payload

```json
{
    "@odata.context": "serviceRoot/$metadata#Airports('KSFO')/Name",
    "value": "San Francisco International Airport"
}
```

Then let's see how to get a property value of a complex type. The request below returns the ***Address*** of the complex type ***Location*** in an ***Airport***.
`GET serviceRoot/Airports('KSFO')/Location/Address `

Response Payload

```json
{
"@odata.context": "serviceRoot/$metadata#Airports('KSFO')/Location/Address",
"value": "South McDonnell Road, San Francisco, CA 94128"
}
```

## Requesting an Individual Property Raw Value

To address the raw value of a primitive property, clients append a path segment containing the string `$value` to the property URL. The request below returns the raw value of property ***Name*** of an ***Airport***.

`GET serviceRoot/Airports('KSFO')/Name/$value`

Response Payload

```json
San Francisco International Airport
```
