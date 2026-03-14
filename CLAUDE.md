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

## 项目目录约定
- 每个项目在 projects/{project-id}/ 下独立管理
- 并行分支在 branches/{branch-id}/ 下隔离
- 所有评估报告以 *-eval.md 命名，与被评估文件同目录

## 硬卡点
当用户要求生成剧本或分镜时，必须先检查对应项目的 synopsis/approved.md 是否存在。
如果不存在，拒绝生成并提示："梗概尚未确认，无法生成剧本。请先确认梗概。"
