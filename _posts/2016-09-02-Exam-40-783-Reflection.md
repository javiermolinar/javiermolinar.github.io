layout: post
title: C# Exam 40-783 Reflection
---

# System.Reflection

Common classes:

-   **Assembly**

-   **EventInfo**

-   **FieldInfo**

-   **MemberInfo**

-   **MethodInfo**

-   **ParameterInf**

-   **PropertyInfo**

# Assembly Class

Instance methods

-   Assembly.CreateInstance(string) //Load the type/class from the assembly

-   Finalize()

-   GetAllCustomAttributes(bool inherit) //Return an array of objects of type attribute

-   GetModule(string)

Static methods

-   GetAssembly(Type) //Gets the currently loaded assembly in which the specified type is defined.

-   GetCallingAssembly() //Returns the Assembly of the method that invoked the currently executing method.

-   GetExecutingAssembly() //Gets the assembly that contains the code that is currently executing.

-   Load(string assemblyName) //Loads an assembly given its AssemblyName.

-   LoadFrom(string) //Load assembly by pathName

-   LoadFile(string)

The Modules property or the GetLoadedModules, GetModules, and GetModule methods return the list of modules or a specific module defined in the assembly. 
A module is a file that composes the Assembly. This is usually a single DLL or EXE file.

## Find assembly

-   The first context is the load context, which contains the assemblies found by probing. Probing is the process of:
    
    -   Looking in the GAC, the host assembly store.

    -   The folder of the executing assembly
    
    -   The private bin folder of the executing assembly..

-   The second context is the load-from context. This contains the assemblies located in the path passed into the LoadFrom method.

-   The third context is the reflection-only context, which contains the assemblies loaded with the ReflectionOnlyLoad and ReflectionOnlyLoadFrom methods.

### The Disadvantages of Using LoadFrom

Is better use Load(string assemblyName) if is possible.

-   If there is already an assembly loaded with the same identity, then this assembly is returned, not the one found in the file path.

-   If an assembly of the same identity is found in the probing path but in a different location, an exception can occur.

-   Third, you must have FileIOPermissionAccess.Read and FileIOPermissionAccess.PathDiscovery permissions to the file path.

**You would use the LoadFile when there are two assemblies with the same identity in different folders on the computer**

```cs
// path1 and path2 point to different copies of the same assembly on disk:

Assembly assembly1 = Assembly.LoadFrom(path1);
Assembly assembly2 = Assembly.LoadFrom(path2);

// These both point to the assembly from path1, so this is true
Console.WriteLine(assembly1.CodeBase == assembly2.CodeBase);

assembly1 = Assembly.LoadFile(path1);
assembly2 = Assembly.LoadFile(path2);

// These point to different assemblies now, so this is false
Console.WriteLine(assembly1.CodeBase == assembly2.CodeBase);
```

# System.Type class

You can use the typeof() keyword and pass in the name of the type:

```cs
System.Type myType = typeof(int);
```

Common properties:

-   Assembly // Returns the assembly

-   BaseType

-   IsClass

-   Name

...

Useful methods:

-   GetConstructors()

-   GetField(string field)

-   GetInterface(String)

-   GetMethods()

# Read and create attributes

## Read

```cs
object[] attributes = referencedAssembly.GetCustomAttributes(false);
```

## Create attributes

-   Attributes classes are public classes derived from System.Attribute that have at least one public constructor.

Attribute classes have two types of parameters:

-   Positional parameters must be specified every time the attribute is used. Positional parameters are specified as constructor arguments to the attribute class.

-   Named parameters are optional. If they are specified when the attribute is used, the name of the parameter must be used. Named parameters are defined by having a nonstatic field or property.


```cs

//How to define it

[System.AttributeUsage(System.AttributeTargets.Class |
                       System.AttributeTargets.Struct)
]
public class Author : System.Attribute
{
    private string name;
    public double version;

    public Author(string name)
    {
        this.name = name;
        version = 1.0;
    }
}

//Use

[Author("P. Ackerman", version = 1.1)]
class SampleClass
{
    // P. Ackerman's code goes here...
}
```