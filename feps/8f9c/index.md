---
slug: "8f9c"
authors:
  - Hrefna <@hrefna@hachyderm.io>
status: DRAFT
dateReceived:
discussionsTo:
featherPubState: REQUIRED
tags:
  - "#ActivityPub"
  - "#http"
  - "#FeatherPub"
---

# Audience Targeting

## Introduction

In the original conceptualization, the `audience` field was known as `scope` and was used to declare who
[the object was meant for](https://github.com/w3c/activitystreams/issues/300).  Later the keyword was
changed to `audience` and later still the meaning was lost so that it is now semantically indistinguishable
from `cc`.

This spec aims to bring its use more in line with its original purpose. It should be noted that this is
_not access control_. More properly this should be understood as a visibility filter.

## Motivation

There have been many, many requests over time for various ways of pairing down the list of recipients for
a message, including: 

- Requests to filter recipients to the local server
- Requests to filter recipients to a small group of servers
- Confusion over creating semantic differences between putting `as:Public` in `cc` vs `to`

This spec looks to provide a way to do these without making promises that would be difficult to keep.

## Design

The spec introduces the following "pseudocollections":

- `<uri>/#Limited`
- `<uri>/#Public`

The `audience` field is always a list and may contain:

- Either of the above pseudocollections.
- The `as:Public` pseudocollection.

The interpretation of each state is as follows:

- Empty list or the attribute is not present: it is sent to the members addressed. They SHOULD NOT forward the full object to others, but may reference it with a link.
- 
