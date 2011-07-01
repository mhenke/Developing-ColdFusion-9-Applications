## Chapter 4: The CFML Programming Language

### Elements of CFML

The basic elements of CFML, including tags, functions, constants, variables, expressions, and CFScript, make it a
powerful tool for developing interactive web applications.

### CFML Basics

CFML is a dynamic application development tool with many of the features of a programming language. 

These features include functions, expressions, variables and constants, and flow-control constructs, such as if-then and loops.

CFML also has a "language within a language," CFScript, which enables you to use a syntax like JavaScript for many operations.

These elements and other basic CFML entities such as comments, data types, escape characters, and reserved words, let you create complex applications.

### Comments

ColdFusion comments have a similar format to HTML comments. However, they use three dash characters instead of two; for example:

```cfm
<!--- This is a ColdFusion Comment. Browsers do not receive it. --->
```

The ColdFusion server removes all ColdFusion comments from the page before returning it to the web server. As a result, the page that a browser receives does not include the comment. Users cannot see the comment even if they view the page source.

You can embed CFML comments in begin tags (not just tag bodies), functions calls, and variable text in number signs. ColdFusion ignores the text in comments such as the following:

```cfm
<cfset MyVar = var1 <!--- & var2 --->>
<cfoutput>#Dateformat(now() <!---, "dddd, mmmm yyyy" --->)#</cfoutput>
```

This technique can be useful if you want to temporarily comment out parts of expressions or optional attributes or arguments.

You can also nest comments, as the following example shows:

```cfm
 <!--- disable this code
<!--- display error message --->
<cfset errormessage1="Oops!">
<cfoutput>
#errormessage1#
</cfoutput>
--->
```

This nesting is useful if you want to temporarily disable a section of code while you test your application.

You can embed comments within comments, however, use this technique carefully.

Note: You cannot embed comments inside a tag name or function name, such as 

```cfm
<cf_My<!--- New --->CustomTag>.
```

You also cannot embed comments inside strings, as in the following example: 

```cfm
IsDefined("My<!--- New --->Variable").
```

### Tags

ColdFusion tags tell the ColdFusion server that it must process information. The ColdFusion server only processes tag contents; it returns text outside ColdFusion to the web server unchanged. ColdFusion provides a wide variety of built-in tags and lets you create custom tags.

### Tag syntax

ColdFusion tags have the same format as HTML tags. They are enclosed in angle brackets (< and >) and can have zero or more named attributes. Many ColdFusion tags have bodies; that is, they have beginning and end tags with text for processing between them. For example:

```cfm
<cfoutput>
 Hello #YourName#! <br>
</cfoutput>
```

Other tags, such as cfset and cfhttp, never have bodies. All the required information goes between the beginning (<) character and the ending (>) character, as in the following example:

```cfm
<cfset YourName="Bob">
```

Note: The cfset tag differs from other tags in that it does not have a body or arguments. Instead, the tag encloses an assignment statement that assigns a value to a variable. The cfset tag can also call a function without assigning a value to a result variable.

Sometimes, although the tag can have a body, it is unnecessary because the attributes specify all the required information. You can omit the end tag and place a forward slash character before the closing (>) character, as in the following example:

```cfm
<cfprocessingdirective pageencoding="euc-jp" />
```

In most cases, you specify tag attributes directly in the tag using the format attributeName=" attributeValue" , as the preceding example shows. However, as an alternative, you can place all the attributes in a structure and specify the structure in a single attributeCollection attribute, using the following format:

```cfm
<tagname attributeCollection="#structureName#">
```

When you use this format for all built-in ColdFusion tags except cfmodule, the tag must have only the attributeCollection attribute. This format is useful when you use dynamic arguments, where the number and values of the arguments to a tag can vary based on processing results. The following example shows this usage:

```cfm
 <!--- Configure dynamic attribute variables. --->
      <cfparam name="theURL" default="http://www.adobe.com">
      <cfparam name="resolveURL" default="yes">

<!--- Code that dynamically changes values for attributes can go here. --->


<!--- Create an arguments structure using variables. --->
      <cfset myArgs=StructNew()>
      <cfset myArgs.url="#theURL#">
<!--- Include a user name and password only if they are available. --->
      <cfif IsDefined("username")>
      <cfset myArgs.username="#username#">
</cfif>
<cfif IsDefined("password")>
      <cfset myArgs.password="#password#">
</cfif>
<cfset myArgs.resolveURL="#resolveURL#">
<cfset myArgs.timeout="2">


<!--- Use the myArgs structure to specify the cfhttp tag attributes. --->
      <cfhttp attributeCollection="#myArgs#">
      <cfoutput>
            #cfhttp.fileContent#
      </cfoutput>
```

Note: The attributeCollection attribute used in the cfmodule tag and when calling custom tags directly is different from the attributeCollection attribute for all other tags. In the cfmodule tag and in custom tags, you can mix the attributeCollection attribute and explicit custom tag attributes. Also, in the cfmodule tag, the attributeCollection attribute cannot contain the name and template attributes. Specify these attributes directly in the cfmodule tag.

You can use the attributeCollection attribute in all tags except the following:

cfargument                  cfelseif                     cflogout                    cfset

 cfbreak                     cffunction                   cfloop                      cfsilent

 cfcase                      cfif                         cfparam                     cfswitch

 cfcatch                     cfimport                     cfprocessingdirective       cftry

 cfcomponent                 cfinterface                  cfproperty

 cfdefaultcase               cflogin                      cfrethrow

 cfelse                      cfloginuser                  cfreturn

### Built-in tags

Built-in tags make up the heart of ColdFusion. These tags have many uses, including the following:

* Manipulating variables
* Creating interactive forms
* Accessing and manipulating databases
* Displaying data
* Controlling the flow of execution on the ColdFusion page
* Handling errors
* Processing ColdFusion pages
* Managing the CFML application framework
* Manipulating files and directories
* Using external tools and objects, including Verity collections, COM, Java, and CORBA objects, and executable programs
* Using protocols, such as mail, http, ftp, and pop

The CFML Reference documents each tag in detail.

### Custom tags

ColdFusion lets you create custom tags. You can create two types of custom tags:

* CFML custom tags that are ColdFusion pages
* CFX tags that you write in a programing language such as Java or C++

Custom tags can encapsulate frequently used business logic or display code. These tags enable you to place frequently used code in one place and call it from many places. Custom tags also let you abstract complex logic into a single, simple interface. They provide an easy way to distribute your code to others. You can even distribute encrypted versions of the tags to prevent access to the tag logic.

You can access a variety of free and commercial custom tags on the Adobe ColdFusion Exchange
(www.adobe.com/go/learn_cfu_cfdevcenter_en). They perform tasks ranging from checking if Cookies and JavaScript are enabled on the client browser to moving items from one list box to another. Many of these tags are free and include source code.

### CFML custom tags

When you write a custom tag in CFML, you can take advantage of all the features of the ColdFusion language, including all built-in tags and even other custom tags. CFML custom tags can include body sections and end tags.

Because they are written in CFML, you do not need to know a programming language such as Java. CFML custom tags provide more capabilities than user-defined functions, but are less efficient.

For more information on CFML custom tags, see "Creating and Using Custom CFML Tags" on page 208. For
information about, and comparisons among, ways to reuse ColdFusion code, including CFML custom tags, user- defined functions, and CFX tags, see "Creating ColdFusion Elements" on page 146.

### CFX Tags

CFX tags are ColdFusion custom tags that you write in a programming language such as Java or C++. These tags can take full advantage of all the tools and resources provided by these languages, including their access to runtime environments.

CFX tags also generally execute faster than CFML custom tags because they are compiled. CFX tags can be cross-platform, but are often platform-specific, for example if they take advantage of COM objects or the Windows API.

For more information on CFX tags, see "Building Custom CFXAPI Tags" on page 224.

### Tags as functions and operators

ColdFusion provides many functions or operator language elements that correspond to CFML tags. Together with the existing CFScript language, these elements let you define many CFCs and functions entirely in CFScript.

The new functions and operators belong to the following tag categories:

* Tags without bodies, such as cfexit and cfinclude
* Language tags with bodies, such as cflock and cftransaction
* Service tags with bodies, such as cfmail and cfquery
* Tags for defining and using components and functions: cfcomponent, cfinterface, cfimport, cffunction, cfproperty, cfargument. For more information, see "Defining components and functions in CFScript" on page 121.

### Tag without bodies
Several basic ColdFusion tags now have corresponding CFScript operators. These operators take a subset of standard tag attributes, and do not allow custom attributes. They do not return values.

The following list specifies the CFML tags and their corresponding CFScript syntax:

* cfabort: abort ["message"];
* cfexit: exit ["methodName"];
* cfinclude: include "template";
* cfparam: param [type] name [=defaultValue];

The param attribute can now take any number of name=value pairs. Param can also take all the attributes of <cfparam> as name-value pairs.

For example:

```cfm
  <cfscript>
   param name="paramname" default="value" min="minvalue" max="maxvalue" pattern="pattern"
   </cfscript>
```

* cfrethrow: rethrow;
* cfthrow: throw "message";

For detailed information on the statement parameters, see the corresponding tag attribute description in the CFML Reference.

Language-level tags with bodies ColdFusion includes CFScript elements that provide the functions of the following language (compiler)-level tags, which have bodies. These tags manage the execution of code within their bodies:

* cflock: lock
* cfthread: thread
* cftransaction: transaction

Thread and transaction support also include functions, such as threadJoin and transactionCommit, that let you manage any thread or transaction, whether you define it with a tag or a function.

The lock, thread, and transaction operations have the following syntax:

operationName attributeName1=value1 attributName2=value2...
{body contents }

### cflock

The lock operation has no special characteristics or limitations. All cflock tag attributes are valid operation parameters.

The following code uses the lock operation:

```cfm
lock scope = "request" timeout = "30" type = "Exclusive" {
request.number = 1;
writeoutput("E-Turtleneck has now sold "& request.number &"
turtlenecks!");
}
```


### cftransaction
To use the transaction operation you specify a begin action parameter. A transaction has the following general form:

```cfm
TRANSACTION action="begin" [isolation="isolationValue"] {
transaction code
}
```

Within the transaction block you call the following methods to manage the transaction:

* transactionCommit()
* transactionRollback([savepoint])
* transactionSetSavepoint([savepoint])

The savepoint parameter is a string identifier for the savepoint.

Note: You can also use theses methods in a cftransaction tag body.

You can nest transaction operations. For more information on nested transactions, see cftransaction in CFML Reference.

The following example uses nested transaction operations:

```cfm
<cfscript>
 qry = new Query();
 qry.setDatasource("test");
 qry.setSQL("delete from art where artid=62");
 qry.execute();
 TRANSACTION action="begin"
  {writeoutput("Transaction in cfscript test");
   TRANSACTION action="begin" {
   qry.setSQL("insert into art(artid, artistid, artname, description, issold, price)
   values ( 62, 1, 'art12', 'something', 1, 100)");
   qry.execute();}
 transactionSetSavepoint("sp01");
 qry.setSQL("update art set artname='art45' where artid=62");
 qry.execute();
 transactionSetSavepoint("sp02");
     qry.setSQL("update art set artname='art56' where artid=62");
     qry.execute();
     transactionrollback("sp02");
     transactioncommit();
     }
</cfscript>
```

### cfthread

To use the thread operation you specify a run action parameter. The thread runs the code in the operation body. A thread block has the following general form:

```cfm
THREAD name="text" [action="run"] [priority="priorityValue"
application-specific attributes] {
thread code
}
```

The code in the thread operation body executes in a single ColdFusion thread. Code outside the body is not part of the
thread. You can use the following methods to manage the thread:

* threadTerminate(threadName)

This function terminates the thread specified by the threadName parameter. It behaves in the same way as cfthread action="terminate".

* threadJoin([[threadName], timeout])

This function joins the current thread with the specified thread or threads. The current thread waits until either the specified threads complete, or the timeout period passes, whichever happens first. The current thread inside a thread function block belongs to that block thread and the current thread outside a thread function block is the page thread.The threadName parameter is a comma-delimited list specifying one or more threads to join with the page thread. If you omit this attribute, the current thread waits until all ColdFusion threads finish running. The timeout parameter specifies the maximum time, in milliseconds, the calling thread waits for the other threads to complete processing. If one or more threads do not complete before the time out period, the current thread processing begins immediately. If you omit this attribute, the current thread waits until all specified threads finish running.

Note: You can also use these functions with transactions that you create by using cftransaction tags.

### Service tags with bodies

ColdFusion provides objects, implemented as CFCs, that correspond to the following service tags:

* cfftp
* cfhttp
* cfmail
* cfpdf
* cfquery
* cfstoredproc

These tags have bodies and provide services such as executing queries or sending mail. Many of them have action attributes, whereas others have an implicit action, such as execute. For each service tag, except for cfmail and cfpdf, a component is returned with applicable properties set and you need to invoke getters on the properties to access the data.

Note: Previously, invoking getName() and getResult() methods returned data like query resultset, pdf object, or ftp prefix,
but now this has been changed and instead a component is returned with appropriate properties set.

The object names are the tag names without the cf prefix, for example, ftp. These objects also support child tag functionality, such as cfmailpart and cfmailparam.

Note: There may be thread-safety issues if implicit setters are used and child tags such as cfmailpart or cfmailparam are added because they get added into the CFC variable scope. It is therefore recommended that you create a new component for each service. If you need to preserve the attribute state, use duplicate() on the component to retain any initialized attribute values.

To use these tags in functions you:

1. Instantiate a service object.
2. Set object attributes and child tags
3. Execute one or more actions on the object.

Note: Unlike the corresponding tags, you cannot use application-specific parameters in these functions. You can only use the parameters that ColdFusion supports directly.

#### Step 1: Instantiate a service object

To create a function object, such as a mail object, use the new operator or createobject() function, as in the following example:

```cfm
myMail = new mail(server="sendmail.myCo.com");
```

#### Step 2a: Managing attributes

You can set attributes in several ways:

* As name=value parameters to the object initializer when you instantiate the object, as in the following example.

```cfm
   myMail = new mail(server="sendmail.myCo.com");
```

* As name=value parameters to an object action method, as in the following example:

```cfm
   Q = myQuery.execute(sql="select * from art");
```

* By using attribute setters, as in the following example:

```cfm
   myMail.setSubject("Hi");
```

Note: You cannot use a getAttributeName function to get the value of the attribute specified by AttributeName. Instead, use GetAttributes(AttributeName).

* By using the following functions:

```cfm
   SetAttributes(attrib1=value,attrib2=value,...);
   GetAttributes([attribName1[,attribName2]]....);
   ClearAttributes([attribName1[,attribName2]]...);
```

Note: If you specify a result attribute for a stored procedure, then calling getPrefix() returns,
executionTime,statusCode,cached . If you do not specify a result attribute, getPrefix() returns only
executionTime and statusCode.

#### Step 2b: Managing child tag operations
All service objects correspond to tags that have child tags. For example, cfmail has cfmailpart and cfmailparam child tags.

To specify the child tag functionality, use the following methods:

* httpObj.addParam
* mailObj.addParam
* mailObj.addPart
* pdfObj.addParam
* queryObj.addParam
* storedProcObj.addParam
* storedProcObj.addProcResult

For example:

```cfm
mailObj.addparam(file="#ExpandPath('test.txt')#");
mailObj.addPart(name="foo",type="html",charset="utf-8",
body="This is a test message.");
```

You can also clear child tag settings by calling the following functions.

* httpObj.clearParams
* mailObj.clearParams
* mailObj.clearParts
* pdfObj.clearParams
* queryObj.clearParams
* storedProcObj.clearParams
* storedProcObj.clearProcResults

If you used multiple add methods on an object, the clear method clears all values set in all the methods.

#### Step 3: Executing service actions

Service tags, excluding cfmail and cfpdf, have one or more actions that return results. Some, including the cfpdf and cfftp tags have action attributes. For these tags, each action corresponds to a method on the service object. For example, the ftp object action methods include open, close, listDir, getFile, rename, and many others. However, the way service tags return data has changed. Now, a component is returned with applicable properties set and you need to invoke getters on the properties to access the data.

Note: The PDF object has two action methods whose names differ from the corresponding cfftp action attribute values:
getPDFInfo and setPDFInfo instead of getInfo and setInfo. This difference is required to prevent name collisions
with the set and get methods for the PDF info attribute.

The cfhttp, cfmail, cfquery, and cfstoredproc tags do not have action attributes. Instead, the tags perform a single action. To perform these actions in cfscript, call the following functions:

* httpObj.send()
* mailObj.send()
* queryObj.execute()
* storedProcObj.execute()

To get an action result, you typically assign the results of the action method to a variable, as in the following example:

```cfm
Q = qry.execute(sql="select * from art");
```

Note: The attributes that specify you for an action are valid only for that action and are cleared once the action is completed.

Service code example: mail, ftp, and http

The following example shows the use of the mail, http, and ftp services in cfscript.

```cfm
<!---mail and ftp service --->
<cfscript>
     m = new mail();
<!---mail service --->
     m.setTo("x@adobe.com");
<!---set attribute using implicit setter --->
     m.setSubject("Hi");
     m.setBody("test mail");
<!---users need to use 'body' to specify cfmail and cfmailpart content --->
     m.addparam(file="#E xpandPath('test.txt')#");
<!---add cfmail param tags --->
     m.addPart(type="html",charset="utf-8",body="some
     mailpart content");
<!---add cfmailpart tags --->
     m.send(to="y@abc.com" ....);
<!---attributes can be overriden when sending mail --->
     m.clear();
<!---clearAttributes(),clearParams() and clearParts() can also be used to clear --->
     individual items, if needed
<!---ftp service --->
     f = new ftp(server="s",username="u",password="p");
<!---check if a specified directory already exists (note the usage of getPrefix ())--->
     f.existsDir(directory ="some_dir").getPrefix().returnValue ? WriteOutput("Directory
     exists"):WriteOutput("Directory does not exist");
     <!---list directory contents (note the usage of getResult() and getPrefix() --->
     r = f.listDir(directory="some_dir",name="dirContents");
     dirContents = r.getResult();
     r.getPrefix().succeeded ? WriteOutput("List Directory operation successful") :
</cfscript>
<!---http service --->
<cfscript>
     httpObj = new http();
     <!---example 1 --->
         <!---add params--->
         httpObj.addParam(type="cgi", Name="Content-type", value =
         "application/x-www-form-urlencoded",encoded="no");
         httpObj.addParam(type="body",value="test1=value1&test2=
         value2&arraytest=value1&arraytest=value2");
     <!---assign the component returned to a variable--->
         r = httpObj.send(url="http://localhost:8500/
         project1/cfscript_test_files/thread-
         safe/http/_cfhttpparam_body.cfm",method="POST");
     <!---use getPrefix() to dump the cfhttp prefix --->
         writedump(r.getPrefix());
     <!---example 2 --->


     <!---using attributes that return a query --->
         r = httpObj.send(url="
         http://localhost:8500/language_enhancements_2/cfscript_test_files/thread-
         safe/http/vamsee.txt")",name="myqry", firstrowasheaders="no",method="GET");
     <!---dump result and name attributes data --->
         writedump(r.getPrefix());
         writedump(r.getResult());
</cfscript>
```

For cfftp, following are available getters on the returned component:

* getPrefix()
** Returns the tag prefix cfftp, which is a struct, available after any cfftp operation
* getResult()
** Applicable only to action="listDir"

For cfhttp, following are the available getters on the returned component:

* getPrefix()
** Returns the cfhttp prefix (struct) available after the tag has executed
* getResult()
** Applicable only if attributes like columns, delimiter, firstrowasheaders, name, or textQualifier are specified, which direct ColdFusion to return a query object.

Query service example

```cfm
<cfscript>
     qryObj = new createObject("component","com.adobe.coldfuison.query").init();
     <!---r here is no longer the query recordset but a component --->
     r = qryObj.execute(sql="select * from art",
     datasource="cfdocexamples",result="myresult",name="myquery");
     <!---new way to access the data --->
     resultset =r.getResult();
     prefixData = r.getPrefix();
     writedump(resultset);
     writedump(prefixData);
     <!---Using QoQ--->
     qryObj.setAttributes(myquery=resultset);
     r = qryObj.execute(sql="select * from myquery", dbtype="query");
     writedump(r.getResult());
     writedump(r.getPrefix());
</cfscript>
```

The following are the available getters on the returned component:

* getPrefix()
** Returns the result struct available after the query has executed.
* getResult()
**  Returns the resultset returned by query (SELECT query) and throws an error for other types of SQL statements or queries (like INSERT, UPDATE, DELETE).


PDF example

Whenever any action is performed for which a name attribute is specified, the new pdf is returned back to the user.

The following code shows typical actions on a PDF.

```cfm
<cfscript>
     pdfObj = new pdf();
     x = pdfObj.read(source=#sourcefile#, name="PDFInfo");
     x = pdfObj.processddx(ddxfile="#tocddx#",inputfiles="#inputStruct#",outputfiles=
         "#outputStruct#",name="ddxVar");
     x = pdfObj.addWatermark(source="#pdf1#",image="#image1#", pages="1",
         overwrite="yes", name="test2");
     x = pdfObj.removewatermark(source="#pdf1#", name="temp");
     x = pdfObj.deletePages(source="#destfolder#dest.pdf",pages="2-4", name="deltest");
     pdfObj.addparam(source="#pdf1#", pages="1-2,4");
     pdfObj.merge(destination="#destfolder#merge-oneBigFile-5.pdf", overwrite="yes");
     pdfObj.thumbnail(source="#pdf1#", overwrite="yes");
     pdfObj.setInfo(source="#pdf1#", info="#{Author="Donald Duck"}#",
                        destination="#destfolder#pdfinfo.pdf", overwrite="yes");
     pdfObj.write(source="myBook", destination="#destfolder#write1.pdf", version="1.4",
                   overwrite="yes");
     pdfObj.protect(source="MyPdfVar", password="adobe", permissions="none",
                        newuserpassword="newuserpw", newownerpassword="newownerpw");
</cfscript>

Storedproc example
The following code shows sample usage of the storedproc service object.

```cfm
<cfscript>
     sp = new storedproc();
     <!---add cfprocparam tags --->
         sp.addParam(TYPE = "IN", CFSQLTYPE="CF_SQL_VARCHAR", VALUE="David",
         DBVARNAME="@firstname");
         sp.addParam(TYPE="IN", CFSQLTYPE="CF_SQL_VARCHAR", VALUE="Peterson",
         DBVARNAME="@lastname", null ="yes");
         sp.add Param(TYPE="OUT", CFSQLTYPE="CF_SQL_INTEGER", variable="MyCount",
         DBVARN AME="@MyCount");
     <!---add cfprocresult        tags --->
         sp.addProcResult(NAME = "home r", RESULTSET = 1);
         sp.addProcResult( NAME = "home r2", RESULTSET = 2);
         sp.addProcResult(NAME = "home r3", RESULTSET = 3) ;
     <!---execute stored proc--->
         r = sp.execute(procedure="sp_weird",datasource="some_dsn",result="r");
         writedump(r.getProcResultSets());
     <!---changed from sp.getProcResults()--->
         writedump(r.getProcResultSets ("home r3"));
         writedump(r.getPrefix());
     <!---changed from sp.getResult()--->
         writedump(r.getProcOutVariables());
     <!---changed from sp.getProcVars()--->
</cfscript>
```

The following are the available getters on the returned component:

* getPrefix()

  Returns the cfstoredproc prefix (struct) available after the procedure has executed.

* getProcResultsets()

  Returns any resultsets returned by the strored procedure.

* getProcOutVariables()



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  29
The CFML Programming Language




   Returns any OUT or INOUT variables set by the procedure.


Functions
Functions typically manipulate data and return a result. You can also create user-defined functions (UDFs), sometimes
referred to as custom functions.

Functions have the following general form:

functionName([argument1[, argument2]]...)

Some functions, such as the Now function take no arguments. Other functions require one or more comma-separated
arguments and can have additional optional arguments. All ColdFusion functions return a value. For example,
Round(3.14159) returns the value 3.


Built-in functions
ColdFusion built-in functions perform a variety of tasks, including, but not limited to, the following:

* Creating and manipulating complex data variables, such as arrays, lists, and structures

* Creating and manipulating queries

* Creating, analyzing, manipulating, and formatting strings and date and time values

* Evaluating the values of dynamic data

* Determining the type of a variable value

* Converting data between formats

* Performing mathematical operations

* Getting system information and resources

For alphabetical and categorized lists of ColdFusion functions, see ColdFusion Functions in the CFML Reference.

You use built-in functions throughout ColdFusion pages. Built-in functions are frequently used in a cfset or
cfoutput tag to prepare data for display or further use. For example, the following line displays today's date in the
format October 24, 2007:

<cfoutput>#DateFormat(Now(), "mmmm d, yyyy")#</cfoutput>

This code uses two nested functions. The Now function returns a ColdFusion date-time value representing the current
date and time. The DateFormat function takes the value returned by the Now function and converts it to the desired
string representation.

Functions are also valuable in CFScript scripts. ColdFusion does not support ColdFusion tags in CFScript, so you must
use functions to access ColdFusion functionality in scripts.


Implicit Get and Set Functions
ColdFusion components support private properties with public setter and getter methods. This behavior supports
object-oriented programming by letting you hide component properties from direct access.

By default, properties that you specify by using the <cfproperty> tag have implicit setPropertyName and
getPropertyName methods that access the PropertyName property in the CFC variables scope.

Use the following code, for example, to set and get the MyProp property of myCFC component:

myCFC.setMyProp(27);
theProp = myCFC.getMyProp();




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    30
The CFML Programming Language




Features of properties with setter and getter methods include the following:

* Component properties you assign with the set method are in the Variables scope that is private to the CFC. You
   can get or reset the properties only by calling get or set methods.

* If a property has a type attribute value, ColdFusion validates the data you pass to the setter function. The default
   attribute has no effect on the property and does not set an initial property value.

* A direct assignment statement, such as myCFC.MyProp=27 creates a standard This scope variable in the CFC, even
   if you specify the property in a cfproperty tag. The This scope variable is independent of the properties that you
   access using the set and get methods. In fact, you can have a This scope variable with the same name as a property
   that you access using the set and get methods.

* Use the cfproperty tag getter and setter attributes to control access to a property from outside the CFC:

   A setter attribute value of true allows application code to set the property (the default behavior).

   A false value specifies that the property can only be set from within the CFC. The getter attribute works similarly.

* Explicit set or get methods override the implicit set and get methods. Therefore, if a CFC has a MyProp property
   with an explicit setMyProp method, and you call the setMyProp() function in your application code, ColdFusion
   uses your function and not an implicit function.


Validate and validateparams attributes
The validate attribute available with <cfproperty> takes the validator to be used for validating data when implicit
setter for this property is called. It takes the following validators:

* string

* boolean

* integer

* numeric

* date

* time

* creditcard: A 13-16 digit number conforming to the mod10 algorithm.

* email: A valid e-mail address.

* eurodate: A date-time value. Any date part must be in the format dd/mm/yy. The format can use /, -, or . characters
   as delimiters.

* regex: Matches input against pattern specified in validateparams.

* ssn: A U.S. social security number.

* telephone: A standard U.S. telephone number.

* UUID: A Home Universally Unique Identifier, formatted 'XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXX',
   where 'X' is a hexadecimal number.

* guid: A Universally Unique Identifier of the form "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" where
   'X' is a hexadecimal number

* zipcode: U.S., 5- or 9-digit format ZIP codes

The validateparams attribute available with <cfproperty> takes the parameters required by the validator specified
in the validate attribute. This should be specified in the implicit struct notation.

* min: Minimum value if validate is integer/numeric/



                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    31
The CFML Programming Language




* max: Maximum value if the validate is integer/numeric/

* minLength: Minimum length of the string if the validate is string

* maxLength: Maximum length of the string if the validate is string

* pattern: regex expression if the validator specified in validate attribute is regex

For example, the following code sets the validators for e-mail, zipcode, and age of an employee.

Note: For age, validate checks if the value is an integer and validateparams checks the range of the value supplied.

<!---Setting validators for an employee's e-mail, age, and zipcode--->
<cfcomponent>
<cfproperty name="mail" validate="email">
<cfproperty name="zip" validate="zipcode">
<cfproperty name="age" validate="integer" validateparams="{min=18,max=60}"> </cfcomponent>


User-defined functions
You can write your own functions, user-defined functions (UDFs). You can use these functions in ColdFusion
expressions or in CFScript. You can call a user-defined function anywhere you can use a built-in CFML function. You
create UDFs using the cffunction tag or the cfscriptfunction statement. UDFs that you create using the
cffunction tag can include ColdFusion tags and functions. UDFs that you create in CFScript can only include
functions. You can create stand-alone UDFs or encapsulate them in a ColdFusion component.

User-defined functions let you encapsulate logic and operations that you use frequently in a single unit. This way, you
can write the code once and use it multiple times. UDFs ensure consistency of coding and enable you to structure your
CFML more efficiently.

Typical user-defined functions include mathematical routines, such as a function to calculate the logarithm of a
number; string manipulation routines, such as a function to convert a numeric monetary value to a string such as "two
dollars and three cents"; and can even include encryption and decryption routines.

Note: The Common Function Library Project at www.cflib.org includes a number of free libraries of user-defined
functions.

For more information on user-defined functions, see "Writing and Calling User-Defined Functions" on page 153.


ColdFusion components
ColdFusion components encapsulate multiple, related, functions. A ColdFusion component is essentially a set of
related user-defined functions and variables, with additional functionality to provide and control access to the
component contents. ColdFusion components can make their data private, so that it is available to all functions (also
called methods) in the component, but not to any application that uses the component.

ColdFusion components have the following features:

* They are designed to provide related services in a single unit.

* They can provide web services and make them available over the Internet.

* They can provide ColdFusion services that Flash clients can call directly.

* They have several features that are familiar to object-oriented programmers, including data hiding, inheritance,
   packages, and introspection.

For more information on ColdFusion components, see "Building and Using ColdFusion Components" on page 177.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     32
The CFML Programming Language




Constants
The value of a constant does not change during program execution. Constants are simple scalar values that you can use
within expressions and functions, such as "Robert Trent Jones" and 123.45. Constants can be integers, real numbers,
time and date values, Boolean values, or text strings. ColdFusion does not allow you to give names to constants.


Variables
Variables are the most frequently used operands in ColdFusion expressions. Variable values can be set and reset, and
can be passed as attributes to CFML tags. Variables can be passed as parameters to functions, and can replace most
constants.

ColdFusion has several built-in variables that provide information about the server and ColdFusion tags return. For a
list of the ColdFusion built-in variables, see Reserved Words and Variables in the CFML Reference.

The following two characteristics classify a variable:

* The scope of the variable, which indicates where the information is available and how long the variable persists

* The data type of the variable value, which indicates the type of information a variable represents, such as number,
    string, or date

See "Data types" on page 32 for a list of data types (which also apply to constant values). For detailed information on
ColdFusion variables, including data types, scopes, and their use, see "Using ColdFusion Variables" on page 38.


Expressions
ColdFusion expressions consist of operands and operators. Operands are constants and variables, such as "Hello" or
MyVariable. Operators, such as the string concatenation operator (&) or the division operator (/) are the verbs that act
on the operands. ColdFusion functions also act as operators.

The simplest expression consists of a single operand with no operators. Complex expressions consist of multiple
operands and operators. For example, the following statements are all ColdFusion expressions:

 12
MyVariable
(1 + 1)/2
"father" & "Mother"
Form.divisor/Form.dividend
Round(3.14159)

For detailed information on using variables, see "Using ColdFusion Variables" on page 38. For detailed information
on expressions and operators, see "Using Expressions and Number Signs" on page 64.


Data types
ColdFusion is considered typeless because you do not explicitly specify variable data types.

However, ColdFusion data, the constants and the data that variables represent, do have data types, which correspond
to the ways the data is stored on the computer.

ColdFusion data belongs to the following type categories:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        33
The CFML Programming Language




 Category               Description and types

 Simple                 Represents one value. You can use simple data types directly in ColdFusion expressions. ColdFusion simple data
                        types are:

                        * strings Asequenceofalphanumericcharactersenclosedinsingleordoublequotationmarks,suchas"Thisis
                           a test."

                        * integers A sequence of numbers written without quotation marks, such as 356.

                        * real numbers, such as -3.14159

                        * Boolean values Use True, Yes, or 1 for true and False, No, or 0 for false. Boolean values are not case sensitive.

                        * date-time values ColdFusion supports a variety of data formats. For more information, see "Date and time
                           formats" on page 44.

 Complex                A container for data. Complex variables generally represent more than one value. ColdFusion built-in complex
                        data types are:

                        * arrays

                        * structures

                        * queries

 Binary                 Raw data, such as the contents of a GIF file or an executable program file

 Object                 COM, CORBA, Java, web services, and ColdFusion Component objects: Complex objects that you create and
                        access using the cfobject tag and other specialized tags.


Note: ColdFusion does not have a data type for unlimited precision decimal numbers, but it can represent such numbers
as strings and provides a function that supports unlimited precision decimal arithmetic. For more information, see
PrecisionEvaluate in the CFML Reference.

For more information on ColdFusion data types, see "Using ColdFusion Variables" on page 38.


Flow control
ColdFusion provides several tags that let you control how a page gets executed. These tags generally correspond to
programming language flow control statements, such as if, then, and else. The following tags provide ColdFusion flow
control:


 Tags                                 Purpose

 cfif, cfelseif, cfelse               Select sections of code based on whether expressions are True or False.

 cfswitch, cfcase, cfdefaultcase      Select among sections of code based on the value of an expression. Case processing is not limited
                                      to True and False conditions.

 cfloop, cfbreak                      Loop through code based on any of the following values: entries in a list, keys in a structure or
                                      external object, entries in a query column, an index, or the value of a conditional expression.

 cfabort, cfexit                      End processing of a ColdFusion page or custom tag.


CFScript also provides a set of flow-control statements. For information on using flow-control statements in CFScript,
see "Extending ColdFusion Pages with CFML Scripting" on page 106. For more details on using flow-control tags, see
the reference pages for these tags in the CFML Reference.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    34
The CFML Programming Language




cfif, cfelseif, and cfelse
The cfif, cfelseif, and cfelse tags provide if-then-else conditional processing, as follows:

1 The cfif tag tests a condition and executes its body if the condition is True.

2 If the preceding cfif (or cfelseif) test condition is False, the cfelseif tag tests another condition and executes
   its body if that condition is True.

3 The cfelse tag can optionally follow a cfif tag and zero or more cfelseif tags. Its body executes if all the
   preceding tags' test conditions are False.

   The following example shows the use of the cfif, cfelseif, and cfelse tags. If the value of the type variable is
   "Date," the date displays; if the value is "Time," the time displays; otherwise, both the time and date display.

    <cfif type IS "Date">
         <cfoutput>#DateFormat(Now())#</cfoutput>
    <cfelseif type IS "Time">
         <cfoutput>#TimeFormat(Now())#</cfoutput>
    <cfelse>
         <cfoutput>#TimeFormat(Now())#, #DateFormat(Now())#</cfoutput>
    </cfif>


cfswitch, cfcase, and cfdefaultcase
The cfswitch, cfcase, and cfdefaultcase tags let you select among different code blocks based on the value of an
expression. ColdFusion processes these tags as follows:

1 The cfswitch tag evaluates an expression. The cfswitch tag body contains one or more cfcase tags and
   optionally includes cfdefaultcase tag.

2 Each cfcase tag in the cfswitch tag body specifies a value or set of values. If a value matches the value determined
   by the expression in the cfswitch tag, ColdFusion runs the code in the body of the cfcase tag and then exits the
   cfswitch tag. If two cfcase tags have the same condition, ColdFusion generates an error.

3 If none of the cfcase tags match the value determined by the cfswitch tag, and the cfswitch tag body includes
   a cfdefaultcase tag, ColdFusion runs the code in the cfdefaultcase tag body.

Note: Although the cfdefaultcase tag does not have to follow all cfcase tags, it is good programming practice to place
it at the end of the cfswitch statement.

The cfswitch tag provides better performance than a cfif tag with multiple cfelseif tags, and is easier to read. Switch
processing is commonly used when different actions are required based on a string variable such as a month or request
identifier.

The following example shows switch processing:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        35
The CFML Programming Language




<cfoutput query = "GetEmployees">
<cfswitch expression = #Department#>
      <cfcase value = "Sales">
               #FirstName# #LastName# is in <b>Sales</b><br><br>
      </cfcase>
      <cfcase value = "Accounting">
               #FirstName# #LastName# is in <b>Accounting</b><br><br>
      </cfcase>
      <cfcase value = "Administration">
               #FirstName# #LastName# is in <b>Administration</b><br><br>
      </cfcase>
      <cfdefaultcase>#FirstName# #LastName# is not in Sales,
               Accounting, or Administration.<br>
      </cfdefaultcase>
</cfswitch>
</cfoutput>


cfloop and cfbreak
The cfloop tag loops through the tag body zero or more times based on a condition specified by the tag attributes.
The cfbreak tag exits a cfloop tag.


cfloop
The cfloop tag provides the following types of loops:


Loop type                Description

Index                    Loops through the body of the tag and increments a counter variable by a specified amount after each loop until
                         the counter reaches a specified value.

Conditional              Checks a condition and runs the body of the tag if the condition is True.

Query                    Loops through the body of the tag once for each row in a query.

List, file, or array     Loops through the body of the tag once for each entry in a list, each line in a file, or each item in an array.

Collection               Loops through the body of the tag once for each key in a ColdFusion structure or item in a COM/DCOM object.


The following example shows a simple index loop:

<cfloop index = "LoopCount" from = 1 to = 5>
The loop index is <cfoutput>#LoopCount#</cfoutput>.<br>
</cfloop>

The following example shows a simple conditional loop. The code does the following:

1 Sets up a ten-element array with the word "kumquats" in the fourth entry.

2 Loops through the array until it encounters an array element containing "kumquats" or it reaches the end of the
   array.

3 Prints the value of the Boolean variable that indicates whether it found the word kumquats and the array index at
   which it exited the loop.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          36
The CFML Programming Language




    <cfset myArray = ArrayNew(1)>
   <!--- Use ArraySet to initialize the first ten elements to 123 --->
   <cfset ArraySet(myArray, 1, 10, 123)>
   <cfset myArray[4] = "kumquats">


   <cfset foundit = False>
   <cfset i = 0>
   <cfloop condition = "(NOT foundit) AND (i LT ArrayLen(myArray))">
        <cfset i = i + 1>
        <cfif myArray[i] IS "kumquats">
              <cfset foundit = True>
        </cfif>
   </cfloop>
   <cfoutput>
   i is #i#<br>
   foundit is #foundit#<br>
   </cfoutput>

Note: You can get an infinite conditional loop if you do not force an end condition. In this example, the loop is infinite if
you omit the <cfset i = i + 1> statement. To end an infinite loop, stop the ColdFusion application server.


cfbreak
The cfbreak tag exits the cfloop tag. You typically use it in a cfif tag to exit the loop if a particular condition occurs.
The following example shows the use of a cfbreak tag in a query loop:

 <cfloop query="fruitOrder">
     <cfif fruit IS "kumquat">
          <cfoutput>You cannot order kumquats!<br></cfoutput>
          <cfbreak>
     </cfif>
     <cfoutput>You have ordered #quantity# #fruit#.<br></cfoutput>
</cfloop>


cfabort and cfexit
The cfabort tag stops processing of the current page at the location of the cfabort tag. ColdFusion returns to the
user or calling tag everything that was processed before the cfabort tag. You can optionally specify an error message
to display. You can use the cfabort tag as the body of a cfif tag to stop processing a page when a condition, typically
an error, occurs.

The cfexit tag controls the processing of a custom tag, and can only be used in ColdFusion custom tags. For more
information see, "Terminating tag execution" on page 219 and the CFML Reference.


Character case
ColdFusion is not case sensitive. For example, the following all represent the cfset tag: cfset, CFSET, CFSet, and even
cfsEt. However, get in the habit of consistently using the same case rules in your programs; for example:

* Develop consistent rules for case use, and stick to them. If you use lowercase characters for some tag names, use
   them for all tag names.

* Always use the same case for a variable. For example, do not use both myvariable and MyVariable to represent the
   same variable on a page.

   Follow these rules to prevent errors on application pages where you use both CFML and case-sensitive languages,
   such as JavaScript.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      37
The CFML Programming Language




Special characters
The double-quotation marks ("), single-quotation mark ('), and number sign (#) characters have special meaning to
ColdFusion. To include any of them in a string, double the character; for example, use ## to represent a single #
character.

The need to escape the single- and double-quotation marks is context sensitive. Inside a double-quoted string, you do
not need to escape single-quotation mark (apostrophe) characters. Inside a single-quoted string, you do not escape
double-quotation mark characters.

The following example illustrates escaping special characters, including the use of mixed single- and double-quotation
marks:

<cfset mystring = "We all said ""Happy birthday to you.""">

<cfset mystring2 = 'Then we said "How old are you now?"'>
<cfoutput>
     #mystring#<br>
     #mystring2#<br>
     Here is a number sign: ##
</cfoutput>

The output looks as follows:

We all said "Happy birthday to you."
Then we said "How old are you now?"
Here is a number sign: #



Reserved words
As with any programming tool, you cannot use just any word or name for ColdFusion variables, UDFs and custom
tags. Avoid using any name that can be confused with a ColdFusion element. In some cases, if you use a word that
ColdFusion uses--for example, a built-in structure name--you can overwrite the ColdFusion data.

The following list indicates words you must not use for ColdFusion variables, user-defined function names, or custom
tag names. While some of these words can be used safely in some situations, you can prevent errors by avoiding them
entirely. For a complete list of reserved words, see the CFML Reference.

* Built-in function names, such as Now or Hash

* Scope names, such as Form or Session

* Any name starting with cf. However, when you call a CFML custom tag directly, you prefix the custom tag page
   name with cf_.

* Operators, such as NE or IS

* The names of any built-in data structures, such as Error or File

* The names of any built-in variables, such as RecordCount or CGI variable names

* CFScript language element names such as for, default, or continue

Also, do not create form field names ending in any of the following, except to specify a form field validation rule using
a hidden form field name. (For more information on form field validation, see "Introduction to Retrieving and
Formatting Data" on page 703.)

* _integer

* _float

* _range




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       38
The CFML Programming Language




* _date

* _time

* _eurodate

Because ColdFusion is not case-sensitive, all of the following are reserved words: IS, Is, iS, and is.


CFScript
CFScript is a language within a language. CFScript is a scripting language that is similar to JavaScript but is simpler to
use. Also, unlike JavaScript, CFScript only runs on the ColdFusion server; it does not run on the client system. A
CFScript script can use all ColdFusion functions and all ColdFusion variables that are available in the script's scope.

CFScript provides a compact and efficient way to write ColdFusion logic. Typical uses of CFScript include:

* Simplifying and speeding variable setting

* Building compact flow control structures

* Encapsulating business logic in user-defined functions

The following sample script populates an array and locates the first array entry that starts with the word "key". It shows
several of the elements of CFScript, including setting variables, loop structures, script code blocks, and function calls.
Also, the code uses a cfoutput tag to display its results. Although you can use CFScript for output, the cfoutput tag
is often easier to use.

 <cfscript>
strings = ArrayNew(1);
strings[1]="the";
strings[2]="key to our";
strings[4]="idea";
for( i=1 ; i LE 4 ; i = i+1 )
{
      if(Find("key",strings[i],1))
           break; }
</cfscript>
<cfoutput>Entry #i# starts with "key"</cfoutput><br>

You use CFScript to create user-defined functions.

For more information on CFScript, see "Extending ColdFusion Pages with CFML Scripting" on page 106. For more
information on user-defined functions, see "Writing and Calling User-Defined Functions" on page 153.



Using ColdFusion Variables

Adobe ColdFusion variables are the most frequently used operands in ColdFusion expressions. Variable values can be
set and reset, and can be passed as attributes to CFML tags. Variables can be passed as parameters to functions, and
can replace most constants.

To create and use ColdFusion variables, you should know the following:

* How variables can represent different types of data

* How the data types get converted

* How variables exist in different scopes

* How scopes are used



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   39
The CFML Programming Language




* How to use variables correctly


Creating variables
You create most ColdFusion variables by assigning them values. (You must use the ArrayNew function to create
arrays.) Most commonly, you create variables by using the cfset tag. You can also use the cfparam tag, and
assignment statements in CFScript. Tags that create data objects also create variables. For example, the cfquery tag
creates a query object variable.

ColdFusion automatically creates some variables that provide information about the results of certain tags or
operations. ColdFusion also automatically generates variables in certain scopes, such as Client and Server. For
information on these special variables, see Reserved Words and Variables in the CFML Reference and the
documentation of the CFML tags that create these variables.

ColdFusion generates an error when it tries to use a variable before it is created. This can happen, for example, when
processing data from an incompletely filled form. To prevent such errors, test for the variable's existence before you
use it. For more information on testing for variable existence, see "Ensuring variable existence" on page 61.

For more information on how to create variables, see "Creating and using variables in scopes" on page 58.


Variable naming rules
ColdFusion variable names, including form field names and custom function and ColdFusion component argument
names, must conform to Java naming rules and the following guidelines:

* A variable name must begin with a letter, underscore, or Unicode currency symbol.

* The initial character can by followed by any number of letters, numbers, underscore characters, and Unicode
   currency symbols.

* A variable name cannot contain spaces.

* A query result is a type of variable, so it overwrites a local variable with the same name.

* ColdFusion variables are not case sensitive. However, consistent capitalization makes the code easier to read.

* When creating a form with fields that are used in a query, match form field names with the corresponding database
   field names.

* Periods separate the components of structure or object names. They also separate a variable scope from the variable
   name. You cannot use periods in simple variable names, with the exception of variables in the Cookie and Client
   scopes. For more information on using periods, see "Using periods in variable references" on page 49.

The following rule applies to variable names, but does not apply to form field and argument names:

* Prefix each variable's name with its scope. Although some ColdFusion programmers do not use the Variables prefix
   for local variable names, use prefixes for all other scopes. Using scope prefixes makes variable names clearer and
   increases code efficiency. In many cases, you must prefix the scope. For more information, see "About scopes" on
   page 56.

   Note: In some cases, when you use an existing variable name, you must enclose it with number signs (#) to allow
   ColdFusion to distinguish it from string or HTML text, and to insert its value, as opposed to its name. For more
   information, see "Using number signs" on page 70.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      40
The CFML Programming Language




Variable characteristics
You can classify a variable using the following characteristics:

* The data type of the variable value, which indicates the kind of information a variable represents, such as number,
   string, or date

* The scope of the variable, which indicates where the information is available and how long the variable persists.


Data types
ColdFusion is often referred to as typeless because you do not assign types to variables and ColdFusion does not
associate a type with the variable name. However, the data that a variable represents does have a type, and the data type
affects how ColdFusion evaluates an expression or function argument. ColdFusion can automatically convert many
data types into others when it evaluates expressions. For simple data, such as numbers and strings, the data type is
unimportant until the variable is used in an expression or as a function argument.

ColdFusion variable data belongs to one of the following type categories:

Simple One value. Can use directly in ColdFusion expressions. Include numbers, strings, Boolean values, and date-
time values.

Binary Raw data, such as the contents of a GIF file or an executable program file.

Complex A container for data. Generally represent more than one value. ColdFusion built-in complex data types
include arrays, structures, queries, and XML document objects.

You cannot use a complex variable, such as an array, directly in a ColdFusion expression, but you can use simple data
type elements of a complex variable in an expression.

For example, with a one-dimensional array of numbers called myArray, you cannot use the expression myArray * 5.
However, you could use an expression myArray[3] * 5 to multiply the third element in the array by five.

Objects Complex constructs. Often encapsulate both data and functional operations. The following table lists the
types of objects that ColdFusion can use, and identifies the chapters that describe how to use them:


 Object type                                See

 Component Object Model (COM)               "Integrating COM and CORBA Objects in CFML Applications" on page 1170

 Common Object Request Broker Architecture  "Integrating COM and CORBA Objects in CFML Applications" on page 1170
 (CORBA)

 Java                                       "Integrating J2EE and Java Elements in CFML Applications" on page 1125

 ColdFusion component                       "Building and Using ColdFusion Components" on page 177

 Web service                                "Using Web Services" on page 1093



Data type notes
Although ColdFusion variables do not have types, it is often convenient to use "variable type" as a shorthand for the
type of data that the variable represents.

ColdFusion can validate the type of data contained in form fields and query parameters. For more information, see
"Testing for a variable's existence" on page 709 and "Using cfqueryparam" on page 416.

The cfdump tag displays the entire contents of a variable, including ColdFusion complex data structures. It is an
excellent tool for debugging complex data and the code that handles it.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      41
The CFML Programming Language




ColdFusion provides the following functions for identifying the data type of a variable:

* IsArray

* IsBinary

* IsBoolean

* IsImage

* IsNumericDate

* IsObject

* IsPDFObject

* IsQuery

* IsSimpleValue

* IsStruct

* IsXmlDoc

ColdFusion also includes the following functions for determining whether a string can be represented as or converted
to another data type:

* IsDate

* IsNumeric

* IsXML

ColdFusion does not use a null data type. However, if ColdFusion receives a null value from an external source such
as a database, a Java object, or some other mechanism, it maintains the null value until you use it as a simple value. At
that time, ColdFusion converts the null to an empty string (""). Also, you can use the JavaCast function in a call to a
Java object to convert a ColdFusion empty string to a Java null.


Numbers
ColdFusion supports integers and real numbers. You can intermix integers and real numbers in expressions; for
example, 1.2 + 3 evaluates to 4.2.


Integers
ColdFusion supports integers between -2,147,483,648 and 2,147,483,647 (32-bit signed integers). You can assign a
value outside this range to a variable, but ColdFusion initially stores the number as a string. If you use it in an
arithmetic expression, ColdFusion converts it into a floating-point value, preserving its value, but losing precision as
the following example shows:

<cfset mybignum=12345678901234567890>
<cfset mybignumtimes10=(mybignum * 10)>
<cfoutput>mybignum is: #mybignum#</cfoutput><br>
<cfoutput>mybignumtimes10 is: #mybignumtimes10# </cfoutput><br>

This example generates the following output:

mybignum is: 12345678901234567890

mybignumtimes10 is: 1.23456789012E+020




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         42
The CFML Programming Language




Real numbers
Real numbers, numbers with a decimal part, are also known as floating point numbers. ColdFusion real numbers can
range from approximately -10300 to approximately 10300. A real number can have up to 12 significant digits. As with
integers, you can assign a variable a value with more digits, but the data is stored as a string. The string is converted to
a real number, and can lose precision, when you use it in an arithmetic expression.

You can represent real numbers in scientific notation. This format is xEy, where x is a positive or negative real number
in the range 1.0 (inclusive) to 10 (exclusive), and y is an integer. The value of a number in scientific notation is x times
10y. For example, 4.0E2 is 4.0 times 102, which equals 400. Similarly, 2.5E-2 is 2.5 times 10-2, which equals 0.025.
Scientific notation is useful for writing very large and very small numbers.


BigDecimal numbers
ColdFusion does not have a special BigDecimal data type for arbitrary length decimal numbers such as
1234567890987564.234678503059281. Instead, it represents such numbers as strings. ColdFusion does, however, have
a PrecisionEvaluate function that can take an arithmetic expression that uses BigDecimal values, calculate the
expression, and return a string with the resulting BigDecimal value. For more information, see PrecisionEvaluate in
the CFML Reference.


Strings
In ColdFusion, text values are stored in strings. You specify strings by enclosing them in either single- or double-
quotation marks. For example, the following two strings are equivalent:

"This is a string"

'This is a string'

You can write an empty string in the following ways:

* "" (a pair of double-quotation marks with nothing in between)

* '' (a pair of single-quotation marks with nothing in between)

Strings can be any length, limited by the amount of available memory on the ColdFusion server. However, the default
size limit for long text retrieval (CLOB) is 64K. The ColdFusion Administrator lets you increase the limit for database
string transfers, but doing so can reduce server performance. To change the limit, select the Enable retrieval of long
text option on the Advanced Settings page for the data source.


Escaping quotation marks and number signs
To include a single-quotation character in a string that is single-quoted, use two single-quotation marks (known as
escaping the single-quotation mark). The following example uses escaped single-quotation marks:

 <cfset myString='This is a single-quotation mark: '' This is a double-quotation mark: "'>
<cfoutput>#mystring#</cfoutput><br>

To include a double-quotation mark in a double-quoted string, use two double-quotation marks (known as escaping
the double-quotation mark). The following example uses escaped double-quotation marks:

 <cfset myString="This is a single-quotation mark: ' This is a double-quotation mark: """>
<cfoutput>#mystring#</cfoutput><br>

Because strings can be in either double-quotation marks or single-quotation marks, both of the preceding examples
display the same text:

This is a single-quotation mark: ' This is a double-quotation mark: "

To insert a number sign (#) in a string, you must escape the number sign, as follows:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        43
The CFML Programming Language




 "This is a number sign ##"


Lists
ColdFusion includes functions that operate on lists, but it does not have a list data type. In ColdFusion, a list is just a
string that consists of multiple entries separated by delimiter characters.

The default delimiter for lists is the comma. If you use any other character to separate list elements, you must specify
the delimiter in the list function. You can also specify multiple delimiter characters. For example, you can tell
ColdFusion to interpret a comma or a semicolon as a delimiter, as the following example shows:

 <cfset MyList="1,2;3,4;5">
<cfoutput>
List length using ; and , as delimiters: #listlen(Mylist, ";,")#<br>
List length using only , as a delimiter: #listlen(Mylist)#<br>
</cfoutput>

This example displays the following output:

List length using ; and , as delimiters: 5

List length using only , as a delimiter: 3

Each delimiter must be a single character. For example, you cannot tell ColdFusion to require two hyphens in a row
as a delimiter.

If a list has two delimiters in a row, ColdFusion ignores the empty element. For example, if MyList is "1,2,,3,,4,,,5" and
the delimiter is the comma, the list has five elements, and list functions treat it the same as "1,2,3,4,5".


Boolean values
A Boolean value represents whether something is true or false. ColdFusion has two special constants--True and
False--to represent these values. For example, the Boolean expression 1 IS 1 evaluates to True. The expression
"Monkey" CONTAINS "Money" evaluates to False.

You can use Boolean constants directly in expressions, as in the following example:

 <cfset UserHasBeenHere = True>

In Boolean expressions, True, nonzero numbers, and the strings "Yes", "1", "True" are equivalent; and False, 0, and the
strings "No", "0", and "False" are equivalent.

Boolean evaluation is not case sensitive. For example, True, TRUE, and true are equivalent.


Date-Time values
ColdFusion can perform operations on date and time values. Date-time values identify a date and time in the range
100 AD to 9999 AD. Although you can specify just a date or a time, ColdFusion uses one data type representation,
called a date-time object, for date, time, and date and time values.

ColdFusion provides many functions to create and manipulate date-time values and to return all or part of the value
in several different formats.

You can enter date and time values directly in a cfset tag with a constant, as follows:

 <cfset myDate = "October 30, 2001">




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          44
The CFML Programming Language




When you do this, ColdFusion stores the information as a string. If you use a date-time function, ColdFusion stores
the value as a date-time object, which is a separate simple data type. When possible, use date-time functions such as
CreateDate and CreateTime to specify dates and times, because these functions can prevent you from specifying the
date or time in an invalid format and they create a date-time object immediately.


Date and time formats
You can directly enter a date, time, or date and time, using standard U.S. date formats. ColdFusion processes the two-
digit-year values 0 to 29 as twenty-first century dates; it processes the two-digit-year values 30 to 99 as twentieth
century dates. Time values can include units down to seconds. The following table lists valid date and time formats:


 To specify              Use these formats

 Date                    October 30, 2003

                         Oct 30, 2003

                         Oct. 30, 2003

                         10/30/03

                         2003-10-30

                         10-30-2003

 Time                    02:34:12

                         2:34a

                         2:34am

                         02:34am

                         2am

 Date and Time           Any combination of valid date and time formats, such as these:

                         October 30, 2003 02:34:12

                         Oct 30, 2003 2:34a

                         Oct. 30, 2001 2:34am

                         10/30/03 02:34am

                         2003-10-30 2am

                         10-30-2003 2am



Locale-specific dates and times
ColdFusion provides several functions that let you input and output dates and times (and numbers and currency
values) in formats that are specific to the current locale. A locale identifies a language and locality, such as English (US)
or French (Swiss). Use these functions to input or output dates and times in formats other than the U.S. standard
formats. (Use the SetLocale function to specify the locale.) The following example shows how to do this:

<cfset oldlocale = SetLocale("French (Standard)")>
<cfoutput>#LSDateFormat(Now(), "ddd, dd mmmm, yyyy")#</cfoutput>

This example outputs a line like the following:

mar., 03 juin, 2003

For more information on international functions, see "Developing Globalized Applications" on page 364 and the
CFML Reference.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                           45
The CFML Programming Language




How ColdFusion stores dates and times
ColdFusion stores and manipulates dates and times as date-time objects. Date-time objects store data on a timeline as
real numbers. This storage method increases processing efficiency and directly mimics the method used by many
database systems. In date-time objects, one day is equal to the difference between two successive integers. The time
portion of the date-and-time value is stored in the fractional part of the real number. The value 0 represents 12:00 AM
12/30/1899.

Although you can use arithmetic operations to manipulate date-and-time values directly, this method can result in
code that is difficult to understand and maintain. Use the ColdFusion date-time manipulation functions instead. For
information on these functions, see the CFML Reference.


Binary data type and binary encoding
Binary data (also referred to as a binary object) is raw data, such as the contents of a GIF file or an executable program
file. You do not normally use binary data directly, but you can use the cffile tag to read a binary file into a variable,
typically for conversion to a string binary encoding before transmitting the file using e-mail.

A string binary encoding represents a binary value in a string format that can be transmitted over the web. ColdFusion
supports three binary encoding formats:


 Encoding                 Format

 Base64                   Encodes the binary data in the lowest six bits of each byte. It ensures that binary data and non-ANSI character data
                          can be transmitted using e-mail without corruption. The Base64 algorithm is specified by IETF RFC 2045, at
                          www.ietf.org/rfc/rfc2045.txt.

 Hex                      Uses two characters in the range 0-9 and A-F represent the hexadecimal value of each byte; for example, 3A.

 UU                       Uses the UNIX UUencode algorithm to convert the data.


ColdFusion provides the following functions that convert among string data, binary data, and string encoded binary
data:


 Function                 Description

 BinaryDecode             Converts a string that contains encoded binary data to a binary object.

 BinaryEncode             Converts binary data to an encoded string.

 CharsetDecode            Converts a string to binary data in a specified character encoding.

 CharsetEncode            Converts a binary object to a string in a specified character encoding.

 ToBase64                 Converts string and binary data to Base64 encoded data.

 ToBinary                 Converts Base64 encoded data to binary data. The BinaryDecode function provides a superset of the ToBase64
                          functionality.

 ToString                 Converts most simple data types to string data. It can convert numbers, date-time objects, and Boolean values. (It
                          converts date-time objects to ODBC timestamp strings.) Adobe recommends that you use the CharsetEncode
                          function to convert binary data to a string in new applications.



Complex data types
Arrays, structures, and queries are ColdFusion built-in complex data types. Structures and queries are sometimes
referred to as objects, because they are containers for data, not individual data values.

For details on using arrays and structures, see "Using Arrays and Structures" on page 82.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       46
The CFML Programming Language




Arrays
Arrays are a way of storing multiple values in a table-like format that can have one or more dimensions. You create
arrays using a function or an assignment statement:

* The ColdFusion ArrayNew function creates an array and specifies its initial dimensions. For example, the following
   line creates an empty two-dimensional array:

    <cfset myarray=ArrayNew(2)>

* A direct assignment creates an array and populates an array element. For example, the following line creates a two-
   dimensional array and sets the value of row 1 column 2 to the current date.

    <cfset myarray[1][2]=Now()>

You reference elements using numeric indexes, with one index for each dimension, as shown in the preceding
example.

You can create arrays with up to three dimensions directly. However, there is no limit on array size or maximum
dimension. To create arrays with more than three dimensions, create arrays of arrays.

After you create an array, you can use functions or direct references to manipulate its contents.

When you assign an existing array to a new variable, ColdFusion creates a new array and copies the old array's contents
to the new array. The following example creates a copy of the original array:

<cfset newArray=myArray>

For more information on using arrays, see "Using Arrays and Structures" on page 82.


Structures
ColdFusion structures consist of key-value pairs, where the keys are text strings and the values can be any ColdFusion
data type, including other structures. Structures let you build a collection of related variables that are grouped under a
single name. To create a structure, use the ColdFusion StructNew function. For example, the following line creates a
new, empty, structure called depts:

<cfset depts=StructNew()>

You can also create a structure by assigning a value in the structure. For example, the following line creates a new
structure called MyStruct with a key named MyValue, equal to 2:

<cfset MyStruct.MyValue=2>

Note: In ColdFusion versions through ColdFusion 7, this line created a Variables scope variable named
"MyStruct.MyValue" with the value 2.

After you create a structure, you can use functions or direct references to manipulate its contents, including adding
key-value pairs.

You can use either of the following methods to reference elements stored in a structure:

* StructureName.KeyName

* StructureName["KeyName"]

The following examples show these methods:

depts.John="Sales"
depts["John"]="Sales"

When you assign an existing structure to a new variable, ColdFusion does not create a new structure. Instead, the new
variable accesses the same data (location) in memory as the original structure variable. In other words, both variables
are references to the same object.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    47
The CFML Programming Language




For example, the following line creates a new variable, myStructure2, that is a reference to the same structure as the
myStructure variable:

 <cfset myStructure2=myStructure>

When you change the contents of myStructure2, you also change the contents of myStructure. To copy the contents
of a structure, use the ColdFusion Duplicate function, which copies the contents of structures and other complex
data types.

Structure key names can be the names of complex data objects, including structures or arrays. This lets you create
arbitrarily complex structures.

For more information on using structures, see "Using Arrays and Structures" on page 82.


Queries
A query object, sometimes referred to as a query, query result, or recordset, is a complex ColdFusion data type that
represents data in a set of named columns, like the columns of a database table. Many tags can return data as a query
object, including the following:

* cfquery

* cfdirectory

* cfhttp

* cfldap

* cfpop

* cfprocresult

In these tags, the name attribute specifies the query object's variable name. The QueryNew function also creates query
objects.

When you assign a query to a new variable, ColdFusion does not copy the query object. Instead, both names point to
the same recordset data. For example, the following line creates a new variable, myQuery2, that references the same
recordset as the myQuery variable:

 <cfset myQuery2 = myQuery>

If you make changes to data in myQuery, myQuery2 also shows those changes.

You reference query columns by specifying the query name, a period, and the column name; for example:

 myQuery.Dept_ID

When you reference query columns inside tags, such as cfoutput and cfloop, in which you specify the query name
in a tag attribute, you do not have to specify the query name.

You can access query columns as if they are one-dimensional arrays. For example, the following line assigns the
contents of the Employee column in the second row of the myQuery query to the variable myVar:

 <cfset myVar = myQuery.Employee[2]>

Note: You cannot use array notation to reference a row (of all columns) of a query. For example, myQuery[2] does not
reference the second row of the myQuery query object.


Working with structures and queries
Because structure variables and query variables are references to objects, the rules in the following sections apply to
both types of data.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    48
The CFML Programming Language




Multiple references to an object
When multiple variables reference a structure or query object, the object continues to exist as long as at least one
reference to the object exists. The following example shows how this works:

 <cfscript> depts = structnew();</cfscript>
<cfset newStructure=depts>
<cfset depts.John="Sales">
<cfset depts=0>
<cfoutput>
     #newStructure.John#<br>
     #depts#
</cfoutput>

This example displays the following output:

Sales

0

After the <cfset depts=0> tag executes, the depts variable does not reference a structure; it is a simple variable with
the value 0. However, the variable newStructure still refers to the original structure object.


Assigning objects to scopes
You can give a query or structure a different scope by assigning it to a new variable in the other scope. For example,
the following line creates a server variable, Server.SScopeQuery, using the local myquery variable:

 <cfset Server.SScopeQuery = myquery>

To clear the server scope query variable, reassign the query object, as follows:

 <cfset Server.SScopeQuery = 0>

This line deletes the reference to the object from the server scope, but does not remove any other references that can
exist.


Copying and duplicating objects
You can use the Duplicate function to make a true copy of a structure or query object. Changes to the copy do not
affect the original.


Using a query column
When you are not inside a tag such as cfloop, cfoutput, or cfmail that has a query attribute, you can treat a query
column as an array. However, query column references do not always behave as you might expect. This section
explains the behavior of references to query columns using the results of the following cfquery tag in its examples:

 <cfquery dataSource="cfdocexamples" name="myQuery">
     SELECT FirstName, LastName
     FROM Employee
</cfquery>

To reference elements in a query column, use the row number as an array index. For example, both of the following
lines display the word "ben":

 <cfoutput> #myQuery.Firstname[1]# </cfoutput><br>
<cfoutput> #myQuery["Firstname"][1]# </cfoutput><br>




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      49
The CFML Programming Language




ColdFusion behavior is less straightforward, however, when you use the query column references myQuery.Firstname
and myQuery["Firstname"] without using an array index. The two reference formats produce different results.

If you reference myQuery.Firstname, ColdFusion automatically converts it to the first row in the column. For example,
the following lines print the word "ben":

 <cfset myCol = myQuery.Firstname >
<cfoutput>#mycol#</cfoutput>

But the following lines display an error message:

 <cfset myCol = myQuery.Firstname >
<cfoutput>#mycol[1]#</cfoutput><br>

If you reference Query["Firstname"], ColdFusion does not automatically convert it to the first row of the column.
For example, the following line results in an error message indicating that ColdFusion cannot convert a complex type
to a simple value:

 <cfoutput> #myQuery['Firstname']# </cfoutput><br>

Similarly, the following lines print the name "marjorie", the value of the second row in the column:

 <cfset myCol = myQuery["Firstname"]>
<cfoutput>#mycol[2]#</cfoutput><br>

However, when you make an assignment that requires a simple value, ColdFusion automatically converts the query
column to the value of the first row. For example, the following lines display the name "ben" twice:

 <cfoutput> #myQuery.Firstname# </cfoutput><br>
<cfset myVar= myQuery['Firstname']>
<cfoutput> #myVar# </cfoutput><br>



Using periods in variable references
ColdFusion uses the period (.) to separate elements of a complex variable such as a structure, query, XML document
object, or external object, as in MyStruct.KeyName. A period also separates a variable scope identifier from the variable
name, as in Variables.myVariable or CGI.HTTP_COOKIE.

With the exception of Cookie and Client scope variables, which must always be simple variable types, you cannot
normally include periods in simple variable names. However, ColdFusion makes some exceptions that accommodate
legacy and third-party code that does not conform to this requirement.

For more information, see "About scopes" on page 56, "Using Arrays and Structures" on page 82, and "Using XML
and WDDX" on page 1058.


Understanding variables and periods
The following descriptions use a sample variable named MyVar.a.b to explain how ColdFusion uses periods when
getting and setting the variable value.


Getting a variable
ColdFusion can correctly get variable values even if the variable name includes a period. For example, the following
set of steps shows how ColdFusion gets MyVar.a.b, as in <cfset Var2 = myVar.a.b> or IsDefined(myVar.a.b):

1 Looks for myVar in an internal table of names (the symbol table).

2 If myVar is the name of a complex object, including a scope, looks for an element named a in the object.

    If myVar is not the name of a complex object, checks whether myVar.a is the name of a complex object and skips
    step 3.


                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         50
The CFML Programming Language




3 If myVar is the name of a complex object, checks whether a is a complex object.

4 If a or myVar.a is the name of a complex object, checks whether b is the name of a simple variable, and returns the
   value of b.

   If myVar is a complex object but a is not a complex object, checks whether a.b is the name of a simple variable and
   returns its value.

   If myVar.a is not a complex object, checks whether myVar.a.b is the name of a simple variable and returns its value.

This way, ColdFusion correctly resolves the variable name and can get its value.

You can also use array notation to get a simple variable with a name that includes periods. In this form of array
notation, you use the scope name (or the complex variable that contains the simple variable) as the "array" name. You
place the simple variable name, in single- or double-quotation marks, inside the brackets.

Using array notation is more efficient than using plain dot notation because ColdFusion does not have to analyze and
look up all the possible key combinations. For example, both of the following lines write the value of myVar.a.b, but
the second line is more efficient than the first:

 <cfoutput>myVar.a.b is: #myVar.a.b#<br></cfoutput>
<cfoutput>myVar.a.b is: #Variables["myVar.a.b"]#<br></cfoutput>


Setting a variable
ColdFusion cannot be as flexible when it sets a variable value as when it gets a variable, because it must determine the
type of variable to create or set. Therefore, the rules for variable names that you set are stricter. Also, the rules vary
depending on whether the first part of the variable name is the Cookie or Client scope identifier.

For example, assume that you have the following code:

 <cfset myVar.a.b = "This is a test">

If a variable myVar does not exist, it does the following:

1 Creates a structure named myVar.

2 Creates a structure named a in the structure myVar.

3 Creates a key named b in myVar.a.

4 Gives it the value "This is a test".

If either myVar or myVar.a exist and neither one is a structure, ColdFusion generates an error.

In other words, ColdFusion uses the same rules as for getting a variable to resolve the variable name until it finds a
name that does not exist yet. It then creates any structures that are needed to create a key named b inside a structure,
and assigns the value to the key.

However, if the name before the first period is either Cookie or Client, ColdFusion uses a different rule. It treats all the
text (including any periods) that follow the scope name as the name of a simple variable, because Cookie and Client
scope variables must be simple. If you have the following code, you see that ColdFusion creates a single, simple Client
scope variable named myVar.a.b:

 <cfset Client.myVar.a.b = "This is a test">
<cfdump var=#Client.myVar.a.b#>




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     51
The CFML Programming Language




Creating variables with periods
Avoid creating the names of variables (except for dot notation in structures) that include periods. However,
ColdFusion provides mechanisms for handling cases where you must do so, for example, to maintain compatibility
with names of variables in external data sources or to integrate your application with existing code that uses periods
in variable names. The following sections describe how to create simple variable names that include periods.


Using brackets to create variables with periods
You can create a variable name that includes periods by using associative array structure notation, as described in
"Structure notation" on page 93. To do so, you must do the following:

* Reference the variable as part of a structure. You can always do this, because ColdFusion considers all scopes to be
   structures. For more information on scopes, see "About scopes" on page 56.

* Place the variable name that must include a period inside brackets and single- or double-quotation marks.

The following example shows this technique:

 <cfset Variables['My.Variable.With.Periods'] = 12>
<cfset Request["Another.Variable.With.Periods"] = "Test variable">
<cfoutput>
     My.Variable.With.Periods is: #My.Variable.With.Periods#<br>
     Request.Another.Variable.With.Periods is:
          #Request.Another.Variable.With.Periods#<br>
</cfoutput>


Creating Client and Cookie variables with periods
To create a Client or Cookie variable with a name that includes one or more periods, simply assign the variable a value.
For example, the following line creates a Cookie named User.Preferences.CreditCard:

 <cfset Cookie.User.Preferences.CreditCard="Discover">



Data type conversion
ColdFusion automatically converts between data types to satisfy the requirements of an expression's operations,
including a function's argument requirements. As a result, you generally don't need to be concerned about
compatibility between data types and the conversions from one data type to another. However, understanding how
ColdFusion evaluates data values and converts data between types can help you prevent errors and create code more
effectively.


Operation-driven evaluation
Conventional programming languages enforce strict rules about mixing objects of different types in expressions. For
example, in a language such as C++ or Basic, the expression ("8" * 10) produces an error because the multiplication
operator requires two numeric operands and "8" is a string. When you program in such languages, you must convert
between data types to ensure error-free program execution. For example, the previous expression might have to be
written as (ToNumber("8") * 10).

In ColdFusion, however, the expression ("8" * 10) evaluates to the number 80 without generating an error. When
ColdFusion processes the multiplication operator, it automatically attempts to convert its operands to numbers. Since
"8" can be successfully converted to the number 8, the expression evaluates to 80.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                            52
The CFML Programming Language




ColdFusion processes expressions and functions in the following sequence:

1 For each operator in an expression, it determines the required operands. (For example, the multiplication operator
    requires numeric operands and the CONTAINS operator requires string operands.)

    For functions, it determines the type required for each function argument. (For example, the Min function requires
    two numbers as arguments and the Len function requires a string.)

2 It evaluates all operands or function arguments.

3 It converts all operands or arguments whose types differ from the required type. If a conversion fails, it reports an
    error.


Conversion between types
Although the expression evaluation mechanism in ColdFusion is powerful, it cannot automatically convert all data.
For example, the expression "eight" * 10 produces an error because ColdFusion cannot convert the string "eight"
to the number 8. Therefore, you must understand the rules for conversion between data types.

The following table explains how conversions are performed. The first column shows values to convert. The remaining
columns show the result of conversion to the listed data type.


 Value        As Boolean                   As number                               As date-time                         As string

 "Yes"        True                          1                                       Error                                "Yes"

 "No"         False                         0                                       Error                                "No"

 True         True                          1                                       Error                                "Yes"

 False        False                         0                                       Error                                "No"

 Number       True if Number is not 0;      Number                                  See "Date-time values" earlier in    String representation
              False otherwise.                                                      this chapter.                        of the number (for
                                                                                                                         example, "8").

 String       If "Yes", True                If it represents a number (for          If it is an ODBC date, time, or      String
                                            example, "1,000" or "12.36E-12"), it is timestamp (for example "{ts
              If "No", False
                                            converted to the corresponding          '2001-06-14 11:30:13'}", or if it is

              If it can be converted to 0,  number. If it represents a date-time    expressed in a standard U.S.

              False                         (see next column), it is converted to   date or time format, including
                                            the numeric value of the                the use of full or abbreviated
              If it can be converted to any corresponding date-time object.         month names, it is converted to
              other number, True                                                    the corresponding date-time
                                                                                    value.

                                                                                    Days of the week or unusual
                                                                                    punctuation result in an error.

                                                                                    Dashes, forward-slashes, and
                                                                                    spaces are generally allowed.

 Date         Error                         The numeric value of the date-time      Date                                 An ODBC timestamp.
                                            object.


ColdFusion cannot convert complex types, such as arrays, queries, and COM objects, to other types. However, it can
convert simple data elements of complex types to other simple data types.


Type conversion considerations
The following sections detail specific rules and considerations for converting between types.




                                                       

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   53
The CFML Programming Language




The cfoutput tag
The cfoutput tag always displays data as a string. As a result, when you display a variable using the cfoutput tag,
ColdFusion applies the type conversion rules to any non-string data before displaying it. For example, the cfoutput
tag displays a date-time value as an ODBC timestamp.


Case-insensitivity and Boolean conversion
Because ColdFusion expression evaluation is not case sensitive, Yes, YES, and yes are equivalent; False, FALSE, and
false are equivalent; No, NO, and no are equivalent; and True, TRUE, and true are equivalent.


Converting binary data
ColdFusion cannot automatically convert binary data to other data types. To convert binary data, use the ToBase64
and ToString functions. For more information, see "Binary data type and binary encoding" on page 45.


Converting date and time data
To ensure that a date and time value is expressed as a real number, add zero to the variable. The following example
shows this:

 <cfset mynow = now()>
Use cfoutput to display the result of the now function:<br>
<cfoutput>#mynow#</cfoutput><br>
Now add 0 to the result and display it again:<br>
<cfset mynow = mynow + 0>
<cfoutput>#mynow#</cfoutput>

At 1:06 PM on June 6, 2003, its output looked like this:

 Use cfoutput to display the result of the now function:
{ts '2003-06-03 13:06:44'}
Now add 0 to the result and display it again:
37775.5463426


Converting numeric values
When ColdFusion evaluates an expression that includes both integers and real numbers, the result is a real number.
To convert a real number to an integer, use a ColdFusion function. The Int, Round, Fix, and Ceiling functions
convert real numbers to integers, and differ in their treatment of the fractional part of the number.

If you use a hidden form field with a name that has the suffix _integer or _range to validate a form input field,
ColdFusion truncates real numbers entered into the field and passes the resulting integer to the action page.

If you use a hidden form field with a name that has the suffix _integer, _float, or _range to validate a form input
field, and the entered data contains a dollar amount (including a dollar sign) or a numeric value with commas,
ColdFusion considers the input to be valid, removes the dollar sign or commas from the value, and passes the resulting
integer or real number to the action page.

ColdFusion does not have an inherent data type for arbitrary precision decimal numbers (BigDecimal numbers).
ColdFusion initially saves such numbers as strings, and if you use them in an expression, converts the value to a
numeric type, often losing precision. You can retain precision by using the PrecisionEvaluate method, which
evaluates string expressions using BigDecimal precision arithmetic and can return the result as a long string of
numbers. For more information, see PrecisionEvaluate in the CFML Reference.


Evaluation and type conversion issues
The following sections explain several issues that you can encounter with type evaluation and conversion.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    54
The CFML Programming Language




Comparing variables to True or False
You might expect the following two cfif tag examples to produce the same results:

 <cfif myVariable>
      <cfoutput>myVariable equals #myVariable# and is True
      </cfoutput>
</cfif>
<cfif myVariable IS True>
      <cfoutput>myVariable equals #myVariable# and is True
      </cfoutput>
</cfif>

However, if myVariable has a numeric value such as 12, only the first example produces a result. In the second case,
the value of myVariable is not converted to a Boolean data type, because the IS operator does not require a specific data
type and just tests the two values for identity. Therefore, ColdFusion compares the value 12 with the constant True.
The two are not equal, so nothing is printed. If myVariable is 1, "Yes", or True, however, both examples print the same
result, because ColdFusion considers these to be identical to Boolean True.

If you use the following code, the output statement does display, because the value of the variable, 12, is not equal to
the Boolean value False:

 <cfif myVariable IS NOT False>
      <cfoutput>myVariable equals #myVariable# and IS NOT False
      </cfoutput>
</cfif>

As a result, use the test <cfif testvariable>, and not use the IS comparison operator when testing whether a variable
is True or False. This issue is a case of the more general problem of ambiguous type expression evaluation, described
in the following section.


Ambiguous type expressions and strings
When ColdFusion evaluates an expression that does not require strings, including all comparison operations, such as
IS or GT, it checks whether it can convert each string value to a number or date-time object. If so, ColdFusion converts
it to the corresponding number or date-time value (which is stored as a number). It then uses the number in the
expression.

Short strings, such as 1a and 2P, can produce unexpected results. ColdFusion can interpret a single "a" as AM and a
single "P" as PM. This can cause ColdFusion to interpret strings as date-time values in cases where this was not
intended.

Similarly, if the strings can be interpreted as numbers, you can get unexpected results.

For example, ColdFusion interprets the following expressions as shown:


 Expression                           Interpretation

 <cfif "1a" EQ "01:00">               If 1:00am is 1:00am.

 <cfif "1P" GT "2A">                  If 1:00pm is later than 2:00am.

 <cfset age="4a">                     Treat the variable age as 4:00 am, convert it to the date-time value 0.16666666667, and add 7 to
                                      make it 7.16666666667.
 <cfset age=age + 7>


 <cfif "0.0" is "0">                  If 0 is 0.


To prevent such ambiguities when you compare strings, use the ColdFusion string comparison functions Compare and
CompareNoCase, instead of the comparison operators.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     55
The CFML Programming Language




You can also use the IsDate function to determine whether a string can be interpreted as a date-time value, or to add
characters to a string before comparison to avoid incorrect interpretation.


Date-time functions and queries when ODBC is not supported
Many CFML functions, including the Now, CreateDate, CreateTime, and CreateDateTime functions, return date-
time objects. ColdFusion creates Open Database Connectivity (ODBC) timestamp values when it converts date-time
objects to strings. As a result, you can get unexpected results when using dates with a database driver that does not
support ODBC escape sequences, or when you use SQL in a query of queries.

If you use SQL to insert data into a database or in a WHERE clause to select data from a database, and the database
driver does not support ODBC-formatted dates, use the DateFormat function to convert the date-time value to a valid
format for the driver. This rule also applies to queries of queries.

For example, the following SQL statement uses the DateFormat function in a query of queries to select rows that have
MyDate values in the future:

<cfquery name="MyQofQQ" dbtype="query">
SELECT *
FROM DateQuery
WHERE MyDate >= '#DateFormat(Now())#'
</cfquery>

The following query of queries fails with the error message "Error: {ts is not a valid date," because the ColdFusion Now
function returns an ODBC timestamp:

<cfquery name="MyQofQQ" dbtype="query">
SELECT *
FROM DateQuery
WHERE MyDate >= '#now()#'
</cfquery>


Using JavaCast with overloaded Java methods
You can overload Java methods so a class can have several identically named methods that differ only in parameter
data types. At run time, the Java virtual machine attempts to resolve the specific method to use, based on the types of
the parameters passed in the call. Because ColdFusion does not use explicit types, you cannot predict which version of
the method the virtual machine will use.

The ColdFusion JavaCast function helps you ensure that the right method executes by specifying the Java type of a
variable, as in the following example:

<cfset emp.SetJobGrade(JavaCast("int", JobGrade))>

The JavaCast function takes two parameters: a string representing the Java data type and the variable whose type you
are setting. You can specify the following Java data types: Boolean, int, long, float, double, and String.

For more information on the JavaCast function, see the CFML Reference.


Using quotation marks
To ensure that ColdFusion properly interprets string data, surround strings in single- or double-quotation marks. For
example, ColdFusion evaluates "10/2/2001" as a string that can be converted into a date-time object. However, it
evaluates 10/2/2001 as a mathematical expression, 5/2001, which evaluates to 0.00249875062469.


Examples of type conversion in expression evaluation
The following examples demonstrate ColdFusion expression evaluation.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         56
The CFML Programming Language




Example 1
 2 * True + "YES" - ('y' & "es")

Result value as string: "2"

Explanation: (2*True) is equal to 2; ("YES"- "yes") is equal to 0; 2 + 0 equals 2.


Example 2
 "Five is " & 5

Result value as string: "Five is 5"

Explanation: 5 is converted to the string "5".


Example 3
 DateFormat("October 30, 2001" + 1)

Result value as string: "31-Oct-01"

Explanation: The addition operator forces the string "October 30, 2001" to be converted to a date-time object, and then
to a number. The number is incremented by one. The DateFormat function requires its argument to be a date-time
object; thus, the result of the addition is converted to a date-time object. One is added to the date-time object, moving
it ahead by one day to October 31, 2001.


About scopes
Variables differ in how they are set (by your code or by ColdFusion), the places in your code where they are
meaningful, and how long their values persist. These considerations are generally referred to as a variable's scope.
Commonly used scopes include the Variables scope, the default scope for variables that you create, and the Request
scope, which is available for the duration of an HTTP request.

Note: User-defined functions also belong to scopes. For more information, see "Specifying the scope of a function" on
page 173.


Scope types
The following table describes ColdFusion scopes:


 Scope                    Description

 Application              Contains variables that are associated with one, named application on a server. The cfapplication tag name
                          attribute or the Application.cfc This.name variable setting specifies the application name. For more information,
                          see "Using Persistent Data and Locking" on page 301.

 Arguments                Variables passed in a call to a user-defined function or ColdFusion component method. For more information, see
                          "About the Arguments scope" on page 161.

 Attributes               Used only in custom tag pages and threads. Contains the values passed by the calling page or cfthread tag in
                          the tag's attributes. For more information, see "Creating and Using Custom CFML Tags" on page 208 and "Using
                          ColdFusion Threads" on page 328.

 Caller                   Used only in custom tag pages. The custom tag's Caller scope is a reference to the calling page's Variables scope.
                          Any variables that you create or change in the custom tag page using the Caller scope are visible in the calling
                          page's Variables scope. For more information, see "Creating and Using Custom CFML Tags" on page 208.

 CGI                      Contains environment variables identifying the context in which a page was requested. The variables available
                          depend on the browser and server software. For a list of the commonly used CGI variables, see Reserved Words
                          and Variables in the CFML Reference.




                                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                           57
The CFML Programming Language




Scope                   Description

Client                   Contains variables that are associated with one client. Client variables let you maintain state as a user moves from
                         page to page in an application, and are available across browser sessions. By default, Client variables are stored in
                         the system registry, but you can store them in a cookie or a database. Client variables cannot be complex data
                         types and can include periods in their names. For more information, see "Using Persistent Data and Locking" on
                         page 301.

Cookie                   Contains variables maintained in a user's browser as cookies. Cookies are typically stored in a file on the browser,
                         so they are available across browser sessions and applications. You can create memory-only Cookie variables,
                         which are not available after the user closes the browser. Cookie scope variable names can include periods.

Flash                    Variables sent by a SWF movie to ColdFusion and returned by ColdFusion to the movie. For more information, see
                         "Using the Flash Remoting Service" on page 606.

Form                     Contains variables passed from a Form page to its action page as the result of submitting the form. (If you use the
                         HTML form tag, you must use method="post".) For more information, see "Introduction to Retrieving and
                         Formatting Data" on page 703.

Local (function local)   Contains variables that are declared inside a user-defined function or ColdFusion component method and exist
                         only while a function executes. For more information, see "Writing and Calling User-Defined Functions" on
                         page 153.

Request                  Used to hold data that must be available for the duration of one HTTP request. The Request scope is available to
                         all pages, including custom tags and nested custom tags, that are processed in response to the request.

                         This scope is useful for nested (child/parent) tags. This scope can often be used in place of the Application scope,
                         to avoid the need for locking variables. Several chapters discuss using the Request scope.

Server                   Contains variables that are associated with the current ColdFusion server. This scope lets you define variables that
                         are available to all your ColdFusion pages, across multiple applications. For more information, see "Using
                         Persistent Data and Locking" on page 301.

Session                  Contains variables that are associated with one client and persist only as long as the client maintains a session.
                         They are stored in the server's memory and can be set to time out after a period of inactivity. For more information,
                         see "Using Persistent Data and Locking" on page 301.

This                     Exists only in ColdFusion components or cffunction tags that are part of a containing object such as a
                         ColdFusion Struct. Exists for the duration of the component instance or containing object. Data in the This scope
                         is accessible from outside the component or container by using the instance or object name as a prefix.

ThisTag                  Used only in custom tag pages. The ThisTag scope is active for the current invocation of the tag. If a custom tag
                         contains a nested tag, any ThisTag scope values you set before calling the nested tag are preserved when the
                         nested tag returns to the calling tag.

                         The ThisTag scope includes three built-in variables that identify the tag's execution mode, contain the tag's
                         generated contents, and indicate whether the tag has an end tag.

                         A nested custom tag can use the cfassociate tag to return values to the calling tag's ThisTag scope. For more
                         information, see "Accessing tag instance data" on page 216.

Thread                   Variables that are created and changed inside a ColdFusion thread, but can be read by all code on the page that
                         creates the thread. Each thread has a Thread scope that is a subscope of a cfthread scope. For more information,
                         see "Using ColdFusion Threads" on page 328.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        58
The CFML Programming Language




 Scope                   Description

 thread local            Variables that are available only within a ColdFusion thread. For more information, see "Using ColdFusion
                         Threads" on page 328.

 URL                     Contains parameters passed to the current page in the URL that is used to call it. The parameters are appended to
                         the URL in the format ?variablename = value[&variablename=value...]; for example
                         www.MyCompany.com/inputpage.cfm?productCode=A12CD1510&quantity=3.

                          If a URL includes multiple parameters with the same name, the resulting variable in the ColdFusion URL scope
                         consists of all parameter values separated by commas. For example, a URL of the form
                         http://localhost/urlparamtest.cfm? param=1&param=2&param=3 results in a URL.param variable value of 1,2,3
                         on the ColdFusion page.

 Variables               The default scope for variables of any type that are created with the cfset and cfparam tags. A Variables scope
                         variable is available only on the page on which it is created and any included pages (see also the Caller scope).

                         Variables scope variables created in a CFC are available only to the component and its functions, and not to the
                         page that instantiates the component or calls its functions.


Important: To prevent data corruption, you lock code that uses Session, Application, or Server scope variables. For more
information, see "Using Persistent Data and Locking" on page 301.


Creating and using variables in scopes
The following table shows how you create and reference variables in different scopes in your code. For more
information on the mechanisms for creating variables in most scopes, see "Creating variables" on page 39.


 Scope prefix    Prefix required to      Where available                                   Created by
                 reference

 (type)

 Application     Yes                     For multiple clients in one application over      Specifying the prefix Application when you
                                         multiple browser sessions. Surround code that     create the variable.
                                         uses application variables in cflock blocks.

 Arguments       No                      Within the body of a user-defined function or     The calling page passing an argument in the
                                         ColdFusion component method.                      function call.

 Attributes      Yes                     On a custom tag page, or inside a thread          For custom tags, the calling page passing the
                                                                                           values to a custom tag page in the custom tag's
                                                                                           attributes.

                                                                                           For threads, the cfthread tag specifying
                                                                                           attribute values.

 Caller          On the custom tag       On the custom tag page, by using the Caller       On the custom tag page, by specifying the prefix
                 page, Yes.              scope prefix.                                     Caller when you create the variable.

                 On the calling page,    On the page that calls the custom tag, as local   On the calling page, by specifying the prefix
                 No (Variables prefix    variables (Variables scope).                      Variables, or using no prefix, when you create
                 is optional).                                                             the variable.

 Cffile          Yes                     Following an invocation of cffile.                A cffile tag.

 CGI             No                      On any page. Values are specific to the latest    The web server. Contains the server
                                         browser request.                                  environment variables that result from the
                                                                                           browser request.

 Client          No                      For one client in one application, over multiple  Specifying the prefix Client when you create the
                                         browser sessions.                                 variable.

 Cookie          No                      For one client in one or more applications and    A cfcookie tag. You can also set memory-only
                                         pages, over multiple browser sessions.            cookies by specifying the prefix Cookie when
                                                                                           you create the variable.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    59
The CFML Programming Language




Scope prefix    Prefix required to Where available                                   Created by
                reference

(type)

Flash           Yes                A ColdFusion page or ColdFusion component         The ColdFusion Client access. You assign a value
                                   called by a Flash client.                         to Flash.You can assign values to the Flash.result
                                                                                     and Flash.pagesize variables.

Form            No                 On the action page of a form and in custom tags   A form or cfform tag. Contains the values of
                                   called by the action page; cannot be used on a    form field tags (such as input) in the form body
                                   form page that is not also the action page.       when the form is submitted. The variable name
                                                                                     is the name of the form field.

Local           No                 Within the body of a user-defined function or     Either of the following:
                                   ColdFusion component method, only while the
                                                                                     * In the function or method definition, a var
                                   function executes.
                                                                                        keyword in a cfset tag or a CFScript var
                                                                                        statement.

                                                                                     * Specifying the Local keyword when you
                                                                                        create a variable in the function or method.

Request         Yes                On the creating page and in any pages run         Specifying the prefix Request when you create
                                   during the current HTTP request after the         the variable.
                                   variable is created, including in custom tags and
                                   nested custom tags.

Server          Yes                To any page on the ColdFusion server. Surround    Specifying the prefix Server when you create the
                                   all code that uses server variables in cflock     variable.
                                   blocks.

Session         Yes                For one client in one application and one         Specifying the prefix Session when you create
                                   browser session. Surround code that uses          the variable.
                                   Session scope variables in cflock blocks.

This            Yes                Within a ColdFusion component or the body of a    Within the component or function by specifying
                                   user-defined function that was created using the  the prefix This when you create the variable.
                                   cffunction tag and place in an object, structure,
                                                                                     In the containing page, by specifying the
                                   or scope. In the containing page, through the
                                                                                     component instance or object that contains the
                                   component instance or containing object.
                                                                                     function as a prefix when you create the
                                                                                     variable.

ThisTag         Yes                On the custom tag page.                           Specifying the prefix ThisTag when you create
                                                                                     the variable in the tag or using the cfassociate
                                                                                     tag in a nested custom tag.

Thread          The thread name.   Any code in the request.                          Using the keyword thread or the thread name
                                                                                     as a prefix when you create the variable.
                Inside the thread
                that creates the                                                     You can create Thread variables only inside the
                variable, you can                                                    thread.
                also use the
                keyword thread.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                      60
The CFML Programming Language




 Scope prefix     Prefix required to Where available                                   Created by
                  reference

 (type)

 thread-local (no none               Within a thread created by the cfthread tag       Using no prefix when you create the variable.
 prefix)                                                                               You can also use the keyword var before the
                                                                                       variable name.

 URL              No                 On the target page of the URL.                    The system. Contains the parameters passed in
                                                                                       the URL query string used to access the page.

 Variables        No                 On the current page. Cannot be accessed by a      Specifying the prefix Variables, or using no
                                     form's action page (unless the form page is also  prefix, when you create the variable. (To create a
 (Local)
                                     the action page). Variables in this scope used on Variables scope variable inside a ColdFusion
                                     a page that calls a custom tag can be accessed in thread, you must use the Variables prefix.)
                                     the custom tag by using its Caller scope;
                                     however, they are not available to any nested
                                     custom tags.



Using scopes
The following sections provide details on how you can create and use variables in different scopes.


Evaluating unscoped variables
If you use a variable name without a scope prefix, ColdFusion checks the scopes in the following order to find the
variable:

1 Local (function-local, UDFs and CFCs only)

2 Arguments

3 Thread local (inside threads only)

4 Query (not a true scope; variables in query loops)

5 Thread

6 Variables

7 CGI

8 Cffile

9 URL

10 Form

11 Cookie

12 Client

Because ColdFusion must search for variables when you do not specify the scope, you can improve performance by
specifying the scope for all variables.

To access variables in all other scopes, you must prefix the variable name with the scope identifier.


Scopes and CFX tags
ColdFusion scopes do not apply to ColdFusion Extension (CFX) tags, custom tags that you write in a programming
language such as C++ or Java. The ColdFusion page that calls a CFX tag must use tag attributes to pass data to the CFX
tag. The CFX tag must use the Java Request and Response interfaces or the C++ Request class to get and return data.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        61
The CFML Programming Language




The Java setVariable Response interface method and C++ CCFX::SetVariable method return data to the
Variables scope of the calling page. Therefore, they are equivalent to setting a Caller scope variable in a custom
ColdFusion tag.


Using scopes as structures
ColdFusion makes all named scopes available as structures. You cannot access the function-local scope for user-
defined functions (UDFs) that you define using CFScript as a structure.

You can reference the variables in named scopes as elements of a structure. To do so, specify the scope name as the
structure name and the variable name as the key. For example, if you have a MyVar variable in the Request scope, you
can reference it in either of the following ways:

Request.MyVar
Request["MyVar"]

Similarly, you can use CFML structure functions to manipulate the contents of the scope. For more information on
using structures, see "Using Arrays and Structures" on page 82.

Important: Do not call StructClear(Session) to clear session variables. This deletes the SessionID, CFID, and
CFtoken built-in variables, effectively ending the session. If you want to use StructClear to delete your application
variables, place those variables in a structure in the Session scope, and then clear that structure. For example, place all
your application variables in Session.MyVars and then call StructClear(Session.MyVars) to clear the variables.


Ensuring variable existence
ColdFusion generates an error if you try to use a variable value that does not exist. Therefore, before you use any
variable whose value is assigned dynamically, you must ensure that a variable value exists. For example, if your
application has a form, it must use some combination of requiring users to submit data in fields, providing default
values for fields, and checking for the existence of field variable values before they are used.

There are several ways to ensure that a variable exists before you use it, including the following:

* You can use the IsDefined function to test for the variable's existence.

* You can use the cfparam tag to test for a variable and set it to a default value if it does not exist.

* You can use a cfinput tag with a hidden attribute to tell ColdFusion to display a helpful message to any user who
   does not enter data in a required field. For more information on this technique, see "Requiring users to enter values
   in form fields" on page 709.


Testing for a variable's existence
Before relying on a variable's existence in an application page, you can test to see if it exists by using the IsDefined
function. To check whether a specific entry exists in an array, use the ArrayIsDefined function. To check whether a
specific key exists in a structure, use the StructKeyExists function.

For example, if you submit a form with an unsettled check box, the action page does not get a variable for the check
box. The following example from a form action page makes sure the Contractor check box Form variable exists before
using it:

<cfif IsDefined("Form.Contractor")>
     <cfoutput>Contractor: #Form.Contractor#</cfoutput>
     </cfif>

You must always enclose the argument passed to the IsDefined function in quotation marks. For more information
on the IsDefined function, see the CFML Reference.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         62
The CFML Programming Language




To test whether an element exists in an array before you display its value, use a format such as the following:

 </cfoutput>
      <cfloop index="i" from="1" to="#Arraylen(myArray)#">
           <cfif ArrayIsDefined(myArray, i)>
                 #i#: #myArray[i]#<br>
           </cfif>
      </cfloop>
</cfoutput>

Notice that in the ArrayIsDefined function, unlike the IsDefined function, you do not surround the variable name
in quotation marks.

If you attempt to evaluate a variable that you did not define, ColdFusion cannot process the page and displays an error
message. To help diagnose such problems, turn on debugging in the ColdFusion Administrator or use the debugger
in your editor. The Administrator debugging information shows which variables are being passed to your application
pages.


Variable existence considerations
If a variable is part of a scope that is available as a structure, you might get a minor performance increase by testing the
variable's existence using the StructKeyExists function instead of the IsDefined function.

You can also determine which Form variables exist by inspecting the contents of the Form.fieldnames built-in
variable. This variable contains a list of all the fields submitted by the form. Remember, however, that form text fields
are always submitted to the action page, and can contain an empty string if the user did not enter data.


Using the cfparam tag
You can ensure that a variable exists by using the cfparam tag, which tests for the variable's existence and optionally
supplies a default value if the variable does not exist. The cfparam tag has the following syntax:

 <cfparam name="VariableName"
      type="data_type"
      default="DefaultValue">

Note: For information on using the type attribute to validate the parameter data type, see the CFML Reference.

There are two ways to use the cfparam tag to test for variable existence, depending on how you want the validation
test to proceed:

* With only the name attribute to test that a required variable exists. If it does not exist, the ColdFusion server stops
   processing the page and displays an error message.

* With the name and default attributes to test for the existence of an optional variable. If the variable exists,
   processing continues and the value is not changed. If the variable does not exist, it is created and set to the value of
   the default attribute, and processing continues.

The following example shows how to use the cfparam tag to check for the existence of an optional variable and to set
a default value if the variable does not already exist:

 <cfparam name="Form.Contract" default="Yes">


Example: testing for variables
Using the cfparam tag with the name attribute is one way to clearly define the variables that a page or a custom tag
expects to receive before processing can proceed. This can make your code more readable, as well as easier to maintain
and debug.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         63
The CFML Programming Language




For example, the following cfparam tags indicate that this page expects two form variables named StartRow and
RowsToFetch:

 <cfparam name="Form.StartRow">
      <cfparam name="Form.RowsToFetch">

If the page with these tags is called without either one of the form variables, an error occurs and the page stops
processing. By default, ColdFusion displays an error message; you can also handle the error as described in "Handling
Errors" on page 275.


Example: setting default values
The following example uses the cfparam tag to see if optional variables exist. If they do exist, processing continues. If
they do not exist, the ColdFusion server creates them and sets them to the default values.

 <cfparam name="Cookie.SearchString" default="temple">
<cfparam name="Client.Color" default="Gray">
<cfparam name="ShowExtraInfo" default="No">

You can use the cfparam tag to set default values for URL and Form variables, instead of using conditional logic. For
example, you could include the following code on the action page to ensure that a SelectedDepts variable exists:

 <cfparam name="Form.SelectedDepts" default="Marketing,Sales">



Validating data
It is often not sufficient that input data merely exists; it must also have the right format. For example, a date field must
have data in a date format. A salary field must have data in a numeric or currency format. There are many ways to
ensure the validity of data, including the following methods:

* Use the cfparam tag with the type attribute to validate a variable.

* Use the IsValid function to validate a variable.

* Use the cfqueryparam tag in a SQL WHERE clause to validate query parameters.

* Use cfform controls that have validation attributes.

* Use a form input tag with a hidden attribute to validate the contents of a form input field.

Note: Data validation using the cfparam,cfqueryparam, and form tags is done by the server. Validation using cfform
tags and hidden fields is done using JavaScript in the user's browser, before any data is sent to the server.

For detailed information on validating data in forms and variables, see "Validating Data" on page 743 For detailed
information on validating query parameters, see "Using cfqueryparam" on page 416.


Passing variables to custom tags and UDFs
The following sections describe rules for how data gets passed to custom tags and user-defined functions that are
written in CFML, and to CFX custom tags that are written in Java or C++.


Passing variables to CFML tags and UDFs
When you pass a variable to a CFML custom tag as an attribute, or to a user-defined function as an argument, the
following rules determine whether the custom tag or function receives its own private copy of the variable or only gets
a reference to the calling page's variable:

* Simple variables and arrays are passed as copies of the data. If your argument is an expression that contains multiple
    simple variables, the result of the expression evaluation is copied to the function or tag.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         64
The CFML Programming Language





* Structures, queries, and cfobject objects are passed as references to the object.

If the tag or function gets a copy of the calling page's data, changes to the variable in the custom tag or function do not
change the value of the variable on the calling page. If the variable is passed by reference, changes to the variable in the
custom tag or function also change the value of the variable in the calling page.

To pass a variable to a custom tag, you must enclose the variable name in number signs. To pass a variable to a function,
do not enclose the variable name in number signs. For example, the following code calls a user-defined function using
three Form variables:

 <cfoutput>
     TOTAL INTEREST: #TotalInterest(Form.Principal, Form.AnnualPercent,Form.Months)#<br>
</cfoutput>

The following example calls a custom tag using two variables, MyString and MyArray:

 <cf_testTag stringval=#MyString# arrayval=#MyArray#>


Passing variables to CFX tags
You cannot pass arrays, structures, or cfobject objects to CFX tags. You can pass a query to a CFX tag by using the
query attribute when calling the tag. ColdFusion normally converts simple data types to strings when passing them to
CFX tags; however, the Java Request Interface getIntAttribute method lets you get a passed integer value.



Using Expressions and Number Signs

In CFML, you create expressions by using number signs to indicate expressions in Adobe ColdFusion tags such as
cfoutput, in strings, and in expressions. You also use variables in variable names and strings to create dynamic
expressions, and dynamic variables.


Expressions
ColdFusion expressions consist of operands and operators. Constants and variables are operands. Operators, such as
the multiplication sign, are the verbs that act on the operands; functions are a form of operator.

The simplest expression consists of a single operand with no operators. Complex expressions have multiple operators
and operands. The following are all ColdFusion expressions:

 12
MyVariable
a++
(1 + 1)/2
"father" & "Mother"
Form.divisor/Form.dividend
Round(3.14159)

Operators act on the operands. Some operators, such as functions with a single argument, take a single operand. Many
operators, including most arithmetic and logical operators, take two operands. The following is the general form of a
two-operand expression:

 Expression Operator Expression

Expressions surround the operator. Each expression can be a simple operand (variable or constant) or a subexpression
consisting of more operators and expressions. Complex expressions are built up using subexpressions. For example,
in the expression (1 + 1)/2, 1 + 1 is a subexpression consisting of an operator and two operands.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                            65
The CFML Programming Language




Operator types
ColdFusion has Five types of operators:

* Arithmetic

* Boolean

* Decision (or comparison)

* String

* Ternary

Functions also can be viewed as operators because they act on operands.


Arithmetic operators
The following table describes the arithmetic operators:


Operator                Description

+ - * /                 Basic arithmetic: Addition, subtraction, multiplication, and division.

                        In division, the right operand cannot be zero.

++ --                   Increment and decrement. Increase or decrease the variable by one.

                        These operators can be used for pre-incrementing or decrementing (as in x = ++ i), where the variable is
                        changed before it is used in the expression. They can also be used for post-incrementing or decrementing (as in
                        x = i++), where the value is changed after it is used in the expression. If the value of the variable i is initially 7,
                        for example, the value of x in x = ++i is 8 after expression evaluation, but in x=i++, the value of x is 7. In both
                        cases, the value of i becomes 8.

                        These operators cannot be used with expressions that involve functions, as in f().a++. Also, you can use an
                        expression such as -++x, but ---x and +++x cause errors, because their meanings are ambiguous. You can use
                        parentheses to group the operators, as in -(--x) or +(++x), however.

+= -= *= /= %=          Compound assignment operators. The variable on the right is used as both an element in the expression and the
                        result variable. Thus, the expression a += b is equivalent to a = a +b.

                        An expression can have only one compound assignment operator.

+ -                     Unary arithmetic: Set the sign of a number.

MOD                     Modulus: Return the remainder after a number is divided by a divisor. The result has the same sign as the divisor.
                        The value to the right of the operator should be an integer; using a non-numeric value causes an error, and if you
or %
                        specify a real number, ColdFusion ignores the fractional part (for example, 11 MOD 4.7 is 3).

\                       Integer division: Divide an integer by another integer. The result is also an integer; for example, 9\4 is 2. The right
                        operand cannot be zero.

^                       Exponentiation: Return the result of a number raised to a power (exponent). Use the caret character (^) to
                        separate the number from the power; for example, 2^3 is 8. Real and negative numbers are allowed for both the
                        base and the exponent. However, any expression that equates to an imaginary number, such -1^.5 results in the
                        string "-1.#IND. ColdFusion does not support imaginary or complex numbers.



Boolean operators
Boolean, or logical, operators perform logical connective and negation operations. The operands of Boolean operators
are Boolean (True/False) values. The following table describes the Boolean operators:




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                           66
The CFML Programming Language




Operator                Description

NOT                     Reverse the value of an argument. For example, NOT True is False and the inverse.

or !

AND                     Return True if both arguments are True; return False otherwise. For example, True AND True is True, but True AND
                        False is False.
or &&

OR                      Return True if any of the arguments is True; return False otherwise. For example, True OR False is True, but False
                        OR False is False.
or ||

XOR                     Exclusive or: Return True if one of the values is True and the other is False. Return False if both arguments are True
                        or both are False. For example, True XOR True is False, but True XOR False is True.

EQV                     Equivalence: Return True if both operands are True or both are False. The EQV operator is the opposite of the XOR
                        operator. For example, True EQV True is True, but True EQV False is False.

IMP                     Implication: The statement A IMP B is the equivalent of the logical statement "If A Then B." A IMP B is False only if
                        A is True and B is False. It is True in all other cases.



Decision operators
The ColdFusion decision, or comparison, operators produce a Boolean True/False result. Many types of operation
have multiple equivalent operator forms. For example, IS and EQ perform the same operation. The following table
describes the decision operators:


Operator                                    Description

IS                                          Perform a case-insensitive comparison of two values. Return True if the values are identical.

EQUAL

EQ

IS NOT                                      Opposite of IS. Perform a case-insensitive comparison of two values. Return True if the values
                                            are not identical.
NOT EQUAL

NEQ

CONTAINS                                    Return True if the value on the left contains the value on the right.

DOES NOT CONTAIN                            Opposite of CONTAINS. Return True if the value on the left does not contain the value on the
                                            right.

GREATER THAN                                Return True if the value on the left is greater than the value on the right.

GT

LESS THAN                                   Opposite of GREATER THAN. Return True if the value on the left is smaller than the value on the
                                            right.
LT

GREATER THAN OR EQUAL TO                    Return True if the value on the left is greater than or equal to the value on the right.

GTE

GE

LESS THAN OR EQUAL TO                       Return True if the value on the left is less than or equal to the value on the right.

LTE

LE




                                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 67
The CFML Programming Language




Note: In CFScript expressions only, you can also use the following decision operators. You cannot use them in expressions
in tags. == (EQ), != (NEQ), > (GT), < (LT), >= (GTE), and <= (LTE).


Decision operator rules
The following rules apply to decision operators:

* When ColdFusion evaluates an expression that contains a decision operator other than CONTAINS or DOES NOT
    CONTAIN, it first determines if the data can be converted to numeric values. If they can be converted, it performs
    a numeric comparison on the data. If they cannot be converted, it performs a string comparison. This can
    sometimes result in unexpected results. For more information on this behavior, see "Evaluation and type
    conversion issues" on page 53.

* When ColdFusion evaluates an expression with CONTAINS or DOES NOT CONTAIN it does a string
    comparison. The expression A CONTAINS B evaluates to True if B is a substring of A. Therefore an expression
    such as the following evaluates as True:

     123.45 CONTAINS 3.4

* When a ColdFusion decision operator compares strings, it ignores the case. As a result, the following expression is
    True:

     "a" IS "A"

* When a ColdFusion decision operator compares strings, it evaluates the strings from left to right, comparing the
    characters in each position according to their sorting order. The first position where the characters differ
    determines the relative values of the strings. As a result, the following expressions are True:

     "ab" LT "aba"
    "abde" LT "ac"


String operators
String operators manipulate strings of characters. The following table describes the operators:


 Operator            Description

 &                   Concatenates strings.

 &=                  Compound concatenation. The variable on the right is used as both an element in the concatenation operation and
                     the result variable. Thus, the expression a &= b is equivalent to a = a & b.

                     An expression can have only one compound assignment operator.


Note: In a Query of Queries, you use || as the concatenation operator.


Ternary Operator
The ternary operator is a decision operator with three operands. It assigns a variable a value based on a Boolean
expression. The operator has the form

 (Boolean expression)? expression1 : expresson2

If the Boolean expression evaluates to true, the operator result is expression1; otherwise, it is expression2

For example

 <cfset c = (a GT b)? a : b >

If a is greater than b, c is assigned the value of a; otherwise, c is assigned the value of b.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   68
The CFML Programming Language




The parentheses can contain any expression that evaluates to a Boolean value, and a and b can be any valid expression.
You can nest this operator inside other expressions.


Operator precedence and evaluation ordering
The order of precedence controls the order in which operators in an expression are evaluated. The order of precedence
is as follows. (Some alternative names for operators, such as EQUALS and GREATER THAN OR EQUAL TO are
omitted for brevity.)

 Unary +, Unary -
^
*, /
\
MOD
+, -
&
EQ, NEQ, LT, LTE, GT, GTE, CONTAINS, DOES NOT CONTAIN, ==, !=, >, >=, <, <=
NOT, !
AND, &&
OR, ||
XOR
EQV
IMP

To enforce a nonstandard order of evaluation, parenthesize expressions. For example:

* 6 - 3 * 2 is equal to 0

* (6 - 3) * 2 is equal to 6

You can nest parenthesized expressions. When in doubt about the order in which operators in an expression are
evaluated, use parentheses to force the order of evaluation.


Using functions as operators
Functions are a form of operator. Because ColdFusion functions return values, you can use function results as
operands. Function arguments are expressions. For example, the following are valid expressions:

* Rand()

* UCase("This is a text: ") & ToString(123 + 456)


Function syntax
The following table shows function syntax and usage guidelines:


 Usage                                  Example

 No arguments                           Function()

 Basic format                           Function(Data)

 Nested functions                       Function1(Function2(Data))

 Multiple arguments                     Function(Data1, Data2, Data3)

 String arguments                       Function('This is a demo')

                                        Function('This is a demo')

 Arguments that are expressions         Function1(X*Y, Function2("Text"))




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     69
The CFML Programming Language




All functions return values. In the following example, the cfset tag sets a variable to the value returned by the Now
function:

 <cfset myDate = DateFormat(Now(), "mmmm d, yyyy")>

You can use the values returned by functions directly to create more complex expressions, as in the following example:

 Abs(Myvar)/Round(3.14159)

For more information on how to insert functions in expressions, see "Using number signs" on page 70.


Optional function arguments
Some functions take optional arguments after their required arguments. If omitted, all optional arguments default to
a predefined value. For example:

* Replace("Eat and Eat", "Eat", "Drink") returns "Drink and Eat"

* Replace("Eat and Eat", "Eat", "Drink", "All") returns "Drink and Drink"

The difference in the results is because the Replace function takes an optional fourth argument that specifies the scope
of replacement. The default value is "One," which explains why only the first occurrence of "Eat" was replaced with
"Drink" in the first example. In the second example, a fourth argument causes the function to replace all occurrences
of "Eat" with "Drink".


Expression evaluation and functions
It is important to remember that ColdFusion evaluates function attributes as expressions before it executes the
function. As a result, you can use any ColdFusion expression as a function attribute. For example, consider the
following lines:

 <cfset firstVariable = "we all need">
<cfset myStringVar = UCase(firstVariable & " more sleep!")>

When ColdFusion server executes the second line, it does the following:

1 Identifies an expression with a string concatenation.

2 Evaluates the firstVariable variable as the string "we all need".

3 Concatenates "we all need" with the string "more sleep!" to get "we all need more sleep!".

4 Passes the string "we all need more sleep!" to the UCase function.

5 Executes the UCase function on the string argument "we all need more sleep!" to get "WE ALL NEED MORE
    SLEEP!".

6 Assigns the string value "WE ALL NEED MORE SLEEP!" to the variable myStringVar.

ColdFusion completes steps 1-3 before running the function.


Using multiple assignments in one expression
You can chain assignments to assign the same value to multiple variables in a single statement. This includes chain
assignments for the results of an expression. The following code displays a chain assignment:

a=b=c=d*5

You can use the var operator in multiple assignments, but the variables with this operator must precede all others. For
example:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       70
The CFML Programming Language




//The following line is valid.
var a = var b = c = d*5
//The following line is not valid.
// a = b = var c = d*5



Using number signs
Number signs (#) have a special meaning in CFML. When the ColdFusion server encounters number signs in CFML
text, such as the text in a cfoutput tag body, it checks to see if the text between the number signs is either a variable
or a function.

    Number signs are also called pound signs.


Is so, it replaces the text and surrounding number signs with the variable value or the result of the function. Otherwise,
ColdFusion generates an error.

For example, to output the current value of a variable named Form.MyFormVariable, you delimit (surround) the
variable name with number signs:

 <cfoutput>Value is #Form.MyFormVariable#</cfoutput>

In this example, the variable Form.MyFormVariable is replaced with the value assigned to it.

Follow these guidelines when using number signs:

* Use number signs to distinguish variables or functions from plain text.

* Surround only a single variable or function in number signs; for example, #Variables.myVar# or #Left(myString,
    position)#. (However, a function in number signs can contain nested functions, such as #Left(trim(myString),
    position)#.

* Do not place complex expressions, such as 1 + 2 in number signs. Although this is allowed in a cfoutput block,
    such as <cfoutput>One plus one is #1 + 1#</cfoutput>, doing so mixes logic and presentation.

* Use number signs only where necessary, because unneeded number signs slow processing.

For a description of using number signs to create variable names, see "Using number signs to construct a variable name
in assignments" on page 74.


Using number signs in ColdFusion tag attribute values
You can place variables, functions, or expressions inside tag attributes by enclosing the variable or expression with
number signs. For example, if the variable CookieValue has the value "MyCookie", the following line sets the
cfcookievalue attribute to "The value is MyCookie":

 <cfcookie name="TestCookie" value="The value is #CookieValue#">

You can optionally omit quotation marks around variables used as attribute values as shown in the following example:

 <cfcookie name = TestCookie value = #CookieValue#>

However, surrounding all attribute values in quotation marks is more consistent with HTML coding style.

If you use string expressions to construct an attribute value, as shown in the following example, the strings inside the
expression use single quotation marks (') to differentiate the quotation marks from the quotation marks that surround
the attribute value.

 <cfcookie name="TestCookie2" value="The #CookieValue & 'ate the cookie!'#">




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      71
The CFML Programming Language




Note: You do not need to use number signs when you use the cfset tag to assign one variable's value to another value. For
example, the following tag assigns the value of the oldVar variable to the new variable, newVar: <cfset newVar =
oldVar>.


Using number signs in tag bodies
You can place variables or functions freely inside the bodies of the following tags by enclosing each variable or
expression with number signs:

* cfoutput

* cfquery

* cfmail

For example:

 <cfoutput>
     Value is #Form.MyTextField#
</cfoutput>


<cfoutput>
     The name is #FirstName# #LastName#.
</cfoutput>


<cfoutput>
     The value of Cos(0) is #Cos(0)#
</cfoutput>

If you omit the number signs, the text, rather than the value, appears in the output generated by the cfoutput
statement.

Two expressions inside number signs can be adjacent to one another, as in the following example:

 <cfoutput>
     "Mo" and "nk" is #Left("Moon", 2)##Mid("Monkey", 3, 2)#
</cfoutput>

This code displays the following text:

"Mo" and "nk" is Monk

ColdFusion does not interpret the double number sign as an escaped # character.


Using number signs in strings
You can place variables or functions freely inside strings by enclosing each variable or expression with number signs;
for example:

 <cfset TheString = "Value is #Form.MyTextField#">
<cfset TheString = "The name is #FirstName# #LastName#.">
<cfset TheString = "Cos(0) is #Cos(0)#">

ColdFusion automatically replaces the text with the value of the variable or the value returned by the function. For
example, the following pairs of cfset statements produce the same result:

 <cfset TheString = "Hello, #FirstName#!">
<cfset TheString = "Hello, " & FirstName & "!">

If number signs are omitted inside the string, the text, rather than the value, appears in the string. For example, the
following pairs of cfset statements produce the same result:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   72
The CFML Programming Language




 <cfset TheString = "Hello, FirstName!">
<cfset TheString = "Hello, " & "First" & "Name!">

As with the cfoutput statement, two expressions can be adjacent to each other in strings, as in the following example:

 <cfset TheString = "Monk is #Left("Moon", 2)##Mid("Monkey", 3, 2)#">

The double-quotation marks around "Moon" and "Monkey" do not need to be escaped (as in ""Moon"" and
""Monkey""). This is because the text between the number signs is treated as an expression; it is evaluated before its
value is inserted inside the string.


Nested number signs
In a few cases, you can nest number signs in an expression. The following example uses nested number signs:

 <cfset Sentence = "The length of the full name is #Len("#FirstName# #LastName#")#">

In this example, number signs are nested so that the values of the variables FirstName and LastName are inserted in
the string whose length the Len function calculates.

Nested number signs imply a complex expression that can typically be written more clearly and efficiently without the
nesting. For example, you can rewrite the preceding code example without the nested number signs, as follows:

 <cfset Sentence2 = "The length of the full name is #Len(FirstName & " " & LastName)#">

The following achieves the same results and can further improve readability:

 <cfset FullName = "#FirstName# #LastName#">
<cfset Sentence = "The length of the full name is #Len(FullName)#">

A common mistake is to place number signs around the arguments of functions, as in:

 <cfset ResultText = "#Len(#TheText#)#">
<cfset ResultText = "#Min(#ThisVariable#, 5 + #ThatVariable#)#">
<cfset ResultText = "#Len(#Left("Some text", 4)#)#">

These statements result in errors. As a general rule, never place number signs around function arguments.


Using number signs in expressions
Use number signs in expressions only when necessary, because unneeded number signs reduce clarity and can increase
processing time. The following example shows the preferred method for referencing variables:

 <cfset SomeVar = Var1 + Max(Var2, 10 * Var3) + Var4>

In contrast, the following example uses number signs unnecessarily and is less efficient than the previous statement:

 <cfset #SomeVar# = #Var1# + #Max(Var2, 10 * Var3)# + #Var4#>



Dynamic expressions and dynamic variables
Many ColdFusion programmers never encounter or require dynamic expressions. However, dynamic variable naming
is important in situations where the variable names are not known in advance, such as in shopping cart applications.

ColdFusion also includes an IIf function, which is most often used without dynamic expressions. This function
dynamically evaluates its arguments, and you often must use the DE function to prevent the evaluation. For more
information on using the IIF function, see "Using the IIF function" on page 78.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         73
The CFML Programming Language




About dynamic variables
Dynamic variables are variables that are named dynamically, typically by creating a variable name from a static part
and a variable part. For example, the following example dynamically constructs the variable name from a variable
prefix and a static suffix:

 <cfset "#flavor#_availability" = "out of stock">

Using dynamic variables in this manner does not require dynamic evaluation.


About dynamic expressions and dynamic evaluation
In a dynamic expression, the actual expression, not just its variable values, is determined at execution time. In other
words, in a dynamic expression the structure of the expression, such as the names of the variables, not just the values
of the variables, gets built at runtime.

You create dynamic expressions using string expressions, which are expressions contained in strings, (that is,
surrounded with quotation marks). Dynamic evaluation is the process of evaluating a string expression. The Evaluate
and IIf functions, and only these functions, perform dynamic evaluation.

When ColdFusion performs dynamic evaluation it does the following:

1 Takes a string expression and treats it as a standard expression, as if the expression was not a string.

2 Parses the expression to determine the elements of the expression and validate the expression syntax.

3 Evaluates the expression, looking up any variables and replacing them with their values, calling any functions, and
   performing any required operations.

This process enables ColdFusion to interpret dynamic expressions with variable parts. However, it incurs a substantial
processing overhead.

Dynamic expressions were important in early versions of ColdFusion, before it supported arrays and structures, and
they still can be useful in limited circumstances. However, the ability to use structures and the ability to use associative
array notation to access structure elements provide more efficient and easier methods for dynamically managing data.
For information on using arrays and structures, see "Using Arrays and Structures" on page 82.


Selecting how to create variable names
The following two examples describe cases when you need dynamic variable names:

* Form applications where the number and names of fields on the form vary dynamically. In this case, the form posts
   only the names and values of its fields to the action page. The action page does not know all the names of the fields,
   although it does know how the field names (that is, the variable names) are constructed.

* If the following are true:

    * ColdFusion calls a custom tag multiple times.

    * The custom tag result must be returned to different variables each time.

    * The calling code can specify the variable in which to return the custom tag result.

   In this case, the custom tag does not know the return variable name in advance, and gets it as an attribute value.

In both cases, it might appear that dynamic expressions using the Evaluate function are needed to construct the
variable names. However, you can achieve the same ends more efficiently by using dynamic variable naming, as shown
in "Example: a dynamic shopping cart" on page 79.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      74
The CFML Programming Language




This does not mean that you must always avoid dynamic evaluation. However, given the substantial performance costs
of dynamic evaluation, first ensure that one of the following techniques cannot serve your purpose:

* An array (using index variables)

* Associative array references containing expressions to access structure elements

* Dynamically generated variable names


Dynamic variable naming without dynamic evaluation
While ColdFusion does not always allow you to construct a variable name inline from variable pieces, but it does allow
the most common uses.


Using number signs to construct a variable name in assignments
You can combine text and variable names to construct a variable name on the left side of a cfset assignment. For
example, the following code sets the value of the variable Product12 to the string "Widget":

 <cfset ProdNo = 12>
<cfset "Product#ProdNo#" = "Widget">

To construct a variable name this way, all the text on the left side of the equal sign must be in quotation marks.

This usage is less efficient than using arrays. The following example has the same purpose as the previous one, but
requires less processing:

 <cfset MyArray=ArrayNew(1)>
<cfset prodNo = 12>
<cfset myArray[prodNo] = "Widget">


Dynamic variable limitation
When you use a dynamic variable name in quotation marks on the left side of an assignment, the name must be either
a simple variable name or a complex name that uses object.property notation (such as MyStruct.#KeyName#). You
cannot use an array as part of a dynamic variable name. For example, the following code generates an error:

 <cfset MyArray=ArrayNew(1)>
<cfset productClassNo = 1>
<cfset productItemNo = 9>
<cfset "myArray[#productClassNo##productItemNo#]" = "Widget">

However, you can construct an array index value dynamically from variables without using quotation marks on the left
side of an assignment. For example, the preceding sample code works if you replace the final line with the following line:

 <cfset myArray[#productClassNo# & #productItemNo#] = "Widget">


Dynamically constructing structure references
The ability to use associative array notation to reference structures provides a way for you to use variables to
dynamically create structure references. (For a description of associative array notation, see "Structure notation" on
page 93.) Associative array structure notation allows you to use a ColdFusion expression inside the index brackets. For
example, if you have a productName structure with keys of the form product_1, product_2 and so on, you can use the
following code to display the value of productName.product_3:

 <cfset prodNo = 3>
<cfoutput>
     Product_3 Name: #ProductName["product_" & prodNo]#
<cfoutput>

For an example of using this format to manage a shopping cart, see "Example: a dynamic shopping cart" on page 79.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                          75
The CFML Programming Language




Using dynamic evaluation
Dynamic evaluation and dynamic expressions have several features and consideratons.


ColdFusion dynamic evaluation functions
The following table describes the functions that perform dynamic evaluation and are useful in evaluating dynamic
expressions:


 Function                      Purpose

 DE                            Escapes any double-quotation marks in the argument and wraps the result in double-quotation marks. The
                               DE function is particularly useful with the IIF function, to prevent the function from evaluating a string to
                               be output.

                               For an example of using the DE function with the IIF function, see "Using the IIF function" on page 78.

 Evaluate                      Takes one or more string expressions and dynamically evaluates their contents as expressions from left to
                               right. (The results of an evaluation to the left can have meaning in an expression to the right.) Returns the
                               result of evaluating the rightmost argument.

                               For more information on this function see "About the Evaluate function" on page 75.

 IIf                           Evaluates a Boolean condition expression. Depending on whether this expression is True or False,
                               dynamically evaluates one of two string expressions and returns the result of the evaluation. The IIF
                               function is convenient for incorporating a cfif tag in line in HTML.

                               For an example of using this function, see "Using the IIF function" on page 78.

 PrecisionEvaluate             Operates identically to the Evaluate function, except that it can calculate arbitrary precision decimal
                               arithmetic. If one or more operands in an arithmetic expression are decimal numbers, such as
                               12947834.986532, and are too long to be represented exactly by a ColdFusion numeric data type, the
                               function uses arbitrary-precision arithmetic to calculate the result, and return the result as an arbitrarily
                               long string of numbers. For more information about this function, see PrecisionEvaluate in the CFML
                               Reference.

 SetVariable                   Sets a variable identified by the first argument to the value specified by the second argument. This function
                               is no longer required in well-formed ColdFusion pages; see "SetVariable function considerations" on
                               page 78.



Function argument evaluation considerations
It is important to remember that ColdFusion always evaluates function arguments before the argument values are
passed to a function:

For example, consider the following DE function:

 <cfoutput>#DE("1" & "2")#</cfoutput>

You might expect this line to display """1"" & ""2""". Instead, it displays "12", because ColdFusion processes the line as
follows:

1 Evaluates the expression "1" & "2" as the string "12".

2 Passes the string "12" (without the quotation marks) to the DE function.

3 Calls the DE function, which adds literal quotation marks around the 12.

Similarly, if you use the expression DE(1 + 2), ColdFusion evaluates 1 + 2 as the integer 3 and passes it to the function.
The function converts it to a string and surrounds the string in literal quotation marks: "3".


About the Evaluate function
The Evaluate function takes one or more string expressions, dynamically evaluates their contents as expressions from
left to right, and returns the result of evaluating the rightmost argument.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   76
The CFML Programming Language




The following example shows the Evaluate function and how it works with ColdFusion variable processing:

 <cfset myVar2="myVar">
<cfset myVar="27/9">
<cfoutput>
     #myVar2#<br>
     #myVar#<br>
     #Evaluate("myVar2")#<br>
     #Evaluate("myVar")#<br>
     #Evaluate(myVar2)#<br>
     #Evaluate(myVar)#<br>
</cfoutput>


Reviewing the code
The following table describes how ColdFusion processes this code:


 Code                                    Description

 <cfset myVar2="myVar">                  Sets the two variables to the following strings:

 <cfset myVar="27/9">                    myVar

                                         27/9

 <cfoutput>                              Displays the values assigned to the variables, myVar and 27/9, respectively.

 #myVar2#<br/>

 #myVar#<br/>


 #Evaluate("myVar2")#<br>                Passes the string "myvar2" (without the quotation marks) to the Evaluate function, which
                                         does the following:

                                         1   Evaluates it as the variable myVar2.

                                         2   Returns the value of the myVar2 variable, the string "myvar" (without the quotation
                                         marks).

 #Evaluate("myVar")#<br>                 Passes the string "myvar" (without the quotation marks) to the Evaluate function, which
                                         does the following:

                                         1    Evaluates it as the variable myVar.

                                         2   Returns the value of the myVar variable, the string "27/9" (without the quotation marks).

 #Evaluate(myVar2)#<br>                  Evaluates the variable myVar2 as the string "myVar" and passes the string (without the
                                         quotation marks) to the Evaluate function. The rest of the processing is the same as in the
                                         previous line.

 #Evaluate(myVar)#<br/>                  Evaluates the variable myVar as the string "27/9" (without the quotation marks), and passes
                                         it to the Evaluate function, which does the following:
 </cfoutput>
                                         1    Evaluates the string as the expression 27/9

                                         2   Performs the division.

                                         3   Returns the resulting value, 3


As you can see, using dynamic expressions can result in substantial expression evaluation overhead, and the code can
be confusing. Therefore, you should avoid using dynamic expressions wherever a simpler technique, such as using
indexed arrays or structures can serve your purposes.


Avoiding the Evaluate function
Using the Evaluate function increases processing overhead, and in most cases it is not necessary. These examples
show some cases where you can consider using the Evaluate function:



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    77
The CFML Programming Language




Example 1
You might be inclined to use the Evaluate function in code such as the following:

 <cfoutput>1 + 1 is #Evaluate(1 + 1)#</cfoutput>

Although this code works, it is not as efficient as the following code:

 <cfset Result = 1 + 1>
<cfoutput>1 + 1 is #Result#</cfoutput>


Example 2
This example shows how you can use an associative array reference in place of an Evaluate function. This technique
is powerful because:

* Most ColdFusion scopes are accessible as structures.

* You can use ColdFusion expressions in the indexes of associative array structure references. For more information
   on using associative array references for structures, see "Structure notation" on page 93.

The following example uses the Evaluate function to construct a variable name:

 <cfoutput>
     Product Name: #Evaluate("Form.product_#i#")#
</cfoutput>

This code comes from an example where a form has entries for an indeterminate number of items in a shopping cart.
A product name field exists for each item in the shopping cart. The field name is of the form product_1, product_2,
and so on, where the number corresponds to the product entry in the shopping cart. In this example, ColdFusion does
the following:

1 Replaces the variable i with its value, for example 1.

2 concatenates the variable value with "Form.product_", and passes the result (for Form.product_1) to the Evaluate
   function, which does the remaining steps.

3 Parses the variable product_1 and generates an executable representation of the variable. Because ColdFusion must
   run its parser, this step requires substantial processing, even for a simple variable.

4 Evaluates the representation of the variable, for example as "Air popper".

5 Returns the value of the variable.

The following example has the same result as the preceding example and is more efficient:

 <cfoutput>
     ProductName: #Form["product_" & i]#
</cfoutput>

In this code, ColdFusion does the following:

1 Evaluates the expression in the associative array index brackets as the string "product_" concatenated with the value
   of the variable i.

2 Determines the value of the variable i; 1.

3 Concatenates the string and the variable value to get product_1.

4 Uses the result as the key value in the Form structure to get Form[product_1]. This associative array reference
   accesses the same value as the object.attribute format reference Form.product_1; in this case, Air popper.

This code format does not use any dynamic evaluation, but it achieves the same effect, of dynamically creating a
structure reference by using a string and a variable.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        78
The CFML Programming Language




SetVariable function considerations
You can avoid using the SetVariable function by using a format such as the following to set a dynamically named
variable. For example, the following lines are equivalent:

 <cfset SetVariable("myVar" & i, myVal)>
<cfset "myVar#i#" = myVal>

In the second line, enclosing the myVar#i# variable name in quotation marks tells ColdFusion to evaluate the name
and process any text in number signs as a variable or function. ColdFusion replaces the #i# with the value of the
variable i, so that if the value of i is 12, this code is equivalent to the line

 <cfset myVar12 = myVal>

For more information on this usage, see "Using number signs to construct a variable name in assignments" on page 74.


Using the IIF function
The IIf function is a shorthand for the following code:

 <cfif argument1>
     <cfset result = Evaluate(argument1)>
<cfelse>
     <cfset result = Evaluate(argument2)>
</cfif>

The function returns the value of the result variable. It is comparable to the use of the JavaScript and Java ? : operator,
and can result in more compact code. As a result, the IIF function can be convenient even if you are not using dynamic
expressions.

The IIF function requires the DE function to prevent ColdFusion from evaluating literal strings, as the following
example shows:

 <cfoutput>
     #IIf(IsDefined("LocalVar"), "LocalVar", DE("The variable is not defined."))#
</cfoutput>

If you do not enclose the string "The variable is not defined." in a DE function, the IIF function tries to evaluate the
contents of the string as an expression and generates an error (in this case, an invalid parser construct error).

The IIF function is useful for incorporating ColdFusion logic in line in HTML code, but it entails a processing time
penalty in cases where you do not otherwise need dynamic expression evaluation.

The following example shows using IIF to alternate table row background color between white and gray. It also shows
the use of the DE function to prevent ColdFusion from evaluating the color strings.

 <cfoutput>
     <table border="1" cellpadding="3">
           <cfloop index="i" from="1" to="10">
                <tr bgcolor="#IIF( i mod 2 eq 0, DE("white"), DE("gray") )#">
                <td>
                       hello #i#
                </td>
                </tr>
           </cfloop>
     </table>
</cfoutput>

This code is more compact than the following example, which does not use IIF or DE:




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    79
The CFML Programming Language




 <cfoutput>
<table border="1" cellpadding="3">
     <cfloop index="i" from="1" to="10">
          <cfif i mod 2 EQ 0>
                <cfset Color = "white">
          <cfelse>
                <cfset Color = "gray">
          </cfif>
          <tr bgcolor="#color#">
                <td>
                    hello #i#
                </td>
          </tr>
     </cfloop>
</table>
</cfoutput>


Example: a dynamic shopping cart
The following example dynamically creates and manipulates variable names without using dynamic expression
evaluation by using associative array notation.

You need to dynamically generate variable names in applications such as shopping carts, where the required output is
dynamically generated and variable. In a shopping cart, you do not know in advance the number of cart entries or their
contents. Also, because you are using a form, the action page only receives Form variables with the names and values
of the form fields.

The following example shows the shopping cart contents and lets you edit your order and submit it. To simplify things,
the example automatically generates the shopping cart contents using CFScript instead of having the user fill the cart.
A more complete example would populate a shopping cart as the user selected items. Similarly, the example omits all
business logic for committing and making the order.


Create the form
1 Create a file in your editor.

    <html>
   <head>
        <title>Shopping Cart</title>
   </head>
   <cfscript>
   CartItems=4;
   Cart = ArrayNew(1);
   for ( i=1; i LE cartItems; i=i+1)
   {
        Cart[i]=StructNew();
        Cart[i].ID=i;
        Cart[i].Name="Product " & i;
        Cart[i].SKU=i*100+(2*i*10)+(3*i);
        Cart[i].Qty=3*i-2;
   }
   </cfscript>


   <body>
   Your shopping cart has the following items.<br>
   You can change your order quantities.<br>
   If you don't want any item, clear the item's check box.<br>




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                    80
The CFML Programming Language




   When you are ready to order, click submit.<br>
   <br>
   <cfform name="ShoppingCart" action="ShoppingCartAction.cfm" method="post">
   <table>
        <tr>
        <td>Order?</td>
        <td>Product</td>
        <td>Code</td>
        <td>Quantity</td>
        </tr>
        <cfloop index="i" from="1" to="#cartItems#">
            <tr>
            <cfset productName= "product_" & Cart[i].ID>
            <cfset skuName= "sku_" & Cart[i].ID>
            <cfset qtyname= "qty_" & Cart[i].ID>
            <td><cfinput type="checkbox" name="itemID" value="#Cart[i].ID#" checked>
                    </td>
            <td><cfinput type="text" name="#productName#" value="#Cart[i].Name#"
                    passThrough = "readonly = 'True'"></td>
            <td><cfinput type="text" name="#skuName#" value="#Cart[i].SKU#"
                    passThrough = "readonly = 'True'"></td>
            <td><cfinput type="text" name="#qtyName#" value="#Cart[i].Qty#">
                    </td>
            </tr>
        </cfloop>
   </table>
   <input type="submit" name="submit" value="submit">
   </cfform>


   </body>
   </html>

2 Save the file as ShoppingCartForm.cfm.


Reviewing the code
The following table describes the code:




                                         

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       81
The CFML Programming Language




Code                                               Description

<cfscript>                                         Create a shopping cart as an array of structures, with each structure
CartItems=4;                                       containing the cart item ID, product name, SKU number, and quantity
Cart = ArrayNew(1);
                                                   ordered for one item in the cart. Populate the shopping cart by
for ( i=1; i LE cartItems; i=i+1)
                                                   looping CartItems times and setting the structure variables to
{
Cart[i]=StructNew();                               arbitrary values based on the loop counter. A real application would
Cart[i].ID=i;                                      set the Name, SKU, and Quantity values on other pages.
Cart[i].Name="Product " & i;
Cart[i].SKU=i*100+(2*i*10)+(3*i);
Cart[i].Qty=3*i-2;
}
</cfscript>

<cfform name="ShoppingCart"                        Start the form and its embedded table. When the user clicks the
action="ShoppingCartAction.cfm" method="post">     submit button, post the form data to the ShoppingCartAction.cfm
<table>
                                                   page.
<tr>
<td>Order?</td>                                    The table formats the form neatly. The first table row contains the
<td>Product</td>
                                                   column headers. Each following row has the data for one cart item.
<td>Code</td>
<td>Quantity</td>
</tr>

<cfloop index="i" from="1" to="#cartItems#">       Loop through the shopping cart entries to generate the cart form
<tr>                                               dynamically. For each loop, generate variables used for the form field
<cfset productName= "product_" & Cart[i].ID>
                                                   name attributes by appending the cart item ID (Cart[i].ID) to a field
<cfset skuName= "sku_" & Cart[i].ID>
                                                   type identifier, such as "sku_".
<cfset qtyname= "qty_" & Cart[i].ID>
<td><cfinput type="checkbox" name="itemID"
                                                   Use a single name, "itemID", for all check boxes. This way, the itemID
value="#Cart[i].ID#" checked>
 </td>                                             value posted to the action page is a list of all the check box field
<td><cfinput type="text" name="#productName#"      values. The check box field value for each item is the cart item ID.
value="#Cart[i].Name#"
 passThrough = "readonly = 'True'"></td>           Each column in a row contains a field for a cart item structure entry.
<td><cfinput type="text" name="#skuName#"          The passthrough attribute sets the product name and SKU fields to
value="#Cart[i].SKU#"                              read only; note the use of single-quotation marks. (For more
 passThrough = "readonly = 'True'"></td>
                                                   information on the cfinput tag passthrough attribute, see the CFML
<td><cfinput type="text" name="#qtyName#"
                                                   Reference.) The check boxes are selected by default.
value="#Cart[i].Qty#">
 </td>
</tr>
</cfloop>
</table>

<input type="submit" name="submit" value="submit"> Create the Submit button and end the form.
</cfform>



Create the Action page
1 Create a file in your editor.

2 Enter the following text:




                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    82
The CFML Programming Language




    <html>
   <head>
        <title>Your Order</title>
   </head>
   <body>
   <cfif isDefined("Form.submit")>
        <cfparam name="Form.itemID" default="">
        <cfoutput>
             You have ordered the following items:<br>
             <br>
             <cfloop index="i" list="#Form.itemID#">
                  ProductName: #Form["product_" & i]#<br>
                  Product Code: #Form["sku_" & i]#<br>
                  Quantity: #Form["qty_" & i]#<br>
                  <br>
             </cfloop>
        </cfoutput>
   </cfif>
   </body>
   </html>

3 Save the file as ShoppingCartAction.cfm

4 Open ShoppingCartform.cfm in your browser, change the check box and quantity values, and click Submit.


Reviewing the code
The following table describes the code:


Code                                            Description

 <cfif isDefined("Form.submit")>                Run the CFML on this page only if it is called by submitting a form. This is not needed
                                                if the form and action pages are separate, but is required if the form and action page
                                                were one ColdFusion page.

<cfparam name="Form.itemID" default="">         Set the default Form.itemID to the empty string. This prevents ColdFusion from
                                                displaying an error if the user clears all check boxes before submitting the form (so
                                                no product IDs are submitted).

<cfoutput>                                      Display the name, SKU number, and quantity for each ordered item.
 You have ordered the following items:<br>
 <br>                                           The form page posts Form.itemID as a list containing the value attributes of all the
 <cfloop index="i" list="#Form.itemID#">        check boxes. These attributes contain the shopping cart item IDs for the selected cart
 ProductName: #Form["product_" & i]#<br>        items. Use the list values to index a loop that outputs each ordered item.
 Product Code: #Form["sku_" & i]#<br>
 Quantity: #Form["qty_" & i]#<br>               Use associative array notation to access the Form scope as a structure and use
 <br>
                                                expressions in the array indexes to construct the form variable names. The
 </cfloop>
                                                expressions consist of a string containing the field name's field type prefix (for
 </cfoutput>
 </cfif>                                        example, "sku_"), concatenated with the variable i, which contains the shopping cart
                                                ItemID number (which is also the loop index variable).




Using Arrays and Structures

Adobe ColdFusion supports dynamic multidimensional arrays. Using arrays can enhance your ColdFusion
application code.

Adobe ColdFusion also supports structures for managing lists of key-value pairs. Because structures can contain other
structures or complex data types as it values, they provide a flexible and powerful tool for managing complex data.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        83
The CFML Programming Language




About arrays
Traditionally, an array is a tabular structure used to hold data, much like a spreadsheet table with clearly defined limits
and dimensions.

In ColdFusion, you typically use arrays to temporarily store data. For example, if your site lets users order goods
online, you can store their shopping cart contents in an array. Using an array lets you make changes easily without
committing the information, which the user can change before completing the transaction, to a database.


Basic array concepts
Subsequent discussions of ColdFusion arrays are based on the following terms:

Array dimension The relative complexity of the array structure.

Index The position of an element in a dimension, ordinarily surrounded by brackets: my1Darray[1],
my2Darray[1][1], my3Darray[1][1][1].

Array element: Data stored at an array index.

The simplest array is a one-dimensional array, like a row in a table. A one-dimensional array has a name (the variable
name) and a numeric index. The index number references a single entry, or cell, in the array.

Thus, the following statement sets the value of the fifth entry in the one-dimensional array MyArray to "Robert":

 <cfset MyArray[5] = "Robert">

A basic two-dimensional (2D) array is like a simple table. A three-dimensional (3D) array is like a cube of data, and so
on. ColdFusion lets you directly create arrays with up to three dimensions. You can use multiple statements to create
arrays with more than three dimensions.

The syntax my2darray[1][3]="Paul" is the same as saying "My2dArray is a two-dimensional array and the value of
the array element index [1][3] is `Paul'".


About ColdFusion arrays
ColdFusion arrays differ from traditional arrays, because they are dynamic. For example, in a conventional array, array
size is constant and symmetrical, whereas in a ColdFusion array, you can have rows of differing lengths based on the
data that is added or removed.

A conventional 2D array is like a fixed-size table made up of individual cells.

A ColdFusion 2D array is actually a one-dimensional array that contains a series of additional 1D arrays. Each of the
arrays that make up a row can expand and contract independently of any other column.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      84
The CFML Programming Language




The following figure represents a ColdFusion 2D array:




     a[1]                 a[1][1] a[1][2] a[1][3] a[1][4]



     a[2]                 a[2][1]                        a[2][5]



     a[3]                 a[3][1] a[3][2]



     a[4]                 a[4][1]         a[4][3]




A ColdFusion 3D array is essentially three nested sets of 1D arrays. The differences between traditional and
ColdFusion 3D arrays are similar, but much harder to show on a page.

Dynamic arrays expand to accept data that you add to them and contract as you remove data from them.


Basic array techniques

Referencing array elements
You reference array elements by enclosing the index with brackets: arrayName[x] where x is the index that you want
to reference. In ColdFusion, array indexes are counted starting with position 1, which means that position 1 in the
firstname array is referenced as firstname[1]. For 2D arrays, you reference an index by specifying two coordinates:
myarray[1][1].

You can use ColdFusion variables and expressions inside the brackets to reference an index, as the following example
shows:

 <cfset myArray=ArrayNew(1)>
<cfset myArray[1]="First Array Element">
<cfset myArray[1 + 1]="Second Array" & "Element">
<cfset arrayIndex=3>
<cfset arrayElement="Third Array Element">
<cfset myArray[arrayIndex]=arrayElement>
<cfset myArray[++arrayIndex]="Fourth Array Element">
<cfdump var=#myArray#>

Note: The IsDefined function does not test the existence of array elements. Instead, place any code that could try to
access an undefined array element in a try block and use a catch block to handle exceptions that arise if elements do not
exist.


Creating arrays
In ColdFusion, you can create arrays explicitly, by using a function to declare the array and then assigning it data, or
implicitly by using an assignment statement. You can create simple or complex, multidimensional arrays.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    85
The CFML Programming Language




Creating arrays using functions
To create an array explicitly, you use the arrayNew function and specify the array dimensions, as in the following
example:

 <cfset myNewArray=ArrayNew(2)>

This line creates a two-dimensional array named myNewArray. You use this method to create an array with up to three
dimensions.

After you create an array, you add array elements, which you can then reference by using the element indexes.

For example, suppose you create a one-dimensional array called firstname:

 <cfset firstname=ArrayNew(1)>

The array firstname holds no data and is of an unspecified length. Next you add data to the array:

 <cfset firstname[1]="Coleman">
<cfset firstname[2]="Charlie">
<cfset firstname[3]="Dexter">

After you add these names to the array, it has a length of 3.


Creating and using arrays implicitly
To create an array implicitly, you do not use the ArrayNew function. Instead, you use a new variable name on the left
side of an assignment statement, and array notation on the right side of the statement, as in the following example:

 <cfset firstnameImplicit=["Coleman","Charlie","Dexter"]>

This single statement is equivalent to the four statements used to create the firstname array in "Creating arrays using
functions" on page 85.

When you create an array implicitly, the right side of the assignment statement has brackets ([]) surrounding the array
contents and commas separating the individual array elements. The elements can be literal values, such as the strings
in the example, variables, or expressions. If you specify variables, do not place the variable names in quotation marks.

You can create an empty array implicitly, as in the following example:

 <cfset myArray = []>

You can also create an array implicitly by assigning a single entry, as the following example shows:

 <cfset chPar[1] = "Charlie">
<cfset chPar[2] = "Parker">

ColdFusion does not allow nested implicit creation of arrays, structures, or arrays and structures. Therefore, you
cannot create a multidimensional array in a single implicit statement. For example, neither of the following statements
is valid:

 <cfset myArray = [[],[]]>
<cfset jazzmen = [["Coleman","Charlie"],["Hawkins", "Parker"]]

To create a two-dimensional array, for example, use a format such as the following:

 <cfset ch = ["Coleman", "Hawkins"]>
<cfset cp = ["Charlie", "Parker"]>
<cfset dg = ["Dexter", "Gordon"]>
<cfset players = [ch, cp, dg]>

You cannot use a dynamic variable when you create an array implicitly. For example, the following expression
generates an error:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  86
The CFML Programming Language




 <cfset i="CP">
<cfset "#i#"=["Charlie","Parker"]>


Creating complex multidimensional arrays
ColdFusion supports dynamic multidimensional arrays. When you declare an array with the ArrayNew function, you
specify the number of dimensions. You can create an asymmetrical array or increase the number of dimensions by
nesting arrays as array elements.

It is important to know that when you assign one array (array1) to an element of another array (array2), array1 is
copied into array2. The original copy of array1 still exists, independent of array2. You can then change the contents of
the two arrays independently.

The best way to understand an asymmetrical array is by looking at it. The following example creates an asymmetric,
multidimensional, array, and the cfdump tag displays the resulting array structure. Several array elements do not yet
contain data.

 <cfset myarray=ArrayNew(1)>
<cfset myotherarray=ArrayNew(2)>
<cfset biggerarray=ArrayNew(3)>


<cfset biggerarray[1][1][1]=myarray>
<cfset biggerarray[1][1][1][10]=3>
<cfset biggerarray[2][1][1]=myotherarray>
<cfset biggerarray[2][1][1][4][2]="five deep">


<cfset biggestarray=ArrayNew(3)>
<cfset biggestarray[3][1][1]=biggerarray>
<cfset biggestarray[3][1][1][2][3][1]="This is complex">
<cfset myarray[3]="Can you see me">


<cfdump var=#biggestarray#><br>
<cfdump var=#myarray#>

Note: The cfdump tag displays the entire contents of an array. It is an excellent tool for debugging arrays and array-
handling code.


Reviewing the code
The following table describes the code:


 Code                                                              Description

 <cfset myarray=ArrayNew(1)>                                       Create three empty arrays, a 1D array, a 2D array, and a 3D array.
 <cfset myotherarray=ArrayNew(2)>
 <cfset biggerarray=ArrayNew(3)>

 <cfset biggerarray[1][1][1]=myarray>                              Make element [1][1][1] of the 3D biggerarray array be a copy
 <cfset biggerarray[1][1][1][10]=3>                                of the 1D array. Assign 3 to the [1][1][1][10] element of the
                                                                   resulting array.

                                                                   The biggerarray array is now asymmetric. For example, it does
                                                                   not have a [1][1][2][1] element.

 <cfset biggerarray[2][1][1]=myotherarray>                         Make element [2][1][1] of the 3D array be the 2D array, and
 <cfset biggerarray[2][1][1][4][2]="five deep">                    assign the [2][1][1][4][2] element the value "five deep".

                                                                   The biggerarray array is now even more asymmetric.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                            87
The CFML Programming Language




 Code                                                            Description

 <cfset biggestarray=ArrayNew(3)>                                Create a second 3D array. Make the [3][1][1] element of this
 <cfset biggestarray[3][1][1]=biggerarray>                       array a copy of the biggerarray array, and assign element
 <cfset biggestarray[3][1][1][2][3][1]="This is complex">
                                                                 [3][1][1][2][3][1].

                                                                 The resulting array is complex and asymmetric.

 <cfset myarray[3]="Can you see me">                             Assign a value to element [3] of myarray.

 <cfdump var=#biggestarray#><br>                                 Use cfdump to view the structure of biggestarray and myarray.
 <cfdump var=#myarray#>
                                                                 Notice that the "Can you see me" entry appears in myarray, but
                                                                 not in biggestarray, because biggestarray has a copy of the
                                                                 original myarray values and the change to myarray does not
                                                                 affect it.



Using implicitly created arrays
You can use implicitly created arrays directly in functions (including user-defined functions) and tags. For example,
the following code uses two implicit arrays, one in a ColdFusion function, the other in a user-defined function:

 <cffunction name="sumarray">
      <cfargument name="inarray" type="array">
         <cfset result = 0>
      <cfloop array="#inarray#" index="i" >
          <cfset result += i>
          </cfloop>
          <cfreturn result>
</cffunction>


<cfoutput>
Summed Implicit array [#ArrayToList([1,2,3,4,5,6])#]: #sumarray([1,2,3,4,5,6])#<br />
</cfoutput>


Adding elements to an array
You can add an element to an array by assigning the element a value or by using a ColdFusion function.


Adding an array element by assignment
You can add elements to an array by defining the value of an array element, as shown in the following cfset tag:

 <cfset myarray[5]="Test Message">

If an element does not exist at the specified index, ColdFusion creates it. If an element exists at the specified index,
ColdFusion replaces it with the new value. To prevent existing data from being overwritten, use the ArrayInsertAt
function, as described in the next section.

If elements with lower-number indexes do not exist, they remain undefined. Assign values to undefined array elements
before you can use them. For example, the following code creates an array and an element at index 4. It outputs the
contents of element 4, but generates an error when it tries to output the (nonexistent) element 3.

 <cfset myarray=ArrayNew(1)>
<cfset myarray[4]=4>
<cfoutput>
      myarray4: #myarray[4]#<br>
      myarray3: #myarray[3]#<br>
</cfoutput>




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    88
The CFML Programming Language




Adding an array element with a function
You can use the following array functions to add data to an array:


 Function               Description

 ArrayAppend            Creates an array element at the end of the array.

 ArrayPrepend           Creates an array element at the beginning of the array.

 ArrayInsertAt          Inserts an array element at the specified index position.


Because ColdFusion arrays are dynamic, if you add or delete an element from the array, any higher-numbered index
values all change. For example, the following code creates a two element array and displays the array contents. It then
uses ArrayPrepend to insert a new element at the beginning of the array and displays the result. The data that was
originally in indexes 1 and 2 is now in indexes 2 and 3.

 <!--- Create an array with three elements. --->
<cfset myarray=ArrayNew(1)>
<cfset myarray[1]="Original First Element">
<cfset myarray[2]="Original Second Element">
<!--- Use cfdump to display the array structure --->
<cfdump var=#myarray#>
<br>
<!--- Add a new element at the beginning of the array. --->
<cfscript>
     ArrayPrepend(myarray, "New First Element");
</cfscript>
<!--- Use cfdump to display the new array structure. --->
<cfdump var=#myarray#>

For more information about these array functions, see the CFML Reference.


Deleting elements from an array
Use the ArrayDeleteAt function to delete data from the array at a particular index, instead of setting the data value
to zero or an empty string. If you remove data from an array, the array resizes dynamically, as the following example
shows:

 <!--- Create an array with three elements --->
<cfset firstname=ArrayNew(1)>
<cfset firstname[1]="Robert">
     <cfset firstname[2]="Wanda">
     <cfset firstname[3]="Jane">
<!--- Delete the second element from the array --->
<cfset temp=ArrayDeleteAt(firstname, 2)>
<!--- Display the array length (2) and its two entries,
     which are now "Robert" and "Jane" --->
<cfoutput>
     The array now has #ArrayLen(firstname)# indexes<br>
     The first entry is #firstname[1]#<br>
            The second entry is #firstname[2]#<br>
     </cfoutput>

The ArrayDeleteAt function removed the original second element and resized the array so that it has two entries,
with the second element now being the original third element.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     89
The CFML Programming Language




Copying arrays
You can copy arrays of simple variables (numbers, strings, Boolean values, and date-time values) by assigning the
original array to a new variable name. You do not have to use ArrayNew to create the array first. When you assign the
existing array to a new variable, ColdFusion creates an array and copies the contents of the old array to the new array.
The following example creates and populates a two-element array. It then copies the original array, changes one
element of the copied array and dumps both arrays. As you can see, the original array is unchanged and the copy has
a new second element.

 <cfset myArray=ArrayNew(1)>
<cfset myArray[1]="First Array Element">
<cfset myArray[2]="Second Array Element">
<cfset newArray=myArray>
<cfset newArray[2]="New Array Element 2">
<cfdump var=#myArray#><br>
<cfdump var=#newArray#>

If your array contains complex variables (structures, query objects, or external objects such as COM objects) assigning
the original array to a new variable does not make a complete copy of the original array. The array structure is copied;
however, the new array does not get its own copy of the complex data, only references to it. To demonstrate this
behavior, run the following code:

 Create an array that contains a structure.<br>
<cfset myStruct=StructNew()>
<cfset myStruct.key1="Structure key 1">
<cfset myStruct.key2="Structure key 2">
<cfset myArray=ArrayNew(1)>
<cfset myArray[1]=myStruct>
<cfset myArray[2]="Second array element">
<cfdump var=#myArray#><br>
<br>
Copy the array and dump it.<br>
<cfset myNewArray=myArray>
<cfdump var=#myNewArray#><br>
<br>
Change the values in the new array.<br>
<cfset myNewArray[1].key1="New first array element">
<cfset myNewArray[2]="New second array element">
<br>
Contents of the original array after the changes:<br>
<cfdump var=#myArray#><br>
Contents of the new array after the changes:<br>
<cfdump var=#myNewArray#>

The change to the new array also changes the contents of the structure in the original array.

To make a complete copy of an array that contains complex variables, use the Duplicate function.


Populating arrays with data
Array elements can store any values, including queries, structures, and other arrays. You can use assignment
statements to populate an array. You can also use several of functions to populate an array with data, including
ArraySet, ArrayAppend, ArrayInsertAt, and ArrayPrepend. These functions are useful for adding data to an
existing array.

In particular, consider using the following techniques:

* Populating an array with the ArraySet function


                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         90
The CFML Programming Language




* Populating an array with the cfloop tag

* Populating an array from a query


Populating an array with the ArraySet function
You can use the ArraySet function to populate a 1D array, or one dimension of a multidimensional array, with some
initial value, such as an empty string or zero. This can be useful to create an array of a certain size, without adding data
to it right away. One reason to do this is so that you can reference all the array indexes. If you reference an array index
that does not contain some value, such as an empty string, you get an error.

The ArraySet function has the following form:

 ArraySet (arrayname, startrow, endrow, value)

The following example initializes the array myarray, indexes 1 - 100, with an empty string:

 ArraySet (myarray, 1, 100, "")


Populating an array with the cfloop tag
The cfloop tag provides a common and efficient method for populating an array. The following example uses a
cfloop tag and the MonthAsString function to populate a simple 1D array with the names of the months. A second
cfloop outputs data in the array to the browser.

 <cfset months=arraynew(1)>


<cfloop index="loopcount" from=1 to=12>
      <cfset months[loopcount]=MonthAsString(loopcount)>
</cfloop>


<cfloop index="loopcount" from=1 to=12>
      <cfoutput>
            #months[loopcount]#<br>
      </cfoutput>
</cfloop>


Using nested loops for 2D and 3D arrays
To output values from 2D and 3D arrays, employ nested loops to return array data. With a one-dimensional (1D)
array, a single cfloop is sufficient to output data, as in the previous example. With arrays of dimension greater than
one, you maintain separate loop counters for each array level.


Nesting cfloop tags for a 2D array
The following example shows how to handle nested cfloop tags to output data from a 2D array. It also uses nested
cfloop tags to populate the array:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   91
The CFML Programming Language




 <cfset my2darray=arraynew(2)>
<cfloop index="loopcount" from=1 to=12>
     <cfloop index="loopcount2" from=1 to=2>
           <cfset my2darray[loopcount][loopcount2]=(loopcount * loopcount2)>
     </cfloop>
</cfloop>


<p>The values in my2darray are currently:</p>


<cfloop index="OuterCounter" from="1" to="#ArrayLen(my2darray)#">
     <cfloop index="InnerCounter" from="1"to="#ArrayLen(my2darray[OuterCounter])#">
           <cfoutput>
                <b>[#OuterCounter#][#InnerCounter#]</b>:
                #my2darray[OuterCounter][InnerCounter]#<br>
           </cfoutput>
     </cfloop>
</cfloop>


Nesting cfloop tags for a 3D array
For 3D arrays, you simply nest an additional cfloop tag. (This example does not set the array values first to keep the
code short.)

 <cfloop index="Dim1" from="1" to="#ArrayLen(my3darray)#">
     <cfloop index="Dim2" from="1" to="#ArrayLen(my3darray[Dim1])#">
           <cfloop index="Dim3" from="1"to="#ArrayLen(my3darray[Dim1][Dim2])#">
                <cfoutput>
                     <b>[#Dim1#][#Dim2#][#Dim3#]</b>:
                           #my3darray[Dim1][Dim2][Dim3]#<br>
                </cfoutput>
           </cfloop>
     </cfloop>
</cfloop>


Populating an array from a query
When populating an array from a query, remember the following:

* You cannot add query data to an array all at once. A looping structure is often required to populate an array from
   a query.

* You can reference query column data using array-like syntax. For example, myquery.col_name[1] references data
   in the first row in the col_name column of the myquery query.

* Inside a cfloopquery= loop, you do not have to specify the query name to reference the query variables.

You can use a cfset tag with the following syntax to define values for array indexes:

 <cfset arrayName[index]=queryColumn[row]>

In the following example, a cfloop tag places four columns of data from a sample data source into an array, myarray.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       92
The CFML Programming Language




<!--- Do the query --->
<cfquery name="test" datasource="cfdocexamples">
     SELECT Emp_ID, LastName, FirstName, Email
     FROM Employees
</cfquery>


<!--- Declare the array --->
<cfset myarray=arraynew(2)>


<!--- Populate the array row by row --->
<cfloop query="test">
     <cfset myarray[CurrentRow][1]=Emp_ID>
     <cfset myarray[CurrentRow][2]=LastName>
     <cfset myarray[CurrentRow][3]=FirstName>
     <cfset myarray[CurrentRow][4]=Email>
</cfloop>


<!--- Now, create a loop to output the array contents --->
<cfset total_records=test.recordcount>
<cfloop index="Counter" from=1 to="#Total_Records#">
     <cfoutput>
         ID: #MyArray[Counter][1]#,
         LASTNAME: #MyArray[Counter][2]#,
         FIRSTNAME: #MyArray[Counter][3]#,
         EMAIL: #MyArray[Counter][4]# <br>
     </cfoutput>
</cfloop>

This example uses the query object built-in variable CurrentRow to index the first dimension of the array.


Array functions
The following functions are available for creating, editing, and handling arrays:


Function              Description

ArrayAppend           Appends an array element to the end of a specified array.

ArrayAvg              Returns the average of the values in the specified array.

ArrayClear            Deletes all data in a specified array.

ArrayDeleteAt         Deletes an element from a specified array at the specified index and resizes the array.

ArrayInsertAt         Inserts an element (with data) in a specified array at the specified index and resizes the array.

ArrayIsDefined        Returns True if the specified array is defined.

ArrayIsEmpty          Returns True if the specified array is empty of data.

ArrayLen              Returns the length of the specified array.

ArrayMax              Returns the largest numeric value in the specified array.

ArrayMin              Returns the smallest numeric value in the specified array.

ArrayNew              Creates an array of specified dimension.

ArrayPrepend          Adds an array element to the beginning of the specified array.

ArrayResize           Resets an array to a specified minimum number of elements.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     93
The CFML Programming Language




 Function                Description

 ArraySet                Sets the elements in a 1D array in a specified range to a specified value.

 ArraySort               Returns the specified array with elements sorted numerically or alphanumerically.

 ArraySum                Returns the sum of values in the specified array.

 ArraySwap               Swaps array values in the specified indexes.

 ArrayToList             Converts the specified 1D array to a list, delimited with the character you specify.

 IsArray                 Returns True if the value is an array.

 ListToArray             Converts the specified list, delimited with the character you specify, to an array.


For more information about each of these functions, see the CFML Reference.

If a function returns an array, you can now reference a specific element array directly in the function call statement.
For example, the following line references the fifth element of the array returned by the myFunc() function:

myFunc()[5]



About structures
ColdFusion structures consist of key-value pairs. Structures let you build a collection of related variables that are
grouped under a single name. You can define ColdFusion structures dynamically.

You can use structures to reference related values as a unit, rather than individually. To maintain employee lists, for
example, you can create a structure that holds personnel information such as name, address, phone number, ID
numbers, and so on. Then you can reference this collection of information as a structure called employee rather than
as a collection of individual variables.

A structure key must be a string. The values associated with the key can be any valid ColdFusion value or object. It can
be a string or integer, or a complex object such as an array or another structure. Because structures can contain any
types of data, they provide a powerful and flexible mechanism for representing complex data.


Structure notation
ColdFusion supports three types of notation for referencing structure contents. The notation that you use depends on
your requirements.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                          94
The CFML Programming Language




Notation                      Description

Object.property               You can reference a property, prop, of an object, obj, as obj.prop. This notation, also called dot notation, is
                              useful for simple assignments, as in this example:

                              depts.John="Sales"

                              Use this notation only when you know the property names (keys) in advance and they are strings, with no
                              special characters, numbers, or spaces. You cannot use the dot notation when the property, or key, is
                              dynamic.

Associative arrays            If you do not know the key name in advance, or it contains spaces, numbers, or special characters, you can
                              use associative array notation. This notation uses structures as arrays with string indexes; for example:

                              depts["John"]="Sales"

                              depts[employeeName] = "Sales"

                              You can use a variable (such as employeeName) as an associative array index. Therefore, enclose any literal
                              key names in quotation marks.

                              For information on using associative array references containing variables, see "Dynamically constructing
                              structure references" on page 74.

Structure                     Use structure notation only when you create structures and set their initial values, not when you are
                              accessing or updating structure data, and only on the right side of an assignment expression. This notation
                              has the following format:

                              {keyName=value[,keyName=value]...}

                              where the square braces ([]) and ellipses (...) indicate optional contents that can be repeated.

                              The following example creates a structure that uses structure notation:

                              <cfset name={firstName = "John", lastName = "Smythe"}>



Referencing complex structures
When a structure contains another structure, you reference the data in the nested structure by extending either
object.property or associative array notation. You can even use a mixture of both notations.

For example, if structure1 has a key key1 whose value is a structure that has keys struct2key1, struct2key2, and so on,
you can use any of the following references to access the data in the first key of the embedded structure:

Structure1.key1.Struct2key1
Structure1["key1"].Struct2key1
Structure1.key1["Struct2key1"]
Structure1["key1"]["Struct2key1"]

The following example shows various ways you can reference the contents of a complex structure:




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    95
The CFML Programming Language




<cfset myArray=ArrayNew(1)>
<cfset myArray[1]="2">
<cfset myArray[2]="3">
<cfset myStruct2=StructNew()>
<cfset myStruct2.struct2key1="4">
<cfset myStruct2.struct2key2="5">
<cfset myStruct=StructNew()>
<cfset myStruct.key1="1">
<cfset myStruct.key2=myArray>
<cfset myStruct.key3=myStruct2>
<cfdump var=#myStruct#><br>


<cfset key1Var="key1">
<cfset key2Var="key2">
<cfset key3Var="key3">
<cfset var2="2">


<cfoutput>
Value of the first key<br>
#mystruct.key1#<br>
#mystruct["key1"]#<br>
#mystruct[key1Var]#<br>
<br>
Value of the second entry in the key2 array<br>
#myStruct.key2[2]#<br>
#myStruct["key2"][2]#<br>
#myStruct[key2Var][2]#<br>
#myStruct[key2Var][var2]#<br>
<br>
Value of the struct2key2 entry in the key3 structure<br>
#myStruct.key3.struct2key2#<br>
#myStruct["key3"]["struct2key2"]#<br>
#myStruct[key3Var]["struct2key2"]#<br>
#myStruct.key3["struct2key2"]#<br>
#myStruct["key3"].struct2key2#<br>
<br>
</cfoutput>


Reviewing the code
The following table describes the code:


Code                                               Description

<cfset myArray=ArrayNew(1)>                        Create a structure with three entries: a string, an array, and an
<cfset myArray[1]="2">                             embedded structure.
<cfset myArray[2]="3">
<cfset myStruct2=StructNew()>
<cfset myStruct2.struct2key1="4">
<cfset myStruct2.struct2key2="5">
<cfset myStruct=StructNew()>
<cfset myStruct.key1="1">
<cfset myStruct.key2=myArray>
<cfset myStruct.key3=myStruct2>

<cfdump var=#myStruct#><br>                        Display the complete structure.

<cfset key1Var="key1">                             Create variables containing the names of the myStruct keys and the
<cfset key2Var="key2">                             number 2.
<cfset key3Var="key3">




                                         

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               96
The CFML Programming Language




Code                                                        Description

<cfoutput>                                                  Output the value of the key1 (string) entry using the following
 Value of the first key<br>                                 notation:
 #mystruct.key1#<br>
 #mystruct["key1"]#<br>                                     * object.property notation
 #mystruct[key1Var]#<br>
 <br>
                                                            * associative array notation with a constant

                                                            * associative array notation with a variable

 <br>                                                       Output the value of the second entry in the key2 array using the
 Value of the second entry in the key2 array<br>            following notation:
 #myStruct.key2[2]#<br>
 #myStruct["key2"][2]#<br>                                  * object.property notation
 #myStruct[key2Var][2]#<br>
 #myStruct[key2Var][var2]#<br>
                                                            * associative array notation with a constant
 <br>

                                                            * associative array notation with a variable

                                                            * associative array notation with variables for both the array and the
                                                              array index

 Value of the struct2key2 entry in the key3                 Output the value of second entry in the key3 embedded structure
structure<br>                                               using the following notation:
 #myStruct.key3.struct2key2#<br>
 #myStruct["key3"]["struct2key2"]#<br>                      * object.property notation
 #myStruct[key3Var]["struct2key2"]#<br>
 #myStruct.key3["struct2key2"]#<br>
                                                            * associative array notation with two constants
 #myStruct["key3"].struct2key2#<br>
 <br>
                                                            * associative array notation with a variable and a constant

                                                            * object.property notation followed by associative array notation

                                                            * associative array notation followed by object.property notation


Creating and using structures
The sample code in the following discussions uses a structure called employee, which is used to add new employees to
a corporate information system.


Creating structures
In ColdFusion, you can create structures explicitly by using a function, and then populate the structure using
assignment statements or functions, or you can create the structure implicitly by using an assignment statement.


Creating structures using functions
You can create structures by assigning a variable name to the structure with the StructNew function as follows:

 <cfset structName = StructNew()>

For example, to create a structure named departments, use the following syntax:

 <cfset departments = StructNew()>

This statement creates an empty structure to which you can add data.


Creating structures implicitly
You can create an empty structure implicitly, as in the following example:

 <cfset myStruct = {}>




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    97
The CFML Programming Language




You can also create a structure by assigning data to a variable. For example, each of the following lines creates a
structure named myStruct with one element, name, that has the value Adobe Systems Incorporated.

 <cfset coInfo.name = "Adobe Systems Incorporated">
<cfset coInfo["name"] = "Adobe Systems Incorporated">
<cfset coInfo = {name = "Adobe Systems Incorporated"}>

When you use structure notation to create a structure, as shown in the third example, you can populate multiple
structure fields. The following example shows this use:

 <cfset coInfo={name="Adobe Systems Incorporated", industry="software"}

ColdFusion does not allow nested implicit creation of structures, arrays, or structures and arrays. The following line,
for example, generates an error:

 <cfset myStruct = {structKey1 = {innerStructKey1 = "innerStructValue1"}}>

Similarly, you cannot use object.property notation on the left side of assignments inside structure notation. The
following statement, for example, causes an error:

 <cfset myStruct={structKey1.innerStructKey1 = "innerStructValue1"}>

Instead of using these formats, use multiple statements, such as the following:

 <cfset innerStruct1 = {innerStructKey1 = "innerStructValue1"}
<cfset myStruct1={structKey1 = innerStruct1}>

You cannot use a dynamic variable when you create a structure implicitly. For example, the following expression
generates an error:

 <cfset i="coInfo">
<cfset "#i#"={name = ""Adobe Systems Incorporated"}>


Using implicitly created structures in functions and tags
You can use implicitly created structures directly in functions (including user-defined functions) and tags. For
example, the following code dumps an implicitly created structure.

 <cfdump var="#{Name ="28 Weeks Later", Time = "7:45 PM"}#">

You can use array notation inside the structure notation, as shown in the following example:

<cfset student = {firstName="Jane", lastName="Janes", grades=[91, 78, 87]}>


Adding and updating structure elements
You add or update a structure element to a structure by assigning the element a value or by using a ColdFusion
function. It is simpler and more efficient to use direct assignment.

You can add structure key-value pairs by defining the value of the structure key, as the following example shows:

 <cfset myNewStructure.key1="A new structure with a new key">
<cfdump var=#myNewStructure#>
<cfset myNewStructure.key2="Now I've added a second key">
<cfdump var=#myNewStructure#>

The following code uses cfset and object.property notation to create a structure element called departments.John,
and changes John's department from Sales to Marketing. It then uses associative array notation to change his
department to Facilities. Each time the department changes, it displays the results:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      98
The CFML Programming Language




 <cfset departments=structnew()>
<cfset departments.John = "Sales">
<cfoutput>
     Before the first change, John was in the #departments.John# Department<br>
</cfoutput>
<cfset Departments.John = "Marketing">
<cfoutput>
     After the first change, John is in the #departments.John# Department<br>
</cfoutput>
<cfset Departments["John"] = "Facilities">
<cfoutput>
     After the second change, John is in the #departments.John# Department<br>
</cfoutput>


Getting information about structures and keys
You use ColdFusion functions to find information about structures and their keys.


Getting information about structures
To find out if a given value represents a structure, use the IsStruct function, as follows:

 IsStruct(variable)

This function returns True if variable is a ColdFusion structure. (It also returns True if variable is a Java object that
implements the java.util.Map interface.)

Structures are not indexed numerically, so to find out how many name-value pairs exist in a structure, use the
StructCount function, as in the following example:

 StructCount(employee)

To discover whether a specific Structure contains data, use the StructIsEmpty function, as follows:

 StructIsEmpty(structure_name)

This function returns True if the structure is empty, and False if it contains data.


Finding a specific key and its value
To determine whether a specific key exists in a structure, use the StructKeyExists function, as follows:

 StructKeyExists(structure_name, "key_name")

Do not place the name of the structure in quotation marks, but you do place the key name in quotation marks. For
example, the following code displays the value of the MyStruct.MyKey only if it exists:

 <cfif StructKeyExists(myStruct, "myKey")>
     <cfoutput> #mystruct.myKey#</cfoutput><br>
</cfif>

You can use the StructKeyExists function to dynamically test for keys by using a variable to represent the key name.
In this case, you do not place the variable in quotation marks. For example, the following code loops through the
records of the GetEmployees query and tests the myStruct structure for a key that matches theLastName field of the
query. If ColdFusion finds a matching key, it displays the Last Name from the query and the corresponding entry in
the structure.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                               99
The CFML Programming Language




 <cfloop query="GetEmployees">
<cfif StructKeyExists(myStruct, LastName)>
     <cfoutput>#LastName#: #mystruct[LastName]#</cfoutput><br>
</cfif>
</cfloop>

If the name of the key is known in advance, you can also use the ColdFusion IsDefined function, as follows:

 IsDefined("structure_name.key")>

However, if the key is dynamic, or contains special characters, use the StructKeyExists function.

Note: Using StructKeyExists to test for the existence of a structure entry is more efficient than using IsDefined.
ColdFusion scopes are available as structures and you can improve efficiency by using StructKeyExists to test for the
existence of variables.


Getting a list of keys in a structure
To get a list of the keys in a CFML structure, you use the StructKeyList function, as follows:

 <cfset temp=StructKeyList(structure_name, [delimiter])>

You can specify any character as the delimiter; the default is a comma.

Use the StructKeyArray function to returns an array of keys in a structure, as follows:

 <cfset temp=StructKeyArray(structure_name)>

Note: The StructKeyList and StructKeyArray functions do not return keys in any particular order. Use the
ListSort or ArraySort functions to sort the results.


Copying structures
ColdFusion provides several ways to copy structures and create structure references. The following table lists these
methods and describes their uses:


Technique                 Use

Duplicate function        Makes a complete copy of the structure. All data is copied from the original structure to the new structure,
                          including the contents of structures, queries, and other objects. As a result changes to one copy of the structure
                          have no effect on the other structure.

                          This function is useful when you want to move a structure completely into a new scope. In particular, if a structure
                          is created in a scope that requires locking (for example, Application), you can duplicate it into a scope that does
                          not require locking (for example, Request), and then delete it in the scope that requires locking.

StructCopy function       Makes a shallow copy of a structure. It creates a structure and copies all simple variable and array values at the top
                          level of the original structure to the new structure. However, it does not make copies of any structures, queries, or
                          other objects that the original structure contains, or of any data inside these objects. Instead, it creates a reference
                          in the new structure to the objects in the original structure. As a result, any change to these objects in one
                          structure also changes the corresponding objects in the copied structure.

                          The Duplicate function replaces this function for most, if not all, purposes.

Variable assignment       Creates an additional reference, or alias, to the structure. Any change to the data using one variable name changes
                          the structure that you access using the other variable name.

                          This technique is useful when you want to add a local variable to another scope or otherwise change the scope of
                          a variable without deleting the variable from the original scope.


The following example shows the different effects of copying, duplicating, and assigning structure variables:




                                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                               100
The CFML Programming Language




Create a structure<br>
<cfset myNewStructure=StructNew()>
<cfset myNewStructure.key1="1">
<cfset myNewStructure.key2="2">
<cfset myArray=ArrayNew(1)>
<cfset myArray[1]="3">
<cfset myArray[2]="4">
<cfset myNewStructure.key3=myArray>
<cfset myNewStructure2=StructNew()>
<cfset myNewStructure2.Struct2key1="5">
<cfset myNewStructure2.Struct2key2="6">
<cfset myNewStructure.key4=myNewStructure2>
<cfdump var=#myNewStructure#><br>
<br>
A StructCopy copied structure<br>
<cfset CopiedStruct=StructCopy(myNewStructure)>
<cfdump var=#CopiedStruct#><br>
<br>
A Duplicated structure<br>
<cfset dupStruct=Duplicate(myNewStructure)>
<cfdump var=#dupStruct#><br>
<br>
A new reference to a structure<br>
<cfset structRef=myNewStructure>
<cfdump var=#structRef#><br>


<br>
Change a string, array element, and structure value in the StructCopy copy.<br>
<br>
<cfset CopiedStruct.key1="1A">
<cfset CopiedStruct.key3[2]="4A">
<cfset CopiedStruct.key4.Struct2key2="6A">
Original structure<br>
<cfdump var=#myNewStructure#><br>
Copied structure<br>
<cfdump var=#CopiedStruct#><br>
Duplicated structure<br>
<cfdump var=#DupStruct#><br>
Structure reference
<cfdump var=#structRef#><br>
<br>
Change a string, array element, and structure value in the Duplicate.<br>
<br>
<cfset DupStruct.key1="1B">
<cfset DupStruct.key3[2]="4B">
<cfset DupStruct.key4.Struct2key2="6B">
Original structure<br>
<cfdump var=#myNewStructure#><br>
Copied structure<br>
<cfdump var=#CopiedStruct#><br>
Duplicated structure<br>
<cfdump var=#DupStruct#><br>
Structure reference
<cfdump var=#structRef#><br>
<br>
Change a string, array element, and structure value in the reference.<br>
<br>



                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    101
The CFML Programming Language




<cfset structRef.key1="1C">
<cfset structRef.key3[2]="4C">
<cfset structRef.key4.Struct2key2="6C">
Original structure<br>
<cfdump var=#myNewStructure#><br>
Copied structure<br>
<cfdump var=#CopiedStruct#><br>
Duplicated structure<br>
<cfdump var=#DupStruct#><br>
Structure reference
<cfdump var=#structRef#><br>
<br>
Clear the original structure<br>
<cfset foo=structclear(myNewStructure)>
Original structure:<br>
<cfdump var=#myNewStructure#><br>
Copied structure<br>
<cfdump var=#CopiedStruct#><br>
Duplicated structure<br>
<cfdump var=#DupStruct#><br>
Structure reference:<br>
<cfdump var=#structRef#><br>


Deleting structure elements and structures
To delete a key and its value from a structure, use the StructDelete function, as follows:

 StructDelete(structure_name, key [, indicateNotExisting ])

The indicateNotExisting argument tells the function what to do if the specified key does not exist. By default, the
function always returns True. However, if you specify True for the indicateNotExisting argument, the function returns
True if the key exists and False if it does not.

You can also use the StructClear function to delete all the data in a structure but keep the structure instance itself,
as follows:

 StructClear(structure_name)

If you use StructClear to delete a structure that you have copied using the StructCopy function, the specified
structure is deleted, but the copy is unaffected.

If you use StructClear to delete a structure that has multiple references, the function deletes the contents of the
structure and all references point to the empty structure, as the following example shows:

 <cfset myStruct.Key1="Adobe">
Structure before StructClear<br>
<cfdump var="#myStruct#">
<cfset myCopy=myStruct>
<cfset StructClear(myCopy)>
After Clear:<br>
myStruct: <cfdump var="#myStruct#"><br>
myCopy: <cfdump var="#myCopy#">


Looping through structures
You can loop through a structure to output its contents, as the following example shows:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 102
The CFML Programming Language




 <!--- Create a structure and set its contents. --->
<cfset departments=structnew()>


<cfset val=StructInsert(departments, "John", "Sales")>
<cfset val=StructInsert(departments, "Tom", "Finance")>
<cfset val=StructInsert(departments, "Mike", "Education")>


<!--- Build a table to display the contents --->
<cfoutput>
<table cellpadding="2" cellspacing="2">
     <tr>
           <td><b>Employee</b></td>
           <td><b>Department</b></td>
     </tr>
     <!--- Use cfloop to loop through the departments structure.
     The item attribute specifies a name for the structure key. --->
     <cfloop collection=#departments# item="person">
           <tr>
               <td>#person#</td>
               <td>#Departments[person]#</td>
           </tr>
     </cfloop>
</table>
</cfoutput>



Structure examples
Structures are useful for grouping a set of variables under a single name. The following example uses structures to
collect information from a form, and to submit that information to a custom tag, named cf_addemployee. For
information on creating and using custom tags, see "Creating and Using Custom CFML Tags" on page 208.


Example file newemployee.cfm
The following ColdFusion page shows how to create structures and use them to add data to a database. It calls the
cf_addemployee custom tag, which is defined in the addemployee.cfm file.

 <html>
<head>
<title>Add New Employees</title>
</head>


<body>
<h1>Add New Employees</h1>
<!--- Action page code for the form at the bottom of this page. --->


<!--- Establish parameters for first time through --->
<cfparam name="Form.firstname" default="">
<cfparam name="Form.lastname" default="">
<cfparam name="Form.email" default="">
<cfparam name="Form.phone" default="">
<cfparam name="Form.department" default="">


<!--- If at least the firstname form field is passed, create
            a structure named employee and add values. --->
<cfif #Form.firstname# eq "">
     <p>Please fill out the form.</p>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                    103
The CFML Programming Language




<cfelse>
<cfoutput>
      <cfscript>
         employee=StructNew();
         employee.firstname = Form.firstname;
         employee.lastname = Form.lastname;
         employee.email = Form.email;
         employee.phone = Form.phone;
         employee.department = Form.department;
      </cfscript>


<!--- Display results of creating the structure. --->
      First name is #StructFind(employee, "firstname")#<br>
      Last name is #StructFind(employee, "lastname")#<br>
      EMail is #StructFind(employee, "email")#<br>
      Phone is #StructFind(employee, "phone")#<br>
      Department is #StructFind(employee, "department")#<br>
     </cfoutput>


<!--- Call the custom tag that adds employees. --->
     <cf_addemployee empinfo="#employee#">
</cfif>


<!--- The form for adding the new employee information --->
<hr>
<form action="newemployee.cfm" method="Post">
First Name:&nbsp;
<input name="firstname" type="text" hspace="30" maxlength="30"><br>
Last Name:&nbsp;
<input name="lastname" type="text" hspace="30" maxlength="30"><br>
EMail:&nbsp;
<input name="email" type="text" hspace="30" maxlength="30"><br>
Phone:&nbsp;
<input name="phone" type="text" hspace="20" maxlength="20"><br>
Department:&nbsp;
<input name="department" type="text" hspace="30" maxlength="30"><br>


<input type="Submit" value="OK">
</form>
<br>
</body>
</html>


Reviewing the code
The following table describes the code:




                                         

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                104
The CFML Programming Language




Code                                                            Description

<cfparam name="Form.firstname" default="">                      Set default values of all form fields so that they exist the first
<cfparam name="Form.lastname" default="">                       time this page is displayed and can be tested.
<cfparam name="Form.email" default="">
<cfparam name="Form.phone" default="">
<cfparam name="Form.department" default="">

<cfif #Form.firstname# eq "">                                   Test the value of the firstname field. This field is required. The
<p>Please fill out the form.</p>                                test is False the first time the page displays.

                                                                If no data exists in the Form.firstname variable, display a
                                                                message requesting the user to fill the form.

<cfelse>                                                        If Form.firstname contains text, the user submitted the form.
<cfoutput>
 <cfscript>                                                     Use CFScript to create a structure named employee and fill it
employee=StructNew();                                           with the form field data.
employee.firstname = Form.firstname;
employee.lastname = Form.lastname;                              Then display the contents of the structure.
employee.email = Form.email;
employee.phone = Form.phone;
employee.department = Form.department;
 </cfscript>

<!--- Display results of creating the structure. --->
 First name is #StructFind(employee, "firstname")#<br>
 Last name is #StructFind(employee, "lastname")#<br>
 EMail is #StructFind(employee, "email")#<br>
 Phone is #StructFind(employee, "phone")#<br>
 Department is #StructFind(employee, "department")#<br>
</cfoutput>

<cf_addemployee empinfo="#employee#">                           Call the cf_addemployee custom tag and pass it a copy of the
</cfif>                                                         employee structure in the empinfo attribute.

                                                                The duplicate function ensures that the custom tag gets a
                                                                copy of the employee structure, not the original. Although this
                                                                is not necessary in this example, it is good practice because it
                                                                prevents the custom tag from modifying the calling contents of
                                                                the structure in the calling page.

<form action="newemployee.cfm" method="Post">                   The data form. When the user clicks OK, the form posts the data
First Name:&nbsp;                                               to this ColdFusion page.
<input name="firstname" type="text" hspace="30"
maxlength="30"><br>
Last Name:&nbsp;
<input name="lastname" type="text" hspace="30"
maxlength="30"><br>
EMail:&nbsp;
<input name="email" type="text" hspace="30"
maxlength="30"><br>
Phone:&nbsp;
<input name="phone" type="text" hspace="20"
maxlength="20"><br>
Department:&nbsp;
<input name="department" type="text" hspace="30"
maxlength="30"><br>

<input type="Submit" value="OK">
</form>



Example file addemployee.cfm
The following file is an example of a custom tag used to add employees. Employee information is passed through the
employee structure (the empinfo attribute). For databases that do not support automatic key generation, also add the
Emp_ID.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                              105
The CFML Programming Language




 <cfif StructIsEmpty(attributes.empinfo)>
     <cfoutput>
          Error. No employee data was passed.<br>
     </cfoutput>
     <cfexit method="ExitTag">
<cfelse>
     <!--- Add the employee --->
     <cfquery name="AddEmployee" datasource="cfdocexamples">
          INSERT INTO Employees
               (FirstName, LastName, Email, Phone, Department)
          VALUES (
          '#attributes.empinfo.firstname#' ,
          '#attributes.empinfo.lastname#' ,
          '#attributes.empinfo.email#' ,
          '#attributes.empinfo.phone#' ,
          '#attributes.empinfo.department#' )
     </cfquery>
</cfif>
<cfoutput>
     <hr>Employee Add Complete
</cfoutput>


Reviewing the code
The following table describes the code:


 Code                                                             Description

 <cfif StructIsEmpty(attributes.empinfo)>                         If the custom tag was called without an empinfo attribute,
 <cfoutput>                                                       displays an error message and exit the tag.
 Error. No employee data was passed.<br>
 </cfoutput>
 <cfexit method="ExitTag">

 <cfelse>                                                         Add the employee data passed in the empinfo structure to the
 <!--- Add the employee --->                                      Employees table of the cfdocexamples database.
 <cfquery name="AddEmployee" datasource="cfdocexamples">
 INSERT INTO Employees                                            Use direct references to the structure entries, not StructFind
 (FirstName, LastName, Email, Phone, Department)                  functions.
 VALUES (
 '#attributes.empinfo.firstname#' ,                               If the database does not support automatic generation of the
 '#attributes.empinfo.lastname#' ,
                                                                  Emp_ID key, add an Emp_ID entry to the form and add it to the
 '#attributes.empinfo.email#' ,
                                                                  query.
 '#attributes.empinfo.phone#' ,
 '#attributes.empinfo.department#' )
 </cfquery>
 </cfif>

 <cfoutput>                                                       Display a completion message. This code does not have to be
 <hr>Employee Add Complete                                        inside the cfelse block because the cfexit tag prevents it from
 </cfoutput>
                                                                  being run if the empinfo structure is empty.



Structure functions
You can use the following functions to create and manage structures in ColdFusion applications. The table describes
the purpose of each function and provides specific, but limited, information that can assist you in determining whether
to use the function instead of other technique.

All functions except StructDelete throw an exception if a referenced key or structure does not exist.

For more information on these functions, see the CFML Reference.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                          106
The CFML Programming Language




Function                       Description

Duplicate                      Returns a complete copy of the structure.

IsStruct                       Returns True if the specified variable is a ColdFusion structure or a Java object that implements the
                               java.util.Map interface.

StructAppend                   Appends one structure to another.

StructClear                    Removes all data from the specified structure.

StructCopy                     Returns a "shallow" copy of the structure. All embedded objects are references to the objects in the original
                               structure. The Duplicate function has replaced this function for most purposes.

StructCount                    Returns the number of keys in the specified structure.

StructDelete                   Removes the specified item from the specified structure.

StructFind                     Returns the value associated with the specified key in the specified structure. This function is redundant
                               with accessing structure elements using associative array notation.

StructFindKey                  Searches through a structure for the specified key name and returns an array containing data on the found
                               key or keys.

StructFindValue                Searches through a structure for the specified simple data value (for example, a string or number) and
                               returns an array containing information on the value location in the structure.

StructGet                      Returns a reference to a substructure contained in a structure at the specified path. This function is
                               redundant with using direct reference to a structure. If you accidentally use this function on a variable that
                               is not a structure, it replaces the value with an empty structure.

StructInsert                   Inserts the specified key-value pair into the specified structure. Unlike a direct assignment statement, this
                               function generates an error by default if the specified key exists in the structure.

StructIsEmpty                  Indicates whether the specified structure contains data. Returns True if the structure contains no data, and
                               False if it does contain data.

StructKeyArray                 Returns an array of keys in the specified structure.

StructKeyExists                Returns True if the specified key is in the specified structure. You can use this function in place of the
                               IsDefined function to check for the existence of variables in scopes that are available as structures.


StructKeyList                  Returns a list of keys in the specified structure.

StructNew                      Returns a new structure.

StructSort                     Returns an array containing the key names of a structure in the order determined by the sort criteria.

StructUpdate                   Updates the specified key with the specified value. Unlike a direct assignment statement, this function
                               generates an error if the structure or key does not exist.




Extending ColdFusion Pages with CFML Scripting

Adobe ColdFusion offers a server-side scripting language, CFScript, that provides ColdFusion functionality in script
syntax. This JavaScript-like language gives developers the same control flow as ColdFusion, but without tags. You can
also use CFScript to write user-defined functions that you can use anywhere that a ColdFusion expression is allowed.


About CFScript
CFScript is a language within a language. It is a scripting language that is like JavaScript but is simpler to use. Also,
unlike JavaScript, CFScript only runs on the ColdFusion server; it does not run on the client system. CFScript code can
use all the ColdFusion functions and expressions, and has access to all ColdFusion variables that are available its scope.



                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 107
The CFML Programming Language




CFScript provides a compact and efficient way to write ColdFusion logic. Typical uses of CFScript include the
following:

* Simplifying and speeding variable setting

* Building compact JavaScript-like flow control structures

* Creating user-defined functions

Because you use functions and expressions directly in CFScript, you do not have to surround each assignment or
function in a cfset tag. Also, CFScript assignments are often faster than cfset tags.

CFScript provides a set of decision and flow-control structures that are more familiar than ColdFusion tags to most
programmers.

In addition to variable setting, other operations tend to be slightly faster in CFScript than in tags.

You can use CFScript to create user-defined functions, or UDFs (also known as custom functions). You call UDFs in
the same manner that you call standard ColdFusion functions. UDFs are to ColdFusion built-in functions what
custom tags are to ColdFusion built-in tags. Typical uses of UDFs include data manipulation and mathematical
calculation routines.

You cannot include ColdFusion tags in CFScript. However, some functions and CFScript statements are equivalent to
commonly used tags. For more information, see "Tag equivalents in CFScript" on page 109.


Comparing tags and CFScript
The following examples show how you can use CFML tags and CFScript to do the same thing. Each example takes data
submitted from a form and places it in a structure; if the form does not have a last name and department field, it
displays a message.


Using CFML tags
<cfif IsDefined("Form.submit")>
     <cfif (Form.lastname NEQ "") AND (Form.department NEQ "")>
          <cfset employee=structnew()>
          <cfset employee.firstname=Form.firstname>
          <cfset employee.lastname=Form.lastname>
          <cfset employee.email=Form.email>
          <cfset employee.phone=Form.phone>
          <cfset employee.department=Form.department>
          <cfoutput>
               Adding #Form.firstname# #Form.lastname#<br>
          </cfoutput>
     <cfelse>
          <cfoutput>
               You must enter a Last Name and Department.<br>
          </cfoutput>
          </cfif>
</cfif>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 108
The CFML Programming Language




Using CFScript
<cfscript>
     if (IsDefined("Form.submit")) {
           if ((Form.lastname NEQ "") AND (Form.department NEQ "")) {
               employee=StructNew();
               employee.firstname=Form.firstname;
               employee.lastname=Form.lastname;
               employee.email=Form.email;
               employee.phone=Form.phone;
               employee.department=Form.department;
               WriteOutput("Adding #Form.firstname# #Form.lastname# <br>");
               }
           else
               WriteOutput("You must enter a Last Name and Department.<br>");
           }
</cfscript>



Language Enhancements in ColdFusion 9
The language enhancements in ColdFusion 9 include new language constructs, extended tag support, new keywords,
script functions implemented as CFCs, and support for new operations.


New tag equivalents in CFScript
The following table lists the tags that have equivalents in CFScript:


Tag                                                     Equivalent in CFScript

cfabort                                                 abort

cfcomponent                                             component

cfcontinue                                              continue

cfdirectory                                             The directory functions DirectoryCreate, DirectoryDelete,
                                                        DirectoryList, and DirectoryRename.
Only for <Cfdirectory action=list/>

cfdump                                                  writedump

cfexit                                                  exit

cffinally                                               finally

cfimport                                                import

cfinclude                                               include

cfinterface                                             interface

cflocation                                              location

cflog                                                   writelog

cfparam                                                 param

cfprocessingdirective                                   pageencoding

cfproperty                                              property

cfrethrow                                               rethrow

cfthread                                                thread




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           109
The CFML Programming Language




Tag                                                       Equivalent in CFScript

cfthrow                                                   throw

cftrace                                                   trace

cftransaction                                             transaction



Script Functions added in ColdFusion 9
The following table has the list of script functions introduced in ColdFusion 9.


Function                                                  Equivalent ColdFusion Tag

ftp                                                       cfftp

http                                                      cfhttp

mail                                                      cfmail

pdf                                                       cfpdf

query                                                     cfquery

storedproc                                                cfstoredproc


For details of the Script Functions, see the section Script Functions Implemented as CFCs in the CFML Reference.


Reserved words introduced in ColdFusion 9
* import

* finally

* local (inside function declaration)

* interface

* pageencoding


What is supported in CFScript

Tag equivalents in CFScript

Tag                                          CFScript equivalent

cfabort                                      abort

cfbreak                                      break. CFScript also has a continue statement that has no equivalent CFML tag.

cfcase                                       case

cfcatch                                      catch

cfcomponent                                  component

cfcontinue                                   continue

cfcookie                                     Direct assignment of Cookie scope memory-only variables. You cannot use direct
                                             assignment to set persistent cookies that are stored on the user system.

cfdefaultcase                                default




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                              110
The CFML Programming Language




Tag                                   CFScript equivalent

cfdirectory                           The directory functions DirectoryCreate, DirectoryDelete, DirectoryList, and
                                     DirectoryRename.
Only for <Cfdirectory action=list/>

cfdump                                writedump

cfelse                                else

cfelseif                              elseif

cfexit                                exit

cffile                                The file functions FileDelete, FileSeek, FileSkipBytes, and FileWriteLine.

cffinally                             finally

cffunction                            function

cfimage                               The Image functions.

cfif                                  if

cfimport                              import

                                      Import in cfscript is only equivalent of <cfimport path="">. You cannot use
                                     <cfimport taglib=""> in cfscript.


cfinclude                             include

cfinterface                           interface

cflocation                            location

cflock                                lock

cflog                                 writelog

cfloop                               * Indexed cfloop: for loops

                                     * Conditional cfloop: while loops and do while loops

                                     * Structure cfloop: for in loop. (There is no equivalent for queries, lists, or objects.)

cfobject                              createobject, new

cfoutput                              writeoutput

cfpdfparam                            param

cfprocessingdirective                 pageencoding

cfproperty                            property

cfrethrow                             rethrow

cfreturn                              return

cfsavecontent                         savecontent

cfset                                 var

                                     var x =1; is equivalent of <cfset var x =1>

                                      Assignment statement x =1; is equivalent of <cfset x =1>

                                      local.x=1; is equivalent of <cfset var x =1>

cfswitch                              switch




                                         

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     111
The CFML Programming Language




Tag                                          CFScript equivalent

cfthread                                     thread

cfthrow                                      throw

cftrace                                      trace

cftransaction                                transaction

cftry                                        try



Example
The following example loops through a query in CFScript:

...
<cfscript>
// Loop through the qGetEmails RecordSet
for (x = 1; x <= qGetEmails.RecordCount; x=x+1) {
      This_id = qGetEmails.Emails_id[x];
      This_Subject = qGetEmails.Subject[x];
      This_RecFrom = qGetEmails.RecFrom[x];
      This_SentTo = qGetEmails.SentTo[x];
      This_dReceived = qGetEmails.dReceived[x];
        This_Body = qGetEmails.Body[x];
... // More code goes here.
}
</cfscript>


Reserved words
In addition to the names of ColdFusion functions and words reserved by ColdFusion expressions (such as NOT, AND,
IS, and so on), the following words are reserved in CFScript. Do not use these words as variables or identifiers in your
scripting code:


break                            do                            import                    var

case                             else                          in                        while

catch                            finally                       interface

try                              for                           pageencoding

continue                         function                      return

default                          if                            switch



Script functions
For a list of script functions, see "Script Functions added in ColdFusion 9" on page 109.


The CFScript language
The CFScript language syntax is similar to other scripting languages, and has the same types of elements.


Identifying CFScript
You enclose CFScript regions inside <cfscript> and </cfscript> tags. No other CFML tags are allowed inside a
cfscript region. The following lines show a minimal script:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       112
The CFML Programming Language




<cfscript>
a = 2;
</cfscript>


Variables
CFScript variables can be of any ColdFusion type, such as numbers, strings, arrays, queries, and objects. The CFScript
code can read and write any variables that are available in the page that contains the script. These variables include all
shared scopes, such as session, application, and server variables.


Expressions and operators
CFScript supports all CFML expressions. CFML expressions include operators (such as +, -, EQ, and so on), as well as
all CFML functions.

You can use several comparison operators in CFScript only, not in CFML tags. (You can also use the corresponding
CFML operators in CFScript.) The following table lists the CFScript-only operators and the equivalent operator that
you can use in CFML tags or CFScript:


CFScript operator             CFML operator                  CFScript operator               CFML operator

==                            EQ                             !=                              NEQ

<                             LT                             <=                              LTE

>                             GT                             >=                              GTE


For information about CFML expressions, operators, and functions, see "Using Expressions and Number Signs" on
page 64.


Statements
CFScript supports the following statements:


assignment                              for-in                                  try-catch

function call                           while                                   function (function definition)

if-else                                 do-while                                var (in custom functions only)

switch-case-default                     break                                   return (in custom functions only)

for                                     continue


The following rules apply to statements:

* You must put a semicolon at the end of a statement.

* Line breaks are ignored. A single statement can cross multiple lines.

* White space is ignored. For example, it does not matter whether you precede a semicolon with a space character.

* Use curly brackets to group multiple statements into one logical statement unit.

* Unless otherwise indicated, you can use any ColdFusion expression in the body of a statement.

Note: For information on the function, var, and return statements, see "Defining functions in CFScript" on page 154.


Statement blocks
Curly bracket characters ({ and }) group multiple CFScript statements so that they are treated as a single unit or
statement. This syntax enables you to create code blocks in conditional statements, such as the following:


                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    113
The CFML Programming Language




if(score GT 0)
{
     result = "positive";
     Positives = Positives + 1;
}

In this example, both assignment statements are executed if the score is greater than 0. If they were not in the code
block, only the first line would execute.

You do not have to place curly bracket characters on their own lines in the code. For example, you could place the open
curly bracket in the preceding example on the same line as the if statement, and some programmers use this style.
However, putting at least the ending brace on its own line makes it easier to read the code and separate code blocks.


Comments
CFScript has two forms of comments: single line and multiline.

A single-line comment begins with two forward slashes (//) and ends at the line end; for example:

//This is a single-line comment.
//This is a second single-line comment.

A multiline comment starts with a /* marker and continues until it reaches a */ marker; for example:

/*This is a multiline comment.
      You do not need to start each line with a comment indicator.
      This line is the last line in the comment. */

The following rules apply to comments:

* Comments do not have to start at the beginning of a line. They can follow active code on a line. For example, the
   following line is valid:

    MyVariable = 12; // Set MyVariable to the default value.

* The end of a multiline comment can be followed on the same line by active code. For example, the following line is
   valid, although it is poor coding practice:

    End of my long comment */ foo = "bar";

* You can use multiline format for a comment on a single line, for example:

    /*This is a single-line comment using multiline format. */

* You cannot nest /* and */ markers inside other comment lines.

* CFML comments (<!--- and --->) do not work in CFScript.


Differences from JavaScript
Although CFScript and JavaScript are similar, they have several key differences. The following list identifies CFScript
features that differ from JavaScript:

* CFScript uses ColdFusion expressions, which are not a superset or a subset of JavaScript expressions. In particular,
   ColdFusion expressions do not support bitwise operators, and the ColdFusion MOD or % operator operates
   differently from the corresponding JavaScript % operator: In ColdFusion, the operator does integer arithmetic and
   ignores fractional parts. ColdFusion expressions also support the EQV, IMP, CONTAINS, and DOES NOT
   CONTAIN operators that are not supported in JavaScript.

* Variable declarations (var keyword) are only used in user-defined functions and threads.

* CFScript is not case sensitive.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    114
The CFML Programming Language




* All statements end with a semicolon, and line breaks in the code are ignored.

* Assignments are statements, not expressions, and therefore cannot be used in situations that require evaluating the
   assignment operation.

* JavaScript objects, such as Window and Document, are not available.

* Only the ColdFusion server processes CFScript. There is no client-side CFScript.


CFScript limitation
You cannot include ColdFusion tags in CFScript. However, you can include cfscript blocks inside other ColdFusion
tags, such as cfoutput.


Using CFScript statements
CFScript includes the following types of statements:

* Assignment statements and functions

* Conditional processing statements

* Looping statements


Using assignment statements and functions
CFScript assignment statements are the equivalent of the cfset tag. These statements have the following form:

lval = expression;

eval is any ColdFusion variable reference; for example:

x = "positive";
y = x;
a[3]=5;
structure.member=10;
ArrayCopy=myArray;

You can use ColdFusion function calls, including UDFs, directly in CFScript. For example, the following line is a valid
CFScript statement:

StructInsert(employee,"lastname",FORM.lastname);


Using conditional processing statements
CFScript includes the following conditional processing statements:

* if and else statements, which serve the same purpose as the cfif, cfelseif, and cfelse tags

* switch, case, and default statements, which are the equivalents of the cfswitch, cfcase, and cfdefaultcase tags


Using if and else statements
The if and else statements have the following syntax:

if(expr) statement [else statement]

In its simplest form, an if statement looks as follows:

if(value EQ 2700)
     message = "You've reached the maximum";

A simple if-else statement looks like the following:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         115
The CFML Programming Language




if(score GT 1)
     result = "positive";
else
     result = "negative";

CFScript does not include an elseif statement. However, you can use an if statement immediately after an else
statement to create the equivalent of a cfelseif tag, as the following example shows:

if(score GT 1)
     result = "positive";
else if(score EQ 0)
     result = "zero";
else
     result = "negative";

As with all conditional processing statements, you can use curly brackets to enclose multiple statements for each
condition, as follows:

if(score GT 1) {
     result = "positive";
     message = "The result was positive.";
     }
else {
     result = "negative";
     message = "The result was negative.";
     }

Note: Often, you can make your code clearer by using braces even where they are not required.


Using switch and case statements
The switch statement and its dependent case and default statements have the following syntax:

switch (expression) {
          case constant: [case constant:]... statement(s) break;
          [case constant: [case constant:]... statement(s) break;]...
          [default: statement(s)] }

Use the following rules and recommendations for switch statements:

* You cannot mix Boolean and numeric constant values in a switch statement.

* Each constant value must be a constant (that is, not a variable, a function, or other expression).

* Multiple caseconstant: statements can precede the statement or statements to execute if any of the cases are true.
   This lets you specify several matches for one code block.

* No two constant values can be the same.

* The statements following the colon in a case statement block do not have to be in curly brackets. If a constant value
   equals the switch expression, ColdFusion executes all statements through the break statement.

* The break statement at the end of the case statement tells ColdFusion to exit the switch statement. ColdFusion
   does not generate an error message if you omit a break statement. However, if you omit it, ColdFusion executes all
   the statements in the following case statement, even if that case is false. In nearly all circumstances, this is not what
   you want to do.

* You can have only one default statement in a switch statement block. ColdFusion executes the statements in the
   default block if none of the case statement constants equals the expression value.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  116
The CFML Programming Language




* The default statement does not have to follow all switch statements, but it is good programming practice to do
   so. If any switch statements follow the default statement you must end the default block code with a break
   statement.

* The default statement is not required. However, use one if the case constants do not include all possible values
   of the expression.

* The default statement does not have to follow all the case statements; however, it is good programming practice
   to place it there.

The following switch statement takes the value of a name variable:

1 If the name is John or Robert, it sets both the male variable and the found variable to True.

2 If the name is Mary, it sets the male variable to False and the found variable to True.

3 Otherwise, it sets the found variable to False.

switch(name) {
      case "John": case "Robert":
           male=True;
           found=True;
           break;
      case "Mary":
           male=False;
           found=True;
           break;
      default:
           found=False;
} //end switch


Using looping statements
CFScript provides a richer selection of looping constructs than those supplied by CFML tags. It enables you to create
efficient looping constructs like those in most programming and scripting languages. CFScript provides the following
looping constructs:

* For

* While

* Do-while

* For-in

CFScript also includes the continue and break statements that control loop processing.


Using for loops
The for loop has the following format:

for (initial-expression; test-expression; final-expression) statement

The initial-expression and final-expression can be one of the following:

* A single assignment expression; for example, x=5 or loop=loop+1

* Any ColdFusion expression; for example, SetVariable("a",a+1)

* Empty

The test-expression can be one of the following:

* Any ColdFusion expression; for example:



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           117
The CFML Programming Language




    A LT 5
   index LE x
   status EQ "not found" AND index LT end

* Empty

Note: The test expression is re-evaluated before each repeat of the loop. If code inside the loop changes any part of the test
expression, it can affect the number of iterations in the loop.

The statement can be a single semicolon terminated statement or a statement block in curly brackets.

When ColdFusion executes a for loop, it does the following:

1 Evaluates the initial expression.

2 Evaluates the test-expression.

3 If the test-expression is False, exits the loop and processing continues following the statement.

   If the test-expression is True:

   a Executes the statement (or statement block).

   b Evaluates the final-expression.

   c Returns to Step 2.

For loops are most commonly used for processing in which an index variable is incremented each time through the
loop, but it is not limited to this use.

The following simple for loop sets each element in a 10-element array with its index number.

for(index=1;
     index LTE 10;
     index = index + 1)
     a[index]=index;

The following, more complex, example demonstrates two features:

* The use of curly brackets to group multiple statements into a single block.

* An empty condition statement. All loop control logic is in the statement block.

<cfscript>
strings=ArrayNew(1);
ArraySet(strings, 1, 10, "lock");
strings[5]="key";
indx=0;
for( ; ; ) {
     indx=indx+1;
     if(Find("key",strings[indx],1)) {
           WriteOutput("Found key at " & indx & ".<br>");
           break;
           }
     else if (indx IS ArrayLen(strings)) {
           WriteOutput("Exited at " & indx & ".<br>");
           break;
           }
}
</cfscript>

This example shows one important issue that you must remember when creating loops: always ensure that the loop
ends. If this example lacked the elseif statement, and there was no "key" in the array, ColdFusion would loop forever
or until a system error occurred; you would have to stop the server to end the loop.


                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      118
The CFML Programming Language




The example also shows two issues with index arithmetic: in this form of loop you must make sure to initialize the
index, and keep track of where the index is incremented. In this case, because the index is incremented at the top of
the loop, initialize it to 0 so it becomes 1 in the first loop.


Using while loops
The while loop has the following format:

while (expression) statement

The while statement does the following:

1 Evaluates the expression.

2 If the expression is True, it does the following:

    a Executes the statement, which can be a single semicolon-terminated statement or a statement block in curly
        brackets.

    b Returns to step 1.

    If the expression is False, processing continues with the next statement.

    The following example uses a while loop to populate a 10-element array with multiples of five.

    a = ArrayNew(1);
    loop = 1;
    while (loop LE 10) {
         a[loop] = loop * 5;
         loop = loop + 1;
         }

As with other loops, make sure that at some point the whileexpression is False and be careful to check your index
arithmetic.


Using do-while loops
The do-while loop is like a while loop, except that it tests the loop condition after executing the loop statement block.
The do-while loop has the following format:

do statement while (expression);

The dowhile statement does the following:

1 Executes the statement, which can be a single semicolon-terminated statement or a statement block in curly
    brackets.

2 Evaluates the expression.

3 If the expression is true, it returns to step 1.

If the expression is False, processing continues with the next statement.

The following example, like the while loop example, populates a 10-element array with multiples of 5:

a = ArrayNew(1);
loop = 1;
do {
a[loop] = loop * 5;
loop = loop + 1;
}
while (loop LE 10);




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                              119
The CFML Programming Language




Because the loop index increment follows the array value assignment, the example initializes the loop variable to 1 and
tests to make sure that it is less than or equal to 10.

The following example generates the same results as the previous two examples, but it increments the index before
assigning the array value. As a result, it initializes the index to 0, and the end condition tests that the index is less than 10.

a = ArrayNew(1);
loop = 0;
do {
      loop = loop + 1;
      a[loop] = loop * 5;
}
while (loop LT 10);

The following example loops through a query:

<cfquery ... name="myQuery">
... sql goes here...
</cfquery>
<cfscript>
if (myQuery.RecordCount gt 0) {
       currRow=1;
       do {
         theValue=myQuery.myField[CurrRow];
         currRow=currRow+1;
       } while (currRow LTE myQuery.RecordCount);
}
</cfscript>


Using for-in loops
The for-in loop loops over the elements in a ColdFusion structure. It has the following format:

for (variable in structure) statement

The variable can be any ColdFusion identifier; it holds each structure key name as ColdFusion loops through the
structure. The structure must be the name of an existing ColdFusion structure. The statement can be a single semicolon
terminated statement or a statement block in reference.

The following example creates a structure with three elements. It then loops through the structure and displays the
name and value of each key. Although the curly brackets are not required here, they make it easier to determine the
contents of the relatively long WriteOutput function. In general, you can make structured control flow, especially
loops, clearer by using curly brackets.

myStruct=StructNew();
myStruct.productName="kumquat";
mystruct.quality="fine";
myStruct.quantity=25;
for (keyName in myStruct) {
      WriteOutput("myStruct." & Keyname & " has the value: " &
               myStruct[keyName] &"<br>");
}

Note: Unlike the cfloop tag, CFScript for-in loops do not provide built-in support for looping over queries and lists.


Using continue and break statements
The continue and break statements enable you to control the processing inside loops:

* The continue statement tells ColdFusion to skip to the beginning of the next loop iteration.



                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  120
The CFML Programming Language




* The break statement exits the current loop or case statement.


Using continue
The continue statement ends the current loop iteration, skips any code following it in the loop, and jumps to the
beginning of the next loop iteration. For example, the following code loops through an array and display's each value
that is not an empty string:

for ( loop=1; loop LE 10; loop = loop+1) {
     if(a[loop] EQ "") continue;
     WriteOutput(loop);
}

(To test this code snippet, you must first create an array, a, with 10 or more elements, some of which are not empty
strings.)

The continue statement is useful if you loop over arrays or structures and you want to skip processing for array
elements or structure members with specific values, such as the empty string.


Using break
The break statement exits the current loop or case statement. Processing continues at the next CFScript statement.
You end case statement processing blocks with a break statement. You can also use a test case with a break statement
to prevent infinite loops, as shown in the following example. This script loops through an array and prints the array
indexes that contain the value key. It uses a conditional test and a break statement to make sure that the loop ends
when at the end of the array.

strings=ArrayNew(1);
ArraySet(strings, 1, 10, "lock");
strings[5]="key";
strings[9]="key";
indx=0;
for( ; ; ) {
     indx=indx+1;
     if(Find("key",strings[indx],1)) {
          WriteOutput("Found a key at " & indx & ".<br>");
          }
     else if (indx IS ArrayLen(strings)) {
          WriteOutput("Array ends at index " & indx & ".<br>");
          break;
     }
}



for-in construct (for arrays)
Note: This feature applies only if you have installed ColdFusion 9 Update 1.

You can loop over arrays in CFScript using for-in construct.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   121
The CFML Programming Language




Example
public String foo(array a)
{
     for(var item in a)
     {
       writedump(item);
     }
}



var declaration within for loop
Note: This feature applies only if you have installed ColdFusion 9 Update 1.

You can use var inline with for-in construct to bind variable to the local scope for both structs and arrays.


Example
public String foo(struct s)
{
     for(var item in s)
     {
       writedump(item & ": " & s[item]);
     }
     writedump(local);
}

For arrays example, see "for-in construct (for arrays)" on page 120.


Defining components and functions in CFScript
ColdFusion supports the syntax for defining CFCs, including interfaces, functions, properties, and parameters entirely
in CFScript. Currently, however, only certain ColdFusion tags are supported as CFScript functions. This section
describes the component definition syntax.

For information on tags as functions see "Tag equivalents in CFScript" on page 109.


Basic Syntax
Syntax for defining a component is as follows:

/**
* ColdFusion treats plain comment text as a hint.
* You can also use the @hint metadata name for hints.
* Set metadata, including, optionally, attributes, (including custom
* attributes) in the last entries in the comment block, as follows:
*@metadataName metadataValue
...
*/
component attributeName="attributeValue" ... {
body contents
}

The following example shows a simple component definition




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   122
The CFML Programming Language




/**
* Simple Component.
*/
component {
/**
* Simple function.
*/
public void function foo() {
WriteOutput("Method foo() called<br>");
}
}

When you define a component entirely in CFScript, you do not have to use a cfscript tag on the page. In this case,
the component keyword can be preceded only by comments (including metadata assignments) and import operators.
Adobe recommends this format as a best practice. You specify component properties as follows:

/**
/*@default defaultValue
* @attrib1Name attrib1Value
* ...
*/
property [type]propName;

If the type precedes the property name, you do not need to use the "type" keyword, only the name of the specific type.
In either format, you must set the name attribute value. All other property attributes, such as type, are optional. As
with cfproperty tags, place the property operators at the top of the component definition, immediately following the
opening brace.

The syntax to define a function is similar to the component definition:

/**
*Comment text, treated as a hint.
*Set metadata, including, optionally, attributes, in the last entries
*in the comment block, as follows:
*@metadataName metadataValue
...
*/
access returnType function functionName(arg1Type arg1Name="defaultValue1"
arg1Attribute="attributeValue...,arg2Type
arg2Name="defaultValue2" arg2Attribute="attributeValue...,...)
functionAttributeName="attributeValue" ... {
body contents
}

You specify all function arguments, including the argument type, default value, and attributes in the function
definition.

The following example shows a function definition:

/**
* @hint="This function displays its name and parameter."
*/
public void function foo(String n1=10)
description="does nothing" hint="overrides hint" (
WriteOutput("Method foo() called<br> Parameter value is " & n1);
}

Specifying the required keyword makes the argument mandatory. If the required keyword is not present then the
argument becomes optional.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    123
The CFML Programming Language




For example:

public function funcname(required string argument1)

Interface definitions follow the same pattern as components, with the same general rules and limitations that apply to
the interfaces you define using cfinterface tags. The following simple code defines an interface with a single function
that takes one string argument, with a default argument value of "Hello World!":

interface {
function method1(string arg1="Hello World!");
function method2 (string arg1="Goodbye World!");
...
}

The following example shows the definition of a simple component with a single function:

/**
* Component defined in CFScript
* @output true
*/
component extends="component_01" {
/**
* Function that displays its arguments and returns a string.
* @returnType string
*/
public function method_03(argOne,argTwo) {
WriteOutput("#arguments.argOne# ");
WriteOutput("#arguments.argTwo# ");
return("Arguments written.");
}
}


Setting attributes
The definition syntax provides two ways to set attributes:

* At the end of a comment that immediately precedes the element, in the following format

    /**
    *Comment
    *@attributeName1 attributeValue
    *@attributeName2 attributeValue
    *...
    */

* In the element declaration using standard attribute-value assignment notation, as in the following line:

    component extends="component_01"

Attribute values set in the element declaration take precedence over the values set in the comment section. Therefore,
if you set an attribute, such as a hint in both locations, ColdFusion ignores the value in the comment section and uses
only the one in the element declaration.


Specifying page encoding
You can specify the character encoding of a component by specifying a pageencoding processing directive at the top
of the component body. You can specify the pageencoding directive only for components. The following code snippet
shows how to use the directive:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                             124
The CFML Programming Language




// this is a component
/**
*@hint "this is a hint for component"
*/
component displayname="My Component" {
pageencoding "Cp1252" ;
//
// The rest of the component definition goes here.
//
}

Note: Currently, you cannot use CFScript to specify the suppresswhitespace processing directive.


Accessing component metadata
To access metadata of a component, function, parameter, or property, use the GetMetadata function. Component
metadata includes the metadata of its properties and functions, including attributes and function parameters.

For detailed information about the structure and contents of the metadata, see GetMetaData in the CFML Reference.

The following trivial code shows the use of component metadata:

//Create an instance of a component.
theComponent=createObject("Component" "myComponent");
// Get the component metadata.
theMetadata = getMetadata(theComponent);
// The component properties are in an array. Display the name
// of the first property in the array.
writeoutput("Property name: " & theMetadata.properties[1].name);


Support for creating custom metadata
Note: To use this feature, you must install ColdFusion 9 Update 1.

You can specify custom metadata for function arguments in script syntax in either of the following ways:

* With arguments, as space-separated list of key-value pairs.

* In annotations, using @arg1.custommetadata "custom value".


Example
custom.cfm

cfscript>
                     writeoutput(new custom().foo(10));
</cfscript>

custom.cfc




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     125
The CFML Programming Language




/**
 * custom metadata for a cfc defined using annotation as well as key-value pairs
 * @cfcMetadata1 "cfc metadata1"
 */
component cfcMetadata2 = "cfc metadata2"
{
                     /**
                     * custom metadata for a property defined using annotation as well as key-value
pairs
                       * @propMetadata1 "property metadata1"
                       */
                     property type="numeric" name="age" default="10" propMetadata2="property
metadata2";


                     /**
                 * custom metadata for a function/argument using both annotation and key-value pairs
                       * @arg1.argmdata1 "arg metadata1"
                       * output true
                       * @fnMetadata1 "function metadata1"
                       */
                    public string function foo(required numeric arg1=20 argmdata2="arg metadata2")
fnMetadata2="function metadata2"
                     {
                                         writedump(getmetadata(this));
                                         return arg1;
                     }
}


Explicit function-local scope
ColdFusion has an explicit function-local scope. Variables in this scope exist only during the execution of the function
and are available only to the function. To declare a function-local scope variable either specify the Local scope name
when assigning the variable, or use the var keyword. Also, you can now use the var keyword anywhere in a function
definition, not just at the top.

Note: Because it is now a scope name, do not use local as a variable or argument name. If you do so, ColdFusion ignores
the variable or argument.

The following code shows the use of the function local scope:

<cffunction name="foo" output="true">
<cfset var x = 5>
<cfset local.y=local.x*4>
<cfset var z=[local.x,local.y]>
<cfset local.u.v={z="2"}>
<cfset zz="in Variables Scope">
<cfdump var="#local#">
</cffunction>;

For more information about function local scope see "Using ColdFusion Variables" on page 38.


Using system level functions
In the <cfscript> mode, you can now use the following basic language constructs:

* throw

* writedump




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    126
The CFML Programming Language




* writelog

* location

* trace

You can call these functions by passing the argument as name="value" pairs or positional notations. For positional
notations, the sequence of arguments must be followed. The sequence of arguments for each construct is mentioned
in the CFML Reference Guide.

Example of passing arguments as name=value pair:

<cfscript>
     writedump(var=myquery, label="query", show="name", format = "text");
</cfscript>

Example of passing arguments as Positional arguments:

<cfscript
     writedump(myquery, false, html, name)
<cfscript>

You do not need to specify all the parameters while using positional arguments. For instance, if you want to specify
the first and third argument, you can add an empty value for the second argument. The exception to this usage is when
there is a boolean type for the second argument where you have to specify true or false.

Note: You cannot mix positional and named arguments while calling a function. For example, if you need to use only the
var and output attributes with the writedump construct, you can use writedump(myquery,html).



Import and new operations using cfimport
CFScript supports import and new operations. "New" is now a keyword. However, it is not a reserved word, so you can
use it as a variable name.

You can use the cfimport tag or import script operator to import a CFC. The import operation puts the contents of the
specified component in the current name space, and caches the resolved component path in memory. The import
action is effective on the current page only. If you import CFCs in Application.cfm, the CFC is not imported on other
pages of the application.

You refer to the imported component directly without using a dot-delimited pathname. Execution time for cached
components is faster than with CFCs that you do not import.

Note: The cfobject and cfobject tags and the CreateObject function also cache the resolved component path. They do not,
however, invoke an initializer function.

In script the import statement has the following syntax:

import "cfc_filepath"

Quotation marks are optional for most paths. Surround the path in quotation marks if any directory or the CFC name
has a hyphen.

The cfimport tag now supports importing CFCs and takes a path attribute to specify the path to the CF file to import.

Use the import function or cfimport tag with a path attribute on top of the page only. Using them elsewhere has the
same effect as putting them on top of the page. Therefore, standard coding practice places the import tags or operators
at the top of the file. The cfimport tag can precede a cfcomponent tag. The import CFScript statement must follow the
component statement.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     127
The CFML Programming Language




The ColdFusion Administrator Sever Settings > Caching page now has a Component Cache option, and a Clear
Component Cache button. To prevent ColdFusion from caching resolved component paths, clear the Component
Cache option. Click the Clear Component Cache button to remove any resolved component paths from the cache.

Note: In all cases, ColdFusion automatically imports the com.adobe.coldfusion.* name space for CFCs. You do not have
to import this path explicitly.

The new operator creates an instance of a CFC. It is equivalent to the cfobject tags and CreateObject function. You can
use new as a CFScript operator, or in assignment statements outside a CFScript block, such as in a cfset tag. ColdFusion
does not have a corresponding cfnew tag.

The new operation has the following syntax:

cfObject=new cfcPath(constructorParam1,...)

or

cfObject=new cfcPath(arg1=constructorParam1Value,...)

If the folder name or CFC name has hyphen, use the following syntax:

cfObject=new "cfc-path"(constructorParam1,...)

If you use the import operator to import the directory that contains the CFC, the cfcPath value is the CFC filename.
The constructor parameters can be positional or in name="value" format. When you use the new operator, ColdFusion
does the following:

1 Looks for an initmethod constructor method in the CFC. If found, ColdFusion instantiates the component and
    runs initmethod.

2 If it does not find an initmethod constructor method, it looks for an init constructor method. If found, ColdFusion
    instantiates the component and runs initmethod.

3 If neither method exists, the new operation instantiates the component but does not call a constructor.

Note: Only the new operator automatically invokes the initmethod or init function. The new operator returns the
value returned by init or initmethod and if the return is void it returns the instance of the CFC. The cfobject tags
and the CreateObject function do not invoke the function and you must explicitly call any custom initialization code.


Handling exceptions
ColdFusion provides two statements for exception handling in CFScript: try and catch. These statements are
equivalent to the CFML cftry and cfcatch tags.

Note: For a discussion of exception handling in ColdFusion, see "Handling Errors" on page 275.


Exception handling syntax and rules
Exception-handling code in CFScript has the following format:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   128
The CFML Programming Language




try {
     Code where exceptions will be caught
}
catch(exceptionType exceptionVariable) {
     Code to handle exceptions of type exceptionType
     that occur in the try block
}
...
catch(exceptionTypeN exceptionVariableN) {
     Code to handle exceptions of type
     exceptionTypeN that occur in the try block
}
finally {
     Code that will execute whether there is an exception or not.
}



Note: In CFScript, catch and finally statements follow the try block; you do not place them inside the try block. This
structure differs from that of the cftry tag, which must include the cfcatch and cffinally tags in its body.

When you have a try statement, you must have a catch statement. In the catch block, the exceptionVariable variable
contains the exception type. This variable is the equivalent of the cfcatch tag cfcatch.Type built-in variable.

The finally block is optional. Its code always runs, and runs after the code in the try block and any catch block.


Exception handling example
The following code shows exception handling in CFScript. It uses a CreateObject function to create a Java object.
The catch statement executes only if the CreateObject function generates an exception. The displayed information
includes the exception message; the except.Message variable is the equivalent of calling the Java getMessage method
on the returned Java exception object. The message in the finally block appears after the catch block message.

<cfscript>
     try {
          emp = CreateObject("Java", "Employees");
     }
     catch(any excpt) {
          WriteOutput("The application was unable to perform a required operation.<br>
     Please try again later.<br>If this problem persists, contact
     Customer Service and include the following information:<br>
                     #excpt.Message#<br>");
     }
     finally {
     writeoutput("<br>Thank you for visiting our web site.<br>come back soon!");
     }
</cfscript>



CFScript example
The following example uses these CFScript features:

* Variable assignment

* Function calls

* For loops

* If-else statements




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      129
The CFML Programming Language




* WriteOutput functions

* Switch statements

The example uses CFScript without any other ColdFusion tags. It creates a structure of course applicants. This
structure contains two arrays; the first has accepted students, the second has rejected students. The script also creates
a structure with rejection reasons for some (but not all) rejected students. It then displays the accepted applicants
followed by the rejected students and their rejection reasons.

<html>
<head>
     <title>CFScript Example</title>
</head>
<body>
<cfscript>


     //Set the variables


     acceptedApplicants[1] = "Cora Cardozo";
     acceptedApplicants[2] = "Betty Bethone";
     acceptedApplicants[3] = "Albert Albertson";
     rejectedApplicants[1] = "Erma Erp";
     rejectedApplicants[2] = "David Dalhousie";
     rejectedApplicants[3] = "Franny Farkle";
     applicants.accepted=acceptedApplicants;
     applicants.rejected=rejectedApplicants;


     rejectCode=StructNew();
     rejectCode["David Dalhousie"] = "score";
     rejectCode["Franny Farkle"] = "too late";


     //Sort and display accepted applicants


     ArraySort(applicants.accepted,"text","asc");
     WriteOutput("The following applicants were accepted:<hr>");
     for (j=1;j lte ArrayLen(applicants.accepted);j=j+1) {
          WriteOutput(applicants.accepted[j] & "<br>");
     }
     WriteOutput("<br>");


     //sort and display rejected applicants with reasons information


     ArraySort(applicants.rejected,"text","asc");
     WriteOutput("The following applicants were rejected:<hr>");
     for (j=1;j lte ArrayLen(applicants.rejected);j=j+1) {




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     130
The CFML Programming Language




         applicant=applicants.rejected[j];
         WriteOutput(applicant & "<br>");
         if (StructKeyExists(rejectCode,applicant)) {
               switch(rejectCode[applicant]) {
                    case "score":
                        WriteOutput("Reject reason: Score was too low.<br>");
                        break;
                    case "late":
                        WriteOutput("Reject reason: Application was late.<br>");
                        break;
                    default:
                        WriteOutput("Rejected with invalid reason code.<br>");
               } //end switch
         } //end if
         else {
               WriteOutput("Reject reason was not defined.<br>");
         } //end else
         WriteOutput("<br>");
     } //end for
</cfscript>


Reviewing the code
The following table describes the code:


Code                                                         Description

<cfscript>                                                   Creates two one-dimensional arrays, one with the
//Set the variables                                          accepted applicants and another with the rejected
acceptedApplicants[1] = "Cora Cardozo";
                                                             applicants. The entries in each array are in random order.
acceptedApplicants[2] = "Betty Bethone";
acceptedApplicants[3] = "Albert Albertson";                  Creates a structure and assign each array to an element
rejectedApplicants[1] = "Erma Erp";
                                                             of the structure.
rejectedApplicants[2] = "David Dalhousie";
rejectedApplicants[3] = "Franny Farkle";
                                                             Creates a structure with rejection codes for rejected
applicants.accepted=acceptedApplicants;
                                                             applicants. The rejectedCode structure does not have
applicants.rejected=rejectedApplicants;
                                                             entries for all rejected applicants, and one of its values
rejectCode=StructNew();                                      does not match a valid code. The structure element
rejectCode["David Dalhousie"] = "score";                     references use associative array notation in order to use
rejectCode["Franny Farkle"] = "too late";
                                                             key names that contain spaces.

ArraySort(applicants.accepted,"text","asc");                 Sorts the accepted applicants alphabetically.
WriteOutput("The following applicants were accepted:<hr>");
for (j=1;j lte ArrayLen(applicants.accepted);j=j+1) {        Displays a heading.
WriteOutput(applicants.accepted[j] & "<br>");
}                                                            Loops through the accepted applicants and writes their
WriteOutput("<br>");                                         names. Curly brackets enhance clarity, although they are
                                                             not needed for a single statement loop.

                                                             Writes an additional line break at the end of the list of
                                                             accepted applicants.

ArraySort(applicants.rejected,"text","asc");                 Sorts rejectedApplicants array          alphabetically and
WriteOutput("The following applicants were rejected:<hr>");  writes a heading.

for (j=1;j lte ArrayLen(applicants.rejected);j=j+1) {        Loops through the rejected applicants.
applicant=applicants.rejected[j];
WriteOutput(applicant & "<br>");                             Sets the applicant variable to the applicant name. This
                                                             makes the code clearer and enables you to easily
                                                             reference the rejectCode array         later in the block.

                                                             Writes the applicant name.




                                         

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               131
The CFML Programming Language




 Code                                                                    Description

 if (StructKeyExists(rejectCode,applicant)) {                            Checks the rejectCode structure for a rejection code
 switch(rejectCode[applicant]) {                                         for the applicant.
 case "score":
 WriteOutput("Reject reason: Score was too low.<br>");                   If a code exists, enters a switch statement that examines
 break;                                                                  the rejection code value.
 case "late":
 WriteOutput("Reject reason: Application was late.<br>");                If the rejection code value matches one of the known
 break;
                                                                         codes, displays an expanded explanation of the
 default:
                                                                         meaning. Otherwise (the default case), displays an
 WriteOutput("Rejected with invalid reason code.<br>");
 } //end switch                                                          indication that the rejection code is not valid.
 } //end if
                                                                         Comments at the end of blocks help clarify the control
                                                                         flow.

 else {                                                                  If there is no entry for the applicant in the rejectCode
 WriteOutput("Reject reason was not defined.<br>");                      structure, displays a message indicating that the reason
 } //end else
                                                                         was not defined.

 WriteOutput("<br>");                                                    Displays a blank line after each rejected applicant.
 } //end for
 </cfscript>                                                             Ends the for loop that handles each rejected applicant.

                                                                         Ends the CFScript.




Using Regular Expressions in Functions

Regular expressions let you perform string matching operations using Adobe ColdFusion functions; in particular.
regular expressions work with the following functions:

* REFind

* REFindNoCase

* REMatch

* REMatchNoCase

* REReplace

* REReplaceNoCase

Regular expressions used in the cfinput and cftextinput tags are JavaScript regular expressions, which have a
slightly different syntax than ColdFusion regular expressions. For information on JavaScript regular expressions, see
"Building Dynamic Forms with cfform Tags" on page 722.


About regular expressions
In traditional string matching, as used by the ColdFusion Find and Replace functions, you provide the string pattern
to search for and the string to search. The following example searches a string for the pattern " BIG " and returns a
string index if found. The string index is the location in the search string where the string pattern begins.

<cfset IndexOfOccurrence=Find(" BIG ", "Some BIG string")>
<!--- The value of IndexOfOccurrence is 5 --->

You must provide the exact string pattern to match. If the exact pattern is not found, Find returns an index of 0.
Because you must specify the exact string pattern to match, matches for dynamic data can be difficult, if not impossible,
to construct.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       132
The CFML Programming Language




The next example uses a regular expression to perform the same search. This example searches for the first occurrence
in the search string of any string pattern that consists entirely of uppercase letters enclosed by spaces:

<cfset IndexOfOccurrence=REFind(" [A-Z]+ ", "Some BIG string")>
<!--- The value of IndexOfOccurrence is 5 --->

The regular expression " [A-Z]+ " matches any string pattern consisting of a leading space, followed by any number of
uppercase letters, followed by a trailing space. Therefore, this regular expression matches the string " BIG " and any
string of uppercase letters enclosed in spaces.

By default, the matching of regular expressions is case-sensitive. You can use the REFindNoCase and
REReplaceNoCase functions for case-insensitive matching.

Because you often process large amounts of dynamic textual data, regular expressions are invaluable in writing
complex ColdFusion applications.


Using ColdFusion regular expression functions
ColdFusion supplies four functions that work with regular expressions:

* REFind

* REFindNoCase

* REMatch

* REMatchNoCase

* REReplace

* REReplaceNoCase

REFind and REFindNoCase use a regular expression to search a string for a pattern and return the string index where
it finds the pattern. For example, the following function returns the index of the first instance of the string " BIG ":

<cfset IndexOfOccurrence=REFind(" BIG ", "Some BIG BIG string")>
<!--- The value of IndexOfOccurrence is 5 --->

To find the next occurrence of the string " BIG ", you must call the REFind function a second time. For an example of
iterating over a search string to find all occurrences of the regular expression, see "Returning matched subexpressions"
on page 141.

REReplace and REReplaceNoCase use regular expressions to search through a string and replace the string pattern
that matches the regular expression with another string. You can use these functions to replace the first match, or to
replace all matches.

For detailed descriptions of the ColdFusion functions that use regular expressions, see the CFML Reference.


Basic regular expression syntax
The simplest regular expression contains only literal characters. The literal characters must match exactly the text
being searched. For example, you can use the regular expression function REFind to find the string pattern " BIG ", just
as you can with the Find function:

<cfset IndexOfOccurrence=REFind(" BIG ", "Some BIG string")>
<!--- The value of IndexOfOccurrence is 5 --->

In this example, REFind must match the exact string pattern " BIG ".

To use the full power of regular expressions, combine literal characters with character sets and special characters, as in
the following example:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          133
The CFML Programming Language




<cfset IndexOfOccurrence=REFind(" [A-Z]+ ", "Some BIG string")>
<!--- The value of IndexOfOccurrence is 5 --->

The literal characters of the regular expression consist of the space characters at the beginning and end of the regular
expression. The character set consists of that part of the regular expression in brackets. This character set specifies to
find a single uppercase letter from A to Z, inclusive. The plus sign (+) after the brackets is a special character specifying
to find one or more occurrences of the character set.

If you removed the + from the regular expression in the previous example, " [A-Z] " matches a literal space, followed
by any single uppercase letter, followed by a single space. This regular expression matches " B " but not " BIG ". The
REFind function returns 0 for the regular expression, meaning that it did not find a match.

You can construct complicated regular expressions containing literal characters, character sets, and special characters.
Like any programming language, the more you work with regular expressions, the more you can accomplish with
them. The examples here are fairly basic. For more examples, see "Regular expression examples" on page 143.


Regular expression syntax
Regular expression syntax has several basic rules and methods.


Using character sets
The pattern within the brackets of a regular expression defines a character set that is used to match a single character.
For example, the regular expression " [A-Za-z] " specifies to match any single uppercase or lowercase letter enclosed
by spaces. In the character set, a hyphen indicates a range of characters.

The regular expression " B[IAU]G " matches the strings " BIG ", " BAG ", and " BUG ", but does not match the string
" BOG ".

If you specified the regular expression as " B[IA][GN] ", the concatenation of character sets creates a regular expression
that matches the corresponding concatenation of characters in the search string. This regular expression matches a
space, followed by "B", followed by an "I" or "A", followed by a "G" or "N", followed by a trailing space. The regular
expression matches " BIG ", " BAG ", "BIN ", and "BAN ".

The regular expression [A-Z][a-z]* matches any word that starts with an uppercase letter and is followed by zero or
more lowercase letters. The special character * after the closing square bracket specifies to match zero or more
occurrences of the character set.

Note: The * only applies to the character set that immediately precedes it, not to the entire regular expression.

A + after the closing square bracket specifies to find one or more occurrences of the character set. You interpret the
regular expression "[A-Z]+" as matching one or more uppercase letters enclosed by spaces. Therefore, this regular
expression matches " BIG " and also matches " LARGE ", " HUGE ", " ENORMOUS ", and any other string of
uppercase letters surrounded by spaces.


Considerations when using special characters
Since a regular expression followed by an * can match zero instances of the regular expression, it can also match the
empty string. For example,

<cfoutput>
     REReplace("Hello","[T]*","7","ALL") - #REReplace("Hello","[T]*","7","ALL")#<BR>
</cfoutput>

results in the following output:

REReplace("Hello","[T]*","7","ALL") - 7H7e7l7l7o




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       134
The CFML Programming Language




The regular expression [T]* can match empty strings. It first matches the empty string before "H" in "Hello". The
"ALL" argument tells REReplace to replace all instances of an expression. The empty string before "e" is matched, and
so on, until the empty string before "o" is matched.

This result might be unexpected. The workarounds for these types of problems are specific to each case. In some cases
you can use [T]+, which requires at least one "T", instead of [T]*. Alternatively, you can specify an additional pattern
after [T]*.

In the following examples the regular expression has a "W" at the end:

<cfoutput>
     REReplace("Hello World","[T]*W","7","ALL") ­
           #REReplace("Hello World","[T]*W","7","ALL")#<BR>
</cfoutput>

This expression results in the following more predictable output:

REReplace("Hello World","[T]*W","7","ALL") - Hello 7orld


Finding repeating characters
In some cases, you might want to find a repeating pattern of characters in a search string. For example, the regular
expression "a{2,4}" specifies to match two to four occurrences of "a". Therefore, it would match: "aa", "aaa", "aaaa", but
not "a" or "aaaaa". In the following example, the REFind function returns an index of 6:

<cfset IndexOfOccurrence=REFind("a{2,4}", "hahahaaahaaaahaaaaahhh")>
<!--- The value of IndexOfOccurrence is 6--->

The regular expression "[0-9]{3,}" specifies to match any integer number containing three or more digits: "123",
"45678", and so on. However, this regular expression does not match a one-digit or two-digit number.

You use the following syntax to find repeating characters:

1 {m,n}

   Where m is 0 or greater and n is greater than or equal to m. Match m through n (inclusive) occurrences.

   The expression {0,1} is equivalent to the special character ?.

2 {m,}

   Where m is 0 or greater. Match at least m occurrences. The syntax {,n} is not allowed.

   The expression {1,} is equivalent to the special character +, and {0,} is equivalent to *.

3 {m}

   Where m is 0 or greater. Match exactly m occurrences.


Case sensitivity in regular expressions
ColdFusion supplies case-sensitive and case-insensitive functions for working with regular expressions. REFind and
REReplace perform case-sensitive matching and REFindNoCase and REReplaceNoCase perform case-insensitive
matching.

You can build a regular expression that models case-insensitive behavior, even when used with a case-sensitive
function. To make a regular expression case insensitive, substitute individual characters with character sets. For
example, the regular expression [Jj][Aa][Vv][Aa], when used with the case-sensitive functions REFind or REReplace,
matches all of the following string patterns:

* JAVA




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                           135
The CFML Programming Language




* java

* Java

* jAva

* All other combinations of case


Using subexpressions
Parentheses group parts of regular expressions into subexpressions that you can treat as a single unit. For example, the
regular expression "ha" specifies to match a single occurrence of the string. The regular expression "(ha)+" matches one
or more instances of "ha".

In the following example, you use the regular expression "B(ha)+" to match the letter "B" followed by one or more
occurrences of the string "ha":

<cfset IndexOfOccurrence=REFind("B(ha)+", "hahaBhahahaha")>
<!--- The value of IndexOfOccurrence is 5 --->

You can use the special character | in a subexpression to create a logical "OR". You can use the following regular
expression to search for the word "jelly" or "jellies":

<cfset IndexOfOccurrence=REFind("jell(y|ies)", "I like peanut butter and jelly">
<!--- The value of IndexOfOccurrence is 26--->


Using special characters
Regular expressions define the following list of special characters:

+ * ? . [ ^ $ ( ) { | \

In some cases, you use a special character as a literal character. For example, if you want to search for the plus sign in
a string, you have to escape the plus sign by preceding it with a backslash:

"\+"

The following table describes the special characters for regular expressions:


Special Character       Description

\                       A backslash followed by any special character matches the literal character itself, that is, the backslash escapes the
                        special character.

                        For example, "\+" matches the plus sign, and "\\" matches a backslash.

.                       A period matches any character, including newline.

                        To match any character except a newline, use [^#chr(13)##chr(10)#], which excludes the ASCII carriage return and
                        line feed codes. The corresponding escape codes are \r and \n.

[ ]                     A one-character character set that matches any of the characters in that set.

                        For example, "[akm]" matches an "a", "k", or "m". A hyphen in a character set indicates a range of characters; for
                        example, [a-z] matches any single lowercase letter.

                        If the first character of a character set is the caret (^), the regular expression matches any character except those
                        in the set. It does not match the empty string.

                        For example, [^akm] matches any character except "a", "k", or "m". The caret loses its special meaning if it is not
                        the first character of the set.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         136
The CFML Programming Language




Special Character      Description

^                      If the caret is at the beginning of a regular expression, the matched string must be at the beginning of the string
                       being searched.

                       For example, the regular expression "^ColdFusion" matches the string "ColdFusion lets you use regular
                       expressions" but not the string "In ColdFusion, you can use regular expressions."

$                      If the dollar sign is at the end of a regular expression, the matched string must be at the end of the string being
                       searched.

                       For example, the regular expression "ColdFusion$" matches the string "I like ColdFusion" but not the string
                       "ColdFusion is fun."

?                      A character set or subexpression followed by a question mark matches zero or one occurrence of the character
                       set or subexpression.

                       For example, xy?z matches either "xyz" or "xz".

|                      The OR character allows a choice between two regular expressions.

                       For example, jell(y|ies) matches either "jelly" or "jellies".

+                      A character set or subexpression followed by a plus sign matches one or more occurrences of the character set or
                       subexpression.

                       For example, [a-z]+ matches one or more lowercase characters.

*                      A character set or subexpression followed by an asterisk matches zero or more occurrences of the character set or
                       subexpression.

                       For example, [a-z]* matches zero or more lowercase characters.

()                     Parentheses group parts of a regular expression into subexpressions that you can treat as a single unit.

                       For example, (ha)+ matches one or more instances of "ha".

(?x)                   If at the beginning of a regular expression, it specifies to ignore whitespace in the regular expression and lets you
                       use ## for end-of-line comments. You can match a space by escaping it with a backslash.

                       For example, the following regular expression includes comments, preceded by ##, that are ignored by
                       ColdFusion:

                       reFind("(?x)

                       one ##first option

                       |two ##second option

                       |three\ point\ five ## note escaped spaces

                       ", "three point five")

(?m)                   If at the beginning of a regular expression, it specifies the multiline mode for the special characters ^ and $.

                       When used with ^, the matched string can be at the start of the entire search string or at the start of new lines,
                       denoted by a linefeed character or chr(10), within the search string. For $, the matched string can be at the end
                       the search string or at the end of new lines.

                       Multiline mode does not recognize a carriage return, or chr(13), as a new line character.

                       The following example searches for the string "two" across multiple lines:

                       #reFind("(?m)^two", "one#chr(10)#two")#

                       This example returns 4 to indicate that it matched "two" after the chr(10) linefeed. Without (?m), the regular
                       expression would not match anything, because ^ only matches the start of the string.

                       The character (?m) does not affect \A or \Z, which always match the start or end of the string, respectively. For
                       information on \A and \Z, see "Using escape sequences" on page 137.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                           137
The CFML Programming Language




Special Character        Description

(?i)                     If at the beginning of a regular expression for REFind(), it specifies to perform a case-insensitive compare.

                         For example, the following line would return an index of 1:

                         #reFind("(?i)hi", "HI")#

                         If you omit the (?i), the line would return an index of zero to signify that it did not find the regular expression.

(?=...)                  If at the beginning of a regular expression, it specifies to use positive lookahead when searching for the regular
                         expression.

                         Positive lookahead tests for the parenthesized subexpression like regular parenthesis, but does not include the
                         contents in the match - it merely tests to see if it is there in proximity to the rest of the expression.

                         For example, consider the expression to extract the protocol from a URL:

                         <cfset regex = "http(?=://)">

                         <cfset string = "http://">

                         <cfset result = reFind(regex, string, 1, "yes")>

                         mid(string, result.pos[1], result.len[1])

                         This example results in the string "http". The lookahead parentheses ensure that the "://" is there, but does not
                         include it in the result. If you did not use lookahead, the result would include the extraneous "://".

                         Lookahead parentheses do not capture text, so backreference numbering will skip over these groups. For more
                         information on backreferencing, see "Using backreferences" on page 139.

(?!...)                  If at the beginning of a regular expression, it specifies to use negative lookahead. Negative is just like positive
                         lookahead, as specified by (?=...), except that it tests for the absence of a match.

                         Lookahead parentheses do not capture text, so backreference numbering will skip over these groups. For more
                         information on backreferencing, see "Using backreferences" on page 139.

(?:...)                  If you prefix a subexpression with "?:", ColdFusion performs all operations on the subexpression except that it will
                         not capture the corresponding text for use with a back reference.


You must be aware of the following considerations when using special characters in character sets, such as [a-z]:

* To include a hyphen (-) in the brackets of a character set as a literal character, you cannot escape it as you can other
    special characters because ColdFusion always interprets a hyphen as a range indicator. Therefore, if you use a literal
    hyphen in a character set, make it the last character in the set.

* To include a closing square bracket (]) in the character set, escape it with a backslash, as in [1-3\]A-z]. You do not
    have to escape the ] character outside the character set designator.


Using escape sequences
Escape sequences are special characters in regular expressions preceded by a backslash (\). You typically use escape
sequences to represent special characters within a regular expression. For example, the escape sequence \t represents
a tab character within the regular expression, and the \d escape sequence specifies any digit, as [0-9] does. ColdFusion
escape sequences are case sensitive.

The following table lists the escape sequences that ColdFusion supports:




                                                       

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                       138
The CFML Programming Language




Escape Sequence         Description

\b                      Specifies a boundary defined by a transition from an alphanumeric character to a nonalphanumeric character, or
                        from a nonalphanumeric character to an alphanumeric character.

                        For example, the string " Big" contains boundary defined by the space (nonalphanumeric character) and the "B"
                        (alphanumeric character).

                        The following example uses the \b escape sequence in a regular expression to locate the string "Big" at the end of
                        the search string and not the fragment "big" inside the word "ambiguous".

                        reFindNoCase("\bBig\b", "Don't be ambiguous about Big.")

                        <!--- The value of IndexOfOccurrence is 26 --->

                        When used inside a character set (for example [\b]), it specifies a backspace

\B                      Specifies a boundary defined by no transition of character type. For example, two alphanumeric characters in a
                        row or two nonalphanumeric characters in a row; opposite of \b.

\A                      Specifies a beginning of string anchor, much like the ^ special character.

                        However, unlike ^, you cannot combine \A with (?m) to specify the start of newlines in the search string.

\Z                      Specifies an end of string anchor, much like the $ special character.

                        However, unlike $, you cannot combine \Z with (?m) to specify the end of newlines in the search string.

\n                      Newline character

\r                      Carriage return

\t                      Tab

\f                      Form feed

\d                      Any digit, similar to [0-9]

\D                      Any nondigit character, similar to [^0-9]

\w                      Any alphanumeric character, or the underscore (_), similar to [[:word:]]

\W                      Any nonalphanumeric character, except the underscore similar to [^[:word:]]

\s                      Any whitespace character including tab, space, newline, carriage return, and form feed. Similar to [ \t\n\r\f].

\S                      Any nonwhitespace character, similar to [^ \t\n\r\f]

\\x                     A hexadecimal representation of character, where d is a hexadecimal digit

\ddd                    An octal representation of a character, where d is an octal digit, in the form \000 to \377



Using character classes
In character sets within regular expressions, you can include a character class. You enclose the character class inside
brackets, as the following example shows:

REReplace ("Adobe Web Site","[[:space:]]","*","ALL")

This code replaces all the spaces with *, producing this string:

Adobe*Web*Site

You can combine character classes with other expressions within a character set. For example, the regular expression
[[:space:]123] searches for a space, 1, 2, or 3. The following example also uses a character class in a regular expression:

<cfset IndexOfOccurrence=REFind("[[:space:]][A-Z]+[[:space:]]",
     "Some BIG string")>
<!--- The value of IndexOfOccurrence is 5 --->



                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                                    139
The CFML Programming Language




The following table shows the character classes that ColdFusion supports. Regular expressions using these classes
match any Unicode character in the class, not just ASCII or ISO-8859 characters.


Character class         Matches

:alpha:                 Any alphabetic character.

:upper:                 Any uppercase alphabetic character.

:lower:                 Any lowercase alphabetic character

:digit:                 Any digit. Same as \d.

:alnum:                 Any alphabetic or numeric character.

:xdigit:                Any hexadecimal digit. Same as [0-9A-Fa-f].

:blank:                 Space or a tab.

:space:                 Any whitespace character. Same as \s.

:print:                 Any alphanumeric, punctuation, or space character.

:punct:                 Any punctuation character

:graph:                 Any alphanumeric or punctuation character.

:cntrl:                 Any character not part of the character classes [:upper:], [:lower:], [:alpha:], [:digit:], [:punct:], [:graph:], [:print:], or
                        [:xdigit:].

:word:                  Any alphabetic or numeric character, plus the underscore (_). Same as \w

:ascii:                 The ASCII characters, in the Hexadecimal range 0 - 7F



Using backreferences
You use parenthesis to group components of a regular expression into subexpressions. For example, the regular
expression "(ha)+" matches one or more occurrences of the string "ha".

ColdFusion performs an additional operation when using subexpressions; it automatically saves the characters in the
search string matched by a subexpression for later use within the regular expression. Referencing the saved
subexpression text is called backreferencing.

You can use backreferencing when searching for repeated words in a string, such as "the the" or "is is". The following
example uses backreferencing to find all repeated words in the search string and replace them with an asterisk:

REReplace("There is is coffee in the the kitchen",
      "[ ]+([A-Za-z]+)[ ]+\1"," * ","ALL")

Using this regular expression, ColdFusion detects the two occurrences of "is" as well as the two occurrences of "the",
replaces them with an asterisk enclosed in spaces, and returns the following string:

There * coffee in * kitchen

You interpret the regular expression [ ]+([A-Za-z]+)[ ]+\1 as follows:

Use the subexpression ([A-Za-z]+) to search for character strings consisting of one or more letters, enclosed by one or
more spaces, [ ]+, followed by the same character string that matched the first subexpression, \1.

You reference the matched characters of a subexpression using a slash followed by a digit n (\n) where the first
subexpression in a regular expression is referenced as \1, the second as \2, and so on. The next section includes an
example using multiple backreferences.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       140
The CFML Programming Language




Using backreferences in replacement strings
You can use backreferences in the replacement string of both the REReplace and REReplaceNoCase functions. For
example, to replace the first repeated word in a text string with a single word, use the following syntax:

REReplace("There is is a cat in in the kitchen",
     "([A-Za-z ]+)\1","\1")

This results in the sentence:

"There is a cat in in the kitchen"

You can use the optional fourth parameter to REReplace, scope, to replace all repeated words, as in the following code:

REReplace("There is is a cat in in the kitchen",
     "([A-Za-z ]+)\1","\1","ALL")

This results in the following string:

"There is a cat in the kitchen"

The next example uses two backreferences to reverse the order of the words "apples" and "pears" in a sentence:

<cfset astring = "apples and pears, apples and pears, apples and pears">
<cfset newString = REReplace("#astring#", "(apples) and (pears)",
     "\2 and \1","ALL")>

In this example, you reference the subexpression (apples) as \1 and the subexpression (pears) as \2. The REReplace
function returns the string:

"pears and apples, pears and apples, pears and apples"

Note: To use backreferences in either the search string or the replace string, you must use parentheses within the regular
expression to create the corresponding subexpression. Otherwise, ColdFusion throws an exception.


Using backreferences to perform case conversions in replacement strings
The REReplace and REReplaceNoCase functions support special characters in replacement strings to convert
replacement characters to uppercase or lowercase. The following table describes these special characters:


 Special character       Description

 \u                      Converts the next character to uppercase.

 \l                      Converts the next character to lowercase.

 \U                      Converts all characters to uppercase until encountering \E.

 \L                      Converts all characters to lowercase until encountering \E.

 \E                      End \U or \L.


To include a literal \u, or other code, in a replacement string, escape it with another backslash; for example \\u.

For example, the following statement replaces the uppercase string "HELLO" with a lowercase "hello". This example
uses backreferences to perform the replacement.

REReplace("HELLO", "([[:upper:]]*)", "Don't shout\scream \L\1")

The result of this example is the string "Don't shout\scream hello".




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       141
The CFML Programming Language




Escaping special characters in replacement strings
You use the backslash character, \, to escape backreference and case-conversion characters in replacement strings. For
example, to include a literal "\u" in a replacement string, escape it, as in "\\u".


Omitting subexpressions from backreferences
By default, a set of parentheses will both group the subexpression and capture its matched text for later referral by
backreferences. However, if you insert "?:" as the first characters of the subexpression, ColdFusion performs all
operations on the subexpression except that it will not capture the corresponding text for use with a back reference.

This is useful when alternating over subexpressions containing differing numbers of groups would complicate
backreference numbering. For example, consider an expression to insert a "Mr." in between Bonjour|Hi|Hello and
Bond, using a nested group for alternating between Hi & Hello:

<cfset regex = "(Bonjour|H(?:i|ello))( Bond)">
<cfset replaceString = "\1 Mr.\2">
<cfset string = "Hello Bond">
#REReplace(string, regex, replaceString)#

This example returns "Hello Mr. Bond". If you did not prohibit the capturing of the Hi/Hello group, the \2
backreference would end up referring to that group instead of " Bond", and the result would be "Hello Mr.ello".


Returning matched subexpressions
The REFind and REFindNoCase functions return the location in the search string of the first match of the regular
expression. Even though the search string in the next example contains two matches of the regular expression, the
function only returns the index of the first:

<cfset IndexOfOccurrence=REFind(" BIG ", "Some BIG BIG string")>
<!--- The value of IndexOfOccurrence is 5 --->

To find all instances of the regular expression, you must call the REFind and REFindNoCase functions multiple times.

Both the REFind and REFindNoCase functions take an optional third parameter that specifies the starting index in the
search string for the search. By default, the starting location is index 1, the beginning of the string.

To find the second instance of the regular expression in this example, you call REFind with a starting index of 8:

<cfset IndexOfOccurrence=REFind(" BIG ", "Some BIG BIG string", 8)>
<!--- The value of IndexOfOccurrence is 9 --->

In this case, the function returns an index of 9, the starting index of the second string " BIG ".

To find the second occurrence of the string, you must know that the first string occurred at index 5 and that the string's
length was 5. However, REFind only returns starting index of the string, not its length. So, you either must know the
length of the matched string to call REFind the second time, or you must use subexpressions in the regular expression.

The REFind and REFindNoCase functions let you get information about matched subexpressions. If you set these
functions' fourth parameter, ReturnSubExpression, to True, the functions return a CFML structure with two arrays,
pos and len, containing the positions and lengths of text strings that match the subexpressions of a regular expression,
as the following example shows:

<cfset sLenPos=REFind(" BIG ", "Some BIG BIG string", 1, "True")>
<cfoutput>
     <cfdump var="#sLenPos#">
</cfoutput><br>




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        142
The CFML Programming Language




Element one of the pos array contains the starting index in the search string of the string that matched the regular
expression. Element one of the len array contains length of the matched string. For this example, the index of the first
" BIG " string is 5 and its length is also 5. If the regular expression does not occur, the pos and len arrays each contain
one element with a value of 0.

You can use the returned information with other string functions, such as mid. The following example returns that
part of the search string matching the regular expression:

<cfset myString="Some BIG BIG string">
<cfset sLenPos=REFind(" BIG ", myString, 1, "True")>
<cfoutput>
     #mid(myString, sLenPos.pos[1], sLenPos.len[1])#
</cfoutput>

Each additional element in the pos array contains the position of the first match of each subexpression in the search
string. Each additional element in len contains the length of the subexpression's match.

In the previous example, the regular expression " BIG " contained no subexpressions. Therefore, each array in the
structure returned by REFind contains a single element.

After executing the previous example, you can call REFind a second time to find the second occurrence of the regular
expression. This time, you use the information returned by the first call to make the second:

<cfset newstart = sLenPos.pos[1] + sLenPos.len[1] - 1>
<!--- subtract 1 because you need to start at the first space --->
<cfset sLenPos2=REFind(" BIG ", "Some BIG BIG string", newstart, "True")>
<cfoutput>
     <cfdump var="#sLenPos2#">
</cfoutput><br>

If you include subexpressions in your regular expression, each element of pos and len after element one contains the
position and length of the first occurrence of each subexpression in the search string.

In the following example, the expression [A-Za-z]+ is a subexpression of a regular expression. The first match for the
expression ([A-Za-z]+)[ ]+, is "is is".

<cfset sLenPos=REFind("([A-Za-z]+)[ ]+\1",
     "There is is a cat in in the kitchen", 1, "True")>
<cfoutput>
     <cfdump var="#sLenPos#">
</cfoutput><br>

The entries sLenPos.pos[1] and sLenPos.len[1] contain information about the match of the entire regular expression.
The array elements sLenPos.pos[2] and sLenPos.len[2] contain information about the first subexpression ("is").
Because REFind returns information on the first regular expression match only, the sLenPos structure does not
contain information about the second match to the regular expression, "in in".

The regular expression in the following example uses two subexpressions. Therefore, each array in the output structure
contains the position and length of the first match of the entire regular expression, the first match of the first
subexpression, and the first match of the second subexpression.

<cfset sString = "apples and pears, apples and pears, apples and pears">
<cfset regex = "(apples) and (pears)">
<cfset sLenPos = REFind(regex, sString, 1, "True")>
<cfoutput>
     <cfdump var="#sLenPos#">
</cfoutput>




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      143
The CFML Programming Language




For a full discussion of subexpression usage, see the sections on REFind and REFindNoCase in the ColdFusion
functions chapter in the CFML Reference.


Specifying minimal matching
The regular expression quantifiers ?, *, +, {min,} and {min,max} specify one or both of a minimum and maximum
number of instances of a given expression to match. By default, ColdFusion locates the greatest number characters in
the search string that match the regular expression. This behavior is called maximal matching.

For example, you use the regular expression "<b>(.*)</b>" to search the string "<b>one</b> <b>two</b>". The regular
expression "<b>(.*)</b>", matches both of the following:

* <b>one</b>

* <b>one</b> <b>two</b>

By default, ColdFusion always tries to match the regular expression to the largest string in the search string. The
following code shows the results of this example:

<cfset sLenPos=REFind("<b>(.*)</b>", "<b>one</b> <b>two</b>", 1, "True")>
<cfoutput>
      <cfdump var="#sLenPos#">
</cfoutput><br>

Thus, the starting position of the string is 1 and its length is 21, which corresponds to the largest of the two possible
matches.

However, sometimes you might want to override this default behavior to find the shortest string that matches the
regular expression. ColdFusion includes minimal-matching quantifiers that let you specify to match on the smallest
string. The following table describes these expressions:


 Expression              Description

 *?                      minimal-matching version of *

 +?                      minimal-matching version of +

 ??                      minimal-matching version of ?

 {min,}?                 minimal-matching version of {min,}

 {min,max}?              minimal-matching version of {min,max}

 {n}?                    (no different from {n}, supported for notational consistency)


If you modify the previous example to use the minimal-matching syntax, the code is as follows:

<cfset sLenPos=REFind("<b>(.*?)</b>", "<b>one</b> <b>two</b>", 1, "True")>
<cfoutput>
      <cfdump var="#sLenPos#">
</cfoutput><br>

Thus, the length of the string found by the regular expression is 10, corresponding to the string "<b>one</b>".


Regular expression examples
The following examples show some regular expressions and describe what they match:




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               144
The CFML Programming Language




Expression                                                  Description

[\?&]value=                                                 A URL parameter value in a URL.

[A-Z]:(\\[A-Z0-9_]+)+                                       An uppercase DOS/Windows path in which (a) is not the root of a
                                                            drive, and (b) has only letters, numbers, and underscores in its text.

^[A-Za-z][A-Za-z0-9_]*                                      A ColdFusion variable with no qualifier.

([A-Za-z][A-Za-z0-9_]*)(\.[A-Za-z][A-Za-z0-9_]*)?           A ColdFusion variable with no more than one qualifier; for example,
                                                            Form.VarName, but not Form.Image.VarName.

(\+|-)?[1-9][0-9]*                                          An integer that does not begin with a zero and has an optional sign.

(\+|-)?[0-9]+(\.[0-9]*)?                                    A real number.

(\+|-)?[1-9]\.[0-9]*E(\+|-)?[0-9]+                          A real number in engineering notation.

a{2,4}                                                      Two to four occurrences of "a": aa, aaa, aaaa.

(ba){3,}                                                    At least three "ba" pairs: bababa, babababa, and so on.



Regular expressions in CFML
The following examples of CFML show some common uses of regular expression functions:


Expression                                                                                Returns

REReplace (CGI.Query_String, "CFID=[0-9]+[&]*", "")                                       The query string with parameter CFID
                                                                                          and its numeric value stripped out.

REReplace("I Love Jellies", "[[:lower:]]","x","ALL"                                       I Lxxx Jxxxxxx

REReplaceNoCase("cabaret","[A-Z]", "G","ALL")                                             GGGGGGG

REReplace (Report,"\$[0-9,]*\.[0-9]*","$***.**")", "")                                    The string value of the variable Report
                                                                                          with all positive numbers in the dollar
                                                                                          format changed to "$***.**".

REFind ("[Uu]\.?[Ss]\.?[Aa}\.?", Report )                                                 The position in the variable Report of
                                                                                          the first occurrence of the abbreviation
                                                                                          USA. The letters can be in either case
                                                                                          and the abbreviation can have a period
                                                                                          after any letter.

REFindNoCase("a+c","ABCAACCDD")                                                           4

REReplace("There is is coffee in the the kitchen","([A-Za-z]+)[                           There * coffee in * kitchen
]+\1","*","ALL")


REReplace(report, "<[^>]*>", "", "All")                                                   Removes all HTML tags from a string
                                                                                          value of the report variable.



Types of regular expression technologies
Many types of regular expression technologies are available to programmers. JavaScript, Perl, and POSIX are all
examples of different regular expression technologies. Each technology has its own syntax specifications and is not
necessarily compatible with other technologies.

ColdFusion supports regular expressions that are Perl compliant with a few exceptions:

* A period, ., always matches newlines.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    145
The CFML Programming Language




* In replacement strings, use \n instead of $n for backreference variables. ColdFusion escapes all $ in the replacement
  string.

* You do not have to escape backslashes in replacement strings. ColdFusion escapes them, except in case conversion
  sequences or escaped versions (for example, \u or \\u).

* Embedded modifiers such as (?i) always operate on the entire expression, even if they are inside a group.

* \` and the combinations \u\L and \l\U are not supported in replacement strings.

The following Perl statements are not supported:

* Lookbehind (?<=) (<?!)

* \\x

* \N

* \p

* \C

An excellent reference on regular expressions is Mastering Regular Expressions, by Jeffrey E. F. Friedl, O'Reilly &
Associates, Inc., 1997, ISBN: 1-56592-257-3, available at www.oreilly.com.

