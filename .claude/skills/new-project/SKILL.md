---
name: new-project
description: 初始化一个新的短剧创作项目并启动采访流程
---
# 新建项目

根据用户提供的创意输入，执行以下步骤：

## 步骤

### 1. 创建项目目录
- 根据创意内容生成一个简短的项目 ID（英文，kebab-case，如 `revenge-urban-drama`）
- 在 `projects/{project-id}/` 下创建以下目录结构：
  ```
  projects/{project-id}/
  ├── synopsis/
  ├── branches/
  │   └── main/
  │       ├── script/
  │       ├── storyboard/
  │       ├── eval/
  │       └── character-state/
  └── changelog.md
  ```

### 2. 保存原始输入
- 将用户的创意文本保存为 `projects/{project-id}/brief.md`

### 3. 初始化 changelog
- 在 `projects/{project-id}/changelog.md` 中记录项目创建事件：
  ```markdown
  # 变更记录

  ## {timestamp} — 项目创建
  - 操作：初始化项目
  - 创意输入：{brief 摘要}
  ```

### 4. 启动采访
- 调用采访 Agent（.claude/agents/interviewer.md）
- 将用户的创意文本作为初始输入传给采访 Agent
- 采访 Agent 会追问缺失信息（最多 3 轮）
- 采访结束后，采访 Agent 生成 `projects/{project-id}/story-pack.md`

### 5. 生成梗概
- 采访完成后，调用梗概生成 Agent（.claude/agents/synopsis-generator.md）
- 生成 `projects/{project-id}/synopsis/v1.md`
- 在 changelog.md 中记录梗概生成事件

### 6. 提示用户
- 告知用户：梗概已生成，请查看 synopsis/v1.md
- 建议运行 `/evaluate synopsis/v1.md` 进行评估
- 提醒：梗概需要用户确认后才能生成剧本
