# OData URI Conventions #

## 1.0 Introduction ##
The Open Data Protocol (OData) enables the creation of REST-based data services, which allow resources, identified using Uniform Resource Identifiers (URIs) and defined in a data model, to be published and edited by Web clients using simple HTTP messages. This specification defines a set of recommended (but not required) rules for constructing URIs to identify the data and metadata exposed by an OData server as well as a set of reserved URI query string operators, which if accepted by an OData server, MUST be implemented as required by this document.

The [OData:Atom] and [OData:JSON] documents specify the format of the resource representations that are exchanged using OData and the [OData:Operations] document describes the actions that can be performed on the URIs (optionally constructed following the conventions defined in this document) embedded in those representations.

Servers are encouraged to follow the URI construction conventions defined in this specification when possible as  consistency promotes an ecosystem of reusable client components and libraries.

## 2.0 Uri Components ##
A URI used by an OData service has at most three significant parts: the service root URI, resource path and query string options. Additional URI constructs (such as a fragment) MAY be present in a URI used by an OData service; however, this specification applies no further meaning to such additional constructs.

![](http://odata.org/images/ODataUri_thumb.png)

The following are two example URIs broken down into their component parts:

	http://services.odata.org/OData/OData.svc 
  	\_______________________________________/
				   | 
			service root URI 

	http://services.odata.org/OData/OData.svc/Category(1)/Products?$top=2&$orderby=name
	\_______________________________________/ \__________________/ \__________________/
                   |                                |                    |
             service root URI                  resource path        query options

## 3.0 Service Root URI ##
The service root URI identifies the root of an OData service. The resource identified by this URI MUST be an AtomPub Service Document (as specified in [RFC5023]) and follow the OData conventions for AtomPub Service Documents (or an alternate representation of an Atom Service Document if a different format is requested). OData: JSON Format specifies such an alternate JSON-based representation of a service document. The service document is required to be returned from the root of an OData service to provide clients with a simple mechanism to enumerate all of the collections of resources available for the data service.

## 4.0 Resource Path ##
The resource path construction rules defined in this section are optional. OData servers are encouraged to follow the URI path construction rules (in addition to the required query string rules) as such consistency promotes a rich ecosystem of reusable client components and libraries.

The resource path section of a URI identifies the resource to be interacted with (such as Customers, a single Customer, Orders related to Customers in London, and so forth). The resource path enables any aspect of the data model (Collections of Entries, a single Entry, Properties, Links, Service Operations, and so on) exposed by an OData service to be addressed.

### 4.1 Addressing Entities ###
The basic rules for addressing a Collection (of Entities), a single Entity within a Collection, as well as a property of an Entity are covered in the 'resourcePath' syntax rule in Appendix A. 

Below is a snippet from Appendix A:

	resourcePath				= 	[ entityContainerName "." ] entitySetName [collectionNavigation] /
									( entityColServiceOpCall / entityColFunctionCall ) [ collectionNavigation ] /
									( entityServiceOpCall	/ entityFunctionCall ) [ singleNavigation ] /
									( complexColServiceOpCall / complexColFunctionCall ) [ boundOperation ] /
									( complexServiceOpCall / complexFunctionCall ) [ boundOperation / complexPropertyPath ] /
									( primitiveColServiceOpCall / primitiveColFunctionCall ) [ boundOperation ] /
									( primitiveServiceOpCall / primitiveFunctionCall ) [ boundOperation / value ] /
									actionCall 

Since OData has a uniform composable URI syntax and associated rules there are many ways to address a collection of entities, including, but not limited to:

- Via an EntitySet (see rule: entitySetName)

		For Example: http://services.odata.org/OData/OData.svc/Products

- By invoking a Function that returns a collection of Entities (see rule: entityColFunctionCall)

		For Example: http://services.odata.org/OData/OData.svc/GetProductsByCategoryId(categoryId=2)

- By invoking an Action that returns a collection of Entities (see rule: actionCall)
- By invoking a ServiceOperation that returns a collection of Entities (see rule: entityColServiceOpCall)

		For Example: http://services.odata.org/OData/OData.svc/ProductsByColor?color='red'

Likewise there are many ways to address a single Entity.

Sometimes a single Entity can be accessed directly, for example by:

- Invoking a Function that returns a single Entity (see rule: entityFunctionCall)
- Invoking an Action that returns a single Entity (see rule: actionCall)
- Invoking a ServiceOperation that returns a single Entity (see rule: entityServiceOpCall)
 
Often however a single Entity is accessed by composing more path segments to a resourcePath that identifies a Collection of Entities, for example by:

- Using a entityKey to select a single Entity (see rules: collectionNavigation and keyPredicate)

		For Example: http://services.odata.org/OData/OData.svc/Categories(1)

- Invoking an Action bound to a collection of Entities that returns a singleEntity (see rule: boundOperation)
- Invoking an Function bound to a collection of Entities that returns a singleEntity (see rule: boundOperation)

		For Example: http://services.odata.org/OData/OData.svc/Products/MostExpensive

These rules are recursive, so it is possible to address a single Entity via another single Entity, a collection via a single Entity and even a collection via a collection, examples include, but are not limited to:

- By following a Navigation from a single Entity to another related Entity (see rule: entityNavigationProperty)

		For Example: http://services.odata.org/OData/OData.svc/Products(1)/Supplier

- By invoking a Function bound to a single Entity that returns a single Entity (see rule: boundOperation)

		For Example: http://services.odata.org/OData/OData.svc/Products(1)/MostRecentOrder

- By invoking an Action bound to a single Entity that returns a single Entity (see rule: boundOperation)
- By following a Navigation from a single Entity to a related collection of Entities (see rule: entityColNavigationProperty)

		For Example: http://services.odata.org/OData/OData.svc/Categories(1)/Products

- By invoking a Function bound to a single Entity that returns a collection of Entities (see rule: boundOperation)

		For Example: http://services.odata.org/OData/OData.svc/Categories(1)/TopTenProducts

- By invoking an Action bound to a single Entity that returns a collection of Entities (see rule: boundOperation)
- By invoking a Function bound to a collection of Entities that returns a collection of Entities (see rule: boundOperation)

		For Example: http://services.odata.org/OData/OData.svc/Categories(1)/Products/AllOrders

- By invoking an Action bound to a collection of Entities that returns a collection of Entities (see rule: boundOperation)

Finally it is possible to compose path segments onto a resourcePath that identifies a Primivite, Complex instance, Collection of Primitives or Collection of Complex instances and bind an Action or Function that returns a Entity or Collections of Entities.

#### 4.1.1 Canonical Uri ####
For OData services conformant with the addressing conventions in this section, the canonical form of an absolute URI identifying a non contained Entity is formed by adding a single path segment to the service root URI. The path segment is made up of the name of the EntitySet associated with the Entity followed by the key predicate identifying the Entry within the Collection. 

For example the URIs [http://services.odata.org/OData/OData.svc/Categories(1)/Products(1)](http://services.odata.org/OData/OData.svc/Categories(1)/Products(1)) and [http://services.odata.org/OData/OData.svc/Products(1)](http://services.odata.org/OData/OData.svc/Products(1)) represent the same Entry, but the canonical URI for the Entry is [http://services.odata.org/OData/OData.svc/Products(1)](http://services.odata.org/OData/OData.svc/Products(1)).

For contained Entities the canonical Uri begins with canonical Uri of the parent, with further path segments that:

- Name and navigation throught the Containing NavigationProperty 
- and, if the NavigationProperty returns a Collection, an EntityKey (see rule: entityKey) that uniquely identifies the entity in that collection.

### 4.2 Addressing Links between Entities ###
Much like the use of links on Web pages, the data model used by OData services supports relationships as a first class construct. For example, an OData service could expose a Collection of Products Entries each of which are related to a Category Entry.

Links between Entries are addressable in OData just like Entries themselves are (as described above). The basic rules for addressing relationships are shown in the following figure. By the following rule:

	entityUri 		= 	; any uri that identifies a single entity
						; examples include: an entitySet followed by a key or a function/serviceOperation that returns a single entity.
	links 			= 	entityUri "$links" / navigationPropertyName   

For example: [http://services.odata.org/OData/OData.svc/Category(1)/$links/Products](http://services.odata.org/OData/OData.svc/Category(1)/$links/Products) addresses the links between Category(1) and Products.

### 4.3 Addressing Operations ###

#### 4.3.1 Addressing Service Operations ####
The semantic rules for addressing and invoking a ServiceOperation are defined in the [OData:Core](OData) document. 
The grammar for addressing and invoking a ServiceOperation is define by 3 syntax rules in Appendix A:

- The serviceOperationCall syntax rule defines the grammar for the ResourcePath that addresses the ServiceOperation.
- The resourcePath syntax rule defines the grammar for any additional composition of OData ResourcePath segments that rely on the results of calling the ServiceOperation.
- The sopParameterNameAndValue syntax rule defines the grammar for specifying any parameters to the ServiceOperation in the query part of the request Uri.

This example, illustrates a call to a ServiceOperation with subsequent resource path segments, that uses all three syntax rules:

	[http://services.odata.org/OData/OData.svc/GetProductsByRating?rating=3&$filter=Price gt 20.0M](http://services.odata.org/OData/OData.svc/GetProductsByRating?rating=3&$filter=Price gt 20.0M)

This invokes the GetProductsByRating ServiceOperation, with the rating parameter value set to 3, and then subsequently filters Products returned by the  ServiceOperation call to include only those with a price greater than $20. 

#### 4.3.2 Addressing Functions ####
The semantic rules for addressing and invoking Functions are defined in the [OData:Core](OData) document. 
The grammar for addressing and invoking Functions is defined by a number syntax grammar rules in Appendix A, in particular:

- The functionCall syntax rule defines the grammar in the ResourcePath for addressing and providing parameters for a function directly from the Service Root.
- The boundFunctionCall syntax rule defines the grammar in the ResourcePath for addressing and providing parameters for a function that is appended to a ResourcePath that identifies some resources that should be used as the binding parameter value when invoking the Function.
- The boundOperation syntax rule (which encompasses the boundFunctionCall syntax rule), when used by the resourcePath syntax rule, illustrates how a boundFunctionCall can be appended to a ResourcePath.
- The functionExpr, boolFunctionExpr, boundFunctionExpr and boolBoundFunctionExpr syntax rules as used by the filter and orderby syntax rules define the grammar for invoking functions to help filter and order resources identified by the ResourcePath of the uri.
- The aliasAndValue syntax rule defines the grammar for providing function parameter values using Parameter Alias Syntax [OData:Core 7.4.2.3.2](OData). 
- The parameterAndValue syntax rule defines the grammar for providing function parameter values using Parameter Name Syntax [OData:Core 7.4.2.3.2](OData).

#### 4.3.3 Addressing Actions ####
The semantic rules for addressing and invoking Actions are defined in the [OData:Core](OData) document.
The grammar for addressing and invoking Actions are defined by the following syntax grammar rules in Appendix A:

- The actionCall syntax rule defines the grammar in the ResourcePath for addressing and invoking an Action directly from the Service Root.
- The boundActionCall syntax rule defines the grammar in the ResourcePath for addressing and invoking an Action that is appended to a ResourcePath that identifies some resources that should be used as the binding parameter value when invoking the Action.
- The boundOperation syntax rule (which encompasses the boundActionCall syntax rule), when used by the resourcePath syntax rule, illustrates how a boundActionCall can be appended to a ResourcePath.

## 5.0 Query String Options ##
The Query Options section of an OData URI specifies three types of information: System Query Options, Custom Query Options, and Operation (Function and ServiceOperation) Parameters. All OData services MUST follow the query string parsing and construction rules defined in this section and its subsections.

### 5.1 System Query Options ###
System Query Options are query string parameters a client may specify to control the amount and order of the data that an OData service returns for the resource identified by the URI. The names of all System Query Options are prefixed with a "$" character. 

An OData service may support some or all of the System Query Options defined. If a data service does not support a System Query Option, it must reject any requests which contain the unsupported option.

The semantics of all System Query Options are defined in the [OData:Core](OData) document.

The grammar and syntax rules the System Query Options are defined in  

### 5.1.2 Filter System Query Option ###
The $filter system query option allows clients to filter the set of resources that are addressed by a request uri. 
$filter specifies conditions that MUST be met by a resource for it to be returned in the set of matching resources. 

The semantics of $filter are covered in the [OData:core](OData) document.

The [filter](#filterRule) syntax rule defines the formal grammar of the $filter query option.

#### 5.1.2.1 Logical Operators ####
OData defines a set of logical operators that evaluate to true or false (i.e. a boolCommonExpr as defined in Appendix A).
Logical Operators are typically used in the Filter System Query Option to filter the set of resources.
However Servers MAY allow for the use of Logical Operators with the OrderBy System Query Option.
 
The syntax rules for the Logical Operators are defined in Appendix A.

##### 5.1.2.1.1 Equals Operator #####
The Equals operator (or 'eq') evaluates to true if the left operand is equal to the right operand, otherwise if evaluates to false.

##### 5.1.2.1.2 Not Equals Operator #####
The Not Equals operator (or 'ne') evaluates to true if the left operand is not equal to the right operand, otherwise if evaluates to false. 	
 
##### 5.1.2.1.3 Greater Than Operator #####
The Greater Than operator (or 'gt') evaluates to true if the left operand is greater than the right operand, otherwise if evaluates to false. 	

##### 5.1.2.1.4 Greater Than or Equal Operator #####
The Greater Than or Equal operator (or 'ge') evaluates to true if the left operand is greater than or equal to the right operand, otherwise if evaluates to false. 

##### 5.1.2.1.5 Less Than Operator #####
The Less Than operator (or 'lt') evaluates to true if the left operand is less than the right operand, otherwise if evaluates to false.

##### 5.1.2.1.6 Less Than or Equal Operator #####
The Less Than operator (or 'le') evaluates to true if the left operand is less than or equal to the right operand, otherwise if evaluates to false.

##### 5.1.2.1.7 Logical And Operator #####
The Logical And operator (or 'and') evaluates to true if both the left and right operands both evaluate to true, otherwise if evaluates to false.

##### 5.1.2.1.8 Logical Or Operator #####
The Logical Or operator (or 'or') evaluates to false if both the left and right operands both evaluate to false, otherwise if evaluates to true.

##### 5.1.2.1.9 Logical Negation Operator #####
The Logical Negation Operator (or 'not') evaluates to true if the operand evaluates to false, otherwise it evalutes to false.

##### 5.1.2.1.10 Examples #####
The following examples illustrate the use and semantics of each of the logical operators:	

	http://services.odata.org/OData/OData.svc/Products?$filter=Name eq 'Milk' (Requests all products with a Name equal to 'Milk').

	http://services.odata.org/OData/OData.svc/Products?$filter=Name ne 'Milk' (Requests all products with a Name not equal to 'Milk').

	http://services.odata.org/OData/OData.svc/Products?$filter=Name gt 'Milk' (Requests all products with a Name greater than 'Milk'). 

	http://services.odata.org/OData/OData.svc/Products?$filter=Name ge 'Milk' (Requests all products with a Name greater than or equal to 'Milk').

	http://services.odata.org/OData/OData.svc/Products?$filter=Name lt 'Milk' (Requests all products with a Name less than 'Milk').

	http://services.odata.org/OData/OData.svc/Products?$filter=Name le 'Milk' (Requests all products with a Name less than or equal to 'Milk').

	http://services.odata.org/OData/OData.svc/Products?$filter=Name eq 'Milk' and Price lt '2.55M' (Requests all products with the Name 'Milk' that also have a Price less than 2.55).

	http://services.odata.org/OData/OData.svc/Products?$filter=Name eq 'Milk' or Price lt '2.55M' (Requests all products that either have the Name 'Milk' or have a Price less than 2.55).

	http://services.odata.org/OData/OData.svc/Products?$filter=not endswith(Name, 'ilk') (Requests all products that do not have a Name that ends with 'ilk'). 

#### 5.1.2.2 Arithmetic Operators ####
OData defines a set of arithmetic operators that require operands that evaluate to numeric types.
Arithmetic Operators are typically used in the Filter System Query Option to filter the set of resources.
However Servers MAY allow for the use of Arithmetic Operators with the OrderBy System Query Option.

The syntax rules for the Arithmetic Operators are defined in Appendix A.

##### 5.1.2.2.1 Addition Operator #####
The Addition Operator (or 'add') adds the left and right numeric operands together.

##### 5.1.2.2.2 Subtraction Operator #####
The Subtraction Operator (or 'sub') subtracts the right numeric operand from the left numeric operand.

##### 5.1.2.2.3 Multiplication Operator #####
The Multiplication Operator (or 'mul') multiples the left and right numeric operands together.

##### 5.1.2.2.4 Division Operator #####
The Division Operator (or 'div') divides the left numeric operand by the right numeric operand.

##### 5.1.2.2.5 Modulo Operator #####
The Modulo Operator (or 'mod') evaluates to the remainder when the left integral operand is divided by the right integral operand.

##### 5.1.2.2.6 Examples ######
The following examples illustrate the use and semantics of each of the Arithmetic operators:

	http://services.odata.org/OData/OData.svc/Products?$filter=Price add 2.45M eq '5.00M' (Requests all products with a Price of 2.55M).

	http://services.odata.org/OData/OData.svc/Products?$filter=Price sub 0.55M eq '2.00M' (Requests all products with a Price of 2.55M).

	http://services.odata.org/OData/OData.svc/Products?$filter=Price mul 2.0M eq '5.10M' (Requests all products with a Price of 2.55M).

	http://services.odata.org/OData/OData.svc/Products?$filter=Price div 2.55M eq '1M' (Requests all products with a Price of 2.55M).

	http://services.odata.org/OData/OData.svc/Products?$filter=Rating mod 5 eq 0 (Requests all products with a Rating exactly divisable by 5).

#### 5.1.2.3 Parenthesis Operator ####
he Parenthesis Operator (or '( )') overrides the group an expression, so that Parenthesis Operator evaluates to the expression grouped inside the parenthesis. For example:

	http://services.odata.org/OData/OData.svc/Products?$filter=( 4 add 5 ) mod ( 4 sub 1 ) eq 0

Requests all products, because 9 mod 3 is 0. 

#### 5.1.2.4 Canonical Functions ####
In addition to operators, a set of functions are also defined for use with the filter query string operator. The following table lists the available functions. Note: ISNULL or COALESCE operators are not defined. Instead, there is a null literal which can be used in comparisons.

The syntax rules for all canonical functions are defined in Appendix A.

##### 5.1.2.4.1 substringof #####
The substringof canonical function has this signature:

	Edm.Boolean substringof(Edm.String, Edm.String)

If implemented the substringof canonical function MUST return true if, and only if, the second parameter string value contains the first parameter string value.
The substringOfMethodCallExpr syntax rule defines how the substringof function is invoked.

For example:
	
	http://services.odata.org/Northwind/Northwind.svc/Customers?$filter=substringof('Alfreds', CompanyName) eq true

Returns all Customers with a CompanyName that contains 'Alfreds'.

##### 5.1.2.4.2 endswith #####
The endswith canonical function has this signature:

	Edm.Boolean endswith(Edm.String, Edm.String)

If implemented the endswith canonical function MUST returns true if, and only if, the first parameter string value ends with the second parameter string value.
The endsWithMethodCallExpr syntax rule defines how the endswith function is invoked.

For example:

	http://services.odata.org/Northwind/Northwind.svc/Customers?$filter=endswith(CompanyName, 'Futterkiste')

Returns all Customers with a CompanyName that end with 'Futterkiste'.

##### 5.1.2.4.3 startswith #####
The startswith canonical function has this signature:

	Edm.Boolean startswith(Edm.String, Edm.String)

If implemented the startswith canonical function MUST return true if, and only if, the first parameter string value starts with the second parameter string value.
The startsWithMethodCallExpr syntax rule defines how the startswith function is invoked.

For example:

	http://services.odata.org/Northwind/Northwind.svc/Customers?$filter=startswith(CompanyName, 'Alfr')
 
Returns all Customers with a CompanyName that starts with 'Alfr'

##### 5.1.2.4.4 length #####
The length canonical function has this signature:

	Edm.Int32 length(Edm.String)

If implemented the length canonical function MUST return the number of characters in the parameter value.
The lengthMethodCallExpr syntax rule defines how the startswith function is invoked.

For example:

	http://services.odata.org/Northwind/Northwind.svc/Customers?$filter=length(CompanyName) eq 19
 
Returns all Customers with a CompanyName that is 19 characters long.

##### 5.1.2.4.5 indexof #####
The length canonical function has this signature:

	Edm.Int32 indexof(Edm.String, Edm.String)

If implemented the indexof canonical function MUST return the zero based character position of the first occurance of the second parameter value in the first parameter value.
The indexOfMethodCallExpr syntax rule defines how the startswith function is invoked.

For example:

	http://services.odata.org/Northwind/Northwind.svc/Customers?$filter=indexof(CompanyName, 'lfreds') eq 1
 
Returns all Customers with a CompanyName containing 'lfreds' starting at the second character. 
 
##### 5.1.2.4.6 replace #####
The replace canonical function has this signature:

	Edm.String replace(Edm.String, Edm.String, Edm.String)

If implemented the replace canonical function MUST return the first parameter value, with all occurances of the second parameter value replaced by the third parameter value.
The replaceMethodCallExpr syntax rule defines how the replace function is invoked.

For example:

	http://services.odata.org/Northwind/Northwind.svc/Customers?$filter=replace(CompanyName, ' ', '') eq 'AlfredsFutterkiste'
 
Returns all Customers with a CompanyName that equals 'AlfredsFutterkiste' once ' ' has been replaced by ''. 

##### 5.1.2.4.7 substring ######
The substring canonical function has consists of two overloads, with the following signatures:
	 
	Edm.String substring(Edm.String, Edm.Int32)
	Edm.String replace(Edm.String, Edm.Int32, Edm.Int32)

If implemented the two argument substring canonical function MUST return a substring of the first parameter string value, starting at the Nth character and finishing at the last character (where N is the second parameter integer value).
If implemented the three argument substring canonical function MUST return a substring of the first parameter string value identified by selecting M characters starting at the Nth character (where N is the second parameter integer value and M is the third parameter integer value).

The substringMethodCallExpr syntax rule defines how the substring canonical functions are invoked.

For example:
 
	http://services.odata.org/Northwind/Northwind.svc/Customers?$filter=substring(CompanyName, 1) eq 'lfreds Futterkiste'
 
Returns all customers with a CompanyName of 'lfreds Futterkiste' once the first character has been removed.

	http://services.odata.org/Northwind/Northwind.svc/Customers?$filter=substring(CompanyName, 1, 2) eq 'lf'
 
Returns all customers with a CompanyName that has 'lf' as the second and third characters respectively. 

##### 5.1.2.4.8 tolower #####
The tolower canonical function has this signature:

	Edm.String tolower(Edm.String)

If implemented the tolower canonical function MUST return the input parameter string value with all uppercase characters converted to lowercase.
The toLowerMethodCallExpr syntax rule defines how the tolower function is invoked.

For example:

	http://services.odata.org/Northwind/Northwind.svc/Customers?$filter=tolower(CompanyName) eq 'alfreds futterkiste'
 
Returns all Customers with a CompanyName that equals 'alfreds futterkiste' once any uppercase characters have been converted to lowercase.

##### 5.1.2.4.9 toupper ######
The toupper canonical function has this signature:

	Edm.String toupper(Edm.String)

If implemented the toupper canonical function MUST return the input parameter string value with all lowercase characters converted to uppercase.
The toUpperMethodCallExpr syntax rule defines how the tolower function is invoked.

For example:

	http://services.odata.org/Northwind/Northwind.svc/Customers?$filter=toupper(CompanyName) eq 'ALFREDS FUTTERKISTE'
 
Returns all Customers with a CompanyName that equals 'ALFREDS FUTTERKISTE' once any lowercase characters have been converted to uppercase.
 
##### 5.1.2.4.10 trim #####
The trim canonical function has this signature:

	Edm.String trim(Edm.String)

If implemented the trim canonical function MUST return the input parameter string value with all leading and trailing whitespace characters removed.
The trimMethodCallExpr syntax rule defines how the trim function is invoked.

For example:
	
	http://services.odata.org/Northwind/Northwind.svc/Customers?$filter=length(trim(CompanyName)) eq length(CompanyName)

Returns all customers with a CompanyName without leading or trailing whitespace characters.

##### 5.1.2.4.11 concat #####
The concat canonical function has this signature:

	Edm.String concat(Edm.String, Edm.String)

If implemented the concat canonical function MUST return a string that concatinates both input parameter string values together.
The concatMethodCallExpr syntax rule defines how the concat function is invoked.

For example:
	
	http://services.odata.org/Northwind/Northwind.svc/Customers?$filter=concat(concat(City, ', '), Country) eq 'Berlin, Germany'

Returns all customers with from the City of Berlin and the Country called Germany.

##### 5.1.2.4.12 year #####
The year canonical function has the following signatures:

	Edm.Int32 year(Edm.DateTime)
	Edm.Int32 year(Edm.DateTimeOffset)
	
If implemented the year canonical function MUST return the year component of the DateTime or DateTimeOffset parameter value.
The yearMethodCallExpr syntax rule defines how the year function is invoked. 

For example:

	http://services.odata.org/Northwind/Northwind.svc/Employees?$filter=year(BirthDate) eq 1971
 
Returns all Employees who were born in 1971.

##### 5.1.2.4.13 years #####
TODO: for Edm.Time

##### 5.1.2.4.14 month #####
The month canonical function has the following signatures:

	Edm.Int32 month(Edm.DateTime)
	Edm.Int32 month(Edm.DateTimeOffset)
	
If implemented the month canonical function MUST return the month component of the DateTime or DateTimeOffset parameter value.
The monthMethodCallExpr syntax rule defines how the month function is invoked. 

For example:

	http://services.odata.org/Northwind/Northwind.svc/Employees?$filter=month(BirthDate) eq 5
 
Returns all Employees who were born in May.

##### 5.1.2.4.15 day #####
The day canonical function has the following signatures:

	Edm.Int32 day(Edm.DateTime)
	Edm.Int32 day(Edm.DateTimeOffset)
	
If implemented the day canonical function MUST return the day component DateTime or DateTimeOffset parameter value.
The dayMethodCallExpr syntax rule defines how the day function is invoked. 

For example:

	http://services.odata.org/Northwind/Northwind.svc/Employees?$filter=day(BirthDate) eq 8
 
Returns all Employees who were born on the 8th day of a month.

##### 5.1.2.4.16 days #####
TODO: for Edm.Time

##### 5.1.2.4.17 hour #####
The day canonical function has the following signatures:

	Edm.Int32 hour(Edm.DateTime)
	Edm.Int32 hour(Edm.DateTimeOffset)
	
If implemented the hour canonical function MUST return the hour component of the DateTime or DateTimeOffset parameter value.
The hourMethodCallExpr syntax rule defines how the hour function is invoked. 

For example:

	http://services.odata.org/Northwind/Northwind.svc/Employees?$filter=hour(BirthDate) eq 4
 
Returns all Employees who were born in the 4th hour of a day.

##### 5.1.2.4.18 hours #####
TODO: for Edm.Time

##### 5.1.2.4.19 minute #####
The minute canonical function has the following signatures:

	Edm.Int32 minute(Edm.DateTime)
	Edm.Int32 minute(Edm.DateTimeOffset)
	
If implemented the minute canonical function MUST return the minute component of the DateTime or DateTimeOffset parameter value.
The minuteMethodCallExpr syntax rule defines how the minute function is invoked. 

For example:

	http://services.odata.org/Northwind/Northwind.svc/Employees?$filter=minute(BirthDate) eq 40
 
Returns all Employees who were born in the 40th minute of any hour on any day.

##### 5.1.2.4.20 minutes ######
TODO: for Edm.Time

##### 5.1.2.4.21 second #####
The second canonical function has the following signatures:

	Edm.Int32 second(Edm.DateTime)
	Edm.Int32 second(Edm.DateTimeOffset)
	
If implemented the second canonical function MUST return the second component of the DateTime or DateTimeOffset parameter value.
The secondMethodCallExpr syntax rule defines how the second function is invoked. 

For example:

	http://services.odata.org/Northwind/Northwind.svc/Employees?$filter=second(BirthDate) eq 40
 
Returns all Employees who were born in the 40th second of any minute of any hour on any day.

##### 5.1.2.4.22 seconds #####
TODO: for Edm.Time
 
##### 5.1.2.4.23 round #####
The round canonical function has the following signatures
	
	Edm.Double round(Edm.Double)
	Edm.Decimal round(Edm.Decimal)

If implemented the round canonical function MUST return round the input numeric parameter value to the nearest numeric value with no decimal component.
The roundMethodCallExpr syntax rule defines how the round function is invoked.
 
For example:

	http://services.odata.org/Northwind/Northwind.svc/Orders?$filter=round(Freight) eq 32
 
Returns all Orders that have a Freight cost that rounds to 32.

##### 5.1.2.4.24 floor #####
The floor canonical function has the following signatures
 
	Edm.Double floor(Edm.Double)
	Edm.Decimal floor(Edm.Decimal)

If implemented the floor canonical function MUST return round the input numeric parameter down value to the nearest numeric value with no decimal component.
The floorMethodCallExpr syntax rule defines how the floor function is invoked.
 
For example:

	http://services.odata.org/Northwind/Northwind.svc/Orders?$filter=floor(Freight) eq 32
 
Returns all Orders that have a Freight cost that rounds down to 32.

##### 5.1.2.4.25 ceiling #####
The ceiling canonical function has the following signatures
 
	Edm.Double ceiling(Edm.Double)
	Edm.Decimal ceiling(Edm.Decimal)

If implemented the ceiling canonical function MUST return round the input numeric parameter up value to the nearest numeric value with no decimal component.
The ceilingMethodCallExpr syntax rule defines how the ceiling function is invoked.
 
For example:

	http://services.odata.org/Northwind/Northwind.svc/Orders?$filter=ceiling(Freight) eq 32
 
Returns all Orders that have a Freight cost that rounds up to 32.
 
##### 5.1.2.4.26 isof #####
The isof canonical function has the following signatures

	Edm.Boolean isof(type)
	Edm.Boolean isof(expression, type)

If implemented the single parameter isof canonical function MUST return true if, and only if, the current instance is assignable to the type specified.
If implemented the two parameter isof canonical function MUST return true if, and only if, the object referred to by the expression is assignable to the type specified.

The isofMethodCallExpr syntax rule defines how the isof function is invoked.

For example:

	http://services.odata.org/Northwind/Northwind.svc/Orders?$filter=isof('NorthwindModel.BigOrder')

Returns only orders that are also BigOrders.

 	http://services.odata.org/Northwind/Northwind.svc/Orders?$filter=isof(Customer, 'NorthwindModel.MVPCustomer')

Returns only orders that have a customer that is a MVPCustomer.

##### 5.1.2.4.27 cast #####
TODO: figure out how to actually do a cast!

#### 5.1.2.5 Operator Precedence ####
OData Servers MUST use the following operator precedence for supported operators when evaluating $filter and $orderby expressions:

	GROUP 					OPERATOR			DESCRIPTION				 

	Logical Operators 		eq 					Equal 					 
							ne 					Not Equal 				 
							gt 					Greater Than 			 
							ge 					Greater Than or Equal 
							lt					Less Than 
							le 					Less Than or Equal 
							and 				Logical And 
							or 					Logical Or 
							not 				Logical Negation 

	Arithmetic Operators 	add 				Addition 
							sub 				Subtraction 
							mul 				Multiplication 
							div 				Division 
							mod 				Modulo 

	Grouping Operators 		( ) 				Precedence grouping 

### 5.1.3 Expand System Query Option ###
The $expand system query option allows clients to request related resources when a resource that satifies a particular request is retrieved.  

The semantics of $expand are covered in the [OData:core](OData) document.

The [expand](#expandRule) syntax rule defines the formal grammar of the $expand system query option.

### 5.1.4 Select System Query Option ###
The $select system query option allows clients to requests a limited set of information for each Entity or ComplexType identified by the ResourcePath and other System Query Options like $filter, $top, $skip etc. 
The $select query option is often used in conjunction with the $expand query option, to first increase the scope of the resource graph returned ($expand) and then selectively prune that resource graph ($select).

The semantics of $select are covered in the [OData:core](OData) document.

The [select](#selectRule) syntax rule defines the formal grammar of the $select query option.

### 5.1.4 OrderBy System Query Option ###
The $orderby system query option allows clients to request resource in a particular order. 

The semantics of $orderby are covered in the [OData:core](OData) document.

The [orderby](#orderByRule) syntax rule defines the formal grammar of the $orderby query option.

### 5.1.5 Top and Skip System Query Options ###
The $top system query option allows clients a required number of resources, used in conjunction $skip query option which allows a client to ask the server to begin sending resource after skipping a required number of resource, a client can request a particular page of matching resources.  

The semantics of $top and $skip are covered in the [OData:core](OData) document.

The [skip](#skipRule) and [top](#topRule) syntax rules define the formal grammar of the $top and $skip query options respectively.

### 5.1.6 Inlinecount System Query Option ####
The $inlinecount system query option allows clients to request a count of the number of matching resources inline with the resources in the response. 
Typically this is most useful when a server implements serverside paging, as it allows clients to retrieve the number of matching resources even if the server decides to only response with a single page of matching resources.

The semantics of $inlinecount is covered in the [OData:core](OData) document.

The [inlinecount](#inlinecountRule) syntax rule define the formal grammar of the $inlinecount query option.

### 5.1.7 Format System Query Option ###
The $format system query option if supported allows clients to request a response in a particular format. Generally requesting a particular format is done using standard content type negotiation, however occasionally the client has no access to request headers which makes standard content type negotiation not an option, it is in these situations that $format is generally used. Where present $format takes precedence over standard content type negotiation.

The semantics of $format is covered in the [OData:core](OData) document.

The [format](#formatRule) syntax rule define the formal grammar of the $format query option. 

## 5.2 Custom Query Options ##
Custom query options provide an extensible mechanism for data service-specific information to be placed in a data service URI query string. A custom query option is any query option of the form shown by the rule "customQueryOption" in Appendix A: ABNF for OData URI Conventions. 

Custom query options MUST NOT begin with a "$" character because the character is reserved for system query options. A custom query option MAY begin with the "@" character, however this doing  can result in custom query options that collide with Function Parameters values specified using Parameter Aliases.

For example this URI addresses provide a 'securitytoken' via a custom query option:
	http://service.odata.org/OData/OData.svc/Products?$orderby=Name&securitytoken=0412312321

## 5.3 Uri Equivalence ##
When determining if two URIs are equivalent, each URI SHOULD be normalized using the rules specified in [RFC3987](http://www.ietf.org/rfc/rfc3987.txt) and [RFC3986](http:// "http://www.ietf.org/rfc/rfc3986.txt") and then compared for equality using the equivalence rules specified in [HTTP/1.1](http://www.ietf.org/rfc/rfc2616.txt), Section 3.2.3.
