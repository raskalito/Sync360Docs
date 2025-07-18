# SYNC 360 ENGINE

The following documentation describes the syntax for programming scripts for the Sync360 ecosystem. Sync360 is an ETL tool which is built on .NET Framework version 4.6.2 and is also the name of a special scripting language which is used to configure business logic for this tool. The Sync360 ETL tool consists of two parts: an IDE which allows manual execution of Sync360 scripting files and a Windows service which is responsible for running Sync360 applications. The concept of Sync360 Applications is an essential part of Sync360 ETL. A Sync360 Application is a specific folder that must have three child folders: Config, Instances and Scripts. Each of these folders consists of XML files that are read by the main Sync360 ETL engine to execute the business logic of the application. The Config folder contains XML files with configuration of connections to external systems. The Instances folder contains XML files with scheduler information, which scripts and how often should be executed. The Scripts folder contains main scripting files which describe the business logic of the Sync360 application.  
When the Sync360 engine executes a script file, each command in the script file executes one by one in a single thread. The Sync360 application, as any ETL tool, has a predefined list of connectors for external systems which can be used to perform CRUD operations against their data. The connector for Dynamics CRM is the most advanced and has support for all syntax capabilities; for other connectors support may vary. The access to specific connectors in the context of script execution depends on configuration files used for script execution, meaning there is a configuration file where the name of connection is explicitly set along with type of connection and authentication information.

# GENERAL SCRIPT SYNTAX

A Sync360 script is an XML file which must be a valid XML file and consist of Sync360-specific commands. The root element of each script file must be `<script>`. Each element under the root element represents either flow control or a command for script execution. The child elements of tags can be used as parameters of the command or, if used inside flow control elements, as commands for optional execution. There is no limitation on the levels or number of commands inside a Sync360 script. An example of a Sync360 command with child elements that act as command properties:
```xml
<set var="CitiesAndCountries">
    <attr name="Cities">Houston,Phoenix</attr>
    <attr name="Countries">USA,UK,Canada</attr>
</set>
```
Names of elements, as well as names of attributes, cannot contain space characters. The name should begin with a letter or an underscore character. The rest of the name may contain the same characters as well as digit characters.
An attribute is a markup construct consisting of a name/value pair that exists within a start-tag or empty element-tag followed by an element name. Values of attributes should always be embedded in single or double quotes.
It is necessary to use identical types of quotes for values of attributes in the same tag (please see Cities and Countries in the example above).
Content inside an element acts as a value. Curly braces `{ }` instruct the engine to evaluate the expression; this is a method of accessing variables or performing logical calculations. Curly braces are always used as a singular instance, meaning content inside them cannot include other curly braces. There are commands that do not require curly braces to start evaluation; it occurs by default by the nature of the command.

```xml
<set var="firstname">Joe</set> <!-- assignment of text value 'Joe' to variable 'firstname' without expressions. -->
<set var="calculatedvalue">{294+322}</set> <!-- assignment of result of expression in the element content. Notice that braces are required -->
<set calculated="294+322" /> <!-- assignment of result of expression via property. Notice that braces are not required -->
<if condition="contacts.Count eq 0"> <!-- an expression in the attribute value. In this command condition attribute doesn't require braces, evaluation always occurs.-->
```
An expression may include constants and variables. A variable name is case-sensitive. 
The expression type is selected automatically based on the expression value evaluation, but it can be converted to a necessary type by using special construction 'as'. The following examples give the same result:
```xml
<set var="counter">{2}</set>
<set var="counter">{2 as 'int'}</set>
```

Variables can contain simple values or complex structures. A value of an array item can be received by specifying the array name and index of the necessary element inside square brackets. Indexes start from zero.
```xml
<log>First account: {account[0]}</log>
```

The random element of an array may be selected if the name of an array is specified with empty square brackets.
```xml
<set var="crmservers">{[crm,crm4,crm5]}</set>
<log>Random CRM server: {crmservers[]}</log>
```

A value of a dictionary item can be received by specifying the dictionary name and name of the necessary element in quotes and inside square brackets or by specifying name of the necessary element followed by the dictionary name with a dot.
```xml
<log>City: {account.city}</log>
<log>City: {account["city"]}</log>
```

If the corresponding element is not found, search of a method with this name will be produced via Reflection and, if the method is found, then it will be applied to the dictionary. Otherwise, an exception will be thrown.
'''xml
<log>Contact is found: {contacts.ContainsKey(contactid)}</log>
'''

# VARIABLE TYPES

Variables in Sync360 use short names for known .NET types and structures, except Object type which is a custom class based on System.Dynamic.DynamicObject. Below are the list of short names and their respective .NET structures in braces. 
```
char (System.Char)  
string (System.String)  
float (System.Single)  
int (System.Int32)  
long (System.Int64)  
double (System.Double)
decimal (System.Decimal)
bool (System.Boolean)
DateTime (System.DateTime)
Guid (System.Guid)
StringBuilder (System.Text.StringBuilder)
Regex (System.Text.RegularExpressions.Regex)
List (System.Collections.Generic.List`1[System.Object])
Dictionary (System.Collections.Generic.Dictionary`2[System.String,System.Object])  
Object (Use4si.Core.DynamicDictionary)
```
Using any of these types it is possible to create an array. Sync360 automatically will use the type of elements inside array to define the type of array, however if there will be different types of elements, the array will be created as System.Object[]
These are types which possible to declare by default. It is also possible to get different types of variables in Sync360 based on specific functions from .NET libraries.

# **CONSTANTS**
There are three predefined constants in Sync360:
"true","false","null"


# **OPERATORS**
In the Sync360 scripting language, an operator is a construct that can be used in expressions. The Sync360 engine doesn't provide operand coercion; operators can be used only across supported types. There are some significant differences in the semantics used for operators compared to other programming languages. For example, operators `=` and `==` in Sync360 syntax mean the same thing. The list of all available operators and their explanations is below:

### Arithmetic operators:
The addition operator is `+`. This operator adds the second operand to the first operand. Supported types: int, long, double, string. The operator is ad hoc polymorphic; for numbers it performs arithmetic operations and for strings performs concatenation.

The subtraction operator is `-`. This operator subtracts the second operand from the first operand. Supported types: int, long, double.

The multiplication operator is `*`. This operator multiplies the first operand by the second operand. Supported types: int, long, double.

The division operator is `/`. This operator divides the first operand by the second operand. Supported types: int, long, double.

### Relational operators:
The equals operator is `=` or `==` or `eq`. This operator returns true if the first operand is equal to the second operand. Supported types: int, long, double, string, bool, date and object.

The not equal operator is `!=` or `<>` or `ne`. This operator returns true if the first operand is not equal to the second operand. Supported types: int, long, double, string, bool, date and object.

The greater than operator is `>` or `gt`. This operator returns true if the first operand is greater than the second operand. Supported types: int, long, double, string and date.

The less than operator is `<` or `lt`. This operator returns true if the first operand is less than the second operand. Supported types: int, long, double, string and date.

The greater than or equal operator is `>=` or `ge`. This operator returns true if the first operand is greater than or equal to the second operand. Supported types: int, long, double, string and date.

The less than or equal operator is `<=` or `le`. This operator returns true if the first operand is less than or equal to the second operand. Supported types: int, long, double, string and date.

### Logical operators:
The and operator is `&&` or `and`. This operator performs a logical-AND of its bool operands. Supported types: bool.

The or operator is `||` or `or`. This operator performs a logical-OR of its bool operands. Supported types: bool.

The not operator is `!` or `not`. This operator is a unary operator that negates its operand. It returns true only if its operand is false. Supported types: bool.

### Program structure operators:
The index, record or object access operator is `[]` or `.`. This operator returns an array element at the specified index, a property of an object or a dictionary element with the specified string key.

```xml
<log>{myDict['property1']}</log>
```
or
```xml
<log>{myDict.property1}</log>
```

The expression validation operator is `.isSet`. This operator can be applied to any expression or variable to validate that it can be evaluated. When an expression can be evaluated and returns a value, the operator returns true; otherwise it returns false. This operator prevents exceptions when applied to expressions.

```xml
<set var="myVar">{232}</set>
<log>{myVar.isSet}</log>
```
or
```xml
<if condition="(250+300+myVar).isSet">
    <log>myVar is a number</log>
</if>
```

The collections total operator is `.Count`. This operator can be applied to any collection variable to get the total number of items in the collection.

```xml
<set var="myArray">{1,2,3}</set>
<log>{myArray.Count}</log>
```

### Conditional operators:
The null coalescing operator is `??`. This operator returns the first calculated value in a chain of expressions. 

**Example:** If the 'MyVar' property of the 'Global' variable is not null, it displays the property value; otherwise it displays the text 'MyVar was not set'.
```xml
<log>{Globals['MyVar'] ?? 'MyVar was not set'}</log>
```

The ternary conditional operator is `?` with usage of `expression ? value if true : value if false`. This operator evaluates an expression, and if it is true, returns the first value; otherwise it returns the second value.

# DECLARATION OF THE VARIABLES

`set` operation is used for declaration and modification of the variables. The name of variable can be supplied via `var` attribute of `set` element, then a value is passed via content of set tag. However, you can skip `var` attribute and define variable name explicitly as a property of set tag, in this case the value passed into the property will be used a variable value. Set element could be also used without any attributes, in this case it used for execution/evaluation of expression passed into content of element, without any assignment.

## Simple variable

To declare a simple variable, define a name using `var` property and provide value via element content. When value passed without curly braces it evaluated as string. Using curly braces and providing digits Sync360 engine attempts to resolve it to best matching simple types. The default behavior that numeric values are resolved as Int32 or Int64 depending on size. Any fraction numbers are resolved as Double. Because of that is there a need to use specific type it is required to use `as` operator to define a type.
```xml
<set var="text">Hello</set>

<set var="num1">{1}</set>
<log>{num1.GetType()}</log> <!-- Outputs 'System.Int32' -->

<set var="num2">{3249570234750343425}</set>
<log>{num2.GetType()}</log> <!-- Outputs 'System.In64' -->

<set var="num3">{1.4}</set>
<log>{num3.GetType()}</log> <!-- Outputs 'System.Double' -->

<set var="num4">{1.4 as 'decimal'}</set>
<log>{num4.GetType()}</log> <!-- Outputs 'System.Decimal' -->
```
It is possible to assign to variable a value of another variable
```xml
<set var="accountid">{value}</set>

<!-- The other way to do same operation -->
<set accountid="value" />
```

## Structures
Structures are essential part of Sync360 scripting as they provide methods for manipulating and working with data. When outputing structure using `log` command the Sync360 engine automatically serialize them to string representation.

### Dictionary

To declare the dictionary variable use construction `<set var="myDict">{new Dictionary()}</set>` after that you can assign values by specifing string keys using `[]` index operator. 
```xml
<set Address="new Dictionary()" />
<set var="Address['Street']">Brookview Dr NW</set>
<set var="Address['State']">GA</set>
<set var="Address['Zip']">30318</set>
<set var="Address['City']">Atlanta</set>
<set var="Address['Country']">US</set>
```

Dictionaries are key structure for writing scripts in Sync360 as it is often used for caching data retrieved from external systems for it easier access via identifiers. Methods that exists in Dictionary like ContainsKey allows easier verification for creating the script logic flow.

### List

List declaration is very similar to Dictionary, use construction `<set var="myList">{new List()}</set>` and then assign values to list using `[]`.
```xml
<set Names="new List()" />
<set var="Names[]">Michael</set>
<set var="Names[]">Joe</set>
```
List structure is particularly useful when there is no need to access individual specific elements or when there is a need to access a random elements.  List also can be used for sorting string or number values due to it default methods.
```xml
<log>{Names[]}</log> <!-- output random element of List -->

<set var="SortedList">{new List()}</set>
<set var="SortedList[]">{29}</set>
<set var="SortedList[]">{5}</set>
<set var="SortedList[]">{32}</set>
<log>{SortedList}</log>  <!-- Outputs [29, 5, 32] -->
<set>{SortedList.Sort()}</set>
<log>{SortedList}</log>  <!-- Outputs [5, 29, 32] -->

```

### Array

To declare an array variable put the values in square brackets separated by comma.
```xml
<set var="crmserver">{["crm1","crm2"]}</set> <!-- 'crmserver' variable type will be System.String[] -->
<set cities="['New York','Los Angeles','Chicago']"/>  <!-- 'cities' variable type will be System.String[] -->
<set randomnumbers="[3,6,7,12]"/> <!-- 'randomnumbers' variable type will be System.In32[] -->

<set var="myDict">{new Dictionary()}</set>
<set var="myList">{new List()}</set>
<set var="myArray">{[myDict,myList]}</set> <!-- 'myArray' variable will be System.Object[] -->
```
Arrays compared to List or Dictionary structures cannot be extended to include additional elements after initialization, the number of elements is strict by definition and it is required to initialize a new array if there is a need to add additional element.

### Object

In Sync360 an object can be created via different syntax constuctions. Since each object is  a dynamic object, it's structure after definition is not strict, you can add additional properties to an object at anytime.

In the following example the same object defined using 4 different syntax schemes:
```xml
<!--Intialize empty Object and then add properties and values -->
<set var="Person">{new Object()}</set>
<set var="Person.Name">Michael</set>
<set var="Person.Lastname">White</set>

<!--Short version of syntax ot initialize empty object and then assign properties -->
<set Person="new Object()" Person.Name="'Michael'" Person.Lastname="'White'"/>

<!--Initiale object with properties and values -->
<set var="Person">
      <attr name="Name">Michael</attr>
      <attr name="Lastname">White</attr>
</set>

<!--Short version of initializing object with properties and their values -->
<set Person="['Name':'Michael','Lastname':'White']"/>
```

The object structure in Sync360 can have as many nested properties as needed, a simple example of a object which describes a house
```xml
<set var="House">
  <attr name="Material">brick</attr>
  <attr name="Price">{300000}</attr>
    <attr name="Rooms">
    <attr name="DiningRoom">{30}</attr>
    <attr name="Bathroom">{8}</attr>
    <attr name="Livingroom">{40}</attr>
    <attr name="Bedroom">{45}</attr>
    <attr name="Kitchen">{15}</attr>
    <attr name="Basement">{20}</attr>
  </attr>
</set>
<set var="House.Color">white</set>

<log>{House}</log>
<!--When outputing objects in Sync360 it searializes them to a string. In this example the result of log command will be:  
['Material': 'brick', 'Price': 300000, 'Rooms': ['DiningRoom': 30, 'Bathroom': 8, 'Livingroom': 40, 'Bedroom': 45, 'Kitchen': 15, 'Basement': 20], 'Color': 'white'] -->
```

# FLOW CONTROL OPERATIONS

## IF,THEN-ELSE, UNLESS

`if` element is used to wrap code block and instruct Sync360 engine to execute this code only when condition evaluates as true. `condition` is property of if element which always evaluates into boolean value, it is not required to use curly braces for this propery.
```xml
<set var="a">{10}</set>
<set var="b">{15}</set>
<if condition="a gt b">
     <log>a is greater then b</log>
</if>
<if condition="a lt b">
     <log>a is less then b</log>
</if>
<if condition="a eq b">
     <log>a is equal to b</log>
</if>
<!--  the result of the above script execution will be single output 'a is less then b' -->
```

`if` elements can be nested as many times as needed to create the logic flow. However too many nested elements can make the code unreadable and therefore there are other methods to control the execution flow. 
`if` can act as standard property that can be applied to any other element in sync360 syntax. When `if` applied to an element the value of it evaluate before execution of the command and only if it true the command will be executed. Another property that can be added to any element is `unless` it works similar to `if` property just makes an opposite evaluation - command executed only if expression doesn't evaluate as true.
```xml
<set var="a">{5}</set>
<set var="b">{5}</set>
<set var="c">{10}</set>
<if condition="a eq b">
  <log>a is same as b</log>
  <log if="b eq c">b is also same as c</log>
  <log unless="b eq c">but b is not same as c</log>
  <if condition="a ne c">
    <log>a is not the same a c</log>
  </if>
</if>
<!-- the result of the above script will be output of the following lines  
a is same as b
but b is not same as c
a is not the same a c
-->
```

When `if` element is used, it is possible to control the execution of flow using nested `then` and `else` elements. That allows to execute a set of different commands when experssion evaluates as true and as false. `then` element can contain inline `if` property and that allows multiple code flows to execute within same parent `if` element. However `else` element cannot have `if` inline and therefore only one nested `else` element can exists per `if`. `then` and `else` child elements are always used together, it is not valid to include only `then` or only `else` inside an `if` element.
```xml
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
</if>
```

## For, While, Break, Continue

`for` element is used when a code block needs to be executed a certain amount of times. Sync360 supports iteration through enumerated structures using `for` statement as well as standard iteration by number of times, this depend on attributes supplied in the `for` element.
```xml
<set crmservers="['crm','crm4','crm5']"/>
<for var="i" from="0" to="crmservers.Count - 1" step="1">
    <log>{crmservers[i]}</log> <!--logs iteratively a value from array by it's index -->
</for>

<set crmservers="['crm','crm4','crm5']"/>
<for var="crmserver" in="crmservers">
    <log>{crmserver}</log>  <!--logs iteratively each individual value from crmservers array -->
</for>
```

`while` element is another way to execute a code block in a loop while expression specified in the `condition` attribute evaluates as `true`.
```xml
<set var="crmservers">{['crm','crm4','crm5']}</set>
<set var="counter">{0}</set>
<while condition="counter lt crmservers.Count">
     <log>{crmservers[counter]}</log>
     <set var="counter">{counter + 1}</set>
</while>
```


`break` element can be used to exit the loop. It is intended to be used in conjunction with `if` element to make a conditional end of cycle. Once break command executed, the Sync360 engine moves to the next command after end of cycle element. It can be used in both `for` and `while` cycles.
```xml
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
    </if>
</for>
<log>End cycle.</log>
```

`continue` element can be used to skip current cycle logic that is located under this element and move on to the next cycle iteration. Also intended to use in conjunction with `if` element. It can be use in both `for` and `while` cycles
```xml
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
     </if>
     <log>{t}</log>
</for>
<log>End cycle.</log>
```

# **FUNCTIONS**

In Sync360 there are static instances of classes with predefined list of methods that aim to help a developer to implement the scripting logic. These classes are written using C# and .NET framework and implement the logic which is not available using standard Sync360 commands. Invoking class methods can be considered as standalone functions.

### Class CSV
The class provides methods to work with CSV files. It has two methods Read and Write. There are default values for parameters supplied to methods of this class:  
```xml
DefaultEncoding = Encoding.UTF8
DefaultDelimiter = ","
DefaultQuote = "'"
DefaultComment = "#"
DefaultTrim = true
DefaultHasHeader = true
DefaultTopLinesToSkip = 0
DefaultAppend = true
DefaultWriteHeader = true
```

`Csv.Read` used to read a csv file into a variable of List type where each element of this list will be a line from csv file as a separate structured record.

The method has overloads and therefore number of parameters can vary, below each available overload
1) `<set var="records">{Csv.Read(string path, Encoding encoding, string delimiter, string quote, string comment, bool trim, bool hasHeader = DefaultHasHeader, int topLinesToSkip = DefaultTopLinesToSkip)}</set>` with this overload 2 last parameters are optional and default value will be used when not specified.
2) `<set var="records">{Csv.Read(string path, Encoding encoding, string delimiter, string quote = DefaultQuote, bool hasHeader = DefaultHasHeader)}</set>` with this overload 
3) `<set var="records">{Csv.Read(string path, Encoding encoding, dynamic configuration)}</set>`  with this overload into last parameter it is required to pass object with properties named `Delimiter`, `Quote`, `Comment`, `Trim`, `HasHeader` and `TopLinesToSkip`. Each property is optional, if missing the default values will be used.
4) `<set var="records">{Csv.Read(string path)}</set>` this overload uses default values.
<b>Examples:</b>
```xml
<set var="records">{Csv.Read('c:\temp\example.csv',Encoding.GetEncoding(1252),',','"','!",true,15)}</set>

<set var="records">{Csv.Read('c:\temp\example.csv',Encoding.GetEncoding('utf-8'),';')}</set>

<set var="configuration">
    <attr name="Delimiter">,</attr>
    <attr name="Quote">'</attr>
    <attr name="Comment">#</attr>
    <attr name="Trim">{true}</attr>
    <attr name="HasHeader">{true}</attr>
    <attr name="TopLinesToSkip">{15}</attr>
</set>
<set var="records">{Csv.Read('c:\temp\example.csv',Encoding.GetEncoding('utf-8'),configuration)}</set>
```

`Csv.Write` this method transforms data loaded into Sync360 engine and writes it into a CSV file. The method has two overloads and 
1) `<set var="result">{Csv.Write(List<object> data, string path, Encoding encoding, string delimiter, string quote = DefaultQuote, string comment = DefaultComment, bool trim = DefaultTrim, bool append = DefaultAppend, bool writeHeader = DefaultWriteHeader)}</set>` The data provided should be a list of Dictionaries where each dictionary instance represents a row, and keys are used as columns and values are used as data which will be written into csv file. Therefore each dictionary instance must have the same keys. The other parameters are the same as for `Csv.Read` method.  
2) `<set>{Csv.Write(List<object> data, string path)}</set>` in this overload default parameters will be used and example of invokation without returning boolean.
<b>Examples:</b>
```xml
<set var="csvData">{new List()}</set>
<set var="row">{new Dictionary"></set>
<set var="row['column1']">text1</set>
<set var="row['column2']">text2</set>
<set var="csvData[]">{row}</set>
<set var="row">{new Dictionary"></set>
<set var="row['column1']">text3</set>
<set var="row['column2']">text4</set>
<set var="csvData[]">{row}</set>
<set>{Csv.Write(csvData, 'c:\temp\sampleData.csv'</set>
<!--Example above generating a simple csv file with two columns named 'column1' and 'column2' and two rows with different values -->

<select from="crm" entity="contact" var="records">
    <attr name="firstname" />
    <attr name="lastname" />
    <attr name="jobtitle" />
    <attr name="emailaddress1" />
    <attr name="parentcustomerid" />
</select>
<set var="csv">{new List()}</set>
<for var="record" in="records">
    <set var="values">{new Dictionary()}</set>
    <for var="key" in="record.Keys">
        <if condition="key.ToString() eq 'parentcustomerid'">
            <then>
                <set var="values[key]">{record[key].Name ?? ''}</set>
            </then>
            <else>
                <set var="values[key]">{record[key]}</set>
            </else>
         </if>
    </for>
</for>
<set>{Csv.Write(csv, 'c:\temp\crmContacts.csv'</set>
<!--Example above dynamically forms required data structure from records retrieved from Dynamics CRM system using select operator. Notice that several flow control operators are used in this example to iterate through data and to output correct properties based on field type (parentcustomerid is a lookup field to another table and therefore to get it's text value .Name property used -->
```

### Class FileUtils
The class provides methods to work with windows file system. It has various methods for manipulating with files, the methods will be described below.  
`FileUtils.ReadFile` opens a file, reads the contents of the file into a byte array, and then closes the file. It can be useful when the file must be transfered from a file system into another system.  
```xml
<set var="Bytes">{FileUtils.ReadFile("c:\temp\data.bin")}</set>
```  
`FileUtils.ReadFileAsBase64` opens a file, reads the contents of the file into a byte array, converts the array into a Base64 string, and then closes the file. This method especially useful for uploading attachments into Dataverse, as the file contents are stored in base64 format inside documentbody property.  
```xml
<set var="Bytes">{FileUtils.ReadFileAsBase64("c:\temp\data.bin")}</set>
```

`FileUtils.ReadIdsFromFile` method is used to read predefined GUIDS from text file. GUIDS should be placed line by line. The result will be an array of System.Guid.  
```xml
<set var="pathtofile">c:\temp\UserGuids.txt</set>
<set var="Ids">{FileUtils.ReadIdsFromFile(pathtofile)}</set>

<!-- Example of UserGuids.txt
73bd3d32-2f53-4d6d-b710-87c2e46b3251
729ec155-ece8-4a43-af62-9447ab74797e
3ca05271-b210-40da-9146-70bc1ea37b29
8702b2d5-525c-4e4e-94d7-1d12668a48be
0edcc747-fe03-453b-b956-3924d60b1e5e
-->
```  

`FileUtils.DirectoryEntries` returns the list of all files and subdirectories in a specified path. The result of method execution will be array of strings. It is an essential method for building ETL process when exchange is performed via files without a specific filename structure as it allows to detect required files in a path for processing.
Let's say we only need to process csv files in a specified path, the following code can be used to get required file names and after that utilize the `Csv.Read` method to get file contents.
```xml
<set var="path">c:\temp\</set>
<set var="filenames">{FileUtils.DirectoryEntries(path)}</set>
<for var="filename" in="filenames">
  <if condition="filename.EndsWith('.csv')">
     <set var="records">{Csv.Read(path + filename)}</set>
     <log>{records.Count}</log> <!-- for example just output the count of rows. In real ETL scenario there will be logic to process records from the file. -->
  </if>
</for>
```

`FileUtils.GetFiles` returns the files that match the specified search pattern in the specified path. The search string to match against the names of files in path. This parameter can contain a combination of valid literal path and wildcard (`*` and `?`) characters, but it doesn't support regular expressions. Compared to `DirectoryEntries` method `GetFiles` returns an array of `System.IO.FileInfo` structure. 
```xml
<set var="path">c:\temp\</set>
<set var="files">{FileUtils.GetFiles(path, "*.csv")}</set>
<for var="file" in="files">
   <log>{file.Name} - {file.Length}</log>
</for>
```

`FileUtils.WriteToFile` used The function writes specified string into a file. It is usually used for logging or for dumping the intermediate result of script execution. Method accept three parameters  `<set>{WriteToFile(string path, string data, bool Append)}</set>`
```<set>{FileUtils.WriteToFile("c:\temp\log.txt","Test", true)}</set> ```

'FileUtils.MoveFile' moves a specified file to a new location, providing the option to specify a new file name.
```xml
<set>{FileUtils.MoveFile("c:\temp\loq.txt", "c:\temp\log_moved.txt") }</set>

<set var="sourcePath">c:\temp\</set>
<set var="targetPath">c:\temp\processed\</set>
<set var="filename">log_moved.txt</set>
<set>{FileUtils.MoveFile(sourcePath + filename, targetPath + filename) }</set>
```

`FileUtils.Zip` method creates a zip archive with files from specified path
```xml
<set>{FileUtils.Zip("C:\temp\", "C:\temp\archive.zip")}</set> <!-- archives all files in temp folder into archive.zip file -->
```

`FileUtils.Unzip` method extracts files from a specified zip-archive into a selected folder.
```xml
<set>{FileUtils.Zip("C:\temp\archive.zip", "C:\temp\extracted\")}</set> <!-- extract files from archive.zip into extracted folder -->
```

### Class Html
This class provides a single method to extract text from html string or htmldocument object.

`Html.ToPlainText` method converts html syntax into plain text by removing any html tags. It has two overloads, it can accept string which contains html syntax or an HtmlDocument structure.
```xml
<set var="htmlString"><![CDATA[
<html>
 <body>
    <div>Some text</div>
 </body>
</html>
]]></set>

<set var="text">{Html.ToPlainText(htmlString)}</set>
<log>{text}</log> <!-- outputs 'Some text' -->
```

### Class Http
The class used to perform http requests. It particularly useful for integration with REST based endpoints, however it is not limited to this. The class is using System.Net.WebClient for permorming http requests with extension to support cookies, unless other specified.

`Http.SetUseDefaultCredentials` sets the use of default credentials for each subsequent http request.  
```xml
<set>{Http.SetUseDefaultCredentials(true)}</set>
```

`Http.SetClientEncoding` sets a client encoding for connections. Encoding should be provided as string.  
```xml
<set>{Http.SetClientEncoding('utf-8')</set>
```

`Http.Get` method performs HTTP GET web request for specified URL. The method has overloads and therefore number of parameters can vary, below each available overload  
1) `<set var="response">{Http.Get(string url)}</set>` can be used for public websites that doesn't require authorization or specific headers. The response is retrieved as a string.
2) `<set var="response">{Http.Get(string url, string login, string password)}</set>` using basic web authorization by username and password. The  information will be added automatically as "Authorization" header with type "Basic".
3) `<set var="response">{Http.Get(string url, string login, string password, string domain)}</set>` can be used for websites requiring windows authentification using NetworkCredentials.
4) `<set var="response">{Http.Get(string url, object headers)}</set>` to pass custom headers. The most commonly used overload for permorming requests as allows full control over what will be passed to request. Headers can be defined as an object or a dictionary, where property name is header name and value is header value.
 ```xml
<set var="response">{Http.Get('http://www.example.com')}</set>

<set var="response">{Http.Get('http://www.bankaccount.com', 'user', 'password')}</set>

<set var="response">{Http.Get('http://internal.xyz', 'user', 'password', 'domain')}</set>

<set headers="new Dictionary()"/>
<set var="headers['User-Agent']">Opera/9.80 (Windows NT 5.1; U; ru) Presto/2.9.168 Version/11.51</set>
<set var="headers['Accept']">*/*</set>
<set var="headers['Content-Type']">application/x-www-form-urlencoded</set>
<set var="url">https://api.system.com/get</set>
<set var="response">{Http.Get(url, headers)}</set>
 ```

`Http.Put` method performs HTTP PUT request to specific URL using provided data and headers.  
```xml
<set headers="new Dictionary()"/>
<set var="headers['Accept']">*/*</set>
<set var="headers['Content-Type']">application/x-www-form-urlencoded</set>
<set var="data">{new Dictionary()}</set>
<set var="data['param1']">Text1</set>
<set var="data['param2']">Text2</set>
<set var="url">https://www.example.com/REST/1/Projects/</set>
<set var="response">{Http.Put(url, data, headers)}</set>

<set var="headers['Content-Type']">application/json</set>
<set var="json">{Json.ToAsciiJson(data)}</set>
<set var="response">{Http.Put(url, json, headers)}</set>
```

`Http.Post` method performs HTTP POST request to specific URL. The method has overloads and therefore number of parameters can vary, below each available overload  
1) `<set var="response">{Http.Post(string url, string data)}</set>` can be used for public websites that doesn't require authorization or specific headers. The result of request will be written in response.
2) `<set var="response">{Http.Post(string url, string login, string password, string data)}</set>` using basic web authorization by username and password. The  information will be added automatically as "Authorization" header with type "Basic".
3) `<set var="response">{Http.Post(string url, string login, string password, string data, object headers)}</set>` also using basic web authorization but allows to include custom headers.
4) `<set var="response">{Http.Post(string url, string login, string password, string domain, string data, object headers)}</set>` can be used for websites requiring windows authentification using NetworkCredentials.
5) `<set var="response">{Http.Post(string url, string data, object headers)}</set>` used when web service requiree a custom authentification scheme via headers.

```xml
<set var="json">{Json.ToAsciiJson(listOfEmployees)}</set>

<set var="response">{Http.Post('https://www.example.com', json, headers)}</set>

<set var="response">{Http.Put("https://flintco.openasset.com/REST/1/Projects/" + project.Value + "/Employees", json, headers)}</set>
```

`Http.Patch` method performs HTTP PATCH request to specific URL. The method has overloads and therefore number of parameters can vary, below each available overload  
1) `<set var="response">{Http.Patch(string url, string data)}</set>` can be used for public websites that doesn't require authorization or specific headers. The result of request will be written in response.
2) `<set var="response">{Http.Patch(string url, string login, string password, string data)}</set>` using basic web authorization by username and password. The  information will be added automatically as "Authorization" header with type "Basic".
3) `<set var="response">{Http.Patch(string url, string login, string password, string data, object headers)}</set>` also using basic web authorization but allows to include custom headers.
4) `<set var="response">{Http.Patch(string url, string login, string password, string domain, string data, object headers)}</set>` can be used for websites requiring windows authentification using NetworkCredentials.
5) `<set var="response">{Http.Patch(string url, string data, object headers)}</set>` used when web service requiree a custom authentification scheme via headers.

```xml
<set var="response">{Http.Patch('http://www.example.com', 'some data', 'headers go here')}</set>
```
`Http.Download` downloads the file that URL contains, e. g. page, archive, document.
```xml
<set var="instructionPdf">{Http.Download('http://www.example.com/docs/instruction.pdf')}</set>
```

`Http.GetRaw` this method performs Get request using TcpClient.TcpClient. So the response will be provided in unchanged, literal form.There are two overloads for this method  
1)`<set var="rawresponse">{Http.GetRaw(string url}</set>` simply perform HTTP GET request  
2)`<set var="rawresponse">{Http.GetRaw(string url, int sendTimeout, int receiveTimeout}</set>` The same as above but allows to specify timeouts  
```xml
<!-- Get raw HTTP response including headers -->
<set var="rawResponse">{Http.GetRaw('http://example.com/api/status')}</set>
<log>{rawResponse}</log> <!-- outputs full HTTP response including headers like:
HTTP/1.1 200 OK
Date: Mon, 23 May 2022 22:38:34 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 23

{"status":"operational"}
-->

<!-- With custom timeouts (in milliseconds) -->
<set var="rawResponse">{Http.GetRaw('http://slowapi.example.com/data', 5000, 10000)}</set>
```

`Http.SetCookie` set cookie for the specific URL.
```xml
<set var="cookie">id=a3fWa;Expires=Thu, 31 Oct 2021 07:28:00 GMT;</set>
<set>{Http.SetCookie("http://example.com", cookie)}</set>
```

`Http.GetCookie` retrieves cookie from the specified URL.
```xml
<set var="cookie">{Http.GetCookie("http://example.com")}</set>
```

### Class Json
The class provides methods to work with JSON format.  
`Json.ToJson` method performs serialization of object to a string in JSON format. Accepts objects as parameter and indents JSON string by default. Indent can be disabled by passing false to second parameter.
```xml
<set var="Person">
  <attr name="firstname">John</attr>
  <attr name="lastname">Doe</attr>
  <attr name="children">{["Mary", "Kate"]}</attr>
</set>
<set var="jsonString">{Json.ToJson(Person)}</set>
<log>{jsonString}</log> <!-- outputs
{
  "firstname": "John",
  "lastname": "Doe",
  "children": [
    "Mary",
    "Kate"
  ]
}
-->

<set var="jsonString">{Json.ToJson(Person,false)}</set>
<log>{jsonString}</log> <!-- outputs
{"firstname":"John","lastname":"Doe","children":["Mary","Kate"]} -->
 ```

`Json.ToAsciiJson` method performs serialization of object to a string in ASCII JSON format and removes non-ASCII symbols. It works and used the same way as ToJson method.  

`Json.FromJson` method deserializes JSON string into DynamicDictionary object.
```xml
<set var="lbr">{'{'}</set>
<set var="rbr">}</set>
<set var="stringJson"><![CDATA[
{lbr}
  "firstname": "John",
  "lastname": "Doe",
  "children": [
    "Mary",
    "Kate"
  ]
{rbr}
]]>
</set>

<set var="jsonObject">{Json.FromJson(stringJson)}</set>
<log>{jsonObject}</log> <!-- outputs
 ['firstname': 'John', 'lastname': 'Doe', 'children': [[], []]]
-->
<log>{jsonObject.children.Count}</log> <!-- outputs 2 -->
```

`Json.GetDictionary` method deserializes JSON string into System.Dynamic.ExpandoObject object.
```xml
<set var="lbr">{'{'}</set>
<set var="rbr">}</set>
<set var="stringJson"><![CDATA[
{lbr}
  "firstname": "John",
  "lastname": "Doe",
  "children": [
    "Mary",
    "Kate"
  ]
{rbr}
]]>
</set>
<set var="jsonObject">{Json.GetDictionary(stringJson)}</set>
<log>{jsonObject}</log> <!-- outputs
['firstname': 'John', 'lastname': 'Doe', 'children': ['Mary', 'Kate']]
-->
```  
`Json.GetArrayFromJson` method deserializes JSON string which starts as array of elements into Generic List of System.Dynamic.ExpandoObject
```xml
<set var="lbr">{'{'}</set>
<set var="rbr">}</set>
<set var="stringJson"><![CDATA[
[
{lbr}
  "firstname": "John",
  "lastname": "Doe",
  "children": [
    "Mary",
    "Kate"
  ]
{rbr},
{lbr}
  "firstname": "Paul",
  "lastname": "Doe",
  "children": [
    "Bob"
  ]
{rbr}
]
]]>
</set>

<set var="jsonObject">{Json.GetArrayFromJson(stringJson)}</set>
<log>{jsonObject}</log> <!-- outputs
 [['firstname': 'John', 'lastname': 'Doe', 'children': ['Mary', 'Kate']], ['firstname': 'Paul', 'lastname': 'Doe', 'children': ['Bob']]]
-->
```

### Class Utils
The generic class with multiple various methods that assist with sync360 scripting.  
`Utils.NewGuid` method returns a new random GUID.  
```xml
<set var="newGuid">{Utils.NewGuid}</set>
```
`Utils.Split` method identifies the substrings in a string value that are delimited by one or more characters provided, then places the substrings into a specified string array.  
```xml
<set var="Names">Roman; Joe; Michael</set>
<set var="NamesArray">{Utils.Split(Names,';')}</set>
<log>{NamesArray[0]}</log> <!-- outputs Roman -->
 ```  
`Utils.Join` method combines array values into string with specified delimiter. It is opposite of Split method.  
```xml
<set var="NamesArray">{['Roman','Joe','Michael']}</set>
<set var="NamesStr">{Utils.Join(NamesArray,';')}</set>
<log>{NamesStr}</log> <!-- outputs Roman;Joe;Michael -->
```  
`Utils.ToUpper` method returns a copy of provided string converted to uppercase.  
```xml
<set var="text">small case</set>
<log>{Utils.ToUpper(text)}</log> <!--outputs SMALL CASE -->
```
`Utils.toLower` method returns a copy of provided string converted to lowercase.  
```xml
<set var="text">ALL CAPS</set>
<log>{Utils.ToLower(text)}</log> <!--outputs all caps -->
```
`Utils.NewLine` method returns newline string ("\n").  
```xml
<log>line1 {Utils.NewLine}line2 </log> <!--outputs
line1 
line2 
-->
```
`Utils.Now` method returns a datetime structure that is set to the current date and time based in the local timezone, which is based on the current environment configuration.  
```xml
<set var="currentDate">{Utils.Now}</set>
<log>{currentDate.Kind}</log> <!-- outputs local -->
```  
`Utils.Replace` method returns a new string in which all occurrences of a specified Unicode String in the current string are replaced with another specified Unicode character or String.  
```xml
<set var="str1">This is an example</set>
<set var="str2">{Utils.Replace(str1,'This','Here')}</set>
```  
`Utils.IsKeyExist` method used to check whether the key/property exists in a dictionary/object, returns boolean.  
```xml
<set var="customobject">
	<attr name="property1">1</attr>
	<attr name="property2">2</attr>
</set>
<log>{Utils.IsKeyExist(customobject, "property1")}</log> <!-- outputs true -->

<set var="myDict">{new Dictionary()}</set>
<set var="myDict['property1']">1</set>
<log>{Utils.IsKeyExist(myDict, "property1")}</log> <!-- equivalent to myDict.ContainsKey("property1"), outputs true -->
```  
`Utils.ParseGuid` method converts the string representation of a GUID to the equivalent Guid structure.
```xml
<set var="newGuidVar">{Utils.ParseGuid('5dcf85ae-ca84-4718-afb8-1795db389763')}</set> <!-- equivalent to new Guid('5dcf85ae-ca84-4718-afb8-1795db389763') -->
```
 ```  
`Utils.TextuallyEquals` method used to compare two variables in their textual representation: If variables are of the same type, it compares them as they are. If variables are of different types, it converts them into strings and then compares the strings. Third parameter can be used to ignore case.
```xml
<set var="var1">1</set>
<set var="var2">{1}</set>
<log>{Utils.TextuallyEquals(var1,var2)}</log> <!-- outputs true -->

<set var="var1">
  <attr name="prop1">1</attr>
  <attr name="prop2">{2}</attr>
</set>

<set var="var2">
  <attr name="prop1">1</attr>
  <attr name="prop2">{2}</attr>
</set>

<log>{Utils.TextuallyEquals(var1,var2,true)}</log> <!-- outputs false ??? why -->
```  
`Utils.Right` method accepts string and a number of characters to cut from the right part of the string. Returns a substring.
```xml
<set var="text">AmazingSync360</set>
<log>{Utils.Right(text,7)}</log> <!--outputs Sync360 -->
```
`Utils.Left` method accepts string and a number of characters to cut from the left part of the string. Returns a substring.  
```xml
<set var="text">Sync360 Rules</set>
<log>{Utils.Left(text,7)}</log> <!--outputs Sync360 -->
```
`Utils.Encode` method encodes a provided string using System.Net.WebUtility.HtmlEncode class. Returns string.
```xml
<set var="text"><![CDATA[Hello, <World>! How are you & your friends?]]></set>
<log>{Utils.Encode(text)}</log> <!--outputs Hello, &lt;World&gt;! How are you &amp; your friends? -->
```
`Utils.Decode` method decodes a provided string using System.Net.WebUtility.HtmlDecode class. Returns string.  
```xml
<set var="text"><![CDATA[Hello, &lt;World&gt;! How are you &amp; your friends?]]></set>
<log>{Utils.Decode(text)}</log> <!--outputs Hello, <World>! How are you & your friends? -->
```

`Utils.DateToString`  method converts provided datetime structure into a string representation of specified format.
```xml
<set var="date">{new DateTime(2020,1,1)}</set>
<log>{Utils.DateToString(date,'yy/MM/dd')}</log> <!--outputs 20/01/01 -->
```
`Utils.StringToDate`  method converts provided string into datetime structure based on provided format.  
```xml
<set var="strDate">2020-01-01</set>
<set var="date">{Utils.StringToDate(strDate,'yyyy-MM-dd')}</set>
<log>{date} - {date.GetType()}</log> <!--outputs 1/1/2020 12:00:00 AM - System.DateTime -->
```
`Utils.SpeecifyKindUtc`  method changes Kind of existing DateTime variable to UTC without performing actual conversion of time. 
```xml
<set var="unspecifiedDate">{new DateTime()}</set>
<set>{Utils.SpeecifyKindUtc(unspecifiedDate)}</set>
```  
`Utils.SpecifyKindLocal`  method changes Kind of existing DateTime variable to Local without performing actual conversion of time.
```xml
<set var="utcDate">{Utils.Now.ToUniversalTime()}</set>
<set>{Utils.SpecifyKindLocal(utcDate)}</set>
```
`Utils.SpecifyKindUnspecified` method changes Kind of existing DateTime variable to Unspecified  
```xml
<set var="localDate">{Utils.Now}</set>
<set>{Utils.SpecifyKindUnspecified(localDate)}</set>
```
`Utils.GetRandom` method returns a random value of provided type up to provided max value.  
```xml
<log>{Utils.GetRandom("string")}</log>
<log>{Utils.GetRandom("bool")}</log>
<log>{Utils.GetRandom("int", 500)}</log>
<log>{Utils.GetRandom("long", 32464236)}</log>
<log>{Utils.GetRandom("decimal", 2.0)}</log>
<log>{Utils.GetRandom("double", 1.0)}</log>
<log>{Utils.GetRandom("datetime", new DateTime(2020,1,1))}</log>
<log>{Utils.GetRandom("date")}</log>
<!-- Each execution of above will return different output, below is just example
Test 1773426270
False
215
6208841438142469929
0.970704748281606
0.956690695116618
3/31/2015 3:36:58 PM
2/19/2014 12:00:00 AM
-->
```

`Utils.Eval` method invokes a provided string as an expression. It is an equivalent of expression specified in curly braces inside a `set` element, the difference that it takes a value of variable and then evaluates it. This method cannot be used for data operations, it is intended for simple expressions.  
```xml
<set var="expression">500+600</set>
<log>{500+600}</log> <!--outputs 1100 -->
<log>{expression}</log> <!--outputs 500+600 -->
<log>{Utils.Eval(expression)}</log> <!--outputs 1100 -->

<set var="myVar1">{200}</set>
<set var="myVar2">{300}</set>
<set var="strExpression">myVar1+myVar2</set>
<log>{strExpression}</log> <!--outputs myVar1+myVar2 -->
<log>{Utils.Eval(strExpression)}</log> <!--outputs 500 -->
```

### Class Xml
The class provides methods to work with xml documents.
`Xml.Load` Loads an XElement from a file, optionally preserving white space, setting the base URI, and retaining line information  
```xml
<set xml="Xml.Load(fileName)"/>
```
`Xml.LoadEnc`  Loads an XElement from a file.
```
todo example
```
`Xml.Parse` Load an XElement from a string that contains XML, optionally preserving white space and retaining line information.  
```xml
<set var="xmlSource"><![CDATA[
 <root>
   <add name="abdc">Test data</add>
   <add name="n2">Second text data</add>
 </root>]]>
</set>
<set xml="Xml.Parse(xmlSource, 1)"/>
```
`Xml.Select` Searches and selects values from within the specified element of an XML file and returns these values as an array.  
```
todo example
```
`Xml.ToXml`  todo description  
`Xml.FromXml` Passes an object (dictionary, list) to XML and a name of the root element and returns an XML string.<br/> Passes XML string and returns an object  
```
to do example
```

### Class Math
The short access to System.Math standard .NET class.  
`<set>{Math.methodName(param)}</set>` where methodName is a class specific method. For the list of supported methods refer <https://learn.microsoft.com/en-us/dotnet/api/system.math?view=netframework-4.6.2>  

### Class File
The short access System.IO.File standard .Net class.  
`<set>{File.methodName(param)}</set>` where methodName is a class specific method. For the list of supported methods refer <https://learn.microsoft.com/en-us/dotnet/api/system.io.file.exists?view=netframework-4.6.2>  

### Class Path
The short access System.IO.Path standard .Net class.  
`<set>{Path.methodName(param)}</set>` where methodName is a class specific method.  For the list of supported methods refer <https://learn.microsoft.com/en-us/dotnet/api/system.io.path?view=netframework-4.6.2>  

### Class Encoding
The short access to System.Text.Encoding .Net class.  
`<set>{Encoding.methodName(param)}</set` where methodName is a class specific method. For the list of supported methods refer <https://learn.microsoft.com/en-us/dotnet/api/system.text.encoding?view=netframework-4.6.2>  

### Structure TimeSpan
The short access to System.TimeSpan .Net structure.  
`<set>{TimeSpans.methodName(param)}</set>` where methodName is a structure specific method. For the list of supported methods refer <https://learn.microsoft.com/en-us/dotnet/api/system.timespan?view=netframework-4.6.2>  


# DATA OPERATIONS
As Sync360's main purpose is data processing, the commands to perform data operations are the most valuable feature of the engine. Developers can utilize the same commands in almost the same syntax to perform CRUD operations in various systems (support of commands varies based on the specific connector). This streamlines the development of integrations or ETL processes. Flow control operators can be used inside elements of data operators; the commands of flow control operators will execute prior to executing the actual command, thus allowing developers to construct queries programmatically using Sync360 syntax logic.

### Create

`create` element creates a record in specified table with defined attributes and returns newly created record uniqueidentifier into variable. The followig attributes are supported for this element:  
`in` attribute specifies name of connection for external system as defined in configuration file for the current context of script execution. It is a required attribute  
`entity` attribute specifies table in which new record should be created. It is a required attribute
`var` attribute specifies the name of variable to store uniqueidentifier of newly created record. It is an optional attribute  
Specify values for the table/properties of new record via child `attr` element.

```xml
<!-- example of create operation where all values are explicitly provided -->
<set var="accountid">{new Guid('5862c957-b2df-4f44-adb7-429ce006d943')}</set> <!-- assuming we know a unique identifier of required record -->
<create in="crmserver" entity="contact" var="newContactId">
    <attr name="firstname">Joe</attr>
    <attr name="lastname">Doe</attr>
    <attr name="emailaddress1">joe.doe@mail.com</attr>
    <attr name="jobtitle">manager</attr>
    <attr name="telephone1">444-44-44</attr>
    <attr name="parentcustomerid">account:{accountid}</attr>
</create>
```

```xml
<!-- creating a record using flow control and predefined mapping -->
<set var="mappings">{new List()}</set>
<set var="mappings[]">
  <attr name="src">FirstName</attr>
  <attr name="dst">firstname</attr>
  <attr name="type">string</attr>
</set>
<set var="mappings[]">
  <attr name="src">LastName</attr>
  <attr name="dst">lastname</attr>
  <attr name="type">string</attr>
</set>
<set var="mappings[]">
  <attr name="src">Email</attr>
  <attr name="dst">emailaddress1</attr>
  <attr name="type">string</attr>
</set> <!-- just three attributes, in real scenario there will be full mapping -->

<set var="Data"> <!-- defining data directly in Sync360 for example, in real scenarios the data will be retrieved from another system -->
    <attr name="FirstName">Joe</attr>
    <attr name="LastName">Doe</attr>
    <attr name="Email">joe.doe@mail.com</attr>
</set>

<create in="crm" entity="contact" var="newContactId">
    <for var="mapping" in="mappings">
        <attr name="{mapping.dst}">{Data[mapping.src]}</attr>
    </for>
</create>
```

## Select

`select` element is used for querying the data from external system. The followig attributes are supported for this element:  
`from` attribute specifies name of connection for external system as defined in configuration file for the current context of script execution. It is a required attribute  
`entity` attribute specifies table name from which data should be retrieved. This attribute is required when using sync360 native way to define query and optional when using connector specific syntax via `query` element.  
`var` attribute specifies variable name that will contain the result of search operation.  
`count` attribute specifies the number of records that will be passed to connector query to limit the returned results. This attribute is optional and can be omited.  
`page` attribute specifies from which page return records. Works in conjuction with count attribute to set the pagesize. This attribute is optional and can be omited.  
```xml
<!-- Retrieves second record from the crm table account, retrieves only unique identifier of the record -->
<select from="crm" entity="account" var="records" count="1" page="2">
</select>
```
The select operation requires child elements for defininig the query structure. The following child elements are used:  
`attr` element is used for specifiying name of fields/attribute on external table that should be retrieved. If there are no attr elements in select operation, the query will return only uniqueidentifier.  
`order` element is used for adding sorting in the query, the sorting will be performed on the external system side.
```xml
<!-- Retrieves all records from account table and sort them ascending. For each record retrieves only uniqueidentifer and name columns.-->
<select from="crm" entity="account" var="records">
    <attr name="accountid" />
    <attr name="name" />
    <order by="name" desc="false" />
</select>
```

`where` element is used to define criteria to narrow down the query. This element requires at least one child element `condition`. 
`condition` child element must have attributes `attr` and `op` for specifiying name of attribute and operator correspondingly. If operator requires a value it is passed into content of element. The following criteria operators available for `op` attribute value:  
Operators that can be applied to any data type: `eq` (Equals), `ne` (Not Equals), `ex` (Exists)  
Operators for numeric fields and dates: `lt` (Less Than), `le` (Less Than or Equal), `gt` (Greater Than), `ge` (Greater Than or Equal)  
Operators for strings: `co` (Contains), `sw` (Start With), `ew` (Ends with)  
Special operators: `between` (check that value is in between of two values), `in` (checks that value is one of provided values)
By default conditions passed to where element are grouped with `and` rule. Multiple condition elements can be grouped with grouping other grouping elements `and`, `or` and `not`. This allows to construct a query of any complexity.

```xml
<select from="crm" entity="contact" var="crmContacts">
    <where>
         <condition attr="firstname" op="eq">John</condition>
         <condition attr="lastname" op="eq">Doe</condition>
    </where>
    <attr name="contactid" />
</select>
<!-- selects all contacts with name John and surname Doe -->

<select from="crm" entity="account" var="verySpecificAccounts">
    <where>
     <or>
      <and>
	<or>
         <condition attr="address1_city" op="eq">Atlanta</condition>
         <condition attr="address1_city" op="eq">Augusta</condition>
       </or>
       <or>
        <condition attr="address1_stateorprovince" op="eq">GA</condition>
        <condition attr="address1_stateorprovince" op="eq">Georgia</condition>
       </or>
      </and>
     <not>
         <condition attr="address1_stateorprovince" op="ex" /> 
     </not>
     <not>
         <condition attr="address1_city" op="ex" /> 
     </not>
     </or>
    </where>
    <attr name="accountid" />
</select>
<!--selects accounts from Georgia state and specific cities and any accounts that don't have either State or City -->
```

`join` element allows querying data from related tables. Attributes: `type` (inner/outer/left/right), `entity` (related table name), `to` (field in main table), `from` (field in related table), `as` (optional alias).

```xml
<select from="crm" entity="contact" var="contactsWithAccounts">
    <attr name="firstname" />
    <attr name="lastname" />
    <join type="inner" entity="account" to="parentcustomerid" from="accountid" as="company">
        <attr name="name" as="companyname" />
        <join type="left" entity="systemuser" to="ownerid" from="systemuserid" as="owner">
            <attr name="fullname" as="ownername" />
        </join>
    </join>
</select>
```

`query` element allows using native query language of the external system. When using `query`, other child elements like `attr`, `where`, `order` are not used.

```xml
<!--using native FetchXML for Dynamics CRM -->
<select from="crm" entity="account" var="accounts">
    <query><![CDATA[
        <fetch version="1.0" mapping="logical" distinct="false" count="50">
            <entity name="account">
                <attribute name="name" />
                <attribute name="accountid" />
                <order attribute="name" descending="true" />
                <filter type="and">
                    <condition attribute="address1_country" operator="eq" value="US" />
                </filter>
            </entity>
        </fetch>
    ]]></query>
</select>

<!--using SQL with parameters -->
<select from="database" entity="employees" var="activeEmployees">
    <query>
        SELECT e.*, d.Name as DepartmentName 
        FROM Employees e
        JOIN Departments d ON e.DepartmentId = d.Id
        WHERE e.Status = @status AND e.JoinDate > @minDate
    </query>
    <attr name="@status">Active</attr>
    <attr name="@minDate">2023-01-01</attr>
</select>
```

## Update
The `update` element modifies existing records. Attributes: `in` (connection name, required), `entity` (table name, required), `var` (result variable, optional). Child elements: `where` (criteria, required), `attr` (fields to update, required).

```xml
<update in="crm" entity="contact">
    <where>
        <condition attr="emailaddress1" op="eq">old@email.com</condition>
    </where>
    <attr name="emailaddress1">new@email.com</attr>
    <attr name="emailstatus">Updated</attr>
</update>

<!-- Update with complex criteria -->
<update in="crm" entity="account">
    <where>
        <and>
            <condition attr="statecode" op="eq">{0}</condition>
            <or>
                <condition attr="revenue" op="lt">{100000}</condition>
                <condition attr="numberofemployees" op="lt">{10}</condition>
            </or>
        </and>
    </where>
    <attr name="customertype">SmallBusiness</attr>
    <attr name="followupdate">{Utils.Now.AddDays(7)}</attr>
</update>
```

## Delete
The `delete` element removes records. Attributes: `in` (connection name, required), `entity` (table name, required), `var` (result variable, optional). Child elements: `where` (criteria, required).

```xml
<delete in="crm" entity="task">
    <where>
        <condition attr="statecode" op="eq">{1}</condition>
        <condition attr="createdon" op="lt">{Utils.Now.AddDays(-365)}</condition>
    </where>
</delete>
```

## Context
The `context` element executes operations under a different user context. Attributes: `for` (connection name, required), `user` (user identifier, required - email for Exchange, GUID for CRM).

```xml
<context for="exchange" user="john.doe@company.com">
    <select from="exchange" entity="appointment" var="userAppointments">
        <where>
            <condition attr="start" op="ge">{Utils.Now}</condition>
        </where>
        <attr name="subject" />
        <attr name="start" />
    </select>
</context>

<context for="crm" user="{salesRepGuid}">
    <create in="crm" entity="phonecall" var="callId">
        <attr name="subject">Follow-up call</attr>
        <attr name="regardingobjectid">opportunity:{opportunityId}</attr>
    </create>
</context>
```

## Batch
The `batch` element groups multiple operations into a single request. Attributes: `for` (connection, required), `var` (results, required), `continueOnError` (true/false, required), `returnResponses` (optional, default true).

```xml
<batch for="crm" var="batchResult" continueOnError="true">
    <for var="contact" in="contacts">
        <create in="crm" entity="contact">
            <attr name="firstname">{contact.FirstName}</attr>
            <attr name="lastname">{contact.LastName}</attr>
        </create>
    </for>
</batch>

<!-- Error handling -->
<if condition="batchResult.IsFaulted">
    <for var="response" in="batchResult.Responses">
        <if condition="response.Fault.isSet">
            <log>Error at index {response.RequestIndex}: {response.Fault.Message}</log>
        </if>
    </for>
</if>
```

## Transaction
The `transaction` element ensures atomicity - all operations succeed or all fail. Attributes: `for` (connection, required), `var` (results, required), `returnResponses` (optional). Does not support `continueOnError`.

```xml
<transaction for="database" var="transferResult">
    <update in="database" entity="accounts">
        <where>
            <condition attr="accountid" op="eq">{sourceAccountId}</condition>
        </where>
        <attr name="balance">{sourceBalance - transferAmount}</attr>
    </update>
    <update in="database" entity="accounts">
        <where>
            <condition attr="accountid" op="eq">{targetAccountId}</condition>
        </where>
        <attr name="balance">{targetBalance + transferAmount}</attr>
    </update>
    <create in="database" entity="transactions">
        <attr name="sourceaccount">{sourceAccountId}</attr>
        <attr name="targetaccount">{targetAccountId}</attr>
        <attr name="amount">{transferAmount}</attr>
        <attr name="transactiondate">{Utils.Now}</attr>
    </create>
</transaction>
```

# EXCEPTION HANDLING OPERATIONS

## Exception
The `exception` element throws an exception to stop script execution.

```xml
<if condition="account.Count eq 0">
    <exception>Account {accountId} not found</exception>
</if>

<if condition="account[0].totaldue gt account[0].creditlimit">
    <exception>Account exceeded credit limit. Due: {account[0].totaldue}, Limit: {account[0].creditlimit}</exception>
</if>
```

## Sandbox
The `sandbox` element provides exception isolation. Attribute: `verbose` (optional, default true).

```xml
<sandbox verbose="true">
    <set var="result">{Http.Get('https://api.example.com/data')}</set>
    <set var="data">{Json.FromJson(result)}</set>
    <onerror of="typeof System.Net.WebException">
        <log>Network error - using cached data</log>
        <set var="data">{cachedData}</set>
    </onerror>
</sandbox>
```

## OnError
The `onerror` element handles specific exception types within a sandbox. Attribute: `of` (type expression, required).

```xml
<sandbox>
    <set var="result">{100 / divisor}</set>
    <onerror of="typeof System.DivideByZeroException">
        <log>Division by zero</log>
        <set var="result">{0}</set>
    </onerror>
    <onerror of="typeof System.Exception">
        <log>Unexpected error: {Exception.Message}</log>
    </onerror>
</sandbox>
```

# STRUCTURAL OPERATIONS

## Include
The `include` element incorporates external script files. Attribute: `name` (path without .xml extension, required). Search order: current directory, application Scripts folder, global Scripts folder.

```xml
<include name="Config/Settings" />
<include name="Utils/DataValidation" />
<include name="Modules/CustomerSync" />
```

## Call
The `call` element executes another script in isolated context. Attribute: `name` (script name, required). Additional attributes are passed as parameters.

```xml
<call name="DataProcessor" Settings="config" SourceFile="'data.csv'" />

<!-- Alternative syntax -->
<MyScriptHelper CallSettings="parameters" />
```

## Script
The `script` element is the root container for Sync360 code.

```xml
<script>
    <!-- Script content -->
</script>
```

## Log
The `log` element outputs messages.

```xml
<log>Processing record {recordId} of {totalRecords}</log>
<log>Memory usage: {GC.GetTotalMemory(false) / 1024 / 1024} MB</log>
```

# EXTENDING FUNCTIONALITY

Bind to .NET types using `typeof` and static methods using `static`. Custom assemblies must be in GAC or Sync360 directory.

```xml
<set StringBuilder="typeof System.Text.StringBuilder" />
<set Files="static System.IO.File" />
<set Regex="typeof System.Text.RegularExpressions.Regex" />
<set CustomValidator="typeof MyCompany.Validator, MyCompany.Integration" />

<set var="sb">{new StringBuilder()}</set>
<set var="exists">{Files.Exists(fullPath)}</set>
<set var="pattern">{new Regex(@"\d{3}-\d{3}-\d{4}")}</set>
```
