![](_page_0_Picture_0.jpeg)

**Prepared By:**

AN

# Sync360 Script Syntax Guide

v.2.1

This document and its supporting materials are proprietary and confidential. The information may not be disclosed to a third party without the express permission of HSO.

![](_page_1_Picture_1.jpeg)

#### **Contributors**

| <b>Name</b> | Value | <br><b>Company</b> |
|-------------|-------|--------------------|
|             |       |                    |

### **Revision History**

| <b>Version</b> | <b>Section Content Change</b>                | <b>Date</b> |
|----------------|----------------------------------------------|-------------|
| 1.1            | First Version of the document was prepared.  |             |
| 1.2            | Second Version of the document was prepared. |             |
| 1.3            | Third Version of the document was prepared.  |             |
| 2.0            | Major update to Functions section added.     | 09.03.2021  |
|                | Minor fixes and updates added.               | 09.15.2021  |

![](_page_2_Picture_1.jpeg)

# **Table of Contents**

| Introduction                                  |  |
|-----------------------------------------------|--|
| General Script Syntax                         |  |
| Variable Types                                |  |
| <u>Constants </u>                             |  |
| Operators                                     |  |
| <u>Functions</u>                              |  |
| Declaration of the Variables                  |  |
| Simple variable                               |  |
| Array variable                                |  |
| Dictionary variables                          |  |
| List variables                                |  |
| Object variables                              |  |
| Flow Control Operations                       |  |
| Break                                         |  |
| Continue ……………………………………………………………………………………………… |  |
| <br>For                                       |  |
| If and Unless                                 |  |
| While                                         |  |
| THEN-ELSE                                     |  |
| Data Operations                               |  |
| Context                                       |  |
| Select                                        |  |
| Create                                        |  |
| Update                                        |  |
| Delete                                        |  |
| Batch                                         |  |
| Transaction                                   |  |
| Search Criteria                               |  |
| Two Operands Condition Operators              |  |
| Contains Condition Operator                   |  |
| Not Operator                                  |  |

![](_page_3_Picture_1.jpeg)

| Column Header 1 | Column Header 2 | Column Header 3 |
|-----------------|-----------------|-----------------|
| Row 1, Cell 1   | Row 1, Cell 2   | Row 1, Cell 3   |
| Row 2, Cell 1   | Row 2, Cell 2   | Row 2, Cell 3   |

| Starts with                                                                                                                                                                                                                                                                                                                                                                            | Ends with / Contains Operations |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| Ends with ………………………………………………………………………………………………                                                                                                                                                                                                                                                                                                                                         |                                 |
| And / Or Operators                                                                                                                                                                                                                                                                                                                                                                     |                                 |
| Exists Condition Operator                                                                                                                                                                                                                                                                                                                                                              |                                 |
| In Condition Operator                                                                                                                                                                                                                                                                                                                                                                  |                                 |
| Between Condition Operator                                                                                                                                                                                                                                                                                                                                                             |                                 |
| Exception Handling Operations                                                                                                                                                                                                                                                                                                                                                          |                                 |
| Exception and OnError                                                                                                                                                                                                                                                                                                                                                                  |                                 |
| Sandbox                                                                                                                                                                                                                                                                                                                                                                                |                                 |
| Structural operations                                                                                                                                                                                                                                                                                                                                                                  |                                 |
| $\label{eq:incu} \text{include} \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, \, \ldots \, $ |                                 |
| Call script                                                                                                                                                                                                                                                                                                                                                                            |                                 |
| Script                                                                                                                                                                                                                                                                                                                                                                                 |                                 |
| <br>Log                                                                                                                                                                                                                                                                                                                                                                                |                                 |
| Extend Functionality                                                                                                                                                                                                                                                                                                                                                                   |                                 |
| Appendix 1                                                                                                                                                                                                                                                                                                                                                                             |                                 |
| Contact Import List                                                                                                                                                                                                                                                                                                                                                                    |                                 |

![](_page_4_Picture_1.jpeg)

# <span id="page-4-0"></span>INTRODUCTION

This document describes the syntax of the Sync360 scripts and capabilities.

![](_page_5_Picture_1.jpeg)

ProServ

# <span id="page-5-0"></span>GENERAL SCRIPT SYNTAX

A script for Sync360 represents an xml file which can be a valid xml file (in case of usage of root element Script as it is described in the clause or a bunch of xml elements.

The script consists of nested elements. Element is a logical document component either begins with a start-tag (for example, <context>) and ends with a matching end-tag (for example, </context>) or consists only of an empty element-tag (for example, <criteria />). The characters between the start- and end-tags, if any, are the element's content, and may contain markup, including other elements, which are called child elements.

```
Example:
<set var="CitiesAndCountries">
     <attr name="Cities">Houston,Phoenix</attr>
     <attr name="Countries">USA,UK,Canada</attr>
\langle/set>
```

Names of elements, as well as names of attributes, cannot contain space characters. The name should begin with a letter or an underline character. The rest of the name may contain as the same characters as well as digit characters.

Attribute is a markup construct consisting of a name/value pair that exists within a start-tag or empty element-tag followed by an element name. Values of attributes should be always embedded in single or double quotes.

**NOTE:** It is necessary to use identical types of quotes for values of attributes in the same tag (please see Cities and Countries in the example above).

Values of attributes can be a template string with expression in curly brackets (or without them). In this case a final value will be obtained by calculation of these expressions and the initial expressions will be substituted with this value. Content of elements rather will be always result of calculation and should be specified without curly brackets. If the template string contains only one expression without other characters, the value will be the result of calculation of this expression.

#### *Example:*

```
<set var="firstname">Joe</set>—a value without expressions.
\leqif condition="contacts. Count eq 0">—an expression in the attribute value.
\leqset var="calculatedvalue">{294+322}\leq/set>—an expression in the element content.
```

![](_page_6_Picture_1.jpeg)

**NOTE:** An expression may include constants and variables. A variable name is case-sensitive (please see *calculatedvalue* in the example above).

The expression type is selected automatically based on the expression value evaluation, but it can be converted to a necessary type by using special construction 'as' (available types are enumerated in the clause **Ошибка! Источник ссылки не найден.**). The following examples give the same result: <set var="counter">{2}</set> <set var="counter">{2 as 'int'}</set>

Moreover, variable values may be a simple value, an array and a dictionary (declaration and modification of variable values are described in the clause 7).

A value of an array item can be received by specifying the array name and index of the necessary element inside square brackets. Indexes start from zero.

```
Example:
<log>First account: {account[0]}</log>
```

The random element of an array may be selected if the name of an array is specified with empty square brackets.

```
Example:
<set var="crmservers">{[crm,crm4,crm5]}</set>
<log>Random CRM server: {crmservers[]}</log>
```

A value of a dictionary item can be received by specifying the dictionary name and name of the necessary element in quotes and inside square brackets or by specifying name of the necessary element followed by the dictionary name with a dot.

```
Examples:
<log>City: {account.city}</log>
<log>City: {account["city"]}</log>
```

If the corresponding element is not found, search of a method with this name will be produced via Reflection and, if the method is found, then it will be applied to the dictionary. Otherwise, an exception will be thrown.

```
Example:
<log>Contact is found: {contacts.ContainsKey(contactid)}</log>
```

![](_page_7_Picture_1.jpeg)

# <span id="page-7-0"></span>VARIABLE TYPES

Variables in Sync360 can be defined with one of types listed in **Table 1**.

| <b>Type</b> | <b>Description</b>                                                                                                                            |
|-------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| char        | The char keyword is used to declare a Unicode character (Unicode 16-bit)                                                                      |
|             | character).                                                                                                                                   |
| char[]      | Array of char variables.                                                                                                                      |
| string      | The string type represents a sequence of zero or more Unicode characters.                                                                     |
| string[]    | Array of string variables.                                                                                                                    |
| byte        | The byte keyword is used to declare variables in range from $0$ to 255                                                                        |
|             | (Unsigned 8-bit integer).                                                                                                                     |
| byte[]      | Array of byte variables.                                                                                                                      |
| int         | The int keyword is used to declare variables in range from -2,147,483,648 to 2,147,483,647 (Signed 32-bit integer).                           |
| int[]       | Array of int type variables.                                                                                                                  |
| long        | The long keyword is used to declare variables in range from - 9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 (Signed 64-bit integer). |
| long[]      | Array of long type variables.                                                                                                                 |
| double      | The double keyword signifies a simple type that stores 64-bit floating-point values.                                                          |
|             | values.                                                                                                                                       |
| double[]    | Array of double type variables.                                                                                                               |
| bool        | The bool type presents a boolean value, its properties can be defined in script.                                                              |
| bool[]      | Array of bool variables.                                                                                                                      |
| date        | The date type presents an instant in time, typically expressed as a date and time of day.                                                     |
| date[]      | Array of date type variables.                                                                                                                 |
| List        | Represents a strongly typed list of objects that can be accessed by index.                                                                    |
|             | <b>Standard .NET List</b>                                                                                                                     |
| Dictionary  | Represents a collection of keys and values. Standard .NET Dictionary.                                                                         |
| Object      | Generic Object, its properties can be defined in script.                                                                                      |

|              | Column 1     | Table 1. Variable types |                 |
|--------------|--------------|-------------------------|-----------------|
| Data Point 1 | Data Value 1 | Variable Type A         | Additional Info |

![](_page_8_Picture_1.jpeg)

# <span id="page-8-0"></span>**CONSTANTS**

There are three predefined constants in Sync360 that listed in **Table 2**.

| <b>Type</b>                             | <b>Description</b>                                                                                                            |
|-----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| "true"                                  | <b>True.</b> This constant represents a Boolean value True.                                                                   |
| $ $ "false"                             | <b>False.</b> This constant represents a Boolean value False.                                                                 |
| "null"                                  | <b>Null.</b> This constant represents Null value                                                                              |
| <b>Type</b>                             | <b>Description</b>                                                                                                            |
| $\mathbf{u} + \mathbf{u}$               | Addition. This operator adds the second operand to the first operand.                                                         |
|                                         | Supported variable types: int, long, double, string.                                                                          |
|                                         | Subtraction. This operator subtracts the second operand from the first                                                        |
| $\mathbf{u} \perp \mathbf{u}$           | operand.                                                                                                                      |
|                                         | Supported variable types: int, long, double, string, bool, date and object.                                                   |
| $gt$ , "gt"                             | Greater than. This operator returns true if the first operand is greater than             the second operand.                 |
|                                         | Supported variable types: int, long, double, string and date.                                                                 |
| $lt$ , "lt"                             | Less than. This operator returns true if the first operand is less than             the second operand.                       |
|                                         | Supported variable types: int, long, double, string and date.                                                                 |
| $\geq$ , "ge"                           | Greater or equal. This operator returns true if the first operand is greater             than or equal to the second operand. |
|                                         | Supported variable types: int, long, double, string and date.                                                                 |
| $\leq$ , "le"                           | Less or equal. This operator returns true if the first operand is less than             or equal to the second operand.       |
|                                         | Supported variable types: int, long, double, string and date.                                                                 |
| "&&", "and"                             | And. This operator performs a logical-AND of its bool operands.                                                               |
|                                         | Supported variable types: bool.                                                                                               |
| $"  ", "or"$                            | Or. This operator performs a logical-OR of its bool operands.                                                                 |
|                                         | Supported variable types: bool.                                                                                               |
| \"!\", \"not\"                          | <b>Not.</b> This operator is a unary operator that negates its operand. It returns true only if its operand is false.         |
|                                         | Supported variable types: bool.                                                                                               |
|                                         |                                                                                                                               |
| \"[]\", \"\".\"                         | Index. This operator returns an array element at the specified index or a dictionary element with the specified string key.   |
|                                         |                                                                                                                               |
| $\\mathbf{u} \\circ \\circ \\mathbf{u}$ | <b>Ternary Operation.</b> This operator returns the first calculated value in chain of expressions.                           |
|                                         | <b>Example:</b> If variable is not null displays variable value, else display text 'MyVar was not set'.                       |
|                                         | 'MyVar was not set'                                                                                                           |
|                                         | {Globals['MyVar'] ?? 'MyVar was not set'}                                                                                     |

#### Table 2. Constants

![](_page_9_Picture_1.jpeg)

# <span id="page-9-0"></span>**OPERATORS**

Table 3. Operators

![](_page_10_Picture_6.jpeg)

![](_page_11_Picture_1.jpeg)

![](_page_11_Picture_2.jpeg)

# <span id="page-11-0"></span>**FUNCTIONS**

Functions in Sync360 are methods for set of predefined objects. There are only two predefined functions that doesn't bind to an object.

**NOTE:** Helper functions (sometimes simply called "helpers") are usually functions that wrap useful functionality that you're going to reuse repeatedly.

| Type        | Description                                                                                                                                                                                                                                                                                                                                                                                                    |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| isSet       | Is Set. This function returns true if the operand has value (not null).                                                                                                                                                                                                                                                                                                                                        |
| Count       | Count. This function returns the number of elements in the collection.                                                                                                                                                                                                                                                                                                                                         |
| Description | The function reads through a CSV file and creates the list of strings where every line is a separate record. The number of parameters specified may vary. And this overloaded function may be used several times throughout the script. If some parameters are not specified, the default values listed in the script are used instead, e.g., $Read(\text{string path}, \text{string quote} = DefaultQuote)$ . |
| Parameters  | Csv.Read(param1, param2, param3, param4, param5, param6, param7, param8)                                                                                                                                                                                                                                                                                                                                       |
|             | string path—path to a file and its name.                                                                                                                                                                                                                                                                                                                                                                       |
|             | Encoding encoding—the type of encoding used in a file.                                                                                                                                                                                                                                                                                                                                                         |
|             | string delimiter—delimiter used in a file.                                                                                                                                                                                                                                                                                                                                                                     |
|             | string quote—multiline text delimiter used in a file.<br>$\bullet$                                                                                                                                                                                                                                                                                                                                             |
|             | string comment—comment symbol used in a file.                                                                                                                                                                                                                                                                                                                                                                  |
|             | bool trim—checks whether value trimming is required or not.                                                                                                                                                                                                                                                                                                                                                    |
|             | bool has Header—checks whether a CSV file has a header or not.                                                                                                                                                                                                                                                                                                                                                 |
|             | int topLinesToSkip—the number of lines to skip in a file.                                                                                                                                                                                                                                                                                                                                                      |

Table 4. Functions

![](_page_12_Picture_1.jpeg)

|           | <b>Example</b>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <b>Description</b> |
|-----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| Csv.Write | <b>Parameters</b><br>Write(param1, param2, param3, param4, param5, param6,<br>param7, param8)<br>string path—a path to a file and its name.<br>$\bullet$<br>Encoding encoding—the type of encoding used in<br>$\bullet$<br>a file.<br>string delimiter—delimiter used in a file.<br>٠<br>string quote—multiline text delimiter used in a file.<br>$\bullet$<br>string comment—comment symbol used in a file.<br>$\bullet$<br>bool trim—checks whether value trimming is<br>required or not.<br>bool hasHeader—checks whether a CSV file has a<br>header or not.<br>int topLinesToSkip—the number of lines to skip in a<br>$\bullet$<br>file.<br> |                    |
| Csv.Open  | <b>Description</b>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |                    |

![](_page_13_Picture_1.jpeg)

| Function Name             | Description                                                                                                                                                                                                                                                                          |
|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                           | <b>Description</b> : The function writes a specified string to a file. It is usually used for logging. The last parameter instructs whether to append to the file.                                                                                                                   |
| FileUtils.WriteToFile     | <b>Parameters</b> : WriteToFile(string, string, bool)       - string fileName: Name of the file where the text will be added.       - string text: Text that will be added to a file.       - bool isAppend: Appends a text to a file.                                               |
|                           | <b>Example</b> : {FileUtils.WriteToFile("c:\\temp\\log.txt", "Test", true)}                                                                                                                                                                                                          |
| FileUtils.ReadIdsFromFile | <b>Description</b> : The function is used to read predefined GUIDS from a text file. GUIDs should be placed line by line. The result will be an array variable.<br><b>Parameters</b> : ReadIdsFromFile(string)       - string fileName: Name of the file from which GUIDs are taken. |
|                           | <b>Example</b> : var Ids = {FileUtils.ReadIdsFromFile(".\\UserGuids.txt")}                                                                                                                                                                                                           |
| FileUtils.ReadFile        | <b>Description</b> : Opens a file, reads the contents of the file into a byte array, and then closes the file.<br><b>Parameters</b> : ReadFile(string)                                                                                                                               |

![](_page_14_Picture_1.jpeg)

|                            | Method Details                                                                                       |
|----------------------------|------------------------------------------------------------------------------------------------------|
|                            | <b>string fileName</b> : A name of the processed file.<br>$\bullet$                                  |
|                            | <b>Example</b>                                                                                       |
|                            | {FileUtils.ReadFile(".\UserGuids.txt")}                                                              |
|                            | <b>Description</b>                                                                                   |
|                            | Open a file, read the contents into a byte array, convert it to a Base64 string, and close the file. |
| FileUtils.ReadFileAsBase64 | <b>Parameters</b>                                                                                    |
| string fileName            | A name of the processed file.                                                                        |
|                            | <b>Example</b>                                                                                       |
|                            | {FileUtils.ReadFileAsBase64(".\UserGuids.txt")}                                                      |
| FileUtils.DirectoryEntries | <b>Parameters</b>                                                                                    |
| string path                | A path to a required directory.                                                                      |
|                            | <b>Description</b> : Returns the list of all files and subdirectories in a specified path.           |
| FileUtils.MoveFile         | <b>Parameters</b>                                                                                    |
| string sourceFileName      | A path to a source file.                                                                             |
| string destFileName        | A new path for the file.                                                                             |
|                            | <b>Description</b> : Specify a new file name, or move to a different directory.                      |
|                            | <b>Example</b> :                                                                                     |
|                            | {FileUtils.MoveFile("c:\\temp\\log.txt", $c:\\temp2\\log.txt$ )}                                     |

![](_page_15_Picture_1.jpeg)

| Description                                                                                                            |  |
|------------------------------------------------------------------------------------------------------------------------|--|
| Returns the names of files (including their paths) that match the specified search pattern in the specified directory. |  |
| Parameters                                                                                                             |  |
| FileUtils.GetFiles(string folder, string searchPattern)                                                                |  |
| - string folder                                                                                                        |  |
| - string searchPattern                                                                                                 |  |
| Example                                                                                                                |  |
| <span>{FileUtils.GetFiles(@"c:\", "c*")}</span>                                                                        |  |
| Description                                                                                                            |  |
| The function archives files into a zip-archive in a selected folder.                                                   |  |
| Parameters                                                                                                             |  |
| FileUtils.Zip(string sourceFolder, string zipFile)                                                                     |  |
| - string sourceFolder: A path to the source folder.                                                                    |  |
| - string zipFile: A file that will be archived.                                                                        |  |
| Example                                                                                                                |  |
| Description                                                                                                            |  |
| The function extracts files from a zip-archive in a selected folder.                                                   |  |
| Parameters                                                                                                             |  |
| FileUtils.Unzip(string zipFile, string extractFolder)                                                                  |  |
| - string zipFile: A name of an archive.                                                                                |  |
| - string extractFolder: A path to the extraction folder.                                                               |  |
| Example                                                                                                                |  |
|                                                                                                                        |  |
| Description                                                                                                            |  |
| Converts plain text to an HTML string or document.                                                                     |  |

![](_page_16_Picture_1.jpeg)

| Method/Description                            | Parameters & Examples                                                                                                            |
|-----------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| <b>ToPlainText</b>                            | Converts an HTML string to plain text.<br>Parameters: ToPlainText(string html)<br>Example: <i>Conversion example here.</i>       |
|                                               | Converts an HTML document to a plain text string.<br>Parameters: ToPlainText(HtmlDocument doc)                                   |
| <b>Description</b>                            | Sets the use of default credentials for each HTTP connection.                                                                    |
|                                               | <b>Http.SetUseDefaultCredentials(bool value)</b><br>Example: <i>Set true to enable default credentials.</i>                      |
| <b>Description</b>                            | Sets a client encoding for connections.                                                                                          |
|                                               | <b>Http.SetClientEncoding(string encoding)</b><br>Example: <i>Set "UTF-8" as the client encoding.</i>                            |
| <b>Description</b>                            | The HTTP GET method requests a representation of the specified resource.                                                         |
|                                               | <b>Http.Get(string url, string method)</b><br>Parameters: Get(string url)                                                        |
|                                               | Description                                                                                                                      |
| string username-retrieves requested username. | $\bullet$                                                                                                                        |
| string password—retrieves requested password. |                                                                                                                                  |
| string domain-retrieves requested password.   |                                                                                                                                  |
| object headers—retrieves specific headers.    |                                                                                                                                  |
| <b>Example</b>                                | $<$ set             var="response">{http.get('http://www.example.com')} <br set>                                                 |
| <b>Description</b>                            | The HTTP PUT request method creates a new resource or replaces a representation of the target resource with the request payload. |
| <b>Parameters</b>                             | Http.Put: Put(string url, string data, object headers)                                                                           |
| string url-URL used in the request.           | string data—data sent as parameters to URL in the request.                                                                       |
| object headers                                |                                                                                                                                  |
| <b>Example</b>                                | var="response">{http.put('http://www.example.com',<br>'some data', 'headers go here') }                                          |
| <b>Description</b>                            | The HTTP POST method is used for sending data to a server.                                                                       |
| <b>Parameters</b>                             | Http.Post: Post(string url, string login, string password, string domain, string data, object headers)                           |
| string url-URL used in the request.           | string login.                                                                                                                    |
|                                               | string password.                                                                                                                 |
|                                               | string domain.                                                                                                                   |
|                                               | string data-data sent as parameters to URL in the request.                                                                       |
|                                               | object headers.                                                                                                                  |
| <b>Example</b>                                | {http.post('http://www.example.com',<br>'login', 'password', 'domain', 'data', 'headers')}                                       |

• string url—URL used in the request.

![](_page_17_Picture_1.jpeg)

![](_page_18_Picture_1.jpeg)

|                  | HTTP Methods                                                                                 |                                                                                                        |
|------------------|----------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| PATCH Method     | Parameters                                                                                   | Patch(string, string, string, string, string, object)                                                  |
|                  |                                                                                              | string url-URL used in the request.                                                                    |
|                  |                                                                                              | string login. $\bullet$                                                                                |
|                  |                                                                                              | string password. $\bullet$                                                                             |
|                  | Example                                                                                      |                                                                                                        |
|                  | $ setvar="response" {http.patch('http://www.example.com', 'some data', 'headers go here')} $ |                                                                                                        |
| Description      | Downloads the file that URL contains, e.g., page, archive, document.                         |                                                                                                        |
|                  | Download Method                                                                              | Http.Download<br>Download(string)                                                                      |
|                  |                                                                                              | string url—an address that contains the required file.                                                 |
|                  | Example                                                                                      |                                                                                                        |
|                  | $ set var="response" {http.download('http://www.example.com')}$                              |                                                                                                        |
| GetRaw Method    | Parameters                                                                                   | Http.GetRaw<br>GetRaw(string, int, int)                                                                |
|                  |                                                                                              | string url<br>int sendTimeout<br>int receiveTimeout                                                    |
|                  |                                                                                              | The Get.Raw method requests a representation of the specified resource but in unchanged, literal form. |
| SetCookie Method | Set cookie for the specified resource.                                                       |                                                                                                        |
|                  | <b>SetCookie</b>                                                                             |                                                                                                        |
| Parameters:      | string url<br>string cookie                                                                  |                                                                                                        |
| Description:     | Sets a cookie for the specified resource.                                                    |                                                                                                        |
|                  | <b>GetCookie</b>                                                                             |                                                                                                        |
| Parameters:      | string url                                                                                   |                                                                                                        |
|                  | <b>Json.ToJson</b>                                                                           |                                                                                                        |
| Parameters:      | object value                                                                                 |                                                                                                        |
| Description:     | Converts an object to JSON format.                                                           |                                                                                                        |
| Example:         | {Json.ToJson("DynamicDictionary")}                                                           |                                                                                                        |
|                  | <b>Json.ToAsciiJson</b>                                                                      |                                                                                                        |
| Parameters:      | object value                                                                                 |                                                                                                        |
| Description:     | Converts an object to ASCII JSON format and removes non-ASCII symbols.                       |                                                                                                        |
| Example:         | {Json.ToAsciiJson("DynamicDictionary")}                                                      |                                                                                                        |
|                  | <b>Json.FromJson</b>                                                                         |                                                                                                        |
| Parameters:      | string value-JSON string that will be converted.                                             |                                                                                                        |
| Description:     | Converts JSON to a dictionary.                                                               |                                                                                                        |

 $\mathsf I$ 

![](_page_19_Picture_1.jpeg)

![](_page_20_Picture_1.jpeg)

| Function              | Description                                                                                                                                                                                                            | Parameters                  | Example                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------|--------------------------------------------------------------------------------------------------|
|                       | <b>Json.FromJson</b>                                                                                                                                                                                                   | Converts JSON to an object. | <set var="test1">{Json.FromJson(string)}</set><br/><log>(test1.GetType()) - (test1)</log>        |
| Json.GetDictionary    | <b>Description:</b> Function for getting a dictionary from JSON.                                                                                                                                                       | string value                | <set var="test1">{Json.GetDictionary(string)}</set><br><log>(test1.GetType()) - (test1)</log>    |
| Json.GetArrayFromJson | <b>Description:</b> Function for getting an array from JSON.                                                                                                                                                           | string value                | <set var="test1">{Json.GetArrayFromJson(string)}</set><br><log>(test1.GetType()) - (test1)</log> |
| Utils.NewGuid         | <b>Description:</b> Generates a new GUID.                                                                                                                                                                              |                             |                                                                                                  |
| Utils.Split           | <b>Description:</b> This function identifies the substrings in a string array that are delimited by one or more characters specified in an array, then places the substrings into a specified Unicode character array. |                             | <set var="Names">Roman; Joe; Michael</set><br><set var="NamesAr">{Utils.Split(Names,';')}</set>  |

![](_page_21_Picture_1.jpeg)

| Company Name |
|--------------|
| ProServ      |

| Function | Description                                                                                         | Parameters       | Example                                              |
|----------|-----------------------------------------------------------------------------------------------------|------------------|------------------------------------------------------|
|          | This function combines array values into a string with specified delimiter.                         |                  |                                                      |
| Join     |                                                                                                     | Array, Delimiter | {'Roman','Joe','Michael'}<br>Utils.Join(NamesAr,';') |
| To Upper | This function returns a copy of this string converted to uppercase.                                 | string           |                                                      |
| To Lower | This function returns a copy of this string converted to lowercase.                                 | string           |                                                      |
| New Line | This function returns the NewLine character.                                                        |                  |                                                      |
| Now      | This function returns a date type object set to the current date and time, expressed as local time. |                  |                                                      |

![](_page_22_Picture_1.jpeg)

| Function              | Description                                                                                                                                                                      |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                       | This function returns a new string in which all occurrences of a specified Unicode String in the current string are replaced with another specified Unicode character or String. |
| Utils.Replace         |                                                                                                                                                                                  |
| <b>Parameters:</b>    | string - The string to be searched and replaced.                                                                                                                                 |
| <b>Example:</b>       | This is an example<br>${Utils.Replace(str1,'This','Here')}$                                                                                                                      |
| Utils.IsKeyExist      | Checks whether the key exists in a dictionary or not.                                                                                                                            |
| <b>Parameters:</b>    | IsKeyExist(object, string) - object dict-the required dictionary; string key-the line that contains a key                                                                        |
| <b>Example:</b>       | {Utils.IsKeyExist("DictionaryExample", $reg4y736) }$                                                                                                                             |
| Utils.ParseGUID       | Converts the string representation of a GUID to the equivalent Guid structure.                                                                                                   |
| <b>Parameters:</b>    | ParseGuid(string) - string guid-the required GUID.                                                                                                                               |
| Utils.TextuallyEquals | Compares two objects:                                                                                                                                                            |

![](_page_23_Picture_1.jpeg)

| Utility Function                          | Description & Parameters                                                                                                                                                                                                  |   | Method                          | Description                                                    | Parameters                                                                                                                                                                        | Example                 |
|-------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---------------------------------|----------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------|
|                                           | If objects are of the same type, it compares them as<br>$\bullet$<br>they are. If objects are of different types, it converts them into strings and then compares the strings.                                            | 1 | DateToString(DateTime, string)  | Converts a DateTime to a string based on the specified format. | DateTime date: The exact date to be converted. string format                                                                                                                      |                         |
|                                           | <b>Parameters:</b> TextuallyEquals(object, object, bool)<br>object one—the first object in a pair.<br>object two—the second object in a pair.<br>bool ignoreCase—manages case sensitivity.<br><b>Example</b>              | 2 | StringToDate(string, string)    | Converts a string to DateTime based on the specified format.   | string date: The exact string to be converted. string format                                                                                                                      |                         |
| Utils. Right<br>Utils. Left               | <b>Description:</b> Manipulates string by taking rightmost or leftmost characters. <b>Parameters:</b> Right(string, int)<br>string str.<br>int charCount Left(string, int)<br>string str.<br>int charCount <b>Example</b> | 3 | Specifies Kind for DateTime     | Applies a specified timezone to date.                          | Utils.SpecifyKindUtc(DateTime) DateTime date Utils.SpecifyKindLocal(DateTime) Utils.SpecifyKindInspecifi(DateTime): DateTime date SpecifyKindUnspecified(DateTime): DateTime date |                         |
| Utils. Encode<br>Utils.Decode             | <b>Description:</b> Encode to/Decode from the standard HTML encoding. <b>Parameters:</b> Encode(string)<br>string str Decode(string)<br>string str <b>Example</b>                                                         | 4 | Utils.Eval(string)              | The function executes a provided string as a command.          | string expression                                                                                                                                                                 | {Utils.Eval(checkCond)} |
| Utils. DateToString<br>Utils.StringToDate | <b>Description:</b> Converts date to string and vice versa.                                                                                                                                                               | 5 | Utils.GetRandom(string, object) | The function gets random values of random types.               | string type object max                                                                                                                                                            |                         |

![](_page_24_Picture_1.jpeg)

![](_page_25_Picture_1.jpeg)

| Method      | Example                                                                                                                         | Description                                                                                                             | Parameters                 |
|-------------|---------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------|----------------------------|
|             | <b>Xml.Load(string, int)</b>                                                                                                    | Loads an XElement from a file, optionally preserving white space, setting the base URI, and retaining line information. | string uri<br>int options  |
|             | <b>Xml.Load(fileName)</b>                                                                                                       | Loads an XElement from a file.                                                                                          |                            |
| Xml.LoadEnc | LoadEnc(string)                                                                                                                 |                                                                                                                         | string uri                 |
|             | <b>Parse(string, int)</b>                                                                                                       | Load an XElement from a string that contains XML, optionally preserving white space and retaining line information.     | string text<br>int options |
|             | <br />             <math>&lt;</math>root<math>&gt;</math> Test data Second text $data$ $<$ $add$ $<$ /root><br>$[]]$ $]]\times$ |                                                                                                                         |                            |

![](_page_26_Picture_1.jpeg)

| <b>Xml.Select</b>  |                                                                   |
|--------------------|-------------------------------------------------------------------|
| Description        | Selects and returns values from an XML element as an array.       |
| <b>Parameters:</b> |                                                                   |
| XElement xml       | The XML element to select from.                                   |
| string expression  | The expression used for selection.                                |
| IDictionary fields | A dictionary of additional fields if required.                    |
| <b>Xml.ToXml</b>   |                                                                   |
| Description        | Passes an object and a root element name to return an XML string. |
| Parameters:        | ToXml(object value, string rootElementName)                       |
| <b>Xml.FromXml</b> |                                                                   |
| Description        | Parses an XML string and returns the corresponding object.        |
| Parameters:        | FromXml(string xml)                                               |
| <b>Math.*</b>      |                                                                   |
| Description        | Used for common mathematical functions. Refer to MSDN.            |
| Link:              | http://msdn.microsoft.com/en-us/library/system.math.aspx          |
| <b>File.*</b>      |                                                                   |
| Description        | Used for operations with files. Refer to MSDN.                    |
| Link:              | http://msdn.microsoft.com/en-us/library/system.io.file.aspx       |
| <b>Path.*</b>      |                                                                   |
| Description        | Used for operations with the filesystem. Refer to MSDN.           |

![](_page_27_Picture_1.jpeg)

![](_page_27_Picture_2.jpeg)

| Topic       | Description                                                                                                                              | Reference URL                                                     |
|-------------|------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------|
|             | <b>Encoding.</b> This .NET object is used for character and text encoding, for the list of supported methods and properties refer to     | http://msdn.microsoft.com/en-us/library/system.text.encoding.aspx |
| Encoding.*  |                                                                                                                                          |                                                                   |
|             | <b>TimeSpans.</b> This .NET object is used for operations with time intervals, for the list of supported methods and properties refer to | http://msdn.microsoft.com/en-us/library/system.timespan.aspx      |
| TimeSpans.* |                                                                                                                                          |                                                                   |

![](_page_28_Picture_1.jpeg)

# <span id="page-28-0"></span>DECLARATION OF THE VARIABLES

**Set** operation is used for declaration and modification of the variables. The name of variable can be added as **var** attribute of **set** element. However, you can skip this attribute and define variable explicitly.

### <span id="page-28-1"></span>Simple variable

To declare a simple variable, all that is needed is the keyword "**var**" followed by the variable name and its value.

```
Example: 
<set var="accountid">{value}</set>
```

```
The other way to do same operation
Example: 
<set accountid="value" />
```

### <span id="page-28-2"></span>Array variable

To declare an array variable simply put the values in square brackets separated by comma.

```
Examples:
<set var="crmserver">{["crm1","crm2"]}</set>
<set cities="['New York','Los Angeles','Chicago']"/>
```

## <span id="page-28-3"></span>Dictionary variables

To declare the dictionary variable use construction "*{new Dictionary()}*" and then set its items list using the similar way as arrays.

```
Example:
<set Address="new Dictionary()" />
<set var="Address['Street']">street</set>
<set var="Address['City']">city</set>
<set var="Address['Country']">country</set>
```

### <span id="page-28-4"></span>List variables

List declaration is very similar to Dictionary, use construction "*{new List()}*" and then set its variables, but without keys.

```
Example:
\leqset Names="new List()" />
<set var="Names[]">Michael</set>
```

![](_page_29_Picture_0.jpeg)

![](_page_29_Picture_1.jpeg)

![](_page_29_Picture_2.jpeg)

<set var="Names[]">Joe</set>

## <span id="page-29-0"></span>Object variables

You can define any kind of object in Sync360 and there are four different methods. Let's look on examples:

#### *Method 1:*

```
<set var="Person">{new Object()}</set>
<set var="Person.Name">Michael</set>
<set var="Person.Lastname">White</set>
```

#### *Method 2:*

```
<set var="Person">
      <attr name="Name">Michael</attr>
      <attr name="Lastname">White</attr>
\langle/set\rangle
```

#### *Method 3:*

```
<set Person="new Object()" Person.Name="'Michael'"
Person.Lastname="'White'"/>
```

#### *Method 4:*

```
<set Person="['Name':'Michael','Lastname':'White']"/>
```

![](_page_30_Picture_1.jpeg)

![](_page_30_Picture_2.jpeg)

# <span id="page-30-0"></span>FLOW CONTROL OPERATIONS

### <span id="page-30-1"></span>Break

**Break** construction can be used to exit the cycle based on specific condition. This construction is applicable for any time of cycle.

*Example:*

```
<set var="testCycle">{['test1','test2','test3','test4','test5']}</set>
     <log>Start cycle.</log>
     <for var="t" in="testCycle">
         <log>{t}</log>
              <break if="t eq 'test3'"/>
    \langle/for\rangle <log>End cycle.</log>
<set var="testCycle">{['test1','test2','test3','test4','test5']}</set>
     <log>Start cycle.</log>
     <for var="t" in="testCycle">
        <log>{t}</log>
         <if condition="t eq 'test3'">
              <break/>
         \langle/if>
     </for>
     <log>End cycle.</log>
```

## <span id="page-30-2"></span>Continue

**Continue** construction can be used to skip cycle logic that is located under this construction and move on to the next cycle iteration.

*Example:*

```
<set var="testCycle">{['test1','test2','test3','test4']}</set>
     <log>Start cycle.</log>
     <for var="t" in="testCycle">
         <continue if="t eq 'test3'"/>
        <log>{t}</log>
    \langle for\rangle <log>End cycle.</log>
<set var="testCycle">{['test1','test2','test3','test4']}</set>
     <log>Start cycle.</log>
     <for var="t" in="testCycle">
         <if condition="t eq 'test3'">
```

![](_page_31_Picture_1.jpeg)

![](_page_31_Picture_2.jpeg)

```
 <continue/>
    \langleif>
    <log>{t}</log>
 </for>
 <log>End cycle.</log>
```

### <span id="page-31-0"></span>For

**For** construction is used when a code block needs to be executed a certain amount of times. There are 2 types of usage syntax listed below.

```
Examples:
<set crmservers="['crm','crm4','crm5']"/>
<for var="i" from="0" to="crmservers.Count - 1" step="1">
     <log>{crmservers[i]}</log>
\langle for\rangle<set crmservers="['crm','crm4','crm5']"/>
<for var="crmserver" in="crmservers">
     <log> {crmserver}</log>
\langle/for\rangle
```

## <span id="page-31-1"></span>If and Unless

**IF** construction can be used when it is necessary to execute a code block only if a specified condition in the **"condition"** attribute is equal to *true*.

```
Example:
\text{Set} var="a">{10}</set>
\text{Set} var="b">{15}\text{<<}set>
<if condition="a gt b">
     <log>a is greater then b</log>
\langle/if>
<if condition="a lt b">
     <log>a is less then b</log>
\langleif>
<if condition="a eq b">
     <log>a is equal to b</log>
\langleif>
```

**Unless** construction is used only in conjunction with **IF** to execute code block if a condition is true and another code **IF** the condition is not true. Any operator can use **IF** or **Unless**.

![](_page_32_Picture_1.jpeg)

![](_page_32_Picture_2.jpeg)

```
Example:
<set Colors="['red','gray','yellow']"/>
<log if="Colors[1] = 'gray'">Have a GRAY color</log>
<log unless="Colors[1] = 'blue'">Not a BLUE color</log>
<set var="MyColor1" if="Colors[2] = 'yellow'">Have a YELLOW color</set>
<log>{MyColor1}</log>
<set var="MyColor2" unless="Colors[1] = 'red'">Not a red color</set>
<log>{MyColor2}</log>
```

## <span id="page-32-0"></span>**While**

**While** construction can be used when a code block need to be executed while specified condition in the **"condition"** attribute is equal to *true*.

```
Example:
<set var="crmservers">{['crm','crm4','crm5']}</set>
<set var="counter">{0}</set>
<while condition="counter lt crmservers.Count">
     <log>{crmservers[counter]}</log>
     <set var="counter">{counter + 1}</set>
\langle/while>
```

## <span id="page-32-1"></span>THEN-ELSE

**THEN–ELSE** construction can be used if one code block should be executed if condition is true and another code should be executed if condition is false.

*Example:*

```
\text{Set var} = "a" > \{10\} </set>
    <set var="b">{15}</set>
    \text{Set} var="c">{10}</set>
     <if condition="a ne b">
           <then>
                <log>a not equal b</log>
          \langle /then>
          \varepsilon <then if="b ne c">
                <log>b not equal c</log>
          \langle/then>
           <else>
```

![](_page_33_Picture_0.jpeg)

![](_page_33_Picture_1.jpeg)

![](_page_33_Picture_2.jpeg)

 <log>a equal b</log> </else> <else if="b eq c"> <log>b equal c</log> </else>  $\langle$ if>

![](_page_34_Picture_1.jpeg)

# <span id="page-34-0"></span>DATA OPERATIONS

### <span id="page-34-1"></span>**Context**

**Context** construction is used when it is necessary to perform a set of operation in a specific user context. Context can be used only if current user has permissions for Impersonation usage on server.

| <b>Attribute</b> | <b>Description</b>                                                                     | <b>Usage</b> |
|------------------|----------------------------------------------------------------------------------------|--------------|
| for              | A server name that a context is created for.                                           | Required     |
| user             | A user Id (MS CRM) / a user E-Mail address (Exchange) that the<br>context is used for. | Required     |

| Table 5. Context Constriction Attributes | Description             |
|------------------------------------------|-------------------------|
| Attribute Name 1                         | Attribute Description 1 |
| Attribute Name 2                         | Attribute Description 2 |

```
Example:
<context for="exchange" user="{userEmail}">
     <select from="exchange" entity="contact" var="contacts">
         <where>
              <or>
                  <condition attr="crmLinkState" op="eq">0
                  </condition>
                  <condition attr="crmLinkState" op="eq">2
                  </condition>
             \langle/or\rangle <condition attr="crmid" op="ne"></condition>
         </where>
         <attr name="EntryId"/>
         <attr name="crmid"/>
         <attr name="crmLinkState"/>
         <attr name="iconindex"/>
         <attr name="crmOwnerId"/>
     </select>
</context>
```

## <span id="page-34-2"></span>Select

**Select** operation looks for records of specific type in accordance with the criteria and stores the retrieved data into a specified variable. The result is always an array of dictionaries. The following tables enumerate the attributes and child elements for Read operation.

![](_page_35_Picture_1.jpeg)

| <b>Attribute</b> | <b>Description</b>                                                     | <b>Usage</b> |
|------------------|------------------------------------------------------------------------|--------------|
| from             | A server name that Read operation will run for.                        | Required     |
| entity           | A type of records that the search will perform for.                    | Optional     |
| var              | The variable name that will contain the result of search<br>operation. | Optional     |
| count            | A number of records that will be returned by search                    | Optional     |
| page             | Determine from which page return records                               | Optional     |

#### Table 6. Read operation attributes.

**NOTE:** Here in after, if "entity" attribute has not been specified, the "contact" entity will be used by default.

| Element | Description                                                                                | Usage    |
|---------|--------------------------------------------------------------------------------------------|----------|
| attr    | An attribute name the found records always contain.                                        | Required |
| where   | A criteria element contains conditions set that will be used to find<br>records to update. | Optional |
| order   | Used for sorting search results by ordering.                                               | Optional |
| query   | A way to specify native query for SQL datasource                                           | Optional |

#### Table 7. Read operation children elements.

*Example:*

To read all names and ids of first 50 accounts where Country attribute value is equal to "US" and sort them by name attribute in descending order, you could use Select operation as follows:

```
<select from="crmserver" entity="account" var="accounts" count="50">
     <where>
         <condition attr="address1_country" op="eq">US</attr>
     </where>
    \langleattr name="name"/>
     <attr name="accountid"/>
     <order by="name" desc="true"/>
</select>
```

**Select** operation supports native SQL queries using query element. You can also pass parameters to the query using **attr** element. The entity element is required to be specified for the code to be valid, but with this method it's not used.

*Example:* <select from="db" entity="tasks" var="tasks"> <query>

![](_page_36_Picture_0.jpeg)

```
 select Tasks.Name as 'ttt', Instances.Name, Instances.InstanceID from 
tasks, Instances where tasks.InstanceID = Instances.InstanceID and Instances.Title 
= @title
     </query>
     <attr name="@title">Main work</attr>
</select>
```

**Select–join** operation looks for records of specific type in accordance with the criteria applied to the entity itself and related entities.

| <b>Attribute</b> | <b>Description</b>                                                                                                                                                                             | <b>Usage</b> |
|------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| type             | Join type (based on used connector)         For example, for CRM connector:         https://docs.microsoft.com/en-us/previous-versions/dynamicscrm-2016/developers-guide/gg327702%28v=crm.8%29 | Required     |
| entity           | Related entity name.                                                                                                                                                                           | Required     |
| to               | Field name of the related entity.                                                                                                                                                              | Required     |
| from             | Field name of the main entity.                                                                                                                                                                 | Required     |
| as               | Attribute name for storing the result. Simplifies data         access.                                                                                                                         | Optional     |

#### Table 8. Join operation attributes.

*Example:*

```
<select from="crm" entity="contact" var="crmContacts">
          <where>
               <condition attr="fullname" op="ex"></condition>
          </where>
          <join type='inner' entity='account' to='parentcustomerid' from='accountid'>
               <where>
                    <condition attr="name" op="ex"></condition>
               </where>
               <attr name="name" as="accountname" />
               <join entity='systemuser' to='ownerid' from='systemuserid' as='owner'>
                     <attr name="fullname" as="userfirstname"/>
               </join>
          </join>
          <attr name="firstname" />
          <attr name="lastname" as="contactlastname"/>
```

![](_page_37_Picture_1.jpeg)

#### </select>

### <span id="page-37-0"></span>**Create**

**Create** operation creates a specified entity record with defined attributes and stores a copy of created data into the specified variable. This operation returns an array of dictionaries.

The following tables list the attributes and children elements for Create operation.

| Attribute | Description                                                       | Usage    |
|-----------|-------------------------------------------------------------------|----------|
| ın        | A server name that the create operation performs for.             | Required |
| entity    | A type of records that will be created.                           | Optional |
| var       | The variable name that will contain a result of Create operation. | Required |

#### Table 9. Create operation attributes.

#### Table 10. Create operation children elements.

| Element | <b>Description</b>                                        | <b>Usage</b> |
|---------|-----------------------------------------------------------|--------------|
| attr    | An attribute name and its value for a new created record. | Required     |

To create the contact, you could use the Create operation as follows:

```
<create in="crmserver" entity="contact" var="createdContact">
    <attr name="firstname">Joe</attr>
    <attr name="lastname">Doe</attr>
    <attr name="emailaddress1">joe.doe@mail.com</attr>
    <attr name="jobtitle">manager</attr>
    <attr name="telephone1">444-44-44</attr>
     <attr name="parentcustomerid">account:{accountid}</attr>
</create>
```

### <span id="page-37-1"></span>Update

**Update** operation updates the specified entity records corresponding with the search criteria with predefined attributes set. The operation returns an array of dictionaries.

| Attribute | Description                                           | Usage    |
|-----------|-------------------------------------------------------|----------|
|           | A server name that the update operation performs for. | Required |

|  | Entity | A type of records that will be updated. | Optional |
|--|--------|-----------------------------------------|----------|
|--|--------|-----------------------------------------|----------|

#### Table 12. Update operation children elements.

| Element | Description                                                                                | Usage    |
|---------|--------------------------------------------------------------------------------------------|----------|
| where   | A criteria element contains conditions set that will be used to find<br>records to update. | Required |
| attr    | An attribute name and its value for updated records.                                       | Required |

*Example:* 

To update account with name "Adidas" and set new Primary Contact attribute, you could use the Update operation as follows:

```
<update in="crmserver" entity="account">
     <where>
         <condition attr="name" op="eq">Adidas</condition>
     </where>
     <attr name="primarycontactid">contact:{primContactId}</attr>
</update>
```

### <span id="page-38-0"></span>Delete

**Delete** operation deletes a specified entity records satisfied to search criteria.

| <b>Attribute</b> | <b>Description</b>                                    | <b>Usage</b> |
|------------------|-------------------------------------------------------|--------------|
| $\ln$            | A server name that the delete operation performs for. | Required     |
| entity           | A type of records that will be deleted.               | Optional     |

#### Table 13. Delete operation attributes.

Table 14. The Delete operation child elements.

| Element | Description                                                                                | Usage    |
|---------|--------------------------------------------------------------------------------------------|----------|
| where   | A criteria element contains conditions set that will be used to find<br>records to delete. | Required |

*Example:*

The following script deletes an Exchange contact by known EntryId:

```
<delete in="exchange">
     <where>
         <condition attr="EntryId" op="eq">{EntryId}</condition>
     </where>
```

![](_page_38_Picture_18.jpeg)

![](_page_39_Picture_1.jpeg)

#### </delete>

### <span id="page-39-0"></span>Batch

**Batch** operation can be used to combine create, update, or delete operations into one batch to minimize the number of requests to target system.

| <b>Attribute</b> | <b>Description</b>                                                                                                                                                                                                               | <b>Usage</b> |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| for              | A server name that Batch operation will run for.                                                                                                                                                                                 | Required     |
| continueOnError  | true - continue processing the next request in the<br>collection even if a fault has been returned from processing<br>the current request in the collection.<br>false - do not continue processing the next request on<br>error. | Required     |
| var              | The variable name that will contain the response from<br>target system. Response is presented only for create<br>operation.                                                                                                      | Required     |
| returnResponses  | true - return responses from each message request<br>processed.<br>false - do not return responses.                                                                                                                              | Optional     |

#### *Example: (file for importing attached [\(APPENDIX 1\)](#page-55-0) separetly)*

```
<set var="PageSize">{10}</set>
```

```
<log>Script started at {Utils.Now}</log>
     <set var="csvContacts">{Csv.Read('c:/temp/contact lite test.txt', 
Encoding.GetEncoding('utf-8'), ';')}</set>
```

```
<set var="Continue">{true}</set>
```

```
<set var="RecordsLeft">{csvContacts.Count}</set>
<set var="Page">{0}</set>
<log>Import started at {Utils.Now}</log>
<log>Batch size = {PageSize}</log>
<while condition="Continue">
     <set var="LastPage">{RecordsLeft lt PageSize}</set>
     <set var="CurrentPage" if="LastPage">{RecordsLeft}</set>
     <set var="CurrentPage" if="!LastPage">{PageSize}</set>
```

![](_page_40_Picture_1.jpeg)

```
 <batch for="crm" continueOnError="true" var="result"
returnResponses="true">
                <for var="iter" from="0" to="CurrentPage-1" step="1">
                      <set var="index">{(PageSize * Page) + iter}</set>
                 <set var="cnt">{csvContacts[index]}</set>
                 <sandbox>
                            <create in="crm" entity="contact">
                                 <attr if="cnt['lastname'].isSet and 
cnt['lastname'] ne ''" name="lastname">{cnt['lastname']}</attr>
                                 <attr if="cnt['firstname'].isSet and 
cnt['firstname'] ne ''" name="firstname">{cnt['firstname']}</attr>
                                 <attr if="cnt['salutation'].isSet and 
cnt['salutation'] ne ''" name="salutation">{cnt['salutation']}</attr>
                                 <attr if="cnt['birthdate'].isSet and 
cnt['birthdate'] ne ''" name="birthdate">{cnt['birthdate']}</attr>
                                 <attr if="cnt['slaname'].isSet and 
cnt['slaname'] ne ''" name="slaname">{cnt['slaname']}</attr>
                                 <attr if="cnt['telephone1'].isSet and 
cnt['telephone1'] ne ''" name="telephone1">{cnt['telephone1']}</attr>
                                 <attr if="cnt['telephone2'].isSet and 
cnt['telephone2'] ne ''" name="telephone2">{cnt['telephone2']}</attr>
                                 <attr if="cnt['telephone3'].isSet and 
cnt['telephone3'] ne ''" name="telephone3">{cnt['telephone3']}</attr>
                                 <attr if="cnt['websiteurl'].isSet and 
cnt['websiteurl'] ne ''" name="websiteurl">{cnt['websiteurl']}</attr>
                                 <attr name="statecode">{1}</attr>
                                 <attr name="statuscode">{2}</attr>
                            </create>
                      </sandbox>
                </for>
           </batch>
           <log>{result}</log>
        <set var="RecordsLeft">{RecordsLeft - CurrentPage}</set>
        \text{Set var} = "Page" > \text{Page} + 1\}<set var="Continue" if="RecordsLeft le 0">{false}</set>
       </while>
     <log>Import finished at {Utils.Now}</log>
```

#### *Example: (Batch error handling)*

```
<if condition="result.IsFaulted">
           <log>An error occured during batch request.</log>
     <for var="resp" in="result.Responses">
```

![](_page_41_Picture_1.jpeg)

```
 <if condition="resp.Fault.isSet">
                        <log>Error on index {resp.RequestIndex} with message: 
{resp.Fault.Message}</log>
                  \langleif>
                   </for>
      \langleif>
```

```
Example: (Batch response handling for create operation)
<set var="newIds">{new List()}</set>
     <for var="response" in="result.Responses">
         <if condition="!response.Fault.isSet">
              <then>
                  <set var="innerResponse">{response.Response}</set>
                  <set if="innerResponse.ResponseName eq 'Create'"
var="newIds[]">{innerResponse.id}</set>
                  <set if="innerResponse.ResponseName eq 'ExecuteTransaction'"
var="newIds[]">{innerResponse.Responses[0].id}</set>
             \langle /then>
              <else>
                  <set var="newIds[]">{null}</set>
              </else>
        \langle/if>
</for>
```

### <span id="page-41-0"></span>**Transaction**

**Transaction** operation can be used to combine create, update or delete operations into one transaction to minimize the number of requests to target system.

| <b>Attribute</b> | <b>Description</b>                               | <b>Usage</b> | <b>Example: (file for</b> |
|------------------|--------------------------------------------------|--------------|---------------------------|
| for              | A server name that Batch operation will run for. | Required     | importing<br>attached     |
|                  | The variable name that will contain the response |              |                           |
| var              | from target system. Response is presented only   | Required     |                           |
|                  | for create operation.                            |              |                           |
|                  | true - return responses from each message        |              |                           |
| returnResponses  | request processed.                               | Optional     |                           |
|                  | false - do not return responses.                 |              |                           |

#### *separetly)*

<set var="PageSize">{10}</set>

![](_page_42_Picture_1.jpeg)

```
<log>Script started at {Utils.Now}</log>
     <set var="csvContacts">{Csv.Read('c:/temp/contact lite test.txt', 
Encoding.GetEncoding('utf-8'), ';')}\langle/set>
     <set var="Continue">{true}</set>
     <set var="RecordsLeft">{csvContacts.er}</set>
     <set var="Page">{0}</set>
     <log>Import started at {Utils.Now}</log>
     <log>Batch size = {PageSize}</log>
     <while condition="Continue">
           <set var="LastPage">{RecordsLeft lt PageSize}</set>
           <set var="CurrentPage" if="LastPage">{RecordsLeft}</set>
           <set var="CurrentPage" if="!LastPage">{PageSize}</set>
         <transaction for="crm" continueOnError="true" var="result"
returnResponses="true">
                <for var="iter" from="0" to="CurrentPage-1" step="1">
                      <set var="index">{(PageSize * Page) + iter}</set>
                 <set var="cnt">{csvContacts[index]}</set>
                 <sandbox>
                           <create in="crm" entity="contact">
                                 <attr if="cnt['lastname'].isSet and 
cnt['lastname'] ne ''" name="lastname">{cnt['lastname']}</attr>
                                 <attr if="cnt['firstname'].isSet and 
cnt['firstname'] ne ''" name="firstname">{cnt['firstname']}</attr>
                                 <attr if="cnt['salutation'].isSet and 
cnt['salutation'] ne ''" name="salutation">{cnt['salutation']}</attr>
                                 <attr if="cnt['birthdate'].isSet and 
cnt['birthdate'] ne ''" name="birthdate">{cnt['birthdate']}</attr>
                                 <attr if="cnt['slaname'].isSet and 
cnt['slaname'] ne ''" name="slaname">{cnt['slaname']}</attr>
                                 <attr if="cnt['telephone1'].isSet and 
cnt['telephone1'] ne ''" name="telephone1">{cnt['telephone1']}</attr>
                                 <attr if="cnt['telephone2'].isSet and 
cnt['telephone2'] ne ''" name="telephone2">{cnt['telephone2']}</attr>
                                 <attr if="cnt['telephone3'].isSet and 
cnt['telephone3'] ne ''" name="telephone3">{cnt['telephone3']}</attr>
                                 <attr if="cnt['websiteurl'].isSet and 
cnt['websiteurl'] ne ''" name="websiteurl">{cnt['websiteurl']}</attr>
                                 <attr name="statecode">{1}</attr>
                                 <attr name="statuscode">{2}</attr>
```

![](_page_43_Picture_1.jpeg)

```
</create>
                </sandbox>
           </for>
     </ transaction>
     <log>{result}</log>
   <set var="RecordsLeft">{RecordsLeft - CurrentPage}</set>
  <set var="Page">{Page + 1}</set>
   <set var="Continue" if="RecordsLeft le 0">{false}</set>
 </while>
<log>Import finished at {Utils.Now}</log>
```

![](_page_44_Picture_1.jpeg)

# <span id="page-44-0"></span>SEARCH CRITERIA

### <span id="page-44-1"></span>Two Operands Condition Operators

There are 6 types of two operands condition operators. The following table describes them in detail.

| Operator | Description                                                                                          |
|----------|------------------------------------------------------------------------------------------------------|
| eq       | Evaluates to true if an attribute has a value that is equal to the condition value.                  |
| ne       | Evaluates to true if an attribute has a value that is not equal to the condition value.              |
| $1t$     | Evaluates to true if the attribute has a value that is less than the condition value.                |
| le       | Evaluates to true if the attribute has a value that is less than or equal to the condition value.    |
| gt       | Evaluates to true if the attribute has a value that is greater than the condition value.             |
| ge       | Evaluates to true if the attribute has a value that is greater than or equal to the condition value. |

Table 17. Two operands operation types.

*Example:*

The following script returns all contacts with first name "Joe":

```
<select from="crmserver" entity="contact" var="contacts_with_name_Joe">
     <where>
         <condition attr="firstname" op="eq">Joe</condition>
     </where>
     <attr name="contactid" />
</select>
```

## <span id="page-44-2"></span>Contains Condition Operator

**Contains** condition operator allow to perform text searches within string properties.Condition evaluates to true if the supplied constant value contains in the property text value.

| Pattern | Description                                                                           |  |
|---------|---------------------------------------------------------------------------------------|--|
| %text   | Evaluates to true if the property text value ends with the supplied constant value.   |  |
| %text%  | Evaluates to true if the supplied constant value contains in the property text value. |  |
| text%   | Evaluates to true if the property text value starts with the supplied constant value. |  |

Table 18. The Contains condition operator search patterns for CRM servers.

**NOTE:** %text% is equals to search without "%" symbol.

*Example:* 

![](_page_45_Picture_1.jpeg)

The following script returns all contacts, which first name contains "J" and mobile phone number contains "5544".

```
<select from="crmserver" entity="contact" var="contacts">
     <where>
         <condition attr="firstname" op="co">J</condition>
         <condition attr="mobilephone" op="co">5544</condition>
     </where>
     <attr name="contactid"/>
</select>
```

Contains condition *for Exchange server* doesn't support the **"%"** symbol - condition evaluates to true if the supplied constant value is contained in the property text value. Search is *case insensitive*. *Example:*

The following script returns all contacts, which first name starts with "J" and mobile phone number containing "5544".

```
<select from="exchange" entity="contact" var="contacts">
     <where>
         <condition attr="firstname" op="co">Jo</condition>
         <condition attr="mobilephone" op="co">5544</condition>
     </where>
     <attr name="EntryId"/>
</select>
```

### <span id="page-45-0"></span>Not Operator

**Not operator** inverts a result of a logical operation.

*Example:*

The following script returns identifiers of all CRM contacts that first name is not equal to "Joe" and DoNotPhone or DoNotEmail properties are not equal to true.

```
<select from="crmserver" entity="contact" var="contacts">
     <where>
         <not>
           <and>
                 <condition attr="firstname" op="eq">Joe</condition>
                 \langleor><condition attr="donotphone" op="eq">{true}
                       </condition>
                       <condition attr="donotemail" op="eq">{true}
                       </condition>
                 \langle/or\rangle</and>
```

![](_page_46_Picture_1.jpeg)

![](_page_46_Picture_2.jpeg)

```
\langle/not\rangle</where>
      <attr name="contactid"/>
</select>
```

### <span id="page-46-0"></span>Starts with

**Starts with** condition operator allow to perform text searches within string properties.Condition evaluates to true if the property text value starts with supplied constant value.

*Example:* 

The following script returns all contacts, which first name starts with "J" and mobile phone number starts with "5544".

```
<select from="crmserver" entity="contact" var="contacts">
         <where>
             <condition attr="firstname" op="sw">J</condition>
             <condition attr="mobilephone" op="sw">5544</condition>
         </where>
         <attr name="contactid"/>
</select>
```

## <span id="page-46-1"></span>Ends with

**Ends with** condition operator allow to perform text searches within string properties.Condition evaluates to true if the property text value ends with supplied constant value.

*Example:* 

The following script returns all contacts, which first name ends with "J" and mobile phone number ends with "5544".

```
<select from="crmserver" entity="contact" var="contacts">
     <where>
         <condition attr="firstname" op="ew">J</condition>
         <condition attr="mobilephone" op="ew">5544</condition>
     </where>
     <attr name="contactid"/>
</select>
```

# <span id="page-46-2"></span>And / Or Operators

These operators should contain two or more conditions. **And** operator evaluates to true only if all its children conditions evaluate to true. **Or** operator evaluates to true if at least one of its children conditions evaluate to true.

![](_page_47_Picture_1.jpeg)

ProServ

#### *Example:*

The following script returns all identifiers of CRM contacts which first name is equal to Joe or DoNotPhone or DoNotEmail properties are equal to true.

```
<select from="crmserver" entity="contact" var="contacts">
      <where>
            <and>
                  <condition attr="firstname" op="eq">Joe</condition>
                  <or>
                        <condition attr="donotphone" op="eq">{true}
                        </condition>
                        <condition attr="donotemail" op="eq">{true}
                        </condition>
                  \langle/or\rangle\langle and\rangle</where>
      <attr name="contactid"/>
</select>
```

### <span id="page-47-0"></span>Exists Condition Operator

**Exists** condition returns true if a specified attribute exists in an entity record. It does not matter whether the property contains a non-empty value or not.

*Example:*

Let us imagine that there is a custom property called ShoeSize that was added to some contacts but others do not have this field.

The following script returns all contacts with this field:

```
<select from="exchange" entity="contact" var="contacts with ShoeSize">
     <where>
           <condition attr="ShoeSize" op="ex" />
     </where>
     <attr name="EntryId"/>
</select>
```

The following script returns all contacts without this field:

```
<select from="exchange" entity="contact" var="contacts_without_ShoeSize">
     <where>
           <not><condition attr="ShoeSize" op="ex"/></not>
     </where>
     <attr name=" EntryId "/>
</select>
```

![](_page_48_Picture_1.jpeg)

### <span id="page-48-0"></span>In Condition Operator

**In** condition operator returns true if the specified attribute matches to a value in a condition values list. The following example return all identifiers of CRM contacts, which first name is Bob, Joe, or Michael.

```
Example:
<select from="crmserver" entity="contact" var="contacts">
     <where>
           <condition attr="firstname" op="in">{['Bob', 'Joe', 
           'Michael']}</condition>
     </where>
     <attr name="contactid" />
</select>
```

## <span id="page-48-1"></span>Between Condition Operator

*Between* condition operator returns true if the specified attribute value is between two values in a conditions values list. There are three ways to specify the condition's value. *Examples:*

All scripts return all identifiers of CRM contacts, which were created in 2011.

```
<set var="dates">{[Utils.Now.AddDays(-100), Utils.Now.AddDays(-
50)]}</set>
     <select from="crm" entity="contact" var="contacts">
           <where>
                <condition attr="createdon"
op="between">{[dates[0],dates[1]]}</condition>
           </where>
           <attr name="contactid" />
           <attr name="createdon" />
     </select>
     <set var="dates">{["01/01/2018", "01/01/2019"]}</set>
     <select from="crm" entity="contact" var="contacts">
           <where>
                <condition attr="createdon"
op="between">{[dates[0],dates[1]]}</condition>
           </where>
           <attr name="contactid" />
           <attr name="createdon" />
     </select>
```

![](_page_49_Picture_1.jpeg)

# <span id="page-49-0"></span>EXCEPTION HANDLING OPERATIONS

### <span id="page-49-1"></span>Exception and OnError

**Exception** operation is used when it is necessary to throw an exception in the process of script execution.

```
Example:
<set var="crmservers">crm,crm4,crm5</set>
<for var="crmserver" in="crmservers">
     <select from="{crmserver}" entity="account" var="accounts">
         <where>
              <condition attr="statecode" op="eq">{0}</condition>
         </where>
         <attr name="accountid" />
     </select>
     <if condition="{accounts.Count = 0}">
        \leqexception>There are no active accounts in {crmserver}. \leq/exception>
    \langleif>
\langle for\rangle
```

**OnError** operation is used in conjunction with **Exception** for handling specific exceptions.

#### *Example:*

```
<sandbox verbose="false">
    <log>{2/0}</log>
     <onerror of="typeof System.DivideByZeroException">
     <log>This is DivideByZero!</log>
     </onerror>
</sandbox>
```

### <span id="page-49-2"></span>Sandbox

**Sandbox** operation is used when it is necessary to hide exceptions in process of script execution from user. Sandbox operation has the **"verbose"** attribute that is used for enabling or disabling errors logging (if it is left empty, then (*true*) will be used by default).

#### *Example:*

The following script looks for CRM contact "Joe Dow" and if a single instance of this contact found, then this contact is set as a primary contact of "Adidas" company. Logging is enabled for the sandbox.

![](_page_50_Picture_1.jpeg)

```
<sandbox verbose="true">
     <select in="crmserver" entity="contact" var="contacts">
           <where>
                <condition attr="firstname" op="eq">Joe</condition>
                <condition attr="lastname" op="eq">Dow</condition>
           </where>
           <attr name="contactid" />
     </select>
     <if condition="contacts.Count = 1">
           <update in="crmserver" entity="account">
                <where>
                      <condition attr="name" op="eq">Adidas</condition>
                </where>
                <attr name="primarycontactid">
                      contact:{contacts[0].contactid}
                </attr>
           </update>
     \langleif>
</sandbox>
```

![](_page_51_Picture_1.jpeg)

# <span id="page-51-0"></span>STRUCTURAL OPERATIONS

### <span id="page-51-1"></span>Include

**Include** operation is used if you want to separate some code into other script files. Sync360 will combine the main script and code from all includes on the moment of execution. This operation has the **"name"** attribute which value should be a path to existing script in regard to scripts folder plus filename without ".xml" extension.

**NOTE: Sync360 IDE** has special order where to find script. First it tries to find specified path in a folder where a current script is situated. If the script is not found it will check Script folder in current Application folder. If after that the script is also not found it will check the standard Script folder in main Sync360 folder.

```
Example:
<include name="Includes\Parameters"/>
<include name="Includes\ReadUserDetails"/>
<include name="Includes\ReadSettings"/>
<context for="exchange" user="{User.Email}">
     <select from="exchange" var="contacts">
           <where>
                 \langleor><condition attr="crmLinkState" op="eq">0
                       </condition>
                       <condition attr="crmLinkState" op="eq">2
                       </condition>
                 \langle/or\rangle<condition attr="crmid" op="ne"></condition>
           </where>
        <attr name="fileas"/>
        <attr name="EntryId"/>
        <attr name="crmid"/>
        <attr name="crmLinkState"/>
        <attr name="iconindex"/>
        <attr name="crmOwnerId"/>
     </select>
</context>
```

<log>Contacts to be considered: {contacts.Count}</log>

![](_page_52_Picture_1.jpeg)

### <span id="page-52-0"></span>Call script

**Call** operation is used when it is necessary to execute another script (that already exists) before current script. Rather than include operation, the script will be executed in own context, and it will not have access to variables in main script. The Call operator supports passing parameters into the script. Operator searches for script in **@private** folder. There are two ways to specify call operator. *Examples:*

```
Call script "MyScriptHelper" and pass parameter "CallSettings" 
<script>
     <var callParameters='new Object()'/>
     <var callParameters.Param1='value1'/>
     <MyScriptHelper CallSettings="callParameters"/>
</script>
<script>
     <var callParameters='new Object()'/>
     <var callParameters.Param1='value1'/>
     <call name="MyScriptHelper" CallSettings="callParameters"/>
</script>
```

## <span id="page-52-1"></span>Script

**Script** element is used for code blocks grouping. *Example:* <script> //There should be the script body </script>

## <span id="page-52-2"></span>Log

**Log** operation can be used when you need to maintain history of script executions and provide some more information to the output. It also eases process of script debugging.

```
Examples:
<script>
     <log>Result: {2+2}</log>
</script>
<script>
     <select from="crm" entity="systemuser" var="users">
```

![](_page_53_Picture_1.jpeg)

![](_page_53_Picture_2.jpeg)

```
<attr name="systemuserid"/>
           <attr name="fullname"/>
      </select>
     <for var="user" in="users">
           <log>{user.systemuserid} {user.fullname}</log>
      \langle / for \rangle</script>
```

![](_page_54_Picture_1.jpeg)

# <span id="page-54-0"></span>EXTEND FUNCTIONALITY

You can extend script engine functionality from the script, by binding to standard .NET classes or by adding custom assemblies. The assembly should be placed in Global Assembly Cache or in the Sync360 main directory.

#### *Example:*

```
<script name="StdLib">
<!-- The following line will bind assembly to ScheduleItem operator-->
     <set ScheduleItem="typeof 
'Use4si.Infrastructure.Processing.ScheduleItem, Use4si.Infrastructure'"/>
<!— Bind to standard .NET class-->
     <set TimeSpan="typeof System.TimeSpan"/>
<!— Bind static methods of standard .NET class -->
     <set TimeSpans="static TimeSpan"/>
</script>
```

![](_page_55_Picture_1.jpeg)

![](_page_55_Picture_2.jpeg)

# <span id="page-55-0"></span>APPENDIX 1

### <span id="page-55-1"></span>Contact Import List

**NOTE:** Save as .txt file format.

lastname;firstname;salutation;birthdate;slaname;telephone1;telephone2;telephone3;websiteurl Milanovic;Nikola;;;;+389 011 344 97 51;;; Sapin;Jean-Luc;;;;546456;;; LeVert;Albert;;;;;;; Delarue;Jean Luc;;;;;;; Dupond;Jean;;;;;;; Batista;Rafael;;;;;;; Kilic;Emrah;;;;+90 262 648 53 00;;; Zalcberg;Ilana;;;;+55 21 3545 7124;;; Sadaka;Mohamed;;;;+971568296165;;; LOCHU; Philippe;;;; + 33 463 05 02 97;;; Dias Ribeiro;Daniel;;;;55 31 3248 6784;;; Chronas;Giorgos;;;;+302106894509;;; Martello;Marina;;;;+39 0512143791;;; Fernandes;Althea;;;;+971044224900 ext: 906;;; Olsen;Drew;;;;;;; Elvin;Paul;;;;01625 238662;;; Weckerlein;Barbara;;;;;;; Stan;Alexandra Georgeta;;;;;;; Streubel;Berthold;;;;+43 (0)1 40400 - 36500;;; Pivkova-Veljanovska;Aleksandra;;;;+ 389 2 3147 783;;; Riera;Pau;;;;;;; Morcillo;Luis;;;;+34 651 52 31 08;;; Dupont;Sonia;;;;+33 1 49 42 48 32;;; Varghese;Rency;;;;;;; Pollard;Laura;;;;(864) 388-1070;;; Dedeurwaerdere;Fransceska;;;;051 23 71 96;;; Kozel;Beth;;;;(314) 454-6093;;; Etzhold;Anna;;;;+49 69 4 08 96 79 0;;; Kooij;Dapp;;;;+32.14.64.1646;;; Bhanushali;Pragnesh;;;;905-287-2870 x227;;; Zavaglia;Katia;;;;;;; BERGOUGNOUX;Anne;;;;00. 334.11.75.98.79;;; Carcouet;Franзois;;;;02 40 08 40 28;;; Faurй;Julien;;;;04 76 76 63 55;;; Vrtel;Petr;;;;;;;

![](_page_56_Picture_1.jpeg)

![](_page_56_Picture_2.jpeg)

;Fredy Andres Tellez;;;;0573194597772 or 05717425961 ext 116;;; Mansour-Hendili;Lamisse;;;;;;; Murphy;Jean;;;;+3531221 4510 / 4590;;; Gymez;Jos Javier;;;;;;; Tzaninis;Dimitris;;;;+302106157006;;; Lima;Andrea;;;;55 31 2511 2200;;; Borras;Silvia;;;;+44 1224550682;;; Peters;Andrea;;;;+49 711 278 34901;;; Williams;Jonathan;;;;+44 01865 225594;;; Bento;Celeste;;;;;;; Garuti;Anna;;;;010 3537 798;;; Castro;Gabriela;;;;55 11 3822 2148 / 3666 2279 / 5078 8527;;; Glotov;Andrey Sergeevich;;;;;;; Diaz;Diego;;;;+5715700929;;; Lindeman;Neal;;;;8573071540;;; Badenas;Celia;;;;+34 93 227 54 00;;; Lucarelli;Debora;;;;;;; Kilic;Seda;;;;;;; ;Irene Pajuvuo;;;;;;; Sorel;Nathalie;;;;;;; Seia;Manuela;;;;+39 02 550 324 32;;; Arnaud Gouttenoire;Estelle;;;;+41216132023;;; Jin;Huilin;;;;;;; Kern;Wolfgang;;;;+49 89 990 17 200;;; Osuna;Carlos;;;;+34 976 76 56 85;;; Cihanoglu;Cihan;;;;+90 05320514833;;; Petrovski;Jordan;;;;+38978434837;;; Morreau;Hans;;;;;;; Crenshaw;Andrew;;;;;;; Noirot;Сйline;;;;+33 3 20 44 59 62;;; Tьmer;Zeynep;;;;+45 43 260 155;;; Castillo;Mary;;;;51 1 513 6666;;; Gйrard;Bйnйdicte;;;;33 (0)3 69 55 11 65;;; Powierska-Czarny;Jolanta;;;;;;; Cidade Batista;Marcelo;;;;+55 11 3069-6330;;; Nagy Hafeez; Elia;;;; +201229133044;;; Kochav;Galya;;;;972-3-9195757;;;