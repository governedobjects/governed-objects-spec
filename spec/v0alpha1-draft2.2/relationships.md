# Relationships

**Version:** v0alpha1  
**Revision:** draft2.2  
**Profile:** Core representation; traversal capability optional  
**Evidence confidence:** EXPERIMENTAL; relationship-set semantics are unchanged from draft2.1

Draft2.1 adds normative relationship-set semantics to the `v0alpha1 draft2` relationship model. Requirements inherited from draft2 keep their identifiers. `REL-ACCEPT-001` and `REL-REMOVE-001` each gain one cross-reference sentence. All other draft2.1 relationship obligations use new identifiers. See [Change Log](change-log.md).

## Normative Requirements

### Representation

**REL-EDGE-001 — Typed canonical edge**  
A Canonical Relationship MUST identify a source Canonical Identity, relationship type, and target Canonical Identity. It MAY carry type information, provenance, and other metadata.

**REL-EQUAL-001 — Relationship equality**  
Two Canonical Relationships MUST be treated as the same relationship if and only if their source Canonical Identity, relationship type, and target Canonical Identity are each equal. Relationship direction and relationship type participate in equality. Metadata, provenance, target-type hints, actor, and timestamps MUST NOT participate in Canonical Relationship equality. A Canonical Relationship has no identity independent of these three components.

**REL-MULT-001 — Set multiplicity**  
A Governed Object's Canonical Relationships MUST contain at most one relationship for any combination of source Canonical Identity, relationship type, and target Canonical Identity under `REL-EQUAL-001`. This constraint applies to the observable canonical relationship collection; internal storage MAY differ if the observable collection satisfies it.

**REL-ORDER-001 — Unordered collection**  
The Canonical Relationships of a Governed Object MUST NOT carry semantic meaning in their enumeration order. Two relationship collections are equal if and only if they contain the same relationships under `REL-EQUAL-001`. A business need for ordering MUST be represented explicitly, for example in Accepted Canonical State or in a separate Governed Object, and not by relationship enumeration order.

**REL-OWNER-001 — Source ownership**  
A Canonical Relationship MUST be part of the canonical representation of its source Governed Object and MUST be protected by the source object's Object Version under `CON-VERSION-001`. Accepting the addition or removal of a Canonical Relationship MUST NOT, by itself, constitute a canonical mutation of the target object.

### Mutation

**REL-ACCEPT-001 — Governed relationship acceptance**  
Adding a Relationship to canonical business or organizational meaning MUST be an explicit canonical semantic mutation. The addition MUST pass applicable governed authorization and validation, use the same exposed object concurrency protection as Accepted Canonical State, produce a new distinguishable Object Version that makes the prior version stale, enter Canonical History as required by `CORE-HIST-001`, be reconstructable when replay is claimed, and remain portable under `REL-PORT-001`. Observing or hydrating a provider-reported edge MUST NOT silently create a Canonical Relationship. A request to add a relationship that is already present under `REL-EQUAL-001` is not an addition and is governed by `REL-DUP-001`.

**REL-REMOVE-001 — Governed relationship removal**  
Removing a Canonical Relationship MUST be an explicit canonical semantic mutation with the same authorization, validation, exposed object concurrency, Object Version, Canonical History, claimed replay, and portability effects required for addition by `REL-ACCEPT-001`. A provider report, binding change, or derived-index update MUST NOT silently remove an accepted Canonical Relationship. A request to remove a relationship that is not present under `REL-EQUAL-001` is not a removal and is governed by `REL-ABSENT-001`.

**REL-NOOP-001 — Accepted relationship no-op**  
A governed relationship add or remove request that is authorized, valid, not stale, and whose requested canonical effect already holds MUST be reported as accepted under `OP-OUTCOME-001`, and MUST NOT alter Canonical Relationships or Accepted Canonical State, produce a new Object Version, or enter Canonical History. Such a request is not an accepted canonical mutation for the purposes of `CON-ADVANCE-001` and `OP-HIST-001`. An implementation MAY record non-canonical audit evidence of it under `EVT-SEPARATE-001`, and SHOULD allow the requester to determine that no canonical change occurred, for example by reporting the unchanged Object Version.

**REL-DUP-001 — Duplicate relationship add**  
A request to add a Canonical Relationship that is already present in the source object's Canonical Relationships under `REL-EQUAL-001`, when authorized, valid, and not stale, MUST be an accepted no-op under `REL-NOOP-001`. It MUST NOT create an additional relationship, MUST NOT be recorded in Canonical History as a relationship addition, and MUST NOT silently replace metadata or other attributes of the existing relationship.

**REL-ABSENT-001 — Absent relationship removal**  
A request to remove a Canonical Relationship that is not present in the source object's Canonical Relationships under `REL-EQUAL-001`, when authorized, valid, and not stale, MUST be an accepted no-op under `REL-NOOP-001`. It MUST NOT be recorded or reported as a canonical relationship removal.

### Authorization and Concurrency

**REL-AUTH-001 — Authorization independent of effect**  
Authorization under `AUTH-BASE-001` and `CORE-GOV-001` MUST apply to every relationship add or remove request, whether or not the request would change Canonical Relationships. An unauthorized request MUST NOT be reported as accepted, and its outcome class MUST NOT depend on whether the requested relationship exists.

**REL-PRECOND-001 — Stale requests conflict regardless of effect**  
When an expected Object Version is supplied with a relationship add or remove request and does not match the current Object Version, the request MUST produce a conflict outcome under `CON-CONFLICT-001`, including when the requested effect already holds, subject to `REL-PRECEDENCE-001`.

**REL-PRECEDENCE-001 — Rejection precedes no-op**  
If a relationship add or remove request is subject to authorization denial, semantic invalidity, or conflict, it MUST produce one of those outcomes and MUST NOT be reported as an accepted no-op. When several apply, the choice among them remains implementation-defined, subject to `AUTH-DISCLOSE-001`, except that an implementation MUST NOT evaluate or report existence-dependent validity in a way that discloses the existence of a relationship to a requester lacking the authority required by `REL-AUTH-001`. `AUTH-ORDER-001` recommends that authorization be evaluated first.

### History, Replay, and Portability

**REL-REPLAY-001 — Relationship history and set replay**  
Canonical History MUST record each accepted relationship addition and each accepted relationship removal as a distinct accepted change, identifying the relationship's source Canonical Identity, relationship type, and target Canonical Identity and the resulting source Object Version, in the deterministic per-object order required by `EVT-ORDER-001`. It MUST NOT record an accepted no-op as a relationship addition or removal. This is a semantic content requirement: it does not require a separate physical event, envelope, record, or storage transaction for each change, provided each accepted change, its resulting Object Version, and its order remain determinable. When replay is claimed under `EVT-REPLAY-001`, replay MUST reconstruct the Canonical Relationships as a collection equal under `REL-ORDER-001` to the accepted collection at the corresponding history position. Replay need not reproduce enumeration order. Canonical Relationships present in the creation or Replay Baseline revision are baseline content under `EVT-BASELINE-001`, not accepted additions. An implementation that supports such relationships MUST preserve them as part of the baseline and replay from them.

**REL-PORT-001 — Relationship portability**  
Canonical Relationships MUST remain associated with canonical identities rather than depend exclusively on provider-native foreign keys. Detaching or replacing a provider MUST NOT delete already accepted canonical relationships solely because the binding changed.

**REL-PORT-002 — Portable relationship-set content**  
An export or transfer claimed under `PORT-SEM-001` MUST represent each Governed Object's Canonical Relationships as a collection of distinct relationships under `REL-EQUAL-001` and `REL-MULT-001`, and MUST NOT introduce duplicates. It MUST preserve relationships whose target is absent from the export or unknown to the recipient. When Canonical History is exported, it MUST preserve accepted relationship additions and removals in deterministic per-object order under `EVT-ORDER-001`. A recipient MUST NOT silently merge, deduplicate, discard, or reorder accepted relationship history or relationship collections.

**REL-COMPAT-001 — Relationship data produced under multiset semantics**  
An implementation that imports, converts, or replays relationship collections or relationship history that contain more than one relationship with the same source Canonical Identity, relationship type, and target Canonical Identity, or history that adds an already-present relationship, MUST NOT silently deduplicate that data or interpret it as though `REL-MULT-001` and `REL-DUP-001` had applied when it was produced. It MUST either refuse the input with a stated reason or apply an explicit, inspectable conversion under `PORT-EVOL-001` that records every change it makes. This requirement applies only when an implementation accepts such data. It does not require any implementation to provide import, export, transfer, or conversion capability. This revision does not define such a conversion.

### Traversal

**REL-TRAVERSE-001 — Authorized traversal**  
An implementation MAY provide relationship traversal. If it does, it MUST apply `AUTH-QUERY-001` and `AUTH-GRAPH-001`, including authorization before returning or expanding through a node.

## Direction and Semantics

`v0alpha1 draft2.2` uses a directed source/type/target model for describing relationship meaning. `(A, t, B)` and `(B, t, A)` are different relationships. An implementation may support inverse lookup or undirected traversal without redefining the canonical direction. Inverse views are derived and are not part of the target object's canonical representation (`REL-OWNER-001`).

A Canonical Relationship is a unique directed assertion. Its **relationship type** is interpreted under the source object's Type Metadata (`TYPE-SEM-001`). It is not an object Type Identity. No relationship-type namespace is prescribed.

Implementations MAY maintain separate internal state and relationship counters, revisions, or storage. They MUST expose an equivalent single concurrency token that protects the whole portable canonical representation: Accepted Canonical State and Canonical Relationships.

**Multiplicity versus cardinality.** Draft2.1 fixes **multiplicity**: at most one relationship per triple (`REL-MULT-001`). It still does not prescribe a **cardinality model**, meaning limits on how many *distinct* targets a relationship type may have, such as "at most one `owned_by`". A type MAY declare cardinality constraints, which are enforced under `TYPE-VALID-001`. A duplicate add of an existing triple is a no-op, not a cardinality violation. No graph query language, edge storage model, relationship-type namespace, per-type cardinality model, or portable serialization is prescribed.

## Additional Business Meaning

The Core triple model grants no independent identity, lifecycle, state, or attributes to a relationship instance. Some business relationships need more:

- independent identity;
- lifecycle;
- state or attributes, such as role, period, or terms;
- governance;
- several distinguishable parallel instances between the same objects.

That meaning MUST NOT be represented by duplicate identical triples. It is modeled explicitly instead:

- through distinct relationship types, for example `primary_contact` and `billing_contact`; or
- by representing the relationship itself as a Governed Object related to each party.

Draft2.1 requires no particular schema for such objects. It introduces no normative edge identifiers and no normative relationship attributes.

## Outcome Summary (Non-Normative)

Version and history refer to the source object. Version comparisons use equality only.

| Request | Outcome | Relationships | Object Version | Canonical History |
| --- | --- | --- | --- | --- |
| Add absent relationship | Accepted canonical mutation | Relationship added | New distinguishable version | Records one addition |
| Add present relationship | Accepted no-op | Unchanged | Unchanged | Unchanged |
| Remove present relationship | Accepted canonical mutation | Relationship removed | New distinguishable version | Records one removal |
| Remove absent relationship | Accepted no-op | Unchanged | Unchanged | Unchanged |
| Stale expected version (any existence) | Conflict | Unchanged | Unchanged | Unchanged (audit MAY) |
| Unauthorized (any existence) | Authorization denied | Unchanged | Unchanged | Unchanged (audit MAY) |
| Invalid request | Semantically invalid | Unchanged | Unchanged | Unchanged (audit MAY) |

The target object's Object Version and Canonical History are unaffected by every row.

## Non-Normative Example

```text
create A; create B
add    A --belongs_to--> B   → accepted mutation   {A→B}   version a2   history +added
add    A --belongs_to--> B   → accepted no-op      {A→B}   version a2   no entry
remove A --belongs_to--> B   → accepted mutation   {}      version a3   history +removed
remove A --belongs_to--> B   → accepted no-op      {}      version a3   no entry

Canonical History of A: creation, added(A,belongs_to,B), removed(A,belongs_to,B)
Replay of A: {}
Canonical History of B: creation only
```

## Duplicate Semantics Versus Idempotency Keys

Set semantics give **state-level** convergence. Repeating an add or remove request leaves the same relationship collection. They do not provide **command-level** deduplication: a repeated request is evaluated again and, if the effect already holds, reports an accepted no-op with the current Object Version, not the original result.

- A repeated **conditional** request conflicts once the first attempt has advanced the Object Version.
- Idempotency keys that return an original result remain optional and outside Core ([Operations](operations.md)).

## Relationship, Dependency, and Authority

A relationship is an asserted typed edge. It does not by itself:

- make a governed decision about the source depend on the target's state; or
- confer, inherit, or narrow authority over the target.

No parent/child hierarchy is implied.

## Evidence Qualification

The Go experiment and Python reference runtime diverged on relationship version, history, and replay effects. Draft2 resolved that. They also diverged on duplicate-add multiplicity, which draft2 left open and draft2.1 resolves.

Draft2.1 relationship-set behavior remains EXPERIMENTAL. Both runtimes have since passed the experimental draft2.1 behavioral harness. That is evidence, not independent conformance, and it establishes no draft2.2 result. Draft2.2 changes no relationship-set semantics. Provider-reported edges remain External Observations unless admitted under that profile. Accepted Core relationships are distinct from them. See [Implementation Notes](implementation-notes.md#draft21-relationship-implementation-status) for known runtime status.
