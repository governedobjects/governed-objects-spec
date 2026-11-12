# Authorization

**Version:** v0alpha1  
**Revision:** draft2.2  
**Profile:** Core, with conditional application/query capabilities  
**Evidence confidence:** STRONG for mutation authorization; MODERATE for graph/query behavior

## Normative Requirements

**AUTH-BASE-001 — Authority before mutation**  
An implementation MUST evaluate the effective authority applicable to an operation before accepting a canonical mutation. Possession of object data, provider credentials, or an application connection MUST NOT itself imply authority. Actor identifiers recorded in Canonical History, restored or imported object content, and governance references MUST NOT themselves grant authority: the presence of an actor in accepted history does not give that actor current authority, and effective authority is determined by the governance applicable where the operation is evaluated.

**AUTH-NARROW-001 — No downstream expansion**  
Authority-bearing downstream context MUST NOT silently expand authority beyond its upstream sources. Where multiple authority bounds apply, the effective authority MUST be no broader than each applicable bound.

**AUTH-APP-001 — Application-bound authority**  
If Application Context participates in authorization, an application MAY narrow the actor's effective authority but MUST NOT grant authority absent from the actor or another applicable upstream source. Application identity MUST remain distinct from actor and Canonical Identity.

**AUTH-QUERY-001 — Authorized disclosure**  
If an implementation supports object read, query, or relationship traversal, it MUST NOT return objects or protected fields that the effective authority cannot read, whether as results or in the details of a denied request (`AUTH-DISCLOSE-001`). It SHOULD avoid disclosing object existence through denial details where the declared policy requires existence concealment.

**AUTH-GRAPH-001 — No inaccessible traversal bridge**  
If an implementation supports graph traversal, an inaccessible object MUST NOT be used as a traversal bridge to reveal otherwise inaccessible descendants, paths, counts, or relationship structure.

**AUTH-DISCLOSE-001 — Disclosure-safe rejection**  
An outcome that does not accept a request MUST NOT disclose protected information to a requester whose effective authority does not permit reading it. Protected information is information about the target object that the requester's effective authority does not permit it to read. For a requester without read authority for the object, it includes at least the current Object Version, Accepted Canonical State including lifecycle state, Canonical Relationships, and Canonical History. Specifically:

1. the outcome's returned content, including denial, conflict, or invalidity details, error text, diagnostic fields, and any embedded audit record, MUST NOT contain protected information; and
2. if the requester also lacks authority for the requested operation, the outcome class and the returned content MUST NOT vary with protected information. This includes whether a supplied expected Object Version matches the current Object Version, and whether a state-dependent or relationship-dependent validation would succeed.

This requirement applies to read, query, and mutation requests. Returned content MAY vary with information that is not protected, such as request identifiers or timestamps: byte-identical responses are not required. Timing and other side channels outside the outcome class and the returned content are not within the scope of this requirement. Whether an object exists for a Canonical Identity is not protected information for the purposes of this requirement: it does not require concealment of whether an object exists. Existence concealment remains governed by the declared policy under `AUTH-QUERY-001`. Content supplied by the requester, such as its own expected Object Version, is not protected information. This requirement does not govern the content of accepted outcomes.

**AUTH-ORDER-001 — Authorization-first evaluation**  
An implementation SHOULD determine that the requester has sufficient authority for the requested operation before evaluating expected-version preconditions, or state-dependent or relationship-dependent validation, whose results could reveal protected information. Evaluating in another order does not by itself violate any mandatory requirement of this revision, provided `AUTH-DISCLOSE-001` is satisfied. It is a deviation from this recommendation only. This requirement does not require implementations to report the same outcome class to a requester that is authorized to read the information the outcome would reveal.

## Policy-Engine Independence

`v0alpha1` specifies authorization outcomes, not a policy language. Capability lists, role systems, relationship-based authorization, Cedar, OpenFGA, custom evaluators, and other mechanisms may satisfy the same semantics.

Policy storage, compilation, caching, and administration are implementation concerns unless and until a portable policy profile is defined.

## Disclosure and Check Ordering

Draft2.1 tied concealment to what an implementation promised. Draft2.2 makes disclosure safety unconditional for protected information (`AUTH-DISCLOSE-001`). Existence concealment remains policy-conditional.

A rejection can disclose protected information through two channels:

- **Returned content.** A current Object Version, lifecycle state, relationships, history, or an embedded audit record in a denial or conflict result. Clause 1 forbids this for every requester who cannot read the information.
- **Outcome variation.** For example, "stale token → CONFLICT, current token → AUTHORIZATION_DENIED" lets a requester test guesses of the current Object Version without reading it. Clause 2 forbids this for a requester that can neither read the information nor perform the operation. Such a requester therefore receives the same outcome for a stale, current, or absent expected version. In practice that is an authorization denial.

The order of internal checks is not itself the defect. Evaluating an expected-version precondition first conforms if neither channel discloses anything. Authorization-first evaluation (`AUTH-ORDER-001`) is the simplest way to satisfy clause 2.

Clause 2 does not cover a requester authorized to perform the operation but not to read the object. For that requester, `CON-CONFLICT-001` requires a stale conditional mutation to be rejected, ordinarily with CONFLICT, while a current one proceeds. The outcome therefore reflects whether its observation is current. Clause 1 still forbids returning the current Object Version or other protected content to it in a non-accepted outcome. What an accepted outcome may reveal to such a requester, for example the new Object Version, is not defined in draft2.2. The same limitation applies to an accepted no-op's unchanged Object Version ([Open Questions](open-questions.md#draft21-deferred-issues)).

A requester that can read the object can already observe the information a conflict or invalidity outcome reveals. Implementations may therefore report different outcome classes to such a requester for the same multiply-failing request. For example, one implementation may report CONFLICT and another AUTHORIZATION_DENIED to a reader that lacks mutation authority and supplies a stale version. Both conform. `AUTH-ORDER-001` recommends the latter.

Relationship existence follows the same principle:

- Authorization applies to every relationship add or remove, including one whose effect already holds (`REL-AUTH-001`).
- An unauthorized requester receives an authorization denial whether or not the relationship exists. It therefore cannot use duplicate-add or absent-remove outcomes to probe relationship existence.
- Ordering among authorization, validation, and version checks remains implementation-defined within `REL-PRECEDENCE-001` and `AUTH-DISCLOSE-001`.

Request-shape validation that depends only on the request, such as an empty relationship type, may precede authorization. It reveals nothing about the object.

## Historical Actors

Accepted history records who performed accepted changes as evidence, not capability (`AUTH-BASE-001`). A restored or imported object's history may name actors that match local principals. Those names confer nothing. Operations after restoration are authorized only by the governance applicable where they are evaluated.

## Applications and Interchangeability

Application Context is not a mandatory Core resource type. The portable behavior is that:

- applications operate canonical objects rather than owning their identity or history;
- application vocabulary may map to canonical type semantics; and
- replacing an application does not inherently replace Canonical Identity or Canonical History.

See [Portability](portability.md) for application replacement requirements.
