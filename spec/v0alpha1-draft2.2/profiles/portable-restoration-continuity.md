# Portable Restoration Continuity Profile

**Version:** v0alpha1  
**Revision:** draft2.2  
**Status:** Optional Profile — new in draft2.2; experimental  
**Depends on:** Core  
**Roles:** Exporter, Restorer  
**Evidence confidence:** EXPERIMENTAL

## Purpose

This profile defines what it means for another implementation to restore a Governed Object's complete accepted history and exact Object Version values, and then keep governing the object. Concurrency, authorization, relationship, and history semantics continue as if the object had always been native to the restoring implementation.

It is a semantic profile. It is **not a wire protocol**. It defines no encoding, field names, document shape, API, transport, or capability-declaration format. Two implementations that each satisfy one role are not thereby interoperable. Interoperability also requires a shared encoding, which this revision does not define.

The profile is optional. Core requires no export, restoration, or runtime replacement. Core constrains their meaning where they occur (`PORT-SEM-001`, `PORT-RESTORE-001`, `PORT-PRESERVE-001`, `CON-UNIQUE-001`, `CORE-LINEAGE-001`, `AUTH-BASE-001`, `TYPE-BIND-001`). This profile adds the capability.

## Roles

| Role | Produces or performs | Main requirements |
| --- | --- | --- |
| **Exporter** | The portable representation of objects it holds, under a governed read | `PRC-EXPORT-001`, `PRC-EXPORT-002`, `PRC-HIST-001`, `PRC-ORDER-001`, `PRC-GOV-001`, `PRC-EVID-001`, `PRC-REFUSE-001` |
| **Restorer** | Restoration of those objects, followed by continued governed operation | Every requirement except `PRC-EXPORT-001` and `PRC-EXPORT-002` |

This table summarizes the applicability rule in `PRC-CLAIM-001`. Where they differ, the rule governs. A Restorer applies the requirements on portable representations by refusing representations that do not meet them, whichever implementation produced them.

Continuity is intrinsic to the Restorer role. This revision defines no restoration claim without exact token preservation, behavioral continuity, and a declared handoff. Restoration under remapped tokens, and restoration from a snapshot without history, are deferred. If they are ever defined, they will be separately named, lesser claims that do not use the word "continuity".

## Scope

This profile covers moving the portable governed semantics of individual Governed Objects from one implementation to another that does not yet hold those identities.

It does not cover:

- synchronization, replication, multi-master operation, or history merge;
- partial, truncated, or compacted history;
- Object Version remapping;
- type migration or conversion during restoration;
- optional-profile data, such as provider bindings, external observations, mandates, or application context;
- policy or type packages;
- authenticity, signatures, issuers, or trust roots; or
- encodings or transfer protocols.

## Normative Requirements

### Claims

**PRC-CLAIM-001 — Role-scoped claims**  
An implementation claiming this profile MUST identify which roles it claims, Exporter, Restorer, or both, and MUST satisfy every requirement of this profile applicable to each claimed role. A requirement, or part of a requirement, whose subject is an Exporter or a portable representation an Exporter produces applies to the Exporter role. One whose subject is a Restorer applies to the Restorer role. A Restorer MUST refuse a portable representation, from any source, that does not satisfy what this profile requires of portable representations. A claim of one role MUST NOT be taken to imply the other. A Restorer claim MUST include `PRC-TOKEN-001`, `PRC-TOKEN-002`, `PRC-CONT-001`, and `PRC-HANDOFF-001`. Evidence that a named pair of implementations exported and restored objects between themselves is evidence about that pair, and MUST NOT be reported as profile conformance of either implementation.

### Export

**PRC-EXPORT-001 — Portable representation content**  
For each exported object, an Exporter MUST produce a portable representation that carries, as semantic content: Canonical Identity; Type Identity and bound Type Version; Accepted Canonical State; Canonical Relationships as a set of distinct relationships, including relationships whose targets are outside the export; the governance reference required by `PRC-GOV-001`; the current Object Version, exactly as the Exporter exposes it; the complete Canonical History required by `PRC-HIST-001`, including any Canonical Relationships present in the creation or Replay Baseline revision, in the order required by `PRC-ORDER-001`; and the specification revision under which the content is claimed. It MUST NOT present as canonical content any storage key, runtime record identifier presented as identity, rejection or audit record, credential, grant, trust configuration, policy content, or optional-profile operational data. A current snapshot MAY accompany the history.

**PRC-EXPORT-002 — Export is a complete governed read**  
An Exporter MUST perform export as a governed read of every included object under the requester's effective authority. If the requester is not authorized to read all content required by `PRC-EXPORT-001` for an object, or the Exporter cannot determine whether a record represents an accepted canonical change, the Exporter MUST refuse to export that object. It MUST NOT omit, redact, or guess at content and present the result as a portable representation under this profile. A denied export is subject to `AUTH-DISCLOSE-001`.

### History

**PRC-HIST-001 — Complete accepted history**  
A portable representation MUST carry the complete Canonical History of the object required by `CORE-HIST-001`, from its creation or Replay Baseline revision to its current revision, without gaps. It MUST identify the first revision it carries as the object's creation or Replay Baseline revision, and MUST carry that revision's initial accepted canonical representation, Type Identity and Type Version, and Object Version. Each later entry MUST carry its accepted semantic effect, applicable Type Identity and Type Version, and resulting Object Version, together with provenance as governed by `PRC-EVID-001`. The history MUST contain only accepted canonical changes. It MUST NOT include authorization denials, semantic invalidity, conflicts, reads, accepted no-ops, or records of export or restoration. The current Object Version MUST equal the resulting Object Version of the last history entry, or of the baseline revision if there is no later entry. A Restorer MUST refuse a representation that does not identify its first revision in this way, or whose history is otherwise detectably incomplete.

**PRC-ORDER-001 — Explicit history order**  
A portable representation MUST make the per-object order required by `EVT-ORDER-001` explicit and unambiguous as part of the representation. A Restorer MUST NOT infer that order from timestamps or Object Version values, or from element or storage order unless the representation defines that order as the history order. A Restorer MUST preserve the order exactly, and MUST refuse a representation whose order is missing or ambiguous.

### Object Version

**PRC-TOKEN-001 — Exact Object Version preservation**  
A Restorer MUST preserve the current Object Version and every resulting Object Version recorded in the restored Canonical History exactly, as opaque values compared only for equality. It MUST NOT parse, renumber, normalize, reissue, or remap them. A Restorer that cannot represent an Object Version value exactly MUST refuse restoration of that object. This requirement does not require any particular Object Version syntax, value domain, or ordering.

**PRC-TOKEN-002 — Object Version continuity**  
After restoration, a Restorer MUST treat the restored current Object Version as the current Object Version for `CON-CONFLICT-001`. It MUST treat every other restored Object Version, and every value never issued for the object, as stale. Every Object Version it subsequently issues for the object MUST differ from every restored Object Version (`CON-UNIQUE-001`).

### Governance

**PRC-GOV-001 — Local governance resolution**  
A portable representation MUST carry a governance reference for each object, preserved exactly. A Restorer MUST resolve that reference against its own governance configuration for the object's type binding, and MUST refuse restoration if the reference is missing or does not resolve. Resolution MUST NOT import grants, policy content, principals, credentials, or trust configuration. Neither the reference nor any restored content confers authority (`AUTH-BASE-001`). If a restored object's reference later ceases to resolve, an operation requiring authorization under it MUST NOT be accepted on the basis of that reference.

### Restorer Capability and Refusal

**PRC-CAP-001 — Restorer capability declaration**  
A Restorer MUST declare, semantically:

- the specification revisions it restores;
- the Type Identity and Type Version bindings it supports;
- the Object Version value forms it can hold exactly;
- whether it supports Canonical Relationships in a baseline revision;
- the precision of time evidence it retains;
- any provenance it requires;
- the evidence level it achieves under `PRC-EVID-001`; and
- whether a multi-object restoration is all-or-nothing.

It MUST refuse content outside its declared capabilities and MUST NOT adapt that content to fit them (`PORT-PRESERVE-001`). Provenance that it declares it does not retain, under `PRC-EVID-001`, is not outside its declared capabilities. This requirement does not define a declaration encoding or discovery mechanism.

**PRC-REFUSE-001 — Explicit refusal**  
An Exporter or Restorer that refuses to export or restore an object MUST identify the object and the class of reason. Examples include an unsupported specification revision or type binding, an unsupported Object Version form or other unsupported feature, incomplete or inconsistent history, a repeated Object Version, a snapshot that does not match the history, an identity collision, an unresolved governance reference, and content outside declared capabilities. Reason labels are not standardized. A refusal MUST NOT be replaced by silent degradation (`PORT-PRESERVE-001`). Refusal reasons MUST be returned only to a requester authorized to perform the export or restoration. A refusal is not an accepted canonical change, and a refused restoration MUST leave the target implementation's canonical content unchanged.

### Restoration

**PRC-NEW-001 — New identities only**  
A Restorer MUST refuse to restore an object whose Canonical Identity already exists in the target implementation, and MUST NOT merge restored content into, overwrite, replace, or reconcile it with an existing object. This profile defines no other handling of an existing identity. A relationship whose target identity already exists in the target implementation is not a collision.

**PRC-VALID-001 — Validation before restoration**  
Before a restored object becomes observable, a Restorer MUST verify at least that:

- the specification revision and type binding are supported (`TYPE-SUPPORT-001`);
- each history entry's recorded effect is well-formed for the declared Type Identity and Type Version;
- the history is complete and explicitly ordered (`PRC-HIST-001`, `PRC-ORDER-001`);
- no Object Version value is repeated within the object's history;
- the current Object Version equals the last resulting Object Version;
- any accompanying snapshot equals the result of applying the history from its baseline;
- the governance reference resolves (`PRC-GOV-001`); and
- the content is within its declared capabilities (`PRC-CAP-001`).

If any check fails, the Restorer MUST refuse restoration of the object. It MUST NOT repair, reorder, complete, or otherwise alter the content to make it pass.

**PRC-AUTH-001 — Restoration authority**  
A Restorer MUST require, for restoration, authority to restore, distinct from authority to create, read, or mutate objects. Authority to restore MUST NOT by itself confer authority to create, read, or mutate the restored object. Authority to create or mutate objects MUST NOT by itself confer authority to restore. An unauthorized restoration MUST be denied, with no restored object observable, and is subject to `AUTH-DISCLOSE-001`.

**PRC-NOEXEC-001 — History is not re-executed**  
A Restorer MUST NOT re-execute or re-authorize restored history entries against current governance. It MUST NOT invoke external side effects for them, or attribute them to the restoring actor. Actors recorded in restored history gain no authority from that record (`AUTH-BASE-001`). Restoration MUST NOT depend on whether those actors exist, or have any authority, in the target implementation.

**PRC-NOREV-001 — Restoration adds no revision**  
A Restorer MUST ensure that, after restoration, the object's Canonical History equals the restored history in number of entries, order, accepted effects, and resulting Object Versions, and that its current Object Version equals the representation's current Object Version. The Restorer MUST NOT append an entry recording the restoration (`PORT-RESTORE-001`). Restoration provenance MAY be retained outside Canonical History.

**PRC-ATOMIC-001 — Per-object atomicity and exact reporting**  
A Restorer MUST restore each object completely or not make it observable at all. Observable means available through any governed read, query, traversal, mutation, or export of the target implementation. This requirement does not prescribe locking, transactions, or any other mechanism. A Restorer handling several objects in one restoration request MUST report exactly which objects were restored and which were refused. It MUST NOT report the request as successful if any object was refused. Unless the Restorer declares all-or-nothing multi-object restoration under `PRC-CAP-001`, a request in which some objects are restored and others refused conforms if it is reported exactly. A Restorer that declares all-or-nothing restoration MUST NOT make any object of a request observable unless every object of that request is restored. This requirement defines no multi-object transaction semantics.

### Evidence

**PRC-EVID-001 — Exact effects and declared evidence fidelity**  
Exporters and Restorers MUST preserve accepted semantic effects, order, and resulting Object Versions exactly (`PRC-ORDER-001`, `PRC-TOKEN-001`). Each provenance item carried in a portable representation MUST be either preserved exactly or declared not retained (`PRC-CAP-001`). For this requirement, relationship metadata that does not participate in relationship equality under `REL-EQUAL-001` is provenance. It MUST NOT be altered, approximated, rounded, or invented. A Restorer that requires a provenance item that is absent MUST refuse restoration. A Restorer MUST declare which of two evidence levels it achieves:

- **effect-equivalent:** accepted effects, order, and Object Versions are exact, and provenance is retained as declared; or
- **evidence-preserving:** effect-equivalent, and every carried provenance item is retained exactly.

This requirement does not enumerate provenance kinds and does not require any particular provenance, such as actor identity, to be present.

### Handoff and Continuity

**PRC-HANDOFF-001 — Declared single-authority handoff**  
A Restorer claim MUST declare how a single accepting authority, meaning a single implementation that accepts canonical changes, is maintained for each restored identity across the handoff from the source. The source is the implementation whose Canonical History the portable representation carries. Examples include retiring the source, making the source read-only for the identity, or an operator procedure. The declared handoff MUST be one under which the source accepts no canonical change for the identity after the revision carried in the portable representation, so that the source's and the Restorer's accepted revisions form one lineage (`CORE-LINEAGE-001`). The declaration is an assumption of the claim. The Restorer is not required to enforce or verify it, and this profile defines no fencing, lease, consensus, or handoff protocol. It does not cover replication, multi-master operation, concurrent authority, or history merge, and a claim of this profile implies no safety for them.

**PRC-CONT-001 — Behavioral continuity**  
A Restorer MUST make each restored object subject to every Core requirement as an object of the Restorer whose Canonical History is the restored history, with no exemption for restored objects and no dependence on the source implementation. If the Restorer also claims the Exporter role, a later export of the object MUST carry the restored content together with every change accepted after restoration, and MUST satisfy `PRC-EXPORT-001`.

## Behavioral Continuity (Non-Normative)

`PRC-CONT-001` adds no behavior beyond Core. It removes any exemption for restored objects. The observable consequences are:

- **B1.** The restored current Object Version is accepted as an expected-version precondition.
- **B2.** Historical and never-issued Object Versions are stale and conflict. Revealing which of the two was supplied is not required and is not recommended.
- **B3.** Authorization uses only the Restorer's governance.
- **B4.** New accepted changes continue the order after the last restored entry.
- **B5.** Relationship-set and no-op semantics are unchanged.
- **B6.** Authorized readers see the restored history, and historical actors gain nothing.
- **B7.** Where the Restorer also exports, re-export reproduces the restored content plus later accepted changes.

## Prerequisites for a Pair of Implementations (Non-Normative)

An Exporter claim and a Restorer claim do not make two implementations able to exchange objects. For an object to move from one to the other, the pair must also agree, outside this profile, on:

- an encoding and a transfer mechanism for the portable representation;
- a specification revision that both support;
- the Type Identity and Type Version bindings involved, with compatible type semantics configured at the Restorer (`TYPE-SUPPORT-001`);
- how the Restorer resolves each governance reference in its own governance configuration (`PRC-GOV-001`);
- Object Version forms that the Restorer can hold exactly (`PRC-TOKEN-001`);
- any provenance the Restorer requires, and the evidence level expected (`PRC-EVID-001`);
- who holds restoration authority at the Restorer (`PRC-AUTH-001`); and
- the handoff procedure (`PRC-HANDOFF-001`).

The same applies to each further hop, for example A to B and then B to C. Evidence that a particular pair has done this is evidence about that pair (`PRC-CLAIM-001`).

## Relationships Across the Transfer Boundary (Non-Normative)

Transfers are open by default. Relationships whose targets are outside the export are preserved and are not validated against the target implementation (`REL-PORT-002`). A Restorer that enforces target existence refuses the object explicitly. It never restores the object without the relationship. A representation may declare that it is closed over its current relationships. How such a declaration is expressed and verified is not defined. A Restorer that verifies it refuses a false declaration.

## Evidence and Time (Non-Normative)

Time evidence is compared as instants. A Restorer that cannot hold a carried precision refuses the object, or declares that it does not retain time evidence. It never rounds. Provenance never affects order or replay (`EVT-CONTENT-001`, `EVT-ORDER-001`).

## Trust (Non-Normative)

Validation under `PRC-VALID-001` detects incoherent content. It does not detect false content that is internally coherent. A restoration is exactly as trustworthy as the authority of whoever performs it (`PRC-AUTH-001`). Signatures, issuers, and trust roots are deferred.

## Encoding Neutrality

Every requirement above is stated over abstract semantic content. A conforming representation may use any encoding that makes the required content and order explicit. The experimental interchange representation used in cross-runtime evidence, including its field names, its positive 64-bit integer Object Versions, and its contiguous history positions, is one non-normative example. It is not required.

## Deferred

These matters are recorded in [Open Questions](../open-questions.md#draft22-deferred-issues):

- Object Version remapping;
- snapshot-only restoration and history compaction;
- bundle atomicity;
- a normative encoding and capability-declaration format;
- the Object Version value domain;
- enumeration of provenance kinds;
- handoff fencing;
- governance-reference syntax; and
- trust.
