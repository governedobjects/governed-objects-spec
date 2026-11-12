# Governed Objects Specification

This directory contains experimental specification work for the Governed Object model.

Documents marked *(not published)* are internal working records; the published revision is self-contained without them.

Draft navigation:

- `v0alpha1 draft1` (earlier draft, not published) — historical first extraction from the Python reference runtime
- `v0alpha1 draft2` (earlier draft, not published) — historical two-implementation clarification revision
- `v0alpha1 draft2.1` (earlier draft, not published) — historical portable relationship-set revision
- [`v0alpha1 draft2.2`](v0alpha1-draft2.2/specification.md) — current v0alpha1 draft

## Status

`v0alpha1 draft2.2` is the current v0alpha1 **Experimental Draft**. It carries forward every draft2.1 requirement identifier. It adds Core semantics for Object Version uniqueness, a single Canonical History lineage, disclosure-safe rejection, conditional restoration, and faithful-or-refuse preservation. It also adds the optional Portable Restoration Continuity profile. These additions draw on cross-runtime portable-restoration experiments. Draft1, draft2, and draft2.1 remain historically intact.

The specification defines portable semantics, optional capability profiles, candidate conformance behavior, and unresolved questions. It does not standardize either runtime's programming language, storage engine, object model, command-line interface, deployment topology, or serialization layouts.

An open Core conformance suite (0.1.0) exists at <https://github.com/governedobjects/governed-objects-conformance> and has been run against both runtimes. Conformance claims follow [TRADEMARKS.md](../TRADEMARKS.md) and name the suite version. This specification itself declares no implementation conformant. Draft2.2 does not establish wire-level interoperability, interchange interoperability, certification, or production stability.

## Document Boundaries

- [Current draft2.2 specification](v0alpha1-draft2.2/specification.md) — status, scope, profiles, and document map
- [Core semantics](v0alpha1-draft2.2/core.md) — the smallest required Governed Object model
- [Optional profiles](v0alpha1-draft2.2/specification.md#conformance-profiles) — delegation, observations, provider binding, canonical authority, agent operation, and portable restoration continuity
- [Conformance](v0alpha1-draft2.2/conformance.md) — candidate observable tests and evidence mapping
- [Implementation notes](v0alpha1-draft2.2/implementation-notes.md) — non-normative implementation choices
- [Open questions](v0alpha1-draft2.2/open-questions.md) — areas intentionally not standardized

## Foundations

The specification translates architectural evidence into independently implementable semantics. It is constrained by, but does not copy normative requirements mechanically from:

- [Project Principles](../PRINCIPLES.md)
- [Governance](../GOVERNANCE.md)

The principles remain design intent and evaluation criteria. This specification identifies the subset that can currently be expressed as observable technical behavior.
