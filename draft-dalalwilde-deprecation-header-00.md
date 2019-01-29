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

{{Deprecation}} of a URI-identified resource [resource](#resource) is a technique to communicate information about the life cycle of a resource: to encourage applications to migrate away from the resource, to discourage applications from forming new dependencies on the resource, and to inform developers of the risks of continuing dependence upon the resource.


## Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{!RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.

## Terminology

### Resource

The key abstraction of information in {{REST}} is a resource. Any information that can be named can be a resource: a document or image, a temporal service (e.g. "today's weather in San Francisco"), a collection of other resources, a non-virtual object (e.g. a person), and so on. A resource is a conceptual mapping to a set of entities, not the entity that corresponds to the mapping at any particular point in time. More precisely, a resource R is a temporally varying membership function `MR(t)`, that for time `t` maps to a set of entities, or values, that are equivalent. The values in the set may be resource representations and/or resource identifiers. 



# The Deprecation HTTP Response Header

## Syntax

    Deprecation: since=<date or version>; sunset=<date>;

To Erik, Mark: We could use Sunset header if it is going to be approved. Else we would use a key named `sunset`.

**Since**

The value of `since` could be the resource (or API) version since when this resource was deprecated. For APIs, that use date-based versioning scheme, the value could be accordingly.

Following example indicates that the resource in context has been deprecated since version `v2`.

    Deprecation: since=v2
    
Following example shows that the resource in context is deprecated since 2018-11-08 (November 8, 2018).    

    Deprecation: since=2018-11-08 


**Sunset (optional)**



The value for `sunset` would be an HTTP-date timestamp, as defined in Section 7.1.1.1 of {{!RFC7231}}. This information provided as a hint allows a service to communicate the fact that a resource is expected to become unresponsive at a specific point in time.

## With Link Header

Link header as defined in {{!RFC8288}} could be used in addition to the Deprecation header to inform the applications about alternates to the deprecated if applicable. Following relationship types as defined in {{!RFC5988}} might be appropriate to use in such cases.

* successor-version
* latest-version
* alternate

Following example provides link to the successor version of the resource that is deprecated.

    Deprecation: v2
    Link: http://api.example.com/v2/customers; rel=successor-version
    
Following example provides link to an alternate resource to use instead of the called resource that is deprecated.    

    Deprecation: 2018-11-11
    Link: http://api.example.com/v2/clients; rel=alternate


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

# Security Considerations

TODO Security

# Example

# Implementation Status

Note to RFC Editor: Please remove this section before publication.

This section records the status of known implementations of the
protocol defined by this specification at the time of posting of this
Internet-Draft, and is based on a proposal described in {{?RFC6982}}.  The description of implementations in this section is
intended to assist the IETF in its decision processes in progressing
drafts to RFCs.  Please note that the listing of any individual
implementation here does not imply endorsement by the IETF.
Furthermore, no effort has been spent to verify the information
presented here that was supplied by IETF contributors.  This is not
intended as, and must not be construed to be, a catalog of available
implementations or their features.  Readers are advised to note that
other implementations may exist.

According to RFC 6982, "this will allow reviewers and working groups
to assign due consideration to documents that have the benefit of
running code, which may serve as evidence of valuable experimentation
and feedback that have made the implemented protocols more mature.
It is up to the individual working groups to use this information as
they see fit".


	Organization: ...
	
	Name: ...
	
	Licensing: ...
	
	Description: ...




--- back


# Acknowledgments
{:numbered="false"}

 The authors take all responsibility for errors and omissions.


