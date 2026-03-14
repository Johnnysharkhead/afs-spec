# AFS Technical Specification — Overview

*Version: 0.1 (Draft)*

This document is the technical specification for the **Agent-Friendly Standard (AFS)**. It defines the four layers that a compliant system must implement, along with the rules and conventions that govern each layer.

For background and motivation, see the [whitepaper](../whitepaper/afs-whitepaper-v0.1.md).

---

## Design Principles

The keywords "MUST", "SHOULD", "MAY", and "MUST NOT" in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

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

## Layer 1 — Interface Layer

*Defines how agents invoke capabilities: transport, endpoints, and authentication.*

### Transport

- A compliant endpoint MUST be reachable over HTTPS.
- A compliant endpoint MUST respond to a standard discovery path: `GET /.well-known/afs` returning a JSON document that links to the tool's `skill.md` and API base URL.
- All requests and responses MUST use UTF-8 encoded JSON unless an alternative encoding is explicitly declared in the skill definition.

### MCP Compatibility

- Every AFS tool MUST also be a valid [MCP (Model Context Protocol)](https://modelcontextprotocol.io/) server.
- AFS extends MCP with additional quality requirements defined in Layers 2–4. An AFS tool is a strict superset of an MCP server.

### Authentication

- Authentication schemes (API key, OAuth 2.0, none) MUST be declared in the skill definition under the `auth` field.
- Authentication MUST NOT be inferred from behaviour alone.
- Tools that require no authentication MUST explicitly declare `auth: { type: none }`.

---

## Layer 2 — Documentation Layer

*Defines how capabilities are described: skill definitions, schemas, and progressive disclosure.*

### skill.md

Every AFS tool MUST include a `skill.md` file — a structured capability manifest with YAML frontmatter and Markdown body. The `skill.md` MUST be organized into progressive disclosure layers:

| Layer | Content | When Loaded |
|-------|---------|-------------|
| **Layer 0 — Overview** | `name`, `version`, `description` (one sentence) | Always — agent reads this to decide relevance |
| **Layer 1 — Capabilities** | `capabilities` list with `inputSchema` and `outputSchema` per capability | When the agent decides to use this tool |
| **Layer 2 — Examples** | Usage examples and workflow templates (Markdown body) | On demand for complex scenarios |
| **Layer 3 — Advanced** | Edge cases, error codes, performance tuning (Markdown body) | Rarely — only for deep troubleshooting |

### Required Fields (Frontmatter)

The YAML frontmatter of `skill.md` MUST include:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | MUST | Unique identifier for the skill |
| `version` | string | MUST | Semantic version (e.g., `"1.0.0"`) |
| `description` | string | MUST | One-sentence description, max 200 characters |
| `sideEffects` | boolean | MUST | `true` if any capability writes, deletes, or modifies data |
| `permissions` | string[] | MUST | Minimum required permission scopes |
| `capabilities` | string[] | MUST | List of capability names |
| `inputSchema` | object | MUST | JSON Schema per capability for inputs |
| `outputSchema` | object | MUST | JSON Schema per capability for outputs |
| `auth` | object | MUST | Authentication configuration |

### user-best-practice.md

Users MAY create a `user-best-practice.md` file alongside the tool's `skill.md` to define recurring workflows and personal preferences. AI Agents SHOULD read this file when present and incorporate its instructions into task execution.

---

## Layer 3 — Efficiency Layer

*Defines how agent-system interactions are optimised: compact responses, verbosity control, pagination, and streaming.*

### Compact Responses

- API responses MUST return structured data optimized for machine consumption, not human-readable prose.
- Responses SHOULD omit null or empty fields by default.
- Tools SHOULD support a `verbosity` parameter (`minimal` | `standard` | `detailed`) to let agents control response size.

### Pagination

- Paginated responses MUST include a `nextCursor` field (or equivalent).
- Tools MUST declare the default and maximum page size in the skill definition.

### Caching

- Responses MUST include standard HTTP cache-control headers where caching is appropriate.
- Tools SHOULD declare cache TTL hints in the skill definition for capabilities that return relatively stable data.

### Streaming

- For long-running operations, tools SHOULD support streaming responses using Server-Sent Events (SSE) or equivalent.
- Streaming endpoints MUST be declared in the skill definition.

---

## Layer 4 — Security Layer

*Defines how trust and safety are enforced: permissions, side-effect declarations, dangerous-operation confirmations, and audit logging.*

### Permission Model

- Skills MUST declare a `permissions` list containing only the minimum required scopes.
- Agents MUST NOT invoke capabilities that require permissions beyond what has been granted.

### Side-Effect Labelling

- Any capability that writes, deletes, or modifies data MUST set `sideEffects: true` in its skill definition.
- Read-only capabilities MUST set `sideEffects: false` or omit the field (default: `false`).

### Dangerous Operation Confirmation

- Capabilities that perform irreversible or high-impact actions (e.g., deleting data, sending external communications) MUST be marked with `dangerousAction: true`.
- Agents SHOULD require explicit user confirmation before invoking dangerous actions.

### Rollback

- Tools that support undo/rollback SHOULD expose a `rollback` capability and declare it in the skill definition.
- The rollback capability MUST accept an operation ID and attempt to reverse the corresponding action.

### Audit Logging

- Implementations SHOULD emit structured audit log entries for every agent-initiated action, including:
  - Timestamp (ISO 8601)
  - Agent identifier
  - Capability invoked
  - Input parameters (with sensitive fields redacted)
  - Outcome (success / failure)

### Code Signing

- Published skill definitions SHOULD be cryptographically signed.
- Skill registries SHOULD verify signatures before listing skills.

---

*This specification is in early draft form. All rules are subject to change before v1.0.*
