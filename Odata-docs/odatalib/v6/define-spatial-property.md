---
title: "Define spatial properties in odatalib"
description: "Define spatial properties in entity data models"
author: saumadan
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Define spaital properties(ODL V6.x)
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v6.md)]

Using Spatial in OData services involves two parts of work:

 - Define **structural properties** of spatial type in entity data models;
 - Create and return spatial instances as **property values** in services.

This section shows how to define spatial properties in entity data models using EdmLib APIs. We will continue to use and extend the sample from the **EdmLib sections**.

### Add Properties *GeometryLoc* and *GeographyLoc*

In the **SampleModelBuilder.cs** file, insert the following code into the `SampleModelBuilder.BuildAddressType()` method:

``` csharp
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        public SampleModelBuilder BuildAddressType()
        {
            ...
            _addressType.AddStructuralProperty("Postcode", EdmPrimitiveTypeKind.Int32);
#region     !!!INSERT THE CODE BELOW!!!
            _addressType.AddStructuralProperty("GeometryLoc", EdmPrimitiveTypeKind.GeometryPoint);
            _addressType.AddStructuralProperty("GeographyLoc", new EdmSpatialTypeReference(EdmCoreModel.Instance.GetPrimitiveType(EdmPrimitiveTypeKind.GeographyPoint), true, 1234));
#endregion
            _model.AddElement(_addressType);
            return this;
        }
        ...
    }
}
```

This code:

 - Adds a default `Edm.GeometryPoint` property `GeometryLoc` to the `Address` type;
 - Adds an `Edm.GeographyPoint` property `GeographyLoc` with a **type facet** `Srid=1234` to the `Address` type.


### Run the Sample

Build and run the sample. Then open the **csdl.xml** file under the **output directory**. The content of **csdl.xml** should look like the following:

![image](/odata/assets/2015-04-21-csdl.png)