---
title: " Custom routing convention"
description: Learn how to override the default Web API OData routing convention with the OData custom routing convention.
ms.date: 7/1/2019
author: madansr7
---
# Custom routing convention
**Applies To**:[!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v7.md)][!INCLUDE[appliesto-webapi](../includes/appliesto-webapi-v6.md)]

It is easy to create custom routing conventions to override the default Web API OData routing convention.

### Property access routing convention

From built-in routing convention section, we know that users should add many actions for every property access. 

For example, if the client issues the following property access request URIs:

```C#
~/odata/Customers(1)/Orders
~/odata/Customers(1)/Address
~/odata/Customers(1)/Name
...
```

Service should have the following actions in `CustomersController` to handle:

```C#
public class CustomersController : ODataController
{
    public string GetOrders([FromODataUri]int key)
    {
        ......
    }
	
    public string GetAddress([FromODataUri]int key)
    {
        ......
    }
	
    public string GetName([FromODataUri]int key)
    {
        ......
    }
}
```

If `Customer` has hundreds of properties, a developer should add hundreds of similar functions in `CustomersController`. This is tedious, but with a custom routing convention we can override this behavior.

### Custom routing convention

We can create our own routing convention class by implementing the `IODataRoutingConvention`. However, if you don't want to change the behaviour to find the *controller*, the new added routing convention class can derive from `NavigationSourceRoutingConvention'.

Let's build a sample property access routing convention class derived from `NavigationSourceRoutingConvention`.

```C#
public class CustomPropertyRoutingConvention : NavigationSourceRoutingConvention
{
  private const string ActionName = "GetProperty";

  public override string SelectAction(ODataPath odataPath, HttpControllerContext controllerContext, ILookup<string, HttpActionDescriptor> actionMap)
  {
    if (odataPath == null || controllerContext == null || actionMap == null)
    {
       return null;
    }

    if (odataPath.PathTemplate == "~/entityset/key/property" ||
        odataPath.PathTemplate == "~/entityset/key/cast/property" ||
	odataPath.PathTemplate == "~/singleton/property" ||
	odataPath.PathTemplate == "~/singleton/cast/property")
    {
      var segment = odataPath.Segments[odataPath.Segments.Count - 1] as PropertyAccessPathSegment;

      if (segment != null)
      {
 	  string actionName = FindMatchingAction(actionMap, ActionName);

	  if (actionName != null)
	  {
	    if (odataPath.PathTemplate.StartsWith("~/entityset/key", StringComparison.Ordinal))
	    {
	      KeyValuePathSegment keyValueSegment = odataPath.Segments[1] as KeyValuePathSegment;
	      controllerContext.RouteData.Values[ODataRouteConstants.Key] = keyValueSegment.Value;
	    }

	    controllerContext.RouteData.Values["propertyName"] = segment.PropertyName;

	    return actionName;
	  }
	}
     }

     return null;
   }

   public static string FindMatchingAction(ILookup<string, HttpActionDescriptor> actionMap, params string[] targetActionNames)
   {
     foreach (string targetActionName in targetActionNames)
     {
       if (actionMap.Contains(targetActionName))
       {
   	  return targetActionName;
       }
     }

     return null;
   }
}
```

Where, we route the following path templates to a certain action named `GetProperty`.

```C#
~/entityset/key/property
~/entityset/key/cast/property
~/singleton/property
~/singleton/cast/property
```

### Enable customized routing convention

The following sample code is used to enable the customized routing convention:

```C#
HttpConfiguration configuration = ......
IEdmModel model = GetEdmModel();
IList<IODataRoutingConvention> conventions = ODataRoutingConventions.CreateDefaultWithAttributeRouting(configuration, model);
conventions.Insert(0, new CustomPropertyRoutingConvention());
configuration.MapODataServiceRoute("odata", "odata", model, new DefaultODataPathHandler(), conventions);
```

Where, we insert our own routing convention at the starting position to override the default Web API OData property access routing convention.

### Add actions

In the `CustomersController`, only one method named `GetProperty` should be added. 

```C#
public class CustomersController : ODataController
{
	[HttpGet]
	public IHttpActionResult GetProperty(int key, string propertyName)
	{
		Customer customer = _customers.FirstOrDefault(c => c.CustomerId == key);
		if (customer == null)
		{
			return NotFound();
		}

		PropertyInfo info = typeof(Customer).GetProperty(propertyName);

		object value = info.GetValue(customer);

		return Ok(value, value.GetType());
	}
	
	private IHttpActionResult Ok(object content, Type type)
	{
		var resultType = typeof(OkNegotiatedContentResult<>).MakeGenericType(type);
		return Activator.CreateInstance(resultType, content, this) as IHttpActionResult;
	}
}
```

### Samples

Below are some request Uri samples to test:

a)
```C#
GET https://localhost/odata/Customers(2)/Name
```

The result is:

```C#
{
  "@odata.context":"https://localhost/odata/$metadata#Customers(2)/Name","value": "Mike"
}
```

b) 
```C#
GET https://localhost/odata/Customers(2)/Location
```

The result is:
```C#
{
  "@odata.context":"https://localhost/odata/$metadata#Customers(2)/Salary","value ":2000.0
}
```

c)
```C#
GET https://localhost/odata/Customers(2)/Location
```

The result is:
```C#
{
  "@odata.context":"https://localhost/odata/$metadata#Customers(2)/Location","Country":"The United States","City":"Redmond"
}
```
