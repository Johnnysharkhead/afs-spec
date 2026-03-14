# AFS Whitepaper v0.1 — Agent-Friendly Standard

<!-- TODO: This is a structural outline. Replace each section with full prose before publication. -->

*Version:* 0.1 (Draft)
*Status:* Work in progress
*Authors:* <!-- TODO: Add author names and affiliations -->

---

## 1. Introduction

<!-- TODO: Describe the purpose of this whitepaper and the intended audience. -->

- What problem does AFS solve?
- Who should read this document?
- How this whitepaper relates to the normative specification in `spec/`.

---

## 2. Problems with Current AI + Software Interaction

<!-- TODO: Expand with concrete examples, data points, and references where available. -->

- **Discoverability** — Agents cannot reliably locate available capabilities.
- **Semantic ambiguity** — API documentation is written for humans, not machines.
- **Fragmented authentication** — Every service uses a different auth model.
- **No standard error semantics** — Failures are communicated inconsistently.
- **Safety gaps** — Side effects and data sensitivity are rarely declared upfront.

---

## 3. AFS Core Principles

<!-- TODO: Refine these principles with the community before v1.0. -->

1. **Explicit over implicit** — Capabilities, inputs, outputs, and constraints must be declared, not inferred.
2. **Layered adoption** — Implementors can adopt AFS incrementally, one layer at a time.
3. **Human-readable and machine-parseable** — Skill definitions must be useful to both developers and agents.
4. **Minimal overhead** — AFS should add as little friction as possible to existing systems.
5. **Safety by default** — Destructive or sensitive operations must be explicitly flagged.

---

## 4. High-Level Architecture

<!-- TODO: Add a diagram (e.g., Mermaid or SVG) illustrating the four layers. -->

AFS organises its concerns into four layers:

| Layer | Responsibility |
|-------|---------------|
| **Interface Layer** | How agents invoke capabilities (endpoints, protocols, auth). |
| **Documentation Layer** | How capabilities are described (skill definitions, schemas). |
| **Efficiency Layer** | How interactions are optimised (caching, batching, pagination). |
| **Security Layer** | How trust and safety are enforced (permissions, rate limits, audit). |

Each layer is independently adoptable and builds on the one below it.

---

## 5. Security & Safety

<!-- TODO: Detail threat models, recommended mitigations, and compliance considerations. -->

- **Scope declaration** — Skills must declare the minimum required permissions.
- **Side-effect labelling** — Read-only vs. write operations must be distinguishable.
- **Data sensitivity** — PII and confidential data fields must be marked.
- **Rate limiting & quotas** — Standard headers and error codes for throttling.
- **Audit trail** — Recommended logging patterns for agent-initiated actions.

---

## 6. Future Work

<!-- TODO: Update this section as the roadmap evolves. -->

- Formal JSON Schema for skill definitions.
- Reference validator and linter tooling.
- Registry protocol for skill discovery.
- Versioning and deprecation conventions.
- Interoperability testing suite.

---

*© Norrai-lab contributors. Licensed under the MIT License.*