---
layout: post
title: C# Exam 40-783 Creating Hierarchies
---

# Inheriting from a Base class

In C# a class can only inherit from one base class

```cs
public class Employee : Person
{
    public string DepartmentName { get; set; }
}
```

## Calling parent constructors

Using **:base()** and calling the base constructor parameters

```cs
public class Employee : Person
{
    public string DepartmentName { get; set; }
    public Employee(string firstName, string lastName,
        string departmentName)
            : base(firstName, lastName)
    {

    }
}
}
```

When a constructor uses the base keyword to invoke a base constructor, the second constructor executes **before** the body of the first constructor executes.

## Calling some class constructors

```cs

public Person(string firstName, string lastName): this(firstName)
{
    LastName = lastName;
}
```

When a constructor uses the this keyword to invoke a second constructor in the same class, the second constructor executes **before** the body of the first constructor executes.

### Defining and implementing interfaces

An interface is similar to a class that specifies **properties, methods, and events**, but it doesn’t provide any code to implement them. It forms a contract specifying features that other classes can implement.

If a class implements an interface, it agrees to provide the features defined by the interface. 


```cs
public interface IStudent
{
    // The student's list of current courses. NO IMPLEMENTATION
    List<string> Courses { get; set; }
    // Print the student's current grades.
    void PrintGrades();
}
```

To implemented it:

-   Implicit implementation:

```cs
public class TeachingAssistant : Faculty, IStudent
{
    // Implement IStudent.Courses.
    // The student's list of current courses.
    public List<string> Courses { get; set; }
    // Implement IStudent.PrintGrades.
    // Print the student's current grades.
    public void PrintGrades()
    {
    // Do whatever is necessary...
    }
}
```
-   Explicit implementation

```cs
// Explicit implementation.
public class TeachingAssistant : Faculty, IStudent
{
    List<string> IStudent.Courses{   get { throw new NotImplementedException();}
}
```

Explicit implemented methods cannot be accesed from the instance directly. It's require to cast it to the interface. Explicit implementation is used for implement more than one inferface with same method signature

```cs
TeachingAssistant ta = new TeachingAssistant();
// The following causes a design time error.
ta.PrintGrades();
// The following code works.
IStudent student = ta;
student.PrintGrades();
```

### Common interfaces

-   IComparable

Implements method CompareTo(object obj) it returns -1,0,1

```cs
class Student : IComparable
{
    public string Name { get; set; }
    public string SurName { get; set; }

    public int CompareTo(object obj)
    {
        return this.Name.CompareTo(((Student)obj).Name);
    }
            
}
```

-   IComparers

Implements method Compare. Compares two objects and returns a value indicating whether one is less than, equal to, or greater than the other.

```cs
class Student : IComparer
{
    public string Name { get; set; }
    public string SurName { get; set; }

    public int Compare(object x, object y)
    {
        return ((Student)x).Name.CompareTo(((Student)y).Name);
    }

}
```

-   IEquatable

The IEquatable interface provides that capability by requiring a class to provide an Equals method.

```cs
public bool Equals(object obj)
{
    Employee emp = obj as Employee;
    if (emp != null)
    {
        return Equals(emp);
    }
    else
    {
        return false;
    }
}
```

-   IEnumerable

A class that implements the IEnumerable interface provides a method for a program to enumerate the items that the class contains. Its GetEnumerator method returns an object that implements IEnumerator.

The IEnumerator object provides a Current property that returns the current object

```cs
class Students : IEnumerable
{
    private Student[] students;

    public Students(int num)
    {
        students = new Student[num];
    }

    public IEnumerator GetEnumerator()
    {
        return students.GetEnumerator();
    }       
}
```