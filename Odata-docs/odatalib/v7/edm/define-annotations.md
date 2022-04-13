---
title: " Define annotations-ODL V7"
description: "Define annotations using EdmLib APIs-ODL V7"
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Define annotations
**Applies To**: [!INCLUDE[appliesto-odataclient](../../../includes/appliesto-odatalib-v7.md)]

EdmLib supports adding annotations on various model elements, including entity sets, entity types, properties, and so on. Annotations can be put under **the `Annotations` XML element**, or under **the annotated target model elements** (**inline** annotations). Users can specify the **serialization location** using the EdmLib API.

This section shows how to define annotations using EdmLib APIs. We will use and extend the sample from the previous section.

### Add an annotation to entity set *Customers*
In the **SampleModelBuilder.cs** file, add the following `using` directive:

```C#
using Microsoft.OData.Edm.Csdl;
using Microsoft.OData.Edm.Vocabularies;
```

Then add the following code into the `SampleModelBuilder` class:

```C#
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        public SampleModelBuilder BuildAnnotations()
        {
            var term1 = new EdmTerm("Sample.NS", "MaxCount", EdmCoreModel.Instance.GetInt32(true));
            var annotation1 = new EdmVocabularyAnnotation(_customerSet, term1, new EdmIntegerConstant(10000000L));
            _model.AddVocabularyAnnotation(annotation1);
            return this;
        }
        ...
    }
}
```

And in the **Program.cs** file, insert the following code into the `Main()` method:

```C#
namespace EdmLibSample
{
    class Program
    {
        public static void Main(string[] args)
        {
            var builder = new SampleModelBuilder();
            var model = builder
                ...
                .BuildMostValuableFunctionImport()
#region         !!!INSERT THE CODE BELOW!!!
                .BuildAnnotations()
#endregion
                .GetModel();
            WriteModelToCsdl(model, "csdl.xml");
        }
    }
}
```

This code adds an `Edm.Int32` annotation `Sample.NS.MaxCount` to the entity set `Customers`, which is put under the `Annotations` element.

### Add an inline Annotation to the Entity Type *Customer*
In the **SampleModelBuilder.cs** file, insert the following code into the `SampleModelBuilder.BuildAnnotations()` method:

```C#
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        public SampleModelBuilder BuildAnnotations()
        {
            ...
            _model.AddVocabularyAnnotation(annotation1);
#region     !!!INSERT THE CODE BELOW!!!
            var term2 = new EdmTerm("Sample.NS", "KeyName", EdmCoreModel.Instance.GetString(true));
            var annotation2 = new EdmVocabularyAnnotation(_customerType, term2, new EdmStringConstant("Id"));
            annotation2.SetSerializationLocation(_model, EdmVocabularyAnnotationSerializationLocation.Inline);
            _model.AddVocabularyAnnotation(annotation2);
#endregion
            return this;
        }
        ...
    }
}
```

This code adds an **inline** `Edm.String` annotation `Sample.NS.KeyName` targetting the entity type `Customer`.

### Add an inline annotation to the property *Customer.Name*
In the **SampleModelBuilder.cs** file, insert the following code into the `SampleModelBuilder.BuildAnnotations()` method:

```C#
namespace EdmLibSample
{
    public class SampleModelBuilder
    {
        ...
        public SampleModelBuilder BuildAnnotations()
        {
            ...
            _model.AddVocabularyAnnotation(annotation2);
#region     !!!INSERT THE CODE BELOW!!!
            var term3 = new EdmTerm("Sample.NS", "Width", EdmCoreModel.Instance.GetInt32(true));
            var annotation3 = new EdmVocabularyAnnotation(_customerType.FindProperty("Name"), term3, new EdmIntegerConstant(10L));
            annotation3.SetSerializationLocation(_model, EdmVocabularyAnnotationSerializationLocation.Inline);
            _model.AddVocabularyAnnotation(annotation3);
#endregion
            return this;
        }
        ...
    }
}
```

This code adds an **inline** `Edm.Int32` annotation `Sample.NS.Width` to the property `Customer.Name`.

### Run the sample
Build and run the sample. Then open the file **csdl.xml** under the **output directory**. The content should look like the following:

![csdl1](/odata/assets/2015-04-20-csdl1.png)
