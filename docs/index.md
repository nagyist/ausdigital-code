**[Back to Ausdigital.org](http://ausdigital.org/)**

# UBL JSON Codes Lists Specification

 * ![raw](http://rfc.unprotocols.org/spec:2/COSS/raw.svg)
 * Editor: Steve Capell
 * Contributors: 

## Introduction

This specification defines a JSON representation for standard code lists (eg the ISO-3166 coutnry code list) and also for context specific subsets and extensions.  

The JSON code list representation and standard API definition provides a alternative to the UBL [Genericode](https://docs.oasis-open.org/codelist/cs-genericode-1.0/doc/oasis-code-list-representation-genericode.html) and [Context/Value Association](http://docs.oasis-open.org/codelist/cs01-ContextValueAssociation-1.0/doc/context-value-association.html) specifications (and the associated XSLT based runtime validation framework) for implementers that prefer a REST/JSON model.

## JSON representation

The JSON representation for standard coe lists is as defined below.

```
{
   "CodeList":{
       "ListURI":"https://github.com/ausdigital/code-lists/blob/master/codes/contexts/PaymentMeansCode-context01.json",
       "SchemeIdentification":{
          "listSchemeURI":"urn:un:unece:uncefact:codelist:standard:UNECE:PaymentMeansCode:D10B",
          "ListID":"UNECE-4461",
          "listAgencyID":"UNECE",
          "listAgencyName":"United Nations Economic Commission for Europe",
          "ListName":"PaymentMeansCode",
          "listVersionID":"D10B"},
       "UsageContext":{
          "ProcessID":["urn:resources.digitalbusinesscouncil.com.au:dbc:einvoicing:ver1.0"],
       "Codes":[
          {"Code":"1","Name":"Instrument not defined"},
          {"Code":"2","Name":"Automated clearing house credit"},
          ...
          {"Code":"100","Name":"BPay"},
          ...],
     }
}   
```

 * The "ListURI" MUST resolve to the web resource that is the the code list JSON file.  For Ausdigital framework codes the path will always be to the /codes folder in this repository as in the example above.
 * The "listSchemeURI" is a canonical identifier issued by the code list management agency.  In the example above that is the UN/ECE.
 * The "SchemeIdentification" elements map exactly to the CCTS properties for the code list and SHOULD be used to populate attributes when [mapping documents](https://github.com/ausdigital/ubl-json/blob/master/docs/TransformationAPI.md) from JSON to UBL XML.
 * The "UsageContext" element is an array of process identifiers that represent the rule base a specific UBL implementation such as the DBC e-invoicing framework.  This element carries the same values as:
    * The "ProcessIdentifier" element in a DCP service metadata record.
    * The "customizationID" element in a UBL instance document.  
 * The "Codes" element MUST contain at least one object representing one code in the scheme.
 * The properties of each object MUST conform to the code property terms defined below and MAY include other properties specific to the coding scheme.


## Code property terms

The {Codes} object array contains the list of actual codes and proerties.  The property names MUST comply with the collowing guidance:

 * "Code" : required : contains a short code value (eg "AU") that must be unique within the scheme and must not contain spaces.
 * "Name" : required : contains a descriptive name for the code value (eg "Australia")
 * "Description" : optional : contains a richer description of the code meaning or interpretation.
 * "Status" : optional : Defaults to "Active" : Defines the status of the code value in the scheme and, if present, MUST be one of:
    * "Active" : In current use
    * "Deprecated" : still valid but should not be used in new implementations - will return a warning if used.
    * "Obsolete" : was historically valid but must not be used any longer - will return an error if used.
  * The code object MAY have any number of additional property terms.

## UBL core code lists

**[UBL core code lists repository](https://github.com/ausdigital/code-lists/tree/master/codes/core)**

All the code lists in the core directory are simply JSON representations of the corresponding UBL code list which itself is usually based on an ISO or UN/ECE code list.

## Context specific lists

**[DBC context specific code lists repository](https://github.com/ausdigital/code-lists/tree/master/codes/contexts)**

Code lists often need to be restricted or extended for specific business contexts.  For example, in the Australian context, it may be useful to remove most of the European specific payment means codes from the standard UBL code list and to add a code for BPAY.  
  
 * The context specific lists are maintained in the /contexts path and MUST be a copy of a code list in the /core path with only the following changes:
    * Addition of a suffix "contextnn" to the file name - for example PaymentMeansCode-2.1-context01.json
    * "ListURI" element to reflect the new code list URL.
    * A list of ProcessID references as shown in the example above.  These MUST match the "customizationID" field contents in the corresponding buisness documents that will use the context specific code list.
    * New codes MAY added or existing codes MAY be removed from the object array of codes as needed for the business context - but existing codes MUST NOT be redefined.
  
To create a context specific code list, clone this repository, create the new code list, notify the working group via the slack channel, and issue a pull request.

## Identifier Scheme Lists

**[identifier scheme code lists repository](https://github.com/ausdigital/code-lists/tree/master/codes/identifiers)** 

This specification introduces an additional type of code list that is a reference list of identifier schemes.  The purpose of these lists is to deliver consistency in the UBL representation of identifier schemes like the ABN as a Party identification.  The code lists also support lossless transformation between simple JSON elements like "ABN":"34132141612" and the full CCTS compliant UBL XML representation by including the CCTS Scheme identification information with each scheme code. 

```
  "CodeList": {
    "ListURI": "https://github.com/ausdigital/code-lists/tree/master/codes/identifiers/PartyIdentifiers-dbc.01.json",
    "SchemeIdentification": {
      "listAgencyName": "Digital Business COuncil",
      "ListID": "PartyID",
      "listSchemeURI": "urn:codes.digitalbusinesscouncil.com.au:partyIdentifiers:ver1.0",
      "ListName": "Party Identifier Schemes",
      "listAgencyID": "Uigitalbusinesscouncil.com.au",
      "listVersionID": "1.0"
    },
    "Codes": [
      {
        "Code": "ABN",
        "Name": "IAustralian Business Number",
        "SchemeIdentification":{
          "schemeID":"urn:oasis:names:tc:ebcore:partyid-type:iso6523:0151",
          "schemeName":"ABN",
          "schemeAgencyID":"ato.gov.au",
          "schemeAgencyName":"Australian Taxation Office",
          "schemeVersionID":"1.0",
          "schemeDataURI":"http://abr.business.gov.au/abrxmlsearchRPC/Forms/SearchByABNv201408.aspx",
          "schemeURI":"urn:oasis:names:tc:ebcore:partyid-type:iso6523:0151"
        },
      },
      {
        "etc":" "
      },
    ]
  }
}
```

## Validation API behaviour

The document validation API behaviour is designed to allow codes used within messages to be validated against a context specific code list where appropriate but to default to the standard code list where a restricted version is not defined for a given process.  The [validation API](https://github.com/ausdigital/ubl-json/blob/master/docs/ValidationAPI.md) SHALL

 * For each code data type in the document, validate the code against the code list identified by the SchemeURI (eg "urn:un:unece:uncefact:codelist:standard:UNECE:PaymentMeansCode:D10B").
 * If the "customizationID" element is present in the document, then use the context specific code list with a matching identifier in the "ProcessID" array.  Otherwise, use the standard code list.
 * There are serveral error conditions.  The error code and error message are described in the table below and MUST be inserted into the standard error structure defined in the The [validation API](https://github.com/ausdigital/ubl-json/blob/master/docs/ValidationAPI.md) specification.

|Error Code|Error Message|
|----------|-------------|
|Code-01|The value {code value} for data item {data element name} cannot be validated because there is no code list for scheme {schemeURI}.|
|Code-02|The value {code value} for data item {data element name} is not valid in {list name} code list.|
|Code-03|The value {code value} for data item {data element name} is not valid in {list name} restricted code list for context {ProcessID}.|

## Licence

Copyright (c) 2016 the Editor and Contributors. All rights reserved.

This Specification is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This Specification is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program; if not, see [http://www.gnu.org/licenses](http://www.gnu.org/licenses).


## Change Process

This document is governed by the [2/COSS](http://rfc.unprotocols.org/spec:2/COSS/) (COSS).


## Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

