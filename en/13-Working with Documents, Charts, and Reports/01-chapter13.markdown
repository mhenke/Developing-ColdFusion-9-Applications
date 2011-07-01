Chapter 13: Working with Documents,
Charts, and Reports

Manipulating PDF Forms in ColdFusion

You can use Adobe ColdFusion to manipulate PDF forms created in Adobe Acrobat Professional and Adobe
LiveCycleTM Designer.


About PDF forms
Adobe ColdFusion lets you incorporate interactive PDF forms in your application. You can extract data submitted
from the PDF forms, populate form fields from an XML data file or a database, and embed PDF forms in PDF
documents created in ColdFusion.

ColdFusion supports interactive forms created with Adobe Acrobat forms and with LiveCycle. In Adobe Acrobat 6.0
or earlier, you can create interactive Acroforms. Using Adobe LiveCycle Designer, which is provided with Adobe
Acrobat Professional 7.0 and later, you can generate interactive forms.

The type of form is significant because it affects how you manipulate the data in ColdFusion. For example, you cannot
use an XML data file generated from a form created in Acrobat to populate a form created in LiveCycle, or the reverse,
because the XML file formats differ between the two types of forms.

Forms created in Acrobat use the XML Forms Data Format (XFDF) file format. Forms created in LiveCycle use the
XML Forms Architecture (XFA) format introduced in Acrobat and Adobe Reader 6. For examples, see "Populating a
PDF form with XML data" on page 915. The file format also affects how you prefill fields in a form from a data source,
because you map the data structure as well as the field names. For examples, see "Prefilling PDF form fields" on
page 916.

The use of JavaScript also differs based on the context. The JavaScript Object Model in a PDF file differs from the
HTML JavaScript Object Model. Consequently, scripts written in HTML JavaScript do not apply to PDF files. Also,
JavaScript differs between forms created in Acrobat and those forms created in LiveCycle: scripts written in one format
do not work with other.

ColdFusion 9 introduced several tags for manipulating PDF forms:


 Tag                               Description

 cfpdfform                         Reads data from a form and writes it to a file or populates a form with data from a data source.

 cfpdfformparam                    A child tag of the cfpdfform tag or the cfpdfsubform tag; populates individual fields in PDF forms.

 cfpdfsubform                      A child tag of the cfpdfform tag; creates the hierarchy of the PDF form so that form fields are filled
                                   properly. The cfpdfsubform tag contains one or more cfpdpformparam tags.


The following table describes a few of the tasks that you can perform with PDF forms:




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         915
Working with Documents, Charts, and Reports




 Task                                                          Tags and actions

 Populate a PDF form with XML data                             populate action of the cfpdf tag

 Prefill individual fields in a PDF form with data from a data populate action of the cfpdfform tag with the cfpdfsubform and
 source                                                        cfpdfparam tags

 Determine the structure of a PDF form                         read action of the cfpdfform tag with the cfdump tag

 Embed an interactive PDF form within a PDF document           populate action of the cfpdfform tag within the cfdocument tag.

                                                               Note: The cfpdfform tag must be at the same level as the
                                                               cfdocumentsection tags, not contained within them.

 Write a PDF form directly to the browser                      populate action of the cfpdfform tag with the destination attribute not
                                                               specified

 Write PDF form output to an XML file                          read action of the cfpdfform tag

 Print a PDF form from ColdFusion                              cfprint tag

 Extract data from a PDF form submission                       source="#PDF.Content#" for the read action of the cfpdfform tag

 Write data extracted from a PDF form submission to a PDF      source="#PDF.Content#" for the populate action of the cfpdfform tag,
 file                                                          and the destination attribute

 Write data in a form generated in LiveCycle to an XDP file    source="#PDF.Content#" for the populate action of the cfpdfform tag,
                                                               and an XDP extension for the output file

 Extract data from an HTTP post submission                     cfdumptagdeterminesthestructureoftheformdata;maptheformfieldstothe
                                                               output fields

 Flatten forms generated in Acrobat (not used forms            cfpdf
 generated in LiveCycle)
                                                               For more information, see "Flattening forms created in Acrobat" on page 941.

 Merge forms generated in Acrobat or LiveCycle with other      cfpdf action="merge"
 PDF documents
                                                               For more information, see "Merging PDF documents" on page 939.



Populating a PDF form with XML data
Some applications submit PDF form data in an XML data file. For example, the e-mail submit option in forms created
in LiveCycle generates an XML data file and delivers it as an attachment to the specified e-mail address. This technique
is an efficient way to transmit and archive data because XML data files are smaller than PDF files. However, XML files
are not user-friendly: to view the file in its original format, the user has to open the PDF form template in Acrobat and
import the XML data file.

ColdFusion automates the process of reuniting XML data with the PDF form that generated it. To reunite them, you
use the populate action of the cfpdfform tag, specify the source, which is the PDF form used as a template, and
specify the XML data file that contains the information submitted by the person who completed the form. You also
have the option to save the result to a new file, which lets you save the completed forms in their original format (and
not just the form data). In the following example, ColdFusion populates the payslipTemplate.pdf form with data from
the formdata.xml data file and writes the form to a new PDF file called employeeid123.pdf:

<cfpdfform source="c:\payslipTemplate.pdf" destination="c:\empPayslips\employeeid123.pdf"
action="populate" XMLdata="c:\formdata.xml"/>

For forms created in LiveCycle, you have the option to write the output to an XML Data Package (XDP) file rather than
a PDF file. For more information, see "Writing LiveCycle form output to an XDP file" on page 920.

Note: If you do not specify a destination, the populate action displays the populated PDF form in a browser window.




                                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      916
Working with Documents, Charts, and Reports




When you populate a form with an XML data file, ensure that the XML data is in the appropriate format. The format
of the XML data file differs based on whether it was generated from Acrobat or LiveCycle. Acrobat generates an XML
Forms Data Format (XFDF) file format. The following example shows the XFDF format:

<?xml version="1.0" encoding="UTF-8"?>
- <xfdf xmlns="http://ns.adobe.com/xfdf/" xml:space="preserve">
      - <fields>
           - <field name="textname">
                       <value>textvalue</value>
                 </field>
           - <field name="textname1">
                       <value>textvalue1</value>
                 </field>
           </fields>
      </xfdf>

Forms created in LiveCycle require an XML Forms Architecture (XFA) format. The following example shows an XFA
format:

<?xml version="1.0" encoding="UTF-8"?>
- <xfa:data xmlns:xfa="http://www.xfa.org/schema/xfa-data/1.0/">
- <form1>
           <SSN>354325426</SSN>
           <fname>coldfusion</fname>
           <num>354325426.00</num>
      - <Subform1>
                 <SSN />
           </Subform1>
</form1>
</xfa>



Prefilling PDF form fields
ColdFusion lets you prefill individual form fields with data extracted from a data source. For example, you can run a
query to extract returning customer information from a data source based on a user name and password and populate
the related fields in an order form. The customer can complete the rest of the fields in the form and submit it for
processing. To do so, map the field names and the data structure of the PDF form to the fields in the data source.

To determine the structure of the PDF form, use the read action of the cfpdfform tag, as the following example shows:

<cfpdfform source="c:\forms\timesheet.pdf" result="resultStruct" action="read"/>

Then use the cfdump tag to display the structure:

<cfdump var="#resultStruct#">

The result structure for a form created in Acrobat form could look something like the following example:


 struct

 firstName                                                  [empty string]

 lastName                                                   [empty string]

 department                                                 [empty string]

 ...                                                        ...


To prefill the fields in ColdFusion, you add a cfpdfformparam tag for each of the fields directly under the cfpdfform tag:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   917
Working with Documents, Charts, and Reports




<cfpdfform action="populate" source="c:\forms\timsheet.PDF">
      <cfpdfformparam name="firstName" value="Boris">
      <cfpdfformparam name="lastName" value="Pasternak">
      <cfpdfformparam name="department" value="Marketing">
...
</cfpdfform>

Forms created in LiveCycle from the standard blank forms contain a subform called form1. The result structure of a
form created in LiveCycle could look like the following example:


 struct

 form1                   struct

                         txtfirstName                                   [empty string]

                         txtlastName                                    [empty string]

                         txtdepartment                                  [empty string]

                         ...                                            ...


To prefill the fields in ColdFusion, add a cfpdfsubform tag for form1 and a cfpdfformparam tag for each of the fields
to fill directly below the cfpdfsubform tag:

<cfpdfform source="c:\forms\timesheetForm.pdf" action="populate">
      <cfpdfsubform name="form1">
            <cfpdfformparam name="txtfirstName" value="Harley">
            <cfpdfformparam name="txtlastName" value="Davidson">
            <cfpdfformparam name="txtDeptName" value="Engineering">
            ...
      </cfpdfsubform>
</cfpdfform>

Note: In dynamic forms created in LiveCycle forms (forms saved as Dynamic PDF Form Files in LiveCycle Designer), you
have the option to mark how many times a record is repeated. Therefore, if no record exists for a subform, the subform
does not appear in the structure returned by the read action of the cfpdfform tag. You view these forms in LiveCycle
Designer to see the hierarchy.


Nesting subforms
Although Acrobat forms do not contain subforms, some contain complex field names. For example, an Acrobat form
could contain the following fields: form1.x.f1, form1.x.f2, form1.x.f3, and so on.

Because the cfpdfparam tag does not handle field names with periods in them, ColdFusion treats forms with complex
field names created in Acrobat the same way as subforms created in LiveCycle. Therefore, the result structure of an
Acrobat form with complex field names would look like the following example:


 struct

 form1                   struct

                         x              struct

                                        f1                               [empty string]

                                        f2                               [empty string]

                                        ...                              ...




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 918
Working with Documents, Charts, and Reports




In ColdFusion, to prefill the fields in forms created in Acrobat, nest the field names as subforms:

<cfpdfform action="populate" source="acrobatForm.pdf">
    <cfpdfsubform name="form1">
          <cfpdfsubform name="x">
               <cfpdfformparam name="f1" value="AGuthrie">
               <cfpdfformparam name="f2" value="123">
               <cfpdfformparam name="f3" value="456">
          </cfpdfsubform>
    </cfpdfsubform>
</cfpdfform>

Often, forms created in LiveCycle contain subforms within the form1 subform. For example, the following grant
application contains nested subforms:


struct

form1              struct

                   grantapplication               struct

                                                  page1                struct

                                                                       orgAddress             [empty string]

                                                                       orgCity                [empty string]

                                                                       orgState               [empty string]

                                                                       ...                    ...

                                                  page2                struct

                                                                       description            [empty string]

                                                                       pageCount              [empty string]

                                                                       ...                    ...


To populate the fields in ColdFusion, map the structure by using nested cfpdfsubform tags:

<cfpdfform source="c:\grantForm.pdf" destination="c:\employeeid123.pdf" action="populate">
    <cfpdfsubform name="form1">
          <cfpdfsubform name="grantapplication">
               <cfpdfsubform name="page1">
                     <cfpdfformparam name="orgAddress" value="572 Evergreen Terrace">
                     <cfpdfformparam name="orgCity" value="Springfield">
                     <cfpdfformparam name="orgState" value="Oregon">
                     ...
               </cfpdfsubform>
               <cfpdfsubform name="page2">
                     <cfpdfformparam name="description" value="Head Start">
                     <cfpdfformparam name="pageCount" value="2">
                     ...
               </cfpdfsubform>
          </cfpdfsubform>
    </cfpdfsubform>
</cfpdfform>

Note: A PDF file can contain only one interactive form. Therefore, if a PDF file contains subforms, a Submit button
submits data for all the subforms simultaneously.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    919
Working with Documents, Charts, and Reports




Embedding a PDF form in a PDF document
You can use the cfpdfform tag inside the cfdocument tag to embed an existing interactive PDF form in a PDF
document. This technique is useful to include additional information with a standard interactive form. For example,
a company could have a generic PDF form for maintaining employee information. You could reuse this form in
different contexts to ensure that the employee information is current.

To create the static PDF pages, use the cfdocument tag and cfdocumentsection tags. Then use the cfpdfform tag
in the cfdocument tag to create an interactive form in the PDF document. When the user updates the form and prints
or submits it, all of the pages in the document, including the static PDF pages, are printed or submitted with the form.

Note: You can embed only one interactive form in a PDF document; therefore, include only one cfpdfform tag in a
cfdocument tag. However, each cfpdfform tag can include multiple cfpdfsubform tags and cfpdfformparam tags.

Use at least one cfdocumentsection tag with the cfpdfform tag, but do not place the cfpdfform tag within the
cfdocumentsection tag. Instead, insure that the cfpdfform and cfdocumentsection tags are at the same level, the
following example shows:

<cfdocument format="pdf">
     <cfdocumentitem type="header">
     <font size="+1">This is the Header</font>
     </cfdocumentitem>
     <cfdocumentitem type="footer">
     <font size="+1">This is the Footer</font>
     </cfdocumentitem>


     <cfdocumentsection>
     <p>This is the first document section.</p>
     </cfdocumentsection>


     <cfpdfform source="c:\forms\embed.pdf" action="populate">
          <cfpdfsubform name="form1">
                <cfpdfformparam name="txtManagerName" value="Janis Joplin">
                <cfpdfformparam name="txtDepartment" value="Sales">
          </cfpdfsubform>
     </cfpdfform>


     <cfdocumentsection>
     <p>This is another section</p>
     </cfdocumentsection>
</cfdocument>

The contents of the cfpdfform tag start on a new page. Any text or code directly after the cfdocument tag and before
the cfpdfform tag applies to the document sections but not to the interactive PDF form in the cfpdfform tag.

The headers and footers that are part of the embedded PDF form do not apply to the rest of the PDF document, and
the headers and footers that are defined in the cfdocument tag do not apply to the interactive form. However, header
and footer information defined in the cfdocumentitem tags resumes in the sections that follow the embedded form
and account for the pages in the embedded form.

Note: The read action of the cfpdfform tag is not valid when you embed a PDF form. Also, you cannot specify a
destination in the cfpdfform tag. However, you can specify a filename in the cfdocument tag to write the PDF
document with the PDF form to an output file. If you do not specify a filename, ColdFusion displays the PDF form in the
context of the PDF document in the browser.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    920
Working with Documents, Charts, and Reports




Extracting data from a PDF form submission
Data extraction differs based on how the PDF form is submitted. ColdFusion supports two types of PDF form
submission: HTTP post, which submits the form data, but not the form itself, and PDF, which submits the entire PDF file.

One use for PDF submission is for archival purpose: because the form is submitted with the data, you can write the
output to a file. HTTP post submissions process faster because only the field data is transmitted, which is useful for
updating a database or manipulating specific data collected from the form, but you cannot write an HTTP post
submission directly to a file.

Note: Although forms created in LiveCycle Designer allow several types of submission, including XDP and XML,
ColdFusion can extract data from HTTP post and PDF submissions only.

In LiveCycle Designer, the XML code for an HTTP post submission looks like the following example:

<submit format="formdata" target="http://localhost:8500/pdfforms/pdfreceiver.cfm"
textEncoding="UTF-8"/>

In LiveCycle Designer, the XML code for a PDF submission looks like the following example:

<submit format="pdf" target="http://localhost:8500/pdfforms/pdfreceiver.cfm"
textEncoding="UTF-16" xdpContent="pdf datasets xfdf"/>

Note: Acrobat forms are submitted in binary format, not XML format.


Extracting data from a PDF submission
Use the following code to extract data from a PDF submission and write it to a structure called fields:

<!--- The following code reads the submitted PDF file and generates a result structure called
fields. --->
<cfpdfform source="#PDF.content#" action="read" result="fields"/>

Use the cfdump tag to display the data structure, as follows:

<cfdump var="#fields#">

Note: When you extract data from a PDF submission, always specify "#PDF.content#" as the source.

You can set the form fields to a variable, as the following example shows:

<cfset empForm="#fields.form1#">

Use the populate action of the cfpdfform tag to write the output to a file. Specify "#PDF.content#" as the source. In
the following example, the unique filename is generated from a field on the PDF form:

<cfpdfform action="populate" source="#PDF.content#"
     destination="timesheets\#empForm.txtsheet#.pdf" overwrite="yes"/>


Writing LiveCycle form output to an XDP file
For Acrobat forms, you can write the output to a PDF file only. For LiveCycle forms, you have the option to write the
output to an XDP file. The filename extension determines the file format: to save the output in XDP format, simply
use an XDP extension in the destination filename, as the following example shows:

<cfpdfform action="populate" source="#PDF.content#"
     destination="timesheets\#empForm.txtsheet#.xdp" overwrite="yes"/>

An XDP file is an XML representation of a PDF file. In LiveCycle Designer, an XDP file contains the structure, data,
annotations, and other relevant data to LiveCycle forms, which renders the form at run time.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  921
Working with Documents, Charts, and Reports




ColdFusion XDP files contain the XDP XML code and the PDF image. Therefore, the file size is larger than a PDF file.
Only write PDF forms to XDP files if you must incorporate them into the LiveCycle Designer workflow on a LiveCycle
server.


Writing PDF output to an XML file
ColdFusion lets you extract data from a PDF form and write the output to an XML data file. To do so, you must save
the form output as a PDF file. (The cfpdfform tag source must always be a PDF file.)

To write the output of a PDF file to an XML file, use the read action of the cfpdfform tag, as the following example
shows:

<cfpdfform action="read" source="#empForm.txtsheet#.pdf"
     XMLdata="timesheets\#empForm.txtsheet#.xml"/>

To save disk space, you can delete the PDF file and maintain the XML data file. As long as you keep the blank PDF
form used as the template, you can use the populate action to regenerate the PDF file. For more information on
populating forms, see "Populating a PDF form with XML data" on page 915.


Extracting data from an HTTP post submission
For an HTTP post submission, use the cfdump tag with the form name as the variable to display the data structure, as
follows:

<cfdump var="#FORM.form1#">

Note: When you extract data from an HTTP post submission, always specify the form name as the source. For example,
specify "#FORM.form1#" for a form generated from a standard template in LiveCycle.

Notice that the structure is not necessarily the same as the structure of the PDF file used as the template (before
submission). For example, the structure of a form before submission could look like the following example:


 struct

 form1                 struct

                       txtDeptName                                      [empty string]

                       txtEMail                                         [empty string]

                       txtEmpID                                         [empty string]

                       txtFirstName                                     [empty string]

                       txtLastName                                      [empty string]

                       txtPhoneNum                                      [empty string]


After submission by using HTTP post, the resulting structure would look like the following example:


 struct

 FORM1          struct




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  922
Working with Documents, Charts, and Reports




 struct

                SUBFORM               struct

                                      HEADER             struct

                                                         HTTPSUBMITBUTTON1                 [empty string]

                                                         TXTDEPTNAME                       Sales

                                                         TXTFIRSTNAME                      Carolynn

                                                         TXTLASTNAME                       Peterson

                                                         TXTPHONENUM                       (617) 872-9178

                                      TXTEMPID           1

                                      TXTEMAIL           carolynp@company


Note: When data extraction using the cfpdfform tag results in more than one page, instead of returning one structure,
the extraction returns one structure per page.

The difference in structure reflects internal rules applied by Acrobat for the HTTP post submission.

To extract the data from the HTTP post submission and update a database with the information, for example, map the
database columns to the form fields, as the following code shows:

<cfquery name="updateEmpInfo" datasource="cfdocexamples">
UPDATE EMPLOYEES
     SET FIRSTNAME = "#FORM1.SUBFORM.HEADER.TXTFIRSTNAME#",
          LASTNAME = "#FORM1.SUBFORM.HEADER.TXTLASTNAME#",
          DEPARTMENT = "#FORM1.SUBFORM.HEADER.TXTDEPTNAME#",
          IM_ID = "#FORM1.SUBFORM.TXTEMAIL#",
          PHONE = "#FORM1.SUBFORM.HEADER.TXTPHONENUM#"
     WHERE EMP_ID = <cfqueryparam value="#FORM1.SUBFORM.TXTEMPID#">
</cfquery>

You can set a variable to create a shortcut to the field names, as the following code shows:

<cfset fields=#form1.subform.header#>

Use the cfoutput tag to display the form data:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 923
Working with Documents, Charts, and Reports




<h3>Employee Information</h3>
<cfoutput>
    <table>
         <tr>
               <td>Name:</td>
               <td>#fields.txtfirstname# #fields.txtlastname#</td>
         </tr>
         <tr>
               <td>Department:</td>
               <td>#fields.txtdeptname#</td>
         </tr>
         <tr>
               <td>E-Mail:</td>
               <td>#fields.txtemail#</td>
         <tr>
               <td>Phone:</td>
               <td>#fields.txtphonenum#</td>
         </tr>
    <table>
</cfoutput>



Application examples that use PDF forms
The following examples show how you can use PDF forms in your applications.


PDF submission example
The following example shows how to populate fields in a PDF form created in LiveCycle Designer based on an
employee login information. When the employee completes the form and clicks the PDF Submit button, the entire
PDF form with the data is submitted to a second processing page where ColdFusion writes the completed form to a file.

On the ColdFusion login page, an employee enters a user name and password:

<!--- The following code creates a simple form for entering a user name and password.
    The code does not include password verification. --->
<h3>Timesheet Login Form</h3>
<p>Please enter your user name and password.</p>
<cfform name="loginform" action="loginform_proc.cfm" method="post">
<table>
    <tr>
         <td>user name:</td>
         <td><cfinput type="text" name="username" required="yes"
                   message="A user name is required."></td>
    </tr>
    <tr>
         <td>password:</td>
         <td><cfinput type="password" name="password" required="yes"
                   message="A password is required."></td>
    </tr>
</table>
    <br/>
    <cfinput type="submit" name="submit" value="Submit">
</cfform>




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    924
Working with Documents, Charts, and Reports




On the first processing page, a query retrieves all of the information associated with the user name from the
cfdocexamples database. The cfpdfform tag populates an associated PDF form created in LiveCycle Designer (called
timesheetForm.pdf) with the employee name, phone number, e-mail address, and department. ColdFusion displays
the populated form in the browser, where the employee can complete the form and submit it.

<!--- The following code retrieves all of the employee information for the user name entered
     on the login page. --->
<cfquery name="getEmpInfo" datasource="cfdocexamples">
     SELECT * FROM EMPLOYEES
     WHERE EMAIL = <cfqueryparam value="#FORM.username#">
</cfquery>


<!---
The following code populates the template called "timesheetForm.pdf" with data from the query
and displays the interactive PDF form in the browser. A field in the PDF form contains the name
of the output file being written. It is a combination of the user name and the current date.
--->


<!--- Notice the use of the cfpdfsubform tag. Forms created from templates in LiveCycle
Designer include a subform called form1. Use the cfpdfsubform tag to match the structure of
the form in ColdFusion. Likewise, the field names in the cfpdfformparam tags must match the
field names in the PDF form. If the form structures and field names do not match exactly,
ColdFusion does not populate the form fields. --->


<cfpdfform source="c:\forms\timesheetForm.pdf" action="populate">
     <cfpdfsubform name="form1">
          <cfpdfformparam name="txtEmpName" value="#getEmpInfo.FIRSTNAME#
               #getEmpInfo.LASTNAME#">
          <cfpdfformparam name="txtDeptName" value="#getEmpInfo.DEPARTMENT#">
          <cfpdfformparam name="txtEmail" value="#getEmpInfo.IM_ID#">
          <cfpdfformparam name="txtPhoneNum" value="#getEmpInfo.PHONE#">
          <cfpdfformparam name="txtManagerName" value="Randy Nielsen">
          <cfpdfformparam name="txtSheet"
               value="#form.username#_#DateFormat(Now())#">
     </cfpdfsubform>
</cfpdfform>

When the user completes the timesheet form (by filling in the time period, projects, and hours for the week) and clicks
the Submit button, Acrobat sends the PDF file in binary format to a second ColdFusion processing page.

Note: In LiveCycle Designer, use the standard Submit button on the PDF form and specify "submit as: PDF" in the button
Object Properties. Also, ensure that you enter the URL to the ColdFusion processing page in the Submit to URL field.

The cfpdfform tag read action reads the PDF content into a result structure named fields. The cfpdfform tag
populate action writes the completed form to a file in the timesheets subdirectory.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 925
Working with Documents, Charts, and Reports




<!--- The following code reads the PDF file submitted in binary format and generates a result
structure called fields. The cfpdfform populate action and the cfoutput tags reference the
fields in the structure. --->


<cfpdfform source="#PDF.content#" action="read" result="fields"/>
<cfset empForm="#fields.form1#">
<cfpdfform action="populate" source="#PDF.content#"
destination="timesheets\#empForm.txtsheet#.pdf" overwrite="yes"/>


<h3>Timesheet Completed</h3>
<p><cfoutput>#empForm.txtempname#</cfoutput>,</p>
<p>Thank you for submitting your timesheet for the week of
<cfoutput>#DateFormat(empForm.dtmForPeriodFrom, "long")#</cfoutput> through
<cfoutput>#DateFormat(empForm.dtmForPeriodto, "long")#</cfoutput>. Your manager,
<cfoutput>#empForm.txtManagerName#</cfoutput>, will notify you upon approval.</p>


HTTP post example
The following example shows how to extract data from a PDF form submitted with HTTP post and use it to update an
employee database. The form was created in LiveCycle Designer.

On the ColdFusion login page, an employee enters a user name and password:

<!--- The following code creates a simple form for entering a user name and password. The code
does not include password verification. --->


<h3>Employee Update Login Form</h3>
<p>Please enter your user name and password.</p>
<cfform name="loginform" action="loginform_procHTTP.cfm" method="post">
<table>
    <tr>
          <td>user name:</td>
          <td><cfinput type="text" name="username" required="yes"
                    message="A user name is required."></td>
    </tr>
    <tr>
          <td>password:</td>
          <td><cfinput type="password" name="password" required="yes"
                    message="A password is required."></td>
    </tr>
</table>
    <br/>
    <cfinput type="submit" name="submit" value="Submit">
</cfform>

On the first processing page, a query retrieves all of the information associated with the user name from the
cfdocexamples database. The cfpdfform tag populates an associated PDF form created in LiveCycle Designer (called
employeeInfoHTTP.pdf) with the employee name, phone number, e-mail address, and department. The form also
includes the employee ID as a hidden field. ColdFusion displays the populated form in the browser where the employee
can change personal information in the form and submit it.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   926
Working with Documents, Charts, and Reports




<!--- The following code retrieves all of the employee information for the user name entered
on the form page. --->
<cfquery name="getEmpInfo" datasource="cfdocexamples">
SELECT * FROM EMPLOYEES
WHERE EMAIL = <cfqueryparam value="#FORM.username#">
</cfquery>


<!--- The following code populates the template called "employeeInfoHTTP.pdf" with data from
the query. As in the previous example, notice the use of the cfpdfsubform tag. The txtEmpID
field is a hidden field on the PDF form. --->


<cfquery name="getEmpInfo" datasource="cfdocexamples">
SELECT * FROM EMPLOYEES
WHERE EMAIL = <cfqueryparam value="#FORM.username#">
</cfquery>


<cfpdfform source="c:\forms\employeeInfoHTTP.pdf" action="populate">
     <cfpdfsubform name="form1">
          <cfpdfformparam name="txtFirstName" value="#getEmpInfo.FIRSTNAME#">
          <cfpdfformparam name="txtLastName" value="#getEmpInfo.LASTNAME#">
          <cfpdfformparam name="txtDeptName" value="#getEmpInfo.DEPARTMENT#">
          <cfpdfformparam name="txtEmail" value="#getEmpInfo.IM_ID#">
          <cfpdfformparam name="txtPhoneNum" value="#getEmpInfo.PHONE#">
          <cfpdfformparam name="txtEmpID" value="#getEmpInfo.Emp_ID#">
     </cfpdfsubform>
</cfpdfform>

When the employee updates the information in the form and clicks the HTTP post Submit button, Acrobat sends the
form data (but not the form itself) to a second ColdFusion processing page.

Note: In LiveCycle Designer, use the HTTP Submit button on the PDF form. Also, ensure that you enter the URL to the
ColdFusion processing page in the URL field of button Object Properties.

Reproduce the structure, not just the field name, when you reference form data. To determine the structure of the form
data, use the cfdump tag.

<!--- The following code reads the form data from the PDF form and uses it to update
     corresponding fields in the database. --->


<cfquery name="updateEmpInfo" datasource="cfdocexamples">
UPDATE EMPLOYEES
     SET FIRSTNAME = "#FORM1.SUBFORM.HEADER.TXTFIRSTNAME#",
          LASTNAME = "#FORM1.SUBFORM.HEADER.TXTLASTNAME#",
          DEPARTMENT = "#FORM1.SUBFORM.HEADER.TXTDEPTNAME#",
          IM_ID = "#FORM1.SUBFORM.HEADER.TXTEMAIL#",
          PHONE = "#FORM1.SUBFORM.HEADER.TXTPHONENUM#"
     WHERE EMP_ID = <cfqueryparam value="#FORM1.SUBFORM.TXTEMPID#">
</cfquery>
<h3>Employee Information Updated</h3>
<p><cfoutput>#FORM1.SUBFORM.HEADER.TXTFIRSTNAME#</cfoutput>,</p>
<p>Thank you for updating your employee information in the employee database.</p>


Embedded PDF form example
The following example shows how to embed an interactive PDF form in a PDF document created with the
cfdocument tag.

On the login page, an employee enters a user name and password:



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    927
Working with Documents, Charts, and Reports




<h3>Employee Login Form</h3>
<p>Please enter your user name and password.</p>
<cfform name="loginform" action="embed2.cfm" method="post">
     <table>
           <tr>
               <td>user name:</td>
               <td><cfinput type="text" name="username" required="yes"
                   message="A user name is required."></td>
           </tr>
           <tr>
               <td>password:</td>
               <td><cfinput type="password" name="password" required="yes"
                   message="A password is required."></td>
           </tr>
     </table>
     <br/>
     <cfinput type="submit" name="submit" value="Submit">
</cfform>

On the processing page, a query populates an interactive PDF form from the cfdocexamples database. The interactive
PDF form is embedded in a PDF document created with the cfdocument tag. The PDF document comprises three
sections: the cfdocumentsection tags define the first and last sections of the document; the cfpdfform tag defines
the second section embedded in the PDF document. Each section starts a new page in the PDF document. The Print
button on the PDF form prints the entire document, including the pages in the sections before and after the interactive
PDF form.

<cfquery name="getEmpInfo" datasource="cfdocexamples">
SELECT * FROM EMPLOYEES
WHERE EMAIL = <cfqueryparam value="#FORM.username#">
</cfquery>


<!--- The following code creates a PDF document with headers and footers.
--->
<cfdocument format="pdf">
     <cfdocumentitem type="header">
     <font size="-1" align="center"><i>Nondisclosure Agreement</i></font>
     </cfdocumentitem>
     <cfdocumentitem type="footer">
     <font size="-1"><i>Page <cfoutput>#cfdocument.currentpagenumber#
of#cfdocument.totalpagecount#</cfoutput></i></font>
     </cfdocumentitem>


<!--- The following code creates the first section in the PDF document. --->
     <cfdocumentsection>
     <h3>Employee Nondisclosure Agreement</h3>
     <p>Please verify the information in the enclosed form. Make any of the necessary changes
     in the online form and click the <b>Print</b> button. Sign and date the last page. Staple
     the pages together and return the completed form to your manager.</p>
     </cfdocumentsection>


<!--- The following code embeds an interactive PDF form within the PDF document with fields
     populated by the database query. The cfpdpfform tag automatically creates a section in
     the PDF document. Do not embed the cfpdfform within cfdocumentsection tags. --->


     <cfpdfform action="populate" source="c:\forms\embed.pdf">
           <cfpdfsubform name="form1">
               <cfpdfformparam name="txtEmpName"



                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  928
Working with Documents, Charts, and Reports




                     value="#getEmpInfo.FIRSTNAME# #getEmpInfo.LASTNAME#">
               <cfpdfformparam name="txtDeptName" value="#getEmpInfo.DEPARTMENT#">
               <cfpdfformparam name="txtEmail" value="#getEmpInfo.IM_ID#">
               <cfpdfformparam name="txtPhoneNum" value="#getEmpInfo.PHONE#">
               <cfpdfformparam name="txtManagerName" value="Randy Nielsen">
          </cfpdfsubform>
    </cfpdfform>


<!--- The following code creates the last document section. Page numbering resumes in this
section. --->
    <cfdocumentsection>
    <p>I, <cfoutput>#getEmpInfo.FIRSTNAME# #getEmpInfo.LASTNAME#</cfoutput>, hereby attest
    that the information in this document is accurate and complete.</p>
    <br/><br/>
    <table border="0" cellpadding="20">
          <tr><td width="300">
          <hr/>
          <p><i>Signature</i></p></td>
          <td width="150">
          <hr/>
          <p><i>Today's Date</i></p></td></tr>
    </table>
    </cfdocumentsection>
</cfdocument>


Update PDF form example
The following example shows how ColdFusion lets you update a PDF form while retaining existing data. The
application lets a user create an office supply request from a blank form created in LiveCycle or modify an existing
supply request. The user has the option to submit the completed form as an e-mail attachment.

<!--- supplyReq1.cfm --->
<!--- The following code prefills fields in a blank form in LiveCycle and writes the prefilled
    form to a new file called NewRequest.pdf in the supplyReqs directory. --->
<cfpdfform source="SupplyReq.pdf" action="populate" destination="supplyReqs/NewRequest.pdf"
    overwrite="yes">
    <cfpdfsubform name="form1">
          <cfpdfformparam name="txtContactName" value="Constance Gardner">
          <cfpdfformparam name="txtCompanyName" value="Wild Ride Systems">
          <cfpdfformparam name="txtAddress" value="18 Melrose Place">
          <cfpdfformparam name="txtPhone" value="310-654-3298">
          <cfpdfformparam name="txtCity" value="Hollywood">
          <cfpdfformparam name="txtStateProv" value="CA">
          <cfpdfformparam name="txtZipCode" value="90210">
    </cfpdfsubform>
</cfpdfform>


<!--- The following code lets users choose an existing supply request form or create a new
    request from the NewRequest.pdf form. --->
<h3>Office Supply Request Form</h3>
<p>Please choose the office supply request form that you would like to open. Choose <b>New
    Supply Request</b> to create a request.</p>


<!--- The following code populates a list box in a form with files located in the specified
    directory. --->
<cfset thisDir = expandPath(".")>
<cfdirectory directory="#thisDir#/supplyReqs" action="list" name="supplyReqs">



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                         929
Working with Documents, Charts, and Reports




<cfif #supplyReqs.name# is "NewRequest.pdf">
    <cfset #supplyReqs.name# = "---New Supply Request---">
</cfif>


<cfform name="fileList" action="supplyReq2.cfm" method="post">
    <cfselect name="file" query="supplyReqs" value="name" display="name"
         required="yes" size="8" multiple="no"/><br/>
    <cfinput type="submit" name="submit" value="OK">
</cfform>


<!--- supplyReq2.cfm --->
<!--- The following code displays the PDF form that the user selected. --->
<cfif #form.file# is "---New Supply Request---">
    <cfset #form.file# = "NewRequest.pdf">
</cfif>
<cfpdfform source="supplyReqs/#form.file#" action="populate"/>



<!--- supplyReq3.cfm --->
<!--- The following code reads the PDF file content from the submitted PDF form. --->
<cfpdfform source="#PDF.content#" action="read" result="fields"/>


<!--- The following code writes the PDF form to a file and overwrites the file if it exists.
--->
<cfpdfform action="populate" source="#PDF.content#"
destination="SupplyReqs/supplyReq_#fields.form1.txtRequestNum#.pdf" overwrite="yes"/>


<!--- The following code customizes the display based on field values extracted from the PDF
    form. --->
<p><cfoutput>#fields.form1.txtRequester#</cfoutput>,</p>
<p>Your changes have been recorded for supply request
<cfoutput>#fields.form1.txtRequestNum#</cfoutput>.</p>
<p>If the form is complete and you would like to submit it to
<cfoutput>#fields.form1.txtContactName#</cfoutput> for processing, click <b>Submit</b>.


<!--- The following code gives the option to e-mail the submitted form as an attachment or
    return to the home page. --->
<cfform name="send" method="post" action="supplyReq4.cfm">
    <cfinput type="hidden"
value="SupplyReqs/supplyReq_#fields.form1.txtRequestNum#.pdf" name="request">
    <cfinput type="hidden" value="#fields.form1.txtRequester#" name="requester">
    <cfinput type="submit" value="Submit" name="Submit">
</cfform>
<p>If you would like to modify your request or choose another request,
<a href="supplyReq1.cfm">click here</a>.</p>


<!--- supplyReq4.cfm --->
<!--- The following code sends the completed PDF form as an attachment to the person
    responsible for processing the form. --->
<p>Your request has been submitted.</p>
<cfmail from="#form.requester#@wildride.com" to="cgardener@wildride.com"
    subject="see attachment">
    Please review the attached PDF supply request form.
    <cfmailparam file="#form.request#">
</cfmail>




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     930
Working with Documents, Charts, and Reports




Assembling PDF Documents

You can use Adobe ColdFusion to assemble PDF documents. You create a unified document from multiple source files
or pages from multiple files by using the cfpdf and cfpdfparam tags.


About assembling PDF documents
You use the cfpdf tag to assemble PDF documents in Adobe ColdFusion. The tag provides several actions for creating
unified output files from multiple sources, as the following table shows:


Action                     Description

addWatermark               Adds a watermark image to one or more pages in a PDF document.

deletePages                Deletes one or more pages from a PDF document.

addheader                  Adds header to a PDF document.

addfooter                  Adds footer to a PDF document.

removeheaderfooter         Removes header and footer from a PDF document.

optimize                   Reduces the quality of PDF documents by image downsampling and removing unused objects.

extracttext                Extracts text from the specified pages or the entire PDF documents

extractimage               Extracts images from the specified pages or the entire PDF document

transform                  Performs page level transformations

getInfo                    Extracts information associated with the PDF document, such as the author, title, and creation date.

merge                      Assembles PDF documents or pages from PDF source files into one output file.

processddx                 Extends the cfpdf tag by providing a subset of Adobe LiveCycleTM Assembler functionality. This action is the
                           default.

protect                    Password-protects and encrypts a PDF document.

read                       Reads a PDF document into a ColdFusion variable.

removeWatermark            Removes watermarks from specified pages in a PDF document.

setInfo                    Sets the Title, Subject, Author, and Keywords for a PDF document,

thumbnail                  Generates thumbnail images from specified pages in a PDF document.

write                      Writes PDF output to a file. Also use to flatten forms created in Acrobat and linearize documents.


Note: You cannot use the cfpdf tag to create a PDF document from scratch. To create a PDF document from HTML
content, use the cfdocument tag. Also, you can use Report Builder to generate a report as a PDF document. Instead of
writing a PDF document to file, you can specify a PDF variable generated as the source for the cfpdf tag.

All but one of the cfpdf tag actions provide shortcuts to common tasks; for example, with one line of code, you can
add a watermark image to one or more pages in an output file, merge all the PDF documents in a directory into a single
output file, or password-protect a PDF document. ColdFusion provides two ways to extend the functionality of the
cfpdf tag: the cfpdfparam tag and the processddx action.

You use the cfpdfparam tag only with the merge action of the cfpdf tag. The cfpdfparam tag gives you more control
over which files are included in the output file; for example you can merge pages from multiple files in different
directories.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                            931
Working with Documents, Charts, and Reports




The processddx action extends the cfpdf tag by providing a subset of Adobe LiveCycle Assembler functionality. You
use the processddx action to process Document Description XML (DDX) instructions explained in "Using DDX to
perform advanced tasks" on page 943. Using DDX instructions requires more coding, but it lets you perform complex
tasks, such as generating a table of contents and adding automatic page numbers.

Also, ColdFusion provides three functions for PDF file, DDX file, and PDF variable verification:


 Function                      Description

 IsDDX                         Determines whether a DDX file, path, and instructions are not null and are valid. Also verifies that ColdFusion
                               supports the schema used for the DDX instructions.

 IsPDFFile                     Determines whether a PDF source file, path, and version are valid and supported on the server running
                               ColdFusion. Also verifies whether a PDF file is corrupted.

 IsPDFObject                   Determines whether a PDF object stored in memory is valid. Also verifies the contents of PDF variables
                               generated by the cfdocument and cfpdf tags.


The following table describes a few document assembly tasks that you can perform with ColdFusion:


 Task                                                         Action

 Add a generated table of contents to a PDF document          cfpdf action="processddx" with the TableOfContents DDX element

                                                              cfpdf action="extracttext" can also be used.

 Add automatic page numbers to a PDF document                 cfpdf action="processddx" with the _PageNumber and
                                                              _LastPagenumber built-in keys. Valid only in the Header and Footer DDX
                                                              elements.

 Add headers and footers to a PDF document                    cfpdf action="processddx" with the Header and Footer DDX
                                                              elements

                                                              or

                                                              cfpdf action="addheader" and cfpdf action="addfooter"

 Add or remove watermarks                                     cfpdf action="processddx" with the Watermark and Background DDX
                                                              elements

                                                              cfpdf action="addWatermark" and cfpdf
                                                              action="removeWatermark"

 Change the encryption algorithm for PDF documents            cfpdf action="protect" encrypt="encryption algorithm"

 Change user permissions on a PDF document                    cfpdf action="protect"
                                                              newOwnerPassword="xxxxx"permissions="comma-separated list"

 Delete pages from a PDF document                             cfpdf action="deletePages"

 Extract text from a PDF document and export it to an XML     cfpdf action="processddx" with the DocumentText DDX element
 file

 Flatten (remove interactivity from) forms created in Acrobat cfpdf action="write" flatten="yes"

 Generate thumbnail images from PDF document pages            cfpdf action="thumbnail"pages="page numbers"

 Linearize PDF documents for faster web display               cfpdf action="write" saveOption="linear"

 Merge pages and page ranges from multiple documents in       cfpdf action="merge" with multiple cfpdfparam tags
 different locations into one PDF document

 Merge PDF documents in a directory into one PDF              cfpdf action="merge" directory="path"
 document




                                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      932
Working with Documents, Charts, and Reports




 Task                                                 Action

 Password-protect PDF documents                       cfpdf action="protect" newUserPassword="xxxx"

 Set the initial view for a PDF document              cfpdf action="processddx" with the InitialViewProfile DDX
                                                      element

 Create different versions of a PDF document          Duplicate function to clone PDF variables



Using shortcuts for common tasks
You use the cfpdf tag actions to perform shortcuts to common PDF document assembly and manipulation.


Adding and removing watermark images
Use the addWatermark and removeWatermark actions to add and remove watermarks from PDF documents. You can
create a watermark and apply it to a PDF document in one of the following ways:

* Use an image file as a watermark.

* Specify a variable that contains an image file.

* Specify a ColdFusion image.

* Use the first page of a PDF document as a watermark.

Note: You can also use the Watermark or Background DDX elements with the processddx action to create a text-string
watermark. For more information, see "Using DDX to perform advanced tasks" on page 943.


Using an image file as a watermark
The following example shows how to specify an image file as a watermark:

<cfpdf action="addWatermark" source="artBook.pdf"
      image="../cfdocs/images/artgallery/raquel05.jpg" destination="output.pdf"
      overwrite="yes">

By default, ColdFusion centers the image on the page, sets the opacity of the image to 3 out of 10 (opaque), and displays
the image in the background of each page in the output file. In the following example, ColdFusion displays the
watermark in the foreground, offset 100 pixels from the left margin of the page and 100 pixels from the bottom margin
of the page. Because the opacity is set to 1, the image does not obscure the page content.

<cfpdf action="addWatermark" source="artBook.pdf"
      image="../cfdocs/images/artgallery/raquel05.jpg" destination="output.pdf"
      overwrite="yes" foreground="yes" opacity=1 showOnPrint="no" position="100,100">

For a complete list of attributes and settings, see the cfpdf tag in the CFML Reference.

With the ColdFusion 9 release, the addWatermark action now supports the rgb and argb formats also. The following
example shows that if you set the parameters for a new image to rgb or argb and then use the cfpdf
action=addwatermark, ColdFusion allows this action:

<!---setting the argb format for myImage--->
<cfset myImage = ImageNew("",200,200,"argb","gray")>


<!---adding watermark for myImage--->
<cfpdf action="addwatermark" rotation="45" foreground="true" image="#myImage#"
source="RemoveArts.pdf" destination="dest.pdf"                  overwrite="yes">




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 933
Working with Documents, Charts, and Reports




Using a variable that contains an image file
You can specify a variable that contains an image as a watermark. The following example shows how to create a form
from which the user can select an image:

<!--- The following code creates a form where you can choose an image to use
     as a watermark. --->
<h3>Choosing a Watermark</h3>
<p>Please choose the image you would like to use as a watermark.</p>


<!--- Create the ColdFusion form to select an image. --->
<table>
<cfform action="addWatermark2.cfm" method="post"
enctype="multipart/form-data">
     <tr>
           <td><img src="../cfdocs/images/artgallery/maxwell01.jpg"/><br/>
           <cfinput type="radio" name="art" value="../cfdocs/images/artgallery/maxwell01.jpg"
                 checked="yes">
           Birch Forest</td>
           <td><img src="../cfdocs/images/artgallery/raquel05.jpg"/><br/>
           <cfinput type="radio" name="art" value="../cfdocs/images/artgallery/raquel05.jpg">
                 Lounging Woman</td>
           <td><img src="../cfdocs/images/artgallery/jeff01.jpg"/><br/>
           <cfinput type="radio" name="art"
                 value="../cfdocs/images/artgallery/jeff01.jpg">Celebration</td>
           <td><img src="../cfdocs/images/artgallery/paul01.jpg"/><br/>
           <cfinput type="radio" name="art"
                 value="../cfdocs/images/artgallery/paul01.jpg">Guitarist
           </td>
     </tr>
</table>
<br/>
<cfinput type="Submit" name="submit" value="Submit"></p>
</cfform>

The processing page uses the image selected from the form as the watermark for a PDF file:

<!--- ColdFusion applies the image selected from the form as the watermark in a PDF document
     by using the input variable form.art. --->
<cfpdf action="addwatermark" source="check.pdf" image="#form.art#" destination="output.pdf"
     foreground="yes" overwrite="true">
<p>The watermark has been added to your personalized checks.</p>


Using a ColdFusion image as a watermark
You can specify a ColdFusion image as a watermark. You can extract an image from a database and manipulate the
image in memory, but you don't have to write the manipulated image to a file. Instead, you can apply the manipulated
image as a watermark in a PDF document.

In the following example, the first ColdFusion page extracts images from a database and populates a pop-up menu with
the titles of the artwork:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                              934
Working with Documents, Charts, and Reports




<!--- Create a query to extract artwork from the cfartgallery database. --->
<cfquery name="artwork" datasource="cfartgallery">
SELECT ARTID, ARTNAME, LARGEIMAGE
FROM ART
ORDER BY ARTNAME
</cfquery>


<!--- Create a form that lists the artwork titles generated by the query. Set the value to
     LARGEIMAGE so that the image file is passed to the processing page. --->
<cfform action="addWatermarkB.cfm" method="post">
<p>Please choose a title:</p>
<cfselect name="art" query="artwork" display="ARTNAME" value="LARGEIMAGE" required="yes"
     multiple="no" size="8">
</cfselect>
<br/>
<cfinput type="submit" name="submit" value="OK">
</cfform>

The action page generates a ColdFusion image from the selected file by using the cfimage tag. The ImageScaleToFit
function resizes the image and applies the bicubic interpolation method to improve the resolution. To use the
manipulated image as a watermark, specify the image variable, as the following example shows:

<!--- Verify that an image file exists and is in a valid format. --->
<cfif IsImageFile("../cfdocs/images/artgallery/#form.art#")>
<!--- Use the cfimage tag to create a ColdFusion image from the file chosen from the list. --->
<cfimage source="../cfdocs/images/artgallery/#form.art#" action="read" name="myWatermark">


<!--- Use the ImageScaleToFit function to resize the image by using the bicubic interpolation
     method for better resolution. --->
<cfset ImageScaleToFit(myWatermark,450,450,"bicubic")>


<!--- Use the ColdFusion image variable as the watermark in a PDF document. --->
<cfpdf action="addWatermark" source="title.pdf" image="#myWatermark#"
     destination="watermarkTitle.pdf" overwrite="yes">
<cfelse>
     <p>I'm sorry, no image exists for that title. Please click the Back button and try
           again.</p>
</cfif>

For more information on ColdFusion images, see "Creating and Manipulating ColdFusion Images" on page 959.


Creating a text image and using it as a watermark
You can use the ImageDrawText function to create a text image in ColdFusion and apply the image as a watermark,
as the following example shows:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 935
Working with Documents, Charts, and Reports




<!--- Create a blank image that is 500 pixels square. --->
<cfset myImage=ImageNew("",500,500)>
<!--- Set the background color for the image to white. --->
<cfset ImageSetBackgroundColor(myImage,"white")>
<!---Clear the rectangle specified on myImage and apply the background color. --->
<cfset ImageClearRect(myImage,0,0,500,500)>
<!--- Turn on antialiasing. --->
<cfset ImageSetAntialiasing(myImage)>


<!--- Draw the text. --->
<cfset attr=StructNew()>
<cfset attr.size=50>
<cfset attr.style="bold">
<cfset attr.font="Verdana">
<cfset ImageSetDrawingColor(myImage,"blue")>
<cfset ImageDrawText(myImage,"PROOF",100,250,attr)>


<!--- Write the text image to a file. --->
<cfimage action="write" source="#myImage#" destination="text.tiff" overwrite ="yes">


<!--- Use the text image as a watermark in the PDF document. --->
<cfpdf action="addwatermark" source="c:/book/1.pdf" image="text.tiff"
     destination="watermarked.pdf" overwrite="yes">

For more information on ColdFusion images, see "Creating and Manipulating ColdFusion Images" on page 959. For
an example of using DDX elements to create a text-string watermark, see "Adding text-string watermarks" on
page 949.


Using a PDF page as a watermark
Use the copyFrom attribute to create a watermark from the first page of a PDF file and apply it to another PDF
document. In the following example, ColdFusion creates a watermark from the first page of image.PDF, applies the
watermark to the second page of artBook.pdf, and writes the output to a new file called output.pdf:

<cfpdf action="addWatermark" copyFrom="image.pdf" source="artBook.pdf" pages="2"
     destination="output.pdf" overwrite="yes">

In this example, image.pdf appears in the background of the second page of artBook.pdf. ColdFusion does not change
the size of the watermark image to fit the page. The page used as a watermark can contain text, graphics, or both.


Removing watermarks
Use the removeWatermark action to remove a watermark from one or more pages in a PDF document. The following
example shows how to remove a watermark from the entire PDF document and write the document to a new output file:

<cfpdf action="removeWatermark" source="artBook.pdf" destination="noWatermark.pdf">

The following example shows how to remove a watermark from the first two pages of a PDF document and overwrite
the source document:

<cfpdf action="removeWatermark" source="artBook.pdf" destination="artBook.pdf"
     overwrite="yes" pages="1-2">

Because the source and the destination are the same and the overwrite attribute is set to yes, ColdFusion overwrites
the source file with the output file.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    936
Working with Documents, Charts, and Reports




Optimizing PDF documents
To optimize a PDF document you can reduce the quality of the document. To reduce the quality of a PDF document
you can either downsample the images or remove unused objects from the document.

To downsample images the algos attribute is used with values bilinear, bicubic, and nearest_neighbour. The
following code snippet generates a PDF after image downsampling:

<cfpdf action = "optimize" algo = "bicubic" source = "..\myBook.pdf" name = #myBook#>

To discard unused objects such as comments, JavaScripts, attachments, bookmarks, and metadata from your PDF
document using the following attributes with optimize action:

<cfpdf action = "optimize"
     noJavaScript
     noThumbnails
     noBookmarks
     noComments
     noMetadata
     noFileAttachments
     noLinks
     nofonts>


Optimizing page count using encodeall
The new encodeall attribute encodes all the unencoded streams in the source. However, it does not discriminate
between dumb encodings like LZW and encodings like flate, so only unencoded streams get flate encoded.

Example:

<cfpdf action=write source="./inputFiles/Source.pdf" destination="./outputFiles/Output.pdf"
encodeAll="yes">


Adding and deleting headers and footers from a PDF
You can add a header and footer to a PDF document using the addheader and addfooter actions, as shown in the
following snippet:

<!---addfooter--->
<cfpdf action = "addfooter"
source = "../myBook.pdf"
destination = "../myBookwithfooter.pdf"
image = "adobelogo.JPG" // Use this attribute to add an image in the footer
align = "right"> // By default, the alignemnt is center


<!---addheader--->
     <cfpdf action = "addheader"
     source = "../myBook.pdf"
     destination = "../myBookwithheader.pdf"
     text = "Adobe"
     align = "left">

Specify the source where the PDF document is located and the destination where the new PDF document with the
header and footer will be saved.

You can also specify an image or text that you need to insert in the footer along with various other attributes such as
align, bottommargin, leftmargin, numberformat, and opacity.

To remove header and footer from a PDF document, use the removeheaderfooter action, as shown in the following
snippet:



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       937
Working with Documents, Charts, and Reports




<cfpdf action = "removeheaderfooter" source="..\mybook.pdf" destination = "new.pdf">

Use this action to remove the header and footer from a PDF document or from specified pages in a document.


Extracting images and text
You can extract text and images from a PDF document using the extracttext and extractimage actions.

The extracttext action extracts all words from the specified page numbers in the PDF document, as shown in the
following code snippet:

<cfpdf action = "extracttext" source = "../myBook.pdf" pages = "5-20, 29, 80" destination
="../adobe/textdoc.txt"

The extractimage action extracts all images from the specified page number in a PDF document, as shown in the
following code snippet:

<cfpdf action = "extractimage" source = "../myBook.pdf" pages = "1-200" destination =
"..\mybookimages" imageprefix = "mybook">

The images are extracted and saved in the directory that you specify in the destination attribute. You can specify a
prefix for the images (imageprefix) being extracted, otherwise the system prefixes the image name similar to "cf+page
number". To save the images in a specific format, use the format attribute.


Performing page level transformations
You can scale a page, specify the position, and rotation values for pages in a PDF document using the transform
action. This action has four attributes that define the size (hscale, vscale), position(position), and rotation (rotation)
of a page. The following code snippet shows the usage. The value for rotation needs to be in steps (0, 90, 180, 270). If
you specify any other value, the system generates an error.

<cfpdf action = "transform"
source = "..\myBook.pdf"
destination = "..\new\myBook.pdf">
hscale = ".5"
vscale = ".15"
position = "8, 10"
rotation = "180">


Deleting pages from a PDF document
Use the deletepages action to remove pages from a PDF document and write the result to a file. You can specify a
single page, a page range (for example, "81­97"), or a comma-separated list of pages to delete, as the following example
shows:

<cfpdf action="deletePages" source="myBook.pdf" pages="10-15,21,89"
     destination="abridged.pdf" overwrite="yes">


Protecting PDF files
Use the protect action to password-protect, set permissions, and encrypt PDF documents for security.


Setting passwords
ColdFusion supports two types of passwords: an owner password and a user password. An owner password controls
the ability to change the permissions on a document. When you specify an owner password, you set permissions to
restrict the operations users can perform, such as the ability to print a document, change its content, and extract
content. The following code creates an owner password for a document:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    938
Working with Documents, Charts, and Reports




<cfpdf action="protect" newOwnerPassword="splunge" source="timesheet.pdf"
     destination="timesheet.pdf" overwrite="yes" permissions="AllowPrinting">

To password-protect a document, set the user password. A user password controls the ability to open a document. If
you set a user password for a document, any person attempting to open the file is prompted to enter a password. The
following example sets the user password for a document:

<cfpdf action="protect" newUserPassword="openSesame" source="timesheet.pdf"
     destination="myTimesheet.pdf">

In the previous example, no restrictions apply to the PDF document after the user enters the correct password. To
restrict usage and password-protect a document, add a user password and an owner password. Use the owner
password to set the permissions, as the following example shows:

<cfpdf action="protect" newUserPassword="openSesame" newOwnerPassword="topSecret"
     source="timesheet.pdf" destination="myTimesheet.pdf" overwrite="yes"
     permissions="AllowPrinting>

In the previous example, a person who enters the user password (openSesame) can print the document only. A person
who enters the owner password (topSecret) is considered the owner of the document, has full access to the file, and
can change the user permissions for that file.


Setting permissions on a PDF document
To set permissions on a PDF document, specify a newOwnerPassword. Conversely, you cannot set the
newOwnerPassword without also setting the permissions attribute. Only an owner can change permissions or add
passwords. For a list of permissions that an owner can set for PDF documents, see cfpdf in the CFML Reference.

Except for all or none, owners can specify a comma-separated list of permissions on a document, as the following
example shows:

<cfpdf action="protect" permissions="AllowinPrinting,AllowDegradedPrinting,AllowSecure"
     source="timesheet.pdf" newOwnerPassword="private" newUserPassword="openSesame"
     destination="myTimesheet.pdf">

In this example, a user must enter the password openSesame before opening the PDF form. All users can print the
document at any resolution, but only the owner can modify the document or change the permissions.


Encrypting PDF files
When you specify the protect action for a PDF file, ColdFusion encrypts the file with the RC4 128-bit algorithm by
default. Depending on the version of Acrobat running on the ColdFusion server, you can set the encryption to protect
the document contents and prevent search engines from accessing the PDF file metadata.

You can change the encryption algorithm by using the encrypt attribute. For a list of supported encryption
algorithms, see cfpdf in the CFML Reference.

The following example changes the password encryption algorithm to RC4 40-bit encryption:

<cfpdf action="protect" source="confidential.pdf" destination="confidential.pdf"
     overwrite="yes" newOwnerPassword="paSSword1" newUserPassword="openSesame"
     encrypt="RC4_40">

To prevent ColdFusion from encrypting the PDF document, set the encryption algorithm to none, as the following
example shows:

<cfpdf action="protect" source="confidential.pdf" encrypt="none" destination="public.pdf">

To decrypt a file, provide the owner or user password and write the output to another file. The following code decrypts
the confidential.pdf file and writes it to a new file called myDocument.pdf:



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     939
Working with Documents, Charts, and Reports




<cfpdf action="write" source="confidential.pdf" password="paSSword1"
     destination="myDocument.pdf">


Managing PDF document information
To retrieve information stored with a source PDF document, such as the creation date, the application used to create
the PDF document, and the name of the person who created the document, use the getInfo action. For a list of data
elements, see PDF file information elements in the CFML Reference.

Use the setInfo action to specify information, such as the author, subject, title, and keywords associated with the
output file. This information is useful for archiving and searching PDF documents. PDF document information is not
displayed or printed with the document.

The following example shows how to set keywords for tax documents. The information is useful for assembling the
documents based on the tax filing requirements for different business types (Sole Proprietor, Partnership, and S
Corporation). Some business types share the same forms and documents. By setting the business type keywords for
each document, you can store the documents in one directory and search them based on keyword values. The
following code sets three keywords for the p535.pdf tax booklet:

<cfset taxKeys=StructNew()>
<cfset taxKeys.keywords="Sole Proprietor,Partnership,S Corporation">
<cfpdf action="setInfo" source="taxes\p535.pdf" info="#taxKeys#"
     destination="taxes\p535.pdf" overwrite="yes">

When you use the setInfo action, ColdFusion overwrites any existing information for that key-value pair. In the
previous example, if the pc535.pdf document contained a keyword of "tax reference", ColdFusion overwrites that
keyword with "Sole Proprietor, Partnership, S Corporation".

To retrieve all of the information associated with the tax file, use the cfdump tag with the getInfo action, as the
following example shows:

<cfpdf action="getInfo" source="taxes\p535.pdf" name="taxInfo">
<cfdump var="#taxInfo#">

To retrieve just the keywords for the PDF document, use this code:

<cfpdf action="getInfo" source="taxes\p535.pdf" name="taxInfo">
<cfoutput>#taxInfo.keywords#</cfoutput>


Using the name attribute with write action
You can now use the name attribute with <cfpdf action = "write"> and specify a variable name for your PDF document.
For example:

<cfpdf action="write" source="myBook" name=#myBook# version="1.4">

This feature is available with both cfpdf and cfpdfform tags.


Merging PDF documents
ColdFusion lets you merge PDF documents in the following ways:

* Merge all of the PDF files in a specified directory.

* Merge a comma-separated list of PDF files.

* Merge individual PDF files, and pages within those files, explicitly, even if the source files are stored in different
   locations.

* Merge the contents of a PDF variable generated by the cfdocument tag or a cfpdf tag




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        940
Working with Documents, Charts, and Reports




* Create PDF packages

To merge the contents of a directory, use the merge action and specify the directory where the source PDF files are
located, as the following example shows:

<cfpdf action="merge" directory="c:/BookFiles" destination="myBook.pdf" overwrite="yes">

By default, ColdFusion merges the source files in descending order by timestamp. You can control the order in which
the PDF files are added to the book by setting the order and ascending attributes. The following code merges the files
in ascending order according to the timestamp on the files:

<cfpdf action="merge" directory="c:/BookFiles" destination="myBook.pdf" order="name"
      ascending="yes" overwrite="yes">

By default, ColdFusion continues the merge process even if it encounters a file that is not a valid PDF document in the
specified directory. To override this setting, set the stopOnError attribute to yes, as the following example shows:

<cfpdf action="merge" directory="c:/BookFiles" destination="myBook.pdf" order="time"
      ascending="yes" overwrite="yes" stopOnError="yes">

You can merge a comma-separated list of PDF files. To do this merge, specify the absolute path for each file, as the
following example shows:

<cfpdf action="merge"
      source="c:\coldfusion\wwwroot\lion\Chap1.pdf,c:\coldfusion\wwwroot\lion\Chap2.pdf"
      destination="twoChaps.pdf" overwrite="yes">

For more control over which files are added to the merged document, use the cfpdfparam tag with the cfpdf tag. The
cfpdfparam tag merges documents or pages from documents located in different directories into a single output file.
When you use the cfpdfparam tag, the PDF files are added to the output file in the order they appear in the code. In
the following example, the cover, title, and copyright pages are followed by the first five pages of the introduction, then
all of the pages in Chapter 1, and then the first page followed by pages 80­95 in Chapter 2:

<!--- Use the cfdocument tag to create PDF content and write the output to a variable called
coverPage.--->
<cfdocument format="PDF" name="coverPage">
<html>
<body>
      <h1>Cover page</h1>
      <p>Please review the enclosed document for technical accuracy and completeness.</p>
</body>
</html>
</cfdocument>


<!--- Use the cfpdf tag to merge the cover page generated in ColdFusion with pages from PDF
files in different locations. --->
<cfpdf action="merge" destination="myBook.pdf" overwrite="yes" keepBookmark="yes">
      <cfpdfparam source="coverPage">
      <cfpdfparam source="title.pdf">
      <cfpdfparam source="e:\legal\copyright.pdf">
      <cfpdfparam source="boilerplate\intro.pdf" pages="1-5">
      <cfpdfparam source="bookfiles\chap1.pdf">
      <cfpdfparam source="bookfiles\chap2.pdf" pages="1,80-95">
</cfpdf>

Because the keepbookmark attribute is set to yes, ColdFusion retains the bookmarks from the source documents in
the output file.

Note: You cannot use the cfpdf tag to create bookmarks in a PDF document.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      941
Working with Documents, Charts, and Reports




Creating PDF Portfolios
You can now create PDF packages using the package = "true" attribute with the merge action:

<cfpdf action="merge" package="yes" destination="./myBook/adobetest.pdf" overwrite="yes">
      <cfpdfparam source="./inputFiles/c.zip" >
      <cfpdfparam source="./inputFiles/d.jpg" >
      <cfpdfparam source="./inputFiles/a.pdf" >
      <cfpdfparam source="./inputFiles/z.txt" >
      <cfpdfparam source="./inputFiles/MSTribute.pps" >
      <cfpdfparam source="./inputFiles/Test1.docx" >
      <cfpdfparam source="./inputFiles/NewMovie.mp3" >
      <cfpdfparam source="./inputFiles/testserver.air" >
      <cfpdfparam source="./inputFiles/123.xml" >
      <cfpdfparam source="./inputFiles/New_test_case.xls" >
</cfpdf>


Flattening forms created in Acrobat
Flattening forms involves removing the interactivity from the form fields. This action is useful for displaying form data
and presenting it without allowing it to be altered. Use the write action to flatten PDF forms, as the following example
shows:

<cfpdf action="write" flatten="yes" source="taxForms\f1040.pdf"
      destination="taxforms/flatForm.pdf" overwrite="yes">
      <a href="http://localhost:8500/Lion/taxforms/flatForm.pdf">1040 form</a>

Note: If you flatten a prefilled form created in Acrobat, ColdFusion flattens the form and removes the data from the form
fields. When you specify a form created in Acrobat as a source file for merge action of the cfpdf tag, ColdFusion
automatically flattens the form and removes data from the form fields, if the fields are filled in. ColdFusion does not
support flattening forms created in LiveCycle.


Linearizing PDF documents for faster web display
For efficient access of PDF files over the web, linearize PDF documents. A linearized PDF file is structured in a way
that displays the first page of the PDF file in the browser before the entire file is downloaded from the web server. As
a result linear PDF documents open almost instantly.

To linearize PDF documents, specify the saveOption attribute of the write action. The following example saves the
output file in linear format:

<cfpdf action="write" saveOption="linear" source="myBook.pdf" destination="fastBook.pdf"
      overwrite="yes">

Note: Linearization can decrease performance when handling large documents.


Generating thumbnail images from PDF pages
Use the thumbnail action to generate thumbnail images from PDF pages. If you specify only the source attribute with
the thumbnail action, ColdFusion automatically creates a directory relative to the CFM page called thumbnails where
it stores a generated JPEG image for each page in the document. The filenames are in the following format:

PDFdocumentName_page_n.JPG

For example, assume that the source file in the following example has 100 pages:

<cfpdf action="thumbnail" source="myBook.pdf">

ColdFusion generates the following files and stores them in the thumbnails directory:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                            942
Working with Documents, Charts, and Reports




myBook_page_1.jpg
myBook_page_2.jpg
myBook_page_3.jpg
...
myBook_page_100.jpg

If you specify a destination, ColdFusion does not create the thumbnails directory and stores the files in the specified
directory instead. The following code generates a thumbnail image called myBook_page_1.jpg from the first page of
myBook.pdf and stores it in a directory called images, which is relative to the CFM page:

<cfpdf action="thumbnail" source="myBook.pdf" pages="1" destination="images">

You change the prefix for the thumbnail filename and the change image file format to PNG or TIFF by specifying the
imagePrefix and format attributes. The following code generates a file called TOC_page_2.PNG from the second
page of myBook.pdf:

<cfpdf action="thumbnail" source="myBook.pdf" pages="2" imagePrefix="TOC" format="PNG"
     destination="images">

The following code generates thumbnails from a range of pages and changes the image background to transparent (the
default is opaque):

<cfpdf action="thumbnail" source="myBook.pdf" pages="1-10,15,8-16,59" transparent="yes"
     destination="\myBook\subset" imagePrefix="abridged">

For an example of how to generate thumbnail images and link them to pages in the source PDF document, see the cfpdf
tag in the CFML Reference.

ColdFusion 9 release has introduced some new attributes for the thumbnail action:

* hires: You can set this attribute to true to extract high resolution images from the page. If a document contains
   high resolution images and you want to retain the resolution of the images, then this attribute is useful.

   For example:

   <cfpdf action="thumbnail" source="./WORK/myBook.pdf" destination="./WORK/Testing_CFPDF"
   overwrite="true" hires="yes">

* overridepage: If you set this attribute to true, the thumbnail generated does not adhere to the PDF page size, but
   to the image size that is present in that page. If the image is not present, the size is set to the maximum size of the page.

* compresstiffs: Use this attribute to compress the size of the thumbnail images. As the name of the attribute
   suggests, it is only valid for the TIFF format. Following is an example:

   <cfpdf action="thumbnail" source="C:\WORK\myBook.pdf" destination="C:\WORK\Testing_CFPDF"
   overwrite="true" hires="yes" format="tiff" compresstiffs="yes">

* maxscale : Use this attribute to specify an integer value for the maximum scale of the thumbnail images.

* maxlength: Use this attribute to specify an integer value of the maximum length of the thumbnail images.

* maxbreadth: Use this attribute to specify an integer value of the maximum width of the thumbnail.

   The following example illustrates the use of maxscale, maxlength, and maxbreadth:

   <cfpdf action="thumbnail" source="./WORK/myBook.pdf" destination="./WORK/Testing_CFPDF"
   overwrite="true" format="jpg" maxscale="3" maxlength="300" maxbreadth="200" hires="yes"
   scale="100">

   Note: Typically, the value of the scale attribute is set to `100' when using the maxscale attribute.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   943
Working with Documents, Charts, and Reports




Using the Duplicate function to create versions of a PDF document
You can use the Duplicate function to clone PDF variables, which is an efficient way to create different versions of a
PDF document from a single source file. For example, you can customize PDF output based on your audience by
creating clones of a PDF variable and performing different actions on each clone. The following example shows how
to create a clone of a PDF document in memory, and create one version of the document with a watermark and another
version of the document where permissions are restricted:

<cfset filename="coldfusion.pdf">
<!--- This code reads a PDF document into a PDF variable called pdfVar1.
           --->
<cfpdf action="read" source="#filename#" name="pdfVar1">
<!--- This code uses the Duplicate function to create a clone of pdfVar1 called pdfVar2. --->
<cfset pdfVar2=Duplicate(pdfVar1)>
<!--- This code creates a watermarked version of the source PDF document from the pdfVar1
      variable. --->
<cfpdf action="addwatermark" source="pdfVar1" rotation="45" image="watermark.jpg"
      destination="watermark_coldfusion.pdf" overwrite="yes">
<!--- This code creates a protected version of the source PDF document from the pdfVar2
      variable. --->
<cfpdf action=protect source="pdfVar2" encrypt="RC4_128" permissions="none"
      newownerpassword="owner1" destination="restricted_coldfusion.pdf" overwrite="yes">



Using DDX to perform advanced tasks
LiveCycle Assembler is a server-based application that processes DDX, a declarative markup language used to define
PDF output files.

The processddx action lets you process DDX instructions without installing LiveCycle Assembler. In addition to all
of the functionality available with the other cfpdf actions, you can use DDX instructions to perform advanced tasks,
such as adding a generated table of contents to a PDF document, adding headers and footers with automatic page
numbers, and creating groups of PDF documents to which you can apply formatting instructions.

ColdFusion does not provide complete LiveCycle Assembler functionality. For a list of DDX elements that you can
access from ColdFusion, see Supported DDX elements in the CFML Reference.

For complete DDX syntax, see the Adobe LiveCycle Assembler Document Description XML Reference.


Using DDX instructions with ColdFusion
Although you can type DDX instructions directly in ColdFusion, typically you use an external DDX file. A DDX file
is basically an XML file with a DDX extension (for example, merge.ddx). You can use any text editor to create a DDX
file. The DDX syntax requires that you enclose the instructions within DDX start and end tags. In the following
example, the PDF element provides instructions for merging two PDF source files (Doc1 and Doc2) into a result file
(Out1):

<?xml version="1.0" encoding="UTF-8"?>
<DDX xmlns="http://ns.adobe.com/DDX/1.0/"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://ns.adobe.com/DDX/1.0/ coldfusion_ddx.xsd">
<PDF result="Out1">
<PDF source="Doc1"/>
<PDF source="Doc2"/>
</PDF>
</DDX>

In ColdFusion, you verify the source DDX file with the IsDDX function:



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     944
Working with Documents, Charts, and Reports




<!--- The following code verifies that the DDX file exists and the DDX instructions are
      valid. --->
<cfif IsDDX("merge.ddx")>

To implement the DDX instructions in ColdFusion, you create two structures: an input structure that maps the DDX
input instructions to the PDF source files, and an output structure that maps the DDX output instructions to a PDF
output file,

The following code maps two files called Chap1.pdf and Chap2.pdf to the Doc1 and Doc2 sources that you defined in
the DDX file:

<!--- This code creates a structure for the input files. --->
<cfset inputStruct=StructNew()>
<cfset inputStruct.Doc1="Chap1.pdf">
<cfset inputStruct.Doc2="Chap2.pdf">

The following code maps the output file called twoChaps.pdf to the Out1 result instruction that you defined in the
DDX file:

<!--- This code creates a structure for the output file. --->
<cfset outputStruct=StructNew()>
<cfset outputStruct.Out1="twoChaps.pdf">

To process the DDX instructions, you use the processddx action of the cfpdf tag, in which you reference the DDX
file, the input structure, and the output structure, as the following example shows:

<cfpdf action="processddx" ddxfile="merge.ddx" inputfiles="#inputStruct#"
      outputfiles="#outputStruct#" name="myBook">

The name attribute creates a variable that you can use to test the success or failure of the action. If the action is
successful, ColdFusion generates an output file with the name and location specified in the output structure. The
following code returns a structure that displays a success, reason for failure, or failure message (if the reason is
unknown) for each output file, depending on the result:

<cfdump var="#myBook#">

The previous example performs the same task as the merge action in ColdFusion, as the following example shows:

<cfpdf action="merge" destination="twoChaps.pdf" overwrite="yes">
      <cfpdfparam source="Chap1.pdf">
      <cfpdfparam source="Chap2.pdf">
</cfpdf>
</cfif>

In this situation, it makes more sense to use the merge action because it is easier. DDX is useful when you have to
perform tasks that you can't perform with other actions in the cfpdf tag, or you require more control over specific
elements.


Adding a table of contents
You use DDX instructions to add a generated table of contents page to the PDF output file. Generating a table of
contents is useful if you are assembling documents from multiple sources. You can generate a table of contents that
contains active links to pages in the assembled PDF document. The following code shows how to create DDX
instructions to merge two documents and add a table of contents:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      945
Working with Documents, Charts, and Reports




<?xml version="1.0" encoding="UTF-8"?>
<DDX xmlns="http://ns.adobe.com/DDX/1.0/"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://ns.adobe.com/DDX/1.0/ coldfusion_ddx.xsd">
     <PDF result="Out1">
               <PDF source="DocumentTitle"/>
               <TableOfContents/>
               <PDF source="Doc1"/>
               <PDF source="Doc2"/>
     </PDF>
</DDX>

The TableOfContents element generates a table of contents from the PDF source elements that follow it. Order is
important: in the previous example, the table of contents appears on a separate page after the Title and before Doc 1
and Doc 2. The table of contents contains entries from Doc 1 and 2, but not from the title page, because the title page
precedes the table of contents in the order of instructions.

You do not reference the TableOfContents element on the corresponding ColdFusion page, as the following example
shows:

<!--- The following code verifies that the DDX file exists and the DDX instructions are
     valid. --->
<cfif IsDDX("makeBook.ddx")>


<!--- This code creates a structure for the input files. --->
<cfset inputStruct=StructNew()>
<cfset inputStruct.Title="Title.pdf">
<cfset inputStruct.Doc1="Chap1.pdf">
<cfset inputStruct.Doc2="Chap2.pdf">


<!--- This code creates a structure for the output file. --->
<cfset outputStruct=StructNew()>
<cfset outputStruct.Out1="Book.pdf">


<!--- This code processes the DDX instructions and generates the book. --->
<cfpdf action="processddx" ddxfile="makeBook.ddx" inputfiles="#inputStruct#"
     outputfiles="#outputStruct#" name="myBook">
</cfif>

ColdFusion generates a table of contents from the DDX instructions and inserts it in the PDF document in the location
that you provided in the DDX file. By default, the table of contents contains active links to the top-level bookmarks in
the merged PDF document.

You can change the default TableOfContents settings in the DDX file, as the following example shows:

<TableOfContents maxBookmarkLevel="infinite" bookmarkTitle="Table of Contents"
     includeInTOC="false"/>

Use the maxBookmarkLevel attribute to specify the level of bookmarks included on the table of contents page. Valid values
are infinite or an integer. Use the bookmarkTitle attribute to add a bookmark to the table of contents page in the
output file. The includeInTOC attribute specifies whether the bookmark title is included on the table of contents page.

Note: You cannot specify keywords as the source for DDX. For example, if you specify <PDF source = "Title"/> and then
add the <_BookmarkTitle/> tag in the DDX file, ColdFusion throws an exception. This is because, the _BookmarkTitle
tag is converted to TITLE and DDX is case-sensititve.

For more information on the TableOfContents element, see the Adobe LiveCycle Assembler Document Description
XML Reference.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     946
Working with Documents, Charts, and Reports




Adding headers and footers
To add headers and footers to a PDF document, specify the Header and Footer elements in the DDX file. The
following example specifies headers and footers for the PDF source called Doc2:

<?xml version="1.0" encoding="UTF-8"?>
<DDX xmlns="http://ns.adobe.com/DDX/1.0/"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://ns.adobe.com/DDX/1.0/ coldfusion_ddx.xsd">
     <PDF result="Out1">
          <PDF source="Title"/>
          <TableOfContents/>
          <PDF source="Doc2" >
               <Header>
                    <Right>
                         <StyledText><p>Right-justified header text</p></StyledText>
                    </Right>
                    <Left>
                         <StyledText><p>Left-justified header text</p></StyledText>
                    </Left>
               </Header>
               <Footer>
                    <Center>
                         <StyledText><p>Centered Footer</p></StyledText>
                    </Center>
               </Footer>
          </PDF>
     </PDF>
</DDX>

In this example, the Header and Footer elements apply only to Doc2 because they are contained within that PDF
source start and end tags; they do not apply to the table of contents or to the title page, which precede the Header and
Footer elements.


Formatting headers and footers
You use DDX instructions to perform the following tasks:

* Add automatic page numbers to headers and footers

* Use style profiles

* Group documents in the PDF output file


Adding automatic page numbers
To add automatic page numbers, use the _PageNumber and _LastPagenumber built-in keys within the Header or
Footer elements. The following code shows how to create footers with right-justified automatic page numbers:

<Footer>
     <Right>
          <StyledText>
               <p>Page <_PageNumber/> of <_LastPageNumber/></p>
          </StyledText>
     </Right>
</Footer>

The first page of the output file is numbered "Page 1 of n", and so on.

For more information on built-in keys, see the Adobe LiveCycle Assembler Document Description XML Reference.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    947
Working with Documents, Charts, and Reports




Using style profiles
The previous example uses the StyledText element to define inline text formatting. To define styles that you can
apply by reference, use the StyleProfile element. Style profiles let you apply a set of styles to different elements in
the PDF output file. The following code shows how to define a style profile for the table of contents Header:

<StyleProfile name="TOCheaderStyle">
      <Header>
           <Center>
               <StyledText>
               <p> color="red" font-weight="bold" font="Arial">Table of Contents</p>
               </StyledText>
           </Center>
      </Header>
</StyleProfile>

To apply the style profile, specify the StyleProfile name by using the styleReference attribute of the Header
element, as the following example shows:

<?xml version="1.0" encoding="UTF-8"?>
<DDX xmlns="http://ns.adobe.com/DDX/1.0/"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://ns.adobe.com/DDX/1.0/ coldfusion_ddx.xsd">
<PDF result="Out1">
      <PDF source="Title"/>
      <TableOfContents>
           <Header styleReference="TOCheaderStyle"/>
      </TableOfContents>
           <PDF source="Doc1"/>
           <PDF source="Doc2"/>
           <PDF source="Doc3"/>
           <PDF source="Doc4"/>
      </PDF>


      <StyleProfile name="TOCheaderStyle">
      <Header>
           <Center>
               <StyledText>
               <p> color="red" font-weight="bold" font="Arial">Table of Contents</p>
               </StyledText>
           </Center>
      </Header>
      </StyleProfile>
</DDX>


Grouping PDF documents
To apply a style profile to a group of documents in the output PDF file, use the PDFGroup element. The following
example shows how to create a group of chapters in the output file and apply a style profile to the Footer element for
all of the documents in the group:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                948
Working with Documents, Charts, and Reports




<?xml version="1.0" encoding="UTF-8"?>
<DDX xmlns="http://ns.adobe.com/DDX/1.0/"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://ns.adobe.com/DDX/1.0/ coldfusion_ddx.xsd">
<PDF result="Out1">
     <PageLabel prefix="page " format="Decimal"/>
     <PDF source="Title"/>
     <TableOfContents>
          ...
     </TableOfContents>
     <PDFGroup>
          <Footer styleReference="FooterStyle" />
          <PDF source="Doc1"/>
          <PDF source="Doc2"/>
          <PDF source="Doc3"/>
          <PDF source="Doc4"/>
     </PDFGroup>
     </PDF>


<StyleProfile name="FooterStyle">
     <Footer>
          <Left>
                <StyledText>
                <p font-size="9pt"><i>CFML Reference</i></p>
                </StyledText>
          </Left>
          <Right>
                <StyledText>
                <p font-size="9pt">Page <_PageNumber/> of <_LastPageNumber/></p>
                </StyledText>
          </Right>
     </Footer>
     </StyleProfile>
</DDX>

For a complete example, see "Using DDX instructions to create a book" on page 954.


Setting the initial view of a PDF document
To set the initial view of a PDF document, use the InitialViewProfile DDX element. Setting the initial view
determines how the PDF output file is displayed on the screen when it is first opened in Adobe Acrobat Reader. You
reference the InitialViewProfile by using the InitialView attribute of the PDFresult element, as the following
example shows:

<?xml version="1.0" encoding="UTF-8"?>
<DDX xmlns="http://ns.adobe.com/DDX/1.0/"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://ns.adobe.com/DDX/1.0/ coldfusion_ddx.xsd">
<PDF result="Out1" initialView="firstView">
          ...
<InitialViewProfile name="firstView" show="BookmarksPanel" magnification="FitPage"
     openToPage="2"/>
...
</DDX>

In this example, the first time the PDF document is displayed in Acrobat Reader, the document is opened to page two
and the bookmark panel is displayed. The magnification of the document is adjusted to fit the page.



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   949
Working with Documents, Charts, and Reports




For more information on IntialViewProfile settings, see the Adobe LiveCycle Assembler Document Description
XML Reference.


Adding text-string watermarks
You use the processddx action with the Background or Watermark DDX elements to create a text-string watermark.
Background elements appear in the background (behind the contents of the page); Watermark elements display in the
foreground (over the contents of the page). The syntax for both the elements is the same.

The following example shows the DDX page for using the text string "DRAFT" as a watermark. The watermark appears
on every page of the output file. By default, the watermark appears in the middle of the page. In this example, the
watermark is rotated 30 degrees:

<?xml version="1.0" encoding="UTF-8"?>
<DDX xmlns="http://ns.adobe.com/DDX/1.0/"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://ns.adobe.com/DDX/1.0/ coldfusion_ddx.xsd">
<PDF result="Out1">
          <Watermark rotation="30" opacity="65%">
               <StyledText><p font-size="50pt" font-weight="bold" color="lightgray"
                    font="Arial">DRAFT</p></StyledText>
          </Watermark>
...
     </PDF>
</DDX>

The following example shows how to add different backgrounds on alternating pages. The verticalAnchor attribute
displays the background text at the top of the page:

<?xml version="1.0" encoding="UTF-8"?>
<DDX xmlns="http://ns.adobe.com/DDX/1.0/"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://ns.adobe.com/DDX/1.0/ coldfusion_ddx.xsd">
<PDF result="Out1">
          <Background alternation="EvenPages" verticalAnchor="Top">
               <StyledText><p font-size="20pt" font-weight="bold" color="gray"
                    font="Arial">DRAFT</p></StyledText>
          </Background>
          <Background alternation="OddPages" verticalAnchor="Top">
          <StyledText><p font-size="20pt" font-weight="bold" color="gray"
               font="Arial"><i>Beta 1</i></p></StyledText>
          </Background>
...
     </PDF>
</DDX>

Instead of applying watermarks to the entire output file, you can apply them to individual source files. The following
example applies a different background to the first three chapters of a book. The fourth chapter has no background:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    950
Working with Documents, Charts, and Reports




<?xml version="1.0" encoding="UTF-8"?>
<DDX xmlns="http://ns.adobe.com/DDX/1.0/"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://ns.adobe.com/DDX/1.0/ coldfusion_ddx.xsd">
<PDF result="Out1">
               <PDF source="Doc1">
               <Background>
               <StyledText><p font-size="20pt" font-weight="bold" color="lightgray"
                    font="Arial">CHAPTER 1</p></StyledText>
               </Background>
         </PDF>
         <PDF source="Doc2">
               <Background>
                    <StyledText><p font-size="20pt" font-weight="bold"
                         color="lightgray" font="Arial">CHAPTER 2</p></StyledText>
               </Background>
         </PDF>
         <PDF source="Doc3">
               <Background>
                    <StyledText><p font-size="20pt" font-weight="bold"
                         color="lightgray" font="Arial">CHAPTER 3</p></StyledText>
               </Background>
         </PDF>
         <PDF source="Doc4"/>
    </PDF>
</DDX>

For more information on using DDX instructions to create watermarks, see the Adobe LiveCycle Assembler Document
Description XML Reference.


Extracting text from a PDF document
You can use the DocumentText DDX element to return an XML file that contains the text in one or more PDF
documents. As with the PDF element, you specify a result attribute the DocumentText element and enclose one or more
PDFsource elements within the start and end tags, as the following example shows:

<?xml version="1.0" encoding="UTF-8"?>
<DDX xmlns="http://ns.adobe.com/DDX/1.0/"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://ns.adobe.com/DDX/1.0/ coldfusion_ddx.xsd">
    <DocumentText result="Out1">
         <PDF source="doc1"/>
    </DocumentText>
</DDX>

The following code shows the CFM page that calls the DDX file. Instead of writing the output to a PDF file, you specify
an XML file for the output:




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  951
Working with Documents, Charts, and Reports




<cfif IsDDX("documentText.ddx">
     <cfset ddxfile = ExpandPath("documentText.ddx")>
     <cfset sourcefile1 = ExpandPath("book1.pdf")>
     <cfset destinationfile = ExpandPath("textDoc.xml")>


     <cffile action="read" variable="myVar" file="#ddxfile#"/>


     <cfset inputStruct=StructNew()>
     <cfset inputStruct.Doc1="#sourcefile1#">


     <cfset outputStruct=StructNew()>
     <cfset outputStruct.Out1="#destinationfile#">


     <cfpdf action="processddx" ddxfile="#myVar#" inputfiles="#inputStruct#"
outputfiles="#outputStruct#" name="ddxVar">


     <!--- Use the cfdump tag to verify that the PDF files processed successfully. --->
     <cfdump var="#ddxVar#">
</cfif>

The XML file conforms to a schema specified in doctext.xsd. For more information, see
http://ns.adobe.com/DDX/DocText/1.0

When you specify more than one source document, ColdFusion aggregates the pages into one file. The following
example shows the DDX code for combining a subset of pages from two documents into one output file:

<?xml version="1.0" encoding="UTF-8"?>
<DDX xmlns="http://ns.adobe.com/DDX/1.0/"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://ns.adobe.com/DDX/1.0/ coldfusion_ddx.xsd">
<DocumentText result="Out1">
     <PDF source="doc1" pages="1-10"/>
     <PDF source="doc2" pages="3-5"/>
</DocumentText>
</DDX>



FDF format support for AcroForms
PDF Acro Form now support the Forms Data Format (FDF). You can now export, import, and populate Acroforms
in FDF using the read and populate actions. The action attribute of the cfpdfform tag contains a new attribute called
fdfdata, where you provide the name of the PDF form to be exported or imported.

Following is an example of how can export a PDF form in FDF format using the read action:

<cfpdfform source= "acroform_export.pdf" action="read" fdfdata="abc.fdf" >
</cfpdfform>

To import form data:

<cfpdfform source= "write_acrroform.pdf" action="populate" fdfdata="abc.fdf"
destination="hello.pdf">
</cfpdfform>

Form data can now be populated in a PDF document using the populate action. The new fdf attribute with populate
allows you use the FDF format internally. Following code snippet illustrates this feature:




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 952
Working with Documents, Charts, and Reports




<cfpdfform source="acroform2.pdf" destinat ion="source_result1 7.pdf" action="populate"
overwrite="true" fdf ="true">
     <cfpdfsubform      name="Text1">
     <cfpdf subform name="0">
     <cfp dfformparam name="0" val ue="Test1.0.0">
     <cfpd fformparam name="1" value ="Test1.0.1">
     <cfpdfformparam name="2" value="Test1.0.2">
     </cfpdfsubform>
           <cfpdfsubform name="1">
           <cfpdfformparam name="0" value="Test1.1.0">
           <cfpdfformparam name="1" value="Test1.1.1">
           <cfpdfformparam name="2" value="Test1.1.2">
     </cfpdfsubform>


     </cfpdfsubform>
     <cfpdfsubform name="Text2">
     <cfpdfformparam name="0" value="Test2.0">
     <cfpdfformparam name="1" value="Test2.1">
     <cfpdfformparam name="2" value="Test2.2">
     <cfpdfformparam name="3" value="Test2.3">
     </cfpdfsubform><cfpdfformparam name="Text3" value="Test3">
     <cfpdfformparam name="Text4" value="Test4">
     <cfpdfformparam name="checkbox1" value="Yes">
     <cfpdfformparam name="listbox1" value="item4">
     <cfpdfformparam name="radiobutton1" value="2">
</cfpdfform>



Application examples
The following examples show you how to use the cfpdf tag to perform PDF document operations in simple
applications.


Merging documents based on a keyword search
The following example shows how to use the getInfo and merge actions to assemble a PDF document from multiple
tax files based on business type (Sole Proprietor, Partnership, or S Corporation). The application assembles the tax
forms and information booklets based on a radio button selection. Some tax forms and booklets apply to more than
one business type (for example, Partnership and S Corporations both use the tax form f8825.pdf). For instructions on
setting keywords for PDF documents, see "Managing PDF document information" on page 939.

This example shows how to perform the following tasks:

* Use the getInfo action to perform a keyword search on PDF files in a directory.

* Create a comma-separated list of files that match the search criteria.

* Use the merge action to merge the PDF documents in the comma-separated list into an output file.

The first CFM page creates a form for selecting the tax documents based on the business type:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      953
Working with Documents, Charts, and Reports




<h3>Downloading Federal Tax Documents</h3>
<p>Please choose the type of your business.</p>
<!--- Create the ColdFusion form to determine which PDF documents to merge. --->
<table>
<cfform action="cfpdfMergeActionTest.cfm" method="post">
     <tr><td><cfinput type="radio" name="businessType"
                 Value="Sole Proprieter">Sole Proprietor</td></tr>
     <tr><td><cfinput type="radio" name="businessType"
                 Value="Partnership">Partnership</td></tr>
     <tr><td><cfinput type="radio" name="businessType" Value="S Corporation">
                 S Corporation</td></tr>
     <cfinput type = "hidden" name = "selection required" value = "must make a selection">
     <tr><td><cfinput type="Submit" name="OK" label="OK"></td></tr>
     </tr>
</cfform>
</table>

The action page loops through the files in the taxes subdirectory and uses the getInfo action to retrieve the keywords
for each file. If the PDF file contains the business type keyword (Sole Proprietor, Partnership, or S Corporation),
ColdFusion adds the absolute path of the file to a comma-separated list. The merge action assembles the files in the list
into an output PDF file:

<!--- Create a variable for the business type selected from the form. --->
<cfset bizType=#form.businessType#>
<!--- Create a variable for the path of the current directory. --->
<cfset thisPath=ExpandPath(".")>


<!--- List the files in the taxes subdirectory. --->
<cfdirectory action="list" directory="#thisPath#\taxes" name="filelist">


<!--- The following code loops through the files in the taxes subdirectory. The getInfo
     action to retrieves the keywords for each file and determines whether the business type
     matches one of the keywords in the file. If the file contains the business type keyword,
     ColdFusion adds the file to a comma-separated list. --->
<cfset tempPath="">
<cfloop query="filelist">
     <cfset fPath="#thisPath#\taxes\#filelist.name#">
     <cfpdf action="GetInfo" source="#fPath#" name="kInfo"></cfpdf>
     <cfif #kInfo.keywords# contains "#bizType#">
          <cfset tempPath=#tempPath# & #fPath# & ",">
     </cfif>
</cfloop>


<!--- Merge the files in the comma-separated list into a PDF output file called "taxMerge.pdf".
--->
<cfpdf action="merge" source="#tempPath#" destination="taxMerge.pdf" overwrite="yes"/>


<h3>Assembled Tax Document</h3>
<p>Click the following link to view your assembled tax document:</p>
<a href="http://localhost:8500/Lion/taxmerge.pdf">
          <p>Your Assembled Tax Document</a></p>




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                              954
Working with Documents, Charts, and Reports




Using DDX instructions to create a book
The following example shows how to create a book using DDX instructions with the processddx action. Specifically,
it shows how to perform the following tasks:

* Merge several PDF documents into an output file.

* Add a generated table of contents page.

* Add headers and footers.

* Add automatic page numbers.

* Apply different styles to the table of contents and the body of the book.

The following code shows the DDX file:

<?xml version="1.0" encoding="UTF-8"?>
<DDX xmlns="http://ns.adobe.com/DDX/1.0/"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://ns.adobe.com/DDX/1.0/ coldfusion_ddx.xsd">
<PDF result="Out1">
     <PDF source="Doc0"/>
<TableOfContents maxBookmarkLevel="3" bookmarkTitle="Table of Contents"
     includeInTOC="false">
         <Header styleReference="TOCheaderStyle"/>
         <Footer styleReference="TOCFooterStyle"/>
     </TableOfContents>
     <PDFGroup>
         <Footer styleReference="FooterStyle"/>
         <PDF source="Doc1"/>
         <PDF source="Doc2"/>
         <PDF source="Doc3"/>
         <PDF source="Doc4"/>
     </PDFGroup>
     </PDF>


     <StyleProfile name="TOCheaderStyle">
     <Header>
         <Center>
               <StyledText>
               <p color="red" font-weight="bold" font="Arial">Table of Contents</p>
               </StyledText>
         </Center>
     </Header>
     </StyleProfile>


     <StyleProfile name="TOCFooterStyle">
     <Footer>
         <Right>
               <StyledText>
               <p font-size="9pt">Page <_PageNumber/> of <_LastPageNumber/></p>
               </StyledText>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   955
Working with Documents, Charts, and Reports




          </Right>
     </Footer>
     </StyleProfile>


     <StyleProfile name="FooterStyle">
     <Footer>
          <Left>
               <StyledText>
               <p font-size="9pt"><i>CFML Reference</i></p>
               </StyledText>
          </Left>
          <Right>
               <StyledText>
               <p font-size="9pt">Page <_PageNumber/> of <_LastPageNumber/></p>
               </StyledText>
          </Right>
     </Footer>
     </StyleProfile>


</DDX>

The following code shows the ColdFusion page that processes the DDX instructions:

<cfif IsDDX("Book.ddx")>
     <cfset inputStruct=StructNew()>
     <cfset inputStruct.Doc0="Title.pdf">
     <cfset inputStruct.Doc1="Chap1.pdf">
     <cfset inputStruct.Doc2="Chap2.pdf">
     <cfset inputStruct.Doc3="Chap3.pdf">
     <cfset inputStruct.Doc4="Chap4.pdf">


     <cfset outputStruct=StructNew()>
     <cfset outputStruct.Out1="myBook.pdf">


     <cfpdf action="processddx" ddxfile="book.ddx" inputfiles="#inputStruct#"
          outputfiles="#outputStruct#" name="ddxVar">


     <cfoutput>#ddxVar.Out1#</cfoutput>
</cfif>


Applying a watermark to a form created in Acrobat
The following example shows how to prefill an interactive Acrobat tax form and apply a text-string watermark to the
completed form that the user posted. Specifically, this example shows how to perform the following tasks:

* Use the cfpdfform and cfpdfformparam tags to populate a form created in Acrobat.

* Use the cfpdfform tag to write the output of a PDF post submission to a file.

* Use the cfpdfprocessddx action to apply a text-string watermark to the completed form.

Note: This example uses the cfdocexamples database and the 1040 and 1040ez Federal tax forms. A valid user name is
"cpeterson." To download the 1040 and 1040ez IRS tax forms used in this example, go to the IRS website. Open the forms
in Acrobat (not LiveCycle Designer) and add a submit button that points to the URL for the ColdFusion processing page.
Also, add a hidden field with a variable that contains a unique filename used for the completed tax form.

The first ColdFusion page creates a login form that prompts for the user name and Social Security Number:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               956
Working with Documents, Charts, and Reports




<!--- The following code creates a simple form for entering a user name and password. The
    code does not include password verification. --->


<h3>Tax Login Form</h3>
<p>Please enter your user name and your social security number.</p>
<cfform name="loginform" action="TaxFile2.cfm" method="post">
<table>
    <tr>
         <td>User name:</td>
         <td><cfinput type="text" name="username" required="yes"
                    message="A user name is required."></td>
    </tr>
    <tr>
         <td>SSN#:</td>
         <td><cfinput type="text" name="SS1" maxLength="3" size="3"
               required="yes" mask="999"> -
         <cfinput type="text" name="SS2" maxLength="2" size="2" required="yes"
               mask="99"> -
         <cfinput type="text" name="SS3" maxLength="4" size="4" required="yes"
               mask="9999"></td>
    </tr>
</table>
    <br/>
    <cfinput type="submit" name="submit" value="Submit">
</cfform>

The second ColdFusion page retrieves the user information from the cfdocexamples database. Also, it creates a pop-
up menu with a list of available tax forms:




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               957
Working with Documents, Charts, and Reports




<!--- The following code retrieves all of the employee information for the
                user name entered on the login page. --->
<cfquery name="getEmpInfo" datasource="cfdocexamples">
SELECT * FROM EMPLOYEES
WHERE EMAIL = <cfqueryparam value="#FORM.username#">
</cfquery>


<h3>Choose a tax form</h3>
<p>Hello <cfoutput>#getEmpInfo.firstname#</cfoutput>,</p>
<p>Please choose a tax form from the list:</p>
<!--- Create a pop-up menu with a list of tax forms. --->
<cfset thisPath=ExpandPath(".")>
<!--- Create a variable called filerID that is a combination of the username and the last
     three digits of the Social Security number. --->
<cfset filerID="#form.username#_#form.SS3#">
<cfdirectory action="list" name="taxForms" directory="#thisPath#/taxforms">
<cfform name="taxList" method="post" action="TaxFile3.cfm">
     <cfselect query="taxForms" value="name" size="10" required="yes" multiple="no"
           name="myTaxForm"/>
     <br/><br/>
     <cfinput type="Submit" name="OK" label="OK">
     <!--- Use hidden fields to pass the first name, last name, and the three parts of
           the SSN# to the tax form. Also, create a hidden field for the filerID variable. --->
     <cfinput type="hidden" name="FirstName" value="#getEmpInfo.FirstName#">
     <cfinput type="hidden" name="LastName" value="#getEmpInfo.LastName#">
     <cfinput type="hidden" name="Phone" value="#getEmpInfo.Phone#">
     <cfinput type="hidden" name="SS1" value="#form.SS1#">
     <cfinput type="hidden" name="SS2" value="#form.SS2#">
     <cfinput type="hidden" name="SS3" value="#form.SS3#">
     <cfinput type="hidden" name="taxFiler" value="#filerID#">
</cfform>

The third ColdFusion page uses the cfpdfform and cfpdfformparam tags to populate the tax form with the user
information. ColdFusion displays the tax prefilled tax form in the browser window where the user can complete the
rest of the form fields. When the user clicks the submit button, Acrobat sends the completed PDF form to the
ColdFusion processing page.

Note: To prefill forms, map each PDF form field name to the corresponding data element in a cfpdfformparam tag. To
view the form fields, open the form in Acrobat Professional and select Forms > Edit Forms in Acrobat. For more
information about prefilling forms, see "Manipulating PDF Forms in ColdFusion" on page 914.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 958
Working with Documents, Charts, and Reports




<!--- The following code populates the tax form template chosen from the list with
     information from the database query and the login form. Because no destination is
     specified, ColdFusion displays the interactive PDF form in the browser. A hidden field
     in the PDF form contains the name of the output file to write. It is a combination of
     the user name and the last three numerals of the user SSN#. The submit button added to
     the form created in Acrobat contains a URL to the ColdFusion processing page. --->


<cfpdfform source="taxForms/#form.myTaxForm#" action="populate">
     <cfif "taxForms/#form.myTaxForm#" is "taxForms/f1040.pdf">
         <cfpdfformparam name="f1_04(0)" value="#form.Firstname#">
         <cfpdfformparam name="f1_05(0)" value="#form.Lastname#">
         <cfpdfformparam name="f2_115(0)" value="#form.Phone#">
         <cfpdfformparam name="f1_06(0)" value="#form.SS1#">
         <cfpdfformparam name="f1_07(0)" value="#form.SS2#">
         <cfpdfformparam name="f1_08(0)" value="#form.SS3#">
         <cfpdfformparam name="filerID" value="#form.taxFiler#_1040">
     <cfelseif "taxForms/#form.myTaxForm#" is "taxForms/f1040ez.pdf">
         <cfpdfformparam name="f1_001(0)" value="#form.Firstname#">
         <cfpdfformparam name="f1_002(0)" value="#form.Lastname#">
         <cfpdfformparam name="f1_070(0)" value="#form.Phone#">
         <cfpdfformparam name="f1_003(0)" value="#form.SS1#">
         <cfpdfformparam name="f1_004(0)" value="#form.SS2#">
         <cfpdfformparam name="f1_005(0)" value="#form.SS3#">
         <cfpdfformparam name="filerID" value="#form.taxFiler#_1040ez">
     </cfif>
</cfpdfform>

The fourth ColdFusion page uses the cfpdfform tag to process the PDF post submission and generate an output file.
The filename is generated from the value of the hidden field in the tax form. The processddx action of the cfpdf tag
uses the DDX instructions in the watermark.ddx file to generate a text-string watermark and apply it to the form.

The following code shows the contents of the watermark.ddx file:

<?xml version="1.0" encoding="UTF-8"?>
<DDX xmlns="http://ns.adobe.com/DDX/1.0/"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://ns.adobe.com/DDX/1.0/ coldfusion_ddx.xsd">
<PDF result="Out1">
     <PDF source="Doc1">
         <Watermark rotation="30" opacity="65%">
               <StyledText><p font-size="85pt" font-weight="bold" color="gray"
                   font="Arial">FINAL</p></StyledText>
         </Watermark>
     </PDF>
     </PDF>
</DDX>


<!--- The following code reads the PDF file submitted in binary format and generates a result
     structure called fields. The cfpdfform populate action and the cfoutput tags reference
     the fields in the structure. --->
<cfpdfform source="#PDF.content#" action="read" result="fields"/>


<cfpdfform action="populate" source="#PDF.content#"
     destination="FiledForms\#fields.filerID#.pdf" overwrite="yes"/>


<!--- The following code verifies that the DDX file exists and the DDX instructions are
     valid. --->




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          959
Working with Documents, Charts, and Reports




<cfif IsDDX("watermark.ddx")>
      <!--- The following code uses the processddx action of the cfpdf tag to create a text-
            string watermark. --->


      <!--- This code creates a structure for the input files. --->
      <cfset inputStruct=StructNew()>
      <cfset inputStruct.Doc1="FiledForms\#fields.filerID#.pdf">


      <!--- This code creates a structure for the output file. --->
      <cfset outputStruct=StructNew()>
      <cfset outputStruct.Out1="FiledForms\#fields.filerID#.pdf">


      <!--- This code processes the DDX instructions and applies the watermark to the form. --->
      <cfpdf action="processddx" ddxfile="watermark.ddx" inputfiles="#inputStruct#"
outputfiles="#outputStruct#" name="Final">
</cfif>


<h3>Tax Form Completed</h3>
<p>Thank you for filing your tax form on line. Copy this URL to view or download your filed
      tax form:</p>
<cfoutput>
<a href="http://localhost:8500/Lion/FiledForms/#fields.filerID#.pdf">
      Link to your completed tax form</a>
</cfoutput>




Creating and Manipulating ColdFusion Images

You can use Adobe ColdFusion to create and manipulate images, retrieve and store images in a database, retrieve
image information for indexes and searches, convert images from one format to another, and write images to the hard
drive.


About ColdFusion images
ColdFusion lets you create and manipulate images dynamically. With ColdFusion, you can automate many image
effects and drawing functions that you perform manually in Adobe Photoshop or other imaging software packages
and integrate the images in your application. For example, contributors to a website can upload photos in different
formats. You can add a few lines of code to your ColdFusion application to verify the images, reformat the images to
a standard size and appearance, write the modified images to a database, and display the images in a browser.

The following table describes a few of the tasks you can perform with ColdFusion images:


 Task                                                      Functions and actions

 Verify whether a ColdFusion variable returns an image     IsImage function

 Verify whether a file is a valid image                    IsImageFile function

 Create thumbnail images                                   ImageScaleToFit function, the ImageResize function, or the resize
                                                           action of the cfimage tag

 Create a watermark                                        ImageSetDrawingTransparency function with any of the ImageDraw
                                                           functions and the ImagePaste function

 Get information about an image (for example, so you can   ImageGetHeight and the ImageGetWidth functions or the ImageInfo
 enforce size restrictions)                                function




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                             960
Working with Documents, Charts, and Reports




 Task                                                            Functions and actions

 Enforce compression on JPEG images                              quality attribute of the write action of the cfimage tag or the
                                                                 ImageWrite function

 Convert an image from one image file format to another (for     cfimage tag or ImageRead and ImageWrite functions
 example, convert a BMP file to a JPEG)

 Convert an image file to a Base64 string                        cfimage tag or the ImageWriteBase64 function

 Create a ColdFusion image from a Base64 string                  ImageReadBase64 function

 Insert a ColdFusion image as a Binary Large Object Bitmap       ImageGetBlob function within a cfquery statement
 (BLOB) in a database

 Create an image from a BLOB in a database                       cfimage tag or the ImageNew function with a cfquery statement

 Create an image from a binary object                            cffile tag to convert an image file to a binary object and then pass the
                                                                 binary object to the ImageNew function

 Create a Completely Automated Public Turing test to tell        captcha action of the cfimage tag
 Computers and Humans Apart (CAPTCHA) image



The ColdFusion image
A ColdFusion image is a construct that is native to ColdFusion. The ColdFusion image contains image data that it
reads from a source. The source can be an image file or another ColdFusion image, which is expressed as a ColdFusion
image variable. The ColdFusion image variable lets you manipulate information dynamically in memory. Optionally,
you can write a ColdFusion image to a file, to a database column, or to a browser.


The cfimage tag
You use the cfimage tag to create a ColdFusion image and as a shortcut to commonly performed image functions,
such as resizing an image, adding a border to an image, and converting an image to a different file format. You can use
the cfimage tag independently or in conjunction with Image functions. You can pass a ColdFusion image created with
the cfimage tag to one or more Image functions to perform complex image manipulation operations.

The following table summarizes the cfimage tag actions:


 Action                    Description

 border                    Creates a rectangular border around the outer edge of an image.

 captcha                   Creates a CAPTCHA image.

 convert                   Converts an image from one file format to another.

 info                      Creates a ColdFusion structure that contains information about the image, including the color model, height, width,
                           and source of the image.

 read                      Reads an image from the specified local file path or URL. If you do not specify an action explicitly, ColdFusion uses
                           read as the default value.


 resize                    Resizes the height and width of an image.

 rotate                    Rotates an image by degrees.

 write                     Writes the image to a file. You can use the write action to generate lower-resolution JPEG files. Also, use the write
                           action to convert images to other file formats, such as PNG and GIF.

 writeToBrowser            Writes one or more images directly to a browser. Use this action to test the appearance of a single image or write
                           multiple images to the browser without saving the images to files.


For more information, see the cfimage tag in the CFML Reference.



                                                         

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                961
Working with Documents, Charts, and Reports




Image functions
ColdFusion provides more than 50 Image functions that expand on the functionality of the cfimage tag. You can pass
images created with the cfimage tag to Image functions or create images with the ImageNew function. The following
table groups the Image functions by category:


 Category                          Image functions

 Verifying images and supported    IsImage, IsImageFile, GetReadableImageFormats, GetWriteableImageFormats
 image formats

 Retrieving image information      ImageGetEXIFTag, ImageGetHeight, ImageGetIPTCTag, ImageGetWidth, ImageInfo

 Reading, writing, and converting  ImageGetBlob, ImageGetBufferedImage, ImageNew, ImageRead, ImageReadBase64, ImageWrite,
 images                            ImageWriteBase64

 Manipulating images               ImageAddBorder, ImageBlur, ImageCopy, ImageCrop, ImageFlip, ImageGrayscale, ImageNegative,
                                   ImageOverlay, ImagePaste, ImageResize, ImageRotate, ImageScaleToFit, ImageSharpen, ImageShear,
                                   ImageTranslate

 Drawing lines, shapes, and text   ImageDrawArc, ImageDrawBeveledRect, ImageDrawCubicCurve, ImageDrawLine, ImageDrawLines,
                                   ImageDrawOval, ImageDrawPoint, ImageDrawQuadraticCurve, ImageDrawRect, ImageDrawRoundRect,
                                   ImageDrawText

 Setting drawing controls          ImageClearRect, ImageRotateDrawingAxis, ImageSetAntialiasing, ImageSetBackgroundColor,
                                   ImageSetDrawingColor, ImageSetDrawingStroke, ImageSetDrawingTransparency,
                                   ImageShearDrawingAxis, ImageTranslateDrawingAxis, ImageXORDrawingMode



Creating ColdFusion images
The ColdFusion image contains image data in memory. Before you can manipulate images in ColdFusion, you create
a ColdFusion image. The following table shows the ways to create a ColdFusion image:


 Task                                                    Functions and tags

 Create a ColdFusion image from an existing image file.  cfimage tag or the ImageNew function

 Create a blank image from scratch.                      ImageNew function

 Create a ColdFusion image from BLOB data in a database. ImageNew function with the cfquery tag

 Create a ColdFusion image from a binary object.         cffile tag with the ImageNew function

 Create a ColdFusion image from a Base64 string.         ImageReadBase64 function and the ImageNew function or the cfimage tag

 Create a ColdFusion image from another ColdFusion       ImageCopy function with the ImageWrite function or the Duplicate function,
 image.                                                  or by passing the image to the ImageNew function or the cfimage tag



Using the cfimage tag
The simplest way to create a ColdFusion image with the cfimage tag is to specify the source attribute, which is the
image file that ColdFusion reads, and the name attribute, which is the variable that defines the image in memory:

<cfimage source="../cfdocs/images/artgallery/jeff01.jpg" name="myImage">

You do not have to specify the read action because it is the default action. Specify the name attribute for the read
action, which creates a variable that contains the ColdFusion image, for example, myImage.

You can pass the myImage variable to another cfimage tag or to Image functions. The following example shows how
to specify a ColdFusion image variable as the source:

<cfimage source="#myImage#" action="write" destination="test_myImage.jpg">




                                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     962
Working with Documents, Charts, and Reports




The write action writes the file to the specified destination, which can be an absolute or relative path. The following
example shows how to create a ColdFusion image from a URL and write it to a file on the local storage drive:

<cfimage source="http://www.google.com/images/logo_sm.gif" action="write"
       destination="c:\images\logo_sm.gif">

Specify the destination for the write action.

When you specify a destination, set the overwrite attribute to "yes" to write to the same file more than once.
Otherwise, ColdFusion generates an error:

<cfimage source="#myImage#" action="write" destination="images/jeff01.jpg" overwrite="yes">


Using the ImageNew function
You create a ColdFusion image with the ImageNew function the same way you define a ColdFusion variable. The
following example creates a ColdFusion image variable named "myImage" from the jeff01.jpg source file:

<cfset myImage=ImageNew("../cfdocs/images/artgallery/jeff01.jpg")>

This line produces the same result as the following code:

<cfimage source="../cfdocs/images/artgallery/jeff01.jpg" name="myImage">

As with the cfimage tag, you can specify an absolute or relative path, a URL, or another ColdFusion image as the
source. In the following example, ColdFusion reads a file from the local drive and passes it to the ImageWrite function,
which writes the image to a new file:

<cfset myImage=ImageNew("../cfdocs/images/artgallery/jeff01.jpg")>
<cfset ImageWrite(myImage,"myImageTest.png")>

The following code produces the same result:

<cfimage source="../cfdocs/images/artgallery/jeff01.jpg" name="myImage">
<cfimage source="#myImage#" action="write" destination="myImageTest.png">

Also, you can create a blank image. When using the ImageNew function, you do not specify the source to create a blank
image. However, you can specify the width and height, respectively. The following example shows how to create a
blank canvas that is 300 pixels wide and 200 pixels high:

<cfset myImage=ImageNew("",300,200)>

Optionally, you can specify the image type, as in the following example:

<cfset myImage=ImageNew("",200,300,"rgb")>

Other valid image types are argb and grayscale. You can use blank images as canvasses for drawing functions in
ColdFusion. For examples, see "Creating watermarks" on page 973.

Also, you can use the ImageNew function to create ColdFusion images from other sources, such as Base64 bytearrays,
file paths, and URLs. The following example creates a ColdFusion image from a JPEG file (x), and then creates another
ColdFusion image (y) from the image in memory:

<cfset x = ImageNew("c:\abc.jpg")>
<cfset y = ImageNew(x)>

For more information about the ImageNew function, see the CFML Reference.


Creating an image from a binary object
You can use the cffile tag to write an image file to ColdFusion variable. Then, you can pass the variable to the
ImageNew function to create a ColdFusion image from the binary object, as the following example shows:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                            963
Working with Documents, Charts, and Reports




<!--- Use the cffile tag to read an image file, convert it to binary format, and write the
     result to a variable. --->
<cffile action = "readBinary" file = "jeff05.jpg" variable = "aBinaryObj">
<!--- Use the ImageNew function to create a ColdFusion image from the variable. --->
<cfset myImage=ImageNew(aBinaryObj)>


Creating images from BLOB data
Many databases store images as BLOB data. To extract BLOB data from a database, create a query with the cfquery
tag. The following example shows how to extract BLOB data and use the cfimage tag to write them to files in PNG
format:

<!--- Use the cfquery tag to retrieve employee photos and last names from the database. --->
<cfquery
     name="GetBLOBs" datasource="myblobdata">
     SELECT LastName,Image
     FROM Employees
</cfquery>
<cfset i = 0>
<table border=1>
     <cfoutput query="GetBLOBs">
     <tr>
          <td>
          #LastName#
          </td>
          <td>
          <cfset i = i+1>
<!--- Use the cfimage tag to write the images to PNG files. --->
          <cfimage source="#GetBLOBs.Image#" destination="employeeImage#i#.png"
               action="write">
          <img src="employeeImage#i#.png"/>
          </td>
     </tr>
</cfoutput>
</table>

The following example shows how to use the ImageNew function to generate ColdFusion images from BLOB data:

<!--- Use the cfquery tag to retrieve all employee photos and employee IDs from a database. --->
<cfquery name="GetBLOBs" datasource="myBlobData">
SELECT EMLPOYEEID, PHOTO FROM Employees
</cfquery>
<!--- Use the ImageNew function to create a ColdFusion images from the BLOB data that was
     retrieved from the database. --->
<cfset myImage = ImageNew(GetBLOBs.PHOTO)>
<!--- Create thumbnail versions of the images by resizing them to fit in a 100-pixel square,
     while maintaining the aspect ratio of the source image. --->
<cfset ImageScaleToFit(myImage,100,100)>
<!--- Convert the images to JPEG format and save them to files in the thumbnails subdirectory,
     using the employee ID as the filename. --->
<cfimage source="#myImage#" action="write"
     destination="images/thumbnails/#GetBLOBs.EMPLOYEID#.jpg">

For information on converting a ColdFusion image to a BLOB, see "Inserting an image as a BLOB in a database" on
page 966.




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       964
Working with Documents, Charts, and Reports




Creating an image from a Base64 string
Base64 is a way to describe binary data as a string of ASCII characters. Some databases store images in Base64 format
rather than as BLOB data. You can use the cfimage tag or the ImageReadBase64 function to read Base64 data directly
from a database. Doing so eliminates the intermediary steps of binary encoding and decoding.

The following examples show how to use the cfimage tag to create a ColdFusion image from a Base64 string:

<!--- This example shows how to create a ColdFusion image from a Base64 string with headers
     (used for display in HTML). --->
<cfimage source="data:image/jpg;base64,/9j/4AAQSkZJRgABAQA.............."
     destination="test_my64.jpeg" action="write" isBase64="yes">


<!--- This example shows how to use the cfimage tag to write a Base64 string without headers.
--->
<cfimage source="/9j/4AAQSkZJRgABAQA.............." destination="test_my64.jpeg"
     action="write" isBase64="yes">

The following examples show how to use the ImageReadBase64 function to create a ColdFusion image from a Base64
string:

<!--- This example shows how to use the ImageReadBase64 function to read a Base64 string
     with headers. --->
<cfset myImage=ImageReadBase64("data:image/jpg;base64,/9j/4AAQSkZJRgABAQA..............")>


<!--- This example shows how to read a Base64 string without headers. --->
<cfset myImage=ImageReadBase64("/9j/4AAQSkZJRgABAQA..............")>

For more information on Base64 strings, see "Converting an image to a Base64 string" on page 966.


Copying an image
You use the ImageCopy function to copy a rectangular area of an existing image and generate a new ColdFusion image
from it. You can paste the new ColdFusion image onto another image, or write it to a file, as the following example
shows:

<!--- Use the cfimage tag to create a ColdFusion image from a JPEG file.
--->
<cfimage source="../cfdocs/images/artgallery/lori05.jpg" name="myImage">
<!--- Turn on antialiasing to improve image quality. --->
<cfset ImageSetAntialiasing(myImage)>
<!--- Copy the rectangular area specified by the coordinates (25,25,50,50) in the image to
     the rectangle beginning at (75,75), and return this copied rectangle as a new ColdFusion
     image. --->
<cfset dupArea = ImageCopy(myImage,25,25,50,50,75,75)>
<!--- Write the new ColdFusion image (dupArea) to a PNG file. --->
<cfimage source="#dupArea#" action="write" destination="test_myImage.png" overwrite="yes">


Duplicating an image
Another way to create a ColdFusion image is to duplicate it. Duplicating an image creates a clone, which is a copy of
an image that is independent of it: if the original image changes, those changes do not affect the clone, and the reverse.
This technique is useful if you want to create several versions of the same image. Duplicating an image can improve
processing time because you retrieve image data from a database or a file once to create the ColdFusion image. Then
you can create several clones and manipulate them in memory before writing them to files. For example, you could
create a thumbnail version, a grayscale version, and an enlarged version of an image uploaded to a server. To do so,
you use the cfimage tag or the ImageNew function to create a ColdFusion image from the uploaded file. You use the
Duplicate function to create three clones of the ColdFusion image.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         965
Working with Documents, Charts, and Reports




To create a clone, you can pass a ColdFusion image variable to the Duplicate function:

<!--- Use the ImageNew function to create a ColdFusion image from a JPEG file. --->
<cfset myImage=ImageNew("../cfdocs/images/artgallery/paul01.jpg")>
<!--- Turn on antialiasing to improve image quality. --->
<cfset ImageSetAntialiasing(myImage)>
<!--- Use the Duplicate function to create three clones of the ColdFusion image. --->
<cfset cloneA=Duplicate(myImage)>
<cfset cloneB=Duplicate(myImage)>
<cfset cloneC=Duplicate(myImage)>
<!--- Create a grayscale version of the image. --->
<cfset ImageGrayscale(cloneA)>
<!--- Create a thumbnail version of the image. --->
<cfset ImageScaleToFit(cloneB,50,"")>
<!--- Create an enlarged version of the image. --->
<cfset ImageResize(cloneC,"150%","")>
<!--- Write the images to files. --->
<cfset ImageWrite(myImage,"paul01.jpg","yes")>
<cfset ImageWrite(cloneA,"paul01_bw.jpg","yes")>
<cfset ImageWrite(cloneB,"paul01_sm.jpg","yes")>
<cfset ImageWrite(cloneC,"paul01_lg.jpg","yes")>
<!--- Display the images. --->
<img src="paul01.jpg">
<img src="paul01_bw.jpg">
<img src="paul01_sm.jpg">
<img src="paul01_lg.jpg">

Also, you can use the cfimage tag and the ImageNew function to duplicate images, as the following example shows:

<!--- Use the cfimage tag to create a ColdFusion image (myImage) and make a copy of it
(myImageCopy). --->
<cfimage source="../cfdocs/images/artgallery/paul01.jpg" name="myImage">
<cfimage source="#myImage#" name="myImageCopy">
<!-- Use the ImageNew function to make a copy of myImage called myImageCopy2. --->
<cfset myImageCopy2 = ImageNew(myImage)>



Converting images
ColdFusion makes it easy to convert images from one file format to another. Also, you can convert an image file to a
binary object, BLOB data, or a Base64 string.


Converting an image file
The extension of the destination file determines the file format of the image. Therefore, to convert an image, simply change
the filename extension in the destination file. The following example shows how to convert a JPEG file to a GIF file:

<cfimage source="../cfdocs/images/artgallery/jeff01.jpg" action="write" destination="jeff01.gif">

Similarly, you can use the ImageWrite function with the ImageNew function:

<cfset myImage=ImageNew("../cfdocs/images/artgallery/jeff01.jpg")>
<cfset ImageWrite(myImage,"jeff01.gif")>

In both examples, the convert action is implied.

The write action does not create a ColdFusion image; it simply writes an image to a file. To convert an image and
generate a ColdFusion image variable, use the convert action:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                966
Working with Documents, Charts, and Reports




<cfimage source="../cfdocs/images/artgallery/jeff01.jpg" action="convert"
     destination="jeff01.gif" name="myImage">

ColdFusion reads and writes most standard image formats. For more information, see Supported image file formats
in the CFML Reference.


Converting an image to a Base64 string
To convert a ColdFusion image to a Base64 string, use the ImageWriteBase64 function. In the following example, the
yes value determines that the output includes the headers required for display in HTML:

<!--- This example shows how convert a BMP file to a Base64 string. --->
<cfset ImageWriteBase64(myImage,"jeffBase64.txt","bmp","yes")>

Note: Microsoft Internet Explorer does not support Base64 strings.


Inserting an image as a BLOB in a database
Many databases store images as BLOB data. To insert a ColdFusion image into a BLOB column of a database, use the
ImageGetBlob function within a cfquery statement, as the following example shows:

<!--- This example shows how to add a ColdFusion image to a BLOB column of a database. --->
<!--- Create a ColdFusion image from an existing JPEG file. --->
<cfimage source="aiden01.jpg" name="myImage">
<!--- Use the cfquery tag to insert the ColdFusion image as a BLOB in the database. --->
<cfquery name="InsertBlobImage" datasource="myBlobData">
INSERT into EMPLOYEES (FirstName,LastName,Photo)
VALUES ("Aiden","Quinn",<cfqueryparam value="#ImageGetBlob(myImage)#"
cfsqltype="cf_sql_blob">)
</cfquery>



Verifying images
Use the IsImage function to test whether an image variable represents a valid ColdFusion image. This function takes
a variable name as its only parameter and returns a Boolean value.

Note: You cannot use the IsImage function to verify whether files are valid images. Instead, use the IsImageFile
function.

Also, ColdFusion provides two tags for determining which image file formats are supported on the server where the
ColdFusion application is deployed: GetReadableImageFormats and GetWriteableImageFormats. For more
information, see the CFML Reference.


Enforcing size restrictions
ColdFusion provides several functions for retrieving information associated with images, including the height and
width of an image. For example, you can use the ImageGetWidth and ImageGetHeight functions to determine
whether an image is too large to upload to a website or database.

The following example shows how to prevent users from uploading images that are greater than 300 pixels wide or 300

pixels high:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     967
Working with Documents, Charts, and Reports




<!--- Create a ColdFusion image named "myImage" from a file uploaded to the server. --->
<cfimage action="read" source="#fileUpload.serverFile#" name="myImage">
<!--- Determine whether the file is greater than 300 pixels in width or height. --->
<cfif ImageGetHeight(myImage) gt 300 or ImageGetWidth(myImage) gt 300>
     <!--- If the file exceeds the size limits, delete it from the server. --->
     <cffile action="delete" file="#fileUpload.serverDirectory#/#fileUpload.serverFile#">
     <cfoutput>
<!--- Display the following message. --->
     <p>
     The image you uploaded was too big. It must be less than 300 pixels wide and 300 pixels
          high. Your image was #imageGetWidth(myImage)# pixels wide and
          #imageGetHeight(myImage)# pixels high.
     </p>
</cfif>

For information about retrieving image metadata, see the ImageGetEXIFTag, ImageGetIPTCTag, and ImageInfo
functions in the CFML Reference.


Compressing JPEG images
To reduce the size of large files, you can convert a JPEG file to a lower quality image by using the write action of the
cfimage tag. Specify a value between 0 (low) and 1 (high) for the quality attribute, as the following example shows:

<cfimage source="../cfdocs/images/artgallery/jeff05.jpg" action="write"
     destination="jeff05_lq.jpg" quality="0.5" overwrite="yes">

You can perform the same operation by using the ImageWrite function:

<cfset myImage=ImageNew("jeff05.jpg")>
<cfset ImageWrite(myImage,"jeff05_lq.jpg","0.5")>



Manipulating ColdFusion images
You can perform a few common manipulation operations on ColdFusion images. For more information on
manipulating ColdFusion images, see the CFML Reference.


Adding borders to images
To create a simple border, use the cfimage tag. The following example creates a ColdFusion image with a 5-pixel blue
border:

<cfimage source="../cfdocs/images/artgallery/jeff01.jpg" action="border" thickness="5"
     color="blue" destination="testMyImage.jpg" overwrite="yes">
<img src="testMyImage.jpg">

The border is added to the outside edge of the source image. This increases the area of the image.

To create complex borders, use the ImageAddBorder function. The following example shows how to nest borders:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       968
Working with Documents, Charts, and Reports




<!--- Create a ColdFusion image from a JPEG file. --->
<cfset myImage=ImageNew("../cfdocs/images/artgallery/jeff01.jpg")>
<!--- Add a 5-pixel blue border around the outside edge of the image. --->
<cfset ImageAddBorder(myImage,5,"blue")>
<!--- Add a 10-pixel magenta border around the blue border. --->
<cfset ImageAddBorder(myImage,10,"magenta")>
<!--- Add a 5-pixel green border around the magenta border. --->
<cfset ImageAddBorder(myImage,20,"green")>
<!--- Write the ColdFusion image to a file. --->
<cfset ImageWrite(myImage,"testMyImage.jpg")>
<img src="testMyImage.jpg"/>

Also, with the ImageAddBorder function, you can add a border that is an image effect. For example, you can use the
wrap parameter to create a tiled border from the source image. The wrap parameter creates a tiled border by adding
the specified number of pixels to each side of the image, as though the image were tiled.

In the following example, 20 pixels from the outside edge of the source image are tiled to create the border:

<cfset myImage=ImageNew("../cfdocs/images/artgallery/jeff03.jpg")>
<cfset ImageAddBorder(myImage,20,"","wrap")>
<cfset ImageWrite(myImage,"testMyImage.jpg")>
<img src="testMyImage.jpg"/>




For examples of other border types, see the ImageAddBorder function in the CFML Reference.


Creating text images
You can create two types of text images:

* A CAPTCHA image, in which ColdFusion randomly distorts the text

* A text image, in which you control the text attributes


Creating a CAPTCHA image
You use the captcha action of the cfimage tag to create a distorted text image that is human-readable but not machine
readable. When you create a CAPTCHA image, you specify the text that is displayed in the CAPTCHA image;
ColdFusion randomly distorts the text. You can specify the height and width of the text area, which affects the spacing
between letters, the font size, the fonts to use for the CAPTCHA text, and the level of difficulty, which affects
readability. Do not use spaces in the text string specified for the text attribute: users cannot detect the spaces as part
of the CAPTCHA image.

The following example shows how to write a CAPTCHA image directly to the browser.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     969
Working with Documents, Charts, and Reports




<!--- This example shows how to create a CAPTCHA image with the text "rEadMe" and write the
     image directly to the browser. --->
<cfimage action="captcha" fontSize="25" width="162" height="75" text="rEadMe"
     fonts="Verdana,Arial,Courier New,Courier">

Note: For the CAPTCHA image to display, the width value must be greater than: fontSize times the number of
characters specified in text times 1.08. In this example, the minimum width is 162.

ColdFusion 9 supports CAPTCHA images in PNG format only.

Note: If you specify the destination attribute to write CAPTCHA images to files, use unique names for the CAPTCHA
image files so that when multiple users access the CAPTCHA images, the files are not overwritten.

The following example shows how to create CAPTCHA images with a high level of text distortion.

<!--- Use the GetTickCount function to generate unique names for the CAPTCHA files. --->
<cfset tc = GetTickCount()>
<!--- Set the difficulty to "high" for a higher level of text distortion. --->
<cfimage action="captcha" fontSize="15" width="180" height="50" text="rEadMe"
     destination="readme#tc#.png" difficulty="high">

For a detailed example, see "Using CAPTCHA to verify membership" on page 979.

The following image shows three CAPTCHA images with low, medium, and high levels of difficulty, respectively:




Using the ImageDrawText function
To create a text image by using the ImageDrawText function, specify the text string and the x and y coordinates for
the location of the beginning of the text string. You can draw the text on an existing image or on a blank image, as the
following examples show:

<!--- This example shows how to draw a text string on a blank image. --->
<cfset myImage=ImageNew("",200,100)>
<cfset ImageDrawText(myImage, "Congratulations!",10,50)>
<cfimage source="#myImage#" action="write" destination="myImage.png" overwrite="yes">
<img src="myImage.png">


<!--- This example shows how to draw a text string on an existing image.
--->
<cfset myImage2=ImageNew("../cfdocs/images/artgallery/jeff01.jpg")>
<cfset ImageDrawText(myImage2,"Congratulations!",10,50)>
<cfimage source="#myImage2#" action="write" destination="myImage2.png" overwrite="yes">
<img src="myImage2.png">

In the previous examples, the text is displayed in the default system font and font size. To control the appearance of
the text, you specify a collection of text attributes, as the following example shows:

<cfset attr = StructNew()>
<cfset attr.style="bolditalic">
<cfset attr.size=20>
<cfset attr.font="verdana">
<cfset attr.underline="yes">

To apply the text attributes to the text string, include the attribute collection name in the ImageDrawText definition.
In the following examples, the "attr" text attribute collection applies the text string "Congratulations!":




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     970
Working with Documents, Charts, and Reports




...
<cfset ImageDrawText(myImage,"Congratulations!",10,50,attr)>

To change the color of the text, use the ImageSetDrawingColor function. This function controls the color of all
subsequent drawing objects on an image. In the following example, two lines of text, "Congratulations!" and
"Gabriella", inherit the color magenta.

<!--- This example shows how to draw a text string on a blank image. --->
<cfset myImage=ImageNew("../cfdocs/images/artgallery/jeff01.jpg")>
<cfset ImageSetDrawingColor(myImage,"magenta")>
<cfset attr = StructNew()>
<cfset attr.style="bolditalic">
<cfset attr.size=20>
<cfset attr.font="verdana">
<cfset attr.underline="yes">
<cfset ImageDrawText(myImage,"Congratulations!",10,50,attr)>
<cfset ImageDrawText(myImage,"Gabriella",50,125,attr)>
<cfimage source="#myImage#" action="write" destination="myImage.jpg" overwrite="yes">
<img src="myImage.jpg"/>

For a list of valid named colors, see the cfimage tag in the CFML Reference.


Drawing lines and shapes
ColdFusion provides several functions for drawing lines and shapes. For shapes, the first two values represent the x
and y coordinates, respectively, of the upper-left corner of the shape. For simple ovals and rectangles, the two numbers
following the coordinates represent the width and height of the shape in pixels. For a line, the values represent the x
and y coordinates of the start point and end point of the line, respectively. To create filled shapes, set the filled
attribute to true. The following example shows how to create an image with several drawing objects:

<!--- Create an image that is 200-pixels square. --->
<cfset myImage=ImageNew("",200,200)>
<!--- Draw a circle that is 100 pixels in diameter. --->
<cfset ImageDrawOval(myImage,40,20,100,100)>
<!--- Draw a filled rectangle that is 40 pixels wide and 20 pixels high.
--->
<cfset ImageDrawRect(myImage,70,50,40,20,true)>
<!--- Draw a 100-pixel square. --->
<cfset ImageDrawRect(myImage,40,40,100,100)>
<!--- Draw two lines. --->
<cfset ImageDrawLine(myImage,130,40,100,200)>
<cfset ImageDrawLine(myImage,50,40,100,200)>
<!--- Write the ColdFusion image to a file. --->
<cfimage source="#myImage#" action="write" destination="testMyImage.gif" overwrite="yes">
<img src="testMyImage.gif"/>

Note: To draw a sequence of connected lines, use the ImageDrawLines function. For more information, see the CFML
Reference.


Setting drawing controls
ColdFusion provides several functions for controlling the appearance of drawing objects. As shown in the
ImageDrawText example, you use the ImageSetDrawingColor function to define the color of text in an image. This
function also controls the color of lines and shapes. To control line attributes (other than color), use the
ImageSetDrawingStroke function. The ImageSetDrawingStroke function uses a collection to define the line
attributes.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                971
Working with Documents, Charts, and Reports




Drawing controls apply to all subsequent drawing functions in an image; therefore, order is important. In the following
example, the drawing stroke attributes defined in the attribute collection apply to the square and the two lines.
Similarly, the color green applies to the rectangle and the square, while the color red applies only to the two lines. You
can reset a drawing control as many times as necessary within an image to achieve the desired effect.

<!--- Create an attribute collection for the drawing stroke. --->
<cfset attr=StructNew()>
<cfset attr.width="4">
<cfset attr.endcaps="round">
<cfset attr.dashPattern=ArrayNew(1)>
<cfset dashPattern[1]=8>
<cfset dashPattern[2]=6>
<cfset attr.dashArray=dashPattern>
<cfset myImage=ImageNew("",200,200)>
<cfset ImageDrawOval(myImage,40,20,100,100)>
<!--- Set the drawing color to green for all subsequent drawing functions. --->
<cfset ImageSetDrawingColor(myImage,"green")>
<cfset ImageDrawRect(myImage,70,50,40,20,true)>
<!--- Apply the attribute collection to all subsequent shapes and lines in the image. --->
<cfset ImageSetDrawingStroke(myImage,attr)>
<cfset ImageDrawRect(myImage,40,40,100,100)>
<!--- Set the drawing color to red for all subsequent drawing functions. --->
<cfset ImageSetDrawingColor(myImage,"red")>
<cfset ImageDrawLine(myImage,130,40,100,200)>
<cfset ImageDrawLine(myImage,50,40,100,200)>
<cfimage source="#myImage#" action="write" destination="testMyImage.gif" overwrite="yes">
<img src="testMyImage.gif"/>


Resizing images
ColdFusion makes it easy to resize images. You can reduce the file size of an image by changing its dimensions, enforce
uniform sizes on images, and create thumbnail images. The following table describes the ways to resize images in
ColdFusion:


Task                                                                Functions and actions

Resize an image                                                     ImageResize function, or the resize action of the cfimage tag

Resize images so that they fit in a defined square or rectangle and ImageScaleToFit function
control the interpolation method

Resize an image and control the interpolation method                ImageResize function



Using the cfimage tag resize action
Use the cfimage tag resize action to resize an image to the specified height and width. You can specify the height
and width in pixels or as a percentage of the original dimensions of the image. To specify a percentage, include the
percent symbol (%) in the height and width definitions.




                                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     972
Working with Documents, Charts, and Reports




<!--- This example shows how to specify the height and width of an image in pixels. --->
<cfimage source="../cfdocs/images/artgallery/jeff01.jpg" action="resize" width="100"
     height="100" destination="jeff01_sm.jpg">
<!--- This example shows how to specify the height and width of an image as percentages. --->
<cfimage source="../cfdocs/images/artgallery/jeff02.jpg" action="resize" width="50%"
     height="50%" destination="jeff02_sm.jpg">


<!--- This example shows how to specify the height of an image in pixels and its width as a
     percentage. Notice that this technique can distort the image. --->
<cfimage source="../cfdocs/images/artgallery/jeff03.jpg" action="resize" width="50%"
     height="100" destination="jeff03_sm.jpg" overwrite="yes">

The cfimage tag requires that you specify both the height and the width for the resize action.

The cfimage tag resize action uses the highestQuality interpolation method for the best quality image (at the cost
of performance). For faster display, use the ImageResize function or the ImageScaleToFit function.


Using the ImageResize function
The ImageResize function is like the cfimage tag resize action. To ensure that the resized image is proportional,
specify a value for the height or width and enter a blank value for the other dimension:

<!--- This example shows how to resize an image to 50% of original size and resize it
     proportionately to the new width. The height value is blank. --->
<cfset myImage=ImageNew("http://www.google.com/images/logo_sm.gif")>
<cfset ImageResize(myImage,"50%","")>
<!--- Save the modified image to a file. --->
<cfimage source="#myImage#" action="write" destination="test_myImage.jpeg" overwrite="yes">
<!--- Display the source image and the resized image. --->
<img src="http://www.google.com/images/logo_sm.gif"/>
<img src="test_myImage.jpeg"/>

The ImageResize function also lets you specify the type of interpolation used to resize the image. Interpolation lets
you control the trade-off between performance and image quality. By default, the ImageResize function uses the
highestQuality interpolation method. To improve performance (at the cost of image quality), change the
interpolation method. Also, you can set the blur factor for the image. The default value is 1 (not blurred). The highest
blur factor is 10 (very blurry). The following example shows how to resize an image using the highPerformance form
of interpolation with a blur factor of 10:

<cfset myImage=ImageNew("../cfdocs/images/artgallery/aiden01.jpg")>
<cfset ImageResize(myImage,"","200%","highPerformance", 10)>
<cfimage action="writeToBrowser" source="#myImage#">

Note: Increasing the blur factor reduces performance.

For a complete list of interpolation methods, see ImageResize in the CFML Reference.


Using the ImageScaleToFit function
To create images of a uniform size, such as thumbnail images or images displayed in a photo gallery, use the
ImageScaleToFit function. You specify the area of the image in pixels. ColdFusion resizes the image to fit the square
or rectangle and maintains the aspect ratio of the source image. Like the ImageResize function, you can specify the
interpolation, as the following example shows:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      973
Working with Documents, Charts, and Reports




<!--- This example shows how to resize an image to a 100-pixel square, while maintaining the
aspect ratio of the source image. --->
<cfimage source="../cfdocs/images/artgallery/jeff05.jpg" name="myImage" action="read">
<!--- Turn on antialiasing. --->
<cfset ImageSetAntialiasing(myImage)>
<cfset ImageScaleToFit(myImage,100,100,"mediumQuality")>
<!--- Display the modified image in a browser. --->
<cfimage source="#myImage#" action="writeToBrowser">

To fit an image in a defined rectangular area, specify the width and height of the rectangle, as the following example
shows:

<!--- This example shows how to resize an image to fit in a rectangle that is 200 pixels
     wide and 100 pixels high, while maintaining the aspect ratio of the source image. --->
<cfimage source="../cfdocs/images/artgallery/jeff05.jpg" name="myImage">
<!--- Turn on antialiasing. --->
<cfset ImageSetAntialiasing(myImage)>
<cfset ImageScaleToFit(myImage,200,100)>
<!--- Display the modified image in a browser. --->
<cfimage source="#myImage#" action="writeToBrowser">

In this example, the width of the resulting image is less than or equal to 200 pixels and the height of the image is less
than or equal to 100 pixels.

Also, you can specify just the height or just the width of the rectangle. To do so, specify an empty string for the
undefined dimension. The following example resizes the image so that the width is exactly 200 pixels and the height
of the image is proportional to the width:

<!--- This example shows how to resizes an image so that it is 200 pixels wide, while
     maintaining the aspect ratio of the source image. The interpolation method is set to
     maximize performance (which reduces image quality). --->
<cfimage source="../cfdocs/images/artgallery/jeff05.jpg" name="myImage">
<!--- Turn on antialiasing. --->
<cfset ImageSetAntialiasing(myImage)>
<cfset ImageScaleToFit(myImage,200,"","highestPerformance")>
<!--- Display the modified image in a browser. --->
<cfimage source="#myImage#" action="writeToBrowser">

For more information, see ImageScaleToFit in the CFML Reference.


Creating watermarks
A watermark is a semitransparent image that is superimposed on another image. One use for a watermark is for
protecting copyrighted images. To create a watermark in ColdFusion, you use the ImageSetDrawingTransparency
function with the ImagePaste function. You can create a watermark image in one of three ways:

* Create a watermark from an existing image file. For example, you can use a company logo as a watermark.

* Create a text image in ColdFusion and apply the image as a watermark. For example, you can create a text string,
   such as Copyright or PROOF and apply it to all the images in a photo gallery.

* Create a drawing image in ColdFusion and use it as a watermark. For example, you can use the drawing functions
   to create a green check mark and apply it to images that have been approved.


Creating a watermark from an image file
The following example shows how to create a watermark from an existing GIF image located on a website:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                              974
Working with Documents, Charts, and Reports




<!--- This example shows how to create a watermark from an existing image. --->
<!--- Create two ColdFusion images from existing JPEG files. --->
<cfimage source="../cfdocs/images/artgallery/raquel05.jpg" name="myImage">
<cfimage source="http://www.google.com/images/logo_sm.gif" name="myImage2">
<cfimage source="#myImage#" action="write" destination="logo.jpg" overwrite="yes">
<cfset ImageSetDrawingTransparency(myImage,50)>
<!--- Paste myImage2 on myImage at the coordinates (0,0). --->
<cfset ImagePaste(myImage,myImage2,0,0)>
<!--- Write the result to a file. --->
<cfimage source="#myImage#" destination="watermark.jpg" action="write" overwrite="yes">
<!--- Display the result. --->
<img src="watermark.jpg"/>


Creating a watermark from a text image
The following example shows how to create a text image in ColdFusion and use it as a watermark:

<!--- Create a ColdFusion image from an existing JPG file. --->
<cfset myImage=ImageNew("../cfdocs/images/artgallery/raquel05.jpg")>
<!--- Scale the image to fit in a 200-pixel square, maintaining the aspect ratio of the
     source image. --->
     <cfset ImageScaleToFit(myImage,200,200)>
<!--- Set the drawing transparency to 75%. --->
     <cfset ImageSetDrawingTransparency(myImage,75)>
<!--- Create a ColdFusion image from scratch. --->
<cfset textImage=ImageNew("",150,140)>
<!--- Set the drawing color to white. --->
     <cfset ImageSetDrawingColor(textImage,"white")>
<!--- Create a collection of text attributes. --->
         <cfset attr=StructNew()>
         <cfset attr.size=40>
         <cfset attr.style="bold">
         <cfset attr.font="Arial">
<!--- Turn on antialiasing. --->
<cfset ImageSetAntialiasing(textImage)>
<!--- Draw the text string "PROOF" on the ColdFusion image. Apply the text attributes that
     you specified. --->
<cfset ImageDrawText(textImage,"PROOF",1,75,attr)>
<!--- Rotate the text image by 30 degrees. --->
     <cfset ImageRotate(textImage,30)>
<!--- Scale the image to fit in a 200-pixel square, maintaining the aspect ratio of the
     source image. --->
     <cfset ImageScaleToFit(textImage,200,200)>
<!--- Paste the text image onto myImage. --->
<cfset ImagePaste(myImage,textImage,0,0)>
<!--- Write the combined image to a file. --->
<cfimage source="#myImage#" action="write" destination="test_watermark.jpg" overwrite="yes">
<!--- Display the image. --->
<img src="test_watermark.jpg"/>


Creating a watermark from a ColdFusion drawing
The following example shows how to draw an image in ColdFusion and use it as a watermark. You use the
ImageSetDrawingStroke function to define the attributes of lines and shapes you create with drawing functions and
the ImageSetDrawingColor function to define the color.




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     975
Working with Documents, Charts, and Reports




<!--- This example shows how to draw a red circle with a line through it and use it as a
      watermark. --->
<!--- Use the ImageNew function to create a ColdFusion image that is 201x201 pixels. --->
<cfset myImage=ImageNew("",201,201)>
<!--- Set the drawing transparency of the image to 30%. --->
<cfset ImageSetDrawingTransparency(myImage,30)>
<!--- Set the drawing color to red. --->
<cfset ImageSetDrawingColor(myImage,"red")>
<!--- Create an attribute collection that sets the line width to ten pixels. --->
      <cfset attr=StructNew()>
      <cfset attr.width = 10>
<!--- Apply the attribute collection to the ImageSetDrawingStroke function. --->
      <cfset ImageSetDrawingStroke(myImage,attr)>
<!--- Draw a diagonal line starting at (40,40) and ending at (165,165) on myImage. The drawing
      attributes you specified are applied to the line. --->
      <cfset ImageDrawLine(myImage,40,40,165,165)>
<!--- Draw a circle starting at (5,5) and is 190 pixels high and 190 pixels wide. The drawing
      attributes you specified are applied to the oval. --->
<cfset ImageDrawOval(myImage,5,5,190,190)>
<!--- Create a ColdFusion image from a JPEG file. --->
<cfimage source="../cfdocs/images/artgallery/raquel05.jpg" name="myImage2">
<!--- Scale the image to fit in a 200-pixel square, maintaining the aspect ratio of the
      source image. --->
<cfset ImageScaleToFit(myImage2,200,200)>
<!--- Paste the myImage2 directly over the myImage. --->
<cfset ImagePaste(myImage,myImage2,0,0)>
<!--- Save the combined image to a file. --->
<cfimage source="#myImage#" action="write" destination="test_watermark.jpg" overwrite="yes">
<!--- Display the image in a browser. --->
<img src="test_watermark.jpg"/>



Writing images to the browser
Use the writeToBrowser action of the cfimage tag to display images directly in the browser without writing them to
files. This technique is useful for testing the appearance of a ColdFusion image. The following example shows how to
test the display of two effects applied to an image:

<cfset myImage=ImageNew("../cfdocs/images/artgallery/paul01.jpg")>
<cfset ImageBlur(myImage,5)>
<cfset ImageNegative(myImage)>
<cfimage source="#myImage#" action="writeToBrowser">

The writeToBrowser action displays images in PNG format.

Also, you can write multiple images to the browser which is useful if you want to manipulate images in memory and
display them without writing them to files. For example, you can duplicate several versions of the same image, display
the versions in a browser, and allow the user to choose one of the images to write to a file. Or, you can extract images
from a database, add a watermark to the images that appear in the browser, such as Proof or Draft, without having to
write the modified images to files first. This way you can maintain one set of image files and change them on-the-fly.
For an example of writing multiple images to the browser, see "Generating a gallery of watermarked images" on
page 977.


Application examples that use ColdFusion images
You can create simple applications that automate image processes by using ColdFusion images.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       976
Working with Documents, Charts, and Reports




Generating thumbnail images
The following example shows how to create a form for uploading images. A visitor to the site can use the form to
upload an image file and generate a thumbnail image from it. You use ColdFusion image operations to perform the
following tasks:

* Verify that the uploaded file is a valid image.

* Ensure that the height or the width of the image does not exceed 800 pixels.

* If the image is valid and within the size limits, generate a thumbnail version of the source image and save it to a file.

Enter the following code on the form page:

<!--- This code creates a form with one field where the user enters the image file to upload.
--->
<cfform action="makeThumbnail.cfm" method="post" enctype="multipart/form-data">
Please upload an image: <cfinput type="file" name="image">
<cfinput type="submit" value="Send Image" name="Submit">
</cfform>

Enter the following code on the action page:

<cfset thisDir = expandPath(".")>
<!--- Determine whether the form is uploaded with the image. --->
<cfif structKeyExists(form,"image") and len(trim(form.image))>
     <!--- Use the cffile tag to upload the image file. --->
     <cffile action="upload" fileField="image" destination="#thisDir#" result="fileUpload"
          nameconflict="overwrite">
     <!--- Determine whether the image file is saved. --->
     <cfif fileUpload.fileWasSaved>
     <!--- Determine whether the saved file is a valid image file. --->
          <cfif IsImageFile("#fileUpload.serverfile#")>
     <!--- Read the image file into a variable called myImage. --->
               <cfimage action="read" source="#fileUpload.serverfile#" name="myImage">
               <!--- Determine whether the image file exceeds the size limits. --->
               <cfif ImageGetHeight(myImage) gt 800 or ImageGetWidth(myImage) gt 800>
                    <!--- If the file is too large, delete it from the server. --->
                    <cffile action="delete"
                         file="#fileUpload.serverDirectory#/#fileUpload.serverFile#">
                    <cfoutput>
                    <p>
                    The image you uploaded was too large. It must be less than 800 pixels wide
                         and 800 pixels high. Your image was #imageGetWidth(myImage)# pixels wide
                         and #imageGetHeight(myImage)# pixels high.
                    </p>
                    </cfoutput>
                    <!--- If the image is valid and does not exceed the size limits,
                         create a thumbnail image from the source file that is 75-pixels
                         square, while maintaining the aspect ratio of the source image.
                         Use the bilinear interpolation method to improve performance.
                         --->
               <cfelse>


     <cfset ImageScaleToFit(myImage,75,75,"bilinear")>
                    <!--- Specify the new filename as the source filename with
                         "_thumbnail" appended to it. --->
                    <cfset newImageName = fileUpload.serverDirectory & "/" &
                         fileUpload.serverFilename & "_thumbnail." &
                         fileUpload.serverFileExt>



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                           977
Working with Documents, Charts, and Reports




                    <!--- Save the thumbnail image to a file with the new filename. --->
                    <cfimage source="#myImage#" action="write"
                         destination="#newImageName#" overwrite="yes">
                    <cfoutput>
                    <p>
                    Thank you for uploading the image. We have created a thumbnail for
                         your picture.
                    </p>
                    <p>
                    <!--- Display the thumbnail image. --->
                    <img src="#getFileFromPath(newImageName)#">
                    </p>
                    </cfoutput>
               </cfif>
         <!--- If it is not a valid image file, delete it from the server. --->
         <cfelse>
               <cffile action="delete"
                    file="#fileUpload.serverDirectory#/#fileUpload.serverFile#">
               <cfoutput>
               <p>
               The file you uploaded, #fileUpload.clientFile#, was not a valid image.
               </p>
               </cfoutput>
         </cfif>
    </cfif>
</cfif>


Generating a gallery of watermarked images
The following example extracts images and information from the cfartgallery database. You use ColdFusion image
operations to perform the following tasks:

* Verify that an image exists for records returned from the database.

* Display the text, SOLD! on images that have been sold.

* Resize images to 100 pixels, maintaining the aspect ratio of the source image.

* Add a 5-pixel border to the images.

* Display the modified images directly in the browser without writing them to files.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                           978
Working with Documents, Charts, and Reports




Example
<!--- Create a query to extract artwork and associated information from the cfartgallery
database. --->
<cfquery name="artwork" datasource="cfartgallery">
SELECT FIRSTNAME, LASTNAME, ARTNAME, DESCRIPTION, PRICE, LARGEIMAGE, ISSOLD, MEDIATYPE
FROM ARTISTS, ART, MEDIA
WHERE ARTISTS.ARTISTID = ART.ARTISTID
AND ART.MEDIAID = MEDIA.MEDIAID
ORDER BY ARTNAME
</cfquery>
<cfset xctr = 1>
<table border="0" cellpadding="15" cellspacing="0" bgcolor="#FFFFFF">
<cfoutput query="artwork">
    <cfif xctr mod 3 eq 1>
    <tr>
         </cfif>
         <!--- Use the IsImageFile function to verify that the image files extracted
               from thedatabase are valid. Use the ImageNew function to create a
               ColdFusion image fromvalid image files. --->
         <cfif IsImageFile("../cfdocs/images/artgallery/#artwork.largeImage#")>
         <cfset myImage=ImageNew("../cfdocs/images/artgallery/#artwork.largeImage#")>
               <td valign="top" align="center" width="200">
               <cfset xctr = xctr + 1>
               <!--- For artwork that has been sold, display the text string "SOLD!"
                   in white on the image. --->
                         <cfif artwork.isSold>
                                <cfset ImageSetDrawingColor(myImage,"white")>
                                     <cfset attr=StructNew()>
                                     <cfset attr.size=45>
                                     <cfset attr.style="bold">
                                     <cfset ImageDrawText(myImage,"SOLD!",35,195, attr)>
                                     </cfif>
         <!--- Resize myImage to fit in a 110-pixel square, scaled proportionately. --->
         <cfset ImageScaleToFit(myImage,110,"","bicubic")>
         <!--- Add a 5-pixel black border around the images. (Black is the default color. --->
         <!--- Add a 5-pixel black border to myImage. --->
         <cfset ImageAddBorder(myImage,"5")>
         <!--- Write the images directly to the browser without saving them to the hard drive.
               --->
         <cfimage source="#myImage#" action="writeToBrowser"><br>
               <strong>#artwork.artName#</strong><br>
               Artist: #artwork.firstName# #artwork.lastName#<br>
               Price: #dollarFormat(artwork.price)#<br>
               #artwork.mediaType# - #artwork.description#<br>
               </td>
         </cfif>
         <cfif xctr-1 mod 3 eq 0>
               </tr>
         </cfif>
</cfoutput>
</table>




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               979
Working with Documents, Charts, and Reports




Using CAPTCHA to verify membership
The following example shows how to create a simple form to verify whether a person (rather than a computer
generating spam) is registering to receive an online newsletter. You generate the CAPTCHA image from a random text
string on the form page and verify the response on the action page.


Example
Enter the following code on the form page:

<!--- Set the length of the text string for the CAPTCHA image. --->
<cfset stringLength=6>
<!--- Specify the list of characters used for the random text string. The following list
     limits the confusion between upper- and lowercase letters as well as between numbers and
     letters. --->
<cfset
     stringList="2,3,4,5,6,7,8,9,a,b,d,e,f,g,h,j,n,q,r,t,y,A,B,C,D,E,F,G,H,K,L,M,N,P,Q,R,S,
     T,U,V,W,X,Y,Z">
<cfset rndString="">
<!--- Create a loop that builds the string from the random characters. --->
<cfloop from="1" to="#stringLength#" index="i">
     <cfset rndNum=RandRange(1,listLen(stringList))>
     <cfset rndString=rndString & listGetAt(stringList,rndNum)>
</cfloop>
<!--- Hash the random string. --->
<cfset rndHash=Hash(rndString)>


<!--- Create the user entry form. --->
<cfform action="captcha2.cfm" method="post">
<p>Please enter your first name:</p>
<cfinput type="text" name="firstName" required="yes">
<p>Please enter your last name:</p>
<cfinput type="text" name="lastName" required="yes">
<p>Please enter your e-mail address:</p>
<cfinput type="text" name="mailTo" required="yes" validate="email">
<!--- Use the randomly generated text string for the CAPTCHA image. --->
<p><cfimage action="captcha" fontSize="24" fonts="Times New Roman" width="200" height="50"
     text="#rndString#"></p>
<p>Please type what you see: </p>
<p><cfinput type="text" name="userInput" required="yes" maxlength=6>
<cfinput type="hidden" name="hashVal" value="#rndHash#">
<p><cfinput type="Submit" name ="OK" value="OK"></p>
</cfform>

Enter the following code on the action page:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     980
Working with Documents, Charts, and Reports




<!--- Verify whether the text entered by the user matches the CAPTCHA string. --->
<cfif #form.hashval# eq Hash(#form.userInput#)>
     <cfoutput>
     <p>
     Thank you for registering for our online newsletter, #form.firstName# #form.lastName#.
     </p>
     <p>
     A notification has been sent to your e-mail address: #form.mailTo#.
     </p>
          <cfmail from="newsletter@domain.com" to="#form.mailTo#" subject="Newsletter">
          Thank you for your interest in our Newsletter.
          </cfmail>
     </cfoutput>
<cfelse>
     <p>I'm sorry; please try again.</p>
</cfif>


Creating versions of an image
The following example shows how to create an application that lets you generate four versions of the same image,
display the versions in a form, and choose which one to save. The application comprises three ColdFusion pages that
perform the following tasks:

* Dynamically populate a drop-down list box from a database query.

* Use the cfimage tag to create a ColdFusion image from the title selected from the list. Use the ImageNew function
   to create three clones of the ColdFusion image. Use the ImageSharpen function to change the sharpness setting for
   each clone.

* Save the file chosen from the form to a new location.


Example
On the first form page, create a query that selects the artwork from the cfartgallery database and displays the titles
in a pop-up menu:

<!--- Create a query to extract artwork from the cfartgallery database. --->
<cfquery name="artwork" datasource="cfartgallery">
SELECT ARTID, ARTNAME, LARGEIMAGE
FROM ART
ORDER BY ARTNAME
</cfquery>


<!--- Create a form that lists the artwork titles generated by the query. Set the value to
     LARGEIMAGE so that the image file is passed to the action page. --->
<cfform action="dupImage2.cfm" method="post">
<p>Please choose a title:</p>
<cfselect name="art" query="artwork" display="ARTNAME" value="LARGEIMAGE" required="yes"
     multiple="no" size="8">
</cfselect>
<br/><cfinput type="submit" name="submit" value="OK">
</cfform>

On the first action page, clone the original image three times, change the sharpness setting for each clone, and display
the results:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                          981
Working with Documents, Charts, and Reports




<!--- Determine whether a valid image file exists. --->
<cfif IsImageFile("../cfdocs/images/artgallery/#form.art#")>
    <cfset original=ImageNew("../cfdocs/images/artgallery/#form.art#")>

    <!--- Use the ImageNew function to create a clone of the ColdFusion image. --->
    <cfset clone1=ImageNew(original)>
    <!--- Use the ImageSharpen function to blur the cloned image. --->
    <cfset ImageSharpen(clone1,-1)>
    <!--- Use the ImageNew function to create a second clone of the original image. --->
    <cfset clone2=ImageNew(original)>
    <!--- Use the ImageSharpen function to sharpen the cloned image. --->
    <cfset ImageSharpen(clone2,1)>
    <!--- Use the ImageNew function to create a third clone for the original image. --->
    <cfset clone3=ImageNew(original)>
    <!--- Use the ImageSharpen function to sharpen the cloned image to the maximum setting.
         --->
    <cfset ImageSharpen(clone3,2)>
<!--- Create a form with a radio button for each selection. The value of the hidden field
    is the relative path of the original image file. --->
<p>Please choose an image:</p>
<table>
    <tr>
    <cfform action="dupImage3.cfm" method="post">
         <td><cfimage source="#original#" action="writeToBrowser"><br />
         <cfinput type="radio" name="foo" value="original">Original Image</td>
         <td><cfimage source="#clone1#" action="writeToBrowser"><br />
         <cfinput type="radio" name="foo" value="blurred">Blurred Image</td>
         <td><cfimage source="#clone2#" action="writeToBrowser"><br />
         <cfinput type="radio" name="foo" value="sharper">Sharper Image</td>
         <td><cfimage source="#clone3#" action="writeToBrowser"><br />
         <cfinput type="radio" name="foo" value="sharpest">Sharpest Image</td>
         <tr><td><cfinput type="Submit" name="OK" value="OK">
         <cfinput type="hidden" name="orig_file"
               value="../cfdocs/images/artgallery/#form.art#">
         </td></tr>
    </cfform>
    </tr>
</table>
<cfelse>
    <p>There is no image associated with the title you selected. Please click the Back button
         and try again.</p>
</cfif>

On the second action page, save the selected image to the C drive:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     982
Working with Documents, Charts, and Reports




<p>The image you have chosen has been saved.</p>
<cfset img=ImageNew("#form.orig_file#")>
<cfswitch expression=#form.foo#>
     <cfcase value="blurred">
          <cfset ImageSharpen(img,-1)>
     </cfcase>
     <cfcase value="sharper">
          <cfset ImageSharpen(img,1)>
     </cfcase>
     <cfcase value="sharpest">
          <cfset ImageSharpen(img,2)>
     </cfcase>
</cfswitch>


<!--- Use the cfimage tag to write the image selected from the form to a file in the C drive.
     Use the value of the hidden field as the source file for the image. --->
<cfimage source="#img#" action="write" destination="c:/myImage.jpg" overwrite="yes">
<img src="c:/myImage.jpg" />




Creating Charts and Graphs

You can use the cfchart tag to display charts and graphs.


About charts
The ability to display data in a chart or graph can make data interpretation much easier. Rather than present a simple
table of numeric data, you can display a bar, pie, line, or other applicable type of chart using colors, captions, and a
two-dimensional or three-dimensional representation of your data.

The cfchart tag, along with the cfchartseries and cfchartdata tags, provide many different chart types. The
attributes to these tags let you customize your chart appearance.

You can create 11 types of charts in Adobe ColdFusion in two and three dimensions. The following figure shows a
sample of each type of chart.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 983
Working with Documents, Charts, and Reports




Note: In two dimensions, bar and cylinder charts appear the same, as do cone and pyramid charts.




Creating a basic chart
You can create a chart in either of the following ways:

* Using the cfchart, cfchartseries, and cfchartdata tags in a ColdFusion page.

* Using the chart wizard that is included with the ColdFusion Report Builder. For more information, see "Creating
  Reports and Documents for Printing" on page 1007.


Creating a chart with ColdFusion tags
To create a chart with ColdFusion tags, you use the cfchart tag along with at least one cfchartseries tag. You can
optionally include one or more cfchartdata tags within a cfchartseries tag. The following table describes these tags:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         984
Working with Documents, Charts, and Reports




 Tag                 Description

 cfchart             Specifies the container in which the chart appears. This container defines the height, width, background color, labels,
                     fonts, and other characteristics of the chart.

                     Include at least one cfchartseries tag within the cfchart tag.

 cfchartseries       Specifies a database query that supplies the data to the chart and one or more cfchartdata tags that specify
                     individual data points. Specifies the chart type, colors for the chart, and other optional attributes.

 cfchartdata         Optionally specifies an individual data point to the cfchartseries tag.


The following example shows an outline of the basic code that you use to create a chart:

<cfchart>
     <cfchartseries type="type">
                <cfchartdata item="something" value="number">
     </cfchartseries>
</chart>

The following example displays a simple pie chart that shows four values:

<cfchart>
     <cfchartseries type="pie">
            <cfchartdata item="New car sales" value="50000">
            <cfchartdata item="Used car sales" value="25000">
            <cfchartdata item="Leasing" value="30000">
            <cfchartdata item="Service" value="40000">
     </cfchartseries>
</cfchart>

Note: If you access charting functionality using Java calls, a watermark `Developer Edition - Not for Production Use'
might be displayed on the chart images. To avoid this, when you assign the server object to a reference, instead of
svr.getDefaultInstance(getPageContext().getServletContext());), use the following code (by prefixing
svr=) :svr=svr.getDefaultInstance(getPageContext().getServletContext());). Ensure that you restart
the server for the changes to take effect.


Creating a chart with the Report Builder wizard
The ColdFusion Report Builder includes a wizard that lets you create charts easily. The wizard lets you specify all of
the chart characteristics that you can specify using the cfchart, cfchartseries, and cfchartdata tags. For
information about using the Report Builder chart wizard, see "Creating Reports and Documents for Printing" on
page 1007.


Charting data
One of the most important considerations when you chart data is the way that you supply the data to the cfchart tag.
You can supply data in the following ways:

* Specify individual data points by using cfchartdata tags.

* Provide all the data in a single query by using cfchartseries tags.

* Combine data from a query with additional data points from cfchartdata tags.

* Provide all the data in a report created with Report Builder. For more information, see "Creating Reports and
    Documents for Printing" on page 1007.

Note: The cfchart tag charts numeric data only. As a result, convert any dates, times, or preformatted currency values,
such as $3,000.53, to integers or real numbers.



                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         985
Working with Documents, Charts, and Reports




Charting individual data points
When you chart individual data points, you specify each data point by inserting a cfchartdata tag in the
cfchartseries tag body. For example, the following code creates a simple pie chart:

<cfchart>
      <cfchartseries type="pie">
          <cfchartdata item="New Vehicle Sales" value=500000>
          <cfchartdata item="Used Vehicle Sales" value=250000>
          <cfchartdata item="Leasing" value=300000>
          <cfchartdata item="Service" value=400000>
      </cfchartseries>
</cfchart>

This pie chart displays four types of revenue for a car dealer. Each cfchartdata tag specifies the income for a
department and a description for the legend.

Note: If two data points have the same item name, ColdFusion creates a graph of the value for the last one specified within
the cfchart tag.

The cfchartdata tag lets you specify the following information about a data point:


 Attribute                Description

 value                    The data value to chart. This attribute is required.

 item                     (Optional) The description for this data point. The item appears on the horizontal axis of bar and line charts, on
                          the vertical axis of horizontal bar charts, and in the legend of pie charts.



Charting a query
Each bar, dot, line, or slice of a chart represents data from one row/column coordinate in your result set. A related
group of data is called a chart series.

Because each bar, dot, line, or slice represents the intersection of two axes, craft the query result set such that the row
and column values have meaning when displayed in a chart. Often, doing so requires you aggregate data in the query.
You typically aggregate data in a query using one of the following:

* Specify a SQL aggregate function (SUM, AVG, MAX, and so on) using a GROUP BY clause in the SELECT
   statement.

* Use a Query of Queries.

* Retrieve data from a view, instead of a table.

When you chart a query, you specify the query name using the query attribute of the cfchartseries tag. For
example, the code for a simple bar chart could be as follows:

<cfchart
          xAxisTitle="Department"
          yAxisTitle="Salary Average"
      >
      <cfchartseries
          type="bar"
          query="DataTable"
          valueColumn="AvgByDept"
          itemColumn="Dept_Name"
          />
</cfchart>




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     986
Working with Documents, Charts, and Reports




This example displays the values in the AvgByDept column of the DataTable query. It displays the Dept_Name column
value as the item label by each bar.

The following table lists the attributes of the cfchartseries tag that you use when working with queries:


 Attribute               Description

 query                   The query that contains the data. Also specify the valueColumn and itemColumn.

 valueColumn             The query column that contains the values to chart.

 itemColumn              The query column that contains the description for this data point. The item normally appears on the horizontal
                         axis of bar and line charts, on the vertical axis of horizontalbar charts, and in the legend in pie charts.



Charting a query of queries
In addition to charting the results of a query, you can also chart the results of a query of queries. For more information
about using query of queries, see "Using Query of Queries" on page 428. Query of queries provides significant power
in generating the data for the chart. For example, you can use aggregating functions such as SUM, AVG, and GROUP
BY to create a query of queries with statistical data based on a raw database query. For more information, see "Using
Query of Queries" on page 428.

You can also take advantage of the ability to dynamically reference and modify query data. For example, you can loop
through the entries in a query column and reformat the data to show whole dollar values.

The example in the following procedure analyzes the salary data in the cfdocexamples database using a query of
queries, and displays the data as a bar chart.

1 Create a ColdFusion page with the following content:

    <!--- Get the raw data from the database. --->
   <cfquery name="GetSalaries" datasource="cfdocexamples">
         SELECT Departmt.Dept_Name,
              Employee.Salary
         FROM Departmt, Employee
         WHERE Departmt.Dept_ID = Employee.Dept_ID
   </cfquery>


   <!--- Generate a query with statistical data for each department. --->
   <cfquery dbtype = "query" name = "DeptSalaries">
         SELECT
              Dept_Name,
              AVG(Salary) AS AvgByDept
         FROM GetSalaries
         GROUP BY Dept_Name
   </cfquery>


   <!--- Reformat the generated numbers to show only thousands. --->
   <cfloop index="i" from="1" to="#DeptSalaries.RecordCount#">
         <cfset DeptSalaries.AvgByDept[i]=Round(DeptSalaries.AvgByDept[i]/1000)*1000>
   </cfloop>


   <html>
   <head>
         <title>Employee Salary Analysis</title>
   </head>


   <body>
   <h1>Employee Salary Analysis</h1>



                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     987
Working with Documents, Charts, and Reports




   <!--- Bar chart, from DeptSalaries Query of Queries. --->
   <cfchart
              xAxisTitle="Department"
              yAxisTitle="Salary Average"
              font="Arial"
              gridlines=6
              showXGridlines="yes"
              showYGridlines="yes"
              showborder="yes"
              show3d="yes"
         >


         <cfchartseries
              type="bar"
              query="DeptSalaries"
              valueColumn="AvgByDept"
              itemColumn="Dept_Name"
              seriesColor="olive"
              paintStyle="plain"
         />
   </cfchart>


   <br>
   </body>
   </html>

2 Save the page as chartdata.cfm in the myapps directory under the web root directory. For example, the directory
   path in Windows could be C:\Inetpub\wwwroot\myapps.

3 Return to your browser and enter the following URL to view the chartdata.cfm page:

   http://localhost/myapps/chartdata.cfm

Note: If a query contains two rows with the same value for the itemColumn attribute, ColdFusion graphs the last row in
the query for that value. For the preceding example, if the query contains two rows for the Sales department, ColdFusion
graphs the value for the last row in the query for Sales.


Reviewing the code
The following table describes the code and its function:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           988
Working with Documents, Charts, and Reports




 Code                                                                Description

 <cfquery name="GetSalaries" datasource="cfdocexamples">             Query the cfdocexamples database to get the Dept_Name
 SELECT Departmt.Dept_Name, Employee.Salary                          and Salary for each employee. Because the Dept_Name is
 FROM Departmt, Employee
                                                                     in the Departmt table and the Salary is in the Employee
 WHERE Departmt.Dept_ID = Employee.Dept_ID
                                                                     table, you need a table join in the WHERE clause. You can
 </cfquery>
                                                                     use the raw results of this query elsewhere on the page.

 <cfquery dbtype = "query" name = "DeptSalaries">                    Generate a new query from the GetSalaries query. Use the
 SELECT                                                              AVG aggregating function to get statistical data on the
 Dept_Name,
                                                                     employees. Use the GROUP BY statement to ensure that
 AVG(Salary) AS AvgByDept
                                                                     only one row exists for each department.
 FROM GetSalaries
 GROUP BY Dept_Name
 </cfquery>

 <cfloop index="i" from="1" to="#DeptSalaries.RecordCount#">         Loop through all the rows in the DeptSalaries query and
 <cfset                                                              round the salary data to the nearest thousand. This loop
 DeptSalaries.AvgByDept[i]=Round(DeptSalaries.AvgByDept[i]/1
                                                                     uses the RecordCount query variable to get the number of
 000)*1000>
                                                                     rows, and directly changes the contents of the query
 </cfloop>
                                                                     object.

 <cfchart                                                            Create a bar chart using the data from the AvgByDept
 xAxisTitle="Department"                                             column of the DeptSalaries query. Label the bars with the
 yAxisTitle="Salary Average"
                                                                     department names.
 font="Arial"
 gridlines=6
 showXGridlines="yes"
 showYGridlines="yes"
 showborder="yes"
 show3d="yes"
 >
  <cfchartseries
 type="bar"
 query="DeptSalaries"
 valueColumn="AvgByDept"
 itemColumn="Dept_Name"
 seriesColor="olive"
 paintStyle="plain"
 />
 </cfchart>


You can also rewrite this example to use the cfoutput and cfchartdata tags within the cfchartseries tag, instead
of using the loop, to round the salary data, as the following code shows:

<cfchartseries
      type="bar"
      seriesColor="olive"
      paintStyle="plain">


      <cfoutput query="deptSalaries">
           <cfchartdata item="#dept_name#" value=#Round(AvgByDept/1000)*1000#>
      </cfoutput>


</cfchartseries>


Combining a query and data points
To chart data from both query and individual data values, you specify the query name and related attributes in the
cfchartseries tag, and provide additional data points by using the cfchartdata tag.

ColdFusion displays the chart data specified by a cfchartdata tag before the data from a query, for example, to the
left on a bar chart. You can use the sortXAxis attribute of the cfchart tag to sort data alphabetically along the x axis.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   989
Working with Documents, Charts, and Reports




One use of combining queries and data points is to provide data that is missing from the database; for example, to
provide the data for one department if the data for that department is missing. The example in the following procedure
adds data for the Facilities and Documentation departments to the salary data obtained from the query shown in the
previous section:

1 Open the chartdata.cfm file in your editor.

2 Edit the cfchart tag so that it appears as follows:

   <cfchart chartwidth="600">
        <cfchartseries
             type="bar"
             query="DeptSalaries"
             itemColumn ="Dept_Name"
             valueColumn="AvgByDept"
             >
             <cfchartdata item="Facilities" value="35000">
             <cfchartdata item="Documentation" value="45000">
        </cfchartseries>
   </cfchart>

3 Save the page as chartqueryanddata.cfm in the myapps directory under the web root directory. For example, the
   directory path in Windows could be C:\Inetpub\wwwroot\myapps.

4 Return to your browser and enter the following URL to view the chartqueryanddata.cfm page:

   http://localhost/myapps/chartqueryanddata.cfm


Charting multiple data collections
Sometimes, you could have more than one series of data to display on a single chart, or you want to compare two sets
of data on the same chart. In some cases, you might want to use different charting types on the same chart. For
example, you could include a line chart on a bar chart.

To combine multiple data series into a single chart, insert multiple cfchartseries tags within a single cfchart tag.
You control how the multiple data collections are charted using the seriesPlacement attribute of the cfchart tag.
Using this attribute, you can specify the following options:

default Let ColdFusion determine the best method for combining the data.

cluster Place corresponding chart elements from each series next to each other.

stacked Combine the corresponding elements of each series.

percent Show the elements of each series as a percentage of the total of all corresponding elements.

The following image shows these options for combining two bar charts:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       990
Working with Documents, Charts, and Reports




You can also combine chart types. The following is a combination bar and line chart:




The only chart type that you cannot mix with others is the pie chart. If you define one of the data series to use a pie
chart, no other chart appears.

The example in the following procedure creates the chart in the previous figure, which shows a bar chart with a line
chart added to it. In this example, you chart the salary of permanent employees (bar) against contract employees (line).

Note: The layering of multiple series depends on the order that you specify the cfchartseries tags. For example, if you
specify a bar chart first and a line chart second, the bar chart appears in front of the line chart in the final chart.


Create a combination bar chart and line chart
1 Open the chartdata.cfm file in your editor.

2 Edit the cfchart tag so that it appears as follows:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                          991
Working with Documents, Charts, and Reports




   <cfchart
             backgroundColor="white"
             xAxisTitle="Department"
             yAxisTitle="Salary Average"
             font="Arial"
             gridlines=6
             showXGridlines="yes"
             showYGridlines="yes"
             showborder="yes"
        >
        <cfchartseries
             type="line"
             seriesColor="blue"
             paintStyle="plain"
             seriesLabel="Contract Salaries"
             >
                  <cfchartdata item="HR" value=70000>
                  <cfchartdata item="Marketing" value=95000>
                  <cfchartdata item="Sales" value=80000>
                  <cfchartdata item="Training" value=93000>
             </cfchartseries>
        <cfchartseries
             type="bar"
             query="DeptSalaries"
             valueColumn="AvgByDept"
             itemColumn="Dept_Name"
             seriesColor="gray"
             paintStyle="plain"
             seriesLabel="Dept. Average Salaries"
             />


  </cfchart>

3 Save the file as chart2queries.cfm in the myapps directory under the web root directory.

4 Return to your browser and view the chart2queries.cfm page.


Controlling chart appearance
You can control the appearance of charts by doing any of the following:

* Using the default chart styles included with ColdFusion

* Using the attributes of the cfchart and cfchartseries tags

* Creating your own chart styles


Using the default chart styles included with ColdFusion
ColdFusion supplies the following chart styles:

* beige

* blue

* default

* red

* silver



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                              992
Working with Documents, Charts, and Reports




* yellow

To use any of these styles, specify the style using the style attribute of the cfchart tag. The following example
illustrates using the beige style:

<cfchart style="beige">
       <cfchartseries type="pie">
           <cfchartdata item="New car sales" value="50000">
           <cfchartdata item="Used car sales" value="25000">
           <cfchartdata item="Leasing" value="30000">
           <cfchartdata item="Service" value="40000">
       </cfchartseries>
</cfchart>

You can specify the appearance of charts by using the attributes of the cfchart and cfchartseries tags.

You can optionally specify the following characteristics to the cfchart tag on the types of charts indicated:


 Chart characteristic Attributes used          Description                                                          Chart type

 File type            format                   Whether to send the chart to the user as a JPEG, PNG, or SWF file.   All
                                               The SWF file is the default format.

 Size                 chartWidth               The width and height, in pixels, of the chart. This size defines the All
                                               entire chart area, including the legend and background area
                      chartHeight
                                               around the chart.

                                               The default height is 240 pixels; the default width is 320 pixels.

 Color                foregroundColor          The colors used for foreground and background objects.               All

                      dataBackgroundColor      The default foreground color is black; the default background
                                               colors are white.
                      backgroundColor
                                               You can specify 16 color names, use any valid HTML color format,
                                               or specify an 8-digit hexadecimal value to specify the RGB value
                                               and transparency. If you use numeric format, use double number
                                               signs; for example, blue or ##FF33CC. To specify the color and
                                               transparency, use the format ##xxFF33CC, where xx indicates the
                                               transparency. The value FF indicates opaque; the value 00
                                               indicates transparent. For the complete list of colors, see
                                               Configuring and Administering ColdFusion.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 993
Working with Documents, Charts, and Reports




Chart characteristic Attributes used         Description                                                             Chart type

Labels               font                    The font attribute specifies the font for all text. The default value   All
                                             is Arial. If you are using a double-byte character set on UNIX, or
                     fontSize
                                             using a double-byte character set in Windows with a file type of
                                             Flash, specify ArialUnicodeMs as the font.
                     fontBold

                                             Note: If a chart attempts to use a font that is not installed on the
                     frontItalic
                                             ColdFusion server, it uses a different font that is available. Also, if
                     labelFormat             you do not specify the font, characters that are not ASCII, such as
                                             Japanese, Chinese, Korean, and so on, can display improperly.
                     xAxisTitle
                                             The fontSize specifies an Integer font size used for all text. The
                     yAxisTitle
                                             default value is 11.

                                             The fontBold attribute specifies to display all text as bold. The
                                             default value is no.

                                             The fontItalic attribute specifies to display all text as italic. The
                                             default value is no.

                                             The labelFormat attribute specifies the format of the y-axis
                                             labels, number, currency, percent, or date. The default value is
                                             number.

                                             The xAxisTitle and yAxisTitle attributes specify the title for
                                             each axis.

Border               showBorder              Use the showBorder attribute to draw a border around the chart.         All
                                             The foregroundColor attribute specifies the border color. The
                                             default value is no.

Grid lines           showXGridlines          Use the showXGridlines and showYGridlines attributes to                 Area
                                             display x-axis and y-axis grid lines. The default value no for x-axis
                     showYGridlines                                                                                  Bar
                                             gridlines, and yes for y-axis gridlines.

                     gridLines                                                                                       Cone
                                             The gridLines attribute specifies the total number of grid lines
                                             on the value axis, including the axis itself. The value of each grid    Curve
                                             line appears along the value axis. The cfchart tag displays
                                             horizontal grid lines only. The default value is 0, which means no      Cylinder
                                             grid lines.
                                                                                                                     Horizontalbar

                                                                                                                     Line

                                                                                                                     Pyramid

                                                                                                                     Scatter

                                                                                                                     Step

Slice style          pieSliceStyle           Displays the pie chart as solid or sliced. The default value is         Pie
                                             sliced.

Markers              showMarkers             The showMarkers attribute displays markers at the data points           All
                                             for two-dimensional line, curve, and scatter charts. The default
                     markerSize
                                             value is yes.

                                             The markerSize attribute specifies an integer number of pixels
                                             for the marker size. ColdFusion determines the default value.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               994
Working with Documents, Charts, and Reports




Chart characteristic Attributes used         Description                                                           Chart type

Value axis           scaleFrom               The minimum and maximum points on the data axis.                      Area

                     scaleTo                 By default, the minimum is 0 or the lowest negative chart data        Bar
                                             value, and the maximum is the largest data value.
                                                                                                                   Cone
                                             Note: If you specify a scaleFrom or scaleTo attribute that would
                                                                                                                   Curve
                                             result in cropping the chart, cfchart uses a value that shows the
                                             entire chart without cropping.                                        Cylinder

                                                                                                                   Horizontalbar

                                                                                                                   Line

                                                                                                                   Pyramid

                                                                                                                   Scatter

                                                                                                                   Step

Axis type            XAxisType               Whether the x axis corresponds to a numeric scale or identifies       Area
                                             different categories, and how to sort the items on the axis.
                     sortXAxis                                                                                     Bar
                                             If the XAxisType attribute value is scale, the x axis is numeric.
                                                                                                                   Cone
                                             All cfchartdataitem attribute values must be numeric, and the
                                             axis is automatically sorted numerically. The scale value lets you    Curve
                                             create graphs of numeric relationships, such as population
                                             against age.                                                          Cylinder

                                             If the attribute value is category (the default), the axis indicates  Horizontalbar
                                             the data category.
                                                                                                                   Line
                                             The sortXAxis attribute determines the order of items when
                                                                                                                   Pyramid
                                             you specify the cfchartdataitem attribute, whose values are
                                             treated as text. By default, the items are displayed in the order in  Scatter
                                             which they are entered in the first chart series.
                                                                                                                   Step

3D appearance        show3D                  The show3D attribute displays the chart in three dimensions. The      All
                                             default value is no.
                     xOffset
                                             The xOffset and yOffset attributes specify the amount by
                     yOffset
                                             which to rotate the chart on a horizontal axis (xOffset) or vertical
                                             axis (yOffset). The value 0 is flat (no rotation), -1 and 1 are for a
                                             full 90 degree rotation left.

                                             (-1) or right (1). The default value is 0.1

Multiple series      showLegend              The showLegend attribute lets you display the chart legend when       All
                                             the chart contains more than one series of data. The default value
                     seriesPlacement
                                             is Yes.

                                             The seriesPlacement attribute specifies the location of each
                                             series relative to the others. By default, ColdFusion determines the
                                             best placement based on the graph type of each series.

Tips                 tipStyle                The tipStyle attribute lets you display a small pop-up window         All
                                             that shows information about the chart element pointed to by the
                     tipBGColor
                                             mouse pointer. Options are none, mousedown, or mouseover.
                                             The default value is mouseover.

                                             The tipBGColor attribute specifies the background color of the
                                             tip window for Flash format only. The default value is white.


You can also use the cfchartseries tag to specify attributes of chart appearance. The following table describes these
attributes:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     995
Working with Documents, Charts, and Reports




 Chart characteristic Attributes used             Description                                                              Chart type

 Multiple series     seriesLabel                  The seriesLabel attribute specifies the text that displays for the        All
                                                  series label.
                     seriesColor
                                                  The seriesColor attribute specifies a single color of the bar, line,
                                                  pyramid, and so on. For pie charts, the color is that of the first slice.
                                                  Subsequent slices are automatically colored based on the specified
                                                  initial color, or use the colorList attribute.

 Paint               paintStyle                   Specifies the way color is applied to a data series. You can specify      All
                                                  solid color, raised button, linear gradient fill with a light center and
                                                  darker outer edge, and gradient fill on lighter version of color. The
                                                  default value is solid.

 Data point colors    colorList                   A comma-separated list of colors to use for each data point for bar,      Pie
                                                  pyramid, area, horizontalbar, cone, cylinder, step, and pie charts.

                                                  You can specify 16 color names, use any valid HTML color format, or
                                                  specify an 8-digit hexadecimal value to specify the RGB value and
                                                  transparency. If you use numeric format, use double number signs;
                                                  for example, blue or ##FF33CC. To specify the color and
                                                  transparency, use the format ##xxFF33CC, where xx indicates the
                                                  transparency. The value FF indicates opaque; the value 00 indicates
                                                  transparent. For the complete list of colors, see Configuring and
                                                  Administering ColdFusion.

                                                  If you specify fewer colors than data points, the colors repeat. If you
                                                  specify more colors than data points, the extra colors are not used.

 Data markers         markerStyle                 Specifies the shape used to mark the data point. Shapes include           Curve
                                                  circle,diamond,letterx,mcross,rcross,rectangle,snow,
                                                                                                                            Line
                                                  and triangle. Supported for two-dimensional charts. The default
                                                  value is rectangle.                                                       Scatter

 Labels               dataLabelStyle              Specifies the way in which the color is applied to the item in the        All
                                                  series Styles include None,Value, Rowlabel, Columnlabel, and
                                                  Pattern.



Creating your own chart styles
You can create your own chart styles by doing either of the following:

* Modifying the chart style XML files

* Using WebCharts3D to create chart styles


Modifying the chart style XML files
You can modify the chart styles included with ColdFusion to create your own chart styles. The files that contain the
style information are XML files located in the cf_root\charting\styles directory. Modify only attributes specified in the
file. To specify additional attributes, follow the instructions in the section "Using WebCharts3D to create chart styles"
on page 996.

Note: Two XML files exist for each default chart style. For example, the beige style for pie charts is defined in the
beige_pie.xml file; the beige style for all other types of charts is defined in the beige.xml file.

1 Open the XML file that you want to modify, for example beige.xml.

2 Modify the file contents.

3 Save the file with a different name; for example myBeige.xml.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      996
Working with Documents, Charts, and Reports




Using WebCharts3D to create chart styles
Starting with ColdFusion MX 7, ColdFusion includes the WebCharts3D utility, which you can use to create chart style
files.

1 Start WebCharts3D by double-clicking the webcharts.bat file in the CFusion\charting directory.

2 (Optional) Open an existing chart.

3 Make the changes you want to the appearance of the chart.

    Note: To use the chart style file in the cfchart tag, you can only make the modifications indicated in the table that
    follows this procedure.

4 Click the XML style tab.

5 Click the Save button in the lower-right corner.

6 Specify the name of the file; for example, mystyle.xml.

7 Specify the directory in which you want to save the chart style file.

    Note: ColdFusion uses the same rules to look for the chart style XML files as it does for files included using the
    cfinclude tag. For more information, seecfinclude.

8 Click Save.

The following table lists the attributes of the cfchart and cfchartseries tags and the associated WebCharts3D
commands:


 Attribute                               WebCharts3D command

 chartHeight                             Drag the chart by handles.

 chartWidth                              Drag the chart by handles.

 dataBackgroundColor                     Background: minColor (type must be PlainColor)

 font                                    font: Family (specify only supported fonts)

 fontBold                                font: check Bold

 fontItalic                              font: check Italic

 fontSize                                font: Size

 foregroundColor                         foreground

 gridlines                               X-axis: labelcount

 labelFormat                             Y-axis: LabelFormat: Number | Percent| Currency | Datetime

 markerSize                              Elements: markerSize

 pieSliceStyle                           style: solid | slice

 rotated                                 Type Frame chart: Elements: Shape:

 scaleFrom                               Yaxis: isAbsolute; scaleMin(int)

 scaleTo                                 Yaxis: isAbsolute; scaleMax(int)

 seriesPlacement                         Elements: place

 show3D                                  is3D

 showBorder                              Decoration: style (none or simple)?




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                      997
Working with Documents, Charts, and Reports




Attribute                              WebCharts3D command

showLegend                             Legend: isVisible

showMarkers                            Elements: showMarkers

showXGridlines                         Frame: isVGridVisible

showYGridlines                         Frame: isHGridVisible

tipbgColor                             Popup: background

tipStyle                               Popup: show on MouseOver | show on MouseDown | Disabled

url                                    Elements: action | Series: action

xAxisTitle                             X-axis: TitleStyle: text (enter text)

xAxisType                              X-axis: type: (category or scale)

xOffset                                Frame: xDepth

yAxisTitle                             Y-axis: TitleStyle: text (enter text)

yAxisType                              Currently has no effect.

yOffset                                Frame: yDepth


The following table lists the attributes of the cfchartseries tag and the associated WebCharts3D commands:


Attribute                               WebCharts3D command

colorlist                               Elements: series: Paint: color

markerStyle                             Elements: series: Marker type: Rectangle | Triangle | Diamond | Circle | Letter | MCROSS | Snow |
                                        RCROSS

paintStyle                              Paint: paint: Plain | Shade | Light

seriesColor                             Elements: series: Paint: color

seriesLabel                             Elements: series:

type                                    Type: Pie chart |

                                        Type Frame chart: Elements: Shape: Bar | Line | Pyramid | Area | Curve | Step | Scatter | Cone |
                                        Cylinder | Horizontalbar |



Creating charts: examples

Creating a bar chart
The example in the following procedure adds a title to the bar chart, specifies that the chart is three-dimensional, adds
grid lines, sets the minimum and maximum y-axis values, and uses a custom set of colors.

1 Open the chartdata.cfm file in your editor.

2 Edit the cfchart tag so that it appears as follows:




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 998
Working with Documents, Charts, and Reports




   <!--- Bar chart, from Query of Queries --->
  <cfchart
             scaleFrom=40000
             scaleTo=100000
             font="arial"
             fontSize=16
             gridLines=4
             show3D="yes"
             foregroundcolor="##000066"
             databackgroundcolor="##FFFFCC"
             chartwidth="450"
        >


        <cfchartseries
             type="bar"
             query="DeptSalaries"
             valueColumn="AvgByDept"
             itemColumn="Dept_Name"
             seriescolor="##33CC99"
             paintstyle="shade"
             />


  </cfchart>

3 Save the file as chartdatastyle1.cfm.

4 View the chartdatastyle1.cfm page in your browser.


Reviewing the code
The following table describes the code in the preceding example.


Code                                     Description

scaleFrom=40000                          Set the minimum value of the vertical axis to 40000.

scaleTo=100000                           Set the maximum value of the vertical axis to 100000. The minimum value is the default, 0.

font="arial"                             Displays text using the Arial font.

fontSize=16                              Makes the point size of the labels 16 points.

gridLines = 4                            Displays four grid lines between the top and bottom of the chart.

show3D = "yes"                           Shows the chart in 3D.

foregroundcolor="##000066"               Sets the color of the text, gridlines, and labels.

databackgroundcolor="##FFFFCC"           Sets the color of the background behind the bars.

seriescolor="##33CC99"                   Sets the color of the bars.

paintstyle="shade"                       Sets the paint display style.



Creating a pie chart
The example in the following procedure adds a pie chart to the page.

1 Open the chartdata.cfm file in your editor.

2 Edit the DeptSalaries query and the cfloop code so that it appears as follows:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                         999
Working with Documents, Charts, and Reports




   <!--- A query to get statistical data for each department. --->
  <cfquery dbtype = "query" name = "DeptSalaries">
        SELECT
             Dept_Name,
             SUM(Salary) AS SumByDept,
             AVG(Salary) AS AvgByDept
        FROM GetSalaries
        GROUP BY Dept_Name
  </cfquery>


  <!--- Reformat the generated numbers to show only thousands. --->
  <cfloop index="i" from="1" to="#DeptSalaries.RecordCount#">
        <cfset DeptSalaries.SumByDept[i]=Round(DeptSalaries.SumByDept[i]/
        1000)*1000>
        <cfset DeptSalaries.AvgByDept[i]=Round(DeptSalaries.AvgByDept[i]/
        1000)*1000>
  </cfloop>

3 Add the following cfchart tag:

   <!--- Pie chart, from DeptSalaries Query of Queries. --->
  <cfchart
             tipStyle="mousedown"
             font="Times"
             fontsize=14
             fontBold="yes"
             backgroundColor = "##CCFFFF"
             show3D="yes"
             >


        <cfchartseries
             type="pie"
             query="DeptSalaries"
             valueColumn="SumByDept"
             itemColumn="Dept_Name"
             colorlist="##6666FF,##66FF66,##FF6666,##66CCCC"
             />
  </cfchart>
  <br>

4 Save the file as chartdatapie1.cfm.

5 View the chartdatapie1.cfm page in your browser:


Reviewing the code
The following table describes the code and its function:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                          1000
Working with Documents, Charts, and Reports




Code                                                   Description

SUM(Salary) AS SumByDept,                              In the DeptSalaries query, add a SUM aggregation function to get the sum of all
                                                       salaries per department.

<cfset                                                 In the cfloop tag, round the salary sums to the nearest thousand.
DeptSalaries.SumByDept[i]=Round(DeptSalari
es.SumByDept[i]/ 1000)*1000>

<cfchart                                               Show a tip only when a user clicks the chart, display text in Times bold font, set the
tipStyle="mousedown"                                   background color to light blue, and display the chart in three dimensions.
font="Times"
fontsize=14
fontBold="yes"
backgroundColor = "##CCFFFF"
show3D="yes"
>

<cfchartseries                                         Create a pie chart using the SumByDept salary sum values from the DeptSalaries
type="pie"                                             query.
query="DeptSalaries"
valueColumn="SumByDept"                                Use the contents of the Dept_Name column for the item labels displayed in the
itemColumn="Dept_Name"                                 chart legend.

colorlist="##6666FF,##66FF66,##FF6666,##66             Get the pie slice colors from a custom list, which uses hexadecimal color numbers.
CCCC"
                                                       The double number signs prevent ColdFusion from trying to interpret the color
/>
                                                       data as variable names.



Creating an area chart
The example in the following procedure adds an area chart to the salaries analysis page. The chart shows the average
salary by start date to the salaries analysis page. It shows the use of a second query of queries to generate a new analysis
of the raw data from the GetSalaries query. It also shows the use of additional cfchart attributes.

1 Open the chartdata.cfm file in your editor.

2 Edit the GetSalaries query so that it appears as follows:

    <!-- Get the raw data from the database. -->
   <cfquery name="GetSalaries" datasource="cfdocexamples">
        SELECT Departmt.Dept_Name,
              Employee.StartDate,
              Employee.Salary
        FROM Departmt, Employee
        WHERE Departmt.Dept_ID = Employee.Dept_ID
   </cfquery>

3 Add the following code before the html tag:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                       1001
Working with Documents, Charts, and Reports




   <!--- Convert start date to start year. --->
  <!--- Convert the date to a number for the query to work --->
  <cfloop index="i" from="1" to="#GetSalaries.RecordCount#">
  <cfset GetSalaries.StartDate[i]=NumberFormat(DatePart("yyyy", GetSalaries.StartDate[i])
  ,9999)>
  </cfloop>


  <!--- Query of Queries for average salary by start year. --->
  <cfquery dbtype = "query" name = "HireSalaries">
        SELECT
             StartDate,
             AVG(Salary) AS AvgByStart
        FROM GetSalaries
        GROUP BY StartDate
  </cfquery>


  <!--- Round average salaries to thousands. --->
  <cfloop index="i" from="1" to="#HireSalaries.RecordCount#">
        <cfset HireSalaries.AvgByStart[i]=Round(HireSalaries.AvgByStart[i]/1000)*1000>
  </cfloop>

4 Add the following cfchart tag before the end of the body tag block:

   <!--- Area-style Line chart, from HireSalaries Query of Queries. --->
  <cfchart
             chartWidth=400
             BackgroundColor="##FFFF00"
             show3D="yes"
        >
        <cfchartseries
             type="area"
             query="HireSalaries"
             valueColumn="AvgByStart"
             itemColumn="StartDate"
         />
  </cfchart>
  <br>

5 Save the page.

6 View the chartdata.cfm page in your browser.


Reviewing the code
The following table describes the code and its function:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 1002
Working with Documents, Charts, and Reports




 Code                                                       Description

 Employee.StartDate,                                        Add the employee start date to the data in the GetSalaries query.

 <cfloop index="i" from="1"                                 Use a cfloop tag to extract the year of hire from the hire data, and
 to="#GetSalaries.RecordCount#">                            convert the result to a four-digit number.
 <cfset
 GetSalaries.StartDate[i]=NumberFormat(DatePart("yy
 yy", GetSalaries.StartDate[i]) ,9999)>
 </cfloop>

 <cfquery dbtype = "query" name = "HireSalaries">           Create a second query from the GetSalaries query. This query contains
  SELECT                                                    the average salary for each start year.
 StartDate,
 AVG(Salary) AS AvgByStart
 FROM GetSalaries
 GROUP BY StartDate
 </cfquery>

 <cfloop index="i" from="1"                                 Round the salaries to the nearest thousand.
 to="#HireSalaries.RecordCount#">
 <cfset
 HireSalaries.AvgByStart[i]=Round(HireSalaries.AvgB
 yStart[i]/1000)*1000>
 </cfloop>

  <cfchart                                                  Create an area chart using the HireSalaries query. Chart the average
 chartWidth=400                                             salaries against the start date.
 BackgroundColor="##FFFF00"
 show3D="yes"                                               Limit the chart width to 400 pixels, show the chart in three dimensions,
 >                                                          and set the background color to white.
 <cfchartseries
 type="area"
 query="HireSalaries"
 valueColumn="AvgByStart"
 itemColumn="StartDate"
  />
 </cfchart>



Setting curve chart characteristics
Curves charts use the attributes already discussed. However, curve charts require a large amount of processing to
render. For fastest performance, create them offline, write them to a file or variable, and then reference them in your
application pages. For information on creating offline charts, see "Writing a chart to a variable" on page 1003.


Administering charts
Use the ColdFusion Administrator to administer charts. In the Administrator, you can choose to save cached charts
in memory or to disk. You can also specify the number of charts to cache, the number of charting threads, and the disk
file for caching images to disk.

ColdFusion caches charts as they are created. In that way, repeated requests of the same chart load the chart from the
cache rather than having ColdFusion render the chart over and over again.

Note: You do not have to perform any special coding to reference a cached chart. Whenever you use the cfchart tag,
ColdFusion inspects the cache to see if the chart has already been rendered. If so, ColdFusion loads the chart from the
cache.

The following table describes the settings for the ColdFusion charting and graphing engine:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                       1003
Working with Documents, Charts, and Reports




 Option                   Description

 Cache Type               Sets the cache type. Charts can be cached in memory or to disk. Caching in memory is faster, but more memory
                          intensive.

 Maximum number of        Specifies the maximum number of charts to store in the cache. When the limit is reached, the oldest chart in the
 images in cache          cache is deleted to make room for a new one. The maximum number of charts you can store in the cache is 250.

 Max number of charting   Specifies the maximum number of chart requests that can be processed concurrently. The minimum number is
 threads                  1 and the maximum is 5. Higher numbers are more memory-intensive.

 Disk cache location      When caching to disk, specifies the directory in which to store the generated charts.



Writing a chart to a variable
In some cases, your application could have charts that are static or charts that, because of the nature of the data input,
take a long time to render. In this scenario, you can create a chart and write it to a variable.

Once written to a variable, other ColdFusion pages can access the variable to display the chart, or you can write the
variable to disk to save the chart to a file. Saving the variable on disk lets you create or update charts only as needed,
rather than every time someone requests a page that contains a chart.

You use the name attribute of the cfchart tag to write a chart to a variable. If you specify the name attribute, the chart
is not rendered in the browser but is written to the variable.

You can save the chart as an Adobe Flash SWF file, or as a JPEG or PNG image file. If you save the image as a SWF file,
you can pass the variable back to a Flash client using ColdFusion Flash Remoting. For more information, see "Using
the Flash Remoting Service" on page 606.

Note: If you write the chart to a JPEG or PNG file, mouseover tips and URLs embedded in the chart for data drill-down
do not work when you redisplay the image from the file. However, if you save the image as a SWF file, both tips and drill-
down URLs work. For more information on data drill-down, see "Linking charts to URLs" on page 1004.


Write a chart to a variable and a file
1 Create a ColdFusion page with the following content:

    <cfchart name="myChart" format="jpg">
         <cfchartseries type="pie">
               <cfchartdata item="New Vehicle Sales" value=500000>
               <cfchartdata item="Used Vehicle Sales" value=250000>
               <cfchartdata item="Leasing" value=300000>
               <cfchartdata item="Service" value=400000>
         </cfchartseries>
    </cfchart>


    <cffile
         action="WRITE"
         file="c:\inetpub\wwwroot\charts\vehicle.jpg"
         output="#myChart#">
    <img src="/charts/vehicle.jpg" height=240 width=320>

2 Save the page as chartToFile.cfm in myapps under the web root directory.

3 View the chartToFile.cfm page in your browser.


Reviewing the code
The following table describes the highlighted code and its function:




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                              1004
Working with Documents, Charts, and Reports




 Code                                                          Description

 <cfchart name="myChart" format="jpg">                         Define a chart written to the myChart variable by using the JPEG
                                                               format.

 <cffile action="WRITE"                                        Use the cffile tag to write the chart to a file.
 file="c:\inetpub\wwwroot\charts\vehicle.jpg"
 output="#myChart#">


 <img src="/charts/vehicle.jpg" height=240                     Use the HTML img tag to display the chart.
 width=320>



Linking charts to URLs
ColdFusion provides a data drill-down capability with charts, which lets you click the data and legend areas of a chart
to request a URL. For example, if you have a pie chart and want a user to be able to select a pie wedge for more
information, you can build that functionality into your chart.

You use the url attribute of the cfchart tag to specify the URL to open when a user clicks anywhere on the chart. For
example, the following code defines a chart that opens the page moreinfo.cfm when a user clicks the chart:

<cfchart
     xAxisTitle="Department"
     yAxisTitle="Salary Average"
     url="moreinfo.cfm"
     >


           <cfchartseries
                seriesLabel="Department Salaries"
           ...
           />


</cfchart>

You can use the following variables in the url attribute to pass additional information to the target page:

* $VALUE$: The value of the selected item, or an empty string

* $ITEMLABEL$: The label of the selected item, or an empty string

* $SERIESLABEL$: The label of the selected series, or an empty string

For example, to let users click the graph to open the page moreinfo.cfm, and pass all three values to the page, you use
the following URL:

url="moreinfo.cfm?Series=$SERIESLABEL$&Item=$ITEMLABEL$&Value=$VALUE$"

The variables are not enclosed in number signs like ordinary ColdFusion variables. They are enclosed in dollar signs.
If you click a chart that uses this url attribute value, it could generate a URL in the following form:

http://localhost:8500/tests/charts/moreinfo.cfm?
     Series=Department%20Salaries&Item=Training&Value=86000

You can also use JavaScript in the URL to execute client-side scripts. For an example, see "Linking to JavaScript from
a pie chart" on page 1007.


Dynamically linking from a pie chart
In the following example, when you click a pie wedge, ColdFusion displays a table that contains the detailed salary
information for the department represented by the wedge. The example is divided into two parts: creating the detail
page and making the pie chart dynamic.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               1005
Working with Documents, Charts, and Reports




Part 1: Creating the detail page
This page displays salary information for the department you selected when you click a wedge of the pie chart. The
department name is passed to this page using the $ITEMLABEL$ variable.

1 Create an application page with the following content:

   <cfquery name="GetSalaryDetails" datasource="cfdocexamples">
        SELECT Departmt.Dept_Name,
             Employee.FirstName,
             Employee.LastName,
             Employee.StartDate,
             Employee.Salary,
             Employee.Contract
        FROM Departmt, Employee
        WHERE Departmt.Dept_Name = '#URL.Item#'
        AND Departmt.Dept_ID = Employee.Dept_ID
        ORDER BY Employee.LastName, Employee.Firstname
  </cfquery>


  <html>
  <head>
        <title>Employee Salary Details</title>
  </head>


  <body>


  <h1><cfoutput>#GetSalaryDetails.Dept_Name[1]# Department
        Salary Details</cfoutput></h1>
  <table border cellspacing=0 cellpadding=5>
  <tr>
        <th>Employee Name</th>
        <th>StartDate</th>
        <th>Salary</th>
        <th>Contract?</th>
  </tr>
  <cfoutput query="GetSalaryDetails">
  <tr>
        <td>#FirstName# #LastName#</td>
        <td>#dateFormat(StartDate, "mm/dd/yyyy")#</td>
        <td>#numberFormat(Salary, "$999,999")#</td>
        <td>#Contract#</td>
  </tr>
  </cfoutput>
  </table>
  </body>
  </html>

2 Save the page as Salary_details.cfm in the myapps directory under the web root directory.


Reviewing the code
The following table describes the code and its function:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   1006
Working with Documents, Charts, and Reports




Code                                                              Description

<cfquery name="GetSalaryDetails"                                  Get the salary data for the department whose name was passed in
datasource="cfdocexamples">                                       the URL parameter string. Sort the data by the last and first names
SELECT Departmt.Dept_Name,
                                                                  of the employee.
Employee.FirstName,
Employee.LastName,
Employee.StartDate,
Employee.Salary,
Employee.Contract
FROM Departmt, Employee
WHERE Departmt.Dept_Name = '#URL.Item#'
AND Departmt.Dept_ID = Employee.Dept_ID
ORDER BY Employee.LastName, Employee.Firstname
</cfquery>

<table border cellspacing=0 cellpadding=5>                        Display the data retrieved by the query as a table. Format the start
<tr>                                                              date into standard month/date/year format, and format the salary
<th>Employee Name</th>
                                                                  with a leading dollar sign, comma separator, and no decimal
<th>StartDate</th>
                                                                  places.
<th>Salary</th>
<th>Contract?</th>
</tr>
<cfoutput query="GetSalaryDetails">
<tr>
<td>#FirstName# #LastName#</td>
<td>#dateFormat(StartDate, "mm/dd/yyyy")#</td>
<td>#numberFormat(Salary, "$999,999")#</td>
<td>#Contract#</td>
</tr>
</cfoutput>
</table>



Part 2: Making the chart dynamic
1 Open chartdata.cfm in your editor.

2 Edit the cfchart tag for the pie chart so it appears as follows:

   <cfchart
             font="Times"
             fontBold="yes"
             backgroundColor="##CCFFFF"
             show3D="yes"
             url="Salary_Details.cfm?Item=$ITEMLABEL$"
             >


        <cfchartseries
             type="pie"
             query="DeptSalaries"
             valueColumn="AvgByDept"
             itemColumn="Dept_Name"
             colorlist="##990066,##660099,##006699,##069666"
        />
  </cfchart>

3 Save the file as chartdetail.cfm.

4 View the chartdata.cfm page in your browser.

5 Click the slices of the pie chart to request the Salary_details.cfm page and pass in the department name of the wedge
  you clicked. The salary information for that department appears.


Reviewing the code
The following table describes the highlighted code and its function:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                1007
Working with Documents, Charts, and Reports




 Code                                                    Description

 url = "Salary_Details.cfm?Item=$ITEMLABEL$"             When the user clicks a wedge of the pie chart, call the Salary_details.cfm
                                                         page in the current directory, and pass it the parameter named Item that
                                                         contains the department name of the selected wedge.



Linking to JavaScript from a pie chart
In the following example, when you click a pie wedge, ColdFusion uses JavaScript to display a pop-up window about
the wedge.


Create a dynamic chart with JavaScript:
1 Create an application page with the following content:

    <script>
   function Chart_OnClick(theSeries, theItem, theValue){
   alert("Series: " + theSeries + ", Item: " + theItem + ", Value: " + theValue);
        }
   </script>


   <cfchart
             xAxisTitle="Department"
             yAxisTitle="Salary Average"
             tipstyle=none
             url="javascript:Chart_OnClick('$SERIESLABEL$','$ITEMLABEL$','$VALUE$');"
        >
        <cfchartseries type="bar" seriesLabel="Average Salaries by Department">
             <cfchartData item="Finance" value="75000">
             <cfchartData item="Sales" value="120000">
             <cfchartData item="IT" value="83000">
             <cfchartData item="Facilities" value="45000">
        </cfchartseries>
   </cfchart>

2 Save the page as chartdata_withJS.cfm in the myapps directory under the web root directory.

3 View the chartdata_withJS.cfm page in your browser:

4 Click the slices of the pie chart to display the pop-up window.



Creating Reports and Documents for Printing

You can use Adobe ColdFusion tags, functions, and tools to create pages and reports that are suitable for printing.


About printable output
Although all web browsers let you print HTML pages, HTML-format pages are not optimized for printed output. For
example, lack of control over line breaks, page breaks, headers, footers, and page numbers are just a few of the
problems that you encounter when designing reports and other pages meant to be printed.

In the context of ColdFusion, the term printable output refers to pages that include the following features:

* Page numbers

* Headers and footers




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1008
Working with Documents, Charts, and Reports




* Page breaks

* Clickable hypertext links when viewed online

ColdFusion provides the following tags for generating printable output:

* cfdocument: Creates printable output and returns it to the browser or saves it in a file. For more information, see
   "Creating PDF and FlashPaper output with the cfdocument tag" on page 1008.

* cfreport: Uses the specified report definition to create printable output and return it to the browser or save it in
   a file. ColdFusion supports report definitions from the following tools:

    * ColdFusion Report Builder: The ColdFusion Report Builder is a banded report writer that is integrated with
       ColdFusion. For more information, see "About Report Builder" on page 1015.

    * Crystal Reports: Crystal Reports is a report writer whose report definitions you can use with the cfreport tag.
       For more information, see "Creating reports with Crystal Reports (Windows only)" on page 1014.

ColdFusion printable reports are available in the following formats:

FlashPaper ColdFusion creates a SWF file. Clients must have an up-to-date version of Adobe Flash Player installed.

Adobe Acrobat ColdFusion creates a PDF file. Clients must have the Adobe Reader installed.

Microsoft Excel (ColdFusion reporting only) ColdFusion creates an Excel spreadsheet.

Note: The Excel report output format type provides limited support for the formatting options available in ColdFusion
reporting. Images and charts are not supported and numeric data containing formatting (such as commas, percents,
currency) appears as plain text in Excel. The Excel output format supports simple reports only and it is recommended that
you give careful design and layout consideration to reports designed for Excel output.

Crystal Reports (Windows only) ColdFusion passes control to Crystal Reports, which creates HTML. This option is
available with the cfreport tag only.


Creating PDF and FlashPaper output with the cfdocument tag
The cfdocument tag converts everything between its start and end tags into PDF or FlashPaper output format and
returns it to the browser or saves it to a file. As a result you can easily convert HTML to printable output, as the
following example shows:

<cfdocument format="FlashPaper">
<p>This is a document rendered by the cfdocument tag.</p>
</cfdocument>

The cfdocument tag supports all HTML and CFML tags, with the following exceptions:

* cfchart

* Tags that generate content displayed in Flash Player.

* Interactive tags, such as form, cfform, and cfapplet

* JavaScript that dynamically modifies elements or element positions

Additionally, the HTML wrapped by the cfdocument tag must be well-formed, with end tags for every start tag and
proper nesting of block-level elements.

Note: ColdFusion does not return HTML and CFML outside the <cfdocument></cfdocument> pair.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1009
Working with Documents, Charts, and Reports




Creating basic reports from HTML and CFML
You can convert HTML-based reports into PDF or FlashPaper output by wrapping the HTML in the cfdocument start
and end tags, and specifying cfdocument attributes, as appropriate, to customize the following items:

* Page size

* Page orientation

* Margins

* Encryption (PDF only)

* User password and owner password (PDF only)

* Permissions (PDF only)

For complete information on these options, see the cfdocument tag discussion in the CFML Reference.

Note: Embedding fonts in the report can help ensure consistent display across multiple browsers and platforms. For more
information on the considerations related to embedding fonts, see "Creating a simple report" on page 1036.

The following example displays a list of employees, using a cfoutput tag to loop through the query:

 <cfdocument format="flashpaper">
<h1>Employee List</h1>
<!--- Inline query used for example purposes only. --->
<cfquery name="EmpList" datasource="cfdocexamples">
     SELECT FirstName, LastName, Salary, Contract
     FROM Employee
</cfquery>
<cfoutput query="EmpList">
#EmpList.FirstName#, #EmpList.LastName#, #LSCurrencyFormat(EmpList.Salary)#,
     #EmpList.Contract#<br>
</cfoutput>
</cfdocument>


Creating sections, headers, and footers
You can use the cfdocument and cfdocumentsection tags to fine-tune your printable output, as follows:

* cfdocumentitem: Creates page breaks, headers, or footers.

* cfdocumentsection: Divides output into sections, optionally specifying custom margins. Within a section, use the
   cfdocumentitem tag to specify unique headers and footers for each section. Each document section starts on a new page.


The cfdocumentitem tag
You use one or more cfdocumentitem tags to specify headers and footers or to create a page break. You can use
cfdocumentitem tags with or without the cfdocumentsection tag, as follows:

* With cfdocumentsection: The cfdocumentitem attribute applies only to the section, and overrides previously
   specified headers and footers.

* Without cfdocumentsection: The cfdocumentitem attribute applies to the entire document, as follows:

   * If the tag is at the top of the document, it applies to the entire document.

   * If the tag is in the middle of the document, it applies to the rest of the document.

   * If the tag is at the end of the document, it has no affect.

You can use the cfdocumentitem tag to create a running header for an entire document, as the following example
shows:



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1010
Working with Documents, Charts, and Reports




<cfdocument format="PDF">
<!--- Running header --->
<cfdocumentitem type="header">
    <font size="-3"><i>Directory Report</i></font>
</cfdocumentitem>
<h3>cfdirectory Example</h3>
<!--- Use cfdirectory to display directories by name and size --->
<cfdirectory
    directory="#GetDirectoryFromPath(GetTemplatePath())#"
    name="myDirectory" recurse="yes"
    sort="directory ASC, name ASC, size DESC">
<!---- Output the contents of the cfdirectory as a cftable ----->
<cftable query="myDirectory"
    htmltable colheaders>
    <cfcol header="DIRECTORY:" text="#directory#">
    <cfcol header="NAME:" text="#Name#">
    <cfcol header="SIZE:" text="#Size#">
</cftable>
</cfdocument>


The cfdocumentsection tag
When using cfdocumentsection, all text in the document must be enclosed within cfdocumentsection tags.
ColdFusion ignores HTML and CFML outside cfdocumentsection tags. The margin attributes override margins
specified in previous sections or in the parent cfdocument tag. If you specify margin attributes, the unit attribute of
the parent cfdocument tag control the units; the default for the unit attribute is inches.

Within a section, use the cfdocumentitem tag to specify unique headers and footers for each section and a page break
before each section, as the following example shows:

<cfquery datasource="cfdocexamples" name="empSalary">
SELECT Emp_ID, firstname, lastname, e.dept_id, salary, d.dept_name
FROM employee e, departmt d
WHERE e.dept_id = d.dept_id
ORDER BY d.dept_name
</cfquery>


<cfdocument format="PDF">
    <cfoutput query="empSalary" group="dept_id">
          <cfdocumentsection>
               <cfdocumentitem type="header">
                    <font size="-3"><i>Salary Report</i></font>
               </cfdocumentitem>
               <cfdocumentitem type="footer">
               <font size="-3">Page #cfdocument.currentpagenumber#</font>
               </cfdocumentitem>
               <h2>#dept_name#</h2>
               <table width="95%" border="2" cellspacing="2" cellpadding="2" >
               <tr>
                    <th>Employee</th>
                    <th>Salary</th>
               </tr>
          <cfset deptTotal = 0 >
          <!--- inner cfoutput --->




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1011
Working with Documents, Charts, and Reports




           <cfoutput>
               <tr>
                    <td><font size="-1">
                    #empSalary.lastname#, #empSalary.firstname#</font>
                    </td>
                    <td align="right"><font size="-1">
                    #DollarFormat(empSalary.salary)#</font>
                    </td>
               </tr>
           <cfset deptTotal = deptTotal + empSalary.salary>
           </cfoutput>
               <tr>
                    <td align="right"><font size="-1">Total</font></td>
                    <td align="right"><font size="-1">#DollarFormat(deptTotal)#</font></td>
               </tr>
               <cfset deptTotal = 0>
               </table>
           </cfdocumentsection>
     </cfoutput>
</cfdocument>


Using the cfdocument scope
When you use the cfdocument tag, ColdFusion creates a scope named cfdocument. This scope contains the following
variables:

currentpagenumber Displays the current page number.

totalpagecount Displays the total page count.

currentsectionpagenumber Displays the current section number.

totalsectionpagecount Displays the total number of sections.

Note: You can use the cfdocument scope variables in expressions within the cfdocumentitem tag only.

You typically use these variables in a header or footer to display the current page number and total number or pages,
as the following example shows:

<cfdocumentitem type= "footer> #cfdocument.currentpagenumber# of
     #cfdocument.totalpagecount#</cfdocumentitem>


Creating bookmarks in PDF files
You can use the cfdocumentbookmark attribute to create bookmarks for each section within a PDF document, as the
following example shows:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               1012
Working with Documents, Charts, and Reports




<cfdocument format="PDF" bookmark="yes">
    <cfdocumentitem type="header">
    <font size="-1" align="center"><i>Building Better Applications</i></font>
    </cfdocumentitem>
    <cfdocumentitem type="footer">
    <font size="-1"><i>Page <cfoutput>#cfdocument.currentpagenumber# of
         #cfdocument.totalpagecount#</cfoutput></i></font>
    </cfdocumentitem>
    <cfdocumentsection name="Introduction">
         <h3>Introduction</h3>
         <p>The introduction goes here.</p>
    </cfdocumentsection>
    <cfdocumentsection name="Chapter 1">
         <h3>Chapter 1: Getting Started</h3>
         <p>Chapter 1 goes here.</p>
    </cfdocumentsection>
    <cfdocumentsection name="Chapter 2">
         <h3>Chapter 2: Building Applications</h3>
         <p>Chapter 2 goes here.</p>
    </cfdocumentsection>
    <cfdocumentsection name="Conclusion">
         <h3>Conclusion</h3>
         <p>The conclusion goes here.</p>
    </cfdocumentsection>
</cfdocument>

The bookmarks appear in the bookmarks panel of the PDF document.


Using cfhttp to display web pages
You can use the cfhttp tag in combination with the cfdocument tag to display entire web pages in PDF or FlashPaper
output format, as the following example shows:

<!--- You can pass a URL in the URL string --->
<cfparam name="url.target_url" default="http://www.boston.com">
<cfoutput>
<cfhttp url="#url.target_url#" resolveurl="yes">


<cfdocument format="FlashPaper">
<cfdocumentitem type="header">
    <cfoutput>#url.target_url#</cfoutput>
</cfdocumentitem>
<cfdocumentitem type="footer">
    <cfoutput>#cfdocument.currentpagenumber# / #cfdocument.totalpagecount#</cfoutput>
</cfdocumentitem>
<!--- Display the page --->
#cfhttp.filecontent#
</cfdocument>
</cfoutput>


Using advanced PDF options
The cfdocument tag supports the Acrobat security options, as the following table shows:




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 1013
Working with Documents, Charts, and Reports




 Security option          Description

 Encryption               Use the encryption attribute to specify whether PDF output is encrypted. Specify one of the following:

                          * 128-bit

                          * 40-bit

                          * none

 User password            Use the userpassword attribute to specify a password that users must enter to view the document.

 Owner password           Use the ownerpassword attribute to specify a password that users must enter to view and optionally modify
                          the document.


Additionally, the cfdocument tag supports the following Acrobat security permissions through the permissions
attribute. Specify one or more of the following values; separate multiple permissions with a comma:


 Permission               Description

 Printing                 Specify the AllowPrinting attribute to enable viewers to print the document.

 Modification             Specify the AllowModifyContents attribute to let viewers modify the document, assuming they have the
                          required software.

 Copy                     Specify the AllowCopy attribute to let viewers select and copy text from the document.

 Annotation               Specify AllowModifyAnnotations to let viewers add comments to the document. If users add annotations,
                          they must save the PDF after making changes.

 Screen readers           Specify AllowScreenReaders to enable access to the document through a screen reader.

 Fill in                  Specify AllowFillIn to enable users to use form fields.

 Assembly                 Specify AllowAssembly to enable users to create bookmarks and thumbnails, as well as insert, delete, and
                          rotate pages.

 Degraded printing        Specify AllowDegradedPrinting to enable lower-resolution printing. This format prints each page as a
                          bitmap, so printing can be slower.


Note: The defaults for these options vary, based on encryption level. These options apply to PDF only. For more
information, see the cfdocument discussion in the CFML Reference.

The following example creates a PDF document that allows copying only:

 <cfdocument format="PDF" encryption="40-bit"
       ownerpassword="us3rpa$$w0rd" userpassword="us3rpa$$w0rd"
       permissions="AllowCopy" >
<h1>Employee List</h1>
<cfquery name="EmpList" datasource="cfdocexamples">
       SELECT FirstName, LastName, Salary
       FROM Employee
</cfquery>
<cfoutput query="EmpList">
       #EmpList.FirstName#, #EmpList.LastName#, #LSCurrencyFormat(EmpList.Salary)#<br>
</cfoutput>
</cfdocument>


Saving printable reports in files
You can use the cfdocument filename attribute to save the generated PDF or SWF content to a file, as the following
example shows:



                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1014
Working with Documents, Charts, and Reports




<!--- The compasstravel database is part of the Getting Started
     tutorial application, found under the cfdocs directory. --->
<cfquery datasource="compasstravel" name="compasstrips">
SELECT tripName, tripDescription, tripLocation, price
FROM trips
ORDER BY price
</cfquery>
<cfdocument format="pdf"
           filename="#GetDirectoryFromPath(GetTemplatePath())#/compasstrips.pdf"
           overwrite="yes">
     <cfdocumentsection>
           <h1 align="center">Compass Travel</h1>
           <h2 align="center">Destination Guide</h2>
           <p align="center"><img src="cfdocs/getting_started/photos/somewhere.jpg"></p>
     </cfdocumentsection>
     <cfdocumentsection>
           <cfdocumentitem type="header">
                <font size="-3"> <i>Compass Travel Trip Descriptions</i></font>
           </cfdocumentitem>
           <cfdocumentitem type="footer">
                <font size="-3">
                    <cfoutput>Page #cfdocument.currentpagenumber#</cfoutput>
                </font>
           </cfdocumentitem>
           <cfoutput query="compasstrips">
                <hr>
                <h2>#tripName#</h2>
                <p><b>#tripLocation#</b></p>
                <p>Price: #DollarFormat(price)#</p>
                <p>#tripDescription#</p>
           </cfoutput>
     </cfdocumentsection>
</cfdocument>



Creating reports with Crystal Reports (Windows only)
When running on Windows, the cfreport tag also supports the execution of reports created using Crystal Reports
version 9 or 10.

Note: When you install Crystal Reports, select the Enable export to HTML and Enable export to Disk options. These
options are not enabled by default, so you must use the Custom Install option.

1 Create a report definition in Crystal Reports.

2 Create a CFM page and add a cfreport tag that invokes the Crystal Reports report definition. The following
   example shows the cfreport tag invoking a Crystal Reports report definition and passing a filter condition:

    <cfreport report = '/reports/monthlysales.rpt'>
         {Departments.Department} = 'International'
   </cfreport>

3 Open a browser and display the CFM page.

   ColdFusion uses COM to call Craxdrt9.dll for Crystal Reports version 9, and Craxdrt.dll for Crystal Reports version
   10. If you have problems with the cfreport tag, ensure that these DLLs are registered and, if not, use regsvr32 to
register them (the default location for these DLLs is C:\Program Files\Crystal Decisions\Report Designer Component\).

For complete information on defining reports in Crystal Reports, see the Crystal Reports documentation.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1015
Working with Documents, Charts, and Reports




Creating Reports with Report Builder

Improve your access to important business data by creating integrated business reports with Adobe ColdFusion
Report Builder and CFML.


About Report Builder
ColdFusion reporting adds integrated business reporting to ColdFusion, providing access to important business data.
ColdFusion reporting consists of server-side run-time processing and a graphical user interface (GUI), called the
Report Builder.

For information on installing the Report Builder, see "Getting started" on page 1015.

The Report Builder is a Windows-only tool that lets you build banded reports. A banded report consists of multiple
horizontal sections (bands), one band for each part of a printed report. For example, data and text in the report header
band prints at the beginning of the report, data and text in the page header band prints at the beginning of each page,
and data and text in the page footer band prints at the end of each page. In the middle of the report is the detail band,
which, at run time, contains one row for each row in the report's result set or database query.

   The Report Builder contains an extensive online Help system, including quick-start tutorial topics and context-
   sensitive dialog box Help. Press F1 to consult the online Help.


Report Builder and CFR files
The Report Builder is a stand-alone application that creates report definitions, interacting with a ColdFusion server,
as necessary. The Report Builder stores report definition information in a ColdFusion Report (CFR) file. This file
contains field definitions, formatting, database SQL statements, CFML, and other information. You display a CFR file
by using the cfreport tag and, if enabled for the report, display the report by running the CFR file in a browser.

Note: The Report Builder runs in the Windows platform only. However, the CFR files created by the Report Builder run
on all platforms that ColdFusion runs on and that have ColdFusion Reporting enabled.


RDS
Remote Development Services (RDS) is a proprietary protocol that uses HTTP to enable the Query Builder and Chart
Wizard to access database data through a ColdFusion data source. To enable this functionality in the Report Builder, you
define settings for an RDS server. RDS server is another name for an associated ColdFusion server that has enabled RDS.

For more information, see "Using CFML in reports" on page 1030.


Run time
At run time, you display the CFR file by using a ColdFusion server that has ColdFusion Reporting enabled. You can
display the CFR file directly or run it through the cfreport tag. Also, you can save the report to a file instead of
returning output to the browser. If the report requires input parameters or a passed query, use the cfreport tag. If
you pass a query attribute in the cfreport tag, it overrides any internal SQL statement in the report definition.


Getting started
For installation instructions, see Installing ColdFusion. When you install the Report Builder, it also registers Windows
DLLs that RDS uses. If these DLLs fail to register properly, the Report Builder generates errors at startup and when
using RDS.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1016
Working with Documents, Charts, and Reports




Setup Wizard
The first time you start the Report Builder, it runs the Setup Wizard. The Setup Wizard prompts you to define default
settings for an associated ColdFusion server. These settings include the following:

* Default unit of measurement: Inches, centimeters, or pixels.

* ColdFusion server. The RDS server that the Query Builder and Chart Wizard use to access database data (RDS must
   be enabled on this server). The Setup Wizard requires the following information:

   * Host name or IP address.

   * Web server port. Typically, the port is 80 if you are using a web server connector, 8500 if you are using the built-
      in web server in the server configuration, 8300 if you are using the built-in web server with the cfusion server in
      the multiserver configuration, or a J2EE-server-specific web server port number.

   * RDS password for the associated ColdFusion server.

* Directory path to the web root used by the associated ColdFusion server (for example, C:\Inetpub\wwwroot or
   C:\ColdFusion\wwwroot).

* URL for the web root used by the associated ColdFusion server (for example, http://localhost or
   http://localhost:8500).

After running the Setup Wizard, the Report Gallery dialog box appears. When you click the Using A Report Wizard
radio button, the Report Builder runs the Report Creation Wizard, which prompts you for information and
automatically generates a complete report definition.

For more information on the Report Creation Wizard, see the Report Builder online Help.


Configuring RDS
Configure one RDS server for each ColdFusion server for which you define reports. After you configure an RDS server,
you can use the Query Builder to access data sources that you defined in the associated ColdFusion server, and select
database columns for use as query fields in a report.


Add an RDS server
1 Open the Preferences dialog box by selecting Edit > Preferences from the menu bar.

2 Click Server Connection.

3 Click the plus sign (+) next to the pop-up menu in the upper-left corner of the dialog box.

4 In the Configure RDS Server dialog box, specify the following information, and then click OK:

   Description A name for the server connection. This name appears in the pop-up menu on the left side of the Query
   Builder.

   Host name The host on which ColdFusion runs. Type localhost or an IP address.

   Port Web server port number. Accept the default port (80) or enter the port number of the ColdFusion server's
   built-in web server (8500 is the default port number).

   Context Root (J2EE configuration only) The context root (if any) for the ColdFusion web application.

   Use Secure Sockets Layer (Optional) Enables SSL security.

   User Name Not applicable to ColdFusion RDS.

   Password RDS password. You set this password in the ColdFusion Administrator.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1017
Working with Documents, Charts, and Reports




      Do not confuse the RDS password with the ColdFusion Administrator password, which you also manage through
      the ColdFusion Administrator.

  Prompt for password Specifies whether to prompt for an RDS password each time you use the Query Builder. If
  you select this option, leave the User Name and Password fields blank.


Designate a default RDS server
1 Open the Preferences dialog box by selecting Edit > Preferences from the menu bar.

2 Click Server Connection.

3 Select an RDS server from the Preferred RDS Server pop-up menu, and click OK.

The Report Builder automatically connects to the specified server when you display the Query Builder or Chart
Wizard.


User interface usage, tips, and techniques
The Report Builder workspace includes the following areas:

* Toolbox: Contains nonvariable elements placed in a report, including text, shapes, images, subreports, and graphs.
  To use toolbox elements, click the element, and then drag in the report band to define the element's size. After you
  place an element on a report band, you can modify its appearance and behavior by using the Properties panel.

* Alignment panel: Use Control-click or Shift-click to select multiple elements in a report band, and then click the
  appropriate alignment icon. You can also use Control+A to select all elements in a report band.

* Report bands: Place toolbox elements, query fields, and calculated fields on report bands. The default report bands
  are report header, page header, column header, page footer, report footer, and watermark. Page header, page footer,
  and watermark are closed by default; to open them drag one of the adjacent splitter bars. To define additional bands
  for groups, select Report > Group Management.

ColdFusion provides three panels that you use to place and format data elements in the workspace:

* Properties panel: Contains display and report characteristics for the selected field. To display the Properties panel,
  choose Window > Properties Inspector from the main menu. To change a property value, type or select a new value,
  and press Enter. For complete information on properties, see the Report Builder online Help.

* Fields and parameters panel: Contains items for query fields, input parameters, and calculated fields. To display
  the Fields and parameters panel, choose Window > Fields and Parameters from the main menu. Use the add, edit,
  and delete icons to manage these fields. After you define a field, drag the field name to add the field, its associated
  label, or both, to a report band.

* Report styles panel: Contains the styles that you define for a report. To display the Report styles panel, choose
  Window > Report Styles from the main menu. Use the add, edit, and delete icons to manage report styles. After you
  define styles, you apply them to elements on the report instead of specifying font, font size, and so on, for each
  individual element. If your report layout, platform, or font availability requirements change, you can modify the
  style to apply the changes throughout the report. Additionally, you can specify a style as the default for the report:
  if no other style is applied to an element in the report, Report Builder applies the default style to that element.

The View menu lets you control whether toolboxes and panel windows appear. Also, you can click a window's title to
undock it and drag it to another area of the screen. For example, you can drag all three panels and dock them in the
same window. Report Builder lets you switch between them by clicking the tabs at the top of the window. To redock a
tool window or panel, drag it to the side or corner until a rectangle appears, and then release the mouse button.

For more information, see "Common reporting tasks and techniques" on page 1019 and the online Help.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1018
Working with Documents, Charts, and Reports




Report definition guidelines
To ensure a successful report, plan the following before defining it in the ColdFusion Report Builder:

* Report design issues:

   Audience Why are you creating this report? Who is the audience?

   Data What data must be in the report? Where does it come from? Whether you use the Query Builder or pass a
   query to the report, plan the data in advance.

   Grouping Are groups required? If so, ensure that the result set is returned in the correct order, and you define a
   group based on the sort column.

   Calculated fields Are there fields that must be totaled or calculated? For column totals, use calculated fields. For
   calculated totals on individual rows, use SQL. For more information, see "Common reporting tasks and
   techniques" on page 1019.

   Input parameters Does the report require variable input? If so, define an input parameter and pass values to the
   report at run time by using the cfreportparam tag. For more information, see "Common reporting tasks and
   techniques" on page 1019.

* Data retrieval strategy:

   Query Builder and basic SQL Use this option when your report has standard selection criteria (such as a WHERE
   clause with sorting and a fixed set of selection criteria) and when you have to develop a report quickly. This method
   also lets you specify cfquery options, such as caching.

   Query Builder and advanced query mode Use this option when you use a ColdFusion query encapsulated in the
   report definition. This option is also useful if the query comes from the cfdirectory, cfldap, or cfpop tags; query
   of queries; or is dynamically constructed with the QueryNew function.

   The cfreport tag and a passed query Use this option when you require more control over the result set used in the
   report. For example, use this option if your application has a form that your clients use to construct dynamic
   selection criteria.

* Related visual information:

   Charts For more information, see "Using charts" on page 1033.

   Subreports For more information, see "Using subreports" on page 1033.


Managing fonts with printable reports
Ideally, reports achieve a consistent look across all client platforms and all browsers. ColdFusion handles this
consistency automatically for graphics and images, using the size specifications in the report definition. However,
potential differences in font availability across browsers, browser versions, languages, and platforms can affect the font
display for your report. A variety of factors ensure a consistent report display.


Embedded fonts
You can ensure consistent report display by embedding fonts. However, reports with embedded fonts have a larger file size.


Output format
The FlashPaper and PDF output formats handle embedded fonts differently.

FlashPaper FlashPaper always embeds fonts, which ensures that reports always display appropriately.

PDF PDF reports can optionally embed fonts, however, if your report doesn't use embedded fonts, ensure that the
fonts are available on the client computers.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          1019
Working with Documents, Charts, and Reports




Font availability on the server computer and the client computer
ColdFusion has different requirements for rendering the fonts in a report, depending on where the fonts are located.

Server computer For all formats, the fonts used in a report must reside on the computer that runs ColdFusion.
ColdFusion requires these fonts to render the report accurately. ColdFusion automatically locates Acrobat built-in
fonts and fonts stored in typical font locations (such as the Windows\fonts directory). However, if your server has
additional fonts installed in nonstandard locations, register them with the ColdFusion Administrator so that the
cfdocument and cfreport tags can locate and render PDF and FlashPaper reports.

Client computer If your PDF report does not embed fonts, the fonts reside on the client computer to ensure consistent
report display.


Mapping logical fonts to physical fonts
If you are using Java logical fonts, such as serif, sans serif, or monospaced, ColdFusion maps these fonts to physical
fonts by using specifications in the cf_root/lib/cffont.properties file. (On the multiserver or J2EE configuration, this file
is in the cf_webapp_root/WEB-INF/cfusion/lib directory). You can modify these mappings, if necessary. Also, if you
are using an operating system whose locale is not English, you can create a locale-specific mapping file by appending
.java-locale-code to the filename. If ColdFusion detects that it is running on a non-English locale, it first checks for a
cffont.properties.java-locale-code file. For example, on a computer that uses the Chinese locale, name the file
cffont.properties.cn. For more information on Java locale codes, see the Sun website.

    The ColdFusion install includes a cffont.properties.ja file for the Japanese locale.


This discussion applies to both the cfdocument and cfreport tags. For more information, see the Report Builder
online Help.


Common reporting tasks and techniques
With Report Builder, you can include data in reports in a variety of formats, and perform calculations on the
information. For more information, including troubleshooting tips, see Report Builder online Help.


Grouping and group breaks
You can add clarity to a report's organization by grouping the information. You can define separate headings for each
new group and also display group-specific summary information, such as subtotals at the end of each group's area of
the report. For example, you could create a report that displays departments, employees, and their salaries. Grouping
the data by department lets users quickly understand department salary characteristics. When the department ID
changes, the ColdFusion Report Builder triggers a group break. The group break completes the old group by displaying
the group footer and starts the new group by displaying the group header.

The ColdFusion Report Builder does not group data itself. Ensure that the SQL used to retrieve the result set is already
grouped in the appropriate order; typically you implement grouping by specifying an ORDER BY clause in the SQL
SELECT statement used for the report. For example, you could use the following SQL SELECT statement:

 SELECT EmployeeID, LastName, FirstName, Title, City, Region, Country
FROM Employees
ORDER BY Country, City

For this example, you can define two groups: one that corresponds to Country, and a second group that corresponds
to City. When you define more than one group, the Group Management dialog box appears with Up Arrow and Down
Arrow keys, which you can use to control group hierarchy. For example, country should precede city, because
countries contain cities.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1020
Working with Documents, Charts, and Reports




Define a group
1 Select Report > Group Management from the menu bar.

2 Click Add.

3 Specify a group name in the Name field.

4 Specify the value that controls grouping (also called a group expression) in the Group on field. At run time,
  ColdFusion triggers a group break when the result of this value changes. These values are often query field names.
  However, this value can also be a calculated field or other type of expression. Sample group expressions include the
  following:

  Query field Creates a group break when the associated column in the result set contains a different value. The field
  that you specify must be one of the sort criteria for the result set; for example, query.country.

  Calculated field Creates a group break when a calculated field returns a different value. For example, if the
  expression calc.FirstLetter returns the first letter of a query column, you can group a report in alphabetical order.

  Boolean expression Creates a group break when a Boolean expression returns a different value. For example, if
  your result set is sorted by the passpercentage column, you could use the Boolean expression query.passpercentage
  LT 50.

5 Specify group break options:

  Start New Column Forces a new column on a group break.

  Start New Page Forces a new page on a group break.

  Reset Page Number Resets the page number to 1 on a group break.

6 Specify band size and printing information:

  Min. height for group The minimum height that must remain on a page for ColdFusion to print the group band on
  that page.

  Reprint Header on Each Page Displays the group header on each page.

7 Click OK.

  The Report Builder adds the group to the report and creates header and footer bands for the group.

8 Click OK again.

9 Add headings, text, query fields, calculated fields, and other information to the group's header and footer.


Create group subtotals
1 Create a calculated field to contain the group subtotal. Create the calculated field that uses the following criteria:

  * Specify a numeric data type.

  * Select Sum in the Calculation field.

  * Specify the field to sum on in the Perform Calculation On field. For example, a report on employees by
     department could sum on query.emp_salary.

  * Specify to reset the field when the group changes.

2 Place the calculated field on the report.

For more information on calculated fields, see the Report Builder online Help.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1021
Working with Documents, Charts, and Reports




Defining, modifying, and using fields and input parameters
The Report Builder supports variable data through query fields, input parameters, and calculated fields, as follows:

Query field Maps to columns in the database result set associated with the report. You define one query field for each
column in the associated database query.

Calculated field Analyzes or sums multiple detail rows in a report. ColdFusion dynamically generates calculated field
values at report-generation time, optionally recalculating the value with each new report, page, column, or group.

Input parameter Specifies data fields that you pass to the report at run time through the cfreportparam tag or from
a main report to a subreport. You can place input parameters directly on a report band or you can use them as input
to a calculated field.


Define a query field
1 Choose Window > Fields and Parameters.

2 Click Query Fields.

3 Click the plus sign (+) at the upper edge of the tab.

4 Type a value for the name field. This value must match a column name in the corresponding cfquery statement and
   cannot contain a period.

5 Type a default label.

6 Specify the data type of the corresponding database column, as follows:


    Object                                  Time                                    Long

    Boolean                                 Double                                  Short

    Byte                                    Float                                   Big Decimal

    Date                                    Integer                                 String

    Time Stamp                              BLOB                                    CLOB


7 Click OK.

Note: The Query Builder defines query fields automatically for all database columns in the result set (this action does not
apply to the Advanced Query Builder). Also, if you run the Query Builder as part of the Report Creation Wizard, the
wizard places query fields on the report.


Define a calculated field
1 Choose Window > Fields and Parameters.

2 Click Calculated Fields.

3 Click the plus sign (+) at the upper edge of the tab.

4 Specify a name, default label text, and data type. Data type options are the same as for query fields.

5 Specify calculation options:

   Calculation Specifies the type of calculation that ColdFusion performs. Valid values are: Average, Count,
   DistinctCount, First, Highest, Lowest, Nothing, Standard Deviation, Sum, System, and Variance. If you specify
   Nothing, you typically use the Perform Calculation On field to specify a dynamic expression. Except for Nothing
   (for which you use the Perform Calculation On field) and System (for which you write a customized scriptlet class),
   you use these calculations for group, page, and report totals.

   Perform Calculation On Specifies a field or expression. Click the ... button to display the Expression Builder.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1022
Working with Documents, Charts, and Reports




   Initial Value Specifies an initial value for the calculated field.

6 Specify the following reset options, and click OK:

   Reset Field When Specifies when to reset the calculated field value. Valid values are: None, Report, Page, Column
   Group.

   Reset Group If Reset Field When is set to Group, use this field to specify the group whose group break triggers the
   reset.

   For additional information on calculated fields, see the Report Builder online Help.


Define an input parameter
1 Choose Window > Fields and Parameters.

2 In the Fields and Parameters panel, click Input Parameters.

3 Click the plus sign (+) at the upper edge of the tab.

4 In the Add Input Parameter dialog box, enter a value for the name field. This value must match an input parameter,
   such as the name attribute of a cfreportparam tag included in the cfreport tag that uses the report definition.

5 Enter the default label text.

6 Specify a data type and default value, and click OK. Data type options are the same as for query fields.

For more information on using input parameters, see "Using input parameters to pass variables and other data at run
time" on page 1029 and "Using subreports" on page 1033.


Place a query field, calculated field, or input parameter on a report band
1 In the Fields and Parameters panel, use the radio buttons to specify whether to place the label, the field, or both.

2 Drag the query field, calculated field, or input parameter from the Fields and Parameters tab to the appropriate
   report band.

3 Drag the query field, calculated field, or input parameter to the desired band.

4 (Optional) Use the Properties panel to customize the field display.

For example, you could have a query field named query.emp_salary and a calculated field that sums query.emp_salary,
resetting it with each group. Place query.emp_salary in the detail band, and the associated calculated field in the group
footer band.


Using toolbox elements on report bands
You use the toolbox to add graphic and textual elements, such as images, circles, squares, lines, dynamic fields, charts,
and subreports, to report bands.

The basic technique for adding toolbox elements is to click in the toolbox element and then drag to define an area in
the appropriate report band. For some toolbox elements, such as image and text box, a dialog box immediately appears,
prompting for more information. For all toolbox elements, you customize the appearance of the element by using the
Properties sheet.

   You can add toolbox elements from the Insert menu.


For information on charts, see "Using charts" on page 1033. For information on subreports, see "Using subreports" on
page 1033.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                              1023
Working with Documents, Charts, and Reports




Create a text box
1 Click the Label icon (abc) in the toolbox.

2 Define the area for the label by dragging on the desired band.

3 Enter the label text in the Edit Label Text dialog box. To add a line break, press Control+Enter.

4 Click OK, or press Enter.

Note: ColdFusion trims leading and trailing blanks from labels. To include leading and trailing blanks, define a dynamic
field and include the blanks in the expression, for example, " My Title ".


Import image files
1 Click the Image icon in the toolbox.

2 Define the area for the image by dragging on the desired band.

3 In the Image File Name dialog box, navigate to the file that contains the image, select the file, and click OK.


Use a database BLOB column as an image source
1 Click the image icon in the toolbox (the icon has a tree on it).

2 Define the area for the image by dragging on the desired band.

    The Image File Name dialog box appears.

    You can also drag the BLOB field from the Fields and Parameters tab to a report band.


1 Click Cancel.

    The Expression Builder appears.

2 Click the Image Type pop-up menu and change File/URL to BLOB.

3 Select the query field or input parameter that contains the BLOB column.

    Note: The BLOB column must contain a binary image in GIF, JPEG, or PNG format.

4 Click OK.

Note: These instructions assume that the contents of the BLOB column can be rendered as an image.


Add rectangles, ellipses, and lines
1 Click the rectangle, ellipses, or line icon in the toolbox.

2 Define the area or line by dragging on the desired band.

3 Resize the selected element by dragging the handles that surround it.

    Pressing the Control key while resizing a rectangle, ellipsis, or line, constrains the element to a square, circle, or angles
    that are multiples of 45 degrees.


Add dynamic fields
1 Click the Field icon in the toolbox.

2 Define the area for the dynamic field by dragging on the desired band.

    The Add Field dialog box appears (if you haven't defined any query fields, the Expression Builder appears).

3 Select the field to add. Selecting a query field, calculated field, or input parameter is the same as dragging from the
    Fields and Parameters tab.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1024
Working with Documents, Charts, and Reports




4 (Optional) Select Manually Entered Expression.

   The Expression Builder appears. This option is useful for calculations that use variables in the same row. For
   example, to compute total price for an order detail line item, you could use the following expression:

    LSNumberFormat((query.unitprice * query.quantity), ",_.__")

5 Click OK.


Aligning elements
Organized element layout is essential to a visually pleasing report. You achieve this organization by aligning, spacing,
and centering visual elements on each band relative to each other, to the band itself, and to elements on other bands.

The Report Builder Align Palette includes the following options:

* Align left, center, and right

* Align top, horizontal, and bottom

* Same heights, widths, and both

* Space equally horizontally

* Space equally vertically

You align, size, and space multiple report elements, as follows:

Relative to the band they are in: You control relative alignment through the Align to Band icon, which is the bottom
icon in the Align Palette. When it is enabled, the Align to Band icon has a rectangle surrounding it, and the Report
Builder aligns and spaces one or more elements relative to the height and width of the band.

Relative to each other: When Align to Band is disabled, Report Builder aligns and spaces two or more elements
relative to each other.


Use the Align Palette
1 Select two or more elements by pressing Control-click, Shift-click, or using lasso select.

2 Click the alignment icon, or select Modify > Alignment > alignment option from the menu bar.

   The Align Palette options are also available from Modify > Alignment on the menu bar.


For complete information on fine-tuning element display, see the Report Builder online Help.


Using report styles
A report style is like a font style in Microsoft Word. Instead of explicitly associating an element with formatting
specifications, you associate the element with a style. doing so provides you with report-wide control of the formatting
characteristics of your report.

Additionally, you can specify style that is the default for the report. The ColdFusion Report Builder uses the default
style for all fields for which you have applied no other font specifications or styles. The default style, if defined, is
displayed in bold in the Report Styles panel.

Report Builder also lets you import styles from a Cascading Style Sheet (CSS) file and export styles defined in Report
Builder to a CSS file. This way you can enforce standard formatting across reports and override styles at run time from
a CFM page. For more information, see "Using Cascading Style Sheets" on page 1044 and the CFML Reference.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1025
Working with Documents, Charts, and Reports




Note: When choosing fonts for your report, ensure that the fonts are available on the server that runs ColdFusion and (if
you don't embed fonts) on the client computer. For more information on fonts, see "Creating a simple report" on
page 1036.


Define a style
1 Choose Window > Report Styles.

2 Click the (+) icon at the upper edge of the Report Styles tab.

3 Type a value for the Name field. Style names must be unique.

4 Add other style characteristics, and click OK.


Specify a style as the default
1 Edit a text style or create one.

2 Select the option with this label: This option is the default style if no other style is selected for an object.

3 Add or modify other text style characteristics, and click OK.


Apply a style to a report element
1 Select the element in the report band.

2 Choose Window > Properties Inspector.

3 Choose the style from the Style pop-up menu.

For more information, see the Report Builder online Help.


Previewing reports
Report building is an iterative process and most developers periodically display the in-progress report to review their
most recent changes. If your report uses an internal query and you established default web root settings, preview
functionality is enabled automatically. If your report uses a passed query, define an associated CFM page and associate
that page with the report. The Report Builder runs this page when you request Report Preview.


Preview a report that uses an internal query
1 (Optional) Define default server connection information using the Preferences dialog box, if you did not define
  these settings previously:

   * Default RDS server configuration (used for Query Builder and Chart Wizard only; not required for report
      preview).

   * Fully qualified path for the local web root directory; for example, C:\ColdFusion\wwwroot or
      C:\Inetpub\wwwroot.

   * URL for the local web root, for example, http://localhost:8500 or http://localhost.

2 (Optional) Specify the output format in the Report Properties dialog box (the default format is FlashPaper).

3 (Optional) If a CFM page runs, specify the URL of the CFM page in the Report Properties dialog box.

4 Save your report.

5 Select File > Preview from the menu bar to display the report.

  Note: If the Report Builder displays the Edit Preview Report URL dialog box instead of displaying the Preview window,
  select Edit > Preferences from the menu bar and insure that the web root file and URL settings are correct on the Server
  Connection pane.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1026
Working with Documents, Charts, and Reports




6 Close the preview window by pressing F12.

If your report is designed to accept a query object from a cfreport tag, associate a URL with the report. If necessary,
the Report Builder prompts for this URL when you preview the report. Otherwise, you can open the Report Properties
dialog box, and specify the URL of the CFM page in the Report Preview URL field.

    You can use the cfreport tag to run a report, regardless of whether the report has an internal query or is passed a
    query.


Preview with an associated CFM file
1 Select Report > Report Properties from the menu bar.

2 Specify the URL of the associated CFM page in the Report Preview URL field. This CFM page must contain a
   cfreport tag whose template attribute specifies the current CFR file and, if necessary, passes a query in the query
   attribute.

3 Save your report.

4 Press F12. Depending on the output format that you have chosen, the Preview Report window displays your report
   in PDF, FlashPaper, RTF, XML, HTML, or Excel format.


Displaying page numbers
The Report Builder includes a built-in calculated field named PAGE_NUMBER, which displays the current page number
when you place it on a report band.


Add a built-in calculated field
1 Click the Field tool in the toolbox.

2 Drag in the center of the header or footer band to define the size of the page number field.

   The Add Field dialog box appears, listing all fields defined for the report, including built-in calculated fields and
   input parameters.

3 Select calc.PAGE_NUMBER, and click OK.

    You can use the Field tool to add any type of field (query field, calculated field, input parameter) to a report.


For information on the other built-in calculated fields, see the Report Builder online Help.


Using layered controls
Layered controls are elements that you place at the same location of a report band, and then use PrintWhen
expressions to conditionally display one or the other at run time. You can use layered elements to customize the
circumstances under which the elements display and enhance a report's ability to communicate important
information.


Place an element directly over another element
1 Place the elements on the band.

2 Choose Window > Properties to display the Properties panel.

3 Specify a PrintWhen expression, display properties, and placement properties for each element using the Properties
   panel, as follows:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1027
Working with Documents, Charts, and Reports




4 Specify a PrintWhen expression for each element. For example, you could specify the following expression to
   display one element when shippeddate is later than requireddate (that is, late) and another element when
   shippeddate is earlier than requireddate:

   First element query.shippeddate LTE query.requireddate

   Second element query.shippeddate GT query.requireddate

5 Specify different display characteristics for each element. For example, if an order is late, display it in red text.

6 Set the Top, Left, Height, and Width properties to the same values for each element.

   When you specify identical placement properties, you access the individual elements through the Layered Controls
   menu.


Use the Layered Controls menu
1 Right-click on the top element.

2 Select Layered Controls > elementname from the pop-up menu. The Report Builder identifies each layered element
   by displaying its PrintWhen expression.

3 Select the element and choose Window > Properties Inspector to view the element properties.


Using links
You can include hypertext links from query fields, calculated fields, input parameters, charts, and images to a variety
of destinations:

* An anchor or page within the same report

* An anchor or page within another report

* An HTML page, optionally specifying an anchor and URL parameters

One use for links is to create drill-down reports, in which you click an item to display detailed information. For
example, clicking an employee line item passes the employee ID as a parameter to a page that displays complete
information for the employee.

For complete usage information on creating anchors and hypertext links, see the Report Builder online Help.


Defining properties for report elements
A set of properties defines every element on a report, including the report itself. These properties affect the look, feel,
and behavior of each element.

For many properties, the Report Builder lets you define their values through user interface elements, such as dialog
boxes, toolbar icons, and menu items. For example, you set a text label's font size using a toolbar icon. You can set
values for all properties, however, through the Properties panel, which display all properties for the currently selected
element.

   Sometimes a report contains multiple, closely spaced elements and it is difficult to select an individual element using
   the mouse. In this case, selecting the element from the Properties panel pop-up menu is an easy way to select an
element.

The Properties panel has two views:

Sort alphabetically All properties for the currently selected element display in alphabetical order.

Sort into groups The Properties panel displays related properties in the following predefined groups:

* Advanced



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1028
Working with Documents, Charts, and Reports




* Columns

* Page Layout

* Printing

* Colors and Style

* Data

* Font

* Font Style

* Formatting

* Hyperlinks

* Layout

* Print Control

The Report Builder displays only groups that relate to the currently selected element.


Set or modify a property for an element in the workspace
1 Select the element.

2 (Optional) If the Properties panel is not already displayed, choose Window > Properties Inspector.

   The Report Builder displays its properties in the Properties panel.

3 Modify the property. Depending on the property, you enter a value, select a value from a pop-up menu, or open the
   Expression Builder to use an expression.

4 Press Enter.

   When you select a color, double-click the color.



Choose a different element
Select the element from the pop-up menu. When you select a new element, the Report Builder selects the element and
displays its properties.

   Although the Properties panel is a powerful way to set properties, you typically set properties through dialog boxes and
   toolbar icons. For example, you use the Report Properties dialog box to set report-wide settings. For complete
information on setting properties, see "Property reference" in the Report Builder online Help.


Displaying reports
Your application can run a report by displaying the CFR file in a browser or by displaying a CFM page whose cfreport
tag runs the report.

You can optionally use the cfreport tag to save the report to a file.

The cfreport tag supports advanced PDF encryption options. For more information, see cfreport in the CFML
Reference.

For information on report preview, see "Previewing reports" on page 1025.


Display a report by using the cfreport tag
1 Create a report, with or without an internal query.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         1029
Working with Documents, Charts, and Reports




2 Create a CFM page and add a cfreport tag that runs the report. If the report does not use an internal query, also
  populate a query and pass it using the query attribute. If the report uses an internal query and you use the query
  attribute, the passed query overrides the internal query.

   <cfquery name="northwindemployees" datasource="localnorthwind">
        SELECT EmployeeID, LastName, FirstName, Title, City, Region, Country
        FROM Employees
        ORDER BY Country, City
   </cfquery>


   <CFREPORT format="PDF" template="EmpReport.cfr"
        query="#northwindemployees#"/>

  Note: ColdFusion does not render text that occurs before or after the cfreport tag.

3 Open a browser and display the CFM page.

  ColdFusion generates the report.

   If you display an HTML report, ColdFusion generates temporary files for images in the report. You can specify how
   long the temporary files are saved on the server by using the resourceTimespan attribute of the cfreport tag. For
more information, see the CFML Reference.


Display a CFR file in a browser
1 Create a report that uses an internal query and does not use input parameters.

2 Open a browser and display the CFR file.


Save a report to a file
1 Create a report, with or without an internal query.

2 Create a CFM page and add a cfreport tag that runs the report. Optionally pass a query attribute, as described in
  the previous procedure. Include a filename attribute that specifies the fully qualified name of the file being created,
  as the following example shows:

   <CFREPORT format="PDF" template="emppicture.cfr"
        filename="#GetDirectoryFromPath(GetTemplatePath())#/emppicture.pdf"
        overwrite="yes"/>

      If you write the report output to an HTML file, ColdFusion creates a directory located relative to the HTML file,
      generates files for the images (including charts) in the report, and stores the image files in the directory. For more
  information, see "Exporting the report in HTML" on page 1047.

      Use the .pdf extension for PDF output format, the .swf extension for FlashPaper output format, .xml extension for
      an XML file, .rtf extension for an RTF file, .html extension for HTML files, and the .xls extension for Excel format.

3 Open a browser and display the CFM page. ColdFusion generates the report, saves the file, and displays an empty
  page in the browser.


Disable browser display of the CFR file
1 Open the Report Properties dialog box by selecting Report > Report Properties from the menu bar.


2 Clear the Allow Direct .CFR Browser Invocation option, and click OK.


Using input parameters to pass variables and other data at run time
Input parameters are data fields that you pass to the report at run time. You can place input parameters directly on a
report band or you can use them as input to a calculated field.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1030
Working with Documents, Charts, and Reports




Define input parameters in the same manner as query fields. You can specify a default value that ColdFusion uses when
no corresponding parameter exists. For more information on defining input parameters, see "Defining, modifying,
and using fields and input parameters" on page 1021.

You use input parameters in the following ways:

* Through the cfreportparam tag: Input parameters must correspond, by name, to cfreportparam tags embedded
   in the CFM page invocation. For example, if you define an input parameter named ReportTime, you pass a
   cfreportparam tag with a name attribute set to ReportTime, as the following example shows:

    <cfreport format="PDF" template="FourthReport.cfr" query="#coursedept#">
         <cfreportparam name="ReportTime" value="#DateFormat(Now())#, #TimeFormat(Now())#">
   </cfreport>

* Subreport parameters: When a subreport requires information from a main report, you define subreport
   parameters in the main report and corresponding input parameters in the subreport. For more information, see
   "Using subreports" on page 1033.

For information on dynamically populating input parameters at run time, see "Advanced query mode" on page 1030.


Using CFML in reports
CFML is the scripting language for the Report Builder. By leveraging CFML, you can create reports that select and
format data to meet your needs. You use CFML in the following areas of the Report Builder:

* Advanced query mode

* Report functions

* Expressions


Advanced query mode
In some cases, you create a complex query, reuse an existing query, or encapsulate additional CFML processing as part
of query creation for the report. To use a query in these ways, you use advanced query mode to create CFML that
returns a query. When you click the Advanced button at the top of the Query Builder, the Report Builder displays a
text entry area in which you can enter CFML that generates a query. ColdFusion executes this tag at report execution
time and passes the query result set to the report.

Note: When you use advanced query mode, the Query Builder does not create query fields automatically. create the
associated query fields manually.

The CFML used in advanced query mode must include a query object whose name matches that in the Variable that
contains the query object field. You can use any CFML tag that returns a query object or the QueryNew function. The
CFML can use multiple query objects, but can only return one.

Note: If you set an empty variable (for example, <cfset name=" ">), the Report Builder throws a Report data binding
error.

This example CFML uses the cfhttp tag to retrieve a query:

<cfhttp
url="http://quote.yahoo.com/download/quotes.csv?Symbols=csco,jnpr&format=sc1l1&ext=.csv"
method="GET"
name="qStockItems"
columns="Symbol,Change,LastTradedPrice"
textqualifier=""""
delimiter=","
firstrowasheaders="no">




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1031
Working with Documents, Charts, and Reports




Another possible use of advanced query mode is to test for passed parameters in the URL or FORM scopes and use
those parameters to retrieve data, as the following example shows:

<!--- First look for URL param. URL overrides cfreportparam. --->
<cfif isDefined("url.deptidin")>
     <cfset param.deptidin = url.deptidin>
</cfif>


<!-- Then look for FORM param. Overrides URL param. --->
<cfif isDefined("form.deptidin")>
     <cfset param.deptidin = form.deptidin>
</cfif>


<cfquery name="CFReportDataQuery" datasource="cfdocexamples">
SELECTLastName, FirstName, Dept_ID
FROMEmployee
WHERE (Dept_ID = #param.deptidin#)
</cfquery>


Using report functions
Report functions are user-defined CFML functions that you code using the Report Function Editor and run in report
fields. You can use them to format data (such as concatenating and formatting all the field that make up an address),
to retrieve data, and for many other purposes.

Three built-in functions are unique to Report Builder: InitializeReport, BeforeExport, and FinalizeReport.
For more information, see the Report Builder online Help.


Report Builder built-in functions
1 Select Report > Report Functions from the menu bar.

    The Report Function Editor displays.

2 Click the Add Default Functions icon (the first on the left).

    The built-in functions are added to the left pane.

3 Select a function from the left pane.

    Commented code associated with the function appears in the right pane.

4 Modify the code and click OK.


Create a report function
1 Select Report > Report Functions from the menu bar.

    The Report Function Editor displays.

2 Click the plus sign to add a new report function.

    The Add Report Function dialog box displays.

3 Specify a name and click OK.

4 The Report Function Editor places a cfreturn tag in the text entry area.

5 Code the function, and click OK. The function is a ColdFusion user-defined function so all UDF rules and features
    are available for use. The following example shows a report function that concatenates address fields:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1032
Working with Documents, Charts, and Reports




   <cfargument name="Name" required="yes"/>
   <cfargument name="Address1" required="yes"/>
   <cfargument name="Address2" required="yes"/>
   <cfargument name="City" required="yes"/>
   <cfargument name="State" required="yes"/>
   <cfargument name="Zip" required="yes"/>


   <cfset variables.CRLF = Chr(13) & Chr(10)>
   <cfset variables.ResultVar="">


   <cfif Trim(arguments.Name) NEQ "">
        <cfset variables.ResultVar='#arguments.Name#'>
   </cfif>
   <cfif Trim(arguments.Address1) NEQ "">
        <cfif variables.ResultVar NEQ "">
             <cfset variables.ResultVar='#variables.ResultVar & variables.CRLF#'>
        </cfif>
        <cfset variables.ResultVar='#variables.ResultVar & arguments.Address1#'>
   </cfif>
   <cfif Trim(arguments.Address2) NEQ "">
        <cfif variables.ResultVar NEQ "">
             <cfset variables.ResultVar='#variables.ResultVar & variables.CRLF#'>
        </cfif>
        <cfset variables.ResultVar='#variables.ResultVar & arguments.Address2#'>
   </cfif>
   <cfif variables.ResultVar NEQ "">
        <cfset variables.ResultVar='#variables.ResultVar & variables.CRLF#'>
   </cfif>


   <cfset variables.ResultVar='#variables.ResultVar & arguments.City & ", " & arguments.State
  & " " & arguments.Zip#'>


   <cfreturn variables.ResultVar>


Use a report function
1 Place a dynamic field on the appropriate report band.

  The Add Field dialog box displays.

2 Specify Manually Entered Expression, and click OK.

  The Expression Builder displays.

3 Specify "report.functionname", and click OK.


Using expressions
Many elements of the Report Builder (including query fields, calculated fields, input parameters, images, and report
object attributes) are single operand ColdFusion expressions. Because these elements are expressions, you can
manipulate them with CFML functions.

The Expression Builder is a graphical interface that lets you quickly apply CFML functions to Report Builder elements.
Uses for the Expression Builder include the following:

* Many of the report object attributes (such as PrintWhen) accept expressions, which you can associate with query
  parameters, input parameters, or ColdFusion page variables. You can tie report attributes and columns to display
  based on run-time data or user preference.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1033
Working with Documents, Charts, and Reports




* Concatenating fields

* Formatting fields

* Calculated fields

* Accessing and displaying ColdFusion page variables and scopes

For information on using the Expression Builder, see Report Builder online Help.

For more information on expressions, see "Using Expressions and Number Signs" on page 64.


Using charts
Charts can help clarify large or complex data sets. The Report Builder lets you place a chart in any report band and
supports many types of charts.

To add a chart to a report, you use the Chart Wizard, which steps you through the chart building process. The Chart
Wizard, which is fully integrated with the Query Wizard to facilitate database-driven charts, helps you define the chart
type, the data used for the report and other formatting options.

   As you use the Chart Wizard to choose and define the various aspects of a given chart, the Report Builder uses RDS
   to generate chart images in real time. However, the data in these chart images is not real.

The Chart Wizard includes the following panels:

* Chart Types: Select the chart type (for example, bar) and subtype (for example, 3D-stacked).

* Chart Series: Select the data for the series. When you add a series, the Report Builder lets you hard-code series data
  or open the Query Builder to populate the series using a database query.

* Chart Formatting: Specifies title and series, general appearance, 3D appearance, lines and markers, and font.

   The data you specify through the Chart Wizard corresponds to the attributes specified in the cfchart, cfchartseries, and
   cfchartdata tags. For more information on these tags, see the CFML Reference.

For complete information on ColdFusion charting capabilities, see "Creating Charts and Graphs" on page 982. For
more information on charting using the Report Builder, see Report Builder online Help.


Using subreports
Subreports let you nest a report within your report. The data that you display in a subreport is typically related to the
data in the main report. You enable this display by passing one or more subreport parameters to the subreport.
However, the data displayed in a subreport can also be unrelated to the data in the main report.

Reasons to use subreports including the following:

* You prefer to avoid complex SQL, such as a RIGHT OUTER JOIN.

* Your report requires data from multiple databases.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1034
Working with Documents, Charts, and Reports




The following example shows the use of subreport parameters and the relationship between a report and a subreport:

             mainreport.cfr

            Subreport:
                subreport.cfr
            Subreport expression :
                custid = #query.CustomerID




              subreport.cfr

                 param.custid
                                           SELECT CustomerID, CompanyName,
                                              ContactName
                                           FROM Customers
                                           WHERE    (CustomerID = '#param.custid#')



Note: Although the Report Builder supports multiple levels of nesting, it displays one level of nesting only.

For additional information on subreports, see the Report Builder online Help.


Defining a subreport
You can define a subreport and include it in a report, or you can define it as part of inserting the subreport in the main
report.

A subreport has the following characteristics:

* Data displayed in the detail band only. A subreport uses no header or footer bands.

* If the subreport is related to the main report, it must include an internal query that uses a SELECT statement with
   a WHERE clause specifying the name of the input parameter used in the main report's Subreport Expression
   property.

If you have already defined a subreport, you add it to the main report and define subreport parameters, as necessary.


Add an existing subreport
1 Define or open your main report.

2 Click the Subreport icon in the toolbox.

3 Drag an area for the subreport in the desired report band.

4 Select From An Existing Report, specify the subreport, and click Next.

5 Select the fields in the main report that correspond to fields in the subreport and click Next.

6 Click Finish.

   The Report Builder adds the subreport to the main report, saving the report to subreport mappings as subreport
   parameters.

7 To modify subreport parameter settings, select the subreport and click Subreport Parameters in the Properties
   panel.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1035
Working with Documents, Charts, and Reports




If you are certain about the data required for a subreport, you can define a new subreport while adding it to the main
report.


Add a new subreport
1 Define or open your main report.

2 Click the Subreport icon in the toolbox.

3 Drag an area for the subreport in the report band.

4 Select As A New Report and click Next.

5 Click Query Builder.

6 Select the tables and columns for the subreport.

7 Specify a WHERE clause for the report by using the Condition and Criteria columns for the key columns.

   Specify a WHERE for Condition and either ='#CFVariable#' (string column) or =#CFVariable# (numeric
   column) for Criteria, and then overtype CFVariable with the name of the input parameter for the subreport (you
   define the input parameter name later in the procedure.)

8 Click Save, and then click Next.

9 Specify grouping fields, if appropriate for your subreport, and click Next.

10 Specify Free Form or Grid, and click Next.

11 Specify Only Detail Band, and click Next.

12 Specify a color scheme, and click Next.

13 Specify headings, as appropriate, and click Next.

14 For each parameter required by the subreport, specify the following:

    * Parameter name.

    * Associated value from the main report (select from the pop-up menu).

    * Data type.

15 Click Next.

16 Specify a fully qualified filename for the subreport, and then click Next.

17 Click Finish.

   Report Builder adds the subreport to the main report. Report Builder lets you change subreport name and modify
   subreport parameters in a main report.


Modify subreport settings
1 Click the subreport element in the main report.

2 To change the subreport, modify Subreport Expression.

3 To modify subreport parameters:

   a Click the Subreport Parameters property.

   b Click the ... button.

   c Add, modify, or delete subreport parameters, and click OK.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1036
Working with Documents, Charts, and Reports




Creating a simple report
The following example shows how to create a simple report by using the Report Wizard and then modifying it. The
example uses the cfartgallery database, which is installed with ColdFusion.

The example shows how to perform the following tasks:

* Create a base report by using the Report Wizard and the Query Builder.

* Use the Expression Builder to modify the data presentation in the report.

* Modify the display text for column data.

* Add a text field to the report and format text and data elements by using report styles.

* Add an image file and images from a database.

* Create and add a calculated field to display the total sales by artist.

* Add group-level and report-level pie charts that show the ratio of sold and unsold art for each artist and for all the
  artists in the database.

* Export report styles to a Cascading Style Sheet (CSS) file.


Create a report by using the Report Wizard
1 Start Report Builder.

2 Select Report Creation Wizard and click OK.

3 Click the Query Builder button:

  Note: If you have RDS configured, provide the RDS password.

  a From the list of data sources in the database pane, expand the cfartgallery database.

  b Expand the Tables folder.

  c Double-click the APP.ART table in the database pane. Report Builder adds the APP.ART table to the table pane.

  d Double-click the APP.ARTISTS table in the database pane. Report Builder adds the APP.ARTISTS table to the
     table pane. Notice that it automatically creates the join between the two tables based on the ARTISTID column.

  e In the APP.ARTISTS table, double-click the FIRSTNAME and LASTNAME columns. The Query Builder adds
     the fields to the select statement in the SQL pane.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1037
Working with Documents, Charts, and Reports




   f  In the ART table, double-click the ARTNAME, DESCRIPTION, PRICE, and ISSOLD columns. The following
      example shows the completed query in the Query Builder:




   g Click the Test Query button to preview the results.

   h Close the test query window and click the Save button in the Query Builder window.

4 Double-click the FIRSTNAME column to add it to the Non-printed Fields pop-up menu and click the Next button.

5 In the Available Fields list, double-click LASTNAME to group the records by the artists' last names.

6 Click the Next button three times to accept the default values.

7 Choose Silver and click the Next button.

8 Change the title of the report to Sales Report and click the Finish button. The Report Creation Wizard generates
   the report and displays it in the Report Builder workspace.

9 Choose File > Save As and save the report as ArtSalesReport1 in the default directory. Report Builder automatically
   adds the CFR extension.

10 Press F12 to preview the report. Report Builder displays the records grouped by the artists' last names.

11 Click the close box to close the Preview Report window and return to the Report Builder workspace.


Changing the column heading labels
By default, the Report Wizard uses the column name for the column headers in the report, but you can change the label
text for column headings.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1038
Working with Documents, Charts, and Reports




Edit the heading label text
1 Double-click the LASTNAME field in the Column Header band.

2 Replace the column name with Artist Name, and click OK.

3 Replace the remaining column labels as follows:

  * ARTNAME > Title

  * DESCRIPTION > Description

  * PRICE > Price

  * ISSOLD > Sold?


Using expressions to format data
Use the Expression Builder to perform the following tasks:

* Change the display of the ISSOLD value to a yes/no expression. By default, Report Builder displays 0 (not sold) or
  1 (sold) for the ISSOLD column based on how the data is stored in the database. You can use a function to change
  the display to yes or no.

* Change the value of the PRICE column to a dollar format.

* Concatenate the artists' first and last names. Even though the FirstName field is a nonprinted field in the report,
  you can add it to an expression because it is part of the SQL query that you created.


Change a Boolean value to yes/no
1 Double-click the query.ISSOLD element in the detail band. Report Builder displays the Expression Builder for that
  element.

2 In the Expression Builder, expand the Functions folder.

3 Choose Display and Formatting from the Functions list. Report Builder displays the list of functions in the right
  pane of the Expression Builder.

4 Double-click YesNoFormat from the list of functions. Report Builder automatically completes the following
  expression in the expression pane:

   YesNoFormat(query.ISSOLD)

5 Click OK to close the Expression Builder and return to the report.

6 Choose File > Save to save your changes to the report.

7 Press F12 to preview the report. Yes or no appears in the Sold? column based on whether the artwork sold.


Display numbers in dollar format
1 Double-click the field in the PRICE column of the detail band.

2 In the expression pane, change the expression to the following text:

   DollarFormat(query.PRICE)

3 Click OK to close the Expression Builder and return to the report.


Concatenate the FIRSTNAME and LASTNAME fields
1 Double-click the query.LASTNAME field in the LASTNAME group header.

2 In the Expression Builder, type the following expression:

   query.FIRSTNAME &" "& query.LASTNAME




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1039
Working with Documents, Charts, and Reports




  Notice that the Expression Builder prompts you with the available field names as you type.

3 Click the OK button in the Expression Builder.

4 Choose File > Save from the Report Builder menu bar to save your changes to the report.

5 Press F12 to preview the report.

  Report Builder displays the first and last name for each of the artists. Notice that the report still is grouped
  alphabetically by last name.

6 Close the preview window.


Adding page breaks before group changes
Create a page break so that each artist name starts on at the top of a page in the report output.


Add page breaks between artist names
1 Choose Report > Group Management from the main menu bar. The Group Management dialog box appears with
  LASTNAME selected.

2 Click the Edit button.

3 Select the Start New Page option and click OK.


Adding a calculated field


Calculate the sum of the artwork sold by artist
1 Choose Window > Fields and Parameters.

2 Report Builder displays the Fields and Parameters panel.

3 Expand the list of calculated fields.

4 With Calculated Fields selected, click the (+) button at the upper edge of the Fields and Parameters panel.

5 Make the following changes in the Add Calculated Field dialog box:

  a Change the name of the calculated field to Sold.

  b Change the label text to Sold.

  c Change the Data Type to Float.

  d Change the Calculation to Sum.

  e In the Perform Calculation On field, enter the following expression:

      Iif(IsBoolean(query.ISSOLD) and query.ISSOLD, query.Price,0)

     This expression multiplies the total price of the artwork per artist by the number of items sold to calculate the
     total sales per artist. If the ISSOLD value for a record is 1 (sold), the value is multiplied by 1 and added to the
     total; if the ISSOLD value for a record is 0 (unsold), the value is multiplied by 0.

  f  Change the Reset Field When value to Group.

  g Change the Reset Group value to LASTNAME, and click OK. Report Builder adds the calculated field definition
     in the Fields and Parameters panel.


Add the calculated field to your report
1 Insert a field in the LASTNAME Footer band.

2 In the Add Field dialog box, select calc.Sold from the pop-up menu.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1040
Working with Documents, Charts, and Reports




3 In the Expression Builder, type the following code:

   DollarFormat(calc.Sold)

4 Press F12 to preview the report. Report Builder displays the sum of the artwork sold for each artist.


Adding and formatting fields
You can add a text field to your report and define a style for it. When you define a style, you can reuse it throughout
your report or export the style so that you can use it in other reports. Also, you can override report styles at run time
by using the cfreport and the cfreportparam tags. For more information, see "Overriding report styles" on
page 1048.


Add a text field
1 In the Controls toolbox on the left side of the Report Builder window, click the text icon (the button with abc on it)
   and place the text field to the left of the calculated field in the LASTNAME footer.

2 In the Edit Label dialog box, type Total Sales, and click OK.


Create a style
1 Choose Window > Report Styles from the main menu.

2 Click the (+) button.

3 In the Name field, enter GroupFooter.

4 Click the Color and Style tab and change the color to #9999CC.

5 Click the Font tab and change the Font to Tahoma and click the bold option. Then click OK. Report Builder adds
   GroupFooter style to the pop-up menu of available styles in the report.

6 Choose File > Save from the menu bar to save your changes to the report.


Apply the style to text and data elements in the report
1 Select the Total Sales text box in the LASTNAME Footer band.

2 Choose Window > Properties Inspector.

3 Choose GroupFooter from the Style pop-up menu.

4 Select the calculated field element and apply the GroupFooter Style to it.

5 Press F12 to preview your report:


Adding images
When you add images with Report Builder, you can perform the following types of tasks:

* Replace the company name text box with a company logo in the report header.

* Use the Query Builder to add images from a database.

* Display the report in RTF format for faster display.


Add a logo to the report header
1 Select the Company Name text box located in the header band preceding Sales Report.

2 Choose Edit > Cut to remove the text box from the report.

3 Click the Add Image icon in the Controls toolbox. (The icon has a picture of a tree on it.)




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1041
Working with Documents, Charts, and Reports




4 Drag the mouse in the header band preceding the Sales Report text box. When you release the mouse, the Image
   File Name dialog box appears.

5 Navigate to the Art World logo file:

   C:\ColdFusion9\wwwroot\cfdocs\getting_started\photos\somewhere.jpg

6 Click Open. Report Builder displays the Art World logo in the area that you selected.

7 With the image selected in the workspace, choose Windows > Properties Inspector. The Properties Inspector for
   the image appears:

   a Under Colors and Style, change the Transparency to Transparent.

   b Under Formatting, change Scale Image to Retain Shape.

8 In the Header band, control-click the logo image and the Sales Report text box in the workspace to select them.

9 Click the Align Left Sides icon in the Controls toolbox.

10 Choose File > Save to save your changes.

11 Press F12 to preview the report.

12 Close the preview window and readjust the image size and location as needed.


Add images from a database
1 From the menu bar, choose Report > Report Query.

2 In the Art table, double-click LARGEIMAGE. The Query Builder adds the LARGEIMAGE column to the select
   statement.

3 Click the Test Query button. A list of image filenames appears to the right of the ISSOLD column.

4 Close the Test Query window and click the Save button in the Query Builder.

5 In the Report window, expand the Detail band by clicking the lower splitter bar and dragging down.

6 Click the Add Image icon in the Controls toolbox and drag the mouse in Detail band of the report to the left of the
   query.ARTNAME field. When you release the mouse, the Image File Name dialog box appears.

7 Navigate to the cfartgallery images directory:

   C:\ColdFusion9\wwwroot\cfdocs\images\artgallery

8 In the File Name field, type #query.largeimage#.

9 Click the Open button. Report Builder adds the column to the Detail band of the report.

10 Align the image column with the top of the Detail band.

11 With the image element selected in the detail band, choose Window > Properties Inspector.

12 Change the following properties:

   a Transparency: Transparent.

   b Scale Image: Retain Shape. This option scales the images proportionately within the bounding box.

   c Error Control: No Image. This option ensures that Report Builder displays blank images rather than generates
      an error for images missing from the database.

   d Using Cache: False. This option refreshes the display each time you preview the report output in the browser.

13 Choose File > Save to save your changes.




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1042
Working with Documents, Charts, and Reports




Change the report output format
1 Choose Report > Report Properties from the menu bar.

2 From the Default Output Format pop-up menu, choose RTF. Use this format for faster display in a web browser.

3 Click OK to close the Report Properties dialog box and return to the report.

4 Choose File > Save to save your changes.

5 Press F12 to preview the report. The images are displayed beneath Artist name and to the left of the art title.

6 Change the Default Output Format to HTML and preview the results.


Adding charts
You can use the Chart Builder to add two pie charts to your report: the first pie chart shows the total dollar amount of
the art sold versus the total dollar amount unsold art for each artist; the second pie chart shows the sum of artwork
sold versus unsold for all of the artists.

The two pie charts are the same except for the scope. To apply a pie chart to a group (the ratio of sold to unsold art for
each artist), add the pie chart to the group footer band. To apply the pie chart to the report (the ratio of sold to unsold
art for all artists), add the pie chart to the report footer band.

In "Adding a calculated field" on page 1039, you added a calculated field for the total dollar amount of artwork sold.
Before you can create the pie chart for this example, create a second calculated field for the total dollar amount of
unsold art.


Add a calculated field for the sum of unsold art
1 Choose Window > Fields and Parameters.

2 Select the Calculated Fields heading in the Fields and Parameters panel.

3 Click the (+) icon at the upper edge of the panel:

   a In the Name field, type Unsold.

   b In the Default Label Text field, type Unsold.

   c In the Data Type field, choose Big Decimal from the pop-up menu.

   d In the Calculation field, choose Sum from the pop-up menu.

   e In the Perform Calculation On field, enter the following expression to calculate the dollar amount of unsold art:

        Iif(IsBoolean(query.ISSOLD) and not(query.ISSOLD), query.Price,0)

   f   In the Reset Field When field, choose Group from the pop-up menu.

   g In the Reset Group field, choose LASTNAME.

   h Click OK to close the Add Calculated Field dialog box and return to the report.

4 Choose File > Save from the menu bar to save your changes to the report.


Add a pie chart to the group footer
1 Expand the LASTNAME Footer band.

2 Choose Insert > Chart from the Report Builder menu bar:

   a Choose Pie from the Base Chart Type list. The Chart Sub-Type appears to the right of the Base Chart Type.

   b Choose the 3D chart.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               1043
Working with Documents, Charts, and Reports




3 Click the Next button. Then click the Add button:

   a In the Series Label field, type Total Sales.

   b In the Paint Style field, choose Light.

   c In the Data Label field, choose Value.

   d In the Color List, type Teal,Gray.

   e In the Chart Data Source area, ensure that the Data From A Fixed List of Values option is selected.

4 Click the Add button:

   a In the Label field, type Sold.

   b In the Value field, choose #calc.Sold# from pop-up menu.

   c Click OK.

5 Click the Add button again:

   a In the Label field, type Unsold.

   b In the Value field, choose #calc.Unsold# from the pop-up menu.

   c Click OK twice to return to the Chart Series dialog box.

6 Click the Next button. In the Chart Formatting dialog box, click the Titles & Series tab and make the following
   changes:

   a In the Chart Title field, type Total Sales for #query.LASTNAME#.

   b In the X Axis Title field, type Sold.

   c In the Y Axis Title field, type Unsold.

   d In the Label Format field, choose Currency from the pop-up menu.

   e Click the 3-D Appearance tab and ensure that Show 3-D is selected.

7 Click the Font tab and make the following changes:

   a Change the Font Name to Arial.

   b Change the Font Size to 9.

8 Click the Finish button. Report Builder adds a place holder for the pie chart in the report.

9 Resize and move the chart to the desired location within the LASTNAME Footer band.

10 Choose File > Save to save your changes to the report.

11 Press F12 to preview the report.


Add a pie chart to the report footer
1 Create two calculated fields to use in the report footer pie chart with the following parameters:


   Name                        TotalSold                               TotalUnsold

   Default Label Text:         Total Sold                              Total Unsold

   Data Type:                  Big Decimal                             Big Decimal

   Calculation:                Sum                                     Sum

   Perform Calculation On:     Iif(IsBoolean(query.ISSOLD) and         Iif(IsBoolean(query.ISSOLD) and
                               query.ISSOLD, query.Price,0)            not(query.ISSOLD), query.Price,0)




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1044
Working with Documents, Charts, and Reports




    Name                        TotalSold                                TotalUnsold

    Initial Value:              0                                        0

    Reset Field When:           Report (Changes)                         Report (Changes)

    Reset Group:                LASTNAME                                 LASTNAME


2 Expand the Report Footer band, which is located directly below the Page Footer band.

3 Copy the pie chart from the Group Footer and paste it in the Report Footer.

4 Double-click the pie chart and click the Next button.

5 Double-click Total Sales to display the Edit Chart Series dialog box.

6 Change the Series Label to Total Sales for Artists.

7 Change the chart series values:


    Label                                                Value

    Sold                                                 #calc.TotalSold#

    Unsold                                               #calc.TotalUnsold#


8 Click the Next button, and then Click the Title & Series tab.

9 Change the Chart Title to Total Sales for Artists, and click Finish.

10 Choose File > Save from the menu bar to save your changes to the report.

11 Press F12 to preview the report.

The Total Sales for Artists pie chart appears only on the last page of the report. Verify that the calculations are correct.


Using Cascading Style Sheets
The Report Creation Wizard automatically creates and applies the following styles to your report:

* ReportTitle

* CompanyName

* PageTitle

* ReportDate

* SubTitle

* DetailData (default style)

* DetailLabel

* PageFooter

* RectangleStyle

* LineStyle

The instructions on "Adding and formatting fields" on page 1040 show how to add a field called GroupFooter and
apply it to a text field and a data field in the GroupFooter band. You can export the styles in a report to a CSS file.
Report Builder automatically generates the CSS code for the styles. This technique is an efficient way to maintain a
single set of styles to use with multiple reports. You can modify the styles in the CSS file by using any text editor and
either import the CSS file in Report Builder or override the styles in the report at run time.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 1045
Working with Documents, Charts, and Reports




Export report styles to a CSS file
1 Choose Window > Report Styles.

2 Click the export icon (the icon with the orange arrow).

3 In the File Name field, type artstyles. Report Builder automatically adds the CSS extension.

4 Navigate the artStyles.css file and double-click it to open it. The following example shows the generated CSS code:

   ReportTitle
  {
             color:Black;
             font-size:24pt;
  }
  CompanyName
  {
             color:#6188A5;
             font-weight:bold;
  }
  PageTitle
  {
             color:#333333;
             font-size:14pt;
             font-weight:bold;
  }
  ReportDate
  {
             color:#333333
  }
  SubTitle
  {
             color:#6089A5;
             font-size:12pt;
             font-weight:bold;
  }
  DetailLabel
  {
             color:Black;
             background-color:#E3EDEF;
             font-weight:bold;
  }
  DetailData
  {
             default-style:true;
             color:Black;
             line-size:thin;
  }




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           1046
Working with Documents, Charts, and Reports




   PageFooter
   {
              color:#2F2F2F;
              font-size:8pt;
   }
   RectangleStyle
   {
              color:#E3EDEF;
              background-color:#E3EDEF;
   }
   LineStyle
   {
              color:#CCCCCC;
              background-color:#CCCCCC;
   }
   GroupFooter
   {
              color:Blue;
              font-weight:bold;
              font-family:Tahoma;

5 Change the ReportTitle style color attribute to Red and add the font-weight attribute, as the following code
  shows:

    ReportTitle
   {
              color:Red;
              font-size:24pt;
              font-weight: bold;
   }

6 Save the CSS file.

Also, you can override report styles from ColdFusion. Form more information, see "Overriding report styles" on
page 1048.

Note: If you add a style to the CSS file, add a style with the same name to the report in Report Builder. Also, Report Builder
does not support all CSS styles. For more information, see the cfreport tag in the CFML Reference.


Import the CSS file
1 Choose Window > Report Styles.

2 Click the import styles icon (the one with the blue arrow).

3 Navigate to the location of the artStyles.css file, and click OK. Report Builder automatically updates the report style
  definition and applies the updated style to report title.

4 Press F12 to preview the report.


Overriding report settings at run time
You can use the cfreport tag in ColdFusion to override report settings in a Report Builder report at run time. The
examples use the CFR file that you created in "Creating a simple report" on page 1036.


Overriding the report query
This example filters the data in the report based on the login ID of the artist. When the artist logs on, the report displays
the data and pie chart for that artist. The report also includes the pie chart with data from all the artists.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1047
Working with Documents, Charts, and Reports




The following code creates a simple login page in ColdFusion. The form uses artist's last name as the user ID. (The
code does not include password verification):

<h3>Artist Login Form</h3>
<p>Please enter your last name and password.</p>
<cfform name="loginform" action="artSalesReport.cfm" method="post">
<table>
     <tr>
          <td>Last Name:</td>
          <td><cfinput type="text" name="username" required="yes" message="A username is
                required."></td>
     </tr>
     <tr>
          <td>Password:</td>
          <td><cfinput type="password" name="password" required="yes" message="A password is
                required."></td>
     </tr>
</table>
     <br />
     <cfinput type="submit" name="submit" value="Submit">
</cfform>

On the processing page, add a query like the one you created in the Report Builder report. The ColdFusion query must
contain at least all of the columns included in the Report Builder query; however, the ColdFusion query can contain
additional data.

The query in the following example selects all of the data from the ART and ARTISTS tables based on the artist's last
name. The cfreport tag uses the pathname of the CFR file as the report template.

<cfquery name="artsales" datasource="cfartgallery">
SELECT *
FROMAPP.ART, APP.ARTISTS
WHERE APP.ART.ARTISTID = APP.ARTISTS.ARTISTID
            AND APP.ARTISTS.LASTNAME= <cfqueryparam value="#FORM.username#">
ORDER BY ARTISTS.LASTNAME
</cfquery>


<cfreport query="#artsales#" template="ArtSalesReport1.cfr" format="RTF"/>

ColdFusion displays the report for the artist in RTF format. Notice that the value of the format attribute overrides the
Default Output format defined in the CFR file.


Exporting the report in HTML
To generate a report in HTML and display it directly in the browser, change the format attribute to HTML:

<cfreport template="ArtSalesReport1.cfr" format="HTML"/>

ColdFusion automatically generates a temporary directory where it stores all of the image files in the report (charts are
saved as PNG files). The location of the temporary directory is:

C:\ColdFusion9\tmpCache\CFFileServlet\_cfreport\_report[unique_identifier]

You can specify when the temporary directory is removed from the server by using the CreateTimeSpan function as
a value for the resourceTimespan attribute:

<cfreport query="#artsales#" template="ArtSalesReport1.cfr" format="HTML"
resourceTimespan="#CreateTimeSpan(0,1,0,0)#"/>




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1048
Working with Documents, Charts, and Reports




You can specify the time span in days, hours, minutes, and seconds. In this example, the temporary directory is deleted
after one hour. For more information, see the CFML Reference.

To export the report output to an HTML file, specify the filename attribute. The following code writes the report
output to an HTML file called artSales.html:

 <cfreport template="ArtSalesReport1.cfr" format="HTML" filename="artSales.html"
overwrite="yes"/>

ColdFusion creates an image directory relative to the HTML output file in the format filename_files. In this example,
ColdFusion automatically generates PNG files for the charts in the report and saves them to a directory called
artSales_files. Also, it generates copies of all of the JPG images extracted from the cfartgallery database and stores them
in the artSales_files directory. For more information, see the CFML Reference.


Overriding report styles
To override the report styles in a report, specify the style attribute of the cfreport tag. The value must contain valid
CSS syntax, the pathname to a CSS file, or a variable that points to valid CSS code. The CSS style names must match
the report style names defined in Report Builder.

The following code shows how to override the styles in the ArtSalesReport1.cfr report with the styles defined in the
artStyles.css file:

 <cfreport template="ArtSalesReport1.cfr" style="artStyles.css" format="PDF"/>

The following code shows how to apply a CSS style as a value of the style attribute:

 <cfreport template="ArtSalesReport1.cfr" style='ReportTitle {defaultStyle: false;
     font-family:"Tahoma"; color: "lime";}' format="FlashPaper">
</cfreport>

The following code shows how to create a variable called myStyle and use it as a value of the style attribute:

 <cfset mystyle='DetailData { defaultStyle: true; font-family: "Tahoma"; color: ##00FFF0;}'>
<cfreport template="ArtSalesReport1.cfr" style="#mystyle#" format="HTML">
</cfreport>

For more information, see the cfreport tag in the CFML Reference.



Creating Slide Presentations

You can use Adobe ColdFusion to create slide presentations.


About ColdFusion presentations
ColdFusion lets you create dynamic slide presentations from source files and from CFML and HTML code on a
ColdFusion page. You can use data extracted from a database to populate the slide content, including graphs and
charts. Also, you can add images, audio tracks, and video clips to each slide in the presentation. ColdFusion provides
three tags for creating slide presentations:




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    1049
Working with Documents, Charts, and Reports




 Tag                              Description

 cfpresentation                   Defines the look of the presentation and determines whether the presentation is saved to files or run
                                  directly in the client browser.

 cfpresentationslide              Defines the content of the slide from one of the following:

                                  * A SWF file

                                  * An HTML file

                                  * A URL that returns HTML content

                                  * HTML and CFML code in the cfpresentationslide start and end tags

 cfpresenter                      Provides information about the person presenting a slide. You can assign a presenter to one or more
                                  slides. Presenter information is displayed in the control panel for the duration of the slide.


You specify at least one slide for the presentation and can assign each presenter to one or more slides. The following
example shows a slide presentation with content from four different sources and two presenters:

<cfpresentation title="myPresentation">
     <cfpresenter name="Tuckerman" title="V.P. of Marketing"
                  email="tuckerman@company.com">
     <cfpresenter name="Anne" title="V.P. of Sales" email="anne@company.com">
     <cfpresentationslide src="slide1.swf" title="Overview" duration="10"
                  presenter="Anne"/>
     <cfpresentationslide src="slide2.htm" title="Q1 Sales" duration="30"
                  presenter="Anne"/>
     <cfpresentationslide src="http://www.markettrends.com/index.htm"
                  title="Market Trends" duration="30" presenter="Tuckerman"/>
     <cfpresentationslide title="Summary" duration="10">
           <h3>Summary</h3>
                  <ul>
                     <li>Projected Sales</li>
                     <li>Challenges Ahead</li>
                     <li>Long Term Goals</li>
                  </ul>
     </cfpresentationslide>
</cfpresentation>

Note: The cfpresentationslide tag requires an end tag. If you specify a source file as the slide content, use the end
slash as a shortcut for the end tag.

When the presentation runs, the slides appear in the order they are listed on the ColdFusion page for the duration
specified in each slide. The presenter information is displayed in a control panel next to the slide to which it is assigned.


Creating a slide presentation
Use the cfpresentation tag to customize the look of the slide presentation. Customizations can include the control
locations and the colors used in the presentation interface, as the following example shows:

<cfpresentation title="Sales Presentation" controlLocation="left" primaryColor="##0000FF"
shadowColor="###000033" textColor="##FFFF00" showNotes="yes">

The title appears at the top of the control panel. The color settings affect the presentation interface, but not the format
of the slides within the presentation. Set the showNotes attribute to yes to display text notes that are defined for
individual slides.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1050
Working with Documents, Charts, and Reports




If you do not specify a directory, as in the previous example, ColdFusion runs the presentation directly in the client
browser. The presentation uses files written to a temp directory on the server. To save the presentation, specify an
absolute path or a directory relative to the CFM page. (ColdFusion does not create the directory; it must exist already.)
In the following example, the presentation files are stored in the salesPresentation directory on the local drive:

<cfpresentation title="Sales Presentation" directory="c:\salesPresenation">

ColdFusion automatically generates the following files necessary to run the presentation and saves them in the
specified directory:

* components.swf

* index.htm

* loadflash.js

* viewer.swf

Also, ColdFusion creates a subdirectory called data where it stores the following files:

* srchdata.xml (which creates the search interface)

* vconfig.xml

* viewer.xml

* A SWF file generated for each slide in the presentation

* Copies of the media files referenced in the presentation slides

   Media files can include JPEG files, FLV and SWF video files, and mp3 audio files. To run the presentation that you
   saved to files, double-click the index.htm file.

Note: ColdFusion does not overwrite the files referenced by the slides in the presentation; changes to the generated
presentation files do not affect the source files.


Adding presenters
Optionally, you can add one or more presenters under the cfpresentation tag. ColdFusion displays the presenter
information in the control panel for the current slide to which it is assigned. A slide does not require a presenter.

Use the cfpresenter tag to specify personal information. This information can include a title, an e-mail address, a
logo and an image of the person, as the following code shows:

<cfpresentation title="Sales Presentation">
<cfpresenter name="Anne" title="V.P. of Sales" biography="Anne Taylor has been a top seller
at Widgets R Us for five years." logo="images/logo.jpg" image="images/ataylor_empPhoto.jpg"
email="ataylor@widgetsrus.com">

The name attribute is required. You use this value to assign the presenter to one or more slides. To assign a presenter
to a slide, use the cfpresenter tag name attribute value as the cfpresentationslide tag presenter attribute. The
following example creates a presenter named Tuckerman and assigns him to a slide called Overview:

<cfpresentation title="Sales Presentation">
<cfpresenter name="Tuckerman" title="V.P. of Marketing">
<cfpresentationslide title="Overview" src="overview.swf" presenter="Tuckerman"
duration="10"/>
...
</cfpresentation>

Note: Assign presenters explicitly to slides. To assign a presenter to more than one slide, use the presenter name in each
of the cfpresentationslide tags.



                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1051
Working with Documents, Charts, and Reports




When you assign a presenter to a slide, the presenter information is displayed in the control panel for the duration of
the slide. Images must be in JPEG format and the files must be located in a path relative to the ColdFusion page.
ColdFusion maps the email attribute value to the contact link in the control panel. This link opens an e-mail message
in the local e-mail application when you click it.

The following code creates three presenters for a presentation and assigns two of the presenters to slides:

<cfpresentation title="Sales Presentation">
     <cfpresenter name="Hannah" title="V.P. of Marketing" image="hannah.jpg">
     <cfpresenter name="Anne" title="V.P. of Sales" image="Anne.jpg">
     <cfpresenter name="Wilson" title="V.P. of Engineering"
                 image="Wilson.jpg">
     <cfpresentationslide title="Overview" presenter="Hannah" duration="30"
                 src="slide1.htm"/>
     <cfpresentationslide title="Q1 Sales" presenter="Anne" duration="15"
                 src="slide2.htm"/>
     <cfpresentationslide title="Projected Sales" presenter="Anne"
                 duration="15" src="slide3.htm" video="promo.flv"/>
     <cfpresentationslide title="Conclusion" src="slide4.htm"/>
</cfpresentation>

The presenter Hannah is assigned to one slide and Anne is assigned to two slides. The last slide in the presentation has
no presenter assigned to it. Because Wilson is not assigned to a slide, his information does not appear in the
presentation. In the second slide, Anne's photo is displayed in the control panel. In the third slide, however, the video
called promo.flv runs in place of Anne's photo in the control panel for the duration of the slide. The video does not
display in the slide.

Note: Videos must be in SWF or FLV format. You cannot specify audio and video for the same slide.


Adding slides
Use one cfpresentationslide tag for each slide in the presentation. The presentation runs the slides in the order
they are listed beneath the cfpresentation tag. You can create content for a slide in one of the following ways:


 Source                 Description                               Example

 A SWF or HTML file     The file must be located on the system    <cfpresentationslide title="slide 1"
                        running ColdFusion. You can specify an    src="presentation/slide1.swf"/>
                        absolute path or a path relative to the
                        ColdFusion page.

                                                                  <cfpresentationslide title="slide 2"
                                                                  src="c:/presentation/slide2.htm"/>

 A URL                  The URL must return HTML content.         <cfpresentationslide title="slide 3"
                                                                  src="http://www.worldview.com/index.htm"/>

 HTML and CFML code     Enclose the HTML and CFML code within the <cfpresentationslide>
 on the ColdFusion page cfpresentationslide start and end tags.
                                                                  <h3>Total Sales</h3>

                                                                  <cfchart format="jpg" chartwidth="500"
                                                                  show3d="yes">

                                                                  <cfchartseries type="pie" query="artwork"
                                                                  itemcolumn="issold" valuecolumn="price"/>

                                                                  </cfchart>

                                                                  </cfpresentationslide>




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1052
Working with Documents, Charts, and Reports




Creating content from source files
The following code creates a presentation with three slides from source files in different locations:

<cfpresentation title="Garden Mania" directory="gardenPresentation">
      <cfpresentationslide title="Seeds of Change" src="c:\gardening\seeds.html"
audio="media\hendrix.mp3" duration="30"/>
      <cfpresentationslide title="Flower Power" src="shockwave\flowerPower.swf" duration="40"/>
      <cfpresentationslide title="Dig Deep" src="http://www.smartgarden.com/index.htm"
duration="15"/>
</cfpresentation>

In this example, ColdFusion generates the files required to run the presentation in the gardenPresentation directory.
It generates a new SWF file in the data subdirectory from each of the slides. ColdFusion also copies the hendrix.mp3
file and saves it in the data subdirectory.

Note: Links within slides created from HTML files are not active.


Creating content from HTML and CFML code
If you do not specify a source file for a slide, create the content by using HTML or CFML in the cfpresentationslide
tag body. The following presentation contains one slide with each with the following types of content:

* Generated from HTML

* Generated from HTML and CFML

* Extracted from an HTML file on an external website




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               1053
Working with Documents, Charts, and Reports




<cfpresentation title="The Road Ahead">
<cfpresentationslide title="Yellow Bricks" audio="myaudio1.mp3" duration="10">
    <h3>Yellow Bricks</h3>
    <table cellpadding=10>
          <tr>
               <td>
                    <ul>
                          <li>Way to go Dorothy</li>
                          <li>Making tracks</li>
                          <li>No place like home</li>
                    </ul>
               </td>
               <td><img src="../cfdocs/images/artgallery/maxwell01.jpg"/>
               </td>
          </tr>
    </table>
    </cfpresentationslide>
<cfpresentationslide title="Wild Ride" duration="5">
    <h3>Wild Ride</h3>
    <cfchart format="jpg" title="Who's Ahead" show3D="yes" chartHeight=500 chartWidth=500>
          <cfchartseries type="pyramid">
               <cfchartdata item="Dorothy" value=10>
               <cfchartdata item="Tin Man" value=30>
               <cfchartdata item="Scarecrow" value=15>
               <cfchartdata item="Lion" value=50>
               <cfchartdata item="Toto" value=5>
          </cfchartseries>
    </cfchart>
</cfpresentationslide>
<cfpresentationslide title="The Golden Age of Ballooning" duration="10"
src="http://www.balloning.com/index.htm"/>
</cfpresentation>

Note: The value for the format attribute of the cfchart tag must be JPG or PNG.

The content for slides is not limited to static data: you can generate content from information extracted from a
database or a query of queries.


Sample presentations
This section provides two sample presentations.


Example 1
The following example creates a simple presentation that incorporates data retrieved from the cfdocexamples
database. It shows how to perform the following tasks:

* Create slides generated from HTML and CFML.

* Add images to slides.

* Add charts and tables with data extracted from a database.

* Add audio tracks to individual slides.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                           1054
Working with Documents, Charts, and Reports




<!--- The following query extracts employee data from the cfdocexamples
               database. --->
<cfquery name="GetSalaryDetails" datasource="cfdocexamples">
    SELECT Departmt.Dept_Name,
         Employee.FirstName,
         Employee.LastName,
         Employee.StartDate,
         Employee.Salary,
         Employee.Contract
    From Departmt, Employee
    Where Departmt.Dept_ID = Employee.Dept_ID
    ORDER BY Employee.LastName, Employee.Firstname
</cfquery>


<!--- The following code creates a presentation with three presenters. --->
<cfpresentation title="Employee Satisfaction" primaryColor="##0000FF" glowColor="##FF00FF"
lightColor="##FFFF00" showoutline="no">
    <cfpresenter name="Jeff" title="CFO" email="jeff@company.com"
               logo="../cfdocs/getting_started/photos/somewhere.jpg"
               image="../cfdocs/images/artgallery/jeff01.jpg">
    <cfpresenter name="Lori" title="VP Marketing" email="lori@company.com"
               logo="../cfdocs/getting_started/photos/somewhere.jpg"
               image="../cfdocs/images/artgallery/lori01.jpg">
    <cfpresenter name="Paul" title="VP Sales" email="paul@company.com"
               logo="../cfdocs/getting_started/photos/somewhere.jpg"
               image="../cfdocs/images/artgallery/paul01.jpg">


<!--- The following code creates the first slide in the presentation
               from HTML. --->
    <cfpresentationslide title="Introduction" presenter="Jeff"
               audio="myAudio1.mp3" duration="5">
    <h3>Introduction</h3>
    <table>
         <tr><td>
               <ul>
                   <li>Company Overview</li>
                   <li>Salary by Department</li>
                   <li>Employee Salary Details</li>
               </ul>
         </td></tr>
    </table>
    </cfpresentationslide>


<!--- The following code creates the second slide in the presentation.
               The chart is populated with data from the database query. --->
    <cfpresentationslide title="Salary by Department" presenter="Lori"
               duration="5" audio="myAudio3.mp3">
         <h3>Salary by Department</h3>
         <cfchart format="jpg" xaxistitle="Department" yaxistitle="Salary">
               <cfchartseries type="bar" query="GetSalaryDetails"
                         itemcolumn="Dept_Name" valuecolumn="salary">
               </cfchartseries>
         </cfchart>
    </cfpresentationslide>


<!--- The following code creates the third slide in the presentation. The table is populated
with data from the query. The table also contains an image located relative to the CFM page on



                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                              1055
Working with Documents, Charts, and Reports




the server. --->
    <cfpresentationslide title="Salary Details" presenter="Paul"
                 duration="10" audio="myAudio1.mp3">
          <h3>Employee Salary Details</h3>
          <table border cellspacing=0 cellpadding=5 valign="top">
          <tr>
          <td>
          <table border cellspacing=0 cellpadding=5 valign="top">
          <tr>
                 <th>Employee Name</th>
                 <th>Start Date</th>
                 <th>Salary</th>
                 <th>Department</th>
                 <th>Contract?</th>
          </tr>
          <cfoutput query="GetSalaryDetails">
          <tr>
                 <td>#FirstName# #LastName#</td>
                 <td>#dateFormat(StartDate, "mm/dd/yyyy")#</td>
                 <td>#numberFormat(Salary, "$9999,9999")#</td>
                 <td>#dept_name#</td>
                 <td>#Contract#</td>
                 </tr></cfoutput>
                 </table>
          </td>
          <td width="200" >
                 <img src="images/raquel02.jpg"/>
          </td>
    </table>
    </cfpresentationslide>
</cfpresentation>


Example 2
The following example shows how to create a simple sales presentation with data from the cfartgallery database.
Specifically, it shows how to perform the following tasks:

* Create slides generated from HTML and CFML.

* Create a slide from a URL that returns HTML content.

* Add charts with data extracted from a database and a query of queries.

* Add video and audio tracks to individual slides.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                            1056
Working with Documents, Charts, and Reports




   <!--- The following query extracts data from the cfartgallery database. --->
  <cfquery name="artwork" datasource="cfartgallery">
  SELECT FIRSTNAME || ' '|| LASTNAME AS FULLNAME, ARTISTS.ARTISTID, ARTNAME, PRICE, ISSOLD
  FROM ARTISTS, ART
  WHERE ARTISTS.ARTISTID = ART.ARTISTID
  ORDER BY LASTNAME
  </cfquery>


  <!--- The following query of queries determines the total dollar amount of
                 sales per artist. --->
  <cfquery dbtype="query" name="artistname">
  SELECT FULLNAME,
  SUM(PRICE) AS totalSale
  FROM ARTWORK
  WHERE ISSOLD = 1
  GROUP BY FULLNAME
  ORDER BY totalSale
  </cfquery>


  <!--- The following code determines the look of the slide presentation. ColdFusion displays
  the slide presentation directly in the browser because no destination is specified. The title
  appears above the presenter information. --->
  <cfpresentation title="Art Sales Presentation" primaryColor="##0000FF" glowColor="##FF00FF"
  lightColor="##FFFF00" showOutline="yes" showNotes="yes">


  <!--- The following code defines the presenter information. You can assign each presenter
  to one or more slides. --->
        <cfpresenter name="Aiden" title="Artist" email="Aiden@artgallery.com"
  image="../cfdocs/images/artgallery/aiden01.jpg">
        <cfpresenter name="Raquel" title="Artist" email="raquel@artgallery.com"
  image="../cfdocs/images/artgallery/raquel05.jpg">
        <cfpresenter name="Paul" title="Artist" email="paul@artgallery.com"
  image="../cfdocs/images/artgallery/paul01.jpg">


  <!--- The following code defines the content for the first slide in the presentation. The
  duration of the slide determines how long the slide plays before proceeding to the next
  slide. The audio plays for the duration of the slide. --->
        <cfpresentationslide title="Introduction" presenter="Aiden" duration="5"
  audio="myAudio1.mp3">
        <h3>Introduction</h3>
        <table>
             <tr><td>
                 <ul>
                       <li>Art Sales Overview</li>
                       <li>Total Sales</li>
                       <li>Total Sales by Artist</li>
                       <li>Conclusion</li>
                 </ul>
             </td>
             <td><img src="../cfdocs/images/artgallery/maxwell01.jpg"/></td></tr>
        </table>
        </cfpresentationslide>


  <!--- The following code generates the second slide in the presentation from an HTML file
  located on an external website. --->
        <cfpresentationslide title="Artwork Sales Overview" presenter="Raquel"
  audio="myAudio2.mp3" duration="5" src="http://www.louvre.com/index.html"/>



                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                           1057
Working with Documents, Charts, and Reports




  <!--- The following code generates the third slide in the presentation, which contains a
  pie chart with data extracted from the initial database query. ColdFusion runs the video
  defined in the cfpresentationslide tag in place of the presenter image defined in the
  cfpresenter tag. --->
        <cfpresentationslide title="Total Artwork Sold" presenter="Aiden"
                 duration="5" video="video1.flv">
             <h3>Total Sales</h3>
             <cfchart format="jpg" chartwidth="500" show3d="yes">
                 <cfchartseries type="pie" query="artwork"
                             colorlist="##00FFFF,##FF00FF" itemcolumn="issold"
                             valuecolumn="price"/>
                 </cfchart>
             </cfpresentationslide>


  <!--- The following code generates the fourth slide in the presentation with
                       data extracted from the query of queries. --->
        <cfpresentationslide title="Sales by Artist" presenter="Paul"
                 duration="5" audio="myAudio3.mp3">
             <h3>Total Sales by Artist</h3>
             <table border cellspacing=10 cellpadding=0>
             <TR>
             <TD>
                 <table border cellspacing=0 cellpadding=5>
                       <tr>
                             <th>Artist Name</th>
                             <th>Total Sales</th>
                       </tr>
                       <tr>
                       <cfoutput query="artistname">
                       <td>#FULLNAME#</td>
                       <td>#dollarFormat(totalSale)#</td>
                       </tr>
                       </cfoutput>
                 </table>
                 </td>
                 <td>
                       <cfchart format="jpg" xaxistitle="Artist" yaxistitle="Total Sales"
                                  chartwidth="400">
                             <cfchartseries type="bar" query="artistname"
                                        itemcolumn="fullname" valuecolumn="totalSale"/>
                       </cfchart>
                 </td>
                 </tr>
             </table>
        </cfpresentationslide>


  <!--- The following code defines the final slide in the presentation. This slide does not
  have a presenter assigned to it. --->
        <cfpresentationslide title="Conclusion" duration="1" notes="Special thanks to Lori and
  Jeff for contributing their art and expertise.">
        <h1>Great Job Team!</h1>
        <p><img src="../cfdocs/images/artgallery/paul05.jpg"></p>
        </cfpresentationslide>
  </cfpresentation>



