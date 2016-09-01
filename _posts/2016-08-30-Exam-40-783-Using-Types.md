---
layout: post
title: C# Exam 40-783 Using Types
---

# Converting between types

## Casting arrays

It works just like other types:

```cs
// Declare and initialize an array of Employees.
Employee[] employees = new Employee[10];
for (int id = 0; id < employees.Length; id++)
    employees[id] = new Employee(id);

// Implicit cast to an array of Persons.
// (An Employee is a type of Person.)
Person[] persons = employees;

// Explicit cast back to an array of Employees.
// (The Persons in the array happen to be Employees.)
employees = (Employee[])persons;

```

**Casting doesn't make a new array** : If you want to make a new array instead of just another reference to an existing array, use Array.Copy or other copy method.

## Converting values

### Parsing methods

Each C# primitive data type has a Parse method to convert from a string to the desire type. It will throw an exception (FormatException) if is not able to make the conversion.

```cs
bool exists = bool.Parse("true");

//This will throw an exception
exists = bool.Parse("somethingelse");
```

### Parsing numbers

-   Parse(string)

-   Parse(string IformatProvider)

-   Parse(string, NumberStyles)

-   Parse(string, NumberStyles, IformatProvider)


#### CultureInfo

Implements IFormatProvider implementing the method GetFormat(Type).

Provides information about a specific culture. The information includes the names for the culture, the writing system, the calendar used, the sort order of strings, and formatting for dates and numbers.

To get the desire culture:

-   CultureInfo.InvariantCulture

-   CultureInfo.GetCultureInfo()

-   CultureInfo.CurrentCulture


One we have it:

- CultureInfo.Calendar

- CultureInfo.DateTimeFormat

- CultureInfo.NumberFormat

```cs

//Invariant culture is a special type of culture which is culture-insensitive.
float num = float.Parse("1.5", CultureInfo.InvariantCulture.NumberFormat);

//1,5 is the german way, so we need to transform an string from that culture to invariant.
float num = float.Parse("1,5", CultureInfo.GetCultureInfo("de-DE").NumberFormat);

//We are pasing a value with a culture format
float num = float.Parse("1,5", CultureInfo.InvariantCulture.NumberFormat);

//num = 15

```

#### NumberStyles

Determines the styles permitted in numeric string arguments that are passed to the Parse and TryParse methods of the integral and floating-point numeric types.

```cs
num = "    -45   ";
val = int.Parse(num, NumberStyles.AllowLeadingSign | 
    NumberStyles.AllowLeadingWhite | NumberStyles.AllowTrailingWhite);
```

Is a flag enum. Some usefull flags are: AllowCurrencySymbol, AllowDecimalPoint,	AllowLeadingSign....

If you provide any NumberStyles values, **any default values are removed**. For example, by defaultdecimal.Parse enables thousands and decimal separators. If you pass the value NumberStyles  .AllowCurrencySymbol to the method, it no longer enables thousands and decimal separators.

### Parsing dates

#### DateTimeStyles

-   AdjustToUniversal: Date and time are returned as a Coordinated Universal Time (UTC). If the input string denotes a local time, through a time zone specifier or AssumeLocal, the date and time are converted from the local time to UTC. 

-   AssumeLocal: If no time zone is specified in the parsed string, the string is assumed to denote a local time.

-   AssumeUniversal:  If no time zone is specified in the parsed string, the string is assumed to denote a UTC.

## System.Convert

The System.Convert class provides more than 300 methods (including overloaded versions) for converting one data type to another.

The System.Convert also provide a method, ChangeType that converts a value into a new type determined by paremeter at run time.

The difference between System.Convert and Parse/Tryparse is that accept null, it will convert null to the default data value.

```cs
bool value = System.Convert(null);
//bool == false, since is the dafault value
```

## System.BitConverter

The System.BitConverter class provides methods to convert values to and from arrays of bytes.The GetBytes method returns an array of bytes representing the value that you pass to it. 

## Boxing/Unboxing

```cs
int value = 10;

//Boxing
object obj = value

//Unboxing. The unboxing has to be to the original type first.
float v2 = (float)(int)obj;
```
## Handling dynamics types

Useful to work when you can not provide complete information about an item at compiling time. It can be used with COM API, from a dynamic language such as IronPython, from the HTML Document Object Model (DOM), from reflection, or from somewhere else in the program. 

The type is a static type, but an object of type dynamic bypasses static type checking. In most cases, it functions like it has type object. At compile time, an element that is typed as dynamic is assumed to support any operation. 

# Manipulating strings

## String class

Strings are a reference type that can be handle as a value type. Inmutable, each operation over a string will result on the creation of a new string in memory.

String fields:

-   Empty

-   Lenght

-   indexer

String Static methods:

-   String.Compare: returns -1,0,1

-   String.Concat: It will result of a new string from two

-   String.Copy

-   String.Equals

-   String.Format

-   String.IsNullOrEmpty

-   String.IsNullOrWhitespace

-   String.Join(string separator, string[] array)

String instance methods:

-   Clone, CompareTo.

-   Contains(string subsString) : Return true if the string contains the subsString

-   EndWith, StartWith (string substring)

-   LastIndexOf, LastIndexOfAny, IndexOf, IndexOfAny (string substring): Return the index of the ocurrence

-   Remove, replace.

-   string[] Split(char[] separators, int count, StringSplitOptions options). StringSplitOptions could be "None" or "RemoveEmptyEntries", Count is the maximun number of substrings.

-   StartWith, SubsTring, ToCharArray, ToLower, ToUpper, Trim, TrimEnd, TrimStart. Trim removes leading and trailing whitespaces

## StringBuilder

Quite handy when its needed to operate with an array on a loop, instead create a new array each time, works over the same reference.

```cs

StringBuilder builder = new StringBuilder();
builder.Append("Nueva");

``` 

## StringWriter

Implements a **TextWriter** for writing information to a string. The information is stored in an underlying StringBuilder.

XmlSerializer use a StringWritter.

Usefull methods:

-   Flush

-   Write - Append a line

-   WriteAsync

-   WriteLine - Append a line and then adds a new line.

## StringReader

Implements a **TextReader** for writing information to a string. The information is stored in an underlying StringBuilder.

XmlSerializer use a StringWritter.

Usefull methods:

-   Peek

-   Read - Read the next character and advances to the following one.

-   ReadLine

-   ReadToEnd - Returns the remaining text from the stringreader to a string

# Formatting  values

## String.Format

Each format item in the formatting string has the following composite format syntax:

{index[,length][:formatString]}

Here, index is the zero-based index of a parameter that follows the formatting string that should be used for this item;

Length is the minimum length of the result for the item; 

FormatString is a standard or custom format string for the item. If length is negative, the value is left-aligned within its length.

```cs
int i = 163;
Console.WriteLine(string.Format("{0} = {1,4} or 0x{2:X}", (char)i, i, i));

//{0} displays argument 0 with default formatting
//{1,4} displays argument 1 in a field at least four characters wide
//{2:X} displays argument 2 with format string X (which displays an integer in hexadecimal)
```

### Formatting strings

Numbers:

C - Currency

D - Decimals

N - Numbers with decimal and thousands separators, N1 one decimal, N2 two decimals...

X - hexadecimal

Dates:

d - Short date

D - Long date

...

Custom

```cs
String.Format("The current date and time: {0:MM/dd/yy H:mm:ss zzz}",thisDate2); 

// The example displays the following output:
//  The current date and time: 06/10/11 15:24:16 +00:00

```


