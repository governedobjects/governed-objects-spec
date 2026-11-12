# Type System

**Version:** v0alpha1  
**Revision:** draft2.2  
**Profile:** Core  
**Evidence confidence:** EXPERIMENTAL for explicit type-version binding, coexistence, and evolution

## Purpose

A Governed Object type supplies organization-controlled meaning without requiring Core to know every asset or business domain. Reservations, contracts, containers, and mandates are examples, not mandatory specification types.

## Normative Requirements

**TYPE-ID-001 — Type identity**  
Every Governed Object MUST identify the type whose semantics govern its Accepted Canonical State, Canonical Relationships, and operations. Type Identity syntax and namespace rules are unresolved in `v0alpha1 draft2.2`.

**TYPE-VERSION-001 — Distinct type revision**  
An implementation MUST distinguish Type Version from Object Version. Every portable Governed Object and every Canonical History record whose semantics depend on a type revision MUST identify the applicable Type Version unambiguously. Type Versions MAY be represented as opaque strings, semantic versions, integers, hashes, or other identifiers, provided their identity and equality semantics are preserved. No ordering scheme is required.

**TYPE-BIND-001 — Stable type-version binding**  
A Governed Object MUST remain interpreted under its bound Type Version until an explicit governed migration or conversion accepts a different binding. Deploying, registering, or activating a newer runtime or type definition MUST NOT silently reinterpret an existing object under different type semantics. Exporting, transferring, or restoring a Governed Object MUST NOT by itself change its Type Identity or bound Type Version. A different binding requires an explicit governed migration or conversion under `PORT-EVOL-001`.

**TYPE-SUPPORT-001 — Type-version coexistence and explicit support**  
Multiple Type Versions MAY coexist. An implementation claiming support for an object's bound Type Version MUST interpret the object and relevant Canonical History according to that version. An unsupported Type Version MUST produce an explicit unsupported outcome or refusal and MUST NOT be silently interpreted as another version.

**TYPE-SEM-001 — Discoverable semantics**  
A type definition MUST make the semantics needed to interpret conforming objects available independently of one application's private model. Those semantics MUST include the accepted state shape and MUST identify any declared actions, lifecycle rules, relationship meanings, validation rules, and profile-specific authority rules that affect portable behavior.

**TYPE-VALID-001 — Declared rules govern acceptance**  
When a type declares structural, lifecycle, or domain-validity constraints, a conforming runtime MUST apply the relevant constraints before accepting a change. External or delegated authority MUST NOT bypass declared lifecycle invariants.

**TYPE-EXT-001 — Independent business types**  
A third party MUST be able to define a new Governed Object type without changing the portable semantics of Core. An implementation MAY require registration, adapters, validators, or reducers, but those mechanisms MUST NOT make the original project's business types mandatory.

## Semantic Areas

A type may define:

- data or content shape;
- accepted lifecycle states;
- actions and valid transitions;
- authorization requirements or policy references;
- field-level visibility;
- canonicalization rules for application/provider vocabulary;
- relationship types;
- external-observation authority rules; and
- replay or conversion semantics.

Only semantics actually used by the type need be present.

## Validation Layers

The specification distinguishes:

1. **authorization** — may this operation be attempted;
2. **structural or contract validation** — is the proposed state/content well-formed;
3. **lifecycle validation** — is the transition allowed from current Accepted Canonical State; and
4. **domain/type validation** — is the operation semantically valid for the type.

An implementation may combine these internally, but it must preserve their required observable effects: failed validation does not become Accepted Canonical State.

## Inheritance and Canonicalization

Type inheritance, composition, aliases, and canonicalization registries are not required by `v0alpha1`. If an implementation exposes such mechanisms, the fully resolved observable type semantics—not the implementation's merge algorithm—determine conformance.

## Evolution

The binding and coexistence requirements above do not define a migration system. Full type conversion, schema migration, event upcasting, and version negotiation remain open questions. Any future mechanism must preserve identity, accepted meaning, and history through explicit governed conversion.
