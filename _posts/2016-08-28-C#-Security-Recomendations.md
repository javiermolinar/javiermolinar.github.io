---
layout: post
title: C# Security recommendations
---

# Table of Contents
1. [Use the checked keyword to control the overflow-checking context for integral-type arithmetic operations and conversions](#Use-the-checked-keyword-to-control-the-overflow-checking-context-for-integral-type-arithmetic-operations-and-conversions)
2. [Never, ever hardcode passwords or other sensitive information into your application.](#ever,-ever-hardcode-passwords-or-other-sensitive-information-into-your-application)
3. [Always validate input that is used to generate SQL queries.](#Always-validate-input-that-is-used-to-generate-SQL-queries)
4. [Validate all inputs into your methods.](#Validate-all-inputs-into-your-methods)
5. [Do not display exception information: it provides any would-be attacker with valuable clues](#Do-not-display-exception-information:-it-provides-any-would-be-attacker-with-valuable-clues)
6. [Ensure that your application works while running with the least possible permissions. Few applications require that a user be logged in as an administrator](#Ensure-that-your-application-works-while-running-with-the-least-possible-permissions.-Few-applications-require-that-a-user-be-logged-in-as-an-administrator)
7. [Do not use your own encryption algorithms. Use the System.Security.Cryptography classes](#Do-not-use-your-own-encryption-algorithms.-Use-the-System.Security.Cryptography-classes)
8. [Do not store sensitive information in XML or other configuration files](#Do-not-store-sensitive-information-in-XML-or-other-configuration-files)
9. [Check managed code that wraps native code carefully. Confirm that the native code is secure](#Check-managed-code-that-wraps-native-code-carefully.-Confirm-that-the-native-code-is-secure)
10. [Use caution when you use delegates passed from outside your application](#Use-caution-when-you-use-delegates-passed-from-outside-your-application)

## Use the checked keyword to control the overflow-checking context for integral-type arithmetic operations and conversions

There are two types of conversions: 

-  **Widening conversions**: The destination datatype can hold any value provided by the the source data type.
-  **Narrowing conversions**: The destination datatype cannot hold all possible values of the source type.

An implicit conversion is when the compiler automatically converts one value to another:

```csharp
    short value = 10;
    int value2 = value; // Widening conversions always succedded.
```

An explicit conversion is when we need to use an additional operator or method to explicity tell the compiler how to perform the coversion.

Narrowing conversions can fail if the value to convert cannot fit on the target variable:

```csharp
    int value = 70000;
    short value2 = (short)value; 
```

By default C# do not throw an exception for invalid conversion in integers or floating point types. The value will be **truncated**. For floating point  the value  will be *Infinity*

To throw an exception for invalid narrowing conversion we can use the **Checked** keyword:

```csharp
    checked{
    int value = 70000;
    //It will throw an overflow exception if the conversion fail, like in this scenario.
    short value2 = (short)value; 
    }        
```

## Never, ever hardcode passwords or other sensitive information into your application

-   DO NOT: store passwords in plain text

    Code can be readed later, decompiling it.

    ```csharp    
    var connectionString = "Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey";
    var queueName = "<Your queue name>";
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);       
    ```

-   DO NOT: Encrypt your password with symmetric algorithm

    It's better than storing the password as plain text. But key management is the challenge. Where do you save that key? If it is a database, It won't be difficult for the hacker who got the encrypted password by hacking the database and decrypt it using the same key.

- Hashing

    ...

## Always validate input that is used to generate SQL queries

-    Use of Prepared Statements (Parameterized Queries)

        A typical scenario is the sql injection, very common when the sql is not parametrized:

        ```csharp    
        using (SqlConnection connection = new SqlConnection(connectionString)) {
            SqlCommand command = new SqlCommand(
                "INSERT INTO People VALUES (firstname, lastname ,middlename)",
                connection
            );

            await connection.OpenAsync();
            command.ExecuteNonQueryAsync();
        }   
        ```

        What happen if the user enter the following string as middle name:

        ```csharp    
        '); DELETE FROM People; --  
        ```

        To guard agains SQL Injection, you should never directly use user input in your SQL strings. Instead use parametrized SQL statements

        ```csharp    
        using (SqlConnection connection = new SqlConnection(connectionString)) {
            SqlCommand command = new SqlCommand(
                "INSERT INTO People ([FirstName],[LastName],[MiddleName]) VALUES (@firstname,@lastname,@middlename)",
                connection
            );

            await connection.OpenAsync();

            command.Parameters.AddWithValue("@firstname", "John");
            command.Parameters.AddWithValue("@lastname", "Doe");
            command.Parameters.AddWithValue("@middlename", "Little");

            command.ExecuteNonQueryAsync();
        }   
            ```

- Escaping all User supplied input

    -   Use server-side input validation: Do not rely on client-side validation because it is easily bypassed. Use client-side validation in addition to server-side validation to reduce round trips to the server and to improve the user experience.
    
    -   Validate length, range, format and type: Make sure that any input meets your guidelines for known good input.
    
    -   Use strong data typing. Assign numeric values to numeric data types such as Integer or Double. Assign string values to string data types. Assign dates to the DateTime data type.

- Explicitly Check Input from Form Fields
    
    -   RegularExpressionValidator: Use this control to constrain text input.
    
    -   RangeValidator: Use this control to check the ranges of numeric, currency, date, and string input.
    
    -   CustomValidator: Use this control for custom validation, such as ensuring that a date is in the future or in the past.

##  Validate all inputs into your methods

Users falls into two categories:

-   Innocent users: They have no bad intentions but can make mistakes.

-   Malicious users: Actively seek weaknesses in your application.

It's very important manage data integrity:

-   Entity integrity: States that each entity (database record) should be uniquely identifiable. Primary key or indexes.

-   Domain integrity: Validity of the data that the entity contains, ie: control over the possible allowed values.

-   Referential integrity: Relationship between entities.

-   User-defined integrity: Specific bussines rules that you need to enforce.

How to validate inputs:

- TryParse:

    If something goes wrong, tryParse will return false, instead throw an exception as Parse.

    ```csharp    
        string value = "true";
        bool b;
        if(bool.tryParse(value, out b)){
            //Do something
        }else{
            //Do somthing
        }
    ```

- Convert:

    Enable null values.

    ```csharp    
        int i = Convert.ToInt32(null);
        //i = 0
    ```

- Regular expressions:

    Enable null values.

    ```csharp    
        bool isEmail = Regex.IsMatch(emailString, @"\A(?:[a-z0-9!#$%&'*+/=?^_`{|}~-]+(?:\.[a-z0-9!#$%&'*+/=?^_`{|}~-]+)*@(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?)\Z", RegexOptions.IgnoreCase);

    ```

## Do not display exception information: it provides any would-be attacker with valuable clues

Failure to filter sensitive information when propagating exceptions often results in information leaks that can assist an attacker's efforts to develop further exploits. An attacker may craft input arguments to expose internal structures and mechanisms of the application. Both the exception message text and the type of an exception can leak information. For example, the FileNotFoundException message reveals information about the file system layout, and the exception type reveals the absence of the requested file.

```csharp    
//This will throw an exception, if is not handle it will provide information about the operation system of the server and the location of the files.
using (StreamReader streamreader = File.OpenText(wrongpath)){
}        
```
## Ensure that your application works while running with the least possible permissions. Few applications require that a user be logged in as an administrator

You can use code access permissions

```csharp    
[FileIOPermission(SecurityAction.Demand, AllLocalFiles = FileIOPermissionAccess.Read)]
public void ReadSomeImportantFile(){

}       
```

## Do not use your own encryption algorithms. Use the System.Security.Cryptography classes

- Symmetric algorithm: AesManaged.

- Asymemtric algorithm: RSACryptoServiceProvider and DSACryptoServiceProvider

- Hashing: SHA256Managed.

##  Give your assemblies strong names

Strongly naming an assembly has several benefits:

-   Strong names guarantee uniqueness: Since you have the private key, no other assembly can have the exact strong name.

-   Strong names protect your version lineage: You are the only one who can distribute updates of your assemblies.

-   Strong names provide a strong integrity check: .Net see whether a strong-name assembly has changed since it was signed.

A strong-named assembly can reference only other assemblies that are also strongly named.

## Do not store sensitive information in XML or other configuration files

## Check managed code that wraps native code carefully. Confirm that the native code is secure

## Use caution when you use delegates passed from outside your application

This is extra important on the MessageHub/Messaging!