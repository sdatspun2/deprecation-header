---
coding: utf-8

title: The Deprecation HTTP Header Field
docname: draft-dalal-deprecation-header-01
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
    
    Sunset:
       target: https://tools.ietf.org/html/draft-wilde-sunset-header-11
       title: The Sunset HTTP Header Field
       author:
       -
           ins: E. Wilde
           name: Erik Wilde
       date: 2019

--- abstract

The HTTP Deprecation response header field can be used to signal to consumers of a URI-identified resource that the use of the resource has been deprecated. Additionally, the deprecation link relation can be used to link to a resource that provides additional context for the deprecation, and possibly ways in which clients can find a replacement for the deprecated resource.


--- middle



# Introduction

Deprecation of a URI-identified resource is a technique to communicate information about the lifecycle of a resource. It encourages applications to migrate away from the resource, discourages applications from forming new dependencies on the resource, and informs applications about the risk of continuing dependence upon the resource. 

The act of deprecation does not change any behavior of the resource. It just informs client of the fact that a resource is deprecated. The Deprecation HTTP response header field MAY be used to convey this fact at runtime to clients. The header field can carry additional information such as since when the deprecation is in effect. 

In addition to the Deprecation header field the resource provider can use other header fields to convey additional information related to deprecation. For example, information such as where to find documentation related to the deprecation or what should be used as an alternate and when the deprecated resource would be unreachable, etc. Alternates of a resource can be similar resource(s) or a newer version of the same resource.


##  Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 {{!RFC2119}} {{!RFC8174}} when, and only when, they appear in all capitals, as shown here.

This specification uses the Augmented Backus-Naur Form (ABNF) notation of {{!RFC5234}} and includes, by reference, the "token" rule, DQUOTE (double quote) rule, the SP (space) rule and the "rule" extension that allows for compact definition of comma-separated lists using a '#' operator (similar to how the '*' operator indicates repetition), HTTP-date rule as defined within Sections 3.2.6 and 7 of {{!RFC7230}} and Section 7.1.1 of {{!RFC7231}}.


# The Deprecation HTTP Response Header Field

The `Deprecation` HTTP response header field allows a server to communicate to a client that the URI-identified resource in context of the message is deprecated. It can also provide information that the resource is deprecated since which version. 

## Syntax

The `Deprecation` response header field lists properties describing the deprecation. Each property consists of a name-value-pair. Servers MUST NOT send Deprecation headers that fail to conform to the following grammar:

    deprecation-header = "Deprecation:" SP "version"=vval, "date"=dval, 
                         *( extension )
    extension = property-name "=" property-value
    property-name = DQUOTE token DQUOTE
    token = <token, defined as in [RFC7230], Section 3.2.6>
    vval = property-value
    property-value = DQUOTE *( pchar ) DQUOTE
    pchar = %x23 / %x2B-3A / %x41-5A / %x61-7A / %x7C
          ; US-ASCII characters  
    dval = DQUOTE HTTP-date DQUOTE

Note that some of the grammatical terms above reference documents that use different grammatical notations than this document (which uses ABNF from {{!RFC5234}}).

Servers MUST NOT include more than one `Deprecation` header field in the same response.

The value of `Deprecation` response header field consists of at least one standard property, `date` or `version` as shown below. Either of `version` or `date` is REQUIRED and using both is also allowed.

    Deprecation: version="version", date="date"


### Version

The value of the `version` property, if present, is the version of the resource that is deprecated. The value of `version` is an opaque version identifier. For resources that use date-based versioning scheme, the value accordingly can be a date. 

Following example indicates that the version v1 of the resource in context is deprecated.

    Deprecation: version="v1"

Following example shows that the version 2018-11-08 (November 8, 2018) of the resource in context is deprecated. Here the versioning scheme used is date-based.   

    Deprecation: version="2018-11-08" 

### Date

The value of `date` property, if present, is the date when resource was deprecated. It is in the form of a quoted HTTP-date timestamp, as defined in Section 7.1.1.1 of {{!RFC7231}}. 

Following example shows that the resource context has been deprecated on Friday, November 11, 2018 at 23:59:59 GMT.

    Deprecation: date="Fri, 11 Nov 2018 23:59:59 GMT"

The deprecation date can be in the future. If the value of `date` is in the future, it means that the resource will be deprecated at the given date in future.



# The Deprecation Link Relation Type

In addition to the Deprecation HTTP header field, the server can use links with the "deprecation" link relation type to communicate to the client where to find more information about deprecation of the context.

This information can be in the form of documentation of the resource including details about the deprecation related aspects of the context or the deprecation policy of the context provider or both for example.

This specification places no restrictions on the representation of the deprecation policy. In particular, the deprecation policy may be available as human-readable documentation or as machine-readable description.


## Documentation

For a URI-identified resource, deprecation could involve one or more parts of request, response or both. These parts could be one or more of the following. 

* URI - deprecation of one ore more query parameter(s) or path element(s)
* method - HTTP method for the resource is deprecated
* request header - one or more HTTP request header(s) is deprecated
* response header - HTTP response header(s) is deprecated
* request body - request body contains one or more deprecated element(s)
* response body - response body contains one or more deprecated element(s)

The purpose of the `Deprecation` header is to provide just enough "hints" about the deprecation to the client application developer. It is safe to assume that on reception of the `Deprecation` header, the client developer would look up the resource's documentation in order to find deprecation related semantics. The resource developer could provide a link to the resource documentation using a `Link` header with relation type `deprecation` as shown below. 

    Deprecation: version="v1"
    Link: <https://developer.example.com/v1/customers>; rel="deprecation"
           type="text/html"

where content at `https://developer.example.com/v1/customers` would be annotated to show deprecation of the relevant parts of the `customers` resource.


## Policy

Resource provider would typically document versioning and deprecation policy with the resource documentation. To inform the client application developer of the deprecation policy, the resource provider could use the `deprecation` relation type as shown below.

    Deprecation: version="v1"
    Link: <https://developer.example.com/deprecation>; rel="deprecation" 
           type="text/html"

where deprecation policy of the resource provider `example.com` is described at `https://developer.example.com/deprecation`.


# Recommend Replacement

`Link` {{!RFC8288}} header could be used in addition to the `Deprecation` header to recommend the client application about available alternates to the deprecated resource. Following relation types as defined in {{!RFC8288}} are RECOMMENDED to use for the purpose.

* `successor-version`: Points to a resource containing the successor version. {{?RFC5829}}
* `latest-version`: Points to a resource containing the latest (e.g., current) version. {{?RFC5829}}
* `alternate`: Designates a substitute. [W3C.REC-html401-19991224]

Following example provides link to the successor version of the v1 version of `customer` resource that is deprecated.

    Deprecation: version="v1"
    Link: <https://api.example.com/v2/customers>; rel="successor-version"

This example provides link to an alternate resource to the `customer` resource that is deprecated.    

    Deprecation: version="2018-11-11"
    Link: <https://api.example.com/v1/clients>; rel="alternate"


# Sunset


In addition to the deprecation related information, if the resource provider wants to convey to the client application that the deprecated resource is expected to become unresponsive at a specific point in time, the {{Sunset}} header could be used in addition to the `Deprecation` header.

Following example indicates that the resource in context has been deprecated since version v2 and its sunset date is Friday, November 11, 2020 at 23:59:59 GMT.

    Deprecation: version="v2"
    Sunset: Fri, 11 Nov 2020 23:59:59 GMT

Following example shows that the resource in context has been deprecated since Friday, November 11, 2018 at 23:59:59 GMT and its sunset date is Friday, November 11, 2020 at 23:59:59 GMT.

    Deprecation: date="Fri, 11 Nov 2018 23:59:59 GMT"
    Sunset: Fri, 11 Nov 2020 23:59:59 GMT    



# IANA Considerations

## The Deprecation Response Header Field

The `Deprecation` response header should be added to the permanent registry of message header fields (see {{!RFC3864}}), taking into account the guidelines given by HTTP/1.1 {{!RFC7231}}.

    Header Field Name: Deprecation
    
    Applicable Protocol: Hypertext Transfer Protocol (HTTP)
    
    Status: Standard
    
    Author: Sanjay Dalal <sanjay.dalal@cal.berkeley.edu>, 
            Erik Wilde <erik.wilde@dret.net>
    
    Change controller: IETF
    
    Specification document: this specification, 
                Section 2 "The Deprecation HTTP Response Header Field"



## The Deprecation Link Relation Type

The `deprecation` link relation type should be added to the permanent registry of link relation types according to Section 4.2 of {{!RFC8288}}:

    Relation Type: deprecation
    
    Applicable Protocol: Hypertext Transfer Protocol (HTTP)
    
    Status: Standard
    
    Author: Sanjay Dalal <sanjay.dalal@cal.berkeley.edu>, 
            Erik Wilde <erik.wilde@dret.net>
    
    Change controller: IETF
    
    Specification document: this specification, 
            Section 3 "The Deprecation Link Relation Type"



# Implementation Status

Note to RFC Editor: Please remove this section before publication.

This section records the status of known implementations of the protocol defined by this specification at the time of posting of this Internet-Draft, and is based on a proposal described in {{?RFC7942}}.  The description of implementations in this section is intended to assist the IETF in its decision processes in progressing drafts to RFCs.  Please note that the listing of any individual implementation here does not imply endorsement by the IETF. Furthermore, no effort has been spent to verify the information presented here that was supplied by IETF contributors.  This is not intended as, and must not be construed to be, a catalog of available implementations or their features.  Readers are advised to note that
other implementations may exist.

According to RFC 7942, "this will allow reviewers and working groups to assign due consideration to documents that have the benefit of running code, which may serve as evidence of valuable experimentation and feedback that have made the implemented protocols more mature. It is up to the individual working groups to use this information as they see fit".



Organization: Zapier

Description: Zapier uses two custom HTTP headers named `X-API-Deprecation-Date` and `X-API-Deprecation-Info`

Reference:  https://zapier.com/engineering/api-geriatrics/



Organization: IBM

IBM uses a custom HTTP header named `Deprecated`

Reference: https://www.ibm.com/support/knowledgecenter/en/SS42VS_7.3.1/com.ibm.qradar.doc/c_rest_api_getting_started.html



Organization: Ultipro

Description: Ultipro uses the HTTP `Warning` header as described in Section 5.5 of {{!RFC7234}} with code `299` 

Reference:  https://connect.ultipro.com/api-deprecation



Organization: Clearbit

Description: Clearbit uses a custom HTTP header named `X-API-Warn`

Reference: https://blog.clearbit.com/dealing-with-deprecation/



Organization: PayPal

Description: PayPal uses a custom HTTP header named `PayPal-Deprecated`

Reference: https://github.com/paypal/api-standards/blob/master/api-style-guide.md#runtime



# Security Considerations

The content of a `Link` header field is not secure, private or integrity-guaranteed, and due caution should be exercised when using it. Use of Transport Layer Security (TLS) with HTTP ({{!RFC7230}} is currently the only end-to-end way to provide such protection.

The suggested `Link` header fields make extensive use of IRIs and URIs. See {{!RFC3987}} for security considerations relating to IRIs. See {{!RFC3986}} for security considerations relating to URIs. See {{!RFC7230}} for security considerations relating to HTTP headers.

Applications that take advantage of typed links should consider the attack vectors opened by automatically following, trusting, or otherwise using links gathered from the HTTP headers. In particular, Link headers that use the `successor-version`, `latest-version` or `alternate` relation types should be treated with due caution. See {{?RFC5829}} for security considerations relating to these link relation types.



# Example

Just deprecation header without any Link headers.

    Deprecation: version="v1"

Deprecation header with link to the successor version.

    Deprecation: version="v1"
    Link: <https://api.example.com/v2/customers>; rel="successor-version"

Deprecation header with links for the successor version and for the API developer's deprecation policy. Also, it shows sunset date for the deprecated version (v1).

    Deprecation: version="v1" 
    Sunset: Fri, 11 Nov 2020 23:59:59 GMT
    Link: <https://api.example.com/v2/customers>; rel="successor-version"
    Link: <https://developer.example.com/deprecation>; rel="deprecation"



--- back


# Acknowledgments


The authors would like to thank Mark Nottingham and Nikhil Kolekar for reviewing this specification. 

The authors take all responsibility for errors and omissions.


