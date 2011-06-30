Chapter 12: ColdFusion Portlets

You can now build your own portlets by leveraging Adobe ColdFusion components (CFCs). You can create your own
portlet using ColdFusion and run it on:

· JBoss portal server

· WebSphere portal server 6.1



Run a ColdFusion portlet on JBoss Portal Server

You can run and access ColdFusion portlets on a JBoss portal server, which can be either local or remote.

· A local host: A portal can access portlets on the same computer where JBoss Portal server exists.

· A remote host: A portal can access portlets deployed on a remote ColdFusion server instance.


Prerequisites
Before you start developing ColdFusion portlets, you must:

· Install JDK 1.5.x.

· For JSR-168, install bundled version of JBoss 2.6.7 or 2.6.8 portal server and JBoss 4.2.3 Application server. For JSR-
  286, install bundled version of JBoss 2.7.2 portal server and JBoss 4.2.3 Application server.

· Deploy ColdFusion on JBoss application server.

· Enable the J2EE sessions in ColdFusion Administrator.


Build a portlet for a local server
To write a ColdFusion portlet:

1 Create a CFC that extends the CFIDE.portlets.ColdFusionPortlet package.

  For example, the following HelloPortlet.cfc extends this package and defines the doView() and doHelp()
  methods:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  900
ColdFusion Portlets




   <cfcomponent extends="CFIDE.portlets.ColdFusionPortlet">


        <cffunction name="doView" returntype="void" output="true">
             <cfargument name="renderRequest" type="any" required="true" hint="A
             javax.portlet.RenderRequest java object">
             <cfargument name="renderResponse" type="any" required="true" hint="A
             javax.portlet.RenderResponse java object">
             <cfoutput>
                   Hello World ColdFusion Portlet
             </cfoutput>
        </cffunction>
        <cffunction name="doHelp" returntype="void" output="true">
             <cfargument name="renderRequest" type="any" required="true" hint="A
             javax.portlet.RenderRequest java object">
             <cfargument name="renderResponse" type="any" required="true" hint="A
             javax.portlet.RenderResponse java object">
             <h1>ColdFusion Help</h1>
             <p>This is a Help message for the Hello Portlet.</p>
        </cffunction>
   </cfcomponent>

2 Save HelloPortlet.cfc in
   <jboss_server_home>\server\default\deploy\cfusion.ear\cfusion.war\portlets\hello.

3 Define HelloPortlet.cfc in portlet.xml, which is located in the WEB-INF folder of ColdFusion web root. The
   portlet.xml file looks similar to the following:

   <?xml version="1.0" encoding="UTF-8"?>
   <portlet-app xmlns="http://java.sun.com/xml/ns/portlet/portlet-app_1_0.xsd"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://java.sun.com/xml/ns/portlet/portlet-app_1_0.xsd
   http://java.sun.com/xml/ns/portlet/portlet-app_1_0.xsd" version="1.0">
        <portlet>
             <description xml:lang="en">This Portlet is a Hello World CF Portlet</description>
             <portlet-name>ColdFusionPortlet</portlet-name>
             <display-name xml:lang="en">CF Hello Portlet</display-name>
             <portlet-class>coldfusion.portlet.ColdFusionPortlet</portlet-class>
             <init-param>
                       <name>cfcName</name>
                       <value>portlets.hello.HelloPortlet</value>
             </init-param>
             <supports>
                   <mime-type>text/html</mime-type>
                   <portlet-mode>VIEW</portlet-mode>
                   <portlet-mode>HELP</portlet-mode>
             </supports>
             <portlet-info>
                   <title>ColdFusion Hello World Portlet</title>
             </portlet-info>
        </portlet>
   </portlet-app>

   The portlet is defined and registered for portlet definition, with targeted cfcName defined as the INIT parameter.
   The INIT parameter value must be from the web root of ColdFusion.

4 Run the JBoss server by running one of the following commands:

   On UNIX




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  901
ColdFusion Portlets




   <JBoss_home>/bin/run.sh

   On Windows

   <JBoss_home>\bin\run.bat

   By default JBoss only binds to localhost. You can have it bind to any IP address by running bin/run.sh -b
   0.0.0.0 on UNIX or bin/run.bat -b 0.0.0.0 on Windows.

5 Launch the JBoss portal server. By default, JBoss binds to port 8080, so launch the server using the URL:
   http://<matching_IP>:<port>/portal/.

   For example: http://127.0.0.1:8080/portal.

6 Log in to the portal by clicking the login link in the upper-right corner. The default credentials are admin/admin.

7 Click the Admin option in the upper-right corner.

8 Click the Portlet Definitions tab. The portlet name, CF HelloPortlet is listed here.




   Portal definition tab displaying CFHelloPortlet


9 Create an instance of this portlet by clicking Create Instance under Actions.

10 Specify the instance name.

11 Add the instance display name.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                           902
ColdFusion Portlets




   Adding instance of CFHelloPortlet


12 Click the Portal Objects tab.

13 Create a new portal page by specifying the portal name in the Create a portal named box.





   Creating a portal


14 Select the created portal page from the list of portals.

15 From Actions, select Page Layout.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 903
ColdFusion Portlets




   Managing created portal


16 From the Portlet instance associated with this Window list, select the portlet instance you created in step 10.




   Portal instance selected




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                            904
ColdFusion Portlets




17 In the Page Layout section, click Add for the center region or left region to add the portlet container in the required area.

18 Go back to the Portal Objects > Portals page and select Make Default option to set the new portal as default.

19 Click the Portal option on the upper-right corner to view the new portal page containing the portlet.




   Portal displaying CFHelloPortlet


Access remote ColdFusion portlets (WSRP)
To access remote ColdFusion portlets and expose them as web services using the Web Service Response Protocol
(WSRP), configure the following:

· WSRP Producer: The Web Service Response Protocol (WSRP) is used to create the producer of the WSDL. A
   WSRP producer can be any ColdFusion standalone, multiserver, or J2EE instance.

· WSRP Consumer: Portlets are exposed as web services, which are then consumed by the portal server (JBoss).


Configure the WSRP producer
1 Create the ColdFusion HelloPortlet.cfc as described in step 1 of "Build a portlet for a local server" on page 899.

2 Save the HelloPortlet.cfc file in the following directory:

   <ColdFusion_webroot>/portlets/hello/

3 Define HelloPortlet.cfc in the cf-wsrp-portlet.xml file.

   <?xml version="1.0" encoding="UTF-8"?>
   <portlet-app xmlns="http://java.sun.com/xml/ns/portlet/portlet-app_1_0.xsd"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://java.sun.com/xml/ns/portlet/portlet-app_1_0.xsd
   http://java.sun.com/xml/ns/portlet/portlet-app_1_0.xsd"
   version="1.0">
        <portlet>
               <description xml:lang="en">This Portlet is a Hello World CF Portlet</description>
               <portlet-name>HelloPortlet</portlet-name>
               <display-name xml:lang="en">Hello Portlet</display-name>
               <portlet-class>portlets.hello.HelloPortlet</portlet-class>
               <supports>
                     <mime-type>text/html</mime-type>
                     <portlet-mode>VIEW</portlet-mode>
                     <portlet-mode>HELP</portlet-mode>
               </supports>
               <supported-locale>en</supported-locale>
               <portlet-info>
                     <title>Hello ColdFusion Portlet</title>
                     <short-title>CF Hello</short-title>
                     <keywords>hello, portlet</keywords>
               </portlet-info>
        </portlet>
   </portlet-app>

   After updating cf-wsrp-portlet.xml, restart your ColdFusion instance.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    905
ColdFusion Portlets




Configure the WSRP consumer
1 Run the JBoss server by running the command:

   On UNIX

   bin/run.sh

   On Windows

   bin\run.bat

   By default JBoss only binds to localhost. You can have it bind to any IP address by running bin/run.sh -b
   0.0.0.0 on UNIX or bin\run.bat -b 0.0.0.0 on Windows.

2 Launch the JBoss portal server. By default, JBoss binds to port 8080, so launch the server using the URL:
   http://localhost:8080/portal/.

3 Log in to the portal by clicking the login link in the upper-right corner. The default credentials are admin/admin.

4 Click the Admin option in the upper-right corner.

5 Click the WSRP tab.

6 Specify the WSRP consumer name, such as wsrp-test in the Create a Consumer Named box.

7 Click Create Consumer.

8 On the next page, specify the WSDL URL of the WSRP producer:

   http://<WSRPProducer_IP>:<port>/<context_root>/WSRPProducer?wsdl

9 Click Refresh & Save. If successful, the following message appears

   "Refresh was successful."

   Note: You can try a demo WSRP consumer hosted by BEA, by entering the WSDL URL:
   http://wsrp.bea.com:7001/producer/producer?WSDL. After clicking Refresh & Save, the system prompts you to
   provide the consumerRole registration property. Use the string "public" and click Update Properties. If the BEA WSRP
   Producer does not get configured, the problem could be with the JBoss Portal Server, rather than the WSRP Producer.
   Make sure that you are using Java 1.5 JVM.

10 Click the Portlet Definitions tab.

11 Select wsrp-test from the View portlets provided by the portlet provider named drop-down list.

12 From the Select the WSRP Portlet You Created box, select the WSRP portlet.

13 Click View Portlets.

14 From Actions column, select Create Instance.

15 Specify the instance name and click Create instance.

16 Optionally, specify the instance display name and other details.

17 Go back to Portal Objects and select default. Alternatively, you can create your own portal object.

18 From the Actions column, select Page Layout.

19 From the Portal Instance Associated to this Window list, select the WSRP portlet instance you created earlier.

20 From the Page Layout section, add the instance to the required region by clicking Add.

21 Go back to the Portal Objects > Portal page.

22 Click Make Default to make the portal which contains the WSRP portlet as default.

23 Click the Portal link on the upper-right corner to view the portal page.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                906
ColdFusion Portlets




Common methods used in portlet.cfc

Some common methods that are used frequently while creating the ColdFusion component portlet, such as
HelloPortlet.cfc, are:


Method         Description                      Syntax

doView()       This method renders the          <cffunction name="doView" returntype="void" output="true">
               portlet content. It is called by <cfargument name="renderRequest" type="any" required="true" hint="A
               the portlet container to allow   javax.portlet.RenderRequest java object">
               the portlet to generate the      <cfargument name="renderResponse" type="any" required="true"

               content of the response based    hint="A javax.portlet.RenderResponse java object">

               on its current state.            <!--- User code goes here --?>
                                                </cffunction>

doHelp()       Helper method to serve up the    <cffunction name="doHelp" returntype="void" output="true">
               HELP mode.                       <cfargument name="renderRequest" type="any" required="true" hint="A
                                                javax.portlet.RenderRequest java object">
                                                <cfargument name="renderResponse" type="any" required="true"
                                                hint="A javax.portlet.RenderResponse java object">
                                                <!--- User code goes here --? </cffunction>

doEdit()       Helper method to serve up the    <cffunction name="doEdit" returntype="void" output="true">
               EDIT mode.                       <cfargument name="renderRequest" type="any" required="true" hint="A
                                                javax.portlet.RenderRequest java object"><cfargument
                                                name="renderResponse" type="any" required="true" hint="A
                                                javax.portlet.RenderResponse java object">
                                                <!--- User code goes here --? </cffunction>

ProcessAction  Called by the portlet container  <cffunction name="processAction" returntype="void" access="public"
()             to allow the portlet to process  output="false" hint="Called by the portlet container to allow the
               an action request                portlet to process an action request.">
                                                <cfargument name="actionRequest" type="any" required="true" hint="A
                                                javax.portlet.ActionRequest java object">
                                                <cfargument name="actionResponse" type="any" required="true"
                                                hint="A javax.portlet.ActionResponse java object">
                                                <!--- User code goes here --? </cffunction>

Init()         Called by the portlet container  <cffunction name="init" returntype="void" access="public"
               to indicate to a portlet that theoutput="false" hint="Called by the portlet container to indicate to
               portlet is being placed into     a portlet that the portlet is being placed into service.">
               service                          <cfargument name="portletConfig" type="any" required="true" hint="A
                                                javax.portlet.PortletConfig java object">
                                                <!--- User code goes here --? </cffunction>

processEvent   This is used to consume the      <cffunction name="processEvent" returntype="void" access="public"
               event once it is published.      output="false" hint="Called by the portlet container requesting the
                                                portlet to process a specific event.">
                                                <!---user code--->
                                                </cffunction>




ColdFusion portlet components

You can configure your ColdFusion portlet components to define its modes, window states, title, scope, and
parameters.

For references of ColdFusion Portlet API, see the JSR-168 specification for all javax.portlet.* classes.

Currently, WSRP 1.0 is the supported standard for portlets.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   907
ColdFusion Portlets




Portlet modes
Portal servers typically allow three portlet modes: View, Edit, and Help.

The View mode is the default state when rendering a portlet. The portlet window has links in the title bar that enable
you to change the mode to Help or Edit.

To add a Help mode view, add the doHelp() with the same signature as the doView() function.

To support the edit mode create the doEdit().


More Help topics
"Common methods used in portlet.cfc" on page 906


Portlet window states
Most portal servers support three window states (normal, minimized, and maximized). You can obtain the current
window state by calling the getWindowState() method of the ColdFusionPortlet base component.


Portlet title
To set the portlet title, add a method to the CFC called getTitle as follows:

<cffunction name="getTitle" returntype="string" output="false" access="public">
          <cfargument name="renderRequest" type="any" required="true" hint="A
          javax.portlet.RenderRequest java object">
          <cfreturn "My ColdFusion Portlet">
</cffunction>



Portlet scope
The ColdFusion portlet toolkit defines the variable request.portlet. It contains the following structures:

request.portlet.parameters - Parameters of the Portlet Request
request.portlet.attributes - attributes of the Portlet Request
request.portlet.properties - properties of the Portlet Request

These variables are defined for convenience and convention.


Create portlet parameters
To create different page views within your portlet you can configure the render parameters such as renderURL.

For example, to set the renderURL parameter:

1 Create a renderURL parameter

   <cfset params = StructNew()>
   <cfset params.page = "somepage">
   <cfoutput><a href="#createRenderURL(params)#">Link to somepage</a>

2  Check for parameter in the page and render conditionally:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                          908
ColdFusion Portlets




   <cfparam name="request.portlet.parameters.page" default="">
   <cfif request.portlet.parameters.page IS "somepage">
        <cfinclude template="somepage.cfm">
   <cfelse>
        <!--- put step 1 here --->
   </cfif>



Processing actions using form posts
To process a form post, use the createActionURL() function, which generates the form action URL.

For example:

<cfoutput>
    <form action="#createActionURL()#" method="post">
          Value:    <input type="text" name="action_value" >
                    <input type="submit" value="Process Action" />
    </form>
</cfoutput>

When the form is submitted, the portal container calls the processAction() method in your CFC. So, add this
method as follows:

<cffunction name="processAction" returntype="void" access="public" output="false"
hint="Called by the portlet container to allow the portlet to process an action request.">
    <cfargument name="actionRequest" type="any" required="true" hint="A
    javax.portlet.ActionRequest java object">
    <cfargument name="actionResponse" type="any" required="true" hint="A
    javax.portlet.ActionResponse java object">
    <cfif IsDefined("request.portlet.parameters.action_value")>
    <!--- do something with this value, such as update your database --->
    </cfif>
</cffunction>



Examples
The following examples show how you can configure portlets. You can add the following code to the doView()
method depending on whether you are configuring the portlet on a local or remote server.

· To get portal user information:

   JSR:

   <cfoutput>#renderRequest.getRemoteUser()#</cfoutput>

   WSRP:

   <cfdump var = #renderRequest.getAttribute("javax.portlet.userinfo")#>

· To display PDF:

   <cfdocument format="pdf" src="http://www.google.com" filename="cfdoc1.pd f "
   overwrite="true">
   </cfdocument>
   <cfset pdfURL = getPortletResponse().encodeURL(getPortletRequest().getContextPath() &
   "/<path of pdf>/cfdoc1.pdf")>
   <cfoutput>
        <object data="#pdfURL#" type="application/pdf" width="600" height="400">
        </object>
   </cfoutput>



                                           

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   909
ColdFusion Portlets




· To display Ajax components, all the URLs used in portlets must be encoded.

    CFPOD:
    <cfset sourceURL = getPortletResponse().encodeURL(getPortletRequest().getContextPath() &
    "/<path to cfm>/expandpath.cfm")>
    <cfpod name="pod01" source="#sourceURL#" height="500" width="300" title="Example CFPod"/>
    expandpath:
    <cfoutput>#ExpandPath("./")#</cfoutput>
    CFWINDOW:
    <cfset sourceURL = getPortletResponse().encodeURL(getPortletRequest().getContextPath() &
    "/<path to cfm>/expandpath.cfm")>
    <cfwindow title="Test Window" name="myWindow" width="200" height="200" initShow="true"
    source="#sourceURL#">
    </cfwindow>




JSR-286 Support

ColdFusion 9 also supports JSR-286 specifications. In portlets there are three types of requests: action, event, and
render. A portlet first handles an action request, and then an event request, and only after that, it would render any
request.

Some of the capabilities of JSR-286 include the following:


Publishing and Processing Events
To define an event, you would have to declare it in portlet.xml

<event-definition>
<qname xmlns:cf="http://adobe.com/coldfusion/portlet/example">cf:HelloEvent
</qname>
     <value-type>java.lang.String</value-type>
</event-definition>

This code defines an event named cf:HelloEvent, where cf refers to the namespace and HelloEvent is the local name.
Its type is defined by the <value-type> tag. These event definitions require you to use qname to uniquely identify the
event.

Now add the events to specific portlets, which are either going to publish (generate) an event or process (consume) an
event. You add this information to portlet.xml as well.

<supported-publishing-event> tag is used to publish an event.



Publishing an event (Event producer)
<portlet>
...
<supported-publishing-event>
<qname xmlns:cf="http://adobe.com/coldfusion/portlet/example">cf:HelloEvent</qname>
</supported-publishing-event>
...
</portlet>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                910
ColdFusion Portlets




Processing an event (event consumer)
<portlet>
....
<supported-processing-event>
<qname xmlns:cf="http://adobe.com/coldfusion/portlet/example">cf:HelloEvent</qname>
</supported-processing-event>
.....
</portlet>

Portlet definition can have both publishing and processing tags. portlet.xml file has event definition, publishing
event, and processing event tags, as its portlets create and consume events.


Initiate events in portlet (CFC)
You publish events in the processAction() method of the portlet code by calling setEvent() on the
ActionResponse object. This setEvent() method takes two parameters: the QName of the event object and the type
of object defined in portlet.xml.

Following is an example of processAction() method.

<cffunction name="processAction" returntype="void" access="public" output="false"
hint="Called by the portlet container to allow the portlet to process an action request.">
          <cfargument name="actionRequest" type="any" required="true" hint="A
javax.portlet.ActionRequest java object">
          <cfargument name="actionResponse" type="any" required="true" hint="A
javax.portlet.ActionResponse java object">
          <cfset super.processAction(arguments.actionRequest, arguments.actionResponse)>
          <!--- send event notification --->
          <cftry>
                <cfset arguments.actionResponse.setEvent("HelloEvent",
request.portlet.parameters.event_value)>
                <cfcatch type="any">
                     <cflog file="simple-event-portlet" type="error" text="processAction() threw
exception: #cfcatch.message#">
                </cfcatch>
          </cftry>
</cffunction>

Capture events in processEvent() method:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                          911
ColdFusion Portlets




<cffunction name="processEvent" returntype="void" access="public" output="false" hint="Called
by the portlet container requesting the portlet to process a specific event.">
           <cfargument name="eventRequest" type="any" required="true" hint="A
javax.portlet.EventRequest java object">
           <cfargument name="eventResponse" type="any" required="true" hint="A
javax.portlet.EventResponse java object">
     <cfset var e = StructNew()>
     <cftry>
           <cfset e.name = arguments.eventRequest.getEvent().getName()>
           <cfset e.value = arguments.eventRequest.getEvent().getValue()>
     <cfif NOT IsDefined("application.EventReceivingPortletEvents")>
           <cfset application.EventReceivingPortletEvents = ArrayNew(1)>
     </cfif>
           <cfset ArrayAppend(application.EventReceivingPortletEvents,e)>
                <cfcatch type="any">


                </cfcatch>
     </cftry>
</cffunction>



Using filters
Filter definition and mapping in portlet.xml

<filter>
     <filter-name>Example ColdFusion Filter</filter-name>
     <filter-class>coldfusion.portlet.ColdFusionPortletFilter</filter-class>
     <lifecycle>RENDER_PHASE</lifecycle>
     <lifecycle>EVENT_PHASE</lifecycle>
     <lifecycle>RESOURCE_PHASE</lifecycle>
     <lifecycle>ACTION_PHASE</lifecycle>
     <init-param>
           <name>cfcName</name>
           <value>portlets.filter.ExampleFilter</value>
     </init-param>
</filter>

Add filter mapping, that filter applied to particular portlet.

<!-- Applies Example Filter to All Portlets -->
     <filter-mapping>
           <filter-name>Example ColdFusion Filter</filter-name>
           <portlet-name>*</portlet-name>
     </filter-mapping>

ExampleFilter.cfc:

The following is the ExampleFilter.cfc mentioned in the portlet.xml.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                          912
ColdFusion Portlets




<cfcomponent extends="CFIDE.portlets.filter.ColdFusionPortletFilter">
          <cffunction name="doRenderFilter" returntype="void">
          <cfargument name="renderRequest">
          <cfargument name="renderResponse">
          <cfargument name="filterChain">
          <cflog file="portlet-filter" type="information" text="doRenderFilter() invoked">
          <!--- call the next filter in the chain --->
          <cfset arguments.filterChain.doFilter(arguments.renderRequest,
arguments.renderResponse)>
    </cffunction>
    <cffunction name="doActionFilter" returntype="void">
          <cfargument name="actionRequest">
          <cfargument name="actionResponse">
          <cfargument name="filterChain">
          <cflog file="portlet-filter" type="information" text="doActionFilter() invoked">
          <!--- call the next filter in the chain --->
          <cfset arguments.filterChain.doFilter(arguments.actionRequest,
arguments.actionResponse)>
    </cffunction>
    <cffunction name="doResourceFilter" returntype="void">
          <cfargument name="resourceRequest">
          <cfargument name="resourceResponse">
          <cfargument name="filterChain">
          <cflog file="portlet-filter" type="information" text="doResourceFilter() invoked">
          <!--- call the next filter in the chain --->
          <cfset arguments.filterChain.doFilter(arguments.resourceRequest,
arguments.resourceResponse)>
    </cffunction>
    <cffunction name="doEventFilter" returntype="void">
          <cfargument name="eventRequest">
          <cfargument name="eventResponse">
          <cfargument name="filterChain">
          <cflog file="portlet-filter" type="information" text="doEventFilter() invoked">
          <!--- call the next filter in the chain --->
          <cfset arguments.filterChain.doFilter(arguments.eventRequest,
arguments.eventResponse)>
    </cffunction>
</cfcomponent>




Run a ColdFusion portlet on WebSphere Portal Server

To access and run ColdFusion portlets on WebSphere Portal Server 6.1:

1 Create cfusion.war file.

2 Extract it using jar -xvf cfusion.war

3  Create a /portlets directory under cfusion.war.

4 Add portlets to the /portlets directory. Add portlet entries to portlet.xml, present at:

   cfusion.war/WEB-INF/portlet.xml




                                           

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                          913
ColdFusion Portlets




5 Go to repackage the WAR file using jar cvf cfusion.war.

6 Deploy it through WebSphere Portal Server administrator console. The portlets would be visible present in
   portlet.xml

7 Create a portal page and add the portlets.