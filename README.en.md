<p align="center">
  <a href="./README.md">简体中文</a>
</p>

<h1 align="center">StoryForge</h1>

<p align="center">
  A Claude Code-based story generation Agent Runtime for computation under uncertainty
</p>

<p align="center">
  <img alt="Claude Code" src="https://img.shields.io/badge/Claude%20Code-Agent%20Runtime-111827?style=flat-square">
  <img alt="Story Generation" src="https://img.shields.io/badge/Story-Generation%20Workflow-b91c1c?style=flat-square">
  <img alt="Harness Engineering" src="https://img.shields.io/badge/Harness%20Engineering-Computation%20Under%20Uncertainty-0f766e?style=flat-square">
  <img alt="Synopsis Gate" src="https://img.shields.io/badge/Flow-Synopsis%20Gate%20First-7c3aed?style=flat-square">
  <img alt="Evals" src="https://img.shields.io/badge/Evals-6%2F6%20Passing-16a34a?style=flat-square">
  <img alt="Artifacts" src="https://img.shields.io/badge/Artifacts-Markdown%20Native-f59e0b?style=flat-square">
</p>

## Overview

StoryForge is an independent open-source Agent Runtime built on top of Claude Code. It uses short-drama creation as the validation scenario, but the real subject of the project is not “generate a script.” The goal is to turn open-ended, generative, non-unique, human-judged work into a runtime that is controllable, auditable, and capable of convergence-aware iteration.

This repository should be read as a standalone runtime project. The tracked structure, rules, evaluations, and examples define the public project. Ignored local notes, drafts, and private design materials are intentionally outside the public runtime.

## Quick Positioning

| Dimension | Description |
| --- | --- |
| Project type | Claude Code-based Agent Runtime |
| Core thesis | Harness Engineering for computation under uncertainty |
| Demonstration scenario | Synopsis, script, and storyboard workflows |
| Key mechanisms | Context assembly, hard gates, role isolation, review loops, convergence stops, audit trails |
| Execution model | Claude Code + Markdown-native repo structure |

## Why Harness Engineering for Computation Under Uncertainty

Many Harness Engineering discussions focus on deterministic computation: compiling, testing, tool orchestration, data transformation, and rule-based validation. Those tasks usually have stable I/O, clearer correctness, and near-binary pass/fail conditions.

StoryForge is aimed at a different class of work: computation under uncertainty.

- The output is not a single correct answer but a set of plausible candidates.
- Quality is judged through rubrics, reviewer agents, and human approval rather than one assertion.
- Revision does not guarantee monotonic improvement; it can stall, diverge, or oscillate.
- Human approval is not an afterthought but a first-class runtime control point.
- Audit trails are not optional metadata but part of safe handoff and replay.

So the harness here does not treat the model as a black-box function call. It treats generation, review, revision, stop conditions, and archiving as runtime primitives.

## System Diagrams

The three diagrams below are reused from the project's internal system design set. Together they show StoryForge from three complementary angles: end-to-end flow, runtime architecture, and the evaluation/revision loop.

### Diagram 1: Main Flow

This diagram shows the path from a raw idea to a final output such as a script or storyboard. The red gate marks the mandatory human approval point.

```mermaid
flowchart TD
    A([用户提出创意]) --> B["采访补全<br>interviewer.md"]
    B --> C["生成 story-pack.md"]
    C --> D["生成梗概<br>synopsis-generator.md"]
    D --> E{"梗概评估<br>narrative-reviewer.md<br>logic-auditor.md"}
    E -->|达标| F
    E -->|不达标| G[自动修正]
    G --> D
    F:::hardgate
    F{"用户确认梗概?<br>→ approved.md"}
    F -->|确认| H{选择输出格式}
    F -->|修改意见| D
    H -->|剧本| I["生成剧本<br>script-generator.md"]
    H -->|分镜| J["生成分镜<br>storyboard-generator.md"]
    I --> K{"剧本评估<br>narrative-reviewer.md<br>character-reviewer.md<br>logic-auditor.md<br>format-checker.md"}
    K -->|达标| L([剧本完成])
    K -->|不达标| M[自动修正]
    M --> I
    J --> N{"分镜评估<br>narrative-reviewer.md<br>logic-auditor.md<br>format-checker.md"}
    N -->|达标| O([分镜完成])
    N -->|不达标| P[自动修正]
    P --> J

    classDef hardgate stroke:#e74c3c,stroke-width:3px
    classDef user fill:#3498db,color:#fff
    classDef gen fill:#2ecc71,color:#fff
    classDef eval fill:#f39c12,color:#fff

    class A,F user
    class B,C,D,I,J gen
    class E,K,N eval
```

### Diagram 2: Runtime Architecture

This view emphasizes the runtime layers: Skills orchestrate, Agents execute, Knowledge supports, Rules inject constraints, and Hooks provide reminders after tool events.

```mermaid
flowchart TB
    subgraph Skills[".claude/skills/ — 技能层"]
        S1["new-project 新建项目<br>SKILL.md"]
        S2["evaluate 评估产物<br>SKILL.md"]
        S3["compare 对比分支<br>SKILL.md"]
    end

    subgraph GenAgents[".claude/agents/ — 生成 Agent"]
        GA1["interviewer.md<br>采访 Agent"]
        GA2["synopsis-generator.md<br>梗概生成"]
        GA3["script-generator.md<br>剧本生成"]
        GA4["storyboard-generator.md<br>分镜生成"]
    end

    subgraph EvalAgents[".claude/agents/ — 评估 Agent"]
        EA1["narrative-reviewer.md<br>叙事评审"]
        EA2["character-reviewer.md<br>角色评审"]
        EA3["logic-auditor.md<br>逻辑审计"]
        EA4["format-checker.md<br>格式检查"]
    end

    subgraph Knowledge["knowledge/ — 知识库"]
        K1["genres/<br>赛道知识 ×4"]
        K2["structure/<br>结构模型 ×3"]
        K3["evaluation/<br>评分标准 ×4"]
        K4["style-guide/<br>风格指南 ×3"]
        K5["templates/<br>输出模板 ×3"]
    end

    subgraph Rules[".claude/rules/ — 规则层"]
        R1["script-writing.md<br>剧本规则 → script/**"]
        R2["storyboard-writing.md<br>分镜规则 → storyboard/**"]
    end

    subgraph Hook[".claude/settings.json — 钩子层"]
        H1["PostToolUse: Write<br>提醒运行 /evaluate"]
    end

    Skills -->|调度| GenAgents
    Skills -->|调度| EvalAgents
    GenAgents -->|参考| Knowledge
    EvalAgents -->|依据| Knowledge
    Rules -.->|路径匹配时<br>自动注入| GenAgents
    Hook -.->|Write 事件后<br>触发提醒| GenAgents

    classDef skill fill:#9b59b6,color:#fff
    classDef gen fill:#2ecc71,color:#fff
    classDef eval fill:#f39c12,color:#fff
    classDef know fill:#3498db,color:#fff
    classDef rule fill:#e74c3c,color:#fff
    classDef hook fill:#95a5a6,color:#fff

    class S1,S2,S3 skill
    class GA1,GA2,GA3,GA4 gen
    class EA1,EA2,EA3,EA4 eval
    class K1,K2,K3,K4,K5 know
    class R1,R2 rule
    class H1 hook
```

### Diagram 3: Evaluation and Auto-Revision Loop

This view focuses on what happens after generation. The runtime scores artifacts, detects convergence or stagnation, and stops rather than revising forever.

```mermaid
flowchart TD
    A["生成产物<br>synopsis/v1.md 或 ep01.md"] --> B["评估 Agent 打分<br>narrative-reviewer.md<br>character-reviewer.md<br>logic-auditor.md<br>format-checker.md"]
    B --> C{"所有维度 ≥ 3/5?<br>依据: synopsis-rubric.md<br>或 script-rubric.md"}
    C -->|是| D(["通过<br>→ *-eval.md"])
    C -->|否| E{"检测分数趋势<br>CLAUDE.md 停机策略"}
    E -->|"收敛: S2 > S1"| F{"超过硬上限?<br>梗概 3轮 / 剧本 5轮"}
    E -->|"停滞: S2 ≈ S1"| G(["停止, 交给用户"])
    E -->|"发散: S2 < S1"| H(["立即停止, 交给用户"])
    E -->|"振荡: 有涨有跌"| I(["停止, 交给用户"])
    F -->|未超过| J["针对性修正<br>→ v2.md / v3.md"]
    F -->|超过上限| K(["强制停止, 交给用户"])
    J --> B

    classDef pass fill:#2ecc71,color:#fff
    classDef stop fill:#e74c3c,color:#fff
    classDef check fill:#f39c12,color:#fff

    class D pass
    class G,H,I,K stop
    class C,E,F check
```

## This Is Not a One-Click Generator

The project is not about writing a longer prompt. It is about making the runtime control structure explicit.

- `synopsis/approved.md` is a hard gate; no approved synopsis means no script or storyboard generation
- generator and evaluator agents operate under separated contexts
- synopsis and script loops have bounded revision budgets and convergence-aware stop conditions
- every major action is logged into `changelog.md`
- artifacts and evaluation reports stay inside the project directory for replay and takeover

## Why Claude Code Runtime Matters

StoryForge is not a conventional web app and not a standalone backend service. Its assumed runtime host is Claude Code.

In that model:

- [CLAUDE.md](./CLAUDE.md) defines global workflow constraints and hard gates
- [`.claude/skills/`](./.claude/skills) exposes structured entry points such as `/new-project`, `/evaluate`, and `/compare`
- [`.claude/agents/`](./.claude/agents) defines generation and review roles
- [`.claude/rules/`](./.claude/rules) injects domain constraints through path matching
- `projects/*` acts as transparent runtime state and artifact storage

Put simply, Claude Code is both the agent host and the operating interface for this runtime.

## Core Components

| Component | Role | Path |
| --- | --- | --- |
| Skills | High-level workflow entry points | [`.claude/skills/`](./.claude/skills) |
| Agents | Generation, review, and audit roles | [`.claude/agents/`](./.claude/agents) |
| Rules | Hard constraints injected by path | [`.claude/rules/`](./.claude/rules) |
| Knowledge | Genre, structure, template, and rubric references | [`knowledge/`](./knowledge) |
| Projects | Runtime state, intermediate artifacts, final outputs | [`projects/`](./projects) |
| Evals | Evaluation cases, baselines, and regression runs | [`evals/`](./evals) |

## Current Capabilities

- interview and normalize a creative brief into `story-pack.md`
- generate a synopsis before any downstream output
- branch into script or storyboard generation after approval
- run rubric-based review with independent evaluator agents
- revise with convergence-aware stopping logic
- preserve changelogs, eval reports, and project artifacts in Markdown

## Repository Status

Based on [`evals/baseline.md`](./evals/baseline.md):

- 8 agent definitions completed
- 3 skills integrated
- 18 knowledge files loaded into runtime context
- 6/6 evaluation cases passing

You can inspect example projects directly:

- [`projects/false-memory/`](./projects/false-memory)
- [`projects/eval-revenge-drama/`](./projects/eval-revenge-drama)

## Quick Start

1. Clone the repository and open it in Claude Code.
2. Use `/new-project` with a creative brief.
3. Review the generated `story-pack.md` and `synopsis/v1.md`.
4. Run `/evaluate` on artifacts and inspect the revision loop.
5. Approve the synopsis before continuing to script or storyboard generation.

## Who This Is For

- developers exploring Claude Code as a multi-agent runtime host
- teams building controllable workflows for content generation
- researchers interested in Harness Engineering for computation under uncertainty
- anyone who wants a Markdown-native, auditable, replayable Agent project example
