---
title: "OData CLI"
description: "How to generate code using the odata-cli dotnet tool"

author: ElizabethOkerio
ms.author: ElizabethOkerio
ms.date: 3/30/2022
ms.topic: article
 
---
# OData CLI

**Applies To**: [!INCLUDE[appliesto-odataclient](../includes/appliesto-odataclient-v7.md)]

**OData CLI** is a CLI tool that generates strongly-typed C# and Visual Basic client code for a specified OData service. It generates a `DataServiceContext` class to interact with the service and CLR types for each entity type and complex type in the service model.

The extension is available at [Nuget as Microsoft.OData.Cli](https://www.nuget.org/packages/Microsoft.OData.Cli/)

## Commands

The **odata-cli** tool has only one command:  

 - **generate** - This is the command that generates strongly typed C# and Visual Basic client code for a specified OData service.  

## Options 

The following is a list of options that can be passed to the **generate** command described above: Most of the options are optional except the `--metadata-uri|-m` and   `--outputdir|-o` options which are required options.  

- `--metadata-uri|-m` - The URI of the metadata document. The value must be set to a valid service document URI or a local file path. 

 - `--file-name|-fn` - The name of the generated file. If not provided, then the default name 'Reference.cs/.vb' is used. 

 - `--custom-headers|-h` - Headers that will get sent along with the request when fetching the metadata document from the service. `Format: Header1:HeaderValue, Header2:HeaderValue`. 

 - `--proxy|-p` - Proxy settings. `Format: domain\\user:password@SERVER:PORT`. 

 - `--namespace-prefix|-ns` - The namespace of the client code generated. Example: NorthWindService.Client or it could be a name related to the OData endpoint that you are generating code for. 

 - `--upper-camel-case|-ucc` – Transforms names (class and property names) to upper camel-case so that to better conform with C# naming conventions.

 - `--internal|-i` - Applies the `internal` class modifier on generated classes instead of `public` thereby making them invisible outside the assembly. 

 - `--multiple-files` - Split the generated classes into separate files instead of generating all the code in a single file. 

 - `--excluded-operation-imports|-eoi` - Comma-separated list of the names of operation imports to exclude from the generated code. Example: `ExcludedOperationImport1, ExcludedOperationImport2`. 

 - `--excluded-bound-operations|-ebo` - Comma-separated list of the names of bound operations to exclude from the generated code. Example: `BoundOperation1, BoundOperation2`. 

 - `--excluded-schema-types|-est` - Comma-separated list of the names of entity types to exclude from the generated code. Example: `EntityType1, EntityType2, EntityType3`. 

 - `--ignore-unexpected-elements|-iue` - This flag indicates whether to ignore unexpected elements and attributes in the metadata document and generate the client code if any  

 - `--outputdir|-o` - Full path to output directory. This could be an empty directory or a directory with a .csproj/.vbproj file.  

## How to Install 

Run the following command to install the tool:

```
dotnet tool install --global Microsoft.OData.Cli --version 0.1.0
```

## How to Run 

If the installation from the above step was successfull, execute the tool as below: 
```
odata-cli generate –m "https://services.odata.org/V4/TripPinServiceRW/$metadata" -o "LinkToOutPutDirectory"
 
```
**NOTE:** It is optional though to include the `$metadata` on the metadata url.

You can include the other options as you may wish to use them.  

Check out this link [using the generated code](/odata/connectedservice/getting-started#using-the-generated-code) on how to use the generated code.  

## Help
To get help on the generate command, run:

```
odata-cli generate --help 
```