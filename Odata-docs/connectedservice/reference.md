---
title: "Settings Reference"
description: This is a comprehensive reference of the configuration options available on OData Connected Service.
author: habbes
ms.author: clhabins
ms.date: 7/17/2020
ms.topic: article
 
---
# Settings Reference

**Applies To**: [!INCLUDE[appliesto-odataclient](../includes/appliesto-odataclient-v7.md)]

This is a comprehensive reference of the configuration options available on OData Connected Service

## Wizard Settings

### Endpoint configuration

Setting/Control        |   Description           | Remarks
-----------------------|-------------------------|----------------
**Service name**       | Arbitrary name for the connected service. A folder by this name will be created in your project to store the generated code, config file and artifacts. | This is **required**. It's also disabled when updating the connected service.
**Load settings from config file** button| Allows you to load a JSON file that contains existing configuration. The wizard will then be populated with the values from this file.  The JSON file should have the same schema as the `ConnectedService.json` file where your settings are persisted. This means that you can load the `ConnectedService.json` of an existing service as a starting point for your configuration. | This button is disabled when updating a connected service.
**Address**  | The address of the service metadata document. This can either be a web URL or an absolute path to a local XML file. It also keeps a list of the most recently used addresses | This is **required**. It's disabled when updating the connected service.
**Include Custom Headers** checkbox | Whether to include custom headers when fetching the metadata. This toggles the **Custom Headers** text field.
**Custom Headers**  | Arbitrary headers to attach to the request when fetching the service metadata document. A header entry is a key-value pair separated by a colon (e.g. `Authorization: Bearer token`). Each entry should be on a separate line. | This value is not persisted in the `ConnectedService.json` file.
**Include WebProxy** checkbox | Toggles web proxy configuration. Enable this if your running Visual Studio behind a proxy.
**Enter the web proxy host** | Web proxy host and port | Not persisted in `ConnectedService.json`.
**Include Proxy Network credentials** | Toggles proxy credentials configuration
**Username** | Proxy user name | No persisted in `ConnectedService.json`
**Password** | Proxy password | Not persisted in `ConnectedService.json`
**Domain**   | Proxy domain | Not persisted in `ConnectedService.json`

### Advanced Settings

Setting/Control     | Description                 | Remarks
--------------------|-----------------------------|--------------------------
**Enter filename without extension** | The name of main the file where the generate code will be stored. The file will get a `.cs`, `.vb`, `.tt` extension depending on whether you're generating C# code, VB code, or T4 templates respectively. | **Required**. Disabled when updating the connected service.
**Use a custom namespace** | Whether to override the namespace in the metadata document with a custom namespace. If this option is enabled, enter the custom namespace in the text field.
**Enable entity and property tracking** | [Entity and property tracking](/odata/client/tracking) allows the `DataServiceContext` to keep track of changes made to entities on the client side. This option generates events that are triggered when properties of the entities change.
**Use c# casing** | Convert names in the model to `PascalCase` (e.g. `alertType` will be renamed to `AlertType`)
**Make generated types internal** | If enabled, the generated types will have an `internal` modifier (`Friend` in VB). This is useful if you're generating client code as part of a class library and you don't want to expose the generated type outside the library.
**Add code templates** | Instead of generating C#/VB code, this option generates T4 templates that can be used to generate the final code. | Disabled when updating the conneced service.
**Open generated files in IDE when generation completes** | Opens the generated file in Visual Studio when code generation completes.
**Generate multiple files** | Split the generated code into multiple files instead of generating one big file. Each major type (entity, enum) will have its own separate file. | Disabled when updating the connected service.

## Config file JSON

Your configuration are persisted in a JSON file called `ConnectedService.json`. You can also load existing configuration from a JSON file when creating a new connected service using the same schema as `ConnectedService.json`. Here's what a sample config file looks like:

```json
{
  "ExtendedData": {
    "ExcludedOperationImports": [
      "ResetDataSource"
    ],
    "EnableNamingAlias": false,
    "IgnoreUnexpectedElementsAndAttributes": false,
    "IncludeT4File": false,
    "ServiceName": "TripPinService",
    "Endpoint": "https://services.odata.org/V4/TripPinServiceRW/$metadata",
    "EdmxVersion": {
      "Major": 4,
      "Minor": 0,
      "Build": 0,
      "Revision": 0,
      "MajorRevision": 0,
      "MinorRevision": 0
    },
    "GeneratedFileNamePrefix": "Reference",
    "UseNamespacePrefix": false,
    "NamespacePrefix": null,
    "UseDataServiceCollection": false,
    "MakeTypesInternal": false,
    "OpenGeneratedFilesInIDE": false,
    "GenerateMultipleFiles": false,
    "CustomHttpHeaders": null,
    "IncludeWebProxy": false,
    "WebProxyHost": null,
    "IncludeWebProxyNetworkCredentials": false,
    "WebProxyNetworkCredentialsUsername": null,
    "WebProxyNetworkCredentialsPassword": null,
    "WebProxyNetworkCredentialsDomain": null,
    "IncludeCustomHeaders": false,
    "ExcludedSchemaTypes": [
      "Microsoft.OData.SampleService.Models.TripPin.Event",
      "Microsoft.OData.SampleService.Models.TripPin.EventLocation",
      "Microsoft.OData.SampleService.Models.TripPin.Flight",
      "Microsoft.OData.SampleService.Models.TripPin.PublicTransportation"
    ]
  }
}
```
The reason all the relevant fields are nested inside the top-level `ExtendedData` object is to retain compatibility with the `ConnectedService.json` schema.
