# Eval Baseline

## 记录时间：2026-03-14
## 最终运行 ID：run-003-full

| Eval Case | 优先级 | Pass | 关键分数 | 首次通过 |
|---|---|---|---|---|
| e2e-revenge-drama | P0 | ✓ | 梗概：叙事 4/5, 冲突 4/5, 爽点 4/5, 钩子 5/5；剧本：叙事 4/5, 冲突 5/5, 爽点 4/5, 钩子 5/5 | run-001-p0 |
| hardgate-synopsis-block | P0 | ✓ | N/A（确定性检查） | run-001-p0 |
| eval-skill-invocation | P1 | ✓ | 22/22 步骤通过 | run-002-p1 |
| eval-context-loading | P1 | ✓ | 15/15 步骤通过 | run-002-p1 |
| eval-low-quality-detection | P1 | ✓ | 冲突 1/5, 爽点 1/5, 钩子 1/5, 付费 1/5（正确识别） | run-002-p1 |
| eval-convergence | P2 | ✓ | 9/9 步骤通过 | run-003-full |

## 总体 Pass Rate：6/6 (100%)

## 系统完整性
- Agent 定义：8/8 完整
- Skill 定义：3/3 完整（/new-project, /evaluate, /compare）
- Knowledge 文件：18 个
- Rules 文件：2/2 完整
- Hooks 配置：1/1 完整
