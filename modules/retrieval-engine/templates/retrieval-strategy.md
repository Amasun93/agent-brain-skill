# 检索策略模板 [v2新增]

> 本文件定义不同查询类型对应的检索路径和参数配置。
> 检索引擎根据此模板选择最优检索策略。

---

## 策略定义

### 策略1：精确查找

```json
{
  "name": "精确查找",
  "trigger_patterns": [
    "包含明确关键词或ID",
    "包含'那个''上次'等指代词+具体主题",
    "包含文件名或编号"
  ],
  "primary_path": "keyword",
  "secondary_path": "association",
  "token_budget": 500,
  "parameters": {
    "keyword_match_mode": "exact_prefix_first",
    "association_hops": 1,
    "max_results": 5
  },
  "examples": [
    "上次讨论的那个JWT方案",
    "L2-2024-015在哪",
    "保险方案的讨论记录"
  ]
}
```

**执行流程**：
1. 从查询中提取关键词
2. 扫描 index.md 的 keywords 列精确匹配
3. 命中 → 返回条目摘要
4. 未命中 → 关联检索：从相近关键词的条目出发，1跳扩展
5. Token 预算 500 → 最多返回 5 条摘要

---

### 策略2：语义理解

```json
{
  "name": "语义理解",
  "trigger_patterns": [
    "模糊意图描述",
    "包含'态度''看法''倾向'等抽象词",
    "不包含具体关键词"
  ],
  "primary_path": "semantic",
  "secondary_path": "association",
  "token_budget": 2000,
  "parameters": {
    "semantic_focus": "prefer_files",
    "association_hops": 1,
    "max_results": 10,
    "depth": "summary_first"
  },
  "examples": [
    "用户对工作的态度",
    "用户怎么看待金钱",
    "用户的沟通风格"
  ]
}
```

**执行流程**：
1. 用自然语言描述查询意图
2. 语义检索：memory_search 或内置搜索
3. 从结果中选取最高分条目，做1跳关联检索
4. Token 预算 2000 → 先返回摘要，按需加载详情
5. 超预算按置信度截断

---

### 策略3：关系追踪

```json
{
  "name": "关系追踪",
  "trigger_patterns": [
    "包含'关联''相关''影响'等关系词",
    "包含'哪些''都有什么'等扩展词",
    "从已知条目出发的扩展查询"
  ],
  "primary_path": "association",
  "secondary_path": "semantic",
  "token_budget": 1500,
  "parameters": {
    "association_hops": 2,
    "relation_types": ["related", "supports", "contradicts", "evolved_from"],
    "semantic_supplement": true,
    "max_results": 15
  },
  "examples": [
    "这个行为模式关联哪些事件",
    "和谨慎价值观相关的都有什么",
    "L3-B-001的所有关联条目"
  ]
}
```

**执行流程**：
1. 识别起点条目（从查询中提取或从上次结果中获取）
2. 读取起点条目的关联字段
3. 1跳：直接关联条目
4. 2跳：关联条目的关联（可选）
5. 语义补充：对关联结果做语义检索，补充遗漏
6. Token 预算 1500 → 按跳数和置信度排序

---

### 策略4：全景扫描

```json
{
  "name": "全景扫描",
  "trigger_patterns": [
    "包含'所有''全部''全景''完整'等范围词",
    "跨层级综合查询",
    "初始化/汇报场景"
  ],
  "primary_path": "keyword+semantic",
  "secondary_path": "association",
  "token_budget": 2500,
  "parameters": {
    "keyword_expansion": true,
    "semantic_focus": "balanced",
    "association_hops": 1,
    "max_results": 20,
    "depth": "index_only_first",
    "group_by_layer": true
  },
  "examples": [
    "告诉我关于用户职业的所有认知",
    "全景扫描一下记忆状态",
    "汇总用户的价值观和行为模式"
  ]
}
```

**执行流程**：
1. 关键词检索 + 语义检索并行
2. 结果按层级分组（L2/L3/L4/L5）
3. 每层只返回索引摘要
4. 用户指定某层 → 加载该层详情
5. Token 预算 2500 → 优先保证覆盖面

---

### 策略5：快速定位

```json
{
  "name": "快速定位",
  "trigger_patterns": [
    "包含明确的文件名或条目ID",
    "包含'在哪''在哪里'等定位词",
    "直接引用编号"
  ],
  "primary_path": "keyword",
  "secondary_path": "none",
  "token_budget": 300,
  "parameters": {
    "keyword_match_mode": "exact_id_first",
    "max_results": 1,
    "depth": "summary_only"
  },
  "examples": [
    "L2-2024-015在哪",
    "behavior.md里有什么",
    "P-001的状态"
  ]
}
```

**执行流程**：
1. 识别目标 ID 或文件名
2. 直接读取对应文件/条目
3. 返回摘要
4. Token 预算 300 → 单条摘要足够

---

### 策略6：矛盾排查

```json
{
  "name": "矛盾排查",
  "trigger_patterns": [
    "包含'矛盾''冲突''不一致'等词",
    "包含'是不是搞错了''好像不对'等质疑词",
    "治理层 Lint 触发"
  ],
  "primary_path": "association",
  "secondary_path": "keyword",
  "token_budget": 1500,
  "parameters": {
    "association_hops": 2,
    "relation_types": ["contradicts", "related"],
    "keyword_search_scope": "same_layer",
    "max_results": 10,
    "contradiction_check": true
  },
  "examples": [
    "和这条认知矛盾的有哪些",
    "用户说的和做的不一致吗",
    "检查行为模式有没有冲突"
  ]
}
```

**执行流程**：
1. 识别矛盾排查的起点
2. 关联检索：找 contradicts 和 related 条目
3. 关键词检索：在同层中搜索可能矛盾的条目
4. 对每对可能矛盾执行 validate()
5. 返回矛盾列表 + 建议处理方式
6. Token 预算 1500 → 最多展示 5 对矛盾

---

## 策略选择算法

```markdown
## 自动策略选择流程

输入: 用户查询文本
输出: 匹配的策略配置

步骤:
1. 特征提取:
   - 是否包含ID/文件名? → 快速定位
   - 是否包含'所有''全部'? → 全景扫描
   - 是否包含'矛盾''冲突'? → 矛盾排查
   - 是否包含'关联''相关'? → 关系追踪
   - 是否包含明确关键词? → 精确查找
   - 默认 → 语义理解

2. 策略合并（如有多重特征）:
   - 精确查找 + 关系追踪 → 先定位再扩展
   - 语义理解 + 矛盾排查 → 语义检索+矛盾校验
   - 合并后的 Token 预算 = max(两策略预算)

3. 应用策略:
   - 激活主路径检索
   - 激活辅路径检索
   - 设定 Token 预算
   - 设定检索参数
```

---

## 平台适配参数覆盖

| 平台 | 策略调整 |
|------|----------|
| **扣子** | 语义理解策略的 primary_path 改为 memory_search；关联检索用 read_file + index.md |
| **Claude Code** | 所有策略保持默认；关键词检索用 ripgrep |
| **Cursor** | 所有策略保持默认；用内置搜索 |
| **通用** | 语义理解降级到关键词检索；Token 预算减半 |

---

*检索策略模板 v2.0 — 因查询制宜*
