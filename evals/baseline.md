# Eval Baseline

## 记录时间：2026-03-14
## 运行 ID：run-001-p0

| Eval Case | Pass | 关键分数 | 备注 |
|---|---|---|---|
| e2e-revenge-drama | ✓ | 梗概：叙事 4/5, 冲突 4/5, 爽点 4/5, 钩子 5/5；剧本：叙事 4/5, 冲突 5/5, 爽点 4/5, 钩子 5/5 | 首次通过，所有维度远超达标线 |
| hardgate-synopsis-block | ✓ | N/A（确定性检查） | CLAUDE.md + agent 定义双重约束验证通过 |

## 总体 Pass Rate：2/2 (100%)

## 系统配置验证
- CLAUDE.md 全局规则：✓
- Agent 定义格式（frontmatter + body）：✓
- Skill 定义完整：✓
- 知识库存在且合理：✓
- Hooks 配置：✓
