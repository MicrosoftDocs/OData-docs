---
title: "Passing OData query options in request body"
description: "Describes how OData query options can be passed in the request body"
date: 2020-09-28 07:49:00
author: gathogojr
ms.author: jogathog
ms.date: 9/28/2020
---
# Passing OData query options in request body
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-core-v7.5.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.5.md)]

The query options part of an OData URL can be quite long, potentially exceeding the maximum length that many hosting environments (including IIS) impose. This can limit the client's ability to request the exact set of data they are interested in, especially when using explict select statements.

OData WebApi V7.5 introduced support for passing query options in the request body. You append `/query` to the resource path of the URL, use the `POST` verb instead of `GET`, and pass the query options part of the URL in the request body.
### Example
POST: `http://ServiceRoot/Movies/$query`  
Content-Type: `text/plain`  
Body: `$select=Id,Name,Classification,RunningTime&$filter=contains(Name,%20%27li%27)&$orderby=Name%20desc`

In the rest of this page, we demonstrate how this feature works by building a simple OData service

### 1. Create the Project
Create a new C# project from the ASP.NET Core Web Application template
- .NET Core platform
- ASP.NET Core 3.1 runtime
- Empty project template
- Uncheck Configure for HTTPS option

### 2. Install Packages
Install OData AspNetCore WebApi package. Using Package Manager Console:
```
Install-Package Microsoft.AspNetCore.OData -Version 7.5.0
```
**Note:** You can also install any higher version

### 3. Define CLR Type
```c#
public class Movie
{
	[Key]
	public int Id { get; set; }
	public string Name { get; set; }
	public string Classification { get; set; }
	public int RunningTime { get; set; }
}
```

### 4. Configure the Service
```c#
public class Startup
{
	public void ConfigureServices(IServiceCollection services)
	{
		services.AddMvc(options => options.EnableEndpointRouting = false)
				.SetCompatibilityVersion(CompatibilityVersion.Version_3_0);
		services.AddOData();
	}

	public void Configure(IApplicationBuilder app)
	{
		var modelBuilder = new ODataConventionModelBuilder();
		modelBuilder.EntitySet<Movie>("Movies");

		app.UseMvc(routeBuilder =>
		{
			routeBuilder.Select().Filter().Expand().Count().OrderBy().SkipToken().MaxTop(100);
			routeBuilder.MapODataServiceRoute(
				routeName: "odata",
				routePrefix: "odata",
				model: modelBuilder.GetEdmModel());
		});
	}
}
```

### 5. Add controller
```c#
public class MoviesController : ODataController
{
	static readonly List<Movie> Movies;

	static MoviesController()
	{
		Movies = new List<Movie>
		{
			new Movie { Id = 1, Name = "Bombshell", Classification = "R", RunningTime = 108 },
			new Movie { Id = 2, Name = "Ad Astra", Classification = "PG-13", RunningTime = 124 },
			new Movie { Id = 3, Name = "Dolittle", Classification = "PG", RunningTime = 101 },
			new Movie { Id = 4, Name = "Doctor Strange", Classification = "PG-13", RunningTime = 115 },
			new Movie { Id = 5, Name = "The Equalizer", Classification = "R", RunningTime = 132 }
		};
	}

	[EnableQuery]
	public List<Movie> Get()
	{
		return Movies;
	}
}
```

### 6. Pass query options in request body
Using an API client such as [Postman](https://www.getpostman.com/tools), send a POST request to `http://localhost:PORT/odata/Movies/$query`.
- Set `Content-Type` header to `text/plain`
- Set request body to `$filter=contains(Name,'li')&$orderby=Name desc&$select=Id,Name,Classification,RunningTime`

You should get the following response:
```json
{
    "@odata.context": "http://localhost:PORT/odata/$metadata#Movies(Id,Name,Classification,RunningTime)",
    "value": [
        {
            "Id": 5,
            "Name": "The Equalizer",
            "Classification": "R",
            "RunningTime": 132
        },
        {
            "Id": 3,
            "Name": "Dolittle",
            "Classification": "PG",
            "RunningTime": 101
        }
    ]
}
```
The feature also allows you split the query options between the request body and the request URL.
### Example
POST: `http://ServiceRoot/Movies/$query?$orderby=Name%20desc`  
Content-Type: `text/plain`  
Body: `$filter=contains(Name,'li')&$select=Id,Name,Classification,RunningTime`

You can visit [this page](/odata/webapi/custom-query-options-parser) to learn how you can implement and plug in a custom parser for query options in the request body.
