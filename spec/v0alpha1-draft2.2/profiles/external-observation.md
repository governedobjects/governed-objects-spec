# External Observation Profile

**Version:** v0alpha1  
**Revision:** draft2.2  
**Status:** Optional Profile  
**Depends on:** Core  
**Evidence confidence:** MODERATE overall; state lifecycle admission is PARTIAL and field validation is unresolved

## Definition

An External Observation is a normalized report about externally observed state associated with a canonical object. It is evidence presented for an authority decision; it is not Accepted Canonical State.

## Normative Requirements

**OBS-MODEL-001 — Observation identity and provenance**  
An External Observation MUST identify the target Canonical Identity, observation identity or deduplication identity, external source/provider identity, and observed semantic changes. It SHOULD carry observation time, provider event identity, Provider Version or sequence, external actor provenance, and source provenance when available.

**OBS-SEPARATE-001 — Observation is not acceptance**  
Receiving, hydrating, materializing, polling, or storing an External Observation MUST NOT by itself alter Accepted Canonical State or Object Version.

**OBS-AUTH-001 — Explicit per-dimension authority decision**  
Each observed semantic change MUST pass through an explicit authority/admission decision before acceptance. Authority MAY be field- or dimension-specific, and one observation MAY produce accepted, canonical-authoritative/rejected, and review-required outcomes for different changes.

**OBS-REVIEW-001 — Resolvable review outcome**  
A review-required decision MUST create or reference a durable, resolvable review artifact. Resolving it MUST be a separate governed operation that authorizes the resolver and revalidates the proposed effect against current canonical state and `OBS-VALID-001`. The artifact MAY be a Governed Object or an implementation-defined record; `v0alpha1` does not prescribe its representation.

**OBS-PROV-001 — Unknown actors are not fabricated**  
When an external actor is not known, the implementation MUST record unknown or unavailable provenance rather than invent an actor identity.

**OBS-DEDUPE-001 — Duplicate semantic effects are prevented**  
Repeated ingestion of the same externally identified observation MUST NOT produce duplicate accepted semantic changes or duplicate Canonical Events.

**OBS-ORDER-001 — Stale observation handling**  
Where trustworthy provider ordering or version metadata is available, a stale observation MUST NOT regress Accepted Canonical State. Where no trustworthy ordering exists, the implementation MUST document that limitation rather than imply stale detection.

**OBS-VALID-001 — Authority does not bypass validity**  
Acceptance of an observed change MUST satisfy applicable structural, lifecycle, and domain/type invariants. A provider-authoritative or auto-admit rule MUST NOT bypass a declared lifecycle invariant.

**OBS-EFFECT-001 — Accepted and non-accepted effects**  
An accepted observed change MUST update Accepted Canonical State, advance Object Version, and enter Canonical History with available provenance. A rejected, stale, duplicate, unchanged, or unresolved review outcome MUST NOT change Accepted Canonical State or Object Version.

**OBS-REL-001 — Provider relationship observations**  
A provider-reported edge MUST remain an External Observation or a clearly classified non-canonical derived index unless explicit governed admission accepts it as a Canonical Relationship. An admitted addition or removal MUST satisfy the Core relationship mutation, Object Version, Canonical History, claimed replay, and portability requirements. Hydration, synchronization, or provider-edge disappearance MUST NOT silently create or remove a Canonical Relationship.

## Authority Categories

`v0alpha1` defines semantic categories rather than requiring the reference enum spellings:

- **external-authoritative / auto-admit** — accept a valid observed change automatically;
- **canonical-authoritative** — retain canonical value and reject the contradictory observation as canonical input;
- **review-required** — defer acceptance pending a separate governed decision.

Behavior for an unconfigured authority rule is unresolved. The reference runtime defaults to review-required; `v0alpha1` does not require that exact default.

## Observation Fields

Possible semantic fields include:

- observation identity;
- Canonical Identity;
- Provider Binding identity;
- observed field/state changes;
- observation time;
- external actor identity, if known;
- provider event identity, if known;
- Provider Version or sequence, if known; and
- source/provenance category.

No transport or field spelling is prescribed.

## Non-Normative Example

```text
provider reports:
  party_size: 4 → 6
  state: confirmed → cancelled
  requested_seating: dining-room → patio

authority decisions:
  party_size       → accept
  state            → keep canonical / repair candidate
  requested_seating→ review
```

## Limits

Changes that a provider never exposes cannot be observed or reconstructed. The profile does not require webhooks; polling, reconciliation, and manual import may feed the same authority semantics.
