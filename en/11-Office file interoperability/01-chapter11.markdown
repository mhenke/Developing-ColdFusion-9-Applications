Chapter 11: Office file interoperability

Adobe ColdFusion provides interfaces to work with PDF, Adobe Flash, and Adobe Connect. ColdFusion 9 now
extends the integration support to OpenOffice and Microsoft Office application formats such as Excel, PowerPoint,
and SharePoint.

Office interoperability supports both OpenOffice and Apache POI libraries (see http://poi.apache.org/ for information
on Apache POI). OpenOffice libraries support conversion of all Office file formats, including Word documents to
PDF. When you use the cfdocument, cfpresentation, or cfspreadsheet tags to convert Office files, the tags first
search for an OpenOffice installation.

If an OpenOffice installation is not found, POI libraries are used. POI libraries support conversion of all office files
except Word documents.

See "Supported Office conversion formats" on page 893 for the complete list of supported Office conversion formats.



Using cfdocument

In addition to the existing functionality, the cfdocumenttag lets you convert Word documents and PowerPoint
presentations to PDF. All versions of Microsoft Word and Microsoft PowerPoint from 97 to 2003 are supported.


Working with documents using OpenOffice
OpenOffice is an open-source office software that supports word processing, spreadsheets, presentations, and more.
OpenOffice stores data in an international open standard format. See http://www.openoffice.org/ for details.

ColdFusion 9 supports OpenOffice, which uses the cfdocument tag to convert a Word document (.doc format) to PDF.

When you use cfdocument to convert a document file, the tag first checks for an OpenOffice installation. When the
OpenOffice installation is found, the tag processes the rich text conversion through the OpenOffice libraries.

The cfdocument attributes, userPassword and permissions are used to open the converted PDF documents. For
complete information about the cfdocument attributes that support OpenOffice document conversion, see the CFML
Reference.

To install OpenOffice, see http://download.openoffice.org/index.html . See the ColdFusion 9 Installation Guide for
information about installing and configuring OpenOffice.


Example
The following example converts a document, MyDocument.doc, to a PDF file. The PDF conversion occurs only when
you specify the format attribute as "pdf".

Note: When you convert, specify only the absolute path, for example, "c:\documents\MyDocument.doc"

<cfdocument
     format="pdf"
     srcfile="C:\documents\MyDocument.doc"
     filename="C:\documents\MyDocument.pdf">
</cfdocument>

Note: If you do not specify the filename attribute, the converted PDF opens in a browser.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               890
Office file interoperability




Working with PowerPoint presentation files
You use the cfdocument tag to convert a PowerPoint presentation (PPT file) to a PDF document.


Example
The following example converts a PowerPoint presentation to a PDF file.

<cfdocument
     format="pdf"
     srcfile="C:\presentations\MyPresentation.ppt"
     filename="C:\presentations\MyPresentation.pdf">
</cfdocument>




Using cfpresentation

The cfpresentation tag is the parent tag for one or more cfpresentationslide tags, where you define the content
for the presentation, and the cfpresenter tags, which provide information about the people presenting the slides.

You use the cfpresentation tag to convert a PowerPoint presentation to an Adobe Connect presentation or HTML.
Browsers like Internet Explorer, Mozilla Firefox, and Safari are all compatible with the conversion from PPT to a
Connect presentation or HTML.

For complete information about cfpresentation, and cfpresentationslide, see CFML Reference.


Examples
The following example converts a PowerPoint presentation to an Adobe Connect presentation.

<cfpresentation
     title="my presentation"
     directory="C:\presentations\"
     overwrite=true>
     <cfpresentationslide
             src="#ppttemplate#backgrounds.ppt"
             slides="1">
     </cfpresentationslide>
     <cfpresentationslide
             duration="4"
             video="video1.flv">
                  Sample slide
     </cfpresentationslide>
</cfpresentation>

The following example converts an HTML file to a PowerPoint presentation.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                   891
Office file interoperability




<cfpresentation
     title = "text string"
     format= "ppt"
     destination="#generated#html_to_ppt_01.ppt"
     backgroundColor = "YELLOW"
     overwrite = "yes">
     <cfpresentationslide
             Title="Q1 Sales Figures"
             duration="14">



<h3>Q1 Sales Figures</h3>
<cfchart
     format="png"
     showborder="yes"
     chartheight="250"
     chartwidth="300"
     pieslicestyle="sliced">
     <cfchartseries type="pie">


     <cfchartdata
     item="Europe"
     value="9">


<cfchartdata
     item="Asia"
     value="20">


<cfchartdata
     item="North America"
     value="50">


<cfchartdata
     item="South America"
     value="21">
</cfchartseries>
</cfchart>
</cfpresentationslide>


<cfpresentationslide
     src="cfdocument_pos24.html"
     duration="15" />
</cfpresentation>

The following example converts a PowerPoint presentation to a Connect presentation.




                                         

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 892
Office file interoperability




<cfpresentation
     title="my presentation"
     directory="C:\presentations"
     overwrite=true>
     <cfpresentationslide
             src="#ppttemplate#backgrounds.ppt"
             slides="1-3,5">
     </cfpresentationslide>
     <cfpresentationslide
             duration="4"
             video="video1.flv">
                  Sample slide
     </cfpresentationslide>
</cfpresentation>




Using cfspreadsheet

The cfspreadsheet tag lets you manage Excel spreadsheets. The tag lets you do the following:

* Read a spreadsheet file (XLS file) and store it in a ColdFusion spreadsheet object, query, CSV string, or HTML
   string.

* Write a single sheet to a new XLS from a query, ColdFusion spreadsheet object, or CSV string variable.

* Add a sheet to an existing XLS file.

Use the spreadsheet functions to manipulate rows and columns in a spreadsheet and the data in the rows and columns.
You can also specify and get comments, values, and formulas for a cell in the spreadsheet.

Microsoft Office Excel 2007 is supported by cfspreadsheet and all the spreadsheet functions except the following:

* SpreadSheetAddSplitPane

* SpreadSheetAddFreezePane

For detailed information about cfspreadsheet and all the spreadsheet functions, see CFML Reference.


Examples
The following example reads the spreadsheet file - SingleSheet.xls and stores the spreadsheet data in a CSV string.

<cfspreadsheet action = "read"
     format="csv"
     src="C:\documents\SingleSheet.xls"
     name="csvvar"
     rows="1-4,5,6,7-8">
     <cfoutput>#csvvar#</cfoutput>

The following example reads the spreadsheet file - template_02.xls and stores the spreadsheet data in a query.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    893
Office file interoperability




<cfspreadsheet
             action = "read"
             src="C:\dcuments\template_02.xls"
             query="excelquery"
             sheet="1"
             rows="1-3,4-5"
             columns="1,4">
<cfoutput
             query="excelquery"
             startrow="1"
             maxrows="#excelquery.recordcount#">
             #excelquery.col_1#
             #excelquery.col_2#
</cfoutput>

The following example reads a spreadsheet file - template_08_Charts_Graph.xls and stores the spreadsheet data in an
HTML string.

<cfspreadsheet
             action = "read"
             format="html"
             src="C:\documents\template_08_Charts_Graph.xls"
             name="report1"
             rows="5-11"
             columns="1-6">
<cfoutput>
             #report1#
</cfoutput>

The following example uses data from a query and writes it to a single sheet in the spreadsheet file - SingleSheet1.xls

<cfquery
             name="excelquery"
             datasource="cfdocexamples">
             SELECT PARKNAME, REGION, STATE FROM Parks WHERE STATE='WI'
             ORDER BY ParkName, State
</cfquery>
<cfspreadsheet
             action = "write"
             filename="C:\SingleSheet1.xls"
             query="excelquery"
             overwrite="true">




Supported Office conversion formats

The following table lists the conversion formats supported by Office applications, and the CFML tags that support the
conversion. It also shows whether OpenOffice installation is required for the conversion.

All versions of Microsoft Word and Microsoft PowerPoint from 97 to 2003 are supported. Also, all versions of
Microsoft Excel from Versions 97 to 2007 are supported.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  894
Office file interoperability




 Format                                                    CFML Tag                        OpenOffice installation

 From                        To

 PPT                          Connect Presentation         cfpresentation                  optional

 PPT                          HTML                         cfpresentation                  optional

 PPT                          PDF                          cfdocument                      optional

 HTML                         PPT                          cfpresentation                  not required

 Excel                        HTML                         cfspreadsheet                   not required

 Excel                        Query                        cfspreadsheet                   not required

 Excel                        In-memory Variable           cfspreadsheet                   not required

 Query                        Excel                        cfspreadsheet                   not required

 In-memory variable           Excel                        cfspreadsheet                   not required

 Word                         PDF                          cfdocument                      required




SharePoint integration

You can use ColdFusion with Microsoft Windows SharePoint Services 2.0 or 3.0, and Microsoft Office SharePoint
Portal Server 2003 or Microsoft Office SharePoint Server 2007. You can integrate ColdFusion applications with
SharePoint features that are exposed as web service actions.


Load SharePoint actions from ColdFusion
Use the cfsharepoint tag to access exposed SharePoint features, without loading the web services directly. The
cfsharepoint tag works only if the SharePoint server uses basic authentication. By default, ColdFusion supports a
subset of features that are exposed as web services in SharePoint. If a web service is not supported in ColdFusion,
specify the URL to the WSDL (Web Services Description Language) of the web service that is loaded.

For information about the supported SharePoint features, see the CFML Reference.


Using cfsharepoint
Sharepoint integration with ColdFusion helps you dynamically manage user lists, views, and groups; work with images
and document workspaces; and use search effectively. The cfsharepoint tag lets you create new lists, retrieve list
items, and update list items on the SharePoint server.

The following example shows how to create a picture library list called "getpics".




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                895
Office file interoperability




<cfsharepoint
     action="addlist"
     login="#login#"
     params="#{ listname ="getpics",
     description="This a picture library list",
     templateId= "109 " }#"/>
 <! --- Creates a folder within the picture library list>
< cfsharepoint
     action       ="create new folder"
     login= "#login#"
     name="collection1"
     params="#{strListName="getpics",
     strParentFolder=""}#"/>
<!--- Uploads pictures to the folder that you created --->
<cfscript>
     myimage = filereadbinary(expandpath("Bird.jpg"));
     //convert the image into byte array to pass as input for "upload" action.
</cfscript>
<cfsharepoint
     action="upload" login="#login#"
     params="#{strListName="testpics",
     strfolder="Collection1",
     bytes="#myimage#",
     filename="bird.jpg",
     fOverwriteifexist=true}#"/>


<!--- Rotates the picture downloaded from the SharePoint server.--->
<cfsharepoint
     name ="result1"
     action="download"
     login="#login#"
     params="#{strListName="getpics",
     strfolder="New Folder",
     itemFileNames=["bird.jpg"],type=1,
     fFetchoriginalIfNotAvailable=true}#"/>
<cfimage
     action="rotate"
     source="#result1.file#"
     isbase64="yes"
     angle="45"
     name="temp"
     destination="bird.jpg"
     overwrite="yes"/>
<cfscript>
baseimage = filereadbinary(expandpath("bird.jpg"));
//convert the image into byte array to pass as input for "upload" action.
</cfscript>
<!--- Uploads the rotated image back to the SharePoint server --->
<cfsharepoint
action="upload"
login="#login#"
params="#{strListName="getpics",strfolder="Collection1",
bytes="#baseimage#",filename="bird.jpg",fOverwriteifexist=true}#"/>

To check and ensure that all the updates are made, you can retrieve the list items using code like the following:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      896
Office file interoperability




<cfsharepoint
     action="getimaginglistitems"
     login="#login#" name="result"
     params="#{strListName="getpics",strFolder="#result3.title#"}#"/>
<cfloop array="#result.Library#" index="n">
<cfif n.ows_FileLeafRef          contains "temppicrotate.jpg">
SUCCESS
<cfbreak>
</cfif>
</cfloop>



Access ColdFusion from SharePoint using custom Web Parts
You can access ColdFusion applications from within SharePoint using custom Web Parts. You can create a custom
Web Part using the Page Viewer Web Part template that is shipped, by default, with SharePoint services 2.0 and 3.0,
and Microsoft Office SharePoint Portal Server 2003 or Microsoft Office SharePoint Server 2007.

1 From the SharePoint Server page, click Modify Shared Page.

2 Select Add Web Part.

3 Click Browse from the pop-up menu. The Web Parts list appears.

4 Select Page Viewer Web Part.

5 Click Add. The Page Viewer Web Part loads.

6 Click the Open the Tools Pane link.

7 Specify the URL of the ColdFusion application in the URL text field. The ColdFusion application loads within the
   Web Part.

Note: In a Multiserver installation on the Macintosh platform, SharePoint does not work properly if the tools.jar file is
present in WEB-INF/cfusion/lib. In this case, you see the following error message:"coldfusion.jsp.JavaCompiler's
UnknownCompiler: Unable to run the internal Java compiler: java.lang.NoClassDefFoundError:
javax/tools/StandardJavaFileManager." To overcome this issue, copy the tools.jar file to a backup directory and delete
the tools.jar file from WEB-INF/cfusion/lib.


Use Single Sign-On to access ColdFusion applications via SharePoint
SharePoint custom Web Parts let you access multiple ColdFusion applications from the SharePoint server using Single
Sign-On (SSO). After signing in, users can access multiple secure ColdFusion applications by accessing ColdFusion
services from multiple Web Parts.

Note: For single sign-on capability, you need Microsoft Office SharePoint Portal Server 2003 or Microsoft Office
SharePoint Server 2007.

To make a ColdFusion application available from SharePoint, use the CFSharepoint SSO WebPart template. This
template is a customized version of PageViewer WebPart. It enables you to pass SSO credentials to the ColdFusion
application. Download this template from the Adobe website or copy it from the ColdFusion 9 DVD.

Remember these points:

* Web Parts support only the native single sign-on solution; other pluggable single sign-on services are not
   supported.

* Only single sign-on credentials are passed to the ColdFusion application. The ColdFusion application must have
   the necessary logic to retrieve the credentials and login to the application.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 897
Office file interoperability




Deploy the CF9SSOWebPart.wsp Web Part for Microsoft Office SharePoint Server 2007
To configure single sign-on for Microsoft Office SharePoint Server 2007, deploy the CF9SSOWebPart.wsp file to the
SharePoint server.

1 Copy the CF9SSOWebPart.wsp file to the BIN folder within the Web Server extensions. It is normally located at
   Program Files\Common Files\Microsoft Shared\Web Server Extensions\12\BIN in the SharePoint server.

2 To deploy the solution to SharePoint, use the command prompt to navigate to Program Files\Common
   Files\Microsoft Shared\Web Server Extensions\12\BIN and enter the following commands, as required.

   To delete the solution if it is already present:

   STSADM.EXE -o deletesolution -name CF9SSOWebPart.wsp -override

    To add the solution to SharePoint:

   STSADM.EXE -o addsolution -f CF9SSOWebPart.wsp

   To deploy the solution to the configured website by specifying the URL:

   STSADM.EXE -o deploysolution -name CF9SSOWebPart.wsp
   -url <virtual server url> -local -allowGacDeployment

   To deploy the solution to all the configured websites:

   STSADM.EXE -o deploysolution -name CF9SSOWebPart.wsp -allcontenturls -local -
   allowGacDeployment


Import the CF9SSOWebPart.wsp Web Part into a Web Part Page
1 Navigate to the web page on the SharePoint server where you want the Web Part to be accessible.

2 In the Web Part page, click Site Actions > Site Settings.

3 In the Site Settings page, click Galleries > Web Parts.

4 In the Web Part gallery, click Upload in the toolbar pane.

5 Select the CF9SSOWebPart.wsp Web Part.

6 Enter the following details in the toolbar pane.

    * URL of the ColdFusion application to access

    * The form field name as the User ID

    * The form field name as the password

    * Name of the SSO application where the credentials are configured

Once the Web Part is deployed, it takes the credentials from the SharePoint Single Sign-On database (based on the
application name entered in the Tools Pane). These credentials are transferred to the ColdFusion application through
the URL (provided in the Tools Pane) in a FORM containing the specified form fields.


Deploy the CF9SharepointSSOCab.CAB Web Part for Microsoft Office SharePoint Portal
Server 2003
To configure single sign-on for Microsoft Office SharePoint Portal Server 2003, deploy the CAB file -
CF9SharepointSSOCab.CAB

1 Copy the CF9SharepointSSOCab.CAB to the BIN folder within the Web Server extensions. It is normally located
   at Program Files\Common Files\Microsoft Shared\Web Server Extensions\60\BIN in the SharePoint server.

2 Create a folder named CFSharePointSSO within the Layouts folder. The Layouts folder is normally located at
   Program Files\Common Files\Microsoft Shared\Web Server Extensions\60\Template\Layouts.


                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 898
Office file interoperability




3 From the CF9SharepointSSOCab.CAB file, copy the template file CFSSO.aspx to the CFSharePointSSO folder that
   you created in the previous step.

4 In the command prompt, navigate to Program Files\Common Files\Microsoft Shared\Web Server
   Extensions\60\bin and enter the following command to add the CAB file:

   stsadm.exe -o addwppack -filename CF9SharepointSSOCab.CAB -globalinstall

   If the CAB file exists, delete the CAB file and then enter the command to add the CAB file as follows:

   stsadm.EXE -o deletewppack -name CF9SharepointSSOCab.CAB

   stsadm.exe -o addwppack -filename CF9SharepointSSOCab.CAB -globalinstall


Configure the CFSharepointSSO Web Part
1 In the Site Settings page, go to Manage Security and Additional Settings > Manage Web Part Gallery.

2 Click New in the toolbar in the Web Part Gallery. The New Web Parts List appears.

3 Select the CFSSOwebpart.dwp Web Part and click Populate Gallery.

4 Add the CFSharepointSSO Web Part to the Virtual Server Gallery. Clicking Edit Page > Modify Shared Page > Add
   Web Parts > Browse > Virtual Server Gallery. Then add the Web Part to the Web Parts list.

5 After adding the CFSharepointSSO Web Part, click the Tools pane and enter the following details.

    * URL of the ColdFusion application to access

    * The form field name as the User ID

    * The form field name as the password

    * Name of the SSO application where the credentials are configured

Once the Web Part is deployed, it takes the credentials from the SharePoint Single Sign-On database (based on the
application name entered in the Tools Pane). These credentials are transferred to the ColdFusion application through
the URL (provided in the Tools Pane) in a FORM containing the specified form fields.



