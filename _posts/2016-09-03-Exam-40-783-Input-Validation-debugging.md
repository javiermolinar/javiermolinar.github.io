layout: post
title: C# Exam 40-783 Input validation and debugging
---

# Validating data

## Using Built-in Functions

-   TryParse

```cs
decimal cost;
if (!decimal.TryParse(costTextBox.Text,NumberStyles.Currency,CultureInfo.CurrentCulture,out cost))
{
// Cost is not a valid currency value. Display an error message.
...
}
```

## String Methods

-   Contains,EndWith,StartWith,IndexOf,IsNullOrEmpty,Split...

## Regular Expressions (System.Text.RegularExpressions.Regex)


-   IsMatch: Returns true if a regular expression matches a string.

-   Match Match(string input): Searches a string for the first part of it that matches a regular expression.

-   Matches: Returns a collection giving information about all parts of a string that match a regular expression.

-   Replace:  Replace Replaces some or all the parts of the string that match a pattern with a new value.   

-   Split:  Splits a string into an array of substrings delimited by pieces of the string that match a regular expression.

The Regex class also provides static versions of these methods that take both a string and a regular expression as parameters.

```cs
//Using static
if (Regex.IsMatch(text, pattern)) valid = true;

//Instantiating
var regex = new Regex(patter);
if(regex.IsMatch(text))
    valid = true;
```

-    Regex.Regex(String, RegexOptions)

RegexOptions:

    -   Compiled : Specifies that the regular expression is compiled to an assembly.

    -   CultureInvariant

    -   IgnoreCase

    -   IgnorePatternWhitespace

-   Match

    -   Properties:

        -   Success : Coincidence has been successfully

        -   Value:  substrings

        -   Capures: collection of all coincidences

```cs
Match match = Regex.Match(input, pattern);
if (match.Success )
   // Report position as a one-based integer.
   Console.WriteLine("'{0}' was found at position {1} in '{2}'.", 
                     match.Value, match.Index + 1, input);

```

## Validate XML

Use XSD file

```cs

XmlReader reader = XmlReader.Create(path);
XmlDocument document = new XmlDocument();

document.Schemas.Add("",xsdPath);
document.Load(reader);

ValidationEventHandler eventHandler = new ValidationEventHandler(ValidationEventHandler);
document.Validate(eventHandler);

```

# Assertions

It will only throw an exception on debug mode

```cs
Debug.Assert(Regex.IsMatch(zip, @"^\d{5}$"));
```

Will throw a message on all builds configuration

```cs
Trace.Assert(Regex.IsMatch(zip, @"^\d{5}$"));
```

# DEBUGGING

## Preprocessor Directives

-   #define and #undef

-   #if, #elif, #else and #endif

```cs
#define DEBUG1

#if DEBUG2

#elif DEBUG1
// Display some debugging information.
...
#else
// Display minimal debugging information.
...
#endif
```

-   #warning and #error: The #warning directive generates a warning that appears in Visual Studio’s Error List.The #error directive is somewhat similar to the #warning directive except it generates an error
instead of a warning

-   #Line: The #line directive enables you to change the program’s line number and optionally the name of the
file that is reported in warnings, errors, and stack traces.

-   #region and #endregion

-   #pragma warning

```cs
#pragma warning disable 0649
public string Description;
#pragma warning restore 0649
```

## Predefined Compiler Constants

-   DEBUG : Normally, DEBUG is defined in debug builds and TRACE is defined in both debug and release builds

-   TRACE

#   Debug and Trace

They provide services that send messages to listener objects.

## Debug and Trace Object

    -   Assert: Checks a boolean condition and throws an exception if it is not true.

    -   WriteLine
    
    -   WriteIf

**Listeners**

To remove the DefaultTraceListener, call the Listeners collection’s Remove method passing it the DefaultTraceListener object

-   ConsoleTraceListener: Sends output to the Console window.

-   EventLogTraceListener: Sends output to an event log.

-   TextWriterTraceListener: Sends output to a stream such as a FileStream. This lets you write output into any file.

# Logging and Event Logs (System.Diagnostics.EventLog)

Logging is the process of instrumenting a program, so it records key events.

Instead of using Debug and Trace to log events, the program can write event records into an ordinary text file.

**writeEntry**

```cs
// Create the source if necessary. (Requires admin privileges.)
if (!EventLog.SourceExists(source))
    EventLog.CreateEventSource(source, log);

// Write the log entry.
EventLog.WriteEntry(source, message, EventLogEntryType.Information, id);
```

# Profiling

Profiling is the process of gathering information about a program to study its speed, memory, disk usage, or other performance characteristics. 

##  Profiling by Hand

```cs
Stopwatch stopwatch = new Stopwatch();
stopwatch.Start();
// Perform the calculations here.
...
Console.WriteLine("Time: " + stopwatch.Elapsed.TotalSeconds.ToString("0.00") +" seconds");
```

## Using Performance Counters

Performance counters track operations system wide to give you an idea of the computer’s activity.

Before you can use a custom performance counter, you need to make one.

The first has type NumberOfItems32, which represents the total number of some event that is counted by a program. The second counter
has type RateOfCountsPerSecond32, which tracks the current number of items per second. When a program increments this counter, the counter automatically updates the counts per second value.

### Create a new category and set of performance counters programmatically

1.   Create a collection of type CounterCreationDataCollection.

2.   Create the counters you want to create as objects of type CounterCreationData and set their necessary properties.

3.   Add the CounterCreationData objects to the collection by calling the collection's Add method.

4.   Call the Create method on the PerformanceCounterCategory class and pass the collection to it.

```cs
// Create a collection of type CounterCreationDataCollection.
System.Diagnostics.CounterCreationDataCollection CounterDatas =  new System.Diagnostics.CounterCreationDataCollection();
// Create the counters and set their properties.
System.Diagnostics.CounterCreationData cdCounter1 =    new System.Diagnostics.CounterCreationData();
System.Diagnostics.CounterCreationData cdCounter2 =    new System.Diagnostics.CounterCreationData();
cdCounter1.CounterName = "Counter1";
cdCounter1.CounterHelp = "help string1";
cdCounter1.CounterType = System.Diagnostics.PerformanceCounterType.NumberOfItems64;

cdCounter2.CounterName = "Counter2";
cdCounter2.CounterHelp = "help string 2";
cdCounter2.CounterType = System.Diagnostics.PerformanceCounterType.NumberOfItems64;

// Add both counters to the collection.
CounterDatas.Add(cdCounter1);
CounterDatas.Add(cdCounter2);
// Create the category and pass the collection to it.
System.Diagnostics.PerformanceCounterCategory.Create(
    "Multi Counter Category", "Category help",
    PerformanceCounterCategoryType.SingleInstance, CounterDatas);
```

## PerformanceCounterType 

The base counter must be immediately after its associated counter in the CounterCreationDataCollection collection your application uses. The following table lists the base counter types with their corresponding performance counter types.

AverageBase goes with : Measure a value over time and display the average of the last two measurements. Needs a base counter.

    - AverageTimer32

    - AverageCount64

CounterMultiBase goes with : A base counter that indicates the number of items sampled.

    - CounterMultiTimer

    - CounterMultiTimerInverse

    - CounterMultiTimer100Ns

    - CounterMultiTimer100NsInverse
