# Memory Governance Skill

**记忆治理模块**：反幻觉 + 证据积累 + Lint，可独立接入任何记忆系统的质量守门员。

---

## 一、设计动机 [v2新增]

### v1 的质量控制短板

agent-brain v1 的质量控制能力（反幻觉四规则、证据积累、矛盾处理）是嵌入在 cognitive-memory 模块内部的，存在三个问题：

| 问题 | 表现 | 后果 |
|------|------|------|
| **不可复用** | 其他记忆系统（AgentMemory、Mem0）无法使用反幻觉校验 | 每个系统各自实现，标准不统一 |
| **无治理 API** | 质量控制散落在各处，没有统一的调用接口 | 难以批量检查、难以自动化 |
| **无仪表盘** | 不知道记忆系统的整体健康状况 | 矛盾累积、孤儿条目、悬空引用悄然增长 |

### v2 的治理方案

把 v1 的质量控制能力抽取为独立的治理层，提供标准 API，任何记忆系统都可接入：

```
┌─────────────────────────────────────────────────────────────┐
│                    记忆治理层                                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  治理 API (任何系统都可调用)                                │
│  ├── validate()    → 反幻觉校验                            │
│  ├── promote()     → 提升层级                              │
│  ├── demote()      → 降级                                  │
│  ├── archive()     → 归档                                  │
│  └── lint()        → 健康检查                              │
│                                                             │
│  治理策略 (可配置)                                          │
│  ├── 证据阈值                                              │
│  ├── 确认要求                                              │
│  ├── 矛盾策略                                              │
│  └── 衰减规则                                              │
│                                                             │
│  治理仪表盘 (可视化)                                        │
│  ├── 记忆总量/分布                                          │
│  ├── 待验证/矛盾/孤儿                                       │
│  └── 新增/归档/降级趋势                                     │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## 二、治理 API [v2新增]

### 2.1 validate(entry) —— 反幻觉校验

**输入**：一条记忆条目（包含内容、层级、来源）

**输出**：校验结果

```json
{
  "result": "pass|fail|needs_review",
  "checks": [
    {
      "rule": "小样本校验",
      "status": "pass",
      "detail": "3次独立观察，满足阈值"
    },
    {
      "rule": "情境依赖校验",
      "status": "pass",
      "detail": "已标注情境：非紧急决策"
    },
    {
      "rule": "言行一致校验",
      "status": "needs_review",
      "detail": "口头表达与历史行为不完全一致，需进一步验证"
    },
    {
      "rule": "可逆表述校验",
      "status": "pass",
      "detail": "使用'倾向于'表述，未使用绝对化词汇"
    }
  ],
  "confidence": "M",
  "suggestions": ["建议标注言行不一致的具体场景"]
}
```

**校验逻辑详解**：

```markdown
## 四规则校验流程

### 规则1: 小样本校验
- 检查: 证据数量是否 ≥ 阈值
- L3: ≥ 3 次独立观察
- L4: ≥ 3 次独立观察 + 无反例
- L5: ≥ 3 次独立观察 + 无反例 + 跨情境一致
- 通过: 证据充足
- 不通过: 标记"待验证"，降低置信度

### 规则2: 情境依赖校验
- 检查: 是否有情境限定词
- 通过: 有明确情境（"在X情境下..."）
- 不通过: 添加情境限定，或标记"需补充情境"

### 规则3: 言行一致校验
- 检查: 声明与行为记录是否一致
- 通过: 行为记录支持声明
- 不通过: 降级表述（"有意愿但执行待观察"）
- 需人工: 数据不足，无法判断

### 规则4: 可逆表述校验
- 检查: 是否使用绝对化词汇
- 通过: 使用可逆表述（"倾向于""目前看来"）
- 不通过: 替换为可逆表述
```

**平台适配**：

| 平台 | 实现方式 | 备注 |
|------|----------|------|
| **扣子** | 在对话中执行校验 Prompt | 无需额外工具 |
| **Claude Code** | 脚本 + LLM 调用 | 可自动化批量校验 |
| **Cursor** | 内联校验 | 边写边校验 |
| **通用** | Prompt 模板 | 任何支持 LLM 的平台 |

---

### 2.2 promote(entry, level) —— 提升记忆层级

**输入**：条目 ID + 目标层级

**输出**：提升结果

```json
{
  "entry_id": "L3-B-001",
  "from_level": "L2",
  "to_level": "L3",
  "result": "success|denied|needs_confirmation",
  "checks": {
    "evidence_sufficient": true,
    "no_contradiction": true,
    "permission": "auto"
  },
  "actions": [
    "写入 behavior.md",
    "更新 index.md 索引",
    "追加操作日志"
  ]
}
```

**提升规则**：

```markdown
## 层级提升规则

### L2 → L3 (行为模式)
- 条件: ≥3 次独立观察 + 反幻觉校验通过
- 权限: 自动（可配置为需确认）
- 执行:
  1. validate(entry) → 确认通过
  2. 写入 behavior.md
  3. 更新 index.md
  4. 追加 log.md

### L3 → L4 (价值观认知)
- 条件: ≥3 次独立观察 + 无反例 + 用户确认
- 权限: 必须用户确认
- 执行:
  1. validate(entry) → 确认通过
  2. 向用户确认："我注意到你... 这是准确的吗？"
  3. 确认后写入 cognition.md
  4. 更新 index.md
  5. 追加 log.md

### L4 → L5 (核心特质)
- 条件: ≥3 次独立观察 + 无反例 + 跨情境一致 + 明确确认
- 权限: 必须明确确认
- 执行:
  1. validate(entry) → 确认通过
  2. 向用户明确确认："我想确认——你是一个X的人，对吗？"
  3. 提供具体观察证据
  4. 确认后写入 core.md
  5. 更新 index.md
  6. 追加 log.md
  7. 标记"已确认核心认知"
```

---

### 2.3 demote(entry, reason) —— 降级记忆

**输入**：条目 ID + 降级原因

**输出**：降级结果

```json
{
  "entry_id": "L3-B-003",
  "from_level": "L3",
  "to_level": "L2",
  "reason": "出现反例：紧急场景下快速决策",
  "timestamp": "2024-02-01T10:30:00+08:00",
  "original_content_preserved": true,
  "archive_path": "brain/archive/by-layer/L3-B-003.md"
}
```

**降级规则**：

```markdown
## 降级触发条件

### 自动降级
- 出现明确反例 → 降一级
- 3个月无新验证 → 标记存疑
- 6个月无任何引用 → 建议归档

### 手动降级
- 用户否认认知 → 立即降级
- 用户修正理解 → 降级后重新提炼
- Agent判断信息过时 → 降级并记录原因

### 降级保护
- 原始内容保留在 archive/
- 降级原因必须记录
- 降级操作写入 log.md
- 可恢复（从 archive/ 恢复）
```

---

### 2.4 archive(entry) —— 归档

**输入**：条目 ID

**输出**：归档结果

```json
{
  "entry_id": "L2-2023-001",
  "archive_path": "brain/archive/by-date/2023/L2-2023-001.md",
  "reason": "超过12个月，L2滚动窗口",
  "recoverable": true,
  "index_updated": true
}
```

**归档规则**：

```markdown
## 归档策略

### L2 情境层
- 活跃: 最近6个月
- 过渡: 6-12个月（降低检索优先级）
- 归档: 超过12个月 → archive/by-date/

### L3 行为层
- 出现反例 → 降级后归档原版本
- 被新认知替代 → 归档旧版本

### L4/L5 认知层
- 仅在用户明确要求时归档
- 归档必须保留原因和可追溯记录

### 归档格式
---
id: [原始ID]
original_path: [原始路径]
archived_at: [归档时间]
reason: [归档原因]
merged_into: [合并目标ID，如有]
recoverable: true
---

[原始内容完整保留]
```

---

### 2.5 lint(scope) —— 健康检查

**输入**：检查范围（all / specific-layer / specific-file）

**输出**：检查报告

```json
{
  "scope": "all",
  "timestamp": "2024-02-01T20:00:00+08:00",
  "summary": {
    "total_entries": 45,
    "issues_found": 3,
    "auto_fixed": 1,
    "needs_attention": 2
  },
  "issues": [
    {
      "type": "contradiction",
      "severity": "high",
      "entries": ["L3-B-001", "L3-B-005"],
      "description": "决策风格矛盾：冷静期 vs 快速决策",
      "suggestion": "标记待验证，区分情境"
    },
    {
      "type": "orphan",
      "severity": "medium",
      "entry": "L3-B-007",
      "description": "无任何关联条目",
      "suggestion": "检查是否应关联或归档"
    },
    {
      "type": "dangling_ref",
      "severity": "low",
      "entry": "index.md#L2-2024-018",
      "description": "引用了不存在的L2-2024-018",
      "suggestion": "清理引用或创建条目",
      "auto_fixed": true
    }
  ]
}
```

**检查项目**：

```markdown
## Lint 检查清单

### 1. 矛盾检测
- 扫描同层条目，检测语义冲突
- 标记矛盾对，建议处理方式
- 严重程度: 高（直接影响认知准确性）

### 2. 孤儿条目
- 检测无任何关联的条目
- 无 related、contradicts、supports
- 严重程度: 中（可能遗漏关联）

### 3. 缺失关联
- 检测应该关联但未关联的条目
- 如: L4 价值观无 L3 行为支撑
- 严重程度: 中（认知链条不完整）

### 4. 悬空引用
- 检测引用了不存在的条目
- 如: index.md 引用 L2-2024-018 但文件不存在
- 严重程度: 低（可自动修复）

### 5. 索引一致性
- 检测 index.md 与实际内容不一致
- 如: 索引中标记"高置信度"但条目标记"待验证"
- 严重程度: 中（可能误导检索）

### 6. Token 超限检查
- 检测认知文件是否超出 Token 上限
- 超限: 触发压缩管线
- 严重程度: 高（影响上下文加载）

### 7. 过期条目
- 检测超过滚动窗口的 L2 条目
- 检测长期无更新的待验证条目
- 严重程度: 低（不影响准确性）

### 8. 格式规范
- 检测条目是否符合写入规范
- 缺少 id/layer/keywords/status 字段
- 严重程度: 低（不影响功能）
```

---

## 三、治理策略配置 [v2新增]

### 配置文件

```json
{
  "governance": {
    "evidence_threshold": {
      "L3": 3,
      "L4": 3,
      "L5": 3
    },
    "confirmation_required": ["L4", "L5"],
    "auto_promote": ["L2_to_L3"],
    "contradiction_strategy": "mark_and_keep",
    "decay": {
      "active_months": 6,
      "transition_months": 12
    },
    "lint_schedule": {
      "quick": "on_session_end",
      "deep": "weekly"
    },
    "archive": {
      "l2_rolling_window_months": 12,
      "pending_stale_months": 3,
      "preserve_original": true
    },
    "token_limits": {
      "index_md": 500,
      "behavior_md": 2000,
      "cognition_md": 1500,
      "core_md": 800
    }
  }
}
```

### 配置项说明

| 配置项 | 默认值 | 说明 | 调整建议 |
|--------|--------|------|----------|
| `evidence_threshold.L3` | 3 | L3 提炼需要的独立观察次数 | 严格场景可提高到 5 |
| `evidence_threshold.L4` | 3 | L4 提炼需要的独立观察次数 | 不建议低于 3 |
| `evidence_threshold.L5` | 3 | L5 提炼需要的独立观察次数 | 不建议低于 3 |
| `confirmation_required` | ["L4","L5"] | 需要用户确认的层级 | 建议保持默认 |
| `auto_promote` | ["L2_to_L3"] | 可自动提升的层级转换 | 严格场景可设为空 |
| `contradiction_strategy` | "mark_and_keep" | 矛盾处理策略 | 见下表 |
| `decay.active_months` | 6 | 活跃条目的最短生命周期 | 按使用频率调整 |
| `decay.transition_months` | 12 | 过渡期长度 | 按记忆密度调整 |

### 矛盾处理策略选项

| 策略 | 行为 | 适用场景 |
|------|------|----------|
| `mark_and_keep` | 标记矛盾，两方保留 | 默认策略，最安全 |
| `new_overwrites` | 新认知覆盖旧认知 | 快速迭代场景 |
| `user_decides` | 标记矛盾，等用户决定 | 严格质量要求 |
| `evidence_wins` | 证据更充分的一方胜出 | 有明确量化标准时 |

---

## 四、作为插件接入其他系统 [v2新增]

### 设计原则

治理层是**平台无关的**——它不关心记忆怎么存，只关心记忆的质量。任何记忆系统都可以通过治理 API 接入。

### 接入 AgentMemory

```
AgentMemory 的自动捕获机制
    │
    ├── on_new_observation → 自动捕获
    │       │
    │       ▼
    │   governance.validate(新观察)
    │       │
    │       ├── pass → 写入认知层（L2/L3）
    │       ├── fail → 丢弃或标记待验证
    │       └── needs_review → 进入待验证队列
    │
    ├── on_consolidation → 合并压缩
    │       │
    │       ▼
    │   governance.promote(条目, 目标层级)
    │       │
    │       ├── success → 提升层级
    │       └── denied → 保持原层级
    │
    └── on_contradiction → 矛盾检测
            │
            ▼
        governance.validate(矛盾条目)
            │
            ├── 情境性 → 降级表述
            └── 实质性 → 标记待验证
```

### 接入 Mem0

```
Mem0 的 add() 操作
    │
    ├── mem0.add(新记忆)
    │       │
    │       ▼
    │   governance.validate(新记忆)
    │       │
    │       ├── pass → mem0.store()
    │       ├── fail → 拒绝存储
    │       └── needs_review → mem0.store(metadata={"governance":"needs_review"})
    │
    ├── mem0.search() → 检索结果
    │       │
    │       ▼
    │   governance.validate(检索结果) → 过滤低置信度
    │
    └── mem0.update() → 更新记忆
            │
            ▼
        governance.validate(更新内容) → 确保更新质量
```

### 接入纯文件系统

```
纯文件系统 (无框架)
    │
    ├── 定期 lint
    │       │
    │       ▼
    │   governance.lint(scope="all")
    │       │
    │       ├── 发现矛盾 → 标记
    │       ├── 发现孤儿 → 建议关联或归档
    │       ├── 发现悬空引用 → 自动修复
    │       └── 发现格式问题 → 生成修复建议
    │
    ├── 写入前校验
    │       │
    │       ▼
    │   governance.validate(新条目) → 确保写入质量
    │
    └── 层级提升
            │
            ▼
        governance.promote(条目, 层级) → 规范提升流程
```

### 接入模式对比

| 接入模式 | 优势 | 劣势 | 适用场景 |
|---------|------|------|----------|
| **全接入** (validate + promote + lint) | 质量最高 | 性能开销大 | 记忆量少、质量要求高 |
| **校验接入** (仅 validate) | 轻量，性能好 | 无主动健康检查 | 记忆量大、实时性要求高 |
| **Lint 接入** (仅定期 lint) | 离线检查，不阻塞 | 不能实时拦截 | 批处理场景 |
| **审计接入** (仅读 log) | 零侵入 | 不能阻止问题 | 已有质量体系的系统 |

---

## 五、治理仪表盘 [v2新增]

### 仪表盘数据

```markdown
## 记忆治理仪表盘

### 总览
| 指标 | 数值 | 趋势 |
|------|------|------|
| 记忆总量 | 45 条 | ↑ +5 本周 |
| L2 情境 | 28 条 | ↑ +3 |
| L3 行为 | 10 条 | ↑ +1 |
| L4 认知 | 5 条 | — |
| L5 核心 | 2 条 | — |

### 质量指标
| 指标 | 数值 | 状态 |
|------|------|------|
| 待验证条目 | 4 条 | 🟡 需关注 |
| 平均验证进度 | 1.8/3 | 🟡 进展中 |
| 矛盾条目 | 1 对 | 🔴 需处理 |
| 孤儿条目 | 2 条 | 🟡 需关联 |
| 悬空引用 | 0 条 | 🟢 健康 |
| Token 超限文件 | 0 个 | 🟢 健康 |

### 7天趋势
| 日期 | 新增 | 归档 | 降级 | 提升确认 |
|------|------|------|------|----------|
| 02-01 | +2 | -1 | 0 | 1 |
| 01-31 | +1 | 0 | 0 | 0 |
| 01-30 | +3 | -2 | -1 | 0 |
| 01-29 | +0 | 0 | 0 | 1 |
| 01-28 | +1 | -1 | 0 | 0 |
| 01-27 | +2 | 0 | 0 | 0 |
| 01-26 | +1 | 0 | 0 | 0 |

### 待处理
1. 🔴 矛盾: L3-B-001 vs L3-B-005 (决策风格矛盾)
2. 🟡 孤儿: L3-B-007 (无关联条目)
3. 🟡 孤儿: L2-2024-022 (无关联条目)
4. 🟡 待验证: P-001 决策冷静期 (1/3)
```

### 仪表盘生成方式

```markdown
## 生成步骤

### 自动生成（on_periodic_lint）
1. 扫描 brain/ 目录结构
2. 统计各层条目数量
3. 执行 lint(scope="all") 获取问题列表
4. 读取 log.md 统计最近 7 天操作
5. 生成仪表盘 Markdown
6. 保存至 brain/governance-dashboard.md

### 手动生成
- 查询: "生成治理仪表盘"
- 触发: 立即执行上述步骤
- 输出: 直接显示在对话中 + 保存文件

### 定期生成
- 频率: 每周（配合 Calendar 触发）
- 存储: brain/governance-dashboard.md
- 历史: 保留最近 4 份，更早的归档
```

### 仪表盘平台适配

| 平台 | 展示方式 | 生成方式 |
|------|----------|----------|
| **扣子** | Markdown 表格（对话中展示） | 对话中生成 + 保存文件 |
| **Claude Code** | Markdown 表格 + 可视化脚本 | 终端输出 + 保存文件 |
| **Cursor** | Markdown 预览 | 内联生成 |
| **通用** | Markdown 文件 | 文件保存 |

---

## 六、与 cognitive-memory 的协作 [v2新增]

### 治理层在认知流程中的位置

```
ingest → wiki-builder → cognitive-memory → git-sync
              │               │
              └───────┬───────┘
                      │
              ┌───────┴───────┐
              │               │
         retrieval-engine  memory-governance
         (怎么取)          (怎么保证对)
```

**治理层不改变认知层的存储结构，只确保写入和访问的质量。**

### 协作场景

| 场景 | cognitive-memory 做什么 | memory-governance 做什么 |
|------|------------------------|------------------------|
| **新观察写入** | 捕获新信号 | validate() 校验后决定写入/拒绝/待验证 |
| **层级提升** | 触发提升决策 | promote() 检查证据/冲突/权限 |
| **矛盾检测** | 发现矛盾 | validate() 判断矛盾类型，决定处理策略 |
| **定期维护** | 触发 Lint | lint() 全面检查，生成报告 |
| **归档管理** | 触发归档 | archive() 安全归档，保留可恢复 |
| **降级处理** | 触发降级 | demote() 记录原因，保留历史 |

### 治理钩子（Hook）

```markdown
## 认知流程中的治理钩子

### 写入前钩子
触发: 任何条目写入 cognitive-memory 之前
操作: governance.validate(entry)
结果:
  - pass → 允许写入
  - fail → 拒绝写入，返回原因
  - needs_review → 标记待验证，允许写入 pending

### 提升前钩子
触发: 层级提升决策时
操作: governance.promote(entry, target_level)
结果:
  - success → 执行提升
  - denied → 阻止提升，返回原因
  - needs_confirmation → 等待用户确认

### 会话结束钩子
触发: on_session_end
操作: governance.lint(scope="recent")
结果:
  - 发现问题 → 记录到 pending.md
  - 无问题 → 无操作

### 定期检查钩子
触发: on_periodic_lint
操作: governance.lint(scope="all")
结果:
  - 生成仪表盘
  - 自动修复（悬空引用等）
  - 标记需人工处理的问题
```

---

## 七、治理日志 [v2新增]

### 治理操作日志格式

在现有 `brain/log.md` 中追加治理相关条目：

```markdown
### 2024-02-01 20:00
- **操作**: governance.validate
- **条目**: L3-B-008
- **结果**: needs_review
- **原因**: 仅1次观察，未达小样本阈值
- **处理**: 写入 pending.md，标记 1/3

### 2024-02-01 20:05
- **操作**: governance.promote
- **条目**: L2-2024-010 → L3
- **结果**: success
- **证据**: 3次独立观察，无反例
- **处理**: 写入 behavior.md，更新索引

### 2024-02-01 20:10
- **操作**: governance.lint
- **范围**: all
- **结果**: 3 issues found
- **自动修复**: 1 (悬空引用)
- **需处理**: 2 (矛盾1, 孤儿1)

### 2024-02-01 20:15
- **操作**: governance.demote
- **条目**: L3-B-003 → L2
- **原因**: 出现反例
- **处理**: 降级并归档原版本

### 2024-02-01 20:20
- **操作**: governance.archive
- **条目**: L2-2023-001
- **原因**: 超过12个月滚动窗口
- **处理**: 移入 archive/by-date/2023/
```

---

## 八、治理策略最佳实践 [v2新增]

### 何时严格，何时宽松

```markdown
## 严格度选择指南

### 严格模式（推荐默认）
- evidence_threshold: L3=5, L4=5, L5=5
- confirmation_required: ["L3","L4","L5"]
- contradiction_strategy: "user_decides"
- 适用: 长期使用、重要决策场景

### 标准模式（默认）
- evidence_threshold: L3=3, L4=3, L5=3
- confirmation_required: ["L4","L5"]
- contradiction_strategy: "mark_and_keep"
- 适用: 日常使用

### 宽松模式
- evidence_threshold: L3=2, L4=3, L5=3
- confirmation_required: ["L5"]
- contradiction_strategy: "new_overwrites"
- 适用: 快速探索、临时项目

### 审计模式
- evidence_threshold: L3=3, L4=3, L5=3
- confirmation_required: ["L4","L5"]
- contradiction_strategy: "mark_and_keep"
- 所有操作记录完整审计日志
- 适用: 合规要求、团队使用
```

### 治理与检索的协同

```markdown
## 治理影响检索

### 置信度排序
- validate() 结果为 pass → 置信度 H
- validate() 结果为 needs_review → 置信度 M
- validate() 结果为 fail → 不进入检索结果

### 矛盾标注
- lint() 发现的矛盾 → 检索结果中标注 [矛盾]
- 检索返回矛盾条目时 → 同时返回矛盾对

### 孤儿条目
- lint() 发现的孤儿 → 检索时降低优先级
- 关联检索无法到达孤儿条目

### Token 超限
- lint() 发现 Token 超限 → 触发压缩管线
- 压缩后再检索 → 减少无效 Token
```

---

## 九、平台适配完整指南 [v2新增]

### 扣子平台

```markdown
## 扣子适配方案

### validate()
- 实现: 在对话中执行校验 Prompt（见 2.1 节）
- 不需要额外工具
- 结果以结构化 Markdown 输出

### promote()
- 实现: 读取条目 → 执行校验 → 写入目标文件
- 工具: read_file + edit_file
- L4/L5 确认: 对话中直接询问用户

### demote()
- 实现: 读取条目 → 归档原版本 → 写入降级版本
- 工具: read_file + write_file + edit_file

### archive()
- 实现: 移动文件内容到 archive/ 目录
- 工具: read_file + write_file + edit_file（删除原条目）

### lint()
- 实现: 批量 read_file → 逐条检查 → 生成报告
- 工具: read_file + memory_search（辅助检索）
- 定期触发: Calendar 任务
```

### Claude Code 平台

```markdown
## Claude Code 适配方案

### 全 API 支持
- validate: 脚本化校验，可批量执行
- promote: 脚本化提升，配合 git commit
- demote: 脚本化降级，保留 git history
- archive: 脚本化归档，配合 git mv
- lint: 定时 cron 执行，输出到文件

### 自动化能力
- git pre-commit hook: 写入前自动 validate
- cron: 定期 lint
- 脚本: 批量操作（归档过期条目等）
```

### Cursor 平台

```markdown
## Cursor 适配方案

### 内联校验
- 边写边校验: 保存文件时自动 validate
- 快捷操作: 右键菜单 → promote/demote/archive

### Lint 集成
- 问题面板: Lint 结果显示在 Problems 面板
- 快速修复: 点击问题 → 自动修复

### 仪表盘
- 侧边栏: 实时显示治理状态
- Markdown 预览: 仪表盘文件实时渲染
```

---

## 十、治理配置模板 [v2新增]

详见 `modules/memory-governance/templates/governance-config.md`

核心配置项：

```json
{
  "governance": {
    "version": "2.0",
    "strictness": "standard",
    "evidence_threshold": { "L3": 3, "L4": 3, "L5": 3 },
    "confirmation_required": ["L4", "L5"],
    "auto_promote": ["L2_to_L3"],
    "contradiction_strategy": "mark_and_keep",
    "decay": { "active_months": 6, "transition_months": 12 },
    "lint_schedule": { "quick": "on_session_end", "deep": "weekly" },
    "token_limits": { "index_md": 500, "behavior_md": 2000, "cognition_md": 1500, "core_md": 800 }
  }
}
```

---

*Memory Governance v2.0 — 让记忆"对得起信任"*
