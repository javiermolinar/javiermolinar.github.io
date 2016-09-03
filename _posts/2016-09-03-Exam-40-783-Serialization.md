layout: post
title: C# Exam 40-783 Serialization
---

Serialization is the process of transforming an object into a form that can either be persisted to storage or transferred from one application domain to another.

## Binary Serialization

The **BinaryFormatter** object is used to serialize and deserialize an object. This is found in the
System.Runtime.Serialization.Formatters.Binary namespace. The two main methods you
need to be concerned about are the Serialize and Desearialize methods.

For a class to be **serialized**, you must add the [Serializable] attribute to the top of the class. The following
example creates a class called Person and makes it Serializable.


-   **void Serialize(Stream serializationStream,object graph)**

-   **object Deserialize(Stream serializationStream)**

```cs
//First we need to add the attribute Serializable
[Serializable]
class Person
{
    private int _id;    
}

IFormatter formatter = new BinaryFormatter();

//Create a file to store the serializate information
Stream stream = new FileStream("Person.bin", FileMode.Create, FileAccess.Write,FileShare.None);

//Serialize
formatter.Serialize(stream, person);
stream.Close();
```

```cs
BinaryFormatter formatter = new BinaryFormatter();
addresses = (Person) formatter.Deserialize(fs);
```

All the fields retain their value, even the private _id field. If you want to prevent the private field from being persisted, you can add the **[NonSerialized]** attribute before the field declaration.

## XML Serialization

XML serialization is just as simple to implement as binary serialization. You use the XmlSerializer class in the System.Xml.Serialization namespace. One difference between the XmlSerializer and
BinaryFormatter is that the **XmlSerializer serializes only public properties and fields**.

-   You also do not need to use the [Serializable] attribute when declaring the class.

-   The class must be public

-   Desearialize(Stream), Desearialize(TextReader), Desearialize(XmlReader)

```cs
Person person = new Person();
person.SetId(1);
person.FirstName = "Joe";
person.LastName = "Smith";

//XML serializer
XmlSerializer xmlSerializer = new XmlSerializer(typeof(Person));

StreamWriter streamWriter = new StreamWriter("Person.xml");

xmlSerializer.Serialize(streamWriter, person);
```

-   Attributes

    -   [XmlAttribute]

        -     [XmlAttribute(Namespace = "Make")]

    -   [Xml]

    -   [XmlElement(ElementName = "TaxRate")]

    -   [XmlArray("TeamMembers")]

    -   [XmlArrayItem("MemberName")]

    -   [XmlIgnore]


## DataContractJsonSerializer

You need to do a little more coding with JSON because you **must explicitly put an attribute before each property or field that you want to be serialized**.

```cs
[DataContract]
public class Person
{
    [DataMember]
    private int _id;
    [DataMember]
    public string FirstName;
    [DataMember]
    public string LastName;

    public void SetId(int id)
    {
    _id = id;
    }
}
```

To ignore a field or property, you simply do not put the [DataMember] attribute in front of its declaration.

To serialize an object to JSON, use the DataContractJsonSerializer class.

```cs
Person person = new Person();
person.SetId(1);
person.FirstName = "Joe";
person.LastName = "Smith";

Stream stream = new FileStream("Person.json", FileMode.Create);

DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(Person));

ser.WriteObject(stream, person);
stream.Close();
```

**Instead of calling a Serialize method, you call the WriteObject method.**

## JavaScriptSerializar

Provides serialization and deserialization functionality for AJAX-enabled applications.

To serialize an object, use the Serialize method. To deserialize a JSON string, use the Deserialize<T> or DeserializeObject methods

## DataContractSerializer

 Serializes and deserializes an instance of a type into an XML stream or document using a supplied data contract.

 ```cs
var serializer = new DataContractSerializer(typeof(Response));

Response response = null;

using (var ms = new MemoryStream(Encoding.UTF8.GetBytes(XmlData)))
{
    response = (Response)serializer.ReadObject(ms);
}
 ```