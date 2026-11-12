# Concurrency

**Version:** v0alpha1  
**Revision:** draft2.2  
**Profile:** Core for mutable objects  
**Evidence confidence:** STRONG

## Normative Requirements

**CON-VERSION-001 — Canonical version token**  
A mutable Governed Object MUST expose one Object Version concurrency token that protects the whole portable canonical representation: Accepted Canonical State and Canonical Relationships. It MUST allow an authorized caller to condition a mutation on the canonical revision it observed. The token's representation is implementation-defined.

**CON-CONFLICT-001 — Stale mutation conflict**  
When an expected Object Version is supplied and does not match the current Object Version, the conditional mutation MUST return **CONFLICT** or a semantically equivalent outcome and MUST NOT apply the requested canonical mutation or its external side effects. A stale conditional mutation MUST NOT be accepted. If another rejection outcome, such as authorization denial, also applies to the request, the implementation MAY report that outcome instead, subject to `AUTH-DISCLOSE-001`.

**CON-ADVANCE-001 — Accepted mutation advances revision**  
Every accepted canonical mutation, including Accepted Canonical State changes and Canonical Relationship additions or removals, MUST produce a new distinguishable Object Version that makes a precondition based on the prior version stale. An accepted no-op under `REL-NOOP-001` is not an accepted canonical mutation and does not produce a new Object Version. The new Object Version produced by an accepted canonical mutation is subject to `CON-UNIQUE-001`.

**CON-NOADVANCE-001 — Non-accepted attempts do not advance**  
A mutation request that does not result in an accepted canonical mutation, including an authorization-denied, semantically-invalid, or conflicting request and a request refused for another reason such as an implementation or resource limit, MUST NOT advance Object Version or enter Canonical History. Audit records of the attempt do not constitute a version advance.

**CON-UNIQUE-001 — Revision-unique Object Version**  
For a Canonical Identity, an Object Version MUST identify at most one accepted canonical revision over the lifetime of that identity. An implementation MUST NOT expose, for that identity, an Object Version equal to one that identifies a different accepted revision. This includes an Object Version that identified an earlier revision whose canonical representation equals the current one, and every Object Version recorded in the object's Canonical History, including history the implementation restored rather than produced. This requirement applies to every path that produces an accepted canonical revision, including optional-profile acceptance paths. It constrains equality only: it does not require Object Versions to be numeric, ordered, monotonic, sortable, or produced by any particular allocation method.

## Version Domains

Object Version protects Accepted Canonical State and Canonical Relationships through one exposed token. An implementation MAY use separate internal state and relationship counters if the exposed token provides equivalent whole-object concurrency protection. Type Version identifies type semantics. Provider Version protects a Provider Projection. These are separate domains and must not be treated as interchangeable tokens.

An operation that supplies an expected Object Version is a **conditional mutation**. An operation that omits it is an **unconditional governed mutation**. `CON-VERSION-001` requires conditional mutation capability, but an implementation MAY require expected versions on selected or all mutation interfaces and MAY permit unconditional governed mutations when the expected version is absent. Authorization and validation apply in either case.

## Non-Normative Example: Stale Mutation

```text
read object at version 8
another accepted mutation produces version 9
attempt mutation with expected version 8
→ conflict
→ Accepted Canonical State remains at version 9
```

## Non-Normative Example: Relationship No-Op and Staleness

```text
object A at version a2 already has relationship (A, belongs_to, B)
add (A, belongs_to, B), expected a2   → accepted no-op; version stays a2 (REL-DUP-001)
add (A, belongs_to, B), expected a1   → conflict, although the relationship exists (REL-PRECOND-001)
add (A, belongs_to, B), no expected   → accepted no-op; version stays a2
```

A stale conditional request conflicts even when its effect already holds. The requester's observation is out of date, and staleness is reported rather than hidden by the no-op.

## Non-Normative Example: Returned State Does Not Reuse a Token

```text
object A at version a1, relationships {}
add    (A, t, B)                  → accepted   {A→B}   version a2
remove (A, t, B)                  → accepted   {}      version a3   (a3 ≠ a1, a3 ≠ a2)
add (A, t, C), expected a1        → conflict                         (CON-UNIQUE-001)
```

The canonical representation at `a3` equals the one at `a1`, but `a3` identifies a different revision. Reissuing `a1` would let a precondition taken at `a1` pass after two intervening accepted changes. A token derived only from current content, such as a hash of state and relationships, cannot satisfy `CON-UNIQUE-001`.

## Historical and Never-Issued Tokens

Under `CON-CONFLICT-001`, any expected Object Version other than the current one is stale. When the requester also lacks authority for the mutation, the implementation may report authorization denial instead. It must do so where `AUTH-DISCLOSE-001` requires the outcome not to vary with the expected version. That includes a token recorded earlier in Canonical History and a token never issued for the object. Revealing which of the two was supplied is not required and is not recommended practice.

## Scope

`v0alpha1 draft2.2` does not require:

- integer, increment-by-one, monotonically ordered, totally ordered, or consecutive versions;
- multi-object transactions;
- field-level conflict detection;
- CRDTs, vector clocks, MVCC, or SQL locks; or
- a distributed consistency protocol.

The exposed Object Version has whole-object portable meaning even when an implementation uses finer-grained internal concurrency.

Operational Metadata changes such as hydration caches, repair echoes, synchronization bookkeeping, and provider binding or detachment do not advance Object Version (`CORE-LINEAGE-001`). If a binding or metadata operation separately accepts a change to the durable canonical representation, that accepted canonical effect follows the ordinary version and history requirements. An implementation that needs a concurrency guard for operational changes may use a separate operational or provider-side revision, which is not Object Version.

Implementations that allocate Object Versions from a counter must ensure that newly issued values avoid every value recorded in the object's Canonical History, including restored history whose values were not issued locally. How that is achieved, for example by a high-water mark, is implementation-defined.
