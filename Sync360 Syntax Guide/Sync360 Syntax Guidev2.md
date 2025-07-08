# GENERAL SCRIPT SYNTAX

A script for Sync360 represents an xml file which can be a valid xml file (in case of usage of root element Script as it is described in the clause or a bunch of xml elements.

The script consists of nested elements. Element is a logical document component either begins with a start-tag (for example, <context>) and ends with a matching end-tag (for example, </context>) or consists only of an empty element-tag (for example, <criteria />). The characters between the start- and end-tags, if any, are the element's content, and may contain markup, including other elements, which are called child elements.

*Example:*

```
<set var="CitiesAndCountries">
    <attr name="Cities">Houston,Phoenix</attr>
     <attr name="Countries">USA,UK,Canada</attr>
</set>
```

Names of elements, as well as names of attributes, cannot contain space characters. The name should begin with a letter or an underline character. The rest of the name may contain as the same characters as well as digit characters.

Attribute is a markup construct consisting of a name/value pair that exists within a start-tag or empty element-tag followed by an element name. Values of attributes should be always embedded in single or double quotes.

**NOTE:** It is necessary to use identical types of quotes for values of attributes in the same tag (please see Cities and Countries in the example above).

Values of attributes can be a template string with expression in curly brackets (or without them). In this case a final value will be obtained by calculation of these expressions and the initial expressions will be substituted with this value. Content of elements rather will be always result of calculation and should be specified without curly brackets. If the template string contains only one expression without other characters, the value will be the result of calculation of this expression.

```
<set var="firstname">Joe</set> <!-- a value without expressions. -->
<if condition="contacts.Count eq 0"> <!-- an expression in the attribute value. -->
<set var="calculatedvalue">{294+322}</set> <!-- an expression in the element content. -->
```

**NOTE:** An expression may include constants and variables. A variable name is case-sensitive (please see *calculatedvalue* in the example above).

The expression type is selected automatically based on the expression value evaluation, but it can be converted to a necessary type by using special construction 'as' (available types are enumerated in the clause **Ошибка! Источник ссылки не найден.**). The following examples give the same result:

```
<set var="counter">{2}</set>
<set var="counter">{2 as 'int'}</set>
```

Moreover, variable values may be a simple value, an array and a dictionary (declaration and modification of variable values are described in the clause 7).

A value of an array item can be received by specifying the array name and index of the necessary element inside square brackets. Indexes start from zero.

*Example:*

<log>First account: {account[0]}</log>

The random element of an array may be selected if the name of an array is specified with empty square brackets.

*Example:*

```
<set var="crmservers">{[crm,crm4,crm5]}</set>
<log>Random CRM server: {crmservers[]}</log>
```

A value of a dictionary item can be received by specifying the dictionary name and name of the necessary element in quotes and inside square brackets or by specifying name of the necessary element followed by the dictionary name with a dot.

*Examples:*

```
<log>City: {account.city}</log>
<log>City: {account["city"]}</log>
```

If the corresponding element is not found, search of a method with this name will be produced via Reflection and, if the method is found, then it will be applied to the dictionary. Otherwise, an exception will be thrown.

*Example:*

<log>Contact is found: {contacts.ContainsKey(contactid)}</log>

# VARIABLE TYPES

Variables in Sync360 can be defined with one of types listed in **Table 1**.

| <b>Type</b> | <b>Description</b>                                                              |
|-------------|---------------------------------------------------------------------------------|
| char        | The char keyword is used to declare a Unicode character (Unicode 16-bit character). |
| char[]      | Array of char variables.                                                        |
| string      | The string type represents a sequence of zero or more Unicode characters.       |
| string[]    | Array of string variables.                                                      |
| byte        | The byte keyword is used to declare variables in range from $0$ to 255 (Unsigned 8-bit integer).          |
| byte[]      | Array of byte variables.                                                        |
| int         | The int keyword is used to declare variables in range from -2,147,483,648 to 2,147,483,647 (Signed 32-bit integer).   |
| int[]       | Array of int type variables.                                                    |
| long        | The long keyword is used to declare variables in range from - 9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 (Signed 64-bit integer).   |
| long[]      | Array of long type variables.                                                   |
| double      | The double keyword signifies a simple type that stores 64-bit floating-point  values.    |
| double[]    | Array of double type variables.                                                 |
| bool        | The bool keyword is used to declare variables to store the Boolean values, true and false. |
| bool[]      | Array of bool variables.                                                        |
| date        | The date type presents an instant in time, typically expressed as a date and   time of day.  |
| date[]      | Array of date type variables.                                                   |
| List        | Represents a strongly typed list of objects that can be accessed by index. Standard .NET List   |
| Dictionary  | Represents a collection of keys and values. Standard .NET Dictionary            |
| Object      | Generic Object, its properties can be defined in script.                        |

Table 1. Variable types

# **CONSTANTS**

There are three predefined constants in Sync360 that listed in **Table 2**.

| <b>Type</b> | <b>Description</b>                                            |
|-------------|---------------------------------------------------------------|
| "true"      | <b>True.</b> This constant represents a Boolean value True.   |
| "false"     | <b>False.</b> This constant represents a Boolean value False. |
| "null"      | <b>Null.</b> This constant represents Null value              |

#### Table 2. Constants

# **OPERATORS**

| <b>Type</b>                  | <b>Description</b>                                                                   |
|------------------------------|--------------------------------------------------------------------------------------|
| $" +"$                       | <b>Addition</b>. This operator adds the second operand to the first operand.<br/><b>Supported variable types:</b> int, long, double, string.|
| $"-"$                        | <b>Subtraction</b>. This operator subtracts the second operand from the first operand.<br/><b>Supported variable types:</b>  int, long, double.   |
| $"*"$                        | <b> Multiplication</b>. This operator multiplies the first operand by the second operand.<br/><b>Supported variable types:</b>  int, long, double.    |
| $"/"$                        | <b>Division</b>. This operator divides the first operand by the second operand.<br/><b>Supported variable types:</b> int, long, double.  |
| $"=", "=="$, "eq"            | <b>Equal.</b> This operator returns true if the first operand is equal to the second operand.<br/><b>Supported variable types:</b> int, long, double, string, bool, date and object.  |
| $"!=", "<>"$, "ne"           | <b>Not equal.</b> This operator returns true if the first operand is not equal to the second operand.<br/><b>Supported variable types:</b> int, long, double, string, bool, date and object.  |
| $">"$ , "gt"                 | <b>Greater than.</b> This operator returns true if the first operand is greater than the second operand.<br/><b>Supported variable types:</b> int, long, double, string and date. |
| $"<"$, "lt"                  | <b>Less than.</b> This operator returns true if the first operand is less than the second operand.<br/><b>Supported variable types:</b> int, long, double, string and date.  |
| $">="$ , "ge"                | <b>Greater or equal.</b> This operator returns true if the first operand is greater than or equal to the second operand.<br/><b>Supported variable types:</b> int, long, double, string and date.    |
| $"<="$, "le"                 | <b>Less or equal.</b> This operator returns true if the first operand is less than or equal to the second operand.<br/><b>Supported variable types:</b> int, long, double, string and date.   |
| "&&", "and"                  | <b>And.</b> This operator performs a logical-AND of its bool operands.<br/><b>Supported variable types:</b> bool.   |
| $"\|\|"$, "or"               | <b>Or.</b> This operator performs a logical-OR of its bool operands.<br/><b>Supported variable types:</b> bool. |
| $"!"$, "not"                 | <b>Not.</b> This operator is a unary operator that negates its operand. It returns true only if its operand is false.<br/><b>Supported variable types:<b/> bool.   |              
| $"[]", "."$                  | <b>Index.</b> This operator returns an array element at the specified index or a dictionary element with the specified string key. |
| $"??"$                       | <b>Ternary Operation.</b> This operator returns the first calculated value in chain of expressions. <br><b>Example:</b> If variable is not null displays variable value, else display text 'MyVar was not set'.<br/>```<log>{Globals['MyVar'] ?? 'MyVar was not set'}</log>``` |           

# **FUNCTIONS**

Functions in Sync360 are methods for set of predefined objects. There are only two predefined functions that doesn't bind to an object.

**NOTE:** Helper functions (sometimes simply called "helpers") are usually functions that wrap useful functionality that you're going to reuse repeatedly.
Table 4. Functions
| <b>Type</b>   | <b>Description</b>     |                                                                                                                                                                                                                                                                 
|---------------|------------------------|
| "isSet"       | <b>Is Set.</b> This function returns true if the operand has value (not null).    |                                                                                                                                                                                                      
| "Count"       | <b>Count.</b> This function returns the number of elements in the collection.     |
| Csv.Read      | <b>Description</b><br/> The function reads through a CSV file and creates the list of strings where every line is a separate record. The number of parameters specified may vary. And this overloaded function may be used several times throughout the script. If some parameters are not specified, the default values listed in the script are used instead, e.g. Read(string path, string quote = DefaultQuote).<br/><b> Parameters</b><br/> Read(param1, param2, param3, param4, param5, param6, param7, param8) <br/> $\bullet$ string path - path to a file and its name.<br/>$\bullet$ Encoding encoding —the type of encoding used in a file. <br/> $\bullet$ string delimiter - delimiter used in a file. <br/> $\bullet$ string quote — multiline text delimiter used in a file. <br/> $\bullet$ string comment - comment symbol used in a file. <br/> $\bullet$ bool trim - checks whether value trimming is required or not. <br/> $\bullet$ bool hasHeader -checks whether a CSV file has a header or not. </br> $\bullet$ int topLinesToSkip - the number of lines to skip in a file.<br/> <b>Example</b> |
| Csv.Write    | <b>Description</b><br>The function transforms CRM records and writes it into a CSV file in the correct form. Since CSV files has a specific structure, scripts create a dictionary for each line and each<br>dictionary has a key (a column header) and a value (a line value). Below is the example of this code:<br>```<set var="csv">{new List()}</set> <for var="record" in="records"> <set var="values">{new Dictionary()}</set> <for var="key" in="record.Keys">  <set var="values[key]>{record[key]}</set> <set var="csv[]">(values)</set> </for>``` <br/> <b>Parameters</b><br>Write(param1, param2, param3, param4, param5, param6,param7, param8)<br> $\bullet$ string path — a path to a file and its name.<br> $\bullet$ Encoding encoding — the type of encoding used in a file.<br> $\bullet$ string delimiter-delimiter used in a file.<br> $\bullet$ string quote — multiline text delimiter used in a file.<br> $\bullet$ string comment - comment symbol used in a file.<br> $\bullet$ bool trim - checks whether value trimming is required or not.<br> $\bullet$ bool hasHeader - checks whether a CSV file has a header or not.<br> $\bullet$ int topLinesToSkip - the number of lines to skip in a file.<br/> <b>Example</b> |
| Csv.Open   | <b>Description</b><br><b>Paremeters</b><br><b>Example</b> |                                                                                                                                                                                                                              
|  FileUtils. WriteToFile | <b>Description</b><br/> The function writes specified string to a file. It is usually           |
|                          | used for logging. Last parameter instructs to append to a               |
|                          | file.                                                                   |
|                          |                                                                         |
|                          | <b>Parameters</b>                                                       |
|                          | WriteToFile(string, string, bool)                                       |
|   | string fileName—a name of the file where the                            |
|                          | string will be added.                                                   |
|                          | string text-a text that will be added to a file.                        |
|                          | bool isAppend—appends a text to a file.                                 |
|                          |                                                                         |
|                          | <b>Example</b>                                                          |
|                          | <set>{FileUtils.WriteToFile("c:\temp\log.txt",</set>                    |
|                          | "Test", true) }                                                         |
|                          | <b>Description</b>                                                      |
|                          | The function is used to read predefined GUIDS from text                 |
|                          | file. GUIDS should be placed line by line. The result will be           |
|                          | array variable.                                                         |
|                          | <b>Parameters</b>                                                       |
| FileUtils.ReadIdsFromFil |                                                                         |
| e                        | ReadIdsFromFile(string)<br>string fileName—a name of the file GUIDS are |
|                          | taken from.                                                             |
|                          |                                                                         |
|                          | <b>Example</b>                                                          |
|                          | <set< td=""></set<>                                                     |
|                          | var="Ids">{FileUtils.ReadIdsFromFile(".\UserGuids.txt                   |
|                          | ") $\}$<br><b>Description</b>                                           |
|                          | Opens a file, reads the contents of the file into a byte array,         |
| FileUtils. ReadFile      | and then closes the file.                                               |
|                          |                                                                         |
|                          | <b>Parameters</b>                                                       |
|                          | ReadFile(string)                                                        |
|                          | string fileName—a name of the processed file.                           |
|                          |                                                                         |
|                          | <b>Example</b>                                                          |
|                          |                                                                         |

|                                | <set<br>var="Bytes"&gt;{FileUtils.ReadFile(".\UserGuids.txt")}<!--</th--></set<br> |
|--------------------------------|------------------------------------------------------------------------------------|
|                                | set>                                                                               |
|                                | <b>Description</b>                                                                 |
|                                | Open a file, reads the contents of the file into a byte array,                     |
|                                | converts the array into a Base64 string, and then closes the                       |
|                                | file.                                                                              |
|                                |                                                                                    |
| FileUtils.ReadFileAsBase<br>64 | <b>Parameters</b>                                                                  |
|                                | ReadFileAsBase64(string)                                                           |
|                                | string fileName—a name of the processed file.                                      |
|                                | <b>Example</b>                                                                     |
|                                | <set< td=""></set<>                                                                |
|                                | var="Bytes">{FileUtils.ReadFileAsBase64(".\UserGuids.txt")}<br>                    |
|                                | <b>Description</b>                                                                 |
|                                | Returns the list of all files and subdirectories in a specified                    |
|                                | path.                                                                              |
| FileUtils.DirectoryEntri       |                                                                                    |
| es                             | <b>Parameters</b>                                                                  |
|                                | DirectoryEntries(string)                                                           |
|                                | string path—a path to a required directory.                                        |
|                                |                                                                                    |
|                                | <b>Example</b>                                                                     |
|                                | <b>Description</b>                                                                 |
|                                | Moves a specified file to a new location, providing the                            |
|                                | option to specify a new file name.                                                 |
|                                | <b>Parameters</b>                                                                  |
| FileUtils.MoveFile             | MoveFile(string, string)                                                           |
|                                | string source File Name-a path to a source file.                                   |
|                                | string destFileName—a new path to a file.                                          |
|                                |                                                                                    |
|                                | <b>Example</b>                                                                     |
|                                | <set>{FileUtils.MoveFile("c:\temp\loq.txt",<br/>"c:\temp2\log.txt") }</set>        |
|                                | <b>Description</b>                                                                 |
| FileUtils.GetFiles             | Returns the names of files (including their paths) that                            |
|                                | match the specified search pattern in the specified                                |
|                                | directory.                                                                         |

|                  | <b>Parameters</b>                                            |
|------------------|--------------------------------------------------------------|
|                  | GetFiles(string, string)                                     |
|                  | string folder-                                               |
|                  | string searchPattern-                                        |
|                  |                                                              |
|                  | <b>Example</b>                                               |
|                  | <set>{FileUtils.GetFiles(@"c:\", "c*")}</set>                |
|                  | <b>Description</b>                                           |
|                  | The function archives files into a zip-archive in a selected |
|                  | folder.                                                      |
|                  | <b>Parameters</b>                                            |
| FileUtils.Zip    |                                                              |
|                  | Zip(param1, param2)                                          |
|                  | string sourceFolder-a path to a source folder.               |
|                  | string zipFile-a file that will be archived.                 |
|                  | <b>Example</b>                                               |
|                  |                                                              |
|                  | <b>Description</b>                                           |
|                  | The function extracts files from a zip-archive in a selected |
|                  | folder.                                                      |
|                  |                                                              |
|                  | <b>Parameters</b>                                            |
| FileUtils.Unzip  | Unzip(param1, param2)                                        |
|                  | string zipFile-a name of an archive.                         |
|                  | string extractFolder-a path to an extraction folder.         |
|                  |                                                              |
|                  | <b>Example</b>                                               |
|                  | <b>Description</b>                                           |
|                  | Converts plain text to an html string or to an html          |
|                  | document.                                                    |
|                  |                                                              |
| Html.ToPlainText | <b>Parameters</b>                                            |
|                  | ToPlainText(string)                                          |
|                  | string html-converts an html string to an html               |
|                  | document.                                                    |
|                  |                                                              |

| Method                        | Description                                                                                                                                                                                                                                                                                                                                                                                                                          |
|-------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Http.SetUseDefaultCredentials | <b>Description</b><br>Sets the use of default credentials for each http<br>connection.<br><b>Parameters</b><br>SetUseDefaultCredentials(bool)<br>bool value<br><b>Example</b>                                                                                                                                                                                                                                                        |
| Http.SetClientEncoding        | <b>Description</b><br>Sets a client encoding for connections.<br><b>Parameters</b><br>SetClientEncoding(string)<br><b>Example</b>                                                                                                                                                                                                                                                                                                    |
| Http.Get                      | <b>Description</b><br>The HTTP GET method requests a representation of the<br>specified resource.<br><b>Parameters</b><br>Get(string, string, string, string, string object)<br>string url-URL used in the request.<br>string username-retrieves requested username.<br>string password-retrieves requested password.<br>string domain-retrieves requested password.<br>object headers-retrieves specific headers.<br><b>Example</b> |

|            | <set< th=""></set<>                                          |
|------------|--------------------------------------------------------------|
|            | var="response">{http.get('http://www.example.com')} <br set> |
|            | <b>Description</b>                                           |
|            | The HTTP PUT request method creates a new resource or        |
|            | replaces a representation of the target resource with the    |
|            | request payload.                                             |
|            |                                                              |
|            | <b>Parameters</b>                                            |
|            | Put(string url, string data, object headers)                 |
| Http.Put   | string url-URL used in the request.                          |
|            | string data-data sent as parameters to URL in the            |
|            | request.                                                     |
|            | object headers                                               |
|            |                                                              |
|            | <b>Example</b>                                               |
|            | $set$<br>var="response">{http.put('http://www.example.com',  |
|            | 'some data', 'headers go here') }                            |
|            | <b>Description</b>                                           |
|            | The HTTP POST method sends data to the server.               |
|            |                                                              |
|            | <b>Parameters</b>                                            |
|            | Post(string, string, string, string, string, object)         |
|            | string url-URL used in the request.                          |
|            | string login.                                                |
| Http.Post  | string password.                                             |
|            | string domain.                                               |
|            | string data—data sent as parameters to URL in the            |
|            | request.                                                     |
|            | object headers.                                              |
|            |                                                              |
|            | <b>Example</b>                                               |
|            | $set$<br>var="response">{http.put('http://www.example.com',  |
|            | 'some data', 'headers go here') }                            |
|            | <b>Description</b>                                           |
|            | The HTTP PATCH request method applies partial                |
|            | modifications to a resource.                                 |
|            | <b>Parameters</b>                                            |
| Http.Patch | Patch(string, string, string, string, string, object)        |
|            | string url-URL used in the request.                          |
|            | string login.<br>$\bullet$                                   |
|            | string password.                                             |
|            | string domain.                                               |
|            |                                                              |

|                | string data.<br>$\bullet$                                                                 |
|----------------|-------------------------------------------------------------------------------------------|
|                | object headers.                                                                           |
|                |                                                                                           |
|                | <b>Example</b><br>$<$ set                                                                 |
|                | var="response">{http.patch('http://www.example.com',<br>'some data', 'headers go here') } |
|                | <b>Description</b>                                                                        |
|                | Downloads the file that URL contains, e. g. page, archive,                                |
|                | document.                                                                                 |
|                |                                                                                           |
|                | <b>Parameters</b>                                                                         |
| Http.Download  | Download(string)                                                                          |
|                | string url-an address that contains the required                                          |
|                | file.                                                                                     |
|                | <b>Example</b>                                                                            |
|                | <set< td=""></set<>                                                                       |
|                | var="response">{http.download('http://www.example.com<br>$')$ }                           |
|                | <b>Description</b>                                                                        |
|                | The Get. Raw method requests a representation of the                                      |
|                | specified resource but in unchanged, literal form.                                        |
|                |                                                                                           |
|                | <b>Parameters</b>                                                                         |
|                | GetRaw(string, int, int)                                                                  |
| Http.GetRaw    | string url<br>$\bullet$                                                                   |
|                | • int sendTimeout                                                                         |
|                | • int receiveTimeout                                                                      |
|                |                                                                                           |
|                | <b>Example</b>                                                                            |
|                |                                                                                           |
|                | <b>Description</b>                                                                        |
|                | Set cookie for the specified resource.                                                    |
|                | <b>Parameters</b>                                                                         |
|                |                                                                                           |
| Http.SetCookie | SetCookie(string, string)<br>string url                                                   |
|                | string cookie                                                                             |
|                |                                                                                           |
|                | <b>Example</b>                                                                            |
|                |                                                                                           |
|                |                                                                                           |
| Http.GetCookie | <b>Description</b>                                                                        |
|                | Gets cookie from the specified resource.                                                  |

|                    | <b>Parameters</b>                                                |
|--------------------|------------------------------------------------------------------|
|                    | GetCookie(string)                                                |
|                    | string url                                                       |
|                    |                                                                  |
|                    | <b>Example</b>                                                   |
|                    | <b>Description</b>                                               |
|                    | Converts an object to JSON format.                               |
|                    | <b>Parameters</b>                                                |
| Json.ToJson        |                                                                  |
|                    | ToJson(object)                                                   |
|                    | object value                                                     |
|                    |                                                                  |
|                    | <b>Example</b><br><set>{Json.ToJson("DynamicDictionary")}</set>  |
|                    | <b>Description</b>                                               |
|                    | Converts an object to ASCII JSON format and removes              |
|                    | non-ASCII symbols.                                               |
|                    |                                                                  |
| Json.ToAsciiJson   | <b>Parameters</b>                                                |
|                    | ToAsciiJson(object value)                                        |
|                    | object value                                                     |
|                    |                                                                  |
|                    | <b>Example</b>                                                   |
|                    | <set>{Json.ToAsciiJson("DynamicDictionary")}</set>               |
|                    | <b>Description</b>                                               |
|                    | Converts JSON to a dictionary.                                   |
|                    |                                                                  |
|                    | <b>Parameters</b>                                                |
| Json.FromJson      | FromJson(string)                                                 |
|                    | string value-JSON string that will be converted.                 |
|                    |                                                                  |
|                    | <b>Example</b><br><set var="test1">{Json.FromJson(string)}</set> |
|                    | <log>(test1.GetType()) - (test1)</log>                           |
|                    | <b>Description</b>                                               |
|                    | Converts JSON to an object.                                      |
|                    |                                                                  |
| Json.GetDictionary | <b>Parameters</b>                                                |
|                    | GetDictionary(string)                                            |
|                    | string value                                                     |
|                    |                                                                  |
|                    | <b>Example</b>                                                   |
|                    | <set var="test1">{Json.GetDictionary(string)}</set>              |

|                       | $\langle \log \rangle$ (test1.GetType()) - (test1) $\langle \log \rangle$                                   |
|-----------------------|-------------------------------------------------------------------------------------------------------------|
|                       | <b>Description</b>                                                                                          |
|                       |                                                                                                             |
|                       | <b>Parameters</b>                                                                                           |
|                       | GetArrayFromJson(string)                                                                                    |
| Json.GetArrayFromJson | string value                                                                                                |
|                       |                                                                                                             |
|                       | <b>Example</b>                                                                                              |
|                       | <set< td=""></set<>                                                                                         |
|                       | var="test1">{Json.GetArrayFromJson(string)}                                                                 |
|                       | $\langle \text{log} \rangle$ (test1. GetType ()) - (test1) $\langle \text{log} \rangle$                     |
|                       | <b>Description</b>                                                                                          |
|                       | This function returns new random GUID.                                                                      |
|                       |                                                                                                             |
| "Utils.NewGuid"       | <b>Parameters</b>                                                                                           |
|                       |                                                                                                             |
|                       | <b>Example</b>                                                                                              |
|                       |                                                                                                             |
|                       |                                                                                                             |
|                       | <b>Description</b>                                                                                          |
|                       | This function identifies the substrings in a string array that                                              |
|                       | are delimited by one or more characters specified in an                                                     |
|                       | array, then places the substrings into a specified Unicode                                                  |
|                       | character array.                                                                                            |
| "Utils.Split"         |                                                                                                             |
|                       |                                                                                                             |
|                       | <b>Parameters</b>                                                                                           |
|                       |                                                                                                             |
|                       | <b>Example</b>                                                                                              |
|                       | <set var="Names">Roman; Joe; Michael</set>                                                                  |
|                       | <set var="NamesAr">{Utils.Split(Names,';')}</set>                                                           |
|                       | <b>Description</b>                                                                                          |
|                       | This function combines array values into string with                                                        |
|                       | specified delimiter.                                                                                        |
|                       |                                                                                                             |
| "Utils.Join"          | <b>Parameters</b>                                                                                           |
|                       |                                                                                                             |
|                       |                                                                                                             |
|                       | <b>Example</b>                                                                                              |
|                       | <set var="NamesAr">{['Roman','Joe','Michael']}</set><br><set var="NamesStr">{Utils.Join(NamesAr,';')}</set> |
|                       |                                                                                                             |
|                       | <b>Description</b>                                                                                          |
| "Utils.toUpper"       | This function returns a copy of this string converted to                                                    |
|                       | uppercase.                                                                                                  |
|                       |                                                                                                             |

|                  | <b>Parameters</b>                                           |
|------------------|-------------------------------------------------------------|
|                  | string                                                      |
|                  |                                                             |
|                  | <b>Example</b>                                              |
|                  |                                                             |
|                  | <b>Description</b>                                          |
|                  | This function returns a copy of this string converted to    |
|                  | lowercase.                                                  |
|                  |                                                             |
| "Utils.toLower"  | <b>Parameters</b>                                           |
|                  | string                                                      |
|                  |                                                             |
|                  | <b>Example</b>                                              |
|                  |                                                             |
|                  | <b>Description</b>                                          |
|                  | This function returns newline string ("\n").                |
| "Utils.NewLine"  |                                                             |
|                  | <b>Parameters</b>                                           |
|                  | <b>Example</b>                                              |
|                  | <b>Description</b>                                          |
|                  | This function returns a date type object that is set to the |
|                  | current date and time on this computer, expressed as the    |
|                  | local time.                                                 |
| "Utils.Now"      |                                                             |
|                  | <b>Parameters</b>                                           |
|                  |                                                             |
|                  | <b>Example</b>                                              |
|                  |                                                             |
|                  |                                                             |
|                  | <b>Description</b>                                          |
|                  | This function Returns a new string in which all occurrences |
|                  | of a specified Unicode String in the current string are     |
|                  | replaced with another specified Unicode character or        |
| "Utils. Replace" | String.                                                     |
|                  | <b>Parameters</b>                                           |
|                  | string                                                      |
|                  |                                                             |

|                        | <set var="str1">This is an example</set>              |
|------------------------|-------------------------------------------------------|
|                        | $<$ set                                               |
|                        | var="str2">{Utils.Replace(str1,'This','Here')}        |
|                        |                                                       |
|                        |                                                       |
|                        |                                                       |
|                        |                                                       |
|                        |                                                       |
|                        |                                                       |
|                        |                                                       |
|                        | <b>Description</b>                                    |
|                        |                                                       |
|                        | Checks whether the key exists in a dictionary or not. |
|                        |                                                       |
|                        |                                                       |
|                        | <b>Parameters</b>                                     |
|                        |                                                       |
|                        | IsKeyExist(object, string)                            |
| Utils. IsKeyExist      | object dict-the required dictionary.                  |
|                        |                                                       |
|                        | string key—the line that contains a key               |
|                        |                                                       |
|                        |                                                       |
|                        | <b>Example</b>                                        |
|                        | <set>{Utils.IsKeyExist("DictionaryExample",</set>     |
|                        | " $reg4y736"$ ) }                                     |
|                        |                                                       |
|                        | <b>Description</b>                                    |
|                        | Converts the string representation of a GUID to the   |
|                        | equivalent Guid structure.                            |
|                        |                                                       |
|                        |                                                       |
| Utils. ParseGUID       | <b>Parameters</b>                                     |
|                        | ParseGuid(string)                                     |
|                        |                                                       |
|                        | string guid-the required GUID.                        |
|                        |                                                       |
|                        | <b>Example</b>                                        |
|                        |                                                       |
|                        |                                                       |
|                        | <b>Description</b>                                    |
|                        |                                                       |
|                        | Compares two objects:                                 |
|                        | If objects are of the same type, it compares them as  |
|                        | they are.                                             |
|                        |                                                       |
|                        | If objects are of different types, it converts them   |
|                        | into strings and then compares the strings.           |
|                        |                                                       |
| Utils. TextualltEquals |                                                       |
|                        | <b>Parameters</b>                                     |
|                        | TextuallyEquals(object, object, bool)                 |
|                        |                                                       |
|                        | object one-the first object in a pair.                |
|                        | object two- the second object in a pair.              |
|                        | bool ignoreCase-manages case sensitivity.             |
|                        |                                                       |
|                        |                                                       |
|                        | <b>Example</b>                                        |

|                                                 | <b>Description</b>                                                                                                           |
|-------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| Utils. Right<br>Utils. Left                     | <b>Parameters</b><br>Right(string, int)<br>string str.<br>int charCount<br>Left(string, int)<br>string str.<br>int charCount |
|                                                 | <b>Example</b>                                                                                                               |
|                                                 | <b>Description</b><br>Encode to/Decode from the standard HTML encoding.                                                      |
| Utils. Encode<br>Utils. Decode                  | <b>Parameters</b><br>Encode(string)<br>string str<br>Decode(string)                                                          |
|                                                 | string str<br><b>Example</b>                                                                                                 |
|                                                 | <b>Description</b><br>Converts date to string and vice versa.                                                                |
| Utils. DateToString<br>Utils.StringToDate       | <b>Parameters</b><br>DateToString(DateTime, string)<br>DateTime date-the exact date to be converted.<br>string format.       |
|                                                 | StringToDate(string, string)<br>string date-the exact string to be converted.<br>string format.                              |
|                                                 | <b>Example</b>                                                                                                               |
| Utils.SpeecifyKindUtc<br>Utils.SpecifyKindLocal | <b>Description</b><br>Applies a specified timezone to date.                                                                  |

| Utils.SpecifyKindInspecifi | <b>Parameters</b>                                                      |
|----------------------------|------------------------------------------------------------------------|
| ed                         | SpecifyKindUtc(DateTime)                                               |
|                            | DateTime date<br>$\bullet$                                             |
|                            |                                                                        |
|                            | SpecifyKindLocal(DateTime)                                             |
|                            | DateTime date                                                          |
|                            |                                                                        |
|                            | SpecifyKindUnspecified(DateTime)                                       |
|                            | DateTime date                                                          |
|                            |                                                                        |
|                            | <b>Example</b>                                                         |
|                            |                                                                        |
|                            |                                                                        |
|                            | <b>Description</b>                                                     |
|                            | The function executes a provided string as a command.                  |
|                            | <b>Parameters</b>                                                      |
| Utils. Eval                |                                                                        |
|                            | Eval(string)                                                           |
|                            | string expression                                                      |
|                            |                                                                        |
|                            | <b>Example</b><br><set var="NeedProcess">{Utils.Eval(checkCond)}</set> |
|                            | <b>Description</b>                                                     |
|                            | The function gets random values of random types.                       |
|                            |                                                                        |
|                            | <b>Parameters</b>                                                      |
|                            | GetRandom(string, object)                                              |
| Utils.GetRandom            | string type                                                            |
|                            | object max                                                             |
|                            |                                                                        |
|                            | <b>Example</b>                                                         |
|                            |                                                                        |
|                            |                                                                        |
|                            |                                                                        |
|                            | <b>Description</b>                                                     |
|                            | Loads an XElement from a file, optionally preserving white             |
|                            | space, setting the base URI, and retaining line information.           |
|                            | <b>Parameters</b>                                                      |
| Xml.Load                   |                                                                        |
|                            | Load(string, int)                                                      |
|                            | string uri                                                             |
|                            | int options                                                            |
|                            |                                                                        |
|                            | <b>Example</b><br><set xml="Xml.Load(fileName)"></set>                 |
|                            |                                                                        |

|             | <b>Description</b>                                    |
|-------------|-------------------------------------------------------|
|             | Loads an XElement from a file.                        |
|             |                                                       |
| Xml.LoadEnc | <b>Parameters</b>                                     |
|             | LoadEnc(string)                                       |
|             | string uri<br>$\bullet$                               |
|             | <b>Example</b>                                        |
|             |                                                       |
|             | <b>Description</b>                                    |
|             | Load an XElement from a string that contains XML,     |
|             | optionally preserving white space and retaining line  |
|             | information.                                          |
|             | <b>Parameters</b>                                     |
|             | Parse(string, int)                                    |
|             | string text                                           |
| Xml.Parse   | int options                                           |
|             |                                                       |
|             | <b>Example</b>                                        |
|             | <set var="xmlSource"><!-- [CDATA]</th--></set>        |
|             | $<$ root $>$<br><add name="abdc">Test data</add>      |
|             | <add name="n2">Second text</add>                      |
|             | data                                                  |
|             | $\langle$ /root>                                      |
|             | $]$ $]\times$ /set>                                   |
|             | <set xml="Xml. Parse(xmlSource, 1)"></set>            |
|             | <b>Description</b>                                    |
|             | Searches and selects values from within the specified |
|             | element of an XML file and returns these values as an |
|             | array.                                                |
|             |                                                       |
|             | <b>Parameters</b>                                     |
| Xml.Select  | Select(XElement, string, IDictionary)                 |
|             | XElement xml                                          |
|             | string expression                                     |
|             | IDictionary fields                                    |
|             | <b>Example</b>                                        |
| Xml.ToXml   | <b>Description</b>                                    |
|             |                                                       |

| Xml.FromXml                                                                                                                                                                                    | Passes an object (dictionary, list) to XML and a name of the root element and returns an XML string.                                                                                  |                                          |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------|
|                                                                                                                                                                                                |                                                                                                                                                                                       | Passes XML string and returns an object. |
| Parameters                                                                                                                                                                                     | ToXml(object, string)                                                                                                                                                                 |                                          |
| object value                                                                                                                                                                                   | string rootElementName                                                                                                                                                                |                                          |
|                                                                                                                                                                                                |                                                                                                                                                                                       |                                          |
| FromXml(string)                                                                                                                                                                                | string xml                                                                                                                                                                            |                                          |
|                                                                                                                                                                                                |                                                                                                                                                                                       | Example                                  |
| Math. This .NET object is used for common mathematical functions, for the list of supported methods refer to http://msdn.microsoft.com/en-us/library/system.math.aspx                          |                                                                                                                                                                                       |                                          |
| File.*                                                                                                                                                                                         | This .NET object is used for operations with files, for the list of supported methods refer to http://msdn.microsoft.com/en-us/library/system.io.file.aspx                            |                                          |
| Path. This .NET object is used for operations with filesystem, for the list of supported methods refer to http://msdn.microsoft.com/en-us/library/system.io.path.aspx                          |                                                                                                                                                                                       |                                          |
| Encoding.*                                                                                                                                                                                     | This .NET object is used for character and text encoding, for the list of supported methods and properties refer to http://msdn.microsoft.com/en-us/library/system.text.encoding.aspx |                                          |
| Timespans. This .NET object is used for operations with time intervals. for the list of supported methods and properties refer to http://msdn.microsoft.com/en-us/library/system.timespan.aspx |                                                                                                                                                                                       |                                          |

# DECLARATION OF THE VARIABLES

**Set** operation is used for declaration and modification of the variables. The name of variable can be added as **var** attribute of **set** element. However, you can skip this attribute and define variable explicitly.

### Simple variable

To declare a simple variable, all that is needed is the keyword "**var**" followed by the variable name and its value.

*Example:* 

```
<set var="accountid">{value}</set>
```

The other way to do same operation *Example:* 

```
<set accountid="value" />
```

## Array variable

To declare an array variable simply put the values in square brackets separated by comma.

*Examples:*

```
<set var="crmserver">{["crm1","crm2"]}</set>
<set cities="['New York','Los Angeles','Chicago']"/>
```

## Dictionary variables

To declare the dictionary variable use construction "*{new Dictionary()}*" and then set its items list

using the similar way as arrays.

*Example:*

```
<set Address="new Dictionary()" />
<set var="Address['Street']">street</set>
<set var="Address['City']">city</set>
<set var="Address['Country']">country</set>
```

### List variables

List declaration is very similar to Dictionary, use construction "*{new List()}*" and then set its variables, but without keys.

```
<set Names="new List()" />
<set var="Names[]">Michael</set>
<set var="Names[]">Joe</set>
```

## Object variables

You can define any kind of object in Sync360 and there are four different methods. Let's look on

examples:

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
</set>
```

#### *Method 3:*

<set Person="new Object()" Person.Name="'Michael'" Person.Lastname="'White'"/>

#### *Method 4:*

```
<set Person="['Name':'Michael','Lastname':'White']"/>
```

# FLOW CONTROL OPERATIONS

### Break

**Break** construction can be used to exit the cycle based on specific condition. This construction is applicable for any time of cycle.

*Example:*

```
<set var="testCycle">{['test1','test2','test3','test4','test5']}</set>
    <log>Start cycle.</log>
    <for var="t" in="testCycle">
         <log>{t}</log>
             <break if="t eq 'test3'"/>
    </for>
         <log>End cycle.</log>
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

## **Continue**

**Continue** construction can be used to skip cycle logic that is located under this construction and move on to the next cycle iteration.

```
<set var="testCycle">{['test1','test2','test3','test4']}</set>
     <log>Start cycle.</log>
    <for var="t" in="testCycle">
         <continue if="t eq 'test3'"/>
         <log>{t}</log>
    </for>
    <log>End cycle.</log>
<set var="testCycle">{['test1','test2','test3','test4']}</set>
    <log>Start cycle.</log>
    <for var="t" in="testCycle">
         <if condition="t eq 'test3'">
             <continue/>
        \langle/if>
         <log>{t}</log>
     </for>
     <log>End cycle.</log>
```

### For

**For** construction is used when a code block needs to be executed a certain amount of times. There are 2 types of usage syntax listed below.

*Examples:*

```
<set crmservers="['crm','crm4','crm5']"/>
<for var="i" from="0" to="crmservers.Count - 1" step="1">
    <log>{crmservers[i]}</log>
</for>
<set crmservers="['crm','crm4','crm5']"/>
<for var="crmserver" in="crmservers">
    <log> {crmserver}</log>
</for>
```

## If and Unless

**IF** construction can be used when it is necessary to execute a code block only if a specified condition in

the **"condition"** attribute is equal to *true*.

*Example:*

```
<set var="a">{10}</set>
<set var="b">{15}</set>
<if condition="a gt b">
     <log>a is greater then b</log>
\langle/if>
<if condition="a lt b">
     <log>a is less then b</log>
\langle/if>
<if condition="a eq b">
     <log>a is equal to b</log>
\langle/if>
```

**Unless** construction is used only in conjunction with **IF** to execute code block if a condition is true and another code **IF** the condition is not true.

Any operator can use **IF** or **Unless**.

```
<set Colors="['red','gray','yellow']"/>
<log if="Colors[1] = 'gray'">Have a GRAY color</log>
<log unless="Colors[1] = 'blue'">Not a BLUE color</log>
<set var="MyColor1" if="Colors[2] = 'yellow'">Have a YELLOW color</set>
<log>{MyColor1}</log>
<set var="MyColor2" unless="Colors[1] = 'red'">Not a red color</set>
<log>{MyColor2}</log>
```

### **While**

**While** construction can be used when a code block need to be executed while specified condition in the **"condition"** attribute is equal to *true*.

*Example:*

```
<set var="crmservers">{['crm','crm4','crm5']}</set>
<set var="counter">{0}</set>
<while condition="counter lt crmservers.Count">
     <log>{crmservers[counter]}</log>
    <set var="counter">{counter + 1}</set>
</while>
```

## THEN-ELSE

**THEN–ELSE** construction can be used if one code block should be executed if condition is true and another code should be executed if condition is false.

```
<set var="a">{10}</set>
<set var="b">{15}</set>
<set var="c">{10}</set>
<if condition="a ne b">
         <then>
             <log>a not equal b</log>
         </then>
         <then if="b ne c">
             <log>b not equal c</log>
         </then>
         <else>
              <log>a equal b</log>
         </else>
         <else if="b eq c">
             <log>b equal c</log>
         </else>
\langle/if>
```

# DATA OPERATIONS

## **Context**

**Context** construction is used when it is necessary to perform a set of operation in a specific user context. Context can be used only if current user has permissions for Impersonation usage on server.

| <b>Attribute</b> | <b>Description</b>                                                                  | <b>Usage</b> |
|------------------|-------------------------------------------------------------------------------------|--------------|
| for              | A server name that a context is created for.                                        | Required     |
| user             | A user Id (MS CRM) / a user E-Mail address (Exchange) that the context is used for. | Required     |

Table 5. Context constriction attributes.

*Example:*

```
<context for="exchange" user="{userEmail}">
     <select from="exchange" entity="contact" var="contacts">
         <where>
             \langle or \rangle <condition attr="crmLinkState" op="eq">0
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

## Select

**Select** operation looks for records of specific type in accordance with the criteria and stores the retrieved data into a specified variable. The result is always an array of dictionaries. The following tables enumerate the attributes and child elements for Read operation.

| <b>Attribute</b> | <b>Description</b>                                  | <b>Usage</b> |
|------------------|-----------------------------------------------------|--------------|
| from             | A server name that Read operation will run for.     | Required     |
| entity           | A type of records that the search will perform for. | Optional     |

#### Table 6. Read operation attributes.

| var   | The variable name that will contain the result of search operation. | Optional |
|-------|---------------------------------------------------------------------|----------|
| count | A number of records that will be returned by search                 | Optional |
| page  | Determine from which page return records                            | Optional |

**NOTE:** Here in after, if "entity" attribute has not been specified, the "contact" entity will be used by default.

| First Name | Last Name | Age |
|------------|-----------|-----|
|            |           |     |

| <b>Element</b> | <b>Description</b>                                                                         | <b>Usage</b> |
|----------------|--------------------------------------------------------------------------------------------|--------------|
| attr           | An attribute name the found records always contain.                                        | Required     |
| where          | A criteria element contains conditions set that will be used to find<br>records to update. | Optional     |
| order          | Used for sorting search results by ordering.                                               | Optional     |
| query          | A way to specify native query for SQL datasource                                           | Optional     |

*Example:*

To read all names and ids of first 50 accounts where Country attribute value is equal to "US" and sort them by name attribute in descending order, you could use Select operation as follows:

```
<select from="crmserver" entity="account" var="accounts" count="50">
    <where>
         <condition attr="address1_country" op="eq">US</attr>
    </where>
    <attr name="name"/>
    <attr name="accountid"/>
    <order by="name" desc="true"/>
</select>
```

**Select** operation supports native SQL queries using query element. You can also pass parameters to the query using **attr** element. The entity element is required to be specified for the code to be valid, but with this method it's not used.

*Example:*

```
<select from="db" entity="tasks" var="tasks">
    <query>
         select Tasks.Name as 'ttt', Instances.Name, Instances.InstanceID from tasks, Instances where 
tasks.InstanceID = Instances.InstanceID and Instances.Title = @title
    </query>
     <attr name="@title">Main work</attr>
</select>
```

**Select–join** operation looks for records of specific type in accordance with the criteria applied to the entity itself and related entities.

Table 8. Join operation attributes.

| <b>Attribute</b> | <b>Description</b>                                                                                                                                                                             | <b>Usage</b> |
|------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| type             | Join type (based on used connector)<br>For example, for CRM connector:<br>https://docs.microsoft.com/en-us/previous-<br>versions/dynamicscrm-2016/developers-<br>guide/gg327702%28v%3dcrm.8%29 | Required     |
| entity           | Related entity name.                                                                                                                                                                           | Required     |
| to               | Field name of the related entity.                                                                                                                                                              | Required     |
| from             | Field name of the main entity.                                                                                                                                                                 | Required     |
| as               | Attribute name for storing the result. Simplifies data<br>access.                                                                                                                              | Optional     |

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
</select>
```

### Create

**Create** operation creates a specified entity record with defined attributes and stores a copy of created data into the specified variable. This operation returns an array of dictionaries.

The following tables list the attributes and children elements for Create operation.

| <b>Attribute</b> | <b>Description</b>                                    | <b>Usage</b> |
|------------------|-------------------------------------------------------|--------------|
| $\ln$            | A server name that the create operation performs for. | Required     |
| entity           | A type of records that will be created.               | Optional     |

Table 9. Create operation attributes.

| The variable name that will contain a result of Create operation. | Required |
|-------------------------------------------------------------------|----------|
|-------------------------------------------------------------------|----------|

Table 10. Create operation children elements.

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

### Update

**Update** operation updates the specified entity records corresponding with the search criteria with predefined attributes set. The operation returns an array of dictionaries.

| Attribute | Description                                           | Usage    |
|-----------|-------------------------------------------------------|----------|
| ın        | A server name that the update operation performs for. | Required |
| entity    | A type of records that will be updated.               | Optional |

Table 11. Update operation attributes.

| First Name | Last Name | Table 12. Update operation children elements. | Age |
|------------|-----------|-----------------------------------------------|-----|
| John       | Doe       |                                               | 25  |

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

## Delete

**Delete** operation deletes a specified entity records satisfied to search criteria.

| <b>Attribute</b> | <b>Description</b>                                    | <b>Usage</b> |
|------------------|-------------------------------------------------------|--------------|
| $\ln$            | A server name that the delete operation performs for. | Required     |
| entity           | A type of records that will be deleted.               | Optional     |

Table 13. Delete operation attributes.

| Element | <b>Description</b>                                                   | <b>Usage</b> |
|---------|----------------------------------------------------------------------|--------------|
| where   | A criteria element contains conditions set that will be used to find | Required     |
|         | records to delete.                                                   |              |

#### *Example:*

The following script deletes an Exchange contact by known EntryId:

```
<delete in="exchange">
     <where>
         <condition attr="EntryId" op="eq">{EntryId}</condition>
    </where>
</delete>
```

## Batch

**Batch** operation can be used to combine create, update, or delete operations into one batch to minimize the number of requests to target system.

| <b>Attribute</b> | <b>Description</b>                                                                                                    | <b>Usage</b> |
|------------------|-----------------------------------------------------------------------------------------------------------------------|--------------|
| for              | A server name that Batch operation will run for.                                                                      | Required     |
| continueOnError  | false – do not continue processing the next request on error.                                                         | Required     |
| var              | The variable name that will contain the response from target system. Response is presented only for create operation. | Required     |

Table 15. Batch operation attributes.

| returnResponses | true—return responses from each message request |          |
|-----------------|-------------------------------------------------|----------|
| processed.      |                                                 | Optional |
|                 | false—do not return responses.                  |          |

```
 <set var="PageSize">{10}</set>
      <log>Script started at {Utils.Now}</log>
      <set var="csvContacts">{Csv.Read('c:/temp/contact lite test.txt', Encoding.GetEncoding('utf-8'), 
';')}</set>
      <set var="Continue">{true}</set>
      <set var="RecordsLeft">{csvContacts.Count}</set>
      <set var="Page">{0}</set>
      <log>Import started at {Utils.Now}</log>
      <log>Batch size = {PageSize}</log>
      <while condition="Continue">
          <set var="LastPage">{RecordsLeft lt PageSize}</set>
          <set var="CurrentPage" if="LastPage">{RecordsLeft}</set>
          <set var="CurrentPage" if="!LastPage">{PageSize}</set>
          <batch for="crm" continueOnError="true" var="result" returnResponses="true">
             <for var="iter" from="0" to="CurrentPage-1" step="1">
                <set var="index">{(PageSize * Page) + iter}</set>
                <set var="cnt">{csvContacts[index]}</set>
                <sandbox>
                   <create in="crm" entity="contact">
                      <attr if="cnt['lastname'].isSet and cnt['lastname'] ne ''"
name="lastname">{cnt['lastname']}</attr>
                      <attr if="cnt['firstname'].isSet and cnt['firstname'] ne ''"
name="firstname">{cnt['firstname']}</attr>
                      <attr if="cnt['salutation'].isSet and cnt['salutation'] ne ''"
name="salutation">{cnt['salutation']}</attr>
                      <attr if="cnt['birthdate'].isSet and cnt['birthdate'] ne ''"
name="birthdate">{cnt['birthdate']}</attr>
                       <attr if="cnt['slaname'].isSet and cnt['slaname'] ne ''"
name="slaname">{cnt['slaname']}</attr>
                      <attr if="cnt['telephone1'].isSet and cnt['telephone1'] ne ''"
name="telephone1">{cnt['telephone1']}</attr>
                      <attr if="cnt['telephone2'].isSet and cnt['telephone2'] ne ''"
name="telephone2">{cnt['telephone2']}</attr>
                      <attr if="cnt['telephone3'].isSet and cnt['telephone3'] ne ''"
name="telephone3">{cnt['telephone3']}</attr>
 <attr if="cnt['websiteurl'].isSet and cnt['websiteurl'] ne ''"
name="websiteurl">{cnt['websiteurl']}</attr>
                      <attr name="statecode">{1}</attr>
                      <attr name="statuscode">{2}</attr>
                   </create>
                </sandbox>
             </for>
          </batch>
          <log>{result}</log>
          <set var="RecordsLeft">{RecordsLeft - CurrentPage}</set>
          <set var="Page">{Page + 1}</set>
          <set var="Continue" if="RecordsLeft le 0">{false}</set>
      </while>
      <log>Import finished at {Utils.Now}</log>
```

```
<!-- Example: (Batch error handling) -->
    <if condition="result.IsFaulted">
         <log>An error occured during batch request.</log>
         <for var="resp" in="result.Responses">
             <if condition="resp.Fault.isSet">
                  <log>Error on index {resp.RequestIndex} with message: {resp.Fault.Message}</log>
             \langle/if>
         </for>
   \langle/if>
```

*Example: (Batch response handling for create operation)*

```
<set var="newIds">{new List()}</set>
     <for var="response" in="result.Responses">
         <if condition="!response.Fault.isSet">
             <then>
                 <set var="innerResponse">{response.Response}</set>
                 <set if="innerResponse.ResponseName eq 'Create'" var="newIds[]">{innerResponse.id}</set>
                 <set if="innerResponse.ResponseName eq 'ExecuteTransaction'"
var="newIds[]">{innerResponse.Responses[0].id}</set>
             </then>
             <else>
                 <set var="newIds[]">{null}</set>
             </else>
        \langle/if>
</for>
```

### **Transaction**

**Transaction** operation can be used to combine create, update or delete operations into one transaction to minimize the number of requests to target system.

| <b>Attribute</b> | <b>Description</b>                                                                                                          | <b>Usage</b> |
|------------------|-----------------------------------------------------------------------------------------------------------------------------|--------------|
| for              | A server name that Batch operation will run for.                                                                            | Required     |
| var              | The variable name that will contain the response<br>from target system. Response is presented only<br>for create operation. | Required     |
| returnResponses  | true - return responses from each message<br>request processed.<br>false - do not return responses.                         | Optional     |

*Example: (file for importing attached separetly)*

```
<set var="PageSize">{10}</set>
         <log>Script started at {Utils.Now}</log>
         <set var="csvContacts">{Csv.Read('c:/temp/contact lite test.txt', Encoding.GetEncoding('utf-8'), 
';')}</set>
```

```
<set var="Continue">{true}</set>
```

```
<set var="RecordsLeft">{csvContacts.er}</set>
          <set var="Page">{0}</set>
         <log>Import started at {Utils.Now}</log>
          <log>Batch size = {PageSize}</log>
          <while condition="Continue">
                   <set var="LastPage">{RecordsLeft lt PageSize}</set>
                   <set var="CurrentPage" if="LastPage">{RecordsLeft}</set>
                    <set var="CurrentPage" if="!LastPage">{PageSize}</set>
         <transaction for="crm" continueOnError="true" var="result" returnResponses="true">
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
                                                           <attr if="cnt['slaname'].isSet and cnt['slaname'] 
ne ''" name="slaname">{cnt['slaname']}</attr>
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
                   </ transaction>
                   <log>{result}</log>
         <set var="RecordsLeft">{RecordsLeft - CurrentPage}</set>
         <set var="Page">{Page + 1}</set>
        <set var="Continue" if="RecordsLeft le 0">{false}</set>
       </while>
<log>Import finished at {Utils.Now}</log>
```

# SEARCH CRITERIA

## Two Operands Condition Operators

There are 6 types of two operands condition operators. The following table describes them in detail.

| Name | Description                                                                                          |
|------|------------------------------------------------------------------------------------------------------|
| eq   | Evaluates to true if an attribute has a value that is equal to the condition value.                  |
| ne   | Evaluates to true if an attribute has a value that is not equal to the condition value.              |
| 1t   | Evaluates to true if the attribute has a value that is less than the condition value.                |
| le   | Evaluates to true if the attribute has a value that is less than or equal to the condition value.    |
|      | value.                                                                                               |
| gt   | Evaluates to true if the attribute has a value that is greater than the condition value.             |
| ge   | Evaluates to true if the attribute has a value that is greater than or equal to the condition value. |

Table 17. Two operands operation types.

#### *Example:*

The following script returns all contacts with first name "Joe":

```
<select from="crmserver" entity="contact" var="contacts_with_name_Joe">
    <where>
         <condition attr="firstname" op="eq">Joe</condition>
    </where>
    <attr name="contactid" />
</select>
```

## Contains Condition Operator

**Contains** condition operator allow to perform text searches within string properties.Condition evaluates to true if the supplied constant value contains in the property text value.

| Pattern | Description                                                                           |
|---------|---------------------------------------------------------------------------------------|
| %text   | Evaluates to true if the property text value ends with the supplied constant value.   |
| %text%  | Evaluates to true if the supplied constant value contains in the property text value. |
| text%   | Evaluates to true if the property text value starts with the supplied constant value. |

Table 18. The Contains condition operator search patterns for CRM servers.

**NOTE:** %text% is equals to search without "%" symbol.

#### *Example:*

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

Contains condition *for Exchange server* doesn't support the **"%"** symbol - condition evaluates to true if

the supplied constant value is contained in the property text value. Search is *case insensitive*.

*Example:*

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

### Not Operator

**Not operator** inverts a result of a logical operation.

*Example:*

The following script returns identifiers of all CRM contacts that first name is not equal to "Joe" and

DoNotPhone or DoNotEmail properties are not equal to true.

```
<select from="crmserver" entity="contact" var="contacts">
     <where>
        <not>
           \langleand\rangle <condition attr="firstname" op="eq">Joe</condition>
               \langle or \rangle <condition attr="donotphone" op="eq">{true}</condition>
                     <condition attr="donotemail" op="eq">{true}</condition>
                 </or>
            </and>
        </not>
    </where>
    <attr name="contactid"/>
</select>
```

### Starts with

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

## Ends with

**Ends with** condition operator allow to perform text searches within string properties.Condition evaluates to true if the property text value ends with supplied constant value.

*Example:* 

The following script returns all contacts, which first name ends with "J" and mobile phone number

ends with "5544".

```
<select from="crmserver" entity="contact" var="contacts">
     <where>
         <condition attr="firstname" op="ew">J</condition>
         <condition attr="mobilephone" op="ew">5544</condition>
     </where>
     <attr name="contactid"/>
</select>
```

## And / Or Operators

These operators should contain two or more conditions. **And** operator evaluates to true only if all its children conditions evaluate to true. **Or** operator evaluates to true if at least one of its children conditions evaluate to true.

*Example:*

The following script returns all identifiers of CRM contacts which first name is equal to Joe or

DoNotPhone or DoNotEmail properties are equal to true.

```
<select from="crmserver" entity="contact" var="contacts">
     <where>
        <and>
          <condition attr="firstname" op="eq">Joe</condition>
           \langle or \rangle <condition attr="donotphone" op="eq">{true}</condition>
                <condition attr="donotemail" op="eq">{true}</condition>
          \langle/or\rangle </and>
    </where>
    <attr name="contactid"/>
</select>
```

## Exists Condition Operator

**Exists** condition returns true if a specified attribute exists in an entity record. It does not matter whether the property contains a non-empty value or not.

*Example:*

Let us imagine that there is a custom property called ShoeSize that was added to some contacts but others do not have this field.

The following script returns all contacts with this field:

```
<select from="exchange" entity="contact" var="contacts_with_ShoeSize">
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

## In Condition Operator

**In** condition operator returns true if the specified attribute matches to a value in a condition values list.

The following example return all identifiers of CRM contacts, which first name is Bob, Joe, or Michael.

*Example:*

```
<select from="crmserver" entity="contact" var="contacts">
    <where>
<condition attr="firstname" op="in">{['Bob', 'Joe', 'Michael']}</condition>
    </where>
<attr name="contactid" />
</select>
```

### Between Condition Operator

*Between* condition operator returns true if the specified attribute value is between two values in a conditions values list. There are three ways to specify the condition's value.

*Examples:*

All scripts return all identifiers of CRM contacts, which were created in 2011.

```
<set var="dates">{[Utils.Now.AddDays(-100), Utils.Now.AddDays(-50)]}</set>
<select from="crm" entity="contact" var="contacts">
      <where>
```

```
 <condition attr="createdon" op="between">{[dates[0],dates[1]]}</condition>
      </where>
      <attr name="contactid" />
      <attr name="createdon" />
</select>
<set var="dates">{["01/01/2018", "01/01/2019"]}</set>
<select from="crm" entity="contact" var="contacts">
      <where>
          <condition attr="createdon" op="between">{[dates[0],dates[1]]}</condition>
      </where>
      <attr name="contactid" />
      <attr name="createdon" />
</select>
```

## EXCEPTION HANDLING OPERATIONS

## Exception and OnError

**Exception** operation is used when it is necessary to throw an exception in the process of script

execution.

*Example:*

```
<set var="crmservers">crm,crm4,crm5</set>
<for var="crmserver" in="crmservers">
    <select from="{crmserver}" entity="account" var="accounts">
         <where>
             <condition attr="statecode" op="eq">{0}</condition>
         </where>
         <attr name="accountid" />
     </select>
     <if condition="{accounts.Count = 0}">
         <exception>There are no active accounts in {crmserver}. </exception>
    \langle/if>
</for>
```

**OnError** operation is used in conjunction with **Exception** for handling specific exceptions.

*Example:*

```
<sandbox verbose="false">
    <log>{2/0}</log>
    <onerror of="typeof System.DivideByZeroException">
    <log>This is DivideByZero!</log>
    </onerror>
</sandbox>
```

### Sandbox

**Sandbox** operation is used when it is necessary to hide exceptions in process of script execution from user. Sandbox operation has the **"verbose"** attribute that is used for enabling or disabling errors logging (if it is left empty, then (*true*) will be used by default).

#### *Example:*

The following script looks for CRM contact "Joe Dow" and if a single instance of this contact found, then this contact is set as a primary contact of "Adidas" company.

Logging is enabled for the sandbox.

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
          <attr name="primarycontactid">contact:{contacts[0].contactid}</attr>
       </update>
   \langle/if>
</sandbox>
```

# STRUCTURAL OPERATIONS

## Include

**Include** operation is used if you want to separate some code into other script files. Sync360 will combine the main script and code from all includes on the moment of execution. This operation has the **"name"** attribute which value should be a path to existing script in regard to scripts folder plus filename without ".xml" extension.

**NOTE: Sync360 IDE** has special order where to find script. First it tries to find specified path in a folder where a current script is situated. If the script is not found it will check Script folder in current Application folder. If after that the script is also not found it will check the standard Script folder in main Sync360 folder.

*Example:*

```
<include name="Includes\Parameters"/>
<include name="Includes\ReadUserDetails"/>
<include name="Includes\ReadSettings"/>
<context for="exchange" user="{User.Email}">
    <select from="exchange" var="contacts">
       <where>
          <or>
              <condition attr="crmLinkState" op="eq">0</condition>
              <condition attr="crmLinkState" op="eq">2</condition>
          </or>
          <condition attr="crmid" op="ne"></condition>
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

## Call script

**Call** operation is used when it is necessary to execute another script (that already exists) before current script. Rather than include operation, the script will be executed in own context, and it will not have access to variables in main script. The Call operator supports passing parameters into the script. Operator searches for script in **@private** folder. There are two ways to specify call operator. *Examples:*

Call script "MyScriptHelper" and pass parameter "CallSettings"

```
<script>
   <var callParameters='new Object()'/>
```

```
 <var callParameters.Param1='value1'/>
    <MyScriptHelper CallSettings="callParameters"/>
</script>
<script>
   <var callParameters='new Object()'/>
   <var callParameters.Param1='value1'/>
  <call name="MyScriptHelper" CallSettings="callParameters"/>
</script>
```

## Script

**Script** element is used for code blocks grouping.

*Example:*

```
<script>
    //There should be the script body
</script>
```

## Log

**Log** operation can be used when you need to maintain history of script executions and provide some more information to the output. It also eases process of script debugging.

```
<script>
     <log>Result: {2+2}</log>
</script>
<script>
    <select from="crm" entity="systemuser" var="users">
       <attr name="systemuserid"/>
       <attr name="fullname"/>
    </select>
    <for var="user" in="users">
       <log>{user.systemuserid} {user.fullname}</log>
    </for>
</script>
```

# EXTEND FUNCTIONALITY

You can extend script engine functionality from the script, by binding to standard .NET classes or by adding custom assemblies. The assembly should be placed in Global Assembly Cache or in the Sync360 main directory.

```
<script name="StdLib">
<!-- The following line will bind assembly to ScheduleItem operator-->
    <set ScheduleItem="typeof 'Use4si.Infrastructure.Processing.ScheduleItem, Use4si.Infrastructure'"/>
<!— Bind to standard .NET class-->
    <set TimeSpan="typeof System.TimeSpan"/>
<!— Bind static methods of standard .NET class -->
    <set TimeSpans="static TimeSpan"/>
</script>
```
