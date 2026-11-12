# Canonical Events and Replay

**Version:** v0alpha1  
**Revision:** draft2.2  
**Profile:** Core, with profile-specific event requirements  
**Evidence confidence:** MODERATE

## Canonical History Model

Canonical History represents accepted canonical change. A runtime may use snapshots, event streams, materialized state, checkpoints, or combinations of them. `v0alpha1 draft2.2` does not require event sourcing, a particular event envelope, a particular creation-event name, or one database table.

## Normative Requirements

**EVT-ACCEPT-001 — Canonical events represent acceptance**  
A Canonical Event MUST represent an accepted semantic change to a Governed Object. An authorization denial, semantic invalidity, conflict, external observation, provider repair record, or model trace MUST NOT be classified as a Canonical Event unless it separately records an accepted canonical change.

**EVT-CONTENT-001 — Sufficient accepted-change semantics**  
Canonical History MUST preserve enough information to determine the affected Canonical Identity, applicable Type Identity and Type Version, accepted semantic effect, resulting Object Version, deterministic per-object order, and available provenance. Its minimum semantic content MUST cover the Replay Baseline or creation semantics and every accepted state, lifecycle, and Canonical Relationship addition or removal. It MUST NOT require provider-native field names to interpret canonical meaning. The accepted semantic effect, resulting Object Version, and order are the canonical content of an accepted change. Provenance is evidence about an accepted change, not part of its accepted effect. It includes actor, timestamps, action or command names, source or record identifiers, provider evidence, and diagnostics. Replay and history equivalence MUST be determined from accepted effects, resulting Object Versions, and order, and MUST NOT depend on provenance. Which provenance is retained is implementation-defined, except where another requirement or a claimed profile requires it.

**EVT-SEPARATE-001 — Operational evidence remains separate**  
Implementations MAY retain authorization denials, semantic invalidity, conflicts, reads, observations, synchronization attempts, debug records, and model traces for audit. Such records MUST remain distinguishable from Canonical History and MUST NOT affect replay unless a separate accepted canonical change occurred.

**EVT-BASELINE-001 — Replay Baseline**  
An implementation claiming canonical-history replay MUST provide a Replay Baseline sufficient to reconstruct the initial accepted canonical representation and its Type Identity and Type Version. The baseline MAY be an initial accepted snapshot followed by subsequent canonical changes, or a creation event followed by complete Canonical History. No event named or shaped as `object_created` is required. The initial accepted canonical representation MAY include Canonical Relationships. Such relationships are initial canonical content of the baseline revision, not relationship additions under `REL-ACCEPT-001`, and replay begins from them.

**EVT-REPLAY-001 — Provider-independent replay**  
An implementation claiming canonical-history replay MUST reconstruct Accepted Canonical State and Canonical Relationships from the Replay Baseline and Canonical History without consulting an external provider, model trace, debug record, or provider synchronization log. Authorization-denied, semantically-invalid, and conflicting operations MUST NOT change the reconstructed canonical representation.

**EVT-ORDER-001 — Deterministic per-object order**  
Canonical History MUST provide a deterministic per-object order of accepted canonical mutations sufficient to interpret object evolution and any claimed replay. That order MUST be the order in which the changes were accepted, and MUST be part of Canonical History itself. It MUST NOT be inferred solely from wall-clock or provenance timestamps, from Object Version values, or from storage or enumeration order that the history representation does not define as its order. Accepted histories containing the same changes in a different order are different histories, even if they produce the same Accepted Canonical State and Canonical Relationships. No global order, wall-clock order, or distributed-log architecture is required.

**EVT-OBS-001 — Accepted observations enter history**  
An implementation claiming the External Observation Profile MUST represent each accepted external semantic change in Canonical History with available observation and provider provenance. Rejected, stale, duplicate, and unresolved observations MUST NOT become accepted state transitions.

**EVT-REPAIR-001 — Synchronization does not invent history**  
Synchronizing a Provider Projection to already Accepted Canonical State MUST NOT create a duplicate Canonical Event or otherwise imply that Accepted Canonical State changed.

## Replay and Detachment

`CORE-HIST-001` makes Canonical History organization-controlled, and `PB-DETACH-001` preserves it across provider detachment. Type-specific reducers, snapshot restoration, event folding, materialized views, and checkpoints are implementation techniques, not requirements.

## Relationship History and Replay

`REL-REPLAY-001` narrows relationship history. Canonical History records each accepted relationship addition or removal as a distinct accepted change and never records an accepted no-op (`REL-NOOP-001`) as either. This is semantic content: a separate physical event, envelope, or storage transaction per change is not required. Replay reconstructs the Canonical Relationships as a set; enumeration order is not significant (`REL-ORDER-001`). Folding history keyed by the Relationship Equality Key is one permitted technique, not a requirement.

History produced under rules that allowed duplicate identical relationships is governed by `REL-COMPAT-001`. It must not be silently deduplicated during replay or import.

Relationships present in the creation or Replay Baseline revision are initial content (`EVT-BASELINE-001`, `REL-REPLAY-001`). They are not recorded or replayed as additions. An implementation that cannot accept relationships in a baseline refuses such content explicitly, for example at restoration (`PORT-PRESERVE-001`). It does not convert them into additions.

## History Order, Effects, and Provenance

Order is part of the history, not a property recomputed from other fields (`EVT-ORDER-001`):

- Timestamps may be equal, reversed, or in the future relative to acceptance order, and the order does not change.
- Object Version values are opaque. Their comparison implies nothing about order.
- A serialization's array or element order is the history order only when that representation explicitly defines it so.
- Two histories with the same final state but different orders are different governed histories.

`EVT-CONTENT-001` separates the canonical content of each accepted change from its provenance:

- **Canonical content:** the accepted semantic effect (the resulting state or lifecycle, or the relationship triple added or removed), the applicable Type Identity and Type Version, the resulting Object Version, and the change's position in the per-object order.
- **Provenance evidence:** actor, accepted-at and other timestamps, action or command name, source, event, or record identifiers, provider evidence, and diagnostics.

Provenance is not required beyond what Draft2.1 already required ("available provenance"). A profile may require specific provenance to be preserved when it claims to preserve it.

## Non-Normative Event Categories

```text
Canonical:
  replay baseline or creation semantics
  accepted lifecycle transition
  accepted field change
  accepted relationship addition
  accepted relationship removal
  accepted external observation

Non-canonical unless separately accepted:
  authorization-denied request
  semantically-invalid request
  stale-version conflict
  accepted relationship no-op (duplicate add, absent-edge remove)
  raw external observation
  review case
  provider repair request/result
  model/tool trace
  read audit
  restoration or import provenance
  provider binding or detachment
```

## Known Tension

The reference runtime stores accepted events, denials, conflicts, and read audits through one event model and filters accepted events during replay. It also falls back to stored materialized state for types without a registered reducer. The Go experiment uses a creation-led event fold. Both architectures remain permitted when they satisfy the baseline, content, order, and replay semantics above. See the Reference Runtime Gap Analysis (not published).
