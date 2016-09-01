layout: post
title: C# Exam 40-783 Managing object life cycle
---

# Managing object life cycle

When the Garbage Collector is executed for collect unused resources and free memory. I will call if exists, the class destructor which is wrapped into a Finalize method.

### Rule 1: Don’t do it (unless you need to).

Implement IDisposable only when :

-   The class owns unmanaged resources.

-   The class owns managed (IDisposable) resources.

### Rule 2: For a class owning managed resources, implement IDisposable (but not a finalizer)

This implementation of IDisposable should only call Dispose for each owned resource. It should not have any other code: no “if” statements, no setting anything to null; just calls to Dispose or Close.

The class should not have a finalizer.

### Rule 3: For a class owning a single unmanaged resource, implement both IDisposable and a finalizer

-   No class should be responsible for multiple unmanaged resources.

-   No class should be responsible for both managed and unmanaged resources.

-   This implementation of IDisposable should call an internal “CloseHandle” method and then end with a call to GC.SuppressFinalize(this).

## Implementing destructor

A destructor is a method with no return type and a name that includes the class’s name prefixed by ~. The GC executes an object’s destructor before permanently destroying it.

-    Destructors can be defined in classes only, not structures.

-    A class can have at most one destructor.

-    Destructors cannot be inherited or overloaded.

-    Destructors cannot be called directly.

-    Destructors cannot have modifiers or parameters.

**The destructor should free only unmanaged resources.**

```cs

~Person()
{
    // Free unmanaged resources here.
    ...
}

```

## Implementing IDisposable

A class that implements the IDisposable interface must provide a Dispose method that cleans up any resources used by the class. The program should call the Dispose method when an object is no longer needed so it can perform this cleanup.

**The Dispose method’s main purpose is to clean up unmanaged resources, but it can also clean up managed resources.**

If the class has only managed resources, it should implement IDisposable but it doesn’t need a destructor.

If the class has only unmanaged resources, it needs to implement IDisposable and needs a destructor in case the program doesn’t call Dispose. A variable can be used to track whether it has been run before.

After freeing resources, **the dispose method should call GC.SuppressFinalize, so the object can skip the finalization queue**.

The using statement begins a block of code that is tied to an object that implements IDisposable. When the block ends, the program automatically calls the object’s Dispose method for you.

```cs
// This is an example of a correct IDisposable implementation.
public sealed class SingleApplicationInstance : IDisposable
{
    private Mutex namedMutex;
    private bool namedMutexCreatedNew;
 
    public SingleApplicationInstance(string applicationName)
    {
        this.namedMutex = new Mutex(false, applicationName, out namedMutexCreatedNew);
    }
 
    public bool AlreadyExisted
    {
        get { return !this.namedMutexCreatedNew; }
    }
 
    public void Dispose()
    {
        namedMutex.Close();
    }
}
```

References:

http://blog.stephencleary.com/2009/08/how-to-implement-idisposable-and.html

## Garbage Collector methods

-   Collect() : Forces an inmediate garbage collection of all generations.

-   KeepAlive() : References the specified object, making it inelifible for garbage collection. The purpose of GC.KeepAlive() is to force the garbage collector to treat the object as still live, thereby preventing it from being collected, and thereby preventing the finalizer from running prematurely.

-   SupressFinalizer() : It's informing the Garbage Collector (GC) that this object was cleaned up fully.