Chapter 14: Using Web Elements and
External Objects

Using XML and WDDX

You can use Adobe ColdFusion to create, use, and manipulate XML documents. You can also use Web Distributed
Data Exchange (WDDX), an XML dialect, for transmitting structured data, including transferring data between
applications and between CFML and JavaScript.


About XML and ColdFusion
XML has rapidly become the universal language for representing documents and data on the web. These documents
can extend beyond the traditional concept of a paper document or its equivalent. For example, XML is often used to
represent database or directory information. XML is also commonly used to represent transaction information, such
as product orders or receipts, and for information such as inventory records and employee data.

Because XML represents data in a tagged, textual format it is an excellent tool for representing information that must
be shared between otherwise-independent applications such as order entry and inventory management. No
application must know anything about the other. Each application only must be prepared to get data in a format that
is structured according to the XML DTD or Schema. For example, in a distributed order processing application, the
order placement component, order fulfillment component, inventory management component, and billing
component can all share information with each other in XML format. They could use a common XML DTD, of
different components could communicate with each other using different DTDs.

After an application parses the XML document, it can then manipulate the information in any way that is appropriate.
For example, you can convert tabular XML data into a ColdFusion recordset, perform queries on the data and then
export the data an XML document. For example, the code in "Example: using XML in a ColdFusion application" on
page 1080 takes a customer order in XML, converts the data to a recordset, and uses a query to determine the order
cost. It then prepares a receipt as an XML document.

ColdFusion provides a comprehensive and easy-to-use set of tools for creating and using XML documents.
ColdFusion lets you do the following with XML documents:

· Convert XML text into ColdFusion XML document objects.

· Create new ColdFusion XML document objects.

· Modify ColdFusion XML document objects.

· Validate XML against a DTD or Schema

· Transform XML using XSLT (Extensible Stylesheet Language Transformation).

· Extract data from XML documents using XPath expressions.

· Convert ColdFusion XML document objects to text and save them in files.

ColdFusion can also represent forms that you create using the cfform tag as XML. You can have ColdFusion generate
the XML and process it using an XSLT skin to generate output for display, or ColdFusion can generate XML text and
place it in a variable for further processing. For more information on XML Forms, see "Creating Skinnable XML
Forms" on page 783




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1059
Using Web Elements and External Objects




The XML document object
ColdFusion represents an XML document as an object, called an XML document object, that is much like a standard
ColdFusion structure. In fact, most ColdFusion structure functions, such as StructInsert, work with XML
document objects. For a full list of ColdFusion functions that work on XML document objects, see "Functions for XML
object management" on page 1069.

You can look at the overall structure of an XML document in two ways: a basic view and a DOM (Document Object
Model)-based node view. The basic view presents all the information in the document, but does not separate the data
into as fine-grained units as the node view. ColdFusion can access XML document contents using either view.


A simple XML document
The descriptions of the basic and node views use the following simple XML document. This document is used in many
of the examples in the ColdFusion XML documentation.

<?xml version="1.0" encoding="UTF-8"?>
<employee>
<!-- A list of employees -->
     <name EmpType="Regular">
           <first>Almanzo</first>
           <last>Wilder</last>
     </name>
     <name EmpType="Contract">
           <first>Laura</first>
           <last>Ingalls</last>
     </name>
</employee>


Basic view
The basic view of an XML document object presents the object as a container that holds one root element structure.
The root element can have any number of nested element structures. Each element structure represents an XML tag
(start tag/end tag set) and all its contents; it can contain additional element structures. A basic view of the simple XML
document looks like the following:


        Document object



        Root Element: employee
         Comment: A list of employees


          Element: name                                  Element: name
           Attributes: EmpType = Regular                  Attributes: EmpType = Contract


            Element: rst          Element: last            Element: rst        Element: last
             Text:                  Text:                    Text:              Text:
               Almanzo               Wilder                   Laura              Ingalls




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1060
Using Web Elements and External Objects




DOM node view
The DOM node view presents the XML document object using the same format as the document's XML Document
Object Model (DOM). In fact, an XML document object is a representation of a DOM object.

The DOM is a World Wide Web Consortium (W3C) recommendation (specification) for a platform- and language-
neutral interface to dynamically access and update the content, structure, and style of documents. ColdFusion
conforms to the DOM Level 2 Core specification, available at www.w3.org/TR/DOM-Level-2-Core.

In the DOM node view, the document consists of a hierarchical tree of nodes. Each node has a DOM node type, a node
name, and a node value. Node types include Element, Comment, Text, and so on. The DOM structures the document
object and each of the elements it contains into multiple nodes of different types, providing a finer-grained view of the
document structure than the basic view. For example, if an XML comment is in the middle of a block of text, the DOM
node view represents its position in the text while the basic view does not.

ColdFusion also lets you use the DOM objects, methods, and properties defined in the W3C DOM Level 2 Core
specification to manipulate the XML document object.

For more information on referencing DOM nodes, see "XML DOM node structure" on page 1061. This document
does not cover the node view and using DOM methods and properties in detail.


XML document structures
An XML document object is a structure that contains a set of nested XML element structures. The following image
shows a section of the cfdump tag output for the document object for the XML in "A simple XML document" on
page 1059. This image shows the long version of the dump, which provides complete details about the document
object. Initially, ColdFusion displays a short version, with basic information. Click the dump header to change between
short, long, and collapsed versions of the dump.




The following code displays this output. It assumes that you save the code in a file under your web root, such as
C:\Inetpub\wwwroot\testdocs\employeesimple.xml

<cffile action="read" file="C:\Inetpub\wwwroot\testdocs\employeesimple.xml"
     variable="xmldoc">
<cfset mydoc = XmlParse(xmldoc)>
<cfdump var="#mydoc#">


The document object structure
At the top level, the XML document object has the following three entries:



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                      1061
Using Web Elements and External Objects




Entry name          Type                 Description

XmlRoot             Element              The root element of the document.

XmlComment          String               A string made of the concatenation of all comments on the document, that is, comments in
                                         the document prologue and epilog. This string does not include comments inside document
                                         elements.

XmlDocType          XmlNode              The DocType attribute of the document. This entry only exists if the document specifies a
                                         DocType. This value is read only; you cannot set it after the document object has been created

                                         This entry does not appear when the cfdump tag displays an XML element structure.



The element structure
Each XML element has the following entries:


Entry name          Type                 Description

XmlName             String               The name of the element; includes the namesapce prefix.

XmlNsPrefix         String               The prefix of the namespace.

XmlNsURI            String               The URI of the namespace.

XmlText or          String               A string made of the concatenation of all text and CData text in the element, but not inside any
                                         child elements. When you assign a value to the XmlCdata element, ColdFusion places the text
XmlCdata
                                         inside a CDATA information item. When you retrieve information from document object, these
                                         element names return identical values.

XmlComment          String               A string made of the concatenation of all comments inside the XML element, but not inside
                                         any child elements.

XmlAttributes       Structure            All of this element's attributes, as name-value pairs.

XmlChildren         Array                All this element's children elements.

XmlParent           XmlNode              The parent DOM node of this element.

                                         This entry does not appear when the cfdump tag displays an XML element structure.

XmlNodes            Array                An array of all the XmlNode DOM nodes contained in this element.

                                         This entry does not appear the cfdump tag when displays an XML element structure.



XML DOM node structure
The following table lists the contents of an XML DOM node structure:


Entry name          Type                 Description

XmlName             String               The node name. For nodes such as Element or Attribute, the node name is the element or
                                         attribute name.

XmlType             String               The node XML DOM type, such as Element or Text.

XmlValue            String               The node value. This entry is used only for Attribute, CDATA, Comment, and Text type nodes.


Note: The cfdump tag does not display XmlNode structures. If you try to dump an XmlNode structure, the cfdump tag
displays "Empty Structure."

The following table lists the contents of the XmlName and XmlValue fields for each node type that is valid in the
XmlType entry. The node types correspond to the objects types in the XML DOM hierarchy.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                            1062
Using Web Elements and External Objects




 Node type                          XmlName                                    xmlValue

 CDATA                              #cdata-section                              Content of the CDATA section

 COMMENT                            #comment                                    Content of the comment

 ELEMENT                            Tag name                                    Empty string

 ENTITYREF                          Name of entity referenced                   Empty string

 PI (processing instruction)        Target entire content excluding the target  Empty string

 TEXT                               #text                                       Content of the text node

 ENTITY                             Entity name                                 Empty string

 NOTATION                           Notation name                               Empty string

 DOCUMENT                           #document                                   Empty string

 FRAGMENT                           #document-fragment                          Empty string

 DOCTYPE                            Document type name                          Empty string


Note: Although XML attributes are nodes on the DOM tree, ColdFusion does not expose them as XML DOM node data
structures. To view an element's attributes, use the element structure's XMLAttributes structure.

The XML document object and all its elements are exposed as DOM node structures. For example, you can use the
following variable names to reference nodes in the DOM tree that you created from the XML example in "A simple
XML document" on page 1059:

mydoc.XmlName
mydoc.XmlValue
mydoc.XmlRoot.XmlName
mydoc.employee.XmlType
mydoc.employee.XmlNodes[1].XmlType



ColdFusion XML tag and functions
The following table lists the ColdFusion tags and functions that create and manipulate XML documents:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    1063
Using Web Elements and External Objects




Tag or function                     Description

<cfxml variable="objectName"        Creates a ColdFusion XML document object consisting of the markup in the tag body. The tag can
                                    include XML and CFML tags. ColdFusion processes all CFML in the tag body before converting the
[caseSensitive="Boolean"]>
                                    resulting text to an XML document object.

                                    If you specify the CaseSensitive="True" attribute, the case of names of elements and attributes
                                    in the document is meaningful. The default value is False.

                                    For more information on using the cfxml tag, see "Creating an XML document object using the
                                    cfxml tag" on page 1067.

XmlParse (XMLText                   Converts an XML document in a file or a string variable into an XML document object, and optionally
                                    validates the document against a DTD or schema.
[[, caseSensitive],
                                    If you specify the optional second argument as True, the case of names of elements and attributes
validator])
                                    in the document is meaningful. The default value is False.

                                    For more information on using the XmlParse function, see "Creating an XML document object from
                                    existing XML" on page 1068.

XmlNew([caseSensitive])             Returns an empty XML document object.

                                    If you specify the optional argument as True, the case of names of elements and attributes in the
                                    document is meaningful. The default value is False.

                                    For more information on using the XmlNew function, see "Creating an XML document object using
                                    the XmlNew function" on page 1068.

XmlElemNew(objectName{,             Returns an XML document object element with the specified name, optionally belonging to the
                                    specified namespace. You can omit the namespaceURI parameter and use only a namespace prefix
namespaceURI],
                                    if the prefix is defined elsewhere in the object.

elementName)
                                    For more information on using theXmlElemNew function, see "Adding an element" on page 1073.

XmlTransform(XMLVar,                Applies an Extensible Stylesheet Language Transformation (XSLT) to an XML document. The
                                    document can be represented as a string variable or as an XML document object. The function
XSLTStringVar[,
                                    returns the resulting XML document as a string.

parameters])
                                    For more information on using theXmlTransform function, see "Transforming documents with XSLT"
                                    on page 1079.

XmlSearch(objectName,               Uses an XPath expression to search an XML document object and returns an array of XML elements
                                    that match the search criteria.
XPathExpression)
                                    For more information on using the XmlSearch function, see "Extracting data with XPath" on
                                    page 1080.

XmlValidate(xmlDoc[,                Uses a Document Type Definition (DTD) or XML Schema to validate an XML text document (in a
                                    string or file) or an XML document object. The validator can be a DTD or Schema. If you omit the
validator])
                                    validator parameter, the document must specify a DTD or schema. For more information on
                                    using the XmlValidate function, see "Validating XML documents" on page 1079

XmlChildPos(element,                Returns the position (index) in an XmlChildren array of the Nth child with the specified element
                                    name. For example, XmlChildPos(mydoc.employee, "name", 2) returns the position in
elementName, position)
                                    mydoc.employee.XmlChildren of the mydoc.employee.name[2] element. This index can be used in
                                    the ArrayInsertAt and ArrayDeleteAt functions.

                                    For more information on using theXmlChildPos function, see "Determining the position of a child
                                    element with a common name" on page 1073, "Adding an element" on page 1073, and "Deleting
                                    elements" on page 1075.

XmlGetNodeType(xmlNode)             Returns a string identifying the type of an XML document object node returned by the function or
                                    in an element's XmlNodes array.

IsWDDX(String)                      Determines whether a string is a well-formed WDDX packet.

IsXML(String)                       Determines whether a string is well-formed XML text.




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                             1064
Using Web Elements and External Objects




Tag or function                     Description

IsXmlAttribute(variable)            Determines whether the function parameter is an XML Document Object Model (DOM) attribute
                                    node.

IsXmlDoc(objectName)                Returns True if the function argument is an XML document object.

IsXmlElem(elementName)              Returns True if the function argument is an XML document object element.

IsXmlNode(variable)                 Determines whether the function parameter is an XML document object node.

IsXmlRoot(elementName)              Returns True if the function argument is the root element of an XML document object.

ToString(objectName)                Converts an XML document object to a string representation.

XmlFormat(string)                   Escapes special XML characters in a string so that the string can be used as text in XML.



About case sensitivity and XML document objects
The tags and functions that create XML document objects let you specify whether ColdFusion treats the object in a
case-sensitive manner. If you do not specify case sensitivity, ColdFusion ignores the case of XML document object
component identifiers, such as element and attribute names. If you do specify case sensitivity, names with different
cases specify different components. For example, if you do not specify case sensitivity, the names
mydoc.employee.name[1] and mydoc.employee.NAME[1] always specify the same element. If you specify case
sensitivity, these names specify two separate elements. You cannot use dot notation references for element or attribute
names in a case-sensitive XML document; for more information see "Referencing the contents of an XML object" on
page 1064.


Using an XML object
Because an XML document object is represented as a structure, you can access XML document contents using either,
or a combination of both, of the following ways:

· Using the element names, such as mydoc.employee.name[1]

· Using the corresponding structure entry names (that is, XmlChildren array entries), such as
   mydoc.employee.XmlChildren[1]

Similarly, you can use either, or a combination of both, of the following notation methods:

· Structure (dot) notation, such as mydoc.employee

· Associative array (bracket) notation, such as mydoc["employee"]


Referencing the contents of an XML object
Use the following rules when you reference the contents of an XML document object on the right side of an assignment
or as a function argument:

· By default, ColdFusion ignores element name case. As a result, it considers the element name MyElement and the
   element name myELement to be equivalent. To make element name matching case-sensitive, specify
   CaseSensitive="True" in the cfxml tag, or specify True as a second argument in the XmlParse or XmlNew
   function that creates the document object.

· If your XML object is case sensitive, do not use dot notation to reference an element or attribute name. Use the
   name in associative array (bracket) notation, or a reference that does not use the case-sensitive name. For example,
   do not use names such as the following:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1065
Using Web Elements and External Objects




    MyDoc.employee.name[1]


   MyDoc.employee.XmlAttributes.Version

   Instead, use names such as the following:

    MyDoc.xmlRoot.XmlChildren[1]
   MyDoc.xmlRoot["name"][1]
   MyDoc.["employee"]["name"][1]


   MyDoc.xmlRoot.XmlAttributes["Version"]
   MyDoc["employee"].XmlAttributes["Version"]

   Important: Because ColdFusion always treats variable names as case-insensitive, using dot notation for element and
   attribute names in a case-sensitive XML document can generate unexpected results (such as all-uppercase variable
   names), exceptions, or both.

· If your XML object is case sensitive, you cannot use dot notation to reference an element or attribute name. Use the
   name in associative array (bracket) notation, or a reference that does not use the case-sensitive name (such as
   XmlChildren[1]) instead.

· Use an array index to specify one of multiple elements with the same name; for example,
   #mydoc.employee.name[1] and #mydoc.employee.name[2].

   If you omit the array index on the last component of an element identifier, ColdFusion treats the reference as the
   array of all elements with the specified name. For example, mydoc.employee.name refers to an array of two name
   elements.

· Use an array index into the XmlChildren array to specify an element without using its name; for example,
   mydoc.XmlRoot.XmlChildren[1].

· Use associative array (bracket) notation to specify an element name that contains a period or colon; for example,
   myotherdoc.XmlRoot["Type1.Case1"].

· You can use DOM methods in place of structure entry names.

For example, the following variables all reference the XmlText value "Almanzo" in the XML document created in "A
simple XML document" on page 1059:

mydoc.XmlRoot.XmlChildren[1].XmlChildren[1].XmlText
mydoc.employee.name[1].first.XmlText
mydoc.employee.name[1]["first"].XmlText
mydoc["employee"].name[1]["first"].XmlText
mydoc.XmlRoot.name[1].XmlChildren[1]["XmlText"]

The following variables all reference the EmpType attribute of the first name element in the XML document created
in "A simple XML document" on page 1059:

mydoc.employee.name[1].XmlAttributes.EmpType
mydoc.employee.name[1].XmlAttributes["EmpType"]
mydoc.employee.XmlChildren[1].XmlAttributes.EmpType
mydoc.XmlRoot.name[1].XmlAttributes["EmpType"]
mydoc.XmlRoot.XmlChildren[1].XmlAttributes.EmpType

Neither of these lists contains a complete set of the possible combinations that can make up a reference to the value or
attribute.


Assigning data to an XML object
When you use an XML object reference on the left side of an expression, most of the preceding rules apply to the
reference up to the last element in the reference string.


                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1066
Using Web Elements and External Objects




For example, the rules in "Referencing the contents of an XML object" on page 1064 apply to
mydoc.employee.name[1].first in the following expression:

mydoc.employee.name[1].first.MyNewElement = XmlElemNew(mydoc, NewElement);

The rule for naming in case correct document objects, however, applies to the full reference string, as indicated by the
following caution:

Important: Because ColdFusion always treats variable names as case-insensitive, using dot notation for element and
attribute names in a case-sensitive XML document can generate unexpected results (such as all-uppercase variable
names), exceptions, or both. In case-sensitive XML documents, use associative array notation or DOM notation names
(such as XmlRoot or XmlChldren[2]).


Referencing the last element on the left side of an expression
The following rules apply to the meaning of the last component on the left side of an expression:

1 The component name is an element structure key name (XML property name), such as XmlComment, ColdFusion
   sets the value of the specified element structure entry to the value of the right side of the expression. For example,
   the following line sets the XML comment in the mydoc.employee.name[1].first element to "This is a comment":

    mydoc.employee.name[1].first.XmlComment = "This is a comment";

2 If the component name specifies an element name and does not end with a numeric index, for example
   mydoc.employee.name, ColdFusion assigns the value on the right of the expression to the first matching element.

   For example, if both mydoc.employee.name[1] and mydoc.employee.name[2] exist, the following expression
   replaces mydoc.employee.name[1] with a new element named address, not an element named name:

    mydoc.employee.name = XmlElemNew(mydoc, "address");

   After executing this line, if there had been both mydoc.employee.name[1] and mydoc.employee.name[2], now
   only one mydoc.employee.name element exists with the contents of the original mydoc.employee.name[2].

3 If the component name does not match an existing element, the element names on the left and right sides of the
   expression must match. ColdFusion creates an element with the name of the element on the left of the expression.
   If the element names do not match, it generates an error.

   For example if a, mydoc.employee.name.phoneNumber element does not exist, the following expression creates
   an mydoc.employee.name.phoneNumber element:

    mydoc.employee.name.phoneNumber = XmlElemNew(mydoc, "phoneNumber");

   The following expression causes an error:

    mydoc.employee.name.phoneNumber = XmlElemNew(mydoc, "address");

4 If the component name does not match an existing element and the component's parent or parents also do not exist,
   ColdFusion creates any parent nodes as specified on the left side and use the previous rule for the last element. For
   example, no mydoc.employee.phoneNumber element exists, the following expression creates a phoneNumber
   element containing an AreaCode element:

    mydoc.employee.name.phoneNumber.AreaCode = XmlElemNew(mydoc, "AreaCode");


Assigning and retrieving CDATA values
To identify that element text is CDATA by placing it inside CDATA start and end marker information items, assign
the text to the XmlCdata element, not the XmlText element. Specify CDATA because ColdFusion escapes the < and >
symbols in the element text when you assign it to an XmlText entry. You can assign a value to an element's XmlText
entry or its XmlCdata entry, but not to both, as each assignment overwrites the other.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               1067
Using Web Elements and External Objects




When you retrieve data from the document object, references to XmlCdata and XmlText return the same string.

The following example shows how ColdFusion handles CDATA text:

<cfscript>
     myCDATA = "This is CDATA text";
     MyDoc = XmlNew();
     MyDoc.xmlRoot = XmlElemNew(MyDoc,"myRoot");
     MyDoc.myRoot.XmlChildren[1] = XmlElemNew(MyDoc,"myChildNodeCDATA");
     MyDoc.myRoot.XmlChildren[1].XmlCData = "#myCDATA#";
</cfscript>


<h3>Assigning a value to MyDoc.myRoot.XmlChildren[1].XmlCdata.</h3>
<cfoutput>
     The type of element MyDoc.myRoot.XmlChildren[1] is:
#MyDoc.myRoot.XmlChildren[1].XmlType#<br>
     The value when output using XmlCdata is: #MyDoc.myRoot.XmlChildren[1].XmlCData#<br>
     The value when output using XmlText is: #MyDoc.myRoot.XmlChildren[1].XmlText#<br>
</cfoutput>
<br>
The XML text representation of Mydoc is:
<cfoutput><XMP>#tostring(MyDoc)#</XMP></cfoutput>


<h3>Assigning a value to MyDoc.myRoot.XmlChildren[1].XmlText.</h3>
<cfset MyDoc.myRoot.XmlChildren[1].XmlText = "This is XML plain text">
<cfoutput>
     The value when output using XmlCdata is: #MyDoc.myRoot.XmlChildren[1].XmlCData#<br>
     The value when output using XmlText is: #MyDoc.myRoot.XmlChildren[1].XmlText#<br>
</cfoutput>
<br>
The XML text representation of Mydoc is:
<cfoutput><XMP>#tostring(MyDoc)#</XMP></cfoutput>



Creating and saving an XML document object
You can use several methods to create and save an XML document object. The specific technique that you use depends
on the application and your coding style.


Creating an XML document object using the cfxml tag
The cfxml tag creates an XML document object that consists of the XML markup in the tag body. The tag body can
include CFML code. ColdFusion processes the CFML code and includes the resulting output in the XML. The
following example shows a simple cfxml tag:




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1068
Using Web Elements and External Objects




<cfset testVar = True>
<cfxml variable="MyDoc">
     <MyDoc>
          <cfif testVar IS True>
               <cfoutput>The value of testVar is True.</cfoutput>
          <cfelse>
               <cfoutput>The value of testVar is False.</cfoutput>
          </cfif>
          <cfloop index = "LoopCount" from = "1" to = "4">
               <childNode>
                    This is Child node <cfoutput>#LoopCount#.</cfoutput>
               </childNode>
          </cfloop>
     </MyDoc>
</cfxml>
<cfdump var=#MyDoc#>

This example creates a document object with a root element MyDoc, which includes text that displays the value of the
ColdFusion variable testVar. MyDoc has four nested child elements, which are generated by an indexed cfloop tag. The
cfdump tag displays the resulting XML document object.

Note: When you use the cfxml tag, do not include an <?xml ?> processing directive in the tag body. This directive is not
required, and causes an error. To process XML text that includes the <?xml ?> directive, use the XmlParse function.


Creating an XML document object using the XmlNew function
The XmlNew function creates an XML document object, which you must then populate. For information on how to
populate a new XML document, see "Adding, deleting, and modifying XML elements" on page 1073.

Note: You cannot set the XmlDocType property for an XML document object that you create with the XmlNew function.

The following example creates and displays the same ColdFusion document object as in "Creating an XML document
object using the cfxml tag" on page 1067.

<cfset testVar = True>
<cfscript>
     MyDoc = XmlNew();
     MyDoc.xmlRoot = XmlElemNew(MyDoc,"MyRoot");
     if (testVar IS TRUE)
          MyDoc.MyRoot.XmlText = "The value of testVar is True.";
     else
          MyDoc.MyRoot.XmlText = "The value of testVar is False.";
     for (i = 1; i LTE 4; i = i + 1)
          {
          MyDoc.MyRoot.XmlChildren[i] = XmlElemNew(MyDoc,"childNode");
          MyDoc.MyRoot.XmlChildren[i].XmlText = "This is Child node " & i &".";
          }
</cfscript>
<cfdump var=#MyDoc#>


Creating an XML document object from existing XML
The XmlParse function converts an XML document or document fragment represented as text into a ColdFusion
document object. You can use a string variable containing the XML or the name or URL of a file that contains the text.
For example, if your application uses cfhttp action="get" to get the XML document, use the following line to create
the XML document object:

<cfset myXMLDocument = XmlParse(cfhttp.fileContent)>




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     1069
Using Web Elements and External Objects




The following example converts an XML text document in a file to an XML document object:

<cfset myXMLDocument=XmlParse("C:\temp\myxmldoc.xml" variable="XMLFileText")>

The XmlParse function takes a second, optional, attribute that specifies whether to maintain the case of the elements
and attributes in the document object. The default is to have the document object be case-insensitive. For more
information on case sensitivity, see "Referencing the contents of an XML object" on page 1064.

The XmlParse function also lets you specify a DTD or Schema to validate the XML text; if the XML is not valid,
ColdFusion generates an error. You can specify the filename or URL of the validator, or the DTD or Schema can be in
a CFML variable. You can also tell ColdFusion to use a DTD or Schema that is identified in the XML text. If you specify
validation, also specify whether the document is case sensitive. The following example validates an XML document
on file using a DTD that it specifies using a URL:

myDoc=XMLParse("C:\CFusion\wwwroot\examples\custorder.xml", false,
           "http://localhost:8500/examples/custorder.dtd")>


Saving and exporting an XML document object
The ToString function converts an XML document object to a text string. You can then use the string variable in any
ColdFusion tag or function.

To save the XML document in a file, use the ToString function to convert the document object to a string variable,
then use the cffile tag to save the string as a file. For example, use the following code to save the XML document
myXMLDocument in the file C:\temp\myxmldoc.xml:

<cfset XMLText=ToString(myXMLDocument)>
<cffile action="write" file="C:\temp\myxmldoc.xml" output="#XMLText#">



Modifying a ColdFusion XML object
As with all ColdFusion structured objects, you can often use several methods to change the contents of an XML
document object. For example, you often have the choice of using an assignment statement or a function to update the
contents of a structure or an array. You can use array and structure functions that you can use to modify an XML
document object. The section "XML document object management reference" on page 1071 provides a quick
reference to modifying XML document object contents. Later sections describe these methods for changing document
content in detail.


Functions for XML object management
The following table lists the ColdFusion array and structure functions that you can use to manage XML document
objects and their functions, and describes their common uses. In several cases you can use either an array function or
a structure function for a purpose, such as for deleting all of an element's attributes or children.


 Function                 Use

 ArrayLen                 Determines the number of child elements in an element, that is, the number of elements in an element's
                          XmlChildren array.

 ArrayIsEmpty             Determines whether an element has any elements in its XmlChildren array.

 StructCount              Determines the number of attributes in an element's XmlAttributes structure.

 StructIsEmpty            Determines whether an element has any attributes in its XmlAttributes structure.

                          Returns True if the specified structure, including the XML document object or an element, exists and is empty.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                      1070
Using Web Elements and External Objects




 Function                Use

 StructKeyArray          Gets an array or list with the names of all of the attributes in an element's XmlAttributes structure. Returns
                         the names of the children of an XML element.
 StructKeyList

 ArrayInsertAt           Adds a new element at a specific location in an element's XmlChildren array.

 ArrayAppend             Adds a new element at the end or beginning of an element's XmlChildren array.

 ArrayPrepend

 ArraySwap               Swaps the children in the XmlChildren array at the specified position.

 ArraySet                Sets a range of entries in an XmlChildren array to equal the contents of a specified element structure. Each
                         entry in the array range is a copy of the structure. Can be used to set a single element by specifying the same
                         index as the beginning and end of the range.

 ArrayDeleteAt           Deletes a specific element from an element's XmlChildren array.

 ArrayClear              Deletes all child elements from an element's XmlChildren array.

 StructDelete            Deletes a selected attribute from an element's XMLAttributes structure.

                         Deletes all children with a specific element name from an element's XmlChildren array.

                         Deletes all attributes of an element.

                         Deletes all children of an element.

                         Deletes a selected property value.

 StructClear             Deletes all attributes from an element's XMLAttributes structure.

 Duplicate               Copies an XML document object, element, or node structure.

 IsArray                 Returns True for the XmlChildren array. Returns false if you specify an element name, such as
                         mydoc.XmlRoot.name, even if multiple name elements exist in XmlRoot.

 IsStruct                Returns False for XML document objects, elements, and nodes. Returns True for XmlAttributes structures.

 StructGet               Returns the specified structure, including XML document objects, elements, nodes, and XmlAttributes
                         structures.

 StructAppend            Appends a document fragment XML document object to another XML document object.

 StructInsert            Adds a new entry to an XmlAttributes structure.

 StructUpdate            Sets or replaces the value of a document object property such as XmlName, or of a specified attribute in an
                         XmlAttributes structure.


Note: Array and structure functions not in the preceding or table or the table in the next section, do not work with XML
document objects, XML elements, or XML node structures.


Treating elements with the same name as an array
In many cases, an XML element has multiple children with the same name. For example, the example document used
in many XML examples has multiple name elements in the employee elements. In many cases, you can treat the child
elements with identical names as an array. For example, to reference the second name element in mydoc.employee,
you can specify mydoc.employee.name[2]. However, you can only use a limited set of Array functions when you use
this notation. The following table lists the array functions that are valid for such references:




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   1071
Using Web Elements and External Objects




Array function                                     Result

IsArray(elemPath.elemName)                         Always returns False.

ArrayClear(elemPath.elemName)                      Removes all the elements with name elemName from the elemPath element.

ArrayLen(elemPath.elemName)                        Returns the number of elements named elemName in the elemPath element.

ArrayDeleteAt(elemPath.elemName, n)                Deletes the nth child named elemName from the elemPath element.

ArrayIsEmpty(elemPath.elemName)                    Always returns False.

ArrayToList(elemPath.elemName, n)                  Returns a comma-separated list of all the XmlText properties of all the children of
                                                   elemPath named elemName.



XML document object management reference
The following tables provide a quick reference to the ways you can modify the contents of an XML document object.
The sections that follow describe in detail how to modify XML contents.

Note: If your XML object is case sensitive, you cannot use dot notation to reference an element or attribute name. Use the
name in associative array (bracket) notation, or a reference that does not use the case-sensitive name (such as
xmlChildren[1]) instead.


Adding information to an element
Use the following techniques to add new information to an element:


Type                Using a function                                           Using an assignment statement

Attribute           StructInsert(xmlElemPath.XmlAttributes, "key",             xmlElemPath.XmlAttributes.key ="value"
                    "value")


                                                                               xmlElemPath.XmlAttributes["key"]="valu
                                                                               e"

Child element       To append:                                                 To append:

                    ArrayAppend(xmlElempath.XmlChildren,newElem)               xmlElemPath.XmlChildren[i] =newElem

                    To insert:

                    ArrayInsertAt(xmlElempath.XmlChildren,                     xmlElemPath.newChildName =newElem
                    position, newElem)
                                                                               (where newChildName must be the same as
                                                                               newElem.XmlName and cannot be an indexed
                                                                               name such as name[3])



Deleting information from an element
Use the following techniques to delete information from an element:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                             1072
Using Web Elements and External Objects




Type                Using a function                                                             Using an assignment statement

Property            StructDelete(xmlElemPath, propertyName)                                      xmlElemPath.propertyName=""


Attribute           All attributes:                                                              Not available

                    StructDelete(xmlElemPath, XmlAttributes)



                    A specific attribute:

                    StructDelete(xmlElemPath.XmlAttributes,"attributeName")

Child element       All children of an element:                                                  Not available

                    StructDelete(xmlElemPath, "XmlChildren")

                    or

                    ArrayClear(xmlElemPath.XmlChildren)



                    All children with a specific name:

                    StructDelete(xmlElementpath,"elemName")
                    ArrayClear(xmlElemPath.elemName)



                    A specific child:

                    ArrayDeleteAt(xmlElemPath.XmlChildren,position)
                    ArrayDeleteAt(xmlElemPath.elemName,position)



Changing contents of an element
Use the following techniques to change the contents of an element:


Type                Using a function                              Using an assignment statement

Property            StructUpdate(xmlElemPath,"propert             xmlElemPath.propertyName ="value"
                    yName", "value")


                                                                  xmlElemPath["propertyName"] ="value"

Attribute           StructUpdate(xmlElemPath.XmlAttri             xmlElemPath.XmlAttributes.attributeName="value"
                    butes,"attributeName", "value")


                                                                  xmlElemPath.XmlAttributes["attributeName"] =
                                                                  "value"

Child element       ArraySet(xmlElemPath.XmlChildren,             Replace first or only child named elementName:
                    index,index, newElement)
(replace)                                                         parentElemPath.elementName =newElement


                    (use the same value for both index entries to
                    change one element)                           parentElemPath["elementName"]= newElement



                                                                  Replace a specific child named elementName:

                                                                  parentElemPath.elementName[index] = newElement

                                                                  or

                                                                  parentElemPath["elementName"][index] = newElement




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1073
Using Web Elements and External Objects




Adding, deleting, and modifying XML elements
Several basic techniques exist for adding, deleting, and modifying XML elements. The example code in the technique
description uses the XML document described in "A simple XML document" on page 1059.


Counting and finding the position of child elements
Often, an XML element has several children with the same name. For example, in the XML document defined in the
simple XML document, the employee root element has multiple name elements.

To manipulate such an object, you often must know the number of children of the same name, and you could have to
know the position in the XmlChildren array of a specific child name that is used for multiple children.


Counting child elements
The following user-defined function determines the number of child elements with a specific name in an element:

<cfscript>
function NodeCount (xmlElement, nodeName)
{
     nodesFound = 0;
     for (i = 1; i LTE ArrayLen(xmlElement.XmlChildren); i = i+1)
     {
          if (xmlElement.XmlChildren[i].XmlName IS nodeName)
               nodesFound = nodesFound + 1;
     }
     return nodesFound;
}
</cfscript>

The following lines use this function to display the number of nodes named "name" in the mydoc.employee element:

<cfoutput>
Nodes Found: #NodeCount(mydoc.employee, "name")#
</cfoutput>


Determining the position of a child element with a common name
The XmlChildPos function determines the location in the XmlChildren array of a specific element with a common
name. You use this index when ColdFusion must know where to insert or delete child elements. For example, if several
name elements exist in mydoc.employee, use the following code to locate name[2] in the XmlChildren array:

<cfset nameIndex = XmlChildPos(mydoc.employee, "name", 2)>


Adding an element
You can add an element by creating an element or by using an existing element.

Use the XmlElemNew function to create a new, empty element. This function has the following form:

XmlElemNew(docObject, elementName)

where docObject is the name of the XML document object in which you are creating the element, and elementName is
the name you are giving the new element.

Use an assignment statement with an existing element on the right side to create an element using an existing element.
See "Copying an existing element" on page 1075 for more information on adding elements using existing elements.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1074
Using Web Elements and External Objects




Adding an element using a function
You can use the ArrayInsertAt or the ArrayAppend function to add an element to an XML document object. For
example, the following line adds a phoneNumber element after the last element for employee.name[2]:

<cfset ArrayAppend(mydoc.employee.name[2].XmlChildren, XmlElemNew(mydoc,
                "phoneNumber"))>

The following line adds a new department element as the first element in employee. The name elements become the
second and third elements.

<cfset ArrayInsertAt(mydoc.employee.XmlChildren, 1, XmlElemNew(mydoc,
                "department"))>

Use the format parentElement.XmlChildren to specify the array of elements to which you are adding the new
element. For example, the following line causes an error:

<cfset ArrayInsertAt(mydoc.employee.name, 2, XmlElemNew(mydoc, "PhoneNumber")>

If you have multiple child elements with the same name, and you want to insert a new element in a specific position,
use the function XmlChildPos to determine the location in the XmlChildren array where you want to insert the new
element. For example, the following code determines the location of mydoc.employee.name[1] and inserts a new name
element as the second name element:

<cfscript>
nameIndex = XmlChildPos(mydoc.employee, "name", 1);
ArrayInsertAt(mydoc.employee.XmlChildren, nameIndex + 1, XmlElemNew(mydoc,
                           "name"));
</cfscript>

Using a namespace: When you use a function to add an element, you can assign the element to a namespace by
including the namespace prefix in the element name. If you have not yet associated the prefix with a namespace URI,
also include a parameter with the namespace URI in the XmlElemNew function. This parameter must be the second
parameter in the method, and the element name must be the third parameter. ColdFusion then associates the
namespace prefix with the URI, and you can omit the URI parameter in further xmlElemNew functions.

The following example adds two to the supplies document root two elements in the Prod namespace. The first
XmlElemNew function use sets the association between the Prod namespace prefix and the URI; the second use only
requires the prefix on the element name.

<cfscript>
      mydoc.supplies.XmlChildren[1] = XmlElemNew(mydoc,
           "http://www.foo.com/Products", "Prod:soap");
      mydoc.supplies.XmlChildren[2] = XmlElemNew(mydoc, "Prod:shampoo");
</cfscript>


Adding an element using direct assignment
You can use direct assignment to append a new element to an array of elements. You cannot use direct assignment to
insert an element into an array of elements.

When you use direct assignment, you can specify on the left side an index into the XmlChildren array greater than the
last child in the array. For example, if two elements exist in mydoc.employee, you can specify any number greater than
two, such as mydoc.employee.XmlChildren[6]. The element is always added as the last (in this case, third) child.

For example, the following line appends a name element to the end of the child elements of mydoc.employee:

<cfset mydoc.employee.XmlChildren[9] = XmlElemNew(mydoc, "name")>




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1075
Using Web Elements and External Objects




If the parent element does not have any children with the same name as the new child, you can specify the name of the
new node or the left side of the assignment. For example, the following line appends a phoneNumber element to the
children of the first name element in mydoc.employee:

<cfset mydoc.employee.name[1].phoneNumber = XmlElemNew(mydoc, "phoneNumber")>

You cannot use the node name on the left to add an element with the same name as an existing element in the parent.
For example, if mydoc.employee has two name nodes, the following line causes an error:

<cfset mydoc.employee.name[3] = XmlElemNew(mydoc, "name")>

However, the following line does work:

<cfset mydoc.employee.XmlChilren[3] = XmlElemNew(mydoc, "name")>


Copying an existing element
You can add a copy of an existing element elsewhere in the document. For example, if a
mydoc.employee.name[1].phoneNumber element exists, but no mydoc.employee. name[2].phoneNumber, the
following line creates an mydoc.employee. name[2]. phoneNumber element with the same value as the original
element. This assignment copies the original element. Unlike with standard ColdFusion structures, you get a true copy,
not a reference to the original structure. You can change the copy without changing the original.

<cfset mydoc.employee.name[2].phoneNumber = mydoc.employee.name[1].phoneNumber>

When you copy an element, the new element must have the same name as the existing element. If you specify the new
element by name on the left side of an assignment, the element name must be the same as the name on the right side.
For example, the following expression causes an error:

<cfset mydoc.employee.name[2].telephone = mydoc.employee.name[1].phoneNumber>


Deleting elements
You can use many methods to delete individual or multiple elements.


Deleting individual elements
Use the ArrayDeleteAt function to delete a specific element from an XML document object. For example, the
following line deletes the second child element in the mydoc.employee element:

<cfset ArrayDeleteAt(mydoc.employee.XmlChildren, 2)>

If an element has only one child element with a specific name, you can also use the StructDelete function to delete
the child element. For example, the following line deletes the phoneNumber element named in the second
employee.name element:

<cfset StructDelete(mydoc.employee.name[2], "phoneNumber")>

When multiple child elements have the same name, specify the element position, either among the elements of the
same name, or among all child elements. Fore example, you can use the following line to delete the second name
element in mydoc.employee:

<cfset ArrayDeleteAt(mydoc.employee.name, 2)>

You can also determine the position in the XmlChildren array of the element you want to delete and use that position.
To do so, use the XmlChildPos function. For example, the following lines determine the location of
mydoc.employee.name[2] and delete the element:

<cfset idx = XmlChildPos(mydoc.employee, "name", 2)>
<cfset ArrayDeleteAt(mydoc.employee.XmlChildren, idx)>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1076
Using Web Elements and External Objects




Deleting multiple elements
If an element has multiple children with the same name, use the StructDelete function or ArrayClear function with
an element name to delete all of an element's child elements with that name. For example, both of the following lines
delete all name elements from the employee structure:

<cfset StructDelete(mydoc.employee, "name")>
<cfset ArrayClear(mydoc.employee.name)>

Use the StructDelete or ArrayClear function with XmlChildren to delete all of an element's child elements. For
example, each of the following lines deletes all child elements of the mydoc.employee.name[2] element:

<cfset StructDelete(mydoc.employee.name[2], "XmlChildren")>
<cfset ArrayClear(mydoc.employee.name[2].XmlChildren)>


Adding, changing, and deleting element attributes
You modify an element's attributes the same way you change the contents of any structure. For example, each of the
following lines adds a Status attribute the second mydoc.employee.name element:

<cfset mydoc.employee.name[2].XmlAttributes.Status="Inactive">
<cfset StructInsert(mydoc.employee.name[2].XmlAttributes, "Status", "Inactive")>

To change an attribute, use a standard assignment statement; for example:

<cfset mydoc.employee.name[2].XmlAttributes.Status="Active">

To delete an attribute, use StructDelete; for example:

<cfset StructDelete(mydoc.employee.name[1].XmlAttributes, "Status")>


Changing element properties
To change an element's properties, including its text and comment, use a standard assignment expression. For
example, use the following line to add "in the MyCompany Documentation Department" to the mydoc.employee XML
comment:

<cfset mydoc.employee.XmlComment = mydoc.employee.XmlComment & "in the
MyCompany Documentation Department">


Changing an element name
The XML DOM does not support changing an element name directly. To change the name of an element, create an
element with the new name, insert it into the XML document object before or after the original element, copy all the
original element's contents to the new element, and then delete the original element.


Clearing an element property value
To clear an element property value, either assign the empty string to the property or use the StructDelete function.
For example, each of the following lines clears the comment string from mydoc.employee:

<cfset mydoc.employee.XmlComment = "">
<cfset StructDelete(mydoc.employee, "XmlComment")>


Replacing or moving an element
To replace an element with a new element, use a standard replacement expression. For example, to replace the
mydoc.employee.department element with a new element named organization, use either of the following lines:

<cfset mydoc.employee.department = XmlElemNew(mydoc, "Organization")>
<cfset mydoc.employee.XmlChildren[1] = XmlElemNew(mydoc, "Organization")>




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1077
Using Web Elements and External Objects




To replace an element with a copy of an existing element, use the existing element on the right side of an expression.
For example, the following line replaces the phoneNumber element for mydoc.employee.name[2] with the
phoneNumber element from mydoc.employee.name[1]:

<cfset mydoc.employee.name[2].phoneNumber=mydoc.employee.name[1].phoneNumber>

This code creates a true copy of the name[1].phoneNumber element as name[2].phoneNumber.

To move an element, assign it to its new location, then delete it from its old location. For example, the following lines
move the phoneNumber element from mydoc.employee.name[1] to mydoc.employee.name[2]:

<cfset mydoc.employee.name[2].phoneNumber=mydoc.employee.name[1].phoneNumber>
<cfset StructDelete(mydoc.employee.name[1], "phoneNumber")>

Note: You cannot copy or move an element from one document object to another document object.


Using XML and ColdFusion queries
You can convert XML documents into ColdFusion query objects and manipulate them using queries of queries. This
technique does not require the use of XPath and provides a method of searching XML documents and extracting data
that is natural to ColdFusion programmers.


Converting XML to a ColdFusion query
The following example reads an XML document, converts it to a query object, and then performs a query of queries
on the object to extract selected data:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                              1078
Using Web Elements and External Objects




<!--- Read the file and convert it to an XML document object --->
<cffile action="read" file="C:\CFusion\wwwroot\myexamples\employees.xml" variable="myxml">
<cfset mydoc = XmlParse(myxml)>


<!--- get an array of employees --->
<cfset emp = mydoc.employee.XmlChildren>
<cfset size = ArrayLen(emp)>


<cfoutput>
Number of employees = #size#
<br>
</cfoutput>
<br>
<!--- create a query object with the employee data --->
<cfset myquery = QueryNew("fname, lname") >
<cfset temp = QueryAddRow(myquery, #size#)>
<cfloop index="i" from = "1" to = #size#>
     <cfset temp = QuerySetCell(myquery, "fname",
          #mydoc.employee.name[i].first.XmlText#, #i#)>
     <cfset temp = QuerySetCell(myquery, "lname",
          #mydoc.employee.name[i].last.XmlText#, #i#)>
</cfloop>


<!--- Dump the query object --->
Contents of the myquery Query object: <br>
<cfdump var=#myquery#>
<br><br>


<!--- Select entries with the last name starting with A and dump the result --->
<cfquery name="ImqTest" dbType="query">
     SELECT lname, fname
     FROM myquery
     WHERE lname LIKE 'A%'
</cfquery>
<cfdump var=#imqtest#>


Converting a query object to XML
The following example shows how to convert a query object to XML. It uses cfquery to get a list of employees from
the cfdocexamples database and saves the information as an XML document.




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1079
Using Web Elements and External Objects




<!--- Query the database and get the names in the employee table --->
<cfquery name="myQuery" datasource="cfdocexamples">
      SELECT FirstName, LastName
      FROM employee
</cfquery>


<!--- Create an XML document object containing the data --->
<cfxml variable="mydoc">
      <employee>
            <cfoutput query="myQuery">
                      <name>
                      <first>#FirstName#</first>
                      <last>#LastName#</last>
                 </name>
            </cfoutput>
      </employee>
</cfxml>


<!--- dump the resulting XML document object --->
<cfdump var=#mydoc#>
<!--- Write the XML to a file --->
\\x
            output=#toString(mydoc)#>



Validating XML documents
ColdFusion provides the following methods for validating a document against a DTD or an XML Schema:

· The XmlParse function can validate XML text that it is parsing against a DTD or Schema. It the function
   encounters a validation error, ColdFusion generates an error and stops parsing the text. If the validator generates
   warnings, but no errors, ColdFusion parses the document and returns the result.

· The XmlValidate function can validate an XML text document or XML document object. against a DTD or
   Schema. The function returns a data structure with detailed information from the validator, including arrays of
   warning, error, and fatal error messages, and a Boolean status variable indicating whether the document is valid.
   Your application can examine the status information and determine how to handle it further.

For examples of XML validation, see XmlParse and XmlValidate in the CFML Reference. The XmlParse example
validates a document using a DTD. The XmlValidate example validates the document using an XML Schema that
represents the same document structure as the DTD.


Transforming documents with XSLT
The Extensible Stylesheet Language Transformation (XSLT) technology transforms an XML document into another
format or representation. For example, one common use of XSLT is to convert XML documents into HTML for
display in a browser. XSLT has many other uses, including converting XML data to another format, such as converting
XML in a vocabulary used by an order entry application into a vocabulary used by an order fulfillment application.

XSLT transforms an XML document by applying an Extensible Stylesheet Language (XSL) style sheet. (When stored
in a file, XSL style sheets typically have the .xsl extension.) ColdFusion provides the XmlTransform function to apply
an XSL transformation to an XML document. The function takes an XML document in string format or as an XML
document object, and an XSL style sheet in string format, and returns the transformed document as a string.

The following code:

1 Reads the simpletransform.xsl style sheet file into a string variable.



                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1080
Using Web Elements and External Objects




2 Uses the style sheet to transform the mydoc XML document object.

3 Saves the resulting transformed document in a second file.

<cffile action="read" file="C:\CFusion\wwwroot\testdocs\simpletransform.xsl"
     variable="xslDoc">
<cfset transformedXML = XmlTransform(mydoc, xslDoc)>
<cffile action="write" file="C:\CFusion\wwwroot\testdocs\transformeddoc.xml"
     output=transformedXML>

XSL and XSLT are specified by the World Wide Web Consortium (W3C). For detailed information on XSL, XSLT, and
XSL style sheets, see the W3C website at www.w3.org/Style/XSL/. Several books are available on using XSL and XSLT.


Extracting data with XPath
XPath is a language for addressing parts of an XML document. Like XSL, XPath is a W3C specification. One of the
major uses of XPath is in XSL transformations. However, XPath has more general uses. In particular, it can extract data
from XML documents, such as complex data set representations. Thus, XPath is another data querying tool.

XPath uses a pattern called an XPath expression to specify the information to extract from an XML document. For
example, the simple XPath expression /employee/name selects the name elements in the employee root element.

The XmlSearch function uses XPath expressions to extract data from XML document objects. The function takes an
XML document object and an XPath expression in string format, and returns the results of matching the XPath
expression with the XML. The returned results can be any XPath return type that ColdFusion can represent, such as
an array of XML object nodes or a Boolean value. For more information, see XmlSearch in the CFML Reference.

The following example extracts all the elements named last, which contain the employee's last names, from the
employeesimple.xml file, and displays the names:

<cffile action="read"
     file="C:\inetpub\wwwroot\examples\employeesimple.xml"
     variable="myxml">
<cfscript>
     myxmldoc = XmlParse(myxml);
     selectedElements = XmlSearch(myxmldoc, "/employee/name/last");
     for (i = 1; i LTE ArrayLen(selectedElements); i = i + 1)
          writeoutput(selectedElements[i].XmlText & "<br>");
</cfscript>

XPath is specified by the World Wide Web Consortium. For detailed information on XPath, see the W3C website at
www.w3.org/TR/xpath. Most books that cover XSLT also discuss XPath.


Example: using XML in a ColdFusion application
The following shows how you can use XML to represent data, and how ColdFusion can use XML data in an
application. Although the example is too simple to be used in an application without substantial changes, it presents
some of the common uses of XML with ColdFusion.

The example receives an order in the form of an XML document, processes it, and generates an XML receipt
document. In this case, the order document is in a file, but it could be received as the result of an HTTP request, or
retrieved using cfpop, cfftp, or other methods. The ColdFusion page does the following with the order:

1 Generates a query object from an XML document.

2 Queries a database table to determine the order discount percentage to use.

3 Uses a query of queries to calculate the total price, then calculates the discounted price.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                              1081
Using Web Elements and External Objects




4 Generates the receipt as an XML document.

This example displays the results of the processing steps to show you what has been done.


The XML document
The order.xml document has the following structure:

· The root element is named order and has one attribute, id.

· One customer element exists with firstname, lastname, and accountnum attributes. The customer element does not
   have a body

· One items element exists that contains multiple item elements

· Each item element has an id attribute and contains a name, quantity, and unitprice element. The name, quantity,
   and unitprice elements contain their value as body text.

The following order.xml document works correctly with the information in the cfdocexamples database:

<order id="4323251">
    <customer firstname="Philip" lastname="Cramer" accountNum="21"/>
    <items>
          <item id="43">
              <name>
                    Large Hammer
              </name>
              <quantity>
                    1
              </quantity>
              <unitprice>
                    15.95
              </unitprice>
          </item>
          <item id="54">
              <name>
                    Ladder
              </name>
              <quantity>
                    2
              </quantity>
              <unitprice>
                    40.95
              </unitprice>
          </item>
          <item id="68">
              <name>
                    Paint
              </name>
              <quantity>
                    10
              </quantity>
              <unitprice>
                    18.95
              </unitprice>
          </item>
    </items>
</order>




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                    1082
Using Web Elements and External Objects




The ColdFusion page
The ColdFusion page looks like the following:

<!--- Convert file to XML document object --->
<cffile action="read" file="C:\CFusion\wwwroot\examples\order.xml" variable="myxml">
<cfset mydoc = XmlParse(myxml)>


<!--- Extract account number --->
    <cfset accountNum=#mydoc.order.customer.XmlAttributes.accountNum#>
<!--- Display Order Information --->
<cfoutput>
    <b>Name=</b>#mydoc.order.customer.XmlAttributes.firstname#
      #mydoc.order.customer.XmlAttributes.lastname#
    <br>
    <b>Account=</b>#accountNum#
    <br>
    <cfset numItems = ArrayLen(mydoc.order.items.XmlChildren)>
    <b>Number of items ordered=</b> #numItems#
</cfoutput>
<br><br>


<!--- Process the order into a query object --->
<cfset orderquery = QueryNew("item_Id, name, qty, unitPrice") >
<cfset temp = QueryAddRow(orderquery, #numItems#)>
<cfloop index="i" from = "1" to = #numItems#>
    <cfset temp = QuerySetCell(orderquery, "item_Id",
          #mydoc.order.items.item[i].XmlAttributes.id#,#i#)>
    <cfset temp = QuerySetCell(orderquery, "name",
          #mydoc.order.items.item[i].name.XmlText#, #i#)>
    <cfset temp = QuerySetCell(orderquery, "qty",
          #mydoc.order.items.item[i].quantity.XmlText#, #i#)>
    <cfset temp = QuerySetCell(orderquery, "unitPrice",
          #mydoc.order.items.item[i].unitprice.XmlText#, #i#)>
</cfloop>


<!--- Display the order query --->
<cfdump var=#orderquery#>
<br><br>


<!--- Determine the discount --->
<cfquery name="discountQuery" datasource="cfdocexamples">
    SELECT *
    FROM employee
    WHERE Emp_Id = #accountNum#
</cfquery>
<cfset drate = 0>
<cfif #discountQuery.RecordCount# is 1>
    <cfset drate = 10>
</cfif>


<!--- Display the discount rate --->
<cfoutput>
    <b>Discount Rate =</b> #drate#%
</cfoutput>
<br><br>


<!--- Compute the total cost and discount price--->



                                           

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 1083
Using Web Elements and External Objects




<cfquery name="priceQuery" dbType="query">
     SELECT SUM(qty*unitPrice)
     AS totalPrice
     FROM orderquery
</cfquery>
<cfset discountPrice = priceQuery.totalPrice * (1 - drate/100)>


<!--- Display the full price and discounted price --->
<cfoutput>
     <b>Full Price=</b> #priceQuery.totalPrice#<br>
     <b>Discount Price=</b> #discountPrice#
</cfoutput>
<br><br>


<!---Generate an XML Receipt --->
<cfxml variable="receiptxml">
<receipt num = "34">
     <cfoutput>
          <price>#discountPrice#</price>
          <cfif drate GT 0 >
             <discountRate>#drate#</discountRate>
          </cfif>
     </cfoutput>
     <itemsFilled>
          <cfoutput query="orderQuery">
                    <name>#name# </name>
                    <qty> #qty# </qty>
                    <price> #qty*unitPrice# </price>
          </cfoutput>
     </itemsFilled>
</receipt>
</cfxml>


<!--- Display the resulting receipt --->
<cfdump var=#receiptxml#>


Reviewing the code
The following table describes the CFML code and its function. For the sake of brevity, it does not include code that
displays the processing results.




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 1084
Using Web Elements and External Objects




Code                                                        Description

 <cffile action="read"                                      Reads the XML from a file and convert it to an XML document object.
file="C:\CFusion\wwwroot\examples\order.xml"
variable="myxml">
<cfset mydoc = XmlParse(myxml)>
<!--- Extract account number --->                           Sets the accountNum variable from the customer entry's accountnum
<cfset                                                      attribute.
accountNum=#mydoc.order.customer.XmlAttributes.acc
ountNum#>

<cfset orderquery = QueryNew("item_Id, name, qty,           Converts the XML document object into a query object.
unitPrice") >
 <cfset temp = QueryAddRow(orderquery, #numItems#)>         Creates a query with columns for the item_id, name, qty, and unitPrice
 <cfloop index="i" from = "1" to = #numItems#>              values for each item.
 <cfset temp = QuerySetCell(orderquery, "item_Id",
                                                            For each XML item entry in the mydoc.order.items entry, fills one row
#mydoc.order.items.item[i].XmlAttributes.id#,#i#)>          of the query with the item's id attribute and the text in the name,
 <cfset temp = QuerySetCell(orderquery, "name",
                                                            quantity, and unitprice entries that it contains.
 #mydoc.order.items.item[i].name.XmlText#, #i#)>
 <cfset temp = QuerySetCell(orderquery, "qty",
 #mydoc.order.items.item[i].quantity.XmlText#,
#i#)>
 <cfset temp = QuerySetCell(orderquery,
"unitPrice",
 #mydoc.order.items.item[i].unitprice.XmlText#,
#i#)>
 </cfloop>

<cfquery name="discountQuery"                               If the account number is the same as an employee ID in the
datasource="cfdocexamples">                                 cfdocexamples database Employee table, the query returns one
 SELECT *
                                                            record. and RecordCount equals 1. In this case, sets a discount rate of
 FROM employee
                                                            10%. Otherwise, sets a discount rate of 0%.
 WHERE Emp_Id = #accountNum#
 </cfquery>
 <cfset drate = 0>
 <cfif #discountQuery.RecordCount# is 1>
 <cfset drate = 10>
 </cfif>

<cfquery name="priceQuery" dbType="query">                  Uses a query of queries with the SUM operator to calculate the total
 SELECT SUM(qty*unitPrice)                                  cost before discount of the ordered items, then applies the discount to
 AS totalPrice
                                                            the price. The result of the query is a single value, the total price.
 FROM orderquery
 </cfquery>
 <cfset discountPrice = priceQuery.totalPrice * (1
- drate/100)>

<cfxml variable="receiptxml">                               Creates an XML document object as a receipt. The receipt has a root
 <receipt num = "34">                                       element named receipt, which has the receipt number as an attribute.
 <cfoutput>
                                                            The receipt element contains a price element with the order cost and
 <price>#discountPrice#</price>
                                                            an itemsFilled element with one item element for each item.
 <cfif drate GT 0 >
    <discountRate>#drate#</discountRate>
 </cfif>
 </cfoutput>
 <itemsFilled>
 <cfoutput query="orderQuery">
 <name>#name# </name>
 <qty> #qty# </qty>
 <price> #qty*unitPrice# </price>
 </cfoutput>
 </itemsFilled>
 </receipt>
 </cfxml>



Moving complex data across the web with WDDX
WDDX is an XML vocabulary for describing a complex data structure, such as an array, associative array (such as a
ColdFusion structure), or a recordset, in a generic fashion. It lets you use HTTP to move the data between different
application server platforms and between application servers and browsers. Target platforms for WDDX include
ColdFusion, Active Server Pages (ASP), JavaScript, Perl, Java, Python, COM, Flash, and PHP.



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1085
Using Web Elements and External Objects




The WDDX XML vocabulary consists of a document type definition (DTD) that describes the structure of standard
data types and a set of components for each of the target platforms to do the following:

· Serialize: The data from its native representation into a WDDX XML document or document fragment.

· Deserialize: A WDDX XML document or document fragment into the native data representation, such as a CFML
    structure.

This vocabulary creates a way to move data, its associated data types, and descriptors that allow the data to be
manipulated on a target system, between arbitrary application servers.

Note: The WDDX DTD, which includes documentation, is located at
www.openwddx.org/downloads/dtd/wddx_dtd_10.txt.

WDDX is a valuable tool for ColdFusion developers, however, its usefulness is not limited to CFML. If you serialize a
common programming data structure (such as an array, recordset, or structure) into WDDX format, you can use
HTTP to transfer the data across a range of languages and platforms. Also, you can use WDDX to store complex data
in a database, file, or even a client variable.

WDDX has two features that make it useful for transferring data in a web environment:

· It is lightweight. The JavaScript used to serialize and deserialize data, including a debugging function to dump
    WDDX data, occupies less than 22 K.

· Unlike traditional client-server approaches, the source and target system can have minimal-to-no prior knowledge
    of each other. They only must know the structure of the data that is being transferred.

WDDX was created in 1998, and many applications now expose WDDX capabilities. The best source of information
about WDDX is www.openwddx.org. This site offers free downloads of the WDDX DTD and SDK and additional
resources, including a WDDX FAQ, a developer forum, and links to additional sites that provide WDDX resources.


Uses of WDDX
WDDX is useful for transferring complex data between applications. For example, you can use it to exchange data
between a CFML application and a CGI or PHP application. WDDX is also useful for transferring data between the
server and client-side JavaScript.


Exchanging data across application servers
WDDX is useful for the transfer of complex, structured data seamlessly between different application server platforms.
For example, an application based on ColdFusion at one business could cfwddx use to convert a purchase order
structure to WDDX. It could then use cfhttp to send the WDDX to a supplier running a CGI-based system.

The supplier could then deserialize the WDDX to its native data form, the extract information from the order, and pass
it to a shipping company running an application based on ASP.


Transferring data between the server and browser
You can use WDDX for server-to-browser and browser-to-server data exchanges. You can transfer server data to the
browser in WDDX format and convert it to JavaScript objects on the browser. Similarly, your application pages can
serialize JavaScript data generated on the browser into WDDX format and transfer the data to the application server.
You then deserialize the WDDX XML into CFML data on the server.

On the server, you use the cfwddx tag to serialize and deserialize WDDX data. On the browser, you use WddxSerializer
and WddxRecordset JavaScript utility classes to serialize the JavaScript data to WDDX. (ColdFusion installs these
utility classes on your server as webroot/CFIDE/scripts/wddx.js.)




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1086
Using Web Elements and External Objects




WDDX and web services
WDDX does not compete with web services. It is a complementary technology focused on solving simple problems of
application integration by sharing data on the web in a pragmatic, productive manner at low cost.

WDDX offers the following advantages:

· It can be used by lightweight clients, such as browsers or Flash Player.

· It can be used to store complex data structures in files and databases.

Applications that take advantage of WDDX can continue to do so if they start to use web services. These applications
could also be converted to use web services standards exclusively; only the service and data interchange formats: not
the application model, must change.


How WDDX works
The following example shows how WDDX works. A simple structure with two string variables could have the
following form after it is serialized into a WDDX XML representation:

<var name='x'>
      <struct>


            <var name='a'>
                   <string>Property a</string>
            </var>
            <var name='b'>
                   <string>Property b</string>
            </var>
      </struct>
</var>

When you deserialize this XML into CFML or JavaScript, the result is a structure that is created by either of the
following scripts:


 JavaScript                               CFScript

 x = new Object();                        x = structNew();

 x.a = "Property a";                      x.a = "Property a";

 x.b = "Property b";                      x.b = "Property b";


Conversely, when you serialize the variable x produced by either of these scripts into WDDX, you generate the XML
listed in the preceding code.

ColdFusion provides a tag and JavaScript objects that convert between CFML, WDDX, and JavaScript. Serializers and
deserializers for other data formats are available on the web. For more information, see www.openwddx.org.

Note: The cfwddx tag and the wddx.js JavaScript functions use UTF-8 encoding to represent data. Any tools that
deserialize ColdFusion-generated WDDX must accept UTF-8 encoded characters. UTF-8 encoding is identical to the
ASCII and ISO 8859 single-byte encodings for the standard 128 "7-bit" ASCII characters. However, UTF-8 uses a two-
byte representation for "high-ASCII" ISO 8859 characters where the initial bit is 1.


WDDX data type support
The following text describes the data types that WDDX supports. This information is a distillation of the description
in the WDDX DTD. For more detailed information, see the DTD at www.openwddx.org.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                       1087
Using Web Elements and External Objects




Basic data types
WDDX can represent the following basic data types:


 Data type                Description

 Null                     Null values in WDDX are not associated with a type such as number or string. The tag converts WDDX Nulls to
                          empty strings.

 Numbers                  WDDX documents use floating-point numbers to represent all numbers. The range of numbers is restricted to
                          +/-1.7E+/-308. The precision is restricted to 15 digits after the decimal point.

 Date-time values         Date-time values are encoded according to the full form of ISO8601; for example, 2002-9-15T09:05:32+4:0.

 Strings                  Strings can be of arbitrary length and must not contain embedded nulls. Strings can be encoded using double-
                          byte characters.



Complex data types
WDDX can represent the following complex data types:


 Data type          Description

 Array              Arrays are integer-indexed collections of objects of arbitrary type. Because most languages start array indexes at 0,
                    while CFML array indexes start at 1, working with array indexes can lead to nonportable data.

 Structure          Structures are string-indexed collections of objects of arbitrary type, sometimes called associative arrays. Because
                    some of the languages supported by WDDX are not case-sensitive, no two variable names in a structure can differ only
                    in their case.

 Recordset          Recordsets are tabular rows of named fields, corresponding to ColdFusion query objects. Only simple data types can
                    be stored in recordsets. Because some of the languages supported by WDDX are not case-sensitive, no two field names
                    in a recordset can differ only in their case. Field names must satisfy the regular expression [_A-Za-z][_.0-9A-Za-z]*
                    where the period (.) stands for a literal period character, not "any character".

 Binary             The binary data type represents strings (blobs) of binary data. The data is encoded in MIME base64 format.



Data type comparisons
The following table compares the basic WDDX data types with the data types to which they correspond in the
languages and technologies commonly used on the web:


 WDDX               CFML                   XML                       Java                   ECMAScript/            COM


                                           Schema                                           JavaScript

 null               N/A                    N/A                       null                   null                   VT_NULL

 boolean            Boolean                boolean                   java.lang.Boolean      boolean                VT_BOOL

 number             Number                 number                    java.lang.Double       number                 VT_R8

 dateTime           DateTime               dateTime                  java.lang.Date         Date                   VT_DATE

 string             String                 string                    java.lang.String       string                 VT_BSTR

 array              Array                  N/A                       java.lang.Vector       Array                  VT_ARRAY | VT_VARIANT




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1088
Using Web Elements and External Objects




 WDDX               CFML                XML                 Java                ECMAScript/     COM


                                        Schema                                  JavaScript

 struct             Structure           N/A                 java.lang.          Object          IWDDXStruct

                                                            Hashtable

 recordset          Query object        N/A                 coldfusion.runtime. WddxRecordset   IWDDXRecordset
                                                            QueryTable

 binary             Binary              binary              byte[]              WddxBinary      V_ARRAY | UI1



Time zone processing
Producers and consumers of WDDX packets can be in geographically dispersed locations. Therefore, it is important
to use time zone information when serializing and deserializing data, to ensure that date-time values are represented
correctly.

The cfwddx action=cfml2wddx tag useTimezoneInfo attribute specifies whether to use time zone information in
serializing the date-time data. In the JavaScript implementation, useTimezoneInfo is a property of the
WddxSerializer object. In both cases, the default useTimezoneInfo value is True.

Date-time values in WDDX are represented using a subset of the ISO8601 format. Time zone information is
represented as an hour/minute offset from universal time (UTC); for example, "2002-9-8T12:6:26-4:0".

When the cfwddx tag deserializes WDDX to CFML, it automatically uses available time zone information, and
converts date-time values to local time. In this way, you need not worry about the details of time zone conversions.

However, when the JavaScript objects supplied with ColdFusion deserialize WDDX to JavaScript expressions, they do
not use time zone information, because in JavaScript it is difficult to determine the time zone of the browser.


Using WDDX
ColdFusion provides several tools for creating and converting WDDX that you can use for common application uses.


Using the cfwddx tag
The tag can do the following conversions:


 From                                              To

 CFML                                              WDDX

 CFML                                              JavaScript

 WDDX                                              CFML

 WDDX                                              JavaScript


A typical cfwddx tag used to convert a CFML query object to WDDX looks like the following:

<cfwddx action="cfml2wddx" input="#MyQueryObject#" output="WddxTextVariable">

In this example, MyQueryObject is the name of the query object variable, and WddxTextVariable is the name of the
variable in which to store the resulting WDDX XML.

Note: For more information on thecfwddx tag, see the CFML Reference.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 1089
Using Web Elements and External Objects




Validating WDDX data
The cfwddx tag has a Validate attribute that you can use when converting WDDX to CFML or JavaScript. When you
set this attribute to True, the XML parser uses the WDDX DTD to validate the WDDX data before deserializing it. If
the WDDX is not valid, ColdFusion generates an error. By default, ColdFusion does not validate WDDX data before
trying to convert it to ColdFusion or JavaScript data.

The IsWDDX function returns True if a variable is a valid WDDX data packet. It returns False otherwise. You can use
this function to validate WDDX packets before converting them to another format. For example, you can use it instead
of the cfwddxvalidate attribute, so that invalid WDDX is handled within conditional logic instead of error-handling
code. You can also use it to pre-validate data that JavaScript at the browser deserializes.


Using JavaScript objects
ColdFusion provides two JavaScript objects, WddxSerializer object and WddxRecordset object, that you can use
in JavaScript to convert data to WDDX. These objects are defined in the file webroot/cfide/scripts/wddx.js.

The CFML Reference describes these objects and their methods in detail. The example "Transferring data from the
browser to the server" on page 1090 shows how you can use these objects to serialize JavaScript to WDDX.


Converting CFML data to a JavaScript object
The following example demonstrates the transfer of a cfquery recordset from a ColdFusion page executing on the
server to a JavaScript object that is processed by the browser.

The application consists of four principal sections:

· Running a data query

· Including the WDDX JavaScript utility classes

· Calling the conversion function

· Writing the object data in HTML

The following example uses the cfdocexamples data source that is installed with ColdFusion:

<!--- Create a simple query --->
<cfquery name = "q" datasource ="cfdocexamples">
      SELECT Message_Id, Thread_id, Username, Posted
      FROM messages
</cfquery>


<!--- Load the wddx.js file, which includes the dump function --->
<script type="text/javascript" src="/CFIDE/scripts/wddx.js"></script>


<script>
      // Use WDDX to move from CFML data to JavaScript
      <cfwddx action="cfml2js" input="#q#" topLevelVariable="qj">


      // Dump the recordset to show that all the data has reached
      // the client successfully.
      document.write(qj.dump(true));
</script>

Note: To see how cfwddx Action="cfml2js" works, save this code under your web root directory, for example in
wwwroot/myapps/wddxjavascript.cfm, run the page in your browser and select View Source in your browser.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1090
Using Web Elements and External Objects




Transferring data from the browser to the server
The following example serializes form field data, posts it to the server, deserializes it, and displays the data. For
simplicity, it only collects a small amount of data. In applications that generate complex JavaScript data collections,
you can extend this basic approach effectively. This example uses the WddxSerializer JavaScript object to serialize
the data, and the cfwddx tag to deserialize the data.


Use the example
1 Save the file under your web root directory, for example in wwwroot/myapps/ wddxserializedeserialze.cfm.

2 Display http://localhost/myapps/wddxserializedeserialze.cfm in your browser.

3 Enter a first name and last name in the form fields.

4 Click Next.

   The name appears in the Names added so far box.

5 Repeat steps 3 and 4 to add as many names as you wish.

6 Click Serialize to serialize the resulting data.

   The resulting WDDX packet appears in the WDDX packet display box. This step is intended only for test purposes.
   Real applications handle the serialization automatically.

7 Click Submit to submit the data.

   The WDDX packet is transferred to the server-side processing code, which deserializes it and displays the
   information.

<!--- load the wddx.js file --->
<script type="text/javascript" src="/CFIDE/scripts/wddx.js"></script>


<!--- Data binding code --->
<script>


    // Generic serialization to a form field
    function serializeData(data, formField) {
          wddxSerializer = new WddxSerializer();
          wddxPacket = wddxSerializer.serialize(data);
          if (wddxPacket != null) {
                formField.value = wddxPacket;
          }
          else {
                alert("Couldn't serialize data");
          }
    }
    // Person info recordset with columns firstName and lastName
    // Make sure the case of field names is preserved
    var personInfo = new WddxRecordset(new Array("firstName", "lastName"), true);


    // Add next record to end of personInfo recordset
    function doNext() {
          // Extract data
          var firstName = document.personForm.firstName.value;
          var lastName = document.personForm.lastName.value;


          // Add names to recordset
          nRows = personInfo.getRowCount();
          personInfo.firstName[nRows] = firstName;




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                        1091
Using Web Elements and External Objects




          personInfo.lastName[nRows] = lastName;


          // Clear input fields
          document.personForm.firstName.value = "";
          document.personForm.lastName.value = "";


          // Show added names on list
          // This gets a little tricky because of browser differences
          var newName = firstName + " " + lastName;
          if (navigator.appVersion.indexOf("MSIE") == -1) {
              document.personForm.names[length] =
                    new Option(newName, "", false, false);
          }
          else {
              // IE version
              var entry = document.createElement("OPTION");
              entry.text = newName;
              document.personForm.names.add(entry);
          }
    }
</script>


<!--- Data collection form --->
<form action="#cgi.script_name#" method="Post" name="personForm">


    <!--- Input fields --->
    Personal information<br>
    First name: <input type=text name=firstName><br>
    Last name: <input type=text name=lastName><br>
    <br>


    <!--- Navigation & submission bar --->
    <input type="button" value="Next" onclick="doNext()">
    <input type="button" value="Serialize"
    onclick="serializeData(personInfo, document.personForm.wddxPacket)">
    <input type="submit" value="Submit">
    <br><br>
    Names added so far:<br>
    <select name="names" size="5">
    </select>
    <br>


    <!--- The WDDX packet is stored here.--->
    <!--- In a real application this text area would be a hidden
              input field. --->
    <br>
    WDDX packet display:<br>
    <textarea name="wddxPacket" rows="10" cols="80" wrap="Virtual">
    </textarea>


</form>



<!--- Server-side processing --->
<hr>




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1092
Using Web Elements and External Objects




<b>Server-side processing</b><br>
<br>
<cfif isdefined("form.wddxPacket")>
     <cfif form.wddxPacket neq "">


           <!--- Deserialize the WDDX data --->
           <cfwddx action="wddx2cfml" input=#form.wddxPacket#
           output="personInfo">


           <!--- Display the query --->
           The submitted personal information is:<br>
           <cfoutput query=personInfo>
                Person #CurrentRow#: #firstName# #lastName#<br>
           </cfoutput>
     <cfelse>
           The client did not send a well-formed WDDX data packet!
     </cfif>
<cfelse>
     No WDDX data to process at this time.
</cfif>


Storing complex data in a string
The following simple example uses WDDX to store complex data, a data structure that contains arrays as a string in a
client variable. It uses the cfdump tag to display the contents of the structure before serialization and after
deserialization. It uses the HTMLEditFormat function in a cfoutput tag to display the contents of the client variable.
The HTMLEditFormat function is required to prevent the browser from trying to interpret (and throwing away) the
XML tags in the variable.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1093
Using Web Elements and External Objects




<!--- Enable client state management --->
<cfapplication name="relatives" clientmanagement="Yes">


<!--- Build a complex data structure --->
<cfscript>
     relatives = structNew();
     relatives.father = "Bob";
     relatives.mother = "Mary";
     relatives.sisters = arrayNew(1);
     arrayAppend(relatives.sisters, "Joan");
     relatives.brothers = arrayNew(1);
     arrayAppend(relatives.brothers, "Tom");
     arrayAppend(relatives.brothers, "Jesse");
</cfscript>


A dump of the original relatives structure:<br>
<br>
<cfdump var="#relatives#"><br>
<br>


<!--- Convert data structure to string form and save it in the client scope --->
<cfwddx action="cfml2wddx" input="#relatives#" output="Client.wddxRelatives">


The contents of the Client.wddxRelatives variable:<br>
<cfoutput>#HtmlEditFormat(Client.wddxRelatives)#</cfoutput><br>


<!--- Now read the data from client scope into a structure --->
<cfwddx action="wddx2cfml" input="#Client.wddxRelatives#" output="sameRelatives">
<br>
A dump of the sameRelatives structure generated from client.wddxRelatives<br>
<cfdump var="#sameRelatives#">




Using Web Services

Web services let you publish and consume remote application functionality over the Internet. When you consume web
services, you access remote functionality to perform an application task. When you publish a web service, you let
remote users access your application functionality to build it into their own applications.


Web services
Since its inception, the Internet has allowed people to access content stored on remote computers. This content can be
static, such as a document represented by an HTML file, or dynamic, such as content returned from a ColdFusion page
or CGI script.

Web services let you access application functionality that someone created and made available on a remote computer.
With a web service, you can make a request to the remote application to perform an action.

For example, you can request a stock quote, pass a text string for translation, or request information from a product
catalog. The advantage of web services is that you do not have to re-create application logic that someone else has
already created and, therefore, you can build your applications faster.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         1094
Using Web Elements and External Objects




Referencing a remote web service within your ColdFusion application is called consuming web services. Since web
services adhere to a standard interface regardless of implementation technology, you can consume a web service
implemented as part of a ColdFusion application, or as part of a .NET or Java application.

You can also create your own web services and make them available to others for remote access, called publishing web
service. Applications that consume your web service can be implemented in ColdFusion or by any application that
recognizes the web service standard.


Accessing a web service
In its simplest form, an access to a web service is like a function call. Instead of the function call referencing a library
on your computer, it references remote functionality over the Internet.

One feature of web services is that they are self-describing. A person who makes a web service available also publishes
a description of the API to the web service as a Web Services Description Language (WSDL) file.

A WSDL file is an XML-formatted document that includes information about the web service, including the following
information:

· Operations that you can call on the web service

· Input parameters that you pass to each operation

· Return values from an operation

Consuming web services typically is a two-step process:

1 Parse the WSDL file of the web service to determine its interface.

   A web service makes its associated WSDL file available over the Internet. You must know the URL of the WSDL
   file defining the service. For example, you can access the WSDL file for the TemperatureService web service at the
   following URL:

   www.xmethods.net/sd/2001/TemperatureService.wsdl

   For an overview of WSDL syntax, see "Working with WSDL files" on page 1095.

2 Make a request to the web service.

   The following example runs an operation on the Temperature web service to retrieve the temperature in ZIP code
   55987:

    <cfinvoke
          webservice="http://www.xmethods.net/sd/2001/TemperatureService.wsdl"
          method="getTemp"
          returnvariable="aTemp">
            <cfinvokeargument name="zipcode" value="55987"/>
   </cfinvoke>
   <cfoutput>The temperature at ZIP code 55987 is #aTemp#</cfoutput>

For more information on consuming web services, see "Consuming web services" on page 1098.


Basic web service concepts
To fully understand how web services work make sure that you are familiar with the underlying architecture of a web
service provider.

Note: For detailed information, consult one of the many web services books.

The following are three primary components of the web services platform:

· SOAP (Simple Object Access Protocol)



                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1095
Using Web Elements and External Objects




· WSDL (Web Services Description Language)

· UDDI (Universal Description, Discovery, and Integration)


Supporting web services with SOAP
SOAP provides a standard XML structure for sending and receiving web service requests and responses over the
Internet. Usually you send SOAP messages using HTTP, but you also can send them using SMTP and other protocols.
ColdFusion integrates the Apache Axis SOAP engine to support web services.

The ColdFusion Web Services Engine performs the underlying functionality to support web services, including
generating WSDL files for web services that you create. In ColdFusion, to consume or publish web services does not
require you to be familiar with SOAP or to perform any SOAP operations.

You can find additional information about SOAP in the W3C SOAP 1.1 note at www.w3.org/TR/SOAP/.


Describing web services with WSDL
A WSDL document is an XML file that describes the purpose of the web service, where it is located, and how to access
it. The WSDL document describes the operations that you can invoke and their associated data types.

ColdFusion can generate a WSDL document from a web service, and you can publish the WSDL document at a URL
to provide information to potential clients. For more information, see "Working with WSDL files" on page 1095.


Finding web services with UDDI
As a consumer of web services, you want to know what web services are available. As a publisher of web services, you
want others to be able to find information about your web services. Universal Description, Discovery, and Integration
(UDDI) provides a way for web service clients to dynamically locate web services that provide specific capabilities. You
use a UDDI query to find service providers. A UDDI response contains information, such as business contact
information, business category, and technical details, about how to invoke a web service.

Although ColdFusion does not directly support UDDI, you can manually register or find a web service using a public
UDDI registry, such as the IBM UDDI Business Registry at https://www-
3.ibm.com/services/uddi/protect/registry.html.

You can find additional information about UDDI at www.uddi.org/about.htm.


Working with WSDL files
WSDL files define the interface to a web service. To consume a web service, you access the service WSDL file to
determine information about it. If you publish your application logic as a web service, create a WSDL file for it.

WSDL is a draft standard supported by the World Wide Web Consortium. You can access the specification at
www.w3.org/TR/wsdl.


Creating a WSDL file
To publish a web service, you construct the service functionality and then create the WSDL file defining the service. In
ColdFusion, you use components to create web services. ColdFusion automatically generates the WSDL file for a
component that you use to produce a web service. For more information on creating web services, see "Publishing web
services" on page 1104.

For more information on components, see "Building and Using ColdFusion Components" on page 177.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               1096
Using Web Elements and External Objects




Accessing web services using Dreamweaver
The Dreamweaver Components tab lets you view web services, including operation names, parameter names, and
parameter data types.


Open the Components tab in Dreamweaver and add a web service
1 Select Window > Components, or use Control+F7, to open the Components panel.

2 In the Components panel, select Web Services from the drop-down list in the upper left of the panel.

3 Click the Plus (+) button.

    The Add Using WSDL dialog box appears.

4 Specify the URL of the WSDL file.

After the web service is defined to Dreamweaver, you can drag it onto a page to call it using the cfinvoke tag.

For more information on using Dreamweaver, see its online Help system.

Note: The Web Services option is not available if you are running Dreamweaver on the Macintosh. However, you can
still use web services by writing code manually.


Reading a WSDL file
A WSDL file takes practice to read. You can view the WSDL file in a browser, or you can use a tool such as
Dreamweaver, which contains a built-in utility for displaying WSDL files in an easy-to-read format.

The following example shows a WSDL file for the TemperatureService web service:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   1097
Using Web Elements and External Objects




<?xml version="1.0"?>
<definitions name="TemperatureService"
targetNamespace="http://www.xmethods.net/sd/TemperatureService.wsdl"xmlns:tns="http://www.xm
ethods.net/sd/TemperatureService.wsdl" xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/" xmlns="http://schemas.xmlsoap.org/wsdl/">
    <message name="getTempRequest">
          <part name="zipcode" type="xsd:string"/>
    </message>
    <message name="getTempResponse">
          <part name="return" type="xsd:float"/>
    </message>
    <portType name="TemperaturePortType">
          <operation name="getTemp">
              <input message="tns:getTempRequest"/>
              <output message="tns:getTempResponse"/>
          </operation>
    </portType>
    <binding name="TemperatureBinding" type="tns:TemperaturePortType">
          <soap:binding style="rpc" transport="http://schemas.xmlsoap.org/soap/http"/>
          <operation name="getTemp">
              <soap:operation soapAction=""/>
              <input>
                    <soap:body use="encoded" namespace="urn:xmethods-Temperature"
encodingStyle="http://schemas.xmlsoap.org/soap/encoding/"/>
              </input>
              <output>
                    <soap:body use="encoded" namespace="urn:xmethods-Temperature"
encodingStyle="http://schemas.xmlsoap.org/soap/encoding/"/>
              </output>
          </operation>
    </binding>
    <service name="TemperatureService">
          <documentation>Returns current temperature in a given U.S. zipcode</documentation>
          <port name="TemperaturePort" binding="tns:TemperatureBinding">
              <soap:address
location="http://services.xmethods.net:80/soap/servlet/rpcrouter"/>
          </port>
    </service>
</definitions>

The following are the major components of the WSDL file:


Component               Definition

definitions             The root element of the WSDL file. This area contains namespace definitions that you use to avoid naming
                        conflicts between multiple web services.

types                   (Not shown) Defines data types used by the service messages.

message                 Defines the data transferred by a web service operation, typically the name and data type of input parameters
                        and return values.

port type               Defines one or more operations provided by the web service.

operation               Defines an operation that can be remotely invoked.

input                   Specifies an input parameter to the operation using a previously defined message.

output                  Specifies the return values from the operation using a previously defined message.




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         1098
Using Web Elements and External Objects




 Component               Definition

 fault                   (not shown) Optionally specifies an error message returned from the operation.

 binding                 Specifies the protocol used to access a web service including SOAP, HTTP GET and POST, and MIME.

 service                 Defines a group of related operations.

 port                    Defines an operation and its associated inputs and outputs.


For additional descriptions of the contents of this WSDL file, see "Consuming web services" on page 1098.


Consuming web services
ColdFusion provides a variety of methods for consuming web services. The method that you choose depends on your
ColdFusion programming style and application.

The following table describes these methods:


 Method                  CFML operator                   Description

 CFScript                ()                              Consumes a web service from within a CFScript block.

 CFML tag                                                Consumes a web service from within a block of CFML code.

 CFML tag                                                Consumes a web service from within a block of CFML code.


One important consideration is that all consumption methods use the same underlying technology and offer the same
performance.


About the examples
The examples shown here reference the TemperatureService web service from XMethods. This web service returns the
temperature for a given ZIP code. You can read the WSDL file for this web service in "Reading a WSDL file" on
page 1096.

The TemperatureService web service has one input parameter, a string that contains the requested ZIP code. It returns
a float that contains the temperature for the specified ZIP code.


Passing parameters to a web service
The message and operation elements in the WSDL file contains subelements that define the web service operations
and the input and output parameters of each operation, including the data type of each parameter. The following
example shows a portion of the WSDL file for the TemperatureService web service:

<message name="getTempRequest">
     <part name="zipcode" type="xsd:string"/>
</message>
<message name="getTempResponse">
     <part name="return" type="xsd:float"/>
</message>
<portType name="TemperaturePortType">
     <operation name="getTemp">
           <input message="tns:getTempRequest"/>
           <output message="tns:getTempResponse"/>
     </operation>
</portType>




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1099
Using Web Elements and External Objects




The operation name used in the examples is getTemp. This operation takes a single input parameter defined as a
message of type getTempRequest.

You can see that the message element named getTempRequest contains one string parameter: zipcode. When you call
the getTemp operation, you pass the parameter as input.


Handling return values from a web service
Web service operations often return information back to your application. You can determine the name and data type
of returned information by examining subelements of the message and operation elements in the WSDL file.

The following example shows a portion of the WSDL file for the TemperatureService web service:

<message name="getTempRequest">
     <part name="zipcode" type="xsd:string"/>
</message>
<message name="getTempResponse">
     <part name="return" type="xsd:float"/>
</message>
<portType name="TemperaturePortType">
     <operation name="getTemp">
          <input message="tns:getTempRequest"/>
          <output message="tns:getTempResponse"/>
     </operation>
</portType>

The operation getTemp returns a message of type getTempResponse. The message statement in the WSDL file defines
the getTempResponse message as containing a single string parameter named return.


Using cfinvoke to consume a web service
With the cfinvoke tag, you reference the WSDL file and invoke an operation on the web service with a single tag.

The cfinvoke tag includes attributes that specify the URL to the WSDL file, the method to invoke, the return variable,
and input parameters. For complete syntax, see the CFML Reference.

Note: You can pass parameters to a web service using the cfinvokeargument tag or by specifying parameter names in
the cfinvoke tag itself. For more information, see "Passing parameters to methods by using the cfinvoke tag" on
page 196.


Access a web service using cfinvoke
1 Create a ColdFusion page with the following content:

    <cfinvoke
   webservice="http://www.xmethods.net/sd/2001/TemperatureService.wsdl"
   method="getTemp"
   returnvariable="aTemp">
   <cfinvokeargument name="zipcode" value="55987"/>
   </cfinvoke>
   <cfoutput>The temperature at ZIP code 55987 is #aTemp#</cfoutput>

2 Save the page as wscfc.cfm in your web root directory.

3 View the page in your browser.

You can omit a parameter by setting the omit attribute to "yes". If the WSDL specifies that the argument is nullable,
ColdFusion sets the associated argument to null. If the WSDL specifies minoccurs=0, ColdFusion omits the argument
from the WSDL. However, CFC web services must still specify required="true" for all arguments.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1100
Using Web Elements and External Objects




You can also use an attribute collection to pass parameters. An attribute collections is a structure where each structure
key corresponds to a parameter name and each structure value is the parameter value passed for the corresponding
key. The following example shows an invocation of a web service using an attribute collection:

<cfscript>
     stArgs = structNew();
     stArgs.zipcode = "55987";
</cfscript>


<cfinvoke
webservice="http://www.xmethods.net/sd/2001/TemperatureService.wsdl"
method="getTemp"
argumentcollection="#stArgs#"
returnvariable="aTemp">
<cfoutput>The temperature at ZIP code 55987 is #aTemp#</cfoutput>

In this example, you create the structure in a CFScript block, but you can use any ColdFusion method to create the
structure.


Using CFScript to consume a web service
The following example uses CFScript to consume a web service. In CFScript, you use the CreateObject function to
connect to the web service. After connecting, you can make requests to the service. For CreateObject syntax, see the
CFML Reference.

After creating the web service object, you can call operations of the web service using dot notation, in the following form:

webServiceName.operationName(inputVal1, inputVal2, ... );

You can handle return values from web services by writing them to a variable, as the following example shows:

resultVar = webServiceName.operationName(inputVal1, inputVal2, ... );

Or, you can pass the return values directly to a function, such as the WriteOutput function, as the following example
shows:

writeoutput(webServiceName.operationName(inputVal1, inputVal2, ...) );


Access a web service from CFScript
1 Create a ColdFusion page with the following content:

    <cfscript>
   ws = CreateObject("webservice",
   "http://www.xmethods.net/sd/2001/TemperatureService.wsdl");
   xlatstring = ws.getTemp("55987");
   writeoutput(xlatstring);
   </cfscript>

2 Save the page as wscfscript.cfm in your web root directory.

3 View the page in your browser.

You can also use named parameters to pass information to a web service. The following example performs the same
operation as the preceding code, except that it uses named parameters to make the web service request:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1101
Using Web Elements and External Objects




<cfscript>
ws = CreateObject("webservice",
"http://www.xmethods.net/sd/2001/TemperatureService.wsdl");
xlatstring = ws.getTemp(zipcode = "55987");
writeoutput("The temperature at 55987 is " & xlatstring);
</cfscript>


Consuming web services that ColdFusion does not generate
To consume a web service that is implemented in a technology other than ColdFusion, the web service must have one
of the following sets of options:

· rpc as the SOAP binding style and encoding as the encodingStyle

· document as the SOAP binding style and literal as the encodingStyle

The following example shows a portion of the WSDL file for the TemperatureService web service:

<binding name="TemperatureBinding" type="tns:TemperaturePortType">
     <soap:binding style="rpc" transport="http://schemas.xmlsoap.org/soap/http"/>
     <operation name="getTemp">
           <soap:operation soapAction=""/>
           <input>
                <soap:body use="encoded" namespace="urn:xmethods-Temperature"
encodingStyle="http://schemas.xmlsoap.org/soap/encoding/"/>
           </input>
           <output>
                <soap:body use="encoded" namespace="urn:xmethods-Temperature"
encodingStyle="http://schemas.xmlsoap.org/soap/encoding/"/>
           </output>
     </operation>
</binding>

The WSDL file for the TemperatureService web service is compatible with ColdFusion because it uses rpc as the
binding style, and encoding as the encodingStyle.


Calling web services from a Flash client
The Flash Remoting service lets you call ColdFusion pages from a Flash client, but it does not let you call web services
directly. To call web services from a Flash client, you can use Flash Remoting to call a ColdFusion component that calls
the web service. The Flash client can pass input parameters to the component, and the component can return to the
Flash client any data returned by the web service.

For more information, see "Using the Flash Remoting Service" on page 606.


Catching errors when consuming web services
During processing, you can catch in your application errors, including SOAP faults, that otherwise propagate to the
browser.

To catch errors, you specify an error type of application to the ColdFusion cfcatch tag, as the following example
shows:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1102
Using Web Elements and External Objects




<cftry>
      Place your application code here ...
      <cfcatch type="application">
          <!--- Add exception processing code here ... --->
      </cfcatch>
      ...
      <cfcatch type="Any">
          <!--- Add exception processing code appropriate for all other
      exceptions here ... --->
      </cfcatch>
</cftry>

For more information on error handling, see "Handling Errors" on page 275.


Handling inout and out parameters
Some web services define inout and out parameters. You use out parameters to pass a placeholder for a return value to
a web service. The web service then returns its result by writing it to the out parameter. Inout parameters let you pass
a value to a web service and lets the web service return its result by overwriting the parameter value.

The following example shows a web service that takes as input an inout parameter containing a string and writes its
results back to the string:

<cfset S="foo">
<cfscript>
      ws=createobject("webservice", "URLtoWSDL")
      ws.modifyString("S");
</cfscript>
<cfoutput>#S#</cfoutput>

Even though this web service takes as input the value of S, because you pass it as an inout parameter, you do not enclose
it in number signs.

Note: ColdFusion supports the use of inout and out parameters to consume web services. However, ColdFusion does not
support inout and out parameters when creating web services for publication.


Configuring web services in the ColdFusion Administrator
The ColdFusion Administrator lets you register web services so that you do not have to specify the entire WSDL URL
when you reference the web service.

Note: The first time you reference a web service, ColdFusion automatically registers it in the Administrator.

For example, the following code references the URL to the TemperatureService WSDL file:

<cfscript>
ws = CreateObject("webservice",
"http://www.xmethods.net/sd/2001/TemperatureService.wsdl");
xlatstring = ws.getTemp("55987");
writeoutput(xlatstring);
</cfscript>

If you register the TemperatureService web service in the Administrator using (for example, the name wsTemp), you
can then reference the web service as follows:

<cfscript>
       ws = CreateObject("webservice", "wsTemp");
       xlatstring = ws.getTemp("55987");
       writeoutput("wsTemp: " & xlatstring);
</cfscript>



                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 1103
Using Web Elements and External Objects




Not only does registering the service in the Administrator enable you to shorten your code, it lets you change a web
service URL without modifying your code. So, if the TemperatureService web service moves to a new location, you only
update the administrator setting, not your application code.

For more information, see the ColdFusion Administrator online Help.


Data conversions between ColdFusion and WSDL data types
A WSDL file defines the input and return parameters of an operation, including data types. For example, the
TemperatureService web service contains the following definition of input and return parameters:

<message name="getTempRequest">
      <part name="zipcode" type="xsd:string"/>
</message>
<message name="getTempResponse">
      <part name="return" type="xsd:float"/>
</message>

As part of consuming web services, understand how ColdFusion converts WSDL defined data types to ColdFusion
data types. The following table shows this conversion:


 ColdFusion data type                                WSDL data type

 numeric                                             SOAP-ENC:double

 Boolean                                             SOAP-ENC:boolean

 string                                              SOAP-ENC:string

 array                                               SOAP-ENC:Array

 binary                                              xsd:base64Binary

 numeric                                             xsd:float

 string                                              xsd:enumeration

 date                                                xsd:dateTime

 void (operation returns nothing)

 struct                                              complex type

 query                                               tns1:QueryBean (Returned by CFCs)


For many of the most common data types, such as string and numeric, a WSDL data type maps directly to a
ColdFusion data type. For complex WSDL data types, the mapping is not as straight forward. In many cases, you map
a complex WSDL data type to a ColdFusion structure. For more information on handling complex data types, see
"Handling complex data types" on page 1115.


Consuming ColdFusion web services
Your application can consume web services created in ColdFusion. You do not have to perform any special processing
on the input parameters or return values because ColdFusion handles data mappings automatically when consuming
a ColdFusion web service.

For example, when ColdFusion publishes a web service that returns a query, or takes a query as an input, the WSDL
file for that service lists its data type as QueryBean. However, a ColdFusion application consuming this web service
can pass a ColdFusion query object to the function as an input, or write a returned QueryBean to a ColdFusion query
object.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               1104
Using Web Elements and External Objects




Note: For a list of how ColdFusion data types map to WSDL data types, see "Data conversions between ColdFusion and
WSDL data types" on page 1103.

The following example shows a ColdFusion component that takes a query as input and echoes the query back to the
caller:

<cfcomponent>
     <cffunction name='echoQuery' returnType='query' access='remote'>
          <cfargument name='input' type='query'>
          <cfreturn #arguments.input#>
     </cffunction>
</cfcomponent>

In the WSDL file for the echotypes.cfc component, you see the following definitions that specify the type of the
function input and output as QueryBean:

<wsdl:message name="echoQueryResponse">
       <wsdl:part name="echoQueryReturn" type="tns1:QueryBean"/>
</wsdl:message>
<wsdl:message name="echoQueryRequest">
       <wsdl:part name="input" type="tns1:QueryBean"/>
</wsdl:message>

For information on displaying WSDL, see "Producing WSDL files" on page 1106.

Since ColdFusion automatically handles mappings to ColdFusion data types, you can call this web service as the
following example shows:

<head>
<title>Passing queries to web services</title>
</head>
<body>
<cfquery name="GetEmployees" datasource="cfdocexamples">
     SELECT FirstName, LastName, Salary
     FROM Employee
</cfquery>


<cfinvoke
     webservice = "http://localhost/echotypes.cfc?wsdl"
     method = "echoQuery"
     input="#GetEmployees#"
     returnVariable = "returnedQuery">


<cfoutput>
     Is returned result a query? #isQuery(returnedQuery)# <br><br>
</cfoutput>


<cfoutput query="returnedQuery">
     #FirstName#, #LastName#, #Salary#<br>
</cfoutput>
</body>



Publishing web services
To publish web services for consumption by remote applications, you create the web service using ColdFusion
components. For more information on components, see "Building and Using ColdFusion Components" on page 177.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1105
Using Web Elements and External Objects




Creating components for web services
ColdFusion components (CFCs) encapsulate application functionality and provide a standard interface for client
access to that functionality. A component typically contains one or more functions defined by the cffunction tag.

For example, the following component contains a single function:

<cfcomponent>
      <cffunction name="echoString" returnType="string" output="no">
          <cfargument name="input" type="string">
          <cfreturn #arguments.input#>
      </cffunction>
</cfcomponent>

The function, named echoString, echoes back any string passed to it. To publish the function as a web service, modify
the function definition to add the access attribute and specify remote, as the following example shows:

<cffunction name="echoString" returnType="string" output="no" access="remote">

By defining the function as remote, ColdFusion includes the function in the WSDL file. Only those functions marked
as remote are accessible as a web service.

The following list defines the requirements for how to create web services for publication:

1 The value of the access attribute of the cffunction tag must be remote.

2 The cffunction tag must include the returnType attribute to specify a return type.

3 The output attribute of the cffunction tag must be set to No because ColdFusion converts all output to XML to
    return it to the consumer.

4 The attribute setting required="false" for the cfargument tag is ignored. ColdFusion considers all parameters
    as required.


Specifying data types of function arguments and return values
The cffunction tag lets you define a single return value and one or more input parameters passed to a function. As
part of the function definition, you include the data type of the return value and input parameters.

The following example shows a component that defines a function with a return value of type string, one input
parameter of type string, and one input parameter of type numeric:

<cfcomponent>
      <cffunction name="trimString" returnType="string" output="no">
          <cfargument name="inString" type="string">
          <cfargument name="trimLength" type="numeric">
      </cffunction>
</cfcomponent>

As part of publishing the component for access as a web service, ColdFusion generates the WSDL file that defines the
component where the WSDL file includes definitions for how ColdFusion data types map to WSDL data types. The
following table shows this mapping:


 ColdFusion data type                             WSDL data type published

 numeric                                          SOAP-ENC:double

 Boolean                                          SOAP-ENC:boolean

 string                                           SOAP-ENC:string

 array                                            SOAP-ENC:Array




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 1106
Using Web Elements and External Objects




 ColdFusion data type                             WSDL data type published

 binary                                           xsd:base64Binary

 date                                             xsd:dateTime

 guid                                             SOAP-ENC:string

 uuid                                             SOAP-ENC:string

 void (operation returns nothing)

 struct                                           Map

 query                                            QueryBean

 any                                              complex type

 component definition                             complex type


In most cases, consumers of ColdFusion web services can easily pass data to and return results from component
functions by mapping their data types to the WSDL data types shown in the preceding table.

Note: Document-literal web services use XML schema data types, not SOAP-ENC data types. For more information, see
"Publishing document-literal style web services" on page 1110.

For ColdFusion structures and queries, Some clients must process their data to map it to the correct type. For more
information, see "Publishing web services that use complex data types" on page 1119.

You can also define a data type in one ColdFusion component based on another component definition. For more
information on using components to specify a data type, see "Using ColdFusion components to define data types for
web services" on page 1109.


Producing WSDL files
ColdFusion automatically creates a WSDL file for any component referenced as a web service. For example, if you have
a component named echo.cfc in your web root directory, you can view its corresponding WSDL file by requesting the
component as follows:

http://localhost/echo.cfc?wsdl

The cfcomponent tag includes optional attributes that you can use to control the WSDL that ColdFusion generates.
You can use these attributes to create meaningful WSDL attribute names, as the following example shows:

<cfcomponent style="document"
namespace = "http://www.mycompany.com/"
      serviceportname = "RestrictedEmpInfo"
      porttypename = "RestrictedEmpInfo"
      bindingname = "myns:RestrictedEmpInfo"
      displayname = "RestrictedEmpInfo"
      hint = "RestrictedEmpInfo">

    For complete control of the WSDL, advanced users can specify the cfcomponentwsdlFile attribute to use a
    predefined WSDL file.

The following example defines a ColdFusion component that can be invoked as a web service:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                           1107
Using Web Elements and External Objects




<cfcomponent>
     <cffunction
     name = "echoString"
     returnType = "string"
     output = "no"
     access = "remote">
          <cfargument name = "input" type = "string">
          <cfreturn #arguments.input#>
     </cffunction>
</cfcomponent>

   If you register the component in Dreamweaver, it appears in the Components tab of the Application panel.


Requesting the WSDL file in a browser returns the following:

<?xml version="1.0" encoding="UTF-8"?>
<wsdl:definitions targetNamespace="http://ws"
xmlns="http://schemas.xmlsoap.org/wsdl/"
xmlns:apachesoap="http://xml.apache.org/xml-soap"
xmlns:impl="http://ws"
xmlns:intf="http://ws"
xmlns:soapenc="http://schemas.xmlsoap.org/soap/encoding/"
xmlns:tns1="http://rpc.xml.coldfusion"
xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/"
xmlns:wsdlsoap="http://schemas.xmlsoap.org/wsdl/soap/"
xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<!--WSDL created by ColdFusion -->
<wsdl:types>
<schema targetNamespace="http://rpc.xml.coldfusion"
xmlns="http://www.w3.org/2001/XMLSchema">
<import namespace="http://schemas.xmlsoap.org/soap/encoding/"/>
<complexType name="CFCInvocationException">
<sequence/>
</complexType>
</schema>
</wsdl:types>
<wsdl:message name="CFCInvocationException">
<wsdl:part name="fault" type="tns1:CFCInvocationException"/>
</wsdl:message>
<wsdl:message name="echoStringResponse">
<wsdl:part name="echoStringReturn" type="xsd:string"/>
</wsdl:message>
<wsdl:message name="echoStringRequest">
<wsdl:part name="input" type="xsd:string"/>
     </wsdl:message>
     <wsdl:portType name="echo">
     <wsdl:operation name="echoString" parameterOrder="input">
     <wsdl:input message="impl:echoStringRequest" name="echoStringRequest"/>
     <wsdl:output message="impl:echoStringResponse"
                     name="echoStringResponse"/>
     <wsdl:fault message="impl:CFCInvocationException" name="CFCInvocationException"/>
     </wsdl:operation>
     </wsdl:portType>
     <wsdl:binding name="echo.cfcSoapBinding" type="impl:echo">
     <wsdlsoap:binding style="rpc" transport="http://schemas.xmlsoap.org/soap/
                http"/>




                                           

DEVELOPING COLDFUSION 9 APPLICATIONS                                             1108
Using Web Elements and External Objects




    <wsdl:operation name="echoString">
    <wsdlsoap:operation soapAction=""/>
    <wsdl:input name="echoStringRequest">
    <wsdlsoap:body encodingStyle="http://schemas.xmlsoap.org/soap/
                          encoding/" namespace="http://ws" use="encoded"/>
    </wsdl:input>
    <wsdl:output name="echoStringResponse">
    <wsdlsoap:body encodingStyle="http://schemas.xmlsoap.org/soap/
                          encoding/" namespace="http://ws" use="encoded"/>
    </wsdl:output>
    <wsdl:fault name="CFCInvocationException">
    <wsdlsoap:fault encodingStyle="http://schemas.xmlsoap.org/soap/
                          encoding/" name="CFCInvocationException" namespace=
                          "http://ws" use="encoded"/>
    </wsdl:fault>
    </wsdl:operation>
    </wsdl:binding>
    <wsdl:service name="echoService">
    <wsdl:port binding="impl:echo.cfcSoapBinding" name="echo.cfc">
    <wsdlsoap:address location="http://localhost:8500/ws/echo.cfc"/>
    </wsdl:port>
    </wsdl:service>
</wsdl:definitions>


Publish a web service
1 Create a ColdFusion page with the following content:

   <cfcomponent output="false">
       <cffunction
                       name = "echoString"
                       returnType = "string"
                       output = "no"
                       access = "remote">
             <cfargument name = "input" type = "string">
             <cfreturn #arguments.input#>
       </cffunction>
   </cfcomponent>

2 Save this file as echo.cfc in your web root directory.

3 Create a ColdFusion page with the following content:

   <cfinvoke webservice ="http://localhost/echo.cfc?wsdl"
       method ="echoString"
       input = "hello"
       returnVariable="foo">


   <cfoutput>#foo#</cfoutput>

4 Save this file as echoclient.cfm in your web root directory.

5 Request echoclient.cfm in your browser.

   The following string appears in your browser:

   hello

You can also invoke the web service using the following code:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1109
Using Web Elements and External Objects




<cfscript>
     ws = CreateObject("webservice", "http://localhost/echo.cfc?wsdl");
     wsresults = ws.echoString("hello");
     writeoutput(wsresults);
</cfscript>


Using ColdFusion components to define data types for web services
ColdFusion lets you define components that contain only properties. Once defined, you can use components to define
data types for web services. The following code defines a component in the file address.cfc that contains properties that
represent a street address:

<cfcomponent>
     <cfproperty name="AddrNumber" type="numeric">
     <cfproperty name="Street" type="string">
     <cfproperty name="City" type="string">
     <cfproperty name="State" type="string">
     <cfproperty name="Country" type="string">
</cfcomponent>

The following code defines a component in the filename.cfc that defines first and last name properties:

<cfcomponent>
     <cfproperty name="Firstname" type="string">
     <cfproperty name="Lastname" type="string">
</cfcomponent>

You can then use address and name to define data types in a ColdFusion component created to publish a web service,
as the following example shows:

<cfcomponent>
     <cffunction
           name="echoName" returnType="name" access="remote" output="false">
                <cfargument name="input" type="name">
                <cfreturn #arguments.input#>
     </cffunction>


     <cffunction
           name="echoAddress" returnType="address" access="remote" output="false">
                <cfargument name="input" type="address">
                <cfreturn #arguments.input#>
     </cffunction>
</cfcomponent>

Note: If the component files are not in a directory under your web root, create a web server mapping to the directory that
contains them. You cannot use ColdFusion mappings to access web services.

The WSDL file for the web service contains data definitions for the complex types name and address. Each definition
consists of the elements that define the type as specified in the ColdFusion component file for that type. For example,
the following example shows the definition for name:

<complexType name="name">
      <sequence>
          <element name="firstname" nillable="true" type="soapenc:string"/>
          <element name="lastname" nillable="true" type="soapenc:string"/>
      </sequence>
</complexType>

You can also specify an array of CFCs in the returnType attribute, as the following example shows:



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1110
Using Web Elements and External Objects




<cfcomponent>
     <cffunction
     name="allNames" returnType="name[]" access="remote" output="false">
     <cfset var returnarray = ArrayNew(1)>
     <cfset var temp = "">
     <cfquery name="empinfo" datasource="cfdocexamples">
      SELECT firstname, lastname
      FROM employee
     </cfquery>
     <cfloop query="empinfo" >
              <cfobject component="name" name="tempname">
              <cfset tempname.Firstname = #empinfo.firstname#>
              <cfset tempname.Lastname = #empinfo.lastname#>
              <cfset temp = ArrayAppend(returnarray, tempname)>
           </cfloop>
           <cfreturn returnarray>
     </cffunction>
</cfcomponent>

When you invoke the web service, it returns an array of CFCs. Access the properties in the CFC by using dot notation,
as the following example shows:

<cfinvoke webservice ="http://localhost:8500/ws/cfcarray.cfc?wsdl"
     method ="allNames"
     returnVariable="thearray">


<cfif IsArray(thearray)>
<h1>loop through the employees</h1>
<p>thearray has <cfoutput>#ArrayLen(thearray)#</cfoutput> elements.</p>
<cfloop index="i" from="1" to="#ArrayLen(thearray)#">
     <cfoutput>#thearray[i].firstname#, #thearray[i].lastname# </cfoutput><br>
</cfloop>
<cfelse>
      <h1>Error: thearray is not an array</h1>
</cfif>


Publishing document-literal style web services
In addition to RPC-oriented operations, for which consumers specify a method and arguments, ColdFusion also lets
you publish web services using the document-literal style. When you use document-literal style, the WSDL for the web
service tells the client to use XML schemas rather than RPC calling conventions.

In most cases, the publisher of a web services identifies it as document-literal or RPC style. To identify the type, open
the WSDL document and find the soap:binding element and examine its style attribute, as the following example
shows:

<wsdl:binding name="WeatherForecastSoap" type="tns:WeatherForecastSoap">
<soap:binding transport="http://schemas.xmlsoap.org/soap/http" style="document" />

In this example, the style is document-literal. Examine the WSDL to determine the methods you can call and the
parameters for each method.

On the client side, the cfinvoke tag and other ColdFusion methods for calling web services handle the style
automatically. In most cases, no modifications are necessary. Similarly, when publishing CFCs as document-literal
style web services, ColdFusion automatically creates and manages the appropriate WSDL.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1111
Using Web Elements and External Objects




To publish CFCs as document-literal style web services, specify cfcomponent style="document", along with the
other attributes required for document-literal style web services. For example, ColdFusion publishes the following
CFC using document-literal style:

<cfcomponent style="document" >
     <cffunction
name = "getEmp"
returntype="string"
output = "no"
access = "remote">
     <cfargument name="empid" required="yes" type="numeric">
     <cfset var fullname = "">
     <cfquery name="empinfo" datasource="cfdocexamples">
      SELECT emp_id, firstname, lastname
      FROM employee
      WHERE emp_id = <cfqueryparam cfsqltype="cf_sql_integer"
                value="#arguments.empid#">
     </cfquery>
     <cfif empinfo.recordcount gt 0>
      <cfset fullname = empinfo.lastname & ", " & empinfo.firstname>
     <cfelse>
     <cfset fullname = "not found">
     </cfif>
<cfreturn #fullname#>
</cffunction>
</cfcomponent>


Securing your web services
You can restrict access to your published web services to control the users allowed to invoke them. You can use your
web server to control access to the directories containing your web services, or you can use ColdFusion security in the
same way that you would to control access to any ColdFusion page.

To browse the HTML description of a CFC file, you request the file by specifying a URL to the file in your browser. By
default, ColdFusion secures access to all URLs that directly reference a CFC file, and prompts you to enter a password
upon the request. Use the ColdFusion RDS password to view the file.

To disable security on CFC file browsing, use the ColdFusion Administrator to disable the RDS password.

For more information, see "Building and Using ColdFusion Components" on page 177.


Using your web server to control access
Most web servers, including IIS and Apache, implement directory access protection using the basic HTTP
authentication mechanism. When a client attempts to access one of the resources under a protected directory, and has
not properly authenticated, the web server automatically sends back an authentication challenge, typically an HTTP
Error 401 Access Denied error.

In response, the client browser opens a login prompt containing a user name and password field. When the user
submits this information, the browser sends it back to the web server. If authentication passes, the web server allows
access to the directory. The browser also caches the authentication data as long as it is open, so subsequent requests
automatically include the authentication data.

Web service clients can also pass the user name and password information as part of the request. The cfinvoke tag
includes the user name and password attributes that let you pass login information to a web server using HTTP basic
authentication. You can include these attributes when invoking a web service, as the following example shows:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1112
Using Web Elements and External Objects




<cfinvoke
     webservice = "http://some.cfc?wsdl"
     returnVariable = "foo"
     ...
     username="aName"
     password="aPassword">
<cfoutput>#foo#</cfoutput>

ColdFusion inserts the user name/password string in the authorization request header as a base64 binary encoded
string, with a colon separating the user name and password. This method of passing the user name/password is
compatible with the HTTP basic authentication mechanism used by web servers.

The ColdFusion Administrator lets you predefine web services. As part of defining the web service, you can specify the
user name and password that ColdFusion includes as part of the request to the web service. Therefore, you do not have
to encode this information using the cfinvoke tag. For information on defining a web service in the ColdFusion
Administrator, see "Configuring web services in the ColdFusion Administrator" on page 1102.


Using ColdFusion to control access
Instead of letting the web server control access to your web services, you can handle the user name/password string in
your Application.cfc or Application.cfm file as part of your own security mechanism. In this case, you use the cflogin
tag to retrieve the user name/password information from the authorization header, decode the binary string, and
extract the user name and password, as the following excerpt from an Application.cfc onRequestStart method
shows:

<cflogin>
     <cfset isAuthorized = false>


     <cfif isDefined("cflogin")
           <!--- Verify user name from cflogin.name and password from
     cflogin.password using your authentication mechanism. --->
           >
           <cfset isAuthorized = true>
     </cfif>
</cflogin>


<cfif not isAuthorized>
     <!--- If the user does not pass a user name/password, return a 401 error.
           The browser then prompts the user for a user name/password. --->
     <cfheader statuscode="401">
     <cfheader name="WWW-Authenticate" value="Basic realm=""Test""">
     <cfabort>
</cfif>

This example does not show how to perform user verification. For more information on verification, see "Securing
Applications" on page 339.


Best practices for publishing web services
ColdFusion web services provide a powerful mechanism for publishing and consuming application functionality.
However, before you produce web services for publication, consider the following best practices:

1 Minimize the use of ColdFusion complex types, such as query and struct, in the web services you create for
   publication. These types require consumers, especially ones that consume the web service using a technology other
   than ColdFusion, to create special data structures to handle complex types.

2 Locally test the ColdFusion components implemented for web services before publishing them over the Internet.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1113
Using Web Elements and External Objects




Using request and response headers
ColdFusion includes a set of functions that enable your web service to get and set request and response headers. You
use these functions to retrieve the response headers from a web service request and to create SOAP headers in a request
that has the mustUnderstand attribute set to be True.

You typically use different functions in web services clients and in the web service CFC, itself:

In the client:

· AddSOAPRequestHeader, called before the request to set a SOAP header.

· GetSOAPResponseHeader, called after the request to retrieve a SOAP header.

· GetSOAPResponse, called after the request to retrieve a SOAP response.

In the web service CFC:

· IsSOAPRequest, called to determine whether the CFC method is being called as a web service.

· GetSOAPRequestHeader, called to retrieve a SOAP header set by the client.

· GetSOAPRequest, called to retrieve a SOAP request sent by the client.

· AddSOAPResponseHeader, called to set a SOAP header that is returned to the client.

Note: When used in a CFC, you can only use these functions in CFC methods if they are being used as web services. Use
the IsSOAPRequest function to determine whether the CFC method is being called as a web service.

The following example CFM page uses the AddSOAPRequestHeader, getSOAPRequest, and GetSOAPResponse
functions:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                        1114
Using Web Elements and External Objects




<cfsavecontent variable="my_xml">
<Request xmlns="http://www.oasis-open.org/asap/0.9/asap.xsd">
<SenderKey>ss</SenderKey>
<ReceiverKey>zz</ReceiverKey>
<ResponseRequired>Yes</ResponseRequired>
<RequestID>id</RequestID>
</Request>
</cfsavecontent>
<cfset xml_obj = xmlparse(my_xml)>


<cfscript>
ws = CreateObject("webservice", "http://localhost:8500/soapexamples/HeaderFuncs.cfc?WSDL");
addSOAPRequestHeader(ws, "http://www.cfdevguide.com/", "testrequestheader", "#xml_obj#");
</cfscript>


<cfscript>
ret=ws.showSOAPHeaders();
inxml = getSOAPRequest(ws);
outxml = getSOAPResponse(ws);
</cfscript>


<cfoutput>
<h2>Return Value</h2>
<!--- This code is XML, so use HTMLCodeFormat. --->
The return value was #ret#
<h2>Complete Request XML</h2>
#htmlcodeformat(inxml)#
<h2>Complete Response XML</h2>
#htmlcodeformat(outxml)#
</cfoutput>

The following example CFC uses the IsSOAPRequest and AddSOAPResponseHeader functions:




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                               1115
Using Web Elements and External Objects




<cfcomponent>
<cffunction
     name = "showSOAPHeaders"
     returnType = "string"
     output = "no"
     access = "remote"
      hint="After calling this function, use GetSOAPRequest and GetSOAPResponse to view
headers">
     <cfset var xml_obj = "">
     <cfset var ret = "">


<cfif IsSOAPRequest()>
<!--- Define a response header --->
<cfsavecontent variable="response_xml">
     <ThisResponseHeader xmlns="http://www.cfdevguide.com">
     <CreatedDateTime><cfoutput>#now()#</cfoutput></CreatedDateTime>
     <ExpiresInterval>6000</ExpiresInterval>
     </ThisResponseHeader>
</cfsavecontent>
<cfset xml_obj = xmlparse(response_xml)>
<!--- Add the response header --->
<cfscript>
addSOAPResponseHeader("http://www.cfdevguide.com/", "testresponseheader", "#xml_obj#");
ret = "Invoked as a web service. Use GetSOAPRequest and GetSOAPResponse to view headers.";
     </cfscript>
<cfelse>
<cfset ret = "Not invoked as a web service">
</cfif>
<cfreturn ret>
</cffunction>
</cfcomponent>



Handling complex data types
When dealing with web services, handling complex types falls into the following categories:

· Mapping the data types of a web service to consume to ColdFusion data types

· Understanding how clients reference your ColdFusion data types when you publish a web service


Consuming web services that use complex data types
The following table shows how WSDL data types are converted to ColdFusion data types:


ColdFusion data type                           WSDL data type

numeric                                        SOAP-ENC:double

Boolean                                        SOAP-ENC:boolean

string                                         SOAP-ENC:string

array                                          SOAP-ENC:Array

numeric                                        SOAP-ENC:float

binary                                         xsd:base64Binary




                                           

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1116
Using Web Elements and External Objects




 ColdFusion data type                            WSDL data type

 date                                            xsd:dateTime

 void (operation returns nothing)

 structure                                       complex type


This table shows that complex data types map to ColdFusion structures. ColdFusion structures offer a flexible way to
represent data. You can create structures that contain single-dimension arrays, multi-dimensional arrays, and other
structures.

The ColdFusion mapping of complex types to structures is not automatic. Accessing the data as a structure requires
some processing of the data. The next sections describe how to pass complex types to web services, and how to handle
complex types returned from web services.


Passing input parameters to web services as complex types
A web service can take a complex data type as input. In this situation, you can construct a ColdFusion structure that
models the complex data type, then pass the structure to the web service.

For example, the following excerpt from a WSDL file shows the definition of a complex type named Employee:

<s:complexType name="Employee">
      <s:sequence>
         <s:element minOccurs="1" maxOccurs="1" name="fname" type="s:string" />
         <s:element minOccurs="1" maxOccurs="1" name="lname" type="s:string" />
         <s:element minOccurs="1" maxOccurs="1" name="active" type="s:boolean" />
         <s:element minOccurs="1" maxOccurs="1" name="age" type="s:int" />
         <s:element minOccurs="1" maxOccurs="1" name="hiredate" type="s:dateTime" />
         <s:element minOccurs="1" maxOccurs="1" name="number" type="s:double" />
      </s:sequence>
</s:complexType>

The Employee data type definition includes six elements, the data type of each element, and the name of each element.

Another excerpt from the WSDL file shows a message definition using the Employee data type. This message defines
an input parameter, as the following code shows:

<message name="updateEmployeeInfoSoapIn">
      <part name="thestruct" type="s0:Employee" />
</message>

A third excerpt from the WSDL file shows the definition of an operation, named updateEmployeeInfo, possibly one
that updates the employee database with the employee information. This operation takes as input a parameter of type
Employee, as the following code shows:

<operation name="updateEmployeeInfo">
      <input message="s0:updateEmployeeInfoSoapIn" />
</operation>

To call the updateEmployeeInfo operation, create a ColdFusion structure, initialize six fields of the structure that
correspond to the six elements of Employee, and then call the operation, as the following code shows:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1117
Using Web Elements and External Objects




<!--- Create a structure using CFScript, then call the web service. --->
<cfscript>
     stUser = structNew();
     stUser.active = TRUE;
     stUser.fname = "John";
     stUser.lname = "Smith";
     stUser.age = 23;
     stUser.hiredate = createDate(2002,02,22);
     stUser.number = 123.321;


     ws = createObject("webservice", "http://somehost/EmployeeInfo.asmx?wsdl");
     ws.updateEmployeeInfo(stUser);
</cfscript>

You can use structures for passing input parameters as complex types in many situations. However, to build a structure
to model a complex type, inspect the WSDL file for the web service to determine the layout of the complex type. This
process can take some practice.


Handling return values as complex types
When a web service returns a complex type, you can write that returned value directly to a ColdFusion variable.

The previous section used a complex data type named Employee to define an input parameter to an operation. A
WSDL file can also define a return value using the Employee type, as the following code shows:

<message name="updateEmployeeInfoSoapOut">
     <part name="updateEmployeeInfoResult" type="s0:Employee" />
</message>
<operation name="updateEmployeeInfo">
     <input message="s0:updateEmployeeInfoSoapIn" />
     <output message="s0:updateEmployeeInfoSoapOut" />
</operation>

In this example, the operation updateEmployeeInfo takes a complex type as input and returns a complex type as
output. To handle the input parameter, you create a structure. To handle the returned value, you write it to a
ColdFusion variable, as the following example shows:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1118
Using Web Elements and External Objects




<!--- Create a structure using CFScript, then call the web service. --->
<!--- Write the returned value to a ColdFusion variable. --->
<cfscript>
      stUser = structNew();
      stUser.active = TRUE;
      stUser.fname = "John";
      stUser.lname = "Smith";
      stUser.age = 23;
      stUser.hiredate = createDate(2002,02,22);
      stUser.number = 123.321;


      ws = createObject("webservice", "http://somehost/echosimple.asmx?wsdl");
      myReturnVar = ws.echoStruct(stUser);


</cfscript>


<!--- Output the returned values. --->
<cfoutput>
      <br>
      <br>Name of employee is: #myReturnVar.fname##myReturnVar.lname#
      <br>Active status: #myReturnVar.active#
      <br>Age:#myReturnVar.age#
      <br>Hire Date: #myReturnVar.hiredate#
      <br>Favorite Number: #myReturnVar.number#
</cfoutput>

You access elements of the variable myReturnVar using dot notation in the same way that you access structure fields.
If a complex type has nested elements, in the way a structure can have multiple levels of nested fields, you use dot
notation to access the nested elements, as in a.b.c.d, to whatever nesting level is necessary.

However, the variable myReturnVar is not a ColdFusion structure. It is a container for the complex type, but has none
of the attributes of a ColdFusion structure. Calling the ColdFusion function isStruct on the variable returns False.

You can copy the contents of the variable to a ColdFusion structure, as the following example shows:

<cfscript>
...
      ws = createObject("webservice", "http://somehost/echosimple.asmx?wsdl");
      myReturnVar = ws.echoStruct(stUser);


      realStruct = structNew();
      realStruct.active = #myReturnVar.active#;
      realStruct.fname = "#myReturnVar.fname#";
      realStruct.lname = "#myReturnVar.lname#";
      realStruct.age = #myReturnVar.age#;
      realStruct.hiredate = #myReturnVar.hiredate#;
      realStruct.number = #myReturnVar.number#;


</cfscript>

Calling IsStruct on realStruct returns True and you can use all ColdFusion structure functions to process it.

This example shows that ColdFusion variables and structures are useful for handling complex types returned from web
services. To understand how to access the elements of a complex type written to a ColdFusion variable, inspect the
WSDL file for the web service. The WSDL file defines the API to the web service and provides you with the information
necessary to handle data returned from it.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1119
Using Web Elements and External Objects




Publishing web services that use complex data types
The two ColdFusion data types that do not map exactly to WSDL data types are struct and query. When you publish
a ColdFusion web service that uses parameters of type struct or query, the consuming application must be able to
handle the data.

Note: If the consumer of a ColdFusion web service is another ColdFusion application, you do not have to perform any
special processing. ColdFusion correctly maps struct and query data types in the web service publisher with the consumer.
For more information, see "Consuming ColdFusion web services" on page 1103.


Publishing structures
A ColdFusion structure can hold an unlimited number of key-value pairs where the values can be of any ColdFusion
data type. While it is a useful and powerful way to represent data, it cannot be directly mapped to any XML data types
defined in the SOAP 1.1 encoding and XML Schema specification. Therefore, ColdFusion structures are treated as a
custom type and the complex type XML schema in WSDL looks like the following:

<complexType name="mapItem">
      <sequence>
         <element name="key" nillable="true" type="xsd:anyType"/>
         <element name="value" nillable="true" type="xsd:anyType"/>
      </sequence>
</complexType>
<complexType name="Map">
      <sequence>
         <element maxOccurs="unbounded" minOccurs="0" name="item" type="apachesoap:mapItem"/>
      </sequence>
</complexType>

This complex type defines a representation of a structure, where the structure keys and values can be any type.

In the WSDL mapping of a ColdFusion structure, each key-value pair in the structure points to the next element in the
structure except for the final field, which contains a value. Use dot notation to access the key-value pairs.


Publishing queries
ColdFusion publishes query data types as the WSDL type QueryBean. The QueryBean data type contains two
elements, as the following excerpt from a WSDL file shows:

<complexType name="QueryBean">
     <all>
          <element name="data" nillable="true" type="intf:ArrayOf_SOAP-ENC_Array" />
          <element name="ColumnList" nillable="true"
     type="intf:ArrayOf_SOAP-ENC_string" />
     </all>
</complexType>

The following table describes the elements of QueryBean:


Element name                          Description

ColumnList                            String array that contains column names

data                                  Two-dimensional array that contains query data


The WSDL file for a QueryBean defines these elements as follows:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1120
Using Web Elements and External Objects




<complexType name="ArrayOf_SOAP-ENC_Array">
    <complexContent>
          <restriction base="SOAP-ENC:Array">
               <attribute ref="SOAP-ENC:arrayType" wsdl:arrayType="SOAP-ENC:Array[]" />
          </restriction>
    </complexContent>
</complexType>
<complexType name="ArrayOf_SOAP-ENC_string">
    <complexContent>
    <restriction base="SOAP-ENC:Array">
          <attribute ref="SOAP-ENC:arrayType" wsdl:arrayType="xsd:string[]" />
    </restriction>
    </complexContent>
</complexType>



Troubleshooting SOAP requests and responses
ColdFusion provides the following facilities for troubleshooting SOAP requests and responses:

· The getSOAPRequest and getSOAPResponse functions.

· The TCP monitor.


Viewing SOAP requests and responses
You can use the getSOAPRequest and getSOAPResponse functions to retrieve and display the XML passed to and
from a web service. Although advanced users can use this information for custom functionality, you typically use these
functions for debugging.

Use these functions in the following places:

· GetSOAPRequest Clients call this function after the web service request; web service CFCs call this function in
   the web service CFC method.

· GetSOAPResponse Clients call this function after the web service request completes; web service CFCs cannot use
   this method.

The following example uses the GetSOAPRequest and GetSOAPResponse functions in a web service client:

<cfscript>
    ws = CreateObject("webservice", "http://localhost:8500/soapexamples/tester.cfc?WSDL");
    addSOAPRequestHeader(ws, "http://mynamespace/", "username", "randy");
    ret = ws.echo_me("value");
</cfscript>


<cfset soapreq = GetSOAPRequest(ws)>
<h2>SOAP Request</h2>
<cfdump var="#soapreq#">


<cfset soapresp = GetSOAPResponse(ws)>
<h2>SOAP Response</h2>
<cfdump var="#soapresp#">
...

The following example uses the GetSOAPRequest function in a web service CFC method:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   1121
Using Web Elements and External Objects




<cfcomponent displayName="testerdebug" hint="Test for underscores">


<cffunction access="remote" name="echo_me" output="false" returntype="string"
displayname="Echo Test" hint="Header test">
<cfargument name="in_here" required="true" type="string">
       <cfset var soapreq = "">


<cfif IsSOAPRequest()>
<cfset soapreq = GetSOAPRequest()>
     <cflog text="#soapreq#"
     log="APPLICATION"
     type="Information">
...


Using the TCP monitor
TCPMonitor is a swing-based application that lets you watch the request and response flow of HTTP traffic. You can
also watch the request and response flow of SOAP traffic. TCPMonitor replaces the Sniffer service formerly used in
Macromedia JRun.


Run TCPMonitor
 On Windows and UNIX platforms, you can execute the TCPMonitor by launching the sniffer utility in the
   jrun_root/bin directory.

   The TCP Monitor main window appears.

   TCPMonitor is a swing-based application that lets you watch the request and response flow of HTTP traffic.
   However, you can also use it to watch the request and response flow of SOAP traffic.


To run TCPMonitor:
1 On Windows and UNIX platforms, you can execute the TCPMonitor by launching the sniffer utility in the
   cf_root/bin (server configuration) or jrun_root/bin (multiserver configuration) directory.

   The TCP Monitor main window appears.

   Note: In the J2EE configuration, run the utility directly out of the JAR file by using the following command:

   java -cp cf_webapp_root/WEB-INF/cfusion/lib/axis.jar java org.apache.axis.utils.tcpmon
   [listening_port] [target_host] [target_port]

2 Enter the values in the main window as described in the following table:


    Field                          Description

    Listen Port#                   Enter a local port number, such as 8123, to monitor for incoming connections. Instead of requesting
                                   the usual port on which your server runs, you request this port. TCPMonitor intercepts the request
                                   and forwards it to the Target Port.

    Listener                       Select Listener to use TCPMonitor as a sniffer service in JRun.

    Proxy                          Select Proxy to enable proxy support for TCPMonitor.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                       1122
Using Web Elements and External Objects




    Field                            Description

    Target Hostname                  Enter the target host to which incoming connections are forwarded.

                                     For example, if you are monitoring a service running on a local server, the hostname is localhost.

    Target Port#                     Enter the port number on the target machine to which TCPMonitor connects. For example, if you are
                                     monitoring a service running on your local ColdFusion server in the server configuration, the default
                                     port number is 8500.

    HTTP Proxy Support               Select this check box only to configure proxy support for TCPMonitor.


   You can optionally specify the Listen Port#, Target Hostname and Target Port# values when invoking TCPMonitor
   on the command line. The following is the syntax for TCPMonitor:

    java org.apache.axis.utils.tcpmon [listening_port] [target_host] [target_port]

3 To add this profile to your TCPMonitor session, click Add.

   A tab appears for your new tunneled connection.

4 Select the new tab. If port conflicts exist, TCPMonitor alerts you in the Request panel.

5 Request a page using the Listen Port defined in this TCPMonitor session. For example, if you entered 8123 for the
   Listen Port, enter the following URL in your browser:

    http://localhost:8123/

   TCPMonitor displays the current request and response information:

   For each connection, the request appears in the Request panel and the response appears in the Response panel.
   TCPMonitor keeps a log of all request-response pairs and lets you view any particular pair by selecting an entry in
   the top panel.

6 To save results to a file for later viewing, click Save. To clear the top panel of older requests that you do not want to
   save, click Remove Selected and Remove All.

7 To resend the request that you are currently viewing and view a new response, click Resend. You can edit the
   request in the Request panel before resending, and test the effects of different requests.

8 To change the ports, click Stop, change the port numbers, and click Start.

9 To add another listener, click the Admin tab and enter the values as described previously.

10 To end this TCPMonitor session, click Close.



Using ColdFusion Web Services

ColdFusion can now expose many of its features as document or literal style SOAP web services. You can leverage
ColdFusion functionality using web services from other languages like PHP, .NET, or Ruby.You can access the features
of the following tags (and their child tags) as SOAP services:

· cfchart

· cfdocument

· cfimage

· cfmail

· cfpop




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1123
Using Web Elements and External Objects




· cfpdf


WorkFlow for a ColdFusion web service
To start using ColdFusion web services, you need to first enable access to ColdFusion services using ColdFusion
Administrator. Once you configure the access permissions:

1 Upload the file to the server (for example, PHP), which calls ColdFusion web services.

2 Call ColdFusion web service with the HTTP source URL pointing to the uploaded file, service user name, and
   password and other attributes.

3 ColdFusion server returns the response.


Enable ColdFusion services
Using ColdFusion Administrator you can set the access permissions for a user or create multiple users with access to
different subsets of the available service. For example, you can allow an internal application access to all services, but
limit the access of another application to image and charting services.

To enable service access:

1 On the Administrator Services > IP Addresses page specify the URL addresses that can access the services. You can
   specify individual addresses, address ranges of the form 10-30, or * wild cards. You can specify IPv4 or IPv6
   addresses.

   For example, you can use the following address patterns:

   10.*.*.*

   10*.30-50.20-30

   10:10:10:10:10:10:10-FF:*

2 On the Administrator Service > User Manager page, specify the users that can access the services. You must specify
   the user name and password. Also specify the allowed services in the Exposed Services section of the page.


Access ColdFusion services using SOAP
You use SOAP to access ColdFusion services as document/style web services. To see the available service requests and
responses, view the service WSDL in the cfWebRoot\CFIDE\services directory. The service WSDL is available from
the following CFC files: chart.cfc, document.cfc, image.cfc, mail.cfc, pdf.cfc, pop.cfc.

For example, to see the image services, use a URL such as the following:

http://<CF server >:<port>/CFIDE/services/image.cfc?wsdl

All the methods and attributes exposed through services can be found in WSDL. Attribute explanation is same as the
attributes of corresponding ColdFusion TAGS/Functions.


Access ColdFusion services from a PHP server
To access ColdFusion services from PHP:

1 Install PHP

2 Use a library or framework, which helps convert WSDL to PHP classes, for example, "WSO2 Web services Frame
   Work for PHP".




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                               1124
Using Web Elements and External Objects




3 Invoke the class with required parameters.

   The following example shows a section of a PHP page that adds a border to an image:

   $input = new AddBorder();
   //Fill in the class fields of $input to match your business logic
   $input->serviceusername = "myuser";
   $input->servicepassword = "mypassword";
   $input->source = "http://myPHPSite/Images/image.jpg";
   $input->thickness = "30";
   $input->color = "blue";
   $input->bordertype = "";
   // call the operation
   $response = $proxy->AddBorder($input);

   In this code snippet,

   serviceusername is the user name set in the ColdFusion administrator with the permission to access the specific
   service being requested.

   servicepassword is the password set for the service user name.

   source is the source URL of the image in the PHP server.Other attributes are the same as the ImageAddborder()
   ColdFusion function.


Batch operation on Image:
The following code performs multiple operations on an uploaded image such as batchOperation() to crop an image
and add a border to it.

$input = new batchOperation();
//TODO: fill in the class fields of $input to match your business logic
//Crop
$element1 = new Element();
$element2 = new Element();
$element3 = new Element();
$element4 = new Element();
$elementArray1 = new ArrayOf_xsd_anyType();
$element1->key = 'x';
$element1->value = '10';
$element2->key = 'y';
$element2->value = '10';
$element3->key = 'width';
$element3->value = '200';
$element4->key = 'height';
$element4->value = '200';
$elementArray1->item[0] = $element1;
$elementArray1->item[1] = $element2;
$elementArray1->item[2] = $element3;
$elementArray1->item[3] = $element4;
$ElementcollectionCrop = new Elementcollection();
$ElementcollectionCrop->key = 'Crop';
$ElementcollectionCrop->value = $elementArray1;
//AddBorder
$element5 = new Element();
$element6 = new Element();




                                           

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 1125
Using Web Elements and External Objects




$element7 = new Element();
$elementArray2 = new ArrayOf_xsd_anyType();
$element5->key = 'thickness';$element5->value = '30';
$element6->key = 'color';
$element6->value = 'green';
$element7->key = 'bordertype';
$element7->value = '';
$elementArray2->item[0] = $element5;
$elementArray2->item[1] = $element6;
$elementArray2->item[2] = $element7;
$ElementcollectionAddBorder = new Elementcollection();
$ElementcollectionAddBorder->key = 'AddBorder';
$ElementcollectionAddBorder->value = $elementArray2;
$input->serviceusername = "myuser";
$input->servicepassword = "mypassword";
$input->source = "http:/<php server>:<port>/image.jpg";
$input->attributes =
array($ElementcollectionCrop,$ElementcollectionAddBorder);
// call the operation
$response = $proxy->batchOperation($input);



Access a ColdFusion service from .NET
Create a new Web service project in Visual Studio and add ColdFusion WSDL as a web reference to the project. Then
add a new item such a web form.


Example: Creating a PDF using .NET
Following is the sample .NET code for creating a PDF from the web site www.google.com.

Document.DocumentService objWebService = new
Document.DocumentService();
Document.Documentsection[] docsectionArray = { };
Document.Documentitem[] docitemArray = { };
string result = objWebService.generate("myuser", "mypassword", "pdf",
"", "", "", "", "yes", "", "", "", "", "","", "", "", "", "", "", "", "", "",
"", "", "", "http://www.google.com/", "", "", "", "", docsectionArray,
docitemArray);

In this code snippet:

Document is the web reference for document WSDL.

generate() is the method to generate the PDF from the HTML source.



Integrating J2EE and Java Elements in CFML
Applications

You can integrate J2EE elements, including JSP pages and servlets; JSP tags; and Java objects, including Enterprise
JavaBeans (EJBs); into your Adobe ColdFusion application.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 1126
Using Web Elements and External Objects




About ColdFusion, Java, and J2EE
ColdFusion is built on a J2EE-compliant Java technology platform. This structure lets ColdFusion applications take
advantage of, and integrate with, J2EE elements. ColdFusion pages can do any of the following:

· Include JavaScript and client-side Java applets on the page.

· Use JSP tags.

· Interoperate with JSP pages.

· Use Java servlets.

· Use Java objects, including JavaBeans and Enterprise JavaBeans.


About ColdFusion and client-side JavaScript and applets
ColdFusion pages, like HTML pages, can incorporate client-side JavaScript and Java applets. To use JavaScript, you
write the JavaScript code just as you do on any HTML page. ColdFusion ignores the JavaScript and sends it to the
client.

The cfapplet tag simplifies using Java client-side applets.


Use an applet on a ColdFusion page
1 Register the applet .class file in ColdFusion Administrator Java Applets Extensions page. (For information on
   registering applets, see the ColdFusion Administrator online Help.)

2 Use the cfapplet tag to call the applet. The appletSource attribute must be the Applet name assigned in the
   ColdFusion Administrator.

For example, ColdFusion includes a Copytext sample applet that copies text from one text box to another. The
ColdFusion Setup automatically registers the applet in the Administrator. To use this applet, incorporate it on your
page. For example:

<cfform action = "copytext.cfm">
     <cfapplet appletsource = "copytext" name = "copytext">
</cfform>


About ColdFusion and JSP
ColdFusion supports JSP tags and pages in the following ways:

· Interoperates with JSP pages: ColdFusion pages can include or forward to JSP pages, JSP pages can include or
   forward to ColdFusion pages, and both types of pages can share data in persistent scopes.

· Imports and uses JSP tag libraries: the cfimport tag imports JSP tag libraries and lets you use its tags.

ColdFusion pages are not JSP pages, however, and you cannot use most JSP syntax on ColdFusion pages. In particular,
you cannot use the following features on ColdFusion pages:

Include, Taglib, and Page directives: Instead, you use CFML import tag to import tag libraries, and the include (or
forward) method of the page context object returned by the ColdFusion GetPageContext function to include pages.
For more information, see "Using JSP tags and tag libraries" on page 1128 and "Interoperating with JSP pages and
servlets" on page 1129.

Expression, Declaration, and Scriptlet JSP scripting elements: Instead, you use CFML elements and expressions.

JSP comments: Instead, you use CFML comments. (ColdFusion ignores JSP comments and passes them to the
browser.)




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1127
Using Web Elements and External Objects




Standard JSP tags: Such as jsp:plugin, unless your J2EE server provides access to these tags in a JAR file. Instead, you
use ColdFusion tags and the PageContext object.


About ColdFusion and servlets
Some Java servlets are not exposed as JSP pages; instead they are Java programs. You can incorporate JSP servlets in
your ColdFusion application. For example, your enterprise could have an existing servlet that performs some business
logic. To use a servlet, the ColdFusion page specifies the servlet by using the ColdFusion GetPageContext function.

When you access the servlet with the GetPageContext function, the ColdFusion page shares the Request, Application,
and Session scopes with the servlet, so you can use these scopes for shared data.

ColdFusion pages can also access servlets by using the cfservlet tag, use the servlet URL in a form tag, or access an
SHTML page that uses a servlet tag.

Note: The cfservlet tag, which provides access to servlets on JRun servers, is deprecated since ColdFusion MX.


About ColdFusion and Java objects
Java objects include the following:

· Standard Java classes and methods that make up the J2EE API

· Custom-written Java objects, including the following:

    · Custom classes, including JavaBeans

    · Enterprise JavaBeans

ColdFusion pages use the cfobject tag to access Java objects.

ColdFusion searches for the objects in the following order:

1 The ColdFusion Java Dynamic Class Load directories:

2 Java archive (.jar) files in web_root/WEB-INF/lib

3 Class (.class) files in web_root/WEB-INF/classes

ColdFusion reloads classes from these directories, as described in the next section, "About class loading."

1 The classpath specified on the JVM and Java Settings page in the ColdFusion Administrator.

2 The default JVM classpath.


About class loading
ColdFusion dynamically loads classes that are either .class files in the web_root/WEB-INF/classes directory or in JAR
files in the web_root/WEB-INF/lib directory. ColdFusion checks the timestamp on the file when it creates an object
that is defined in either directory, even when the class is already in memory. If the file that contains the class is newer
than the class in memory, ColdFusion loads the class from that directory.

To use this feature, make sure that the Java implementation classes that you modify are not in the general JVM
classpath.

To disable automatic class loading of your classes, place the classes in the JVM classpath. Classes located on the JVM
classpath are loaded once per server lifetime. To reload these classes, stop and restart ColdFusion.


About GetPageContext and the PageContext object
Because ColdFusion pages are J2EE servlet pages, all ColdFusion pages have an underlying Java PageContext object.
CFML includes the GetPageContext function that you can then use in your ColdFusion page.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1128
Using Web Elements and External Objects




The PageContext object exposes several fields and methods that can be useful in J2EE integration. In particular, it
includes the include and forward methods that provide the equivalent of the corresponding standard JSP tags.

For more information on other features of the PageContext object, see Java documentation. You can find the Javadoc
description of this class at http://java.sun.com/j2ee/1.4/docs/api/javax/servlet/jsp/PageContext.html.


About CFML variables and Java variables
Because ColdFusion variables are case-independent and Java variables are case-dependent, be careful about variable
names. Use the following rules and guidelines when sharing data between ColdFusion and Java code, including JSP
pages and servlets.


Rules
· If you use mixed case variables, all variable names must be unique, independent of case. For example, you must not
   have two Java variables, MyVariable and MYVARIABLE. ColdFusion cannot distinguish between the two.

· If you share Request scope variables between a CFML page and a JSP page or servlet, all shared Request scope
   variable names must be all-lowercase in the JSP page or servlet. Mixed case or all-uppercase variables cause null
   pointer exceptions if CFML refers to these variables.

· If you share Application or Session scope variables between a CFML page and a JSP page or servlet and use a named
   ColdFusion application (the common usage), the variables on the JSP page or servlet are case-independent.

· If you share the Application or Session scope variables between a CFML page and a JSP page or servlet, and use an
   unnamed ColdFusion application, the variable names in the JSP page or servlet must be all lowercase.

· When you specify a class name in the cfobject tag or CreateObject function, the name must be case-correct.


Guidelines
· You can prevent problems by consistently using all-lowercase variable names.

· In your CFML, use the same case as you do in your Java or JSP. Doing so does not change how the application
   works, but does help prevent confusion.


Using JSP tags and tag libraries
You can use JSP tags from any JSP tag library. For example, you can use any of the custom tags in the open-source
Apache Jakarta Project Taglibs project tag libraries, located at http://jakarta.apache.org/taglibs/index.html. This
project consists of several individual JSP custom tag libraries for purposes ranging from JNDI access to generating
random text strings.


Using a JSP tag in a ColdFusion page
JSP pages use a standard set of tags, such as jsp:forward and jsp:include. You can also import custom JSP tag
libraries into a JSP application. You can use both the standard JSP tags and custom JSP tags in ColdFusion pages.


Standard JSP tags and ColdFusion
ColdFusion tags provide equivalent features to most standard JSP tags. For example, the cfapplet tag provides the same
service as the jsp:plugin tag, and cfobject tag lets you use JavaBeans, as does the jsp:usebean tag. Similarly, you do
not use the jsp:getproperty tag because ColdFusion automatically gets properties when you reference them.
Therefore, ColdFusion does not support the use of standard JSP tags directly.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1129
Using Web Elements and External Objects




However, two standard JSP tags provide functionality that is useful in ColdFusion pages: the forward and include
tags invoke JSP pages and Java servlets. The PageContext object described in About GetPageContext and the
PageContext object section has forward and include methods that provide the same operations. For more
information about using these methods, see "Accessing a JSP page or servlet from a ColdFusion page" on page 1130.


Using custom JSP tags in a ColdFusion page
Follow these steps to use a custom JSP tag on a ColdFusion page:


Use a custom tag
1 Place the tag library, consisting of the taglibname.jar file, and the taglibname.tld file, if one is supplied, in the
   web_root/WEB-INF/lib directory. The JSP custom tag library must be in this directory for you to use the cfimport
   tag.

2 Restart ColdFusion.

3 In the ColdFusion page that uses a JSP tag from the tag library, specify the tag library name in a cfimport tag; for
   example:

   <cfimport taglib="/WEB-INF/lib/random.jar" prefix="random">

   If the TLD file is not included in the JAR file, use the .tld extension in place of the .jar extension.

   Note: The cfimport tag must be on the page that uses the imported tag. You cannot place the cfimport tag in
   Application.cfm.

4 Use the custom tag using the form prefix:tagName; for example:

   <random:number id="myNum" range="000000-999999" />

Note: You cannot use the cfsavecontent tag to suppress output of a custom JSP tag.


Example: using the random tag library
The following example uses the random tag library from the Apache Jakarta Taglibs project and calls the number tag.
The number tag initializes a random number generator that uses a secure algorithm to generate a six-digit random
number. You get a new random number each time you reference the variable randPass.random.

 <cfimport taglib="/WEB-INF/lib/taglibs-random.jar" prefix="myrand">
<myrand:number id="randPass" range="000000-999999" algorithm="SHA1PRNG" provider="SUN" />
<cfset myPassword = randPass.random>
<cfoutput>
     Your password is #myPassword#<br>
</cfoutput>

For more information on the Jakarta random tag library and how to use its tags, see the documentation at the Apache
Jakarta Taglibs project website, http://jakarta.apache.org/taglibs/index.html. The Taglibs project includes many open
source custom tag libraries.


Interoperating with JSP pages and servlets
ColdFusion pages and JSP pages can interoperate in several ways:

· ColdFusion pages can invoke JSP pages and servlets.

· JSP pages can invoke ColdFusion pages.

· ColdFusion pages, JSP pages, and servlets can share data in three scopes.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1130
Using Web Elements and External Objects




Integrating JSP and servlets in a ColdFusion application
You can integrate JSP pages and servlets in your ColdFusion application. For example, you can write some application
pages in JSP and write others in CFML. ColdFusion pages can access JSP pages by using the JSP include and forward
methods to call the page. As with any web application, you can use href links in ColdFusion pages to open JSP pages.

The ability to use JSP lets you incorporate legacy JSP pages in your ColdFusion application, or conversely, use CFML
to expand an existing JSP application using ColdFusion pages.

If you have a JSP page that must call a ColdFusion page, you also use a jsp:forward or jsp:include tag to call the
ColdFusion page. For an example of calling a ColdFusion page from a JSP page, see "Calling a JSP page from a
ColdFusion page" on page 1132.


Accessing a JSP page or servlet from a ColdFusion page
To access a JSP page or servlet from a ColdFusion page, you use the GetPageContext function with the forward or the
include method. For example, to include a JSP "Hello World" page in your ColdFusion application, use the following line:

 GetPageContext().include("hello.jsp");

To pass parameters to the JSP page, include the parameters in the page URL.

For example, you could want to integrate an existing JSP customer response component into a new ColdFusion order
processing application. The order processing application provides the order number, total cost, and expected shipping
date, and the customer response component sends the response to the e-mail address on file for the particular
customer number. The ColdFusion application could use the following CFScript code to call the response JSP page:

 urlParams =
"UID=#order.uid#&cost=#order.total#&orderNo=#order.orderNo#&shipDate=#order.shipDateNo#"
getPageContext().forward(URLEncodedFormat("/responsegen/responsegen.jsp?#urlParams#"));

To access a servlet that exposes the same functionality, you use the same code, although the URL would change. For
example, to run a servlet called HelloWorldServlet, you place the servlet .java or .class file in the serverroot/WEB-
INF/classes directory and access the servlet with the URL /servlet/HelloWorldServlet.


Accessing ColdFusion application and session variables in JSP pages
ColdFusion runs as a J2EE application on the J2EE application server. The J2EE application ServletContext is a data
structure that stores objects as attributes. A ColdFusion Application scope is represented as an attribute named by the
Application scope name. The attribute contains the scope values as a hash table. Therefore, you access ColdFusion
Application scope variable in a JSP page or servlet using the following format:

 ((Map)application.getAttribute("CFApplicationName"))).get("appVarName")

Similarly, the ColdFusion Session scope is a structure within the J2EE session. Because ColdFusion identifies sessions
by the application name. the session structure is contained in an attribute of the J2EE session that the application name
identifies. Therefore, you access ColdFusion session variables as follows:

 ((Map)(session.getAttribute("CFApplicationName"))).get("sessionVarName")


Sharing data between ColdFusion pages and JSP pages or servlets
If an application includes ColdFusion pages and JSP pages or servlets, they can share data in the Request, Session and
Application scopes. The following table lists the ways that you can access JSP pages with which you want to share the
scope data:




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1131
Using Web Elements and External Objects




 Scope                   Can share data using

 Request                 forward, include

                         Note: Shared Request scope variable names in the JSP page or servlet must be all-lowercase.

 Session                 href, cfhttp, forward, include

 Application             href, cfhttp, forward, include


Note: When you share data between ColdFusion pages and JSP pages, be careful about data type conversion issues. For
more information, see "Java and ColdFusion data type conversions" on page 1139.

To share session variables, specify J2EE session management in the ColdFusion Administrator. For more information
on configuring and using J2EE Session scope management, see "ColdFusion and J2EE session management" on
page 312.

For example, you could put the customer order structure used in the previous example in the Session scope. Then, you
would not have to pass the order values as a set of parameters. Instead, the JSP pages could access the Session scope
variables directly, and the ColdFusion page would only require a line like the following to call the JSP page:

getPageContext().forward(URLEncodedFormat("/responsegen/responsegen.jsp"));

For examples of using the Request, Session, and Application scopes to share data between ColdFusion pages and JSP
pages, including samples of the appropriate JSP code, see "Examples: using JSP with CFML" on page 1132.

Note: When running in the server configuration, ColdFusion also shares the Form scope when calling a JSP or servlet. In
the J2EE configuration, however, sharing the Form scope is dependent on the J2EE application server. For example, JRun
shares the Form scope, IBM WebSphere does not. ColdFusion always shares the Request, Session, and Application scopes.


Unnamed ColdFusion Application and Session scopes
If you do not specify an application name in the This.name variable in the Application.cfc initialization code or by
using the ColdFusion cfapplication tag, the application is unnamed, and the Application scope corresponds to the
ColdFusion J2EE servlet context. ColdFusion, therefore, supports only a single unnamed application. If multiple
cfapplication tags and Application.cfc files do not specify an application name, all pages in these applications share
the servlet context as their Application scope.

All sessions of unnamed applications correspond directly to the session object of the J2EE application server. (If you do
not use J2EE session variables, ColdFusion ensures that the J2EE session lasts at least as long as the session time-out.)

You access an Application scope variable from a ColdFusion unnamed application in a JSP page using the following
format:

application.getAttribute("applicationVariableName")

You access Session scope variables in a ColdFusion unnamed application as follows:

session.getAttribute("sessionVariableName")

Note: When you use application and session variables for the unnamed ColdFusion application in JSP pages and servlets,
the variable names must be case-correct. The characters in the variable name must have the same case as you used when
you created the variable in ColdFusion. You do not have to use case-correct application and session variable names for
named ColdFusion applications.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                              1132
Using Web Elements and External Objects




Examples: using JSP with CFML
The following simple examples show how you can integrate JSP pages, servlets, and ColdFusion pages. They also show
how you can use the Request, Application, and Session scopes to share data between ColdFusion pages, JSP pages, and
servlets.


Calling a JSP page from a ColdFusion page
The following page sets Request, Session, and application variables and calls a JSP page, passing it a name parameter:

<cfapplication name="myApp" sessionmanagement="yes">
<cfscript>
Request.myVariable = "This";
Session.myVariable = "is a";
Application.myVariable = "test.";
GetPageContext().include("hello.jsp?name=Bobby");
</cfscript>


Reviewing the code
The following table describes the CFML code and its function:


Code                                            Description

<cfapplication name="myApp"                     Specifies the application name as myApp and enables session management. In most
sessionmanagement="yes">                        applications, this tag is in the Application.cfm page.

<cfscript>                                      Sets ColdFusion Request, Session, and Application, scope variables. Uses the same
Request.myVariable = "This";                    name, myVariable, for each variable.
Session.myVariable = "is a";
Application.myVariable = "test.";

GetPageContext().include("hello.jsp?name        Uses the GetPageContext function to get the current servlet page context for the
=Bobby");                                       ColdFusion page. Uses the include method of the page context object to call the
</cfscript>
                                                hello.jsp page. Passes the name parameter in the URL.


The ColdFusion page calls the hello.jsp page. It displays the name parameter in a header and the three variables in the
remainder of the body.

<%@page import="java.util.*" %>
<h2>Hello <%= request.getParameter("name")%>!</h2>


<br>Request.myVariable: <%= request.getAttribute("myVariable")%>
<br>session.myVariable: <%= ((Map)(session.getAttribute("myApp"))).get("myVariable")%>
<br>Application.myVariable: <%=
((Map)(application.getAttribute("myApp"))).get("myVariable")%>

The following table describes the JSP code and its function (line breaks added for clarity):




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                 1133
Using Web Elements and External Objects




Code                                        Description

<%@page import="java.util.*" %>             Imports the java.util package. This package contains methods required in the JSP page.

<h2>Hello <%= request.getParameter          Displays the name passed as a URL parameter from the ColdFusion page. The parameter
("name")%>!</h2>                            name is case sensitive,

                                            Note: The getParameter request method cannot get all ColdFusion page request
                                            parameter values on some application servers. For example, on IBM WebSphere, you
                                            cannot use getParameter to get form fields.

<br>request.myVariable: <%= request.        Uses the getAttribute method of the JSP request object to displays the value of the
getAttribute("myvariable")%>                Request scope variable myVariable.

                                            The JSP page must use all lowercase characters to reference all request scope variables
                                            that it shares with CFML pages. You can use any case on the CFML page, but if you use
                                            mixed case or all uppercase on the JSP page, the variable is not set from the ColdFusion
                                            page.

<br>session.myVariable: <%=                 Uses the getAttribute method of the JSP session object to get the myApp object (the
((Map)(session.getAttribute("myApp"))       Application scope). Casts this object to a Java Map object and uses the get method to
).get("myVariable")%>
                                            obtain the myVariable value for display.

                                            CFML pages and JSP pages share Session variables independent of the variable name
                                            case. The variable on the JSP page can have any case mixture and still receive the value
                                            from the ColdFusion page. For example, instead of myVariable, you could use
                                            MYVARIABLE or myvariable on this line.

<br>Application.myVariable:                 Uses the getAttribute method of the JSP myApp application object to obtain the
<%=((Map)(application.getAttribute("m       value of myVariable in the Application scope.
yApp"))).get("myVariable")%>
                                            CFML pages and JSP pages share Application variables independent of the variable
                                            name case. The variable on the JSP page can have any case mixture and still receive the
                                            value from the ColdFusion page. For example, instead of myVariable, you could use
                                            MYVARIABLE or myvariable on this line.



Calling a ColdFusion page from a JSP page
The following JSP page sets Request, Session, and application variables and calls a ColdFusion page, passing it a name
parameter:

<%@page import="java.util.*" %>


<% request.setAttribute("myvariable", "This");%>
<% ((Map)session.getAttribute("myApp")).put("myVariable", "is a");%>
<% ((Map)application.getAttribute("myApp")).put("myVariable", "test.");%>


<jsp:include page="hello.cfm">
     <jsp:param name="name" value="Robert" />
</jsp:include>


Reviewing the code
The following table describes the JSP code and its function:




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                     1134
Using Web Elements and External Objects




 Code                                             Description

 <%@page import="java.util.*" %>                  Imports the java.util package. This package contains methods required in the JSP
                                                  page.

 <% request.setAttribute("myvariable",            Uses the setAttribute method of the JSP request object to set the value of the
 "This");%>                                       Request scope variable myVariable.

                                                  The JSP page must use all lowercase characters to refer to all request scope
                                                  variables that it shares with CFML pages. You can use any case on the CFML page,
                                                  but if you use mixed case to all uppercase on the JSP page, the JSP page does not
                                                  share it with the ColdFusion page.

 <%                                               Uses the getAttribute method of the JSP session object to get the myApp
 ((Map)session.getAttribute("myApp")).put(        object (the Application scope). Casts this object to a Java Map object and uses the
 "myVariable", "is a");%>
                                                  set method to set the myVariable value.

                                                  CFML pages and JSP pages share Session variables independent of the variable
                                                  name case. The variable on the JSP page can have any case mixture and still share
                                                  the value with the ColdFusion page. For example, instead of myVariable, you could
                                                  use MYVARIABLE or myvariable on this line.

 <%                                               Uses the getAttribute method of the JSP application object to get myApp
 ((Map)application.getAttribute("myApp")).        object (the Application scope) and casts it to a Map object. It then sets the value of
 put("myVariable","test.");%>
                                                  myVariable in the myApp application scope object.

                                                  CFML pages and JSP pages share Application variables independent of the variable
                                                  name case. The variable on the JSP page can have any case mixture and still share
                                                  the value with the ColdFusion page. For example, instead of myVariable, you could
                                                  use MYVARIABLE or myvariable on this line.

 <jsp:include page="hello.cfm">                   Sets the name parameter to Robert and calls the ColdFusion page hello.cfm.
 <jsp:param name="name" value="Robert"/>
 </jsp:include>


The JSP page calls the following hello.cfm page. It displays the Name parameter in a heading and the three variables in
the remainder of the body.

<cfapplication name="myApp" sessionmanagement="yes">
<cfoutput>
<h2>Hello #URL.name#!</h2>
Request.myVariable: #Request.myVariable#<br>
Session.myVariable: #Session.myVariable#<br>
Application.myVariable: #Application.myVariable#<br>


</cfoutput>

The following table describes the CFML code and its function:


 Code                                              Description

 <cfapplication name="myApp"                       Specifies the application name as myApp and enables session management. In
 sessionmanagement="yes">                          most applications, this tag is in the Application.cfm page.

 <cfoutput><h2>Hello #URL.name#!</h2>              Displays the name passed using the jsp:param tag on the JSP page. The
                                                   parameter name is not case sensitive.

 Request.myVariable:                               Displays the Request.myVariable, Session. myVariable, and
 #Request.myVariable#<br>                          Application.myVariable values. All variable names on CFML pages are case
 Session.myVariable:
                                                   independent.
 #Session.myVariable#<br>
 Application.myVariable:
 #Application.myVariable#<br>
 </cfoutput>




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1135
Using Web Elements and External Objects




Using Java objects
You use the cfobject tag to create an instance of a Java object. You use other ColdFusion tags, such as cfset and
cfoutput, or CFScript to invoke properties (attributes), and methods (operations) on the object.

Method arguments and return values can be any valid Java type; for example, simple arrays and objects. ColdFusion
does the appropriate conversions when strings are passed as arguments, but not when they are received as return
values. For more information on type conversion issues, see "Java and ColdFusion data type conversions" on
page 1139.

The examples in this discussion assume that the name attribute in the cfobject tag specified the value obj, and that
the object has a property called Property, and methods called Method1, Method2, and Method3.

Note: The cfdump tag displays the public methods and data of an object.


Using basic object techniques
You can use ColdFusion to invoke Java objects and access object methods and properties.


Invoking objects
The cfobject tag makes Java objects available in ColdFusion. It can access any Java class that is available on the JVM
classpath or in either of the following locations:

· In a Java archive (.jar) file in web_root/WEB-INF/lib

· In a class (.class) file in web_root/WEB-INF/classes

For example:

 <cfobject type="Java" class="MyClass" name="myObj">

Although the cfobject tag loads the class, it does not create an instance object. Only static methods and fields are
accessible immediately after the call to cfobject.

If you call a public non-static method on the object without first calling the init method, ColdFusion makes an
implicit call to the default constructor.

To call an object constructor explicitly, use the special ColdFusion init method with the appropriate arguments after
you use the cfobject tag; for example:

 <cfobject type="Java" class="MyClass" name="myObj">
<cfset ret=myObj.init(arg1, arg2)>

Note: The init method is not a method of the object, but a ColdFusion identifier that calls the new function on the class
constructor. So, if a Java object has an init method, a name conflict exists and you cannot call the object init method.

To have persistent access to an object, use the init function, because it returns a reference to an instance of the object,
and cfobject does not.

An object created using cfobject or returned by other objects is implicitly released at the end of the ColdFusion page
execution.


Using properties
Use the following coding syntax to access properties if the object does either of the following actions:

· Exposes the properties as public properties.

· Does not make the properties public, but is a JavaBean that provides public getter and setter methods of the form
   getPropertyName() and setPropertyName(value). For more information, see "Calling JavaBean get and set
   methods" on page 1136.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1136
Using Web Elements and External Objects




· To set a property: <cfset obj.property = "somevalue">

· To get a property: <cfset value = obj.property>

Note: ColdFusion does not require consistently capitalized property and method names. However, it is good
programming practice to use the same case in ColdFusion as you do in Java to ensure consistency.


Calling methods
Object methods usually take zero or more arguments. Some methods return values, while others might not. Use the
following techniques to call methods:

1 If the method has no arguments, follow the method name with empty parentheses, as in the following cfset tag:

    <cfset retVal = obj.Method1()>

2 If the method has one or more arguments, place the arguments in parentheses, separated by commas, as in the
   following example, which has one integer argument and one string argument:

    <cfset x = 23>
    <cfset retVal = obj.Method1(x, "a string literal")>

Note: When you invoke a Java method, the type of the data being used is important. For more information see "Java and
ColdFusion data type conversions" on page 1139.


Calling JavaBean get and set methods
ColdFusion can automatically invoke getPropertyName() and setPropertyName(value) methods if a Java class
conforms to the JavaBeans pattern. As a result, you can set or get the property by referencing it directly, without having
to explicitly invoke a method.

For example, if the myFishTank class is a JavaBean, the following code returns the results of calling the getTotalFish()
method on the myFish object:

 <cfoutput>
     There are currently #myFish.TotalFish# fish in the tank.
</cfoutput>

The following example adds one guppy to a myFish object by implicitly calling the setGuppyCount(int number)
method:

 <cfset myFish.GuppyCount = myFish.GuppyCount + 1>

Note: You can use the direct reference method to get or set values in some classes that have getProperty and setProperty
methods but do not conform fully to the JavaBean pattern. However, you cannot use this technique for all classes that
have getProperty and setProperty methods. For example, you cannot directly reference any of the following standard Java
classes, or classes derived from them: Date, Boolean, Short, Integer, Long, Float, Double, Char, Byte, String, List, Array.


Calling nested objects
ColdFusion supports nested (scoped) object calls. For example, if an object method returns another object and you
invoke a property or method on that object, you can use the following syntax:

 <cfset prop = myObj.X.Property>.

Similarly, you can use code such as the following CFScript line:

GetPageContext().include("hello.jsp?name=Bobby");

In this code, the ColdFusion GetPageContext function returns a Java PageContext object, and the line invokes the
include method of the PageContext object.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1137
Using Web Elements and External Objects




Creating and using a simple Java class
Java is a strongly typed language, unlike ColdFusion, which does not enforce data types. As a result, some subtle
considerations exist when calling Java methods.


The Employee class
The Employee class has four data members: FirstName and LastName are public, and Salary and JobGrade are private.
The Employee class has three overloaded constructors and an overloaded SetJobGrade method.

Save the following Java source code in the file Employee.java, compile it, and place the resulting Employee.class file in
a directory that is specified in the classpath:

public class Employee {


public String FirstName;
public String LastName;
private float Salary;
private int JobGrade;


public Employee() {
     FirstName ="";
     LastName ="";
     Salary = 0.0f;
     JobGrade = 0;
}


public Employee(String First, String Last) {
     FirstName = First;
     LastName = Last;
     Salary = 0.0f;
     JobGrade = 0;
}


public Employee(String First, String Last, float salary, int grade) {
     FirstName = First;
     LastName = Last;
     Salary = salary;
     JobGrade = grade;
}


public void SetSalary(float Dollars) {
     Salary = Dollars;
}


public float GetSalary() {
     return Salary;
}


public void SetJobGrade(int grade) {




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                  1138
Using Web Elements and External Objects




    JobGrade = grade;
}


public void SetJobGrade(String Grade) {
    if (Grade.equals("CEO")) {
          JobGrade = 3;
    }
    else if (Grade.equals("MANAGER")) {
          JobGrade = 2;
    }
    else if (Grade.equals("DEVELOPER")) {
          JobGrade = 1;
    }
}


public int GetJobGrade() {
    return JobGrade;
}


}


A CFML page that uses the Employee class
Save the following text as JEmployee.cfm:

<html>
<body>
<cfobject action="create" type="java" class="Employee" name="emp">
<!--- <cfset emp.init()> --->
<cfset emp.firstname="john">
<cfset emp.lastname="doe">
<cfset firstname=emp.firstname>
<cfset lastname=emp.lastname>
</body>


<cfoutput>
    Employee name is #firstname# #lastname#
</cfoutput>
</html>

When you view the page in your browser, you get the following output:

Employee name is john doe


Reviewing the code
The following table describes the CFML code and its function:


Code                                         Description

<cfobject action=create type=java            Loads the Employee Java class and gives it an object name of emp.
class=Employee name=emp>

<!--- <cfset emp.init()> --->                Does not call a constructor. ColdFusion calls the default constructor when it first uses
                                             the class; in this case, when it processes the next line.




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         1139
Using Web Elements and External Objects




 Code                                            Description

 <cfset emp.firstname="john">                    Sets the public fields in the emp object to your values.
 <cfset emp.lastname="doe">

 <cfset firstname=emp.firstname>                 Gets the field values back from emp object.
 <cfset lastname=emp.lastname>

 <cfoutput>                                      Displays the retrieved values.
 Employee name is #firstname# #lastname#
 </cfoutput>



Java considerations
The following points are important when you write a ColdFusion page that uses a Java class object:

· The Java class name is case sensitive. Ensure that the Java code and the CFML code use Employee as the class name.

· Although Java method and field names are case sensitive, ColdFusion variables are not case sensitive, and
   ColdFusion does any necessary case conversions. As a result, the sample code works even though the CFML uses
   emp.firstname and emp.lastname; the Java source code uses FirstName and LastName for these fields.

· If you do not call the constructor (or, as in this example, comment it out), ColdFusion automatically runs the
   default constructor when it first uses the class.


Using an alternate constructor
The following ColdFusion page explicitly calls one of the alternate constructors for the Employee object:

 <html>
<body>


<cfobject action="create" type="java" class="Employee" name="emp">
<cfset emp.init("John", "Doe", 100000.00, 10)>
<cfset firstname=emp.firstname>
<cfset lastname=emp.lastname>
<cfset salary=emp.GetSalary()>
<cfset grade=emp.GetJobGrade()>


<cfoutput>
     Employee name is #firstname# #lastname#<br>
     Employee salary #DollarFormat(Salary)#<br>
     Employee Job Grade #grade#
</cfoutput>


</body>
</html>

In this example, the constructor takes four arguments: the first two are strings, the third is a float, and the fourth is an
integer.


Java and ColdFusion data type conversions
ColdFusion does not use explicit types for variables, while Java is strongly typed. However, ColdFusion data does use
underlying Java types to represent data.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                         1140
Using Web Elements and External Objects




Under most situations, when the method names are not ambiguous, ColdFusion can determine the data types that a
Java object requires, and often it can convert ColdFusion data to the required types. For example, ColdFusion text
strings are implicitly converted to the Java String type. Similarly, if a Java object contains a doIt method that expects a
parameter of type int, and CFML is issuing a doIt call with a CFML variable x that contains an integer value,
ColdFusion converts the variable x to Java int type. However, ambiguous situations can result from Java method
overloading, where a class has multiple implementations of the same method that differ only in their parameter types.


Default data type conversion
Whenever possible, ColdFusion automatically matches Java types to ColdFusion types.

The following table lists how ColdFusion converts ColdFusion data values to Java data types when passing arguments.
The left column represents the underlying ColdFusion representation of its data. The right column indicates the Java
data types into which ColdFusion can automatically convert the data:


CFML                                 Java

Integer                              short, int, long (short and int can result in a loss of precision).

Real number                          float double (float can result in a loss of precision.

Boolean                              boolean

Date-time                            java.util.Date

String, including lists              String

                                     short, int, long, float, double, java.util.Date, when a CFML string represents a number or date.

                                     boolean, for strings with the value Yes, No, True, and False (case-insensitive).

Array                                java.util.Vector (ColdFusion Arrays are internally represented using an instance of a java.util.Vector
                                     object.)

                                     ColdFusion can also map a CFML array to any of the following when the CFML array contains
                                     consistent data of a type that can be converted to the data type of the Java arr: byte[], char[],
                                     boolean[], int[], long[], float[], double[], String[], or Object[]. When a CFML array contains data of
                                     different of types, the conversion to a simple array type could fail.

Structure                            java.util.Map

Query object                         java.util.Map

XML document object                  Not supported.

ColdFusion component                 Not applicable.


The following table lists how ColdFusion converts data returned by Java methods to ColdFusion data types:


Java                                 CFML

boolean/Boolean                      Boolean

byte/Byte                            String

char/Char                            String

short/Short                          Integer

int/Integer                          Integer

long/Long                            Integer

float/Float                          Real Number




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1141
Using Web Elements and External Objects




 Java                                CFML

 double/Double                       Real Number

 String                              String

 java.util.Date                      Date-time

 java.util.List                      Comma-delimited list

 byte[]                              Array

 char[]                              Array

 boolean[]                           Array

 String[]                            Array

 java.util.Vector                    Array

 java.util.Map                       Structure



Resolving ambiguous data types with the JavaCast function
You can overload Java methods so a class can have several identically named methods. At runtime, the JVM resolves
the specific method to use based on the parameters passed in the call and their types.

In the section The Employee class, the Employee class has two implementations for the SetJobGrade method. One
method takes a string variable, the other an integer. If you write code such as the following, which implementation to
use is ambiguous:

<cfset emp.SetJobGrade("1")>

The "1" could be interpreted as a string or as a number, so no way exists to know which method implementation to
use. When ColdFusion encounters such an ambiguity, it throws a user exception.

The ColdFusion JavaCast function helps you resolve such issues by specifying the Java type of a variable, as in the
following line:

<cfset emp.SetJobGrade(JavaCast("int", "1"))>

The JavaCast function takes two parameters: a string representing the Java data type, and the variable whose type you
are setting. You can specify the following Java data types: boolean, int, long, float, double, and String.

For more information about the JavaCast function, see the CFML Reference.


Handling Java exceptions
You handle Java exceptions just as you handle standard ColdFusion exceptions, with the cftry and cfcatch tags. You
specify the name of the exception class in the cfcatch tag that handles the exception. For example, if a Java object
throws an exception named myException, you specify myException in the cfcatch tag.

Note: To catch any exception generated by a Java object, specify java.lang.Exception for the cfcatchtype attribute. To
catch any Throwable errors, specify java.lang.Throwable in the cfcatch tag type attribute.

For more information on exception handling in ColdFusion, see "Handling Errors" on page 275.


Example: exception-throwing class
The following Java code defines the testException class that throws a sample exception. It also defines a myException
class that extends the Java built-in Exception class and includes a method for getting an error message.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1142
Using Web Elements and External Objects




The myException class has the following code. It throws an exception with a message that is passed to it, or if no
argument is passed, it throws a canned exception.

//class myException
public class myException extends Exception
{
     public myException(String msg) {
          super(msg);
     }
     public myException() {
          super("Error Message from myException");
     }
}

The testException class contains one method, doException, which throws a myException error with an error message,
as follows:

public class testException {
     public testException ()
     {
     }
     public void doException() throws myException {
          throw new myException("Throwing an exception from testException class");
     }
}


Example: CFML Java exception handling code
The following CFML code calls the testException class doException method. The cfcatch block handles the resulting
exception.

<cfobject action=create type=java class=testException name=Obj>
<cftry>
     <cfset Obj.doException() >
     <cfcatch type="myException">
          <cfoutput>
               <br>The exception message is: #cfcatch.Message#<br>
          </cfoutput>
     </cfcatch>
</cftry>


Examples: using Java with CFML
The following examples show several examples of using Java objects in CFML. They include examples of using a
custom Java class, a standard Java API class in a user-defined function, a JavaBean, and an Enterprise JavaBean (EJB).


Using a Java API in a UDF
The following example of a user-defined function (UDF) is functionally identical to the GetHostAddress function
from the NetLib library of UDFs from the Common Function Library Project, www.cflib.org. It uses the InetAddress
class from the standard Java 2 java.net package to get the Internet address of a specified host:




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1143
Using Web Elements and External Objects




 <cfscript>
function GetHostAddress(host) {
     // Define the function local variables.
     var iaddrClass="";
     var address="";
     // Initialize the Java class.
     iaddrClass=CreateObject("java", "java.net.InetAddress");
     // Get the address object.
     address=iaddrClass.getByName(host);
     // Return the address
     return address.getHostAddress();
}
</cfscript>
<cfoutput>#gethostaddress("adobe.com")#</cfoutput>


Using an EJB
ColdFusion can use EJBs that JRun 4.0 servers provide. The JRun server jrun.jar file must have the same version as the
jrun.jar file in ColdFusion.

To call an EJB, you use cfobject to create and call the appropriate objects. Before you use an EJB, do the following:

1 Have a properly deployed EJB running on a J2EE server. The bean must be registered with the JNDI server.

2 Have the following information:

    · Name of the EJB server

    · Port number of the JNDI naming service on the EJB server

    · Name of the EJB, as registered with the naming service

3 Install the EJB home and component interface compiled classes on your ColdFusion web server, either as class files
   in the web_root/WEB-INF/classes directory or packaged in a JAR file the web_root/WEB-INF/lib directory.

Note: To use an EJB served by a JRUN server, your ColdFusion installation and the JRun server that hosts the EJB must
have the same version of the jrun.jar file (located in cf_root\runtime\lib directory in ColdFusion).

Although the specific steps for using an EJB depend on the EJB server and on the EJB itself, they generally correspond
to the following order.

1 Use the cfobject tag to create an object of the JNDI naming context class (javax.naming.Context). You use fields
   from this class to define the information that you use to locate the EJB. Because you only use fields, you do not
   initialize the object.

2 Use the cfobject tag to create a java.util.Properties class object to contain the context object properties.

3 Call the init method to initialize the Properties object.

4 Set the Properties object to contain the properties that are required to create an initial JNDI naming context. These
   properties include the INITIAL_CONTEXT_FACTORY and PROVIDER_URL properties. You could also need to
   provide SECURITY_PRINCIPAL and SECURITY_CREDENTIALS values required for secure access to the
   naming context. For more information on these properties, see the JNDI documentation.

5 Use the cfobject tag to create the JNDI InitialContext (javax.naming. InitialContext) object.

6 Call the init method for the InitialContext object with the Properties object values to initialize the object.

7 Call the lookup method of the InitialContextext object to get a reference to the home interface for the bean that
   you want. Specify the JNDI name of the bean as the lookup argument.

8 Call the create method of the bean home object to create an instance of the bean. If you are using Entity beans,
   you typically use a finder method instead. A finder method locates one or more existing entity beans.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                1144
Using Web Elements and External Objects




9 Now you can use the bean methods as required by your application.

10 When finished, call the close method of the context object to close the object.

The following code shows this process using a simple Java Entity bean on a JRun 4.0 server. It calls the getMessage
method of the bean to obtain a message.

<html>
<head>
    <title>cfobject Test</title>
</head>


<body>
<H1>cfobject Test</H1>
<!--- Create the Context object to get at the static fields. --->
<CFOBJECT
    action=create
    name=ctx
    type="JAVA"
    class="javax.naming.Context">


<!--- Create the Properties object and call an explicit constructor--->
<CFOBJECT
    action=create
    name=prop
    type="JAVA"
    class="java.util.Properties">


<!--- Call the init method (provided by cfobject)
          to invoke the Properties object constructor. --->
<cfset prop.init()>


<!--- Specify the properties These are required for a remote server only --->
<cfset prop.put(ctx.INITIAL_CONTEXT_FACTORY, "jrun.naming.JRunContextFactory")>
<cfset prop.put(ctx.PROVIDER_URL, "localhost:2908")>
<!--- <cfset prop.put(ctx.SECURITY_PRINCIPAL, "admin")>
          <cfset prop.put(ctx.SECURITY_CREDENTIALS, "admin")>
    --->
<!--- Create the InitialContext --->
<CFOBJECT
    action=create
    name=initContext
    type="JAVA"
    class="javax.naming.InitialContext">


<!--- Call the init method (provided through cfobject)
          to pass the properties to the InitialContext constructor. --->
<cfset initContext.init(prop)>




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                1145
Using Web Elements and External Objects




<!--- Get reference to home object. --->
<cfset home = initContext.lookup("SimpleBean")>


<!--- Create new instance of entity bean.
           (hard-wired account number). Alternatively,
           you would use a find method to locate an existing entity bean. --->
<cfset mySimple = home.create()>


<!--- Call a method in the entity bean. --->
<cfset myMessage = mySimple.getMessage()>


<cfoutput>
    #myMessage#<br>
</cfoutput>


<!--- Close the context. --->
<cfset initContext.close()>


</body>
</html>


Using a custom Java class
The following code provides a more complex custom class than in the example "Creating and using a simple Java class"
on page 1137. The Example class manipulates integer, float, array, Boolean, and Example object types.


The Example class
The following Java code defines the Example class. The Java class Example has one public integer member,
mPublicInt. Its constructor initializes mPublicInt to 0 or an integer argument. The class has the following public
methods:


Method                                Description

ReverseString                         Reverses the order of a string.

ReverseStringArray                    Reverses the order of elements in an array of strings.

Add                                   Overloaded: Adds and returns two integers or floats or adds the mPublicInt members of two
                                      Example class objects and returns an Example class object.

SumArray                              Returns the sum of the elements in an integer array.

SumObjArray                           Adds the values of the mPublicInt members of an array of Example class objects and returns an
                                      Example class object.

ReverseArray                          Reverses the order of an array of integers.

Flip                                  Switches a Boolean value.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                               1146
Using Web Elements and External Objects




public class Example {
    public int mPublicInt;


    public Example() {
          mPublicInt = 0;
    }


    public Example(int IntVal) {
          mPublicInt = IntVal;
    }


    public String ReverseString(String s) {
          StringBuffer buffer = new StringBuffer(s);
          return new String(buffer.reverse());
    }


    public String[] ReverseStringArray(String [] arr) {
          String[] ret = new String[arr.length];
          for (int i=0; i < arr.length; i++) {
              ret[arr.length-i-1]=arr[i];
          }
          return ret;
    }


    public int Add(int a, int b) {
          return (a+b);
    }


    public float Add(float a, float b) {
          return (a+b);
    }


    public Example Add(Example a, Example b) {
          return new Example(a.mPublicInt + b.mPublicInt);
    }


    static public int SumArray(int[] arr) {
          int sum=0;
          for (int i=0; i < arr.length; i++) {
              sum += arr[i];
          }
          return sum;
    }


    static public Example SumObjArray(Example[] arr) {




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                            1147
Using Web Elements and External Objects




          Example sum= new Example();
          for (int i=0; i < arr.length; i++) {
              sum.mPublicInt += arr[i].mPublicInt;
          }
          return sum;
    }


    static public int[] ReverseArray(int[] arr) {
          int[] ret = new int[arr.length];
          for (int i=0; i < arr.length; i++) {
              ret[arr.length-i-1]=arr[i];
          }
          return ret;
    }


    static public boolean Flip(boolean val) {
          System.out.println("calling flipboolean");
          return val?false:true;
    }
}


The useExample ColdFusion page
The following useExample.cfm page uses the Example class to manipulate numbers, strings, Booleans, and Example
objects. The CFML JavaCast function ensures that CFML variables convert into the appropriate Java data types.

<html>
<head>
    <title>CFOBJECT and Java Example</title>
</head>
<body>


<!--- Create a reference to an Example object --->
<cfobject action=create type=java class=Example name=obj>
<!--- Create the object and initialize its public member to 5 --->
<cfset x=obj.init(JavaCast("int",5))>


<!--- Create an array and populate it with string values,
          then use the Java object to reverse them. --->
<cfset myarray=ArrayNew(1)>
<cfset myarray[1]="First">
<cfset myarray[2]="Second">
<cfset myarray[3]="Third">
<cfset ra=obj.ReverseStringArray(myarray)>


<!--- Display the results --->
<cfoutput>
    <br>
    original array element 1: #myarray[1]#<br>
    original array element 2: #myarray[2]#<br>
    original array element 3: #myarray[3]#<br>
    after reverseelement 1: #ra[1]#<br>
    after reverseelement 2: #ra[2]#<br>
    after reverseelement 3: #ra[3]#<br>
    <br>
</cfoutput>




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                       1148
Using Web Elements and External Objects




<!--- Use the Java object to flip a Boolean value, reverse a string,
          add two integers, and add two float numbers --->
<cfset c=obj.Flip(true)>
<cfset StringVal=obj.ReverseString("This is a test")>
<cfset IntVal=obj.Add(JavaCast("int",20),JavaCast("int",30))>
<cfset FloatVal=obj.Add(JavaCast("float",2.56),JavaCast("float",3.51))>


<!--- Display the results --->
<cfoutput>
    <br>
    StringVal: #StringVal#<br>
    IntVal: #IntVal#<br>
    FloatVal: #FloatVal#<br>
    <br>
</cfoutput>


<!--- Create a two-element array, sum its values,
          and reverse its elements --->
<cfset intarray=ArrayNew(1)>
<cfset intarray[1]=1>
<cfset intarray[2]=2>
<cfset IntVal=obj.sumarray(intarray)>
<cfset reversedarray=obj.ReverseArray(intarray)>


<!--- Display the results --->
<cfoutput>
    <br>
    IntVal1 :#IntVal#<br>
    array1: #reversedarray[1]#<br>
    array2: #reversedarray[2]#<br>
    <br>
</cfoutput><br>


<!--- Create a ColdFusion array containing two Example objects.
          Use the SumObjArray method to add the objects in the array
          Get the public member of the resulting object--->
<cfset oa=ArrayNew(1)>
<cfobject action=create type=java class=Example name=obj1>
<cfset VOID=obj1.init(JavaCast("int",5))>
<cfobject action=create type=java class=Example name=obj2>
<cfset VOID=obj2.init(JavaCast("int",10))>
<cfset oa[1] = obj1>
<cfset oa[2] = obj2>
<cfset result = obj.SumObjArray(oa)>
<cfset intval = result.mPublicInt>


<!--- Display the results --->
<cfoutput>
    <br>
    intval1: #intval#<br>
    <br>
</cfoutput><br>
</body>
</html>




                                          

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1149
Using Web Elements and External Objects




Using Microsoft .NET Assemblies

You can use Adobe ColdFusion to call local or remote Microsoft .NET assembly class methods and access assembly
fields. This documentation describes how to configure and run the ColdFusion .NET extension software and how to
access and use .NET classes in your ColdFusion code. For information about .NET technology or how to develop .NET
applications, see Microsoft .NET documentation.


About ColdFusion and .NET
ColdFusion lets you access and use Microsoft .NET assembly classes as CFML objects. CFML applications can use
.NET assemblies in the following ways:

· Directly access and control Microsoft products, such as Word, Excel, or PowerPoint.

· Use existing .NET components.

· Use .NET assemblies that you create to leverage features that are difficult to use or not available in ColdFusion or
    Java. (Because ColdFusion is a J2EE application, if you cannot code a feature in CFML, it is more efficient to create
    it in Java than to use .NET.)

The .NET classes that your application uses do not have to be local; your ColdFusion application can access .NET
components that are located on remote systems, even systems that are located outside your firewall. Also, the
ColdFusion system does not require .NET run-time software installed to use remote .NET components, so ColdFusion
running on a UNIX, Linux, Solaris, or OS-X system can access and use .NET assemblies.

You can use the cfobject tag or CreateObject function to create a reference to a .NET class object, by specifying
either .NET or dotnet as the object type. You use the reference to access the .NET class fields and call the .NET class
methods. This technique provides a tightly coupled, stateful, efficient method for accessing .NET classes from
ColdFusion. As an alternative, your .NET application can make the class methods available as web services; however,
using a web service is less reliable, has lower performance, and is less scalable than using ColdFusion objects for the
.NET classes.

Note: .NET applications cannot access ColdFusion component functions directly. You can make the functions available
as web services by specifying remote access. For more information on creating ColdFusion web services, see "Using Web
Services" on page 1093.

Because you use the .NET assembly classes the same way that you use any other ColdFusion object, you do not have
to understand the details of .NET technology; you only have to understand how to use the specific .NET class that you
are accessing. Code that uses a .NET method can be as simple as the following lines:

 <cfobject type = ".NET" name = "mathInstance" class = "mathClass"
            assembly = "C:/Net/Assemblies/math.dll">
<cfset myVar = mathInstance.multiply(1,2)>

ColdFusion .NET access has the following additional features:

· If you make a change in the .NET assembly, ColdFusion automatically recognizes the change and uses that version
    for the next invocation.

· Your application can access .NET assemblies running on multiple machines.

· You can secure the communication between ColdFusion and .NET by using SSL.

· Primitive data types are automatically mapped between ColdFusion and .NET data types.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1150
Using Web Elements and External Objects




How .NET access works
For ColdFusion to access .NET assemblies, ColdFusion .NET extension software must run on the system that hosts the
assemblies. A ColdFusion system that accesses only remote assemblies does not require the .NET extension. The .NET
extension software provides the .NET-side connectivity features that enable access to .NET assemblies, including a
.NET-side agent (which normally runs as the ColdFusion .NET service) that listens for and handles requests from the
ColdFusion system.

On the ColdFusion system, the ColdFusion objects use Java proxies that act as local representatives of the .NET classes.
These proxies use binary TCP or SOAP-based HTTP communication to access a .NET-side agent. The agent then uses
a DLL to invoke the .NET assembly classes. This communication is required in all cases, even if ColdFusion and the
.NET assemblies are on the same system.

The following image shows how CFML-to-.NET access works:




                      CFML Page               Application view
                                                                                     .NET Assembly




                                                                                                 Invokes
               Invokes




                                                                                          JNBShare.dll

                      Java Proxy




                                                                                 JNBDotNetSide.
                Uses                                                             exe.config
                                                                                                         Uses


                                                                              Specifies
                                                                              assembly classes

                     JNBCore.jar
                     (installed on all         TCP/Binary or                          JNBDotNetSide.exe
                                                                                      (runs as a Windows
                     ColdFuson                                                        service)
                                               HTTP/SOAP
                     systems)
                                               communications


                                           both sides can be                          .NET Side
         ColdFusion Side
                                           on a single system


If your .NET assemblies are on the local system, ColdFusion automatically creates and manages all required proxies
and configuration information. Ensure only that the .NET extension is installed on your system and that the
ColdFusion .NET Service is running. You can use the cfobject tag or CreateObject function to access the
assemblies without any additional steps.



                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1151
Using Web Elements and External Objects




If the assemblies are on a remote system, you install and use the ColdFusion .NET extension software on the .NET
system to create Java proxies for the .NET classes, and then move or copy them to the ColdFusion system. Also edit
the JNBDotNetSide.exe.config file on the remote system to specify the .NET classes you use. The .NET system requires
the following .NET extension software:

· JNBDotNetSide.exe, the .NET-side agent that communicates with the ColdFusion system (normally run as the
    ColdFusion .NET service).

· JNBDotNetSide.exe.config, a configuration file that identifies the .NET assemblies that ColdFusion can access.

· jnbproxy.exe and jnbproxyGui.exe are command line and GUI-based programs that generate the Java proxies that
    represent the .NET assemblies.

· Additional support files, including JNBShare.dll, which invoke the .NET assembly classes.

For information on installing the ColdFusion .NET extension, see Installing ColdFusion guide.

Note: When you install a new .NET version, reinstall the ColdFusion .NET extension.


Accessing .NET assemblies
ColdFusion provides two methods for accessing .NET assemblies:

· A local access method for .NET objects that are installed on the ColdFusion system

· A remote access method for .NET objects located on other systems.

For both methods, install the ColdFusion .NET extension and run the ColdFusion .NET service on the system that
hosts the assemblies. You need not install the extension or run the service on a ColdFusion system that accesses only
remote assemblies. For information on installing the ColdFusion .NET extension, see Installing ColdFusion guide.


Accessing local assemblies
For local access, ColdFusion automatically generates and uses proxies for the required .NET assemblies when you first
use the cfobject tag or CreateObject function. ColdFusion caches the proxies for future use, so it does not generate
assembly proxies each time.

Usually when you are accessing local .NET assemblies, you do not have to override the default communication
configuration settings. Sometimes you could have to specify these settings, however. If other software on your system
uses the default 6086 port, for example, change the port number specification in the jnbridge\DotNetSide.exe.config
file, and specify the changed port number in your cfobject tag or CreateObject tag. For information on changing
the port number specification, see "Configuring the .NET-side system" on page 1154,

To use the local access method, use the cfobject tag or CreateObject function to create and access the proxy. You
can use the resulting ColdFusion object to construct the .NET object, call the .NET object's methods, and access its
fields. For detailed information on using .NET classes, see "Using .NET classes" on page 1155.


Accessing remote assemblies
The remote access technique accesses .NET assemblies by using TCP or HTTP to communicate with a .NET-side agent
on a remote system. You create proxy instances and call assembly methods as you do in the Local access method, but
first configure the remote .NET-side agent and, in most cases, the proxy classes that represent the remote .NET classes.


Configure remote .NET access
1 On the remote system, install the ColdFusion .NET integration software and run the .NET-side agent (see Installing
    ColdFusion guide).




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         1152
Using Web Elements and External Objects




2 If the .NET assemblies reside only on the remote system, generate proxy JAR files on that system that represent the
    assemblies (see "Generating the Java proxy classes" on page 1152). Then copy or move the proxy files to the local
    system. If identical .NET assemblies also reside on the local system, you can skip this step.

3 Configure the .NET-side system for remote access (see "Configuring the .NET-side system" on page 1154).


Generating the Java proxy classes
The Java proxy generation code requires direct access to the .NET assemblies to generate the proxy classes. Therefore,
if the system that runs your ColdFusion application does not have the assemblies installed, run a tool on the .NET-side
system to create the Java proxies. ColdFusion installs two proxy generation programs, jnbproxyGui.exe and
jnbproxy.exe in the jnbridge directory when you install the .NET services. The jnbproxyGui.exe program is a Windows
user interface application, and the jnbproxy.exe program is a command line application. Both programs have identical
capabilities.

Note: If the system running the ColdFusion application has the assemblies installed, but must access remote versions of
the assemblies (for example, because of configuration differences), you need not manually generate the proxy classes, and
you can skip this step. Instead, specify the paths to the local .exe or .dll files in the assembly attribute of the cfobject
tag (or CreateObject function) and specify the remote server in the server attribute. Configure the remote system for
access, however.

On a ColdFusion system, the jnbproxyGui and jnbproxy programs are located in the cfroot\jnbridge directory. When
you use the stand-alone installer, the programs are located in the installDir\jnbridge directory.

This document provides the basic information necessary to generate a proxy JAR file using the jnbproxyGui tool.
Additional information is available in the following locations:

· The jnbridge directory includes a jnbproxy.chm Windows Help file with more complete documentation on the
    JNBridge technology that powers the ColdFusion .NET feature, including detailed information on both the
    jnbproxyGui and jnbproxy programs.

· The jnbridge\docs subdirectory includes additional documentation, including users guide.pdf, a PDF version of the
    information in the Help file.

Note: The JNBridge documentation includes information on features that are not supported in ColdFusion. ColdFusion,
for example, does not support access from .NET assemblies to ColdFusion or memory-only communication.


Using the jnbproxyGui tool
You use the jnbproxyGui program to generate a proxy JAR file.


Generate and install a proxy JAR
1 Start JNBProxyGui.exe.

2 The first time you run the program, it displays the Enter Java Options dialog box. Configure the options, and click OK.

    You can change the configuration settings at a later time by selecting Project > Java Options.

    On a system with ColdFusion: If ColdFusion is currently running on this system, ensure that the Start Java
    Automatically option, located on the right side of the JNBProxy Enter Java Options (Project > Java Options) dialog
    box is cleared. Leave the default values for the other settings.

    When you open an existing project, you could get a Restart Java Side pop-up window with the message "You must
    stop and restart the Java side before these changes to the classpath can take effect." You can ignore this message and
    click OK to proceed.

    When you start the program, the Java Options dialog box could appear. You do not have to change anything; click
    OK or Cancel to open the Launch JNBProxy dialog box.



                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         1153
Using Web Elements and External Objects




   In some cases, JNBProxyGui could behave as follows when the Start Java Automatically option is not selected.

   On a system without ColdFusion: If ColdFusion is not currently running on the system, ensure that the following
   options, which are located on the right side of the interface, are set. Leave the default values for the other settings.

   · Ensure that the Start Java Automatically option is selected.

   · Specify the java.exe file to use to compile the JAR file. You can use a Java 1.4 or 1.5 (J2SE 5.0) version of this file.

   · Specify the jnbcore.jar file. The ColdFusion server installer places this file in the cfroot\lib directory. The J2EE
       installer places the file in the cf_webapp_root\WEB-INF\cfusion\lib directory.

   · Specify the bcel.jar file. The ColdFusion server installer places this file in the cfroot\lib directory. The J2EE
       installer places the file in the cf_webapp_root\WEB-INF\cfusion\lib directory.

3 In the Launch JNBProxy dialog box, select Create New Java > .NET Project, and click OK.

4 In the main Java proxy generation interface, set up and build a project:

   a If you have not already done so, add the directory that contains your assembly files to the JNBProxy your project.
       Select Project >Edit Assembly List. In the Assembly List dialog box, click the Add button. In the New Assembly
       List Element dialog box, navigate to the directory that contains your assemblies. Select the directory (or
       directories) in the tree, and click OK. Then click OK in the Edit Assembly List dialog box.

   b Open the Locate Assembly File dialog box (Project > Add Classes From Assembly File) and navigate to the
       directory that you added to the assembly list in step a. Select the assembly file or files that contain classes that
       require proxies and click OK.

   c The classes in the selected file, and other .NET core classes on which they depend, appear in the Environment
       pane. Select all classes for which you want proxies in your JAR file, and click the Add+ button to add the selected
       classes and all supporting classes.

   d In the Exposed Proxies list, select the classes to include in the JAR file. Normally, select all the listed classes,
       which ensures that all required classes are included.

   e Select Project > Build from the main menu. In the Save Generated Proxies dialog box, specify the location and
       JAR file in which to save the generated proxies, and click Save.

   f   After the project is built, select File > Save Project and specify the file in which to save your project.

       The next time you run the jnbproxyGui program, you can select your project and reuse your previous settings,
       including the Assembly List.

5 Copy the JAR file to a directory on your ColdFusion system. You specify this path in the cfobject tag assembly
   attribute.


Supporting classes
JNBProxy can generate proxies not only for the .NET classes that are explicitly listed, but also for supporting classes. A
supporting class for a given .NET class is any class that could be needed as a direct or indirect result of using that .NET
class. For a given .NET class, supporting classes include all of the following:

· The class.

· The class's superclass or superinterface (if it exists) and all of its supporting classes.

· The class's implemented interfaces (if any) and all of their supporting classes.

· For each field in the class:

   · The field's class and all of its supporting classes.

   · For each of the field's index parameters, the parameter's class and all of its supporting classes.



                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1154
Using Web Elements and External Objects




· For each method in the class:

    · The method's return value's class (if any) and all of its supporting classes.

    · For each of the method's parameters, the parameter's class and all of its supporting classes.

· For each constructor in the class, for each of the constructor's parameters, the parameter's class and all of its
   supporting classes.

Unlike Java, where supporting classes include exceptions that methods throw, .NET supporting classes don't include
thrown exceptions, because they are not declared in advance.

The number of supporting classes depends on the classes explicitly listed, but it often can be 200-250 classes. Usually
you generate all supporting classes. However, to save time or space, you can generate only those classes explicitly
specified, without supporting classes.

If a proxy for a supporting class has not been generated, and a proxy for such a class is later needed when the proxies
are used, the proxy for the nearest superclass to the required class is used instead. If that proxy hasn't been generated,
the proxy for the superclass of that superclass is used if it has been generated, and so forth, until the proxy for
System.Object (which is always generated) is encountered. Thus, even with an incomplete set of proxies, code executes,
although functionality and other information could be lost.

In the jnbproxyGui tool, when you click the Add button, the list includes only the explicitly listed classes. When you
click the Add+ button, the list also includes the supporting classes. In the jnbproxy command line program, the default
command generates proxies for the supporting classes; use the /ns option to override this default.


Configuring the .NET-side system
To configure the .NET-side system, you edit the jnbridge\JNBDotNetSide.exe.config configuration file in the
following ways:

· For local assemblies, edit this file only if you do not use the default port, or if you use SSL security.

· For a .NET assembly on a remote machine, register the assemblies in this file to make it accessible to ColdFusion.


Edit the configuration file
1 Ensure that the following lines are in the <configSections> subsection of the <configuration> section:

     <jnbridge>
        <javaToDotNetConfig scheme="Protocol" port="local port number"
              useSSL="true|false" certificateLocation="server certificate path"/>
    </jnbridge>

    · The scheme attribute specifies the communications protocol, and must be jtcp or http.

    · The port number is the port of the .NET-side agent, normally 6086.

    · The useSSL attribute specifies whether to use SSL for secure communications. The attribute is optional; the
       default is to not use SSL.

    · The certificateLocation attribute specifies the location of the server SSL certificate. It is required only if the
       useSSL attribute is true.

   These settings must be the same as the corresponding attributes in your cfobject tag.

2 If the .NET assemblies are on a remote system, specify the assemblies that ColdFusion accesses by adding the
   following elements inside the <jnbridge> section.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1155
Using Web Elements and External Objects




    <assemblyList>
        <assembly file="path to assembly or fully qualified name"/>
        ...
   </assemblyList>

3 Stop and restart the .NET-side agent, if it is running. For example, on a ColdFusion system, restart the ColdFusion
   .NET Service. Your ColdFusion application can now access the .NET classes that you configured.

The following example is a bare-bones JNBDotNetSide.exe.config file that specifies a .NET-side TCP server
configuration. The server communicates by using TCP binary mode and listens on port 6086. Java clients can access \\x

 <?xml version="1.0" encoding="utf-8"?>
<configuration>
     <sectionGroup name="jnbridge">
           <section name="dotNetToJavaConfig"
                      type="System.Configuration.SingleTagSectionHandler"/>
           <section name="javaToDotNetConfig"
                      type="System.Configuration.SingleTagSectionHandler"/>
           <section name="tcpNoDelay"
                      type="System.Configuration.SingleTagSectionHandler"/>
           <section name="javaSideDeclarations"
                      type="System.Configuration.NameValueSectionHandler"/>
           <section name="assemblyList"
                      type="com.jnbridge.jnbcore.AssemblyListHandler, JNBShare"/>
     </sectionGroup>
     <jnbridge>
           <javaToDotNetConfig scheme="jtcp" port="6086"/>
           <assemblyList>
                \\x
                <assembly file="System.Windows.Forms, Version=1.0.5000.0,
                      Culture=neutral, PublicKeyToken=b77a5c561934e089"/>
           </assemblyList>
     </jnbridge>
</configuration>



Using .NET classes
You use .NET assembly classes the same way you use Java and other objects that you create using the cfobject tag or
CreateObject function. In the simplest case, your application code only has to use the following format to include a
local .NET class method:

 <cfobject type = ".NET" name = "mathInstance" class = "mathClass"
           assembly = "C:/Net/Assemblies/math.dll">
<cfset myVar = mathInstance.multiply(1,2)>

Using CFScript and the CreateObject function, you can do the following:

 <cfscript>
     mathInstance = CreateObject(".NET", "mathClass",
           "C:/Net/Assemblies/math.dll");
     myVar = mathInstance.multiply(1,2);
</cfscript>

Note: You cannot load two DLLs with same fully qualified name. ColdFusion always uses the first DLL that it accesses
until the server is restarted. For example, if page1.cfm uses c:\dev\a.dll and page2.cfm uses c:\dev2\a.dll, and both DLLs
have the same fully qualified name, the first DLL file to be loaded remains loaded, and both CFML pages use it.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1156
Using Web Elements and External Objects




When you create objects and access class methods and fields, and convert data types between ColdFusion and .NET,
be aware of the following considerations and limitations:

· Data type conversion considerations described in "Converting between .NET and ColdFusion data types" on
   page 1157

· Limitations described in the "Limitations" section of cfobject: .NET object in the CFML Reference.


Instantiating objects and calling class constructors
When you use the cfobject tag to create a .NET object, ColdFusion does not create an instance of the object.
ColdFusion creates the object instance in either of the following cases:

· If the class has a default constructor, ColdFusion automatically calls the constructor when you first invoke a non-
   static method of the object.

· If the class does not have a default constructor, or if the class has multiple constructors and you do not want to use
   the default, call the special init method of the ColdFusion object. The cfobject tag automatically creates init
   methods for all class constructors. Using the init method causes ColdFusion to call the class constructor with the
   corresponding number and types of parameters. For example, the following tags cause ColdFusion to call the
   MyClass constructor that takes two integer parameters:

    <cfobject type=".NET" name="myObj" class="com.myCo.MyClass"
             assembly="c:\assemblies\myLib.dll">
    <cfset myObj.init(10, 5)>

Note: ColdFusion does not create instances of objects if you use only their static methods.


Calling methods
You call .NET methods in the same way that you use any other ColdFusion object methods. For example, if the
MyClass class has a getName method that takes a numeric ID and returns a name, you would call the method as
follows:

<cfset theID="2343">
<cfset userName=mObj.getName(theID)>


Getting and setting fields
You can access and change public fields of any .NET class by calling the following methods:

Get_fieldName()
Set_fieldName(value)

For example, if the .NET class has a public field named accountID, you can access and change its value by using the
Get_accountID() and Set_accountID() methods, as follows:

<cfobject type=".NET" class="com.myCo.MyClass"
          assembly="c:\assemblies\myLib.dll" name="myObj">
<cfset theAccount=myObj.Get_accountID()>
<cfset myObj.Set_accountID(theAccount + 1)>

You can access, but not modify final fields, so you can only call Get_fieldName() for these fields.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        1157
Using Web Elements and External Objects




Converting between .NET and ColdFusion data types
Accessing .NET classes requires a Java proxy on the ColdFusion system and .NET code on the target system, so data
must be converted among ColdFusion, Java, and .NET (to be exact, Microsoft Intermediate Language, or MSIL) data
types. ColdFusion converts data types automatically. Usually, you do not have to take any special steps to ensure
correct conversion. Some conversion limitations exist, and in some cases you must explicitly specify a data type when
you call a method in a .NET proxy object.

The following paragraphs describe data conversion issues and how to handle them. For a detailed specification of how
ColdFusion converts among ColdFusion data, Java data types, and .NET data types, see cfobject: .NET object in the
CFML Reference.


Data type conversion rules and techniques
ColdFusion converts data automatically among ColdFusion, Java, and CLR data types. The following table indicates
how ColdFusion converts among .NET Common Language Runtime (CLR) primitive and standard data types, the
Java data types used in the proxies to represent CLR data types, and ColdFusion data types in your CFML application.


 .NET type                  Java type             ColdFusion type

 sbyte                       byte                 Integer

 byte                        short                Integer

 short                       short                Integer

 ushort                      int                  Integer

 int                         int                  Integer

 uint                        long                 Number

 char                        char                 Integer or string

 long                        long                 Number

 ulong                       float                Number

 float                       float                Number

 double                      double               Number

                                                  The returned number retains greater precision than is normally displayed in ColdFusion.
                                                  Use the PrecisionEvaluate function to access and display the full precision of a returned
                                                  double value. You can also pass a value with full double precision to a .NET method.

 bool                        boolean              Boolean

 enum                                             Not converted, but enumerator elements can be accessed directly by using the format
                                                  Enumerator_variable.enumerator, as in MyColor.Red

 array                       array                Array

 string                      String               String

 System.Collections.ArrayLis java.util.ArrayList  Array
 t
                                                  Note: ColdFusion converts from .NET type to ColdFusion type only, it does not convert
                                                  ColdFusion Arrays to .NET ArrayLists.

 System.Collections.Hashtab java.util.Hashtable   Structure
 le
                                                  Note: ColdFusion converts from .NET type to ColdFusion type only, it does not convert
                                                  ColdFusion Structures to .NET Hashtables




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                    1158
Using Web Elements and External Objects




 .NET type                  Java type          ColdFusion type

 System.Data.DataTable                          Query

                                               Note: ColdFusion converts from .NET type to ColdFusion type only, it does not convert
                                                ColdFusion Queries to .NET DataTables

 System.DateTime            java.util.Date      Date/time

 decimal                    java.math.BigDecima String representation of the decimal number.
                            l
 System.Decimal                                 For details on using decimal numbers, see "Using decimal numbers" on page 1158.

 System.Object                                  If a .NET argument is of type System.Object, ColdFusion Strings are converted directly.
                                                Other types require using the JavaCast function.

                                                ColdFusion cannot convert System.object instances returned by .NET methods to
                                                ColdFusion types, but you can access them using the Object methods.

                                                For detailed information, see "Converting data to System.Object type" on page 1158.



Using decimal numbers
Use the JavaCast function to convert ColdFusion data into BigDecimal format before you pass the value to a .NET
function, as in the following example:

 <cfset netObj.netFunc(javacast("bigdecimal","439732984732048"))>

ColdFusion automatically converts returned decimal and System.Decimal values to ColdFusion string
representations.


Ensuring decimal and date/time conversions
ColdFusion converts .NET decimal or System.Decimal types only if the proxy for System.Decimal is a value type proxy.
Similarly, it converts .NET System.DateTime values to ColdFusion Date-time values only if the proxy for
System.DateTime is a value type proxy. The ColdFusion server always uses value proxies when it generates these
proxies. If you use the JNBProxyGUI.exe tool to generate the proxy, however, make sure to generate the proxy for
System.Decimal as value type.


Converting data to System.Object type
When a .NET method specifies System.Object (as opposed to a specific Object subclass, such as System.Boolean)
as the argument type, and you want to pass primitive values as arguments to that method, use the javacast function
to identify the data conversion. Once ColdFusion knows the data type, it automatically converts to the appropriate
.NET type. Here is the table that describes the conversion rule from ColdFusion type to .NET type.


 .NET Type                                         Type used in javacast

 bool / System.Boolean                             boolean

 bool[] / System.Boolean[]                         boolean[]

 char / System.Char                                char

 char[] / System.Char[]                            char[]

 double / System.Double                            double

 double[] / System.Double[]                        double[]

 float / System.Single                             float

 float[] / System.Single[]                         float[]




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1159
Using Web Elements and External Objects




 int / System.Int32                           int

 int[] / System.Int32[]                       int[]

 long / System.Int64                          long

 long[] / System.Int64[]                      long[]

 sbyte / System.Sbyte                         byte

 sbyte []/ System.Sbyte[]                     byte []

 short / System.Int16                         short

 short[] / System.Int16[]                     short[]

 System.Decimal                               bigdecimal

 System.String                                String


Note: You do not have to use a JavaCast function to convert ColdFusion string variables. They are automatically
converted to .NET System.String.

Create special objects for .NET primitive unsigned data types, such as byte (unsigned byte), ushort (unsigned short),
uint (unsigned int) and ulong (unsigned long), for which no corresponding java types exist. The following table lists
the .NET primitive types and the corresponding class you must use.


 .NET type                                   Class used in cfobject/createObject

 byte / System.Byte                          System.BoxedByte

 ushort / System.UInt16                      System.BoxedUShort

 uint / System.UInt32                        System.BoxedUInt

 ulong / System.UInt64                       System.BoxedULong


Use the createObject function or cfobject tag to create these special objects, in the same manner as you create
other .NET classes, before you use them in your assignment statement. For example, the following line creates a ushort
representation of the value 100:

<cfset boxedUShort = createObject(".NET". "System.BoxedUShort").init(100)>

The following example creates a System.Hashtable object and populates it with examples of all types of primitives.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1160
Using Web Elements and External Objects




 <!--- create a .NET Hashtable --->
<cfset table = createObject(".NET", "System.Collections.Hashtable")>


<!--- call HashTable.add(Object, Object) method for all primitives --->
<cfset table.add("shortVar", javacast("short", 10))>
<cfset table.add("sbyteVar", javacast("byte", 20))>
<cfset table.add("intVar", javacast("int", 123))>
<cfset table.add("longVar", javacast("long", 1234))>
<cfset table.add("floatVar", javacast("float", 123.4))>
<cfset table.add("doubleVar", javacast("double", 123.4))>
<cfset table.add("charVar", javacast("char", 'c'))>
<cfset table.add("booleanVar", javacast("boolean", "yes"))>
<cfset table.add("StringVar", "Hello World")>
<cfset table.add("decimalVar", javacast("bigdecimal", 123234234.505))>


<!--- call HashTable.add(Object, Object) for unsigned primitive types. --->
<cfset boxedByte = createObject(".NET", "System.BoxedByte").init(10)>
<cfset table.add("byteVar", boxedByte)>


<cfset boxedUShort = createObject(".NET", "System.BoxedUShort").init(100)>
<cfset table.add("ushortVar", boxedUShort)>


<cfset boxedUInt = createObject(".NET", "System.BoxedUInt").init(123)>
<cfset table.add("uintVar", boxedUInt)>


<cfset boxedULong = createObject(".NET", "System.BoxedULong").init(123123)>
<cfset table.add("ulongVar", boxedULong)>


<cfdump var="#DotNetToCFType(table)#">

Any other .NET objects can be passed as it is.


Handling ambiguous type conversions
ColdFusion cannot determine the correct data type conversion if a method has multiple signatures with the same
number of parameters that differ only in the parameter data types. In this case, use the JavaCast method to convert the
ColdFusion data to the Java type that corresponds to the .NET type.

For example, if a .NET class has methods myFunc(ulong) and myFunc(int), use the JavaCast method to convert your
ColdFusion variable to the Java float or int data type, as the following line shows:

 myFunc(JavaCast(int, MyVar));

Similarly, if a .NET class has methods myFunc(int) and myFunc(String), use the JavaCast method to convert your
ColdFusion variable to the Java int or String data type, as shown in the following line:

 myFunc(JavaCast(String, "123");

In some cases, the JavaCast function cannot eliminate ambiguity because a single Java type corresponds to multiple
.NET types. In these cases, ColdFusion creates a proxy with only one method, which uses the .NET data type that
corresponds directly to a Java type.

For example, if the .NET class has methods myFunc(ulong) and myFunc(float), the generated proxy has only one
method. This method calls myFunc(float), because the Java float type used to handle ColdFusion floating-point
numbers corresponds directly to the .NET float type. In this case, you can never call the .NET myFunc(ulong) method.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         1161
Using Web Elements and External Objects




Working with complex .NET data types
When you use complex .NET data such as Hashtable, ArrayList and DataTable, ColdFusion normally automatically
converts the data to the corresponding ColdFusion data type: structure, array, and query, respectively. When you work
with this data you take specific actions to enable the proper access and conversion of the data, as follows:

· Use associative array notation to properly access .NET Hashtable data from ColdFusion

· You cannot use ColdFusion variables directly in parameters that take Hashtable, ArrayList, or DataTable input.

· You can disable automatic conversion of complex .NET data to ColdFusion types.

· You can manually convert complex .NET data to ColdFusion types.


Using Hashtable data in ColdFusion
.NET Hashtables are case sensitive, but most methods of ColdFusion structure access are not case sensitive. Only
associative array notation of the form structName["keyName"] is case sensitive. When .NET Hashtables are
converted to CF structure, the entire data set is converted, even if the element keys differ only in case. Therefore, to get
the values of the keys that differ only in case, use associative array notation.

The following example shows this issue. It creates a Hashtable object with three entries whose key values vary only in
case. In the example, output using dot-delimited structure notation always returns the same value, corresponding to
the all-uppercase key, but associative array notation returns the correct result.

 <!--- Create a Hashtable and convert it to a ColdFusion structure. --->
<cfset table = createObject(".NET", "System.Collections.Hashtable")>
<cfset table.add("Key", "Value1")>
<cfset table.add("KEY", "Value2")>
<cfset table.add("key", "Value3")>
<cfset cftable = DotNetToCFType(table)>


<cfdump var="#cftable#">


<h3>Using dot notation</h3>
Key : <cfoutput>#cftable.Key#</cfoutput><br>
KEY : <cfoutput>#cftable.KEY#</cfoutput><br>
key : <cfoutput>#cftable.key#</cfoutput><br>
<p>


<h3>Using associative array notation</h3>
Key : <cfoutput>#cftable["Key"]#</cfoutput><br>
KEY : <cfoutput>#cftable["KEY"]#</cfoutput><br>
key : <cfoutput>#cftable["key"]#</cfoutput><br>


Using .Net ArrayList in ColdFusion
ColdFusion converts System.Collections.ArrayList objects to ColdFusion arrays, and you can perform all standard
ColdFusion array operations on them. The following example shows this usage:

.Net Code:

 public ArrayList getList(){
     ArrayList myAL = new ArrayList();
     myAL.Add("Hello");
     myAL.Add(1);
     myAL.add(true);
     Return AL;
}




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                1162
Using Web Elements and External Objects




ColdFusion Code:

 <cfset cflist = netObject.getList()>
<cfloop array="#cflist#" index="item">
     <cfoutput>#item#</cfoutput><br>
</cfloop>


<cfif cflist[3]>
          <cfoutput>Third element in the list is true</cfoutput>
</cfif>


Using ADO.Net DataTable in ColdFusion
ColdFusion converts System.Data.DataTable objects to ColdFusion query objects, and you can perform all standard
ColdFusion query operations on them. The following example shows this usage:

.Net code:

 public DataTable datasetMethod()
{
     //conn string
     string connectionString = "...";


     //connection
     using (SqlConnection connection = new SqlConnection(connectionString))
     {
          SqlCommand cmd = new SqlCommand(@"SELECT * FROM [tblEmployees]", connection);
          connection.Open();
          SqlDataReader reader = cmd.ExecuteReader();
          DataTable dt = new DataTable();
          dt.Load(reader);
          return dt;
}
}

ColdFusion code:

 <cfset query1 = netObject.datasetMethod()>
<cfoutput query="query1">
     Query1.CurrentRow = #query1.CurrentRow#<br>
</cfoutput>


Using ColdFusion complex types in .NET input parameters
When a .NET method returns an ArrayList, Hashtable, or DataTable, ColdFusion automatically converts it to a
ColdFusion array, structure, or query, respectively. However ColdFusion does not automatically convert from
ColdFusion data types to these .NET types. (ColdFusion does automatically convert ColdFusion arrays to .Net array
types.) Therefore, you cannot use ColdFusion variables directly as input parameters to .NET object instance methods
that require .NET System.Collection.ArrayList, System.Collection.Hashtable, or System.Data.DataTable types.
Instead create instances of these .NET types and populate them with the required data before you pass them to the
.NET method. For an example of creating and populating a System.Collection.Hashtable object, see the example at the
end of the "Converting data to System.Object type" section.




                                             

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1163
Using Web Elements and External Objects




Disabling automatic conversion of complex .NET data
You can disable automatic conversion of .NET System.Collections.Hashtable,
System.Collections.ArrayList or System.Data.DataTable objects to the corresponding ColdFusion structure,
array, or query objects. You could want to disable the conversion under the following circumstances:

· If a collection or DataTable returned by a .NET method is large and you only want a small subset of the data. If auto
   conversion is enabled, ColdFusion creates a data structure with all the object's fields. Creating the structure could
   take significant time and resources, because ColdFusion must invoke .NET methods internally to get each of the
   fields. You can disable the automatic conversion and retrieve the fields or data from .NET objects like any other
   objects.

· If you invoke a .NET method that returns a complex variable, and then pass the variable to another .NET method
   as argument. If automatic conversion is enabled, you cannot pass the Hashtable object from the first method
   directly to the second method.

To disable automatic conversion, set the JVM coldfusion.dotnet.disableautoconversion system property to true. For
example, in a ColdFusion stand-alone server, or if you use JRun as your J2EE server, include the following setting in
the JVM.config file:

 -Dcoldfusion.dotnet.disableautoconversion=true


Manually converting complex .NET objects
Use the DotNetToCFType function to convert a System.Collections.Hashtable,
System.Collections.ArrayList or System.Data.DataTable object to a ColdFusion structure, array, or query
respectively when either of the following circumstances are true:

· You have set the coldfusion.dotnet.disableautoconversion system property to true.

· Automatic conversion is enabled, you created the complex .NET object by using the createObject function or
   cfobject tag, and you want to convert this object into the corresponding ColdFusion representation.

For an example of using the function, see DotNetToCFType in the CFML Reference.


Using .NET objects
.NET fields and return values with class types are available in ColdFusion as .NET objects. You can use the object's
methods to access object data and make it available to ColdFusion using supported data types.

The following example gets information about a system's drives. It calls the System.IO.DriveInfo.GetDrives() method
to get an array of System.IO.DriveInfo objects, one per drive. It then calls the object methods to get specific
information about the drives, and displays the information. The example uses a cfdump tag to simplify the code.

Note: The System.IO.DriveInfo is not included in the .NET 1.x framework. It is included in .NET 2.0 and later
frameworks. For information on determining the .NET framework, see "Determining and changing the .NET version" on
page 1170.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1164
Using Web Elements and External Objects




<!--- Create a query for the drive information results. --->
<cfset result=QueryNew("name,type,isready,format,label,totalsize,freespace"
                          ,"varchar,varchar,bit,varchar,varchar,double,double")>
<!--- Create a .NET System.IO.DriveInfo object. --->
<cfobject type=".NET" name="sidiClass" class="System.IO.DriveInfo">
<!--- Get the drives. --->
<cfset drives=sidiClass.GetDrives()>
<!--- Loop through drives. --->
<cfloop from="1" to="#ArrayLen(drives)#" index="i">
     <!--- Add a row to the query.--->
     <cfset QueryAddRow(result)>
     <!--- Get the drive name, type, and ready flag. --->
     <cfset QuerySetCell(result, "name", drives[i].Get_Name())>
     <cfset QuerySetCell(result, "type",
                    drives[i].Get_DriveType().ToString())>
     <cfset QuerySetCell(result, "isready", drives[i].Get_IsReady())>
     <!--- Get extra details ONLY if the drive is ready. --->
     <cfif drives[i].Get_IsReady()>
          <cfset QuerySetCell(result, "format", drives[i].Get_DriveFormat())>
          <cfset QuerySetCell(result, "label", drives[i].Get_VolumeLabel())>
          <cfset QuerySetCell(result, "totalsize", drives[i].Get_TotalSize())>
          <cfset QuerySetCell(result, "freespace",
                          drives[i].Get_AvailableFreeSpace())>
     </cfif>
</cfloop>
<cfdump var="#result#">



.NET Interoperability Limitations
ColdFusion .NET interoperability has the following limitations

· You cannot invoke methods with pointers as arguments or the return type.

· You cannot invoke methods that take Out parameters.

· ColdFusion can only convert from System.Data.DataTable, System.Collection.Hashtable and
   System.Collection.ArrayList to ColdFusion data types. ColdFusion cannot convert from ColdFusion queries,
   structures, and arrays to these System data types; however, it can convert from ColdFusion arrays to the CLR array
   type. Therefore, you cannot pass structures or queries directly to .NET methods.

· You cannot access .NET user interface components.

· You cannot use callbacks (events and Delegates) from .NET side.

· ColdFusion cannot determine the correct data type conversion if a method has multiple signatures that have the
   same number of parameters and differ only in the parameter data types. In this case, use the JavaCast method to
   convert the ColdFusion data to the Java type that corresponds to the .NET type.

· If the JavaCast function cannot eliminate ambiguity between functions with the same number of parameters
   because a single Java type corresponds to multiple .NET types, ColdFusion creates a single proxy that uses the .NET
   data type that corresponds directly to a Java type.

   For more information on how to ambiguous handle type conversions, see "Converting between .NET and
   ColdFusion data types" on page 1157.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1165
Using Web Elements and External Objects




· Assemblies registered in the DotNetSide.exe.config file must have unique class names. If two or more assemblies
   have the same class name, method invocation can result in an error or can give the wrong result. For example, do
   not have two DLLs, a.dll and b.dll, that contain the same class name, nam1.name2.MyClass. If you use one DLL and
   later want to use another DLL that contains a class that clashes with first, restart the ColdFusion .NET Service if
   ColdFusion and .NET both are on the same machine. If they are on the different machines, remove the entry for
   the first DLL from the DotNetSide.exe.config file and restart the ColdFusion .NET Service on the Windows
   machine hosting the .NET service.


Example applications
The first application example uses a Microsoft .NET system class method directly. The second application example
uses a custom C# class to access Microsoft Word.


Example: Using a .NET class directly
The following example uses the Microsoft .NET System.Net.NetworkInformation.Ping class method directly to ping
servers. This class is supported in .NET version 2.0 and later.

<!--- This function pings the specified host. --->
<cffunction name="Ping" returntype="string" output="false">
     <cfargument name="host" type="string" required="yes">
     <!--- Local variables --->
     <cfset var pingClass="">
     <cfset var pingReply="">
     <!--- Get Ping class --->
     <cfobject type=".NET" name="pingClass"
                class="System.Net.NetworkInformation.Ping">
     <!--- Perform synchronous ping (using defaults) --->
     <cfset pingReply=pingClass.Send(Arguments.host)>
     <!--- Return result --->
     <cfreturn pingReply.Get_Status().ToString()>
</cffunction>


<h3>Ping Test</h3>
<cfoutput>
     127.0.0.1: #Ping("127.0.0.1")#<br>
     www.adobe.com: #Ping("www.adobe.com")#<br>
</cfoutput>


Example: Using a custom class to access Microsoft Word
The following ColdFusion application uses a custom C# WordCreator class, and supporting classes in Microsoft Office
and Word DLLs, to create a Word document. The application opens Microsoft Word, writes five copies of the text
specified by the someText variable, and saves the document in the file specified by the filename variable. The
application leaves the instance of Word open.

Note: For an example that uses a .NET System class directly and does not require any cousin .NET code, see the
"Limitations" section of cfobject: .NET object in the CFML Reference.

The second listing shows the WordCreator C# source code. To run this application locally, compile this class and the
Microsoft Interop.Word.dll file, and place them in the C:\dotnet directory. (Alternatively, you can place them
elsewhere and change the paths in the cfobjectassembly attribute.) You could need additional or different Microsoft
DLL files, depending on the version of Microsoft Office that you have installed.

The ColdFusion application contains the following code:




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                         1166
Using Web Elements and External Objects




<cfset filename="C:\dotNet\demo.doc">
<cfif fileexists(filename)>
    <cffile action="delete" file="#filename#">
</cfif>
<cfobject type=".NET" assembly="C:\dotNetApp\WordApp.dll,C:\dotNet\Interop.Office.dll"
name="wordCreator" class="WordApp.WordCreator">
<cfset wordCreator.init("#filename#")>
<cfdump label="WordCreator Class Dump" var="#wordCreator#">


<cfset someText = "ColdFusion created this sample document using Windows .NET class methods.
The text is long enough to appear in the Word file on multiple lines.">


<cfloop from=1 to=5 index =i>
    <cfset wordCreator.addText(someText)>
    <cfset wordCreator.newParagraph()>
    <cfset wordCreator.newParagraph()>
    <cfset wordCreator.addText("Starting a new paragraph. It starts a
              a new line.")>
    <cfset wordCreator.newParagraph()>
    <cfset wordCreator.newParagraph()>
</cfloop>
<cfset wordCreator.save()>

The C# source for the WordCreator class is as follows:

using System;
using System.IO;
using System.Collections.Generic;
using System.Text;


// The commented-out lines could be needed on some systems in place of,
// or in addition to, the line that follows them.
// using Microsoft.Office.Core;
// using Word;
// using Microsoft.Office;
// using Microsoft.Office.Interop;
using Microsoft.Office.Interop.Word;
namespace WordApp {
    public class WordCreator {
          object readOnly = false;
          object isVisible = true;
          object missing = System.Reflection.Missing.Value;
          object docType = 0;
          object newTemplate = false;
          object template = "normal.dot";
          object format = WdSaveFormat.wdFormatDocument;
          ApplicationClass app = new ApplicationClass();
          private object fileName;
          private Document doc;
          private bool isNewDoc = false;


              public WordCreator(String fileName) {
              this.fileName = fileName;
              app.Visible = true;
              if (File.Exists(fileName))
                    doc = app.Documents.Open(ref this.fileName, ref missing, ref
                          readOnly, ref missing, ref missing, ref missing, ref missing,




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                    1167
Using Web Elements and External Objects




                          ref missing, ref missing, ref missing, ref missing, ref
                          isVisible, ref missing, ref missing, ref missing, ref missing);
               else {
                    doc = app.Documents.Add(ref template, ref newTemplate,
                          ref docType, ref isVisible);
                    isNewDoc = true;
               }
               doc.Activate();
          }


          public void addText(String text) {
               app.Selection.TypeText(text);
          }


          public void newParagraph() {
               app.Selection.TypeParagraph();
          }


          public void save() {
               if(!isNewDoc)
                    doc.Save();
               else doc.SaveAs(ref fileName, ref format, ref missing, ref missing,
                    ref missing, ref missing, ref missing, ref missing, ref missing,
                    ref missing, ref missing, ref missing, ref missing, ref missing,
                    ref missing, ref missing);
          }
     }
}



Advanced tools
Occasionally, the use of additional tools for generating proxies and running the .NET extension software can be helpful
in some workflows.


Using the jnbproxy command
You can use the jnbproxy command-line tool as an alternative to the jnbproxyGui program, to generate Java proxies.
For more information, see "Generating the Java proxy classes" on page 1152.

For example, you can use this command in a batch file to generate multiple proxy JAR files in a single operation.

The jnbproxy command has the following format:

 jnbproxy options... classes...

For example:

 jnbproxy /al C:\dotNet\netdll\PrimitiveTypes.dll /d C:\dotNet\MyJavajars
     /host localhost /n PrimitiveTypes /nj /pd j2n /port 6085 /pro b
     /pp C:\ColdFusion8\lib CSharpDatatypes.PrimitiveTypes


Options
The following table lists the options that you can use. To create proxies on a system that is running ColdFusion, use
the /nj option and do not specify the /bp, /java, or /jp options.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        1168
Using Web Elements and External Objects




 Option             Req/Opt      Default                      Description

 /al assemblylist   Required                                  Specifies a semicolon-separated series of file paths of .NET assemblies
                                                              (DLL and EXE files) that contain the required .NET classes.

 /bp bcelpath       Optional     Use the CLASSPATH            Specifies the path to the folder that contains the bcel.jar file.
                                 environment variable to
                                                              Ignored if you use the /nj option.
                                 locate the file.

 /cf                Required                                  Use the ColdFusion software license. If you do not include this option,
                                                              your proxies are limited to a 30-day trial period.

 /d directory       Optional     The current execution        Specifies the directory in which to write a JAR file with the generated
                                 directory.                   proxies.

 /f classfile       Optional                                  Reads the classes from the specified text file, not the command line.

                                                              For more information, see the JNBridge documentation.

 /h                 Optional                                  Lists the options and usage information. Typing the command jnbproxy
                                                              with no options or arguments results in the same information.

 /host hostname     Required                                  Specifies the host on which the .NET code is located. This option can be a
                                                              host name or an IP address. Normally, you specify localhost.

 /java javapath     Optional     Use the first java.exe file  Specifies the path of the directory that contains the java.exe program to
                                 found using the system       use when automatically starting Java.
                                 PATH environment variable.
                                                              Ignored if you use the /nj option.

 /jp jnbcorepath    Optional     Use, the CLASSPATH           Specifies the path of the folder containing the file jnbcore.jar.
                                 environment variable.
                                                              Ignored if you use the /nj option.

 /ls                Optional     Generate and list the        Lists all classes that are generated in support of the specified classes (see
                                 proxies.                     "Supporting classes" on page 1153), but don't generate the proxies.

 /n name            Optional     Create a file named          Specifies the name of the JAR file in which to place the proxies. Do not
                                 jnbproxies.jar.              specify the .jar extension; the tool automatically adds it.

 /nj                Optional     Start Java automatically.    Does not start Java automatically. If you use this option, Java must be
                                                              running, and the /bp, /java, /jp, and /wd options, if present, are
                                                              ignored.

 /ns                Optional     Generate proxies for all     Generates proxies for the classes specified on the command line (or class
                                 supporting classes.          file) only, not for any supporting classes.

 /pd                Required                                  Specifies the direction in which the proxies operate. Must be j2n.

 /port portNum      Required                                  Specifies the port on which the .NET side listens when generating the
                                                              proxies. Must be an integer. Normally this value is 6085.

 /pro protocol      Required                                  Specifies the communication mechanism between the .NET and Java
                                                              sides. The valid values are:

                                                              ·  b TCP/binary

                                                              ·  h (HTTP/SOAP


 /wd dir            optional     The system's default         Specifies the working directory for the JVM.
                                 working directory.
                                                              Ignored if the /nj option is present.



Classes
A space-separated sequence of fully qualified .NET class names (for example, CSharpDatatypes.PrimitiveTypes)
for which to generate proxies. The proxies for System.Object and System.Type are always generated, even if they are
not listed in the class list.



                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         1169
Using Web Elements and External Objects




Passing data by reference and value
The proxy generators let you specify whether to pass parameters and return values by reference or by value.


About passing by reference and value
When you pass data by reference, the information transferred between the Java Proxy and the .NET side is a logical
pointer to the underlying .NET object, which continues to reside on the .NET side. When you pass data by value, the
transferred information contains a copy of the contents of the .NET object, which could or continue to reside on the
.NET side after a function call. Passing by reference and value have different advantages.

When you pass data by reference, only changed values are passed between the Java proxy and the .NET object directly.
All other information is passed as reference to its representation in the corresponding objects. Because the reference
is typically much smaller than the actual object, passing by reference is typically fast. Also, because the reference points
to a .NET object that continues to exist on the .NET side, if that .NET object is updated, the updates are immediately
accessible to the proxy object on the Java side. The disadvantage of reference proxies is that any access to data in the
underlying object (for example, field or method accesses) requires a round trip from the Java side to the .NET side
(where the information resides) and back to the Java side.

When you pass data by value, a copy of the data is passed between .NET and Java. Because the data object itself is
typically bigger than a reference, passing an object by value takes longer than passing it by reference. Also, the value
that is passed is a snapshot of the object taken at the time that it was passed. The passed object maintains no connection
to the underlying .NET object, therefore, the passed value does not reflect any updates to the underlying .NET object
that are made after the object is passed. The advantage of passing data by value proxies is that all data in the object is
local to the Java side, and field accesses are fast, because they do not require a round trip to the .NET side and back to
get the data.

The choice of whether to use reference or value proxies depends on the desired semantics of the generated proxies, and
on performance.

· In general, use reference proxies (the default), because they maintain the normal parameter-passing semantics of
    Java and C#.

· In general, use value proxies in any of the following cases:

    · The class functions always must pass parameter values and return values back and forth.

    · The class object contains little data.

    · The object data changes frequently, and the object is either relatively small or the frequency of accesses to data
       outweighs the time taken to transfer the object.


Specifying the data passing method
When you use the JNBProxy.gui tool to generate proxies, you can designate the proxies that pass by reference and
which proxies pass by value. The default proxy type is reference.

To set the data passing method for a class, right-click on the class in the Exposed Proxies pane. Select the desired
passing method from the list that appears. After you select the passing method, the color of the proxy class changes, to
indicate its type: black for reference, or blue for value (public fields/properties style).


Set the passing method for multiple proxy classes simultaneously
1 Select Project > Pass By Reference / Value from the menu bar.

2 The Pass by Reference / Value dialog box lists all proxy classes in the Exposed Proxies pane. Select the classes whose
    passing value you want to set.

3 Click the Reference or Value (Public fields/properties) button to associate the selected classes to the desired type.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                        1170
Using Web Elements and External Objects




4 Repeat steps 2 and 3 to select multiple groups of classes and set their passing methods.

5 Click OK.


Determining and changing the .NET version
If you get errors when using a .NET object in your application, you could have version issues. For example, many
Microsoft system classes were added in .NET Version 2.0, including System.IO.DriveInfo and
System.Net.NetworkInformation.Ping. For examples of these classes in applications, see "Using .NET objects" on
page 1163 and "Example: Using a .NET class directly" on page 1165, respectively.

Use the following function to get the current .NET version:

 <cffunction name="GetDotNetVersion" returntype="string">
      <cfset var seClass="">
      <cfobject type=".NET" name="seClass" class="System.Environment">
      <cfreturn seClass.Get_Version().ToString()>
</cffunction>

If the function reports that the active version is not the one you require, install or reinstall the correct version of the
.NET framework redistributable package on the system that runs ColdFusion. Then reinstall the ColdFusion .NET
extension so that it uses the correct .NET version.


Running the .NET extension agent as an application
The ColdFusion .NET extension installer configures the .NET-side extension agent to run automatically as the
ColdFusion .NET service. You can also run the .NET extension agent as an application.


Run the .NET extension agent as an application
1 Ensure that you stopped the ColdFusion .NET service, if it was running.

2 Open a command prompt window and navigate to the jnbridge directory. On a stand-alone ColdFusion server
    configuration, this directory is installDir\jnbridge. On a system with a stand-alone .NET extension installation, or
    a J2EE or multiserver configuration, it is in the .NETInstallDir\jnbridge directory, and the default installation
    directory is C:\ColdFusonDotNetExtension.

3 Enter the following command:

    JNBDotNetSide




Integrating COM and CORBA Objects in CFML
Applications

You can invoke COM (Component Object Model) or DCOM (Distributed Component Object Model) and CORBA
(Common Object Request Broker) objects.


About COM and CORBA
Adobe ColdFusion supports access to COM and CORBA objects, which can be used in many applications.




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1171
Using Web Elements and External Objects




About objects
COM and CORBA are two of the object technologies supported by ColdFusion. Other object technologies include Java
and ColdFusion components. For more information on ColdFusion components see "Building and Using ColdFusion
Components" on page 177.

An object is a self-contained module of data and its associated processing. An object is a building block that you can
together with other objects and integrate into ColdFusion code to create an application.

A handle, or name, represents an object. Objects have properties that represent information. Objects also provide
methods for manipulating the object and getting data from it. The exact terms and rules for using objects vary with the
object technology.

You create instances of objects using the cfobject tag or the CreateObject function. You then use the object and its
methods in ColdFusion tags, functions, and expressions. For more information on the ColdFusion syntax for using
objects, see "Creating and using objects" on page 1172.


About COM and DCOM
COM (Component Object Model) is a specification and a set of services defined by Microsoft to enable component
portability, reusability, and versioning. DCOM (Distributed Component Object Model) is an implementation of COM
for distributed services, which allows access to components residing on a network.

COM objects can reside locally or on any network node. COM is supported on Microsoft Windows platforms.

For more information on COM, go to the Microsoft COM website, www.microsoft.com/com.


About CORBA
CORBA (Common Object Request Broker Architecture) is a distributed computing model for object-oriented
applications defined by the Object Management Group (OMG). In this model, an object is an encapsulated entity
whose services are accessed only through well-defined interfaces. The location and implementation of each object is
hidden from the client requesting the services. ColdFusion supports CORBA 2.3 on both Windows and UNIX.

CORBA uses an Object Request Broker (ORB) to send requests from applications on one system to objects executing
on another system. The ORB allows applications to interact in a distributed environment, independent of the
computer platforms on which they run and the languages in which they are implemented. For example, a ColdFusion
application running on one system can communicate with an object that is implemented in C++ on another system.

CORBA follows a client-server model. The client invokes operations on objects that the server manages, and the server
replies to requests. The ORB manages the communications between the client and the server using the Internet Inter-
ORB Protocol (IIOP).

Each CORBA object has an interface that is defined in the CORBA Interface Definition Language (IDL). The CORBA
IDL describes the operations that can be performed on the object, and the parameters of those operations. Clients do
not have to know anything about how the interface is implemented to make requests.

To request a service from the server, the client application gets a handle to the object from the ORB. It uses the handle
to call the methods specified by the IDL interface definition. The ORB passes the requests to the server, which
processes the requests and returns the results to the client.

For information about CORBA, see the following OMG website, which is the main web repository for CORBA
information: www.omg.com.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1172
Using Web Elements and External Objects




Creating and using objects
You use the cfobject tag or the CreateObject function to create a named instance of an object. You use other
ColdFusion tags, such as cfset and cfoutput, to invoke the object properties and methods.

Many of the techniques for creating and using objects apply to both COM and CORBA objects. The examples here
assume a sample object named "obj", and that the object has a property called "Property", and methods called
"Method1", "Method2", and "Method3".


Creating objects
You create, or instantiate (create a named instance of) an object in ColdFusion with the cfobject tag or
CreateObject function. The specific attributes or parameters that you use depend on the type of object you use, and
are described in detail in "Creating and using COM objects" on page 1174 and "Creating CORBA objects" on
page 1184. The following examples use a cfobject tag to create a COM object and a CreateObject function to create
a CORBA object:

<cfobject type="COM" action="Create" name="obj" class="sample.MyObject">
obj = CreateObject("CORBA", "d:\temp\tester.ior", "IOR", "Visibroker")

ColdFusion releases any object created by cfobject or CreateObject, or returned by other objects, at the end of the
ColdFusion page execution.


Using properties
Use standard ColdFusion statements to access properties as follows:

1 To set a property, use a statement or cfset tag, such as the following:

    <cfset obj.property = "somevalue">

2 To get a property, use a statement or cfset tag, such as the following:

    <cfset value = obj.property>

As shown in this example, you do not use parentheses on the right side of the equation to get a property value.


Calling methods
Object methods usually take zero or more arguments. You send In arguments, whose values are not returned to the
caller by value. You send Out and In,Out arguments, whose values are returned to the caller, by reference. Arguments
sent by reference usually have their value changed by the object. Some methods have return values, while others do not.

Use the following techniques to call methods:

· If the method has no arguments, follow the method name with empty parentheses, as in the following cfset tag:

    <cfset retVal = obj.Method1()>

· If the method has one or more arguments, place the arguments in parentheses, separated by commas, as in the
   following example, which has one integer argument and one string argument:

    <cfset x = 23>
   <cfset retVal = obj.Method1(x, "a string literal")>

· If the method has reference (Out or In,Out) arguments, use double quotation marks (") around the name of the
   variable you are using for these arguments, as shown for the variable x in the following example:

    <cfset x = 23>
   <cfset retVal = obj.Method2("x","a string literal")>
   <cfoutput> #x#</cfoutput>




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1173
Using Web Elements and External Objects




   In this example, if the object changes the value of x, it now contains a value other than 23.


Calling nested objects
ColdFusion supports nested (scoped) object calls. For example, if an object method returns another object, and you
invoke a property or method on that object, you can use the syntax in either of the following examples:

 <cfset prop = myObj.X.Property>

or

 <cfset objX = myObj.X>
<cfset prop = objX.Property>



Getting started with COM and DCOM
ColdFusion is an automation (late-binding) COM client. As a result, the COM object must support the IDispatch
interface, and arguments for methods and properties must be standard automation types. Because ColdFusion is a
typeless language, it uses the object type information to correctly set up the arguments on call invocations. Any
ambiguity in the object data types can lead to unexpected behavior.

In ColdFusion, only use server-side COM objects, which do not have a graphical user interface. If your ColdFusion
application invokes an object with a graphical interface in a window, the component could appear on the web server
desktop, not on the client desktop. This behavior can take up ColdFusion server threads and prevent further web server
requests from being serviced.

ColdFusion can call Inproc, Local, or Remote COM objects. The attributes specified in the cfobject tag determine
which type of object is called.


COM requirements
To use COM components in your ColdFusion application, you need at least the following items:

· The COM objects (typically DLL or EXE files) that you want to use in your ColdFusion application pages. Ensure
   that these components implement the IDispatch interface, and therefore allow late binding.

· Microsoft OLE/COM Object Viewer, available from Microsoft. This tool lets you view registered COM objects.

   Object Viewer lets you view the class information of an object so that you can properly define the class attribute
   for the cfobject tag. It also displays the interfaces the object supports, so you can discover the properties and
   methods (for the IDispatch interface) of the object.


Registering the object
After you acquire an object, register it with Windows for ColdFusion (or any other program) to find it. Some objects
have setup programs that register objects automatically, while others require manual registration.

You can register Inproc object servers (.dll or .ocx files) manually by running the regsvr32.exe utility using the
following form:

 regsvr32 c:\path\servername.dll

You typically register Local servers (.exe files) either by starting them or by specifying a command-line parameter, such
as the following:

 C:\pathname\servername.exe -register




                                                  

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1174
Using Web Elements and External Objects




Finding the component ProgID and methods
Your COM object supplier provides documentation that explains each of the component methods and the ProgID. If
you do not have documentation, use either the ColdFusion cfdump tag or the OLE/COM Object Viewer to view the
component interface.


Using the cfdump tag to view COM object interfaces
Effective with ColdFusion, the ColdFusion cfdump tag displays the following information about a COM object:

· Public methods

· Put properties

· Get properties

The method and property information include the parameter or property types and whether they are in, out, optional,
or retval values. The cfdump tag output does not include the ProgID of the object.

Note: The dump header indicates the ColdFusion object class, which is coldfusion.runtime.com.ComProxy, and the COM
object CLSID.


Using the OLE/COM Object Viewer
The OLE/COM Object Viewer installation installs the executable, by default, as \mstools\bin\oleview.exe. You use the
Object Viewer to retrieve a COM object ProgID, as well as its methods and properties.

To find an object in the Object Viewer, it must be registered, as described in "Registering the object" on page 1173. The
Object Viewer retrieves all COM objects and controls from the Registry, and presents the information in a simple
format, sorted into groups for easy viewing.

By selecting the category and then the component, you can see the ProgID of a COM object. The Object Viewer also
provides access to options for the operation of the object.


To view object properties:
1 Open the Object Viewer and scroll to the object that you want to examine.

2 Select and expand the object in the left pane of the Object Viewer.

3 Right-click the object to view it, including the TypeInfo.

   If you view the TypeInfo, you see the object methods and properties. Some objects do not have access to the
   TypeInfo area, which is determined when an object is built and by the language used.


Creating and using COM objects
Use the cfobject tag or the CreateObject function to create an instance of the COM object (component) in
ColdFusion before your application pages can invoke any methods or assign any properties in the component.

For example, the following code uses the cfobject tag to create the Windows CDO (Collaborative Data Objects) for
NTS NewMail object to send mail:

<cfobject type="COM"
          action="Create"
          name="Mailer"
          class="CDONTS.NewMail">

The following line shows how to use the corresponding CreateObject function in CFScript:

Mailer = CreateObject("COM", "CDONTS.NewMail");




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                        1175
Using Web Elements and External Objects




Several examples in this documentation use this object.

Note: CDO is installed by default on all Windows NT and 2000 operating systems that have installed the Microsoft SMTP
server. In Windows NT Server environments, the SMTP server is part of the Option Pack 4 setup. In Windows 2000 Server
and Workstation environments, it is bundled with the operating system.

note. >>>The CDO for NTS NewMail component includes methods and properties to perform a wide range of mail-
handling tasks. (In the OLE/COM Object Viewer, methods and properties can be grouped, so you could find it difficult
to distinguish between them at first.)

The CDO for NTS NewMail object includes the following properties:

Body [ String ]
Cc[ String ]
From[ String ]
Importance[ Long ]
Subject[ String ]
To[ String ]

You use these properties to define elements of your mail message. The CDO for NTS NewMail object also includes a
send method which has optional arguments to send messages.


Connecting to COM objects
The action attribute of the cfobject tag provides the following two ways to connect to COM objects:

Create method (cfobject action="Create") Takes a COM object, typically a DLL, and instantiates it before
executing methods and assigning properties.

Connect method (cfobject action="Connect") Links to an object, typically an executable, that is already running
on the server.

You can use the optional cfobjectcontext attribute to specify the object context. If you do not specify a context,
ColdFusion uses the setting in the Registry. The following table describes the context attribute values:


 Attribute value               Description

 InProc                        An in-process server object (typically a DLL) that is running in the same process space as the calling
                               process, such as ColdFusion.

 local                         An out-of-process server object (typically an EXE file) that is running outside the ColdFusion process space
                               but running locally on the same server.

 remote                        An out-of-process server object (typically an EXE file) that is running remotely on the network. If you
                               specify remote, Also use the server attribute to identify where the object resides.



Setting properties and executing methods
The following example, which uses the sample Mailer COM object, shows how to assign properties to your mail
message and how to execute component methods to handle mail messages.

In the example, form variables contain the method parameters and properties, such as the name of the recipient, the
desired e-mail address, and so on:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1176
Using Web Elements and External Objects




 <!--- First, create the object --->
<cfobject type="COM"
      action="Create"
      name="Mailer"
      class="CDONTS.NewMail">


<!--- Second, use the form variables from the user entry form to populate a number
           of properties necessary to create and send the message. --->
<cfset Mailer.From = "#Form.fromName#">
<cfset Mailer.To = "#Form.to#">
<cfset Mailer.Subject = "#Form.subject#">
<cfset Mailer.Importance = 2>
<cfset Mailer.Body = "#Form.body#">
<cfset Mailer.Cc = "#Form.cc#">


<!--- Last, use the Send() method to send the message.
      Invoking the Send() method destroys the object.--->
<cfset Mailer.Send()>

Note: Use the cftry and cfcatch tags to handle exceptions thrown by COM objects. For more information on exception
handling, see "Handling runtime exceptions with ColdFusion tags" on page 287.


Releasing COM objects
By default, COM object resources are released when the Java garbage collector cleans them. You can use the
ReleaseCOMObject function to immediately release resources if an object is no longer needed.

Use the ReleaseCOMObject function to release COM objects that are launched as an external process, such as
Microsoft Excel. The garbage collector does not always clean these processes in a short time, resulting in multiple
external processes running, which drains system resources.

If the COM object has an end method, such as a quit method that terminates the program, call this method before you
call the ReleaseComObject function. If you use the ReleaseComObject function on an object that is in use, the object
is prematurely released and your application gets exceptions.


Example
The following example creates a Microsoft Excel application object, uses it, then releases the object when it is no longer
needed:

 <h3>ReleaseComObject Example</h3>
<cfscript>
obj = CreateObject("Com", "excel.application.9");
//code that uses the object goes here
obj.quit();
ReleaseComObject(obj);
</cfscript>


General COM object considerations
When you use COM objects, consider the following to prevent and resolve errors:

· Ensuring correct threading

· Using input and output arguments

· Understanding common COM-related error messages




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1177
Using Web Elements and External Objects




Ensuring correct threading
Improper threading can cause serious problems when using a COM object in ColdFusion. Make sure that the object
is thread-safe. An object is thread-safe if it can be called from many programming threads simultaneously, without
causing errors.

Visual Basic ActiveX DLLs are typically not thread-safe. If you use such a DLL in ColdFusion, you can make it thread-
safe by using the OLE/COM Object Viewer to change the threading model of the object to the Apartment model.

If you are planning to store a reference to the COM object in the Application, Session, or Server scope, do not use the
Apartment threading model. This threading model is intended to service only a single request. If your application
requires you to store the object in any of these scopes, keep the object in the Both threading model, and lock all code
that accesses the object, as described in "Locking code with cflock" on page 318.


Change the threading model of a COM Object
1 Open the OLE/COM Object Viewer.

2 Select All Objects under Object Classes in the left pane.

3 Locate your COM object. The left pane lists the objects by name.

4 Select your object.

5 Select the Implementation tab in the right pane.

6 Select the Inproc Server tab, below the App ID field.

7 Select the Threading Model drop-down list and select Apartment or Both, as appropriate.


Using input and output arguments
COM object methods in arguments are passed by value. The COM object gets a copy of the variable value, so you can
specify a ColdFusion variable without surrounding it with quotation marks.

COM object out method arguments are passed by reference. The COM object modifies the contents of the variable on
the calling page, so the calling page can use the resulting value. To pass a variable by reference, surround the name of
an existing ColdFusion variable with quotation marks. If the argument is a numeric type, assign the variable a valid
number before you make the call. For example:

 <cfset inStringArg="Hello Object">
<cfset outNumericArg=0>
<cfset result=myCOMObject.calculate(inStringArg, "outNumericArg")>

The string "Hello Object" is passed to the object's calculate method as an input argument. The method sets the value
of outNumericArg to a numeric value.


Understanding common COM-related error messages
The following table described some error messages you could encounter when using COM objects:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                   1178
Using Web Elements and External Objects




 Error                                                          Cause

 Error Diagnostic Information                                   The COM object is not registered or does not exist.

 Error trying to create object specified in the tag.

 COM error 0x800401F3. Invalid class string.

 Error Diagnostic Information                                   The COM object is not registered or does not exist. This error usually
                                                                occurs when an object existed previously, but was removed.
 Error trying to create object specified in the tag.

 COM error 0x80040154. Class not registered.

 Error Diagnostic Information                                   The COM object was instantiated correctly, but the method you
                                                                specified does not exist.
 Failed attempting to find "SOMEMETHOD" property/method on the
 object COM error 0x80020006.

 Unknown name.



Accessing Complex COM Objects using Java proxies
ColdFusion supports Java proxies to access COM objects. If you do not create Java proxies in advance, ColdFusion
must dynamically discover the COM interface. This technique can have two disadvantages:

· Dynamic discovery takes time and can reduce server performance with frequently used complex COM objects.

· Dynamic discovery uses the IDispatcher interface to determine the COM object features, and does not always
    handle some complex COM interfaces.

To overcome these problems, ColdFusion includes a utility, com2java.exe, that creates static Java stub proxy classes
for COM objects. ColdFusion can use these Java stubs to access COM objects more efficiently than when it creates the
proxies dynamically. Additionally, the com2java.exe utility can create stubs for features that the dynamic proxy
generator could miss.

ColdFusion ships with pregenerated stubs for the Windows XP, Windows 2000, and Windows 97 editions of Microsoft
Excel, Microsoft Word, and Microsoft Access. ColdFusion is configured to automatically use these stubs.

If you create Java stub files for a COM object, you continue to use the cfobject tag with a type attribute value of COM,
or the CreateObject function with a first argument of COM, and you access the object properties and methods as you
normally do for COM objects in ColdFusion.

Use the following steps to use the com2java.exe utility. This procedure uses Microsoft Outlook as an example.


To create Java stub files for COM objects:
1 Configure your system as follows:

    a Ensure that a JDK (Java Development Kit) is correctly installed, including proper configuration of the
        CLASSPATH and the command prompt PATH variable.

    b Add CF_root\lib\jintegra.jar to your CLASSPATH.

2 Make a new directory for the Java stub files; for example:

     mkdir C:\src\outlookXP

    This directory can be temporary. You add files from the directory to a ColdFusion JAR file.




                                                     

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                       1179
Using Web Elements and External Objects




3 Run the CF_root\Jintegra\bin\com2java.exe program from a command line or the Windows Start Menu. A window
   appears.

   a If a COM class implements multiple interfaces that define methods with the same names, click the Options
      button and clear the Implement interfaces that may conflict option. The generated Java stub classes do not
      implement the additional, conflicting, interfaces. You can still access the interfaces using the getAsXXX method
      that is generated. See the generated comments in the Java files.

   b Click the Select button.

   c Select your COM object's Type Library or DLL. For Microsoft Outlook in Windows XP, it is normally Program
      Files\Microsoft Office\Office10\MSOUTL.OLB.

   d Enter a package name (for example, outlookXP) in the Java package field in the com2java dialog box. This
      package will contain all the classes for the Java stubs for the COM object.

      Note: Adobe uses a package name that starts with coldfusion.runtime.com.com2java for the packages that contain
      the preinstalled Java stubs for Microsoft Excel, Microsoft Word, and Microsoft Access. For example, the name for
      the package containing the Microsoft Word XP Java stub classes is coldfusion.runtime.com.com2java.wordXP.
      This package name hierarchy results in the wordXP classes having a path inside the msapps.jar file of
      coldfusion\runtime\com\com2java\wordXP\className.class. Although this naming convention is not necessary,
      consider using a similar package naming convention for clarity, if you use many COM objects.

   e Click the Generate Proxies button to display the File browser. Select the directory you created in step 2., and click
      the file browser OK button to generate the stub files.

   f  Click Close to close the com2java.exe utility.

      The files generated in your directory include the following:

      · A Java interface and proxy class for each COM interface

      · A Java class for each COM class

      · A Java interface for each ENUM (a set of constant definitions)

4 Compile your Java code. In a command prompt, do the following:

   a Make the directory that contains the Java stubs (in this example, C:\src\outlookXP) your working directory.

   b Enter the following line:

       javac -J-mx100m -J-ms100m *.java

      The compiler switches ensure that you have enough memory to compile all the necessary files.

      Note: If you did not place jintegra.jar on your CLASSPATH in step 1b, add the switch -
      classpath:/cf_root/lib/jintegra.jar, where cf_rootis the directory where ColdFusion is installed, to the
      command.

5 Ensure that the ColdFusion server is not running. To stop the ColdFusion server, open the Services control panel,
   select ColdFusion application server, and click Stop.

6 Add your .class files to the ColdFusion Microsoft application Java stubs file by doing the following:

   a In the Windows Command prompt, make the parent directory of the directory that contains your class files your
      working directory. In this example, make c:\src your working director by entering cd .. in the Command prompt
      from step 4.

   b Enter the following command:

           jar -uvf cf_root\lib\msapps.jar directoryName\*.class




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                          1180
Using Web Elements and External Objects




      Where cf_root is the directory where ColdFusion is installed and directoryName is the name of the directory that
      contains the class files. For the OutlookXP example, enter the following line:

            jar -uvf C:\CFusion\lib\msapps.jar outlookXP\*.class

7 Update the cf_root /lib/neo-comobjmap.xml file by appending your object definition to the list. The object
   definition consists of the following lines:

    <var name="progID">
   <string>PackageName.mainClass</string>
   </var>

   Use the following values in these lines:

   ProgID The COM object ProgID, as displayed in the OLE/COM object viewer.

   PackageName The package name you specified in step 3c.

   mainClass The main class of the COM object. The main class contains the methods you invoke. For many
   Microsoft applications, this class is Application. In general, the largest class file created in step 4 is the main class.

   For example, to add outlookXP to neo-comobjmap.xml, add the lines in bold text above the </struct> end tag:

    <var name="access.application.9">
   <string>coldfusion.runtime.com.com2java.access2k.Application</string>
   </var>
   <var name="outlook.application.10">
   <string>outlookXP.Application</string>
   </var>
   </struct>

   In this example, outlook.application.10 is the ProgID of the Outlook COM object, outlookXP is the package name
   you specified in step 3c, and Application is the main class of the COM object.

8 Restart the ColdFusion server: Open the Services control panel, select ColdFusion application server, and click
   Start.

9 After you have installed the stubs, you can delete the directory you created in step 2., including all its contents.


Using the Application Scope to improve COM performance
The Java call to create a COM object instance can take substantial time. As a result, creating COM objects in
ColdFusion can be substantially slower than in ColdFusion 5. For example, on some systems, creating a Microsoft
Word application object could take over one second using ColdFusion, while on the same system, the overhead of
creating the Word object could be about 200 milliseconds.

Therefore, in ColdFusion, you can improve COM performance substantially if you can share a single COM object in
the Application scope among all pages.

Use this technique only if the following are true:

· The COM object need not be created for every request or session. (For session-specific objects, consider using the
   technique described here with the Session scope in place of the Application scope.)

· The COM object is designed for sharing.

Because the object can be accessed from multiple pages and sessions simultaneously, also consider the following
threading and locking issues:

· For best performance, make the object multi-threaded. Otherwise, only one request can access the object at a time.




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                         1181
Using Web Elements and External Objects




· Lock the code that accesses and modifies common data. In general, you do not have to lock code that modifies a
   shared object's data, including writable properties or file contents, if multiple requests do not share the data (as
   opposed to the object) . However, specific locking needs depend on the COM object's semantics, interface, and
   implementation.

· All cflock tags in the application that use an Application scope lock share one lock. Therefore, code that accesses
   a frequently used COM object inside an Application scope lock can become a bottleneck and reduce throughput if
   many users request pages that use the object. In some cases, you can avoid some contention by placing code that
   uses the COM object in named locks. Place the code that creates the object in an Application scope lock.

Note: You can also improve the performance of some COM objects by creating Java stubs, as described in "Accessing
Complex COM Objects using Java proxies" on page 1178. Using a Java stub does not improve performance as much as
sharing the COM object, but the technique works with all COM objects. Also, generate Java stubs to correctly access
complex COM objects that do not properly make all their features available through the COM IDispatcher interface.
Therefore, to get the greatest performance increase and prevent possible problems, use both techniques.


Example 1: Using the FileSystem object
The following example uses the Microsoft FileSystem Scripting object in the Application scope. This code creates a
user-defined function that returns a structure that consists of the drive letters and free disk space for all hard drives on
the system.

<cfapplication name="comtest" clientmanagement="No" Sessionmanagement="yes">


<!--- Uncomment the following line if you must delete the object from the
Application scope during debugging. Then restore the comments.
This technique is faster than stopping and starting the ColdFusion server. --->
     <!--- <cfset structdelete(Application, "fso")> --->


<!--- The getFixedDriveSpace user-defined function returns a structure with
the drive letters as keys and the drive's free space as data for all fixed
drives on a system. The function does not take any arguments --->


<cffunction name="getFixedDriveSpace" returnType="struct" output=True>
     <!--- If the FileSystemObject does not exist in the Application scope,
     create it. --->
     <!--- For information on the use of initialization variables and locking in
          this code, see "Locking application variables efficiently" in Chapter 15,
          "Using Persistent Data and Locking" --->
     <cfset fso_is_initialized = False>
     <cflock scope="application" type="readonly" timeout="120">
          <cfset fso_is_initialized = StructKeyExists(Application, "fso")>
     </cflock>
     <cfif not fso_is_initialized >
          <cflock scope="Application" type="EXCLUSIVE" timeout="120">
               <cfif NOT StructKeyExists(Application, "fso")>
                     <cfobject type="COM" action="create" class="Scripting.FileSystemObject"
                          name="Application.fso" server="\\localhost">
               </cfif>
          </cflock>
     </cfif>


     <!--- Get the drives collection and loop through it to populate the




                                              

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1182
Using Web Elements and External Objects




          structure. --->
    <cfset drives=Application.fso.drives()>
    <cfset driveSpace=StructNew()>
    <cfloop collection="#drives#" item="curDrive">
          <!--- A DriveType of 2 indicates a fixed disk --->
          <cfif curDrive.DriveType IS 2>
          <!--- Use dynamic array notation with the drive letter for the struct key
          --->
              <cfset driveSpace["#curDrive.DriveLetter#"]=curDrive.availablespace>
          </cfif>
    </cfloop>
    <cfreturn driveSpace>
</cffunction>


<!--- Test the function. Get the execution time for running the function --->
<cfset start = getTickCount()>
<cfset DriveInfo=getFixedDriveSpace()>
<h3>Getting fixed drive available space</h3>
<cfoutput>Execution Time: #int(getTickCount()-start)# milliseconds</cfoutput><br><br>
<cfdump label="Drive Free Space" var="#driveInfo#">


Example 2: Using the Microsoft Word application object
The following example uses the Microsoft Word application COM object in the Application scope to convert a Word
document to HTML. This example works with Word 2000 as written. To work with Word 97, change "Val(8)" to
"Val(10)".

This example uses an Application scope lock to ensure that no other page interrupts creating the object. Once the
Word object exists, the example uses a named lock to prevent simultaneous access to the file that is being converted.

<cfapplication name="comtest" clientmanagement="No" Sessionmanagement="yes">
<!--- Uncomment the following line if you must delete the object from the
Application scope --->
<!--- <cfset structdelete(Application, "MyWordObj")> --->


<!--- use the GetTickCount function to get a current time indicator, used for
          displaying the total processing time. --->
<cfset start = GetTickCount()>
<!--- If necessary, create the Word.application object and place it in the
          Application scope --->
<cfset WordObj_is_initialized = False>
<cflock scope="application" type="readonly" timeout=120>
    <cfset WordObj_is_initialized = StructKeyExists(application, "MyWordObj")>
</cflock>
<cfif not WordObj_is_initialized >
    <cflock scope="Application" type="exclusive" timeout="120">
          <cfif not StructKeyExists(application, "MyWordObj")>


<!--- First try to connect to an existing Word object --->
              <cftry>
                    <cfobject type="com"
                          action="connect"
                          class="Word.application"
                          name="Application.MyWordobj"
                          context="local">
                    <cfcatch>
<!--- No object exists, create one --->




                                           

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                      1183
Using Web Elements and External Objects




                           <cfobject type="com"
                                action="Create"
                                class="Word.application"
                                name="Application.MyWordobj"
                                context="local">
                     </cfcatch>
                 </cftry>


                 <cfset Application.mywordobj.visible = False>
          </cfif>
     </cflock>
</cfif>


<!--- Convert a Word document in temp.doc to an HTML file in temp.htm. --->
<!--- Because this example uses a fixed filename, multiple pages could try
     to use the file simultaneously. The lock ensures that all actions from
     reading the input file through closing the output file are a single "atomic"
     operation, and the next page cannot access the file until the current page
     completes all processing.
     Use a named lock instead of the Application scope lock to reduce lock contention. --->
<cflock name="WordObjLock" type="exclusive" timeout="120">
     <cfset docs = application.mywordobj.documents()>
     <cfset docs.open("c:\CFusion\wwwroot\temp.doc")>
     <cfset converteddoc = application.mywordobj.activedocument>
     <!--- Val(8) works with Word 2000. Use Val(10) for Word 97 --->
     <cfset converteddoc.saveas("c:\CFusion\wwwroot\temp.htm",val(8))>
     <cfset converteddoc.close()>
</cflock>


<cfoutput>
     Conversion of temp.htm Complete<br>
     Execution Time: #int(getTickCount()-start)# milliseconds<br>
</cfoutput>



Getting started with CORBA
The ColdFusion cfobject tag and CreateObject function support CORBA through the Dynamic Invocation Interface
(DII). As with COM, the object's type information must be available to ColdFusion. Therefore, an IIOP-compliant
Interface Repository (IR) must be running on the network, and the object's Interface Definition Language (IDL)
specification must be registered in the IR. If your application uses a naming service to get references to CORBA objects,
a naming service must also be running on the network.

ColdFusion loads ORB runtime libraries at startup using a connector, which does not tie ColdFusion customers to a
specific ORB vendor. ColdFusion currently includes connectors for the Borland Visibroker 4.5 ORB. The source
necessary to write connectors for other ORBs is available under NDA to select third-party candidates and ORB
vendors.

configuring and enabling CORBA access in ColdFusion requires several steps. For detailed instructions, see Installing
ColdFusion.

Note: When you enable CORBA access in ColdFusion, one step requires you to start the Interface Repository using an
IDL file. This file must contain the IDL for all the CORBA objects that you invoke in ColdFusion applications on the
server.




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                                              1184
Using Web Elements and External Objects




Creating CORBA objects
In ColdFusion, the cfobject tag and CreateObject function create a stub, or proxy object, for the CORBA object on the
remote server. You use this stub object to invoke the remote object.

The following table describes the attributes you use in the cfobject tag to create a CORBA object:


Attribute                Description

type                     Must be CORBA. COM is the default.

context                  Specifies the CORBA binding method, that is, how the object is obtained, as follows:

                         ·  IOR Uses a file containing the object's unique Interoperable Object Reference.

                         ·  NameService Uses a naming service.


class                    Specifies the information required for the binding method to access the object.

                         If you set the context attribute to IOR, The class attribute must be to the full path of a file containing the string
                         version of the IOR. ColdFusion must be able to read this IOR file at all times, so make it local to the server or locate
                         it on the network in an accessible place.

                         If you set the context attribute to NameService, The class attribute must be a name delimited by forward
                         slashes (/), such as MyCompany/Department/Dev. You can use period-delimited "kind" identifiers as part of the
                         class attribute; for example, adobe.current/Eng.current/CF"

name                     Specifies the name (handle) that your application uses to call the object's interface.

locale                   (Optional) Identifies the connector configuration. You can omit this option if ColdFusion Administrator has only
                         one connector configuration, or if it has multiple connector configurations and you want to use the one that is
                         currently selected in the Administrator. If you specify this attribute, it must be an ORB name you specified in the
                         CORBA Connector ORB Name field when you configured a CORBA connector in ColdFusion Administrator; for
                         example, Visibroker.


For example, use the following CFML to invoke a CORBA object specified by the tester.ior file if you configured your
ORB name as Visibroker:

<cfobject action = "create" type = "CORBA" context = "IOR"
     class = "d:\temp\tester.ior" name = "handle" locale = "Visibroker">

When you use the CreateObject function to invoke this CORBA object, specify the name as the function return
variable, and specify the type, class, context, and locale as arguments. For example, the following line creates the same
object as the preceding cfobject tag:

handle = CreateObject("CORBA", "d:\temp\tester.ior", "IOR", "Visibroker")


Using a naming service
Currently, ColdFusion can only resolve objects registered in a CORBA 2.3-compliant naming service.

If you use a naming service, make sure that its naming context is identical to the naming context specified in the
property file of the Connector configuration in use, as specified in the ColdFusion Administrator CORBA Connectors
page. The property file must contain the line "SVCnameroot=name" where name is the naming context being used.
The server implementing the object must bind to this context, and register the appropriate name.


Using CORBA objects in ColdFusion
After you create the object, you can invoke attributes and operations on the object using the syntax described in
"Creating and using objects" on page 1172.




                                                    

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1185
Using Web Elements and External Objects




Using CORBA interface methods in ColdFusion
When you use the cfobject tag or the CreateObject function to create a CORBA object, ColdFusion creates a handle to
a CORBA interface: the cfobjectname attribute or the CreateObject function return variable. For example, the
following CFML creates a handle named myHandle:

<cfobject action = "create" type = "CORBA" context = "IOR"
     class = "d:\temp\tester.ior" name = "myHandle" locale="visibroker">
<cfset myHandle = CreateObject("CORBA", "d:\temp\tester.ior", "IOR", "visibroker")

You use the handle name to invoke all of the interface methods, as in the following CFML:

<cfset ret=myHandle.method(foo)>


Method name case considerations
Method names in IDL are case sensitive. However, ColdFusion is not case sensitive. Therefore, do not use methods
that differ only in case in IDL.

For example, the following IDL method declarations correspond to two different methods:

testCall(in string a); // method #1
TestCall(in string a); // method #2

However, ColdFusion cannot differentiate between the two methods. If you call either method, you cannot be sure
which of the two gets invoked.


Passing parameters by value (in parameters)
CORBA in parameters are always passed by value. When calling a CORBA method with a variable in ColdFusion,
specify the variable name without quotation marks, as shown in the following example:


 IDL                      void method(in string a);

 CFML                     <cfset foo="my string">

                          <cfset ret=handle.method(foo)>



Passing variables by reference (out and inout parameters)
CORBA out and inout parameters are always passed by reference. As a result, if the CORBA object modifies the value
of the variable that you pass when you invoke the method, your ColdFusion page gets the modified value.

To pass a parameter by reference in ColdFusion, specify the variable name in double-quotation marks in the CORBA
method. The following example shows an IDL line that defines a method with a string variable, b, that is passed in and
out of the method by reference. It also shows CFML that calls this method.


 IDL                      void method(in string a, inout string b);

 CFML                     <cfset foo = "My Initial String">

                          <cfset ret=handle.method(bar, "foo")>

                          <cfoutput>#foo#</cfoutput>


In this case, the ColdFusion variable foo corresponds to the inout parameter b. When the CFML executes, the
following happens:

1 ColdFusion calls the method, passing it the variable by reference.

2 The CORBA method replaces the value passed in, "My Initial String", with some other value. Because the variable
   was passed by reference, this action modifies the value of the ColdFusion variable.



                                                      

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                            1186
Using Web Elements and External Objects




3 The cfoutput tag prints the new value of the foo variable.


Using methods with return values
Use CORBA methods that return values as you would any ColdFusion function; for example:


 IDL                    double method(out double a);

 CFML                   <cfset foo=3.1415>

                        <cfset ret=handle.method("foo")>

                        <cfoutput>#ret#</cfoutput>



Using IDL types with ColdFusion variables
ColdFusion supports specific CORBA data types and converts between CORBA types and ColdFusion data.


IDL support
The following table shows which CORBA IDL types ColdFusion supports, and whether they can be used as parameters
or return variables. (NA means not applicable.)


 CORBA IDL type                 General support               As parameters       As return value

 constants                      No                            No                  No

 attributes                     Yes (for properties)          NA                  NA

 enum                           Yes (as an integer)           Yes                 Yes

 union                          No                            No                  No

 sequence                       Yes                           Yes                 Yes

 array                          Yes                           Yes                 Yes

 interface                      Yes                           Yes                 Yes

 typedef                        Yes                           NA                  NA

 struct                         Yes                           Yes                 Yes

 module                         Yes                           NA                  NA

 exception                      Yes                           NA                  NA

 any                            No                            No                  No

 boolean                        Yes                           Yes                 Yes

 char                           Yes                           Yes                 Yes

 wchar                          Yes                           Yes                 Yes

 string                         Yes                           Yes                 Yes

 wstring                        Yes                           Yes                 Yes

 octet                          Yes                           Yes                 Yes

 short                          Yes                           Yes                 Yes

 long                           Yes                           Yes                 Yes

 float                          Yes                           Yes                 Yes




                                                   

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                  1187
Using Web Elements and External Objects




CORBA IDL type                  General support                   As parameters                    As return value

double                          Yes                               Yes                              Yes

unsigned short                  Yes                               Yes                              Yes

unsigned long                   Yes                               Yes                              Yes

longlong                        No                                No                               No

unsigned longlong               No                                No                               No

void                            Yes                               NA                               Yes



Data type conversion
The following table lists IDL data types and the corresponding ColdFusion data types:


IDL type                     ColdFusion type

boolean                      Boolean

char                         One-character string

wchar                        One-character string

string                       String

wstring                      String

octet                        One-character string

short                        Integer

long                         Integer

float                        Real number

double                       Real number

unsigned short               Integer

unsigned long                Integer

void                         Not applicable (returned as an empty string)

struct                       Structure

enum                         Integer, where 0 corresponds to the first enumerator in the enum type

array                        Array (must match the array size specified in the IDL)

sequence                     Array

interface                    An object reference

module                       Not supported (cannot dereference by module name)

exception                    ColdFusion throws an exception of type coldfusion.runtime.corba.CorbaUserException

attribute                    Object reference using dot notation



Boolean data considerations
ColdFusion treats any of the following as Boolean values:




                                                 

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                   1188
Using Web Elements and External Objects




 True                   "yes", "true", or 1

 False                  "no", "false", or 0


You can use any of these values with CORBA methods that take Boolean parameters, as the following code shows:


 IDL                    module Tester
                        {
                        interface TManager
                        {
                        void testBoolean(in boolean a);
                        void testOutBoolean(out boolean a);
                        void testInoutBoolean(inout boolean a);
                        boolean returnBoolean();
                        }
                        }

 CFML                   <cfset handle = CreateObject("CORBA", "d:\temp\tester.ior", "IOR", "") >
                        <cfset ret = handle.testboolean("yes")>
                        <cfset mybool = True>
                        <cfset ret = handle.testoutboolean("mybool")>
                        <cfoutput>#mybool#</cfoutput>
                        <cfset mybool = 0>
                        <cfset ret = handle.testinoutboolean("mybool")>
                        <cfoutput>#mybool#</cfoutput>
                        <cfset ret = handle.returnboolean()>
                        <cfoutput>#ret#</cfoutput>



Struct data type considerations
For IDL struct types, use ColdFusion structures. You can prevent errors by using the same case for structure key names
in ColdFusion as you do for the corresponding IDL struct field names.


Enum type considerations
ColdFusion treats the enum IDL type as an integer with the index starting at 0. As a result, the first enumerator
corresponds to 0, the second to 1, and so on. In the following example, the IDL enumerator a corresponds to 0, b to 1
and c to 2:


 IDL                    module Tester
                        {
                        enum EnumType {a, b, c};
                        interface TManager
                        {
                        void testEnum(in EnumType a);
                        void testOutEnum(out EnumType a);
                        void testInoutEnum(inout EnumType a);
                        EnumType returnEnum();
                        }
                        }

 CFML                   <cfset handle = CreateObject("CORBA", "d:\temp\tester.ior", "IOR", "") >
                        <cfset ret = handle.testEnum(1)>


In this example, the CORBA object gets called with the second (not first) entry in the enumerator.


Double-byte character considerations
If you are using an ORB that supports CORBA later than version 2.0, you do not have to do anything to support
double-byte characters. Strings and characters in ColdFusion convert appropriately to wstring and wchar data when
they are used. However, the CORBA 2.0 IDL specification does not support the wchar and wstring types, and uses the
8-bit Latin-1 character set to represent string data. In this case, you cannot pass parameters containing those
characters, however, you can call parameters with char and string types using ColdFusion string data.




                                               

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                 1189
Using Web Elements and External Objects




Handling exceptions
Use the cftry and cfcatch tags to catch CORBA object method exceptions thrown by the remote server, as follows:

1 Specify type="coldfusion.runtime.corba.CorbaUserException" in the cfcatch tag to catch CORBA
   exceptions.

2 Use the cfcatch.getContents method to get the contents of the exception object.

The cfcatch.getContents method returns a ColdFusion structure containing the data specified by the IDL for the
exception.

The following code example shows the IDL for a CORBA object that raises an exception defined by the
PrimitiveException exception type definition, and the CFML that catches the exception and displays the contents of
the object.


 IDL                    interface myInterface
                        {
                        exception PrimitiveException
                        {
                        long l;
                        string s;
                        float f;
                        };
                        void testPrimitiveException() raises (PrimitiveException);
                        }

 CFML                   <cftry>
                        <cfset ret0 = handle.testPrimitiveException()>
                        <cfcatch type=coldfusion.runtime.corba.CorbaUserException>
                        <cfset exceptStruct= cfcatch.getContents()>
                        <cfdump var ="#exceptStruct#">
                        </cfcatch>
                        </cftry>



CORBA example
The following code shows an example of using a LoanAnalyzer CORBA object. This simplified object determines
whether an applicant is approved for a loan based on the information that is supplied.

The LoanAnalyzer CORBA interface has one method, which takes the following two in arguments:

· An Account struct that identifies the applicant's account. It includes a Person struct that represents the account
   holder, and the applicant's age and income.

· A CreditCards sequence, which corresponds to the set of credit cards the user currently has. A member of the
   CardType enumerator represents the credit card type. (This example assumes that the applicant has no more than
   one of any type of card.)

The object returns a Boolean value indicating whether the application is accepted or rejected.




                                            

DEVELOPING COLDFUSION 9 APPLICATIONS                                                                                     1190
Using Web Elements and External Objects




The CFML does the following:

1 Initializes the values of the ColdFusion variables that are used in the object method. In a more complete example,
   the information would come from a form, query, or both.

   The code for the Person and Account structs is straightforward. The cards variable, which represents the applicant's
   credit cards, is more complex. The interface IDL uses a sequence of enumerators to represent the cards. ColdFusion
   represents an IDL sequence as an array, and an enumerator as 0-indexed number indicating the position of the
   selected item among the items in the enumerator type definition.

   In this case, the applicant has a Master Card, a Visa card, and a Diners card. Because Master Card (MC) is the first
   entry in the enumerator type definition, it is represented in ColdFusion by the number 0. Visa is the third entry, so
   it is represented by 2. Diners is the fifth entry, so it is represented by 4. To represent the sequence, place these
   numbers in an array. Doing so results in a three-element, one-dimensional array containing 0, 2, and 4.

2 Instantiates the CORBA object.

3 Calls the approve method of the CORBA object and gets the result in the return variable, ret.

4 Displays the value of the ret variable, Yes, or No.

   IDL

   struct Person
   {
   long pid;
   string name;
   string middle;
   string last_name;
   }
   struct Account
   {
   Person person;
   short age;
   double income;
   }
   double loanAmountl
   enum cardType {AMEX, VISA, MC, DISCOVER, DINERS};
   typedef sequence<cardType> CreditCards;
   interface LoanAnalyzer
   {
   boolean approve( in Account, in CreditCards);
   }

   CFML




                                                

DEVELOPING COLDFUSION 9 APPLICATIONS                                    1191
Using Web Elements and External Objects




   <!--- Declare a "person" struct ---->
   <cfset p = StructNew()>
   <cfif IsStruct(p)>
   <cfset p.pid = 1003232>
   <cfset p.name = "Eduardo">
   <cfset p.middle = "R">
   <cfset p.last_name = "Doe">
   </cfif>
   <!---- Declare an "Account" struct --->
   <cfset a = StructNew()>
   <cfif IsStruct(a)>
   <cfset a.person = p>
   <cfset a.age = 34>
   <cfset a.income = 150120.50>
   </cfif>
   <!----- Declare a "CreditCards" sequence --->
   <cfset cards = ArrayNew(1)>
   <cfset cards[1] = 0> <!--- corresponds to Amex --->
   <cfset cards[2] = 2> <!--- corresponds to MC --->
   <cfset cards[3] = 4> <!--- corresponds to Diners --->
   <!---- Creating a CORBA handle using the Naming Service---->
   <cfset handle = CreateObject("CORBA", "FirstBostonBank/MA/Loans",
   "NameService") >
   <cfset ret=handle.approve(a, cards)>
   <cfoutput>Account approval: #ret#</cfoutput>




                                          