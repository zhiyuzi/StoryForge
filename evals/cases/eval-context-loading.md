# Eval: 上下文加载验证 — 逐集生成时的上下文包是否正确

## 类型
上下文工程验证（Context Engineering）

## 优先级
P1

## 目的
验证剧本生成 Agent 在逐集生成时，正确加载了规定的上下文包，且没有加载不该加载的内容。

## 执行步骤

### Step 1：验证 Agent 定义
读取 .claude/agents/script-generator.md，检查上下文加载规则：

**始终加载（应存在）：**
- story-pack.md
- synopsis/approved.md
- episode-plan.md

**动态加载（第 N 集）：**
- ep{N-1}.md（上一集全文）
- ep{N-1}-eval.md（上一集评估报告）
- character-state/ep{N-1}.md（角色状态快照）
- unresolved-threads.md（伏笔列表）

**不加载：**
- ep{1} ~ ep{N-2} 的全文

### Step 2：验证 Rules 自动注入
检查 .claude/rules/script-writing.md：
- globs 是否正确匹配 projects/*/branches/*/script/**
- 是否引用了 dialogue.md、short-drama-beats.md、script-template.md

### Step 3：验证角色状态更新机制
检查 script-generator.md 中是否定义了生成后自动更新：
- character-state/ep{N}.md
- unresolved-threads.md
- episode-plan.md

## 评估方法
- 配置验证（确定性）：检查 agent 定义和 rules 文件中的规则是否完整

## Pass 标准
- Agent 定义中明确列出了所有应加载和不应加载的上下文
- Rules 文件的 globs 正确匹配剧本路径
- 生成后更新机制完整定义
