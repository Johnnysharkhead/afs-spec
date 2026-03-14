# AFS Technical Specification — Overview

*Version: 0.2 (Draft)*

This document is the entry point for the **Agent-Friendly Standard (AFS)** technical specification. For background and motivation, see the [whitepaper](../whitepaper/afs-whitepaper-v0.1.md).

---

## How to Read This Spec

The keywords "MUST", "SHOULD", "MAY", and "MUST NOT" in all spec documents are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

---

## Design Principles

1. **Explicit over implicit** — Capabilities, inputs, outputs, and constraints MUST be declared, not inferred.
2. **Layered adoption** — Implementors can adopt AFS incrementally, one layer at a time.
3. **Human-readable and machine-parseable** — Skill definitions MUST be useful to both developers and agents.
4. **Minimal overhead** — AFS SHOULD add as little friction as possible to existing systems.
5. **Safety by default** — Destructive or sensitive operations MUST be explicitly flagged.

---

## Three Interface Surfaces

Every AFS-compliant tool MUST provide at least one, and SHOULD provide all three of the following interaction surfaces, backed by the same core logic:

| Interface | Audience | Requirements |
|-----------|----------|-------------|
| **API** | AI Agents (primary channel) | MUST: Structured JSON I/O, explicit schemas |
| **CLI** | Developers / Ops | SHOULD: Thin wrapper over the API for debugging and scripting |
| **Human UI** | End users | MAY: Graphical interface for direct user interaction |

The API is the canonical interface. The CLI and Human UI are convenience layers that MUST NOT expose capabilities absent from the API.

---

## Specification Documents

The spec is organized into the following documents:

| Document | Description |
|----------|-------------|
| **[Layer 1 — Interface](./layer-1-interface.md)** | Transport, endpoints, discovery, authentication, MCP compatibility |
| **[Layer 2 — Documentation](./layer-2-documentation.md)** | skill.md format, progressive disclosure, user-best-practice.md |
| **[Layer 3 — Efficiency](./layer-3-efficiency.md)** | Compact responses, verbosity control, pagination, caching, streaming |
| **[Layer 4 — Security](./layer-4-security.md)** | Permissions, side-effects, dangerous operations, rollback, audit, code signing |
| **[skill.md Schema](./skill-schema.md)** | Canonical JSON Schema and field reference for skill.md |
| **[Glossary](./glossary.md)** | Definitions of key terms used throughout the spec |
| **[Conformance Levels](./conformance.md)** | What it means to be AFS Level 1 / 2 / 3 compliant |

---

*This specification is in early draft form. All rules are subject to change before v1.0.*
