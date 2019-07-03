---
title: "URL components"
description: "URL components basics"
author: madansr7
ms.author: madansr7
ms.date: 7/1/2019
ms.topic: article
 
---

# URL components

URLs represent individual resources, collections of resources, or operations, and clients interact with those resources using standard GET, PUT, PATCH, POST and DELETE operations.
An OData service URL consists of 3 parts.

```html
http://host:port/path/SampleService.svc/Categories(1)/Products?$top=2&$orderby=Name
\______________________________________/\____________________/ \__________________/
                  |                               |                       |
          service root URL                  resource path           query options
```

1. ***Service root*** URL

    The service root of a url is the base url of the service. When a _GET_ request is made to this url it will return a service document that defines all the resources available via that service.

2. ***Resource path***
    A resource by REST definition is an object which is accessible over the HTTP by using the standard GET,POST,PUT,PATCH and DELETE methods. It could be a single object or a collection of similar objects, presented in an ordered or unordered.
    An object is constructed of a certain type, has relationships with other objects and has methods that operate on it.
    Some examples for resources might be: customers, a single customer, orders related to a single customer, and so forth.
    Examples of addressable aspects of these resources as exposed by the data model might be: collections of entities, a single entity, properties, links, operations, and so on.

    Here are a few examples of how a resource could be accessed:
    - `http://host/service/Products` gets access to an entity set
    - `http://host/service/ProductsByCategoryId(catId=2)` allows execution of a function

3. ***Query options***

    Query options are essentially standardized querystring parameters that can be passed to an OData service to run queries on a requested resource. They can help perform certain operations like `select`, `filter`, `count`, `skip`, `order`, `search` and `format` on resources.
    All OData query options are prefixed by a `$` sign and they are case-insensitive.

    For e.g. the following url selects a product by color.
    `https://server/products?$filter=color eq 'red'`
