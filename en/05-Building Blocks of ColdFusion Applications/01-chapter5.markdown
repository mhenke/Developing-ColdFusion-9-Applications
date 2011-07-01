Chapter 5: Building Blocks of ColdFusion
Applications

Creating ColdFusion Elements

You can create ColdFusion elements to organize your code. When you create any of these elements, you write your
code once and use it, without copying it, in many places.


About CFML elements that you create
Adobe ColdFusion provides you with several techniques and elements to create sections of code that you can use
multiple times in an application. Many of the elements also let you extend the built-in capabilities of ColdFusion.
ColdFusion provides the following techniques and elements:

* ColdFusion pages you include using the cfinclude tag

* User-defined functions (UDFs)

* ColdFusion components

* Custom CFML tags

* CFX (ColdFusion Extension) tags

ColdFusion can also use elements developed using other technologies, including the following:

* JSP tags from JSP tag libraries. For information on using JSP tags, see "Integrating J2EE and Java Elements in CFML
   Applications" on page 1125.

* Java objects, including objects in the Java run-time environment and JavaBeans. For information on using Java
   objects, see "Integrating J2EE and Java Elements in CFML Applications" on page 1125.

* Microsoft COM (Component Object Model) objects. For information on using COM objects, see "Integrating
   COM and CORBA Objects in CFML Applications" on page 1170.

* CORBA (Common Object Request Broker Architecture) objects. For information on using CORBA objects, see
   "Integrating COM and CORBA Objects in CFML Applications" on page 1170.

* Web services. For information on using web services, see "Using Web Services" on page 1093.


Including pages with the cfinclude tag
The cfinclude tag adds the contents of a ColdFusion page to another ColdFusion page, as if the code on the included
page was part of the page that uses the cfinclude tag. It lets you pursue a "write once use multiple times" strategy for
ColdFusion elements that you incorporate in multiple pages. Instead of copying and maintaining the same code on
multiple pages, you can store the code in one page and then reference it in many pages. For example, the cfinclude
tag is commonly used to place a header and footer on multiple pages. This way, if you change the header or footer
design, you only change the contents of a single file.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         147
Building Blocks of ColdFusion Applications




The model of an included page is that it is part of your page; it just resides in a separate file. The cfinclude tag cannot
pass parameters to the included page, but the included page has access to all the variables on the page that includes it.
The following image shows this model:


                 <html>
                                                         <table>
                 ...
                                                         <tr><td>
                 <cnclude template=
                                                         Included Text here
                    "Template.cfm">
                                                         </td></tr>
                 ...
                 </html>                                 </table>

MyPage.cfm                                Template.cfm

                                   <html>
                                   ...
                                   <table>
                                   <tr><td>
                                   Included Text here
                                   </td></tr>
                                   </table>
                                   ...
                    MyPage.cfm
                                   </html>



Using the cfinclude tag
When you use the cfinclude tag to include one ColdFusion page in another ColdFusion page, the page that includes
another page is referred to as the calling page. When ColdFusion encounters a cfinclude tag it replaces the tag on the
calling page with the output from processing the included page. The included page can also set variables in the calling page.

The following line shows a sample cfinclude tag:

<cfinclude template = "header.cfm">

Note: You cannot break CFML code blocks across pages. For example, if you open a cfoutput block in a ColdFusion
page, close the block on the same page; you cannot include the closing portion of the block in an included page.

ColdFusion searches for included files as follows:

* The template attribute specifies a path relative to the directory of the calling page.

* If the template value is prefixed with a forward slash (/), ColdFusion searches for the included file in directories that
   you specify on the Mappings page of the ColdFusion Administrator.

Important: A page must not include itself. Doing so causes an infinite processing loop. To resolve the problem, stop the
ColdFusion server.


Include code in a calling page
1 Create a ColdFusion page named header.cfm that displays your logo. Your page can consist of just the following
   lines, or it can include many lines to define an entire header:

    <img src="mylogo.gif">
    <br>

   (For this example to work, you must also place your logo as a GIF file in the same directory as the header.cfm file.)

2 Create a ColdFusion page with the following content:




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 148
Building Blocks of ColdFusion Applications




    <html>
   <head>
         <title>Test for Include</title>
   </head>
   <body>
         <cfinclude template="header.cfm">
   </body>
   </html>

3 Save the file as includeheader.cfm and view it in a browser.

The header appears along with the logo.


Recommended uses
Consider using the cfinclude tag in the following cases:

* For page headers and footers

* To divide a large page into multiple logical chunks that are easier to understand and manage

* For large "snippets" of code that are used in many places but do not require parameters or fit into the model of a
   function or tag


About user-defined functions
User-defined functions (UDFs) let you create application elements in a format in which you pass in arguments and get
a return a value. You can define UDFs using CFScript or the cffunction tag. The two techniques have several
differences, of which the following are the most important:

* If you use the cffunction tag, your function can include CFML tags.

* If you write your function using CFScript, you cannot include CFML tags.

You can use UDFs in your application pages just as you use standard ColdFusion functions. When you create a
function for an algorithm or procedure that you use frequently, you can then use the function wherever you need the
procedure, just as you would use a ColdFusion built-in function. For example, the following line calls the function
MyFunct and passes it two arguments:

<cfset returnValue=MyFunct(Arg1, Arg2)>

You can group related functions in a ColdFusion component. For more information, see "Using ColdFusion
components" on page 149.

As with custom tags, you can easily distribute UDFs to others. For example, the Common Function Library Project at
www.cflib.org is an open-source collection of CFML user-defined functions.


Recommended uses
Typical uses of UDFs include, but are not limited to, the following:

* Data manipulation routines, such as a function to reverse an array

* String and date and time routines, such as a function to determine whether a string is a valid IP address

* Mathematical calculation routines, including standard trigonometric and statistical operations or calculating loan
   amortization

* Routines that call functions externally, for example using COM or CORBA, such as routines to determine the space
   available on a Windows file system drive




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     149
Building Blocks of ColdFusion Applications




Consider using UDFs in the following circumstances:

* You want to pass in arguments, process the results, and return a value. UDFs can return complex values, including
   structures that contain multiple simple values.

* You want to provide logical units, such as data manipulation functions.

* Your code must be recursive.

* You distribute your code to others.

If you can create either a UDF or a custom CFML tag for a particular purpose, first consider creating a UDF because
running it requires less system overhead than using a custom tag.


For more information
For more information on user-defined functions, see "Writing and Calling User-Defined Functions" on page 153.


Using ColdFusion components
ColdFusion components (CFCs) are ColdFusion templates that contain related functions and arguments that each
function accepts. The CFC contains the CFML tags necessary to define its functions and arguments and return a value.
ColdFusion components are saved with a .cfc extension.

CFCs combine the power of objects with the simplicity of CFML. By combining related functions into a single unit,
they provide an object or class shell from which functions can be called.

ColdFusion components can make their data private, so that it is available to all functions (also called methods) in the
component, but not to any application that uses the component.

ColdFusion components have the following features:

* They are designed to provide related services in a single unit.

* They can provide web services and make them available over the Internet.

* They can provide ColdFusion services that Flash clients can call directly.

* They have several features that are familiar to object-oriented programmers, including data hiding, inheritance,
   packages, and introspection.


Recommended uses
Consider using ColdFusion components when doing the following:

* Creating web services. (To create web services in ColdFusion, you must use components.)

* Creating services that are callable by Flash clients.

* Creating libraries of related functions, particularly if they must share data.

* Using integrated application security mechanisms based on roles and the requestor location.

* Developing code in an object-oriented manner, in which you use methods on objects and can create objects that
   extend the features of existing objects.


For more information
For more information on using ColdFusion components, see "Building and Using ColdFusion Components" on
page 177




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   150
Building Blocks of ColdFusion Applications




Using custom CFML tags
Custom tags written in CFML behave like ColdFusion tags. They can do all of the following:

* Take arguments.

* Have tag bodies with beginning and ending tags.

* Do specific processing when ColdFusion encounters the beginning tag.

* Do processing that is different from the beginning tag processing when ColdFusion encounters the ending tag.

* Have any valid ColdFusion page content in their bodies, including both ColdFusion built-in tags and custom tags
   (referred to as nested tags), or even JSP tags or JavaScript.

* Be called recursively; that is, a custom tag can, if designed properly, call itself in the tag body.

* Return values to the calling page in a common scope or the Variables scope of the calling page, but custom tags do
   not return values directly, the way functions do.

Although a custom tag and a ColdFusion page that you include using the cfinclude tag are both ColdFusion pages,
they differ in how they are processed. When a page calls a custom tag, it hands processing off to the custom tag page
and waits until the custom tag page completes. When the custom tag finishes, it returns processing (and possibly data)
to the calling page; the calling page can then complete its processing. The following image shows this process. The
arrows indicate the flow of ColdFusion processing the pages.

                                           <cf IsDened("Attributes.Type")>
    <html>                                   <cf Attributes.Type IS "Date">
    <head>                                      <cfoutput>#DateFormat(Now())#<cfoutput>
    <title>Title</title>                     <cfelseif Attributes.Type IS "Time">
    </head>                                     <cfoutput>#TimeFomat(Now())#<cfoutput>
    <body>                                   <cfelse>
    <h3>The date is:</h3>                       <cfoutput>#Now()#<cfoutput>
                                             </cf>
    <cf_now>                               <cfelse>
                                             <cfoutput>#Now()#</cfoutput>
    </body>                                </cf>
    </html>




          MyPage.cfm                                          Now.cfm


Calling custom CFML tags
Unlike built-in tags, you can run custom CFML tags in the following three ways:

* Call a tag directly.

* Call a tag using the cfmessagebox tag.

* Use the cfimport tag to import a custom tag library directory.

To call a CFML custom tag directly, precede the filename with cf_, omit the .cfm extension, and place the name in
angle brackets (<>). For example, use the following line to call the custom tag defined by the file mytag.cfm:

<cf_myTag>

If your tag takes a body, end it with the same tag name preceded with a forward slash (/), as follows:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    151
Building Blocks of ColdFusion Applications




</cf_myTag>

For information on using the cfmodule and cfimport tags to call custom CFML tags, see "Creating and Using
Custom CFML Tags" on page 208.


Recommended uses
ColdFusion custom tags let you abstract complex code and programming logic into simple units. These tags let you
maintain a CFML-like design scheme for your code. You can easily distribute your custom tags and share tags with
others. For example, the ColdFusion Developer Exchange includes a library of custom tags that perform a wide variety
of often-complex jobs; see www.adobe.com/go/learn_cfu_exchange_en.

Consider using CFML custom tags in the following circumstances:

* You need a tag-like structure, which has a body and an end tag, with the body contents changing from invocation
   to invocation.

* You want to associate specific processing with the beginning tag, the ending tag, or both tags.

* To use a logical structure in which the tag body uses "child" tags or subtags. This structure is like the cfform tag,
   which uses subtags for the individual form fields.

* You do not need a function format in which the calling code uses a direct return value.

* Your code must be recursive.

* Your functionality is complex.

* To distribute your code in a convenient form to others.

If you can create either a UDF or a custom CFML tag for a purpose, first consider creating a UDF because running it
requires less system overhead than using a custom tag.


For more information
For more information on custom CFML tags, see "Creating and Using Custom CFML Tags" on page 208.


Using CFX tags
ColdFusion Extension (CFX) tags are custom tags that you write in Java or C++. Generally, you create a CFX tag to do
something that is not possible in CFML. CFX tags also let you use existing Java or C++ code in your ColdFusion
application. Unlike CFML custom tags, CFX tags cannot have bodies or ending tags.

CFX tags can return information to the calling page in a page variable or by writing text to the calling page.

CFX tags can do the following:

* Have any number of custom attributes.

* Create and manipulate ColdFusion queries.

* Dynamically generate HTML that your page returns to the client.

* Set variables within the ColdFusion page from which they are called.

* Throw exceptions that result in standard ColdFusion error messages.


Calling CFX tags
To use a CFX tag, precede the class name with cfx_ and place the name in angle brackets. For example, use the
following line to call the CFX tag defined by the MyCFXClass class and pass it one attribute.

<cfx_MyCFXClass myArgument="arg1">



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     152
Building Blocks of ColdFusion Applications




Recommended uses
CFX tags provide one way of using C++ or Java code. However, you can also create Java classes and COM objects and access
them using the cfobject tag. CFX tags, however, provide some built-in features that the cfobject tag does not have:

* CFX tags are easier to call in CFML code. You use CFX tags directly in CFML code as you would any other tag, and
   you can pass arguments using a standard tag format.

* ColdFusion provides predefined classes for use in your Java or C++ code that facilitate CFX tag development. These
   classes include support for request handling, error reporting, and query management.

CFX tags are useful in the following circumstances:

* You already have existing application functionality written in C++ or Java that you want to incorporate into your
   ColdFusion application.

* You cannot build the functionality you need using ColdFusion elements.

* You want to provide the new functionality in a tag format, as opposed to using the cfobject tag to import native
   Java or COM objects.

* You want to use the Java and C++ classes provided by ColdFusion for developing your CFX code.


For more information
For more information on CFX tags, see "Building Custom CFXAPI Tags" on page 224.


Selecting among ColdFusion code reuse methods
The following table lists common reasons to employ code reuse methods and indicates the techniques to consider for
each purpose. The letter P indicates that the method is preferred. (There can be more than one preferred method.) The
letter A means that the method provides an alternative that is useful in some circumstances.

This table does not include CFX tags. You use CFX tags only when it is best to code your functionality in C++ or Java.
For more information about using CFX tags, see "Using CFX tags" on page 151.


 Purpose                                        cfinclude tag     Custom tag            UDF         Component

 Provide code, including CFML, HTML, and static P
 text, that must be used in multiple pages.

 Deploy headers and footers.                    P

 Include one page in another page.              P

 Divide pages into smaller units.               P

 Use variables from a calling page.             A                 P                     P

 Implement code that uses recursion.                              P                     P           P

 Distribute your code to others.                                  P                     P           P

 Operate on a body of HTML or CFML text.                          P

 Use subtags.                                                     P

 Provide a computation, data manipulation, or                     A                     P
 other procedure.

 Provide a single functional element that takes                   A                     P
 any number of input values and returns a
 (possibly complex) result.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 153
Building Blocks of ColdFusion Applications




 Purpose                                       cfinclude tag     Custom tag          UDF          Component

 Use variables with names that change from use                   A                   P            P
 to use.

 Provide accessibility from Flash clients.                       A                   A            P

 Use built-in user security features.                                                A            P

 Encapsulate multiple related functions and                                                       P
 properties.

 Create web services.                                                                             P

 Implement object-oriented coding                                                                 P
 methodologies.




Writing and Calling User-Defined Functions

Creating custom functions for algorithms or procedures that you call frequently lets you organize and reuse the
functions in your Adobe ColdFusion application pages.


About user-defined functions
You can create your own custom functions, known as user-defined functions, or UDFs. You then use them in your
application pages the same way you use standard ColdFusion functions. You can also organize functions you create by
grouping related functions into ColdFusion components. For more information, see "Building and Using ColdFusion
Components" on page 177.

When you create a function for an algorithm or procedure that you use frequently, you can then use the function
wherever you require the procedure. If you must change the procedure, you change only one piece of code. You can
use your function anywhere that you can use a ColdFusion expression: in tag attributes, between number (#) signs in
output, and in CFScript code. Typical uses of UDFs include, but are not limited to the following:

* Data manipulation routines, such as a function to reverse an array

* String and date/time routines, such as a function to determine whether a string is a valid IP address

* Mathematical calculation routines, including standard trigonometric and statistical operations or calculating loan
    amortization

* Routines that call functions externally, for example using COM or CORBA, including routines to determine the
    space available on a Windows file system drive

For information about selecting among user-defined functions, ColdFusion components, and custom tags, see
"Creating ColdFusion Elements" on page 146.

Note: The Common Function Library Project at www.cflib.org is an open source collection of CFML user-defined
functions.


Creating user-defined functions
Before you create a UDF, determine where you want to define it, and whether you want to use CFML or CFScript to
create it.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   154
Building Blocks of ColdFusion Applications




Determining where to create a user-defined function
You can define a function in the following places:

* In a ColdFusion component. If you organize your functions in ColdFusion components, you use the functions as
   described in "Using ColdFusion components" on page 189.

* On the page where it is called. You can even define it below the place on the page where it is called, but this poor
   coding practice can result in confusing code.

* On a page that you include using a cfinclude tag. The cfinclude tag must be executed before the function gets
   called. For example, you can define all the functions for your application's on a single page and place a cfinclude
   tag at the top of pages that use the functions.

* On any page that places the function name in a scope common with the page on which you call the function. For
   more information on UDF scoping, see "Specifying the scope of a function" on page 173.

* On the Application.cfc or Application.cfm page. For more information, see "Designing and Optimizing a
   ColdFusion Application" on page 235.

For recommendations on selecting where you define functions, see the sections "Using Application.cfm and function
include files" on page 173 and "Specifying the scope of a function" on page 173.


About creating functions using CFScript
You use the function statement to define the function in CFScript. CFScript function definitions have the following
features and limitations:

* The function definition syntax is familiar to anyone who uses JavaScript or most programming languages.

* CFScript is efficient for writing business logic, such as expressions and conditional operations.

* CFScript function definitions cannot include CFML tags.

The following is a CFScript definition for a function that returns a power of 2:

 <cfscript>
function twoPower(exponent) {
     return 2^exponent;
}
</cfscript>

For more information on how to use CFScript to define a function, see "Defining functions in CFScript" on page 154.


Defining functions in CFScript
You define functions using CFScript in a similar manner defining JavaScript functions. You can define multiple
functions in a single CFScript block.

Note: For more information on using CFScript, see "Extending ColdFusion Pages with CFML Scripting" on page 106.


CFScript function definition syntax
A CFScript function definition has the following syntax:

 function functionName( [argName1[, argName2...]] )
     {
           CFScript Statements
     }

The following table describes the function variables:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                               155
Building Blocks of ColdFusion Applications




Function variable               Description

functionName                    The name of the function. You cannot use the name of a standard ColdFusion function or any name that
                                starts with "cf". You cannot use the same name for two different function definitions. Function names
                                cannot include periods.

argName1...                     Names of the arguments required by the function. The number of arguments passed into the function
                                must equal or exceed the number of arguments in the parentheses at the start of the function definition.
                                If the calling page omits any of the required arguments, ColdFusion generates a mismatched argument
                                count error.


The body of the function definition must be in curly brackets, even if it is empty.

The following two statements are allowed only in function definitions:


Statement                                      Description

varvariableName = expression;                  Creates and initializes a variable that is local to the function (function variable). This variable
                                               has meaning only inside the function and is not saved between calls to the function. It has
                                               precedence in the function body over any variables with the same name that exist in any
                                               other scopes. You never prefix a function variable with a scope identifier, and the name
                                               cannot include periods. The initial value of the variable is the result of evaluating the
                                               expression. The expression can be any valid ColdFusion expression, including a constant or
                                               even another UDF.

                                               All var statements must be at the top of the function declaration, before any other
                                               statements. Initialize all variables when you declare them. You cannot use the same name
                                               for a function variable and an argument.

                                               Each var statement can initialize only one variable.

                                               Use the var statement to initialize all function-only variables, including loop counters and
                                               temporary variables.

returnexpression;                              Evaluates expression (which can be a variable), returns its value to the page that called the
                                               function, and exits the function. You can return any ColdFusion variable type.



A simple CFScript example
The following example function adds the two arguments and returns the result:

 <cfscript>
function Sum(a,b) {
     var sum = a + b;
     return sum;
}
</cfscript>

In this example, a single line declares the function variable and uses an expression to set it to the value that it returns.
This function can be simplified so that it does not use a function variable, as follows:

 function MySum(a,b) {Return a + b;}

Always use curly brackets around the function definition body, even if it is a single statement.

Note: ColdFusion does not COPY any of the function arguments into the local scope of a function. However, if an
unscoped variable is called, it is searched first in argument scope and then local scope.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  156
Building Blocks of ColdFusion Applications




About creating functions by using tags
You use the cffunction tag to define a UDF in CFML. The cffunction tag syntax has the following features and
limitations:

* Developers who have a background in CFML or HTML, but no scripting or programming experience are more
   familiar with the syntax.

* You can include any ColdFusion tag in your function definition. Therefore, you can create a function, for example,
   that accesses a database.

* You can embed CFScript code inside the function definition.

* The cffunction tag provides attributes that enable you to easily limit the execution of the tag to authorized users
   or specify how the function can be accessed.

The following code uses the cffunction tag to define the exponentiation function:

 <cffunction name="twoPower" output=True>
     <cfargument name="exponent">
     <cfreturn 2^exponent>
</cffunction>

For more information on how to use the cffunction tag to define a function, see "Defining functions by using the
cffunction tag" on page 156.


Defining functions by using the cffunction tag
The cffunction and cfargument tags let you define functions in CFML without using CFScript.

For information on ColdFusion components, see "Building and Using ColdFusion Components" on page 177. For
more information on the cffunction tag, see the CFML Reference.


The cffunction tag function definition format
A cffunction tag function definition has the following format:

 <cffunction name="functionName" [returnType="type" roles="roleList"
           access="accessType" output="Boolean"]>
     <cfargument name="argumentName" [Type="type" required="Boolean"
           default="defaultValue">]
     <!--- Function body code goes here. --->
     <cfreturn expression>
</cffunction>

where brackets ([]) indicate optional arguments. You can have any number of cfargument tags.

The cffunction tag specifies the name you use when you call the function. You can optionally specify other function
characteristics, as the following table describes:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                            157
Building Blocks of ColdFusion Applications




 Attribute                 Description

 name                      The function name.

 returnType                (Optional) The type of data that the function returns. The valid standard type names are: any, array, binary,
                           Boolean, date, guid, numeric, query, string, struct, uuid, variableName, xml, and void. If you specify any other
                           name, ColdFusion requires the argument to be a ColdFusion component with that name.

                           ColdFusion throws an error if you specify this attribute and the function tries to return data with a type that
                           ColdFusion cannot automatically convert to the one you specified. For example, if the function returns the result
                           of a numeric calculation, a returnType attribute of string or numeric is valid, but array is not.

 roles                     (Optional) A comma-delimited list of security roles that can run this method. If you omit this attribute, ColdFusion
                           does not restrict user access to the function.

                           If you use this attribute, the function executes only if the current user is logged in using the cfloginuser tag
                           and is a member of one or more of the roles specified in the attribute. Otherwise, ColdFusion throws an
                           unauthorized access exception. For more information on user security, see "Securing Applications" on page 339.

 output                    (Optional) Determines how ColdFusion processes displayable output in the function body.

                           If you do not specify this option, ColdFusion treats the body of the function as normal CFML. As a result, text and
                           the result of any cfoutput tags in the function definition body are displayed each time the function executes.

                           If you specify true or yes, the body of the function is processed as if it is in a cfoutput tag. ColdFusion displays
                           variable values and expression results if you surround the variables and expressions with number signs (#).

                           If you specify false or no., the function is processed as if it is in a cfsilent tag. The function does not display
                           any output. The code that calls the function is responsible for displaying any function results.


Use cfargument tags for required function arguments. All cfargument tags must precede any other CFML code in a
cffunction tag body. Therefore, place the cfargument tags immediately following the cffunction opening tag. The
cfargument tag takes the following attributes:


 Attribute                 Description

 name                      The argument name.

 type                      (Optional) The data type of the argument. The type of data that is passed to the function. The valid standard type
                           names are any, array, binary, Boolean, date, guid, numeric, query, string, struct, uuid, and variableName. If you
                           specify any other name, ColdFusion requires the argument to be a ColdFusion component with that name.

                           ColdFusion throws an error if you specify this attribute and the function is called with data of a type that
                           ColdFusion cannot automatically convert to the one you specified. For example, if the argument type attribute
                           is numeric, you cannot call the function with an array.

 required                  (Optional) A Boolean value that specifies whether the argument is required. If set to true and the argument is
                           omitted from the function call, ColdFusion throws an error. The default value is false. The required attribute is
                           not required if you specify a default attribute.

                           Because you do not identify arguments when you call a function, all cfargument tags that specify required
                           arguments must precede any cfargument tags that specify optional arguments in the cffunction definition.

 default                   (Optional) The default value for an optional argument if no argument value is passed. If you specify this attribute,
                           ColdFusion ignores the required attribute.


Note: The cfargument tag is not required for optional arguments. This feature is useful if a function can take an
indeterminate number of arguments. If you do not use the cfargument tag for an optional argument, reference it by
using its position in the Arguments scope array. For more information see "Using the Arguments scope as an array" on
page 162.




                                                       

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   158
Building Blocks of ColdFusion Applications




Using a CFML tag in a user-defined function
The most important advantage of using the cffunction tag over defining a function in CFScript is that you can
include CFML tags in the function. Thus, UDFs can encapsulate activities, such as database lookups, that require
ColdFusion tags. Also, you can use the cfoutput tag to display output on the calling page with minimal coding.

Note: To improve performance, avoid using the cfparam tag in ColdFusion functions. Instead, use the cfset tag.

The following example function looks up and returns an employee department ID. It takes one argument, the
employee ID, and looks up the corresponding department ID in the cfdocexamples Employee table:

 <cffunction name="getDeptID" >
     <cfargument name="empID" required="true" type="numeric">
     <cfset var cfdocexamples="">
     <cfquery dataSource="cfdocexamples" name="deptID">
           SELECT Dept_ID
           FROM Employee
           WHERE Emp_ID = #empID#
     </cfquery>
     <cfreturn deptID.Dept_ID>
</cffunction>


Rules for function definitions
The following rules apply to functions that you define using CFScript or the cffunction tag:

* The function name must be unique. It must be different from any existing variable, or UDF, except that you can
   use the ColdFusion advanced security function names.

* You can have a user-defined function with the same name as a built-in function for a CFC but not for CFM.

* You cannot use the following names to create user-defined functions:

   * writedump

   * writelog

   * location

   * throw

   * trace

* The function name must not start with the letters cf in any form. (For example, CF_MyFunction, cfmyFunction,
   and cfxMyFunction are not valid UDF names.)

* You cannot redefine or overload a function. If a function definition is active, ColdFusion generates an error if you
   define a second function with the same name.

* You cannot nest function definitions; that is, you cannot define one function inside another function definition.

* The function can be recursive, that is, the function definition body can call the function.

* The function does not have to return a value.

You can use tags or CFScript to create a UDF. Each technique has advantages and disadvantages.


Calling user-defined functions
You can call a function anywhere that you can use an expression, including in number signs (#) in a cfoutput tag, in
a CFScript, or in a tag attribute value. One function can call another function, and you can use a function as an
argument to another function.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   159
Building Blocks of ColdFusion Applications




You can call a UDF in two ways:

* With unnamed, positional arguments, as you would call a built-in function

* With named arguments, as you would use attributes in a tag

You can use either technique for any function. However, if you use named arguments, use the same argument names
to call the function as you use to define the function. You cannot call a function with a mixture of named and unnamed
arguments.

One example of a user-defined function is a TotalInterest function that calculates loan payments based on a principal
amount, annual percentage, and loan duration in months. (The definition of this function, see "A user-defined
function example" on page 171). You can call the function without argument names on a form action page, as follows:

 <cfoutput>
     Interest: #TotalInterest(Form.Principal, Form.Percent, Form.Months)#
</cfoutput>

You can call the function with argument names, as follows:

 <cfoutput>
Interest: #TotalInterest(principal=Form.Principal, annualPercent=Form.Percent,
     months=Form.Months)#
</cfoutput>



Working with arguments and variables in functions

Good argument naming practice
Use an argument name that represents its use. For example, the following code is unlikely to result in confusion:

 <cfscript>
     function SumN(Addend1,Addend2)
     { return Addend1 + Addend2; }
</cfscript>
<cfset x = 10>
<cfset y = 12>
<cfoutput>#SumN(x,y)#</cfoutput>

The following, similar code is more likely to result in programming errors:

 <cfscript>
     function SumN(x,y)
     { return x + y; }
</cfscript>
<cfset x = 10>
<cfset y = 12>
<cfoutput>#SumN(x,y)#<cfoutput>


Passing arguments
ColdFusion passes the following data types to the function by value:

* Integers

* Real numbers

* Strings (including lists)

* Date-time objects




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                            160
Building Blocks of ColdFusion Applications




* Arrays

As a result, any changes that you make in the function to these arguments do not affect the variable that was used to
call the function, even if the calling code is on the same ColdFusion page as the function definition.

ColdFusion passes queries, structures, and external objects such as COM objects into the function by reference. As a
result, any changes to these arguments in the function also change the value of the variable in the calling code.

For an example of the effects of passing arguments, see "Passing complex data" on page 160.


Passing complex data
Structures, queries, and complex objects such as COM objects are passed to UDFs by reference, so the function uses
the same copy of the data as the caller. Arrays are passed to user-defined functions by value, so the function gets a new
copy of the array data, and the array in the calling page is unchanged by the function. As a result, always handle arrays
differently from all other complex data types.


Passing structures, queries, and objects
For your function to modify the copy of a structure, query, or object, in the caller, pass the variable as an argument.
Because the function gets a reference to the structure in the caller, the caller variable reflects all changes in the function.
You do not have to return the structure to the caller. After the function returns, the calling page accesses the changed
data by using the structure variable that it passed to the function.

If you do not want a function to modify the copy of a structure, query, or object, in the caller, use the Duplicate
function to make a copy and pass the copy to the function.


Passing arrays
If you want your function to modify the caller's copy of the array, the simplest solution is to pass the array to the
function and return the changed array to the caller in the function return statement. In the caller, use the same
variable name in the function argument and return variable.

The following example shows how to directly pass and return arrays. In this example, the doubleOneDArray function
doubles the value of each element in a one-dimensional array.

 <cfscript>
//Initialize some variables
//This creates a simple array.
a=ArrayNew(1);
a[1]=2;
a[2]=22;
//Define the function.
function doubleOneDArray(OneDArray) {
      var i = 0;
      for ( i = 1; i LE arrayLen(OneDArray); i = i + 1)
           { OneDArray[i] = OneDArray[i] * 2; }
      return OneDArray;
}
//Call the function.
a = doubleOneDArray(a);
</cfscript>
<cfdump var="#a#">




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       161
Building Blocks of ColdFusion Applications




This solution is simple, but it is not always optimal:

* This technique requires ColdFusion to copy the entire array twice, once when you call the function and once when
   the function returns. Doing so is inefficient for large arrays and can reduce performance, particularly if the function
   is called frequently.

* You can use the return value for other purposes, such as a status variable.

If you do not use the return statement to return the array to the caller, you can pass the array as an element in a
structure and change the array values inside the structure. Then the calling page can access the changed data by using
the structure variable it passed to the UDF.

The following code shows how to rewrite the previous example using an array in a structure. It returns True as a status
indicator to the calling page and uses the structure to pass the array data back to the calling page.

 <cfscript>
//Initialize some variables.
//This creates a simple array as an element in a structure.
arrayStruct=StructNew();
arrayStruct.Array=ArrayNew(1);
arrayStruct.Array[1]=2;
arrayStruct.Array[2]=22;
//Define the function.
function doubleOneDArrayS(OneDArrayStruct) {
     var i = 0;
     for ( i = 1; i LE arrayLen(OneDArrayStruct.Array); i = i + 1)
           { OneDArrayStruct.Array[i] = OneDArrayStruct.Array[i] * 2; }
     return True;
}
//Call the function.
Status = doubleOneDArrayS(arrayStruct);
WriteOutput("Status: " & Status);
</cfscript>
</br>
<cfdump var="#arrayStruct#">

Use the same structure element name for the array (in this case Array) in the calling page and the function.


About the Arguments scope
All function arguments exist in their own scope, the Arguments scope.

The Arguments scope exists for the life of a function call. When the function returns, the scope and its variables are
destroyed.

However, destroying the Argument scope does not destroy variables, such as structures or query objects, that
ColdFusion passes to the function by reference. The variables on the calling page that you use as function arguments
continue to exist; if the function changes the argument value, the variable in the calling page reflects the changed value.

The Arguments scope is special, in that you can treat the scope as either an array or a structure. This dual nature of the
Arguments scope is useful because it makes it easy to use arguments in any of the following circumstances:

* You define the function using CFScript.

* You define the function using the cffunction tag.

* You pass arguments using argument name=value format.

* You pass arguments as values only.

* The function takes optional, undeclared arguments.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  162
Building Blocks of ColdFusion Applications




The contents of the Arguments scope
The following rules apply to the Arguments scope and its contents:

* The scope contains all the arguments passed into a function.

* If you use cffunction to define the function, the scope always contains an entry "slot" for each declared argument,
   even if you do not pass the argument to the function when you call it. If you do not pass a declared (optional)
   argument, the scope entry for that argument is empty.

   When you call a function that you defined using CFScript, Pass the function a value for each argument declared in
   the function definition. Therefore, the Arguments scope for a CFScript call does not have empty slots.

The following example shows these rules. Assume that you have a function declared, as follows:

 <cffunction name="TestFunction">
     <cfargument name="Arg1">
     <cfargument name="Arg2">
</cffunction>

You can call this function with a single argument, as in the following line:

 <cfset TestFunction(1)>

The resulting Arguments scope looks like the following:


 As an array                                        As a structure

 Entry                      Value                   Entry                        Value

 1                          1                       Arg1                         1

 2                          undefined               Arg2                         undefined


In this example, the following functions return the value 2 because the scope contains two defined arguments:

ArrayLen(Arguments)
StructCount(Arguments)

However, the following tests return the value false, because the contents of the second element in the Arguments
scope is undefined.

 Isdefined("Arguments.Arg2")
testArg2 = Arguments[2]>
Isdefined("testArg2")

Note: The IsDefined function does not test the existence of array elements. Use the function ArrayContains to search
the array elements.


Using the Arguments scope as an array
The following rules apply to referencing Arguments scope as an array:

* If you call the function using unnamed arguments, the array index is the position of the argument in the function
   call.

* If you use names to pass the arguments, the array indexes correspond to the order in which the arguments are
   declared in the function definition.

* If you use names to pass arguments, and do not pass all the arguments defined in the function, the Arguments array
   has an empty entry at the index corresponding to the argument that was not passed. This rule applies only to
   functions created using the cffunction tag.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     163
Building Blocks of ColdFusion Applications




* If you use a name to pass an optional argument that is not declared in the function definition, the array index of the
   argument is the sum of the following:

   * The number of arguments defined with names in the function.

   * The position of the optional argument among the arguments passed in that do not have names defined in the
       function.

   However, using argument names in this manner is not good programming practice because you cannot ensure that
   you always use the same optional argument names when calling the function.

To demonstrate these rules, define a simple function that displays the contents of its Arguments array and call the
function with various argument combinations, as the following example shows:

 <cffunction name="TestFunction" >
     <cfargument name="Arg1">
     <cfargument name="Arg2">
     <cfloop index="i" from="1" to="#ArrayLen(Arguments)#">
           <cfoutput>Argument #i#: #Arguments[i]#<br></cfoutput>
     </cfloop>
</cffunction>


<strong>One Unnamed argument</strong><br>
<cfset TestFunction(1)>
<strong>Two Unnamed arguments</strong><br>
<cfset TestFunction(1, 2)>
<strong>Three Unnamed arguments</strong><br>
<cfset TestFunction(1, 2, 3)>
<strong>Arg1:</strong><br>
<cfset TestFunction(Arg1=8)>
<strong>Arg2:</strong><br>
<cfset TestFunction(Arg2=9)>
<strong>Arg1=8, Arg2=9:</strong><br>
<cfset TestFunction(Arg1=8, Arg2=9)>
<strong>Arg2=6, Arg1=7</strong><br>
<cfset TestFunction(Arg2=6, Arg1=7)>
<strong>Arg1=8, Arg2=9, Arg3=10:</strong><br>
<cfset TestFunction(Arg1=8, Arg2=9, Arg3=10)>
<strong>Arg2=6, Arg3=99, Arg1=7</strong><br>
<cfset TestFunction(Arg2=6, Arg3=99, Arg1=7)>

Note: Although you can use the Arguments scope as an array, the IsArray(Arguments) function always returns false
and the cfdump tag displays the scope as a structure.


Using the Arguments scope as a structure
The following rule applies when referencing Arguments scope as a structure:

* Use the argument names as structure keys. For example, if your function definition includes a Principal argument,
   reference the argument as Arguments.Principal.

   The following rules are also true, but avoid writing code that uses them. To ensure program clarity, only use the
   Arguments structure for arguments that you name in the function definition. Use the Arguments scope as an array
   for optional arguments that you do not declare in the function definition.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     164
Building Blocks of ColdFusion Applications




* If you do not name an optional argument in the function definition, but do use a name for it in the function call,
   use the name specified in the function call For example, if you have an unnamed optional argument and call the
   function using the name myOptArg for the argument, you can reference the argument as Arguments.myOptArg
   in the function body. This usage, however, is poor programming practice, as it makes the function definition
   contents depend on variable names in the code that calls the function.


Using the Arguments scope in CFScript
A function can have optional arguments that you do not have to specify when you call the function. To determine the
number of arguments passed to the function, use the following function:

 ArrayLen(Arguments)

When you define a function using CFScript, the function must use the Arguments scope to retrieve the optional
arguments. For example, the following SumN function adds two or more numbers together. It requires two arguments
and supports any number of additional optional arguments. You can reference the first two, required, arguments as
Arg1 and Arg2 or as Arguments[1] and Arguments[2]. Access the third, fourth, and any additional optional
arguments as Arguments[3], Arguments[4], and so on

 function SumN(Arg1,Arg2) {
     var arg_count = ArrayLen(Arguments);
     var sum = 0;
     var i = 0;
     for( i = 1 ; i LTE arg_count; i = i + 1 )
     {
           sum = sum + Arguments[i];
     }
     return sum;
}

With this function, any of the following function calls are valid:

 SumN(Value1, Value2)
SumN(Value1, Value2, Value3)
SumN(Value1, Value2, Value3, Value4)

and so on.

The code never uses the Arg1 and Arg2 argument variables directly, because their values are always the first two
elements in the Arguments array and it is simpler to step through the array. Specifying Arg1 and Arg2 in the function
definition ensures that ColdFusion generates an error if you pass the function one or no arguments.

Note: Avoid referring to a required argument in the body of a function by both the argument name and its place in the
Arguments scope array or structure, as doing so can be confusing and makes it easier to introduce errors.


Using the Arguments scope in cffunction definitions
When you define a function using the cffunction tag, you generally reference the arguments directly by name if all
arguments are named in the cfargument tags. If you do use the Arguments scope identifier, follow the rules listed in
"About the Arguments scope" on page 161.

For more information on using the Arguments scope in functions defined using CFScript, see "Using the Arguments
scope in CFScript" on page 164.


Function-only variables
In addition to the Arguments scope, each function can have variables that exist only inside the function, and are not
saved between times the function gets called. As soon as the function exits, all the variables in this scope are removed.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     165
Building Blocks of ColdFusion Applications




In CFScript, you create function-only variables with the var statement. Unlike other variables, you never prefix
function-only variables with a scope name.


Using function-only variables
Make sure to use the var statement in CFScript UDFs to declare all function-specific variables, such as loop indexes
and temporary variables that are required only for the duration of the function call. Doing so ensures that these
variables are available inside the function only, and makes sure that the variable names do not conflict with the names
of variables in other scopes. If the calling page has variables of the same name, the two variables are independent and
do not affect each other.

For example, if a ColdFusion page has a cfloop tag with an index variable i, and the tag body calls a CFScript UDF that
also has a loop with a function-only index variable i, the UDF does not change the value of the calling page loop index,
and the calling page does not change the UDF index. So you can safely call the function inside the cfloop tag body.

In general, use the var statement to declare all UDF variables, other than the function arguments or shared-scope
variables, that you use only inside CFScript functions. Use another scope, however, if the value of the variable must
persist between function calls; for example, for a counter that the function increments each time it is called.


Referencing caller variables
A function can use and change any variable that is available in the calling page, including variables in the caller's
Variables (local) scope, as if the function was part of the calling page. For example, if you know that the calling page
has a local variable called Customer_name (and no function scope variable named Customer_name exists) the
function can read and change the variable by referring to it as Customer_name or (using better coding practice)
Variables.Customer_name. Similarly, you can create a local variable inside a function and then use it anywhere in the
calling page after the function call. You cannot use the variable before you call the function.

However, generally avoid using the caller's variables directly inside a function. Using the caller's variables creates a
dependency on the caller. Ensure that the code outside the function uses the same variable names as the function.
Doing so can become difficult if you call the function from many pages.

You can avoid these problems by using only the function arguments and the return value to pass data between the
caller and the function. Do not reference calling page variables directly in the function. As a result, you can use the
function anywhere in an application (or even in multiple applications), without concern for the calling code variables.

As with other programming practices, valid exceptions to this recommendation exist. For example, you can do any of
the following:

* Use a shared scope variable, such as an Application or Session scope counter variable.

* Use the Request scope to store variables used in the function. For more information, see "Using the Request scope
   for static variables and constants" on page 174.

* Create context-specific functions that work directly with caller data if you always synchronize variable names.

Note: If your function must directly change a simple variable in the caller (one that is not passed to the function by
reference), you can place the variable inside a structure argument.


Using arguments
Function arguments can have the same names, but different values, as variables in the caller. Avoid such uses for
clarity, however.

The following rules apply to argument persistence:

* Because ColdFusion passes simple variable and array arguments by value, their names and values exist only while
   the function executes.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      166
Building Blocks of ColdFusion Applications




* Because ColdFusion passes structures, queries, and objects such as COM objects by reference, the argument name
   exists only while the function executes, but the underlying data persists after the function returns and can be
   accessed by using the variable name of the caller. The variable name of the caller and the argument name can be
   different.

Note: If a function must use a variable from another scope that has the same name as a function-only variable, prefix the
external variable with its scope identifier, such as Variables or Form. (However, remember that using variables from
other scopes directly in your code is often poor practice.)


Handling errors in UDFs
ColdFusion provides several techniques to handle errors in UDFs:

* Display error messages directly in the function.

* Return function status information to the calling page.

* Use try/catch or cftry/cfcatch blocks and the cfthrow and cfrethrow tags to handle and generate exceptions.

The technique you use depends on the circumstances of your function and application and on your preferred
programming style. However, it is best for most functions to use the second or third technique, or a combination of
the two.


Displaying error messages
Your function can test for errors and use the WriteOutput function to display an error message directly to the user.
This method is useful for providing immediate feedback to users for simple input errors. You can use it independently
or in conjunction with either of the other two error-handling methods.

For example, the following variation on a "Hello world" function displays an error message if you do not enter a name
in the form:

 <cfform method="POST" action="#CGI.script_name#">
     <p>Enter your Name:&nbsp;
     <input name="name" type="text" hspace="30" maxlength="30">
     <input type="Submit" name="submit" value="OK">
</cfform>
<cfscript>
     function HelloFriend(Name) {
           if (Name is "") WriteOutput("You forgot your name!");
           else WriteOutput("Hello " & name &"!");
           return "";
     }
     if (IsDefined("Form.submit")) HelloFriend(Form.name);
</cfscript>


Reviewing the code
The following table describes the code:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   167
Building Blocks of ColdFusion Applications




Code                                                                   Description

<cfform method="POST" action="#CGI.script_name#">                      Creates a simple form requesting you to enter your name.
<p>Enter your Name:&nbsp;
<input name="name" type="text" hspace="30"                             Uses the script_name CGI variable to post to this page
maxlength="30">                                                        without specifying a URL.
<input type="Submit" name="submit" value="OK">
</cfform>                                                              If you do not enter a name, the form posts an empty string as
                                                                       the name field.

<cfscript>                                                             Defines a function to display "Hello name!" First, checks
function HelloFriend(Name) {                                           whether the argument is an empty string. If so, displays an
if (Name is "") WriteOutput("You forgot your            name!");
                                                                       error message.
else WriteOutput("Hello " & name &"!"); return "";
}                                                                      Otherwise displays the hello message.
if (IsDefined("Form.submit"))
HelloFriend(Form.name);                                                Returns the empty string. (The caller does not use the return
</cfscript>
                                                                       value). It is not necessary to use curly brackets around the if
                                                                       or else statement bodies because they are single statements.

                                                                       If this page has been called by submitting the form, calls the
                                                                       HelloFriend function. Otherwise, the page just displays the
                                                                       form.



Providing status information
In some cases, such as those where the function cannot provide a corrective action, the function cannot, or should not,
handle the error directly. In these cases, your function can return information to the calling page. The calling page
must handle the error information and act appropriately.

Consider the following mechanisms for providing status information:

* Use the return value to indicate the function status only. The return value can be a Boolean success/failure
   indicator. The return value can also be a status code, for example where 1 indicates success, and various failure types
   are assigned known numbers. With this method, the function must set a variable in the caller to the value of a
   successful result.

* Set a status variable that is available to the caller (not the return variable) to indicate success or failure and any
   information about the failure. With this method, the function can return the result directly to the caller. In this
   method, the function uses only the return value and structure arguments to pass the status back to the caller.

Each of these methods can have variants, and each has advantages and disadvantages. The technique that you use
depends on the type of function, the application in which you use it, and your coding style.

The following example, which modifies the function used in "A user-defined function example" on page 171, uses one
version of the status variable method. It provides two forms of error information:

* It returns -1, instead of an interest value, if it encounters an error. This value can serve as an error indicator because
   you never pay negative interest on a loan.

* It also writes an error message to a structure that contains an error description variable. Because the message is in
   a structure, it is available to both the calling page and the function.


The TotalInterest function
After changes to handle errors, the TotalInterest function looks like the following. Code that is changed from the
example in "A user-defined function example" on page 171 is in bold.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                            168
Building Blocks of ColdFusion Applications




<cfscript>
function TotalInterest(principal, annualPercent, months, status) {
     Var years = 0;
     Var interestRate = 0;
     Var totalInterest = 0;
     principal = trim(principal);
     principal = REReplace(principal,"[\$,]","","ALL");
     annualPercent = Replace(annualPercent,"%","","ALL");
     if ((principal LE 0) OR (annualPercent LE 0) OR (months LE 0)) {
           Status.errorMsg = "All values must be greater than 0";
           Return -1;
     }
     interestRate = annualPercent / 100;
     years = months / 12;
     totalInterest = principal*(((1+ interestRate)^years)-1);
     Return DollarFormat(totalInterest);
}
</cfscript>


Reviewing the code
The following table describes the code that has been changed or added to the previous version of this example. For a
description of the initial code, see "A user-defined function example" on page 171.


Code                                                                             Description

function TotalInterest(principal, annualPercent, months, status)                 The function now takes an additional
                                                                                 argument, a status structure. Uses a
                                                                                 structure for the status variable so that
                                                                                 changes that the function makes affect the
                                                                                 status structure in the caller.

if ((principal LE 0) OR (annualPercent LE 0) OR (months LE 0)) {                 Checks to make sure the principal, percent
Status.errorMsg = "All values must be greater than 0";                           rate, and duration are all greater than zero.
Return -1;
}                                                                                If any is not, sets the errorMsg key (the only
                                                                                 key) in the Status structure to a descriptive
                                                                                 string. Also, returns -1 to the caller and
                                                                                 exits the function without processing
                                                                                 further.



Calling the function
The code that calls the function now looks like the following. Code that is changed from the example in "A user-
defined function example" on page 171 is in bold.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   169
Building Blocks of ColdFusion Applications




<cfset status = StructNew()>
<cfset myInterest = TotalInterest(Form.Principal,
     Form.AnnualPercent,Form.Months, status)>
<cfif myInterest EQ -1>
     <cfoutput>
           ERROR: #status.errorMsg#<br>
     </cfoutput>
<cfelse>
     <cfoutput>
           Loan amount: #Form.Principal#<br>
           Annual percentage rate:
                 #Form.AnnualPercent#<br>
           Loan duration: #Form.Months# months<br>
           TOTAL INTEREST: #myInterest#<br>
     </cfoutput>
</cfif>


Reviewing the code
The following table describes the code that has been changed or added:


Code                                                        Description

<cfset status = StructNew()>                                Creates a structure to hold the function status.

<cfset myInterest = TotalInterest                           Calls the function. This time, the function requires four arguments,
(Form.Principal, Form.AnnualPercent,                        including the status variable.
Form.Months, status)>

<cfif myInterest EQ -1>                                     If the function returns -1, there must be an error. Displays the message
<cfoutput>                                                  that the function placed in the status.errorMsg structure key.
ERROR: #status.errorMsg#<br>
</cfoutput>

<cfelse>                                                    If the function does not return -1, it returns an interest value. Displays
<cfoutput>                                                  the input values and the function return value.
Loan amount: #Form.Principal#<br>
Annual percentage rate:
#Form.AnnualPercent#<br>
Loan duration: #Form.Months# months<br>
TOTAL INTEREST: #myInterst#<br>
</cfoutput>
</cfif>



Using exceptions
UDFs written in CFScript can handle exceptions using the try and catch statements. UDFs written using the
cffunction tag can use the cftry, cfcatch, cfthrow, and cfrethrow tags. Using exceptions corresponds to the way
many functions in other programming languages handle errors, and can be an effective way to handle errors. In
particular, it separates the functional code from the error-handling code, and it can be more efficient than other
methods at runtime, because it does not require testing and branching.

Exceptions in UDFs have the following two dimensions:

* Handling exceptions generated by running the UDF code

* Generating exceptions when the UDF identifies invalid data or other conditions that would cause errors if
   processing continued




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     170
Building Blocks of ColdFusion Applications




Handling exceptions in UDFs
Use try/catch blocks to handle exceptions in a UDF under the same conditions that any other ColdFusion application
uses try/catch blocks. These conditions are typically circumstances where the function uses an external resource, such
as a Java, COM, or CORBA object, a database, or a file. When possible, have your application prevent, rather than
catch, exceptions caused by invalid application data. For example, the application can prevent users from entering a
zero value for a form field that is used to divide another number, rather than handling exceptions generated by dividing
by zero.

When ColdFusion catches an exception, the function can use any of the following methods to handle the exception:

* If the error is recoverable (for example, if the problem is a database time-out where in some cases retrying resolves
   the issue), try to recover from the problem.

* Display a message, as described in "Displaying error messages" on page 166.

* Return an error status, as described in "Providing status information" on page 167.

* If the UDF is defined using the cffunction tag, throw a custom exception, or rethrow the exception so that it the
   calling ColdFusion page catches it. For more information on throwing and rethrowing exceptions, see "Handling
   runtime exceptions with ColdFusion tags" on page 287.


Generating exceptions in UDFs
If you define your function using the cffunction tag, you can use the cfthrow and cfrethrow tags to throw errors
to the page that called the function. You can use this technique whenever your UDF identifies an error, instead of
displaying a message or returning an error status. For example, the following code rewrites the example from
"Providing status information" on page 167 to use the cffunction tag and CFML, and to throw and handle an
exception if any of the form values are not positive numbers.

The lines that identify invalid data and throw the exception are in bold. The remaining lines are equivalent to the
CFScript code in the previous example. However, the code that removes unwanted characters must precede the error
checking code.

 <cffunction name="TotalInterest">
     <cfargument name="principal" required="Yes">
     <cfargument name="annualPercent" required="Yes">
     <cfargument name="months" required="Yes">
     <cfset var years = 0>
     <cfset var interestRate = 0>
     <cfset var totalInterest = 0>


     <cfset principal = trim(principal)>
     <cfset principal = REReplace(principal,"[\$,]","","ALL")>
     <cfset annualPercent = Replace(annualPercent,"%","","ALL")>


     <cfif ((principal LE 0) OR (annualPercent LE 0) OR (months LE 0))>
           <cfthrow type="InvalidData" message="All values must be greater than 0.">
     </cfif>


     <cfset interestRate = annualPercent / 100>
     <cfset years = months / 12>
     <cfset totalInterest = principal*
                (((1+ interestRate)^years)-1)>
     <cfreturn DollarFormat(totalInterest)>
</cffunction>

The code that calls the function and handles the exception looks like the following. The changed lines are in bold.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  171
Building Blocks of ColdFusion Applications




<cftry>
      <cfset status = StructNew()>
      <cfset myInterest = TotalInterest(Form.Principal, Form.AnnualPercent,
           Form.Months, status)>
      <cfoutput>
           Loan amount: #Form.Principal#<br>
           Annual percentage rate: #Form.AnnualPercent#<br>
           Loan duration: #Form.Months# months<br>
           TOTAL INTEREST: #myInterest#<br>
      </cfoutput>
<cfcatch type="InvalidData">
      <cfoutput>
           #cfcatch.message#<br>
      </cfoutput>
</cfcatch>

</cftry>


A user-defined function example
The following simple function takes a principal amount, an annual percentage rate, and a loan duration in months and
returns the total amount of interest paid over the period. You can optionally use the percent sign for the percentage
rate, and include the dollar sign and comma separators for the principal amount.

You could use the TotalInterest function in a cfoutput tag of a form's action page, as follows:

<cfoutput>
      Loan amount: #Form.Principal#<br>
      Annual percentage rate: #Form.AnnualPercent#<br>
      Loan duration: #Form.Months# months<br>
      TOTAL INTEREST: #TotalInterest(Form.Principal, Form.AnnualPercent, Form.Months)#<br>
</cfoutput>


Defining the function using CFScript
<cfscript>
function TotalInterest(principal, annualPercent, months) {
      Var years = 0;
      Var interestRate = 0;
      Var totalInterest = 0;
      principal = trim(principal);
      principal = REReplace(principal,"[\$,]","","ALL");
      annualPercent = Replace(annualPercent,"%","","ALL");
      interestRate = annualPercent / 100;
      years = months / 12;
      totalInterest = principal*(((1+ interestRate)^years)-1);
      Return DollarFormat(totalInterest);
}
</cfscript>


Reviewing the code
The following table describes the code:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               172
Building Blocks of ColdFusion Applications




Code                                                                   Description

function TotalInterest(principal, annualPercent, months) {             Starts the TotalInterest function definition.
                                                                       Requires three variables: the principal amount, the
                                                                       annual percentage rate, and the loan duration in
                                                                       months.

Var years = 0;                                                         Declares intermediate variables used in the function
Var interestRate = 0;                                                  and initializes them to 0. All var statements must
Var totalInterest = 0;
                                                                       precede the rest of the function code.

principal = trim(principal);                                           Removes any leading or trailing spaces from the
principal = REReplace(principal,"[\$,]","","ALL");                     principal argument. Removes any dollar sign ($) and
annualPercent = Replace(annualPercent,"%","","ALL");
                                                                       comma (,) characters from the principal argument to
interestRate = annualPercent / 100;
                                                                       get a numeric value.
years = months / 12;

                                                                       Removes any percent (%) character from the
                                                                       annualPercent argument to get a numeric value,
                                                                       then divides the percentage value by 100 to get the
                                                                       interest rate.

                                                                       Converts the loan from months to years.

totalInterest = principal*                                             Calculates the total amount of interest due. It is possible
(((1+ interestRate)^years)-1);                                         to calculate the value in the Return statement, but this
Return DollarFormat(totalInterest);
                                                                       example uses an intermediate totalInterest variable to
}
                                                                       make the code easier to read. Returns the result
                                                                       formatted as a US currency string.

                                                                       Ends the function definition.



Defining the function using the cffunction tag
The following code replaces CFScript statements with their equivalent CFML tags.

 <cffunction name="TotalInterest">
     <cfargument name="principal" required="Yes">
     <cfargument name="annualPercent" required="Yes">
     <cfargument name="months" required="Yes">
     <cfset var years = 0>
     <cfset var interestRate = 0>
     <cfset var totalInterest = 0>
     <cfset principal = trim(principal)>
     <cfset principal = REReplace(principal,"[\$,]","","ALL")>
     <cfset annualPercent = Replace(annualPercent,"%","","ALL")>
     <cfset interestRate = annualPercent / 100>
     <cfset years = months / 12>
     <cfset totalInterest = principal*
                (((1+ interestRate)^years)-1)>
     <cfreturn DollarFormat(totalInterest)>
</cffunction>



Using UDFs effectively
Many techniques help you use user-defined functions more effectively.


Using functions in ColdFusion component
In many cases, the most effective use of UDFs is within a CFC. For more information on CFCs, see "Building and Using
ColdFusion Components" on page 177.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                              173
Building Blocks of ColdFusion Applications




Using Application.cfm and function include files
Consider the following techniques for making your functions available to your ColdFusion pages:

* If you consistently call a small number of UDFs, consider putting their definitions on the Application.cfm page.

* If you call UDFs in only a few of your application pages, do not include their definitions in Application.cfm.

* If you use many UDFs, place their definitions on one or more ColdFusion pages that contain only UDFs. You can
   include the UDF definition page in any page that calls the UDFs.

The next section describes other techniques for making UDFs available to your ColdFusion pages.


Specifying the scope of a function
User-defined function names are essentially ColdFusion variables. ColdFusion variables are names for data. Function
names are names (references) for segments of CFML code. Therefore, like variables, functions belong to scopes.


About functions and scopes
Like ColdFusion variables, UDFs exist in a scope:

* When you define a UDF, ColdFusion puts it in the Variables scope.

* You can assign a UDF to a scope the same way you assign a variable to a scope, by assigning the function to a name
   in the new scope. For example, the following line assigns the MyFunc UDF to the Request scope:

    <cfset Request.MyFunc = Variables.MyFunc>

   You can now use the function from any page in the Request scope by calling Request.MyFunc.


Selecting a function scope
The following table describes the advantages and disadvantages of each function scope:


Scope                      Considerations

Application                Makes the function available across all invocations of the application. Access to UDFs in Application scope is
                           multithreaded and you can execute multiple copies of the UDF at one time.

Request                    Makes the function available for the life of the current HTTP request, including in all custom tags and nested
                           custom tags. This scope is useful if a function is used in a page and in the custom tags it calls, or in nested custom
                           tags.

Server                     Makes the function available to all pages on a single server. In most cases, this scope is not a good choice because
                           in clustered systems, it only makes the function available on a single server, and all code that uses the function
                           must be inside a cflock block.

Session                    Makes the function available to all pages during the current user session. This scope has no significant advantages
                           over the Application scope.



Using the Request scope
You can effectively manage functions that are used in application pages and custom tags by doing the following:

1 Define the functions on a function definitions page.

2 On the functions page, assign the functions to the request scope.

3 Use a cfinclude tag to include the function definition page on the application page, but do not include it on any
   custom tag pages.

4 Always call the functions using the request scope.




                                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      174
Building Blocks of ColdFusion Applications




This way you only include the functions once per request and they are available throughout the life of the request. For
example, create a myFuncs.cfm page that defines your functions and assigns them to the Request scope using syntax
such as the following:

 function MyFunc1(Argument1, Argument2)
{ Function definition goes here }
Request.MyFunc1 = MyFunc1

The application page includes the myFuncs.cfm page:

 <cfinclude template="myfuncs.cfm">

The application page and all custom tags (and nested custom tags) call the functions as follows:

 Request.MyFunc1(Value1, Value2)


Using the Request scope for static variables and constants
You can partially break the rule described in the section "Referencing caller variables" on page 165. Here, the function
defines variables in the Request scope. However, it is a specific solution to a specific issue, where the following
circumstances exist:

* Your function initializes a large number of variables.

* The variables have either of the following characteristics:

   * They must be static: only the function uses them, the function can change their values, and their values must
       persist from one invocation of the function to the next.

   * They are named constants; that is the variable value never changes.

* Your application page (and any custom tags) calls the function multiple times.

* You can assure that only the function uses the variable names.

In these circumstances, you can improve efficiency and save processing time by defining your function's variables in
the Request scope, rather than the Function scope. The function tests for the Request scope variables and initializes
them if they do not exist. In subsequent calls, the variables exist and the function does not reset them.

The NumberAsString function, written by Ben Forta and available from www.cflib.org, takes advantage of this
technique.


Using function names as function arguments
Because function names are ColdFusion variables, you can pass a function's name as an argument to another function.
This technique allows a function to use another function as a component. For example, a calling page can call a
calculation function, and pass it the name of a function that does some subroutine of the overall function.

This way, the calling page could use a single function for different specific calculations, such as calculating different
forms of interest. The initial function provides the framework, while the function whose name is passed to it can
implement a specific algorithm that the calling page requires.

The following simple example shows this use. The binop function is a generalized function that takes the name of a
function that performs a specific binary operation and two operands. The binop function simply calls the specified
function and passes it the operands. This code defines a single operation function, the sum function. A more complete
implementation would define multiple binary operations.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     175
Building Blocks of ColdFusion Applications




 <cfscript>
function binop(operation, operand1, operand2)
{ return (operation(operand1, operand2)); }
function sum(addend1, addend2)
{ return addend1 + addend2;}
x = binop(sum, 3, 5);
writeoutput(x);
</cfscript>


Handling query results using UDFs
When you call a UDF in the body of a tag that has a query attribute, such as a cfloop tag, any function argument that
is a query column name passes a single element of the column, not the entire column. Therefore, the function must
manipulate a single query element.

For example, the following code defines a function to combine a single first name and last name to make a full name.
It queries the cfdocexamples database to get the first and last names of all employees, and then it uses a cfoutput tag
to loop through the query and call the function on each row in the query.

 <cfscript>
function FullName(aFirstName, aLastName)
     { return aFirstName & " " & aLastName; }
</cfscript>


<cfquery name="GetEmployees" datasource="cfdocexamples">
     SELECT FirstName, LastName
     FROM Employee
</cfquery>


<cfoutput query="GetEmployees">
#FullName(FirstName, LastName)#<br>
</cfoutput>

You generally use functions that manipulate many rows of a query outside tags that loop over queries. Pass the query
to the function and loop over it inside the function. For example, the following function changes text in a query column
to uppercase. It takes a query name as an argument.

 function UCaseColumn(myquery, colName) {
     var currentRow = 1;
     for (; currentRow lte myquery.RecordCount; currentRow = currentRow + 1)
     {
           myquery[colName][currentRow] = UCase(myquery[colName][currentRow]);
     }
     Return "";
}

The following code uses a script that calls the UCaseColumn function to convert all the last names in the GetEmployees
query to uppercase. It then uses cfoutput to loop over the query and display the contents of the column.

 <cfscript>
     UCaseColumn(GetEmployees, "LastName");
</cfscript>
<cfoutput query="GetEmployees">
     #LastName#<br>
</cfoutput>




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    176
Building Blocks of ColdFusion Applications




Identifying and checking for UDFs
You can use the IsCustomFunction function to determine whether a name represents a UDF. The
IsCustomFunction function generates an error if its argument does not exist. As a result, ensure that the name exists
before calling the function, for example, by calling the IsDefined function. The following code shows this use:

 <cfscript>
if(IsDefined("MyFunc"))
     if(IsCustomFunction(MyFunc))
           WriteOutput("MyFunc is a user-defined function");
     else
           WriteOutput("Myfunc is defined but is NOT a user-defined function");
else
     WriteOutput("MyFunc is not defined");
</cfscript>

You do not surround the argument to IsCustomFunction in quotation marks, so you can use this function to
determine if function arguments are themselves functions.


Using the Evaluate function
If your user-defined function uses the Evaluate function on arguments that contain strings, Make sure that all
variable names you use as arguments include the scope identifier. Doing so avoids conflicts with function-only
variables.

The following example returns the result of evaluating its argument. It produces the expected results, the value of the
argument, if you pass the argument using its fully scoped name, Variables.myname. However, the function returns the
value of the function local variable if you pass the argument as myname, without the Variables scope identifier.

 <cfscript>
     myname = "globalName";
     function readname(name) {
           var myname = "localName";
           return (Evaluate(name));
     }
</cfscript>


<cfoutput>
<!--- This one collides with local variable name. --->
     The result of calling readname with myname is:
           #readname("myname")# <br>
<!--- This one finds the name passed in. --->
     The result of calling readname with Variables.myname is:
           #readname("Variables.myname")#
</cfoutput>


Using recursion
A recursive function is a function that calls itself. Recursive functions are useful when an algorithm that repeats the
same operation multiple times using the results of the preceding repetition can solve the problem. Factorial
calculation, used in the following example, is one case where recursion is useful. The Towers of Hanoi game is also
solved using a recursive algorithm.

A recursive function, like looping code, must have an end condition that always stops the function. Otherwise, the
function continues until a system error occurs or you stop the ColdFusion server.

The following example calculates the factorial of a number, that is, the product of all the integers from 1 through the
number; for example, 4 factorial is 4 X 3 X 2 X 1 = 24.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           177
Building Blocks of ColdFusion Applications




 function Factorial(factor) {
      If (factor LTE 1)
            return 1;
      else
            return factor * Factorial(factor -1);
}

If the function is called with a number greater than 1, it calls itself using an argument one less than it received. It
multiplies that result by the original argument, and returns the result. Therefore, the function keeps calling itself until
the factor is reduced to 1. The final recursive call returns 1, and the preceding call returns 2 * 1, and so on, until all the
initial call returns the end result.

Important: If a recursive function calls itself too many times, it causes a stack overflow. Always test any recursive
functions under conditions that are likely to cause the maximum number of recursions to ensure that they do not cause
a stack overflow.



Building and Using ColdFusion Components

A ColdFusion component (CFC) file contains data and functions that you define in related, multiple methods. You
use CFC pages to organize related actions in one file, which provide can simplify your programming. For more
information on creating applications that use CFCs, see the Adobe website: www.adobe.com.


About ColdFusion components
A ColdFusion component (CFC) is a file saved with the extension .cfc. A CFC can contain data and functions. Within
a CFC, data is referred to as properties. Although you use the cffunction tag to define functions within a CFC, they are
typically referred to as methods instead of functions.

The page on which you define a CFC is also known as a component page. Component pages use the same tags and
functions that regular CFML pages do, plus a small number of special tags (in particular, the cfcomponent tag) and tag
attributes.

You define related methods in a CFC. Unlike ColdFusion custom tags, a single CFC can perform many related actions,
defined in multiple methods. The methods can share a data context, such as metadata and scoping, or manage a
particular database or set of tables. For example, you can define the methods to insert, update, delete, and retrieve
records from a particular database or table in one CFC.


CFCs and object-oriented programming
CFCs are building blocks that let you develop ColdFusion code in an object-oriented manner, although CFCs do not
require you to do object-oriented programming. Some of the object-oriented features of CFCs include encapsulation,
inheritance, and introspection. CFC object-oriented features are like the object-oriented elements in other languages,
like JavaScript.

The technique of incorporating both code and data into one object such as a CFC is known as encapsulation.
Encapsulation lets users pass data to and get a result from your CFC without having to understand the underlying
code. When you use encapsulation, you can validate data that is passed to the CFC. CFCs can also enforce data types,
check for required parameters, and optionally assign default values.

One CFC can inherit the methods and properties of another CFC. Inheritance lets you build multiple specific
components without rewriting the code for the basic building blocks of the components. For more information, see
"Using the Super keyword" on page 202.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    178
Building Blocks of ColdFusion Applications




CFCs support introspection; that is, they can provide information about themselves. If you display a component page
directly in an HTML browser, inspect it in the ColdFusion and Adobe Dreamweaver CS3 component browsers, or use
the CFML GetMetadata function, you see information about the component. This information includes its path,
property, methods, and additional information that you can specify using special documentation attributes and tags.
For more information, see "Using introspection to get information about components" on page 205.

When you use a ColdFusion component, you can invoke a method in the CFC. However, typically, you create an
instance of the CFC, and then invoke methods and refer to properties of the CFC.


When to use CFCs
You can use CFCs in the following ways:

* Developing structured, reusable code

* Creating web services

* Creating Flash Remoting elements

* Using asynchronous CFCs


Developing structured, reusable code
CFCs provide an excellent method for developing structured applications that separate display elements from logical
elements and encapsulate database queries. You can use CFCs to create application functionality that you (and others)
can reuse wherever needed, like user-defined functions (UDFs) and custom tags. If you want to modify, add, or remove
component functionality, you make changes in only one component file.

CFCs have several advantages over UDFs and custom tags. These advantages, which CFCs automatically provide,
include all of the following:

* The ability to group related methods into a single component, and to group related components into a package

* Properties that multiple methods can share

* The This scope, a component-specific scope

* Inheritance of component methods and properties from a base component, including the use of the Super keyword

* Access control

* Introspection for CFC methods, properties, and metadata

CFCs have one characteristic that prevents them from being the automatic choice for all code reuse. It takes relatively
more processing time to instantiate a CFC than to process a custom tag. In turn, it takes substantially more time to
process a custom tag than to execute a user-defined function (UDF). However, after a CFC is instantiated, calling a
CFC method has about the same processing overhead as an equivalent UDF. As a result, do not use CFCs in place of
independent, single-purpose custom tags or UDFs. Instead, use CFCs to create bodies of related methods, particularly
methods that share properties.

For more information about UDFs, custom tags, and other ColdFusion code reuse techniques, see "Creating
ColdFusion Elements" on page 146.


Creating web services
ColdFusion can automatically publish CFC methods as web services. To publish a CFC method as a web service, you
specify the access="remote" attribute in the method's cffunction tag. ColdFusion generates all the required Web
Services Description Language (WSDL) code and exports the CFC methods. For more information on creating web
services in ColdFusion, see "Using Web Services" on page 1093.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     179
Building Blocks of ColdFusion Applications




Creating Flash Remoting elements
Adobe Flash applications that use Flash Remoting can easily take advantage of ColdFusion components for business
logic. In a CFC, the cffunction tag names the function and contains the application logic, and the cfreturn tag returns
the result to Flash.

Note: For ColdFusion component methods to communicate with Flash applications, set the access attribute of the
cffunction tag to remote.

For more information on creating CFCs for Flash Remoting, see "Using Flash with CFCs" on page 616.


Using asynchronous CFCs
ColdFusion provides an event gateway that lets you send a message to a CFC asynchronously. This gateway lets you
initialize processing by a CFC without waiting for the CFC to complete or return a value. You can use asynchronous
CFCs that use this gateway for the following:

* Reindexing a Verity collection

* Logging information

* Running batch processes

For more information on using asynchronous CFCs, see "About event gateways" on page 1254.


Creating ColdFusion components
When you create CFCs, you create methods, which are ColdFusion user-defined functions, in the component page.
You pass data to a method by using parameters. The method then performs the function and, if specified in the
cfreturn tag, returns data.

You can also define variables in a CFC. Within a CFC, these variables are known as properties.


Tags for creating CFCs
You use the following tags to create a CFC. You include these tags on the CFML page that defines the CFC.


 Tag                       Description

 cfcomponent               Contains a component definition; includes attributes for introspection. For more information, see "Building
                           ColdFusion components" on page 180.

 cffunction                Defines a component method (function); includes attributes for introspection. For more information, see
                           "Defining component methods" on page 180.

 cfargument                Defines a parameter (argument) to a method; includes attributes for introspection. For more information, see
                           "Defining and using method parameters" on page 183.

 cfproperty                Defines variables for CFCs that provide web services; also use to document component properties. For more
                           information, see "The cfproperty tag" on page 188.



Elements of a CFC
A CFC has the following characteristics:

* It is a single CFML page with a .cfc filename extension. The component name is the same as the filename. For
    example, if the file is myComponent.cfc, the component name is myComponent.

* The page is surrounded by a cfcomponent tag. No code can be outside this tag.

* The component page defines methods (functions), properties (data), or both. Most CFCs have methods, or
    methods and properties, but you can also have a CFC that contains only properties.



                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    180
Building Blocks of ColdFusion Applications




* You use the cffunction tag to define CFC methods. The CFScript function statement can create simple methods,
   but it does not provide options to control access to the method, provide metadata, specify a return type, or control
   generated output.

* You can write code on the component page that is outside cffunction definitions. This code executes when the
   CFC is instantiated or whenever you invoke a method of the CFC.


Building ColdFusion components
You use the cfcomponent and cffunction tags to create ColdFusion components. By itself, the cffunction tag does
not provide functionality. The cfcomponent tag provides an envelope that describes the functionality that you build
in CFML and enclose in cffunction tags. The following example shows the skeleton of a component with two
methods:

 <cfcomponent>
     <cffunction name="firstMethod">
            <!--- CFML code for this method goes here. --->
     </cffunction>
     <cffunction name="secondMethod">
            <!--- CFML code for this method goes here. --->
     </cffunction>
</cfcomponent>


Defining component methods
You define component methods using cffunction tags. The following example defines a CFC that contains two
methods, getall and getsalary:

 <cfcomponent>
     <cffunction name="getall" output="false" returntype="query">
           <cfset var queryall="">
           <cfquery name="queryall" datasource="cfdocexamples">
                SELECT * FROM EMPLOYEE
           </cfquery>
           <cfreturn queryall>
     </cffunction>
     <cffunction name="getsalary" output="false">
           <cfset var getNamesandSalary="">
                <cfquery name="getNamesandSalary" datasource="cfdocexamples">
                      SELECT FirstName, LastName, Salary FROM EMPLOYEE
                </cfquery>
           <cfreturn getNamesandSalary>
     </cffunction>
</cfcomponent>

Because component methods are ColdFusion functions, most of their features and coding techniques are identical to
those of user-defined functions. For more information on using the cffunction tag to create functions, see "Writing
and Calling User-Defined Functions" on page 153. Like other ColdFusion functions, CFC methods can display
information directly by generating output, or can return a value to the code or client that invoked the method.

You use the following cffunction tag attributes only for CFCs:

* The displayname and hint attributes, which document the CFC; for more information, see "Documenting CFCs"
   on page 187.

* The access attribute, which controls access to the CFC; for more information, see "Using access security" on
   page 204.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      181
Building Blocks of ColdFusion Applications




For detailed reference information on the cffunction tag, see the CFML Reference.


Defining CFCs with related methods
When defining CFCs, it is good programming practice to organize related methods in one CFC. For example, you
could place all methods that perform operations related to a user, such as addUser, editUser, and
storeUserPreferences, in one CFC. You can group related mathematical functions into one CFC. A CFC can also
contain all the methods and properties necessary for a shopping cart. The following CFC contains two cffunction tags
that define two component methods, getEmp and getDept. When invoked, the component methods query the
ExampleApps database. The cfreturn tag returns the query results to the client, or page, where the method was
invoked.

 <cfcomponent>
     <cffunction name="getEmp">
           <cfset var empQuery="">
            <cfquery name="empQuery" datasource="cfdocexamples" dbtype="ODBC">
                SELECT FIRSTNAME, LASTNAME, EMAIL
                FROM tblEmployees
            </cfquery>
            <cfreturn empQuery>
     </cffunction>
     <cffunction name="getDept">
           <cfset var deptQuery="">
           <cfquery name="deptQuery" datasource="cfdocexamples" dbtype="ODBC">
                SELECT *
                FROM tblDepartments
            </cfquery>
            <cfreturn deptQuery>
     </cffunction>
</cfcomponent>


Placing executable code in a separate file
You can place executable code in a separate file from the main component definition page. By placing the method
execution code in a separate file, you can separate property initialization code, meta information, and the method
definition shell from the executable method definition code. This technique lets you modularize your code and helps
prevent CFML pages from getting too long and complex.

To separate the component method code, use a cfinclude tag on the component definition page to call the page that
contains the component method code.

Note: If your method takes arguments or returns data to the page that invokes it, the cfargument tag and the cfreturn tag
must be on the component definition page, not on the included page.


Create a component method by using the cfinclude tag
1 Create a tellTime.cfc file with the following code:

    <cfcomponent>
         <cffunction name="getUTCTime">
              <cfinclude template="getUTCTime.cfm">
         <cfreturn utcStruct.Hour & ":" & utcStruct.Minute>
         </cffunction>
   </cfcomponent>

2 Create a ColdFusion page with the following code, and save it as getUTCTime.cfm in the same directory as
   tellTime.cfc:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       182
Building Blocks of ColdFusion Applications




    <cfscript>
         serverTime=now();
         utcTime=GetTimeZoneInfo();
         utcStruct=structNew();
         utcStruct.Hour=DatePart("h", serverTime);
         utcStruct.Minute=DatePart("n", serverTime);
         utcStruct.Hour=utcStruct.Hour + utcTime.utcHourOffSet;
         utcStruct.Minute=utcStruct.Minute + utcTime.utcMinuteOffSet;
         if (utcStruct.Minute LT 10) utcStruct.Minute = "0" & utcStruct.Minute;
    </cfscript>

    In the example, the getUTCTime method definition calls the getUTCTime.cfm file with the cfinclude tag. The
    getUTCTime.cfm code calculates the UTC time representation of the current time and populates a structure with
    hour and minute values. The method in tellTime.cfc then uses the information in the structure to return the current
    UTC time as a string to the calling page. The included page must not include a cfreturn statement.


Initializing instance data
Some components have instance data, which is data that persists as long as the component instance exists. For
example, a shopping cart component can have instance data that includes the IDs and quantities of items that the user
places in the shopping cart. Instance data is often shared by several methods that can create, delete, or modify the data.

You can refer to instance data of a CFC only if you create an instance of the CFC. From inside the CFC, you refer to
instance data of the CFC using the this prefix, for example this.firstvariable. From the calling page, you refer
to instance data using dot notation, including the name of the instance of the component and the name of the instance
data, as in objectname.ivarname. Components whose methods you invoke without first instantiating the component
do not typically have instance data.

You initialize instance data at the top of the component definition, before the method definitions. ColdFusion executes
this code when it instantiates the component; for example, when a cfobject tag creates the component instance.
Because this code executes only when the instance is created and it typically "constructs" properties of the component,
instance data initialization code is sometimes called constructor code.

You can use any CFML tag or function in constructor code, and the code can perform any ColdFusion processing, such
as querying a database or data validation and manipulation. If one component extends another, the parent
component's constructor code executes before the child component's constructor code.

Note: ColdFusion does not require you to place the initialization code at the top of the component definition; however, it
is good programming practice to do so.

The following example shows constructor code for a shopping cart CFC:

 <cfcomponent>
     <!--- Initialize the array for the cart item IDs and quantities. --->
     <cfset This.CartData = ArrayNew(2)>
     <!--- The following variable has the ID of the "Special Deal" product for
                this session. --->
     <cfset This.Special_ID = RandRange(1, 999)>

For information on scopes, see "The This scope" on page 198 and "The Variables scope" on page 198.

A useful technique is to define a method named init(), which initializes an instance of a CFC, acting as a constructor.
The init() method can initialize constants and return an instance of the component to the calling page. The following
code illustrates an example of an init() method:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                183
Building Blocks of ColdFusion Applications




 <cfcomponent displayname="shoppingCart">
     <cffunction name="init" access="public" output="no" returntype="shoppingCart">
           <cfargument name="shoppingCartID" type="UUID" required="yes">
           <cfset variables.shoppingCartID = arguments.shoppingCartID>
           <cfreturn this>
     </cffunction>


     <!--- Additional methods go here. --->
</cfcomponent>

In this example, the init() method uses the variables scope to make the shopping cart ID available anywhere in the
CFC. For more information about scope, see "CFC variables and scope" on page 198.


Defining and using method parameters
You pass data to a method by using parameters. To define a component method parameter, use the cfargument tag in
the cffunction tag body. To define multiple parameters, use multiple cfargument tags. The tag names a parameter and
lets you specify the following:

* Whether the parameter is required

* The type of data that is required

* A default argument value

* Display name and hint metadata for CFC introspection

Note: You can create CFC methods that do not use cfargument tags, for example, if you use positional parameters in
your methods. However, most CFC methods use the cfargument tag.


Example: convertTemp.cfc
The convertTemp.cfc file consists of the following:

 <cfcomponent>
     <!--- Celsius to Fahrenheit conversion method. --->
     <cffunction name="ctof" output="false">
           <cfargument name="temp" required="yes" type="numeric">
           <cfreturn ((temp*9)/5)+32>
     </cffunction>


     <!--- Fahrenheit to Celsius conversion method. --->
     <cffunction name="ftoc" output="false">
           <cfargument name="temp" required="yes" type="numeric">
           <cfreturn ((temp-32)*5/9)>
     </cffunction>
</cfcomponent>


Reviewing the code
The convertTemp CFC contains two methods that convert temperature. The following table describes the code and its
function:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                              184
Building Blocks of ColdFusion Applications




 Code                                            Description

 <cfcomponent>                                   Defines the component.

 <cffunction name="ctof" output="false">         Defines the ctof method.

                                                 Indicates that this method does not display output.

 <cfargument name="temp" required="yes"          Creates the temp parameter of the ctof method. Indicates that it is required and
 type="numeric">                                 that the expected value is numeric.

 <cfreturn ((temp*9)/5)+32>                      Defines the value that the method returns.

 </cffunction>                                   Ends the method definition.

 <cffunction name="ftoc" output="false">         Defines the ftoc method.

                                                 Indicates that this method does not display output.

 <cfargument name="temp" required="yes"          Creates the temp parameter of the ftoc method. Indicates that it is required and
 type="numeric">                                 that the expected value is numeric.

 <cfreturn ((temp-32)*5/9)>                      Defines the value that the method returns.

 </cffunction>                                   Ends the method definition.

 </cfcomponent>                                  Ends the component definition.



Example: tempConversion.cfm
The ColdFusion page tempConversion.cfm is an HTML form in which the user enters the temperature to convert, and
selects the type of conversion to perform. When the user clicks the Submit button, ColdFusion performs the actions
on the processForm.cfm page. The file tempConversion.cfm, which is in the same directory as convertTemp.cfc,
consists of the following:

<cfform action="processForm.cfm" method="POST">
     Enter the temperature:
           <input name="temperature" type="text"><br>
           <br>
     Select the type of conversion:<br>
           <select name="conversionType">
                <option value="CtoF">Celsius to Farenheit</option>
                <option value="FtoC">Farenheit to Celsius</option>
           </select><br><br>
     <input name="submitform" type="submit" value="Submit">
</cfform>


Example: processForm.cfm
The ColdFusion page processForm.cfm calls the appropriate component method, based on what the user entered in
the form on the tempConversion.cfm page. Place it in the same directory as convertTemp.cfc.




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                185
Building Blocks of ColdFusion Applications




<cfif #form.conversionType# is "CtoF">
     <cfinvoke component="convertTemp" method="ctof" returnvariable="newtemp"
           temp=#form.temperature#>
     <cfoutput>#form.temperature# degrees Celsius is #newtemp# degrees
           Farenheit.</cfoutput>
     <cfelseif #form.conversionType# is "FtoC">
           <cfinvoke component="convertTemp" method="ftoc"
                returnvariable="newtemp" temp=#form.temperature#>
           <cfoutput>#form.temperature# degrees Fahrenheit is #newtemp# degrees
                Celsius.</cfoutput>
</cfif>


Reviewing the code
The file processForm.cfm invokes the appropriate component method. The following table describes the code and its
function:


 Code                                                Description

 <cfif form.conversionType is "CtoF">                Executes the code in the cfif block if the user selected Celsius to Fahrenheit
                                                     as the conversion type in the form on the tempConversion.cfm page.

 <cfinvoke component="convertTemp"                   Invokes the ctof method of the convertTemp component, without
 method="ctof"returnvariable="newtemp"arguments      creating an instance of the convertTemp component. Specifies newtemp
 .temp="#form.temperature#">                         as the result variable for the method. Assigns the temperature value that the
                                                     user entered in the form to the variable temp, which is specified in the
                                                     cfargument tag of the ctof method. When invoking the ctof method,
                                                     the temp variable is assigned to the Arguments scope. For more information
                                                     about variables and scope, see "CFC variables and scope" on page 198.

 <cfoutput>#form.temperature# degrees Celsius is     Displays the temperature that the user entered in the form, the text
 #newtemp#bdegrees Fahrenheit.</cfoutput>            "degrees Celsius is," the new temperature value that results from the ctof
                                                     method, and the text "degrees Fahrenheit."

 <cfelseif #form.conversionType# is "FtoC">          Executes the code in the cfelseif block if the user selected Fahrenheit to
                                                     Celsius as the conversion type in the form on the tempConversion.cfm page.

 <cfinvoke component="converttemp"                   Invokes the ftoc method of the convertTemp component, without
 method="ftoc"returnvariable="newtemp"               creating an instance of the convertTemp component. Specifies newtemp
 temp=#form.temperature#>                            as the result variable for the method. Assigns the temperature value that the
                                                     user entered in the form to the variable temp, which is specified in the
                                                     cfargument tag of the ftoc method. When invoking the ftoc method,
                                                     the temp variable is assigned to the Arguments scope. For more information
                                                     about variables and scope, see "CFC variables and scope" on page 198.

 <cfoutput>#form.temperature# degrees Fahrenheit     Displays the temperature that the user entered in the form, the text
 is #newtemp#degrees Celsius.</cfoutput>             "degrees Fahrenheit is," the new temperature value that results from the
                                                     ftoc method, and the text "degrees Celsius."


 </cfif>                                             Closes the cfif block.


To run the example, display the tempConversion.cfm page in your browser. When you enter a value in the text box of
the form, the value is stored in the form.temperature variable. Processing is then performed on the processForm.cfm
page, which refers to the value as form.temperature. When you invoke either method, the cfinvoke tag assigns the
value form.temperature to temp; temp is the argument specified in the cfargument tag of the appropriate method.
The appropriate method in the convertTemp component performs the necessary calculations and returns the new
value as newtemp.

For detailed reference information on the cfargument tag, see the CFML Reference.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  186
Building Blocks of ColdFusion Applications




To access the parameter values in the component method definition, use structure- or array-like notation with the
Arguments scope. The following example refers to the lastName argument as Arguments.lastname; it could also
refer to it as Arguments[1]. In addition, you can access arguments directly using number (#) signs, such as
#lastname#; however, it is better programming practice to identify the scope (for example,
#Arguments.lastname#). Also, you can use Array- or structure-like notation, which lets you loop over multiple
parameters.

For more information on the Arguments scope, see "The Arguments scope" on page 200.


Define parameters in the component method definition
Create a component with the following contents, and save it as corpQuery.cfc in a directory under your web root
directory:

<cfcomponent>
     <cffunction name="getEmp">
            <cfargument name="lastName" type="string" required="true"
                      hint="Employee last name">
            <cfset var empQuery="">
             <cfquery name="empQuery" datasource="cfdocexamples">
                SELECT LASTNAME, FIRSTNAME, EMAIL
                FROM tblEmployees
                WHERE LASTNAME LIKE '#Arguments.lastName#'
             </cfquery>
                <!--- Use cfdump for debugging purposes. --->
             <cfdump var=#empQuery#>
     </cffunction>
     <cffunction name="getCat" hint="Get items below specified cost">
     <cfargument name="cost" type="numeric" required="true">
            <cfset var catQuery="">
            <cfquery name="catQuery" datasource="cfdocexamples">
                SELECT ItemName, ItemDescription, ItemCost
                FROM tblItems
                WHERE ItemCost <= #Arguments.cost#
             </cfquery>
                <!--- Use cfdump for debugging purposes. --->
             <cfdump var=#catQuery#>
     </cffunction>
</cfcomponent>

In the example, the cfargument attributes specify the following:

* The name attributes define the parameter names.

* The type attribute for the lastName argument specifies that the parameter must be a text string. The type attribute
   for the cost argument specifies that the parameter must be a numeric value. These attributes validate the data
   before it is submitted to the database.

* The required attributes indicate that the parameters are required, if not, ColdFusion throws an exception.

* The Arguments scope provides access to the parameter values.


Providing results
ColdFusion components can provide information in the following ways:

* They can generate output that is displayed on the calling page.

* They can return a variable.



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      187
Building Blocks of ColdFusion Applications




You can use either technique, or a combination of both, in your applications. The best technique to use depends on
your application's needs and your coding methodologies. For example, many CFC methods that perform business
logic return the results as a variable, and many CFC methods that display output directly are designed as modular units
for generating output, and do not do business logic.


Displaying output
If you do not specifically suppress output, any text, HTML code, or output that CFML tags generate inside your
method gets returned as generated output to the client that calls the component method. If the client is a web browser,
it displays these results. For example, the following getLocalTime1 component method shows the local time directly
on the page that invokes the method:

 <cfcomponent>
      <cffunction name="getLocalTime1">
           <cfoutput>#TimeFormat(now())#</cfoutput>
      </cffunction>
</cfcomponent>

Component methods that are called by using Flash Remoting or as web services cannot use this method to provide
results.


Returning a results variable
In the component method definition, you use the cfreturn tag to return the results to the client as variable data. For
example, the following getLocalTime2 component method returns the local time as a variable to the ColdFusion page
or other client that invokes the method:

 <cfcomponent>
      <cffunction name="getLocalTime">
           <cfreturn TimeFormat(now())>
      </cffunction>
</cfcomponent>

The ColdFusion page or other client, such as a Flash application, that receives the result then uses the variable data as
appropriate.

Note: If a CFC is invoked using a URL or by submitting a form, ColdFusion returns the variable as a WDDX packet. A
CFC that is invoked by Flash Remoting, or any other instance of a CFC, must not return the This scope.

You can return values of all data types, including strings, integers, arrays, structures, and instances of CFCs. The
cfreturn tag returns a single variable, as does the return CFScript statement. Therefore, if you want to return more
than one result value at a time, use a structure. If you do not want to display output in a method, use output="false"
in the cffunction tag.

For more information on using the cfreturn tag, see the CFML Reference.


Documenting CFCs
ColdFusion provides several ways to include documentation about your CFCs in your component definitions. The
documentation is available when you use introspection to display information about the CFC or call the GetMetadata
or GetComponentMetaData function to get the component's metadata. You can use the following tools for
documenting CFCs:

* The displayname and hint attributes

* User-defined metadata attributes

* The cfproperty tag




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      188
Building Blocks of ColdFusion Applications




For information on displaying the information, see "Using introspection to get information about components" on
page 205.


The displayname and hint attributes
The cfcomponent, cffunction, cfargument, and cfproperty tags have displayname and hint attributes.

The displayname attribute lets you provide a more descriptive name for a component, attribute, method, or property.
When you use introspection, this attribute appears in parentheses next to the component or method name, or on the
parameter information line.

You use the hint attribute for longer descriptions of the component, method, or argument. In the introspection
display, this attribute appears on a separate line or on several lines of the component or method description, and at the
end of the argument description.


Metadata attributes
You can include arbitrary metadata information as attributes of the cfcomponent, cffunction, cfargument, and
cfproperty tags. To create a metadata attribute, specify the metadata attribute name and its value. For example, in
the following cfcomponent tag, the Author attribute is a metadata attribute. This attribute is not used as a function
parameter; instead, it indicates who wrote this CFC.

 <cfcomponent name="makeForm" Author="Bean Lapin">

Metadata attributes are not used by ColdFusion for processing; they also do not appear in standard ColdFusion
introspection displays; however, you can access and display them by using the GetMetaData or
GetComponentMetaData function to get the metadata. Each attribute name is a key in the metadata structure of the
CFC element.

Metadata attributes are used for more than documentation. Your application can use the GetMetadata function to get
the metadata attributes for a component instance, or the GetComponentMetaData function to get the metadata for an
interface or component that you have not yet instantiated. You can then act based on the values. For example, a
mathCFC component could have the following cfcomponent tag:

 <cfcomponent displayname="Math Functions" MetaType="Float">

In this case, a ColdFusion page with the following code sets the MetaTypeInfo variable to Float:

 <cfobject component="mathCFC" name="MathFuncs">
<cfset MetaTypeInfo=GetMetadata(MathFuncs).MetaType>

Note: All metadata values are replaced by strings in the metadata structure returned from the GetMetadata function.
Because of this, do not use expressions in custom metadata attributes.


The cfproperty tag
The cfproperty tag is used to create complex data types with WSDL descriptors and for component property
documentation, as follows:

* It can create complex data types with WSDL descriptions for ColdFusion web services. For more information, see
   "Using ColdFusion components to define data types for web services" on page 1109.

* It can provide documentation of component properties in the ColdFusion introspection output. The introspection
   information includes the values of the standard cfproperty tag attributes.

Note: The cfproperty tag does not create a variable or assign it a value. It is used for information purposes only. You
use a cfset tag, or CFScript assignment statement, to create the property and set its value.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      189
Building Blocks of ColdFusion Applications




Saving and naming ColdFusion components
The following table lists the locations in which you can save component files and how they can be accessed from each
location:


                                  URL            Form          Flash Remoting    Web services        ColdFusion page

 Current directory                N/A            Yes           N/A               N/A                 Yes

 Web root                         Yes            Yes           Yes               Yes                 Yes

 ColdFusion mappings              No             No            No                No                  Yes

 Custom tag roots                 No             No            No                No                  Yes


Note: ColdFusion mappings and custom tag roots can exist within the web root. If so, they are accessible to remote
requests, including URL, form, Flash Remoting, and web services invocation.

When you store components in the same directory, they are members of a component package. You can group related
CFCs into packages. Your application can refer to any component in a directory specifically by using a qualified
component name that starts with a subdirectory of one of the accessible directories and uses a period to delimit each
directory in the path to the directory that contains the component. For example, the following example is a qualified
name of a component named price:

 catalog.product.price

In this example, the price.cfc file must be in the catalog\product subdirectory of a directory that ColdFusion searches
for components, as listed in the preceding table. When you refer to a component using the qualified name, ColdFusion
looks for the component in the order described in "Specifying the CFC location" on page 196.

Establishing a descriptive naming convention is a good practice, especially if you plan to install the components as part
of a packaged application.


Using ColdFusion components
You can use a CFC in two ways:

1 You can instantiate a CFC object, which creates a CFC instance. You then invoke the methods of the instance. You
   can access the CFC methods and data as instance elements. You can also use the instance in the cfinvoke tag to
   invoke the CFC methods. When you instantiate a CFC, data in the CFC is preserved as long as the CFC instance
   exists, and ColdFusion does not incur the overhead of creating the instance each time you call a method.

   Instantiate CFCs to preserve data in the CFC. To ensure processing efficiency if you use the CFC more than once
   on a page, instantiate the CFC before you invoke its methods.

   Methods that are executed remotely through Flash Remoting and web services always create an instance of the CFC
   before executing the method.

2 You can invoke (call) a method of the CFC without creating an instance of the CFC, which is referred to as
   transiently invoking a method. In this case, ColdFusion creates an instance of the CFC that exists only from the time
   you invoke the method until the method returns a result. No data is preserved between invocations and ColdFusion
   does not keep an instance of the CFC that you can reuse elsewhere in your CFML. It is considered a best practice
   to create an instance of a CFC before invoking any of its methods, unless your CFML request uses the CFC only
   once. If you transiently invoke a method frequently, consider creating a user-defined function to replace the CFC
   method.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   190
Building Blocks of ColdFusion Applications




You can create persistent CFCs by assigning the CFC instance to a persistent scope, such as the Session or Application
scope. This way, you can create CFCs for objects, such as shopping carts or logged-in users, that must persist for
sessions. You can also create CFCs that provide application-specific data and methods.


Tags for using CFCs
The following table lists the tags that you use to instantiate or invoke a CFC. You use these tags on the CFML page on
which you instantiate or invoke the CFC.


 Tag                                   Description

 cfinvoke                              Invokes a method of a CFC.

 cfinvokeargument                      Passes the name and value of a parameter to a component method.

 cfobject                              Creates a CFC instance.

 CreateObject                          Creates a CFC instance.



CFC invocation techniques
ColdFusion provides many ways to instantiate CFCs and invoke CFC methods. The following table lists the
techniques, including the ColdFusion tags and functions that you use:


 Invocation                     Description                                      For more information

 cfinvoke tag                   Invokes a component method. Can invoke            See "Invoking CFC methods with the cfinvoke tag" on
                                methods of a CFC instance or invoke the           page 191.
                                methods transiently.

 cfset tag and assignment       Invoke methods and access properties of a         See "Using components directly in CFScript and CFML"
 statements                     component instance.                               on page 193.

 URL (HTTP GET)                 Transiently invokes a component method by         See "Invoking component methods by using a URL" on
                                specifying the component and method names         page 194.
                                in the URL string.

 Form control(HTTP POST)        Transiently invokes a component method using      See "Invoking component methods by using a form" on
                                the HTML form and input tags and their            page 194.
                                attributes.

 Flash Remoting                 ActionScript can transiently invoke component     See "Using the Flash Remoting Service" on page 606.
                                methods.

 Web services                   The cfinvoke tag and CFScript consume web         See "Using Web Services" on page 1093.
                                services in ColdFusion. External applications can
                                also consume CFC methods as web services.



Instantiating CFCs
If you use a CFC multiple times in a ColdFusion request, or if you use a CFC with persistent properties, use the cfobject
tag or CreateObject function to instantiate the CFC before you call its methods.

The following example uses the cfobject tag to create an instance of the tellTime CFC.

<cfobject component="tellTime" name="tellTimeObj">

The following example uses the CreateObject function to instantiate the same component in CFScript:

tellTimeObj = CreateObject("component", "tellTime");




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        191
Building Blocks of ColdFusion Applications




Invoking CFC methods with the cfinvoke tag
The cfinvoke tag can invoke methods on a CFC instance or invoke CFC methods transiently. You can also use the
cfinvoke tag to invoke CFC methods from within a CFC.


Invoking methods of a CFC instance
To invoke a component method of a CFC instance, use the cfinvoke tag and specify the following:

* The CFC instance name, enclosed in number signs (#), in the component attribute.

* The method name, in the method attribute.

* Any parameters. For information on passing parameters, see "Passing parameters to methods by using the cfinvoke
   tag" on page 196.

* If the component method returns a result, the name of the variable for the result in the returnVariable attribute.

The following procedure creates an application that displays the current UTC and local time.

1 Create a file named tellTime2.cfc with the following code:

    <cfcomponent>
         <cffunction name="getLocalTime" access="remote">
              <cfreturn TimeFormat(now())>
         </cffunction>
         <cffunction name="getUTCTime" access="remote">
              <cfscript>
                    serverTime=now();
                    utcTime=GetTimeZoneInfo();
                    utcStruct=structNew();
                    utcStruct.Hour=DatePart("h", serverTime);
                    utcStruct.Minute=DatePart("n", serverTime);
                    utcStruct.Hour=utcStruct.Hour + utcTime.utcHourOffSet;
                    utcStruct.Minute=utcStruct.Minute + utcTime.utcMinuteOffSet;
                    if (utcStruct.Minute LT 10) utcStruct.Minute = "0" & utcStruct.Minute;
              </cfscript>
              <cfreturn utcStruct.Hour & ":" & utcStruct.Minute>
         </cffunction>
   </cfcomponent>

   The example defines two component methods: getLocalTime and getUTCTime.

2 Create a ColdFusion page, with the following code and save it in the same directory as the tellTime component:

    <!--- Create the component instance. --->
   <cfobject component="tellTime2" name="tellTimeObj">
   <!--- Invoke the methods. --->
   <cfinvoke component="#tellTimeObj#" method="getLocalTime" returnvariable="localTime">
   <cfinvoke component="#tellTimeObj#" method="getUTCTime" returnvariable="UTCTime">
   <!--- Display the results. --->
   <h3>Time Display Page</h3>
   <cfoutput>
         Server's Local Time: #localTime#<br>
         Calculated UTC Time: #UTCTime#
   </cfoutput>

This example uses the cfobject tag to create an instance of the tellTime component and the cfinvoke tag to invoke the
instance's getLocalTime and getUTCTime methods. In this example, the CFC contains the functional logic in the
methods, which return a result to the calling page, and the calling page displays the results. This structure separates the
logic from the display functions, which usually results in more reusable code.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 192
Building Blocks of ColdFusion Applications




Invoking component methods transiently
In ColdFusion pages or components, the cfinvoke tag can invoke component methods without creating a persistent
CFC instance.

To invoke a component method transiently, use the cfinvoke tag and specify the following:

* The name or path of the component, in the component attribute.

* The method name, in the method attribute.

* Any parameters. For information on passing parameters, see "Passing parameters to methods by using the cfinvoke
   tag" on page 196.

* If the component method returns a result, the name of the variable that contains the result, in the returnVariable
   attribute.

The following procedure creates an application that displays the local time.

1 Create the following component and save it as tellTime.cfc:

    <cfcomponent>
         <cffunction name="getLocalTime">
              <cfoutput>#TimeFormat(now())#</cfoutput>
         </cffunction>
   </cfcomponent>

   The example defines a component with one method, getLocalTime, that displays the current time.

2 Create a ColdFusion page, with the following code, and save it in the same directory as the tellTime component:

    <h3>Time Display Page</h3>
   <b>Server's Local Time:</b>
   <cfinvoke component="tellTime" method="getLocalTime">

   Using the cfinvoke tag, the example invokes the getLocalTime component method without creating a persistent
   CFC instance.


Using the cfinvoke tag within the CFC definition
You can use the cfinvoke tag to invoke a component method within the component definition; for example, to call a
utility method that provides a service to other methods in the component. To use the cfinvoke tag in this instance,
do not create an instance or specify the component name in the cfinvoke tag, as the following example shows:

<cfcomponent>
      <cffunction name="servicemethod" access="public">
           <cfoutput>At your service...<br></cfoutput>
      </cffunction>
      <cffunction name="mymethod" access="public">
           <cfoutput>We're in mymethod.<br></cfoutput>
           <!--- Invoke a method in this CFC. --->
           <cfinvoke method="servicemethod">
      </cffunction>
</cfcomponent>

Note: When you invoke a method from within the component definition in which you define the method, do not use the
This scope, because this resets the access privileges.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    193
Building Blocks of ColdFusion Applications




Invoking methods by using dynamic method names
The cfinvoke tag is the only way to efficiently invoke different component methods based on variable data (for
example, form input). In this case, you use a variable name, such as Form.method, as the value of the method attribute.
In the following example, the user selects a report from a form:

 <select name="whichreport">
      <option value="all">Complete Report</option>
      <option value="salary">Salary Information</option>
</select>

The cfinvoke tag then invokes the appropriate method, based on what the user selected:

 <cfinvoke component="getdata" method="#form.whichreport#" returnvariable="queryall">


Using components directly in CFScript and CFML
You can invoke methods of a component instance directly using CFScript or in CFML tags. To invoke component
methods directly, use the CreateObject function or cfobject tag to instantiate the component. Thereafter, use the
instance name followed by a period and the method that you are calling to invoke an instance of the method. Always
use parentheses after the method name, even if the method does not take any parameters.

You can use this syntax anywhere that you can use a ColdFusion function, such as in cfset tags or surrounded by
number signs in the body of a cfoutput tag.


Invoking component methods in CFScript
The following example shows how to invoke component methods in CFScript:

 <!--- Instantiate once and reuse the instance.--->
<cfscript>
      tellTimeObj=CreateObject("component","tellTime");
      WriteOutput("Server's Local Time: " & tellTimeObj.getLocalTime());
      WriteOutput("<br> Calculated UTC Time: " & tellTimeObj.getUTCTime());
</cfscript>

In the example, the three CFScript statements do the following:

1 The CreateObject function instantiates the tellTime CFC as tellTimeObj.

2 The first WriteOutput function displays text followed by the results returned by the getLocalTime method of the
    tellTimeObj instance.

3 The second WriteOutput function displays text followed by the results returned by the getUTCTime method of the
    tellTimeObj instance.

In CFScript, you use the method name in standard function syntax, such as methodName().


Invoking component methods in CFML
The following example uses CFML tags to produce the same results as the CFScript example:

 <cfobject name="tellTimeObj" component="tellTime">
<cfoutput>
      Server's Local Time: #tellTimeObj.getLocalTime()#<br>
      Calculated UTC Time: #tellTimeObj.getUTCTime()#
</cfoutput>


Accessing component data directly
You can access data in the component's This scope directly in CFScript and cfset assignment statements. For example,
if a user data CFC has a This.lastUpdated property, you could have code such as the following:



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   194
Building Blocks of ColdFusion Applications




<cfobject name="userDataCFC" component="userData">
<cfif DateDiff("d", userDataCFC.lastUpdated, Now()) GT 30>
     <!--- Code to deal with older data goes here. --->
</cfif>

For more information, see "The This scope" on page 198.


Invoking CFC methods with forms and URLs
You can invoke CFC methods directly by specifying the CFC in a URL, or by using HTML and CFML form tags.
Because all HTTP requests are transient, these methods only let you transiently invoke methods. They do not let you
create persistent CFC instances.


Invoking component methods by using a URL
To invoke a component method by using a URL, append the method name to the URL in standard URL query-string,
name-value syntax. You can invoke only one component method per URL request, for example:

http://localhost:8500/tellTime.cfc?method=getLocalTime

Note: To use URL invocation, set the access attribute of the cffunction tag to remote.

To pass parameters to component methods using a URL, append the parameters to the URL in standard URL query-
string, name-value pair syntax; for example:

http://localhost:8500/corpQuery.cfc?method=getEmp&lastName=camden

To pass multiple parameters within a URL, use the ampersand character (&) to delimit the name-value pairs; for
example:

http://localhost:8500/corpQuerySecure.cfc?method=getAuth&store=women&dept=shoes

Note: To ensure data security, Adobe strongly recommends that you not pass sensitive information over the web using
URL strings. Potentially sensitive information includes all personal user information, including passwords, addresses,
telephone numbers, and so on.

If a CFC method that you access using the URL displays output directly, the user's browser shows the output. You can
suppress output by specifying output="No" in the cffunction tag. If the CFC returns a result using the cfreturn tag,
ColdFusion converts the text to HTML edit format (with special characters replaced by their HTML escape sequences),
places the result in a WDDX packet, and includes the packet in the HTML that it returns to the client.


Invoking component methods by using a form
To invoke a method by using a ColdFusion or HTML form, do the following:

* Specify the CFC filename or path in the form or cfform tag action attribute.

* Specify the CFC method in a hidden form field, as follows:

     <form action="myComponent.cfc" method="POST">.
         <input type="Hidden" name="method" value="myMethod">

* Alternatively, if you use the POST method to submit the form, you can follow the filename with
   ?method=methodname, where methodname is the name of the CFC method, as shown in the following line. You
   cannot use this technique with the GET method.

     <form action="myComponent.cfc?method=myMethod" method="POST">.

* Create an input tag for each component method parameter. The name attribute of the tag must be the method
   parameter name and the field value is the parameter value.

* Specify the access="remote" attribute in the cffunction tag that defines the CFC method being invoked



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      195
Building Blocks of ColdFusion Applications




If the CFC method that you invoke from the form displays output directly, the user's browser shows the output. (You
can use the cffunction tag output attribute to disable displaying output.) If the CFC returns a result using the cfreturn
tag, ColdFusion converts the text to HTML edit format, places it in a WDDX packet, and includes the packet in the
HTML that it returns to the client.

1 Create a corpFind.cfm file with the following contents:

    <h2>Find People</h2>
    <form action="components/corpQuery.cfc?method=getEmp" method="post">
         <p>Enter employee's last Name:</p>
         <input type="Text" name="lastName">
         <input type="Hidden" name="method" value="getEmp">
         <input type="Submit" title="Submit Query"><br>
    </form>

    In the example, the form tag's action attribute points to the corpQuery component and invokes the getEmp
    method.

2 Create a corpQuery.cfc file, specifying access="remote" for each cffunction tag, as the following example
    shows:

    <cfcomponent>
         <cffunction name="getEmp" access="remote">
              <cfargument name="lastName" required="true">
              <cfset var empQuery="">
                <cfquery name="empQuery" datasource="cfdocexamples">
                    SELECT LASTNAME, FIRSTNAME, EMAIL
                    FROM tblEmployees
                    WHERE LASTNAME LIKE '#arguments.lastName#'
                </cfquery>
                <cfoutput>Results filtered by #arguments.lastName#:</cfoutput><br>
                <cfdump var=#empQuery#>
         </cffunction>
    </cfcomponent>

3 Open a web browser and enter the following URL:

    http://localhost/corpFind.cfm

    ColdFusion displays the search form. After you enter values and click the Submit Query button, the browser
    displays the results.


Accessing CFCs from outside ColdFusion and basic HTML
Flash applications that use Flash Remoting can easily take advantage of ColdFusion components for business logic.
Similarly, you can export CFCs so that any application can access CFC methods as web services.

For ColdFusion component methods to communicate with Flash Remoting applications, set the access attribute of
the cffunction tag to remote.

For more information on creating CFCs for Flash Remoting, see "Using the Flash Remoting Service" on page 606

Any application, whether it is a ColdFusion application, a Java application, JSP page, or a .Net application, can access
well-formed ColdFusion components as web services by referencing the WSDL file that ColdFusion automatically
generates.

To see a component's WSDL definition, specify the component web address in a URL, followed by ?wsdl; for example:

http://localhost:8500/MyComponents/arithCFC.cfc?wsdl

For more information on using CFCs as web services, see "Using Web Services" on page 1093



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  196
Building Blocks of ColdFusion Applications




Specifying the CFC location
When you instantiate or invoke a component, you can specify the component name only, or you can specify a qualified
path. To specify a qualified path, separate the directory names with periods, not backslashes. For example,
myApp.cfcs.myComponent specifies the component defined in myApp\cfcs\myComponent.cfc. For additional
information, see "Saving and naming ColdFusion components" on page 189.

ColdFusion uses the following rules to find the specified CFC:

* If you use a cfinvoke or cfobject tag, or the CreateObject function, to access the CFC from a CFML page,
   ColdFusion searches directories in the following order:

   1 Local directory of the calling CFML page

   2 Web root

   3 Directories specified on the Custom Tag Paths page of ColdFusion Administrator

* If you specify only a component name, ColdFusion searches each of these directories, in turn, for the component.

* If you specify a qualified path, such as myApp.cfcs.myComponent, ColdFusion looks for a directory matching the
   first element of the path in each of these directories (in this example, myApp). If ColdFusion finds a matching
   directory, it looks for a file in the specified path beneath that directory, such as myApp\cfcs\myComponent.cfc,
   relative to each of these directories.

   Note: If ColdFusion finds a directory that matches the first path element, but does not find a CFC under that directory,
   ColdFusion returns a not found error and does not search for another directory.

* If you invoke a CFC method remotely, using a specific URL, a form field, Flash Remoting, or a web service
   invocation, ColdFusion looks in the specified path relative to the web root. For form fields and URLs that are
   specified directly on local web pages, ColdFusion also searches relative to the page directory.

   Note: On UNIX and Linux systems, ColdFusion attempts to match a CFC name or custom tag name with a filename,
   as follows: First, it attempts to find a file with the name that is all lowercase. If it fails, it tries to find a file whose case
   matches the CFML case. For example, if you specify <cfobject name="myObject" Component="myComponent">,
   ColdFusion first looks for mycomponent.cfc and, if it doesn't find it, ColdFusion looks for myComponent.cfc.


Passing parameters to methods
You pass parameters to a method in a CFC by using the cfinvoke tag, direct method invocations, or by passing
parameters in a URL.


Passing parameters to methods by using the cfinvoke tag
When you use the cfinvoke tag, ColdFusion provides several methods for passing parameters to CFC methods:

* As cfinvoke tag attributes, in name="value" format

* In the cfinvoke tag argumentcollection attribute

* In the cfinvoke tag body, using the cfinvokeargument tag

You can use any combination of these methods in a single invocation. If you use the same name in two or three of these
methods, ColdFusion uses the value based on the following order of precedence:

1 cfinvokeargument tags

2 cfinvoke attribute name-value pairs

3 argumentcollection arguments




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     197
Building Blocks of ColdFusion Applications




Passing parameters by using attribute format
You can pass parameters in the cfinvoke tag as tag attribute name-value pairs, as the following example shows:

<cfinvoke component="authQuery" method="getAuthSecure"
     lastName="#session.username#" pwd="#url.password#">

In the example, the parameters are passed as the lastName and pwd attributes.

Note: The cfinvoke tag attribute names are reserved and cannot be used for parameter names. The reserved attribute
names are: component, method, argumentCollection, and returnVariable. For more information, see the CFML
Reference.


Passing parameters in the argumentCollection attribute
If you save attributes to a structure, you can pass the structure directly using the cfinvoke tag's argumentCollection
attribute. This technique is useful if an existing structure or scope (such as the Forms scope) contains values that you
want to pass to a CFC as parameters, and for using conditional or looping code to create parameters.

When you pass an argumentCollection structure, each structure key is the name of a parameter inside the structure.

The following example passes the Form scope to the addUser method of the UserDataCFC component. In the method,
each form field name is a parameter name; the method can use the contents of the form fields to add a user to a
database.

<cfinvoke component="UserDataCFC" method="addUser" argumentCollection="#Form#">


Passing parameters by using the cfinvokeargument tag
To pass parameters in the cfinvoke tag body, use the cfinvokeargument tag. Using the cfinvokeargument tag, for
example, you can build conditional processing that passes a different parameter based on user input.

The following example invokes the corpQuery component:

<cfinvoke component="corpQuery" method="getEmp">
     <cfinvokeargument name="lastName" value="Wilder">
</cfinvoke>

The cfinvokeargument tag passes the lastName parameter to the component method.

In the following example, a form already let the user select the report to generate. After instantiating the getdata and
reports components, the action page invokes the doquery component instance, which returns the query results in
queryall. The action page then invokes the doreport component instance and uses the cfinvokeargument tag to
pass the query results to the doreport instance, where the output is generated.

<cfobject component="getdata" name="doquery">
<cfobject component="reports" name="doreport">
     <cfinvoke component="#doquery#" method="#form.whichreport#" returnvariable="queryall">
     <cfinvoke component="#doreport#"method="#form.whichreport#">
           <cfinvokeargument name="queryall" value="#queryall#">
     </cfinvoke>


Passing parameters in direct method invocations
ColdFusion provides three methods for passing parameters to CFC methods in direct method invocations:

1 You can pass the parameters the form of comma-separated name="value" entries, as in the following CFScript
   example:

    authorized = securityCFC.getAuth(name="Almonzo", Password="LauRa123");




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        198
Building Blocks of ColdFusion Applications




2 You can pass the parameters in an argumentCollection structure. The following code is equivalent to the
    previous example:

     argsColl = structNew();
    argsColl.username = "Almonzo";
    argsColl.password = "LauRa123";
    authorized = securityCFC.getAuth(argumentCollection = argsColl);

3 You can pass positional parameters to a method by separating them with commas. The following example calls the
    getAuth method, and passes the name and password as positional parameters:

     authorized = securityCFC.getAuth("Almonzo", "LauRa123");

Note: For more information on using positional parameters and component methods in ColdFusion functions, see
"Creating user-defined functions" on page 153.


Passing parameters in a URL
ColdFusion lets you pass parameters to CFC methods in a URL. To do so, you append the URL in standard URL query-
string, name-value pair syntax; for example:

 http://localhost:8500/CompanyQuery.cfc?method=getEmp&lastName=Adams



CFC variables and scope
CFCs interact with ColdFusion scopes and use local variables.

Note: Components also have a Super keyword that is sometimes called a scope. For information on the Super keyword,
see "Using the Super keyword" on page 202.


The This scope
The This scope is available within the CFC and is shared by all CFC methods. It is also available in the base component
(if the CFC is a child component), on the page that instantiates the CFC, and all CFML pages included by the CFC.

Inside the CFC, you define and access This scope variables by using the prefix This, as in the following line:

 <cfset This.color="green">

In the calling page, you can define and access CFC This scope variables by using the CFC instance name as the prefix.
For example, if you create a CFC instance named car and, within the car CFC specify <cfset
This.color="green">, a ColdFusion page that instantiates the CFC could refer to the component's color property as
#car.color#.

Variable values in the This scope last as long as the CFC instance exists and, therefore, can persist between calls to
methods of a CFC instance.

Note: The This scope identifier works like the This keyword in JavaScript and ActionScript. CFCs do not follow the Java
class model, and the This keyword behaves differently in ColdFusion than in Java. In Java, This is a private scope, whereas
in ColdFusion, it is a public scope.


The Variables scope
The Variables scope in a CFC is private to the CFC. It includes variables defined in the CFC body (initialization or
constructor code) and in the CFC methods. When you set Variables scope variables in the CFC, they cannot be seen
by pages that invoke the CFC.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     199
Building Blocks of ColdFusion Applications




The CFC Variables scope does not include any of the Variables scope variables that are declared or available in the page
that instantiates or invokes the CFC. However, you can make the Variables scope of the page that invokes a CFC
accessible to the CFC by passing Variables as an argument to the CFC method.

You set a Variables scope variable by assigning a value to a name that has the Variables prefix or no prefix.

Values in the Variables scope last as long as the CFC instance exists, and therefore can last between calls to CFC
instance methods.

The Variables scope is available to included pages, and Variables scope variables that are declared in the included page
are available in the component page.

Note: The Variables scope is not the same as the function local scope, which makes variables private within a function.
Always define function-local variables using the var keyword of the Local scope name.


Example: sharing the Variables scope
The following example shows how to make the Variables scope of the page that invokes a CFC accessible to the CFC by
passing Variables as an argument to the CFC method. It also illustrates that the Variables scope is private to the CFC.

The following code is for the callGreetMe.cfm page:

 <cfset Variables.MyName="Wilson">
<cfobject component="greetMe" name="myGreetings">
<cfoutput>
     Before invoking the CFC, Variables.Myname is: #Variables.MyName#.<br>
     Passing Variables scope to hello method. It returns:
#myGreetings.hello(Variables.MyName)#.<br>
After invoking the CFC, Variables.Myname is: #Variables.MyName#.<br>
</cfoutput>
<cfinvoke component="greetMe" method="VarScopeInCfc">

The following code is for the greetMe CFC:

 <cfcomponent>
<cfset Variables.MyName="Tuckerman">
     <cffunction name="hello">
           <cfargument name="Name" Required=true>
           <cfset Variables.MyName="Hello " & Arguments.Name>
           <cfreturn Variables.MyName>
     </cffunction>
     <cffunction name="VarScopeInCfc">
           <cfoutput>Within the VarScopeInCfc method, Variables.MyName is:
#variables.MyName#<br></cfoutput>
     </cffunction>
</cfcomponent>

In this example, the callGreetMe.cfm page does the following:

1 Sets the MyName variable in its Variables scope to Wilson.

2 Displays the Variables.MyName value.

3 Calls the greetMe CFC and passes its Variables scope as a parameter.

4 Displays the value returned by the greetMe CFC.

5 Displays the Variables.MyName value.

6 Invokes the VarScopeInCfc method, which displays the value of Variables.MyName within the CFC.

When you browse the callGreetMe.cfm page, the following appears:



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       200
Building Blocks of ColdFusion Applications




Before invoking the CFC, Variables.Myname is: Wilson.
Passing Variables scope to hello method. It returns: Hello Wilson.
After invoking the CFC, Variables.Myname is: Wilson.
Within the VarScopeInCfc method, Variables.MyName is: Tuckerman


The Arguments scope
The Arguments scope exists only in a method, and is not available outside the method. The scope contains the variables
that you passed into the method, including variables that you passed in the following ways:

* As named attributes to the cfinvoke tag

* In the cfargumentcollection attribute of the cfinvoke tag

* In cfinvokeargument tags

* As attributes or parameters passed into the method when the method is invoked as a web service, by Flash
   Remoting, as a direct URL, or by submitting a form

You can access variables in the Arguments scope using structure notation (Arguments.variablename), or array
notation (Arguments[1] or Arguments["variablename"]).

The Arguments scope does not persist between calls to CFC methods.

Variables in the Arguments scope are available to pages included by the method.


Other variable scopes
A CFC shares the Form, URL, Request, CGI, Cookie, Client, Session, Application, Server, and Flash scopes with the
calling page. Variables in these scopes are also available to all pages that are included by a CFC. These variables do not
have any behavior that is specific to CFCs.


Function local variables
Variables that you declare with the Var keyword inside a cffunction tag or CFScript function definition are available
only in the method in which they are defined, and only last from the time the method is invoked until it returns the
result. You cannot use the Var keyword outside function definitions.

Note: Always use the Var keyword or Local scope name on variables that are only used inside the function in which they
are declared.

Define all function local variables at the top of the function definition, before any other CFML code; for example:

<cffunction ...>
     <cfset Var testVariable = "this is a local variable">
           <!--- Function code goes here. --->
     <cfreturn myresult>
</cffunction>

Any arguments declared with the cfargument tag must appear before any variables defined with the cfset tag. You
can also place any cfscript tag first and define variables that you declare with the Var keyword in the script.

Use function local variables if you place the CFC in a persistent scope such as the Session scope, and the function has
data that must be freed when the function exits.

Local variables do not persist between calls to CFC methods.

Local variables are available to pages included by the method.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  201
Building Blocks of ColdFusion Applications




Using CFCs effectively
Several techniques let you effectively use CFCs in your applications:

* Structure and reuse code

* Build secure CFCs

* Use introspection to get information about components


Structuring and reusing code
Component inheritance and the Super keyword are two important tools for creating structured, object-oriented
ColdFusion components.

Component inheritance Lets you create a single base component and reuse this code in multiple subclasses that are
derived from the base component. Typically a base component is more general, and subcomponents are typically more
specific. Each subclass does not have to redefine the code in the base component, but can override it if necessary.

The Super keyword Lets a component that overrides a base component method execute the original base component
method. This technique lets your subclassed component override a method without losing the ability to call the
original version of the method.


Using component inheritance
Component inheritance lets you import component methods and properties from one component to another
component. Inherited components share any component methods or properties that they inherit from other
components, and ColdFusion initializes instance data in the parent CFC when you instantiate the CFC that extends it.

Component inheritance defines an is a relationship between components. For example, a component named
president.cfc inherits its methods and properties from manager.cfc, which inherits its methods and properties from
employee.cfc. In other words, president.cfc is a manager.cfc; manager.cfc is an employee.cfc; and president.cfc is an
employee.cfc.

In this example, employee.cfc is the base component; it's the component upon which the others are based. The
manager component extends the employee component; it has all the methods and properties of the employee
component, and some additional ones. The president component extends the manager component. The president
component is called a subcomponent or child component of the manager component, which, in turn, is a child
component of the employee component.

1 Create the employee.cfc file with the following content:

    <cfcomponent>
         <cfset This.basesalary=40*20>
   </cfcomponent>

2 Create the manager.cfc file with the following content:

    <cfcomponent extends="employee">
         <cfset This.mgrBonus=40*10>
   </cfcomponent>

   In the example, the cfcomponent tag's extends attribute points to the employee component.

3 Create the president.cfc file with the following content:

    <cfcomponent extends="manager">
         <cfset This.prezBonus=40*20>
   </cfcomponent>

   In the example, the cfcomponent tag's extends attribute points to the manager component.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      202
Building Blocks of ColdFusion Applications




4 Create the inherit.cfm file with the following content, and save it in the same directory as the components you
   created in the previous steps:

    <cfobject name="empObj" component="employee">
   <cfobject name="mgrObj" component="manager">
   <cfobject name="prezObj" component="president">
   <cfoutput>
         An employee's salary is #empObj.basesalary# per week.<br>
         A manager's salary is #mgrObj.basesalary + mgrObj.mgrBonus# per week.<br>
         A president's salary is #prezObj.basesalary + prezObj.mgrBonus +
              prezObj.PrezBonus# per week.
   </cfoutput>

When you browse the inherit.cfm file, the manager component refers to the basesalary defined in employee.cfc,
which is the base component; the president component refers to both the basesalary defined in the employee
component, and the mgrBonus defined in the manager component. The manager component is the parent class of the
president component.


Using the component.cfc file
All CFCs automatically extend the ColdFusion WEB-INF/cftags/component.cfc component. (The WEB-INF
directory is in the cf_root/wwwroot directory on ColdFusion configured with an embedded J2EE server. It is in the
cf_root directory when you deploy ColdFusion on a J2EE server.) This CFC is distributed as a zero-length file. You can
use it for any core methods or properties that you want all CFCs in your ColdFusion application server instance to
inherit.

Note: When you install a newer version of ColdFusion, the installation procedure replaces the existing component.cfc file
with a new version. Therefore, before upgrading, save any code that you have added to the component.cfc file, and then
copy the code into the new component.cfc file.


Using the Super keyword
You use the Super keyword only on CFCs that use the Extends attribute to extend another CFC. Unlike ColdFusion
scopes, the Super keyword is not used for variables; it is only used for CFC methods, and it is not available on
ColdFusion pages that invoke CFCs.

The Super keyword lets you refer to versions of methods that are defined in the CFC that the current component
extends. For example, the employee, manager, and president CFCs each contain a getPaid method. The manager CFC
extends the employee CFC. Therefore, the manager CFC can use the original versions of the overridden getPaid
method, as defined in the employee CFC, by prefixing the method name with Super.

1 Create the employee.cfc file with the following content:

    <cfcomponent>
         <cffunction name="getPaid" returntype="numeric">
              <cfset var salary=40*20>
              <cfreturn salary>
         </cffunction>
   </cfcomponent>

2 Create the manager.cfc file with the following content:

    <cfcomponent extends="employee">
         <cffunction name="getPaid" returntype="numeric">
              <cfset var salary=1.5 * Super.getPaid()>
              <cfreturn salary>
         </cffunction>
   </cfcomponent>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          203
Building Blocks of ColdFusion Applications




3 Create the president.cfc file with the following content:

    <cfcomponent extends="manager">
         <cffunction name="getPaid" returntype="numeric">
              <cfset var salary=1.5 * Super.getPaid()>
              <cfreturn salary>
         </cffunction>
   </cfcomponent>

4 Create the payday.cfm file with the following content, and save it in the same directory as the components that you
   created in the previous steps:

    <cfobject name="empObj" component="employee">
   <cfobject name="mgrObj" component="manager">
   <cfobject name="prezObj" component="president">
   <cfoutput>
         <cfoutput>
              An employee earns #empObj.getPaid()#.<br>
              A manager earns #mgrObj.getPaid()#.<br>
              The president earns #prezObj.getPaid()#.
         </cfoutput>
   </cfoutput>

In this example, each getPaid method in a child component invoked the getPaid method of its parent component.
The child's getPaid method then used the salary returned by the parent's getPaid method to calculate the
appropriate amount.

Included pages can use the Super keyword.

Note: The Super keyword supports only one level of inheritance. If you use multiple levels of inheritance, you can only use
the Super keyword to access the current component's immediate parent. The example in this section illustrates handling
this limitation by invoking methods in a chain.


Using component packages
Components stored in the same directory are members of a component package. Component packages help prevent
naming conflicts, and facilitate easy component deployment; for example:

* ColdFusion searches the current directory first for a CFC. If you place two components in a single directory as a package,
   and one component refers to the other with only the component name, not a qualified path, ColdFusion always searches
   the package directory first for the component. As a result, if you structure each application's components into a package,
   your applications can use the same component names without sharing the component code.

* If you use the access="package" attribute in a method's cffunction tag, access to the method is limited to
   components in the same package. Components in other packages cannot use this method, even if they specify it
   with a fully qualified component name. For more information on access security, see "Using access security" on
   page 204.


Invoke a packaged component method with the cfinvoke tag
1 In your web root directory, create a directory named appResources.

2 In the appResources directory, create a directory named components.

3 Copy the tellTime2.cfc file you created in "Invoking methods of a CFC instance" on page 191 and the
   getUTCTime.cfm file that you created in "Placing executable code in a separate file" on page 181 to the components
   directory.

4 Create the timeDisplay.cfm file with the following content and save it in your web root directory:



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     204
Building Blocks of ColdFusion Applications




    <!--- Create the component instance. --->
    <cfobject component="appResources.components.tellTime2" name="tellTimeObj">
    <!--- Invoke the methods. --->
    <cfinvoke component="#tellTimeObj#" method="getLocalTime"
         returnvariable="localTime" >
    <cfinvoke component="#tellTimeObj#" method="getUTCTime"
         returnvariable="UTCTime" >
    <!--- Display the results. --->
    <h3>Time Display Page</h3>
    <cfoutput>
         Server's Local Time: #localTime#<br>
         Calculated UTC Time: #UTCTime#
    </cfoutput>

   You use dot syntax to navigate directory structures. Place the directory name before the component name.

5 Browse the timeDisplay.cfm file in your browser.

The following example shows a CFScript invocation:

<cfscript>
helloCFC = createObject("component", "appResources.components.catQuery");
helloCFC.getSaleItems();
</cfscript>

The following example shows a URL invocation:

http://localhost/appResources/components/catQuery.cfc?method=getSalesItems


Using CFCs in persistent scopes
You can place a CFC instance in the Session or Application scope. This way, the component properties continue to
exist while the scope persists. For example, if you use a CFC for a shopping cart application, where the shopping cart
contents must persist for the length of the user's session. If you place the shopping cart CFC in the Session scope, you
can use component properties to store the cart contents. For example, the following line creates an instance of the
shoppingCart component in the Session scope:

<cfobject name="Session.myShoppingCart" component="shoppingCart">

Code that manipulates persistent scope CFC properties must be locked, just as all other code that manipulates
persistent scope properties must be locked. Therefore, lock both of the following types of application code:

* Code that directly manipulates properties of a persistent scope CFC instance

* Code that calls methods of a persistent scope CFC instance that manipulate properties of the instance

If you place multiple CFC instances in a single persistent scope, you can create a named lock for each CFC instance.
For more information on locking, see "Using Persistent Data and Locking" on page 301.

Note: Session scope CFCs cannot be serialized, so you cannot use them with clustered sessions; for example, if you want
to support session failover among servers.


Building secure ColdFusion components
To restrict access to component methods, ColdFusion components use access, role-based, or programmatic security.


Using access security
CFC access security lets you limit the code that can access the components. You specify the access to a CFC method
by specifying the cffunctionaccess attribute, as follows:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                       205
Building Blocks of ColdFusion Applications




 Type                     Description

 private                  Available only to the component that declares the method and any components that extend the component in
                          which it is defined. This usage is like the Java protected keyword, not the Java private keyword.

 package                  Available only to the component that declares the method, components that extend the component, or any other
                          components in the package. A package consists of all components defined in a single directory. For more
                          information on packages, see "Using component packages" on page 203.

 public                   Available to any locally executing ColdFusion page or component method.

 remote                   Available to a locally or remotely executing ColdFusion page or component method, or to a local or remote client
                          through a URL, form submission, Flash Remoting, or as a web service.



Using role-based security
If you specify a roles attribute in a cffunction tag, only users who are logged in with one of the specified roles can
execute the method. When a user tries to invoke a method without authorization, an exception is returned.

The following example creates a component method that deletes files:

<cfcomponent>
      <cffunction
              name="deleteFile" access="remote" roles="admin,manager" output="no">
           <cfargument name="filepath" required="yes">
           <cffile action="DELETE" file=#arguments.filepath#>
      </cffunction>
</cfcomponent>

In the example, the cffunction tag includes the roles attribute to specify the user roles allowed to access it. In this
example, only users in the role admin and manager can access the function. Notice that multiple roles are delimited by
a comma.

For information on ColdFusion security, including the cflogin tag and role-based security in ColdFusion, see
"Securing Applications" on page 339.


Using programmatic security
You can implement your own security within a method to protect resources. For example you can use the ColdFusion
function IsUserInAnyRole to determine if a user is in particular role, as the following example shows:

<cffunction name="foo">
      <cfif IsUserInRole("admin")>
           ... do stuff allowed for admin
      <cfelseif IsUserInRole("user")>
           ... do stuff allowed for user
      <cfelse>
           <cfoutput>unauthorized access</cfoutput>
           <cfabort>
      </cfif>
</cffunction>


Using introspection to get information about components
ColdFusion provides several ways for you to get information about components:

* Request a component page from the browser

* Use the ColdFusion component browser

* Use the Adobe Dreamweaver Components panel



                                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  206
Building Blocks of ColdFusion Applications




* Use the GetMetaData function

Development teams can use the information about components as up-to-date API reference information.

Note: For information about how to include documentation in CFCs for display by using introspection, see "Documenting
CFCs" on page 187.


Requesting a component page from the browser
When you access a CFC directly with a web browser without specifying a component method, the following chain of
events occurs:

1 The request is redirected to the cfcexplorer.cfc file, which is located in the
   cf_root/wwwroot/CFIDE/componentutils directory.

2 The cfcexplorer component prompts users for the ColdFusion RDS or Administrator password, if necessary.

3 The cfcexplorer component renders an HTML description and returns it to the browser.


Using the ColdFusion component browser
You can also browse the components available in ColdFusion using the component browser, which is located at
cf_root/wwwroot/CFIDE/componentutils/componentdoc.cfm.

The browser has three panes:

* The upper-left pane lists all CFC packages that ColdFusion can access, and has all components and refresh links.

* The lower-left pane lists CFC component names. When the browser first appears, or when you click the all
   components link in the upper pane, the lower pane lists all available components. If you click a package name in
   the upper left pane, the lower pane lists only the components in the package.

* The right pane initially lists the paths of all components. When you click a component name in the lower-left pane,
   the right pane shows the ColdFusion introspection page, as described in "Requesting a component page from the
   browser" on page 206.

Note: When RDS user names are enabled, the component browser accepts the root administrator user (admin) with either
the administrator or RDS single password.


Using the Dreamweaver Components panel
The Dreamweaver Components panel lists all available components, including their methods, method parameters,
and properties. The panel's context menu includes options to create a component, edit the selected component, insert
code to invoke the component, or show detailed information on the component or component element. The Get
description option shows the ColdFusion introspection page, as described in "Requesting a component page from the
browser" on page 206. For more information on viewing and editing CFCs in Dreamweaver, see the Dreamweaver
online Help.


Using the GetMetaData function
The CFML GetMetaData function returns a structure that contains all the metadata of a CFC instance. This structure
contains substantially more data about the CFC than the cfdump tag shows, and includes the following information:

* All attributes to the component tag, including any metadata-only attributes, plus the component path.

* An array of structures that contains complete information on each method (function) in the component. This
   information describes all attributes, including metadata-only function and parameter attributes.

* Within each function structure, a Parameters element that contains an array of parameters specified by cfargument
   tags. Information on each parameter includes any metadata-only attributes.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   207
Building Blocks of ColdFusion Applications




* Information about any properties that are specified using the cfproperty tag.


Display metadata for a CFC
1 Create the tellAboutCfcs.cfm file in the same directory as the telltime.cfc file, with the following code:

    <!--- Create an instance of the component. --->
   <cfobject component="tellTime" name="tellTimeObj">
   <!--- Create a structure. --->
   <cfset aboutcfc=structNew()>
   <!--- Populate the structure with the metadata for the
                    tellTimeObj instance of the tellTime CFC. --->
   <cfset aboutcfc=GetMetaData(tellTimeObj)>
   <cfdump var="aboutcfc">

2 View the tellAboutCfcs.cfm file in a browser.

For information on how to specify CFC metadata, including how to use component tags and how to specify metadata-
only attributes, see "Documenting CFCs" on page 187.


ColdFusion component example
Several code examples in the Developing ColdFusion Applications reuse code, particularly queries. To illustrate the
advantages of CFCs, these examples invoke the appropriate method in the CFC that appears in the following example.
Although Adobe recommends using CFCs to create structured, reusable code, some code examples in this manual
contain queries within a CFML page, rather than invoking a CFC, in order to clearly illustrate a particular element of
ColdFusion.

<cfcomponent>
     <cffunction name="allemployees" access="public" output="false"
           returntype="query">
           <cfset var getNames="">
           <cfquery name="getNames" datasource="cfdocexamples">
                SELECT * FROM Employee
           </cfquery>
     </cffunction>


     <cffunction name="namesalarycontract" access="public" output="false"
           returntype="query">
           <cfset var EmpList="">
           <cfquery name="EmpList" datasource="cfdocexamples">
                SELECT Firstname, Lastname, Salary, Contract
                FROM Employee
           </cfquery>
     </cffunction>


     <cffunction name="fullname" access="public" output="false"
           returntype="query">
           <cfset var engquery="">
           <cfquery name="engquery" datasource="cfdocexamples">
                SELECT FirstName || ' ' || LastName AS FullName
                FROM Employee
           </cfquery>
     </cffunction>


     <cffunction name="bydept" access="public" output="false" returntype="query">
           <cfset var deptquery="">



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               208
Building Blocks of ColdFusion Applications




           <cfquery name="deptquery" datasource="cfdocexamples">
                SELECT Dept_ID, FirstName || ' ' || LastName
                AS FullName
                FROM Employee
                ORDER BY Dept_ID
           </cfquery>
     </cffunction>


     <cffunction name="employeebyURLID" access="public" output="false"
           returntype="query">
           <cfset var GetRecordtoUpdate="">
           <cfquery name="GetRecordtoUpdate" datasource="cfdocexamples">
                SELECT * FROM Employee
                WHERE Emp_ID = #URL.Emp_ID#
           </cfquery>
     </cffunction>


     <cffunction name="deleteemployee" access="public" output="false"
           returntype="void">
           <cfset var DeleteEmployee="">
           <cfquery name="DeleteEmployee" datasource="cfdocexamples">
                DELETE FROM Employee
                WHERE Emp_ID = #Form.Emp_ID#
           </cfquery>
     </cffunction>


     <cffunction name="distinctlocs"access="public" output="false"
           returntype="query">
           <cfset var GetDepartments="">
           <cfquery name="GetDepartments" datasource="cfdocexamples">
                SELECT DISTINCT Location
                FROM Departmt
           </cfquery>
     </cffunction>
</cfcomponent>




Creating and Using Custom CFML Tags

You can extend CFML by creating and using custom CFML tags that encapsulate common code.


Creating custom tags
Custom tags let you extend CFML by adding your own tags to the ones supplied with ColdFusion. After you define a
custom tag, you can use it on a ColdFusion page just as you would any of the standard CFML tags, such as cfquery
and cfoutput.

You use custom tags to encapsulate your application logic so that it can be referenced from any ColdFusion page.
Custom tags allow for rapid application development and code reuse while offering off-the-shelf solutions for many
programming chores.

For example, you can create a custom tag, named cf_happybirthday, to generate a birthday message. You could then
use that tag in a ColdFusion page, as follows:

<cf_happybirthday name="Ted Cantor" birthDate="December 5, 1987">




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       209
Building Blocks of ColdFusion Applications




When ColdFusion processes the page containing this tag, it could output the message:

 December 5, 1987 is Ted Cantor's Birthday.
Please wish him well.

A custom tag can also have a body and end tag, for example:

 <cf_happybirthdayMessge name="Ellen Smith" birthDate="June 8, 1993">
     <p> Happy Birthday Ellen!</p>
     <p> May you have many more!</p>
</cf_happybirthdayMessge>

This tag could output the message:

 June 8, 1993 is Ellen Smith's Birthday.
Happy Birthday Ellen!
May you have many more!

For more information about using end tags, see "Handling end tags" on page 217.


Creating and calling custom tags
You implement a custom tag with a single ColdFusion page. You then call the custom tag from a ColdFusion page by
inserting the prefix cf_ before the page's filename. The page that references the custom tag is referred to as the calling
page.

1 Create a ColdFusion page, the custom tag page, that shows the current date:

    <cfoutput>#DateFormat(Now())#</cfoutput>

2 Save the file as date.cfm.

3 Create a ColdFusion page, the calling page, with the following content:

    <html>
   <head>
         <title>Date Custom Tag</title>
   </head>
   <body>


         <!--- Call the custom tag defined in date.cfm --->
         <cf_date>


   </body>
   </html>

4 Save the file as callingdate.cfm.

5 View callingdate.cfm in your browser.

   This custom tag returns the current date in the format DD-MMM-YY.

As you can see from this example, creating a custom tag in CFML is no different from writing any ColdFusion page.
You can use all CFML constructs, as well as HTML. You are free to use any naming convention that fits your
development practice. Unique descriptive names make it easy for you and others to find the right tag.

Note: Although tag names in ColdFusion pages are not case sensitive, custom tag filenames must be lowercase on UNIX.


Storing custom tag pages
You must store custom tag pages in any one of the following:

* The same directory as the calling page



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                      210
Building Blocks of ColdFusion Applications




* The cfusion\CustomTags directory

* A subdirectory of the cfusion\CustomTags directory

* A directory that you specify in the ColdFusion Administrator

To share a custom tag among applications in multiple directories, place it in the cfusion\CustomTags directory. You
can create subdirectories to organize custom tags. ColdFusion searches recursively for the Custom Tags directory,
stepping down through any existing subdirectories until the custom tag is found.

You can have a situation where you have multiple custom tags with the same name. To guarantee which tag
ColdFusion calls, copy it to the same directory as the calling page. Or, use the cfmodule tag with the template
attribute to specify the absolute path to the custom tag. For more information on cfmodule, see the next section.


Calling custom tags with the cfmodule tag
You can also use the cfmessagebox tag to call custom tags if you want to specify the location of the custom tag page.
The cfmodule tag is useful if you are concerned about possible name conflicts when using a custom tag, or if the
application must use a variable to dynamically call a custom tag at runtime.

Use either a template or name attribute in the tag, but you cannot use both. The following table describes the basic
cfmodule attributes:


 Attribute                Description

 template                 Required if the name attribute is not used. Same as the template attribute in cfinclude. This attribute:

                          * Specifies a path relative to the directory of the calling page.

                          * If the path value is prefixed with "/", ColdFusion searches directories explicitly mapped in the ColdFusion
                            Administrator for the included file.

                          Example: <cfmodule template="../MyTag.cfm"> identifies a custom tag file in the parent directory.

 name                     Required if the template attribute is not used. Use period-separated names to uniquely identify a subdirectory
                          under the CustomTags root directory.

                          Example: <cfmodule name="MyApp.GetUserOptions"> identifies the file GetUserOptions.cfm in the
                          CustomTags\MyApp directory under the ColdFusion root directory.

 attributes               The custom tag's attributes.


For example, the following code specifies to execute the custom tag defined by the mytag.cfm page in the parent
directory of the calling page:

<cfmodule template="../mytag.cfm">

For more information on using the cfmessagebox tag, see the CFML Reference.


Calling custom tags with the cfimport tag
You can use the cfimport tag to import custom tags from a directory as a tag library. The following example imports
the tags from the directory myCustomTags:

<cfimport prefix="mytags" taglib="myCustomTags">

Once imported, you call the custom tags using the prefix that you set when importing, as the following example shows:

<mytags:customTagName>

where customTagName corresponds to a ColdFusion application page named customTagName.cfm. If the tag takes
attributes, you include them in the call:

<mytags:custom_tag_name attribute1=val_1 attribute2=val_2>



                                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      211
Building Blocks of ColdFusion Applications




You can also include end tags when calling your custom tags, as the following example shows:

<mytags:custom_tag_name attribute1=val_1 attribute2=val_2>
...
</mytags:custom_tag_name>

ColdFusion calls the custom tag page twice for a tag that includes an end tag: once for the start tag and once for the
end tag. For more information on how ColdFusion handles end tags, and how to write your custom tags to handle
them, see "Handling end tags" on page 217.

One of the advantages to using the cfimport tag is that you can define a directory structure for your custom tags to
organize them by category. For example, you can place all security tags in one directory, and all interface tags in
another. You then import the tags from each directory and give them a different prefix:

<cfimport prefix="security" taglib="securityTags">
<cfimport prefix="ui" taglib="uiTags">
...
<security:validateUser name="Bob">
...
<ui:greeting name="Bob">
...

Reading your code becomes easier because you can identify the location of your custom tags from the prefix.


Securing custom tags
The ColdFusion security framework enables you to selectively restrict access to individual tag files and tag directories.
This feature can be an important safeguard in team development. For details, see Configuring and Administering
ColdFusion.


Accessing existing custom tags
Before creating a custom tag in CFML, review the free and commercial custom tags available on the Adobe developer's
exchange (www.adobe.com/go/learn_cfu_cfdevcenter_en).You might find a tag that does what you want.

Tags are grouped in several broad categories and are downloadable as freeware, shareware, or commercial software.
You can view each tag's syntax and usage information. The gallery contains a wealth of background information on
custom tags and an online discussion forum for tag topics.

Tag names with the cf_ preface are CFML custom tags; those tags with the cfx_ preface are ColdFusion extensions
written in Java or C++. For more information about the CFX tags, see "Building Custom CFXAPI Tags" on page 224.

If you do not find a tag that meets your specific needs, you can create your own custom tags in CFML.


Passing data to custom tags
To make your custom tags flexible, you often pass data to them for processing. To do so, you write custom tags that
take tag attributes and other data as input from a calling page.


Passing values to and from custom tags
Because custom tags are individual ColdFusion pages, variables and other data are not automatically shared between
a custom tag and the calling page. To pass data from the calling page to the custom tag, you can specify attribute name-
value pairs in the custom tag, just as you do for normal HTML and CFML tags.

For example, to pass the value of the NameYouEntered variable to the cf_getmd tag, you can call the custom tag as
follows:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         212
Building Blocks of ColdFusion Applications




 <cf_getmd Name=#NameYouEntered#>

To pass multiple attributes to a custom tag, separate them with a space in the tag as follows:

 <cf_mytag Firstname="Thadeus" Lastname="Jones">

In the custom tag, you use the Attributes scope to access attributes passed to the tag. Therefore, in the getmd.cfm page,
you access the passed attribute as Attributes.Name. The mytag.cfm custom tag page refers to the passed attributes as
Attributes.Firstname and Attributes.Lastname.

The custom tag page can also access variables set in the calling page by prefixing the calling page's local variable with
Caller. However, this technique is not the best way to pass information to a custom tag, because each calling page
would be required to create variables with the names required by the custom tag. You can create more flexible custom
tags by passing parameters using attributes.

Variables created within a custom tag are deleted when the processing of the tag terminates. Therefore, if you want to
pass information back to the calling page, write that information back to the Caller scope of the calling page. You
cannot access the custom tag's variables outside the custom tag itself.

For example, use the following code in the getmd.cfm page to set the variable Doctor on the calling page:

 <cfset Caller.Doctor="Doctor " & Attributes.Name>

If the variable Doctor does not exist in the calling page, this statement creates it. If the variable exists, the custom tag
overwrites it.

The following image shows the relationship between the variables on the calling page and the custom tag:


 <cfset NameYouEntered="Smith">

 <cf_getmd Name=#NameYouEntered#>
                                                     <cfset Caller.Doctor="Doctor " & Attributes.Name>

 <cfoutput>
 You are now #Variables.Doctor#.<br>
 </cfoutput>


A. calling page B. getmd.cfm


One common technique used by custom tags is for the custom tag to take as input an attribute that contains the name
of the variable to use to pass back results. For example, the calling page passes returnHere as the name of the variable
to use to pass back results:

 <cf_mytag resultName="returnHere">

In mytag.cfm, the custom tag passes back its results using the following code:

 <cfset "Caller.#Attributes.resultName#" = result>

Be careful not to overwrite variables in the calling page from the custom tag. Adopt a naming convention to minimize
the chance of overwriting variables. For example, prefix the returned variable with customtagname_, where
customtagname is the name of the custom tag.

Note: Data that pertains to the HTTP request or to the current application is visible in the custom tag page. This data
includes the variables in the Form, URL, Cgi, Request, Cookies, Server, Application, Session, and Client scopes.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         213
Building Blocks of ColdFusion Applications




Using tag attributes summary
Custom tag attribute values are passed from the calling page to the custom tag page as name-value pairs. CFML custom
tags support required and optional attributes. Custom tag attributes conform to the following CFML coding standards:

* ColdFusion passes any attributes in the Attributes scope.

* Use the Attributes.attribute_namesyntax when referring to passed attributes to distinguish them from custom
   tag page local variables.

* Attributes are not case sensitive.

* Attributes can be listed in any order within a tag.

* in the tag invocation, a space must separate attribute name-value pairs.

* Passed values that contain spaces must be enclosed in double-quotation marks.

* Use the cfparam tag with a default attribute at the top of a custom tag to test for and assign defaults for optional
   attributes that are passed from a calling page. For example:

    <!--- The value of the variable Attributes.Name comes from the calling page. If
         the calling page does not set it, make it "Who". --->
   <cfparam name="Attributes.Name" default="Who">

* Use the cfparam tag or a cfif tag with an IsDefined function at the top of a custom tag to test for required
   attributes that must be passed from a calling page; for example, the following code issues an abort if the user does
   not specify the Name attribute to the custom tag:

    <cfif not IsDefined("Attributes.Name")>
         <cfabort showError="The Name attribute is required.">
   </cfif>


Custom tag example with attributes
The following example creates a custom tag that uses an attribute that is passed to it to set the value of a variable called
Doctor on the calling page.

1 Create a ColdFusion page (the calling page) with the following content:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                214
Building Blocks of ColdFusion Applications




    <html>
   <head>
         <title>Enter Name</title>
   </head>
   <body>
   <!--- Enter a name, which could also be done in a form. --->
   <!--- This example simply uses a cfset. --->
   <cfset NameYouEntered="Smith">


   <!--- Display the current name. --->
   <cfoutput>
   Before you leave this page, you're #Variables.NameYouEntered#.<br>
   </cfoutput>


   <!--- Go to the custom tag. --->
   <cf_getmd Name="#NameYouEntered#">
   <!--- Come back from the Custom tag --->


   <!--- Display the results of the custom tag. --->
   <cfoutput>
   You are now #Variables.Doctor#.<br>
   </cfoutput>
   </body>
   </html>

2 Save the page as callingpage.cfm.

3 Create another page (the custom tag) with the following content:

    <!--- The value of the variable Attributes.Name comes from the calling page.
         If the calling page does not set it, make it "Who". --->
   <cfparam name="Attributes.Name" default="Who">


   <!--- Create a variable called Doctor, make its value "Doctor "
         followed by the value of the variable Attributes.Name.
         Make its scope Caller so it is passed back to the calling page.
   --->
   <cfset Caller.Doctor="Doctor " & Attributes.Name>

4 Save the page as getmd.cfm.

5 Open the file callingpage.cfm in your browser.

The calling page uses the getmd custom tag and displays the results.


Reviewing the code
The following table describes the code and its function:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                215
Building Blocks of ColdFusion Applications




 Code                                                         Description

 <cfset NameYouEntered="Smith">                               In the calling page, create a variable NameYouEntered and assign
                                                              it the value Smith.

 <cfoutput>                                                   In the calling page, display the value of the NameYouEntered
 Before you leave this page, you're                           variable before calling the custom tag.
 #Variables.NameYouEntered#.<br>
 </cfoutput>

 <cf_getmd Name="#NameYouEntered#">                           In the calling page, call the getmd custom tag and pass it the Name
                                                              attribute whose value is the value of the local variable
                                                              NameYouEntered.

 <cfparam name="Attributes.Name" default="Who">               The custom tag page normally gets the Name variable in the
                                                              Attributes scope from the calling page. Assign it the value Who if
                                                              the calling page did not pass an attribute.

 <cfset Caller.Doctor="Doctor " & Attributes.Name>            In the custom tag page, create a variable called Doctor in the
                                                              Caller scope so it exists in the calling page as a local variable.

                                                              Set its value to the concatenation of the string "Doctor" and the
                                                              value of the Attributes.Name variable.

 <cfoutput>                                                   In the calling page, display the value of the Doctor variable
 You are now #Variables.Doctor#.<br>                          returned by the custom tag page. (This example uses the Variables
 </cfoutput>
                                                              scope prefix to emphasize the fact that the variable is returned as a
                                                              local variable.)



Passing custom tag attributes by using CFML structures
You can use the reserved attribute attributeCollection to pass attributes to custom tags using a structure. The
attributeCollection attribute must reference a structure containing the attribute names as the keys and the
attribute values as the values. You can freely mix attributeCollection with other attributes when you call a custom
tag.

The key-value pairs in the structure specified by the attributeCollection attribute get copied into the Attributes
scope of the custom tag pages. This action has the same effect as specifying the attributeCollection entries as
individual attributes when you call the custom tag. The custom tag page refers to the attributes passed using
attributeCollection the same way as it does other attributes; for example, as Attributes.CustomerName or
Attributes.Department_number.

Note: You can combine tag attributes and the attributeCollection attribute when you use a custom tag directly or
when you use the cfmodule tag to invoke a custom tag. If you pass an attribute with the same name both explicitly and
in the attributeCollection structure, ColdFusion passes only the tag attribute to the custom tag and ignores the
corresponding attribute from the attribute collection. You cannot combine tag attributes and the
attributeCollection attribute when you use standard (built in) ColdFusion tags.

Custom tag processing reserves the attributeCollection attribute for the structure holding a collection of custom
tag attributes. If attributeCollection does not reference such a collection, ColdFusion generates a template
exception.

The following example uses an attributeCollection attribute to pass two of four attributes:

 <cfset zort=StructNew()>
<cfset zort.x = "-X-">
<cfset zort.y = "-Y-">
<cf_testtwo a="blab" attributeCollection=#zort# foo="16">

If testtwo.cfm contains the following code:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     216
Building Blocks of ColdFusion Applications




 ---custom tag ---<br>
<cfoutput>#attributes.a# #attributes.x# #attributes.y#
     #attributes.foo#</cfoutput><br>
--- end custom tag ---

its output is the following statement:

 ---custom tag ---
blab -X- -Y- 16
--- end custom tag ---

One use for attributeCollection is to pass the entire Attributes scope of one custom tag to another. This technique
is useful when you have one custom tag that calls a second custom tag and you want to pass all attributes from the first
tag to the second.

For example, you call a custom tag with the following code:

 <cf_first attr1="foo" attr2="bar">

To pass all the attributes of the first custom tag to the second, you include the following statement in first.cfm:

 <cf_second attributeCollection="#attributes#">

Within the body of second.cfm, you reference the parameters passed to it as follows:

 <cfoutput>#attributes.attr1#</cfoutput>
<cfoutput>#attributes.attr2#</cfoutput>



Managing custom tags
If you deploy custom tags in a multideveloper environment or distribute your tags publicly, you can use the advanced
security and template encoding capabilities of ColdFusion.

The ColdFusion security framework enables you to selectively restrict access to individual tags or to tag directories.
This restriction can be an important safeguard in team development. For more information, see "Securing
Applications" on page 339.

You can use the command line utility cfcompile to precompile your custom tag files into Java class files or byte code.
For more information, see Using the cfcompile utility in Configuring and Administering ColdFusion.


Executing custom tags
ColdFusion provides techniques for executing custom tags, including handling end tags and processing body text.


Accessing tag instance data
When a custom tag page executes, ColdFusion keeps data related to the tag instance in the thisTag structure. You can
access the thisTag structure from within your custom tag to control processing of the tag. The behavior is like that of
the File tag-specific variable (sometimes called the File scope).

ColdFusion generates the variables in the following table and writes them to the thisTag structure:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        217
Building Blocks of ColdFusion Applications




 Variable                    Description

 ExecutionMode               Contains the execution mode of the custom tag. Valid values are "start", "end", and "inactive".

 HasEndTag                   Distinguishes between custom tags that are called with and without end tags. Used for code validation. If the
                             user specifies an end tag, HasEndTag is set to true; otherwise, it is set to false.

 GeneratedContent            Specifies the content that the tag generates. This content includes anything in the body of the tag, including
                             the results of any active content, such as ColdFusion variables and functions. You can process this content as
                             a variable.

 AssocAttribs                Contains the attributes of all nested tags if you use cfassociate to make them available to the parent tags.
                             For more information, see "High-level data exchange" on page 221.


The following example accesses the ExecutionMode variable of the thisTag structure from within a custom tag:

<cfif thisTag.ExecutionMode is 'start'>


Handling end tags
The preceding examples of custom tags reference a custom tag by using just a start tag:

<cf_date>

In this case, ColdFusion calls the custom tag page date.cfm to process the tag.

However, you can create custom tags that have both a start and an end tag. For example, the following tag has both a
start and an end tag:

<cf_date>
     ...
</cf_date>

ColdFusion calls the custom tag page date.cfm twice for a tag that includes an end tag: once for the start tag and once
for the end tag. As part of the date.cfm page, you can determine if the call is for the start or end tag, and perform the
appropriate processing.

ColdFusion also calls the custom tag page twice if you use the shorthand form of an end tag:

<cf_date/>

You can also call a custom tag using the cfmodule tag, as shown in the following example:

<cfmodule ...>
     ...
</cfmodule>

If you specify an end tag to cfmessagebox, then ColdFusion calls your custom tag as if it had both a start and an end tag.


Determining if an end tag is specified
You can write a custom tag that requires users to include an end tag. If a tag must have an end tag provided, you can
use thisTag.HasEndTag in the custom tag page to verify that the user included the end tag.

For example, in date.cfm, you could include the following code to determine whether the end tag is specified:

<cfif thisTag.HasEndTag is 'False'>
     <!--- Abort the tag--->
     <cfabort showError="An end tag is required.">
</cfif>




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         218
Building Blocks of ColdFusion Applications




Determining the tag execution mode
The variable thisTag.ExecutionMode contains the mode of invocation of a custom tag page. The variable has one of
the following values:

Start Mode for processing the end tag.

End Mode for processing the end tag.

Inactive Mode when the custom tag uses nested tags. For more information, see "Nesting custom tags" on page 219.

If an end tag is not explicitly provided, ColdFusion invokes the custom tag page only once, in Start mode.

A custom tag page named bold.cfm that makes text bold could be written as follows:

 <cfif thisTag.ExecutionMode is 'start'>
     <!--- Start tag processing --->
     <B>
<cfelse>
     <!--- End tag processing --->
     </B>
</cfif>

You then use this tag to convert the text to bold:

 <cf_bold>This is bold text</cf_bold>

You can also use cfswitch to determine the execution mode of a custom tag:

 <cfswitch expression=#thisTag.ExecutionMode#>
     <cfcase value= 'start'>
           <!--- Start tag processing --->
     </cfcase>
     <cfcase value='end'>
           <!--- End tag processing --->
     </cfcase>
</cfswitch>


Considerations when using end tags
How you code your custom tag to divide processing between the start tag and end tag depends greatly on the function
of the tag. However, use the following rules to help you make your decisions:

* Use the start tag to validate input attributes, set default values, and validate the presence of the end tag if the custom
   tag requires it.

* Use the end tag to perform the actual processing of the tag, including any body text passed to the tag between the
   start and end tags. For more information on body text, see "Processing body text" on page 218.

* Perform output in either the start or end tag; do not divide it between the two tags.


Processing body text
Body text is any text that you include between the start and end tags when you call a custom tag, for example:

 <cf_happybirthdayMessge name="Ellen Smith" birthDate="June, 8, 1993">
     <p> Happy Birthday Ellen!</p>
     <p> May you have many more!</p>
</cf_happybirthdayMessge>

In this example, the two lines of code after the start tag are the body text.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   219
Building Blocks of ColdFusion Applications




You can access the body text within the custom tag using the thisTag.GeneratedContent variable. The variable
contains all body text passed to the tag. You can modify this text during processing of the tag. The contents of the
thisTag.GeneratedContent variables are returned to the browser as part of the tag's output.

The thisTag.GeneratedContent variable is always empty during the processing of a start tag. Any output generated
during start tag processing is not considered part of the tag's generated content.

A custom tag can access and modify the generated content of any of its instances using the
thisTag.GeneratedContent variable. In this context, the term generated content means the results of processing the
body of a custom tag. The content includes all text and HTML code in the body, the results of evaluating ColdFusion
variables, expressions, and functions, and the results generated by descendant tags. Any changes to the value of this
variable result in changes to the generated content.

As an example, consider a tag that comments out the HTML generated by its descendants. Its implementation could
look as follows:

 <cfif thisTag.ExecutionMode is 'end'>
     <cfset thisTag.GeneratedContent ='<!--#thisTag.GeneratedContent#-->'>
</cfif>


Terminating tag execution
Within a custom tag, you typically perform error checking and parameter validation. As part of those checks, you can
choose to abort the tag, using cfabort, if a required attribute is not specified or other severe error is detected.

The cfexit tag also terminates execution of a custom tag. However, the cfexit tag is designed to give you more
flexibility when coding custom tags than cfabort. The cfexit tag's method attribute specifies where execution
continues. The cfexit tag can specify that processing continues from the first child of the tag or continues
immediately after the end tag marker.

You can also use the method attribute to specify that the tag body executes again. This capability enables custom tags
to act as high-level iterators, emulating cfloop behavior.

The following table summarizes cfexit behavior:


 Method attribute value                    Location of cfexit call                Behavior

 ExitTag (default)                          Base page                             Acts like cfabort

                                           ExecutionMode=start                    Continue after end tag

                                           ExecutionMode=end                      Continue after end tag

 ExitTemplate                               Base page                             Acts like cfabort

                                           ExecutionMode=start                    Continue from first child in body

                                           ExecutionMode=end                      Continue after end tag

 Loop                                       Base page                             Error

                                           ExecutionMode=start                    Error

                                           ExecutionMode=end                      Continue from first child in body



Nesting custom tags
A custom tag can call other custom tags from within its body text, thereby nesting tags. ColdFusion uses nested tags
such as cfgraph and cfgraphdata, cfhttp and cfhttpparam, and cftree and cftreeitem. The ability to nest tags
allows you to provide similar functionality.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     220
Building Blocks of ColdFusion Applications




The following example shows a cftreeitem tag nested within a cftree tag:

<cftree name="tree1"
      required="Yes"
      hscroll="No">
      <cftreeitem value=fullname
           query="engquery"
           queryasroot="Yes"
           img="folder,document">
</cftree>

The calling tag is known as an ancestor, parent, or base tag; the tags that ancestor tags call are known as descendant,
child, or sub tags. Together, the ancestor and all descendant tags are called collaborating tags.

In order to nest tags, the parent tag must have a closing tag.

The following table lists the terms that describe the relationships between nested tags:


 Calling tag          Tag nested within the calling tag  Description

 Ancestor             Descendant                         An ancestor is any tag that contains other tags between its start and end tags.
                                                         A descendant is any tag called by a tag.

 Parent               Child                              Parent and child are synonyms for ancestor and descendant.

 Base tag             Sub tag                            A base tag is an ancestor that you explicitly associate with a descendant,
                                                         called a sub tag, with cfassociate.


You can create multiple levels of nested tags. In this case, the sub tag becomes the base tag for its own sub tags. Any
tag with an end tag present can be an ancestor to another tag.

Nested custom tags operate through three modes of processing, which are exposed to the base tags through the variable
thisTag.ExecutionMode.


Passing data between nested custom tags
A key custom tag feature is for collaborating custom tags to exchange complex data without user intervention, while
encapsulating each tag's implementation so that others cannot see it.

When you use nested tags, address the following issues:

* What data must be accessible?

* Which tags can communicate to which tags?

* How are the source and targets of the data exchange identified?

* What CFML mechanism is used for the data exchange?


What data is accessible?
To enable developers to obtain maximum productivity in an environment with few restrictions, CFML custom tags
can expose all their data to collaborating tags.

When you develop custom tags, document all variables that collaborating tags can access and/or modify. When your
custom tags collaborate with other custom tags,

make sure that they do not modify any undocumented data.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        221
Building Blocks of ColdFusion Applications




To preserve encapsulation, place all tag data access and modification operations in custom tags. For example, rather
than documenting that the variable MyQueryResults in a tag's implementation holds a query result and expecting
users to manipulate MyQueryResults directly, create a nested custom tag that manipulates MyQueryResult. This
technique protects the users of the custom tag from changes in the tag's implementation.


Variable scopes and special variables
Use the Request scope for variables in nested tags. The Request scope is available to the base page, all pages it includes,
all custom tag pages it calls, and all custom tag pages called by the included pages and custom tag pages. Collaborating
custom tags that are not nested in a single tag can exchange data using the request structure. The Request scope is
represented as a structure named Request.


Where is data accessible?
Two custom tags can be related in a variety of ways in a page. Ancestor and descendant relationships are important
because they relate to the order of tag nesting.

A tag's descendants are inactive while the page is executed; that is, the descendent tags have no instance data. A tag,
therefore, can only access data from its ancestors, not its descendants. Ancestor data is available from the current page
and from the whole runtime tag context stack. The tag context stack is the path from the current tag element up the
hierarchy of nested tags, including those tags in included pages and custom tag references, to the start of the base page
for the request. Both cfinclude tags and custom tags appear on the tag context stack.


High-level data exchange
Although the ability to create nested custom tags is a tremendous productivity gain, keeping track of complex nested
tag hierarchies can become a chore. The cfassociate tag lets the parent know what the children are up to. By adding
this tag to a sub tag, you enable communication of its attributes to the base tag.

In addition, there are many cases in which descendant tags are used only as a means for data validation and exchange
with an ancestor tag, such as cfhttp/cfhttpparam and cftreecftreeitem. You can use the cfassociate tag to
encapsulate this processing.

The cfassociate tag has the following format:

<cfassociate baseTag="tagName" dataCollection="collectionName">

The baseTag attribute specifies the name of the base tag that gets access to this tag's attributes. The dataCollection
attribute specifies the name of the structure in which the base tag stores the subtag data. Its default value is
AssocAttribs. ColdFusion requires a dataCollection attribute only if the base tag can have more than one type of
subtag. It is convenient for keeping separate collections of attributes, one per tag type.

Note: If the custom tag requires an end tag, the code processing the structure referenced by the dataCollection attribute
must be part of end-tag code.

When cfassociate is encountered in a sub tag, the sub tag's attributes are automatically saved in the base tag. The
attributes are in a structure appended to the end of an array whose name is thisTag.collectionName.

The cfassociate tag performs the following operations:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       222
Building Blocks of ColdFusion Applications




<!--- Get base tag instance data --->
     <cfset data = getBaseTagData(baseTag)>
     <!--- Create a string with the attribute collection name --->
     <cfset collection_Name = "data.#dataCollection#">
     <!--- Create the attribute collection, if necessary --->
     <cfif not isDefined(collectionName)>
           <cfset #collection_Name# = arrayNew(1)>
     </cfif>
     <!--- Append the current attributes to the array --->
     <cfset temp=arrayAppend(evaluate(collectionName), attributes)>

The code accessing subtag attributes in the base tag could look like the following:

<!--- Protect against no subtags --->
     <cfparam Name='thisTag.assocAttribs' default=#arrayNew(1)#>


     <!--- Loop over the attribute sets of all sub tags --->
     <cfloop index=i from=1 to=#arrayLen(thisTag.assocAttribs)#>


           <!--- Get the attributes structure --->
           <cfset subAttribs = thisTag.assocAttribs[i]>
           <!--- Perform other operations --->


     </cfloop>


Ancestor data access
A structure object contains all the ancestor's data.

The following functions provide access to ancestral data:

* GetBaseTagList: Returns a comma-delimited list of uppercase ancestor tag names, as a string. The first list
   element is the current tag, the next element is the parent tag name if the current tag is a nested tag. If the function
   is called for a top-level tag, it returns an empty string.

* GetBaseTagData, InstanceNumber=1): Returns an object that contains all the variables (not just the local
   variables) of the nth ancestor with a given name. By default, the closest ancestor is returned. If there is no ancestor
   by the given name, or if the ancestor does not expose any data (such as cfif), an exception is thrown.


Example: ancestor data access
This example creates two custom tags and a simple page that calls each of the custom tags. The first custom tag calls
the second. The second tag reports on its status and provides information about its ancestors.


Create the calling page
1 Create a ColdFusion page (the calling page) with the following content:

    Call cf_nesttag1 which calls cf_nesttag2<br>
   <cf_nesttag1>
   <hr>


   Call cf_nesttag2 directly<br>
   <cf_nesttag2>
   <hr>

2 Save the page as nesttest.cfm.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                         223
Building Blocks of ColdFusion Applications




Create the first custom tag page
1 Create a ColdFusion page with the following content:

    <cf_nesttag2>

2 Save the page as nesttag1.cfm.


Create the second custom tag page
1 Create a ColdFusion page with the following content:

    <cfif thisTag.executionmode is 'start'>
         <!--- Get the tag context stack. The list looks something like
         "MYTAGNAME, CALLINGTAGNAME, ..." --->
         <cfset ancestorlist = getbasetaglist()>


         <!--- Output your own name. You are the first entry in the context stack. --->
         <cfoutput>
         <p>I'm custom tag #ListGetAt(ancestorlist,1)#</p>


         <!--- Output all the contents of the stack a line at a time. --->
         <cfloop index="loopcount" from="1" to="#listlen(ancestorlist)#">
              Ancestorlist entry #loopcount# n is #ListGetAt(ancestorlist,loopcount)#<br>
         </cfloop><br>
         </cfoutput>


         <!--- Determine whether you are nested inside a custom tag. Skip the first
              element of the ancestor list, i.e., the name of the custom tag I'm in. --->
         <cfset incustomtag = ''>
         <cfloop index="elem"
              list="#listrest(ancestorlist)#">
              <cfif (left(elem, 3) eq 'cf_')>
                    <cfset incustomtag = elem>
                    <cfbreak>
              </cfif>
         </cfloop>


         <cfif incustomtag neq ''>
              <!--- Say that you are there. --->




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                224
Building Blocks of ColdFusion Applications




              <cfoutput>
                    I'm running in the context of a custom tag named #inCustomTag#.<p>
              </cfoutput>


              <!--- Get the tag instance data. --->
              <cfset tagdata = getbasetagdata(incustomtag)>


              <!--- Find out the tag's execution mode. --->
              I'm located inside the
              <cfif tagdata.thisTag.executionmode neq 'inactive'>
                    custom tag code either because it is in its start or end execution mode.
              <cfelse>
                    body of the tag
              </cfif>
              <p>
         <cfelse>
              <!--- Say that you are lonely. --->
              I'm not nested inside any custom tags. :^( <p>
         </cfif>
    </cfif>

2 Save the page as nesttag2.cfm.

3 Open the file nesttest.cfm in your browser.



Building Custom CFXAPI Tags

Sometimes, the best approach to application development is to develop elements of your application by building
executable to run with ColdFusion. Perhaps the application requirements go beyond what is currently feasible in
CFML. Perhaps you can improve application performance for certain types of processing. Or, you have existing code
that already solves an application problem and you want to incorporate it into your ColdFusion application.

To meet these types of requirements, you can use the ColdFusion Extension Application Programming Interface (CFX
API) to develop custom ColdFusion tags based on Java or C++.


What are CFX tags?
ColdFusion Extension (CFX) tags are custom tags written against the ColdFusion Extension Application
Programming Interface. Generally, you create a CFX tag if you want to do something that is not possible in CFML, or
if you want to improve the performance of a repetitive task.

One common use of CFX tags is to incorporate existing application functionality into a ColdFusion application. That
means if you already have the code available, CFX tags make it easy to use it in your application.

CFX tags can do the following:

* Handle any number of custom attributes.

* Use and manipulate ColdFusion queries for custom formatting.

* Generate ColdFusion queries for interfacing with non-ODBC based information sources.

* Dynamically generate HTML to return to the client.

* Set variables within the ColdFusion application page from which they are called.

* Throw exceptions that result in standard ColdFusion error messages.



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               225
Building Blocks of ColdFusion Applications




You can build CFX tags using C++ or Java.

Note: ColdFusion provides several different techniques to create reusable code, including custom tags. For information
on all of these techniques, see "Creating ColdFusion Elements" on page 146.


Before you begin developing CFX tags in Java
Before you begin developing CFX tags in Java, configure your Java development environment. Also, it can be helpful
to review the examples here before you create CFX tags.


Sample Java CFX tags
Before you begin developing a CFX tag in Java, it can be useful to study sample CFX tags. You can find the Java source
files for the examples for Windows in the cfx\java\distrib\examples subdirectory of the main installation directory. In
UNIX systems, the files are located in the cfx/java/examples directory. The following table describes the example tags:


 Example                        Action                                        Demonstrates

 HelloColdFusion                Prints a personalized greeting.               The minimal implementation required to create a CFX
                                                                              tag.

 ZipBrowser                     Retrieves the contents of a ZIP archive.      How to generate a ColdFusion query and return it to
                                                                              the calling page.

 ServerDateTime                 Retrieves the date and time from a network    Attribute validation, using numeric attributes, and
                                server.                                       setting variables within the calling page.

 OutputQuery                    Returns a ColdFusion query in an HTML table.  How to handle a ColdFusion query as input, throw
                                                                              exceptions, and generate dynamic output.

 HelloWorldGraphic              Generates a "Hello World!" graphic in JPEG    How to dynamically create and return graphics from a
                                format.                                       Java CFX tag.



Setting up your development environment to develop CFX tags in Java
You can use a wide range of Java development environments, including the Java Development Kit (JDK) from Sun, to
build Java CFX tags. You can download the JDK from Sun http://java.sun.com/j2se.

Adobe recommends that you use one of the commercial Java IDEs, so you have an integrated environment for
development, debugging, and project management.


Configuring the classpath
To configure your development environment to build Java CFX tags, ensure that the supporting classes are visible to
your Java compiler. These classes are located in the cfx.jar archive, located in one of the following directories:

Server configuration cf_root/wwwroot/WEB-INF/lib

J2EE configuration cf_webapp_root/WEB-INF/lib

Consult your Java development tool documentation to determine how to configure the compiler classpath for your
particular environment.

The cfx.jar archive contains the classes in the com.allaire.cfx package, which are required for developing and
deploying Java CFX tags.

When you create new Java CFX tags, compile them into the WEB-INF/classes directory. Doing so simplifies your
development, debugging, and testing processes.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       226
Building Blocks of ColdFusion Applications




After you finish with development and testing, you can deploy your Java CFX tag anywhere on the classpath visible to
ColdFusion.


Customizing and configuring Java
Use the ColdFusion Administrator > Server Settings > JVM and Java Settings page to customize your Java
development environment by customizing the classpath and Java system properties, or by specifying an alternate JVM.
For more information, see the ColdFusion Administrator online Help.


Writing a Java CFX tag
To create a Java CFX tag, create a class that implements the Custom tag interface. This interface contains one method,
processRequest, which is passed Request and Response objects that are then used to do the work of the tag.

The example in the following procedure creates a simple Java CFX tag named cfx_MyHelloColdFusion that writes a
text string back to the calling page.

1 Create a source file in your editor with the following code:

    import com.allaire.cfx.* ;


    public class MyHelloColdFusion implements CustomTag {
         public void processRequest( Request request, Response response )
         throws Exception {
         String strName = request.getAttribute( "NAME" ) ;
         response.write( "Hello, " + strName ) ;
         }
    }

2 Save the file as MyHelloColdFusion.java in the WEB_INF/classes directory.

3 Compile the java source file into a class file using the Java compiler. If you are using the command line tools
   bundled with the JDK, use the following command line, which you execute from within the classes directory:

    javac -classpath cf_root\WEB-INF\lib\cfx.jar MyHelloColdFusion.java

Note: The previous command works only if the Java compiler (javac.exe) is in your path. If it is not in your path, specify
the fully qualified path; for example, c:\jdk1.3.1_01\bin\javac in Windows or /usr/java/bin/javac in UNIX.

If you receive errors during compilation, check the source code to make sure that you entered it correctly. If no errors
occur, you successfully wrote your first Java CFX tag.


Calling the CFX tag from a ColdFusion page
You call Java CFX tags from within ColdFusion pages by using the name of the CFX tag that is registered on the
ColdFusion Administrator CFX Tags page. This name should be the prefix cfx_ followed by the class name (without
the .class extension).


Register a Java CFX tag in the ColdFusion Administrator
1 In the ColdFusion Administrator, select Extensions > CFX Tags.

2 Click Register Java CFX.

3 Enter the tag name (for example, cfx_MyHelloColdFusion).

4 Enter the class name without the .class extension (for example, MyHelloColdFusion).

5 (Optional) Enter a description.

6 Click Submit.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     227
Building Blocks of ColdFusion Applications




You can now call the tag from a ColdFusion page.


Call a CFX tag from a ColdFusion page
1 Create a ColdFusion page (.cfm) in your editor with the following content to call the HelloColdFusion custom tag:

    <html>
   <body>
         <cfx_MyHelloColdFusion NAME="Les">
   </body>
   </html>

2 Save the file in a directory configured to serve ColdFusion pages. For example, you can save the file as
   C:\inetpub\wwwroot\cfdocs\testjavacfx.cfm in Windows or /home/docroot/cfdocs/testjavacfx.cfm in UNIX.

3 If you have not already done so, register the CFX tag in the ColdFusion Administrator (see "Registering CFX tags"
   on page 234).

4 Request the page from your browser using the appropriate URL; for example:

   http://localhost/cfdocs/testjavacfx.cfm

ColdFusion processes the page and returns a page that displays the text "Hello, Les." If an error is returned instead,
check the source code to make sure that you entered it correctly.


Delete a CFX tag in the ColdFusion Administrator
1 In the ColdFusion Administrator, select Extensions > CFX Tags.

2 For the tag to delete, click the Delete icon in the Controls column of the Registered CFX Tags list.


Processing requests
Implementing a Java CFX tag requires interaction with the Request and Response objects passed to the
processRequest method. In addition, CFX tags that must work with ColdFusion queries also interface with the
Query object. The com.allaire.cfx package, located in the WEB-INF/lib/cfx.jar archive, contains the Request,
Response, and Query objects.

For a complete description of these object types, see ColdFusion Java CFX Reference in the CFML Reference. For a
complete example Java CFX tag that uses Request, Response, and Query objects, see "ZipBrowser example" on
page 228.


Request object
The Request object is passed to the processRequest method of the CustomTag interface. The following table lists the
methods of the Request object for retrieving attributes, including queries, passed to the tag and for reading global tag
settings:


Method                                 Description

attributeExists                        Checks whether the attribute was passed to this tag.

debug                                  Checks whether the tag contains the debug attribute.

getAttribute                           Retrieves the value of the passed attribute.

getAttributeList                       Retrieves a list of all attributes passed to the tag.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       228
Building Blocks of ColdFusion Applications




 Method                                Description

 getIntAttribute                       Retrieves the value of the passed attribute as an integer.

 getQuery                              Retrieves the query that was passed to this tag, if any.

 getSetting                            Retrieves the value of a global custom tag setting.


For detailed reference information on each of these interfaces, see the CFML Reference.


Response object
The Response object is passed to the processRequest method of the CustomTag interface. The following table lists the
methods of the Response object for writing output, generating queries, and setting variables within the calling page:


 Method                         Description

 write                          Outputs text to the calling page.

 setVariable                    Sets a variable in the calling page.

 addQuery                       Adds a query to the calling page.

 writeDebug                     Outputs text to the debug stream.


For detailed reference information on each of these interfaces, see the CFML Reference.


Query object
The Query object provides an interface for working with ColdFusion queries. The following table lists the methods of
the Query object for retrieving name, row count, and column names and methods for getting and setting data
elements:


 Method                         Description

 getName                        Retrieves the name of the query.

 getRowCount                    Retrieves the number of rows in the query.

 getColumnIndex                 Retrieves the index of a query column.

 getColumns                     Retrieves the names of the query columns.

 getData                        Retrieves a data element from the query.

 addRow                         Adds a new row to the query.

 setData                        Sets a data element within the query.


For detailed reference information on each of these interfaces, see CFML Reference.


Life cycle of Java CFX tags
A new instance of the Java CFX object is created for each invocation of the Java CFX tag. As a result, it is safe to store
per-request instance data within the members of your CustomTag object. To store data and objects that are accessible
to all instances of your CustomTag, use static data members. If you do so, ensure that all accesses to the data are thread-
safe.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        229
Building Blocks of ColdFusion Applications




ZipBrowser example
The following example shows the use of the Request, Response, and Query objects. The example uses the java.util.zip
package to implement a Java CFX tag called cfx_ZipBrowser, which is a ZIP file browsing tag.

Note: The Java source file that implements cfx_ZipBrowser, ZipBrowser.java, is included in the
cf_root/cfx/java/distrib/examples (server configuration) or cf_webapp_root/WEB-
INF/cfusion/cfx/java/distrib/examples (J2EE configuration) directory. Compile ZipBrowser.java to implement the tag.

The tag archive attribute specifies the fully qualified path of the ZIP archive to browse. The tag name attribute must
specify the query to return to the calling page. The returned query contains three columns: Name, Size, and
Compressed.

For example, to query an archive at the path C:\logfiles.zip for its contents and output the results, you use the following
CFML code:

<cfx_ZipBrowser
     archive="C:\logfiles.zip"
     name="LogFiles">


<cfoutput query="LogFiles">
#Name#,#Size#, #Compressed# <BR>
</cfoutput>

The Java implementation of ZipBrowser is as follows:

import com.allaire.cfx.* ;
import java.util.Hashtable ;
import java.io.FileInputStream ;
import java.util.zip.* ;


public class ZipBrowser implements CustomTag {
     public void processRequest( Request request, Response response )
           throws Exception {
           // Validate that required attributes were passed.
           if (!request.attributeExists( "ARCHIVE" ) || !request.attributeExists( "NAME" ) ) {
                throw new Exception(
           "Missing attribute (ARCHIVE and NAME are both " +
                "required attributes for this tag)" ) ;
           }
      // get attribute values
           String strArchive = request.getAttribute( "ARCHIVE" ) ;
           String strName = request.getAttribute( "NAME" ) ;


     // create a query to use for returning the list of files
           String[] columns = { "Name", "Size", "Compressed" } ;
           int iName = 1, iSize = 2, iCompressed = 3 ;
           Query files = response.addQuery( strName, columns ) ;


     // read the ZIP file and build a query from its contents




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       230
Building Blocks of ColdFusion Applications




           ZipInputStream zin = new ZipInputStream( new FileInputStream(strArchive) ) ;
           ZipEntry entry ;
           while ( ( entry = zin.getNextEntry()) != null ) {
                // Add a row to the results.
                      int iRow = files.addRow() ;


                      // populate the row with data
                      files.setData( iRow, iName, entry.getName() ) ;
                      files.setData( iRow, iSize, String.valueOf(entry.getSize()) ) ;
                      files.setData( iRow, iCompressed,
                            String.valueOf(entry.getCompressedSize())) ;


                      // Finish up with entry.
                      zin.closeEntry() ;
                }


                // Close the archive.
                zin.close() ;
     }
}



Approaches to debugging Java CFX tags
Java CFX tags are not stand-alone applications that run in their own process, like typical Java applications. Rather, they
are created and run from an existing process. As a result, debugging Java CFX tags is more difficult, because you cannot
use an interactive debugger to debug Java classes that another process has loaded.

To overcome this limitation, you can use one of the following techniques:

* Debug the CFX tag while it is running within ColdFusion by outputting the debug information as needed.

* Debug the CFX tag using a Java IDE (Integrated Development Environment) that supports debugging features,
   such as setting breakpoints, stepping through your code, and displaying variable values.

* Debug the request in an interactive debugger offline from ColdFusion using the special com.allaire.cfx debugging
   classes.


Outputting debugging information
Before using interactive debuggers became the norm, programmers typically debugged their programs by inserting
output statements in their programs to indicate information such as variable values and control paths taken. Often,
when a new platform emerges, this technique comes back into vogue while programmers wait for more sophisticated
debugging technology to develop for the platform.

If you must debug a Java CFX tag while running against a live production server, reset this technique. In addition to
outputting debugging text using the Response.write method, you can also call your Java CFX tag with the
debug="On" attribute. This attribute flags the CFX tag that the request is running in debug mode and therefore can
generate additional extended debugging information. For example, to call the HelloColdFusion CFX tag in
debugging mode, use the following CFML code:

<cfx_HelloColdFusion name="Robert" debug="On">

To determine whether a CFX tag is run with the debug attribute, use the Request.debug method. To write debugging
output in a special debugging block after the tag finishes executing, use the Response.writeDebug method. For
information on using these methods, see ColdFusion Java CFX Reference in CFML Reference.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  231
Building Blocks of ColdFusion Applications




Debugging in a Java IDE
You can use a Java IDE to debug your Java CFX tags. As a result, you can develop your Java CFX tag and debug it in a
single environment.

1 Start your IDE.

2 In the project properties (or the project setting of your IDE), make sure your CFX class is in the web_root\WEB-
   INF\classes directory or in the system classpath.

3 Make sure the libraries cf_root/wwwroot/WEB-INF/lib/cfx.jar (cf_webapp_root/WEB-INF/lib/cfx.jar in the J2EE
   configuration) and cf_root/runtime/lib/jrun.jar (server configuration only) are included in your classpath.

4 In your project settings, set your main class to jrunx.kernel.JRun and application parameters to -startdefault.

5 Debug your application by setting breakpoints, single stepping, displaying variables, or by performing other
   debugging actions.


Using the debugging classes
To develop and debug Java CFX tags in isolation from the ColdFusion, you use three special debugging classes that are
included in the com.allaire.cfx package. These classes lets you simulate a call to the processRequest method of
your CFX tag within the context of the interactive debugger of a Java development environment. The three debugging
classes are the following:

* DebugRequest: An implementation of the Request interface that lets you initialize the request with custom
   attributes, settings, and a query.

* DebugResponse: An implementation of the Response interface that lets you print the results of a request once it
   has completed.

* DebugQuery: An implementation of the Query interface that lets you initialize a query with a name, columns, and
   a data set.


Implement a main method
1 Create a main method for your Java CFX class.

2 Within the main method, initialize a DebugRequest and DebugResponse, and a DebugQuery. Use the appropriate
   attributes and data for your test.

3 Create an instance of your Java CFX tag and call its processRequest method, passing in the DebugRequest and
   DebugResponse objects.

4 Call the DebugResponse.printResults method to output the results of the request, including content generated,
   variables set, queries created, and so on.

After you implement a main method as described previously, you can debug your Java CFX tag using an interactive,
single-step debugger. Specify your Java CFX class as the main class, set breakpoints as appropriate, and begin
debugging.


Example:debugging classes
The following example demonstrates how to use the debugging classes:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                             232
Building Blocks of ColdFusion Applications




import java.util.Hashtable ;
import com.allaire.cfx.* ;


public class OutputQuery implements CustomTag {
     // debugger testbed for OutputQuery
     public static void main(String[] argv) {
           try {
                // initialize attributes
                Hashtable attributes = new Hashtable() ;
                attributes.put( "HEADER", "Yes" ) ;
                attributes.put( "BORDER", "3" ) ;


                // initialize query


                String[] columns = { "FIRSTNAME", "LASTNAME", "TITLE" } ;


                String[][] data = {
                      { "Stephen", "Cheng", "Vice President" },
                      { "Joe", "Berrey", "Intern" },
                      { "Adam", "Lipinski", "Director" },
                      { "Lynne", "Teague", "Developer" } };


                DebugQuery query = new DebugQuery( "Employees", columns, data ) ;


                // create tag, process debugging request, and print results
                OutputQuery tag = new OutputQuery() ;
                DebugRequest request = new DebugRequest( attributes, query ) ;
                DebugResponse response = new DebugResponse() ;
                tag.processRequest( request, response ) ;
                response.printResults() ;
           }
           catch( Throwable e ) {
                e.printStackTrace() ;
           }
}


     public void processRequest(Request request, Response response) throws Exception {
           // ...code for processing the request...
     }
}



Developing CFX tags in C++
You can develop CFX tags in C++.


Sample C++ CFX tags
Before you begin development of a CFX tag in C++, you can study the two CFX tags included with ColdFusion. These
examples can help you get started working with the CFXAPI. The two example tags are as follows:

* CFX_DIRECTORYLIST: Queries a directory for the list of files it contains.

* CFX_NTUSERDB (Windows only): Lets you add and delete Windows NT users.

In Windows, these tags are located in the cf_root\cfx\examples directory. In UNIX, these tags are in the
cf_root/coldfusion/cfx/examples directory.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               233
Building Blocks of ColdFusion Applications




Setting up your C++ development environment
The following compliers generate valid CFX code for UNIX platforms:


 Platform                  Compiler

 Solaris                   Sun Workshop C++ compiler, version 5.0 or higher (gcc cannot be used to compile CFX code on Solaris)

 Linux                     Gnu C++ compiler - gcc


Before you can use your C++ compiler to build custom tags, enable the compiler to locate the CFX API header file,
cfx.h. In Windows, add the CFX API include directory to your list of global include paths. In Windows, this directory
is cf_root\cfx\include. In UNIX, this directory is cf_root/cfx/include. in UNIX, you need -I <includepath> on your
compile line (see the Makefile for the directory list example in the cfx/examples directory).


Compiling C++ CFX tags
CFX tags built in Windows and in UNIX must be thread-safe. Compile CFX tags for Solaris with the -mt switch on the
Sun compiler.


Locating your C++ library files in UNIX
In UNIX systems, your C++ library files can be in any directory as long as the directory is included in
LD_LIBRARY_PATH or SHLIB_PATH (HP-UX only).


Implementing C++ CFX tags
CFX tags built in C++ use the tag request object, represented by the C++ CCFXRequest class. This object represents a
request made from an application page to a custom tag. A pointer to an instance of a request object is passed to the
main procedure of a custom tag. The methods available from the request object let the custom tag accomplish its work.
For information about the CFX API classes and members, see ColdFusion C++ CFX Reference in the CFML Reference.

Note: Calling a nonexistent C++ CFX procedure or entry point causes a JVM crash in UNIX.


Debugging C++ CFX tags
After you configure a debugging session, you run your custom tag from within the debugger, set breakpoints, single-
step, and so on.


Debugging in Windows
You can debug custom tags within the Visual C++ environment.

1 Build your C++ CFX tag using the debug option.

2 Restart ColdFusion.

3 Start Visual C++.

4 Select Build > Start Debug > AttachProcess.

5 Select jrunsvc.exe.

    Adobe recommends that you shut down all other Java programs.

6 Execute any ColdFusion page that calls the CFX tag.

7 Select File > Open to open a file in VisualDev in which to set a breakpoint.

8 Set a breakpoint in the CFX project.

    The best place is to place it in ProcessRequest(). Next time you execute the page you will reach the breakpoint.



                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   234
Building Blocks of ColdFusion Applications




Registering CFX tags
To use a CFX tag in your ColdFusion applications, first register it in the Extensions, CFX Tags page in the ColdFusion
Administrator.

1 In the ColdFusion Administrator, select Extensions > CFX Tags.

2 Click Register C++ CFX.

3 Enter the Tag name (for example, cfx_MyNewTag).

4 If the Server Library .dll field is empty, enter the filepath.

5 Accept the default Procedure entry.

6 Clear the Keep library loaded box while developing the tag.

   For improved performance, when the tag is ready for production use, you can select this option to keep the DLL in
   memory.

7 (Optional) Enter a description.

8 Click Submit.

You can now call the tag from a ColdFusion page.


Delete a CFX tag
1 In the ColdFusion Administrator, select Extensions > CFX Tags.

2 For the tag to delete, click the Delete icon in the Controls column of the Registered CFX Tags list.




                      