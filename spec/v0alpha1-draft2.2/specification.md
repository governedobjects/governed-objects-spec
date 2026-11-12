# Governed Objects Specification v0alpha1 Draft 2.2

**Version:** v0alpha1  
**Revision:** draft2.2  
**Status:** Experimental Draft — proposed for review; not frozen, final, or certified

**Governed Objects Specification v0alpha1 Draft 2.2** is a complete, self-contained revision. It carries forward every `v0alpha1 draft2.1` requirement identifier. It adds the minimum Core semantics that experimental cross-runtime portable-restoration evidence showed to be missing or ambiguous:

- Object Version values that are unique to a revision within an object, even when an earlier state recurs;
- one Canonical History lineage per object, complete from creation or the Replay Baseline;
- acceptance order as an intrinsic part of history, and accepted effect separated from provenance;
- disclosure-safe rejection, meaning protected information does not leak to requesters who cannot read it, whether through outcome content or outcome variation;
- non-accepted outcomes beyond the four minimum classes that change nothing canonical;
- restoration, where performed, that is not itself a canonical change; and
- faithful preservation or explicit refusal where preservation is claimed.

It also defines an optional [Portable Restoration Continuity Profile](profiles/portable-restoration-continuity.md). The profile has Exporter and Restorer roles. It defines what it means for another implementation to restore an object's complete history and exact Object Version values and then continue governed operation under a declared single-authority handoff. Moving an object between two implementations also requires out-of-band agreement on encoding, type semantics, governance resolution, and other prerequisites the profile lists.

The decision basis is the approved synthesis and decision closure of the interchange analysis (not published). Requirement-level traceability is in the Decision Traceability and Revision Map documents (not published); every requirement identifier is listed in the [requirement inventory](requirement-inventory.json).

Draft2.2 improves semantic precision but does not establish wire-level interoperability, interchange interoperability, formal conformance certification, or production stability. The Portable Restoration Continuity Profile is a semantic profile, not a wire protocol, and defines no encoding. An open Core conformance suite (0.1.0) exists at <https://github.com/governedobjects/governed-objects-conformance> and has been run against both runtimes, with results recorded by the suite. Conformance claims follow [TRADEMARKS.md](../../TRADEMARKS.md) and name the suite version. This specification itself declares no implementation conformant.

Earlier architecture documents (not published) provide evidence and historical context. Where an older architecture document conflicts with this draft, `spec/v0alpha1-draft2.2/` defines the `v0alpha1 draft2.2` claim; the conflict remains relevant evidence for gap analysis.

## Relationship to Draft2.1

- **Draft2.1 remains frozen.** `v0alpha1 draft2.1` (earlier draft, not published) is unchanged and remains the definition of a `v0alpha1 draft2.1` claim. Draft2.2 neither amends it nor reclassifies any implementation's draft2.1 status.
- **Identifiers are stable.** No draft2.1 requirement is removed, renumbered, or renamed. Six Core requirements are new. Each changed requirement is classified CLARIFIED or MATERIALLY_CHANGED in the [Change Log](change-log.md) and the Revision Map (not published).
- **Some draft2.1-conforming behavior is not draft2.2-conforming.** Examples include reissuing an earlier Object Version when an earlier state recurs, and reporting a stale-version conflict to a requester that can neither read the object nor perform the operation. See the [compatibility notes](change-log.md#compatibility).
- **Portable restoration is optional.** Core requires no export, restoration, or runtime replacement. It constrains their meaning only where they occur or are claimed. The Portable Restoration Continuity Profile is claimed separately and by role.
- **Separate labels.** Draft2, draft2.1, and draft2.2 results are reported and labeled separately (`CONF-CLAIM-001`).
- **No Draft3 content.** Graph-dependent governance, cross-object dependency semantics, relationship-dependent authorization or validation, and multi-object transactional semantics are separate design work. Draft 2.2 does not introduce cross-object dependency semantics.

## Relationship to Draft2

- **Draft2 remains frozen.** `v0alpha1 draft2` (earlier draft, not published) is unchanged and remains the definition of a `v0alpha1 draft2` claim.
- **Draft2.1 added normative relationship-set semantics**, which draft2.2 carries forward unchanged. Behavior that draft2 permitted, such as accepting a duplicate relationship addition as a second edge, remains conformant to draft2 but not to draft2.1 or draft2.2.
- Draft2 incorporated findings from a Go Core implementation, separately written by the specification's maintainer, and a semantic differential review against the originating Python reference runtime. That evidence is carried forward unchanged. Neither implementation is the specification, and neither is declared conformant merely because it supplied evidence.

## Purpose

This specification defines an independently implementable semantic model for organization-owned Governed Objects. It identifies:

- required Core semantics;
- optional conformance profiles;
- observable candidate conformance behavior;
- non-normative reference-runtime choices; and
- unresolved matters that are deliberately not standardized.

The objective is semantic portability: preserving canonical identity, Accepted Canonical State, Canonical Relationships, Type Metadata, governance semantics or references, and Canonical History independently of replaceable applications, providers, intelligence systems, and runtime implementations. Accepted Canonical State and Canonical Relationships together form the durable canonical representation. Operational Metadata is a distinct category and does not automatically participate in Object Version, Canonical History, or replay.

## Normative Language

The capitalized terms **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY** indicate normative requirements in this draft:

- **MUST** and **MUST NOT** identify behavior required for interoperability, business sovereignty, or semantic correctness.
- **SHOULD** and **SHOULD NOT** identify strongly preferred behavior for which an implementation may have a documented justification to differ.
- **MAY** identifies optional behavior.

Normative requirements have provisional identifiers. Explanatory prose, examples marked non-normative, evidence classifications, and implementation notes are not requirements.

## Scope

Core specifies the smallest useful Governed Object:

- durable Canonical Identity;
- Type Identity and distinguishable type revision semantics;
- Accepted Canonical State;
- Canonical Relationships;
- Object Version and optimistic-concurrency semantics for mutable objects;
- governance and authorization semantics;
- declared validation and lifecycle semantics;
- Canonical History and conditional replay semantics; and
- semantic portability independent of a particular runtime, including what export, transfer, and restoration mean where they occur.

Core does not require providers, external observations, mandates, agents, LLMs, encryption, containers, or distributed deployment.

## Document Map

- [Terminology](terminology.md)
- [Core](core.md)
- [Type System](type-system.md)
- [Operations](operations.md)
- [Authorization](authorization.md)
- [Concurrency](concurrency.md)
- [Events and Replay](events.md)
- [Relationships](relationships.md)
- [Portability](portability.md)
- [Portable Restoration Continuity Profile](profiles/portable-restoration-continuity.md)
- [Delegated Authority Profile](profiles/delegation.md)
- [External Observation Profile](profiles/external-observation.md)
- [Provider Binding Profile](profiles/provider-binding.md)
- [Canonical Authority Profile](profiles/canonical-authority.md)
- [Agent Operation Profile](profiles/agent-operation.md)
- [Conformance and Evidence Mapping](conformance.md)
- [Draft2.2 Change Log, Requirement Inventory, and Normative Traceability](change-log.md)
- [Machine-Readable Requirement Inventory](requirement-inventory.json)
- [Non-Normative Implementation Notes](implementation-notes.md)
- [Open Questions](open-questions.md)

## Conformance Profiles

| Profile | Purpose | Status in v0alpha1 draft2.2 |
| --- | --- | --- |
| Core | Identity, accepted state, types, operations, authorization, versions, relationships, history, conditional replay, and portability | Required |
| Application Context | Application identity/provenance and authority narrowing | Optional Core authorization capability; not a resource-type profile |
| Delegated Authority | Monotonic, bounded delegation through mandates or equivalent authority artifacts | Optional |
| External Observation | Explicit admission of externally observed state | Optional |
| Provider Binding | Replaceable external operational projections | Optional |
| Canonical Authority | Dual-precondition outbound repair and provider replacement | Optional; requires Provider Binding and External Observation semantics |
| Agent Operation | Agents and model-backed operators under the ordinary governed boundary | Optional thin composition of Core and, when used, Delegated Authority |
| Portable Restoration Continuity | Complete-history export and exact-token restoration with continued governed operation after a declared single-authority handoff | Optional; claimed by role (Exporter, Restorer, or both); semantic, not a wire protocol |

### Conditional Core Capabilities

Core requirements are conditional where the described capability is not universally needed:

- mutable-object concurrency applies to mutable objects;
- query requirements apply when multi-object query is exposed;
- relationship traversal requirements apply when traversal is exposed;
- replay requirements apply when canonical-history replay is claimed;
- Application Context requirements apply when applications participate in authorization or portability claims; and
- semantic export/transfer requirements apply when portability of an exported representation is claimed;
- restoration requirements (`PORT-RESTORE-001`) apply when an implementation restores previously accepted history; and
- preservation requirements (`PORT-PRESERVE-001`) apply when preservation across export, transfer, or restoration is claimed.

An implementation may provide Core without every conditional capability, but its conformance declaration must identify which it claims.

Conformance claims are governed by `CONF-CLAIM-001` in [Conformance](conformance.md). A capability declaration is required semantically, but `v0alpha1` does not define a wire endpoint or document encoding for that declaration.

## Core Boundary Classification

| Concept | Classification | Rationale |
| --- | --- | --- |
| Canonical identity | CORE | Fundamental sovereignty boundary |
| Accepted Canonical State | CORE | One part of the durable canonical representation |
| Canonical Relationships | CORE representation; traversal optional | The other part of the durable canonical representation; an unordered set of unique directed typed triples owned by the source object; graph APIs are not universally required |
| Object Version | CORE for mutable objects | Protects accepted state and accepted relationships from stale writes; identifies at most one revision per object; opaque, compared only for equality |
| Type Metadata | CORE | Type Identity and Type Version govern interpretation without becoming object state |
| Governance references | CORE | Control acceptance without being conflated with state, relationships, or operational metadata |
| Canonical events/history | CORE | Preserves accepted change semantics as one complete, ordered lineage; provenance is evidence |
| Replay | OPTIONAL CORE CAPABILITY | If claimed, reconstruction uses Canonical History without providers or non-canonical traces |
| Authorization | CORE | Governed operations require authority |
| Operational Metadata | NON-CANONICAL unless separately accepted | Binding, hydration, repair, cache, and synchronization data does not automatically affect Object Version, history, or replay |
| Applications | OPTIONAL authorization context | Applications may narrow authority and remain replaceable |
| Mandates | OPTIONAL PROFILE | Delegation is not required for a small Core |
| Agents | OPTIONAL PROFILE | Agents are actors; no separate Core engine is required |
| LLMs | NON-NORMATIVE reasoning implementation | Model protocols and prompts are outside portable semantics |
| External observations | OPTIONAL PROFILE | Only needed when external state is admitted |
| Provider bindings | OPTIONAL PROFILE | Core objects may be entirely local |
| Authority rules for observations | OPTIONAL PROFILE | Specific to External Observation |
| Outbound reconciliation | OPTIONAL PROFILE | Specific to Canonical Authority |
| Provider versions | OPTIONAL PROFILE | Separate provider-side concurrency domain |
| Provider replacement | OPTIONAL PROFILE | Defined by Canonical Authority; Provider Binding alone defines detachment |
| Export, restoration, runtime replacement | CONDITIONAL CORE meaning; OPTIONAL PROFILE capability | Core constrains their meaning where performed; Portable Restoration Continuity defines the capability |
| Disclosure-safe rejection | CORE | Rejections do not reveal protected information to non-readers; existence concealment remains policy |
| Projection gaps | OPTIONAL PROFILE | Arise only with partial provider projections |
| Governed Container | NON-NORMATIVE example type/profile | Demonstrates generality but is not a mandatory business type |
| Encryption | NON-NORMATIVE implementation/security capability | No portable cryptographic profile exists |
| Wire encoding | UNRESOLVED | An experimental interchange representation exists as evidence only; none is normative |
| Policy representation | UNRESOLVED | Authorization behavior is specified; policy-language portability is not |
| Deployment topology | NON-NORMATIVE implementation | Embedded, daemon, server, HA, or managed forms are permitted |

## Principle Traceability

| Founding principle | Specification implication |
| --- | --- |
| Organization-Owned Canonical Identity | `CORE-ID-*`, `PB-*`, and `CA-*` keep external IDs replaceable |
| Accepted State Is Organization-Owned | `CORE-STATE-*` and `OBS-*` separate observations from acceptance |
| Accepted History Survives Replacement | `CORE-HIST-*`, `EVT-*`, `PB-*`, and `CA-*` preserve accepted history |
| Applications Operate State; They Do Not Own It | `AUTH-APP-*` and `PORT-APP-*` separate application context |
| Providers Are Replaceable | Provider Binding and Canonical Authority profiles |
| Intelligence Is Non-Authoritative | `AGENT-*` keeps model output outside authority |
| Authority Must Not Expand Downstream | `AUTH-NARROW-*` and `DEL-*` require monotonic narrowing |
| Canonical Meaning Survives Projection Gaps | `PB-GAP-*` retains unsupported canonical meaning |
| Portability Is More Than Bytes | `PORT-*` defines semantic portability |
| Implementations Must Be Replaceable | Encoding, language, persistence, and deployment remain non-normative |
| Independent Conformance | Requirements are observable and implementation-neutral; the harness remains open |
| Evolution Preserves Organizational Control | `CORE-EVOL-*`, `TYPE-VERSION-*`, and `PORT-EVOL-*` preserve identity/history through change |

This matrix does not convert every principle into a technical requirement. [PRINCIPLES.md](../../PRINCIPLES.md) remains the design and evaluation baseline.

In requirement-family references above, a trailing `*` means all defined requirement identifiers sharing that prefix; it is not itself a requirement identifier.

## Evidence Confidence

| Area | Confidence | Basis |
| --- | --- | --- |
| Canonical identity and accepted state | STRONG | Both implementations converge on the central identity and accepted-state invariants |
| Governed operations, authorization, and validation effects | STRONG | Both implementations preserve rejection without accepted change |
| Object optimistic concurrency effects | STRONG | Both implementations reject stale conditional state mutation without accepted effects |
| Canonical events and replay | MODERATE | Core meaning is clarified; implementations still differ in replay architecture and completeness |
| Relationships and authorization-aware traversal | EXPERIMENTAL | Draft2 resolved version/history/replay divergence. Draft2.1 resolved multiplicity and no-op divergence, and both runtimes pass the experimental draft2.1 harness. Draft2.2 changes no relationship-set semantics |
| Application authority/interchangeability | MODERATE | Demonstrated with two applications in one domain |
| Delegated Authority | MODERATE | Demonstrated recursively, with implementation-specific trust root and depth limits |
| Agent Operation | MODERATE | Deterministic and fake-model paths use one tool/session design |
| External Observation | MODERATE | Strong separation, authority, deduplication, provenance, and replay evidence; validation and review authorization are partial |
| Provider Binding | MODERATE | Demonstrated across fake reservation and contract providers |
| Canonical Authority | MODERATE | Strong tests, but only fake providers and one runtime path |
| Type Version and evolution | EXPERIMENTAL | Draft2 clarifies binding and coexistence; the published Python runtime stores and checks an explicit Type Version, but neither runtime demonstrates full migration |
| Semantic portability | EXPERIMENTAL | Draft2.2 defines restoration meaning and an optional exact-token continuity profile from experimental two-runtime evidence (four directions, including re-export). Encoding, transfer protocol, token remapping, and bundle atomicity remain unresolved |
| Disclosure-safe rejection and revision-unique tokens | EXPERIMENTAL | Normative in draft2.2. Known runtime gaps exist. The open Core conformance suite (0.1.0) has run against both runtimes for draft2.2; current results are recorded by the suite, not by this table. See [Implementation Notes](implementation-notes.md#draft22-known-runtime-status) |
| Stable wire encoding | UNRESOLVED | No independent interoperability evidence |
| Conformance harness execution | EXPERIMENTAL | An open Core conformance suite (0.1.0) exists and has run against both runtimes; results are self-attested suite output, not independent third-party execution |

## Non-Normative Two-Implementation Evidence

Two implementations separately written by the specification's maintainer — a Go Core implementation and the originating Python runtime — matched 24 of 30 comparatively testable requirement areas (80%), an unpublished internal comparison. Divergence was concentrated in relationship mutation/version/history, outcome classification, replay baselines, and Type Version handling. This is evidence used to improve semantic precision, not a formal conformance score or a claim of interoperability. It predates draft2.1 and is not a draft2.1 or draft2.2 result.

Later experimental interchange work exported, restored, and continued operating objects between the two runtimes against draft2.1. It exposed the Core gaps draft2.2 addresses. That work is evidence for this revision. It is not conformance evidence and does not establish interoperability.

## Explicit Non-Goals

`v0alpha1 draft2.2` does not standardize:

- a programming language, SDK, class/struct hierarchy, package layout, or method name;
- database technology, table layout, relationship store, event store, cache, audit layout, or storage engine;
- runtime deployment topology, high availability, Kubernetes, or container orchestration;
- a policy engine, policy language, or validation library;
- an OIDC provider, identity protocol, cryptographic identity, signature, or encryption scheme;
- a provider API protocol, webhook transport, queue, or event broker;
- an LLM provider, model protocol, prompt format, or tool-calling protocol;
- a user interface or command-line interface;
- exact outcome labels, version-token representation, snapshot/event architecture, or serialization layouts;
- relationship edge identifiers, normative relationship attributes, per-type relationship cardinality, or a relationship-type namespace;
- graph-dependent governance or cross-object dependency semantics;
- multi-object or bundle transactional semantics, replication, multi-master operation, or history merge;
- Object Version remapping, history compaction, or type migration during restoration;
- the current Python YAML/JSON/filesystem layouts or the Go JSON fixture names;
- mandatory business types such as reservations, contracts, mandates, or containers;
- foundation governance, a certification body, licensing, or commercial models.

## Serialization Status

`v0alpha1 draft2.2` defines semantic portability and restoration meaning but does not define a stable normative interchange encoding, an import/export API, or a transfer protocol. Draft 2.2 does not establish a normative wire format. The experimental interchange JSON used as evidence is not normative. YAML, JSON, CBOR, and other encodings remain possible future work. Examples in these documents are non-normative and illustrate behavior only.

An independent team can implement the abstract semantics in another language from this draft. An open Core conformance suite (0.1.0) exists and has run against both runtimes, but no stable wire encoding exists, so a new implementation cannot yet demonstrate wire-level interoperability or final conformance with another runtime because type packages, governance packages, history encoding, and capability declarations remain unresolved.

## Non-Normative Architectural Analogy

Desired/accepted state, observed external state, controllers, reconciliation, typed resources, and version preconditions have useful parallels with Kubernetes. This analogy does not define Governed Object semantics, and no conforming implementation is required to use Kubernetes.
