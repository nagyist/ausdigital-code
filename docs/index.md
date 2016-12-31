# UBL JSON Codes

## UBL JSON Code Lists Specification

 * ![raw](http://rfc.unprotocols.org/spec:2/COSS/raw.svg)
 * Editor: Steve Capell
 * Contributors: 

## Introduction

This specification defines a JSON representation for standard code lists (eg the ISO-3166 coutnry code list) and also for context specific subsets and extensions.  

The JSON code list representation and standard API definition provides a alternative to the UBL [Genericode](https://docs.oasis-open.org/codelist/cs-genericode-1.0/doc/oasis-code-list-representation-genericode.html) and [Context/Value Association](http://docs.oasis-open.org/codelist/cs01-ContextValueAssociation-1.0/doc/context-value-association.html) specifications (and the associated XSLT based runtime validation framework) for implementers that prefer a REST/JSON model.

## JSON Representation

The JSON representation for standard coe lists is as defined below.

```
{
   "CodeList":{
       "ListURI":"http://ausdigital.org/code-lists/paymentMeans-v1.0.0.json",
       "SchemeIdentification":{
          "ListID":"UN/ECE 4461",
          "listAgencyID":"UN/ECE",
          "listAgencyName":"United Nations Economic Commission for Europe",
          "ListName":"PaymentMeansCode",
          "listVersionID":"D10B",
          "listSchemeURI":"urn:un:unece:uncefact:codelist:standard:UNECE:PaymentMeansCode:D10B"},
       "UsageContext":{
          "ProcessID":["urn:resources.digitalbusinesscouncil.com.au:dbc:einvoicing:ver1.0"]
       "Codes":[
          {"Code":"1","Name":"Instrument not defined"},
          {"Code":"2","Name":"Automated clearing house credit"},
          ...
          {"Code":"20","Name":"Cheque"},
          ...],
     }
}   
```

* The "ListURI" MUST resolve to the web resource that is the the code list JSON file.
* The "SchemeIdentification" elements map exactly to the CCTS properties for the code list and SHOULD be used to populate attributes when mapping from JSON to UBL XML
* The "UsageContext" element is an array of process identifiers that represent the rule base a specific UBL implementation such as the DBC e-invoicing framework.  This element carries the same values as 
  * The "ProcessIdentifier" element in a DCP service metadata record.
  * The ""customizationID" element in a UBL instance document.  
* The "Codes" element MUST contain at least one object representing one code in the scheme.
* The properties of each object MUST conform to the code property terms defined below and MAY include other properties specific to the coding scheme.

## Code Property Terms

The {Codes} object array contains the list of actual codes and proerties.  The property names MUST comply with the collowing guidance:

* "Code" : required : contains a short code value (eg "AU") that must be unique within the scheme and must not contain spaces.
* "Name" : required : contains a descriptive name for the code value (eg "Australia")
* "Description" : optional : contains a richer description of the code meaning or interpretation.
* "Status" : optional : Defaults to "Active" : Defines the status of the code value in the scheme and, if present, MUST be one of:
  * "Active" : In current use
  * "Deprecated" : still valid but should not be used in new implementations - will return a warning if used.
  * "Obsolete" : was historically valid but must not be used any longer - will return an error if used.

## Context Association

Stuff about linking to the processID

## Validation API behaviour

Define standard error structure for document validation API response if a code is invalid.

## Licence

Copyright (c) 2016 the Editor and Contributors. All rights reserved.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see [http://www.gnu.org/licenses](http://www.gnu.org/licenses).


## Change Process

This document is governed by the [2/COSS](http://rfc.unprotocols.org/spec:2/COSS/) (COSS).


## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

