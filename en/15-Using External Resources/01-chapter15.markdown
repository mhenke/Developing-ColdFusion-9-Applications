Chapter 15: Using External Resources

Sending and Receiving E-Mail

You can add interactive e-mail features to your Adobe ColdFusion applications by using the cfmail and cfpop tags.
This complete two-way interface to mail servers makes the ColdFusion e-mail capability a vital link to your users.


Using ColdFusion with mail servers
Adding e-mail to your ColdFusion applications lets you respond automatically to user requests. You can use e-mail in
your ColdFusion applications in many different ways, including the following:

· Trigger e-mail messages based on requests or orders from users.

· Allow users to request and receive additional information or documents through e-mail.

· Confirm customer information based on order entries or updates.

· Send invoices or reminders, using information pulled from database queries.

ColdFusion offers several ways to integrate e-mail into your applications. To send e-mail, you generally use the Simple
Mail Transfer Protocol (SMTP). To receive e-mail, you use the Post Office Protocol (POP) to retrieve e-mail from the
mail server. To use e-mail messaging in your ColdFusion applications, you must have access to an SMTP server, a valid
POP account, or both.

In your ColdFusion application pages, you use the cfmail and cfpop tags to send and receive e-mail, respectively.


How ColdFusion sends mail
The ColdFusion implementation of SMTP mail uses a spooled architecture. If you select to spool mail on the Mail page
in the ColdFusion Administrator, when an application page processes a cfmail tag, the messages that are generated are
not sent immediately. Instead, they are spooled to disk and processed in the background. This architecture has two
advantages:

· End users of your application are not required to wait for SMTP processing to complete before a page returns to
   them. This design is especially useful when a user action causes the sending of more than a handful of messages.

· Messages sent using cfmail are delivered reliably, even in the presence of unanticipated events like power outages
   or server crashes.

You can set how frequently ColdFusion checks for spooled mail messages on the Mail page in the ColdFusion
Administrator. If ColdFusion is busy or has a large existing queue of messages, however, delivery can occur after the
spool interval.

Some ColdFusion editions have advanced spooling options that let you fine-tune how ColdFusion sends mail. For
more information, see Configuring and Administering ColdFusion.


Error logging and undelivered messages
ColdFusion logs all errors that occur during SMTP message processing to the file mail.log in the ColdFusion log
directory. The log entries contain the date and time of the error as well as diagnostic information about why the error
occurred.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1193
Using External Resources




If a message is not delivered because of an error, ColdFusion writes it to this directory:

· In Windows: \CFusion\Mail\Undelivr

· On UNIX: /opt/coldfusion/mail/undelivr

The error log entry that corresponds to the undelivered message contains the name of the file written to the UnDelivr
(or undelivr) directory.

Note: To have ColdFusion try to resend a message that it could not deliver, move the message file from the Undelivr
directory to the Spool directory.

For more information about the mail logging settings in the ColdFusion Administrator, see Configuring and
Administering ColdFusion.


Sending e-mail messages
Before you configure ColdFusion to send e-mail messages, you must have access to an SMTP e-mail server. Also,
before you run application pages that access the e-mail server, you can configure the ColdFusion Administrator to use
the SMTP server. If you must override the ColdFusion Administrator SMTP server setting for any messages, you can
specify a new mail server in the server attribute of the cfmail tag.


Configure ColdFusion for e-mail
1 In the ColdFusion Administrator, select Server Settings > Mail.

2 In the Mail Server box, enter the name or IP address of your SMTP mail server.

3 (Optional) Change the Server Port and Connection Timeout default settings.

4 Select the Verify Mail Server Connection option to make sure ColdFusion can access your mail server.

5 If your mail server does not use port 25, the default, SMTP port, change the Server Port default settings.

6 Depending on your ColdFusion edition, the Mail page in the Administrator has additional options that you can use
   to configure and optimize ColdFusion mail behavior. Select these options as appropriate.

7 Click Submit Changes.

   ColdFusion saves the settings. The page displays a message indicating success or failure for connecting to the server.

ColdFusion Enterprise edition includes additional mail spooling and delivery features. For more information on these
features, and for information on the ColdFusion Administrator mail settings, see Configuring and Administering
ColdFusion.


Sending SMTP e-mail with the cfmail tag
The cfmail tag provides support for sending SMTP e-mail from within ColdFusion applications. The cfmail tag is
like the cfoutput tag, except that cfmail outputs the generated text as an SMTP mail message rather than to a page.
The cfmail tag supports all the attributes and commands that you use with cfoutput, including query. The following
table describes basic cfmail tag attributes that you could use to send a simple e-mail message. For a complete list of
attributes, see the cfmail description in the CFML Reference.


 Attribute               Description

 subject                 The subject of the message.

 from                    The e-mail address of the sender.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         1194
Using External Resources




 Attribute               Description

 to                      The e-mail address of the recipient. Use a comma-delimited list to specify multiple recipients.

 cc                      (Optional) The e-mail address of a carbon copy recipient. The recipient address is visible to other recipients. Use
                         a comma-delimited list to specify multiple cc recipients.

 bcc                     (Optional) The e-mail address of a blind carbon copy recipient. The recipient address is not visible to other
                         recipients. Use a comma-delimited list to specify multiple bcc recipients.



Send a simple e-mail message
1 Create a ColdFusion page with the following content:

    <html>
    <head>
         <title>Sending a simple e-mail</title>
    </head>
    <body>
    <h1>Sample e-mail</h1>
    <cfmail
         from="Sender@Company.com"
         to="#URL.email#"
         subject="Sample e-mail from ColdFusion">


    This is a sample e-mail message to show basic e-mail capability.


    </cfmail>
    The e-mail was sent.


    </body>
    </html>

2 Save the file as send_mail.cfm in the myapps directory under your web_root directory.

3 Open your browser and enter the following URL:

   http://localhost:8500/myapps/send_mail.cfm?email=myname@mycompany.com

   (Replace myname@mycompany.com with your e-mail address.)

   The page sends the e-mail message to you, through your SMTP server.

Note: If you do not receive an e-mail message, check whether you have configured ColdFusion to work with your SMTP
server; for more information, see "Sending e-mail messages" on page 1193.

The cfmail tag has many options that let you customize your mail or control how it is sent. For a description of all
attributes, including options to wrap mail text at a specified column, specify the mail character encoding, and specify
the mail server, user name, and password, see the cfmail description in the CFML Reference.


Sending HTML e-mail
If you know all the mail recipients use mail applications that are capable of reading and interpreting HTML code in a
mail message, you can use the cfmail tag to send an HTML message. The cfmail tag type="HTML" attribute informs
the receiving e-mail client that the message contains embedded HTML tags that must be processed. For an example
that sends HTML mail, see "Including images in a message" on page 1200.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1195
Using External Resources




Sending multipart mail messages
The cfmailpart tag lets you create multipart mail messages, with each part having a different MIME type or character
set. For example, if you do not know that all recipients can interpret HTML mail messages, you can send your message
as a multipart mail with a text part and an HTML part. To do so, use two cfmailpart tags, one with the HTML version
of the message and one with the plain text message, as shown in the following example. To test this example, replace
the To attribute value with a valid e-mail address, save and run the page, and check the incoming e-mail at the address
you entered.

<cfmail from = "peter@domain.com" To = "paul@domain.com"
Subject = "Which version do you see?">
     <cfmailpart
           type="text"
           wraptext="74">
                You are reading this message as plain text, because your mail reader
                does not handle HTML text.
     </cfmailpart>>
     <cfmailpart
           type="html">
                <h3>HTML Mail Message</h3>
                <p>You are reading this message as <strong>HTML</strong>.</p>
                <p>Your mail reader handles HTML text.</p>
     </cfmailpart>
</cfmail>

Note: In the HTML version of the message, escape any number signs, such as those used to specify colors, by using two #
characters; for example, bgcolor="##C5D9E5".


Sample uses of the cfmail tag
An application page containing the cfmail tag dynamically generates e-mail messages based on the tag settings. Some
of the tasks that you can accomplish with cfmail include the following:

· Sending a mail message in which the data the user enters in an HTML form determine the recipient and contents

· Using a query to send a mail message to a database-driven list of recipients

· Using a query to send a customized mail message, such as a billing statement, to a list of recipients that is
   dynamically populated from a database


Sending form-based e-mail
In the following example, the contents of a customer inquiry form submittal are forwarded to the marketing
department. You could also use the same application page to insert the customer inquiry into the database. You
include the following code on your form so that it executes when users enter their information and submit the form:

<cfmail
     from="#Form.EMailAddress#"
     to="marketing@MyCompany.com,sales@MyCompany.com"
     subject="Customer Inquiry">


A customer inquiry was posted to our website:


Name: #Form.FirstName# #Form.LastName#
Subject: #Form.Subject#


#Form.InquiryText#
</cfmail>



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                              1196
Using External Resources




Sending query-based e-mail
In the following example, a query (ProductRequests) retrieves a list of the customers who inquired about a product
during the previous seven days. ColdFusion sends the list, with an appropriate header and footer, to the marketing
department:

<cfmail
     query="ProductRequests"
     from="webmaster@MyCompany.com"
     to="marketing@MyCompany.com"
     subject="Widget status report">


Here is a list of people who have inquired about
MyCompany Widgets during the previous seven days:


<cfoutput>
#ProductRequests.FirstName# #ProductRequests.LastName# (#ProductRequests.Company#) -
#ProductRequests.EMailAddress#&##013;
</cfoutput>


Regards,
The webmaster
webmaster@MyCompany.com


</cfmail>


Reviewing the code
The following table describes the code:


 Code                                                           Description

 <cfoutput>                                                     Presents a dynamic list embedded within a normal message,
 #ProductRequests.FirstName#                                    repeating for each row in the ProductRequests query. Because the
 #ProductRequests.LastName#
                                                                cfmailtagspecifiesaquery,thecfoutputtagdoesnotuseaquery
 (#ProductRequests.Company#) -
                                                                attribute. The &##013; forces a carriage return between output
 #ProductRequests.EMailAddress#&##013;
 </cfoutput>                                                    records.



Sending e-mail to multiple recipients
In addition to simply using a comma-delimited list in the to attribute of the cfmail tag, you can send e-mail to multiple
recipients by using the query attribute of the cfmail tag. The following examples show how you can send the same
message to multiple recipients and how you can customize each message for the recipient.


Sending a simple message to multiple recipients
In the following example, a query (BetaTesters) retrieves a list of people who are beta testing ColdFusion. This query
then notifies each beta tester that a new release is available. The contents of the cfmail tag body are not dynamic. What
is dynamic is the list of e-mail addresses to which the message is sent. Using the variable #TesterEMail#, which refers
to the TesterEmail column in the Betas table, in the to attribute, enables the dynamic list:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1197
Using External Resources




<cfquery name="BetaTesters" datasource="myDSN">
     SELECT * FROM BETAS
</cfquery>


<cfmail query="BetaTesters"
     from="beta@MyCompany.com"
     to="#BetaTesters.TesterEMail#"
     subject="Widget Beta Four Available">


To all Widget beta testers:


Widget Beta Four is now available
for downloading from the MyCompany site.
The URL for the download is:


http://beta.mycompany.com


Regards,
Widget Technical Support
beta@MyCompany.com


</cfmail>


Customizing e-mail for multiple recipients
In the following example, a query (GetCustomers) retrieves the contact information for a list of customers. The query
then sends an e-mail to each customer to verify that the contact information is still valid:




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                  1198
Using External Resources




<cfquery name="GetCustomers" datasource="myDSN">
    SELECT * FROM Customers
</cfquery>


<cfmail query="GetCustomers"
    from="service@MyCompany.com"
    to="#GetCustomers.EMail#"
    subject="Contact Info Verification">


Dear #GetCustomers.FirstName# -


We'd like to verify that our customer
database has the most up-to-date contact
information for your firm. Our current
information is as follows:


Company Name: #GetCustomers.Company#
Contact: #GetCustomers.FirstName# #GetCustomers.LastName#


Address:
    #GetCustomers.Address1#
    #GetCustomers.Address2#
    #GetCustomers.City#, #GetCustomers.State# #GetCustomers.Zip#


Phone: #GetCustomers.Phone#
Fax: #GetCustomers.Fax#
Home Page: #GetCustomers.HomePageURL#


Please let us know if any of this
information has changed, or if we must
get in touch with someone else in your
organization regarding this request.


Thanks,
Customer Service
service@MyCompany.com


</cfmail>


Reviewing the code
The following table describes the code and its function:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                              1199
Using External Resources




 Code                                                         Description

 <cfquery name="GetCustomers" datasource="myDSN">             Retrieves all data from the Customers table into a query named
 SELECT * FROM Customers                                      GetCustomers.
 </cfquery>

 <cfmail query="GetCustomers"                                 Uses the to attribute of cfmail, the #GetCustomers.Email# query
 from="service@MyCompany.com"                                 column causes ColdFusion to send one message to the address listed
 to="#GetCustomers.EMail#"
                                                              in each row of the query. Therefore, the mail body does not use a
 subject="Contact Info Verification">
                                                              cfoutput tag.

 Dear #GetCustomers.FirstName# -                              Uses other query columns (#GetCustomers.FirstName#,
                                                              #GetCustomers.LastName#, and so on) within the cfmail section
 ......
                                                              to customize the contents of the message for each recipient.

 Company Name: #GetCustomers.Company#
 Contact: #GetCustomers.FirstName#
 #GetCustomers.LastName#

 Address:
 #GetCustomers.Address1#
 #GetCustomers.Address2#
 #GetCustomers.City#, #GetCustomers.State#
 #GetCustomers.Zip#

 Phone: #GetCustomers.Phone#
 Fax: #GetCustomers.Fax#
 Home Page: #GetCustomers.HomePageURL#



Digitally signing e-mail
To add digital signature to your mail, specify the attributes sign, keystore, keystorepassword, keyalias, and
keypassword as provided in the following example:

<cfmail from="Sender@Company.com" server="sendmail.myCo.com" sign="true"
keystore="C:\OpenSSL\bin\hello.jks" keystorepassword="digital" to="Recepient@Company.com"
keyalias="crypto" keypassword="signature" subject="Mail with Digital Signature">

To add digital signature to all the mails you send, instead of adding the attributes to the tag, specify the settings in the
Server Settings > Settings page of the ColdFusion Administrator.

The supported keystores are JKS and PKCS12.

Due to import control restrictions in various countries, the policy files (local_policy.jar and US_export_policy.jar)
support only limited cryptography. If the key strength exceeds the limit, you might encounter the error suggesting that
the keystore cannot be loaded. If you are from an eligible country, you can download the unlimited strength version of
the policy files and replace the default cryptography JAR files with them. The files are available on the Java SDK web site.


Using the cfmailparam tag
You use the cfmailparam tag to include files in your message or add a custom header to an e-mail message. You can
send files as attachments or display them inline in the message. You nest the cfmailparam tag within the cfmail tag.


Attaching files to a message
You can use one cfmailparam tag for each attachment, as the following example shows:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1200
Using External Resources




<cfmail from="daniel@MyCompany.com"
     to="jacob@YourCompany.com"
     subject="Requested Files">


Jake,


Here are the files you requested.


Regards,
Dan


<cfmailparam file="c:\widget_launch\photo_01.jpg">
<cfmailparam file="c:\widget_launch\press_release.doc">


</cfmail>

Use a fully qualified system path for the file attribute of cfmailparam. The file must be located on a drive on the
ColdFusion server machine (or a location on the local network), not the browser machine.


Including images in a message
You can use the cfmailparam to include images from other files in an HTML message, as follows:

1 Place a cfmailparam tag for each image following the cfmail start tag.

2 In each cfmailparam tag, do the following

   · Set the file attribute to the location of the image.

   · Specify disposition="inline"

   · Set the contentID attribute to a unique identifier; for example, myImage1.

3 In the location in your HTML where you want the message included, use an img tag such as the following:

    <img src="cid:myImage1">

The following example shows a simple mail message with an inline image. In this case, the image is located between
paragraphs, but you could include it directly inline with the text. To test this example, replace the cfmailto parameter
with a valid e-mail address and change the file parameter to the path to a valid image.

<cfmail type="HTML"
          to = "Peter@myCo.com"
          from = "Paul@AnotherCo.com"
          subject = "Sample inline image">
     <cfmailparam file="C:\Inetpub\wwwroot\web.gif"
          disposition="inline"
          contentID="image1">
     <P>There should be an image here</p>
     <img src="cid:image1">
     <p> This text follows the picture</p>
</cfmail>


Adding a custom header to a message
When the recipient of an e-mail message replies to the message, the reply is sent, by default, to the address specified in
the From field of the original message. You can use the cfmailparam tag to provide a Reply-To e-mail address that tells
the mail client to override the value in the From field. Using cfmailparam, the reply to the following example is
addressed to widget_master@YourCompany.com:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1201
Using External Resources




<cfmail from="jacob@YourCompany.com"
      to="daniel@MyCompany.com"
      subject="Requested Files">
<cfmailparam name="Reply-To" value="widget_master@YourCompany.com">


Dan,
Thanks very much for the sending the widget press release and graphic.
I'm now the company's Widget Master and am accepting e-mail at
widget_master@YourCompany.com.


See you at Widget World 2002!


Jake
</cfmail>

Note: You can combine the two uses of cfmailparam within the same ColdFusion page. Write a separate cfmailparam
tag for each header and for each attached file.


Receiving e-mail messages
You create ColdFusion pages to access a Post Office Protocol (POP) server to retrieve e-mail message information.
ColdFusion can then display the messages (or just header information), write information to a database, or perform
other actions.

The cfpop tag lets you add Internet mail client features and e-mail consolidation to applications. Although a
conventional mail client provides an adequate interface for personal mail, in many cases an alternative interface to
some mailboxes is advantageous. You use cfpop to develop targeted mail clients to suit the specific needs of a wide
range of applications. The cfpop tag does not work with the other major e-mail protocol, Internet Mail Access
Protocol (IMAP).

Here are three instances in which implementing POP mail makes sense:

· If your site has generic mailboxes that more than one person reads (sales@yourcompany.com), it can be more
    efficient to construct a ColdFusion mail front end to supplement individual user mail clients.

· In many applications, you can automate mail processing when the mail is formatted to serve a particular purpose;
    for example, when subscribing to a list server.

· If you want to save e-mail messages to a database.

Using cfpop with your POP server is like running a query on your mailbox contents. You set its action attribute to
retrieve either headers (using the GetHeaderOnly value) or entire messages (using the GetAll value) and assign it a
name value. You use the name to access the recordset that cfpop returns, for example, when using the cfoutput tag. To
access a POP server, you also must define the server, username, and password attributes.

Note: If the cfpop tag encounters an error, such as an improperly formatted e-mail message, when retrieving messages,
it tries to ignore the error; it returns empty fields in the result structure and retrieves any available messages.

For more information on the cfpop tag syntax and variables, see the CFML Reference.


Using the cfpop tag
Use the following steps to add POP mail to your application.


Implement the cfpop tag in your application
1 Choose the mailboxes to access within your ColdFusion application.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1202
Using External Resources




2 Determine which mail message components you must process: message header, message body, attachments, and
   so on.

3 Decide whether you must store the retrieved messages in a database.

4 Decide whether to delete messages from the POP server after you retrieve them.

5 Incorporate the cfpop tag in your application and create a user interface for accessing a mailbox.

6 Build an application page to handle the output. Retrieved messages can include characters that do not display
   properly in the browser.

You use the cfoutput tag with the HTMLCodeFormat and HTMLEditFormat functions to control output to the
browser. These functions convert characters with special meanings in HTML, such as the less than (<), greater than
(>), and ampersand (&) symbols, into HTML-escaped characters, such as &lt;, &gt;, and &amp;. The HTMLCodeFormat
tag also surrounds the text in a pre tag block.


The cfpop query variables
Like any ColdFusion query, each cfpop query returns variables that provide information about the record:

RecordCount The total number of records returned by the query.

ColumnList A list of the headings of the columns that the query returns.

CurrentRow The current row of the query that cfoutput, or cfloop in a query-driven loop, is processing.

The query includes one variable that the cfquery tag does not return: the UID variable contains the unique identifier
of the e-mail message file.

You can reference these properties in a cfoutput tag by prefixing the query variable with the query name in the name
attribute of cfpop:

<cfoutput>
This operation returned #Sample.RecordCount# messages.
</cfoutput>



Handling POP mail
You use cfpop tag to manage mail. You can specify the messages to act on. You can get message headers, messages, and
attachments, and you can delete messages.


Specifying the message or messages
For all cfpop actions, you can tell the tag to perform the action on all messages, or to do it on selected messages. To
operate on all messages, for example to get all message headers, do not specify a messageNumber or UID attribute. To
operate on specific messages, for example, to delete three selected messages, specify a messageNumber or UID attribute
with a comma-delimited list of messages to act on.


Retrieving message headers
To retrieve message headers without getting the messages, specify action="GetHeaderOnly" in the cfpop tag.
Whether you use cfpop to retrieve the header or the entire message, ColdFusion returns a query object that contains
one row for each message in the specified mailbox. you specify the query object name in the cfpop tag name attribute.
The query has the following fields:

· date

· from




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1203
Using External Resources




· header (A string with all the mail header fields, including entries that have separate fields in the query object)

· messageNumber (The sequential number of the message in the POP server; identical to the row number of the entry
   in the query object)

· messageID (The mail header Message-ID field)

· replyTo

· subject

· cc

· to

· UID (The mail header X-UID field)

The cfpop tag with the getHeaderOnly attribute retrieves any file attachments if you specify an attachmentPath
attribute; otherwise, it does not get the attachments, and the attachmentfiles column contains empty strings.


Retrieve only the message header
1 Create a ColdFusion page with the following content:

    <html>
   <head>
   <title>POP Mail Message Header Example</title>
   </head>


   <body>
   <h2>This example retrieves message header information:</h2>


   <cfpop server="mail.company.com"
        username=#myusername#
        password=#mypassword#
        action="GetHeaderOnly"
        name="Sample">


   <cfoutput query="Sample">
        MessageNumber: #HTMLEditFormat(Sample.messageNumber)# <br>
        To: #HTMLEditFormat(Sample.to)# <br>
        From: #HTMLEditFormat(Sample.from)# <br>
        Subject: #HTMLEditFormat(Sample.subject)# <br>
        Date: #HTMLEditFormat(Sample.date)#<br>
              Cc: #HTMLEditFormat(Sample.cc)# <br>
        ReplyTo: #HTMLEditFormat(Sample.replyTo)# <br><br>
   </cfoutput>


   </body>
   </html>

2 Edit the following lines so that they use valid values for your POP mail server, user name, and password:

    <cfpop server="mail.company.com"
        username=#myusername#
        password=#mypassword#

3 Save the file as header_only.cfm in the myapps directory under your web_root and view it in your web browser:

   This code retrieves the message headers and stores them in a cfpop recordset called Sample. For more information
   about working with recordset data, see "Using Query of Queries" on page 428.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1204
Using External Resources




The HTMLCodeFormat function replaces characters that have meaning in HTML, such as the less than (<) and greater
than (>) signs that can surround detailed e-mail address information, with escaped characters such as &lt; and &gt;.

In addition, you can process the date returned by cfpop with the ParseDateTime function, which accepts an argument
for converting POP date/time objects into a CFML date-time object.

You can reference any of these columns in a cfoutput tag, as the following example shows:

<cfoutput>
      #ParseDateTime(queryname.date, "POP")#
      #HTMLCodeFormat(queryname.from)#
      #HTMLCodeFormat(queryname.messageNumber)#
</cfoutput>


Retrieving messages
When you use the cfpop tag with action="GetAll", ColdFusion returns the same columns as with getheaderonly,
plus the following additional columns:

· attachments (A tab-delimited list of attachment filenames)

· attachmentfiles (A tab-delimited list of paths to the attachment files retrieved to the local server, if any. You get the
    files only if you specify an attachmentpath attribute.)

· body

· htmlbody

· textbody

If the message is multipart, the htmlbody and textbody fields contain the contents of the HTML and plain text parts,
and the body field has the first part in the message. If the message has only one part, the body contains the message,
and either the htmlbody or textbody field, depending on the message type, also has a copy of the message.


Retrieve entire messages
1 Create a ColdFusion page with the following content:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          1205
Using External Resources




    <html>
    <head><title>POP Mail Message Body Example</title></head>


    <body>
    <h2>This example adds retrieval of the message body:</h2>
    <cfpop server="mail.company.com"
          username=#myusername#
          password=#mypassword#
          action="GetAll"
          name="Sample">


    <cfoutput query="Sample">
          MessageNumber: #HTMLEditFormat(Sample.messageNumber)# <br>
          To: #Sample.to# <br>
          From: #HTMLEditFormat(Sample.from)# <br>
          Subject: #HTMLEditFormat(Sample.subject)# <br>
          Date: #HTMLEditFormat(Sample.date)#<br>
          Cc: #HTMLEditFormat(Sample.cc)# <br>
          ReplyTo: #HTMLEditFormat(Sample.replyTo)# <br>
          <br>
          Body:<br>
          #Sample.body#<br>
          <br>
          Header:<br>
          #HTMLCodeFormat(Sample.header)#<br>
          <hr>
          </cfoutput>


    </body>
    </html>

2 Edit the following lines so that they use valid values for your POP mail server, user name, and password:

    <cfpop server="mail.company.com"
          username=#myusername#
          password=#mypassword#

3 Save the file as header_body.cfm in the myapps directory under your web_root and view it in your web browser:

This example does not use a CFML function to encode the body contents. As a result, the browser displays the
formatted message as you would normally see it in a mail program that supports HTML messages.


Retrieving messages and attachments
When you use the cfpop tag with an attachmentpath attribute to specify the directory in which to store attachments,
ColdFusion retrieves any attachment files from the POP server and saves them in the specified directory. The cfpop
tag fills the attachmentfiles field with a tab-separated list of the locations of the attachment files. Use the cffile tag to
delete these temporary files when they are no longer needed. ColdFusion creates the directory if it does not exist.
(ColdFusion must have the appropriate rights on the system to create the directory.)

If a message has no attachments, the attachments and attachmentfiles columns contain empty strings.

Note: CFML does not provide a way to change the name of a mail attachment returned by cfpop before it tries to save
the file. If the attachment name is invalid for the file system on which ColdFusion is running, the attachment cannot be
saved.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1206
Using External Resources




Retrieve all parts of a message, including attachments
1 Create a ColdFusion page with the following content:

   <html>
   <head>
   <title>POP Mail Message Attachment Example</title>
   </head>


   <body>
   <h2>This example retrieves message header,
   body, and all attachments:</h2>


   <cfpop server="mail.company.com"
        username=#myusername#
        password=#mypassword#
        action="GetAll"
        attachmentpath="c:\temp\attachments"
        name="Sample">


   <cfoutput query="Sample">
        MessageNumber: #HTMLEditFormat(Sample.MessageNumber)# <br>
        To: #HTMLEditFormat(Sample.to)# <br>
        From: #HTMLEditFormat(Sample.from)# <br>
        Subject: #HTMLEditFormat(Sample.subject)# <br>
        Date: #HTMLEditFormat(Sample.date)# <br>
        Cc: #HTMLEditFormat(Sample.cc)# <br>
        ReplyTo: #HTMLEditFormat(Sample.ReplyTo)# <br>
        Attachments: #HTMLEditFormat(Sample.Attachments)# <br>
        Attachment Files: #HTMLEditFormat(Sample.AttachmentFiles)# <br>
        <br>
        Body:<br>
        #Sample.body# <br>
        <br>
        Header:<br>
        HTMLCodeFormat(Sample.header)# <br>
        <hr>
   </cfoutput>


   </body>
   </html>

2 Edit the following lines so that they use valid values for your POP mail server, user name, and password:

   <cfpop server="mail.company.com"
        username=#myusername#
        password=#mypassword#

3 Save the file as header_body_att.cfm in the myapps directory under your web_root and view it in your web browser:

Note: To avoid duplicate filenames when saving attachments, set the generateUniqueFilenames attribute of cfpop to Yes.


Deleting messages
Using the cfpop tag to delete a message permanently removes it from the server. By default, retrieved messages remain
on the POP mail server. To delete the messages, set the action attribute of the cfpop tag to Delete. Use the
messagenumber attribute to specify the messages to delete; omit the attribute to delete all the user's messages from the
server.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  1207
Using External Resources




Note: Message numbers are reassigned at the end of every POP mail server communication that contains a delete action.
For example, if you retrieve four messages from a POP mail server, the server returns the message numbers 1,2,3,4. If you
delete messages 1 and 2 with a single cfpop tag, messages 3 and 4 are assigned message numbers 1 and 2, respectively.


Delete messages
1 Create a ColdFusion page with the following content:

   <html>
   <head>
   <title>POP Mail Message Delete Example</title>
   </head>


   <body>
   <h2>This example deletes messages:</h2>


   <cfpop server="mail.company.com"
        username=#username#
        password=#password#
        action="Delete"
        messagenumber="1,2,3">


   </body>
   </html>

2 Edit the following lines so that they use valid values for your POP mail server, user name, and password:

   <cfpop server="mail.company.com"
        username=#myusername#
        password=#mypassword#

3 Save the file as message_delete.cfm in the myapps directory under your web_root and view the file in your web
   browser.

4 Run the header_only.cfm page that you created to confirm that the messages have been deleted.

Important: When you view this page in your web browser, ColdFusion immediately deletes the messages from the POP
server.



Interacting with Microsoft Exchange Servers

You can use Adobe ColdFusion to interact with Microsoft Exchange servers to send, get, and manage mail; and to
create, get, and manage calendar events, connections, and tasks.


Using ColdFusion with Microsoft Exchange servers
ColdFusion can interact with the Microsoft Exchange server to perform the following actions:


Item                     Actions

Mail messages            get, get attachments, get meeting information, move to a different folder, delete, delete attachments, set
                         properties




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                1208
Using External Resources




Item                     Actions

Calendar events          create, get, get attachments, delete, delete attachments, modify, respond

Contacts                 create, get, get attachments, delete, delete attachments, modify

Tasks                    create, get, get attachments, delete, delete attachments, modify


To perform these actions, you use the following ColdFusion tags:


Tag                                Purpose

cfexchangeconnection               Opens and closes persistent connections between an application and the Exchange server.

                                   Gets information about subfolders of the Inbox.

cfexchangecalendar                 Creates, gets, and manages calendar events.

cfexchangecontact                  Creates, gets, and manages contacts.

cfexchangemail                     Gets and manages mail messages. Does not send mail.

cfmail                             Sends mail to the exchange server.

cfexchangetask                     Creates, gets, and manages tasks.

cfexchangefilter                   Specifies the criteria to get specific items. Used only as a child of the cfexchangecalendar,
                                   cfexchangecontact, cfexchangemail, and cfexchangetask tags that specify the get action.


The following list describes a few of the activities you can do using ColdFusion with the Exchange server:

· Build a customized Exchange web client interface.

· View information about upcoming tasks.

· Create mailing lists based on contact entries.

· Automatically add tasks to users' task lists based on new bug reports or customer contacts.

· Schedule meetings and appointments.

· Show and manage meeting attendee availability.


Managing connections to the Exchange server
To communicate with an Exchange server, establish a connection with the server. The connection can use the HTTP
protocol or the HTTPS protocol. By default, ColdFusion connects to the mailbox that belongs to the login user name,
but you can also connect to any mailbox whose owner has delegated access rights to the login user name. You can also
access the server by using a proxy host.

Note: To establish any connection, the Exchange server must grant the login user Outlook web access. For information
on how to enable this access, see "Enabling Outlook web access" on page 1209.

Connections to the server can be persistent or transient:

· A persistent connection lasts until you explicitly close it. Persistent connections let you use a single connection for
   multiple tasks, which saves the processing overhead of opening and closing a separate connection for each
   interaction with the Exchange server.

· A transient connection lasts for the duration of the tag that interacts with the Exchange server. Transient
   connections are a useful technique on ColdFusion pages where you only have to access the Exchange server for a
   single tag; for example, where you only get a set of contacts.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1209
Using External Resources




Enabling access to the Exchange server
To enable access to the Exchange server, ensure the following:

· The Exchange server, Exchange access, and WebDav access are configured in IIS.

· The Exchange server enables Outlook web access to all login users.

· If you are using HTTPS to log into the exchange server, you have a valid client certificate in the JRE certificate store.


Ensure that IIS is configured for access to the Exchange server
1 Open the IIS manager from the Administrative Tools control panel on the machine where the Exchange server is
   installed.

2 Expand the Web Sites node in the tree on the left pane. If you see Exchange there, the web application is configured
   for Exchange. If you do not see it, follow the Microsoft instructions for configuring Exchange in the website

3 Click the Web Service Extension node in the tree on the left pane. The right pane shows Web Service Extensions
   and their status. Make sure that Microsoft Exchange Server and WebDav entries are both allowed. If either entry is
   prohibited, select it and click the Allow button.


Enabling Outlook web access
To establish any connection, the Exchange server must grant the login user Outlook web access.


Check and grant web access
1 In the Exchange administrator, open Administrative Tools > Active Directory Users and Computers > your domain
   name > users.

2 Right-click the user whose ID you use to establish connections.

3 Select the Exchange Features tab.

4 In the Protocols section, enable the Outlook Web Access entry if it is disabled.


Enabling HTTPS access to the Exchange server
To enable HTTPS access from ColdFusion to the Exchange server you must

· Enable SSL on the Exchange server system

· Ensure that the JRE certificate store has a valid client certificate


Enabling SSL on the Exchange server system
Use the following steps to enable SSL on the Exchange server system:

1 On the system where the Exchange server is installed, open the IIS manager from the Administrative Tools control
   panel.

2 In the tree on the left pane, expand the Web Sites node,

3 Right-click Exchange and ExchWeb in the expanded list and open the Web Site Properties dialog, then click the
   Directory Security tab.

4 In the Secure Communications section, click Edit to open the Secure Communications dialog. Select the Require
   secure channel (SSL) option, click OK, and click Apply.

As an alternative to steps 3 and 4, you could do the following: Right-click Default Web Site. In Secure
Communications->Edit, check the Require secure channel (SSL) option, click OK, and Click Apply. Select the nodes
(for example Exchange) for which to enable SSL.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1210
Using External Resources




Enabling HTTPS access on the ColdFusion server
To use HTTPS to access the exchange server, you must have a valid client certificate in the JRE certificate store. If a
known authority did not issue the certificate on the Exchange server, install a certificate. The Java certificate store
already contains certificates from some authorities.

You can ask your system administrator to give you a certificate that you can install on the ColdFusion server, or you
can do the following:

1 Open Outlook Web Access in Internet Explorer and go to File->Properties.

2 Click the certificates button.

3 Click the Details tab and the 'Copy To File' button on the tab. Then follow the wizard options to save the certificate.

To install the certificate, run the following command using keytool.exe, which is in the jre\bin folder:

keytool.exe -importcert -file <path_to_certificate_file> -keystore ..\lib\security\cacerts

Note: The keytool.exe program requires you to enter a password. The default password is changeit.


Using persistent connections
To open a persistent connection, you use the cfexchangeconnection tag and specify the open action, the server IP
address or URL, the user name, and the name of the connection (which you use in subsequent tags to specify the
connection). You typically also specify a password, and can specify several other attributes, including a proxy host or
a delegate mailbox ID. For details, see cfexchangeconnection in the CFML Reference.

Persistent connections use HTTP or HTTPS protocol with the keepAlive property set to true. As a result, the
connections do not automatically close at the end of an HTTP request or ColdFusion page. Close the connection when
you are done using it. If you do not, ColdFusion retains the connection until an inactivity time-out period elapses, after
which, ColdFusion recovers the connection resources.

Note: You can store a connection in a persistent scope, such as the Application scope, and reuse it on multiple pages.
However, you get no advantage by doing so, because the connections are lightweight and you get no substantial
performance gain if you use a persistent scope.

The following example opens a connection, gets all mail sent from spamsource.com, and deletes the messages from the
Exchange server:

<cfexchangeConnection
     action = "open"
     username = "#user1#"
     password = "#password1#"
     server = "#exchangeServerIP#"
     connection = "conn1">


<cfexchangemail action = "get" name = "spamMail" connection = "conn1">
     <cfexchangefilter name = "fromID" value = "spamsource.com">
</cfexchangemail>


<cfloop query="spamMail">
     <cfexchangemail action = "delete" connection = "conn1"
                uid = "#spamMail.uid#">
</cfloop>


<cfexchangeConnection
     action = "close"
     connection = "conn1">




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1211
Using External Resources




Using transient connections
Transient connections last only as long as the tag that uses them takes to complete processing. To create a transient
connection, you specify the connection directly in your action tag (such as cfexchangetask) by using the same
attributes as you do in the cfexchangeconnection tag (except for the connection name).

The following example uses a transient connection to create a single task:

<!--- Create a structure with the task fields. --->
<cfscript>
     stask = StructNew();
     stask.Priority = "high";
     stask.Status = "Not_Started";
     stask.DueDate = "3:00 PM 09/14/2007";
     stask.Subject = "My New Task";
     stask.PercentCompleted = 0;
     Message = "Do this NOW!";
</cfscript>


<!--- Create the task by using a transient connection. --->
<cfexchangetask action = "create"
     username = "#user1#"
     password = "#password1#"
     server = "#exchangeServerIP#"
     task = "#stask#"
     result = "theUID">


<!--- Display the UID to confirm that the action completed. --->
<cfdump var = "#theUID#">


Accessing delegated accounts
In Exchange, one user can grant, or delegate, another user access rights to their account. Users can delegate reviewer
(read-only), author (read/write), or editor (read-write-delete) rights to any combination of the calendar, contacts,
Inbox, or task list.

Note: You cannot use ColdFusion to delegate access rights.

To access the delegator's account as a delegated user, specify the following information:

· Specify the delegated user's user name and password in the username and password attributes.

· Specify the mailbox name of the account that you are accessing in the mailboxName attribute.

You can access the account in a cfexchangeconnection tag that opens a persistent connection, or in a ColdFusion
Exchange tag that uses a transient connection.

For example, if access rights to the docuser3 account are delegated to docuser4, you can use the
cfexchangeconnection tag as in the following example to open a connection to the docuser3 account by using
docuser4's credentials:

<cfexchangeconnection action="open"
     connection="theConnection"
     server="myexchangeserver.mycompany.com"
     username="docuser4"
     password="secret"
     mailboxName="docuser3">




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1212
Using External Resources




You can use this connection for any activities that docuser3 has delegated to docuser4. If docuser3, for example, has
only delegated reviewer rights to the calendar, you can use this connection only with the cfexchangecalendar tag
with get and getAttachments attributes.


Creating Exchange items
You can create Exchange events, contacts, and tasks by using the cfexchangecalendar, cfexchangecontact, or
cfexchangetask tag, respectively, and specifying an action attribute value of create. You create mail messages by
using the cfmail tag to send the message. For information on sending mail, see "Sending and Receiving E-Mail" on
page 1192.

When you create a calendar event, contact, or task, you specify the action, the connection information (persistent
connection name or transient connection attributes) and an attribute that specifies a structure with the information
you are adding. You can also specify a result variable that contains the value of the Exchange UID for the entry that
you create. You can use this UID to identify the entry in tags that modify or delete the entry.

The name of the attribute that you use to specify the entry information varies with the tag you are using, as follows:


 Tag                             Attribute

 cfexchangecalendar              event

 cfexchangecontact               contact

 cfexchangetask                  task


Enclose in number signs (#) the variable that contains the details of the event, contact, or task data, as in the following
example:

<cfexchangecalendar action="create" connection="myConn" event="#theEvent#"
     result="resultUID">

The contents of the entry information structure depend on the tag. For details of the structure contents, see
cfexchangecalendar, cfexchangecontact, and cfexchangetask in the CFML Reference.

Note: To create an Exchange calendar appointment, create a calendar event and do not specify any required or optional
attendees.

The following example lets a user enter information in a form and creates a contact on the Exchange server with the
information:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                          1213
Using External Resources




<!--- Create a structure to hold the contact information. --->
<cfset sContact="#StructNew()#">


<!--- A self-submitting form for the contact information --->
<cfform format="flash" width="550" height="460">
    <cfformitem type="html"><b>Name</b></cfformitem>
    <cfformgroup type="horizontal" label="">
          <cfinput type="text" label="First" name="firstName" width="200">
          <cfinput type="text" label="Last" name="lastName" width="200">
    </cfformgroup>
    <cfformgroup type="VBox">
          <cfformitem type="html"><b>Address</b></cfformitem>
          <cfinput type="text" label="Company" name="Company" width="435">
          <cfinput type="text" label="Street" name="street" width="435">
          <cfinput type="text" label="City" name="city" width="200">
          <cfselect name="state" label="State" width="100">
                <option value="CA">CA</option>
                <option value="MA">MA</option>
                <option value="WA">WA</option>
          </cfselect>
          <cfinput type="text" label="Country" name="Country" width="200"
                     Value="U.S.A.">
          <cfformitem type="html"><b>Phone</b></cfformitem>
          <cfinput type="text" validate="telephone" label="Business"
                     name="businessPhone" width="200">
          <cfinput type="text" validate="telephone" label="Mobile"
                     name="cellPhone" width="200">
          <cfinput type="text" validate="telephone" label="Fax" name="fax"
                     width="200">
          <cfformitem type="html"><b>Email</b></cfformitem>
          <cfinput type="text" validate="email" name="email" width="200">
    </cfformgroup>


    <cfinput type="Submit" name="submit" value="Submit" >
</cfform>



<!--- If the form was submitted, fill the contact structure from it. --->
<cfif isDefined("Form.Submit")>
    <cfscript>
          sContact.FirstName=Form.firstName;
          sContact.Company=Form.company;
          sContact.LastName=Form.lastName;
          sContact.BusinessAddress.Street=Form.street;
          sContact.BusinessAddress.City=Form.city;
          sContact.BusinessAddress.State=Form.state;




                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1214
Using External Resources




          sContact.BusinessAddress.Country=Form.country;
          sContact.BusinessPhoneNumber=Form.businessPhone;
          sContact.MobilePhoneNumber=Form.cellPhone;
          sContact.BusinessFax=Form.fax;
          sContact.Email1=Form.email;
     </cfscript>


     <!--- Create the contact in Exchange --->
     <cfexchangecontact action="create"
          username ="#user1#"
          password="#password1#"
          server="#exchangeServerIP#"
          contact="#sContact#"
          result="theUID">


     <!--- Display a confirmation that the contact was added. --->
     <cfif isDefined("theUID")>
          <cfoutput>Contact Added. UID is#theUID#</cfoutput>
     </cfif>
</cfif>

For another example of creating items, which creates a task, see "Using transient connections" on page 1211.


Getting Exchange items and attachments
You can get calendar events, contacts, mail messages, and tasks from the Exchange server. You can also get
attachments to these items.

Getting an exchange item and its attachments can require multiple operations.

· To get mail that is not directly in the Inbox, specify the path from the root of the mailbox to the mail folder, and
   you can get items from only a single mail folder at a time. You can use the cfexchangeconnection tag to get the
   names, paths, and sizes of all folders in a mailbox, and can use the results to iterate over the folders.

· To get an attachment to an item, you must first get the item, and then use the item UID to get its attachments.

· If an Exchange item contains a message with inline images, the images are available as attachments. You can get the
   attachments, use the attachment CID to locate the image in the message, and display the image inline.

   Note: The getattachment action does not always populate the CID field for HTML mail that contains inline
   attachments, such as images. This problem occurs because some Exchange clients do not always set the CID values if
   the attachments are sent inline.


Getting and using folder names
To get the names of folders in the mailbox, or the subfolders of a particular folder, use the cfexchangeconnection tag
with the getSubfolders action. This action returns a query with a row for each subfolder. The query has three
columns:

· folder name

· full path from the mailbox to the folder, including the Inbox

· folder size, in bytes

You can specify the folder whose subfolders you are getting and whether to recursively get all levels of subfolders.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                1215
Using External Resources




You can use a folder path from the getSubfolders action in the cfexchangemail tag folder attribute to specify the
folder that contains the mail message that requires action. If you do not specify a folder, the cfexchangemail tag
searches only the top level of the Inbox for the message on which to act.

To perform operations on mail from multiple folders, including getting mail items or attachments, you can loop over
the entries in the query returned by the getSubfolders action, as the following example shows. This example
generates a report of all declined meeting messages in the Inbox and all its subfolders.

<!--- Create a connection. --->
<cfexchangeConnection
     action="open"
     username ="#user2#"
     password="#password2#"
     server="#exchangeServerIP#"
     connection="conn1">


<!--- Get the names and paths to all subfolders. --->
<cfexchangeconnection action="getSubfolders" connection="conn1"
     name="folderInfo" folder="Inbox" recurse="yes">


<!--- Get the information from the Inbox top level.
                The getSubfolders results do not include an Inbox row. --->
     <cfexchangemail action="get" connection="conn1"
                name="theResponses">
          <cfexchangefilter name="MessageType" value="Meeting_Response">
     </cfexchangemail>


<!--- Use a query of queries to select only the declined meetings. --->
<!--- You cannot use cfexchangefilter to filter for the meeting response type. --->
     <cfquery dbtype="query" name="theResponses">
          SELECT * FROM theResponses
          WHERE MEETINGRESPONSE = 'Decline'
     </cfquery>


<!--- Loop through the subfolders and get the meeting responses from each
     folder. --->
<cfloop query="folderInfo">
     <cfexchangemail action="get" connection="conn1"
                name="#folderinfo.foldername#">
          <cfexchangefilter name="folder" value="#folderinfo.folderpath#">
          <cfexchangefilter name="MessageType" value="Meeting_Response">
     </cfexchangemail>


     <!--- Use the evaluate function to get the name of the folder. --->
     <cfset meetingData=evaluate(folderinfo.foldername)>
     <!--- Use a query of queries with a UNION clause to add this folder's
                results to the theResponses query. --->




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1216
Using External Resources




     <cfquery dbtype="query" name="theResponses">
          SELECT * FROM meetingData
          WHERE MEETINGRESPONSE = 'Decline'
          UNION
          SELECT * FROM theResponses
     </cfquery>
</cfloop>


<!--- Close the connection. --->
<cfexchangeConnection
     action="close"
     connection="conn1">


<!--- Display the results. --->
<h3>The Declined Responses:</h3>
<cftable query="theResponses" colheaders="yes" border="yes">
     <cfcol header="From" text="#FROMID#">
     <cfcol header="Subject" text="#SUBJECT#">
     <cfcol header="Message" text="#MESSAGE#">
</cftable>


Getting items
You get one or more events, contacts, mail messages, or tasks from the Exchange server by using a cfexchangecalendar,
cfexchangecontact, cfexchangemail, or cfexchangetask tag, respectively, and specifying an action attribute value of
get. ColdFusion returns the items in a query object that you specify in the name attribute. You determine the items to
get by specifying selection conditions in cfexchangefilter child tags. The code to get items from the Exchange server
has the following pattern:

<cfexchange***
          action="get"
          name="results query object name"
          connection information>
     <cfexchangefilter
                name="filter type"
                value"filter value>
     <cfexchangefilter
                name="data/time filter type"
                from="start date/time"
                to="end date/time">
     .
     .
     .
</cfexchange>

The following rules determine how you get items:

· You can have zero or more cfexchangefilter tags.

   · If you do not specify a maxrows field in the structure specified by the name attribute, ColdFusion gets a
      maximum of 100 items. To get more items, specify a maxrows field value greater than 100.

   · If you specify multiple cfexchangefilter tags with differentname attributes, ColdFusion gets all items that
      match all of the specified conditions.

   · If you specify multiple cfexchangefilter tags with identicalname attributes ColdFusion gets the items that
      match only the last tag with the duplicate name attribute.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1217
Using External Resources




· The name attributes correspond to field names in the Exchange item records. The valid values for the name
   attributes depend on the type of item you are getting. For detailed lists of the valid values, see the corresponding tag
   references in the CFML Reference.

· If the name attribute specifies a field that takes text or numeric information, you use the value attribute to specify
   the condition.

· If the name attribute specifies a field that takes a date, time, or date and time, you use the from and to attributes to
   specify the range. You can omit one of these attributes to specify an open-ended range, such as all dates up to and
   including December 1, 2007.

· Date ranges are inclusive. The selected items include ones with the specified to or from dates.

· You cannot use the empty string as a value attribute to search for an empty value. To find entries where a particular
   field has an empty value, get all entries and use a query of queries to filter the results to include only entries where
   the field is empty.

· In fields that take text strings such as Message and or Subject, ColdFusion returns items that contain the exact
   phrase that you specify in the value attribute.

· When you use the cfexchangemail tag, ColdFusion gets only items a single folder. If you include a filter for a
   folder, ColdFusion gets items that are directly in the Inbox only and does not search any subfolders. For an example
   of getting information from multiple folders, see "Getting and using folder names" on page 1214.

When ColdFusion gets the results, it creates the query object specified in the name attribute, if it does not exist, and
populates each row with a single item such as a mail message. The query columns depend on the type of item. For
example, a mail message has FromID and ToID fields, and a contact has FirstName and LastName fields. For detailed
information on the returned structures, see the corresponding tag in the CFML Reference.

The query results for all types of items have two columns:

· A UID column with the unique ID of the item. You use this value to specify the item when you delete, modify, or
   (for calendar entries) respond to it. You also use the UID value to get the item attachments.

· A HasAttachments column with a Boolean value specifying whether the item has any attachments. If this field is
   true, you can use the getAttachments action to get the attachments.

The following example gets the mail messages that were sent during the last week to the docuser1 user from any e-mail
address that includes adobe.com. To keep this code short, the example uses the cfdump tag to show the results.

<cfset rightNow = Now()>
<cfset lastWeek = DateAdd("d","-7", rightNow)>


<cfexchangemail action="get" name="weeksMail"
           username ="#user1#" password="#password1#"
           server="#exchangeServerIP#">
     <cfexchangefilter name="FromID" value="adobe.com">
     <cfexchangefilter name="TimeSent" from="#lastWeek#" to="#rightNow#">
</cfexchangemail>


<cfdump var="#weeksMail#">


Getting item attachments
To get the attachments to an Exchange contact, event, message, or task, use a ColdFusion Exchange tag with a
getAttachments action. Also specify the following information in the tag:

· The UID of the message that contains the attachment or attachments.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                1218
Using External Resources




· The name of the query to hold the information about the returned attachments. When the tag completes
   processing, the query object contains one record for each retrieved attachment. The query has six columns that
   contain the filename, complete path to the saved attachment file, MIME type, file size, CID value (or an empty
   string) and an indicator that shows whether the attachment is a message.

· The path where the attachment is saved. (If you omit the path, ColdFusion does not get the attachments, but does
   get the information about the attachments.)

· Optionally, whether to create unique filenames by appending numbers to the names when two or more attachments
   have the same names. (The default is to not create unique filenames.)

The following ColdFusion Exchange tag gets all attachments to the message identified by the theUID variable, saves
them in the C:/temp/cf_files/attachments directory, and stores information about the attachments in the attachInfo
structure:

<cfexchangemail action="getattachments"
     connection="myconn1"
     uid="#theUID#"
     name="#attachInfo#"
     attachmentPath="C:/temp/cf_files/attachments"
     generateUniqueFilenames="true">

To get message attachments, you must have the UID of the message and know that the message has attachments. Use
a ColdFusion Exchange tag, such as cfexchangemail, with the get action to determine this information. When the
tag completes processing, the query specified by the name attribute includes the following columns:

· The HasAttachments field is true if a message has one or more attachments

· The UID field contains the Exchange UID of the item. The exact UID format depends on the type of item; event,
   contact, message, or task.

You can use these fields in your decision logic that determines whether to get attachments for a message and
determines the message UID.

The following example gets the attachments to all mail messages from docuser2 in the last week. It places each
message's attachments in a directory whose name is the hexadecimal part of the message UID. For each message with
attachments, the application reports subject and date of the message, followed by a table listing the message's
attachments. The table includes the attachment name, MIME type, and size.

Notice that if a message has multiple attachments with the same name, the attachment information query always lists
the attachments with their original, duplicate names, even if you specify generateUniqueFilenames="true". The
generateUniqueFilenames attribute only affects the names of the files on disk. The attachmentFilePath column of
the attachment information structure does have the unique filenames, however.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               1219
Using External Resources




<cfset rightNow = Now()>
<cfset lastWeek = DateAdd("d","-7", rightNow)>


<cfexchangeconnection
     action="open"
     username ="#user1#"
     password="#password1#"
     server="#exchangeServerIP#"
     connection="conn1">


<cfexchangemail action="get" folder="Inbox/MailTest" name="weeksMail"
          connection="conn1">
     <cfexchangefilter name="FromID" value="docuser2">
     <cfexchangefilter name="TimeSent" from="#lastWeek#" to="#rightNow#">
</cfexchangemail>


<cfloop query="weeksMail">
     <cfif weeksmail.HasAttachment>
          <!--- The UID is surrounded in <> characters and has an @ character.
          Extract the hexadecimal number part for use as a directory name. --->
          <cfset atpos=Find('@', weeksMail.UID)>
          <cfset shortUID=Mid(weeksMail.UID, 2, atpos-2)>


          <cfexchangemail action="getAttachments"
                connection="conn1"
                folder="Inbox/MailTest"
                uid="#weeksMail.uid#"
                name="attachData"
                attachmentPath="C:/temp/cf_files/attachments/#shortUID#"
                generateUniqueFilenames="true">


          <cfoutput>
                Directory #shortUID# contains these attachments to the
                following message:<br />
                Subject: #weeksMail.Subject#<br />
                Sent: #dateFormat(weeksmail.TimeSent)#<br />
                <cftable query="attachData" colheaders="true">
                     <cfcol header="Filename" text="#attachmentFilename#">
                     <cfcol header="Size" text="#size#">
                     <cfcol header="MIME type" text="#mimeType#">
                </cftable>
          </cfoutput>


     </cfif>
</cfloop>


<cfexchangeconnection
     action="close"
     connection="conn1">


Displaying images inline
If an HTML message includes inline images, the Exchange server saves the images as attachments. Take the following
steps to display the images in the retrieved message:

1 Use cfexchangemail tag get action to get the mail message.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 1220
Using External Resources




2 Use cfexchangemail tag getattachments action to get the message attachments. Specify the UID of the mail
   message you got in the previous step. Also specify an attachmentPath attribute value that is under your web root,
   so that you can access the saved files by using a URL.

3 Search through the HTMLMessage field text that you got in step 1 and find the image items. Get the CID (content
   ID) value for each image.

4 Search the attachments query that you got in step 1. For each row with a CID column value that you got in step 3,
   get the corresponding attachmentFilePath column value.

5 Replace every img tag src attribute value with the attachmentFilePath field value that corresponds to the cid
   value.

6 Display the resulting HTML.

The following example shows how to display a message with an inline image by retrieving the image from the
attachments.

<!--- Open the connection to the Exchange server. --->
<cfexchangeconnection
     action="open"
     username = "#user1#"
     password = "#password1#"
     server = "#exchangeServerIP#"
     connection = "testconn">


<!--- Get the mail message. --->
<cfexchangeMail action="get" connection ="testconn" name="getMail">
     <cfexchangeFilter name="Subject" value="sample inline image">
</cfexchangeMail>


<cfdump var="#getMail#">


<!--- The following code assumes we found only one matching message. --->
<cfoutput query="getMail">
     <cfset theUID = #getMail.UID#>
     <cfset htmlmessage = getMail.htmlmessage>
</cfoutput>


<!--- Get the message attachments. --->
<CFExchangeMail action="getAttachments" UID ="#theUID#" connection="testconn"
name="attachments"
attachmentPath="C:\ColdFusion8\wwwroot\My_Stuff\cfexchange\Book\attachments"
generateuniquefilenames="no">


<!--- Extract the image names from the mail message --->
<!--- Initialize the index into the message used in finding --->
<cfset findstart = 1>
<!--- Use an index loop to find all image source entries in the message --->
<!--- This example supports up to 25 inline images --->
<cfloop index="i" from="1" to="25">
     <!--- find a cid: entry --->
     <cfset stringStart[i] = Find('"cid:', htmlmessage, findstart)>


     <!--- Exit the loop if no match was found --->
     <cfif (stringstart[i] EQ 0)>
          <cfbreak>
     </cfif>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1221
Using External Resources




     <!--- Increment the string index used in finding images. --->
     <cfset findstart = stringstart[i] +5 >
     <!--- Get text to the right of âBADCHAR~cid:.âBADCHARTM
           Using a string length of 30 should get more than the image name. --->
     <cfset rightpart[i]=Mid(htmlmessage, findstart, 30)>
     <!--- use the ListFirst function to remove all the text starting
           at the quotation mark. --->
     <cfset imagename[i]=ListFirst(rightpart[i], '"')>


     <!--- Loop over the attachments query and find the CID. --->
     <cfloop query="attachments">
           <!--- Replace the image name with the contents of the attachment --->
           <cfif attachments.CID EQ imagename[i]>
                <cfset htmlmessage = Replace(htmlmessage,"cid:#imagename[i]#",
                "attachments/#attachments.ATTACHMENTFILENAME#")>
           </cfif>
     </cfloop>
</cfloop>


<h3>The full mail message</h3>
<cfoutput>#htmlmessage#</cfoutput>


<cfexchangeconnection
     action="close"
     connection = "testconn">



Modifying Exchange items
You can modify any elements of calendar, contact, and task items that you can set in ColdFusion. For mail message,
you can change the Importance, Sensitivity, and IsRead flags, and you can move the mail messages between
folders.

Note: If an item has attachments and you specify attachments when you modify the item, the new attachments are added
to the previous attachments; they do not replace them. Use the deleteAttachments action to remove any obsolete or
changed attachments.


Modifying calendar, contact, and task items
You can modify calendar, contact, and task items by using the cfexchangecalendar, cfexchangecontact, or
cfexchangetask tag with an action attribute value of modify. You specify a contact, event, or task attribute with
a structure that contains the item properties that you want to change, and their new values. You do not have to specify
the values for properties that you are not changing. To change the end time of a calendar task, for example, you specify
only an EndTime field in the event attribute structure.

The following example lets you create, and then modify a calendar event. When you first submit the form, ColdFusion
creates the calendar event and redisplays the form with the data you entered. Accept the event before you modify the
form and resubmit it. When you submit the form a second time, ColdFusion sends the modification information. For
information about accepting events, see "Working with meetings and appointments" on page 1225.

The following example resends all the event data (to limit the example length), but you could change the example so
that it only sends modified data. This example also omits recurrence information to keep the code relatively simple:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                      1222
Using External Resources




<!--- Initialize the form.eventID to 0, to indicate a new event. --->
<!--- The EventID field is a hidden field managed by this application. --->
<cfparam name="form.eventID" default="0">


<!--- If the form was submitted, populate an event structure from it. --->
<cfif isDefined("Form.Submit")>
    <cfscript>
          sEvent=StructNew();
          sEvent.AllDayEvent="false";
          sEvent.Subject=Form.subject;
          if (IsDefined("Form.allDay")) {
                sEvent.AllDayEvent="true";
                sEvent.StartTime=createDateTime(Year(Form.date), Month(Form.date),
                     Day(Form.date), 8, 0, 0);
          }
          else {
                sEvent.StartTime=createDateTime(Year(Form.date), Month(Form.date),
                     Day(Form.date), Hour(Form.startTime), Minute(Form.startTime), 0);
                sEvent.EndTime=createDateTime(Year(Form.date), Month(Form.date),
                     Day(Form.date), Hour(Form.endTime), Minute(Form.endTime), 0);
          }
          sEvent.Location=Form.location;
          sEvent.RequiredAttendees=Form.requiredAttendees;
          sEvent.OptionalAttendees=Form.optionalAttendees;
          //sEvent.Resources=Form.resources;
          if (Form.reminder NEQ "") {
                sEvent.Reminder=Form.reminder;
          }
          else {
                sEvent.Reminder=0;
          }
          sEvent.Importance=Form.importance;
          sEvent.Sensitivity=Form.sensitivity;
          sEvent.message=Form.Message;
    </cfscript>


    <!--- If the form is being submitted for the first time,
                create an event. --->
    <cfif form.eventID EQ 0>
    <!--- Create the event in Exchange --->
          <cfexchangecalendar action="create"
                username ="#user1#"
                password="#password1#"
                server="#exchangeServerIP#"
                event="#sEvent#"
                result="theUID">
          <!--- Display the new event UID and set form.eventID to it. --->
          <cfif isDefined("theUID")>
                <cfoutput>Event Added. UID is #theUID#</cfoutput>
                <cfset Form.eventID = theUID >
          </cfif>


    <cfelse>
    <!--- The form is being resubmitted with new data; update the event. --->
          <cfexchangecalendar action="modify"
                username ="#user1#"
                password="#password1#"



                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1223
Using External Resources




                server="#exchangeServerIP#"
                event="#sEvent#"
                uid="#Form.eventID#">
          <cfoutput>Event ID #Form.eventID# Updated.</cfoutput>


     </cfif>
</cfif>


<!--- A self-submitting form for the event information --->
<cfform format="xml" preservedata="true" style="width:500" height="600">
     <cfinput type="text" label="Subject" name="subject" style="width:435">
     <br />
     <cfinput type="checkbox" label="All Day Event" name="allDay">
     <cfinput type="datefield" label="Date" name="date" validate="date"
          style="width:100">
     <cfinput type="text" label="Start Time" name="startTime" validate="time"
          style="width:100">
     <cfinput type="text" label="End Time" name="endTime" validate="time"
          style="width:100"><br />
     <cfinput type="text" label="Location" name="location"
          style="width:435"><br />
     <cfinput type="text" label="Required Attendees" name="requiredAttendees"
          style="width:435"><br />
     <cfinput type="text" label="Optional Attendees" name="optionalAttendees"
          style="width:435"><br />
     <cfinput type="text" label="Resources" name="resources"
          style="width:435"><br />
     <cfinput type="text" label="Reminder (minutes)" validate="integer"
          name="reminder" style="width:200">
     <cfselect name="importance" label="Importance" style="width:100">
          <option value="normal">Normal</option>
          <option value="high">High</option>
          <option value="low">Low</option>
     </cfselect>
     <cfselect name="sensitivity" label="Sensitivity" style="width:100">
          <option value="normal">Normal</option>
          <option value="company-confidential">Confidential</option>
          <option value="personal">Personal</option>
          <option value="private">Private</option>
     </cfselect>
     <cfinput type="textarea" label="Message" name="message" style="width:435;
          height:100">
     <cfinput type="hidden" name="eventID" value="#Form.EventID#">
     <cfinput type="Submit" name="submit" value="Submit" >
</cfform>


Setting mail attributes
To set a mail message's Importance, Sensitivity, or IsRead flag, use the cfexchangemail tag with an action
attribute value of set. Specify only the flags that you are changing in the message attribute.

The following example snippet implements a catch-up operation by changing the IsRead flag to true on all mail
messages that are directly in the Inbox and are more than two weeks old. The example does not change the flags on
any messages in folders in the Inbox; to do so, use a separate cfexchangemail tag for each folder. For information on
accessing and using multiple folders, see "Getting and using folder names" on page 1214.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1224
Using External Resources




<!--- Create a structure with a true IsRead field --->
<cfset changeValues.IsRead="true">


<!--- Open the connection. --->
<cfexchangeConnection
     action="open"
     username ="#user1#"
     password="#password1#"
     server="#exchangeServerIP#"
     connection="conn1">


<!--- Get the mail in the Inbox that is at least two weeks old. --->
<cfexchangemail action="get" name="oldMail" connection="conn1">
     <cfexchangefilter name="timeSent" from="01/01/2000"
           to="#DateAdd("d","-14", Now())#">
</cfexchangemail>


<!--- Loop through the resulting oldMail query and set the IsRead indicator
                to true. --->
<cfloop query="oldMail">
     <cfexchangemail action="set"
           connection="conn1"
           message="#changeValues#"
           uid="#oldMail.uid#">
</cfloop>


<!--- Close the connection. --->
<cfexchangeConnection
     action="close"
     connection="conn1">


Moving mail between folders
To move a one or more mail messages from one folder to another, use the cfexchangemail tag move action, as shown
in the following code snippet, which moves all messages with the subject "Rams and Ewes" from the Unread folder in
the Inbox to the Sheep folder in the inbox.

<cfexchangemail action="move" connection="con1" folder="Inbox/Unread"
           destinationfolder="Inbox/Sheep">
     <cfexchangefilter name="subject" value="Rams and Ewes">
</cfexchangemail>



Deleting Exchange items and attachments
To delete an exchange item, use the ColdFusion Exchange tag with the action attribute of delete and specify the item
UID. Deleting the exchange item deletes all attachments

To delete only the attachments to an exchange item, use the ColdFusion Exchange tag with the action attribute of
deleteAttachments and specify the item UID,

This example deletes all meeting requests in the Inbox for meetings that have passed, but does not delete any requests
in folders in the Inbox. To delete requests in the Inbox, use a separate cfexchangemail tag for each folder. For
information on accessing and using multiple folders, see "Getting and using folder names" on page 1214.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1225
Using External Resources




<cfexchangeconnection
    action="open"
    username ="#user#"
    password="#password#"
    server="#exchangeServerIP#"
    connection="conn1">


<!--- Get all meeting notifications from the Inbox. --->
<cfexchangemail action="get" name="requests" connection="conn1">
    <cfexchangefilter name="MessageType" value="Meeting">
</cfexchangemail>


<!--- Get the meeting request data and put it in an array. --->
<cfset i=1>
<cfset meetingData=ArrayNew(1)>
<cfloop query="requests">
    <cfexchangemail action="getmeetinginfo" connection="conn1"
          name="meeting" meetinguid="#MeetingUID#" mailUID="#UID#">
    <cfset meetingData[i]=meeting>
    <cfset i=i+1>
</cfloop>


<!--- Loop through the request data array and delete any outdated
meeting messages from the Inbox. --->
<cfloop index="i" from="1" to="#ArrayLen(meetingData)#" >
    <cfif meetingData[i].StartTime LTE now()>
          <cfexchangemail action="delete" connection="conn1"
                UID="#meetingData[i].UID#">
    </cfif>
</cfloop>


<cfexchangeconnection
    action="close"
    connection="conn1">

For another example that deletes all mail from a known spam address, see "Using persistent connections" on
page 1210.


Working with meetings and appointments
The following techniques apply specifically to calendar events and the notices about meetings that you get in your mail
Inbox:

· How to get detailed information about meeting requests, cancellation notices, and responses to invitations

· How to specify event recurrence


Working with meeting notices and requests
Your mailbox gets a meeting notice when someone takes any of the following actions:

· Sends you a meeting request

· Cancels a meeting in your calendar

· Responds to a meeting request that you sent and tells Exchange to notify you




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1226
Using External Resources




The information provided by the cfexchangemail tag with the get action does not provide detailed information
about meeting. It only includes the following meeting-related information:

· The event UID

· The type of message type: a meeting request, response, or cancellation

· If the message is a response to a meeting request, an indication whether the meeting was accepted, declined, or
   tentatively accepted

Also, a meeting request does not appear in your calendar (so you cannot get detailed information about it using the
cfexchangecalendar tag) until you accept it.

To get detailed information about a meeting message, use the cfexchangemail tag with the getMeetingInfo action.
After getting the information, you can take the necessary action, such as using an cfexchangecalendar tag with the
response action to accept or decline a meeting request.


Get meeting message details and respond to meeting requests
1 Get the mail messages that contain the meeting notifications by using a cfexchangemail tag with an action
   attribute value of get and a cfexchangefilter child tag with the following attributes:

   · A name attribute with a value MessageType

   · A value attribute with a value of Meeting, Meeting_Request, Meeting_Response, or Meeting_Cancel. A
      value of Meeting gets all meeting notifications.

   You can use additional cfexchangefilter tags to further limit the messages you get.

   When the cfexchangemail tag completes processing, the MeetingUID column of the structure specified by the
   cfexchangemail tag name attribute contains the UIDs of the meetings.

2 For each meeting, get the information about the meeting by using a cfexchangemail tag with the following
   attributes:

   · An action attribute value of getMeetingInfo.

   · A meetingUID attribute value with the value from the MeetingUID column of the structure specified by the
      cfexchangemail tag name attribute.

   · (Optional) A mailuid attribute with the UID of the message that contained the meeting notification. Use this
      attribute to identify a specific message if the Inbox contains multiple messages about a single meeting.

3 Use the information returned in step 2 in application-specific logic to determine the required messages and actions.
   For example, you could display all meeting requests in a form that lets a user submit a response to each message.

4 To respond to a meeting request, use the cfexchangecalendar tag with an action value of respond and set the
   following the attributes:

   · Set the uid attribute to the Meeting UID you received in step 2. Do not use the Message UID.

   · Specify a responseType value of accept, decline, or tentative.

   · (Optional) Specify a notify value of true (the default value) or false to control whether the event owner
      receives a meeting response message.

   · If the owner receives a notification, you can also specify a message attribute with a text message that is included
      in the response.

The following example shows how you can use this process. It displays all meeting invitations in the Inbox and lets the
user respond to each request and send a message with the response:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                1227
Using External Resources




<cfexchangeconnection
    action="open"
    username ="#user2#"
    password="#password2#"
    server="#exchangeServerIP#"
    connection="conn1">


<cfif isDefined("Form.Submit")>


<!--- When the form has been submitted, send the responses. --->
    <cfloop index="k" from="1" to="#Form.responses#">
          <cfset resp = Form["response" & k] >
          <cfset msg = Form["respMessage" & k] >
          <cfset msguid = Form["UID" & k] >
          <cfexchangecalendar action="respond" connection="conn1"
                uid="#msguid#" responseType="#resp#" message="#msg#">
          <cfoutput><h4>Response #k# sent!</h4></cfoutput>
    </cfloop>


<cfelse>
    <!--- Get all messages with meeting Requests. --->
    <cfexchangemail action="get" name="requests" connection="conn1">
          <cfexchangefilter name="MessageType" value="Meeting_Request">
    </cfexchangemail>


    <!--- Get the meeting request data. --->
    <cfloop query="requests">
          <cfexchangemail action="getmeetinginfo" connection="conn1"
                name="meeting" meetinguid="#MeetingUID#">
          <cfset meetingData[requests.currentrow]=meeting>
    </cfloop>


    <!--- Display the invitation data in a form. --->
    <cfform name="bar">
          <cfloop index="j" from="1" to="#ArrayLen(meetingData)#">
                <cfoutput>
                     <h3>Meeting Request #j#</h3>
                     Subject: #meetingData[j].Subject# <br />
                     Sensitivity: #meetingData[j].Sensitivity# <br />
                     Organizer: #meetingData[j].Organizer# <br />
                     All Day?: #meetingData[j].AllDayEvent# <br />
                     Day: #DateFormat(meetingData[j].StartTime)#&nbsp;&nbsp;
                     Starts: #TimeFormat(meetingData[j].StartTime)#&nbsp;&nbsp;
                     Ends: #TimeFormat(meetingData[j].EndTime)# <br />
                     Duration: #meetingData[j].Duration# <br />
                     Location: #meetingData[j].Location# <br />
                     Message: #meetingData[j].Message# <br />
                </cfoutput>


                <!--- Specify the response to this invitation. --->
                <h4>response:</h4>
                <cfinput type="radio" checked name="response#j#" value="accept">




                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                             1228
Using External Resources




                     Accept
                 <cfinput type="radio" name="response#j#" value="decline">Decline
                 <cfinput type="radio" name="response#j#" value="tentative">Tentative
                 <br />
                 <cftextarea name="respMessage#j#" label="Message (optional)"
                     width="300" height="200" />
                 <cfinput type="hidden" name="UID#j#"
                     value="#meetingData[j].MeetingUID#">
                 <hr />
           </cfloop>
           <cfinput type="hidden" name="responses"
                 value="#ArrayLen(meetingData)#">
           <cfinput type="Submit" name="submit" value="Submit">
     </cfform>


</cfif>


<cfexchangeconnection
     action="close"
     connection="conn1">

For an example that gets information about all declined meeting messages in the Inbox and all its subfolders, see the
example in "Getting and using folder names" on page 1214.


Specifying Calendar recurrence
To create an event that recurs multiple times, you specify the following fields in the event attribute structure:

· Set the IsRecurring field to true.

· Specify a RecurrenceType field value of DAILY, WEELY, MONTHLY, or YEARLY.

· (Optional) Specify one of the following mutually exclusive fields: RecurrenceCount, RecurrenceEndDate, or
   RecurrenceNoEndDate.

   Note: If you omit all three of these fields, the event is created with no end date, and if you specify a count or end date,
   the RecurrenceNoEndDate value is automatically false; therefore, Specify a RecurrenceNoEndDate field only if you
   are changing an existing event with a recurrence count or end date to one with no end date.

· Specify the recurrence details in additional fields that depend on the recurrence type.

To change an event recurrence, including to change whether the event recurs, you specify only the fields whose values
change. To stop an event from recurring, set the IsRecurring field to false. To convert an event from nonrecurring
to recurring, set the IsRecurring field to true and set all the necessary recurrence fields.

The following sections describe how to specify each type of recurrence. For detailed descriptions of the fields that you
use, see cfexchangecalendar in the CFML Reference.

Note: If you specify a recurrence rule that conflicts with the start date that you specify, the first occurrence of the event is
on first day following the start date that conforms to the rule, not on the start date. For example, if you schedule an event
for the second Tuesday of the month, and specify a start date of June 2, 2007, the first occurrence of the event is on June
12, 2007.


Specifying daily recurrence
To set a recurrence that is based on days, you do one of the following:

· Define a RecurrenceFrequency field to specify the frequency of the event, in days. To schedule a meeting for every
   third day, for example, specify RecurrenceFrequency="3".



                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1229
Using External Resources




· Specify RecurEveryWeekDay="true" to specify a meeting that is held five days a week.

You cannot use daily recurrence to schedule a single event that occurs a multiple number of times, but only on week
days. To schedule such an event, specify a weekly recurrence with multiple recurrence days.

The following CFScript code sample sets daily recurrence for every three days and sets the event to occur 20 times:

IsRecurring="true";
RecurrenceType="DAILY";
RecurrenceCount="20";
RecurrenceFrequency="3";


Specifying weekly recurrence
You can create an event that always occurs on the same day or days of the week, and occurs every week or every several
weeks by specifying RecurrenceType="WEEKLY". You use the following fields to control the frequency:

· Define a RecurrenceFrequency field to specify the frequency of the event, in weeks. If you omit this field, the event
   occurs every week. To schedule a meeting for every fourth week, for example, specify RecurrenceFrequency="4".

· Specify a RecurrenceDays field with a comma-delimited list of one of more of the following strings: MON, TUE, WED,
   THUR, FRI, SAT, SUN. If you omit this attribute, the event recurs on the day of the week determined by the startTime
   field value.

The following CFScript code sample sets an event that occurs on Tuesday and Thursday of every other week until
December 3, 2007.

IsRecurring="true";
RecurrenceType="WEEKLY";
RecurrenceEndDate="12/13/2007";
RecurrenceFrequency="2";
RecurrenceDays="TUE,THU;


Specifying monthly recurrence
You can create an event that always occurs on a monthly basis, or occurs every several months by specifying
RecurrenceType="MONTHLY". You can schedule two types of events:

· Events that occur on the same date of each scheduled month, for example, on the tenth day of every three months.

· Events that occur on the same week of the month and the same day of the week, for example, on the second thursday
   of every month, or on the last Friday of every six months.

To specify a date-based monthly event, you only specify the recurrence type, and, if the recurrence is not every month,
the frequency. ColdFusion schedules the event to occur on the day of the week determined by the startTime field
value. To schedule a meeting that occurs on the start date every four months, specify the following recurrence fields:

IsRecurring="true";
RecurrenceType="MONTHLY";
RecurrenceFrequency="4";

To specify an event that occurs on the same day of the week, specify the following fields in addition to
RecurrenceType:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     1230
Using External Resources




 Field                            Description

 RecurrenceFrequency              The frequency of the event, in months. If you omit this field, the event occurs every month.

 RecurrenceWeek                   The week of the month on which the event occurs. Valid values are first, second, third, fourth,
                                  and last.

 RecurrenceDay                    The day of the week on which the event occurs. Valid values are SUN, MON, TUE, WED, THU, FRI, and SAT.


The following CFScript code sample sets an event that occurs on the third Thursday of every three months:

IsRecurring="true";
RecurrenceType="Monthly";
RecurrenceFrequency="3";
RecurrenceWeek="third";
RecurrenceDay="THU";


Specifying yearly recurrence
You can create an event that always occurs on a yearly basis by specifying RecurrenceType="YEARLY". You can
schedule two types of events:

· Events that occur on the same date of each year, for example, on every August 10.

· Events that occur on a specific day week and month, for example, on the second Thursday of August.

To specify a date-based yearly event, you only specify the recurrence type. ColdFusion schedules the event to occur
each year on the date determined by the startTime field value. To schedule a meeting that occurs on the start date
every year, specify the following recurrence fields:

IsRecurring="true";
RecurrenceType="YEARLY";

To specify an event that occurs on the same day of the week and month each year, specify the following fields in
addition to RecurrenceType:


 Field                            Description

 RecurrenceMonth                  The month of the year which the event occurs. Valid values are JAN, FEB, MAR, APR, MAY, JUN, JUL, AUG,
                                  SEP, OCT, NOV, and DEC.

 RecurrenceWeek                   The week of the month during which the event occurs. Valid values are first, second, third,
                                  fourth, and last.

 RecurrenceDay                    The day of the week on which the event occurs. Valid values are SUN, MON, TUE, WED, THU, FRI, and SAT.


The following CFScript code sample sets an event that occurs on the third Thursday of August three months:

IsRecurring="true";
RecurrenceType="YEARLY";
RecurrenceMonth="AUG";
RecurrenceWeek="third";
RecurrenceDay="THU";


Example: Setting calendar recurrence
The following example lets you create events with all types of recurrence. To limit the code length, it does not prevent
you from attempting to create events with invalid field combinations. When you submit the form, if an event is created,
the form redisplays, preceded by a dump that shows the field values that were used to create the event, and the event
UID. You cannot resubmit the form to modify the event, but you can change some values in the form and create an
event.



                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                      1231
Using External Resources




<!--- Create a structure to hold the event information. --->
<cfparam name="form.eventID" default="0">


<!--- If the form was submitted, populate the event structure from it. --->
<cfif isDefined("Form.Submit")>
    <cfscript>
          sEvent.AllDayEvent="false";
          sEvent=StructNew();
          sEvent.Subject=Form.subject;
          if (IsDefined("Form.allDay")) {
                sEvent.AllDayEvent="true";
                sEvent.StartTime=createDateTime(Year(Form.date), Month(Form.date),
                     Day(Form.date), 8, 0, 0);
          }
          else {
                sEvent.StartTime=createDateTime(Year(Form.date), Month(Form.date),
                     Day(Form.date), Hour(Form.startTime), Minute(Form.startTime), 0);
                sEvent.EndTime=createDateTime(Year(Form.date), Month(Form.date),
                     Day(Form.date), Hour(Form.endTime), Minute(Form.endTime), 0);
          }
          sEvent.Location=Form.location;
          sEvent.RequiredAttendees=Form.requiredAttendees;
          sEvent.OptionalAttendees=Form.optionalAttendees;
          //sEvent.Resources=Form.resources;
          if (Form.reminder NEQ "") {
                sEvent.Reminder=Form.reminder;
          }
          else {
                sEvent.Reminder=0;
          }
          sEvent.Importance=Form.importance;
          sEvent.Sensitivity=Form.sensitivity;
          //Recurrence Fields
          if (IsDefined("Form.isRecurring")) {
                sEvent.IsRecurring="true";}
          if (IsDefined("Form.recurrenceNoEndDate")) {
                sEvent.RecurrenceNoEndDate="true";}
          if (Form.recurrenceCount NEQ "") {
          sEvent.RecurrenceCount=Form.recurrenceCount;}
          if (Form.recurrenceEndDate NEQ "") {
          sEvent.RecurrenceEndDate=createDateTime(Year(Form.recurrenceEndDate),
                Month(Form.recurrenceEndDate), Day(Form.recurrenceEndDate), 0, 0,
                0);}
          sEvent.RecurrenceType=Form.recurrenceType;
          if (Form.recurrenceFrequency NEQ "") {
          sEvent.recurrenceFrequency=Form.recurrenceFrequency;}
          if (IsDefined("Form.recurEveryWeekDay")) {
                sEvent.RecurEveryWeekDay="true";}
          if (Form.recurrenceDays NEQ "") {
          sEvent.RecurrenceDays=Form.recurrenceDays;}
          if (Form.recurrenceDay NEQ "") {
          sEvent.RecurrenceDay=Form.recurrenceDay;}
          if (Form.recurrenceWeek NEQ "") {
          sEvent.RecurrenceWeek=Form.recurrenceWeek;}
          if (Form.recurrenceMonth NEQ "") {
          sEvent.RecurrenceMonth=Form.recurrenceMonth;}




                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                            1232
Using External Resources




          sEvent.message=Form.Message;
    </cfscript>


<cfdump var="#sEvent#">


<!--- Create the event in Exchange. --->
    <cfexchangecalendar action="create"
          username ="#user1#"
          password="#password1#"
          server="#exchangeServerIP#"
          event="#sEvent#"
          result="theUID">
    <!--- Output the UID of the new event --->
    <cfif isDefined("theUID")>
          <cfoutput>Event Added. UID is#theUID#</cfoutput>
          <cfset Form.eventID = theUID >
    </cfif>
</cfif>


<cfform format="xml" preservedata="true" style="width:500" height="700">
    <cfinput type="text" label="Subject" name="subject" style="width:435">
                <br />
    <cfinput type="checkbox" label="All Day Event" name="allDay">
    <cfinput type="datefield" label="Date" name="date" validate="date"
          style="width:100">
    <cfinput type="text" label="Start Time" name="startTime" validate="time"
          style="width:100">
    <cfinput type="text" label="End Time" name="endTime" validate="time"
          style="width:100"><br />
    <cfinput type="text" label="Location" name="location"
          style="width:435"><br />
    <cfinput type="text" label="Required Attendees" name="requiredAttendees"
          style="width:435"><br />
    <cfinput type="text" label="Optional Attendees" name="optionalAttendees"
          style="width:435"><br />
    <cfinput type="text" label="Resources" name="resources"
          style="width:435"><br />
    <cfinput type="text" label="Reminder (minutes)" validate="integer"
          name="reminder" style="width:200">
    <cfselect name="importance" label="Importance" style="width:100">
          <option value="normal">Normal</option>
          <option value="high">High</option>
          <option value="low">Low</option>
    </cfselect>
    <cfselect name="sensitivity" label="Sensitivity" style="width:100">
          <option value="normal">Normal</option>
          <option value="company-confidential">Confidential</option>
          <option value="personal">Personal</option>
          <option value="private">Private</option>
    </cfselect>
    <hr />
    <!--- Recurrence Information --->
    <cfinput type="checkbox" label="IsRecurring" name="isRecurring">
    <cfinput type="checkbox" label="RecurrenceNoEndDate" name="noEndDate">
    <cfinput type="text" label="RecurrenceCount" validate="integer"
          required="false" name="recurrenceCount">
    <cfinput type="text" label="RecurrenceEndDate" validate="date"



                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1233
Using External Resources




          required="false" name="recurrenceEndDate">
     <cfselect name="RecurrenceType" label="Recurrence Type"
                style="width:100">
          <option value="DAILY">Daily</option>
          <option value="WEEKLY">Weekly</option>
          <option value="MONTHLY">Monthly</option>
          <option value="YEARLY">Yearly</option>
     </cfselect>
     <cfinput type="text" label="RecurrenceFrequency" validate="integer"
          name="recurrenceFrequency">
     <cfinput type="checkbox" label="RecurEveryWeekDay"
          name="recurEveryWeekDay">
     <cfinput type="text" label="RecurrenceDays" name="recurrenceDays">
     <cfinput type="text" label="RecurrenceDay" name="recurrenceDay">
     <cfselect name="RecurrenceWeek" label="RecurrenceWeek" style="width:100">
          <option value=""></option>
          <option value="first">First</option>
          <option value="second">Second</option>
          <option value="third">Third</option>
          <option value="fourth">Fourth</option>
          <option value="last">Last</option>
     <cfinput type="text" label="RecurrenceMonth" name="recurrenceMonth">
     </cfselect>
     <hr />
     <cfinput type="textarea" label="Message" name="message" style="width:300;
                height:100">
     <cfinput type="Submit" name="submit" value="Submit" >
</cfform>




Interacting with Remote Servers

Adobe ColdFusion wraps the complexity of Hypertext Transfer Protocol (HTTP) and File Transfer Protocol (FTP)
communications in a simplified tag syntax that lets you extend your site offerings across the web.


About interacting with remote servers
Transfer protocols are mechanisms for moving files and information from a source to one or more destinations. Two
of the more common protocols are the Hypertext Transfer Protocol (HTTP) and the File Transfer Protocol (FTP).
ColdFusion has the cfhttp and cfftp tags that let you use these protocols to interact with remote servers.

The cfhttp tag lets you receive a web page or web-based file, just as a web browser uses HTTP to transport web pages.
When you type a URL into a web browser, you make an HTTP request to a web server. With the cfhttp tag, you can
display a web page, send variables to a ColdFusion or CGI application, retrieve specialized content from a web page,
and create a ColdFusion query from a text file. You can use the Get or Post methods to interact with remote servers.

The cfftp tag takes advantage of the main purpose of FTP--transporting files. Unlike HTTP, FTP was not designed
to interact with other servers for processing and interacting with data. After you establish an FTP connection with the
cfhttp tag, you can use it to upload, download, and manage files and directories.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  1234
Using External Resources




Using cfhttp to interact with the web
The cfhttp tag, which lets you retrieve information from a remote server, is one of the more powerful tags in the CFML
tag set. You can use one of two methods--Get or Post--to interact with a remote server using the cfhttp tag:

· Using the Get method, you can only send information to the remote server in the URL. This method is often used
   for a one-way transaction in which cfhttp retrieves an object.

· Using the Post method, you can pass variables to a ColdFusion page or CGI program, which processes them and
   returns data to the calling page. The calling page then appears or further processes the data that was received. For
   example, when you use cfhttp to Post to another ColdFusion page, that page does not appear. It processes the
   request and returns the results to the original ColdFusion page, which then uses the information as appropriate.


Using the cfhttp Get method
You use Get to retrieve files, including text and binary files, from a specified server. The retrieved information is stored
in a special variable, cfhttp.fileContent. The following examples show several common Get operations.


Retrieve a file and store it in a variable
1 Create a ColdFusion page with the following content:

    <html>
   <head>
         <title>Use Get Method</title>
   </head>
   <body>
   <cfhttp
         method="Get"
         url="http://www.adobe.com"
         resolveurl="Yes">
   <cfoutput>
         #cfhttp.FileContent# <br>
   </cfoutput>


   </body>
   </html>

2 (Optional) Replace the value of the url attribute with another URL.

3 Save the file as get_webpage.cfm in the myapps directory under your web_root and view it in the web browser.

   The browser loads the web page specified in the url attribute.


Reviewing the code
The following table describes the code and its function:


 Code                                               Description

 <cfhttp method="Get"                               Get the page specified in the URL and make the links absolute instead of relative
 url="http://www.adobe.com"                         so that they appear properly.
 resolveurl="Yes">

 <cfoutput>                                         Display the page, which is stored in the variable cfhttp.fileContent, in the
 #cfhttp.FileContent# <br>                          browser.
 </cfoutput>



Get a web page and save it in a file
1 Create a ColdFusion page with the following content:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  1235
Using External Resources




   <html>
   <head>
        <title>Use Get Method</title>
   </head>
   <body>


   <cfhttp
        method = "Get"
        url="http://www.adobe.com/software"
        path="c:\temp"
        file="adobe_software.htm">
   </body>
   </html>

2 (Optional) Replace the value of the url attribute with another URL and change the filename.

3 (Optional) Change the path from C:\temp to a path on your hard drive.

4 Save the page as save_webpage.cfm in the myapps directory under your web_root directory.

5 Go to the specified path and view the file that you specified in a text editor (using the values specified in step 1, the
   path is C:\temp\macr_software.htm).

   The saved file does not appear properly in your browser because the Get operation saves only the specified web page
   HTML. It does not save the frame, image, or other files that the page could include.


Reviewing the code
The following table describes the code and its function:


Code                                              Description

<cfhttp                                           Get the page specified in the URL and save it in the file specified by the path and
method = "Get"                                    file attributes.
url="http://www.adobe.com/software"
path="c:\temp"                                    When you use the path and file attributes, ColdFusion ignores any
file="macr_software.htm">                         resolveurl attribute. As a result, frames and other included files cannot appear
                                                  when you view the saved page.



Get a binary file and save it
1 Create a ColdFusion page with the following content:

   <cfhttp
        method="Get"
        url="http://www.adobe.com/adobe/accessibility/images/spotlight.jpg"
        path="c:\temp"
        file="My_SavedBinary.jpg">
   <cfoutput>
        #cfhttp.MimeType#
   </cfoutput>

2 (Optional) Replace the value of the url attribute with the URL of a binary file that you want to download.

3 (Optional) Change the path from C:\temp to a path on your hard drive.

4 Save the file as save_binary.cfm in the myapps directory under your web_root and open it in the web browser to
   view the MIME type.

5 (Optional) Verify that the binary file now exists at the location you specified in the path attribute.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          1236
Using External Resources




Reviewing the code
The following table describes the code and its function:


Code                                                     Description

<cfhttp method="Get"                                     Get a binary file and save it in the path and file specified.
url="http://www.adobe.com/adobe/accessibility/
images/spotlight.jpg" path="c:\temp"
file="My_SavedBinary.jpg">

<cfoutput>                                               Display the MIME type of the file.
#cfhttp.MimeType#
</cfoutput>



Creating a query object from a text file
You can create a query object from a delimited text file by using the cfhttp tag and specifying method="Get" and the
name attribute. This technique is a powerful method for processing and handling text files. After you create the query
object, you can easily reference columns in the query and perform other ColdFusion operations on the data.

ColdFusion processes text files in the following manner:

· You can specify a field delimiter with the delimiter attribute. The default is a comma.

· If data in a field could include the delimiter character, surround the entire field with the text qualifier character,
   which you can specify with the textqualifier attribute. The default text qualifier is the double-quotation mark (").

· The textqualifier="" specifies that no text qualifier exists. If you use textqualifier="""" (four " marks in
   a row), it explicitly specifies the double-quotation mark as the text qualifier.

· If a text qualifier exists, surround all field values with the text qualifier character.

· To include the text qualifier character in a field, use a double character. For example, if the text qualifier is ", use ""
   to include a quotation mark in the field.

· The first row of text is always interpreted as column headings, so that row is skipped. You can override the file
   column heading names by specifying a different set of names in the columns attribute. Specify a name for each
   column. You then use these new names in your CFML code. However, ColdFusion never treats the first row of the
   file as data.

· When duplicate column heading names are encountered, ColdFusion adds an underscore character to the
   duplicate column name to make it unique. For example, if two CustomerID columns are found, the second is
   renamed "CustomerID_".


Create a query from a text file
1 Create a text file with the following content:

   OrderID,OrderNum,OrderDate,ShipDate,ShipName,ShipAddress
   001,001,01/01/01,01/11/01,Mr. Shipper,123 Main Street
   002,002,01/01/01,01/28/01,Shipper Skipper,128 Maine Street

2 Save the file as text.txt in the myapps directory under your web_root.

3 Create a ColdFusion page with the following content:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1237
Using External Resources




   <cfhttp method="Get"
         url="http://127.0.0.1/myapps/text.txt"
         name="juneorders"
         textqualifier="">


   <cfoutput query="juneorders">
         OrderID: #OrderID#<br>
         Order Number: #OrderNum#<br>
         Order Date: #OrderDate#<br>
   </cfoutput>
   <!--- Now substitute different column names --->
   <!--- by using the columns attribute --->
   <hr>
   Now using replacement column names<br>


   <cfhttp method="Get"
         url="http://127.0.0.1/myapps/text.txt"
         name="juneorders"
         columns="ID,Number,ODate,SDate,Name,Address"
         textqualifier="">


   <cfoutput query="juneorders">
         Order ID: #ID#<br>
         Order Number: #Number#<br>
         Order Date: #SDate#<br>
   </cfoutput>

4 Save the file as query_textfile.cfm in the myapps directory under your web_root and view it in the web browser.


Using the cfhttp Post method
Use the Post method to send cookie, form field, CGI, URL, and file variables to a specified ColdFusion page or CGI
program for processing. For Post operations, use the cfhttpparam tag for each variable you want to post. The Post method
passes data to a specified ColdFusion page or an executable that interprets the variables being sent and returns data.

For example, when you build an HTML form using the Post method, you specify the name of the page to which form
data is passed. You use the Post method in cfhttp in a similar way. However, with the cfhttp tag, the page that receives
the Post does not, itself, display anything.


Pass variables to a ColdFusion page
1 Create a ColdFusion page with the following content:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                       1238
Using External Resources




   <html>
   <head>
        <title>HTTP Post Test</title>
   </head>
   <body>
   <h1>HTTP Post Test</h1>
   <cfhttp method="Post"
        url="http://127.0.0.1:8500/myapps/post_test_server.cfm">
        <cfhttpparam type="Cookie"
              value="cookiemonster"
              name="mycookie6">
        <cfhttpparam type="CGI"
              value="cgivar "
              name="mycgi">
        <cfhttpparam type="URL"
              value="theurl"
              name="myurl">
        <cfhttpparam type="Formfield"
              value="twriter@adobe.com"
              name="emailaddress">
        <cfhttpparam type="File"
              name="myfile"
              file="c:\pix\trees.gif">
   </cfhttp>
   <cfoutput>
   File Content:<br>
        #cfhttp.filecontent#<br>
   Mime Type:#cfhttp.MimeType#<br>
   </cfoutput>
   </body>
   </html>

2 Replace the path to the GIF file to a path on your server (just before the closing cfhttp tag).

3 Save the file as post_test.cfm in the myapps directory under your web_root.

Note: Write a page to view the variables, as described in next procedure.


Reviewing the code
The following table describes the code and its function:


Code                                                        Description

<cfhttp method="Post"                                       Post an HTTP request to the specified page.
url="http://127.0.0.1:8500/myapps/post_test_server
.cfm">

<cfhttpparam type="Cookie" value="cookiemonster"            Send a cookie in the request.
name="mycookie6">

<cfhttpparam type="CGI" value="cgivar "                     Send a CGI variable in the request.
name="mycgi">

<cfhttpparam type="URL" value="theurl"                      Send a URL in the request.
name="myurl">

<cfhttpparam type="Formfield"                               Send a Form field in the request.
value="twriter@adobe.com" name="emailaddress">




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    1239
Using External Resources




Code                                                      Description

<cfhttpparam type="File" name="myfile"                    Send a file in the request.
file="c"\pix\trees.gif">
                                                          The </> tag ends the http request.

<cfoutput>                                                Display the contents of the file that the page that is posted to creates
File Content:<br>                                         by processing the request. In this example, the contents is the output
#cfhttp.filecontent#<br>
                                                          from the cfoutput tag in server.cfm.

Mime Type: #cfhttp.MimeType#<br>                          Display the MIME type of the created file.
</cfoutput>



View the variables
1 Create a ColdFusion page with the following content:

   <html>
   <head><title>HTTP Post Test</title> </head>
   <body>
   <h1>HTTP Post Test</h1>
   <cffile destination="C:\temp\"
        nameconflict="Overwrite"
        filefield="Form.myfile"
        action="Upload"
        attributes="Normal">
   <cfoutput>
        The URL variable is: #URL.myurl# <br>
        The Cookie variable is: #Cookie.mycookie6# <br>
        The CGI variable is: #CGI.mycgi#. <br>
        The Formfield variable is: #Form.emailaddress#. <br>
        The file was uploaded to #File.ServerDirectory#\#File.ServerFile#.
   </cfoutput>
   </body>
   </html>

2 Replace C:\temp\ with an appropriate directory path on your hard drive.

3 Save the file as post_test_server.cfm in the myapps directory under your web_root.

4 View post_test.cfm in your browser and look for the file in C:\temp\ (or your replacement path).


Reviewing the code
The following table describes the code and its function:


Code                                                      Description

<cffile destination="C:\temp\"                            Write the transferred document to a file on the server. You send the
nameconflict="Overwrite" filefield="Form.myfile"          file using the type="File" attribute, but the receiving page gets it as
action="Upload" attributes="Normal">
                                                          a Form variable, not a File variable. This cffile tag creates File variables,
                                                          as follows.

<cfoutput>                                                Output information. This page does not display the results. They are
                                                          passed back to the posting page in its cfhttp.filecontent
                                                          variable.

The URL variable is: #URL.myurl# <br>                     Output the value of the URL variable sent in the HTTP request.

The Cookie variable is: #Cookie.mycookie# <br>            Output the value of the Cookie variable sent in the HTTP request.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                            1240
Using External Resources




 Code                                                       Description

 The CGI variable is: #CGI.mycgi# <br>                      Output the value of the CGI variable sent in the HTTP request.

 The Form variable is: #Form.emailaddress#. <br>            Output the Form variable sent in the HTTP request. You send the
                                                            variable using the type="formField" attribute but the receiving
                                                            page gets it as a Form variable.

 The file was uploaded to                                   Output the results of the cffile tag on this page. This time, the
 #File.ServerDirectory#\#File.ServerFile#.                  variables really are File variables.
 </cfoutput>



Return results of a CGI program
The following code runs a CGI program search.exe on a website and displays the results, including both the MIME
type and length of the response. The search.exe program must expect a "search" parameter.

<cfhttp method="Post"
          url="http://www.my_favorite_site.com/search.exe"
          resolveurl="Yes">
     <cfhttpparam type="Formfield"
          name="search"
          value="ColdFusion">
</cfhttp>
<cfoutput>
     Response Mime Type: #cfhttp.MimeType#<br>
     Response Length: #len(cfhttp.filecontent)# <br>
     Response Content: <br>
          #htmlcodeformat(cfhttp.filecontent)#<br>
</cfoutput>



Performing file operations with cfftp
The cfftp tag lets you perform tasks on remote servers using File Transfer Protocol (FTP). You can use cfftp to cache
connections for batch file transfers when uploading or downloading files.

Note: To use cfftp, select the Enable ColdFusion Security option on the Sandbox Security page in the Security area in
the ColdFusion Administrator. (In the Standard Edition, select Security > Basic Security.)

For server/browser operations, use the cffile, cfcontent, and cfdirectory tags.

Using cfftp involves two major types of operations: connecting, and transferring files. The FTP protocol also provides
commands for listing directories and performing other operations. For a complete list of attributes that support FTP
operations and additional details on using the cfftp tag, see the CFML Reference.


Open an FTP connection and retrieve a file listing
1 Create a ColdFusion page with the following content:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                     1241
Using External Resources




   <html>
   <head>
        <title>FTP Test</title>
   </head>


   <body>
   <h1>FTP Test</h1>
   <!--- Open ftp connection --->
   <cfftp connection="Myftp"
        server="MyServer"
        username="MyUserName"
        password="MyPassword"
        action="Open"
        stoponerror="Yes">


   <!--- Get the current directory name. --->
   <cfftp connection=Myftp
        action="GetCurrentDir"
        stoponerror="Yes">


   <!--- output directory name --->
   <cfoutput>
        The current directory is:#cfftp.returnvalue#<p>
   </cfoutput>


   <!--- Get a listing of the directory. --->
   <cfftp connection=Myftp
        action="listdir"
        directory="#cfftp.returnvalue#"
        name="dirlist"
        stoponerror="Yes">
   <!--- Close the connection.--->
   <cfftp action="close" connection="Myftp">
   <p>Did the connection close successfully?
        <cfoutput>#cfftp.succeeded#</cfoutput></p>


   <!--- output dirlist results --->
   <hr>
   <p>FTP Directory Listing:</p>


   <cftable query="dirlist" colheaders="yes" htmltable>
        <cfcol header="<B>Name</b>" TEXT="#name#">
        <cfcol header="<B>Path</b>" TEXT="#path#">
        <cfcol header="<B>URL</b>" TEXT="#url#">
        <cfcol header="<B>Length</b>" TEXT="#length#">
        <cfcol header="<B>LastModified</b>"
        TEXT="#DateFormat(lastmodified)#">
        <cfcol header="<B>IsDirectory</b>"
              TEXT="#isdirectory#">
   </cftable>

2 Change MyServer to the name of a server for which you have FTP permission.

3 Change MyUserName and MyPassword to a valid user name and password.

   To establish an anonymous connection, enter "anonymous" as the user name and an e-mail address (by
   convention) for the password.




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   1242
Using External Resources




4 Save the file as ftp_connect.cfm in the myapps directory under your web_root and view it in the web browser.


Reviewing the code
The following table describes the code and its function:


 Code                                                       Description

 <cfftp connection="Myftp" server="MyServer"                Open an FTP connection to the MyServer server and log on as
 username="MyUserName" password="MyPassword"                MyUserName. If an error occurs, stop processing and display an error.
 action="Open" stoponerror="Yes">
                                                            You can use this connection in other cfftp tags by specifying the
                                                            Myftp connection.

 <cfftp connection=Myftp action="GetCurrentDir"             Use the Myftp connection to get the name of the current directory;
 stoponerror="Yes">                                         stop processing if an error occurs.
 <cfoutput>
 The current directory is: #cfftp.returnvalue#<p>           Display the current directory.
 </cfoutput>

 <cfftp connection=Myftp action="ListDir"                   Use the Myftp connection to get a directory listing. Use the value
 directory="#cfftp.returnvalue#" name="dirlist"             returned by the last cfftp call (the current directory of the
 stoponerror="Yes">
                                                            connection) to specify the directory to list. Save the results in a
                                                            variable named dirlist (a query object). Stop processing if an error
                                                            occurs.

 <cfftp action="close" connection="Myftp">                  Close the connection, and do not stop processing if the operation fails
 <p>Did the connection close successfully?                  (because you can still use the results). Instead, display the value of the
 <cfoutput>#cfftp.succeeded#</cfoutput></p>
                                                            cfftp.succeeded variable, which is Yes if the connection is closed,
                                                            and No if the operation failed.

 <cftable query="dirlist" colheaders="yes"                  Display a table with the results of the ListDir FTP command.
 htmltable>
 <cfcol header="<B>Name</b>" TEXT="#name#">
 <cfcol header="<B>Path</b>" TEXT="#path#">
 <cfcol header="<B>URL</b>" TEXT="#url#">
 <cfcol header="<B>Length</b>" TEXT="#length#">
 <cfcol header="<B>LastModified</b>"
 TEXT="#DateFormat(lastmodified)#">
 <cfcol header="<B>IsDirectory</b>"
 TEXT="#isdirectory#">
 </cftable>


After you establish a connection with cfftp, you can reuse the connection to perform additional FTP operations until
either you or the server closes the connection. When you access an already-active FTP connection, you need not
respecify the user name, password, or server. In this case, make sure that when you use frames, only one frame uses
the connection object.

Note: For a single simple FTP operation, such as GetFile or PutFile, you need not establish a connection. Specify all the
necessary login information, including the server and any login and password, in the single cfftp request.


Caching connections across multiple pages
The FTP connection established by the cfftp tag is maintained only in the current page unless you explicitly assign the
connection to a variable with Application or Session scope.

Assigning a cfftp connection to an application variable could cause problems, since multiple users could access the
same connection object at the same time. Creating a session variable for a cfftp connection makes more sense,
because the connection is available to only one client and does not last past the end of the session.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1243
Using External Resources




Example: caching a connection
<cflock scope="Session" timeout=10>
<cfftp action="Open"
      username="anonymous"
      password="me@home.com"
      server="ftp.eclipse.com"
      connection="Session.myconnection">
</cflock>

In this example, the connection cache remains available to other pages within the current session. For this approach
to work, enable session variables in your application, and lock code that uses session variables. For more information
on locking, see "Using Persistent Data and Locking" on page 301.

Note: Changing connection characteristics, such the retrycount or timeout values, could require you to re-establish
the connection.


Connection actions and attributes
The following table shows the available cfftp actions and the attributes they require when you use a named (that is,
cached) connection. If you do not specify an existing connection name, specify the username, password, and server
attributes.


 Action                         Attributes                  Action                        Attributes

 Open                           none                        Rename                        existing

                                                                                          new

 Close                          none                        Remove                        server

                                                                                          item

 ChangeDir                      directory                   GetCurrentDir                 none

 CreateDir                      directory                   GetCurrentURL                 none

 ListDir                        name                        ExistsDir                     directory

                                directory

 RemoveDir                      directory                   ExistsFile                    remotefile

 GetFile                        localfile                   Exists                        item

                                remotefile

 PutFile                        localfile

                                remotefile




Managing Files on the Server

The cffile, cfdirectory, and cfcontent tags handle browser and server file management tasks, such as uploading files
from a client to the web server, viewing directory information, and changing the content type that is sent to the web
browser. To perform server-to-server operations, use the cfftp tag, described in "Performing file operations with cfftp"
on page 1240.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         1244
Using External Resources




About file management
Adobe ColdFusion lets you access and manage the files and directories on your ColdFusion server. The cffile tag has
several attributes for moving, copying, deleting, and renaming files. You use the cfdirectory tag to list, create, delete,
and rename directories. The cfcontent tag lets you define the MIME (Multipurpose Internet Mail Extensions) content
type that returns to the web browser.


Using cffile
You can use the cffile tag to work with files on the server in several ways:

· Upload files from a client to the web server using an HTML form

· Move, rename, copy, or delete files on the server

· Read, write, or append to text files on the server

You use the action attribute to specify any of the following file actions: upload, move, rename, copy, delete, read,
readBinary, write, and append. The required attributes depend on the action specified. For example, if
action="write", ColdFusion expects the attributes associated with writing a text file.

Note: Consider the security and logical structure of directories on the server before allowing users access to them. You can
disable the cffile tag in the ColdFusion Administrator. Also, to access files that are not located on the local ColdFusion
system, ColdFusion services must run using an account with permission to access the remote files and directories.


Uploading files
File uploading requires that you create two files:

· An HTML form to specify file upload information

· An action page containing the file upload code

The following procedures describe how to create these files.


Create an HTML file to specify file upload information
1 Create a ColdFusion page with the following content:

    <head><title>Specify File to Upload</title></head>
   <body>
   <h2>Specify File to Upload</h2>
   <!--- the action attribute is the name of the action page --->
   <form action="uploadfileaction.cfm"
              enctype="multipart/form-data"
              method="post">
         <p>Enter the complete path and filename of the file to upload:
         <input type="file"
                   name="FiletoUpload"
                   size="45">
         </p>
         <input type="submit"
                   value="Upload">
   </form>
   </body>

2 Save the file as uploadfileform.cfm in the myapps directory under your web_root and view it in the browser.

Note: The form does not work until you write an action page for it (see the next procedure).




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   1245
Using External Resources




Reviewing the code
The following table describes the code and its function:


Code                                              Description

<form action="uploadfileaction.cfm"               Create a form that contains file selection fields for upload by the user. The action
enctype="multipart/form-data"                     attribute value specifies the ColdFusion template that processes the submitted
method="post">
                                                  form. The enctype attribute value tells the server that the form submission
                                                  contains an uploaded file. The method attribute is set to post to submit a
                                                  ColdFusion form.

<input type="file" name="FiletoUpload"            Allow the user to specify the file to upload. The file type instructs the browser to
size="45">                                        prepare to read and transmit a file from the user system to your server. It
                                                  automatically includes a Browse button to let the user look for the file instead of
                                                  manually entering the entire path and filename.


The user can enter a file path or browse the system and select a file to send.

1 Create a ColdFusion page with the following content:

   <html>
   <head> <title>Upload File</title> </head>
   <body>
   <h2>Upload File</h2>


   <cffile action="upload"
              destination="c:\temp\"
              nameConflict="overwrite"
              fileField="Form.FiletoUpload">


   <cfoutput>
   You uploaded #cffile.ClientFileName#.#cffile.ClientFileExt#
                   successfully to #cffile.ServerDirectory#.
   </cfoutput>


   </body>
   </html>

2 Change the following line to point to an appropriate location on your server:

   destination="c:\temp\"

   Note: This directory must exist on the server.

3 Save the file as uploadfileaction.cfm in the myapps directory under your web_root.

4 View uploadfileform.cfm in the browser, enter a file to upload, and submit the form.

   The file you specified uploads.


Reviewing the code
The following table describes the code and its function:


Code                                              Description

<cffile action="upload"                           Output the name and location of the uploaded file on the client machine.

destination="c:\temp\"                            Specify the destination of the file.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     1246
Using External Resources




 Code                                               Description

 nameConflict="overwrite"                           If the file exists, overwrite it.

 fileField="Form.FiletoUpload">                     Specify the name of the file to upload. Do not enclose the variable in number signs.

 You uploaded                                       Inform the user of the file that was uploaded and its destination. For information
 #cffile.ClientFileName#.#cffile.                   on scope variables, see "Evaluating the results of a file upload" on page 1247.
 ClientFileExt# successfully to
 #cffile.ServerDirectory#.


Note: This example performs no error checking and does not incorporate any security measures. Before deploying an
application that performs file uploads, ensure that you incorporate both error handling and security. For more
information, see "Securing Applications" on page 339 and "Handling Errors" on page 275.


Resolving conflicting filenames
When you save a file to the server, a file with the same name could exist. To resolve this problem, assign one of these
values to the nameConflict attribute of the cffile tag:

Error (default) ColdFusion stops processing the page and returns an error. The file is not saved.

Skip Allows custom behavior based on file properties. The tag does not save the file or return an error.

Overwrite Overwrites a file that has the same name as the uploaded file.

MakeUnique Generates a unique filename for the uploaded file. The name is stored in the file object variables
serverFile and serverFileName. You can use this variable to record the name used when the file was saved. The unique
name might not resemble the attempted name. For more information on file upload status variables, see "Evaluating
the results of a file upload" on page 1247.


Controlling the type of file uploaded
For some applications, you could want to restrict the type of file that is uploaded, for example, to not accept graphic
files in a document library.

You use the accept attribute to restrict the type of file that you allow in an upload. When an accept qualifier is
present, the uploaded file MIME content type must match the criteria specified or an error occurs. The accept
attribute takes a comma-separated list of MIME data names, optionally with wildcards.

The browser determines the file MIME type. Common types, such as image/gif and text/plain, are registered in the
browser.

Note: Current versions of Microsoft Internet Explorer and Netscape support MIME type associations. Other browsers and
earlier versions might ignore these associations.

ColdFusion saves any uploaded file if you omit the accept attribute or specify "*/*". You can restrict the file types, as
demonstrated in the following examples.

The following cffile tag saves an image file only if it is in the GIF format:

<cffile action="Upload"
      fileField="Form.FiletoUpload"
      destination="c:\uploads\"
      nameConflict="Overwrite"
      accept="image/gif">

The following cffile tag saves an image file only if it is in GIF or JPEG format:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1247
Using External Resources




<cffile action="Upload"
     fileField="Form.FiletoUpload"
     destination="c:\uploads\"
     nameConflict="Overwrite"
     accept="image/gif, image/jpeg">

Note: If you receive an error like "The MIME type of the uploaded file (image/jpeg) was not accepted by the server", enter
accept="image/jpeg" to accept JPEG files.

This cffile tag saves any image file, regardless of the format:

<cffile action="Upload"
     fileField="Form.FiletoUpload"
     destination="c:\uploads\"
     nameConflict="Overwrite"
     accept="image/*">


Setting file and directory attributes
In Windows, you specify file attributes using attributes attribute of the cffile tag. In UNIX, you specify file or
directory permissions using the mode attribute of the cffile or cfdirectory tag.


Windows
In Windows, you can set the following file attributes:

· Hidden

· Normal

· ReadOnly

To specify several attributes in CFML, use a comma-separated list for the attributes attribute; for example,
attributes="ReadOnly,Hidden". If you do not use the attributes attribute, the existing attributes of the file are
maintained. If you specify any other attributes in addition to Normal, the additional attribute overrides the Normal
setting.


UNIX
In UNIX, you can individually set permissions on files and directories for each of three types of users--owner, group,
and other. You use a number for each user type. This number is the sum of the numbers for the individual permissions
allowed. Values for the mode attribute correspond to octal values for the UNIX chmod command:

· 4 = read

· 2 = write

· 1 = execute

You enter permissions values in the mode attribute for each type of user: owner, group, and other in that order. For
example, use the following code to assign read permissions for everyone:

mode=444

To give a file or directory owner read/write/execute permissions and read-only permissions for everyone else:

mode=744


Evaluating the results of a file upload
After a file upload is completed, you can retrieve status information using file upload status variables. This status
information includes data about the file, such as its name and the directory where it was saved.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                          1248
Using External Resources




You can access file upload status variables using dot notation, using either file.varname or cffile.varname. Although
you can use either the File or cffile prefix for file upload status variables, cffile is preferred; for example,
cffile.ClientDirectory. The File prefix is retained for backward compatibility.

Note: File status variables are read only. They are set to the results of the most recent cffile operation. If two cffile tags
execute, the results of the first are overwritten by the subsequent cffile operation.

The following table describes the file upload status variables that are available after an upload:


 Variable                               Description

 attemptedServerFile                    Initial name that ColdFusion uses when attempting to save a file; for example, myfile.txt. (see
                                        "Resolving conflicting filenames" on page 1246).

 clientDirectory                        Directory on the client system from which the file was uploaded.

 clientFile                             Full name of the source file on the client system with the filename extension; for example,
                                        myfile.txt.

 clientFileExt                          Extension of the source file on the client system without a period; for example, txt (not .txt).

 clientFileName                         Name of the source file on the client system without an extension; for example, myfile.

 contentType                            MIME content type of the saved file; for example, image for image/gif.

 contentSubType                         MIME content subtype of the saved file; for example, gif for image/gif.

 dateLastAccessed                       Date that the uploaded file was last accessed.

 fileExisted                            Indicates (Yes or No) whether the file existed with the same path.

 fileSize                               Size of the uploaded file.

 fileWasAppended                        Indicates (Yes or No) whether ColdFusion appended the uploaded file to an existing file.

 fileWasOverwritten                     Indicates (Yes or No) whether ColdFusion overwrote a file.

 fileWasRenamed                         Indicates (Yes or No) whether the uploaded file was renamed to avoid a name conflict.

 fileWasSaved                           Indicates (Yes or No) whether ColdFusion saved the uploaded file.

 oldFileSize                            Size of the file that was overwritten in the file upload operation. Empty if no file was overwritten.

 serverDirectory                        Directory where the file was saved on the server.

 serverFile                             Full name of the file saved on the server with the filename extension; for example, myfile.txt.

 serverFileExt                          Extension of the file saved on the server without a period; for example, txt (not .txt).

 serverFileName                         Name of the file saved on the server without an extension; for example, myfile.

 timeCreated                            Date and time the uploaded file was created.

 timeLastModified                       Date and time of the last modification to the uploaded file.



Moving, renaming, copying, and deleting server files
With the cffile tag, you can create application pages to manage files on your web server. You can use the tag to move
files from one directory to another, rename files, copy a file, or delete a file.

The examples in the following table show static values for many of the attributes. However, the value of all or part of
any attribute in a cffile tag can be a dynamic parameter.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         1249
Using External Resources




 Action             Example code

 Move a file        <cffile action="move" source="c:\files\upload\KeyMemo.doc" destination="c:\files\memo\">


 Rename a file      <cffile action="rename" source="c:\files\memo\KeyMemo.doc"
                    destination="c:\files\memo\OldMemo.doc">

 Copy a file        <cffile action="copy" source="c:\files\upload\KeyMemo.doc"
                    destination="c:\files\backup\">

 Delete a file      <cffile action="delete" file="c:\files\upload\oldfile.txt">


This example sets the ReadOnly flag bit for the uploaded file:

<cffile action="Copy"
     source="c:\files\upload\keymemo.doc"
     destination="c:\files\backup\"
     attributes="ReadOnly">

Note: Ensure that you include the trailing slash (\) when you specify the destination directory. Otherwise, ColdFusion
treats the last element in the path as a filename. This rule only applies to copy actions.


Reading, writing, and appending to a text file
In addition to managing files on the server, you can use the cffile tag to read, create, and modify text files. As a result,
you can do the following things:

· Create log files. (You can also use cflog to create and write to log files.)

· Generate static HTML documents.

· Use text files to store information that can be incorporated into web pages.


Reading a text file
You can use the cffile tag to read an existing text file. The file is read into a local variable that you can use anywhere
in the application page. For example, you could read a text file and then insert its contents into a database, or you could
read a text file and then use one of the string replacement functions to modify the contents.


Read a text file
1 Create a ColdFusion page with the following content:

    <html>
    <head>
          <title>Read a Text File</title>
    </head>


    <body>
    Ready to read the file:<br>
    <cffile action="read"
          file="C:\inetpub\wwwroot\mine\message.txt"
          variable="Message">


    <cfoutput>
          #Message#
    </cfoutput>
    </body>
    </html>

2 Replace C:\inetpub\wwwroot\mine\message.txt with the location and name of a text file on the server.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                1250
Using External Resources




3 Save the file as readtext.cfm in the myapps directory under your web_root and view it in the browser.


Writing a text file on the server
You can use the cffile tag to write a text file based on dynamic content. For example, you could create static HTML
files or log actions in a text file.


Create a form in to capture data for a text file
1 Create a ColdFusion page with the following content:

    <html>
    <head>
          <title>Put Information into a Text File</title>
    </head>


    <body>
    <h2>Put Information into a Text File</h2>


    <form action="writetextfileaction.cfm" method="Post">
          <p>Enter your name: <input type="text" name="Name" size="25"></p>
          <p>Enter the name of the file: <input type="text" name="FileName" size="25">.txt</p>
          <p>Enter your message:
          <textarea name="message"cols=45 rows=6></textarea>
          </p>
          <input type="submit" name="submit" value="Submit">
    </form>
    </body>
    </html>

2 Save the file as writetextfileform.cfm in the myapps directory under your web_root.

Note: The form does not work until you write an action page for it (see the next procedure).


Write a text file
1 Create a ColdFusion page with the following content:

    <html>
    <head>
          <title>Write a Text File</title>
    </head>
    <body>
    <cffile action="write"
          file="C:\inetpub\wwwroot\mine\#Form.FileName#.txt"
          output="Created By: #Form.Name#
    #Form.Message# ">
    </body>
    </html>

2 Modify the path C:\inetpub\wwwroot\mine\ to point to a path on your server.

3 Save the file as writetextfileaction.cfm in the myapps directory under your web_root.

4 View the file writetextfileform.cfm in the browser, enter values, and submit the form.

    The text file is written to the location you specified. If the file exists, it is replaced.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1251
Using External Resources




Appending a text file
You can use the cffile tag to append additional text to the end of a text file; for example, when you create log files.


Append a text file
1 Open the writetextfileaction.cfm file.

2 Change the value for the action attribute from write to append so that the file appears as follows:

    <html>
   <head>
        <title>Append a Text File</title>
   </head>
   <body>
   <cffile action="append"
        file="C:\inetpub\wwwroot\mine\message.txt"
        output="Appended By: #Form.Name#">
   </body>
   </html>

3 Save the file as writetextfileaction.cfm in the myapps directory under your web_root.

4 View the file in the browser, enter values, and submit the form.

   The appended information displays at the end of the text file.


Using cfdirectory
Use the cfdirectory tag to return file information from a specified directory and to create, delete, and rename
directories. When listing directory contents or deleting a directory, you can optionally use the recurse attribute to
access or delete all subdirectories.

As with the cffile tag, you can disable cfdirectory processing in the ColdFusion Administrator. For details on the
syntax of this tag, see the CFML Reference.


Returning file information
When you use the action="list" attribute setting, the cfdirectory returns a query object as specified in the name
attribute. The name attribute is required when you use the action="list" attribute setting. This query object contains
result columns that you can reference in a cfoutput tag, using the value specified in the name attribute:

name Directory entry name.

directory Directory containing the entry.

size Directory entry size.

type File type: File or Dir.

dateLastModified Date an entry was last modified.

attributes (Windows only) File attributes, if applicable.

mode (UNIX only) The octal value representing the permissions setting for the specified directory.

Note: ColdFusion supports the ReadOnly and Hidden values for the attributes attribute for cfdirectory sorting.

Depending on whether your server is on a UNIX system or a Windows system, either the Attributes column or the
Mode column is empty. Also, you can specify a filename in the filter attribute to get information on a single file.

The following procedure describes how to create a ColdFusion page in which to view directory information.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1252
Using External Resources




View directory information
1 Create a ColdFusion page with the following content:

   <html>
   <head>
        <title>List Directory Information</title>
   </head>


   <body>
   <h3>List Directory Information</h3>
   <cfdirectory
        directory="c:\inetpub\wwwroot\mine"
        name="mydirectory"
        sort="size ASC, name DESC, datelastmodified">


   <table cellspacing=1 cellpadding=10>
   <tr>
        <th>Name</th>
        <th>Size</th>
        <th>Type</th>
        <th>Modified</th>
        <th>Attributes</th>
        <th>Mode</th>
   </tr>
   <cfoutput query="mydirectory">
   <tr>
        <td>#mydirectory.name#</td>
        <td>#mydirectory.size#</td>
        <td>#mydirectory.type#</td>
        <td>#mydirectory.dateLastModified#</td>
        <td>#mydirectory.attributes#</td>
        <td>#mydirectory.mode#</td>
   </tr>
   </cfoutput>
   </table>


   </body>
   </html>

2 Modify the path C:\inetpub\wwwroot\mine so that it points to a directory on your server.

3 Save the file as directoryinfo.cfm in the myapps directory under your web_root and view it in the browser.


Using cfcontent
The cfcontent tag downloads files from the server to the client. You can use this tag to set the MIME type of the content
returned by a ColdFusion page and, optionally, define the name of a file for the current page to download. By default,
ColdFusion returns a MIME content type of text/html so that a web browser renders your template text as a web page.

As with the cffile and cfdirectory tags, you can disable processing in the ColdFusion Administrator.


About MIME types
A MIME type is a label that identifies the contents of a file. the browser uses the MIME type specification to determine
how to interact with the file. For example, the browser could open a spreadsheet program when it encounters a file
identified by its MIME content type as a spreadsheet file.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 1253
Using External Resources




A MIME content type consists of "type/subtype" format. The following are common MIME content types:

· text/html

· image/gif

· application/pdf


Changing the MIME content type with cfcontent
You use the cfcontent tag to change the MIME content type that returns to the browser along with the content
generated from your ColdFusion page.

The cfcontent tag has one required attribute, type, which defines the MIME content type returned by the current page.


Change the MIME content type with cfcontent
1 Create an HTML page with the following content:

   <h1>cfcontent_message.htm</h1>


   <p>This is a <em>test message</em> written in HTML.</p>
   <p>This is the <em>second paragraph</em> of the test message.
   As you might expect, it is also written in HTML.</p>

2 Save the file as cfcontent_message.htm in the myapps directory under your web_root.

   The ColdFusion file that you write in steps 3 through 7 calls this file.

3 Create a ColdFusion page with the following content:

   <html>
   <head>
   <title>cfcontent Example</title>
   </head>


   <body>
   <h3>cfcontent Example</h3>


   <cfcontent
        type = "text/html"
        file = "C:\CFusion\wwwroot\myapps\cfcontent_message.htm"
        deleteFile = "No">
   </body>
   </html>

4 If necessary, edit the file = line to point to your myapps directory.

5 Save the file as cfcontent.cfm in the myapps directory under your web_root and view it in the browser.

   The text of the called file (cfcontent_message.htm) displays as normal HTML.

6 In cfcontent.cfm, change type = "text/html" to type = "text/plain".

7 Save the file and view it in the browser (refresh it if necessary).

   The text displays as unformatted text, in which HTML tags are treated as text.

The following example shows how the cfcontent tag can create an Excel spreadsheet that contains your data.


Create an Excel spreadsheet with cfcontent
1 Create a ColdFusion page with the following content:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                              1254
Using External Resources




   <!--- Use cfsetting to block output of HTML
   outside cfoutput tags. --->
   <cfsetting enablecfoutputonly="Yes">


   <!--- Get employee info. --->
   <cfquery name="GetEmps" datasource="cfdocexamples">
        SELECT * FROM Employee
   </cfquery>


   <!--- Set content type. --->
   <cfcontent type="application/msexcel">


   <!--- Suggest default name for XLS file. --->
   <!--- "Content-Disposition" in cfheader also ensures
   relatively correct Internet Explorer behavior. --->
   <cfheader name="Content-Disposition" value="filename=Employees.xls">


   <!--- Format data using cfoutput and a table.
              Excel converts the table to a spreadsheet.
              The cfoutput tags around the table tags force output of the HTML when
              using cfsetting enablecfoutputonly="Yes" --->
   <cfoutput>
        <table cols="4">
              <cfloop query="GetEmps">
                   <tr>
                        <td>#Emp_ID#</td>
                        <td>#FirstName#</td>
                        <td>#LastName#</td>
                   </tr>
              </cfloop>
        </table>
   </cfoutput>

2 Save the file as employees_to_excel.cfm in the myapps directory under your web_root and view it in the browser.

   The data appears in an Excel spreadsheet.



Using Event Gateways

Adobe ColdFusion provides event gateways, which you can use when writing applications. You configure an event
gateway for an application and deploy the application.

To use event gateways, you should have a thorough knowledge of ColdFusion development concepts and practices,
including ColdFusion components (CFCs). To write applications for custom gateways that are not provided in
ColdFusion, you must also know the details of the event gateway you are using, including its requirements.


About event gateways
ColdFusion event gateways are ColdFusion elements that let ColdFusion react to or generate external events or
messages in an asynchronous manner. Event gateways let a ColdFusion application handle information that does not
come through an HTTP request. For example, you can use event gateways to handle instant messages, short messages
from mobile devices, or messages sent to a TCP/IP port.




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1255
Using External Resources




The event gateway mechanism has the following major features:

· ColdFusion event gateways do not require HTTP requests. ColdFusion developers can write ColdFusion gateway
   applications without using any CFM pages (just CFCs).

· ColdFusion CFCs can use event gateways to listen for and respond directly to external events.

· Event gateways operate asynchronously. A gateway typically gets a message and dispatches it for processing,
   without requiring or waiting for a response.

· ColdFusion developers can create event gateways to handle any type event that a Java application can receive.

ColdFusion includes several product-level event gateways, such as a gateway for the XMPP (Extensible Messaging and
Presence Protocol) instant messaging protocol. Adobe also provides the source for several example gateways, such as
a generalized socket gateway, that you can extend to handle your specific needs. You can also write your own gateways
in Java to handle other event or messaging technologies supported by the Java runtime or by third-party providers,
such as gateways for additional instant messaging protocols, gateways for specific ERP systems, or other protocols,
such as NNTP.


Using event gateways
Because event gateways provide a generalized asynchronous messaging mechanism, you can use them with many
kinds of event or messaging resources. For example, ColdFusion includes gateways (either product quality, or lighter
weight example gateways) for communicating between ColdFusion applications and the following types of resources:

· Mobile phones and other devices that support short messaging services (SMS)

· XMPP or IBM Sametime Instant message clients

· Java Sockets (which let your ColdFusion application communicate with TCP/IP-based devices and programs, such
   as Telnet terminal clients).

· Java Messaging Service (JMS) resources, such as storefront sales order handling systems.

Event gateways are not limited to sending or receiving information using communications protocols. For example,
ColdFusion includes an example event gateway that monitors changes to a directory and invokes a CFC method
whenever the directory changes. ColdFusion also includes an event gateway that lets a CFML application "call" a CFC
asynchronously and continue processing without getting a response from the CFC.

Just as you can create event gateways that serve many different event or messaging based technologies, you can write
many kinds of applications that use them. Just a few examples of possible gateway uses include the following.


Server to client push examples
· An application that sends an instant message (IM) or SMS text message to a person who can approve a purchase
   order, get a response, and mark the purchase order as approved or denied.

· A bot that notifies users through their preferred messaging method (mobile phone, instant messaging, or even e-

   mail) when watch list stock goes up, and offers to buy or sell the stock immediately.

· An application that authenticates web users by sending them an SMS message that includes code that they must to
   enter into the browser to proceed.


Client to server examples
· A menu-based SMS application that lets users get information from any of several web service data providers.
   ColdFusion includes an SMS menuing example int the gateways/cfc directory.

· An instant messaging application that takes messages from users to technical support and assigns and directs the
   messages to the most available support staff member. The application could also log the user ID and session, and
   you could use ColdFusion to generate usage reports.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1256
Using External Resources




· A directory lookup robot IM "buddy" that responds to messages chat contain an employee name with the
   employee's phone number or buddy ID.


Server to serve examples
· A JMS subsystem that publishes status updates that business intelligence systems consume.

· A system that monitors and publishes download events from a website.


Event gateway terms and concepts
This document uses the following terms when referring to event gateways:

Event A trigger that ColdFusion can receive from an external source. ColdFusion event gateways receive events.

Message The information provided by an event. In ColdFusion, a message is the data structure that the event gateway
receives when an event is triggered.

Event gateway Java code that receives events and sends them to and from ColdFusion application code. This
document uses the term event gateway, without the word type or instance, for the general concept of a ColdFusion
event gateway. Where the context makes the meaning obvious, the term can also mean event gateway type or event
gateway instance.

Event gateway type A specific event gateway implementation, represented by a Java class. Each event gateway type
handles messages belonging to a particular a communications method or protocol, such as short message service
(SMS), an instant messaging protocol, or Sockets. You generally have one event gateway type per communication
protocol. You configure each event gateway type on the Gateway Types page in the Event Gateways area in the
ColdFusion Administrator.

Event gateway instance A specific instance of an event gateway type class. You configure each event gateway instance
on the ColdFusion Gateway Instances page by specifying the event gateway type, an ID, the path to the event gateway
application CFC that uses this instance, and a configuration file (if needed for the selected event gateway type). You
can have multiple event gateway instances per event gateway type, for example, for different event gateway
applications.

Event gateway application One or more CFCs and any supporting CFM pages that handle events from an event
gateway instance and send messages using the event gateway instance. The event gateway application is not part of an
event gateway instance, but the code that is responsible for processing event messages to and from the instance.

Event gateway listener Code in an event gateway that receives events from an event source and passes them to the
ColdFusion gateway service for delivery to a CFML listener CFC.

Listener CFC A CFC that contains one or more methods that respond to incoming messages from one or more event
gateway instances. Part of an event gateway application.

ColdFusion gateway service The part of ColdFusion that provides underlying support for event gateways, including a
path between an event gateway instance and listener CFCs.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1257
Using External Resources




How event gateway applications work
The following diagram shows the architecture of ColdFusion event gateway applications:



   SMSC server                                                                  CFEvent        Listener
                     Event                        CFEvent
                              Event Gateway
     (for SMS                                                                  Message           CFC
                                 Instance
    messages )        Event                       CFEvent                                   Event Gateway
                                                                                              Application

                                                                                CFEvent        Listener
      Instant                                     CFEvent
                     Event    Event Gateway                                    Message           CFC
    messaging
                                 Instance                     ColdFusion                    Event Gateway
     provider                                     CFEvent
                      Event                                      Event                        Application
          .                          .
                                                                Gateway                            .
          .                          .                           Service                           .
          .                          .                                                             .

          .                          .
                                                                                               Listener
                                                                                CFEvent
                                                                                                 CFC
  Other message      Event                        CFEvent
                              Event Gateway
    generator /
                                 Instance                                                       CFM
     receiver         Event                       CFEvent                      Message
                                                                                                Page

                                                                                           Event Gateway
                                   Java                                                      Application
                                                             ColdFusion
                                                                                               CFML




How event gateways interact
Typically, a ColdFusion event gateway instance, a Java object, listens for events coming from an external provider. For
example, a general socket event gateway listens for messages on an IP socket, and an SMS event gateway receives
messages from an SMSC server.

Each event gateway instance communicates with one or more listener CFCs through the ColdFusion event gateway
service. The listener CFCs receive CFEvent object instances that contain the messages, process them, and can send
responses back to the event gateway, which can send the messages to the external resources.

Alternatively, a ColdFusion application can initiate a message by calling a ColdFusion function that sends the message
to the event gateway. The event gateway then forwards the message to an external resource, such as an instant
messaging server. A CFC in the application listens for any responses to the sent message.

Some event gateways can be one way: they listen for a specific event and send it to a CFC, or they get messages from a
ColdFusion function and dispatch it, but they do not do both. The example DirectoryWatcherGateway discussed in
"Example event gateways" on page 1271 listens for events only, and the asynchronous CFML event gateway receives
messages from CFML only. (You could even say that the directory watcher gateway doesn't listen for events; it creates
its own events internally by periodically checking the directory state.) For information on the asynchronous CFML
event gateway, see "Using the CFML event gateway for asynchronous CFCs" on page 1269.


Event gateway structure
Java programmers develop ColdFusion event gateways by writing Java classes that implement the
coldfusion.eventgateway.Gateway interface. ColdFusion event gateways normally consist of one or more threads that
listen for events from an event provider, such as a Socket, an SMSC server, or some other source. The event gateway
sends event messages to the ColdFusion event gateway service message queue, and provides a method that gets called
when an event gateway application CFC or CFM page sends an outgoing message.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1258
Using External Resources




The event gateway class can also do the following:

· Provide the ColdFusion application with access to a helper class that provides event gateway-specific services, such
    as buddy-list management or connection management.

· Use a file that specifies configuration information, such as IP addresses and ports, passwords, and other ID
    information, internal time-out values, and so on.


About developing event gateway applications
ColdFusion application developers write applications that use event gateways. The person or company that provides
the event gateway supplies gateway-specific information to the ColdFusion developer. This information must include
the structure and contents of the messages that the ColdFusion application receives and sends to the event gateway,
plus any information about configuration files or helper methods that the ColdFusion application could use.

The ColdFusion developer writes a CFC that listens for messages. Many event gateway types send messages to a
listener CFC method named onIncomingMessage. A minimal event gateway application could implement only this
single method. More complex event gateway types can require multiple CFC listener methods. For example, the
ColdFusion XMPP IM event gateway sends user messages to the onIncomingMessage CFC method, but sends
requests to add buddies to the onAddBuddyRequest CFC method.

Depending on the event gateway and application types, the event gateway application could include CFM pages or
CFC methods to initiate outgoing messages. The application also could use an event gateway-specific GatewayHelper
object to do tasks such as getting buddy lists in IM applications or getting the status of a messaging server.

The ColdFusion application developer also configures an event gateway instance in the ColdFusion Administrator,
and possibly in a configuration file. The ColdFusion Administrator configuration information specifies the listener
CFC that handles the messages from the event gateway and other standard event gateway configuration details. The
configuration file, if necessary, contains event gateway type-specific configuration information.


Event gateway facilities and tools
ColdFusion provides several features and tools for developing and deploying event-handling applications, these
including the following:

· Standard event gateways.

· Development tools and example code.

· A gateway directory structure configured for use by custom event gateways and event gateway applications. This
    directory also contains the example code.

· An event gateway-specific log file

· Three pages in the ColdFusion Administrator for managing event gateways.


Standard event gateways
Adobe provides several event gateways as part of ColdFusion. These event gateways support the following messaging
protocols:

SMS (Short Message Service) A system designed for exchanging short, often text, messages with wireless devices, such
as mobile phones or pagers. For detailed information on using the SMS event gateway, see "Using the SMS Event
Gateway" on page 1292.

XMPP (Extensible Messaging and Presence Protocol): An open, XML-based protocol for instant messaging. For
detailed information on using the XMPP event gateway, see "Using the Instant Messaging Event Gateways" on
page 1276.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1259
Using External Resources




IBM Lotus Instant Messaging (commonly referred to as Lotus Sametime)          The IBM product for real-time
collaboration. For detailed information on using the Lotus Sametime event gateway, see "Using the Instant Messaging
Event Gateways" on page 1276.

ColdFusion also provides an event gateway, the CFML asynchronous event gateway, that lets a CFML application
invoke a CFC method asynchronously. This event gateway does not follow the model of providing a mechanism for
exchanging messages with resources outside ColdFusion. Instead, it provides a one-way path for invoking CFCs when
an application does not require (indeed, cannot receive) a return value from the CFC. For detailed information on
using the CFML asynchronous event gateway, see "Using the CFML event gateway for asynchronous CFCs" on
page 1269.


Development tools and example code
ColdFusion provides the following tools and example code for developing your own event gateways and event gateway
applications:

· An SMS client (phone simulator) and a short message service center (SMSC) server simulator, for developing SMS
   applications without requiring an external SMS provider.

· Four sample event gateways with source code:

   · A template for an empty event gateway that contains a skeleton on which you can build your own event gateways

   · A TCP/IP socket event gateway that listens on a TCP/IP port

   · A directory watcher event gateway that monitors changes to the contents of a directory

   · A Java Messaging Service (JMS) gateway that acts as a JMS consumer or producer.

· Several sample applications, including the following:

   · A menu application that uses an inquiry-response drill-down menu to provide services such as weather reports
      and stock quotes.

   · A simple echo application that sends back the messages that it receives.

   · A temperature converter, an asynchronous logging application.

   · An application that returns employee phone number and other information.

   The chapters in this document use these example applications.

· JavaDoc documentation for the Java interfaces and classes that you use to create gateways.

For more information on these examples, see "Using the example event gateways and gateway applications" on
page 1271.


The ColdFusion gateway directory
The ColdFusion installation includes a cf_root\WEB-INF\cfusion\gateway directory on J2EE configurations, or
cf_root\gateway directory on server configurations. This directory contains all the code for ColdFusion example event
gateways and example event gateway applications, and example configuration files for use by standard ColdFusion
event gateways. You do not have to place your event gateways, event gateway application CFCs, or event gateway
configuration files in this directory, but ColdFusion is configured to find event gateways and CFCs that you place
there.

The following table lists the event gateway directory subdirectories, their purpose, and their initial contents. For more
information on using the example event gateways and applications, see "Using the example event gateways and
gateway applications" on page 1271.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                      1260
Using External Resources




Directory                Purpose

cfc                      Event gateway application CFCs. ColdFusion is installed with an Administrator Mapping between /gateway and
                         this cfc directory.

cfc/examples             Code for the ColdFusion sample applications.

config                   Configuration files for all ColdFusion event gateways, including standard ColdFusion event gateways, such as
                         SMS, and example event gateways, such as the directory watcher event gateway.

doc/api                  JavaDoc for the Gateway, and GatewayHelper interfaces, and the CFEvent, GatewayServices, and GenericGateway
                         classes that gateway developer use when writing gateways. This documentation is a subset of the information in
                         Gateway development interfaces and classes in the CFML Reference.

lib                      Executable code for example and user-developed event gateway classes. The ColdFusion class loader includes
                         this directory on its classpath and includes any JAR files that are in that directory on the class path. The
                         examples.jar file in this directory contains the class files for the DirectoryWatcherGateway, EmptyGateway, and
                         SocketGateway classes.

src/examples             Source code for the example event gateway classes that Adobe provides. Includes the EmptyGateway.java file
                         and the following subdirectories:

                         · socket: Socket gateway source files

                         · watcher: directory watcher gateway source files

                         · JMS: JMS gateway source files


The eventgateway.log file
Event gateways provided with ColdFusion log event gateway errors and events to the cf_root\WEB-
INF\cfusion\logs\eventgateway.log file on J2EE configurations, or the cf_root\logs\eventgateway.log file on server
configurations. ColdFusion includes methods that let any event gateway use this file. This log file can be useful in
debugging event gateways and event gateway applications.


ColdFusion Administrator event gateway pages
The ColdFusion Administrator includes a Gateways section with three pages for managing event gateways:

· Settings

· Gateway types

· Gateway Instances

The Settings page lets you enable and disable support for event gateways, specify the number of threads that
ColdFusion can devote to processing events, specify the maximum number events that ColdFusion can hold in its
event queue (which holds events that are waiting to be processed) and start the SMS test server.

The Gateway Types page lets you add, remove, and configure event gateway types by specifying a name, a Java class,
and startup time-out behavior.

Note: The gateway type name in the ColdFusion Administrator does not have to be the same as the gateway type that is
used in the gateway Java code and the CFEvent data structure; however, use the same name in both places for consistency.

The Gateway Instances page lets you add, remove, configure, start, and stop individual event gateway instances. You
configure an event gateway instance by specifying a unique ID, the gateway type, one or more listener CFC paths, a
configuration file (not required for all gateway types), and a startup mode (manual, automatic, or disabled).




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         1261
Using External Resources




Structure of an event gateway application
To develop an event gateway application, you create and use some or all of the following elements:

· One or more listener CFCs that handle any incoming messages and send any necessary responses.

· In some applications, ColdFusion pages that generate outgoing messages directly.

· An event gateway instance configuration in the ColdFusion Administrator. This configuration could require a
   separate event gateway configuration file.

· In some applications, a GatewayHelper object to provide access to additional features of the protocol or technology;
   for example, to manage instant messaging buddy lists.


The role of the listener CFC
All incoming event messages must be handled by one or more listener CFCs. You specify the listener CFCs when you
configure an event gateway in the ColdFusion Administrator. Specify at least one CFC in the administrator. Some
gateway types can use more than one CFC. By default, the ColdFusion event gateway service delivers events by calling
the onIncomingMessage method of the CFC.

The event gateway developer must inform the event gateway application developer of methods that the listener CFC
must implement (could be only the onIncomingMessage method) and of the structure and contents of the event
message data, contained in the CFEvent instance, that the listener CFC must handle. Outgoing messages have the same
event message data structure as incoming messages.

Many gateways let the listener CFCs send a response by calling the cfreturn function, but ColdFusion does not
require a return value. Listener CFCs can also use the SendGatewayMessage function, which provides more flexibility
than the cfreturn tag.


The role of ColdFusion pages
ColdFusion CFM pages cannot receive event messages. However, they can send messages using an event gateway.
Therefore, an event gateway application that initiates outgoing messages could use one or more SendGatewayMessage
functions to send the messages. An application that sends an SMS message to notify users when a package ships, for
example, could use the SendGatewayMessage function to send the notification.


The role of the ColdFusion Administrator
The Gateway Instances page in the ColdFusion Administrator associates a specific event gateway instance with one or
more listener CFCs that processes messages from the event gateway. It tells the ColdFusion event gateway service to
send messages received by the event gateway to the listener CFC. It also lets you specify a configuration file for the event
gateway instance and whether to start the event gateway instance (and therefore any responder application) when
ColdFusion starts. For more information on using the Administrator, see the ColdFusion Administrator online Help.


The role of the GatewayHelper object
A ColdFusion event gateway provides an information conduit: at its most basic, it receives and dispatches event
messages. In some cases, however, an event gateway must provide additional functionality. An instant messaging event
gateway, for example, must provide such services as managing buddies and providing status information. To support
such use, an event gateway can enable access to a GatewayHelper object. The event gateway developer writes a Java
class that provides the necessary utility routines as Java methods, and ColdFusion application developers can get an
instance of the class by calling the CFML GetGatewayHelper method. The application calls the GatewayHelper object
methods using normal ColdFusion object access techniques. The ColdFusion instant messaging event gateways and
the example socket event gateway provide GatewayHelper objects.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1262
Using External Resources




Configuring an event gateway instance
Before you develop or deploy an event gateway application, use the ColdFusion Administrator to configure an event
gateway instance that handles the event messages. You specify the following information:

· An event gateway ID to identify the specific event gateway instance. You use this value in the CFML
   GetGatewayHelper and SendGatewayMessage functions.

· The event gateway type, which you select from the available event gateway types, such as SMS or Socket.

· The absolute path to the listener CFC or CFCs that handles incoming messages. If you have multiple listener CFCs,
   enter the paths separated by commas. Specify absolute file paths, even if you place the CFCs in the ColdFusion
   gateway\cfc directory.

· A configuration file, if necessary for this event gateway type or instance.

· The event gateway start-up status; one of the following:

   Automatic Start the event gateway when ColdFusion starts.

   Manual Do not start the event gateway with ColdFusion, but allow starting it from the ColdFusion Administrator
   Event Gateways list.

   Disabled Do not allow the event gateway to start.


Developing an event gateway application
All event gateway applications handle information. They exchange event messages, and possibly other types of
information, with other resources. Event gateway applications require a listener CFC to handle events that are sent to
the event gateway. Event gateway applications can also use the following code elements:

· SendGatewayMessage CFML functions to send messages from outside the listener CFC (or, optionally, from the CFC)

· GatewayHelper objects

· The eventgateway log file


Event gateway application models
Event gateway applications follow one or both of the following models:

Responder applications Where event messages from external sources initiate a response from a ColdFusion listener CFC

Initiator applications Where a ColdFusion application generates event messages to send out using the event gateway

Unlike other ColdFusion applications, responder applications are request-free. They do not have CFM pages, just
CFCs, and they do not respond to HTTP requests. Instead, ColdFusion the event gateway service deliver the event
messages directly to the listener CFC, and the CFC listener method returns any response directly to the event gateway
service. Applications that allow mobile phone owners to get a news feed, check for text messages, or request other
forms of information follow this model.

Initiator applications are like most ColdFusion applications. At some point, ColdFusion executes a CFM page in
response to a request. (The ColdFusion Administrator Scheduled Tasks page can initiate the request.) ColdFusion
sends a message to the event gateway when the application calls a CFML SendGatewayMessage function. An
application that uses SMS to notify customers when orders have been shipped follows this model.


Sending information to the event gateway
A ColdFusion application can send an outgoing message to the event gateway in either of the following ways:

· In a cfreturn tag in the listener CFC listener method



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 1263
Using External Resources




· By calling the ColdFusion SendGatewayMessage function

The first method is useful to automatically respond to incoming messages. Some complex applications that respond
to incoming messages could use the SendGatewayMessage function either in place or in addition to the return value.

Some event gateway types also use a GatewayHelper object to send information to external resources. For example, the
ColdFusion XMPP and Lotus Sametime instant messaging event gateways provide a GatewayHelper object that can
manage buddy lists, and set configuration and status information on the instant messaging server. For more
information on the GatewayHelper object, see "Using the GatewayHelper object" on page 1268. For more information
on the instant messaging GatewayHelper object, see "Sample IM message handling application" on page 1282.

The example code in "Example event gateway CFC" on page 1266 shows the use of a listener return value, and indicates
how event gateways can require different data in the return structure to send equivalent messages.


Developing event gateway listener CFCs
The listener CFC responds to event gateway messages. The listener CFC uses, at a minimum, the following basic
software elements:

· One or more listener methods

· CFEvent structures that contain the messages

Listener CFCs can use ColdFusion persistent scopes to store data that must be preserved over multiple CFC
invocations or shared with other CFML elements.


Listener methods
The ColdFusion event gateway service calls one or more listener methods in the CFC to process incoming messages.
The number of listener methods that you must write and their names depends on the event gateway. For example, the
ColdFusion SMS event gateway requires a single listener method, which is typically named onIncomingMessage. (You
can change the SMS event gateway listener method name in the event gateway configuration file.) The ColdFusion
XMPP IM event gateway expects the listener CFC to have five methods: onIncomingMessage, onAddBuddyRequest,
onAddBuddyResponse, onBuddyStatus, and onIMServerMessage. By default, if the event gateway does not specify
the method name, ColdFusion calls the onIncomingMessage method of the listener CFC. For the sake of consistency,
Adobe recommends you to use the onIncomingMessage method for any event gateway with a single listener method.

The listener method does the following:

1 Takes a single parameter, a CFEvent structure.

2 Processes the contents of the instance as required by the application.

3 Optionally, returns an outgoing message to the event gateway in a cfreturn tag. It can also send a message back
   to the event gateway by calling the ColdFusion SendGatewayMessage function.

The following code shows a listener CFC with an onIncomingMessage method that echoes a message back to the
Socket event gateway that sent it. It contains the minimum code required to process an incoming message and respond
to the sender using the socket gateway.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         1264
Using External Resources




<cfcomponent displayname="echo" hint="echo messages from the event gateway">
      <cffunction name="onIncomingMessage" output="no">
            <cfargument name="CFEvent" type="struct" required="yes">
            <!--- Create a return structure that contains the message. --->
            <cfset retValue = structNew()>
            <cfset retValue.DestinationID = arguments.CFEvent.OriginatorID>
            <cfset retValue.MESSAGE = "Echo: " & arguments.CFEvent.Data.MESSAGE>
            <!--- Send the return message back. --->
            <cfreturn retValue>
      </cffunction>
</cfcomponent>

Other event gateways require different fields in the return structure. For example, to echo a message using the SMS
event gateway, you use the following lines to specify the return value:

<cfset retValue.command = "submit">
<cfset retValue.sourceAddress = arguments.CFEVENT.gatewayid>
<cfset retValue.destAddress = arguments.CFEVENT.originatorid>
<cfset retValue.ShortMessage = "Echo: " & arguments.CFEvent.Data.MESSAGE>


The CFEvent structure
The ColdFusion event gateway service passes a CFEvent structure with information about the message event to the
listener method. The following table describes the structure fields:


 Field                   Description

 GatewayID               The event gateway that sent the event; the value is the ID of an event gateway instance configured on the
                         ColdFusion Administrator Gateway Instances page. If the application calls the SendGatewayMessage function
                         to respond to the event gateway, it uses this ID as the function's first parameter.

 Data                    A structure containing the event data, including the message. The Data structure contents depend on the event
                         gateway type.

 OriginatorID            The originator of the message. The value depends on the protocol or event gateway type. Many event gateways
                         require this value in response messages to identify the destination of the response. Identifies the sender of the
                         message.

 GatewayType             The type of event gateway, such as SMS. An application that can process messages from multiple event gateway
                         types can use this field. This value is the gateway type name that the event Gateway class specifies. It is not
                         necessarily the same as the gateway type name in the ColdFusion Administrator.

 CFCPath                 The location of the listener CFC. The listener CFC does not require this field.

 CFCMethod               The listener method that ColdFusion invokes to process the event. The listener CFC does not require this field.

 CFCTimeout              The time-out, in seconds, for the listener CFC to process the event request. The listener CFC does not require this
                         field.


When a ColdFusion application responds to an event gateway message, or sends a message independently, it does not
use a CFEvent structure. However, the ColdFusion event gateway service creates a Java CFEvent instance with the
message data before calling the outgoingMessage method of the event gateway.


Using persistent scopes in listener CFCs
ColdFusion listener CFCs can use the Application, Client, and Session persistent scopes.

Because incoming event gateway messages are not associated with HTTP requests, ColdFusion uses different session
and client IDs for interactions initiated by these events than for CFM Page requests, as follows:




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1265
Using External Resources




 Identifier               Structure

 Session ID               gatewayType_gatewayID_originatorID

 cfid                     originatorID

 cftoken                  gatewayType_gatewayID


The gatewayID value is the event gateway ID that you set in the ColdFusion Administrator, and gatewayType and
originatorID are the values that the event gateway sets in the CFEvent instance for an incoming message.


Application scope
The Application scope lets the CFC share data with any ColdFusion page or CFC that uses the same application name.
This way, a listener CFC can use the same Application scope as CFML pages that send messages. Also, you can place
multiple listener CFCs in a single directory and have them share an Application.cfc or Application.cfm file and
application name.

As with all ColdFusion code, use the Application.cfc This.name variable or the cfapplication tag to set the
application name. The listener CFC can use an Application.cfc or Application.cfm file if the CFC is in a directory that
is in or under one of the following places:

· the ColdFusion web root

· a directory that is in the ColdFusion Administrator Mappings list.

The ColdFusion installer creates a mapping in the ColdFusion Administrator for the gateway\cfc directory.


Client scope
The Client scope can store long-term information associated with the ID of the message sender. For example, it can
store information about an IM buddy.

To use Client variables across multiple connections, your gateway type must use the same client ID for all interactions
with a particular client. For many technologies and gateways, such as the IM and SMS gateways, this requirement is
not an issue.

Note: To use Client scope variables with gateways, you must store the Client scope variables in a data source or the
registry. You cannot store the variables in cookies, because gateways do not use cookies.


Session scope
The Session scope can store information required across multiple interactions. For example, an interactive IM or SMS
application that uses a drill-down menu to select a service can store the information about the menu selections in the
Session scope.

Event gateway sessions terminate when they time out. Because the identifiers for event sessions and clients differ from
request-driven session and client identifiers, you cannot use the same Session or Client scope on a standard CFM page
that sends an outgoing message and in a listener CFC that handles an incoming response to that message.

For an example of using the Session scope, see the example Menu application in the gateway\cfc\examples\menu
directory.

Note: ColdFusion cannot create a session if an initiator application uses a SendGatewayMessage method to start an
interaction with a client, such as an SMS user. In this case, the sending code must keep track (for example, in a database)
of the messages it sends and their destinations. When a response event arrives, it can look up the originatorID to
determine whether it was in response to an outgoing message.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1266
Using External Resources




Debugging event gateway CFCs
When an event gateway CFC responds to an event, it cannot display debugging information in the response page, as
CFM pages do. As a result, many of the normal ColdFusion debugging techniques, including the cfdump tag, are not
available. When you develop event gateway CFCs, consider the following debugging techniques:

· Place trace variables in the Application scope. These variables persist, and you can specify an application name for
   your CFC (see "Application scope" on page 1265). You can inspect the Application scope contents, including your
   trace variables, in any CFML page that has the same application name as your CFC.

· Use cflog tags to help you trace any errors by logging significant events to a file. Also, carefully inspect the
   eventgateway.log and exceptions.log files that ColdFusion maintains. For more information on using the
   eventgateway.log file, see "The eventgateway.log file" on page 1260.

· You can simulate responses from CFCs to the event gateway by using the SendGatewayMessage function in a CFM
   page. The function's message parameter should contain the information that the CFC would place in its return
   variable.

· If you run ColdFusion from the command line, you can use the Java System.out.println method to write
   messages to the console window, as the following code shows:

   <cfscript>
         sys = createObject("java", "java.lang.System");
         sys.out.println("Debugging message goes here");
   </cfscript>

Note: You do not have to restart the event gateway instance when you change a CFC. ColdFusion automatically uses the
updated CFC when the next event occurs.


Example event gateway CFC
The following code shows a temperature scale converter tool that can work with any of several event gateways: SMS,
XMPP, Lotus Sametime, or the example Socket event gateway. Users enter a string that consists of the temperature
scale (F, Fahrenheit, C, or Celsius), a comma, and a temperature on their device. The CFC converts Celsius to
Fahrenheit or Fahrenheit to Celsius, and returns the result.

This example shows how a responder event gateway application can work, and illustrates how different event gateway
types require different outgoing message formats:

<cfcomponent displayname="tempconverter" hint="Convert temperatures between
     Celsius and Fahrenheit">


<cffunction name="onIncomingMessage" output="no">
     <cfargument name="CFEvent" type="struct" required="yes">
     <!--- Standard error message giving the correct input format. --->
     <cfset var errormsg = "Please enter scale, integer where scale is F or C,
           for example:F, 32">


     <!--- Get the message. --->
     <cfset data=cfevent.DATA>
     <cfset message="#data.message#">
     <!--- Where did it come from? --->
     <cfset orig="#CFEvent.originatorID#">


     <!--- Process the input, generate a message with the new temperature. --->
     <!--- Input format is: degrees, temperature. --->
     <cfif listlen(message) eq 2>
           <cfif (listgetat(message,1) IS "F") OR
                     (listgetat(message,1) IS "Fahrenheit") OR




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                       1267
Using External Resources




                     (listgetat(message,1) IS "C") OR
                     (listgetat(message,1) IS "Celsius")>
                <cfset scale=listgetat(message,1)>
                <cfif isNumeric(listgetat(message,2))>
                     <cfset temperature=listgetat(message,2)>
                     <cfswitch expression="#scale#">
                         <cfcase value="F, Fahrenheit">
                            <cfset retmsg = temperature & " degrees Fahrenheit is "
                                 & (temperature-32.0) * (5.0/9.0) & " degrees Celsius">
                         </cfcase>
                         <cfcase value="C, Celsius">
                            <cfset retmsg = temperature & " degrees Celsius is "
                                 &(temperature * 9.0/5.0) + 32 & " degrees Fahrenheit">
                         </cfcase>
                     </cfswitch>
                <cfelse>
                     <cfset retmsg=errormsg>
                </cfif>
          <cfelse>
                <cfset retmsg=errormsg>
          </cfif>
    <cfelse>
          <cfset retmsg=errormsg>
    </cfif>


    <!--- Fill the return value as required for the event gateway type. --->
    <cfif arguments.CFEVENT.GatewayType is "Socket">
          <cfset retValue = structNew()>
          <cfset retValue.MESSAGE = retmsg>
          <cfset retValue.originatorID = orig>
    <cfelseif (arguments.CFEVENT.GatewayType is "Sametime") OR
                (arguments.CFEVENT.GatewayType is "XMPP")>
          <cfset retValue = structNew()>
          <cfset retValue.MESSAGE = retmsg>
          <cfset retValue.BuddyID = arguments.CFEVENT.DATA.SENDER>
          <cfset retValue.originatorID = orig>
    <cfelseif arguments.CFEVENT.GatewayType is "SMS">
          <cfset retValue = structNew()>
          <cfset retValue.command = "submit">
          <cfset retValue.sourceAddress = arguments.CFEVENT.gatewayid>
          <cfset retValue.destAddress = arguments.CFEVENT.originatorid>
          <cfset retValue.shortMessage = retmsg>
    </cfif>


    <!--- Send the return message back. --->
    <cfreturn retValue>


</cffunction>
</cfcomponent>


Sending a message using the SendGatewayMessage function
The SendGatewayMessage function has the following format:




                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1268
Using External Resources




SendGatewayMessage(gatewayID, messageStruct)

· The gatewayID parameter must be the gateway ID specified in the ColdFusion Administrator for the event gateway
   instance that sends the message.

· The messageStruct parameter is a structure whose contents depends on the requirements of the outgoingMessage
   method of the event gateway, and possibly the recipient application. For example, in addition to any message, the
   structure could include a destination identifier.

The CFEvent instance passed to the event gateway contains these two parameters in the GatewayID and Data fields;
the remaining fields are empty.

The following example sends a message to a logging CFC, which logs information to a file. If the SendGatewayMessage
function returns "OK", the example code displays a message. The code uses an instance of the asynchronous CFML
event gateway named Asynch Logger. The props variable used in the messageStruct parameter has two entries, the
destination file and the message to log.

<cfscript>
     status = "No";
     props = structNew();
     props.Message = "Replace me with a variable with data to log";
     status = SendGatewayMessage("Asynch Logger", props);
     if (status IS "OK") WriteOutput("Event Message ""#props.Message#"" has been sent.");
</cfscript>

Note: To see the code for the CFC that logs the information, see "Using the CFML event gateway for asynchronous CFCs"
on page 1269.


Using the GatewayHelper object
The ColdFusion GetGatewayHelper function tells ColdFusion to create and initialize a Java GatewayHelper object
that provides event gateway-specific helper methods and properties. To use this function, the event gateway must
implement a GatewayHelper class. For example, an instant messaging event gateway could make buddy list
management methods available in a GatewayHelper object.

The ColdFusion GetGatewayHelper function takes a single parameter, the ID of the event gateway instance that
provides the helper, and returns a GatewayHelper Java object. The parameter value must be the gateway ID for the
instance that is specified in the ColdFusion Administrator. If you do not want to hard-code an ID value in the
application (for example, if your listener CFC can respond to multiple event gateway instances), get the gateway ID
from the CFEvent structure of the first incoming message.

The CFML code accesses the GatewayHelper object's methods and properties using standard ColdFusion Java object
access techniques (see "Integrating J2EE and Java Elements in CFML Applications" on page 1125). For example, if an
event gateway's GatewayHelper class includes an addBuddy method that takes a single String parameter, you could use
the following code to get the ColdFusion XMPP or Sametime gateway GatewayHelper object and add a buddy to the
buddies list:

<cfscript>
     myHelper = GetGatewayHelper(myGatewayID);
     status = myHelper.addBuddy("jsmith23", "Jim Smith", "support");
</cfscript>


Using the event gateway error log file
When a standard ColdFusion event gateway encounters an error that does not prevent the event gateway from
continuing to process, it logs it to the eventgateway.log file in the ColdFusion logs directory. Other event gateways can
also to log information in this file, or to other application-specific files in the logs directory.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1269
Using External Resources




The standard ColdFusion event gateways log errors in interaction with any messaging server, errors in messages sent
by the ColdFusion application, and recoverable errors in event gateway operation. The event gateways also log
informational status messages for significant normal events, including event gateway initialization and restarts.

ColdFusion event gateway messages in the eventgateway.log file normally have the following format:

gatewayType (gatewayID) message body

When you are developing an event gateway application, you can use the ColdFusion Log viewer to inspect the
eventgateway.log file and filter the display by using the gateway type and possibly the gateway ID as keywords. By
selecting different severity levels, you can get a good understanding of errors and possible inefficiencies in your
application and event gateway operation.


Deploying event gateways and applications
To deploy an event gateway application in a ColdFusion server, install your listener CFC and configure a gateway
instance that uses the CFC.


Deploy an event gateway application
1 Ensure that the ColdFusion Administrator is configured with the required event gateway type. If it is not, deploy
   the event gateway type (see "Deploying an event gateway" on page 1333).

2 If the event gateway type requires a configuration file, ensure that a valid file exists in the gateway\config directory.
   Some event gateways could be designed to let multiple event gateway instances share a configuration file. Others
   could require a separate file for each event gateway instance.

3 Install the event gateway application listener CFC and any other application components. ColdFusion provides a
   cf_root\gateways\cfc directory as a convenient location for these CFCs, and includes a mapping in the ColdFusion
   Administrator page for that directory. However, ColdFusion does not require you to install the listener CFC in this
   directory.

4 Configure an event gateway instance on the Gateway Instances page of the Event Gateways section in the
   ColdFusion Administrator (see "Configuring an event gateway instance" on page 1262)


Using the CFML event gateway for asynchronous CFCs
The ColdFusion CFML event gateway lets CFML code send a message to CFC methods asynchronously. This event
gateway lets you initiate processing by a CFC method without waiting for it to complete or return a value. Possible uses
for asynchronous CFCs that you access using this event gateway include the following:

· Reindexing a Verity collection with new information without delaying an application, for example, when a user
   uploads a new file

· Logging information, particularly if significant amount of data must be logged

· Running batch processes that could take a substantial amount of time to complete

Because asynchronous CFCs run independently of a request, they do not provide feedback to the user. Save any results
or error information to a file, data source, or other external resource.

By default, ColdFusion delivers the message to a CFC method named onIncomingMessage. You can specify any
method name, however, in the SendGatewayMessage method's data parameter.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                       1270
Using External Resources




CFML event gateway data structure
The structure that you use in the CFML SendGatewayMessage function can include two types of fields:

· The structure can include any number of fields with arbitrary contents for use in by the CFC.

· Several optional fields can configure how the gateway delivers the information to the CFC.

The CFML gateway looks for the following optional fields, and, if they exist, uses them to determine how it delivers
the message. Do not use these field names for data that you send to your CFC method.


 Field                    Use

 cfcpath                  Overrides the CFC path specified in the ColdFusion Administrator. This field lets you use a single gateway
                          configuration in the ColdFusion Administrator multiple CFCs.

 method                   Sets the name of the method to invoke in the CFC. The default method is onIncomingMessage. This field lets
                          you use a single gateway configuration in the ColdFusion Administrator for a CFC that has several methods.

 originatorID             Sets the originatorID field of the CFEvent object that ColdFusion delivers to the CFC. The default value is
                          CFMLGateway.

 timeout                  Sets the time-out, in seconds, during which the listener CFC must process the event request and return before
                          ColdFusion gateway services terminates the request. The default value is the Timeout Request value set on the
                          Server Settings page in the ColdFusion Administrator. Set this value if a request could validly take longer to
                          process than the default timeout; for example, if the request involves a long processing time.



Using the CFML gateway
The following procedure describes how to use an asynchronous CFC that has a single, onIncomingMessage method.


Use an asynchronous CFC
1 Create a CFC with an onIncomingMessage method. Place the CFC in an appropriate directory for your
    application. For example, you can place it in the cf_root\WEB-INF\cfusion\gateway\cfc directory on J2EE
    configurations, in the cf_root\gateway\cfc directory on server configurations, or in a subdirectory of these
    directories. ColdFusion is installed with mappings to these cfc gateway directories.

    The onIncomingMessage method must take a CFEvent structure that contains input information in its Data field,
    and processes the contents of the Data field as needed.

2 Use the Gateway Instances page in the ColdFusion Administrator to add an instance of the CFML event gateway
    type. Specify the following:

    · A unique Gateway ID.

    · The path to the CFC that you created in step 1.

    · The startup mode. Select Automatic startup mode to start the event gateway when ColdFusion starts up.

    · Do not specify a configuration file.

3 Start the event gateway instance.

4 Write CFML code that uses SendGatewayMessage functions to send messages in structures to the event gateway
    instance ID that you specified in step 2. The SendGatewayMessage function returns true if the gateway successfully
    queues the message in the ColdFusion Gateway Service; false, otherwise. It does not ensure that the CFC receives
    or processes the message.

5 Run your CFML application.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1271
Using External Resources




Example: logging messages
The following asynchronous CFML event gateway CFC uses the cflog tag to log a message to a file in the ColdFusion
logs directory. The CFC takes a message with the following fields:

· file   The name of the file in which to place the message. The default value is defaultEventLog.

· type    The cflog type attribute to use. The default value is info.

· message      The message text.

<cfcomponent>
     <cffunction name="onIncomingMessage" output="no">
          <cfargument name="CFEvent" type="struct" required="yes">
          <cfscript>
                if (NOT IsDefined("CFEvent.Data.file")) {
                         CFEvent.Data.file="defaultEventLog"; }
                if (NOT IsDefined("CFEvent.Data.type")) {
                     CFEvent.Data.type="info"; }
          </cfscript>
          <cflog text="#CFEvent.Data.message#"
                     file="#CFEvent.Data.file#"
                type="#CFEvent.Data.type#"
                thread="yes"
                date="yes"
                time="yes"
                application="yes">
     </cffunction>
</cfcomponent>

The following minimal CFML page tests the event gateway:

Sending an event to the CFML event gateway that is registered in the
     ColdFusion Administrator as Asynch Logger.<br>
<cfscript>
     status = false;
     props = structNew();
     props.Message = "Replace me with a variable with data to log";
     status = SendGatewayMessage("Asynch Logger", props);
     if (status IS True) WriteOutput('Event Message "#props.Message#" has been sent.');
</cfscript>



Using the example event gateways and gateway applications
ColdFusion provides several example event gateways and applications in the cf_root\WEB-INF\cfusion\gateway
directory on J2EE configurations or the cf_root\gateway directory on server configurations. These gateways provide
example code that you can examine or use in developing your gateways. They are intended as examples only, and are
not complete, product-quality, implementations.


Example event gateways
The gateway\src\examples directory and its subdirectories include the sources for the example event gateways.
Compiled versions are located in the gateway\lib\examples.jar file. This document briefly describes the event gateways
and their functions. For more detailed information, see the code and comments in the files.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1272
Using External Resources




EmptyGateway
The EmptyGateway.java file contains an event gateway template that you can use as a skeleton for creating your own
event gateway. For more information on this class, and on creating new event gateways, see "Creating Custom Event
Gateways" on page 1320


SocketGateway
The SocketGateway event gateway listens on a TCP/IP port. Therefore, you can use this gateway for applications that
send and respond to messages using TCP/IP-based protocols such as Telnet, or for applications that send messages
between sockets. For example, a simple gateway application that responds to messages from a Telnet terminal client
without supporting the full Telnet protocol.

Note: The ColdFusion Administrator uses Socket as the gateway type name for the SocketGateway class.

The SocketGateway.java file defines two classes: SocketGateway, the event gateway, and SocketHelper, a
GatewayHelper class. The Source file is located in the gateway\src\examples\socket directory.

SocketGateway Listens on a TCP/IP port. This event gateway is multi-threaded and can handle multiple clients
simultaneously. It can send outgoing messages to existing clients, but cannot establish a link itself.

By default, the SocketGateway class listens on port 4445, but you can specify the port number in a configuration file.
The file should contain a single line in the following format:

port=portNumber

SocketHelper A GatewayHelper class with the following methods:

getSocketIDs() returns an array containing the socket IDs of all Java sockets that are open. The event gateway opens
a socket for each remote client.

killSocket(String socketid)removes the specified socket. Returns a Boolean success indicator.


DirectoryWatcherGateway
The DirectoryWatcherGateway event gateway sends events to the listener CFC when a file is created, deleted, or
modified in a directory. The watcher runs in a thread that sleeps for an interval specified in the configuration file, and
when the interval has passed, checks for changes since the last time it was awake. If it finds added, deleted, or changed
files, it sends a message to a listener CFC. You can configure separate CFCs for add, delete, and change events, or use
a single CFC for all events. The source for this event gateway is located in the gateway/src/examples/watcher directory.

Note: The ColdFusion Administrator uses DirectoryWatcher as the gateway type name for the
DirectoryWatcherGateway class.


Configuration file
This event gateway requires a configuration file, consisting of lines in the following format:

directory=C:/temp

Note: If you use backward slash characters (\) as directory separators in Windows the file paths, escape them by using
double slashes, as in C:\\temp. You can use forward slashes (/) as the directory separator on all operating systems,
including Windows.

Note: When you specify filename extensions in the Directory Watcher configuration file, do not include the period,
instead use a comma, for example, doc,txt.

The configuration file can have comment lines, preceded by a number sign (#). If you omit a property or comment it
out, ColdFusion uses the default value. If you specify a property with no value, ColdFusion sets an empty property.
The configuration file can define the following values:



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     1273
Using External Resources




 Property              Req/Opt        Description

 directory             Required       Path to the directory to watch.

 recurse               Optional       Whether to check subdirectories. The default value is no.

 extensions            Optional       Comma-delimited list of extensions to watch. The event gateway logs only changed files with these
                                      extensions. An asterisk (*) indicates all files. The default value is all files.

 interval              Optional       Number of milliseconds between the times that the event gateway checks the directory. The
                                      default value is 60 seconds.

 addFunction           Optional       Name of the function to call when a file is added. The default value is onAdd.

 changeFunction        Optional       Name of the function to call when a file is changed. The default value is onChange.

 deleteFunction        Optional       Name of the function to call when a file is deleted. The default value is onDelete.


An example configuration file is located in the gateway\config\directory-watcher.cfg file.


CFC methods
When the directory contents change, the event gateway calls one of the following CFC listener methods, unless you
change the names in the configuration file:

· onAdd

· onChange

· onDelete

The CFEvent.Data field sent to the listener methods includes the following fields:


 Field                   Description

 TYPE                    Event type, one of ADD, CHANGE, DELETE.

 FILENAME                Absolute path from the system directory root to the file that was added, deleted, or changed.

 LASTMODIFIED            The date and time that the file was created or modified. This field is not included if the file was deleted.


The event gateway supports multiple listener CFCs and sends the event messages to all listeners. The event gateway is
one way; it watches for events and dispatches event information to a CFC, but it does not accept return values from
the CFC or input from SendGatewayMessage functions.

The directory watcher logs errors to the watcher.log file in the ColdFusion logs directory.


Example DirectoryWatcher application
The following code shows a simple directory watcher application. It enters a line in a log file each time a file is added,
deleted, or changed in the directory specified in the configuration file. ColdFusion includes the date and time that a
log entry is made. However, if the directory watcher monitors changes infrequently, for example once every minute or
more, the time in the log entry could differ from the time a file was added or changed, so the information includes the
time (but not date) for these actions.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 1274
Using External Resources




<cfcomponent>
<cffunction name="onAdd" output="no">
      <cfargument name="CFEvent" type="struct" required="yes">
      <cfset data=CFEvent.data>
      <cflog file="MydirWatcher" application="No"
            text="ACTION: #data.type#;FILE: #data.filename#;
            TIME: #timeFormat(data.lastmodified)#">
</cffunction>


<cffunction name="onDelete" output="no">
<cfargument name="CFEvent" type="struct" required="yes">
<cfset data=CFEvent.data>
<cflog file="MydirWatcher" application="No"
      text=" ACTION: #data.type#;FILE: #data.filename#">
</cffunction>


<cffunction name="onChange" output="no">
<cfargument name="CFEvent" type="struct" required="yes">
<cfset data=CFEvent.data>
<cflog file="MydirWatcher" application="No"
            text=" ACTION: #data.type#;FILE: #data.filename#;
            TIME: #timeFormat(data.lastmodified)#">
</cffunction>
</cfcomponent>


JMSGateway
The JMSGateway class acts as a Java Messaging Service consumer or producer. The source for this event gateway is
located in gateway/src/examples/JMS. The gateway requires a configuration file, which is in
gateway/config/jmsgateway.cfg. For full documentation of the configuration options, See the configuration file. The
ColdFusion Administrator lists the compiled gateway (which is included in the gateway\lib\examples.jar file) on the
Gateway Types page.

Note: The ColdFusion Administrator uses JMS as the gateway type name for the JMSGateway class.


Using the JMS Gateway as a consumer
The JMSGateway class creates a subscriber to the topic specified in the configuration file. The gateway consumes the
following types of messages:

· TextMessage

· BytesMessage containing raw UTF-8 text

The gateway passes the contents of the message to the configured CFC in the event structure, as follows:


 Field                   Contents

 data.id                 Message correlation ID

 data.msg                Text of the message

 gatewayType             Gateway type: JMS

 originatorID            Topic name from which the message was consumed




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         1275
Using External Resources




The listener CFC method must be named onIncomingMessage. If the CFC method does not send a message in
response, it should return a structure containing a status field with a value of OK or EXCEPTION. (In this case, The
gateway checks the return status field, but does not process these return values further.) To send a message, the CFC
method must return a structure as documented in the following section.


Using the JMS Gateway as a producer
To send a JMS message, the return value of your CFC method or the second, messageStruct, parameter to the
SendGatewayMessage function must be a structure with the following fields:


 Field                   Contents

 status                  Must be SEND.

 topic                   Name of the topic to publish the message to.

 id                      (Optional) The JMS correlation ID to associate with the message. The default is null.

 message                 Text of the message to publish.

 asBytes                 (Optional) How to publish the message:

                         · If omitted, no, or false, send the message as text.

                         · If any other value, send the message as byte-encoded UTF-8.


If you send the message in a SendGatewayMessage function, the function returns OK if the gateway sends the
message, or EXCEPTION if it fails to send the message.


ActiveMQ JMS event gateway
Apache ActiveMQ is a message broker that implements JMS. The source for this event gateway is located in
gateway/src/examples/ActiveMQ. For information about using the ActiveMQ JMS event gateway, see the
gateway\docs\ActiveMQDeveloperGuide.pdf file.


Menu example application
ColdFusion is installed with a menu-based responder application. The menu application is written to work with any
of the standard ColdFusion event gateways (SMS, XMPP, and Sametime) and with the Socket example event gateway,
and ColdFusion is preconfigured with an instance of the application that uses SMS, as follows:

· The Gateway Instances page in the ColdFusion Administrator includes a gateway instance for this application that
   uses the SMS gateway type.

· The gateway/cfc/examples/menu directory and its subdirectories include the CFML for the application

· The gateway/config/sms-test.cfg file is configured to use this application with the SMS client (phone simulator),
   and short message service center (SMSC) server simulator that are provided with ColdFusion.

The application presents users with a drill-down menu of tools that they can use, including a weather report, stock
information, status and configuration information, and language tools such as a dictionary.

The code for this application is relatively complex and is distributed among 13 files. The following brief description
provides an overview of how it works. To get a full understanding of how the application works, see the source code.

· The top level, menu, directory contains two files: Application.cfm and main.cfc.

· The Application.cfm file consists of a single cfapplication tag that enables session management and names the
   application. Session variables maintain the current state information of the session, such as the active menu, and so on.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1276
Using External Resources




· The main.cfc file contains the master CFC; the event gateway configuration in ColdFusion Administrator uses it as
   the listener CFC. The main CFC file processes CFEvent structures from the event gateway. It does the following:

   1 Inspects the gatewayType field to determine the rest of the structure contents. This check is necessary because
      different event gateways place the message in fields with different names.

   2 If a Session.menu variable does not exist, initializes the menu system. To do so, it calls methods in two other
      CFCs: menu and menunode. These two CFCs contain the menu system code.

   3 Calls the session.menu.process method to process the user input. This method can dispatch a message to an
      individual application for processing, if appropriate.

· The apps directory contains several CFCs. Each file contains the code for a single application, such as the weather
   report or dictionary lookup (definition.cfc).


Use the menu application with the Socket event gateway
1 On the Gateway Settings page in the ColdFusion Administrator, click the Start SMS Test Server button.

2 On the Gateway Instances page in the ColdFusion Administrator, start the SMS Menu App - 5551212 event gateway
   by clicking the green play button (third button from the left in the Actions column). If the Status does not say
   Running after a few seconds, click Refresh to check that the server started.

3 In the cf_root\WEB-INF\cfusion\bin directory on J2EE configurations or the cf_root\bin directory on server
   configurations, run the SMSClient.bat file (on Windows) or SMSClient.sh file (on UNIX or Linux) to start the SMS
   phone simulator. The simulator is preconfigured by default to "call" the default SMS event gateway configuration.

4 Enter any character by typing or by using the mouse to click the simulator keypad, and press Enter on your
   keyboard or click Send on the simulator.

5 The menu application responds with the top-level menu. Enter L for language tools such as a dictionary and
   thesaurus, S to get stock quotes or weather forecasts, or C to get information about the server. Press Enter on your
   keyboard or click Send on the simulator.

6 The application displays a submenu. For example, if you select S in step 5, the options are Q for a stock quote, W
   for weather, or B to go back to the previous menu. Enter your selection.

7 The application requests information such as a Zip code for the weather, stock symbol for a price, word for the
   dictionary, and so on. Enter and send the required information (or enter B to go back to the menu).

8 The application gets and displays the requested information. Depending on the application, you could also be
   prompted to enter M to get more. Enter M (if more information is available), another term, or B to return to the
   previous menu.

9 Continue by entering menu items and detailed information requests.

10 To exit, select File > Exit from the menu bar.



Using the Instant Messaging Event Gateways

You can develop an application that uses either of two instant message (IM) event gateway types provided with Adobe
ColdFusion: an IBM Lotus Sametime gateway, and an Extensible Messaging and Presence Protocol (XMPP) gateway.

Before you use the IM event gateways, become familiar with ColdFusion event gateway principles and programming
techniques (see "Using Event Gateways" on page 1254).




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                1277
Using External Resources




About ColdFusion and instant messages
ColdFusion includes two instant messaging gateway types: one for messaging using the XMPP protocol, and one for
IBM Lotus Instant Messaging (Sametime). These gateway types use identical interfaces for sending and receiving
messages and for managing the IM presence information and infrastructure. This documentation, therefore, refers to
IM gateways, and only describes the two types where differences exist.

The ColdFusion IM gateways act as IM clients and let you do the following:

· Send and receive instant messages.

· Send and respond to buddy or friend requests and manage buddy/friend information.

· Set and get status and other information.

· Receive and handle messages from the IM server.


About XMPP
XMPP (Extensible Messaging and Presence Protocol) is an open, XML-based protocol for instant messaging. It is the
core protocol of the Jabber Instant Messaging and Presence technology that the Jabber Software Foundation develops.
As of November 2004, four Internet Engineering Task Force (IETF) specifications (RFCs) defined XMPP, numbers
3920-3923. RFC 3920 covers the XMPP core, and 3921 covers instant messaging and presence. Numerous XMPP
servers and clients are available. ColdFusion supports the IETF XMPP protocol.

The following websites provide additional information about the XMPP protocol:

· Jabber Software Foundation: www.jabber.org/. This site includes information on available XMPP servers and
   clients.

· IETF has copies of the Internet standards for XMPP: www.ietf.org/rfc.html.

· The xmpp.org website was under development as of December 2004; at that time it included several useful links,
   including links to relevant specifications: www.xmpp.org/.


About IBM Lotus Instant Messaging (Sametime)
IBM Lotus Instant Messaging, commonly referred to as Lotus Sametime, is the IBM product for real-time
collaboration. For more information about this product, see www.lotus.com/sametime.

Note: In the Enterprise Edition, to use the Lotus Sametime event gateway, disable FIPS-140 Compliant Strong
Cryptography by adding the following to the JVM arguments in the ColdFusion Administrator:

-Dcoldfusion.disablejsafe=false


About IM application development and deployment
The following information introduces the ColdFusion IM application development tools and process, and discuss IM
messaging providers.


ColdFusion IM gateway classes
ColdFusion provides the following instant messaging gateway classes:

XMPPGateway The class for the XMPP event gateway type

SAMETIMEGateway The class for the IBM Lotus Instant Messaging event gateway

You implement your IM application by configuring a gateway instance in ColdFusion Administrator that uses one of
these gateway classes and creating a ColdFusion application that uses the gateway instance to communicate with an
instant messaging server.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  1278
Using External Resources




Application development and deployment process
The following is a typical process for developing and deploying an IM application:

1 Design your application.

2 Configure an IM event gateway instance to use an available XMPP or Lotus Sametime server.

3 Write your CFCs, CFM pages, and any other application elements.

4 Test your application using your XMPP or Lotus Sametime server and an appropriate client application.

5 Deploy the application (see "Deploying event gateways and applications" on page 1269).


How the IM event gateway and provider interact
Each IM event gateway instance has a single instant messaging ID. Establish the ID and its related password on the IM
server using server-specific tools, such as a standard instant messaging client. In ColdFusion, you set the ID, password, and
other gateway-specific information in a gateway configuration file, and you create a gateway instance that uses this file.

When you start the gateway, it logs on to the IM server with the ID and password, and receives and sends the messages
for the ID. The gateway sends incoming messages to a CFC, which you specify when you configure the gateway
instance in the ColdFusion Administrator. The gateway passes outgoing messages from this CFC and from other
CFML code to the IM server.

The IM event gateway also provides several helper methods for managing the gateway and its configuration
information.


Incoming message handling
You write the following ColdFusion CFC methods to handle incoming messages and requests from the IM event
gateway. These CFCs receive messages from the IM server and can respond to them by setting a return value.


 CFC method                        Message type

 onIncomingMessage                 Standard message from IM users.

 onAddBuddyRequest                 Requests from others to add the gateway ID to their buddy list.

 onAddBuddyResponse                Responses from others to requests from your gateway to add them to your buddy lists. Also used by
                                   buddies to ask to be removed from your list.

 onBuddyStatus                     Presence status messages from other users.

 onIMServerMessage                 Error and status messages from the IM server.


For more information on these methods, see "Handling incoming messages" on page 1280.


Outgoing message handling
Applications send outgoing instant messages using the CFML SendGatewayMessage method. Incoming message-
handling CFC methods can also send messages, including responses to requests from others to add the ColdFusion
gateway's ID to their buddy list. For more information on sending messages, see "Sending outgoing messages" on
page 1281.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  1279
Using External Resources




IMGatewayHelper methods
The ColdFusion IM gateway provides the IMGatewayHelper class, a gateway helper that you can access by calling the
CFML GetGatewayHelper function. The IMGatewayHelper class has methods that let you do the following:

· Get and set gateway configuration information and get gateway statistics

· Get and set the gateway online presence status

· Manage the gateway's buddy list

· Manage permissions for others to get information about the gateway status.

   For more information on using GatewayHelper methods, including lists of all the methods, see "Using the
   GatewayHelper object" on page 1287.


Configuring an IM event gateway
You provide IM-specific configuration information to the IM event gateway in a configuration file. You specify the
configuration file location when you configure the IM event gateway instance in the ColdFusion Administrator.
ColdFusion provides sample XMPP and Lotus Sametime event gateway configuration files in the cf_root\WEB-
INF\cfusion\gateway\config directory on J2EE configurations, and cf_root\gateway\config directory on server
configurations. The configuration file can have the following information.

Note: The default value in the table is the value the gateway uses if the configuration file omits the property, not the value
in the default configuration files.


 Property               Default value          Description

 userID                 none                   (Required) The IM user ID to use to connect to the IM server.

 password               none                   (Required) Password for the user.

 secureprotocol         none                   XMPP only.

                                               Required if you set securerequirement to true.

                                               The protocol to use for secure communications. The following values are valid:

                                               · TSL

                                               · SSL

 securerequirement      false                  XMPP only.

                                               Specifies whether the gateway must use secure communications. The following values
                                               are valid:

                                               · true

                                               · false

                                               If this value is true, specify a secureprotocol value, and connections succeed only if
                                               a secure connection is established.

 serverip               XMPP: jabber.org       Address of XMPP or Lotus Sametime server to which to send messages. Can be a server
                                               name or IP address.
                        Sametime:
                        stdemo3.dfw.ibm.com

 serverport             XMPP: 5222             Port on the server to which to send the messages.

                        Sametime:1533          If the XMPP secureprotocol parameter is set to SSL, specify 5223.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                       1280
Using External Resources




 Property               Default value            Description

 retries                -1                       Integer number of times to retry connecting to the IM server on gateway startup or if the
                                                 Gateway gets disconnected.

                                                 0 = do not to retry

                                                 -1 = try forever

 retryinterval          5                        Real number of seconds to wait between connection attempts. The minimum is 1
                                                 second.

 onIncomingMessageFu    onIncomingMessage        Name of CFC method to call to handle an incoming message. If you specify the property
 nction                                          without a value, such as "onIncomingMessageFunction=", the gateway does not send
                                                 this event to a CFC.

 onAddBuddyRequestFu    onAddBuddyRequest        Name of CFC method to call to handle an incoming buddy request. If you specify the
 nction                                          property without a value, the gateway does not send this event to a CFC.

 onAddBuddyResponseF    onAddBuddyResponse       Name of CFC method to call to handle an incoming response to a buddy request sent by
 unction                                         ColdFusion. If you specify the property without a value, the gateway does not send this
                                                 event to a CFC.

 onBuddyStatusFunction  onBuddyStatus            Name of CFC method to call to handle an incoming buddy status message, such as If you
                                                 specify the property without a value, the gateway does not send this event to a CFC.

 onIMServerMessageFun   onIMServerMessage        Name of CFC method to call to handle an incoming message method. If you specify the
 ction                                           property without a value, the gateway does not send this event to a CFC.


Note: If you do not have a CFC method to handle any of the event types, specify the corresponding property without a
value. Use the following entry in the configuration file, for example, if you do not have a method to handle
IMServerMessage events: onIMServerMessageFunction=


Handling incoming messages
The IM event gateway handles five types of messages, and your CFC must implement a listener method for each
message type. The following table describes the message-handling CFC methods and the messages they handle. It lists
the default CFC method names; however, you can change the names in the gateway configuration file.


 CFC method                          Description

 onIncomingMessage                   Standard message from an IM user. The application processes the message body appropriately; for
                                     example, it could display the message in an interface window.

                                     This method can return a response message to the sender.

 onAddBuddyRequest                   Request from another IM user to add your application's IM ID to their buddy list. The CFC must
                                     determine whether to accept or reject the request, or to take no action. An action is not always
                                     appropriate in cases where the request must be reviewed offline for approval and responses are
                                     sent at a later time.

                                     The CFC returns a message with the decision as a command value and optionally a text message
                                     specifying the reason. If you accept the request, the requestor automatically gets added to the list
                                     of IDs that can get status information for the gateway. If you specify no action, ColdFusion does not
                                     respond.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         1281
Using External Resources




CFC method                              Description

onAddBuddyResponse                      Response from another IM user to a request from the gatewaybeing added to their buddy list. The
                                        response message is accept or decline.

                                        Your application can handle this response as appropriate; for example, to add or remove the ID
                                        from a list of message recipients.

                                        This method does not return a value.

onBuddyStatus                           Message indicating a gateway buddy's status. Received when a buddy's status changes; for
                                        example, from OFFLINE to ONLINE.

                                        This method does not return a value.

onIMServerMessage                       Status messages from the IM server, such as warning or error messages. The messages you can
                                        receive depend on the IM server that sends them. For information on the server messages, see the
                                        documentation for the IM server that your gateway instance uses.

                                        This method does not return a value.


For detailed information on each method, including examples of their use, see IM Gateway CFC incoming message
methods in the CFML Reference. For an example that uses these functions, see "Sample IM message handling
application" on page 1282.


Sending outgoing messages
You use the SendGatewayMessage CFML function or the return value of a CFC listener method to send outgoing
messages. The ColdFusion IM gateway accepts the following outgoing message commands:


Command              Description

submit               (Default) Sends a normal message to another IM user.

accept               Accepts an add buddy request. Adds the buddy to the list of IDs that get your presence information and sends an
                     acceptance message to the buddy ID.

decline              Declines an add buddy request and sends a rejection message to the buddy ID.

noact                Tells the gateway to take no action. The gateway logs a message that indicates that it took no action, and contains the
                     gateway type, gateway ID, and buddy ID.


The message structure that you return in the gateway listener CFC function or use as the second parameter in the
CFML SendGatewayMessage function can have the following fields. The table lists the fields and the commands in
which they are used, and describes each field's use.


Field                 Commands               Description

buddyID               All                    The destination user ID.

command               All                    The command; if omitted, ColdFusion treats the message as a submit command.

message               submit                 A text message to send to the destination user.

reason                accept, decline        A text description of the reason for the action or other message to send to the add buddy
                                             requestor.


In typical use, a ColdFusion application uses the accept, decline, and noact commands in the return value of the
onAddBuddyRequest method, and uses the submit command (or no command, because submit is the default
command) in SendGatewayMessage CFML functions and the return value of the onIncomingMessage CFC method.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1282
Using External Resources




The SendGatewayMessage CFML function can send any command, and can be used to send an accept or decline
message. One possible use is in an application where someone must review all buddy requests before they are added.
In this case, the onAddBuddyRequest CFC method could initially send a noact command in its return value, and save
the request information in a database. Administrators could use a separate ColdFusion application to review the
request information. This application could use the SendGatewayMessage function with an accept or decline
command to act on the request and inform the requestor.

The following example onIncomingMessage method of a listener CFC echoes incoming IM messages to the message
originator:

<cffunction name="onIncomingMessage" output="no">
     <cfargument name="CFEvent" type="struct" required="yes">
     <cfset retValue.MESSAGE = "echoing: " & CFEvent.DATA.message>
     <cfset retValue.BuddyID = arguments.CFEVENT.DATA.SENDER>
     <cfreturn retValue>
</cffunction>



Sample IM message handling application
The application described here consists of two CFCs: an employee phone directory lookup CFC that responds to an
onIncomingMessage event, and a Gateway management CFC that responds to all other events. This example shows
how an application can respond to events and send outgoing messages.

You can configure a gateway to use both CFCs by entering the paths to the two CFCs, separated by a comma, in the
CFC Path field of the Add/Edit ColdFusion Event Gateways form on the Gateway Instances page in the ColdFusion
Administrator.


Phone directory lookup CFC
The following CFC implements a simple employee phone directory lookup application. The user sends an instant
message containing some part of the name to looked up (a space requests all names). The onIncomingMessage
response depends on the number matches.

· If there is no match, the onIncomingMessage function returns a message indicating that there are no matches.

· If there is one match, the function returns the name, department, and phone number.

· If there are up to ten matches, the function returns a list of the names preceded by a number that the user can enter
   to get the detailed information.

· If there are over ten matches, the function returns a list of only the first ten names. A more complex application can
   let the user get multiple lists of messages to provide access to all names.

· If the user enters a number, and previously got a multiple-match list, the application returns the information for
   the name that corresponds to the number.

The following listing shows the CFC code:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                           1283
Using External Resources




<cfcomponent>
    <cffunction name="onIncomingMessage">
          <cfargument name="CFEvent" type="struct" required="YES">
          <!--- Remove any extra white space from the message. --->
          <cfset message =Trim(arguments.CFEvent.data.MESSAGE)>
          <!--- If the message is numeric, a previous search probably returned a
                list of names. Get the name to search for from the name list stored in
                the Session scope. --->
          <cfif isNumeric(message)>
                <cfscript>
                     if (structKeyExists(session.users, val(message))) {
                         message = session.users[val(message)];
                     }
                </cfscript>
          </cfif>


          <!--- Search the database for the requested name. --->
          <cfquery name="employees" datasource="cfdocexamples">
                select FirstName, LastName, Department, Phone
                from Employees
                where 0 = 0
                <!--- A space indicates the user entered a first and last name. --->
                <cfif listlen(message, " ") eq 2>
                     and FirstName like '#listFirst(message, " ")#%'
                     and LastName like '#listlast(message, " ")#%'
                <!--- No space: the user entered a first or a last name. --->
                <cfelse>
                     and (FirstName like '#listFirst(message, " ")#%'
                     or LastName like '#listFirst(message, " ")#%')
                </cfif>
          </cfquery>


          <!--- Generate andreturn the message.--->
          <cfscript>
                retrunVal = structNew();
                retrunVal.command = "submit";
                retrunVal.buddyID = arguments.CFEvent.data.SENDER;


                //No records were found.
                if (employees.recordCount eq 0) {
                     retrunVal.message = "No records found for '#message#'";
                }
                //One record was found.
                else if (employees.recordCount eq 1) {
                // Whitespace in the message text results in bad formatting,
                // so the source cannot be indented.
                     retrunVal.message = "Requested information:
#employees.firstName# #employees.lastName#
#employees.Department#
#employees.Phone#";
                }
                //Multiple possibilities were found.
                else if (employees.recordCount gt 1) {
                     //If more than ten were found, return only the first ten.
                     if (employees.recordCount gt 10)
                     {
                         retrunVal.message = "First 10 of #employees.recordCount# records";



                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1284
Using External Resources




                     }else{
                          retrunVal.message = "Records found: #employees.recordCount#";
                     }
                     // The session.users structure contains the found names.
                     // The record key is a number that is also returned in front of the
                     // name in the message.
                     session.users = structNew();
                     for(i=1; i lte min(10, employees.recordCount); i=i+1)
                     {
                          // These two lines are formatted to prevent extra white space.
                          retrunVal.message = retrunVal.message & "
#i# - #employees.firstName[i]# #employees.lastName[i]#";
                          // The following two lines must be a single line in the source
                          session.users[i]="#employees.firstName[i]#
                                #employees.lastName[i]#";
                     }
                }
                return retrunVal;
          </cfscript>
     </cffunction>
</cfcomponent>


Status and request-handling CFC
The following CFC handles all IM events, except onIncomingMessage. It maintains an Application scope buddyStatus
structure that contains information on the gateway buddies. This structure limits the interactions that are needed with
the IM server to get buddy and status information. The application also logs significant events, such as requests to add
buddies and error messages from the IM server. In particular, it does the following:

· The onBuddyStatus function updates the Application scope buddy status structure when the gateway gets an event
   message indicating that a buddy's status has changed.

· The onAddBuddyRequest function searches for the requested buddy's name in a data source. If it finds a single
   instance of the name, it adds the buddy and updates the status in the Application scope buddyStatus structure. If it
   doesn't find name, it declines the buddy request. If it finds multiple instances of the name, it tells the gateway to
   take no action. It also logs all actions.

· The onAddBuddyResponse function adds the buddy to the Application scope buddy status structure if the buddy
   request is accepted, and sets the current status. It logs all responses.

· The onIMServerMessage function logs all messages that it receives.

   This example uses the IM_ID column of the Employees database of the cfdocexamples database that is included
   with ColdFusion. The entries in this column assume that you use an XMPP server "company." To run this
   exampleconfigure an XMPP server with this name and with clients with names in this database, or change the
   database entries to match IM server clients. Also, configure a gateway instance in the ColdFusion Administrator
   that uses this server.

The following listing shows the CFC code:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                           1285
Using External Resources




<cfcomponent>


<cffunction name="onBuddyStatus">
    <cfargument name="CFEvent" type="struct" required="YES">
    <cflock scope="APPLICATION" timeout="10" type="EXCLUSIVE">
          <cfscript>
          // Create the status structures if they don't exist.
                if (NOT StructKeyExists(Application, "buddyStatus")) {
                     Application.buddyStatus=StructNew();
                }
                if (NOT StructKeyExists(Application.buddyStatus, CFEvent.Data.BUDDYNAME)) {
                     Application.buddyStatus[#CFEvent.Data.BUDDYNAME#]=StructNew();
                }
                // Save the buddy status and timestamp.


Application.buddyStatus[#CFEvent.Data.BUDDYNAME#].status=CFEvent.Data.BUDDYSTATUS;


Application.buddyStatus[#CFEvent.Data.BUDDYNAME#].timeStamp=CFEvent.Data.TIMESTAMP;
          </cfscript>
    </cflock>
</cffunction>


<cffunction name="onAddBuddyRequest">
    <cfargument name="CFEvent" type="struct" required="YES">
    <cfquery name="buddysearch" datasource="cfdocexamples">
          select IM_ID
          from Employees
          where IM_ID = '#CFEvent.Data.SENDER#'
    </cfquery>
    <cflock scope="APPLICATION" timeout="10" type="EXCLUSIVE">
          <cfscript>
                // If the name is in the DB once, accept; if it is missing, decline.
                // If it is in the DB multiple times, take no action.
                if (buddysearch.RecordCount IS 0) {
                     action="decline";
                     reason="Invalid ID";
                }
                else if (buddysearch.RecordCount IS 1) {
                     action="accept";
                     reason="Valid ID";
                     //Add the buddy to the buddy status structure only if accepted.
                     if (NOT StructKeyExists(Application,
                            "buddyStatus")) {
                         Application.buddyStatus=StructNew();
                     }
                     if (NOT StructKeyExists(Application.buddyStatus,
                            CFEvent.Data.SENDER)) {
                         Application.buddyStatus[#CFEvent.Data.SENDER#]=StructNew();
                     }
                     Application.buddyStatus[#CFEvent.Data.SENDER#].status=
                         "Accepted Buddy Request";
                     Application.buddyStatus[#CFEvent.Data.SENDER#].timeStamp=
                         CFEvent.Data.TIMESTAMP;
                     Application.buddyStatus[#CFEvent.Data.SENDER#].message=
                         CFEvent.Data.MESSAGE;
                }
                else {



                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                    1286
Using External Resources




                     action="noact";
                     reason="Duplicate ID";
                }
          </cfscript>
    </cflock>
    <!--- Log the request and decision information. --->
    <cflog file="#CFEvent.GatewayID#Status"
                text="onAddBuddyRequest; SENDER: #CFEvent.Data.SENDER# MESSAGE:
#CFEvent.Data.MESSAGE# TIMESTAMP: #CFEvent.Data.TIMESTAMP# ACTION: #action#">
    <!--- Return the action decision. --->
    <cfset retValue = structNew()>
    <cfset retValue.command = action>
    <cfset retValue.BuddyID = CFEvent.DATA.SENDER>
    <cfset retValue.Reason = reason>
    <cfreturn retValue>
</cffunction>



<cffunction name="onAddBuddyResponse">
    <cfargument name="CFEvent" type="struct" required="YES">
    <cflock scope="APPLICATION" timeout="10" type="EXCLUSIVE">
          <cfscript>
                //Do the following only if the buddy accepted the request.
                if (NOT StructKeyExists(Application, "buddyStatus")) {
                     Application.buddyStatus=StructNew();
                     }
                if (#CFEVENT.Data.MESSAGE# IS "accept") {
                     //Create a new entry in the buddyStatus record for the buddy.
                     if (NOT StructKeyExists(Application.buddyStatus,
                            CFEvent.Data.SENDER)) {
                         Application.buddyStatus[#CFEvent.Data.SENDER#]=StructNew();
                     }
                     //Set the buddy status information to indicate buddy was added.
                     Application.buddyStatus[#CFEvent.Data.SENDER#].status=
                         "Buddy accepted us";
                     Application.buddyStatus[#CFEvent.Data.SENDER#].timeStamp=
                         CFEvent.Data.TIMESTAMP;
                     Application.buddyStatus[#CFEvent.Data.SENDER#].message=
                         CFEvent.Data.MESSAGE;
                }
          </cfscript>
    </cflock>
    <!--- Log the information for all responses. --->
    <cflog file="#CFEvent.GatewayID#Status"
          text="onAddBuddyResponse; BUDDY: #CFEvent.Data.SENDER# RESPONSE:
#CFEvent.Data.MESSAGE# TIMESTAMP: #CFEvent.Data.TIMESTAMP#">
</cffunction>



<cffunction name="onIMServerMessage">
    <!--- This function just logs the message. --->
    <cfargument name="CFEvent" type="struct" required="YES">
    <cflog file="#CFEvent.GatewayID#Status"
          text="onIMServerMEssage; SENDER: #CFEvent.OriginatorID# MESSAGE:
#CFEvent.Data.MESSAGE# TIMESTAMP: #CFEvent.Data.TIMESTAMP#">
</cffunction>
</cfcomponent>



                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 1287
Using External Resources




Using the GatewayHelper object
The CFML GetGatewayHelper function returns a GatewayHelper object with several methods that manage your
gateway and buddy list. The GatewayHelper methods let you do the following:

· Get and set gateway configuration information and get gateway statistics.

· Get and set the gateway online status.

· Manage the gateway's buddy list

· Manage permissions for others to get information about the gateway status.

The following sections briefly describe the class methods. For detailed information about each method, see IM
Gateway GatewayHelper class methods in the CFML Reference.


Gateway configuration information and statistics methods
The following table describes the methods that you can use to get and set configuration information and get gateway
statistics:


Method                           Description

getName                          Returns the gateway's user name.

getNickName                      Returns the gateway's display name (nickname).

getProtocolName                  Returns the name of the instant messaging protocol (JABBER for XMPP, or SAMETIME).

numberOfMessagesReceived         Returns the number of messages received by the gateway since it was started.

numberOfMessagesSent             Returns the number of messages sent by the gateway since it was started.

setNickName                      Sets the gateway's display name (nickname).



Gateway online status methods
The following table describes the methods that you can use to get and set the gateway's online availability status
(presence information):


Method                            Description

getCustomAwayMessage              Returns the gateway's custom away message if the setStatus method set it.

getStatusAsString                 Returns the online status of the gateway.

getStatusTimeStamp                Returns the date/time that the gateway changed its online status.

isOnline                          Returns True if the gateway is connected to the IM server; otherwise, returns false.

setStatus                         Changes the gateway's online status; for example, to away or idle.



Gateway buddy management methods
The following table describes the methods that you can use to manage the gateway's buddy list:


Method                            Description

addBuddy                          Adds a buddy to the gateway's buddy list and tells the IM server to send the gateway messages with
                                  the buddy's online state.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                      1288
Using External Resources




 Method                           Description

 getBuddyInfo                     Gets information about the specified user from the buddy list, deny list, and permit list.

 getBuddyList                     Returns the gateway's buddy list.

 removeBuddy                      Removes the specified user name from the gateway's buddy list and tells the IM server to stop
                                  sending the gateway messages with the user's online state.



Gateway permission management methods
The IM gateways can manage the information that other users can get about the gateway's online status.

Note: XMPP permission management is included in the XMPP 1.0 specification, but several XMPP servers that were
available at the time of the ColdFusion release do not support permission management.

The following table describes the gateway permission management methods:


 Method                           Description

 addDeny                          Tells the IM server to add the specified user to the gateway's deny list. If the permitMode is
                                  DENY_SOME, these users cannot receive messages on the gateway's state.

 addPermit                        Tells the IM server to add the specified user to the server's permit list. If the permitMode is
                                  PERMIT_SOME, these users receive messages on the gateway's state.

 getDenyList                      Returns the list of users that the server has been told not to send state information to.

 getPermitList                    Returns the list of users that the server has been told to send state information to.

 getPermitMode                    Gets the gateway's permit mode from the IM server. The permit mode determines whether all users
                                  can get the gateway's online state information, or whether the server uses a permit list or a deny list
                                  to control which users get state information.

 removeDeny                       Removes the user from the gateway's deny list.

 removePermit                     Removes the user from the gateway's permit list.

 setPermitMode                    Sets the gateway's permit mode on the IM server.



GatewayHelper example
This example lets you use the XMPP or SameTime GatewayHelper class to get and set status and other information,
including managing buddy lists and view permissions lists.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                            1289
Using External Resources




<cfapplication name="gateway_tool" sessionmanagement="yes">


<!--- Set the gateway buddy name to default values.--->
<cfparam name="session.gwid" default="XMPP Buddy Manager">
<cfparam name="session.buddyid" default="hlichtin2@mousemail">


<!--- Reset gateway and buddy ID if form was submitted. --->
<cfif isdefined("form.submitbuddy")>
    <cfset session.buddyid=form.buddyid>
    <cfset session.gwid=form.gwid>
</cfif>


<!--- Display the current gateway and buddy ID. --->
<h3>Using the GatewayHelper</h3>
<!--- Form to display and reset gateway and Buddy ID. --->
<cfform action="#cgi.script_name#" method="post" name="changeIDs">
    Current buddy ID: <cfinput type="text" name="buddyid" value="#session.buddyid#"><br>
    Current gateway ID: <cfinput type="text" name="gwid" value="#session.gwid#"><br>
    <cfinput name="submitbuddy" value="Change gateway/buddy" type="submit">
</cfform>


<!--- When a buddy is set, display the links and forms to get and set
          information, and so on, Where form input is required, the form uses a GET method
          so a url.cmd variable represents each selection. --->


<cfoutput>
<h3>Select one of the following to get or set.</h3>
<ul>
    <li><a href="#cgi.script_name#?cmd=buddyinfo">buddyinfo</a>
    <li>LIST: <a href="#cgi.script_name#?cmd=buddylist">buddylist</a> |
          <a href="#cgi.script_name#?cmd=permitlist">permitlist</a> |
          <a href="#cgi.script_name#?cmd=denylist">denylist</a>
    <li>ADD: <a href="#cgi.script_name#?cmd=addbuddy">addbuddy</a> |
          <a href="#cgi.script_name#?cmd=addpermit">addpermit</a> |
          <a href="#cgi.script_name#?cmd=adddeny">adddeny</a>
    <li>REMOVE: <a href="#cgi.script_name#?cmd=removebuddy">removebuddy</a> |
          <a href="#cgi.script_name#?cmd=removepermit">removepermit</a> |
                <a href="#cgi.script_name#?cmd=removedeny">removedeny</a>
    <!--- NOTE: This list does not include OFFLINE because the gateway resets itself to online.
--->
    <li>setStatus (XMPP):
          <cfloop list="ONLINE,AWAY,DND,NA,FREE_TO_CHAT" index="e">
                <a href="#cgi.script_name#?cmd=setstatus&status=#e#">#e#</a> |
          </cfloop>
    <li>setStatus (Sametime):
          <cfloop list="ONLINE,AWAY,DND,IDLE" index="e">
                <a href="#cgi.script_name#?cmd=setstatus&status=#e#">#e#</a> |
          </cfloop>
    <li>
          <form action="#cgi.script_name#" method="get">
                setStatus with CustomAwayMessage:
                <input type="hidden" name="cmd" value="setstatus2">
                <select name="status">
                     <cfloop
list="ONLINE,OFFLINE,AWAY,DND,IDLE,INVISIBLE,NA,OCCUPIED,FREE_TO_CHAT,ONPHONE,ATLUNCH,BUSY,N
OT_AT_HOME,NOT_AT_DESK,NOT_IN_OFFICE,ON_VACATION,STEPPED_OUT,CUSTOM_AWAY" index="e">
                         <option value="#e#">#e#</option>



                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                             1290
Using External Resources




                     </cfloop>
                </select>
                <input type="text" name="custommsg" value="(custom away massage)" size="30"/>
                <input type="submit"/>
          </form>
    <li>
          <form action="#cgi.script_name#" method="get">
                setNickName:
                <input type="hidden" name="cmd" value="setnickname">
                <input type="text" name="nickname" value="(enter nickname)">
                <input type="submit">
          </form>
--->
    <li>INFO: <a href="#cgi.script_name#?cmd=getname">getname</a> |
          <a href="#cgi.script_name#?cmd=getnickname">getnickname</a> |
          <a href="#cgi.script_name#?cmd=getcustomawaymessage">getcustomawaymessage</a> |
          <a href="#cgi.script_name#?cmd=getprotocolname">getprotocolname</a> |
          <a href="#cgi.script_name#?cmd=getstatusasstring">getstatusasstring</a> |
          <a href="#cgi.script_name#?cmd=isonline">isonline</a>
    <li>MESSAGE COUNT:
          <a
href="#cgi.script_name#?cmd=numberofmessagesreceived">numberofmessagesreceived</a> |
          <a href="#cgi.script_name#?cmd=numberofmessagessent">numberofmessagessent</a>
    <li>RUNNING TIME: <a
href="#cgi.script_name#?cmd=getsignontimestamp">getsignontimestamp</a> |
          <a href="#cgi.script_name#?cmd=getstatustimestamp">getstatustimestamp</a>
    <li>setPermitMode:
          <cfloop
list="PERMIT_ALL,DENY_ALL,PERMIT_SOME,DENY_SOME,IGNORE_IN_LIST,IGNORE_NOT_IN_LIST"
                     index="e"><a href="#cgi.script_name#?cmd=setpermitmode&mode=#e#">#e#</a> |
          </cfloop> <span class="note">doesn't work for XMPP</span>
    <li><a href="#cgi.script_name#?cmd=getpermitmode">getpermitmode</a>
    <li>setPlainTextMode:
          <cfloop list="PLAIN_TEXT,RICH_TEXT" index="e">
                <a href="#cgi.script_name#?cmd=setplaintextmode&mode=#e#">#e#</a> |
          </cfloop>
    <li><a href="#cgi.script_name#?cmd=getplaintextmode">getplaintextmode</a>
</ul>
</cfoutput>


<!--- The url.cmd value exists if one of the previous links or forms has been submitted, and
identifies the type of request. --->
<cfoutput>
<cfif isdefined("url.cmd")>
    <!--- Get the GatewayHelper for the gateway. --->
    <cfset helper = getGatewayHelper(session.gwid)>
    <!--- Get the buddy list if the list or full buddy information was requested. --->
    <cfswitch expression="#LCase(url.cmd)#">
          <cfcase value="buddylist,buddyinfo">
                <cfset ret=helper.getBuddyList()>
          </cfcase>
          <cfcase value="denylist">
                <cfset ret=helper.getDenyList()>
          </cfcase>
          <cfcase value="permitlist">
                <cfset ret=helper.getPermitList()>
          </cfcase>



                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                        1291
Using External Resources




          <cfcase value="addbuddy">
                <cfset ret=helper.addBuddy("#session.buddyid#",
                     "#session.buddyid#", "")>
          </cfcase>
          <cfcase value="addpermit">
                <cfset ret=helper.addPermit("#session.buddyid#",
                     "#session.buddyid#", "")>
          </cfcase>
          <cfcase value="adddeny">
                <cfset ret=helper.addDeny("#session.buddyid#",
                     "#session.buddyid#", "")>
          </cfcase>
          <cfcase value="removebuddy">
                <cfset ret=helper.removeBuddy("#session.buddyid#", "")>
          </cfcase>
          <cfcase value="removepermit">
                <cfset ret=helper.removePermit("#session.buddyid#", "")>
          </cfcase>
          <cfcase value="removedeny">
                <cfset ret=helper.removeDeny("#session.buddyid#", "")>
          </cfcase>
          <cfcase value="setstatus">
                <cfset ret=helper.setStatus(url.status, "")>
          </cfcase>
          <cfcase value="setstatus2">
                <cfset ret=helper.setStatus(url.status, url.custommsg)>
          </cfcase>
          <cfcase value="getcustomawaymessage">
                <cfset ret=helper.getCustomAwayMessage()>
          </cfcase>
          <cfcase value="getname">
                <cfset ret=helper.getName()>
          </cfcase>
          <cfcase value="getnickname">
                <cfset ret=helper.getNickname()>
          </cfcase>
          <cfcase value="getprotocolname">
                <cfset ret=helper.getProtocolName()>
          </cfcase>
          <cfcase value="getsignontimestamp">
                <cfset ret=helper.getSignOnTimeStamp()>
          </cfcase>
          <cfcase value="getstatusasstring">
                <cfset ret=helper.getStatusAsString()>
          </cfcase>
          <cfcase value="getstatustimestamp">
                <cfset ret=helper.getStatusTimeStamp()>
          </cfcase>
          <cfcase value="isonline">
                <cfset ret=helper.isOnline()>
          </cfcase>
          <cfcase value="numberofmessagesreceived">
                <cfset ret=helper.numberOfMessagesReceived()>
          </cfcase>
          <cfcase value="numberofmessagessent">
                <cfset ret=helper.numberOfMessagesSent()>
          </cfcase>



                                        

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1292
Using External Resources




          <cfcase value="setnickname">
                <cfset ret=helper.setNickName(url.nickname)>
          </cfcase>
          <cfcase value="setpermitmode">
                <cfset ret=helper.setPermitMode(url.mode)>
          </cfcase>
          <cfcase value="getpermitmode">
                <cfset ret=helper.getPermitMode()>
          </cfcase>
          <cfcase value="setplaintextmode">
                <cfset ret=helper.setPlainTextMode(url.mode)>
          </cfcase>
          <cfcase value="getplaintextmode">
                <cfset ret=helper.getPlainTextMode()>
          </cfcase>
          <cfdefaultcase>
                <cfset ret[1]="Error; Invalid command. You shouldn't get this.">
          </cfdefaultcase>
    </cfswitch>
    <br>
    <!--- Display the results returned by the called GatewayHelper method. --->
    <strong>#url.cmd#</strong><br>
    <cfdump var="#ret#">
    <br>
    <!--- If buddy information was requested, loop through buddy list to get
          information for each buddy and display it. --->
    <cfif comparenocase(url.cmd, "buddyinfo") is 0 and arraylen(ret) gt 0>
    <b>Buddy info for all buddies</b><br>
          <cfloop index="i" from="1" to="#arraylen(ret)#">
                <cfdump var="#helper.getBuddyInfo(ret[i])#" label="#ret[i]#"></cfloop>
    </cfif>
</cfif>
</cfoutput>




Using the SMS Event Gateway

You can develop an application that uses the short message service (SMS) event gateway type provided with Adobe
ColdFusion. ColdFusion provides tools for developing SMS applications.

Before you use the SMS event gateway, become familiar with ColdFusion event gateway principals and programming
techniques (see "Using Event Gateways" on page 1254). Although not required, a basic knowledge of SMS is helpful.


About SMS and ColdFusion
Short Message Service (SMS) is a system designed for sending short, often text, messages to and from wireless devices,
such as mobile phones or pagers. SMS is widely used in Europe and Asia and is becoming increasingly popular in the
United States and elsewhere. Some uses for SMS include the following:

· Performing banking transactions

· Sending authentication codes, for example, to be used to access web resources

· Voting, such as popularity voting for reality television shows

· Initiating an action (such as a server restart) and getting a response



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1293
Using External Resources




· Notifying users of events such as package shipments or restaurant table availability, or providing stock or weather
   alerts

· Sending person-to-person text messages

· Presenting interactive text-based menus on a mobile phone

· Providing cellular phone updates, such as direct download of logos

· Providing telematics and mobile or remote wireless device applications, such as soda machines, vehicle tracking,
   smart gas pumps, and so on

SMS protocol features include, but are not limited to, the following:

· Authentication verification is built in.

· Communications can be secure.

· Store and forward communication is performed in near real time.

· Communications can be two-way and session-aware.

· Mobile devices such as mobile phones already include support; you do not install software on the client.


About SMS
The following discussion simplifies SMS technology and describes only a typical use with a ColdFusion application. For
a more complete discussion of SMS, see the publicly available literature, including the several books that discuss SMS.

In a ColdFusion SMS application, a mobile device such as a mobile phone communicates (via intermediate steps) with
a message center, such as a short message service center (SMSC). For example, a mobile phone user calls a telephone
number that the SMS provider has associated with your account; the SMSC gets the messages that are sent to this
number. The SMSC can store and forward messages. A ColdFusion application can initiate messages to wireless
devices, or it can respond to incoming messages from the devices.

The SMSC communicates with a ColdFusion SMS event gateway using short message peer-to-peer protocol (SMPP)
over TCP/IP. Information is transferred by exchanging Protocol Data Units (PDUs) with structures that depend on
the type of transaction, such as a normal message submission, a binary data submission, or a message intended for
multiple recipients.

Because the SMSC is a store-and-forward server, it can hold messages that cannot be immediately delivered and try to
deliver them when the receiving device is available. The SMSC provider configures the time that a message is held on
the server for delivery. For example, AT&T Wireless saves messages for 72 hours; after that time, any undelivered
messages are deleted. Your messages can request a different time-out (by specifying a ValidityPeriod field). The
message can also use a registeredDelivery field to tell the SMSC to inform you about whether and when the
message is delivered.

SMS communication can be secure. Voice and data communications, including SMS message traffic between the
SMSC and the mobile device is encrypted as part of the GSM standard. The SMSC authenticates the mobile user's
identity before the encrypted communication session begins. Secure the communications between ColdFusion and the
SMSC. Typically, you use a secure hardware or software VPN connection around the SMPP connection.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 1294
Using External Resources




The following image shows the SMS path between mobile devices and ColdFusion gateways:




                             SMPP-
       SMS gateway
                             TCP/IP
         instance

                                                                                     Cell Phone
                                 -       Carrier SMSC
                             SMPP/IP
                                 P
                               TC

       SMS gateway
          Instance
                                                                                       PDA
                                                           Wireless network




                           SMPP-
       SMS Gateway         TCP/IP
         instance


                                 Carrier SMSC                                       Telephone
        ColdFusion



Using the SMS event gateway, ColdFusion establishes a two-way (transceiver) connection to the SMSC of the
telecommunications carrier or other SMPP account provider. The SMPP provider assigns an address for your account,
and you associate an event gateway instance with the address. Addresses are normally telephone numbers, but carriers
often support "short code" addresses that do not require a full 10-digit number. You also configure the gateway
instance to communicate with the provider's specified TCP/IP address using a system ID and a password.

Note: The ColdFusion SMS event gateway conforms to the SMPP 3.4 specification, which you can download from the
SMS Forum at www.smsforum.net.

A ColdFusion application can initiate and send messages to SMS-enabled devices by specifying the destination mobile
device telephone number, or mobile devices can send messages to a ColdFusion listener CFC by using the gateway
instance's telephone number. Incoming messages include the sender's number, so listener CFCs can respond to
messages sent by mobile devices.


About SMS application development and deployment
To develop an SMS gateway application, you use the ColdFusion SMS application development tools and process to
interact with SMS messaging providers.


ColdFusion SMS application tools
ColdFusion provides the following tools for developing SMS applications:

SMSGateway The class for the SMS event gateway type

SMS test server A lightweight SMSC simulator

SMS client simulator A graphical interface for sending and receiving SMS messages with the SMS test server

You implement your SMS application by creating a ColdFusion application that uses an instance of the SMSGateway
class to communicate with one or more SMSCs. You can use the SMS testing server and client simulator to test your
application without requiring an outside SMS service provider.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          1295
Using External Resources




Application development and deployment process
The following is a typical process for developing and deploying an SMS application:

1 Design your application.

2 Configure an SMS event gateway instance to use the ColdFusion SMS test server.

3 Write your ColdFusion CFCs, CFM pages, and any other application elements.

4 Test your application using the test server and client simulator.

5 Establish an SMPP account with a telecommunications provider.

6 Reconfigure your event gateway, or create an event gateway instance, to use your telecommunications provider's
    SMSC. Configure the gateway using the information supplied by your provider.

7 Test your application using the telecommunications provider's SMSC and target mobile devices.

8 Make the application publicly available.


About SMS providers
Before you can deploy an SMS application, establish an account with a provider that supports SMPP 3.4 over TCP/IP.
Two kinds of providers exist:

· Telecommunications carriers such as nation-wide cellular phone providers

· Third-party SMPP aggregators

The type of provider and specific provider you use depend on your needs and provider capabilities and price
structures. Less expensive providers could have slower response times. Telecommunications carriers could be more
expensive but might provide more throughput and faster SMPP response times.


How the SMS event gateway and provider SMSC interact
This following information provides a brief overview of the interactions between the ColdFusion SMS event gateway
and the SMPP provider's SMSC. It is designed to help you to understand the basics of SMPP interactions, and defines
the terms necessary to for you to understand gateway configuration and message handling. For more details, see the
SMPP specification, which is available at www.smsforum.net/.

A typical interaction between an SMSC and a ColdFusion SMS event gateway instance consists of messages, or PDUs
sent between the two entities, such as a mobile device and a ColdFusion event gateway instance (and therefore, and
event gateway application).


Gateway binding
The event gateway must bind to the SMSC before they can communicate. The SMS event gateway instance initiates a
binding by sending a bind_transceiver PDU to the SMSC, which includes the gateway's ID and password. If the initial
bind request fails, the gateway retries the bind at the rate specified by the gateway configuration file retry-interval value
until either the bind is successful or the gateway reaches the maximum number of retries, specified by the retries
configuration value. If the bind operation fails, ColdFusion logs an error to the eventgateway.log file, and you restart
the gateway instance in the ColdFusion Administrator to establish the connection.

Note: Some SMSCs can send a prohibited status in response to a bind request. If the gateway receives such a status
response, it sets the retry interval to one minute and the maximum number of retries to 15. The SMS gateway detects
SMPP 5.0-compliant and AT&T prohibited status responses.

When the SMSC accepts the bind request, it returns a bind_transceiver_resp PDU. The binding remains in effect until
the gateway instance shuts down and sends an unbind PDU to the SMSC. Because the gateway binds as a transceiver,
it can initiate messages to the SMSC, and the SMSC can send messages to it.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1296
Using External Resources




Incoming PDU handling
If the ColdFusion SMS event gateway gets an Unbind PDU from the SMSC, it sends an unbind_resp PDU to the SMSC,
does a restart, and attempts to rebind to the SMSC.

When the event gateway receives an EnquireLink or any other request PDU from the SMSC, it sends a default response
to the SMSC.

The gateway receives incoming messages from the SMSC in deliver _sm PDUs; it does not handle data_sm PDUs.
Deliver_sm PDUs can contain user- or application-generated messages, or disposition responses for messages that the
gateway has sent. The gateway extracts the short message field and source and destination addresses from the PDU,
places them in a CFEvent object, and sends the object to ColdFusion event gateway services for delivery to the listener
CFC. For information on how the CFML application must handle these incoming messages, see "Handling incoming
messages" on page 1298.


Outgoing message handling
The gateway supports three types of outgoing messages from ColdFusion applications. The CFML
sendGatewayMessage function or a listener CFC method cfreturn tag can specify the following commands:

submit Sends a submit_sm PDU with the message contents to the SMSC. This PDU sends a message to a single
destination.

submitMulti Sends a submit_multi PDU with the message contents to the SMSC. This PDU sends a message to
multiple destinations.

data Sends a data_sm PDU with the message contents to the SMSC. This command is an alternative to the submit
command, and interactive applications such as those provided via a wireless application protocol (WAP) framework
typically use it.

The SMS gateway lets you control the contents of all of the fields of these PDUs. For more information on the
individual commands, see "Sending outgoing messages" on page 1300.

When you send a message, if the SMSC responds with a status that indicates that the message was rejected or not sent,
ColdFusion logs information about the failure in the eventgateway.log file. If the SMSC indicates that the service type
is not available (SMPP v5 ESME_RSERTYPUNAVAIL status or AT&T Serviced denied status), and the gateway
configuration file transient-retry value is set to yes, the gateway also tries to resend the message.


Outgoing message synchronization and notification
The gateway and SMSC communicate asynchronously: the gateway does not wait for a response from the SMSC for
one message before it sends another message. However, you can configure your gateway instance so that the CFML
sendGatewayMessage function behaves asynchronously or synchronously.

· In asynchronous mode, the function returns when the message is queued in ColdFusion gateway services.

· In synchronous mode, the function waits until the SMSC receives the message and returns a message ID, or an error
    occurs.

For more information on configuring message synchronization and sending messages synchronously, see
"Controlling SMS message sending and response" on page 1303.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    1297
Using External Resources




Configuring an SMS event gateway
You provide SMS-specific configuration information to the SMS event gateway in a configuration file. You specify the
configuration file location when you configure the SMS event gateway instance in the ColdFusion Administrator.
ColdFusion provides a sample SMS event gateway configuration file in cf_root\WEB-
INF\cfusion\gateway\config\sms-test.cfg on J2EE configurations, and cf_root\gateway\config\sms-test.cfg on server
configurations. The following table describes the configuration file contents.

Note: The following configuration information describes the configuration fields, but does not include detailed
explanations of SMPP-specific terminology, listings of all valid values of properties that are defined in the SMPP
specification, or explanations of how to select appropriate SMPP-specific values for your application. For further
information, see documentation on the SMPP 3.4 protocol at www.smsforum.net/ and other publicly available
documentation. Your SMS service provider generally specifies requirements for several of these configuration values.
Consult the provider documentation.


 Property           Default                 Description

 ip-address                                 IP address of the SMSC, as specified by the SMPP provider. For the ColdFusion SMS test
                                            server, you normally use 127.0.0.1.

 port               0                       Port number to bind to on the SMSC. The ColdFusion SMS test server uses port 7901.

 system-id                                  Name that identifies the event gateway to the SMSC, as established with the SMPP provider.
                                            To connect to the ColdFusion SMS test server, the system-id must be cf.

 password                                   Password for authenticating the event gateway to the SMSC. To connect to the ColdFusion
                                            SMS test server, the password must be cf.

 source-ton         1                       Type of Number (TON) of the source address, that is, of the address that the event gateway
                                            uses for outgoing messages, as specified in the SMPP specification. Values include 0,
                                            unknown; 1, international number; 2, national number.

 source-npi         1                       Numeric Plan Indicator (NPI) of the source address as specified in the SMPP specification.
                                            Values include 0, unknown; 1, ISDN.

 source-address     empty string            Address (normally, a phone number) of the event gateway. Identifies the sender of
                                            outgoing messages to the SMSC.

 addr-ton           1                       TON for the incoming addresses that this event gateway serves.

 addr-npi           1                       NPI for the incoming addresses that this event gateway serves.

 address-range                              The range of incoming addresses (phone numbers) that remote devices can use to send
                                            messages to the event gateway instance; often, the same as the source-address.

 message-rate       100                     Integer or decimal value that specifies the number of messages the gateway is allowed to
                                            send to your service provider per second. 0 is unlimited.

 mode               synchronous             Message transmission mode:

                                            · synchronous The gateway waits for the response from the server when sending a
                                               message. In this mode, the SendGatewayMessage CFML function returns the SMS
                                               messageID of the message, or an empty string if an error occurs.

                                            · asynchronous The gateway does not wait for a response. In this mode, the
                                               SendGatewayMessage CFML function always returns an empty string.

 network-retry      no                      Gateway behavior when a network error occurs while trying to deliver a message:

                                            · yes The gateway queues the message for delivery when the gateway is able to rebind
                                               to the SMSC. Retrying is useful if the gateway is in asynchronous mode, where the CFML
                                               SendGatewayMessage function does not return an error.

                                            · no The gateway does not retry sending the message.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    1298
Using External Resources




 Property           Default                Description

 transient-retry    no                     Gateway behavior when the SMSC returns an error that indicates a transient error, where it
                                           may be able to accept the message in the future:

                                           · yes Thegatewayattemptstoresendthemessage.Retryingisusefulifthegatewayisin
                                             asynchronous mode, where the CFML SendGatewayMessage function does not return
                                             an error.

                                           · no The gateway does not retry sending the message.

 cfc-method         onIncomingMessage      Listener CFC method for ColdFusion to invoke when the gateway gets incoming messages.

 destination-ton    1                      Default TON of addresses for outgoing messages.

 destination-npi    1                      Default NPI of addresses for outgoing messages.

 service-type       empty string           Type of messaging service; can be empty or one of the following values: CMT, CPT, VMN,
                                           VMA, WAP, or USSD.

 system-type        empty string           Type of system (ESME, External Short Message Entity ); used when binding to the SMSC.
                                           Some SMSCs might be able to send responses that are specific to a given type of ESME.
                                           Normally set to SMPP.

 receive-timeout    -1 (do not time out)   The time-out, in seconds, for trying to receive a message from the SMSC after it establishes
                                           a connection. To wait indefinitely until a message is received, set the receive-timeout
                                           to -1.

 ping-interval      60                     Number of seconds between EnquireLink messages that the event gateway sends to the
                                           server to verify the health of the connection.

 retries            -1 (try forever)       Number of times to retry connecting to the SMSC to send a message before the gateway
                                           goes into a failed state. If the gateway is in a failed state, the getStatus method returns
                                           FAILED, and theColdFusion Administrator shows the gateway status as Failed. The gateway
                                           must be restarted before it can be used.

 retry-interval     10                     Number of seconds between connection retries.


You can also set the following values in each outgoing message: source-ton, source-npi, source-address, destination-
ton, destination-npi, and service-type. The message field names differ from the configuration file property names.


Handling incoming messages
The SMS event gateway handles messages that are contained in deliver_sm PDUs. These PDUs request the gateway to
deliver one of the following types of message:

· A user- or application-generated text message

· A message disposition response

Note: The SMS event gateway does not handle messages that are contained in data_sm PDUs.

The event gateway sends the object to event gateway services, which delivers it to the listener CFC. The CFEvent object
that the listener CFC receives contains the following fields:

Note: Consider SMS messages and any other data that enters through an Event Gateway handler to be potentially hostile.
For example, if SMS data is archived in a SQL database, an attacker could construct a message that modifies or deletes
data, or even takes over the SQL Server. Therefore, be sure to perform Event Gateway input validation, just as you would
validate web form input.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1299
Using External Resources




 Field                           Value

 CfcMethod                       Listener CFC method name

 Data.dataCoding                 Character set or the noncharacter data type of the message contents

 Data.destAddress                Address to which the message was sent

 Data.esmClass                   Message type

 Data.MESSAGE                    Message contents

 Data.messageLength              Length of the MESSAGE field

 Data.priority                   Message priority level, in the range 0-3

 Data.protocol                   GSM protocol; not used for other networks

 Data.registeredDelivery         Requested type of delivery receipt or acknowledgment, if any

 Data.sourceAddress              Address of the device that sent this message

 GatewayType                     Always SMS

 OriginatorID                    Address of the device that sent the message


For a detailed description of each field, see SMS Gateway incoming message CFEvent structure in the CFML Reference.

The CFC's listener method extracts the message from the Arguments.CFEvent.Data.MESSAGE field and acts on it as
appropriate for the application. If necessary, the listener can use two fields to determine the required action:

· CFEvent.Data.esmClass indicates the type of information in the MESSAGE field.

· CFEvent.Data.registeredDelivery indicates whether the sender requested any type of delivery receipt or
    acknowledgment.


CFEvent.Data.esmClass field
The CFEvent.Data.esmClass field identifies whether the CFEvent.Data.Message field contains a message, or any of the
following types of message disposition responses. For these responses, the CFEvent object Data.MESSAGE field
contains the acknowledgment or receipt information.

SMSC Delivery Receipt An indication of the message's final status, sent by the SMSC. The short message text includes
the message ID of the original message, the number of messages sent and delivered (for messages sent to a distribution
list), the date and time that the message was sent and delivered or otherwise disposed of, the message disposition, a
network-specific error code (if available), and the first 20 bytes of the message. For details of the SMSC delivery receipt
message structure, see Appendix B of the SMS 3.4 specification.

SME Delivery Acknowledgement An indication from the recipient device that the user has read the short message.
Supported by TDMA and CDMA wireless networks only.

SME Manual/User Acknowledgement An application-generated reply message sent in response to an application
request message. Supported by TDMA and CDMA wireless networks only.

Intermediate Delivery Notification A provider-specific notification on the status of a message that has not yet been
delivered, sent during the SMSC retry lifetime for the message. Intermediate Notification support depends on the
SMSC implementation and SMSC service provider. For more information, see your provider documentation.

When you send a message, you can request any combination of message disposition responses in the outgoing
message's registered_delivery parameter. If your application requests responses, the listener CFC must be
prepared to handle these messages, as appropriate.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   1300
Using External Resources




CFEvent.Data.registeredDelivery field
The CFEvent.Data.registeredDelivery field indicates whether the message sender has requested a receipt or
acknowledgment. Your application can respond to a request for an SME Delivery Acknowledgement or an SME
Manual/User Acknowledgement. (Only the SMSC sends the other notification types.) For more information on these
notification types, see the SMS 3.4 specification. Appendix B contains detailed information on the information that
you must place in the shortMessage field of the returned acknowledgment message.


Incoming message handling example
The following example code is an SMS-only version of the echo.cfc example that is included in the ColdFusion
gateway/cfc/examples directory. This example shows the minimal code required to receive and respond to an SMS
message.

<cfcomponent displayname="echo" hint="Process events from the test gateway and return echo">
<cffunction name="onIncomingMessage" output="no">
     <cfargument name="CFEvent" type="struct" required="yes">
     <!--- Get the message --->
     <cfset data=cfevent.DATA>
     <cfset message="#data.message#">
     <!--- where did it come from? --->
     <cfset orig="#CFEvent.originatorID#">
     <cfset retValue = structNew()>
     <cfset retValue.command = "submit">
     <cfset retValue.sourceAddress = arguments.CFEVENT.gatewayid>
     <cfset retValue.destAddress = arguments.CFEVENT.originatorid>
     <cfset retValue.shortMessage = "echo: " & message>
     <!--- send the return message back --->
     <cfreturn retValue>
</cffunction>
</cfcomponent>



Sending outgoing messages
Your ColdFusion application can send submit, submitMulti, and data commands to the event gateway in an
outgoing message.


The submit command
To send a message to a single destination address in an SMPP SUBMIT_SM PDU, the structure used in the Data
parameter of a SendGatewayMessage function or the return variable of the CFC listener method normally has the
following fields:


 Field                      Contents

 command                    If present, the value must be "submit". If you omit this field, the event gateway sends a submit message.

 shortMessage               The Message contents. specify one of these fields, but not both. The SMPP specification imposes a maximum
                            size of 254 bytes on the shortMessage field, and some carriers could limit its size further. The
 or
                            messagePayloadfieldcancontainupto64Kbytes;itmuststartwith0x0424,followedby2bytesspecifying

 messagePayload             the payload length, followed by the message contents.


 destAddress                The address to which to send the message (required).

 sourceAddress              The address of this application. You can omit this field if it is specified in the configuration file.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1301
Using External Resources




You can also set optional fields in the structure, such as a field that requests a delivery receipt. For a complete list of
fields, see submit command in the CFML Reference. For detailed descriptions of these fields, see the documentation
for the SUBMIT_MULTI PDU in the SMPP3.4 specification, which you can download from the SMS Forum at
www.smsforum.net/.

Note: To send long messages, you can separate the message into multiple chunks and use a submit command to send each
chunk separately. In this case, a CFC would use multiple SendGatewayMessage functions, instead of the cfreturn
function.


Example: Using the submit command in sendGatewayMessage function
The following example from a CFM page uses a sendGatewyMessage CFML function with a submit command to
send an SMS messages that you enter in the form. This example uses the SMS gateway that is configured in the
ColdFusion installation, and sends the message to the SMS client simulator.

<h3>Sending SMS From a Web Page Example</h3>
<cfif IsDefined("form.oncethrough") is "Yes">
<cfif IsDefined("form.SMSMessage") is True AND form.SMSMessage is not "">
     <h3>Sending Text Message: </h3>
           <cfoutput>#form.SMSMessage#</cfoutput><br>
           <cfscript>
           /* Create a structure that contains the message. */
           msg = structNew();
           msg.command = "submit";
           msg.destAddress = "5551234";
           msg.shortMessage = form.SMSMessage;
           ret = sendGatewayMessage("SMS Menu App - 5551212", msg);
           </cfscript>
</cfif>
<hr noshade>
</cfif>
<!--- begin by calling the cfform tag --->
<cfform action="command.cfm" method="POST">
     SMS Text Message: <cfinput type="Text" name="SMSMessage" value="Sample text Message"
required="No" maxlength="160">
<p><input type = "submit" name = "submit" value = "Submit">
<input type = "hidden" name = "oncethrough" value = "Yes">
</cfform>
</body>
</html>

For a simple example of a listener CFC uses the submit command to echo incoming SMS messages to the message
originator, see "Incoming message handling example" on page 1300.


The submitMulti command
To send a single text message to multiple recipients using an SMPP SUBMIT_MULTI PDU, the Data parameter of a
SendGatewayMessage function or the return variable of the CFC listener method normally has the following fields:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        1302
Using External Resources




 Field                    Contents

 command                  Must be "submitMulti".

 shortMessage             The message contents. Specify one of these fields, but not both. The SMPP specification imposes a maximum size
                          of 254 bytes on the shortMessage field, and some carriers could limit its size further. The messagePayload
 or
                          field can contain up to 64 K bytes; it must start with 0x0424, followed by 2 bytes specifying the payload length,

 messagePayload           followed by the message contents.


 destAddress              A ColdFusion array of destination addresses (required).

                          You cannot specify individual TON and NPI values for these addresses; all must conform to a single setting.

 sourceAddress            The address of this application; you can omit this field if it is specified in the configuration file.


You can also set optional fields in the structure, such as a field that requests delivery receipts. For a complete list of
fields, see submitMulti command in the CFML Reference. For detailed descriptions of these fields, see the
documentation for the SUBMIT_MULTI PDU in the SMPP 3.4 specification, which you can download from the SMS
Forum at www.smsforum.net/.


Example: Using the submitMulti command in an onIncomingMessage method
The following example onIncomingMessage method sends a response that echoes an incoming message to the
originator address, and sends a copy of the response to a second address. To test the example, run two instances of the
ColdFusion SMS client application. Use the default phone number of 5551212 for the first, and set the second one to
have a phone number of 555-1235. (Notice that the second phone number requires a hyphen (-).) Send a message from
the first simulator, and the response appears in both windows.

<cffunction name="onIncomingMessage" output="no">
     <cfargument name="CFEvent" type="struct" required="yes">
     <!--- Get the message. --->
<cfset data=CFEvent.DATA>
<cfset message="#data.message#">
     <!--- Create the return structure. --->
     <cfset retValue = structNew()>
     <cfset retValue.command = "submitmulti">
     <cfset retValue.sourceAddress = arguments.CFEVENT.gatewayid>
     <cfset retValue.destAddresses=arraynew(1)>
     <!--- One destination is incoming message originator;
                          get the address from CFEvent originator ID. --->
     <cfset retValue.destAddresses[1] = arguments.CFEvent.originatorid>
     <cfset retValue.destAddresses[2] = "555-1235">
     <cfset retValue.shortMessage = "echo: " & message>
<cfreturn retValue>
</cffunction>
</cffunction>


The data command
To send binary data to a single destination address in an SMPP DATA_SM PDU, the Data parameter of a
SendGatewayMessage function or the return variable of the CFC listener method must have the following fields:


 Field                    Contents

 command                  Must be "data" .




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1303
Using External Resources




 Field                    Contents

 messagePayload           Message data. To convert data to binary format, use the ColdFusion toBinary function.

 destAddress              Address to which to send the message.

 sourceAddress            Address of this application; can be omitted if specified in the configuration file.


You can also set optional fields in the structure, such as a field that requests a delivery receipt. For a complete list of
fields, see data command in the CFML Reference. For detailed descriptions of these fields, see the documentation for
the SUBMIT_MULTI PDU in the SMPP3.4 specification, which you can download from the SMS Forum at
www.smsforum.net/.


Example: Using the data command
The following example onIncomingMessage method converts an incoming message to binary data, and sends the
binary version of the message back to the originator address:

<cffunction name="onIncomingMessage" output="no">
      <cfargument name="CFEvent" type="struct" required="yes">
      <!--- Get the message. --->
<cfset data=CFEvent.DATA>
<cfset message="#data.message#">
      <!--- Create the return structure. --->
      <cfset retValue = structNew()>
      <cfset retValue.command = "data">
      <!--- Sending to incoming message originator; get value from CFEvent. --->
      <cfset retValue.destAddress = arguments.CFEvent.originatorid>
      <cfset retValue.messagePayload = tobinary(tobase64("echo: " & message))>
      <cfreturn retValue>
</cffunction>


Controlling SMS message sending and response
This documentation describes some of the more common options for sending messages, and how they affect your
application. For information on other ways to configure outgoing message, see the SMPP specification.


Synchronization mode
You can specify asynchronous or synchronous message mode in the gateway configuration file.

· If you specify asynchronous mode, the sendGatewayMessage function returns an empty string when the gateway
    submits the message to service code for sending to the SMSC. ColdFusion logs errors that occur after this point,
    such as if a message sent by the gateway to the SMSC times out or if the gateway gets an error response; the
    application does not get notified of any errors.

· If you specify synchronous mode (the default), the sendGatewayMessage function does not return until the
    gateway gets a response from the SMSC or the attempt to communicate times out. If the message is sent
    successfully, the function returns the SMPP message ID string. If an error occurs, the function returns an error
    string.

Use synchronous mode if your application must determine whether its messages reach the SMSC. Also use
synchronous mode if the application requests return receipts.

Note: If you use synchronous mode and the SMSC returns the messgeID as a hexadecimal string, ColdFusion converts it
automatically to its decimal value.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1304
Using External Resources




The following example is an expansion of Example: Using the submit command in sendGatewayMessage function
discussed in "The submit command" on page 1300. It checks for a nonempty return value and displays the message
number returned by the SMS. This example uses the SMS gateway that is configured when ColdFusion is installed. If
you change the gateway specified in the SendGatewayMessage function, make sure that your gateway's configuration
file specifies synchronous mode.

<h3>Sending SMS From a Web Page Example</h3>


<cfif IsDefined("form.oncethrough") is "Yes">
      <cfif IsDefined("form.SMSMessage") is True AND form.SMSMessage is not "">
             <h3>Sending a Text Message: </h3>
                 <cfoutput>#form.SMSMessage#</cfoutput><br><br>


           <cfscript>
                 /* Create a structure that contains the message. */
                 msg = structNew();
                 msg.command = "submit";
                 msg.destAddress = "5551234";
                 msg.shortMessage = form.SMSMessage;
                 ret = sendGatewayMessage("SMS Menu App - 5551212", msg);
           </cfscript>
      </cfif>
      <cfif isDefined("ret") AND ret NEQ "">
           <h3>Text message sent</h3>
           <cfoutput>The Message Id is: #ret#</cfoutput>
           <br><br>
      </cfif>
      <hr noshade>
</cfif>


<!--- begin by calling the cfform tag --->
<cfform>
      SMS Text Message: <cfinput type="Text" name="SMSMessage"
           value="Sample text Message" required="No" maxlength="160">
      <p><input type = "submit" name = "submit" value = "Submit">
      <input type = "hidden" name = "oncethrough" value = "Yes">
</cfform>


Optional parameters for outgoing SMS
You can send vendor-specific optional parameters by way of ColdFusion SMS gateway.

To set the optional parameters, specify them using the optionalparameter attribute.

If the gateway receives optional parameters in a message, they are included in the data struct that is returned to the
listener CFC method named onIncomingMessage under the optionalParameters key.

The following code describes how to add optional parameters:

params=StructNew();
params["parameter"]=BinaryDecode("string","binaryencoding");
params["parameter"]=CharsetDecode("string, encoding");
outgoingSMS.optionalParameters=params;

· parameter: Vendor-specific optional parameter.

· BinaryDecode: See BinaryDecode in CFML Reference.

· CharsetDecode: See CharsetDecode in CFML Reference.



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1305
Using External Resources




If there is only one optional parameter, you can instead use the following code:

out.optionalParameter=parameter;
out.optionalParameterValue="value";

Note: Ensure that the Java Short.decode(String) function can parse the key or the value is a byte.


Message disposition notification
You can request the SMSC to return a message disposition response to indicate the fate of your message. To request a
delivery receipt, include a RegisteredDelivery field in the Data parameter of a SendGatewayMessage function or the
return variable of the CFC listener method. This field can have the following values:


 Value      Meaning

 0          (Default) Do not return delivery information.

 1          Return a receipt if the message is not delivered before the time-out.

 2          Return a receipt if the message is delivered or fails.


Some providers also support intermediate delivery notifications. For more information, see your provider's
documentation.

To use delivery notification, send your message using synchronous mode, so you get a message ID. Your incoming
message routine must be able to handle the receipts (see "Handling incoming messages" on page 1298).


Validity period
You can change the length of time that the SMSC keeps a message and tries to deliver it. (Often the default value is 72
hours.) For a message sent to an emergency worker, for example, a short validity period (such as 15 min.) can be
appropriate. To change this value, include a validityPeriod field in the Data parameter of a SendGatewayMessage
function or the return variable of the CFC listener method. To specify a time period, use the following pattern:
YYMMDDhhmmsst00R. In this pattern, t indicates tenths of seconds, and 00R specifies that this value is a relative time
period, not a date-time value. The time format 000001063000000R, for example, specifies a validity period of 0 years,
0 months, 1 day, 6 hours, 30 minutes.


ColdFusion SMS development tools
ColdFusion provides the following tools for developing SMS applications:

· SMS test server

· SMS client simulator


SMS test server
The ColdFusion SMS test server is a lightweight SMSC simulator that listens on TCP/IP port 7901 for SMPP
connection requests from other SMS resources, such as ColdFusion SMS gateways or the SMS client simulator. The
resource supplies a user name, password, and telephone number (address). The user name and password must
correspond to a name and password in the simulator's configuration file (described later).

After the SMS test server establishes a connection, it listens for incoming messages and forwards them to the specified
destination address, if the destination address also corresponds to an existing SMPP connection.




                                                       

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1306
Using External Resources




The SMS test server lets you develop SMS applications without having to use an external SMSC supplier such as a
telecommunications provider. The server supports the ColdFusion SMS gateway submit and submitMulti
commands. It also accepts, but does not deliver messages sent using the SMS gateway data command. It does not
include any store and forward capabilities.

Start the SMS test server by clicking the Start SMS Test Server button on the Settings page in the Event Gateways area
in the ColdFusion Administrator.

Note: The SMS test server does not automatically restart when you restart ColdFusion. Manually restart the server if you
restart ColdFusion.

The SMS test server reads the cf_root\WEB-INF\cfusion\lib\sms-test-users.txt file on J2EE configurations or
cf_root\lib\sms-test-users.txt file on server configurations to get valid user names and passwords. ColdFusion includes
a version of this file configured with several names and passwords. One valid combination is user name cf and
password cf. You can edit this file to add or delete entries. The file must include a name and password entry for each
user that connects to the test server. Separate user entries with blank lines, as the following example shows:

name=cf
password=cf


name=user1
password=user1


SMS client simulator
The ColdFusion SMS client simulator is a simple External Short Message Entity (ESME) that simulates a (limited-
function) mobile phone. It can connect to the SMS test server and exchange messages with it.

Note: On UNIX and Linux systems, the client simulator requires X-Windows.

Use the following procedure to use the simulator.


Use the SMS simulator
1 Ensure that you have started the SMS test server and configured and started an SMS event gateway instance in
   ColdFusion Administrator.

2 Run SMSClient.bat in Windows or SMSClient.sh on UNIX or Linux. These files are located in the cf_root\WEB-
   INF\cfusion\bin directory on J2EE configurations and the cf_root\bin directory on server configurations.

   If you installed a pure Java version of ColdFusion, for example, on Apple Mac OS X systems, enter the following
   command to start the simulator:

    java -jar cf_root/WEB-INF/cfusion/lib/smpp.jar

3 A dialog box appears, requesting the server, port, user name, password, and the phone number to use for this
   device. The simulator sends this phone number as the source address, and accepts SMS messages sent by the SMSC
   server to it using this number as the destination address.

   To connect to the SMS test server, accept the default values and specify an arbitrary phone number; you can also
   specify any user name-password pair that is configured in the cf_root\WEB-INF\cfusion\lib\sms-test-users.cfg file
   or cf_root\lib\sms-test-users.cfg or file.

4 Click Connect.

5 The SMS device simulator client appears. In the Send SMS To field, enter a phone number in the address-range
   property specified in the configuration file of the SMS event gateway that you want to send messages to.

6 Type a message directly into the message field (to the left of the Send button), or use the simulator keypad to enter
   the message.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1307
Using External Resources




7 Click the Send button.

The client simulator has a Connection menu with options to connect and disconnect from the SMSC server, and to
check the connection. The connection information appears in a status line at the bottom of the client.


Sample SMS application
The following CFC implements a simple employee phone directory lookup application. The user sends a message
containing some part of the name being looked up (a space requests all names). The onIncomingMessage response
depends on the number matches.

· If no match exists, the onIncomingMessage function returns a message indicating that no matches exist.

· If one match exists, the function returns the name, department, and phone number.

· If up to ten matches exist, the function returns a list of the names preceded by a number that the user can enter to
   get the detailed information.

· If over ten matches exist, the function returns a list of only the first ten names. A more complex application could
   let the user get multiple lists of messages to provide access to all names.

· If the user enters a number, and previously got a multiple-match list, the application returns the information for
   the name that corresponds to the number.

The following listing shows the CFC code:

<cfcomponent>
     <cffunction name="onIncomingMessage">
           <cfargument name="CFEvent" type="struct" required="YES">
           <!--- Remove any extra white space from the message. --->
           <cfset message =Trim(arguments.CFEvent.data.MESSAGE)>
           <!--- If the message is numeric, a previous search probably returned a
                list of names. Get the name to search for from the name list stored in
                the Session scope. --->
           <cfif isNumeric(message)>
                <cfscript>
                     if (structKeyExists(session.users, val(message))) {
                         message = session.users[val(message)];
                     }
                </cfscript>
           </cfif>


           <!--- Search the database for the requested name. --->
           <cfquery name="employees" datasource="cfdocexamples">
                select FirstName, LastName, Department, Phone
                from Employees
                where 0 = 0
                <!--- A space indicates the user entered a first and last name. --->
                <cfif listlen(message, " ") eq 2>
                     and FirstName like '#listFirst(message, " ")#%'
                     and LastName like '#listlast(message, " ")#%'
                <!--- No space: the user entered a first or a last name. --->
                <cfelse>
                     and (FirstName like '#listFirst(message, " ")#%'
                     or LastName like '#listFirst(message, " ")#%')
                </cfif>
           </cfquery>


           <!--- Generate andreturn the message.--->



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                             1308
Using External Resources




          <cfscript>
                returnVal = structNew();
                returnVal.command = "submit";
                returnVal.sourceAddress = arguments.CFEVENT.gatewayid;
                returnVal.destAddress = arguments.CFEVENT.originatorid;


                //No records were found.
                if (employees.recordCount eq 0) {
                     returnVal.shortMessage = "No records found for '#message#'";
                }
                //One record was found.
                else if (employees.recordCount eq 1) {
                // Whitespace in the message text results in bad formatting,
                // so the source cannot be indented.
                     returnVal.shortMessage = "Requested information:
#employees.firstName# #employees.lastName#
#employees.Department#
#employees.Phone#";
                }
                //Multiple possibilities were found.
                else if (employees.recordCount gt 1) {
                     //If more than ten were found, return only the first ten.
                     if (employees.recordCount gt 10)
                     {
                         returnVal.shortMessage = "First 10 of #employees.recordCount# records";
                     }else{
                         returnVal.shortMessage = "Records found: #employees.recordCount#";
                     }
                     // The session.users structure contains the found names.
                     // The record key is a number that is also returned in front of the
                     // name in the message.
                     session.users = structNew();
                     for(i=1; i lte min(10, employees.recordCount); i=i+1)
                     {
                         // These two lines are formatted to prevent extra white space.
                         returnVal.shortMessage = returnVal.shortMessage & "
#i# - #employees.firstName[i]# #employees.lastName[i]#";
                         // The following two lines must be a single line in the source
                         session.users[i]="#employees.firstName[i]# #employees.lastName[i]#";
                     }
                }
                return returnVal;
          </cfscript>
     </cffunction>
</cfcomponent>




Using the FMS event gateway

The FMS event gateway provides interfaces between the Flash Media Server 2 and the Adobe ColdFusion server. As a
result, ColdFusion applications and Adobe Flash clients can share data.

Before you use the gateway, become familiar with ColdFusion event gateway principles and programming techniques
(see "Using Event Gateways" on page 1254). A basic knowledge of Flash Media Server is also helpful.




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           1309
Using External Resources




About Flash Media Server
Flash Media Server 2 is the newest version of Flash Communication Server. Flash Media Server 2 offers traditional
streaming media capabilities and a flexible development environment for creating and delivering innovative,
interactive media applications. You can use Flash Media Server to create and deliver the following media experiences:

· Video on Demand

· Live web-event broadcasts

· Mp3 streaming

· Video blogging

· Video messaging

· Multimedia chat environments

To learn more about and to download the Flash Media Server, go to the Adobe website. at
www.adobe.com/go/learn_cfu_flashmediaserver_en.


How ColdFusion and Flash Media Server interact through the FMS gateway
The FMS event gateway lets you modify data through the ColdFusion application or the Flash client, and reflect the
change in the Flash Media Server shared object. The FMS event gateway listens to the shared object, and notifies
ColdFusion when other clients modify shared objects. The FMS event gateway also lets ColdFusion modify shared
objects.

ColdFusion provides the following tools for developing FMS applications:

FCSj.jar The JAR file that implements the Java API to communicate with Flash Media Server.

FMSGateway The class for the FMS event gateway type. You implement your FMS application by creating a
ColdFusion application that uses an instance of the FMSGateway class to communicate with one or more Flash Media
Server.


Modifying data in the Flash client
The FMS event gateway listens to Flash Media Server shared objects, and notifies ColdFusion when a Flash client
modifies a shared object. The following steps occur when a Flash client modifies a Flash Media Server shared object:

1 A user modifies data in the Flash client.

2 Flash Media Server updates the appropriate shared object.

3 Flash Media Server notifies the FMS event gateway.

4 The FMS event gateway calls the appropriate methods in CFCs in your ColdFusion application. These CFCs
   perform all actions required, including notifying the FMS Gateway Helper to update the shared object.

5 The FMS Gateway Helper sends a message to the FMS event gateway to update the shared object.

6 The FMS event gateway updates the shared object.

7 Flash Media Server notifies all the Flash clients that it modified the shared object. As a result, the Flash clients reflect
   the change.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                              1310
Using External Resources




The following image shows the interaction between Flash Media Server, the FMS event gateway, and the ColdFusion
application:


               1.
            Flash client     Flash client
                               10.                            ColdFusion Server
        Flash Media Server

           Sales Data So
                              9.
           Sales Data List
 2.                                       3.
       Changed Sales Data                          FMS event gateway
                                                                                                      database
                                                         8.                        4.
                                                                              FMS_gateway.cfc           6.
                                                  FMS Gateway Helper               5.
                                                         7.                       sales.cfc




Modifying data in a ColdFusion application
The FMS event gateway lets ColdFusion applications modify Flash Media Server shared objects. The following steps
occur when data that affects a shared object is modified in a ColdFusion application:

1 The user submits a form that contains data to modify using a ColdFusion page.

2 The ColdFusion page calls the appropriate CFC, which contains a method to update the database.

3 The method in the CFC updates the database and calls a method in the FMS Gateway Helper.

4 The FMS Gateway Helper calls the FMS event gateway to update the appropriate shared object.

5 Flash Media Server updates the shared object.

6 Flash Media Server notifies the Flash client that a shared object has changed.

7 The Flash client makes the changes in its content as appropriate.


Application development and deployment process
The following is a typical process for developing and deploying an application that uses the FMS event gateway:

1 Design your application.

2 Configure an FMS event gateway instance to use the Flash Media Server.

3 Write your ColdFusion CFCs, CFM pages, and any other application elements.

4 Create or identify a Flash client that manipulates a Flash Media Server shared object to test your ColdFusion
   application.

5 Test your application using Flash Media Server and the Flash client.

6 Make the application publicly available.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                1311
Using External Resources




Configuring an FMS event gateway
You provide FMS event gateway-specific configuration information to the FMS event gateway in a configuration file.
You specify the configuration file location when you configure the FMS event gateway instance in the ColdFusion
Administrator. The configuration file contains the URL of the Flash Media Server application and the name of the
Flash Media Server shared object. The following example is a sample configuration file:

#
# FMS event gateway configuration
#


# This is the URL to the Flash Media Server application.
rtmpurl=rtmp://localhost/SalesDataApp


# This is the shared object you would like this gateway to connect and listen to.
sharedobject=SalesDataSO


FMS event gateway GatewayHelper class methods
The following table lists the FMS event gateway GatewayHelper class methods:


 Method                            Description

 setProperty                       Sets the property of the Flash Media Server shared object. The following parameters are valid:

                                   name: The string that contains the name of the shared object.

                                   value: The shared object.


 getProperty                       Gets the property of the Flash Media Server shared object. The following parameters are valid:

                                   name: The string that contains the name of the shared object.



Data translation
ColdFusion and Flash Media Server use different data types; therefore, data translation is required to pass data from
one to the other. In addition to basic data types such as numeric, String, and Boolean, you can pass ColdFusion queries,
structures, and arrays to Flash Media Server. You pass a ColdFusion query object to Flash Media Server as an array of
java.util.HashMap. Each HashMap object in the array contains a key-value pair for column names and values for each
row in the query. When you pass a ColdFusion array to Flash Media Server, the FMS event gateway converts it to a
Java array of objects. When you pass a ColdFusion structure, no conversion is required.

The FMS event gateway does not support passing CFCs in shared objects.



Using the Data Services Messaging Event Gateway

Using the Data Services Messaging gateway type provided with Adobe ColdFusion, you can create applications that
send messages to and receive messages from LiveCycle Data Services ES. You configure the Data Services Messaging
gateway and write and test an application that uses the event gateway.

Before you use the Data Services Messaging gateway, become familiar with ColdFusion event gateway principles and
programming techniques (see "Using Event Gateways" on page 1254). Also be familiar with Adobe LiveCycle Data
Services ES.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          1312
Using External Resources




About Flex and ColdFusion
ColdFusion includes the Data Services Messaging event gateway, which uses the ColdFusion event Gateway Adapter
to send messages to and receive messages from the LiveCycle Data Services ES. This means that ColdFusion
applications and Flex applications can publish to and consume events from the same event queue.

Note: To use the Data Services Messaging event gateway to interact with a Flex application, the Flex application must be
running on LiveCycle Data Services ES.


How ColdFusion and Flex interact
You can send messages from a ColdFusion application to a Flex application, through the Data Services Messaging
event gateway. Conversely, you can send messages from a Flex application to a ColdFusion application.

Either the ColdFusion application or the Flex application can initiate sending a message.

1 The Flex application generates a message.

2 The Flex Message Service passes the message to the ColdFusion Event Gateway Adapter.

3 The ColdFusion Event Gateway Adapter sends the message to the Data Services Messaging event gateway, by using
   Java Remote Method Invocation (Java RMI).

4 The Data Services Messaging event gateway and the ActionScript translator convert ActionScript 3.0 data types to
   the appropriate ColdFusion values and add the message to the event gateway queue.y

5 The ColdFusion server calls the onIncomingMessage method of the Data Services Messaging event gateway listener CFC.

6 The ColdFusion application generates a message, which it sends to the ColdFusion server.

7 The ColdFusion server sends the message to the Data Services Messaging event gateway.

8 The Data Services Messaging event gateway and the ActionScript translator convert ColdFusion values to the
   appropriate ActionScript 3.0 data types and then the gateway sends the message to the ColdFusion Event Gateway
   Adapter.

9 The ColdFusion Event Gateway Adapter sends the message to the Flex Message Service.

10 The Flex Message Service passes the message to the Flex application.

Note: The RMI registry, which facilitates communication between the ColdFusion Event Gateway Adapter and the Data
Services Messaging event gateway uses port 1099, which is the default port for Java RMI. You cannot change this port
number. To ensure that the RMI registry provides registry service for both LiveCycle Data Services and ColdFusion, start
LiveCycle Data Services first, and then start ColdFusion. If you stop Flex, restart LiveCycle Data Services, and then restart
the gateway.


Application development and deployment process
The following is a typical process for developing and deploying a ColdFusion application that communicates with a
Flex application through the Data Services Messaging event gateway:

1 Design your application.

2 Configure a Data Services Messaging event gateway instance.

3 Write your ColdFusion CFCs, CFM pages, and any other application elements.

4 Test your application using Flex Enterprise Services 2.

5 Make the application publicly available.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        1313
Using External Resources




Configuring a Data Services Messaging event gateway
Although you can configure an instance of a Data Services Messaging event gateway by creating a configuration file
and specifying that file as the configuration file when you create an instance of the event gateway, you can also provide
the configuration information in the message sent from the Flex application. You provide configuration information
to the Data Services Messaging event gateway in a configuration file to do either of the following:

· Have the Data Services Messaging event gateway send messages to Flex Enterprise Services 2 on a different
   computer

· Use the Data Services Messaging event gateway with a specific Flex destination, and ignore any destination
   specified in the message

The Data Services Messaging event gateway configuration file is a simple Java properties file that contains the following
properties:


 Property                Description

 destination             A hard-coded destination. If you specify this value, any destination information in the message is ignored.

 host                    The host name or IP address of the Flex Enterprise Services 2 server.


The following example is a configuration file:

#
# Flex event gateway configuration
#


# This is the destination of the messages.
destination=Gateway1


# host name or IP address of the Flex Enterprise Server
host=127.0.0.1

If you create a configuration file, save it in the cf_root/gateway/config/ directory, with the extension .cfg.


Sending outgoing messages
Your ColdFusion application sends a message to a Flex application by doing the following actions:

1 The ColdFusion application sends an outgoing message, in a cfreturn tag in the listener method of the listener
   CFC, or by calling the ColdFusion SendGatewayMessage function.

2 A method provided by the Data Services Messaging gateway gets called when you send an outgoing message.

In outgoing messages sent from CFML, the following structure members are translated to the Flex message:


 Name                    Contents

 body                    Body of the message. Required.

 CorrelationID           Correlation identifier of the message.

 Destination             Flex destination of the message. Required if it is not specified in the configuration file.

 Headers                 If the message contains any headers, the CFML structure that contains the header names as keys and values.

 LowercaseKeys           If the value is set to yes, the structure keys are converted to lowercase during creation of ActionScript types.

 TimeToLive              Number of milliseconds during which this message is valid.




                                                       

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 1314
Using External Resources




In addition, the Data Services Messaging event gateway automatically provides values for the following Flex message
fields:


 Name                   Contents

 MessageID              A UUID that identifies the message.

 Timestamp              Time the message is sent.

 ClientID               ID of the Data Services Messaging event gateway instance.


Note: A single instance of the Data Services Messaging event gateway can send messages to any destination that is
registered with the ColdFusion Event Gateway Adapter. However, if the destination is configured in the Data Services
Messaging gateway configuration file, the destination in the message is ignored.


Sending outgoing message example
The following example from a CFM page creates a structure that contains the message. The destination is the
destination ID specified in the flex-services.xml file for the instance of the Data Services Messaging event gateway to
send the message to. The body is the body of the message. The sendGatewyMessage CFML function sends the message
to the instance of the gateway.

<cfset success = StructNew()>
<cfset success.msg = "E-mail was sent at " & Now()>
<cfset success.Destination = "gateway1">
<cfset ret = SendGatewayMessage("Flex2CF2", success)>

To ensure that properties maintain the correct case, define Flex-related information as follows:

myStruct['mySensitiveProp']['myOtherSensitiveProp']

The following is an example of using headers to send to a specific subtopic of the destination:

<cfset var msg = structnew()>
<cfset msg.destiNation = 'ColdFusionGateway'>
<cfset msg.body = 'somebody'>
<cfset msg['headers']['DSSubtopic'] = 'somesubtopic'>
<cfset sendgatewaymessage('CF2FLEX2' , msg)>



Handling incoming messages
When a Flex application sends a message to a ColdFusion application, the Data Services Messaging event gateway
sends a CFEvent structure to the onIncomingMessage function of the configured CFC, with the following information
mapped to the data of the event:


 Name                   Contents

 body                   Body of the message.

 ClientID               ID of the client that sent the message.

 CorrelationID          Correlation identifier of the message.

 Destination            Flex destination of the message.

 Headers                If the message contains any headers, the CFML structure that contains the header names as keys and values.

 Timestamp              Timestamp of the message.


The incoming message data structure also includes the values of messageID and timeToLive from the Flex message.



                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1315
Using External Resources




Incoming message handling example
The following example places data that is contained in the body of the message from the Flex application into a
structure. It then uses the contents of the structure to generate an e-mail message.

<cfcomponent displayname="SendEmail" hint="Handles incoming message from Flex">
     <cffunction name="onIncomingMessage" returntype="any">
     <cfargument name="event" type="struct" required="true">


           <!--- Create a structure to hold the message object sent from Flex--->
           <cfset messagebody = event.data.body>


           <!--- Populate the structure. --->
           <cfset mailfrom="#messagebody.emailfrom#">
           <cfset mailto="#messagebody.emailto#">
           <cfset mailsubject="#messagebody.emailsubject#">
           <cfset mailmessage ="#messagebody.emailmessage#">


           <!--- Send e-mail with values from the structure. --->
           <cfmail from="#mailfrom#"
                to="#mailto#"
                subject="#mailsubject#">
                <cfoutput>#mailmessage#</cfoutput>
           </cfmail>
     </cffunction>
</cfcomponent>

If the Flex application sends the message in the header instead of in the body, you create and populate the structure,
as the following example shows:

<cfset messageheader = StructNew()>
<cfset messageheader.sendto = event.data.headers.emailto>
<cfset messageheader.sentfrom = event.data.headers.emailfrom>
<cfset messageheader.subject = event.data.headers.emailsubject>
<cfset messageheader.mailmsg = event.data.headers.emailmessage>


<cfset mailfrom="#messageheader.sentfrom#">
<cfset mailto="#messageheader.sendto#">
<cfset mailsubject="#messageheader.subject#">
<cfset mailmessage ="#messageheader.mailmsg#">


New methods introduced in ColdFusion 9.0.1
The following new methods have been introduced in ColdFusion Messaging Gateway CFCs:

· allowSend

· allowSubscribe

    Both the methods take subtopic as the parameter.

    These methods help you place control over subscribing and sending data to a particular subtopic.

Note: To call these methods on their gateway CFC, specify the gateway id under the messaging destination in messaging-
config.xml (Web_INF/Flex). By default, the value is *.


Data translation
The following table lists the ColdFusion data types and the corresponding Flash or ActionScript data type:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               1316
Using External Resources




ColdFusion data type                  Flash data type

String                                String

Array                                 [] = Array

Struct                                {} = untyped Object

Query                                 ArrayCollection

CFC                                   Class = typed Object (if a matching ActionScript class exists, otherwise the CFC becomes a
                                      generic untyped Object (map) in ActionScript)

CFC Date                              ActionScript Date

CFC String                            ActionScript String

CFC Numeric                           ActionScript Numeric

ColdFusion XML Object                 ActionScript XML Object




Using the Data Management Event Gateway

Using the Data Management event gateway type provided with Adobe ColdFusion, you can have ColdFusion
applications notify Adobe Flex applications when data managed by a destination has changed. You configure the Data
Management event gateway and write an application that uses the event gateway.

Before using the Data Management event gateway, become familiar with ColdFusion event gateway principles and
programming techniques (see "Using Event Gateways" on page 1254). Also, be familiar with LiveCycle Data Services ES.


About ColdFusion and Flex
ColdFusion includes the Data Management event gateway, which uses the ColdFusion Data Service Adapter to send
messages to LiveCycle Data Services ES. This means that ColdFusion applications can notify a Flex application about
changes in the data that the destination manages.

Note: To use the Data Management event gateway to send messages to a Flex application, the Flex application must be
running on LiveCycle Data Services ES.


How ColdFusion and Flex interact
You can send messages from a ColdFusion application to a Flex application through the Data Management event
gateway. This gateway type only lets you send messages from a ColdFusion application to a Flex application.

The following image shows the process in which a message is sent from the ColdFusion application to the Flex
application:


                                             LiveCycle
                                          Data Services             ColdFusion
 ColdFusion           ColdFusion               event                    Data                  Message                    Flex
 application            server                gateway                  Service                 Service               application
                                                                      Adapter
                                           ActionScript
                                             translator


1 The ColdFusion application generates a message, which it sends to the ColdFusion server.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    1317
Using External Resources




2 The ColdFusion server sends the message to the Data Management event gateway.

3 The Data Management event gateway and the ActionScript translator convert ColdFusion values to the appropriate
   ActionScript 3.0 data types, and then the gateway sends the message to the ColdFusion Data Service Adapter.

4 The ColdFusion Data Service Adapter sends the message to the LiveCycle Data Services Message Service.

5 The Message Service passes the message to the Flex application.

   If you are running LiveCycle Data Services ES on the ColdFusion server, communication between LiveCycle Data
   Services ES and ColdFusion does not use RMI.

   If you are running LiveCycle Data Services ES remotely, to ensure that the RMI registry provides registry service
   for both LiveCycle Data Services ES and ColdFusion, start LiveCycle Data Services ES first, and then start
   ColdFusion. If you stop LiveCycle Data Services ES, restart LiveCycle Data Services ES, and then restart the
   gateway.

   If you are running LiveCycle Data Services ES remotely, the RMI registry, which facilitates communication between
   the ColdFusion Data Service Adapter and the Data Management event gateway uses port 1099. This port is the
   default value for Java RMI. You can change the port number by adding -Dcoldfusion.rmiport=1234, replacing
   1234 with the appropriate port number, to the Java JVM arguments on both the ColdFusion server and the Flex
   server.


Application development and deployment process
The following is a typical process for developing and deploying a ColdFusion application that communicates with a
Flex application through the Data Management event gateway:

1 Design your application.

2 Configure a Data Management event gateway instance.

3 Write your ColdFusion CFCs, CFM pages, and any other application elements.

4 Test your application using LiveCycle Data Services ES.

5 Make the application publicly available.


Configuring a Data Management event gateway
Although you can configure an instance of a Data Management event gateway by creating a configuration file and
specifying that file as the configuration file when you create an instance of the event gateway, you can also provide the
configuration information in the message. You provide configuration information to the Data Management event
gateway in a configuration file to do either of the following:

· Have the Data Management event gateway send messages to LiveCycle Data Services ES on a different computer.

· Use the Data Management event gateway with a specific Flex destination, and ignore any destination specified in
   the message.

The Data Management event gateway configuration file is a simple Java properties file that contains the following
properties:


Property                 Description

destination              A hard-coded destination. If you specify this value, any destination information in the message is ignored.

host                     The host name or IP address of the LiveCycle Data Services ES server. Omit the host name if you are running
                         LiveCycle Data Services ES as part of ColdFusion.


The following example is a configuration file:



                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         1318
Using External Resources




#
# Data Management event gateway configuration
#
# This is the destination where messages are sent.
destination=myDestination


# Host name or IP address of the LiveCycle Data Services ES Server
host=127.0.0.1

If you create a configuration file, save it in the cf_root/gateway/config/ directory, with the extension .cfg.

Note: A single instance of the Data Management event gateway can send messages to any destination that is registered
with the ColdFusion Data Service Adapter. However, if the destination is configured in the Data Management event
gateway configuration file, the destination in the message is ignored.


Sending messages
Your ColdFusion application sends a message to a Flex application by calling the ColdFusion SendGatewayMessage
function. In messages sent from CFML, the following structure members are translated to the Flex message:


 Name                       Contents

 destination                Destination of the message. This entry is required if it is not specified in the configuration file.

 action                     Required. The notification action that is being performed: create, delete, deleteID, refreshfill, or
                            update.


 item                       Required when action="create" or action="delete". The record that was added or deleted.

 identity                   Required when action="deleteID". A structure that contains the identity properties (primary key) of the
                            record that was deleted.

 fillparameters             Optional. An array that contains the fills parameters that specify which fill operations to refresh.

 newversion                 Required when action="update". The record that was updated.

 previousversion            Optional. The previous record, before the update. This entry is used for conflict resolution.

 changes                    Optional when action="update". A comma-delimited list or array of property names that were updated in
                            the record. If you omit this entry, ColdFusion assumes that all properties changed. When you change a large
                            number of records, you specifying the property names can improve performance.

                            Required when action="batch". An array of structures that contain the changes. You can batch multiple
                            changes and send them in a single notification. The changes can be of different types, for example five updates,
                            one delete, and two creates. Each event structure must contain an action.



Example
The following example creates a structure for each event type. It then creates a structure that contains the message. The
message structure contains an array of event structures to send to Flex. The destination is the destination ID specified
in the flex-services.xml file for the instance of the Data Management event gateway to send the message to. The body
is the body of the message. The sendGatewyMessage CFML function sends the message to the instance of the gateway.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                               1319
Using External Resources




<cfscript>
// Create event
createEvent = StructNew();
createEvent.action = "create";
createEvent.item = newContact;


// Create update notification
updateEvent = StructNew();
updateEvent.action="update";
updateEvent.previousversion = oldContact;
updateEvent.newversion = updatedContact;


// Create delete notification
identity = StructNew();
identity["contactId"] = newId;
deleteEvent = StructNew();
deleteEvent.action = "deleteID";
deleteEvent.identity = identity;


// Send a batch notification
all = StructNew();
all.destination="cfcontact";
all.action="batch";
all.changes = ArrayNew(1);
all.changes[1] = createEvent;
all.changes[2] = updateEvent;
all.changes[3] = deleteEvent;
r = sendGatewayMessage("LCDS", all);
</cfscript>



Data translation
The following table lists the ColdFusion data types and the corresponding Adobe Flash or ActionScript data type:


ColdFusion data type                  Flash data type

String                                String

Array                                 [] = Array

Struct                                {} = untyped Object

Query                                 ArrayCollection

CFC                                   Class = typed Object (if a matching ActionScript class exists, otherwise the CFC becomes a
                                      generic untyped Object (map) in ActionScript)

CFC Date                              ActionScript Date

CFC String                            ActionScript String

CFC Numeric                           ActionScript Numeric

ColdFusion XML Object                 ActionScript XML Object




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1320
Using External Resources




Creating Custom Event Gateways

Adobe ColdFusion lets you create event gateways. Building a gateway requires a knowledge of Java programming,
including Java event-handling and thread-handling concepts, and of the technology to which you are providing the
gateway, including the types of messages that you handle. This documentation also assumes that you have a thorough
knowledge of ColdFusion development concepts and practices, including ColdFusion components (CFCs).


Event gateway architecture
A ColdFusion event gateway listens for events and passes them to ColdFusion for handling by the application listener
CFC or CFCs. It must implement the coldfusion.eventgateway.Gateway interface, and use the ColdFusion
GatewayServices class. The following image expands on the basic event handling architecture diagram to show how a
ColdFusion event gateway works:


       External event                                                                            CFEvent        Listener
         generator /       event       Listener thread
                                                                                                 Message          CFC
           receiver                       addEvent                  CFEvent
                                           method

                           event                                                                                Listener
                                                                                                                  CFC
                                         outgoingMessage
                                                                    CFEvent
              .                                method
                                                                                  ColdFusion                        .
              .
                                                                                    Event                           .
              .
                                                                                  Gateway                           .
              .                          Event Gateway
                                                                                   Services

                                                 .
                                                                                                 CFEvent          CFC
                                                 .
                                                 .
                                                                                                                Applicat
                                                                                                                 CFML
                                                                                                 Message
       External event     event                                     CFEvent                                      Page
          generator /                    Event Gateway
           receiver        event                                    CFEvent                                   Application



Receiving messages: The event gateway listener thread receives events from an external event source such as a socket
or SMSC server, and calls the GatewayServices addEvent method to send a CFEvent instance to ColdFusion.

Sending messages: The ColdFusion event gateway service calls the outgoingMessage method of the event gateway
and passes it a CFEvent instance with the destination and message information. The event gateway forwards the
message as appropriate to the external receiver.

The event gateway architecture is not limited to handling messages from external sources, such as SMS devices or IM
clients. It can also be used to handle events that are internal to the local system or even the ColdFusion application.
Also, a gateway does not have to implement two-way communications.

The sample directory watcher gateway provided with ColdFusion is an example of an internal, one way, gateway. It
has a single thread that periodically checks a local directory and sends a message to a CFC when the directory contents
change. This gateway does not support outgoing messages. (The code for this gateway is in the
gateway/src/examples/watcher directory.)

Another internal gateway, the asynchronous CFML gateway, is provided as part of the ColdFusion product. Unlike
most gateways, it does not have a listener thread. Its outgoingMessage method gets messages from CFML
SendGatewayMessage functions, and dispatches them to a CFC onIncomingMessage method for handling. This
gateway lets ColdFusion support request-free asynchronous processing. For more information on using this gateway,
see "Using the CFML event gateway for asynchronous CFCs" on page 1269.


                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  1321
Using External Resources




Event gateway elements
You use the following the elements to create and configure a gateway:

· "Gateway interface" on page 1321

· "GatewayServices class" on page 1322

· "CFEvent class" on page 1322

· "GatewayHelper class" on page 1323

· "Gateway configuration file" on page 1324

· "Gateway development classes" on page 1324

Note: The gateway interfaces and classes, except for the GenericGateway class, are fully documented in Gateway
development interfaces and classes in the CFML Reference. All interfaces and classes in this list, including the
GenericGateway class, are documented in less detail in the JavaDocs located in the ColdFusion gateways\docs directory.
The JavaDocs documentation lacks examples and does not have the detailed usage information that you find in the CFML
Reference.


Gateway interface
The ColdFusion event gateway must implement the coldfusion.eventservice.Gateway interface. The following table
lists the interface method signatures:

Note: For detailed information on implementing each method, see "Building an event gateway" on page 1325. For
reference pages for these methods, see Gateway interface in the CFML Reference.


 Signature                                          Description

 void setGatewayID(String id)                       Sets the gateway ID that uniquely identifies the gateway instance. ColdFusion
                                                    calls this method before it starts the event gateway, even if the gateway class
                                                    constructor also sets the ID.

 void setCFCListeners(String[] listeners)           Identifies the CFCs that listen for incoming messages from the event gateway.
                                                    The array contains one or more paths to the listener CFCs. ColdFusion calls this
                                                    method before it starts the event gateway, and if the configuration for a running
                                                    event gateway changes.

 GatewayHelper getHelper()                          Returns a coldfusion.eventgateway.GatewayHelper class instance, or null. The
                                                    GatewayHelper class provides event gateway-specific utility methods to CFML
                                                    applications. ColdFusion calls this method when a ColdFusion application calls
                                                    the GetGatewayHelper function.

 String getGatewayID()                              Returns the gateway ID.

 int getStatus()                                    Gets the event gateway status. The interface defines the following status
                                                    constants: STARTING, RUNNING, STOPPING, STOPPED, FAILED.

 void start()                                       Starts the event gateway. Starts at least one thread for processing incoming
                                                    messages. ColdFusion calls this method when it starts an event gateway.

 void stop()                                        Stops the event gateway. Stops the threads and destroys any resources.
                                                    ColdFusion calls this method when it stops an event gateway.

 void restart()                                     Restarts a running event gateway. ColdFusion calls this method when the
                                                    ColdFusion Administrator restarts a running event gateway.

 String outgoingMessage (coldfusion.                Handles a message sent by ColdFusion and processes it as needed by the
 eventgateway.CFEvent cfmessage)                    gateway type to send a message. ColdFusion calls this method when the listener
                                                    method of a listener CFC returns a CFEvent or when a ColdFusion application
                                                    calls the SendGatewayMessage function. The CFML SendGatewayMessage
                                                    function gets the returned String as its return value.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     1322
Using External Resources




GatewayServices class
The Gateway class uses the coldfusion.eventgateway.GatewayServices class to interact with the ColdFusion event
gateway services. This class has the following methods:


Signature                                          Description

GatewayServices getGatewayServices                 Static method that returns the GatewayServices object. Gateway code can call
                                                   this method at any time, if necessary.

boolean addEventmsg)                               Sends a CFEvent instance to ColdFusion for dispatching to a listener CFC. The
                                                   event gateway uses this method to send all incoming messages to the
                                                   application for processing. Returns False if the event is not added to the queue.

int getQueueSize                                   Returns the current size of the ColdFusion event queue. This queue handles all
                                                   messages for all gateways.

int getMaxQueueSize                                Returns the maximum size of the ColdFusion event queue, as set in the
                                                   ColdFusion Administrator.

Logger getLogger                                   Returns a ColdFusion Logger object that the event gateway can use to log
                                                   information in the eventgateway.log log file (the default) or the specified log file.
Logger getLoggerlogfile)
                                                   The logfile attribute must be a filename without a filename extension, such as
                                                   mylogifile. ColdFusion places the file in the ColdFusion logs directory and
                                                   appends .log to the specified filename.

                                                   For information on using the logger object, see "Logging events and using log
                                                   files" on page 1332.



CFEvent class
The Gateway class sends and receives CFEvent instances to communicate with the ColdFusion listener CFC or
application. The Gateway notifies ColdFusion of a message by sending a CFEvent instance in a
GatewayServices.addEvent method. Similarly, the Gateway receives a CFEvent instance when ColdFusion calls the
gateway outgoingMessage method.

The CFEvent class extends the java.util.Hashtable class and has the following methods to construct the instance and
set and get its fields. (In CFML, you treat CFEvent instances as structures.)


Methods                                             Description

CFEventgatewayID)                                   CFEvent constructor. The gatewayID parameter must be the value that is passed
                                                    in the gateway constructor or set using the Gateway setGatewayID method.

void setGatewayTypetype)                            Identifies the type of event gateway, such as SMS. For the sake of consistency,
                                                    use this name in the Type Name field when you add an event gateway type on
String getGatewayType
                                                    the Gateway Types page in the ColdFusion Administrator.

void setDatadata)                                   The event data; includes the message being passed to or from ColdFusion. The
                                                    content of the field depends on the event gateway type. The Map keys must be
Map getData
                                                    strings.

                                                    Because ColdFusion is not case sensitive, it converts the Map passed in the
                                                    setData method to a case-insensitive Map. As a result, do not create entries in
                                                    the data with names that differ only in case.

void setOriginatorIDid)                             Identifies the originator of an incoming message or the destination of an
                                                    outgoing message. The value depends on the protocol or event gateway type.
String getOriginatorID

void setCFCPathpath)                                An absolute path to the application listener CFC that processes the event. By
                                                    default, ColdFusion uses the first path configured for the event gateway
String getCFCPath
                                                    instance on the Event Gateways page in the ColdFusion Administrator.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   1323
Using External Resources




 Methods                                             Description

 void setCFCMethodmethod)                            The method in the listener CFC that ColdFusion calls to process this event. By
                                                     default, ColdFusion calls the onIncomingMessage method. For the sake of
 String getCFCMethod
                                                     consistency, Adobe recommends that any event gateway with a single listener
                                                     does not override this default. A gateway, such as the ColdFusion XMPP
                                                     gateway, that uses different listener methods for different message types, uses
                                                     this method to identify the destination method.

 void setCFCTimeoutseconds)                          The time-out, in seconds, for the listener CFC to process the event request.
                                                     When ColdFusion calls the listener CFC to process the event, and the CFC does
 String getCFCTimeout
                                                     not process the event in the specified time-out period, ColdFusion terminates
                                                     the request and logs an error in the application.log file. By default, ColdFusion
                                                     uses the Timeout Request value set on the Server Settings page in the
                                                     ColdFusion Administrator.

 String getGatewayID                                 The event gateway instance that processes the event. Returns the gateway ID
                                                     that was set in the CFEvent constructor.



GatewayHelper class
ColdFusion includes a coldfusion.eventgateway.GatewayHelper Java marker interface. You implement this interface
to define a class that provides gateway-specific utility methods to the ColdFusion application or listener CFC. For
example, an instant messaging event gateway could use a helper class to provide buddy list management methods to
the application.

The Gateway class must implement a getHelper method that returns the helper class or null (if the gateway does not
need such a class).

ColdFusion applications call the GetGatewayHelper CFML function, which calls gateway's the getHelper method to
get an instance of the helper class. The application can then call helper class methods using ColdFusion object dot
notation.

The following code defines the SocketHelper class, the gateway helper for the SocketGateway class. It has an empty
constructor and two public methods: one returns the socket IDs; the other closes a specified socket. These classes let
an application monitor and end session connections.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1324
Using External Resources




public class SocketHelper implements GatewayHelper {
      public SocketHelper() {
      }
      public coldfusion.runtime.Array getSocketIDs () {
           coldfusion.runtime.Array a = new coldfusion.runtime.Array();
           Enumeration e = socketRegistry.elements();
           while (e.hasMoreElements()) {
                a.add(((SocketServerThread)e.nextElement()).getName());
            }
            return a;
       }
      public boolean killSocket (String socketid) {
           try
           {
                ((SocketServerThread)socketRegistry.get(socketid)).socket.close();
                ((SocketServerThread)socketRegistry.get(socketid)).socket = null;
                socketRegistry.remove(socketid);
                return true;
           }
           catch (IOException e) {
                return false;
           }
      }
}


Gateway configuration file
Gateways can use a configuration file to specify information that does not change frequently. For example, the
ColdFusion SMS event gateway configuration file contains values that include an IP address, port number, system ID,
password, and so on.

You can specify a configuration file path for each event gateway instance in the ColdFusion Administrator.
ColdFusion passes the file path in the gateway constructor when it instantiates the event gateway. The configuration
file format and content handling is up to you. It is the responsibility of the gateway class to parse the file contents and
use it meaningfully.

One good way to access and get configuration data is to use the java.util.Properties class. This class takes an ISO8859-
1 formatted input stream with one property setting per line. Each property name must be separated from the value by
an equal sign (=) or a colon (:), as the following example shows:

ip-address=127.0.0.1
port=4445

The example SocketGateway event gateway uses this technique to get an optional port number from a configuration
file. For an example of reading a properties file and using its data, see the code in "Class constructor" on page 1325.


Gateway development classes
ColdFusion provides two classes that you can use as building blocks to develop your event gateway classes. Each
corresponds to a different development methodology:

· The coldfusion.eventgateway.GenericGateway class is an abstract class from which you can derive your gateway
    class.

· The EmptyGateway class in the gateway\src\examples directory is a template gateway that you can complete to
    create your gateway class.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1325
Using External Resources




The GenericGateway class
ColdFusion includes a coldfusion.eventgateway.GenericGateway abstract class that implements many of the methods
of ColdFusion Gateway interface and provides some convenience methods for use in your gateway class.

You can derive your gateway class from this class, which handles the basic mechanics of implementing a gateway, such
as the getGatewayID and SetCFCListeners methods. Your derived class must implement at least the following
methods:

· startGateway (not start)

· stopGateway (not stop)

· outgoingMessage

Your derived gateway class also must implement the following:

· If you support a configuration file, a constructor that takes a configuration file, and configuration loading routines.

· If you use a gatewayHelper class, the getHelper method.

· If the event source status can change asynchronously from the gateway, the getStatus method.

The example JMS gateway is derived from the generic gateway class. The gateway class JavaDocs in the gateway\docs
directory provide documentation for this class. (The CFML Reference does not document this class.)


The EmptyGateway class
The gateway\src\examples\EmptyGateway.java file contains an event gateway template that you can use as a skeleton
for creating your own event gateway. (The gateway directory is in the cf_root directory in the server configuration and
the cf_root\WEB-INF-cfusion directory on J2EE configurations.) This file contains minimal versions of all methods in
the coldfusion.eventgateway.Gateway interface. It defines a skeleton listener thread and initializes commonly used
Gateway properties. The EmptyGateway source code includes comments that describe the additional information that
you must provide to complete an event gateway class.


Building an event gateway
To build a Gateway class, you can start with the EmptyGateway.java file as a template. (In the server configuration, this
file is located in the cf_root/gateway/src/examples/ directory; in the J2EE configuration, the file is in the cf_root/WEB-
INF/cfusion/gateway/src/examples/ directory.) This file defines a nonfunctional event gateway, but has the basic
skeleton code for all Gateway class methods.

Wherever possible, this document uses code based on the sample Socket event gateway to show how to implement
event gateway features. (In the server configuration, this file is
cf_root/gateway/src/examples/socket/SocketGateway.java; in the J2EE configuration, the file is cf_root/WEB-
INF/cfusion/gateway/src/examples/socket/SocketGateway.java.)


Class constructor
An event gateway can implement any of the following constructors:

· MyGateway(String gatewayID, String configurationFile)

· MyGateway(String gatewayID)

· MyGateway()

When ColdFusion starts, it calls the constructor for each event gateway instance that you configure in ColdFusion.
(ColdFusion also calls the gateway Start method after the event gateway is instantiated.). ColdFusion first attempts
to use the two-parameter constructor.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         1326
Using External Resources




Because each event gateway instance must have a unique ID, ColdFusion provides redundant support for providing
the ID. If the event gateway implements only the default constructor, ColdFusion provides the ID by calling the
setGatewayID method of the event gateway.

If the event gateway does not implement the two-parameter constructor, it does not get configuration file information
from ColdFusion.

The constructor normally calls the static GatewayServices.getGatewayServices method to access ColdFusion event
gateway services. Although you need not make this call, it is a good coding practice.

A minimal constructor that takes only a gateway ID could look like the following:

public MyGateway(String gatewayID) {
      this.gatewayID = gatewayID;
      this.gatewayService = GatewayServices.getGatewayServices();
}

The gateway constructor must throw a coldfusion.server.ServiceRuntimeException exception if an error occurs that
otherwise cannot be handled. For example, throw this exception if the event gateway requires a configuration file and
cannot read the file contents.

If your gateway uses a configuration file, have the constructor load the file, even if the Start method also loads the file.
Load the file because the constructor does not run in an independent thread, and ColdFusion can display an error in
the ColdFusion Administrator of the file fails to load. If the Start method, which does run in a separate thread, fails
to load the file, ColdFusion logs the error, but it cannot provide immediate feedback in the administrator.

The sample Socket event gateway has a single constructor that takes two parameters. It tries to load a configuration
file. If you specify a configuration file in the ColdFusion Administrator, or the file path is invalid, it gets an IO
exception. It then uses the default port and logs a message indicating what it did. The following example shows the
Gateway constructor code and the loadProperties method it uses:

public SocketGateway(String id, String configpath)
{
      gatewayID = id;
      gatewayService = GatewayServices.getGatewayServices();
      // log things to socket-gateway.log in the CF log directory
      log = gatewayService.getLogger("socket-gateway");
      propsFilePath=configpath;
      try
      {
           FileInputStream propsFile = new FileInputStream(propsFilePath);
           properties.load(propsFile);
           propsFile.close();
           this.loadProperties();
        }
        catch (IOException e)
      {
           // Use default value for port and log the status.
           log.warn("SocketGateway(" + gatewayID + ") Unable to read configuration
                 file '" + propsFilePath + "': " + e.toString() + ".Using default port
                 " + port + ".", e);
      }
}


private void loadProperties() {
      String tmp = properties.getProperty("port");
      port = Integer.parseInt(tmp);
}



                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1327
Using External Resources




Providing Gateway class service and information routines
Several gateway methods perform event gateway configuration services and provide event gateway information. The
ColdFusion event gateway services call many of these methods to configure the event gateway by using information
stored by the ColdFusion Administrator, and to get access to resources and information that the event gateway services
and applications require. Some of these methods can also be useful in event gateway code. The following methods
provide these services and information:

· setCFCListeners

· setGatewayID

· getHelper

· getGatewayID

· getStatus

ColdFusion calls the setCFCListeners method with the CFC or CFCs that are specified in the ColdFusion
Administrator when it starts a gateway. ColdFusion also calls the method in a running event gateway when the
configuration information changes, so the method must be written to handle such changes. The setCFCListeners
method must save the listener information so that the gateway code that dispatches incoming messages to gateway
services can use the listener CFCs in setCFCPath methods.

ColdFusion calls the setGatewayID method when it starts a gateway. The getGatewayID method must return the
value set by this method.

ColdFusion calls the getHelper method when an application calls the CFML GetGatewayHelper function.

The following code shows how the SocketGateway class defines these methods. To create a gateway, modify the
getHelper definition to return the correct class, or to return null if no gateway helper class exists. Most gateways can
leave the other method definitions unchanged.

public void setCFCListeners(String[] listeners) {
     this.listeners = listeners;
}
public GatewayHelper getHelper() {
     // SocketHelper class implements the GatewayHelper interface.
     return new SocketHelper();
}
public void setGatewayID(String id) {
     gatewayID = id;
}
public String getGatewayID() {
     return gatewayID;
}
public int getStatus() {
     return status;
}


Starting, stopping, and restarting the event gateway
Because an event gateway uses at least one listener thread, it must have start, stop, and restart methods to control
the threads. These methods must also maintain the status variable that the Gateway class getStatus method checks,
and change its value among STARTING, RUNNING, STOPPING, STOPPED, and FAILED, as appropriate.


The start method
The start method initializes the event gateway. It starts one or more listener threads that monitor the event source
and respond to any messages it receives from the source.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1328
Using External Resources




The start method should return within a time-out period that you can configure for each event gateway type in the
ColdFusion Administrator. If it does not, the ColdFusion Administrator has a Kill on Startup Timeout option for each
gateway type. If you select the option, and a time-out occurs, the ColdFusion starter thread calls an interrupt on the
gateway thread to try to kill it, and then exits.

Note: If the start method is the listener (for example, in a single-threaded gateway), the method does not return until
the gateway stops. Do not set the Kill on Startup Timeout option in the ColdFusion Administrator for such gateways.

If the gateway uses a configuration file, load the configuration from the file. Doing so lets users change the
configuration file and restart the gateway without restarting ColdFusion. Also load the configuration file in the
constructor; for more information, see "Class constructor" on page 1325.

In the SocketGateway class, the start method starts an initial thread. (In a single-threaded Gateway, this thread would
be the only one.) When the thread starts, it calls a socketServer method, which uses the Java ServerSocket class to
implement a multi-threaded socket listener and message dispatcher. For more information on the listener, see
"Responding to incoming messages" on page 1329.

public void start()
{
     status = STARTING;
     listening=true;
     // Start up event generator thread
     Runnable r = new Runnable()
     {
           public void run()
           {
                socketServer();
           }
     };
     Thread t = new Thread(r);
     t.start();
     status = RUNNING;
}


The stop method
The stop method performs the event gateway shutdown tasks, including shutting down the listener thread or threads
and releasing any resources. The following example shows the SocketGateway stop method:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               1329
Using External Resources




public void stop()
{
    // Set the status variable to indicate that the server is stopping.
    status = STOPPING;
    // The listening variable is used as a switch to stop listener activity.
    listening=false;
    // Close the listener thread sockets.
    Enumeration e = socketRegistry.elements();
    while (e.hasMoreElements()) {
          try
          {
                ((SocketServerThread)e.nextElement()).socket.close();
          }
          catch (IOException e1)
          {
                // We don't care if a close failed.
                //log.error(e1);
          }
    }
    // Close and release the serverSocket instance that gets requests from the
    // network.
    if (serverSocket != null) {
          try
          {
                serverSocket.close();
          }
          catch (IOException e1)
          {
          }
          //Release the serverSocket.
          serverSocket = null;
    }
    // Shutdown succeeded; set the status variable.
    status = STOPPED;
}


The restart method
In most cases, you implement the restart method by calling the stop method and the start method consecutively,
but you could be able to optimize this process for some services. The following code shows the SocketGateway class
restart method:

public void restart() {
    stop();
    start();
}


Responding to incoming messages
One or more listener threads respond to incoming messages (events). The threads must include code to dispatch the
messages to ColdFusion event gateway services, as follows:

1 Create a CFEvent instance.

2 Create a Map instance that contains the message and any other event gateway-specific information, and pass it to
   the CFEvent setData method.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1330
Using External Resources




3 Call the CFEvent setOriginator method to specify the source of the message. (This call is required if the
    ColdFusion application sends a response.)

4 Call the CFEvent setGateWayType method to specify the event gateway type.

5 Set any other CFEvent fields where the default behavior is not appropriate; for example, call the setCFCPath
    method to replace the default listener CFC. (For information on default CFEvent fields, see "CFEvent class" on
    page 1322.)

6 Call the gatewayService.addEvent method to dispatch the CFEvent instance to ColdFusion.

7 Handle cases where the event is not added to the event gateway service queue (the addEvent method returns False).

If your application sends any messages to multiple listener CFCs, the gateway must create and configure a CFEvent
instance and call the gatewayService.addEvent method to send the message to each separate listener CFC. The
setCFCListeners method of the gateway must make the CFC paths available to the gateway for configuring the
CFEvent instances.

If your ColdFusion server carries a heavy event gateway message load, the ColdFusion event gateway services event
queue could reach the maximum value set in the ColdFusion Administrator. When the queue reaches the maximum,
the gatewayService.addEvent method returns False and fails. Your code can do any of the following:

· Return a message to the sender to indicate that their message was not received.

· Wait until the queue is available by periodically comparing the values returned by the GatewayService
    getQueueSize and getMaxQueueSize methods, and retry the addEvent method when the queue size is less than
    the maximum.

· Log the occurrence using the logger returned by the GatewayService getLogger method. (For more information,
    see "Logging events and using log files" on page 1332.)

The SocketGateway class implements the listener using a java.net.ServerSocket class object and SocketServerThread
listener threads. (See the SocketGateway source for the SocketServerThread code.) When the listener thread gets a
message from the TCP/IP socket, it calls the following processInput method to dispatch the message to ColdFusion.
This method explicitly sets all required and optional CFEvent fields and sends the event to ColdFusion. If the
addEvent call fails, it logs the error.

Note: Much of the processInput method code supports multiple listener CFCs. A gateway that uses only a single listener
CFC, would require only the code in the latter part of this method.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               1331
Using External Resources




private void processInput(String theInput, String theKey)
{
      // Convert listeners list to a local array
      // Protect ourselves if the list changes while we are running
      String[] listeners;
      int size = cfcListeners.size();
      if (size > 0)
      {
          // Capture the listeners list
          synchronized (cfcListeners)
          {
                listeners = new String[size];
                cfcListeners.toArray(listeners);
          }
      }
      else
      {
          // Create a dummy list
          listeners = new String[1];
          listeners[0] = null;
      }
      // Broadcast to all the CFC listeners
      // Send one message at a time with different CFC address on them
      for (int i = 0; i < listeners.length; i++)
      {
          String path = listeners[i];
          CFEvent event = new CFEvent(gatewayID);
          Hashtable mydata = new Hashtable();
          mydata.put("MESSAGE", theInput);
          event.setData(mydata);
          event.setGatewayType("SocketGateway");
          event.setOriginatorID(theKey);
          event.setCfcMethod(cfcEntryPoint);
          event.setCfcTimeOut(10);
          if (path != null)
                event.setCfcPath(path);
                boolean sent = gatewayService.addEvent(event);
          if (!sent)
                log.error("SocketGateway(" + gatewayID + ") Unable to place message on
                     event queue. Message not sent from " + gatewayID + ", thread " + theKey
                     + ".Message was " + theInput);
      }
}


Responding to a ColdFusion function or listener CFC
The ColdFusion event gateway services call the outgoingMessage method of the gateway to handle messages
generated when the listener method of an event gateway application listener CFC returns a message or any CFML code
calls a SendGatewayMessage function. This method must send the message to the appropriate external resource.

The outgoingMessage method parameter is a CFEvent instance, containing the information about the message to
send out. The CFEvent getData method returns a Map object that contains event gateway-specific information about
the message, including any message text. All CFEvent instances received by the outgoingMessage contain information
in the Data and GatewayID fields.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1332
Using External Resources




CFEvent instances returned from listener CFC onIncomingMessage methods include the originator ID of the
incoming message and other information. However, a gateway that could handle messages from the ColdFusion
SendGatewayMessage function cannot rely on this information being available, so it is good practice to require that
all outgoing messages include the destination ID in the data Map.

The outgoingMessage method returns a String value. The CFML sendGatewayMessage function returns this value
to the ColdFusion application. Indicate the status of the message in the returned string. By convention, ColdFusion
event gateway outgoingMessage methods return "OK" if they do not encounter errors and do not have additional
information (such as a message ID) to return.

Because event messages are asynchronous, a positive return normally does not indicate that the message was successful
delivered, only that the outgoingMessage method successfully handled the message. In some cases, however, it is
possible to make the outgoingMessage method at least partially synchronous. The SMS gateway, for example,
provides two outgoingMessage modes:

Asynchronous mode The outgoingMessage method returns when the message is queued internally for delivery to the
short message service center (SMSC) of the messaging provider.

Synchronous mode The method does not return until the message is delivered to the SMSC, or an error occurs.

This way, an SMS application can get a message ID for later use, such as to compare with a message receipt.


Example outgoingMessage method
The following outgoingMessage method is like the version in the SocketGateway class. It does the following:

1 Gets the contents of a MESSAGE field of the Data Map returned by the CFEvent class getData method.

2 Gets the destination from an outDestID field in the data Map.

3 Uses the socket server thread of the destination to write the message.

public String outgoingMessage(coldfusion.eventgateway.CFEvent cfmsg) {
     String retcode="ok";
     // Get the table of data returned from the event handler
     Map data = cfmsg.getData();
     String message = (String) data.get("MESSAGE");
     // Find the right socket to write to from the socketRegistry hash table
     // and call the writeoutput method of the socket thread.
     // (Get the destination ID from the data map.)
     if (data.get("outDestID") != null)
          ((SocketServerThread)socketRegistry.get(data.get("outDestID"))).
                     writeOutput(message);
     else {
          System.out.println("cannot send outgoing message. OriginatorID is not
                available.");
          retcode="failed";
          }
     return retcode;
}


Logging events and using log files
The GatewayServices.getLogger method returns an instance of the coldfusion.eventgateway.Logger class that you
can use to log messages to a file in the ColdFusion logs directory. (You set this directory on the ColdFusion
Administrator Logging Settings page.) The method can take no parameter, or one parameter:

· The default GatewayServices.getLogger method uses the eventgateway.log file.

· Optionally, you can specify a log filename, without the .log extension or directory path.


                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1333
Using External Resources




The following example tells ColdFusion to log messages from the gateway to the mygateway.log file in the ColdFusion
logs directory:

coldfusion.eventgateway.Logger log =getGatewayServices().getLogger("mygateway");

The Logger class has the following methods, all of which take a message string. The method you use determines
severity level that is set in the log message.

· info

· warn

· error

· fatal

You can also pass these methods an exception instance as a second parameter. When you pass an exception,
ColdFusion places the exception information in the exception.log file in the ColdFusion logs directory.

You can use these methods to log any messages that you find appropriate. If you use the default eventgateway.log file,
however, remember that all ColdFusion standard gateways us it, and other gateways could use it. As a result, limit the
messages that you normally log to this file to infrequent normal occurrences (such as gateway startup and shutdown)
or errors for which you want to retain data.

ColdFusion uses the following format for the message text, so have your application follow this pattern:

GatewayType (Gateway ID) Message

The SMS event gateway, for example, includes the following exception catching code, which logs a general exception
messages and the exception name in the eventgateway.log file, and also (automatically) logs the exception in the
exceptions.log file:

catch(Exception e)
{
      logger.error("SMSGateway (" + gatewayID + ") Exception while processing
      incoming event: " + e, e);
}

Note: When you are developing an event gateway application, you can use the ColdFusion Log viewer to inspect your log
files and the standard ColdFusion log files, including the eventgateway.log file and exception.log file. You can use the
viewer to filter the display, for example, by selecting different severity levels, or searching for keywords.


Deploying an event gateway
To deploy an event gateway, you deploy and event gateway type and configure one or more event gateway instances.


Deploy an event gateway type in ColdFusion
1 Compile your Gateway class and place it in a JAR file along with any other required classes.

    Note: The ColdFusion class loader includes the gateway \lib directory on its classpath and includes any JAR files that
    are in that directory on the class path.

2 Place the JAR file in the cf_root\WEB-INF\cfusion\gateway\lib directory on J2EE configurations or the
    cf_root\gateway\lib directory on server configurations. This directory is on the ColdFusion classpath.

3 Ensure that ColdFusion event gateway services are enabled on the ColdFusion Administrator Data & Services >
    Event Gateway > Settings page.

4 On the ColdFusion Administrator Data & Services > Event Gateways page, click the Manage Gateway Types button
    to display the Gateway Types page.



                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1334
Using External Resources




5 On the Add/Edit ColdFusion Event Gateway Types form, enter a type name (for example, SMS for the SMS event
   gateway), a description, and the full Java class name (for example, coldfusion.eventgateway.sms.SMSGateway for
   the SMS event gateway). If appropriate, change the Startup Timeout settings from the default values.

6 Click the Add Type button to deploy the event gateway type in ColdFusion.

The following procedure describes how to configure an event gateway instance that uses the gateway type.


Configure an event gateway instance
1 If you have finished deploying the event gateway type, click the Manage Gateway Instances button; otherwise, select
   Event Gateways > Gateway Instances in the ColdFusion Administrator.

2 On the Add/Edit ColdFusion Event Gateways Instances form, do the following:

   · Enter the instance name in the Gateway ID field

   · Select the event gateway type that you added from the Gateway Type menu

   · Specify the paths to the listener CFC or CFCs that handle the messages.

   · If the event gateway requires a configuration file, enter the path to the file in Gateway Configuration File field.

   · If you do not want the gateway to start up automatically when ColdFusion starts, change the Startup Mode
      selection to Manual or Disabled

3 Click the Add Gateway Instance button.

4 In the list of configured instances, click the start button (green triangle) on the entry for the gateway instance to
   start the instance.



Using the ColdFusion Extensions for Eclipse

The Adobe ColdFusion Extensions for Eclipse include wizards that help generate code for common tasks and an
extension that lets you connect to remote servers from Adobe Flash Builder and Eclipse.

To use the ColdFusion Extensions for Eclipse, you should be familiar with ColdFusion components, as well as
accessing and using data in ColdFusion applications. You should also be familiar with Eclipse or Adobe Flash Builder.


About the ColdFusion Extensions for Eclipse
To make some common coding tasks easier, the ColdFusion Extensions for Eclipse include the following:

· Eclipse RDS Support plug-in, which lets you access files and data sources on a ColdFusion server.

· ColdFusion/Flex Application wizard, which lets you create master and detail pages in an application to create, read,
   update, and delete records in a database.

· ColdFusion/Ajax Application wizard, which lets you create master and detail pages that use Ajax elements in an
   application to create, read, update, and delete records in a database.

· RDS CRUD wizard, which lets you dynamically create a ColdFusion component (CFC) based on a table that is
   registered in the ColdFusion Administrator on a ColdFusion server

· ActionScript to CFC wizard, which lets you create a CFC based on an ActionScript class file.

· CFC to ActionScript wizard, which lets you create an ActionScript file based on a CFC Value Object

· Services Browser, which lets you browse CFCs, manage a list of web services, and generate the CFML code to invoke
   a web service.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           1335
Using External Resources




For information about installing the ColdFusion Extensions for Eclipse, see Installing ColdFusion guide.


Eclipse RDS Support
Remote Development Services (RDS) lets you access files and data sources registered in the ColdFusion Administrator
on a ColdFusion server. To use Eclipse RDS Support, you must enable RDS when you install ColdFusion. With Eclipse
RDS Support, you can use Flash Builder or CFEclipse as your IDE and access ColdFusion files remotely.

Eclipse RDS Support is supported on all ColdFusion server platforms.

Before you install Eclipse RDS Support, you must have the following installed:

· Eclipse 3.1 or later, Flex Builder 2 or later, or Flash Builder

· ColdFusion MX 7.0.1 or later


Configuring RDS
Before using RDS, you must configure ColdFusion servers.


Configure any ColdFusion servers that you want to connect to using RDS
1 In Flash Builder or Eclipse, select Window > Preferences > ColdFusion > RDS Configuration.

2 To configure the default localhost server, select localhost and specify the following:

   · Description

   · Host name (127.0.0.1)

   · Port number (8500 if you are using the built-in web server)

   · Context root, if necessary (For more information about the context root, see Installing ColdFusion guide.)

   · Password, which is the RDS password

3 To specify additional servers, click New, and specify the following:

   · Description, which can be any name you want

   · Host name (IP address or machine name)

   · Port number (8500 if you are using the built-in web server)

   · Context root, if necessary

      For more information about the context root, see Installing ColdFusion guide.

   · Password, which is the RDS password

4 To remove a server definition, select the server and click Remove.

5 To test a connection, select the server and click Test Connection.

Note: If you are using ColdFusion MX 7 or earlier, the message "The RDS server was successfully contacted, but your
security credentials were invalid," appears. The message indicates that the password was not validated, even if it is correct.
Click OK to close the message.

Once you have configured the RDS connection to your CF servers, you can view the files, folders and data sources on
RDS servers. Each RDS server appears as a node in the RDS Fileview and Dataview, with the name you specified when
you configured the RDS server.


View files and folders or data sources do the following
1 In Flash Builder, select Window > Other Views. In Eclipse, select Window > Show View > Other.



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1336
Using External Resources




2 Select RDS.

3 To access the file system on the RDS server, select RDS Fileview.

4 To access data sources on the RDS server, select RDS Dataview.


Using the RDS Fileview
The RDS Fileview lists all the folders and files on the RDS server. You use the navigation buttons as indicated in the
following table:


 Button            Action

                   Refresh the active RDS server.



                   Create a file in the currently selected folder.



                   Delete the currently selected file.



                   Create a folder in the currently selected folder.



                   Delete the currently selected folder.



Note: RDS Eclipse Support does not support file operations such as copy and paste, drag and drop, and changing file
attributes. However, delete, save, save as, and rename are supported. Also, on ColdFusion servers after ColdFusion 5, the
date last modified field does not appear.

To rename a folder or file, right-click the folder or filename.


Using the RDS Dataview
The RDS Dataview lists all the data sources on the RDS server. You use the buttons as indicated in the following table:


 Button            Name                                             Description

                   Refresh                                          Refresh the currently selected item.



                   Query Viewer                                     Opens the RDS Query Viewer.




You can build queries using either the RDS Query Viewer or the Visual Query Builder. The RDS Visual Query Builder
is like the ColdFusion Report Builder Query Builder and the HomeSite Query Builder.


Build and execute a query using the RDS Query Viewer
1 Click the RDS Query Viewer icon on the RDS Dataview tab.

    The RDS Query Viewer opens in its own tab, which means that if you have other documents open, the RDS Query
    Viewer has focus.

2 Do one of the following:

    · Enter the SQL, and double-click the field names and table names as appropriate.

    · Click the Visual Query Builder button.




                                                       

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1337
Using External Resources




   For more information about using the Visual Query Builder, see "Using Visual Query Builder" on page 1337.

3 To try the query, click Execute query.

   The first 50 records of the result set appear.


Using Visual Query Builder
You use the Query Builder to define a SQL statement. The following image shows the Query Builder user interface:




Build a SQL statement using the Table pane and the Properties panel
1 Expand a data source.

2 Double-click the columns to be named in the SELECT statement.

   As you select columns, the Query Builder creates the SELECT statement in the area at the lower edge of the pane.

3 If you select columns from more than one table, you must specify the column or columns used to join them by
   dragging a column from one table to the related column in the second table.

4 (Optional) Specify sort order by doing the following:

   a Locate the column in the Properties panel.

   b Click in the Sort Type cell of the column you want to sort by.

   c Specify Ascending or Descending.

   d (Optional) If you specify multiple sort columns, you specify precedence using the Sort Order cell.

5 (Optional) Specify selection criteria by doing the following:

   a Locate the column in the Properties panel.

   b Click in the Condition cell.

   c Select WHERE.

   d Specify WHERE clause criteria in the Criteria cell.

      Note: If you specify selection criteria, the Query Builder creates a WHERE clause. To use an INNER JOIN or other
      advanced selection criteria instead, you must code the SQL manually.

6 (Optional) To specify an aggregate function, GROUP BY, or an expression:

   a Locate the column in the Properties panel.

   b Click in the Condition cell.

   c Select Group By or the aggregate function (such as COUNT).



                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                           1338
Using External Resources




7 (Optional) To specify SQL manually, type the SQL statement in the SQL pane.

   Note: You code SQL manually to use an INNER JOIN instead of a WHERE clause, use an OUTER JOIN, or use a
   database stored procedure.

8 (Optional) To specify the data type of a query parameter:

   a Click the + button under Parameters.

   b Enter the name of the parameter.

   c Select the data type.

9 Review the SELECT statement that displays in the SQL pane, and use the Table and Properties panes to make
   adjustments, if necessary.

10 (Optional) Click Test Query.

11 Click Save.


ColdFusion/Flex Application wizard
The ColdFusion/Flex Application wizard creates ColdFusion and Flex files for a create, read, update, delete (CRUD)
application. You specify the master, detail, and master/detail pages to include in the application, and the relationship
between the application's pages. The wizard lets you use Visual Query Builder to generate the SQL statements. For
more information about using Visual Query Builder, see "Using Visual Query Builder" on page 1337.


Designing your application
Before starting the ColdFusion/Flex Application wizard, you should determine which pages to include in your
application, including the following:

· Whether each page is a master, detail, or master/detail page

· The fields to display in each page

· The fields that connect one page to another

In the following example, you create an application for an art gallery. The first page lists all the artists that your gallery
represents. When a user selects an artist, a page that lists all the works by that artist appears. When the user then selects
a work of art, a page that contains details about that piece of art appears. In this example, your application contains the
following pages:

· A master page that lists the artists

· A master/detail page in which the master page lists the works of art by the artist selected on the List of Artists master
   page, and a detail page that contains details about the artwork selected on the Artwork master page.

You may find it helpful to draw a diagram of the tables and fields that you want to include in your application,
including which ones to display in your application, as the following image shows:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1339
Using External Resources




Start the ColdFusion/Flex Application wizard
1 Configure your RDS servers. For more information, see "Configuring RDS" on page 1335.

2 In Eclipse or Flash Builder, select File > New > Other.

3 Under ColdFusion Wizards, select ColdFusion/Flex Application wizard, and then click Next.

4 After reading the introductory text, click Next.

5 To load the settings from an application you previously created using the ColdFusion/Flex Application wizard,
   select the configuration file, and then click Load ColdFusion/Flex Application Wizard Settings.

6 Click Next.

7 Select the RDS server on which you want the application to reside.

8 Specify the data source to use. The data source is configured in the ColdFusion Administrator.

   Although you specify one default data source at this point, you can access data from other data sources in your
   application.

9 Click Next.


Specifying form layout
The Form Layout dialog box lets you specify the pages to use in your application. You can create master, detail, or
master/detail pages. In your application, you can link master, detail, and master/detail pages as follows:


Page type               Can link to

master                   master

                         master/detail

                         detail

                         master and detail

                         master and master/detail

master/detail            master

                         master/detail



Create a page
1 Click the plus sign (+).

2 In the Name text box, enter the name for the page.

3 Select the page type (master, detail, or master/detail).

4 Click Edit Master Page, Edit Detail page, or Edit Master Section, depending on the type of form you are creating.

   The Visual Query Builder starts.

5 Use Visual Query Builder to specify the data source, tables, and fields to include in the form, and then click Save to
   save the query. For more information about using Visual Query Builder, see "Using Visual Query Builder" on
   page 1337.

6 Repeat steps 1 through 5 for each form in your application.

7 Use the right and left arrows to specify the relationship of the forms in your application. For example, detail forms
   should appear indented, directly under the related master form in the Navigation Tree panel. You drag and drop
   items to move them in the tree structure.

8 Click Next.



                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1340
Using External Resources




   The Project information page appears.

9 Specify the following:

    · The context root, if applicable

    · Whether to include a login page in the application

    · The location of the services-config.xml configuration file that the project should use

    · The web root URL

    · Whether to use an existing or new Flash Builder or Eclipse project

    · The project name and the location of the project if it is new

10 Click Finish.

The ColdFusion/Flex Application wizard creates the ColdFusion and Flex files that comprise your application. You
can test the application by clicking the Run Main button in Flash Builder or Eclipse, or by browsing to the main
application page, which is located at http://<server_name>:<port_number>/<project_name>/bin/main.html. You can
also manually modify the application files as appropriate for your needs.


Tips for creating applications with the ColdFusion/Flex Application wizard
Although the ColdFusion/Flex Application wizard greatly simplifies creating CRUD applications, keep in mind the
following information to ensure that you create the application that you designed.

· To adjust UI elements, open the MXML file in Flash Builder or Eclipse design mode.

· When you create a project that has the same name as a project you previously created, the wizard creates a backup
   folder that contains the files from the project you previously created.

· If you create a master page and a detail page for a table in which there is no primary key defined, the wizard selects
   the first field in the database as the key value to represent the row.

· In master pages, link a field to the Parameters box to add type validation to the query by using the cfqueryparam
   tag. Doing this is optional.

· You must select a primary key column in the master form; the wizard chooses the key by default. If you create a
   master page and do not link it to the id property, you cannot add it to the site tree under another master page.

· Deselect the Display column for fields that your application uses that you do not want to appear in your application.

· Specify the sort order for the field by which to sort data in the page, and specify any other conditions as appropriate.

· Change the labels for fields by clicking the field name in the Label column, and then entering a new field name.

· In a detail page, create a combo box that is populated by dynamic data. To do this, change the value in the Input
   Control column for the field to use to populate the combo box to be ComboBox, click the Input Lookup Query
   (sub-select) column in that field, and then use the Visual Query Builder to specify the data to use.

· When you create a detail page, display of the primary key is disabled automatically.

· When you create a detail page, input controls are assigned by default. You can change them from the default values,
   which appear as follows:

    · Boolean and bit values appear as a check box.

    · Memo and CLOB values appear as a text area.

    · Everything else appears as a text input control.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1341
Using External Resources




ColdFusion/Ajax Application wizard
The ColdFusion/Ajax Application wizard creates a ColdFusion create, read, update, and delete (CRUD) application
that contains Ajax elements. For information about using the wizard, see "ColdFusion/Flex Application wizard" on
page 1338.

You start the wizard just as you start the ColdFusion/Flex Application wizard, except that you select the
ColdFusion/Ajax Application wizard. Unlike the ColdFusion/Flex Application wizard, the ColdFusion/Ajax
Application wizard does not generate login screens.


ActionScript to CFC wizard
The ActionScript to CFC wizard lets you create a ColdFusion component (CFC) based on an ActionScript class file.


Use the ActionScript to CFC wizard
1 In Flash Builder or Eclipse, go to the Project Navigator.

2 Right-click an ActionScript class file.

3 Select ColdFusion Wizards > Create CFC.

4 Enter the location of the CFC file in the Save to Project Folder text box, or click Browse and select the location.

5 Enter the filename of the CFC in the CFC Filename text box.

6 To replace an existing file, select Overwrite file.

7 To create get and set methods in the CFC, in addition to property definitions, select Generate Get/Set Methods.

8 To specify the property scope, select public or private.

9 Click Finish.


CFC to ActionScript wizard
The CFC to ActionScript wizard lets you create an ActionScript file based on a ColdFusion component (CFC) Value
Object.


Use the CFC to ActionScript wizard
1 In Flash Builder or Eclipse, go to the Project Navigator.

2 Right-click a CFC Value Object file.

3 Select ColdFusion Wizards > Create AS Class.

4 Enter the location of the ActionScript file in the Save to Project Folder text box, or click Browse and select the
   location.

5 Enter the class package in the AS Class Package text box.

6 Enter the filename of the ActionScript class file in the AS Class Name text box.

7 To replace an existing file, select Overwrite file.

8 Enter the path to the CFC in the Path to CFC text box.

9 To create get and set methods in the ActionScript Class file, select Generate Get/Set Methods.

10 Click Finish.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                1342
Using External Resources




RDS CRUD wizard
The Remote Development Services (RDS) CRUD Wizard lets you dynamically create a ColdFusion component (CFC)
based on a table that is registered in the ColdFusion Administrator on a ColdFusion server. To use the RDS CRUD
wizard, you must have the Eclipse RDS Support plug-in installed. (The Eclipse RDS Support plug-in is installed when
you install the ColdFusion wizards.)

The RDS CRUD Wizard lets you create the following types of CFCs:

· ActiveRecord style CRUD CFC, which includes all of the properties, get and set methods, and SQL methods in one
   CFC. The CFC includes the following methods:

   init() or init(primary key value)

   load(primary key value)

   save()

   delete()

· Bean/DAO style CRUD CFCs, which creates two related CFCs:

   · A Bean CFC, also called a Value Object, which contains the property definitions and get and set methods.

   · The DAO CFC, which contains the following methods:

      read(primary key value)

      create(cfc instance)

      update(cfc instance)

      delete(cfc instance)

· Data Service assembler CFC, which includes a Bean (also referred to as a Value Object), a DAO CFC, and an
   assembler CFC. The assembler CFC is required to take advantage of the Flex Data Services feature


Use the RDS CRUD wizard
1 In Flash Builder or Eclipse, go to the RDS Dataview by doing the following:

   a Select Window > Show View > Other.

   b Select RDS.

   c Select RDS Dataview.

2 Right click a table name.

3 Select ColdFusion Wizards > Create CFC.

4 Enter the project folder where you want to save the CFC in the CFC Folder text box.

5 Enter the CFC package in the CFC Package Name text box.

6 (Optional) Select the Primary Key column if a primary key is not defined in the database.

7 (Optional) To specify the primary key column in addition to the other values specified in the CFC, select the
   Primary Key is Controlled by the User option. If the primary key is automatically generated by the database (the
   identity field), do not select this option.

8 To replace existing files, select the Overwrite Files If They Already Exist option.

9 Select one of the following CFC Types:

   · Active Record CFC



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1343
Using External Resources




   · Bean CFC & DAO CFC

   · Flex Data Service Assembler CFCs

10 Enter the names of the CFCs in the appropriate text boxes.

11 To create an ActionScript Value Object:

   a Select the Create an ActionScript Value Object in Addition to the CFCs option.

   b Enter the location for the ActionScript Value Object in the AS Folder text box, or click Browse to browse to the
      location.

   c To create get and set methods in the ActionScript Class file, select Generate Get/Set Methods.

12 Click Finish.


Services Browser
The ColdFusion Services Browser lets you view all of the CFCs and web services on your computer.


Use the Services Browser
1 In Flash Builder or Eclipse, select Window > Show View > Other.

2 Select ColdFusion > Services Browser.

The Services Browser can do the following:

· Browse components

· Manage web services


Browsing components
The Service Browser lists the following components:

· Components that the ColdFusion component browser lists

   The ColdFusion component browser is located at cf_root/wwwroot/CFIDE/componentutils/componentdoc.cfm.

· Components that are located in any directories specified in the ColdFusion Administrator Mappings page

· Components that are located in any directories specified in the ColdFusion Administrator Custom Tag paths page

You can restrict the list of CFCs according to whether the functions in a CFC are remote, public, or private.

A sample element of the list appears as follows:




The first line of the listing contains the path. The second line includes the name of the CFC. The next two lines contain
the names of the functions in the CFC. The function name is followed by any argument, a colon, then the type of the
return value. The listing echo(echoString):STRING indicates that the echo function has an argument named
echoString, and that it returns a string. The myCFC CFC appears as follows:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1344
Using External Resources




<cfcomponent>
     <cffunction name="echo" output="No" returntype="string">
          <cfargument name="echoString" required="Yes">
          <cfreturn "echo: #arguments[1]#">
     </cffunction>


     <cffunction name="getArtists" returntype="query" hint="query the database and return the
                results.">
          <cfquery name="artists" datasource="cfcodeexplorer">
                select *
                from artists
          </cfquery>
          <cfreturn artists>
     </cffunction>
</cfcomponent>


Managing web services
The Services Browser lets you manage a list of web services by adding or deleting WSDL URLs from a list. In addition,
when you are editing a ColdFusion file, you can use the Services Browser to generate CFML code to invoke a web
service or to create a web service object. Similarly, when you are editing an ActionScript file, you can use the Services
Browser to generate ActionScript.

To view the list of web services, click the Show Web Services button in the top right corner of the Services Browser view.


Add a web service to the list
1 Right-click in the Services Browser view.

2 Select Add WSDL.

3 Enter a valid WSDL URL.

4 Click OK.


Delete a web service from the list
1 Right-click in the Services Browser view.

2 Select Delete WSDL.


Invoke a web service in ColdFusion
1 Place your mouse pointer where you want to insert the code.

2 View the list of web services.

3 Highlight a web service or a method in a web service and right-click.

4 Select Insert CFInvoke.

The code that the Service Browser generates appears in the ColdFusion file. The following is an example of the code
that the Service Browser generates:

<cfinvoke
      webservice="http://arcweb.esri.com/services/v2/MapImage.wsdl"
      method="convertMapCoordToPixelCoord"
      returnVariable="convertMapCoordToPixelCoord" >
     <cfinvokeargument name="mapCoord" type="" />
     <cfinvokeargument name="viewExtent" type="" />
     <cfinvokeargument name="mapImageSize" type="" />
</cfinvoke>



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                1345
Using External Resources




Create a web service object in ColdFusion
1 Place your mouse pointer where you want to insert the code.

2 View the list of web services.

3 Highlight a web service or a method in a web service and right-click.

4 Select Insert CFInvoke.

The code that the Service Browser generates appears in the ColdFusion file. The following is an example of the code
that the Service Browser generates:

createObject("webservice",
"http://arcweb.esri.com/services/v2/MapImage.wsdl").convertMapCoordToPixelCoord(mapCoord,
viewExtent, mapImageSize);

