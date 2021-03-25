---
title: A proposed standard which allows websites and services to disclose information about their usage of analytics software and user tracking.
abbrev: analytics.txt
docname: draft-offen-analyticstxt-latest
category: info

ipr: trust200902
area: General
workgroup: Network Working Group
keyword: Internet-Draft

stand_alone: yes
smart_quotes: no
pi: [toc, sortrefs, symrefs]

author:
 -
    ins: F. Ring
    name: Frederik Ring
    organization: Offen
    email: frederik.ring@gmail.com
 -
    ins: H. Niefeld
    name: Hendrik Niefeld
    organization: Offen
    email: hello@niefeld.com

normative:
informative:



--- abstract

Privacy has become an important feature for users of websites and services. This document propopes a well-defined way for websites and services to signal their usage of analytics and tracking software to users and tooling used by users. analytics.txt aims to be an elaborate standard that describes the usage of analytics and tracking in a non-biased way that is understandable both for a non-technical audience, but also useful for consumption by tools and software.

--- middle

# Introduction

TODO Introduction

# Conventions and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{!RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.

The term "implementors" refers to the providers of services and websites that wish to use an analytics.txt file.

# Specification

This document defines a text file format that can be used by implementors to signal information about their usage of analytics software to both users and other software.

By convention, this file is called analytics.txt. Its location and scope are described in section 3.

This text file contains multiple fields with different values.  A field contains a "name" which is the first part of a field all the way up to the colon (for example: "Contact:") and follows the syntax
defined for "field-name" in section 3.6.8 of {{?RFC5322}}.  Field names are case-insensitive (as per section 2.3 of {{?RFC5234}}).  The "value" comes after the field name and follows the syntax defined for "unstructured" in section 3.2.5 of {{?RFC5322}}.  The file MAY also contain blank lines.

A field MUST always consist of a name and a value (for example: "Contact: mailto:data-protection@example.com).  An analytics.txt file can have an unlimited number of fields.  Each field MUST appear on its own line.  Unless specified otherwise by the field definition, multiple values MUST be chained together for a single field (for example: "Compliance: gdpr, ccpa") using the "," (%x2c). Unless otherwise indicated in a definition of a particular field, a field MAY NOT appear multiple times.

Implementors should be aware that some of the fields may contain URIs using percent-encoding (as per section 2.1 of {{?RFC3986}}).

Implementors SHOULD aim for creating an analytics.txt file that is easy to understand by non-technical audiences.

## Comments

Any line beginning with the "#" (%x23) symbol MUST be interpreted as a comment.  The content of the comment may contain any ASCII or Unicode characters in the %x21-7E and %x80-FFFFF ranges plus the tab (%x09) and space (%x20) characters.

Example:

```
# A comment
```

Implementors SHOULD make deliberate use of comments to make an analytics.txt file more accessible for non-technical audiences.

## Line Separators

Every line MUST end either with a carriage return and line feed characters (CRLF / %x0D %x0A) or just a line feed character (LF / %x0A).

## Extensibility

Like many other formats and protocols, this format may need to be extended over time to fit the ever-changing landscape of the Internet. Special attention is required for defining the allowed values in enumerations to ensure they are a. extensible and b. do not become obsolete too quickly.

## Field Definitions

Field names are case-insensitive, yet implementors SHOULD use the capitalized style used in this document for consistency.

Field values are case-insensitive. In case a field contains an enumeration, implementors MUST refer to the allowed values given by the specification.

### Contact

This REQUIRED field holds an email address that can be used for contacting the provider of the analytics.txt file. It shall be used for inquiries about the correctness and additions to the data provided in the file.

Example:

```jsx
Contact: mailto:hioffen@posteo.de
```

### Collects

This REQUIRED multi-value field indicates which potentially privacy relevant user specific data is being collected or used in session identification. These MUST also be specified if not persisted as-is, but also when stored in a hashed and/or combined form.

Values MUST be one of the following:

- ip-address
- geographic-location
- ua-string
- fingerprint
- device-type
- url
- referrer
- visit-duration
- custom-events
- session-recording

Example:

```
Collects: url, device-type, referrer
```

### Stores

This REQUIRED multi-value field indicates whether data is persisted on the client during the collection of analytics data and declares the browser features used for doing so. If no data is being persisted, the value `none` MUST be used.

Values MUST be one of the following:

- first-party-cookies
- third-party-cookies
- local-storage
- etag
- other
- none

Example:

```
Stores: 1st-party-cookies, local-storage
```

### Uses

This REQUIRED multi-value field indicates the technical implementation details for how analytics data is being collected.

Values MUST be one of the following:

- javascript
- pixel
- server-side
- logs
- other

Example:

```
Uses: script
```

### Allows

This REQUIRED multi-value field discloses information about whether user consent is being acquired before collecting analytics data, and if it is possible for users to opt out of the collection of usage data. Regulations about user consent do not apply to this field.

Values MUST be one of the following:

- opt-in
- opt-out
- none

Example:

```
Allows: opt-in, opt-out
```

### Retains

This REQUIRED single-value field indicates the duration for which the analytics data is being stored before being delete. The value is a duration as defined in {{?RFC 3339}}. Implementors SHOULD add a comment providing a human readable value.

Example:

```
# Data is retained for twelve months
Retains: P12M
```

### Session

This OPTIONAL, RECOMMENDED single-value field indicates the coverage in session tracking. It MUST contain a single value only.

The value MUST be one of the following:

- anonymous (each event is anonymous)
- session (a user can be identified throughout a single browser session, session is defined as per browser session)
- user (a user can be identified across multiple sessions)

Example:

```
Session: user
```

### Visibility

This OPTIONAL, RECOMMENDED multi-value field indicates whether the website provides a way for users or the general public to access data.

Values MUST be one of the following:

- user
- public

Example:

```
Visibility: user
```

### Compliance

This OPTIONAL field indicates conformance with certain regulations and legislations.

Example values are:

- gdpr
- hiipa
- ccpa

Example:

```
Compliance: gdpr, ccpa
```

### Vendors

This OPTIONAL field indicates which software is being used for collecting analytics.

Example values are:

- google-analytics
- plausible
- offen
- hotjar
- matomo

Example:

```
Vendors: offen, hotjar
```

## Example of an analytics.txt file

```
# analytics.txt file for https://www.analyticstxt.org
Contact: mailto:hioffen@posteo.de

Collects: url, referrer, device-type
Stores: first-party-cookies, local-storage
# Data is encrypted end-to-end
Uses: javascript
# Users can also delete their usage data only without opting out
Allows: opt-in, opt-out
# Data is retained for 6 months
Retains: P6M

# Optional fields
Session: user
Visibility: user
Compliance: gdpr
Vendors: offen
```

# Location of the analytics.txt file

By default, an analytics.txt file SHOULD be placed in the ".well-known" path as per {{!RFC8615}} of a domain name or IP address. In case implementors are unable to meet this requirement, two other options are available.

Implementors MAY signal the location of an analytics.txt file in the context of a HTML document using a link element of rel "analytics"

Example:

```html
<link rel="analytics" href="https://example.com/resources/analytics.txt">
```

In addition to that implementors MAY send an HTTP header of `X-Analytics-Txt` with a response, sending the URI of the applicable file.

Example:

```
X-Analytics-Txt: https://example.com/resources/analytics.txt
```

In case multiple of these signals are being used, the precedence taken is:

- X-Analytics-Txt Header
- link element
- .well-known location

## Scope of a file

An analytics.txt file MUST only apply to the domain or IP address in the URI used to retrieve it, not to any of its subdomains or parent domains.  An analytics.txt file MAY also apply to products and services provided by the organization publishing the file.

# Security Considerations

## Incorrect or stale information

TODO incorrect information

## Spam

TODO Spam

# IANA Considerations

This document has no IANA actions.

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
