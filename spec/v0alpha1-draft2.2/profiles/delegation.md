# Delegated Authority Profile

**Version:** v0alpha1  
**Revision:** draft2.2  
**Status:** Optional Profile  
**Depends on:** Core  
**Evidence confidence:** MODERATE

## Purpose

This profile defines portable delegation semantics without requiring the reference runtime's `DelegatedMandate` class, storage projection, or delegation-depth limit.

An implementation may represent a mandate as a Governed Object, a signed authority artifact, or another durable representation, provided the observable semantics below hold.

## Normative Requirements

**DEL-MODEL-001 — Delegation dimensions**  
A delegated-authority artifact MUST identify a principal, delegate, delegated capabilities or actions, status, and any supported scope, purpose, and validity bounds. Omitted dimensions MUST have documented semantics and MUST NOT silently grant broader authority than the principal can delegate.

**DEL-NARROW-001 — Monotonic narrowing**  
Effective delegated authority MUST be no broader than the authority available from its principal and every applicable upstream delegation. This narrowing MUST apply independently to each supported authority dimension, including capabilities, scope, purpose, and validity.

**DEL-CHAIN-001 — Child delegation**  
If delegated delegation is supported, a child delegation MUST NOT expand any authority dimension beyond its parent chain. Cycles MUST be rejected or made ineffective. This specification does not prescribe a maximum depth.

**DEL-EFFECT-001 — Current effectiveness**  
Revoked, expired, not-yet-valid, or otherwise ineffective authority MUST NOT authorize an operation. If an upstream authority becomes ineffective, descendant authority derived from it MUST also become ineffective even if the descendant's stored status has not changed.

**DEL-TRUST-001 — Explicit trust root**  
An implementation MUST document the trust root used to authenticate principals, resolve authority, and evaluate delegated authority without infinite recursion. The trust root is not required to be a Governed Object, but it MUST NOT be exposed as a general application authorization bypass.

## Mandate Lifecycle

Draft, active, revoked, and derived-expired states are demonstrated by the reference runtime, but exact state names are not normative. Implementations must preserve the distinction between stored lifecycle status and time-derived effectiveness.

## Delegation Administration

Creating, activating, narrowing, or revoking delegated authority is itself authority-sensitive. If mandates are represented as Governed Objects, their administration is subject to ordinary Core operation, authorization, concurrency, and history semantics.

## Non-Normative Example

```text
principal authority:
  capabilities = [read, seat, cancel]
  scope        = restaurant-7
  purpose      = operations

delegated authority:
  capabilities = [read, seat]
  scope        = restaurant-7
  purpose      = operations
  valid_until  = 22:00

result: valid narrowing
```

## Explicitly Non-Normative

- a maximum delegation depth of two or four;
- intersecting only an immediate parent rather than the complete upstream chain;
- Python mandate classes;
- one SQLite projection/cache;
- exact relationship names such as `delegated_by`;
- exact status enum spellings; and
- a particular cryptographic representation.
