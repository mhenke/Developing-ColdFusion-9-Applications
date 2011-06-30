Chapter 6: Developing CFML Applications

Designing and Optimizing a ColdFusion Application

Application elements and how you structure an application on your server make your Adobe ColdFusion pages an
effective Internet application. You use the Application.cfc and Application.cfm files and various coding methods to
optimize the efficiency of your application.


About applications
The term application can mean many things. An application can be as simple as a guest book or as sophisticated as a
full Internet commerce system with catalog pages, shopping carts, and reporting.

An application, however, has a specific meaning in ColdFusion. A ColdFusion application has the following
characteristics:

· It consists of one or more ColdFusion pages that work together and share a common set of resources.

· All pages in the application share an application name and configuration settings as specified in an Application.cfc
    file or a cfapplication tag.

· All pages in the application share variables in the Application scope.

· You can write application-wide event handlers for specific events, such as request start or session end.

What appears to a user to be a single application (for example, a company's website), can consist of multiple
ColdFusion applications.

ColdFusion applications are not J2EE applications. However, if you do not specify an application name in your
Application.cfc file or cfapplication tag, the Application scope corresponds to the J2EE application servlet context.

ColdFusion applications end when the application has been inactive for the application time-out period or the server
stops. When the application times out, ColdFusion releases all Application scope variables. You must, therefore, select
a time-out period that balances the need for clearing Application scope memory and the overhead of re-creating the
scope. A typical application time-out is two days.

ColdFusion applications and sessions are independent of each other. For example, if an application times out while a
user's session is active, the session continues and the session context, including the user's Session scope variables, is
unaffected by the application ending and restarting.

Although, there are no definite rules about how you represent your web application as a ColdFusion application or
applications, the following guidelines are useful:

· Application pages share a common general purpose. For example, a web storefront is typically a single ColdFusion
    application.

· Many, but not necessarily all, pages in a ColdFusion application share data or common code elements, such as a
    single login mechanism.

· Application pages share a common look-and-feel, often enforced by using common code elements, such as the
    same header and footer pages, and a common error message template.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   236
Developing CFML Applications




Elements of a ColdFusion application
Before you develop a ColdFusion application, determine how to structure the application and how to handle
application-wide needs and issues. In particular, consider all of the following:

· The overall application framework

· Reusable application elements

· Shared variables

· Application events and the Application.cfc file

· Application-level settings and functions

· Application security and user identification


The application framework
The application framework is the overall structure of the application and how your directory structure and application
pages reflect that structure. Use a single application framework to structure multiple ColdFusion applications into a
single website or Internet application. You can structure a ColdFusion application by using many methodologies. For
example, the Fusebox application development methodology is one popular framework for developing ColdFusion
web applications. (For more information on Fusebox, see www.fusebox.org.)

Information on how to use or develop a specific application framework is not provided. However, there is information
about the tools that ColdFusion provides for building your framework, including the Application.cfc file, how an
application's directory structure affects the application, and how you map the directory structure. For more
information on mapping the application framework, see "Structuring an application" on page 239.

Note: For one example of an application framework, see "ColdFusion Methodologies for Content Management,"
available at www.adobe.com/go/learn_cfu_content_mgmt_en.


Reusable application elements
ColdFusion provides a variety of reusable elements that you use to provide commonly used functionality and extend
CFML. These elements include the following:

· User-defined functions (UDFs)

· CFML custom tags

· ColdFusion components (CFCs)

· CFX (ColdFusion Extension) tags

· Pages that you include using the cfinclude tag

For an overview of these elements, and information about how to choose among them, see "Creating ColdFusion
Elements" on page 146.


Shared variables
The following ColdFusion variable scopes maintain data that lasts beyond the scope of the current HTTP request:


 Variable scope          Variables available

 Server                  To all applications on a server and all clients




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                      237
Developing CFML Applications




 Variable scope          Variables available

 Application             To all pages in an application for all clients

 Client                  For a single client browser over multiple browser sessions in one application

 Session                 For a single client browser for a single browser session in one application


For more information on using these variables, including how to use locks to ensure that the data they contain remains
accurate, see "Using Persistent Data and Locking" on page 301.


Application events and the Application.cfc file
Application events are specific occurrences during the life cycle of an application. Each time one of these events occurs,
ColdFusion runs the corresponding method in your Application.cfc file (also referred to as the application CFC). The
Application.cfc file defines application settings and implements methods to handle the application events.

Implement application CFC methods to handle the following events:


 Event                   Trigger

 Application start       ColdFusion starts processing the first request for a page in an application that is not running.

 Application end         An application time-out setting is reached or the server shuts down.

 Session start           A new session is created as a result of a request that is not in an existing session.

 Session end             A session time-out setting is reached.

 Request start           ColdFusion receives a request, including HTTP requests, messages to the event gateway, SOAP requests, or Flash
                         Remoting requests.

 Request                 Immediately after ColdFusion finishes processing the request start event. The handler for this event is intended
                         for use as a filter for the request contents. For more information on the differences between request start and
                         request events, see "Managing requests in Application.cfc" on page 246.

 Request end             ColdFusion finishes processing all pages and CFCs for the request.

 Exceptions              An exception occurs that is not handled in a try/catch block.


The Application.cfc file also defines application-wide settings, including the application name and whether the
application supports Session variables.

For more information on using application events and the Application.cfc file, see "Defining the application and its
event handlers in Application.cfc" on page 241.


Other application-level settings and functions
Adobe recommends that when defining application-level settings, variables, and functions in new code, you do not
use the techniques used previous to ColdFusion MX 7. Instead, use the Application.cfc file and its variables and
methods, which provide more features and include logical, hierarchical structure.

If you do not have an Application.cfc file, ColdFusion processes the following two pages, if they are available, every
time it processes any page in the application:

· The Application.cfm page is processed before each page in the application.

· The OnRequestEnd.cfm page is processed after each page in the application.

Note: UNIX systems are case-sensitive. To ensure that your pages work on UNIX, always capitalize the A in
Application.cfm and the O, R, and E in OnRequestEnd.cfm.




                                                       

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         238
Developing CFML Applications




The Application.cfm page can define the application. It can contain the cfapplication tag that specifies the
application name, and code on this page is processed for all pages in the application. This page can define application-
level settings, functions, and features.

The OnRequestEnd.cfm page is used in fewer applications than the Application.cfm page. It lets you provide common
clean-up code that gets processed after all application pages, or specify dynamic footer pages.

   The OnRequestEnd.cfm page does not execute if the page runs a cflocation tag.


For more information on the Application.cfm and OnRequestEnd.cfm pages, see "Using an Application.cfm page" on
page 252. For information on placing these pages in the application directory structure, see "Structuring an
application" on page 239.

Note: You can create a ColdFusion application without using an Application.cfc, Application.cfm, or OnRequestEnd.cfm
page. However, it is much easier to use the Application.cfm page than to have each page in the application use a
cfapplication tag and define common application elements.


Specifying settings per application
Set the following on a per-application basis:

· Mappings

· Custom tag paths

These settings override the server-side settings in the ColdFusion Administrator for the specified application only.
Specifying per application settings does not change the server-wide settings. To set per-application settings, first enable
per-application settings on the Settings page of the ColdFusion Administrator. You then set the mappings or custom
tag paths in the Application.cfc file.

Custom Tags in per-application settings override those defined in the ColdFusion Administrator. For example, if you
have two custom tags of the same name and they are in different locations in the Administrator and per-application
settings, the one in the per-application settings is taken first.

Note: Per-application settings are supported in applications that use an Application.cfc file only, not in applications that
use an Application.cfm file. The per-application settings do not work if you have disabled application variables in the
Memory Variables page of the Administrator.


Set the mappings per application
1 Check the Enable Per App Settings option on the Settings page of the ColdFusion Administrator.

2 Include code like the following in your Application.cfc file:

    <cfset THIS.mappings["/MyMap"]="c:\inetpub\myStuff">

   or

    <cfset StructInsert(THIS.mappings, "/MyMap", "c:\inetpub\myStuff")>

   (To use the second format, you must first create a THIS.mappings structure.)


Set the custom tag paths per application
1 Check the Enable Per App Settings option on the Settings page of the ColdFusion Administrator.

2 Include code like the following in your Application.cfc file:

    <cfset customtagpaths = "c:\mapped1,c:\mapped2">
   <cfset customtagpaths = ListAppend(customtagpaths,"c:\mapped3")>
   <cfset This.customtagpaths = customtagpaths>



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         239
Developing CFML Applications




Application security and user identification
All applications must ensure that malicious users cannot make improper use of their resources. Additionally, many
applications require user identification, typically to control the portions of a site that the user accesses, to control the
operations that the user performs, or to provide user-specific content. ColdFusion provides the following forms of
application security to address these issues:

Resource (file and directory-based) security Limits the ColdFusion resources, such as tags, functions, and data sources
that application pages, in particular directories, access. Consider the resource security needs of your application when
you design the application directory structure.

User (programmatic) security Provides an authentication (login) mechanism and a role-based authorization
mechanism to ensure that users only access and use selected features of the application. User security also incorporates
a user ID, which you use to customize page content. To implement user security, you include security code, such as
the cflogin and cfloginuser tags, in your application.

For more on implementing security, see "Securing Applications" on page 339.


Structuring an application
When you design a ColdFusion application, structure its contents into directories and files, also known as mapping
the directory structure. This activity is an important step in designing a ColdFusion application. Before you start
building the application, establish a root directory for the application. You store application pages in subdirectories of
the root directory.


How ColdFusion finds and process application definition pages
ColdFusion uses the following rules to locate and process the Application.cfc, Application.cfm, and
OnRequestEnd.cfm pages that define application-specific elements. The way ColdFusion locates these files helps
determine how you structure an application.

Each time ColdFusion processes a page request it does the following:

1 When ColdFusion starts processing the request, it does the following:

   · It searches the page's directory for a file named Application.cfc. If one exists, it creates a new instance of the CFC,
      processes the initial events, and stops searching. (ColdFusion creates a new instance of the CFC and processes
      its initialization code for each request.)

   · If the requested page's directory does not have an Application.cfc file, it checks the directory for an
      Application.cfm file. If one exists, ColdFusion logically includes the Application.cfm page at the beginning of
      the requested page and stops searching further.

   · If the requested page's directory does not have an Application.cfc or Application.cfm file, ColdFusion searches
      up the directory tree and checks each directory first for an Application.cfc file and then, if one is not found, for
      an Application.cfm page, until it reaches the root directory (such as C:\). When it finds an Application.cfc or
      Application.cfm file, it processes the page and stops searching.

2 ColdFusion processes the requested page's contents.

3 When the request ends, ColdFusion does the following:

   · If you have an Application.cfc, ColdFusion processes the CFC's onRequestEnd method and releases the CFC
      instance.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    240
Developing CFML Applications




   · If you do not have an Application.cfc, but do have an Application.cfm page, ColdFusion looks for an
      OnRequestEnd.cfm in the same directory as the Application.cfm page ColdFusion uses for the current page.
      ColdFusion does not search beyond that directory, so it does not run an OnRequestEnd.cfm page that resides
      in another directory. Also, the OnRequestEnd.cfm page does not run if there is an error or an exception on the
      application page, or if the application page executes the cfabort or cfexit tag.

The following rules determine how ColdFusion processes application pages and settings:

· ColdFusion processes only one Application.cfc or Application.cfm page for each request. If a ColdFusion page has
   a cfinclude tag pointing to an additional ColdFusion page, ColdFusion does not search for an Application.cfc or
   Application.cfm page when it includes the additional page.

· If a ColdFusion page has a cfapplication tag, it first processes any Application.cfc or Application.cfm, and then
   processes the cfapplication tag. The tag overrides the settings from the application files, including the
   application name and the behaviors set by the cfapplication tag attributes.

· You can have multiple Application.cfc files, Application.cfm files, and cfapplication tags that use the same
   application name. In this case, all pages that have the same name share the same application settings and
   Application scope and set and get all the variables in this scope. ColdFusion uses the parameter settings of the
   cfapplication tag or the most recently processed file, if the settings, such as the session time-out, differ among
   the files.

Note: If your application runs on a UNIX platform, which is case-sensitive, spell Application.cfc, Application.cfm, and
OnRequestEnd.cfm with capital letters.


Defining the directory structure
Defining an application directory structure with an application-specific root directory has the following advantages:

Development The application is easier to develop and maintain, because the application page files are well-organized.

Portability You can easily move the application to another server or another part of a server without changing any
code in the application page files.

Application-level settings Application pages that are under the same directory can share application-level settings and
functions.

Security Application pages that are under the same directory can share web server security settings.

When you place your application in an application-specific directory hierarchy, you can use a single application
definition (Application.cfc or Application.cfm) page in the application root directory, or place different application
definition pages that govern individual sections of the application in different directories.

You divide your logical web application into multiple ColdFusion applications by using multiple application definition
pages with different application names. Alternatively, use multiple application definition pages that specify the same
application name, but have different code, for different subsections of your application.

The directory trees in the following image show two approaches to implementing an application framework:

· In the example on the left, a company named Web Wonders, Inc. uses a single Application.cfc file installed in the
   application root directory to process all application page requests.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      241
Developing CFML Applications




· In the example on the right, Bandwidth Associates uses the settings in individual Application.cfc files to create
   individual ColdFusion applications at the departmental level. Only the Products application pages are processed
   using the settings in the root Application.cfc file. The Consulting, Marketing, and Sales directories each have their
   own Application.cfc file.


                   Web Wonders, Inc.                       Bandwidth Associates



                             Application.cfc                        Application.cfc



                             Products                               Products


                                       Orders                       Consulting


                             Support                                          Application.cfc


                             Services                               Marketing


                                                                              Application.cfc



                                                                    Sales


                                                                              Application.cfc




Defining the application and its event handlers in Application.cfc
The Application.cfc file defines application-wide settings and variables, and application event handlers:

· Application-wide settings and variables include page processing settings, default variables, data sources, style
   settings, and other application-level constants.

· Application event handlers are CFC methods that ColdFusion automatically executes when specific events occur
   during the lifetime of an application: application start and end, session start and end, request start, execution, and
   end, and exceptions.


Defining application-level settings and variables
When you create an application, you can set many application-wide properties and characteristics, including the
following items:

· Application name

· Application properties, including Client-, Application-, and Session-variable management options

· Page processing options

· Default variables, data sources, style settings, and other application-level constants

For information on setting default variables, see "Setting application default variables and constants in
onApplicationStart" on page 245.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     242
Developing CFML Applications




Naming the application
Define the application and give it a name by setting the This.name variable in the Application.cfc initialization section,
before the method definitions. By using a specific application name, you define a set of pages as part of the same logical
application.

ColdFusion supports unnamed applications, which are useful for ColdFusion applications that must interoperate with
JSP tags and servlets. Consider creating an unnamed application only if your ColdFusion pages must share Application
or Session scope data with existing JSP pages and servlets. You cannot have more than one unnamed application on a
server instance. For more information on using unnamed applications, see "Sharing data between ColdFusion pages
and JSP pages or servlets" on page 1130.


Setting application properties
Specify application properties by setting This scope variables in the Application.cfc initialization code. (These are the
same properties that you set in the cfapplication tag.) The following table lists the This scope variable that
ColdFusion uses to set application properties and describes their uses.


Variable                      Default                 Description

applicationTimeout            Administrator value     Life span, as a real number of days, of the application, including all Application
                                                      scope variables. Use the createTimeSpan function to generate this variable.

clientManagement              False                   Whether the application supports Client scope variables.

clientStorage                 Administrator value     Where Client variables are stored; can be cookie, registry, or the name of a
                                                      data source.

loginStorage                  Cookie                  Whether to store login information in the Cookie scope or the Session scope.

scriptProtect                 Administrator Value     Whether to protect variables from cross-site scripting attacks.

sessionManagement             False                   Whether the application supports Session scope variables.

sessionTimeout                Administrator Value     Life span, as a real number of days, of the user session, including all Session
                                                      variables. Use the createTimeSpan function to generate this variable.

setClientCookies              True                    Whether to send CFID and CFTOKEN cookies to the client browser.

setDomainCookies              False                   Whether to use domain cookies for the CFID and CFTOKEN values used for
                                                      client identification, and for Client scope variables stored using cookies. If
                                                      False, ColdFusion uses host-specific cookies. Set to True for applications
                                                      running on clusters.


The following example code from the top of an Application.cfc sets the application name and properties:

<cfcomponent>
<cfset This.name = "TestApplication">
<cfset This.clientmanagement="True">
<cfset This.loginstorage="Session">
<cfset This.sessionmanagement="True">
<cfset This.sessiontimeout="#createtimespan(0,0,10,0)#">
<cfset This.applicationtimeout="#createtimespan(5,0,0,0)#">

For more information on these settings, see cfapplication in the CFML Reference.


Setting page processing options
The cfsetting tag lets you specify the following page processing attributes to apply to all pages in your application:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        243
Developing CFML Applications




 Attribute                          Use

 showDebugOutput                    Specifies whether to show debugging output. This setting cannot enable debugging if it is disabled
                                    in the ColdFusion Administrator. However, this option ensures that debugging output is not
                                    displayed, even if the Administrator enables it.

 requestTimeout                     Specifies the page request time-out. If ColdFusion cannot complete processing a page within the
                                    time-out period, it generates an error. This setting overrides the setting in the ColdFusion
                                    Administrator. Use this setting to increase the page time-out if your application or page frequently
                                    accesses external resources that are slow, such as external LDAP servers or web services providers.

 enableCFOutputOnly                 Disables output of text that is not included inside cfoutput tags. This setting helps ensure that
                                    extraneous text in your ColdFusion pages does not get displayed.


Often, you use the cfsetting tag on individual pages, but you can also use it in your Application.cfc file. For example,
using it in multi-application environment to override the ColdFusion Administrator settings in one application.

You can place an application-wide cfsetting tag in the component initialization code, normally following the This
scope application property settings, as the following example shows:

 <cfcomponent>
<cfscript>
      This.name="MyAppl";
      This.clientmanagement="True";
      This.loginstorage="Session" ;
      This.sessionmanagement="True" ;
      This.sessiontimeout=CreateTimeSpan(0,0,1,0);
</cfscript>
<cfsetting showdebugoutput="No" enablecfoutputonly="No">

The cfsetting tag in this example affects all pages in an application. You can override the application-wide settings
in the event methods, such as onRequestStart, or on individual ColdFusion pages.


Using application event handlers
The following table describes the application event CFC methods that you can implement, including when they are
triggered.


 Method                             When run

 onApplicationStart                 The application first starts: when the first request for a page is processed or the first CFC method is
                                    invoked by an event gateway instance, Flash Remoting request, or a web service invocation.

                                    This method is useful for setting application-wide (Application scope) variables, such as the names
                                    of data sources.

 onApplicationEnd                   The application ends: when the application times out or the server shuts down.

 onSessionStart                     A new session is created as a result of a request that is not in an existing session, including
                                    ColdFusion event gateway sessions. The application must enable sessions for this event to happen.

 onSessionEnd                       A session time-out setting is reached. This event is not triggered when the application ends or the
                                    server shuts down.

 onRequestStart                     ColdFusion receives any of the following: a request, an HTTP request (for example, from a browser),
                                    a message to an event gateway, a SOAP request, or a Flash Remoting request.

 onRequest                          The onRequestStart event has completed. This method acts as a filter for the requested page
                                    content.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  244
Developing CFML Applications




 Method                               When run

 onRequestEnd                         All pages and CFCs in the request have been processed: equivalent to the OnRequestEnd.cfm page.

 onMissingTemplate                    When ColdFusion receives a request for a nonexistent page.

 onError                              When an exception occurs that is not caught by a try/catch block.


When ColdFusion receives a request, it instantiates the Application CFC and runs the Application.cfc code in the
following order:

· CFC initialization code at the top of the file

· onApplicationStart, if not run before for this application

· onSessionStart, if not run before for this session

· onRequestStart

· onRequest, or the requested page if there is no onRequest method

· onRequestEnd

The following methods are triggered by specific events:

· onApplicationEnd

· onSessionEnd

· onMissingTemplate

· onError

The onApplicationEnd and onSessionEnd methods do not execute in the context of a page request, so they cannot
access request variables or display information to the user. The onMissingTemplate method is triggered when a URL
specifies a CFML page that does not exist. The OnError method does not always execute in the context of a request;
use its Event argument to determine the context.


Managing the application with Application.cfc
Use the onApplicationStart and onApplicationEnd methods to configure and manage the application; that is, to
control resources that are used by multiple pages and requests and must be consistently available to all code in your
application. Such resources include data sources, application counters such as page hit variables, or style information
for all pages.

The onApplicationStart method executes when ColdFusion gets the first request for a page in the application after
the server starts. The onApplicationEnd method executes when the application server shuts down or if the
application is inactive for the application time-out period.

The following are some of the ways you use these methods. For more information, see entries for onApplicationStart
and onApplicationEnd in the CFML Reference.


Defining application utility functions
Functions that you define in Application.cfc and do not place in a shared scope are, by default, available only to other
methods in the CFC.

If your Application.cfc implements the onRequest method, any utility functions that you define in Application.cfc are
also directly available in to the target page, because Application.cfc and the target page share the Variables scope.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        245
Developing CFML Applications




If your application requires utility functions that are used by multiple pages, not just by the methods in Application.cfc,
and you do not use an onRequest method, Adobe recommends that you place them in a separate CFC and access them
by running that CFC. As with other ColdFusion pages, Application.cfc can access any CFC in a directory path that is
configured on the ColdFusion Administrator Mappings page. Therefore, use this technique to share utility functions
across applications.

If your Application.cfc defines utility functions that you want available on request pages and does not use an
onRequest method, explicitly place the functions in a ColdFusion scope, such as the Request scope, as the following
code shows:

 <cffunction name="theFunctionName" returntype="theReturnType">
     <!--- Function definition goes here. --->
</cffunction>


<cffunction name="OnRequestStart">
     <!--- OnRequestStart body goes here --->
          <cfset Request.theFunctionName=This.theFunctionName>
</cffunction>

On the request page, you would include the following code:

 <cfset myVar=Request.theFunctionName(Argument1...)>

Functions that you define in this manner share the This scope and Variables scope with the Application.cfc file for the
request.


Setting application default variables and constants in onApplicationStart
You can set default variables and application-level constants in Application.cfc. For example, you can do the following:

· Specify a data source and ensure that it is available

· Specify domain name

· Set styles, such as fonts or colors

· Set other application-level variables

You do not have to lock Application scope variables when you set them in the Application.cfc onApplicationStart
method.

For details on implementing the onApplicationStart method, see onApplicationStart in the CFML Reference.


Using the onApplicationEnd method
Use the onApplicationEnd method for any clean-up activities that your application requires when it shuts down,
such as saving data in memory to a database, or to log the application end. You cannot use this method to display data
on a user page, because it is not associated with any request. The application ends, even if this method throws an
exception. An application that is used often is unlikely to execute this method, except when the server is shut down.

For details on implementing the onApplicationEnd method, see onApplicationEnd in the CFML Reference.


Managing sessions in Application.cfc
Use the onSessionStart and onSessionEnd methods to configure and manage user sessions; that is, to control
resources that are used by multiple pages while a user is accessing your site from during a single browser session. The
session begins when a user first requests a page in your application, and ends when the session times out. For more
information on Session scope and Session variables, see "Using Persistent Data and Locking" on page 301.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      246
Developing CFML Applications




Session resources include variables that store data that is needed throughout the session, such as account numbers,
shopping cart contents, or CFCs that contain methods and data that are used by multiple pages in a session.

Note: Do not include the cflogin tag or basic login processing in the onSessionStart method, as the code executes only
at the start of the session; it cannot handle user logout, and cannot fully ensure security.


Using the onSessionStart method
This method is useful for initializing session data, such as user settings or shopping cart contents, or for tracking the
number of active sessions. You do not need to lock the Session scope when you set session variables in this method.

For more information, see the onSessionStart entry in the CFML Reference.


Using the onSessionEnd method
Use this method for any clean-up activities when the session ends. (For information on ending sessions, see "Ending
a session" on page 315.) For example, you can save session-related data, such as shopping cart contents or information
about whether the user has not completed an order, in a database, or you can log the end of the session to a file. You
cannot use this method to display data on a user page, because it is not associated with a request.

Note: Sessions do not end, and the onSessionEnd method is not called when an application ends. For more information,
see the onSessionEnd entry in the CFML Reference.


Managing requests in Application.cfc
ColdFusion provides three methods for managing requests: onRequestStart, onRequest, and onRequestEnd.
ColdFusion processes requests, including these methods, as follows:

1 ColdFusion always processes onRequestStart at the start of the request.

2 If you implement an onRequest method, ColdFusion processes it; otherwise, it processes the requested page. If you
   implement an onRequest method, explicitly call the requested page in your onRequest method.

3 ColdFusion always processes onRequestEnd at the end of the request.

You can use each of the Application.cfc request methods to manage requests as follows:


Using the onRequestStart method
This method runs at the beginning of the request. It is useful for user authorization (login handling), and for request-
specific variable initialization, such as gathering performance statistics.

If you use the onRequestStart method and do not use the onRequest method, ColdFusion automatically processes
the request when it finishes processing the onRequestStart code.

Note: If you do not include an onRequest method in Application.cfm file, the onRequestStart method does not share
a Variables scope with the requested page, but it does share Request scope variables.

For more information, see the entry for onRequestStart in the CFML Reference


User authentication
When an application requires a user to log in, include the authentication code, including the cflogin tag or code that
calls this tag, in the onRequestStart method. Doing so ensures that the user is authenticated at the start of each
request. For detailed information on security and creating logins, see "Securing Applications" on page 339 For an
example that uses authentication code generated by the Adobe Dreamweaver CF Login Wizard, see onRequestStart in
the CFML Reference.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  247
Developing CFML Applications




Using the onRequest method
The onRequest method differs from the onRequestStart method in one major way: the onRequest method
intercepts the user's request. This difference has two implications:

· ColdFusion does not process the request unless you explicitly call it, for example, by using a cfinclude tag. This
   behavior lets you use the onRequest method to filter requested page content or to implement a switch that
   determines the pages or page contents to be displayed.

· When you use cfinclude to process request, the CFC instance shares the Variables scope with the requested page.
   As a result, any method in the CFC that executes can set the page's Variables scope variables, and the
   onRequestEnd method can access any Variable scope values that the included page has set or changed. Therefore,
   for example, the onRequestStart or onRequest method set variables that are used on the page.

To use this method as a filter, place the cfinclude tag inside a cfsavecontent tag, as the following example shows:

 <cffunction name="onRequest">
     <cfargument name = "targetPage" type="String" required=true/>
     <cfsavecontent variable="content">
          <cfinclude template=#Arguments.targetPage#>
     </cfsavecontent>
     <cfoutput>
          #replace(content, "report", "MyCompany Quarterly Report", "all")#
     </cfoutput>
</cffunction>

For more information, see the entry for onRequest in the CFML Reference


Using the onRequestEnd method
You use the onRequestEnd method for code that should run at the end of each request. (In ColdFusion versions
through ColdFusion MX 6.1, you would use the OnRequestEnd.cfm page for such code). Typical uses include
displaying dynamic footer pages. For an example, see onSessionEnd in the CFML Reference.

Note: If you do not include an onRequest method in Application.cfm file, the onRequestEnd method does not share a
Variables scope with the requested page, but it does share Request scope variables.

For more information, see the entry for onRequestEnd in the CFML Reference.


Handling errors in Application.cfc
The following sections briefly describe how you to handle errors in Application.cfc. For more information on error
pages and error handling, see "Handling Errors" on page 275 For details on implementing the onError method, see
onError in the CFML Reference.


Application.cfc error handling techniques
Application.cfc handles errors in any combination of the following ways:

· Use try/catch error handling in the event methods, such as onApplicationStart or onRequestStart, to handle
   exceptions that happen in the event methods.

· Implement the onError method. This method receives all exceptions that are not directly handled by try/catch
   handlers in CFML code. The method can use the cfthrow tag to pass any errors it does not handle to ColdFusion
   for handling.

· Use cferror tags in the application initialization code following the cfcomponent tag, typically following the code
   that sets the application's This scope variables. These tags specify error processing if you do not implement an
   onError method, or if the onError method throws an error. You could implement an application-specific
   validation error handler, for example, by placing the following tag in the CFC initialization code:



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   248
Developing CFML Applications




    <cferror type="VALIDATION" template="validationerrorhandler.cfm">

· The ColdFusion default error mechanisms handle any errors that are not handled by the preceding techniques.
   These mechanisms include the site-wide error handler that you specify in the ColdFusion Administrator and the
   built-in default error pages.

These techniques let you include application-specific information, such as contact information or application or
version identifiers, in the error message, and let you display all error messages in the application in a consistent
manner. You use Application.cfc to develop sophisticated application-wide error-handling techniques, including
error-handling methods that provide specific messages, or use structured error-handling techniques.

Note: The onError method catches errors that occur in the onSessionEnd and onApplicationEnd application event
methods. It does not display messages to the user, however, because there is no request context. The onError function
logs errors that occur when the session or application ends.


Handling server-side validation errors in the onError method
Server-side validation errors are actually ColdFusion exceptions; as a result, if your application uses an onError
method, this method gets the error and must handle it or pass it on to ColdFusion for handling.

To identify a server-side validation error, search the Arguments.Exception.StackTrace field for
coldfusion.filter.FormValidationException. You can then handle the error directly in your onError routine, or throw
the error so that either the ColdFusion default validation error page or a page specified by an cferror tag in your
Application.cfc initialization code handles it.


Example: error Handling with the onError method
The following Application.cfc file has an onError method that handles errors as follows:

· If the error is a server-side validation error, the onError method throws the error for handling by ColdFusion,
   which displays its standard validation error message.

· For any other type of exception, the onError method displays the name of the event method in which the error
   occurred and dumps the exception information. In this example, because you generate errors on the CFM page
   only, and not in a Application.cfc method, the event name is always the empty string.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        249
Developing CFML Applications




<cfcomponent>
<cfset This.name = "BugTestApplication">
<cffunction name="onError">
     <!--- The onError method gets two arguments:
                An exception structure, which is identical to a cfcatch variable.
                The name of the Application.cfc method, if any, in which the error
                happened.
     <cfargument name="Except" required=true/>
     <cfargument type="String" name = "EventName" required=true/>
     <!--- Log all errors in an application-specific log file. --->
     <cflog file="#This.Name#" type="error" text="Event Name: #Eventname#" >
     <cflog file="#This.Name#" type="error" text="Message: #except.message#">
     <!--- Throw validation errors to ColdFusion for handling. --->
     <cfif Find("coldfusion.filter.FormValidationException",
                           Arguments.Except.StackTrace)>
          <cfthrow object="#except#">
     <cfelse>
          <!--- You can replace this cfoutput tag with application-specific
                    error-handling code. --->
          <cfoutput>
                <p>Error Event: #EventName#</p>
                <p>Error details:<br>
                <cfdump var=#except#></p>
          </cfoutput>
     </cfif>
</cffunction>
</cfcomponent>

To test this example, place a CFML page with the following code in the same page as the Application.cfc file, and enter
valid and invalid text in the text input field.

<cfform>
     This box does Integer validation:
     <cfinput name="intinput" type="Text" validate="integer" validateat="onServer"><br>
     Check this box to throw an error on the action page:
     <cfinput type="Checkbox" name="throwerror"><br>
     <cfinput type="submit" name="submitit">
     </cfform>
<cfif IsDefined("form.fieldnames")>
     <cfif IsDefined("form.throwerror")>
          <cfthrow type="ThrownError" message="This error was thrown from the bugTest action
page.">
     <cfelseif form.intinput NEQ "">
          <h3>You entered the following valid data in the field</h3>
          <cfoutput>#form.intinput#</cfoutput>
     </cfif>
</cfif>

Note: For more information on server-side validation errors, see "Validating Data" on page 743.


Example: a complete Application.cfc
The following example is a simplified Application.cfc file that illustrates the basic use of all application event handlers:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                           250
Developing CFML Applications




<cfcomponent>
<cfset This.name = "TestApplication">
<cfset This.Sessionmanagement=true>
<cfset This.Sessiontimeout="#createtimespan(0,0,10,0)#">
<cfset This.applicationtimeout="#createtimespan(5,0,0,0)#">


<cffunction name="onApplicationStart">
    <cftry>
         <!--- Test whether the DB that this application uses is accessible
                   by selecting some data. --->
         <cfquery name="testDB" dataSource="cfdocexamples" maxrows="2">
              SELECT Emp_ID FROM employee
         </cfquery>
         <!--- If we get database error, report it to the user, log the error
              information, and do not start the application. --->
         <cfcatch type="database">
              <cfoutput>
                   This application encountered an error.<br>
                   Please contact support.
              </cfoutput>
              <cflog file="#This.Name#" type="error"
                   text="cfdocexamples DB not available. message: #cfcatch.message#
                   Detail: #cfcatch.detail# Native Error: #cfcatch.NativeErrorCode#">
              <cfreturn False>
         </cfcatch>
    </cftry>
    <cflog file="#This.Name#" type="Information" text="Application Started">
    <!--- You do not have to lock code in the onApplicationStart method that sets Application
scope variables. --->
    <cfscript>
         Application.availableResources=0;
         Application.counter1=1;
         Application.sessions=0;
    </cfscript>
    <!--- You do not need to return True if you don't set the cffunction returntype attribute.
--->
    </cffunction>


<cffunction name="onApplicationEnd">
    <cfargument name="ApplicationScope" required=true/>
    <cflog file="#This.Name#" type="Information"
         text="Application #ApplicationScope.applicationname# Ended">
</cffunction>



<cffunction name="onRequestStart">
    <!--- Authentication code, generated by the Dreamweaver Login Wizard,
         makes sure that a user is logged in, and if not displays a login page. --->
    <cfinclude template="mm_wizard_application_include.cfm">
<!--- If it's time for maintenance, tell users to come back later. --->
    <cfscript>
         if ((Hour(now()) gt 1) and (Hour(now()) lt 3)) {
              WriteOutput("The system is undergoing periodic maintenance.
                   Please return after 3:00 AM Eastern time.");
              return false;
         } else {
              this.start=now();



                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                               251
Developing CFML Applications




         }
    </cfscript>
</cffunction>


<cffunction name="onRequest">
    <cfargument name = "targetPage" type="String" required=true/>
    <cfsavecontent variable="content">
         <cfinclude template=#Arguments.targetPage#>
    </cfsavecontent>
    <!--- This is a minimal example of an onRequest filter. --->
    <cfoutput>
         #replace(content, "report", "MyCompany Quarterly Report", "all")#
    </cfoutput>
</cffunction>


<!--- Display a different footer for logged in users than for guest users or
           users who have not logged in. --->


<cffunction name="onRequestEnd">
    <cfargument type="String" name = "targetTemplate" required=true/>
    <cfset theAuthuser=getauthuser()>
    <cfif ((theAuthUser EQ "guest") OR (theAuthUser EQ ""))>
         <cfinclude template="noauthuserfooter.cfm">
    <cfelse>
         <cfinclude template="authuserfooter.cfm">
    </cfif>
</cffunction>


<cffunction name="onSessionStart">
    <cfscript>
         Session.started = now();
         Session.shoppingCart = StructNew();
         Session.shoppingCart.items =0;
    </cfscript>
    <cflock timeout="5" throwontimeout="No" type="EXCLUSIVE" scope="SESSION">
         <cfset Application.sessions = Application.sessions + 1>
    </cflock>
    <cflog file="#This.Name#" type="Information" text="Session:
         #Session.sessionid# started">
</cffunction>


<cffunction name="onSessionEnd">
    <cfargument name = "SessionScope" required=true/>
    <cflog file="#This.Name#" type="Information" text="Session:
              #arguments.SessionScope.sessionid# ended">
</cffunction>


<cffunction name="onError">
    <cfargument name="Exception" required=true/>
    <cfargument type="String" name = "EventName" required=true/>
    <!--- Log all errors. --->
    <cflog file="#This.Name#" type="error" text="Event Name: #Eventname#">
    <cflog file="#This.Name#" type="error" text="Message: #exception.message#">




                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     252
Developing CFML Applications




     <!--- Some exceptions, including server-side validation errors, do not
                generate a rootcause structure. --->
     <cfif isdefined("exception.rootcause")>
          <cflog file="#This.Name#" type="error"
               text="Root Cause Message: #exception.rootcause.message#">
     </cfif>
     <!--- Display an error message if there is a page context. --->
     <cfif NOT (Arguments.EventName IS onSessionEnd) OR
               (Arguments.EventName IS onApplicationEnd)>
          <cfoutput>
               <h2>An unexpected error occurred.</h2>
               <p>Please provide the following information to technical support:</p>
               <p>Error Event: #EventName#</p>
               <p>Error details:<br>
               <cfdump var=#exception#></p>
          </cfoutput>
     </cfif>
     </cffunction>


</cfcomponent>



Migrating from Application.cfm to Application.cfc
To migrate an existing application that uses Application.cfm to one that uses Application.cfc, do the following:

· Replace the cfapplication tag with CFC initialization code that sets the Application.cfc This scope variables that
   correspond to the tag attributes.

· Place in the onApplicationStart method, any code that initializes Application scope variables, and any other
   application-specific code that executes only when the application starts. Often, such code in Application.cfm is
   inside a block that tests for the existence of an Application scope switch variable. Remove the variable test and the
   Application scope lock that surrounds the code that sets the Application scope variables.

· Place in the onSessionStart method, any code that initializes Session scope variables, and any other application-
   specific code that executes only when the session starts. Remove any code that tests for the existence of Session
   scope variables to be for initialized and the Session scope lock that surrounds the code that sets the Session scope
   variables.

· Place in the onRequestStart method, any cflogin tag and related authentication code.

· Place in the onRequest method, any code that sets Variables scope variables and add a cfinclude tag that includes
   the page specified by the Arguments of the method.Targetpage variable.

· Place in the onRequestEnd method, any code you have in an OnRequestEnd.cfm page.

· Consider replacing cferror tags with an onError event method. If you do not do so, place the cferror tags in the
   CFC initialization code.


Using an Application.cfm page
If you do not use an Application.cfc file, use the Application.cfm page to define application-level settings and
functions.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   253
Developing CFML Applications




Naming the application
Use the cfapplication tag to specify the application name and define a set of pages as part of the same logical
application. Although you can create an application by placing a cfapplication tag with the application name on
each page, you normally place the tag in the Application.cfm file; for example:

<cfapplication name="SearchApp">

Note: The value that you set for the name attribute in the cfapplication tag is limited to 64 characters.


Setting the client, application, and session variables options
Use the cfapplication tag to specify client state and persistent variable use, as follows:

· To use Client scope variables, specify clientManagement=True.

· To use Session scope variables, specify sessionManagment=True.

You can also optionally do the following:

· Set application-specific time-outs for Application and Session scope variables. These settings override the default
   values set in the ColdFusion Administrator.

· Specify a storage method for Client scope variables. This setting overrides the method set in the ColdFusion
   Administrator.

· Specify not to use cookies on the client browser.

For more information on configuring these options, see "Using Persistent Data and Locking" on page 301 and the
CFML Reference.


Defining page processing settings
The cfsetting tag lets you specify page processing attributes that you want to apply to all pages in your application.
For more information, see "Setting page processing options" on page 242.


Setting application default variables and constants
Set default variables and application-level constants on the Application.cfm page. For example, specify the following
values:

· A data source

· A domain name

· Style settings, such as fonts or colors

· Other important application-level variables

Often, an Application.cfm page uses one or more cfinclude tags to include libraries of commonly used code, such as
user-defined functions, that are required on many of the application's pages.


Processing logins
When an application requires a user to log in, you typically place the cflogin tag on the Application.cfm page. For
detailed information on security and creating logins, including an Application.cfm page that manages user logins, see
"Securing Applications" on page 339




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     254
Developing CFML Applications




Handling errors
Use the cferror tag on your Application.cfm page to specify application-specific error-handling pages for request,
validation, or exception errors, as shown in the following example. This way you include application-specific
information, such as contact information or application or version identifiers, in the error message, and you display
all error messages in the application in a consistent manner.

For more information on error pages and error handling, see "Handling Errors" on page 275.


Example: an Application.cfm page
The following example shows a sample Application.cfm file that uses several of the techniques typically used in
Application.cfm pages. For the sake of simplicity, it does not show login processing; for a login example, see "Securing
Applications" on page 339.

 <!--- Set application name and enable Client and Session variables. --->
<cfapplication name="Products"
      clientmanagement="Yes"
      clientstorage="myCompany"
      sessionmanagement="Yes">


<!--- Set page processing attributes. --->
<cfsetting showDebugOutput="No">


<!--- Set custom global error handling pages for this application.--->
<cferror type="request"
      template="requesterr.cfm"
      mailto="admin@company.com">
<cferror type="validation"
      template="validationerr.cfm">


<!--- Set the Application variables if they aren't defined. --->
<!--- Initialize local app_is_initialized flag to false. --->
<cfset app_is_initialized = False>
<!--- Get a read-only lock. --->
<cflock scope="application" type="readonly" timeout=10>
<!--- Read init flag and store it in local variable. --->
      <cfset app_is_initialized = IsDefined("Application.initialized")>
</cflock>
<!--- Check the local flag. --->
<cfif not app_is_initialized>
<!--- Application variables are not initialized yet.
          Get an exclusive lock to write scope. --->
      <cflock scope="application" type="exclusive" timeout=10>
          <!--- Check the Application scope initialized flag since another request
               could have set the variables after this page released the read-only
               lock. --->
          <cfif not IsDefined("Application.initialized")>
               <!--- Do initializations --->
               <cfset Application.ReadOnlyData.Company = "MyCompany">
               <!--- and so on --->
               <!--- Set the Application scope initialization flag. --->
               <cfset Application.initialized = "yes">




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               255
Developing CFML Applications




         </cfif>
    </cflock>
</cfif>


<!--- Set a Session variable.--->
<cflock timeout="20" scope="Session" type="exclusive">
    <cfif not IsDefined("session.pagesHit")>
         <cfset session.pagesHit=1>
    <cfelse>
         <cfset session.pagesHit=session.pagesHit+1>
    </cfif>
</cflock>


<!--- Set Application-specific Variables scope variables. --->
<cfset mainpage = "default.cfm">
<cfset current_page = "#cgi.path_info#?#cgi.query_string#">


<!--- Include a file containing user-defined functions called throughout
         the application. --->
<cfinclude template="commonfiles/productudfs.cfm">


Reviewing the code
The following table describes the code and its function:


Code                                                     Description

<cfapplication name="Products"                           Names the application, enables Client and Session scope variables,
clientmanagement="Yes"                                   and sets the client variable store to the myCompany data source.
clientstorage="myCompany"
sessionmanagement="Yes">

<cfsetting showDebugOutput="No">                         Ensures that debugging output is not displayed, if the ColdFusion
                                                         Administrator enables it.

<cferror type="request"                                  Specifies custom error handlers for request and validation errors
template="requesterr.cfm"                                encountered in the application. Specifies the mailing address for use in
mailto="admin@company.com">
                                                         the request error handler.
<cferror type="validation"
template="validationerr.cfm">

<cfset app_is_initialized = False>                       Sets the Application scope variables, if they are not already set. For a
                                                         detailed description of the technique used to set the Application
                                                         scope variables, see "Using Persistent Data and Locking" on page 301.

<cflock timeout="20" scope="Session"                     Sets the Session scope pagesHit variable, which counts the number
type="exclusive">                                        of pages touched in this session. If the variable does not exist, creates
<cfif not IsDefined("session.pagesHit")>
                                                         it; otherwise, increments it.
<cfset session.pagesHit=1>
<cfelse>
<cfset session.pagesHit=session.pagesHit+1>
</cfif>
</cflock>

<cfset mainpage = "default.cfm">                         Sets two Variables scope variables that are used throughout the
<cfset current_page =                                    application. Creates the current_page variable dynamically; its value
"#cgi.path_info#?#cgi.query_string#">
                                                         varies from request to request.

<cfinclude template="commonfiles/productudfs.cfm">       Includes a library of user-defined functions that are used in most pages
                                                         in the application.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         256
Developing CFML Applications




Optimizing ColdFusion applications
You can optimize your ColdFusion application in many ways. Optimizing ColdFusion mostly involves good
development and coding practices. For example, good database design and usage is a prime contributor to efficient
ColdFusion applications.

In many other topics, the optimization techniques provided are pertinent to the related ColdFusion topic. The
following information is about general ColdFusion optimization tools and strategies, and particularly about using
CFML caching tags for optimization. There is also information on optimizing database use, an important area for
application optimization.

The ColdFusion Administrator provides caching options for ColdFusion pages and SQL queries. For information on
these options, see the ColdFusion Administrator online Help and Configuring and Administering ColdFusion.

For information on debugging techniques that help you identify slow pages, see "Debugging and Troubleshooting
Applications" on page 378.

For additional information on optimizing ColdFusion, see the Adobe ColdFusion support center at
www.adobe.com/go/learn_cfu_support_en.


Caching ColdFusion pages that change infrequently
Some ColdFusion pages produce output that changes infrequently. For example, if you have an application that
extracts a vendor list from a database or produces a quarterly results summary. Normally, when ColdFusion gets a
request for a page in the application, it does all the business logic and display processing that are required to produce
the report or generate and display the list. If the results change infrequently, it is an inefficient use of processor
resources and bandwidth.

The cfcache tag tells ColdFusion to cache the HTML that results from processing a page request in a temporary file
on the server. This HTML need not be generated each time the page is requested. When ColdFusion gets a request for
a cached ColdFusion page, it retrieves the pregenerated HTML page without having to process the ColdFusion page.
ColdFusion can also cache the page on the client. If the client browser has its own cached copy of the page from a
previous viewing, ColdFusion instructs the browser to use the client's page rather than resending the page.

Note: The cfcache tag caching mechanism considers that each URL is a separate page. Therefore,
http://www.mySite.com/view.cfm?id=1 and http://www.mySite.com/view.cfm?id=2 result in two separate cached pages.
Because ColdFusion caches a separate page for each unique set of URL parameters, the caching mechanism
accommodates pages for which different parameters result in different output.


Using the cfcache tag
You tell ColdFusion to cache the page results by placing a cfcache tag on your ColdFusion page before code that
outputs text. The tag lets you specify the following information:

· Whether to cache the page results on the server, the client system, or both. The default is both. The default is
   optimal for pages that are identical for all users. If the pages contain client-specific information, or are secured with
   ColdFusion user security, set the action attribute in the cfcache tag to ClientCache.

· The directory on the server in which to store the cached pages. The default directory is cf_root/cache. It is a good
   practice to create a separate cache directory for each application. Doing so prevents the cfcache tag flush action
   from inappropriately flushing more than one application's caches at a time.

· The time span that indicates how long the page lasts in the cache from when it is stored until it is automatically
   flushed.

You can also specify several attributes for accessing a cached page on the web server, including a user name and
password (if required by the web server), the port, and the protocol (HTTP or HTTPS) to use to access the page.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        257
Developing CFML Applications




Place the cfcache tag before any code on your page that generates output, typically at the top of the page body. For
example, the following tag tells ColdFusion to cache the page on both the client and the server. On the server, the page
is cached in the e:/temp/page_cache directory. ColdFusion retains the cached page for one day.

 <cfcache timespan="#CreateTimespan(1, 0, 0, 0)#" directory="e:/temp/page_cache">

Important: If an Application.cfm or Application.cfc page displays text (for example, if it includes a header page), use the
cfcachetag on the Application.cfm page, in addition to the pages that you cache. Otherwise, ColdFusion displays the
Application.cfm page output twice on each cached page.


Flushing cached pages
ColdFusion automatically flushes any cached page if you change the code on the page. It also automatically flushes
pages after the expiration time span passes.

Use the cfcache tag with the action="flush" attribute to immediately flush one or more cached pages. You can
optionally specify the directory that contains the cached pages to be flushed and a URL pattern that identifies the pages
to flush. If you do not specify a URL pattern, all pages in the directory are flushed. The URL pattern can include asterisk
(*) wildcards to specify parts of the URL that can vary.

When you use the cfcache tag to flush cached pages, ColdFusion deletes the pages cached on the server. If a flushed
page is cached on the client system, it is deleted, and a new copy gets cached the next time the client tries to access the
ColdFusion page.

The following example flushes all the pages in the e:/temp/page_cache/monthly directory that start with HR:

 <cfcache action="flush" directory="e:/temp/page_cache/monthly" expirURL="HR*">

If you have a ColdFusion page that updates data that you use in cached pages, the page that does the updating includes
a cfcache tag that flushes all pages that use the data.

For more information on the cfcache tag, see the CFML Reference.


Caching parts of ColdFusion pages
In some cases, your ColdFusion page contains a combination of dynamic information that ColdFusion must generate
each time it displays the page, and information that it generates dynamically, but that changes less frequently.
ColdFusion 9 provides granular control over caching. You can cache fragments of page that lets you cache the
infrequently changed content.The following example illustrates fragment caching:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           258
Developing CFML Applications




<!--- Greet the us er. --->
<cf output>
      W elcome to our home page.<br>
      The time is #TimeFormat(Now())#.<br>
      Your lucky number is: #RandRa nge(1,1000)#<br>
      <hr>
</cfoutput>
<!--- If the flag is false, query the DB, and save an image of
      the results output to a variable. ---> <cfcache action="optimal"
timespan="#createtimespan(0,1,0,0)#" idletime="#createtimespan(0,0,30,0)#"><!--- Perform
database query. ---><cfquery dataSource="cfartgallery" name="specialQuery">                                 SELECT * from
art
</cfquery>
<!--- Calculate sale price and display the results. --->
            <h2>Check out the following specials</h2>
            <table>
<cfoutput query="specialQuery">
                 <tr>
                      <td>#artid#</td>
                      <td>#Description#</td>
                      <td>#price#</td>
           </tr>
</cfoutput>
            </table></cfcache><hr><p>Thank you for visiting us!</p>

In this example, the highlighted code creates a page fragment that displays all art records from the table art. The cache
is set to expire either after one hour or after an idle time of 30 minutes. After the cache is invalidated, cache is recreated
the next time the page is accessed thereby displaying updated information (if any).


Using Amazon S3 storage
Note: To use this feature, you must install ColdFusion 9 Update 1

.ColdFusion customers can now store data in Amazon S3. The support is extended across almost all tags and functions
that take file or directory as input or output.

Storing files in Amazon S3 can be performed in the same manner as storing files on disk. Use a prefix s3:// to indicate
that the files reside on Amazon S3. For example, s3://testbucket/sample.txt.


Amazon S3
For using Amazon S3, ColdFusion user must have an S3 account with Amazon.

For concepts and details related to Amazon S3, see the AmazonS3 Documentation.


Accessing Amazon S3
Use either of the following URL formats to access Amazon S3 from ColdFusion:

· s3://bucket/x/y/sample.txt

    Here, bucket is the name of the bucket and the remaining portion of the URL is the key name of the Amazon S3
    object.

    In this case, specify the following authentication information in the Application.cfc:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                             259
Developing CFML Applications




  <cfscript>
             this.name ="Object Operations";
             this.s3.accessKeyId = "key_ID";
             this.s3.awsSecretKey = "secret_key";
             this.s3.defaultLocation="location";
             </cfscript>

  For example,

  <cffile action="write" output="S3 Specification" file="s3://testbucket/sample.txt"/>

· s3://accessKeyId:awsSecretKey@bucket/x/y/sample.txt

  · This format has the accessKeyID and awsSecretKey specified in it.

  · @ acts as the token to indicate the end of authentication information.

  Note: If you have specified the accessKeyID and awsSecretKey in both the URL and Application.cfc, then value
  specified in the URL takes precedence.


Example
<cffile action="write" output="S3 Specifications"
file="s3://accessKeyID:awsSecretKey@bucket/x/y/sample.txt"/>


Supported operations
The following are the supported operations on Amazon S3:

· Create and delete bucket

· Get bucket location

· Read, write, copy, and delete object

· List keys in bucket

· Get and set metadata for object or bucket

· Get and set ACL for object or bucket


Bucket operations
Use the cfdirectory tag or the directory functions to perform the bucket operation (create, delete, or list).




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                       260
Developing CFML Applications




 Operation     Tag used                        Function   Example

 Create        cfdirectory                     DirectoryC <cfdirectory action="create" directory="s3://bucket1"/>
               action="create"                 reate

               The directory attribute can
               only take the path of the
               bucket. Any additional path
               results in an error.

               All other attributes are
               ignored. While creating S3
               bucket, the default bucket
               location is US. You can
               change the location using the
               attribute storeLocation.

               storeLocation is a new
               attribute added to the
               cfdirectory tag.

               You can specify ACL for the
               bucket while creating it using
               the storeACL attribute
               which takes a struct value. For
               details, see "Setting up access
               control" on page 261.

 List keys     cfdirectory                     DirectoryL <cfdirectory action="list" directory="s3://bucket1/X/y"
               action="list"                   ist        />

                                                          Since Amazon S3 does not have the concept of directory, it returns the key
                                                          name (that is, the full path) of objects contained in the bucket.

                                                          Directoryattributeinthiscasetakesthepath,forexample,s3://bucket1
                                                          in which objects have to be searched. The path that follows the bucket name
                                                          is used as a prefix to perform the list operation and all the objects that match
                                                          the prefix are returned.

                                                          In this case, the following attributes are ignored: recurse, type, and sort.

 Delete        cfdirectory                     DirectoryD <cfdirectory action="delete" directory="s3://bucket1"/>
               action="delete"                 elete


Note: To verify if the bucket exists and is accessible, use the function directoryExists.


Object operations
All object operations are similar to file operations (read, write, copy, and delete). Therefore, the tag cffile and the
file functions can be used to perform the operations. The following table describes the common scenarios:


 Operation     Tag used                      Function     Example

 Read          cffile                        FileRead     <cffile action="read" file="s3://testbucket/test.txt"
               action="read"                              variable="data"/>


 Write         cffile                        FileWrite    <cffile action="write" output="#data#"
               action="write"                             file="s3://testbucket/test.txt"/>


 Delete        cffile                        FileDelete   <cffile action="delete" file="s3://testbucket/test.txt"/>
               action="delete"

 Copy          cffile                        FileCopy     <cffile action="copy" source="s3://testbucket/test.txt"
               action="copy"                              destination="s3://bucket2/a/b.txt"/>




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                261
Developing CFML Applications




The following are the supported functions:

· FileIsEOF

· FileReadBinary

· Filecopy

· FileReadLine

· FileExists

· FileWriteln

· FileOpen

· FileClose

· FileRead

· FileDelete


New attributes in cfdirectory action="create" tag

 Attribute Added Description                                                    Example

 storeLocation Used to change the location of the created bucket. The           <cfdirectory action="create"
                 location can either be EU or US. The default location is US.   directory="s3://<bucketname>"
                                                                                storelocation="US">
                                                                                <cfdirectory action="create"
                                                                                directory="s3://<bucketname>"
                                                                                storelocation="EU">


 storeACL        An array of struct where each struct represents a permission or<cfdirectory action="create"
                 grant as discussed in "ACLObject" on page 262.                 directory="s3://<bucketname>"
                                                                                storeACL="ACLObject">



Setting up access control
Amazon S3 lets you set access control list (ACL) for buckets and objects. The ACLs for buckets and objects are
independent. You have to manage them separately. Also, object ACLs do not inherit from the bucket ACLs.

ACL consists of multiple Grants where each grant has a grantee and a permission. S3 allows three types of grantees:

· group

· email

· canonical (ID)

The following are the possible permissions:

· read

· write

· read_acp

· write_acp

· full_control

See Amazon S3 ACL Documentation for more details.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 262
Developing CFML Applications




ACLObject
ACLObject is an array of struct where each struct represents an ACL grant. The grantee details are as follows:

   group Must have the keys Group (with value all, authenticated, or log_delivery) and permission.

   email Must have the keys email and permission.

   canonical Must have the keys Id and permission. displayName is optional.


Sample ACLObject
all_read = {group="all", permission="read"};
   owner_full = {email="xxx@yyy.com", permission="full_control"};
   aclObj = [owner_full, all_read];



Access control functions


storeSetACL

Description
Sets the ACL for object or bucket.


Returns
Nothing


Syntax

StoreSetACL(url, ACLObject)


Parameters

Parameter          Description

url                Amazon S3 URLs (content or object)

ACLObject          An array of struct where each struct represents a permission or grant as discussed in "ACLObject" on page 262.



History
ColdFusion 9 Update 1: Added this function


Usage
Use this function to set full permission. The function overwrites all existing permissions. Only the ones you set in the
current context exist.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                263
Developing CFML Applications




Example
<cftry>
    <cfset dir = "s3://bucket_name">
    <cfif !directoryExists(dir)>
          <cfset directorycreate(dir)>
    </cfif>


    <cfset perm = structnew()>
    <cfset perm.group = "all">
    <cfset perm.permission = "read">
    <cfset perm1 = structnew()>
    <cfset perm1.email = "email ID">
    <cfset perm1.permission = "FULL_CONTROL">
    <cfset myarrray = arrayNew(1)>
    <cfset myarrray = [perm,perm1]>
    <cfset fileWrite("#dir#/test.txt","This is to test all users permission")>


    <cfset StoreSetACL("#dir#/textl.txt","#myarrray#")>
    <cfset test = StoreGetACL ("#dirkey#/test.txt") >


    <cfdump var= "test">


    <cfcatch>
          <cfdump var="#cfcatch#">
    </cfcatch>
</cftry>



storeAddACL

Description
Adds ACL to existing ACL for object or bucket.


Returns
Nothing


Syntax

StoreAddACL(url, ACLObject)


Parameters

Parameter         Description

url               Amazon S3 URLs (content or object).

ACLObject         An array of struct where each struct represents a permission or grant as discussed in "ACLObject" on page 262.



History
ColdFusion 9 Update 1: Added this function


Usage
Use this function to add permissions to the existing ones.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                264
Developing CFML Applications




Example
<cftry>
    <cfset dir = "s3://bucket_name/">
    <cfset perm = structnew()>
    <cfset perm.group = "authenticated">
    <cfset perm.permission = "READ">
    <cfset perm1 = structnew()>
    <cfset perm1.email = "email_ID">
    <cfset perm1.permission = "READ_ACP">
    <cfset myarrray = [perm,perm1]>
    <cfif NOT DirectoryExists(dir)>
         <cfset directoryCreate(dir)>
    </cfif>
    <cfset fileWrite("#dir#/Sample.txt","This is to test StoreAddACL")>
    <cfset StoreAddACL("#dir#","#myarrray#")>
    <cfset test = StoreGetACL(dirkey)>
         <cfdump var="#test#">
    <cfcatch>
         <cfdump var="#cfcatch#">
    </cfcatch>
</cftry>



storeGetACL

Description
Gets the ACL object or bucket.


Returns
Returns an ACLObject


Syntax

StoreGetACL(url, ACLObject)


Parameters

Parameter         Description

url               Amazon S3 URLs (content or object)

ACLObject         An array of struct where each struct represents a permission or grant as discussed in "ACLObject" on page 262.



History
ColdFusion 9 Update 1: Added this function


Example
<cfset dir = "s3://bucket_Name">
    <cfif NOT DirectoryExists(dir)>
           <cfset directoryCreate(dir)>
         </cfif>
    <cfset test = StoreGetACL("#dir#")>
    <cfdump var="#test#">




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   265
Developing CFML Applications




Using metadata
Amazon S3 allows you to specify metadata for both objects and buckets.

The following two functions let you get and set the metadata on objects or buckets.


StoreGetMetadata

Description
Returns the metadata related to the object or bucket.


Returns
Object metadata or bucket metadata


Syntax

StoreGetMetadata(url)


Parameters

Parameter         Description

url               Amazon S3 URLs (bucket or object).



History
ColdFusion 9 Update 1: Added this function


Example
<cfdump var = #StoreGetMetadata("bucket_Name")#>



StoreSetMetadata

Description
Sets the metadata on bucket or object.


Returns
Nothing


Syntax

StoreSetMetadata(url,Struct)


Parameters

Parameter         Description

url               Amazon S3 URLs (bucket or object).

struct            Represents the metadata. See "Standard keys" on page 266 for a list of standard keys in metadata.

                  You can also have custom metadata apart from the standard ones.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   266
Developing CFML Applications




History
ColdFusion 9 Update 1: Added this function


Example
<cfscript>
       mydate = #Now()#;
       hello = structNew();
       hello.color = "grey";
/cfscript>


<cfset dir = "s3://mycfbucket">
       <cffile action="write" file="#dir#/hello5.txt" output="Sample s3 text">


       <cfset StoreSetMetadata("#dir#/hello5.txt","#hello#")>
       <cfset test = StoreGetMetadata("#dir#/hello5.txt")>


<cfdump var="#test#">



Standard keys
The following are the standard keys in the metadata:


For objects
· last_modified

· date

· owner

· etag

· content_length

· content_type

· content_encoding

· content_disposition

· content_language

· content_md5

· md5_hash


For buckets
· date

· owner


Security considerations
Sandboxing is not applicable to S3 buckets or objects as Amazon S3 has its own security features that take care of it.


Supported functions

fileOpen                     fileClose                    fileCopy                    fileDelete

fileExists                   fileisEOF                    fileMove                    fileWrite

fileRead                     fileReadBinary               fileReadLine                fileSetLastModified

getFileInfo                  getDirectoryFromPath         directoryCreate             directoryDelete




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   267
Developing CFML Applications




directoryExists              directoryList                   imageNew                    imageRead

imageWrite                   imageWriteBase64                isImageFile                 isPDFFile


Supported tags

All cffile actions           All cfdirectory actions         cfdocument                  cffeed
                             (except rename)
cfftp                        cfimage                         cfloop                      All cfimage actions


Limitations
· The following tags are not supported:

    · cfpdf

    · cfpdfform

· The following functions are not supported:

    · FileSetAccessMode that sets attributes of a file in Linux/UNIX

    · FilesSetAttribute that sets the attributes of a file in Windows

    · cfzip does not accept Amazon S3 object as source.

    · When S3 object is used as output for outputfile attribute of cfexecute tag, it results in an error Timeout
       period expired without completion of <exe>. It also results in a NullPointerException at server console.

    · To use the function fileMove, the source and destination objects must have the same bucket name. That is,
       you cannot move Amazon S3 objects across buckets or to other file systems.


Working with in-memory files
Memory-based virtual file system speeds up the processing of transient data. In-memory files are not written to disk
and are saved on RAM. They function similar to disk files but perform faster.

In ColdFusion, in-memory files help you to simplify the execution of dynamic code. In-memory files are supported
across almost all tags and functions that take file or directory as input or output.

You use in-memory files in the same manner as files on disk, but with a prefix ram:/// to indicate that they reside on
RAM. For example, ram:///a/b/dynamic.cfm.


Writing and executing dynamic CFM files
The following syntax explains how to write CFM data in to an in-memory file:

<cffile action="write" output="#cfml#"
file="ram:///filename.cfm"/>

The following sample syntax explains how to use the in-memory CFM file:

· For tags that take logical path, define mapping in Administrator. Execute in-memory CFM pages using the
    cfinclude tag:

    <cfinclude template="/inmemory/filename.cfm">

    Create a mapping for ram:/// so that it can be used in the tags. In this example, /inmemory is the mapping that
    points to ram:///.

· For tags that take absolute path, specify the syntax as provided in the following example:

    <cffile action="append" file="ram:///a/b/dynamic.cfm" output="I'm appending">

Note: You cannot have Application.cfm as an in-memory file. If you have one, it is ignored.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   268
Developing CFML Applications




Example
The following code describes how to write an image as an in-memory file:

<cffile action="readBinary" variable="myImage" file="#ExpandPath('./')#/blue.jpg">
<cffile action="write" output="#myImage#" file="ram:///a.jpg">
<cfif FileExists("ram:///a.jpg")>
<cfoutput>a.jpg exists</cfoutput>
<cfelse>
<cfoutput>a.jpg Doesn't exists</cfoutput>
</cfif>


Writing and instantiating dynamic CFC files
The following syntax explains how you can write CFC code in to an in-memory file:

<cffile action="write" output="#cfcData#"
file="ram:///filename.cfc"/>

The following sample syntax explains how you can instantiate the in-memory CFC file:

<cfset cfc=CreateObject("component","inmemory.filename")/>

Here, inmemory is the ColdFusion mapping that points to ram:///.

Note: You cannot have Application.cfc as an in-memory file. If you have one, it is ignored.


Example
The following code writes a CFC as in-memory file:

<cffile action="read" file="#ExpandPath('./')#/dynamic.cfc" variable="Message">
<cffile action="write" file="ram:///cfc/dynamic.cfc" output="#Message#">

To invoke a component method:

<cfinvoke component="inmemory.cfc.dynamic" method ="method1" returnVariable="returnVariable">
<cfinvokeargument name="paramOne" value="hello">
</cfinvoke>
<cfoutput>#returnVariable#</cfoutput>


Working with in-memory file system
The following sections provide information that can help you to access and use in-memory files.


Using in-memory files
· You can call a CFC saved on RAM from a CFM file on disk. Similarly, an in-memory CFM file can call a CFC saved
  on disk.

· If a CFC extends another CFC in the same directory in RAM, you can use relative path. For instance, if a.cfc and
  b.cfc belong to the same directory in RAM, a.cfc can extend b.cfc using relative path as shown in the following code:

  <cfcomponent name="a" extends"b">
  ...
  </cfcomponent>

· You can use in-memory ColdFusion interfaces in the same way as you use in-memory CFCs.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                          269
Developing CFML Applications




Supported functions
The following file functions are supported for in-memory files:

· FileIsEOF

· FileReadBinary

· Filemove

· Filecopy

· FileReadLine

· FileExists

· FileOpen

· FileWriteln

· FileClose

· FileRead

· FileDelete

· DirectoryExists

· FileSetLastModified

· GetFileInfo

· GetDirectoryFromPath

· GetFileFromPath

· ImageNew

· ImageRead

· ImageWrite

· ImageWriteBase64

· IsImageFile

· IsPDFFile

· FileSetLastModified


Example
The following syntax explains the function FileSetLastModified()

<cftry>
<cffile action="write" file="ram:///a.txt" output="Testing the function FileSetLastModified">
<cfset date="12/12/2007">
<cfscript>
FileSetLastModified("ram:///a.txt", "#date#");
sleep(1000);
WriteOutput(#GetFileInfo("ram:///a.txt").lastmodified#);
</cfscript>
<cfcatch>
<cfset PrintException(cfcatch)>
</cfcatch>
</cftry>
<cf_expectedresults>{ts '2007-12-12 00:00:00'}
</cf_expectedresults>



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                270
Developing CFML Applications




File operations
The following file operations are supported for in-memory files:

· Directory-specific operations: create, delete, list, and rename.

· File-specific operations: copy, create, write, append, delete, rename, create attributes, modes move, and read.


Example
The following code illustrates the file and directory operations:

<cfdirectory action = "create" directory = "ram://src" >
<cfdirectory action = "create" directory = "ram://des" >
<cfdirectory action = "rename" directory = "ram:///CurrentDir" newDirectory = "NewDir">
<cfdirectory action="list" directory="ram://" name="listDir" recurse="yes" >
<cfdump var="#listDir#">
<cffile action="write" file = "ram://src/test.txt" output = "Release Description">
<cffile action="copy" source="ram://src/test.txt" destination="ram://des/final.txt" >
<cffile action="rename" source = "ram:///src/message.txt" destination = "ram:///des/test.txt">
<cffile action ="move" source = "ram:///des/test.txt" destination = "c:\des\move.txt">



Document and image actions
All image and document actions can use in-memory image files as shown in the following examples:

<cfimage action="captcha" fontSize="15" width="180" height="50" text="readMe"
destination="ram:///readMe.jpg"
difficulty="medium">
<cfimage source="ram://aiden02.png" action="convert"
destination="#ExpandPath("./")#/blue1.JPG" overwrite="yes">
<cfdocument format="pdf" filename="ram://Sample.pdf" overwrite="yes">Sample Text</cfdocument>



Custom tags
In-memory CFM pages and CFCs can call custom tags but the custom tags must be present in disk. In-memory custom
tags are not supported.


Using in-memory files in tags
The following tags are supported for in-memory files:

· cfcontent

· cfdocument

· cfdump

· cfexchange

· cfexecute

· cffeed

· cfhttp

· cfftp

· cfimage

· cfloop




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     271
Developing CFML Applications




· cfpresentation

· cfprint

· cfreport

· cfzip


Example using the tag cfcontent
<cfcontent file="ram:///a.jpg" type="image/jpeg" deletefile="yes">



Adding permissions
ColdFusion lets you add permissions for directories/files on RAM using an existing sandbox security setup. You can
only set up sandbox security for disk directories and not for RAM directories. Access to an in-memory directory/file
can be restricted only through an existing local file system sandbox.

Therefore, to set up sandbox security for in-memory files, select a sandbox that you have already configured for a disk
directory.

By default the ram:/// directories are included in the list of secured folders and have read, write, execute, and delete
permissions set. Therefore, all RAM files have permissions by default in a sandbox. All the security restrictions that
apply to disk files apply to in-memory files.

To set up Sandbox security for in-memory files,

1 Open the Security > Sandbox Security page in the ColdFusion Administrator.

   The Sandbox Security Permissions page appears.

2 In the Add Security Sandbox box, specify a disk directory and then click Add.

3 In Files/Directories, specify the in-memory file path. For example, ram:///a/b (for directory) or
   ram:///a/b/dynamic.cfm (for file).

4 Select the required permissions, click Add Files/Paths, and then click Finish.

For further details on sandbox security, refer to the ColdFusion Administration Guide.


Accessing VFS information
The GetVFSMetaData function lets you access VFS information. This function takes the parameter RAM as input.

This function returns a structure that contains the following information:

· If support for in-memory virtual file system is enabled

· Memory limit in bytes for in-memory virtual file system

· The used and free memory

For example, <cfdump var="#getVFSMetaData("ram")#">

Note: The Settings page of the ColdFusion Administrator has the options to enable/disable the in-memory virtual file
system support. You can also specify the memory limit in Megabytes (MB) for in-memory virtual file system.


Deleteing in-memory files
The in-memory files remain in RAM as long as the server is up. When required, clean up the files using
cffile/cfdirectory with the action=delete. For example, delete all the contents in RAM directory "ram://a/b" using
the following code: <cfdirectory action="delete" directory="ram:///a/b" recurse="yes">


                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  272
Developing CFML Applications




Limitations
· File names/Directory names on RAM are case sensitive.

· In-memory files must be accessed using mapping or absolute path. Relative paths to files/directories are not
   supported.

    · Correct: ram:///a/b/

    · Incorrect: ram:///a/b/../..

· You cannot access in-memory files using HTTP/HTTPS protocols. Instead, use ram:///<file>. For example,
   ram:///a/b/test.cfm.

· DDX files and font files cannot be accessed as in-memory files.

· The following functions are not supported for in-memory files.

    · FileSetAccessMode that sets attributes of a file in Linux/Unix

    · FilesSetAttribute that sets the attributes of a file in Windows

· The following tags are not supported:

    · cfpdf

    · cfpdfform

· The following scenarios are not supported:

    · The cfreport tag does not accept a template report on RAM. Therefore, the following does not work:

       <cfreport format="PDF" template="ram:///myReport1.cfr" filename="ram:///test.pdf"
       overwrite="yes">

       In this case, the myReport1.cfr must reside on your disk.

    · The cfimport tag does not accept tag libraries on RAM. For instance, the following does not work:

       <cfimport prefix="custom" taglib="ram:///a/b/mytags.jar">

    · Renaming across file systems (as shown in the following syntax) is not supported.

       <cffile action="rename" source="ram:///src1/message2.txt"
       destination="#ExpandPath('./')#/123.txt">.

       Instead, you can move the files.

    · For the cfexecute tag, the executable cannot be on RAM and must be on your local file system. But the output
       can be an in-memory file as illustrated in the following code:

       <cfexecute name="C:\WINDOWS\System32\netstat.exe" arguments="-e"
       outputFile="ram:///output.txt" timeout="1">
       </cfexecute>
       <cfset thisPath=ExpandPath("*.*")>


Optimizing database use
Poor database design and incorrect or inefficient use of the database are among the most common causes of inefficient
applications. Consider the different methods that are available for using databases and information from databases
when you design your application. For example, to average the price of many products from a SQL query, it is more
efficient to use SQL to get the average than to use a loop in ColdFusion.

Two important ColdFusion tools for optimizing your use of databases are the cfstoredproc tag and the cfquery tag
cachedWithin attribute.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    273
Developing CFML Applications




Using stored procedures
The cfstoredproc tag lets ColdFusion use stored procedures in your database management system. A stored
procedure is a sequence of SQL statements that is assigned a name, compiled, and stored in the database system. Stored
procedures encapsulate programming logic in SQL statements, and database systems are optimized to execute stored
procedures efficiently. As a result, stored procedures are faster than cfquery tags.

You use the cfprocparam tag to send parameters to the stored procedure, and the cfprocresult tag to get the record
sets that the stored procedure returns.

The following example executes a Sybase stored procedure that returns three result sets, two of which the example uses.
The stored procedure returns the status code and one output parameter, which the example displays.

<!--- cfstoredproc tag --->
<cfstoredproc procedure = "foo_proc" dataSource = "MY_SYBASE_TEST"
      username = "sa" password = "" returnCode = "Yes">


      <!--- cfprocresult tags --->
      <cfprocresult name = RS1>
      <cfprocresult name = RS3 resultSet = 3>


      <!--- cfprocparam tags --->
      <cfprocparam type = "IN"
           CFSQLType = CF_SQL_INTEGER
                value = "1">
      <cfprocparam type = "OUT"CFSQLType = CF_SQL_DATE
           variable = FOO>
<!--- Close the cfstoredproc tag. --->
</cfstoredproc>


<cfoutput>
      The output param value: '#foo#'<br>
</cfoutput>


<h3>The Results Information</h3>
<cfoutput query = RS1>
      #name#,#DATE_COL#<br>
</cfoutput>
<br>
<cfoutput>
      <hr>
      Record Count: #RS1.recordCount#<br>
      Columns: #RS1.columnList#<br>
      <hr>
</cfoutput>


<cfoutput query = RS3>
      #col1#,#col2#,#col3#<br>
</cfoutput>
<br>
<cfoutput>
      <hr><br>
      Record Count: #RS3.recordCount#<br>
      Columns: #RS3.columnList#<br>
      <hr>


      The return code for the stored procedure is: '#cfstoredproc.statusCode#'<br>
</cfoutput>



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                              274
Developing CFML Applications




Reviewing the code
The following table describes the code and its function:


 Code                                                                   Description

 <cfstoredproc procedure = "foo_proc" dataSource =                      Runs the stored procedure foo_proc on the
 "MY_SYBASE_TEST" username = "sa" password = "" returnCode =            MY_SYBASE_TEST data source. Populates the
 "Yes">
                                                                        cfstoredprocstatusCode variable with the status
                                                                        code returned by stored procedure.

 <cfprocresult name = RS1>                                              Gets two record sets from the stored procedure: the first
 <cfprocresult name = RS3 resultSet = 3>                                and third result sets it returns.

 <cfprocparam type = "IN" CFSQLType = CF_SQL_INTEGER value =            Specifies two parameters for the stored procedure, an
 "1">                                                                   input parameter and an output parameter. Sets the
 <cfprocparam type = "OUT" CFSQLType = CF_SQL_DATE variable =
                                                                        input parameter to 1 and the ColdFusion variable that
 FOO>
                                                                        gets the output to FOO.
 <!--- Close the cfstoredproc tag. --->
 </cfstoredproc>
                                                                        Ends the cfstoredproc tag body.

 <cfoutput>                                                             Displays the results of running the stored procedure:
 The output param value: '#foo#'<br>
 </cfoutput>                                                            · The output parameter value,

 <h3>The Results Information</h3>                                       · Thecontentsofthetwocolumnsinthefirstrecordset
 <cfoutput query = RS1>
 #name#,#DATE_COL#<br>                                                    identified by the name and DATE_COL variables. You
 </cfoutput>                                                              set the values of these variables elsewhere on the
 <br>                                                                     page.
 <cfoutput>
 <hr>                                                                   · The number of rows and the names of the columns in
 Record Count: #RS1.recordCount#<br>
                                                                          the first record set
 Columns: #RS1.columnList#<br>
 <hr>
                                                                        · The contents of the columns in the other record set
 </cfoutput>
                                                                          identified by the col1, col2, and col3 variables.
 <cfoutput query = RS3>
 #col1#,#col2#,#col3#<br>                                               · The number of rows and the names of the columns in
 </cfoutput>
                                                                          the record set.
 <br>
 <cfoutput>                                                             · The status value returned by the stored procedure.
 <hr><br>
 Record Count: #RS3.recordCount#<br>
 Columns: #RS3.columnList#<br>
 <hr>

 The return code for the stored procedure is:
 '#cfstoredproc.statusCode#'<br>
 </cfoutput>


For more information on creating stored procedures, see your database management software documentation. For
more information on using the cfstoredproc tag, see the CFML Reference.


Using the cfquery tag cachedWithin attribute
The cfquery tag cachedWithin attribute tells ColdFusion to save the results of a database query for a specific period
of time. This way, ColdFusion accesses the database on the first page request, and does not query the database on
further requests until the specified time expires. Using the cachedWithin attribute significantly limits the overhead of
accessing databases that do not change rapidly.

This technique is useful if the database contents only change at specific, known times, or if the database does not
change frequently and the purpose of the query does not require up- to-date results.

Use the CreateTimeSpan function to specify the cachedWithin attribute value (in days, hours, minutes, seconds
format). For example, the following code caches the results of getting the contents of the Employees table of the
cfdocexamples data source for one hour.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    275
Developing CFML Applications




<cfquery datasource="cfdocexamples" name="master"
     cachedWithin="#CreateTimeSpan(0,1,0,0)#">
     SELECT * FROM Employees
</cfquery>


Providing visual feedback to the user
If an application takes a while to process data, it is useful to provide visual feedback to indicate that something is
happening, so the user does not assume that there is a problem and requests the page again. Although doing this does
not optimize your application's processing efficiency, it does make the application appear more responsive.

Use the cfflush tag to return partial data to a user, as shown in "Introduction to Retrieving and Formatting Data" on
page 703.

You can also use the cfflush tag to create a progress bar. For information on this technique, see the technical article
"Understanding Progress Meters in ColdFusion 5" at www.adobe.com/go/learn_cfu_progress_meters_en. (Although
this article was written for ColdFusion 5, it also applies to later versions of ColdFusion.)



Handling Errors

Adobe ColdFusion includes many tools and techniques for responding to errors that your application encounters.
These tools include error handling mechanisms and error logging tools.

For information on user input validation, see "Introduction to Retrieving and Formatting Data" on page 703 and
"Building Dynamic Forms with cfform Tags" on page 722 For information on debugging, see "Debugging and
Troubleshooting Applications" on page 378.


About error handling in ColdFusion
By default, ColdFusion generates its own error messages when it encounters errors. In addition, it provides a variety
of tools and techniques for you to customize error information and handle errors when they occur. You can use any
of the following error-management techniques.

· Specify custom pages for ColdFusion to display in each of the following cases:

    · When a ColdFusion page is missing (the Missing Template Handler page)

    · When an otherwise-unhandled exception error occurs during the processing of a page (the Site-wide Error
       Handler page)

   You specify these pages on the Settings page in the Server Settings page in the ColdFusion Administrator; for more
   information, see the ColdFusion Administrator Help.

· Use the cferror tag to specify ColdFusion pages to handle specific types of errors.

· Use the cftry, cfcatch, cfthrow, and cfrethrow tags to catch and handle exception errors directly on the page
   where they occur.

· In CFScript, use the try and catch statements to handle exceptions.

· Use the onError event in Application.cfc to handle exception errors that are not handled by try/catch code on the
   application pages.

· Log errors. ColdFusion logs certain errors by default. You can use the cflog tag to log other errors.

The following information is detailed in the next few topics:

· The basic building blocks for understating types of ColdFusion errors and how ColdFusion handles them


                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                           276
Developing CFML Applications




· How to use the cferror tag to specify error-handling pages

· How to log errors

· How to handle ColdFusion exceptions

   Note: Information about using the cftry and cfcatch tags is provided, but not the equivalent CFScript try and
   catch statements. The general discussion of exception handling applies to tags and CFScript statements. However, the
   code that you use and the information available in CFScript differs from the information in the tags. For more
   information on handling exceptions in CFScript, see "Handling errors in UDFs" on page 166.


Understanding errors
You can look at errors in many ways; for example, you can look at errors by their causes. You can also look at them by
their effects, particularly by whether your application can recover from them. You can also look at them the way
ColdFusion does, as follows:


About error causes and recovery
Errors can have many causes. Depending on the cause, the error can be recoverable. A recoverable error is one for
which your application can identify the error cause and take action on the problem. Some errors, such as time-out
errors, are recoverable without indicating to the user that an error was encountered. An error for which a requested
application page does not exist is not recoverable, and the application can only display an error message.

Errors such as validation errors, for which the application cannot continue processing the request, but can provide an
error-specific response, can also be considered recoverable. For example, an error that occurs when a user enters text
where a number is required can be considered recoverable, because the application can recognize the error and
redisplay the data field with a message providing information about the cause of the error and telling the user to reenter
the data.

Some types of errors are recoverable in some, but not all circumstances. For example, your application can retry a
request following a time-out error, but it must also be prepared for the case where the request always times out.

Error causes fall in the broad categories listed in the following table:


 Category                 Description

 Program errors           Can be in the code syntax or the program logic. The ColdFusion compiler identifies and reports program syntax
                          errors when it compiles CFML into Java classes. Errors in your application logic are harder to locate. For
                          information on debugging tools and techniques, see "Debugging and Troubleshooting Applications" on
                          page 378.

                          Unlike ColdFusion syntax errors, SQL syntax errors are only caught at runtime.

 Data errors              Are typically user data input errors. You use validation techniques to identify errors in user input data and enable
                          the user to correct the errors.

 System errors            Can come from a variety of causes, including database system problems, time-outs due to excessive demands on
                          your server, out-of-memory errors in the system, file errors, and disk errors.


Although these categories do not map completely to the way ColdFusion categorizes errors they provide a useful way
of thinking about errors and can help you in preventing and handling errors in your code.


ColdFusion error types
Before you can effectively manage ColdFusion errors, you must understand how ColdFusion classifies and handles
them. ColdFusion categorizes errors as detailed in the following table:




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         277
Developing CFML Applications




 Type                               Description

 Exception                          An error that prevents normal processing from continuing. All ColdFusion exceptions are, at their
                                    root, Java exceptions.

 Missing template                   An HTTP request for a ColdFusion page that cannot be found. Generated if a browser requests a
                                    ColdFusion page that does not exist.

                                    Missing template errors are different from missing include exceptions, which result from
                                    cfinclude tags or custom tag calls that cannot find their targets.

 Form field data validation         Server-side form field validation errors are a special kind of ColdFusion exception. You specify server-
                                    side form validation by using cfform attributes or hidden HTML form fields. All other types of server-
                                    side validation, such as the cfparam tag generate runtime exceptions. For more information on
                                    validating form fields see "Validating Data" on page 743.

                                    ColdFusion includes a built-in error page for server-side form field validation errors, and the cferror
                                    tag includes a type attribute that lets you handle these errors in a custom error page, but if you use
                                    onError processing in Application.cfc, or try/catch error handling, the error appears as an Application
                                    exception. For more information on handling Form field validation in Application.cfc see "Handling
                                    server-side validation errors in the onError method" on page 248.


Note: The onSubmit and onBlur form field validation techniques use JavaScript or Flash validation on the client browser.


About ColdFusion exceptions
Most ColdFusion errors are exceptions. You can categorize ColdFusion exceptions in two ways:

· When they occur

· Their type


When exceptions occur
ColdFusion errors can occur at two times, when the CFML is compiled into Java and when the resulting Java executes,
called runtime exceptions.


Compiler exceptions
Compiler exceptions are programming errors that ColdFusion identifies when it compiles CFML into Java. Because
compiler exceptions occur before the ColdFusion page is converted to executable code, you cannot handle them on
the page that causes them. However, other pages can handle these errors. For more information, see "Handling
compiler exceptions" on page 282.


Runtime exception
A runtime exception occurs when the compiled ColdFusion Java code runs. It is an event that disrupts the normal flow
of instructions in the application. Exceptions can result from system errors or program logic errors. Runtime
exceptions include:

· Error responses from external services, such as an ODBC driver or CORBA server

· CFML errors or the results of cfthrow or cfabort tags

· Internal errors in ColdFusion




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  278
Developing CFML Applications




ColdFusion exception types
ColdFusion exceptions have types that you specify in the cferror, cfcatch, and cfthrow error-handling tags. A
cferror or cfcatch tag handles only exceptions of the specified type. You identify an exception type by using an
identifier from one (or more) of the following type categories:

· Basic

· Custom

· Advanced

· Java class

Note: Use only custom error type names and the Application basic type name in cfthrow tags. All other built-in
exception type names identify specific types of system-identified errors, so do not use them for errors that you identify
yourself.


Basic exception types
All ColdFusion exceptions except for custom exceptions belong to a basic type category. These types consist of a
broadly defined categorization of ColdFusion exceptions. The following table describes the basic exception types:


 Type                           Type name                    Description

 Database failures              Database                     Failed database operations, such as failed SQL statements, ODBC
                                                             problems, and so on.

 Missing include file errors    MissingInclude               Errors where files specified by the cfinclude, cfmessagebox, and cferror
                                                             tags are missing. (A cferror tag generates a missingInclude error
                                                             only when an error of the type specified in the tag occurs.)

                                                             The MissingInclude error type is a subcategory of Template error. If
                                                             you do not specifically handle the MissingInclude error type, but do
                                                             handle the Template error type, the Template error handler catches
                                                             these errors. MissingInclude errors are caught at runtime.

 Template errors                Template                     General application page errors, including invalid tag and attribute
                                                             names. Most Template errors are caught at compile time, not
                                                             runtime.

 Object exceptions              Object                       Exceptions in ColdFusion code that works with objects.

 Security exceptions            Security                     Catchable exceptions in ColdFusion code that works with security.

 Expression exceptions          Expression                   Failed expression evaluations; for example, if you try to add 1 and "a".

 Locking exceptions             Lock                         Failed locking operations, such as when a cflock critical section times
                                                             out or fails at runtime.

 Verity Search engine exception SearchEngine                 Exceptions generated by the Verity search engine when processing
                                                             cfindex, cfcollection, or cfsearch tags.

 Application-defined exception  Application                  Custom exceptions generated by a cfthrow tag that do not specify a
 events raised by cfthrow                                    type, or specify the type as Application.

 All exceptions                 Any                          Any exceptions. Includes all types in this table and any exceptions that
                                                             are not handled in another error handler, including unexpected
                                                             internal and external errors.


Note: The Any type includes all error with the Java object type of java.lang.Exception. It does not include
java.lang.Throwable errors. To catch Throwable errors, specify java.lang.Throwable in the cfcatch tag type attribute.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     279
Developing CFML Applications




Custom exceptions
You can generate an exception with your own type by specifying a custom exception type name, for example
MyCustomErrorType, in a cfthrow tag. You then specify the custom type name in a cfcatch or cferror tag to
handle the exception. Custom type names must be different from any built-in type names, including basic types and
Java exception classes.


Advanced exception types
The Advanced exceptions consist of a set of specific, narrow exception types. These types are supported in ColdFusion
for backward-compatibility.


Java exception classes
Every ColdFusion exception belongs to, and is identified by, a specific Java exception class in addition to its basic,
custom, or advanced type. The first line of the stack trace in the standard error output for an exception identifies the
Java class of the exception.

For example, if you attempt to use an array function such as ArrayIsEmpty on an integer variable, ColdFusion
generates an exception that belongs to the Expression exception basic type and the
coldfusion.runtime.NonArrayException Java class.

In general, most applications do not use Java exception classes to identify exceptions. However, you can use Java class
names to catch exceptions in non-CFML Java objects; for example, the following line catches all Java input/output
exceptions:

<cfcatch type="java.io.IOException">


How ColdFusion handles errors
The following information describes briefly how ColdFusion handles errors. Detailed information is provided in the
remaining topics.


Missing template errors
If a user requests a page that ColdFusion cannot find, and the Administrator Server Settings Missing Template
Handler field specifies a Missing Template Handler page, ColdFusion uses that page to display error information.
Otherwise, it displays a standard error message.


Form field validation errors
When a user enters invalid data in an HTML tag that uses onServer or hidden form field server-side data validation
ColdFusion does the following:

1 If the Application CFC (Application.cfc) has an onError event handler method, ColdFusion calls the method.

2 If the Application.cfc initialization code or the Application.cfm page has a cferror that specifies a Validation error
   handler, ColdFusion displays the specified error page.

3 Otherwise, it displays the error information in a standard format that consists of a default header, a bulleted list
   describing the errors, and a default footer.

For more information on using hidden form field validation, see "Validating Data" on page 743. For more information
on Application.cfc, see "Designing and Optimizing a ColdFusion Application" on page 235.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                             280
Developing CFML Applications




Compiler exception errors
If ColdFusion encounters a compiler exception, how it handles the exception depends on whether the error occurs on
a requested page or on an included page:

· If the error occurs on a page that is accessed by a cfinclude or cfmessagebox tag, or on a custom tag page that
    you access using the cf_ notation, ColdFusion handles it as a runtime exception in the page that accesses the tag.
    For a description of how these errors are handled, see the next section, " Runtime exception errors."

· If the error occurs directly on the requested page, and the Administrator Settings Site-wide Error Handler field
    specifies an error handler page, ColdFusion displays the specified error page. Otherwise, ColdFusion reports the
    error using the standard error message format described in "Error messages and the standard error format" on
    page 280.


Runtime exception errors
If ColdFusion encounters a runtime exception, it does the action for the first matching condition in the following table:


Condition                                                               Action

The code with the error is inside a cftry tag and the exception type is Executes the code in the cfcatch tag.
specified in a cfcatch tag.
                                                                        If the cftry block does not have a cfcatch tag for this error, tests for
                                                                        an appropriate cferror handler or site-wide error handler.

The ColdFusion application has an Application.cfc with an onError       Executes the code in the onError method. For more information on
method                                                                  using the onError method, see "Handling errors in Application.cfc" on
                                                                        page 247.

A cferror tag specifies an exception error handler for the exception    Uses the error page specified by the cferror tag.
type.

The Administrator Settings Site-wide Error Handler field specifies an   Uses the custom error page specified by the Administrator setting.
error handler page.

A cferror tag specifies a Request error handler.                        Uses the error page specified by the cferror tag.

The default case.                                                       Uses the standard error message format


For example, if an exception occurs in CFML code that is not in a cftry block, and Application.cfc does not have an
onError method, but a cferror tag specifies a page to handle this error type, ColdFusion uses the specified error page.



Error messages and the standard error format
If your application does not handle an error, ColdFusion displays a diagnostic message in the user's browser.

Error information is also written to a log file for later review. (For information on error logging, see "Logging errors
with the cflog tag" on page 285.)

The standard error format consists of the information listed in the following table. ColdFusion does not always display
all sections.




                                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                                  281
Developing CFML Applications




 Section                 Description

 Error description       A brief, typically online, description of the error.

 Error message           A detailed description of the error. The error message diagnostic information displayed depends on the type of
                         error. For example, if you specify an invalid attribute for a tag, this section includes a list of all valid tag attributes.

 Error location          The page and line number where ColdFusion encountered the error, followed by a short section of your CFML that
                         includes the line. This section does not display for all errors.

                         In some cases, the cause of an error can be several lines above the place where ColdFusion determines that there
                         is a problem, so the line that initially causes the error might not be in the display.

 Resources               Links to documentation, the Knowledge Base, and other resources that can help you resolve the problem.

 Error environment       Information about the request that caused the error. All error messages include the following:
 information
                         · User browser

                         · User IP address

                         · Date and time of error

 Stack trace             The Java stack at the time of the exception, including the specific Java class of the exception. This is helpful if you
                         must contact Adobe Technical Support.

                         The stack trace is collapsed by default. Click the heading to display the trace.


    If you get a message that does not explicitly identify the cause of the error, check the key system parameters, such as
    available memory and disk space.


Determining error-handling strategies
ColdFusion provides you with many options for handling errors, particularly exceptions, as described in "How
ColdFusion handles errors" on page 279. The considerations for determining which forms of error handling to use are
as follows:


Handling missing template errors
Missing template errors occur when ColdFusion receives an HTTP request for a page ending in .cfm that it cannot
find. You can create your own missing template error page to present application-specific information or provide an
application-specific appearance. You specify the missing template error page on the Administrator Settings page.

The missing error page can use CFML tags and variables. In particular, you can use the CGI.script_name variable in
text such as the following to identify the requested page:

<cfoutput>The page #Replace(CGI.script_name, "/", "")# is not available.<br>
Make sure that you entered the page correctly.<br>
</cfoutput>

(In this code, the Replace function removes the leading slash sign from the script name to make the display more
friendly.)




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                           282
Developing CFML Applications




Handling form field validation errors
When you use server-side form field validation, the default validation error message describes the error cause plainly
and clearly. However, to give the error message a custom look or provide additional information such as service
contact phone numbers and addresses, use the cferror tag with the Validation attribute in the Application.cfc
initialization code. Or, specify your own validation on the Application.cfm page. An example of such a page is
provided at Example of a validation error page section. You can also place form field validation error handling code in
the Application.cfc onError method.


Handling compiler exceptions
You cannot handle compiler exceptions directly on the page where they occur, because the exception is caught before
ColdFusion starts running the page code. Fix all compiler exceptions as part of the development process. Use the
reported error message and the code debugging techniques discussed in "Debugging and Troubleshooting
Applications" on page 378 to identify and correct the cause of the error.

Compiler exceptions that occur on pages you access by using the cfinclude or cfmessagebox tags can be handled as
runtime errors by surrounding the cfinclude or cfmodule tag in a cftry block. The compiler exception on the
accessed page gets caught as a runtime error on the base page. However, avoid this "solution" to the problem, as the
correct method for handling compiler errors is to remove them before you deploy the application.


Handling runtime exceptions
You have many choices for handling exceptions, and the exact path you take depends on your application and its
needs. The following table provides a guide to selecting an appropriate technique:


 Technique                Use

 cftry                    Place cftry blocks around specific code sections where exceptions can be expected and you want to handle
                          those exceptions in a context-specific manner; for example, if you want to display an error message that is specific
                          to that code.

                          Use cftry blocks where you can recover from an exception. For example, you can retry an operation that times
                          out, or access an alternate resource. You can also use the cftry tag to continue processing where a specific
                          exception does not harm your application; for example, if a missing resource is not required.

                          For more information, see "Handling runtime exceptions with ColdFusion tags" on page 287.

 Application.cfc onError  Implement the onError method in your Application.cfc to consistently handle application-specific exceptions
 method                   that are generated by multiple code sections in the application. For more information on error handling using
                          Application.cfc, see "Handling errors in Application.cfc" on page 247.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                            283
Developing CFML Applications




 Technique                Use

 cferrorwith exception-   Use the cferror tag to specify error pages for specific exception types. These pages cannot recover from errors,
 specific error handler   but they can provide the user with information about the cause of the error and the steps to take to prevent the
 pages                    problem.

                          For more information, see "Specifying custom error messages with the cferror tag" on page 283.

 cferror with a Request   Use the cferror tag to specify a Request error handler that provides a customized, application-specific message
 error page               for unrecoverable exceptions. Place the tag in the Application.cfc initialization code or on the Application.cfm
                          page to make it apply to all pages in an application.

                          A Request error page cannot use CFML tags, but it can display error variables. As a result, you can use it to display
                          common error information, but you cannot provide error-specific instructions. Typically, Request pages display
                          error variable values and application-specific information, including support contact information.

                          For example code, see Example of a request error page section.

 Site-wide error handler  Specify a site-wide error handler in the Administrator to provide consistent appearance and contents for all
 page                     otherwise-unhandled exceptions in all applications on your server.

                          Like the Request page, the site-wide error handler cannot perform error recovery. However, it can include CFML
                          tags in addition to the error variables.

                          Because a site-wide error handler prevents ColdFusion from displaying the default error message, it allows you to
                          limit the information reported to users. It also lets you provide all users with default contact information or other
                          instructions.



Specifying custom error messages with the cferror tag
Custom error pages let you control the error information that users see. You can specify custom error pages for
different types of errors and handle different types of errors in different ways. For example, you can create specific
pages to handle errors that could be recoverable, such as request time-outs. You can also make your error messages
consistent with the look and feel of your application.

You can specify the following types of custom error message pages:


 Type                     Description

 Validation               Handles server-side form field data validation errors. The validation error page cannot include CFML tags, but it
                          can display error page variables.

                          You can use this attribute only in the Application.cfc initialization code or on the Application.cfm page. It has no
                          effect when used on any other page. Therefore, you can specify only one validation error page per application,
                          and that page applies to all server-side validation errors.

 Exception                Handles specific exception errors. You can specify individual error pages for different types of exceptions.

 Request                  Handles any exception that is not otherwise-handled. The request error page runs after the CFML language
                          processor finishes. As a result, the request error page cannot include CFML tags, but can display error page
                          variables. A request error page is useful as a backup if errors occur in other error handlers.



Specifying a custom error page
You specify the custom error pages with the cferror tag. For Validation errors, the tag must be in the Application.cfc
initialization code or on the Application.cfm page. For Exception and Request errors, you can set the custom error
pages on each application page. However, because custom error pages generally apply to an entire application, it is
more efficient to place these cferror tags in the Application.cfc or Application.cfm file also. For more information
on using these pages, see "Designing and Optimizing a ColdFusion Application" on page 235

The cferror tag has the attributes listed in the following table:




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        284
Developing CFML Applications




 Attribute              Description

 Type                   The type of error that causes ColdFusion to display this page: Exception, Request, or Validation.

 Exception              Use only for the Exception type. The specific exception or exception category that causes the page to display. This
                        attribute can specify any of the types described in "About ColdFusion exceptions" on page 277.

 Template               The ColdFusion page to display.

 MailTo                 (Optional) An e-mail address. The cferror tag sets the error page error.mailTo variable to this value. The error
                        page can use the error.mailTo value in a message that tells the user to send an error notification. ColdFusion
                        does not send any message itself.


The following cferror tag specifies a custom error page for exceptions that occur in locking code and informs the
error page of the e-mail address to use to send a notification each time this type of error occurs:

<cferror type = "exception"
      exception = "lock"
      template = "../common/lockexcept.cfm"
      mailto = "server@mycompany.com">

For detailed information on the cferror tag, see the CFML Reference.


Creating an error application page
The following table lists the rules and considerations that apply to error application pages:


 Type                    Considerations

 Validation              · Cannot use CFML tags.

                         · Can use HTML tags.

                         · Can use the Error.InvalidFields, Error.validationHeader, and Error.validationFooter
                           variables by enclosing them with number signs (#).

                         · Cannot use any other CFML variables.

 Request                 · Cannot use CFML tags.

                         · Can use HTML tags.

                         · Can use nine CFML error variables, such as Error.Diagnostics, by enclosing them with number signs.

                         · Cannot use other CFML variables.

 Exception               · Can use full CFML syntax, including tags, functions, and variables.

                         · Can use nine standard CFML Error variables and cfcatch variables. Use either Error or cferror as the prefix
                           for both types of variables.

                         · Can use other application-defined CFML variables.

                         · To display any CFML variable, use the cfoutput tag.


The following table describes the variables available on error pages: Exception error pages can also use all of the
exception variables listed in the section "Exception information in cfcatch blocks" on page 289. To use these variables,
replace the cfcatch prefix with cferror or error. For example, to use the exception message in an error page, refer
to it as error.message.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    285
Developing CFML Applications




In general, production Exception and Request pages should not display detailed error information, such as that
supplied by the error.diagnostics variable. Typically, Exception pages e-mail detailed error information to an
administrative address or log the information using the cflog tag instead of displaying it to the user. For more
information on using the cflog tag, see "Logging errors with the cflog tag" on page 285.


Example of a request error page
The following example shows a custom error page for a request error:

 <html>
<head>
<title>Products - Error</title>
</head>
<body>


<h2>Sorry</h2>


<p>An error occurred when you requested this page.</p>
<p>Please send e-mail with the following information to #error.mailTo# to report
     this error.</p>


<table border=1>
<tr><td><b>Error Information</b> <br>
     Date and time: #error.DateTime# <br>
     Page: #error.template# <br>
     Remote Address: #error.remoteAddress# <br>
     HTTP Referer: #error.HTTPReferer#<br>
</td></tr></table>


<p>We apologize for the inconvenience and will work to correct the problem.</p>
</body>
</html>


Example of a validation error page
The following example shows a simple custom error page for a validation error:

 <html>
<head>
<title>Products - Error</title>
</head>
<body>


<h2>Data Entry Error</h2>


<p>You failed to correctly complete all the fields
in the form. The following problems occurred:</p>


#error.invalidFields#


</body>
</html>



Logging errors with the cflog tag
ColdFusion provides extensive capabilities for generating, managing, and viewing log files, as described in Configuring
and Administering ColdFusion. It also provides the cflog tag which adds entries to ColdFusion logs.



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   286
Developing CFML Applications




ColdFusion automatically logs errors to the default logs if you use the default error handlers. In all other cases, use the
cflog tag in your error handling code to generate log entries.

The cflog tag lets you specify the following information:

· A custom file or standard ColdFusion log file in which to write the message.

· Text to write to the log file. This can include the values of all available error and cfcatch variables.

· Message severity (type): Information Warning, Fatal, or Error.

· Whether to log any of the following: application name, thread ID, system date, or system time. By default, all get
   logged.

For example, you could use a cflog tag in an exception error-handling page to log the error information to an
application-specific log file, as in the following page:

<html>
<head>
<title>Products - Error</title>
</head>
<body>


<h2>Sorry</h2>


<p>An error occurred when you requested this page.
The error has been logged and we will work to correct the problem.
We apologize for the inconvenience. </p>


<cflog type="Error"
           file="myapp_errors"
           text="Exception error --
                Exception type: #error.type#
                Template: #error.template#,
                Remote Address: #error.remoteAddress#,
                HTTP Reference: #error.HTTPReferer#
                Diagnostics: #error.diagnostics#">


</body>
</html>


Reviewing the code
The following table describes the highlighted code and its function:


Code                                                            Description

<cflog type="Error"                                             When this page is processed, log an entry in the file myapp_errors.log
file="myapp_errors"                                             file in the ColdFusion log directory. Identify the entry as an error
text="Exception error --
                                                                message and include an error message that includes the exception
Exception type: #error.type#
                                                                type, the path of the page that caused the error, the remote address
Template: #error.template#,
Remote Address: #error.remoteAddress#,                          that called the page, and the error's diagnostic message.
HTTP Reference: #error.HTTPReferer#
Diagnostics: #error.diagnostics#">


A log file entry like the following is generated if you try to call a nonexistent custom tag and this page catches the error
(line breaks added for clarity):




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    287
Developing CFML Applications




"Error","web-13","12/19/01","11:29:07",MYAPP,"Exception error --
          Exception type: coldfusion.runtime.CfErrorWrapper
          Template: /MYStuff/MyDocs/exceptiontest.cfm,
          Remote Address: 127.0.0.1,
          HTTP Reference:
          Diagnostics: Cannot find CFML template for custom tag testCase. Cannot
          find CFML template for custom tag testCase. ColdFusion attempted looking
          in the tree of installed custom tags but did not find a custom tag with
          this name."

The text consists of a comma-delimited list of the following entries:

· Log entry type, specified by the cflogtype attribute

· ID of the thread that was executing

· Date the entry was written to the log

· Time the entry was written to the log

· Application name, as specified in the Application.cfc initialization code (by setting the This.application variable)
   or by a cfapplication tag (for example, in an Application.cfm file).

· The message specified by the cflogtext attribute.


Handling runtime exceptions with ColdFusion tags
Exceptions include any event that disrupts the normal flow of instructions in a ColdFusion page, such as failed
database operations, missing include files, or developer-specified events. Ordinarily, when ColdFusion encounters an
exception, it stops processing and displays an error message, or an error page specified by a cferror tag or the Site-
wide Error Handler option on the Settings page in the Administrator. However, you can use the ColdFusion exception
handling tags to catch and process runtime exceptions directly in ColdFusion pages.

This ability to handle exceptions directly in your application pages enables your application to do the following:

· Respond appropriately to specific errors within the context of the current application page

· Recover from errors whenever possible.


Exception-handling tags
ColdFusion provides the exception-handling tags listed in the following table:


Tag                      Description

cftry                    If any exceptions occur while processing the tag body, look for a cfcatch tag that handles the exception, and
                         execute the code in the cfcatch tag body.

cfcatch                  Execute code in the body of this tag if the exception caused by the code in the cftry tag body matches the
                         exception type specified in this tag's attributes.

                         Used in cftry tag bodies only.

cfthrow                  Generate a user-specified exception.

cfrethrow                Exit the current cfcatch block and generates a new exception of the same type.

                         Used only in cfcatch tag bodies.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   288
Developing CFML Applications




Using cftry and cfcatch tags
The cftry tag lets you go beyond reporting error data to the user:

· You can include code that recovers from errors so your application can continue processing without alerting the
   user.

· You can create customized error messages that apply to the specific code that causes the error.

For example, you can use cftry to catch errors in code that enters data from a user registration form to a database.
The cfcatch code could do the following:

1 Retry the query, so the operation succeeds if the resource was only temporarily unavailable.

2 If the retries fail:

   · Display a custom message to the user

   · Post the data to an e-mail address so the company staff can enter the data after the problem has been solved.

Code that accesses external resources such as databases, files, or LDAP servers where resource availability is not
guaranteed is a good candidate for using try/catch blocks.


Try/catch code structure
In order for your code to directly handle an exception, the tags in question must appear within a cftry block. It is a
good idea to enclose an entire application page in a cftry block. You then follow the cftry block with cfcatch
blocks, which respond to potential errors. When an exception occurs within the cftry block, processing is thrown to
the cfcatch block for that type of exception.

Here is an outline for using cftry and cfcatch to handle errors:

<cftry>
     Put your application code here ...
     <cfcatch type="exception type1">
          Add exception processing code here ...
     </cfcatch>
     <cfcatch type="exception type2">
          Add exception processing code here ...
     </cfcatch>
     ...
     <cfcatch type="Any">
          Add exception processing code appropriate for all other exceptions here ...
     </cfcatch>
</cftry>


Try/catch code rules and recommendations
Follow these rules and recommendations when you use cftry and cfcatch tags:

· The cfcatch tags must follow all other code in a cftry tag body.

· You can nest cftry blocks. For example, the following structure is valid:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  289
Developing CFML Applications




   <cftry>
        code that may cause an exception
        <cfcatch ...>
             <cftry>
                  First level of exception handling code
                  <cfcatch ...>
                       Second level of exception handling code
                  </cfcatch
             </cftry>
        </cfcatch>
  </cftry>

  If an exception occurs in the first level of exception-handling code, the inner cfcatch block can catch and handle
  it. (An exception in a cfcatch block cannot be handled by cfcatch blocks at the same level as that block.)

· ColdFusion always responds to the latest exception that gets raised. For example, if code in a cftry block causes
  an exception that gets handled by a cfcatch block, and the cfcatch block causes an exception that has no handler,
  ColdFusion displays the default error message for the exception in the cfcatch block, and you are not notified of
  the original exception.

· If an exception occurs when the current tag is nested inside other tags, the CFML processor checks the entire stack
  of open tags until it finds a suitable cftry/cfcatch combination or reaches the end of the stack.

· Use cftry with cfcatch to handle exceptions based on their point of origin within an application page, or based
  on diagnostic information.

· The entire cftry tag, including all its cfcatch tags, must be on a single ColdFusion page. You cannot place the
  <cftry> start tag on one page and have the </cftry> end tag on another page.

· For cases when a cfcatch block is not able to successfully handle an error, consider using the cfrethrow tag, as
  described in "Using the cfrethrow tag" on page 296.

· If an exception can be safely ignored, use a cfcatch tag with no body; for example:

   <cfcatch Type = Database />

· In problematic cases, enclose an exception-prone tag in a specialized combination of cftry and cfcatch tags to
  immediately isolate the tag's exceptions.


Exception information in cfcatch blocks
Within the body of a cfcatch tag, the active exception's properties are available in a cfcatch object. The object
contents are described as follows:


Standard cfcatch variables
The following table describes the variables that are available in most cfcatch blocks:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         290
Developing CFML Applications




 Property variable              Description

 cfcatch.Detail                 A detailed message from the CFML compiler. This message, which can contain HTML formatting, can help
                                to determine which tag threw the exception.

                                The cfcatch.Detail value is available in the CFScript catch statement as the exceptionVariable
                                parameter.

 cfcatch.ErrorCode              The cfthrow tag can supply a value for this code through the errorCode attribute. For
                                Type="Database", cfcatch.ErrorCode has the same value as cfcatch.SQLState.

                                Otherwise, the value of cfcatch.ErrorCode is the empty string.

 cfcatch.ExtendedInfo           Custom error message information. This is returned only to cfcatch tags for which the type attribute is
                                Application or a custom type.

                                Otherwise, the value of cfcatch.ExtendedInfo is the empty string.

 cfcatch.Message                The exception's default diagnostic message, if one was provided. If no diagnostic message is available, this
                                is an empty string.

                                The cfcatch.Message value is included in the value of the CFScript catch statement
                                exceptionVariable parameter.


 cfcatch.RootCause              The Java servlet exception reported by the JVM as the cause of the "root cause" of the exception.

 cfcatch.TagContext             An array of structures structure containing information for each tag in the tag stack The tag stack consists
                                of each tag that is currently open.

 cfcatch.Type                   The exception's type, returned as a string.


Note: If you use the cfdump tag to display the cfcatch variable, the display does not include variables that do not have
values.

The cfcatch.TagContext variable contains an array of tag information structures. Each structure represents one
level of the active tag context at the time when ColdFusion detected the exception. That is, there is one structure for
each tag that is open at the time of the exception. For example, if the exception occurs in a tag on a custom tag page,
the tag context displays information about the called custom tag and the tag in which the error occurs.

The structure at position 1 in the array represents the currently executing tag at the time the exception was detected.
The structure at position ArrayLen represents the initial tag in the stack of tags that were executing when the compiler
detected the exception.

The following table lists the tagContext structure attributes:


 Entry                                         Description

 Column                                        Obsolete (retained for backwards compatibility). Always 0.

 ID                                            The tag in which the exception occurred. Exceptions in CFScript are indicated by two
                                               question marks (??). All custom tags, including those called directly, are identified as
                                               cfmodule.

 Line                                          The line on the page in which the tag is located.

 Raw_Trace                                     The raw Java stack trace for the error.

 Template                                      The pathname of the application page that contains the tag.

 Type                                          The type of page; it is always a ColdFusion page.



Database exceptions
The following additional variables are available whenever the exception type is database:




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   291
Developing CFML Applications




 Property variable                        Description

 cfcatch.NativeErrorCode                  The native error code associated with this exception. Database drivers typically provide
                                          error codes to assist in the diagnosis of failing database operations. The values assumed by
                                          cfcatch.NativeErrorCode are driver-dependent.

                                          If no error code is provided, the value of cfcatch.nativeErrorCode is -1. The value is 0
                                          for queries of queries.

 cfcatch.SQLState                         The SQLState code associated with this exception. Database drivers typically provide error
                                          codes to assist in the diagnosis of failing database operations. SQLState codes are more
                                          consistent across database systems than native error codes.

                                          If the driver does not provide an SQLState value, the value of cfcatch.SQLState is -1.

 cfcatch.Sql                              The SQL statement sent to the data source.

 cfcatch.queryError                       The error message as reported by the database driver.

 cfcatch.where                            If the query uses the cfqueryparam tag, query parameter name-value pairs.



Expression exceptions
The following variable is only available for Expression exceptions:


 Property variable                        Description

 cfcatch.ErrNumber                        An internal expression error number, valid only when type="Expression".



Locking exceptions
The following additional information is available for exceptions related to errors that occur in cflock tags:


 Property variable                        Description

 cfcatch.lockName                         The name of the affected lock. This is set to "anonymous" if the lock name is unknown.

 cfcatch.lockOperation                    The operation that failed. This is set to "unknown" if the failed operation is unknown.



Missing include exceptions
The following additional variable is available if a missing file specified by a cfinclude tag causes the error.


 Property variable                        Description

 cfcatch.missingFileName                  The name of the missing file.



Using the cftry tag: an example
The following example shows the cftry and cfcatch tags. It uses the cfdocexamples data source, which many of the
examples listed here use, and a sample included file, includeme.cfm.

If an exception occurs when you run the cfquery statement, the application page flow switches to the
cfcatchtype="Database" exception handler. It then resumes with the next statement after the cftry block, once
the cfcatchtype="Database" handler completes. Similarly, the cfcatchtype="MissingInclude" block handles
exceptions raised by the cfinclude tag.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                       292
Developing CFML Applications




<!--- Wrap code you want to check in a cftry block --->
<cfset EmpID=3>
<cfparam name="errorCaught" default="">
<cftry>
    <cfquery name="test" datasource="cfdocexamples">
         SELECT Dept_ID, FirstName, LastName
         FROM Employee
         WHERE Emp_ID=#EmpID#
    </cfquery>


    <html>
    <head>
    <title>Test cftry/cfcatch</title>
    </head>
    <body>
    <cfinclude template="includeme.cfm">
    <cfoutput query="test">
         <p>Department: #Dept_ID#<br>
         Last Name: #LastName#<br>
         First Name: #FirstName#</p>
    </cfoutput>


<!--- Use cfcatch to test for missing included files. --->
<!--- Print Message and Detail error messages. --->
<!--- Block executes only if a MissingInclude exception is thrown. --->
    <cfcatch type="MissingInclude">
         <h1>Missing Include File</h1>
         <cfoutput>
         <ul>
              <li><b>Message:</b> #cfcatch.Message#
              <li><b>Detail:</b> #cfcatch.Detail#
              <li><b>Filename:</b> #cfcatch.MissingFileName#
         </ul>
         </cfoutput>
         <cfset errorCaught = "MissingInclude">
    </cfcatch>


<!--- Use cfcatch to test for database errors.--->
<!--- Print error messages. --->
<!--- Block executes only if a Database exception is thrown. --->
    <cfcatch type="Database">
         <h1>Database Error</h1>
         <cfoutput>
         <ul>
              <li><b>Message:</b> #cfcatch.Message#
              <li><b>Native error code:</b> #cfcatch.NativeErrorCode#
              <li><b>SQLState:</b> #cfcatch.SQLState#
              <li><b>Detail:</b> #cfcatch.Detail#
              </ul>




                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                              293
Developing CFML Applications




          </cfoutput>
          <cfset errorCaught = "Database">
    </cfcatch>


<!--- Use cfcatch with type="Any" --->
<!--- to find unexpected exceptions. --->
    <cfcatch type="Any">
          <cfoutput>
               <hr>
               <h1>Other Error: #cfcatch.Type#</h1>
               <ul>
                     <li><b>Message:</b> #cfcatch.Message#
                     <li><b>Detail:</b> #cfcatch.Detail#
               </ul>
          </cfoutput>
          <cfset errorCaught = "General Exception">
    </cfcatch>
</body>
</html>
</cftry>

Use the following procedure to test the code.


Test the code
1 Make sure that there is no includeme.cfm file and display the page. The cfcatch type="MissingInclude" block
  displays the error.

2 Create a nonempty includeme.cfm file and display the page. If your database is configured properly, you see an
  employee entry and do not get any error.

3 In the cfquery tag, change the line:

   FROM Employee

  to:

   FROM Employer

  Display the page. This time the cfcatch type="Database" block displays an error message.

4 Change Employer to Employee.

  Change the cfoutput line:

   <p>Department: #Dept_ID#<br>

  to:

   <p>Department: #DepartmentID#<br>

  Display the page. This time the cfcatch type="Any" block displays an error message indicating an expression
  error.

5 Change DepartmentID back to Dept_ID and redisplay the page. The page displays properly.

  Open \CFusion\Log\MyAppPage.log in your text editor. You should see a header line, an initialization line, and
  four detail lines, like the following:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        294
Developing CFML Applications




   "Severity","ThreadID","Date","Time","Application","Message"
  "Information","web-0","11/20/01", "16:27:08",, "cf_root\runtime\servers\default\logs\
  MyAppPage.log initialized"
  "Information","web-0","11/20/01","16:27:08",,
        "Page: web_root/MYStuff/MyDocs/ cftryexample.cfm Error: MissingInclude"
  "Information","web-1","11/20/01","16:27:32",,"
        Page: web_root/MYStuff/MyDocs/ cftryexample.cfm Error: "
  "Information","web-0","11/20/01","16:27:49",,
        "Page: web_root/MYStuff/MyDocs/ cftryexample.cfm Error: Database"
  "Information","web-1","11/20/01","16:28:21",,
        "Page: web_root/MYStuff/MyDocs/ cftryexample.cfm Error: General Exception"
  "Information","web-0","11/20/01","16:28:49",,
        "Page: web_root/MYStuff/MyDocs/ cftryexample.cfm Error: "


Reviewing the code
The following table describes the code:


Code                                            Description

<cfset EmpID=3>                                 Initializes the employee ID to a valid value. An application would get the
<cfparam name="errorCaught" default="">         value from a form or other source.

                                                Sets the default errorCaught variable value to the empty string (to
                                                indicate no error was caught).

                                                There is no need to put these lines in a cftry block.

<cftry>                                         Starts the cftry block. Exceptions from here to the end of the block can be
<cfquery name="test"                            caught by cfcatch tags.
datasource="cfdocexamples">
SELECT Dept_ID, FirstName, LastName             Queries the cfdocexamples database to get the data for the employee
FROM Employee                                   identified by the EmpID variable.
WHERE Emp_ID=#EmpID#
</cfquery>

<html>                                          Begins the HTML page. This section contains all the code that displays
<head>                                          information if no errors occur.
<title>Test cftry/cfcatch</title>
</head>                                         Includes the includeme.cfm page.
<body>
<cfinclude template="includeme.cfm">            Displays the user information record from the test query.
<cfoutput query="test">
<p>Department: #Dept_ID#<br>
Last Name: #LastName#<br>
First Name: #FirstName#</p>
</cfoutput>

<cfcatch type="MissingInclude">                 Handles exceptions thrown when a page specified by the cfinclude tag
<h1>Missing Include File</h1>                   cannot be found.
<cfoutput>
<ul>                                            Displays cfcatch variables, including the ColdFusion basic error message,
<li><b>Message:</b> #cfcatch.Message#           detail message, and the name of the file that could not be found.
<li><b>Detail:</b> #cfcatch.Detail#
<li><b>Filename:</b> #cfcatch.MissingFileName#  Sets the errorCaught variable to indicate the error type.
</ul>
</cfoutput>
<cfset errorCaught = "MissingInclude">
</cfcatch>




                                         

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                            295
Developing CFML Applications




Code                                                            Description

<cfcatch type="Database">                                       Handles exceptions thrown when accessing a database.
 <h1>Database Error</h1>
 <cfoutput>                                                     Displays cfcatch variables, including the ColdFusion basic error message,
 <ul>                                                           the error code and SQL state reported by the databases system, and the
 <li><b>Message:</b> #cfcatch.Message#                          detailed error message.
 <li><b>Native error code:</b>
#cfcatch.NativeErrorCode#                                       Sets the errorCaught variable to indicate the error type.
 <li><b>SQLState:</b> #cfcatch.SQLState#
 <li><b>Detail:</b> #cfcatch.Detail#
 </ul>
 </cfoutput>
 <cfset errorCaught = "Database">
 </cfcatch>

<cfcatch type="Any">                                            Handles any other exceptions generated in the cftry block.
 <cfoutput>
 <hr>                                                           Since the error can occur after information has displayed (in this case, the
 <h1>Other Error: #cfcatch.Type#</h1>                           contents of the include file), draws a line before writing the message text.
 <ul>
 <li><b>Message:</b> #cfcatch.Message#                          Displays the ColdFusion basic and detailed error message.
 <li><b>Detail:</b> #cfcatch.Detail#
 </ul>                                                          Sets the errorCaught variable to indicate the error type.
 </cfoutput>
 <cfset errorCaught = "General Exception">
 </cfcatch>

</body>                                                         Ends the HTML page, then the cftry block.
 </html>
 </cftry>



Using the cfthrow tag
You can use the cfthrow tag to raise your own, custom exceptions. When you use the cfthrow tag, you specify any or
all of the following information:


Attribute                Meaning

type                     The type of error. It can be a custom type that has meaning only to your application, such as InvalidProductCode.
                         You can also specify Application, the default type. You cannot use any of the predefined ColdFusion error types,
                         such as Database or MissingTemplate.

message                  A brief text message indicating the error.

detail                   A more detailed text message describing the error.

errorCode                An error code that is meaningful to the application. This field is useful if the application uses numeric error codes.

extendedInfo             Any additional information of use to the application.


All of these values are optional. You access the attribute values in cfcatch blocks and Exception type error pages by
prefixing the attribute with either cfcatch or error, as in cfcatch.extendedInfo. The default ColdFusion error
handler displays the message and detail values in the Message pane and the remaining values in the Error Diagnostic
Information pane.


Catching and displaying thrown errors
The cfcatch tag catches a custom exception when you use any of the following values for the cfcatchtype attribute:

· The custom exception type specified in the cfthrow tag.

· A custom exception type that hierarchically matches the initial portion of the type specified in the cfthrow tag. For
    more information, see the next section, Custom error type name hierarchy.

· Application, which matches an exception that is thrown with the Applicationtype attribute or with no type
    attribute.



                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      296
Developing CFML Applications




· Any, which matches any exception that is not caught by a more specific cfcatch tag.

Similarly, if you specify any of these types in a cferror tag, the specified error page displays information about the
thrown error.

Because the cfthrow tag generates an exception, a Request error handler or the Site-wide error handler can also
display these errors.


Custom error type name hierarchy
You can name custom exception types using a method that is similar to Java class naming conventions: domain name
in reverse order, followed by project identifiers, as in the following example:

 <cfthrow
     type="com.myCompany.myApp.Invalid_field.codeValue"
     errorcode="Dodge14B">

This fully qualified naming method is not required; you can use shorter naming rules, for example,
myApp.Invalid_field.codeValue, or even codeValue.

This naming method is not just a convention; ColdFusion uses the naming hierarchy to select from a possible
hierarchy of error handlers. For example, assume that you use the following cfthrow statement:

 <cfthrow type="MyApp.BusinessRuleException.InvalidAccount">

Any of the following cfcatch error handlers would handle this error:

 <cfcatch type="MyApp.BusinessRuleException.InvalidAccount">
<cfcatch type="MyApp.BusinessRuleException">
<cfcatch type="MyApp">

The handler that most exactly matches handles the error. In this case, the
MyApp.BusinessRuleException.InvalidAccount handler runs. However, if you used the following cfthrow tag:

 <cfthrow type="MyApp.BusinessRuleException.InvalidVendorCode

the MyApp.BusinessRuleException handler receives the error.

The type comparison is not case sensitive.


When to use the cfthrow tag
Use the cfthrow tag when your application can identify and handle application-specific errors. One typical use for the
cfthrow tag is in implementing custom data validation. The cfthrow tag is also useful for throwing errors from a
custom tag page to the calling page.

For example, on a form action page or custom tag used to set a password, the application can determine whether the
password entered is a minimum length, or contains both letters and number, and throw an error with a message that
indicates the password rule that was broken. The cfcatch block handles the error and tells the user how to correct the
problem.


Using the cfrethrow tag
The cfrethrow tag lets you create a hierarchy of error handlers. It tells ColdFusion to exit the current cfcatch block
and "rethrow" the exception to the next level of error handler. Thus, if an error handler designed for a specific type of
error cannot handle the error, it can rethrow the error to a more general-purpose error handler. The cfrethrow tag
can only be used in a cfcatch tag body.


The cfrethrow tag syntax
The following pseudocode shows how you can use the cfrethrow tag to create an error-handling hierarchy:



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 297
Developing CFML Applications




<cftry>
    <cftry>
          Code that might throw a database error
          <cfcatch Type="Database">
                <cfif Error is of type I can Handle>
                     Handle it
                <cfelse>
                     <cfrethrow>
                </cfif
          </cfcatch>
    </cftry>
    <cfcatch Type="Any">
          General Error Handling code
    </cfcatch>
</cftry>

Although this example uses a Database error as an example, you can use any cfcatch type attribute in the innermost
error type.

Follow these rules when you use the cfrethrow tag:

· Nest cftry tags, with one tag for each level of error handling hierarchy. Each level contains the cfcatch tags for
   that level of error granularity.

· Place the most general error catching code in the outermost cftry block.

· Place the most specific error catching code in the innermost cftry block.

· Place the code that can cause an exception error at the top of the innermost cftry block.

· End each cfcatch block except those in the outermost cftry block with a cfrethrow tag.


Example: using nested tags, cfthrow, and cfrethrow
The following example shows many of the discussed techniques including nested cftry blocks and the cfthrow and
cfrethrow tags. The example includes a simple calling page and a custom tag page:

· The calling page does little more than call the custom tag with a single attribute, a name to be looked up in a
   database. It does show, however, how a calling page can handle an exception thrown by the custom tag.

· The custom tag finds all records in the cfdocexamples database with a matching last name, and returns the results
   in a Caller variable. If it fails to connect with the main database, it tries a backup database.


The calling page
The calling page represents a section from a larger application page. To keep things simple, the example hard-codes
the name to be looked up.

<cftry>
    <cf_getEmps EmpName="Jones">
    <cfcatch type="myApp.getUser.noEmpName">
          <h2>Oops</h2>
          <cfoutput>#cfcatch.Message#</cfoutput><br>
    </cfcatch>
</cftry>
<cfif isdefined("getEmpsResult")>
    <cfdump var="#getEmpsResult#">
</cfif>




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 298
Developing CFML Applications




Reviewing the code
The following table describes the code:


Code                                                              Description

<cftry>                                                           In a cftry block, calls the cf_getEmps custom tag
 <cf_getEmps EmpName="Jones">                                     (getEmps.cfm).

<cfcatch type="myApp.getUser.noEmpName">                          If the tag throws an exception indicating that it did not receive
<h2>Oops</h2>                                                     a valid attribute, catches the exception and displays a message,
<cfoutput>#cfcatch.Message#</cfoutput><br>
                                                                  including the message variable set by the cfthrow tag in the
</cfcatch>
                                                                  custom tag.

<cfif isdefined("getEmpsResult")>                                 If the tag returns a result, uses the cfdump tag to display it. (A
 <cfdump var="#getEmpsResult#">                                   production application would not use the cfdump tag.)
 </cfif>



The custom tag page
The custom tag page searches for the name in the database and returns any matching records in a getEmpsResult
variable in the calling page. It includes several nested cftry blocks to handle error conditions. For a full description,
see Reviewing the code section, following the example:

Save the following code as getEmps.cfm in the same directory as the calling page.

<!--- If the tag didn't pass an attribute, throw an error to be handled by
                the calling page --->
<cfif NOT IsDefined("attributes.EmpName")>
     <cfthrow Type="myApp.getUser.noEmpName"
          message = "Last Name was not supplied to the cf_getEmps tag.">
     <cfexit method = "exittag">
<!--- Have a name to look up --->
<cfelse>
<!--- Outermost Try Block --->
     <cftry>


<!--- Inner Try Block --->
          <cftry>
<!--- Try to query the main database and set a caller variable to the result --->
                <cfquery Name = "getUser" DataSource="cfdocexamples">
                     SELECT *
                     FROM Employee
                     WHERE LastName = '#attributes.EmpName#'
                </cfquery>
                <cfset caller.getEmpsResult = getuser>
<!--- If the query failed with a database error, check the error type
                to see if the database was found --->
                <cfcatch type= "Database">
                     <cfif (cfcatch.SQLState IS "S100") OR (cfcatch.SQLState IS
                          "IM002")>


<!--- If the database wasn't found, try the backup database --->
<!--- Use a third-level Try block --->
                          <cftry>
                                 <cfquery Name = "getUser" DataSource="cfdocexamplesBackup">
                                     SELECT *
                                     FROM Employee
                                     WHERE LastName = '#attributes.EmpName#'
                                 </cfquery>




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                               299
Developing CFML Applications




                                   <cfset caller.getEmpsResult = getuser>


<!--- If still get a database error, just return to the calling page
              without setting the caller variable. There is no cfcatch body.
              This might not be appropriate in some cases.
              The Calling page ends up handling this case as if a match was not
              found --->
                                   <cfcatch type = "Database" />
<!--- Still in innermost try block. Rethrow any other errors to the next
              try block level --->
                              <cfcatch type = "Any">
                                   <cfrethrow>
                              </cfcatch>
                          </cftry>


<!--- Now in second level try block.
              Throw all other types of Database exceptions to the next try
                    block level --->
                    <cfelse>
                          <cfrethrow>
                    </cfif>
              </cfcatch>
<!--- Throw all other exceptions to the next try block level --->
              <cfcatch type = "Any">
                          <cfrethrow>
              </cfcatch>
         </cftry>


<!--- Now in Outermost try block.
           Handle all unhandled exceptions, including rethrown exceptions, by
              displaying a message and exiting to the calling page.--->
         <cfcatch Type = "Any">
              <h2>Sorry</h2>
              <p>An unexpected error happened in processing your user inquiry.
                    Please report the following to technical support:</p>
              <cfoutput>
                    Type: #cfcatch.Type#
                    Message: #cfcatch.Message#
              </cfoutput>
              <cfexit method = "exittag">
         </cfcatch>
    </cftry>
</cfif>


Reviewing the code
The following table describes the code:




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        300
Developing CFML Applications




Code                                                    Description

<cfif NOT IsDefined("attributes.EmpName")>              Makes sure the calling page specified an EmpName attribute. If
<cfthrow Type="myApp.getUser.noEmpName"                 not, throws a custom error that indicates the problem and exits
message = "Last Name was not supplied to the cf_getEmps
                                                        the tag. The calling page handles the thrown error.
tag.">
<cfexit method = "exittag">

<cfelse>                                                If the tag has an EmpName attribute, does the remaining work
<cftry>                                                 inside an outermost try block. The cfcatch block at its end
                                                        handles any otherwise-uncaught exceptions.

<cftry>                                                 Starts a second nested try block. This block catches exceptions
<!--- Try to query the main database and set a caller   in the database query.
variable to the result --->
<cfquery Name = "getUser" DataSource="cfdocexamples">   If there are no exceptions, sets the calling page's
SELECT *                                                getEmpsResult variable with the query results.
FROM Employee
WHERE LastName = '#attributes.EmpName#'
</cfquery>
<cfset caller.getEmpsResult = getuser>

<cfcatch type= "Database">                              If the query threw a Database error, checks to see if the error was
<cfif (cfcatch.SQLState IS "S100") OR (cfcatch.SQLState caused by an inability to access the database (indicated by an
IS "IM002")>
                                                        SQLState variable value of S100 or IM002).
 <cftry>
<cfquery Name = "getUser"                               If the database was not found, starts a third nested try block and
DataSource="cfdocexamplesBackup">
                                                        tries accessing the backup database. This try block catches
SELECT *
FROM Employee                                           exceptions in this second database access.
WHERE LastName = '#attributes.EmpName#'
                                                        If the database inquiry succeeds, sets the calling page's
</cfquery>
<cfset caller.getEmpsResult = getuser>                  getEmpsResult variable with the query results.


<cfcatch type = "Database" />                           If the second database query failed with a database error, gives
                                                        up silently. Because the Database type cfcatch tag does not
                                                        have a body, the tag exits. The calling page does not get a
                                                        getEmpsResult variable. It cannot tell whether the database
                                                        had no match or an unrecoverable database error occurred, but
                                                        it does know that no match was found.

<cfcatch type = "Any">                                  If the second database query failed for any other reason, throws
<cfrethrow>                                             the error up to the next try block.
</cfcatch>
</cftry>                                                Ends the innermost try block

<cfelse>                                                In the second try block, handles the case in which the first
<cfrethrow>                                             database query failed for a reason other than a failure to find the
</cfif>
                                                        database.
</cfcatch>

                                                        Rethrows the error up to the next level, the outermost try block.

<cfcatch type = "Any">                                  In the second try block, catches any errors other exceptions and
<cfrethrow>                                             rethrows them up to the outermost try block.
</cfcatch>
</cftry>                                                Ends the second try block.

<cfcatch Type = "Any">                                  In the outermost try block, handles any exceptions by
<h2>Sorry</h2>                                          displaying an error message that includes the exception type
<p>An unexpected error happened in processing your user
                                                        and the exception's error message. Because there was no code
inquiry. Please report the following to technical
                                                        to try that is not also in a nested try block, this cfcatch tag
support:</p>
<cfoutput>                                              handles only errors that are rethrown from the nested blocks.
Type: #cfcatch.Type#
Message: #cfcatch.Message#                              Exits the custom tag and returns to the calling page.
</cfoutput>
                                                        Ends the catch block, try block, and initial cfif block.
<cfexit method = "exittag">
</cfcatch>
</cftry>
</cfif>




                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      301
Developing CFML Applications




Testing the code
To test the various ways errors can occur and be handled in this example, try the following:

· In the calling page, change the attribute name to any other value; for example, My Attrib. Then change it back.

· In the first cfquery tag, change the data source name to an invalid data source; for example, NoDatabase.

· With an invalid first data source name, change the data source in the second cfquery tag to cfdocexamples.

· Insert cfthrow tags throwing custom exculpations in various places in the code and observe the effects.



Using Persistent Data and Locking

Adobe ColdFusion provides several variable scopes in which data persists past the life of a single request. These are the
Client, Application, Session, and Server scopes. These scopes let you save data over time and share data between pages
and even applications. Use these scopes as persistent scopes. In particular, use the Client and Session scopes to
maintain information about a user across multiple requests.

ColdFusion lets you lock access to sections of code to ensure that ColdFusion does not attempt to run the code, or
access the data that it uses, simultaneously or in an unpredictable order. This locking feature is important for ensuring
the consistency of all shared data, including data in external sources in addition to data in persistent scopes.

You can use persistent scopes to develop an application and use locking to ensure data consistency.


About persistent scope variables
ColdFusion provides four variable scopes, described in the following table, that let you maintain data that must be
available to multiple applications or users or must last beyond the scope of the current request.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                            302
Developing CFML Applications




 Variable scope          Description

 Client                  Contains variables that are available for a single client browser over multiple browser sessions in an application.
                         For information about browser sessions, see, "What is a session?" on page 311.

                         Useful for client-specific information, such as client preferences, that you want to store for a significant period of
                         time.

                         Data is stored as cookies, database entries, or Registry values. Client variables can time out after an extended
                         period.

                         Although do not have to use the Client scope prefix in the variable name, code that uses the prefix is more
                         efficient and easier to maintain.

 Session                 Contains variables that are available for a single client browser for a single browser session in an application.

                         Useful for client-specific information, such as shopping cart contents, that you want to persist while the client is
                         visiting your application.

                         Data is stored in memory and times out after a period of inactivity or when the server shuts down.

                         ColdFusion Administrator lets you select between two kinds of session management, Standard ColdFusion
                         Session management and J2EE session management. For information about types of session management, see
                         "ColdFusion and J2EE session management" on page 312.

                         Use the Session scope prefix in the variable name.

 Application             Contains variables that are available to all pages in an application for all clients.

                         Useful for application-specific information, such as contact information, that can vary over time and should be
                         stored in a variable.

                         Data is stored in memory and times out after a period of inactivity or when the server shuts down.

                         Use the Application scope prefix in the variable name.

 Server                  Contains variables that are available to all applications in a server and all clients.

                         Useful for information that applies to all pages on the server, such as an aggregate page-hit counter.

                         Data is stored in memory. The variables do not time out, but you can delete variables you create, and all server
                         variables are automatically deleted when the server stops running.

                         Use the Server scope prefix in the variable name.


The following sections provide information that is common to all or several of these variables. Later sections describe
how to use the Client, Session, Application, and Server scopes in your applications, and provide detailed information
about locking code.


ColdFusion persistent variables and ColdFusion structures
All persistent scopes are available as ColdFusion structures. As a result, you can use ColdFusion structure functions to
access and manipulate Client, Session, Application, and Server scope contents. Information about using these
functions in detail is not covered, but information about features or limitations that apply to specific scopes is
provided.

Note: Although you can use the StructClear function to clear your data from the Server scope, the function does not
delete the names of the variables, only their values, and it does not delete the contents of the Server.os and
Server.ColdFusion structures. Using the StructClear function to clear the Session, or Application scope clears the entire
scope, including the built-in variables. Using the StructClear function to clear the Client scope clears the variables from
the server memory, but does not delete the stored copies of the variables.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                             303
Developing CFML Applications




ColdFusion persistent variable issues
Variables in the Session, Application, and Server scopes are kept in ColdFusion server memory. This storage method
has several implications:

· All variables in these scopes are lost if the server stops running.

· Variables in these scopes are not shared by servers in a cluster.

· To prevent race conditions and ensure data consistency, lock access to all code that changes variables in these
   scopes or reads variables in these scopes with values that can change.

Note: If you use J2EE session management and configure the J2EE server to retain session data between server restarts,
ColdFusion retains session variables between server restarts.

Additionally, be careful when using client variables in a server cluster, where an application can run on multiple
servers.


Locking memory variables
Because ColdFusion is a multi-threaded system in which multiple requests can share Session, Application, and Server
scope variables, it is possible for two or more requests to try to access and modify data at the same time. ColdFusion
runs in a J2EE environment, which prevents simultaneous data access, so multiple requests do not cause severe system
errors. However, such requests can result in inconsistent data values, particularly when a page changes more than one
variable.

To prevent data errors with session, application, and server variables, lock code that writes and reads data in these
scopes. For more information, see "Locking code with cflock" on page 318.


Using variables in clustered systems
Because memory variables are stored in memory, they are not available to all servers in a cluster. As a result, you
generally do not use Session, Application, or Server scope variables in clustered environment. However, use these
scope variables in a clustered system in the following circumstances:

· If the clustering system supports "sticky" sessions, in which the clustering system ensures that each user session
   remains on a single server. In this case, you can use session variables as you would on a single server.

· You can use Application and Server scope variables in a cluster for write-once variables that are consistently set, for
   example, from a database.

To use client variables on a clustered system, store the variables as cookies or in a database that is available to all servers.
If you use database storage, on one server only, select the Purge Data for Clients that Remain Unvisited option on the
Client Variables, Add/Edit Client Store page in the Server Settings area in the ColdFusion Administrator.


Managing the client state
Because the web is a stateless system, each connection that a browser makes to a web server is unique to the web server.
However, many applications must keep track of users as they move through the pages within the application. This is
the definition of client state management.

ColdFusion provides tools to maintain the client state by seamlessly tracking variables associated with a browser as the
user moves from page to page within the application. You can use these variables in place of other methods for tracking
client state, such as URL parameters, hidden form fields, and HTTP cookies.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                              304
Developing CFML Applications




About client and session variables
ColdFusion provides two tools for managing the client state: client variables and session variables. Both types of
variables are associated with a specific client, but you manage and use them differently, as described in the following
table:


 Variable type           Description

 Client                  Data is saved as cookies, database entries, or Registry entries. Data is saved between server restarts, but is initially
                         accessed and saved more slowly than data stored in memory.

                         Each type of data storage has its own time-out period. You can specify the database and Registry data time-outs
                         in the ColdFusion Administrator. ColdFusion sets Cookie client variables to expire after approximately ten years.

                         Data is stored on a per-user and per-application basis. For example, if you store client variables as cookies, the user
                         has a separate cookie for each ColdFusion application provided by a server.

                         Client variables must be simple variables, such as numbers, dates, or strings. They cannot be arrays, structures,
                         query objects, or other objects.

                         Client variable names can include periods. For example, My.ClientVar is a valid name for a simple client variable.
                         Avoid such names, however, to ensure code clarity.

                         You do not have to prefix client variables with the scope name when you reference them, However, if you do not
                         use the Client prefix, you might unintentionally refer to a variable with the same name in another scope. Using
                         the prefix also optimizes performance and increases program clarity.

                         You do not lock code that uses client variables.

                         You can use client variables that are stored in cookies or a common database in clustered systems.

 Session                 Data is stored in memory so it is accessed quickly.

                         Data is lost when the client browser is inactive for a time-out period. You specify the time-out in the ColdFusion
                         Administrator, the Application.cfc initialization code, or Application.cfm.

                         As with client variables, data is available to a single client and application only.

                         Variables can store any ColdFusion data type.

                         You must prefix all variable names with the Session scope name.

                         Lock code that uses session variables to prevent race conditions.

                         You can use session variables in clustered systems only if the systems support "sticky" sessions, where a session is
                         limited to a single server.


Session variables are normally better than client variables for values that need to exist for only a single browser session.
Reserve client variables for client-specific data, such as client preferences that you want available for multiple browser
sessions.


Maintaining client identity
Because the web is a stateless system, client management requires some method for maintaining knowledge of the
client between requests. Normally you do this using cookies, but you can also do it by passing information between
application pages. Information about how ColdFusion maintains client identity in a variety of configurations and
environments, and the issues that can arise with client state management are described as follows:


About client identifiers
To use client and session variables, ColdFusion must be able to identify the client. It normally does so by setting the
following two cookie values on the client's system:

· CFID: A sequential client identifier

· CFToken: A random-number client security token




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          305
Developing CFML Applications




These cookies uniquely identify the client to ColdFusion, which also maintains copies of the variables as part of the
Session and Client scopes. You can configure your application so that it does not use client cookies, but in this case,
pass these variables to all the pages that your application calls. For more information about maintaining client and
session information without using cookies, see "Using client and session variables without cookies" on page 305.

You can configure ColdFusion to use J2EE servlet session management instead of ColdFusion session management
for session variables. This method of session management does not use CFID and CFToken values, but does use a client-
side jsessionid session management cookie. For more information on using J2EE session management, see
"ColdFusion and J2EE session management" on page 312.


Using client and session variables without cookies
Often, users disable cookies in their browsers. In this case, ColdFusion cannot maintain the client state automatically.
You can use client or session variables without using cookies, by passing the client identification information between
application pages. However, this technique has significant limitations, as follows:

1 Client variables are effectively the same as session variables, except that they leave unusable data in the client data
   store.

   Because the client's system does not retain any identification information, the next time the user logs on,
   ColdFusion cannot identify the user with the previous client and must create a new client ID for the user. Any
   information about the user from a previous session is not available, but remains in client data storage until
   ColdFusion deletes it. If you clear the Purge Data for Clients that Remain Unvisited option in the ColdFusion
   Administrator, ColdFusion never deletes this data.

   Therefore, do not use client variables, if you allow users to disable cookies. To retain client information without
   cookies, require users to login with a unique ID. You can then save user-specific information in a database with the
   user's ID as a key.

2 ColdFusion creates a new session each time the user requests a page directly in the browser, because the new request
   contains no state information to indicate the session or client.

Note: You can prevent ColdFusion from sending client information to the browser as cookies by setting
This.setClientCookies variable in Application.cfc or the setClientCookies attribute of the cfapplication tag to No.

To use ColdFusion session variables without using cookies, each page must pass the CFID and CFToken values to any
page that it calls as part of the request URL. If a page contains any HTML hrefa= links, cflocation tags, form tags,
or cfform tags the tags must pass the CFID and CFToken values in the tag URL. To use J2EE session management, pass
the jsessionid value in page requests. To use ColdFusion client variables and J2EE session variables, pass the CFID,
CFToken, and jsessionid values in URLs.

Note: The behavior is as follows when CFID and CFTOKEN are provided in the URL: If session exists, the CFID and
CFTOKEN from the URL are ignored. If the session does not exist, CFID and CFTOKEN from the URL are used to
validate the session and the session is used if it is valid. If the session is not valid, a new session is created. CFID and
CFTOKEN are regenerated.

ColdFusion provides the URLSessionFormat function, which does the following:

· If the client does not accept cookies, automatically appends all required client identification information to a URL.

· If the client accepts cookies, does not append the information.

The URLSessionFormat function automatically determines which identifiers are required, and sends only the
required information. It also provides a more secure and robust method for supporting client identification than
manually encoding the information in each URL, because it only sends the information that is required, when it is
required, and it is easier to code.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        306
Developing CFML Applications




To use the URLSessionFormat function, enclose the request URL in the function. For example, the following cfform
tag posts a request to another page and sends the client identification, if necessary:

<cfform method="Post" action="#URLSessionFormat("MyActionPage.cfm")#>

    If you use the same page URL in multiple URLSessionFormat functions, you can gain a small performance
    improvement and simplify your code if you assign the formatted page URL to a variable, for example:

<cfset myEncodedURL=URLSessionFormat(MyActionPage.cfm)>
<cfform method="Post" action="#myEncodedURL#">


Client identifiers and security
The following client identifier issues can have security implications:

· Ensuring the uniqueness and complexity of the CFToken identifier

· Limiting the availability of Session identifiers

The next sections discuss these issues.


Ensuring CFToken uniqueness and security
By default, ColdFusion uses an eight-digit random number in the CFToken identifier. This CFToken format provides
a unique, secure identifier for users under most circumstances. (In ColdFusion, the method for generating this number
uses a cryptographic-strength random number generator that is seeded only when the server starts.)

However, in the ColdFusion Administrator, you can enable the Settings page to produce a more complex CFToken
identifier. If you enable the Use UUID for cftoken option, ColdFusion creates the CFToken value by prepending a 16-
digit random hexadecimal number to a ColdFusion UUID. The resulting CFToken identifier looks similar to the
following:

3ee6c307a7278c7b-5278BEA6-1030-C351-3E33390F2EAD02B9



Providing Session security
ColdFusion uses the same client identifiers for the Client scope and the standard Session scope. Because the CFToken
and CFID values are used to identify a client over a period of time, they are normally saved as cookies on the user's
browser. These cookies persist until the client's browser deletes them, which can be a considerable length of time. As
a result, hackers could have more access to these variables than if ColdFusion used different user identifiers for each
session.

A hacker who has the user's CFToken and CFID cookies could gain access to user data by accessing a web page during
the user's session using the stolen CFToken and CFID cookies. While this scenario is unlikely, it is theoretically possible.

You can remove this vulnerability by selecting the Use J2EE Session Variables option on the ColdFusion
Administrator Memory Variables page. The J2EE session management mechanism creates a new session identifier for
each session, and does not use either the CFToken or the CFID cookie value.


Security-related changes
The following security-related specifications apply when you upgrade to ColdFusion 9 Upgrade 1:

· CFID, CFTOKEN, and jsessionid are marked httpOnly. This reduces the chance of session information being
   compromised on Cross Site Scripting (XSS) attack.

· Set the following system property for the session cookies to be httpOnly:
   Dcoldfusion.sessioncookie.httponly=true




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       307
Developing CFML Applications




· The support for session cookies to be httpOnly depends on the application server you use:

    · For Tomcat/JBoss, httpOnly is not supported for JSESSIONID

    · On JRun, add the system property in jvm.config file

    · For other application servers, see the relevant documentation for details on httpOnly support for session
       cookies.


Configuring and using client variables
Use client variables for data that is associated with a particular client and application and that must be saved between
user sessions. Use client variables for long-term information such as user display or content preferences.


Enabling client variables
To enable client variables, you specify This.clientmanagement="True" in the Application.cfc initialization code, or set
the cfapplication tag clientmanagement attribute to Yes in the Application.cfm file. For example, to enable client
variables in an application named SearchApp, you can use the following line in the application's Application.cfm page:

<cfapplication NAME="SearchApp" clientmanagement="Yes">


Choosing a client variable storage method
By default, ColdFusion stores client variables in the Registry. In most cases, however, it is more appropriate to store
the information as client cookies or in a SQL database.

The ColdFusion Administrator Client Variables page controls the default client variable location. You can override the
default location by specifying a This.clientStorage value in Application.cfc or by setting the clientStorage attribute
in the cfapplication tag.

You can specify the following values for the client storage method:

· Registry (default). Client variables are stored under the key HKEY_LOCAL_MACHINE\
   SOFTWARE\Macromedia\ColdFusion\CurrentVersion\Clients.

· Name of a data source configured in ColdFusion Administrator

· Cookie

Generally, it is most efficient to store client variables in a database. Although the Registry option is the default, the
Registry has significant limitations for client data storage. The Registry cannot be used in clustered systems and its use
for client variables on UNIX is not supported in ColdFusion.


Using cookie storage
When you set the client storage method to Cookie, the cookie that ColdFusion creates has the application's name.
Storing client data in a cookie is scalable to large numbers of clients, but this storage mechanism has some limitations.
In particular, if the client turns off cookies in the browser, client variables do not work.

Consider the following additional limitations before implementing cookie storage for client variables:

· Any Client variable that you set after a cfflush tag is not sent to the browser, so the variable value does not get
   saved.

· Some browsers allow only 20 cookies to be set from a particular host. ColdFusion uses two of these cookies for the
   CFID and CFToken identifiers, and also creates a cookie named cfglobals to hold global data about the client, such
   as HitCount, TimeCreated, and LastVisit. This limits you to 17 unique applications per client-host pair.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                308
Developing CFML Applications




· Some browsers set a size limit of 4K bytes per cookie. ColdFusion encodes nonalphanumeric data in cookies with
    a URL encoding scheme that expands at a 3-1 ratio, which means you should not store large amounts of data per
    client. ColdFusion throws an error if you try to store more than 4,000 encoded bytes of data for a client.


Configuring database storage
When you specify a database for client variable storage, do not always have to manually create the data tables that store
the client variables.

If ColdFusion can identify that the database you are using supports SQL creation of database tables, create the database
in advance. When you click the Add button on the Select Data Source to Add as Client Store box on the Memory
Variables page, the Administrator displays a Add/Edit Client Store page which contains a Create Client Database
Tables selection box. Select this option to have ColdFusion create the necessary tables in your database. (The option
does not appear if the database already has the required tables.)

If your database does not support SQL creation of tables, or if you are using the ODBC socket [Macromedia] driver to
access your database, use your database tool to create the client variable tables. Create the CDATA and CGLOBAL
tables.

The CDATA table must have the following columns:


 Column                  Data type

 cfid                    CHAR(64), TEXT, VARCHAR, or any data type capable of taking variable length strings up to 64 characters

 app                     CHAR(64), TEXT, VARCHAR, or any data type capable of taking variable length strings up to 64 characters

 data                    MEMO, LONGTEXT, LONG VARCHAR, CLOB, or any data type capable of taking long, indeterminate-length strings


The CGLOBAL table must have the following columns:


 Column                  Data type

 cfid                    CHAR(64), TEXT, VARCHAR, or any data type capable of taking variable length strings up to 64 characters

 data                    MEMO, LONGTEXT, LONG VARCHAR, CLOB, or any data type capable of taking long, indeterminate-length strings

 lvisit                  TIMESTAMP, DATETIME, DATE, or any data type that stores date and time values


Note: Different databases use different names for their data types. The names in the preceding tables are common, but
your database might use other names.

To improve performance, create indexes when you create these tables. For the CDATA table, index these cfid and app
columns. For the CGLOBAL table, index the cfid column.


Specifying client variable storage in your application
The override the default client variable storage location, set the This.clientstorage variable in the Application.cfc
initialization code, or use the cfapplication tag clientStorage attribute.

The following lines from an Application.cfc file tell ColdFusion to store the client variables in the mydatasource data
source:

<cfscript>
       This.name"SearchApp";
       This.clientManagement="Yes";
       This.clientStorage="mydatasource";
</cfscript>




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          309
Developing CFML Applications




The following code from an Application.cfm file does the same thing as the previous example:

<cfapplication name"SearchApp"
     clientmanagement="Yes"
     clientstorage="mydatasource">


Using client variables
When you enable client variables for an application, you can use them to keep track of long-term information that is
associated with a particular client.

Client variables must be simple data types: strings, numbers, lists, Booleans, or date and time values. They cannot be
arrays, recordsets, XML objects, query objects, or other objects. If you must store a complex data type as a client
variable, you can use the cfwddx tag to convert the data to WDDX format (which is represented as a string), store the
WDDX data, and use the cfwddx tag to convert the data back when you read it. For more information on using
WDDX, see "Using WDDX" on page 1088.

Note: When saving client variable data in WDDX format, in the case of the registry and SQL Server, the limit is about
4K; with ORACLE, the limit is about 2K.


Creating a client variable
To create a client variable and set its value, use the cfset or cfparam tag and use the Client scope identifier as a
variable prefix; for example:

<cfset Client.FavoriteColor="Red">

After you set a client variable this way, it is available for use within any page in your application that is accessed by the
client for whom the variable is set.

The following example shows how to use the cfparam tag to check for the existence of a client parameter and set a
default value if the parameter does not exist:

<cfparam name="Client.FavoriteColor" default="Red">


Accessing and changing client variables
You use the same syntax to access a client variable as for other types of variables. You can use client variables anywhere
you use other ColdFusion variables.

To display the favorite color that has been set for a specific user, for example, use the following code:

<cfoutput>
     Your favorite color is #Client.FavoriteColor#.
</cfoutput>

To change the client's favorite color, for example, use code such as the following:

<cfset Client.FavoriteColor = Form.FavoriteColor>


Standard client variables
The Client scope has the following built-in, read-only variables that your application can use:




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  310
Developing CFML Applications




 Variable                       Description

 Client.CFID                    The client ID, normally stored on the client system as a cookie.

 Client.CFToken                 The client security token, normally stored on the client system as a cookie.

 Client.URLToken                Value depends on whether J2EE session management is enabled.

                                No session management or ColdFusion session management: A combination of the CFID and CFToken
                                values, in the form CFID=IDNum&CFTOKEN=tokenNum. This variable is useful if the client does not
                                support cookies and you pass the CFID and CFToken variables from page to page.

                                J2EE session management: A combination of CFID,CFToken, and session ID values in the form
                                CFID=IDNum&CFTOKEN=tokenNum&jsessionid=SessionID.

 Client.HitCount                The number of page requests made by the client.

 Client.LastVisit               The last time the client visited the application.

 Client.TimeCreated             The time the CFID and CFToken variables that identify the client to ColdFusion were first created.


Note: ColdFusion lets you delete or change the values of the built-in client variables. As a general rule, avoid doing so.

You use the Client.CFID, Client.CFToken, and Client.URLToken variables if your application supports browsers
that do not allow cookies. For more information on supporting browsers that do not allow cookies, see "Using client
and session variables without cookies" on page 305.

You can use the Client.HitCount and time information variables to customize behavior that depends on how often
users visit your site and when they last visited. For example, the following code shows the date of a user's last visit to
your site:

<cfoutput>
      Welcome back to the Web SuperShop. Your last
      visit was on #DateFormat(Client.LastVisit)#.
</cfoutput>


Getting a list of client variables
To obtain a list of the custom client parameters associated with a particular client, use the GetClientVariablesList
function, as follows:

<cfoutput>#GetClientVariablesList()#</cfoutput>

The GetClientVariablesList function returns a comma-separated list of the names of the client variables for the
current application. The standard system-provided client variables (CFID, CFToken, URLToken, HitCount,
TimeCreated, and LastVisit) are not returned in the list.


Deleting client variables
To delete a client variable, use the StructDelete function or the DeleteClientVariable function. For example, the
following lines are equivalent:

<cfset IsDeleteSuccessful=DeleteClientVariable("MyClientVariable")>
<cfset IsDeleteSuccessful=StructDelete(Client, "MyClientVariable")>

The Client Variables page in the ColdFusion Administrator lets you set a time-out period of inactivity after which
ColdFusion removes client variables stored in either the Registry or a data source. (The default value is 10 days for
client variables stored in the Registry, and 90 days for client variables stored in a data source.)

Note: You cannot delete the system-provided client variables (CFID, CFToken, URLToken, HitCount, TimeCreated, and
LastVisit).




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         311
Developing CFML Applications




Using client variables with cflocation
If you use the cflocation tag to redirect ColdFusion to a path that ends with .dbm or .cfm, the Client.URLToken
variable is automatically appended to the URL. You can prevent this behavior by adding the attribute addtoken="No"
to the cflocation tag.


Caching client variable
When ColdFusion reads or writes client variables, it caches the variables in memory to help decrease the overhead of
accessing the client data. As a result, ColdFusion only accesses the client data store when you read its value for the first
time or, for values you set, when the request ends. Additional references to the client variable use the cached value in
ColdFusion memory, thereby processing the page more quickly.


Exporting the client variable database
If your client variable database is stored in the Windows system Registry and you need to move it to another machine,
you can export the Registry key that stores your client variables and take it to your new server. The system Registry lets
you export and import Registry entries.

To export your client variable database from the Registry in Windows:

1 Open the Registry editor.

2 Find and select the following key:

    HKEY_LOCAL_MACHINE\SOFTWARE\Macromedia\ColdFusion\CurrentVersion\Clients

3 On the Registry menu, click Export Registry File.

4 Enter a name for the Registry file.

After you create a Registry file, you can copy it to a new machine and import it by clicking Import Registry File on the
Registry editor Registry menu.

Note: On UNIX systems, the registry entries are kept in /opt/coldfusion/registry/cf.registry, a text file that you can copy
and edit directly.


Configuring and using session variables
Use session variables when you need the variables for a single site visit or set of requests. For example, use session
variables to store a user's selections in a shopping cart application. (Use client variables if you need a variable in
multiple visits.)

Important: Place code that uses session variables inside cflock tags in circumstances that could result in race conditions
from multiple accesses to the same variable. For information on using cflock tags see "Locking code with cflock" on
page 318.


What is a session?
A session refers to all the connections that a single client makes to a server in the course of viewing any pages associated
with a given application. Sessions are specific to both the individual user and the application. As a result, every user of
an application has a separate session and has access to a separate set of session variables.

This logical view of a session begins with the first connection to an application by a client and ends after that client's
last connection. However, because of the stateless nature of the web, it is not always possible to define a precise point
at which a session ends. A session should end when the user finishes using an application. In most cases, however, a
web application has no way of knowing if a user has finished or is just lingering over a page.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         312
Developing CFML Applications




Therefore, sessions always terminate after a time-out period of inactivity. If the user does not access a page of the
application within this time-out period, ColdFusion interprets this as the end of the session and clears any variables
associated with that session.

The default time-out for session variables is 20 mins. You can change the default time-out on the Memory Variables
page in the Server Settings area in the ColdFusion Administrator.

You can also set the time-out period for session variables inside a specific application (thereby overruling the
Administrator default setting) by setting the Application.cfc This.sessionTimeout variable or by using the
cfapplication tag sessionTimeout attribute. However, you cannot set a time-out value for that is greater than the
maximum session time-out value set on the Administrator Memory Variables page.

For detailed information on ending sessions and deleting session variables, see "Ending a session" on page 315.


ColdFusion and J2EE session management
The ColdFusion server can use either of the following types of session management:

· ColdFusion session management

· J2EE servlet session management

ColdFusion session management uses the same client identification method as ColdFusion client management.

J2EE session management provides the following advantages over ColdFusion session management:

· J2EE session management uses a session-specific session identifier, jsessionid, which is created afresh at the start
   of each session.

· You can share session variables between ColdFusion pages and JSP pages or Java servlets that you call from the
   ColdFusion pages.

· The Session scope is serializable (convertible into a sequence of bytes that can later be fully restored into the original
   object). With ColdFusion session management, the Session scope is not serializable. Only serializable scopes can be
   shared across servers.

Therefore, consider using J2EE session management in any of the following cases:

· You want to maximize session security, particularly if you also use client variables

· You want to share session variables between ColdFusion pages and JSP pages or servlets in a single application.

· You want to be able to manually terminate a session while maintaining the client identification cookie for use by
   the Client scope.

· You want to support clustered sessions; for example, to support session failover among servers.


Configuring and enabling session variables
To use session variables, enable them in two places:

· ColdFusion Administrator

· The Application.cfc initialization code This.sessionManagement variable or the active cfapplication tag.

ColdFusion Administrator, Application.cfc, and the cfapplication tag also provide facilities for configuring session
variable behavior, including the variable time-out.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        313
Developing CFML Applications




Selecting and enabling session variables in ColdFusion Administrator
To use session variables, they must be enabled on the ColdFusion Administrator Memory Variables page. (They are
enabled by default.) You can also use the Administrator Memory Variables page to do the following:

· Select to use ColdFusion session management (the default) or J2EE session management.

· Change the default session time-out. Application code can override this value. The default value for this time-out
   is 20 mins.

· Specify a maximum session time-out. Application code cannot set a time-out greater than this value. The default
   value for this time-out is two days.


Enabling session variables in your application
Enable session variables in the initialization code of your Application.cfc file or in the cfapplication tag in your
Application.cfm file.

Do the following in the Application.cfc initialization code, below the cfcomponent tag, to enable session variables:

· Set This.sessionManagement="Yes".

· Set This.name to specify the name of the application.

· Optionally, set This.sessionTimeout to set an application-specific session time-out value. Use the CreateTimeSpan
   function to specify the number of days, hours, minutes, and seconds for the time-out.

Do the following in the Application.cfm file to enable session variables:

· Set sessionManagement="Yes"

· Use the name attribute to specify the name of the application.

· Optionally, use the sessionTimeout attribute to set an application-specific session time-out value. Use the
   CreateTimeSpan function to specify the number of days, hours, minutes, and seconds for the time-out.

The following sample code enables session management for the GetLeadApp application and sets the session variables
to time out after a 45-minute period of inactivity:

<cfapplication name="GetLeadApp"
     sessionmanagement="Yes"
     sessiontimeout=#CreateTimeSpan(0,0,45,0)#>


Storing session data in session variables
Session variables are designed to store session-level data. They are a convenient place to store information that all
pages of your application might need during a user session, such as shopping cart contents or score counters.

Using session variables, an application can initialize itself with user-specific data the first time a user accesses one of
the pages of the application. This information can remain available while that user continues to use that application.
For example, you can retrieve information about a specific user's preferences from a database once, the first time a user
accesses any page of an application. This information remains available throughout that user's session, thereby
avoiding the overhead of retrieving the preferences repeatedly.


Standard session variables
If you use ColdFusion session variables, the Session scope has four built-in, read-only variables that your application
can use. If you use J2EE session management, the Session scope has two built-in variables. Generally, you use these
variables in your ColdFusion pages only if your application supports browsers that do not allow cookies. For more
information on supporting browsers that do not allow cookies, see "Using client and session variables without cookies"
on page 305. The following table describes the built-in session variables.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     314
Developing CFML Applications




 Variable                       Description

 Session.CFID                   ColdFusion session management only: the client ID, normally stored on the client system as a cookie.

 Session.CFToken                ColdFusion session management only: the client security token, normally stored on the client system as a
                                cookie.

 Session.URLToken               ColdFusion session management: A combination of the CFID and CFToken values in the form
                                CFID=IDNum&CFTOKEN=tokenNum. Use this variable if the client does not support cookies and you must
                                pass the CFID and CFToken variables from page to page.

                                J2EE session management: A combination of the CFID and CFToken cookies and the J2EE session ID, in
                                the form CFID=IDNum&CFTOKEN=tokenNum&jsessionid=SessionID.

 Session.SessionID              A unique identifier for the session.

                                ColdFusion session management: a combination of the application name and CFID and CFToken values.

                                J2EE session management: the jsessionid value.


Note: ColdFusion lets you delete or change the values of the built-in session variables. As a general rule, avoid doing so.

If you enable client variables and ColdFusion session management, ColdFusion uses the same values for the Client and
Session scope CFID, CFToken, and URLtoken variables. ColdFusion gets the values for these variables from the same
source, the client's CFID and CFTOKEN cookies.

If you use J2EE session management, the Session scope does not include the Session.CFID or Session.CFToken
variables, but does include the Session.URLToken and Session.SessionID variables. In this case, the
Session.SessionID is the J2EE session ID and Session.URLToken consists of the string jsessionid= followed by
the J2EE session ID.


Getting a list of session variables
Use the StructKeyList function to get a list of session variables, as follows:

<cflock timeout=20 scope="Session" type="Readonly">
      <cfoutput> #StructKeyList(Session)# </cfoutput>
</cflock>

Important: Always put code that accesses session variables inside cflock tags.


Creating and deleting session variables
Use a standard assignment statement to create a new session variable, as follows:

<cflock timeout=20 scope="Session" type="Exclusive">
      <cfset Session.ShoppingCartItems = 0>
</cflock>

Use the structdelete tag to delete a session variable; for example:

<cflock timeout=20 scope="Session" type="Exclusive">
      <cfset StructDelete(Session, "ShoppingCartItems")>
</cflock>

Note: If you set session variables on a CFML template that uses the cflocation tag, ColdFusion might not set the
variables. For more information, see TechNote at www.adobe.com/go/tn_18171.


Accessing and changing session variables
You use the same syntax to access a session variable as for other types of variables. However, lock any code that accesses
or changes session variables.



                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       315
Developing CFML Applications




For example, to display the number of items in a user's shopping cart, use the following code:

<cflock timeout=20 scope="Session" type="Exclusive">
     <cfoutput>
           Your shopping cart has #Session.ShoppingCartItems# items.
     </cfoutput>
</cflock>

To increase the number of items in the shopping cart, use the following code:

<cflock timeout=20 scope="Session" type="Exclusive">
     <cfset Session.ShoppingCartItems = Session.ShoppingCartItems + 1>
</cflock>


Ending a session
The following rules apply to ending a session and deleting Session scope variables:

· If you use ColdFusion session management, ColdFusion automatically ends sessions and deletes all Session scope
   variables if the client is inactive for the session time-out period. The session does not end when the user closes the
   browser.

· If you use J2EE session management, ColdFusion ends the session and deletes all Session scope variables if the client
   is inactive for the session time-out period. However, the browser continues to send the same session ID, and
   ColdFusion reuses this ID for sessions with this browser instance, as long as the browser remains active.

· Logging a user out does not end the session or delete Session scope variables.

· In many cases, you can effectively end a session by clearing the Session scope, as shown in the following line. The
   following list, however, includes important limitations and alternatives:

   <cfset StructClear(Session)>

· Clearing the Session scope does not clear the session ID, and future requests from the browser continue to use the
   same session ID until the browser exits. It also does not log out the user, even if you use Session scope storage for
   login information. Always use the cflogout tag to log out users.

· If you use J2EE session management, you can invalidate the session, as follows:

   <cfset getPageContext().getSession().invalidate()>

   This line creates a pointer to the servlet page context and calls an internal method to reset the session. This clears
   all session information, including the session ID Session scope variables, and if you are using session login storage,
   the login information, for future request. However, the session information does remain available until the end of
   the current request. After you invalidate a session, attempts by the browser to access the application will generate
   an invalid session exception until the session times out.

   Note: You cannot destroy the session and create a session on the same request, as creating a new session involves
   sending session cookies back.

· If you do not use client cookies, the Session scope and login state is available to your application only as long as you
   pass the session's CFID, CFTOKEN, and, for J2EE sessions, jsessionid values in the URL query string. After you
   stop using these values, however, the session data remains in memory until the session time-out period elapses.


Configuring and using application variables
Application variables are available to all pages within an application, that is, pages that have the same application
name. Because application variables are persistent, you easily can pass values between pages. You can use application
variables for information including the application name, background color, data source names, or contact
information.



                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        316
Developing CFML Applications




You set the application name in the cfapplication tag, normally on your application's Application.cfm page. The
application name is stored in the Application.applicationName variable.

Unlike client and session variables, application variables do not require that a client name (client ID) be associated with
them. They are available to any clients that use pages in the application.

Important: Place code that uses application variables inside cflock tags in circumstances that could result in race
conditions from multiple accesses to the same variable. For information on using cflock tags, see "Locking code with
cflock" on page 318.


Configuring and enabling application variables
To use application variables, do the following:

· Ensure that they are enabled in the ColdFusion Administrator. (They are enabled by default.)

· Specify the application name by setting the This.name variable in the initialization code of the Application.cfc or
   by setting the name attribute of the cfapplication tag for the current page.

Note: ColdFusion supports unnamed applications for compatibility with J2EE applications. For more information, see
"Unnamed ColdFusion Application and Session scopes" on page 1131

The ColdFusion Administrator also lets you specify the following information:

· A default variable time-out. If all pages in an application are inactive for the time-out period, ColdFusion deletes
   all the application variables. The Application.cfc file or cfapplication tag can override this value for a specific
   application. The default value for this time-out is two days.

· A maximum time-out. The application code cannot set a time-out greater than this value. The default value for this
   time-out is two days.

You can set the time-out period for application variables within a specific application by using the
This.applicationTimeout variable of Application.cfc or the applicationTimeout attribute of the cfapplication tag.


Storing application data in application variables
Application variables are a convenient place to store information that all pages of your application might need, no
matter which client is running that application. Using application variables, an application could, for example,
initialize itself when the first user accesses any page of that application. This information can then remain available
indefinitely, thereby avoiding the overhead of repeated initialization.

Because the data stored in application variables is available to all pages of an application, and remains available until a
specific period of inactivity passes or the ColdFusion server shuts down, application variables are convenient for
application-global, persistent data.

However, because all clients running an application see the same set of application variables, these variables are not
appropriate for client-specific or session-specific information. To target variables for specific clients, use client or
session variables.


Using application variables
Generally, application variables hold information that you write infrequently. In most cases, the values of these
variables are set once, most often when an application first starts. Then the values of these variables are referenced
many times throughout the life of the application or the course of a session.

In circumstances that could result in race conditions from multiple accesses to the same variable, place code that writes
to Application scope variables or reads Application scope variables with data that can change inside cflock tags.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               317
Developing CFML Applications




Because each Application scope variable is shared in memory by all requests in the application, these variables can
become bottlenecks if used inappropriately. Whenever a request is reading or writing an Application scope variable,
any other requests that use the variable must wait until the code accessing the variable completes. This problem is
increased by the processing time required for locking. If many users access the application simultaneously and you use
Application scope variables extensively, your application performance might degrade. If your application uses many
application variables, consider whether the variables must be in the Application scope or whether they can be Session
or Request scope variables.

The application scope has one built-in variable, Application.applicationName, which contains the application
name you specify in the cfapplication tag.

Access and manipulate application variables the same way you use session variables, except that you use the variable
prefix Application, not Session, and specify Session as the lock scope. For examples of using session variables see
"Creating and deleting session variables" on page 314 and "Accessing and changing session variables" on page 314.

For information on locking write-once read-many application variables efficiently, see "Locking application variables
efficiently" on page 323


Using server variables
Server variables are associated with a single ColdFusion server. They are available to all applications that run on the
server. Use server variables for data that must be accessed across clients and applications, such as global server hit
counts.

Server variables do not time out, but they are lost when the server shuts down. You can delete server variables.

Server variables are stored on a single server. As a result, do not use server variables if you use ColdFusion on a server
cluster.

You access and manipulate server variables the same way use Session and application variables, except you use the
variable prefix Server.

Important: Place code that uses server variables inside cflock tags in circumstances that could result in race conditions
from multiple accesses to the same variable. You do not have to lock access to built-in server variables.

ColdFusion provides the following standard built-in read-only server variables:


 Variable                                     Description

 Server.ColdFusion.AppServer                  The name of the J2EE application server ColdFusion is using. For ColdFusion server
                                              editions, which have an integrated application server, the name is JRun4.

 Server.ColdFusion.Expiration                 The date on which the ColdFusion license expires if it is the trial version.

 Server.ColdFusion.ProductLevel               The server product level, such as Enterprise.

 Server.ColdFusion.ProductName                The name of the product (ColdFusion).

 Server.ColdFusion.ProductVersion             The version number for the server that is running, such as 6,0,0.

 Server.ColdFusion.Rootdir                    Directory under which ColdFusion is installed, such as C:\cfusion.

 Server.ColdFusion.SerialNumber               The serial number assigned to this server installation.

 Server.ColdFusion.SupportedLocales           The locales, such as English (US) and Spanish (Standard), supported by the server.

 Server.OS.AdditionalInformation              Additional information provided by the operating system, such as the Service Pack
                                              number.

 Server.OS.arch                               The processor architecture, such as x86 for Intel Pentium processors.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     318
Developing CFML Applications




 Variable                                     Description

 Server.OS.BuildNumber                        The specific operating system build, such as 1381

 Server.OS.Name                               The name of the operating system, such as Windows NT.

 Server.OS.Version                            The version number of the operating system, such as 4.0.



Locking code with cflock
The cflock tag controls simultaneous access to ColdFusion code. The cflock tag lets you do the following:

· Protect sections of code that access and manipulate shared data in the Session, Application, and Server scopes, and
   in the Request and Variables scopes for applications that use ColdFusion threads.

· Ensure that file updates do not fail because files are open for writing by other applications or ColdFusion tags.

· Ensure that applications do not try to simultaneously access ColdFusion extension tags written using the CFX API
   that are not thread-safe. This is important for CFX tags that use shared (global) data structures without protecting
   them from simultaneous access (not thread-safe). However, Java CFX tags can also access shared resources that
   could become inconsistent if the CFX tag access is not locked.

· Ensure that applications do not try to simultaneously access databases that are not thread-safe. (This is not
   necessary for most database systems.)

ColdFusion is a multi-threaded web application server that can process multiple page requests at a time. As a result,
the server can attempt to access the same information or resources simultaneously, as the result of two or more
requests.

Although ColdFusion is thread-safe and does not try to modify a variable simultaneously, it does not ensure the correct
order of access to information. If multiple pages, or multiple invocations of a page, attempt to write data
simultaneously, or read and write it at the same time, the resulting data can be inconsistent, as shown in the following
Sample locking scenarios section.

Similarly, ColdFusion cannot automatically ensure that two sections of code do not attempt to access external
resources such as files, databases, or CFX tags that cannot properly handle simultaneous requests. Nor can ColdFusion
ensure that the order of access to these shared resources is consistent and results in valid data.

By locking code that accesses such resources so that only one thread can access the resource at a time, you can prevent
race conditions.


Sample locking scenarios
The following examples present scenarios in which you need to lock ColdFusion code. These scenarios show only two
of the circumstances where locking is vital.


Reading and writing a shared variable
If you have an application-wide value, such as a counter of the total number of tickets sold, you could have code such
as the following on a login page:

<cfset Application.totalTicketsSold = Application.totalTicketsSold + ticketOrder>

When ColdFusion executes this code, it performs the following operations:

1 Retrieves the current value of Application.totalTicketsSold from temporary storage.

2 Increments this value.

3 Stores the result back in the Application scope.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      319
Developing CFML Applications




Suppose that ColdFusion processes two ticket orders at approximately the same time, and that the value of
Application.totalTicketsSold is initially 160. The following sequence might happen:

1 Order 1 reads the total tickets sold as 160.

2 Order 2 reads the total tickets sold as 160.

3 Order 1 adds an order of 5 tickets to 160 to get 165.

4 Order 2 adds an order of 3 tickets to 160 to get 163.

5 Order 1 saves the value 165 to Application.totalTicketsSold

6 Order 2 saves the value 163 to Application.totalTicketsSold

The application now has an inaccurate count of the tickets sold, and is in danger of selling more tickets than the
auditorium can hold.

To prevent this from happening, lock the code that increments the counter, as follows:

<cflock scope="Application" timeout="10" type="Exclusive">
     <cfset Application.totalTicketsSold = Application.totalTicketsSold + ticketOrder>
</cflock>

The cflock tag ensures that while ColdFusion performs the processing in the tag body, no other threads can access
the Application scope. As a result, the second transaction is not processed until the first one completes. The processing
sequence looks something like the following:

1 Order 1 reaches the lock tag, which gets an Application scope lock.

2 Order 1 reads the total tickets sold as 160.

3 Order 2 reaches the lock tag. Because there is an active Application scope lock, ColdFusion waits for the lock to free.

4 Order 1 adds an order of 5 tickets to 160 to get 165.

5 Order 1 saves the value 165 to Application.totalTicketsSold.

6 Order 1 exits the lock tag. The Application scope lock is now free.

7 Order 2 gets the Application scope lock and can begin processing.

8 Order 2 reads the total tickets sold as 165.

9 Order 2 adds an order of 3 tickets to 165 to get 168.

10 Order 2 saves the value 168 to Application.totalTicketsSold.

11 Order 2 exits the lock tag, which frees the Application scope lock. ColdFusion can process another order.

The resulting Application.totalTickesSold value is now correct.


Ensuring consistency of multiple variables
Often an application sets multiple shared scope variables at one time, such as many values submitted by a user on a
form. If the user submits the form, clicks the back button, and then resubmits the form with different data, the
application can end up with a mixture of data from the two submissions, in much the same manner as shown in the
previous section.

For example, an application stores information about order items in a Session scope shopping cart. If the user submits
an item selection page with data specifying sage green size 36 shorts, and then resubmits the item specifying sea blue
size 34 shorts, the application can end up with a mixture of information from the two orders, such as sage green size
34 shorts.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          320
Developing CFML Applications




By placing the code that sets all of the related session variables in a single cflock tag, you ensure that all the variables
get set together. In other words, setting all of the variables becomes an atomic, or single, operation. It is like a database
transaction, where everything in the transaction happens, or nothing happens. In this example, the order details for
the first order all get set, and then they are replaced with the details from the second order.

For more examples of using locking in applications, see "Examples of cflock" on page 324.


Using the cflock tag with write-once variables
You need not use cflock when you read a variable or call a user-defined function name in the Session, Application,
or Server scope if it is set in only one place in the application, and is only read (or called, for a UDF) everywhere else.
Such data is called write-once. If you set an Application or Session scope variable in Application.cfm and never set it
on any other pages, lock the code that sets the variable, but do not have to lock code on other pages that reads the
variable's value. If you set the variable in the corresponding start method in Application.cfc (for example,
onApplicationStart for Application scope variables), you do not have to lock the code that sets the variable.

However, although leaving code that uses write-once data unlocked can improve application performance, it also has
risks. Ensure that the variables are written only once. For example, ensure that the variable is not rewritten if the user
refreshes the browser or clicks a back button. Also, it can be difficult to ensure that you, or future developers, do not
later set the variable in more than one place in the application.


Using the cflock tag
The cflock tag ensures that concurrently executing requests do not run the same section of code simultaneously and
thus manipulate shared data structures, files, or CFX tags inconsistently. It is important to remember that cflock
protects code sections that access or set data, not the variables themselves.

You protect access to code by surrounding it in a cflock tag; for example:

<cflock scope="Application" timeout="10" type="Exclusive">
     <cfif not IsDefined("Application.number")>
           <cfset Application.number = 1>
     </cfif>
</cflock>


Lock types
The cflock tag offers two modes of locking, specified by the type attribute:

Exclusive locks (the default lock type) Allow only one request to process the locked code. No other requests can run
code inside the tag while a request has an exclusive lock.

Enclose all code that creates or modifies session, application, or server variables in exclusive cflock tags.

Read-only locks Allow multiple requests to execute concurrently if no exclusive locks with the same scope or name are
executing. No requests can run code inside the tag while a request has an exclusive lock.

Enclose code that only reads or tests session, application, or server variables in read-only cflock tags. You specify a
read-only lock by setting the type="readOnly" attribute in the cflock tag, for example:

<cflock scope="Application" timeout="10" type="readOnly">
     <cfif IsDefined("Application.dailyMessage")>
           <cfoutput>#Application.dailyMessage#<br></cfoutput>
     </cfif>
</cflock>




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                             321
Developing CFML Applications




Although ColdFusion does not prevent you from setting shared variables inside read-only lock tag, doing so loses the
advantages of locking. As a result, be careful not to set any session, application, or server variables inside a read-only
cflock tag body.

Note: You cannot upgrade or downgrade a lock from one type to another. In other words, do not nest an exclusive lock
in a read-only lock of the same name or scope; the exclusive lock will always time out. Also, do not nest a read-only lock
inside an exclusive lock with the same name or scope; doing so has no effect.


Lock scopes and names
The cflock tag prevents simultaneous access to sections of code, not to variables. If you have two sections of code that
access the same variable, they must be synchronized to prevent them from running simultaneously. You do this by
identifying the locks with the same scope or name attributes.

Note: ColdFusion does not require you to identify exclusive locks. If you omit the identifier, the lock is anonymous and
you cannot synchronize the code in the cflock tag block with any other code. Anonymous locks do not cause errors when
they protect a resource that is used in a single code block, but they are bad programming practice. You must always
identify read-only locks.


Controlling access to data with the scope attribute
When the code that you are locking accesses session, application, or server variables, synchronize access by using the
cflockscope attribute.

You can set the attribute to any of the following values:


 Scope                   Meaning

 Server                  All code sections with this attribute on the server share a single lock.

 Application             All code sections with this attribute in the same application share a single lock.

 Session                 All code sections with this attribute that run in the same session of an application share a single lock.

 Request                 All code sections with this attribute that run in the same request share a single lock. You use this scope only if your
                         application uses the cfthread tag to create multiple threads in a single request. Locking the Request scope also
                         locks access to Variables scope data. For more information on locking the Request scope, see "Locking thread data
                         and resource access" on page 334.


If multiple code sections share a lock, the following rules apply:

· When code is running in a cflock tag block with the type attribute set to Exclusive, code in cflock tag blocks
   with the same scope attribute is not allowed to run. They wait until the code with the exclusive lock completes.

· When code in a cflock tag block with the type readOnly is running, code in other cflock tag blocks with the same
   scope attribute and the readOnlytype attribute can run, but any blocks with the same scope attribute and an
   Exclusive type cannot run and must wait until all code with the read-only lock completes. However, if a read-only
   lock is active and code with an exclusive lock with the same scope or name is waiting to execute, read-only requests
   using the same scope or name that are made after the exclusive request is queued must wait until code with the
   exclusive lock executes and completes.


Controlling locking access to files and CFX tags with the name attribute
The cflockname attribute provides a second way to identify locks. Use this attribute when you use locks to protect
code that manges file access or calls non-thread-safe CFX code.

When you use the name attribute, specify the same name for each section of code that accesses a specific file or a specific
CFX tag.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           322
Developing CFML Applications




Controlling and minimizing lock time-outs
Include a timeout attribute in your cflock tag. The timeout attribute specifies the maximum time, in seconds, to
wait to obtain the lock if it is not available. By default, if the lock does not become available within the time-out period,
ColdFusion generates a Lock type exception error, which you can handle using cftry and cfcatch tags.

If you set the cflockthrowOnTimeout attribute to No, processing continues after the time-out at the line after the
</cflock> end tag. Code in the cflock tag body does not run if the time-out occurs before ColdFusion can acquire
the lock. Therefore, never use the throwOnTimeout attribute for CFML that must run.

Normally, it does not take more than a few seconds to obtain a lock. Very large time-outs can block request threads
for long periods of time and radically decrease throughput. Always use the smallest time-out value that does not result
in a significant number of time-outs.

To prevent unnecessary time-outs, lock the minimum amount of code possible. Whenever possible, lock only code
that sets or reads variables, not business logic or database queries. One useful technique is to do the following:

1 Perform a time-consuming activity outside a cflock tag

2 Assign the result to a Variables scope variable

3 Assign the Variables scope variable's value to a shared scope variable inside a cflock block.

For example, if you want to assign the results of a query to a session variable, first get the query results using a Variables
scope variable in unlocked code. Then, assign the query results to a session variable inside a locked code section. The
following code shows this technique:

<cfquery name="Variables.qUser" datasource="#request.dsn#">
     SELECT FirstName, LastName
     FROM Users
     WHERE UserID = #request.UserID#
</cfquery>
<cflock scope="Session" timeout="5" type="exclusive">
     <cfset Session.qUser = Variables.qUser>
</cflock>


Considering lock granularity
When you design your locking strategy, consider whether you should have multiple locks containing small amounts
of code or few locks with larger blocks of code. There is no simple rule for making such a decision, and you might do
performance testing with different options to help make your decision. However, consider the following issues:

· If the code block is larger, ColdFusion spends more time inside the block, which can increase the number of times
   an application waits for the lock to released.

· Each lock requires processor time. The more locks you have, the more processor time is spent on locking code.


Nesting locks and avoiding deadlocks
Inconsistent nesting of cflock tags and inconsistent naming of locks can cause deadlocks (blocked code). If you are
nesting locks, you must consistently nest cflock tags in the same order and use consistent lock scopes (or names).

A deadlock is a state in which no request can execute the locked section of the page. All requests to the protected section
of the page are blocked until there is a time-out. The following table shows one scenario that would cause a deadlock:




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         323
Developing CFML Applications




 User 1                                                             User 2

 Locks the Session scope.                                           Locks the Application scope.

 Tries to lock the Application scope, but the Application scope is  Tries to lock the Session scope, but the Session scope is already locked
 already locked by User 2.                                          by User 1.


Neither user's request can proceed, because it is waiting for the other to complete. The two are deadlocked.

Once a deadlock occurs, neither of the users can do anything to break the deadlock, because the execution of their
requests is blocked until the deadlock is resolved by a lock time-out.

You can also cause deadlocks if you nest locks of different types. An example of this is nesting an exclusive lock inside
a read-only lock of the same scope or same name.

To avoid a deadlock, lock code sections in a well-specified order, and name the locks consistently. In particular, to lock
access to the Server, Application, and Session scopes, do so in the following order:

1 Lock the Session scope. In the cflock tag, specify scope="Session".

2 Lock the Application scope. In the cflock tag, specify scope="Application".

3 Lock the Server scope. In the cflock tag, specify scope="Server".

4 Unlock the Server scope.

5 Unlock the Application scope.

6 Unlock the Session scope.

Note: You can skip any pair of lock and unlock steps in the preceding list if you do not need to lock a particular scope. For
example, you can omit steps 3 and 4 if you do not need to lock the Server scope.


Copying shared variables into the Request scope
You can avoid locking some shared-scope variables multiple times during a request by doing the following:

1 Copy the shared-scope variables into the Request scope in code with an exclusive lock in the Application.cfc
    onRequestStart method or the Application.cfm page.

2 Use the Request scope variables on your ColdFusion pages for the duration of the request.

3 Copy the variables back to the shared scope in code with an exclusive lock in the Application.cfc onRequestEnd
    method on the OnRequestEnd.cfm page.

With this technique the "last request wins." For example, if two requests run simultaneously, and both requests change
the values of data that was copied from the shared scope, the data from the last request to finish is saved in the shared
scope, and the data from the previous request is not saved.


Locking application variables efficiently
The need to lock application variables can reduce server performance, because all requests that use Application scope
variables must wait on a single lock. This issue is a problem even for write-once read-many variables, because you still
must ensure that the variable exists, and possibly set the value before you can read it.

You can minimize this problem by using a technique such as the following to test for the existence of application
variables and set them if they do not exist:

1 Use an Application scope flag variable to indicate if the variable or variables are initialized. In a read-only lock,
    check for the existence of the flag, and assign the result to a local variable.

2 Outside the cflock bock, test the value of the local variable




                                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                             324
Developing CFML Applications




3 If it the local variable indicates that the application variables are not initialized, get an exclusive Application scope lock.

4 Inside the lock, again test the Application scope flag, to make sure that another page has not set the variables
  between step one and step four.

5 If the variables are still not set, set them and set the Application scope flag to true.

6 Release the exclusive lock.

The following code shows this technique:

<!--- Initialize local flag to false. --->
<cfset app_is_initialized = False>
<!--- Get a readonly lock --->
<cflock scope="application" type="readonly">
     <!--- read init flag and store it in local variable --->
     <cfset app_is_initialized = IsDefined("APPLICATION.initialized")>
</cflock>
<!--- Check the local flag --->
<cfif not app_is_initialized >
<!--- Not initialized yet, get exclusive lock to write scope --->
     <cflock scope="application" type="exclusive">
          <!--- Check nonlocal flag since multiple requests could get to the
                      exclusive lock --->
          <cfif not IsDefined("APPLICATION.initialized") >
                <!--- Do initializations --->
                <cfset APPLICATION.varible1 = someValue >
                  ...
                <!--- Set the Application scope initialization flag --->
                <cfset APPLICATION.initialized = "yes">
          </cfif>
     </cflock>
</cfif>



Examples of cflock
The following examples show how to use cflock blocks in a variety of situations.


Example with application, server, and session variables
This example shows how you can use cflock to guarantee the consistency of data updates to variables in the
Application, Server, and Session scopes.

This example does not handle exceptions that arise if a lock times out. As a result, users see the default exception error
page on lock time-outs.

The following sample code might be part of the Application.cfm file:




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                325
Developing CFML Applications




<cfapplication name="ETurtle"
    sessiontimeout=#createtimespan(0,1,30,0)#
    sessionmanagement="yes">


<!--- Initialize the Session and Application
variables that will be used by E-Turtleneck. Use
the Session lock scope for the session variables. --->


<cflock scope="Session"
    timeout="10" type ="Exclusive">
    <cfif not IsDefined("session.size")>
         <cfset session.size = "">
    </cfif>
    <cfif not IsDefined("session.color")>
         <cfset session.color = "">
    </cfif>
</cflock>


<!--- Use the Application scope lock for the Application.number variable.
This variable keeps track of the total number of turtlenecks sold.
The following code implements the scheme shown in the Locking Application
variables effectively section --->


<cfset app_is_initialized = "no">
<cflock scope="Application" type="readonly">
    <cfset app_is_initialized = IsDefined("Application.initialized")>
</cflock>
<cfif not app_is_initialized >
    <cflock scope="application" timeout="10" type="exclusive">
         <cfif not IsDefined("Application.initialized") >
              <cfset Application.number = 1>
              <cfset Application.initialized = "yes">
         </cfif>
    </cflock>
</cfif>


<!--- Always display the number of turtlenecks sold --->


<cflock scope="Application"
    timeout="10"
    type ="ReadOnly">
    <cfoutput>
    E-Turtleneck is proud to say that we have sold
    #Application.number# turtlenecks to date.
    </cfoutput>
</cflock>

The remaining sample code could appear inside the application page where customers place orders:




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                326
Developing CFML Applications




<html>
<head>
<title>cflock Example</title>
</head>


<body>
<h3>cflock Example</h3>


<cfif IsDefined("Form.submit")>


<!--- Lock session variables --->
<!--- Note that we use the automatically generated Session
    ID as the order ID --->
<cflock scope="Session"
    timeout="10" type="ReadOnly">
    <cfoutput>Thank you for shopping E-Turtleneck.
    Today you have chosen a turtleneck in size
    <b>#form.size#</b> and in the color <b>#form.color#</b>.
    Your order ID is #Session.sessionID#.
    </cfoutput>
</cflock>


<!--- Lock session variables to assign form values to them. --->


<cflock scope="Session"
    timeout="10"
    type="Exclusive">
    <cfparam name=Session.size default=#form.size#>
    <cfparam name=Session.color default=#form.color#>
</cflock>
<
!--- Lock the Application scope variable application.number to
update the total number of turtlenecks sold. --->


<cflock scope="Application"
    timeout="30" type="Exclusive">
    <cfset application.number=application.number + 1>
</cflock>


<!--- Show the form only if it has not been submitted. --->
<cfelse>
<form action="cflock.cfm" method="Post">


<p> Congratulations! You have just selected
the longest-wearing, most comfortable turtleneck
in the world. Please indicate the color and size
you want to buy.</p>


<table cellspacing="2" cellpadding="2" border="0">
<tr>
<td>Select a color.</td>
<td><select type="Text" name="color">
         <option>red
         <option>white
         <option>blue
         <option>turquoise
         <option>black



                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      327
Developing CFML Applications




           <option>forest green
           </select>
      </td>
</tr>
<tr>
      <td>Select a size.</td>
      <td><select type="Text" name="size">
           <option>small
           <option>medium
           <option>large
           <option>xlarge
           </select>
      </td>
</tr>
<tr>
      <td></td>
      <td><input type="Submit" name="submit" value="Submit">
      </td>
</tr>
</table>
</form>
</cfif>


</body>
</html>

Note: In this simple example, the Application.cfm page displays the Application.number variable value. Because the
Application.cfm file is processed before any code on each ColdFusion page, the number that displays after you click the
submit button does not include the new order. One way you can resolve this problem is by using the OnRequestEnd.cfm
page to display the value at the bottom of each page in the application.


Example of synchronizing access to a file system
The following example shows how to use a cflock block to synchronize access to a file system. The cflock tag
protects a cffile tag from attempting to append data to a file already open for writing by the same tag executing on
another request.

If an append operation takes more than 30 seconds, a request waiting to obtain an exclusive lock to this code might
time out. Also, this example uses a dynamic value for the name attribute so that a different lock controls access to each
file. As a result, locking access to one file does not delay access to any other file.

<cflock name=#filename# timeout=30 type="Exclusive">
      <cffile action="Append"
           file=#fileName#
           output=#textToAppend#>
</cflock>


Example of protecting ColdFusion extensions
The following example shows how you can build a custom tag wrapper around a CFX tag that is not thread-safe. The
wrapper forwards attributes to the non-thread-safe CFX tag that is used inside a cflock tag.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       328
Developing CFML Applications




<cfparam name="Attributes.AttributeOne" default="">
<cfparam name="Attributes.AttributeTwo" default="">
<cfparam name="Attributes.AttributeThree" default="">


<cflock timeout=5
          type="Exclusive"
          name="cfx_not_thread_safe">
     <cfx_not_thread_safe attributeone=#attributes.attributeone#
          attributetwo=#attributes.attributetwo#
          attributethree=#attributes.attributethree#>
</cflock>




Using ColdFusion Threads

You can use threads in Adobe ColdFusion to simultaneously run multiple streams of execution in a ColdFusion page
or CFC.


About ColdFusion threads
Threads are independent streams of execution. Multiple threads on a page or CFC can execute simultaneously and
asynchronously, letting you perform asynchronous processing in CFML.

Threads are useful for two broad types of activities:

· When multiple actions can occur simultaneously

· When you do not have to wait for one action to complete before starting the next action

Some typical uses for threads include the following examples:

· An application that aggregates information from multiple external sources, that takes significant time to respond,
   has the code that gets information from each source in a separate thread. This way, the application starts all requests
   quickly and has to wait only until the last response is received, instead of having to wait for a response to each
   request before the next request can start. One example of such usage is an RSS or Atom feed aggregator.

· A page that sends many mail messages runs the code that sends the mail messages in a separate thread and doesn't
   wait for it to complete to continue processing. The thread that sends the mail messages continues processing after
   the page-level processing is completed and the application starts processing another page.

· An application does maintenance of user data, such as using update queries, deleting records, and so on, whenever
   a user logs into the site. If the application does the maintenance in a separate thread, the user gets an immediate
   response after logging in, without having to wait for the updates to complete.

When ColdFusion processes a page, the page executes in a single thread, called the page thread. The cfthread tag lets
you create additional threads that can process independently of the page thread, and lets you synchronize thread
processing, for example, by having the page thread wait until threads that you create complete their processing.


Creating and managing ColdFusion threads
You use the cfthread tag and the Sleep function to create and manage ColdFusion threads. You manage a thread by
doing the following actions:

· Start the thread running.

· Temporarily suspend the processing of the thread. This action is useful if one thread must wait for another thread
   to do processing, but both threads must continue processing without joining.


                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         329
Developing CFML Applications




· End a thread. You typically end a running thread if there is an error, or if it is still processing after a long time.

· Have the page or a thread wait until one or more other threads have completed processing before proceeding with
   its processing, called joining the threads. You typically join threads when one thread requires the results from
   another thread. For example, if a page uses multiple threads to get several news feeds for display, it joins all the feed
   threads before it displays the results.

Each thread runs the code inside a cfthread tag body and normally exits when the tag body code completes
processing.


Starting a thread
You start a thread by using a cfthread tag with an action attribute value of run. CFML code within the cfthread
tag body executes on a separate thread while the page request thread continues processing. Only the page thread can
create other threads. A thread that you create with a cfthread tag cannot create a child thread, so you cannot have
multiple nested threads.

Optionally, when you start the thread, you can specify a priority level of high, normal (the default), or low to specify
the relative amount of time that the processor should devote to the thread. Page-level code always runs at normal
priority, so you can give your threads more or less processing time than the page.

For more information on using thread attributes, see "The Attributes scope and thread attributes" on page 333.


Suspending a thread
In some cases, one thread must wait until a second thread completes some operations, but should not wait until the
second thread completes all processing, so you cannot just join the threads. For example, one thread might do
initialization that multiple threads require, and then it might continue with additional processing. The other threads
could suspend themselves until initialization is complete.

The Sleep function and cfthread tag with a sleepaction attribute provide two equivalent mechanisms for doing
such synchronization. They suspend the thread processing for a specified period of time. A code loop could test a
condition variable and sleep for a period before retesting the condition. When the condition is true (or a value is
reached, or some other test is valid), the program exits the loop and the thread continues processing.

The following example shows how one thread could use a sleep function to wait for a second thread to perform some
actions.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      330
Developing CFML Applications




<!--- ThreadA loops to simulate an activity that might take time. --->
<cfthread name="threadA" action="run">
          <cfset thread.j=1>
          <cfloop index="i" from="1" to="1000000">
          <cfset thread.j=thread.j+1>
      </cfloop>
</cfthread>


<!--- ThreadB loops, waiting until threadA finishes looping 40000 times.
               the loop code sleeps 1/2 second each time. --->
<cfthread name="threadB" action="run">
      <cfscript>
          thread.sleepTimes=0;
          thread.initialized=false;
          while ((threadA.Status != "TERMINATED") && (threadA.j < 400000)) {
               sleep(500);
               thread.sleeptimes++;
          }
          // Don't continue processing if threadA terminated abnormally.
          If (threadA.Status != "TERMINATED") {
               thread.initialized=true;
               // Do additional processing here.
          }
      </cfscript>
</cfthread>


<!Join the page thread to thread B. Don't join to thread A.--->
<cfthread action="join" name="threadB" timeout="10000" />


<!--- Display the thread information. --->
<cfoutput>
      current threadA index value: #threadA.j#<br />
      threadA status: #threadA.Status#<br>
      threadB status: #threadB.Status#<br>
      threadB sleepTimes: #threadB.sleepTimes#<br>
      Is threadB initialized: #threadB.initialized#<br>
</cfoutput>


Ending a thread
If a thread never completes processing (is hung), it continues to occupy system resources, so it is good practice to have
your application check for hung threads and end them. Also consider ending threads that take excessive time to
process and might significantly reduce the responsiveness of your application or server.

To end a thread, use the cfthread tag with an action attribute value of terminate, as the following code snippet
shows.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     331
Developing CFML Applications




<!--- Thread1 sleeps to simulate an activity that might hang. --->
<cfthread name="thread1" action="run">
          <cfset thread.j=1>
     <cfset sleep(50000) >
     </cfthread>


<!--- Thread2 loops to simulate an activity that takes less time. --->
<cfthread name="thread2" action="run">
          <cfset thread.j=1>
          <cfloop index="i" from="1" to="10">
          <cfset thread.j=thread.j+1>
     </cfloop>
</cfthread>


<!--- The page thread sleeps for 1/2 second to let thread
           processing complete. --->
<cfset sleep(500) >


<!--- The page thread loops through the threads and terminates
               any that are still running or never started.
          Note the use of the cfthread scope and associative array
          notation to reference the dynamically named threads without
          using the Evaluate function. --->
<cfloop index="k" from="1" to="2">
<cfset theThread=cfthread["thread#k#"]>
     <cfif ((theThread.Status IS "RUNNING") || (theThread.Status IS "NOT_STARTED"))>
          <cfthread action="terminate" name="thread#k#" />
     </cfif>
</cfloop>


<!--- Wait 1/2 second to make ensure the termination completes --->
<cfset sleep(500) >


<!--- Display the thread information. --->
<cfoutput>
     thread1 index value: #thread1.j#<br />
     thread1 status: #thread1.Status#<br>
     thread2 index value: #thread2.j#<br />
     thread2 status: #thread2.Status#<br>
</cfoutput>

Note: You can also have the ColdFusion Sever Monitor automatically check for and terminate hung threads.


Joining threads
You use the cfthreadtag with an action attribute value of join to join two or more threads. You join threads when
one thread depends on one or more other threads completing before it can do some processing. For example, a page
can start multiple threads to do processing and join them before it processes the thread results. By default, the join
action stops the current thread from doing further processing until all the specified threads complete processing.

You can use a timeout attribute to specify the number of milliseconds that the current thread waits for the thread or
threads being joined to finish. If any thread does not finish by the specified time, the current thread proceeds without
waiting for the remaining thread or threads to complete.

The following code, for example, joins three threads to the current thread (often, the main page thread). The current
thread waits up to six seconds for the other threads to complete, and continues processing if one or more threads do
not complete by then.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          332
Developing CFML Applications




<cfthread action="join" name="t1,t2,t3" timeout="6000"/>

If the timeout attribute value is 0, the default value, the current thread continues waiting until all joining threads
finish. In this case, if the current thread is the page thread, the page continues waiting until the threads are joined, even
if you specify a page time-out. As a general rule, specify a timeout value to limit hung threads.


Using thread data
Because multiple threads can process simultaneously within a single request, applications must ensure that data from
one thread does not improperly affect data in another thread. ColdFusion provides several scopes that you can use to
manage thread data, and a request-level lock mechanism that you use to prevent problems caused by threads that
access page-level data. ColdFusion also provides metadata variables that contain any thread-specific output and
information about the thread, such as its status and processing time.


Thread scopes
Each thread has three special scopes:

· The thread-local scope

· The Thread scope

· The Attributes scope


The thread-local scope
The thread-local scope is an implicit scope that contains variables that are available only to the thread, and exist only
for the life of the thread. Any variable that you define inside the cfthread tag body without specifying a scope name
prefix is in the thread local scope and cannot be accessed or modified by other threads.

To create a thread-local variable, assign the variable in the cfthread tag body without specifying a scope prefix, as in
the following lines:

<cfset var index=1>
<cfset index=1>

These two lines are equivalent, with one exception: If you use the var keyword, the assignment code must immediately
follow the cfthread tag, before any other CFML tags.


The Thread scope
The Thread scope contains thread-specific variables and metadata about the thread. Only the owning thread can write
data to this scope, but the page thread and all other threads in a request can read the variable values in this scope.
Thread scope data remains available until the page and all threads that started from the page finish, even if the page
finishes before the threads complete processing.

To create a Thread scope variable, in the cfthread tag body, use the keyword Thread or the name of the thread (for
example, myThread) as a prefix. the following examples of creating a Thread scope variable are equivalent:

<cfset Thread.myValue = 27>
<cfset myThread.myValue = 27>

To access a thread's Thread scope variables outside the thread, prefix the variable with the thread's name, as in the
following example:

<cfset nextValue=myThread.myValue + 1>




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        333
Developing CFML Applications




Thread scope variables are only available to the page that created the thread or to other threads created by that page.
No other page can access the data. If one page must access another page's Thread scope data, you must place the data
in a database or file and access it from there.

The Thread scope of each thread is a subscope of a special scope, cfthread, that lasts as long as the request, or until the
last thread that it starts completes, whichever is longer. Thus, if you have two threads, myThread1 and myThread2,
you can access their Thread scopes as cfthread.myThread1 and cfthread.myThread2 until all threads and the request
complete. In most cases, there is no need to use the cfthread scope directly. However, you can use the cfthread scope
name in either of the following situations:

1 If you generate the thread name dynamically, you can avoid using the Evaluate function by using the cfthread
   scope with associative array notation, as the following code snippet shows:

    <cfset threadname="thread_#N#">
    ...
    <!--- The following two lines are equivalent --->
    <cfset threadscopeForNthThread = cfthread[threadname] >
    <cfset threadscopeForNthThread = Evaluate(threadname) >

2 If you have a thread with the same name as a Variables scope variable, you can access that thread's Thread scope
   only by prefacing the Thread name with cfthread. Otherwise, you access the Variables scope variable, or get an
   error.


The Attributes scope and thread attributes
The Attributes scope contains attributes that are passed to the thread, either individually or in the
attributeCollection attribute. The Attributes scope is available only within the thread and only for the life of the
thread.

ColdFusion makes a complete (deep) copy of all the attribute variables before passing them to the thread; therefore,
the values of the variables inside the thread are independent of the values of any corresponding variables in other
threads, including the page thread. For example, if you pass a CFC instance as an attribute to a thread, the thread gets
a complete new copy of the CFC, including the contents of its This scope at the time that you create the thread. Any
changes made to the original CFC outside the thread, for example, by calling a CFC function, have no effect on the
copy that is in the thread. Similarly, any changes to the CFC instance in the thread have no effect on the original CFC
instance.

Copying the data ensures that the values passed to threads are thread-safe, because the attribute values cannot be
changed by any other thread. If you do not want duplicate data, do not pass it to the thread as an attribute or in the
attributeCollection attribute. Instead, keep the data in a scope that the thread can access. An example of an object
that should not be passed to the thread as an attribute is a singleton CFC that should never be duplicated. The singleton
CFC must be kept in some shared scope and accessed by threads. For more information, see the "Using other scopes"
on page 334.

Because ColdFusion copies all attributes by value, you can have multiple threads, for example, threads created
dynamically in a loop, that use the same attribute names, but where each thread gets a different value, as shown in the
following code excerpt, which creates separate threads to copy each of several files in a directory:

<cfloop query="dir">
      <cfset threadname = "thread_" & #i#>
      <cfset i=i+1>
      <cfthread name="#threadname#" filename="#dir.name#">
          <cffile action="COPY" source="#src#\#filename#"
                destination="#dest#\#filename#\">
      </cfthread>
</cfloop>




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                       334
Developing CFML Applications




Using other scopes
Threads have access to all the ColdFusion scopes. All the threads run by a page share the same Variables and This
scope. All the threads run in a request share the same Form, URL, Request, CGI, Cookie, Session, Application, Server
and Client scopes. Be careful to lock access to these scopes if more than one thread could try to modify the data in the
scopes; otherwise you can get deadlocks between threads. For more information, see "Locking thread data and
resource access" on page 334.

Although a thread can access all the scopes, it might not be able to write to scopes like Session, Cookie, or Request after
the request page processing completes.


Scope precedence
If you do not specify a scope prefix on a variable inside a cfthread tag body, ColdFusion checks scopes in the
following order to find the variable:

1 Function-local, in function definitions in the thread only

2 Thread-local

3 Attributes

4 Variables

5 Thread/cfthread

Other scopes are checked in the standard scope checking order.


Locking thread data and resource access
When an application uses multiple threads, be careful to ensure that the threads do not simultaneously attempt to use
or modify shared resources that are not themselves thread-safe, including the following items:

· If multiple threads modify a Variables or Request scope variable, use a Request scope lock to control access to the
    code that uses the variable to prevent deadlocks and race conditions. Similarly, use a Request scope lock around
    code that accesses built-in data structures or subscopes of the Variables scope, such as the Forms variable, that you
    change in multiple threads.

· Multiple threads should not try to access any other shared resource simultaneously. For example, do not use the
    same FTP connection from multiple threads. To prevent this behavior, place the code that uses the resource in
    named cflock tags. Use the same name attribute for all cflock tags around code that uses a specific resource.

For more information on locking code, see cflock and "Locking code with cflock" on page 318.


Metadata variables
The Thread scope contains the following variables that provide information about the thread, called metadata.


 Variable              Description

 Elapsedtime           The amount of processor time that has been spent handling the thread.

 Error                 A ColdFusion error structure that contains the keys that you can access in a cfcatch tag. This variable has a value
                       only if an unhandled error occurred during thread processing. For information on handling thread errors, see
                       "Handling ColdFusion thread errors" on page 336.

 Name                  The thread name.

 Output                Output text that the thread generates. Threads cannot display output directly. For more information see "Handling
                       thread output" on page 336.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                          335
Developing CFML Applications




 Variable              Description

 Priority              The thread processing priority, as specified when you created the thread.

 Starttime             The time at which the thread began processing.

 Status                The current status of the thread. For information on using the Status in an application, see "Using the thread status"
                       on page 336.


As with other variables in the Thread scope, thread metadata is available to all of a page's threads by specifying the
thread name as a variable prefix. For example, the page thread can get the current elapsed time of the myThread1
thread from the myThread1.ElapsedTime variable.

The metadata is available from the time that you create the thread until the time when the page and all threads started
on the page complete processing, even if the page finishes before the threads finish. This way, you can get thread
output, error information, and processing information during and after the time when the thread is processing.


Working with threads
Multi-threaded applications use several building blocks, including the following:

· Starting threads in loops

· Getting information about the thread processing status

· Displaying thread results

· Handling thread errors

· Using database transactions with threads


Starting threads inside loops
Because threads run asynchronously, page level variables can change during thread execution. As a result of this
behavior, if you start threads inside a cfloop, and code inside the threads uses the value of the loop iterator (like the
index variable, query name, list item), pass the loop iterator to the thread as an attribute.

The following example shows the use of threads inside a loop. It uses an indexed cfloop tag to start five threads. Each
thread gets the current loop index value in a threadIndex attribute. The thread adds an array entry with the
threadIndex attribute value of the thread and the current value of the page cfloop index, pageIndex. After joining
the threads, the page displays the array contents. When you run the example, particularly if you run it multiple times,
you see that at the time the thread saves data to the array, the value of pageIndex has incremented past the
threadIndex value, and multiple threads often have the same pageIndex value; but the multiple threads always have
the correct threadIndex value.

<cfloop index="pageIndex" from="1" to="5">
      <cfthread name="thr#pageIndex#" threadIndex="#pageIndex#" action="run">
          <cfset Variables.theOutput[threadIndex]="Thread index attribute:" &
                threadIndex & "&nbsp;&nbsp; Page index value: " & pageIndex>
      </cfthread>
</cfloop>


<cfthread action="join" name="thr1,thr2,thr3,thr4,thr5" timeout=2000/>


<cfloop index="j" from="1" to="5">
      <cfoutput>#theOutput[j]# <br /></cfoutput>
</cfloop>




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                336
Developing CFML Applications




Using the thread status
The Thread scope status metadata variable lets the page, or any other thread started by the page, determine the status
of any thread. The page processing code can then take a necessary action, for example, if the thread has terminated
abnormally or has hung. The status variable can have the following values:


 Value                       Meaning

 NOT_STARTED                 The thread has been queued but is not processing yet.

 RUNNNG                      The thread is running normally.

 TERMINATED                  The thread stopped running as a result of one of the following actions:

                             · A cfthread tag with a terminate action stopped the thread.

                             · An error occurred in the thread that caused it to terminate.

                             · A ColdFusion administrator stopped the thread from the Server Monitor.

 COMPLETED                   The thread ended normally.

 WAITING                     The thread has run a cfthread tag with action="join", and one or more of the threads being joined have
                             not yet completed.


Applications can check the thread status to manage processing. For example, an application that requires results from
a thread specifies a time-out when it joins the thread; in this case, it can check for the COMPLETED status to ensure
that the thread has completed processing and the join did not just result from a time-out. Similarly, an application can
check the status value of threads that might not start or might not complete normally, and terminate it if necessary.
The example in "Ending a thread" on page 330 checks thread status and terminates any threads with RUNNING or
NOT_STARTED status.


Handling thread output
To prevent conflicts, only the page thread displays output. Therefore, named threads have the following limitations:

· ColdFusion places all output that you generate inside a thread, such as HTML and plain text, or the generated
    output of a cfoutput tag, in the Thread scope output metadata variable. The page-level code can display the
    contents of this variable by accessing the threadName.output variable.

· All tags and tag actions that directly send output to the client (instead of generating page text such as HTML
    output), do not work inside the thread. For example, to use the cfdocument or cfreport tags in a thread, specify
    a filename attribute; to use a cfpresentation tag, use a directory attribute.


Handling ColdFusion thread errors
If an error occurs in a thread, page-level processing is not affected, and ColdFusion does not generate an error message.
If you do not handle the error by using a try/catch block in the thread code, the thread with the error terminates and
the page-level code or other threads can get the error information from the thread metadata Error variable and handle
the error appropriately.

You cannot use page- or application-based error handling techniques to manage errors that occur during thread
execution. For that reason, you cannot use the cferror tag or the onError application event handler for thread errors.
Instead, use either of the following techniques:

1 Use cftry/cfcatch tags or try/catch CFScript statements in the cfthread body to handle the errors inside the
    thread.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        337
Developing CFML Applications




2 Handle the error outside the thread by using the thread error information that is available to the page and other
   threads in the Thread scope threadName.Error variable. Application code can check this variable for error
   information. For example, after you join to a thread that had an error, you could check the threadname.status
   variable for a value of terminated, which indicates that the thread terminated abnormally. You could then check
   the threadName.Error variable for information on the termination cause.


Handling database transactions
Database transactions cannot span threads. For example, consider a page with the following structure:

<cftransaction>
     <cfthread name ="t1" ...>
          <cfquery name="q1" ...>
                ...
          </cfquery>
     </cfthread>
     <cfquery name="q2" ...>
          ...
     </cfquery>
     <cfthread action="join" name="t1" ... />
</cftransaction>

In this case, query q1 is not included in the transaction that contains query q2. To include both queries in the
transaction, you must place the complete transaction in a single thread, using a structure such as the following:

<cfthread name ="t1" ...>
     <cftransaction>
          <cfquery name="q1" ...>
                ...
          </cfquery>
          <cfquery name="q2" ...>
                ...
          </cfquery>
     </cftransaction>
</cfthread>
<cfthread action="join" name="t1" ... />



Using ColdFusion tools to control thread use
The Tag Limit Settings section of the ColdFusion Administrator Server Settings > Request Tuning page lets you specify
a maximum number of cfthread-started threads that can run at one time. When ColdFusion reaches this maximum,
it queues additional cfthread requests and starts the queued threads when running threads end.


Example: getting multiple RSS feeds
The following example uses three threads to get the results of three RSS feeds. The user must submit the form with all
three feeds specified. The application joins the threads with a time-out of 6 seconds, and displays the feed titles and the
individual item titles as links.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                   338
Developing CFML Applications




<!--- Run this code if the feed URL form has been submitted. --->
<cfif isDefined("Form.submit")>
    <cfloop index="i" from="1" to="3">
         <!--- Use array notation and string concatenation to create a variable
                     for this feed. --->
         <cfset theFeed = Form["Feed"&i]>
         <cfif theFeed NEQ "">
              <!--- Use a separate thread to get each of the feeds. --->
              <cfthread action="run" name="t#i#" feed="#theFeed#">
                   <cffeed source="#feed#"
                          properties="thread.myProps"
                          query="thread.myQuery">
              </cfthread>
         <cfelse>
              <!--- If the user didn't fill all fields, show an error message. --->
              <h3>This example requires three feeds.<br />
              Click the Back button and try again.</h3>
              <cfabort>
         </cfif>
    </cfloop>


    <!--- Join the three threads. Use a 6 second timeout. --->
    <cfthread action="join" name="t1,t2,t3" timeout="6000" />


    <!--- Use a loop to display the results from the feeds. --->
    <cfloop index="i" from="1" to="3">
         <!--- Use the cfthread scope and associative array notation to get the
                   Thread scope. --->
         <cfset feedResult=cfthread["t#i#"]>
         <!--- Display feed information only if you got items,
                     for example, the feed must complete before the join. --->
         <cfif isDefined("feedResult.myQuery")>
              <cfoutput><h2>#feedResult.myProps.title#</h2></cfoutput>
              <cfoutput query="feedResult.myQuery">
                   <p><a href="#RSSLINK#">#TITLE#</a></p>
              </cfoutput>
         </cfif>
    </cfloop>


</cfif>


<!--- The form for entering the feeds to aggregate. --->
<cfform>
    <h3>Enter three RSS Feeds</h3>
    <cfinput type="text" size="100" name="Feed1" validate="url"
         value="http://rss.adobe.com/events.rss?locale=en"><br />
    <cfinput type="text" size="100" name="Feed2" validate="url"
         value="http://weblogs.macromedia.com/dev_center/index.rdf"><br />
    <cfinput type="text" size="100" name="Feed3" validate="url"
         value="http://rss.adobe.com/studio.rss?locale=en"><br />
    <cfinput type="submit" name="submit">
</cfform>




                                         

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       339
Developing CFML Applications




Securing Applications

Resource security (Adobe ColdFusion Standard) or sandbox security (Adobe ColdFusion Enterprise) restricts access to
specific resources, such as tags and files. You use the ColdFusion Administrator to configure sandbox or resource
security, and structure an application to take advantage of this security.

User security depends on a user identity. You can implement user security in Adobe ColdFusion applications.

For detailed information on using Administrator-controlled security features, see Configuring and Administering
ColdFusion.


ColdFusion security features
ColdFusion provides scalable, granular security for building and deploying your ColdFusion applications.

ColdFusion provides the following types of security resources:

Development ColdFusion Administrator is password-protected. Additionally, you can specify a password for access
to data sources from Dreamweaver. For more information on configuring Administrator security passwords, see the
ColdFusion Administrator online Help.

CFML features The CFML language includes the following features that you can use to enhance application security.

· The cfqueryparam tag: This tag helps prevent users from injecting malicious SQL expressions. For more
    information on using this tag for database security, see "Enhancing security with cfqueryparam" on page 416,

· Scriptprotect setting: This setting helps protect against cross-site scripting attacks. You set this value with the
    ColdFusion Administrator Enable Global Script Protection setting, in the Application.cfc This.scriptprotect
    variable, or in the corresponding cfapplication tag scriptprotect attribute. For more information on this
    feature, see cfapplication in the CFML Reference. For information on Application.cfc see "Defining the application
    and its event handlers in Application.cfc" on page 241.

· Encryption and hashing functions: The Encrypt, Decrypt, and Hash functions let you select a secure algorithm
    for encrypting and decrypting data or generating a hash "fingerprint." You can select from among several secure
    algorithms that underlying Java security mechanisms support. For encryption, these include, AES, Blowfish, DES
    and Triple DES. For more information, see the Encrypt, Decrypt, and Hash, functions in the CFML Reference.

· Data validation tools      ColdFusion includes a variety of tools for validating form input and other data values,
    including ways to ensure that users do not submit malicious form data. For information on data validation see
    "Validating Data" on page 743; for specific information on security and validation, see "Security considerations"
    on page 747.

Resource/Sandbox The ColdFusion Administrator can limit access to ColdFusion resources, including selected tags
and functions, data sources, files, and host addresses. In the Standard Edition, you configure a single set of resource
limitations that apply to all your ColdFusion applications.

In the Enterprise Edition, you can have multiple sandboxes, based on the location of your ColdFusion pages, each with
its own set of resource limitations. You can confine applications to secure areas, thereby flexibly restricting the access
that the application has to resources.

User ColdFusion applications can require users to log in to use application pages. You can assign users to roles
(sometimes called groups); ColdFusion pages can determine the logged-in user's roles or ID and selectively determine
what to do based on this information. User security is also called authentication and authorization security.

Note: You can also use the cfencode utility, located in the cf_root/bin directory, to obscure ColdFusion pages that you
distribute. Although this technique cannot prevent persistent hackers from determining the contents of your pages, it does
prevent inspection of the pages. The cfencode utility is not available on OS X.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                            340
Developing CFML Applications




About resource and sandbox security
ColdFusion provides two levels of resource-based security:

· ColdFusion Standard refers to its resource-based security as resource security. It lets you specify a single set of
    limitations on access to ColdFusion resources that apply to all ColdFusion applications.

· ColdFusion Enterprise refers to its resource-based security as sandbox security. Sandbox security is a superset of
    resource security. Sandbox security lets you create multiple sandboxes, each corresponding to a different directory.
    For each sandbox, you specify a set of resource limitations that apply to all ColdFusion pages in the sandbox
    directory and its subdirectories. If you create a sandbox that is a subdirectory of a sandbox, the subdirectory's rules
    override the parent directory's rules.

The ColdFusion Administrator Resource Security page (in Standard) and Sandbox Security page (in Enterprise) let
you enable resource-based security. In ColdFusion Standard, the page lets you configure the resource settings that
apply to all your ColdFusion applications. In ColdFusion Enterprise, the page lets you create sandboxes and configure
the resource limitations for each sandbox individually.


Resource control
ColdFusion lets you control access to the following resources:


 Resource                Description

 Data sources            Enables access to specified data sources.

 CF tags                 Prevents pages from using CFML tags that access external resources. You can prevent pages in the directory from
                         using any or all of the following tags:

                         cfcollection, cfcontent, cfcookie, cfdirectory, cfdocument, cfexecute, cffile, cfftp, cfgridupdate, cfhttp,
                         cfhttpparam, cfindex, cfinsert, cfinvoke, cfldap, cflog, cfmail, cfobject, cfobjectcache, cfpop, cfquery, cfregistry,
                         cfreport, cfschedule, cfsearch, cfstoredproc, cftransaction, cfupdate

 CF functions            Prevents pages from using CFML functions that access external resources. You can prevent pages from using any
                         or all of the following functions:

                         CreateObject (COM, Java, Web Service),DirectoryExists. ExpandPath, FileExists, GetBaseTemplatePath,
                         GetDirectoryFromPath, GetFileFromPath, GetGatewayHelper, GetProfileString, GetTempDirectory, GetTempFile,
                         GetTemplatePath, SendGatewayMessage, SetProfileString

 Files/directories       Sets read, write, execute, and delete access to specified directories, directory trees, or files.

 Server/ports            Controls access from ColdFusion to IP addresses and port numbers. You can specify host names or numeric
                         addresses, and you can specify individual ports and port ranges.


Note: For more information on configuring resource and sandbox security, see Configuring and Administering
ColdFusion and the ColdFusion Administrator online Help.


Sandbox security
In ColdFusion Enterprise, sandbox security lets you apply different sets of rules to different directory structures. Use
it to partition a shared hosting environment so that a number of applications with different purposes, and possibly
different owners, run securely on a single server. When multiple applications share a host, you set up a separate
directory structure for each application, and apply rules that let each application access only its own data sources and
files.

Sandbox security also lets you structure and partition an application to reflect the access rights that are appropriate to
different functional components. For example, if your application has both employee inquiry functions and HR
functions that include creating, accessing, and modifying sensitive data, you could structure the application as follows:

· HR pages go in one directory with access rules that enable most activities.



                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   341
Developing CFML Applications




· Employee pages go in another directory whose rules limit the files they modify and the tags they use.

· Pages required for both HR and employee functions go in a third directory with appropriate access rules.


About user security
User security lets your application use security rules to determine what it shows. It has two elements:

Authentication Ensures that a valid user is logged-in, based on an ID and password provided by the user. ColdFusion
(or, in some cases if you use web server authentication, the web server) maintains the user ID information while the
user is logged-in.

Authorization Ensures that the logged-in user is allowed to use a page or perform an operation. Authorization is
typically based on one or more roles (sometimes called groups) to which the user belongs. For example, in an employee
database, all users could be members of either the employee role or the contractor role. They could also be members
of roles that identify their department, position in the corporate hierarchy, or job description. For example, someone
could be a member of some or all of the following roles:

· Employees

· Human Resources

· Benefits

· Managers

Roles enable you to control access in your application resources without requiring the application to maintain
knowledge about individual users. For example, suppose you use ColdFusion for your company's intranet. The
Human Resources department maintains a page on the intranet on which all employees can access timely information
about the company, such as the latest company policies, upcoming events, and job postings. You want everyone to be
able to read the information, but you want only certain authorized Human Resources employees to be able to add,
update, or delete information.

Your application gets the user's roles from the user information data store when the user logs in, and then enables
access to specific pages or features based on the roles. Typically, you store user information in a database, LDAP
directory, or other secure information store.

You also use the user ID for authorization. For example, to let employees view customized information about their
salaries, job levels, and performance reviews. You certainly would not want one employee to view sensitive
information about another employee, but you would want managers to be able to see, and possibly update, information
about their direct reports. By employing both user IDs and roles, you ensure that only the appropriate people access
or work with sensitive data.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 342
Developing CFML Applications




The following image shows a typical flow of control for user authentication and authorization. Following sections
expand on this diagram to describe how you implement user security in ColdFusion.


                                    User requests a
                                           page.




                                         Is a user
                                                           Yes
                                        logged in



                                             No



                                  Display login form.


                          No


                               Use ID and password to
                               authenticate user and get
                               user's authorization roles.




                                       Is the user
                                    authenticated?



                                            Yes



                                       Log user in.




                               Process requested page.




 User is authenticated but
      not authorized.                 Is user in role
                            No
    Do not do secured            needed for activity?

         operations.



                                            Yes


                               User is authenticated and
                                       authorized.
                                Do secured operations.



Authenticating users
Use either, or both, of the following forms of authentication to secure your ColdFusion application:

· Web server authentication, where the web server authenticates the user and does not allow access to the website by
   users without valid login IDs

· Application authentication, where the ColdFusion application authenticates the user and does not allow access to
   the application by users without valid login IDs


Web server authentication
All major web servers support basic HTTP authentication. Some web servers also support other authentication
methods, including Digest HTTP authentication and Microsoft NTLM authentication.




                                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     343
Developing CFML Applications




Note: Dreamweaver and Studio MX do not support NTLM security with RDS. Therefore, you cannot use RDS with these
applications if the ColdFusion RDS servlet (cf_root/CFIDE/main/ide.cfm) is in a directory that is protected using NTLM
security.

In web server authentication, the web server requires the user to log in to access pages in a particular directory, as
follows:

1 When the user first requests a page in the secured directory, the web server notifies the browser that the requested
   page requires credentials (a user ID and password).

   Basic HTTP authentication sends the user ID and password in a base64-encoded string with each request. Use SSL
   (Secure Sockets Layer) for all page transactions, to protect the user ID and password from unauthorized access. For
   more information on SSL and the keytool utility, see "About LDAP Server Security" on page 475.

2 The browser prompts the user for the credentials.

3 The user supplies the credentials and the browser send the information back to the web server along with the
   original request.

4 The web server checks the user ID and password, using its own user authentication mechanism.

5 If the user logs in successfully, the browser caches the authentication information and sends it in an HTTP
   Authorization header with every subsequent page request from the user.

6 The web server processes the requested page and all future page requests from the browser that contain the HTTP
   Authorization header, if it is valid for the requested page.

You can use web server authentication without using any ColdFusion security features. In this case, you configure and
manage all user security through the web server's interfaces.

You can also use web server authentication with ColdFusion application authentication, and thus use ColdFusion
security for authorization. If the web server uses basic HTML authentication, the ColdFusion cflogin tag provides
access to the user ID and password that the user entered to log in to the web server. If the web server uses Digest or
NTLM authentication, the cflogin tag normally gets the user ID, but not the password.

As a result, your application rely on the web server to authenticate the user against its user and password information,
and does not have to display a login page. You use the cflogin and cfloginuser tags to log the user into the
ColdFusion user security system, and use the IsUserInAnyRole and GetAuthUser functions to ensure user
authorization. For more information on this form of security, see "A web server authentication security scenario" on
page 349.

Note: If a user has logged in using web server authentication and has not logged in using ColdFusion application
authentication, the GetAuthUser tag returns the web server user ID. You could use this feature to combine web server
authentication with application authorization based on the user's ID.


Application authentication
With application authentication, you do not rely on the web server to enforce application security. The application
performs all user authentication and authorization. The application displays a login page, checks the user's identity
and login against its own authorization store, such as an LDAP directory or database, and logs the user into
ColdFusion using the cfloginuser tag. The application then uses the IsUserInAnyRole and GetAuthUser functions
to check the user's roles or identity for authorization before running a ColdFusion page or specific code on a page. For
an example of application authentication use, see "An application authentication security scenario" on page 350.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       344
Developing CFML Applications




ColdFusion authentication storage and persistence
How ColdFusion application authentication information is maintained by the browser and ColdFusion, and therefore
how long it is available, depends on the following:

· Whether the user's browser enables cookies

· Whether the application supports the Session scope for login storage

Note: For detailed information on Session scope, see "Configuring and using session variables" on page 311. Cookie scope
contains the cookies that arthe browser sends; for more information on using cookies, see cfcookie in the CFML Reference.


Authentication and cookies
Because HTTP is connectionless, a login can last beyond a single web page viewing only if the browser provides a
unique identifier that software on the server uses to confirm that the current user is authenticated. Normally, this is
done by using memory-only cookies that are automatically destroyed when the user closes all open browser windows.
The specific cookies and how they are used depend on whether the application supports the Session scope for login
storage.

Note: For information on user logins without cookies, see "Using ColdFusion security without cookies" on page 344.


Using the Session scope
If you do the following, ColdFusion maintains login information in the Session scope instead of the Cookie scope:

· Enable the Session scope in the ColdFusion Administrator and the Application.cfc initialization code or
   cfapplication tag.

· Specify loginStorage="Session" in the Application.cfc initialization code or cfapplication tag.

   When ColdFusion maintains login information in the Session scope, it stores the authentication details in a
   Session.cfauthorization variable, and ColdFusion uses the session cookie information to identify the user. Session-
   based authentication has the following advantages over less persistent login storage:

· After the user logs in, the user ID and password are not passed between the server and the browser.

· The login information and the session share a single time-out. You do not have to manually synchronize sessions
   and logins.

· If you use server clusters, the Session scope login ID is available across the cluster. For more information on server
   clustering, see Configuring and Administering ColdFusion.

If you do not enable the Session scope, the authentication information is not kept in a persistent scope. Instead, the
detailed login information is placed in a memory-only cookie (CFAUTHORIZATION_applicationName) with a
base64-encoded string that contains the user name, password, and application name. The client sends this cookie to
the web server each time it makes a page request while the user is logged-in. Use SSL for all page transactions to protect
the user ID and password from unauthorized access.


Using ColdFusion security without cookies
Implement a limited-lifetime form of ColdFusion security if the user's browser does not support cookies. In this case
you do not use the cflogin tag, only the cfloginuser tag. It is the only time you should use the cfloginuser tag
outside a cflogin tag.

Without browser cookies, the effect of the cfloginuser tag is limited to a single HTTP request. Provide your own
authentication mechanism and call cfloginuser on each page on which you use ColdFusion login identification.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                              345
Developing CFML Applications




Using ColdFusion security tags and functions
ColdFusion provides the following tags and functions for user security:


 Tag or function              Purpose

 cflogin                      A container for user authentication and login code. The body of the tag runs only if the user is not logged
                              in. When using application-based security, you place code in the body of the cflogin tag to check the
                              user-provided ID and password against a data source, LDAP directory, or other repository of login
                              identification. The body of the tag includes a cfloginuser tag (or a ColdFusion page that contains a
                              cfloginuser tag) to establish the authenticated user's identity in ColdFusion.

 cfloginuser                  Identifies (logs in) a user to ColdFusion. Specifies the user's ID, password, and roles. This tag is typically used
                              inside a cflogin tag.

                              The cfloginuser tag requires three attributes, name, password, and roles, and does not have a body.
                              The roles attribute is a comma-delimited list of role identifiers to which the logged-in user belongs. All
                              spaces in the list are treated as part of the role names, so you should not follow commas with spaces.

                              While the user is logged-in to ColdFusion, security functions access the user ID and role information.

 cflogout                     Logs out the current user. Removes knowledge of the user ID and roles from the server. If you do not use
                              this tag, the user is automatically logged out as described in "Logging out users" on page 348.

                              The cflogout tag does not take any attributes, and does not have a body.

 cfNTauthenticate             Authenticates a user name and password against the NT domain on which ColdFusion server is running,
                              and optionally retrieves the user's groups.

 cffunction                   If you include a roles attribute, the function executes only when there is a logged-in user who belongs to
                              one of the specified roles.

 IsUserInAnyRole              Returns True if the current user is a member of the specified role.

 GetAuthUser                  Returns the ID of the currently logged-in user.

                              This tag first checks for a login made with cfloginuser tag. If none exists, it checks for a web server login
                              (cgi.remote_user.



Using the cflogin tag
The cflogin tag executes only if there is no currently logged-in user. It has the following three optional arguments
that control the characteristics of a ColdFusion login:


 Attribute                     Use

 idleTimeout                   If no page requests occur during the idleTimeout period, ColdFusion logs out the user. The default is 1800
                               seconds (30 mins). This is ignored if login information is stored in the Session scope.

 applicationToken              Limits the login validity to a specific application as specified by a ColdFusion page's cfapplication tag. The
                               default value is the current application name.

 cookieDomain                  Specifies the domain of the cookie used to mark a user as logged-in. You use cookieDomain if you have a
                               clustered environment (for example, x.acme.com, x2.acme.com, and so on). This lets the cookie work for
                               all the computers in the cluster.



Login identification scope and the applicationToken attribute
The login identification created by the cflogin tag is valid only for pages within the directory that contains the page
that uses the cflogin tag and any of its subdirectories. Therefore, if a user requests a page in another directory tree,
the current login credentials are not valid for accessing those pages. This security limitation lets you use the same user
names and passwords for different sections of your application (for example, a UserFunctions tree and a
SecurityFunctions tree) and enforce different roles to the users depending on the section.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           346
Developing CFML Applications




ColdFusion uses the applicationToken value to generate a unique identifier that enforces this rule. The default
applicationToken value is the current application name, as specified by a cfapplication tag or Application.cfc
unitization code. In normal use, you need not specify an applicationToken value in the cflogin tag.


Specifying the Internet domain
Use the cookieDomain attribute to specify the domain of the cookie used to mark a user as logged-in. You use
cookieDomain if you have a clustered environment (for example, www.acme.com, www2.acme.com, and so on). This
lets the cookie work for all computers in the cluster. For example, to ensure that the cookie works for all servers in the
acme.com domain, specify cookieDomain=".acme.com". To specify a domain name, start the name with a period.

Important: Before setting the cookie domain, consider the other applications or servers in the broader domain might have
access to the cookie. For example, a clustered payroll application at payroll1.acme.com, payroll2.acme.com, and so on,
might reveal sensitive information to the test computer at test.acme.com, if the cookie domain is broadly set to
".acme.com".


Getting the user ID and password
The cflogin tag has a built-in cflogin structure that contains two variables, cflogin.username and cflogin.password,
if the page is executing in response to any of the following:

· Submission of a login form that contains input fields with the names j_username and j_password.

· A request that uses HTTP Basic authentication and, therefore, includes an Authorization header with the user name
    and password.

· A message from the Flash Remoting gatewayConnection object that has the setCredentials method set.

· A request that uses NTLM or Digest authentication. In this case, the user name and password are hashed using a
    one-way algorithm before they are placed in the Authorization header; ColdFusion gets the user name from the web
    server and sets the cflogin.password value to the empty string.

You use the first three techniques with application authentication, and the last technique with web server
authentication. The cflogin structure provides a consistent interface for determining the user's login ID and password,
independent of the technique that you use for displaying the login form.

Important: Login forms send the user name and password without encryption. Basic HTTP authentication sends the user
name and password in a base64-encoded string with each request; this format can easily be converted back to plain text.
Use these techniques only with https requests, or when you are not concerned about password security.

Provide login information to your application for authentication as follows:


Use a login form to get user information
When you build an application that gets the User ID and password using a login form, the cflogin tag checks for the
existence of a cflogin structure containing the user's login information. If the structure does not exist, it displays a login
form, typically using a cfinclude tag on a login page; the following code shows this use.

In the Application.cfc onRequestStart method, or a ColdFusion page or CFC method called by the method, you have
the following:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           347
Developing CFML Applications




 <cflogin>
     <cfif NOT IsDefined("cflogin")>
           <cfinclude template="loginform.cfm">
           <cfabort>
       <cfelse>
     <!--- Code to authenticate the user based on the cflogin.user and
           cflogin.password values goes here. --->
     <!--- If User is authenticated, determine any roles and use a line like the
           following to log in the user. --->
           <cfloginuser name="#cflogin.name#" Password = "#cflogin.password#"
                roles="#loginQuery.Roles#">
     </cfif>
</cflogin>

A simple login form looks like the following:

 <cfform name="loginform" action="#CGI.script_name#?#CGI.query_string#"
           method="Post">
     <table>
       <tr>
                <td>user name:</td>
                <td><cfinput type="text" name="j_username" required="yes"
                     message="A user name is required"></td>
       </tr>
       <tr>
                <td>password:</td>
                <td><cfinput type="password" name="j_password" required="yes"
                     message="A password is required"></td>
       </tr>
     </table>
     <br>
     <input type="submit" value="Log In">
</cfform>


Use a browser dialog box to get user information
Application authentication does not require you to use a login form; you can rely on the browser to display its standard
login dialog box, instead. To do so, your cflogin tag body returns an HTTP status 401 to the browser if the user is not
logged in or if the login fails; that is, if it does not have a valid cflogin structure. The browser displays its login dialog
box. When the user clicks the login button on the dialog box, the browser returns the login information as an HTTP
Authorization header to ColdFusion, which places the information in the cflogin tag's cflogin structure.

This technique has the advantage of simplicity; you do not need a login form and the user gets a familiar-looking login
page. Be careful of security issues, however. The browser sends the user name and password in a base64-encoded
string, not just when the user logs in, but with each request. Use SSL (Secure Sockets Layer) for all page transactions
to protect the user ID and password from unauthorized access.

Note: Ensure that your web server is configured correctly to support browser-based login forms for this use. For example,
in IIS 5, enable anonymous access and disable Basic authentication and Integrated Windows authentication.

The following cflogin tag tells the browser to display a login form if the user has not logged in:




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     348
Developing CFML Applications




 <cflogin>
     <cfif NOT IsDefined("cflogin")>
          <cfheader statuscode="401">
          <cfheader name="www-Authenticate" value="Basic
               realm=""MM Wizard #args.authtype# Authentication""">
     </cfif>
     <cfabort>
     <cfelse>
          <!--- code to authenticate the user based on the cflogin.user and
               cflogin.password values goes here. --->
</cflogin>


Log in a user using Flash Remoting
If you are developing a Rich Internet Application with Flash and Flash Remoting, your ColdFusion application does
not need to be coded specially for a Flash login. The Flash Remoting gateway makes the user ID and password available
to the cflogin tag in the cflogin structure.

In your Flash code, you use the ActionScript SetCredentials method to send login information to ColdFusion. Your
Flash SWF file displays the user ID and password fields, and uses their contents in the setCredentials method, as
follows:

 if (inited == null)
{
     inited = true;
     NetServices.setDefaultGatewayUrl("http://localhost/flashservices/gateway");
     gatewayConnection = NetServices.createGatewayConnection();
     gatewayConnection.setCredentials(userID, password);
     myService = gatewayConnection.getService("securityTest.thecfc", this);
}

For more information on using Flash Remoting, see "Using the Flash Remoting Service" on page 606 and "Using Flash
Remoting Update" on page 619.


Logging out users
After a user logs in, the ColdFusion user authorization and authentication information remains valid until any of the
following happens:

· The application uses a cflogout tag to log out the user, usually in response to the user clicking a log-out link or
   button.

· If your application uses the Session scope for login information, the session ends.

· If your application does not use the Session scope for login information, the user does not request a new page for
   the cflogin tag idleTimeout period.

· If your application does not use Session scope for login information, or if you use J2EE-based session identification,
   the user closes all browser windows.

Logging out a user by using the cflogout tag does not close the user's session, but if you use session login storage, it
does remove the login information (the Session.cfauthorization variable) from the Session scope. For more
information on ending sessions, see "Ending a session" on page 315.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         349
Developing CFML Applications




Important: If you use web server­based authentication or any form authentication that uses a Basic HTTP
Authorization header, the browser continues to send the authentication information to your application until the user
closes the browser, or in some cases, all open browser windows. As a result, after the user logs out and your application
uses the cflogout tag, until the browser closes, the cflogin structure in the cflogin tag will contain the logged-out user's
UserID and password. If a user logs out and does not close the browser, another user can access pages with the first user's
login.


Security scenarios
There are two detailed security scenarios. The first scenario uses the web server to perform the authentication against
its user and password database. The second scenario uses ColdFusion for all authentication and authorization.


A web server authentication security scenario
An application that uses web server authentication could work as follows. The example in "Web server­based
authentication user security example" on page 354 ­ implements this scenario.

1 When the user requests a page from a particular directory on the server for the first time after starting the browser,
    the web server displays a login page and logs in the user. The web server handles all user authentication.

2 Because the user requested a ColdFusion page, the web server hands the request to ColdFusion.

3 When ColdFusion receives a request for a ColdFusion page, it instantiates the Application.cfc and runs
    onRequestStart method. If you use an Application.cfm page in place of the Application.cfc, it runs the contents
    of the Application.cfm page before it runs the requested page. The onRequestStart method or Application.cfm
    page contains a cflogin tag. ColdFusion executes the cflogin tag body if the user is not logged into ColdFusion.
    The user is logged in if the cfloginuser tag has run successfully for this application and the user has not been
    logged out.

4 Code in the cflogin tag body uses the user ID and password from the browser login, contained in the cflogin.name
    and cflogin.password variables, as follows. (With Digest or NTLM web server authentication, the cflogin.password
    variable is the empty string.)

    a It checks the user's name against information it maintains about users and roles. In a simple case, the application
       has two roles, one for users and one for administrators. The CFML assigns the Admin role to any user logged
       on with the user ID Admin and assigns the User role to all other users.

    b It calls the cfloginuser tag with the user's ID, password, and roles, to identify the user to ColdFusion.

5 Application.cfc or the Application.cfm page completes processing, and ColdFusion processes the requested
    application page.

6 The application uses the IsUserInAnyRole function to check whether the user belongs to a role before it runs
    protected code that must be available only to users in that role.

7 The application uses the GetAuthUser function to determine the user ID; for example, to display the ID for
    personalization. It can also use the ID as a database key to get user-specific data.

Important: If you use web server­based authentication or any form authentication that uses a Basic HTTP
Authorization header, the browser continues to send the authentication information to your application until the user
closes the browser, or in some cases, all open browser windows. As a result, after the user logs out and your application
uses the cflogout tag, until the browser closes, the cflogin structure in the cflogin tag will contain the logged-out user's
UserID and password. If a user logs out and does not close the browser, another user can access pages with the first user's
login.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    350
Developing CFML Applications




An application authentication security scenario
An application that does its own authentication works as follows. The example in "Application-based user security
example" on page 356 implements this scenario.

1 Whenever ColdFusion receives a request for a ColdFusion page, it instantiates the Application.cfc and runs the
   onRequestStart method. If you use an Application.cfm page in place of Application.cfc, ColdFusion runs the
   contents of the Application.cfm page before it runs the requested page. The onRequestStart method or
   Application.cfm page contains the cflogin tag. ColdFusion executes the cflogin tag body if the user is not logged
   in. A user is logged in if the cfloginuser tag has run during the current session and the user had not been logged
   out by a cflogout tag.

2 Code in the cflogin tag body checks to see if it has received a user ID and password, normally from a login form.

3 If there is no user ID or password, the code in the cflogin tag body displays a login form that asks for the user's
   ID and password.

   The form posts the login information back to the originally requested page, and the cflogin tag in the
   onRequestStart method or the Application.cfm page runs again. This time, the cflogin tag body code checks the
   user name and password against a database, LDAP directory, or other policy store, to ensure that the user is valid
   and get the user's roles.

4 If the user name and password are valid, the cflogin tag body code calls the cfloginuser tag with the user's ID,
   password, and roles, to identify the user to ColdFusion.

5 When the user is logged in, application pages use the IsUserInAnyRole function to check whether the user belongs
   to a role before they run protected code that must be available only to users in that role.

   The application can use the GetAuthUser function to determine the user ID; for example, to display the ID for
   personalization. It can also use the ID as a database key to get user-specific data.

6 Each application page displays a link to a logout form that uses the cflogout tag to log out the user. Typically, the
   logout link is in a page header that appears in all pages. The logout form can also be in the Application.cfc (for
   example, in the onRequestStart or onRequestEnd method) or on the Application.cfm page.

Although this scenario shows one method for implementing user security, it is only an example. For example, your
application could require users to log in for only some pages, such as pages in a folder that contains administrative
functions. When you design your user security implementation, remember the following:

· Code in the cflogin tag body executes only if there is no user logged in.

· With application authentication, you write the code that gets the identification from the user and tests this
   information against a secure credential store.

· After you have authenticated the user, you use the cfloginuser tag to log the user into ColdFusion.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                            351
Developing CFML Applications




The following image shows this flow of control. For simplicity, it omits the log-out option.


                                                  User requests a                Step 1
                                                        page.



                                                 Applicaton.cfc or
                                               Application.cfm   runs
                                                         rst.


                  In Application.cfc
                 (or Application.cfm)
                  and related les
                                                      Is a user
                                                                             Yes
                                                     logged in?

     Form posts to         Step 3
       originally
      requested                                           No
        page.                             In cogin tag

                                                                       Step 2

                            Display                Has the user
     User submits
                              login     No      submitted a login ID
        form.
                              form.               and password?




                                                         Yes
                                                                       Step 4

                                              Use ID and password to
                                             request user's roles from
                                                  login database.




                                                     Is the user
                                     No
                                                        valid?



                                                         Yes



                                           Use coginuser to log in the
                                              user and identify roles.




                  In requested page

                                                                IsUserInRole     Step 5

                                                                   function
                      User is valid but
                     not authorized; do            Is user in role
                       not do secured           needed for activity?
                         operations.




                                                 User is valid and
                                                   authorized ; do
                                                      secured
                                                    operations .




Implementing user security
You can implement security in the following ways.




                                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        352
Developing CFML Applications




Using the Dreamweaver Login Wizard
ColdFusion installs a Login Wizard command in the Dreamweaver Commands menu that generates a skeleton set of
pages for managing user authentication and authorization.

The wizard asks you to select how to authenticate the login information. Select one of the following options:

· Simple Specify a single user ID and password in the wizard. All users must enter this information to log in. Use
   this option for testing, or use the generated files as a template where you can replace the authentication code with
   more complex code. For example, to verify the ID and password against a database.

· NT domain Specify an NT domain in the wizard, and the wizard generates code that queries the domain.

· LDAP Specify the LDAP server and port, the user name and password required to access the login data, and the
   distinguished name to use to start the search for the user name. The wizard generates the code to query the LDAP
   server with the user ID and password.

The wizard asks you to select one of the following options for displaying the request for login information:

· Browser Dialog Box

· ColdFusion Login Form


Structure code generated by the Login Wizard
The wizard generates or modifies the following files in the directory or site that you specify:

Application.cfc If this file does not exist, the wizard creates it with a single onRequestStart method; it does not
specify an application name or any other methods. If the file exists, but does not have an onRequestStart method, it
adds the method. If Application.cfc and the onRequestStart method exist, the wizard inserts the required code at the
beginning of the method. The resulting onRequestStart method has a cfinclude tag that specifies
mm_wizard_application_include.cfm; it also has a simple form with a logout button, which appears at the top of each
page in the application.

Note: If the wizard creates the Application.cfc file, change the file to specify the application name. For more information
on Application.cfc, see "Designing and Optimizing a ColdFusion Application" on page 235.

mm_wizard_application_include.cfm The Login Wizard uses the information specified in the wizard fields to set
several CFC method arguments. It then uses them to invoke the performlogin method of the master login CFC,
mm_wizard.authenticate.

mm_wizard_authenticate.cfc This CFC contains all of the user authentication and login logic. The CFC consists of the
following methods:

· The ntauth, ldapauth, and simpleauth authentication methods check the user's name and ID against the valid login
   information, and return information about whether the user is authenticated. For the details of how they
   authenticate the user and the specific return values, see the methods.

· The performLogin method is the master login method. It contains the cflogin tag, which displays the login form
   and calls the required authentication method. If the authentication method's return argument indicates a valid
   user, the method logs the user in.

· The logout method logs out a user. If you specified Browser Dialog Box as the login page type, it also calls the
   closeBrowser method to close the browser window. This behavior is necessary because the browser continues to
   send the old login credentials after the user logs out, and the cflogin tag will automatically use them and log the
   user in again.

· The closeBrowser method closes the browser window or tells the user to close the browser window to complete the
   logout, depending on the browser type.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  353
Developing CFML Applications




mm_wizard_login.cfm This file contains a ColdFusion login form. The wizard generates this file for all options, but
does not use it if you specify Browser Dialog login.

index.cfm or mm_wizard_index.cfm The wizard generates an index.cfm page if the directory does not have one;
otherwise, creates an mm_wizard_index.cfm page. These pages let you test the generated login code before you
implement your application, or without using any of your standard application pages. To test your login, open the
index.cfm page in your browser.


Modifying the login code for your application
The Login Wizard creates a basic framework for authenticating a user. Customize this framework to meet the needs
of your application. Typical security-related changes include the following:

· Providing user-specific role information in the cflogin tag

· Authenticating users against a database


Providing user-specific role information
The Login Wizard sets all users in a single role. In mm_wizard_authenticate.cfc, the performlogin method is hard-
coded to set the role to "user." The authentication routines handle roles differently. (For the details, see the
mm_wizard_authenticate.cfc code.) If your application uses roles for authorization, change the authentication method
to get and return valid role information, and change the performlogin method to use the information in the roles
attribute of its cfloginuser tag.


Authenticating users against a database
If you use a database to maintain user IDs and passwords, create your login framework by specifying simple
authentication, and modify the code to use the database. The following instructions describe a simple way to change
the code to use a database. They do not include all the cleanup work (particularly, removing the hard-coded user name
and password) needed for a well-formatted application.

Replace the following code:

 <cfif sUserName eq uUserName AND sPassword eq uPassword>
     <cfset retargs.authenticated="YES">
<cfelse>
     <cfset retargs.authenticated="NO">
</cfif>
<cfreturn retargs>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       354
Developing CFML Applications




With code like the following:
 <cfquery name="loginQuery" dataSource="#Application.DB#" >
     SELECT *
     FROM Users
     WHERE UserName = <cfqueryparam value="#uUserName#" CFSEQLType=
          'CF_SQL_VARCHAR'AND password = <cfqueryparam value="#uPassword#"
          CFSEQLType='CF_SQL_VARCHAR'>
</cfquery>


<cfif loginQuery.recordcount gt 0>
     <cfset retargs.authenticated="YES">
     <cfset retargs.roles=loginQuery.roles>
<cfelse>
     <cfset retargs.authenticated="NO">
</cfif>
<cfreturn retargs>

Note: For greater security, consider using a hashed password. Do not store the password directly in the database; instead,
use the hash function to create a secure password fingerprint, and store it in the database. When the user provides a
password, use the Hash function on the submitted string and compare it with the value in the database.


Web server­based authentication user security example
The following example shows how to implement user security using web-server­based basic authentication and two
roles, user and administrator.

This example has two ColdFusion pages:

1 The Application.cfc page logs the user into the ColdFusion security system and assigns the user to specific roles
   based on the user's ID.

   This page also includes the one-button form and logic for logging out a user, which appears at the top of each page.

2 The securitytest.cfm page is a sample application page. It displays the logged-in user's roles.

This simple example does not provide a user log-out interface. Test the security behavior by adding your own pages to
the same directory as the Application.cfc page.


Example: Application.cfc
The Application.cfc page consists of the following:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                             355
Developing CFML Applications




<cfcomponent>
<cfset This.name = "Orders">
<cffunction name="OnRequestStart">
    <cfargument name = "request" required="true"/>
    <cflogin>
         <cfif IsDefined("cflogin")>
              <cfif cflogin.name eq "admin">
                    <cfset roles = "user,admin">
              <cfelse>
                    <cfset roles = "user">
              </cfif>
              <cfloginuser name = "#cflogin.name#" password = "#cflogin.password#"
                    roles = "#roles#" />
         <cfelse>
              <!--- This should never happen. --->
              <h4>Authentication data is missing.</h4>
              Try to reload the page or contact the site administrator.
         <cfabort>
         </cfif>
    </cflogin>
</cffunction>
</cfcomponent>


Reviewing the code
The Application.cfc onRequestStart method executes before the code in each ColdFusion page in an application. For
more information on the Application.cfc page and when it is executed, see "Designing and Optimizing a ColdFusion
Application" on page 235.

The following table describes the CFML code in Application.cfc and its function:


Code                                                 Description

<cfcomponent>                                        Identifies the application and starts the onRequestStart method that
<cfset This.name = "Orders">                         runs at the starts of each request. The login information on this page only
<cffunction name="OnRequestStart">
                                                     applies to this application.
<cfargument name = "request" required="true"/>

<cflogin>                                            Executes if there is no logged-in user.
<cfif IsDefined("cflogin")>
<cfif cflogin.name eq "admin">                       Makes sure that the user is correctly logged in by the web server.
<cfset roles = "user,admin">                         (Otherwise, there would be no cflogin variable.)
<cfelse>
<cfset roles = "user">                               Sets a roles variable based on the user's ID. Assigns users named "admin"
</cfif>                                              to the admin role. Assigns all other users to the users role.

<cfloginuser name = "#cflogin.name#" password =      Logs the user into the ColdFusion security system and specifies the user's
"#cflogin.password#" roles = "#roles#" />            password, name, and roles. Gets the password and name directly from the
                                                     cflogin structure.

<cfelse>                                             This code should never run, but if the user somehow got to this page
<!--- This should never happen. --->                 without logging in to the web server, this message would display and
<h4>Authentication data is missing.</h4>
                                                     ColdFusion would stop processing the request.
Try to reload the page or contact the site
administrator.
<cfabort>

</cfif>                                              Ends the if/else block.
</cflogin>
</cffunction>                                        Ends the cflogin tag body.
</cfcomponent>
                                                     Ends the onRequestStart method.

                                                     Ends the Application component.




                                           

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  356
Developing CFML Applications




Example: securitytest.cfm
The securitytest.cfm page shows how any application page uses ColdFusion user authorization features. The web
server ensures the existence of an authenticated user, and the Application.cfc page ensures that the user is assigned to
roles the page content appears. The securitytest.cfm page uses the IsUserInAnyRole and GetAuthUser functions to
control the information that is displayed.

The securitytest.cfm page consists of the following:

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
<head>
     <title>Basic authentication security test page</title>
</head>


<body>
<cfoutput>
     <h2>Welcome #GetAuthUser()#!</h2>
</cfoutput>


ALL Logged-in Users see this message.<br>
<br>
<cfscript>
     if (IsUserInRole("admin"))
          WriteOutput("Users in the admin role see this message.<br><br>");
     if (IsUserInRole("user"))
          WriteOutput("Everyone in the user role sees this message.<br><br>");
</cfscript>


</body>
</html>


Reviewing the code
The following table describes the securitytest.cfm page CFML code and its function:


Code                                                        Description

 <cfoutput>                                                 User is already logged in by Application.cfc. Displays a welcome
 <h2>Welcome #GetAuthUser()#!</h2>                          message that includes the user's login ID.
 </cfoutput>

ALL Logged-in Users see this message.<br>                   Displays this message in all cases. The page does not display until a
 <br>                                                       user is logged in.

<cfscript>                                                  Tests whether the user belongs to each of the valid roles. If the user is
 if (IsUserInRole("admin"))                                 in a role, displays a message with the role name.
 WriteOutput("Users in the admin role see this
message.<br><br>");                                         The user sees one message per role to which the user belongs.
 if (IsUserInRole("user"))
 WriteOutput("Everyone in the user role sees this
message.<br><br>");
 </cfscript>



Application-based user security example
The following example shows how to implement user security by authenticating users and then allowing users to see
or use only the resources that they are authorized to access.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        357
Developing CFML Applications




This example has three ColdFusion pages:

· The Application.cfc page contains the authentication logic that checks whether a user is logged in, requests the
  login page if the user is not logged in, and authenticates the data from the login page. If the user is authenticated, it
  logs the user in.

  This page also includes the one-button form and logic for logging out a user, which appears at the top of each page.

· The loginform.cfm page displays the login form. The code on this page could also be included in Application.cfc.

· The securitytest.cfm page is a sample application page. It displays the logged-in user's roles.

Test the security behavior by adding your own pages to the same directory as the Application.cfc page.

The example gets user information from the LoginInfo table of the cfdocexamples database that is installed with
ColdFusion. You can replace this database with any database containing UserID, Password, and Roles fields. The
sample database contains the following data:


UserID                  Password                Roles

BobZ                    Ads10                   Employee,Sales

JaniceF                 Qwer12                  Contractor,Documentation

RandalQ                 ImMe                    Employee,Human Resources,Manager


Because spaces are meaningful in roles strings, do not follow the comma separators in the Roles fields with spaces.


Example: Application.cfc
The Application.cfc page consists of the following:

<cfcomponent>
<cfset This.name = "Orders">
<cfset This.Sessionmanagement="True">
<cfset This.loginstorage="session">


<cffunction name="OnRequestStart">
     <cfargument name = "request" required="true"/>
     <cfif IsDefined("Form.logout")>
          <cflogout>
     </cfif>


     <cflogin>
          <cfif NOT IsDefined("cflogin")>
               <cfinclude template="loginform.cfm">
               <cfabort>
          <cfelse>
               <cfif cflogin.name IS "" OR cflogin.password IS "">
                    <cfoutput>
                          <h2>You must enter text in both the User Name and Password fields.
                          </h2>
                    </cfoutput>
                    <cfinclude template="loginform.cfm">
                    <cfabort>
               <cfelse>
                    <cfquery name="loginQuery" dataSource="cfdocexamples">
                    SELECT UserID, Roles
                    FROM LoginInfo
                    WHERE



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                            358
Developing CFML Applications




                          UserID = '#cflogin.name#'
                          AND Password = '#cflogin.password#'
                    </cfquery>
                    <cfif loginQuery.Roles NEQ "">
                          <cfloginuser name="#cflogin.name#" Password = "#cflogin.password#"
                              roles="#loginQuery.Roles#">
                    <cfelse>
                          <cfoutput>
                              <H2>Your login information is not valid.<br>
                              Please Try again</H2>
                          </cfoutput>
                          <cfinclude template="loginform.cfm">
                          <cfabort>
                    </cfif>
              </cfif>
         </cfif>
    </cflogin>


    <cfif GetAuthUser() NEQ "">
         <cfoutput>
                    <form action="securitytest.cfm" method="Post">
                    <input type="submit" Name="Logout" value="Logout">
              </form>
         </cfoutput>
    </cfif>


</cffunction>
</cfcomponent>


Reviewing the code
The Application.cfc page executes before the code in each ColdFusion page in an application. For more information
on the Application.cfc page and when it is executed, see "Designing and Optimizing a ColdFusion Application" on
page 235.

The following table describes the CFML code in Application.cfc and its function:


Code                                                      Description

<cfcomponent>                                             Identifies the application, enables session management, and enables
<cfset This.name = "Orders">                              storing login information in the Session scope.
<cfset This.Sessionmanagement="True">
<cfset This.loginstorage="session">                       Begins the definition of the onRequestStart method that runs at
                                                          the starts of each request.
<cffunction name="OnRequestStart">
<cfargument name = "request" required="true"/>

<cfif IsDefined("Form.logout")>                           If the user just submitted the logout form, logs out the user. The
<cflogout>                                                following cflogin tag runs as a result.
</cfif>

<cflogin>                                                 Runs if there is no logged-in user.
<cfif NOT IsDefined("cflogin")>
<cfinclude template="loginform.cfm">                      Tests to see if the user has submitted a login form. If not, uses
<cfabort>                                                 cfinclude to display the form. The built-in cflogin variable exists
                                                          and contains the user name and password only if the login form used
                                                          j_username and j_password for the input fields.

                                                          The cfabort tag prevents processing of any code that follows on this
                                                          page.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                             359
Developing CFML Applications




Code                                                   Description

<cfelse>                                               Runs if the user submitted a login form.
<cfif cflogin.name IS "" OR cflogin.password IS "">
<cfoutput>                                             Tests to make sure that both name and password have data. If either
<h2>You must enter text in both the User Name and      variable is empty, displays a message, followed by the login form.
Password fields. </h2>
</cfoutput>                                            The cfabort tag prevents processing of any code that follows on this
<cfinclude template="loginform.cfm">                   page.
<cfabort>

<cfelse>                                               Runs if the user submitted a login form and both fields contain data.
<cfquery name="loginQuery"
dataSource="cfdocexamples">                            Uses the cflogin structure's name and password entries to find the
SELECT UserID, Roles                                   user record in the database and get the user's roles.
FROM LoginInfo
WHERE
UserID = '#cflogin.name#'
AND Password = '#cflogin.password#'
</cfquery>

<cfif loginQuery.Roles NEQ "">                         If the query returns data in the Roles field, logs in the user using the
<cfloginuser name="#cflogin.name#" Password =          user's name and password and the Roles field from the database. In
"#cflogin.password#" roles="#loginQuery.Roles#">
                                                       this application, every user must be in some role.

<cfelse>                                               Runs if the query did not return a role. If the database is valid, this
<cfoutput>                                             means there was no entry matching the user ID and password.
<H2>Your login information is not valid.<br>
                                                       Displays a message, followed by the login form.
Please Try again</H2>
</cfoutput>                                            The cfabort tag prevents processing of any code that follows on this
<cfinclude template="loginform.cfm">
                                                       page.
<cfabort>

</cfif>                                                Ends the loginquery.Roles test code.
</cfif>
</cfif>                                                Ends the form entry empty value test.
</cflogin>
                                                       Ends the form entry existence test.

                                                       Ends the cflogin tag body.

<cfif GetAuthUser() NEQ "">                            If a user is logged-in, displays the Logout button.
<cfoutput>
<form action="securitytest.cfm" method="Post">         If the user clicks the button, posts the form to the application's
<input type="submit" Name="Logout" value="Logout">     (theoretical) entry page, index.cfm.
</form>
</cfoutput>                                            Application.cfc then logs out the user and displays the login form. If
</cfif>                                                the user logs in again, ColdFusion displays index.cfm.

</cffunction>                                          Ends the onRequestStart method
</cfcomponent>
                                                       Ends the Application component.



Example: loginform.cfm
The loginform.cfm page consists of the following:




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                             360
Developing CFML Applications




<H2>Please Log In</H2>
<cfoutput>
    <form action="#CGI.script_name#?#CGI.query_string#" method="Post">
          <table>
               <tr>
                    <td>user name:</td>
                    <td><input type="text" name="j_username"></td>
               </tr>
               <tr>
                    <td>password:</td>
                    <td><input type="password" name="j_password"></td>
               </tr>
          </table>
          <br>
          <input type="submit" value="Log In">
    </form>
</cfoutput>


Reviewing the code
The following table describes the loginform.cfm page CFML code and its function:


Code                                                      Description

<H2>Please Log In</H2>                                    Displays the login form.
<cfoutput>
<form
action="#CGI.script_name#?#CGI.query_string#"
method="Post">                                            Constructs the form action attribute from CGI variables, with a ?
<table>                                                   character preceding the query string variable. This technique works
<tr>                                                      because loginform.cfm is accessed by a cfinclude tag on
<td>user name:</td>
                                                          Application.cfc, so the CGI variables are those for the originally
<td><input type="text" name="j_username"></td>
                                                          requested page.
</tr>
<tr>
<td>password:</td>
<td><input type="password" name="j_password"></td>
                                                          The form requests a user ID and password and posts the user's input to
</tr>
</table>                                                  the page specified by the newurl variable.
<br>
                                                          Uses the field names j_username and j_password. ColdFusion
<input type="submit" value="Log In">
</form>                                                   automatically puts form fields with these values in the cflogin.name
</cfoutput>                                               and cflogin.password variables inside the cflogin tag.



Example: securitytest.cfm
The securitytest.cfm page shows how any application page can use ColdFusion user authorization features.
Application.cfc ensures the existence of an authenticated user before the page content appears. The securitytest.cfm
page uses the IsUserInAnyRole and GetAuthUser functions to control the information that is displayed.

The securitytest.cfm page consists of the following:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                361
Developing CFML Applications




<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
<head>
    <title>Security test page</title>
</head>


<body>
<cfoutput>
    <h2>Welcome #GetAuthUser()#!</h2>
</cfoutput>


ALL Logged-in Users see this message.<br>
<br>
<cfscript>
    if (IsUserInRole("Human Resources"))
         WriteOutput("Human Resources members see this message.<br><br>");
    if (IsUserInRole("Documentation"))
         WriteOutput("Documentation members see this message.<br><br>");
    if (IsUserInRole("Sales"))
         WriteOutput("Sales members see this message.<br><br>");
    if (IsUserInRole("Manager"))
         WriteOutput("Managers see this message.<br><br>");
    if (IsUserInRole("Employee"))
         WriteOutput("Employees see this message.<br><br>");
    if (IsUserInRole("Contractor"))
         WriteOutput("Contractors see this message.<br><br>");
</cfscript>


</body>
</html>


Reviewing the code
The following table describes the securitytest.cfm page CFML code and its function:


Code                                                      Description

<cfoutput>                                                Displays a welcome message that includes the user's login ID.
<h2>Welcome #GetAuthUser()#!</h2>
</cfoutput>

ALL Logged-in Users see this message.<br>                 Displays this message in all cases. The page does not display until a
<br>                                                      user is logged in.

<cfscript>                                                Tests whether the user belongs to each of the valid roles. If the user is
if (IsUserInRole("Human Resources"))                      in a role, displays a message with the role name.
WriteOutput("Human Resources members see this
message.<br><br>");                                       Users see one message per role that they belong.
if (IsUserInRole("Documentation"))
WriteOutput("Documentation members see this
message.<br><br>");
if (IsUserInRole("Sales"))
WriteOutput("Sales members see this
message.<br><br>");
if (IsUserInRole("Manager"))
WriteOutput("Managers see this message.<br><br>");
if (IsUserInRole("Employee"))
WriteOutput("Employees see this message.<br><br>");
if (IsUserInRole("Contractor"))
WriteOutput("Contractors see this
message.<br><br>");
</cfscript>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                             362
Developing CFML Applications




Using an LDAP directory for security information
LDAP directories are often used to store security information. The following example of a cflogin tag checks an
LDAP directory to authenticate the user and retrieve the user's roles.

For more information on using LDAP directories with ColdFusion, see "Managing LDAP Directories" on page 449.

<cfapplication name="Orders" sessionmanagement="Yes" loginstorage="Session">
<cflogin>
    <cfif isDefined("cflogin")>
         <!--- setting basic attributes --->
         <cfset LDAP_root = "o=mycompany.com">
         <cfset LDAP_server = "ldap.mycompany.com">
         <cfset LDAP_port = "389">


         <!--- Create the prefix and suffix parts of the user's DN. --->
         <cfset userPrefix = "cn=">
         <cfset userSuffix = ",ou=Users,o=mycompany.com">


         <!--- Concatenate the user's DN and use it to authenticate. --->
         <cfset LDAP_username = userPrefix&cflogin.name&userSuffix>


         <!--- This filter will look for groups for containing the user's ID. --->
         <cfset userfilter =
              "(&(objectClass=groupOfUniqueNames)(uniqueMember=#LDAP_username#))">


         <!--- Search for groups containing the user's dn.
              The groups represent the user's roles.
              NOTE: Your LDAP permissions must allow authenticated users to search.
              groups. --->
         <cftry>
              <cfldap action="QUERY"
                   name="auth"
                   attributes="cn"
                   referral="yes"
                   start="#LDAP_root#"
                   scope="SUBTREE"
                   server="#LDAP_server#"
                   port="#LDAP_port#"
                   filter="#userfilter#"
                   username="#LDAP_username#"
                   password="#cflogin.password#"
              >
              <cfcatch type="any">
                   <cfif FindNoCase("Invalid credentials", cfcatch.detail)>
                          <cfoutput>
                             <script>alert("User ID or Password invalid for user:




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                            363
Developing CFML Applications




                                   #cflogin.name#")</script>
                          </cfoutput>
                          <cfabort>
                    <cfelse>
                          <cfoutput>
                              <script>alert("Unknown error for user: #cflogin.name#
                                   #cfcatch.detail#")</script>
                          </cfoutput>
                          <cfabort>
                    </cfif>
              </cfcatch>
         </cftry>


<!--- If the LDAP query returned a record, the user is valid. --->
         <cfif auth.recordcount>
              <cfloginuser name="#cflogin.name#" password="#cflogin.password#"
                    roles="#valueList(auth.cn)#">
         </cfif>
    </cfif>
</cflogin>


Reviewing the code
The following table describes the code and its function. Comments and some tab characters have been removed for
brevity.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  364
Developing CFML Applications




Code                                                 Description

<cflogin>                                            Starts the cflogin tag body. Sets several variables to the values used as
<cfif isDefined("cflogin")>                          attributes in the cfldap tag.
<!--- setting basic attributes --->
<cfset LDAP_root = "o=mycompany.com">
<cfset LDAP_server = "ldap.mycompany.com">
<cfset LDAP_port = "389">

<!--- Create the prefix and suffix parts of the
user's DN. --->
<cfset userPrefix = "cn=">                           Sets prefix and suffix values used to create a distinquished name (dn) for
<cfset userSuffix =                                  binding to the LDAP server.
",ou=Users,o=mycompany.com">

<!--- Concatenate the user's DN and use it to
authenticate. --->                                   Creates the user's bind dn by concatenating the prefix and suffix with
<cfset LDAP_username =                               cflogin.name. This variable is used for authenticating the user to the LDAP
userPrefix&cflogin.name&userSuffix>
                                                     server.
<!--- This filter will look for groups for
containing the user's ID. --->                       Sets the filter used to search the directory and retrieve the user's group
<cfset userfilter =
                                                     memberships. The group membership represents the user's roles within the
"(&(objectClass=groupOfUniqueNames)(uniqueMemb
er=#LDAP_username#))">                               organization.


<cftry>                                              In a cftry block, uses the user's concatenated dn to authenticate to the
<cfldap action="QUERY"                               LDAP server and retrieve the common name (cn) attribute for groups to
name="auth"
                                                     which the user is a member. If the authentication fails the LDAP server
attributes="cn"
                                                     returns an error.
referral="yes"
start="#LDAP_root#"
scope="SUBTREE"
server="#LDAP_server#"
                                                     Note: The LDAP permissions must allow an authenticated user to read and
port="#LDAP_port#"
                                                     search groups in order for the query to return results.
filter="#userfilter#"
username="#LDAP_username#"
password="#cflogin.password#"
>

<cfcatch type="any">                                 Catches any exceptions.
<cfif FindNoCase("Invalid credentials",
cfcatch.detail)>                                     Tests to see if the error information includes the string "invalid credentials",
<cfoutput>                                           which indicates that either the dn or password is invalid. If so, displays a
<script>alert("User ID or Password invalid for       dialog box with an error message indicating the problem.
user: #cflogin.name#")</script>
</cfoutput>                                          Otherwise, displays a general error message.
<cfabort>
<cfelse>                                             If an error is caught, the cfabort tag ends processing of the request after
<cfoutput>
                                                     displaying the error description.
<script>alert("Unknown error for user:
#cflogin.name# #cfcatch.detail#")</script>           End of cfcatch and cftry blocks.
</cfoutput>
<cfabort>
</cfif>
</cfcatch>
</cftry>

<cfif auth.recordcount>                              If the authorization query returns a valid record, logs in the user. Uses the
<cfloginuser name="#cflogin.name#"                   valueList function to create a comma-separated list of the users retrieved
password="#cflogin.password#"
                                                     group memberships, and passes them in the cfloginuserroles attribute.
roles="#valueList(auth.cn)#">
</cfif>                                              Ends the initial isDefined("cflogin")cfif block .
</cfif>
</cflogin>                                           Ends the cflogin tag body




Developing Globalized Applications

Adobe ColdFusion lets you develop dynamic applications for the Internet. Users from different countries and
geographical areas access many ColdFusion applications. One design detail that you must consider is the globalization
of your application so that you can best serve customers in different areas.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       365
Developing CFML Applications




Introduction to globalization
Globalization lets you create applications for all of your customers in all the languages that you support. In some cases,
globalization can let you accept data input using a different character set than the one you used to implement your
application. For example, you can create a website in English that lets customers submit form data in Japanese. Or, you
can allow a request URL to contain parameter values entered in Korean.

Your application also can process data containing numeric values, dates, currencies, and times. Each of these types of
data can be formatted differently for different countries and regions.

You can also develop applications in languages other than English. For example, you can develop your application in
Japanese so that the default character encoding is Shift-JIS, your ColdFusion pages contain Japanese characters, and
your interface displays in Japanese.

Globalizing your application requires that you perform one or more of the following actions:

· Accept input in more than one language.

· Process dates, times, currencies, and numbers formatted for multiple locales.

· Process data from a form, database, HTTP connection, e-mail message, or other input formatted in multiple
   character sets.

· Create ColdFusion pages containing text in languages other than English.


Defining globalization
You might probably find several different definitions for globalization. Here, globalization is defined as an
architectural process where you place as much application functionality as possible into a foundation that can be
shared among multiple languages.

Globalization is composed of the following two parts:

Internationalization Developing language-neutral application functionality that can recognize, process, and respond
to data regardless of its representation. That is, whatever the application can do in one language, it can also do in
another. For example, think of copying and pasting text. A copy and paste operation should not be concerned with the
language of the text it operates on. For a ColdFusion application, you might have processing logic that performs
numeric calculations, queries a database, or performs other operations, independent of language.

Localization Taking shared, language-neutral functionality, and applying a locale-specific interface to it. Sometimes
this interface is referred to as a skin. For example, you can develop a set of menus, buttons, and dialog boxes for a
specific language, such as Japanese, that represents the language-specific interface. You then combine this interface
with the language-neutral functionality of the underlying application. As part of localization, you create the
functionality to handle input from customers in a language-specific manner and respond with appropriate responses
for that language.


Importance of globalization in ColdFusion applications
The Internet has no country boundaries. Customers can access websites from anywhere in the world, at any time, or
on any date. Unless you want to lock your customers into using a single language, such as English, to access your site,
consider globalization issues.

One reason to globalize your applications is to avoid errors and confusion for your customers. For example, a date in
the form 1/2/2003 is interpreted as January 2, 2003 in the United States, but as February 1, 2003 in European countries.

Another reason to globalize your applications is to display currencies in the correct format. Think of how your
customers would feel when they find out the correct price for an item is 15,000 American dollars, not 15,000 Mexican
pesos (about 1600 American dollars).



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      366
Developing CFML Applications




Your website can also accept customer feedback or some other form of text input. You might want to support that
feedback in multiple languages using a variety of character sets.


How ColdFusion supports globalization
ColdFusion is implemented in Java. As a Java application, ColdFusion uses Java globalization features. For example,
ColdFusion stores all strings internally using the Unicode character set. Because it uses Unicode, ColdFusion can
represent any text data from any language.

In addition, ColdFusion includes many tags and functions designed to support globalizing your applications. You can
use these tags and functions to set locales, convert date and currency formats, control the output encoding of
ColdFusion pages, and perform other actions.


Character sets, character encodings, and locales
When you discuss globalization issues, two topics that you must consider are the character sets or character encodings
recognized by the application and the locales for which the application must format data.

A character set is a collection of characters. For example, the Latin alphabet is the character set that you use to write
English, and it includes all of the lower- and uppercase letters from A to Z. A character set for French includes the
character set used by English, plus special characters such as "é," "à," and "ç."

The Japanese language uses three alphabets: Hiragana, Katakana, and Kanji. Hiragana and Katakana are phonetic
alphabets that each contain 46 characters plus two accents. Kanji contains Chinese ideographs adapted to the Japanese
language. The Japanese language uses a much larger character set than English because Japanese supports more than
10,000 different characters.

In order for a ColdFusion application to process text, the application must recognize the character set used by the text.
The character encoding maps between a character set definition and the digital codes used to represent the data.

In general use, the terms character set (or charset) and character encoding are often used interchangeably, and most
often a specific character encoding encodes one character set. However, this is not always true; for example, there are
multiple encodings of the Unicode character set. For more information on character encodings, see "About character
encodings" on page 366.

Note: ColdFusion uses the term charset to indicate character encoding in some attribute names, structure field keys, and
function parameter names.

A locale identifies the exact language and cultural settings for a user. The locale controls how dates and currencies are
formatted, how to display time, and how to display numeric data. For example, the locale English (US) determines that
a currency value displays as:

$100,000.00

while a locale of Portuguese (Portugese) displays the currency as:


R$ 100.000

To correctly display date, time, currency, and numeric data to your customers, you must know the customer's locale.
For more information on locales, see "Locales" on page 368.


About character encodings
A character encodingmaps each character in a character set to a numeric value that a computer can represent. These
numbers can be represented by a single byte or multiple bytes. For example, the ASCII encoding uses 7 bits to represent
the Latin alphabet, punctuation, and control characters.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               367
Developing CFML Applications




You use Japanese encodings, such as Shift-JIS, EUC-JP, and ISO-2022-JP, to represent Japanese text. These encodings
can vary slightly, but they include a common set of approximately 10,000 characters used in Japanese.

The following terms apply to character encodings:

SBCS Single-byte character set; a character set encoded in one byte per character, such as ASCII or ISO 8859-1.

DBCS Double-byte character set; a method of encoding a character set in no more than 2 bytes, such as Shift-JIS. Many
character encoding schemes that are referred to as double-byte, including Shift-JIS, allow mixing of single-byte and
double-byte encoded characters. Others, such as UCS-2, use 2 bytes for all characters.

MBCS Multiple-byte character set; a character set encoded with a variable number of bytes per character, such as UTF-8.

The following table lists some common character encodings; however, there are many additional character encodings
that browsers and web servers support:


 Encoding            Type               Description

 ASCII               SBCS               7-bit encoding used by English and Indonesian Bahasa languages

 Latin-1             SBCS               8-bit encoding used for many Western European languages

 (ISO 8859-1)

 Shift_JIS           DBCS               16-bit Japanese encoding

                                        Note: Use an underscore character (_), not a hyphen (-) in the name in CFML attributes.

 EUC-KR              DBCS               16-bit Korean encoding

 UCS-2               DBCS               Two-byte Unicode encoding

 UTF-8               MBCS               Multibyte Unicode encoding. ASCII is 7-bit; non-ASCII characters used in European and many
                                        Middle Eastern languages are two-byte; and most Asian characters are three-byte


The World Wide Web Consortium maintains a list of all character encodings supported by the Internet. You can find
this information at www.w3.org/International/O-charset.html.

Computers must often convert between character encodings. In particular, the character encodings most commonly
used on the Internet are not used by Java or Windows. Character sets used on the Internet are typically single-byte or
multiple-byte (including DBCS character sets that allow single-byte characters). These character sets are most efficient
for transmitting data, because each character takes up the minimum necessary number of bytes. Currently, Latin
characters are most frequently used on the web, and most character encodings used on the web represent those
characters in a single byte.

Computers, however, process data most efficiently if each character occupies the same number of bytes. Therefore,
Windows and Java both use double-byte encoding for internal processing.


The Java Unicode character encoding
ColdFusion uses the Java Unicode Standard for representing character data internally. This standard corresponds to
UCS-2 encoding of the Unicode character set. The Unicode character set can represent many languages, including all
major European and Asian character sets. Therefore, ColdFusion can receive, store, process, and present text from all
languages supported by Unicode.

The Java Virtual Machine (JVM) that is used to processes ColdFusion pages converts between the character encoding
used on a ColdFusion page or other source of information to UCS-2. The page or data encodings that ColdFusion
supports depend on the specific JVM, but include most encodings used on the web. Similarly, the JVM converts
between its internal UCS-2 representation and the character encoding used to send the response to the client.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     368
Developing CFML Applications




By default, ColdFusion uses UTF-8 to represent text data sent to a browser. UTF-8 represents the Unicode character
set using a variable-length encoding. ASCII characters are sent using a single byte. Most European and Middle Eastern
characters are sent as 2 bytes, and Japanese, Korean, and Chinese characters are sent as 3 bytes. One advantage of UTF-
8 is that it sends ASCII character set data in a form that is recognized by systems designed to process only single-byte
ASCII characters, while it is flexible enough to handle multiple-byte character representations.

While the default format of text data returned by ColdFusion is UTF-8, you can have ColdFusion return a page to any
character set supported by Java. For example, you can return text using the Japanese language Shift-JIS character set.
Similarly, ColdFusion can handle data that is in many different character sets. For more information, see "Determining
the page encoding of server output" on page 371.


Character encoding conversion issues
Because different character encodings support different character sets, you can encounter errors if your application
gets text in one encoding and presents it in another encoding. For example, the Windows Latin-1 character encoding,
Windows-1252, includes characters with hexadecimal representations in the range 80-9F, while ISO 8859-1 does not
include characters in that range. As a result, under the following circumstances, characters in the range 80-9F, such as
the euro symbol (Ä), are not displayed properly:

· A file encoded in Windows-1252 includes characters in the range 80-9F.

· ColdFusion reads the file, specifying the Windows-1252 encoding in the cffile tag.

· ColdFusion displays the file contents, specifying ISO-8859 in the cfcontent tag.

Similar issues can arise if you convert between other character encodings; for example, if you read files encoded in the
Japanese Windows default encoding and display them using Shift-JIS. To prevent these problems, ensure that the
display encoding is the same as the input encoding.


Locales
A locale identifies the exact language and cultural settings to use for a user. The locale controls how to format the
following:

· Dates

· Times

· Numbers

· Currency amounts

ColdFusion supports all locales supported by the JVM that it uses.

Note: Current JVM versions (through 1.4.2) do not support localized numbers such as Arabic-hindic numbers used in
Arabic locales or hindic digits used in Hindi locales. ColdFusion uses Arabic numbers in all locales.


Locale names
ColdFusion supports two formats for specifying locale names: the standard Java locale names and the ColdFusion
naming convention that was required through ColdFusion 6.1.

· You can specify all locales using a name consisting of the following:

    · Two lowercase letters to identify the language; for example, en for English, or zh for Chinese.

    · Optionally, an underscore and two uppercase letters to identify the regional variant of the language; for example,
       US for the United States, or HK for Hong Kong.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       369
Developing CFML Applications




      For example, en_US represents United States English and es_MX represents Mexican Spanish. For a list of the
      Java locale identifiers supported in the Sun 1.4.2 JVM and their meanings, see
      http://java.sun.com/j2se/1.4.2/docs/guide/intl/locale.doc.html.

Previous to ColdFusion MX 7, ColdFusion supported a limited set of locales, and used identifiers that consisted of the
name of the language, followed, for most languages, by a regional identifier in parentheses, such as English (US) or
German (Standard). ColdFusion continues to support these names; for a list, see SetLocale in the CFML Reference.

The Server.coldfusion.supportedlocales variable is a comma-delimited list of the locale names that you can
specify.

ColdFusion also includes a GetLocaleDisplayName function that returns a locale name in a format that is meaningful
to users. It lets you display the locale using words in the user's language; for example, français (France).


Determining the locale
ColdFusion determines the locale value as follows:

· By default, ColdFusion uses the JVM locale, and the default JVM locale is the operating system locale. You can set
   the JVM locale value explicitly in ColdFusion in the JVM Arguments field on the Java and JVM Settings page in the
   ColdFusion Administrator; for example:

   -Duser.language=de -Duser.country=DE.

· A locale set using the SetLocale function persists for the current request or until it is reset by another SetLocale
   function in the request.

· If a request has multiple SetLocale functions, the current locale setting affects how locale-sensitive ColdFusion
   tags and functions (such as the functions that start with LS) format data. The last SetLocale function that
   ColdFusion processes before sending a response to the requestor (typically the client browser) determines the value
   of the response Content-Language HTTP header. The browser that requested the page displays the response
   according to the rules for the language specified by the Content-Language header.

· ColdFusion ignores any SetLocale functions that follow a cfflush tag.


Using the locale
The SetLocale function determines the default formats that ColdFusion uses to output date, time, number, and
currency values. You use the GetLocale function to determine the current locale setting of ColdFusion, or you can
use the GetLocaleDisplayName function to get the locale name in a format that is meaningful to users. If you have
not made a call to SetLocale, GetLocale returns the locale of the JVM.

The current locale has two effects:

· When ColdFusion formats date, time, currency, or numeric output, it determines how to format the output. You
   can change the locale multiple times on a ColdFusion page to format information according to different locale
   conventions. This enables you to output a page that properly formats different currency values, for example.

· When ColdFusion returns a page to the client, it includes the HTTP Content-Language header. ColdFusion uses
   the last locale setting on the page for this information.

Note: In earlier versions of ColdFusion, the default locale was always English, not the operating system's locale. For the
Japanese version of ColdFusion, the default was Japanese.

The following example uses the LSCurrencyFormat function to output the value 100,000 in monetary units for all the
ColdFusion-supported locales. You can run this code to see how the locale affects the data returned to a browser.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      370
Developing CFML Applications




 <p>LSCurrencyFormat returns a currency value using the locale convention.
<!--- loop through list of locales; show currency values for 100,000 units --->
<cfloop LIST = "#Server.Coldfusion.SupportedLocales#"
      index = "locale" delimiters = ",">
<cfset oldlocale = SetLocale(locale)>
      <cfoutput><p><b><I>#locale#</I></b><br>
            Local: #LSCurrencyFormat(100000, "local")#<br>
            International: #LSCurrencyFormat(100000, "international")#<br>
            None: #LSCurrencyFormat(100000, "none")#<br>
            <hr noshade>
      </cfoutput>
</cfloop>

This example uses the ColdFusion variable Server.Coldfusion.SupportedLocales, which contains a list of all
supported ColdFusion locales.


Processing a request in ColdFusion
When ColdFusion receives an HTTP request for a ColdFusion page, ColdFusion resolves the request URL to a physical
file path and reads the file contents to parse it. A ColdFusion page can be encoded in any character encoding supported
by the JVM used by ColdFusion, but need to be specified so that ColdFusion can identify it.

The content of the ColdFusion page on the server can be static data (typically HTML and plain text not processed by
ColdFusion), and dynamic content written in CFML. Static content is written directly to the response to the browser,
and dynamic content is processed by ColdFusion.

The default language of a website might be different from that of the person connecting to it. For example, you could
connect to an English website from a French computer. When ColdFusion generates a response, the response must be
formatted in the way expected by the customer. This includes both the character set of the response and the locale.

How ColdFusion determines the character set of the files that it processes, and how it determines the character set and
locale of its response to the client are described as follows:


Determining the character encoding of a ColdFusion page
When a request for a ColdFusion page occurs, ColdFusion opens the page, processes the content, and returns the
results back to the browser of the requestor. To process the ColdFusion page, though, ColdFusion has to interpret the
page content.

One piece of information used by ColdFusion is the Byte Order Mark (BOM) in a ColdFusion page. The BOM is a
special character at the beginning of a text stream that specifies the order of bytes in multibyte characters used by the
page. The following table lists the common BOM values:


 Encoding                             BOM signature

 UTF-8                                EF BB BF

 UTF-16 Big Endian                    FE FF

 UTF-16 Little Endian                 FF FE


To insert a BOM character in a CFML page easily, your editor must support BOM characters. Many web page
development tools support insertion of these characters, including Dreamweaver, which automatically sets the BOM
based on the Page Properties Document Encoding selection.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     371
Developing CFML Applications




If your page does not contain a BOM, you can use the cfprocessingdirective tag to set the character encoding of
the page. If you insert the cfprocessingdirective tag on a page that has a BOM, the information specified by the
cfprocessingdirective tag must be the same as for the BOM; otherwise, ColdFusion issues an error.

The following procedure describes how ColdFusion recognizes the encoding format of a ColdFusion page.


Determine the page encoding (performed by ColdFusion)
1 Use the BOM, if specified on the page.

   Adobe recommends that you use BOM characters in your files.

2 Use the pageEncoding attribute of the cfprocessingdirective tag, if specified. For detailed information on how
   to use this attribute, see the cfprocessingdirective tag in the CFML Reference.

3 Default to the JVM default file character encoding. By default, this is the operating system default character
   encoding.


Determining the page encoding of server output
Before ColdFusion can return a response to the client, it must determine the encoding to use for the data in the
response. By default, ColdFusion returns character data using the Unicode UTF-8 format.

ColdFusion pages (.cfm pages) default to using the Unicode UTF-8 format for the response, even if you include the
HTML meta tag in the page. Therefore, the following example doesnot modify the character set of the response:

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
     "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<title>Untitled Document</title>
<meta http-equiv="Content-Type"
     content="text/html;
     charset="Shift_JIS">
</head>
...

In this example, the response still uses the UTF-8 character set. Use the cfcontent tag to set the output character set.

However, within a ColdFusion page you can use the cfcontent tag to override the default character encoding of the
response. Use the type attribute of the cfcontent tag to specify the MIME type of the page output, including the
character set, as follows:

<cfcontent type="text/html charset=EUC-JP">

Note: ColdFusion also provides attributes that let you specify the encoding of specific elements, such as HTTP requests,
request headers, files, and mail messages. For more information, see "Tags and functions for controlling character
encoding" on page 372 and "Handling data in ColdFusion" on page 374.

The rest of this chapter describes ColdFusion tags and functions that you use for globalization, and discusses specific
globalization issues.


Tags and functions for globalizing applications
ColdFusion supplies many tags and functions that you can use to develop globalized applications.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        372
Developing CFML Applications




Tags and functions for controlling character encoding
The following tags and functions let you specify the character encoding of text that ColdFusion generates and
interprets:


 Tag or function             Attribute or parameter           Use

 cfcontent                   type                             Specifies the encoding in which to return the results to the client browser.
                                                              For more information, see "Determining the page encoding of server
                                                              output" on page 371.

 cffile                      charset                          Specifies how to encode data written to a file, or the encoding of a file
                                                              being read. For more information, see "File data" on page 377.

 cfheader                    charset                          Specifies the character encoding in which to encode the HTTP header
                                                              value.

 cfhttp                      charset                          Specifies the character encoding of the HTTP request.

 cfhttpparam                 mimeType                         Specifies the MIME media type of a file; can positionally include the file's
                                                              character encoding.

 cfmail                      charset                          Specifies the character encoding of the mail message, including the
                                                              headers.

 cfmailpart                  charset                          Specifies the character encoding of one part of a multipart mail message.

 cfprocessingdirective       pageEncoding                     Identifies the character encoding of the contents of a page to be
                                                              processed by ColdFusion. For more information, see"Determining the
                                                              character encoding of a ColdFusion page" on page 370.

 CharsetDecode               encoding                         Converts a string in the specified encoding to a binary object.

 CharsetEncode               encoding                         Converts a binary object to a string in the specified encoding.

 GetEncoding                                                  Returns the character encoding of text in the Form or URL scope.

 SetEncoding                 charset                          Specifies the character encoding of text in the Form or URL scope. Used
                                                              when the character set of the input to a form, or the character set of a URL,
                                                              is not in UTF-8 encoding.

 ToBase64                    encoding                         Specifies the character encoding of the string being converted to Base 64.

 ToString                    encoding                         Returns a string encoded in the specified character encoding.

 URLDecode                   charset                          Specifies the character encoding of the URL being decoded.

 URLEncodedFormat            charset                          Specifies the character encoding to use for the URL.



Functions for controlling and using locales
ColdFusion provides the following functions that let you specify and identify the locale and format text based on the
locale:


 Tag or function                   Use

 GetLocale                         Returns the current locale setting.

 GetLocaleDisplayName              Returns the name of a locale in the language of a specific locale. The default value is the current
                                   locale in the locale's language.

 LSCurrencyFormat                  Converts numbers into a string in a locale-specific currency format. For countries that use the euro,
                                   the result depends on the JVM version.

 LSDateFormat                      Converts the date part of a date/time value into a string in a locale-specific date format.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                      373
Developing CFML Applications




 Tag or function                     Use

 LSEuroCurrencyFormat                Converts a number into a string in a locale-specific currency format. Formats using the euro for all
                                     countries that use euro as the currency.

 LSIsCurrency                        Determines whether a string is a valid representation of a currency amount in the current locale.

 LSIsDate                            Determines whether a string is a valid representation of a date/time value in the current locale.

 LSIsNumeric                         Determines whether a string is a valid representation of a number in the current locale.

 LSNumberFormat                      Converts a number into a string in a locale-specific numeric format.

 LSParseCurrency                     Converts a string that is a currency amount in the current locale into a formatted number. For
                                     countries that use the euro, the result depends on the JVM version.

 LSParseDateTime                     Converts a string that is a valid date/time representation in the current locale into a date-time
                                     object.

 LSParseEuroCurrency                 Converts a string that is a currency amount in the current locale into a formatted number. Requires
                                     euro as the currency for all countries that use the euro.

 LSParseNumber                       Converts a string that is a valid numeric representation in the current locale into a formatted
                                     number.

 LSTimeFormat                        Converts the time part of a date/time value into a string in a locale-specific date format.

 SetLocale                           Specifies the locale setting.


Note: Many functions that have names starting with LS have corresponding functions that do not have this prefix:
DateFormat, IsDate, IsNumeric, NumberFormat, ParseDateTime, and TimeFormat. These function use English (US)
locale rules.

If you do not precede calls to the LS functions with a call to the SetLocale function, they use the locale defined by the
JVM, which typically is the locale of the operating system.

The following example uses the LSDateFormat function to display the current date in the format for each locale
supported by ColdFusion:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   374
Developing CFML Applications




<!--- This example shows LSDateFormat --->
<html>
<head>
<title>LSDateFormat Example</title>
</head>
<body>
<h3>LSDateFormat Example</h3>
<p>Format the date part of a date/time value using the locale convention.
<!--- loop through a list of locales; show date values for Now()--->
<cfloop list = "#Server.Coldfusion.SupportedLocales#"
    index = "locale" delimiters = ",">
    <cfset oldlocale = SetLocale(locale)>


    <cfoutput><p><B><I>#locale#</I></B><br>
          #LSDateFormat(Now(), "mmm-dd-yyyy")#<br>
          #LSDateFormat(Now(), "mmmm d, yyyy")#<br>
          #LSDateFormat(Now(), "mm/dd/yyyy")#<br>
          #LSDateFormat(Now(), "d-mmm-yyyy")#<br>
          #LSDateFormat(Now(), "ddd, mmmm dd, yyyy")#<br>
          #LSDateFormat(Now(), "d/m/yy")#<br>
          #LSDateFormat(Now())#<br>
          <hr noshade>
    </cfoutput>
</cfloop>
</body>
</html>


Additional globalization tags and functions
In addition to the tags and functions that are specifically for globalized applications, you might find the following
useful when writing a globalized application:

· All string manipulation functions. For more information, see the String functions list in ColdFusion Functions in
   the CFML Reference.

· The GetTimeZoneInfo function, which returns the time zone of the operating system.


Handling data in ColdFusion
Many of the issues involved with globalizing applications deal with processing data from the various sources supported
by ColdFusion, including the following:

· General character encoding issues

· Locale-specific content

· Input data from URLs and HTML forms

· File data

· Databases

· E-mail

· HTTP

· LDAP

· WDDX

· COM



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        375
Developing CFML Applications




· CORBA

· Searching and indexing


General character encoding issues
Applications developed for earlier versions of ColdFusion that assumed that the character length of a string was the
same as the byte length might produce errors in ColdFusion. The byte length of a string depends on the character
encoding.


Locale-specific content


Generating multilocale content
In an application that supports users in multiple locales and produces output that is specific to multiple locales, you
call the SetLocale function in every request to set the locale for that specific request. When processing has completed,
the locale should be set back to its previous value. One useful technique is to save the user's desired locale in a Session
variable once the user has selected it, and use the Session variable value to set the locale for each user request during
the session.


Supporting the euro
The euro is the currency of many European countries, and ColdFusion supports the reading and writing of correctly
formatted euro values. Unlike other supported currencies, the euro is not tied to any single country (or locale). The
LSCurrencyFormat and LSParseCurrency functions rely on the underlying JVM for their operations, and the rules
used for currencies depend on the JVM. For Sun JVMs, the 1.3 releases did not support euros and used the older
country-specific currencies. The 1.4 releases use euros for all currencies that are in the euro zone as of 2002. If you are
using a JVM that does not support the euro, use the LSEuroCurrencyFormat and LSParseEuroCurrency functions
to format and parse euro values in locales that use euros as their currency.


Input data from URLs and HTML forms
A web application server receives character data from request URL parameters or as form data.

The HTTP 1.1 standard only allows US-ASCII characters (0-127) for the URL specification and for message headers.
This requires a browser to encode the non-ASCII characters in the URL, both address and parameters, by escaping
(URL encoding) the characters using the "%xx" hexadecimal format. URL encoding, however, does not determine how
the URL is used in a web document. It only specifies how to encode the URL.

Form data uses the message headers to specify the encoding used by the request (Content headers) and the encoding
used in the response (Accept headers). Content negotiation between the client and server uses this information.

There are several techniques for handling both URL and form data entered in different character encodings.


Handling URL strings
URL requests to a server often contain name-value pairs as part of the request. For example, the following URL
contains name-value pairs as part of the URL:

http://company.com/prod_page.cfm?name=Stephen;ID=7645

As discussed previously, URL characters entered using any character encoding other than US-ASCII are URL-encoded
in a hexadecimal format. However, by default, a web server assumes that the characters of a URL string are single-byte
characters.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       376
Developing CFML Applications




One common method used to support non-ASCII characters within a URL is to include a name-value pair within the
URL that defines the character encoding of the URL. For example, the following URL uses a parameter called encoding
to define the character encoding of the URL parameters:

http://company.com/prod_page.cfm?name=Stephen;ID=7645;encoding=Latin-1

Within the prod_page.cfm page, you can check the value of the encoding parameter before processing any of the other
name-value pairs. This guarantees that you handle the parameters correctly.

You can also use the SetEncoding function to specify the character encoding of URL parameters. The SetEncoding
function takes two parameters: the first specifies a variable scope and the second specifies the character encoding used
by the scope. Since ColdFusion writes URL parameters to the URL scope, you specify "URL" as the scope parameter to
the function.

For example, if the URL parameters are passed using Shift-JIS, you could access them as follows:

<cfscript>
     setEncoding("URL", "Shift_JIS");
     writeoutput(URL.name);
     writeoutput(URL.ID);
</cfscript>

Note: To specify the Shift-JIS character encoding, use the Shift_JIS attribute, with an underscore (_), not a hyphen (-).


Handling form data
The HTML form tag and the ColdFusion cfform tag let users enter text on a page, then submit that text to the server.
The form tags are designed to work only with single-byte character data. Since ColdFusion uses 2 bytes per character
when it stores strings, ColdFusion converts each byte of the form input into a two-byte representation.

However, if a user enters double-byte text into the form, the form interprets each byte as a single character, rather than
recognize that each character is 2 bytes. This corrupts the input text, as the following example shows:

1 A customer enters three double-byte characters in a form, represented by 6 bytes.

2 The form returns the six bytes to ColdFusion as six characters. ColdFusion converts them to a representation using
   2 bytes per input byte for a total of 12 bytes.

3 Outputting these characters results in corrupt information displayed to the user.

To work around this issue, use the SetEncoding function to specify the character encoding of input form text. The
SetEncoding function takes two parameters: the first specifies the variable scope and the second specifies the
character encoding used by the scope. Since ColdFusion writes form parameters to the Form scope, you specify "Form"
as the scope parameter to the function. If the input text is double-byte, ColdFusion preserves the two-byte
representation of the text.

The following example specifies that the form data contains Korean characters:

<cfscript>
     setEncoding("FORM", "EUC-KR");
</cfscript>
<h1> Form Test Result </h1>
<strong>Form Values :</strong>


<cfset text = "String = #form.input1# , Length = #len(Trim(form.input1))#">
<cfoutput>#text#</cfoutput>




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       377
Developing CFML Applications




File data
You use the cffile tag to write to and read from text files. By default, the cffile tag assumes that the text that you are
reading, writing, copying, moving, or appending is in the JVM default file character encoding, which is typically the
system default character encoding. For cffile action="Read", ColdFusion also checks for a byte order mark (BOM)
at the start of the file; if there is one, it uses the character encoding that the BOM specifies.

Problems can arise if the file character encoding does not correspond to JVM character encoding, particularly if the
number of bytes used for characters in one encoding does not match the number of bytes used for characters in the
other encoding.

For example, assume that the JVM default file character encoding is ISO 8859-1, which uses a single byte for each
character, and the file uses Shift-JIS, which uses a two-byte representation for many characters. When reading the file,
the cffile tag treats each byte as an ISO 8859-1 character, and converts it into its corresponding two-byte Unicode
representation. Because the characters are in Shift-JIS, the conversion corrupts the data, converting each two-byte
Shift-JIS character into two Unicode characters.

To enable the cffile tag to correctly read and write text that is not encoded in the JVM default character encoding,
you can pass the charset attribute to it. Specify as a value the character encoding of the data to read or write, as the
following example shows:

 <cffile action="read"
     charset="EUC-KR"
     file = "c:\web\message.txt"
     variable = "Message" >


Databases
ColdFusion applications access databases using drivers for each of the supported database types. The conversion of
client native language data types to SQL data types is transparent and is done by the driver managers, database client,
or server. For example, the character data (SQL CHAR, VARCHAR) you use with JDBC API is represented using
Unicode-encoded strings.

Database administrators configure data sources and usually are required to specify the character encodings for
character column data. Many of the major vendors, such as Oracle, Sybase, and Informix, support storing character
data in many character encodings, including Unicode UTF-8 and UTF-16.

The database drivers supplied with ColdFusion correctly handle data conversions from the database native format to
the ColdFusion Unicode format. You do not have to perform any additional processing to access databases. However,
always check with your database administrator to determine how your database supports different character
encodings.


E-mail
ColdFusion sends e-mail messages using the cfmail, cfmailparam, and cfmailpart tags.

By default, ColdFusion sends mail in UTF-8 encoding. You can specify a different default encoding on the Mail page
in the ColdFusion Administrator, and you can use the charset attribute of the cfmail and cfmailpart tags to specify
the character encoding for a specific mail message or part of a multipart mail message.


HTTP
ColdFusion supports HTTP communication using the cfhttp and cfhttpparam tags and the GetHttpRequestData
function.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     378
Developing CFML Applications




The cfhttp tag supports making HTTP requests. The cfhttp tag uses the Unicode UTF-8 encoding for passing data
by default, and you can use the charset attribute to specify the character encoding. You can also use the cfhttpparam
tag mimeType attribute to specify the MIME type and character set of a file.


LDAP
ColdFusion supports LDAP (Lightweight Directory Access Protocol) through the cfldap tag. LDAP uses the UTF-8
encoding format, so you can mix all retrieved data with other data and safely manipulated it. No extra processing is
required to support LDAP.


WDDX
ColdFusion supports the cfwddx tag. ColdFusion stores WDDX (Web Distributed Data Exchange) data as UTF-8
encoding, so it automatically supports double-byte character encodings. You do not have to perform any special
processing to handle double-byte characters with WDDX.


COM
ColdFusion supports COM through the cfobjecttype="com" tag. All string data used in COM interfaces is constructed
using wide characters (wchars), which support double-byte characters. You do not have to perform any special
processing to interface with COM objects.


CORBA
ColdFusion supports CORBA through the cfobjecttype="corba" tag. The CORBA 2.0 interface definition language
(IDL) basic type "String" used the Latin-1 character encoding, which used the full 8-bits (256) to represent characters.

As long as you are using CORBA later than version 2.0, which includes support for the IDL types wchar and wstring,
which map to Java types char and string respectively, you do not have to do anything to support double-byte
characters.

However, if you are using a version of CORBA that does not support wchar and wstring, the server uses char and string
data types, which assume a single-byte representation of text.


Searching and indexing
ColdFusion supports Verity search through the cfindex, cfcollection, and cfsearch tags. To support multilingual
searching, the ColdFusion product CD-ROM includes the Verity language packs that you install to support different
languages.



Debugging and Troubleshooting Applications

Adobe ColdFusion provides detailed debugging information to help you resolve problems with your application. You
configure ColdFusion to provide debugging information, and use the cftrace and cftimer tags to provide detailed
information on code execution. You can also use tools for validating your code before you run it and troubleshoot
particular problems.

Note: Adobe Dreamweaver provides integrated tools for displaying and using ColdFusion debugging output. For
information on using these tools, see the Dreamweaver online Help.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    379
Developing CFML Applications




Configuring debugging in the ColdFusion Administrator
ColdFusion can provide important debugging information for every application page requested by a browser. The
ColdFusion Administrator lets you specify which debugging information to make available and how to display it. The
Administrator settings briefly described. For more information, see the online Help for the Debugging pages.


Debugging Settings page
In the Administrator, the following options on the Debugging Settings page determine the information that
ColdFusion displays in debugging output:


Option                                   Description

Enable Robust Exception Information      Enables the display of the following information when ColdFusion displays the exception
                                         error page. (Cleared by default.)

                                         · Path and URL of the page that caused the error

                                         · Line number and short snippet of the code where the error was identified

                                         · Any SQL statement and data source

                                         · Java stack trace

Enable Debugging                         Enables debugging output. When this option is cleared, no debugging information is
                                         displayed, including all output of cftrace and cftimer calls. (Cleared by default.)

                                         You should disable debugging output on production servers. Doing so increases security by
                                         ensuring that users cannot see debugging information. It also improves server response
                                         times. You can also limit debugging output to specific IP addresses; for more information, see
                                         "Debugging IP addresses page" on page 380.

Select Debugging Output Format           Determines how to display debugging output:

                                         · The classic.cfm template (the default) displays information as plain HTML text at the
                                           bottom of the page.

                                         · The dockable.cfm template uses DHTML to display the debugging information using an
                                           expanding tree format in a separate window. This window can be either a floating pane or
                                           docked to the browser window. For more information on the dockable output format, see
                                           "Using the dockable.cfm output format" on page 384.

Report Execution Times                   Lists ColdFusion pages that run as the result of an HTTP request and displays execution
                                         times, ColdFusion also highlights in red pages with processing times greater than the
                                         specified value, and you can select between a summary display or a more detailed, tree
                                         structured, display.

General Debug Information                Displays general information about the request: ColdFusion Version, Template, Time Stamp,
                                         User Locale, User Agent, User IP, and Host Name.

Database Activity                        Displays debugging information about access to SQL data sources and stored procedures.
                                         (Selected by default.)

Exception information                    Lists all ColdFusion exceptions raised in processing the request. (Selected by default.)

Tracing information                      Displays an entry for each cftrace tag. When this option is cleared, the debugging output
                                         does not include tracing information, but the output page does include information for
                                         cftrace tags that specify inline="Yes". (Selected by default.)

                                         For more information on using the cftrace tag, see "Using the cftrace tag to trace
                                         execution" on page 385.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         380
Developing CFML Applications




 Option                                       Description

 Variables                                    Enables the display of ColdFusion variable values. When this option is cleared, disables
                                              display of all ColdFusion variables in the debugging output. (Selected by default.)

                                              When enabled, ColdFusion displays the values of variables in the selected scopes. You can
                                              select to display the contents of any of the ColdFusion scopes except Variables, Attributes,
                                              Caller, and ThisTag. To enhance security, Application, Server, and Request variable display is
                                              disabled by default,

 Enable Performance Monitoring                Allows the standard NT Performance Monitor application to display information about a
                                              running ColdFusion application server.

 Enable CFSTAT                                Enables you to use of the cfstat command line utility to monitor real-time performance.
                                              This utility displays the same information that ColdFusion writes to the NT System Monitor,
                                              without using the System Monitor application. For information on the cfstat utility, see
                                              Configuring and Administering ColdFusion.



Debugging IP addresses page
By default, when you enable debugging output, the output is visible only to local users (that is, via IP address 127.0.0.1).
You can specify additional IP addresses whose users can see debugging output, or even disable output to local users.
In the Administrator, use the Debugging IPs page to specify the addresses that can receive debugging messages.

Note: If you must enable debugging on a production server, for example to help locate the cause of a difficult problem, use
the Debugging IP Addresses page to limit the output to your development systems and prevent clients from seeing the
debugging information.


Using debugging information from browser pages
The ColdFusion debugging output that you configure in the Administrator displays whenever an HTML request
completes. It represents the server conditions at the end of the request. For information on displaying debugging
information while a request is processed, see "Using the cftrace tag to trace execution" on page 385.

The dockable.cfm debugging output format shows the debugging output in collapsed format. The next sections show
each of the debugging sections and describe how you can use the information they display.


General debugging information
ColdFusion displays general debugging information. In the classic.cfm output format, the information appears at the
top of the debugging output and has no heading.

The general debugging information includes the following values. The table lists the names used in the classic output
template view.


 Name                     Description

 ColdFusion               The ColdFusion version.

 Template                 The requested template. (In the dockable.cfm format, this appears in the Page Overview section and is called
                          Page.)

 TimeStamp                The time the request was completed. (In the dockable.cfm format, this appears in the Page Overview section and
                          is called Date.)

 Locale                   The locality and language that determines how information is processed, particularly the message language.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                          381
Developing CFML Applications




 Name                     Description

 User Agent               The identity of the browser that made the HTTP request.

 Remote IP                The IP address of the client system that made the HTTP request.

 Host Name                The name of the host running the ColdFusion server that executed the request.



Execution Time
The Execution Time section displays the time required to process the request. It displays information about the time
required to process all pages required for the request, including the Application.cfc, Application.cfm, and
OnRequestEnd.cfm pages, if used, and any CFML custom tags, pages included by the cfinclude tag, and any
ColdFusion component (CFC) pages.

    To display execution time for a specific block of code, use the cftimer tag.


You can display the execution time in two formats:

· Summary

· Tree

Note: Execution time decreases substantially between the first and second time you use a page after creating it or changing
it. The first time ColdFusion uses a page it compiles the page into Java bytecode, which the server saves and loads into
memory. Subsequent uses of unmodified pages do not require recompilation of the code, and therefore are substantially
faster.


Summary execution time format
The summary format displays one entry for each ColdFusion page processed during the request. If a page is processed
multiple times it appears only once in the summary. For example, if a custom tag gets called three time in a request, it
appears only once in the output.

The following table describes the display fields:


 Column                   Description

 Total Time               The total time required to process all instances of the page and all pages that it uses. For example, if a request
                          causes a page to be processed two times, and the page includes another page, the total time includes the time
                          required to process both pages twice.

 Avg Time                 The average time for processing each instance of this page and the pages that it uses. The Avg Time multiplied
                          by the Count equals the Total Time.

 Count                    The number of times the page is processed for the request.

 Template                 The path name of the page.


The page icon indicates the requested page.

Any page with an average processing time that exceeds the highlight value that you set on the Debugging Settings page
in the ColdFusion Administrator appears in red.

The next to last line of the output displays the time that ColdFusion took to parse, compile, and load pages, and to start
and end page processing. This image is not included in the individual page execution times. The last line shows the
sum of all the time it took to process the request.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          382
Developing CFML Applications




Tree execution time format
The tree execution time format is a hierarchical, detailed view of how ColdFusion processes each page. If a page
includes or calls second page, the second page appears below and indented relative to the page that uses it. Each page
appears once for each time it is used. Therefore, if a page gets called three times in processing a request, it appears three
times in the tree. Therefore the tree view displays both processing times and an indication of the order of page
processing.

As in the summary view, the execution times (in parentheses) show the times to process the listed page and all pages
required to process the page, that is, all pages indented below the page in the tree.

By looking at this output in this image you can determine the following information:

· ColdFusion took 0 ms to process an Application.cfm page as part of the request.

· The requested page was tryinclude.cfm. It took 203 ms to process this page and all pages required to execute it. The
   code directly on this page took 71 milliseconds (203 - 93 - 16 - 23) to process.

· The mytag2.cfm page was processed three times. All processing took 93 milliseconds, and the average processing
   time was 31 milliseconds. (This page does not call any other pages.)

· The mytag1.cfm page was processed two times. All processing took 78 milliseconds, and the average processing
   time was 39 milliseconds. This time included the time to process mytag2.cfm (this tag calls the mytag2 custom tag);
   therefore, the code directly on the page took an average of 8 milliseconds and a total of 16 milliseconds to process.

· The includeme.cfm page took about 62 ms to process. This processing time includes the time to process the
   mytag1.cfm, and therefore also the time to process mytag2.cfm once. Therefore the code directly on the page took
   23 milliseconds (62-39) to process.

· ColdFusion took 125 ms for processing that was not associated with a specific page.

· The total processing time was 328 milliseconds, the sum of 125 + 203.


Database Activity
In the Administrator, when Database Activity is selected on the Debugging Settings page, the debugging output
includes information about database access.


SQL Queries
The SQL Queries section provides information about tags that generate SQL queries or result in retrieving a cached
database query: cfquery, cfinsert, cfgridupdate, and cfupdate.

The output displays the following information:

· Page on which the query is located.

· The time when the query was made.

· Query name.

· An indicator if the result came from a cached query.

· SQL statement, including the results of processing any dynamic elements such as CFML variables and
   cfqueryparam tags. This information is useful because it shows the results of all ColdFusion processing of the SQL
   statement.

· Data source name.

· Number of records returned; 0 indicates no match to the query.

· Query execution time.

· Any query parameters values from cfqueryparam tags.


                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      383
Developing CFML Applications




Stored Procedures
The stored procedures section displays information about the results of using the cfstoredproc tag to execute a
stored procedure in a database management system.

The output displays the following information:

· Stored procedure name

· Data source name

· Query execution time

· Page on which the query is located.

· The time when the query was made.

· A table displaying the procedure parameters sent and received, as specified in the cfprocparam tags, including the
   ctype, CFSQLType, value, variable, and dbVarName attributes. The variable information for OUT and INOUT
   parameters includes the returned value.

· A table listing the procedure result sets returned, as specified in the cfprocresult tag.


Exceptions
In the Administrator, when Exception Information is selected on the Debugging Settings page, the debugging output
includes a list of all ColdFusion exceptions raised in processing the application page.

The exception information includes information about any application exceptions that are caught and handled by
your application code or by ColdFusion.

Exceptions represent events that disrupt the normal flow of an application. You should catch and, whenever possible,
recover from foreseeable exceptions in your application, as described in "Handling Errors" on page 275. However, you
might also want to be alerted to caught exceptions when you are debugging your application. For example, if a file is
missing, your application can catch the cffile exception and use a backup or default file instead. If you enable
exception information in the debugging output, you can immediately see when this happens.


Trace points
In the Administrator, when Tracing Information is selected on the Debugging Settings page, the debugging output
includes the results of all cftrace tags, including all tags that display their results inline. Therefore, the debugging
output contains a historical record of all trace points encountered in processing the request.

For more information on using the cftrace tag, see "Using the cftrace tag to trace execution" on page 385.


Scope variables
In the Administrator, when the Variables option and one or more variable scopes are selected on the Debugging
Settings page, the debugging output displays the values of all variables in the selected scopes. The debugging output
displays the values that result after all processing of the current page.

By displaying selected scope variables you can determine the effects of processing on persistent scope variables, such
as application variables. This can help you locate problems that do not generate exceptions.

The Form, URL, and CGI scopes are useful for inspecting the state of a request. They let you inspect parameters that
affect page behavior, as follows:

URL variables Identify the HTTP request parameters.

Form variables Identify the form fields posted to an action page.

CGI variables Provide a view of the server environment following the request.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    384
Developing CFML Applications




Similarly, the Client, Session, Application, and Server scope variables show the global state of the application, and can
be useful in tracing how each page affects the state of the ColdFusion persistent variables.


Using the dockable.cfm output format
The dockable.cfm output format has several features that are not included in the classic.cfm debugging display.


Application page selections
ColdFusion displays two buttons at the bottom of each page, as described in the following table:


 Button                              Description

 Debug This page                     Tells ColdFusion to display the debugging information for the selected frame. Refreshes the debug
                                     pane if you select it for the current frame (or the application does not use frames).

 Floating/Docked debug pane          Toggles the display between a floating window and a pane docked to the left of the selected frame.



Debug pane features
The debug pane has the following features:

· You can expand and collapse each debugging information category, such as Exceptions, by clicking the plus or
   minus sign (+ or -) in front of each category heading. You can also expand and collapse each scope data type display
   in the Scoped Variables section.

· The top of the debug pane displays the URL of the application page being debugged (as identified by the
   cgi.script_name variable). Click this link to refresh the page and display the debugging information that results.
   (You can also refresh the page and debugging information by using your browser's Refresh button or key.)

· The debug pane also displays a box where you can enter a page path or URL. When you click the Go button,
   ColdFusion processes the page and the debug pane is updated with the debugging information for the new page.


Controlling debugging information in CFML
The following sections describe how you can use CFML tags and functions to display or hide debugging and tracing
information.


Generating debugging information for an individual query
In the Administrator, the cfquery tag debug attribute overrides the Database Activity setting on the Debugging
Settings page. The debug attribute has an effect only when debugging output is enabled on the Debugging Settings
page, as follows:

· If Database Activity is selected in the Administrator, specify debug="No" to prevent ColdFusion from displaying
   the query's SQL and statistics in the debugging output.

· If Database Activity is not selected in the Administrator, specify debug="Yes" or debug to have ColdFusion display
   the query's SQL and statistics in the debugging output.

For example, if Database Activity is not selected in the Administrator, you can use the following code to show the query
execution time, number of records returned, ColdFusion page, timestamp, and the SQL statement sent to the data
source for this query only:

 <cfquery name="TestQuery" datasource="cfdocexamples" debug>
      SELECT * FROM TestTable
</cfquery>




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      385
Developing CFML Applications




The debug attribute can be useful to disable query debugging information generated by queries in custom tags that
you call frequently, so that you only see the debugging information for queries in pages that call the tags.

You can also view stored procedure-specific debugging information by specifying the debug attribute in the
cfstoredproc tag.


Controlling debugging output with the cfsetting tag
Use the cfsetting tag showDebugOutput attribute to turn off debugging output for a specific page. The attribute
controls debugging output only if the Debugging Settings page in the ColdFusion Administrator enables debugging
output. The default value of the attribute is Yes. The following tag suppresses all debugging output for the current page:

 <cfsetting showDebugOutput="No">

You can put this tag in the initialization code of the Application.cfc file or on your Application.cfm page to suppress
all debugging output for an application, and override it on specific pages by setting showDebugOutput="Yes" in
cfsetting tags on those pages. Conversely, you can leave debugging on for the application, and use the cfsetting
showDebugOutput="No" tag to suppress debugging on individual pages where the output could cause errors or
confusion.

You can also use the showDebugOutput attribute to control debugging output if you do not have access to the
ColdFusion Administrator, but only if the Administrator enables debugging.


Using the IsDebugMode function to run code selectively
The IsDebugMode function returns True if debugging is enabled. You can use this function in a cfif tag condition to
selectively run code only when debugging output is enabled. The IsDebugMode function lets you tell ColdFusion to
run any code in debug mode, so it provides more flexibility than the cftrace tag for processing and displaying
information.

You can use the IsDebugMode function to selectively log information only when debugging is enabled. Because you
control the log output, you have the flexibility of silently logging information without displaying trace information in
the browser. For example, the following code logs the application page, the current time, and the values of two variables
to the log file MyAppSilentTrace.log when debugging is enabled:

 <cfquery name="MyDBQuery" datasource="cfdocexamples">
      SELECT *
      FROM Employee
</cfquery>
<cfif IsDebugMode()>
      <cflog file="MyAppSilentTrace" text="Page: #cgi.script_name#,
      completed query MyDBQuery; Query Execution time:
      #cfquery.ExecutionTime# Status: #Application.status#">
</cfif>

    If you use cfdump tags frequently for debugging, place them in <cfif IsDebugMode()> tags; for example <cfif
    IsDebugMode()><cfdump var=#myVar#></cfif>. This way you ensure that if you leave any cfdump tags in
production code, they are not displayed when you disable debugging output.


Using the cftrace tag to trace execution
The cftrace tag displays and logs debugging data about the state of your application at the time the cftrace tag
executes. You use it to provide "snapshots" of specific information as your application runs.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          386
Developing CFML Applications




About the cftrace tag
The cftrace tag provides the following information:

· A severity identifier specified by the cftrace tag type attribute

· A timestamp indicating when the cftrace tag executed

· The time elapsed between the start of processing the request and when the current cftrace tag executes.


· The time between any previous cftrace tag in the request and the current one. If this is the first cftrace tag
   processed for the request, the output indicates "1st trace". ColdFusion does not display this information in inline
   trace output, only the log and in the standard debugging output.

· The name of the page that called the cftrace tag

· The line on the page where the cftrace call is located

· A trace category specified by the category attribute

· A message specified by the text attribute

· The name and value, at the time the cftrace call executes, of a single variable specified by the var attribute

A typical cftrace tag might look like the following:

 <cftrace category="UDF End" inline = "True" var = "MyStatus"
     text = "GetRecords UDF call has completed">

You can display the cftrace tag output in either or both of the following ways:

· As a section in the debugging output: To display the trace information in the debugging output, in the
   Administrator, select Tracing Information on the Debugging Settings page.

· Inline in your application page: When you specify the inline attribute in a cftrace tag, ColdFusion displays the
   trace output on the page at the cftrace tag location. (An inline cftrace tag does not display any output if it is
   inside a cfsilent tag block.)

The cftrace tag executes only if you select Enable Debugging on the ColdFusion Administrator Debugging Settings
page. To display the trace results in the debugging output, you must also specify Tracing Information on the
Debugging Settings page; otherwise, the trace information is logged and inline traces are displayed, but no trace
information appears in the debugging output.

Note: When you use inline trace tags, ColdFusion sends the page to the browser after all page processing is completed, but
before it displays the debugging output from the debug template. As a result, if an error occurs after a trace tag but before
the end of the page, ColdFusion might not display the trace for that tag.

The following image shows inline trace messages:




The following table lists the displayed information:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     387
Developing CFML Applications




 Entry                                                        Meaning

                                                               Trace type (severity) specified in the cftrace call; in this case,
                                                               Information.

 [CFTRACE 13:21:11.011]                                        Time when the cftrace tag executed.

 [501 ms]                                                      Time taken for processing the current request to the point of the
                                                              cftrace tag.

 [C:\CFusion\wwwroot\MYStuff\mydocs\tractest.cfm]              Path in the web server of the page that contains the cftrace tag.

 @ line:14                                                     The line number of the cftrace tag.

 [UDF End]                                                     Value of the cftrace tag category attribute.

 GetRecords UDF call has completed                             The cftrace tag text attribute with any variables replaced with their
                                                               values.

 MyStatus Success                                              Name and value of the variable specified by the cftrace tag var
                                                               attribute.


ColdFusion logs all cftrace output to the file logs\cftrace.log in your ColdFusion installation directory.

A log file entry looks like the following:

 "Information","web-29","04/01/02","13:21:11","MyApp","[501 ms (1st trace)]
[C:\ColdFusion9\wwwroot\MYStuff\mydocs\tractest.cfm @ line: 14] - [UDF End] [MyStatus =
Success] GetRecords UDF call has completed "

This entry is in standard ColdFusion log format, with comma-delimited fields inside double-quote characters. The
information displayed in the trace output is in the last, message, field.

The following table lists the contents of the trace message and the log entries. For more information on the log file
format, see "Logging errors with the cflog tag" on page 285.


 Entry                                                                        Meaning

 Information                                                                  The Severity specified in the cftrace call.

 web-29                                                                       Server thread that executed the code.

 04/01/02                                                                     Date the trace was logged.

 13:21:11                                                                     Time the trace was logged.

 MyApp                                                                        The application name, as specified in a cfapplication tag.

 501 ms (1st trace)]                                                          The time ColdFusion took to process the current request
                                                                              up to the cftrace tag, This is the first cftrace tag
                                                                              processed in this request. If there had been a previous
                                                                              cftrace tag, the parentheses would contain the
                                                                              number of milliseconds between when the previous
                                                                              cftrace tag ran and when this tag ran.

 [C:\CFusion\wwwroot\MYStuff\mydocs\tracetest.cfm @ line: 14]                 Path of the page on which the trace tag is located and the
                                                                              line number of the cftrace tag on the page.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  388
Developing CFML Applications




Entry                                                                    Meaning

[UDF End]                                                                Value of the cftrace tag category attribute.

[MyStatus = Success]                                                     Name and value of the variable specified by the cftrace
                                                                         tag var attribute. If the variable is a complex data type,
                                                                         such as an array or structure, the log contains the variable
                                                                         value and the number of entries at the top level of the
                                                                         variable, such as the number of top-level structure keys.

GetRecords UDF call has completed                                        The cftrace tag text attribute with any variables
                                                                         replaced with their values.



Using tracing
As its name indicates, the cftrace tag is designed to help you trace the execution of your application. It can help you
do any of several things:

· You can time the execution of a tag or code section. This capability is useful for tags and operations that can take
  substantial processing time. Typical candidates include all ColdFusion tags that access external resources, including
  cfquery, cfldap, cfftp, cffile, and so on. To time execution of any tag or code block, call the cftrace tag
  before and after the code you want to time.

· You can display the values of internal variables, including data structures. For example, you can display the raw
  results of a database query.

· You can display an intermediate value of a variable. For example, you could use this tag to display the contents of
  a raw string value before you use string functions to select a substring or format it.

· You can display and log processing progress. For example, you can place a cftrace call at the head of pages in your
  application or before critical tags or calls to critical functions. (Doing this could result in massive log files in a
  complex application, so use this technique with care.)

· If a page has many nested cfif and cfelseif tags you can place cftrace tags in each conditional block to trace
  the execution flow. When you do this, use the condition variable in the message or var attribute.

· If you find that the ColdFusion server is hanging, and you suspect a particular block of code (or call to a cfx tag,
  COM object, or other third-party component), you can place a cftrace tag before and after the suspect code, to
  log entry and exit.


Calling the cftrace tag
The cftrace tag takes the following attributes. All attributes are optional.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                             389
Developing CFML Applications




 Attribute                Purpose

 abort                    A Boolean value. If you specify True, ColdFusion stops processing the current request immediately after the tag.
                          This attribute is the equivalent of placing a cfabort tag immediately after the cftrace tag. The default is False.
                          If this attribute is True, the output of the cftrace call appears only in the cftrace.log file. The line in the file
                          includes the text "[ABORTED]".

 category                 A text string specifying a user-defined trace type category. This attribute lets you identify or process multiple
                          trace lines by categories. For example, you could sort entries in a log according to the category.

                          The category attribute is designed to identify the general purpose of the trace point. For example, you might
                          identify the point where a custom tag returns processing to the calling page with a "Custom Tag End" category.
                          You can also use finer categories; for example, by identifying the specific custom tag name in the category.

                          You can include simple ColdFusion variables, but not arrays, structures, or objects, in the category text by
                          enclosing the variable name in number signs (#).

 inline                   A Boolean value. If you specify True, ColdFusion displays trace output in-line in the page. The default is False.

                          The inline attribute lets you display the trace results at the place that the cftrace call is processed. This
                          provides a visual cue directly in the ColdFusion page display.

                          Trace output also appears in a section in the debugging information display.

 text                     A text message describing this trace point. You can include simple ColdFusion variables, but not arrays,
                          structures, or objects, in the text output by enclosing the variable name in number signs (#).

 type                     A ColdFusion logging severity type. The inline trace display and dockable.cfm output format show a symbol for
                          each type. The default debugging output shows the type name, which is also used in the log file. The type name
                          must be one of the following:


                                Information (default)


                                Warning


                                Error


                               Fatal Information

 var                      The name of a single variable that you want displayed. This attribute can specify a simple variable, such as a string,
                          or a complex variable, such as a structure name. Do not surround the variable name in number signs.

                          Complex variables are displayed in inline output in cfdump format; the debugging display and log file report the
                          number of elements in the complex variable, instead of any values.

                          You can use this attribute to display an internal variable that the page does not normally show, or an intermediate
                          value of a variable before the page processes it further.

                          To display a function return value, place the function inside the message. Do not use the function in the var
                          attribute, because the attribute cannot evaluate functions.


Note: If you specify inline trace output, and a cftrace tag is inside a cfsilent tag block, ColdFusion does not display
the trace information in line, but does include it in the standard debugging display.

The following cftrace tag displays the information in the example output and log entry in "About the cftrace tag" on
page 386 :

<cftrace abort="False" category="UDF End" inline = "True" text = "GetRecords UDF
      call has completed" var = "MyStatus">



Using the cftimer tag to time blocks of code
The cftimer tag displays execution time for a specified section of CFML code.




                                                       

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  390
Developing CFML Applications




Using timing
Use this tag to determine how long it takes for a block of code to execute. This is useful when ColdFusion debugging
output indicates excessive execution time, but does not pinpoint the long-running block of code.

To use this tag, enable debugging in the ColdFusion Administrator Debugging Settings page. In the Debugging
Settings page, you must also specifically enable usage of the cftimer tag by checking the Timer Information check box.

   If you enable debugging for the cftimer tag only and display timing information in an HTML comment, you can
   generate timing information without disturbing production users.


Calling the cftimer tag
You can control where the cftimer tag displays timing information, as follows:

· Inline: Displays timing information following the </cftimer> tag.

· Outline: Displays timing information at the beginning of the timed code and draws a box around the timed code.
   (This requires browser support for the HTML FIELDSET attribute.)

· Comment: Displays timing information in an HTML comment in the format <!--label: elapsed-timems >--. The
   default label is cftimer.

· Debug: Displays timing information in the debugging output under the heading CFTimer Times.

The following example calls the cftimer tag multiple times, each time using a different type attribute:

<HTML>
<body>
<h1>CFTIMER test</h1>
<!--- type="inline" --->
     <cftimer label="Query and Loop Time Inline" type="inline">
          <cfquery name="empquery" datasource="cfdocexamples">
                     select *
                     from Employees
          </cfquery>


          <cfloop query="empquery">
                <cfoutput>#lastname#, #firstname#</cfoutput><br>
          </cfloop>
     </cftimer>
<hr><br>
<!--- type="outline" --->
     <cftimer label="Query and CFOUTPUT Time with Outline" type="outline">
          <cfquery name="coursequery" datasource="cfdocexamples">
                     select *
                     from CourseList
          </cfquery>


     <table border="1" width="100%">
          <cfoutput query="coursequery">
          <tr>
          <td>#Course_ID#</td>
          <td>#CorName#</td>
          <td>#CorLevel#</td>
          </tr>
          </cfoutput>
          </table>
     </cftimer>
<hr><br>



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   391
Developing CFML Applications




<!--- type="comment" --->
    <cftimer label="Query and CFOUTPUT Time in Comment" type="comment">
          <cfquery name="parkquery" datasource="cfdocexamples">
                    select *
                    from Parks
          </cfquery>
<p>Select View &gt; Source to see timing information</p>
    <table border="1" width="100%">
          <cfoutput query="parkquery">
          <tr>
          <td>#Parkname#</td>
          </tr>
          </cfoutput>
          </table>
    </cftimer>


<hr><br>
<!--- type="debug" --->
    <cftimer label="Query and CFOUTPUT Time in Debug Output" type="debug">
          <cfquery name="deptquery" datasource="cfdocexamples">
                    select *
                    from Departments
          </cfquery>
<p>Scroll down to CFTimer Times heading to see timing information</p>
    <table border="1" width="100%">
          <cfoutput query="deptquery">
          <tr>
          <td>#Dept_ID#</td>
          <td>#Dept_Name#</td>
          </tr>
          </cfoutput>
          </table>
    </cftimer>
</body>



Using the Code Analyzer
The Code Analyzer has two purposes:

· It can validate the CFML syntax of your application. To do so, the analyzer runs the ColdFusion compiler on your
  pages, but does not execute the compiled code. It reports errors that the compiler encounters.

· It provides information about the incompatibility (and its severity), and suggests a remedy where one is required.

· It can identify places where ColdFusion might behave differently than previous versions. The analyzer identifies the
  following kinds of features:

  · No longer supported: Their use results in errors. For example, the closable attribute is not supported for the tag
      cflayoutarea in border layout (cflayout with type="border").

  · Deprecated: They are still available, but their use is not recommended and they might not be available in future
      releases. Deprecated features might also behave differently now than in previous releases. For example, in
      cfcache tag the following attributes are deprecated: directory, cachedirectory, port, and protocol.

  · Modified behavior: They might behave differently than in previous versions. For example, if you use cfcache
      tag in ColdFusion 9 without end tag (</cfcache>), then instead of caching only the current page (which was
      the behavior in the previous releases), the entire request is cached.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       392
Developing CFML Applications




    · New: These are features newly added to ColdFusion 9. For example, if you use throw as a user-defined function
        in a CFM, analyzer informs that throw is a built-in ColdFusion function and suggests you to rename. If you use
        throw as a user-defined function in a CFC, analyzer informs that throw is a built-in function and suggests you
        to prefix it with object scope. For more details on new features, see example, "What's new in ColdFusion 9" on
        page 5.

You can run the Code Analyzer from the ColdFusion Administrator. Select Code Analyzer from the list of Debugging
& Logging pages.

Note: The Code analyzer does not execute the pages that it checks. Therefore, it cannot detect invalid attribute
combinations if the attribute values are provided dynamically at runtime.


Troubleshooting common problems
A few common problems that you might encounter and ways to resolve them are described here.

For more information on troubleshooting ColdFusion, see the ColdFusion Support Center Testing and
Troubleshooting page at www.adobe.com/go/learn_cfu_troubleshoot_en. For common tuning and precautionary
measurements that can help you prevent technical problems and improve application performance, see the
ColdFusion tech tips article, TechNote number 13810. A link to the article is located near the top of the Testing and
Troubleshooting page.


CFML syntax errors
Problem: You get an error message such as the following:

 Encountered "function or tag name" at line 12, column 1.
Encountered "\"" at line 37, column 20.
Encountered "," at line 24, column 61.
Unable to scan the character '\"' which follows "" at line 38, column 53.

These errors typically indicate that you have unbalanced <, ", or # characters. One of the most common coding errors
is to forget to close quoted code, number sign-delimited variable names, or opening tags. Make sure the code in the
identified line and previous lines do not have missing characters.

The line number in the error message often does not identify the line that causes the error. Instead, it identifies the
first line where the ColdFusion compiler encountered code that it could not handle as a result of the error.

Problem: You get an error message you do not understand.

Make sure all your CFML tags have matching end tags where appropriate. It is a common error to omit the end tag for
the cfquery, cfoutput, cftable, or cfif tag.

As with the previous problem, the line number in the error message often does not identify the line that causes the
error, but the first line where the ColdFusion compiler encounters code that it could not handle as a result of the error.
Whenever you have an error message that does not appear to report a line with an error, check the code that precedes
it for missing text.

Problem: Invalid attribute or value.

If you use an invalid attribute or attribute values, ColdFusion returns an error message. To prevent such syntax errors,
use the CFML Code Analyzer. Also see "Using the cftrace tag to trace execution" on page 385.

Problem: You suspect that there are problems with the structure or contents of a complex data variable, such as a
structure, array, query object, or WDDX-encoded variable.

Use the cfdump tag to generate a table-formatted display of the variable's structure and contents. For example, to dump
a structure named relatives, use the following line. Surround the variable name with number signs (#).



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  393
Developing CFML Applications




<cfdump var=#relatives#>


Data source access and queries
Problem: You cannot make a connection to the database.

Create the data source before you can connect. Connection errors can include problems with the location of files,
network connections, and database client library configuration.

Create data sources before you refer to them in your application source files. Verify that you can connect to the
database by clicking the Verify button on the Data Sources page of the ColdFusion Administrator. If you are unable to
make a simple connection from that page, you might need to consult your database administrator to help solve the
problem.

Also, check the spelling of the data source name.

Problem: Queries take too long.

Copy and paste the query from the Queries section of the debugging output into the query analysis tool of your
database. Then retrieve and analyze the execution plan generated by the query optimizer of the database server. (The
method for doing this varies from dbms to dbms.) The most common cause of slow queries is the lack of a useful index
to optimize the data retrieval. In general, avoid table scans (or "clustered index" scans) whenever possible.


HTTP/URL
Problem: ColdFusion cannot correctly decode the contents of your form submission.

The method attribute in forms sent to the ColdFusion server must be Post, for example:

<form action="test.cfm" method="Post">

Problem: The browser complains or does not send the full URL string when you include spaces in URL parameters.

Some browsers automatically replace spaces in URL parameters with the %20 escape sequence, but others might
display an error or just send the URL string up to the first character (as does Netscape 4.7).

URL strings cannot have embedded spaces. Use a plus sign (+) or the standard HTTP space character escape sequence
(%20), wherever you want to include a space. ColdFusion correctly translates these elements into a space.

A common scenario in which this error occurs is when you dynamically generate your URL from database text fields
that have embedded spaces. To avoid this problem, include only numeric values in the dynamically generated portion
of URLs.

Or, you can use the URLEncodedFormat function, which automatically replaces spaces with %20 escape sequences. For
more information on the URLEncodedFormat function, see the CFML Reference.



Using the ColdFusion Debugger

Adobe ColdFusion provides debugging information for individual pages. However, for complex development tasks,
you require a robust and interactive debugger. ColdFusion provides a line debugger that you can use when developing
ColdFusion applications in Eclipse or Adobe Flash Builder. You can set breakpoints, step over, into, or out of code,
and inspect variables. You can also view ColdFusion log files.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 394
Developing CFML Applications




About the ColdFusion Debugger
The ColdFusion Debugger is an Eclipse plugin. It runs in the Eclipse Debug perspective. You can use the ColdFusion
Debugger to perform debugging tasks, including the following:

· Setting breakpoints

· Viewing variables

· Stepping over, into, and out of function calls


Install and uninstall the ColdFusion Debugger
To use the ColdFusion Debugger, you must have the following software installed:

· Eclipse version 3.1.2, Eclipse version 3.2, Flex Builder 2, or Flash Builder

· ColdFusion 9

To install the ColdFusion Debugger, you install the ColdFusion Eclipse plugins. For more information, see Installing
ColdFusion.


Set up ColdFusion to use the Debugger
Before you can use the Debugger, enable debugging in the ColdFusion Administrator.

1 In the ColdFusion Administrator, select Debugging & Logging > Debugger Settings.

2 Enable the Allow Line Debugging option.

3 Specify the port to use for debugging if different from the default that appears.

4 Specify the maximum number of simultaneous debug session if different from the default.

5 Click Submit Changes.

6 You may have to increase the time after which requests time-out by doing the following:

  a Select Server Settings > Settings.

  b Enable the Timeout Requests After (Seconds) option.

  c Enter 300 or other appropriate number in the text box.

7 The debugger server listens for commands from the Eclipse client from a separate port from the one specified in
  step 3. By default, ColdFusion launches the debugger server with a random available port. This could be a problem
  if ColdFusion (and hence debugger server) is behind a firewall and the firewall blocks the random port that the
  debugger is listening..

  To prevent this problem, you can specify a fixed debugger server port number and allow this port in the firewall.
  To set a fixed debugger server port number, specify the following JVM argument on the Java and JVM page of the
  ColdFusion Administrator (or the appropriate place for you J2EE Application Server), replacing portNumber with
  the port you want to use:

   -DDEBUGGER_SERVER_PORT=portNumber

8 Restart ColdFusion. If you are running the J2EE configuration of ColdFusion, restart the server in debug mode with
  the debug port as specified.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        395
Developing CFML Applications




9 To modify the debug settings, in Eclipse, select Window > Preferences > ColdFusion > Debug Settings. You can
   specify the home page URL, which points to the page that appears in the Debug Output Buffer of the debugger when
   you click the Home button. You can also specify the extensions of the types of files that you can debug and variable
   scopes that you want the Debugger to recognize. To improve performance when debugging large files, deselect all
   scopes for which you do not require information.

   Note: To ensure that the debugger stops in the template you are debugging on the line that causes a ColdFusion error,
   select Preferences > ColdFusion > Debug Settings and select the Enable Robust Exception Information checkbox.

10 To configure an RDS server, in Eclipse, select Window > Preferences > ColdFusion > RDS Configuration.

   If you are running ColdFusion on the same computer as Eclipse, localhost is configured by default. To use any
   additional RDS servers, enter the configuration information.

11 If ColdFusion and Eclipse are not running on the same computer, in Eclipse, select Window > Preferences >
   ColdFusion > Debug Mappings. Then specify the path that Eclipse uses to open files on the ColdFusion server and
   the path that ColdFusion uses to find the files that you are editing in Eclipse.

   Mapping ensures that Eclipse and ColdFusion are working on the same file. For example, if you are editing files in
   an Eclipse project that points to D:\MyCoolApp. Then, when you deploy the files to the ColdFusion server, you
   copy them to W:\websites\MyCoolSite\, which the ColdFusion server recognizes as
   D:\Shared\websites\MyCoolSite. The mapping in Eclipse specifies that the Eclipse directory is D:\MyCoolApp and
   the server is D:\Shared\websites\MyCoolSite. Eclipse translates the file path (D:\MyCoolApp\index.cfm) to a path
   that the ColdFusion server recognizes (D:\Shared\websites\MyCoolSite\index.cfm). To see more information
   about the interaction between the client and the server, add the following to the JVM arguments in the ColdFusion
   Administrator:

    -DDEBUGGER_TRACE=true

12 If you are not running the server configuration of ColdFusion, specify Java debugging parameters in the
   configuration file or startup script of the application server you are running. The parameters should look like the
   following:

    -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=<port_number>

   Ensure that the port number you specify is the same port number specified on the Debugger Settings page of
   ColdFusion Administrator.

   If you are running the server configuration, ColdFusion writes these debugging parameters to the jvm.config file
   when you use the Debugger Settings page of the ColdFusion Administrator.

13 If you are not running the server configuration and your application server is not running on JRE 1.6, copy the
   tools.jar file of the JDK version that your application server is running to the \lib folder of ColdFusion. For example,
   if you are running JRun that runs on JRE 1.4, copy the tools.jar file of JDK 1.4 to the \lib folder of ColdFusion.

14 If you are running the server version of ColdFusion and you specify a JRE version other than JRE 1.6 in the
   jvm.config file, copy the tools.jar file of the JDK version specified in your jvm.config file to the \lib folder of
   ColdFusion.

Note: To debug ColdFusion applications running on the multiserver configuration, start the ColdFusion server from the
command line using the following command:

jrun -config <path_to_jvm_config> -start <server_name>


Test and modify the debugger in Eclipse
You can determine whether the Debugger server is configured correctly in Eclipse by selecting Window > Preferences
> ColdFusion > RDS Configuration, and then clicking Test Debugger.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       396
Developing CFML Applications




You can modify RDS configurations easily from the RDS Fileview or RDS Dataview by clicking the Edit RDS
Preferences button in the view.


About the Debug perspective
After you install the ColdFusion Plugin, enable the debugger in ColdFusion, and configure Eclipse, you can use the
ColdFusion Debugger in Eclipse. It is available in the Eclipse Debug perspective.

The Debug perspective includes the following:

· Debug pane, which keeps the results of each completed session. The following buttons appear at the top of this pane:

   · Resume - Resumes a debugging session

   · Suspend - Pauses a debugging session

   · Terminate - Stops a debugging session

   · Disconnect - Disconnects the debugger from the selected debug target when debugging remotely

   · Remove All Terminated Launches - Clears all terminated debug targets from the display

   · Step Into - Executes code line by line, including included code, UDFs, CFCs, and the like

   · Step Over - Executes code line by line, excluding included code, UDFs, CFCs, and the like

   · Step Return - Returns to the original page from which you entered the included code, UDF, CFC, or the like

   · Drop to Frame -Reenters a specified stack frame, which is analogous to going in reverse and restarting your
      program partway through

   · Use Step Filters/Step Debug - Ensures that all step functions apply step filters

   · Menu - Displays the menu that lets you manage the view, show system threads, show qualified names, and show
      monitors

· Variables pane, which shows the current variables, including the variable scope. The following buttons appear at
  the top of this pane:

   · Show Type Names - Displays the type of the variables

   · Show Logical Structure - This button is not supported

   · Collapse All - Collapses the information in the panel to show only variable types

· Breakpoints pane - Lists breakpoints in the ColdFusion application. The following buttons appear at the top of this pane:

   · Remove Selected Breakpoints - Removes a breakpoint

   · Remove All Breakpoints - Removes all breakpoints

   · Show Breakpoints Supported by Selected Targets - Displays the breakpoints for what you are currently
      debugging

   · Go to File for Breakpoint - Goes to the file in which the selected breakpoint is set

   · Skip All Breakpoints - Ignores all breakpoints

   · Expand All - Expands the information in the pane

   · Collapse All - Collapses the information in the pane

   · Link with Debug View - Highlights the selected breakpoint when the application stops execution in the Debug View

   · Add Java Exception Breakpoint - Lets you specify which Java exception to throw when you reach the selected
      breakpoint




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        397
Developing CFML Applications




   · Menu - Lets you specify the type of information to display in the Breakpoints pane

· Debug Output Buffer - Contains two panes: Browser, which displays what appears in the browser during
   application execution; Server Output Buffer, which displays the debug output.

· Edit pane, which displays the stacked source panes, one for each source file you have open.

· Outline pane, which displays the current source file's content in outline form


Using the ColdFusion Debugger
After you enabled the debugger in the ColdFusion Administrator and configure Eclipse, you can debug ColdFusion
pages that are in an Eclipse project.

You can use the ColdFusion Debugger to do the following tasks:

· Setting a breakpoint

· Executing code line by line

· Inspecting variables


Begin debugging a ColdFusion application
1 Open the file in the Eclipse project to debug.

   You do not have to create an Eclipse project in the same folder as CFML source. You can create a project in a
   different folder, create a folder under that project, and then link it to the folder where CFML sources reside.

2 Click Debug in the upper-right corner of the Eclipse workbench to go to the Debug perspective.

3 Select Window > Show View > Debug Output Buffer to see the output from your application and how your
   application appears in a browser.

4 Select Window > Preferences and specify the home page for your debugging session, the extensions of the file types
   that you can debug, and the variable scopes of the variables to show in the Variables pane. Click OK.

   The home page is the page that appears in the Debug Output Buffer pane when you click the Home button in the
   Debug Output Buffer pane.

5 To begin debugging the file whose source appears in the Edit pane, click the Debug icon in the Eclipse toolbar.

6 Click New to create a new debugging configuration.

7 Specify the home page for the active debug session.

   This is the page that appears in the Debug Output Buffer pane when you click the Debug Session Home button in
   the Debug Output Buffer pane.

8 Click Debug to start the debug session.

Note: If you are in the process of debugging a template and then try to browse to or refresh that page, doing so can result
in unexpected behavior in the Debugger.


Setting a breakpoint
You can set breakpoints in your CFML file to stop execution of the page at particular points. When you set a
breakpoint on a line, execution of the CFML stops just before that line. For example, if you set a breakpoint on the
third line in the following CFML page, execution stops before <cfset myName = "Wilson">.

 <cfset yourName = "Tuckerman">
<cfoutput>Your name is #yourName#.</cfoutput>
<cfset myName = "Wilson"




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         398
Developing CFML Applications




Run the page that you want to debug before setting any breakpoints to compile it before debugging it. This improves
performance during debugging. You cannot set a breakpoint in a file that is not part of a project.

1 In Eclipse, open the file in which you want to set a breakpoint.

2 While highlighting the line where you want to set the breakpoint, do one of the following:

    · Double-click in the marker bar that appears to the left of the editor area.

    · Right click, and then select Toggle Breakpoint.

    · Press Alt+Shift+B.

    A blue dot appears before the line on which you set the breakpoint.

    Also, you can view a list of breakpoints set in the current Eclipse project in the Breakpoints panel.

    ColdFusion breakpoints have four states in the Eclipse debugger:

    · Enabled and Valid - This is a breakpoint at a valid location. It is represented by a solid blue circle and stops code
       execution when encountered.

    · Unresolved - ColdFusion sets the breakpoint for the page that is loaded in its memory. If you modify the page
       and do not execute it, the source is not in sync with the page that ColdFusion sees on the server. In this situation,
       ColdFusion may consider the line where you want to set breakpoint to be invalid. However, you have not yet
       executed the page; when you do so, that line may be valid. This type of breakpoint is represented by a question
       mark (?) icon.

       For performance reasons, ColdFusion does not try to resolve unresolved breakpoints every time you execute the
       page. It tries to resolve them when you modify the page and execute it. If you think that the line at which
       ColdFusion shows an unresolved breakpoint is valid, delete the breakpoint and set it again.

    · Invalid - If ColdFusion determines that the CFML that you edit in Eclipse is the same as the CFML in its
       memory, and that the breakpoint you have set is at an invalid line, the breakpoint appears as a red X.

    · Disabled.


Executing code line by line
You can use the Step Into, Step Over, and Step Return buttons to proceed through your CFML application line by line.
Use Step Into to proceed into included files, such as UDFs or CFCs. Use the Step Over button to proceed through your
CFML application, bypassing included files, such as UDFs or CFCs. Use the Step Return button to return to the
original page from which you entered the included file, such as UDFs or CFCs.

For the stepping process to work properly, clear the cache of compiled classes. To do so, recompile all CFML pages
compiled with an earlier version of ColdFusion. In large files, you might find that stepping and breakpoints are slow.
To improve performance, in Eclipse, select Windows > Preferences > ColdFusion > Debug Settings and deselect all
scopes for which you do not require information.

Avoid using Step In on CFML instructions such as the cfset tag. Step In is more performance intensive than Step
Over. You can use Step In for UDFs, CFCs, custom tags, and included files.

When stepping into functions, tags, and files, Eclipse expects the file to be displayed in one of the open projects. The
file that you are stepping in must be in an open Eclipse project.

Sometimes Eclipse 3.2.1 does not show the stack trace, and step buttons are disabled, even though the debugger has
stopped at a line. To enable the step buttons, click the debugger server instance in the Debug window. To see the stack
trace, click either Step In or Step Out.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  399
Developing CFML Applications




Inspecting variables
As you observe execution of your code, you can see the values and scope of variables in the Variables panel. The
Variables panel displays the scope and value of variables as the CFML code executes. Only variables whose scopes are
those you selected in the Preferences dialog box appear in the Variables pane.


Viewing ColdFusion log files
You can easily see the contents of all the log files that ColdFusion generates by using the Log File Viewer.

1 In Eclipse, select Window > Show View > Other > ColdFusion > CF Log Viewer.

2 To view details of a log file, double-click the name of the file.

3 To include the log files in another folder, click the Add Log Folder button, select the folder, and then click OK.

4 To remove a folder from the list, without deleting it from the computer's file system, click the Remove Log Folder
  button, select the folder, and then click OK.

5 To remove a log file from the computer's file system, click the Delete Log File button.

6 To remove the contents of the detail pane, click the Menu button, and then click Clear Log.

7 To update the contents of the detail pane, click the Menu button, and then click Refresh Log.




                                  