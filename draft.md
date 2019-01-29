---
coding: utf-8

title: The Deprecation HTTP Header
docname: draft-dalalwilde-deprecation-header-00
category: std

stand_alone: yes
pi: [toc, tocindent, sortrefs, symrefs, strict, compact, comments, inline]

author:
  -
    ins: S. Dalal
    name: Sanjay Dalal
    email: sanjay.dalal@cal.berkeley.edu
  -    
    ins: E. Wilde
    name: Erik Wilde
    email: erik.wilde@dret.net
    uri: http://dret.net/netdret
  
normative:

informative:
    REST:
        target: http://www.ics.uci.edu/~fielding/pubs/dissertation/fielding_dissertation.pdf
        title: Architectural Styles and the Design of Network-based Software Architectures
        author:
        -
            ins: R. Fielding
            name: Roy Thomas Fielding
            org: University of California, Irvine
        date: 2000
        seriesinfo:
            "Ph.D.": "Dissertation, University of California, Irvine"
        format:
            PDF: http://www.ics.uci.edu/~fielding/pubs/dissertation/fielding_dissertation.pdf     
    Deprecation:
       target: http://openjdk.java.net/jeps/277
       title: JEP 277 - Enhanced Deprecation
       author:
       -
       	ins: S. Marks
       	name: Stuart Marks
       date: 2017         
      
--- abstract

The HTTP Deprecation header field can be used to signal to consumers of a URI-identified resource that the use of the resource has been deprecated. Additionally, the deprecation link relation can be used to link to a resource that provides additional context for the deprecation, and possibly ways in which clients can find a replacement for the deprecated resource.


--- middle



# Introduction

{{Deprecation}} of a URI-identified [resource](#resource) is a technique to communicate information about the life cycle of a resource: to encourage client applications to migrate away from the resource, to discourage applications from forming new dependencies on the resource, and to inform developers of the risks of continuing dependence upon the resource. The act of deprecation does not change any behavior of the resource. It just informs the clients of the fact and optionally provides additional information such as since when the deprecation is in effect, what is the alternate(s) if any. Alternates could be similar resource(s) or later version of the same resource.

## Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 {{!RFC2119}} {{!RFC8174}} when, and only when, they appear in all capitals, as shown here.

## Terminology

### Resource

The key abstraction of information in {{REST}} is a resource. Any information that can be named can be a resource: a document or image, a temporal service (e.g. "today's weather in San Francisco"), a collection of other resources, a non-virtual object (e.g. a person), and so on. A resource is a conceptual mapping to a set of entities, not the entity that corresponds to the mapping at any particular point in time. More precisely, a resource R is a temporally varying membership function `MR(t)`, that for time `t` maps to a set of entities, or values, that are equivalent. The values in the set may be resource representations and/or resource identifiers. 


# The Deprecation HTTP Response Header


## Syntax

    Deprecation: since=<date or version>; sunset=<date>;

To Erik, Mark: We could use Sunset header if it is going to be approved. Else we would use a key named `sunset` in addition to `since`.

**Since**

The value of `since` could be the resource (or API) version since when this resource was deprecated. For APIs, that use date-based versioning scheme, the value could be accordingly.

Following example indicates that the resource in context has been deprecated since version `v2`.

    Deprecation: since=v2
    
Following example shows that the resource in context is deprecated since 2018-11-08 (November 8, 2018).    

    Deprecation: since=2018-11-08 


**Sunset (optional)**

The value for `sunset` would be an HTTP-date timestamp, as defined in Section 7.1.1.1 of {{!RFC7231}}. This information provided as a hint allows a service to communicate the fact that a resource is expected to become unresponsive at a specific point in time.


Following example indicates that the resource in context has been deprecated since version `v2` and sunset date for the deprecated version is Fri, 11 Nov 2020 23:59:59 GMT.

	Deprecation: since=v2; sunset=Fri, 11 Nov 2020 23:59:59 GMT

## Associated Link Header

Link header as defined in {{!RFC8288}} could be used in addition to the Deprecation header to inform the applications about alternates to the deprecated resource as needed. Following relationship types as defined in {{!RFC5988}} might be appropriate to use in such cases.

* successor-version: Points to a resource containing the successor version. {{!RFC5829}}
* latest-version: Points to a resource containing the latest (e.g., current) version. {{!RFC5829}}
* alternate: Designates a substitute. [W3C.REC-html401-19991224]

Following example provides link to the successor version of the v1 version of `customer` resource that is deprecated.

    Deprecation: since=v2
    Link: https://api.example.com/v2/customers; rel=successor-version
    
This example provides link to an alternate resource to the `customer` resource that is deprecated.    

    Deprecation: since=2018-11-11
    Link: https://api.example.com/v1/clients; rel=alternate


# The Deprecation Link Relation Type



    


# IANA Considerations

## The Deprecation Response Header Field

The `Deprecation` response header should be added to the permanent registry of message header fields (see {{!RFC3864}}), taking into account the guidelines given by HTTP/1.1 {{!RFC7231}}.

	Header Field Name: Deprecation
	
	Applicable Protocol: Hypertext Transfer Protocol (HTTP)

	Status: Informational
	
	Author/Change controller: IETF
	
	Specification document(s): RFC XXXX


## The Deprecation Link Relation Type

The `deprecation` link relation type should be added to the permanent registry of link relation types according to Section 4.2 of {{!RFC8288}}:


	Relation Name: deprecation
	
	Description: ...
	
	Reference: RFC XXXX



# Implementation Status

Note to RFC Editor: Please remove this section before publication.

This section records the status of known implementations of the protocol defined by this specification at the time of posting of this Internet-Draft, and is based on a proposal described in {{?RFC6982}}.  The description of implementations in this section is intended to assist the IETF in its decision processes in progressing drafts to RFCs.  Please note that the listing of any individual implementation here does not imply endorsement by the IETF. Furthermore, no effort has been spent to verify the information presented here that was supplied by IETF contributors.  This is not intended as, and must not be construed to be, a catalog of available implementations or their features.  Readers are advised to note that
other implementations may exist.

According to RFC 6982, "this will allow reviewers and working groups to assign due consideration to documents that have the benefit of running code, which may serve as evidence of valuable experimentation and feedback that have made the implemented protocols more mature. It is up to the individual working groups to use this information as they see fit".


	Organization: PayPal
	
	Name: PayPal as a Service, PayPal's API Style Guide - https://github.com/paypal/api-standards/blob/master/api-style-guide.md, deprecation https://github.com/paypal/api-standards/blob/master/api-style-guide.md#runtime 
	
	Licensing: OpenSource (https://www.paypal-engineering.com/2017/09/)
	
	Description: PayPal began the journey to transform all of PayPal’s core capabilities into a platform of discoverable, well-encapsulated, reusable API-driven products few years back. PayPal as a Service (PPaaS) is a micro service platform offering a portfolio of 150+ RESTful APIs for use by internal and external developers to access several PayPal capabilities. These APIs are used by applications including but not limited to build.com, Uber, eBay, Venmo, Braintree, Samsung, and numerous internal PayPal applications. 
	
	Maturity: Production (internal and external APIs). 
	
	Coverage: HTTP 1.1, HTTP Header
	
	Contact: Nikhil Kolekar (nikhil.kolekar@gmail.com)
	

# Security Considerations

TODO Security



# Example

Just deprecation header without any Link headers.

    Deprecation: since=v2
    
Deprecation header with link to the successor version.

    Deprecation: since=v2
    Link: https://api.example.com/v2/customers; rel=successor-version
    
    
Deprecation header with links for the successor version and for the API developer's depercation policy. Also, it shows `sunset` date for the deprecated version (`v1`);
    
    Deprecation: since=v2; sunset=Fri, 11 Nov 2020 23:59:59 GMT
    Link: https://api.example.com/v2/customers; rel=successor-version
    Link: https://developer.example.com/deprecation; rel=deprecation


--- back


# Acknowledgments
{:numbered="false"}

 The authors take all responsibility for errors and omissions.


