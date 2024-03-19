# FeatherPub Data Model

The FeatherPub Data Model works as a subset (though not a _strict_ subset) of ActivityPub, but works to build a _pure JSON projection_ of that model.

This unwinds some of the core assumptions that go into working with JSON-LD and leads to some differences in assumptions and directions:

1. **Functional and Non-Functional Fields**. For the most part if something _can_ be a list in FeatherPub then it is _always_ a list. An exception is made for type fields, in part as a nod to backwards compatibility.
2. **Types**. Things are not allowed to be a URI identifier for an object _or_ an arbitrary object _or_ a Link to an arbitrary object _or_ a URI identifier of a Link pointing at an arbitrary object in most cases, and those cases where this the flexibility is allowed are explicitly declared. As much as possible, types should be restricted.
3. **Inheritance**. Inheritance here is framed as it is used in _type theory_ rather than how it tends to be used in _ontology_ and _semantics_ discussions. This means (following the Liskov Substitution Principle) that if "`A` inherits from `B`" then `A` can be used anywhere `B` is accepted.
4. **Minimalism**. While other fields are perfectly allowable, the fields _in the spec_ are those that are used _by the spec_ for processing and aim to always have distinct meanings in terms of processing. If it doesn't change anything in the spec then downstream applications may still choose to use it, but it doesn't change anything about how the FeatherPub processors work.
5. **Conformance**. Everything should be relatively straightforward to work with using JSON-Schema and OpenAPI3, preferably without using a thousand nested `oneOf` statements.

To do this, types are divided into one of four cases:

1. **Interfaces**. These can be inherited from generally. Cannot be used as a type in a type field.
2. **Abstract Classes**. These cannot combine with other abstract classes, but can combine with other interfaces. Cannot be used as a type in a type field.
3. **Classes**. These cannot combine with additional classes or abstract classes, but can include mixins. Can be used as a primary type.
4. **Mixins**. These cannot be instantiated on their own, but can be combined with a primary type. Mixins can be **implicit** (and thus can be used without being declared in the type signature) or **explicit** (must be declared in the type signature to be used).

In addition, types are classified into **packages**. Packages are a notational grouping that is used for conceptual convenience, but the dependency tree between packages is maintained as a DAG: no circular dependencies allowed.
