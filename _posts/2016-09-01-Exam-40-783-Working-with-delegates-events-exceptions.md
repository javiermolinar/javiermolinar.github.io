layout: post
title: C# Exam 40-783Working with Delegates, Events and Exceptions
---

# Working with Delegates
Note that the delegate keyword doesn't always mean that a delegate type is being declared. The same keyword is used when creating instances of the delegate type using anonymous methods.

```cs
//1. We need to declare the delegate, which contains the signature of the methods that can reference.
private delegate float FunctionDelegate(float x);

//2. We must create an instance of our delegate.
private FunctionDelegate TheFunction;

//3. The method must exists and its signature must be the same*
private static float Function1(float x)
{
    return (float)(12 * Math.Sin(3 * x) / (1 + Math.Abs(x)));
}

//4. We assing our delegate instance with the method
TheFunction = Function1;

//5. We use the delegate
float y = TheFunction(1.23f);
```

It's possible to concat as many referenced methods to the delegate af you want.

```cs
Action del1 = Method1;
Action del2 = Method2;
Action del3 = del1 + del2 + del1; // Sum
del3 -= del1 //Substract
```

A reference of a static method is clear, for instance methods it will execute the method of the class that define it.

- **Covariance**

Allow a method return a type derived from the specific type:

```cs

public delegate Person GetPerson(int number);

public Employee GetEmployee(int number){

}

//Employee is a subclass of Person
private GetPerson myDelegate = GetEmployee;

```

- **Contravariance**

Allow a method accept parameters of a base class of the specific parameater

# Built-in delegate types

```cs
public delegate void Action<in T1, in T2>(T1 arg1, T2 arg2)

public delegate TResult Func<in T1, in T2, out TResult>(T1 arg1, T2 arg2)
```

# Anonymous Methods

```cs
private Func<float, float> Function = delegate(float x) { return x * x; };
```

# Lambda Expressions

```cs
Func<float, float> square = (float x) => x * x;
```

# Async lambdas

```cs
runAsyncButton.Click += async ( ) =>
{
     await DoSomethingAsync();
  
};
```

# Working with Events

```cs
//How to defined it, same as delegate
public event Action Overdrawn;
```

Microsoft recommends that all events provide two parameters: the object that is raising the event
and another object that gives arguments that are relevant to the event. The second object should be
of a class derived from the EventArgs class.

```cs
//How to defined it, same as delegate
public event EventHandler ThresholdReached;
```

EventHandler is a build in type of delegate with the following signature:

```cs

public delegate void EventHandler(
	object sender,
	EventArgs e
)

public delegate void EventHandler<TEventArgs>(
	object sender,
	TEventArgs e
)
```

-   **sender**: The source of the event.

-   **e**:   Base class for classes that contain event data, and provides a value to use for events that do not include event data.

```cs

public delegate void EventHandler(
	object sender,
	EventArgs e
)
```

## How its works

```cs

class Program
{
    static void Main(string[] args)
    {
        Counter c = new Counter();

        //We need to assing at least one subscription method to the event
        c.ThresholdReached += c_ThresholdReached;

        c.Add(1);
        
    }

    //The arguments of the delegate are those
    static void c_ThresholdReached(object sender, EventArgs e)
    {
        Console.WriteLine("The threshold was reached.");
        Environment.Exit(0); 
    }
}


class Counter
{    
    //1. We declare the Event type, as a field of the class
    public event EventHandler ThresholdReached;

    public void Add()
    {   
        //This method will trigger the event
        OnThresholdReached(EventArgs.Empty);
    }

    protected virtual void OnThresholdReached(EventArgs e)
    {
        //2. We instantiate an EventHanlder instance.
        EventHandler handler = ThresholdReached;

        //3. We need to check that the handler is not null, since it needs to be at least one subscription method
        if (handler != null)
        {
            //4. The subscriptions methods are executed. The parameters of the EventHandler are the Object how raise the event and the events arguments
            handler(this, e);
        }
    }
}
```

1.  Declare the event type field: public event EventHandler ThresholdReached;

2.  Add some subscription method to it, the method must have the correct signature: void c_ThresholdReached(object sender, EventArgs e)

3.  Instantiate the event.

4.  Trigger the event as a regular delegate, the subscriber method will be called.

An event can be raised only from **within the class that declared it**, so a subclass cannot raise the base class’s events.

The solution that Microsoft uses in the .NET Framework and many other class hierarchies is to give the base class a protected method that raises the event. Then a derived class can call that method to
raise the event.

To Subscribe:   processOrderButton.Click += processOrderButton_Click;

Unsubscribe:    processOrderButton.Click -= processOrderButton_Click;

As with delegates, events without subscribtions assigned will throw an Exceptions

The order of execution of the subscriptions is the same as invocation.

