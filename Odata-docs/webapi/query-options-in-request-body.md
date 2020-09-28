---
title:  "Passing OData query options in request body"
date:   2020-09-28 07:49:00

ms.date: 9/28/2020
---
# Passing OData query options in request body
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-core-v7.5.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.5.md)]

The query options part of an OData URL can be quite long, potentially exceeding the maximum length that many hosting environments (including IIS) impose. This can limit the client's ability to request the exact set of data they are interested in, especially when using explict select statements.

OData AspNet WebApi V7.5 introduced support for passing query options in the request body. This is achieved by prepating a POST request with the query options part of the URL in the body and sending that to an endpoint comprising of the resource path appended with `/$query` - e.g. http://ServiceRoot/Movies/$query. The `Content-Type` header should be set to `text/plain`. 

In the rest of this page, we demonstrate how the feature works by building a simple OData service

### 1. Create the Project
Create a new C# project from the ASP.NET Core Web Application template
- .NET Core platform
- ASP.NET Core 3.1 runtime
- Empty project template
- Configure for HTTPS - unchecked

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
			routeBuilder.MapODataServiceRoute("odata", "odata", modelBuilder.GetEdmModel());
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
Using an API client such as [Postman](https://www.getpostman.com/tools), send a POST request to http://localhost:PORT/odata/Movies/$query.
- Set `Content-Type` header to `text/plain`
- Set request body to `$filter=contains(Name,%20%27li%27)&$orderby=Name%20desc&$select=Id,Name,Classification,RunningTime`

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
The feature allows you split the query options between the request body and the request URL. Using the example, you can achieve the same outcome by sending a POST with request body as `$filter=contains(Name,%20%27li%27)&$select=Id,Name,Classification,RunningTime` to http://localhost:PORT/odata/Movies/$query?$orderby=Name%20desc