# SmartSharp_FlexData
A data collection of SmartSharp project. Store and manipulate data using flex type of data and optimized SQL querys

# SmartSharp Framework Library

## FlexData Component 

### Sumary:
1. [About tool](#welcome)
2. [Publics properties](#PublicsProperties)
3. [ADD Methods](#ADDMethods)
4. [FlexAddOptions](#FlexAddOptions)
5. [GET Properties](#get-properties-methods)

Welcome to SmartSharp FlexData component and development tests.
The objective of this componente is give to your C# project a flex data container:

Store any data in rows and columns with a free organization. You can put various rows collections with different designs identifying this rows groups by the column "table id" of the each row. 
SmartSharp Flex Data use **List<Dictionary<string, Dynamic>>** object, so it is a rows collection and not substitute simple Dicitonary to store unitary data. Becouse it, the **ADD** method just add complete rows to collection.
<br>

### PublicsProperties:

Property          | Definition
------------------|-----------------------------
AffectedColumns   | Count of columns affected by filter or methods
AffectedRows	  | Count of rows affected by filter or methods
ColumnsNames      | Dictionary with table name to key and value with string with all columns names of it
Exception         | Exception message raised by any method or property
ExceptionNumber   | Exception number of message
FlexTableField    | The name of column with ID table name in collection it is a reference to identify especific group of rows
FlexRowIndexField | The name of column with index row
LastAddRow        | The last rows addictioned in main collection 
QueryMain         | Last query string used in last executed filter
Result			  | Collection result of filter
<br>

### ADDMethods
ADD methods by simples Keypair (key, value), row string ( **using "SELECT" SQL format statment** ) and group of rows in a string separed by ";" :

**Exemple 01:** 
**First row:**   tableid='users', nick='john', age=20, status=true 
**Second row:**  tableid='client', name='Mary Stwart', birthdate=#1970-04-11

The second row have differents columns of the first row, but the 'tabid' field identify the group of each row belongs. <br>
**Exemple 02**: 
**First row:**   tableid='users', nick='john', age=20, status=true 
**Second row:**	 tableid='users', nick='jack', birthdate=#2000-11-21, status=true
**Third row:**   tableid='client', name='Mary Stwart', birthdate=#1970-04-11 

In exemple 2, a exception is raised because the default design in table named 'user' was defined by first row with this same table id.

**Exemple 03**: 
users: nick='john', age=20, status=true;   
nick='jack', birthdate=#2000-11-21, status=true;  
client: name='Mary Stwart', birthdate=#1970-04-11 

See in exemple 3 that you can inform the name of row collection ("tableID") starting row with it separating it of columns clause by ":".
The syntax:
"**table_name:** column01=value, column02=value" 
is same of:
 "**tableid=table_name**, column01=value, column02-value"
~~~CSharp
//Add 3 rows to a 'login' table.
//You only need inform the tableID (collectin rows) in first row

dic = new();
if (!dic.Add(@"login:nick='lfernando', email='fernando@gmail.com', type='adm', active=true;
                     nick='sandra', email='sandra@gmail.com', type='user', active=false;
                     nick='jasanildo', email='jasax@gmail.com', type='user', active=true"))
{
	 Console.WriteLine(dic.Exception);
}
~~~
~~~CSharp
//Add news rows with more than one tables id(row collections)
//Add detached new row

 dic = new();
 if (!dic.Add(@"login:nick='lfernando', email='fernando@gmail.com', type='adm', active=true;
                      nick='sandra', email='sandra@gmail.com', type='user', active=false;
                      nick='jasanildo', email='jasax@gmail.com', type='user', active=true
               client:cod=0, name='José Silva', age=22, city='Brasilia'"))
 { 
	 Console.WriteLine(dic.Exception); 
 }

if (!dic.Add(@"cliente: #cod=0, name='José Silva', age=22, city='Brasilia';
			   cod=1, name='John Doe', age=44, city='New York'"))
{ 
    Console.WriteLine(dic.Exception); 
}
~~~

See in add row second clause the **"cod"** column. It start with **'#'** char to indicate a primary key column.
Only need inform this in a **first add** method with **this tableid** and just in **first row** of a tableid, in other words, if the primary key of a respective tableid already was informed in previous added row, that column always be a primary key of that respective tableid.

#### FlexAddOptions is a options to add existing row of respective tableid:
 1. **UpdateIfExistsID**: If exists a row with same primary key, it is updated by new informed row.  **Important**: If new row not have same columns of target row, it raised a exception.
 3. **IgnoreIfExists**: If exists a row with same primary key, the add command is ignored and that row not will be add.

~~~CSharp
if (!dic.Add(@"cliente:cod=0, name='José Silva', age=22, city='Brasilia'", FlexAddOptions.UpdateIfExistsID))
{ 
    Console.WriteLine(dic.Exception); 
}
~~~

### GET Properties Methods:

Flex data still offers various GET data functions returning filtered results based by SQL syntax querys. 

~~~CSharp
DataFlex data = new()
~~~

Methods	          	     | Definition
-------------------------|-----------------------------
data["*"]		  	     | [Load in RESUL property all rows with all columns in all tables](#GetAllRows)
data["from login"] 	     | [Load in RESULT property all rows from respective table key](#GetAllRowsFromTable)
data["select nome, idade"]| [Return only rows that contain all columns of the "SELECT" clause](GetLiteralAllRowsFilteredColumns)
data["SELECT name WHERE idade=40"]| [Load in RESULT rows by SQL query](GetValuesByColumnANDValue)
data["WHERE idade=40"]| [Return only rows result of WHERE query](GetAllRowsFilteredByWhere)
data[1]  | [Load in RESULT property all rows from respective table index](GetAllRowsFromTableIndex)
data[1, 1] | [Load in RESULT property all rows from respective table index and row index](GetAllRowsFromTableIndexAndRowIndex)
data[1, 1, 1] | [Load in RESULT property all rows from respective table index and row index and column index](GetAllRowsFromTableIndexAndRowIndexColIndex)

### GET property and filters. Usability and methods exemple. 

# GetAllRows <br>
~~~CSharp
//Load in RESULT property and show in console all rows with all columns in all tables

if (dic["*"] !=null)
{
	Console.WriteLine(dic.Exception);
}
else
{
    foreach (var row in dic.Result)
    {
        Console.WriteLine();

        foreach (var col in row)
       {
            Console.WriteLine($"{col.Key} - {col.Value}");
       }
    }
}
~~~
~~~CSharp
//ORDERBY by TABLE ID
//The @table use table name informed in "FlexTableField" property

if (dic["* ORDERBY @table"] !=null)
{
     Console.WriteLine(dic.Exception);
     Assert.Fail();
}
else
{
     foreach (var row in dic.Result)
	 {
		Console.WriteLine();

		foreach (var col in row)
		{
		   Console.WriteLine($"{col.Key} - {col.Value}");
		}           
    }
}
~~~

# GetAllRowsFromTable

# GetLiteralAllRowsFilteredColumns

# GetValuesByColumnANDValue

# Get_AllRowsFilteredByWhere

# GetAllRowsFromTableIndex

# Get_AllRowsFromTableIndexAndRowIndex

# GetAllRowsFromTableIndexAndRowIndexColIndex
