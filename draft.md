---
coding: utf-8

title: The Deprecation HTTP Header
docname: draft-dalal-deprecation-header-00
category: std

stand_alone: yes
pi: [toc, tocindent, sortrefs, symrefs, strict, compact, comments, inline]

author:
  -
    ins: S. Dalal
    name: Sanjay Dalal
    email: sanjay.dalal@cal.berkeley.edu
    uri: https://github.com/sdatspun2
  -    
    ins: E. Wilde
    name: Erik Wilde
    email: erik.wilde@dret.net
    uri: http://dret.net/netdret
  
normative:

informative:
    
    Deprecation:
       target: http://openjdk.java.net/jeps/277
       title: JEP 277 - Enhanced Deprecation
       author:
       -
           ins: S. Marks
           name: Stuart Marks
       date: 2017         
      
--- abstract

The HTTP Deprecation response header can be used to signal to consumers of a URI-identified resource that the use of the resource has been deprecated. Additionally, the deprecation link relation can be used to link to a resource that provides additional context for the deprecation, and possibly ways in which clients can find a replacement for the deprecated resource.


--- middle



# Introduction

{{Deprecation}} of a URI-identified resource is a technique to communicate information about the life cycle of a resource: to encourage client applications to migrate away from the resource, to discourage applications from forming new dependencies on the resource, and to inform developers of the risks of continuing dependence upon the resource. The act of deprecation does not change any behavior of the resource. It just informs the clients of the fact. 

Deprecation HTTP response header field MUST be used to convey this fact at runtime to the clients. This header could carry additional information such as since when the deprecation is in effect, what is the alternate(s) if any, and possibly when the deprecated resource would be unreachable (or sunset). Alternates of a resource could be similar resource(s) or a later version of the same resource.

##  Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 {{!RFC2119}} {{!RFC8174}} when, and only when, they appear in all capitals, as shown here.

This specification uses the Augmented Backus-Naur Form (ABNF) notation of {{!RFC5234}} and includes, by reference, the "token" rule, the "quoted-string" rule and the #rule extension hat allows for compact definition of comma-separated lists using a '#' operator (similar to how the '*' operator indicates repetition), HTTP-date rule as defined within Sections 3.2.6 and 7 of {{!RFC7230}} and Section 7.1.1 of {{!RFC7231}}.

The following core rules are included by reference, as defined in {{!RFC5234}, Appendix B.1: ALPHA (letters), CTL (controls), DIGIT (decimal 0-9), DQUOTE (double quote), OCTET (any 8-bit sequence of data), SP (space), and VCHAR (any visible [USASCII] character).

As a convention, ABNF rule names prefixed with "obs-" denote "obsolete" grammar rules that appear for historical reasons.

# The Deprecation HTTP Response Header Field

The `Deprecation` HTTP response header field allows a server to communicate to a client that the URI-identified resource in context of the message is deprecated. It could also provide information that the resource is deprecated since which version and optionally when it is expected to become unresponsive. 

## Syntax

The `Deprecation` response header contains the header name "Deprecation" followed by a ":" and a property(s). Each property consists of a name-value-pair. Servers SHOULD NOT send Deprecation headers that fail to conform to the following grammar:

     deprecation-header               = "Deprecation:" SP deprecation-property-list
     deprecation-property-list        = 1#3deprecation-property
     deprecation-property             = deprecation-property-name "=" deprecation-property-value
     deprecation-property-name        = token
     token                            = <token, defined as in [RFC7230], Section 3.2.6>     
     deprecation-property-value       = deprecation-value-octet / quoted-string / HTTP-date
     deprecation-property-value-octet = %x21 / %x23-2B / %x2D-3A / %x3C-5B / %x5D-7E
                                      ; US-ASCII characters excluding CTLs,
                                      ; whitespace DQUOTE, comma, semicolon,
                                      ; and backslash
     quoted-string                    = <quoted-string, as defined in [RFC7230], Section 3.2.6>
     HTTP-date                        = <HTTP-date, as defined in [RFC7231], Section 7.1.1>    


Note that some of the grammatical terms above reference documents that use different grammatical notations than this document (which uses ABNF from {{!RFC5234}}).
   
To maximize compatibility with user agents, servers that wish to store arbitrary data in a deprecation-property-value SHOULD encode that data, for example, using Base64 {{!RFC4648}}.
   
Servers SHOULD NOT include more than one `Deprecation` header field in the same response. If a server sends multiple responses containing `Deprecation` headers concurrently to the user agent (e.g., when communicating with the user agent over multiple sockets), these responses create a "race condition" that can lead to unpredictable behavior. 


The value of `Deprecation` header field could consist of at most 3 standard pairs: `date`, `version` and `sunset` as shown below. Either `version` or `date` is REQUIRED, `sunset` is OPTIONAL.

    Deprecation: version="version", date="date", sunset="sunset date"
    

### Version

The value of the `version` property, if present, could be the deprecated version of the resource. For resources that use date-based versioning scheme, the value would be accordingly. 

Following example indicates that the version v1 of the resource in context is deprecated.

    Deprecation: version="v1"
    
Following example shows that the version 2018-11-08 (November 8, 2018) of the resource in context is deprecated. Here the versioning scheme used is date-based.   

    Deprecation: version="2018-11-08" 

### Date

The value of `date` property, if present, would be the date when resource was deprecated. It would be in the form of an HTTP-date timestamp, as defined in Section 7.1.1.1 of {{!RFC7231}}. 

Following example shows that the resource in context is deprecated on Friday, November 11, 2018 at 23:59:59 GMT.

    Deprecation: date="Fri, 11 Nov 2018 23:59:59 GMT"

Date could be in future too. If the value of `date` is in future, it means that the resource will be deprecated on the given date in future.

### Sunset (optional)

The value for `sunset` property would be an HTTP-date timestamp, as defined in Section 7.1.1.1 of {{!RFC7231}}. This information provided as a hint and allows a service to communicate the fact that a resource is expected to become unresponsive at a specific point in time.

Following example indicates that the resource in context has been deprecated since version v2 and its sunset date is Friday, November 11, 2020 at 23:59:59 GMT.

    Deprecation: version="v2", sunset="Fri, 11 Nov 2020 23:59:59 GMT"
    
Following example shows that the resource in context has been deprecated since Friday, November 11, 2018 at 23:59:59 GMT and its sunset date is Friday, November 11, 2020 at 23:59:59 GMT.

    Deprecation: date="Fri, 11 Nov 2018 23:59:59 GMT", sunset="Fri, 11 Nov 2020 23:59:59 GMT"    
    




## Recommend Replacement

`Link` {{!RFC8288}} header could be used in addition to the `Deprecation` header to recommend the client application about available alternates to the deprecated resource. Following relation types as defined in {{!RFC5988}} are RECOMMENDED to use for the purpose.

* `successor-version`: Points to a resource containing the successor version. {{!RFC5829}}
* `latest-version`: Points to a resource containing the latest (e.g., current) version. {{!RFC5829}}
* `alternate`: Designates a substitute. [W3C.REC-html401-19991224]

Following example provides link to the successor version of the v1 version of `customer` resource that is deprecated.

    Deprecation: version="v1"
    Link: <https://api.example.com/v2/customers>; rel="successor-version"
    
This example provides link to an alternate resource to the `customer` resource that is deprecated.    

    Deprecation: version="2018-11-11"
    Link: <https://api.example.com/v1/clients>; rel="alternate"


# The Deprecation Link Relation Type

In addition to the Deprecation HTTP header, the server could use a `Link` header(s) to communicate to the client where to find more information about deprecation of the resource in context. This information could be in the form of documentation of the resource including details about deprecated aspects of the resource or the deprecation policy of the resource provider or both or something relevant.
 
## Documentation

For a URI-identified resource, deprecation could involve one or more parts of request, response or both. These parts could be: 

* URI - deprecation of one ore more query parameter(s) or path element(s)
* method - HTTP method for the resource is deprecated
* request header - one or more HTTP request header(s) is deprecated
* response header - HTTP response header(s) is deprecated
* request body - request body contains one or more deprecated element(s)
* response body - response body contains one or more deprecated element(s)

The purpose of the `Deprecation` header is to provide just enough "hints" about the deprecation to the client application developer. It is safe to assume that on reception of the `Deprecation` header, the client developer would look up documentation related to the resource including its deprecation semantics. 

In order to improve the client developer's experience, the resource developer could provide a link to the documentation using a `Link` header  with relation type `deprecation` as shown below. 

    Deprecation: version="v1"
    Link: <https://developer.example.com/v1/customers>; rel="deprecation" type="text/html"

where content at `https://developer.example.com/v1/customers` is properly annotated to show deprecation of the relevant parts of the `customers` resource.


## Policy

To inform the client application developer of the deprecation policy, the resource provider could use `deprecation` relation type as shown below.

    Deprecation: version="v1"
    Link: <https://developer.example.com/deprecation>; rel="deprecation" type="text/html"
  
where deprecation policy of the resource provider `example.com` is described at `https://developer.example.com/deprecation`.


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
    
    Name: PayPal API Guidelines
    
    Licensing: OpenSource (https://www.paypal-engineering.com/2017/09/)
    
    Description: PayPal core capabilities are available from a 
    platform of discoverable, well-encapsulated, reusable API-driven 
    products. PayPal as a Service (PPaaS) is a micro service platform 
    offering a portfolio of 150+ RESTful APIs for use by internal and 
    external developers to access several PayPal capabilities. These 
    APIs are used by applications including but not limited to 
    build.com, Uber, eBay, Venmo, Braintree, Samsung, and numerous 
    internal PayPal applications. 
    
    Maturity: Production (internal and external APIs). 
    
    Coverage: HTTP Header for deprecation
    
    Contact: Nikhil Kolekar (nikhil.kolekar@gmail.com)
    

# Security Considerations

The content of a `Link` header field is not secure, private or integrity-guaranteed, and due caution should be exercised when using it. Use of Transport Layer Security (TLS) with HTTP ({{!RFC2818}} and {{!RFC2817}}) is currently the only end-to-end way to provide such protection.

The suggested `Link` header fields make extensive use of IRIs and URIs. See {{!RFC3987}} for security considerations relating to IRIs. See {{!RFC3986}} for security considerations relating to URIs. See {{!RFC2616}} for security considerations relating to HTTP headers.

Applications that take advantage of typed links should consider the attack vectors opened by automatically following, trusting, or otherwise using links gathered from the HTTP headers. In particular, Link headers that use the `successor-version`, `latest-version` or `alternate` relation types should be treated with due caution. See {{!RFC5829}} for security considerations relating to these link relation types.
   


# Example

Just deprecation header without any Link headers.

    Deprecation: version="v1"
    
Deprecation header with link to the successor version.

    Deprecation: version="v1"
    Link: <https://api.example.com/v2/customers>; rel="successor-version"
    
    
Deprecation header with links for the successor version and for the API developer's deprecation policy. Also, it shows `sunset` date for the deprecated version (v1).
    
    Deprecation: version="v1", sunset="Fri, 11 Nov 2020 23:59:59 GMT"
    Link: <https://api.example.com/v2/customers>; rel="successor-version"
    Link: <https://developer.example.com/deprecation>; rel="deprecation"


--- back


# Acknowledgments
{:numbered="false"}

The authors would like to thank Mark Nottingham, Nikhil Kolekar, ... for review of this specification. The authors take all responsibility for errors and omissions.


