# Conformance and Reference Evidence

**Version:** v0alpha1  
**Revision:** draft2.2  
**Status:** Candidate conformance design; an open Core conformance suite (0.1.0) exists at <https://github.com/governedobjects/governed-objects-conformance> and has been run against both runtimes

## Conformance Claims

**CONF-CLAIM-001 — Declared conformance scope**  
An implementation claiming conformance to this revision MUST:

1. identify **Version `v0alpha1`** and **Revision `draft2.2`** exactly;
2. claim Core;
3. identify each optional profile, each role within an optional profile that defines roles, each conditional Core capability, and each provider/dimension capability scope it claims;
4. satisfy every normative requirement applicable to those claims; and
5. disclose documented deviations, unsupported provider dimensions, and unresolved encoding assumptions.

A claim that identifies only `v0alpha1`, identifies another revision, or omits its revision is not a `v0alpha1 draft2.2` conformance claim. A result obtained against `v0alpha1 draft2` or `v0alpha1 draft2.1` MUST NOT be reported as a `v0alpha1 draft2.2` result, and conformance to one revision MUST NOT be inferred from conformance to another. A Core claim does not imply a claim of any optional profile or profile role. Evidence that two implementations exchanged objects successfully is not a conformance result for either. Because this revision is an Experimental Draft and no stable wire encoding exists, claims remain provisional. The existence of two implementations is evidence for the draft, not proof that either is conformant.

## Candidate Conformance Inventory

This inventory records implementation-neutral black-box candidates. It does not define a transport, adapter, process protocol, fixture encoding, event envelope, or wire format, and it does not turn reference-runtime test names into normative interfaces.

### Core, Types, Operations, Concurrency, Relationships, and History

| Evidence source | Requirements | Candidate black-box behavior |
| --- | --- | --- |
| Python identity/provider tests; Go identity tests | `CORE-ID-001`, `CORE-ID-002` | Canonical Identity remains stable and distinct from application, provider, storage, and runtime identifiers |
| Python accepted-state tests; Go mutation tests | `CORE-STATE-001`, `CORE-STATE-002` | Accepted state is distinguishable from operational metadata and changes only through governed acceptance |
| Python/Go governed mutation evidence | `OP-ACCEPT-001`, `AUTH-BASE-001` | An authorized, semantically valid mutation is accepted and changes the durable canonical representation |
| Python authorization tests; Go denied-operation tests | `OP-OUTCOME-001`, `OP-NOCHANGE-001` | Insufficient authority produces an authorization-denied outcome distinguishable from accepted, semantically-invalid, and conflict outcomes; labels are free |
| Python validation/lifecycle tests; Go invalid-operation tests | `TYPE-VALID-001`, `OP-OUTCOME-001`, `OP-NOCHANGE-001` | Structural/type, lifecycle, or domain invalidity produces a semantically-invalid outcome distinct from authorization denial and leaves canonical state, relationships, version, and history unchanged |
| Python stale-version tests; Go stale-mutation tests | `CON-CONFLICT-001`, `CON-NOADVANCE-001` | A stale supplied expected Object Version produces conflict and leaves canonical state, relationships, Object Version, history, and external side effects unchanged |
| Python and Go optional-precondition paths | `CON-VERSION-001` | A mutation interface that permits omission can accept an otherwise valid unconditional governed mutation; an interface may instead require an expected version |
| Python Type Version gap; Go explicit type metadata | `TYPE-ID-001`, `TYPE-VERSION-001` | Every portable object exposes Type Identity and Type Version, and Type Version is present independently of Object Version |
| Go type-version evidence plus negative candidate | `TYPE-VERSION-001` | Mutating an object changes Object Version without implicitly changing Type Version |
| Draft2 negative candidate | `TYPE-BIND-001` | Registering or activating a newer type implementation does not reinterpret an existing object absent an explicit governed conversion |
| Draft2 negative candidate | `TYPE-SUPPORT-001` | An unsupported bound Type Version produces an explicit unsupported outcome or refusal and is not interpreted as another version |
| Python extension tests; Go WorkItem/Party types | `TYPE-SEM-001`, `TYPE-EXT-001` | An independently defined business type participates without changing Core semantics or requiring a built-in business type |
| Go governed relationship add evidence | `REL-ACCEPT-001`, `CON-ADVANCE-001`, `OP-HIST-001` | An accepted relationship addition passes authorization/validation, changes the shared Object Version, enters Canonical History, and makes the prior version stale |
| Draft2 relationship removal candidate | `REL-REMOVE-001`, `CON-ADVANCE-001`, `OP-HIST-001` | An accepted relationship removal has the same governed, shared-version, and history effects as addition |
| Draft2 stale relationship candidate | `REL-ACCEPT-001`, `REL-REMOVE-001`, `CON-CONFLICT-001` | A stale supplied precondition for relationship add or remove conflicts without changing relationships, Object Version, history, or external effects |
| Draft2 candidate from the Python/Go history differential | `CORE-HIST-001`, `EVT-CONTENT-001` | Accepted relationship additions and removals are identifiable in Canonical History with Type Version, resulting Object Version, and deterministic per-object order |
| Python/Go replay evidence | `EVT-REPLAY-001` | Claimed replay reconstructs Accepted Canonical State and Canonical Relationships and excludes authorization denials, semantic invalidity, and conflicts |
| Go creation-led replay evidence | `EVT-BASELINE-001`, `EVT-REPLAY-001` | A creation-event baseline followed by complete history reconstructs the initial and current canonical representation |
| Python snapshot/materialized replay evidence | `EVT-BASELINE-001`, `EVT-REPLAY-001` | An initial accepted snapshot followed by canonical changes can serve as the Replay Baseline without requiring a specially named creation event |
| Draft2 history-order candidate | `EVT-ORDER-001` | Repeated reads of one object's Canonical History expose the same deterministic accepted-mutation order; no global or wall-clock order is assumed |
| Python application replacement tests | `PORT-APP-001`, `REL-PORT-001` | Replacing an application preserves canonical identity, state, relationships, governance, and continuous history |
| Semantic export candidate | `PORT-SEM-001` | An export claiming semantic portability preserves the required semantic inventory, including bound Type Version and relationships, without requiring a prescribed encoding |

Rows labeled "Draft2 ... candidate" originated in `v0alpha1 draft2`. They remain applicable to draft2.1 and draft2.2 unchanged.

### Draft2.1 Relationship-Set Candidates

These candidates are new in draft2.1 and unchanged in draft2.2. They are not executable scenarios. Each requires that the "observe" step be carried out through governed reads that the actor is authorized to perform. Object Versions are compared only for equality. Relationship collections are compared as sets.

| # | Obligation | Requirements | Candidate black-box behavior |
| --- | --- | --- | --- |
| 1 | Relationship equality by triple | `REL-EQUAL-001`, `REL-EDGE-001` | After adding `(A, t, B)`: adding `(B, t, A)` is an effective addition, because direction matters. Adding `(A, u, B)` with `u ≠ t` is an effective addition, because type matters. Re-adding `(A, t, B)` with different optional metadata is a no-op that neither replaces the existing metadata nor creates a second relationship. |
| 2 | One relationship per triple | `REL-MULT-001`, `REL-ORDER-001` | After any sequence of accepted adds and removes of `(A, t, B)`, the observable Canonical Relationships of A contain `(A, t, B)` zero or one times. Removing it once after any number of repeated adds leaves it absent. |
| 3 | Duplicate add as accepted no-op | `REL-DUP-001`, `REL-NOOP-001` | Adding a present relationship, authorized and not stale, produces an accepted outcome. Relationships, Object Version, and Canonical History are unchanged. |
| 4 | Absent-edge removal as accepted no-op | `REL-ABSENT-001`, `REL-NOOP-001` | Removing a relationship that was never added, or was already removed, produces an accepted outcome with relationships, Object Version, and Canonical History unchanged. No removal entry appears in history. |
| 5 | Authorization of otherwise no-op requests | `REL-AUTH-001`, `REL-PRECEDENCE-001`, `AUTH-BASE-001` | An actor lacking authority receives an authorization-denied outcome for a duplicate add and for an absent-edge remove. The outcome class is the same whether or not the relationship exists, and nothing canonical changes. |
| 6 | Stale expected-version conflict on otherwise no-op requests | `REL-PRECOND-001`, `REL-PRECEDENCE-001`, `CON-CONFLICT-001` | An authorized duplicate add or absent-edge remove that supplies a stale expected Object Version produces conflict with nothing canonical changed. The same request with the current version, or with none where permitted, is an accepted no-op. |
| 7 | No Canonical History or Object Version advance for no-ops | `REL-NOOP-001`, `REL-REPLAY-001`, `CON-ADVANCE-001`, `OP-HIST-001` | After `add, add, remove, remove` of one triple, Canonical History records exactly one accepted addition and one accepted removal, and the Object Version changed exactly twice, as observed by equality comparison of successive versions. The target object's Object Version and Canonical History are unchanged throughout (`REL-OWNER-001`). |
| 8 | Replay preserving the unordered relationship set | `REL-REPLAY-001`, `REL-ORDER-001`, `EVT-REPLAY-001` | Where replay is claimed, replaying an object with several relationships, including no-op attempts and relationships added in different orders, reconstructs a collection equal as a set to the accepted collection. Order is not compared. |
| 9 | Portable representation preserving relationship-collection meaning | `REL-PORT-002`, `REL-COMPAT-001`, `PORT-SEM-001` | Where export is claimed, exported content represents each object's relationships as a set of distinct triples, keeps relationships to targets outside the export, and preserves accepted relationship history order. Where multiset-origin input is accepted for import, it is refused or explicitly converted with a record of each change, never silently deduplicated. No encoding is implied. |

Candidates 8 and 9 are conditional on replay and export claims respectively. Candidate 9 does not establish interchange interoperability. Draft2.2 constrains Object Version values across restoration (`CON-UNIQUE-001`, `PORT-RESTORE-001`) but defines no encoding, and token remapping remains deferred.

### Draft2.2 Candidates

Draft2.2 candidates for the new and changed Core requirements and for the [Portable Restoration Continuity Profile](profiles/portable-restoration-continuity.md) are listed in the Conformance Plan (not published). They are planning material, not executable scenarios. Draft2.2 changes no draft2.1 candidate above. Candidate 5's outcome invariance is now also required by `AUTH-DISCLOSE-001`.

### Draft2, Draft2.1, and Draft2.2 Results

The frozen `v0alpha1 draft2` conformance harness and its results remain a `draft2` target. The experimental `v0alpha1 draft2.1` harness and its results remain a `draft2.1` target. Neither is relabeled as a draft2.2 result. Draft2.2 behavior should be evaluated through a separate target.

Draft2.1 and draft2.2 candidates are not added to an earlier target in any way that changes its reproducible results. A `draft2` harness that compares relationship collections as sorted lists is duplicate-sensitive and is not a draft2.1 set comparison.

Named Python test functions cited in the remaining tables below (Authorization and Traversal, Delegated Authority, Agent Operation, External Observation, Provider Binding and Canonical Authority) are reference evidence from an earlier, unpublished version of the Python runtime. They are not names in the published Python runtime.

### Authorization and Traversal

| Reference evidence | Requirements | Candidate black-box behavior |
| --- | --- | --- |
| Python runtime authorization test | `AUTH-BASE-001` | Missing authority prevents mutation |
| Python runtime test `test_actor_allows_app_denies_cancel` | `AUTH-NARROW-001`, `AUTH-APP-001` | Application context narrows actor authority |
| Python runtime test `test_actor_denies_app_cannot_grant` | `AUTH-APP-001` | Application context cannot grant authority absent upstream |
| Python runtime test `test_unknown_application_denied` | `OP-CTX-001` | Unknown authority-bearing context is not trusted |
| Python runtime traversal tests | `AUTH-QUERY-001` | Unauthorized start object is not disclosed |
| Python runtime traversal tests | `AUTH-QUERY-001` | Hidden object identifiers do not appear in traversal output |
| Python runtime traversal tests | `AUTH-QUERY-001` | Protected fields are omitted |
| Python runtime traversal tests | `AUTH-GRAPH-001`, `REL-TRAVERSE-001` | An inaccessible node is not a traversal bridge |

### Delegated Authority

| Reference evidence | Requirements | Candidate black-box behavior |
| --- | --- | --- |
| Python runtime test `test_valid_delegation` | `DEL-MODEL-001`, `DEL-NARROW-001` | Valid narrowed authority can become effective |
| Python runtime test `test_principal_cannot_delegate_missing_capability` | `DEL-NARROW-001` | A principal cannot delegate a capability it lacks |
| Python runtime capability/scope/time expansion tests | `DEL-NARROW-001`, `DEL-CHAIN-001` | A child cannot expand any supported authority dimension |
| Python runtime test `test_self_parent_rejected` | `DEL-CHAIN-001` | A delegation cycle is rejected or ineffective |
| Python runtime test `test_parent_revocation_disables_child` | `DEL-EFFECT-001` | Upstream revocation disables descendant authority |
| Python runtime test `test_expired_mandate_denies_mutation` | `DEL-EFFECT-001` | Expired authority cannot mutate |
| Python runtime test `test_mandate_replay` | `DEL-MODEL-001`, `EVT-REPLAY-001` | Governed mandate representation can be reconstructed |

### Agent Operation

| Reference evidence | Requirements | Candidate black-box behavior |
| --- | --- | --- |
| Python runtime test `test_agent_is_actor_type` | `AGENT-ACTOR-001` | An agent is evaluated as an ordinary actor |
| Python runtime test `test_no_separate_agent_policy_engine` | `AGENT-ACTOR-001`, `AUTH-BASE-001` | Agent operation uses ordinary authorization semantics |
| Python runtime test `test_bound_context_unchanged_after_tamper_attempt` | `AGENT-BIND-001` | Model arguments cannot replace bound authority identity |
| Python runtime test `test_injection_attempts_denied` | `AGENT-AUTH-001`, `AGENT-BIND-001` | A model proposal does not bypass governance |
| Python runtime test `test_revoked_mandate_denies_execute` | `AGENT-AUTH-001`, `DEL-EFFECT-001` | A model-backed agent loses authority when its mandate is revoked |
| Python runtime test `test_replay_ignores_model_trace` | `AGENT-TRACE-001`, `EVT-REPLAY-001` | Model trace is unnecessary for canonical replay |

### External Observation

| Reference evidence | Requirements | Candidate black-box behavior |
| --- | --- | --- |
| Python runtime test `test_hydration_does_not_mutate_canonical_state` | `CORE-STATE-002`, `OBS-SEPARATE-001` | Hydration records observation without silent canonical mutation |
| Python runtime test `test_provider_auto_accept_party_size` | `OBS-AUTH-001`, `OBS-EFFECT-001` | A valid auto-admitted change enters Accepted Canonical State |
| Python runtime test `test_duplicate_observation_ignored` | `OBS-DEDUPE-001` | A duplicate observation does not duplicate accepted change |
| Python runtime test `test_unknown_external_actor` | `OBS-PROV-001` | An unknown external actor remains unknown |
| Python runtime test `test_stale_provider_version` | `OBS-ORDER-001` | A stale ordered observation does not regress state |
| Python runtime canonical-authoritative/review tests | `OBS-AUTH-001`, `OBS-REVIEW-001`, `OBS-EFFECT-001` | Rejected and unresolved review outcomes remain unaccepted; separate governed resolution may accept |
| Python runtime test `test_replay_includes_external_changes` | `EVT-OBS-001`, `EVT-REPLAY-001` | An accepted observation survives canonical replay |
| Python runtime test `test_one_observation_three_outcomes` | `OBS-AUTH-001` | One observation can produce independent per-dimension decisions |
| Draft2 provider-edge candidate | `OBS-REL-001`, `REL-ACCEPT-001`, `REL-REMOVE-001` | A provider edge remains observation/index data until governed admission; disappearance does not silently remove a Canonical Relationship |

### Provider Binding and Canonical Authority

| Reference evidence | Requirements | Candidate black-box behavior |
| --- | --- | --- |
| Python runtime provider tests | `PB-TRANSLATE-001`, `EVT-CONTENT-001` | Provider-native vocabulary does not become canonical history vocabulary |
| Python runtime provider tests | `PB-READ-001` | Provider read failure does not become a canonical change |
| Python runtime test `test_detach_preserves_identity_and_history` | `PB-DETACH-001` | Detachment preserves canonical identity and history |
| Python runtime repair-planning tests | `CA-DRIFT-001`, `CA-SCOPE-001` | Only eligible canonical-authoritative drift creates repair |
| Python runtime test `test_tampered_repair_value_rejected` | `CA-DERIVE-001` | A caller cannot turn repair into arbitrary mutation |
| Python runtime canonical/provider stale tests | `CA-CANONICAL-PRE-001`, `CA-PROVIDER-PRE-001`, `CA-CONFLICT-001` | Canonical and provider precondition failures are distinct and prevent stale writes |
| Python runtime test `test_replay_unaffected_by_sync_history` | `CA-EFFECT-001`, `EVT-REPLAY-001` | Synchronization history is not replay input |
| Python runtime test `test_outbound_repair_webhook_is_echo` | `CA-ECHO-001`, `EVT-REPAIR-001` | Repair echo creates no duplicate canonical event |
| Python runtime provider replacement/gap tests | `PB-GAP-001`, `CA-REPLACE-001`, `PORT-PROVIDER-001` | Replacement preserves canonical meaning and unsupported dimensions remain Projection Gaps |

## Evidence Boundaries

The Python and Go runtimes are useful evidence with known gaps. An open Core conformance suite exists, and both runtimes have been run against it; the suite results, which are self-attested, record which scenarios pass, are not applicable, or are not executed. Neither runtime supplies a normative interchange encoding, and the suite has no Portable Restoration Continuity target. Earlier harness results are `draft2` or `draft2.1` results and are not relabeled as draft2.2 results. Experimental cross-runtime interchange evidence gathered against draft2.1 informed this revision. It is not conformance evidence for either runtime and does not establish interoperability. Candidate implementation mechanisms include adapters, fixture-driven processes, HTTP surfaces, and import/export tools; this draft chooses none.
