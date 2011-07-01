## Chapter 3: Introducing ColdFusion

You use Adobe ColdFusion to create dynamic Internet applications.

### About Internet applications and web application servers

With ColdFusion, you develop Internet applications that run on web application servers.

### About web pages and Internet applications

The Internet has evolved from a collection of static HTML pages to an application deployment platform. First, the Internet changed from consisting of static web pages to providing dynamic, interactive content. Rather than providing unchanging content where organizations merely advertise goods and services, dynamic pages enable companies to conduct business ranging from e-commerce to managing internal business processes. For example, a static HTML page lets a bookstore publish its location, list services such as the ability to place special orders, and advertise upcoming
events like book signings. A dynamic website for the same bookstore lets customers order books online, write reviews of books they read, and even get suggestions for purchasing books based on their reading preferences.

More recently, the Internet has become the underlying infrastructure for a wide variety of applications. With the arrival of technologies such as XML, web services, J2EE (Java 2 Platform, Enterprise Edition), and Microsoft .NET, the Internet has become a multifaceted tool for integrating business activities. Now, enterprises can use the Internet to integrate distributed activities, such as customer service, order entry, order fulfillment, and billing.

Adobe ColdFusion is a rapid application development environment that lets you build dynamic websites and Internet applications quickly and easily. It lets you develop sophisticated websites and Internet applications without knowing the details of many complex technologies, yet it lets advanced developers take advantage of the full capabilities of many of the latest Internet technologies. 

### About web application servers

Typically, web browsers make requests, and web servers, such as Microsoft Internet Information Server (IIS) and the Apache web server, fulfill those requests by returning the requested information to the browser. This information includes, but is not limited to, HTML and FLA files.

Web server capabilities are limited because all it does is wait for requests to arrive and attempt to fulfill those requests as soon as possible. A web server does not let you do the following tasks:

* Interact with a database, other resource, or other application.
* Serve customized information based on user preferences or requests.
* Validate user input.

A web server, basically, locates information and returns it to a web browser.

To extend the capabilities of a web server, you use a web application server, a program that extends web server capabilities to do tasks such as those in the preceding list.

### How a web server and web application server work together

The following steps explain how a web server and web application server work together to process a page request:

1. The user requests a page by typing a URL in a browser, and the web server receives the request.
2. The web server looks at the filename extension to determine whether a web application server must process the page. Then, one of the following actions occur:
* If the user requests a file that is a simple web page (often one with an HTM or HTML extension), the web server fulfills the request and sends the file to the browser.
* If the user requests a file that is a page that a web application server must process (one with a CFM, CFML, or CFC extension for ColdFusion requests), the web server passes the request to the web application server. The web application server processes the page and sends the results to the web server, which returns those results to the browser. The following image shows this process:
3. Web server instructs application server to
1. Web browser                   
2. Web server receives process the page. requests a w eb page. the page request..



Internet                                               Application
                                                                              Web Server
                                                                                                       Server
5. The web server       
4. The application server sends the output processes the page and to the browser. generates output.
                                                         
Because web application servers interpret programming instructions and generate output that a web browser can interpret, they let web developers build highly interactive and data-rich websites, which can do tasks such as the following:

* Query other database applications for data.
* Dynamically populate form elements.
* Dynamically generate Flash data.
* Provide application security.
* Integrate with other systems using standard protocols such as HTTP, FTP, LDAP, POP, and SMTP.
* Create shopping carts and e-commerce websites.
* Respond with an e-mail message immediately after a user submits a form.
* Return the results of keyword searches.

### About ColdFusion

Adobe ColdFusion is a rapid scripting environment server for creating dynamic Internet Applications. ColdFusion Markup Language (CFML) is a tag-based scripting language that is easy to learn. CFML provides connectivity to enterprise data and powerful built-in search and charting capabilities. ColdFusion enables developers to easily build and deploy dynamic websites, content publishing systems, self-service applications, commerce sites, and more.

ColdFusion pages are plain text files that you use to create web applications. You can create your ColdFusion applications by writing all the code manually or by using wizards (provided with some editors) to generate the majority of the code for you.

### Saving ColdFusion pages

In order for the ColdFusion server to process a page, save the ColdFusion page on a computer where ColdFusion is installed. If you are creating your pages on a local server (on which ColdFusion is running), you can save the pages locally; if you are using a remote server, save your pages on that server.

If you are using the J2EE configuration, you typically save ColdFusion pages under the ColdFusion web application root. For example, in the default directory structure when you use the J2EE configuration with JRun, you save pages under jrun_root/servers/cfusion/cfusion-ear/cfusion-war.

### Testing ColdFusion pages

To ensure that the code you wrote is working as expected, you view the ColdFusion page in a browser by going to the appropriate URL, for example http://localhost/test/mypage.cfm. If you are using the built-in web server, specify the port to use in the URL, for example, http://localhost:8500/test/cfpage.cfm. The address localhost is only valid when you view pages locally.

Note: On Vista, the address ::1 is equivalent to localhost. You can use the ColdFusion GetLocalHostIP function to get the IP address of localhost.

The URL for a remote site includes the server name or IP address of the server where ColdFusion is installed; for example, http://<serveripaddress>/test/mypage.cfm. Some ColdFusion J2EE configurations require a context root in the URL; for example, http://<server>/<context-root>/mypage.cfm. For example, if you deploy an EAR file and use the default context root of cfconroot, you specify http://localhost/cfconroot/test/mypage.cfm.


### Elements of ColdFusion

ColdFusion consists of the following core elements:
* ColdFusion scripting environment
* CFML
* ColdFusion Administrator
* Verity Search Server

### The ColdFusion scripting environment

The ColdFusion scripting environment provides an efficient development model for Internet applications. At the heart of the ColdFusion scripting environment is the ColdFusion Markup Language (CFML), a tag-based programming language that encapsulates many of the low-level details of web programming in high-level tags and functions.

### ColdFusion Markup Language

ColdFusion Markup Language (CFML) is a tag-based language, like HTML, that uses special tags and functions. With CFML, you can enhance standard HTML files with database commands, conditional operators, high-level formatting functions, and other elements to rapidly produce web applications that are easy to maintain. However, CFML is not limited to enhancing HTML. For example, you can create Flash output that consist entirely of Flash elements and CFML. Similarly, you can use CFML to create web services for use by other applications.

For more information, see "Elements of CFML" on page 17.

### CFML tags
CFML looks like HTML--it includes starting and, in most cases, ending tags, and each tag is enclosed in angle brackets. All ending tags are preceded with a forward slash (/) and all tag names are preceded with cf; for example:

'''
 <cftagname>
     tag body text and CFML
</cftagname>
'''

CFML increases productivity by providing a layer of abstraction that hides many low-level details involved with Internet application programming. At the same time, CFML is powerful and flexible. ColdFusion lets you easily build applications that integrate files, databases, legacy systems, mail servers, FTP servers, objects, and components.

CFML tags serve many functions. They provide programming constructs, such as conditional processing and loop structures. They also provide services, such as charting and graphing, full-text search, access to protocols such as FTP, SMTP/POP, and HTTP, and much more. The following table lists a few examples of commonly used 

ColdFusion tags:


Tag                     Purpose

cfquery                 Establishes a connection to a database (if one does not exist), executes a query, and returns results to the
                         ColdFusion environment.

cfoutput                Displays output that can contain the results of processing ColdFusion functions, variables, and expressions.

cfset                   Sets the value of a ColdFusion variable.

cfmail                  Lets an application send SMTP mail messages using application variables, query results, or server files. (Another
                         tag, cfpop, gets mail.)

cfchart                 Converts application data or query results into graphs, such as bar charts or pie charts, in Flash, JPG, or PNG
                         format.

cfobject                Invokes objects written in other programming languages, including COM (Component Object Model)
 
components, Java objects such as Enterprise JavaBeans, or Common CORBA (Object Request Broker Architecture) objects. CFML Reference describes the CFML tags in detail.

###  CFML functions and CFScript
CFML includes built-in functions that perform a variety of roles, including string manipulation, data management, and system functions. CFML also includes a built-in scripting language, CFScript, that lets you write code in a manner that is familiar to programmers and JavaScript writers.


### CFML extensions

You can extend CFML further by creating custom tags or user-defined functions (UDFs), or by integrating COM, C++, and Java components (such as JSP tag libraries). You can also create ColdFusion components (CFCs), which encapsulate related functions and properties and provide a consistent interface for accessing them.

All these features let you easily create reusable functionality that is customized to the types of applications or websites that you are building.
                                                 
### CFML development tools
Adobe Dreamweaver CS3 helps you develop ColdFusion applications efficiently. It includes many features that simplify and enhance ColdFusion development, including tools for debugging CFML. Because CFML is written in an HTML-like text format, and you often use HTML in ColdFusion pages, you can also use an HTML editor or a text editor, such as Notepad, to write ColdFusion applications.

ColdFusion 9 includes a line debugger that you can use to debug your ColdFusion applications in EclipseTM or Adobe FlexTM BuilderTM.


### Verity Search Server
The Verity Search Server (also called the Verity search engine) provides full text search capability for documents and data on a ColdFusion site.

### ColdFusion Administrator
ColdFusion Administrator configures and manages the ColdFusion application server. It is a secure web-based application that you can access using any web browser, from any computer with an Internet connection. It includes a Server Monitor, which lets you see the status of your ColdFusion server.

For more information about ColdFusion Administrator, see Configuring and Administering ColdFusion.

### About J2EE and the ColdFusion architecture

As the Internet software market has matured, the infrastructure services required by distributed Internet applications, including ColdFusion applications, have become increasingly standardized. The most widely adopted standard today is the Java 2 Platform, Enterprise Edition (J2EE) specification. J2EE provides a common set of infrastructure services for accessing databases, protocols, and operating system functionality, across multiple operating systems.


### About ColdFusion and the J2EE platform
ColdFusion is implemented on the Java technology platform and uses a J2EE application server for many of its base services, including database connectivity, naming and directory services, and other runtime services. ColdFusion can be configured to use an embedded J2EE server (in the server configuration) or it can be deployed as a J2EE application on an independent J2EE application server (in the multiserver configuration or the J2EE configuration). ColdFusion Enterprise includes a fully featured version of the JRun J2EE application server, or can be deployed on third-party J2EE servers such as IBM WebSphere and BEA WebLogic.

For more information on ColdFusion configurations, see Installing ColdFusion.

By implementing the ColdFusion scripting environment on top of the J2EE platform, ColdFusion takes advantage of the power of the J2EE platform while also providing an easy-to-use scripting environment and built-in services. Moreover, because ColdFusion is built on a J2EE platform, you can easily integrate J2EE and Java functionality into your ColdFusion application. As a result, ColdFusion pages can do any of the following:

* Share session data with JSPs (Java Server Pages) and Java servlets.
* Import custom JSP tag libraries and use them like ColdFusion custom tags.
* Integrate with Java objects, including the J2EE Java API, JavaBeans, and Enterprise JavaBeans.

For more information on using J2EE features in ColdFusion, see "Integrating J2EE and Java Elements in CFML Applications" on page 1125.