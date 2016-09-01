layout: post
title: C# Exam 40-783Working with Delegates, Events and Exceptions
---

# Working with Delegates

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