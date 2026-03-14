# Eval: Skill 调用验证 — /new-project 和 /evaluate 是否正常工作

## 类型
功能验证（Functional）

## 优先级
P1

## 目的
验证两个核心 Skill 的定义完整性和工作流正确性。

## 执行步骤

### Step 1：验证 /new-project Skill
读取 .claude/skills/new-project/SKILL.md，检查：
1. frontmatter 包含 name、description、allowed-tools
2. 步骤定义完整：
   - 创建项目目录结构
   - 保存 brief.md
   - 初始化 changelog.md
   - 调用采访 Agent
   - 生成 story-pack.md
   - 调用梗概生成 Agent
   - 提示用户

### Step 2：验证 /evaluate Skill
读取 .claude/skills/evaluate/SKILL.md，检查：
1. frontmatter 包含 name、description、allowed-tools
2. 步骤定义完整：
   - 识别产物类型（梗概/剧本/分镜）
   - 选择对应评估 Agent 组合
   - 执行评估
   - 保存评估报告
   - 判定与后续（达标/自动修正/停机）
   - 记录到 changelog

### Step 3：验证 Agent 引用
检查 Skill 中引用的 Agent 文件是否都存在：
- .claude/agents/interviewer.md
- .claude/agents/synopsis-generator.md
- .claude/agents/narrative-reviewer.md
- .claude/agents/format-checker.md
- .claude/agents/character-reviewer.md
- .claude/agents/logic-auditor.md

### Step 4：验证 Hooks 配置
读取 .claude/settings.json，检查：
- PostToolUse Write hook 是否配置
- hook 命令是否正确

## 评估方法
- 配置验证（确定性）：检查文件存在性和内容完整性

## Pass 标准
- 两个 Skill 定义完整，步骤无遗漏
- 所有引用的 Agent 文件存在
- Hooks 配置正确
