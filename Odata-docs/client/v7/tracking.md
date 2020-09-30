---
title: "Client Tracking"
description: "This tutorial describes how to use client tracking on client side"

author: mumbi-o
ms.author: mowambug
ms.date: 7/1/2019
ms.topic: article
 
---
# Client tracking
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odataclient-v7.md)]

OData Client for .NET supports two levels tracking : entity tracking and property tracking(only top level properties). Entity tracking enables you to track an entity in `DataServiceContext`. You can enable property tracking by aid of `DataServiceCollectionOfT`.

## Entity Tracking 

`DataServiceContext` provides several ways to track an entity.

1. Newly added entities will be automatically tracked. 
2. If you use `DataServiceContext.AttachTo` to attach an entity, `DataServiceContext` will track the entity. 
3. Entities returned by queries are also tracked if `DataServiceContext.MergeOption` is not `MergeOption.NoTracking`.  

Once entities are tracked, you can use `DataServiceContext.EntityTracker` to get each entity descriptor which is used to describe the entity on client side. the entity tracker can also be used to get the link descriptor of all tracked links.

Once entities are tracked, the changes of these entities can be sent back to the data service when you call `DataServiceContext.SaveChanges` method.

If you are using `MergeOption.NoTracking` when you query an entity. You cannot get ETag of the entity  from `DataServiceContext` if it exists, since you cannot get the entity descriptor for the entity. Then, if you want to call AttachTo to track the entity, you need provide the ETag of the entity.

`DataServiceContext` tracks each relationship as a link. You can use methods
`AddRelatedObject`, `AttachLink`, `AddLink`, `SetLink`, `DetachLink`, `DeleteLink` to track a link.

One sample to use `AttachTo` and `DeleteLink`.

``` csharp

	DefaultContainer dsc = new DefaultContainer(new Uri("https://services.odata.org/V4/(S(uvf1y321yx031rnxmcbqmlxw))/TripPinServiceRW/"));

    public void ClientEntityTracking()
    {
        var person = new Person()
	    {
	        UserName = "clydeguess"
	    };
	
	    var oneFriend = new Person()
	    {
	        UserName = "keithpinckney"
	    };
	    dsc.AttachTo("People", person);
	    dsc.AttachTo("People", oneFriend);
	    dsc.DeleteLink(person, "Friends", oneFriend);
	    dsc.SaveChanges();
    }

```

## Property Tracking 

Please refer to [client property tracking for patch](https://devblogs.microsoft.com/odata/tutorial-sample-client-property-tracking-for-patch/) for detail.