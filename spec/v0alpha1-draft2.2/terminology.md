# Terminology

**Version:** v0alpha1  
**Revision:** draft2.2  
**Status:** Experimental Draft

Terms in this document are semantic. Their spelling does not require a particular field name, class, table, or serialization.

## Core Terms

**Governed Object**  
A typed, versioned representation of an organizational asset or business entity with durable canonical identity, Accepted Canonical State, Canonical Relationships, Type Metadata, governance, lifecycle semantics where declared, and Canonical History.

**Canonical Identity**  
The durable organization-controlled identity of a Governed Object. It is distinct from identifiers assigned by applications, providers, storage systems, or runtime implementations.

**Accepted Canonical State**  
The organization-owned state that a conforming runtime has accepted for the object. Together with Canonical Relationships, it forms the durable canonical representation. External provider state, observations, caches, application views, and model context are not Accepted Canonical State unless admitted through governed semantics.

**Canonical Relationships**  
The organization-accepted, unordered set of typed directed semantic edges whose source is a given Governed Object. The set contains at most one edge per Relationship Equality Key (`REL-EQUAL-001`, `REL-MULT-001`, `REL-ORDER-001`). Canonical Relationships belong to the source object's canonical representation (`REL-OWNER-001`). Together with Accepted Canonical State, they form the durable canonical representation. Provider-reported edges and derived indexes are not Canonical Relationships unless admitted through governed semantics.

**Relationship Equality Key**  
The combination of source Canonical Identity, relationship type, and target Canonical Identity that determines Canonical Relationship equality under `REL-EQUAL-001`. Metadata and provenance are not part of it. The term names a semantic combination, not a field, identifier, or encoding.

**Canonical History**  
The deterministically ordered, durable representation of accepted canonical changes to a Governed Object, including accepted state, lifecycle, and relationship mutations. Canonical History excludes rejected attempts, concurrency conflicts, model traces, provider observations, and synchronization records except where those records also represent a separately accepted canonical change.

**Accepted Canonical Revision**  
The durable canonical representation of a Governed Object as established by its creation or Replay Baseline revision, or as produced by one accepted canonical change. Each accepted canonical change produces a new revision, distinct from every earlier revision even when their content is equal. Each revision has exactly one Object Version (`CORE-LINEAGE-001`, `CON-UNIQUE-001`).

**Creation or Replay Baseline Revision**  
The first accepted canonical revision of a Governed Object: the revision established when it was created, or the initial accepted canonical representation from which its Replay Baseline starts (`EVT-BASELINE-001`). Records of the object from before it became a Governed Object are not Canonical History. A later revision cannot become the baseline revision. Discarding the history before a later snapshot is an omission under `CORE-HIST-001`.

**Canonical History Lineage**  
The single ordered sequence of accepted canonical revisions of a Governed Object, from its creation or Replay Baseline revision to its current revision (`CORE-LINEAGE-001`).

**Canonical Event**  
A history record describing an accepted canonical change. An implementation may use event sourcing, snapshots plus change records, or another representation, provided the required history semantics remain portable.

**Object Version**  
A concurrency token identifying a revision of the durable canonical representation. An accepted canonical state or relationship mutation makes the prior token stale. Its syntax, ordering properties, and storage are implementation-defined. Within one Canonical Identity, an Object Version identifies at most one accepted revision (`CON-UNIQUE-001`).

**Type Identity**  
The portable identity of the semantic type governing an object.

**Type Version**  
The revision of a type's portable semantics. Type Version is distinct from Object Version.

**Type Metadata**  
The Type Identity and applicable Type Version needed to interpret an object and its Canonical History. Type Metadata is distinct from Accepted Canonical State and Canonical Relationships.

**Governance Reference**  
A portable reference or semantic association connecting an object to the governance that controls its operations. Governance references are distinct from Accepted Canonical State, Canonical Relationships, Type Metadata, and Operational Metadata.

**Operational Metadata**  
Implementation or profile data used to operate, observe, cache, bind, project, repair, synchronize, or diagnose an object without becoming part of its durable canonical representation. Operational Metadata does not automatically participate in Object Version, Canonical History, or replay; a separate governed acceptance is required for any semantic effect that does.

**Governed Operation**  
A request to read, query, create, or mutate Governed Objects under an authorization and validation context.

**Operation Context**  
The semantic inputs used to evaluate an operation. These include an actor, action, target object or type, business parameters, and, when applicable, application identity, delegated authority, purpose, provenance, and an expected Object Version.

**Accepted Outcome**  
An operation outcome that accepts the requested semantic effect under applicable authorization, validation, and concurrency semantics.

**Accepted No-Op**  
An accepted outcome for an authorized, valid, non-stale relationship add or remove request whose requested effect already holds (`REL-NOOP-001`). An accepted no-op is not an accepted canonical mutation: it does not change the canonical representation, produce a new Object Version, or enter Canonical History.

**Authorization-Denied Outcome**  
An operation outcome in which insufficient authority prevents acceptance.

**Semantically-Invalid Outcome**  
An operation outcome in which schema/type, lifecycle, domain, or equivalent semantic validation prevents acceptance.

**Conflict Outcome**  
An operation outcome in which a precondition no longer matches current state. A conflict is not an authorization denial and does not accept the requested mutation.

**Protected Information**  
Information about a target object that a requester's effective authority does not permit it to read. For a requester without read authority for the object, this includes at least the current Object Version, Accepted Canonical State including lifecycle state, Canonical Relationships, and Canonical History (`AUTH-DISCLOSE-001`).

**Portable Representation**  
Any representation of a Governed Object's portable semantic content (`PORT-SEM-001`) produced for export, transfer, or restoration. No encoding is implied.

**Restoration**  
Establishing, in an implementation, a Governed Object and its previously accepted Canonical History from a portable representation, without treating the act as an accepted canonical change (`PORT-RESTORE-001`). Restoration is distinct from creating a new object with copied content.

**Governance**  
The authorization, admission, lifecycle, and policy semantics controlling how an object may be read or changed. This term does not prescribe a policy language or engine.

**Relationship**  
A typed semantic edge from one Canonical Identity to another. A provider-reported edge remains an observation until governed admission makes it a Canonical Relationship. The relationship type is interpreted under the source object's Type Metadata. It is not an object Type Identity.

## Profile Terms

**Exporter**  
A role in the Portable Restoration Continuity Profile: an implementation that produces a portable representation of an object it holds.

**Restorer**  
A role in the Portable Restoration Continuity Profile: an implementation that restores an object from a portable representation, or refuses to.

**Application Context**  
Identity and authority bounds associated with the software surface requesting an operation. Application Context is distinct from actor identity and Canonical Identity.

**Delegated Authority**  
Authority granted by a principal to a delegate, constrained by supported dimensions such as capabilities, scope, purpose, validity, and status.

**External Observation**  
A normalized report about externally observed state associated with a canonical object. An observation is a claim, not Accepted Canonical State.

**Authority Decision**  
A governed decision about whether an observed external value is accepted, rejected in favor of canonical state, or held for review. Decisions may differ by field or semantic dimension.

**Provider Binding**  
An association between a Canonical Identity and a provider or external-system identity.

**Provider Projection**  
An external operational representation of some or all canonical meaning.

**Projection Gap**  
Canonical meaning or state that a provider cannot represent, observe, or operate.

**Drift**  
A difference between Accepted Canonical State and a Provider Projection.

**Outbound Repair**  
A conditional operation that brings a supported Provider Projection into conformity with current Accepted Canonical State. It is not a canonical mutation mechanism.

**Canonical Precondition**  
Evidence that the canonical state used to plan a repair has not changed.

**Provider Precondition**  
Evidence that the provider state used to plan a repair has not changed.

**Provider Replacement**  
Replacement of one Provider Projection and binding with another while preserving required canonical semantics.

**Agent**  
An actor implemented by software. An agent may use deterministic or probabilistic reasoning but receives no authority merely from that reasoning.

**Model Trace**  
Operational evidence of model reasoning or tool use. It is not Canonical History.

## Distinct Version Domains

`v0alpha1` distinguishes:

- **Specification Version** — the revision of this specification
- **Type Version** — the revision of a Governed Object type's semantics
- **Object Version** — the concurrency revision protecting Accepted Canonical State and Canonical Relationships
- **Event Schema Version** — a future version identifier for portable event representation; unresolved in `v0alpha1`
- **Provider Version** — a provider-side concurrency or ordering token

No equality, ordering, or shared encoding is implied across these version domains.
