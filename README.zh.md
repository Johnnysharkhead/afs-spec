[English](./README.md) | [中文](./README.zh.md)

# AFS — Agent-Friendly Standard

> 不是让 AI 学会用人类的软件，而是让软件成为 AI 的好工具。

**AFS（Agent-Friendly Standard）** 是一份开放规范，定义软件应当如何从设计层面对 AI Agent 友好——让 Agent 能够安全、高效、可靠地发现、理解和调用软件能力。

---

## 为什么需要 AFS？

当前的 AI Agent 被迫通过截屏、模拟点击和脆弱的浏览器自动化来与人类软件交互。每个 SaaS 产品都内置了自己的 AI 助手，导致上下文割裂、无法跨应用协作。MCP 等工具驱动架构面临工具膨胀、压缩推理空间的问题。而 OpenClaw 等 Agent 框架缺少专用工具，被迫授予系统级权限，爆炸半径不可控。

AFS 颠覆了这一模式：不是让 AI 去适应人类软件，而是定义软件应当如何从底层设计，使其成为 AI 智能体的好工具。

### AFS 解决的四种困境

- **屏幕级操作既贵又不可靠** — AI 浪费大量 Token 处理截屏；页面超时、布局变化导致操作频繁失败。
- **应用内嵌 AI 各自为政** — 十个应用十个 AI，无法共享上下文、无法跨应用协作。
- **MCP 工具爆炸** — 一次性暴露所有接口，上下文膨胀、推理准确率下降、延迟可观。
- **Agent 框架缺少安全边界** — OpenClaw 等平台被迫授予系统级权限，爆炸半径不可控。

### AFS 提供什么

- **工具三层构成** — API（核心逻辑）+ CLI（智能体操作通道）+ GUI（可选人类观察通道），共享一套核心逻辑。
- **skill.md** — 标准化、机器可读的能力说明书，支持渐进式披露（L0–L3，按需加载）。
- **三级合规分级** — AFS-1（可调用）、AFS-2（安全可控）、AFS-3（可监督），可逐级渐进采用。
- **架构级安全** — 进程级隔离、风险标注、CLI 参数验证、可审计性。
- **与 MCP 互补** — AFS 定义工具如何设计，MCP 定义智能体与工具如何通信，最佳实践是同时采用。
- **Agent 无关** — 适用于 OpenClaw、Claude Code 或任何能读取 skill.md 并执行 CLI 命令的 AI Agent。

---

## 仓库结构

```
afs-spec/
├── README.md                          ← 英文 README
├── README.zh.md                       ← 中文 README（你在这里）
├── LICENSE                            ← 双协议说明
├── LICENSE-CC-BY-4.0                  ← CC BY 4.0（文档）
├── LICENSE-MIT                        ← MIT（代码）
├── whitepaper/
│   ├── afs-whitepaper-eng.md          ← 白皮书 v0.2（英文）
│   └── afs-whitepaper-zh.md           ← 白皮书 v0.2（中文）
├── spec/
│   └── overview.md                    ← 技术规范
└── examples/
    └── afs-email/
        └── skill.md                   ← 示例：AFS Email skill
```

| 目录 | 用途 |
|------|------|
| `whitepaper/` | 阐述 AFS 的动机、设计哲学、技术架构和安全模型的叙述性文档。 |
| `spec/` | 规范性技术文档。 |
| `examples/` | 具体的、带注释的 AFS skill 定义示例。 |

---

## 快速开始

1. **阅读白皮书** — [`whitepaper/afs-whitepaper-zh.md`](whitepaper/afs-whitepaper-zh.md) 了解完整愿景和动机（[English](whitepaper/afs-whitepaper-eng.md)）。
2. **阅读规范** — [`spec/overview.md`](spec/overview.md) 了解技术规范。
3. **查看示例** — [`examples/afs-email/skill.md`](examples/afs-email/skill.md) 查看一个完整的 skill 定义示例。

---

## 参与贡献

AFS 是一个开放项目，欢迎各种形式的贡献：

- **反馈** — 提交 Issue 来建议修改或指出模糊之处。
- **提案** — 针对 `spec/` 或 `whitepaper/` 提交 Pull Request。
- **示例** — 在 `examples/` 下新建目录，为你的场景编写 `skill.md`。

---

## 路线图

| 里程碑 | 状态 |
|--------|------|
| v0.1 — 白皮书 + 仓库脚手架 | ✅ 完成 |
| v0.2 — 白皮书 v0.2（工具三层构成、安全模型、合规分级） | ✅ 完成 |
| v0.3 — 规范性文档对齐 + skill 定义的 JSON Schema | 🟡 进行中 |
| AFS 软件市场 — 开放工具市场，含安全审查与合规验证 | ⬜ 计划中 |
| AFS Shell — 面向 AI 智能体的统一 CLI 操作环境 | ⬜ 计划中 |
| v1.0 — 稳定规范、参考验证器和官方示例 | ⬜ 计划中 |

---

*本项目以文档为先，当前阶段不包含构建工具或代码。*

*© Norrai-lab 贡献者。文档采用 [CC BY 4.0](LICENSE-CC-BY-4.0) 协议，代码采用 [MIT](LICENSE-MIT) 协议。*
