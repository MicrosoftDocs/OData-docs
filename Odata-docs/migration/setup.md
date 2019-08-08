---
title: "Setup"
description: ""
author: aayc
ms.author: aayc
ms.date: 8/8/2019
ms.topic: article
 
---
# Setup
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

There are two steps to configure your service to use OData Migration.

### Step 1: Configuring Services

Adding OData Migration to your service configuration is simply one properly placed line of code.

```C#
public static void ConfigureServices(IServiceCollection services)
{
	// your code here
	
	// AddOData must be called before AddODataMigration
	services.AddOData();
	services.AddODataMigration();

	// your code here
}
```

AddODataMigration takes no arguments, and adds the following to your service collection:

1. OData Migration filters
2. OData Migration input formatter
3. OData Migration output formatter

The OData Migration filters are ASP.NET Core filters that are used to:

1. Add version and content ID headers to batched requests
2. Catch exceptions in inner requests within batch requests and send them back as status code 500 internal server errors with content ID headers attached

The OData Migration input and output formatters observe the incoming request to see if it contains either of the specific OData version headers.
These headers are `dataserviceversion: 3.0` and `maxdataserviceversion: 3.0`  If either of these headers are present in the request, the input formatter
will deserialize the request as an OData V3 request, and the output formatter will return JSON that is OData V3 compliant.  Both of these formatters
make use of the OData EDMX contract to validate requests and responses just as they would be validated in V4.

### Step 2: Configuring IApplicationBuilder

Configuring your application to use OData Migration in your application's pipeline requires two parameters:

1. An OData V3 model or string representation (EDMX) of the OData V3 model
2. An OData V4 model

Both of these parameters are used for model validation during translation.  The EDMX of the OData V3 model will be returned when clients query your service for metadata.

Supplying these two parameters to the UseODataMigration extension method is all you need to do to configure your application:
```C#
public static void Configure(IApplicationBuilder builder)
{
	// your code here

	// If using batching, you must call UseODataBatching before UseODataMigration
	builder.UseODataBatching();

	IEdmModel v4model = ...

    // If you are working with a Data.Edm.IEdmModel:
    Data.Edm.IEdmModel v3model = ...
    builder.UseODataMigration(v3model, v4model);

    // If you have your OData V3 model representation as an EDMX string (e.g., by querying your V3 service for metadata):
    //string v3Edmx = ...
	//builder.UseODataMigration(v3Edmx, v4model);

	// your code here
}
```

Calling UseODataMigration inserts the middleware responsible for translating incoming request URLs.  For example, an OData V3 request URL might look like:

```
https://localhost/v3/Product(guid'02951787-4c1a-4dff-a917-a04b21b40ad3')
```

whereas the equivalent OData V4 request URL looks like:

```
https://localhost/v3/Product(02951787-4c1a-4dff-a917-a04b21b40ad3)
```

OData Migration extension's middleware will take care of this conversion for you automatically, provided that you are using JSON and have either the `dataserviceversion: 3.0` or `maxdataserviceversion: 3.0` header in your request headers.

Be aware that in its current state, using this extension will cause your v3 metadata to always be returned in response to any metadata requests.