---
title: "Allow serialization of additional properties in odatalib"
description: "Describes serialization and support for additional properties."
author: madansr7
ms.author: saumadan
ms.date: 7/1/2019
ms.topic: article
 
---
# Allow serialization of properties
**Applies To**: [!INCLUDE[appliesto-odataclient](../../includes/appliesto-odatalib-v7.md)]

We now support serializing additional properties which are not advertised in metadata since ODataLib 6.13.0. To achieve this, users just need to turn off the `ThrowOnUndeclaredPropertyForNonOpenType` validation setting when constructing `ODataMessageWriterSettings`.

Here is a full example which is trying to write an extra property `Prop1` in the entity. The implementation of `InMemoryMessage` used in this sample can be found [here](https://github.com/OData/odata.net/blob/ODataV4-7.x/test/FunctionalTests/Microsoft.OData.Core.Tests/InMemoryMessage.cs).

```C#
//Construct the model
EdmModel model = new EdmModel();
var entityType = new EdmEntityType("Namespace", "EntityType", null, false, true, false);
entityType.AddKeys(entityType.AddStructuralProperty("ID", EdmPrimitiveTypeKind.Int32, false));
entityType.AddStructuralProperty("Name", EdmCoreModel.Instance.GetString(isNullable: true), null);

model.AddElement(entityType);

var container = new EdmEntityContainer("Namespace", "Container");
var entitySet = container.AddEntitySet("EntitySet", entityType);

// Create ODataMessageWriterSettings
MemoryStream outputStream = new MemoryStream();
IODataResponseMessage message = new InMemoryMessage { Stream = outputStream };
message.SetHeader("Content-Type", "application/json;odata.metadata=minimal");
ODataUri odataUri = new ODataUri
{
    ServiceRoot = new Uri("https://example.org/odata.svc")
};
ODataMessageWriterSettings settings = new ODataMessageWriterSettings
{
    ODataUri = odataUri
};
settings.Validations &= ~ValidationKinds.ThrowOnUndeclaredPropertyForNonOpenType;

// Write the payload with extra property "Prop1"
var entity = new ODataResource
{
    Properties = new[]
    {
        new ODataProperty { Name = "ID", Value = 102 },
        new ODataProperty { Name = "Name", Value = "Bob" },
        new ODataProperty { Name = "Prop1", Value = "Var1" }
    }
};

using (var messageWriter = new ODataMessageWriter(message, settings, model))
{
    ODataWriter writer = messageWriter.CreateODataResourceWriter(entitySet, entityType);
    writer.WriteStart(entity);
    writer.WriteEnd();

    outputStream.Seek(0, SeekOrigin.Begin);
    var output = new StreamReader(outputStream).ReadToEnd();
    Console.WriteLine(output);
    Console.ReadLine();
}
```

`Prop1` will appear in the payload:

```JSON
{
    "@odata.context":"https://example.org/odata.svc/$metadata#EntitySet/$entity",
    "ID":102,
    "Name":"Bob",
    "Prop1":"Var1"
}
```
