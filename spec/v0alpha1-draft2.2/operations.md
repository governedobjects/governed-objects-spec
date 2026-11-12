# Governed Operations

**Version:** v0alpha1  
**Revision:** draft2.2  
**Profile:** Core  
**Evidence confidence:** STRONG

## Abstract Operation Model

A Governed Operation is evaluated from semantic context rather than a prescribed command class. Depending on the operation, context may include:

- actor identity;
- action;
- target Canonical Identity or Type Identity;
- business parameters;
- application context;
- delegated-authority context;
- purpose;
- provenance and correlation information; and
- an expected Object Version.

Field names and transport encodings are implementation-defined.

## Normative Requirements

**OP-CTX-001 — Sufficient operation context**  
An operation MUST carry or resolve the actor, requested action, target, and business parameters needed for authorization and validation. Optional profile contexts MUST be bound in a way that prevents an untrusted requester from silently substituting authority-bearing identity.

**OP-OUTCOME-001 — Distinguishable outcomes**  
An implementation MUST distinguish the minimum semantic outcome classes **ACCEPTED**, **AUTHORIZATION_DENIED**, **SEMANTICALLY_INVALID**, and **CONFLICT** when those conditions apply. Exact labels are implementation-defined. An implementation MAY combine schema/type, lifecycle, and domain invalidity within **SEMANTICALLY_INVALID**, and MAY expose a richer taxonomy while preserving these distinctions. When the conditions of more than one of these classes apply to a request that is not accepted, the implementation MAY report any one of the applicable classes, subject to `AUTH-DISCLOSE-001` and to any requirement that constrains the choice, such as `REL-PRECEDENCE-001`. An implementation MAY report additional non-accepted outcomes for conditions none of these classes describes, such as an implementation or resource refusal. Such an outcome MUST NOT be reported as **ACCEPTED**, or as another minimum class whose condition does not apply, and is subject to `OP-NOCHANGE-001`.

**OP-ACCEPT-001 — Acceptance boundary**  
A requested mutation MUST become part of the durable canonical representation only after applicable authorization, structural, lifecycle, relationship, and domain/type validation succeeds.

**OP-NOCHANGE-001 — Non-accepted attempts**  
A mutation request that does not result in an accepted canonical mutation, including an authorization-denied, semantically-invalid, or conflicting request and a request refused for another reason such as an implementation or resource limit, MUST NOT alter Accepted Canonical State or Canonical Relationships, advance Object Version, or enter Canonical History. It MAY produce distinguishable non-canonical audit evidence.

**OP-HIST-001 — Accepted mutation history**  
Every accepted mutation MUST produce or update Canonical History so that its accepted semantic effect and resulting Object Version can be determined. An accepted no-op under `REL-NOOP-001` is not an accepted mutation for the purposes of this requirement.

## Processing Semantics

The observable processing model is:

```text
operation context
    → authority evaluation
    → applicable structural/contract validation
    → applicable lifecycle validation
    → applicable domain/type validation
    → accepted | authorization denied | semantically invalid | conflict
```

An implementation may reorder internal checks where doing so does not:

- permit unauthorized mutation;
- disclose protected information contrary to `AUTH-DISCLOSE-001`, or leak other information the implementation promises to conceal;
- invoke external side effects for a stale mutation; or
- change the specified outcome semantics.

Authorization-first evaluation is recommended (`AUTH-ORDER-001`). Another order conforms if `AUTH-DISCLOSE-001` holds.

## Other Non-Accepted Outcomes

The four minimum classes are unchanged in draft2.2. An implementation may also refuse a request for reasons none of them describes, for example exhausting its Object Version space or another resource limit. Draft2.2 does not standardize such an outcome, its label, or its retry semantics. It requires only that:

- the refusal is not reported as ACCEPTED, or as a minimum class whose condition does not apply (`OP-OUTCOME-001`); and
- nothing canonical changes (`OP-NOCHANGE-001`, `CON-NOADVANCE-001`).

Non-canonical audit records of the refusal remain permitted.

## Reads and Queries

Reads and queries are governed operations. An implementation may audit successful reads, but a read audit record is not a Canonical Event merely because the read was allowed.

## Idempotency and Correlation

Idempotency keys, command identifiers, correlation identifiers, and exactly-once protocols are not Core requirements in `v0alpha1`. Implementations may provide them. Portable idempotency claim semantics remain unresolved; the reference runtime demonstrates deduplication for a limited mutation path.

Draft2.1 relationship-set semantics (`REL-DUP-001`, `REL-ABSENT-001`) are not an idempotency mechanism. They make repeated relationship requests converge on the same canonical relationship collection, but each repeated request is still evaluated again for authorization, validity, and concurrency. A repeated conditional request conflicts once an earlier attempt has advanced the Object Version. Returning an earlier result for a repeated command remains an optional idempotency capability.

## Accepted No-Ops

A relationship add or remove request whose requested effect already holds is an **accepted no-op** (`REL-NOOP-001`). It is reported as **ACCEPTED**, but it is not an accepted mutation: `OP-ACCEPT-001`, `OP-HIST-001`, and `CON-ADVANCE-001` impose nothing further, and the canonical representation, Object Version, and Canonical History are unchanged. A rejection outcome takes precedence over an accepted no-op (`REL-PRECEDENCE-001`). Draft2.1 defined accepted no-ops only for relationship add and remove requests, and draft2.2 does not extend them. No-op semantics for other operation classes, such as an update whose requested value equals the current value, remain undefined and deferred.
