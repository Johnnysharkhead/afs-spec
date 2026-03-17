[English](./README.md) | [中文](./README.zh.md)

# AFS — Agent-Friendly Standard

> Don't teach AI to use human software — make software a good tool for AI.

**AFS (Agent-Friendly Standard)** is an open specification that defines how software should be designed from the ground up so that AI agents can discover, understand, and interact with it safely, efficiently, and reliably.

---

## Why AFS?

Modern AI agents are forced to interact with software built for humans — through screenshots, simulated clicks, and brittle browser automation. Meanwhile, every SaaS product ships its own siloed AI assistant, creating fragmented context and inability to collaborate across applications. Tool-driven approaches like MCP suffer from tool bloat that compresses the agent's reasoning space. And agent frameworks like OpenClaw lack purpose-built tools, forcing system-level permissions with uncontrollable blast radius.

AFS flips the model: instead of making AI adapt to human software, AFS defines how software should be designed from the ground up to be a good tool for AI agents.

### The four dilemmas AFS solves

- **Screen-level automation is expensive and unreliable** — AI wastes massive tokens processing screenshots; page timeouts and layout changes cause frequent failures.
- **Embedded AI assistants are siloed** — ten apps means ten separate AIs that can't share context or collaborate.
- **MCP tool explosion** — exposing all interfaces at once bloats the context, degrades reasoning accuracy, and increases latency.
- **Agent frameworks lack security boundaries** — platforms like OpenClaw must grant system-level permissions, making the blast radius uncontrollable.

### What AFS provides

- **Three-layer tool architecture** — API (core logic) + CLI (agent operation channel) + GUI (optional human observation channel), sharing one set of core logic.
- **skill.md** — A standardized, machine-readable capability manifest with progressive disclosure (L0–L3, load only what you need).
- **Three compliance levels** — AFS-1 (Callable), AFS-2 (Secure & Controllable), AFS-3 (Supervisable), adoptable incrementally.
- **Security by architecture** — Process-level isolation, risk annotation, CLI parameter validation, and auditability.
- **Complementary to MCP** — AFS defines how tools should be designed; MCP defines how agents and tools communicate. Best practice is to adopt both.
- **Agent-agnostic** — Works with OpenClaw, Claude Code, or any AI Agent that can read skill.md and execute CLI commands.

---

## Repository Structure

```
afs-spec/
├── README.md                          ← You are here
├── LICENSE                            ← Dual license overview
├── LICENSE-CC-BY-4.0                  ← CC BY 4.0 (documentation)
├── LICENSE-MIT                        ← MIT (code)
├── whitepaper/
│   ├── afs-whitepaper-eng.md          ← Whitepaper v0.2 (English)
│   └── afs-whitepaper-zh.md           ← Whitepaper v0.2 (Chinese)
├── spec/
│   └── overview.md                    ← Technical specification
└── examples/
    └── afs-email/
        └── skill.md                   ← Worked example: AFS Email skill
```

| Directory | Purpose |
|-----------|---------|
| `whitepaper/` | Narrative documents explaining the motivation, design philosophy, technical architecture, and security model of AFS. |
| `spec/` | Normative technical specification. |
| `examples/` | Concrete, annotated examples of AFS-compliant skill definitions. |

---

## Quick Start

1. **Read the whitepaper** — [`whitepaper/afs-whitepaper-eng.md`](whitepaper/afs-whitepaper-eng.md) for the full vision and motivation ([中文版](whitepaper/afs-whitepaper-zh.md)).
2. **Read the spec** — [`spec/overview.md`](spec/overview.md) for the technical specification.
3. **See an example** — [`examples/afs-email/skill.md`](examples/afs-email/skill.md) for a worked skill definition.

---

## Getting Involved

AFS is an open project and welcomes contributions of all kinds:

- **Feedback** — Open an issue to suggest changes or flag ambiguities.
- **Proposals** — Submit a pull request against `spec/` or `whitepaper/`.
- **Examples** — Add a new directory under `examples/` with a `skill.md` for your use case.

---

## Roadmap

| Milestone | Status |
|-----------|--------|
| v0.1 — Whitepaper + repository scaffold | ✅ Complete |
| v0.2 — Whitepaper v0.2 (three-layer tool architecture, security model, compliance levels) | ✅ Complete |
| v0.3 — Normative spec alignment + JSON Schema for skill definitions | 🟡 In progress |
| AFS Software Marketplace — open tool marketplace with security review and compliance verification | ⬜ Planned |
| AFS Shell — unified CLI operating environment for AI agents | ⬜ Planned |
| v1.0 — Stable specification, reference validator, and official examples | ⬜ Planned |

---

*This project is documentation-first. No build tooling or code is included at this stage.*

*© Norrai-lab contributors. Documentation licensed under [CC BY 4.0](LICENSE-CC-BY-4.0). Code licensed under [MIT](LICENSE-MIT).*
