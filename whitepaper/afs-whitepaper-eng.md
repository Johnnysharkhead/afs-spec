---
title: "AFS — AI/Agent-Friendly Standard"
subtitle: "Whitepaper v0.2"
author:
  - Norrai-lab
date: "March 2026"
abstract: |
  AFS (Agent-Friendly Standard) is an open specification that defines how software
  should be designed from the ground up so that AI agents can discover, understand,
  and interact with it safely, efficiently, and reliably. Rather than forcing AI to
  navigate human interfaces, AFS makes software a first-class tool for AI agents.
keywords: [Agent-Oriented System Design, Software Engineering, AI Agent, MCP]
titlepage: true
titlepage-color: "1a1a2e"
titlepage-text-color: "FFFFFF"
titlepage-rule-color: "e94560"
toc-own-page: true
number-sections: true
colorlinks: true
linkcolor: "e94560"
---


# AFS Whitepaper v0.2 — Agent-Friendly Standard

*"Don't teach AI to adapt to human software — make software built for AI."*

*Version:* 0.2 (Draft)
*Status:* Work in progress
*Date:* March 2026

---

## 1. Introduction

We are witnessing a paradigm shift in how humans interact with software. The rise of autonomous AI agents — capable of reasoning, planning, and executing multi-step workflows — demands a fundamental rethinking of software design. Today's software was built for humans, and AI agents are forced to navigate this human-centric landscape, wasting enormous resources, introducing security vulnerabilities, and producing unreliable results.

**AFS (AI/Agent-Friendly Standard)** is an open specification that flips the script. Instead of making AI adapt to human software, AFS defines how software should be designed from the ground up to be a good tool for AI agents. AFS provides a common vocabulary, layered architecture, and lightweight conventions that make any service or tool "agent-ready" — enabling safe, efficient, and reliable interactions.

This whitepaper explains the motivation behind AFS, its core design philosophy, technical architecture, and positioning within the broader AI + software ecosystem. For the normative technical specification, see [`spec/overview.md`](../spec/overview.md).

---

## 2. The Problem: Four Dilemmas of AI + Software Interaction

### 2.1 Screen-Level Operation — Making AI Mimic Humans

One current approach is to make AI "learn" to use human software — through screenshots, simulated clicks, and browser automation. This approach has three fatal flaws: AI must process massive screenshots and UI metadata, making token consumption extremely high; page timeouts, button location failures, and layout changes cause frequent operation failures; many websites deploy anti-bot measures that actively block automated access.

**Core bottleneck: Extremely poor reliability, extremely high cost.**

### 2.2 In-App Embedded AI — Fragmented Silos

Another approach is for every product to embed its own AI assistant: Zoom has its own AI, Miro has its own AI, N8N has its own AI. This creates severe fragmentation: ten applications means ten separate AIs, each unaware of the others. Zoom AI summarizes a meeting, but getting Miro AI to create a board from those notes still requires manual copy-paste. Users pay repeatedly for essentially the same LLM capability in every application, and cross-application orchestration is virtually impossible.

**Core bottleneck: Context fragmentation, inability to collaborate across applications.**

### 2.3 MCP and Tool-Driven Architecture — Token Explosion

Many software vendors have begun offering official MCP toolsets, wrapping APIs as tools for AI to call. The MCP protocol itself supports dynamic tool discovery, and its design direction is correct. However, **the current mainstream MCP usage pattern** has serious problems: most MCP server implementations expose all interfaces at once, lacking task-specific tool organization strategies. When a general-purpose agent needs to connect to multiple domains simultaneously — codebases, document management, scheduling, etc. — just the tool names, parameter schemas, and usage descriptions consume massive tokens, with prohibitive initialization costs and latency.

More critically, a massive tool list fractures the model's attention. As the number of tools grows, LLMs become increasingly prone to hallucinations and declining accuracy in reasoning, context understanding, and tool selection. Anthropic proactively rejected this approach when designing Claude Code, keeping only the most essential interfaces and rigorously evaluating every new tool addition. This confirms a key insight: AI agents don't need unbounded API accumulation — they need **on-demand capability discovery mechanisms**.

**Core bottleneck: Tool bloat compresses reasoning space — the more tools connected, the worse the agent performs.**

### 2.4 Lessons from OpenClaw — Right Direction, Missing Infrastructure

OpenClaw is the open-source AI Agent that exploded in popularity in early 2026, surpassing Linux in GitHub stars, validating the enormous user demand for "one agent to rule everything." But it also exposed deep infrastructure flaws: lacking a supporting ecosystem, it essentially still operates tools designed for humans, with massive token consumption; it requires near-total system permissions — shell execution, file system access, email operations — a single prompt injection attack could leak secrets, delete data, or execute arbitrary code; the skill ecosystem is polluted by malicious content, as non-technical users rarely scrutinize skills before installing them, enabling malware distribution and credential theft.

The root cause is not the vision — it's that OpenClaw uses *human tools* to orchestrate everything. Without purpose-built AI-friendly tools, the only option is to grant the agent system-level permissions, making the blast radius uncontrollable.

**Core bottleneck: Missing security model, blurred permission boundaries.**

### 2.5 Problem Summary

| Dilemma | Core Bottleneck | AFS Response Section |
|---------|----------------|---------------------|
| Screen-level operation | Poor reliability, high cost | §3.3 Structured interfaces + §3.4 Compact responses |
| In-app embedded AI | Context fragmentation | §5 ④ Unified entry point |
| MCP tool explosion | Reasoning space compressed | §3.4 Progressive disclosure + §3.5 Startup loading |
| OpenClaw permission chaos | Missing security boundaries | §3.2 Why CLI + §4 Security model |

---

## 3. The AFS Specification (Agent-Friendly Standard)

**AFS** is an open specification that defines what software architecture enables AI agents to invoke tools most efficiently, safely, and accurately. It does not create new AI models — instead, it answers a fundamental question: **How does software become a good tool for AI?**

### 3.1 Three Layers of a Tool

Every AFS-compliant tool consists of three layers, sharing one set of core logic:

| Layer | Nature | Description |
|-------|--------|-------------|
| **API** | Core logic layer | Structured I/O, the single source of truth for all operations. CLI and GUI are both wrappers around it. |
| **CLI** | Agent operation channel + developer debugging interface | Standardized command-line wrapper over the API. AI operates through CLI; developers use it for debugging and scripting. |
| **GUI (optional)** | Human observation channel | For human oversight and inspection of AI's work output, following the dual-channel architecture (see §3.6). |

Agents learn what a tool can do through skill.md (see §3.3) and execute specific operations through CLI/API. The GUI is not the AI's entry point — it is the human's monitoring screen.

### 3.2 Why CLI Rather Than Direct API Calls?

A natural question: since the API itself uses structured JSON I/O, why does AFS wrap a CLI layer on top of the API instead of having agents call the API directly?

Three reasons:

**First, OS-level sandboxing and permission control.** CLI processes inherently run under the operating system's process model, directly leveraging mature security infrastructure — container isolation, file system permissions, user identity switching, seccomp sandboxes, and more. Direct API calls require the application layer to implement all security logic independently. Wrapping operations as CLI commands means each invocation is an independent process that can be constrained by OS-level security policies, with the blast radius limited to a single process.

**Second, auditability and composability.** CLI calls inherently produce human-readable execution logs (command + arguments + output) that both humans and automated systems can directly audit. CLI commands can be composed through pipes, supporting Unix-philosophy tool orchestration. This composability is exactly what AI agents need when orchestrating multiple tools.

**Third, semantic density.** A well-designed CLI command (e.g., `afs-crm contact list --status active --limit 10`) is itself a natural-language-level operation description. LLMs can generate, read, and reason about CLI commands, achieving semantic density far higher than equivalent JSON API request bodies. This directly reduces token consumption and lowers the probability of generating incorrect calls.

It is important to emphasize: CLI is a **wrapper layer** over the API, not a **replacement**. All business logic is still carried by the API layer; CLI is the standardized, secure operation channel between agents and the API.

### 3.3 Developer-Provided skill.md: Application Capability Description Document (for AFS-Standard Software)

skill.md is the **machine-readable capability description file** that every AFS tool must provide. It is the sole entry point for AI Agents to understand applications developed to the AFS standard — equivalent to the application's "user manual."

**Format requirements:** Markdown format with a fixed hierarchical structure. Tool developers are responsible for authoring and maintaining it.

**Hierarchical structure (progressive disclosure):**

| Level | Content | Loading Trigger | Purpose |
|-------|---------|----------------|---------|
| **L0 — Overview** | One-sentence description + core command list (names and summaries) | Automatically injected into context at tool installation | Agent determines whether the tool is relevant to the current task |
| **L1 — Capabilities** | Parameters, I/O formats, and basic use cases for each command | Loaded on demand when the agent decides to use the tool | Complete basic operations |
| **L2 — Examples** | Multi-step workflow examples, common composition patterns | Loaded on demand for complex tasks | Handle non-trivial scenarios |
| **L3 — Advanced** | Edge cases, advanced configuration, performance tuning | Rarely loaded | Expert-level usage |

**Design principle:** Each layer should be independently usable — reading L0 should be sufficient to decide whether to use the tool; reading L1 should be sufficient to complete basic operations. Layers should have no strong dependencies; an AI should not need to read L2 to understand L1.

The distinction from traditional API documentation (e.g., OpenAPI/Swagger): skill.md is optimized for LLMs, prioritizing semantic clarity over formal completeness, and emphasizing progressive loading to minimize token overhead.

### 3.4 Compact Responses — Return Only Necessary Data

CLI defaults to a compact output format containing only the most critical fields. Full details are available on demand via `--verbose` or `--fields` parameters. Every extra field consumes tokens and context space.

Example comparison:

```
# Default output (compact)
$ afs-crm contact list --status active
ID      Name          Status
C-0012  Alice Zhang   active
C-0034  Bob Li        active

# Detailed output (on demand)
$ afs-crm contact list --status active --verbose
ID      Name          Email              Phone         Created     Status
C-0012  Alice Zhang   alice@example.com  +86-138-xxxx  2026-01-15  active
C-0034  Bob Li        bob@example.com    +86-139-xxxx  2026-02-03  active
```

### 3.5 Startup Loading — Ready on Launch

When a user opens software developed with the AI/Agent-Friendly Standard (AFS), skill.md's L0 layer (one-sentence description + core command list) is automatically injected into the agent's context, skipping the tool discovery step. Detailed documentation is loaded on demand when the AI decides to use the tool.

This directly addresses the token explosion problem described in §2.3: agents incur only the L0 description cost of roughly a few dozen tokens per tool at startup, rather than loading all schemas at once.

### 3.6 Semantic Design — Commands as Documentation, Errors as Guidance

**Semantic CLI:** LLMs inherently possess semantic understanding capabilities. CLI commands and parameters should use complete verb phrases and natural-language values (e.g., `--status active` rather than `--s 1`), allowing AI to infer command meaning without consulting documentation, greatly reducing the documentation volume needed.

**Semantic error messages:** CLI error output should include the error type, cause, and suggested corrective action, enabling AI to self-correct without referring back to documentation.

Example:
```
$ afs-report generate --quarter March
Error [INVALID_VALUE]: --quarter expects Q1-Q4, got "March".
Suggestion: Did you mean --quarter Q1?
```

### 3.7 AFS Compliance Levels

AFS defines three compliance levels. Higher levels mean greater AI-friendliness. Each level has independent value, with strict progression between levels:

| AFS Level | Requirements | Problem Solved |
|-----------|-------------|----------------|
| **AFS-1 (Callable)** | API + skill.md (at minimum L0 and L1 layers) | AI can invoke the tool and understand how to use it — lowest barrier |
| **AFS-2 (Secure & Controllable)** | On top of AFS-1, core functionality and high-risk operations must be wrapped as CLI (e.g., read/write/edit/delete of critical system files), with risk levels annotated in skill.md; other low-frequency operations may remain as pure API access | Critical operations have architecture-level security boundaries while maintaining flexibility |
| **AFS-3 (Supervisable)** | On top of AFS-2, provide a GUI observation channel; state changes executed via CLI are synced to the GUI in real time | Humans can observe AI operations through GUI and intervene at any time |

**Regarding AFS-2 risk level classification:** AFS-2 requires tool developers to annotate the risk level for each operation in skill.md. The standard provides the following three-tier classification as a baseline guide:

| Risk Level | Definition | Examples |
|-----------|-----------|----------|
| **read** | Read-only operations that do not change system state | Query lists, get details, export reports |
| **write** | Create or modify data; reversible | Create records, update fields, rename files |
| **destructive** | Irreversible operations or those involving sensitive resources | Delete data, modify permissions, send emails, execute payments |

Tool developers may refine their own risk classifications beyond this baseline, but skill.md must include a risk level annotation for each command. Agents or orchestration platforms use these to determine which operations can be auto-executed and which require human confirmation.

---

## 4. Security Model

Security is one of the most critical design dimensions of AFS. The OpenClaw permission chaos revealed in §2.4 demonstrates that without architecture-level security constraints, the more powerful an AI agent becomes, the greater the risk (e.g., system prompt injection attacks). AFS's security strategy is built on four layers:

### 4.1 Process-Level Isolation

Each CLI command invocation is an independent process running in a restricted execution environment. Tool developers should support running CLI in containers, sandboxes, or under restricted user identities (developers specify which operations are high-risk in their skill.md, and agents learn from skill.md to run high-risk operations in sandboxes). Agents do not need system-level shell permissions — they can only interact with the system through predefined CLI commands, with each command's input space, output format, and side-effect scope fully defined.

Consider an analogy: suppose an LLM-based agent is tasked with cooking. There are two approaches: 1. Give it fire-making tools, knives, cookware, and ingredients — all operations require the robot itself to handle: using knives, lighting fire, etc. 2. Pre-build a cooking machine with a single "cook" button — just place ingredients in the machine and press the button; the cooking machine handles the rest. The agent has permission only to press the button.

The risk and reliability trade-off between these two approaches is self-evident. Most of the uncertainty and risk in Approach 1 is encapsulated within the cooking machine (i.e., the CLI) in Approach 2. If the agent suffers a prompt injection attack, the consequences under Approach 1 versus Approach 2 are drastically different.

Contrast with the OpenClaw model: OpenClaw grants agents a full shell, allowing arbitrary command execution (`rm -rf /`, `curl` to exfiltrate data, modifying SSH keys). Under the AFS model, agents can only invoke the CLI command set declared by the tool, shrinking the attack surface to the tool API's boundary.

### 4.2 Risk Annotation and Operation Confirmation

As described in §3.7, each operation is annotated with a risk level in skill.md. The orchestration layer (agent framework or AFS Shell) can enforce policies accordingly: read operations execute automatically, write operations are logged, destructive operations require human confirmation. This transforms the security decision from "do we trust the agent?" to "do we trust this specific operation?" — finer granularity, more controllable risk.

### 4.3 Input Validation and Prompt Injection Defense

A security advantage of semantic CLI is that the parameter space is predefined and finite. The `--quarter` parameter only accepts Q1-Q4; `--status` only accepts active/inactive — these constraints are enforced at the CLI level, independent of the LLM's judgment. In contrast, directly concatenating user input into an API request body is more susceptible to prompt injection attacks. The CLI's parameter validation mechanism provides an architecture-level defense against prompt injection.

### 4.4 Auditability

CLI calls inherently generate structured execution records: timestamps, commands, arguments, return codes, and output summaries. These logs can be audited by security teams, scanned by automated rules, and referenced by rollback mechanisms. In enterprise compliance scenarios, traceability of "what did the AI do?" is often a prerequisite for deployment.

---

## 5. Core Value

The following explains item by item how AFS systematically addresses the four dilemmas raised in §2:

### ① Token Savings — Lower Cost, Faster Response, Higher Reasoning Accuracy

**Addressing §2.1 high cost of screen-level operation + §2.3 MCP tool explosion.**

Three mechanisms work in concert to reduce token consumption: structured CLI replaces screenshots and UI metadata (addressing §2.1); compact response formats return only necessary data (§3.4); skill.md's progressive disclosure ensures agents load only the documentation layers needed for the current task (§3.3 + §3.5), rather than ingesting all tool schemas at once (addressing §2.3). Shorter context windows mean lower API call costs, faster response speeds, and — critically — higher LLM reasoning accuracy.

### ② Security Built In — Architecture-Level Constraints, Not Trust Assumptions

**Addressing §2.4 OpenClaw's permission chaos.**

AFS's security model (§4) shrinks the attack surface across four layers: process-level isolation limits the blast radius of each invocation; risk annotation pushes security decisions down to operation granularity; CLI parameter validation establishes an architecture-level defense against prompt injection; execution logs ensure auditability. The core idea of this mechanism: **don't trust that the agent won't make mistakes — let the architecture itself limit the consequences of mistakes**.

### ③ Reliability — Structured Interfaces Replace Fragile Screen Parsing

**Addressing §2.1 high failure rate of screen-level operation.**

CLI input and output are deterministic: command formats are fixed, parameter types are explicit, output structures are predictable. No more "button not found," "page timed out," or "output format unexpectedly changed" failure modes. In enterprise scenarios, operational reliability and predictability are often more decisive than cost.

### ④ Unified Entry Point, Shared Context, Cross-Tool Collaboration

**Addressing §2.2 fragmentation of in-app embedded AI.**

A single AI agent orchestrates all AFS tools with naturally shared context. Users no longer need to manually copy-paste between different applications' AI assistants, nor pay separately for AI features in each application. A meeting summary can directly become the input for creating tasks in a project management tool — because they share the same agent's context.

### ⑤ Agent-Agnostic, Maximum Portability

AFS tools are not bound to any specific agent framework. Whether OpenClaw, Claude Code, Open Code, or any new framework — as long as it can read skill.md and execute CLI commands, it can use AFS software. This protects tool developers' investment: adapt once, usable by all agents.

---

## 6. Relationship with MCP

AFS and MCP (Model Context Protocol) are not competitors — they are complements.

**MCP** defines the **communication protocol** between agents and tools — how messages are transmitted, how tools are registered, how results are returned. It solves the problem of "how do agents and tools communicate."

**AFS** defines the **design specification** of the tools themselves — how APIs are organized, how capabilities are described, how security boundaries are drawn. It solves the problem of "how do tools become good tools."

An analogy: MCP is like the HTTP protocol; AFS is like the RESTful API design convention. HTTP defines how requests are transmitted; REST defines how APIs should be designed. They solve problems at different layers, and best practice is to adopt both.

An AFS-compliant tool can absolutely be exposed to agents through the MCP protocol. In fact, AFS's skill.md progressive disclosure design can serve as an enhancement to MCP's tool discovery mechanism — returning only L0-layer descriptions in MCP's tool list, then loading the full L1/L2 documentation after the agent selects the tool.

---

## 7. Future Roadmap

AFS is still under continuous revision. Below is our planning for future development directions:

### 7.1 AFS Software Marketplace

Establish an open AFS tool marketplace. All listed tools must undergo open-source community security review and AFS compliance level verification, ensuring agents can invoke them safely. The marketplace provides one-click installation services, reducing onboarding costs for users and agents.

### 7.2 AFS Shell — Unified Operation Entry Point

The current operating system entry point is the graphical desktop: users launch applications by clicking icons and manage tasks by switching windows. AFS Shell CLI-ifies this paradigm — it is a command-line operating environment for AI agents, where all software management operations (install, launch, uninstall, configure) are unified through CLI. AI agents orchestrate multiple AFS tools within AFS Shell as naturally as developers orchestrate multiple command-line tools in a terminal.

Users can freely connect any LLM API as the driving agent, without being locked into any single model provider.

### 7.3 The Bigger Vision: Toward Agent OS

We are witnessing an irreversible industry shift: from the "software + embedded AI" bundling model to a new paradigm where **AI is the entry point and hub, and software becomes AI's plugins**.

AFS's ultimate vision is to become the foundation layer of an **Agent OS**:

| OS Analogy | AFS Counterpart |
|-----------|----------------|
| Kernel system call specification | AFS specification (how tools should be designed) |
| App store | AFS Software Marketplace (how tools are distributed) |
| Shell / Desktop environment | AFS Shell (how agents operate) |

Just as an operating system defines the rules for how applications interact with hardware, AFS defines the rules for how AI agents interact with software tools. As more software adopts AFS, an AI-centered software ecosystem will naturally emerge.

From a technical path perspective, realizing Agent OS requires completing three milestones in sequence: first, stabilization and adoption of the AFS specification itself (current stage); second, implementation of AFS Shell and establishment of the tool marketplace; finally, OS-level integration — making AFS Shell a native agent interaction layer of the operating system. Each stage is independently useful, with each subsequent stage building upon the previous one.

---

*© Norrai-lab contributors. Licensed under [CC BY 4.0](../LICENSE-CC-BY-4.0).*
