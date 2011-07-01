Chapter 8: ColdFusion ORM

Relational databases are the core of most enterprise applications. However, when you map a relational database to
objects, it becomes a challenge. Object relational mapping (ORM) is a programming framework that allows you to
define a mapping between application object model and the relational database.

In an object model, the application objects are not aware of the database structure. Objects have properties and
references to other objects. Databases consist of tables with columns that maybe related to other tables. ORM provides
a bridge between the relational database and the object model.

By using ORM, you can access and update data entirely using the object model of an application. ORM provides
features such as:

* Database vendor independence

* Caching

* Concurrency

* Performance optimization



Introducing ColdFusion ORM

In previous ColdFusion releases, database access was achieved by:

* Managing relational data using tags such as cfquery, cfinsert, and cfupdate, which handle SQL statements.

* Managing objects using ColdFusion components (CFCs), and object lifecycle using the application itself

* Writing SQL queries for each CFC, even for basic CRUD (Create, Retrieve, Update, and Delete) operations.

The complexity of managing these tasks increases as your application grows.

ColdFusion ORM automates most of these tasks, which:

* Makes application code cleaner and more manageable

* Enhances your productivity and lets you develop database applications faster

* Creates applications that can run faster because of built-in ORM optimizations

* Minimizes the amount of code you write

Apart from providing a framework for mapping the object model with the relational database, ColdFusionORM
provides data query and retrieval facilities.

For more information, see www.hibernate.org.


ColdFusion ORM example
ColdFusion ORM manages persistence through objects, which are also called entities in the ORM context. In
ColdFusion, persistence is managed through CFCs and their properties. Each persistent CFC in ColdFusion
application maps to a table in the database. Each property in the persistent CFC maps to a column in the table.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       534
ColdFusion ORM




The following example explains these concepts by building a simple application, which would enable you to jumpstart
with ColdFusion ORM. The example uses the cfartgallery data source that is shipped as part of ColdFusion 9
documentation option in the installer. The cfartgallery data source has Artists and Art tables. Artists has a one-to-
many relationship with the Art table.

Step 1:

Specify the ORM settings in the Application.cfc file.

The minimum required settings are mentioned in the following sample code snippet:

Application.cfc


<cfset this.name = "ArtGalleryApp">
<cfset this.ormenabled = "true">
<cfset this.datasource = "cfartgallery">

Apart from these, there are other settings that you can use to configure ORM. For details, see "ORM settings" on
page 538.

Important: Define these setting only in Application.cfc and not in Application.cfm.

Step 2:

Map the ARTISTS.cfc to the database table.

1 Create the ARTISTS.cfc.

2 Flag it as a persistent CFC and map it to the ARTISTS table.

   To make the ARTISTS.cfc persistent, the persistent attribute should be set to true in the cfcomponent tag. The
   table attribute should be set to the table name. If table attribute is not specified, then the CFC name is taken as the
   table name.

   Each CFC can be given an entity name. Entity name is the name used by the ORM related functions to work with
   the persistent CFC. It can be specified by using the entityname attribute in cfcomponent. If entityname is not
   specified, then the CFC name is taken as the entityname.

3 Now, create properties in ARTISTS.cfc and map them to the columns in the table. One property should be created
   for each column in the table. To map the property to the column, the column attribute should be set to the
   corresponding column name. If the column attribute is not specified, then the name of the property is taken as the
   column name.

For details on setting the ORM-specific attributes, see "Define ORM mapping" on page 542.

The ARTISTS.cfc is defined as follows:

<cfcomponent persistent="true">
    <cfproperty name="id" column = "ARTISTID" generator="increment">
    <cfproperty name="FIRSTNAME">
    <cfproperty name="LASTNAME">
    <cfproperty name="ADDRESS">
    <cfproperty name="CITY">
    <cfproperty name="STATE">
    <cfproperty name="POSTALCODE">
    <cfproperty name="EMAIL">
    <cfproperty name="PHONE">
    <cfproperty name="FAX">
    <cfproperty name="thepassword">
</cfcomponent>




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 535
ColdFusion ORM




Step 3:

Perform CRUD operations.

To retrieve data from the ARTISTS table, use EntityLoad():

     ARTISTS = EntityLoad("ARTISTS")

All the records from the ARTISTS table are retrieved as an object array.

To add a new artist, create a new artist object and call EntitySave() for this object.

<cfscript>
try {
     newArtistObj = EntityNew("artists");
     newArtistObj.setfirstname("John");
     newArtistObj.setlastname("Smith");
     newArtistObj.setaddress("5 Newport lane");
     newArtistObj.setcity("San Francisco");
     newArtistObj.setstate("CA");
     newArtistObj.setPostalCode("90012");
     newArtistObj.setphone("612-832-2343");
     newArtistObj.setfax("612-832-2344");
     newArtistObj.setemail("jsmith@company.com");
     newArtistObj.setThePassword("jsmith");
     EntitySave(newartistobj);
     ormflush();
} catch(Exception ex) {
     WriteOutput("<p>#ex.message#</p>");
}
</cfscript>

To update an existing record, load that object and make changes to it. ColdFusion automatically detects that the row
for this object needs to be updated and it will get updated when ORMFlush() is called.

Note: ORMFlush() is called at the end of the request by default.

In the following code, the newArtistObj is already managed by ORM, so it does not need to be loaded again.

newArtistObj.setphone("612-832-1111");
ormflush();

To delete a record, EntityDelete() is used.

EntityDelete(newArtistObj);
ormflush();

Step 4:

Define Relationships

First define the mapping for the ART table to define a relationship between artwork and artists.

The ART.cfc is defined as follows:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   536
ColdFusion ORM




<cfcomponent persistent="true">
     <cfproperty name="artid" generator="increment">
     <cfproperty name="artname">
     <cfproperty name="price">
     <cfproperty name="largeimage">
     <cfproperty name="mediaid">
     <cfproperty name="issold">
</cfcomponent>

In cfartgallery, the table ARTISTS has a one-to-many relationship with ART table, which are joined using the foreign
key column ARTISTID. This means that each artist has created multiple artwork pieces and many artworks are created
by one artist. To represent this in the object model, each ARTISTS object would contain an array of ART objects. Each
ART object will contain a reference to its ARTISTS object. This is an example of a bidirectional relationship.

To achieve this, you need to add an extra property to the ARTISTS.cfc object that contains the array of ART objects
for that ARTIST.

<cfproperty name="art" type="array" fieldtype="one-to-many" cfc="Art" fkcolumn="ARTISTID">

fieldtype="one-to-many" specifies the type of relation.

CFC="Art" is used to convey that the relationship is with "ART" cfc.

fkcolumn="artistid" specifies the foreign key.

ART forms a many-to-one relationship with ARTISTS table because each piece of artwork is created by an artist and
many other pieces of artwork are created by the same artist. To define this relationship, add a property in ART.cfc to
define the relationship with ARTISTS.cfc.

<cfproperty name="artists" fieldtype="many-to-one" fkcolumn="artistid" cfc="Artists"
lazy="true">

fieldtype="many-to-one" specifies the type of relation.

CFC="ARTISTS" is used to convey that the relationship is with "ARTISTS" cfc.

fkcolumn="ARTISTID" specifies the foreign key.

Step 5:

Retrieve records in relationship

<cfscript>
     artist = EntityLoad("Artists", 1, true);
     art = artist.getArts();
     WriteOutput("<b>" & artist.getid() & " " & artist.getfirstname() & " " &
     artist.getlastname() & "</b> has " & ArrayLen(art) & " art:<br>");
     if (ArrayLen(art) > 0)
     {
          for(j = 1; j <= ArrayLen(art); j ++)
          {
                art = art[j];
                WriteOutput(art.getartname() & "<br>");
          }
     }
</cfscript>




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        537
ColdFusion ORM




Architecture

In ColdFusion ORM, you need to define an object mapping to create persistent objects. The object mapping includes
details such as:

* The table name for the object's class

* The column name that corresponds to each field in the object

* The join conditions for related objects

ColdFusion allows you to specify the mapping in CFCs. Such CFCs are called as persistent CFCs. Each persistent CFC
usually maps to a table in the database. Each property in the CFC usually maps to a column in the table. Additional
properties may be used to define relationships and other mapping details.

When ColdFusion creates the Hibernate configuration for the application, these persistent CFCs are used to
automatically generate Hibernate mapping files, which have the extension ".hbmxml". For example, if ARTISTS.cfc is
a persistent CFC, ColdFusion would automatically generate Artists.hbmxml. Hibernate mapping files contain the
mapping information in XML format that Hibernate defines, to work with ColdFusion ORM. These Hibernate
mapping files can be created manually.

For more information about creating Hibernate mappings manually, see "Advanced mapping" on page 566.

To use ColdFusion ORM, ColdFusion application must have ormenabled set to true in the THIS scope of
Application.cfc. To define a persistent CFC, set persistent="true" in cfcomponent tag. An array of attributes are
available in cfcomponent and cfproperty to specify mapping information.

For details, see "Define ORM mapping" on page 542.

When the application starts, ColdFusion first loads the Hibernate configuration file if it is specified in the application.
The Hibernate configuration file contains various configuration parameters like including, dialect, cache settings, and
mapping files that are required for the application. If a configuration file is not specified, ColdFusion ORM creates the
Hibernate configuration using the default settings.

For details about these configuration parameters, see www.hibernate.org/hib_docs/reference/en/html/session-
configuration.html.

After loading the Hibernate configuration, all the mapping files (*.hbmxml) in the application folder and its mapped
folders are loaded and added to the configuration.

ColdFusion then searches for persistent CFCs in the application folder and its mapped folders. If the hibernate
mapping file is not present for any persistent CFC, ColdFusion generates it. If mapping information, such as primary
key, foreign key, and column data type is missing in the persistent CFCs, ColdFusion automatically inspects the
database and identifies the mapping.

ColdFusion then checks if DDL needs to be generated. This can be configured using the dbcreate option in the ORM
settings. Based on the configuration option specified in dbcreate, tables are created or updated.The Hibernate
SessionFactory is then built and made available to the application as long as the application is running. The
SessionFactory is used to create Hibernate sessions that manage the persistent object lifecycle.

In ColdFusion, a Hibernate session starts when the first CRUD method is called and ends when the request ends or
when the ORMCloseSession() method is called.

To improve performance, Hibernate batches all the Create/Update/Delete operations in the session and runs them
when the session is flushed or only when necessary. Session Flush happens when the request ends or when the
ORMFlush() method is called.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  538
ColdFusion ORM




For transactions, a new session is always created at the start of a transaction and ends at the end of a transaction. Any
previous open sessions are flushed and closed at the start of the transaction.

The Hibernate configuration is created and loaded only when the application starts. Therefore, any modifications to
the mapping in the persistent CFCs or in the Hibernate mapping files are not loaded automatically. To load these
modifications, you can either restart the application or call ORMReload().

To restart the application, you can stop the application using ApplicationStop() and the next request to any page
in this application automatically starts it.



Configure ORM

The configuration for ORM is done in Application.cfc which makes this configuration application specific. For a
ColdFusion application to use ORM, the following are the mandatory settings that need to be configured:

1 Enable ORM for the application. To do this, set the ormenabled property to true in the THIS scope of
   application.cfc

2 Provide the data source name by either setting data source property to true in the THIS scope of application or by
   defining it in ORM configuration for the application.

   Note that the data source should be configured on the server.

The ORM configuration is specified using a struct called ormsettings, which is defined in the THIS scope of
Application.cfc. The following table describes the settings for ORM that can be defined in Application.cfc.


 Property Name                           Description

 ormenabled                              Specifies whether ORM should be used for the ColdFusion application.Set the value to true to
                                         use ORM. The default is false.

 datasource                              Defines the data source that should be used by ORM.

 ormsettings                             The struct that defines all the ORM settings. For details, see "ORM settings" on page 538



ORM settings
The following settings can be set in the ormsettings struct that ColdFusion uses to configure ORM. All these settings
are optional. If you specify the value of any ORM setting to true or yes, then the setting is enabled, otherwise it is
disabled.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          539
ColdFusion ORM




Property Name              Default         Description

autogenmap                  true           Specifies whether ColdFusion should automatically generate mapping for the
                                           persistent CFCs. If autogenmap=false, mapping should be provided in the
                                           form of .HBMXML files.

automanageSession           true           Lets you specify if ColdFusion must manage Hibernate session automatically.

Added in ColdFusion 9.0.1                  * If enabled: ColdFusion manages the session completely. That is, it decides
                                             when to flush the session, when to clear the session, and when to close the
                                             session.

                                           * Ifdisabled:Theapplicationisresponsibleformanagingflushing,clearing,or
                                             closing of the session. The only exception is (in the case of transaction),
                                             when the transaction commits, the application flushes the session.

                                           ColdFusion closes the ORM session at the end of request irrespective of this flag
                                           being enabled or disabled.

cacheconfig                                Specifies the location of the configuration file that should be used by the
                                           secondary cache provider.This setting is used only when
                                           secondarycacheenabled=true.

                                           See "Secondary level cache" on page 590 for details.

cacheprovider              ehcache         Specifies the cache provider that should be used by ORM as secondary cache.
                                           The values can be:

                                           * Ehcache

                                           * JBossCache

                                           * Hashtable

                                           * SwarmCache

                                           * OSCache

                                           Fully qualified name of the class for any other cache provider.

                                           This setting is used only when secondarycacheenabled=true.

                                           See "Secondary level cache" on page 590 for details.

catalog                                    Specifies the default Catalog that should be used by ORM.

cfclocation                                Specifies the directory (or array of directories) that should be used by
                                           ColdFusion to search for persistent CFCs to generate the mapping. If
                                           cfclocationisset,ColdFusionlooksatonlythepathsspecifiedinit.Ifitisnot
                                           set, ColdFusion looks at the application directory, its sub-directories, and its
                                           mapped directories to search for persistent CFCs.

datasource                                 Specifies the data source that should be used by ORM. If it is not specified here,
                                           then the data source specified for the application is picked up.

dbcreate                    none           ColdFusion ORM can automatically create the tables for your application in the
                                           database when ORM is initialized for the application. This can be enabled by
                                           using dbcreate in ormsettings. dbCreate takes the following values:

                                           * update: Setting this value creates the table if it does not exist or update the
                                             table if it exists.

                                           * dropcreate:Settingthisvaluedropsthetableifitexistsandthencreatesit.

                                           * none (default): Setting this value does not change anything in the database
                                             schema.




                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          540
ColdFusion ORM




Property Name             Default          Description

dialect                                    Specifies the dialect.

                                           ColdFusion supports the following dialects:

                                           * DB2

                                           * DB2AS400

                                           * DB2OS390

                                           * Derby

                                           * PostgreSQL

                                           * MySQL

                                           * MySQLwithInnoDB

                                           * MySQLwithMyISAM

                                           * Oracle8i

                                           * Oracle9i

                                           * Oracle10g

                                           * Sybase

                                           * SybaseAnywhere

                                           * MicrosoftSQLServer

                                           * Informix

                                           Apart from these dialects, you can specify custom dialects by using the fully
                                           qualified class name.

                                           Note: For Microsoft Access, dialect cannot be detected
                                           automatically. Use Microsoft SQL Server as the dialect for it.

eventHandling             false            Specifies whether ORM Event callbacks should be given. See Event Handling in
                                           CFC for details.

flushatrequestend         true             Specifies whether ormflush should be called automatically at request end. If
                                           flushatrequestendisfalse,ormflushisnotcalledautomaticallyatrequest
                                           end.

                                           See ORM session management.

logSQL                    false            Specifies whether the SQL queries that are executed by ORM will be logged. If
                                           LogSQL=true, the SQL queries are logged to the console.


ormconfig                                  The Hibernate configuration file.

                                           This file contains various configuration parameters like, dialect, cache settings,
                                           and mapping files that are required for the application. For more details, see
                                           www.hibernate.org/hib_docs/reference/en/html/session-configuration.html.

                                           The settings defined in the ormsettings override the settings defined in the
                                           Hibernate Configuration XML file.The connection information in the Hibernate
                                           Configuration XML file is however ignored because ColdFusion uses its own
                                           connection pool.

                                           You will need to use this only when you need to use a hibernate setting that is
                                           not available using ormsetting.




                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  541
ColdFusion ORM




Property Name               Default                 Description

savemapping                 false                   Specifies whether the generated Hibernate mapping file has to be saved to
                                                    disc. If you set the value to true, the Hibernate mapping XML file is saved with
                                                    the filename "CFC name".hbmxml in the same directory as the CFC.

                                                    If any value of savemapping is specified in CFC, it will override the value
                                                    specified in the ormsetting.

schema                                              Specifies the default Schema that should be used by ORM.

secondarycacheenabled       false                   Specifies whether secondary caching should be enabled. See Use secondary
                                                    cache for details.

skipCFCWithError            false                   Lets you specify if ColdFusion must skip the CFCs that have errors. If set to
                                                    true, ColdFusion ignores the CFCs that have errors.
Added in ColdFusion 9.0.1

sqlscript                                           Path to the SQL script file that gets executed after ORM is initialized. This
                                                    applies if dbcreate is set to dropcreate. This must be the absolute file path
                                                    or the path relative to the application.The SQL script file lets you populate the
                                                    tables before the application is accessed.

useDBForMapping             true                    Specifies whether the database has to be inspected to identify the missing
                                                    information required to generate the Hibernate mapping. The database is
                                                    inspected to get the column data type, primary key and foreign key
                                                    information.


Sample Application.cfc

<cfset this.name = "ArtGallery">
    <cfset this.ormenabled = "true">
    <cfset this.ormsettings={datasource="cfartgallery", logsql="true"}>



Logging
Monitoring SQL queries that get generated and executed by ORM is critical for troubleshooting and performance
optimization.

You can monitor and log the queries by:

* Defining logsql in ormsettings: This is a simple way to quickly enable SQL logging. The flag should be enabled
   in application.cfc:

   <cfset this.ormsettings.logsql = "true">

   This logs all the SQL queries that are generated by Hibernate to the console and server's output log file.

* Using log4J.properties: Hibernate uses log4j for its logging and you can completely control its logging
   including SQL by modifying the log4j.properties, which is present under <CF_HOME>/lib directory.

   Following is a sample snippet from the log4j.properties file:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  542
ColdFusion ORM




   ###--------------- Hibernate Log Settings ------
   ### Set Hibernate log
   log4j.logger.org.hibernate=ERROR, HIBERNATECONSOLE


   ### log just the SQL
   #log4j.logger.org.hibernate.SQL=DEBUG, HIBERNATECONSOLE
   #log4j.additivity.org.hibernate.SQL=false
   ### Also log the parameter binding to the prepared statements.
   #log4j.logger.org.hibernate.type=DEBUG
   ### log schema export/update ###
   log4j.logger.org.hibernate.tool.hbm2ddl=DEBUG, HIBERNATECONSOLE
   ### log cache activity ###
   log4j.logger.org.hibernate.cache=ERROR, HIBERNATECONSOLE
   # HibernateConsole is set to be a ColsoleAppender for Hibernate message                                 using a
   PatternLayout.
   log4j.appender.HIBERNATECONSOLE=org.apache.log4j.ConsoleAppender
   log4j.appender.HIBERNATECONSOLE.layout=org.apache.log4j.PatternLayout
   log4j.appender.HIBERNATECONSOLE.layout.ConversionPattern=%d{MM/dd HH:mm:ss} [%t] HIBERNATE
   %-5p - %m%n%n
   #---------------------------------------------

   These settings control the SQLs that are generated for entity operations, how the data is bound to the statement
   while executing, what SQLs are generated for DDL, and what operations are performed on the secondary cache. All
   the logs get logged to console using HIBERNATECONSOLE which is actually a console appender. It can easily be
   changed to a FileAppender, which will then be logged to a log file. The configuration controls the logging for the
   following:

   * SQL generated for entity operations

   * Parameter binding for the prepared statements

   * SQL generated for DDL

   * Secondary cache operations

   With the default settings, all the logs get logged to console. You can also direct the logging to a log file using the
   FileAppender provided by log4j.

   See log4j for more details on Appenders.


    Log4j Properties                                   Description

    log4j.logger.org.hibernate.SQL                     This controls when and how the SQL will be logged. DEBUG says all the SQL will
                                                       be logged

    log4j.logger.org.hibernate.type                    This logs the parameter binding to the prepared statement.

    log4j.logger.org.hibernate.tool.hbm2ddl            Logs SQL for DDL i.e schema export.

    log4j.logger.org.hibernate.cache                   Logs secondary cache information.




Define ORM mapping

The ORM mapping can be defined either in the CFC or in a separate Hibernate mapping file (.hbmxml). See
"Advanced mapping" on page 566 for details on Hibernate mapping file. The ORM mapping is defined in the CFC
using ORM-specific attributes on cfcomponent and cfproperty tag.

Following example shows a CFC (ARTIST.cfc) with mapping information:



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          543
ColdFusion ORM




<cfcomponent persistent="true" entityname="Artist" table="Artists">
     <cfproperty name="id" column="ARTISTID" generator="increment">
     <cfproperty name="firstname">
     <cfproperty name="lastname">
     <cfproperty name="address">
     <cfproperty name="city">
     <cfproperty name="state">
     <cfproperty name="postalcode">
     <cfproperty name="email">
     <cfproperty name="phone">
     <cfproperty name="fax">
     <cfproperty name="thepassword">
</cfcomponent>



Map a ColdFusion component
The tag cfcomponent defines a persistent CFC by setting persistent="true". It allows you to specify various other
mappings for a persistent CFC.

If your persistent CFC has init method, ensure that either it has no arguments or all arguments are optional.


Syntax
<cfcomponent
     accessors= "yes|no"
     persistent="true|[false]"
     entityname="entity_name"
     table="table_name"
     schema="schema"
     catalog="catalog"
     dynamicInsert="true|[false]"
     dynamicupdate="true|[false]"
     readonly="true|[false]"
     selectbeforeupdate="true|[false]"
     discriminatorvalue="discriminator_value"
     discriminatorcolumn="discriminator_column"
     joincolumn="join_column"
     cacheuse="read-only|read-write|nonstrict-read-write|transactional|[none]"
     cachename="cache_name"
     batchsize="batchsize"
     optimisticLock="none|[version]|dirty|all"
     lazy="[true]|false"
     savemapping="true|false">


Attributes

Attribute              Req/Opt              Default                     Description

accessors              true                                             If set to false, ColdFusion ORM does not generate the
                                                                        implicit getters and setters.

batchsize              Optional             1                           An integer value that specifies the number of records
                                                                        to be retrieved at a single instance.

                                                                        For details, see Lazy Loading.

cachename              optional                                         Use this value to specify the name of the secondary
                                                                        cache. See Caching for details.




                                           

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               544
ColdFusion ORM




Attribute           Req/Opt            Default             Description

cacheuse            optional            none               Use this value to specify the caching strategy to be
                                                           used for caching this component's data in the
                                                           secondary cache. See Caching for details.

catalog             Optional                               Used to specify the database catalog name.

discriminatorcolumn optional                               Use this attribute to define the discriminator column
                                                           to be used in inheritance mapping. See Inheritance
                                                           mapping for details.

discriminatorvalue  optional                               Use this attribute to define the discriminator value to
                                                           be used in inheritance mapping. See Inheritance
                                                           mapping for details.

dynamicInsert       Optional            false              Whether INSERT SQL is to be generated at runtime:

                                                           * true

                                                           * false

                                                           Only those columns whose values are not null are
                                                           included in the SQL.

dynamicupdate       Optional            false              Whether UPDATE SQL is to be generated at runtime:

                                                           * true

                                                           * false

                                                            Only those columns that have changed values are
                                                           included in the SQL.

entityname          Optional            Name of the CFC    Specifies the name of the entity. Entity name is the
                                                           name used by the ORM related functions to work
                                                           with the persistent CFC. If entityname is not
                                                           specified, then the CFC name is taken as the
                                                           entityname. The entityname must be unique in
                                                           the application.

joincolumn          optional                               Use this attribute to define the join column for
                                                           inheritance mapping. See Inheritance mapping for
                                                           details.

lazy                Optional            true               Whether loading is to be done lazily:

                                                           * true

                                                           * false

                                                           For details, see Lazy Loading.

optimisticLock      Optional            version            Determines the locking strategy.

                                                           It can be any one of the following four values:

                                                           * none

                                                           * version

                                                           * dirty

                                                           * all

                                                           For details, see Optimistic locking.




                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                             545
ColdFusion ORM




Attribute               Req/Opt               Default                    Description

persistent              Required              false                      Whether the CFC is a persistent CFC:

                                                                         * true

                                                                         * false

readonly                Optional              false                      Whether the table is read-only:

                                                                         * true

                                                                         * false

savemapping             Optional                                         Saves the generated Hibernate mapping file to the
                                                                         disk.If you have set savemapping for the
                                                                         application, then the value you set here overrides it.

schema                  Optional                                         Used to specify the schema name.

selectbeforeupdate      Optional              false                      Whether Hibernate should never perform an SQL
                                                                         UPDATE unless it is certain that an object is actually
                                                                         modified:

                                                                         * true

                                                                         * false

                                                                         In cases when a transient object is associated with a
                                                                         new session using update(), Hibernate performs an
                                                                         extra SQL SELECT to determine if an UPDATE is
                                                                         actually required.

table                   Optional              Name of the CFC            Specifies the name of the database table to which the
                                                                         CFC maps.



Map the properties
The cfproperty tag is used to define:

* Primary or composite key

* Columns

* Relationships

* Versioning

The following table lists the common attributes that are used to define these mappings:




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  546
ColdFusion ORM




Attribute        Req/Optional           Default                  Description

default          optional                                        This sets the default value on the property when the object is
                                                                 created.

fieldtype        optional               column                   This attribute is used to specify the type of the property. Use this
                                                                 attribute to specify the following:

                                                                 * Primary key: Specify fieldtype       = "id" to map a property to
                                                                    the primary key in the table. For details, see Primary key.

                                                                 * Column: Specify the fieldtype        = "column" to map a
                                                                    property to a column in the table. For details, see Column.

                                                                 * Relationship: Specify the fieldtype      =
                                                                    "relationship_type". The relationship_type can be one-
                                                                    to-one,one-to-many,many-to-one,ormany-to-many.For
                                                                    details, see Define Relationships.

                                                                 * Version: Specify the fieldtype="version" to indicate that
                                                                    the column contains versioned data. For details, see Version.

                                                                 * Timestamp:Specifythefieldtype="timestamp"toindicate
                                                                    the column contains data with timestamp. For details, see Time
                                                                    stamp.

                                                                 * Collection: Specify the fieldtype="collection" to define
                                                                    the collection mapping. For details, see Collection Mapping

name             required                                        Specifies the name of the property.

type             optional                                        Specifies the ColdFusion data type for the property.

persistent       optional               true                     Specifies whether this property should be persisted. If
                                                                 persistent="false" all the ORM related attributes are ignored.

                                                                 Set this attribute to false if you do not want this property to be
                                                                 persisted by ORM. For example:

                                                                 <cfcomponent persistent=true>
                                                                        <cfproperty name="a">
                                                                        <cfproperty name="b" persistent="true">
                                                                          <cfproperty name="c" persistent="false">
                                                                 </cfcomponent>

                                                                 When an object of this CFC is persisted, the properties a and b
                                                                 would be persisted in the database but the property c would not
                                                                 be persisted.

remotingFetc     optional               true                     If remotingFetch is false, then the value of that attribute is not
h                                                                sent over flash remoting. The attribute is true by default for all
                                                                 properties. However, for ORM CFCs where persistent = true, the
                                                                 value of the remotingFetch attribute is set to false, by default,
                                                                 for one-to-one, one-to-many, many-to-one, or many-to-many
                                                                 relationships.



Primary key


Simple primary key
In a relational database, a primary key is defined as a key that uniquely identifies a row in a table. Typically, a table has
one primary key that represents a single column of information in the table.

To indicate that a cfproperty maps to a primary key in the table, set the attribute fieldtype="id".




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                            547
ColdFusion ORM




Syntax
<cfproperty
     name="property_name"
     fieldType="id"
     ormtype="type"
     column="column_name"
     generator="increment|identity
     |sequence|sequence-identity|seqhilo
     |uuid|guid|native|[assigned]|select|foreign"
     params="{key1=val1,key2=val2...}"
     sqltype="sql_type"
     length="column_length"
     unsavedvalue="instantiated_instance">


Example
An example to define an assigned primary key:

<cfproperty name="artistid" fieldtype="id" column="ARTISTID" generator="assigned">

An example to define a auto-generated primary key using increment generator:

<cfproperty name="artid" fieldtype="id" column="ARTID" generator="increment">

An example to define a auto-generated primary key using a generator, which requires additional parameters:

<cfproperty name="id" fieldtype="id" column="ID" generator="sequence"
params="{sequence='id_sequence'}>


Attributes

Attribute             Req/Opt               Default                Description

column                Optional              The value of the name  Used to specify the primary key column name.
                                            attribute

fieldType             Optional                                     Should be "id" for primary key. If fieldtype is not
                                                                   specified and the useDBForMapping=true, then the
                                                                   fieldtype is determined by inspecting the database.


generator             Optional              assigned               Algorithm used to generate unique identifiers for
                                                                   instances of the persistent CFC. See Generators for
                                                                   details.

length                optional                                     Use this attribute to specify the length of the column. This
                                                                   attribute is used for table creation only.

name                  Required                                     Name of the property

ormtype               Optional              String                 Used to specify the data type of the primary key. If data
                                                                   type is not set and the ORM setting
                                                                   useDBForMapping=true, then the ormtype is
                                                                   determined by inspecting the database.

                                                                   The different data types that are supported by
                                                                   ColdFusion are specified in the ORM data types.

params                                                             Additional parameters required by the various
                                                                   generators to generate the ID.

                                                                   The value for the params attribute should follow the CF
                                                                   Struct syntax. For example: params =
                                                                   {key1='value1', key2='value2'}

                                                                   See Generators for details.




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                              548
ColdFusion ORM




Attribute                 Req/Opt              Default                 Description

selectkey                 optional                                     The column name that is used to retrieve the primary key
                                                                       generated by a database trigger.

sqltype                   optional                                     Use this attribute to specify the DB-specific SQLType that
                                                                       should be used for the column. This attribute is used for
                                                                       table creation only.

                                                                       If this attribute is not specified, ColdFusion will
                                                                       automatically decide the SQLType based on the
                                                                       ormtype specified for this property.


unSavedValue              optional                                     An identifier property value that indicates that an
                                                                       instance is newly instantiated and not saved/loaded in
                                                                       the database



Composite Key
If the primary key consists of more than one column, it is called as a composite key. A composite key can be specified
by using fieldtype='id' on all the properties that form the primary key.

Example

If in a table, the columns Order_id and Product_id form a composite key, then, they should be defined as:

<cfproperty name="Order_Id" fieldtype="id" column="Order_Id">
<cfproperty name="Product_Id" fieldtype="id" column="Product_Id">

For a composite key, the generator should always be "assigned".


Generators
Generators are algorithms that are used to generate unique identifiers for instances of the persistent CFC. Generators
can have any one of the following values:

* increment: This algorithm generates identifiers of type long, short, or int by incrementing a counter maintained
    by ORM. This is commonly used when auto-generation for the primary key is not enabled in the table and you want
    ORM to generate the primary key. This should be used when a single instance of ColdFusion is the only process to
    insert data into the table.

* identity: For databases such as DB2, MySQL, Sybase, and MS SQL, which support identity columns, you can use
    identity key generation. The key is automatically generated by the database and the object is populated with the
    generated key. This strategy requires ORM to execute two SQL queries to insert a new object.

* sequence: For databases such as DB2, Oracle, PostgreSQL, Interbase, McKoi, and SAP, which support sequences,
    you can use sequence style key generation. The key is automatically generated by the database and the object is
    populated with the generated key. This strategy requires ORM to execute two SQL queries to insert a new object.
    This generator includes the sequence parameter, which needs to be specified in the params attribute.

    For example:

    <cfproperty name="id" fieldtype="id" generator="sequence"
    params="{sequence='id_sequence'}">

* native: This algorithm is commonly used to automatically generate primary keys. This generator picks identity or
    sequence depending upon the capabilities of the underlying database.

* assigned: This algorithm is used when the application needs to assign its own identifier to the object. It is the
    default generator used by ColdFusion.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   549
ColdFusion ORM




* foreign: This is used with a <one-to-one> primary key association. In this case, the primary key is the same as
   the primary key of the associated object. This generator would need the property parameter, which needs to be
   specified in the params attribute. The value of the param property should be the name of the relationship property.

   See One-to-one relationships for details.

* seqhilo: See www.hibernate.org/5.html.

* uuid: See www.hibernate.org/5.html.

* guid: See www.hibernate.org/5.html.

* select: See www.hibernate.org/5.html.

* sequence-identity: See www.hibernate.org/5.html.


Column
To indicate that a cfproperty maps to a column in the table, specify fieldtype="column". If the fieldtype is not
specified for cfproperty, it is mapped as a column property.


Syntax
<cfproperty
    name="Property name"
    fieldtype="column"
    column="column_name"
    persistent="true|false"
    formula="SQL expression"
    ormtype="ormtype"
    update="[true]|false"
    insert="[true]|false"
    optimisticLock="[true]|false"
    generated="true|[false]"
    length="column_length"
    precision="precision"
    scale="scale"
    index="index_name"
    unique = "true|[false]"
    uniquekey="uniquekey_name"
    notnull="true|[false]"
    dbdefault="default_col_value"
    sqltype="sql_type">


Example
To specify a simple property:

<cfproperty name="FIRSTNAME"/>

To specify a property which has a different name than that of the column name:

<cfproperty name="LNAME" column="LASTNAME"/>

To specify a property which should be read-only:

<cfproperty name="password" column="THEPASSWORD" insert="false" update="false">




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               550
ColdFusion ORM




Attributes

Attribute          Req/Opt             Default               Description

column             optional            Name of the property  Name of the column.

dbdefault                                                    This sets the default value of a column in the table when schema is
                                                             exported.

fieldType          optional            column                Should be "column" for column property.

formula            optional                                  SQL expression that defines the value of the property. If you specify
                                                             a formula, the column value is ignored.

                                                             See Computed property.

generated          optional            never                 Specifies that this property value is actually generated by the
                                                             database {never|always|insert}

                                                             *  never: Specifies that the value for this property is never
                                                                generated by database

                                                             *  always: Specifies that the value for this property is always
                                                                generated by database

                                                             *  insert: Specifies that the value for this property is generated at
                                                                the time of insert but is not regenerated at updates.

insert             optional            true                  Specifies whether the column should be included in SQL UPDATE
                                                             and/or INSERT statements:

                                                             {true/false}

                                                             Set update=false and insert=false if the column needs to be
                                                             read-only.

name               Required                                  Name of the property. If this attribute is not specified, the name of
                                                             the property is used as the column name.

optimisticlock     optional            true                  Whether updates to this property require acquisition of the
                                                             optimistic lock on the table row:

                                                             {true/false}

ormtype            optional            string                Specifies the data type.

                                                             If specified, then

                                                             * If ORM setting useDBForMapping is set to true and the table
                                                                exists, then ormtype is obtained by inspecting the table

                                                             * (Otherewise) Type, if specified, is used as ormtype

update             optional            true                  Specifies whether the column should be included in SQL update
                                                             statement:

                                                             {true/false}

                                                             Set update=false and insert=false if the column needs to be
                                                             read-only.



DDL-only attributes
The following attributes are used only when DDL generation is required and not used for runtime.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   551
ColdFusion ORM




 Attribute           Req/Opt                   Default         Description

 dbdefault           optional                                  Specifies the default value of the column in the table.

 index               optional                                  Specifies the name of an index that is created using the mapped
                                                               column.

 length              optional                                  Specifies the length value.

 notnull             optional                  false           A Boolean value that specifies whether a notnull constraint should
                                                               be added for this column.

 precision           optional                                  Specifies the precision value.

 scale               optional                                  Specifies the scale value.

 sqltype             optional                                  This allows user to override the default mapping of ormtype to SQL
                                                               datatype. sqltype is used as the DB specific SQL type for a column
                                                               when creating the table. If this attribute is not specified, ColdFusion
                                                               will automatically decide the sqltype based on the ormtype
                                                               specified for this property.

                                                               For example:

                                                               <cfProperty name="active" ormtype="char"
                                                               sqltype="bit">
                                                               <cfProperty name="balance" ormtype="float"
                                                               sqltype="decimal(13,3)">


 unique              optional                                  Specifies if there should be a unique constraint on the column.

 uniquekey           optional                                  Groups columns in a single unique key constraint.



Computed property
Computed property is a property whose value does not come from a column but is computed using a SQL query. Use
formula attribute to specify the SQL to be used to retrieve the value for this property.

<cfcomponent persistent="true" table="ARTISTS" schema="APP">
      <cfproperty name="ID" column="ARTISTID" fieldtype="id"/>
      <cfproperty name="FIRSTNAME"/>
      <cfproperty name="LASTNAME"/>
      <cfproperty name="NumberOfArts" formula="select count(*) from Art art where
                               art.ArtistID=ArtistID"/>
</cfcomponent>


Versioning
Versioning is a technique that allows you to implement concurrency control fora component. You can specify either
version or timestamp property for a component.

For details, see Optimistic locking.

Note: A component can have only one versioning property, either timestamp or version. If you specify multiple versioning
properties, such as two timestamps, or two versions, or a timestamp and a version, an error is thrown.


Version
Use the version attribute to indicate that the column contains versioned data. The version attribute is useful for long
transactions.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          552
ColdFusion ORM




Syntax
<cfproperty
name="fieldname"
fieldtype="version"
column="column name"
ormtype="type"
generated="true|[false]"
insert="[true]|false">


Example
To create a simple version property:

<cfproperty name="version" fieldtype="version">


Attribute

Attribute            Req/Opt                 Default                   Description

column               Optional                                          The name of the column that contains versioned data

fieldtype            Required                                          Should be "version" for primary key.

generated            Optional                never                     Specifies if the versioned field is generated by the
                                                                       database. The values are "never" and "always".

insert               Optional                                          Specifies if the versioned field should be included in
                                                                       the SQL INSERT statement.

name                 Required                                          Name of the property.

ormtype              Optional                int                       The data type can be any one of the following:

                                                                       integer

                                                                       long

                                                                       short



Time stamp
Use the timestamp attribute to indicate that the column contains time-stamped data. Use the timestamp attribute as
an alternative to the version attribute.


Syntax
<cfproperty
name="fieldname"
fieldtype="timestamp"
column="column name"
generated="true|[false]"
source="[vm]|db">




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 553
ColdFusion ORM




Attribute          Req/Opt               Default               Description

column             Optional                                    The name of the column that contains time-stamped
                                                               data.

fieldtype          Required                                    Specifies the field type.

                                                               Specify the field type value as timestamp for a time-
                                                               stamped field.

generated          Optional              false                 Specifies if the timestamp field is generated by the
                                                               database. You can select from the following values:

                                                               false

                                                               true


name               Required                                    Name of the property.

source             Optional              vm                    Specifies the source from where the timestamp has
                                                               to be retrieved. You can select from the following
                                                               values:

                                                               db

                                                               vm



ORM data types
You can use any of the following ORM data types for CFCs:

* string

* character

* char

* short

* integer

* int

* long

* big_decimal

* float

* double

* Boolean

* yes_no

* true_false

* text

* date

* timestamp

* binary

* serializable

* blob

* clob




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       554
ColdFusion ORM




Escaping SQL keywords in table and column name
ColdFusion automatically escapes the table name or column name if it is an SQL keyword or if there is a space in it.

The list of SQL keywords are present in <CF_HOME>/lib/sqlkeywords.properties file. This file contains standard
ANSI SQL keywords and some database-specific keywords. You can modify this file to include any other SQL keyword
that is missing. In case you are adding SQL keyword for a database other than the ones specified in this file, you should
also add it to the 'ANSI' list so that ColdFusion can use it.


Define Relationships
Relationship is the most crucial aspect of ORM. In a relational database, relation between tables are defined using
foreign key. However, in case of objects, relation between two objects is defined using association where one object has
a reference to another. ORM defines how the object relation is mapped to the database relation.

In this topic, relation and association would be used interchangeably.

Before you learn how to define the mapping for relation, it is important to understand few key concepts:

* Source object: Object having the reference to the related object is termed as source of the relationship.

* Target object: Object being referred or associated is termed as target of the relationship.

* Direction and Navigability: In relational database, the relationship is always unidirectional, which implies that you
   can navigate from one table to another but not back to the same table. However, object model can be either
   unidirectional or bidirectional. A unidirectional association means that source has the reference to the target but the
   target does not know about the source. A bidirectional association means that both the objects have reference to each
   other and you can navigate from either object to another. In other words, source has a reference to the target and
   target also has a reference to the source. This also means that both the objects are source and target at the same time.

   To set the association between objects, you need to set the references appropriately. For example, in case of Person-
   Address relation, where one person as one address, you need to associate Address to person as:

   person.setAddress(address);

   At this point, person object knows about the Address object but the address object does not know the person object.
   So, this is a unidirectional relation between Person-Address. To make this bidirectional, you need to associate
   Person to Address as:

   address.setPerson(person);

* Multiplicity: This defines how many target entities can a particular source have and how many source entities can
   a particular target have. Consider the example of artwork and artist, where an artist has many artwork pieces. In an
   object model, an artwork has reference to one artist and an artist has reference to many pieces of artwork. So, for
   artwork and artist the multiplicity is many-to-one and for artist and artwork, it is one-to-many. The other two type
   of multiplicities are one-to-one and many-to-many.

   In this topic, multiplicity would be referred to as the type of relationship.

To indicate that a property defines the relationship between two persistent components, as a result of relationship in
the database table, specify the fieldtype in the cfproperty tag to one of the following:

* one-to-one

* one-to-many

* many-to-one

* many-to-many




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  555
ColdFusion ORM




You can also use the link table to establish a relationship. A link table contains the foreign key to both tables that
participate in the relationship. ORM looks for the map key column using the link table and not the target table.


Relationship attributes
This table specifies the attribute details for all the relationship types.

The "Applies to" column indicates the relationship type that the attribute is applicable to; "all" indicates that the
attribute is applicable to all relationship types.


Attribute                  Applies to      Re/Opt        Default      Description

batchsize                  one-to-many     Optional                   An integer value that specifies the "batchsize" for fetching
                                                                      uninitialized collections. For details, see Batch fetching.
                           many-to-many

cacheuse                   one-to-many     optional                   Use this value to specify the caching strategy to be used for
                                                                      caching this component's data in the secondary cache.
                            many-to-
                           many                                       See Caching for details.

cachename                  one-to-many     optional      <entityname  Use this value to specify the name of the secondary cache.
                                                         >
                           many-to-many                               See Caching for details.
                                                         <relationnam
                                                         e>

cascade                    all             optional                   See the Cascade options section for details.

cfc                        all             Required                   Name of the associated CFC.

constrained                one-to-one      Optional      false        Whether a constraint is set on this table's Primary Key column
                                                                      to reference the Primary Key in the other table:

                                                                      true

                                                                      false

                                                                      See One-to-one relationships for details.

fetch                      all             Optional      select       Specifies whether join query or sequential select query will be
                                                                      used to retrieve the associated objects. The values are:

                                                                      join

                                                                      select

                                                                      See Lazy Loading for details.

fieldtype                  all             Required      column       Specifies the type of relationship mapping:

                                                                      one-to-one

                                                                      one-to-many

                                                                      many-to-one

                                                                      many-to-many




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               556
ColdFusion ORM




Attribute              Applies to    Re/Opt      Default     Description

fkcolumn               all           Optional                 Specifies the foreign key column.

                                                              In case the relation is established using link table, this specifies
                                                              the foreign key column in the link table that references the
                                                              primary key of the source object.

                                                              If the relationship is established using multiple foreign key
                                                              columns (that reference the composite key of the source
                                                              table), then you must use comma-separated column
                                                              names.Also, the order in which the column names are
                                                              specified must match the order of composite keys defined. If
                                                              you do not specify any values, then

                                                              * If the table exists and the constraints are defined,
                                                                 ColdFusion automatically selects the values from the table

                                                              * If the table does not exist, ColdFusion auto-generates the
                                                                 values

foreignkeyname         one-to-one    optional    autogenerate Specifies the name of the foreign key constraint. This is used
                                                 d            only when the tables are created by ORM.
                        many-to-one,

                        many-to-
                       many

index                  many-to-one   optional    false        Specifies the name of the index for the foreign key column.

insert                 many-to-one   Optional    true         Specifies whether the column should be included in SQL
                                                              UPDATE and/or INSERT statements. Values are:

                                                              true
                                                              false

                                                              Set update=false and insert=false if the column needs
                                                              to be read-only.

inverse                one-to-many   Optional    false        Specifies whether SQL query should be executed for this
                                                              association when persisting this object. Value are:
                       many-to-many
                                                              true

                                                              false

                                                              See "Inverse" section for details.

inversejoincolumn      all           Optional                 Specifies the foreign key column in the Join table that
                                                              references the primary key column in the target table.

                                                              In case of a composite key, you can use a comma-separated list
                                                              of column names.

                                                              If the join table has multiple foreign key columns (that
                                                              reference the composite key of the target table), then you
                                                              must use comma-separated column names.Also, the order in
                                                              which the column names are specified must match the order
                                                              of composite keys defined.If you do not specify any values,
                                                              then

                                                              * If the table exists and the constraints are defined, then
                                                                 ColdFusion automatically selects the values from the table

                                                              * If the table does not exist, then ColdFusion auto-generates
                                                                 the values




                                           

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                            557
ColdFusion ORM




Attribute              Applies to     Re/Opt      Default   Description

lazy                   all            Optional    true      Specifies if the association should be loaded lazily.

                                                            true

                                                            false

                                                            extra

                                                            See Lazy Loading for details.

linkcatalog            all            Optional              Catalog for the link table.

linkschema             all            Optional              Schema for the link table.

linktable              all            Required              Name of the link table.

mappedby               all            Optional              In a relationship, the foreign key can reference a unique
                                                            column other than the primary key. In such cases, use
                                                            mappedby to specify the property name that maps to the
                                                            unique key column.

missingrowIgnored      many-to-one,   Optional    false     Values are:

                       many-to-                             true
                       many,
                                                            false
                       (in ColdFusion
                                                            If the value is true, and the row that is referenced by the foreign
                       9.0.1) one-to-
                                                            key is missing, it is treated as a null association.
                       one

                                                            The default is false, in which case an exception is thrown.

name                   all            Required              Name of the field

notnull                many-to-one    optional    false     Use this to add the not-null constraint for the foreign key
                                                            column when the table is created by ORM.

optimisticlock         all            Optional    true      Whether updates to this property need acquisition of the
                                                            optimistic lock on the table row. Values are:

                                                            true

                                                            false

                                                            See Optimistic locking for details.

orderby                one-to-many    Optional              Specifies the orderby string that needs to be used to sort the
                                                            associated collection. Use the following format to specify this
                       many-to-many
                                                            string:

                                                            "col1 <asc/desc> (, col2<asc/desc>)" or "col1(,
                                                            col2)"

                                                            In the latter case, asc is taken as default.

readonly               one-to-many    Optional    false     Values are:

                       many-to-many                         true

                                                            false

                                                            If set to true, it indicates that the collection never changes and
                                                            can be cached.

remotingFetch          all            Optional    false     The value of the remotingFetch attribute is false by default for
                                                            any property that shares one-to-one, one-to-many, many-to-
                                                            one, or many-to-many relationship. Set this value to true to
                                                            retrieve data on the client-side.




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   558
ColdFusion ORM




 Attribute                 Applies to    Re/Opt      Default      Description

 singularname              one-to-many   optional    property     Use this property to define the custom name for generated
                                                     name         relationship methods. See Generated methods for
                           many-to-many
                                                                  relationships between CFCs.

 structkeycolumn           one-to-many                            The column in the target table to use as key if the collection
                                                                  type is struct.
                           many-to-many

                           type=struct

 structkeytype             one-to-many   Optional                 Specifies the data type of the key, when type=struct.

                           many-to-many                           For the entire list of data types, see the Data Types section.

                           type =struct

 type                      one-to-many   Optional                 Specifies the datatype of the relationship property:

                           many-to-many                           array

                                                                  struct

 update                    many-to-one   Optional    true         Specifies whether the column should be included in SQL
                                                                  update statement:

                                                                  true

                                                                  false

                                                                  Set update=false and insert=false if the column needs to be
                                                                  read-only.

 unique                    many-to-one   optional    false        Use this to add the unique constraint for the foreign key
                                                                  column when the table is created by ORM. This effectively
                                                                  makes this relation one-to-one.

 uniquekey                 many-to-one   optional                 Groups columns in a single unique key constraint.

 where                     one-to-many   optional                 Use this to specify a SQL that will be used to filter the collection
                                                                  retrieved. See "Applying filters on relationship" for details.
                           many-to-many



Cascade options
In an association, it is cumbersome to apply an action performed on object to the other object. For example, in case of
an Department-Employee one-to-many association, if you add an employee, the same change needs to be updated on
the Department as well. The cascade option in Hibernate allows you to perform such operations.

You can specify the following values in the cascade attribute:

* all: Allows you to apply all operations to be cascaded to the associated object.

* save-update: If the parent object is saved, the associated objects are saved as well.

* delete: Deletes the child object if the delete operation is called on the parent object.

* delete-orphan: This is a special kind of cascade option that applies to one-to-many relation only. Deletes all child
   objects for which the association has been removed.

* all-delete-orphan: Cascades all operations to child objects, and performs delete-orphan action.

* refresh: Cascades the refresh action to the child object. The refresh action is used to reload an object and its
   collections.

Typically, cascade attribute is not used on a many-to-one or a many-to-many relationship.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     559
ColdFusion ORM




You can also specify comma separated cascade values in the cascade attribute. For a one-to-one or a one-to-many
relationship, the most common values are all-delete-orphan.

For an association where the child object can continue to exist even if the parent object is deleted, you can apply the
save-update cascade value.


Applying filter on associated objects
In one-to-many and many-to-many relationships, an array or struct is retrieved. Filters can be applied to retrieve a
subset of the associated objects. The filter can be specified in the where attribute, which is an SQL where clause. In a
one-to-many association for artist and artwork:

If you want to retrieve only the unsold artwork for every Artist object, you need to define the mapping as follows:

<cfproperty name="unsoldArts" cfc="Art" fieldtype="one-to-many" fkcolumn="ARTISTID"
where="issold=0">


Inverse
In a bidirectional relationship, the inverse attribute is used on an association property to specify whether an SQL
query should be executed for the association, when persisting the object.

Consider the ART and ARTIST CFCs, which have a bidirectional one-to-many relationship. This means that each ART
object has a reference to the ARTIST object and ARTIST object has a reference to the ART object. While persisting
ARTIST and the associated ART, the relationship can be established in the database from both sides of the object.
Setting inverse=true on one side of the relation tells ORM to ignore this side of relation for executing the SQL.

As a general rule, in a bidirectional relation, one side must set inverse to true. For one-to-many or many-to-one
relation, inverse should be set on the many side of the relation. For example, in ARTIST-ART relation, inverse should
be set to true on the 'art' property in ARTIST.

In many-to-many relation, you can set inverse=true on any side of the relation.


One-to-one relationships
A one-to-one relationship is where the source object has an attribute that references another single target object and
vice-versa. An example of this relationship is the relationship between an employee and the assigned office cubicle,
where one employee has one office cubicle and one office cubicle belongs to only one employee.

A one-to-one relationship between two persistent components are defined using fieldtype value one-to-one.

Syntax:

<cfproperty name="fieldname"
fieldtype="one-to-one"
cfc="Referenced_CFC_Name"
linktable="Link table name"
linkcatalog="Catalog for the link table"
linkschema="Schema for the link table"
fkcolumn="Foreign Key column name"
inversejoincolumn="Column name or comma-separated list of primary key columns"
cascade="cascade_options"
constrained="true|[false]"
fetch="join|[select]"
lazy="[true]|false">

There are two types of one-to-one relationships:

* Primary key association



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        560
ColdFusion ORM




* Unique Foreign Key association


Primary key association
In this type of association, the primary key of one table references to the primary key of another table. That is, both the
tables share the same primary key.

The following example shows how to define this mapping.


Example
Consider the EMPLOYEE and OFFICECUBICLE example. Both the tables share the same primary key. The mapping
for these tables are as follows:

* EMPLOYEE.cfc

   <cfcomponent persistent="true" table="Employee">
         <cfproperty name="id" fieldtype="id" generator="native">
         <cfproperty name="firstname">
         <cfproperty name="lastname">
         <cfproperty name="officecubicle" fieldtype="one-to-one" cfc="OfficeCubicle">
   </cfcomponent>

* OFFICECUBICLE.cfc

   <cfcomponent persistent="true" table="OfficeCubicle">
         <cfproperty name="id" fieldtype="id" generator="foreign"
   params="{property='Employee'}">
         <cfproperty name="Employee" fieldtype="one-to-one" cfc="Employee" constrained="true">
         <cfproperty name="Location">
         <cfproperty name="Size">
   </cfcomponent>

   fieldtype=one-to-one specifies that the property is a one-to-one property.

   constrained=true on Employee property in OFFICECUBICLE.cfc, means that a constraint is set on the
   OFFICECUBICLE table for its ID to reference the ID in the EMPLOYEE table.

   The ID of EMPLOYEE table is auto-generated. The ID of the OFFICECUBICLE table should be the same as the ID
   of the Employee table. For this, set generator="foreign". Foreign generator takes one parameter 'property' as
   input, which should be the relationship property name of OFFICECUBICLE entity which is 'EMPLOYEE' in this case.

   Here, primary key values of related rows in both the tables must be the same. The identity generator algorithm in
   the mapping for the component (whose mapped table has the constraint), must be set to foreign.


Unique foreign key association
In this type of association, the foreign key of one table references the primary key of another table, and the foreign key
column has a unique constraint. To define this relationship, fkcolumn attribute should be specified on the
relationship-property of the CFC whose table contains the foreign key column. The other end of relation should use
mappedby attribute.

Syntax




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     561

ColdFusion ORM




<cfproperty
name="fieldname"
fieldtype="one-to-one"
cfc="Referenced_CFC_Name"
linktable="Link table name"
linkcatalog="Catalog for the link table"
linkschema="Schema for the link table"
fkcolumn="Foreign Key column name"
inversejoincolumn="Column name or comma-separated list of primary key columns"
mappedby="Mapped_Field_name_in_referenced_CFC"
cascade="none"
fetch="join|[select]"
lazy="[true]|false">

Note: The mappedby attribute can not be specified with the fkcolumn attribute.


Example
In the EMPLOYEE and OFFICECUBICLE example, OFFICECUBICLE has a foreign key column, EMPLOYEEID.
This foreign key references the primary key of the Employee table. OFFICECUBICLE has an auto-generated primary
key, which does not participate in the relationship.

EMPLOYEE.cfc

<cfcomponent persistent="true" table="Employee">
     <cfproperty name="EmployeeID" fieldtype="id" generator="native">
     <cfproperty name="firstname">
     <cfproperty name="lastname">
     <cfproperty name="officecubicle" fieldtype="one-to-one" cfc="officecubicle"
mappedby="Employee">
</cfcomponent>

OFFICECUBICLE.cfc

<cfcomponent persistent="true" table="officecubicle">
     <cfproperty name="id" fieldtype="id" generator="native">
     <cfproperty name="Employee" fieldtype="one-to-one" cfc="Employee" fkcolumn="EmployeeID">
     <cfproperty name="Location">
     <cfproperty name="Size">
</cfcomponent>

* In OFFICECUBICLE entity, fkcolumn="EmployeeID" specifies that EmployeeID is the foreign key column in
   OFFICECUBICLE table.

* mappedby="Employee" specifies that the one-to-one relationship is with the foreign-key property 'EMPLOYEE' in
   OFFICECUBICLE entity and not with its primary key.

* In Employee entity, fkcolumn should not be specified.

In this case, OFFICECUBICLE entity has a independent Primary key which is auto-generated.


One-to-many relationship
A one-to-many relationship is where the source object has field that stores a collection of target objects. These target
objects may also have an inverse relationship back to the source object. This relationship is established by having a
foreign key in the target table that maps to the primary key of the source table.

An example of a one-to-many relationship is the relation between artist and art, where the artist has many artwork
pieces.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        562
ColdFusion ORM




A one-to-many relationship between two persistent components is defined using the fieldtype value one-to-many
in the cfproperty tag. The source object contains a collection of target objects. ColdFusion allows the collection to
be one of the following types:

* Array

* Struct

This collection is a persistence aware collection. Any addition or deletion from this collection is automatically persisted
in the database.


Array
An Artist object can contain the Art objects as an array. To define this mapping in the CFC, use the following syntax:

Syntax

<cfproperty
name="field_name"
fieldtype="one-to-many"
cfc="Referenced_CFC_name"
linktable="Link table name"
linkcatalog="Catalog for the link table"
linkschema="Schema for the link table"
fkcolumn="Foreign Key column name"
inversejoincolumn="Column name or comma-separated list of primary key columns "
type="array"
orderby="order_by_string"
cascade="cascade_options"
lazy="[true]|false|extra"
fetch="join|[select]"
inverse="true|[false]"
batchsize="N"
optimisticlock="[true]|false"
readonly="true|[false]">

For the artist-art example, the relationship property in Artist.cfc is defined as follows:

<cfproperty name="art" type="array" fieldtype="one-to-many" cfc="Art" fkcolumn="ARTISTID">

* type-array specifies that the artist object will contain art objects in an array.

* fkcolumn="ArtistID" specifies that the foreign key column is ARTISTID that references the primary key of
   ARTIST table.


Struct
An Artist object can contain the Art objects as a struct. The key would be any column in the ART table (usually the
primary key or a unique key). The value would be the Art object. To define this mapping, use the following syntax.

Syntax




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    563
ColdFusion ORM




<cfproperty
name="field_name"
fieldtype="one-to-many"
cfc="Referenced_CFC_name"
linktable="Link table name"
linkcatalog="Catalog for the link table"
linkschema="Schema for the link table"
fkcolumn="Foreign Key column name"
inversejoincolumn="Column name or comma-separated list of primary key columns"
type="struct"
orderby="order_by_String"
structkeycolumn = "Structure_Key_Column"
structkeytype="ormtype"
cascade="cascade_options"
lazy="[true]|false|extra"
fetch="join|[select]"
inverse="true|[false]"
batchsize="N"
optimisticlock="[true]|false"
readonly="true|[false]">

For the artist-art example, you can define the relationship property as:

<cfproperty name="art" type="struct" fieldtype="one-to-many" cfc="Art" fkcolumn="ARTISTID"
structkeytype="int" structkeycolumn="ArtID">

* type=struct specifies that the artist object will contain art objects in a struct.

* structkeycolumn="ArtID" specifies that the key of the struct would be ArtID.

   Note that ARTID is the primary key in Art table.

* structkeytype="int" specifies the datatype of structkeycolumn.

* fkcolumn="ArtistID" specifies that the foreign key column is ARTISTID that references the primary key of Artist
   table.


Many-to-one relationship
A many-to-one relationship is the inverse of a one-to-many relationship. In this relationship, many source objects can
reference the same target object.

An example of this relationship is the relation between Art and Artist, where many Art are created by the same Artist.
This relationship is established with the foreign key in the source table that references the primary key in the target
table.

A many-to-one relationship between two persistent components is defined using the fieldtype value many-to-one
in the cfproperty tag.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    564
ColdFusion ORM




Syntax
<cfproperty
name="fieldname"
fieldtype="many-to-one"
cfc="Referenced_CFC_Name"
linktable="Link table name"
linkcatalog="Catalog for the link table"
linkschema="Schema for the link table"
fkcolumn="Foreign Key column name"
inversejoincolumn="Column name or comma-separated list of primary key columns"
column="Foreign_Key_Column"
mappedby="Mapped_Field_name_in_referenced_CFC"
cascade="cascade_options"
fetch="join|[select]"
lazy="true|false"
insert="[true]|false"
update="[true]|false"
optimisticlock="[true]|false"
missingrowIgnored="true|[false]">

For the art-artist example, the relationship in the ART.cfc can be defined as:

<cfproperty name="artist" fieldtype="many-to-one" fkcolumn="artistid" cfc="Artist">

fkcolumn="ARTISTID" indicates that the foreign key column in Art table references the primary key ARTISTID of
ARTIST table.


Many-to-many relationships
A many-to-many relationship is where the source objects contain a collection of target objects and the target objects
in turn contain a collection of source objects.

An example of a many-to-many relationship is the relation between Order and Product, where an order has many
products and a product has many orders.

This relationship is established by using a third table called a 'LinkTable'. The LinkTable contains the foreign key to
both the tables participating in the relation. ORM looks for the map key column in the LinkTable and not the target
table.

In the preceding example of Order-Product, a many-to-many relationship is established by using LinkTable.

A many-to-many relationship between two persistent CFCs is defined using the fieldtype="many-to-many" value
in the cfproperty tag.

Note: If the fkcolumn name is not specified, ORM generates the fkcolumn name in the "#relationName#_ID" format.


Syntax
Order.cfc




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    565
ColdFusion ORM




<cfproperty
name="fieldname"
fieldtype="many-to-many"
cfc="fully qualified name"
linktable="Link table name"
linkcatalog="Catalog for the link table"
linkschema="Schema for the link table"
fkcolumn="Foreign Key column name"
inversejoincolumn="Column name or a composite key with comma-separated primary key columns"
mappedby="Property in the target component that is referenced by fkcolumn in join table"
type="[array]|struct"
orderby="order by String
structkeycolumn="The structure key column name"
structkeydatatype="datatype".
cascade="cascade options" inverse="true|[false]" lazy = "[true]|false" [optional]
fetch="join|[select]" [optional] batchsize="integer" optimisticlock="[true]|false"
readonly="true|[false]"
missingrowIgnored="true|[false]">

For the Order-Product example, the many-to-many relationship is established using a third table "OrderProduct" that
has two foreign keys: OrderId and ProductId. OrderId references the primary key orderId in the order table, and
ProductId references the primary key productId in the Product table. This relationship can be defined as follows:

* Order.cfc

   <cfproperty
   name="products"
   fieldtype="many-to-many"
   CFC="Product"
   linktable="Order_Product"
   FKColumn="orderId"
   inversejoincolumn="productId"
   lazy="true"
   cascade="all"
   orderby="productId">

* Product.cfc

   <cfproperty
   name="orders"
   fieldtype="many-to-many"
   CFC="Order"
   linktable="Order_Product"
   FKColumn="productId"
   inversejoincolumn="orderId"
   lazy="true"
   cascade="all"
   orderby="orderId">

   The fkcolumn here is the foreign key in the link table that references the primary key of the source table.

   InverseJoinColumn is the foreign key in the link table that references the primary key of the target table. This
   attribute can also take a composite key value, for example you can specify inversejoincolumn="field1, field2", where
   field1 and field2 form the composite key.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                            566
ColdFusion ORM




Advanced mapping

Collection Mapping
Collection mapping is similar to a one-to-many relationship mapping. However, in collection mapping, you have a
collection of values instead of a collection of persistent target objects.

Consider the Artist-Art tables. If you want each Artist object to contain an array of artwork names instead of artwork
objects, collection mapping should be used.

To define collection mapping in the CFC, use fieldtype="collection" in the cfproperty tag.

The collection can either be Array or Struct.


Array


Syntax
name="field_name"
fieldtype="collection"
type="array"
table="table_name"
fkcolumn="foreign_key_column_name"
elementtype="ormtype"
elementColumn="column_name from the link table that should be
used for populating"
orderby="order by string"
lazy = "true|[false]"
readonly="true|[false]"
optimisticlock="[true]|false"
batchsize="batch size">


Example
If each Artist object contains an array of artwork names instead of artwork objects, this mapping can be defined in
Artist.cfc as:

<cfproperty name="artNames" fieldtype="collection" type="array" table="ART"
fkcolumn="ARTISTID" elementcolumn="ARTNAME" elementtype="string">


Attribute

Attribute            Req/Opt                         Default                       Description

batchsize            Optional                                                      An integer value that specifies the
                                                                                   "batchsize" for fetching uninitialized
                                                                                   collections. For details, see Lazy Loading.

elementColumn        Required                                                      Specifies the column name that contains
                                                                                   the data to be fetched for collection.

elementtype          Optional                        String                        Data type of the selected column. SeeORM
                                                                                   data types for details.

fieldtype            Required                                                      Should be "collection".




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                             567
ColdFusion ORM




Attribute        Req/Opt                   Default              Description

fkcolumn         Optional                                       The foreign key column in the specified
                                                                table.

                                                                If you do not specify the foreign key
                                                                column and useDBForMapping is true
                                                                in ormsettings, ColdFusion
                                                                automatically determines a foreign key
                                                                column after inspecting the database.

lazy             Optional                  true                 Specifies if loading is to be done lazily:

                                                                true

                                                                false

                                                                See Lazy Loading for details.

name             Required                                       Name of the collection.

optimisticlock   Optional                  true                 Specifies the locking strategy.

                                                                true

                                                                false

orderBy          Optional                                       Specifies the Order By string.

readonly         Optional                  false                true

                                                                false

                                                                If set to true, it indicates that the collection
                                                                never changes and can be cached.

table            Required                                       Name of the table from where the values
                                                                will be fetched.

type             Optional                  array                Specifies if the collection type is:

                                                                array

                                                                struct



Struct


Syntax
<cfproperty
name="field_name"
fieldtype="collection"
type="struct"
table="table_name"
fkcolumn="foreign_key_column_name"
structkeycolumn="column in the target table to be used as key in the struct"
structkeytype="ormtype of the key in the struct"
elementtype="ormtype of the valye in the struct"
elementColumn="column name from the table that should be used in
value of struct"
orderby="order by string"
lazy = "[true]|false"
readonly="true|[false]"
optimisticlock="[true]|false"
batchsize="batch size">




                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                568
ColdFusion ORM




 Attribute           Req/Opt                  Default                      Description

 batchsize           Optional                                              An integer value that specifies the "batchsize" for
                                                                           lazily fetching instances of this collection.

 elementcolumn       Required                                              Specifies the column name that contains the data to
                                                                           be fetched for collection.

 elementtype         Required                                              Data type of the value. See ORM data types for details.

 fieldtype           Required                                              Should be a collection.

 fkcolumn            Optional                                              The foreign key column in the table.

                                                                           If foreign key column is not specified and
                                                                           useDBForMapping is set to true in ORMSetting,
                                                                           ColdFusion automatically determines the Foreign Key
                                                                           column after inspecting the database.

 lazy                Optional                 true                         Specifies if loading is to be done lazily:

                                                                           true

                                                                           false

                                                                           See Lazy Loading for details.

 name                Required                                              Name of the collection property.

 optimisticlock      Optional                 true                         true

                                                                           false

 orderby             Optional                                              Specifies the Order By string.

 readonly            Optional                 false                        Value are:

                                                                           true

                                                                           false

                                                                           If you set it to true, the collection never changes and
                                                                           can be cached.

 structkeycolumn     Required                                              Column name in the table that will be used as key of
                                                                           struct.

 structkeyType       Required                                              Specifies the data type of the key, when
                                                                           type=struct.

                                                                           For the entire list of data types, see the Data Type
                                                                           section.

 table               Required                                              Name of the table from where the collection will be
                                                                           fetched.

 type                Optional                 array                        Specifies if the collection type is:

                                                                           array

                                                                           struct



Inheritance mapping
If the object you need to persist has a hierarchy, the CFCs of that object hierarchy need to be mapped to the relational
tables such that the entire hierarchy is persisted.

There are multiple strategies followed for inheritance mapping:

* Table per hierarchy



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          569
ColdFusion ORM




* Table per subclass without discriminator

* Table per subclass with discriminator


Table per hierarchy
In this model, the object hierarchy is persisted in a single table. This table includes columns for all the properties of all
the CFCs in the hierarchy. The concrete subclass represented by a row is identified based on the value of the
discriminator column. In this strategy, all the CFCs of the hierarchy must have the same table name.

Note: If the discriminator column and discriminator value is not specified, a default discriminator column name and
value is picked up.

Example

The following example demonstrates an implementation of table per hierarchy:


            Payment                                    Payment Table

  PaymentID <<PK>>                              Payment (extended by)
  PaymentType <<Discriminator>>                    Check Payment
  Amount                                           CreditCard Payment
  CardNo
  CardType
  CheckNo
  BankName
  City

Example for table per hierarchy


In the preceding figure, discriminatorColumn is PaymentType. Depending on the values of PaymentType whether
it is credit card or check, the row is represented as a CreditCardpayment or checkPayment object respectively.

The following example illustrates how you can model the table per hierarchy:

Payment.cfc (parent class)

<cfcomponent persistent="true" table="Payment" discriminatorColumn="paymentType">
<cfproperty name="id">
   <cfproperty name="amount">
</cfcomponent>

CreditCardPayment.cfc

<cfcomponent persistent="true" extends="Payment" table="Payment" discriminatorValue="CCard">
      <cfproperty name="cardNo">
      <cfproperty name="cardType">
</cfcomponent>

CheckPayment.cfc

<cfcomponent persistent="true" extends="Payment" table="Payment" discriminatorValue="check">
      <cfproperty name="checkNo">
      <cfproperty name="bankName">
      <cfproperty name="city">
</cfcomponent>




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    570
ColdFusion ORM




Table per subclass without discriminator
In this model, there are separate tables for each class in the hierarchy and these tables are joined by a primary key.
When the object is persisted, properties of the parent component are stored in the parent table and the remaining
properties are stored in the child table.


                              Payment

                   PaymentID <<PK>>
                   Amount                                                           Payment class extended by

                                                                                    CheckPayment class

      CreditCardPayment                    CheckPayment                             CreditCardPayment class


 paymentID                            paymentID
 cardNo                               checkNo
 cardType                             bankName
                                      city

Table per subclass without discriminator


In the preceding figure, the tables are joined by join column paymentId. You can model the tables as follows:

Payment.cfc

<cfcomponent persistent="true" table="Payment">
  <cfproperty name="paymentId">
  <cfproperty name="amount">
</cfcomponent>

CreditCardpayment.cfc

<cfcomponent persistent="true" extends="Payment" table="CreditCardPayment"
                     joinColumn="paymentId">
     <cfproperty name="cardNo">
  <cfproperty name="cardType">
</cfcomponent>

CheckPayment.cfc

<cfcomponent persistent="true" extends="Payment" table="CheckPayment" joinColumn="paymentId">
     <cfproperty name="checkNo">
  <cfproperty name="bankName">
  <cfproperty name="city">
</cfcomponent>

When an object of type CreditCardPayment is persisted, the property amount is stored in the payment table and the
properties cardNo and cardType are stored in the CreditCardPayment table. The primary key of the
CreditCardPayment remains the same as the primary key of the Payment table.


Table per subclass with discriminator
This model is similar to the table per subclass without discriminator strategy except that there is a discriminator
column in the parent table. In addition, the child components has a disciminatorValue attribute in the
cfcomponent tag.

The following example demonstrates the table per subclass with discriminator attribute:

Payment.cfc




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  571
ColdFusion ORM




<cfcomponent persistent="true" table="Payment" discriminatorColumn="paymentType">
        <cfproperty name="paymentId">
        <cfproperty name="amount">
</cfcomponent>

CreditCardPayment.cfc

<cfcomponent persistent="true" extends="Payment" table="CreditCardPayment"
joinColumn="paymentId" discriminatorValue="CCard">
        <cfproperty name="cardNo">
        <cfproperty name="cardType">
</cfcomponent>

CheckPayment.cfc

<cfcomponent persistent="true" extends="Payment" table="CheckPayment" joinColumn="paymentId"
discriminatorValue="Check">
        <cfproperty name="checkNo">
        <cfproperty name="bankName">
        <cfproperty name="city">
</cfcomponent>

When an object of type CreditCardPayment is persisted, the property amount is stored in the payment table and the
properties cardNo and cardType are stored in the CreditCardPayment table. The primary key of CreditCardPayment
remains the same as the primary key of the Payment table. The value of PaymentType is the value of
disciminatorColumn attribute of the respective object.


Embedded mapping
This mapping is used when a CFC has an embedded object which also needs to be persisted along with the parent's
data. The CFC of the embedded object must have the attribute embedded set to "true" on the cfcomponent tag.

Important: The embedded object cannot be a persistent object. This feature is supported only when the hibernate
mapping is explicitly defined in the hibernate mapping file (.hbmxml files).

        Name                    Employee

 FirstName                  EmployeeID
 LastName                   EmployeeName
 Title                      Designation



Name.cfc embedded in Employee.cfc


The diagram shows two CFCs Employee and Name where EmployeeName field of the Employee.cfc is an object of
Name.cfc. In the database, both these objects are persisted in the Employee table as a single row. Name object itself
does not have its own identity. This mapping can be modeled as follows:

name.cfc

<cfcomponent embedded="true">
        <cfproperty name="FirstName">
        <cfproperty name="LastName">
        <cfproperty name=" Title">
</cfcomponent>

employee.cfc




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  572
ColdFusion ORM




<cfcomponent persistent="true">
     <cfproperty name="EmployeeID">
     <cfproperty name="EmployeeName">
     <cfproperty name="Designation">
</cfcomponent>

employee.hbmxml

<hibernate-mapping >
     <class     name="cfc:Employee" table="Employees">
          <id name="EmployeeID" type="integer" column="EmployeeID">
                <generator class="native"/>
          </id>
          <component name="EmployeeName" class="cfc:Name">
          <property name="LastName" type="string" column="LastName"/>
          <property name="FirstName" type="string" column="FirstName"/>
          <property name="Title" type="string" column="Title"/>
          </component>
          <property name="Designation" type="string" column="Designation"/>
     </class>
</hibernate-mapping>

If the persistent CFC has a collection of embedded objects, then this mapping also has to be defined in the XML as
shown in the following example. Here, employee object has a collection of IMData objects. Note that the IMData object
is not persistent.

employee.cfc

<cfcomponent          persistent="true">
     <cfproperty name="EmployeeID">
     <cfproperty name="EmployeeName">
     <cfproperty name= "IMIDs" type="array">
     <cfproperty name="Designation">
</cfcomponent>

IMData.cfc

<cfcomponent embedded="true">
     <cfproperty name="type">
     <cfproperty name="ID">
</cfcomponent>

employee.hbmxml

<hibernate-mapping>
     <class     name="cfc:Employee" table="Employees">
           <id name="EmployeeID" type="integer" column="EmployeeID">
                               <generator class="native"/>
           </id>
           <property name="EmployeeName" type="string" column="EmployeeName"/>
                <bag name="IMIDs" table="IMData" lazy="true">
                    <key column="EmployeeID" />
                    <composite-element class="cfc:IMData">
                         <property name="type" type="string" column="Type"/>
                         <property name="ID" type="string" column="ID"/>
                    </composite-element>
                </bag>
                <property name="Designation" type="string" column="Designation"/>
     </class>
</hibernate-mapping>



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   573
ColdFusion ORM




Emp.cfm

<cfscript>
     employee = EntityNew("Employee");
     employee.setEmployeeName("Dan Watson");
     imdata1 = new IMData();
     imdata1.setType("IMClient1");
     imdata1.setID("msngrId1");
     imdata2 = new IMData();
     imdata2.setType("IMClient 2");
     imdata2.setID("msngrId2");
     employee.setIMIDs([imdata1, imdata2]);
     EntitySave(employee);
</cfscript>

For more details on component mapping in hibernate, see Component Mapping in Hibernate Reference Guide.


Join mapping in a CFC
Join mapping is used to map the properties of one CFC to several tables. In this case, tables are joined using a join
column. For example, consider a case where the Employee and Address tables, are mapped to a single CFC Employees.
Therefore, the employee.cfc has some fields, which are persisted in Employee table and some fields that are persisted
in Address table. The attributes joincolumn and table should be specified for those fields that need to be persisted
in Address table. In this case, table would be Address and joinColumn would be AddressID.

Following is a sample employee.cfc

Employee.cfc

<cfcomponent persistent="true">
     <cfproperty name="id">
     <cfproperty name="name">
     <cfproperty name="houseno" column="houseno" table="Address" joincolumn="addressId">
     <cfproperty name="street" table="Address" joincolumn="addressId">
     <cfproperty name="city" table="Address" joincolumn="addressId">
     <cfproperty name="country" table="Address" joincolumn="addressId">
</cfcomponent>


Define the ORM mapping in the Hibernate mapping file
ColdFusion can also use the standard Hibernate Mapping XML file to define the mapping between the object and the
database. You can use both Java classes and CFCs in Hibernate mapping. For a complete description of hibernate
mapping, see www.hibernate.org/hib_docs/reference/en/html/mapping.html.

Note the following points when using Hibernate mapping files.

* The extension of the Hibernate configuration file is *.hbmxml.

* The class name must be specified as cfc:<fully qualified name of cfc>. If a package is specified in the hibernate
   mapping, then specify the class name as cfc:<name of cfc>.

* The entityname attribute is optional. If you do not specify this attribute, it takes the component name, by default.
   For example, for the component artgallery.art, the value of the entityname attribute is "Art", by default.

* The entity name must be unique for an application. If there are two components with the same name (even if they
   are in different packages), specify different entity names for each of these components.

The following is an example of Hibernate mapping:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  574
ColdFusion ORM




<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">
<hibernate-mapping>
     <class lazy="true" name="cfc:artGallery.Art" schema="APP" table="Art">
     <id name="artid" type="int">
           <column length="10" name="ARTID"/>
           <generator class="identity"/>
     </id>
           <property name="artname" type="string">
                <column length="50" name="ARTNAME"/>
           </property>


           <property name="price" type="java.math.BigDecimal">
                <column length="19" name="PRICE"/>
           </property>


           <property name="largeimage" type="string">
                <column length="30" name="LARGEIMAGE"/>
           </property>


           <property name="mediaid" type="int">
                <column length="10" name="MEDIAID"/>
           </property>


           <property name="issold" type="boolean">
                <column length="5" name="ISSOLD"/>
           </property>
           <many-to-one class="cfc:artGallery.Artists"              column="artistid" name="artist"/>
     </class>
</hibernate-mapping>



Mapping CFCs without properties
If a persistent CFC has no property, all table columns are added as properties provided that the table exists and you
have specified it.


Example
Artist.cfc

component persistent="true" table="artists"
{
}

index.cfm

<cfset artists = entityLoad("Artist")>
<cfdump var="#artists#">

The columns are fetched only as column properties. Relationship, timestamp, or version field are not added.


Limitations
* If the mapping file for the CFC exists, then the properties are not added automatically.

* In the case of inheritance, properties are not added to the CFC automatically.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     575
ColdFusion ORM




Working with objects

Operations can be performed on an Entity object, and the auto-generated methods in the entity can be called.


Generated accessors
As described in Define ORM mapping, the persistent fields for an object are defined in the CFC using cfproperty.
ColdFusion generates the accessor methods (getter and setter) for each property in the CFC that can be invoked. For
more information, see Implicit Get and Set Functions. For example, if a property is defined in Artist as follows:

<cfproperty name="firstName" >

There are two methods generated in the Artist object:

* setFirstName(firstName)

* getFirstName()

You can invoke these methods like regular methods in the CFC. For a property, the generated setter saves the value for
the property in the object's VARIABLES scope. The generated getter retrieves the value of the property from the
VARIABLES scope. ORM always uses the property value from the VARIABLES scope. That is, while saving the object's
data in the table, ORM retrieves the value of the property from VARIABLES scope. While populating the object after
reading from the table, ORM puts the property's value in the VARIABLES scope.If you define your own accessor
methods for a property, store the property value in the VARIABLES scope for ORM to access it.


Generated methods for relationships between CFCs
When a relationship is defined in a CFC, ColdFusion generates a few methods in the CFC object to add or remove
associated objects and to check their existence, for each relationship defined in the CFC.

The generated methods for relationships include:

* add<relationship_property_name>()

   This method is generated for one-to-many and many-to-many relationships. The method adds the given object to
   the association collection (array or struct) of the component. For a bidirectional relationship, this method does not
   set the association on the other end.

   For type="array", the method signature is:

   add<relationship_property_name>(<associated_object>)

   For type="struct", the method signature is:

   add<relationship_property_name>(<key>, <associated_object>)

* boolean remove<relationship_property_name>()

   This method is generated for one-to-many and many-to-many relationships. The method removes the object from
   the associated collection (array or struct) of the component. If the associated object was removed from the
   collection successfully, then true is returned. For a bidirectional relationship, this method does not remove the
   association from the other end.

   For type="array", the method signature is:

   remove<relationship_property_name>(<associated_object>)

   For type="struct", the method signature is:

   remove<relationship_property_name>(<key>).




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        576
ColdFusion ORM




* boolean Has<relationship_property_name>()

   This method is generated for all the relationships. For one-to-many and many-to-many, this method checks
   whether the association collection is empty. If the association collection is empty, it will return true. For one-to-one
   and many-to-one, this method checks whether the associated object exists.

* boolean Has<relationship_property_name>(<associated_object>)

   This method is generated for one-to-many and many-to-many relationships. The method checks whether the given
   associated object is present in the association collection. If it is present, it returns true.

   For type="array", the method signature is

   boolean has<relationship_property_name>(<associated_object>)

   For type="struct", the method signature is

   boolean has<relationship_property_name>(<key>)


Example
Consider the following example of artists (ARTISTS table) and artwork (ART table), where the artist forms a one-to-
many relationship with artwork.

Artist.cfc

<cfcomponent persistent="true" schema="APP" table="Artists">
     <cfproperty name="artistid" fieldtype="id"/>
     <cfproperty name="firstname"/>
     <cfproperty name="lastname"/>
     <cfproperty name="state"/>
     <cfproperty name="art" fieldtype="one-to-many" cfc="Art" fkcolumn="ArtistID" >
</cfcomponent>

Art.cfc

<cfcomponent persistent="true" schema="APP" table="Art">
     <cfproperty name="artid" fieldtype="id"/>
     <cfproperty name="artname"/>
     <cfproperty name="issold"/>
</cfcomponent>

In this example Artist has a relation field art with Art. The following methods are implicitly added to the Artist object:

* addArts(Art art)

* booleanremoveArts(Art art)

* booleanhasArts()

* booleanhasArts(Art art)

The attribute singularName provides the flexibility to change the name of the generated relationship methods. For
example, if the relationship property of Artist is specified as follows:

<cfproperty name="art" fieldtype="one-to-many" cfc="Art" fkcolumn="ArtistID"
singularName="Art">

then the following methods are generated:

* addArt(Art art)

* removeArt(Art art)

* hasArt()



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     577
ColdFusion ORM




* hasArt(Art art)


Perform create, read, update, delete operations on ORM Objects
In any data-centric application, you can perform the following operations on the database:

* Insert (Create)

* Update

* Retrieve

* Delete

Once the object relational model is defined in a ColdFusion application, you can perform CRUD operations on the
objects directly using the methods provided by ColdFusion ORM. ColdFusion ORM, in turn, takes care of persisting
the object in the database.


Create entities

EntityNew

Creates an object for persistent CFC with the given entity name. This is similar to CreateObject but it uses entityname
whereas CreateObject takes CFC name. If there is no CFC defined in the application with the given entityname, an
error will be thrown.

If the persistent CFC has an init method, then the function EntityNew calls the init method while creating the
object.


Syntax
<entity> EntityNew("<entityName>")


Save entities

EntitySave

Saves or Updates the data of the entity and all related entities to the database.

ColdFusion automatically tries to find if a new record should be inserted or an existing record be updated for the given
entity. If forceinsert=true, then ColdFusion always tries to insert the entity as a new record.

Calling this method may not run the insert/update SQL immediately. It is possible that various SQL statements are
queued and then run as a batch for performance reasons. The SQL statements are run when the ORM session is
flushed.


Syntax
EntitySave(entity, [forceinsert])


 Parameter                 Description

 entity                    The Entity that needs to be saved in the database.

 forceinsert               If true, then ColdFusion always tries to insert the entity as a new record.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      578
ColdFusion ORM




Example:
<cfset artist = EntityNew("Artist")>
<cfset artist.setFirstName("Marcia")>
<cfset artist.setlastName("Em")>
<cfset EntitySave(artist)>


Update objects

EntitySave

The method to update an object is the same as saving an object. Load the object that needs to be updated, make updates,
and save the object.


Syntax
EntitySave(entity, [forceinsert])


Example
The following example changes the first name of an artist with an Artist ID 1:

<cfset artist1 = EntityLoad("Artist", 1, true)>
<cfset artist1.setFirstName("Garcia")>
<cfset EntitySave(artist1)>


Read/Load objects
Entities are loaded using the EntityLoad methods. All EntityLoad methods take the entity name as input.

If the persistent CFC has an init method, the methods call the init method while creating objects.


Syntax
EntityLoad (entityname)
EntityLoad (entityname, id [, unique])
EntityLoad (entityname, filtercriteria [,unique]
EntityLoad(entityname, filtercriteria, sortorder [, options])
EntityLoadByExample(sampleentity [, unique])
EntityReload(entity)


Examples
* EntityLoad (entityname)

    Loads and returns an array of entities of the specified entity name. For example, to retrieve all the objects of the
    "artist" CFC:

    <cfset artist = EntityLoad('ARTIST')>

* EntityLoad (entityname, id [, unique])

    Loads and returns an entity whose Primary key's value is id. The entity is returned as an array by default. If unique
    is true, then the entity is returned.

    If the entity has a composite key, then the id has to be specified as key-value pairs (ColdFusion struct).

    Example 1:

    This example loads the Artist object with PK 100 and returns a single-element array containing the artist object.

    <cfset artistArr = EntityLoad('Artist', 100)>

    Example 2:



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   579
ColdFusion ORM




   This example loads the Artist object with PK 100 and returns the artist object.

   <cfset artistobj = EntityLoad('Artist', 100, true)>

   Example 3:

   This example loads the OrderDetail object which has the composite key OrderID=100 and ProductID=1 and
   returns the orderdetail object.

   <cfset orderDetail = EntityLoad('orderdetails', {OrderID=100, ProductID=1}, true)>

* EntityLoad (entityname, filtercriteria [,unique]

   Loads and returns an array of entities of the given entity name that matches the filtercriteria.
   filtercriteria is a key-value pair (ColdFusion struct) of property names and its values. If there are more than
   one key-value pair in filtercriteria, then they always use the AND operator. If you are sure that only one record
   exists that matches this filtercriteria, unique=true can be specified so that a single entity is returned instead
   of an array. If unique=true and multiple records are returned, then an exception occurs. For example, to retrieve
   details of all artists that have state `CA':

   <cfset artistsFromCA = EntityLoad('Artist', {state="CA"}>

   To retrieve a unique object, specify unique= "true". If more than one object satisfies the condition, an exception
   occurs.

   This example loads the artist object whose firstName is "Austin" and lastname is "Weber".

   <cfset artist = EntityLoad('artist', {firstname="Austin", lastname="Weber"}, "true")>

* EntityLoad(entityname,filtercriteria,sortorder[, options])

   Loads and returns an array of entities that satisfy the filtercriteria that is sorted as specified by the sortorder
   parameter.

   filtercriteria is a key-value pair (ColdFusion struct) of property names and its values. If there are more than
   one key-value pairs in filtercriteria, then they always use the AND operator.

   sortorder is a string, and should be specified in the following syntax:

   "propname1 asc, propname2 desc, ..."

   Some examples of sortorder are as follows:

   "firstname asc, lastname desc"

   "firstname"

   "country, age desc"

   Example:

   To retrieve artists whose state is CA, and sorted by City and FirstName:

   <cfset artistsFromCA = EntityLoad('artist', {state="CA"}, "city asc, firstName")>

   Certain configuration options can be input as name-value pairs as options argument. Several options can be
   specified to control the behavior of entity retrieval.

   * maxResults: Specifies the maximum number of objects to be retrieved.

   * offset: Specifies the start index of the resultset from where it has to start the retrieval.

   * cacheable: Whether the result of this query is to be cached in the secondary cache. Default is false.

   * cachename: Name of the cache in secondary cache.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    580
ColdFusion ORM




    * timeout: Specifies the timeout value (in seconds) for the query.

   Maxresults and timeout are used for pagination.

   Example

   To load first 5 artists whose state is "CA" that are sorted on the firstName.

   <cfset artists = EntityLoad("Artist",{state='CA"}, "FirstName", {maxResults=5}>

* EntityLoadByExample(sampleentity [,unique])

   Loads and returns an array of objects that match the sampleentity. The filter criteria is constructed by ANDing
   all the non-null properties of the sampleentity.For example, to retrieve an array of objects matching the specified
   values:

   <cfset artist= CreateObject("component", "artist")>
   <cfset artist.setState("CA")>
   <cfset artist.setCity("Berkeley")>
   <cfset artist=EntityLoadByExample(artist)>

   If you are sure that only one record exists that matches the specified filtercriteria, you can specify
   unique=true so that a single entity is returned instead of an array. If unique=true and multiple records are
   returned, then an exception occurs.

* EntityReload(entity)

   Reloads data for an entity that is already loaded in this session. This method refetches data from the database and
   repopulates the entity.


Delete objects
EntityDelete

This method deletes the record from the database for the specified entity. Depending on the cascade attribute specified
in the mapping, it also deletes the associated objects.


Syntax

EntityDelete(entity)


Example
For example, to delete an Artist with ArtistID 5:

<cfset artist = EntityLoad('artist', 5, true)>
<cfset EntityDelete(artist)>


Convert object to query
EntitytoQueryThis method converts the input entity object or the input array of entity objects to a query object. The
name of the properties are used as the query column names. Use the optional parameter Entity_name to return the
query of the given entity in the case of inheritance mapping. All the objects in the input array should be of the same
type. Relationship properties are not be included in the result query.


Syntax
EntitytoQuery (orm_object, [entity_name])
EntitytoQuery (orm_object_array, [entity_name])




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         581
ColdFusion ORM




Example 1
<cfset artists = EntityLoad("Artist")>
<cfset artistQuery = EntityToQuery(artists)>


Example 2
<cfset creditCardPayments = EntityLoad("CreditCardPayment")>
<cfset paymentQuery = EntityToQuery(creditCardPayments, "payment")>


Merge entities

EntityMerge

To attach an entity to the current ORM session you can use the entitymerge function. It copies the state of the given
object onto the persistent object with the same identifier and returns the persistent object.

If there is no persistent instance currently associated with the session, it is loaded. The given instance is not associated
with the session. You need to use the returned object from this session. For details, see EntityMerge in CFML Reference.



Using queries

ColdFusion lets you use HQL (Hibernate Query Language) to run queries directly on the database. If you are familiar
with HQL, you can use it for running complex queries.

In general, use HQL in the following scenarios:

* The query is not specific to a particular object but only to some fields in the object.

* To retrieve some fields of the object without loading the object.

* When you use table joins.

* When you use aggregate functions like min, max, avg, and count.

* To retrieve entities by specifying a filter that needs to use operators other than AND.

For more information on HQL, see

www.hibernate.org/hib_docs/reference/en/html/queryhql.html

The HQL methods return a single or multi-dimensional array of values or entities, based on what the HQL query
returns.

If you are sure that only one record exists that matches this filter criteria, specify unique=true so that a single entity
is returned instead of an array. You can use unique=true to suppress the duplicate records from the query result.

Note: entityname and properties used in HQL are case sensitive.

The following HQL methods are available:

ORMExecuteQuery(hql, [params] [,unique])

ORMExecuteQuery(hql, [,unique] [, queryoptions])

ORMExecuteQuery(hql, params [,unique] [,queryOptions])

ORMExecuteQuery (hql, params, boolean unique, Map queryOptions)




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        582
ColdFusion ORM




ORMExecuteQuery(hql, [,unique] [, queryoptions])
Runs the HQL on the default data source specified for the application. You can specify several options to control the
behavior of retrieval using queryoptions:

* maxResults: Specifies the maximum number of objects to be retrieved.

* offset: Specifies the start index of the resultset from where it has to start the retrieval.

* cacheable: Whether the result of this query is to be cached in the secondary cache. Default is false.

* cachename: Name of the cache in secondary cache.

* timeout: Specifies the timeout value (in seconds) for the query

Maxresults and timeout are used for pagination.

Note: If the query returns an object or an array of objects, the init method of the persistent CFC is called (if available).

Examples

To retrieve an array of artwork objects from the ART table:

<cfset art = ORMExecuteQuery("from ART")>

To retrieve an array of artwork objects that have a price greater than 400 dollars:

<cfset art = ORMExecuteQuery("from ART where price > 400")>

To retrieve an array of artwork objects that have a priceid 100:

<cfset artObj = ORMExecuteQuery("from ART where priceid = 100>

To retrieve an array of objects that contain the first name of artists:

<cfset firstNameArray = ORMExecuteQuery("select FirstName from Artist")>

To retrieve the number of artwork objects:

<cfset numberOfArts = ORMExecuteQuery("select count(*) from Art")>

To retrieve an array of objects that have an artistid 1:

<cfset firstName = ORMExecuteQuery("select FirstName from Artist where ARTISTID = 1", true)>

To retrieve an array of ten artist objects starting from the fifth row in the query result:

<cfset artists = ORMExecuteQuery("from Artist", false, {offset=5, maxresults=10, timeout=5})>


ORMExecuteQuery(hql, params [,unique] [,queryOptions])
This type of ORMExecuteQuery lets you pass unnamed parameters to the query. Use '?' (question mark) as the place-
holder for the parameters. The values to the parameters should be passed as an array to params.

Examples: unnamed parameters

To retrieve an array of artist objects with artistid equal to 40:

<cfset artists = ORMExecuteQuery("from ARTIST where artistid > ?", [40])>

To retrieve an array of artwork objects with a priceid equal to 1:

<cfset artObj = ORMExecuteQuery("from ART where priceid=?", [1], true)>

To retrieve an array of objects with a price id equal to 40, and price lesser than 80 dollars:

<cfset artists = ORMExecuteQuery("from ART where priceid > ? and price < ?", [40, 80])>




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       583
ColdFusion ORM




Note: In case of more than one parameter, values are picked up based on the parameter sequence, for example, the first
parameter will be replaced by first value and second parameter will be replaced by second value.

Examples: named parameters

This type of ORMExecuteQuery lets you pass named parameters to the query. The placeholder for the parameter should
be a name and should start with ":" as in ":age" or ":id". The values to the names should be passed as key-value pairs.

For example, to retrieve artist details of all artists whose reside in USA and are also citizens of USA, your code should
look like this:

<cfset USArtists = ORMExecuteQuery("from ARTIST where country=:country and
citizenship=:country", {country='USA'})>
<cfset orderDetail = ORMExecuteQuery("from Orders where OrderID=:orderid and
ProductID=:productid", {orderid=1, productid=901}, true)>

Note: Parameters are not case-sensitive.

Examples: group by

This type of ORMExecuteQuery lets you retrieve aggregate or grouped values for the query.

For example, to retrieve the first name and last name along with the status of the artwork being sold or not, you can
write a query similar to the following:

<cfset artist = ORMExecuteQuery(
                "SELECT art.Artist.Firstname, art.Artist.Lastname, SUM(art.Price) as Sold FROM
Art as art WHERE art.IsSold=1 GROUP BY art.Artist.Firstname, art.Artist.Lastname")>
     <cfloop array="#artist#" index="artistItem">
     <cfoutput>
     #artistItem[1]# #artistItem[2]# #artistItem[3]#<br>
     </cfoutput>
</cfloop>

Note: Built-in functions to obtain the data such as getFirstName() or getLastName() cannot be used if you are using select
queries with specific column names. The result will be returned as an array object and values can be retrieved using array
index.

Example: order by

This type of ORMExecuteQuery lets you retrieve sorted data from a data source using the order by clause. For example,
to sort the data from the Artist table by firstname, use the following code:

<cfset artist = ORMExecuteQuery('FROM Artist ORDER BY firstname ASC', false, {maxresults=5} )>
<cfloop array="#artist#" index="artistObj">
     <cfoutput>Name = #artistObj.getFirstName()#
#artistObj.getLastName()#<br></cfoutput>
<br>
</cfloop>

Example: aggregate functions

This type of ORMExecuteQuery lets you retrieve data when using aggregate functions such as sum, count, avg.

<cfset artist = ORMExecuteQuery(
                "SELECT COUNT(*) FROM Art as art WHERE art.Artist.ArtistID=:ArtistID AND
art.IsSold=:Sold", { ArtistID=1, Sold=True }, True )>
<cfoutput>
     #artist#
</cfoutput><br>




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          584
ColdFusion ORM




Example: expressions

This type of ORMExecuteQuery lets you retrieve data using expressions such as mathematical operators, logical
operators, binary comparisons, and many others.

For example, the following code is used to retrieve the price of an artwork, which is greater than or equal to 10000 along
with the name and description of the artwork.

<cfset artArr = ORMExecuteQuery("from Art where price>=10000")>
<cfloop array="#artArr#" index="artObj">
     <cfoutput>
           Art Name = #artObj.getArtName()#<br>
           Description = #artObj.getDescription()#<br>
           Price = #artObj.getPrice()#<br>
     </cfoutput>
<br>
</cfloop>




Transaction and concurrency

When ORM methods are invoked without any transaction, all the data is committed to the database when the ORM
session is flushed. ORM session is flushed when ORMFlush() is called or if autoflush is enabled when the request ends.

This works fine when there is not much concurrency, however in most practical scenarios you would need to use
transaction in your application so that the data in your database is always in a consistent state.

With ColdFusion ORM, you can manage transactions in the following two ways:

* Using Hibernate transaction: User has full control and ColdFusion does not intervene. The application has to
   flush/close the session and commit/rollback the transaction.

   For more information on transactions, go to the following URL:

   http://community.jboss.org/wiki/sessionsandtransactions

* Using CFTransaction: ColdFusion manages the transaction. Since a transaction cannot be distributed (across
   different data sources), application must ensure that the changes made in the transaction affect only one Hibernate
   session. That is, only one data source.

   ColdFusion allows reading of data from other sessions (data source) in a transaction but changes must be made in
   only one session. Multiple dirty sessions at any time in the transaction can result in exceptions and the transaction
   is rolled back. Before transaction begins, all existing sessions in the request are flushed. The previous session (if any)
   is reused.

   When the transaction is committed, the dirty session is automatically flushed (before committing the transaction).
   When the transaction is rolled back, the changed session cannot be used any longer because it can cause rolled back
   data to get committed later. Therefore, the session participating in the transaction is cleared when transaction is
   rolled back.

A description of transaction is beyond the scope of this document. For more information on transactions, see the
hibernate documentation.

To run the ORM methods inside a transaction, they must be inside <cftransaction>. A simple example snippet of
using ORM with <cftransaction> is as follows:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                       585
ColdFusion ORM




<cftransaction>
      <cfset acct1 = EntityLoad("Account", "101")>
      <cfset acct2 = EntityLoad("Account", "102")>
      <cfset acct1.debit(1000)>
      <cfset acct2.credit(1000)>
      <cfset EntitySave(acct1)>
      <cfset EntitySave(acct2)>
</cftransaction>

Because we have not called commit on the <cftransaction> specifically, it is automatically committed when the
<cftransaction> ends.

All <cftransaction> semantics including savepoint, multiple rollbacks, multiple commits, and nested transactions
work with ORM. You can also have both queries and ORM in the same <cftransaction>.

When <cftransaction> begins, any existing ORM session is flushed and closed, and a new ORM session is created.
The <cftransaction> can be committed or rolled back using appropriate ColdFusion tags in <cftransaction>.
When the transaction ends and has not been committed or rolled back explicitly, it is automatically committed and
the ORM session is closed. If there is any error inside the transaction, without any exception handling, the transaction
is rolled back.

For more details on <cftransaction>, see the CFML Reference Guide.

Note: Even if ORMFlush() is called explicitly inside a <cftransaction> tag, the SQL runs but the data is committed only
when the transaction commits.


Change of behavior in ColdFusion 9 Update 1

 When..                              Behavior in ColdFusion 9                              Changed behavior

 When the transaction starts         The existing session closes and a new session starts. The existing session is flushed and is reused.

 When the transaction is committed   The existing session is flushed and closed.           The existing session is flushed.

 When the transaction is rolled back The existing session is closed without flushing.      The existing session is cleared.



Optimistic locking
A <cftransaction> could prevent scalability in highly concurrent applications because it locks a database for a
transaction that could run for a longer time. Also, a transaction cannot run beyond the duration of a request. There
can be scenarios where an object is loaded in one request and the same instance is updated in another request or by
another application. In this scenario, the application needs to maintain the transaction semantics and prevent the
update if the row has been modified by some other request. This can be achieved by using optimistic concurrency
control, which allows high concurrency in your application along with high scalability.

Optimistic concurrency control uses either number-based or timestamp-based versioning approach. In a number-
based approach, a version number is incremented and for the timestamp approach, a timestamp is set to the current
time whenever the object is modified. It must be noted that version increment or timestamp updation is managed by
Hibernate and is not triggered at the database level.

* Using version: To use optimistic concurrency control using version numbers, add a property with
    fieldtype='version' in your CFC.

    For example:




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        586
ColdFusion ORM




   /**
   * @persistent
   * @table Users
   */
   component{
        property name="id" fieldtype="id" datatype="int" generator="native";
        property string fname;
        property string lname;
        property name="version" fieldtype="version" datatype="int" ;
   }

   Whenever a user object is updated, its version number is automatically incremented. The version number is used
   in the SQL update statement in such a way that updating proceeds only when the version number has not been
   changed by some other request or some other application.

   In case updating fails because the version number was changed outside the current session, an error is thrown
   specifying that the session contained stale data.

* Using timestamp: To use optimistic concurrency control using timestamp, add a property with
   fieldtype="timestamp" in your CFC.

   For example:

   /**
   * @persistent
   * @table Users
   */
   component{
        property name="id" fieldtype="id" datatype="int" generator="native";
        property string fname;
        property string lname;
        property name="lastModified" fieldtype="timestamp";
   }

   Whenever a user object is updated, its timestamp is automatically set to the current time. Sometimes this is
   preferred over version because it also tells you when the user object was last modified.

   In case updating fails because the timestamp was changed outside of the current session, an error is thrown
   specifying that the session contained stale data.

If you do not have version or timestamp properties in your object, it is still possible to use optimistic locking, but only
for objects that are retrieved and modified in the same ORM session. For optimistic locking of detached objects
(objects that were loaded in some other request/ORM session), you must use a version number or timestamp.

To use optimistic locking for objects that do not have version or timestamp, you need to set attribute 'optimistic-
lock' on the CFC. This attribute can take the following values:

* all: This means that all the properties are included in the where clause of update query.

* dirty (default): This means that only the modified properties are included in the where clause of the update query.

* version: This means that only the version field is included in the where clause of update query.

* none: This means that none of the properties are included in the where clause, which in effect means that optimistic
   concurrency is disabled for that component.

   Example:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         587
ColdFusion ORM




   /**
   * @persistent
   * @table Users
   * @optimistic-lock all
   */
   component{
         property name="id" fieldtype="id" datatype="int" generator="native";
         property string fname;
         property string lname;
   }

Apart from defining optimistic lock control at the CFC level, you can also define it at the property level using
'optimisticlock' (true|false: default true) attribute.

You can specify optimisticlock=true for a property to acquire optimistic lock when the property is updated. Setting
this attribute determines whether a version increment will occur when the property is dirty.

In case of one-to-many and many-to-many association, if the state of the collection changes, then version of the
corresponding entity is incremented. It is advised that you disable this setting for one-to-many associations.



Performance optimization

Lazy Loading
Optimizing SQL queries enhances the performance of any data-centric application. Some of the common approaches
used to optimize SQL queries are:

* Avoid round trips to the database and fetch all required data for an operation using a single SQL query using Joins.

* Fetch only required data to reduce the load on the database

SQL queries are generated and executed by the underlying ORM engine. Therefore, Hibernate provides various hooks
to optimize SQL. The fetching strategy is one of the most important hooks, which defines the data that to be fetched,
the time of fetching the data, and the way in which it needs to be fetched.

There are four strategies for loading an object and its associations.

* Immediate fetching

* Lazy fetching

* Eager fetching

* Batch fetching

Note: If memory tracking is enabled on a server, it accesses each field of the object to compute its size. As a result, even
lazy fields are accessed causing the lazy fields to get loaded immediately.


Immediate fetching
In this strategy, the associated object is fetched immediately after the owning entity is fetched, either from the database
using a separate SQL query or from the secondary cache. This is not an efficient strategy to use, unless the associated
object is cached in the secondary cache or when separate queries are more efficient than a Join query. You can define
this strategy by setting lazy="false" and fetch="select" for the relationship property definition in the CFC.

<cfproperty name="art" fieldtype="one-to-many" cfc="ART" fkcolumn="ARTISTID" lazy="false"
fetch="select">




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          588
ColdFusion ORM




With this strategy, on loading the artists object, its art object is loaded immediately using a separate SQL query. As a
result, this strategy is extremely vulnerable to 'N+1 Select problem'.


Lazy fetching
In this strategy, the associated object or collection is fetched only when required. Although you need to send a new
request to the database each time you need data, this strategy controls how much of data is loaded and when is it
loaded. This helps in reducing the database load.

When you load an entity, by default, ColdFusion ORM loads the entity's data but relations and any mapped collections
and are not loaded. They are loaded only when you want to load them by calling the getter method. Therefore, the
relations and collection mappings are lazily loaded. For example, when the artist object is loaded, all its artworks are
not loaded and they are loaded only when getarts() is called.

ColdFusion ORM provides three types of lazy loading for relationships:

* lazy: This is the default lazy loading that applies to collection mapping, one-to-many and many-to-many
   relationship. In this case, when you call the accessor for the collection/relation, the collection is fully loaded. So,
   when you call EntityLoad() for a particular artist, its artworks are not loaded at that time. When you call
   artist.getarts(), all the art object belonging to the artist will get loaded. This is achieved by setting lazy="true" on
   the relationship property definition in the CFC.

   Example:

   In artist.cfc

   <cfproperty name="art" fieldtype="one-to-many" cfc="ART" fkcolumn="artistId" lazy="true">

* Extra lazy: This applies to one-to-many and many-to-many relationships. This type of lazy loading goes one step
   ahead of lazy and does not load all the associated objects when the accessor for that relation is called. It just loads
   the primary keys for those objects and keeps a proxy object for them. When you call any method on the wrapper
   object, that object's data is loaded from the database.

   For example, when you call artist.getarts(), it executes a query on the database to fetch the primary key of the
   related artwork objects and creates a proxy artwork object. So, you do not load the data for all the artwork objects
   in memory. When you access a particular artwork object and invoke any method on it, then it fires another query
   to the database to load that particular artwork object. This is achieved by setting lazy="extra" on the relationship
   property definition in the CFC.

   Example:

   In artist.cfc

   <cfproperty name="art" fieldtype="one-to-many" cfc="art" fkcolumn="artistId" lazy="extra" >

* proxy: This applies to one-to-one and many-to-one relationships. When the owner object is loaded, the related
   object is not loaded from the database. ColdFusion only creates a proxy object for the related object and when any
   method is invoked on the related object, the data for the proxy object is loaded from the database and populated in
   the proxy object.

   For example, if the art-artist table relation is lazy, when the art object is loaded, the artists object is not loaded and
   when you call art.getartist(), you would only get a proxy object. When you call any method on the proxy
   object, query gets executed on the database to load artist object's data. This is achieved by setting lazy="true" on
   the relationship property definition in the CFC

   Example:

   In ART.cfc




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      589
ColdFusion ORM




   <cfproperty name="artist" fieldtype="many-to-one" cfc="artist" fkcolumn="artistId"
   lazy="true">

   Important: An entity is loaded only once in the request (in Hibernate session) and there is always only one copy of it
   in the request. So, for artwork and artist relationship, which is lazy, if the artist is already loaded, calling
   art.getartist() will not create a proxy object and will return the loaded artist object.

Lazy loading can be disabled by setting lazy="false" on the relationship property definition in the CFC.

Choosing an appropriate lazy loading option is very important for the performance of your application. Extra lazy
means more number of trips to the database (each trip to the database is expensive) but less data in memory whereas
no lazy loading means a huge object graph in the memory. So, you need to balance the approach based on the
application need.


Eager fetching
In this strategy, the associated object or collection is fetched together with the owning entity using a single SQL Join
query. This strategy reduces the number of trips to the database and is a good optimization technique when you always
access the associated object immediately after loading the owning entity. You can define this strategy by setting
fetch="join" for the relationship property definition in the CFC.


Batch fetching
This strategy tells Hibernate to optimize the second SQL select in Immediate fetching or lazy fetching to load batch of
objects or collections in a single query. This allows you to load a batch of proxied objects or uninitilized collections
that are referenced in the current request. This is generally useful in nested tree loading. You can specify this using
"batchsize" attribute for CFC or relationship property.

There are two ways you can tune batch fetching:

* Batch fetching at CFC level: This allows batch fetching of the proxied objects and is applied to one-to-one and
   many-to-one relationship. For example, consider artwork and artist example where there are 25 art instances
   loaded in the request (ORM session). Each artwork has a reference to the artist and the relationship is lazy.
   Therefore, art objects contain the proxied object for artist. If you now iterate through all the art objects and call
   getartist() on each, by default 25 SELECT statements are executed to retrieve the proxied owners, one for each
   artist proxy object. This can be batched by specifying the 'batchsize' attribute on the artist CFC:

   <cfcomponent table="artist" batchsize="10" ...>

   When you call getartist() on the first art object, it batch fetches 10 artist objects that are proxied in the current
   request.

   So for 25 art objects, this type of batch fetching makes Hibernate execute a maximum of three queries in batches of
   10, 10, and 5.

* Batch fetching at collections: This allows batch fetching of value collections, one-to-many or many-to-many
   relationships that are uninitialized. For example, consider artist-art one-to-many relationship where there are 25
   artists loaded and each artist has a lazy collection of artworks. If you now iterate through the artists and call
   getarts() on each, by default 25 SELECT statements are executed, one for each artist to load its art objects. This
   can be optimized by enabling batch fetching, which is done by specifying "batchsize" on the relationship
   property:

   Example:

   In artist.cfc:

   <cfproperty name="art" fieldtype="one-to-many" cfc="art" fkcolumn="artistId" lazy="true"
   batchsize="10">




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       590
ColdFusion ORM




   One important thing to understand here is that batchsize here does not mean that 10 artworks are loaded at one
   time for a artist. It actually means that 10 artwork collections (artworks for 10 artists) are loaded together.

   When you call getarts() on the first artist, artworks for 9 other artists are also fetched along with the one that was
   asked for.

   The value for batchsize attribute should be chosen based on the expected number of proxied objects or
   uninitialized collections in the session.


Caching
Caching is extensively used for optimizing database applications and effectively reducing traffic between the database
and the application.

ColdFusion ORM supports two levels of caching:

* Session level

* Secondary level


Session level cache
Objects that are loaded from the database are always cached in the ORM Session as long as the session is open. When
EntityLoad is called to retrieve an object in a session for the first time, ORM fetches the data from the database and
constructs the object. In any subsequent call to load the same object in the same session, ORM fetches the object from
the session cache. To forcefully retrieve the object from the database, EntityReload should be called on the object.

For more details on ORM Sessions and its lifecycle, see "ORM session management" on page 595 and "Architecture"
on page 537.


Secondary level cache
ColdFusion provides the ability to store the data that is retrieved from the database in secondary cache. The contents
in secondary cache live longer than the life-time of a session. It can also be the life-time of the process or in-definite
(disk-caching), depending on the ability of the secondary cache provider. The cache can also be used in a distributed
environment depending on the ability of the secondary cache provider.

An important difference between session level cache and secondary level cache is that the session level caches the whole
object but the secondary level caches only the data.

Secondary level cache can be leveraged by using an external cache provider with ColdFusion ORM. EHCache,
JBossCache, OSCache, SwarmCache, and Tangosol Coherence Cache are some popular secondary cache providers,
which can be plugged into Hibernate.

ColdFusion uses EHCache as the default secondary cache provider. EHCache is a distributed caching solution that
supports memory and disk-based caching. EHCache can be configured using a configuration file. Different cache
regions can be defined in the configuration file. Each cache region has its own configuration that specifies details
including the number of elements it can store, eviction policy, time to live (ttl), and idle time.

ehcache.xml is available in the following location: CF_root\lib\. For details of the properties in the ehcache.xml, refer
to the documentation available at the following URL:

http://ehcache.org/

The following is a sample EHCache configuration file (ehcache.xml):




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    591
ColdFusion ORM




<ehcache>
     <diskStore path="java.io.tmpdir"/>
     <defaultCache
           maxElementsInMemory="10000"
           eternal="false"
           timeToIdleSeconds="120"
           timeToLiveSeconds="120"
           overflowToDisk="true"
           diskPersistent="false"
           diskExpiryThreadIntervalSeconds="120"
           memoryStoreEvictionPolicy="LRU"
           />
     <cache name="Artist"
           maxElementsInMemory="20"
           eternal="true"
           overflowToDisk="false"
           />
</ehcache>


Modifications to ehcache.xml in ColdFusion 9.0.1
ehCache.xml includes the following configuration properties:

* diskSpoolBufferSizeMB: Size to allocate the DiskStore for a spool buffer.

   The default size is 30 MB. Each spool buffer is used only by its cache.

   Turning on trace-level logging shows if backup for cache created/updated using action="put" occurs in the
   diskstore.

* clearOnFlush: Determines if the MemoryStore must be cleared when the cache is flushed. By default, the
   MemoryStore is cleared.

* diskExpiryThreadIntervalSeconds: The number of seconds between runs of the disk expiry thread. The default
   value is 120 seconds.

Note: The functions cacheGetProperties and cacheSetProperties can be used to get/set these properties.


Use secondary cache
To use secondary cache, you must configure the following settings in the application:

* ormsettings.secondarycacheenabled

   This setting defines whether the secondary cache would be used by the application. By default, this is set to false.

* ormsettings.Cacheprovider

   This setting defines the cache provider that needs to be used for secondary cache. This defaults to EHCache. The
   other values for this setting are JBossCache, OSCache, SwarmCache and Hashtable. You can also specify the fully
   qualified class name of the cache provider.

* ormsettings.cacheconfig

   This setting defines the configuration file required by the secondary cache provider. For example, EHCache
   requires EHCache.xml that defines the configuration settings for the secondary cache. Specify the path to the XML
   file in this setting. If this setting is not defined, cache provider uses its default configuration.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          592
ColdFusion ORM




After you have configured the secondary cache, it is critical to identify the objects in your application that can be
cached because the data cached by secondary cache is shared by all the sessions of an application. Typically, caching
should be enabled for a CFC that represents:

* Data that changes rarely

* Data that is local to an application and is not modified by other applications

* Non-critical data

For each type of object that needs to be cached, you also need to decide the access strategies. ORM provides the
following cache strategies that you can use for your objects:

* read-only

   This strategy is useful for data that is read frequently but never updated. This is the best performing cache strategy.

* nonrestrict-read-write

   This strategy is useful for data that is updated occasionally. Typically, it is very unlikely that two transactions would
   update the same object simultaneously.

* read-write

   This strategy may be appropriate if your data needs to be updated. It carries more overhead than the two preceding
   strategies.

* Transactional

   This strategy provides the support for transactional cache. It can only be used if the cache provider is transaction
   aware.

Support for these strategies depend on the cache provider. Not all the cache providers support all the cache strategies.
For more information on these strategies, see:

www.hibernate.org/hib_docs/reference/en/html/performance-cache.html

The secondary cache can cache the following types of data.

* Persistent object data

* Persistent object association

* Query data


Cache data of a persistent object
In this case, the data of the persistent object is cached. It will not cache the associations or associated object's data. To
enable this flag on a persistent CFC, specify the following attributes on the component.

* cacheuse: Defines the caching strategy.

* cachename: Defines the name of the cache region to be used by the secondary cache provider. If you do not specify
   a region name for the component, the entity name of the component is considered as the cache name. In case a
   region is not specified in the configuration file, a region is automatically created with the default configuration.

For example:

<cfcomponent persistent="true" schema="APP" table="Artists" cachename="artist"
cacheuse="read-only">




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      593
ColdFusion ORM




Cache the association data of a persistent object
In this case, the primary key of the associated objects are cached. It does not cache the objects loaded as part of the
association unless caching is enabled for those objects. To cache an association, specify the following attributes on the
association property.

* cacheuse: Defines the caching strategy.

* cachename: Defines the name of the cache region to be used by the secondary cache provider. If you do not specify
    a region name for the association property, the <comoponent_name>.<property_name> is considered as the cache
    name. In case a region is not specified in the configuration file, a region is automatically created with the default
    configuration.

For example:

<cfproperty name="art" fieldtype="one-to-many" cfc="CArt" fkcolumn="ArtID"
cachename="ArtistArts" cacheuse="read-only">


Cache query data
In this case, the results of queries that are executed by ORMExecuteQuery() or EntityLoad() methods are cached in
the secondary cache. To enable caching query data, pass "cacheable=true" and "cachename='cachename' values in
the options struct of the methods. If you do not specify the cachename, the query is cached in the default query cache.
It is recommended that you to specify the cachename so that you can control eviction.

For example:

availableArts = ORMExecuteQuery("from CArt where issold=0", {}, false, {cacheable=true,
cachename="availableArtsQuery"});


Secondary cache example using EHCache
Step 1: Set the following in Application.cfc:

<cfset this.name="Caching_Example">
<cfset this.datasource="cfartgallery">
<cfset this.ormenabled="true">
<cfset this.ormsettings.secondarycacheEnabled=true>
<cfset this.ormsettings.cacheProvider= "ehcache">
<cfset this.ormsettings.cacheConfig="ehcache.xml">

Step 2: Define the cache settings in the CFCs.

CArtist.cfc

<cfcomponent persistent="true" schema="APP" table="Artists" cachename="artist"
cacheuse="read-only">
      <cfproperty name="artistid" fieldtype="id"/>
      <cfproperty name="firstname"/>
      <cfproperty name="lastname"/>
      <cfproperty name="state"/>
      <cfproperty name="art" fieldtype="one-to-many" cfc="CArt" fkcolumn="ArtID"
cachename="ArtistArts" cacheuse="read-only">
</cfcomponent>

CArt.cfc

<cfcomponent persistent="true" schema="APP" table="Art">
      <cfproperty name="artid" generator="identity" fieldtype="id"/>
      <cfproperty name="artname"/>
      <cfproperty name="issold"/>
</cfcomponent>



                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       594
ColdFusion ORM




Step 3:

<cfscript>
//This will cache the Artist Component and also the association.                      It wouldn't cache the Art
objects.
artistObj = EntityLoad("CArtists", 3, true);
//This will cache the query.
availableArts = ORMExecuteQuery("from CArt where issold=0", {}, false, {cacheable=true,
cachename="availableArtsCache"});
</cfscript>


Evict content from secondary cache
ColdFusion provides the following methods to evict contents from the secondary cache.

ORMEvictEntity("<component_name>", [primarykey])

This method is used to evict items for the given component name, from the secondary cache. If the primary key is
specified, then the data of the entity with that primary key is evicted. Primary key should be a value in case of simple
primary key or should be a struct in case of composite primary key.

Example:

<cfset ORMEvictEntity("CArtists")>

Evicts all the cache data of CArtist entity.

<cfset ORMEvictEntity("CArtists", 1)>

Evicts the cache data of CArtists entity whose primary key is 1.

ORMEvictCollection("<component_name>", "<collection_name>", [primarykey])

This method is used to evict all the collection/association data for the given component name and collection name,
from the secondary cache. If the primary key is specified, then, the collection or association data of the entity with the
primary key is evicted.

Example:

<cfset ORMEvictCollection("CArtists", "art")>

Evicts all the association or collection data of collection art belonging to the component CArtists.

<cfset ORMEvictCollection("CArtists", "art", 1)>

Evict the association or collection data of collection art belonging to the component CArtists with primary key 1.

ORMEvictQueries([cachename])

This method is used to evict the data of all the queries from the default query cache. If cache name is specified, then,
the data of all the queries belonging to the cache region with the given cache name are evicted. Example:

<cfset ORMEvictQueries()>

Evicts the data of all the queries from the default query cache.

<cfset ORMEvictQueries("availableArtsCache")>

Evicts the data of all the queries from the cache region with the name availableArtsCache.


Support for user-defined caches in ColdFusion 9.0.1
Except in cacheSetProperties and cacheGetProperties, user-defined caches are supported in all caching
functions.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         595
ColdFusion ORM




Edit ehCache.xml (cfroot/lib)to set the properties for user-defined caches as shown in the following example:

<!--- item to put in user-defined cache --->
<cfset currentTime = Now()>
<!--- put item in user-defined cache --->
<cfset timeToLive=createtimespan(0,0,0,30)>
<cfset timeToIdle=createtimespan(0,0,0,30)>
<cfset customCache = "usercache">
<cfset id = "cache1">
<cfset cachePut(id,currentTime,timeToLive,timeToIdle,customCache)>
<!-- list items in the cache --->
List Items in cache:
<cfset cacheIds = cacheGetAllIds(customCache)>
<cfdump var="#cacheIds#"><br>
<!--- print cache data --->
<cfset cachedData = cacheGet(id,customCache)>
<cfoutput>#cachedData#</cfoutput>
<!--- print cache metadata --->
Cache metadata:
<cfset mdata = cacheGetMetadata(id,"object",customCache)>
<cfdump var="#mdata#">
<!--- clear user-defined cache --->
<cfset cacheRemove(ArrayToList(cacheIds),true,customCache)>




ORM session management

Hibernate Session is a thread-safe and short-lived object that represents a conversation between the application and
the persistence layer. This is required to perform CRUD operations (Create, Delete, Update, Retrieval) on a persistent
entity in Hibernate. For ColdFusion applications that use ORM, this session is automatically managed by ColdFusion.
Hibernate sessions also act as the first level of cache, which ensures that only one copy of an object exists in the session.

When CRUD operations are performed in the session, data of the entity is not synchronized with the database
immediately. That is, the SQL statements for the operations are not issued immediately and they are queued. The data
is synchronized with the database when the session is flushed. When the session is flushed, the SQL operations are
performed in the following order:

1 all entity insertions, in the same order the corresponding objects were saved using EntitySave()

2 all entity updates

3 all collection deletions

4 all collection element deletions, updates, and insertions

5 all collection insertions

6 all entity deletions, in the same order the corresponding objects were deleted using EntityDelete()

The only exception to this is that objects with nativeId generation are inserted immediately when the object is saved.

Note: ColdFusion creates and manages Hibernate sessions only if ormenabled is set to true in application scope.

When the ColdFusion application starts, it builds the Hibernate session factory that is available for the application life
time. This factory is used to create Hibernate sessions that manage the persistent object lifecycle. When the first CRUD
method is called, a Hibernate session gets created and is closed when the request ends or when the
ormclosesessionmethod is called. For details on how ColdFusion works with Hibernate, see the "Architecture" on
page 537.



                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      596
ColdFusion ORM




In multiple data source scenarios supported in ColdFusion 9 Update 1, there are multiple sessions (one for each data
source) in the same request. For all entity functions, the appropriate sessions are used transparently.

ColdFusion exposes a few methods to let CFML developers work with the Hibernate sessions directly. ORM session-
related functions also take optional data source argument. If you do not specify a data source, the default data source
specified for ORM is used. The methods are as follows:


ORMGetSession()
Returns the current ORM session. That is, the Hibernate Session associated with the data source specified in the
application. This returns the underlying Hibernate Session that can be used to call the API, which otherwise is not
exposed by ColdFusion.

For information on Session API, see www.hibernate.org/hib_docs/v3/api/org/hibernate/Session.html.


ORMCloseSession()
Closes the current ORM session associated with the data source specified in the application.


ORMFlush()
Flushes the current ORM session associated with the data source specified in the application. ORMFlush flushes all the
pending CRUD operations in that request.


ORMClearSession()
ORMClearSession removes all the entities that are loaded or created in the session. This clears the first level cache and
removes the objects that are not yet saved to the database.


ORMGetSessionFactory()
ORMGetSessionFactory returns the underlying Hibernate SessionFactory object. For information on Session API,
see www.hibernate.org/hib_docs/v3/api/org/hibernate/SessionFactory.html



Event Handling in CFC

ORM provides callbacks to the event listeners for all the persistence events like Load, Insert, Update, Delete. These
events can be used for data validations or transformation or for some generic functions such as auditing. These events
can be handled at two levels in ColdFusion ORM:

* In a persistent CFC

* Using an event handler CFC

To enable event handling for an application, define the following setting: ormsettings.eventhandling= "true"

By default, this flag is disabled. If you do not specify this flag while the event handler CFC is defined, the flag is
considered as enabled.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     597
ColdFusion ORM




Event handling in a persistent CFC
A persistent CFC can have various methods and if these methods are present, callbacks can be sent on those events to
the CFC. The CFC can then handle these events. In this case, the event for entity persistence comes to the CFC that the
system loads, inserts, updates, or deletes. These methods are:

* preLoad(): This method is called before the load operation or before the data is loaded from the database.

* postLoad(): This method is called after the load operation is complete.

* preInsert(): This method is called just before the object is inserted.

* postInsert(): This method is called after the insert operation is complete.

* preUpdate(Struct oldData): This method is called just before the object is updated. A struct of old data is passed
   to this method to know the original state of the entity being updated.

* postUpdate(): This method is called after the update operation is complete.

   Note: When you call the EntitySave() method on an object that is not loaded using EntityLoad(), it gets updated but
   the intercepter call fails. This happens because an empty map is created for the object and there is no previous data
   associated with it.

* preDelete(): This method is called before the object is deleted.

* postDelete(): This method is called after the delete operation is complete.


Event handling using an event handler CFC
An application-wide event handler CFC can be defined to handle callback when *any* entity is inserted, updated,
deleted, or retrieved. This CFC must be configured at the application level as an ORM setting:

ormsettings.evenHandler="X.Y.EventHandler"

The event handler CFC needs to implement the CFIDE.ORM.IEventHandler interface. This CFC gets the callbacks
from all persistence-related events and handles them accordingly. In this case, a single CFC handles the events for all
the CFCs.

This interface contains the following methods for the event handler CFC:

For application-wide event handler CFC, you need to specify the component name also along with other arguments.

The methods for application-wide event handler are:

* preLoad(entity): This method is called before the load operation or before the data is loaded from the database.

* postLoad(entity): This method is called after the load operation is complete.

* preInsert(entity): This method is called just before the object is inserted.

* postInsert(entity): This method is called after the insert operation is complete.

* preUpdate(entity, Struct oldData): This method is called just before the object is updated. A struct of old
   data is passed to this method to know the original state of the entity being updated.

   Note: When you call the EntitySave() method on an object that is not loaded using EntityLoad(), it gets updated but
   the intercepter call fails. This happens because an empty map is created for the object and there is no previous data
   associated with it.

* postUpdate(entity): This method is called after the update operation is complete.

* preDelete(entity): This method is called before the object is deleted.

* postDelete(entity): This method is called after the delete operation is complete.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       598
ColdFusion ORM




Note: If event handlers are defined in both persistent CFC and event handler CFC, the persistent CFC is given the callback
before calling the application wide event handler.



Autogenerating database schema

ColdFusion automatically creates tables when ORM is initialized for the application. For auto-generating tables, do
the following:

In the THIS scope of Application.cfc, in ormsettings struct, set the dbCreate property to one of the following values:

* update: Creates the table (if it does not exist) or updates the table (if it exists).

* dropcreate: Drops the table if it exists and then creates it.

For example,

<cfset this.ormsettings.dbCreate="update">

Certain specific attributes (DDL-only attributes) defined for the tags cfcomponent and cfproperty can be use to
define various attributes for the auto-generated tables and columns. DDL-only attributes are used only for DDL
generation. For details of these attributes, see the table in the section DDL-only attributes in "Column" on page 549.


Examples
Application.cfc

<cfset this.name = "AG"/>
<cfset this.ormenabled=true/>
<cfset this.datasource = "ORM_DDL">
<cfset this.ormsettings.dbCreate="dropcreate">
<cfset this.ormsettings.sqlscript="mysqlscript.sql">

Artists.cfc

<cfcomponent persistent="true" table="Artists">
        <cfproperty name="artistid" fieldtype="id" ormtype="integer" length=10>
        <cfproperty name="firstname" ormtype="string" length="20" notnull="true">
        <cfproperty name="lastname" ormtype="string" length="20" notnull="true">
        <cfproperty name="address"            ormtype="string" length="50">
        <cfproperty name="city" ormtype="string" length="20">
        <cfproperty name="state" ormtype="string" length="2">
        <cfproperty name="postalcode" ormtype="string" length="10">
        <cfproperty name="email" ormtype="string" length="50" unique="true">
        <cfproperty name="phone" ormtype="string" length="20">
        <cfproperty name="fax" ormtype="string" length="12">
        <cfproperty name="thepassword" ormtype="string" length="20">
</cfcomponent>

art.cfc




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        599
ColdFusion ORM




<cfcomponent persistent="true" table="Art">
       <cfproperty name="artid" generator="identity" fieldtype="id">
       <cfproperty name="artname" ormtype="string" length="50">
       <cfproperty name="price" ormtype="double">
       <cfproperty name="largeimage" ormtype="string" length="30">
       <cfproperty name="mediaid" ormtype="integer" length="10">
       <cfproperty name="issold" ormtype="boolean" dbdefault=1>
       <cfproperty name="artist" fkcolumn="artistid" fieldtype="many-to-one" cfc="CArtists">
</cfcomponent>



Naming strategy
When you build a database centric application, typically you would follow some database standard and naming
convention. ColdFusion ORM allows you to define this convention at one central place for the application using the
'naming strategy'.

The advantage of using a naming strategy is that you do not need to change the code throughout your application. The
naming strategy specifies how the table and column have to be named for a CFC and its properties.

Naming strategy takes "logical name" for a table or column and returns the actual table or column name that should
be used.

* Logical table name: This is the table name specified for the CFC. If it is not specified, the entity name is taken as the
   logical table name. If the entity name is also not specified, the unqualified CFC name, for example, Person for
   a.b.c.Person, is taken as the logical table name.

* Logical column name: This is the column name specified for a CFC property. If it is not specified, the property
   name is taken as the logical column name.

Naming strategy is applied to an application by setting the following in Application.cfc

<cfset this.ormsettings.namingstrategy="strategy">

The value of strategy could be:

* default: This strategy uses the logical table or column name as it is. ColdFusion ORM using this value as the
   default strategy.

* smart: This strategy changes the logical table or column name to uppercase. Also, if the logical table or column
   name is in camel case, this strategy breaks the camelcased name and separates the broken words using underscore.
   For example, for a CFC named "OrderProduct", this strategy changes the table name as "ORDER_PRODUCT".

* your own cfc : You can get complete control of the naming strategy by providing your own implementation. You
   need to specify the fully qualified name of the CFC as the value for naming strategy. This CFC must implement
   cfide.orm.INamingStrategy interface.

The cfide.orm.INamingStrategy interface is as follows:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                            600
ColdFusion ORM




 /**
* Strategy to specify the table name for a CFC and column name for a property in the cfc.
* This can be used to specify the application specific table and column naming convention.
* This rule will be applied even if the user has specified the table/column name in the mapping
so that
* the name can be changed for any application at one place without changing the names in all the
code.
*/
interface
{
/**
* Defines the table name to be used for a specified table name. The specified table name is either
* the table name specified in the mapping or chosen using the entity name.
*/
public string function getTableName(string tableName);


/**
* Defines the column name to be used for a specified column name. The specified column name is
either
* the column name specified in the mapping or chosen using the proeprty name.
*/
public string function getColumnName(string columnName);
}

This interface is specified in the application using:

this.ormsettings.namingstrategy="com.adobe.UCaseStrategy"

Note: The naming strategy applies to all the table or column names, which you use in the mapping including link table
and fkcolumn, even though there is no CFC or cfproperty associated with them.


Populating the database using script
After the tables are created using DDL, you can optionally populate the database with data using SQL script. To do
this, specify the path (absolute file path or path relative to the application) to the SQL script file that has to be executed.
The script is run only when dbcreate is set to dropcreate. The SQL script file lets you populate the tables before the
application is accessed. Ensure that each SQL statement starts on a new line and ends with a semi-colon.


Example
Mysqlscript.sql

insert into Artists(artistid, firstname, lastname, address, city, state, postalcode, email,
phone, fax, thepassword)
values(1, 'Aiden', 'Donolan', '352 Corporate Ave.', 'Denver', 'CO', '80206-4526',
'aiden.donolan@donolan.com', '555-751-8464', '555-751-8463', 'peapod');
insert into Artists(artistid, firstname, lastname, address, city, state, postalcode, email,
phone, fax, thepassword)
values(2, 'Austin', 'Weber', '25463 Main Street, Suite C', 'Berkeley', 'CA', '94707-4513',
'austin@life.com', '555-513-4318', '510-513-4888', 'nopolyes');
insert into Art(artname, price, largeimage, mediaid, issold, artistid)
values('Michael', 13900, 'aiden02.jpg', 1, 0, 1);
insert into Art(artname, price, largeimage, mediaid, issold, artistid)
values('Space', 9800, 'elecia01.jpg', 2, 1, 2);




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        601
ColdFusion ORM




Support for multiple data sources for ORM

Note: This feature applies only if you have installed ColdFusion 9 Update 1.


Introduction
You can use multiple data sources for ORM in ColdFusion applications. A multiple data source setup is useful in
scenarios where your application has multiple modules that interact with each other.

Hibernate inherently supports single data source for a Hibernate configuration. To support multiple data sources,
ColdFusion builds and manages multiple Hibernate configurations and SessionFactory objects, one for each data
source in the application.


Usage scenario
Consider an application with the following three modules:

* HR

* Finance

* Sales

Assume that all these modules have their own databases (and therefore separate data sources). But at the application-
level, all the three modules have to interact with each other. A single data source makes it impossible to build the entire
application using ORM. Building three separate applications is not advisable as the interaction between the
applications is possible only using web services.

If you use a multiple data source setup for ORM, all the three modules can be built in ORM. They can be part of the
same application and the modules can interact with each other.


Configuring the application to use multiple data sources
Configure Persistent CFCs with the attribute datasource pointing to the appropriate data source. You can specify the
attribute data source on the CFC using the tag cfcomponent or by specifying the annotation on Component in the
CFC definition. If you do not specify a data source, the default data source is used for that CFC.

Since a Hibernate configuration uses a single data source, all related CFCs (using ORM relationships) must have the
same data source.


Example
Art.cfc

<cfcomponent persistent="true" datasource="artgallery" table="Art">
   ...
</cfcomponent>

Author.cfc

<cfcomponent persistent="true" datasource="bookclub" table="author">
   ...
</cfcomponent>




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     602
ColdFusion ORM




ORM settings
The following are the data source-specific ORM settings for which you can specify string or struct values in the
Application.cfc:

* schema

* catalog

* dialect

* dbcreate

* sqlscript

For multiple data sources, a struct can be specified with data source name as the key and the appropriate setting as the
value. If a string value is specified, it applies to the default data source of ORM.


Example 1

<cfset this.ormsettings.dbcreate={artgallery="dropcreate", bookclub="none"}>


Example 2

<cfset this.ormsettings.dbcreate="dropcreate">

If multiple data sources are used for ORM, these settings apply to the default ORM data source.


Mapping using Hibernate mapping files
In multiple data source scenarios, the data source information must be provided in the CFC (and not in .hbmxml file).

Also, all CFCs used in one .hbmxml file must have the same data source.


Example
The following example illustrates two different entities using two different data sources. In this example, art.cfc and
artist.cfc are related and therefore use the same data source.

art.cfc

<cfcomponent persistent="true" table="art" datasource="cfartgallery">
<cfproperty name="ArtID" fieldtype="id" generator="native">
<cfproperty name="ArtName">
<cfproperty name="IsSold">
</cfcomponent>

artists.cfc

<cfcomponent persistent="true" table="artists" datasource="cfartgallery">
<cfproperty name="ArtistID" fieldtype="id">
<cfproperty name="FirstName">
<cfproperty name="LastName">
<cfproperty name="art" fieldtype="one-to-many" cfc="art" fkcolumn="ArtistID">
</cfcomponent>

authors.cfc

<cfcomponent persistent="true" table=authors datasource="cfbookclub">
<cfproperty name="AuthorID" fieldtype="id">
<cfproperty name="LastName">
<cfproperty name="FirstName">
</cfcomponent>




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      603
ColdFusion ORM




index.cfm

<cfoutput>Original Data<br></cfoutput>
<cfset artistObj = EntityLoad("artists", 1, true)>
<cfoutput>#artistObj.getArtistID()# | #artistObj.getFirstName()# |
#artistObj.getLastName()#<br></cfoutput>
<cfset artObj = artistObj.getart()>
<cfoutput>#artObj[1].getartname()# <br></cfoutput>
<cfset authorObj = EntityLoad("authors", 1, true)>
<cfoutput>#authorObj.getFirstName()#</cfoutput>
<cfoutput>#authorObj.getLastName()#</cfoutput>



ORM Function enhancements
Multiple data source support impacts the following ORM functions:


ORMGetSession

Description
Returns the Hibernate session associated with the data source in the request. If ORM is not configured for this data
source, it results in an exception. If data source is not specified, the Hibernate session of the default data source is
returned.

Use this session object to call the APIs, which, otherwise, ColdFusion does not expose.

For information on session APIs, see:

http://docs.jboss.org/hibernate/core/3.3/api/org/hibernate/Session.html


Function syntax

ormgetsession([datasource])


ORMCloseSession

Description
Closes the Hibernate session associated with the data source in the request. If you do not specify a data source, the
Hibernate session associated with the default data source is closed.


Function syntax

ormclosesession([datasource])


ORMCloseAllSessions

Description
Closes all Hibernate sessions in the request.


Function Syntax

ormcloseallsessions()


History
ColdFusion 9 Update 1: Added this function



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    604
ColdFusion ORM




ORMFlush

Description
Flushes the Hibernate session associated with the data source in the request. ORMFlush flushes all pending CRUD
operations in the request. Any changes made in the objects, in the current ORM session, are saved to the database.

If you do not specify the data source, the Hibernate session associated with the default data source is flushed.


Function syntax

ormflush([datasource])


ORMFlushall

Description
Flushes all the current Hibernate sessions in the request.


Function syntax

ormflushall()


History
ColdFusion 9 Update 1: Added this function


ORMClearSession

Description
Clears the Hibernate session associated with the given data source.

The function clears the first level cache and removes the objects that are not yet saved to the database.

If you do not specify the data source, the Hibernate session associated with the default data source is cleared.


Function syntax
Ormclearsession([datasource])


ORMGetSessionFactory

Description
Returns the Hibernate Session Factory object associated with the data source. Results in an error if ORM is not
configured for this data source. If you do not specify the data source, the Hibernate session factory object associated
with the default data source is returned.

For information on Session API, go to the following URL:

http://docs.jboss.org/hibernate/core/3.3/api/org/hibernate/SessionFactory.html


Function syntax

Ormgetsessionfactory([datasource])




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    605
ColdFusion ORM




ORMEvictQueries

Description
This method is used to evict the data of all the queries from the default query cache of the specified data source. If cache
name is specified, then the data of all queries belonging to the cache region with the given cache name are evicted.

If no data source is specified, the default query cache of the default data source is evicted.


Syntax

ORMEvictQueries([cachename])

ORMEvictQueries([cachename], datasource)


Parameter          Description

cachename          Name of the cache region that you want to evict.

datasource         Name of the data source whose cache you want to evict. If you do not specify the cache, the default query cache is
                   evicted.



ORMExecuteQuery

Description
Executes a Hibernate Query Language (HQL) query.

By default, this function works on ORM's default data source. To use this function for another data source, specify the
data source key-value pair within the queryoptions.


Syntax

ORMExecuteQuery(hql, [params] [,unique])

ORMExecuteQuery(hql, [,unique] [, queryoptions])

ORMExecuteQuery(hql, params [,unique] [,queryOptions])


Parameters

Parameter              Description

Hql                    The HQL query that has to be executed.

Params                 Object parameter for the entity.

Unique                 Specifies if the object parameter is unique.

Queryoptions           Key-value pair of options for the query.



Example
<cfset artistArr = ORMExecuteQuery("from Artists where artistid=1", true,
{datasource="cfartgallery"})>
<cfset countArray = ORMExecuteQuery("select count(*) from Authors", [], false,
{datasource="cfbookclub"})>
