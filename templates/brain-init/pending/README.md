# pending/ — 毛坯区

> **v2.2 新增**：所有新信号的"暂存区"，两阶段工作流的第一阶段。
> **不要写进 cognition/**：新信号先到这里，**不**直接进 L3/L4 认知层。
> **不是矛盾队列**：矛盾 / 待验证 L2+ 仍走 v2.1 的 `pending.md`。

---

## 是什么

`brain/pending/` 是 agent-brain v2.2 引入的**新信号毛坯区**：

- **每个文件 = 一个待验证主题**
- **文件名规范**：`YYYY-MM-DD-{简短主题}.md`
- **生命周期**：写入 → 证据积累 → governance.lint() 蒸馏 → promote/demote/归档

**为什么需要这个目录**：

| v2.1 痛点 | v2.2 解法 |
|----------|-----------|
| 新信号直接占 L3/L4 槽位 | 新信号先到 pending/，证据达标再 promote |
| 一次性表达污染认知层 | pending/ 是天然缓冲 |
| 评测员说"模板空壳、缺案例" | pending/ 文件本身就是真实新信号展示区 |

---

## 怎么写

### 最小规范

```markdown
---
topic: 决策冷静期
date: 2026-06-07
evidence_count: 1/3
related: [cognition/behavior.md#决策风格]
status: 毛坯
---

## 观察记录
- 时间: 2026-06-07 14:30
- 情境: 讨论保险方案
- 原话: "我需要再想想"
- 后续: 用户 2 天后才回来决定

## 初步假设
用户做决策前需要冷静期，不是真的"想想"

## 待验证
- [ ] 是否每次重大决策都需要？
- [ ] 冷静期一般是几天？
```

### 最小信息

- 主题名 + 日期 + ≥1 段观察
- 其他字段（related、status、evidence_count）可选
- **不要**追求完美结构——pending/ 是毛坯区

### 同一主题多次出现

```text
第 1 次观察：pending/2026-06-07-决策冷静期.md（evidence_count: 1/3）
第 2 次观察：追加观察记录，evidence_count: 2/3
第 3 次观察：再追加，evidence_count: 3/3 → lint 触发 promote
```

**字符串相似度去重**（复用 v2.1 算法）：Levenshtein 距离 ≤ 3 字符 OR 关键词 Jaccard ≥ 0.5 → 视为同一主题。

---

## 怎么用（蒸馏流程）

```text
【阶段 1：捕获】
  新信号 → pending/{日期-主题}.md（毛坯）

【阶段 2：蒸馏（governance.lint() 触发）】
  governance.lint(scope="pending")
    ↓
  判定：
    a. 主题明确 + 证据 ≥ 3 + 无反例 → promote 到 cognition/behavior.md
    b. 主题明确 + 证据不足 → 转 pending.md（v2.1 待验证队列）
    c. 反例出现 → demote，标注"已否定"
    d. 长期无进展（>30天） → demote，归档
    e. 一次性表达 / 噪声 → 直接归档
```

---

## 配置开关

如果你的 Agent 习惯直接写 cognition/，可以**关闭两阶段**（保持 v2.1 行为）：

```json
{
  "modules": {
    "cognitive_memory": {
      "two_stage_workflow": {
        "enabled": true,
        "evidence_threshold": 3,
        "auto_lint": "daily"
      }
    }
  }
}
```

> 默认开启两阶段。如果你的 Agent 已经有成熟的提炼流程，可关闭。

---

## 与 pending.md 的边界

| 路径 | 角色 | 何时写 |
|------|------|--------|
| `brain/pending.md` | 矛盾 / 待验证 L2+ 队列 | on_contradiction_detected 触发 |
| `brain/pending/{日期-主题}.md` | 毛坯区（所有新信号） | on_new_observation 触发 |

> **简记**：pending.md = 已认定是认知层候选；pending/ = 暂存一切新信号。

---

## 平台适配

| 平台 | pending/ 支持 | 降级方案 |
|------|---------------|----------|
| **扣子（有文件能力）** | ✅ 完整支持 | — |
| **Claude Code** | ✅ 完整支持 | — |
| **Cursor** | ✅ 完整支持 | — |
| **纯对话 Agent** | ⚠️ 降级 | 用对话内"待验证清单"替代 |

---

## 进一步阅读

- 主 SKILL.md "核心机制 2：先毛坯后蒸馏"
- `modules/cognitive-memory/SKILL.md` "pending/ 毛坯区" 章节
- `modules/memory-governance/SKILL.md` "lint 检查清单 - 9. pending/ 扫描" 章节
