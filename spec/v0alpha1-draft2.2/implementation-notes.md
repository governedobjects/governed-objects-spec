# Reference Runtime Implementation Notes

**Version:** v0alpha1  
**Revision:** draft2.2  
**Status:** NON-NORMATIVE

The Python runtime is the source implementation from which draft1 was extracted. A Go Core experiment, separately written by the specification's maintainer, supplied differential evidence for draft2. Draft2.1 adds relationship-set semantics derived from the approved relationship analysis. Both runtimes demonstrate that many semantics are coherent, but neither runtime's code, data model, or operational choices are the specification.

This document makes no conformance claim for either runtime. Both runtimes have since been updated for draft2.2, and their current results come from running the open conformance suite, not from this document.

Implementation freedom is intentional. A conforming implementation may choose its language, classes or structs, package layout, database and storage architecture, policy engine, validation library, exact outcome labels, Object Version token representation, snapshot/event/materialization strategy, audit storage, and serialization, provided the normative semantic effects are preserved.

## Explicitly Non-Normative Choices

| Reference-runtime choice | Why it is not normative |
| --- | --- |
| Python | Other languages must be able to implement the same semantics |
| Pydantic models | Validation library and in-memory model are implementation choices |
| SQLite | Persistence engine and transaction model are not portable semantics |
| PyYAML and YAML type files | `v0alpha1` defines no stable type-package encoding |
| `src/governed_objects/` package layout | Repository organization is not an API |
| `GovernedObjectRuntime` and other class names | Class names and object-oriented design are not required |
| Method names such as `execute`, `replay`, `plan_repair`, and `apply_repair` | Behavior is specified abstractly, not as a Python API |
| `GovernedObject`, `CommandContext`, and `GovernanceEvent` field names | Semantic inputs and outputs may have different representations |
| SQLite table and column names | Database organization is private to the implementation |
| One SQLite database | Embedded, service, federated, and distributed implementations remain possible |
| Current event reducers | Reducers are one replay technique |
| Go structs, interfaces, and in-memory records | The clean-room architecture is comparative evidence, not a required object or storage model |
| Go JSON fixture field names and document shape | The Go experiment did not define the portable encoding |
| Python serialization field names and document shape | Existing Python/YAML/JSON forms do not define the portable encoding |
| Different authorization/version ordering for transitions and container puts | Check ordering is not a portable command taxonomy |
| Current application registry | Application identity and authority may be resolved differently |
| Local actor YAML files | Identity and authentication protocols are unresolved |
| Capability-list policy evaluator | Other policy languages and engines may satisfy authorization semantics |
| `MandateProjection` cache | Delegated-authority indexing is implementation-defined |
| `MandateResolver` class | A trust root is required semantically; this class is not |
| Delegation depth limits (`2` and resolver safety limit `4`) | No portable maximum depth is established |
| Immediate-parent effective-authority intersection | Full upstream narrowing is the profile requirement; current chain mechanics are partial |
| Fake reservation/contract providers | Provider interfaces and native schemas are test fixtures |
| Fake-provider API methods | `fetch`, `apply_action`, `apply_patch`, and `create_from_canonical` are not provider standards |
| Provider-name branching such as `endswith("-b")` | Demo mapper selection is not portable provider behavior |
| Provider capability constants | Capability discovery and certification remain open |
| Observation acceptance without a canonical precondition | Observation/concurrent-command arbitration remains unresolved |

## Draft2.1 Relationship Implementation Status

This section is non-normative. It records what source inspection and probes recorded in an analysis document (not published) showed about the unchanged reference runtimes. It is not a conformance result: no draft2.1 conformance target exists yet.

| Behavior | Python reference runtime (draft2 baseline) | Go reference runtime (draft2 baseline) |
| --- | --- | --- |
| Multiplicity (`REL-MULT-001`) | At most one canonical edge per triple | Appends a second identical edge, which is non-conforming to draft2.1 |
| Duplicate add (`REL-DUP-001`) | Accepted; no version advance and no history | Accepted as an effective addition with a version advance and history entry, which is non-conforming to draft2.1 |
| Absent-edge removal (`REL-ABSENT-001`) | Accepted no-op | Accepted no-op |
| Authorization of no-op requests (`REL-AUTH-001`) | Authorization evaluated before the existence check | Authorization evaluated; version check first |
| Stale no-op requests (`REL-PRECOND-001`) | Conflict | Conflict |
| Replay (`REL-REPLAY-001`) | Set fold | Multiset fold; follows from duplicate append |

Both runtimes' draft2 behavior remains as it was. Go's duplicate append was permitted by draft2 and remains draft2-compatible. Neither runtime had been evaluated against draft2.1 when this section was written.

> **Draft2.2 note:** This section is carried forward from draft2.1 unchanged apart from this note. Later draft2.1-line runtime tags pass the experimental draft2.1 behavioral harness. Those are draft2.1 results, not draft2.2 results. Draft2.2 changes no relationship-set semantics.

Python validates request shape, such as an empty type or target, before authorization. `REL-PRECEDENCE-001` permits this because the check does not depend on relationship existence.

Relationship data produced by the unchanged Go reference runtime can contain repeated identical additions. Importing, converting, or replaying that data under draft2.1 is governed by `REL-COMPAT-001`. It must be refused or explicitly converted with a record, never silently deduplicated, and draft2.1 defines no conversion procedure.

### Separate High-Priority Draft2 Defect: Python Provider-Edge Collision

The approved relationship analysis found a defect in an earlier Python runtime snapshot. When provider-edge synchronization reported a relationship whose source, type, and target equal an accepted Canonical Relationship, the provider record replaced the canonical record in storage. The accepted relationship then silently disappeared from the object's Canonical Relationships, with no Object Version advance and no Canonical History entry.

- It violated existing draft2 requirements `REL-REMOVE-001` and `OBS-REL-001`, and therefore also draft2.1.
- It affected provider-bound objects only. The Core fixture types used by the unchanged draft2 harness did not exercise this path.

**Current status:** read-only inspection of the published Python runtime (`relationships/sync.py`) shows that provider-edge synchronization now tracks non-provider-owned relationships as protected and skips a colliding provider edge rather than replacing it, and a dedicated test exercises this path. This has not been executed as part of this revision; no provider-bound relationship interoperability claim should rest on this code path without running that test.

## Draft2.2 Known Runtime Status

This section is non-normative. It records behavior of the Python reference runtime and the Go clean-room runtime that draft2.2 makes non-conforming. The behavior was observed at their draft2.1 baseline and later interchange snapshots, which were identical for every executed item. Both runtimes have since been updated for draft2.2, and their current results come from the conformance suite, not from this section. This section is a record of the observations as made against the draft2.1 baseline. None of this is a draft2.2 conformance result.

### Confirmed by execution

| # | Runtime | Behavior | Draft2.2 requirement |
| --- | --- | --- | --- |
| E1 | Go | Returns the complete object record (state, relationships, history) and the current Object Version in every mutation rejection, and the Object Version in a read denial, to requesters who cannot read | `AUTH-DISCLOSE-001` clause 1, `AUTH-QUERY-001` |
| E2 | Go | Evaluates expected-version preconditions before authorization. This gives a requester that can neither read nor mutate a stale-versus-current token oracle on all four mutations | `AUTH-DISCLOSE-001` clause 2 (`AUTH-ORDER-001` recommends the fix) |
| E3 | Python | Returns Object Version and lifecycle state, and state-bearing denial events, in every rejection, including read denial, to requesters who cannot read | `AUTH-DISCLOSE-001` clause 1, `AUTH-QUERY-001` |
| E4 | Python | Container `put` evaluates the precondition before policy. It returns CONFLICT with the actual version to a requester without authority, which is a token oracle | `AUTH-DISCLOSE-001` clauses 1 and 2 |
| E5 | Python | Provider detach advances Object Version without an accepted revision, so a subsequent precondition on the pre-detach version spuriously conflicts | `CORE-LINEAGE-001`, `PB-DETACH-001` |

### Found by code inspection only

| # | Runtime | Behavior | Draft2.2 requirement |
| --- | --- | --- | --- |
| I1 | Python | The legacy `EventReplayer` orders history by timestamp. This is non-conforming only if it is a claimed replay path | `EVT-ORDER-001` |

### Theoretical

| # | Behavior | Draft2.2 requirement |
| --- | --- | --- |
| T1 | Content-hash or state-derived tokens, which repeat when state returns to an earlier value | `CON-UNIQUE-001` |
| T2 | Multi-writer deployments that fork an object's lineage | `CORE-LINEAGE-001` |
| T3 | Object Version implemented as a storage row version that also covers operational metadata. This was already contrary to the draft2.1 definition | `CORE-LINEAGE-001` |
| T4 | Reissuing a new Object Version for an unchanged revision, for example on restart, reload, or storage relocation, which makes a current precondition spuriously conflict. Found in adversarial review | `CORE-LINEAGE-001` (one Object Version per revision) |

### Not non-conforming

- Both runtimes return CONFLICT to a requester that can read but not mutate and supplies a stale version, and Python container `put` does the same. This deviates from the `AUTH-ORDER-001` SHOULD only. Such a requester can already read the version.
- Both runtimes let a requester distinguish whether an object exists. Existence concealment remains policy-conditional (`AUTH-QUERY-001`).
- A runtime that cannot hold another runtime's token form cannot claim the Restorer role. That is not a Core defect.
- Both runtimes add operational audit rows on rejection. That remains permitted (`OP-NOCHANGE-001`).

### Interchange evidence

Experimental interchange work restored objects between the two runtimes in all four directions against draft2.1. It preserved histories and exact positive 64-bit integer tokens, continued governed operation, and re-exported. That evidence informed the Portable Restoration Continuity Profile. It does not make integer tokens normative, is not a profile conformance result, and does not establish interoperability.

## Reference Business Types

Earlier development used several non-Core example business types, not in the published runtime, to demonstrate that one runtime can support different domains. None was a required Core type. `DelegatedMandate`, which is in the published runtime, supplies evidence for the Delegated Authority Profile but its exact shape is not mandatory.

Field names, lifecycle states, permissions, provider mappings, and relationships for any such type are non-normative examples.

## Deployment

A conforming implementation might be:

- an embedded library;
- a single-process daemon;
- a standalone server;
- a high-availability distributed control plane; or
- a managed service.

`v0alpha1 draft2.2` requires none of these deployment forms and does not require Kubernetes, containers, a service mesh, or a particular operating system.

## Useful but Non-Normative Analogies

Kubernetes concepts such as desired state, observed state, controllers, reconciliation, resource versions, and typed APIs informed parts of the architecture. This analogy is not a normative description.

## Evidence Use

Reference tests are cited in [Conformance](conformance.md) only when they demonstrate observable semantics. Tests for Python types, SQL persistence details, CLI formatting, exact enum strings, specific fake-provider records, and file layouts are implementation-correctness evidence rather than portable conformance requirements.
