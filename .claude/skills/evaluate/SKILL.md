---
name: evaluate
description: 对指定产物运行完整评估，自动选择对应的评估 Agent 组合
---
# 评估产物

对指定的产物文件运行评估，自动选择对应的评估 Agent。

## 参数
- 用户传入的参数是待评估文件的路径（相对于项目目录）

## 步骤

### 1. 识别产物类型
根据文件路径判断产物类型：
- `synopsis/*.md`（非 eval 文件）→ 梗概评估
- `branches/*/script/ep*.md`（非 eval 文件）→ 剧本评估
- `branches/*/storyboard/ep*.md`（非 eval 文件）→ 分镜评估

### 2. 选择评估 Agent
根据产物类型选择评估 Agent 组合：

**梗概评估：**
- 叙事评审 Agent（.claude/agents/narrative-reviewer.md）— 叙事效率、冲突处理、爽点密度、钩子强度
- 逻辑审计 Agent（.claude/agents/logic-auditor.md）— 逻辑一致性、付费点优化、赛道匹配度

**剧本评估（全部 4 个评估 Agent）：**
- 规范检查 Agent（.claude/agents/format-checker.md）— 格式规范（确定性检查）
- 叙事评审 Agent（.claude/agents/narrative-reviewer.md）— 叙事效率、冲突处理、爽点密度、钩子强度
- 角色评审 Agent（.claude/agents/character-reviewer.md）— 角色一致性、情感深度
- 逻辑审计 Agent（.claude/agents/logic-auditor.md）— 逻辑一致性、付费点优化、赛道匹配度

**分镜评估：**
- 规范检查 Agent — 格式规范
- 叙事评审 Agent — 视觉叙事效率、钩子强度
- 逻辑审计 Agent — 逻辑一致性、赛道匹配度

### 3. 执行评估
- 调用选定的评估 Agent
- 评估 Agent 读取被评估文件 + 相关上下文（story-pack.md、approved-synopsis.md 等）
- 评估 Agent 按 G-Eval 流程输出评估报告

### 4. 保存评估报告
- 评估报告保存在被评估文件同目录，命名为 `{原文件名}-eval.md`
- 例如：`synopsis/v1.md` → `synopsis/v1-eval.md`

### 5. 判定与后续
- 如果所有维度达标（≥ 3/5）→ 通过，提示用户确认
- 如果有维度 ≤ 2/5 → 触发自动修正：
  1. 将评估报告传给对应的生成 Agent
  2. 生成 Agent 针对性修正，输出下一版本
  3. 重新评估修正版本
  4. 对比前后分数：
     - 收敛（分数提升）→ 继续，最多再来 1-2 轮
     - 停滞（分数不变）→ 停止，交给用户
     - 发散（分数下降）→ 立即停止，交给用户
  5. 硬上限：梗概 3 轮，剧本 5 轮

### 6. 记录到 changelog
- 在项目的 changelog.md 中记录评估事件：
  ```markdown
  ## {timestamp} — 评估 {文件名}
  - 评估 Agent：{agent 名称}
  - 维度评分：{各维度分数}
  - 判定：{通过/需修改}
  - 修正轮次：{如有修正}
  ```
