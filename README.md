# FeatherPub

> Birds of a feather, federate together

FeatherPub is an implementation of ActivityPub aimed at solving the following:

- **ActivityPub is flexible to a degree that it makes interoperability difficult**. This problem is intrinsic to the model that ActivityPub is based on (JSON-LD and ultimately RDF): it is easy to write, but hard to parse, and hard to ensure that what you are looking at is what you want. There are ways that this could be solved on top of the JSON-LD model still, but ActivityPub leaves that flexibility in place.
- **ActivityPub is hard to test**. Testing asynchronous systems is difficult just to start with, but while tools exist to do this none of these are defined as part of the ActivityPub spec. This leads to implementations having various ways of implementing testing which are not cross compatible, or only cross compatible because of heroic work from developers who are trying to interoperate.
- **ActivityPub is unopinionated**. Whether you are building a codeforge or a microblogging application, ActivityPub didn't want to close any doors. This makes it hard to know that you have implemetned "enough" of it, and makes it impossible to implement all of it.

In some contexts these are all the result of tradeoffs made during the creation of ActivityPub, sometimes these are the result of how the big systems in this space operate, but regardless of why they came about they are now things that we need to find ways to address.

FeatherPub is _not a fork_. FeatherPub is an implementation that aims to be an opinionated implementation framework for solving the problems in a project the developer is working on called Njall, in this sense it can be considered as part of the ongoing development of Njall, spun off into its own form.

To do this it combines several different specs, including FEPs and other specs layered on top of ActivityPub, and specifies One Way(tm) of building these systems, based largely (but not entirely) on how these systems are build in practice. It aims to be interoperable "out of the gate" and make it easy to interoperate.

This is a work in progress. It is not meant to be used in its present state, but rather to show and get feedback on this way of building compatible systems. This will change later, and the goal is to fully release this in a more open way down the road. I have no desire to be a BDFL.

## What FeatherPub Does (or Aims to Do) Differently

At FeatherPub's core is an unwinding of the assumptions that RDF and JSON-LD bring into this space. While these are reasonable technologies for a set of problems their application in this space has caused a great deal of confusion. This is not to say this is the only way to approach this--for example, the [ActivityPub Minimal Core](https://github.com/steve-bate/activitypub-mincore) takes the opposite approach--but rather it is an opinionated way to solve these problems.

It aims to be compatible _on write_ with other ActivityPub implementations. Which is to say it is not going to do anything too strange and a fully compliant ActivityPub system will generally be able to understand a FeatherPub object. But it only aims to _read_ other FeatherPub implementations. Nothing in this spec should be construed to say that you can't or even shouldn't read other implementations, but rather to define a subset that says: "if you can read these items then you can talk to any FeatherPub instance, at least at a basic level."

It also defines some specifications for the _server_ and _protocol_ outside of what ActivityPub covers. Things like security need to be eventually given some coverage, and details such as whether to implement NodeInfo are covered as well. The goal is that FeatherPub-based systems be able to talk with other FeatherPub-based systems mostly or entirely based around _spec compliance_ rather than needing to address every situation individually.

## Development and Layout

This is all early in development and development progress is going to be haphazard. In a way you can think of it as a thought experiment more than an actual project, though of course it aims to actually _be_ an actual project in the future. For the moment, however, no guarantees are made that things won't change in the future.

The layout of this project divides into the following:

- **feps** -- things that work as individual FEP proposals and that may go out individually.
- **specs** -- specifications that do not necessarily work in their current form as FEPs. Something either too enmeshed to how FeatherPub works or that I haven't been able to tease out into separate pieces yet.

There's an aim to provide `.feature` files for pieces, but the code to run the _steps_ for these is in another repo at the moment. The goal is to ultimately have it all in one repo, but for the moment I am trying not to drive myself to distraction.

# License

Code, including `.feature` code, is under Apache 2.0, see the LICENSE file for more details.

The goal is to release the spec itself under either CC-0 (as a FEP) or CC-BY 4.0. But until it is further along these parts will be "all rights reserved." This applies to any markdown or plain text files.
