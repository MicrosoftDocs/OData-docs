---
title: "Model references in ODL v6"
description: "Model references-ODL v6"
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
---
# Define model references(ODL V6.x)
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v6.md)]

Model referencing is an advanced OData feature. When you want to use some types defined in another model, you can reference that model in your own model. Typically when we talking about model referencing, we have a **main model** and one or more **sub models**. The main model references the sub models. But that is not an absolute role because a main model can also be referenced by another model. That is to say models can have **mutual references**.

This section covers a scenario where we have one main model and two sub models. The main model references the two sub models while the two sub models references each other. We will introduce two ways to define model references: **by code** or **by EDMX** (CSDL). If you would like to create the model by writing code, you can take a look at the first subsection. If you want to create your model by reading an EDMX file, please refer to the second subsection.

### Define Model References by Code
Let us begin by defining the **first sub model** `subModel1`. The model contains a complex type `NS1.Complex1` which will have a structural property of another complex type defined in another model. We also add an EDM reference to `subModel1` pointing to the second model located at `https://model2`. This URL should be the **service metadata location**. The namespace to include is `NS2` and the model alias is `Alias2`.

``` csharp
var subModel1 = new EdmModel();

var complex1 = new EdmComplexType("NS1", "Complex1");
subModel1.AddElement(complex1);

var reference1 = new EdmReference("https://model2");
reference1.AddInclude(new EdmInclude("Alias2", "NS2"));

var references1 = new List<IEdmReference> {reference1};
subModel1.SetEdmReferences(references1);
```

Then we do the same thing for the **second sub model** `subModel2`. This model contains a complex type `NS2.Complex2` and references the first model located at `https://model1`.

``` csharp
var subModel2 = new EdmModel();

var complex2 = new EdmComplexType("NS2", "Complex2");
subModel2.AddElement(complex2);

var reference2 = new EdmReference("https://model1");
reference2.AddInclude(new EdmInclude("Alias1", "NS1"));

var references2 = new List<IEdmReference> {reference2};
subModel2.SetEdmReferences(references2);
```

Now we will add one structural property to the two complex types `NS1.Complex1` and `NS2.Complex2` respectively. The **key** is that the property type is defined in the other model.

``` csharp
complex1.AddStructuralProperty("Prop", new EdmComplexTypeReference(complex2, true));
complex2.AddStructuralProperty("Prop", new EdmComplexTypeReference(complex1, true));
```

After defining the two sub models, we now define the main model. This model contains a complex type `NS.Complex3` and references the two sub models. This complex type contains two structural properties of type `NS1.Complex1` and `NS2.Complex2` respectively.

``` csharp
var mainModel = new EdmModel();

var complex3 = new EdmComplexType("NS", "Complex3");
complex3.AddStructuralProperty("Prop1", new EdmComplexTypeReference(complex1, true));
complex3.AddStructuralProperty("Prop2", new EdmComplexTypeReference(complex2, true));
mainModel.AddElement(complex3);

var references3 = new List<IEdmReference> { reference1, reference2 };
mainModel.SetEdmReferences(references3);
```

### Define Model References by EDMX
As an example, we store the EDMX of the three models in three string constants and create three `StringReader`s as if we are reading the model contents from remote locations.

``` csharp
const string mainEdmx =
@"<?xml version=""1.0"" encoding=""utf-16""?>
<edmx:Edmx Version=""4.0"" xmlns:edmx=""https://docs.oasis-open.org/odata/ns/edmx"">
  <edmx:Reference Uri=""https://model2"">
    <edmx:Include Namespace=""NS2"" Alias=""Alias2"" />
  </edmx:Reference>
  <edmx:Reference Uri=""https://model1"">
    <edmx:Include Namespace=""NS1"" Alias=""Alias1"" />
  </edmx:Reference>
  <edmx:DataServices>
    <Schema Namespace=""NS"" xmlns=""https://docs.oasis-open.org/odata/ns/edm"">
      <ComplexType Name=""Complex3"">
        <Property Name=""Prop1"" Type=""NS1.Complex1"" />
        <Property Name=""Prop2"" Type=""NS2.Complex2"" />
      </ComplexType>
    </Schema>
  </edmx:DataServices>
</edmx:Edmx>";

const string edmx1 =
@"<?xml version=""1.0"" encoding=""utf-16""?>
<edmx:Edmx Version=""4.0"" xmlns:edmx=""https://docs.oasis-open.org/odata/ns/edmx"">
  <edmx:Reference Uri=""https://model2"">
    <edmx:Include Namespace=""NS2"" Alias=""Alias2"" />
  </edmx:Reference>
  <edmx:DataServices>
    <Schema Namespace=""NS1"" xmlns=""https://docs.oasis-open.org/odata/ns/edm"">
      <ComplexType Name=""Complex1"">
        <Property Name=""Prop"" Type=""NS2.Complex2"" />
      </ComplexType>
    </Schema>
  </edmx:DataServices>
</edmx:Edmx>";

const string edmx2 =
@"<?xml version=""1.0"" encoding=""utf-16""?>
<edmx:Edmx Version=""4.0"" xmlns:edmx=""https://docs.oasis-open.org/odata/ns/edmx"">
  <edmx:Reference Uri=""https://model1"">
    <edmx:Include Namespace=""NS1"" Alias=""Alias1"" />
  </edmx:Reference>
  <edmx:DataServices>
    <Schema Namespace=""NS2"" xmlns=""https://docs.oasis-open.org/odata/ns/edm"">
      <ComplexType Name=""Complex2"">
        <Property Name=""Prop"" Type=""NS1.Complex1"" />
      </ComplexType>
    </Schema>
  </edmx:DataServices>
</edmx:Edmx>";

IEdmModel model;
IEnumerable<EdmError> errors;
if (!EdmxReader.TryParse(XmlReader.Create(new StringReader(mainEdmx)), (uri) =>
{
    if (string.Equals(uri.AbsoluteUri, "https://model1/"))
    {
        return XmlReader.Create(new StringReader(edmx1));
    }

    if (string.Equals(uri.AbsoluteUri, "https://model2/"))
    {
        return XmlReader.Create(new StringReader(edmx2));
    }

    throw new Exception("invalid url");
}, out model, out errors))
{
    throw new Exception("bad model");
}
```

The models constructed in either way should be the same. For the complete sample code in this section, please visit `https://github.com/OData/ODataSamples/blob/master/Components/Edm/Program.cs#L122-L228`.