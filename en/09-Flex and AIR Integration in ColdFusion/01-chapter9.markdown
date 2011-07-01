Chapter 9: Flex and AIR Integration in
ColdFusion

Using the Flash Remoting Service

Using the Flash Remoting service of Adobe ColdFusion, ColdFusion developers can work together with Flash
designers to build dynamic Flash user interfaces for ColdFusion applications.

For a complete description of Flash Remoting capabilities, including how ColdFusion interacts with Flash Remoting,
see Using Flash Remoting MX 2004 and Flash Remoting ActionScript Dictionary in Flash Help. You can also access the
Flash Remoting documentation on the Flash Remoting Developer Center at
www.adobe.com/go/learn_cfu_flashremoting_en.


About using the Flash Remoting service with ColdFusion
Using the Flash Remoting service of ColdFusion, ColdFusion developers can work with Flash MX 2004 designers to
build Flash user interfaces (UIs) for ColdFusion applications. Building Flash UIs requires the separation of user
interface code from business logic code. You build user interface controls in Flash, and you build the business logic in
ColdFusion.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    607
Flex and AIR Integration in ColdFusion




The following is a simplified representation of the relationship between Flash and ColdFusion:

       SWF les



   .SWFs




                        Computer            Interactive TV       Mobile phone         PDA




                                                 HTTP




                                            Application Server
                                                                                           Web
                                              Flash Remoting                            services




                                              Database


Planning your SWF application
When you are planning ColdFusion application development with Flash UIs, remember the importance of separating
display code from business logic. Separating display code from business logic enables your ColdFusion applications to
interact with multiple client types, such as SWF applications, web browsers, and web services.

When you build ColdFusion applications for multiple clients, your ColdFusion pages and components return
common data types, including strings, integers, query objects, structures, and arrays. Clients that receive the results
can process the passed data according to the client type, such as ActionScript with Flash, or CFML with ColdFusion.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                           608
Flex and AIR Integration in ColdFusion




To use the Flash Remoting service with ColdFusion, you build ColdFusion pages and components or deploy Java
objects. In ColdFusion pages, you use the Flash variable scope to interact with SWF applications. ColdFusion
components (CFCs) natively support Flash interaction. The public methods of Java objects are also available to the
Flash Remoting service.

The Flash Remoting ActionScript API has been updated to comply with ActionScript 2.0. The ActionScript 2.0 version
of the API consists of the following significant features:


 Flash Remoting MX 2004 ActionScript 2.0 API Features

 Enforcement of strict data typing, which requires you to declare the data types of variables and prohibits you from assigning different types
 of data to them.

 Enforcement of case sensitivity, which means that myvar and myVar are two different variables, though they were considered the same
 variable with different spellings in ActionScript 1.0.

 A new Service class, which lets you create a gateway connection and at the same time obtain a reference to a service and its methods. It
 includes the connection property, which returns the connection and also lets you set credentials for authorization on the remote server.

 Note: The NetServices class is still supported but has been deprecated in favor of the new Service and Connection classes

 A new Connection class that helps you create and use Flash Remoting connections.

 Note: The Connection class supersedes the former NetConnection class.

 A new PendingCall object returned on each call to a service method that is run using the Service object. The PendingCall object contains the
 responder property, which you use to specify the methods to handle the results of the service call.

 A new RelayResponder class, which specifies the methods to which the result and fault outcomes of a service call are relayed.

 A RecordSet object that contains new properties (columnNames, items, and length), new methods (clear(), contains(),
 editField(), getEditingData(), getIterator(), getLocalLength(), getRemoteLength(), isEmpty(), and sortItems()),
 and the new modelChanged event.


For more information on the ActionScript 2.0 Flash Remoting API, see Flash Remoting ActionScript Dictionary Help.


Configuring the Flash Remoting Gateway
The following parameters in the ColdFusion web.xml file point the Flash Remoting gateway to the gateway-config.xml
file.

<init-param>
      <param-name>gateway.configuration.file</param-name>
      <param-value>/WEB-INF/gateway-config.xml</param-value>
             </init-param>
<init-param>
      <param-name>whitelist.configuration.file</param-name>
      <param-value>/WEB-INF/gateway-config.xml</param-value>
</init-param>
<init-param>
      <param-name>whitelist.parent.node</param-name>
      <param-value>gateway-config</param-value>
</init-param>

Both the web.xml file and the gateway-config.xml file are located in the WEB-INF directory of your ColdFusion server.
As a general rule, you do not have to change these web.xml settings.




                                                         

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 609
Flex and AIR Integration in ColdFusion




ColdFusion MX 7 and later versions of ColdFusion configure Flash gateways differently from previous ColdFusion
releases. Parameters that worked before this release are no longer supported, and you specify all configuration
parameters in the gateway-config.xml file. Also, the Flash gateway now supports a whitelist, which specifies which
remote sources can be accessed through the gateway. The two web.xml entries that identify the whitelist must specify
your gateway-config.xml file and gateway-config as the parent node.

You can modify the gateway-config.xml file to configure service adapters, add service names to the whitelist, change
the logging level, and specify how the gateway handles case sensitivity.

You can configure gateway features in the gateway-config.xml file as follows:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  610
Flex and AIR Integration in ColdFusion




Feature                                   Description

service adapters                          By default, the PageableResultSetAdapter, the ColdFusionAdapter, the CFCAdapter (for
                                          ColdFusion components), and the CFSSASAdapter (for server-side ActionScript)
                                          adapters are enabled in ColdFusion.

                                          You can also enable the JavaBeanAdapter, JavaAdapter, EJBAdapter, ServletAdapter,
                                          and CFWSAdapter (for web services) by removing their enclosing comment symbols (<!-
                                          - and -->). The following service adapter tags are defined as the default tag values:

                                          <service-adapters>

                                          <adapter>flashgateway.adapter.resultset.PageableResultSetAdapter
                                          </adapter>
                                             <adapter>coldfusion.flash.adapter.ColdFusionAdapter</adapter>
                                             <adapter>coldfusion.flash.adapter.CFCAdapter</adapter>
                                             <adapter>coldfusion.flash.adapter.CFSSASAdapter</adapter>
                                             <!-- <adapter type="stateful-
                                          class">flashgateway.adapter.java.JavaBeanAdapter</adapter> -->
                                             <!-- <adapter type="stateless-
                                          class">flashgateway.adapter.java.JavaAdapter</adapter> -->
                                             <!-- <adapter
                                          type="ejb">flashgateway.adapter.java.EJBAdapter</adapter> -->
                                             <!-- <adapter
                                          type="servlet">flashgateway.adapter.java.ServletAdapter</adapter
                                          > -->
                                             <!-- <adapter>coldfusion.flash.adapter.CFWSAdapter</adapter> -
                                          ->
                                          </service-adapters>

security                                  You can edit security settings in child tags of the <security> tag.

                                          In the <login-command> tag, you can set the
                                          flashgateway.security.LoginCommand implementation for performing local
                                          authentication on a specific application server. By default, the <login-command> tag is
                                          set to the following values:

                                          <login-command>
                                             <class>flashgateway.security.JRunLoginCommand</class>
                                             <server-match>JRun</server-match>
                                          </login-command>

                                          In the <show-stacktraces> tag, you can enable stack traces. Stack traces are useful
                                          for debugging and product support, but they should not be sent to the client in
                                          production mode because they can expose internal information about the system. The
                                          following <show-stacktraces> tag is the default tag:

                                          <show-stacktraces>false</show-stacktraces>

                                          The <whitelist> tag specifies which remote sources can be accessed through the
                                          gateway. The * character can be used as a wildcard to imply ALL matches. The following
                                          <whitelist> tag shows the default value:

                                          <whitelist>
                                             <source>*</source>
                                          </whitelist>

                                          When you deploy your application, ensure that you change this setting so that it
                                          specifies only the services that the gateway needs to access to run your application.
                                          Remember that for ColdFusion based services, directories are treated as "packages" and
                                          thus you specify a period delimited path from the web root to the directory or file
                                          containing the services you will allow access to. An asterisk wildcard allows access to all
                                          services in a particular directory. You can have multiple <source> tags.

                                          The following whitelist allows access to the webroot/cfdocs/exampleapps/ directory,
                                          which includes the flash1 through flash5 Flash Remoting example directories. It also
                                          allows access to a webroot/BigApp/remoting directory and its children.

                                          <whitelist>
                                             <source>cfdocs.exampleapps.*</source>
                                             <source>BigApp.remoting.*</source>
                                          </whitelist>




                                           

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                              611
Flex and AIR Integration in ColdFusion




 Feature                                                Description

 logger level                                           You can set the level of logging between None, Error, Info, Warning, and Debug. The
                                                        following tag is the default logger level tag:

                                                        <logger level="Error">coldfusion.flash.ColdFusionLogger</logger>

 redirect URL                                           In the <redirect-url> tag, you can specify a URL to receive HTTP requests that are
                                                        not sent with AMF data. By default, the <redirect-url> tag is set to
                                                        {context.root}, which is the context root of the web application:

                                                        <redirect-url>{context.root}</redirect-url>

 case sensitivity                                       The <lowercase-keys> tag specifies how the gateway handles case sensitivity.
                                                        ActionScript 1.0 and ColdFusion use case insensitive data structures to store associative
                                                        arrays, objects and structs. The Java representation of these data types requires a case-
                                                        insensitive Map, which the gateway achieves by forcing all keys to lowercase.

                                                        ActionScript 2.0 is case sensitive and requires a <lowercase-keys> tag value of false.

                                                        The following <lowercase-keys> tag is the default tag:

                                                        <lowercase-keys>true</lowercase-keys>



Using the Flash Remoting service with ColdFusion pages
When you build a ColdFusion page that interacts with a SWF application, the directory name that contains the
ColdFusion pages translates to the service name that you call in ActionScript. The individual ColdFusion page names
within that directory translate to service functions that you call in ActionScript.

Note: Flash Remoting cannot interact with virtual directories accessed through a ColdFusion mapping.

In your ColdFusion pages, you use the Flash variable scope to access parameters passed to and from a SWF application.
To access parameters passed from a SWF application, you use the parameter name appended to the Flash scope or
the Flash.Params array. To return values to the SWF application, use the Flash.Result variable. To set an
increment value for records in a query object to be returned to the SWF application, use theFlash.Pagesize variable.

The following table shows the variables contained in the Flash scope:


 Variable                Description                                                                For more information

 Flash.Params            Array that contains the parameters passed from the SWF application.        See "Accessing parameters passed from
                         If you do not pass any parameters, Flash.params still exists, but it is    Flash" on page 612.
                         empty.

 Flash.Result            The variable returned from the ColdFusion page to the SWF                  See "Returning results to Flash" on page 613.
                         application that called the function.

                         Note: Because ActionScript performs automatic type conversion, do
                         not return a Boolean literal to Flash from ColdFusion. Return 1 to
                         indicate true, and return 0 to indicate false.

 Flash.Pagesize          The number of records returned in each increment of a record set to a      See "Returning records in increments to
                         SWF application.                                                           Flash" on page 614.


The following table compares the ColdFusion data types and their ActionScript equivalents:


 ActionScript data type                                             ColdFusion data type

 Number (primitive data type)                                       Number

 Boolean (primitive data type)                                      Boolean (0 or 1)

 String (primitive data type)                                       String




                                                         

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                       612
Flex and AIR Integration in ColdFusion




 ActionScript data type                                        ColdFusion data type

 ActionScript Object                                            Structure

 ActionScript Object (as the only argument passed to a service  Arguments of the service function. ColdFusion pages (CFM files): flash
 function)                                                      variable scope, ColdFusion components (CFC files): named arguments

 Null                                                           Null (Asc() returns 0, which translates to not defined)

 Undefined                                                      Null (Asc() returns 0, which translates to not defined)

 Ordered array                                                  Array

 Note: ActionScript array indexes start at zero (for example:   Note: ColdFusion array indexes start at one (for example: my_CFarray[1]).
 my_ASarray[0]).

 Named (or associative) array                                   Struct

 Date object                                                    Date

 XML object                                                     XML document

 RecordSet                                                      Query object (when returned to a SWF application only; you cannot pass a
                                                                RecordSet from a SWF application to a ColdFusion application)


Also, remember the following considerations regarding data types:

* If a string data type on the server represents a valid number in ActionScript, Flash can automatically cast it to a
   number if needed.

* To return multiple, independent values to the SWF application, place them in a complex variable that converts to
   a Flash Object, Array, or Associative Array, that can hold all of the required data. Return the single variable and
   access its elements in the SWF application.

For a complete explanation of using Flash Remoting data in ActionScript, see Using Flash Remoting MX 2004Help.


Accessing parameters passed from Flash
To access variables passed from SWF applications, you append the parameter name to the Flash scope or use the
Flash.Params array. Depending on how the values were passed from Flash, you refer to array values using ordered
array syntax or structure name syntax. Only ActionScript objects can pass named parameters.

For example, if you pass the parameters as an ordered array from Flash,array[1] references the first value. If you pass
the parameters as named parameters, you use standard structure-name syntax like params.name.

You can use most of the CFML array and structure functions on ActionScript collections. However, the StructCopy
CFML function does not work with ActionScript collections. The following table lists ActionScript collections and
describes how to access them in ColdFusion:




                                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                              613
Flex and AIR Integration in ColdFusion




 Collection             ActionScript example                      Notes

 Strict array           var myArray:Array = new Array();          The Flash Remoting service converts the Array argument to a
                        myArray[0] = "zero";                      ColdFusion array. All CFML array operations work as expected.
                        myArray[1] = "one";
                        myService.myMethod(myArray);              <cfset p1=Flash.Params[1][1]>
                                                                  <cfset p2=Flash.Params[1][2]>

 Named or associative   var myStruct:Array = new Array();         Named array keys are not case-sensitive in ActionScript.
 array                  myStruct["zero"] = "banana";
                        myStruct["one"] = "orange";               <cfset p1=Flash.Params[1].zero>
                        myService.myMethod(myStruct);             <cfset p2=Flash.Params[1].one>


 Mixed array            var myMxdArray:Array = new Array();       Treat this collection like a structure in ColdFusion. However,
                        myMxdArray["one"] = 1;                    keys that start with numbers are invalid CFML variable names.
                        myMxdArray[2] = true;
                                                                  Depending on how you attempt to retrieve this data,
                                                                  ColdFusion sometimes throws an exception. For example, the
                                                                  following CFC method throws an exception:

                                                                  <cfargument name="ca" type="struct">
                                                                  <cfreturn ca.2>

                                                                  The following CFC method does not throw an exception:

                                                                  <cfargument name="ca" type="struct">
                                                                  <cfreturn ca["2"]>

 UsinganActionScript    myService.myMethod({ x:1, Y:2, z:3 }); This notation provides a convenient way of passing named
 object initializer for                                           arguments to ColdFusion pages. You can access these
 named arguments                                                  arguments in ColdFusion pages as members of the Flash scope:

                                                                  <cfset p1 = Flash.x>
                                                                  <cfset p2 = Flash.y>
                                                                  <cfset p3 = Flash.z>

                                                                  Or, you can access them as normal named arguments of a CFC
                                                                  method.


The Flash.Params array retains the order of the parameters as they were passed to the method. You use standard
structure name syntax to reference the parameters; for example:

<cfquery name="flashQuery" datasource="cfdocexamples">
      SELECT ItemName, ItemDescription, ItemCost
      FROM tblItems
      WHERE ItemName EQ '#Flash.paramName#'
</cfquery>

In this example, the query results are filtered by the value of Flash.paramName, whichreferences the first parameter
in the passed array. If the parameters are passed as an ordered array from the SWF application, you use standard
structure name syntax; for example:

<cfset Flash.Result = "Variable 1:#Flash.Params[1]#, Variable 2: #Flash.Params[2]#">

Note: ActionScript array indexes start at zero. ColdFusion array indexes start at one.


Returning results to Flash
In ColdFusion pages, only the value of the Flash.Result variable is returned to the SWF application. For more
information about supported data types between ColdFusion and Flash, see the data type table in "Using the Flash
Remoting service with ColdFusion pages" on page 611. The following procedure creates the service function
helloWorld, which returns a structure that contains simple messages to the SWF application.


Create a ColdFusion page that passes a structure to a SWF application
1 Create a folder in your web root, and name it helloExamples.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    614
Flex and AIR Integration in ColdFusion




2 Create a ColdFusion page, and save it as helloWorld.cfm in the helloExamples directory.

3 Modify helloWorld.cfm so that the CFML code appears as follows:

    <cfset tempStruct = StructNew()>
   <cfset tempStruct.timeVar = DateFormat(Now ())>
   <cfset tempStruct.helloMessage = "Hello World">

   In the example, two string variables are added to a structure; one with a formatted date and one with a simple
   message. The structure is passed back to the SWF application using the Flash.Result variable.

    <cfset Flash.Result = tempStruct>

4 Save the file.

Remember, the directory name is the service address. The helloWorld.cfm file is a method of the helloExamples Flash
Remoting service. The following ActionScript example calls the helloWorld ColdFusion page and displays the values
that it returns:

import mx.remoting.*;
import mx.services.Log;
import mx.rpc.*;


// Connect to helloExamples service and create the howdyService service object
     var howdyService:Service = new Service(
                 "http://localhost/flashservices/gateway",
                 null,
                 "helloExamples",
                 null,
                 null );
// Call the service helloWorld() method
var pc:PendingCall = howdyService.helloWorld();
// Tell the service what methods handle result and fault conditions
pc.responder = new RelayResponder( this, "helloWorld_Result", "helloWorld_Fault" );


function helloWorld_Result(re:ResultEvent)
{
     // Display successful result
     messageDisplay.text = re.result.HELLOMESSAGE;
     timeDisplay.text = re.result.TIMEVAR;
}


function helloWorld_Fault(fe:FaultEvent)
{
     // Display fault returned from service
     messageDisplay.text = fe.fault;
}

Note: Due to ActionScript's automatic type conversion, do not return a Boolean literal to Flash from ColdFusion. Return
1 to indicate true, and return 0 to indicate false.


Returning records in increments to Flash
ColdFusion lets you return record set results to Flash in increments. For example, if a query returns 20 records, you
can set the Flash.Pagesize variable to return five records at a time to Flash. Incremental record sets let you minimize
the time that a SWF application waits for the application server data to load.


Create a ColdFusion page that returns an incremental record set to Flash
1 Create a ColdFusion page, and save it as getData.cfm in the helloExamples directory.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    615
Flex and AIR Integration in ColdFusion




2 Modify getData.cfm so that the code appears as follows:

    <cfparam name="pagesize" default="10">
   <cfif IsDefined("Flash.Params")>
         <cfset pagesize = Flash.Params[1]>
   </cfif>
   <cfquery name="myQuery" datasource="cfdocexamples">
         SELECT *
         FROM tblParks
   </cfquery>
   <cfset Flash.Pagesize = pagesize>
   <cfset Flash.Result = myQuery>

   In this example, if a single parameter is passed from the SWF application, the pagesize variable is set to the value
   of the Flash.Params[1] variable; otherwise, the value of the variable is the default, 10. Next, a statement queries
   the database. After the querying, the pagesize variable is assigned to the Flash.Pagesize variable. Finally, the
   query results are assigned to the Flash.Result variable, which is returned to the SWF application.

3 Save the file.

When you assign a value to the Flash.Pagesize variable, you are specifying that if the record set has more than a
certain number of records, the record set becomes pageable and returns the number of records specified in the
Flash.Pagesize variable. For example, the following code calls the getData() function of the CFMService and uses
the first parameter to request a page size of 5:

import mx.remoting.*;
import mx.services.Log;
import mx.rpc.*;
// Connect to helloExamples service and create the CFMService service object
     var CFMService:Service = new Service(
                 "http://localhost/flashservices/gateway",
                 null,
                 "helloExamples",
                 null,
                 null );
// Call the service getData() method
var pc:PendingCall = CFMService.getData(5);
// Tell the service what methods handle result and fault conditions
pc.responder = new RelayResponder( this, "getData_Result", "getData_Fault" );


function getData_Result(re:ResultEvent)
{
     // Display successful result
     DataGlue.bindFormatStrings(employeeData, re.result, "#PARKNAME#, #CITY#, #STATE#");
}
function getData_Fault(fe:FaultEvent)
{
     // Display fault returned from service
     trace("Error description from server: " + fe.fault.description);
}

In this example, employeeData is a Flash list box. The result handler, getData_Result, displays the columns
PARKNAME, CITY, and STATE in the employeeData list box. After the initial delivery of records, the RecordSet
ActionScript class assumes the task of fetching records. In this case, the list box requests more records as the user
scrolls the list box.

You can configure the client-side RecordSet object to fetch records in various ways using the
RecordSet.setDeliveryMode ActionScript function.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                616
Flex and AIR Integration in ColdFusion




Using Flash with CFCs
CFCs require little modification to work with a SWF application. The tag cffunction tag names the method and
contains the CFML logic, the cfargument tag names the arguments, and the cfreturn tag returns the result to the SWF
application. The name of the CFC file (*.cfc) translates to the service name in ActionScript.

Note: For CFC methods to communicate with SWF applications, set the cffunction tag's access attribute to remote.

The following example replicates the helloWorld function that was previously implemented as a ColdFusion page.
For more information, see "Using the Flash Remoting service with ColdFusion pages" on page 611.


Create a CFC that interacts with a SWF application
1 Create a CFC and save it as flashComponent.cfc in the helloExamples directory.

2 Modify the code in flashComponent.cfc so that it appears as follows:

    <cfcomponent name="flashComponent">
         <cffunction name="helloWorld" access="remote" returnType="Struct">
               <cfset tempStruct = StructNew()>
               <cfset tempStruct.timeVar = DateFormat(Now ())>
               <cfset tempStruct.helloMessage = "Hello World">
               <cfreturn tempStruct>
         </cffunction>
    </cfcomponent>

   In this example, the helloWorld function is created. The cfreturn tag returns the result to the SWF application.

3 Save the file.

The helloWorld service function is now available through the flashComponent service to ActionScript. The
following ActionScript example calls this function:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 617
Flex and AIR Integration in ColdFusion




import mx.remoting.*;
import mx.services.Log;
import mx.rpc.*;


// Connect to the Flash component service and create service object
      var CFCService:Service = new Service(
                 "http://localhost/flashservices/gateway",
                 null,
                 "helloExamples.flashComponent",
                 null,
                 null );
// Call the service helloWorld() method
var pc:PendingCall = CFCService.helloWorld();
// Tell the service what methods handle result and fault conditions
pc.responder = new RelayResponder( this, "helloWorld_Result", "helloWorld_Fault" );


function helloWorld_Result(re:ResultEvent)
{
      // Display successful result
      messageDisplay.text = re.result.HELLOMESSAGE;
      timeDisplay.text = re.result.TIMEVAR;
}


function helloWorld_Fault(fe:FaultEvent)
{
      // Display fault returned from service
      messageDisplay.text = fe.fault;
}

In this example, the CFCService object references the flashComponent component in the helloExamples directory.
Calling the helloWorld function in this example executes the function that is defined in flashComponent.

For ColdFusion components, the component filename, including the directory structure from the web root, serves as
the service name. Remember to delimit the path directories with periods rather than backslashes.


Using the Flash Remoting service with ColdFusion Java objects
You can run various kinds of Java objects with ColdFusion, including JavaBeans, Java classes, and Enterprise
JavaBeans. You can use the ColdFusion Administrator to add additional directories to the classpath.


Add a directory to ColdFusion classpath
1 Open the ColdFusion Administrator.

2 In the Server Settings menu, click the Java and JVM link.

3 Add your directory to the Class Path form field.

4 Click Submit Changes.

5 Restart ColdFusion.

When you place your Java files in the classpath, the public methods of the class instance are available to your SWF
movie.

For example, assume the Java class utils.UIComponents exists in a directory in your ColdFusion classpath. The Java
file contains the following code:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                             618
Flex and AIR Integration in ColdFusion




package utils;
public class UIComponents
{
     public UIComponents()
     {
     }
     public String sayHello()
     {
           return "Hello";
     }
}

Note: You cannot call constructors with Flash Remoting. Use the default constructor.

In ActionScript, the following javaService call runs the sayHello public method of the utils.UIComponents class:

import mx.remoting.*;
import mx.services.Log;
import mx.rpc.*;


// Connect to service and create service object
     var javaService:Service = new Service(
                 "http://localhost/flashservices/gateway",
                 null,
                 utils.UIComponents",
                 null,
                 null );
// Call the service sayHello() method
var pc:PendingCall = javaService.sayHello();
// Tell the service what methods handle result and fault conditions
pc.responder = new RelayResponder( this, "sayHello_Result", "sayHello_Fault" );


function sayHello_Result(re:ResultEvent)
{
     // Display successful result
     trace("Result is: " + re.result);
}


function sayHello_Fault(fe:FaultEvent)
{
     // Display fault returned from service
     trace("Error is: " + fe.fault.description);
}

Note: For more information about using Java objects with ColdFusion, see "Using Java objects" on page 1135


Handling errors with ColdFusion and Flash
To help with debugging, use cftry and cfcatch tags in your ColdFusion page or component to return error messages
to Flash Player. For example, the ColdFusion page, causeError.cfm, contains the code:

<cftry>
     <cfset dev = Val(0)>
     <cfset Flash.Result = (1 / dev)>
     <cfcatch type = "any">
           <cfthrow message = "An error occurred in this service: #cfcatch.message#">
     </cfcatch>
</cftry>



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    619
Flex and AIR Integration in ColdFusion




The second cfset tag in this example fails because it tries to divide by zero (0). The message attribute of the cfthrow
tag describes the error; ColdFusion returns this attribute to the SWF application.

To handle the error in your SWF application, create a fault handler like causeError_Fault in the following example:

import mx.remoting.*;
import mx.services.Log;
import mx.rpc.*;


// Connect to service and create service object
      var CFMService:Service = new Service(
                 "http://localhost/flashservices/gateway",
                 null,
                 "helloExamples",
                 null,
                 null );
// Call the service causeError() method
var pc:PendingCall = CFMService.causeError();
// Tell the service what methods handle result and fault conditions
pc.responder = new RelayResponder( this, "causeError_Result", "causeError_Fault" );


function causeError_Result(re:ResultEvent)
{
      // Display successful result
      messageDisplay.text = re.result;
}


function causeError_Fault(fe:FaultEvent)
{
      // Display fault returned from service
      trace("Error message from causeError is: " + fe.fault.description);
}

This example displays the trace message from the causeError_Fault function in the Flash Output panel. The portion
of the message that is contained in fe.fault.description is the portion of the message that is contained in
#cfcatch.message# in the causeError.cfm page.

Note: When you create a ColdFusion page that communicates with Flash, ensure that the ColdFusion page works before
using it with Flash.



Using Flash Remoting Update

You can use Flash Remoting Update to create Rich Internet Applications by using Adobe ColdFusion with Adobe
Flash Builder or earlier versions of Flex Builder, with the advanced data retrieval features of ColdFusion, such as the
cfpop, cfldap, and cfquery tags. In addition, you can use Flash Remoting Update to create Flash Forms and SWF
files that contain features, such as server call backs and customized user interface.


Prerequisites for using Flash Remoting Update
You can use Flash Remoting Update with all configurations of ColdFusion (server, multiserver, and J2EE) on all the
platforms that ColdFusion supports.

To use Flash Remoting Update, you must have the following installed:

* Flex 2 SDK or later, Flex Builder 2 or later, Flash Builder 4.


                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     620
Flex and AIR Integration in ColdFusion




   Note: With ColdFusion 9, you will need to install LiveCycle Data Services 2.6.1 manually because it is not available
   as an option with the ColdFusion installer. However, ColdFusion 9 is available with the BLAZE DS installation, which
   allows messaging support. For more information about manually installing LiveCycle Data Services ES 2.6.1, see
   Installing LiveCycle Data Services Manually in the Installation Guide.

* Flash Player 8.5 or later


Configure Flex Compilation
You use Flash Builder or the Flex SDK to compile Flex applications into SWF files. To use the Flash Remoting Update,
these programs must use the ColdFusion services-config.xml file when compiling the MXML.

You need to configure Flash Builder to use the ColdFusion configuration file, or specify the file when you use the SDK
to compile your application (as described in Compile and Run the application).


Configure Flex Builder 2 to use the ColdFusion configuration file
When you use the Flex Builder Project Setup Wizard and select ColdFusion as the server type, the wizard configures
Flex Builder to use the services-config.xml file for you. Use the following steps to configure your project:

1 Select File> New> Flex Project to open the New Flex Project Wizard. and enter the appropriate information in the
   first sections of the Create a Flex project page.

2 Select one of the radio buttons, as follows:

    * Select ColdFusion Flash Remoting to compile in Flex Builder.

    * If you installed LiveCycle Data Services with ColdFusion and want to use messaging or data management, select
       Flex Data Services.

3 If you select Flex Data services, select whether to compile the application locally in Flex Builder or on the
   application server where the page is viewed. Do not select to compile code that you deploy on the server; this option
   is for development purposes only.

4 Click Next and complete creating the project, then click Finish.

If you select Basic on the first Create a Flex Project page, and decide later to compile the application for use with
ColdFusion, configure Flex Builder manually, as follows:

1 Select Project > Properties.

2 Select Flex Complier in the right pane of the Properties dialog.

3 In the Additional Compiler arguments add -services= followed by the absolute path to the services-config.xml
   file in the local ColdFusion installation. For example, on a Windows system with a default ColdFusion stand-alone
   installation, specify the following argument string.

    -services=C:/ColdFusion9/wwwroot/WEB-INF/flex/services-config.xml


Configure Flex Builder 3 to use the ColdFusion configuration file
When you use the Flex Builder Project Setup Wizard and select ColdFusion as the server type, the wizard configures
Flex Builder to use the services-config.xml file for you. Use the following steps to configure your project:

1 Select File> New> Flex Project to open the New Flex Project Wizard. and enter the appropriate information in the
   first sections of the Create a Flex project page.

2 In the Server technology section of the Create a Flex project page, select ColdFusion as the Application server type,
   and select Use remote object access service.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     621
Flex and AIR Integration in ColdFusion




3 Select one of the radio buttons, as follows:

    * Select ColdFusion Flash Remoting to compile in Flex Builder.

    * If you installed LiveCycle Data Services and want to compile the application on the server, select LiveCycle Data
       services in Flex Builder 3.

4 Click Next to open the Configure ColdFusion page, and enter the required information. If you selected LiveCycle
   Data services in step 3, you can select to compile the application locally or on the server. Select to compile on the
   server only when you are developing your application, for convenience. Do not select to compile on the server code
   that you deploy, because the MXML page is not compiled to a SWF file until the user requests it, and the compiler
   does not create an HTML wrapper page.

5 Click Finish to complete the configuration.

If you do not specify ColdFusion in the Server technology section of the Create a Flex project page, and decide later to
compile the application for use with ColdFusion, configure Flex Builder manually, as follows:

1 Select Project > Properties.

2 Select Flex Complier in the right pane of the Properties dialog.

3 In the Additional Compiler arguments add -services= followed by the absolute path to the services-config.xml
   file in the local ColdFusion installation. For example, on a Windows system with a default ColdFusion stand-alone
   installation, specify the following argument string.

    -services=C:/ColdFusion8/wwwroot/WEB-INF/flex/services-config.xml



Specify a CFC
To specify a CFC to connect to, you do one of the following:

* Specify the dot-delimited path from the web root to the CFC in the MXML.

* Create a named resource for the CFC. Creating this resource is like registering a data source; you then use the
   resource name in your XML.


Specify the CFC in the MXML
To specify the CFC in your MXML, use code such as the following:

<mx:RemotObject
     id="myCfc"
     destination="ColdFusion"
     source="myApplication.components.User"/>

ColdFusion 9 supports BlazeDS that allows messaging support for ColdFusion. When you install ColdFusion, the
following files are added to the /WEB-INF/flex directory:

* remoting-config.xml

* messaging-config.xml

* services-config.xml

* proxy-config.xml

The destination ColdFusion is preconfigured in remoting-config.xml. The default source value for this destination is
the wildcard, *. For more information about the changes in Flash Remoting for ColdFusion 9, see Changes in the XML
configuration files for Flash Remoting in ColdFusion 9 and ColdFusion 9.0.1.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      622
Flex and AIR Integration in ColdFusion




You do not have to use the ColdFusion destination if you have configured other valid destinations in the configuration
file. In this case, the destination definition must specify * as the value of its source element. If you specify a source
other than * in remoting-config.xml, then that source definition overrides the source specified in the MXML.

For details of defining a destination, see Create a named resource for a CFC.


Create a named resource for a CFC
1 Edit the WEB-INF/flex/remoting-config.xml file by adding a destination entry for the CFC, for example:

    <service id="remoting-service" class="flex.messaging.services.RemotingService"
    messageTypes="flex.messaging.messages.RemotingMessage">
          <adapters>
          <adapter-definition id="cf-object"
          class="coldfusion.flash.messaging.ColdFusionAdapter" default="true" />
          <adapter-definition id="java-object"
          class="flex.messaging.services.remoting.adapters.JavaAdapter" />
          </adapters>


               <default-channels>
               <channel ref="my-cfamf" />
               </default-channels>
                     <destination id="ColdFusion">
               <channels>


               <channel ref="my-cfamf" />
               </channels>
               <properties>
               <source>*</source>
               </properties>
          </destination>
    </service>

    The source attribute specifies the dot notation to the CFC from the web root (the classpath to the CFC).

    The channel-ref tag refers to the channel-definition in the services-config.xml file. In the preceding sample, the my-
    cfamf channel-definition has been referenced, which looks similar to the following:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   623
Flex and AIR Integration in ColdFusion




   <channel-definition id="my-cfamf" class="mx.messaging.channels.AMFChannel"> <endpoint
   uri="http://{server.name}:{server.port}{context.root}/flex2gateway/"
   class="coldfusion.flash.messaging.CFAMFEndPoint" />
         <properties>
               <polling-enabled>false</polling-enabled>
               <serialization>
                    <enable-small-messages>false</enable-small-messages>
               </serialization>
               <coldfusion>
                    <access>
                          <use-mappings>true</use-mappings>
                          <method-access-level>remote</method-access-level>
                    </access>
                    <use-accessors>true</use-accessors>
                    <use-structs>false</use-structs>
                    <property-case>
                          <force-cfc-lowercase>false</force-cfc-lowercase>
                          <force-query-lowercase>false</force-query-lowercase>
                          <force-struct-lowercase>false</force-struct-lowercase>
                    </property-case>
               </coldfusion>
         </properties>
   </channel-definition>

2 Restart the ColdFusion server.

The following table describes the XML attributes for remoting-config.xml:


Element                                Description

destination id                         The destination attribute that the MXML mx:RemoteObject tag must specify to access the
                                       CFC.

channels                               A container for one of more child channel attributes specifying the AMF channels to use to
                                       access the ColdFusion server.

channel-ref                            Reference to the channel-definition id specified in the services-config.xml file.

source                                 The dot-delimited file path to the CFC, from the cfWebRoot, or, if the use-mappings property is
                                       true, an entry in the ColdFusion Administrator Mappings page.


access                                 Properties that control how the CFC is accessed on the ColdFusion server.


The following table lists the XML attributes for services-config.xml:


Elements                               Description

channel-definition id                  Channel definition

coldfusion                             Contains tags to set access levels, mappings to find CFCs, access to public or remote methods.

access                                 define the resolution rules and access level of the CFC being invoked

use-mappings                           Use the ColdFusion mappings to find CFCs, by default only CFC files under your web root can be
                                       found.

method-access-level                    Allow "public and remote" or just "remote" methods to be invoked




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                           624
Flex and AIR Integration in ColdFusion




Elements                               Description

use-accessors                          Whether the Value Object CFC has getters and setters. Set the value of use-accessors to true if
                                       there are getters and setters in the Value Object CFC.

use-structs                            Set the value of use-structs to true if you don't require any translation of ActionScript to CFCs. The
                                       assembler can still return structures to Flex, even if the value is false. The default value is false.

force-cfc-lowercase                    Whether to make property names, query column names, and structure keys lowercase when
force-query-lowercase                  converting to ActionScript. Query column names must precisely match the case of the
force-struct-lowercase
                                       corresponding ActionScript variables. The default value is false.



Use the CFC

Use the CFC in your application:
1 In the MXML file, you use the <mx:RemoteObject> tag to connect to your CFC named resource. With this
   connection, you can call any remote method on the CFC.

2 If you created a destination for the CFC in the remoting-config.xml file, specify the destination name in the
   mx:RemoteObject tag; for example:

    <mx:RemoteObject
         id="a_named_reference_to_use_in_mxml"
         destination="CustomID"
         result="my_CFC_handler(event)"/>

   If you did not create a destination for the CFC, specify the ColdFusion destination and the CFC path in the
   mx:RemoteObbject tag; for example:

    <mx:RemotObject
         id="myCfc"
         destination="ColdFusion"
         source="myApplication.components.User"/>

3 Call a CFC method, for example, as the following example shows:

    <mx:Button label="reload" click="my_CFC.getUsers()"/>

   In this example, when a user presses a button, the Click event calls the CFC method getUsers.

4 Specify a handler for the returned result of the CFC method call for the <mx:RemoteObject> tag, as the following
   example shows.

    private function my_CFC_handler( event:ResultEvent )
   {
   // Show alert with the value that is returned from the CFC.
   mx.controls.Alert.show(ObjectUtil.toString(event.result));
   }



Compile and Run the application
You can compile and run your application from Flash Builder or without using Flash Builder. The techniques you can
use also depend on whether you have installed LiveCycle Data Services.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       625
Flex and AIR Integration in ColdFusion




Compile and run the application using Flash Builder
To compile and run an application using Flash Builder, make sure that Flash Builder is configured as described in
"Configure Flex Compilation" on page 620. Compile your application normally to create a SWF file. When you
configure your Flash Builder project you can specify the location in which to place it. By default, Flash Builder attempts
to place the SWF file and an HTML wrapper page under the web root. You can then run the application as appropriate,
for example, by requesting the HTML wrapper for the SWF file in a browser.


Compile and Run the application without Flash Builder
To compile the application directly using the SDK, set the Flex compiler to use the ColdFusion services-config.xml file.
Set the Flex compiler by adding to the mxml command line " -services=" followed by the absolute path to the
services-config.xml file in the local ColdFusion installation. For example, on a Windows system with a default
ColdFusion standalone installation, specify the following argument string.

-services=C:/ColdFusionCentuar/wwwroot/WEB-INF/flex/services-config.xml




Offline AIR Application Support

ColdFusion 9 provides offline AIR application support, which includes data persistence and synchronization. These
features let an AIR application use a local SQLite database that represents data on the ColdFusion server.

You cannot use these features in applications built with Flash, which run in a browser or Flash Player. These features
only support AIR applications with intermittent connectivity to the ColdFusion data provider. They enable users to
run the AIR application offline and then synchronize data with the ColdFusion application the next time the
application runs online.

To support offline AIR data access, you code ActionScript elements on the client side and CFML on the server side.

Note: Some of the code in the following discussion uses an AIR application that displays and updated an Employee
database that ColdFusion manages for its sample code. However, the snippets below are not all from this example, and
do not make up a complete or consistent application.


More Help topics
"Offline AIR application support in ColdFusion 9.0.1" on page 646


ColdFusion server side
The ColdFusion application uses a CFC to represent the data being exchanged and synchronized. For example, you
could have an ORM employee component with a structure as follows:

<cfcomponent persistent="true" displayname="EMP">
     <cfproperty name="id" type="numeric" fieldtype="id" generator="native">
     <cfproperty name="firstName" type="string">
     <cfproperty name="lastName" type="string">
     ...
     <cfproperty name="countryCode" type="string">
</cfcomponent>

You can also use a traditional non-ORM CFC. In this case, the Fetch and Sync Methods use the cfquery tag and
related tags and function for database operations.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         626
Flex and AIR Integration in ColdFusion




To manage interactions with the AIR application and keep the data synchronized, ColdFusion application uses a
component called the SyncManager. The SyncManager implements the CFIDE.AIR.ISyncManager interface. The
component has two functions:

* A fetch function that the AIR application calls to get data from ColdFusion. This function is not part of the
   ISyncManager interface, but is required. The function can have any arbitrary name, but is called fetch by
   convention.

* A sync function that the AIR application calls to synchronize the ColdFusion and AIR data sources when the
   application updates or changes data. This function takes three parameters:

   operations An array of operations to perform INSERT, UPDATE, or DELETE.

   clientObjects An array of objects, where each item in the array represents the client's current view of the data
   to be synchronized.

   originalObjects An array of objects, each item in this Array represents the corresponding original data from the
   server (if any), such as an existing employee record that a user is updating. For an INSERT operation, this object is
   null. For a DELETE operation, this object is normally the same as the current data.

Incase of Conflict during Sync process, the sync function returns to the AIR client an Array of
"CFIDE.AIR.Conflict.cfc" objects. Each of this Conflict object consists of a single serverObject element. The sync
function sets the element to equal the server copy of the record that is in conflict. The client application can then handle
the conflict as described in "Conflict management" on page 635.


Server-Side notes
* When the sync function performs a DELETE operation, it gets the primary key ID from the OriginalObject of the
   Sync method, as the ClientObject is NULL. For update and insert operations, use the ClientObject key value.

* When you do an INSERT operation, the CFC checks whether the OriginalObject parameter of the sync method is
   a simple value, as in the following code:

   {NOT IsSimpleValue(OriginalObject)}

* In an INSERT operation, OriginalObject passed to the Sync function is null. So if you attempt to retrieve any of its
   properties, you get a Method NOT Found error. For Example, OriginalObject.GetID results in a Method GetID()
   not found error. So, for Insert operation, use ClientObject to access various fields.

* While a ColdFusion application can use cfquery to directly manage the database, most AIR applications are
   expected to use the ORM feature. The discussion here uses ColdFusion ORM for server-side data management.

* You may see the following kind of error message if you are using ColdFusion 8 Remoting with AIR offline
   applications, which have server side "Sync" method using ORM EntitySave()/EntityDelete() methods.

   Error handling message: flex.messaging.MessageException: Unable to invoke CFC - a different
   object with the same identifier value was already associated with the session: [address#1].
   Root cause:org.hibernate.NonUniqueObjectException: a different object with the same
   identifier value was already associated with the session: [address#1]

   You may also encounter this error with ColdFusion 9 Remoting but only for EntityDelete method.

   To resolve this sort of error, call your EntitySave/EntityDelete method in following way in "Sync" method.

   <cfif operation eq "INSERT" OR operation eq "UPDATE">
         <cfset obj = ORMGetSession().merge(clientobject)>
         <cfset EntitySave(obj)>
   <cfelseif operation eq "DELETE">
         <cfset obj = ORMGetSession().merge(originalobject)>
         <cfset EntityDelete(obj)>
   </cfif>



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    627
Flex and AIR Integration in ColdFusion




* In case of a conflict, the sync function returns an array of "CFIDE.AIR.Conflict" objects to the client. There are
   four properties a conflict object can have: operation,serverobject,clientobject,originalobject.

   The serverobject property of the conflict object must be a user-defined CFC type that represents the server-side
   database table. The following example generates a conflict object with a valid ServerObject property of type
   employee.cfc, which represents the Employee table:

   <cfset serverobject = EntityLoadByPK("employee",originalobject.getId())>
   <cfset conflict = CreateObject("component","CFIDE.AIR.conflict")>
   <cfset conflict.serverobject = serverobject>
   <cfset conflict.clientobject = clientobject>
   <cfset conflict.originalobject = originalobject>
   <cfset conflict.operation = operation>
   <cfset conflicts[conflictcount++] = conflict>
   <cfreturn conflicts>

   If you are using ColdFusion ORM, you can replace the preceding example with the following code.

   <cfset conflict = CreateObject("component","CFIDE.AIR.Conflict")
   <cfset serverobject = EntityLoadByPK("employee",#res.IDENTITYCOL#)>
   <cfset conflict.SetServerobject(serverobject)>

* When an AIR client with stale data tries to update an already deleted record from the database, server throws the
   conflict, and the client's conflict handle, which has the KeepAllServerObjects or KeepServerObject method
   accepts the changes from the server. However, the client method does not delete the stale record, which no longer
   exists in the server database, from the client database.

   To prevent this issue: The serverObject property of the conflict object returned by the server must be null, if the
   record that the client requests for updating is no longer in the database. For example:

   <cfset serverobject = EntityLoadByPK("employee",originalobject.getId())>
   <!----If the operation is INSERT, serverObject is also NULL.hence NEQ condition---->
   <cfif not isdefined('serverobject') and operation NEQ "INSERT" >
   <cflog text="CONFLICT::SERVER OBJECT NOT FOUND, RECORD MAY BE DELETED ALREADY">
   <cfset conflict = CreateObject("component","CFIDE.AIR.conflict")>
   <cfset conflict.clientobject = clientobject>
   <cfset conflict.originalobject = originalobject>
   <cfset conflict.operation = operation>
   <cfset conflicts[conflictcount++] = conflict>
   <cfcontinue>
   </cfif>



Offline AIR application code constructs
To code an AIR application that synchronizes with ColdFusion, include the cfair.swc file in your AIR project. This file
contains all the ColdFusion client-side code to support interactions between AIR and ColdFusion. The file is installed
with ColdFusion in the cf_webroot/CFIDE/scripts/AIR directory. In Flash Builder, specify the swc file location in the
Project > Properties > FlexBuildPath > Library Path > Add SWClibrary dialog.


Data object
The AIR application represents the managed data in an ActionScript object that corresponds to the ColdFusion-side
data CFC. For example, in the Employee example, the AIR application has an Employee.as file containing an Employee
ActionScript class that corresponds to ColdFusion employee.cfc:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                             628
Flex and AIR Integration in ColdFusion




package test.basic
      {
            [Bindable]
            [RemoteClass(alias="AIRIntegration.employee")]
            [Entity]
            public class Employee
            {
            /** The user id of the employee **/
            [Id]
            public var id:int;
            public var firstName : String;
            public var lastName : String;
            public var displayName : String;
            ....
            public var countryCode : String = 'US';
            ....
            }

Note: You do not need to create any SQLite databases or tables; they are created automatically. For example, once the
Employee class is defined as above, the first time you invoke the class, the equivalent SQLite table is created for server data
persistence.


Data object metadata
You use the following metadata to define the data object:


 Metadata Element                             Purpose

 [Entity]                                     Specifies that instances of this class can be persisted to the SQLite database. This element is
                                              required.

 [Table(name="tableName")]                    The name of the SQLite table in which the object is to be stored. Defaults to the name of the
                                              class.

 [Id]                                         Precedes a field definition. Indicates that the field is a primary key in the table. For composite
                                              keys, use Id tags on all the primary key fields.

 [Transient]                                  A Boolean value specifying whether the property or field is persistent. A True value indicates
                                              that the field is not persistent and so it is not a part of the client side Sqlite table.

 [Column(name="name",                         Specifies the SQLite database column that contains data for this field.
 columnDefinition="TEXT|INTEGER|
                                              name: Column name. If not specified, defaults to the property name.
 FLOAT|BOOLEAN|VARCHACHAR",
 nullable=true|false, unique=true|false)]),   columnDefinition: The SQL Datatype to use for the column.

                                              nullable: Specifies whether a field can have a null value.

                                              unique: Specifies whether the value for each field must be unique within the column.

 [RemoteClass]                                Used for all remote objects, not just ColdFusion. The alias attribute identifies the
                                              corresponding class on the remote server. This information is used to map between
                                              ActionScript data types and the remote data types.

                                              It is mandatory that you specify the [RemoteClass] metadata tag for the ActionScript classes
                                              or entities that map with the server-side CFC. If you do not specify this metadata tag, you get
                                              a runtime error. For example, you specify the alias name for the Address entity as follows:
                                              [RemoteClass(alias="myFolder.AIRIntegration.Address")]

                                              The alias name must be unique within the AIR application.


Note: For private properties in a class, set [Column] metadata on the accessor functions (getxxx and setxxx) and not on
the private property, as shown in the following code:





                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     629
Flex and AIR Integration in ColdFusion




private var name:String; // Private property
[Column(name="FNAME",columnDefinition="VARCHAR")]
public function set fname(name:String):void // accessor function
{
           this.name = name;
}
public function get fname():String // accessor function
{
           return name;
}



Client Side
ColdFusion 9 extends offline application support to the client side of the application by letting you code ActionScript
elements on the client side. The data that is exchanged and synchronized on the client side is managed through
persistent objects in the local or offline database.


Managing relationships
The ActionScript persistent framework lets you define the following relationship types between two persistent objects.

* one-to-one

* one-to-many

* many-to-one

* many-to-many

To understand how the persistent framework handles relationships, let us consider an example of the Employee and
Department objects in a database.

If you do not specify attribute values, the default values are taken as follows:

* The default table name is the class name.

* The default value for columnDefinition is the ActionScript type of the field.

* The default value for referencedColumnName is the primary key of the target entity.

*   The default value for targetEntity is the ActionScript type of the referring field.

Note: In case you are using ORM CFCs, the remotingFetch attribute in the <cfproperty> tag is set to false by default for
all relationships. You must set this attribute to true to retrieve data on the client side.


One-to-one relationship
Consider a one-to-one relationship where one employee belongs to a single department. You can use code like the
following to define a one-to-one mapping between the Department and Employee objects with DEPTID as the foreign
key column name.

[Entity]
[Table(name="employee")]
public class Employee
      {
           [Id]
           var id:uint;
           [OneToOne(targetEntity="Department"|fetchType="EAGER(default)|LAZY")]
           [JoinColumn(name="DEPTID", referencedColumnName="DEPT_ID")]
           var dept:Department;
      }



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   630
Flex and AIR Integration in ColdFusion




The [JoinColumn]tag specifies the foreign key column and all the attributes of the column tag. Do not specify
[JoinColumn] for both the entities in the relationship. For example, in the one-to-one relationship between the
Department and Employee objects, specify [JoinColumn]only for one of the entities depending on the direction of
the relationship.

referencedColumnName specifies the primary key column that it refers to. Class indicates the target entity, which is
Department in this example.

The default fetchType value is EAGER. See "Lazy loading and fetch type" on page 631 for information on fetch types.


One-to-many relationship
Consider a one-to-many relationship where one employee belongs to many departments. You can use code like the
following to define a one-to-many mapping between the Department and Employee objects.

public class Employee
     {
           [Id]
           var id:uint;
           [OneToMany(targetEntity="Department",mappedBy="department",
           fetchType="EAGER|LAZY(default)")]
           var depts:ArrayCollection;
     }

There is no column specified in the Employee table but refers to the field in the Department entity that points to the
Employee entity.

The default fetchType value is LAZY. See "Lazy loading and fetch type" on page 631 for information on fetch types


Many-to-one relationship
Consider a many-to-one relationship where many employees belong to a single department. You can use code like the
following to define a many-to-one mapping between the Department and Employee objects.

public class Employee
     {
           [Id]
           var id:uint;
           [ManyToOne(targetEntity="Department",fetchType="EAGER(default)|LAZY")]
           [JoinColumn(name="deptId",referencedColumnName="DEPT_ID")]
           var dept:Department;
     }

The default fetchType value is EAGER. See "Lazy loading and fetch type" on page 631 for information on fetch types


Many-to-many relationship
Consider a many-to-many relationship where many employees belong to many departments. You can use code like
the following to define a many-to-many mapping between the Department and Employee objects.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     631
Flex and AIR Integration in ColdFusion




public class Employee
     {
           [Id]
           [Column(name="ID")]
           var id:uint;
           [ManyToMany(targetEntity="Department",
           fetchType="EAGER|LAZY(default)")]
           [JoinTable(name="EMP_DEPT")]
           [JoinColumn(name="EMPID", referencedColumnName="ID")]
           [InverseJoinColumn(name="DEPID", referencedColumnName="DEPTID")]
           var depts:ArrayCollection;
     }

The default fetchType value is LAZY. See "Lazy loading and fetch type" on page 631 for information on fetch types.

For a many-to-many relationship, you specify metadata like the following only on one of the entities and not both.

[JoinTable(name="ORDER_PRODUCT")]
[JoinColumn(name="ORDER_ID",referencedColumnName="oid")]
[InverseJoinColumn(name="PRODUCT_ID",referencedColumnName="pid")]

The[JoinColumn]tag specifies the foreign key column and all the attributes of the column tag. The
[InverseJoinColumn] tag specifies the reference to the joining entity in the [JoinTable] tag. In this example, join
table "EMP_DEPT" has a column named "DEPID", which refers to the "DEPTID" column of the Department table.

The[JoinTable] tag defines the join table for the many-to-many relationship specifying the join column and inverse
join column. In this example, a join table named "EMP_DEPT" is created in the Offline SQLite DB with a many-to-
many relationship between the Employee and Department tables.


Lazy loading and fetch type
The ActionScript persistent framework supports lazy loading although it may not be as intuitive because of the
asynchronous connection with the database.

The fetch type EAGER or LAZY determines the fetch type for the relationship that is loaded. An EAGER fetch type loads
the relationship and fetches data when the call is first made. A LAZY fetch type loads the relationship and fetches data
only when an explicit fetch call is made. The default value for the fetch type is EAGER and the default value for
ignoreLazyLoad is false.

When you specify fetchType="EAGER" at the class-definition level, the loadByPk function always loads the related
object, irrespective of the value you specify for the ignoreLazyLoad parameter.

When you specify fetchType="LAZY" at the class-definition level, you can have two possibilities:

* When you specify the ignoreLazyLoad parameter as true, the related object is also loaded. For example, if you
   have two related objects Address and Customer, and specify loadByPK(Customer,{id:3},true), the Address
   object is also loaded.

* When you do not specify any value for the ignoreLazyLoad parameter, it takes the default value that is false, and
   the related object is not loaded. For example, if you have two related objects Address and Customer, and specify
   loadByPK(Customer,{id:3}), the Address object is not loaded.


Cascading options
Cascading lets you specify the operations to be cascaded from the parent object to the associated object. The supported
operations are INSERT, UPDATE, and DELETE. The cascadeType attribute lets you set any of the following values.

ALL If The source entity is inserted, updated, or deleted, the target entity is also inserted, updated, or deleted.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     632
Flex and AIR Integration in ColdFusion




PERSIST If The source entity is inserted or updated, the target entity is also inserted or updated.

REMOVE If The source entity is deleted, the target entity is also deleted.

The one-to-one, one-to-many, many-to-one, and many-to-many relationships are all supported by cascading. You can
use code like the following to specify the cascading options:

ManyToMany(cascadeType="ALL or PERSIST or REMOVE")

If you do not specify the cascadeType option, only the source entity is persisted or updated.

When you specify cascadeType='ALL or REMOVE'to remove the parent object and the associated child objects, load
the parent object using load***() method and pass it through session.remove(parentObj). If you do not use this
method of loading, only the parent object gets deleted from SQLite database and the child objects remain.

Note: If you have enabled lazy loading by specifying fetchType='LAZY' at the entity level, when you load a parent object
using load***() method, the child objects are not loaded. When you specify cascadeType='ALL or REMOVE' and
try to delete the parent object by passing it through session.remove(parentObj), it does not delete the child objects.
To overcome this limitation, use the load***() method with ignorelazyloading='true'.


Using the AIR SyncManager class to manage data
The AIR application uses the SyncManager class to fetch data from the server and synchronize the local data with the
ColdFusion data source. The SyncManager uses a coldfusion.air.Session object to manage the session between the
client and the local SQLite database, and uses calls to the following methods in the ColdFusion sync manager CFC:

* fetch to get data from the remote system

* sync to synchronize the local and remote data

The following text describes basic functionality that you must implement. For details on the SyncManager and Session
classes, and other classes in the coldfusion.air package, see ActionScript 3.0 Reference. Alternatively, you can see the
standalone Adobe ColdFusion ActionScript Language Reference, which is accessible through the Documentation link
on the Resources page of the ColdFusion Administrator.

The AIR application init() function creates and configures a SyncManager instance, and fetches the initial data from
ColdFusion as shown in the following code:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    633
Flex and AIR Integration in ColdFusion




private function init():void
{
syncmanager = new SyncManager();
//The ColdFusion server and port. Port without double quotes as it is


//expected to be integer and IP is taken as String.
syncmanager.cfPort = CFServerPort;
syncmanager.cfServer = "CFServerIP";


//The CFC that implements the ColdFusion sync manager. Here
//AIRIntegration is the user defined folder under webroot.
syncmanager.syncCFC = "AIRIntegration.empManager";


//Specify a user-defined CF destination,if not specified, default destination
//'ColdFusion' will be used
syncmanager.destination = 'USerDefinedCFDestination'


//The event listener for conflict events returned byt the CFC
syncmanager.addEventListener(ConflictEvent.CONFLICT, conflictHandler);


//The local database file
var dbFile:File = File.userDirectory.resolvePath("EmpManager.db");


//Create a session object, which handles all interactions between the
//AIR application and the SQLite database.
var sessiontoken:SessionToken = syncmanager.openSession(dbFile, 999);


//Add a responder for handling session connection results.
sessiontoken.addResponder(new mx.rpc.Responder(connectSuccess,
connectFault));
}


Fetching data from the server
Use the SyncManager fetch method to fetch data from the ColdFusion server by calling the fetch method of the server
data object. The syncManager.fetch method takes the name of the CFC fetch method (typically fetch) as its first
parameter, followed by any CFC fetch method parameters.

The syncManager.fetch method returns an AsyncToken object that provides access to the data. The function
returns the token synchronously. The ColdFusion CFC response returns asynchronously. Therefore, call the token's
addResponder method to specify a responder that handles the responses for successful and failed fetches.

To fetch the initial data from the server, you can include the following code in the Application init() method.

// fetch the data.
var token:AsyncToken = syncmanager.fetch("fetch");
//Specify the responder to handle the fetch results.
token.addResponder(new mx.rpc.Responder(fetchSuccess, fetchFault));


Managing the local database
You use a Session object to manage the data in the local SQLite database. You call the syncmanager.openSession
method to create a session with a specific database file. The method returns a SessionToken token, and the
SessionToken.session property provides access to the session. You use code in the token's openSessionSuccess
responder event handler to provide access to the session object. This way, you do not access the session, and therefore
the database, until it is successfully opened.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       634
Flex and AIR Integration in ColdFusion




The following code expands on the session initialization code that was shown above. and shows an openSessionSuccess
event handler that uses the session to save the contents of the remote database in the local image. In this example, users
is the array collection fetched from server:

var dbFile:File = File.userDirectory.resolvePath("basic.db");
var token:SessionToken = syncmanager.openSession(dbFile, 113);
token.addResponder(new mx.rpc.Responder(openSessionSuccess, openSessionFault));
function openSessionSuccess(event:SessionResultEvent):void
{
           //Initialize a variable for access to the session.
           var session:Session = event.sessionToken.session;
           //Save the remote database data in the local database.
           //users is the array collection fetched from server
           var saveToken:SessionToken = session.saveCache(users);
           //Add responder event handlers for successful and failed saves.
           saveToken.addResponder(new mx.rpc.Responder(saveSuccess, saveFault));
}

Once you have access to the session, you can get (load) data from the SQLite database, and insert, delete, and update
data database by calling the session objects methods. For details on the session object methods, see ActionScript 3.0
Reference. Alternatively, you can see the standalone Adobe ColdFusion ActionScript Language Reference, which is
accessible through the Documentation link on the Resources page of the ColdFusion Administrator.


Notes:
* The SQLite database doesn't validate column types when it creates a table. If you give it an invalid value for column
    data type, it creates the column with that type.

* When you pass a unique integer ID parameter (one that is not used in the application) to the OpenSession method,
    the method creates an intermediate database file, which tracks the client changes to be committed on the server. If
    you use more than one database in a single application, use a unique ID for each database. Using a single ID ensures
    that you use the correct database for each client-side transaction.

* For asynchronous calls (such as SaveCache) that save fetched data in the local database, the call result can be
    available by using the session token when the call returns, before the responders are added. This situation occurs if
    the SaveCache operation tries to save null data. That is, if the fetch operation returned null data. In such cases, a
    responder is not required.

    There are two ways to handle this situation:

    1   Check whether the result property of the session token returned by the function is null:

       if (token.result == null) {
                  Add the responder here
       }
       else {
                  Directly use the token.result
             }

    2 Check that the ArrayCollection that is input to the SaveCache function is not null. The null response indicates
       that the fetch operation did not get a result from the server:

       If (ArrayCollection != null) {
                  Call SaveCache() function
                  Add Responder to the SaveCache Token
             }
             else {
                  Handle the condition here.
             }



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         635
Flex and AIR Integration in ColdFusion




* If you call the SaveUpdate Method and a record with the specified primary key doesn't exist, the function inserts
   the record. The method updates an existing record only if the primary key exists in the client database.

* After you fetch data from the server, use only the SaveCache and SaveCacheUpdate methods to save the fetched
   data into client side database. If you use the Save function to store the fetched data, the data is marked for insert on
   server on commit, and the data you just got is written back to the server. In this case, a conflict occurs for the server
   database primary key. If the server-side logic handles this conflict by ignoring the primary key ID from the client,
   and lets the server generate a new ID, then the records are inserted, resulting in multiple copies of the data with
   different IDs.

* For AIR integration offline support, if you do not globally declare the variables for client side ActionScript classes
   but attempt to save the data records fetched from server using session.saveCache() or
   session.saveUpdateCache(), you might encounter AIR side error stackstrace with a message similar to the
   following:

   "Error: The object of type Object does not have the Entity metadata tag at
   coldfusion.air::EntityCache/addMetadata()[D:\p4\depot\ColdFusion\cf_main\tools\AIRIntegrat
   ion\OfflineSupport\src\coldfusion\air\EntityCache.as:228]"


Sending data to the server
The client SyncManager object tracks all the changes to the local data that happens through the session, so that the
local data and server data can be synchronized. The SyncManager also tracks the old data instances when data that is
already on the server is updated locally.

When the AIR application calls the Session.commit method, all changes that happened on the session are passed to
the CFC sync function. The sync function determines if there are any conflicts between the returned information. If
there are no conflicts, it updates the server data source. Otherwise, it handles the data as described in "Conflict
management" on page 635.

Note: When you call the session.commit method, if the server does not throw an error, the commit method dispatches a
CommitSucces event. This event indicates that the session.commit method has executed successfully, and the client data
has been handed to the server CFC sync method. (If an error occurs while sending data to the sync method, the client
receives a CommitFault event.) Therefore, the CommitSucces event does not mean that the server has saved the client
data, but only that it has received the data. For example, the server does not save the data if there is a conflict, but the
CommitSucces event is still dispatched. You handle conflicts separately by adding an event listener on the SyncManager.
The event Flow is the first to get the CommitSucces event, and then a ConflictEvent event.


Setting Remote Credentials for SyncManager
To authenticate the AIR client that is connecting to the ColdFusion server, you can send the remoting credentials,
which can be used on the server side under the <cflogin> tag. This is the same as setting the remoting credential for
a normal flash remoting object.

The following code contains the getRemoteObject() method in SyncManager, which gets the underlying flash
remoting object, so that you have full and same control over this as a normal flash remoting object.

syncmanager.getRemoteObject().SetRemoteCredentials("username","password");



Conflict management
Conflicts can happen in an offline application when the client modifies data that is already modified on the server. To
identify such a conflict, the session.Commit method passes the following data to the ColdFusion server sync method:

operations: An array of operations to perform INSERT, UPDATE, or DELETE.

clientobjects: An array of new data changes.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          636
Flex and AIR Integration in ColdFusion




originalobjects: An array of data that was in the client database before the change. There is no conflict in the
following circumstances:

* If your are updating a record and the data on the server is same as the data in the originalobject. The client before
   the change had the same data as the server. In this case, the server updates its data source. If the old client data
   differs from that on the server, the application must handle the conflict.

* If you are inserting a new record. In this case, there is no originalobject value and ColdFusion can insert the record
   in the data store.

You use the ColdFusion ObjectEquals function to identify conflicts. Pass the function the new instance of cfc from the
client and the original instance to check if they are equal. If they are equal, the client has been working with the latest
data. If it is not, the server can raise a conflict by returning the sever version of the instance present on the server from
the sync method by creating an instance of CFIDE.AIR.conflict.cfc, setting its serverobject property (its only
property) to the server value of the data, and returning the array of conflict objects to the AIR client.

The following code is an ideal example of sync method that uses ORM methods for syncing operations and also
handles conflicts.

<cffunction name="sync" returntype="any">
<cfargument name="operations" type="array" required="true">
<cfargument name="clientobjects" type="array" required="true">
<cfargument name="originalobjects" type="array" required="false">
<cfset conclits = ArrayNew(1)>
<cfset conflictcount = 1>


<cfloop index="i" from="1" to="#ArrayLen( operations )#">
     <cfset operation = operations[i]>
     <cfset clientobject = clientobjects[i]>
     <cfset originalobject = originalobjects[i]>
     <cfif operation eq "INSERT">
                        <cfset obj = ORMGetSession().merge(clientobject)>
                        <cfset EntitySave(obj)>
     <cfelseif listfindnocase("UPDATE,DELETE",operation) neq 0>
            <cfset serverobject = EntityLoadByPK("employee",originalobject.getId())>
            <cfif not isdefined('serverobject') >
                  <cflog text="CONFLICT::SERVER OBJECT NOT FOUND, RECORD MAY BE DELETED ALREADY">
                             <cfset conflict = CreateObject("component","CFIDE.AIR.conflict")>
                             <cfset conflict.clientobject = clientobject>
                             <cfset conflict.originalobject = originalobject>
                             <cfset conflict.operation = operation>
                             <cfset conflicts[conflictcount++] = conflict>
                             <cfcontinue>
                  </cfif>
                  <cfset isNotConflict = ObjectEquals(originalobject, serverobject)>
                  <cfif isNotConflict>
                             <cfif operation eq "UPDATE">
                                     <cfset obj = ORMGetSession().merge(clientobject)>
                                     <cfset EntitySave(obj)>




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       637
Flex and AIR Integration in ColdFusion




                             <cfelseif operation eq "DELETE">
                                     <cfset obj = ORMGetSession().merge(originalobject)>
                                     <cfset EntityDelete(obj)>
                             </cfif>
                 <cfelse><!----Conflict--->
                             <cflog text = "is a conflict">
                             <cfset conflict = CreateObject("component","CFIDE.AIR.conflict")>
                             <cfset conflict.serverobject = serverobject>
                             <cfset conflict.clientobject = clientobject>
                             <cfset conflict.originalobject = originalobject>
                             <cfset conflict.operation = operation>
                             <cfset conflicts[conflictcount++] = conflict>
                             <cfcontinue>
                 </cfif>
     </cfif>
                 </cfloop>
<cfif conflictcount gt 1>
<cfreturn conflicts>
</cfif>
</cffunction>

The CFC handling of the conflict depends on your application. In some cases, it can be appropriate to ignore the
conflict and update the server data source with the new client data. In many cases, as in the preceding example, the
CFC informs the client about the conflict by returning the server value of the data.

On the client side, you use code such as the following to register the method that handles the conflict that the server
returns.

syncmanager.addEventListener(ConflictEvent.CONFLICT, conflictHandler);
function conflictHandler(event:ConflictEvent):void
{
var conflicts:ArrayCollection = event.result as ArrayCollection;
var token:SessionToken = session.keepAllServerObjects(conflicts);
token.addResponder(new mx.rpc.Responder(conflictSuccess, conflictFault));
}

The conflictevent object contains an array of conflict objects that contain the clientinstance, originalinstance and the
serverinstance. To accept the server's data, the application calls keepAllServerObjects, which takes an ArrayCollection
that was passed to the conflict handler, or call the keepServerObject that takes an individual Conflict instance as shown
in the following code. This conflict handler simply accepts any returned server object.

function conflictHandler(event:ConflictEvent):void
{
var conflicts:ArrayCollection = event.result as ArrayCollection;
var conflict:Conflict = conflicts.getItemAt(0);
var token:SessionToken = session.keepServerObject(conflict);
token.addResponder(new mx.rpc.Responder(conflictSuccess, conflictFault));
}

Conflicts can happen in the following cases:

* When the client does an update after the server data was updated. In this case, the client was using an old instance
   of data and not the latest data on the server. The server can inform the client by creating an instance of conflict.cfc
   in the sync method and setting the server instance on it. On the client side, you can call the keepServerObject
   function in the conflict handler to resolve the conflict by updating the client database with the server instance.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        638
Flex and AIR Integration in ColdFusion




* When the client does an update but that record no longer exists on the server. Again, a conflict can be passed to the
   client from the server by creating an instance of Conflict.cfc and returning it. There is no need to set a serverobject
   property, as there is no server instance of the inserted data.

* When the client did an insert, but for example, the server data uses an autoincrement primary key field. The server,
   therefore, does not use the primary key inserted by the client. To inform the client of the correct key field value, the
   server returns the conflict cfc instance with the server instance. The ActionScript Calling keepServerObject method
   can then update the local data with the new primary key value from the server.

Note: After a commit or conflict resolution, it is recommended to synchronize the client database with the server data
source, because the server can have new data available from other clients.

ActionScript has a few reserved keywords. When you name the Class/SQLite table, ensure that you do not use any of
the reserved keywords. For example, Order is an ActionScript reserved keyword. If you name a table or class as Order,
the table creation fails. To avoid this name conflict, use the [Table(name="OrderTable")] metadata tag to override
the default name. Your code for the Order.as class could look something like the following:

package test
{
[Entity]
[Table(name="OrderTable")]
public class Order
{
public function Order()
{
}
[Id]
public var oid:uint;
public var name:String;
[ManyToMany(targetEntity="test::Product",cascadeType='ALL')]
public var products:Array;
}
}



Offline AIR application example
The example here describes how to build an offline AIR application that has a one-to-one relationship between the
Customer and Address objects in the database. You can use this example as a basis to build offline AIR applications
for the other relationship types.


Client-side (AIR application) code
Create a folder called "onetoone" in your AIR project and add the ActionScript class files: Customer.as and Address.as
with code that could be something like the following:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                        639
Flex and AIR Integration in ColdFusion




Customer.as
package onetoone
 {
           [Bindable]
           [RemoteClass(alias="AIRIntegration.custome r")]
           [Entity]


     public class         Customer
     {
           [Id]
                 public var cid:int;
                 public var name: String;
                 [OneToOne(cascadeType='ALL',fetchType="EAGER")]
                 [JoinColumn(name="add_id",referencedColumnName="aid")]


     public var address:Address;
     }
}


Address.as
package onetoone
{
           [Bindable]
           [RemoteClass(alias="AIRIntegration.address")]
           [Entity]


     public class Address
     {
           [Id]
                 public var aid:int;
                 public var street:String;
     }
}


MainApplication.mxml
Add code like the following in the MainApplication.mxml file to perform CRUD operations on the database.

Note: For Customer.as and Address.as ActionScript classes, global variables have been declared.




                                           

DEVELOPING COLDFUSION 9 APPLICATIONS                                                 640
Flex and AIR Integration in ColdFusion




<?xml version="1.0" encoding="utf-8"?>
<mx:WindowedApplic ation xmlns:mx="http://www.adobe.com/2006/mxml"
layout="absolute" creationComplete="init()">
<mx:Script>


<![CDATA[
import mx.collections.ArrayCollection;
import mx.rpc.AsyncToken;
import mx.controls.Alert;


import coldfusion.air.events.*;
import coldfusion.air.*;


import onetoone.Address;
import onetoone.Customer;


private var session:Session;
private var dbFile:File;


private var cusColl:ArrayCollection;
private var syncmanager:SyncManager;
private var add:Address;//global variable for address.as
private var cus:Customer; //global variable for customer.as


private function init():void
     {
           // Provide Credentials for Server side Connection and CFC
           syncmanager = new SyncManager();
           syncmanager.cfPort = 80;
           syncmanager.cfServer = "localhost";
           // Path of the Server side CFC from CF webroot
           syncmanager.syncCFC = "AIRIntegration.cusManager";
           // This handler is called when any conflict occurs while
           writing back changes on the server side
           syncmanager.addEventListener(ConflictEvent.CONFLICT, conflictHandler);
           // Fetch Server side DB data onto Client SQLite DB while
           starting the App
           var token:AsyncToken= syncmanager.fetch("fetch");
           token.addResponder(new mx.rpc.Responder(fetchSuccess, fetchFault));
     }


private function conflictHandler(event:ConflictEvent):void
     {
           Alert.show("conflict man!");
           var conflicts:ArrayCollection = event.result as ArrayCollection;
           // Accept Server data and write it to client side SQLite DB
           var token:SessionToken
           = session.keepAllServerObjects(conflicts);
           token.addResponder(new
           mx.rpc.Responder(conflictSuccess, conflictFault));
     }


private function conflictSuccess(event:SessionResultEvent):void
     {
           Alert.show("conflict resolved");
     }




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                      641
Flex and AIR Integration in ColdFusion




private function fetchSuccess(event:SyncResultEvent):void
     {
           var cus:Array = event.result as Array;
           cusColl = new ArrayCollection(cus);
           // Open a Session for the client side SQLite DB
           dbFile = File.userDirectory.resolvePath("onetoonesync.db");
           var sessiontoken:SessionToken
           =syncmanager.openSession(dbFile,017915);
           sessiontoken.addResponder(new
           mx.rpc.Responder(connectSuccess,connectFault));
     }


private function connectSuccess(event:SessionResultEvent):void
     {
           session = event.sessionToken.session;
           if(cusColl.length > 0)
           {
           // This operation saves it to the AIR SQLite DB
           var savetoken:SessionToken
           = session.saveCache(cusColl);
           savetoken.addResponder(new
           mx.rpc.Responder(saveCacheSuccess, savefault));
     }
     else
           {
                 Alert.show("No data available from Server to save on local DB");
           }
     }


private function saveCacheSuccess(event:SessionResultEvent):void
     {
           Alert.show("Data saved on client Sqlite DB from Server");


           /*
           A new Insert is tried here. Note that this is not a complete user interface
           application. Otherwise, typically, users need to provide inputs to populate
           the Customer/Address Objects
           */
     var cus:Customer = new Customer();
     cus.cid=12;
     cus.name="New Customer";
     var add:Address = new Address();
     add.aid = 14;
     add.street = 'New Address';
     cus.address = add;


     /*
     INSERT the new Records, this is first saved in client side SQLite DB.
     On the Commit operation this new record is saved in the Server side DB
     Notice that although you are saving the Customer object here,
     this operation saves even the binded Address Object also,
     as both the entities are CASCADED inside Customer Class
     */
     var savetoken:SessionToken = session.save(cus);
     savetoken.addResponder(new mx.rpc.Responder(savesuccess, savefault));
     }




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                             642
Flex and AIR Integration in ColdFusion




private function savesuccess(event:SessionResultEvent):void
     {
     Alert.show("Customer was Inserted Successfully");
     // Load some otehr Customer(ex: id=11) so that we can perform Update on
     that Customer
     var loadtoken:SessionToken =session.loadByPK(Customer,{cid:11});
     loadtoken.addResponder(new mx.rpc.Responder(loadCustomer,loadFault))
     }
private function loadCustomer(event:SessionResultEvent):void
     {
     var cus1:Customer = event.result as Customer;
     cus1.name = "UpdateCustomerName";
     var add1:Address = new Address;
     add1.aid = 22;
     add1.street = 'UpdatedCustomerAddress';
     cus1.address = add1;
     /*
     Let's call update now and save it to Client side SQLite DB
     */
     var savetoken:SessionToken = session.update(cus1);
     savetoken.addResponder(new mx.rpc.Responder(updateSuccess,updatefualt));
     }


private function updateSuccess(event:SessionResultEvent):void
     {
     Alert.show("Customer was updated Successfully");
     /*
     Let's Load another Customer(for example, with id 128) to perform a Delete operation on that
     */
     var loadtoken:SessionToken = session.loadByPK(Customer,{cid:128});
     loadtoken.addResponder(new mx.rpc.Responder(loadCustomerForDelete,loadFault));
     }


private function loadCustomerForDelete(event:SessionResultEvent):void
     {
     // pass the loaded customer to remove function
     var removetoken:SessionToken = session.remove(event.result);
     removetoken.addResponder(new mx.rpc.Responder(removeSuccess,removeFault));
     }
private function removeSuccess(event:SessionResultEvent):void
     {
     Alert.show("Customer was deleted Successfully");
     }


private function commit():void
     {
     /*
     Until now, you have performed Insert/Update/Delete operation on Customer/Address
     entities on the client side SQLite DB. Now use the Commit opertaion to
     send them to the Server.
     */
     var committoken:SessionToken = session.commit();
     committoken.addResponder(new mx.rpc.Responder(commitSuccess, commitFault));
     }


private function commitSuccess(event:SessionResultEvent):void
     {



                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                643
Flex and AIR Integration in ColdFusion




     Alert.show("Server has been updated with local changes");
     /*
     Now that you have completed all the operations, you can close the SQLite DB
     connection/session. It is a good practice to Close the session,
     after you complete all the operations.
     */
     var closetoken:SessionToken
     = session.close();
     closetoken.addResponder(new
     mx.rpc.Responder(sessionclosesuccess, sessionclosefault));
     }
private function sessionclosesuccess(event:SessionResultEvent):void
     {
     Alert.show("Session Close Success");
     }


     // Fault Handlers
     private function fetchFault(event:SyncFaultEvent):void
     {
     Alert.show("fetch fault" + event.toString());
     }
     private function conflictFault(event:SessionFaultEvent):void
     {
     Alert.show("conflict not resolved");
     }
     private function connectFault(event:SessionFaultEvent):void
     {
     Alert.show("connect failure" + event.toString());
     }
     private function sessionclosefault(event:SessionFaultEvent):void
     {
     Alert.show("Session Close Failed::"+event.error);
     }
     private function removeFault(event:SessionFaultEvent):void
     {
     Alert.show("Delete Operation Failed::"+event.error);
     }
     private function commitFault(event:SessionFaultEvent):void
     {




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  644
Flex and AIR Integration in ColdFusion




     Alert.show("Commit Failed::"+event.error);
     }
     private function loadFault(event:SessionFaultEvent):void
     {
     Alert.show("Load Failed::"+event.error);
     }
     private function updatefualt(event:SessionFaultEvent):void
     {
     Alert.show("update fault"+event.error);
     }
     private function savefault(event:SessionFaultEvent):void
     {
     Alert.show("Save Fault::"+event.error);
     }
]]>
</mx:Script>
<mx:Button click="commit()" name="commitbutton"
label="Commit/write local data to Server">
</mx:Button>
</mx:WindowedApplication>


Server-side code
Create the following cfc files - Application.cfc, Customer.cfc, Address.cfc, and Cusmanager.cfc with code like the
following. The AIR client interacts with the Cusmanager.cfc file, in which you specify the code to fetch and sync the
data back to the server.


Application.cfc
<cfcomponent>
           <cfset this.name = "OneTonOneExample">
           <cfset this.datasource="testorm">
           <cfset this.ormenabled="true">
           <cfset this.ormsettings={dialect = "MicrosoftSQLServer"}>
</cfcomponent>


Customer.cfc
<cfcomponent persistent="true">
           <cfproperty name="cid" fieldtype="id" >
           <cfproperty name="name" >
           <cfproperty name="address" fieldType='one-to-one'
           CFC="address" fkcolumn='aid' cascade='all' >
</cfcomponent>


Address.cfc
<cfcomponent persistent="true">
           <cfproperty name="aid" fieldtype="id" >
           <cfproperty name="street" >
</cfcomponent>




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                              645
Flex and AIR Integration in ColdFusion




Cusmanager.cfc
<cfcomponent implements="CFIDE.AIR.ISyncManager">
<!----Fetch method--->
<cffunction name="fetch" returnType="Array" access="remote">
<cfset cus = ArrayNew(1)>
<cfset cus = EntityLoad("customer")>
<cfreturn cus>
</cffunction>
<!----SYNC method--->
<cffunction name="sync" returntype="any">
<cfargument name="operations" type="array" required="true">
<cfargument name="clientobjects" type="array" required="true">
<cfargument name="originalobjects" type="array" required="false">


<cfset conclits = ArrayNew(1)>
<cfset conflictcount = 1>


<cfloop index="i" from="1" to="#ArrayLen( operations )#">
     <cfset operation = operations[i]>
     <cfset clientobject = clientobjects[i]>
     <cfset originalobject = originalobjects[i]>
           <cfif operation eq "INSERT">
                      <cfset obj = ORMGetSession().merge(clientobject)>
                      <cfset EntitySave(obj)>
     <cfelseif listfindnocase("UPDATE,DELETE",operation) neq 0>
           <cfset serverobject = EntityLoadByPK("employee",originalobject.getcid())>
           <cfif not isdefined('serverobject') >
           <cflog text="CONFLICT::SERVER OBJECT NOT FOUND, RECORD MAY BE DELETED ALREADY">
                             <cfset conflict = CreateObject("component","CFIDE.AIR.conflict")>
                             <cfset conflict.clientobject = clientobject>
                             <cfset conflict.originalobject = originalobject>
                             <cfset conflict.operation = operation>
                             <cfset conflicts[conflictcount++] = conflict>
                             <cfcontinue>
                 </cfif>


                 <cfset isNotConflict = ObjectEquals(originalobject, serverobject)>
                 <cfif isNotConflict>


                             <cfif operation eq "UPDATE">
                                     <cfset obj = ORMGetSession().merge(clientobject)>
                                     <cfset EntitySave(obj)>
                             <cfelseif operation eq "DELETE">
                                     <cfset obj = ORMGetSession().merge(originalobject)>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  646
Flex and AIR Integration in ColdFusion




                                     <cfset EntityDelete(obj)>
                             </cfif>


                 <cfelse><!----Conflict--->
                             <cflog text = "is a conflict">
                             <cfset conflict = CreateObject("component","CFIDE.AIR.conflict")>
                             <cfset conflict.serverobject = serverobject>
                             <cfset conflict.clientobject = clientobject>
                             <cfset conflict.originalobject = originalobject>
                             <cfset conflict.operation = operation>
                             <cfset conflicts[conflictcount++] = conflict>
                             <cfcontinue>
                 </cfif>
     </cfif>
           </cfloop>
<cfif conflictcount gt 1>
<cfreturn conflicts>
</cfif>
</cffunction>
</cfcomponent>



Offline AIR application support in ColdFusion 9.0.1
The AIR integration feature introduced in ColdFusion 9 has an ActionScript ORM for persisting entities in the SQLite
database present within Adobe Integrated Runtime (AIR). This release has the following enhancements for this
ActionScript ORM:

* Support for auto-generating primary keys

* Support for encrypted database (introduced in AIR 1.5).

* Cache file used by ActionScript ORM to track the operations on SQLite database is now in the
   applicationStoragedirectory instead of applicationDirectory. You can specify the location of the cahceDirectory in
   openSession API on syncmanager.

* Supports Self Join relationships for one-to-one, one-to-many, and many-to-many database relationships.

* Supports both Array and ArrayCollection for use in ActionScript Entity to represent a collection in a database
   relationship.

* ActionScript ORM logs all the SQL statements that ORM uses to persist entities into the SQLite database.

* New APIs keepClientObject and keepAllClientObjects to ensure that the server updates are not retained
   when ColdFusion server raises conflict.

* The class SessionToken is dynamic and therefore, data can be stored on the token returned from the ORM APIs.

* Supports autocommit mode


Auto-generating primary keys
This release supports primary key generation for the ActionScript ORM using the metadata tag GeneratedValue.


GeneratedValue


Description
Adding this tag on an ActionScript primary key file auto-generates primary key.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     647
Flex and AIR Integration in ColdFusion




Parameters

 Parameter           Description

 strategy            UUID uses the Flash UUID API to generate the ID (used for primary key of type string) or INCREMENT (for primary key
                     of type int).

 initialValue        Applies only for INCREMENT strategy. Specifies the initial value of the primary key. The default value is 0.

 incrementBy         Applies only for INCREMENT strategy. Specifies the integer with which the value must be incremented to generate the
                     primary key.


If the ID value is not present in the object, the value is generated and is assigned the primary key value. If the key value
is already present on the object instance, then the key generation is ignored.

For integer primary keys, the database table is checked for the presence of existing primary keys. If the highest key
value is greater than the initialValue, then the key that is generated next will be an increment of the highest key
value. For example, if the initialValue that you specify is 1, and the database (already) has a key value 5, then the
next key is generated with the value 6 (5+1, if incrementBy is set to 1).


Example
//Integer Primary Keys
===========
package test.apollo.CFSQLiteSupport.INCREMENTPK
{
      [Entity]
      [RemoteClass(alias="Customer")]
      public class Customer
      {
           public function Customer()
           {
           }
           [Id]
           [GeneratedValue(strategy="INCREMENT",initialValue=5,incrementBy=2) ]
           public var cid:int;
           public var name:String;


           [OneToOne(mappedBy="customer")]
           public var ord:Order;
      }
}




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  648
Flex and AIR Integration in ColdFusion




//String Primary Keys
===========
package test.apollo.CFSQLiteSupport.UUIDPK
{
     [Entity]
     [RemoteClass(alias="Customer")]
     public class Customer
     {
           public function Customer()
           {
           }
           [Id]
           [GeneratedValue(strategy="UUID") ]
           public var cid:String;
           public var name:String;


           [OneToOne(mappedBy="customer")]
           public var ord:Order;
     }
}


Encrypting the database
You can protect the database used by ActionScript ORM with an encryption key.

Use the ByteArray encryption key for syncmanager.openSession method to encrypt the database. The user-
specified database file and the cache database file (used by the ActionScript ORM) are both encrypted using the
encryption key you specify.

The key is optional.

Example

dbFile = File.userDirectory.resolvePath("customerManger.db");
     dbDir = File.applicationStorageDirectory;
     var keyGenerator:EncryptionKeyGenerator = new EncryptionKeyGenerator();
     var encryptionKey:ByteArray = keyGenerator.getEncryptionKey("UserPassword");


     var sessiontoken:SessionToken
=syncmanager.openSession(dbFile,179176,encryptionKey,dbDir);

For details on EncryptionKeyGenerator, see the section Using the EncryptionKeyGenerator class to obtain a secure
encryption key in Developing Adobe AIR 1.5 Applications with Flex.


Specifying the cache directory
The cache directory where you store the cache file can be specified using the cacheDirectory (instance of
flash.filesystem.File) for the syncmanager.openSession method.

The cacheDirectory is optional.

Note: By default, the cache file used by the ActionScript ORM is stored in the File.applicationStorageDirectory (in
ColdFusion 9, it was stored in File.applicationDirectory).

For example, see "Encrypting the database" on page 648




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  649
Flex and AIR Integration in ColdFusion




Support for self joins
Database table can be related to itself through a foreign key. A typical example is an Employee table with a manager
relationship containing the employee id of the managers (who manage the employee).

The manager id refers to another row in the same table. This is an example of one-to-one self join.

There can be one-to-many self join and many-to-many self joins with an intermediate join table.

ColdFusion 9 Update 1 has self join support for all the relationships in the ActionScript ORM.

The following ActionScript class definition for customer entity illustrates how all the self-join relationships are
defined:

     package
{


     [Bindable]
     [RemoteClass(alias="AIRIntegration.customer")]
     [Entity]
     public class Customer
     {
           [Id]
           [GeneratedValue(strategy="INCREMENT",initialValue=5,incrementBy=2)]
           public var cid:int;
           public var name:String;


           [OneToOne(cascadeType='ALL',fetchType="EAGER")]
           [JoinColumn(name="add_id",referencedColumnName="aid")]
           public var address:Address;


           // Many-to-One self Join
           [ManyToOne(targetEntity="onetoone::Customer",fetchType="EAGER")]
           [JoinColumn(name="managerId",referencedColumnName="cid")]
           public var manager:Customer;


           // One-to-one Self Join
           [OneToOne(targetEntity="onetoone::Customer",fetchType="EAGER")]
           [JoinColumn(name="spouseId",referencedColumnName="cid",unique="true")]
           public var spouse:Customer;


         // Many-to-Many self Join




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  650
Flex and AIR Integration in ColdFusion




         [ManyToMany(targetEntity="onetoone::Customer",fetchType="EAGER")]
         [JoinTable(name="CUSTOMER_PARENTS_MAPPINGS")]
         [JoinColumn(name="CUST_ID",referencedColumnName="cid")]
         [InverseJoinColumn(name="PARENT_ID",referencedColumnName="cid")]
         public var parents:Array;



         // Many-to-Many self Join
         [ManyToMany(targetEntity="onetoone::Customer",fetchType="EAGER")]
         [JoinTable(name="CUSTOMER_CHILDREN_MAPPINGS")]
         [JoinColumn(name="CUST_ID",referencedColumnName="cid")]
         [InverseJoinColumn(name="CHILD_ID",referencedColumnName="cid")]
           public var children:Array;


[OneToMany(targetEntity="onetoone::Order",cascadeType='REMOVE',mappedBy="customer",fetchType
="EAGER")]
           public var orders:Array;


     }
}


ArrayCollection to hold multiple entities
In addition to Array, you can now use ArrayCollection to hold multiple entities in a database relationship.
ArrayCollection can also be used in the ActionScript entities as Arrays are used to represent the related entities.

Example

package
{
     import mx.collections.ArrayCollection;


     [Bindable]
     [RemoteClass(alias="AIRIntegration.customer")]
     [Entity]
     public class Customer
     {
           [Id]
           [GeneratedValue(strategy="INCREMENT",initialValue=5,incrementBy=2)]
           public var cid:int;
           public var name:String;


           [OneToOne(cascadeType='ALL',fetchType="EAGER")]
           [JoinColumn(name="add_id",referencedColumnName="aid")]
           public var address:Address;


[OneToMany(targetEntity="onetoone::Order",cascadeType='REMOVE',mappedBy="customer",fetchType
="EAGER")]
           public var orders:ArrayCollection;


     }
}


Server-side configuration
See the section "Changes in the XML configuration files for Flash Remoting in ColdFusion 9 and ColdFusion 9.0.1"
on page 674.




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      651
Flex and AIR Integration in ColdFusion




Logging SQL statements
The ActionScript ORM logs all SQL statements that it executes.

The log can be configured as follows:

 Add a log target for the AIR applications as shown in the following example:

   var logTarget:TraceTarget = new TraceTarget();
   logTarget.filters = "*";
   logTarget.level = LogEventLevel.ALL;
   Log.addTarget(logTarget);

The log target is the TraceTarget where all the trace statements appear. The log target can be set to any other log using
the Flash APIs.


ColdFusion ActionScript APIs
The following two APIs have been introduced to the session class in the coldfusion.air package:


keepAllClientObjects


Description
Takes an ArrayCollection of conflict instances and keeps the client object for every conflict instance in the
ArrayCollection.


Returns
An instance of coldfusion.air.SessionToken (which is the token for keepAllClientObjects call).


Syntax

public function keepAllClientObjects(conflicts:ArrayCollection):SessionToken


Parameters

 Parameter                                      Description

 mx.collections.ArrayCollection                 An ArrayCollection of conflicts raised by the server.



Example
private function conflictHandler(event:ConflictEvent):void
{
    // Alert.show("Server returned a Conflict !");
     var conflicts:ArrayCollection = event.result as ArrayCollection;
     // Ignore Server data and retain client Data in                 SQLite DB
     var token:SessionToken = session.keepAllClientObjects(conflicts);
     token.addResponder(new mx.rpc.Responder(conflictSuccess, conflictFault));
}


keepClientObject


Description
Ensures that the client object is retained instead of the one from the server (despite server raising data conflict).

The API also ensures that the retained client object is not sent to the server as a new operation on sync.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                652
Flex and AIR Integration in ColdFusion




Returns
An instance of coldfusion.air.SessionToken associated with keepClientObject call.


Syntax

public function keepClientObject(conflict:coldfusion.air.Conflict):SessionToken


Parameters

 Parameter                                   Description

 coldfusion.air.Conflict                     The conflict that the server raises.



Example
See the example in the section keepAllClientObjects. For keepClientObject, the only difference is that you must
iterate over each conflict in the conflictarray collection.


Offline AIR SQLite API enhancements
The following new parameters for openSession:


 New Parameters       Type          Required/Optional     Description

 encryptionKey        ByteArray Optional                  Used to encrypt offline SQLite database. For details, see "Encrypting
                                                          the database" on page 648.

 cacehDirectory       File          Optional              Used to specify a custom cache directory. For details, see "Specifying
                                                          the cache directory" on page 648.



SessionToken class is dynamic
A class is dynamic in ActionScript if you can add additional key-value pairs to the instance of the class.

In this release, sessionToken is dynamic class. Therefore, you can add additional information that can be passed from
where the API is called to the success or fault handlers.

Example

private function fetchData():void
{
var token:AsyncToken= syncmanager.fetch("fetch");
token.addResponder(new mx.rpc.Responder(fetchSuccess, fetchFault));


                     // Test For SessionToken class to be Dynamic, so that Dynamic Properties could
be added
                       token.userdefined_key = "value";
             }
           public function fetchSuccess(event:SyncResultEvent):void
                 {


                    if(event.token.userdefined_key == "value")
                       { .... }
                  }


Support for AutoCommit
SyncManager supports a Boolean property autoCommit.

The default value is false.



                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      653
Flex and AIR Integration in ColdFusion




If true, the changes in the local database are committed to the server when the save, saveUpdate, and remove
methods are used as shown here:

private var syncmanager:SyncManager = new SyncManager();
syncmanager.autoCommit = true;

This functionality helps you minimize the conflicts during the synchronization with the server, particularly in the case
of auto-generation of primary key on client and serverside.


New attribute for SessionResultEvent and SessionToken
The classes SessionResultEvent and SessionToken have a new attribute autoGeneratedId that gets populated
with the auto-generated ID used by ActionScript ORM. autoGeneratedId is populated only when a key is generated
by the ActionScript ORM in that specific call.


Example
private function connectSuccess(event:SessionResultEvent):void
{
   // Generate an Order Obect
    .
    .
    .
    .
   // Save the Order
   var savetoken:SessionToken = session.save(ord);
   savetoken.addResponder(new mx.rpc.Responder(savesuccess, savefault));
}
private function savesuccess(event:SessionResultEvent):void
{
    // This is how, you can access autogenrated PK
    RememberINTPK = event.autoGeneratedId.toString();
    var loadtoken:SessionToken = session.loadByPK(Order,{oid:RememberINTPK},true);
    loadtoken.addResponder(new mx.rpc.Responder(loadsuccess,loadfailure))
}

Note: Assume that the server database generates primary keys and you choose to generate primary key on client SQL Lite
table (as shown in the example). This scenario results in a conflict which the application developer must resolve. An
option is to design your application in such a way that you minimize conflicts between client and server objects. In this
case, you can set client object primary keys as null or empty string before saving data to the database server using
serverside ORM EntitySave function.



Proxy ActionScript Classes for ColdFusion Services

Flex-based applications in AIR and Flash can access several ColdFusion services by using ColdFusion proxy
ActionScript classes. This feature is available in all Flex-based applications that run on Flash and AIR. ColdFusion
provides services corresponding to the following tags and their child subtags: cfchart, cfdocument, cfimage,
cfmail, cfpdf, cfpop. Using ColdFusion you can also upload files from the application to the server.

ColdFusion provides the following Flex proxy classes and related support classes:

* Config (configures the application for using ColdFusion services)

* Util (includes file upload support)

* Chart (cfchart)



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   654
Flex and AIR Integration in ColdFusion




* Document (cfdocument)

* Image (cfimage)

* Mail (cfmail)

* PDF (cfpdf)

* Pop (cfpop)

These classes are part of coldfusion.service.mxml package, distributed in the cfservices.swc file. You normally use these
classes in MXML tag format, using the cf namespace identifier, as in the following line:

<cf:Image id="image" action="AddBorder" source="Uploaded Image server URL" thickness="5"
color="Blue"/>

To use a ColdFusion service in an application built with Flex, you use the Config class to establish the connection, and
then use the other classes to access the ColdFusion services.

In ColdFusion 9, you can also specify the remoting destination in the Config class as well as all the proxy tags.

Note: To use ColdFusion services from Flex and AIR, you must enable access to the services as described in "Enable
ColdFusion Services" in the ColdFusion Web Services section.


About the cfservices.swc file
To use any ColdFusion service class in an application built with Flex/AIR, do the following:

1 Include the cfservices.swc file, located at /CFIDE/scripts/AIR/cfservices.swc, in your application

2 In Flash Builder, add the cfservices.swc file to your project by right-clicking Flex/AIR Project > Properties >Flex
   BuildPath >Library Path >Add SWC.

For details on all ColdFusion service classes, and other classes in the coldfusion.air package, see ActionScript 3.0
Reference. Alternatively, you can see the standalone Adobe ColdFusion ActionScript Language Reference, which is
accessible through the Documentation link on the Resources page of the ColdFusion Administrator.

For information about attributes for specific services, such as Mail or Pop, see the attributes of corresponding
ColdFusion tags and functions.


Using the Config class
Use the Config class to set the configuration parameters for ColdFusion Services, including the connection details and
event handlers. Therefore, use the Config class before using any of the service classes. The parameters set on the Config
class are global and can be overridden by the individual service proxy classes.

The following table lists the Config class parameters, normally used as attributes of a Config tag.


 Attribute                 Description

 serviceUserName           The userName set in the ColdFusion Administrator with the permission to access the specific service being
                           requested.

 servicePassword           The password set in the ColdFusion Administrator for the user name.

 cfServer                  The server name or IP address of the CF server.

 cfPort                    The port on which the CF Server is running.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                      655
Flex and AIR Integration in ColdFusion




 Attribute                 Description

 cfContextRoot             The context root if any for the CF server.

 secureHTTP                Boolean value specifying whether to use http or https to run the service.

 destination               The destination attribute can be used to specify a user defined remoting destination in WEB-INF/flex/remoting-
                           config.xml. If not specified, default ColdFusion destination is used.


You normally specify the config class using an MXML tag as follows:

<cf:Config id="conf" cfServer="CF Server IP Address/HostName" CFPort="HTTP Port on Which CF
accepts request" destination="UserDefinedRemotingDestination" >

You can also override the server settings directly in the service tag, for example:

<cf:Image id="image" action="AddBorder" cfServer="IP Address" cfPort="Port number"
source="Uploaded Image server URL" thickness="5" color="Blue"
destination="UserDefinedRemotingDestination" />



Using the Util class
The Image, PDF, and Mail services typically act on a file that has been uploaded to the server. To upload the file to your
CF server, use the Util class to run the ColdFusion Upload service. The Util class consists of two elements:

*   The UPLOAD_URL constant contains the URL on the ColdFusion server of the Upload service, relative to cf_webroot.

*   The extractURLFromUploadResponse() method takes response returned by the Upload service as input and
   returns the path of the uploaded file on the ColdFusion server.

You use the UPLOAD_URL constant and the extractURLFromUploadResponse() function in the following workflow
to upload a file and use the file in a ColdFusion service.


Event flow of the ColdFusion service
1   Use the ActionScript flash.net.FileReference APIs and the Util.UPLOAD_URL variable to upload an Image,
   PDF, or mail attachment to the server on which the action has to be performed. The upload URL to supply to the
   flash.net.FileReference APIs can be constructed as follows in the ActionScript part of the application:

   uploadURL.url =
   "http://"+conf.cfServer+":"+conf.cfPort+"/"+conf.contextRoot+"/"+Util.UPLOAD_URL;
   var variables:URLVariables = new URLVariables();
          variables.serviceusername = conf.serviceUserName;
          variables.servicepassword = conf.servicePassword;
          uploadURL.data = variables;
          uploadURL.method="POST";

    Here, specify "conf.cfServer", "conf.cfPort" and "conf.ContextRoot" in the <cf:Config> tag. Specify
   "conf.ContextRoot" only if ColdFusion is deployed as a J2EE application.

   Note: The ActionScript FileUpload functionality is out of the scope for this feature hence it is not explained in detail,
   but an example of the usage is provided in code in MAIL class section. For further information on FileUpload
   functionality see the ActionScript documentation.

2 Once the file is uploaded, the server returns an XML response containing URL of the uploaded file. Use the Util
   class function extractURLFromUploadResponse() to extract the URL from the XML

3 Use the file URL in the source attribute of the service tags.

4 When the required service tag attributes are set, run the service action by calling the following method:




                                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        656
Flex and AIR Integration in ColdFusion




    serviceObject.execute()

5 If the action succeeds, the server returns the result. If there is an exception, it returns the fault. Handle the
    ResultEvent and FaultEvent objects in the service-specific result handler and fault handler that you specify in
    the service tag, or in global handlers that you specify in the <cf:config>. The ResultEvent object contains the URL
    of the File on which the operations have been performed. Users can save this file by downloading it on their
    machine or rendering it in the application. The FaultEvent object contains the exception details that occurred on
    the server while performing the operation.

For granular control over proxy classes, you can get hold of the underlying RemotObject by using getRemoteObject()
method on the proxy class object. For example, for <cf:Mail id="mailId">, you can get it using the following code in
ActionScript.

var mailobject:RemoteObject = mailId.getRemoteObject();



Using the Mail class
The Mail class is the proxy for the ColdFusion Mail service, which provides the functionality of the cfmail tag. You
specify the required cfmail and child tag attributes for the action as Mail tag attributes. The default Mail action on this
tag is send.

The following AIR application uses the Mail Service and file upload functionality. It refers to the CFCredentials.mxml
file to reference the credentials of the user for authentication.

Following is the CFCredentials.mxml file being used for the example:

<?xml version="1.0" encoding="utf-8"?>
<mx:Panel xmlns:mx="http://www.adobe.com/2006/mxml" currentState="collapsed" toolTip="Double
Click to Collapse/Expand" creationComplete="retrieveCredential()" headerHeight="5"
layout="vertical" mouseDown="check Collapse(event )" resizeEffect="Resize">
<mx:Script>
      <![CDATA[
      import mx.controls.Alert;
      import mx.rpc.http.HTTPService
      import mx.rpc.events.Result Event;
      import mx.rpc.events.FaultEvent;
      import flash.data.EncryptedLocalStore;
      import flash.utils.ByteArray;
      public var service:HTTPService = new HTTPService();
             private function testConnection () :void
           {
                     var CF_HTTP URL:String;
                     if(cfip.text == null)
                     {
                        Alert.show("IP Not provided or Invalid IP Address");
                     }
                     else if(cfprt.text!= "" && cfcnxtrt.text != "")
                     {CF_HTTPURL="http://"+cfip.text+":"+cfprt. text+"/"
+cfcnxtrt.text+"/flex2gateway/";
                     }
                     else if(cf prt.text == "" && cfcnxtrt.text == "")
                     {
                          CF_HTTPURL ="http://"+cfip.text+"/flex2gateway/";
                     }
                     else if(cf prt.text!= "" && cfcnxtrt.text == "")
                     {
                          CF_HTTPURL="http://"+cf ip.text+":"+cfprt.text+"/flex2gateway/";




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                             657
Flex and AIR Integration in ColdFusion




                     }


                     if(cfserviceusername.text == "" && cfservicepassword.text == "")
                      {
                        Alert.show("CF Service UserName and Password are not required to test
                      CF server connectivity but they will be required while using the CF
                      services","Note");


                      }


                       service.url = CF_HTTPURL;
                       service.method = "POST";
                       service.addEventListener(ResultEvent.RESULT,httpResult);
                       service.addEventListener(FaultEvent.FAULT,httpFault);
                       service.send();


             }


             public function httpResult(event:ResultEvent):void
           {
           Alert.show("Connection with ColdFusion server Successful","Connection Status");
           }
             public function httpFault(event:FaultEvent):void
           {
           Alert.show("ColdFusion server could not be reached, Make sure credentials are
           correct and CF server is running","Error");
             }
             private function checkCollapse(event:MouseEvent):void
           {
           if( event.clickCount == 2)
                 {
                      currentState = currentState == "collapsed" ? "":"collapsed";
                 }
           }
           private function rememberCredential():void
           {
           var data:ByteArray = new ByteArray();
           data.writeUTFBytes(cfip.text);
           En cryptedLocalStore.setItem('IP', data );
           var data:ByteArray = new ByteArray();
           data.writeUTFBytes(cfprt.text);
           Encr yptedLocalStore.setItem('PORT', data );
           var data:ByteArray = new ByteArray();
           data.writeUTFBytes(cfcnxtrt.text);
           Encrypt edLocalStore.setItem('CONTEXT', data );
           var data:ByteArray = new ByteArray();
           data.writeUTFBytes(cfserviceusername.text);
           Encr yptedLocalStore.setItem( 'USER', data );
           var data:ByteArray = new ByteArray();
           data.writeUTFBytes(cfservicepassword.text);
           Encrypted LocalStore.setItem('PASS', data );
           }
           private function retrieveCredential():void
           {
           try{
           cfip.text = EncryptedLocalStore.getItem('IP').toString();
           cfprt.text = EncryptedLocalStore.getItem('PORT').toString();



                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                       658
Flex and AIR Integration in ColdFusion




           cfcnxtrt.text = EncryptedLocalStore.getItem('CONTEXT').toString();
           cfserviceusername.text = EncryptedLocalStore.getItem('USER').toString();
           cfservicepassword.text = EncryptedLocalStore.getItem(' PASS').to String();
           }
           catch(e:Error)
           {
           }
}
              private function resetCredential():void
                {
                 EncryptedLocalStore.reset();
                 cfip.text = "";
                 cfprt.text = "";
                 cfcnxtrt.text        = "";
                 cfserviceusername.text       = ""  ;
                 cfservicepassword.text = "";
              }


     ]]>
</mx:Script>
<mx:ControlBar>
     <mx:Label text="CFServer IP"/>
     <mx: TextInput id="cfip" text="" width="             70"/>
     <mx:Label text="CFServer Port"/>
     <mx:TextInput id="cfprt" text="" width="40"/>
     <mx:Labeltext="CFServer Context Root (if any)"/>
     <mx:TextInput id="cfcnxtrt" text="" width="70"/>
     <mx:Label text="CFService UserName"/>
     <mx:TextInput id= "cfserviceusername" text="" width="70"/>
     <mx:Label text="CFService Password"/>
     <mx:TextInput displayAsPassword="true" id="cfservicepassword" text="" width="70"/>
     <mx:Button id="testconn" label="Test Connection" click="testConnection()"/>
     <mx:Button id="save" label=" Remember"click="rememberCredential()"/>
     <mx:Button id="reset" label ="Reset" click = "resetCred e n tial()"/>
</mx:ControlBar>
<mx:states>
     <mx:State name="collapsed">
           <mx: SetProperty          name="height" value="10"/>
     </mx:State>
</mx:states>
</mx:Panel>

The AIR application example is as follows:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                            659
Flex and AIR Integration in ColdFusion




<?xml version="1.0" encoding="utf-8"?>
<mx:WindowedApplication xmlns:mx="http://www.adobe.com/2006/mxml" layout="absolute"
xmlns:local="com.*" creationComplete="init()" xmlns:cf="coldfusion.service.mxml.*">
<mx:Script>
<![CDATA[
import coldfusion.service.events.*;
import com.CFCredential;
import mx.collections.ArrayCollection;
import mx.binding.utils.BindingUtils;
import mx.rpc.events.FaultEvent;
import mx.rpc.events.ResultEvent;
import mx.controls.Alert;
import flash.events.*;
import flash.net.FileReference;
import flash.net.FileReferenceList;
import flash.net.URLRequest;
import flash.net.URLVariables;
import coldfusion.service.Util;
     public var fileTypes:Array = new Array();
     public var imageTypes:FileFilter = new FileFilter("Images (*.jpg; *.jpeg; *.gif; *.png)"
,"*.jpg; *.jpeg; *.gif; *.png");
     public var documentTypes:FileFilter = new FileFilter("Documents (*.pdf), (*.doc), (*.rtf),
(*.txt)",("*.pdf; *.doc; *.rtf, *.txt"));
     [Bindable]
           private var fileslist:ArrayCollection;
           private var filereflist:FileReferenceList
           public var fileRef:FileReference = new FileReference();
     [Bindable]
     public var mailPartArray:Array = [{type:"text",content:"Plain text only"},
{type:"html",content:"<B>bold text man!!</B>"}];
     public var uploadURL:URLRequest = new URLRequest();
     [Bindable]
     public var attachCollection:Array = new Array();
     public var urlcnt:int=0;
     public function init():void
     {
     fileslist = new ArrayCollection();
     filereflist = new FileReferenceList;
     fileRef = new FileReference;
     uploadURL.url =
"http://"+conf.cfServer+":"+conf.cfPort+"/"+conf.contextRoot+"/"+Util.UPLOAD_URL;
     var variables:URLVariables = new URLVariables();
     variables.serviceusername = conf.serviceUserName;
     variables.servicepassword = conf.servicePassword;
     uploadURL.data = variables;
     uploadURL.method = "POST"; //this can also be set to "POST" depending on your needs
                 uploadURL.contentType = "multipart/form-data";
                 fileTypes.push(imageTypes);
                 fileTypes.push(documentTypes);
           //Add Event Listeners to UI
           attachbutton.addEventListener(MouseEvent.CLICK, browseFiles);
           sendbutton.addEventListener(MouseEvent.CLICK,uploadFiles);
           filereflist.addEventListener(Event.SELECT, selectHandler);
           //mailtest.send();
           }


//Browse for files



                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                          660
Flex and AIR Integration in ColdFusion




     private function browseFiles(event:Event):void
           {
                 filereflist.browse(fileTypes);
           }


//   called after user selects files form the browse dialogue box.
     private function selectHandler(event:Event):void
           {var i:int;
                 for (i=0;i < event.currentTarget.fileList.length; i ++)
                 {
                      fileslist.addItem(event.currentTarget.fileList[i]);
                      attachList.text +=  event.currentTarget.fileList[i].name + ", ";
                 }
           }
     private function uploadFiles(event:Event):void
     {
           if (fileslist.length > 0)
           {
                 fileRef = FileReference(fileslist.getItemAt(0));
                 fileRef.addEventListener(Event.COMPLETE, completeHandler);
                 fileRef.addEventListener(DataEvent.UPLOAD_COMPLETE_DATA,dataHandler);
                 fileRef.upload(uploadURL);
           }
           else if (fileslist.length == 0)
                 {
                      sendmail();
                 }
           }


// called after a file has been successfully uploaded | We use this as well to check if there
are any files left to upload and how to handle it
     private function completeHandler(event:Event):void
           {
                 // Alert.show("File Uploaded successfully");
                 fileslist.removeItemAt(0);
                 if (fileslist.length > 0)
                 {
                      uploadFiles(null);
                 }
           }
//called after file upload is done and Data has been returned from Server
     private function dataHandler(event:DataEvent):void
     {
           attachCollection[urlcnt++] = {"file":
           Util.extractURLFromUploadResponse(event.data.toString())};
           if (fileslist.length == 0)
                 sendmail();
     }
     private function sendmail():void
     {
           mailtest.execute();
     }
     public function handleResult(event:ColdFusionServiceResultEvent):void
     {
           from.text="";too.text=""; cc.text="";bcc.text="";
           subject.text="";mailbody.text="";
           attachList.text=""; fileslist.removeAll();



                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                        661
Flex and AIR Integration in ColdFusion




           Alert.show("Mail was delivered Successfully");
     }
     public function handleError(event:ColdFusionServiceFaultEvent):void
     {
           Alert.show("Failure"+ event.toString());
     }
     ]]>
</mx:Script>
<mx:Panel width="100%" height="100%">
     <local:CFCredential id="cred" />
     <mx:ControlBar>
     <mx:Spacer width="100%"/>
     <mx:HBox>
           <mx:Button label="Send Mail" id="sendbutton"/>
           <mx:Button label="Attachment" id="attachbutton"/>
     </mx:HBox>
     </mx:ControlBar>
<mx:VBox width="100%" height="100%">
     <mx:HBox width="100%">
     <mx:Text text="From" width="100" />
     <mx:TextInput width="100%" id="from"/>
     </mx:HBox>
     <mx:HBox width="100%">
     <mx:Text text="To" width="100"/>
     <mx:TextInput width="100%" id="too"/>
     </mx:HBox>
     <mx:HBox width="100%">
     <mx:Text text="CC" width="100"/>
     <mx:TextInput width="100%" id="cc"/>
     </mx:HBox>
     <mx:HBox width="100%">
     <mx:Text text="Bcc" width="100"/>
     <mx:TextInput width="100%" id="bcc"/>
     </mx:HBox >
     <mx:HBox width="100%">
     <mx:Text text="Subject" width="100"/>
     <mx:TextInput width="100%" id="subject"/>
     </mx:HBox>
     <mx:HBox width="100%">
     <mx:Text text="Attachments" width="100"/>
     <mx:TextInput width="100%" id="attachList"        enabled="false"
     backgroundDisabledColor="white"/>
     </mx:HBox>
     <mx:TextArea width="100%" height="100%" id="mailbody"/>
</mx:VBox>
</mx:Panel>


     <!--Provide your CF server credentials here-->
           <cf:Config id="conf"




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    662
Flex and AIR Integration in ColdFusion




           cfServer="{cred.cfip.text}"
           cfPort="{int(cred.cfprt.text)}"
           cfContextRoot="{cred.cfcnxtrt.text}"
           servicePassword="{cred.cfservicepassword.text}"
           serviceUserName="{cred.cfserviceusername.text}"
     />


     <cf:Mail id="mailtest"
           server="10.192.36.227"
           to="{too.text}" bcc="{bcc.text}" cc="{cc.text}"
           failTo="jayesh@adobe.com" replyTo="jayesh@adobe.com"
           subject="{subject.text}" content="{mailbody.text}"
           from="{from.text}"
           attachments="{attachCollection}"
           type="text" charset="utf-8" mailerId="CF" priority="1"
           timeout="60" useTLS="true" wrapText="5"
           result="handleResult(event)"
           fault="handleError(event)"
     />
</mx:WindowedApplication>



Using the Image class
The Image class is the proxy for the ColdFusion Image service, which provides the functionality of the cfimage tag.
You specify the required cfimage attributes for the action as Image tag attributes. The following examples show typical
usage, in this case adding a border:

<?xml version="1.0" encoding="utf-8"?>
<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" layout="vertical"
xmlns:cf="coldfusion.service.mxml.*"             creationComplete="init()">
<mx:Script>
<![CDATA[
import mx.controls.Alert;
import coldfusion.service.events.*;


function init():void
     {
           img.execute();
     }


function handleResult(event:ColdFusionServiceResultEvent):void
     {
           mx.controls.Alert.show("result=" + event.result.toString());
           retImage.source = event.result.toString();
     }


function handleError(event:ColdFusionServiceFaultEvent):void
     {
           mx.controls.Alert.show(event.toString());
     }
]]>
</mx:Script>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     663
Flex and AIR Integration in ColdFusion




<cf:Config
     id="configid"
     cfServer="127.0.0.1"
     cfPort="80"
     servicePassword="service"
     serviceUserName="service"/>


<!-- Add border-->
<cf:Image
     id="img"
     action="addborder"
     color="red"
     thickness="5"
     source="http://127.0.0.1:80/GetExifmetaData.jpg"
     result="handleResult(event)"
     fault="handleError(event)"/>


<mx:Image id="retImage"/>
</mx:Application>


Batch operation
A batch operation lets you perform multiple image manipulations on an image as part of one operation, instead of
performing the operations individually. You upload an image to the server, use the batch operation to perform
multiple Image operations on it, and get the modified image back from the server. To use this action, use an associative
array of actions and corresponding attributes. It is easier to create an associative array in ActionScript than MXML.

The following example shows code to create the associative array with the action information and perform the batch
operation:

ActionScript Part:

[Bindable]
public var attributes:Array =
[{AddBorder:{color:"Red",thickness:"50"}},
{Resize:{width:"50%",height:"50%",interpolation:"blackman",blurfactor:"2"}},
{Flip:{transpose:"270"}}]

MXML Part:

<!-- batch operation --
<cf:Image id="img" action="batchoperation"
source="http://localhost:8500/cat.jpg"
attributes="{attributes}"/>



Using the PDF class
The Pdf class is the proxy for the ColdFusion PDF service, which provides the functionality of the cfpdf tag. You
specify the required cfpdf attributes for the action as Pdf tag attributes. The following examples show each supported
action:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                               664
Flex and AIR Integration in ColdFusion




<!-- Get Info-->
<cf:Pdf id="pdftest" action="GETINFO"
source="http://localhost:8500/lcds26_devguide_040908.pdf"/>


<!-- Delete Pages-->
<cf:Pdf id="pdftest1" action="deletepages"
source="http://localhost:8500/lcds26_devguide_040908.pdf"
pages="1" resultHandler="handleDeleteResult"
errorHandler="handleDeleteError"/>


<!-- Merge files--><cf:Pdf id="pdftest" action="mergeFiles"
source="http://localhost:8500/lcds26_devguide_040908.pdf,
http://localhost:8500/EC205W_JoelGeraci.pdf"
resultHandler="handleMergeResult"
errorHandler="handleMergeError"/>


<!-- extract pages-->
<cf:Pdf id="pdftest" action="extractpages"
source="http://localhost:8500/lcds26_devguide_040908.pdf"
pages="2" keepBookmark="true"
resultHandler="handleExtractResult"
errorHandler="handleExtractError"/>


<!-- addwatermark-->
<cf:Pdf id="pdftest" action="addwatermark"
source="http://localhost:8500/1page.pdf"
image="http://localhost:8500/IMG_8680.JPG"
resultHandler="handleExtractResult"
errorHandler="handleExtractError"/>


<!-- removewatermark-->
<cf:Pdf id="pdftest" action="removewatermark"
source="http://localhost:8500/CFFileServlet/
_cfservicelayer/_cf3466030530070122606.pdf"
resultHandler="handleExtractResult"
errorHandler="handleExtractError"/>


<!-- protect-->
<cf:Pdf id="pdftest" action="protect"
source="http://localhost:8500/1page.pdf"
newUserPassword="test" permissions="All"
resultHandler="handleExtractResult"
errorHandler="handleExtractError"/>


<!-- mergespecificpages-->
<cf:Pdf id="pdftest" action="mergespecificpages"




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       665
Flex and AIR Integration in ColdFusion




pdfParam="{pdfparams}" keepBookmark="true"
resultHandler="handleExtractResult"
errorHandler="handleExtractError"/>


<!-- set info-->
<cf:Pdf id="pdftest" action="setinfo"
source="http://localhost:8500/1page.pdf" info="{elements}"
resultHandler="handleExtractResult"
errorHandler="handleExtractError"/>


<!-- thumbnail-->
<cf:Pdf id="pdftest" action="thumbnail"
source="http://localhost:8500/EC205W_JoelGeraci.pdf"
resultHandler="handleThumbnailResult"
errorHandler="handleThumbnailError"/>


<!-- ProcessDDX-->
<cf:Pdf id="pdftest" action="processddx"
ddxString="{ddx}" outputFiles="{outputFiles}" result="handleProcessDDXResult"
fault="handleProcessDDXError"/>



Using the Chart class
The Chart class is the proxy for the ColdFusion Chart service, which provides the functionality of the cfchart tag and
its child chartdata and chartseries tags.

* You specify the cfchart attributes as Chart object properties.

* You represent chart series in the chartSeries element of the chart object. The chartSeries element is an arrays
    of objects, each of which represents a single chart (chartseries tag) document section. These objects include a
    type element for the chart type, a chartdata element for the chart data, and elements for any other series attributes.

* You represent each chart's data as an array of objects, each of which contains an item element and a value element.
    You use these arrays as the chartdata elements of the chartSeries object.

* You call the document object execute() function to run the service.

The following example shows how you can use the chart service:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                 666
Flex and AIR Integration in ColdFusion




<?xml version="1.0" encoding="utf-8"?>
<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml"
layout="absolute" xmlns:cf="coldfusion.service.mxml.*" creationComplete="init()">
<mx:Script>
     <![CDATA[
       import mx.controls.Alert;
       import mx.rpc.events.FaultEvent;
       import mx.rpc.events.ResultEvent;


     [Bindable]
     public var chaSer:Array;
     public var chaDat:Array;
       function init():void
       {
       chaDat =
       [{item:"Facilities",value:"35000"},
       {item:"Facilities1",value:"32000"}];
       chaSer = [{type:"bar",chartdata:chaDat},
       {type:"line",chartdata:chaDat}];chartest.execute();
       }
                 function handleResult(event:ResultEvent):void
                 {
                 Alert.show("success" + event.result.toString());
                 }
                 function handleFault(event:FaultEvent):void
                 {
                 Alert.show("failure" + event.toString());
                 }
     ]]>
</mx:Script>
     <cf:Config id="configid" cfServer="localhost"
     cfPort="80" servicePassword="service"
     serviceUserName="service" >
     </cf:Config>
<cf:Chart id="chartest"
           action="generate"
           format="jpg"
           xAxisTitle="Department"
           yAxisTitle="Salary Average"
           chartSeries="{chaSer}"
           result="handleResult(event)"
           fault="handleFault(event)"
           backgroundColor = "Black"




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     667
Flex and AIR Integration in ColdFusion




           chartHeight = "500"
           chartWidth = "600"
           dataBackgroundColor = "yellow"
           font = "ariel"
           fontBold = "yes"
           fontItalic = "yes"
           fontSize = "12"
           foregroundColor = "red"
           gridLines = "2"
           labelFormat = "number"
           markerSize = "10"
           showBorder = "yes"
           showLegend = "yes"
           showMarkers = "yes"
           showxGridLines="yes"
           showyGridLines="yes"
           tipBgColor="blue"
           tipStyle = "MouseOver"
           title = "AIR Integration testing"/>
</mx:Application>



Using the Document class
The Document class is the proxy for the ColdFusion Document service, which provides the functionality of the
cfdocument tag and its child cfdocumentsection and cfdocumentitem tags.

* You specify the cfdocument attributes as Document object properties. You specify document content that is not
   in a section as a content element of the document object.

* You represent document sections in the documentSection element of the document object. The documentSection
   element is an arrays of objects, each of which represents a single document section. These objects include a content
   element for the section content, an optional documentItem element for the document items, and elements for any
   other section attributes.

* You represent all document items in a document section (or the document object) as an array of objects with type
   and content elements. The array element type field specifies whether the item is a header, footer, or page break. You
   specify the document item array as a documentItem element of the document object or a documentSection object.

* You call the document object execute() function to run the service.

The following excerpt from the full example shows how to create sections and items and add them to a document:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                  668
Flex and AIR Integration in ColdFusion




[Bindable]
var docItem:Array = [{type:"header",content:"<font size='-3'>
<i>Salary Report</i></font>"},{type:"footer",
content:"<font size='-3'>
Page #cfdocument.currentpagenumber#</font>"}];


[Bindable]var docSectionItem:Array = [{content:"<table width='95%'
border='2' cellspacing='2' cellpadding='2' >
<tr><th>Salary</th></tr><tr>
<td><font size='-1'>John</font></td>
<td align='right'><font size='-1'>Guess What</font></td></tr>
<tr><td align='right'><font size='-1'>Total</font></td>
<td align='right'><font size='-1'>Peanuts</font></td></tr>",
documentitem:docItem},{content:"content2",documentitem:docItem}];
.
.
.
cfDoc.documentSection = docSectionItem;

The following example shows some typical document use:

<?xml version="1.0" encoding="utf-8"?>
<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml"
layout="vertical" xmlns:cf="coldfusion.service.mxml.*"
creationComplete="init()">
<mx:Script>
     <![CDATA[
     import mx.controls.Alert;
     import mx.rpc.events.ResultEvent;
     import coldfusion.service.PdfParam;


     [Bindable]
     var docItem:Array = [{type:"header",content:"
     <font size='-3'><i>Salary Report</i></font>"},
     {type:"footer",content:"<font size='-3'>
     Page <cfoutput>#cfdocument.currentpagenumber#
     </cfoutput></font>"}];


     [Bindable]
     var docSection:Array =
     [{content:"content1"},{content:"content2"},
     {content:"content3"}];


     [Bindable]
     var docSectionItem:Array =
     [{content:"content1",documentitem:docItem},
     {content:"content2",documentitem:docItem},
     {content:"content3",documentitem:docItem}];
     [Bindable]
     var res:String = new String();
     private function init():void
           {
                 doctestnow.execute();
           }
     private function handleResult(event:ResultEvent):void
           {
                 res=event.result.toString();




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                669
Flex and AIR Integration in ColdFusion




                 //Alert.show("httpurl= "+event.result.toString());
           }
           private function handleError(event:Event):void
           {
                 mx.controls.Alert.show(event.toString());
           }
           ]]>
</mx:Script>
<cf:Config id="configid" cfServer="localhost"
cfPort="80" servicePassword="service" serviceUserName="service" />


           <!-- simple case-->
           <cf:Document id="doctestnow" action="generate"
           format="flashpaper" result="handleResult(event)"
           fault="handleError(event)"
           content="&lt;table&gt;&lt;tr&gt;&lt;td&gt;bird&lt;/td&gt;&lt;td&gt;
           1&lt;/td&gt;&lt;/tr&gt;&lt;tr&gt;&lt;td&gt;fruit&lt;/td&gt;&lt;
           td&gt;2&lt;/td&gt;&lt;/tr&gt;&lt;tr&gt;&lt;td&gt;rose&lt;/td&gt;
           &lt;td&gt;3&lt;/td&gt;&lt;/tr&gt;&lt;/table&gt;"/>
           <!--doc item case -->
           <!--<cf:Document id="doctestnow" action="generate"
           format="flashpaper" result="handleResult(event)"
           fault="handleError(event)" documentItem="{docItem}"/>-->
           <!-- doc section case-->
           <!--<cf:Document id="doctestnow" action="generate"
           format="flashpaper" result="handleResult(event)"
           fault="handleError(event)" documentSection="{docSection}"/>-->


           <!-- doc section and doc item case
           <cf:Document id="doctestnow" action="generate"
           format="flashpaper" result="handleResult(event)"
           fault="handleError(event)" documentSection="{docSectionItem}" />-->
           <mx:SWFLoader source="{res}"/>
</mx:Application>



Using the Pop class
The Pop class is the proxy for the ColdFusion Pop service, which provides the functionality of the cfpop tag. You
specify the cfpop action and required attributes as Pop object properties and call the object execute() function to
run the service. The following example shows the user each supported action:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                    670
Flex and AIR Integration in ColdFusion




<?xml version="1.0" encoding="utf-8"?>
<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" layout="absolute"
xmlns:cf="coldfusion.service.mxml.*" creationComplete="init()">
<mx:Script>
<![CDATA[
import mx.rpc.events.ResultEvent;
import mx.rpc.events.FaultEvent;
import mx.controls.Alert;
import coldfusion.service.events.*;
     public function init():void
           {
                 poptest.execute();
           }
     public function handleResult(event:ResultEvent):void
           {
                 Alert.show("Success" + event.toString());
           }
     public function handleError(event:FaultEvent):void
           {
                 Alert.show("Failure");
           }
     public function handleGetAll(event:ResultEvent):void
           {
                 var res:Array = event.result as Array;
                 for(var i:uint = 0; i < res.length; i++)
                 {
                      var key:String;
                      for(key in res[i])
                      {
                             trace("object key = " + key.toString());
                             if(res[i][key] != null)
                             {
                                  trace("object value = " + res[i][key].toString());
                             }
                      }
                 }
           }
     ]]>
</mx:Script>


<cf:Config id="configid" cfServer="localhost" cfPort="8500"
servicePassword="service" serviceUserName="service"            />


<!--<cf:Pop id="poptest" action="getall" result="handleGetAll(event)"
host="10.192.36.227" userName="failoveruser" password="password"
fault="handleError(event)"/>-->


<!--<cf:Pop id="poptest" action="getheaderonly" result="handleGetAll(event)"
host="10.192.36.227" userName="failoveruser" password="password"
fault="handleError(event)"/>-->


<cf:Pop id="poptest" action="delete" messageNumber="25" host="10.192.36.227"
userName="failoveruser" password="password" result="handleResult(event)"
fault="handleError(event)" />
</mx:Application>




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    671
Flex and AIR Integration in ColdFusion




Using the LiveCycle Data Services ES Assembler

To use Adobe ColdFusion as the back-end data manager for an Adobe Flex application, you use the Adobe LiveCycle
Data Services ES assembler. You configure the LiveCycle Data Services ES assembler and write an application that uses
the assembler.

To use LiveCycle Data Services ES with ColdFusion, you have to be familiar with ColdFusion components; accessing
and using data in ColdFusion applications; and using LiveCycle Data Services ES.


About ColdFusion and Flex
ColdFusion 9 supports LiveCycle Data Services 2.6.1 however the ColdFusion installation does not provide the option
to install LiveCycle Data Services. You need to manually install LiveCycle Data Services to use it with ColdFusion. For
more information about manually installing LiveCycle Data Services, see Installing LiveCycle Data Services Manually
in the Installation Guide.

By default, ColdFusion installs BlazeDS which provides messaging support in ColdFusion.

For more information about the changes in using Flash Remoting, see Changes in the XML configuration files for
Flash Remoting in ColdFusion 9 and ColdFusion 9.0.1.

The LiveCycle Data Services ES assembler lets you use ColdFusion components (CFCs) to provide the back-end data
management for a Flex application that uses the Data Management Service. You can run LiveCycle Data Services ES
as part of ColdFusion or remotely. If you are running LiveCycle Data Services ES as part of ColdFusion, LiveCycle Data
Services ES and ColdFusion communicate directly. If you are running LiveCycle Data Services ES remotely, LiveCycle
Data Services ES and ColdFusion communicate by using RMI. The following diagram shows how ColdFusion and
LiveCycle Data Services ES interact in both cases:




Note: To use the LiveCycle Data Services ES assembler, the Flex application must be running on Flex Data Services 2.0.1
or LiveCycle Data Services 2.5, although not every feature is supported in Flex Data Services 2.0.1.

The Flex server includes a ColdFusion Data Service adapter. The adapter processes changes to data to ensure that data
on the client is synchronized with back-end data and conversely; it executes the sync, fill, count, and get
operations, identifies conflicts, and passes results to LiveCycle Data Services ES.

ColdFusion includes the LiveCycle Data Services ES assembler; along with the ActionScript translator, it converts the
input arguments where necessary and translates the return values.

Note: If you install LiveCycle Data Services ES, ColdFusion does not map .SWF files. This means that all .SWF files are
served through the ColdFusion web application instead of the web server.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     672
Flex and AIR Integration in ColdFusion




The following diagram shows the process that LiveCycle Data Services ES and ColdFusion use when a Flex application
calls a method in a ColdFusion component:




1 A Flash client requests data that the LiveCycle Data Management Service adapter handles.

2 Flex calls a fill, sync, get, or count method in the Data Service.

3 If you are running LiveCycle Data Services ES remotely, copy the lcds2.6_install/resources/lib/flex-*.jar files to the
   <ColdFusion_home>/lib/ directory for a standalone deployment or WEB-INF/cfusion/lib directory for J2EE
   deployment.

4 The ColdFusion Data Service adapter sends the request to the LiveCycle Data Services ES assembler. If you are
   running LiveCycle Data Services ES remotely, the adapter sends the request by using Java Remote Method
   Invocation (Java RMI).

5 The LiveCycle Data Services ES assembler and the ActionScript translator convert ActionScript 3.0 data types to
   the appropriate ColdFusion values.

6 The ColdFusion server runs the fill, sync, get, or count method of the assembler CFC, which runs the
   appropriate methods in the DAO CFC.

7 The ColdFusion application creates an array of Value Objects or appropriate return value, which it sends to the
   ColdFusion server.

8 The ColdFusion server sends the results to the LiveCycle Data Services ES assembler.

9 The LiveCycle Data Services ES assembler and the ActionScript translator convert ColdFusion values to the
   appropriate ActionScript 3.0 data types, and then the assembler sends the results to the ColdFusion Data Service
   adapter.

10 The ColdFusion Data Service adapter sends the results to the LiveCycle Data Management Service.

11 The LiveCycle Data Management Service passes the results to the Flash client.

Note: The RMI registry, which facilitates communication between the ColdFusion Data Service assembler and the remote
LiveCycle Data Management Service uses port 1099, which is the default port for Java RMI. You can change the port
number by adding -Dcoldfusion.rmiport=1234 to the Java JVM arguments on both the ColdFusion server and the Flex
server.


Application development and deployment process
The following is a typical process for developing and deploying a Flex application that uses the ColdFusion Data
Service adapter and LiveCycle Data Services ES assembler to manage back-end database tasks:

1 Design your application.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 673
Flex and AIR Integration in ColdFusion




2 Create the Flex application, in which you define a DataService component in MXML or ActionScript. The
   DataService component calls methods on a server-side Data Management Service destination to perform activities
   such as filling client-side data collections with data from remote data sources and synchronizing the client and
   server versions of data.

3 Configure a destination for the ColdFusion Data Service adapter so that the Flex application to connect to the
   ColdFusion back-end application. For more information, see "Configuring a destination for the ColdFusion Data
   Service adapter" on page 673.

4 Write your ColdFusion CFCs. For more information, see "Writing the ColdFusion CFCs" on page 680.

   Note: To make creating the CFCs easier, ColdFusion includes wizards that you can use in Flash Builder. For more
   information, see "Using the ColdFusion Extensions for Eclipse" on page 1334.

5 Test your application by using Flex.


Configuring a destination for the ColdFusion Data Service adapter
To provide the information necessary for the Flex application to connect to the ColdFusion back-end application, you
configure a destination. In the destination, you specify the ColdFusion Data Service adapter, the channels to use to
transport messages to and from the destination, the CFC that contains the fill, get, sync, and count methods, and
other settings.

To provide configuration information, you edit the following files:

1 services-config.xml

   You specify channel definitions and enable ColdFusion-specific debugging output in the Flex console in this file.
   Change the port numbers in the services-config.xml file for the RTMP channels if you run more than one
   ColdFusion instance with the integrated LiveCycle Data Services ES.

2 data-management-config.xml

   This file is added only when you install LiveCycle Data Services 2.6.1 manually. You specify adapters and
   destinations in this file.

To ensure that Flex recognizes the LiveCycle Data Services ES assembler and can transport messages to and from the
destination, by doing the following:

* Specifying ColdFusion-specific channel definitions

* Specifying the ColdFusion Data Service adapter

* Specifying a destination

* Enabling ColdFusion-specific debugging output


Enhanced Flash Remoting
ColdFusion 9 has introduced enhanced Flash Remoting.

* Enhanced Flash Remoting supports Circular references for objects which are not supported in old Flash Remoting
   (ColdFusion 8).

* Enhanced Flash Remoting is significantly faster than the old one

The default ColdFusion 9 installation makes use of this Enhanced Flash Remoting. The structure inside the xml
configuration files under WEB-INF/flex directory has been changed to support Enhanced Flash Remoting.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      674
Flex and AIR Integration in ColdFusion




ColdFusion 9 supports Old XML configurations files as well, but with these files it will not be using Enhanced Flash
Remoting. To take advantage of Enhanced Flash Remoting, while migrating your old XML files to ColdFusion9, you
should make sure that they comply with new XML structural changes.

If you are using LCDS with ColdFusion 9, then Flash Remoting works on LCDS 2.6.1. Ensure that you are on LCDS
2.6.1 to make use of the enhanced Flash Remoting.

ColdFusion 9 is backward compatible with old Flash Remoting as well, to support LCDS2.5 and prior releases.

Now in a case, where you require to continue with LCDS 2.5 or prior versions, it will not be possible for you to use
enhanced Flash Remoting offered by ColdFusion 9. In this scenario, you can continue working with LCDS2.5 and
prior versions with old style XML configuration files.

To use the old style flash remoting with LCDS 2.5, first remove the existing ColdFusion 9 flex-*.jar files from
cfusion/lib by taking a backup of the files and placing the LCDS2.5 flex-*.jar files in the cfusion\lib directory.
After this, you can continue to use the old style (ColdFusion 8) XML configuration files by placing them under WEB-
INF\Flex\ directory. Also make sure that WEB-INF\ flex\jar\cfdataserviceadapter.jar is also present.

For detailed steps on integrating LCDS2.5 with ColdFusion, see the technote on www.adobe.com.

Another case here could be that you have integrated LCDS 2.6.1 with ColdFusion 9 and still you want to use old Flash
Remoting. You can implement this scenario, but in this case you cannot take advantages offered by enhanced Flash
Remoting.


Changes in the XML configuration files for Flash Remoting in ColdFusion 9 and ColdFusion
9.0.1
For ColdFusion 9, the structure of the services-config.xml file has changed. These structural changes are:

* A new <coldfusion> tag has been added under the <properties> in <channel-definition>, where the
   <access>, <use-accessor>, <use-structs>, and <property-case> tags are defined.

   In old ColdFusion 8 style remoting, these tags used to be present in the destination, defined in data-management-
   config.xml file.

* Previously, the <serialization> tag included:

   <serialization>
         <instantiate-types>false</instantiate-types>
   </serialization>

   However, now you need to either set the <instantiate-types> to true or remove it from the services-config.xml
   file.

* The <enable-small-messages> flag must be set to false under the serialization property.

   Note: In case you create custom channel definition on your client side by overriding the XML-based channel
   configurations, you still need to set "enableSmallMessages" flag to false. This is shown in the following code example:

   <mx:ChannelSet id="myChannelSet">
   <mx:channels>
   <mx:AMFChannel enableSmallMessages="false"
   url="http://localhost:8500/flex2gateway/cfamfpolling" id="cfAMFPolling"
   pollingEnabled="true" pollingInterval="8"/>
   </mx:channels>
   </mx:ChannelSet>

* In ColdFusion 9, the endpoint class names have been changed from the endpoint classes in ColdFusion 8. The
   following table provides a list of channel-definitions and their corresponding endpoint classes:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     675
Flex and AIR Integration in ColdFusion




    Channel-definition       ColdFusion 9 Endpoint Class                            ColdFusion 8 Endpoint Class
    ID

    my-cfamf                 coldfusion.flash.messaging.CFAMFEndPoint               flex.messaging.endpoints.AMFEndpoint


    cf-polling-amf           coldfusion.flash.messaging.CFAMFEndPoint               flex.messaging.endpoints.AMFEndpoint


    my-cfamf-secure          coldfusion.flash.messaging.SecureCFAMFEnd              flex.messaging.endpoints.SecureAMFEn
                             Point                                                  dpoint


    cf-rtmp                  coldfusion.flash.messaging.CFRTMPEndPoint              flex.messaging.endpoints.RTMPEndpoin
                                                                                    t


   Note: For LiveCycle Data Services ES, the cf-polling-amf and cf-rtmp channel definitions are used.

* ColdFusion 9.0.1 supports BlazeDS 4.

   The following table details the endpoint classes for BlazeDS:


    Channel-definition ID          Endpoint Class

    my-streaming-amf               coldfusion.flash.messaging.CFStreamingAMFEndPoint

    secure-streaming-amf           coldfusion.flash.messaging.SecureCFStreamingAMFEndPoint


* ColdFusion 9.0.1 supports LCDS 3 and LCDS 3.1.

   The following table details the endpoint classes for LCDS:


    Channel-definition ID          Endpoint Class

    my-nio-amf                     coldfusion.flash.messaging.CFNIOAMFEndPoint secure-nio-amf

    secure-nio-amf                 coldfusion.flash.messaging.SecureCFNIOAMFEndPoint

    secure-cf-rtmp                 coldfusion.flash.messaging.SecureCFRTMPEndPoint

    my-nio-amf-stream              coldfusion.flash.messaging.CFStreamingNIOAMFEndPoint

    secure-nio-amf-stream          coldfusion.flash.messaging.SecureCFStreamingNIOAMFEndPoint


* In ColdFusion 9.0.1, a channel-definition construct has been introduced in services-config.xml
   (CF_root/wwroot/WEB-INF/flex/) named serialize-array-to-arraycollection.

   This construct provides more flexibility and control for users to decide whether to serialize the ColdFusion array
   to ActionScript Array or ArrayCollection.

   To serialize, in the XML, set the value to true for the following:

   <serialize-array-to-arraycollection>false</serialize-array-to-arraycollection>

   Note: This construct is not considered when ColdFusion Array is sent to LCDS Flex Client. In this case, ColdFusion
   Array always gets translated to ActionScript ArrayCollection.

* All the other files that need to be referenced from services-config.xml are now `included' in services-config.xml.

In ColdFusion 8, the services-config.xml files looked similar to the following:




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                          676
Flex and AIR Integration in ColdFusion




<channel-definition id="cf-polling-amf" class="mx.messaging.channels.AMFChannel">
<endpoint url="http://{server.name}:{server.port}{context.root}/
     flex2gateway/cfamfpolling" class="flex.messaging.endpoints.AMFEndpoint"/>
                 <properties>
                 <polling-enabled>true</polling-enabled>
                      <serialization>
                             <instantiate-types>false</instantiate-types>
                      </serialization>
                 </properties>
</channel-definition>


<channel-definition id="cf-rtmp" class="mx.messaging.channels.RTMPChannel">
<endpoint url="rtmp://{server.name}:2048" class="flex.messaging.endpoints.RTMPEndpoint"/>
<properties>
     <idle-timeout-minutes>20</idle-timeout-minutes>
           <serialization>
                 <!-- This must be turned off for any CF channel -->
                      <instantiate-types>false</instantiate-types>
                 </serialization>
</properties>
</channel-definition>

The new services-config.xml file looks similar to this:

<channel-definition id="cf-polling-amf "class="mx.messaging.channels.AMFChannel">
<endpoint uri="http://{server.name}:{server.port}{context.root}
/flex2gateway/cfamfpolling" class="coldfusion.flash.messaging.CFAMFEndPoint" />
<properties>
     <polling-enabled>true</polling-enabled>
     <polli ng-interval-seconds>8</polling-interval-seconds>
     <serialization>
           <enable-small-messages>false</enable-small-messages>
     </serialization>
     <coldfusion>
           <access>
                 <use-mappings>true</use-mappings>
                 <method-access-level>remote</method-access-level>
           </access>
           <use-accessors>true</use-accessors>
           <use-structs>false</use-structs>
           <property-case>
                 <force-cfc-lowercase>false</force-cfc-lowercase>
                 <force-query-lowercase>false</force-query-lowercase>
                 <force-struct-lowercase>false</force-struct-lowercase>
           </property-case>
     </coldfusion>
</properties>
</channel-definition>
<channel-definition id="cf-rtmp" class="mx.messaging.channels.RTMPChannel">
<endpoint uri="rtmp://{server.name}:2048" class="coldfusion.flash.messaging.CFRTMPEndPoint"/>




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    677
Flex and AIR Integration in ColdFusion




     <properties>
                 <idle-timeout-minutes>20</idle-timeout-minutes>
     <serialization>
           <enable-small-messages>false</enable-small-messages>
     </serialization>
                 <coldfusion>
                      <access>
                             <use-mappings>true</use-mappings>
                             <method-access-level>remote</method-access-level>
                      </access>
                      <use-accessors>true</use-accessors>
                      <use-structs>false</use-structs>
                      <property-case>
                             <force-cfc-lowercase>false</force-cfc-lowercase>
                             <force-query-lowercase>false</force-query-lowercase>
                             <force-struct-lowercase>false</force-struct-lowercase>
                      </property-case>
                 </coldfusion>
     </properties>
</channel-definition>


Specifying ColdFusion-specific channel definitions
LiveCycle Data Services ES transports messages to and from destinations over message channels that are part of the
Flex messaging system. When you configure a destination, you reference the messaging channels to use. To connect
to a ColdFusion back-end application, ensure that the services-config.xml file contains definitions for the cf-
polling-amf channel and the cf-rtmp channel in the channels section. If you are running LiveCycle Data Services
ES in ColdFusion, the services-config.xml file is in the wwwroot\WEB-INF\flex directory and contains the channel
definitions by default. If you are running LiveCycle Data Services ES remotely, the services-config.xml file is located
in the under \WEB-INF\flex directory when you install LiveCycle Data Services ES in the default location.

The new channel definitions include the following:

<channel-definition id="cf-polling-amf" class="mx.messaging.channels.AMFChannel ">
<endpoint uri="http://{server.name}:{server.port}{context.root}
/flex2gateway/cfamfpolling"class="coldfusion.flash.messaging.CFAMFEndPoint" />
<properties>
     <polling-enabled> true </polling-enabled>
     <polling-interval-seconds>8</polling-interval-seconds>
     <serialization>
           <enable-small-messages>false</enable-small-messages>
     </serialization>
     <coldfusion>
           <access>
                 <use-mappings>true</use-mappings>
                 <method-access-level>remote</method-access-level>
           </access>
           <use-accessors>true</use-accessors>
           <use-structs>false</use-structs>
           <property-case>
                 <force-cfc-lowercase>false</force-cfc-lowercase>
                 <force-query-lowercase>false</force-query-lowercase>
                 <force-struct-lowercase>false</force-struct-lowercase>
           </property-case>
     </coldfusion>
</properties>
</channel-definition>



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                           678
Flex and AIR Integration in ColdFusion




Element                                              Description

access                                               Define the resolution rules and access level of the CFC being invoked

use-accessors                                        Whether the Value Object CFC has getters and setters. Set the value of use-
                                                     accessors to true if there are getters and setters in the Value Object CFC. However,
                                                     if you set use-accessors to true and there are no getters and setters in the value
                                                     object CFC, ColdFusion sets the value of any property of the value object CFC in the
                                                     this scope. If your CFC does not have any getters and setters, you can increase
                                                     performance by setting this to false so that ColdFusion does not spend time looking
                                                     for these methods. The default value is true.

use-structs                                          Whether to translate ActionScript to CFCs. Set the value of use-structs to true if
                                                     you don't require any translation of ActionScript to CFCs. The assembler can still return
                                                     structures to Flex, even if the value is false. The default value is false.

force-cfc-lowercase                                  Whether to make property names, query column names, and structure keys lowercase
                                                     when converting to ActionScript. Query column names must precisely match the case
force-query-lowercase
                                                     of the corresponding ActionScript variables. The default value is false.

force-struct-lowercase


use-mappings                                         A Boolean value specifying whether the source attribute can be relative to (start with)
                                                     a ColdFusion mapping. The default value is true.

method-access-level                                  Specifies the access attribute values a CFC must have for ColdFusion to respond to
                                                     the request. The following values are valid:

                                                     *  remote Flex can access only functions that specify remote access. (the default)

                                                     *  public Flex can access functions that specify both remote or public access.



Specifying the ColdFusion Data Service adapter
Flex provides adapters to connect to various back-end applications. To use the ColdFusion Data Service adapter, you
specify it in the data management configuration file by copying the following adapter-definition to the adapters section
of the data-management-config.xml file that is in the WEB-INF/flex folder of the server on which you want to run the
Flex application. If you are running LiveCycle Data Services ES in ColdFusion, the data-management-config.xml file
contains the adapter definitions by default.

The adapter definition includes the following line:

<adapter-definition id="coldfusion-dao" class="coldfusion.flex.CFDataServicesAdapter"/>


Specifying a destination
A destination is the server-side service or object that you call. You configure Data Management destinations in the
data-management-config.xml file.

The destination contains the following elements:


Element                            Description

destination id                     The ID must be unique for each destination.

adapter-ref                        The name of the adapter to use. You use the ColdFusion adapter-ref element for any ColdFusion
                                   specific destinations.

channels-ref                       Use the ColdFusion configured channels that have the instantiate-types flag set to false.

component                          The name or path on the ColdFusion server.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                             679
Flex and AIR Integration in ColdFusion




Element                            Description

scope                              The scope, which can be application, session, or request. The application value specifies that
                                   there is only one instance; request specifies that there is a new CFC for each call. ColdFusion does not
                                   support session. (Do not confuse this setting with the ColdFusion variable scope; they are not related.)

hostname                           The host name or IP address of the ColdFusion host. If you are running LiveCycle Data Services as part of
                                   ColdFusion you do not specify a host name or IP address; however, if you are running LiveCycle Data
                                   Services ES remotely, specify a host name or IP address.

identity                           The ID of the ColdFusion Data Management server as configured in the ColdFusion Administrator.

                                   This is required only if you are accessing a ColdFusion server remotely using RMI and have more than
                                   one instance of ColdFusion on a machine.

remote-username                    Credentials to pass to the assembler CFC for all clients. It is preferable to use the ActionScript
                                   setRemoteCredentials() API on the client.
remote-password


identity property                  The property or list of properties that are the primary key in the database.

query-row-type                     Optional. If the assembler fill method returns a query, define an ActionScript type for each row in the
                                   query that the ArrayCollection returned.

fill-method                        Whether to update the results of a fill operation after a create or update operation.

use-fill-contains                  Optional. Whether the assembler has a fill-contains method. This method is used to determine
                                   whether to refresh the fill. If the specified method returns true, the fill is re-executed after a create or
                                   update operation. Set use-fill-contains to true only when auto-refresh is set to true. The
                                   default value is false.

auto-refresh                       Optional. Whether to refresh the fill after a create or update operation. The default value is true.

ordered                            Optional. Whether order is important for this filled collection. Allows performance optimization when
                                   order is not important. The default value is true.


The following code shows a sample destination:

<destination id="cfcontact">
<!-- Use the ColdFusion adapter for any CF specific destinations-->
     <adapter ref="coldfusion-dao" />
<channels>
<channel ref="cf-polling-amf" />
</channels>


     <properties>
           <!--The component name or path on the CF server-->
                 <component>samples.contact.ContactAssembler</component>
           <!--Either "application" or "request"-->
                 <scope>request</scope>
           <!-- The hostname or IP address of the CF host. If Data Services is installed as
                 part of CF, you omit this. If Data Services runs outside of CF, you must
                 define this. <hostname>localhost</hostname>-->


           <!--This is the ID of the ColdFusion Data Management service as configured in
           the ColdFusion Administrator. Only needed if you have more than one instance of
           CF on a machine and Data Services is not installed as part of CF.
           <identity>default</identity> -->
           <!--Credentials to pass to the assembler CFC for all clients. Generally better
           to use setRemoteCredentials() API on client <remote-username></remote-username>
           <remote-password></remote-password>-->
     <metadata>
           <identity property="contactId" />




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                680
Flex and AIR Integration in ColdFusion




           <!--Optional, If the Assembler fill routine returns a query,you must define an
                 Actionscript type for the rows.-->
           <query-row-type>samples.contact.Contact</query-row-type>
     </metadata>


     <network>
           <!--    Add network elements here-->
     </network>


     <server>
     <!-- The method declarations are ignored for CFC Assemblers, with the exception of
     the fill-method settings. No parameters are defined here, unlike Java. Any arguments
     provided via the AS call are passed along to the CFC, just use optional arguments
     when defining the CFC.-->
     <fill-method>
     <!--Does the assembler have a "fill-contains" method? This method is used to
     determine whether to refresh the fill. If the specified method returns true the fill
     is re-executed after a create or update. Auto-refresh determines if the fill is
     always refreshed if not specified. May only be used when auto-refresh is true.
     Optional. Default is false.-->
     <use-fill-contains>false</use-fill-contains>
     <!-- Determines whether to refresh the fill on updates or creates. Optional. Default
           value is true.-->
     <auto-refresh>true</auto-refresh>
     <!--Determines whether order is important for this filled collection. Allows for
     performance optimization when order is not important. Optional. Default value is
     true.-->


     <ordered>true</ordered>


     </fill-method>
     </server>
     </properties>
</destination>


Enabling ColdFusion-specific debugging output
You enable ColdFusion-specific debugging output in the Flex console by adding the following <pattern> tag in the
<filters> tag in the logging section in the services-config.xml file:

<pattern>DataService.coldfusion</pattern>

For more information, see "Configuring the Data Service" in Developing Flex Applications, which is included in the
Flex documentation.

Note: The ColdFusion Administrator lets you enable or disable LiveCycle Data Management support. If you are running
more than one instance of ColdFusion, use a unique ID to specify each instance of ColdFusion for which you enable
LiveCycle Data Management support. You do so by specifying the identity in the identity element in the data-
management-config.xml file.


Writing the ColdFusion CFCs
When you create your ColdFusion CFCs, you can do one of the following:

* Create an assembler CFC and a Value Object CFC.

* Create an assembler CFC, a Data Access Object (DAO) CFC, and a Value Object CFC.



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    681
Flex and AIR Integration in ColdFusion




You place the database manipulation functionality directly in the methods in the assembler CFC and create a Value
Object CFC, which is a CFC that contains property definitions and related get and set methods.

To separate the lower level database functionality from the high-level Flex assembler operations, you create a Data
Access Object (DAO) CFC that contains the lower level database functionality. Using this approach, which is the
Bean/DAO methodology, requires that you place the fill, get, sync, and count methods in the assembler CFC. The
methods in the assembler CFC call methods in the DAO CFC that perform the lower level database functions such as
retrieving records. The DAO CFC creates Value Objects, which are CFCs that contain the values. A Value Object is
essentially a row in the result set.

The LiveCycle Data Management Service recognizes the methods: fill, get, sync, and count. The fill method
retrieves records from a database and populates an array with the records. The get method retrieves a specific record.
The sync method lets you keep track of synchronization conflicts by accepting a change list, which is an array of
change objects. The count method returns a number that indicates how many records are in a result set. To perform
any of these database tasks, the Flex application calls the appropriate fill, get, sync, or count method in the
assembler CFC. You can also use a fillContains method, which checks whether to update the results of a fill. For
more information, see "Managing fills" on page 683.


Creating the fill method
The fill method retrieves records from a database and populates an array with the records. If you use the Bean/DAO
methodology, you create the lower level read method separately in the DAO CFC.

The fill method returns the results of a read operation. In the fill method, you create an array to hold the results
of the read, and then return the results of the read operation. The essential elements of a fill method appear as
follows:

<cffunction name="fill" output="no" returntype="samples.contact.Contact[]" access="remote">
     <cfreturn variables.dao.read()>
</cffunction>

You can return a Value Object CFC, a query, or an array of CFML structures. Using a query instead of a Value Object
CFC may improve performance. However, ColdFusion cannot handle nested results sets when you use a query. For
example, if one of the CFC properties you are returning from the fill method was populated with another complex
type such as another CFC type, ColdFusion cannot automatically convert a column in the query to an object with a
custom type. In this case, you return an array of CFCs, and the fill method or the read method in the DAO CFC
constructs the correct object.

You can use structures wherever you currently create a ColdFusion component in the Assembler. However, you still
receive CFC Value Objects from Flex. For example, the Change Objects that you receive in the sync method contain
CFCs, assuming that you have a remote alias defined in the ActionScript type.

You can create Value Object CFCs in the get method. However, using the structure functionality, you can create and
return a structure instead of a CFC, because the structures are translated in the same way as CFCs. You can also return
an array of structures from the fill method instead of an array of CFCs, for example, if you have to do processing on
your data and working with CFCs isn't fast enough. Generally, structures are faster than CFCs. You also use structures
when a member of the result object is a complex object. In this case, you create another structure as the value of that
key and provide the __type__ key for it.

You specify the returntype of the fill method as a Value Object CFC, a query, or an array:

1 Value Object:

    <cffunction name="fill" output="no"
         returntype="samples.contact.Contact[]" access="remote">

2 Query:



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               682
Flex and AIR Integration in ColdFusion




    <cffunction name="fill" output="no"
         returntype="query" access="remote">

3 Array of structures:

    <cffunction name="fill" output="no"
         returntype="array" access="remote">

In addition to specifying the returntype of the fill function depending on whether you are using Value Objects, a
query, or an array of structures, you also do the following in the lower level read function:

* Specify the returntype of the read function as the Value Object CFC, a query, or an array, for example:

   * <cffunction name="read" output="false" access="public"
       returntype="samples.contact.Contact[]">

   * <cffunction name="read" output="false" access="public" returntype="query">

   * <cffunction name="read" output="false" access="public" returntype="array">

* If you are using Value Objects:

   * Create the array to contain the Value Objects, as follows:

        <cfset var ret = ArrayNew(1)>

   * Loop through the query to create each Value Object based on each row of the query, for example:

        <cfloop query="qRead">
             <cfscript>
                  obj = createObject("component",
                        "samples.contact.Contact").init();
                  obj.setcontactId(qRead.contactId);
                  obj.setfirstName(qRead.firstName);
                  obj.setlastName(qRead.lastName);
                  obj.setaddress(qRead.address);
                  obj.setcity(qRead.city);
                  obj.setstate(qRead.state);
                  obj.setzip(qRead.zip);
                  obj.setphone(qRead.phone);
                  ArrayAppend(ret, obj);
             </cfscript>
       </cfloop>

* If you are using a query:

   * Ensure that you configured the destination with the row type for the destination so that ColdFusion correctly
       labels each rows in the query with the corresponding ActionScript type. Use the query-row-type element,
       which is in the metadata section of the destination.

   * Specify the following in the fill method:

        <cffunction name="fill" output="no" returntype="query"
             access="remote">
             <cfargument name="param" type="string" required="no">
                  <cfquery name="myQuery" .>
                  </cfquery>
                  <!--- Return the result --->
             <cfreturn myQuery>
       </cffunction>

   * If you are using a DAO CFC, edit the read method to return a query instead of an array of CFCs.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           683
Flex and AIR Integration in ColdFusion




     * Ensure that the query column names match the case of the properties in the ActionScript object. Use the
        property-case settings in the destination to do so. Set the force-query-lowercase element to false so that
        ColdFusion converts all column names to lowercase.

* If you are using an array of structures:

     * Create the array to contain the Value Objects, as follows:

        <cfset var ret = ArrayNew(1)>

     * Loop through the query to create the structure that contains the results of the query, for example:

        <cfloop query="qRead">
             <cfscript>
                  stContact = structNew();
                  stContact["__type__"] = "samples.contact.Contact";
                  stContact["contactId"] = qRead.contactId;
                  stContact["firstName"] = qRead.firstName;
                  stContact["lastName"] = qRead.lastName;
                  stContact["address"] = qRead.address;
                  stContact["city"] = qRead.city;
                  stContact["state"] = qRead.state;
                  stContact["zip"] = qRead.zip;
                  stContact["phone"] = qRead.phone;
                  ArrayAppend(ret, duplicate(stContact));
             </cfscript>
        </cfloop>

     * Use the __type__ structure element to specify that the Value Object CFC is the type, for example:

        stContact["__type_"] = "samples.contact.Contact";

     * Use the associative array syntax, for example, contact["firstName"] to ensure that you match the case of the
        ActionScript property. If you use the other syntax, for example, contact.firstName="Joan", ColdFusion
        makes the key name uppercase.


Managing fills
To determine whether to refresh a fill result after an item is created or updated, you include a fillContains method
in the assembler and set both use-fill-contains and auto-refresh to true in the fill-method section of the data-
management-config.xml file. The following example shows a fill-method section:

 <fill-method>
      <use-fill-contains>true</use-fill-contains>
      <auto-refresh>true</auto-refresh>
      <ordered>false</ordered>
</fill-method>

In this example, ordered is set to false because the fill result is not sorted by any criteria. However, if the fill result is
sorted, you set ordered to true. When an item changes in a fill result that is ordered, refresh the entire fill result.

The fillContains method tells the Flex application whether it is necessary to run the fill again after an item in the
fill result has changed. The fillCcontains method returns a value that indicates how the fill be treated for that
change. When the fillContains method returns true, the fill is executed after a create or update operation.

The following example shows the fillContains method signature:

 <cffunction name="fillContains" output="no" returnType="boolean" access="remote">
        <cfargument name="fillArgs" type="array" required="yes">
        <cfargument name="item" type="[CFC type object]" required="yes">
        <cfargument name="isCreate" type="boolean" required="yes">



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 684
Flex and AIR Integration in ColdFusion




The fillContains method has the following arguments:

* fillArgs is a list of arguments to pass to the fill method.

* item is the record to check to determine if it is in the result set.

* isCreate indicates whether the record is new.

A sample fillContains method, which determines whether the fill arguments (part of the first or last name) are
in the Contact item passed to the function, is as follows:

 <cffunction name="fillContains" output="no" returnType="boolean"access="remote">
     <cfargument name="fillArgs" type="array" required="yes">
     <cfargument name="item" type="samples.contact.Contact" required="yes">
     <cfargument name="isCreate" type="boolean" required="yes">


<cfif ArrayLen(fillArgs) EQ 0>
<!--- This is the everything fill. --->
<cfreturn true>
<cfelseif ArrayLen(fillArgs) EQ 1>
<!--- This is a search fill. --->
     <cfset search = fillArgs[1]>
     <cfset first = item.getFirstName()>
     <cfset last = item.getLastName()>
     <!--- If the first or last name contains the search string, --->
     <cfif (FindNoCase(search, first) NEQ 0) OR (FindNoCase(search, last)
           NEQ 0)>
<!--- this record is in the fill. --->
<cfreturn true>
<cfelse>
<!--- this record is NOT in the fill. --->
<cfreturn false>
</cfif>
</cfif>


<!--- By default, do the fill.--->
<cfreturn true>
</cffunction>

If you are running LiveCycle Data Services ES locally, you can determine whether a fill operation is a refresh or a
client triggered fill. You do so by calling the DataServiceTransaction.getCurrentDataServiceTransaction().isRefill()
method in your ColdFusion application as follows:

 <cfscript>
dst = CreateObject("java", "flex.data.DataServiceTransaction");
t = dst.getCurrentDataServiceTransaction();
isRefill = t.isRefill();
</cfscript>

This does not work over RMI when ColdFusion and Flex are not in the same web application.


Creating the get method
The get method retrieves a specific record. The get method calls the lower level read method. If you use the
Bean/DAO methodology, as described in "Writing the ColdFusion CFCs" on page 680, you create the lower level read
method separately in the DAO CFC.

The following example shows the essential elements of a get method:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                685
Flex and AIR Integration in ColdFusion




<cffunction name="get" output="no" returnType="samples.contact.Contact" access="remote">
     <cfargument name="uid" type="struct" required="yes">
     <cfset key = uid.contactId>
     <cfset ret=variables.dao.read(id=key)>
     <cfreturn ret[1]>
</cffunction>

The returntype of a get method can be any of the following:

* The Value Object CFC

* Any

* An array


Creating the sync method
The sync method lets you keep track of synchronization conflicts by accepting a change list, which is an array of
change objects. In the sync method, you pass in an array of changes, loop over the array and apply the changes, and
then return the change objects, as follows:

<cffunction name="sync" output="no" returnType="array" access="remote">
     <cfargument name="changes" type="array" required="yes">


     <!-- Create the array for the returned changes. -->
     <cfset var newchanges=ArrayNew(1)>


     <!-- Loop over the changes and apply them. --->
     <cfloop from="1" to="#ArrayLen(changes)#" index="i" >
           <cfset co = changes[i]>
           <cfif co.isCreate()>
                 <cfset x = doCreate(co)>
           <cfelseif co.isUpdate()>
                 <cfset x = doUpdate(co)>
           <cfelseif co.isDelete()>
                 <cfset x = doDelete(co)>
           </cfif>
           <cfset ArrayAppend(newchanges, x)>
     </cfloop>


     <!-- Return the change objects, which indicate success or failure. --->
     <cfreturn newchanges>
</cffunction>


Creating the count method
The count method returns a number that indicates how many records are in a result set. If you use the Bean/DAO
methodology, as described in "Writing the ColdFusion CFCs" on page 680, you create the lower level count method
separately in the DAO CFC.

The count method contains the following essential elements, without any error handling:

<cffunction name="count" output="no" returntype="Numeric" access="remote">
     <cfargument name="param" type="string" required="no">
     <cfreturn variables.dao.count()>
</cffunction>

This count method calls a different count method in the DAO CFC, which contains the following essential elements,
without any error handling:



                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  686
Flex and AIR Integration in ColdFusion




<cffunction name="count" output="false" access="public" returntype="Numeric">
     <cfargument name="id" required="false">
     <cfargument name="param" required="false">
     <cfset var qRead="">


     <cfquery name="qRead" datasource="FDSCFCONTACT">
           select COUNT(*) as totalRecords
           from Contact
     </cfquery>


     <cfreturn qRead.totalRecords>
</cffunction>



Notifying the Flex application when data changes
You use the LiveCycle Data Services ES event gateway type provided with ColdFusion, to have ColdFusion
applications notify Flex when data that a destination manages has changed. You configure the LiveCycle Data Services
ES event gateway and write an application that uses the event gateway. For more information, see "Using the Data
Management Event Gateway" on page 1316.


Authentication
To authenticate users when using the LiveCycle Data Services ES assembler, you use the Flex
setRemoteCredentials() method on the DataService object. The credentials, which are in the FlexSession object,
are passed to the ColdFusion application, where you can use the cflogin tag to perform authentication. Alternatively,
you can set credentials in the Flex destination, although it is not the recommended way to do so.

You can set the credentials by doing either of the following:

* Specifying credentials in ActionScript

* Specifying credentials in the Flex destination


Specifying credentials in ActionScript
To specify credentials in ActionScript, you use the setRemoteCredentials() method, as the following example
shows:

ds = new DataService("mydest");
ds.setRemoteCredentials("wilsont", "password");


Specifying credentials in the Flex destination
To specify credentials in the Flex destination, you edit the data-management-config.xml file that is in the WEB-
INF/flex folder of the server on which you run the Flex application. In the properties element, you include the
remote-username and remote-password elements, as follows:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  687
Flex and AIR Integration in ColdFusion




 <destination id="cfcontact">
      <adapter ref="coldfusion-dao" />
           <channels>
                 <channel ref="cf-dataservice-rtmp" />
           </channels>
      <properties>
           <source>samples.contact.ContactAssembler</source>
           <scope>application</scope>
           <remote-username>wilsont</remote-username>
           <remote-password>password</remote-password>
...
      /properties>
</destination>



Enabling SSL
You encrypt communication between ColdFusion and Flex by enabling Secure Sockets Layer (SSL). Enabling SSL only
makes sense if you are running LiveCycle Data Services ES remotely. To use SSL, create a keystore file. The keystore is
a self-signed certificate. (You do not require a certificate signed by a Certificate Authority, although if you do use one,
you do not have to configure Flex as indicated in the following steps.) The information in the keystore is encrypted
and can be accessed only with the password that you specify. To create the keystore, you use the Java keytool utility,
which is included in your Java Runtime Environment (JRE).

To enable SSL, you do the following:

1 Create the keystore

2 Configure Flex

3 Enable SSL in the ColdFusion Administrator


Create the keystore
Generate the SSL server (ColdFusion) keystore file by using the keytool utility, with a command like the following:

 keytool -genkey -v -alias FlexAssembler -dname "cn=FlexAssembler" -keystore cf.keystore -
keypass mypassword -storepass mypassword

The following table describes the parameters of the keytool utility that you use:


 Parameter                Description

 -alias                   The name of the keystore entry. You can use any name as long as you are consistent when referring to it.

 -dname                   The Distinguished Name, which contains the Common Name (cn) of the server.

 -keystore                The location of the keystore file.

 -keypass                 The password for your private key.

 -storepass               The password for the keystore. The encrypted storepass is stored in ColdFusion configuration files.

 -rfc                     Generates the certificate in the printable encoding format.

 -file                    The name of the keystore file.

 -v                       Generates detailed certificate information.


Next, you place the certificate that you created in the file that the JVM uses to decide what certificates to trust. The file
in which you place the certificate (usually named cacerts), is located in the JRE, under the lib/security folder.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 688
Flex and AIR Integration in ColdFusion




Configure Flex
1 Export the keystore to a certificate by using the keytool utility, with a command like the following:

     keytool -export -v -alias FlexAssembler -keystore cf.keystore -rfc -file cf.cer

2 Import the certificate into the JRE cacerts file for your server by using the keytool utility, with a command like the
    following:

     keytool -import -v -alias FlexAssembler -file cf.cer -keystore
    C:\fds2\UninstallerData\jre\lib\security\cacerts

    The previous example specifies the location of the keystore for LiveCycle Data Services ES with integrated JRun,
    installed using the default settings. If you are using a different server, specify the location of the cacerts file for the
    JRE that you are using. For example, if you are using JBoss, you specify the keystore location as
    $JAVA_HOME/jre/lib/security/cacerts.


Enable SSL in the ColdFusion Administrator
1 In the ColdFusion Administrator, select Data & Services > Flex Integration, and specify the keystore file in the Full
    Path to Keystore text box.

2 Specify the keystore password in the Keystore password text box.

3 Select the Enable RMI over SSL for Data Management option, and then click Submit Changes.

    If you specify an invalid keystore file or password, ColdFusion does not enable SSL, and disables Flex Data
    Management Support.


Data translation
The following table lists the ColdFusion data types and the corresponding Adobe Flash or ActionScript data type:


 ColdFusion data type                   Flash data type

 String                                 String

 Array                                  [] = Array

 Struct                                 {} = untyped Object

 Query                                  ArrayCollection

 CFC                                    Class = typed Object (if a matching ActionScript class exists, otherwise the CFC becomes a
                                        generic untyped Object (map) in ActionScript)

 CFC Date                               ActionScript Date

 CFC String                             ActionScript String

 CFC Numeric                            ActionScript Numeric

 ColdFusion XML Object                  ActionScript XML Object




Using Server-Side ActionScript

Adobe ColdFusion server configuration includes the Flash Remoting service, a module that lets Adobe Flash
developers create server-side ActionScript. These ActionScript files can directly access ColdFusion query and HTTP
features through two new ActionScript functions: CF.query and CF.http.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     689
Flex and AIR Integration in ColdFusion




About server-side ActionScript
ColdFusion includes a module called the Flash Remoting service that acts as a broker for interactions between Flash
and ColdFusion. Flash Remoting supports a range of object types, and lets you reference an ActionScript file that lives
on a ColdFusion server. You can partition data-intensive operations on the server, while limiting the amount of
network transactions necessary to get data from the server to the client.

Flash developers can create server-side ActionScript files to access ColdFusion resources; they do not have to learn
CFML (ColdFusion Markup Language). This ability lets you logically separate the Flash presentation elements of your
applications from the business logic. You have the option of creating ActionScript files that reside on the server to
partition this processing away from your client applications.

You have a simple interface for building queries using server-side ActionScript, and an equally simple interface for
running these queries from your client-side ActionScript.


Client-side ActionScript requirements
On the client side, you only need a small piece of code that establishes a connection to the Flash Remoting service and
references the server-side ActionScript you want to use.

For example (notice the embedded comments):

// This #include is needed to connect to the Flash Remoting service
#include "NetServices.as"


// This line determines where Flash should look for the Flash Remoting service.
// Ordinarily, you enter the URL to your ColdFusion server.
// Port 8500 is the Flash Remoting service default.
NetServices.setDefaultGatewayUrl("http://mycfserver:8500");


// With the Flash Remoting service URL defined, you can create a connection.
gatewayConnnection = NetServices.createGatewayConnection();


// Reference the server-side ActionScript.
// In this case, the stockquotes script file lives in the web root of the
// ColdFusion server identified previously. If it lived in a subdirectory
// of the web root called "mydir," you would reference it
// as "mydir.stockquotes".
stockService = gatewayConnnection.getService("stockquotes", this);


// This line invokes the getQuotes() method defined in the stockquotes
// server-side ActionScript.
stockService.getQuotes("macr");


// Once the record set is returned, you handle the results.
// This part is up to you.
function getQuotes_Result ( result )
{
     // Do something with results
}

Note: Client-side ActionScript does not support the two new server-side ActionScript functions, CF.query and CF.http.


Server-side requirements
Creating ActionScript that executes on the server helps leverage your knowledge of ActionScript. It also provides direct
access to ColdFusion query and HTTP features. The CF.query and CF.http ActionScript functions let you perform
ColdFusion HTTP and query operations.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     690
Flex and AIR Integration in ColdFusion




Note: On the server side, ActionScript files use the extension .asr.

For example, the following server-side ActionScript code builds on the client-side code shown previously:

// Filename: stockquotes.asr
// Here is the getQuotes method invoked in the client-side ActionScript.
// It accepts a single stock quote symbol argument.
function getQuotes(symbol)
{
     // Query some provider for the specified stock quote and return the
     // results. In this case, the getQuotesFromProvider method is
     // defined elsewhere in this ActionScript code.
     data = getQuotesFromProvider(symbol);
     // Return the data to the client.
     // Note: this example does not include any of the error checking
     // logic you would normally use prior to returning the data.
     return data;
}

The getQuotes function conducts the stock quote request and returns the results of the request to the client as a
RecordSet object.


Software requirements
To use server-side ActionScript files, you must have the following software installed:

* Adobe Flash

* ColdFusion

* Flash Remoting Components

For more information about these products, go to www.adobe.com.


Location of server-side ActionScript files
You can place ActionScript files (*.asr) on the server anywhere below the root directory of the web server. To specify
subdirectories of the web root or a virtual directory, use package dot notation (use dots instead of slashes in a fully
qualified directory name). For example, in the following assignment code, the stockquotes.asr file is located in the
mydir/stock/ directory:

stockService = gatewayConnnection.getService("mydir.stock.stockquotes", this);

You can also point to virtual mappings, such as cfsuite.asr.stock.stockquotes where cfsuite is a virtual
mapping and asr.stock is subdirectories of that mapping.


Benefits
Server-side ActionScript lets your ActionScript engineers use their knowledge of ActionScript to write code for the
back end of their SWF files, which can mean more meaningful levels of interactivity for your users. Your SWF files can
share a library of server-side ActionScript functions, which means you can define functions that are tailored to your
own business.

You could, for example, create a server-side ActionScript file that defines a whole library of SQL query methods. With
these query methods defined on the server side, your Flash designers only have to run the specific query function they
want to return data to their SWF movies. They do not have to write any SQL, and they do not have to create a query
every time they retrieve data from a ColdFusion data source. It is a way of creating reusable queries that your entire
Flash design team can use.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      691
Flex and AIR Integration in ColdFusion




Coding the ColdFusion query and HTTP operations in ActionScript is very straightforward. The CF.query and
CF.http functions provide a well-defined interface for building SQL queries and HTTP operations.

For example, the following is a typical server-side ActionScript function definition that returns query data:

// This function shows a basic CF.query operation using only
// arguments for data source name and for SQL.
function basicQuery()
{
     mydata = CF.query({datasource:"customers",
           sql:"SELECT * FROM myTable"});
     return mydata;
}


What to do next
If you are already familiar with ActionScript, here a few things to get you started:

* How to establish a connection with the Flash Remoting service using client-side ActionScript. See "Connecting to
   the Flash Remoting service" on page 691

* How to reference server-side ActionScript functions and methods. See "Using server-side ActionScript functions"
   on page 692.

* How to code the server-side CF.query and CF.http functions. See "Using the CF.query function" on page 694 and
   "Using the CF.http function" on page 700. Also see the reference pages for these functions in the CFML Reference.

For additional information on using Flash Remoting, see "Using the Flash Remoting Service" on page 606 and Using
Flash Remoting.


Connecting to the Flash Remoting service
Before you can use functions defined in your server-side ActionScript files, connect the Adobe SWF movie to the
server-side Flash Remoting service.


Create a Flash Remoting service connection
1 Include the necessary ActionScript classes in the first frame of the SWF movie that uses server-side ActionScript
   functions.

   a Use the following command to include the NetServices class:

       #include "NetServices.as"

   b (Optional) Use the following command to include the NetDebug class:

       #include "NetDebug.as"

       For more information about the NetDebug and RecordSet classes, see Using Flash Remoting.

2 Since the Flash Remoting service serves as a broker for calls to server-side ActionScript functions, identify the Flash
   Remoting service URL as an argument in the NetServices.setDefaultGatewayUrl function. For example:

    NetServices.setDefaultGatewayURL("http://localhost:8500/flashservices")

   Specify a server host name. The default port number for the Flash Remoting service is 8500.

3 Create the gateway connection using the NetServices.createGatewayConnection function; for example:

    gatewayConnection = NetServices.createGatewayConnection();




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       692
Flex and AIR Integration in ColdFusion




Using server-side ActionScript functions
After you connect to the Flash Remoting service, you call functions that are defined in your server-side ActionScript
files, and return results.


Call a function
1 Create an instance of the server-side ActionScript file using the getService function. This function instantiates
    the server-side ActionScript file as an object to use on the client side. For example:

    albumService = gatewayConnection.getService("recordsettest", this)

    Where recordsettest represents the name of the server-side ActionScript file, without the filename extension
    .asr.

2 Call a function defined in your server-side ActionScript object. Use dot notation to specify the object name followed
    by the function name; for example:

    albumService.getAlbum("The Color And The Shape", "1999");

    Where albumService is the instance of the server-side ActionScript file and getAlbum is a function that passes two
    arguments, "The Color and The Shape" and "1999".

    Note: Arguments must occur in the order defined in the function declaration.

3 Handle the function results in ActionScript. See "Using the function results in ActionScript" on page 692.


Using the function results in ActionScript
To use the results returned by server-side ActionScript, create a corresponding results function. The results function
uses a special naming convention that ties it to the function that calls the server-side ActionScript. For example, if you
defined a client-side ActionScript function called basicCustomerQuery, you also must create a results function called
basicCustomerQuery_Result.

The results returned by server-side ActionScript functions differ somewhat depending on whether you are using
CF.http or CF.query:

* The CF.query function returns a record set, which you manipulate using methods available in the RecordSet
    ActionScript class object. See Using results returned by the CF.query function section.

* The CF.http function returns simple text strings through properties that you reference in your server-side
    ActionScript. See Using results returned by the CF.http function section.


Using results returned by the CF.query function
You use functions in the RecordSet ActionScript object to access the data returned in a CF.query record set; for
example, how many records are in the record set and the names of the columns. You can also use the RecordSet
functions to pull the query data out of the record set. To do so, you reference a specific row number in the record set
and use the getItemAt RecordSet function, as in the following example:

// This function populates a Flash text box with data in the first row
// of the record set under the "email" column name.
function selectData_Result ( result )
{
      stringOutput.text = result.getItemAt(0)["email"];
      _root.employeesView.setDataProvider(result);
}

In the example, the column name is referenced in the getItemAt function between square brackets [ ]. (In
ActionScript, indexes start at 0, so getItemAt(0) returns the first row.)



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         693
Flex and AIR Integration in ColdFusion




For more information, see "Using the CF.query function" on page 694.


Using results returned by the CF.http function
The CF.http server-side ActionScript function returns data as simple text. You write server-side functions that
reference the properties available in the object returned by the CF.http function. These properties store the file
content of the retrieved file, HTTP status codes, the MIME type of the returned file, and so on. On the client side, you
create return functions to handle data returned by the CF.http function. You write these functions to handle simple
text data.

For more information, see "Using the CF.http function" on page 700.


Global and request scope objects
Global and request scope objects are implicitly available in all server-side ActionScript. The following table describes
these scope objects:


 Scope name           Type                Description

 config               Global              Initialization information for the server-side ActionScript adapter.

                                          Class: javax.servlet.ServletConfig

 application          Global              The context for the current web application. The context defines methods that provide, for
                                          example, the MIME type of a file that can be used to write to a log file. There is one context per
                                          web application.

                                          Class: javax.servlet.ServletContext

 request              Request             An object containing client request information. The object provides data, including
                                          parameter name and values, attributes, and an input stream.

                                          Class: HttpServletRequest (subtype of javax.servlet.ServletRequest)

 response             Request             An object to assist in sending a response to the client. It provides HTTP-specific functionality
                                          in sending a response. Do not use the OutputStream or PrintWriter to send data back to the
                                          client.

                                          Class: HttpServletResponse (subtype of javax.servlet.ServletResponse)


For more information about these scope objects, see the documentation on the javax.servlet class at
http://java.sun.com.


About the CF.query function and data sources
You use the CF.query function to populate SWF movie elements with data retrieved from a ColdFusion data source.
To use the CF.query function you do the following:


Pull data into your SWF movie from a ColdFusion data source
1 Create a server-side ActionScript file that performs queries against a ColdFusion data source.

2 Write ActionScript code in your SWF movie that references your ActionScript file (.asr) on the ColdFusion server.

You create server-side ActionScript to execute the query and return the data in a record set to the client--your SWF
movie. You can use methods in the RecordSet ActionScript object on the client to manipulate data in the record set
and present data in your SWF movie.

Note: Client-side ActionScript files use the .as extension. Server-side ActionScript files use the .asr (ActionScript remote)
extension.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   694
Flex and AIR Integration in ColdFusion




Publishing dynamic data
You use the server-side ActionScript feature in ColdFusion to publish dynamic data. To do this, you write server-side
ActionScript files that perform queries against ColdFusion data sources. Before using ActionScript, you must
understand how to do the following:

* Create database queries in the server-side ActionScript file using the CF.query ActionScript function. See "Using
   the CF.query function" on page 694.

* Reference the server-side ActionScript file in your SWF movie. See "Connecting to the Flash Remoting service" on
   page 691.

Using the CF.query function, you can do the following tasks:

* Create user login interfaces that validate users against a ColdFusion data source.

* Populate form elements and data grids with data from a ColdFusion data source.

* Create banners that pull data (such as URLs or image file paths) out of a database.

The CF.query function can retrieve data from any supported ColdFusion data source (see "About ColdFusion data
sources" on page 694).


About ColdFusion data sources
For ColdFusion developers, the term data source can mean many different types of structured data accessible locally
or across a network. You can query websites, Lightweight Directory Access Protocol (LDAP) servers, POP mail
servers, and documents in a variety of formats. For server-side ActionScript, a data source ordinarily means the entry
point to a ColdFusion database.

Your ColdFusion administrator can help you identify and configure data sources. To create ActionScript files that
successfully perform queries on ColdFusion data sources, you must know how ColdFusion identifies the data source,
as well as any other parameters that affect your ability to connect to that database, such as whether a user name and
password are required to connect.

You use server-side ActionScript in ColdFusion to return record set data to a Flash client from a ColdFusion data
source. You specify the ColdFusion data source name and the SQL statement you execute on the data source as
arguments in the CF.query function in server-side ActionScript.

Typically, your server-side ActionScript handles the interaction with the ColdFusion data source, and returns a record
set to the Flash client through the Flash Remoting service.

For more detailed information about ColdFusion data sources, see Configuring and Administering ColdFusion.


Using the CF.query function
You use the CF.query function in your server-side ActionScript to retrieve data from a ColdFusion data source. This
function lets you perform queries against any ColdFusion data source.

Note: The CF.query function maps closely to the cfquery CFML tag, although it currently supports a subset of the
cfquery attributes.

Use the CF.query function to do the following:

* Identify the data source you want to query.

* Pass SQL statements to the data source.

* Pass other optional parameters to the database.

For reference information about the CF.query function, see CF.query in the CFML Reference.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                             695
Flex and AIR Integration in ColdFusion




About CF.query function syntax
You can write the CF.query ActionScript function using either named arguments or positional arguments. The
named argument style is more readable, but it requires more code. Although the positional argument style supports a
subset of CF.query arguments, it allows a more compact coding style that is more appropriate for simple expressions
of the CF.query function.


Using CF.query named argument syntax
The CF.query function accepts the following named arguments:

// CF.query named argument syntax
CF.query
       ({
           datasource:"data source name",
           sql:"SQL stmts",
           username:"username",
           password:"password",
           maxrows:number,
           timeout:milliseconds
       })

Note: The named argument style requires curly brackets {} to surround the function arguments.


Using CF.query positional argument syntax
Positional arguments support a subset of CF.query arguments, and you can create more efficient code. The following
is the syntax for the positional argument style:

// CF.query positional argument syntax
CF.query(datasource, sql);
CF.query(datasource, sql, maxrows);
CF.query(datasource, sql, username, password);
CF.query(datasource, sql, username, password, maxrows);

Note: When using positional arguments, do not use curly braces {}.



About the CF.query record set
The CF.query function returns a RecordSet object, which is an instance of the RecordSet class of objects. The
RecordSet class provides a wide range of functions for handling record set data.

You use methods in the RecordSet ActionScript class in your client-side ActionScript to change data returned in the
CF.query record set.

Currently, the following methods are available in the RecordSet class:


 Method                          Description

 addItem                         Appends a record to the end of the specified RecordSet

 addItemAt                       Inserts a record at the specified index

 addView                         Requests notification of changes in a RecordSet object's state

 filter                          Creates a RecordSet object that contains selected records from the original RecordSet object

 getColumnNames                  Returns the names of all the columns of the RecordSet

 getItemAt                       Retrieves a record from a RecordSet object

 getItemID                       Gets the unique ID corresponding to a record




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 696
Flex and AIR Integration in ColdFusion




Method                           Description

getLength                        Returns the total number of records in a RecordSet object

getNumberAvailable               Returns the number of records that have been downloaded from the server

isFullyPopulated                 Determines whether a RecordSet object can be edited or manipulated

isLocal                          Determines whether a RecordSet object is local or server-associated

removeAll                        Removes all records from the RecordSet object

removeItemAt                     Removes a specified record

replaceItemAt                    Replaces the entire contents of a record

setDeliveryMode                  Changes the delivery mode of a server-associated record set

setField                         Replaces one field of a record with a new value

sort                             Sorts all records by a specified compare function

sortItemsBy                      Sorts all the records by a selected field


These functions are available for every RecordSet object returned by the CF.query function to the Flash client. You
run these functions as follows:

objectName.functionName();

For example, in the result function that you create to handle record set data returned by the CF.query function, you
can reference the database column names returned in the record set using the getColumnNames RecordSet function:

function selectData_Result ( result )
{
     //result holds the query data; employeesView is a Flash list box
     stringOutput.text = result.getColumnNames();
     _root.employeesView.setDataProvider(result);
}



Building a simple application
The following procedure describes how to build a simple server-side ActionScript application. The example
application, a corporate personnel directory, uses the NetServices object to connect to the personneldirectory
server-side ActionScript. The personneldirectory server-side ActionScript retrieves data from a ColdFusion data
source and returns the results to the SWF file as a RecordSet object.

Note: The server-side ActionScript application that you create provides the back-end services in an application.

This example requires the following:

* A server-side ActionScript file named personneldirectory.asr that includes functions that interact with a
   ColdFusion data source.

* A client-side SWF movie in which the NetServices object is created.


Create the application
1 Write server-side ActionScript that performs the database query and returns data to the client through the Flash
   Remoting service.

2 Create the SWF movie interface. See "Creating the SWF movie interface" on page 697.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  697
Flex and AIR Integration in ColdFusion




3 Define a search function that sends user data to the Flash Remoting service. See "Submitting user data to the Flash
   Remoting service" on page 697.

4 Define a result function that captures the results returned from the Flash Remoting service. See "Capturing Flash
   Remoting service results" on page 698.

5 Ensure that the SWF movie has established a connection to the Flash Remoting service. See "Checking for a Flash
   Remoting service connection" on page 699.


Writing the server-side ActionScript function
The example here creates a search function that performs a simple search operation against a ColdFusion data source.
This function accepts two arguments, firstName and lastName, and returns any records found that match these
arguments.


Create a server-side ActionScript function
 Create a server-side ActionScript file that contains the following code:

    //search takes firstName lastName arguments
   function search(firstName, lastName)
   {
         searchdata = CF.query({datasource: "bigDSN",
               sql:"SELECT * from personnel WHERE fname = firstName AND lname = lastName"{);


         if (searchdata)
               return searchdata;
         else
               return null;
   }


Creating the SWF movie interface
The SWF movie interface example here consists of one frame with a variety of text boxes and a submit button.

1 In the Flash authoring environment, create a Flash source file, and save it as pDirectory.fla.

2 Create two input text boxes. Name one text box variable lastName and the other firstName.

3 Create a dynamic text box, and name its variable status.

4 Insert a list box component, and name it dataView.

5 Insert a push-button component.

6 Save your work.


Submitting user data to the Flash Remoting service
To send data to server-side ActionScript, create a function that passes the data from the SWF movie to server-side
ActionScript. The search function, applied at the frame level, collects the user-entered data from the firstName and
lastName text boxes and passes the data as function arguments to the directoryService object, which is created when
the SWF movie connects to the Flash Remoting service. For more information, see "Checking for a Flash Remoting
service connection" on page 699.

The following is a Flash ActionScript example:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               698
Flex and AIR Integration in ColdFusion




#include "NetServices.as"
function search()
{
     // The search() method is defined in the server-side AS file
     directoryService.search(firstName.text, lastName.text);
     dataView.setDataProvider(null);
     status.text = "waiting...";
}


Reviewing the code
The following table describes the code and its function:


Code                                                 Description

directoryService.search                              Passes the contents of the firstName and lastName text boxes to server-side
                                                     ActionScript.
(firstName.text, lastName.text);


dataView.setDataProvider                             Clears the dataView list box component.

(null);


status.text = "waiting...";                          Displays a message in the status text box while the record set is being
                                                     retrieved from server-side ActionScript.



Capturing Flash Remoting service results
When you create a function that calls a server-side ActionScript function, also create a function to handle the data
returned by server-side ActionScript. Define the function with the same name as the function making the initial call,
but you append _Result to the name.

For example, if you create a function called basicQuery to return query data, you define a results function to handle
returned data; declare the results function as basicQuery_Result.

In the following example, the results function search_Result supplies the record set to the
dataView.setDataProvider function:

function search_Result(resultset)
{
     dataView.setDataProvider(resultset);
     status.text = (0+resultset.getLength())+" names found.";
}


Reviewing the code
The following table describes the code and its function:


Code                                                 Description

function search_Result                               The _Result suffix tells the Flash Remoting service to return the results of
                                                     the search function to this function.
(resultset)


dataView.setDataProvider                             Assigns the results returned by the Flash Remoting service to the dataView
                                                     list box.
(resultset);


status.text = (0+resultset.                          Displays the number of records returned by the Flash Remoting service.

getLength())+" names found.";




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    699
Flex and AIR Integration in ColdFusion




Checking for a Flash Remoting service connection
To ensure that the SWF movie is connected to the Flash Remoting service, you use an if statement; for example:

if (inited == null)
{
     inited = true;
     NetServices.setDefaultGatewayUrl("http://localhost:8500/flashservices/
           gateway");
     gateway_conn = NetServices.createGatewayConnection();
     directoryService = gateway_conn.getService(personneldirectory, this);
     status.text = "Type into the text boxes, then click 'Search'";
}

In this example, the inited variable is evaluated for a value. If inited is null (not connected), the movie connects to
the Flash Remoting service using the NetServices object. For more information about connecting to the Flash
Remoting service, see "Connecting to the Flash Remoting service" on page 691.


About the CF.http function
You use the CF.http ActionScript function to retrieve information from a remote HTTP server using HTTP Get and
Post methods, as follows:

* Using the Get method, you send information to the remote server directly in the URL. This method is common for
   a one-way transaction in which the CF.http function retrieves an object, such as the contents of a web page.

* The Post method can pass variables to a form or CGI program, and can also create HTTP cookies.

The most basic way to use the CF.http function is to use it with the Get method argument to retrieve a page from a
specified URL. The Get method is the default for the CF.http function.

The following server-side example retrieves file content from the specified URL:

function basicGet(url)
{
     // Invoke with just the url argument. This is an HTTP GET.
     result = CF.http(url);
     return result.get("Filecontent");
}

The client-side example could look like the following:

#include "NetServices.as"
NetServices.setDefaultGatewayUrl("http://mycfserver:8500");
gatewayConnnection = NetServices.createGatewayConnection();
myHttp = gatewayConnnection.getService("httpFuncs", this);


// This is the server-side function invocation
url = "http://anyserver.com";
myHttp.basicGet(url);


// Create the results function
function basicGet_Result()
{
     url = "http://anyserver.com
     ssasFile.basicGet(url)
}




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                            700
Flex and AIR Integration in ColdFusion




Using the CF.http function
The CF.http function returns an object that contains properties, also known as attributes. You reference these
attributes to access the contents of the file returned, header information, HTTP status codes, and so on. The following
table shows the available properties:


 Property                   Description

 Text                       A Boolean value indicating whether the specified URL location contains text data.

 Charset                    The charset used by the document specified in the URL.

                            HTTP servers normally provide this information, or the charset is specified in the charset parameter of the
                            Content-Type header field of the HTTP protocol. For example, the following HTTP header announces that the
                            character encoding is EUC-JP:

                            Content-Type: text/html; charset=EUC-JP

 Header                     Raw response header. The following is an example header:

                            HTTP/1.1 200 OK

                            Date: Mon, 04 Mar 2002 17:27:44 GMT

                            Server: Apache/1.3.22 (Unix) mod_perl/1.26

                            Set-Cookie: MM_cookie=207.22.48.162.4731015262864476;

                            path=/; expires=Wed, 03-Mar-04 17:27:44 GMT;

                            domain=.adobe.com

                            Connection: close

                            Content-Type: text/html

 Filecontent                File contents, for text and MIME files.

 Mimetype                   MIME type. Examples of MIME types include text/html, image/png, image/gif, video/mpeg, text/css, and
                            audio/basic.

 responseHeader             Response header. If there is one instance of a header key, this value can be accessed as a simple type. If there is
                            more than one instance, values are put in an array in the responseHeader structure.

 Statuscode                 HTTP error code and associated error string. Common HTTP status codes returned in the response header include
                            the following:

                            400: Bad Request

                            401: Unauthorized

                            403: Forbidden

                            404: Not Found

                            405: Method Not Allowed



Referencing HTTP Post parameters in the CF.http function
To pass HTTP Post parameters in the CF.http function, construct an array of objects and assign this array to a
variable named params. The following arguments can only be passed as an array of objects in the params argument of
the CF.http function:




                                                       

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     701
Flex and AIR Integration in ColdFusion




 Parameter                  Description

 name                       The variable name for data that is passed

 type                       Transaction type:

                            * URL

                            * FormField

                            * Cookie

                            * CGI

                            * File

 value                      Value of URL, FormField, Cookie, File, or CGI variables that are passed


In the following example, the CF.http function passes HTTP Post parameters in an array of objects:

function postWithParamsAndUser()
{
     // Set up the array of Post parameters. These are just like cfhttpparam tags.
     params = new Array();
     params[1] = {name:"arg2", type:"URL", value:"value2"};


     url = "http://localhost:8500/";


     // Invoke with the method, url, params, username, and password
     result = CF.http("post", url, params, "karl", "salsa");
     return result.get("Filecontent");
}


Using the CF.http Post method
You use the Post method to send cookie, form field, CGI, URL, and file variables to a specified ColdFusion page or CGI
program for processing. For POST operations, use the params argument for each variable that you post. The Post method
passes data to a specified ColdFusion page or an executable that interprets the variables being sent, and returns data.

For example, when you build an HTML form using the Post method, you specify the name of the page to which form
data is passed. You use the Post method in the CF.http function in a similar way. However, with the CF.http
function, the page that receives the Post does not display anything. See the following example:

function postWithParams()
{
     // Set up the array of Post parameters. These are just like cfhttpparam tags.
     // This example passes formfield data to a specified URL.
     params = new Array();
     params[1] = {name:"Formfield1", type:"FormField", value:"George"};
     params[2] = [name:"Formfield2", type:"FormField", value:"Brown"};


     url = "http://localhost:8500/";


     // Invoke CF.http with the method, url, and params
     result = CF.http("post", url, params);
     return result.get("Filecontent");
}




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   702
Flex and AIR Integration in ColdFusion




Using the CF.http Get method
You use the Get method to retrieve files, including text and binary files, from a specified server. You reference
properties of the object returned by the CF.http function to access things like file content, header information, MIME
type, and so on.

The following example uses the CF.http function to show a common approach to retrieving data from the web:

// Returns content of URL defined in url variable
// This example uses positional argument style
function get()
{
     url = "http://www.adobe.com/software/coldfusion/";


     //Invoke with just the url argument. Get is the default.
     result = CF.http(url);
     return result.get("Filecontent");
}

For more information about CF.http function properties, see CF.http in the CFML Reference.




                                              

                        