---
title: A File Format for the Discoverable Use of Analytics
abbrev: analytics.txt
docname: draft-ring-analyticstxt-latest
category: info

ipr: trust200902
area: General
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
  DNT:
    title: Tracking Preference Expression (DNT)
    target: https://www.w3.org/TR/tracking-dnt/
    author:
      -
        name: Roy T. Fielding
      -
        name: David Singer
  GPC:
    title: Global Privacy Control (GPC)
    target: https://globalprivacycontrol.github.io/gpc-spec/
    author:
      -
        name: Robin Berjon
      -
        name: Sebastian Zimmeck
      -
        name: Ashkan Soltani
      -
        name: David Harbage
      -
        name: Peter Snyder

--- abstract

Internet privacy has become an important feature for users of websites and services.
This document proposes a way for websites and services to declare and disclose their usage of analytics and tracking software.
analytics.txt aims to be an elaborate file format that describes the privacy related characteristics of analytics and tracking software in a non-biased way.
An analytics.txt file is understandable for a non-technical audience, while also useful for the automated consumption by tools and software.

--- middle

# Introduction

## Motivation

User tracking and the utilization of analytics software on websites has become a widely employed routine, visibly and invisibly affecting the way the user facing internet works and behaves.
Yet, there is no well-defined way of accessing information about what software is being used and what kind of data it is collecting in a standardized way.
Legislation can only ever cover a subset of the range of existing technological implementations, creating incentives for software to find workarounds, thus allowing them to hide their presence from users.
Automated audits are limited to aspects that are possible to detect in clients, but cannot disclose other important implementation details.

## Scope of this proposal

This document defines a way to specify the privacy related characteristics of analytics and tracking software.
We aim for this information to be consumable both by humans as well as software.

The file "analytics.txt" is not intended to replace the requirement for complying with existing regulations, but supposed to give insights beyond the scope of these regulations.

### About providing a human readable format

A fundamental design goal of the "analytics.txt" format is to make such a file human readable.
While the percentage of consumers that are actually human beings will likely be low - browser extensions or search engines would be good examples of possible consumers - this tenet can drive the specification into a direction where the format will focus on providing information that is useful for human beings, even when captured and processed further by other software.

## Definition of the term "analytics" in the scope of this document

Analytics as referred to in this document involves the collection of usage statistics in order to generate reports that can help the providers of websites and services to better understand and optimize their services towards real world user behavior.
This can also include measuring different content against different groups of users.

## Verifying the provided information

"analytics.txt" is designed to provide insights beyond what is technically auditable from a client perspective.
While some characteristics could be determined automatically or manually at client level, others won't, and will rely on implementors providing correct information about what is happening at layers that are opaque to users.
This means consumers of an "analytics.txt" file will implictly need to trust the implementor to provide correct information, implicating two design goals for the format (technical implications are discussed in {{incorrect-information}}).

### Non-biased

All of the given datapoints are purely informational, there is no right or wrong option to choose from, and the format will never provide guidelines on how to assess or rate an "analytics.txt" file.
Based on this, implementors don't have strong incentives for providing incorrect information, but choose implementation because they are wishing to disclose information about their site that they otherwise couldn't.

### Non-canonical

An "analytics.txt" file should never be the canonical source of truth for making automated decisions or ratings about a site.
It is supposed to be one of multiple signals that can be used for assessing the behavior of a website, creating the possibility to connect and compare the provided data with what has been surveyed using other channels of information.

# Conventions and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 {{!RFC2119}} {{!RFC8174}} when, and only when, they appear in all capitals, as shown here.

The term "implementors" refers to the providers of services and websites that wish to use an analytics.txt file.

# Specification

This document defines a text file format that can be used by implementors to signal information about their usage of analytics software to both users and software.

By convention, this file is called "analytics.txt".
Its location and scope are described in {{location}}.

This text file contains multiple fields with different values.
A field contains a "name" which is the first part of a field all the way up to the colon (for example: "Author:") and follows the syntax defined for "field-name" in section 3.6.8 of {{?RFC5322}}.
Field names are case-insensitive (as per section 2.3 of {{?RFC5234}}).
The "value" comes after the field name and follows the syntax defined for "unstructured" in section 3.2.5 of {{?RFC5322}}.
The file MAY also contain blank lines and comments.

A field MUST always consist of a name and a value (for example: "Author: Jane Doe <jane.doe@example.com>").
Each field MUST appear on its own line.
Unless specified otherwise by the field definition, multiple values MUST be chained together for a single field (for example: "Implements: gdpr, ccpa") using the "," character (%x2c).
A field MAY NOT appear multiple times.

Implementors SHOULD aim for authoring an analytics.txt file that is easy to understand by non-technical audiences.

## Comments

Any line beginning with the "#" (%x23) symbol MUST be interpreted as a comment.
The content of the comment may contain any ASCII or Unicode characters in the %x21-7E and %x80-FFFFF ranges plus the tab (%x09) and space (%x20) characters.

Example:

~~~~~~~~~~
# This is a comment
~~~~~~~~~~

Implementors SHOULD make deliberate use of comments to make an analytics.txt file more accessible for non-technical audiences.

## Line Separators

Every line MUST end either with a carriage return and line feed characters (CRLF / %x0D %x0A) or just a line feed character (LF / %x0A).

## Extensibility

Like many other formats and protocols, this format may need to be extended over time to fit the ever-changing landscape of the Internet.
Special attention is required for defining the allowed values in enumerations to ensure they are a. extendable and b. do not become obsolete too quickly.

## Field Definitions

Field names are case-insensitive, yet implementors SHOULD use the capitalized style used in this document for consistency.

Field values are case-insensitive.
Unless otherwise specified, implementors MUST refer to the allowed values for a field given by the specification.

### Author {#author-field}

This REQUIRED field holds an OPTIONAL display name and a REQUIRED email address ("name-addr") as per section 3.4 of {{!RFC5322}} providing information about a person or entity responsible for maintaining the contents of the file.
The field MUST contain a valid email address which shall be used for inquiries about the correctness and additions to the data provided in the file.

#### Example

~~~~~~~~~~
Author: Jane Doe <jane.doe@example.com>
~~~~~~~~~~

### Collects {#collects-field}

This REQUIRED multi-value field indicates which potentially privacy relevant user specific data is being collected or used in session identification or other procedures.
These values MUST also be specified if a property is not persisted as-is, but stored or processed in a hashed and/or combined form.
Some of the allowed values overlap to a certain extent, e.g. a User Agent string might be used in a Browser Fingerprint.

#### Allowed values

##### none

No analytics data is collected at all. This value MUST NOT be used in conjunction with other values.

##### url

The URL of a visit, including its path, is collected and used.
This MUST also be specified in case URLs are stripped of certain parameters or pseudonymized before being stored.

##### time

The time of visit is collected.

##### ip-address

The request IP address is being used.

##### geo-location

Geographic location of users is determined and used.
This could for example be derived from the request IP, or from using browser APIs.

##### user-agent

Information about the utilized User Agent is being collected.

##### fingerprint

Browser Fingerprinting is used.
Such mechanisms usually try to compute a unique identifier from properties of the host Operating System, allowing them to re-identify users without having to persist an identifier.

##### device-type

The user's device type (e.g. mobile / tablet / desktop) is being determined and collected.
The categories and rules for this distinction might be different for different software solutions.

##### referrer

The Referrer of a visit is collected and used. This MUST also be specified if the referrer value is stripped of potential path fragments.

##### visit-duration

The duration of a visit, either on page- or on session-level is measured and used.

##### custom-events

Custom events like conversion goals are defined and used.
This MAY be left out in case the analytics software in use offers such functionality, but implementors chose not to use the feature.

##### session-recording

Detailed behavior like mouse movement and scrolling is recorded and can possibly be played back when analyzing the analytics data.

#### Example

~~~~~~~~~~
Collects: url, device-type, referrer
~~~~~~~~~~

### Stores

This field is REQUIRED unless the only value of the Collects field as per {{collects-field}} is none.
The multi-value field indicates whether data is persisted on the client during the collection of analytics data and declares the browser features used for doing so.
In case no data is being persisted at all, the value none MUST be used as the single entry for this field.

#### Allowed values

##### none

No data is persisted on the client during the collection of usage data.
This value MUST NOT be used in conjunction with other values.

##### first-party-cookies

First party cookies are in use.
There is no differentiation between session or persistent cookies, just like HTTP and JavaScript cookies are considered equal.

##### third-party-cookies

Third party cookies are in use.
There is no differentiation between session or persistent cookies, just like HTTP and JavaScript cookies are considered equal.

##### local-storage

Data is persisted on the client using non-cookie JavaScript APIs like `localStorage`, `sessionStorage`, `WebSQL` or `IndexedDB`

##### cache

The analytics software leverages browser cache mechanisms to store identifiers.
For example, ETag headers can be used to identify users based on their browser caches' contents.
This value is not required in case the analytics software sends static resources with cache headers, but does not make use of the request headers on subsequent requests for purposes other than managing caching of assets.

#### Example

~~~~~~~~~~
Stores: first-party-cookies, local-storage
~~~~~~~~~~

### Uses

This field is REQUIRED unless the only value of the Collects field {{collects-field}} is none.
The multi-value field indicates the technical implementation details for how analytics data is being collected.

#### Allowed values

##### javascript

A client-side script is used to collect data.

##### pixel

A static resource - typically a pixel - transferred via HTTP is being used to collect data through the request parameters.

##### server-side

Collection of usage data is happening on the server side at the application layer.

##### logs

Usage data is being calculated from server log files.

##### other

Other techniques that are not described in this section are in use.

#### Example

~~~~~~~~~~
Uses: script
~~~~~~~~~~

### Allows

This field is REQUIRED unless the only value of the Collects field {{collects-field}} is none.
The multi-value field discloses information about whether user consent is being acquired before collecting analytics data, and if it is possible for users to opt out of the collection of usage data.

#### Allowed values

##### none

The software does not define a way for users to opt in or opt out of the collection of usage data.
This value also applies to scenarios where only a subset of data is collected by default and could be extended by opting in.
This value MUST NOT be used in conjunction with other values.

##### opt-in

No usage data is collected before users have given their consent.

##### opt-out

Users can opt out of collection of usage data using a dedicated feature tailored towards the user audience.
This value is only applicable in case no data at all is collected after having opted out.

#### Example

~~~~~~~~~~
Allows: opt-out
~~~~~~~~~~

### Retains

This field is REQUIRED unless the only value of the Collects field {{collects-field}} is none.
The single-value field indicates the duration for which the analytics data is being stored before being deleted. This duration MUST also cover periods where data might transition to be stored in aggregated form only.
The value is either a duration in days (including the days suffix), or the token "perpetual" in case data is retained without expiring it at some point.
A day is defined as 24 hours.
In case the retention period does not divide evenly into days, it MUST be brought up to the next round figure.

#### Example

~~~~~~~~~~
Retains: 365 days
~~~~~~~~~~

### Honors

This OPTIONAL, RECOMMENDED multi-value field indicates which browser level privacy controls are being honored when collecting data.

#### Allowed values

##### none

Data is collected even if any of the browser settings listed below are in use.
This value MUST NOT be used in conjunction with other values.

##### do-not-track

User-Agents that have DoNotTrack {{DNT}} enabled will be excluded from the collection of analytics data.

##### global-privacy-control

User agents that have Global Privacy Control {{GPC}} enabled will be excluded from the collection of analytics data.

#### Example

~~~~~~~~~~
Honors: do-not-track, global-privacy-control
~~~~~~~~~~

### Tracks

This OPTIONAL, RECOMMENDED multi-value field indicates the coverage in session and user lifecycle tracking.

#### Allowed values

##### none

Each event that is collected is anonymous.
There is no way to connect and group multiple pageviews by user or similar.
This value MUST NOT be used in conjunction with other values.

##### sessions

Metrics that source from a single browser session can be grouped and distinguished as such.

##### users

Users can be identified across multiple browser sessions.

#### Example

~~~~~~~~~~
Tracks: sessions, users
~~~~~~~~~~

### Varies

This OPTIONAL, RECOMMENDED single-value field indicates the usage of content experiments like A/B testing.
It MUST contain a single value only.

#### Allowed values

##### none

All users are served the same content without any changes.
This value MUST NOT be used in conjunction with other values.

##### random

Content experiments are performed by grouping users randomly into buckets and serving them different content.

##### geographic

Content experiments are performed by targeting user based on their geographic location.

##### behavioral

Content experiments are performed by grouping users into buckets based on their behavior and serving them different content.

#### Example

~~~~~~~~~~
Varies: random
~~~~~~~~~~

### Shares

This OPTIONAL, RECOMMENDED multi-value field indicates whether data is shared with select users, the general public or third parties.

#### Allowed values

##### none

The data collected is not shared with any party unless directly affiliated with the implementor, e.g. employees.

##### per-user

Users can access the usage data that is associated with them in a non-aggregated way, isolating all data that is specific to their current means of re-identification.

##### general-public

Usage statistics for the site or service are available to the general public.

##### third-party

Data is being shared non-publicly with third parties.
This MUST also be specified when datasets are aggregated or pseudonymized beforehand.

#### Example

~~~~~~~~~~
Shares: general-public
~~~~~~~~~~

### Implements

This OPTIONAL field indicates conformance with existing regulations and legislation. Values for this field SHOULD use all lowercase tokens with whitespace being replaced by the dash character (%x2d).

Example values are:

- gdpr
- ccpa

#### Example

~~~~~~~~~~
Implements: gdpr, ccpa
~~~~~~~~~~

### Deploys

This OPTIONAL field indicates which software is being used for collecting analytics. Values for this field SHOULD use all lowercase tokens with whitespace being replaced by the dash character (%x2d).

Example values are:

- google-analytics
- plausible
- hotjar
- matomo

#### Example

~~~~~~~~~~
Deploys: google-analytics, hotjar
~~~~~~~~~~

## Examples of analytics.txt files

### A site using analytics

~~~~~~~~~~
# analytics.txt file for www.example.com
Author: Jane Doe <doe@example.com>

Collects: url, referrer, device-type
Stores: first-party-cookies, local-storage
# Usage data is encrypted end-to-end
Uses: javascript
# Users can also delete their usage data only without opting out
Allows: opt-in, opt-out
Retains: 186 days

# Optional fields
Honors: none
Tracks: sessions, users
Varies: none
Shares: per-user
Implements: gdpr
~~~~~~~~~~

### Specifying required fields only

~~~~~~~~~~
Author: John Doe <doe@example.com>
Collects: url, ip-address, geo-location, user-agent, referrer, device-type, custom-events
Stores: none
Uses: javascript
Allows: none
Retains: perpetual
~~~~~~~~~~

### A site not using any analytics

~~~~~~~~~~
# analytics.txt file for www.example.com
Author: Jane Doe <doe@example.com>
Collects: none
~~~~~~~~~~

# Location of the analytics.txt file {#location}

By default, an analytics.txt file SHOULD be placed in the ".well-known" location as per {{!RFC8615}} of a domain name or IP address.

## Alternatives

In case implementors are unable to meet this requirement, other options are available.

### link Tag

Implementors MAY signal the location of an analytics.txt file in the context of a HTML document using a link element of rel "analytics"

Example:

~~~~~~~~~~
<link rel="analytics" href="https://example.com/resources/analytics.txt">
~~~~~~~~~~

### HTTP Header

Implementors MAY send an HTTP header of `X-Analytics-Txt` with a response, sending the URI of the applicable file.

Example:

~~~~~~~~~~
X-Analytics-Txt: https://example.com/resources/analytics.txt
~~~~~~~~~~

## Precedence

In case multiple of these signals are being used, the precedence taken is:

1. X-Analytics-Txt Header
1. link element
1. ".well-known" location

## Scope of a file

An analytics.txt file located in the ".well-known" location MUST only apply to the domain or IP address of the URI used to retrieve it, and SHALL NOT apply to any of its subdomains or parent domains.
If the location is signaled using the HTTP Header or in the document markup itself, its scope SHALL be limited to the requested resource only.

If distributed in non-standard locations, an analytics.txt file MAY also apply to products and services provided by the organization publishing the file (e.g. desktop or mobile applications) and which cannot be mapped to a domain name or IP address.
In such cases, implementors MUST add sufficient commentary describing the applicable scope.

# Security Considerations

## Incorrect or stale information {#incorrect-information}

If information given in an "analytics.txt" file is incorrect or not kept up to date, this can result in usage of services under wrong assumptions, thus exposing users to possibly unwanted data collection and handling.
Not having an "analytics.txt" file may be preferable to having incorrect or stale information in this file.
This guideline also applies to field level: in case of ambiguities or uncertainties, it's recommended to omit a field or a value rather than providing incorrect information.
Implementors MUST use the "Author" field (see {{author-field}}) to allow inquiries about the correctness of the given information.

## Spam

Implementors should be aware that disclosing mandatory author information as per {{author-field}} in such a file exposes them to possible Spam schemes or spurious requests.

## Multi-user environments

In multi-user / multi-tenant environments, it may possible for a single user to take over the location of the "/.well-known/analytics.txt" file which would also apply to others.
Organizations should ensure the ".well-known" location is properly protected. Implementors can instead use other locations as per {{location}} in such scenarios.

# IANA Considerations

## Well-Known URIs registry

The "Well-Known URIs" registry should be updated with the following additional values (using the template from {{!RFC8615}}):

URI suffix: analytics.txt

Specification document(s): this document

Status: permanent

--- back
