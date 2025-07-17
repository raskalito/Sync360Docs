# SYNC 360 ENGINE

The following documentation describes the syntax for programming scripts for Sync360 application. Sync360 is ETL tool which executes Sync360 scripts, it is build on .NET Framework version 4.6.2 and derives a lot of types from it.
When Sync360 engine executes a script file, each command in the script file execute one by one in a single thread. The Sync360 application as any ETL tool has predefined list of connectors for external systems which can be used to perform CRUD operations against their data. The connector for Dynamics CRM is most advanced and has support of all syntax capabilities, for other connectors support may vary. The access to specific connector in context of script execution depends on configuration files used for script execution, meaning there is a configuration file where the name of connection explictly set along with type of connection and authetification information.

# GENERAL SCRIPT SYNTAX

A Sync360 script is an xml file which must be a valid xml file and consist of Sync360 specific commands. The root element of each script file must be ```<script>```. Each element under the root element represents either flow control or a command for the script execution.
The child elements of tags can be used as parameters of the command or if used inside flow control elements as command for optional execution. There is no limitation of levels or number of commands inside a sync360 script.
An example of sync360 command with child elements that acts as command properties.
```
<set var="CitiesAndCountries">
    <attr name="Cities">Houston,Phoenix</attr>
    <attr name="Countries">USA,UK,Canada</attr>
</set>
```
Names of elements, as well as names of attributes, cannot contain space characters. The name should begin with a letter or an underline character. The rest of the name may contain as the same characters as well as digit characters.
Attribute is a markup construct consisting of a name/value pair that exists within a start-tag or empty element-tag followed by an element name. Values of attributes should be always embedded in single or double quotes.
It is necessary to use identical types of quotes for values of attributes in the same tag (please see Cities and Countries in the example above). 

Content inside an element acts as a value. Curly braces { } instructs the engine to evaluate the expression, this is a method of acccessing variables or performing logical calculations. For some commands the properties will be evaluated without curly brackets.  
```
<set var="firstname">Joe</set> <!-- assignment of text value 'Joe' to variable 'firstname' without expressions. -->
<set var="calculatedvalue">{294+322}</set> <!-- assignment of result of expression in the element content. Notice that braces are required -->
<set calculated="294+322" /> <!-- assignment of result of expression via property. Notice that braces are not required -->
<if condition="contacts.Count eq 0"> <!-- an expression in the attribute value. In this command condition attribute doesn't require braces, evaluation always occurs.-->
```

An expression may include constants and variables. A variable name is case-sensitive (please see *calculatedvalue* in the example above).

The expression type is selected automatically based on the expression value evaluation, but it can be converted to a necessary type by using special construction 'as'. The following examples give the same result:
```
<set var="counter">{2}</set>
<set var="counter">{2 as 'int'}</set>
```

Moreover, variable values may be a simple value, an array and a dictionary (declaration and modification of variable values are described in the clause 7).
A value of an array item can be received by specifying the array name and index of the necessary element inside square brackets. Indexes start from zero.
```
<log>First account: {account[0]}</log>
```

The random element of an array may be selected if the name of an array is specified with empty square brackets.
```
<set var="crmservers">{[crm,crm4,crm5]}</set>
<log>Random CRM server: {crmservers[]}</log>
```

A value of a dictionary item can be received by specifying the dictionary name and name of the necessary element in quotes and inside square brackets or by specifying name of the necessary element followed by the dictionary name with a dot.
```
<log>City: {account.city}</log>
<log>City: {account["city"]}</log>
```

If the corresponding element is not found, search of a method with this name will be produced via Reflection and, if the method is found, then it will be applied to the dictionary. Otherwise, an exception will be thrown.
'''
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
In Sync360 scripting language operators is a construct that can be used in expressions. Sync360 engine doesn't provide operand coercion, operators can be used only across supported types.
There are some significant differences in semantics used for operators compared to other programming languages. For example operator '=' and '==' in sync360 syntax means the same.
The list of all available operators and their explanation is below:

### Arithmetic operators:
Addition operator is `+`. This operator adds second operand to the first operand. Supported types: int, long, double, string. The operator is ad hoc polymorphic, for numbers it performs arithmetic operation and for strings performs concantenation.  
Subtraction operator is `-`. This operator subtracts second operand from the first operand. Supported types: int, long, double.  
Multiplication operator is `*`. This operator multiplies first operand by the second operand. Supported types: int, long, double.  
Division operator is `/`. This operator divides the first operand by the second operand. Supported types: int, long, double.  

### Relational operators:
Equals operator is `=` or `==` or `eq`. This operator returns true if the first operand is equal to the second operand. Supported  types: int, long, double, string, bool, date and object.  
Not equal operatoris `!=` or `<>` or `ne`. This operator returns true if the first operand is not equal to the second operand. Supported  types: int, long, double, string, bool, date and object.  
Greater tha operator  is `>` or `gt`'. This operator returns true if the first operand is greater than the second operand. Supported types: int, long, double, string and date.  
Less than is `<` or `lt`. This operator returns true if the first operand is less than the second operand. Supported types: int, long, double, string and date.  
Greater or equal is `>=` or `ge`. This operator returns true if the first operand is greater than or equal to the second operand. Supported  types: int, long, double, string and date.  
Less or equal is `<=` or `le`. This operator returns true if the first operand is less than or equal to the second operand. Supported  types: int, long, double, string and date.  

### Logical operators:
And is `&&` or `and`. This operator performs a logical-AND of its bool operands. Supported  types: bool.  
Or is `||` or `or`. This operator performs a logical-OR of its bool operands. Supported types: bool.  
Not is `!` or `not`. This operator is a unary operator that negates its operand. It returns true only if its operand is false.Supported  types: bool.  

### Program structure operators:
Index, Record or Object access is  `[]` or `.`. This operator returns an array element at the specified index, a property of an object or a dictionary element with the specified string key.  
```<log>{myDict['property1']}</log>``` or ```<log>{myDict.property1}</log>```  

Expression validation is `.isSet`. This operator can be applied to any expression or variable  to validate that it can be evaluated. When expression can be evaluated and returns a value operator returns true otherwise it return false. This operator prevents exceptions when applied to expressions  
 ```<set var="myVar">{232}</set> <log>{myVar.isSet}</log> ``` or ```<if condition="(250+300+myVar).isSet"><log>myVar is a number</log></if>```  

Collections total is `.Count`. This operator can be applied to any collection variable to get total number of items in collection.  
```<set var="myArray">{1,2,3]}</set> <log>{myArray.Count</log>```  

### Conditional operators
Null coalesing is `??`. This operator returns the first calculated value in chain of expressions. <br><b>Example:</b> If 'MyVar' property of 'Global' variable is not null displays property value, else display text 'MyVar was not set'. ```<log>{Globals['MyVar'] ?? 'MyVar was not set'}</log>```  
Ternary conditional is `?` with usage of ` expression ? value if true : value if false`. This operator evaluates an expression before and if it is true 

# DECLARATION OF THE VARIABLES

`set` operation is used for declaration and modification of the variables. The name of variable can be supplied via `var` attribute of `set` element, then a value is passed via content of set tag. However, you can skip `var` attribute and define variable name explicitly as a property of set tag, in this case the value passed into the property will be used a variable value. Set element could be also used without any attributes, in this case it used for execution/evaluation of expression passed into content of element, without any assignment.

## Simple variable

To declare a simple variable, define a name using `var` property and provide value via element content. When value passed without curly braces it evaluated as string. Using curly braces and providing digits Sync360 engine attempts to resolve it to best matching simple types. The default behavior that numeric values are resolved as Int32 or Int64 depending on size. Any fraction numbers are resolved as Double. Because of that is there a need to use specific type it is required to use `as` operator to define a type.
```
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
```
<set var="accountid">{value}</set>

<!-- The other way to do same operation -->
<set accountid="value" />
```

## Structures
Structures are essential part of Sync360 scripting as they provide methods for manipulating and working with data. When outputing structure using `log` command the Sync360 engine automatically serialize them to string representation.

### Dictionary

To declare the dictionary variable use construction `<set var="myDict">{new Dictionary()}</set>` after that you can assign values by specifing string keys using `[]` index operator. 
```
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
```
<set Names="new List()" />
<set var="Names[]">Michael</set>
<set var="Names[]">Joe</set>
```
List structure is particularly useful when there is no need to access individual specific elements or when there is a need to access a random elements.  List also can be used for sorting string or number values due to it default methods.
```
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
```
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
```
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
```
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
```
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
```
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

When `if` element is used, it is possible to control the execution of flow using nested `then` and `else` elements. That allows to execute a set of different commands when experssion evaluates as true and as false. `then` element can contain inline `if` property and that allows multiple code flows to execute within same parent `if` element. However `else` element cannot have `if` inline and therefore only one nested `else` element can exists per `if`.
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
</if>
```

## For, While, Break, Continue

`for` element is used when a code block needs to be executed a certain amount of times. Sync360 supports iteration through enumerated structures using `for` statement as well as standard iteration by number of times, this depend on attributes supplied in the `for` element.
```
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
```
<set var="crmservers">{['crm','crm4','crm5']}</set>
<set var="counter">{0}</set>
<while condition="counter lt crmservers.Count">
     <log>{crmservers[counter]}</log>
     <set var="counter">{counter + 1}</set>
</while>
```


`break` element can be used to exit the loop. It is intended to be used in conjunction with `if` element to make a conditional end of cycle. Once break command executed, the Sync360 engine moves to the next command after end of cycle element. It can be used in both `for` and `while` cycles.
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
    </if>
</for>
<log>End cycle.</log>
```

`continue` element can be used to skip current cycle logic that is located under this element and move on to the next cycle iteration. Also intended to use in conjunction with `if` element. It can be use in both `for` and `while` cycles
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
     </if>
     <log>{t}</log>
</for>
<log>End cycle.</log>
```

# **FUNCTIONS**

In Sync360 there are static instances of classes with predefined list of methods that aim to help a developer to implement the scripting logic. These classes are written using C# and .NET framework and implement the logic which is not available using standard Sync360 commands. Invoking class methods can be considered as standalone functions.

### Class CSV
The class provides methods to work with CSV files. It has two methods Read and Write. There are default values for parameters supplied to methods of this class:  
```
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
```
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
```
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
        <if condition="key.ToString() eq 'parentcustomerid'>
            <then>
                <set var="values[key]">{record[key].Name ?? ''}</set>
            </then>
            <else>
                <set var="values[key]">{record[key]}</set>
            </else>
    <for>
</for>
<set>{Csv.Write(csv, 'c:\temp\crmContacts.csv'</set>
<!--Example above dynamically forms required data structure from records retrieved from Dynamics CRM system using select operator. Notice that several flow control operators are used in this example to iterate through data and to output correct properties based on field type (parentcustomerid is a lookup field to another table and therefore to get it's text value .Name property used -->
```

### Class FileUtils
The class provides methods to work with windows file system. It has various methods for manipulating with files, the methods will be described below.  
`FileUtils.ReadFile` opens a file, reads the contents of the file into a byte array, and then closes the file. It can be useful when the file must be transfered from a file system into another system.  
``` <set var="Bytes">{FileUtils.ReadFile("c:\temp\data.bin")}</set> ```  

`FileUtils.ReadFileAsBase64` opens a file, reads the contents of the file into a byte array, converts the array into a Base64 string, and then closes the file. This method especially useful for uploading attachments into Dataverse, as the file contents are stored in base64 format inside documentbody property.  
```<set var="Bytes">{FileUtils.ReadFileAsBase64("c:\temp\data.bin")}</set>```

`FileUtils.ReadIdsFromFile` method is used to read predefined GUIDS from text file. GUIDS should be placed line by line. The result will be an array of System.Guid.  
```
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
```
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
``` 
<set var="path">c:\temp\</set>
<set var="files">{FileUtils.GetFiles(path, "*.csv")}</set>
<for var="file" in="files">
   <log>{file.Name} - {file.Length}</log>
</for>
```

`FileUtils.WriteToFile` used The function writes specified string into a file. It is usually used for logging or for dumping the intermediate result of script execution. Method accept three parameters  `<set>{WriteToFile(string path, string data, bool Append)}</set>`
```<set>{FileUtils.WriteToFile("c:\temp\log.txt","Test", true)}</set> ```

'FileUtils.MoveFile' moves a specified file to a new location, providing the option to specify a new file name.
```
<set>{FileUtils.MoveFile("c:\temp\loq.txt", "c:\temp\log_moved.txt") }</set>

<set var="sourcePath">c:\temp\</set>
<set var="targetPath">c:\temp\processed\</set>
<set var="filename">log_moved.txt</set>
<set>{FileUtils.MoveFile(sourcePath + filename, targetPath + filename) }</set>
```

`FileUtils.Zip` method creates a zip archive with files from specified path
```
<set>{FileUtils.Zip("C:\temp\", "C:\temp\archive.zip")}</set> <!-- archives all files in temp folder into archive.zip file -->
```

`FileUtils.Unzip` method extracts files from a specified zip-archive into a selected folder.
```
<set>{FileUtils.Zip("C:\temp\archive.zip", "C:\temp\extracted\")}</set> <!-- extract files from archive.zip into extracted folder -->
```

### Class Html and HtmlText
These classes are used to work with html syntax and html document.

`Html.ToPlainText` method converts html syntax into plain text by removing any html tags. It has two overloads, it can accept string which contains html syntax or an HtmlDocument structure.
```
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
```<set>{Http.SetUseDefaultCredentials(true)}</set>```

`Http.SetClientEncoding` sets a client encoding for connections. Encoding should be provided as string.  
```<set>{Http.SetClientEncoding('utf-8')</set>```

`Http.Get` method performs HTTP GET web request for specified URL. The method has overloads and therefore number of parameters can vary, below each available overload  
1) `<set var="response">{Http.Get(string url)}</set>` can be used for public websites that doesn't require authorization or specific headers. The response is retrieved as a string.
2) `<set var="response">{Http.Get(string url, string login, string password)}</set>` using basic web authorization by username and password. The  information will be added automatically as "Authorization" header with type "Basic".
3) `<set var="response">{Http.Get(string url, string login, string password, string domain)}</set>` can be used for websites requiring windows authentification using NetworkCredentials.
4) `<set var="response">{Http.Get(string url, object headers)}</set>` to pass custom headers. The most commonly used overload for permorming requests as allows full control over what will be passed to request. Headers can be defined as an object or a dictionary, where property name is header name and value is header value.
 ```
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
```
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

```
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

``` 
<set var="response">{Http.Patch('http://www.example.com', 'some data', 'headers go here')}</set>
```
`Http.Download` downloads the file that URL contains, e. g. page, archive, document.
```
<set var="instructionPdf">{Http.Download('http://www.example.com/docs/instruction.pdf')}</set>
```

`Http.GetRaw` this method performs Get request using TcpClient.TcpClient. So the response will be provided in unchanged, literal form.There are two overloads for this method  
1)`<set var="rawresponse">{Http.GetRaw(string url}</set>` simply perform HTTP GET request  
2)`<set var="rawresponse">{Http.GetRaw(string url, int sendTimeout, int receiveTimeout}</set>` The same as above but allows to specify timeouts  
```
example todo
```

`Http.SetCookie` set cookie for the specific URL.
```
<set var="cookie">id=a3fWa;Expires=Thu, 31 Oct 2021 07:28:00 GMT;</set>
<set>{Http.SetCookie("http://example.com", cookie)}</set>
```

`Http.GetCookie` retrieves cookie from the specified URL.
```
<set var="cookie">{Http.GetCookie("http://example.com")}</set>
```

### Class Json
The class provides methods to work with JSON format.  
`Json.ToJson` method performs serialization of object to a string in JSON format. Accepts objects as parameter and indents JSON string by default. Indent can be disabled by passing false to second parameter.
```
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
``` 
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
```
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
```
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
```
<set var="newGuid">{Utils.NewGuid}</set>
```
`Utils.Split` method identifies the substrings in a string value that are delimited by one or more characters provided, then places the substrings into a specified string array.  
```
<set var="Names">Roman; Joe; Michael</set>
<set var="NamesArray">{Utils.Split(Names,';')}</set>
<log>{NamesArray[0]}</log> <!-- outputs Roman -->
 ```  
`Utils.Join` method combines array values into string with specified delimiter. It is opposite of Split method.  
```
<set var="NamesArray">{['Roman','Joe','Michael']}</set>
<set var="NamesStr">{Utils.Join(NamesArray,';')}</set>
<log>{NamesStr}</log> <!-- outputs Roman;Joe;Michael -->
```  
`Utils.ToUpper` method returns a copy of provided string converted to uppercase.  
```
<set var="text">small case</set>
<log>{Utils.ToUpper(text)}</log> <!--outputs SMALL CASE -->
```
`Utils.toLower` method returns a copy of provided string converted to lowercase.  
```
<set var="text">ALL CAPS</set>
<log>{Utils.ToLower(text)}</log> <!--outputs all caps -->
```
`Utils.NewLine` method returns newline string ("\n").  
```
<log>line1 {Utils.NewLine}line2 </log> <!--outputs
line1 
line2 
-->
```
`Utils.Now` method returns a datetime structure that is set to the current date and time based in the local timezone, which is based on the current environment configuration.  
```
<set var="currentDate">{Utils.Now}</set>
<log>{currentDate.Kind}</log> <!-- outputs local -->
```  
`Utils.Replace` method returns a new string in which all occurrences of a specified Unicode String in the current string are replaced with another specified Unicode character or String. <br/> <b>Parameters</b> <br/> $\bullet$ string <br/> <b>Example</b> <br/> ``` <set var="str1">This is an example</set>  <set var="str2">{Utils.Replace(str1,'This','Here')}</set> ``` |
`Utils.IsKeyExist` <b>Description</b></br/> Checks whether the key exists in a dictionary or not.<br/> <b>Parameters</b><br/> IsKeyExist(object, string) <br/>  $\bullet$ object dict - the required dictionary. <br/>  $\bullet$   string key — the line that contains a key <br/> <b>Example</b> <br/> ``` <set>{Utils.IsKeyExist("DictionaryExample", "reg4y736")}</set> ``` |
`Utils.ParseGUID` <b>Description</b> <br/> Converts the string representation of a GUID to the equivalent Guid structure.<br/> <b>Parameters</b><br/> ParseGuid(string) <br/>  $\bullet$ string guid - the required GUID.<br/> <b>Example</b><br/> ``` <set var="newGuidVar">{Utils.ParseGUID('5dcf85ae-ca84-4718-afb8-1795db389763')</set> ```|
`Utils.TextualltEquals` <b>Description</b><br/> Compares two objects:  If objects are of the same type, it compares them as they are. If objects are of different types, it converts them into strings and then compares the strings.<br/> <b>Parameters</b> TextuallyEquals(object, object, bool)</br>  $\bullet$ object one - the first object in a pair. </br>  $\bullet$  object two - the second object in a pair.</br>  $\bullet$ bool ignoreCase - manages case sensitivity.<br/> <b>Example</b>|
`Utils.Right`  
`Utils.Left` <b>Description</b> <br/> <b>Parameters</b><br>Right(string, int)<br> $\bullet$ string str<br> $\bullet$ int charCount<br>Left(string, int)<br> $\bullet$ string str<br> $\bullet$ int charCount<br/> <b>Example</b>|                                                     
`Utils.Encode`  
`Utils.Decode` <b>Description</b><br>Encode to/Decode from the standard HTML encoding.<br/><b>Parameters</b><br>Encode(string)<br> $\bullet$ string str<br>Decode(string)<br/> $\bullet$ string str<br><b>Example</b>|                                                                 
`Utils.DateToString`  
`Utils.StringToDate`  <b>Description</b><br>Converts date to string and vice versa.<br/> <b>Parameters</b><br>DateToString(DateTime, string)<br> $\bullet$ DateTime date-the exact date to be converted.<br/> $\bullet$ string format.<br/> StringToDate(string, string)<br>$\bullet$ string date - the exact string to be converted.<br> $\bullet$ string format  
`Utils.SpeecifyKindUtc`  Applies UTC timezone to DateTime  
```
<set var="unspecifiedDate">{new DateTime()}</set>
<set>{Utils.SpeecifyKindUtc(unspecifiedDate)}</set>
```  
`Utils.SpecifyKindLocal`  
`Utils.SpecifyKindInspecified` | <b>Description</b><br>Applies a specified timezone to date.<br/> <b>Parameters</b> SpecifyKindUtc(DateTime)<br/> $\bullet$ DateTime date <br/> SpecifyKindLocal(DateTime) <br/> $\bullet$ DateTime date <br/> SpecifyKindUnspecified(DateTime) <br/> $\bullet$ DateTime date <br/> <b>Example</b>|
`Utils.Eval`   The function executes a provided string as a command.<br/> <b>Parameters</b><br/> Eval(string) <br/> $\bullet$ string expression<br/>  <b>Example</b><br/> ``` <set var="NeedProcess">{Utils.Eval(strExpression)}</set> ``` |
`Utils.GetRandom`  <b>Description</b><br/> The function gets random values of random types<br/> <b>Parameters</b><br/> GetRandom(string, object)<br/> $\bullet$ string type <br/> $\bullet$ object max <br/> <b>Example</b> |

### Class Xml
The class provides methods to work with xml documents.
`Xml.Load` Loads an XElement from a file, optionally preserving white space, setting the base URI, and retaining line information  
```
<set xml="Xml.Load(fileName)"/>
```
`Xml.LoadEnc`  Loads an XElement from a file.
```
todo example
```
`Xml.Parse` Load an XElement from a string that contains XML, optionally preserving white space and retaining line information.  
``` 
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
As Sync360 main purpose is data processing the commands to perform data operations is most valuable feature of the engine. Developers can utilize the same commands in almost the same syntax to perform CRUD operations in various system (support of command is varied based on specific connector). This streamlines the development of integrations or ETL processes.
Flow control operators can be used inside elements of data operators, the commands of flow control operators will execute prior executing the actual command, thus allowing to construct queries programatticaly using sync360 syntax logic.

### Create

`create` element creates a record in specified table with defined attributes and returns newly created record uniqueidentifier into variable. The followig attributes are supported for this element:  
`in` attribute specifies name of connection for external system as defined in configuration file for the current context of script execution. It is a required attribute  
`entity` attribute specifies table in which new record should be created. It is a required attribute
`var` attribute specifies the name of variable to store uniqueidentifier of newly created record. It is an optional attribute  
Specify values for the table/properties of new record via child `attr` element.

```
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

```
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
```
<!-- Retrieves second record from the crm table account, retrieves only unique identifier of the record -->
<select from="crm" entity="account" var="records" count="1" page="2">
</select>
```
The select operation requires child elements for defininig the query structure. The following child elements are used:  
`attr` element is used for specifiying name of fields/attribute on external table that should be retrieved. If there are no attr elements in select operation, the query will return only uniqueidentifier.  
`order` element is used for adding sorting in the query, the sorting will be performed on the external system side.
```
<!-- Retrieves all records from account table and sort them ascending. For each record retrieves only uniqueidentifer and name columns.-->
<select from="crm" entity="account" var="records">
    <attr name="accountid" />
    <attr name="name" />
    <order by="name" desc="false" />
</select>
```

`where` element is used to define criteria to narrow down the query. This element requires at least one child element `condition`.  
`condition` child element must have attributes `attr` and `op` for specifiying name of attribute and operator correspondingly. If operator requires a value it is passed into content of element. The following criteria operators availalbe for `op` attribute value:  
Operators that can be applied to any data type: `eq` (Equals), `ne` (Not Equals), `ex` (Exists)
Operators for numeric fields and dates: `lt` (Less Than), `le` (Less Than or Equal), `gt` (Greater Than), `ge` (Greater Than or Equal)
Operators for strings: `co` (Contains), `sw` (Start With), `ew` (Ends with)

```
<select from="crmserver" entity="contact" var="contacts_with_name_Joe">
    <where>
         <condition attr="firstname" op="eq">Joe</condition>
    </where>
    <attr name="contactid" />
</select>
```
`query` element is used to specify query in native query language of external system. When `query` element used all the details of query are specified as content of it and other child elements not used. In other words you can either use `query` child element or define the query using `attr`, `order` and `where`.

The result of select operation will be array of Use4si.Core.DynamicDictionary structures.

To read all names and ids of first 50 accounts where Country attribute value is equal to "US" and sort them by name attribute in descending order, you could use Select operation as follows:
```
<!--using sync360 syntax -->
<select from="crmserver" entity="account" var="accounts" count="50">
    <where>
         <condition attr="address1_country" op="eq">US</attr>
    </where>
    <attr name="name"/>
    <attr name="accountid"/>
    <order by="name" desc="true"/>
</select>

<!--same operation as above but using native query of crm system, which is fetchxml. Notice that CDATA is used to escape fecthxml as sync360 scripts are also xml -->
<select from="crmserver" entity="account" var="accounts">
    <query>
<![CDATA[
<fetch version="1.0" output-format="xml-platform" mapping="logical" distinct="false" count="50">
  <entity name="account">
    <attribute name="name" />
    <attribute name="accountid" />
    <order attribute="name" descending="true" />
    <filter type="and">
      <condition attribute="address1_country" operator="eq" value="US" />
    </filter>
    <link-entity name="contact" from="contactid" to="primarycontactid" visible="false" link-type="outer" alias="a_410707b195544cd984376608b1802904">
      <attribute name="emailaddress1" />
    </link-entity>
  </entity>
</fetch>
]]>
    </query>
</select>
```

**Select** operation supports native SQL queries using query element. You can also pass parameters to the query using **attr** element. The entity element is required to be specified for the code to be valid, but with this method it's not used.
```
<select from="db" entity="tasks" var="tasks">
    <query>
         select Tasks.Name as 'ttt', Instances.Name, Instances.InstanceID from tasks, Instances where tasks.InstanceID = Instances.InstanceID and Instances.Title = @title
    </query>
     <attr name="@title">Main work</attr>
</select>
```

**Select–join** operation looks for records of specific type in accordance with the criteria applied to the entity itself and related entities.

#### Table 8. Join operation attributes.

| <b>Attribute</b> | <b>Description</b>                                                                                                                                                                             | <b>Usage</b> |
|------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| type             | Join type (based on used connector).<br/> For example, for CRM connector:  <https://docs.microsoft.com/en-us/previous-versions/dynamicscrm-2016/developers-guide/gg327702%28v%3dcrm.8%29>  | Required     |
| entity           | Related entity name.                                                                                                                                                                           | Required     |
| to               | Field name of the related entity.                                                                                                                                                              | Required     |
| from             | Field name of the main entity.                                                                                                                                                                 | Required     |
| as               | Attribute name for storing the result. Simplifies data<br>access.                                                                                                                              | Optional     |

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



### Update

**Update** operation updates the specified entity records corresponding with the search criteria with predefined attributes set. The operation returns an array of dictionaries.

#### Table 11. Update operation attributes.
| Attribute | Description                                           | Usage    |
|-----------|-------------------------------------------------------|----------|
| ın        | A server name that the update operation performs for. | Required |
| entity    | A type of records that will be updated.               | Optional |


#### Table 12. Update operation children elements.
| Element | Description                                                                                | Usage    |
|---------|--------------------------------------------------------------------------------------------|----------|
| where   | A criteria element contains conditions set that will be used to find<br>records to update. | Required |
| attr    | An attribute name and its value for updated records.                                       | Required |


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

#### Table 13. Delete operation attributes.
| <b>Attribute</b> | <b>Description</b>                                    | <b>Usage</b> |
|-------------------|------------------------------------------------------|--------------|
| in           | A server name that the delete operation performs for. | Required     |
| entity           | A type of records that will be deleted.               | Optional     |


#### Table 14. The Delete operation child elements.
| Element | <b>Description</b>                                                   | <b>Usage</b> |
|-|-|-|
| where   | A criteria element contains conditions set that will be used to find records to delete.| Required     |


The following script deletes an Exchange contact by known EntryId:
```
<delete in="exchange">
     <where>
         <condition attr="EntryId" op="eq">{EntryId}</condition>
    </where>
</delete>
```

## **Context**

`context` element is used to wrap commands that must be performed under specific user context. By default all data operations are performed under user/service specified in the configuration file, the context allows to perform the operation under another user/service credential, the only requirement is for the service account to have impersonation permission in the target. The context element affects only data operation commands for specific connection specified in `for` attribute, it doesn't has any effect to any non data operation commands. `user` attribute in context element is used to specify whom it is required to impersonate. In current version of Sync360 context command can be used for Exchange and Dynamics CRM/Power Platform connections. For Exchange connection user attribute expects email address of target user, for CRM connection the user attribute should be GUID of user record.

```
<context for="exchange" user="{userEmail}">
     <select from="exchange" entity="contact" var="contacts">
         <where>
             <or> <condition attr="crmLinkState" op="eq">0</condition>
                  <condition attr="crmLinkState" op="eq">2</condition>
            </or>
            <condition attr="crmid" op="ne"></condition>
         </where>
         <attr name="EntryId"/>
         <attr name="crmid"/>
         <attr name="crmLinkState"/>
         <attr name="iconindex"/>
         <attr name="crmOwnerId"/>
     </select>
</context>

<context for="crm" user="{userGuid}">
     <select from="crm" entity="contact" var="contacts">
         <where>
            <condition attr="contactid" op="eq">{contactId}</condition>
         </where>
         <attr name="fullname"/>
         <attr name="jobtitle"/>
         <attr name="emailaddress1"/>
     </select>
</context>
```

## Batch

**Batch** operation can be used to combine create, update, or delete operations into one batch to minimize the number of requests to target system.

#### Table 15. Batch operation attributes.
| <b>Attribute</b> | <b>Description</b>                                                                                                    | <b>Usage</b> |
|-|-|-|
| for              | A server name that Batch operation will run for.                                                                      | Required     |
| continueOnError  | false – do not continue processing the next request on error.                                                         | Required     |
| var              | The variable name that will contain the response from target system. Response is presented only for create operation. | Required     |
| returnResponses | true—return responses from each message request  processed.<br/> false—do not return responses.                        | Optional     |

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
                      <attr if="cnt['lastname'].isSet and cnt['lastname'] ne ''" name="lastname">{cnt['lastname']}</attr>
                      <attr if="cnt['firstname'].isSet and cnt['firstname'] ne ''" name="firstname">{cnt['firstname']}</attr>
                      <attr if="cnt['salutation'].isSet and cnt['salutation'] ne ''" name="salutation">{cnt['salutation']}</attr>
                      <attr if="cnt['birthdate'].isSet and cnt['birthdate'] ne ''" name="birthdate">{cnt['birthdate']}</attr>
                      <attr if="cnt['slaname'].isSet and cnt['slaname'] ne ''" name="slaname">{cnt['slaname']}</attr>
                      <attr if="cnt['telephone1'].isSet and cnt['telephone1'] ne ''" name="telephone1">{cnt['telephone1']}</attr>
                      <attr if="cnt['telephone2'].isSet and cnt['telephone2'] ne ''" name="telephone2">{cnt['telephone2']}</attr>
                      <attr if="cnt['telephone3'].isSet and cnt['telephone3'] ne ''" name="telephone3">{cnt['telephone3']}</attr>
                      <attr if="cnt['websiteurl'].isSet and cnt['websiteurl'] ne ''" name="websiteurl">{cnt['websiteurl']}</attr>
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
             </if>
         </for>
   </if>
```



```
<!- Example: (Batch response handling for create operation) -->
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
        </if>
</for>
```

### **Transaction**

**Transaction** operation can be used to combine create, update or delete operations into one transaction to minimize the number of requests to target system.

#### Table16 . Transaction operation attributes.
| <b>Attribute</b> | <b>Description</b>                                                                                                          | <b>Usage</b> |
|------------------|-----------------------------------------------------------------------------------------------------------------------------|--------------|
| for              | A server name that Batch operation will run for.                                                                            | Required     |
| var              | The variable name that will contain the response<br>from target system. Response is presented only<br>for create operation. | Required     |
| returnResponses  | true - return responses from each message<br>request processed.<br>false - do not return responses.                         | Optional     |

```
<set var="PageSize">{10}</set>
<log>Script started at {Utils.Now}</log>
<set var="csvContacts">{Csv.Read('c:/temp/contact lite test.txt', Encoding.GetEncoding('utf-8'),';')}</set>
<set var="Continue">{true}</set>
<set var="RecordsLeft">{csvContacts.Count}</set>
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
                    <attr name="lastname" if="cnt['lastname'].isSet and cnt['lastname'] ne ''">{cnt['lastname']}</attr>
                    <attr name="firstname" if="cnt['firstname'].isSet and cnt['firstname'] ne ''">{cnt['firstname']}</attr>
                    <attr name="salutation" if="cnt['salutation'].isSet and cnt['salutation'] ne ''">{cnt['salutation']}</attr>
                    <attr name="birthdate" if="cnt['birthdate'].isSet and cnt['birthdate'] ne ''" >{cnt['birthdate']}</attr>
                    <attr name="slaname"  if="cnt['slaname'].isSet and cnt['slaname'] ne ''">{cnt['slaname']}</attr>
                    <attr name="telephone1" if="cnt['telephone1'].isSet and cnt['telephone1'] ne ''">{cnt['telephone1']}</attr>
                    <attr name="telephone2" if="cnt['telephone2'].isSet and cnt['telephone2'] ne ''">{cnt['telephone2']}</attr>
                    <attr name="telephone3" if="cnt['telephone3'].isSet and cnt['telephone3'] ne ''" >{cnt['telephone3']}</attr>
                    <attr name="websiteurl" if="cnt['websiteurl'].isSet and cnt['websiteurl'] ne ''">{cnt['websiteurl']}</attr>
                    <attr name="statecode">{1}</attr>
                    <attr name="statuscode">{2}</attr>
                </create>
            </sandbox>
        </for>
    </transaction>
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

#### Table 17. Two operands operation types.
| Name | Description                                                                                          |
|------|------------------------------------------------------------------------------------------------------|
| eq   | Evaluates to true if an attribute has a value that is equal to the condition value.                  |
| ne   | Evaluates to true if an attribute has a value that is not equal to the condition value.              |
| lt   | Evaluates to true if the attribute has a value that is less than the condition value.                |
| le   | Evaluates to true if the attribute has a value that is less than or equal to the condition value.    |
| gt   | Evaluates to true if the attribute has a value that is greater than the condition value.             |
| ge   | Evaluates to true if the attribute has a value that is greater than or equal to the condition value. |



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

Contains condition *for Exchange server* doesn't support the **"%"** symbol - condition evaluates to true if the supplied constant value is contained in the property text value. Search is *case insensitive*.

The following script snippet returns all contacts, which first name starts with "J" and mobile phone number containing "5544".
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

The following script snippet returns identifiers of all CRM contacts that first name is not equal to "Joe" and DoNotPhone or DoNotEmail properties are not equal to true.

```
<select from="crmserver" entity="contact" var="contacts">
     <where>
        <not>
           <and>
              <condition attr="firstname" op="eq">Joe</condition>
              <or>
                 <condition attr="donotphone" op="eq">{true}</condition>
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

The following script snippet returns all contacts, which first name starts with "J" and mobile phone number starts with "5544".
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

The following script snippet returns all contacts, which first name ends with "J" and mobile phone number ends with "5544".
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

The following script snippet returns all identifiers of CRM contacts which first name is equal to Joe or DoNotPhone or DoNotEmail properties are equal to true.
```
<select from="crmserver" entity="contact" var="contacts">
     <where>
        <and>
          <condition attr="firstname" op="eq">Joe</condition>
          <or>
            <condition attr="donotphone" op="eq">{true}</condition>
            <condition attr="donotemail" op="eq">{true}</condition>
          </or>
         </and>
    </where>
    <attr name="contactid"/>
</select>
```

## Exists Condition Operator

**Exists** condition returns true if a specified attribute exists in an entity record. It does not matter whether the property contains a non-empty value or not.

Let us imagine that there is a custom property called ShoeSize that was added to some contacts but others do not have this field.

The following script snippet returns all contacts with this field:

```
<select from="exchange" entity="contact" var="contacts_with_ShoeSize">
    <where>
         <condition attr="ShoeSize" op="ex" />
    </where>
    <attr name="EntryId"/>
</select>
```

The following scrip snippet returns all contacts without this field:

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

The following script snippet return all identifiers of CRM contacts, which first name is Bob, Joe, or Michael.
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

The following two script snippets return all identifiers of CRM contacts, which were created in 2011.
```
<set var="dates">{[Utils.Now.AddDays(-100), Utils.Now.AddDays(-50)]}</set>
<select from="crm" entity="contact" var="contacts">
      <where>
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

**Exception** operation is used when it is necessary to throw an exception in the process of script execution.
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
     </if>
</for>
```

**OnError** operation is used in conjunction with **Exception** for handling specific exceptions.
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

The following script looks for CRM contact "Joe Dow" and if a single instance of this contact found, then this contact is set as a primary contact of "Adidas" company. Logging is enabled for the sandbox.
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
   </if>
</sandbox>
```

# STRUCTURAL OPERATIONS

## Include

**Include** operation is used if you want to separate some code into other script files. Sync360 will combine the main script and code from all includes on the moment of execution. This operation has the **"name"** attribute which value should be a path to existing script in regard to scripts folder plus filename without ".xml" extension.

**NOTE: Sync360 IDE** has special order where to find script. First it tries to find specified path in a folder where a current script is situated. If the script is not found it will check Script folder in current Application folder. If after that the script is also not found it will check the standard Script folder in main Sync360 folder.
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
<log>Contacts to be considered: {contacts.Count}</log>
```

## Call script

**Call** operation is used when it is necessary to execute another script (that already exists) before current script. Rather than include operation, the script will be executed in own context, and it will not have access to variables in main script. The Call operator supports passing parameters into the script. Operator searches for script in **@private** folder. There are two ways to specify call operator. *Examples:*

Call script "MyScriptHelper" and pass parameter "CallSettings"
```
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

## Script

**Script** element is used for code blocks grouping.

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

