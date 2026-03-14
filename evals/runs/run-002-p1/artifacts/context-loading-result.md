# Eval Result: eval-context-loading

## 执行时间：2026-03-14

---

## Step 1：验证 Agent 定义（script-generator.md）

**文件：** `.claude/agents/script-generator.md`

### 始终加载（应存在）
| 上下文项 | 期望 | Agent 定义中 | 结果 |
|---|---|---|---|
| story-pack.md | 始终加载 | ✓ "story-pack.md — 世界观、角色卡、风格约束" | PASS |
| synopsis/approved.md | 始终加载 | ✓ "synopsis/approved.md — 已确认的梗概" | PASS |
| episode-plan.md | 始终加载 | ✓ "episode-plan.md — 全集大纲计划（如果存在）" | PASS |

额外始终加载项（Agent 定义中有，eval case 未列出但属于合理扩展）：
- knowledge/templates/script-template.md — 剧本输出模板
- knowledge/evaluation/script-rubric.md — 评估标准
- knowledge/learned-rules.md — 跨项目经验

### 动态加载（第 N 集）
| 上下文项 | 期望 | Agent 定义中 | 结果 |
|---|---|---|---|
| ep{N-1}.md（上一集全文） | 动态加载 | ✓ "上一集全文 ep{N-1}.md — 保持衔接" | PASS |
| ep{N-1}-eval.md（上一集评估报告） | 动态加载 | ✓ "上一集评估报告 ep{N-1}-eval.md — 避免重复同样的问题（如果存在）" | PASS |
| character-state/ep{N-1}.md | 动态加载 | ✓ "角色状态快照 character-state/ep{N-1}.md — 截至上一集的角色状态（如果存在）" | PASS |
| unresolved-threads.md | 动态加载 | ✓ "未解决伏笔列表 unresolved-threads.md — 滚动更新（如果存在）" | PASS |

### 不加载
| 上下文项 | 期望 | Agent 定义中 | 结果 |
|---|---|---|---|
| ep{1} ~ ep{N-2} 全文 | 不加载 | ✓ "ep{1} ~ ep{N-2} 的全文（太长，挤占上下文窗口）" | PASS |

### 硬卡点检查
Agent 定义中包含硬卡点："生成前必须确认 synopsis/approved.md 存在。如果不存在，拒绝生成并提示用户先确认梗概。" — PASS

---

## Step 2：验证 Rules 自动注入（script-writing.md）

**文件：** `.claude/rules/script-writing.md`

### Globs 匹配
| 检查项 | 期望 | 实际 | 结果 |
|---|---|---|---|
| globs 定义 | 匹配剧本路径 | `projects/*/branches/*/script/**` | PASS |
| 路径覆盖范围 | 所有项目的所有分支的剧本目录 | ✓ 通配符正确覆盖 | PASS |

### 知识文件引用
| 引用文件 | 期望 | Rules 中 | 文件存在 | 结果 |
|---|---|---|---|---|
| knowledge/style-guide/dialogue.md | 引用 | ✓ "对白写作规范" | YES | PASS |
| knowledge/structure/short-drama-beats.md | 引用 | ✓ "短剧节奏模型" | YES | PASS |
| knowledge/templates/script-template.md | 引用 | ✓ "剧本输出模板" | YES | PASS |

### 硬性规则完整性
Rules 文件包含以下硬性规则：
- 每集时长 90-120 秒 ✓
- 每句对白不超过 15 字 ✓
- 每集结尾必须有钩子 ✓
- 前 10 秒必须有冲突或悬念 ✓
- 推进型对白占比 ≥ 70% ✓
- 每集至少 1 个爽点 ✓
- 对白格式规范 ✓
- 动作描写规范 ✓

---

## Step 3：验证角色状态更新机制

**文件：** `.claude/agents/script-generator.md` — "生成后自动更新"章节

| 更新项 | 期望 | Agent 定义中 | 结果 |
|---|---|---|---|
| character-state/ep{N}.md | 每集生成后更新角色状态 | ✓ "每个角色截至本集的状态" | PASS |
| unresolved-threads.md | 更新伏笔列表 | ✓ "新增本集伏笔、标记已解决的" | PASS |
| episode-plan.md | 如需调整后续计划 | ✓ "如果需要调整后续计划" | PASS |

### 修正模式
Agent 定义中还包含修正模式规则：
- 只关注标记为"必须修改"的维度 ✓
- 针对性修改，不大幅重写 ✓
- 保持与前后集的衔接一致性 ✓

---

## 综合结果：PASS（15/15 步骤通过）
