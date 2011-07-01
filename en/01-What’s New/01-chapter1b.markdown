Chapter 1: What's New The following sections explain what is new and
changed in ColdFusion 9 and ColdFusion 9 update. What's new in
ColdFusion 9.0 Update 1 Area What's new and changed Language Support for
the following: \* for-in construct (for arrays) in CFScript \* var
declaration within for loop in CFScript \* Function argument metadata \*
Function equivalents for cfile action="upload" (FileUpload) and cffile
action="uploadall" (FileUploadAll) The following script functions have
been implemented as CFCs: \* dbinfo \* imap \* pop \* ldap \* feed
Caching \* New function cacheGetSession \* New parameter template in the
function cacheGetMetadata \* cacheGetProperties and cacheSetProperties
support diskSpoolBufferSizeMB, clearOnFlush, and
diskExpiryThreadIntervalSeconds \* Using user-defined caching regions in
all cache functions except cacheGetProperties and cacheSetProperties IIS
7 IIS 7 configuration for ColdFusion has no dependency on IIS 6 Metabase
compatibility. DEVELOPING COLDFUSION 9 APPLICATIONS 2 What's New Area
What's new and changed ColdFusion Ajax \* Support for CFCs outside
webroot \* ColdFusion.FileUpload.getselectedfiles returns the status of
upload operation \* While using cffileupload, the url attribute is now
optional and it defaults to cgi.script\\\_name \* Support for selecting
multiple rows in cfgrid \* Support for turning on/off grid column
headers \* New attribute autoExpand in cfgridcolumn \* Supports mask
attribute for HTML grids \* The fileupload control now passes session
information implicitly to the target page if session management is
turned on either in Application.cfc or Application.cfm. \* Added the
following JavaScript Functions: \*
ColdFusion.Autosuggest.getAutosuggestObject \*
ColdFusion.Layout.disableSourceBind \*
ColdFusion.Layout.enableSourceBind \* Coldfusion.fileUpload.setUrl \*
ColdFusion.grid.getSelectedRows \* ColdFusion.Map.show \*
ColdFusion.Map.hide \* ColdFusion.Map.refresh \*
ColdFusion.Grid.getTopToolbar \* ColdFusion.Grid.getBottomToolbar \*
ColdFusion.Grid.showTopToolbar \* ColdFusion.Grid.hideTopToolbar \*
ColdFusion.Grid.showBottomToolbar \* ColdFusion.Grid.hideBottomToolbar
\* ColdFusion.Grid.refreshTopToolbar \*
ColdFusion.Grid.refreshBottomToolbar DEVELOPING COLDFUSION 9
APPLICATIONS 3 What's New Area What's new and changed ORM \* Supports
multiple data sources for ORM in ColdFusion applications \* The
following enhancements: \* New attribute MappedSuperClass added to
cfcomponent/component \* New attributes skipCFCWithError and
automanagesession added to the ormsettings struct in the THIS scope of
Application.cfc \* Attribute missingrowignored in cfproperty now
supports one-to- one relationship \* The function EntityNew takes the
property values as struct in a second argument Amazon S3 Support
ColdFusion customers can now store data in Amazon S3. SpreadSheet
Support for the following: \* New functions SpreadsheetRemoveSheet and
SpreadsheetFormatCellRange \* New attribute excludeHeaderRow in
cfspreadsheet \*
Performanceimprovementsforformattinghugenumberofrowsandcolumnsusingthe
SpreadSheet format functions \* Preformatting of a cell while you use
SpreadSheetformatcell or SpreadSheetformatcellrange \* Vertical
alignment in format struct using the key verticalalignment AIR
integration The following enhancements: \* Support for auto-generating
primary keys \* Support for encrypted database (introduced in AIR 1.5)
\* Cache file used by ActionScript ORM to track the operations on SQLite
database is now in the applicationStoragedirectory instead of
applicationDirectory. You can specify the location of the cahceDirectory
in openSession API on syncmanager \* Self Join relationships for
one-to-one, one-to-many and many-to-many database relationships \*
Supports both Array and ArrayCollection for use in ActionScript Entity
to represent a collection in a database relationship \* ActionScript ORM
logs all the SQL statements that ORM uses to persist entities into the
SQLite database \*
NewAPIskeepClientObjectandkeepAllClientObjectstoensurethattheserver
updates are not retained when ColdFusion server raises conflict \* The
class SessionToken is dynamic and therefore, data can be stored on the
token returned from the ORM APIs \* Supports autocommit mode Flash
Remoting A channel-definition construct has been introduced in
services-config.xml (CF\\\_root/wwroot/WEB-INF/flex/) named
serialize-array-to-arraycollection. DEVELOPING COLDFUSION 9 APPLICATIONS
4 What's New Area What's new and changed BlazeDS 4 and LCDS Support for
the following: \* LCDS 3 and LCDS 3.1 \* BlazeDS 4 \* New methods
allowSend and allowSubscribe in ColdFusion Messaging Gateway CFCs Solr
Apart from overall improvement in the accuracy of indexing, the
following enhancements: \* Displays correct MIME types for all documents
\* Enhanced support for indexing of metadata for binary files \* Support
for the attribute previousCriteria (in the tag cfsearch) \* Both the
tags cfindex and cfsearch support the attribute categoryTree \* Option
to enable/disable term highlighting for entire document Logging The
following enhancements \* ColdFusion generates log files for the
following services: http, ftp, web service, Portlet, Derby, and Feed \*
Enable/Disable logging: A new icon has been added in the Actions column
of the Log Files page (ColdFusion Administrator \\\> Debugging &
Logging) \* Support for automatic logging of scheduled tasks Server
monitoring Enhancements in this release help you use Server Monitoring
effectively in load conditions. ColdFusion Administrator has the
following monitoring options: Enable monitoring, Enable profiling, and
Enable memory tracking. Configurable seed for password Option to specify
a new seed value to encrypt data source passwords encryption OEM
upgrades The following versions are supported: \* Microsoft .NET
Framework 4 \* Ehcache 2.0 \* Hibernate 3.5.2 \* ExtJS 3.1 \* Solr 1.4
\* DataDirect Connect for JDBC 4.1 \* MySQL 5.1.11 Other enhancements \*
Application.cfc lets you specify data source authentication details in
the attribute datasource \* Support for HQL in cfquery \* ·New actions
for cfpdf AIR Proxy \* The ActionScript proxy class for PDF service has
the following new attributes: extracttext and extractimage \* CFID,
CFTOKEN, and jsessionid are marked httpOnly DEVELOPING COLDFUSION 9
APPLICATIONS 5 What's New What's new in ColdFusion 9 ColdFusion 9 offers
new and enhanced features that help you develop and manage applications
effectively. This release provides improved application performance with
more granular control over code, templates, and applications. The
following is a list of top new features for ColdFusion 9. ORM support
ColdFusion Object-relational mapping (ColdFusion ORM) is a powerful
Rapid Application Development (RAD) solution to build data-centric
applications. It provides a bridge between relational databases and
ColdFusion components by allowing you to build applications using only
objects, without writing any SQL code. It uses the open- source
Hibernate library as the underlying engine. ColdFusion ORM provides: \*
Enterprise infrastructure for Rapid Application Development \* In-built
performance optimization that include caching and lazy loading \*
Cleaner and more manageable application code \* Database vendor
independence For more information, see ColdFusion ORM. Flex and AIR
integration Adobe AIR applications Offline capabilities Offline
application support for AIR applications includes data persistence and
synchronization. ColdFusion uses SQLite in the client and ORM on the
server to automatically manage conflict resolution and data
synchronization when the application comes back online. ActionScript
proxies For AIR/Flex clients to access ColdFusion services. The MXML
tags can map to ColdFusion tags on the server. For more information, see
Offline AIR Application Support. Enhanced Flash Remoting Flash Remoting
has been re-engineered in ColdFusion 9 to allow high performance and
faster remoting. This release supports circular reference. For more
information, see Using Flash Remoting Update. Integration with BlazeDS
BlazeDS allows messaging support for ColdFusion. By default, ColdFusion
installs BlazeDS. Language enhancements CFScript Language constructs You
can now use the following basic language constructs: throw, writedump,
writelog, location, and trace. DEVELOPING COLDFUSION 9 APPLICATIONS 6
What's New Script functions This release has introduced the following
new functions implemented as CFCs: query, mail, http, storedproc, pdf,
and ftp. Keywords This release has introduced keywords for abort, exit,
include, param, property, rethrow, and throw. Operations CFScript now
supports import and new operations. In addition, ColdFusion now supports
the following: \* Writing component and interface entirely in CFScript
\* Complete control over function declaration in CFScript \* Java Doc
style comments \* Ternary operator For more information, see Extending
ColdFusion Pages with CFML Scripting. onServerStart ColdFusion now
supports a CFC with onServerStart method that runs only when the server
starts. The function is useful for application-independent tasks, such
as instantiating the applications, configuring logging, or setting up
the scheduler. For more details, see the section onServerStart in
ColdFusion CFML Reference. Other language enhancements Apart from new
cfsrcipt features and onServerStart, this release has the following
language enhancements: \* Nested cftransaction \* UDF name conflict
resolution for CFCs \* Local scope \* Var scope support anywhere in
functions \* Implicit getters and setters for cfproperty in CFCs
Integration with other products and technologies New Ajax controls With
ColdFusion 9, you now have access to a broader set of Ajax controls that
leverage the new Ext JS 3.0 library using CFML tags and attributes. You
can utilize Ajax without knowing its inner workings and writing less
code. The new Ajax controls support: \* Geographical maps \* Media
player \* Multi-file upload \* Enhanced data grid \* Improved Ajax
plumbing \* Enhanced auto-suggest \* Accordion navigation \* Progress
indicator DEVELOPING COLDFUSION 9 APPLICATIONS 7 What's New \* Confirm,
alert, and prompt dialog boxes \* Slider For more information, see Using
Ajax User Interface Components and Features. Integration with SharePoint
The cfsharepoint tag lets you interact with Microsoft Office SharePoint
Servers from a ColdFusion application. You can now use ColdFusion with
Microsoft Windows SharePoint Services 2.0 or 3.0 and Microsoft Office
SharePoint Portal Server 2003 or 2007. You can now: \* Load SharePoint
actions from a ColdFusion application \* Access a ColdFusion application
from SharePoint using custom Web Parts \* Use Single Sign-On to access
and display ColdFusion applications using SharePoint For more
information, see SharePoint integration. Office file interoperability
ColdFusion provides interfaces to work with PDF, Adobe Flash, and Adobe
Connect. ColdFusion now extends the integration support to Office
applications such as Excel and PowerPoint. Using this feature, you can:
\* Create, read, and update MS Excel spreadsheets using the
cfspreadsheet tag \* Generate PDFs from MS Word and MS PowerPoint
automatically using the cfdocument tag \* Generate PowerPoint
presentations dynamically from HTML using the cfpresentation tag \*
Create Connect presentations from MS PowerPoint using the cfpresentation
tag For more information, see Office file interoperabilityOffice file
interoperability. Integration with Apache Solr server Solr is an
open-source enterprise search server based on the Lucene Java search
library. It is a powerful alternative to Verity. Solr provides better
performance while indexing and searching. You can index unlimited number
of documents and access using the cfsearch tag. ColdFusion provides an
easy migration path from Verity. Also, Solr supports Macintosh
environments. For more information, see Solr search support and the
section Solr Server and Collections in Configuring and Administering
ColdFusion. Performance enhancements Granular control over caching
ColdFusion 9 provides better control over caching. The following
features help to improve the performance of your application: \* Caching
page fragments \* Caching in memory. Memory is now the default cache
location. DEVELOPING COLDFUSION 9 APPLICATIONS 8 What's New \* Caching
specific objects. Includes the ability to put, get, and flush cached
objects. \* Setting cache dependencies \* Setting idle timeout \*
Getting metadata about cached objects For more information, see
Optimizing ColdFusion applications. In-memory files In-memory files
allow you to dynamically generate CFM files and execute them directly
from memory. Memory-based virtual file system speeds up the processing
of transient data. In-memory files are not written to disk and are saved
on RAM. In ColdFusion, in-memory files help you simplify the execution
of dynamic code. Though they function in the same manner as disk files,
they are faster. In-memory files are supported across all tags and
functions that take file/directory as input or output. They work in the
same manner as files saved on the disk. For more information, see
Working with in-memory files. Improved Clustering This release supports
serialization of query, array, and datetime types in CFC. Other
performance enhancements \* Improved CFC performance \* Faster Java
method invocation Database enhancements DataDirect This release supports
DataDirect driver version 4.0 SP 1. The feature helps to enhance
database operations by providing the following features: \* Support for
MySQL (Enterprise and Commercial editions), Oracle11g, DB2v9.5, Informix
11, and SQL Server 2008 \* Support for IPv6 \* Option to set a default
query timeout value For more information, see the DataDirect Connect
JDBC Support in the Configuring and Administering ColdFusion. Note: By
default, the datasource property MaxPooledStatements is set to 100 (and
not 1000 as in the previous releases) for the drivers DB2, Informix,
MSSQLServer, Oracle, Sybase, and MySQL(DataDirect). Adobe recommends
that you maintain the default number of max pooled statements to avoid
memory-related issues. datasource is optional attribute The attribute
datasource is an optional attribute for the tags cfquery, cfinsert,
cfupdate, and cfdbinfo. You can specify this in the Application.cfc.
Code Analyzer You can migrate code from ColdFusion 7 or ColdFusion 8 to
ColdFusion 9. For more information, see Using the Code Analyzer.
DEVELOPING COLDFUSION 9 APPLICATIONS 9 What's New Service features
ColdFusion as a service ColdFusion exposes many existing enterprise
services as web services. You can access these services using SOAP and
AMF/Flash remoting. The following are the exposed services: \* cfpdf \*
cfImage \* cfdocument \* cfmail \* cfpop \* cfchart You can secure the
exposed services to prevent access by unknown applications and users.
You can do this by configuring the client IP address range to which
services are accessible. You can also set up user access control for the
services. Other enhancements Server Manager Server Manager is an
AIR-based desktop application that allows you to centrally manage
multiple ColdFusion servers from one location. The application enables
ColdFusion server administrators to monitor and manage multiple servers
and apply the settings from one ColdFusion server to other ColdFusion
servers. Server Manager provides improved system management and
minimizes errors by ensuring a consistent configuration across multiple
servers or clusters. You can use Server Manager to: \* Create data
sources \* Schedule tasks and notify alerts \* Apply hot fixes \* Clear
cache across a cluster of ColdFusion servers \* Compare settings across
servers \* Monitor server health For more information, see Working with
Server Manager. Built-in support for portlet standards This release
supports JSR-168, JSR-286, and WSRP specifications. Now you can easily
build ColdFusion-powered content for leading portal servers. You can
define a ColdFusion component as a portlet. Exposing of all enterprise
applications, including ColdFusion applications, in one integrated
portal has improved application user experience. For more information,
see ColdFusion Portlets. DEVELOPING COLDFUSION 9 APPLICATIONS 10 What's
New PDF functionality The following list includes new features and
improved functionality: \* FDF support in PDF forms \* PDF package \*
Size optimization \* Adding headers and footers to PDF documents using
the cfpdf tag \* Support for RGB/ARGB, cfimage and accessible images in
watermarking a pdf using the cfpdf tag \* Improved quality and
performance of thumbnail generation \* Support for extracting image and
text from PDF. For more information, see Assembling PDF Documents. IMAP
support You can query an IMAP server to retrieve and manage mails within
multiple folders using the cfimap tag. This feature lets you: \*
Retrieve messages and store information in a query object. You can also
download attachments in a temporary ColdFusion folder or a new folder.
\* Delete any unnecessary mail or user-created folders \* Mark multiple
messages as read \* Manage mail folders by creating folders, renaming
them, or moving messages across folders For more information, see the
section cfimap in ColdFusion CFML Reference. JRE specifications This
release includes JRE version JRE 6 Update 14 for all platforms except
Solaris which has the version JRE 6 Update 12.
