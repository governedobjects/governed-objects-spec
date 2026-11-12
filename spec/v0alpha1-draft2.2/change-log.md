# Draft2.2 Change Log, Requirement Inventory, and Normative Traceability

**Version:** v0alpha1  
**Revision:** draft2.2  
**Status:** Normative-change evidence record

Adversarial-review corrections are recorded in the Review Log (not published).

This record covers the changes from `v0alpha1 draft2.1` to **Governed Objects Specification v0alpha1 Draft 2.2**. The draft2-to-draft2.1 change log is part of the frozen draft2.1 specification (earlier draft, not published). It is not repeated here.

Per-requirement detail is in the Revision Map (not published) and the machine-readable [requirement inventory](requirement-inventory.json). Decision-level detail is in Decision Traceability (not published).

## Revision Statement

- **Draft2.1 remains frozen.** `v0alpha1 draft2.1` (earlier draft, not published) is unchanged. Draft2.2 is a separate, complete, self-contained revision in `spec/v0alpha1-draft2.2/`.
- **Minimum semantic change.** Draft2.2 adds the Core rules that experimental portable-restoration evidence between two runtimes showed to be missing or ambiguous. It adds one optional profile. Nothing else in draft2.1 is reorganized.
- **Stable identifiers.** No draft2.1 requirement is removed, renumbered, or retitled. Changed requirements keep their identifiers. Unchanged requirements keep byte-identical normative text, except `TYPE-ID-001`, whose only change is the revision label.
- **No blanket backward compatibility.** Some draft2.1-conforming behavior does not conform to draft2.2 ([Compatibility](#compatibility)).
- **Separate labels.** Draft2, draft2.1, and draft2.2 results are labeled separately (`CONF-CLAIM-001`).
- **Not final.** Draft2.2 is proposed for review. It is not final, frozen, or certified. The new profile is a semantic profile, not a wire protocol, and no interoperability is claimed.
- **Draft3 excluded.** Graph-dependent governance, cross-object dependency semantics, and multi-object transactions are not included ([Draft 3 Boundary](#draft-3-boundary)).
- **Decision basis.** The interchange synthesis and its decision closure, which concluded "READY TO DRAFT 2.2". They are not published. They rest on frozen earlier interchange evidence, and on executed closure probes against the draft2.1 baseline and later interchange runtime snapshots.

## Evidence Standard

Draft2.2 uses the draft2 and draft2.1 evidence-basis categories:

- **TWO-IMPLEMENTATION EVIDENCE**: both runtimes exported, restored, continued, and re-exported objects in four directions. Closure probes executed disclosure and detach behavior.
- **CLEAN-ROOM AMBIGUITY**: draft2.1 did not say whether an Object Version may recur when state recurs, whether history must be complete, how order is established, or what restoration means.
- **FOUNDING-PRINCIPLE REQUIREMENT**: accepted history survives replacement, and implementations must be replaceable.
- **EDITORIAL CLARIFICATION**

Implementation behavior informs the wording. It does not become normative merely because a runtime chose it. In particular, the experimental interchange JSON, its positive 64-bit integer tokens, and its contiguous history positions are not normative.

## Contradiction Review

Two contradictions arose while drafting. Both concern the disclosure rule. Both were resolved with the smallest wording change. No closure decision was overridden.

- **Stale and unauthorized.** Draft2.1 `CON-CONFLICT-001` says a stale conditional mutation MUST return CONFLICT. `AUTH-DISCLOSE-001` clause 2 requires a requester that can neither read nor mutate to receive the same outcome for a stale and a current expected version. For such a requester, that outcome is in practice an authorization denial. Read literally, the two cannot both hold.
  - The tension already existed in draft2.1. Its "Information Ordering" prose left check order open, the Python lifecycle path authorized first, and `REL-PRECEDENCE-001` left the choice among rejections implementation-defined for relationships.
  - **Resolution:** `CON-CONFLICT-001` is CLARIFIED. When another rejection outcome also applies, the implementation MAY report it instead, subject to `AUTH-DISCLOSE-001`. A stale conditional mutation is still never accepted.

- **Writer without read authority.** The closure's outcome-invariance rule says a non-reader's outcome must not vary with protected information. That includes whether its expected version matches. But `CON-CONFLICT-001` requires a stale conditional mutation to conflict and a current one to proceed. For a requester authorized to mutate but not to read, both cannot hold.
  - **Resolution:** `AUTH-DISCLOSE-001` splits the rule. Clause 1, the content restriction, applies to every requester lacking read authority. Clause 2, outcome invariance, applies only when the requester also lacks authority for the requested operation. That is the case the closure evidence (E2, E4) concerns.
  - The remaining inference by write-only requesters is recorded as deferred. It is the same limitation draft2.1 recorded for accepted no-ops.
- **Existence concealment.** `AUTH-DISCLOSE-001` protects object content and the Object Version. Existence concealment stays policy-conditional under the unchanged `AUTH-QUERY-001` SHOULD. The closure did not require global existence concealment.

The draft2.1 explanatory sections "Information Ordering" (authorization) and the "Object Version mapping unresolved" paragraph (portability) are replaced, not contradicted. Draft2.1 left both matters open.

## Requirement Accounting

| Category | Count |
| --- | --- |
| Draft2.1 requirements | 96 |
| Carried forward UNCHANGED | 79 (78 byte-identical; 1 revision label only) |
| CLARIFIED | 13 |
| MATERIALLY_CHANGED | 4 |
| Removed, renumbered, or retitled | 0 |
| NEW Core | 6 |
| NEW profile (Portable Restoration Continuity) | 19 |
| **Draft2.2 total** | **121** (66 Core, 55 optional profile) |

Core: 60 draft2.1 Core requirements, plus 6 new. Profiles: 36 draft2.1 profile requirements (Provider Binding 7, Canonical Authority 10, External Observation 10, Delegated Authority 5, Agent Operation 4), plus 19.

### Reconciliation With the Closure Estimate

The closure estimated 76 unchanged, 16 clarified, 4 materially changed, and 5 new Core. Draft2.2 differs in two deliberate ways:

- **Three fewer clarifications.** The closure's 16 clarified requirements include `CON-CONFLICT-001`, `CORE-EVOL-001`, `PORT-RUNTIME-001`, and `PORT-EVOL-001`. Of these, `CON-CONFLICT-001` is clarified in draft2.2, but for a different reason than the synthesis gave: to resolve the stale-and-unauthorized contradiction. The synthesis's note about historical and never-issued tokens is carried in prose. The other three keep byte-identical text. Their proposed clarifications were pointers or notes that do not change obligations, so they are carried in prose (Revision Map, not published). The type-binding clarification proposed for `PORT-EVOL-001` is placed in `TYPE-BIND-001`, which already governs binding changes.
- **One more new Core requirement.** The closure's "disclosure-safe rejection" rule combined a MUST with an authorization-first SHOULD. Draft2.2 states the SHOULD separately as `AUTH-ORDER-001`, so that the MUST and the SHOULD can be tested and reported separately. No additional obligation results.

### Inherited Requirements with Unchanged Wording (79)

These requirements keep byte-identical normative text: the 78 listed by file in the Revision Map (not published). `TYPE-ID-001` changes only `v0alpha1 draft2.1` to `v0alpha1 draft2.2`.

### Clarified Requirements (13)

Each clarification states what draft2.1 intended or left ambiguous. Some make previously ambiguous behavior non-conforming ([Compatibility](#compatibility)).

| ID | Clarification |
| --- | --- |
| `CORE-HIST-001` | Canonical History is complete from the creation or Replay Baseline revision to the current revision |
| `CON-CONFLICT-001` | When another rejection outcome also applies, it may be reported instead, subject to `AUTH-DISCLOSE-001`. A stale conditional mutation is still never accepted |
| `CON-ADVANCE-001` | The new Object Version is subject to `CON-UNIQUE-001` |
| `OP-OUTCOME-001` | When the conditions of several non-accepted classes apply, any applicable one may be reported, subject to `AUTH-DISCLOSE-001` and `REL-PRECEDENCE-001`. Additional non-accepted outcomes, such as an implementation or resource refusal, may exist. They are never reported as ACCEPTED or as an inapplicable minimum class |
| `EVT-ORDER-001` | The order is the acceptance order and part of history itself. It is not inferred from timestamps, Object Version values, or undefined storage or enumeration order. A different order is a different history |
| `EVT-CONTENT-001` | Accepted effect, resulting Object Version, and order are the canonical content. Provenance is evidence. Replay and history equivalence do not depend on provenance |
| `EVT-BASELINE-001` | Baseline relationships are initial canonical content, not additions |
| `REL-REPLAY-001` | Baseline relationships are preserved and replayed as baseline content |
| `PORT-SEM-001` | Revision label. Preserving history means complete, ordered history with effects and resulting Object Versions. State equality and snapshot-only exports do not satisfy it. Core does not require token value retention |
| `TYPE-BIND-001` | Export, transfer, and restoration do not by themselves change Type Identity or Type Version |
| `AUTH-BASE-001` | Historical actors, restored content, and governance references grant no authority |
| `AUTH-QUERY-001` | Applies to read as well as query and traversal, and to denial details. Cross-references `AUTH-DISCLOSE-001`. The existence-concealment SHOULD is unchanged |
| `PB-DETACH-001` | A detachment that changes only the binding or Operational Metadata produces no new Object Version or history entry |

### Materially Changed Requirements (4)

| ID | Draft2.1 | Draft2.2 |
| --- | --- | --- |
| `OP-NOCHANGE-001` | Covered authorization-denied, semantically-invalid, and conflicting mutations | Covers every mutation request that is not accepted, including refusals for implementation or resource limits |
| `CON-NOADVANCE-001` | Same three classes | Same widening as `OP-NOCHANGE-001` |
| `REL-PRECEDENCE-001` | Choice among rejections implementation-defined, except for relationship-existence disclosure | Also subject to `AUTH-DISCLOSE-001`. Refers to the `AUTH-ORDER-001` recommendation. The existence clause is unchanged |
| `CONF-CLAIM-001` | Identified draft2.1. Profiles and capabilities were claimed | Identifies draft2.2 and claims roles within role-based profiles. Draft2 and draft2.1 results are not draft2.2 results. Core implies no profile or role. Pairwise exchange evidence is not conformance |

## New Core Requirements (6)

### `CON-UNIQUE-001` — Revision-unique Object Version

Within one Canonical Identity, an Object Version identifies at most one accepted revision over the identity's lifetime, including when state returns to an earlier value and for restored history. It constrains equality only. It does not require numeric, ordered, monotonic, or sortable tokens, or any allocation method.
**Basis:** the interchange synthesis and decision closure (not published). Theoretical break T1.

### `CORE-LINEAGE-001` — One Canonical History lineage

Accepted revisions form one lineage from the creation or baseline revision. Every exposed Object Version is that of a revision on it, and each revision has exactly one Object Version. Operational-Metadata-only operations produce no new Object Version. No implementation, alone or cooperating, produces a second lineage. No mechanism is prescribed.
**Basis:** the interchange synthesis and decision closure (not published). Breaks E5, T2, T3.

### `AUTH-DISCLOSE-001` — Disclosure-safe rejection

Clause 1: no protected information in rejection content for non-readers. Clause 2: no outcome variation with protected information for requesters that can neither read nor perform the operation. It applies to read, query, and mutation. Existence concealment is not required.
**Basis:** the interchange synthesis and decision closure (not published). Breaks E1 to E4.

### `AUTH-ORDER-001` — Authorization-first evaluation (SHOULD)

Establish authority before disclosure-capable checks. Other orders conform if `AUTH-DISCLOSE-001` holds.
**Basis:** the interchange synthesis and decision closure (not published).

### `PORT-RESTORE-001` — Restoration is not a canonical change (conditional)

Where restoration is performed: no history entry, no new revision, and no attribution to historical actors. Restoration is not required.
**Basis:** the interchange synthesis and decision closure (not published).

### `PORT-PRESERVE-001` — Faithful preservation or explicit refusal (conditional)

Where preservation is claimed: preserve faithfully or refuse with a reason. There is no silent omission, truncation, normalization, coercion, reordering, reinterpretation, or replacement. A declared and identified limitation is not silent.
**Basis:** the interchange synthesis and decision closure (not published).

## New Optional Profile: Portable Restoration Continuity (19)

[Portable Restoration Continuity Profile](profiles/portable-restoration-continuity.md). The profile has two roles. The Exporter produces a complete governed-read representation. The Restorer restores it with exact tokens, validates, refuses explicitly, and continues operation under a declared single-authority handoff. It is encoding-neutral.

| Group | Requirements |
| --- | --- |
| Claims | `PRC-CLAIM-001` |
| Export | `PRC-EXPORT-001`, `PRC-EXPORT-002` |
| History | `PRC-HIST-001`, `PRC-ORDER-001` |
| Object Version | `PRC-TOKEN-001`, `PRC-TOKEN-002` |
| Governance | `PRC-GOV-001` |
| Capability and refusal | `PRC-CAP-001`, `PRC-REFUSE-001` |
| Restoration | `PRC-NEW-001`, `PRC-VALID-001`, `PRC-AUTH-001`, `PRC-NOEXEC-001`, `PRC-NOREV-001`, `PRC-ATOMIC-001` |
| Evidence | `PRC-EVID-001` |
| Handoff and continuity | `PRC-HANDOFF-001`, `PRC-CONT-001` |

## Non-Requirement Changes

- **specification.md:** Draft 2.2 title and status. New "Relationship to Draft2.1" section; the draft2 section is condensed. Scope, document map, profile table, conditional capabilities, boundary and confidence rows, non-goals, and serialization status are updated.
- **terminology.md:** new terms Canonical History Lineage, Protected Information, Portable Representation, Restoration, Exporter, and Restorer. The Object Version definition cross-references `CON-UNIQUE-001`.
- **core.md:** non-normative "Canonical History Lineage" section. The Core exclusions list covers export, restoration, and runtime replacement.
- **concurrency.md:** returned-state example, a historical-and-never-issued-token note, a counter-allocation note, and Operational Metadata prose.
- **authorization.md:** "Information Ordering" is replaced by "Disclosure and Check Ordering". New "Historical Actors" section.
- **operations.md:** reordering prose points to `AUTH-DISCLOSE-001`. New "Other Non-Accepted Outcomes" section. No-op scope is stated as unchanged.
- **events.md:** baseline-relationship prose, and a "History Order, Effects, and Provenance" section. Two categories are added to the non-canonical list.
- **relationships.md:** revision label and evidence qualification.
- **portability.md:** the "Object Version mapping unresolved" paragraph is replaced by "Object Version Across Export and Restoration". New "Restoration and Preservation" section. The encoding status states that no normative wire format exists.
- **provider-binding.md:** binding and detachment are operational. Detach authority is undefined.
- **conformance.md:** draft2.2 candidates point to the Conformance Plan (not published), with draft2.2 result labeling.
- **open-questions.md:** new "Draft2.2 Deferred Issues" section. The Object Version mapping row is marked partly resolved. The tensions and "must not be frozen" list are updated.
- **implementation-notes.md:** new "Draft2.2 Known Runtime Status" section.
- **Historical evidence documents** (three historical evidence documents, not published): header and a draft2.2 note only. Bodies are unchanged.
- **Other profile documents:** revision header only.
- **New documents:** the profile, Revision Map, Decision Traceability and Conformance Plan (not published), and [requirement-inventory.json](requirement-inventory.json).

## Compatibility

Draft2.2 does not claim that every draft2.1-conforming implementation conforms to draft2.2. Behavior draft2.1 permitted stays conformant to draft2.1. Known behavior that draft2.2 makes non-conforming ([Implementation Notes](implementation-notes.md#draft22-known-runtime-status)):

| Source | Behavior | Rule |
| --- | --- | --- |
| E1 (Go, executed) | Full record and version in rejections to non-readers | `AUTH-DISCLOSE-001` clause 1 |
| E2 (Go, executed) | Precondition before authorization, which gives non-readers an oracle | `AUTH-DISCLOSE-001` clause 2 |
| E3 (Python, executed) | Version and lifecycle state in rejections to non-readers | `AUTH-DISCLOSE-001` clause 1 |
| E4 (Python, executed) | Container `put` token oracle | `AUTH-DISCLOSE-001` |
| E5 (Python, executed) | Detach advances Object Version | `CORE-LINEAGE-001`, `PB-DETACH-001` |
| I1 (Python, code inspection) | Timestamp-ordered legacy replayer, only if it is a claimed replay path | `EVT-ORDER-001` |
| T1 (theoretical) | State-derived tokens | `CON-UNIQUE-001` |
| T2 (theoretical) | Lineage-forking multi-writer deployments | `CORE-LINEAGE-001` |
| T3 (theoretical) | Row versions that also cover operational metadata | `CORE-LINEAGE-001` |
| T4 (theoretical, found in adversarial review) | A new Object Version for an unchanged revision, for example on restart or relocation | `CORE-LINEAGE-001` |

Not non-conforming: CONFLICT to a reader lacking mutate authority (a SHOULD deviation from `AUTH-ORDER-001`); existence distinguishability; inability to hold another runtime's token form (such a runtime just cannot claim the Restorer role); and audit rows on rejection.

At the time this section was written, neither runtime had been changed for draft2.2, and draft2.1 harness results remained draft2.1 results. Both runtimes have since been updated for draft2.2 and run against the open Core conformance suite (0.1.0); see [Known Runtime Status](#known-runtime-status).

## Draft 3 Boundary

Draft2.2 is a single-object revision. Every addition concerns one object's identity, revisions, history, governance reference, disclosure, or restoration. Draft2.2 does not introduce:

- graph-dependent governance, meaning authorization, validation, or lifecycle that depends on relationships or related objects;
- cross-object dependency semantics;
- multi-object or bundle transactional semantics. `PRC-ATOMIC-001` is per object with exact reporting only;
- replication, multi-master operation, or history merge. `PRC-HANDOFF-001` is a declaration only; or
- a normative encoding.

These remain separate design work.

## Categorized Registry

### MUST CLARIFY (resolved in draft2.2)

- Object Version revision uniqueness when state recurs, and across restored history
- Canonical History completeness, single lineage, and intrinsic order
- Accepted effect versus provenance
- Baseline relationships
- Disclosure through rejection content and outcome variation
- Effects of non-accepted outcomes outside the four minimum classes
- Meaning of restoration and of preservation claims
- Type binding across transfer
- Detachment as an Operational Metadata change

### SHOULD REMAIN FLEXIBLE

- Order of internal checks, where disclosure-safe (`AUTH-ORDER-001` recommends authorization first)
- Object Version syntax, domain, ordering, and allocation method
- Which provenance is retained, outside claimed profiles
- Outcome labels, including any exhaustion outcome

### DEFER

See [Open Questions](open-questions.md#draft22-deferred-issues): token remapping, snapshot-only restoration, compaction, bundle atomicity, encoding, token domain, evidence kinds and actor evidence, generalized no-ops, existence concealment, write-only disclosure, replication and multi-master operation, handoff fencing, detach authority, type migration during restoration, governance-reference syntax, and Draft3.

## Known Runtime Status

See [Implementation Notes](implementation-notes.md#draft22-known-runtime-status) for the draft2.1 baseline record described above. Current draft2.2 results for both runtimes come from the open Core conformance suite (0.1.0), not from this document. Runtime remediation is not part of this revision.
