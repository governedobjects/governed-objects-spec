# Canonical Authority Profile

**Version:** v0alpha1  
**Revision:** draft2.2  
**Status:** Optional Profile  
**Depends on:** Core, External Observation, Provider Binding  
**Evidence confidence:** MODERATE

## Purpose

Canonical Authority extends governed observation with safe outbound synchronization. For declared canonical-authoritative dimensions, Accepted Canonical State may drive a supported Provider Projection back into conformity.

Under `CONF-CLAIM-001`, the profile claim identifies the providers and semantic dimensions for which both trustworthy observation and conditional mutation are supported. Unsupported dimensions remain outside that claim and are reported as partial capability or Projection Gaps.

## Normative Requirements

**CA-DRIFT-001 — Drift detection**  
The implementation MUST compare Accepted Canonical State with a normalized Provider Projection without treating the provider view as automatically accepted. It MUST identify the semantic dimensions that differ.

**CA-SCOPE-001 — Authorized repair scope**  
Automatic outbound repair MUST be limited to dimensions declared canonical-authoritative. A review-required dimension MAY become repairable only after a governed decision explicitly keeps the canonical value.

**CA-AUTH-001 — Repair authority and provenance**  
Every repair attempt MUST execute under an identifiable initiating authority, such as an authorized actor or bounded trusted controller, and MUST retain that initiating context in synchronization history. Automatic controller authority MUST be limited to declared canonical-authoritative dimensions and MUST NOT bypass a required human/review decision.

**CA-DERIVE-001 — Canonical-derived values**  
Repair values MUST be derived from current Accepted Canonical State at application time. Arbitrary caller-supplied desired values MUST NOT use the repair path to create new canonical truth or bypass ordinary governed mutation.

**CA-CANONICAL-PRE-001 — Canonical precondition**  
Before applying a planned repair, the implementation MUST verify that the relevant Canonical Precondition still matches. If canonical state changed, the stale repair MUST NOT reach the provider.

**CA-PROVIDER-PRE-001 — Provider precondition**  
Before or atomically with provider mutation, the implementation MUST verify that the relevant Provider Precondition still matches. If provider state changed, the stale repair MUST NOT overwrite the newer Provider Projection.

**CA-CONFLICT-001 — Distinct conflict outcomes**  
Canonical-precondition and provider-precondition failures MUST be distinguishable operational outcomes. Neither outcome may change Accepted Canonical State or imply successful provider repair.

**CA-EFFECT-001 — Repair is synchronization only**  
Applying an outbound repair MUST NOT mutate Accepted Canonical State, advance Object Version, or create a Canonical Event when canonical state was already accepted. Repair requests, results, and failures MAY be retained as separate synchronization history.

**CA-ECHO-001 — Echo deduplication**  
A provider observation caused by a successful repair MUST NOT create a duplicate accepted semantic change or Canonical Event.

**CA-REPLACE-001 — Provider replacement preservation**  
Replacing a provider MUST preserve Canonical Identity, Accepted Canonical State, Canonical History, canonical Relationships, and governance semantics. The new Provider Projection MUST be derived from Accepted Canonical State for every supported projected dimension. Binding identity, external ID, provider-native representation, and synchronization metadata MAY change.

## Dual-Precondition Model

```text
Canonical Precondition
          AND
Provider Precondition
          ↓
conditional provider mutation
```

No integer version or HTTP ETag is required. A provider may use any trustworthy token or conditional operation that detects intervening change.

If conditional mutation is unavailable, the implementation cannot claim Canonical Authority for that provider/dimension. It may still claim Provider Binding or External Observation.

## Non-Normative Examples

### Repair

```text
Accepted Canonical State: confirmed
Provider Projection:      cancelled

plan from canonical state
verify canonical precondition
verify provider precondition
conditionally set provider to confirmed

Accepted Canonical State and Canonical History remain unchanged
```

### Provider replacement

```text
Canonical Identity: reservation-701
Provider A: A-991
    → detach / replace projection
Provider B: B-774

Preserved:
  canonical identity
  accepted state
  canonical history
  relationships
  governance
```

## Failure and Unknown Outcomes

Provider unavailable, provider not found, unsupported field, rejected/tampered repair, conflict, and outcome-unknown conditions should be distinguishable where the provider can expose them. Cross-system atomicity and recovery from ambiguous outcomes remain unresolved.
