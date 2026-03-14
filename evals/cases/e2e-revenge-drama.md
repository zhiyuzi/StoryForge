# Eval: 端到端复仇短剧链路

## 类型
端到端（E2E）

## 优先级
P0

## 输入
```
我想做一个都市复仇短剧。女主是个普通白领，被渣男未婚夫和闺蜜联手背叛，
还被公司陷害开除。但她其实是隐藏身份的豪门千金，被养父母从小寄养在普通家庭。
觉醒后一步步反击，最终让所有欺负过她的人付出代价。
10 集左右，每集 2 分钟，女频复仇爽剧。
```

## 预设采访回答
当采访 Agent 追问时，使用以下预设回答：
- 结局走向：彻底复仇成功，所有反派付出代价
- 是否需要感情线：是，男二是商业精英，在女主最低谷时帮助她，后期发展感情
- 付费墙位置：第 8 集结尾
- 风格偏好：快节奏，爽感优先，不要太多内心独白

## 执行步骤

### Step 1：项目初始化
- 调用 /new-project，输入上述创意文本
- 验证：projects/{id}/ 目录创建成功

### Step 2：采访补全
- 采访 Agent 追问缺失信息
- 使用预设回答回复
- 验证：story-pack.md 生成且包含以下字段：
  - 赛道类型 ✓
  - 目标受众 ✓
  - 集数与时长 ✓
  - 核心人设（至少 3 个角色）✓
  - 核心冲突 ✓
  - 结局走向 ✓

### Step 3：梗概生成
- 梗概生成 Agent 基于 story-pack.md 生成梗概
- 验证：synopsis/v1.md 存在且符合 synopsis-template.md 格式：
  - 有基本信息 ✓
  - 有一句话概括 ✓
  - 有核心冲突 ✓
  - 有分集大纲（每集有核心事件、冲突升级、爽点、钩子）✓
  - 有付费设计 ✓

### Step 4：梗概评估
- 调用 /evaluate synopsis/v1.md
- 验证：synopsis/v1-eval.md 生成且包含：
  - chain-of-thought 分析 ✓
  - 各维度分数 ✓
  - 修改建议 ✓
  - 综合判定 ✓

### Step 5：梗概确认
- 确认梗概（生成 synopsis/approved.md）
- 验证：synopsis/approved.md 存在

### Step 6：剧本生成
- 调用剧本生成 Agent 生成第 1 集
- 验证：branches/main/script/ep01.md 存在且符合 script-template.md 格式：
  - 有集信息 ✓
  - 有场景划分 ✓
  - 有对白和动作 ✓
  - 有本集钩子 ✓
  - 有本集摘要 ✓

### Step 7：剧本评估
- 调用 /evaluate branches/main/script/ep01.md
- 验证：ep01-eval.md 生成且格式正确

### Step 8：留痕检查
- 验证：changelog.md 记录了所有关键操作

## 评估方法
- Step 1-2：结构验证（确定性，检查文件是否存在、字段是否完整）
- Step 3：结构验证 + LLM-as-Judge（梗概质量）
- Step 4：格式验证（评估报告是否包含 chain-of-thought + 分数 + 建议）
- Step 5：规则检查（approved.md 是否生成）
- Step 6-7：LLM-as-Judge + 结构验证
- Step 8：结构验证

## Pass 标准
- 所有结构验证项通过
- 梗概评估所有维度 ≥ 3/5
- 剧本评估所有维度 ≥ 3/5
- changelog.md 记录了所有关键操作
