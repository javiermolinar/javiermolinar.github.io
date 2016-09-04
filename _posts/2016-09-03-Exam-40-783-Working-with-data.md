layout: post
title: C# Exam 40-783 Working with data
---

# Working with data collections

## Array

```cs

//It needs to know the size at the beginning
int [] myArray = new int[5];

//It is possible to initializate at the beginning
int [] myArray = new int[] { 1, 2, 3 };

//Multidimentional
int [,] myArray = new int [5,5];

//Jagged
//A jagged array is an array whose elements are arrays. 
//The elements of a jagged array can be of different dimensions and sizes. 
//A jagged array is sometimes called an "array of arrays.

int [][] myArray = new int [5][];

```

Array.Rank give you the number of dimensions.

GetLenght(int dimension) give you the lenght for that dimension.

Lenght give you the total number of elements on all dimensions


## SortedList

A SortedList is a collection that contains key\value pairs but it is different from a Hashtable because
it can be referenced by the key or the index and because it is sorted. The elements in the SortedList
are sorted by the IComparable implementation of the key or the IComparer implementation when the
SortedList is created.

```cs
SortedList mySortedList = new SortedList();

mySortedList.Add(3, "three");
mySortedList.Add(2, "second");
mySortedList.Add(1, "first");


foreach (DictionaryEntry item in mySortedList)
{
    Debug.WriteLine(item.Value);
}
```

## Working with ADO.NET

### System.Data.Common.DBConnection

Properties

-   ConnectionString

-   Database

-   DataSource

Methods

-   BeginTransaction

-   BeginTransaction(IsolationLevel)
    
    -   ReadCommitted:  Volatile data cannot be read during the transaction, but can be modified.

    -   ReadUncommitted:    Volatile data can be read and modified during the transaction.

    -   RepeatableRead: Volatile data can be read but not modified during the transaction. New data can be added during the transaction.

-   Close

-   Open : method is used to establish a connection to the database.

### SqlConnection : DBConnection

```cs
SqlConnection cn = new SqlConnection();
cn.ConnectionString = "Server=myServerAddress;Database=myDataBase;User Id=myUsername;Password=myPassword;";
//Open the connection
cn.Open();
cn.Close();
```

```cs
using (SqlConnection cn = new SqlConnection())
{
    cn.ConnectionString = "Server=myServerAddress;Database=myDataBase;User Id=myUsername;Password=myPassword;";
    //Open the connection
    cn.Open();
}
```

### SqlCommand : DBCommand

#### ExecuteNonQuery Method

Queries that do not return a resultset

```cs
SqlConnection cn = new SqlConnection();
cn.ConnectionString = "Server=myServerAddress;Database=myDataBase;
User Id=myUsername;Password=myPassword;";
cn.Open();

SqlCommand cmd = new SqlCommand();
    cmd.Connection = cn;
    cmd.CommandType = CommandType.Text;
    cmd.CommandText = "INSERT INTO Person (FirstName, LastName) " +"VALUES ('Joe', 'Smith')";
    
cmd.ExecuteNonQuery();

cn.Close();
```

#### ExecuteReader Method

Use the ExecuteReader method to retrieve results from the database such as when you use a Select statement.

A DBDataReader object is a **forward-only** resultset that remains connected to the database **the entire time the reader is open**.

```cs
SqlConnection cn = new SqlConnection();
cn.ConnectionString = "Server=myServerAddress;Database=myDataBase;User Id=myUsername;Password=myPassword;";
cn.Open();

SqlCommand cmd = new SqlCommand();
    cmd.Connection = cn;
    cmd.CommandType = CommandType.Text;
    cmd.CommandText = "SELECT * FROM Person";

//First we create the ExecuteReader from the command
SqlDataReader dr = cmd.ExecuteReader();

//If is not empy
if (dr.HasRows)
{
    //Read row by row
    while (dr.Read())
    {
        Debug.WriteLine(string.Format("First Name: {0} , Last Name: {1}", dr["FirstName"], dr["LastName"]));
    }
}

//Close the reader
dr.Close();

//Close connection
cn.Close();
```

```cs
using(SqlDataReader reader = cmd.ExecuteReader()){
    ///
}
```

### ExecuteScalar Method

Perfect when you want a single column for a single row, for example for SUM or AVG

```cs
SqlCommand cmd = new SqlCommand();
cmd.Connection = cn;
cmd.CommandType = CommandType.Text;
cmd.CommandText = "SELECT COUNT(*) FROM Person";
object obj = cmd.ExecuteScalar();

```

### DataSet, DataTable, and DataAdapter

A DataTable is similar to a DBDataReader except that it is disconnected from the database; you can move the cursor back and forth;
and you can update data in the DataTable, reconnect to the database, and commit the changes. A DataSet is a container for one or more DataTables.

```cs
SqlDataAdapter da = new SqlDataAdapter("SELECT * FROM Person", cn);
//Like dataCarrier
DataSet ds = new DataSet();
//We include the new DataTable Person into our dataSet
da.Fill(ds, "Person");
cn.Close();
foreach (DataRow row in ds.Tables[0].Rows)
{
    Debug.WriteLine(string.Format("First Name: {0} , Last Name: {1}",
    row["FirstName"], row["LastName"]));
}
```

### Using transactions

```cs
var connection = GetConnection();
try
{
    using (TransactionScope rootScope = new TransactionScope())
    {
        using (connection)
        {
            connection.Open();
            // All code placed here will take part in the transaction           
        }
        //If Everithing went ok, all changes wer commited if not it performs a rollback
        rootScope.Complete();
    }
}
catch (TransactionAbortedException tae)
{
    Console.WriteLine("Test aborted: " + tae.Message);
}
```

## I/O operations

Drives

-   DriveInfo

Directory

-   Directory (Static)

-   DirectoryInfo

File

-   File (Static)

-   FileInfo 

Path

### DirectoryInfo

-   Properties:

    -   Attributes

    -   CreationTime

    -   Parent

-   Methods:

    -   Create()

    -   CreateSubdirectory(string name)

    -   Delete

    -   EnumerateFiles


```cs
public static void Main() 
{
    // Specify the directories you want to manipulate.
    DirectoryInfo di = new DirectoryInfo(@"c:\MyDir");
    try 
    {
        // Determine whether the directory exists.
        if (di.Exists) 
        {
            // Indicate that the directory already exists.
            Console.WriteLine("That path exists already.");
            return;
        }

        // Try to create the directory.
        di.Create();
        Console.WriteLine("The directory was created successfully.");

        // Delete the directory.
        di.Delete();
        Console.WriteLine("The directory was deleted successfully.");

    } 
}
```

### FileInfo

-   Properties: Directory, DirectoryName, Exists, Lenght, Name

-   Methods:

    -   CreateText: Creates a StreamWriter that writes a new text file.   

    -   AppendAllText:  Creates a StreamWriter that can be used to append text to the file

    -   Copy/CopyTo

    -   Create   : Return a FileStream

    -   Delete

    -   Open:   Returns a FileStream for read, write or read/write

    -   OpenText(): Creates a StreamReader

 ```cs
public static void Main() 
{
    //The file was not “opened,”; the instance is simply enabling you to get information about the file.
    FileInfo fi = new FileInfo(@"c:\MyTest.txt");

    // This text is added only once to the file.
    if (!fi.Exists) 
    {
        //Create a file to write to.
        using (StreamWriter sw = fi.CreateText()) 
        {
            sw.WriteLine("Hello");   
        }	
    }

    // This text will always be added, making the file longer over time
    using (StreamWriter sw = fi.AppendText()) 
    {
        sw.WriteLine("This");        
    }	

    //Open the file to read from.
    using (StreamReader sr = fi.OpenText()) 
    {
        string s = "";
        while ((s = sr.ReadLine()) != null) 
        {
            Console.WriteLine(s);
        }
    }
}
 ```   
## Streams (Working with bytes, need encodinng)

Streams are classes used to contain the contents of a file:

-   FileStream, read and write files

```cs
FileStream fileStream = new FileStream(@"c:\Chapter9Samples\Numbers.txt",FileMode.Create, FileAccess.Write, FileShare.None);
```

**FileMode**
    
    -   Append :Opens a file if it exists and seeks to the end of the file, or creates a new file if it doesn’t exist.

    -   CreateNew: Creates a new file. If the file already exists, an exception is thrown.

    -   Create: Creates a new file. If the file already exists it will be overwritten.

    -   Open

    -   OpenOrCreate

**FileAccess**

    -   Read, Write, ReadWrite

**FileShare**

The FileShare enumeration determines the type of access other streams can have on this file at the
same time you have it open

    - None, Read, write, readwrite, deleted

**FileIOPermissionAccess**

    - NoAccess, read, write, append

-   MemoryStream, read and write on memory

```cs
string path = @"c:\temp\MyTest.txt";

//Create the file.
using (FileStream fs = File.Create(path))
{
    //Since it is a stream.
    byte[] info = new UTF8Encoding(true).GetBytes(value);
    fs.Write(info, 0, info.Length);
}

//Open the stream and read it back.
using (FileStream fs = File.OpenRead(path))
{
    byte[] b = new byte[1024];
    UTF8Encoding temp = new UTF8Encoding(true);
    while (fs.Read(b,0,b.Length) > 0)
    {
        Console.WriteLine(temp.GetString(b));
    }
}
```

## Readers and writters (Working with text, not with bytes)

-   BinaryReader/BinaryWritter

-   StreamReader: TextReader, StreamWriter: TextWritter

-   StringReader: TextReader , StringWritter: TextWritter

-   TextReader, TextWriter


**StreamReader**

The StreamReader class is used to read character input in a particular encoding. The default encoding
is UTF-8.

-   Close

-   Peek

-   int Read() //Returns the next character (as an **integer**)

-   Read(char[],int index,int count) //Read a number of (count) characters from the index.

-   ReadLine //Reads a line of characters and returns a **string**

-   ReadToEnd //Reads all characters from the current position to the end of the file and returns a **string**

```cs
using (StreamReader sr = new StreamReader("TestFile.txt")) 
{
    string line;
    // Read and display lines from the file until the end of 
    // the file is reached.
    while ((line = sr.ReadLine()) != null) 
    {
        Console.WriteLine(line);
    }
}
```

**StringReader**

The StringReader class is similar to the StreamReader class, except that instead of reading a file you read a string.

```cs
StringBuilder stringToRead = new StringBuilder();
stringToRead.AppendLine("Characters in 1st line to read");
stringToRead.AppendLine("and 2nd line");
stringToRead.AppendLine("and the end");

using (StringReader reader = new StringReader(stringToRead.ToString()))
{
    string readText = await reader.ReadToEndAsync();
    Console.WriteLine(readText);
}        
```

## HttpWebRequest/HttpWebResponse

```cs
// Create a request for the URL. 		
WebRequest request = WebRequest.Create ("http://www.contoso.com/default.html");
// Get the response.
HttpWebResponse response = (HttpWebResponse)request.GetResponse ();
// Get the stream containing content returned by the server.
StreamReader reader = new StreamReader (response.GetResponseStream ());
// Read the content.
string responseFromServer = reader.ReadToEnd ();
// Cleanup the streams and the response.
reader.Close ();
response.Close ();
```

# Consuming XML

-   XMLReader : Fast way of reading an XML

    -   Create(Stream)
    
    -   Create(String) //From uri

    -   Create(TextReader) //This could be a StringReader as well

```cs
using (XmlReader reader = XmlReader.Create("hireDate.xml", settings)) {

  // Move to the hire-date element.
  reader.MoveToContent();
  reader.ReadToDescendant("hire-date");

  // Return the hire-date as a DateTime object.
  DateTime hireDate = reader.ReadElementContentAsDateTime();
  Console.WriteLine("Six Month Review Date: {0}", hireDate.AddMonths(6));
}

```    

-   XMLWritter

-   XMLDocument  : In-memory XML Document. Support navigating and editing


# Consuming JSON


NewtonSoft / JavaScriptSerializer
