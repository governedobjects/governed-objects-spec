# Core Governed Object Semantics

**Version:** v0alpha1  
**Revision:** draft2.2  
**Profile:** Core (required)  
**Evidence confidence:** STRONG overall; replay and type evolution are qualified separately

## Definition

A Core Governed Object is a typed, versioned representation of an organizational asset or business entity. Its durable canonical identity, Accepted Canonical State, Canonical Relationships, Type Metadata, governance semantics or references, and Canonical History are controlled independently of replaceable applications, providers, intelligence systems, and runtime implementations.

Core describes semantics, not an object envelope or API shape.

Accepted Canonical State and Canonical Relationships together form the durable canonical representation. They are distinct from Type Metadata, governance references, Canonical History, and Operational Metadata. Operational Metadata does not automatically become a canonical mutation or participate in Object Version, Canonical History, or replay.

## Normative Requirements

### Canonical Identity

**CORE-ID-001 — Durable identity**  
Every Governed Object MUST have a Canonical Identity that remains stable for the lifetime of that object. The specification does not constrain the identity to a UUID, integer, string, URI, or any particular syntax.

**CORE-ID-002 — Identity independence**  
Canonical Identity MUST be semantically distinct from identifiers assigned by applications, external providers, storage systems, or a particular runtime implementation. Rebinding or replacing one of those systems MUST NOT, by itself, replace Canonical Identity.

### Accepted Canonical State

**CORE-STATE-001 — Explicit accepted state**  
A Governed Object MUST have an identifiable Accepted Canonical State. An implementation MUST be able to distinguish that state from external observations, provider operational state, materialized caches, application-local views, model context, and synchronization metadata.

**CORE-STATE-002 — Governed acceptance**  
Accepted Canonical State MUST change only through a governed acceptance path that applies the authorization and declared validation semantics relevant to the change. Merely reading, hydrating, observing, or projecting external state MUST NOT silently change Accepted Canonical State.

### Governance

**CORE-GOV-001 — Governed boundary**  
An implementation MUST associate operations that can read or alter a Governed Object with sufficient governance context to determine whether the operation is authorized and semantically valid. The policy language, evaluator, and storage representation are implementation-defined.

### Canonical History

**CORE-HIST-001 — Accepted history**  
An implementation MUST retain Canonical History of accepted canonical changes. At minimum, that history MUST preserve baseline or creation semantics, accepted state and lifecycle mutations, accepted relationship additions and removals, the Type Version applicable to each semantic effect, and the resulting Object Version. Rejected requests, authorization denials, semantic invalidity, conflicts, debug records, model traces, raw or unresolved provider observations, and provider synchronization records MAY be audited, but MUST NOT be treated as accepted changes unless a separate governed acceptance occurred. Canonical History MUST be complete: from the creation or Replay Baseline revision to the current revision, it MUST NOT omit any accepted canonical change.

**CORE-LINEAGE-001 — One Canonical History lineage**  
The accepted canonical revisions of a Governed Object MUST form one Canonical History lineage: a single sequence, in the order required by `EVT-ORDER-001`, that begins with the creation or Replay Baseline revision and continues through the revision produced by each accepted canonical change. Every Object Version an implementation exposes for the object MUST be the Object Version of a revision in that lineage, and each revision MUST have exactly one Object Version. The current Object Version therefore changes only when an accepted canonical change produces a new revision. An operation that changes only Operational Metadata MUST NOT produce a new Object Version. Implementations, individually or cooperating, MUST NOT accept canonical changes for one Canonical Identity in a way that produces more than one lineage. This requirement does not prescribe a history storage layout, history positions, record identifiers, or a mechanism for maintaining a single lineage.

### Evolution

**CORE-EVOL-001 — Preservation through evolution**  
Changing a runtime implementation, specification revision, or type revision MUST NOT implicitly replace Canonical Identity or make previously Accepted Canonical State and Canonical History inaccessible solely because the implementation changed. Conversion mechanisms remain unresolved, but any conversion used for portable evolution MUST be explicit and inspectable.

### Canonical History Lineage (Non-Normative)

`CORE-LINEAGE-001` states normatively what the Draft 2.1 definitions of Object Version and Operational Metadata already implied. An Object Version names a revision of the durable canonical representation. Each such revision is either the creation or Replay Baseline revision, or the result of an accepted canonical change. Those revisions form one line.

The rule does **not** mean that:

- every Operational Metadata change is Canonical History; it means only that such a change is not presented as a new Object Version;
- every provider event is business history; provider observations enter history only through a governed acceptance;
- history uses one event table, integer positions, a database sequence, record UUIDs, or any physical append structure;
- several stored records cannot together represent one accepted revision;
- Object Versions are ordered; they remain opaque and compared by equality only;
- an accepted no-op creates a revision; it exposes the existing Object Version (`REL-NOOP-001`).

The final clause, which forbids producing more than one lineage, states an invariant that any deployment topology must preserve. It is not directly testable through one implementation's black-box behavior. A conformance claim that relies on more than one accepting authority is outside the evidence for this revision. Replication and multi-authority operation are deferred ([Open Questions](open-questions.md#draft22-deferred-issues)).

## Required Semantic Components

A Core implementation therefore needs semantic equivalents of:

- Canonical Identity;
- Type Metadata comprising Type Identity and applicable Type Version;
- Accepted Canonical State;
- Canonical Relationships;
- Object Version for mutable objects;
- governance semantics or portable governance references and authorization context;
- declared validation and lifecycle semantics where applicable;
- Canonical History; and
- Operational Metadata kept semantically distinct from canonical meaning; and
- the ability to report accepted, authorization-denied, semantically-invalid, and conflicting operation outcomes where those outcomes apply.

The components may be stored together or separately. They need not be represented by one class, row, document, or process.

## Non-Normative Example: Basic Object

```text
Canonical Identity: asset-42
Type Identity:      example/Asset
Type Version:       revision A
Object Version:     8
Accepted State:     active
Relationships:      owned_by → party-7
Canonical History:  creation, activation
```

This example defines no required field names or encoding.

## Core Exclusions

Core does not require:

- provider binding or provider replacement;
- external observation ingestion;
- delegated mandates;
- application identity;
- agents or probabilistic models;
- graph traversal APIs;
- container admission or encryption;
- a network service;
- export, transfer, restoration, or runtime-replacement capability, which are conditional (`PORT-RESTORE-001`, `PORT-PRESERVE-001`) or belong to the optional [Portable Restoration Continuity Profile](profiles/portable-restoration-continuity.md); or
- a stable interchange encoding.

A small implementation may satisfy Core as an embedded library or single-process runtime.
