# 治理配置模板 [v2新增]

> 本文件定义 memory-governance 模块的配置项和可选值。
> 复制此模板为 `governance-config.json` 并根据场景调整。

---

## 完整配置

```json
{
  "governance": {
    "version": "2.0",

    "strictness": "standard",

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
    },

    "dashboard": {
      "enabled": true,
      "schedule": "weekly",
      "retain_history": 4
    },

    "hooks": {
      "pre_write": "validate",
      "pre_promote": "promote",
      "post_session": "lint_quick",
      "periodic": "lint_deep"
    }
  }
}
```

---

## 配置项详解

### strictness（严格度）

| 值 | 说明 | 适用场景 |
|----|------|----------|
| `strict` | 高阈值 + 全确认 + 用户决策矛盾 | 长期使用、重要决策 |
| `standard` | 默认阈值 + L4/L5确认 + 标记保留 | 日常使用 |
| `loose` | 低阈值 + 仅L5确认 + 新覆盖旧 | 快速探索、临时项目 |
| `audit` | 默认阈值 + 完整审计日志 | 合规要求、团队使用 |

**严格度覆盖规则**：设置 strictness 后，以下配置项自动调整：

```json
{
  "strict": {
    "evidence_threshold": { "L3": 5, "L4": 5, "L5": 5 },
    "confirmation_required": ["L3", "L4", "L5"],
    "contradiction_strategy": "user_decides"
  },
  "standard": {
    "evidence_threshold": { "L3": 3, "L4": 3, "L5": 3 },
    "confirmation_required": ["L4", "L5"],
    "contradiction_strategy": "mark_and_keep"
  },
  "loose": {
    "evidence_threshold": { "L3": 2, "L4": 3, "L5": 3 },
    "confirmation_required": ["L5"],
    "contradiction_strategy": "new_overwrites"
  },
  "audit": {
    "evidence_threshold": { "L3": 3, "L4": 3, "L5": 3 },
    "confirmation_required": ["L4", "L5"],
    "contradiction_strategy": "mark_and_keep"
  }
}
```

> **注意**：如果同时设置了 strictness 和具体配置项，具体配置项优先。

---

### evidence_threshold（证据阈值）

| 字段 | 类型 | 默认值 | 范围 | 说明 |
|------|------|--------|------|------|
| `L3` | integer | 3 | 2-10 | 行为模式提炼需要的独立观察次数 |
| `L4` | integer | 3 | 2-10 | 价值观认知提炼需要的独立观察次数 |
| `L5` | integer | 3 | 2-10 | 核心特质提炼需要的独立观察次数 |

**调整建议**：
- 记忆量少（<50条）：保持默认 3
- 记忆量多（>200条）：提高到 5
- 关键决策场景：L5 提高到 5+

---

### confirmation_required（确认要求）

| 值 | 说明 |
|----|------|
| `["L4", "L5"]` | 默认：L4/L5 需要用户确认 |
| `["L3", "L4", "L5"]` | 严格：L3 也需要确认 |
| `["L5"]` | 宽松：仅 L5 需要确认 |
| `[]` | 最宽松：全自动（不推荐） |

---

### auto_promote（自动提升）

| 值 | 说明 |
|----|------|
| `["L2_to_L3"]` | 默认：L2→L3 可自动提升 |
| `[]` | 所有提升都需要确认 |
| `["L2_to_L3", "L3_to_L4"]` | L2→L3 和 L3→L4 可自动 |

> **注意**：L4→L5 永远不允许自动提升，必须用户明确确认。

---

### contradiction_strategy（矛盾策略）

| 值 | 行为 | 风险 | 适用 |
|----|------|------|------|
| `mark_and_keep` | 标记矛盾，两方保留 | 最低 | 默认，最安全 |
| `new_overwrites` | 新认知覆盖旧认知 | 中 | 快速迭代 |
| `user_decides` | 等用户决定 | 最低 | 严格质量要求 |
| `evidence_wins` | 证据更充分的一方胜出 | 低 | 有量化标准 |

---

### decay（衰减规则）

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `active_months` | integer | 6 | 活跃条目最短生命周期 |
| `transition_months` | integer | 12 | 过渡期结束进入归档 |

**衰减流程**：
```
0 ──── active_months ──── transition_months ────→
│         活跃期              过渡期              归档
│      (正常检索)        (降低优先级)          (移入archive/)
```

---

### lint_schedule（Lint 计划）

| 字段 | 值 | 说明 |
|------|-----|------|
| `quick` | `"on_session_end"` / `"on_session_start"` / `"manual"` | 快速 Lint 触发时机 |
| `deep` | `"weekly"` / `"biweekly"` / `"monthly"` / `"manual"` | 深度 Lint 频率 |

**Quick Lint 范围**：
- 悬空引用检查
- 索引一致性检查
- 仅检查本次会话涉及的条目

**Deep Lint 范围**：
- 全部检查项目（矛盾/孤儿/缺失关联/悬空引用/索引一致性/Token超限/过期/格式）
- 生成治理仪表盘

---

### archive（归档配置）

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `l2_rolling_window_months` | integer | 12 | L2 情境条目的滚动窗口 |
| `pending_stale_months` | integer | 3 | 待验证条目无进展的最大月数 |
| `preserve_original` | boolean | true | 归档时是否保留原始内容 |

---

### token_limits（Token 上限）

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `index_md` | integer | 500 | index.md 的 Token 上限 |
| `behavior_md` | integer | 2000 | behavior.md 的 Token 上限 |
| `cognition_md` | integer | 1500 | cognition.md 的 Token 上限 |
| `core_md` | integer | 800 | core.md 的 Token 上限 |

**超限处理**：触发压缩管线（见 cognitive-memory SKILL.md [v2新增] 章节）

---

### dashboard（仪表盘）

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `enabled` | boolean | true | 是否启用仪表盘 |
| `schedule` | string | `"weekly"` | 生成频率 |
| `retain_history` | integer | 4 | 保留最近几份历史 |

---

### hooks（治理钩子）

| 字段 | 值 | 说明 |
|------|-----|------|
| `pre_write` | `"validate"` / `"none"` | 写入前是否校验 |
| `pre_promote` | `"promote"` / `"none"` | 提升前是否检查 |
| `post_session` | `"lint_quick"` / `"none"` | 会话结束后是否快速 Lint |
| `periodic` | `"lint_deep"` / `"none"` | 定期是否深度 Lint |

---

## 预设配置方案

### 个人日常使用（推荐）

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
    "archive": { "l2_rolling_window_months": 12, "pending_stale_months": 3, "preserve_original": true },
    "token_limits": { "index_md": 500, "behavior_md": 2000, "cognition_md": 1500, "core_md": 800 },
    "dashboard": { "enabled": true, "schedule": "weekly", "retain_history": 4 },
    "hooks": { "pre_write": "validate", "pre_promote": "promote", "post_session": "lint_quick", "periodic": "lint_deep" }
  }
}
```

### 团队协作使用

```json
{
  "governance": {
    "version": "2.0",
    "strictness": "audit",
    "evidence_threshold": { "L3": 5, "L4": 5, "L5": 5 },
    "confirmation_required": ["L3", "L4", "L5"],
    "auto_promote": [],
    "contradiction_strategy": "user_decides",
    "decay": { "active_months": 12, "transition_months": 24 },
    "lint_schedule": { "quick": "on_session_end", "deep": "biweekly" },
    "archive": { "l2_rolling_window_months": 24, "pending_stale_months": 6, "preserve_original": true },
    "token_limits": { "index_md": 500, "behavior_md": 2000, "cognition_md": 1500, "core_md": 800 },
    "dashboard": { "enabled": true, "schedule": "biweekly", "retain_history": 12 },
    "hooks": { "pre_write": "validate", "pre_promote": "promote", "post_session": "lint_quick", "periodic": "lint_deep" }
  }
}
```

### 快速探索使用

```json
{
  "governance": {
    "version": "2.0",
    "strictness": "loose",
    "evidence_threshold": { "L3": 2, "L4": 3, "L5": 3 },
    "confirmation_required": ["L5"],
    "auto_promote": ["L2_to_L3", "L3_to_L4"],
    "contradiction_strategy": "new_overwrites",
    "decay": { "active_months": 3, "transition_months": 6 },
    "lint_schedule": { "quick": "manual", "deep": "monthly" },
    "archive": { "l2_rolling_window_months": 6, "pending_stale_months": 2, "preserve_original": false },
    "token_limits": { "index_md": 500, "behavior_md": 2000, "cognition_md": 1500, "core_md": 800 },
    "dashboard": { "enabled": true, "schedule": "monthly", "retain_history": 2 },
    "hooks": { "pre_write": "none", "pre_promote": "none", "post_session": "none", "periodic": "lint_deep" }
  }
}
```

---

## 配置变更日志

每次修改配置应记录：

```markdown
### YYYY-MM-DD
- **变更**: [描述变更内容]
- **原因**: [为什么变更]
- **影响**: [影响哪些功能]
- **操作者**: [谁修改的]
```

---

*治理配置模板 v2.0 — 因场景制宜*
