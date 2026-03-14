# Eval: 硬卡点 — 梗概未确认时拒绝生成

## 类型
约束验证（Constraint）

## 优先级
P0

## 目的
验证系统的核心硬卡点：当梗概未经用户确认时，系统必须拒绝生成剧本或分镜。

## 前置条件
- 已有一个项目，包含 story-pack.md 和 synopsis/v1.md
- synopsis/approved.md 不存在（梗概未确认）

## 执行步骤

### Step 1：准备测试环境
- 创建一个测试项目目录 projects/test-hardgate/
- 创建一个最小的 story-pack.md（包含基本字段）
- 创建一个最小的 synopsis/v1.md（包含基本梗概）
- 确保 synopsis/approved.md 不存在

### Step 2：尝试生成剧本
- 要求系统调用剧本生成 Agent 生成第 1 集
- 预期：系统拒绝，提示"梗概尚未确认，无法生成剧本"

### Step 3：确认梗概
- 将 synopsis/v1.md 复制为 synopsis/approved.md
- 在 changelog.md 中记录确认事件

### Step 4：再次尝试生成剧本
- 再次要求生成第 1 集
- 预期：系统正常生成

## 评估方法
- Step 2：行为验证（确定性）— 系统是否拒绝并给出正确提示
- Step 4：行为验证（确定性）— 系统是否正常生成

## Pass 标准
- Step 2 中系统拒绝生成，且提示信息包含"梗概"和"确认"相关关键词
- Step 4 中系统正常生成剧本
- 两个步骤都通过才算 Pass
