# Agent Operation Profile

**Version:** v0alpha1  
**Revision:** draft2.2  
**Status:** Optional Thin Profile  
**Depends on:** Core; Delegated Authority when delegation is used  
**Evidence confidence:** MODERATE

## Scope

Agent operation introduces little new Governed Object machinery. An agent is an actor using Core operations. Deterministic software and probabilistic models are reasoning implementations outside the authority decision.

This profile exists to make the trust boundary testable without standardizing model providers, prompts, tools, or session classes.

## Normative Requirements

**AGENT-ACTOR-001 — Agent uses ordinary authority**  
An agent MUST act through the same governed operation and authorization semantics as other actors. Agent status MUST NOT provide an authorization bypass.

**AGENT-AUTH-001 — Reasoning grants no authority**  
A model or agent proposal MUST NOT gain authority merely because it selected an object, action, parameter, tool, or expected version. The runtime MUST independently evaluate the bound actor and every applicable application, delegation, purpose, and object-policy constraint.

**AGENT-BIND-001 — Authority context is trusted input**  
Authority-bearing identity and context MUST be bound or verified outside untrusted model output. A model MUST NOT be able to replace actor, principal, application, mandate, or purpose identity by supplying operation arguments.

**AGENT-TRACE-001 — Trace separation**  
Model reasoning, prompts, tool traces, and chain-of-thought MUST NOT be required to interpret Canonical History or replay Accepted Canonical State. Operational traces MAY be retained separately subject to applicable security and privacy policy.

## Non-Requirements

This profile does not require:

- an LLM;
- a tool-calling protocol;
- a fixed tool registry;
- a prompt format;
- chain-of-thought persistence;
- a model provider;
- bounded step counts; or
- a distinct agent policy engine.

An implementation using no probabilistic model may still claim this profile if software agents operate under the specified boundary.
