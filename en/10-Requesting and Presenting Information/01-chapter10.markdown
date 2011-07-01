Chapter 10: Requesting and Presenting
Information

Introduction to Retrieving and Formatting Data

Adobe ColdFusion lets you retrieve and format data. You can use forms to get user data and control the data that a
dynamic web page displays. You can also populate a table with query results and use ColdFusion functions to format
and manipulate data. To use these features, you should be familiar with HTML forms.


Using forms in ColdFusion
ColdFusion lets you use a variety of types of forms. You can use plain HTML or CFML, and you can generate HTML,
Flash, or skinned XML forms.


ColdFusion forms tags
You can use HTML or CFML tags to define your form. ColdFusion includes the following CFML tags that correspond
to HTML tags, but provide additional functionality:

* cfapplet

* cfform

* cfinput

* cfselect

* cftextarea

These tags support all the attributes of their HTML counterparts, plus ColdFusion attributes and features.

ColdFusion also provides the following forms tags that have no direct equivalent in HTML:

* cfcalendar     Lets users select dates from a Flash month-by-month calendar.

* cfgrid     Displays and lets users enter data in a row and column grid format; can get data directly from a query.

* cfslider    Lets users input data by moving a sliding marker.

* cftree    Displays data in a hierarchical tree format with graphical indicators; can get data directly from a query.


ColdFusion Form tag features
ColdFusion forms tags provide the following features:

Built-in validation support You can validate data in the client browser or on the server. You can specify that a field is
required, contains a specific type of data, has a maximum length, or is in a range of values. You can also use data
masking to control user input. For more information on validation, see "Validating Data" on page 743.

Note: ColdFusion also provides a method of doing on-server validation of HTML form controls.

Flash format forms and elements You can display a form as Flash, which works identically on a variety of platforms
and provides additional display features not available in HTML. These features include accordion-style and multiple-
tab form panes and automatic element positioning. You can also display cftree, cfgrid, and cfcalendar form




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  704
Requesting and Presenting Information




elements as Flash items in an otherwise-HTML form. For more information on Flash forms and form elements, see
"Creating Forms in Flash" on page 765.

XML Skinable forms ColdFusion can generate XML forms and apply XSLT skins to format the forms. XML format
forms let you separate the form presentation from the form logic and data field information. They give you detailed
control over the appearance of the forms by applying custom skins, and let you create custom controls. For more
information on XML skinnable forms, see "Creating Skinnable XML Forms" on page 783.

Direct support for ColdFusion variables You can easily use ColdFusion variables directly to populate your form
controls. For example, you can specify a query result to populate the cfgrid and cftree tags.

These features make CFML forms tags powerful and flexible, and let you easily develop fully featured, pleasing forms.

The CFML tags used here, do not describe or use most of their special features. See"Building Dynamic Forms with
cfform Tags" on page 722 for information on how to use many of the tags that are specific to ColdFusion, such as
cftree and cfgrid.


Creating a basic form
The following simple form shows how you can create a form that lets a user enter data. This form uses basic CFML
form tags. It does not use any of the advanced features of ColdFusion, such as validation, Flash or XML format, or
special input controls. You could convert it to a purely HTML form by removing the initial "cf" prefix from the tag
names, and the form would work.




The following table shows the format of form control tags:


 Control            Code

 Text control       <cfinput type="Text" name="ControlName" size="Value" maxlength="Value">


 List (select) box  <cfselect name="ControlName">
                    <option value="Value1">DisplayName1
                    <option value="Value2">DisplayName2
                    <option value="Value3">DisplayName3
                    </cfselect>

 Radio buttons      <cfinput type="Radio" name="ControlName" value="Value1">DisplayName1
                    <cfinput type="Radio" name="ControlName" value="Value2">DisplayName2
                    <cfinput type="Radio" name="ControlName" value="Value3">DisplayName3




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      705
Requesting and Presenting Information




Control           Code

Check box         <cfinput type="Checkbox" name="ControlName" value="Yes|No">Yes


Reset button      <cfinput type="Reset" name="ControlName" value="DisplayName">


Submit button     <cfinput type="Submit" name="ControlName" value="DisplayName">


The following listing shows the form source in detail. To test the form and use it as input for later examples, save this
code as formpage.cfm.

<html>
<head>
<title>Input form</title>
</head>
<body>
<!--- Specify the action page in the form tag. The form variables will
          pass to this page when the form is submitted. --->


<cfform action="actionpage.cfm" method="post">


<!--- Text box. --->
<p>
First Name: <cfinput type="Text" name="FirstName" size="20"maxlength="35"><br>
Last Name: <cfinput type="Text" name="LastName" size="20" maxlength="35"><br>
Salary: <cfinput type="Text" name="Salary" size="10" maxlength="10">
</p>


<!--- List box. --->
<p>
City
<cfselect name="City">
    <option value="Arlington">Arlington
    <option value="Boston">Boston
    <option value="Cambridge">Cambridge
    <option value="Minneapolis">Minneapolis
    <option value="Seattle">Seattle
</cfselect>
</p>


<!--- Radio buttons. --->
<p>
Department:<br>




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      706
Requesting and Presenting Information




<cfinput type="radio" name="Department" value="Training">Training<br>
<cfinput type="radio" name="Department" value="Sales">Sales<br>
<input type="radio" name="Department"
      value="Marketing">Marketing<br>
</p>


<!--- Check box. --->
<p>
Contractor? <cfinput type="checkbox" name="Contractor"
      value="Yes" checked>Yes
</p>


<!--- Reset button. --->
<cfinput type="Reset" name="ResetForm" value="Clear Form">
<!--- submit button --->
<cfinput type="Submit" name="SubmitForm" value="Submit">


</cfform>
</body>
</html>


Forms guidelines
When using forms, keep in mind the following guidelines:

* To make the coding process easy to follow, name form controls the same as target database fields. For example, if
    a text control corresponds to a data source FirstName field, use FirstName as the control name.

* For ease of use, limit radio buttons to between three and five mutually exclusive options. If you need more options,
    consider a drop-down list.

* Use list boxes to allow the user to choose from many options or to choose multiple items from a list.

* Check boxes, radio buttons, and list boxes do not pass data to action pages unless they are selected on a form. If you
    try to reference these variables on the action page, you receive an error if they are not present. For information on
    how to determine whether a variable exists on the action page, see "Testing for a variable's existence" on page 709.

* You can dynamically populate drop-down lists using query data. For more information, see "Dynamically
    populating list boxes" on page 718.


Working with action pages
When the user submits a form, ColdFusion runs the action page specified by the cfform or form tag action attribute.
A ColdFusion action page is like any other application page, except that you can use the form variables that are passed
to it from an associated form.


Processing form variables on action pages
The action page gets a form variable for every form control that contains a value when the form is submitted.

Note: If multiple controls have the same name, one form variable is passed to the action page with a comma-delimited
list of values.

A form variable's name is the name that you assigned to the form control on the form page. Refer to the form variable
by name within tags, functions, and other expressions on an action page.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    707
Requesting and Presenting Information




On the action page, the form variables are in the Form scope, prefix them with "Form." to explicitly tell ColdFusion
that you are referring to a form variable. For example, the following code references the LastName form variable for
output on an action page:

<cfoutput>
     #Form.LastName#
</cfoutput>

The Form scope also contains a list variable called Form.fieldnames. It contains a list of all form variables submitted
to the action page. If no form variables are passed to the action page, ColdFusion does not create the
Form.fieldnames list.


Using form data to generate SQL statements
As described in previous chapters, you can retrieve a record for every employee in a database table by composing a
query like the following:

<cfquery name="GetEmployees" datasource="cfdocexamples">
     SELECTFirstName, LastName, Contract
     FROM Employee
</cfquery>

When you want to return information about employees that matches user search criteria, you use the SQL WHERE
clause with a SQL SELECT statement. When the WHERE clause is processed, it filters the query data based on the
results of the comparison.

For example, to return employee data for only employees with the last name of Smith, you build a query that looks like
the following:

<cfquery name="GetEmployees" datasource="cfdocexamples">
     SELECT FirstName, LastName, Contract
     FROM Employee
     WHERE LastName = 'Smith'
</cfquery>

However, instead of placing the LastName directly in the SQL WHERE clause, you can use the text that the user
entered in the form for comparison:

<cfquery name="GetEmployees" datasource="cfdocexamples">
     SELECT FirstName, LastName, Salary
     FROM Employee
     WHERE LastName=<cfqueryparam value="#Form.LastName#"
     CFSQLType="CF_SQL_VARCHAR">
</cfquery>

For security, this example encapsulates the form variable within the cfqueryparam tag to ensure that the user passed
a valid string value for the LastName. For more information on using the cfqueryparam tag with queries and on
dynamic SQL, see "Accessing and Retrieving Data" on page 410.


Creating action pages
Use the following procedure to create an action page for the formpage.cfm page that you created in the previous
example.


Create an action page for the form
1 Create a ColdFusion page with the following content:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                            708
Requesting and Presenting Information




   <html>
  <head>
  <title>Retrieving Employee Data Based on Criteria from Form</title>
  </head>


  <body>
  <cfquery name="GetEmployees" datasource="cfdocexamples">
        SELECT FirstName, LastName, Salary
        FROM Employee
        WHERE LastName=<cfqueryparam value="#Form.LastName#"
        CFSQLType="CF_SQL_VARCHAR">
  </cfquery>
  <h4>Employee Data Based on Criteria from Form</h4>
  <cfoutput query="GetEmployees">
  #FirstName#
  #LastName#
  #Salary#<br>
  </cfoutput>
  <br>
  <cfoutput>Contractor: #Form.Contractor#</cfoutput>
  </body>
  </html>

2 Save the page as actionpage.cfm in the myapps directory.

3 View the formpage.cfm page in your browser.

4 Enter data, for example, Smith, in the Last Name box and submit the form.

  The browser displays a line with the first and last name and salary for each entry in the database that match the
  name you typed, followed by a line with the text "Contractor: Yes".

5 Click Back in your browser to redisplay the form.

6 Remove the check mark from the check box and submit the form again.

  This time an error occurs because the check box does not pass a variable to the action page. For information on
  modifying the actionpage.cfm page to fix the error, see "Testing for a variable's existence" on page 709.


Reviewing the code
The following table describes the highlighted code and its function:


Code                                                       Description

<cfquery                                                   Queries the data source cfdocexamples and names the query
name="GetEmployees"datasource="cfdocexamples">             GetEmployees.

SELECT FirstName, LastName, Salary                         Retrieves the FirstName, LastName, and Salary fields from the
FROM Employee                                              Employee table, but only if the value of the LastName field matches
WHERE LastName=<cfqueryparam
                                                           what the user entered in the LastName text box in the form on
value="#Form.LastName#" CFSQLType="CF_SQL_VARCHAR">
                                                           formpage.cfm.

<cfoutput query="GetEmployees">                            Displays results of the GetEmployees query.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   709
Requesting and Presenting Information




 Code                                                        Description

 #FirstName#                                                 Displays the value of the FirstName, LastName, and Salary fields for a
 #LastName#                                                  record, starting with the first record, then goes to the next line. Keeps
 #Salary#<br>
                                                             displaying the records that match the criteria you specified in the
                                                             SELECT statement, followed by a line break, until you run out of
                                                             records.

 </cfoutput>                                                 Closes the cfoutput block.

 <br>                                                        Displays a blank line followed by the text "Contractor": and the value
 <cfoutput>Contractor: #Form.Contractor#                     of the form Contractor check box.
 </cfoutput>
                                                             A more complete example would test to ensure the existence of the
                                                             variable and would use the variable in the query.



Testing for a variable's existence
Before relying on a variable's existence in an application page, you can test to see if it exists using the ColdFusion
IsDefined function. A function is a named procedure that takes input and operates on it. For example, the IsDefined
function determines whether a variable exists. CFML provides a large number of functions, which are documented in
the CFML Reference.

The following code prevents the error in the previous example by checking to see whether the Contractor Form
variable exists before using it:

<cfif IsDefined("Form.Contractor")>
     <cfoutput>Contractor: #Form.Contractor#</cfoutput>
</cfif>

The argument passed to the IsDefined function must always be enclosed in double-quotation marks. For more
information on the IsDefined function, see the CFML Reference.

If you attempt to evaluate a variable that you did not define, ColdFusion cannot process the page and displays an error
message. To help diagnose such problems, turn on debugging in the ColdFusion Administrator. The Administrator
debugging information shows which variables are being passed to your application pages.


Requiring users to enter values in form fields
One of the limitations of HTML forms is the inability to define input fields as required. Because this is an important
requirement for database applications, ColdFusion lets you require users to enter data in fields. To specify a field as
required, you can do either of the following:

* Use the required attribute of the cfinput, cfselect, cftextarea, and cftree tags.

* Use a hidden field that has a name attribute composed of the field name and the suffix _required. You can use this
   technique with CFML and HTML form tags.

For example, to require that the user enter a value in the FirstName field of a cfinput tag, use the following syntax:

<cfinput type="Text" name="FirstName" size="20" maxlength="35" required="Yes">

To require that the user enters a value in the FirstName field of an HTML input tag, use the following syntax:

<input type="Text" name="FirstName" size="20" maxlength="35">
<input type="hidden" name="FirstName_required">

In either of these examples, if the user leaves the FirstName field empty, ColdFusion rejects the form submittal and
returns a message informing the user that the field is required. You can customize the contents of this error message.

If you use a required attribute, you customize the message by using the message attribute, as follows:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   710
Requesting and Presenting Information




<cfinput type="Text" name="FirstName" size="20" maxlength="35" required="Yes"
     message="You must enter your first name.">

If you use a hidden field tag, you customize the message using the value attribute of the hidden field, as follows:

<input type="hidden" name="FirstName_required"
     value="You must enter your first name.">


Form variable notes and considerations
When using form variables in an action page, keep in mind the following guidelines:

* A form variable is available on the action page and pages that it includes.

* Prefix form variables with "Form."when referencing them on the action page.

* Surround variable values with number signs (#) for output.

* Variables for check boxes, radio buttons, and list boxes with size attributes greater than 1 only get passed to the
   action page if you select an option. Text boxes, passwords, and text area fields pass an empty string if you do not
   enter text.

* An error occurs if the action page tries to use a variable that was not passed.

* If multiple controls have the same name, one form variable is passed to the action page with a comma-delimited
   list of values.

* You can validate form variable values on the client or the server.


Working with queries and data
The ability to generate and display query data is one of the most important and flexible features of ColdFusion. Some
of these tools are effective for presenting any data, not just query results.


Using HTML tables to display query results
You can use HTML tables to specify how the results of a query appear on a page. To do so, you place the cfoutput tag
inside the table tags. You can also use the HTML th tag to place column labels in a header row. To create a row in the
table for each row in the query results, place the tr block inside the cfoutput tag.

In addition, you can use CFML functions to format individual pieces of data, such as dates and numeric values.


Place the query results in a table
1 Open the ColdFusion actionpage.cfm page in your editor.

2 Modify the page so that it appears as follows:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 711
Requesting and Presenting Information




   <html>
  <head>
  <title>Retrieving Employee Data Based on Criteria from Form</title>
  </head>


  <body>
  <cfquery name="GetEmployees" datasource="cfdocexamples">
        SELECT FirstName, LastName, Salary
        FROM Employee
        WHERE LastName=<cfqueryparam value="#Form.LastName#"
        CFSQLType="CF_SQL_VARCHAR">
  </cfquery>
  <h4>Employee Data Based on Criteria from Form</h4>
  <table>
  <tr>
  <th>First Name</th>
  <th>Last Name</th>
  <th>Salary</th>
  </tr>
  <cfoutput query="GetEmployees">
  <tr>
  <td>#FirstName#</td>
  <td>#LastName#</td>
  <td>#Salary#</td>
  </tr>
  </cfoutput>
  </table>
  <br>
  <cfif IsDefined("Form.Contractor")>
        <cfoutput>Contractor: #Form.Contractor#</cfoutput>
  </cfif>
  </body>
  </html>

3 Save the page as actionpage.cfm in the myapps directory.

4 View the formpage.cfm page in your browser.

5 Enter Smith in the Last Name text box and submit the form.

  The records that match the criteria specified in the form appear in a table.


Reviewing the code

The following table describes the highlighted code and its function:


Code                                           Description

<table>                                        Places data into a table.

<tr>                                           In the first row of the table, includes three columns, with the headings: First Name,
<th>First Name</th>                            Last Name, and Salary.
<th>Last Name</th>
<th>Salary</th>
</tr>

<cfoutput query="GetEmployees">                Tells ColdFusion to display the results of the GetEmployees query.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                712
Requesting and Presenting Information




 Code                                            Description

 <tr>                                            For each record in the query, creates a row in the table, with three columns that
 <td>#FirstName#</td>                            display the values of the FirstName, LastName, and Salary fields of the record.
 <td>#LastName#</td>
 <td>#Salary#</td>
 </tr>

 </cfoutput>                                     Ends the output region.

 </table>                                        Ends the table.



Formatting individual data items
You can format individual data items. For example, you can format the salary data as monetary values. To format the
salary data using the dollar format, you use the CFML function DollarFormat.


Change the format of the Salary
1 Open the file actionpage.cfm in your editor.

2 Change the following line:

    <td>#Salary#</td>

   to

    <td>#DollarFormat(Salary)#</td>

3 Save the page.


Building flexible search interfaces
One option with forms is to build a search based on the form data. For example, you could use form data as part of the
WHERE clause to construct a database query.

To give users the option to enter multiple search criteria in a form, you can wrap conditional logic around a SQL AND
clause as part of the WHERE clause. The following action page allows users to search for employees by department,
last name, or both.

Note: ColdFusion provides the Verity search utility that you can also use to perform a search. For more information, see
"Building a Search Interface" on page 476.


Build a more flexible search interface
1 Open the ColdFusion actionpage.cfm page in your editor.

2 Modify the page so that it appears as follows:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                         713
Requesting and Presenting Information




   <html>
  <head>
  <title>Retrieving Employee Data Based on Criteria from Form</title>
  </head>
  <body>
  <cfquery name="GetEmployees" datasource="cfdocexamples">
        SELECT Departmt.Dept_Name,
             Employee.FirstName,
             Employee.LastName,
             Employee.StartDate,
             Employee.Salary
        FROM Departmt, Employee
        WHERE Departmt.Dept_ID = Employee.Dept_ID
        <cfif IsDefined("Form.Department")>
        AND Departmt.Dept_Name=<cfqueryparam value="#Form.Department#"
                        CFSQLType="CF_SQL_VARCHAR">
        </cfif>
        <cfif Form.LastName IS NOT "">
             AND Employee.LastName=<cfqueryparam value="#Form.LastName#"
                        CFSQLType="CF_SQL_VARCHAR">
        </cfif>
  </cfquery>


  <h4>Employee Data Based on Criteria from Form</h4>
  <table>
  <tr>
  <th>First Name</th>
  <th>Last Name</th>
  <th>Salary</th>
  </tr>
  <cfoutput query="GetEmployees">
  <tr>
  <td>#FirstName#</td>
  <td>#LastName#</td>
  <td>#Salary#</td>
  </tr>
  </cfoutput>
  </table>
  </body>
  </html>

3 Save the file.

4 View the formpage.cfm page in your browser.

5 Select a department, optionally enter a last name, and submit the form.


Reviewing the code
The following table describes the highlighted code and its function:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   714
Requesting and Presenting Information




 Code                                           Description

 SELECT Departmt.Dept_Name,                     Retrieves the fields listed from the Departmt and Employee tables, joining the
 Employee.FirstName,                            tables based on the Dept_ID field in each table.
 Employee.LastName,
 Employee.StartDate,
 Employee.Salary
 FROM Departmt, Employee
 WHERE Departmt.Dept_ID = Employee.Dept_ID

 <cfif IsDefined("Form.Department")>            If the user specified a department on the form, only retrieves records where the
 AND Departmt.Dept_Name=<cfqueryparam           department name is the same as the one that the user specified. Use number signs
 value="#Form.Department#"
                                                (#) in the SQL AND statement to identify Form.Department as a ColdFusion
 CFSQLType="CF_SQL_VARCHAR">
                                                variable, but not in the IsDefined function.
 </cfif>

 <cfif Form.LastName IS NOT "">                 If the user specified a last name in the form, only retrieves the records in which the
 AND Employee.LastName=<cfqueryparam            last name is the same as the one that the user entered in the form.
 value="#Form.LastName#"
 CFSQLType="CF_SQL_VARCHAR">
 </cfif>



Returning results to the user
When you return your results to the user, ensure that your pages respond to the user's needs and are appropriate for
the type and amount of information. In particular, consider the following situations:

* When there are no query results

* When you return partial results


Handling no query results
Your code must accommodate the cases in which a query does not return any records. To determine whether a search
has retrieved records, use the RecordCount query variable. You can use the variable in a conditional logic expression
that determines how to display search results appropriately to users.

Note: For more information on query variables, including RecordCount, see "Accessing and Retrieving Data" on
page 410.

For example, to inform the user when no records are found by the GetEmployees query, insert the following code
before displaying the data:

<cfif GetEmployees.RecordCount IS "0">
     No records match your search criteria. <BR>
<cfelse>

Do the following:

* Prefix RecordCount with the query name.

* Add a procedure after the cfif tag that displays a message to the user.

* Add a procedure after the cfelse tag to format the returned data.

* Follow the second procedure with a </cfif> tag end to indicate the end of the conditional code.


Return search results to users
1 Edit the actionpage.cfm page.

2 Change the page so that it appears as follows:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                             715
Requesting and Presenting Information




   <html>
  <head>
  <title>Retrieving Employee Data Based on Criteria from Form</title>
  </head>


  <body>
  <cfquery name="GetEmployees" datasource="cfdocexamples">
        SELECT Departmt.Dept_Name,
             Employee.FirstName,
             Employee.LastName,
             Employee.StartDate,
             Employee.Salary
        FROM Departmt, Employee
        WHERE Departmt.Dept_ID = Employee.Dept_ID
        <cfif isdefined("Form.Department")>
             AND Departmt.Dept_Name = <cfqueryparam value="#Form.Department#"
                   CFSQLType="CF_SQL_VARCHAR">
        </cfif>
        <cfif Form.LastName is not "">
             AND Employee.LastName = <cfqueryparam value="#Form.LastName#"
                   CFSQLType="CF_SQL_VARCHAR">
        </cfif>
  </cfquery>


  <cfif GetEmployees.recordcount is "0">
  No records match your search criteria. <br>
  Please go back to the form and try again.
  <cfelse>
  <h4>Employee Data Based on Criteria from Form</h4>
  <table>
  <tr>
  <th>First Name</th>
  <th>Last Name</th>
  <th>Salary</th>
  </tr>
  <cfoutput query="GetEmployees">
  <tr>
  <td>#FirstName#</td>
  <td>#LastName#</td>
  <td>#Salary#</td>
  </tr>
  </cfoutput>
  </cfif>
  </table>
  </body>
  </html>

3 Save the file.

4 Return to the form, enter search criteria, and submit the form.

5 If no records match the criteria you specified, the message appears.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      716
Requesting and Presenting Information




Returning results incrementally
You can use the cfflush tag to incrementally display long-running requests to the browser before a ColdFusion page
is fully processed. This tag lets you give the user quick feedback when it takes a long time to complete processing a
request. For example, when a request takes time to return results, you can use the cfflush tag to display the message,
"Processing your request -- please wait." You can also use it to incrementally display a long list as it gets retrieved.

The first time you use the cfflush tag on a page, it sends to the browser all of the HTML headers and any other
available HTML. Subsequent cfflush tags on the page send only the output that ColdFusion generated after the
previous flush.

You can specify an interval attribute to tell ColdFusion to flush the output each time that at least the specified
number of bytes become available. (The count does not include HTML headers and any data that is already available
when you make this call.) You can use the cfflush tag in a cfloop tag to incrementally flush data as it becomes
available. This format is useful when a query responds slowly with large amounts of data.

When you flush data, make sure that a sufficient amount of information is available, because some browsers do not
respond if you flush only a small amount. Similarly, if you use an interval attribute, set it for a reasonable size, such
as a few hundred bytes or more, but not many thousands of bytes.

Limitations of the cfflush tag: Because the cfflush tag sends data to the browser when it executes, it has several
limitations, including the following:

* Using any of the following tags or functions on a page anywhere after the cfflush tag can cause errors or
    unexpected results: cfcontent, cfcookie, cfform, cfheader, cfhtmlhead, cflocation, and SetLocale. (These
    tags and functions normally modify the HTML header, but cannot do so after a cfflush tag, because the cfflush
    tag sends the header.)

* Using the cfset tag to set a cookie anywhere on a page that has a cfflush tag does not set the cookie in the browser.

* Using the cfflush tag within the body of several tags, including cfsavecontent, cfqueryparam, and custom tags,
    can cause errors.

* If you save Client variables as cookies, any client variables that you set after a cfflush tag are not saved in the
    browser.

* You can catch cfflush errors, except Cookie errors, with a cfcatch tag. Catch cookie errors with a cfcatch
    type="Any" tag.


Example: using the cfloop tag and Rand function
The following example uses the cfloop tag and the Rand random number generating function to artificially delay the
generation of data for display. It simulates a situation in which it takes time to retrieve the first data and additional
information becomes available slowly.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               717
Requesting and Presenting Information




<html>
<head>
    <title>Your Magic numbers</title>
</head>


<body>
<h1>Your Magic numbers</h1>
<P>It will take us a little while to calculate your ten magic numbers.
It takes a lot of work to find numbers that truly fit your personality.
So relax for a minute or so while we do the hard work for you.</P>
<h2>We are sure you will agree it was worth the short wait!</h2>
<cfflush>


<cfflush interval=10>
<!--- Delay Loop to make is seem harder. --->
<cfloop index="randomindex" from="1" to="200000" step="1">
    <cfset random=rand()>
</cfloop>


<!--- Now slowly output 10 random numbers. --->
<cfloop index="Myindex" from="1" to="10" step="1">
    <cfloop index="randomindex" from="1" to="100000" step="1">
         <cfset random=rand()>
    </cfloop>
    <cfoutput>
         Magic number #Myindex# is:&nbsp;&nbsp;#RandRange(
    100000, 999999)#<br><br>
    </cfoutput>
</cfloop>
</body>
</html>


Reviewing the code
The following table describes the code and its function:


Code                                                     Description

<h2>We are sure you will agree it was worth the short     Sends the HTML header and all HTML output to the cfflush tag to the
wait!</h2>                                                user. This displays the explanatory paragraph and H2 tag contents.
<cfflush>

<cfflush interval=10>                                     Flushes additional data to the user whenever at least 10 bytes are
                                                          available.

<cfloop index="randomindex" from="1" to="200000"          Inserts an artificial delay by using the Rand function to calculate many
step="1">                                                 random numbers.
<cfset random=Rand()>
</cfloop>

<cfloop index="Myindex" from="1" to="10" step="1">        Generates and displays 10 random numbers. This code uses two
<cfloop index="randomindex" from="1" to="100000"          loops. The outer loop repeats ten times, once for each number to
step="1">
                                                          display. The inner loop uses the Rand function to create another delay
<cfset random=rand()>
                                                          by generating more (unused) random numbers. It then calls the
</cfloop>
<cfoutput>                                                RandRange function to generate a six-digit random number for
Magic number #Myindex# is:&nbsp;&nbsp;#RandRange          display.
(100000,999999)#<br><br>
</cfoutput>
</cfloop>




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   718
Requesting and Presenting Information




Dynamically populating list boxes
The code in "Creating a basic form" on page 704 hard-coded the form's list box options. Instead of manually entering
the information on a form, you can dynamically populate a list box with database fields. When you write code this way,
the form page automatically reflects the changes that you make to the database.

You use two tags to dynamically populate a list box:

* Use the cfquery tag to retrieve the column data from a database table.

* Use the cfselect tag with the query attribute to dynamically populate the options of this form control.


Dynamically populate a list box
1 Open the formpage.cfm page.

2 Modify the file so that it appears as follows:

    <html>
   <head>
   <title>Input form</title>
   </head>
   <body>
   <cfquery name="GetDepartments" datasource="cfdocexamples">
        SELECT DISTINCT Location
        FROM Departmt
   </cfquery>


   <!--- Define the action page in the form tag.
        The form variables pass to this page
        when the form is submitted --->


   <cfform action="actionpage.cfm" method="post">


   <!--- Text box. --->
   <p>
   First Name: <cfinput type="Text" name="FirstName" size="20" maxlength="35"><br>
   Last Name: <cfinput type="Text" name="LastName" size="20" maxlength="35"><br>
   Salary: <cfinput type="Text" name="Salary" size="10" maxlength="10">
   </p>


   <!--- List box. --->
   City
   <cfset optsize=getDepartments.recordcount + 1>
   <cfselect name="City" query="GetDepartments" value="Location" size="#optsize#">
        <option value="">Select All
   </cfselect>


   <!--- Radio buttons. --->
   <p>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   719
Requesting and Presenting Information




    Department:<br>
    <cfinput type="radio" name="Department" value="Training">Training<br>
    <cfinput type="radio" name="Department" value="Sales">Sales<br>
    <cfinput type="radio" name="Department" value="Marketing">Marketing<br>
    <cfinput type="radio" name="Department" value="HR">HR<br>
    </p>


    <!--- Check box. --->
    <p>
    Contractor? <cfinput type="checkbox" name="Contractor" value="Yes" checked>Yes
    </p>


    <!--- Reset button. --->
    <cfinput type="reset" name="ResetForm" value="Clear Form">


    <!--- Submit button. --->
    <cfinput type="submit" name="SubmitForm" value="Submit">
    </cfform>
    </body>
    </html>

3 Save the page as formpage.cfm.

4 View the formpage.cfm page in a browser.

    The changes that you just made appear in the form.

    Remember that you need an action page to submit values.


Reviewing the code
The following table describes the highlighted code and its function:


 Code                                                                Description

 <cfquery name="GetDepartments"                                      Gets the locations of all departments in the Departmt table.
 datasource="cfdocexamples">                                         The DISTINCT clause eliminates duplicate location names from
 SELECT DISTINCT Location
                                                                     the returned query results.
 FROM Departmt
 </cfquery>

 <cfset optsize=getDepartments.recordcount + 1>                      Sets the optsize variable to the number of entries to add
                                                                     dynamically to the selection list, plus one for the manually
                                                                     coded Select All option.

 <cfselect name="City" query="GetDepartments"                        Populates the City selection list from the Location column of
 value="Location" size="#optsize#">                                  the GetDepartments query. The control has one option for
 <option value="">Select All
                                                                     each row returned by the query.
 </cfselect>

                                                                     Adds an option that allows users to select all locations. If the
                                                                     user selects this option, the form value is an empty string. The
                                                                     action page must check for the empty string and handle it
                                                                     appropriately.



Creating dynamic check boxes and multiple-selection list boxes
When an HTML or CFML form contains a list of check boxes with the same name or a multiple-selection list box (that
is, a box in which users can select multiple items from the list), the user's entries are made available as a comma-
delimited list with the selected values. These lists can be useful for a wide range of input types.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       720
Requesting and Presenting Information




Note: If the user does not select a check box or make a selection from a list box, no variable is created. The cfinput and
cfupdate tags do not work correctly if there are no values. To prevent errors, make the form fields required, use dynamic
SQL, or use the cfparam tag to set a default value for the form field.


Check boxes
When you place a series of check boxes with the same name in a form, the variable that is created contains a comma-
delimited list of values. The values can be either numeric values or alphanumeric strings. These two types of values are
treated slightly differently.


Handling numeric values
Suppose you want a user to select one or more departments using check boxes. You then query the database to retrieve
detailed information on the selected departments. The code for a simple set of check boxes that lets the user select
departments looks like the following:

<cfinput type="checkbox"
     name="SelectedDepts"
     value="1">
     Training<br>


<cfinput type="checkbox"
     name="SelectedDepts"
     value="2">
     Marketing<br>


<cfinput type="checkbox"
     name="SelectedDepts"
     value="3">
     HR<br>


<cfinput type="checkbox"
     name="SelectedDepts"
     value="4">
     Sales<br>
</html>

The user sees the name of the department, but the value attribute of each check box is a number that corresponds to
the underlying database primary key for the department's record.

If the user checks the Marketing and Sales items, the value of the SelectedDepts form field is 2,4 and you use the
SelectedDepts value in the following SQL statement:

SELECT *
     FROM Departmt
     WHERE Dept_ID IN ( #Form.SelectedDepts# )

The ColdFusion server sends the following statement to the database:

SELECT *
     FROM Departmt
     WHERE Dept_ID IN ( 2,4 )


Handling string values
To search for a database field that contains string values (instead of numeric), modify the checkbox and cfquery
syntax to make sure that the string values are sent to the data source in single-quotation marks (').




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         721
Requesting and Presenting Information




The first example searched for department information based on a numeric primary key field called Dept_ID.
Suppose, instead, that the primary key is a database field called Dept_Name that contains string values. In that case,
your code for check boxes should look like the following:

<cfinput type="checkbox"
     name="SelectedDepts"
     value="Training">
     Training<br>


<cfinput type="checkbox"
     name="SelectedDepts"
     value="Marketing">
     Marketing<br>


<cfinput type="checkbox"
     name="SelectedDepts"
     value="HR">
     HR<br>


<cfinput type="checkbox"
     name="SelectedDepts"
     value="Sales">
     Sales<br>

If the user checked Marketing and Sales, the value of the SelectedDepts form field would be the list Marketing,Sales
and you use the following SQL statement:

SELECT *
     FROM Departmt
     WHERE Dept_Name IN
     (#ListQualify(Form.SelectedDepts,"'")#)

In SQL, all strings must be surrounded in single-quotation marks. The ListQualify function returns a list with the
specified qualifying character (here, a single-quotation mark) around each item in the list.

If you select the second and fourth check boxes in the form, the following statement gets sent to the database:

SELECT *
     FROM Departmt
     WHERE Dept_Name IN ('Marketing','Sales')


Multiple selection lists
A multiple-selection list box is defined by a select or cfselect tag with a multiple or multipe="yes" attribute
and a size attribute value greater than 1. ColdFusion treats the result when a user selects multiple choices from a
multiple-selection list box like the results of selecting multiple check boxes. The data made available to your page from
any multiple-selection list box is a comma-delimited list of the entries selected by the user; for example, a list box could
contain the four entries: Training, Marketing, HR, and Sales. If the user selects Marketing and Sales, the form field
variable value is Marketing, Sales.

You can use multiple-selection lists to search a database in the same way that you use check boxes.


Handling numeric values
Suppose you want the user to select departments from a multiple-selection list box. The query retrieves detailed
information on the selected departments, as follows:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         722
Requesting and Presenting Information




Select one or departments to get more information on:
<cfselect name="SelectDepts" multiple>
      <option value="1">Training
      <option value="2">Marketing
      <option value="3">HR
      <option value="4">Sales
</cfselect>

If the user selects the Marketing and Sales items, the value of the SelectDepts form field is 2,4. If this parameter is used
in the following SQL statement:

SELECT *
      FROM Departmt
      WHERE Dept_ID IN (#form.SelectDepts#)

The following statement is sent to the database:

SELECT *
      FROM Departmt
      WHERE Dept_ID IN (2,4)


Handling string values
Suppose you want the user to select departments from a multiple-selection list box. The database search field is a string
field. The query retrieves detailed information on the selected departments, as follows:

<cfselect name="SelectDepts" multiple>
      <option value="Training">Training
      <option value="Marketing">Marketing
      <option value="HR">HR
      <option value="Sales">Sales
</cfselect>

If the user selects the Marketing and Sales items, the SelectDepts form field value is Marketing,Sales.

Just as you did when using check boxes to search database fields containing string values, use the ColdFusion
ListQualify function with multiple-selection list boxes:

SELECT *
      FROM Departmt
      WHERE Dept_Name IN (#ListQualify(Form.SelectDepts,"'")#)

The following statement is sent to the database:

SELECT *
      FROM Departmt
      WHERE Dept_Name IN ('Marketing','Sales')




Building Dynamic Forms with cfform Tags

You can use the cfform tag to create rich, dynamic forms with sophisticated graphical controls, including several Java
applet or Flash controls. You can use these controls without writing a line of Java or Flash code.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                            723
Requesting and Presenting Information




Creating custom forms with the cfform tag
The cfform tag and its CFML subtags let you create dynamic forms in three formats:

HTML Generates standard HTML tags wherever possible, and uses applets or Flash for more complex controls, such
as grids, trees, and calendars. HTML lets you present a familiar appearance, but does not let you easily separate data
and presentation, or provide some of the more complex structures, such as Flash tabbed navigators or accordions, or
customized XML controls.

Flash Presents a modern, visually pleasing appearance. Flash format supports several controls, such as tabbed
navigators and accordions, that are not available in HTML. Flash forms are also browser-independent. In Flash format,
Flash Player works in all commonly used browsers on Windows and Macintosh systems, and in Netscape and Mozilla
on Linux.

XML Lets you specify an Extensible Stylesheet Language Transformation (XSLT) skin that converts the XML into
styled HTML output. Adobe ColdFusion provides several skins that you can use, and you can write your own custom
skins and support custom controls.

The cfform tag and its subtags also provide you with several methods for validating input data. For example, you can
perform the validation on the browser or on the server. You can check the data type, or you can mask data input.

Individual cfform tags have additional dynamic features. Several of the tags do not have HTML counterparts, and
others directly support dynamically populating the control from data sources. Also, the cfform tag preservedata
attribute retains user input in a form after the user submits the form, so the data reappears if the form gets redisplayed.

The information here describes features of the cfform tag and focuses on using several of the cfform child tags that
do not have HTML counterparts. For other features of ColdFusion forms that you create using the cfform tag, see the
following:

* "Validating Data" on page 743

* "Creating Forms in Flash" on page 765

* "Creating Skinnable XML Forms" on page 783


The cfform controls
The following table describes the ColdFusion controls that you use in forms created using the cfform tag. You can use
these tags only inside a cfform tag. Unless otherwise stated, these controls are supported in HTML, Flash, and XML
skinnable forms.


 Control              Description                                                                 For more information

 cfapplet             Embeds a custom Java applet in the form. Not supported in Flash format       "Embedding Java applets" on page 741.
                      forms.

 cfcalendar           Displays an interactive Flash calendar that can be included in an HTML       The cfcalendar tag in the CFML Reference
                      or Flash format form. ignored in XML skinable forms. The calendar lets a
                      user select a date for submission as a form variable.

 cfform               Creates a container control for organizing and formatting multiple form      "Creating Forms in Flash" on page 765,
                      controls. Used in the cfform tag body of Flash and XML skinable forms.       "Creating Skinnable XML Forms" on
                      Ignored in HTML forms.                                                       page 783

 cfformitem           Inserts a horizontal line, a vertical line, or formatted or unformatted text "Creating Forms in Flash" on page 765,
                      in a Flash form. Used in the cfform or cfformgroup tag body for Flash        "Creating Skinnable XML Forms" on
                      and XML forms. Ignored in HTML forms.                                        page 783

 cfgrid               Creates a Java applet or Flash data grid that you can populate from a        "Creating data grids with the cfgrid tag" on
                      query or by defining the contents of individual cells. You can also use      page 732
                      grids to insert, update, and delete records from a data source.




                                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                       724
Requesting and Presenting Information




 Control             Description                                                              For more information

 cfinput             Equivalent to the HTML input tag, with the addition of input validation. "Creating a basic form" on page 704

 cfselect            Displays a selection box. Equivalent to the HTML select tag, with the    "Building drop-down list boxes" on
                     addition of input validation.                                            page 731

 cfslider            Creates a Java applet-based control that lets users enter data by moving "Building slider bar controls" on page 732
                     a slider. Not supported in Flash format forms.

 cftextarea          Displays a text input area. Equivalent to the HTML textarea tag, with    The cftextarea tag in the CFML Reference
                     the addition of input validation.

 cftree              Creates a Java applet or Flash hierarchical tree-format control that can "Building tree controls with the cftree tag"
                     include graphical images for the different elements. Can also generate   on page 725
                     a ColdFusion structure that represents the tree data and attributes.



Preserving input data with the preservedata attribute
The cfformpreservedata attribute tells ColdFusion to continue displaying the user data in a form after the user
submits the form. Data is preserved in the cfinput, cfslider, cftextinput, and cftree controls and in cfselect
controls populated by queries. If you specify a default value for a control, and a user overrides that default in the form,
the user input is preserved.

You can retain data on the form when the same page contains the form and the form's action code; that is, the form
submits to itself. You can also retain the data if the action page has a copy of the form, and the control names are the
same in the forms on both pages. (The action page form need not be identical to the initial form. It can have more or
fewer elements than the initial page form; only the form elements with identical names on both pages keep their data.)

Note: The preservedata setting on the action page controls the preservation of the data.

For example, if you save this form as preserve.cfm, it continues to display any text that you enter after you submit it,
as follows:

<cfform action="preserve.cfm" preservedata="Yes">
      <p>Please enter your name:
      <cfinput type="Text" name="UserName" required="Yes"><p>
      <input type="Submit" name=""> <input type="RESET">
</cfform>


Usage notes for the preservedata attribute
When you use the preservedata attribute, follow these guidelines:

* In the cftree tag, the preservedata attribute causes the tree to expand to the previously selected element. For this
    to work correctly, set the completePath attribute to True.

* The preservedata attribute has no effect on a cfgrid tag. If you populate the control from a query, update the
    data source with the new data (typically by using a cfgridupdate tag) before redisplaying the grid. The grid then
    displays the updated database information.


Browser considerations
The applet-based versions of the cfgrid, cfslider, and cftree forms use JavaScript and Java to display their
content. To allow them to display consistently across a variety of browsers, these applets use the Java plug-in. As a
result, they are independent of the level of Java support provided by the browser.

ColdFusion downloads and installs the browser plug-in if necessary. Some browsers display a single permission dialog
box asking you to confirm the plug-in installation. Other browsers, like older versions of Netscape, require you to
navigate some simple option windows.



                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       725
Requesting and Presenting Information




Because the controls use JavaScript to return data to ColdFusion, if you disable JavaScript in your browser, it cannot
properly run forms that contain these controls. In that case, the controls still display, but data return and validation
does not work and you can receive a JavaScript error.

Because Java is handled by the plug-in and not directly by the browser, disabling Java execution in the browser does
not affect the operation of the controls. If for some other reason, however, the browser is unable to render the controls
as requested, a "not supported" message appears in place of the control.

You can use the cfform tag's notsupported attribute to specify an alternative error message.

You can avoid browser Java and JavaScript issues with the cfgrid and cftree controls by using the Flash format
versions of these controls. These controls work on Windows, Mac OS X, and Linux, and do not rely on Java support.
There is no Flash version of the cfslider control, and there is no applet version of the cfcalendar control.


Building tree controls with the cftree tag
The cftree tag lets you display hierarchical information within a form in a space-saving collapsible tree populated
from data source queries. To build a tree control with the cftree tag, you use individual cftreeitem tags to populate
the control.

You can create trees in three formats:

Applet Creates a Java applet that the client must download. Downloading an applet takes time; therefore, using the
cftree tag can be slightly slower than using an HTML form element to retrieve the same information. In addition,
browsers must be Java-enabled for the cftree tag to work properly.

Flash Generates a Flash control that you can include in an HTML or Flash form. For more information on Flash
Forms see "Creating Forms in Flash" on page 765.

Object Creates a hierarchical ColdFusion structure that represents the tree data and many of the cftree and
cftreeitem attributes.

The different formats support different sets of features and attributes. The information here discusses general
techniques that apply to all three formats, and indicates any techniques that do not apply to a specific format. It uses
applet format for all examples, which use applet-specific attributes. For details on the features and attributes supported
in each format, see the cftree entry in the CFML Reference.


Create and populate a tree control from a query
1 Create a ColdFusion page with the following content:

    <cfquery name="engquery" datasource="cfdocexamples">
        SELECT FirstName || ' ' || LastName AS FullName
        FROM Employee
   </cfquery>
   <cfform name="form1" action="submit.cfm">
   <cftree name="tree1"
        required="Yes"
        hscroll="No">
        <cftreeitem value="FullName"
              query="engquery"
              queryasroot="Yes"
              img="folder,document">
   </cftree>
   </cfform>

2 Save the page as tree1.cfm and view it in your browser.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 726
Requesting and Presenting Information




Reviewing the code
The following table describes the highlighted code and its function:


Code                                                   Description

<cftree name="tree1"                                   Creates a tree and names it tree1.

required="Yes"                                         Specifies that a user must select an item in the tree.

hscroll="No"                                           Does not allow horizontal scrolling.

<cftreeitem value="FullName" query="engquery"          Creates an item in the tree and puts the results of the query named engquery
                                                       in it. Because this tag uses a query, it puts one item on the tree per query
                                                       entry.

queryasroot="Yes"                                      Specifies the query name as the root level of the tree control.

img="folder,document"                                  Uses the folder and document images that ship with ColdFusion in the tree
                                                       structure.

                                                       When populating a cftree tag with data from a cfquery tag, you can specify
                                                       images or filenames for each level of the tree as a comma-separated list.



Grouping output from a query
In a query that you display using a cftree control, to organize your employees by department, separate column names
with commas in the cftreeitemvalue attribute.


Organize the tree based on ordered results of a query
1 Create a ColdFusion page named tree2.cfm with the following content:

   <!--- CFQUERY with an ORDER BY clause. --->
   <cfquery name="deptquery" datasource="cfdocexamples">
        SELECT Dept_ID, FirstName || ' ' || LastName
        AS FullName
        FROM Employee
        ORDER BY Dept_ID
   </cfquery>


   <!--- Build the tree control. --->
   <cfform name="form1" action="submit.cfm">


   <cftree name="tree1"
        hscroll="No"
        border="Yes"
        height="350"
        required="Yes">


   <cftreeitem value="Dept_ID, FullName"
        query="deptquery"
        queryasroot="Dept_ID"
        img="computer,folder,document"
        imgopen="computer,folder"
        expand="yes">


   </cftree>
   <br>
   <br><input type="Submit" value="Submit">
   </cfform>




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   727
Requesting and Presenting Information




2 Save the page and view it in your browser.


Reviewing the code
The following table describes the highlighted code and its function


Code                                              Description

ORDER BY Dept_ID                                  Orders the query results by department.

<cftreeitem value="Dept_ID,FullName"              Populates the tree with the department ID, and under each department, the full
                                                  name for each employee in the department.

queryasroot="Dept_ID"                             Labels the root "Dept_ID".

img="computer,folder,document"                    Uses the ColdFusion supplied computer image for the root level, folder image for the
                                                  department IDs, and document for the names, independent of whether any level is
imgopen="computer,folder"
                                                  expanded (open) or collapsed. The imgopen attribute has only two items, because
                                                  the employee names can never be open.


The cftreeitem comma-separated value, img, and imgopen attributes correspond to the tree level structure. In
applet format, if you omit the img attribute, ColdFusion uses the folder image for all levels in the tree; if you omit the
imgopen attribute, ColdFusion uses the folder image for all expanded levels in the tree. Flash format ignores the img
and imgopen attributes and always uses folders for levels with children and documents for nodes without children.


The cftree form variables
The cftree tag lets you force a user to select an item from the tree control by setting the required attribute to Yes.
With or without the required attribute, ColdFusion passes two form variables to the application page specified in the
cfformaction attribute:

* Form.treename.path        Returns the complete path of the user selection, in the form:
   [root]\node1\node2\node_n\value

* Form.treename.node         Returns the node of the user selection.

To return the root part of the path, set the completepath attribute of the cftree tag to Yes; otherwise, the path value
starts with the first node. If you specify a root name for a tree item using the queryasroot tag, that value is returned
as the root. If you do not specify a root name, ColdFusion returns the query name as the root. If there is no query name,
ColdFusion returns the tree name as the root.

In the previous example, if the user selects the name "John Allen" in the tree, ColdFusion returns the following form
variables:

Form.tree1.path = 3\John Allen
Form.tree1.node = John Allen

The deptquery root does not appear in the path, because the cftree tag does not specify completePath="Yes". You
can specify the character used to delimit each element of the path form variable in the cftreedelimiter attribute.
The default is a backslash character (\).


Input validation
Although the cftree tag does not include a validate attribute, you can use the required attribute to force a user to
select an item from the tree control. In addition, you can use the onValidate attribute to specify your own JavaScript
code to perform validation.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   728
Requesting and Presenting Information




Structuring tree controls
Tree controls built with the cftree tag can be complex. Knowing how to specify the relationship between multiple
cftreeitem entries helps you handle the most complex cftree constructs.


Creating a one-level tree control
The following example consists of a single root and some individual items:

<cfquery name="deptquery" datasource="cfdocexamples">
     SELECT Dept_ID, FirstName || ' ' || LastName
     AS FullName
     FROM Employee
     ORDER BY Dept_ID
</cfquery>


<cfform name="form1" action="submit.cfm">
     <cftree name="tree1">
          <cftreeitem value="FullName"
          query="deptquery"
          queryasroot="Department">
          img="folder,document">
     </cftree>
<br>
<cfinput type="submit" value="Submit">
</cfform>


Creating a multilevel tree control
When populating a cftree control, you create the multilevel structure of the tree by specifying a parent for each item
in the tree. The parent attribute of the cftreeitem tag allows your cftree tag to show relationships between
elements in the tree.

In this example, every cftreeitem tag, except the top level Divisions, specifies a parent. For example, the cftreeitem
tag specifies Divisions as its parent.

The following code populates the tree directly, not from a query:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        729
Requesting and Presenting Information




<cfform name="form2" action="cfform_submit.cfm">
<cftree name="tree1" hscroll="No" vscroll="No"
     border="No">
     <cftreeitem value="Divisions">
     <cftreeitem value="Development"
          parent="Divisions" img="folder">
     <cftreeitem value="Product One"
          parent="Development" img="document">
     <cftreeitem value="Product Two"
          parent="Development">
     <cftreeitem value="GUI"
          parent="Product Two" img="document">
     <cftreeitem value="Kernel"
          parent="Product Two" img="document">
     <cftreeitem value="Product Three"
          parent="Development" img="document">
     <cftreeitem value="QA"
          parent="Divisions" img="folder">
     <cftreeitem value="Product One"
          parent="QA" img="document">
     <cftreeitem value="Product Two"
          parent="QA" img="document">
     <cftreeitem value="Product Three"
          parent="QA" img="document">
     <cftreeitem value="Support"
          parent="Divisions" img="fixed">
     <cftreeitem value="Product Two"
          parent="Support" img="document">
     <cftreeitem value="Sales"
          parent="Divisions" img="computer">
     <cftreeitem value="Marketing"
          parent="Divisions" img="remote">
     <cftreeitem value="Finance"
          parent="Divisions" img="element">
</cftree>
</cfform>


Image names in a cftree tag
Note: The information here applies to applet format trees. In Flash, you cannot control the tree icons. Flash uses open and
closed folders and documents as the icons. In object format, the image information is preserved in fields in the object
structure.

The default image displayed in a tree is a folder. However, you can use the img attribute of the cftreeitem tag to
specify a different image.

When you use the img attribute, ColdFusion displays the specified image beside the tree items when they are not open.
When you use the imgopen attribute, ColdFusion displays the specified image beside the tree items when they are open
(expanded). You can specify a built-in ColdFusion image name, the file path to an image file, or the URL of an image
of your choice, such as http://localhost/Myapp/Images/Level3.gif. You cannot use a custom image in Flash format. As
a general rule, make the height of your custom images less than 20 pixels.

When populating a cftree control with data from a cfquery tag, you can use the img attribute of cftreeitem tag to
specify images or filenames for each level of the tree as a comma-separated list.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       730
Requesting and Presenting Information




The following are the ColdFusion built-in image names:

* computer

* document

* element

* folder

* floppy

* fixed

* remote

Note: In applet format, you can also control the tree appearance by using the cftree tag lookAndFeel attribute to
specify a Windows, Motif, or Metal look.


Embedding URLs in a cftree tag
The href attribute in the cftreeitem tag lets you designate tree items as links. To use this feature in a cftree control,
you define the destination of the link in the href attribute of the cftreeitem tag. The URL for the link can be a relative
URL or an absolute URL, as in the following examples.


Embed links in a cftree control
1 Create a ColdFusion page named tree3.cfm with the following contents:

   <cfform action="submit.cfm">
   <cftree name="oak"
        highlighthref="Yes"
        height="100"
        width="200"
        hspace="100"
        vspace="6"
        hscroll="No"
        vscroll="No"
        border="No">
        <cftreeitem value="Important Links">
        <cftreeitem value="Adobe Home"
             parent="Important Links"
             img="document"
             href="http://www.adobe.com">
        <cftreeitem value="ColdFusion Developer Center"
             parent="Important Links"
             img="document"
             href="http://www.adobe.com/devnet/coldfusion/">
   </cftree>
   </cfform>

2 Save the page and view it in your browser.


Reviewing the code
The following table describes the highlighted code and its function:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                              731
Requesting and Presenting Information




 Code                                                Description

 href="http://www.adobe.com">                        Makes the node of the tree a link.

 href="http://www.adobe.com/devnet/mx/coldfu         Makes the node of the tree a link.
 sion/">
                                                     Although this example does not show it, the href attribute can refer to the
                                                     name of a column in a query if that query populates the tree item.



Specifying the tree item in the URL
When a user clicks a tree item to link to a URL, the cftreeItemKey variable, which identifies the selected value, is
appended to the URL in the following form:

http://myserver.com?CFTREEITEMKEY=selected_item_value_attribute

If the value attribute includes spaces, ColdFusion replaces the spaces with plus characters (+).

Automatically passing the name of the selected tree item as part of the URL makes it easy to implement a basic "drill
down" application that displays additional information based on the selection. For example, if the specified URL is
another ColdFusion page, it can access the selected value as the variable URL.CFTREEITEMKEY.

To disable this behavior, set the appendkey attribute in the cftree tag to no.


Building drop-down list boxes
The drop-down list box that you can create in a cfform tag with a cfselect tag is like the HTML select tag.
However, the cfselect tag gives you more control over user inputs, provides error handling, and, most importantly,
lets you automatically populate the selection list from a query.

You can populate the drop-down list box from a query, or using lists of option elements created by the option tag.
The syntax for the option tag with the cfselect tag is the same as for the HTML option tag.

When you populate a cfselect tag with data from a query, you only need to specify the name of the query that is
supplying data for the cfselect tag and the query column name for each list element to display.


Populate a drop-down list box with query data using the cfselect tag
1 Create a ColdFusion page with the following content:

    <cfquery name="getNames"
         datasource="cfdocexamples">
         SELECT * FROM Employee
    </cfquery>


    <cfform name="Form1" action="submit.cfm">


         <cfselect name="employees"
              query="getNames"
              value="Emp_ID"
              display="FirstName"
              required="Yes"
              multiple="Yes"
              size="8">
         </cfselect>


         <br><input type="Submit" value="Submit">
    </cfform>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           732
Requesting and Presenting Information




2 Save the file as selectbox.cfm and view it in your browser.

Because the tag includes the multiple attribute, the user can select multiple entries in the list box. Also, because the
value tag specifies Emp_ID, the primary key for the Employee table, Employee IDs (not first names) get passed in the
Form.Employee variable to the application page specified in the cfformaction attribute.

You can use a query to create a two-level hierarchical list grouped by one of the query columns. For an example of this
use, see the example for the cfselect entry in the CFML Reference.


Building slider bar controls
You can use the cfslider control in a cfform tag to create a slider control and define a variety of characteristics,
including label text, label font name, size, boldface, italics, and color, and slider range, positioning, and behavior. Slider
bars are useful because they are highly visual and users can only enter valid values. The cfslider tag is not supported
in Flash format forms.


Create a slider control
1 Create a ColdFusion page with the following content:

    <cfform name="Form1" action="submit.cfm">
         <cfslider name="myslider"
              bgcolor="cyan"
              bold="Yes"
              range="0,1000"
              scale="100"
              value="600"
              fontsize="14"
              label="Slider %value%"
              height="60"
              width="400">
   </cfform>

2 Save the file as slider.cfm and view it in your browser.

To get the value of the slider in the action page, use the variable Form.slider_name; in this case, Form.myslider.


Creating data grids with the cfgrid tag
The cfgrid tag creates a cfform grid control that resembles a spreadsheet table and can contain data populated from
a cfquery tag or from other sources of data. As with other cfform tags, the cfgrid tag offers a wide range of data
formatting options, as well as the option of validating user selections with a JavaScript validation script.

You can also perform the following tasks with a cfgrid tag:

* Sort data in the grid alphanumerically.

* Update, insert, and delete data.

* Display images in the grid.

Note: Flash format grids support a subset of the features available in applet format grids. For details on features supported
in each format, see the cfgrid tag in the CFML Reference.

Users can sort the grid entries in ascending order by double-clicking any column header. Double-clicking again sorts
the grid in descending order. In applet format, you can also add sort buttons to the grid control.

When users select grid data and submit the form, ColdFusion passes the selection information as form variables to the
application page specified in the cfformaction attribute.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        733
Requesting and Presenting Information




Just as the cftree tag uses the cftreeitem tag, the cfgrid tag uses the cfgridcolumn and cfgridrow tags. You can
define a wide range of row and column formatting options, as well as a column name, data type, selection options, and
so on. You use the cfgridcolumn tag to define individual columns in the grid or associate a query column with a grid
column.

Use the cfgridrow tag to define a grid that does not use a query as the source for row data. If a query attribute is
specified in the cfgrid tag, the cfgridrow tags are ignored.

The cfgrid tag provides many attributes that control grid behavior and appearance. Only the most important of these
attributes are described here. For detailed information on these attributes, see the cfgrid tag in the CFML Reference.


Working with a data grid and entering data
The following image shows an example applet format grid created using a cfgrid tag.

The following table describes some navigating tips:


Action                              Procedure

Sorting grid rows                   Double-click the column header to sort a column in ascending order. Double-click again to sort the
                                    rows in descending order.

Rearranging columns                 Click any column heading and drag the column to a new position.

Determining editable grid areas     When you click an editable cell, it is surrounded by a yellow box.

Determining noneditable grid areas  When you click a cell (or row or column) that you cannot edit, its background color changes. The
                                    default color is salmon pink.

Editing a grid cell                 Double-click the cell. Press Return when you finish entering the data.

Deleting a row                      Click any cell in the row and click the Delete button. (Not available in Flash format grids.)

Inserting a row                     Click the Insert button. An empty row appears at the bottom of the grid. To enter a value in each cell,
                                    double-click the cell, enter the value, and click Return. (Not available in Flash format grids.)



Populate a grid from a query
1 Create a ColdFusion page named grid1.cfm with the following contents:

    <cfquery name="empdata" datasource="cfdocexamples">
         SELECT * FROM Employee
   </cfquery>


   <cfform name="Form1" action="submit.cfm" >
         <cfgrid name="employee_grid" query="empdata"
                    selectmode="single">
                <cfgridcolumn name="Emp_ID">
                <cfgridcolumn name="LastName">
                <cfgridcolumn name="Dept_ID">
         </cfgrid>
         <br>
         <cfinput name="submitit" type="Submit" value="Submit">
   </cfform>

   Note: Use the cfgridcolumndisplay="No" attribute to hide columns that you want to include in the grid but not
   expose to an end user. You typically use this attribute to include columns such as the table's primary key column in
   the results returned by the cfgrid tag.

2 Save the file and view it in your browser.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               734
Requesting and Presenting Information




Reviewing the code
The following table describes the highlighted code and its function:


 Code                                                            Description

 <cfgrid name="employee_grid" query="empdata"                    Creates a grid named employee_grid and populate it with the
                                                                 results of the query empdata.

                                                                 If you specify a cfgrid tag with a query attribute defined and no
                                                                 corresponding cfgridcolumn attributes, the grid contains all the
                                                                 columns in the query.

 selectmode="single">                                            Allows the user to select only one cell; does not allow editing.
                                                                 Other modes are row, column, and edit.

 <cfgridcolumn name="Emp_ID">                                    Puts the contents of the Emp_ID column in the query results in
                                                                 the first column of the grid.

 <cfgridcolumn name="LastName">                                  Puts the contents of the LastName column in the query results
                                                                 in the second column of the grid.

 <cfgridcolumn name="Dept_ID">                                   Puts the contents of the Dept_ID column in the query results in
                                                                 the third column of the grid.



Creating an editable grid
You can build grids to allow users to edit data within them. Users can edit individual cell data, as well as insert, update,
or delete rows. To enable grid editing, you specify selectmode="edit" in the cfgrid tag.

You can let users add or delete grid rows by setting the insert or delete attributes in the cfgrid tag to Yes. Setting
the insert and delete attribute to Yes causes the cfgrid tag to display Insert and Delete buttons as part of the grid.

You can use a grid in two ways to change your ColdFusion data sources:

* Create a page to which you pass the cfgrid form variables. In that page, perform cfquery operations to update
   data source records based on the form values returned by the cfgrid tag.

* Pass grid edits to a page that includes the cfgridupdate tag, which automatically extracts the form variable values
   and passes that data directly to the data source.

Using the cfquery tag gives you complete control over interactions with your data source. The cfgridupdate tag
provides a much simpler interface for operations that do not require the same level of control.


Controlling cell contents
You can control the data that a user can enter into a cfgrid cell in the following ways:

* By default, a cell is not editable. Use the cfgrid attribute selectmode="edit" to edit cell contents.

* Use the cfgridcolumntype attribute to control sorting order, to make the fields check boxes, or to display an
   image.

* Use the cfgridcolumnvalues attribute to specify a drop-down list of values from which the user can choose. You
   can use the valuesDisplay attribute to provide a list of items to display that differs from the actual values that you
   enter in the database. You can use the valuesDelimiter attribute to specify the separator between values in the
   valuesvaluesDisplay lists.

* Although the cfgrid tag does not have a validate attribute, it does have an onValidate attribute that lets you
   specify a JavaScript function to perform validation.

For more information on controlling the cell contents, see the attribute descriptions for the cfgridcolumn tag in the
CFML Reference.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        735
Requesting and Presenting Information




How user edits are returned
When a user inserts or deletes a row in a grid or changes any cells in a row and submits the grid, ColdFusion creates
the following arrays as Form variables:


 Array name                                Description

 gridname.colname                          Stores the new values of inserted, deleted, or updated cells. (Entries for deleted cells contain
                                           empty strings.)

 gridname.Original.colname                 Stores the original values of inserted, deleted, or updated cells.

 gridname.RowStatus.Action                 Stores the type of change made to the grid rows: D for delete, I for insert, or U for update.


Note: The periods in these names are not structure separators; they are part of the text of the array name.

ColdFusion creates a gridname.colname array and a gridname.Original.colname array for each column in the grid. For
each inserted, deleted, or changed row in the grid, ColdFusion creates a row in each of these arrays.

For example, the following arrays are created if you update a cfgrid tag called mygrid that consists of two displayable
columns (col1, col2) and one hidden column (col3):

Form.mygrid.col1
Form.mygrid.col2
Form.mygrid.col3
Form.mygrid.original.col1
Form.mygrid.original.col2
Form.mygrid.original.col3
Form.mygrid.RowStatus.Action

The value of the array index increments for each row that is added, deleted, or changed, and does not indicate a grid
row number. All rows for a particular change have the same index in all arrays. Unchanged rows do not have entries
in the arrays.

If the user changes a single cell in col2, the following array elements contain the edit operation, the edited cell value,
and the original cell value:

Form.mygrid.RowStatus.Action[1]
Form.mygrid.col2[1]
Form.mygrid.original.col2[1]

If the user changes the values of the cells in col1 and col3 in one row and the cell in col2 in another row, the information
about the original and changed values is in the following array entries:

Form.mygrid.RowStatus.Action[1]
Form.mygrid.col1[1]
Form.mygrid.original.col1[1]
Form.mygrid.col3[1]
Form.mygrid.original.col3[1]
Form.mygrid.RowStatus.Action[2]
Form.mygrid.col2[2]
Form.mygrid.original.col2[2]

The remaining cells in the arrays (for example, Form.mygrid.col2[1] and Form.mygrid.original.col2[1]) have the
original, unchanged values.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  736
Requesting and Presenting Information




Example: editing data in a grid
The following example creates an editable grid. For code brevity, the example handles only three of the fields in the
Employee table. A more realistic example would include, at a minimum, all seven table fields. It can also hide the
contents of the Emp_ID column or display the Department name (from the Departmt table), instead of the
Department ID.


Create the editable grid
1 Create a ColdFusion page with the following content:

   <cfquery name="empdata" datasource="cfdocexamples">
        SELECT * FROM Employee
  </cfquery>


  <cfform name="GridForm"
        action="handle_grid.cfm">


        <cfgrid name="employee_grid"
             height=425
             width=300
             vspace=10
             selectmode="edit"
             query="empdata"
             insert="Yes"
             delete="Yes">


             <cfgridcolumn name="Emp_ID"
                   header="Emp ID"
                   width=50
                   headeralign="center"
                   headerbold="Yes"
                   select="No">


             <cfgridcolumn name="LastName"
                   header="Last Name"
                   width=100
                   headeralign="center"
                   headerbold="Yes">


             <cfgridcolumn name="Dept_ID"
                   header="Dept"
                   width=35
                   headeralign="center"
                   headerbold="Yes">


        </cfgrid>
        <br>
        <cfinput name="submitit" type="Submit" value="Submit">
  </cfform>

2 Save the file as grid2.cfm.

3 View the results in your browser.


Reviewing the code
The following table describes the code and its function:



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         737
Requesting and Presenting Information




Code                                       Description

<cfgrid name="employee_grid"               Populates a cfgrid control with data from the empdata query. Selecting a grid cell enables
 height=425                                you to edit it. You can insert and delete rows. The grid is 425 X 300 pixels and has 10 pixels
 width=300
                                           of space above and below it.
 vspace=10
 selectmode="edit"
 query="empdata"
 insert="Yes"
 delete="Yes">

<cfgridcolumn name="Emp_ID"                Creates a 50-pixel wide column for the data in the Emp_ID column of the data source.
 header="Emp ID"                           Centers a header named Emp ID and makes it bold.
 width=50
 headeralign="center"                      Does not allow users to select fields in this column for editing. Since this field is the table's
 headerbold="Yes"                          primary key, users should not be able to change it for existing records, and the DBMS
 select="No">
                                           should generate this field as an autoincrement value.

<cfgridcolumn name="LastName"              Creates a 100-pixel wide column for the data in the LastName column of the data source.
 header="Last Name"                        Centers a header named Last Name and makes it bold.
 width=100
 headeralign="center"
 headerbold="Yes">

<cfgridcolumn name="Dept_ID"               Creates a 35-pixel wide column for the data in the Dept_ID column of the data source.
 header="Dept"                             Centers a header named Dept and makes it bold.
 width=35
 headeralign="center"
 headerbold="Yes">



Updating the database with the cfgridupdate tag
The cfgridupdate tag provides a simple mechanism for updating the database, including inserting and deleting
records. It can add, update, and delete records simultaneously. It is convenient because it automatically handles
collecting the cfgrid changes from the various form variables, and generates appropriate SQL statements to update
your data source.

In most cases, use the cfgridupdate tag to update your database. However, this tag does not provide the complete
SQL control that the cfquery tag provides. In particular, the cfgridupdate tag has the following characteristics:

* You can update only a single table.

* Rows are deleted first, then rows are inserted, then any changes are made to existing rows. You cannot modify the
   order of changes.

* Updating stops when an error occurs. It is possible that some database changes are made, but the tag does not
   provide any information on them.


Update the data source with the cfgridupdate tag
1 Create a ColdFusion page with the following contents:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 738
Requesting and Presenting Information




   <html>
  <head>
        <title>Update grid values</title>
  </head>
  <body>


  <h3>Updating grid using cfgridupdate tag.</h3>


  <cfgridupdate grid="employee_grid"
        datasource="cfdocexamples"
        tablename="Employee">


  Click <a href="grid2.cfm">here</a> to display updated grid.


        </body>
  </html>

2 Save the file as handle_grid.cfm.

3 View the grid2.cfm page in your browser, change the grid, and then submit them.

Note: To update a grid cell, modify the cell contents, and then press Return.


Reviewing the code
The following table describes the highlighted code and its function:


Code                                                    Description

<cfgridupdate grid="employee_grid"                      Updates the database from the Employee_grid grid.

datasource="cfdocexamples"                              Updates the cfdocexamples data source.

tablename="Employee"                                    Updates the Employee table.



Updating the database with the cfquery tag
You can use the cfquery tag to update your database from the cfgrid changes. This tag provides you with full control
over how the updates are made and lets you handle any errors that arise.


Update the data source with the cfquery tag
1 Create a ColdFusion page with the following content:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                           739
Requesting and Presenting Information




   <html>
  <head>
        <title>Catch submitted grid values</title>
  </head>
  <body>


  <h3>Grid values for Form.employee_grid row updates</h3>


  <cfif isdefined("Form.employee_grid.rowstatus.action")>


        <cfloop index = "counter" from = "1" to =
             #arraylen(Form.employee_grid.rowstatus.action)#>


             <cfoutput>
                   The row action for #counter# is:
                   #Form.employee_grid.rowstatus.action[counter]#
                   <br>
             </cfoutput>


             <cfif Form.employee_grid.rowstatus.action[counter] is "D">
                   <cfquery name="DeleteExistingEmployee"
                        datasource="cfdocexamples">
                        DELETE FROM Employee
                        WHERE Emp_ID=<cfqueryparam
                                      value="#Form.employee_grid.original.Emp_ID[counter]#"
                                      CFSQLType="CF_SQL_INTEGER" >
                   </cfquery>


             <cfelseif Form.employee_grid.rowstatus.action[counter] is "U">
                   <cfquery name="UpdateExistingEmployee"
                        datasource="cfdocexamples">
                        UPDATE Employee
                        SET
                              LastName=<cfqueryparam
                                      value="#Form.employee_grid.LastName[counter]#"
                                      CFSQLType="CF_SQL_VARCHAR" >,
                              Dept_ID=<cfqueryparam
                                      value="#Form.employee_grid.Dept_ID[counter]#"
                                      CFSQLType="CF_SQL_INTEGER" >
                        WHERE Emp_ID=<cfqueryparam
                              value="#Form.employee_grid.original.Emp_ID[counter]#"
                              CFSQLType="CF_SQL_INTEGER">
                   </cfquery>




                                           

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          740
Requesting and Presenting Information




             <cfelseif Form.employee_grid.rowstatus.action[counter] is "I">
                   <cfquery name="InsertNewEmployee"
                        datasource="cfdocexamples">
                        INSERT into Employee (LastName, Dept_ID)
                        VALUES (<cfqueryparam
                              value="#Form.employee_grid.LastName[counter]#"
                                      CFSQLType="CF_SQL_VARCHAR" >,
                                   <cfqueryparam value="#Form.employee_grid.Dept_ID[counter]#"
                                      CFSQLType="CF_SQL_INTEGER" >)
                   </cfquery>


             </cfif>
        </cfloop>
  </cfif>


  Click <a href="grid2.cfm">here</a> to display updated grid.


  </body>
  </html>

2 Rename your existing handle_grid.cfm file as handle_grid2.cfm to save it, and then save this file as
  handle_grid.cfm.

3 View the grid2.cfm page in your browser, change the grid, and then submit them.


Reviewing the code
The following table describes the code and its function:


Code                                                               Description

<cfif isdefined("Form.employee_grid.rowstatus.action")>            If there is an array of edit types, changes the table.
<cfloop index = "counter" from = "1" to =                          Otherwise, does nothing. Loops through the remaining
#arraylen(Form.employee_grid.rowstatus.action)#>
                                                                   code once for each row to be changed. The counter
                                                                   variable is the common index into the arrays of change
                                                                   information for the row being changed.

<cfoutput>                                                         Displays the action code for this row: U for update, I for
The row action for #counter# is:                                   insert, or D for delete.
#Form.employee_grid.rowstatus.action[counter]#
<br>
</cfoutput>

<cfif Form.employee_grid.rowstatus.action[counter] is "D">         If the action is to delete a row, generates a SQL DELETE
<cfquery name="DeleteExistingEmployee"                             query specifying the Emp_ID (the primary key) of the
datasource="cfdocexamples">
                                                                   row to be deleted.
DELETE FROM Employee
WHERE Emp_ID=<cfqueryparam
value="#Form.employee_grid.original.Emp_ID[counter]#"
CFSQLType="CF_SQL_INTEGER" >
</cfquery>




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               741
Requesting and Presenting Information




 Code                                                                   Description

 <cfelseif Form.employee_grid.rowstatus.action[counter] is "U">         Otherwise, if the action is to update a row, generates a
 <cfquery name="UpdateExistingEmployee"                                 SQL UPDATE query to update the LastName and
 datasource="cfdocexamples">
                                                                        Dept_ID fields for the row specified by the Emp_ID
 UPDATE Employee
                                                                        primary table key.
 SET
 LastName=<cfqueryparam
 value="#Form.employee_grid.LastName[counter]#"
 CFSQLType="CF_SQL_VARCHAR" >,
 Dept_ID=<cfqueryparam
 value="#Form.employee_grid.Dept_ID[counter]#"
 CFSQLType="CF_SQL_INTEGER" >
 WHERE Emp_ID=<cfqueryparam
 value="#Form.employee_grid.original.Emp_ID[counter]#"
 CFSQLType="CF_SQL_INTEGER">
 </cfquery>

 <cfelseif Form.employee_grid.rowstatus.action[counter] is "I">         Otherwise, if the action is to insert a row, generates a
 <cfquery name="InsertNewEmployee"                                      SQL INSERT query to insert the employee's last name
 datasource="cfdocexamples">
                                                                        and department ID from the grid row into the database.
 INSERT into Employee (LastName, Dept_ID)
                                                                        The INSERT statement assumes that the DBMS
 VALUES (<cfqueryparam
 value="#Form.employee_grid.LastName[counter]#"                         automatically increments the Emp_ID primary key. If
 CFSQLType="CF_SQL_VARCHAR" >,                                          you use the version of the cfdocexamples database that
 <cfqueryparam value="#Form.employee_grid.Dept_ID[counter]#"            is provided for UNIX installations, the record is inserted
 CFSQLType="CF_SQL_INTEGER" >)                                          without an Emp_ID number.
 </cfquery>

 </cfif>                                                                Closes the cfif tag used to select among deleting,
 </cfloop>                                                              updating, and inserting.
 </cfif>
                                                                        Closes the loop used for each row to be changed.

                                                                        Closes the cfif tag that surrounds all the active code.



Embedding Java applets
The cfapplet tag lets you embed Java applets either on a ColdFusion page or in a cfform tag. To use the cfapplet
tag, first register your Java applet using the ColdFusion Administrator Java Applets page (under Extensions). In the
ColdFusion Administrator, you define the interface to the applet, encapsulating it so that each invocation of the
cfapplet tag is simple.

The cfapplet tag within a form offers several advantages over using the HTML applet tag:

* Return values: The cfapplet tag requires a form field name attribute, so you can avoid coding additional
   JavaScript to capture the applet's return values. You can reference return values like any other ColdFusion form
   variable: Form.variablename.

* Ease of use: The applet's interface is defined in the ColdFusion Administrator, so each instance of the cfapplet
   tag in your pages only needs to reference the applet name and specify a form variable name.

* Parameter defaults: ColdFusion uses the parameter value pairs that you defined in the ColdFusion Administrator.
   You can override these values by specifying parameter value pairs in the cfapplet tag.

When an applet is registered, you enter just the applet source and the form variable name:

<cfapplet appletsource="Calculator"name="calc_value">

By contrast, with the HTML applet tag, you must declare all the applet's parameters every time you want to use it in
a ColdFusion page.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  742
Requesting and Presenting Information




Registering a Java applet
Before you can use a Java applet in your ColdFusion pages, register the applet in the ColdFusion Administrator.

1 Open the ColdFusion Administrator by clicking the Administrator icon in the ColdFusion Program group and
   entering the Administrator password.

2 Under Extensions, click Java Applets.

   The Java Applets page appears.

3 Click the Register New Applet button.

   The Add/Edit Applet page appears.

4 Enter options in the applet registration fields, as described in the ColdFusion Administrator online help. Use the
   Add button to add parameters.

5 Click Submit.


Using the cfapplet tag to embed an applet
After you register an applet, you can use the cfapplet tag to place the applet in a ColdFusion page. The cfapplet tag
has two required attributes: appletsource and name. Because you registered the applet and you defined each applet
parameter with a default value, you can run the applet with a simple form of the cfapplet tag:

<cfapplet appletSource="appletname" name="form_variable">


Overriding alignment and positioning values
To override any of the values defined in the ColdFusion Administrator for the applet, you can use the optional
cfapplet parameters to specify custom values. For example, the following cfapplet tag specifies custom spacing and
alignment values:

<cfapplet appletSource="myapplet"
     name="applet1_var"
     height=400
     width=200
     vspace=125
     hspace=125
     align="left">


Overriding parameter values
You can override the values that you assigned to applet parameters in the ColdFusion Administrator by providing new
values for any parameter. To override a parameter, you must have already defined the parameter and its default value
in the ColdFusion Administrator Applets page. The following example overrides the default values of two parameters,
Param1 and Param2:

<cfapplet appletSource="myapplet"
     name="applet1_var"
     Param1="registered parameter1"
     Param2="registered parameter2">


Handling form variables from an applet
The cfapplet tag name attribute corresponds to a variable in the action page, Form.appletname, which holds any value
that the applet method returns when it is executed in the cfform tag.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      743
Requesting and Presenting Information




Not all Java applets return values. For instance, graphical widgets might not return a specific value. For this type of
applet, the method field in the ColdFusion Administrator remains empty, but you must still provide a cfappletname
attribute.

You can only use one method for each applet that you register. If an applet includes more than one method that you
want to access, you can register the applet with a unique name for each additional method you want to use.


Reference a Java applet return value in your application page
1 Specify the name of the method in the Add/Registered Java Applet page of the ColdFusion Administrator.

2 Specify the method name in the name attribute of the cfapplet tag.

When your page executes the applet, ColdFusion creates a form variable with the name that you specified. If you do
not specify a method, ColdFusion does not create a form variable.



Validating Data

You can validate data in Adobe ColdFusion, including form data, variable data and function parameters.


About ColdFusion validation
Data validation lets you control data that is entered into an application by ensuring that the data conforms to specific
type or formatting rules. Validation techniques have the following features:

* They let you provide feedback to users so that they can immediately correct information they provide. For example,
    a form can provide immediate feedback when a user enters a name in a telephone number field, or the form could
    force the user to enter the number in the correct format.

* They help prevent application errors that can arise when processing invalid data. For example, a validation test can
    prevent a variable that is used in a calculation from having nonnumeric data.

* They can help enhance security by preventing malicious users from providing data that takes advantage of system
    security weaknesses, such as buffer overrun attacks.

ColdFusion provides several techniques to ensure that data is valid. These include techniques for validating form data
and for validating ColdFusion variables. They also include techniques for validating form data before the user submits
it to ColdFusion, or on the ColdFusion server.

When you design data validation you consider the following factors:

The validation technique Whether to validate on the client's browser or on the server, and the specific server- or
client-side validation technique, such as whether to validate when a field loses focus or when the user submits the form.

The validation type The specific method that you use to validate the data, including the rules that you apply to test the
data validity, such as testing for a valid telephone number.


Validation techniques
Different validation techniques apply to different ColdFusion tags or coding environments; for example, you can use
masking only in HTML and Flash format cfinput tags. Validation techniques also vary in where and when they
execute; for example, on the client browser when the user submits form data, or on the server when processing data.

The following table describes the ColdFusion validation techniques:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    744
Requesting and Presenting Information




Validation technique               Applies to            Where and when          Description
                                                         performed

mask                               HTML and Flash format On the client as the    ColdFusion generates JavaScript or ActionScript
                                   cfinput tags          user enters data        to directly control the data a user enters by
(mask attribute)
                                                                                 specifying a pattern. For example, 999-999-9999
                                                                                 requires a user to enter ten digits, and
                                                                                 automatically fills in the dash (-) separators to
                                                                                 create a formatted telephone number.

                                                                                 For detailed information on using masks, see
                                                                                 "Handling invalid data" on page 750.

onBlur                             cfinput and           On the client when the  In HTML and XML format, ColdFusion generates
                                   cftextarea tags       data field loses focus  JavaScript that runs on the browser to check
(validateat="onBlur" attribute)
                                                                                 whether entered data is valid and provide
                                                                                 immediate feedback, if the entry is invalid.

                                                                                 In Flash format, uses Flash built-in validation
                                                                                 routines.

onSubmit                           cfinput and           On the client when the  In HTML or XML format, the validation logic is
                                   cftextarea tags       user clicks Submit      identical to onBlur validation, but the test is not
(validateat="onSubmit"
                                                                                 done until the user submits the form.
attribute)
                                                                                 In Flash format, this validation type is identical to
                                                                                 onBlur Validation. Flash checks do not
                                                                                 differentiate between the two events for
                                                                                 validation.

onServer                           cfinput and           On the server when      ColdFusion checks submitted data for validity and
                                   cftextarea tags       ColdFusion gets the     runs a validation error page if the data is not valid.
(validateat="onServer"
                                                         submitted form          You can use the cferror tag to specify the
attribute)
                                                                                 validation error page.

hidden field                       All Forms, including  On the server when      ColdFusion uses the same validation logic as with
                                   HTML-only forms       ColdFusion gets the     onServer validation, but you must create
                                                         submitted form          additional, hidden, fields and you can use this
                                                                                 technique with HTML tags or CFML tags.

                                                                                 For detailed information on using hidden fields,
                                                                                 see "Validating form data using hidden fields" on
                                                                                 page 755.

JavaScript                         cfgrid, cfinput,      On the client, when the ColdFusion includes the specified JavaScript
                                   cfslider,             user clicks Submit,     function in the HTML page it sends to the browser,
(onValidate ="function"
                                   cftextarea, and       before field-specific   and the browser calls it.
attribute)
                                   cftree tags in HTML   onSubmit validation
                                                                                 For detailed information on using JavaScript for
                                   and XML format forms
                                                                                 validation, see "Validating form input and
                                                                                 handling errors with JavaScript" on page 759.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    745
Requesting and Presenting Information




 Validation technique               Applies to            Where and when         Description
                                                         performed

 IsValid function                   ColdFusion variables  On the server, when    ColdFusion tests the variable to determine
                                                          the function executes  whether it follows a specified validation rule and
                                                                                 the function returns true or false.

                                                                                 For more information on using the IsValid
                                                                                 function for validation, see "Validating data with
                                                                                 the IsValid function and the cfparam tag" on
                                                                                 page 762.

 cfparam tag                        ColdFusion variables  On the server, when    ColdFusion checks the specified variable. If the
                                                          the tag executes       value does not meet the validation criteria,
                                                                                 ColdFusion generates an expression exception.

                                                                                 For more information on using the cfparam tag
                                                                                 for validation, see, "Validating data with the IsValid
                                                                                 function and the cfparam tag" on page 762.

 cfargument tag                     UDF and CFC function  On the server, when a  ColdFusion checks the argument value when it is
                                    arguments             function is called or  passed to the function. If the value does not meet
                                                          invoked                the validation criteria, ColdFusion generates an
                                                                                 application exception.

                                                                                 For more information on using the cfargument
                                                                                 tag, see "Writing and Calling User-Defined
                                                                                 Functions" on page 153.


Note: For more information on ColdFusion error handling, see "Handling Errors" on page 275.


Selecting a validation technique
The following considerations affect the validation technique that you select:

* If you are validating form data, the techniques you use can vary depending on whether you are using HTML, Flash,
    or XML forms; for example, different form types have different validation limitations.

* Different validation techniques are appropriate for different form controls and data types.

* Available techniques vary depending on when and where you want the data validated; on the client or the server,
    when the user enters data or submits a form, or when ColdFusion processes a variable or function argument.

* Each technique has specific features and considerations, such as the form of user feedback, feature limitations, and
    so on.

* Security issues or concerns that apply to your environment or application can affect the technique you select.

The table in the preceding section described some of the considerations (see "Validation techniques" on page 743). The
following table describes additional considerations for selecting a validation technique. For additional considerations
that are specific to form fields, see "Validation type considerations" on page 749.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     746
Requesting and Presenting Information




Validation technique                 Features                       Considerations                   Security issues

mask                                 Directly controls user input.   Limited to cfinput tags.        In HTML and XML format, can be
                                                                     Provides limited control over   circumvented because JavaScript
(mask attribute)
                                                                     user input patterns.            runs directly in the browser.

onBlur                               Provides immediate              Limited to cfinput and          In HTML and XML format, can be
                                     feedback if a user enters      cftextarea tags. In HTML         circumvented because JavaScript
(validateat="onBlur" attribute)
                                     invalid data.                   or XML format, requires the     runs directly in the browser.
                                                                     browser to enable JavaScript.

onSubmit                             All entered data is available   Limited to cfinput and          In HTML and XML format, can be
                                     to the user; only the invalid  cftextarea tags. In Flash        circumvented because JavaScript
(validateat="onSubmit" attribute)
                                     data needs reentering.          format, is identical to onBlur. runs directly in the browser.
                                                                     In HTML or XML format,
                                                                     validates after all fields have
                                                                     been entered, and requires
                                                                     the browser to enable
                                                                     JavaScript.

onServer                             Does not require browser        Limited to cfinput and          Can be circumvented because
                                     support.                       cftextarea tags.                 validation rules are submitted with
(validateat="onServer" attribute)
                                                                                                     the form.

Hidden form field                    Does not require browser        Limited to forms.               Can be circumvented because
                                     support. Can be used with                                       validation rules are submitted with
                                     HTML or CFML form                                               the form.
                                     elements.

JavaScript                           Allows all on-client            Limited to specific             Can be circumvented because
                                     processing supported by the     ColdFusion form tags. Calls a   JavaScript runs directly in the
(onValidate = "function"
                                     browser. Can be used with       single JavaScript function.     browser.
attribute)
                                     HTML or CFML form               JavaScript levels of support
                                     elements.                       can vary among browsers,
                                                                     and users can disable
                                                                     JavaScript in their browsers.

IsValid function                     Can be used for any variable,   When used with a form field,    None
                                     not just form fields. Returns a runs after the data is
                                     Yes or No result that you use   submitted. Must be used
                                     to determine further            each time a variable needs to
                                     processing.                     be validated. Provides some
                                                                     data type checks not
                                                                     available in forms validation
                                                                     techniques.

cfparam tag                          Can be used for any variable,   When used with a form field,    None
                                     not just form fields. The tag   the tag runs after the data is
                                     can set a default value in      submitted. You respond to
                                     addition to validating data.    validation failures using
                                                                     error-handling code.

cfargument tag                       Used for arguments to           Runs when the function is       None
                                     functions written using the     called on the server. You
                                     cffunction tag.                 respond to validation failures
                                                                     using error-handling code.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        747
Requesting and Presenting Information




Security considerations
Although form-specific validation techniques provide good methods for preventing users from submitting invalid or
badly formatted data, they cannot prevent users from submitting maliciously formatted data from HTML forms.
Malicious users can circumvent validation techniques that require validation on the browser using JavaScript or
submission of validation rules in hidden fields. If you must use a technique for preventing malicious data submissions,
consider using the following techniques:

* The onSubmit or OnBlur validation in Flash forms, which use Flash built-in validation.

* The IsValid function and the cfparam, and cfargument tags, which let you test variables and arguments in your
    CFML code.

* The cfqueryparam tag in cfquery tags, which can help protect databases from malicious query input (see
    "Enhancing security with cfqueryparam" on page 416.

* The script protection option, which helps prevent cross-site scripting attacks. You can set this option on the
    ColdFusion Administrator Server Settings > Settings page or by using the Application.cfc This.scriptProtect
    variable or the cfapplication tag scriptprotect attribute. For more information on cross-site scripting attacks and
    this option, see the cfapplication tag page in the CFML Reference.


Data validation types
The following table lists the types of data you can validate when you use most ColdFusion validation techniques. It
does not include mask validation. Some validation types are not available for all techniques; in these cases the table
indicates the limitations. The onBlur and onSubmit validation algorithms for Flash forms can vary from the validation
algorithms described in the following table, and most commonly have less functionality. The asterisk (*) in the Type
field column indicates that the field is required. For more detailed descriptions of the onServer validation algorithms,
see the table in "Validating form data using hidden fields" on page 755.


 Type field                            Description

 date                                  When validating on the server, allows any date/time format that returns true in the IsDate
                                       function, including a time value. When validating on the client, same as USdate.

 USdate *                              A U.S. date of the format mm/dd/yy, with 1- or 2-digit days and months, and 1-through 4-digit
                                       years. The separators can be slash (/), hyphen (-), or period (.) characters

 eurodate *                            A date of the format dd/mm/yy, with 1- or 2-digit days and months, and 1- through 4-digit years.
                                       The separators can be slash (/), hyphen (-), or period (.) characters.

 time *                                When validating on the server, allows any date/time format that returns True in the IsDate
                                       function, including a date value. When validating on the client, allows a time of format hh:mm[:ss]
                                       [A/PM].

 float *                               A number; allows integers. When validating form fields on the server, integer values are converted
                                       to real numbers.

 numeric                               A number; allows integers. When validating form fields on the server, integer values are
                                       unchanged.

 integer *                             An integer.

 range *                               A numeric range specified by a range attribute or max and min attributes.

 boolean                               A value that can be converted to a Boolean value: Yes, No, True, or False (all case-independent), or
                                       a number.

 telephone *                           Standard U.S. telephone formats. Allows an initial 1 long-distance designator and up to 5-digit
                                       extensions, optionally starting with x.

 zipcode *                             U.S. 5- or 9-digit ZIP code format #####-####. The separator can be a hyphen (-) or a space.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                             748
Requesting and Presenting Information




Type field                                 Description

creditcard *                               Strips blanks and dashes; verifies number using mod10 algorithm. The number must have 13­16
                                           digits.

ssn * or social_security_number *          US. Social Security number format, ###-##-####. The separator can be a dash (-) or a space.

email *                                    A valid e-mail address of the form name@server.domain. ColdFusion validates the format only; it
                                           does not check that entry is a valid active e-mail address.

URL *                                      A valid URL pattern; supports http, https, ftp file, mailto, and news URLs.

guid *                                     A unique identifier that follows the Microsoft/DCE format, xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx,
                                           where x is a hexadecimal number.

uuid *                                     A universally unique identifier (UUID) that follows the ColdFusion format, xxxxxxxx-xxxx-xxxx-
                                           xxxxxxxxxxxxxxxx, where x is a hexadecimal number.

regex * or regular_expression *            Matches the value against a regular expression specified in a pattern attribute. Valid in HTML and
                                           XML format only; ignored in Flash format.


Note: For more details on how ColdFusion handles data when it does onServer and hidden field validation, see
"Validating form data using hidden fields" on page 755.

The following validation types can only be used in cfinput tags:


Type                        Description

maxlength                   Limits the input to a maximum number of characters specified by a maxlength attribute.

noblanks                    Does not allow fields that consist only of blanks. ColdFusion uses this validation only if the required attribute is
                            True.

SubmitOnce                  Used only with cfformsubmit and image types; prevents the user from submitting the same form multiple
                            times before until the next page loads, Use this attribute, for example, to prevent a user from submitting an order
                            form a second time before getting the confirmation for the initial order, and thereby making a duplicate order,
                            Valid in HTML and XML format only; ignored in Flash format.


You can use the following validation types in cfparam and cfargument tags and the IsValid function only:


Type                        Description

any                         Any type of value

array                       An array of values

binary                      A binary value

query                       A query object

string                      A string value or single character

struct                      A structure

variableName *              A string formatted according to ColdFusion variable naming conventions.



Validating form fields
In basic form field validation, do the following:

* Use a cfinput or cftextarea tag.

* Specify a validation type, such as numeric, or multiple types.




                                                       

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      749
Requesting and Presenting Information




* Optionally, specify an error message.

* Optionally, specify a validation technique. (By default, ColdFusion uses onSubmit validation.)

The following example specifies onBlur validation of a telephone number:

 Phone: <cfinput type="text" name="HPhone"
          validateat="onBlur"
          validate="required,telephone"
          message="Please enter a standard U.S. telephone number with an optional
               extension, such as x12345">

The following information describes considerations for validation in cfinput and cftextarea tags, and show a more
complete example.


Validation type considerations
General considerations: Consider the following issues when you determine how to validate form data:

* When you validate form data using onBlur, onSubmit, onServer, or hidden form field validation, you can specify
   one or more validation types for each field that you validate. For example, you can specify that a field entry is
   required and that it must be numeric. To specify multiple validation types for onSubmit, onBlur, or onServer
   validation, specify the type values in a comma-delimited list.

* If you use onBlur, onSubmit, or onServer type validation, you can specify only one error message for each field that
   you validate. If you use hidden field validation, you can create a custom message for each validation rule (except for
   range checking).

* In the cfinput tag, most validation type attributes apply only to text or password fields.

Validation algorithm differences: The underlying validation code used when validating form data can differ
depending on the validation technique and the form type. As a result, the algorithms used vary in some instances,
including the following:

* The validation algorithms used for date/time values in onSubmit and OnBlur validation are different from those
   validation algorithms used for all server-side validation techniques.

* The algorithms used for onSubmit and OnBlur validation in Flash can vary from those algorithms used for HTML
   or XML format, and generally follow simpler rules.

For detailed information on the validation algorithms used for validation techniques used on the server, see
"Validating form data using hidden fields" on page 755.


Validating data in XML skinnable forms
If you create an XML skinnable form and use any skin provided by Adobe, such as the basic.xsl or silver.xsl skin, you
can use all form validation techniques that are available for HTML forms.

If you use a custom skin (XSL file), the available validation techniques depend on the skin. The
cf_webroot\CFIDE\scripts\xsl directory contains a _cfformvalidation.xsl file that implements all ColdFusion HTML
form validation techniques and supports onBlur, onSubmit, onServer, and hidden form field validation. XML skin
writers can include this file in their skin XSLT to implement ColdFusion validation for their skin.


Example: basic form validation
The following form asks for information to use when registering a new user. It checks to make sure that the user enters
required information. (Only the telephone number is optional.) It also checks to make sure that the telephone number
and e-mail address are properly formatted and that the number to use in a challenge question is in the proper range.
This example performs onSubmit validation. It posts back to itself, and dumps the submitted results.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 750
Requesting and Presenting Information




<cfif IsDefined("form.fieldnames")>
    <cfdump var="#form#"><br>
</cfif>


<cfform name="myform" preservedata="Yes" >
    First Name: <cfinput type="text" size="15" name="firstname"
         required="yes" message="You must enter a first name."><br>
    Last Name: <cfinput type="text" size="25" name="lastname"
         required="yes" message="You must enter a last name."><br>
    Telephone: <cfinput type="text" size="20" name="telephone"
         validate="telephone" message="You must enter your telephone
         number, for example 617-555-1212 x1234"><br>
    E-mail: <cfinput type="text" size="25" name="email"
         validate="email" required="Yes"
         message="You must enter a valid e-mail address."><br>
    Password:<cfinput type="password" size="12" name="password1"
         required="yes" maxlength="12"
         message="You must enter a password."><br>
    Reenter password:<cfinput type="password" size="12" name="password2"
         required="yes" maxlength="12"
         message="You must enter your password twice."><br>
    We will ask you for the following number, in the range 100-999 if you forget
         your password.<br>
    Number: <cfinput type="text" size="5" name="chalenge"
         validate="range" range="100,999" required="Yes"
         message="You must enter a reminder number in the range 100-999."><br>
    <cfinput type="submit" name="submitit">
</cfform>



Handling invalid data
How you handle invalid data depends on the validation type. The information here describes validation error-
handling rules and considerations. For detailed information on error handling in ColdFusion, including invalid data
handling, see "Handling Errors" on page 275.

1 For onBlur, onSubmit, or onServer validation, you can use the cfinput or cftextarea tag's message attribute to
  specify a text-only error message to display. Otherwise, ColdFusion uses a default message that includes the name
  of the form field that was invalid. (For OnServer validation, you can customize this message, as described in
  "Handling form field validation errors" on page 282.) The following example displays an error message when the
  user enters an invalid e-mail address:

        E-mail: <cfinput type="text" size="25" name="email"
             validate="email" message="You must enter a valid e-mail address.">

2 For hidden form validation, you can specify a text-only error message in the hidden field's value attribute.
  Otherwise, ColdFusion uses a default message that includes the name of the form field that was invalid. (You can
  customize this message, as described in "Handling form field validation errors" on page 282.) The following
  cfinput tag, for example, uses a hidden field validation to display an error message if the user enters an invalid
  address. (It uses onServer validation to display a different error message if the user fails to enter a number.)

   Telephone: <cfinput type="text" size="20" name="telephone"
        validateat="onServer" required="Yes"
        message="You must enter a telephone number">
        <cfinput type="hidden" name="telephone_cfformtelephone"
        value="The number you entered is not in the correct format.<br>Use a
        number such as (617) 555-1212, 617-555-1212, or 617-555-1212 x12345">




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    751
Requesting and Presenting Information




3 For HTML and XML format forms (using ColdFusion skins), most ColdFusion form tags have an onError
   attribute that lets you specify a Javascript function to run if an onSubmit error occurs.

4 For the IsValid function, you write separate code paths to handle valid and invalid data. The following example
   shows a simplified case that displays an error message if the user entered an invalid e-mail address, or a different
   message if the address is valid:

    <cfif IsValid("email", custEmail)>
         Thank you for entering a valid address.
         <!--- More processing would go here. --->
   <cfelse>
         You must enter a valid e-mail address.<br>
         Click the Back button and try again.
   </cfif>

5 For cfparam and cfargument tags, you use standard ColdFusion error-handling techniques. You can include the
   tag in a try block and use a catch block to handle the error, or you can use a custom error-handling page. The
   following example form action page code uses a custom error page, expresserr.cfm, to handle the error that the
   cfparam tag generates if a user submits a form with an invalid e-mail address:

    <cferror type="EXCEPTION" exception="expression" template="expresserr.cfm">
   <cfif IsDefined("form.fieldnames")>
         <cfparam name="form.custEmail" type="email">
         <!--- Normal form processing code goes here. --->
   </cfif>



Masking form input values
The cfinput tag mask attribute controls the format of data that can be entered into a text or datefield input field.
You can also use a mask attribute in the cfcalendar tag. You can combine masking and validation on a field.

* In HTML and Flash form format, a mask can control the format of data entered into a text field.

* In the cfcalendar tag, and, for Flash format forms, the datefield type cfinput field, a mask can control the
   format of the date that ColdFusion uses for the date a user chooses in the displayed calendar.

Note: The standard ColdFusion XML skins do not support masking.


Masking text input
In text fields, ColdFusion automatically inserts literal mask characters, such as - characters in telephone numbers.
Users type only the variable part of the field. You can use the following characters to mask data:


Mask character                        Effect

A                                     Allows an uppercase or lowercase character: A­Z and a­z.

X                                     Allows an uppercase or lowercase character or number: A­Z, a­z, and 0­9.

9                                     Allows a number: 0­9.

?                                     Allows any character.

All other characters                  Automatically inserts the literal character.


The following pattern enforces entry of a part number of the format EB-1234-c1-098765, where the user starts the
entry by typing the first numeric character, such as 1. ColdFusion fills in the preceding EB prefix and all hyphen (-)
characters. The user must enter four numbers, followed by two alphanumeric characters, followed by six numbers.

<cfinput type="text" name="newPart" mask="EB-9999-XX-999999" />




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        752
Requesting and Presenting Information




Note: You cannot force a user to type an A, X, 9, or question mark (?) character. To ensure that a pattern is all-uppercase
or all-lowercase, use the ColdFusion UCase or LCase functions in the action page.


Masking cfcalendar and datefield input
In the cfcalendar tag and the Flash format datefield input control, you use the following masks to determine the
format of the output. You can use uppercase or lowercase characters in the mask:


 Mask                     Pattern

 D                        Single- or double-digit day of month, such as 1 or 28

 DD                       Double-digit day of month, such as 01 or 28

 M                        Single- or double-digit month, such as 1 or 12

 MM                       Double-digit month, such as 01 or 12

 MMM                      Abbreviated month name, such as Jan or Dec

 MMMM                     Full month name, such as January or December

 YY                       Two-character year, such as 05

 YYYY                     Four-character year, such as 2005

 E                        Single-digit day of week, in the range 0 (Sunday)­6 (Saturday)

 EEE                      Abbreviated day of week name, such as Mon or Sun

 EEEE                     Full month day of week name, such as Monday or Sunday


The following pattern specifies that the Flash form sends the date selected using a datefield input control to
ColdFusion as text in the format 04/29/2004:

<cfinput name="startDate" type="datefield" label="date:" mask="mm/dd/yyyy"/>



Validating form data with regular expressions
You can use regular expressions to match and validate the text that users enter in cfinput and cftextinput tags.
Ordinary characters are combined with special characters to define the match pattern. The validation succeeds only if
the user input matches the pattern.

Regular expressions let you check input text for a wide variety of custom conditions for which the input must follow a
specific pattern. You can concatenate simple regular expressions into complex search criteria to validate against
complex patterns, such as any of several words with different endings.

You can use ColdFusion variables and functions in regular expressions. The ColdFusion server evaluates the variables
and functions before the regular expression is evaluated. For example, you can validate against a value that you
generate dynamically from other input data or database values.

Note: The rules listed here are for JavaScript regular expressions, and apply to the regular expressions used in cfinput
and cftextinput tags only. These rules differ from the rules used by the ColdFusion functions REFind, REReplace,
REFindNoCase, and REReplaceNoCase. For information on regular expressions used in ColdFusion functions, see
"Using Regular Expressions in Functions" on page 131.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                753
Requesting and Presenting Information




Special characters
Because special characters are the operators in regular expressions, to represent a special character as an ordinary one,
escape it by preceding it with a backslash. For example, use two backslash characters (\\) to represent a backslash
character.


Single-character regular expressions
The following rules govern regular expressions that match a single character:

* Special characters are: + * ? . [ ^ $ ( ) { | \

* Any character matches itself if it is not a special character or if a preceding backslash (\) escapes the character.

* A backslash (\) followed by any special character matches the literal character itself; that is, the backslash escapes
   the special character.

* A period (.) matches any character except newline.

* A set of characters enclosed in brackets ([]) is a one-character regular expression that matches any of the characters
   in that set. For example, "[akm]" matches an a, k, or m. If you include ] (closing square bracket) in square brackets,
   it must be the first character. Otherwise, it does not work, even if you use \].

* A dash can indicate a range of characters. For example, [a-z] matches any lowercase letter.

* If the first character of a set of characters in brackets is the caret (^), the expression matches any character except
   those characters in the set. It does not match the empty string. For example: "[^akm]" matches any character except
   a, k, or m. The caret loses its special meaning if it is not the first character of the set.

* You can make regular expressions case insensitive by substituting individual characters with character sets; for
   example, "[Nn][Ii][Cc][Kk]" is a case-insensitive pattern for the name Nick (or NICK, or nick, or even nIcK).

* You can use the following escape sequences to match specific characters or character classes:

Escape seq       Matches                                         Escape seq   Meaning

[\b]             Backspace.                                      \s           Any of the following white-space characters: space,
                                                                              tab, form feed, and line feed.

\b               A word boundary, such as a space.               \S           Any character except the white-space characters
                                                                              matched by \s.

\B               A nonword boundary.                             \t           Tab.

\cX              The control character Ctrl-x. For example, \cv  \v           Vertical tab.
                 matches Ctrl-v, the usual control character for
                 pasting text.

\d               A digit character [0-9].                        \w           An alphanumeric character or underscore. The
                                                                              equivalent of [A-Za-z0-9_].

\D               Any character except a digit.                   \W           Any character not matched by \w. The equivalent of
                                                                              [^A-Za-z0-9_].

\f               Form feed.                                      \n           Backreference to the nth expression in parentheses.
                                                                              See "Backreferences" on page 754.

\n               Line feed.                                      \ooctal      The character represented in the ASII character table
                                                                              by the specified octal number.

\r               Carriage return.                                \\xhex       The character represented in the ASCII character
                                                                              table by the specified hexadecimal number.




                                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         754
Requesting and Presenting Information




Multicharacter regular expressions
Use the following rules to build a multicharacter regular expression:

* Parentheses group parts of regular expressions into a subexpression that can be treated as a single unit. For example,
   "(ha)+" matches one or more instances of ha.

* A one-character regular expression or grouped subexpression followed by an asterisk (*) matches zero or more
   occurrences of the regular expression. For example, "[a-z]*" matches zero or more lowercase characters.

* A one-character regular expression or grouped subexpression followed by a plus sign (+) matches one or more
   occurrences of the regular expression. For example, "[a-z]+" matches one or more lowercase characters.

* A one-character regular expression or grouped subexpression followed by a question mark (?) matches zero or one
   occurrence of the regular expression. For example, "xy?z" matches either xyz or xz.

* The carat (^) at the beginning of a regular expression matches the beginning of the field.

* The dollar sign ($) at the end of a regular expression matches the end of the field.

* The concatenation of regular expressions creates a regular expression that matches the corresponding
   concatenation of strings. For example, "[A-Z][a-z]*" matches any capitalized word.

* The OR character (|) allows a choice between two regular expressions. For example, "jell(y|ies)" matches either jelly
   or jellies.

* Curly brackets ({}) indicate a range of occurrences of a regular expression. You use them in the form "{m, n}" where
   m is a positive integer equal to or greater than zero indicating the start of the range and n is equal to or greater than
   m, indicating the end of the range. For example, "(ba){0,3}" matches up to three pairs of the expression ba. The form
   "{m,}" requires at least m occurrences of the preceding regular expression. The form "{m}" requires exactly m
   occurrences of the preceding regular expression. The form "{,n}" is not allowed.


Backreferences
Backreferencing lets you match text in previously matched sets of parentheses. A slash followed by a digit n (\n) refers
to the nth parenthesized subexpression.

One example of how you can use backreferencing is searching for doubled words; for example, to find instances of "the
the" or "is is" in text. The following example shows backreferencing in a regular expression:

(\b[A-Za-z]+)[ ]+\1

This code matches text that contains a word that is repeated twice; that is, it matches a word (specified by the \b word
boundary special character and the "[A-Za-z]+)" followed by one or more spaces (specified by "[ ]+"), followed by the
first matched subexpression, the first word, in parentheses. For example, it would match "is is", but not "This is".


Exact and partial matches
ColdFusion validation normally considers a value to be valid if any of it matches the regular expression pattern. If you
want to ensure that the entire entry matches the pattern, "anchor" it to the beginning and end of the field, as follows:

* If a caret (^) is at the beginning of a pattern, the field must begin with a string that matches the pattern.

* If a dollar sign ($) is at the end of a pattern, the field must end with a string that matches the pattern.

* If the expression starts with a caret and ends with a dollar sign, the field must exactly match the pattern.


Expression examples
The following examples show some regular expressions and describe what they match:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     755
Requesting and Presenting Information




Expression                               Description

[\?&]value=                              Any string containing a URL parameter value.

^[A-Z]:(\\[A-Z0-9_]+)+$                  An uppercase Windows directory path that is not the root of a drive and has only letters,
                                         numbers, and underscores in its text.

^(\+|-)?[1-9][0-9]*$                     An integer that does not begin with a zero and has an optional sign.

^(\+|-)?[1-9][0-9]*(\.[0-9]*)?$          A real number.

^(\+|-)?[1-9]\.[0-9]*E(\+|-)?[0-9]+$     A real number in engineering notation.

a{2,4}                                   A string containing two to four occurrences of a: aa, aaa, aaaa; for example, aardvark, but not
                                         automatic.

(ba){2,}                                 A string containing least two ba pairs; for example, Ali baba, but not Ali Baba.


Note: An excellent reference on regular expressions is Mastering Regular Expressions by Jeffrey E.F. Friedl, published by
O'Reilly & Associates, Inc.


Validating form data using hidden fields
ColdFusion lets you specify form field validation on the server by using hidden form fields whose names consist of the
name of the field to validate and the validation type. Hidden field validation uses the same underlying techniques and
algorithms as onServer validation of ColdFusion form fields.

Hidden field validation has the following features:

* You can use it with standard HTML tags. For example, you can validate data in an HTML input tag. This feature
   was useful in releases previous to ColdFusion MX 7, because the cfinput tag did not support all HTML type
   attributes.

* It is backward-compatible with validation previous to ColdFusion MX 7, when hidden field validation was the only
   way to do validation on the server.

* Because you use a separate tag for each validation type, if you specify multiple validation rules for a field, you can
   specify a different error message for each rule.

* You can use hidden field validation with any form field type that submits a data value, not input, cfinput,
   textarea, or cftextarea.


Specifying hidden form field validation
To specify hidden field validation, you do the following:

* Create one HTML input element or CFML cfinput tag of type="hidden" for each validation rule.

* Specify the name of the field to validate as the first part of the hidden field name.

* Specify the type of validation, starting with an underscore character (_), as the second part of the hidden field name.

* You can specify multiple rules for each form data field. For example, to specify range and required validation for a
   field named myValue, create hidden myValue_cfformrange and myValue_cfformrequired fields.

* For most types of validation, specify the error message as the field value attribute.

* For range, maximum length, or regular expression validation, specify the rule, such as the maximum length, in the
   value attribute. For these validation types, you cannot specify a custom error message.

The following example uses hidden fields to require data in a date field and ensure that the field contains a date. It
consists only of HTML tags.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                          756
Requesting and Presenting Information




<input type="text" name="StartDate" size="16" maxlength="16"><br>
<input type="hidden" name="StartDate_required"
            value="You must enter a start date.">
<input type="hidden" name="StartDate_date"
     value="Please enter a valid date as the start date.">

Use the following suffixes at the end of hidden form field names to specify the validation type. The type identifier
always starts with an underscore. Several validation rules have two names you can use. The names that do not start
with "_cf" were used in earlier releases and are retained for backward compatibility. For consistency and clarity, Adobe
recommends using the names that start with "_cf" in new forms.


Field name suffix                     Verifies

_integer, _cfforminteger              An integer of the range -2,147,483,648 -- 2,147,483,647. Treats the initial characters "$ € ¥ £ +" as
                                      valid input, but removes them from the number.

_cfformnumeric                        Any numeric value. Treats the initial characters "$ € ¥ £ +"as valid input, but does NOT remove them
                                      from the number.

_float, _cfformfloat                  Any value (including an integer) that can be represented as a floating point number with up to 12
                                      significant digits. Treats the initial characters "$ € ¥ £ +" as valid input, but removes them from the
                                      number.

                                      Converts input data to a real number; for example a dump of an integer value on the action page
                                      includes a decimal point followed by a 0.

_range, _cfformrange                  A numeric value within boundaries specified by the value attribute. Specify the range in the value
                                      attribute using the format "min=minvalue max=maxvalue." You cannot specify a custom error
                                      message for this validation.

_date, _cfformdate                    A date in any format that ColdFusion can understand; converts the input to ODBC date format.
                                      Allows entry of a time part, but removes it from the ODBC value.

_cfformusdate                         A date in the form m/d/y, m-d-y , or m.d.y, The m and d format can be 1 or 2 digits; y can be 2 or 4
                                      digits. Does not convert the string to an ODBC value and does not allow a time part.

_eurodate, _cfformeurodate            A date in the form d/m/y, d-m-y, or d.m.y. The m and d format can be 1 or 2 digits; y can be 2 or 4
                                      digits. Converts the input to ODBC date format. Allows entry of a time part, but removes it from the
                                      ODBC value.

_time, _cfformtime                    A time. Can be in 12-hour or 24-hour clock format, and can include seconds in the form hh:mm:ss
                                      or a case-independent am or pm indicator.

                                      Converts the input to ODBC time format. Allows entry of a date part, but removes it from the ODBC
                                      value.

_cfformcreditcard                     After stripping blanks and dashes, a number that conforms to the mod10 algorithm. Number must
                                      have 13-16 digits.

_cfformSSN,                           A nine-digit Social Security number. Can be of the form xxx-xx-xxxx or xxx xx xxxx.
_cfformsocial_security_number

_cfformtelephone                      Standard U.S. telephone formats. Does not support international telephone numbers.

                                      Allows area codes with or without parentheses, and hyphens (-), spaces, periods, or no separators
                                      between standard number groups. Can be preceded by a 1 long-distance designator, and can end
                                      with an up-to-5 digit extension, optionally starting with x. The area code and exchange must begin
                                      with a digit in the range 1 - 9.

_cfformzipcode                        A 5-digit or 9-digit U.S. ZIP code. In 9-digit codes, precede the final four digits by a hyphen (-) or
                                      space.

_cfformemail                          A valid e-mail address. Valid address characters are a-zA-Z0-9_- and the period and separator.
                                      There must be a single at sign (@) and the text after the @ character must include a period, as in
                                      my_address@MyCo.com or b-b.jones27@hisco.co.uk.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                               757
Requesting and Presenting Information




Field name suffix                    Verifies

_cfformURL                           A valid URL. Must start with http:\\, https:\, ftp:\, file:\, mailto:, or news:. Can include, as appropriate,
                                     user name and password designators and query strings. The main part of the address can only have
                                     the characters A-Za-z0-9 and -.

_cfformboolean                       A value that can be treated as a Boolean: Yes, No, True, False, 0, 1.

_cfformUUID                          A universally unique identifier (UUID) that follows the ColdFusion format, xxxxxxxx-xxxx-xxxx-
                                     xxxxxxxxxxxxxxxx, where x is a hexadecimal number.

_cfformGUID                          A unique identifier that follows the Microsoft/DCE format, xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx,
                                     where x is a hexadecimal number.

_cfformnoblanks                      The field must not include blanks. ColdFusion uses this validation only if you also specify a
                                     _required hidden field.

_cfformmaxlength                     The number of characters must not exceed the number specified by the tag value attribute.

_cfformregex,                        The data must match a JavaScript regular expression specified by the tag value attribute.
_cfformregular_expression

_required, _cfformrequired           Data must be entered or selected in the form field.



Hidden form field considerations
Consider the following rules and recommendations when determining whether and how to use hidden form field
validation:

* Use hidden field validation if you want to validate data from standard HTML input tags. The cfinput and
   cftextarea tags include a validateAt attribute that provides a simpler method for specifying server-side
   validation.

* Consider using hidden field validation with the cfinput and cftextarea tags if you specify multiple validation
   rules for a single field and want to provide a separate error message for each validation.

* Do not use the suffixes listed in the table as field names.

* Adding a validation rule to a field does not make it a required field. Add a separate _required hidden field to
   ensure user entry.


Hidden form field example
The following procedure creates a simple form for entering a start date and a salary. It uses hidden fields to ensure that
you enter data and that the data is in the right format.

This example uses a self-submitting form; the same ColdFusion page is both the form page and its action page. The
page uses an IsDefined function to check that form data has been submitted. This way, the pages does not show any
results until you submit the input. The form uses HTML tags only; you can substitute these tags with the CFML
equivalents.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   758
Requesting and Presenting Information




<html>
<head>
    <title>Simple Data Form</title>
</head>
<body>
<h2>Simple Data Form</h2>


<!--- Form part. --->
<form action="datatest.cfm" method="Post">
    <input type="hidden"
          name="StartDate_cfformrequired"
          value="You must enter a start date.">
    <input type="hidden"
          name="StartDate_cfformdate"
          value="Enter a valid date as the start date.">
    <input type="hidden"
          name="Salary_cfformrequired"
          value="You must enter a salary.">
    <input type="hidden"
          name="Salary_cfformfloat"
          value="The salary must be a number.">
    Start Date:
    <input type="text"
          name="StartDate" size="16"
          maxlength="16"><br>
    Salary:
    <input type="text"

          name="Salary"
          size="10"
          maxlength="10"><br>
    <input type="reset"
          name="ResetForm"
          value="Clear Form">
    <input type="submit"
          name="SubmitForm"
          value="Insert Data">
</form>
<br>


<!--- Action part. --->
<cfif isdefined("Form.StartDate")>
    <cfoutput>
          Start Date is: #DateFormat(Form.StartDate)#<br>
          Salary is: #DollarFormat(Form.Salary)#
    </cfoutput>
</cfif>
</html>

When the user submits this form, ColdFusion scans the form fields to find any validation rules. It then uses the rules
to analyze the user's input. If any of the input rules are violated, ColdFusion displays an error page with the error
message that you specified in the hidden field's value attribute. The user must go back to the form, correct the
problem, and resubmit the form. ColdFusion does not accept form submission until the user enters the entire form
correctly.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                      759
Requesting and Presenting Information




Because numeric values often contain commas and currency symbols, ColdFusion automatically deletes these
characters from fields with _cfforminteger and _cfformfloat rules before it validates the form field and passes the data
to the form's action page. ColdFusion does not delete these characters from fields with _cfformnumeric rules.


Reviewing the code
The following table describes the code and its function:


 Code                                            Description

 <form action="datatest.cfm" method="Post"> Gathers the information from this form sends it to the dataform.cfm page (this
                                                 page) using the Post method.

 <input type="hidden"                            Requires input into the StartDate input field. If there is no input, displays the error
 name="StartDate_cfformrequired" value="You      information "You must enter a start date." Requires the input to be in a valid date
 must enter a start date.">
                                                 format. If the input is not valid, displays the error information "Enter a valid date as
 <input type="hidden"
                                                 the start date."
 name="StartDate_cfformdate" value="Enter a
 valid date as the start date.">

 <input type="hidden"                            Requires input into the Salary input field. If there is no input, displays the error
 name="Salary_cfformrequired" value="You         information "You must enter a salary." Requires the input to be in a valid number.
 must enter a salary.">
                                                 If it is not valid, displays the error information "The salary must be a number."
 <input type="hidden"
 name="Salary_cfformfloat" value="The
 salary must be a number.">

 Start Date:                                     Creates a text box called StartDate in which users can enter their starting date.
 <input type="text" name="StartDate"             Makes it 16-characters wide.
 size="16" maxlength="16"><br>

 Salary:                                         Creates a text box called Salary in which users can enter their salary. Makes it ten-
 <input type="text" name="Salary" size="10"      characters wide.
 maxlength="10"><br>

 <cfif isdefined("Form.StartDate")>              Displays the values of the StartDate and Salary form fields only if they are defined.
  <cfoutput>                                     They are not defined until you submit the form, so they do not appear on the initial
 Start Date is:
                                                 form. Uses the DateFormat function to display the start date in the default date
 #DateFormat(Form.StartDate)#<br>
                                                 format. Uses the DollarFormat function to display the salary with a dollar sign and
 Salary is: #DollarFormat(Form.Salary)#
 </cfoutput>                                     commas.
 </cfif>



Validating form input and handling errors with JavaScript
ColdFusion lets you write your own validation routines in JavaScript, and lets you create JavaScript error handlers.


Validating input with JavaScript
In addition to native ColdFusion input validation using the validate attribute of the cfinput and cftextarea tags,
the following tags support the onValidate attribute, which lets you specify a JavaScript function to handle your
cfform input validation:

* cfgrid

* cfinput

* cfslider

* cftextarea

* cftree

ColdFusion passes the following arguments to the JavaScript function that you specify in the onValidate attribute:

* The form JavaScript DOM object




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       760
Requesting and Presenting Information




* The name attribute of the form element

* The value of the control to validate

For example, if you write the cfinput tag as the following:

 <cfinput type="text"
     ...
<!--- Do not include () in JavaScript function name. --->
     onvalidate="handleValidation"
     ...
>

You define the JavaScript function as the following:

 <script>
<!--
function handleValidation(form_object, input_object, object_value) {
...
}
//-->
</script>


Example: validating a password
The following example validates a password. The password must have at least one of each of the following: an
uppercase letter, a lowercase letter, and a number. It must be from 8 through 12 characters long. If the password is
invalid, the browser displays a message box. If the password is valid, it redisplays the page with a brief success message.


Use JavaScript to validate form data
1 Create a ColdFusion page with the following content:

   <html>
   <head>
         <title>JavaScript Validation</title>


   <!--- JavaScript used for validation. --->
   <script>
   <!--
         // Regular expressions used for pattern matching.
         var anUpperCase = /[A-Z]/;
         var aLowerCase = /[a-z]/;
         var aNumber = /[0-9]/;
         /* The function specified by the onValidate attribute.
              Tests for existence of at least one uppercase, lowercase, and numeric
              character, and checks the length for a minimum.
              A maximum length test is not needed because of the cfinput maxlength
              attribute. */
         function testpasswd(form, ctrl, value) {
              if (value.length < 8 || value.search(anUpperCase) == -1 ||
                   value.search (aLowerCase) == -1 || value.search (aNumber) == -1)
              {
                   return (false);
              }
              else
              {
                   return (true);
              }




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        761
Requesting and Presenting Information




        }
   //-->
   </script>
   </head>


   <body>
   <h2>JavaScript validation test</h2>
   <!--- Form is submitted only if the password is valid. --->
   <cfif IsDefined("Form.passwd1")>
        <p>Your Password if valid.</p>
   </cfif>
   <p>Please enter your new password:</p>


   <cfform name="UpdateForm" preservedata="Yes" >
        <!--- The onValidate attribute specifies the JavaScript validation
                     function. The message attribute is the message that appears
                     if the validation function returns False. --->
        <cfinput type="password" name="passwd1" required="YES"
              onValidate="testpasswd"
              message="Your password must have 8-12 characters and include uppercase
   and lowercase letters and at least one number."
              size="13" maxlength="12">


   <input type="Submit" value=" Update... ">
   </cfform>


   </body>
   </html>

2 Save the page as validjs.cfm.

3 View the validjs.cfm page in your browser.


Handling failed validation
The onError attribute lets you specify a JavaScript function to execute if an onValidate, onBlur, or onSubmit
validation fails. For example, if you use the onValidate attribute to specify a JavaScript function to handle input
validation, you can also use the onError attribute to specify a JavaScript function to handle a failed validation (that is,
when onValidate returns a False value). If you use the onValidate attribute, you can also use the onError attribute
to specify a JavaScript function that handles the validation errors. The following cfform tags support the onerror
attribute:

* cfgrid

* cfinput

* cfselect

* cfslider

* cftextinput

* cftree

ColdFusion passes the following JavaScript objects to the function in the onerror attribute:

* The JavaScript form object

* The name attribute of the form element

* The value that failed validation



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       762
Requesting and Presenting Information




* The error message text specified by the CFML tag's message attribute

The following example shows a form that uses an onError attribute to tell ColdFusion to call a showErrorMessage
JavaScript function that uses the alert method to display an error message. The function assembles the message from
the invalid value and the contents of the cfinput tag's message attribute.

<!--- The JavaScript function to handle errors.
            Puts a message, including the field name and value, in an alert box. --->
<script>
     <!--
     function showErrorMessage(form, ctrl, value, message) {
          alert("The value " + value +" of the " + ctrl + " field " + message);
     }
     //-->
</script>


<!--- The form.
     The cfinput tags use the onError attribute to override the ColdFusion
     default error message mechanism. --->
<cfform>
     <!--- A minimum quantity is required and must be a number. --->
     Minimum Quantity: <cfinput type="Text" name="MinQuantity"
          onError="showErrorMessage" validate="numeric" required="Yes"
          message="is not a number." ><br>
     <!--- A maximum quantity is optional, but must be a number if supplied. --->
     Maximum Quantity: <cfinput type="Text" name="MaxQuantity"
          onError="showErrorMessage" validate="numeric"
                message="is not a number." ><br>
     <cfinput type="submit" name="submitit">
</cfform>



Validating data with the IsValid function and the cfparam tag
The IsValid function and cfparam tag validate any ColdFusion variable value, not just forms variables. Because they
reside entirely on the ColdFusion server, they can provide a secure mechanism for ensuring that the required
validation steps get performed. Users cannot evade any of the checks by modifying the form data that gets submitted.
These techniques also provide greater flexibility in how you respond to user errors, because you can use full CFML
syntax in your error-handling code.

These two validation techniques operate as follows:

* The IsValid function tests the value of a ColdFusion variable. If the value is valid, it returns True; if the value is
   invalid, it returns False.

* The cfparam tag with a type attribute tests the value of a ColdFusion value for validity. If the value is valid, it does
   nothing; if the value is invalid, it throws a ColdFusion expression exception.

You can use either technique interchangeably. The technique you choose should depend on your coding style and
programming practices. It can also depend on the specific information that you want to display if an error occurs.


Example: IsValid function validation
The following example checks whether a user has submitted a numeric ID and a valid e-mail address and phone
number. If any of the submitted values does not meet the validation test, the page displays an error message.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        763
Requesting and Presenting Information




<!--- Action code. First make sure the form was submitted. --->
<cfif isDefined("form.saveSubmit")>
     <cfif isValid("integer", form.UserID) and isValid("email", form.emailAddr)
                and isValid("telephone", form.phoneNo)>
           <cfoutput>
                <!--- Application code to update the database goes here --->
                <h3>The e-mail address and phone number for user #Form.UserID#
                    have been added</h3>
           </cfoutput>
     <cfelse>
           <H3>Please enter a valid user ID, phone number, and e-mail address.</H2>
     </cfif>
     <cfelse>
</cfif>


<!--- The form. --->
<cfform action="#CGI.SCRIPT_NAME#">
     User ID:<cfinput type="Text" name="UserID"><br>
     Phone: <cfinput type="Text" name="phoneNo"><br>
     E-mail: <cfinput type="Text" name="emailAddr"><br>
     <cfinput type="submit" name="saveSubmit" value="Save Data"><br>
</cfform>


Examples: cfparam tag validation
The following two examples use cfparam tags to do the same tests as in the "Example: IsValid function validation" on
page 762. They check whether a user has submitted a numeric ID and a valid e-mail address and phone number. If any
of the submitted values does not meet the validation test, ColdFusion throws an expression exception.

In the first example, the error is handled by the exprerr.cfm page specified in the cferror tag. In this case, if the user
made multiple errors, ColdFusion lists only one.

In the second example, each invalid field is handled in a separate try/catch block. In this case, the user gets information
about each error.


Using an error-handling page
The self-posting form and action page looks as follows:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                             764
Requesting and Presenting Information




<!--- Action part of the page. --->
<!--- If an expression exception occurs, run the expresser.cfm page. --->
<cferror type="EXCEPTION" exception="expression" template="expresserr.cfm">
<!--- Make sure the form was submitted. --->
<cfif isDefined("form.saveSubmit")>
<!--- Use cfparam tags to check the form field data types. --->
         <cfparam name="form.emailAddr" type="email">
         <cfparam name="form.UserID" type="integer">
         <cfparam name="form.phoneNo" type="telephone">
    <!--- Application code to update the database goes here. --->
    <cfoutput>
         <h3>The e-mail address and phone number for user #Form.UserID#
               have been added</h3>
    </cfoutput>
</cfif>


<!--- The form. --->
<cfform action="#CGI.SCRIPT_NAME#">
    User ID:<cfinput type="Text" name="UserID"><br>
    Phone: <cfinput type="Text" name="phoneNo"><br>
    E-mail: <cfinput type="Text" name="emailAddr"><br>
    <cfinput type="submit" name="saveSubmit" value="Save Data"><br>
</cfform>

The expresserr.cfm page looks as follows:

<cfoutput>
    You entered invalid data.<br>
    Please click the browser Back button and try again<br>
    #cferror.RootCause.detailMessage#
</cfoutput>


Using cftry and cfcatch tags
The self-posting form and action page looks as follows:

<!--- Use a Boolean variable to indicate whether all fields are good. --->
<cfset goodData="Yes">
<!--- Make sure the form was submitted. --->
<cfif isDefined("form.saveSubmit")>
<!--- The cftry block for testing the User ID value. --->
    <cftry>
<!--- The cfparam tag checks the field data types. --->
         <cfparam name="form.UserID" type="integer">
<!--- If the data is invalid, ColdFusion throws an expression exception. --->
<!--- Catch and handle the exception. --->
         <cfcatch type="expression">
               <!--- Set the data validity indicator to False. --->
               <cfset goodData="No">
               <cfoutput>
                    Invalid user ID.<br>
                    #cfcatch.detail#<br><br>
               </cfoutput>
         </cfcatch>
    </cftry>
<!--- The cftry block for testing the e-mail address value. --->
    <cftry>
         <cfparam name="form.emailAddr" type="email">




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                765
Requesting and Presenting Information




          <cfcatch type="expression">
               <cfset goodData="No">
               <cfoutput>
                    Invalid e-mail address.<br>
                    #cfcatch.detail#<br><br>
               </cfoutput>
          </cfcatch>
    </cftry>
<!--- The cftry block for testing the telephone number value. --->
    <cftry>
          <cfparam name="form.phoneNo" type="telephone">
          <cfcatch type="expression">
               <cfset goodData="No">
               <cfoutput>
                    Invalid telephone number.<br>
                    #cfcatch.detail#<br><br>
               </cfoutput>
          </cfcatch>
    </cftry>
    <!--- Do this only if the validity indicator was not set to False in a
               validation catch block. --->
    <cfif goodData>
          <!--- Application code to update the database goes here. --->
          <cfoutput>
               <h3>The e-mail address and phone number for user #Form.UserID#
                    have been added</h3>
          </cfoutput>
    </cfif> <!--- goodData is True--->
</cfif> <!--- Form was submitted. --->


<cfform action="#CGI.SCRIPT_NAME#" preservedata="Yes">
    User ID:<cfinput type="Text" name="UserID"><br>
    Phone: <cfinput type="Text" name="phoneNo"><br>
    E-mail: <cfinput type="Text" name="emailAddr"><br>
    <cfinput type="submit" name="saveSubmit" value="Save Data"><br>
</cfform>




Creating Forms in Flash

You can create effective forms in Adobe Flash format, in which Adobe ColdFusion displays forms using Flash, not
HTML.


About Flash forms
ColdFusion can deliver forms to the client in Flash (SWF file) format. ColdFusion automatically generates the Flash
binary from your CFML code and displays it on the client. Flash forms have the following advantages over HTML
forms:

* They are browser-independent. Flash Player works in all commonly used browsers on Windows and Macintosh
   systems, and in Netscape and Mozilla on Linux.

* By default, they present a modern, visually pleasing appearance, and you can apply predefined color skins or
   customize the appearance with specifications like those specifications in a Cascading Style Sheet (CSS).




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  766
Requesting and Presenting Information




* They let you develop complex, multipart forms that do not require multiple pages, by using tabbed or accordion-
   style dialog boxes.

* They automatically do much of the layout work for you.

   Note: Flash form configuration requirements differ from ColdFusion requirements. For example, Flash forms do not
   work sometimes with all J2EE servers supported by ColdFusion. For more information, see Installing ColdFusion.

In addition to creating Flash forms, ColdFusion lets you specify Flash format for cfcalendar, cftree, and cfgrid
tags. Use these tags to embed Flash calendar choosers, trees, and grids in HTML forms, to eliminate the need to use
Java applets. Information about using Flash grids and trees in HTML forms is not discussed here. However, the
information about grids and trees also applies to these elements.


A Flash form example
Flash forms provide many features that help you quickly create easy-to-use, professional-looking, complex forms. The
following image contains a two-tab form that shows many of these features:




This form includes the following features:

* Each tab contains a different section of the overall form, and users can enter data on both tabs before submitting
   the form. This technique can eliminate the need for multiple forms on multiple HTML pages.

* The first and last names are required fields, indicated by the red asterisks.

* The Flash form automatically fills the e-mail field with data from the name fields, but the user can override this
   information.

* When the user selects the date field, a calendar automatically opens for picking the date.


Flash form CFML differences from HTML forms
Because ColdFusion sends a Flash form to the client in SWF file format, everything inside a Flash form is rendered by
Flash. Rendering the form in Flash has several effects:

* Plain text and HTML tags in the body of a Flash Form have no effect.

* Specify all form content inside CFML tags that support Flash forms.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        767
Requesting and Presenting Information




* ColdFusion provides two tags that let you take advantage of Flash features and perform tasks that you would
   otherwise do in HTML: use the cfformitem tag to add text blocks and horizontal and vertical rules to your form,
   and you use the cfformgroup tag to structure your form.

* Standard ColdFusion forms tags, such as cfinput and cftree, include attributes that work only with Flash forms,
   and attribute values that let you specify form style and behavior. These tags include the skin attribute with many
   Flash-specific style attribute values for appearance, and the bind attribute for filling a field value with data from
   other fields.

   The reference pages for the individual tags in the CFML Reference describe the form tags and their features,
   indicating which attributes and values work with Flash forms.


Building Flash forms
You build Flash forms using standard ColdFusion form tags, plus the cfformgroup and cfformitem tags. These tags
create the elements of the form, as follows:

* The cfcalendar, cfgrid, cfinput, cfselect, cftextarea, and cftree tags create controls for data display and
   user input.

* The cfformitem tag lets you add formatted or unformatted text, spacers, and horizontal and vertical rules without
   using HTML.

* The cfformgroup tag creates containers, such as horizontally aligned boxes or tabbed navigators, that let you
   group, organize, and structure the form contents.

Flash forms follow a hierarchical structure of containers and children.

1 The cfform tag is the master container, and its contents are child containers and controls.

2 The cfformgroup tag defines a container that organizes its child elements.

3 All other tags create individual controls, including display elements such as rules.

   For example, the image in the About Flash forms section has the following hierarchical structure of containers and
   children. (This outline only shows the structure of the page that is visible in the image. It omits the structure of the
   Preferences tab.)

   1    cfform
   2          cfformgroup type="tabnavigator" -- Tab navigator container
   3               cfformgroup type="page" -- Tabbed page container, child of tabnavigator
   4                    cfformgroup type="horizontal" -- Aligns its two children horizontally
   5                          cfinput type="text" -- First name input control
   5                          cfinput type="spacer" -- Space between the name input controls
   5                          cfinput type="text" -- Last name input control
   4                    cfformitem type="hrule" -- Displays a rule
   4                    cfformitem type="html" -- Displays formatted text
   4                    cffinput type="text" -- E-mail input control
   4                    cfformitem type="hrule" -- Displays a rule
   4                    cfinput type="text" -- Phone number input control
   4                    cfinput type="spacer" -- Space between the phone and date controls
   4                    cfinput type="datefield" -- Date input control
   3               cfinput type="page" -- Second tabbed page container for preferences
                   .
                   .
   2          cfformgroup type="horizontal" -- Follows the tabnavigator in the form
   3          cfinput type="submit" -- Submit button control
   3          cfinput type="reset" -- Reset button control




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                       768
Requesting and Presenting Information




Adding text, images, rules, and space with the cfformitem tag
Because Flash forms do not support inline HTML, you use the cfformitem tag to add text blocks and horizontal and
vertical rules to your form. (Flash form controls, such as cfinput, use the label or value attribute to specify text
labels.) You can also use the cfformitem tag to add spacers between visual form elements.

The cfformitem type="hrule" and cfformitem type="vrule" tags put horizontal and vertical rules in the form.
You can specify the rule thickness, color, and shadow characteristics by using style specifications. For more
information on specifying rule styles, see Styles for cfformitem with hrule or vrule type attributes in ColdFusion Flash
Form Style Reference in the CFML Reference.

The cfformitem type="spacer" tag puts a blank space of the specified height and width on the form. This tag type
does not use styles; it can be useful in improving the form appearance by helping you control the form layout.

The cfformitem type="text" tag lets you insert plain text in the form You can use the style attribute to apply a
consistent format to the text.

The cfformitem type="html" tag lets you insert formatted text and images in the form. You can include basic
HTML-style formatting tags in the body of this tag to add images and to format and style the text.

You can use the following formatting tags and attributes in the body of a cfformitem type="html" tag:


 Tag                      Valid attributes

 a                        href URL to link to.

                          target window name; can be a standard HTML window name such as _blank.


 b                        None.

 br                       None.

 font                     color Must be in hexadecimal format, such as #FF00AA. Use a single number sign (#) character.

                          faceC an be a comma-delimited list of font face names; Flash uses the first font that is available on the client
                          system.

                          size In pixels; + and -relative values are allowed.


 i                        None.

 img                      See the attribute table for the img tag.

                          Note: Close this tag with /> or an </img> tag.

 li                       None.

 p                        align Must be one of the following: left, right, center.


 textformat               See the attribute table for the textformat tag.

 u                        None.


The img tag supports the following attributes:


 Attribute                Description

 src                      (Required) URL or path to a JPEG or SWF file. Images are not displayed until they have downloaded completely.
                          Flash Player does not support progressive JPEG files.

 width                    Width of the image, in pixels.

 height                   Height of the image in pixels.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    769
Requesting and Presenting Information




 Attribute              Description

 align                  Horizontal alignment of the embedded image within the text field. Valid values are left and right. The default
                        value is left.

 hspace                 Number of pixels of horizontal space that surround the image where no text appears. The default value is 8.

 vspace                 Number of pixels of vertical space that surround the image where no text appears. The default value is 8.


Note: Because of the Flash dynamic sizing rules, to ensure that the image displays properly, you sometimes have to specify
the formitem tag height attribute and the width and height attributes for the form or the containing cfformgroup
tag. Also, the image always displays on a new line, not inline with text, and text that follows the image in your code
occupies any available space to the right of the image.

The textformat tag is specific to Flash, and has the following attributes:


 Attribute               Description

 blockindent             Block indentation, in points.

 indent                  Indentation from the left margin to the first character in the paragraph.

 leading                 Amount of leading (vertical space) between lines.

 leftmargin              Left margin of the paragraph, in points.

 rightmargin             Right margin of the paragraph, in points.

 tabstops                Custom tab stops as an array of nonnegative integers. To specify tabs in text, use the \t escape character.


For detailed descriptions of these tags, see the Flash documentation.

The following code creates a simple Flash form that consists of a formatted text area surrounded by horizontal rules:

<cfform name="myform" height="220" width="400" format="Flash" >
      <!--- Use text formitem tag with style specifications for the heading. --->
      <cfformitem type="text" style="fontWeight:bold; fontSize:14;">
      Flash form with formatted text and rules
      </cfformitem>
      <!--- The spacer adds space between the text and the rule --->
      <cfformitem type="spacer" height="2" />
      <cfformitem type="hrule" />
      <cfformitem type="html">
           <b><font color="#FF0000" size="+4" face="serif">
           This form has formatted text, including:</font></b><br>
           <textformat blockindent="20" leading="2">
                <li>colored text</li>
                <li><i>italic and bold text</i></li>
                <li>a bulleted list in an indented block</li>
           </textformat>
           <p><b>The text is preceded and followed by horizontal rules</b></p>
           It also has a link to a web page.</b><br>
           <a href="http://www.adobe.com/" target="_blank">
           <font color="#0000FF"><u>
           This link displays the Adobe home page in a new browser window
           </u></font></a>
      </cfformitem>
      <cfformitem type="spacer" height="2"/>
      <cfformitem type="hrule"/>
</cfform>




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                             770
Requesting and Presenting Information




Using the cfformgroup tag to structure forms
ColdFusion provides form group container types that provide basic structure to a Flash form. You specify these types
in the type attribute of the cfformgroup tag. Use the following container types to control the layout of controls and
groups of controls:


 Type                    Description

 horizontal              Arranges individual controls horizontally, and optionally applies a label to the left of the controls. Use only for
                         arranging ColdFusion form controls, including cfformitem controls. As a general rule, do not use to organize
                         cfformgroup containers; use the hbox attribute instead.

                         If you put other cfformgroup tags inside a horizontal form group, the controls inside the included
                         cfformgroup tag do not align with other controls in the horizontal group.


 vertical                Arranges individual controls vertically, and optionally applies a label to the left (not top) of the controls. Use only
                         for groups of ColdFusion form controls, including cfformitem controls. As a general rule, do not use to organize
                         cfformgroup containers; use the vbox attribute instead.

                         If you put other cfformgroup tags inside a vertical form group, the controls inside the included
                         cfformgroup tag do not align with other controls in the vertical group.


 hbox                    Arranges groups of controls horizontally. Does not apply a label. Use this attribute value to arrange other
                         cfformgroup containers. This tag does not enforce alignment of child controls that have labels, so you should
                         not use it to align individual controls.

 vbox                    Arranges groups of controls vertically. Does not apply a label. Use this attribute value to arrange other
                         cfformgroup containers. This tag does not enforce alignment of child controls that have labels, so do not use it
                         to align individual controls.

 hdividedbox             Arranges two or more children horizontally, and places divider handles between the children that users can drag
                         to change the relative sizes of the children. Does not apply a label. The direct children of an hdividedbox
                         container must be cfformgroup tags with type attributes other than horizontal or vertical.

 vdividedbox             Arranges two or more children vertically, and places divider handles between the children that users can drag to
                         change the relative sizes of the children. Does not apply a label. The direct children of a vdividedbox container
                         must be cfformgroup tags with type attributes other than horizontal or vertical.

 tile                    Arranges its children in a rectangular grid in row-first order. Does not apply a label.

 panel                   Consists of a title bar containing the label attribute text, a border, and a content area with vertically arranged
                         children.

 accordion               Places each of its child pages in an accordion pleat with a label bar. Displays the contents of one pleat at a time.
                         Users click the labels to expand or contract the pleat pages. Does not apply a label.

 tabnavigator            Places each of its children on a tabbed page. Users click the tabs to display a selected page. Does not apply a label.

 page                    The immediate child of an accordion or tab navigator container. Specifies the label on the pleat bar or tab, and
                         arranges its child containers and controls vertically.


For more information on using the accordion, tabnavigator, and pagecfformgroup types, see "Creating complex
forms with accordion and tab navigator containers" on page 773.


Example: structuring with the cfformgroup tag
The following example shows a form with an hdividedbox container with two vbox containers. The left box uses a
horizontal container to arrange two radio buttons. The right box uses a tile container to lay out its check boxes.
You can drag the divider handle to resize the two boxes. When you submit the form, the ColdFusion page dumps the
Form scope to show the submitted data.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                 771
Requesting and Presenting Information




<cfif Isdefined("Form.fieldnames")>
<cfdump var="#form#" label="form scope">
<br><br>
</cfif>


<cfform name="myform" height="200" width="460" format="Flash" skin="HaloBlue">
    <cfformitem type="html" height="20">
         <b>Tell us your preferences</b>
    </cfformitem>
    <!--- Put the pet selectors to the left of the fruit selectors. --->
    <cfformgroup type="hdividedbox" >
    <!--- Group the pet selector box contents, aligned vertically. --->
         <cfformgroup type="VBox"height="130">
               <cfformitem type="text" height="20">
                    Pets:
               </cfformitem>
               <cfformgroup type="vertical" height="80">
                    <cfinput type="Radio" name="pets" label="Dogs" value="Dogs"
                           checked>
                    <cfinput type="Radio" name="pets" label="Cats" value="Cats">
               </cfformgroup>
         </cfformgroup>


    <!--- Group the fruit selector box contents, aligned vertically. --->
         <cfformgroup type="VBox" height="130">
               <cfformitem type="text" height="20">
                    Fruits:
               </cfformitem>
               <cfformgroup type="tile" height="80" width="190" label="Tile box">
                    <--- Flash requires unique names for all controls --->
                    <cfinput type = "Checkbox" name="chk1" Label="Apples"
                           value="Apples">
                    <cfinput type="Checkbox" name="chk2" Label="Bananas"
                           value="Bananas">
                    <cfinput type="Checkbox" name="chk3" Label="Pears"
                           value="Pears">
                    <cfinput type="Checkbox" name="chk4" Label="Oranges"
                    value="Oranges">
                    <cfinput type="Checkbox" name="chk5" Label="Grapes"
                           value="Grapes">
                    <cfinput type="Checkbox" name="chk6" Label="Cumquats"
                           value="Cumquats">
               </cfformgroup>
         </cfformgroup>
    </cfformgroup>
    <cfformgroup type="horizontal">
         <cfinput type="submit" name="submit" width="100" value="Show Results">
         <cfinput type="reset" name="reset" width="100" value="Reset Fields">
    </cfformgroup>
</cfform>




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          772
Requesting and Presenting Information




Controlling sizes in Flash forms
Sizing elements in a Flash form is something of an art, rather than a science. As a general rule, if you don't specify the
height and width attributes, Flash tends to do a good job of laying out the form. However, keep in mind the following
considerations:

* If you do not specify the height and width attributes in the cfform tag, Flash reserves the full dimensions of the
   visible browser window, if the form is not in a table, or the table cell, if the form is in a table, even if they are not
   required for the form contents. Any HTML output that precedes or follows the form causes the output page to
   exceed the size of the browser window.

* If you do not specify the height or width of a control, including a form group, Flash adjusts the dimensions, trying
   to fit the controls in the available space. For example, Flash often extends input boxes to the width of the containing
   control, if not otherwise specified.

   In general, it is best to use the following process when you design your Flash form.


Determine the sizes of a Flash form and its controls
1 When you first create the form, don't specify any height and width attributes on the form or its child tags. Run
   the form and examine the results to determine height and width values to use.

2 Specify height and width attributes in the cfform tag for the desired dimensions of the form. You can specify
   absolute pixel values, or percentage values relative to the size of the containing window.

3 Specify any height or width attributes on individual tags. These values must be in pixels.

4 Repeat step 3 for various tags, and possibly step 2, until your form has a pleasing appearance.


Repeating Flash form elements based on query data
The repeatercfformgroup type tells Flash Player to iterate over a query and create a set of the cfformgroup tag's
child controls for each row in the query. For each set of child controls, bind attributes in the child tags can access fields
in the current query row. This cfformgroup type lets you create Flash forms where the number of controls can change
based on a query, without requiring ColdFusion to recompile the Flash SWF file for the form. This significantly
enhances server performance.

Note: For more information on binding data, see "Binding data in Flash forms" on page 776.

Optionally, you can specify a start row and a maximum number of rows to use in the repeater. Unlike most
ColdFusion tags, repeater index values start at 0, not 1. To specify a repeater that starts on the first line of the query
object and uses no more than 15 rows, use a tag such as the following:

<cfformgroup type="repeater" query="q1" startrow="0" maxrows="15">

One example that can use a repeater is a form that lets a teacher select a specific class and update the student grades.
Each class can have a different number of students, so the form must have a varying number of input lines. Another
example is a shopping cart application that displays the product name and quantity ordered and lets users change the
quantity.

The following example uses the cfformgroup tag with a repeatertype attribute value to populate a form. It creates
a query, and uses the repeater to iterate over a query and create a firstname and lastname input box for each row in the
query.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    773
Requesting and Presenting Information




<cfif IsDefined("Form.fieldnames")>
    <cfdump var="#form#" label="form scope">
    <br><br>
</cfif>
<cfscript>
    q1 = queryNew("id,firstname,lastname");
    queryAddRow(q1);
    querySetCell(q1, "id", "1");
    querySetCell(q1, "firstname", "Rob");
    querySetCell(q1, "lastname", "Smith");
    queryAddRow(q1);
    querySetCell(q1, "id", "2");
    querySetCell(q1, "firstname", "John");
    querySetCell(q1, "lastname", "Doe");
    queryAddRow(q1);
    querySetCell(q1, "id", "3");
    querySetCell(q1, "firstname", "Jane");
    querySetCell(q1, "lastname", "Doe");
    queryAddRow(q1);
    querySetCell(q1, "id", "4");
    querySetCell(q1, "firstname", "Erik");
    querySetCell(q1, "lastname", "Pramenter");
</cfscript>


<cfform name="form1" format="flash" height="220" width="450">
    <cfselect label="select a teacher" name="sel1" query="q1" value="id"
         display="firstname" width="100" />
    <cfformgroup type="repeater" query="q1">
         <cfformgroup type="horizontal" label="name">
               <cfinput type="Text" name="fname"bind="{q1.currentItem.firstname}">
               <cfinput type="Text" name="lname" bind="{q1.currentItem.lastname}">
         </cfformgroup>
    </cfformgroup>
    <cfinput type="submit" name="submitBtn" value="Send Data" width="100">
</cfform>


Creating complex forms with accordion and tab navigator containers
The accordion and tabnavigator attributes of the cfformgroup tag let you construct complex forms that would
otherwise require multiple HTML pages. With accordions and tab navigator containers, users can switch among
multiple entry areas without submitting intermediate forms. All data that they enter is available until they submit the
form, and all form elements load at one time.




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     774
Requesting and Presenting Information




An accordion container places each logical form page on an accordion pleat. Each pleat has a label bar; when the user
clicks a bar, the current page collapses and the selected page expands to fill the available form space. The following
image shows a three-pleat accordion, open to the middle pleat, Preferences:




A tab navigator container places each logical form page on a tabbed frame. When the user clicks a tab, the selected page
replaces the previous page. The image in About Flash forms shows a tab navigator container.

The following example generates a two-tab navigator container that gets contact information and preferences. You can
change it to an accordion container by changing the type attribute of the first cfformgroup tag from tabnavigator
to accordion. To prevent the accordion from having scroll bars, increase the cfform tag height attribute to 310 and
the tabnavigator tag height attribute to 260.

<cfif IsDefined("Form.fieldnames")>
     <cfdump var="#form#" label="form scope">
     <br><br>
</cfif>
<br>
<cfform name="myform" height="285" width="480" format="Flash" skin="HaloBlue">
     <cfformgroup type="tabnavigator" height="240" style="marginTop: 0">
          <cfformgroup type="page" label="Contact Information">
                <!--- Align the first and last name fields horizontally. --->
                <cfformgroup type="horizontal" label="Your Name">
                     <cfinput type="text" required="Yes" name="firstName" label="First"
                           value="" width="100"/>
                     <cfinput type="text" required="Yes" name="lastName" label="Last"
                           value="" width="100"/>
                </cfformgroup>
                <cfformitem type="hrule" />
                <cfformitem type="HTML"><textformat indent="95"><font size="-2">
                     Flash fills this field in automatically.
                     You can replace the text.
                     </font></textformat>
                </cfformitem>
                <!--- The bind attribute gets the field contents from the firstName
                           and lastName fields as they get filled in. --->
                <cfinput type="text" name="email" label="email"
                     bind="{firstName.text}.{lastName.text}@mm.com">
                <cfformitem type="spacer" height="3" />
                <cfformitem type="hrule" />
                <cfformitem type="spacer" height="3" />


                <cfinput type="text" name="phone" validate="telephone" required="no"




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                             775
Requesting and Presenting Information




                    label="Phone Number">
               <cfinput type="datefield" name="mydate1" label="Requested date">
         </cfformgroup>


         <cfformgroup type="page" label="Preferences" style="marginTop: 0">
               <cfformitem type="html" height="20">
                    <b>Tell us your preferences</b>
               </cfformitem>
               <!--- Put the pet selectors to the left of the fruit selectors. --->
               <cfformgroup type="hdividedbox" >
               <!--- Group the pet selector box contents, aligned vertically. --->
                    <cfformgroup type="VBox"height="130">
                           <cfformitem type="text" height="20">
                               Pets:
                           </cfformitem>
                           <cfformgroup type="vertical" height="80">
                               <cfinput type="Radio" name="pets" label="Dogs" value="Dogs"
                                     checked>
                               <cfinput type="Radio" name="pets" label="Cats" value="Cats">
                           </cfformgroup>
                    </cfformgroup>


               <!--- Group the fruit selector box contents, aligned vertically. --->
                    <cfformgroup type="VBox" height="130">
                           <cfformitem type="text" height="20">
                               Fruits:
                           </cfformitem>
                           <cfformgroup type="tile" height="80" width="190" label="Tile box">
                               <--- Flash requires unique names for all controls. --->
                               <cfinput type = "Checkbox" name="chk1" Label="Apples"
                                     value="Apples">
                               <cfinput type="Checkbox" name="chk2" Label="Bananas"
                                     value="Bananas">
                               <cfinput type="Checkbox" name="chk3" Label="Pears"
                                     value="Pears">
                               <cfinput type="Checkbox" name="chk4" Label="Oranges"
                                     value="Oranges">
                               <cfinput type="Checkbox" name="chk5" Label="Grapes"
                                     value="Grapes">
                               <cfinput type="Checkbox" name="chk6" Label="Kumquats"
                                     value="Cumquats">
                           </cfformgroup>
                    </cfformgroup>
               </cfformgroup>
         </cfformgroup>
    </cfformgroup>


    <cfformgroup type="horizontal">
         <cfinput type = "submit" name="submit" width="100" value = "Show Results">
         <cfinput type = "reset" name="reset" width="100" value = "Reset Fields">
    </cfformgroup>
</cfform>




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      776
Requesting and Presenting Information




Binding data in Flash forms
The bind attribute lets you set the value of the fields using the contents of other form fields. You can use the bind
attribute with the cftextarea tag and any cfinput type that takes a value, including hidden. This data binding
occurs dynamically as the user enters data within Flash on the client system. Flash does not send any information to
ColdFusion until the user submits the form. To use the bind attribute to specify the field value, use the following
formats:


 Data source                         bind attribute format

 cfinputtype="text" or               bind="{sourceName.text}"
 cftextarea text

 cfinput selected radio button       bind="{sourceName.selectedData}"

 cftree selected item                bind="{sourceName.selectedNode.getProperty('data').value}

 cfgrid selected item                bind="{sourceName.selectedItem.COLUMNAME}"

 cfselect selected item              bind="{sourceName.selectedItem.data}"


Note: If you use the bind attribute, you cannot use the value attribute.

The following rules and techniques apply to the binding formats:

* The sourceName value in these formats is the name attribute of the tag that contains the element that you are
   binding to.

* You can bind to additional information about a selected item in a tree. Replace value with display to get the
   displayed value, or with path to get the path to the node in the tree.

* You can bind to the displayed value of a cfselect item by replacing data with label.

* If the user selects multiple items in a cfselect control, the selectedItem object contains the most recent selection,
   and a selectedItems array contains all selected items. You can access the individual values in the array, as in
   myTree.selectedItems[1].data. The selectedItems array exists only if the user selects multiple items; otherwise, it is
   undefined.

* You can use ActionScript expressions in Flash bind statements.

   The following example shows how to use the values from the firstName and lastName fields to construct an e-mail
   address. The user can change or replace this value with a typed entry.

    <cfformgroup type="horizontal" label="Your Name">
         <cfinput type="text" required="Yes" name="firstName" label="First"
              value="" width="100"/>
         <cfinput type="text" required="Yes" name="lastName" label="Last"
              value="" width="100"/>
   </cfformgroup>
   <cfinput type="text" name="email" label="email"
         bind="{firstName.text}.{lastName.text}@mm.com">



Setting styles and skins in Flash forms
ColdFusion provides the following methods for controlling the style and appearance of Flash forms and their elements:

Skins provide a simple method for controlling the overall appearance of your form. A single skin controls the entire
form.

Styles provide a finer-grained level of control than skins. Each style specifies a particular characteristic for a single
control. Many styles are also inherited by a control's children.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  777
Requesting and Presenting Information




You can use both techniques in combination: you can specify a skin for your form and use styles to specify the
appearance (such as input text font) of individual controls.

For detailed information on the style names and values that you can use, see ColdFusion Flash Form Style Reference
in the CFML Reference.


Controlling form appearance with Flash skins
The cfform tag takes a skin attribute, which lets you select an overall appearance for your form. The skin determines
the color used for highlighted and selected elements.

You can select the following Flash skins:

* haloBlue

* haloGreen (the default)

* haloOrange

* haloSilver


About Flash form styles
The ColdFusion Flash form tags have a style attribute that lets you specify control characteristics using CSS syntax.
You can specify a style attribute in the following tags:

* cfform

* cfformgroup

* cfcalendar

* cfformitem, types hrule and vrule

* cfgrid

* cfinput

* cfselect

* cftextarea

* cftree

   The attributes for the cfform and cfformgroup generally apply to all the form or form group's children.

   Flash supports many, but not all, standard CSS styles. ColdFusion Flash forms only support applying specific CSS
   style specifications to individual CFML tags and their corresponding Flash controls and groups. You cannot use an
   external style sheet or define a document-level style sheet, as you can for HTML forms.


Flash form style specification syntax
To specify a Flash style, use the following format:

style="stylename1: value; stylename2: value; ..."

For example, the following code specifies three style values for a text input control:

<cfinput type="text" name="text2" label="Last"
     style="borderSyle:inset; fontSize:12; backgroundColor:##FFEEFF">


About Flash form style value formats
Style properties can be Boolean values, strings, numbers, or arrays of these values.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        778
Requesting and Presenting Information




Length format
You specify styles that take length or dimension values, including font sizes, in pixels.

The fontSize style property lets you use a set of keywords in addition to numbered units. You can use the following
keywords when you set the fontSize style property. The exact sizes are defined by the client browser.

* xx-small

* x-small

* small

* medium

* large

* x-large

* xx-large

   The following cfinput tag uses the style attribute with a fontSize keyword to specify the size of the text in the
   input box:

   <cfinput type="text" name="text1" style="fontSize:X-large" label="Name">


Time format
You specify styles that take time values, such as the openDuration style that specifies how fast an accordion pleat
opens, in milliseconds. The following example shows an accordion tag that takes one-half second to change between
accordion pleats:

<cfformgroup type="accordion" height="260" style="openDuration: 500">


Color format
You define color values, such as those for the backgroundColor style, in the following formats:


 Format              Description

 hexadecimal         Hexadecimal colors are represented by a six-digit code preceded by two number sign characters (##). Two # characters
                     are required to prevent ColdFusion from interpreting the character. The range of possible values is ##000000 to
                     ##FFFFFF.

 VGA color names     VGA color names are a set of 16 basic colors supported by all browsers that support CSS. The available color names are
                     Aqua, Black, Blue, Fuchsia, Gray, Green, Lime, Maroon, Navy, Olive, Purple, Red, Silver, Teal, White, and
                     Yellow. Some browsers support a larger list of color names. VGA color names are not case-sensitive.


Some styles support only the hexadecimal color format.

Some controls accept multiple colors. For example, the tree control's depthColors style property can use a different
background color for each level in the tree. To assign multiple colors, use a comma-delimited list, as the following
example shows:

style="depthColors: ##EAEAEA, ##FF22CC, ##FFFFFF"


About Flash form style applicability and inheritance
Because of the way Flash control styles are implemented, some common styles are valid, but have no effect, in some
tags. Therefore, in the table in Styles valid for all controls in ColdFusion Flash Form Style Reference in the CFML
Reference, the listed styles do not cause errors when used in controls, but might not have any effect.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           779
Requesting and Presenting Information




Styles can be inheritable or noninheritable. If a style is noninheritable, it only affects the tag, and does not affect any of
its children. For example, to maintain a consistent background color in an hbox form group and its children tags,
specify the color in all tags. If a style is inheritable, it applies to the tag and its children.


Example: applying styles to a Flash form
The following code creates a Flash form that uses a skin and styles to control its appearance.

The code for the form is as follows. Comments in the code explain how formatting controls and styles determine the
appearance.

<!--- Specify the form height and width, use the HaloBlue skin.
            Note: Flash ignores a backgroundColor style set in cfform. --->
<cfform name="myform" height="390" width="440" format="Flash" skin="HaloBlue">
     <!--- The input area is a panel. Styles to specify panel characteristics.
                 Child controls inherit the background color and font settings. --->
     <cfformgroup type="Panel" label="Contact Information"
               style="marginTop:20; marginBottom:20; fontSize:14; fontStyle:italic;
               headerColors:##FFFF00, ##999900; backgroundColor:##FFFFEE;
               headerHeight:35; cornerRadius:12">
           <!--- This vbox sets the font size and style, and spacing between and
                      around its child controls. --->
           <cfformgroup type="vbox" style="fontSize:12; fontStyle:normal;
                     verticalGap:18; marginLeft:10; marginRight:10">
               <!--- Use a horizontal group to align the first and last name fields
                            and set a common label. --->
               <cfformgroup type="horizontal" label="Name" >
                     <!--- Use text styles to highlight the entered names. --->
                     <cfinput type="text" required="Yes" name="firstName" label="First"
                           value="" width="120" style="color:##006090; fontSize:12;
                           fontWeight:bold" />
                     <cfinput type="text" required="Yes" name="lastName" label="Last"
                           value="" width="120" style="color:##006090; fontSize:12;
                           fontWeight:bold"/>
               </cfformgroup>
               <!--- Horizontal rules surround the e-mail address.
                            Styles specify the rule characteristics. --->
               <cfformitem type="hrule" style="color:##999900; shadowColor:##DDDD66;
                     strokeWidth:4"/>
               <cfformitem type="HTML" Style="marginTop:0; marginBottom:0">
                     <textformat indent="57"> <font size="-1">Flash fills this field in




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       780
Requesting and Presenting Information




                     automatically. You can replace the text.</font></textformat>
               </cfformitem>
               <cfinput type="text" name="email" label="email"
                     bind="{firstName.text}.{lastName.text}@mm.com">
               <cfformitem type="hrule" style="color:##999900; shadowColor:##DDDD66;
                     strokeWidth:4"/>
               <cfinput type="text" name="phone" validate="telephone" label="Phone">
               <!--- Styles control the colors of the current, selected, and
                     rolled-over dates. --->
               <cfinput type="datefield" name="mydate1" label="Date"
                     style="rollOverColor:##DDDDFF; selectionColor:##0000FF;
                     todayColor:##AAAAFF">
           </cfformgroup> <!--- vbox --->
     </cfformgroup> <!--- panel --->
     <!--- A style centers the buttons at the bottom of the form. --->
     <cfformgroup type="horizontal"style="horizontalAlign:center">
           <cfinput type = "submit" name="submit" width="100" value = "Show Results">
           <cfinput type = "reset" name="reset" width="100" value = "Reset Fields">
     </cfformgroup>
</cfform>



Using ActionScript in Flash forms
ActionScript 2.0 is a powerful scripting language that is used in Flash and other related products and is like JavaScript.
You can use a subset of ActionScript 2.0 code in your Flash forms.

Information on how to include ActionScript in your Flash forms, and restrictions and additions to ActionScript that
apply to ColdFusion Flash forms are described here. However, information on writing ActionScript is not provided.
For details on ActionScript and how you can use it, see the Flash ActionScript 2.0 documentation, including the
documents available in the Flash and Flex sections of LiveDocs at www.adobe.com/go/learn_cfu_docs_en.


Using ActionScript code in CFML
You can use ActionScript in the following attribute of tags in CFML Flash format forms:

* Form and control events, such as the onSubmit attribute of the cfform tag, or the onChange and onClick
   attributes of the cfinput tag. The attribute description on the tag reference pages in the CFML Reference list the
   event attributes.

* Bind expressions, which you can use to set field values. For more information on binding data, see "Binding data
   in Flash forms" on page 776.

   Your ActionScript code can be inline in the form attribute specification, you can make a call to a custom function
   that you define, or you can use the ActionScript include command in the attribute specification to get the
   ActionScript from a .as file.

   The following example shows a simple Fahrenheit to Celsius converter that does the conversion directly on the
   client, without requiring the user to submit a form to the ColdFusion server.

   <cfform format="flash" width="200" height="150">
        <cfinput type="text" name="fahrenheit" label="Fahrenheit" width="100"
              value="68">
        <cfinput type="text" name="celsius" label="Celsius" width="100">
        <cfinput type="button" name="convert" value="Convert" width="100"
              onClick="celsius.text = Math.round((farenheit.text-32)/1.8*10)/10">
   </cfform>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       781
Requesting and Presenting Information




   Note: You do not use the text property (for example, fieldname.text) to access hidden fields. To access a hidden field,
   use the format formname.fieldname = 'value'.


Custom ActionScript functions
Custom ActionScript functions are the equivalent of CFML UDFs. You can define your own functions in ColdFusion
by using the cfformitem tag with a type attribute value of script, or you can define the functions in an ActionScript
(.as) file. Also, ColdFusion includes a small number of predefined custom ActionScript functions that you can use in
your Flash form controls.

You can use the following custom functions in the ActionScript for all form controls to reset or submit the form:

* resetForm()

* submitForm()

You can use the following custom functions in cfgrid tags only to insert and delete rows in the grid:

* GridData.insertRow(gridName)

* GridData.deleteRow(gridName)

The following example shows how you can use the two GridData functions to add custom buttons that add and delete
rows from a Flash form. These buttons are equivalent to the buttons that ColdFusion creates if you specify
insert="yes" and delete="yes" in the cfgrid tag, but they allow you to specify you own button text and
placement. This example puts the buttons on the side of the grid, instead of below it and uses longer than standard
button labels.

<cfform format="flash" height="265" width="400">
      <cfformitem type="html">
            You can edit this grid as follows:
            <ul>
            <li>To change an item, click the field and type.</li>
            <li>To add a row, click the Insert Row button and type in the fields
                  in the highlighted row.</li>
            <li>To delete a row, click anywhere in the row and click the
                  Delete Row button</li>
            </ul>
            <p><b>When you are done, click the submit button.</b></p>
      </cfformitem>
      <!--- The hbox aligns the grid and the button vbox horizontally --->
      <cfformgroup type="hbox" style="verticalAlign:bottom;
                  horizontalAlign:center">
            <!--- To make all elements align properly, all of the hbox children must
                  be containers, so we must put the cfgrid tag in a vbox tag. --->
            <cfformgroup type="vbox">
                  <!-- An editable grid with hard coded data (for simplicity).
                      By default, this grid does not have insert or delete buttons. --->
                  <cfgrid name="mygrid" height="120" width="250" selectmode="edit">
                      <cfgridcolumn name="city">
                      <cfgridcolumn name="state">
                      <cfgridrow data="Rockville,MD">
                      <cfgridrow data="Washington,DC">
                      <cfgridrow data="Arlington,VA">
                  </cfgrid>
            </cfformgroup>
            <!--- Group the Insert and Delete buttons vertically;
                      use a vbox to ensure correct alignment. --->




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                             782
Requesting and Presenting Information




           <cfformgroup type="vbox" name="buttons"style="verticalAlign:bottom;
                     horizontalAlign:center">
                <!--- Use a spacer to position the buttons. --->
                <cfformitem type="spacer" height="18" />
                <!--- Use the insertRow method in the onClick event to add a row. --->
                <cfinput type="button" name="ins" value="Insert a new row" width="125"
                     onClick="GridData.insertRow(mygrid);">
                <!--- Use the deleteRow method in the onClick event to delete
                     the selected row --->
                <cfinput type="button" name="del" value="Delete selected row"
                           width="125" onClick="GridData.deleteRow(mygrid)">
                <cfinput type="submit" name="f1" value="Submit" width="125">
           </cfformgroup>
      </cfformgroup>
</cfform>


<!--- Dump the form if it has been submitted. --->
<cfif IsDefined("form.fieldnames")>
<cfdump var="#form#"><br>
</cfif>



Best practices for Flash forms

Minimizing form recompilation
Flash forms are sent to the client as SWF files, which ColdFusion must compile from your CFML code. The following
techniques can help limit how frequently ColdFusion must recompile a Flash form.

* Only data must be dynamic. Whenever a variable name changes, or a form characteristic, such as an element width
    or a label changes, the Flash output must be recompiled. If a data value changes, the output does not need to be
    recompiled.

* Use cfformgroup type="repeater" if you must loop no more than ten times over no more than ten elements.
    This tag does not require recompiling when the number of elements changes. It does have a processing overhead
    that increases with the number of loops and elements, however, so for large data sets or many elements, it is often
    more efficient not to use the repeater.


Caching data in Flash forms
The cfform tag timeout attribute specifies how many seconds ColdFusion retains Flash form data on the server.
When a Flash form is generated, the values for the form are stored in memory on the server. When the Flash form is
loaded on the client, it requests these form values from the server. If this attribute is 0, the default, the data on the server
is immediately deleted after the data has been requested from the Flash form.

A Flash form can be reloaded multiple times if a user displays a page with a Flash form, goes to another page, and uses
the browser Back button to return to the page with the form. This behavior is common with search forms, login forms,
and the like. When the user returns to the original page:

* If the timeout value is 0, or the time-out period has expired, the data is no longer available, and ColdFusion returns
    a data-expired exception to the browser; in this case, the browser typically tells the user to reload the page.

* If the time-out has not expired, the browser displays the original data.

    If your form data contains sensitive information, such as credit card numbers or social security numbers, leave the
    time-out set to 0. Otherwise, consider setting a time-out value that corresponds to a small number of minutes.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       783
Requesting and Presenting Information




Using Flash forms in a clustered environment
Flash forms require sticky sessions when used in a cluster.



Creating Skinnable XML Forms

You can create XML skinnable forms, which are forms that generate XForms-compliant XML and are normally
formatted using an XSLT (extensible stylesheet language transformations) skin.

You can use XML skinnable forms with the skins that Adobe ColdFusion provides without having any knowledge of
either XML or XSLT. For information on using XML with ColdFusion, see "Using XML and WDDX" on page 1058.


About XML skinnable forms
A ColdFusion form with a format="XML" attribute is an XML skinnable form. When ColdFusion processes an XML
skinnable form, it generates the form as XML. By default, it applies an XML Stylesheet Language Transform (XSLT)
skin to the XML and generates a formatted HTML page for display on the user's browser. Optionally, you can specify
an XSLT file, or you can process the raw XML in your ColdFusion page.

By using XML skinnable forms, you can control the type and appearance of the forms that ColdFusion generates and
displays. ColdFusion provides a set of standard skins, including a default skin that it uses if you do not specify another
skin (or tell it not to apply a skin). You can also create your own XSLT skin and have ColdFusion use it to give your
forms a specific style or appearance.


ColdFusion forms and XForms
ColdFusion skinnable forms conform to and extend the W3C XForms specification. This specification provides an
XML syntax for defining interactive forms using a syntax that is independent of form appearance. ColdFusion forms
tags include attributes that provide information that does not correspond directly to the XForms model, such as
appearance information, validation rules, and standard HTML attributes. ColdFusion skinnable forms retain this
information in XForms extensions so that an XSL transformation can use the values to determine appearance or do
other processing.

For more information on XML structure of ColdFusion skinnable forms, see "ColdFusion XML format" on page 788.


The role of the XSLT skin
An XSLT skin and associated cascading style sheet (CSS) determine how an XML skinnable form is processed and
displayed, as follows:

* The XSLT skin tells ColdFusion how to process the XML, and typically converts it to HTML for display. The skin
   specifies the CSS style sheet to use to format the output.

* The CSS style sheet specifies style definitions that determine the appearance of the generated output.

XSLT skins give you extensive freedom in the generated output. They let you create a custom appearance for your
forms, or even different appearances for different purposes. For example, you could use the same form in an intranet
and on the Internet, and change only the skin to give a different appearance (or even select different subsets of the form
for display). You can also create skins that process your form for devices, such as wireless mobile devices.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     784
Requesting and Presenting Information




How ColdFusion processes XML skinnable forms
When ColdFusion processes a cfform tag that specifies XML format and an XSLT skin, it does the following to the form:

1 Converts the CFML form tags into an XForms-compliant XML text format and makes it available in a variable with
   the same name as the form. ColdFusion ignores inline text or HTML tags in the form, and does not pass them to
   the XML. (It does process HTML option tags that are children of a cfselect tag.)

2 Applies an XSLT skin to the XML; for example, to convert the form into HTML. The XSLT file specifies the CSS
   style sheet.

3 Returns the resulting, styled, form to the client, such as a user's browser.

If you omit the cfform tag skin attribute, ColdFusion uses a default skin.

If you specify skin="none", ColdFusion performs the first step, but omits the remaining steps. Your application must
handle the XML version of the form as needed. This technique lets you specify your own XSL engine, or incorporate
the form as part of a larger form.


ColdFusion XSL skins
ColdFusion provides the following XSLT skins:

* basic

* basiccss

* basiccss_top

* beige

* blue

* default

* lightgray

* red

* silver

The XSLT skin files are located in the cf_webroot\CFIDE\scripts\xsl directory, and the CSS files that they use for style
definitions are located in the cf_webroot\CFIDE\scripts\css directory.

The default skin and the basic skin format forms identically. ColdFusion uses the default skin if you do not specify a
skin attribute in the cfform tag. The default and basic skins are simple skins that use tables for arranging the form
contents. The basic skin uses div and span tags to arrange the elements. The skins with names of colors are like the
basic skin, but make more use of color.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    785
Requesting and Presenting Information




Example: a simple skinnable form
The following image shows a simple XML skinnable form that uses the default skin to format the output:




This form is used in the examples and description.


Building XML skinnable forms
You build ColdFusion XML skinnable forms using standard ColdFusion forms tags, including cfformgroup and
cfformitem tags. These tags create the elements of the form, the building blocks of the form.

ColdFusion converts the following tags to XML for processing by the XSLT:

Standard ColdFusion form data control tags The cfgrid, cfinput, cfselect, cfslider, cftextarea, and tree
tags specify the controls that the form displays.

cfformitem tags Add individual items to your form, such as text or rules. The valid types depend on the skin.

cfformgroup tags Group, organize, and structure the form contents. The valid types depend on the skin.

These tags are designed so you can develop forms in a hierarchical structure of containers and children. Using this
model, the cfform tag is the master container, and its contents are children containers and controls. Each
cfformgroup tag defines a container that organizes its child elements.

The specific tags and attributes that you use in your form depend on the capabilities of the XSLT skin. You use only
the tag and attribute combinations that the skin supports. If you are using a skin provided by a third party, make sure
that the supplier provides information on the supported attributes.


Using standard ColdFusion form tags
You use standard ColdFusion form tags, such as cfinput or cftree, as you normally do in standard CFML forms to
generate form input elements. ColdFusion maps most of these tags and their subtags (such as option tags in the
cfselect tag) to equivalent XForms elements. ColdFusion maps applet and Flash format cfgrid and cftree tags to
ColdFusion XML extensions that contain Java applet or Flash objects. It converts XML format cfgrid and cftree
tags to ColdFusion XML extension.

The specific attributes you can use and their meanings can depend on the skins.

Using ColdFusion skins: The skins that are supplied with ColdFusion support the attributes that you can use with
HTML forms. You can also use label attributes to provide labels for the following tags:

* cfinput with type attribute values of text, button, password, and file



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     786
Requesting and Presenting Information




* cfselect

* cfslider

* cftextarea

Using other skins: If you use any other skin, some attributes are not supported, or the skin supports custom attributes.
Get the information about the supported attributes from the XSLT skin developer.


Using cfformitem tags
ColdFusion does not process inline text or standard HTML tags when it generates an XML form; therefore, you use
the cfformitem tag to add formatted HTML or plain text blocks and any other display elements, such as horizontal
and vertical rules, to your form.

ColdFusion converts all cfformitemtype attribute values to all-lowercase. For example, if you specify
type="MyType" ColdFusion converts the type name to "mytype".

ColdFusion makes no other limitations on the cfformitemtype attributes that you can use in a form, but the XSLT
skin must process the attributes to display the items.

Using ColdFusion skins: The skins provided in ColdFusion support the following cfformitem types:

* hrule

* text

* html

The hrule type inserts an HTML hr tag, and the text type displays unformatted plain text.

The html type displays HTML-formatted text. You can include standard HTML text markup tags, such as strong, p,
ul, or li, and their attributes. For example, the following text from the Example: a simple skinnable form section
shows how you could use a cfformitem tag to insert descriptive text in a form:

<cfformitem type="html">
     <b>We value your input</b>.<br>
     <em>Please tell us a little about yourself and your thoughts.</em>
</cfformitem>

Using other skins: If you use any other skin, the supported attributes and attribute values depend on the skin
implementation. Get the information about the supported attributes and attribute values from the XSLT skin
developer.


Using cfformgroup tags
The cfformgroup tag lets you structure forms by organizing its child tags, for example, to align them horizontally or
vertically. Some skins use cfformgroup tags for more complex formatting, such as tabbed navigator or accordion
containers. ColdFusion makes no limitations on the type attributes that you can use in a form, but the XSLT must
process the resulting XML to affect the display.

Using ColdFusion skins: The skins provided in ColdFusion support the following type attribute values:

* horizontal

* vertical

* fieldset

The horizontal and vertical types arrange their child tags in the specified direction and place a label to the left of
the group of children. The following text from the Example: a simple skinnable form section shows how you could use
a cfformgroup tag to apply a Name label and align first and last name fields horizontally:



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    787
Requesting and Presenting Information




<cfformgroup type="horizontal" label="Name">
     <cfinput type="text" name="firstname" label="First" required="yes">
     <cfinput type="text" name="lastname" label="Last" required="yes">
</cfformgroup>

The fieldset type corresponds to the HTML fieldset tag, and groups its children by drawing a box around them
and replacing part of the top line with legend text. To specify the legend, use the label attribute. To specify the box
dimensions, use the style attribute with height and width values.

The following code shows a simple form group with three text controls. The cfformgroup type="vertical" tag ensures
that the contents of the form is consistently aligned. The cfformgroup type="horizontal" aligns the firstname and
lastname fields horizontally.

<cfform name="comments" format="xml" skin="basiccss" width="400"
          preservedata="Yes" >
     <cfformgroup type="fieldset" label="Contact Information">
          <cfformgroup type="vertical">
                <cfformgroup type="horizontal" label="Name">
                     <cfinput type="text" size="20" name="firstname" required="yes">
                     <cfinput type="text" size="25" name="lastname" required="yes">
                </cfformgroup>
                <cfinput type="text" name="email" label="E-mail" validation="email">
          </cfformgroup>
          </cfformgroup>
</cfform>

Note: Because XML is case-sensitive, but ColdFusion is not, ColdFusion converts cfformgroup and cfformitem
attributes to all-lowercase letters. For example, if you specify cfformgroup type="Random", ColdFusion converts the
type to random in the XML.

Using other skins: If you use any other skin, the supported attributes and attribute values depend on the skin
implementation. Get the information about the supported attributes and attribute values from the skin developer.


Example: CFML for a skinnable XML form
The following CFML code creates the form shown in the image in "About XML skinnable forms" on page 783. It shows
how you can use CFML to structure your form.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                            788
Requesting and Presenting Information




<cfform name="comments" format="xml" skin="basiccss" width="400" preservedata="Yes" >
      <cfinput type="hidden" name="revision" value="12a">
      <cfformgroup type="fieldset" label="Basic Information">
           <cfformgroup type="vertical">
           <cfformgroup type="horizontal" label="Name">
                 <cfinput type="text" size="20" name="firstname" required="yes">
                 <cfinput type="text" size="25" name="lastname" required="yes">
           </cfformgroup>
           <cfinput type="text" name="email" label="E-mail" validate="email" maxlength="35">
           <cfselect name="satisfaction" style="width:120px" multiple="false"
label="Satisfaction">
                 <option selected>very satisfied</option>
                 <option>somewhat satisfied</option>
                 <option>somewhat dissatisfied</option>
                 <option>very dissatisfied</option>
                 <option>no opinion</option>
           </cfselect>
           </cfformgroup>
           </cfformgroup>
      <cfformitem name="html1" type="html">
      <p><b>We value your input</b>.<br>
      <em>Please tell us a little about yourself and your thoughts.</em></p>
      </cfformitem>
      <cftextarea name="thoughts" label="Additional Comments" rows="5" cols="66">We really want
to hear from you!</cftextarea>
      <cfformgroup type="horizontal">
           <cfinput type="submit" name="submit" style="width:80" value="Tell Us">
           <cfinput type="reset" name="reset" style="width:80" value="Clear Fields">
      </cfformgroup>
</cfform>



ColdFusion XML format
The XML generated from a ColdFusion cfform tag and its children are described here. It provides a building block
toward creating your own XSL skins.


XML namespace use
The XML that ColdFusion generates for forms uses elements and attributes in several XML namespaces. Namespaces
are named collections of names that help ensure that XML names are unique. They often correspond to a web standard,
a specific document type definition (DTD), or a schema. In XML, the namespace name and a colon (:) precede the name
of the tag that is defined in that namespace; for example xf:model for the XForms namespace model tag.

ColdFusion uses several standard XML namespaces defined by the World Wid Web Consortium (W3C). These
namespaces correspond to specifications for standard XML dialects such as XHTML, XForms, and XML Events.
ColdFusion XML forms also use a custom namespace for skinnable forms XML extensions. The following table lists
the namespaces in the XML that ColdFusion generates.


 Prefix          URL                                     Used for

 html            http://www.w3.org/1999/xhtml            Form tag information, including action, height, width, and name. XHTML
                                                         compliant.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                789
Requesting and Presenting Information




 Prefix         URL                                      Used for

 xf             http://www.w3.org/2002/xforms            XForms model (including initial field values) and XForms elements that
                                                         correspond to cfform tags.

 ev             http://www.w3.org/2001/xml-events        System events. Used for the cfinput type="reset".

 cf                                                      All ColdFusion extensions, including passthrough of attributes that do not
                                                         correspond to XForms elements or attributes.



XML structure
For each CFML tag, ColdFusion converts attributes and element values to XML in the XForms xf:model element, or
in individual control elements, such as the XForms xf:input, xf:secret, or xf:group elements.

ColdFusion generates XForms XML in the following format. The numbers on each line indicate the level of nesting of
the tags.

1     form tag
2         XForms model element
3              XForms instance element
4                    cf:data element
3              XForms submission element
3              XForms bind element
3              XForms bind element
3         .
3         .
3         .
2         (end of model element)
2         XForms or ColdFusion extension control element
2         XForms or ColdFusion extension control element
               .
               .
               .
1     (end of form)


Data model
The XForms data model specifies the data that the form submits. It includes information on each displayed control
that can submit data, including initial values and validation information. It does not contain information about
cfformgroup or cfformitem tags. The data model consists of the following elements and their children:

* One xf:instance element

* One xf:submission element

* One xf:bind element for each form control that can submit data


xf:instance element
The XForms xf:instance element contains information about the form data controls. Any control that can submit
data has a corresponding instance element. If the control has an initial value, the instance element contains that value.

The xf:instance element contains a single cf:data element that contains an element for each data control: cfgrid,
most cfinput tag types, cfselect, cfslider, cftextarea, and cftree. Each element name is the corresponding
CFML tag's name attribute. For applet and Flash format cfgrid and cftree tags, the element name is the value of the
cf_param_name parameter of the tree or grid's Java applet object. Only cfinput tags of types submit, image, reset,
andbutton do not have instance data, because they cannot submit data.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   790
Requesting and Presenting Information




The body of each element contains the initial control data from the CFML tag's value attribute or its equivalent. For
example, for a cfselect tag, the xf:instance element body is a comma-delimited list that contains the name
attributes of all the option tags with a selected attribute. For submit and image buttons, the body contains the name
attribute value.

The following example shows the xf:instance element for the form shown in the image in "About XML skinnable
forms" on page 783:

<xf:instance>
      <cf:data>
                 <firstname/>
                 <lastname/>
                 <email/>
                 <revision>Comment Form revision 12a</revision>
                 <satisfaction>very satisfied</satisfaction>
                 <thoughts>We really want to hear from you!</thoughts>
      </cf:data>
</xf:instance>


xf:submission element
The xf:submission element specifies the action when the form is submitted, and contains the values of the
cfformaction and method attributes.:

The following example shows the XML for the form shown in the image in "About XML skinnable forms" on page 783:

<xf:submission action="/_MyStuff/phase1/forms/XForms/FrameExamples/Figure1.cfm"
      method="post"/>


xf:bind elements
The xf:bind elements provide information about the input control behavior, including the control type and any data
validation rules. The XML has one bind element for each instance element that can submit data. It does not have bind
elements for controls such as cfformitem tags, or cfinput tags with submit, input, reset, or image types. Each element
has the following attributes:


 Attribute                Description

 id                       CFML tag name attribute value

 nodeset                  XPath expression with the path in the XML to the instance element for the control

 required                 CFML tag required attribute value


Each xf:bind element has an xf:extension element with ColdFusion specific information, including type and
validation values. The following table lists the cf namespace elements that are used here.


 Element                                                   Description

 cf:attribute name="type"                                  Control type. One of the following:

                                                           CHECKBOX, FILE, IMAGE, PASSWORD, RADIO, SELECT, SUBMIT TEXT, CFSLIDER.

                                                           The TEXT type is used for cfinputtype="text" and cftextinput.

 cf:attribute name="onerror"                               JavaScript function specified by the control's onError attribute, if any.

 cfargument name="maxlength"                               Value of the control's maxlength attribute, if any.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          791
Requesting and Presenting Information




Element                                         Description

cf:validate type="valiadationtype"              Data validation information.

                                                Has one attribute, type, the validation type, and one or more cf:argument
                                                and cf:trigger children. ColdFusion generates a cf:validate element for
                                                each of the following:

                                                *  cfinput or cftextareavalidation attribute

                                                *  cfinput or cftextarearange attribute

                                                *  cfslider: the range and message attributes are specified by a
                                                   cf:validate type="range" element


cf:argument                                     Data validation specification.

(in the body of a cf:validate element)          Has one attribute, name, and body text. Each cf:validate element can have
                                                multiple cf:argument children, corresponding to the validation-related
                                                CFML tag attribute values, such as maximum length, and maximum and
                                                minimum range values. The element body contains the CFML attribute value.

                                                Valid name values are as follows. Unless specified otherwise, the name is
                                                identical to the corresponding CFML tag attribute name.

                                                *  max

                                                *  message

                                                *  min

                                                *  pattern


cf:trigger                                      When to do the validation; specifies a form element validateAt attribute
                                                value.
(in the body of a cf:validate element)
                                                Has one attribute, event, which can be one of the following:

                                                *  onBlur

                                                *  onSubmit

                                                *  onServer

                                                If a validateAt attribute specifies multiple validation triggers, the XML has
                                                one cf:trigger element for each entry in the list.


The following example shows the xf:bind element of the form shown in the image in "About XML skinnable forms"
on page 783:





                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                           792
Requesting and Presenting Information




<xf:bind id="firstname"
           nodeset="//xf:model/xf:instance/cf:data/firstname"
           required="true()">
     <xf:extension>
           <cf:attribute name="type">TEXT</cf:attribute>
           <cf:attribute name="onerror">_CF_onError</cf:attribute>
     </xf:extension>
</xf:bind>
<xf:bind id="lastname"
           nodeset="//xf:model/xf:instance/cf:data/lastname"
           required="true()">
     <xf:extension>
           <cf:attribute name="type">TEXT</cf:attribute>
           <cf:attribute name="onerror">_CF_onError</cf:attribute>
     </xf:extension>
</xf:bind>
<xf:bind id="email"
           nodeset="//xf:model/xf:instance/cf:data/email" required="false()">
     <xf:extension>
           <cf:attribute name="type">TEXT</cf:attribute>
           <cf:attribute name="onerror">_CF_onError</cf:attribute>
     </xf:extension>
</xf:bind>
<xf:bind id="satisfaction"
           nodeset="//xf:model/xf:instance/cf:data/satisfaction"
           required="false()">
     <xf:extension>
           <cf:attribute name="type">SELECT</cf:attribute>
           <cf:attribute name="onerror">_CF_onError</cf:attribute>
     </xf:extension>
</xf:bind>
<xf:bind id="thoughts"
           nodeset="//xf:model/xf:instance/cf:data/thoughts" required="false()">
     <xf:extension>
           <cf:attribute name="type">TEXT</cf:attribute>
           <cf:attribute name="onerror">_CF_onError</cf:attribute>
     </xf:extension>
</xf:bind>


Control elements
The XML tags that follow the xf:bind element specify the form controls and their layout. The XML includes one
element for each form control and cfformitem or cfformgroup tag.


CFML to XML tag mapping
ColdFusion maps CFML tags to XForms elements and ColdFusion extensions as the following table shows:


CFML tag                                    XML tag

cfinput type="text"                         xf:input

cfinput type="password"                     xf:secret

cfinput type="hidden"                       None: instance data only

cfinput type="file"                         xf:upload




                                         

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                             793
Requesting and Presenting Information




CFML tag                                        XML tag

cfinput type="radio"                            xf:select1

cfinput type="checkbox"                         xf:select

cfinput type="button"                           xf:trigger

cfinput type="image"                            xf:submit

cfinput type="reset"                            xf:submit

cfinput type="submit"                           xf:submit

cfselect multiple="false"                       xf:select1

cfselect multiple="true"                        xf:select

cftextarea                                      xf:textarea

cfslider                                        xf:range

cfgrid                                          cf:grid

cftree                                          cf:tree

cfformitem type="text"                          xf:output

cfformitem type="html"                          xf:output

cfformitem type="*" (all but text, html)        xf:group appearance="*"

cfformgroup type="*"                            xf:group appearance="*"


ColdFusion converts cfformitem tags with text and html type attributes to XForms output elements with the tag
body in a <![CDATA[ section. It converts all other cfformitem tags to XForms group elements, and sets each
element's appearance attribute to the cfformitem tag's type attribute. The XSLT must process these elements to
produce meaningful output. For example, the ColdFusion default skin transform displays the xf:output text blocks
and processes the xf:groupappearance="hrule" element, but it ignores all other xf:group elements.


General control element structure
Each control element that a standard XForms control element can represent has the following general structure.

<xf:tagname bind="bindid" id="bindid">
     <xf:label>label</xf:label>
     <xf:extension>
           <cf:attribute name="type">controltype</cf:attribute>
           <cf:attribute name="attribname>attribvalue</cf:attribute>
           <cf:attribute name="attribname>attribvalue</cf:attribute>
           .
           .
           .
     </xf:extension>
</xf:tagname>

The following table describes the variable parts of this structure:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     794
Requesting and Presenting Information




 Part                    Description

 tagname                 The xf or cf namespace element name, as identified in the table in "CFML to XML tag mapping" on page 792.

 bindid                  ID attribute of the model xf:bind element for this control. Specified by the control's CFML tag name attribute.

 label                   Control label text. Specified by one of the following:

                         * The CFML tag label attribute

                         * The value attribute of the radiobutton, radio, submit, and resetcfinput tags

                         * The tag body content of cfselectoption subtags,

                         * Not used for cfgrid and cftree tags.

 controltype             Type of control. One of the following:

                         * The cfinputtype attribute

                         *  Select, slider, or textarea, for the cfselect, cfslider, or cftextarea tags, respectively.

                         * Not used for cfgrid and cftree tags.

 attribname              Name of a CFML tag attribute. There is a cf:attribute tag for each attribute specified in the CFML code that
                         does not otherwise have an entry in the XML.

 attribvalue             Value of a CFML tag attribute.



Tag-specific element structure
The information described here about the tag-specific features of the XML for several types of input tags is not all-
inclusive. For the specific structure of any ColdFusion form tag, see the XML generated from the tag by ColdFusion.


Selection tags
Tags that are used for selection, cfselect, cfinputtype="radio", and cfinputtype="checkbox" are converted to
XForms select and select1 elements. These elements include an xf:choices element, which in turn has an
xf:item element for each item a user can choose. Each item normally has an xf:label element and an xf:value
element. Check boxes have a single item; select and radio button controls have more than one.

The following example shows the CFML code for a group of two radio buttons, followed by the generated XML control
elements. This example also shows the use of a cfformgroup tag to arrange and label the radio button group.


CFML
<cfformgroup type="horizontal" label="Accept?">
     <cfinput type = "Radio" name = "YesNo" value = "Yes" checked>
     <cfinput type = "Radio" name = "YesNo" value = "No">
</cfformgroup>




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    795
Requesting and Presenting Information




XML
<xf:group appearance="horizontal">
    <xf:label>Accept?</xf:label>
    <xf:extension/>
    <xf:select1 appearance="full" bind="YesNo" id="YesNo">
         <xf:extension>
               <cf:attribute name="type">radio</cf:attribute>
         </xf:extension>
         <xf:choices>
               <xf:item>
                    <xf:label>Yes</xf:label>
                    <xf:value>Yes</xf:value>
                    <xf:extension>
                           <cf:attribute name="checked">checked</cf:attribute>
                    </xf:extension>
               </xf:item>
               <xf:item>
                    <xf:label>No</xf:label>
                    <xf:value>No</xf:value>
                    <xf:extension/>
               </xf:item>
         </xf:choices>
    </xf:select1>
</xf:group>



cfgrid tags
ColdFusion represents a cfgrid tag using the cf:grid XML tag. This tag has four attributes: format, which can be
Flash, Applet, or XML; and the id, name, and bind attributes, which all have the value of the cfgrid tag name attribute.

For applet and Flash format grids, ColdFusion inserts cfgrid controls in the XML as HTML embed objects in
<![CDATA[ sections in the body of a cf:grid tag. The controls can be Java applets or in SWF file format.

For XML format grids, ColdFusion converts the CFML to XML in the following format:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                               796
Requesting and Presenting Information




<cf:grid bind="gridname" name="gridname" format="xml" id="gridname>
    <metadata>
         <cfgridAttribute1>attributeValue</cfgridAttribute1>
         ...
         (There are an entry for attributes with a specified or default value.)
    </metadata>
    <columns>
         <column cfgridcolumnAttribute1="value" ... />
         ...
    </columns>
    <rows>
         <row>
               <column1Name>row1Column1Value</column1Name>
               <column2Name>row1Column2Value</column2Name>
               ...
         </row>
         <row>
               <column1Name>row2Column1Value</column1Name>
               <column2Name>row2Column2Value</column2Name>
         </row>
         ...
    </rows>
</cf:grid>

The following example shows a minimal grid with two nodes.


CFML
<cfgrid name="mygrid" Format="xml" selectmode="Edit" width="350">
    <cfgridcolumn name="CorName" header="Course Name" >
    <cfgridcolumn name="Course_ID" header="ID">
    <cfgridrow data="one0,two0">
    <cfgridrow data="one1,two1">
</cfgrid>


XML
Most metadata lines are omitted for brevity:




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                              797
Requesting and Presenting Information




<cf:grid bind="mygrid" format="XML" id="mygrid" name="mygrid">
     <metadata>
          <autowidth>false</autowidth>
          <insert>false</insert>
          <delete>false</delete>
          <sort>false</sort>
          <italic>false</italic>
          <bold>false</bold>
          <appendkey>true</appendkey>
          <highlughthref>true</highlughthref>
          <griddatalines>Left</griddatalines>
          <gridlines>true</gridlines>
          <rowheaders>true</rowheaders>
          <rowheaderalign>Left</rowheaderalign>
          <rowheaderitalic>false</rowheaderitalic>
          <rowheaderbold>false</rowheaderbold>
          <colheaders>true</colheaders>
          <colheaderalign>Left</colheaderalign>
          <colheaderitalic>false</colheaderitalic>
          <colheaderbold>false</colheaderbold>
          <selectmode>Edit</selectmode>
          <notsupported>&lt;b&gt; Browser must support Java to view ColdFusion Java
               Applets&lt;/b&gt;</notsupported>
          <picturebar>false</picturebar>
          <insertbutton>insert</insertbutton>
          <deletebutton>delete</deletebutton>
          <sortAscendingButton>SortAsc</sortAscendingButton>
          <sortDescendingButton>SortDesc</sortDescendingButton>
     </metadata>
     <columns>
          <column bold="false" display="true" header="Course Name"
               headerBold="false" headerItalic="false" italic="false"
               name="CorName" select="true"/>
          <column bold="false" display="true" header="ID"
               headerBold="false" headerItalic="false" italic="false"
               name="Course_ID" select="true"/>
     </columns>
     <rows>
          <row>
               <CorName>one0</CorName>
               <Course_ID>two0</Course_ID>
          </row>
          <row>
               <CorName>one1</CorName>
               <Course_ID>two1</Course_ID>
          </row>
     </rows>
</cf:grid>



The cftree tags
For applet and Flash format trees, ColdFusion inserts cftree controls in the XML as HTML embed objects in
<![CDATA[ sections in the tag body. The controls can be Java applets or in Flash SWF file format. The cf:tree XML
tag has two attributes: format, which can be Flash or Applet, and id.

For XML format trees, ColdFusion converts the CFML to XML in the following format:



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                             798
Requesting and Presenting Information




cf:tree format="XML" id="treename>
    <metadata>
         <cftreeAttribute1>attributeValue</cftreeAttribute1>
         ...
    </metadata>
    <node cfml tree item attributes>
         <node //nested node with no children
               cfml tree item attributes />
         ...
    </node>
    ...
</cf:tree>

The following example shows a minimal tree with two nodes:


CFML
<cfform name="form2" Format="XML" >
<cftree name="tree1" hscroll="No" vscroll="No" format="xml"
    border="No">
    <cftreeitem value="Divisions">
    <cftreeitem value="Development"
         parent="Divisions" img="folder">
</cftree>
</cfform>


XML
The following code shows only the XML that is related to the tree appearance:

<cf:tree format="xml" id="tree1">
    <metadata>
         <fontWeight/>
         <align/>
         <lookAndFeel>windows</lookAndFeel>
         <delimiter>\</delimiter>
         <completePath>false</completePath>
         <border>false</border>
         <hScroll>false</hScroll>
         <vScroll>false</vScroll>
         <appendKey>true</appendKey>
         <highlightHref>true</highlightHref>
         <italic>false</italic>
         <bold>false</bold>
    </metadata>
    <node display="Divisions" expand="true" href="" img=""
         imgOpen="" parent="" path="Divisions" queryAsRoot="true"
                           value="Divisions">
         <node display="Development" expand="true" href=""
               img="folder" imgOpen="" parent="Divisions"
               path="Divisions\Development" queryAsRoot="true"
                               value="Development"/>
    </node>
</cf:tree>




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                            799
Requesting and Presenting Information




The cfformgroup and cfformitem tags
All cfformgroup tags and all cfformitem tags, except type="html" and type="text", generate xf:group
elements. The following rules determine the element structure:

* The CFML tag type attribute determines the xf:groupappearance attribute.

* ColdFusion converts type attribute values to all-lowercase characters.

* For cfformgroup tags only, the CFML label attribute determines the xf:grouplabel attribute.

* All other CFML attributes are placed in cf:attribute elements in a xf:extension element.

* The cfformitem tags generate an xf:output element with the body text in a <![CDATA[ section.

The following example shows two cformitem tags, and the resulting XML:


CFML
<cfformitem name="text1" type="text" style="color:green">
     Please tell us a little about yourself and your thoughts.
</cfformitem>
<cfformitem type="hrule" height="3" width="200" testattribute="testvalue" />


XML
<xf:output><![CDATA[Please tell us a little about yourself and your
thoughts.]]>
     <xf:extension>
         <cf:attribute name="style">color:green</cf:attribute>
     </xf:extension>
</xf:output>
<xf:group appearance="hrule">
     <xf:extension>
         <cf:attribute name="width">200</cf:attribute>
         <cf:attribute name="height">3</cf:attribute>
         <cf:attribute name="testattribute">testvalue</cf:attribute>
     </xf:extension>
</xf:group>



Example: control element XML
The following code shows the XML for the input controls for the form shown in the image in "About XML skinnable
forms" on page 783. This code immediately follows the end of the xf:model element.




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                           800
Requesting and Presenting Information




<xf:group appearance="horizontal">
    <xf:label>name</xf:label>
    <xf:extension/>
    <xf:input bind="firstname" id="firstname">
         <xf:label>First</xf:label>
         <xf:extension>
               <cf:attribute name="type">text</cf:attribute>
               <cf:attribute name="size">20</cf:attribute>
         </xf:extension>
    </xf:input>
    <xf:input bind="lastname" id="lastname">
         <xf:label>Last</xf:label>
         <xf:extension>
               <cf:attribute name="type">text</cf:attribute>
               <cf:attribute name="size">25</cf:attribute>
         </xf:extension>
    </xf:input>
</xf:group>
<xf:input bind="email" id="email">
    <xf:label>Email</xf:label>
    <xf:extension>
         <cf:attribute name="type">text</cf:attribute>
         <cf:attribute name="validation">email</cf:attribute>
    </xf:extension>
</xf:input>
<xf:output><![CDATA[<b>We value your input</b>.<br>
    <em>Please tell us a little about yourself and your thoughts.</em>]]>
    <xf:extension/>
</xf:output>
<xf:group appearance="vertical">
    <xf:extension/>
    <xf:select1 appearance="minimal" bind="satisfaction" id="satisfaction">
         <xf:label>Satisfaction</xf:label>
         <xf:extension>
               <cf:attribute name="type">select</cf:attribute>
               <cf:attribute name="style">width:200</cf:attribute>
         </xf:extension>
         <xf:choices>
               <xf:item>
                    <xf:label>very satisfied</xf:label>
                    <xf:value>very satisfied</xf:value>
               </xf:item>
               <xf:item>
                    <xf:label>somewhat satisfied</xf:label>
                    <xf:value>somewhat satisfied</xf:value>
               </xf:item>
               <xf:item>
                    <xf:label>somewhat dissatisfied</xf:label>
                    <xf:value>somewhat dissatisfied</xf:value>
               </xf:item>
               <xf:item>
                    <xf:label>very dissatisfied</xf:label>
                    <xf:value>very dissatisfied</xf:value>
               </xf:item>
               <xf:item>
                    <xf:label>no opinion</xf:label>
                    <xf:value>no opinion</xf:value>



                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 801
Requesting and Presenting Information




               </xf:item>
           </xf:choices>
     </xf:select1>
     <xf:textarea bind="thoughts" id="thoughts">
           <xf:label>Additional Comments</xf:label>
           <xf:extension>
               <cf:attribute name="type">textarea</cf:attribute>
               <cf:attribute name="rows">5</cf:attribute>
               <cf:attribute name="cols">40</cf:attribute>
           </xf:extension>
     </xf:textarea>
</xf:group>
<xf:group appearance="horizontal">
     <xf:extension/>
     <xf:submit id="submit" submission="comments">
           <xf:label>Tell Us</xf:label>
           <xf:extension>
               <cf:attribute name="type">submit</cf:attribute>
               <cf:attribute name="name">submit</cf:attribute>
           </xf:extension>
     </xf:submit>
     <xf:submit id="reset">
           <xf:label>Clear Fields</xf:label>
           <reset ev:event="DOMActivate"/>
           <xf:extension>
               <cf:attribute name="name">reset</cf:attribute>
           </xf:extension>
     </xf:submit>
</xf:group>



Creating XSLT skins
You can create your own XSLT skins to process the XML that ColdFusion generates. You must be familiar with XSLT
and CSS programming. General information on writing XSLT transformations or CSS styles is not provided here.
However, information about the following areas is provided:

* How ColdFusion passes form attribute values to the XML file

* How to extend XSLT skins that ColdFusion provides as templates

* Basic techniques for extending the basic.xsl file to support additional cfformgroup and cfformitem tag type
   attributes

* How to extend the ColdFusion CSS files to enhance form appearance.


XSLT skin file locations
If you specify an XSLT skin by name and omit the .xsl extension, ColdFusion looks for the file in the cfform script
source directory and its subdirectories. You can specify the script source directory in your cfform tag scriptsrc
attribute, and you can set a default location on the Settings page in the ColdFusion Administrator. When you install
ColdFusion, the default location is set to /CFIDE/scripts/ (relative to the web root).

You can also use a relative or absolute file path, or a URL, to specify the XSLT skin location. ColdFusion uses the
directory of the CFML page as the root of relative paths. The following formats are valid:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                            802
Requesting and Presenting Information




 Format                                                                               Location

 <cfform format="xml" skin="basic">                                                   Searches for XML/CSS in the default directory and its
                                                                                      subdirectories.

 <cfform format="xml" skin="c:\foo\bar\basic.xsl">                                    Uses the absolute path.

 <cfform format="xml" skin="basic.xsl">                                               Searches in the current directory.

 <cfform format="xml" skin="..\basic.xsl:">                                           Searches the parent of the current directory.

 <cfform format="xml" skin="http://anywhereOnTheWeb/basic.xsl">                       Uses the specified URL.


Note: Hosting companies sometimes move the default skin location folder out of CFIDE. Doing this lets them secure the
CFIDE while giving site developers access to the files that you need for cfform.


Attribute and value passthrough
ColdFusion passes form tag attributes or attribute values that it does not specifically process directly to the XML, as
follows:

* It converts cfformitem and cfformgrouptype attributes to xf:group element appearance attributes.

* It passes the name and value of tag attributes that it does not recognize or process in cf:attribute elements.

This passthrough feature lets you create custom versions of any of the following items for your XSLT to process:

* The cfformitem types, such as rules, spacers, or other display elements

* The cfgroup types, such as divided boxes or tabbed dialog boxes

* The custom cfinput types, such as a custom year chooser element

* ColdFusion tag attributes, such as those used to control validation


Extending ColdFusion XSLT skins
ColdFusion provides basic XSLT transforms that you can use as templates and extend for making your own skin. Each
skin has a base XSL file, which include several utility XSL files. Utility filenames start with an underscore (_), and
multiple base skins share the files. The following table describes the XSL files, which are located in the cf_webroot\\x


 File                              Description

 default.xsl                       The default transform that ColdFusion uses if you do not specify a skin attribute for an XML format form.
                                   Identical to the basic.xsl file.

 basic.xsl                         A basic form format that arranges form elements using a table.

 basiccss.xsl                      A basic form format that arranges form elements using HTML div and span tags.

 colorname.xsl                     A basic form format that arranges form elements using a table and applies a color scheme determined by
                                   the colorname to the form. Based on the basic.xsl file.

 _cfformvalidation.xsl             Applies ColdFusion validation rules. Used by all skins.

 _formelements.xsl                 Transformation rules for form elements except for those defined using cfformgroup tags. Used by all
                                   skins

 _group_type.xsl                   Transformation rules for cfformgroup tags. The tag type attribute is part of the filename. Files with table
                                   in the name are used by basic.xsl and its derivatives. Files with css in the name are used by basiccss.xsl.
 _group_type_table.xsl

 _group_type_css.xsl




                                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     803
Requesting and Presenting Information




All skins support the same set of CFML tags and tag types, and do a relatively simple transformation from XML to
HTML. For example, they do not support horizontal or vertical rules.

The ColdFusion skin XSL files have several features that you can use when designing and developing your own
transformation. They do the following:

* Provide an overall structure and initial templates for implementing custom transformations.

* Show how you can handle the various elements in the ColdFusion-generated XML.

* Use a structure of included files that can form a template for your XSLT code.

* The base XSL files include a separate file, _cfformvalidation.xsl, with complete code for generating the hidden fields
   required for ColdFusion onServer validation and the JavaScript for performing ColdFusion onSubmit and onBlur
   validation. You can include this file without modification to do ColdFusion validation in your XSLT template, or
   you can change it to add other forms of validation or to change the validation rules.

* The base XSL files include files, that implement several form groups, laying out the child tags and applying a label
   to the group. These files can serve as templates for implementing additional form group types or you can expand
   them to provide more sophisticated horizontal and vertical form groups.

* You can add custom cfformgroup and cfformitemtype attributes by including additional XSL files.


Extending basic.xsl cfformgroup and cfformitem support
The following procedure describes the steps for extending the basic.xsl file to support additional cfformgroup and
cfformitem types. You can use similar procedures to extend other xsl files.


Add support for cfformgroup and cfformitem types to the basic.xsl
1 Create an XSL file.

2 For each type attribute that you want to support, create an xsl:template element to do the formatting. The match
   attribute of the element must have the following format:

    match="xf:group[@appearance='type_attribute_name']"

   For example, to add a panel cfformgroup type, add an element with a start tag such as the following:

    <xsl:template match="xf:group[@appearance='panel']">

3 Deploy your XSL file or files to the cf_webroot\\x

4 Add an include statement to the basic.xsl file at the end of the Supported groups section; for example, if you create
   a my_group_panel.xsl file to handle a panel cfformgroup type, your basic.xsl file would include the following lines:

    <!-- include groups that will be supported for this skin-->
   <xsl:include href="_group_vertical_table.xsl" />
   <xsl:include href="_group_horizontal_table.xsl" />
   <xsl:include href="_group_fieldset.xsl"/>
   <xsl:include href="my_group_panel.xsl" />


Styling forms by extending the ColdFusion CSS files
Each ColdFusion skinnable form XSL file uses a corresponding CSS style sheet to specify the form style and layout
characteristics. The following CSS files are located in the cf_webroot\CFIDE\scripts\css directory:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                           804
Requesting and Presenting Information




 File                           Description

 basic_style.css                Provides a plain style for ColdFusion XSL files that use table-based formatting. These files are identical and
                                are used by the basic.xsl and default.xsl transforms. ColdFusion uses the default_style.css if you do not
 default_style.css
                                specify a skin in your cfform tag.

 basic2_style.css               The basic_style with limited positioning changes for use with XSL files that have div-based formatting.
                                Used by the basiccss.xsl transform.

 css_layout.css                 Style specifications for laying out forms that use div-based formatting. Used by the basiccss.xsl transform.

 colorname_style.css            Used by the color-formatted ColdFusion skins. Defines the same classes as basic_style.css, with additional
                                property specifications.


The ColdFusion XSL files and their corresponding CSS style sheets use classes extensively to format the form. The
basic.xsl file, for example, has only one element style; all other styles are class-based. Although the CSS files contain
specifications for all classes used in the XSL files, they do not always contain formatting information. The horizontal
class definition in basic_style.css, which is used for horizontal form groups, for example, is empty.

You can enhance the style of XML skinnable forms without changing the XSL transform by enhancing the style sheets
that ColdFusion provides.



Using Ajax User Interface Components and Features

Use Adobe ColdFusion Ajax-based layout and form controls and other Ajax-based user interface capabilities to create
a dynamic application.

For information about how ColdFusion uses the Ajax framework in general, or how to use ColdFusion Ajax data and
programming capabilities, including binding to form data and managing JavaScript resources, see "Using Ajax Data
and Development Features" on page 858.


About Ajax and ColdFusion user interface features
Ajax (Asynchronous JavaScript and XML) is a set of web technologies for creating interactive web applications. Ajax
applications typically combine:

* HTML and CSS for formatting and displaying information.

* JavaScript for client-side dynamic scripting

* Asynchronous communication with a server using the XMLHttpRequest function.

* XML or JSON (JavaScript Object Notation) as a technique for serializing and transferring data between the sever
    and the client.

ColdFusion provides many tools that simplify using Ajax technologies for dynamic applications. By using ColdFusion
tags and functions, you can easily create complex Ajax applications.


ColdFusion Ajax features
ColdFusion provides two types of Ajax features:

* Data and development features

* User interface features




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   805
Requesting and Presenting Information




Data and development features
ColdFusion data and development features help you develop effective Ajax applications that use ColdFusion to
provide dynamic data. They include many features that you can use with other Ajax frameworks, including Spry.

The following data and development features are important for use with form and layout tags:

* ColdFusion supports data binding in many tags. Binding allows form and display tags to dynamically display
   information based on form input. In the simplest application, you display form data directly in other form fields.
   But usually, you pass form field data as parameters to CFC or JavaScript functions or CFM pages, and use the results
   to control the display.

* The cfajaximport tag specifies the location of the JavaScript and CSS files that a ColdFusion page imports or to
   selectively import files required by specific tags. The ability to change the file location lets you support a wide range
   of configurations and use advanced techniques, such as application-specific styles.

For more information about the data and development features and how to use them, see "Using Ajax Data and
Development Features" on page 858.


User Interface tags and features
Several ColdFusion user interface elements incorporate Ajax features. The tags and tag-attribute combinations can be
divided into the following categories:

* Container tags that lay out or display contents

* File management tags that handle files

* Forms tags that dynamically display data

* A menu tag that lets you create menu bars and pull-down menus

* User assistance features that provide tool tips and form completion

* Four other tags for using geographical maps, progress bar, media player, and message box.

The following table lists the basic tags and attributes that display the Ajax-based features. For information on
additional forms-specific features, see "Using Ajax form controls and features" on page 820.


Tag/attribute                                   Description

Container tags

cfdiv                                           An HTML div region that can be dynamically populated by a bind expression. Forms
                                                in this region submit asynchronously.

cflayout                                        A horizontal or vertical box, a tabbed region, or a set of bordered regions that can
                                                include a top, bottom, left, right, and center regions.

cflayoutarea                                    An individual region within a cflayout area, such as the display that appears in a
                                                tabbed layout when the user select a tab. Forms in this region submit asynchronously.

cfpod                                           An area of the browser window with an optional title bar and a body that contains
                                                display elements. Forms in this region submit asynchronously.

cfwindow                                        A pop-up window within the browser. You can also use the
                                                ColdFusion.Window.createWindow function to create a pop-up window. Forms
                                                in this region submit asynchronously.

File management tags

cffileupload                                    A dialog for uploading multiple files from the user's system.

Forms tags




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  806
Requesting and Presenting Information




 Tag/attribute                                 Description

 cfgrid format="html"                          A dynamic, editable, sortable, data grid.

 cfinput type="datefield"                      An input control that users can fill by selecting a date from a pop-up calendar.

 cftextarea richtext="yes"                     A text area with a set of controls that let users format the displayed text.

 cftree format="html"                          A dynamic, editable, tree-format representation of data.

 cfslider                                      A slider control, for selecting a numeric value from a range, in a ColdFusion form.

 Menu tags

 cfmenu                                        A menu bar or the root of a drop-down menu.

 cfmenuitem                                    An individual item in a menu, or the root of a submenu.

 User assistance tags and attributes

 cfinput type="text"                           A drop-down autofill suggestion box. As the user types, a list appears with completion
 autosuggest="bindexpression"                  suggestions based on the text the user has typed.

 cftooltip tag, and the tooltip attribute on   A textual description of a control or region that appears when the user hovers the
 cfinput, cfselect, cftextarea controls        mouse over the control or region.

 Other tags

 cfprogressbar                                 A progress bar to indicate the progress of an activity such as a file download.

 cfmap                                         A geographical map within a ColdFusion web page.

 cfmediaplayer                                 An inbuild media player.

 cfmessagebox                                  A control for displaying pop-up messages.


In addition to the tags and attributes, ColdFusion provides many JavaScript functions that let you control and manage
the display. Many functions control the display of specific tags. For example, you can use JavaScript functions to
dynamically display and hide the window. There are also several utility tags, such as the
ColdFusion.getElementValue function that gets the value of a control attribute, or the ColdFusion.navigate
function that displays the results of a URL in a container tag. For a complete list of all ColdFusion Ajax JavaScript
functions, and detailed function descriptions, see Ajax JavaScript Functions in the CFML Reference.


Using ColdFusion Ajax user interface features
ColdFusion Ajax user interface features let you create data-driven pages that update dynamically without requiring
multiple HTML pages or page refreshes or non-HTML display tools such as Flash forms. Many user interface features
use data binding to dynamically get data based on other data values: form field values, form control selections, and
selections in Spry data sets.

ColdFusion Ajax user interface controls and features can be divided into two major categories:

* Display layout

* Data interaction

Display layout controls include the cflayout, cfpod, and cfwindow controls. Some of the data interaction features
include the HTML cfgrid control, the cfmenu control, and dynamic autosuggest lists for text input controls. Most
display layout and data interaction features can use data binding to dynamically interact with the user.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           807
Requesting and Presenting Information




ColdFusion Ajax user interface features are based on the Yahoo User Interface Library and the Ext JavaScript Library.
Also, the cftextarea rich text editor is based on the FCKeditor text editor. In most situations, you require only
ColdFusion tags and functions (including JavaScript functions) to create and manage the interface. However,
advanced developers can modify the library code, particularly the CSS styles, to customize the controls in more
complex ways.


Controlling Ajax user interface layout
The following layout tags let you dynamically control the display:

* cfdiv

* cflayout

* cfpod

* cfwindow

For information about how you can use these tags to submit form contents asynchronously, see "Using Ajax containers
for form submission" on page 820.


Using the cfdiv tag
The cfdiv tag is a general-purpose container that lets you use a bind expression to specify its contents. It therefore lets
you dynamically refresh any arbitrary region on the page based on bind events. By default, the tag creates an HTML
div region, but it can create any HTML tag with body contents. Unlike other ColdFusion Ajax container tags, you can
use any type of bind expression to populate contents: CFC or JavaScript function, URL, or a string with bind
parameters. As a result, the cfdiv tag provides substantial flexibility in dynamically populating the page contents.

The cfdiv tag is also useful if you want a form to submit asynchronously. That is, whether or not you use a bind
expression to populate the tag. If you submit a form that is inside a cfdiv tag (including in HTML returned by a bind
expression), the form submits asynchronously. The response from the form submission populates the cfdiv region.
(The cflayoutarea, cfwindow, and cfpod tags have the same behavior.) For example, you could have a page with a
form that includes a list of artists, and lets you add artists. If the form is in a cfdiv tag, when the user submits the form,
the entire page is not refreshed, only the region inside the cfdiv tag. For an example of using container controls for
asynchronous forms, see "Using Ajax containers for form submission" on page 820.

One use case for a cfdiv tag is an application where a cfgrid tag displays an employee list. Details of the selected row
in the grid are displayed inside a cfdiv tag with a bind expression that specifies the cfgrid in a bind parameter. As
users click through different employees on the grid, they get the employee details in the cfdiv region.

The following simple example shows how you can use the cfdiv tag to get data using a bind expression. It uses binding
to display the contents of a text input field in an HTML div region. When a user enters text in the input box and tabs
out of it, or clicks another region of the application, the div region displays the entered text.

The cfdiv tag.cfm file, the main application file, has the following contents.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         808
Requesting and Presenting Information




<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<title>cfdiv Example</title>
</head>


<body>
<cfform>
      <cfinput name="tinput1" type="text">
</cfform>


<h3> using a div</h3>
<cfdiv bind="url:divsource.cfm?InputText={tinput1}" ID="theDiv"
          style="background-color:##CCffFF; color:red; height:350"/>
</body>
</html>

The divsource.cfm file that defines the contents of the div region has the following code:

<h3>Echoing main page input:</h3>
<cfoutput>
      <cfif isdefined("url.InputText")>
          #url.InputText#
      <cfelse>
          No input
      </cfif>
</cfoutput>


Using layouts
The cflayout tag controls the appearance and arrangement of one or more child cflayoutarea regions. The
cflayoutarea regions contain display elements and can be arranged in one of the following ways:

* Horizontally or vertically.

* In a free-form bordered grid (panel layout) with up to five regions: top, bottom, left. right, and center. You can
   optionally configure the layout so that users can resize or collapse any or all of the regions, except the center region.
   The center region grows or shrinks to take up any space that other regions do not use. You can also dynamically
   show or hide individual regions, or let users collapse, expand, or close regions.

* As a tabbed display, where selecting a tab changes the display region to show the contents of the tab's layout area.
   You can dynamically show and hide, and enable and disable tabs, and optionally let users close tabs.

You can configure a layout area to have scroll bars all the time, only when the area content exceeds the available screen
size, or never. You can let layout area contents extend beyond the layout area. You can also nest layouts inside layout
areas to create complex displays.

You can define the layout area content in the cflayoutarea tag body. But, you can also use a bind expression to
dynamically get the content by calling a CFC function, requesting a CFML page, or calling a JavaScript function.

ColdFusion provides many JavaScript functions for managing layouts, including functions to collapse, expand, show,
and hide border areas; and to create, enable, disable, select, show, and hide tabs. For a complete list of functions, see
Ajax JavaScript Functions in the CFML Reference.

The following example shows the use of a tabbed layout, including the use of JavaScript functions to enable and disable
a tab, and to show and hide a tab.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    809
Requesting and Presenting Information




 <html xmlns="http://www.w3.org/1999/xhtml">
<head>
</head>


<body>
<!--- The tabheight attribute sets the height of all tab content areas and therefore the
                layout height. The width style controls the layout width. --->
<cflayout type="tab" name="mainTab" tabheight="300px" style="width:400px">


     <!--- Each layoutarea is one tab. --->
     <cflayoutarea title="First Tab" name="tab1">
     <h2>The First Tab</h2>
     <p>
     Here are the contents of the first tab.
     </p>
     </cflayoutarea>


     <cflayoutarea title="Second Tab" name="tab2">
     <h2>The Second Tab</h2>
     <p>
     This is the content of the second tab.
     </p>
     </cflayoutarea>
</cflayout>


<p>
Use these links to test selecting tabs via JavaScript:<br />
<a href="" onClick="ColdFusion.Layout.selectTab('mainTab','tab1');return false;">
     Click here to select tab 1.</a><br />
<a href="" onClick="ColdFusion.Layout.selectTab('mainTab','tab2');return false;">
     Click here to select tab 2.</a><br />
</p>


<p>
Use these links to test disabling/enabling via JavaScript. Notice that you cannot disable
     the currently selected tab.<br />
<a href="" onClick="ColdFusion.Layout.enableTab('mainTab','tab1');return false;">
     Click here to enable tab 1.</a><br />
<a href="" onClick="ColdFusion.Layout.disableTab('mainTab','tab1');return false;">
     Click here to disable tab 1.</a><br />
</p>


</body>
</html>

For an example that uses a bordered layout with cfpod children, see the next section. For another example of a tab
layout, see the cflayoutarea tag in the CFML Reference. For an example of a bordered layout nested inside a layout area
of a vertical layout, see cflayout in the CFML Reference.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         810
Requesting and Presenting Information




Styling layouts
The cflayout and cflayoutarea tags have style attributes. The cflayout tag style attribute controls the style of
the layout container, and sets default values for many, but not all, styles for the layout areas. For example, the color and
background color styles of the cflayout tag set the default text and background colors in the layout areas. But the
cflayout tag border style sets only the color of the border around the entire layout, not the layout area borders. The
cflayoutarea tag style attribute controls the style of the individual layout area and overrides any corresponding
settings in the cflayout tag.

As is often the case with complex controls, the effects of layout and layout area styles can vary. For example, do not
often specify the height style in the cflayout tag; instead, specify height styles on each of the cflayoutarea tags.

The following simple example shows a tab layout with two layout areas. The layout has a light pink background color,
and the layout areas have three pixel-wide red borders.:

 <cflayout name="layout1" type="tab" style="background-color:##FFCCCC">
     <cflayoutarea title="area1" style="border:3px solid red">
          Layout area 1
     </cflayoutarea>
     <cflayoutarea title="area1" style="border:3px solid red">
          Layout area 2
     </cflayoutarea>
</cflayout>


Using pods
The cfpod control creates a content region with a title bar and surrounding border. You can define the pod content in
the cfpod tag body, or you can use a bind expression to dynamically get the content from a URL. Pods are frequently
used for portlets in a web portal interface and for similar displays that are divided into independent, possibly
interactive, regions.

You control the pod header style and body style independently by specifying CSS style properties in the headerStyle
and bodyStyle attributes.

The following example uses multiple pods inside cflayoutarea tags to create a simple portal. The time pod gets the
current time from a CFML page. The contents of the other pods is defined in the cfpod bodies for simplicity. Each pod
uses the headerStyle and bodyStyle attributes to control the appearance.

The cfpodExample.cfm application has the following code:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                        811
Requesting and Presenting Information




<html>
<head>
</head>
<body>
<cflayout name="theLayout" type="border" style="height:300;">
     <cflayoutarea position="left" size="300" style="float:right;">
          <cfpod width="300" name="theNews" title="All the latest news"
                    headerstyle="background-color:##DDAADD; font-size:large;
                           font-style:italic; color:black"
                    bodyStyle="background-color:##FFCCFF; font-family:sans-serif;
                           font-size:80%">
               Contents of a news feed would go here.
          </cfpod>
     </cflayoutarea>
     <cflayoutarea position="center" align="center" >
          <cfpod name="theSports" width="500"
                    title="What's new in your favorite sports"
                    headerstyle="background-color:##AADDDD; font-size:large;
                           font-style:italic; color:black"
                    bodyStyle="background-color:##CCFFFF; font-family:sans-serif;
                           font-size:90%">
               Contents of a sports feed would go here.
          </cfpod>
     </cflayoutarea>
     <cflayoutarea position="right" size="302">
          <cfpod width="300" height="20" name="thetime" title="The Weather"
                    source="podweather.cfm"
                    headerstyle="background-color:##DDAADD; font-style:italic;
                           color:black"
                    bodyStyle="background-color:##FFCCFF; font-family:sans-serif;
                           font-size:80%" />
          <cfpod width="300" name="thestocks" title="What's new in business"
                    headerstyle="background-color:##DDAADD; font-size:large;
                           color:black; font-style:italic"
                    bodyStyle="background-color:##FFCCFF; font-family:sans-serif;
                           font-size:80%">
               Contents of a news feed would go here.
          </cfpod>
     </cflayoutarea>


</cflayout>
</body>
</html>

In this example, the podweather.cfm page contains only the following line. A more complete example would
dynamically get the weather from a feed and format it for display.

Partly Cloudy, 76 degrees


Using pop-up windows
ColdFusion HTML pop-up windows have the following characteristics:

* They have title bars

* They float over the browser window and can be placed at an arbitrary location over the window.




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               812
Requesting and Presenting Information




* They can be modal (users cannot interact with the main window when the pop-up window is displayed) or non-
  modal (users can interact with both windows).

* You can specify that the user can drag, close, or resize the window.

* You can create and show a window independently. After you create the window, you can use JavaScript functions
  to show and hide it multiple times without having to create it again.


Display and hide windows
You display a window in the following ways:

* By using a ColdFusion cfwindow tag with an initShow attribute value of to create and show the window.

* By using a ColdFusion cfwindow tag with an initShow attribute value of false and calling the
  ColdFusion.Window.show JavaScript function to display it.

* By using ColdFusion.Window.create and ColdFusion.Window.show JavaScript functions.

You can hide a window that is currently showing by calling the ColdFusion.Window.hide function. You can use the
ColdFusion.Window.onShow and ColdFusion.Window.onhide functions to specify JavaScript functions to run
when a window shows or hides.

The following example shows how you can create, display, and hide a window. It also shows several of the
configuration options that you can set, including whether the user can close, drag, or resize the window. When you
run the application, the cfwindow tag creates and shows Window 1. You can then hide it and reshow it. To show
Window 2, click the Create Window 2 button, followed by the Show Window 2 button. You can then hide and show it.

The following example shows the main application page:

<html>
<head>
<script>
     <!--
     //Configuration parameters for window 2.
     var config =
     {x:250,y:300,height:300,width:300,modal:false,closable:false,
         draggable:true,resizable:true,initshow:false,minheight:200,minwidth:200
     }
     -->
</script>


</head>
<body>


<!--- Create a window with a title and show it. Don't allow dragging or resizing. --->
<cfwindow name="window1" title="CFML Window" draggable="false"
         resizable="false" initshow="true" height="250" width="250" x=375 y=0>
     <p>
     This content was defined in the cfwindow tag body.
     </p>
</cfwindow>




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   813
Requesting and Presenting Information




<form>
<!--- Use the API to show and hide Window 1. --->
     <input type="button" value="Show Window1"
                onClick="ColdFusion.Window.show('window1')">
     <input type="button" value="Hide Window1"
                onClick="ColdFusion.Window.hide('window1')"><br />


<!--- Use the API to create, show, and hide Window 2 --->
     <input type="button" value="Create Window2"
                onClick="ColdFusion.Window.create('window2', 'JavaScript Window',
                'window2.cfm', config)">
     <input type="button" value="Show Window2"
                onClick="ColdFusion.Window.show('window2')">
     <input type="button" value="Hide Window2"
           onClick="ColdFusion.Window.hide('window2')">
</form>


</body>
</html>

The window2.cfm file with the contents of Window 2 has the following contents:

<cfoutput>
<p>
This content was loaded into window 2 from a URL.<br />
</p>
</cfoutput>


Use the window show and hide events
You can use the onShow and onHide events that are triggered each time a window shows and hides to control your
application. To do so, call the ColdFusion.Window.onShow and ColdFusion.Window.onHide functions to specify the
event handlers. Both functions take the window name and the handler function as parameters. The event handler
functions can take a single parameter, the window name.

The following example displays an alert message when a window hides or shows. The alert message includes the
window name. The alert does not show when the window first appears, because the cfwindow tag uses the initShow
attribute to initially display the window. An alert message does appear when the user hides the window by clicking the
Toggle Window button or the close button on the window.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 814
Requesting and Presenting Information




<html>
<head>
    <script language="javascript">
         //Boolean value tacking the window state.
         var shown=true;


         //Functions to display an alert box when
         function onshow(name) {
               alert("window shown = " + name);
         }
         function onhide(name) {
               alert("window hidden = " + name);
         }


         //Initialize the window show/hide behavior.
         function initWindow() {
               ColdFusion.Window.onShow("testWindow", onshow);
               ColdFusion.Window.onHide("testWindow", onhide);
         }


         //Show or hide the window, depending on its current state.
         function toggleWindow() {
               if (shown) {
                    ColdFusion.Window.hide("testWindow");
                    shown = false;
               }
               else {
                    ColdFusion.Window.show("testWindow");
                    shown = true;
               }
         }


    </script>
</head>
<!-- The body tag onLoad event calls the window show/hide initializer function. -->
<body onLoad="initWindow()">


<cfwindow name="testWindow" initshow=true title="test window" closable=true> Window contents
</cfwindow>


<cfform>
    <cfinput name="button" value="Toggle Window" onclick="javascript:toggleWindow()"
type="button"/>
</cfform>
</body>
</html>


Control container contents
ColdFusion provides a variety of ways to set and change container tag contents:

* You can use bind expressions in the container tag source (or for cfdiv, bind) attribute. The container then
  dynamically updates any time a bound control changes.

* You can call the ColdFuson.navigate function to change the container body to be the contents returned by a
  specified URL. This function lets you specify a callback handler to do additional processing after the new content
  loads, and also lets you specify an error handler.



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         815
Requesting and Presenting Information




   The callback handler can be useful to provide information about a successful navigation operation. For example,
   you could make a pod's title bar italic to indicate loading (just before the navigate call), and use the callback handler
   to switch it back to normal once the navigate completes. Similarly, if a pod is shows pages from a book, the callback
   handler could update a page number in a separate field once a page loads

* You can use the special controlName_body variable to access and change the body contents for cfpod and
   cfwindow controls. For example, you can use the controlName_body.innerHTML property to set the body HTML.
   For cfpod and cfwindow tags, you can also use the controlName_title to get or set the title bar contents of the
   control.

These different techniques provide you with flexibility in writing your code. For example, the ColdFuson.navigate
function and the controlName_body variable provide similar functionality. However, with the controlName_body
technique, you make explicit Ajax requests to get markup for the body, and the JavaScript functions in the retrieved
markup might not work properly. ColdFusion.navigate takes care of these issues. Therefore, limit use of the
controlName_body technique to simpler use cases.

The following example shows how you can use various techniques to change container contents. It consists of a main
page and a second windowsource.cfm page with text that appears in a main page window when you click a button. The
main page has a cfpod control, two cfwindow controls, and the following buttons:

* The "Simple navigate" button calls a ColdFusion.navigate function to change the contents of the second
   window.

* The "Change w2 body & title" button replaces the second window's body and title innerHTML values directly to
   specific strings.

* The "Change pod body" button changes the pod body innerHTML to the value of the second window's title
   innerHTML.

The following example shows the main page:

<html>
<head>
<!--- Callback handler puts text in the window.cfm callback div block. --->
<script language="javascript">
    var mycallBack = function(){
          document.getElementById("callback").innerHTML = "<br><br>
    <b>This is printed by the callback handler.</b>";
    }


<!--- The error handler pops an alert with the error code and message. --->
    var myerrorHandler = function(errorCode,errorMessage){
          alert("[In Error Handler]" + "\n\n" + "Error Code: " + errorCode + "\n\n" +
                "Error Message: " + errorMessage);
    }
</script>
</head>


<body>
<cfpod height="50" width="200" title="The Title" name="theTitle">
    This is a cfpod control.
</cfpod><br>


<!--- Clicking the link runs a ColdFusion.navigate function that replaces the second window's
                contents with windowsource.cfm. The callback handler then updates the window
                contents further. --->
<cfwindow name="w1" title="CF Window 1" initShow=true
          x=10 y=200 width="200">



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    816
Requesting and Presenting Information




    This is a cfwindow control.<br><br>
    <a href="javascript:ColdFusion.navigate('windowsource.cfm','w2',
         mycallBack,myerrorHandler);">Click</a> to navigate Window 2</a>
</cfwindow>


<cfwindow name="w2" title="CF Window 2" initShow=true
         x=250 y=200 width="200">
    This is a second cfwindow control.
</cfwindow>


<cfform>
    <!--- This button only replaces the second window body with the body of the
                    windowsrc.cfm page. --->
    <cfinput type="button" name="button" value="Simple navigate"
               onClick="ColdFusion.navigate('windowsource.cfm','w2');">
    <!--- This button replaces the second window body and title content. --->
    <cfinput type="button" name="button2" value="Change w2 body & title"
               onClick="w2_body.innerHTML='New body inner HTML';w2_title.innerHTML=
                    'New Title inner HTML'">
    <!--- This button puts the second window title in the pod body. --->
    <cfinput type="button" name="button3" value="Change pod body"
               onClick="theTitle_body.innerHTML=w2_title.innerHTML;">
</cfform>
</body>
</html>

The following example shows the windowsource.cfm page:

This is markup from "windowsource.cfm"
<!--- The callback handler puts its output in the following div block. --->
<div id="callback"></div>



Using menus and toolbars
The cfmenu and cfmenuitem tags let you create vertical menus and horizontal toolbars.


Define menus and toolbars
* Use a single cfmenu tag to define the general menu characteristics.

* Create a horizontal (toolbar) menu or vertical menu by specifying a cfmenutype attribute value of horizontal or
  vertical.

* Menus can have submenus, but only the top menu can be horizontal. All children of a horizontal menu are vertical.

* The top-level menu shows initially, a submenu shows when the user moves the mouse over the menu root in the
  parent menu.

* Use cfmenuitem tags to specify individual menu items.

* To create submenus, nest cfmenuitem tags. The parent tag becomes the root of the submenu.

* All cfmenuitem tags, except tags for dividers, must have a display attribute, which defines the text to show on the
  menu item, and can optionally have an image attribute.

* A horizontal menu has dividers between all items. You place dividers in vertical menus by specifying a cfmenuitem
  tag with a divider attribute.

* To make a menu item active, specify a href attribute with a URL or a JavaScript function to call when the user clicks
  the menu item.



                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     817
Requesting and Presenting Information




The following example shows a simple horizontal menu with submenus that uses JavaScript to change the display
contents. When the user selects an end item in a menu, the text in the div block below the menu shows the path to the
selected menu.

<html>
<head>
</head>
<body>


<!--- The selected function changes the text in the selectedItemLabel div block to show the
           selected item. --->
<script type="text/javascript">
     function selected(item) {
           var el = document.getElementById("selectedItemLabel");
           el.innerHTML = "You selected: " + item;
     }
</script>


<!--- A horizontal menu with nested submenus. Clicking an end item calls the selected
           function. --->
<cfmenu name="hmenu" bgcolor="##9999ff" selectedfontcolor="##0000dd"
           selecteditemcolor="##ddddff">
     <cfmenuitem display="Home" href="javascript:selected('Home');" />
     <cfmenuitem display="File">
           <cfmenuitem display="Open...">
               <cfmenuitem display="Template" href="javascript:selected('File &gt;
                     Open... &gt; Template');" />
               <cfmenuitem divider="true" />
               <cfmenuitem display="CSS" href="javascript:selected('File &gt; Open... &gt;
                     CSS');" />
           </cfmenuitem>
           <cfmenuitem display="Close" href="javascript:selected('File &gt; Close');" />
     </cfmenuitem>
     <cfmenuitem display="Help">
           <cfmenuitem display="About" href="javascript:selected('Help &gt; About');" />
     </cfmenuitem>
</cfmenu>


<!--- A div with initial text.
           The selected function changes the text by resetting the innerHTML. --->
<div style=" margin-top: 100; margin-left: 10;"><span id="selectedItemLabel">
     Please select an item!</span></div>


</body>
</html>


Styling menus
The cfmenu and cfmenuitem tags have several attributes that let you easily control the menu appearance. These
attributes consist of two types: basic and CSS style. Basic attributes, such as the cfmenu tag fontColor attribute,
control individual menu characteristics. CSS style attributes let you specify a CSS style specification for a whole menu
or part of a menu. The following information describes how the CSS style specifications interact and affect the menu
style. For descriptions of all style-related attributes, see the cfmenu and cfmenuitem descriptions in the CFML
Reference.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                      818
Requesting and Presenting Information




The cfmenu and cfmenuitem tags provide a hierarchy of CSS style attributes that affect different parts of the menu.
The following table describes these attributes in hierarchical order:


 Attribute                       Description

 cfmenu attributes

 menuStyle                       Applies to the menu, including any parts of the menu that surround the menu items. If you do not
                                 override this style in a cfmenu tag childStyle attribute or by specifying style information in the
                                 cfmenuitem tags, this attribute controls the style of the top-level items.


 childStyle                      Applies to the items in the top level menu and all child menu items, including the children of submenus.
                                 This attribute lets you use a single style specification for all menu items.

 cfmenuitem attributes

 style                           Applies to the current menu item only. It is not overridden by the childStyleattribute.

 menuStyle                       Controls the overall style of any submenu of this menu item. This attribute controls the submenu of the
                                 current menu item, but not to any child submenus of the submenu.

 childStyle                      Applies to all child menu items of the current menu item, including the children of submenus.


In addition to these styles, consider any style-related attributes, such as bgcolor, that you set on the cfmenu tag.

When you design your menu, keep in mind the following issues:

* Keep font sizes at 20 pixels or smaller. Larger sizes can result in menu text in vertical menus exceeding the menu
   boundaries.

* Consider how the style attributes interact. Because each menu and submenu consists of a surrounding menu area
   and individual child items, be careful when you choose background colors. For example, if you specify different
   background-color styles in the cfmenu tag's menuStyle and childStyle attributes, the menu items are one color
   and the surrounding menu area are a different color.

For an application that shows some of the effects of menu style attributes, see the example in the cfmenuitem tag in
the CFML Reference.

ColdFusion attributes provide most style options that you are likely to require. However, you can, if necessary, modify
the basic menu styles for all menus by editing the menu-related styles in the CSS files in the yui.css file. This file is
located by default in the web_root/CFID/scripts/ajax/resources/yui directory. For more information about these styles,
see the Yahoo! User Interface Library menu documentation.


Uploading files
The cffileupload tag lets you select multiple files and upload them to a server.


Working with the cffileupload
The cffileupload tag displays a dialog that lets you upload multiple files. The following are the file upload features:

* Uses callback and error handlers that lets provides control over file upload process after upload completion or if
   errors occur.

* Lets you style the file upload control

* Provides option to stop or continue with upload in case of errors

* Provides option to send custom response to callback and error handlers




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       819
Requesting and Presenting Information




Sending custom response to the callback and error handlers
The page/URL that handles the upload operation on the server can send back a struct with the keys status and
message as shown here:

<cffile action = "upload"
destination = "#Expandpath('./upload')#"
nameconflict="makeunique">
<cfset str.STATUS = 200>
<cfset str.MESSAGE = "File Upload Successful"><cfoutput>#serializeJSON(str)#</cfoutput>

The following example illustrates the error handler:

<cftry>
<cffile action = "upload"
destination = "#Expandpath('./upload')#">
<cfcatch type="any">
<cfset str.STATUS = 500>
<cfset str.MESSAGE = "Error occurred while uploading the file">
<cfoutput>#serializeJSON(str)#</cfoutput>
</cfcatch>
</cftry>

If the user tries to upload a file already present in the upload directory, it results in an error. The status and message
are set to the specifications in the catch block.


Using styles
The attributes headercolor, textcolor, bgcolor, titletextalign, titletextcolor, and rollovercolor lets
you style the file upload control.

The following example illustrates the styling of file upload control:

<cffileupload
              url="uploadAll.cfm"
              name="myuploader3"
              align="right"


style="headercolor:silver;textcolor:1569C7;titletextalign:right;titletextcolor:black;bgcolor
:74BBFB;"/>

The following code shows how the attribute onUploadComplete is used:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  820
Requesting and Presenting Information




<!--- upload.cfm --->
<!--- <cffile action = "upload" destination = "#Expandpath('./upload')#"
nameconflict="makeunique"> --->
<script language="javascript">
     var uploadCompleteHandler = function(obj){
          var result = "Upload Details:" + "\n\n";
          for(var i=0;i < obj.length; i++){
                result = result + "FILENAME: " + obj[i].FILENAME + "\n" + "STATUS: " + obj[i].STATUS
+ "\n" + "MESSAGE: " + obj[i].MESSAGE + "\n\n";
          }
     }
</script>
<br>
<cffileupload
     url="uploadall.cfm"
     name="myuploader"
     onUploadComplete="uploadCompleteHandler"
     maxUploadSize=100
     stopOnError=false
     />



Using Ajax form controls and features
ColdFusion HTML forms and controls provide the following Ajax-based features:

* The cfgrid, cfinput, cfselect, cftextarea, and cftree controls support binding to get control contents.

* ColdFusion functions support asynchronous submission of forms without refreshing the entire page. When a form
  is in an Ajax container control, it is done automatically. Also, the ColdFusion.Ajax.SubmitForm JavaScript
  function and Ajax proxy setForm function support manual asynchronous submissions.

* The cfgrid and cftree tags provide HTML grids and trees that do not require a Java applet or Flash.

* The cftextarea control has a rich text editor option. The text editor is configurable.

* The cfinput tag supports a datefield type with an Ajax-based pop-up calendar from which user can select the date.

* The cfinput tag with text type supports an autosuggest attribute that lets you dynamically supply a drop-down
  list of field completions based on the current user input.

* The cfinput, cfselect, and cftextarea tags support a tooltip attribute that specifies a pop-up tool tip to
  display when the user moves the mouse over the control. The cftooltip tag displays a tool over any region of a
  page, not just a form control.


Using Ajax form controls
ColdFusion Ajax-based form controls let you submit Ajax forms in your applications without refreshing the entire page.

Note: When you use Ajax to submit forms asynchronously from the page, you cannot use cfinput or input tags to
upload files.


Using Ajax containers for form submission
The ColdFusion Ajax container tags, cfdiv, cflayoutarea, cfpod, and cfwindow, automatically submit any forms
that they contain asynchronously. When the form is submitted, the result returned by the action page replaces the
contents of the container, but has no effect on the rest of the page.

The following example shows this behavior in the submitSimple.cfm page:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      821
Requesting and Presenting Information




<html xmlns="http://www.w3.org/1999/xhtml">
<head>
</head>
<body>
<cflayout type="vbox" name="layout1">
     <cflayoutarea>
           <h3>This area is not refreshed when the form is submitted.</h3>
           <br />
     </cflayoutarea>


     <cflayoutarea>
           <h3>This form is replaced by the action page</h3>
           <cfform name="myform" format="html" action="showName.cfm">
                 <cfinput type = "Text" name = "name">
                 <cfinput type = "submit" name = "submit" value = "Enter name">
           </cfform>
     </cflayoutarea>
</cflayout>


</body>
</html>

In the following example, when you enter a name in the text input and click the Enter name button, the entered text
replaces the form on the page, but the rest of the page is not refreshed. This example shows the showName.cfm action page:

<cfif IsDefined("Form.name")>
     <cfoutput>The Name is : <strong>#Form.name#</strong></cfoutput>
</cfif>


Using the cfajaxproxy SetForm function
The SetForm function of the proxy object created by the cfajaxproxy tag causes the proxy to pass the form values as
arguments to the next CFC function that you call after the SetForm function. This way, you can pass the current values
of fields in a form to a CFC function, which can then do the necessary processing and return a result.

When you use the SetForm function, the following rules apply to the arguments in the called CFC function:

* The function does not need to specify the form fields in cfargument tags, and the function gets the field values
   passed by name.

* Form fields that have the same names as CFC arguments override the CFC argument values.

* If you do not specify form fields in the cfargument tags, they do not necessarily follow any declared arguments,
   when you use positional (array) notation to access them in the arguments structure.

* The arguments scope in the CFC function includes two fields that ColdFusion uses to control its behavior. These
   fields are intended for internal use, and their names might change in future releases. Both field values are set to
   true:

    * _CF_NODEBUG tells ColdFusion not to return debugging output in the call response.

    * _CF_NOCACHE tells ColdFusion to send a no cache header on the response, which prevents the browser from
       caching the response and ensures that every Ajax request results in a network call.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       822
Requesting and Presenting Information




The following example shows how to use the SetForm tag to submit the contents of a login form. When the user clicks
the Login! button, the doLogin function calls the proxy setForm function and then the AuthenticationSystem.cfc
validateCredentials method. The validateCredentials method checks the password of the user and if it is
valid, returns true to the proxy. Because the proxy is synchronous (the default), the doLogin method gets the returned
value. If the value is true, it hides the login window; the user can then access the page contents. If the return value is
false, the doLogin function displays a message in the login window title bar.

The following example shows the setForm.cfm application:

<html>
<head>
           <script type="text/javascript">
                function doLogin() {
                      // Create the Ajax proxy instance.
                      var auth = new AuthenticationSystem();


                      // setForm() implicitly passes the form fields to the CFC function.
                      auth.setForm("loginForm");
                      //Call the CFC validateCredentials function.
                      if (auth.validateCredentials()) {
                           ColdFusion.Window.hide("loginWindow");
                      } else {
                           var msg = document.getElementById("loginWindow_title");
                           msg.innerHTML = "Incorrect username/password. Please try again!";
                      }
                }
           </script>
</head>


<body>
<cfajaxproxy cfc="AuthenticationSystem" />


<cfif structKeyExists(URL,"logout") and URL.logout>
     <cflogout />
</cfif>


<cflogin>
     <cfwindow name="loginWindow" center="true" closable="false"
                      draggable="false" modal="true"
                      title="Please login to use this system"
                      initshow="true" width="400" height="200">
           <!--- Notice that the form does not have a submit button.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                823
Requesting and Presenting Information




               Submit action is performed by the doLogin function. --->
          <cfform name="loginForm" format="xml">
               <cfinput type="text" name="username" label="username" /><br />
               <cfinput type="password" name="password" label="password" />
               <cfinput type="button" name="login" value="Login!" onclick="doLogin();" />
          </cfform>
    </cfwindow>
</cflogin>


<p>
This page is secured by login.
You can see the window containing the login form.
The window is modal; so the page cannot be accessed until you log in.
<ul>
    <li><a href="setForm.cfm">Continue using the application</a>!</li>
    <li><a href="setForm.cfm?logout=true">Logout</a>!</li>
</ul>
</p>
</body>
</html>

The following example shows the AuthenticationSystem.cfc file:

<cfcomponent output="false">


    <cffunction name="validateCredentials" access="remote" returntype="boolean"
               output="false">
          <cfargument name="username" type="string"/>
          <cfargument name="password" type="string"/>


          <cfset var validated = false/>
          <!--- Ensure that attempts to authenticate start with new credentials. --->
          <cflogout/>


          <cflogin>
               <cfif arguments.username is "user" and arguments.password is "secret">
                    <cfloginuser name="#arguments.username#"
                               password="#arguments.password#" roles="admin"/>
                    <cfset validated = true/>
               </cfif>
          </cflogin>


          <cfreturn validated/>
    </cffunction>
</cfcomponent>


Using the ColdFusion.Ajax.submitForm function
You can use the ColdFusion.Ajax.submitForm function to submit form contents to a CFML page (or other active
page) at any time. For example, you could use this function to automatically save a partially completed form. This
function does not support uploading a file attachment to the form.

When you use this function, you pass it the name of the form to submit and the URL of the page that processes the
form. You can also specify the following optional parameters:

* A callback function that handles the returned results

* An error handler that takes two parameters, an HTTP error code and a message



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                824
Requesting and Presenting Information




* The HTTP method (by default, POST)

* Whether to submit the form asynchronously (by default, true)

The following proof of concept example uses the ColdFusion.Ajax.submitForm function to submit two form fields
to an asyncFormHandler.cfm page, which simply echoes the form values. The callback handler displays an alert with
the returned information.

<html>
<head>
<!--- The cfajaximport tag is required for the submitForm function to work
               because the page does not have any Ajax-based tags. --->
<cfajaximport>


<script>
     function submitForm() {
          ColdFusion.Ajax.submitForm('myform', 'asyncFormHandler.cfm', callback,
               errorHandler);
     }


     function callback(text)
     {
          alert("Callback: " + text);
     }


     function errorHandler(code, msg)
     {
          alert("Error!!! " + code + ": " + msg);
     }
</script>


</head>
<body>


<cfform name="myform">
     <cfinput name="mytext1"><br />
     <cfinput name="mytext2">
</cfform>


<a href="javascript:submitForm()">Submit form</a>
</body>
</html>

The asynchFormHandler.cfm page consists of a single line, as follows:

<cfoutput>Echo: #form.mytext1# #form.mytext2#</cfoutput>


Using the ColdFusion.navigate function to submit a form
The ColdFusion.navigate JavaScript function can submit a form to a URL and have the returned output appear in
a specified container control, such as a cfdiv, cflayout, cfpod, or cfwindow tag. This function lets you populate a
control other than the one that contains the form when the user submits the data. You can also use the function to
submit the form asynchronously when a user performs an action outside the form, such as clicking a menu item.

For an example that uses this function, see the ColdFusion.navigate function in the CFML Reference.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                           825
Requesting and Presenting Information




Using HTML grids
The ColdFusion HTML cfgrid control lets you use a bind expression to dynamically populate the grid. HTML grids
that use bind expressions are paged; as users navigate from page to page of the grid, the grid dynamically gets the data
for only the required page from the data source. You also use bind expressions when you let users edit form contents,
and other ColdFusion controls can bind to the grid. Also, HTML grids provide several JavaScript functions that you
can use to manage and manipulate the grids.

You can also create a static HTML grid by specifying a cfgrid tag that does not use a bind expression. With static
grids, all data is initially available.

In HTML mode, if you move the mouse over a column heading, a down arrow button appears. Clicking the button
displays a list with the following options:

* Sort the grid in ascending or descending order based on the column's content.

* Select the columns to display.

* If the grid has a groupfield attribute: turn grouping off and on and group by the column value.

If you specify selectMode="edit" for an HTML grid, the grid displays Insert, Save, Cancel, and Delete buttons to
the bottom bar. The Insert button opens a new editable row. The Save button commits any changes to the bind source.
The Cancel button rolls back any changes that have not been saved. The Delete button deletes a selected row. You need
not press the Save button after clicking the Delete button.


Dynamically filling form data
HTML grids can dynamically fill the grid data by using a bind attribute with a bind expression that calls a CFC or
JavaScript function, or a URL. The bind expression uses bind parameters to specify dynamic information provided by
the grid and the values of any other form field attributes.

Pass the following bind parameters to the bind expression. If you omit any of the parameters in the function call or
URL, you get an error. These parameters send information about the grid and its state to the data provider function.
The data for these parameters is provided automatically. You do not set any values manually.


 Parameter name                   Description

 cfgridpage                       The number of the page for which to retrieve data.

 cfgridpagesize                   The number of rows of data in the page. The value of this parameter is the value of the pageSize
                                  attribute.

 cfgridsortcolumn                 The name of the column that determines the sorting order of the grid. This value is set only after the user
                                  clicks a column heading.

 cfgridsortdirection              The direction of the sort, may be 'ASC' (ascending) or 'DESC' (descending). This value is set only after the
                                  user clicks a column heading.


Note: The cfgridsortcolumn and cfgridsortdirection parameters can be empty if the user or application has not
sorted the grid, for example, by clicking a grid column header.

For more information on binding and bind parameters, see "Using Ajax Data and Development Features" on page 858
in the CFML Reference.

You can use optional parameters to specify additional information to pass to the called function. These parameters
provide data that the called function requires to determine the data to return. For example, if the function returns the
cities in a state, you would pass it the state name. Any or all of the optional function parameters can be bind parameters.
A state name, for example, could come from the selection in a states cfselect control.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 826
Requesting and Presenting Information




If you do not want the grid to refresh automatically when other controls change, you can use the @none specifier on all
optional bind parameters. Doing this, prevents automatic updating of the grid based on the bound control values. Use
the ColdFusion.Grid.refresh JavaScript function to explicitly refresh the grid contents. For more information on the
use of the @none specifier and explicitly refreshing the control, see "Specifying bind parameters" on page 861.

If the grid supports user sorting of the data (the sort attribute is true), the function called by the bind expression must
return data in the desired sorted order, and must use the values of the cfgridsortcolumn and
cfgridsortdirection bind parameters to determine the order. Even if you do not allow user sorting, still pass these
parameters to the function; otherwise, you get an error. Also, your function or action page must handle cases where
these parameters are empty strings, because their values are not set until the user selects a column header to sort the
grid, or you call the JavaScript ColdFusion.Grid.sort function.

The format of the returned data depends on how you get the data:


 Bind type                      Return value

 CFC                            A ColdFusion structure. ColdFusion automatically converts the structure for return to the caller.
                                Alternatively, you can return a JSON representation of the structure.

 URL                            A JSON representation of a structure. No other body contents is allowed.

 JavaScript                     A JavaScript object.


When you specify a CFC in the bind attribute, use the queryConvertForGrid function to convert a query directly
into a structure that you can use as your CFC return value.

When you specify a CFML page in the bind attribute, use the queryConvertForGrid function to convert a query into
a structure, and then use the serializeJSON function to convert the structure into a JSON representation.

If you manually create a JavaScript object or its JSON representation, it must have two top-level keys:

* TOTALROWCOUNT: The total number of rows in the query data set being returned. This value is the total number of
    rows of data in all pages in the grid, and not the number of rows in the current page.

* QUERY: The contents of the query being returned. The QUERY value must also be an object with two keys:

    * COLUMNS: An array of the column names.

    * DATA: A two-dimensional array, where the first dimension corresponds to the rows and the second dimension
       corresponds to the field values, in the same order as the COLUMNS array.

Note: If a CFC manually creates a return structure, the QUERY value can be a ColdFusion query object; ColdFusion
automatically converts it for remote access.

The following example defines an object that a JavaScript bind function can return to provide the data for a cfgrid tag:

 var myobject =
     {"TOTALROWCOUNT":6,"QUERY":{"COLUMNS":["EMP_ID","FIRSTNAME",
           "EMAIL"],"DATA":[[1,"Carolynn","CPETERSON"],
           [2,"Dave","FHEARTSDALE"], [3,"Linda","LSTEWART"],
           [4,"Aaron","ASMITH"], [5,"Peter","PBARKEN"],
           [6,"Linda","LJENNINGS"],]}};

The following example uses a bind expression and a CFC to populate a dynamic, paged, data grid. The CFML page
contains the following form:




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       827
Requesting and Presenting Information




<html xmlns="http://www.w3.org/1999/xhtml">
<head>
</head>


<body>
<cfform name="form01">
    <cfgrid format="html" name="grid01" pagesize=5 sort=true
               bind="cfc:places.getData({cfgridpage},{cfgridpagesize},
               {cfgridsortcolumn},{cfgridsortdirection})">
          <cfgridcolumn name="Emp_ID" display=true header="eid" />
          <cfgridcolumn name="FirstName" display=true header="Name"/>
          <cfgridcolumn name="Email" display=true header="Email" />
    </cfgrid>
</cfform>
</body>
</html>

The places.cfc file looks as follows. Notice that the query gets the full data set each time the function gets called. the
QueryConvertForGrid function selects and returns only the required page of data:

<cfcomponent>
    <cffunction name="getData" access="remote" output="false">
          <cfargument name="page">
          <cfargument name="pageSize">
          <cfargument name="gridsortcolumn">
          <cfargument name="gridsortdirection">
          <cfquery name="team" datasource="cfdocexamples">
               SELECT Emp_ID, FirstName, EMail
               FROM Employees
               <cfif gridsortcolumn neq "" or gridsortdirection neq "">
                     order by #gridsortcolumn# #gridsortdirection#
               </cfif>
          </cfquery>
          <cfreturn QueryConvertForGrid(team, page, pageSize)>
    </cffunction>
</cfcomponent>

The following example is equivalent to the previous one, but uses a URL bind expression in the main page and a CFML
page to return the data.

The main page contains the following form:

<html xmlns="http://www.w3.org/1999/xhtml">
<head>
</head>


<body>
<cfform name="form01">
    <cfgrid format="html" name="grid01" pagesize=5 sort=true
          bind="url:getdata.cfm?page={cfgridpage}&pageSize={cfgridpagesize}
               &sortCol={cfgridsortcolumn}&sortDir={cfgridsortdirection}">
          <cfgridcolumn name="Emp_ID" display=true header="eid" />
          <cfgridcolumn name="FirstName" display=true header="Name"/>
          <cfgridcolumn name="Email" display=true header="Email" />
    </cfgrid>
</cfform>
</body>
</html>




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          828
Requesting and Presenting Information




The following example shows the getdata.cfm page:

 <!--- Empty string; the default end of the query SQL. --->
<cfset queryEnd="">


<cfquery name="team" datasource="cfdocexamples">
      SELECT Emp_ID, FirstName, EMail
      FROM Employees
      <cfif sortcol neq "" or sortdir neq "">
            order by #sortcol# #sortdir#
      </cfif>
</cfquery>


<!--- Format the query so that the bind expression can use it. --->
<cfoutput>#serializeJSON(QueryConvertForGrid(team, page, pageSize))#
</cfoutput>

If your database lets you specify SQL to retrieve only the required page of data in a query, you can optimize efficiency
by using such a query. Do not use the QueryConvertForGrid function. Instead, manually create the return structure
and return only the single page of data. Ensure that you set the TotalRowCount field to the number of rows in the entire
data set, not the number of rows in the returned page of data.


Using the bindOnLoad attribute
The bindOnLoad attribute causes a control to execute its bind expression immediately when it loads, and not wait until
the event that normally triggers the bind expression evaluation to occur. This way, the control can be filled with an
initial value. This attribute is false by default for all ColdFusion Ajax controls that have the attribute, except cfdiv
and cfgrid, for which it is true by default. Having a truebindOnLoad value on these controls ensures that they are
populated when they load.

When a control with a truebindOnLoad attribute is bound to a control that also binds when the page loads, the first
and second control load themselves at the onLoad page event. Then the first control loads itself again in response to a
change event from the second control when that control completes loading. So, the first control makes two Ajax calls,
whereas it must make only one, when the second control finished loading.

Because the cfinput, cfselect, and cftextarea control bindOnLoad attributes are false by default, you do not
encounter any problems if a cfgrid or cfdiv tag binds to any of these controls and you do not explicitly set the
bindOnLoad attributes. However, if the control does set its bindOnLoad attribute to true, set the cfgrid or cfdiv
attribute to false to ensure that the control only fetches data when the control that it is bound to returns.

You can also get a double loading if a grid binds to a Spry data set. By default, the grid and data set load data at page
load, and then the grid loads data again in response to a selection change event from the data set when it sets focus to
its first row. Set bindOnLoad to false to ensure that the grid fetches data only when it receives a selection change
event from the data set.


Dynamically editing grid contents
When you use a bind expression to get cfgrid data dynamically, you can also update the data source dynamically with
user input, without requiring the user to submit the form. You can use dynamic updating to update or delete data in
the data source. (To edit cfgrid data, select the contents of a field and type the new value; to delete a row, select a field
in the row and click the delete button at the bottom of the grid.)

You cannot insert new rows directly in a grid that uses a bind expression. To add rows, enter the data in a form, and
make sure that the grid refreshes after the form has been submitted.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                          829
Requesting and Presenting Information




To update or delete data dynamically, do the following:

* Specify selectmode="edit" in the cfgrid tag. This lets the user edit the grid.

* Specify an onChange attribute in the cfgrid tag. The attribute must use a bind expression to specify a CFC method,
   JavaScript function, or URL of a page that updates the data source. The bind expression has the same format as the
   bind expression described in "Dynamically filling form data" on page 825; however, it must take the following bind
   parameters that the grid automatically passes. These parameters send information about the grid and its state to the
   onChange function.


    Parameter name                 Description

    cfgridaction                   The action performed on the grid. 'U' for update, or 'D' for delete.

    cfgridrow                      A structure or JavaScript Object whose keys are the column names and values are the original values
                                   of the updated or deleted row.

    cfgridchanged                  A structure or JavaScript Object with a single entry, whose key is the name of the column with the
                                   changed value, and whose value is the new value of the field. If the grid action is delete, this structure
                                   exists but is empty.


When you update data dynamically, you can also use the onError attribute to specify the name of a JavaScript function
to handle any errors that result in a CFC or URL returning an HTTP error status. The method must take two
parameters: the HTTP error code and a text message that describes the error. The following example shows an
onError handler function:

 <script type="text/javascript">
    function errorhandler(id,message) {
          alert("Error while updating \n Error code: "+id+" \nMessage:
          "+message);}
</script>

The following example displays the members of a department and lets users edit the data in the fields. When the focus
leaves the edited field an onChange event triggers and the form calls the editData CFC function to update the data
source.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       830
Requesting and Presenting Information




<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<script type="text/javascript">
    function errorhandler(id,message) {
          alert("Error while updating\n Error code: "+id+"\n Message: "+message);
    }
</script>
</head>


<body>
<cfform name="form01">
    <cfgrid format="html" name="grid01" pagesize=11
               stripeRows=true stripeRowColor="gray"
               bind="cfc:places.getData({cfgridpage},{cfgridpagesize},
                    {cfgridsortcolumn},{cfgridsortdirection})"
               delete="yes" selectmode="edit"
               onchange="cfc:places.editData({cfgridaction},{cfgridrow},{cfgridchanged})">
          <cfgridcolumn name="Emp_ID" display=true header="Employee ID"/>
          <cfgridcolumn name="FirstName" display=true header="Name"/>
          <cfgridcolumn name="Email" display=true header="Email"/>
    </cfgrid>
</cfform>
</body>
</html>

The getData function is identical to the getData function in "Dynamically filling form data" on page 825. This
example shows the editData function in the CFC:

<cffunction name="editData" access="remote" output="false">
    <cfargument name="gridaction">
    <cfargument name="gridrow">
    <cfargument name="gridchanged">


    <cfif isStruct(gridrow) and isStruct(gridchanged)>
          <cfif gridaction eq "U">
               <cfset colname=structkeylist(gridchanged)>
               <cfset value=structfind(gridchanged,#colname#)>
               <cfquery name="team" datasource="cfdocexamples">
                    update employees set <cfoutput>#colname#</cfoutput> =
                           '<cfoutput>#value#</cfoutput>'
                    where Emp_ID = <cfoutput>#gridrow.Emp_ID#</cfoutput>
               </cfquery>
          <cfelse>
               <cfquery name="team" datasource="cfdocexamples">
                    delete from employees where emp_id = <cfoutput>#gridrow.Emp_ID#
                           </cfoutput>
               </cfquery>
          </cfif>
    </cfif>
</cffunction>


Binding controls to grid contents
You can bind the contents of a form control to the data in a grid field by specifying a bind parameter as the form control
bind attribute value. To do so, use the following syntax:

<cfinput name="name" type="text" bind="{gridName.columnName}">




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   831
Requesting and Presenting Information




By default, each time the selected row in the grid changes, the bind parameter is re-evaluated, and the control value
changes to the value of the specified column of selected grid cell.


Grid JavaScript functions
You can use the following JavaScript functions to manage an HTML format grid:


 Function                                 Description

 ColdFusion.Grid.getGridObject            Gets the underlying Ext JS JavaScript library object.

 ColdFusion.Grid.refresh                  Manually refreshes a displayed grid.

 ColdFusion.Grid.sort                     Sorts the grid.


For more information, see the ColdFusion.Grid.getGridObject, ColdFusion.Grid.refresh, and ColdFusion.Grid.sort
functions in the CFML Reference.


Using the Boolean column and grouping
The example in this section shows how to use the Boolean column. The code also illustrates how to group data in a
grid on a selected grid column.

This example uses all types of Boolean representations. For grouping, groupField is set to active and the grid data is
grouped accordingly.

<cfset emps = querynew("firstname,department, salary,active")>
<cfset queryaddrow(emps,10)>
<cfset querysetcell(emps,"firstname","Debra",1)>
<cfset querysetcell(emps,"department","Accounting",1)>
<cfset querysetcell(emps,"salary","100000",1)>
<cfset querysetcell(emps,"active","Y",1)>
<cfset querysetcell(emps,"firstname","Doherty",2)>
<cfset querysetcell(emps,"department","Finance",2)>
<cfset querysetcell(emps,"salary","120000",2)>
<cfset querysetcell(emps,"active","Yes",2)>
<cfset querysetcell(emps,"firstname","Ben",3)>
<cfset querysetcell(emps,"department","Law",3)>
<cfset querysetcell(emps,"salary","200000",3)>
<cfset querysetcell(emps,"active","true",3)>
<cfset querysetcell(emps,"firstname","Aaron",4)>
<cfset querysetcell(emps,"department","Accounting",4)>
<cfset querysetcell(emps,"salary","200000",4)>
<cfset querysetcell(emps,"active","1",4)>
<cfset querysetcell(emps,"firstname","Josh",5)>
<cfset querysetcell(emps,"department","CF",5)>
<cfset querysetcell(emps,"salary","400000",5)>
<cfset querysetcell(emps,"active",true,5)>
<cfset querysetcell(emps,"firstname","Peterson",6)>
<cfset querysetcell(emps,"department","Accounting",6)>
<cfset querysetcell(emps,"salary","150000",6)>
<cfset querysetcell(emps,"active","0",6)>
<cfset querysetcell(emps,"firstname","Damon",7)>
<cfset querysetcell(emps,"department","Finance",7)>
<cfset querysetcell(emps,"salary","100000",7)>
<cfset querysetcell(emps,"active","N",7)>
<cfset querysetcell(emps,"firstname","Tom",8)>
<cfset querysetcell(emps,"department","CF",8)>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                           832
Requesting and Presenting Information




<cfset querysetcell(emps,"salary","100000",8)>
<cfset querysetcell(emps,"active","false",8)>
<cfset querysetcell(emps,"firstname","Adam",9)>
<cfset querysetcell(emps,"department","CF",9)>
<cfset querysetcell(emps,"salary","300000",9)>
<cfset querysetcell(emps,"active",false,9)>
<cfset querysetcell(emps,"firstname","Sean",10)>
<cfset querysetcell(emps,"department","CF",10)>
<cfset querysetcell(emps,"salary","250000",10)>
<cfset querysetcell(emps,"active","No",10)>
<cfform name="form01">
    <cfgrid format="html"           insert="yes" insertButton="Add Row"
         name="grid01"
         selectmode="edit"
         width=600
         collapsible="true"
         title="Employees"
         autowidth="yes"
         query="emps"
         sort="yes"
         groupField="active">
               <cfgridcolumn name="FirstName" header="FirstName"/>
               <cfgridcolumn name="Department" header="Department" />
               <cfgridcolumn name="Salary" display=true header="Salary" type="numeric"
values="1000000,1200000" valuesdisplay="1000000,1200000"/>
               <cfgridcolumn name="Active"          display=true header="Contract" type="boolean" />
    </cfgrid>
</cfform>


Using the date column
The following example shows how to use the date column. In the code, startdatecolumn is of type date. A
mask,Y/m/d is used. Y is year in four digits, m months with leading zero, and d the days with leading zero.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     833
Requesting and Presenting Information




<!--- using cfgridcolum type="date" --->
<cfset emps = querynew("firstname,department, salary,startdate")>
<cfset queryaddrow(emps,3)>
<cfset querysetcell(emps,"firstname","Debra",1)>
<cfset querysetcell(emps,"department","Accounting",1)>
<cfset querysetcell(emps,"salary","100000",1)>
<cfset querysetcell(emps,"startdate","2009/1/1",1)>
<cfset querysetcell(emps,"firstname","Doherty",2)>
<cfset querysetcell(emps,"department","Finance",2)>
<cfset querysetcell(emps,"salary","120000",2)>
<cfset querysetcell(emps,"startdate","2005/2/21",2)>
<cfset querysetcell(emps,"firstname","Ben",3)>
<cfset querysetcell(emps,"department","Law",3)>
<cfset querysetcell(emps,"salary","200000",3)>
<cfset querysetcell(emps,"startdate","2008/03/03",3)>
<cfform name="form01">
     <cfgrid format="html"            insert="yes" insertButton="Add Row"
            name="grid01"
            selectmode="edit"
            width=600
            collapsible="true"
            title="Employees"
            autowidth="yes"
            query="emps"
            sort="yes"
            groupField="department">
                 <cfgridcolumn name="FirstName" header="FirstName"/>
                 <cfgridcolumn name="Department" header="Department" />
                 <cfgridcolumn name="Salary" display=true header="Salary" type="numeric"
values="1000000,1200000" valuesdisplay="1000000,1200000"/>
                 <cfgridcolumn name="StartDate"            display=true header="StartDate" type="date"
mask="Y/m/d"/>
     </cfgrid>
</cfform>

For details of various types of masks that can be used, see the CFML Reference Guide.


Datehandling when the attribute mask is used in ColdFusion 9.0.1
If the attribute mask is applied to a datefield column in an HTML grid, ColdFusion converts the date to an intermediate
format as shown here:

MMMMM, dd yyyy HH:mms

for example,

January, 19 2005 07:35:42

This is required for proper date conversion and is applicable both when data is sent to the server (for example, when
using an onChange grid event) and when data is received from the server (for example, populating a date field in a
grid). Therefore, in some cases, users might have to format the date if they are updating a date column in the database.

Note: Date values which are NULL are sent as empty strings when the form is submitted. In such cases, set the value to
NULL explicitly while updating the date column in the database.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  834
Requesting and Presenting Information




Using HTML trees
An HTML cftree tag creates an Ajax-based tree data representation that you can populate from a query or a bind
expression. The behavior with a query is equivalent to the behavior of applet or Flash trees. Bind expressions let you
populate the tree based on the values of other controls or Spry data sets. Also, when you use a bind expression, the tree
loads dynamically, getting only the data required for the current display.


Populating the tree using a bind expression
You use the bind attribute and bind expressions to dynamically and incrementally load and display tree data as the
user navigates the tree. The child tree items do not exist until the parent node expands. This behavior avoids prefilling
a tree with large amounts of data. It lets the tree children change dynamically (you can optionally get the children each
time the item expands) and can enhance application responsiveness.

For more information about binding and bind parameters, see "Binding data to form fields" on page 860.

Bind expressions in trees work in the following ways:

* If you use a bind expression, the cftree tag can have only a single cftreeitem tag. Therefore, the function or URL
   called by the bind expression must be able to populate all levels of the tree.

* When a tree item expands, the CFC or JavaScript function or active page specified by the bind attribute returns an
   array with the values for the child nodes of the item. The dynamic tree code on the client constructs the child items
   by using these values.

* When a control to which the tree is bound generates an event that the tree is listening for, the tree is refreshed. For
   example, if the tree uses a bind expression that includes a select box as a bind parameter, the tree collapses to the
   root nodes when the selected value in the select box changes.

When you use a bind expression to populate a cftree control, specify a CFC function, JavaScript function, or URL,
and pass the following bind parameters. If you omit either of the parameters from your function call or URL, you get
an error. These parameters provide information about the tree and its state, and are automatically provided by the
control.


 Bind parameter                Description

 {cftreeitempath}              Passes the path of the current (parent) node to the method, which uses it to generate the next node.

 {cftreeitemvalue}             Passes the current tree item value (normally the value attribute)


The called function or URL cannot return nested arrays and structures, that is, it can only return a single level of items.

When a function or URL is first called to populate the root-level tree items, the value passed in the cftreeitemvalue
variable is the empty string. Your bind function can test for an empty string to determine that it is populating the root
level of the tree.

The @none event specifier is also useful if you use the ColdFusion.Tree.refresh JavaScript function to manually refresh
the tree. When you call the Refresh function, the bind expression fetches data from all bind parameters, including
@none parameters. If you specify @none in all bind parameters that specify other controls, the tree does not respond
automatically to changes in the other controls, but it does pick up data from the bind parameters when you use the
ColdFusion.Tree.Referesh function to explicitly refresh the tree.

The format of the data that the function or URL in a bind expression must return depends on the type of bind
expression




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     835
Requesting and Presenting Information




 Bind type                     Return value

 CFC                           A ColdFusion array of structures. ColdFusion automatically converts the structure to JSON format when it
                               returns the result to the caller. Alternatively, you can return a JSON representation of the structure.

 JavaScript                    A JavaScript Array of Objects.

 URL                           A JSON representation of an array of structures. No other body content is allowed.


Each structure in the array of structures or objects defines the contents and appearance of the node for a child item.
Each structure must have a VALUE field, and can have the following fields. Except for LEAFNODE, these structure
keys correspond to cftreeitem attributes.

* DISPLAY

* EXPAND

* HREF

* IMG

* IMGOPEN

* LEAFNODE

* TARGET

Note: If a CFC does not return a value field, you do not get an error, but the tree does not work properly.

The LEAFNODE structure element is only used in the bind response structures. It must be a Boolean value that
identifies whether the node is a leaf. If the value is true, the tree does not show a +/- expansion indicator in front of
the node, and users cannot expand the node.

If your bind expression specifies a JavaScript function, the function must use all-uppercase letters for the field names;
for example, use VALUE and DISPLAY, not value and display. ColdFusion uses all capital letters in the structure key
names. ColdFusion is not case-sensitive, so CFCs can use lowercase letters for the field names; JavaScript is case-
sensitive, so the JavaScript function must match the uppercase field names.

If you use a URL to get the tree items from a CFML page, you can use the serializeJSON function to convert the array
to JSON format. If the array with the tree items is named itemsArray, for example, the following line specifies the page
output:

 <cfoutput>#serializeJSON(itemsArray)#</cfoutput>


Example 1: a simple tree
The following simple example creates a simple hierarchical tree of unlimited depth, with one node per level. Each node
label (specified by the display attribute) identifies the node depth:

The following example shows the CFML page:

 <cfform name="testform">
     <cftree name="t1" format="html">
           <cftreeitem bind="cfc:makeTree.getNodes({cftreeitemvalue},{cftreeitempath})">
     </cftree>
</cfform>

The following example shows the maketree.cfc file with the getNodes method that is called when the user expands a node:




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        836
Requesting and Presenting Information




 <cfcomponent>
     <cffunction name="getNodes" returnType="array" output="no" access="remote">
           <cfargument name="nodeitemid" required="true">
           <cfargument name="nodeitempath" required="true">
           <!--- The initial value of the top level is the empty string. --->
           <cfif nodeitemid IS "">
                 <cfset nodeitemid =0>
           </cfif>
           <!--- Create an array with one element defining the child node. --->
           <cfset nodeArray = ArrayNew(1)>
           <cfset element1 = StructNew()>
           <cfset element1.value = nodeitemid + 1>
           <cfset element1.display = "Node #nodeitemid#">
           <cfset nodeArray[1] = element1>
           <cfreturn nodeArray>
     </cffunction>
</cfcomponent>



Handling leaf nodes
Code that returns the information for leaf nodes of the trees must always set the LEAFNODE structure field to true.
This prevents the tree from displaying a + expansion indicator in the tree leaf node tree entries and from attempting
to expand the node. The following example shows how you use the LEAFNODE field.


Example 2: a more complex tree with leaf node handling
The following tree uses the cfartgallery database to populate a tree where the top level is the art medium, the second
level is the artist, and the leaf nodes are individual works of art. When the user clicks an artwork, the application shows
the art image.

This example shows how to generate return values that are specific to the level in the tree and the parent value. It also
shows the use of the LEAFNODE return structure element.

In this application, the CFC return structure keys are specified in lowercase letters, and ColdFusion automatically
converts them to uppercase. Notice that the database contains entries only for the painting, sculpture, and
photography categories, so just those top-level tree nodes have child nodes.

The following example shows the main application page:




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                       837
Requesting and Presenting Information




<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<!--- The loadimage function displays the image of the selected art.
         It is called when the user clicks the image item. --->
<script>
    function loadImage(img) {
         var imgURL = '<img src="/cfdocs/images/artgallery/'+img+'">';
         var imgDiv = document.getElementById('image');
               imgDiv.innerHTML = imgURL;
    }
</script>


</head>
<body>


<!--- The form uses a table to place the tree and the image. --->
<cfform name="ex1" action="ex1.cfm" method="post">
    <table>
         <tr valign="top">
               <td>
                    <cftree name="mytree" format="html">
                           <!--- When you use a bind expression, you must have only one
                                     cftreeitem, which populates the tree level. --->
                           <cftreeitem bind="cfc:tree.getItems({cftreeitempath},
                               {cftreeitemvalue})">
                    </cftree>
               </td>
               <td>
                    <div id="image"></div>
               </td>
         </tr>
    </table>
</cfform>
</body>
</html>

The following example shows the tree.cfc file:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                        838
Requesting and Presenting Information




<cfcomponent output="false">


<cfset variables.dsn = "cfartgallery">


<!--- Function to populate the current level of the tree. --->
<cffunction name="getItems" returnType="array" output="false" access="remote">
    <cfargument name="path" type="string" required="false" default="">
    <cfargument name="value" type="string" required="false" default="">
    <cfset var result = arrayNew(1)>
    <cfset var q = "">
    <cfset var s = "">


    <!--- The cfif statements determine the tree level. --->
    <!--- If there is no value argument, the tree is empty. Get the media types. --->
    <cfif arguments.value is "">
         <cfquery name="q" datasource="#variables.dsn#">
         SELECT mediaid, mediatype
         FROM media
         </cfquery>
         <cfloop query="q">
               <cfset s = structNew()>
               <cfset s.value = mediaid>
               <cfset s.display = mediatype>
               <cfset arrayAppend(result, s)>
         </cfloop>


    <!--- If the value argument has one list entry, it is a media type. Get the artists for
               the media type.--->
    <cfelseif listLen(arguments.value) is 1>
         <cfquery name="q" datasource="#variables.dsn#">
         SELECT artists.lastname, artists.firstname, artists.artistid
         FROM art, artists
         WHERE art.mediaid = <cfqueryparam cfsqltype="cf_sql_integer"
                    value="#arguments.value#">
         AND art.artistid = artists.artistid
         GROUP BY artists.artistid, artists.lastname, artists.firstname
         </cfquery>
         <cfloop query="q">
               <cfset s = structNew()>
               <cfset s.value = arguments.value & "," & artistid>
               <cfset s.display = firstName & " " & lastname>
               <cfset arrayAppend(result, s)>
         </cfloop>


    <!--- We only get here when populating an artist's works. --->
    <cfelse>
         <cfquery name="q" datasource="#variables.dsn#">
         SELECT art.artid, art.artname, art.price, art.description,
                    art.largeimage, artists.lastname, artists.firstname
         FROM art, artists
         WHERE art.mediaid = <cfqueryparam cfsqltype="cf_sql_integer"
                    value="#listFirst(arguments.value)#">
         AND art.artistid = artists.artistid
         AND artists.artistid = <cfqueryparam cfsqltype="cf_sql_integer"




                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    839
Requesting and Presenting Information




                    value="#listLast(arguments.value)#">
          </cfquery>
          <cfloop query="q">
               <cfset s = structNew()>
               <cfset s.value = arguments.value & "," & artid>
               <cfset s.display = artname & " (" & dollarFormat(price) & ")">
               <cfset s.href = "javaScript:loadImage('#largeimage#');">
               <cfset s.children=arrayNew(1)>
               <!--- leafnode=true prevents node expansion and further calls to the
                    bind expression. --->
               <cfset s.leafnode=true>
               <cfset arrayAppend(result, s)>
          </cfloop>


     </cfif>


     <cfreturn result>
</cffunction>


</cfcomponent>



Binding other controls to a tree
ColdFusion tags that use bind expressions can bind to the selected node of a tree by using the following formats:

* {[form:]tree.node} retrieves the value of the selected tree node.

* {[form:]tree.path} retrieves the path of the selected tree node. If the completePath attribute value is true, the
   bound path includes the root node.

The bind expression is evaluated each time a select event occurs on an item in the tree. If you specify any other event
in the bind parameter, it is ignored.


Tree JavaScript functions
You can use the following JavaScript functions to manage an HTML tree:


 Function                            Description

 ColdFusion.Tree.getTreeObject       Gets the underlying Yahoo User Interface Library TreeView JavaScript object.

 ColdFusion.Tree.refresh             Manually refreshes a tree.


For more information, see the ColdFusion.Tree.getTreeObject and ColdFusion.Tree.refresh functions in the CFML
Reference.


Using the rich text editor
The ColdFusion rich text editor lets users enter and format rich HTML text by using an icon-driven interface based
on the open source FCKeditor Ajax widget. The editor includes numerous formatting controls, and icons for such
standard operations as searching, printing, and previewing text. Text editor controls are not covered. For detailed
information on the editor icons and controls, see http://wiki.fckeditor.net/UsersGuide.

Note: Do not bind to a rich text area on load of a page, for example, from another control, such as a text box.

The following example shows a simple rich text editor. When a user enters text and clicks the Enter button, the
application refreshes and displays the formatted text above the editor region.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       840
Requesting and Presenting Information




<html xmlns="http://www.w3.org/1999/xhtml">
<head>
</head>


<body>
<!--- Display the text if the form has been submitted with text. --->
<cfif isdefined("form.text01") AND (form.text01 NEQ "")>
     <cfoutput>#form.text01#</cfoutput><br />
</cfif>


<!--- A form with a basic rich text editor and a submit button. --->
<cfform name="form01" >
     <cftextarea richtext=true name="text01" />
     <cfinput type="submit" value="Enter" name="submit01"/>
</cfform>
</body>
</html>

Note: If you use the rich text editor in your pages, you cannot configure your web server to have ColdFusion process files
with the .html or .htm extensions. The default HTML processor must handle pages with these extensions.


Configuring the rich text editor
You can customize the rich text editor in many ways. The cftextarea attributes support some basic customization
techniques. For more detailed information, see the FCKEditor website at http://wiki.fckeditor.net/.


Defining custom toolbars
You can use the following techniques to control the appearance of the toolbar:

* Specify the toolbar name in the toolbar attribute

* Create custom toolbars in the fckconfig.js file.

The editor has a single toolbar consisting of a set of active icons and fields, and separators. The toolbar attribute lets
you select the toolbar configuration. The attribute value specifies the name of a toolbar set, which you define in a
FCKConfig.ToolbarSets entry in the cf_webRoot/CFIDE/scripts/ajax/FCKEditor/fckconfig.js file.

The rich text editor comes configured with two toolbar sets: the Default set, which contains all supported editing
controls, and a minimal Basic set. By default, the editor uses the Default set. To create a custom toolbar named
BasicText with only text-editing controls, create the following entry in the fckconfig.js file, and specify
toolbar="BasicText" in the textarea tag.

FCKConfig.ToolbarSets["BasicText"] = [
          ['Source','DocProps','-','NewPage','Preview'],
          ['Cut','Copy','Paste','PasteText','PasteWord','-','Print','SpellCheck'],
          ['Undo','Redo','-','Find','Replace','-','SelectAll','RemoveFormat'],
          ['Bold','Italic','Underline'],
          ['Outdent','Indent'],
          ['JustifyLeft','JustifyCenter','JustifyRight','JustifyFull'],
          '/',
          ['Style','FontFormat','FontName','FontSize'],
          ['TextColor','BGColor'],
          ['FitWindow','-','About']
     ];

This configuration defines a toolbar with two rows that contain a subset of the full tool set designed to support basic
text editing.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     841
Requesting and Presenting Information




Follow these rules when you define a toolbar:

* Start the definition with FCKConfig.ToolbarSets.

* Specify the toolbar name in double quotation marks and brackets ([""]). Use this name, case correct, in the
    cftextarea tag toolbar attribute.

* Follow the toolbar name with an equal sign (=).

* Place all the toolbar controls inside a set of brackets, and follow the definition with a semicolon (;).

* Group controls in brackets.

* Place each entry in single quotation marks (') and separate the entries with commas (,).

* Use the hyphen (-) character to specify a separator.

* Use a forward slash (/) character to start a new row.

For a complete list of the valid toolbar entries, see the Default configuration in fckconfig.js.


Defining custom styles
You can add custom styles that users choose in the Styles selector and apply to selected text. To create a custom style,
add a Style element to /CFIDE/scripts/ajax/FCKEditor/fckstyles.xml. The Style XML element has the following
format:

* The name attribute specifies the name that appears in the Style selector.

* The element attribute specifies the HTML element that surrounds the text.

* Each Attribute child element defines the name and value of an attribute of the HTML tag.

For example, the following definition creates a style that makes the selected text bold and underlined:

<Style name="Custom Bold And Underline " element="span">
      <Attribute name="style" value="font-weight: bold; text-decoration: underline;"/>
</Style>

If you use a custom XML file, instead of fckstyles.xml, to define your styles, specify the filepath in the stylesXML
attribute.


Defining custom templates
The editor includes a set of basic templates that insert HTML formatting into the textarea control. For example, the
ImageandTitle template places a placeholder for an image on the left of the area, and a title and text to the right of
the image. Then right-click the image area to specify the image source and other properties, and replace the
placeholder title and text.

You create your own templates by creating entries in cf_webRoot/CFIDE/scripts/ajax/FCKEditor/fcktemplates.xml
file. Each template XML entry has the following format:

      <Template title="template title" image="template image">
           <Description>template description</Description>
           <Html>
               <![CDATA[
                     HTML to insert in the text area when the user selects the template.
               ]]>
           </Html>
      </Template>

The template title, image, and description appear in the Templates dialog box that appears when the user clicks the
template icon on the rich text editor toolbar.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        842
Requesting and Presenting Information




The following example template defines a title followed by text:

<Template title="Title and Text" image="template1.gif">
     <Description>A Title followed by text.</Description>
     <Html>
           <![CDATA[
                <h3>Type the title here</h3>
                Type the text here
           ]]>
     </Html>
</Template>

The name "Title and Text" and the template1.gif image appear in the template selection dialog box.

If you use a custom XML file, instead of fcktemplates.xml, to define your templates, specify the file path in the
templatesXML attribute.


Defining custom skins
To create a custom skin that you specify in the skin attribute, create a subdirectory of the
cf_webRoot/CFIDE/scripts/ajax/FCKeditor/editor/skins directory. The name of this subdirectory is the name that you
use to specify the skin in the skin attribute. The custom skin directory must contain an images subdirectory and have
the following files:

* fck_editor.css: Defines the main interface, including the toolbar, its items (like, buttons, panels) and the context menu.

* fck_dialog.css: Defines the basic structure of dialog boxes (standard for all dialogs).

* fck_strip.gif: Defines the Default toolbar buttons and context menu icons. It is a vertical image that contains all
   icons placed one above the other. Each icon must correspond to a 16x16 pixels image. You can add custom images
   to this strip.

* images/toolbar.buttonarrow.gif: Defines the small arrow image used in the toolbar combos and panel buttons.

Place all other images used by the skin (that are specified in the CSS files) in the images subfolder.

The most common way of customizing the skin is to change the fck_editor.css and fck_dialog.css files. For information
on the skin format and contents, see the comments in those files.


Using the datefield input control
The HTML cfinput control with a type value of datefield lets users select dates from a pop-up calendar or enter
the dates directly in the input box. When you use the control, keep in mind the following considerations:

* To correctly display label text next to the control in both Internet Explorer and Firefox, surround the label text in
   a <div style="float:left;"> tag and place three <br> tags between each line.

* Consider specifying an overflow attribute with a value of visible in the cflayoutarea tag so that if the pop-up
   calendar exceeds the layout area boundaries, it appears completely.

* If you use a mask attribute to control the date format, it does not prevent the user from entering dates that do not
   conform to the mask. The mask attribute determines the format for dates that users select in the pop-up calendar.
   Also, if the user types a date in the field and opens the pop-up calendar, the calendar displays the selected date only
   if the entered text follows the mask pattern. If you do not specify a mask attribute, the pop-up only matches the
   default matching pattern.

* If the user types a date with a month name or abbreviation in the control, instead of picking a date from the
   calendar, the selected date appears in the pop-up calendar only if both of the following conditions are true:

    * The month position and name format match the mask pattern.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        843
Requesting and Presenting Information




   * The month name matches, case correct, the month names specified by the monthNames attribute, or, for an mmm
      mask, their three-letter abbreviations.

* If the date mask specifies yy for the years, the pop-up calendar uses dates in the range 1951-2050, so if the user
  enters 3/3/49 in the text field, the calendar displays March 3, 2049.

* If the user enters invalid numbers in a date, the pop-up calendar calculates a valid date that corresponds to the
  invalid input. For example, if the user enters 32/13/2007 for a calendar with a dd/mm/yyyy mask, the pop-up
  calendar displays 01/02/2008.

The following example shows a simple tabbed layout where each tab contains a form with several datefield controls.:

<html>
<head>
</head>


<body>
<cflayout type="tab" tabheight="250px" style="width:400px;">
    <cflayoutarea title="test" overflow="visible">
          <br>
          <cfform name="mycfform1">
               <div style="float:left;">Date 1: </div>
               <cfinput type="datefield" name="mydate1"><br><br><br>
               <div style="float:left;">Date 2: </div>
               <cfinput type="datefield" name="mydate2" value="15/1/2007"><br><br><br>
               <div style="float:left;">Date 3: </div>
               <cfinput type="datefield" name="mydate3" required="yes"><br><br><br>
               <div style="float:left;">Date 4: </div>
               <cfinput type="datefield" name="mydate4" required="no"><br><br><br>
          </cfform>
    </cflayoutarea>
    <cflayoutarea title="Mask" overflow="visible">
          <cfform name="mycfform2">
               <br>
               <div style="float:left;">Date 1: </div>
               <cfinput type="datefield" name="mydate5" mask="dd/mm/yyyy">
                    (dd/mm/yyyy)<br><br><br>
               <div style="float:left;">Date 2: </div>
               <cfinput type="datefield" name="mydate6" mask="mm/dd/yyyy">
                    (mm/dd/yyyy)<br><br><br>
               <div style="float:left;">Date 3: </div>
               <cfinput type="datefield" name="mydate7" mask="d/m/yy">
                    (d/m/yy)<br><br><br>
               <div style="float:left;">Date 4: </div>
               <cfinput type="datefield" name="mydate8" mask="m/d/yy">
                    (m/d/yy)<br><br><br>
          </cfform>
    </cflayoutarea>
</cflayout>


</body>
</html>

Note: In Internet Explorer versions previous to IE 7, this example shows the calendars for the first three fields in a page
behind the following input controls.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         844
Requesting and Presenting Information




Using autosuggest text input fields
When you create a text input (type="text") in HTML, use the autosuggest attribute to specify a static or dynamic
source that provides field completion suggestions as the user types. Use the autosuggestMinLength attribute to
specify the number of characters the user must type before first displaying any suggestions.

Note: To place label text next to a cfinput control that uses an autosuggest attribute and have it display correctly in
both Internet Explorer and Firefox, surround the label text in an HTML div tag with a style="float: left"
attribute. Also if you have multiple controls, and place them on separate lines, follow the input controls with three <br>
tags, as in the following example. Otherwise, the label and control do not lay out properly.

 <div style="float: left"> Name: </div>
<cfinput name="userName" type="text" autosuggest="Andrew, Jane, Robert"> <br><br><br>

The control suggests entries from a static list of values. To use a static suggestion list, specify the list entries in the
autosuggest attribute, and separate the entries by the character specified by the delimiter attribute (by default, a
comma), as the following example shows:

 <cfinput type="text"
      autosuggest="Alabama\Alaska\Arkansas\Arizona\Maryland\Minnesota\Missouri"
      name="city" delimiter="\">

In this example, if you type the character a (in uppercase or lowercase) in the cfinput control, the list of states that
start with A appears in a drop-down list. You navigate to a selection by using the arrow keys, and press Enter to select
the item.

You also have the control suggest values from a dynamically generated suggestion list. To use a dynamic list, specify a
CFC function, JavaScript function, or URL in the autosuggest attribute. Use the autosuggestBindDelay attribute
to specify the minimum time between function invocations as the user types. This limits the number of requests that
are sent to the server. If you use a dynamic list, the input field has an icon to its right that animates while suggestions
are fetched.

When you use a bind expression you must include a {cfautosuggestvalue} bind parameter in the function call or
URL parameters. This parameter binds to the user input in the input control and passes it to the function or page.

A CFC or JavaScript autosuggest function must return the suggestion values as a one-dimensional array or as a
comma-delimited list.

The HTTP response body from a URL must consist only of the array or list of suggestion values in JSON format. In
ColdFusion, you can use the serializeJSON function to convert an array to JSON format. If an array with the
suggestions is named nodeArray, for example, the following line would specify the only output on a CFML page that
is called by using a bind expression with a URL:

 <cfoutput>#serializeJSON(nodeArray)#</cfoutput>

You do not have to limit the returned data to values that match the cfautosuggestvalue contents, because the client-
side code displays only the values that match the user input. In fact, the called function or page does not even have to
use the value of the cfautosuggestvalue parameter that you pass to it. However, use the parameter if the returned
data would otherwise be long.

The following example shows how to use a bind expression to populate autosuggest lists. The Last Name text box
displays an autosuggest list with all last names in the database that match the keys typed in the box. The First Name
text box uses binding to the Last Name text box to display only the first names that correspond to the last name and
the text entered in the box. The database query limits the responses to only include results that match the autosuggest
criteria, so the autosuggest list displays all the returned results, and the suggestions only match if the database entry
has a case-correct match.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   845
Requesting and Presenting Information




To test this example with the cfdocexamples database, type S in the first box and the autosuggest list shows Smith and
Stewart. If you select Smith and enter A or J in the First Name box, you get a name suggestion.

The following example shows the application:

<html xmlns="http://www.w3.org/1999/xhtml">
<head>
</head>
<body>


<cfform>
    Last Name:<br />
    <cfinput type="text" name="lastName"
          autosuggest="cfc:suggestcfc.getLNames({cfautosuggestvalue})"><br />
    <br />
    First Name:<br />
    <cfinput type="text" name="firstName"
          autosuggest="cfc:suggestcfc.getFNames({cfautosuggestvalue},{lastName})">
</cfform>
</body>
</html>

The following example shows the suggestcfc.cfc file:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   846
Requesting and Presenting Information




<cfcomponent>


     <cffunction name="getLNames" access="remote" returntype="array" output="false">
          <cfargument name="suggestvalue" required="true">
          <!--- The function must return suggestions as an array. --->
          <cfset var myarray = ArrayNew(1)>
          <!--- Get all unique last names that match the typed characters. --->
          <cfquery name="getDBNames" datasource="cfdocexamples">
          SELECT DISTINCT LASTNAME FROM Employees
          WHERE LASTNAME LIKE <cfqueryparam value="#suggestvalue#%"
               cfsqltype="cf_sql_varchar">
          </cfquery>
          <!--- Convert the query to an array. --->
          <cfloop query="getDBNames">
               <cfset arrayAppend(myarray, lastname)>
          </cfloop>
          <cfreturn myarray>
     </cffunction>


     <cffunction name="getFNames" access="remote" returntype="array"
               output="false">
          <cfargument name="suggestvalue" required="true">
          <cfargument name="lastName" required="true">
          <cfset var myarray = ArrayNew(1)>
          <cfquery name="getFirstNames" datasource="cfdocexamples">
          <!--- Get the first names that match the last name and the typed characters. --->
          SELECT FIRSTNAME FROM Employees
          WHERE LASTNAME = <cfqueryparam value="#lastName#"
               cfsqltype="cf_sql_varchar">
          AND FIRSTNAME LIKE <cfqueryparam value="#suggestvalue & '%'#"
               cfsqltype="cf_sql_varchar">
          </cfquery>
          <cfloop query="getFirstNames">
               <cfset arrayAppend(myarray, Firstname)>
          </cfloop>
          <cfreturn myarray>
     </cffunction>


     </cfcomponent>


Issue with numeric data
For CFC methods that returns numeric data with a leading zero, for example, zip code 02674, the zero is interpreted
by the bind expression as an octal number and its decimal equivalent (in this case 1468) even if you set
returnformat="string".To resolve this issue, for URL binds or binds routed by way of a JavaScript function (for
example, using cfajaxproxy), you can set returnformat=plain to retain the numeric value.Also, leading zeros are
stripped from the suggestion list for autosuggest controls.


Using the cfslider tag
The cfslider tag places a slider control, for selecting a numeric value from a range, in a ColdFusion form. The slider
can be used within a cfform tag for forms in HTML and applet format. The cfslider is not supported with Flash
forms.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          847
Requesting and Presenting Information




In HTML forms, you can specify maximum, minimum, and increment values, to help you quickly filter complex
results. The available slider controls are: Vertical, with controls that can be adjusted to the top or bottom; Horizontal,
with controls that can be adjusted to the left or right; Tip, that displays the values as data tips; and Snapping, that moves
in incremental values.


Manipulating slider at runtime
The following code illustrates how you can manipulate a slider at runtime:

 <script language="javascript">
     //use Coldfusion AJAX functions
     var sliderChange = function(slider,value)
     {
          //get slider name
          slidername = slider.getId();
          //get slider value
          currValue = ColdFusion.Slider.getValue(slidername);
          //set a new slider value
          newValue = parseInt(currValue+10);
          ColdFusion.Slider.setValue(slidername,newValue);
          //hide slider
          if(confirm("Do you want to hide slider?"))
          {
               ColdFusion.Slider.hide(slidername);
          }
          //show slider
          if(confirm("Do you want to show slider?"))
          {
                     ColdFusion.Slider.show(slidername);
          }
          //disable slider
          if(confirm("Do you disable the slider?"))
          {
               ColdFusion.Slider.disable(slidername);
          }
          //enable slider
          if(confirm("Do you enable the slider?"))
          {
               ColdFusion.Slider.enable(slidername);
          }
     }
     var sliderDrag = function(slider)
     {
          //get slider name
          slidername = slider.getId();
          document.getElementById('currentSliderValue').innerHTML =                         "Current Slider value :
<font color='red'><strong>" + ColdFusion.Slider.getValue(slidername) + "<strong></font>";
     }
</script>
<br>
<cfform name="frm1">




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    848
Requesting and Presenting Information




     <p>
     <span id="currentSliderValue">Current Slider Value: <font
color="red"><strong>50</strong></font></span><br>
     </p>
</cfform>
<p>
<br><b>Volume</b>:
<cfslider
     name="s"
     format="html"
     min=1
     max=100
     value="50"
     tip="yes"
     onChange="sliderChange"
     onDrag = "sliderDrag"
     vertical="no"
     width="200pt"
     >
</p>



Using geographical maps
The cfmap tag lets you embed a geographical map within your ColdFusion page. The following are the supported map
types:

* earth

* terrain

* satellite

* hybrid

* map (default)


Using the marker window
The marker window opens when you click the marker icon in the map. It is used to provide information pertaining to
the locations in the map, for example address or latitude and longitude. The marker window can be populated with
static or dynamic content.


Populating data using static content
To manually populate data in the marker window, specify the value in the markerwindowcontent attribute.


Populating dynamic data using bind expression
To dynamically populate data, use the markerbind attribute with a bind expression that calls a CFC, JavaScript
function, or a URL. The bind expression uses bind parameters to specify dynamic information and the values of any
other form field attributes.

Pass the bind parameters to the bind expression. If you omit any of the parameters in the function call or URL, you get
an error. These parameters send information about the map and its state to the data provider function. The data for
these parameters is provided automatically. You do not set any values manually.

Provide the data as provided in the following code:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                849
Requesting and Presenting Information




<br>
<cfoutput>
<table>
    <tr>
          <td bgcolor='red'><h4><font color='white'>URL Bind Example</font></td>
    </tr>
</table>
Map Name: #cfmapname#<br>
Latitude, Longitude: (#DecimalFormat(cfmaplatitude)#,#DecimalFormat(cfmaplongitude)#)<br>
Address: #cfmapaddress#<br>
</cfoutput>

The following table provides details of the parameters:


Parameter name                 Description

cfmapname                      The name of the map.

cfmaplatitude                  The latitude value for the location, in degrees. This value is set as the center of the map.

cfmaplongitude                 The longitude value for the location, in degrees. This value is set as the center of the map.

cfmapaddress                   The address of the location, which is set as the center of the map.


The format of the returned data depends on how you get the data:


Bind type                      Return value

CFC                            A ColdFusion structure. ColdFusion automatically converts the structure for return to the caller.
                               Alternatively, you can return a JSON representation of the structure.

URL                            A JSON representation of a structure. No other body contents is allowed.

JavaScript                     A JavaScript object.


Use the showmarkerwindow attribute to control the display of the window.

The following example uses a bind expression and a CFC to populate dynamic data using a CFC bind expression. The
CFML page contains the following:

<br>
<cfmap
          centeraddress="Hobart, Tasmania, Australia"
          name="map1"
          type="map"
          tip="Hobart, Tasmania, Australia"
          zoomControl="small3d"
          markerbind="cfc:maps.getMapData({cfmapname}, {cfmaplatitude}, {cfmaplongitude},
{cfmapaddress})"
          showmarkerwindow = true>
    <cfmapitem name="m1" address="Taj Mahal, Agra, India" tip="Taj Mahal, Agra, India">
    <cfmapitem name="m2" latitude="40.46" longitude="117.05" showmarkerwindow=true
tip="Great Wall of China, Bejing">
    <cfmapitem name="m3" address="Stonehenge, England" tip="Stonehenge, England"
showmarkerwindow = false>
</cfmap>

The map.cfc is as follows:




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                               850
Requesting and Presenting Information




<cfcomponent>
    <cffunction name="getMapData" access="remote">
         <cfargument name="cfmapname">
         <cfargument name="cfmaplatitude">
         <cfargument name="cfmaplongitude">
         <cfargument name="cfmapaddress">
         <cfsavecontent variable="markup">
               <br>
               <cfoutput>
               <table>
                    <tr>
                           <td bgcolor='red'><h4><font color='white'>CFC Bind Example</font></td>
                    </tr>
               </table>
               Map Name: #cfmapname#<br>
               Latitude, Longitude:
(#DecimalFormat(cfmaplatitude)#,#DecimalFormat(cfmaplongitude)#)<br>
               Address: #cfmapaddress#<br>
               </cfoutput>
         </cfsavecontent>
         <cfreturn markup>
    </cffunction>
</cfcomponent>

The following example shows how to populate dynamic data using a JavaScript bind expression:

<script language="JavaScript">
    var getMapData = function(cfmapname, cfmaplatitude, cfmaplongitude, cfmapaddress){
         var msg = "";
         msg = msg + "Map Name: " + cfmapname + "<br>";
         msg = msg + "Latitude,longitude: " + "(" + cfmaplatitude + "," + cfmaplongitude + ")"
+ "<br>";
         msg = msg + "Address: " + cfmapaddress + "<br>";
         //alert(msg);
         return "<br><table><tr><td bgcolor='red'><h4><font color='white'>" + "Javascript Bind
Example" + "</font></td></tr></table><hr>" + msg;
    }
</script>
<cfmap
    centeraddress="Hobart, Tasmania, Australia"
    name="map1"
    type="map"
    tip="Hobart, Tasmania, Australia"
    zoomControl="small3d"
    markerbind="javascript:getMapData({cfmapname}, {cfmaplatitude}, {cfmaplongitude},
{cfmapaddress})"
    showmarkerwindow = true>
    <cfmapitem name="m1" address="Taj Mahal, Agra, India" tip="Taj Mahal, Agra, India">
    <cfmapitem name="m2" latitude="40.46" longitude="117.05" showmarkerwindow=true tip="Great
Wall of China, Bejing">
    <cfmapitem name="m3" address="Stonehenge, England" tip="Stonehenge, England"
showmarkerwindow = false>
</cfmap>

The following example shows how to populate dynamic data using a URL bind expression:




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    851
Requesting and Presenting Information




<cfmap
     centeraddress="Hobart, Tasmania, Australia"
     name="map1"
     type="map"
     tip="Hobart, Tasmania, Australia"
     zoomControl="small3d"
markerbind="url:mapdata.cfm?cfmapname={cfmapname}&cfmaplatitude={cfmaplatitude}&cfmaplongitu
de={cfmaplongitude}&cfmapaddress={cfmapaddress}"
     showmarkerwindow = true>
     <cfmapitem name="m1" address="Taj Mahal, Agra, India" tip="Taj Mahal, Agra, India">
     <cfmapitem name="m2" latitude="40.46" longitude="117.05" showmarkerwindow=true tip="Great
Wall of China, Bejing">
     <cfmapitem name="m3" address="Stonehenge, England" tip="Stonehenge, England"
showmarkerwindow = false>
</cfmap>


Specifying Google map key
The Google Maps API key is required to embed Google Maps in your web pages.

The following URL provides details of how to sign up for the Google Maps API key:

http://code.google.com/apis/maps/signup.html

Currently, ColdFusion supports only embedding of Google map. To generate a map, provide a valid Google map API
key, and specify the latitude and longitude of the location, or the address of the location. The Google map API key can
be specified in the following ways:

* Using the cfajaximport tag. You specify the map API key in the params attribute as follows:

   <cfajaximport params="#{googlemapkey='Map API Key'}#"

* Using Application.cfc as follows:

   <cfset this.googlemapkey="Map API Key">

* Using the Settings page in the ColdFusion Administrator. Specify the map API key in the Google Map Key field.
   You can also specify the map API key in runtime.cfc.


Styling markers
You can specify the following:

* Custom marker icon: Specify the path to the icon using the markericon attribute. Ensure that you specify an image
   of appropriate size.

* Marker icon color: Use the markercolor attribute. You can specify a color of your preference only for the default
   icon and not for others.

* Map title: Use the title attribute.


Using the cfprogressbar tag
The cfprogressbar tag has the following characteristics:

* Automatically runs the progress bar for a duration that you specify.

* Dynamically loads data using bind expressions

* Lets styling of the progress bar




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   852
Requesting and Presenting Information




* Uses callback and error handlers that give control to the users after the progress bar completes processing or if it
  encounters any exceptions.

* Lets programmatic control over progress bar using JavaScript APIs.


Progress bar modes
The progress bar supports the following two modes:


Dynamic mode
User specifies the bind expression to provide data for the progress bar to display. The bind attribute specifies a
function that determines the indicator length.

The following CFM code shows how to use a CFC bind expression:

<cfajaxproxy cfc="pbar" jsclassname="pbar">
<head>
    <script>
          var utils = new pbar();
          var count = 0;
          var init = function()
          {
               document.getElementById('cfpbLabel').style.display = 'block';
               ColdFusion.ProgressBar.show('pBar');
               ColdFusion.ProgressBar.start('pBar');
          }
          var hideProgessBar = function()
          {
               document.getElementById('cfpbLabel').style.display = 'none';
               ColdFusion.ProgressBar.hide('pBar');
               utils.resetStatus();
          }
    </script>
</head>
<cfform>
    <div id="cfpbLabel" style="display:none">
          Saving File:
    </div>
    <cfprogressbar
          name="pBar"
          autodisplay=false
          bind="cfc:pbar.getProgessData()"
          onComplete="hideProgessBar"
          width="400">
    <cfset ajaxOnLoad('init')>
</cfform>

The following pb.cfc has the function that returns data for the progressbar:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                        853
Requesting and Presenting Information




<cfcomponent>
    <cffunction name="resetStatus" access="remote">
         <!---
         Clear count from session so that next time the progress bar runs from the start
time.
         --->
         <cfif session.count gte 10>
               <cfset structdelete(session,"count")>
         </cfif>
    </cffunction>
    <cffunction name="getProgessData" access="remote">
         <!--- use a count to track progress --->
         <cfif not isdefined('session.count')>
               <cfset session.count = 1>
         <cfelse>
               <cfset session.count = session.count + 1 >
         </cfif>
         <!--- struct with status and message components of the progressbar --->
         <cfset data = {status=session.count * 0.1,message=(session.count * 10)  & "%"}>
         <cfreturn data>
    </cffunction>
</cfcomponent>

The following CFM code explains how to use the URL bind expression:

<head>
    <script>
         var init = function()
         {
               document.getElementById('cfpbLabel').style.display = 'block';
               ColdFusion.ProgressBar.show('pBar');
               ColdFusion.ProgressBar.start('pBar');
         }
         var hideProgessBar = function()
         {
               document.getElementById('cfpbLabel').style.display = 'none';
               ColdFusion.ProgressBar.hide('pBar');
         }
    </script>
</head>
<cfform>
    <div id="cfpbLabel" style="display:none">
         Saving File:
    </div>
    <cfprogressbar
         name="pBar"
         autodisplay=false
         bind="url:progressdata.cfm"
         onComplete="hideProgessBar"
         width="400">
    <cfset ajaxOnLoad('init')>
</cfform>

The following is the Progressdata.cfm:




                                         

DEVELOPING COLDFUSION 9 APPLICATIONS                                                          854
Requesting and Presenting Information




<!--- use a count to indicate progress --->
<cfif not isdefined('session.count')>
    <cfset session.count = 1>
<cfelse>
    <cfset session.count = session.count + 1 >
</cfif>
<!--- the struct to be sent back; using the populate the status and message components of the
progressbar --->
<cfset data = {status=session.count * 0.1,message=(session.count * 10)     & "%"}>
<!--- clear count from session to start afresh the next time the program is run --->
<cfif session.count eq 10>
    <cfset structdelete(session,"count")>
</cfif>
<!--- data sent back via URL binds must use SerializeJSON() --->
<cfoutput>#SerializeJSON(data)#</cfoutput>

The following CFM code has the JavaScript bind expression:

<head>
    <script>
         var count = 0;
         var init = function()
         {
               document.getElementById('cfpbLabel').style.display = 'block';
               ColdFusion.ProgressBar.show('pBar');
               ColdFusion.ProgressBar.start('pBar');
         }
         var hideProgessBar = function()
         {
               document.getElementById('cfpbLabel').style.display = 'none';
               ColdFusion.ProgressBar.hide('pBar');
         }
         var getProgessData = function()
         {
               count++;
               if(count > 10)
                    return {STATUS:1,MESSAGE:"Done"}
               else
                    return {STATUS:count*0.1,MESSAGE:(count * 10) + "%"}
         }
    </script>
</head>
<cfform>
    <div id="cfpbLabel" style="display:none">
         Saving File:
    </div>
    <cfprogressbar
         name="pBar"
         autodisplay=false
         bind="javascript:getProgessData()"
         onComplete="hideProgessBar"
         width="400"
         >
    <cfset ajaxOnLoad('init')>
</cfform>




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    855
Requesting and Presenting Information




Manual mode
In the manual mode, you specify the duration the progress bar takes to complete the display of progress.

In the following example, autodisplay is set to false as a result of which the progress bar is not shown when the
page is first loaded. When the page is loaded, init function is invoked and the function displays and runs the progress
bar. The default interval used in this mode is one second.

<head>
     <script>
           var init = function()
           {
                 document.getElementById('cfpbLabel').style.display = 'block';
                 ColdFusion.ProgressBar.show('pBar');
                 ColdFusion.ProgressBar.start('pBar');
           }
           var hideProgessBar = function(){
                 document.getElementById('cfpbLabel').style.display = 'none';
                 ColdFusion.ProgressBar.hide('pBar');
           }
     </script>
</head>
<cfform>
     <div id="cfpbLabel" style="display:none">
           Saving File:
     </div>
     <cfprogressbar
           name="pBar"
           duration="10000"
           autodisplay=false
           onComplete="hideProgessBar"
           width="400"
     />
     <cfset ajaxOnLoad('init')>
</cfform>


Working with a progress bar at runtime
This section illustrates how to use the JavaScript API to update the progress bar status. The following CFM code loads
a progress bar using the JavaScript API ColdFusion.ProgressBar.updatestatus.

On program load, intit function displays the progress bar and calls the getProgressData JavaScript function to
manually update the progress bar. The getProgressData function assigns the status and message variables passed to the
JavaScript API update status.

While working with a progress bar at runtime, ensure that you specify a dummy duration (for instance,
duration=5000). Even though the custom JavaScript function decides the actual duration, duration is a mandatory
attribute.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                            856
Requesting and Presenting Information




<cfajaxproxy cfc="pbar" jsclassname="pbar">
<head>
    <script>
         var utils = new pbar();
         var init = function()
         {
               document.getElementById('cfpbLabel').style.display = 'block';
               ColdFusion.ProgressBar.show('pBar');
               getProgessData();
         }
         var hideProgessBar = function()
         {
               document.getElementById('cfpbLabel').style.display = 'none';
               ColdFusion.ProgressBar.hide('pBar');
         }
         var getProgessData = function()
         {
               for(i=1;i <= 10;i++)
               {
                    var status = parseFloat(i * 0.10);
                    var message = Math.round(status * 100) + "%";
                    ColdFusion.ProgressBar.updateStatus('pBar',status,message);
                    utils.sleep(1000);
               }
               hideProgessBar();
         }
    </script>
</head>
<cfform>
    <div id="cfpbLabel" style="display:none">
         Saving File:
    </div>
    <cfprogressbar
         name="pBar"
         autodisplay=false
         duration=15000
         onComplete="hideProgessBar"
         width="400">
    <cfset ajaxOnLoad('init')>
</cfform>

The sleep function in the following CFC provides sleep functionality in the JavaScript code:

<cfcomponent>
<cffunction name="sleep" access="remote">
    <cfargument name="timetosleep" default="1000">
    <cfset sleep(timetosleep)>
</cffunction>
</cfcomponent>


Styling the progress bar
The cfprogressbar has style attribute that lets you decide:

* Background color of the progress bar

* Color of the progress message

* Color of the progress indicator



                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  857
Requesting and Presenting Information




The following code illustrates styling: style="bgcolor:ADD8E6;progresscolor:6183A6;textcolor:191970"


Using the cfmessagebox tag
The cfmessagebox tag lets you define controls for displaying pop-up messages. In addition to the standard alert box,
this tag lets you include a prompt and entry field in the box.


Using confirm dialog
The following code shows how to build a confirm message box with two buttons YES and NO.

<cfmessagebox
     type="confirm"
     name="msgbox1"
     title="Confirm Dialog"
     message="Do you want proceed?"
     buttonType="YesNo"
     icon="info"
     labelYes="Click Yes to continue"
     labelNo="No"
     x=100
     y=200>
<!--- This example illustrates usage of the Confirm dialog in "YesNoCancel" mode --->
<cfmessagebox
     type="confirm"
     name="msgbox2"
     title="Save File"
     message="Do you want to save the file?"
     buttonType="YesNoCancel"
     icon="question"
     labelYes="Click Yes to save the file"
     labelNo="No"
     labelCancel="Quit"
     width="400"
     x=500
     y=200>
<br><br>
<input
     type="button"
     name="confirm1"
     onClick="javascript:ColdFusion.MessageBox.show('msgbox1');"
     value="YesNo Confirm"
     >
<input
     type="button"
     name="confirm2"
     onClick="javascript:ColdFusion.MessageBox.show('msgbox2');"
     value="YesNoCancel Confirm"
     >


Styling a message box
The bodyStyle attribute, a CSS style specification for the body of the message box helps you to style the message. As
a general rule, use this attribute to set color and font styles.

The following example illustrates the usage of the bodyStyle attribute:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                858
Requesting and Presenting Information




<cfmessagebox
     type="alert"
     name="msgbox1"
     title="Download Status"
     message="File Download Complete"
     icon="info"
     width="400"
     bodyStyle="background-color:white;color:blue"
     x=300
     y=200>
<br><br>
<input
     type="button"
     name="alert"
     onClick="javascript:ColdFusion.MessageBox.show('msgbox1');"
     value="Alert MessageBox"
     >




Using Ajax Data and Development Features

Adobe ColdFusion supports Ajax features to use data dynamically in web pages.

For information on ColdFusion Ajax user interface capabilities, see "Using Ajax User Interface Components and
Features" on page 804.


About ColdFusion Ajax data and development features
Ajax (Asynchronous JavaScript and XML) is a set of web technologies for creating interactive web applications. Ajax
applications typically combine:

* HTML and CSS for formatting and displaying information.

* JavaScript for client-side dynamic scripting

* Asynchronous communication with a server using the XMLHttpRequest function.

* XML or JSON (JavaScript Object Notation) as a technique for serializing and transferring data between the sever
   and the client.

ColdFusion provides many tools that simplify using Ajax technologies for dynamic applications. By using ColdFusion
tags and functions, you can easily create complex Ajax applications.


ColdFusion Ajax features
ColdFusion provides data management and development, and user interface Ajax features.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      859
Requesting and Presenting Information




Data and development features
ColdFusion data and development features help you develop effective Ajax applications that use ColdFusion to
provide dynamic data. They include many features that you can use with other Ajax frameworks, including Spry.

* ColdFusion supports data binding in many tags. Binding allows an application that uses form and display tags, such
  as cfselect and cfwindow, to dynamically display information based on form input. In the simplest application,
  you display form data directly in other form fields, but usually you pass form field data as parameters to CFC or
  JavaScript functions or URLs and use the results to control the display. Data binding uses events to automatically
  update the display, typically when the bound input data changes. You can also use the
  ColdFusion.Ajax.submitForm JavaScript function to get the current value of any bindable element.

* The cfajaxproxy tag creates a JavaScript proxy that represents a CFC on the server. It manages the
  communication between the client and server, and provides several functions to simplify and manage handling the
  communication and its results. This tag provides access to all remote functions in a CFC. It also lets applications,
  including applications that use Ajax frameworks or widget sets such as Dojo or Backbase, easily access data from
  ColdFusion servers.

* The cfsprydataset tag lets you use bind expressions to dynamically create and update Adobe Spry data sets.
  Applications that use Spry framework elements, such as dynamic regions, use this tag to populate the Spry elements
  with information based on ColdFusion control input. This feature lets you easily intermix Spry and ColdFusion
  controls.

* The cfajaximport tag specifies the location of the JavaScript and CSS files that a ColdFusion page imports. You
  also use this tag to selectively import files required by specific Ajax-based tags and functions. The ability to change
  the file location lets you support a wide range of configurations and use advanced techniques, such as application-
  specific styles. Although ColdFusion automatically determines and imports the required files, sometimes you must
  manually specify the information.

* ColdFusion provides several CFML functions that let you create and consume JSON format data on the server and
  let you prepare data for use in HTML cfgrid tags.

* You display a floating logging window that shows client-side logging and debugging information. ColdFusion Ajax
  features display information and error messages in this window, and several logging tags let you display additional
  information, including the structure of complex JavaScript variables.


User interface features
* Ajax-based HTML controls including the following:

   * Tree

   * Grid

   * Rich text editor

   * Date field

   * Autosuggest text input

* Pop-up menus and menu bars.

* Container tags that provide bordered, box, and tabbed layouts, pop-up windows, and pod regions.

* A cfdiv container tag that enables asynchronous form submission and binding in HTML div and other regions.

* Tool tips for specific controls and HTML regions.

For detailed information on using the user interface features, see "Using Ajax User Interface Components and
Features" on page 804.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          860
Requesting and Presenting Information




ColdFusion Ajax tags
The following table lists ColdFusion Ajax-related tags and functions, including all tags that support Ajax-based
features. It does not include subtags that are used only in the bodies of the listed tags:


 Data tags                      UI tags                       UI tags                         Functions

 cfajaximport                   cfdiv                         cfselect                        AjaxLink

 cfajaxproxy                    cfgrid                        cftextarea                      AjaxOnLoad

 cfsprydataset                  cfinput                       cftree                          DeserializeJSON

                                cflayout                      cftooltip                       IsJSON

                                cfmenu                        cfwindow                        QueryConvertForGrid

                                cfpod                                                         SerializeJSON



Binding data to form fields
Many ColdFusion Ajax features use binding to provide dynamic interactivity based on user input or changing data.
When you use binding, a bind expression gets evaluated, and the display gets updated based on new data each time a
specific event (onChange by default) occurs on a form control field specified by a bind parameter. This way, the value
of the tag that specifies the bind expression, and the display, get updated dynamically based on changing information,
including user-entered form data. When you use binding the page contents updates, but the entire page is not
refreshed.

Note: When a bound window is not visible, or a tab is not selected, its contents is not updated when the controls it is bound
to change. When the tab or window is made visible, it is updated only if events have been received from the bound controls
while the control was not visible.

Depending on the specific ColdFusion tag, a bind expression uses bind parameter values directly or passes bind
parameter values as parameters to a CFC function, a JavaScript function, or an HTTP request and uses the function or
request response to update the page. Use the following as the data source for a bind expression:

* ColdFusion form control attributes and values. You can bind to the following controls:

    * cfgrid

    * cfinput with checkbox, datefield, file, hidden, radio, or text types

    * cfselect

    * cftextarea

    * cftree

* Spry data set elements

Note: You cannot use a bind expression to bind to controls in a dynamically loaded region. For example, you cannot bind
from a control on one page to a control in a layout area on that page if the cflayoutarea tag uses a source attribute
for its contents. However, a dynamically loaded region binds to controls on the page that loads it, so the file specified by
the source attribute uses bind expressions that specify controls on the page that contains the cflayoutarea tag.

The results of the bind expression determine the value of the tag that uses the expression. For example, if you specify
a URL in a bind expression as the source attribute of a cfwindow control, the page specified by the URL must return
the full contents of the window.

For more examples, see "Using Ajax User Interface Components and Features" on page 804 and the reference pages
for controls that support binding.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    861
Requesting and Presenting Information




Using bind expressions
To specify a bind expression, use one of the following formats:

* cfc:componentPath.functionName(parameters)

   Note: In ColdFusion 9, the component path cannot use a mapping. The componentPath value must be a dot-
   delimited path from the web root or the directory that contains the current page.

* javascript:functionName(parameters)

* url:URL?parameters

* URL?parameters

* A string containing one or more instances of {bind parameter}, such as {firstname}.{lastname}@{domain}

In formats 1-4 the parameters normally include one or more bind parameters. The following table lists the tag
attributes that support bind expressions and the formats each use:


 Attribute              Tags                                                                     Supported formats

 autosuggest            cfinput type="text"                                                      1, 2, 3

 bind                   cfdiv, cfinput, cftextarea                                               1, 2, 3, 5

 bind                   cfajaxproxy, cfgrid, cfselect, cfsprydataset, cftreeitem                 1, 2, 3

 onChange               cfgrid                                                                   1, 2, 3

 source                 cflayoutarea, cfpod, cfwindow                                            4


The following examples show some of these uses:

bind="cfc:myapp.bookorder.getChoices({book})"
source="/myApp/innerSource/cityWindow.cfm?cityname={inputForm:city}

In these examples, {book} and {inputForm:city} specify bind parameters that dynamically get data from the book
and city controls, and the city control is in the inputForm form.

If a bind attribute specifies a page that defines JavaScript functions, the function definitions on that page must have
the following format:

functionName = function(arguments) {function body}

Function definitions that use the following format may not work:

function functionName (arguments) {function body}

However, Adobe recommends that you include all custom JavaScript in external JavaScript files and import them on
the application's main page, and not write them inline in code that you get using the source attribute. Imported pages
do not have this function definition format restriction.


Specifying bind parameters
A bind parameter specifies a form control value or other attribute, as in the following example:

bind="cfc:myapplication.bookSearch.getStores({form1:bookTitle})"

In this example, the bind parameter is form1:bookTitle and specifies the value attribute of the bookTitle field of the
form1 form.

Bind parameters have either of the following formats:




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        862
Requesting and Presenting Information




 {[formName:]controlName[.attributeName][@event]}
{SpryDataSetName.fieldName}

The brackets ([]) indicate optional contents and are not part of the parameter.

Note: To include a literal brace character in a bind expression, escape the character with a backslash, as \{, \}.


The formname value
The formname entry identifies the form that contains the control you are binding to. Specify a form name if multiple
forms contain bind targets with the same names. To specify the form name, start the bind expression with the form's
id attribute the name attribute if you did not specify an id attribute, and follow it with a colon (:). To specify the book
control that is in a form named inputForm, for example, use the following format:

 bind="cfc:myapp.bookorder.getChoices({inputForm:book})"


The controlName value
To bind to a form field, the controlName value must be the value of the id or name attribute of the form control to
which you are binding. If a control has both an id and a name attribute, use either value.

You can bind to any ColdFusion form control, including cfgrid and cftree. You cannot bind to values in other
ColdFusion tags, such as cftable.

To bind to a Spry data set, specify the data set name in this part of the bind parameter.

You can bind to multiple radio buttons or check boxes by giving them the same name value. If all the radio buttons in
a radio button group have the same name value, the bind parameter represents the selected button. If multiple check
boxes have the same name value, the bind parameter represents the values of the selected controls in a comma-
delimited list. If you also specify a unique id attribute for each check box or radio button, specify an HTML label tag
for each button or check box and use the id value in the for attribute; in this case, users select items by clicking the
label, not just the button or box.

If a cfselect control supports multiple selections, the bind expression returns the information about the selected
items in a comma-delimited list.

You can bind only to controls that are available in the DOM tree when the bind is registered. Binds are registered when
the page with the bind expression loads, either in the browser window or in a container tag. As a result, if you have two
cfdiv, cflayoutarea, cfpod, or cfwindow containers that you load by using a source (or for cfdiv tag, bind)
attribute, you cannot bind controls in one container to controls in the other, because one container cannot be assured
that the other is loaded when it loads. Similarly, elements on the main page cannot bind to elements on a dynamically
loaded container. To prevent this problem, define the bind target in line on the main page, instead of using a source
or bind attribute to retrieve the markup that contains the bind target. In other words, the "master" form with fields
that serve as sources of bind expressions is loaded statically (on the main page), and the "child" controls that depend
on the data are loaded dynamically, on a page that is specified in a source or bind attribute.


The attributeName value
When you bind to a form control, by default, the bind expression represents the value attribute of the specified
control. If the bind target is a cfselect tag, the bind expression represents a comma delimited list of the values of the
selected items.

To bind to a different attribute, follow the control name or id with a period (.) and the attribute name. To pass the
checked attribute of a checkbox cfinput tag as a CFC parameter, for example, use an expression such as the
following:

 bind="cfc:myapp.bookorder.useStatus({myForm:approved.checked"@click}"




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        863
Requesting and Presenting Information




Note: You can bind to the display text of a select box, instead of the value, by specifying an attribute name of innerHTML.

Note: When you bind to a check box, use the @click event specifier to ensure that the bind expression is triggered in
Internet Explorer when the user selects or deselects the check box, not when the box loses focus.

Grids and trees do not have default bind attributes.

* Always specify a grid target attribute by using the format {gridID.columnName}. The bind expression gets the value
    of the specified column in the selected row.

* For trees, you must bind to a specific node in the tree. Specify the node by using the node ID or an explicit path to
    the node.

To bind to a Spry data set element or attribute, use standard Spry path notation. For example, specify an element name.


The event value
By default, the bind expression function executes each time the control specified in the bind parameter has an
onChange event. To trigger updates on a different JavaScript event, end the bind expression with an at sign (@) and
the event name, without the "on" prefix. The following code, for example, executes the getChoices CFC each time the
user presses the mouse button while the pointer is over the book control:

 bind="cfc:myapp.bookorder.getChoices({inputForm:book@mousedown})"

Note: To bind to a cfinput control with type attribute of button, specify a bind event setting, such as click. The
change event is the default event has no effect.

When you bind to a Spry data set, do not specify an event. The expression is evaluated when the selected row changes
in the data set, or when the data set reloads with new data.

You can also specify that a specific bind parameter never triggers bind expression reevaluation, by specifying @none as
the event. This is useful, for example, if a bind expression uses multiple bind parameters binding to different form
fields, and you want the bind expression to trigger changes only when one of the fields changes, not when the others
change. In this case, you would specify @none for the remaining fields, so events from those fields would not trigger
the bind. The following code shows this use:

 bind="cfc:books.getinfo({iForm:book}, {iForm:author@none})"

The @none event specifier is also useful when used with autosuggest text inputs, trees and grids, as follows:

* When you use an autosuggest text input, the bind expression is evaluated as a user types in text, and picks up data
    from all bind parameters, including those parameters with @none specified. Therefore, for autosuggest, specify
    @none for all bind parameters, because there is no way for it to react to changes in the parameters.

* When you call the ColdFusion.Grid.refresh or ColdFusion.Tree.refresh function, the function fetches data
    from all bind parameters when it evaluates the bind expression, including any parameters with @none specified. If
    you specify @none for all bind parameters, the tree or grid might not respond to changes in other controls, but gets
    data from all the bind parameters each time you explicitly refresh it.


Using CFC functions in bind expressions
As with JavaScript functions, you pass arguments to a CFC function specified in a bind expression positionally. When
you do this, the argument names in a CFC function definition do not have to be the same as the bind parameter names,
but the arguments in the bind expression must be in the same order as the arguments in the CFC function definition.

Alternatively, you pass named CFC function arguments. Then, the bind expression and CFC function must use the
same names for the arguments, and the function does not have to define the arguments in the same order as they are
passed. To specify argument names in a bind expression, use a format such as the following, which uses two named
parameters, arg1 and arg2:



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        864
Requesting and Presenting Information




bind="cfc:mycfc.myfunction(arg1={myform:myfield1},arg2={myform:myfield2})"


Support for CFCs outside webroot
Note: To use this feature, you must install ColdFusion 9 Update 1.

Components outside the webroot can be accessed in bind expressions. This implies that tags such as cfajaxproxy or
Ajax components such as grid, map, or progress bar can be used in more effective ways.

Note: In the previous releases, the CFCs had to be web-accessible for Ajax applications to function.

In addition to accessing CFCs using relative or absolute path, you can also use any of the following methods to access CFCs:

* logical mappings (defined in the ColdFusion Administrator)

* per-app mappings (defined in Application.cfc)

* imports (using cfimport/import)


Usage
The following code shows the usage of this enhancement using per-map mappings:

Application.cfc

THIS.mappings["/mycfc"] = "C:\www\shared\components";

Test.cfm

<cfajaxproxy cfc="mycfc.utils" jsclassname='jsobjname' />


Example
In this example, a per-app mapping named mycfcs has been created in Application.cfc pointing to "c:\components".
For the sample code to work, create a folder named components in your system root (in this example, c:\) and copy
the Employee.cfc to that folder.

Application.cfc

<cfcomponent>
     <cfset this.name = "cfcoutsidewebroot">
     <cfset this.sessionmanagement = true>
     <Cfset mappingname = "/mycfcs">
     <Cfset mappingpath = "c:\components\">
     <cfset this.mappings[mappingname] = mappingpath>
</cfcomponent>

Employee.cfc




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       865
Requesting and Presenting Information




<cfcomponent>
     <cfscript>
          remote any function
getEmployees(page,pageSize,gridsortcolumn="EMP_ID",gridsortdirection="ASC"){
               var startRow = (page-1)*pageSize;
               var endRow = page*pageSize;


               if(!isdefined("arguments.gridsortcolumn") or
isdefined("arguments.gridsortcolumn") and trim(arguments.gridsortcolumn) eq "")
                    gridsortcolumn = "EMP_ID";
               if(!isdefined("arguments.gridsortdirection") or
isdefined("arguments.gridsortdirection") and arguments.gridsortdirection eq "")
                    gridsortdirection = "ASC";
               var mysql = "SELECT Emp_ID, FirstName,             EMail, Department FROM Employees";
               if(isdefined("arguments.gridsortcolumn") and arguments.gridsortcolumn neq "")
                    mysql = mysql & " ORDER BY " & gridsortcolumn;
               if(isdefined("arguments.gridsortdirection") and arguments.gridsortdirection neq
"")
                    mysql = mysql & " " & gridsortdirection ;
               rs1 = new query(name="team", datasource="cfdocexamples", sql=mysql).execute();
                    return QueryConvertForGrid(rs1.getResult(), page, pageSize);
          }



          remote any function editEmployees(gridaction,gridrow,gridchanged){
                    writelog("edit employee info");
          }


     </cfscript>
</cfcomponent>

Employee.cfm

<cfform>
     <cfgrid
               format="html"
               name="grid01"
               pagesize=10
               title="Employee database"


bind="cfc:mycfcs.employee.getEmployees({cfgridpage},{cfgridpagesize},{cfgridsortcolumn},{cfg
ridsortdirection})"
               onChange="cfc:mycfcs.
employee.editEmployees({cfgridaction},{cfgridrow},{cfgridchanged})">
               <cfgridcolumn name="Emp_ID" display=false header="ID" />
               <cfgridcolumn name="FirstName" display=true header="First Name"/>
               <cfgridcolumn name="Email" display=true header="Email"/>
               <cfgridcolumn name="Department" display=true header="Department" />
     </cfgrid>
</cfform>


Using binding in control attributes
When you use direct binding you specify a bind expression in a ColdFusion form or display control attribute. In the
simplest, form of binding you use form fields, such as a name field, to fill other fields, such as an e-mail field, as the
following example. shows. When you enter a name or domain and tab to click in another field, the name is added to
the e-mail field.



                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        866
Requesting and Presenting Information




<html>
<head>
</head>
<body>


<cfform name="mycfform">
     First Name: <cfinput type="text" name="firstname" value=""><br>
     Last Name: <cfinput type="text" name="lastname" value=""><br>
     Domain: <cfinput type="text" name="domain" value=""><br>
     E-mail: <cfinput type="text" name="email1" size="30"
           bind="{firstname}.{lastname}@{domain}">
</cfform>
</body>
</html>

The following example shows the results of binding to radio buttons and check boxes with the same name attribute but
different id attributes. Notice that because each control has a separate id value that is used in the label tags, you click
the labels to select and deselect the controls.

<html>
<head>
</head>
<body>


<cfform name="myform">
     Pick one:
     <cfinput id="pickers1" name="pickone" type="radio" value="Apples">
           <label for="pickers1">Apples</label>
     <cfinput id="pickers2" name="pickone" type="radio" value="Oranges">
           <label for="pickers2">Oranges</label>
     <cfinput id="pickers3" name="pickone" type="radio" value="Mangoes">
           <label for="pickers3">Mangoes</label>
     <br>
     <cfinput name="pickone-selected" bind="{pickone}"><br />
     <br />


     Pick as many as you like:
     <cfinput id="pickers4" name="pickmany" type="checkbox" value="Apples">
           <label for="pickers4">Apples</label>
     <cfinput id="pickers5" name="pickmany" type="checkbox" value="Oranges">
           <label for="pickers5">Oranges</label>
     <cfinput id="pickers6" name="pickmany" type="checkbox" value="Mangoes">
           <label for="pickers6">Mangoes</label>
     <br/>
     <cfinput name="pickmany-selected" bind="{pickmany}"><br />


</cfform>
</body>
</html>

Most applications call a CFC function, or JavaScript function, or use a URL to make an HTTP request (typically to a
CFML page), and pass bind parameters as the function or URL parameters.

The following example uses the same form as the first example in the preceding section, but uses a different bind
expression with the following features:

* It uses the keyup events of the name and domain fields to trigger binding. So the e-mail field gets updated each time
   that you enter a letter in any of these fields.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     867
Requesting and Presenting Information




* It calls a CFC, which uses only the first letter of the first name when forming the e-mail address, and forces the
   domain name to be all lowercase.

The following example shows the bindapp.cfm page:

 <html>
<head>
</head>
<body>
<cfform name="mycfform">
     First Name: <cfinput type="text" name="firstname" value=""><br>
     Last Name: <cfinput type="text" name="lastname" value=""><br>
     Domain: <cfinput type="text" name="domain" value=""><br>
     E-mail: <cfinput type="text" name="email"
           bind="cfc:bindFcns.getEmailId({firstname@keyup},{lastname@keyup},
           {domain@keyup})">
</cfform>
</body>
</html>

The following example shows the bindFcns.cfc CFC file:

 <cfcomponent>
     <cffunction name="getEmailId" access="remote">
           <cfargument name="firstname">
           <cfargument name="lastname">
           <cfargument name="domain">
           <cfreturn
"#left(arguments.firstname,1)#.#arguments.lastname#@#lcase(arguments.domain)#">
     </cffunction>
</cfcomponent>

Many of the examples in the documentation for ColdFusion Ajax features use binding, including more complex forms
of binding.


Using the cfajaxproxy tag to bind to display controls
The cfajaxproxy tag with a bind attribute makes any of the following elements dependent on one or more bound
ColdFusion Ajax controls:

* A single CFC function

* A single JavaScript function

* An HTTP request; for example, the URL of a CFML page

The function or request executes whenever a specific event (by default, the onChange event) of the bound control
occurs.

Note: if you specify a bind attribute with a URL, the HTTP request includes a _CF_NODEBUG URL parameter. ColdFusion
checks this value, and when it is true, does not append to the response any debugging information that it normally would
send. This behavior ensures that JSON responses to Ajax requests do not include any non-JSON (that is, debugging
information) text.

The cfajaxproxy tag includes the following attributes that determine how the proxy handles the data returned by the
function or the page:

* The onError function specifies code to handle an HTTP error return. You use this attribute with a URL or CFC bind.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    868
Requesting and Presenting Information




* The onSuccess function handles a valid return from the function or page and updates the display as required with
   the returned information.

Binding a function or request by using the cfajaxproxy tag enables you to perform a server-side action, such as
updating a database by using bind parameter values based on a user action in some control, and then run a specific
action or set of actions in one or more controls based on the server response. Because it uses an onSuccess function
to process the return from the server, this form of binding provides substantially more flexibility than a CFML control
bind parameter. This format also lets you use a control bind parameter for one kind of action, and the cfajaxproxy
tag for a different activity.

For example, if you have a form with an editable cfgrid control and a delete button that a user clicks to delete a grid
row. The application must have the following behaviors:

* When the user clicks the delete button two things must happen:

   * The application must call a mycfc.deleteButton CFC function to delete the row from the database.

   * The grid must update to remove the deleted row.

* When the user edits the grid content, the grid must call a mycfc.update function to update the database.

Implement these behaviors by doing the following:

* In the cfgrid tag, specify a bind attribute that uses a bind expression to call a mycfc.update function each time the
   user changes the grid contents.

* In a cfajaxproxy tag, specify a bind attribute that calls the mycfc.deleterow CFC function, and specify an
   onSuccess attribute that calls the ColdFusion.Grid.refresh function to update the displayed grid when the
   CFC function returns successfully.

The following code snippets show how you could do this:

<cfajaxproxybind="cfc:mycfc.deleteRow({deletebutton@click},
          {mygrid.id@none}"onSuccess="ColdFusion.Grid.refresh('mygrid', true)">


...


<cfinput type="button" name="deletebutton">
<cfgrid name="mygrid" bind="mycfc.update({cfgridpage}, {cfgridpagesize},
          {cfgridsortcolumn}, {cfgridsortdirection})>

The following complete example shows a simple use of the bind attribute in a cfajaxproxy tag. For the sake of brevity,
the bind expression calls a JavaScript function; as a result, the cfajaxproxy tag cannot use a onError attribute.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 869
Requesting and Presenting Information




<html>
<head>
<script language="javascript">
     function test(x,y){
          return "Hello, " + x + "!";
     }
     function callbackHandler(result){
          alert("Bind expression evaluated. Result: \n" + result);
     }
</script>


<cfajaxproxy bind="javascript:test({input1@none},{button1@click})"
          onSuccess="callbackHandler">
</head>


<body>
<cfform name="mycfform">
          <cfinput type="text" value="" name="input1" size="30">
          <cfinput type="button" name="button1" value="Submit">
</cfform>
</body>
</html>


Getting bindable attribute values in JavaScript
Use the ColdFusion.Ajax.submitForm function in your JavaScript code to get the current value of any attribute of
a bindable control. This technique is useful for getting values for complex controls such as cfgrid and cftree. For
more information, see the ColdFusion.Ajax.submitForm function in the CFML Reference.


Managing the client-server interaction
Manage the client-server interaction in several ways:

* Use the cfajaxproxy tag to create a client-side JavaScript proxy for a CFC and its functions. You then call the
   proxy functions in client JavaScript code to access the server-side CFC functions.

* Use the cfsprydataset tag to dynamically populate a Spry data set from a URL or a CFC. You then use the data
   set to populate Spry dynamic regions. You also use Spry data sets in bind expressions.

* Use the cfajaxproxy tag to bind fields of ColdFusion Ajax form controls as parameters to a specific CFC function,
   JavaScript function, or HTTP request, and specify JavaScript functions to handle successful or error results. The
   function is run each time the event determined by the bind expression occurs.

* Use ColdFusion Ajax-based UI tags, such as cftree or cfgrid that automatically get data from CFCs or URLs by
   using data binding.

For Information on working with Spry, including how to use the cfsprydataset tag, see "Using Spry with
ColdFusion" on page 874. For detailed information on using binding, including how to use binding with ColdFusion
UI tags and the cfajaxproxy tag, see "Binding data to form fields" on page 860. For more information on using the
ColdFusion Ajax-based UI tags, see "Using Ajax User Interface Components and Features" on page 804.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     870
Requesting and Presenting Information




Using ColdFusion Ajax CFC proxies
Use the cfajaxproxy tag to create a client-side JavaScript proxy for a CFC and its functions. The proxy object has the
following characteristics:

* It provides a JavaScript function that corresponds to each CFC remote function. Calling these functions in your
   client-side JavaScript code remotely calls the CFC functions on the server.

* It provides JavaScript support functions for controlling the communication, which specifies asynchronous result
   and error handler callbacks, and sends form data to the server. For detailed information on these functions, see the
   cfajaxproxy tag in the CFML Reference.

* It manages the interactions between the client and the CFC, including serializing and deserializing JavaScript arrays
   and structures to and from JSON format for transmission over the web.

* It ensures automatic serialization (into JSON format) and deserialization of CFC return values.

By using a ColdFusion Ajax proxy, any JavaScript code can call the proxied CFC functions. Thus, any Ajax application,
not just one that uses ColdFusion Ajax UI elements, can use dynamic data provided by CFCs. Also, the proxy provides
access to all of the functions in a CFC, not just the single function that you specify in a bind expression.


Creating a JavaScript CFC proxy
The cfajaxproxy tag with a cfc attribute generates a JavaScript proxy that represents a CFC on the web client.
Because a ColdFusion page that uses the cfajaxproxy tag is used as an Ajax client web page, the page typically starts
with the cfajaxproxy tag (or tags), and the remainder of the page consists of the HTML and JavaScript required to
control the display and perform the page logic on the client.

Note: Because JavaScript is case-sensitive, make sure that you match the case of the keys in any ColdFusion structure or
scope that you send to the client. By default, ColdFusion sets variable names and structure element names to all-
uppercase. (You create structure element names with lowercase characters by specifying the names in associative array
notation, for example, myStruct["myElement"]="value".) The keys for the two arrays in the JSON object that the
ColdFusion SerializeJSON function generates to represent a query are COLUMNS and DATA, for example, not columns
and data.

For more information about creating and using CFC proxies, see the cfajaxproxy tag in the CFML Reference.


Configuring the CFC proxy
The proxy provides several JavaScript functions that you use to control the behavior of the proxy:

* You use the setAsyncMode and setSyncMode functions to control the call mode. By default, all calls to remote CFC
   functions are asynchronous, the most common synchronization method for Ajax applications.

* You use the setCallbackHandler and setErrorHandler functions to specify the functions that handle the results
   of successful and unsuccessful asynchronous calls.

   Note: For error handling to work properly, select the Enable HTTP Status Codes option on the Server Settings >
   Settings page of the ColdFusion Administrator.

* You use the setHTTPMethod function to control whether the call uses a GET HTTP request (the default) or a POST
   request.

* You use the setForm function to prepare the proxy to send full form data to the remote function. This function
   causes the proxy to pass each form field as a separate parameter to the CFC function.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     871
Requesting and Presenting Information




* You use the setReturnFormat function to specify whether to return the result in JSON format (the default), in
    WDDX format, or as plain text. Use the setQueryFormat function to specify whether to return a JSON format
    query as an object with an array of column names and an array of row arrays, or as an object that corresponds to
    the WDDX query format. These functions only effect the format of data returned by ColdFusion. Data sent from
    the proxy to the server is always in JSON format.


Submitting data to a CFC
When you use an Ajax CFC proxy, you send to the CFC function any client-side data that can be serialized to JSON
format, not just form data. However, the proxy cannot serialize DOM tree elements because they are wrappers on
native code. Therefore, you cannot use DOM tree elements directly as parameters to a CFC function that you call by
using an Ajax proxy. To ensure correct serialization to JSON for sending to the CFC, use basic JavaScript types only:
array, object, and simple types. Instead of using a DOM element directly, you pass only the specific element attributes
that you require to the CFC function, either individually or in an array or object.

When you use the cfc attribute, you submit form data to the CFC without refreshing the client page by calling the
proxy setForm function before you call a CFC proxy function in your JavaScript. The proxy function then passes all
field values of the specified form to the CFC function. In the CFC function Arguments scope, the argument names are
the form control ID attributes (or, by default, the name attributes) and the argument values are the control values.

Note: You cannot use the setForm function to submit the contents of file fields.

To pass the form parameters to your proxy function, invoke the proxy function immediately after you call the setForm
function. Subsequent proxy function invocations do not get the form parameters.

If you also pass arguments explicitly to the CFC, cfargument tags in the CFC function that specify the explicitly passed
arguments must precede any cfargument tags for the form fields. For example, if you have the following submitForm
JavaScript function:

function submitForm() {
      var proxy = new remoteHandler();
      proxy.setCallbackHandler(callbackHandler);
      proxy.setErrorHandler(errorHandler);
      proxy.setForm('myform');
      proxy.setData('loggedIn');
}

In this example, the remoteHandler.cfc setData function starts as follows:

      <cffunction name="setData" access="remote" output="false">
          <cfargument name="loggedIn">
          <cfargument name="userName">
      ...

In this example, userName is the name of a form field. If the cfargument tag for userName preceded the cfargument
tag for the loggedIn explicitly passed variable, the CFC function would not get the value of loggedIn. Your CFC
function can omit cfargument tags for the form fields.


Example: Using an asynchronous CFC proxy
The following example uses a remote CFC method to populate a drop-down list of employees. When you select a name
from the list, it uses a call to the CFC method to get information about the employee, and displays the results.

The main application page has the following lines:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                            872
Requesting and Presenting Information




<!--- The cfajaxproxy tag creates a client-side proxy for the emp CFC.
         View the generated page source to see the resulting JavaScript.
         The emp CFC must be in the components subdirectory of the directory
         that contains this page. --->
<cfajaxproxy cfc="components.emp" jsclassname="emp">


<html>
    <head>
         <script type="text/javascript">


               // Function to find the index in an array of the first entry
               // with a specific value.
               // It is used to get the index of a column in the column list.
               Array.prototype.findIdx = function(value){
                    for (var i=0; i < this.length; i++) {
                           if (this[i] == value) {
                               return i;
                           }
                    }
               }


               // Use an asynchronous call to get the employees for the
               // drop-down employee list from the ColdFusion server.
               var getEmployees = function(){
                    // Create an instance of the proxy.
                    var e = new emp();
                    // If you set a callback handler for the proxy, the proxy's calls
                    // are asynchronous.
                    e.setCallbackHandler(populateEmployees);
                    e.setErrorHandler(myErrorHandler);
               // The proxy getEmployees function represents the CFC
               // getEmployees function.
                    e.getEmployees();
               }


               // Callback function to handle the results returned by the
               // getEmployees function and populate the drop-down list.
               var populateEmployees = function(res)
               {
                    with(document.simpleAJAX){
                           var option = new Option();
                           option.text='Select Employee';
                           option.value='0';
                           employee.options[0] = option;
                           for(i=0;i<res.DATA.length;i++){
                               var option = new Option();
                               option.text=res.DATA[i][res.COLUMNS.findIdx('FIRSTNAME')]
                                     + ' ' + res.DATA[i][[res.COLUMNS.findIdx('LASTNAME')]];
                               option.value=res.DATA[i][res.COLUMNS.findIdx('EMP_ID')];
                               employee.options[i+1] = option;
                           }
                    }
               }


               // Use an asynchronous call to get the employee details.
               // The function is called when the user selects an employee.
               var getEmployeeDetails = function(id){



                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                    873
Requesting and Presenting Information




                    var e = new emp();
                    e.setCallbackHandler(populateEmployeeDetails);
                    e.setErrorHandler(myErrorHandler);
               // This time, pass the employee name to the getEmployees CFC
               // function.
                    e.getEmployees(id);
               }
               // Callback function to display the results of the getEmployeeDetails
               // function.
               var populateEmployeeDetails = function(employee)
               {
                    var eId = employee.DATA[0][0];
                    var efname = employee.DATA[0][1];
                    var elname = employee.DATA[0][2];
                    var eemail = employee.DATA[0][3];
                    var ephone = employee.DATA[0][4];
                    var edepartment = employee.DATA[0][5];


                    with(document.simpleAJAX){
                           empData.innerHTML =
                           '<span style="width:100px">Employee Id:</span>'
                           + '<font color="green"><span align="left">'
                           + eId + '</font></span><br>'
                           + '<span style="width:100px">First Name:</span>'
                           + '<font color="green"><span align="left">'
                           + efname + '</font></span><br>'
                           + '<span style="width:100px">Last Name:</span>'
                           + '<font color="green"><span align="left">'
                           + elname + '</font></span><br>'
                           + '<span style="width:100px">Email:</span>'
                           + '<font color="green"><span align="left">'
                           + eemail + '</span></font><br>'
                           + '<span style="width:100px">Phone:</span>'
                           + '<font color="green"><span align="left">'
                           + ephone + '</font></span><br>'
                           + '<span style="width:100px">Department:</span>'
                           + '<font color="green"><span align="left">'
                           + edepartment + '</font></span>';
                    }
               }


               // Error handler for the asynchronous functions.
               var myErrorHandler = function(statusCode, statusMsg)
               {
                    alert('Status: ' + statusCode + ', ' + statusMsg);




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    874
Requesting and Presenting Information




               }


         </script>
    </head>


    <body>
         <!--- The form to display the employee drop-down list and
         employee data. --->
         <form name="simpleAJAX" method="get">
               List of Employees:&nbsp;&nbsp;&nbsp;
               <select name="employee" onChange="getEmployeeDetails(this.value)">
                     <script language="javascript">
                           getEmployees();
                     </script>
               </select>
               <br><br>
               <span id="empData"></span>
         </form>
    </body>
</html>

The following component, which gets the data from the data source, must be in a file named emp.cfc in the
components subdirectory of the application directory. The CFC uses the cfdocexamples data source that is installed
with ColdFusion if you install the documentation.

<cfcomponent>
    <cfset this.dsn = "cfdocexamples">
    <cffunction name="getEmployees" access="remote" returnFormat="json" output="false">
         <cfargument name="empid" required="no" type="string" default="0">
         <Cfquery name="qryEmp" datasource="#this.dsn#">
               select * from Employees
               <cfif empid neq 0>
               where Emp_ID = #empid#
               </cfif>
         </Cfquery>
         <cfreturn qryEmp>
    </cffunction>
</cfcomponent>



Using Spry with ColdFusion
ColdFusion provides support for mixing native ColdFusion elements and Spry elements in a single application.

* ColdFusion tags use Spry data sets directly in bind expressions. Therefore, a ColdFusion form element, such as
   cfinput, binds to a field in a dynamic Spry data set, and is updated each time the data set updates, including when
   the user selects an item in a Spry control or dynamic region that the data set populates.

   To bind to a Spry data set, specify the data set name followed by the path to the specific element that you bind to,
   by using standard Spry path syntax. For example, if dsFilters is a Spry data set with a name column, the
   {dsFilters.name} bind parameter binds to the value of the current row's name column. The bind parameter
   cannot specify an event; the bind expression is re-evaluated each time the selected row in the data set changes. The
   following example shows the bind syntax:

   <cfinput name="Input1" type="text"
   bind="CfC:DataManager.getInData(filter={dsFilters.name})




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     875
Requesting and Presenting Information




* Spry data sets use a CFC function as the data source. To do this, you simply specify the URL of the CFC in the
   Spry.Data.XMLDataSet function, just as you would invoke any remote CFC method using a URL. Specify the
   method name with a method URL parameter, and pass data to the function in additional URL parameters, as in the
   following example:

    Spry.Data.XMLDataSet("MyAppMgr.cfc?method=getFilter&filter=scores",
   "filters/filter");

* The cfsprydataset tag dynamically creates and updates Spry XML or JSON data sets based on ColdFusion form data.
   Spry dynamic regions and other elements then use this data to control their display.

   The following example shows a cfsprydataset tag that creates a Spry XML data set named dsProducts by calling
   the getData.getProductDetails function and passing it the value of the selected name in a cfgrid control. The data
   set updates each time the name value changes.

    <cfsprydataset
         name="dsProducts"
         type="xml"
         bind="CFC:getData.getProductDetails(prodname={myform:mygrid.name})"
         xpath="products/product"
         options="{method: 'POST'}"
         onBindError="errorHandler">

ColdFusion includes the complete Spry 1.5 framework release in web_root/CFIDE/scripts/ajax/spry directory. For
more information about Spry framework, see www.adobe.com/go/learn_spry_framework_en. For more information,
see the cfsprydataset tag in the CFML Reference.


Spry data set example
This example has the following behavior:

1 It uses a CFC function directly to populate a Spry XML data set, from an XML file.

2 It displays information from the Spry data in a Spry dynamic region list box.

3 It uses the selected item in the Spry data set to control the contents of a cfgrid control. The cfgrid bind expression
   calls a CFC and passes it a parameter bound to the selected item in the Spry XML data set.

4 It creates a second Spry XML data set by using a cfsprydataset tag that binds to the selected item in the cfgrid
   control and calls a CFC function.

5 It displays information from the second Spry data set in a second Spry dynamic region.

The example lets a user select the genre of books to display: all books, fiction, or nonfiction from a Spry list box
populated from the XML file. The selected genre determines the information displayed by a cfgrid control, and a text
input control shows the selected genre. The selected item in the cfgrid control determines the information that is
displayed in a second Spry dynamic region.

The application consists of the following files:

* A roundtrip.cfm page with the display controls and related logic

* A GridDataManager.cfc file with two functions:

   * A getFilter function that gets the XML for the spry data set

   * A getData function that gets the contents of the cfgrid control

   * A getProduct function that gets detailed information on the selected book

* A Filters.xml file with the XML data for the spry data set




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 876
Requesting and Presenting Information




For this example to display images, create an images subdirectory of your application directory that contains images
with the names specified by the BOOKIMAGE column of the cfbookclub database BOOKS table.


The roundtrip.cfm page
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:spry="http://ns.adobe.com/spry">


<head>
<!--- The screen.css style sheet is provided in the Spry distribution. --->
<link href="screen.css" rel="stylesheet" type="text/css" media="all"/>
<!--- Include the XPath and Spry JavaScript files. --->
<script type="text/javascript"
     src="/CFIDE/scripts/ajax/spry/includes/xpath.js"></script>
<script type="text/javascript"
     src="/CFIDE/scripts/ajax/spry/includes/SpryData.js"></script>


<!--- Create the dsFilters Spry XML data set used to populate the FiltersList dynamic region
     that lists the filters. Call the GridDataManager CFC getFilter method directly from a
     Spry XMLDataSet function because no binding is needed. --->
<script>
     var dsFilters = new
          Spry.Data.XMLDataSet("GridDataManager.cfc?method=getFilter", "filters/filter");
</script>


<!--- Use a cfsprydataset tag with binding to generate a dsProduct Spry data set with details
     about the book grid selection. --->
<cfsprydataset
     name="dsProduct"
     type="xml"
     bind="CFC:GridDataManager.getProductDetails(prodname={bookform:bookgrid.TITLE})"
     xpath="products/product"
     options="{method: 'POST'}"
     onBindError="errorHandler">


<!--- Function to handle bind errors. --->
<script language="javascript">
     errorHandler = function(code,msg){
          alert("Error w/bind occurred. See details below:\n\n" + "Error Code: "
               + code + "\n" + "Error Message: " + msg);
     }
</script>


<!--- Specify the size of the FiltersList Spry dynamic region.
               By default it would be unnecessarily large. --->
<style type="text/css">
<!--
#FiltersList {
     height:100px;
     width: 150px;
}
-->
</style>
</head>


<body>
<!--- A Spry dynamic region containing repeated ListBoxItem controls.
     Each item specifies a filter to use in filling the book list grid.



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                        877
Requesting and Presenting Information




    The items are populated by the data from the CFC getFilter method. --->
<div id="FiltersList" spry:region="dsFilters" class="SpryHiddenRegion">
    <div spry:repeat="dsFilters" class="ListBoxItemGroup">
         <div class="ListBoxItem"
               onclick="dsFilters.setCurrentRow('{dsFilters::ds_RowID}');"
               spry:selectgroup="feedsList" spry:select="SelectedListBoxItem"
               spry:hover="ListBoxItemHover">
                    {dsFilters::description}
         </div>
    </div>
</div>


<!--- A ColdFusion form with the book list data grid. --->
<cfform name="bookform">
<!--- Create a book list grid.
         Users select the book for which to get details from this grid.
         Populate it with the results of the CFC getData method.
         Pass the method the value of the name field of the selected
         item in the dsfilters Spry dynamic region. --->
    <cfgrid name="bookgrid"
               format="html"
               bind="CfC:GridDataManager.getData(page={cfgridpage},
                    pageSize={cfgridpagesize},sortCol={cfgridsortcolumn},
                    sortDir={cfgridsortdirection},filter={dsFilters.name})"
               selectMode="browse"
               width=400
               delete="true"
               pageSize=7>
         <cfgridcolumn name="TITLE" header="Name" width=200>
         <cfgridcolumn name="GENRE" header="Type" width=200>
    </cfgrid><br />
    <!--- Show the value of the name field of the selected item in the Spry dynamic region.
    --->
    <cfinput name="filter" bind="{dsFilters.name}">
</cfform>


<hr>


<!--- A Spry dynamic region that uses the dsProduct data set to display information on the
    selected product. --->
<div id="RSSResultsList" spry:detailregion="dsProduct" class="SpryHiddenRegion">
    <strong>{name}</strong><br>
    <img src="images/{bookimage}" alt="product box shot" width="238" height="130"/>
    <div>{desc}</div>
</div>


<hr>


</body>
</html>




                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                          878
Requesting and Presenting Information




The gridDataManager.cfc file
<cfcomponent name="GridDataManager">


    <!--- The getFilter function gets the filter XML to populate the dsFilters Spry data set.
         It specifies returnFormat=plain to send XML text. --->
    <cffunction name="getFilter" access="remote" output="false" returnFormat="plain">
         <cffile action="read" file="#ExpandPath('.')#\Filters.xml" variable="filtersxml">
         <cfcontent type="text/xml" reset="yes">
         <cfreturn filtersxml>
    </cffunction>


    <!--- The getData function returns books that match the specified genre, or all books if
         there is no genre. --->
    <cffunction name="getData" access="remote" output="false">
         <cfargument name="page" required="yes">
               <cfargument name="pageSize" required="yes">
         <cfargument name="sortCol" required="yes">
               <cfargument name="sortDir" required="yes">
         <cfargument name="filter" required="no">
         <cfquery name="books" datasource="cfbookclub">
               select TITLE, GENRE from BOOKS
               <cfif isDefined("arguments.filter") AND arguments.filter NEQ "">
                    where GENRE = '#arguments.filter#'
               </cfif>
               <cfif arguments.sortCol NEQ "" AND arguments.sortDir NEQ "">
                    order by #arguments.sortCol# #arguments.sortDir#
               <cfelse>
                    order by TITLE ASC
               </cfif>
         </cfquery>
         <!--- Return the data only for the current page. --->
         <cfreturn QueryConvertForGrid(books, arguments.page,
               arguments.pageSize)>
    </cffunction>


    <!--- The getProductDetails gets data for a single book and converts it to XML for use
         in the dsProduct Spry data set. --->
    <cffunction name="getProductDetails" access="remote" output="false">
         <cfargument name="prodname" default="The Road">
         <!--- Get the information about the book from the database. --->
         <cfquery name="bookDetails" datasource="cfbookclub">
               select TITLE, GENRE, BOOKIMAGE, BOOKDESCRIPTION from BOOKS
                    where TITLE = '#arguments.prodname#'
         </cfquery>
         <!--- Convert the query results to XML. --->




                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          879
Requesting and Presenting Information




         <cfoutput>
               <cfxml variable="BookDetailsXML" >
                    <?xml version="1.0" encoding="iso-8859-1"?>
                    <products>
                           <product>
                               <name>#BookDetails.TITLE#</name>
                               <category>#BookDetails.GENRE#</category>
                               <bookimage>#BookDetails.BOOKIMAGE#</bookimage>
                               <desc>#BookDetails.BOOKDESCRIPTION#</desc>
                           </product>
                    </products>
               </cfxml>
         </cfoutput>
         <!--- Convert the XML object to an XML string. --->
         <cfset xmldata = xmlparse(BookDetailsXML)>
         <cfcontent type="text/xml" reset="yes">
         <cfreturn xmldata>
     </cffunction>


</cfcomponent>


The Filters.xml file
<?xml version="1.0" encoding="iso-8859-1"?>
<filters>


     <filter>
         <filterid>1</filterid>
         <name></name>
         <description>No Filter</description>
     </filter>


     <filter>
         <filterid>2</filterid>
         <name>Fiction</name>
         <description>Look for Fiction</description>
     </filter>


     <filter>
         <filterid>3</filterid>
         <name>Non-fiction</name>
         <description>Look for Nonfiction</description>
     </filter>


</filters>



Specifying client-side support files
By default, ColdFusion does the following:

* Gets all the client-side JavaScript, CSS, and other files required for Ajax-based features from the
   web_root/CFIDE/scripts/ajax directory.

* For each application page, imports only the JavaScript files required for the tags that are explicitly included on the page.

In some cases, override these default behaviors.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          880
Requesting and Presenting Information




Specifying a custom script or CSS location
In some situations, you cannot use the default location for the CFIDE directory, because a hosting site blocks access to
it to prevent access to the ColdFusion Administrator. Then, move the CFIDE/scripts directory, or the subdirectories
that you use in your applications, to a different location.

In other situations, you have custom versions of some of the client-side files, such as the CSS files that specify form
control appearance, that apply only to certain applications.

In both situations, inform ColdFusion of the new location. Specify the location of either or both directories containing
the following files:

* All client-side resources required by the ColdFusion Ajax features

* Only the CSS files required by the ColdFusion Ajax features


Specify the client-side resource location
Use any of the following techniques to control the location of the directory that contains the client-side resources
required by the ColdFusion Ajax features:

* If the ColdFusion client-side files required by all applications, including the files used by cfform tags are in a single
    location, you specify the directory in the ColdFusion Administrator > Server Settings > Settings page, Default
    CFFORM ScriptSrc Directory field. The directory you specify and its subdirectories must have the same structure
    and contents as the CFIDE/scripts directory tree.

* If the client-side files required for Ajax features on a specific page are in one location, you use the cfajaximport
    tag scriptsrc attribute to specify the source directory. This tag overrides the setting in the administrator, and does
    not affect the files used for standard cfform features. The directory you specify must have an ajax subdirectory with
    the same structure and contents as the CFIDE/scripts/ajax directory tree.

* You specify the client-side source directory for a specific form in the cfform tag scriptsrc attribute. This setting
    overrides any cfajaximport tag setting for the form and its child controls. The directory you specify and its
    subdirectories must have the same structure and contents as the CFIDE/scripts directory tree.

If you require multiple resource locations for a single page, make sure that each JavaScript file is imported only once
on a page, the first time it is required. Therefore, you cannot use different copies of one JavaScript file on the same page.

To prevent problems, ColdFusion generates an error if you specify more than one scriptsrc attribute on a page.
Therefore, if multiple forms require custom client-side resource files, specify their location in a single cfajaximport
tag, not in scriptsrc attributes in the cfform tags.


Specify the CSS file location
Use the cfajaximport tag cssSrc attribute to specify the location of a directory that contains only the CSS files that
control the style of ColdFusion Ajax-based controls. This attribute overrides any scriptsrc value in determining the
CSS file location. Therefore, you could use the CSS files in the scriptsrc directory tree for most pages, and specify a
cssSrc attribute on selected application pages that require a custom look.

For detailed information on how to use the scriptsrc and cssSrc attributes, and requirements for the contents of
the specified directory, see the cfajaximport tag in the CFML Reference.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       881
Requesting and Presenting Information




Importing tag-specific JavaScript files
In the following situations, ColdFusion does not automatically import the JavaScript files that are required for Ajax-
based tags:

* If you use a ColdFusion Ajax-based tag on a page that you specify by using a source or bind attribute in a container
   tag, such as cfdiv, cflayoutarea, cfpod, or cfwindow. Place a cfajaximport tag on the page that has the
   container tag and use the tags attribute to specify the Ajax feature tags that are on the other pages. (You do not
   have to do this for any tags that are also used on the page with the source attribute.)

* If you use a ColdFusion Ajax JavaScript function, such as ColdFusion.Window.create or ColdFusion.navigate, on
   a page that does not otherwise import the required ColdFusion Ajax JavaScript functions, use the cfajaximport
   tag to import the required JavaScript functions. If you are using a function, such as coldFuson.navigate, that is
   not used for a specific control, you can omit any attributes; the default behavior is to import the base functions that
   are not control-specific. If you are using a function such as ColdFusion.Window.create, use the tags attribute
   and identify the associated control, for example, cfwindow in the following line:

    <cfajaximport tags="cfwindow">

For detailed information on importing tag-specific JavaScript files, see the cfajaximport tag in the CFML Reference.


Using data interchange formats
All complex data that is communicated over an HTTP connection must be serialized into a string representation that
can be transmitted over the web. Most commonly, web client applications use XML or JSON.

As a general rule, ColdFusion automatically handles all necessary serialization and deserialization when you use
ColdFusion Ajax features. The proxies that you create with the cfajaxproxy tag, and the bind expressions that call
CFC functions automatically request data in JSON format, and automatically deserialize JSON data to JavaScript
variables.

ColdFusion also provides the capability to create, convert, and manage data in web interchange formats. This is
helpful, for example, if you use custom Ajax elements to get data from ColdFusion servers.

Also, you use ColdFusion data serialization capability for any applications that create or consume complex data
transmitted over an HTTP connection. For example, if you want to make a web service or feed available in JSON
format, many Yahoo! web services currently are accessible by using simple URLS that return data as JSON.

Note: For information on ColdFusion tags and functions for handling XML or WDDX data, see "Using XML and
WDDX" on page 1058.


Controlling CFC remote return value data format
By default, CFC functions convert data that they return to remote callers to WDDX format. However, they can also
return the data in JSON format, or as plain string data. (XML objects are automatically converted to string
representation when returning plain data.)

ColdFusion Ajax elements that request data from CFC functions, including bind expressions and the function proxies
generated by the cfajaxproxy tag, automatically generate a returnFormat parameter in the HTTP URL to request
JSON data from the CFC function.

Control the CFC function return format in the following ways:

* Use the returnFormat attribute on the cffunction tag.

* Set a returnFormat parameter in the HTTP request that calls the CFC function.

* Use the CFC proxy setReturnFormat function. (You do this only if your client-side code requires non-JSON
   format data, for example, XML or WDDX.)



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       882
Requesting and Presenting Information




If the requested return format is JSON and the function returns a query, ColdFusion serializes the query into a JSON
object in either of the following formats:

* As a JSON object with two entries: an array of column names, and an array of column data arrays.

    These entries are returned in the following situations:

    * By default

    * If you specify an HTTP URL parameter of queryFormat="row"

    * If you use the cfajaxproxy tag and call the proxy object's setReturnFormat function with a parameter value
       of row

    ColdFusion client-side binding and proxy code automatically converts this data into JavaScript that is consumed
    directly by HTML grids.

* As a JSON object with three entries: the number of rows, an array of column names, and an object where each key
    is a column name and each value is an array with the column data

    These entries are returned in the following situations:

    * If you specify an HTTP URL parameter of queryFormat="column"

* If you use the cfajaxproxy tag and call the proxy object's setQueryFormat function with a parameter value of
    column

    ColdFusion client-side binding and proxy code does not convert column format data into JavaScript that is
    consumed directly by HTML grids. However, use this format with the cfajaxproxy tag, because you can refer to
    the returned data by using the column names directly. For example, if a CFC function returns a query with user
    data, you get the user names in your JavaScript by specifying values such as userData.firstName[0] and
    userData.lastName[0].

For more information, see the SerializeJSON function in the CFML Reference.


Using JSON
JSON (JavaScript Object Notation) is a lightweight JavaScript-based data interchange format for transmission between
computer systems. It is a much simpler format than XML or WDDX, and is an efficient, compact format for
transmitting data required for Ajax applications. ColdFusion Ajax bind expressions that use CFCs tell the CFC
function to send the data in JSON format by including a returnformat="json" parameter in the HTTP request, and
automatically handle the JSON-formatted result.

JSON represents objects by using { key : value , key : value... } notation, and represents arrays in standard [ value ,
value... ] notation. Values can be strings, numbers, objects, arrays, true, false, or null. Therefore, you can nest arrays
and objects inside each other. For a detailed specification of the JSON format, see www.JSON.org.

Although ColdFusion Ajax-based controls and the cffunction tag interoperate transparently, without you
converting anything to JSON format, other applications can take advantage of JSON format data. Many public feeds
are now available in JSON format. For example, the Yahoo! search interface returns a JSON data set, del.icio.us
provides JSON feeds showing your posts and tags, and Blogger feeds are available in JSON format. You don't have to
use Ajax to display these feeds; use standard ColdFusion tags and functions to display the results.

The following CFML functions support using JSON format in server-side code:

* DeserializeJSON

* SerializeJSON

* IsJSON




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     883
Requesting and Presenting Information




For more information about these functions and examples, see the CFML Reference.

The following example shows how to use ColdFusion JSON functions in a non-Ajax application. It does a Yahoo
search for references to "ColdFusion Ajax" and displays these results:

* The total number of web pages found

* The titles and summaries of the (by default 10) returned results. The title is a link to the web pageURL.

<!--- Send an http request to the Yahoo Web Search Service. --->
<cfhttp
     url='http://api.search.yahoo.com/WebSearchService/V1/webSearch?appid=YahooDemo&query=
     "ColdFusion Ajax"&output=json'>


<!--- The result is a JSON-formatted string that represents a structure.
          Convert it to a ColdFusion structure. --->
<cfset myJSON=DeserializeJSON(#cfhttp.FileContent#)>


<!--- Display the results. --->
<cfoutput>
     <h1>Results of search for "ColdFusion 9"</h1>
     <p>There were #myJSON.ResultSet.totalResultsAvailable# Entries.<br>
     Here are the first #myJSON.ResultSet.totalResultsReturned#.</p>
     <cfloop index="i" from="1" to="#myJSON.ResultSet.totalResultsReturned#">
          <h3><a href="#myJSON.ResultSet.Result[i].URL#">
               #myJSON.ResultSet.Result[i].Title#</a></h3>
          #myJSON.ResultSet.Result[i].Summary#
     </cfloop>
</cfoutput>


Serialization of numeric values
Note: To use this feature, you must install ColdFusion 9 Update 1.

In the previous releases (including ColdFusion 9), serializing an integer using serializeJSON, converts the number to
a double. For example, SerializeJSON (123) returns 123.0.

In ColdFusion 9.0.1, the integer is retained in its original format but enclosed within quotes. That is, SerializeJSON
(123) returns "123". This is applicable only to positive integers.

The following table gives more examples:


Input                                Serialized JSON

10                                   "10"

012                                  "012"

10.25                                "10.25"

10.25E5                              "1025000.0"

10.25E-5                             "1.025E-4"

-10                                  "-10.0"

-10.25                               "-10.25"


Note: To remove the quotes in the returned value, for positive integers and integers within quotes, set the jvm argument
json.numberasdouble totrue . However, negative integers such as -10 will still be serialized to "-10.0" even if
json.numberasdouble=true.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          884
Requesting and Presenting Information




Ajax plumbing enhancement in ColdFusion 9.0.1
ORM CFCs support the attribute remotingfetch for a property.

By default, remotingfetch is set to false.

While serializing ORM CFCs, ColdFusion introspects the remotingfetch property and if it is false, does not return
any relationship information.

If it is set to true, relationship information is shown. If circular reference is detected, only one level of relationship is
shown.


Debugging Ajax applications
ColdFusion provides a set of JavaScript functions that log information to a pop-up display window. ColdFusion also
logs many standard client-side activities to the window.


Display logging information
To display the logging window you must do the following:

1 Enable ColdFusion to send information to the logging window

2 Request logging window information in the main CFML page request.


Enable logging output
To enable ColdFusion to send information to the logging window, do the following:

* Select the Enable Ajax Debug Log Window option on the ColdFusion Administrator > Debugging & Logging >
    Debug Output Settings page. To view exception messages in the logging window, select the Enable Robust
    Exception Information option on the Debug Output Settings page.

* Make sure that the IP address of the system where you do the debugging is included on the ColdFusion
    Administrator > Debugging & Logging > Debugging IP List page of the ColdFusion Administrator. By default this
    list includes only 127.0.0.1.


Display logging information for a page
To display the logging window when you request a CFML page in the browser, specify an HTTP parameter of cfdebug
in the URL when you request a page, as in the following URL:

 http://localhost:8500/myStore/products.cfm?cfdebug

After the debug log window appears, it continues running until you navigate to a new page in the browser. The logging
window includes options that let you filter the messages by either or both of the following criteria:

* Severity

* Category

You can select to display logging information at any combination of four levels of severity: debug, info, error, and
window. The specific logging function that you call determines the severity level.

The logging window always displays options to filter the output by using standard categories: bind, global, http,
LogReader, and widget. (For information on these categories, see "Standard ColdFusion logging messages" on
page 885.) It also displays a filter option for each custom category that you specify in a ColdFusion logging call.
ColdFusion does not limit the number of categories you create, but create only as many categories as you require to
debug your application effectively.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                          885
Requesting and Presenting Information




Logging information
You call the following JavaScript functions to send information to the logger. In most cases, the function corresponds
to a severity level, as follows:


 Function                                 Severity           Purpose

 ColdFusion.Log.debug                     debug              A message that aids in debugging problems.

 ColdFusion.Log.dump                      debug              A representation of a single variable in a format similar to cfdump. This
                                                             function displays the structure and contents of JavaScript Array and Object
                                                             variables.

 ColdFusion.Log.error                     error              Information about an error. Use this function only in error-handling code.

 ColdFusion.Log.info                      info               Information about properly operating code that is useful in tracing and
                                                             analyzing the client-side code's execution.


You cannot generate a window-level message. This level is reserved for messages generated by the log reader window,
including information about JavaScript errors in the log function calls.

When you call a logging function, you specify a message and a category.

* The message can include JavaScript variables and HTML markup, such as bold text and line breaks.

* The category is a short descriptive name. ColdFusion generates a check box option for each category to filter the
   logging window output. This parameter is optional; the default value is global. You can specify a standard
   ColdFusion category or a custom category.

To log information for a page, you must have a ColdFusion Ajax tag on the page, or use the cfajaximport tag. The
cfajaximport tag does not require any attributes to enable logging.

The following logging function generates an error level, Pod A category log message:

 ColdFusion.Log.error("<b>Invalid value:</b><br>" + arg.A, "Pod A");


Standard ColdFusion logging messages
ColdFusion automatically logs messages in the following categories:


 Category                   Description

 global                     (the default) Messages that are not logged from within the ColdFusion Ajax libraries, for example, initialization
                            of the logging infrastructure.

 http                       Information about HTTP calls and their responses, including the contents of HTTP requests and information on
                            CFC invocations and responses.

 LogReader                  Messages about the log display window.

 bind                       Bind-related actions such as evaluating a bind expression.

 widget                     Control-specific actions such as tree and grid creation.



Ajax programming rules and techniques
The following techniques help you to prevent Ajax application errors, improve application security, and develop more
effective applications.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    886
Requesting and Presenting Information




Preventing errors
The following rules and techniques help you prevent errors in your applications:

* To ensure that your code works properly, make sure that all your pages, including dynamically loaded content and
  pages that contain dynamic regions, have valid html, head, and body tags, and that all script tags are located in
  the page head. This is important for any page with ColdFusion Ajax tags and script tags, where it ensures that the
  script code is processed and that code is generated in the correct order. It also prevents problems in some browsers,
  such as Internet Explorer.

* All JavaScript function definitions on pages that you include dynamically, for example by using a bind expression,
  the ColdFusion.navigate function, or a form submission within a ColdFusion Ajax container tag, must have the
  following syntax format:

   functionName = function(arguments) {function body}

  Function definitions that use the following format might not work:

   function functionName (arguments) {function body}

  However, Adobe recommends that you include all custom JavaScript in external JavaScript files and import them
  on the application's main page, and not write them in-line in code that you get dynamically. Imported pages do not
  have this restriction on the function definition format.

* In a CFM page, if you call a JavaScript function present in a file that is bound to the page, ensure that you do not
  use the keyword var while declaring the function. var declares a function-local scope variable. Therefore, you
  cannot invoke the JavaScript function from the parent page.

* As a general rule, the id attributes or name attributes, when you do not specify id attributes, of controls should be
  unique on the page, including on any pages that you specify in source attributes. Exceptions to this rule include
  the following:

   * You can use the same name attribute for all options in a radio button group. Bind expressions get information
      about the selected button.

   * You can use the same name attribute for check boxes in a group if you want a single bind expression to get
      information about all selected controls in the group.

   * If you have multiple similar forms on a page, you might have controls in each form with the same name or ID.
      You specify the individual controls in bind expressions by including the form name in the bind parameter.

* Do not use an Application.cfc onRequestEnd function or onRequestEnd.cfm page that creates output in
  applications that use the cfajaxproxy tag or bind expressions that call CFC functions to get data. ColdFusion Ajax
  features normally require that all returned data from the server must be in JSON format; the onRequestEnd
  method onRequestEnd.cfm page appends any output as non-JSON information to the end of the returned data.

* By default, all ColdFusion structure element names are in all uppercase characters. Therefore, your client-side Ajax
  code, such as an onSuccess function specified by a cfajaxproxy tag, must use uppercase letters for the returned
  object's element names if you do not explicitly ensure that the element names are not all uppercase. (You can create
  structure element names with lowercase characters by specifying the names in associative array notation, for
  example, myStruct["myElement"]="value".)

* ColdFusion Ajax controls throw JavaScript errors if badly formed HTML causes errors in the browser DOM
  hierarchy order. One example of such badly formed HTML is a table that contains a cfform tag, which in turn
  contains table rows. In this situation, you place the table tag inside the cfform tag.

For browser-specific issues and other issues that could affect application appearance and behavior, see the ColdFusion
Release Notes on the Adobe website at www.adobe.com/go/learn_cfu_docs_en, and the ColdFusion Developer Center
on the Adobe website at www.adobe.com/go/prod_techarticles.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     887
Requesting and Presenting Information




Improving security
ColdFusion includes several capabilities that help to ensure the security of Ajax application. Also, the ColdFusion
Administrator disables output to the client-side logging window by default (see "Enable logging output" on page 884).

* To prevent cross-site scripting, you cannot use remote URLs in code that executes on the client. For example, if you
  use a URL such as http://www.myco.com/mypage.cfm in a cfwindow tag source attribute, the remote page does
  not load in the window and the window shows an error message. If you must access remote URLs, do so in CFML
  code that executes on the server, for example, by using a cfhttp tag on the page specified by a source attribute.

* When a CFC function returns remote data in JSON format, by default, the data is sent without any prefix or
  wrapper. To help prevent cross-site scripting attacks where the attacker accesses the JSON data, you can tell
  ColdFusion to prefix the returned data with one or more characters. You can specify this behavior in several ways.
  The value of an item in the following list is determined by the preceding item in this list:

  1 In the Administrator, enable the Prefix Serialized JSON option on Server Settings > Settings page (the default
     value is false). You can also use this setting to specify the prefix characters. The default prefix is //, which is
     the JavaScript comment marker that turns the returned JSON code into a comment from the browser's
     perspective. The // prefix helps prevent security breaches because it prevents the browser from converting the
     returned value to the equivalent JavaScript objects.

  2 Set the Application.cfc file This.secureJSON and This.secureJSONPrefix variable values, or set the
     cfapplication tag secureJSON and secureJSONPrefix attributes.

  3 Set the cffunction tag secureJSON attribute. (You cannot use the cffunction tag to set the prefix.)

     As a general rule, use one of these techniques for any CFC or CFML page that returns sensitive data, such as
     credit card numbers.

     When you use any of these techniques, the ColdFusion Ajax elements that call CFC functions, including bind
     expressions and the CFC proxies created by the cfajaxproxy tag, automatically remove the security prefix
     when appropriate. You do not have to modify your client-side code.

* ColdFusion provides capabilities that help prevent security attacks where an unauthorized party attempts to
  perform an action on the server, such as changing a password. Use the following techniques to ensure that a request
  to a CFML page or remote CFC function comes from a ColdFusion Ajax feature, such as a bind expression or CFC
  proxy, that is a valid part of your application:

   * In the cffunction tag in a CFC function that returns data to an Ajax client, specify a verifyClient attribute
     with a value of yes.

   * At the top of a CFML page or function that is requested by a ColdFusion Ajax client, call the VerifyClient
     ColdFusion function. This function takes no parameters.

  The VerifyClient function and attribute tell ColdFusion to require an encrypted security token in each request.
  To use this function, enable client management or session management in your application; otherwise, you do not
  get an error, but ColdFusion does not verify clients.

  Enable client verification only for code that responds to ColdFusion Ajax client code, because only the ColdFusion
  Ajax library contains the client-side support code. Enabling client verification for clients other than ColdFusion
  Ajax applications can result in the client application not running.

  As a general rule, use this function for Ajax requests to the server to perform sensitive actions, such as updating
  passwords. Typically, do not enable client verification for public APIs that do not need protected, search engine web
  services. Also, do not enable client verification for the top-level page of an application, because the security token
  is not available when the user enters a URL in the browser address bar.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    888
Requesting and Presenting Information




Programming effectively
The following recommendations help improve or customize your ColdFusion Ajax application.

* Use the AjaxOnLoad function, which specifies a JavaScript function to run when the page loads, to perform any
  initialization actions that are required for a page to function properly. Use the AjaxOnLoad function to call
  functions when a page is loaded in a container tag. One use for this function could be on a page that pops up a login
  window if the user is not already logged in when it displays. You can use the AjaxOnLoad function to specify a
  JavaScript function that determines the login status and pops up the window only if necessary.

* Use the following ColdFusion JavaScript functions to access the Ext JS or Yahoo YUI JavaScript library objects that
  underlie border and tab style cflayout controls, cfwindow controls, and HTML format cfgrid and cftree
  controls. Then use the raw object to modify the displayed control.

   * ColdFusion.Layout.getBorderLayout

   * ColdFusion.Grid.getGridObject

   * ColdFusion.Layout.getTabLayout

   * ColdFusion.Tree.getTreeObject

   * ColdFusion.Window.getWindowObject

  For documentation on the objects and how to manage them, see the Ext documentation at
  extjs.com/deploy/ext/docs/ and the Yahoo toolkit documentation at developer.yahoo.com/yui/.

