# AFS Technical Specification — Overview

<!-- TODO: This document is the authoritative technical reference for AFS. Expand each section with normative language (MUST / SHOULD / MAY) before v1.0. -->

This document is the technical specification for the **Agent-Friendly Standard (AFS)**. It defines the four layers that a compliant system must implement, along with the rules and conventions that govern each layer.

For background and motivation, see the [whitepaper](../whitepaper/afs-whitepaper-v0.1.md).

---

## Layer 1 — Interface Layer

*Defines how agents invoke capabilities: transport, endpoints, and authentication.*

<!-- TODO: Replace placeholder bullets with normative rules. -->

- A compliant endpoint MUST be reachable over HTTPS and respond to a standard health-check path (e.g., `GET /.well-known/afs`).
- Authentication schemes (API key, OAuth 2.0, none) MUST be declared in the skill definition and MUST NOT be inferred from behaviour alone.
- All requests and responses MUST use UTF-8 encoded JSON unless an alternative encoding is explicitly declared in the skill definition.

---

## Layer 2 — Documentation Layer

*Defines how capabilities are described: skill definitions, input/output schemas, and human-readable metadata.*

<!-- TODO: Define the canonical schema format and required vs. optional fields. -->

- Every capability MUST be described by a skill definition file that conforms to the AFS skill schema (to be formalised in v0.2).
- Skill definitions MUST include: `name`, `description`, `version`, `capabilities`, `inputSchema`, and `outputSchema`.
- Human-readable descriptions SHOULD be written in plain English and MUST NOT exceed 500 characters per field.

---

## Layer 3 — Efficiency Layer

*Defines how agent-system interactions are optimised: caching, batching, and pagination.*

<!-- TODO: Specify cache-control headers, batch request envelope format, and pagination conventions. -->

- Responses MUST include standard HTTP cache-control headers where caching is appropriate.
- Systems that support batch operations SHOULD expose a dedicated batch endpoint following the AFS batch envelope format.
- Paginated responses MUST include a `nextCursor` field (or equivalent) and MUST declare the maximum page size in the skill definition.

---

## Layer 4 — Security Layer

*Defines how trust and safety are enforced: permissions, side-effect declarations, and audit logging.*

<!-- TODO: Define the permission scope vocabulary and audit log format. -->

- Skills MUST declare a `permissions` list containing only the minimum required scopes.
- Any capability that writes, deletes, or modifies data MUST set `sideEffects: true` in its skill definition.
- Implementations SHOULD emit structured audit log entries for every agent-initiated action, including a timestamp, agent identifier, and the capability invoked.

---

*This specification is in early draft form. All rules are subject to change before v1.0.*