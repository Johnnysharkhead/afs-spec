# AFS — Agent-Friendly Standard

> An open specification for building agent-friendly systems.

---

## What is AFS?

<!-- TODO: Expand this section with a more detailed description of AFS. -->

AFS (Agent-Friendly Standard) is an open specification that defines how software systems should expose their capabilities so that AI agents can discover, understand, and interact with them reliably. AFS provides a common vocabulary, layered architecture, and lightweight conventions that make any service or tool "agent-ready" without requiring changes to its core business logic.

---

## Why AFS?

<!-- TODO: Add concrete motivating examples and pain points here. -->

Modern AI agents must navigate a fragmented landscape of APIs, documentation formats, and authentication schemes. Without a shared standard:

- Agents waste time inferring undocumented behaviour.
- Developers duplicate integration work across every agent framework.
- Security and safety properties are left implicit or inconsistent.

AFS solves this by giving both builders and agents a single, predictable contract.

---

## Repository Structure

```
afs-spec/
├── README.md               ← You are here
├── LICENSE                 ← MIT License
├── whitepaper/
│   └── afs-whitepaper-v0.1.md   ← High-level vision and rationale
├── spec/
│   └── overview.md              ← Technical specification (layers & rules)
└── examples/
    └── afs-email/
        └── skill.md             ← Worked example: AFS Email skill
```

| Directory      | Purpose |
|----------------|---------|
| `whitepaper/`  | Narrative documents explaining the motivation, core principles, and high-level architecture of AFS. |
| `spec/`        | Normative technical specification. Defines the four AFS layers and their rules. |
| `examples/`    | Concrete, annotated examples of AFS-compliant skill definitions. |

---

## Getting Involved

<!-- TODO: Add links to discussions, contribution guide, and community channels once they exist. -->

AFS is an open project and welcomes contributions of all kinds:

- **Feedback** — Open an issue to suggest changes or flag ambiguities.
- **Proposals** — Submit a pull request against `spec/` or `whitepaper/`.
- **Examples** — Add a new directory under `examples/` with a `skill.md` for your use-case.

Please read `CONTRIBUTING.md` (coming soon) before submitting your first PR.

---

## Roadmap

<!-- TODO: Keep this roadmap in sync with project milestones. -->

| Milestone | Status |
|-----------|--------|
| v0.1 — Whitepaper draft & repository scaffold | 🟡 In progress |
| v0.2 — Normative spec for Interface & Documentation layers | ⬜ Planned |
| v0.3 — Efficiency & Security layers + JSON Schema for skill definitions | ⬜ Planned |
| v1.0 — Stable specification, reference validator, and official examples | ⬜ Planned |

---

*This repository is documentation-first. No build tooling or code is included at this stage.*
