<p align="center">
  <a href="./README.md">简体中文</a>
</p>

<h1 align="center">StoryForge</h1>

<p align="center">
  A Claude Code-based Agent Runtime for Harness Engineering under uncertainty
</p>

<p align="center">
  <img alt="Claude Code" src="https://img.shields.io/badge/Claude%20Code-Agent%20Runtime-111827?style=flat-square">
  <img alt="Harness Engineering" src="https://img.shields.io/badge/Harness%20Engineering-Uncertainty%20Computation-0f766e?style=flat-square">
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
| Core thesis | Harness Engineering for uncertainty computation |
| Demonstration scenario | Synopsis, script, and storyboard workflows |
| Key mechanisms | Context assembly, hard gates, role isolation, review loops, convergence stops, audit trails |
| Execution model | Claude Code + Markdown-native repo structure |

## Why Harness Engineering for Uncertainty Computation

Many Harness Engineering discussions focus on deterministic computation: compiling, testing, tool orchestration, data transformation, and rule-based validation. Those tasks usually have stable I/O, clearer correctness, and near-binary pass/fail conditions.

StoryForge is aimed at a different class of work: uncertainty computation.

- The output is not a single correct answer but a set of plausible candidates.
- Quality is judged through rubrics, reviewer agents, and human approval rather than one assertion.
- Revision does not guarantee monotonic improvement; it can stall, diverge, or oscillate.
- Human approval is not an afterthought but a first-class runtime control point.
- Audit trails are not optional metadata but part of safe handoff and replay.

So the harness here does not treat the model as a black-box function call. It treats generation, review, revision, stop conditions, and archiving as runtime primitives.

## Harness Logic

```mermaid
flowchart TD
    A["Goal Layer<br/>Turn open-ended creative intent<br/>into auditable outputs"] --> B

    subgraph Context["Context Layer"]
        B["creative brief"]
        C["story-pack"]
        D["approved synopsis"]
    end

    subgraph Orchestration["Orchestration Layer"]
        E["/new-project"]
        F["synopsis-generator"]
        G{"output branch"}
        H["script-generator"]
        I["storyboard-generator"]
    end

    subgraph Review["Review Layer"]
        J["narrative-reviewer"]
        K["logic-auditor"]
        L["character-reviewer"]
        M["format-checker"]
    end

    subgraph Constraint["Constraint Layer"]
        N["hard gate:<br/>no approved synopsis,<br/>no downstream generation"]
        O["generator and evaluator<br/>must stay isolated"]
        P["convergence-aware stop policy"]
    end

    subgraph Audit["Audit Layer"]
        Q["changelog.md"]
        R["*-eval.md"]
        S["projects/* artifacts"]
    end

    B --> C
    C --> E
    E --> F
    F --> J
    F --> K
    J -->|revise| F
    K -->|revise| F
    J --> D
    K --> D
    D --> G
    G --> H
    G --> I
    H --> J
    H --> K
    H --> L
    H --> M
    I --> J
    I --> K
    I --> M
    J -->|revise| H
    K -->|revise| H
    L -->|revise| H
    M -->|revise| H
    J -->|revise| I
    K -->|revise| I
    M -->|revise| I
    N -.guards.-> H
    N -.guards.-> I
    O -.isolates.-> J
    O -.isolates.-> K
    O -.isolates.-> L
    O -.isolates.-> M
    P -.stops.-> J
    P -.stops.-> K
    P -.stops.-> L
    P -.stops.-> M
    F --> Q
    H --> Q
    I --> Q
    J --> R
    K --> R
    L --> R
    M --> R
    Q --> S
    R --> S
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
2. Let Claude Code load the project rules from [CLAUDE.md](./CLAUDE.md).
3. Use `/new-project` with a creative brief.
4. Review the generated `story-pack.md` and `synopsis/v1.md`.
5. Run `/evaluate` on artifacts and inspect the revision loop.
6. Approve the synopsis before continuing to script or storyboard generation.

## Who This Is For

- developers exploring Claude Code as a multi-agent runtime host
- teams building controllable workflows for content generation
- researchers interested in Harness Engineering for uncertainty computation
- anyone who wants a Markdown-native, auditable, replayable Agent project example
