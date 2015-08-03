---
layout: post
title: "Obtaining data from a submission that's in error"
date: 2006-09-25
comments: true
---
Some services return non-successful HTTP status codes, but still expect the
client to respond to the body of the response. For example, a SOAP request to
add a new customer to a database might return an HTTP 500 code to indicate
that the customer already exists, but the actual body of the response might be
perfectly usable XML.

  
Since XForms discards returned data when there is a submission error, and
classes HTTP 500 as an error, we have introduced the `instance-error`
attribute on `submission`.

<!-- more -->

  
The `instance-error` attribute is an IDREF, pointing to an instance in the
host document. If no `instance-error` attribute is present on that submission
then processing continues as normal for `xforms-submit-error`.

  
However, when an error response is received from a formsPlayer submission
_with_ an `instance-error` attribute, one of the following things may happen:

  

  1. If the instance-error attribute does not refer to a real instance, processing continues as defined in the XForms spec.
  

  2. If an instance-error attribute points to an instance in the document:  
  

    * if the response is XML, the response XML is loaded into that instance replacing the existing contents;
  

    * if the response is _not_ XML, the instance is replaced with a document containing a root element of `instanceData`, which contains the response data as CDATA.
  
  
  
  
This feature is available from [version
1.5.0.1030](/node/262/release#version-1.5.0.1030) onwards.

