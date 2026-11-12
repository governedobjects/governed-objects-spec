# Portability

**Version:** v0alpha1  
**Revision:** draft2.2  
**Profile:** Core, with profile-specific preservation rules  
**Evidence confidence:** EXPERIMENTAL for cross-runtime semantic portability; UNRESOLVED for encoding and transfer

## Semantic Portability

Data export moves bytes. Governed Object portability preserves enough organization-owned meaning to continue interpreting and operating an object through a different implementation. In `v0alpha1 draft2.2`, this is an abstract semantic-content contract, not a required encoding or tested interchange compatibility.

## Normative Requirements

**PORT-SEM-001 — Portable semantic content**  
Any Governed Object export or transfer claimed to preserve `v0alpha1 draft2.2` semantics MUST preserve, as semantic content: Canonical Identity; Type Metadata including Type Identity and the bound Type Version; Accepted Canonical State; Canonical Relationships; the Object Version semantics protecting that whole canonical representation; governance semantics or portable governance references; and Canonical History required by `CORE-HIST-001`. If replay portability is claimed, it MUST also preserve the Replay Baseline and subsequent history needed by `EVT-BASELINE-001` and `EVT-REPLAY-001`. These are content requirements, not field-name, document-shape, or encoding requirements. Preserving Canonical History means preserving the complete Canonical History required by `CORE-HIST-001`: every accepted canonical change, with its accepted effect and resulting Object Version, in the order required by `EVT-ORDER-001`. Equality of Accepted Canonical State or Canonical Relationships does not establish preservation of Canonical History, and an export that carries only a current snapshot does not satisfy this requirement. This requirement does not by itself require a recipient to retain the exporter's Object Version values. An optional profile may require exact token preservation.

**PORT-APP-001 — Application replacement**  
If an implementation exposes Application Context or claims application portability, replacing an application MUST NOT, by itself, require replacement of Canonical Identity, Accepted Canonical State, canonical Relationships, governance, or Canonical History. Application-specific views and workflow code MAY require reimplementation.

**PORT-RUNTIME-001 — Runtime independence**  
Interpreting portable Governed Object semantics and evaluating conformance MUST NOT require permission from, dependence on, or access to a particular runtime vendor or commercial service.

**PORT-EVOL-001 — Explicit portable evolution**  
Specification, type, and schema conversions used to move portable objects between supported revisions MUST be explicit and inspectable. They MUST NOT silently discard Canonical Identity, accepted semantic meaning, or Canonical History.

**PORT-PROVIDER-001 — Profile preservation**  
An implementation claiming provider replacement under the Canonical Authority Profile MUST preserve the semantics required by `PB-DETACH-001` and `CA-REPLACE-001`. Provider Binding alone defines detachment, not replacement bootstrap.

**PORT-RESTORE-001 — Restoration is not a canonical change**  
If an implementation restores previously accepted Canonical History for a Governed Object, whether from an export, a transfer, or another implementation, the act of restoration MUST NOT itself constitute an accepted canonical change. It MUST NOT append an entry to the restored object's Canonical History, MUST NOT by itself introduce a new accepted canonical revision or advance the current revision, and MUST NOT attribute the restoration, or authority for it, to actors recorded in the restored history. Evidence of the restoration MAY be retained outside Canonical History. This requirement applies only where restoration is performed. It does not require an implementation to support restoration.

**PORT-PRESERVE-001 — Faithful preservation or explicit refusal**  
An implementation that claims to preserve a Governed Object's portable semantic content across export, transfer, or restoration, whether under `PORT-SEM-001`, under a claimed profile, or as a narrower declared claim, MUST, for the content within that claim, either preserve that content faithfully or refuse the operation explicitly, identifying the reason. Faithful preservation means that the semantic content, and its order where order is significant, is unchanged. A change of encoding or representation that leaves the semantic content unchanged is not an alteration. It MUST NOT silently omit, truncate, normalize, coerce, reorder where order is significant, reinterpret, or replace content within the claim, including content required by `PORT-SEM-001` or by a claimed profile. A narrower claim, such as a claim to preserve Accepted Canonical State only, constrains only the content it names and MUST NOT be presented as satisfying `PORT-SEM-001` or a profile. A limitation that the implementation declares before the operation and identifies when it applies is not silent. This requirement applies only where such a preservation claim is made.

## Relationship-Set Portability

`REL-PORT-002` makes the relationship content portable. An export under this revision carries each object's Canonical Relationships as a set of distinct relationships. It keeps relationships whose targets are absent from the export or unknown to the recipient. It preserves accepted relationship history in deterministic per-object order. The recipient must not silently merge, deduplicate, discard, or reorder that content.

Relationship collections or history produced under multiset rules, such as data from a draft2 runtime that accepted duplicate identical additions, are governed by `REL-COMPAT-001`. They must be refused or explicitly converted under `PORT-EVOL-001`, never silently deduplicated. This revision defines no conversion procedure.

Preserving set content and history does not establish interchange interoperability.

## Object Version Across Export and Restoration

Draft2.1 left open how Object Version values relate across an export and import. Draft2.2 resolves part of that question:

- Core requires preserving the Object Version *semantics* (`PORT-SEM-001`). It does not require a recipient to keep the exporter's values.
- If a recipient restores history rather than recreating the object, restoration is not a new revision (`PORT-RESTORE-001`). Any values the recipient later issues must not equal any value recorded in the restored history (`CON-UNIQUE-001`).
- The optional [Portable Restoration Continuity Profile](profiles/portable-restoration-continuity.md) requires exact preservation of current and historical Object Version values, and refusal of any value the restorer cannot hold exactly.

Token remapping, meaning restoration under new values with a declared correspondence, is not defined and is deferred. So are restoration from a snapshot without history and history compaction. See [Open Questions](open-questions.md#draft22-deferred-issues).

## Restoration and Preservation

Core does not require any implementation to export, restore, or replace a runtime. It constrains only what such operations mean when they occur or are claimed:

- **Restoration is not a mutation.** The restored object's current revision is the exporter's current revision. No `imported` history entry is appended (`PORT-RESTORE-001`). Provenance of the restoration, such as who restored it, when, and from where, may be recorded as non-canonical evidence (`EVT-SEPARATE-001`).
- **Historical actors do not authorize.** Restored history is not re-authorized, and its actors gain no local authority (`AUTH-BASE-001`).
- **No silent degradation.** An implementation that claims preservation either preserves the content or refuses with a reason (`PORT-PRESERVE-001`). A declared, identified limitation is not silent.
- **Type binding is unchanged.** Restoration does not upgrade the object's type (`TYPE-BIND-001`).

## Encoding Status

`v0alpha1 draft2.2` specifies semantic content, not a stable wire encoding. Draft 2.2 does not establish a normative wire format. It does not define:

- a canonical JSON or YAML document;
- event serialization;
- relationship serialization;
- policy packaging;
- a type package format;
- cross-runtime transfer protocol; or
- signature and cryptographic binding rules.

The Python runtime's serialization names, the clean-room Go experiment's JSON names, and the experimental interchange representation used for cross-runtime evidence are non-normative examples. A conforming representation MUST preserve the required semantics but need not adopt either implementation's names or shapes.

As a result, two implementations can implement the same semantic requirements but cannot yet claim tested interchange compatibility solely from this draft.

## Non-Normative Examples

### Application replacement

```text
Application A view: Booking
Application B view: Reservation

Both operate:
  Canonical Identity reservation-701
  Accepted Canonical State confirmed
  one Canonical History
```

### Byte export is insufficient

```text
CSV rows without type semantics, identity continuity,
relationships, governance, or accepted history
≠
portable Governed Object representation
```

## What Portability Does Not Promise

Semantic portability does not imply zero switching cost, identical user interfaces, preservation of proprietary workflow conveniences, zero-downtime migration, or that every external provider can represent every canonical field.
