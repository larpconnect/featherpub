# Base Spec Guide

Broadly speaking FeatherPub may be divided into three sections:

- _Federated_ -- what is required for FeatherPub to federate with itself and others
- _Client_ -- How a client can talk to a FeatherPub service
- _Server_ -- What is required to set up such a server

Demarcating these and splitting them into separate areas is work for the future, but for the most part it should be clear which mantle they fall under, and in some cases it will be made explicit. A "FeatherPub-compliant" server will implement all three of them, but servers may theoretically choose to implement just "FeatherPub Server" and "FeatherPub Client" without implementing federation, say, or any other mixture/combination.

## FEPs

- [6f55](../feps/6f55/index.md) (local) -- **Control Messages** -- **REQUIRED** (Server). Optional returning of `Operation` in a `202` SHOULD also be implemented.
- [6f55](../feps/8f9c/index.md) (local) -- **Audience Targeting (using the audience field)** -- **REQUIRED** (Federated).
- [67ff](https://codeberg.org/fediverse/fep/src/branch/main/fep/67ff/fep-67ff.md) -- **FEDERATION.md** -- **REQUIRED** (Federated). SHOULD contain information on how to federate with the service in question, sufficient for an implementer to get started. SHOULD be hosted in a prominent location that is easily findable within the code repo and/or the `.wellknown` path.
- [f1d5](https://codeberg.org/fediverse/fep/src/branch/main/fep/f1d5/fep-f1d5.md) -- **NodeInfo** -- **RECOMMENDED** (Server). Implementations SHOULD use [NodeInfo version 2.x](https://github.com/jhass/nodeinfo/blob/main/PROTOCOL.md).
- [d556](https://codeberg.org/fediverse/fep/src/branch/main/fep/d556/fep-d556.md) -- **Webfinger** -- **RECOMMENDED** (Server).
- [2677](https://codeberg.org/fediverse/fep/src/branch/main/fep/2677/fep-2677.md) -- **Identifying the Application Actor** -- **RECOMMENDED** (Server).
