# Open Questions and Architectural Tensions

**Version:** v0alpha1  
**Revision:** draft2.2  
**Status:** Experimental Draft

These questions are intentionally unresolved.

- Draft2 closed the relationship add/remove effect, Replay Baseline, Type Version binding/coexistence, and deterministic per-object history-order questions exposed by the two-implementation comparison.
- Draft2.1 closes relationship equality, multiplicity, duplicate-add, absent-edge-removal, and relationship no-op questions ([Relationships](relationships.md)).
- Draft2.2 closes part of the Object Version question across export and restoration. It covers revision uniqueness, restoration as a non-change, and exact token preservation in an optional profile. It also closes Canonical History completeness, lineage, and order, and disclosure-safe rejection ([Change Log](change-log.md)).

The questions below remain open.

## Draft2.2 Deferred Issues

These matters were identified by the draft2.2 interchange synthesis and are deliberately not resolved in draft2.2. Each is a candidate for later work. None is Draft3 content unless stated.

| Deferred issue | Status in draft2.2 | Useful next experiment |
| --- | --- | --- |
| Object Version remapping | Not defined. Restoration under new token values with a declared correspondence is neither permitted by the Portable Restoration Continuity Profile nor defined by Core. How a precondition taken against an exporter's token is treated after remapping is undefined | Define a remapping declaration and test stale-precondition behavior across it |
| Snapshot-only restoration | Not defined. A current-snapshot export does not satisfy `PORT-SEM-001` history preservation. Recreating an object from a snapshot without history is not restoration | Specify a declared "history-truncated" representation and its continuity limits |
| History compaction, retention, and truncation | Not defined. `CORE-HIST-001` requires complete history | Compare compaction schemes that retain replay equivalence and revision uniqueness |
| Bundle and multi-object restoration atomicity | Per-object atomicity is required by the profile. Whether a multi-object bundle is all-or-nothing is unresolved, and implementations must report per-object results exactly | Restore a bundle with one invalid member and compare atomic and per-object policies |
| Normative encoding for portable representations | Not defined. The experimental interchange JSON is evidence only | Independent encoders and decoders round-tripping one semantic fixture set |
| Object Version value domain | Not constrained beyond equality and `CON-UNIQUE-001`. Positive-integer or 64-bit tokens are not required. Recipients refuse tokens they cannot hold exactly | Exchange string, large-integer, and structured tokens across runtimes |
| Allocation exhaustion outcome | Not standardized. `OP-OUTCOME-001` permits an additional non-accepted outcome, and `OP-NOCHANGE-001` constrains its effects. No label, for example `OBJECT_VERSION_SPACE_EXHAUSTED`, is standardized | Compare exhaustion handling at the maximum representable token |
| Evidence kinds and actor evidence | Provenance is evidence (`EVT-CONTENT-001`). Which evidence kinds exist, and whether actor identity must be recorded or preserved, is not enumerated. The profile distinguishes effect-equivalent and evidence-preserving claims without listing kinds | Define a minimal provenance vocabulary and test exact preservation |
| Generalized no-op semantics | Accepted no-ops remain defined only for relationship add and remove (`REL-NOOP-001`). Equal-value updates and other operations are not addressed | Compare equal-value update handling across runtimes |
| Existence concealment | Remains policy-conditional (`AUTH-QUERY-001`). `AUTH-DISCLOSE-001` protects object content and version, not existence | Evaluate an optional concealment profile |
| Writers without read authority | `AUTH-DISCLOSE-001` clause 2 does not apply to a requester authorized for the operation. Such a writer can distinguish stale from current expected versions, and can observe an accepted no-op's unchanged version | Evaluate whether a concealment profile should cover write-only authority |
| Replication, multi-master operation, and history merge | Excluded. The profile defines a declared single-authority handoff only. `CORE-LINEAGE-001` forbids multiple lineages | Separate distributed-authority design line |
| Handoff mechanics | The profile requires a declared handoff but defines no fencing, lease, or source-retirement protocol | Test handoff fencing across two runtimes |
| Provider detachment authority | Who may detach a provider is not defined | Specify detachment as a governed operation in the Provider Binding Profile |
| Type migration during restoration | Excluded. Restoration preserves type binding (`TYPE-BIND-001`) | Combine restoration with explicit conversion under `PORT-EVOL-001` |
| Governance-reference resolution | The profile requires local resolution or refusal but defines no reference syntax or package format | Resolve one governance reference in two runtimes |
| Graph-dependent governance, cross-object dependency, and multi-object transactions | Excluded; Draft3 design work | Separate Draft3 line; not part of draft2.2 |

## Draft2.1 Deferred Issues

These matters are outside draft2.1 normative scope. Draft2.1 does not resolve them and does not establish interchange interoperability.

| Deferred issue | Status in draft2.1 | Useful next experiment |
| --- | --- | --- |
| Object Version mapping across runtime interchange | **Partly resolved in draft2.2** by `CON-UNIQUE-001`, `PORT-RESTORE-001`, `PORT-SEM-001`, and the Portable Restoration Continuity Profile. Remapping remains deferred (above). Draft2.1 status: unresolved. `PORT-SEM-001` requires preserving Object Version *semantics*. Whether an importer keeps opaque exporter tokens, issues new ones, or relates the two, and how a stale precondition against the exporter is treated after import, is undefined | Constrained Core interchange experiment comparing token-preserving and token-reissuing import |
| Conversion of historical duplicate-edge data (for example, unchanged Go reference data with repeated identical additions) | `REL-COMPAT-001` requires refusal or an explicit, recorded conversion and forbids silent deduplication. No conversion procedure is defined | Define and test a recorded conversion over Go `add, add, remove` histories, including how the resulting set and history are represented |
| Wire or interchange encoding, import/export API | Undefined; see [Portability](portability.md#encoding-status) | Serialize representative Core objects and relationship sets and round-trip them between two runtimes |
| Type migration and event upcasting | Unchanged from draft2 | See "Full type/schema migration and conversion" below |
| Multi-object atomicity | Unchanged from draft2. Relationship mutations change only the source object | See "Cross-object transactions" below |
| Graph-dependent evaluation, meaning authorization, validation, or lifecycle that depends on relationships or related objects | Excluded; separate design work | Separate Draft3 line; not part of draft2.1 |
| Real provider integration | Unchanged from draft2; fake providers only | See "Providers and Operations" below |
| Production crash consistency | Undefined; no crash-injection evidence | Crash-injection tests around relationship mutation and history append |
| Relationship attributes, edge identity, and parallel instances | Not normative. Business meaning needing identity, lifecycle, or attributes is modeled explicitly ([Relationships](relationships.md#additional-business-meaning)) | Compare a reified-relationship object pattern against a future edge-attribute proposal |
| Relationship-type namespace and per-type cardinality | Not prescribed. Types MAY declare cardinality under `TYPE-VALID-001` | Two runtimes enforcing one declared cardinality constraint |
| Identity normalization for equality | Draft2.1 compares Canonical Identities and relationship types for equality. Case, Unicode, or alias normalization is not defined | Test equality across runtimes with differing identity syntax |
| Target existence at add time | Not required. Dangling targets are preserved in export (`REL-PORT-002`) | Compare strict-target and permissive-target validation policies |
| Existence inference by writers lacking read authority | Still open in draft2.2 (see "Writers without read authority" above). `REL-AUTH-001` conceals existence from unauthorized requesters. An authorized writer without read authority may still infer existence from an accepted no-op's unchanged Object Version | Evaluate whether a concealment profile is needed |

## Interchange and Versioning

| Question | Why unresolved / missing evidence | Useful next experiment |
| --- | --- | --- |
| Normative wire or interchange format | Current YAML, JSON, Python, and SQLite forms have not been exchanged between independent runtimes | Serialize representative Core objects/history and round-trip them between two minimal implementations |
| Canonical identity syntax and namespace | Tests prove stability, not global naming, delegation, or collision rules | Compare URI, opaque-token, and organization-scoped identity models |
| Type identity and package naming | Current YAML filenames and `kind` values are local conventions | Build the same third-party type in two runtimes without shared source code |
| Specification-version negotiation | Draft2 clarified draft1 semantics and draft2.1 adds relationship-set semantics, but no runtime negotiates specification revisions | Prototype readers supporting two incompatible draft revisions |
| Full type/schema migration and conversion | Neither implementation has a portable conversion contract across type revisions | Define and test explicit migration, history conversion/upcasting, failure handling, and rollback while preserving identity/history |
| Event schema evolution and upcasting | Reducers are local Python code and event envelopes are not versioned portably | Replay old fixtures through independently written converters |
| Portable Canonical History representation | Required semantics and per-object order are defined, but encoding, compaction, retention, and proof of equivalence are not | Exchange event-led and snapshot-plus-history exports and verify equivalent replay |
| Relationship serialization | Draft2.1 fixes relationship-set content (`REL-PORT-002`) but no encoding. Current triples are SQLite records or in-memory lists with local metadata | Exchange cyclic, typed, partially visible relationship sets between runtimes |
| Object creation, duplicate identity, deletion, and tombstones | Creation is tested, but duplicate-ID and portable deletion/retention semantics are not defined | Run shared lifecycle fixtures against two minimal runtimes |

## Governance and Identity

| Question | Why unresolved / missing evidence | Useful next experiment |
| --- | --- | --- |
| Policy portability versus policy-engine independence | Observable authorization is tested, but capability YAML does not represent a portable policy standard | Express one policy suite in two unrelated engines and compare decisions |
| Principal authentication and identity | Actors are local YAML records without cryptographic binding | Integrate one language-neutral identity mechanism while preserving actor semantics |
| Trust-root portability | The runtime uses trusted persistence and a `MandateResolver`; no external trust representation exists | Implement delegation resolution with a different trust root and compare outcomes |
| Cryptographic mandate representation | Mandates are canonical local objects without portable signatures | Sign and verify delegated authority across two independent runtimes |
| Governance-reference packaging | Core requires governance semantics but no portable reference/package format | Export one object and its governing policy semantics without private runtime state |

## Providers and Operations

| Question | Why unresolved / missing evidence | Useful next experiment |
| --- | --- | --- |
| Provider capability discovery | Capabilities are hard-coded for fake providers | Define a provider-neutral capability document and test honest partial support |
| Provider certification | No production providers or certification process exist | Defer until capability declaration and a provider certification process exist |
| Ambiguous provider outcomes | Fake timeout behavior demonstrates uncertainty, not a complete recovery protocol | Test reconcile-and-retry against a failure-injecting provider |
| Cross-system atomicity | Canonical persistence and provider APIs do not share a transaction | Evaluate outbox/saga protocols without making one technique normative |
| Continuous reconciliation | Current reconciliation is invoked explicitly | Test scheduling, backoff, and ordering while preserving authority semantics |
| Cross-object transactions | Object operations are independent | Model one business invariant spanning multiple objects and failure boundaries |
| Fine-grained concurrency | Whole-object optimistic concurrency is the only demonstrated model | Compare field-level preconditions or merge semantics against Core invariants |
| Provider writes that bypass governed admission | External systems may still establish operational facts before observation/admission | Integrate a provider where all authoritative writes pass through a governed boundary |

## Runtime and Conformance

| Question | Why unresolved / missing evidence | Useful next experiment |
| --- | --- | --- |
| Cross-runtime object transfer | Experimental interchange between the two runtimes informed draft2.2, but no normative encoding exists, and the Core conformance suite has no Portable Restoration Continuity target | Build a draft2.2 target that exercises the Portable Restoration Continuity Profile (Conformance Plan, not published) |
| Independent interoperability | Clean-room Go provides semantic evidence, but no wire exchange exists and the conformance suite has no cross-runtime exchange target | Run shared black-box scenarios and interchange fixtures across both runtimes |
| Executable conformance interface | The Core conformance suite defines a language-neutral adapter protocol (newline-delimited JSON over standard input and output), but this specification does not standardize an executable interface | Compare black-box HTTP, fixture-driven process, and adapter-based harness designs |
| Capability declaration encoding | Profiles can be named semantically, but no endpoint/document is defined | Test static conformance manifests before defining a network API |
| Distributed/HA semantics | Reference persistence is local SQLite | Operate two writers and define which guarantees are Core versus deployment-specific |
| Optional profile expansion | The clean-room comparison implemented Core, not the optional profiles | Compare independent implementations of each narrow profile before adding profile scope |

## Architectural Tensions

### Canonical authority versus operational freshness

Accepted Canonical State is organization-owned, while a provider may know newer operational facts that have not been observed or admitted. `v0alpha1` preserves the distinction rather than claiming canonical state is always externally freshest.

### Policy portability versus engine independence

Core must preserve authorization meaning without standardizing the current capability evaluator. A portable policy representation may improve interchange but could over-constrain independent engines.

### Review case representation

Review cases are structured records in the reference runtime, while mandates are Governed Objects. `v0alpha1` does not decide whether review cases must become Governed Objects.

### Provider authority versus lifecycle authority

External-authoritative fields may be auto-admitted, but declared lifecycle invariants still constrain state transitions. Conflict resolution across richer lifecycle evolution needs more evidence.

### Trusted reconciliation versus actor authorization

Automatic repair currently runs through trusted system context, while explicit human-triggered reconciliation is expected to require authority. The portable actor/audit model for system reconciliation is unresolved.

### Governed commands versus observation concurrency

Ordinary mutations can carry a canonical precondition, while accepted External Observations currently advance Object Version without checking a caller-observed canonical version. Arbitration between concurrent governed commands, review resolution, and observation admission is unresolved.

### Authorization versus version-check ordering

The reference runtime authorizes lifecycle transitions before checking Object Version but checks container-put versions before authorization. `v0alpha1` specifies required outcomes and disclosure constraints rather than standardizing either implementation order. Draft2.2 makes the disclosure constraint unconditional for protected information (`AUTH-DISCLOSE-001`) and recommends authorization first (`AUTH-ORDER-001`). It still does not mandate an order.

### Audit history versus Canonical History

The reference runtime stores allowed reads, denials, conflicts, and accepted mutations through one event model. Replay filters by decision and reducer behavior. The portable boundary must remain semantic rather than table-based.

## What Must Not Be Frozen Yet

- stable wire encoding;
- Object Version remapping, and value domains beyond equality;
- bundle restoration atomicity, history compaction, and snapshot-only restoration;
- handoff fencing and replication;
- conversion procedures for relationship data produced under multiset semantics;
- relationship edge identity and normative relationship attributes;
- full type migration/conversion and event-upcasting APIs;
- creation, duplicate-identity, deletion, and tombstone semantics;
- policy package format;
- identity URI scheme;
- conformance harness protocol;
- provider capability document format;
- distributed transaction semantics;
- trust-root and cryptographic profiles;
- expansion of optional profile scope;
- provider writes that bypass governed admission; and
- certification or governance mechanics.
