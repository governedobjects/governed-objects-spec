# Governance

## Current Governance

The project is currently **founder-led** while the architecture and initial specification are being developed and validated. During this stage, Brafford Labs, LLC maintains the Governed Objects Specification and makes final decisions on its content.

There is no formal steering committee, contributor voting process, or certification body at this stage. Decisions about architecture direction, experimental phases, and documentation are made pragmatically to test whether the business sovereignty model can be implemented coherently.

This is intentional. Long-term governance has **not** been finalized.

## Decision Making During Experimental Stage

During this stage, decisions prioritize:

1. **Fidelity to [PRINCIPLES.md](PRINCIPLES.md)** — especially business sovereignty and replaceability
2. **Evidence from working experiments** — phases should demonstrate claims with tests and demos
3. **Honest limits** — unobserved vendor changes, projection gaps, and partial provider support must remain visible
4. **Separation of semantics from implementation**

Experimental phases may be superseded. Implementation details may change. Principles should remain the evaluation baseline.

## Relationship to PRINCIPLES.md

[PRINCIPLES.md](PRINCIPLES.md) is the project's architectural constitution during the experimental period.

Any future governance model should:

- Preserve the Business Sovereignty Principles
- Protect the portability of the specification
- Avoid giving any single implementation vendor unilateral control over what constitutes a conforming Governed Object system
- Enable conformance to be evaluated independently, without requiring permission from, dependence on, or access to a particular commercial provider

Governance processes should not be used to entrench one runtime, one hosted service, or one commercial vendor as mandatory for organizations to retain their canonical business state.

## Specification and Implementation Governance

Governance of a particular runtime, hosted service, distribution, or commercial implementation may differ from governance of the portable Governed Objects Specification.

A company or maintainer may legitimately control its own implementation, service, roadmap, and operational features. That control should not by itself confer permanent unilateral authority over portable specification semantics or technical conformance requirements.

Brafford Labs, LLC also develops implementations of the specification. Its role as specification maintainer is a current-stage arrangement, not a claim of permanent unilateral authority.

Likewise, the existence of a founding or dominant implementation should not make that implementation a mandatory dependency for organizations to retain or interpret canonical business state.

**Future governance should avoid allowing financial scale, implementation market share, or contribution volume alone to confer unilateral control over portable specification semantics.**

Large implementers may contribute substantial engineering resources and should be able to participate meaningfully. However, contribution scale alone should not allow one organization to redefine portable semantics in ways that make independent implementations or smaller participants structurally dependent on it.

**Future governance changes should be evaluated against the same Business Sovereignty Principles used to evaluate technical architecture.**

A governance change that makes the portable specification materially dependent on a particular implementation or provider should therefore be treated as a sovereignty tradeoff and made explicit.

## Versions and Stability

- A version labeled Final never changes after publication.
- Experimental drafts are clearly labeled and carry no stability promise. Drafts may change at any time, without notice, including in ways that break implementations.
- Once Final versions exist: editorial fixes do not change meaning; additions that keep existing implementations valid are minor revisions; changes that may break existing implementations are major revisions, announced at least 90 days before becoming Final, with migration notes. This notice requirement applies only to Final versions.
- Security exception: if a Final version contains a flaw that creates a serious security risk, a breaking correction may be published with shorter notice, or without notice, when waiting would cause greater harm. The reasons are recorded in the change log.
- Each revision keeps stable requirement identifiers and a change log, as the current drafts do.

## Proposing Changes

Anyone may propose changes by opening an issue in the specification's public GitHub repository. Proposals receive a response, and the reasons for accepted and declined changes are recorded in the change log.

## Conformance and Certification

- Anyone may claim conformance to a named version by passing the openly available conformance suite for that version. No permission from Brafford Labs, LLC or any other provider is required.
- Until an executable conformance suite exists for a version, implementations may state only that they implement it.
- The openly available conformance suite aims to test every normative requirement that can be observed from outside an implementation. Additional testing tools, including commercial tools from Brafford Labs, LLC, may test beyond the specification (for example deeper security, performance or operational checks), but are never needed to establish conformance. If any such tool reveals a normative requirement the open suite does not adequately test, a corresponding test is added to the open suite.
- Any certification mark offered by Brafford Labs, LLC is an optional extra and is never required to claim conformance.
- If a neutral foundation or similar body is formed for the specification, Brafford Labs, LLC will transfer the certification mark and program to it.
- Use of the Governed Objects name is described in [TRADEMARKS.md](TRADEMARKS.md).

## Future Governance Goals

When the project moves beyond experimental validation, governance should aim to:

- Keep normative specification semantics independent of any one codebase
- Enable multiple conforming implementations to compete on quality, operations, and support
- Provide transparent processes for specification evolution
- Protect the specification and ecosystem from capture — particularly situations where "conformance" effectively requires dependence on one vendor

The specific structure (foundation, steering committee, etc.) remains to be decided.

## What Is Not Yet Decided

The following are **explicitly deferred**:

- Foundation or nonprofit structure
- Technical steering committee structure and membership rules
- Formal contributor voting or election processes
- Long-term specification ownership and stewardship
- Certification program details (the principles above are fixed)
- Commercial model for any future implementation or service
- Relationship between reference implementation maintainers and specification editors

None of these should be inferred from the existence of this repository or of any runtime.

## Contributing

Contributions are accepted under the Apache License 2.0 (Section 5), with a Developer Certificate of Origin sign-off on each commit. Contributors of normative text also make the commitment in [PATENTS.md](PATENTS.md) for their contributions.

## License

The Governed Objects Specification is licensed under the [Apache License 2.0](LICENSE). See [NOTICE](NOTICE), [PATENTS.md](PATENTS.md) for the implementation patent commitment, and [TRADEMARKS.md](TRADEMARKS.md) for use of the name. The Apache License does not grant trademark rights.
