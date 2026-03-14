# 短剧创作 Agent 系统

## 全局规则
- 所有输出必须为 MD 格式，不使用 JSON
- 剧本必须先有已确认的梗概才能生成（硬卡点：synopsis/approved.md 不存在时拒绝生成）
- 评估 Agent 和生成 Agent 必须使用不同的上下文，禁止同一 agent 自评
- 每次生成或评估操作都必须记录到 changelog.md
- 生成与评估必须由不同的 Agent 角色执行，上下文隔离

## 可用 Skills
- `/new-project` — 初始化项目目录 + 启动采访流程
- `/evaluate <file>` — 对指定产物运行完整评估（自动选择对应的评估 Agent 组合）
- `/compare` — 对比当前项目的并行分支，生成 comparison.md 和推荐排名

## 知识索引
- 赛道知识：knowledge/genres/
- 故事结构：knowledge/structure/
- 评分标准：knowledge/evaluation/
- 输出模板：knowledge/templates/
- 风格指南：knowledge/style-guide/
- 跨项目经验：knowledge/learned-rules.md

## 工作流约束
1. 创意输入 → 采访补全 → story-pack.md → 梗概 → 确认 → 剧本/分镜，严格按序
2. 梗概评估不达标时自动修正，最多 3 轮；剧本评估最多 5 轮
3. 修正后分数未提升（停滞或发散）→ 立即停止，交给用户
4. 逐集生成时，只加载上一集全文 + 角色状态快照 + 伏笔列表，不加载更早的集
5. 并行分支：同一梗概可生成多个风格版本，通过 /compare 对比后由用户选择

## 停机策略（收敛判据）
自动修正循环中，检测收敛趋势而非盲目计数：
- 收敛（S2 > S1）：继续，最多再来 1-2 轮
- 停滞（S2 ≈ S1）：停止，交给用户
- 发散（S2 < S1）：立即停止，交给用户
- 振荡（某些维度升某些降）：停止，交给用户
- 硬上限：梗概 3 轮，剧本 5 轮

## 经验沉淀
- 项目级经验：projects/{project-id}/lessons.md
- 跨项目规则：knowledge/learned-rules.md
- 同类修改出现 2 次以上 → 提示沉淀为跨项目规则

## 项目目录约定
- 每个项目在 projects/{project-id}/ 下独立管理
- 并行分支在 branches/{branch-id}/ 下隔离
- 所有评估报告以 *-eval.md 命名，与被评估文件同目录

## 硬卡点
当用户要求生成剧本或分镜时，必须先检查对应项目的 synopsis/approved.md 是否存在。
如果不存在，拒绝生成并提示："梗概尚未确认，无法生成剧本。请先确认梗概。"
