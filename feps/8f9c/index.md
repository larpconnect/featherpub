---
slug: "8f9c"
authors:
  - Hrefna <@hrefna@hachyderm.io>
status: DRAFT
dateReceived:
discussionsTo: https://github.com/larpconnect/featherpub/issues/2
featherPubState: REQUIRED
tags:
  - "#ActivityPub"
  - "#http"
  - "#FeatherPub"
---

# Audience Targeting

## Introduction

In the original conceptualization, the `audience` field was known as `scope` and was used to declare who
[the object was meant for](https://github.com/w3c/activitystreams/issues/300). Later the keyword was
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

### Pseudocollections

The spec introduces the following "pseudocollections":

- `<uri>/#Limited`
- `<uri>/#Public`
- `<uri>/#Local`
- `as:Limited`

The `<uri>` MUST contain an authority and a secure schema, and MUST NOT contain any fragment other than `#Limited`, `#Local`, or `#Public`. As with pseudocollections in ActivityPub these must _not_ be sent to as if they were inboxes.

The `audience` field is always a list and may contain:

- Any of the above pseudocollections. These are _mutually exclusive_ with each other for the same base URI.
- The `as:Public` pseudocollection.
- Collections that the actor can see and the server has access to

The interpretation of each state is as follows:

- Empty list or the attribute is not present: it is sent to the members addressed. Servers other than the originating server SHOULD NOT forward the full object to others, but may reference it with a link.
- `as:Public` is as interpreted in [ActivityPub's public addressing](https://www.w3.org/TR/activitypub/#public-addressing). This MAY be sent to shared inboxes on the network or shared as per usual.
- `as:Limited` is as interpreted in [ActivityPub's public addressing](https://www.w3.org/TR/activitypub/#public-addressing) but with the following modifications:
  - It MUST NOT be sent to public inboxes for actors that are not addressed in some other field.
  - It SHOULD NOT appear on public timelines or as part of public collections unless those are explicitly addressed as part of the other addressing fields.
  - It MAY send it to the public inboxes for any actors that are addressed in other fields.
- `<uri>/#Limited` is as `as:Limited` but is restricted to those _actors_ whose identifying URI starts with `<uri>`. Receiving servers (including the originating server) MUST NOT send it directly to any server not identified by the audience field, but may reference the object with a link.
- `<uri>/#Public` is as `as:Public` but is restricted to those _actors_ whose identifying URI starts with `<uri>`. It may only be sent to the shared inboxes identified by those actors. Receiving servers SHOULD NOT send it directly to any server not identified by the audience field, but may refernece the object with a link.
- `<uri>/#Local` is a statement that _this object should not be federated_. Local is incompatible with messages that are addressed to anyone not on the same server, and they can only be used if the sending actor's URI is prefixed with the `<uri>`.
- Any collections objects SHOULD only be interpreted by the originating server and ignored on other servers. On the origianting server these act as a filter: any actors specified in the collection may be considered part of the audience for the purposes of sending it.

Note that there is always an implicit grant that the originating actor's _own_ actor may always receive the full object. You can always send objects to yourself.

Conformant servers will follow these restrictions and will ensure that the full objects are not sent outside of the identified servers (which can be any servers in the case of `as:Limited` and `as:Local`). There can be no guarantee that any given server will follow the rules, of course, and this is not an access control mechanism: No restrictions are put into place here (though of course servers may implement their own) about who a server can _show_ a given object to. Rather this is about who, when the objects are sent on, the servers should be allowed to send the full object to versus just a link.

The rules around sending a link instead of the full content does not apply to moderation actions in conformant servers. Servers may remain conformant when sending an entire object along in, for example, a `Flag` action.

This is most relevant to the originating server, where the client sends a message to them.

Servers SHOULD put some reasonable limit on the number of `audience` fields that they will process.

### Addressing

Any _actor_ entity identified in the standard addressing fields (`to` etc) is assumed to have an implicit grant to receive the object in the `audience` field.

If the `audience` field is populated then any _collection_ entity identified in the standard addressing fields MUST first be filtered to remove anyone who does not match one (or more) of the audience criteria.

Conformant servers will generally not put `as:Public` (or any of the other pseudocollections) in the other addressing fields and will ignore when other messages include them, but `as:Public` MAY be put there for backwards compatibility with other implementations.

### Failures

The following conditions are not allowed and SHOULD result in errors:

- URIs that do not contain an authority and a schema.
- URIs that contain a fragment.
- Specifying `#Local` for servers other than your own, or in combination with any other `audience` pseudocollections.
- Using `#Limited` and `#Public` together.

The exact form of the error is not specified here, following whatever is normal for the system.

Receiving servers MAY choose to reject messages that violate any of these rules. If they choose to process it despite the error then they SHOULD take the most restrictive interpretation and only send a link for anything else.

## Security

It cannot be emphasized enough that this is not an _access control feature_. This is not a specification of "who can access the object," rather this is a statement about "who the object should can be sent to." This is a "scope" for the messages and is mostly meant as a way for the client to tell their server "only send this to my followers who are on these three servers."

This system makes no guarantees that servers being sent to are conformant and has no mechansims to ensure this. Still, it is beneficial for conformant servers to more generally "play by the rules" where they can do so.

## Incompatibilities

The only major incompatibility the author was able able to identify was with the [Group federation FEP](https://www.w3.org/ns/activitystreams). This is more a function of how the group federation changes the meaning of audience and uses it in a way that is variant with both the original intent and with the spec for their own purpose. There are other ways to solve that same problem and the group federation specification is, while adopted by several groups (such as Peertube), incompatible already with other specifications.

## Alternatives Considered

### Matching Objects

Having objects that have rules in them for matching has some semantic cleanliness and it allows for much more complex set logic operations on who is included in an audience list, e.g., limiting messages exclusively to mutuals. This, however, is substantially more complex to implement and manage, while the strategy used here is straightforward by comparison.

### Public Only

The design could drop the `Limited` and `Local` options, mirroring how ActivityPub works more precisely. Several implementations, however, already capture a distinction between `as:Public` appearing in `to` vs. `cc` and this is meant to capture that semantically. Several servers also provide "Local Only" modes using a variety of different mechanisms for indicating this intent, but these are not entirely compatible with each other. It is hoped that this mechanism provides a kind of "clean answer" to these various solutions.

### Restricting Visibility

An option would be to treat this like an ABAC system and use these as properties to restrict who can see it. While servers may absolutely do this, that is beyond the scope of what is happening here, which is more about who gets the message in the first place and, in conformant servers, how things like "forwarding from the inbox" and fields like `object` work.

It's also notable that the link may _always_, in this design, be sent by a conformant server. This prevents a lot of edge cases and complexities in evaluation: Can I `Announce` a restricted object? Does it need the original limitations or can it have a different subset, etc. Handling all of these cases is likely to create false expectations around what is or is not allowed, and so you can always attach a link if you aren't sure, just not the object itself.

### Alternatives to URI Prefixing

It would be ideal to be able to specify this cleanly based on factors other than the `id` field of the `Actor` object. There are a few of these strategies, for example based on the prefixes of the inbox itself (which does not need to be on the same server as the `id`). These scenarios get complex _very_ quickly and distinguishing between them safely is difficult. This way works with how ActivityPub is constructed and is _generally_ used today.

### Using Boolean Flags, Another Field, or other strategies

Several systems attempt to do something similar to do this using some combination of boolean flags and other fields, but there is a lack of agreement on what those fields should look like. This strategy uses `audience` as it was originally intended and is flexible to combine with other strategies (such as matching objects) in the future.

# Changelog

| Date          | Changes                 |
| :------------ | :---------------------- |
| 17 March 2024 | First published version |
