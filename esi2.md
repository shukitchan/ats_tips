# Edge Side Include (ESI) 2.0 Draft Specification

## Abstract

This specification defines ESI 2.0, the Edge Side Includes language, which allows content assembly by HTTP surrogates,
by providing an in-markup XML-based language. It supersedes version 1.0 as defined in http://www.w3.org/TR/esi-lang. The
2.0 specification improves the previous version with added capability to access HTTP requests and responses between the
surrogate and the client, as well as between surrogate and origin server. It also allows JSON objects and multi-part
responses to be retrieved and stored as variables and thus can  to be accessed later in the document. The specification
also improves the first byte latency of the content assembly with capability to declare inside the document where data
flushing should begin. Finally it adds new string operator such as “contains” and new variables to retrieve random
numbers, time stamp and formatted date.

## Status

Draft

## 1. Introduction

### 1.1 Background

Edge Side Includes (ESI) 1.0 specification was published in 2001. It provides a mean to assemble content on HTTP
surrogates. Further more, surrogates allows these contents or parts of pages to be cacheable. This leads to improved
end-user perceived performance, reduce processing overhead on the origin server, and enhanced availability.

### 1.2 Design Requirement
The intention of ESI 2.0 is to improve on the specification to make it more flexible and easy to use. Deep integration
with HTTP request/response model provides mode control over the requests sent for the includes as well as response sent
back to the client.. The idea of sourcing variables from URI opens the door to more use cases, such as retrieving
contents from various places on a document with one single URI. Elements, such as <esi:inline>, <esi:remove>, <esi:try>,
<esi:attempt>, <esi:exeption> are not popular or efficient, and should be removed. There are additions, such as
“contains” operator and random number, time stamp and formatted date string variables, that provides more flexibility
and therefore supports more use cases.

Simplicity is an important feature of  the original ESI specification. It allows surrogates to function efficiently
without complicated parsing and run-time checks/analysis. The new specification intends to maintain simplicity as a
feature in its design.

The control of where ESI is processed is addressed in the Edge Architecture Specification. Its capability token is

```
ESI/2.0b
```

### 1.3 Modern Day Use Cases

Modern web sites most often use client side JavaScript to  do content assembly. The technique requires using JavaScript
to make HTTP requests and then uses the response to make changes to the DOM of the document on client side. Surrogates
with ESI 2.0 capability can complement this as a high performance, highly available option to render HTML documents in
situations where using JavaScript is not a good option. e.g. When user disable JavaScript in the browser or when a
robot/crawler is accessing the page. It may also be not efficient/practical to make too many HTTP requests in one page
while browsing it on a mobile device.

## 2. ESI elements

### 2.1 include

The <esi:include> element specifies a fragment of assembly. It tells the ESI Processors to fetch the resource specified
by the “src” attribute. This can be an simple URI, as shown in the example below, or can include variables (see
“Variables”). In either case, the final attribute value must be a valid URI. Relative URIs will be resolved relative to
the template. The resulting object will replace the element in the markup served to the client.

For example:

```
<esi:include src="/hello-world.html"/>
```

There are optional attributes. “timeout” defines the timeout of the include request in seconds. “response” defines the
variable name that can hold the response object of this request. “method” defines the method of the request. “entity”
can be used with POST “method” and provide the POST data for the request

For example:

```
<esi:include src="/hello-world.html" response="obj" timeout="5"/>
<esi:include src="/post.php" method="POST" entity="mydata"/>
```

Client request headers will be sent for each of these includes. Optional sub-elements of <esi:req-hdr> allows adding,
removing, changing request headers. The attributes “name” and “value” are mandatory and indicates the header name and
value. The “replace” attributes are optional and if “true”, it indicates the stripping of request headers of the same
name before adding the current one.

For example:

```
<esi:include src="/hello-world.html">
    <esi:req-hdr name="Cookie" value="" replace="true"/>
</esi:include>
```

Optional sub-element <esi:fallback> allows specifying what to do when the request fails. If it is not specified and the
include URI fails, the ESI processor displays an empty string. There is an optional “src” which can specify a URI for
that response. And if it fails as well, the ESI processor displays an empty string. In addition, optional sub-elements
of <esi:req-hdr> can exist under <esi:fallback>

For example:

```
<esi:include src="/hello-world.html">
    <esi:fallback src="/fallback.html">
        <esi:req-hdr name="Cookie" value="" replace="true"/>
    </esi:fallback>
</esi:include>
```

If there is no “src” attribute for sub-element <esi:fallback>, the ESI processor displays the text inside this element
when the include URI fails

For example:

```
<esi:include src="/hello-world.html">
    <esi:fallback>Sorry! </esi:fallback>
</esi:include>
```

Variables can be used in all attributes of <esi:include> and its sub-elements. If the variable is undefined, an empty
string will be used in place of that variable.

For example:

```
<esi:include src="/hello-world{request.cookie['A']}.html"/>
```

### 2.2 load

The <esi:load> element can source a JSON object or multi-part response into a variable. The “src” and “name” attributes
are mandatory. “src” specifies the URI of the request and “name” specifies the variable name.

For example:

```
<esi:load name="data" src="/data.php"/>
```

The response of the URI can be a simple JSON object.

For example:

```
{  "a":"test1", "b":"test2"}
```

And by loading this response into the variable “data”. You can access members of the JSON object as member of the “data”
object.

For example:

```
{data.a}
{data.b}
```

The response of the URI can also be a multi-part response with “multipart/mixed” as the value of the “Content-Type”
response header.

For example, assume that the “Content-Type” is “multipart/mixed; boundary=”simple boundary”, the response can be :

```
--simple boundary
Content-Type: text/application

First part
--simple boundary
Content-Type: text/plain

Second part
--simple boundary
```

And by loading this response into the variable “data”, You can access members of the response as member of the “data”
object.

For example:

```
{data.part[0]}
{data.part[1]}
```

You can also access the part headers as

```
{data.part[0].header['Content-Type']}
{data.part[1].header['Content-Type']}
```

Please note nested multi-part responses are not supported.

There are optional attributes. “timeout” defines the timeout of the include request in seconds. “response” defines the
variable name that can hold the response object of this request. “method” defines the method of the request. “entity”
can be used with POST “method” and provide the POST data for the request

For example:

```
<esi:load name="data1" src="/data1.php" response="obj" timeout="5"/>
<esi:load name="data2" src="/data2.php" method="POST" entity="mydata"/>
```

Client request headers will be sent for each of these includes. Optional sub-elements of <esi:req-hdr> allows adding,
removing, changing request headers. The attributes “name” and “value” are mandatory and indicates the header name and
value. The “replace” attributes are optional and if “true”, it indicates the stripping of request headers of the same
name before adding the current one.

For example:

```
<esi:load name="data" src="/data.php">
    <esi:req-hdr name="Cookie" value="" replace="true"/>
</esi:include>
```

If the request fails, the variable will be undefined and invalid.

Variables can be used in all attributes of <esi:load> and its sub-elements. If the variable is undefined, an empty
string will be used in place of that variable.

For example:

```
<esi:load name="data" src="/data{request.cookie['A']}.php"/>
```

### 2.3 vars

<esi:vars> element is the same as specified in original 1.0 specification. In addition, ESI processor returns empty
string when invalid variables are used

For example:

```
<esi:vars>{request.cookie['A']}</esi:vars>
```

### 2.4 choose | when | otherwise

These elements work the same as in original 1.0 specification. In addition, variables can be used inside the “test”
attribute. If the variable is undefined, the test should evaluated to be false.

### 2.5 <!– esi … –>

This construct works the same as in original 1.0 specification

### 2.6 inline | remove | try | attempt | exception

These elements are deprecated in 2.0

### 2.7 flush

<esi:flush> element is used to indicate the position in the document when ESI processor can start flushing data to
client. Before data flushing can start the status and response header must be sent first. There are sub-elements that
can allow these to be specified before flushing begins. If they are not specified, the status and response header of the
ESI document will be sent instead.

Optional sub-element <esi:status> allows specifying the status of the response. There are two attributes: “value” and
“phrase”. “value” is mandatory and specify the numeric status code. “phrase” is optional and specify the status phrase
for the response. If it is not provided, the default phrase for the specified status code will be used.

Optional sub-elements of <esi:resp-hdr> allows adding, removing, changing response headers. The attributes “name” and
“value” are mandatory and indicates the header name and value. The “replace” attributes are optional and if “true”, it
indicates the stripping of response headers of the same name before adding the current one.

For example:

```
<esi:flush>
    <esi:status name="200"/>
    <esi:resp-hdr name="Cache-Control" value="max-age=0, private" replace="true"/>
</esi:flush>
```

Variables can be used in all attributes of the sub-elements. If the variable is undefined, an empty string will be used
in place of that variable.

## 3. Variables

### 3.1 Client Request

Client request is available as the “request” variable, with the following properties.

* .method – request method
* .header[‘A’] – value of request header ‘A’
* .fullquery – value of the query string
* .query[0] – value of the first field-value pair of the query string
* .query[‘A’] – value of the field ‘A’ in query string
* .fullpath – value of the full path
* .path[0] – value of the first path component
* .fullmatrix – value of the full matrix string
* .matrix[0] – value of the first field-value pair of the matrix string
* .matrix[‘A’] – value of the field ‘A’ in matrix string
* .body – value of the HTTP request if it is a POST
* .cookie[‘A’] – value of cookie ‘A’
* .cookie[‘A’][‘B’] – value of sub cookie ‘B’ of cookie ‘A’

### 3.2 include/load Response

Responses from includes and loads can be available as object, with the following properties

* .status – response status code
* .phrase – response status phrase
* .header[‘A’] – value of response header ‘A’
* .mediatype – e.g. if “Content-Type” is equal to “text/html; charset=iso-latin-1”, value of this property will be “text/html”
* .fallback – set to 1 if object is representing the response of a fallback request of include

### 3.3 load object

The response body of loads is available as object. (See “load” above)

### 3.4 Variables for get random number, current time, formatted time string

There are additional variables. The values of these variables will not change during the processing.

* Math.random – return a random number between 0 and 99.
* Date.timestamp – return the current unix time stamp
* Date.date – return the date format according to RFC 822

## 4. Functions and Expressions

Expression is the same as the original 1.0 specification with two additional operators

### 4.1 string operator: contains / containsIgnoreCase

An additional operator “contains” will check to see if the left operand contains the right operand string.
“containsIgnoreCase” is the case-insensitive version of “contains”

For example:

```
{request.header['User-Agent']} contains 'iPhone'
```

## 5. ESI Processor Consideration

After parsing the ESI document, the ESI processor should immediately send requests for includes/loads that have no
dependencies on other includes/loads. Then it should start processing the ESI document from top to bottom, maintaining a
buffer of data/assembled data that are ready to be sent to client and a list of available variables. “Request” should
exist as a variable at the very beginning. When include/load elements are countered in the ESI document, the processor
will wait till the corresponding requests are done. And the ESI processor will make those corresponding variables of
include/load requests to be available after it encountered the include/load elements. When include/load requests are
done, the ESI processor will also look for other include/load elements inside the document that depends on it and start
to send out those new requests. When the processor encounter the flush element, the buffer of assembled data will be
sent to client and all subsequent processing will flush data to client right away without going to the buffer.

ESI Processor implementations may limit the number of includes used in a single ESI resource. Additionally, they may
limit the number and/or depth of included documents that will be recursed. This assures that ESI processing does not
monopolize resources or impact end-user perceived performance.

ESI Processor may generate messages when URI requests fail or when invalid variables are accessed.

## Appendix A: Acknowledgements

Author would like to thank Mark Notthingham for the early contribution to the development of the ESI 2.0 specification
