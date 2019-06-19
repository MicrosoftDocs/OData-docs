---
title: " OData V4 Web API Scaffolding"
description: "How to Use OData V4 Web API Scaffolding"

ms.date: 06/03/2015
---
# OData V4 Web API Scaffolding
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

### Install Visual Studio Extension

The installer of OData V4 Web API Scaffolding can be downloaded from Visual Studio Gallery: [Microsoft Web API OData V4 Scaffolding](https://visualstudiogallery.msdn.microsoft.com/db6b8857-06cc-4f40-95dd-a379f0494f45). Double click VSIX to install, the extension supports the VS2013 and VS2015, now.

### Generate Controller Code With Scaffolding

The scaffolding is used to generate controller code for model class. Two kinds of scaffolders are provided: for model without entity framework(Microsoft OData v4 Web API Controller) and model using entity framework(Microsoft OData v4 Web API Controller Using Entity Framework). 

#### Scaffolder for model without entity framework:

Before using scaffolding, you need to create a web api project and add model classes, the following is a sample:

![ProjAndClass](/odata/assets/11-01-ProjAndClass.PNG)

Then, you can right click "Controller" folder in solution explorer, select "Add" -> "Controller". "Microsoft OData v4 Web API Controller" will be in the scaffolder list, as following:

![SelectController](/odata/assets/11-01-SelectController.PNG)

Select scaffold item, then choose a model class you want to generate the controller. You can also select the "Using Async" if your data need to be got in Async call.

![SelectModelClass](/odata/assets/11-01-SelectModelClass.PNG)

After click "Add", the controller will be generated and added into your project. Meanwhile, all reference needed, including OData Lib and OData Web API, will be added into the project, too.

![Complete](/odata/assets/11-01-Complete.PNG)

#### Scaffolder for model using entity framework:
If want to use entity framework as provider in service, no matter whether derived class of DbContext contained in project, when right click "Controller" folder in solution explorer, select "Add" -> "Controller" -> "Microsoft OData v4 Web API Controller Using Entity Framework" as scaffolder:

![SelectScaffolderWithContext](/odata/assets/11-01-SelectScaffolderWithContext.PNG)

Then you will see as following:

![SelectModelWithoutContext](/odata/assets/11-01-SelectModelWithoutContext.PNG)

Please select the existing Model (need build before scaffolding). You can select the existing data context class or add a new one:

![AddNewDataContext](/odata/assets/11-01-AddNewDataContext.PNG)

After click "Add", the controller will be generated and added into your project, new data context class will be added if needed. Meanwhile, all reference needed, including OData Lib and OData Web API, will be added into the project, too.

### Change WebApiConfig.cs File
After generating the controller code, you may need to add some code in WebApiConfig.cs to generate model. Actually the code needed are in the comment of generated controller:

![ChangeWebApiConfig](/odata/assets/11-01-ChangeWebApiConfig.PNG)

Just need to copy/paste the code to WebApiConfig.cs.

### Add the Code to retrieve Data
As Scaffolding only generate the framework of controller code, data retrieve part should also be added into controller generated. Here, we write a simple in-memory data source and return all of them when call "GetProducts" method:

#### Add in ProductsController:
```c#
private static List<Product> products = new List<Product>()
{
  new Product() {Id = 1, Name = "Test1"},
};
```

#### Add in GetProducts Method:
```c#
return Ok(products);
```
