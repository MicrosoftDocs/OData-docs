---
title: "OData CLI"
description: "How to generate code using the odata-cli dotnet tool"

author: ElizabethOkerio
ms.author: elokerio
ms.date: 3/30/2022
ms.topic: article
 
---
# OData CLI

**Applies To**: [!INCLUDE[appliesto-odataclient](../includes/appliesto-odataclient-v7.md)]

## Install
There are 2 ways to download and install the **OData CLI**

 - dotnet global tool:
 
    To install the latest release of the OData CLI [Nuget Package](https://www.nuget.org/packages/Microsoft.OData.Cli/), use the [dotnet tool install](/dotnet/core/tools/dotnet-tool-install) command

    ```.NET CLI
    dotnet tool install --global Microsoft.OData.Cli --version 0.1.0
    ```

 - Direct Download:

    You can download the binary files from this [GitHub Release](https://github.com/OData/ODataConnectedService/releases/tag/v0.1.0) and use them directly to generate code.

## Synopsis

```Console
odata-cli [-h|--help] [--version] <command>
```

## Description

**odata-cli** is a CLI tool that generates strongly-typed C# and Visual Basic client code for a specified OData service. It generates a [DataServiceContext](/dotnet/api/microsoft.odata.client.dataservicecontext) class to interact with the service and CLR types for each entity type and complex type in the service model.

## Options

 - `version`

    Displays the version of the odata-cli
 - `-h|--help`

    Shows the command-line help


## Commands

### generate

It generates strongly typed C# and Visual Basic client code for a specified OData service. 

### Synopsis

```Console
odata-cli generate [-h|--help] [-m|--metadata-uri] [-fn|--file-name] [-h|--custom-headers] [-p}--proxy] [-ns|--namespace-prefix] [-ucc|--upper-camel-case] [-i|--internal] [--multiple-files] [-eoi|--excluded-operation-imports] [-ebo|--excluded-bound-operations] [-est|--excluded-schema-types] [-o|--outputdir]
```

### Options 

- `--metadata-uri|-m` 

     The URI of the metadata document. The value must be set to a valid service document URI or a local file path. This is a required option

 - `--file-name|-fn`
 
    The name of the generated file. If not provided, then the default name 'Reference.cs/.vb' is used. 

 - `--custom-headers|-h`
 
     Headers that will get sent along with the request when fetching the metadata document from the service. `Format: Header1:HeaderValue, Header2:HeaderValue`. 

 - `--proxy|-p` 
 
    Proxy settings. `Format: domain\\user:password@SERVER:PORT`. 

 - `--namespace-prefix|-ns`
 
    The namespace of the client code generated. Example: NorthWindService.Client or it could be a name related to the OData endpoint that you are generating code for. 

 - `--upper-camel-case|-ucc`
 
    Transforms names (class and property names) to upper camel-case so that to better conform with C# naming conventions.

 - `--internal|-i`
 
    Applies the `internal` class modifier on generated classes instead of `public` thereby making them invisible outside the assembly. 

 - `--multiple-files`
 
    Split the generated classes into separate files instead of generating all the code in a single file. 

 - `--excluded-operation-imports|-eoi`
 
    Comma-separated list of the names of operation imports to exclude from the generated code. Example: `ExcludedOperationImport1, ExcludedOperationImport2`. 

 - `--excluded-bound-operations|-ebo`
 
    Comma-separated list of the names of bound operations to exclude from the generated code. Example: `BoundOperation1, BoundOperation2`. 

 - `--excluded-schema-types|-est`
 
    Comma-separated list of the names of entity types to exclude from the generated code. Example: `EntityType1, EntityType2, EntityType3`. 

 - `--ignore-unexpected-elements|-iue`
 
    This flag indicates whether to ignore unexpected elements and attributes in the metadata document and generate the client code if any  

 - `--outputdir|-o`
 
    Full path to output directory. This could be an empty directory or a directory with a .csproj/.vbproj file. This is a required option.

### Examples
```Console
odata-cli generate -m "http://localhost/odata" -o "link-to-output-directory"
```
The metadata-uri could be a link to an OData endpoint or a link to an xml file in your local computer storage. 

The output directory can be a link a to an empty folder or a link to a project folder(a folder with the .csproj file). 