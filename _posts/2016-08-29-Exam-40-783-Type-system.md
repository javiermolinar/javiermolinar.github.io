---
layout: post
title: C# Exam 40-783
---

# Creating value types

## Working with structs

Structs can contain more than just properties. They can include functions, constructors, constants,
indexers, operators, events, and nested types and can implement interfaces.

-   Unlike classes, structs can be instantiated without using a new operator.

-   Constructors are optional, but if included they must contain parameters. No default constructors are allowed, neither destructors (Since it is a value type, hence in the Stack).

-   Fields cannot be initialized in a struct body.

-   Fields can be initialized only by using the constructor or after the struct is declared.

-   Private members can be initialized using only the constructor.

-   Creating a new struct type without the new operator will not result in a call to a constructor if one is present.

-   If your struct contains a reference type (class) as one of its members, you must call the reference type’s constructor explicitly.

-   A struct cannot inherit from another struct or class, and it cannot be the base of a class. All structs inherit directly from System.ValueType, which inherits from System.Object.

-   A struct can implement interfaces.

-   A struct can be used as a nullable type and can be assigned a null value.

The struct possible modifiers are:

new,public,protected,internal,private

```cs

struct House
{
    private int value;
    //int value2 = 10; That's not possible on an struct
    public int Value { get { return value; } set { this.value = value; } }

    public House (int value)
    {
        this.value = value;
    }
}
```

## Working with enumerations

An enumeration type (also named an enumeration or an enum) provides an efficient way to define a set of named integral constants that may be assigned to a variable. 

```cs
enum Days { Sunday, Monday, Tuesday, Wednesday, Thursday, Friday, Saturday };

//By default the underlying type of each element in the enum is int. You can specify another integral numeric type by using a colon
enum Months : byte { Jan, Feb, Mar, Apr, May, Jun, Jul, Aug, Sep, Oct, Nov, Dec }; 

// enum call Months, using an overidden initializer
enum Months {Jan = 1, Feb, Mar, Apr, May, Jun, Jul, Aug, Sept, Oct, Nov, Dec};

```

```cs
Months thisMonth = Months.Dec;
//You can verify the underlying numeric values by casting to the underlying type
byte monthNumber = (byte)thisMonth;
```

You can assign any values to the elements in the enumerator list of an enumeration type

```cs
enum MachineState
{
    PowerOff = 0,
    Running = 5,
    Sleeping = 10,
    Hibernating = Sleeping + 5
}
```

You can use an enumeration type to define bit flags, which enables an instance of the enumeration type to store any combination of the values that are defined in the enumerator list.

You create a bit flags enum by applying the System.FlagsAttribute attribute and defining the values appropriately so that AND, OR, NOT and XOR bitwise operations can be performed on them.

```cs
[Flags]
enum Days2
{
    None = 0x0,
    Sunday = 0x1,
    Monday = 0x2,
    Tuesday = 0x4,
    Wednesday = 0x8,
    Thursday = 0x10,
    Friday = 0x20,
    Saturday = 0x40
}
```

# Creating reference types

- **Access Modifiers**: All types and type members have an accessibility level, which controls whether they can be used from other code in your assembly or other assemblies. 

    -   public: The type or member can be accessed by any other code in the same assembly or another assembly that references it.
   
    -   private: The type or member can be accessed only by code in the same class or struct.

    -   protected: The type or member can be accessed only by code in the same class or struct, or in a class that is derived from that class.

    -   internal:  The type or member can be accessed by any code in the same assembly, but not from another assembly.

    -   Protected internal: The type or member can be accessed by any code in the assembly in which it is declared, or from within a derived class in another assembly. 

- **Fields**:  A field is a variable of any type that is declared directly in a class or struct. Fields are members of their containing type. Generally, you should use fields only for variables that have private or protected accessibility. 

- **Properties**: A property is a member that provides a flexible mechanism to read, write, or compute the value of a private field. Properties can be used as if they are public data members, but they are actually special methods called accessors. 

    -   A get property accessor is used to return the property value, and a set accessor is used to assign a new value.

    -   Properties that do not implement a set accessor are read only.

    -   **Restricting Accessor Accessibility**:

        ```cs
        public string Name
        {
            get
            {
                return name;
            }
            protected set
            {
                name = value;
            }
        }
        ´´´

        -   You cannot use accessor modifiers on an interface or an explicit interface member implementation.
       
        -   You can use accessor modifiers only if the property or indexer has both set and get accessors. In this case, the modifier is permitted on one only of the two accessors.
        
        -   If the property or indexer has an override modifier, the accessor modifier must match the accessor of the overridden accessor, if any.
       
        -   The accessibility level on the accessor must be more restrictive than the accessibility level on the property or indexer itself.

    -   **Auto-Implemented properties**: Auto-implemented properties make property-declaration more concise when no additional logic is required in the property accessors.

         ```cs
            public string Name {get; set;}; 
        ```

    -   **Expression body definitions**: It is common to have properties that simply return immediately with the result of an expression. 

        ```cs
            public string Name => First + " " + Last; 
        ```

        The property must be **read only**, and you do not use the get accessor keyword.


## Abstract methods

The abstract modifier indicates that the thing being modified has a missing or incomplete implementation.  The abstract modifier can be used with classes, methods, properties, indexers, and events.  Use the abstract modifier in a class declaration to indicate that a class is intended only to be a base class of other classes.  Members marked as abstract, or included in an abstract class, must be implemented by classes that derive from the abstract class.  

Abstract classes have the following features:

-   An abstract class cannot be instantiated.

-   An abstract class may contain abstract methods and accessors.

-   It is not possible to modify an abstract class with the sealed  modifier because the two modifers have opposite meanings.  The sealed modifier prevents a class from being inherited and the abstract modifier requires a class to be inherited.  

-   A non-abstract class derived from an abstract class must include actual implementations of all inherited abstract methods and accessors.

Use the abstract modifier in a method or property declaration to indicate that the method or property does not contain implementation.

Abstract methods have the following features:

-   An abstract method is implicitly a virtual method.

-   Abstract method declarations are only permitted in abstract classes.

-   Because an abstract method declaration provides no actual implementation, there is no method body; the method declaration simply ends with a semicolon and there are no curly braces ({ }) following the signature.  For example:  

´´´cs
public abstract void MyMethod();
´´´

The implementation is provided by an overriding method override, which is a member of a non-abstract class.

It is an error to use the static or virtual modifiers in an abstract method declaration.


´´´cs
abstract class BaseClass   // Abstract class
{
    protected int _x = 100;
    protected int _y = 150;
    public abstract void AbstractMethod();   // Abstract method    
}

class DerivedClass : BaseClass
{
    public override void AbstractMethod()
    {
        _x++;
        _y++;
    }
}
´´´

## Indexed properties

The primary purpose of indexed properties is to allow "array-like access to groups of items"


```cs
class Student
{
    public string Name { get; set; }
    public string SurName { get; set; }
}

class ClassRoom
{
    public string Number { get; set; }
    private readonly Student[] students;
    
    public Student this[int index]
    {
        get { return students[index]; }
        set { students[index] = value; }
    }

    public ClassRoom (int numStudents, string number)
    {
        Number = number;
        students = new Student[numStudents];
    }
}


//How to use it
var classroom = new ClassRoom(10, "4A");
            classroom[1] = new Student()
            {
                Name = "Juan",
                SurName = "Paoli"
            };
```

-   They accept an index value in place of the standard property value parameter.

-   They are identified through the use of the keyword this.

-   Indexed properties can be created in classes or structs.

-   Only one indexed property can exist in a single class or struct.

-   They can contain get and set methods just like other properties.

-   The type of an indexer and the type of its parameters must be at least as accessible as the indexer itself


## Indexers in interfaces

Indexers can be declared on an interface. Accessors of interface indexers differ from the accessors of class indexers in the following ways:

-   Interface accessors do not use modifiers.

-   An interface accessor does not have a body.

```cs
public interface ISomeInterface
{
    //...

    // Indexer declaration:
    string this[int index]
    {
        get;
        set;
    }
}
```

## Generics types and methods

Generics introduce to the .NET Framework the concept of type parameters, which make it possible to design classes and methods that defer the specification of one or more types until the class or method is declared and instantiated by client code

-   Use generic types to maximize code reuse, type safety, and performance.

-   The most common use of generics is to create collection classes.

-   The .NET Framework class library contains several new generic collection classes in the System.Collections.Generic namespace.These should be used whenever possible instead of classes such as ArrayList in the System.Collections namespace.

-   You can create your own generic interfaces, classes, methods, events and delegates.

-   Generic classes may be constrained to enable access to methods on particular data types.

-   Information on the types that are used in a generic data type may be obtained at run-time by using reflection.

### Defining generics types

```cs
//Generic class
public class GenericQueue<T>
{
    //Generic parameter 
    public void Enqueue(T obj);
    //Generic return type
    public T DeQueue();
}
```

### Using generics types

```cs
 GenericList<int> list = new GenericList<int>();
```

### Generic types restrictions

-   where T: struct: The type argument must be a value type.Any value type except Nullable can be specified.See Utilizar tipos que aceptan valores NULL (Guía de programación de C#) for more information.

-   where T : class: The type argument must be a reference type; this applies also to any class, interface, delegate, or array type.

-   where T : new(): The type argument must have a public parameterless constructor.When used together with other constraints, **the new() constraint must be specified last**.

-   where T : <base class name>: The type argument must be or derive from the specified base class.

-   where T : <interface name>: The type argument must be or implement the specified interface.Multiple interface constraints can be specified.The constraining interface can also be generic.

-   where T : U :The type argument supplied for T must be or derive from the argument supplied for U.