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

Privacy has become an important feature for users of websites and services. This document propopes a discoverable way for websites and services to declare their usage of analytics and tracking software to both users and the tooling used by users. analytics.txt aims to be an elaborate standard that describes the usage of analytics and tracking software in a non-biased way that is understandable for a non-technical audience, but also useful for consumption by tools and software.

--- middle

# Introduction

The usage of analytics software and user tracking on websites is becoming an increasingly important factor for users on the internet. Yet, there is no well-defined way of accessing such information in a standardized way. Legislation only covers certain technological implementations, incentivizing software to find workarounds, thus being able hiding their presence from users. Automated audits are limited to aspects that are possible to detect in clients.

This document defines a way to specify the privacy related characteristics of analytics and tracking software. We aim for this information to be consumable both by humans as well as other software. For example, search engines or browser extensions could make use of this data and display information to users.

The file "analytics.txt" is not intended to replace the requirement for complying to certain regulations, but supposed to give insights beyond the scope of these regulations.

## Scope of the term "analytics" in this document

TODO define analytics

# Conventions and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{!RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.

The term "implementors" refers to the providers of services and websites that wish to use an analytics.txt file.

# Specification

This document defines a text file format that can be used by implementors to signal information about their usage of analytics software to both users and software.

By convention, this file is called analytics.txt. Its location and scope are described in {{location}}.

This text file contains multiple fields with different values. A field contains a "name" which is the first part of a field all the way up to the colon (for example: "Author:") and follows the syntax
defined for "field-name" in section 3.6.8 of {{?RFC5322}}. Field names are case-insensitive (as per section 2.3 of {{?RFC5234}}). The "value" comes after the field name and follows the syntax defined for "unstructured" in section 3.2.5 of {{?RFC5322}}. The file MAY also contain blank lines.

A field MUST always consist of a name and a value (for example: "Author: Jane Doe <jane.doe@example.com>").  An analytics.txt file can have an unlimited number of fields.  Each field MUST appear on its own line. Unless specified otherwise by the field definition, multiple values MUST be chained together for a single field (for example: "Compliance: gdpr, ccpa") using the "," (%x2c). Unless otherwise indicated in a definition of a particular field, a field MAY NOT appear multiple times.

Implementors SHOULD aim for creating an analytics.txt file that is easy to understand by non-technical audiences.

## Comments

Any line beginning with the "#" (%x23) symbol MUST be interpreted as a comment. The content of the comment may contain any ASCII or Unicode characters in the %x21-7E and %x80-FFFFF ranges plus the tab (%x09) and space (%x20) characters.

Example:

```
# A comment
```

Implementors SHOULD make deliberate use of comments to make an analytics.txt file more accessible for non-technical audiences.

## Line Separators

Every line MUST end either with a carriage return and line feed characters (CRLF / %x0D %x0A) or just a line feed character (LF / %x0A).

## Extensibility

Like many other formats and protocols, this format may need to be extended over time to fit the ever-changing landscape of the Internet. Special attention is required for defining the allowed values in enumerations to ensure they are a. extendable and b. do not become obsolete too quickly.

## Field Definitions

Field names are case-insensitive, yet implementors SHOULD use the capitalized style used in this document for consistency.

Field values are case-insensitive. In case a field contains an enumeration of multiple values, implementors MUST refer to the allowed values given by the specification.

### Author {#author-field}

This REQUIRED field holds an OPTIONAL author name and a REQUIRED email address providing information about who is responsible for maintaining the contents of the file. The field MUST contain a valid email address which shall be used for inquiries about the correctness and additions to the data provided in the file.

Example:

```
Contact: Jane Doe <jane.doe@example.com>
```

### Collects

This REQUIRED multi-value field indicates which potentially privacy relevant user specific data is being collected or used in session identification. These MUST also be specified if properties are not persisted as-is, but stored or otherwise computed in a hashed and/or combined form.

#### Allowed values

##### ip-address

The request IP address is being used.

##### geographic-location

Geographic location of users is determined and used.

##### ua-string

Information about the User Agent used by the user is being used.

##### fingerprint

Browser Fingerprinting is used.

##### device-type

The user's device type (e.g. mobile / tablet / desktop) is being determined and used.

##### url

The URL of a visit is collected and used.

##### referrer

The Referrer of a visit is collected and used.

##### visit-duration

The duration of a visit, either on page- or on session-level is measured and used.

##### custom-events

Custom events like conversion goals are defined and used. This can be left out in case the analytics software in use offers such functionality, but implementors chose not to use the feature.

##### session-recording

Detailed behavior like mouse movement and scrolling is recorded and can possibly be played back when analyzing the analytics data.

#### Example

```
Collects: url, device-type, referrer
```

### Stores

This REQUIRED multi-value field indicates whether data is persisted on the client during the collection of analytics data and declares the browser features used for doing so. If no data is being persisted, the value `none` MUST be used.

#### Allowed values

##### first-party-cookies

First party cookies are in use. There is no differentiation between session or persistent cookies, just like HTTP and JavaScript cookies are considered equal.

##### third-party-cookies

Third party cookies are in use. There is no differentiation between session or persistent cookies, just like HTTP and JavaScript cookies are considered equal.

##### local-storage

Data is persisted on the client using non-cookie JavaScript APIs like `localStorage`, `sessionStorage` or `IndexedDB`

##### etag

The analytics software leverages browser caches to store identifiers.

##### none

No data is persisted on the client during the collection of usage data.

#### Example

```
Stores: 1st-party-cookies, local-storage
```

### Uses

This REQUIRED multi-value field indicates the technical implementation details for how analytics data is being collected.

#### Allowed values

##### javascript

A client-side script is used to collect data.

##### pixel

A resource - typically a pixel - downloaded via HTTP is being used to collect data through the request parameters.

##### server-side

Collection of usage data is happening on the server side at application layer.

##### logs

Usage data is being calculated from server log files.

##### other

Other techniques that are not described in this section are in use.

#### Example

```
Uses: script
```

### Allows

This REQUIRED multi-value field discloses information about whether user consent is being acquired before collecting analytics data, and if it is possible for users to opt out of the collection of usage data. Regulations about user consent do not apply to this field.

#### Allowed values

##### opt-in

No usage data is collected before users have given their consent.

##### opt-out

Users can opt out of collection of usage data using a dedicated feature tailored towards the user audience.

##### none

The software does not define a way for users to opt in or opt out of the collection of usage data.

#### Example

```
Allows: opt-in, opt-out
```

### Retains

This REQUIRED single-value field indicates the duration for which the analytics data is being stored before being delete. The value is a duration as defined in {{!RFC 3339}}. Implementors SHOULD add a comment providing a human readable value to this field.

#### Example

```
# Data is retained for twelve months
Retains: P12M
```

### Session

This OPTIONAL, RECOMMENDED single-value field indicates the coverage in session tracking. It MUST contain a single value only.

#### Allowed values

##### anonymous

Each event that is collected is anonymous. There is now way to connect two pageviews or similar.

##### session

A user can be reidentified throughout a single browser session.

##### user

A user can be identified across multiple browser sessions.

#### Example

```
Session: user
```

### Visibility

This OPTIONAL, RECOMMENDED multi-value field indicates whether the website provides a way for users or the general public to access data.

#### Allowed values

##### user

Users can access the usage data that is associated with them in a non-aggregated way, isolating all data that is specific to their current means of reidentification.

##### public

Usage statistics for the site or service are available to the general public.

#### Example

```
Visibility: public
```

### Compliance

This OPTIONAL field indicates conformance with certain regulations and legislations.

Example values are:

- gdpr
- hiipa
- ccpa

#### Example

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

#### Example

```
Vendors: offen, hotjar
```

## Example of an analytics.txt file

```
# analytics.txt file for https://www.analyticstxt.org
Author: Frederik Ring <hioffen@posteo.de>

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

# Location of the analytics.txt file {#location}

By default, an analytics.txt file SHOULD be placed in the ".well-known" path as per {{!RFC8615}} of a domain name or IP address.

## Alternatives

In case implementors are unable to meet this requirement, other options are available.

### link tag

Implementors MAY signal the location of an analytics.txt file in the context of a HTML document using a link element of rel "analytics"

Example:

```html
<link rel="analytics" href="https://example.com/resources/analytics.txt">
```

### HTTP Header

In addition to that implementors MAY send an HTTP header of `X-Analytics-Txt` with a response, sending the URI of the applicable file.

Example:

```
X-Analytics-Txt: https://example.com/resources/analytics.txt
```

## Precendence

In case multiple of these signals are being used, the precedence taken is:

1. X-Analytics-Txt Header
1. link element
1. ".well-known" location

## Scope of a file

An analytics.txt file MUST only apply to the domain or IP address in the URI used to retrieve it, not to any of its subdomains or parent domains. An analytics.txt file MAY also apply to products and services provided by the organization publishing the file.

# Security Considerations

## Incorrect or stale information

If information given in an "analytics.txt" file is incorrect or not kept up to date, this can result in usage of services under wrong assumptions, thus exposing users to possibly unwanted data collection and handling. Not having an "analytics.txt" file may be preferable to having incorrect or stale information in this file. Implementors MUST use the "Author" field (see {{author-field}}) to allow inquiries about the correctness of the given information.

## Spam

Implementors should be aware that disclosing mandatory author information as per {{author-field}} in such a file exposes them to possible Spam schemes or spurious requests.

## Multi-user Environments

In multi-user / multi-tenant environments, it may possible for a single user to take over the location of the "/.well-known/security.txt" file which would also apply to others. Organizations should ensure the ".well-known" location is properly protected. Implementors can instead use other locations as per {{location}} in such scenarios.

# IANA Considerations

## Well-Known URIs registry

The "Well-Known URIs" registry should be updated with the following additional values (using the template from {{!RFC8615}}):

URI suffix: analytics.txt

Specification document(s): this document

Status: permanent

--- back
