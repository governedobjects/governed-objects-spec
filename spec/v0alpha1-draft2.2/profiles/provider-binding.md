# Provider Binding Profile

**Version:** v0alpha1  
**Revision:** draft2.2  
**Status:** Optional Profile  
**Depends on:** Core  
**Evidence confidence:** MODERATE

## Purpose

This profile associates a canonical object with an external operational implementation without making the provider's identity or representation canonical.

## Normative Requirements

**PB-MODEL-001 — Binding semantics**  
A Provider Binding MUST identify the canonical object, provider identity, and provider-native object identity needed to address the Provider Projection. It MAY include provider-specific synchronization metadata.

**PB-ID-001 — Canonical identity remains independent**  
A provider-native identifier MUST NOT replace or redefine Canonical Identity. Changing a binding or external identifier MUST NOT, by itself, create a new canonical object.

**PB-DETACH-001 — Detachment preservation**  
Detaching a provider MUST preserve Canonical Identity, Accepted Canonical State, Canonical History, canonical Relationships, and governance semantics already accepted by the organization. Provider freshness and provider-only operational behavior MAY become unavailable. A detachment that changes only the Provider Binding or other Operational Metadata MUST NOT produce a new Object Version or a Canonical History entry (`CORE-LINEAGE-001`). If an accepted canonical change accompanies a detachment, that change follows the ordinary version and history requirements.

**PB-GAP-001 — Projection gaps preserve meaning**  
If a provider cannot represent, observe, or operate a canonical field or semantic dimension, the implementation MUST retain the canonical meaning and MUST NOT silently remove or redefine it to fit the provider. The gap MUST be identifiable as a provider capability limitation.

**PB-TRANSLATE-001 — Provider vocabulary isolation**  
Provider-native field names, state codes, and identifiers MUST be translated at the binding/projection boundary and MUST NOT be required to interpret Canonical History or the portable type semantics.

**PB-READ-001 — Operational read failure**  
A provider read or availability failure MUST NOT alter Accepted Canonical State or create a Canonical Event. The implementation MAY return a distinct operational failure and MAY retain non-canonical operational audit evidence.

## Capability Description

**PB-CAP-001 — Provider capability disclosure**  
An implementation claiming this profile SHOULD be able to describe relevant provider capabilities, including:

- representable canonical dimensions;
- observable dimensions;
- writable dimensions;
- Provider Version or conditional-write support;
- bootstrap support; and
- external provenance availability.

`v0alpha1` does not define a capability-discovery wire format or provider certification process.

## Provider Projection

A Provider Projection may be partial. Provider state may be operationally fresher than Accepted Canonical State until observed and admitted. Conversely, canonical meaning may exceed what the provider can represent.

Binding, rebinding, and detachment are operational changes. Evidence of them may be retained outside Canonical History (`EVT-SEPARATE-001`). Who may detach a provider, and how that authority is governed, is not defined in draft2.2.

Provider Binding does not by itself grant Canonical Authority. Outbound repair and replacement bootstrap are specified by the [Canonical Authority Profile](canonical-authority.md).

## Non-Normative Example

```text
Canonical Identity: reservation-701

Provider Binding:
  provider identity: Provider A
  external identity: A-991

The string forms and field labels are illustrative only.
```
