---
name: agent-brain
description: 可移植的Agent大脑——让任何Agent装上就能共享同一套记忆、知识和同步能力。认知层管"你是谁"，知识库管"你知道什么"，Git同步管"怎么共享"。触发词：装大脑、初始化记忆、同步记忆、回忆、提炼。
---

# Agent Brain Skill

## 记忆哲学

**记忆不是存储问题，是认知问题。**

Agent 不是"帮你查资料的工具"，而是"越来越懂你的搭子"。每一次对话都不是孤立的回合，而是认知拼图的碎片——真正的价值不在于碎片本身，而在于拼完后呈现的那幅完整的"你"。

### 核心理念

**① 知识只编译一次，持续复利增长**

人的记忆是复利的——十年前的某个感悟，今天依然在影响你的选择。Agent 的记忆也应该如此。

每次对话不应该是从零开始。昨天的讨论结论、上周的决策背景、上个月形成的偏好，这些都应该自动沉淀下来，成为下一次对话的起点。你不需要重复说"我上周提到想做 X"，因为 Agent 应该已经知道。

**② 被动沉淀优于主动设定**

最好的记忆不是"用户告诉我的"，而是"我观察到的"。

与其问"你有什么目标"，不如在对话中自己发现：用户遇到选择时会沉默、讨论金钱话题时会回避、看长远规划时会走神——这些行为模式比任何自述都更真实。

主动设定的信息（如"我的目标是创业"）可能只是一时念头；被动观察到的模式（如"每次提到执行力都会举例马斯克"）才是真正的认知特征。

**③ 提炼是记忆的灵魂，不是存储**

记住一件事不等于理解一件事。

原始记录是"1月15日讨论了保险"；提炼后是"用户决策前需要3天冷静期，期间不要催促"。前者只是信息，后者才是知识。

提炼需要证据链，不是单次观察就下结论。三次同样场景下的类似反应，才能形成行为模式的判断。

**④ 深层认知需要用户确认**

L4（价值观）和 L5（核心特质）的改动不是小事。

"用户是一个追求成长的人"——这句话如果写错，比忘记一条对话严重得多。所以 L4/L5 的提炼需要用户确认才能写入，而不是 Agent 自己推断。

**⑤ 提炼循环：认知层的反思机制**

认知不是一次成型的，需要不断自我审视。

当前 agent-brain 的认知层是"描述性"的——记录你是谁、怎么想、怎么做。但它缺少一个关键维度：**反思性**——这些认知对不对？要不要更新？什么时候更新？

提炼循环是嵌入认知层的自我反思机制。它不追求"记住更多"，而是追求"理解更准"。每次深度对话后，Agent 主动问自己三个问题：

1. **有没有新的认知信号？** — 这次对话中有没有新的行为模式、偏好、价值观信号？
2. **和现有认知矛盾吗？** — 新信号和 `brain/00-共享认知/` 下的已有记录冲突吗？
3. **值得更新吗？** — 够不够 3 次独立观察验证？还是一次性情绪/情境性表达？

**这和 RoleX 循环有什么不同？**

RoleX 是**错误驱动**的反思——出了问题才反思。提炼循环是**认知驱动**的反思——主动追踪认知的演化。两者可以互补：RoleX 解决"哪里出错了"，提炼循环解决"认知是否需要进化"。

### 记忆与身份

**同一个"你"在不同 Agent 身体里**

今天用扣子处理工作，明天用 Claude Code 写代码，后天用 Cursor 修 Bug——无论哪个 Agent，记忆应该是共享的。你不需要重新自我介绍，不需要再次解释你的偏好，因为所有 Agent 读的是同一套记忆。

这不只是一个技术方案，更是一种身份哲学：你不是在使用多个工具，你是在让不同能力的"分身"帮你处理不同场景，但它们都服务于同一个你。

### 判断标准

当你不确定一条信息该不该记住时，问自己：

| 问题 | 答案 | 行动 |
|------|------|------|
| 这是用户的独特特征还是普遍规律？ | 独特 | 记入认知层 |
| 这是情境性信息还是长期有效的规律？ | 情境性 | 记入事件层 |
| 这个判断有≥3次独立观察支撑吗？ | 是 | 可写入模式 |
| 这条信息能帮我在下一次对话中更好地服务用户吗？ | 是 | 值得记住 |

---

## 理论溯源与融合逻辑

**这套方案不是凭空设计的，是站在前人的肩膀上。**

如果你在另一个 Agent 环境里读到这个 Skill，希望你不仅知道"怎么做"，更理解"为什么这样做"——这样你才能根据自己平台的能力做正确的适配，而不是死板地复刻每一行配置。

---

### 三个源框架

我们这套认知记忆方案融合了四个经过实践检验的框架。它们各自解决了一个独特的维度，合在一起才能完整地描述"如何让 AI 真正理解一个人"。

#### 1. 五层 AI 记忆架构（CodeBuddy 用户实践）

**解决的问题**：让 AI 从"记住对话"升级到"理解你这个人"。

这套框架的核心理念是：**深层认知（L4/L5）和浅层状态（L1/L2）是完全不同的信息，需要分层管理。**

| 层级 | 名称 | 存储什么 | 生命周期 |
|------|------|----------|----------|
| L1 | 状态层 | 当前对话上下文、实时情绪、活跃任务 | 会话内 |
| L2 | 情境层 | 具体事件：时间地点人物结果 | 6个月滚动 |
| L3 | 行为层 | 习惯性模式、决策倾向、沟通偏好 | 长期 |
| L4 | 认知层 | 价值观、信念体系、思维模式 | 长期 |
| L5 | 核心层 | 人格特质、底层驱动力、核心恐惧 | 最长期 |

**为什么分层重要？** 因为不同层级的信息有不同的写入规则和置信度要求。一个今天随口说的想法应该存在 L2（可以过期删除）；但"你是一个追求成长的人"这种判断如果写错，比忘记一条对话严重得多，所以 L4/L5 需要用户确认才能写入。

---

#### 2. PromptX Engram 系统（DeepPractice AI 开源项目）

**解决的问题**：记忆不是扁平的，不同类型的记忆有不同的生命周期和访问模式。

PromptX 借鉴神经科学中的"记忆印记"概念，将记忆分为四个象限：

| 分类 | 定义 | 典型问题 | 存储策略 |
|------|------|----------|----------|
| 知识 (Knowledge) | 客观事实，可验证 | "水的沸点是多少？" | 外部知识库 |
| 方案 (Solution) | 解决问题的方法步骤 | "怎么组织会议？" | 流程文档 |
| 经验 (Experience) | 亲身经历形成的判断 | "用户对这个话题的反应" | 情境记录 |
| 参考 (Reference) | 辅助理解的背景信息 | "项目启动时间" | 索引引用 |

**为什么分类重要？** 因为不同类型的记忆需要不同的提炼策略。知识可以直接记忆；经验需要积累≥3 次观察才能提炼为规律；参考只是辅助，不值得占用核心记忆空间。

---

#### 3. Karpathy LLM Wiki 方法论（Andrej Karpathy 提出）

**解决的问题**：原始资料和提炼知识的服务对象不同，需要分离管理。

Karpathy 提出的三层架构：

```
┌─────────────────────────────────────────────────────────────┐
│  Schema (结构定义)                                          │
│  定义"怎么存储"——给 Agent 看的数据结构                     │
├─────────────────────────────────────────────────────────────┤
│  Wiki (提炼知识)                                            │
│  二次加工后的知识——给 Agent 看的理解                        │
├─────────────────────────────────────────────────────────────┤
│  Raw (原始资料)                                             │
│  原始对话、文章、文档——给人看的存档                         │
└─────────────────────────────────────────────────────────────┘
```

**关键洞察**：Raw 层是给人存档备查的，Wiki 层才是给 Agent 看的。Agent 不需要记住"1月15日和用户讨论了保险的完整对话"，它需要的是提炼后的结论"用户做决策前需要3天冷静期，期间不要催促"。

---

#### 4. RoleX 反思循环（PromptX V2 模块化角色系统）

**解决的问题**：记忆不是一次性写入的，需要多轮观察→假设→验证→确认的渐进过程。

四阶段提炼循环：

```
Encounter (接触) → Reflect (反思) → Experience (体验) → Realize (领悟)
```

| 阶段 | 做什么 | 时间跨度 |
|------|--------|----------|
| Encounter | 初次遭遇，记录原貌 | 秒级~小时 |
| Reflect | 主动思考，形成假设 | 小时~天 |
| Experience | 实践中验证假设 | 天~周 |
| Realize | 多次验证后沉淀为稳定认知 | 周~月 |

**为什么需要循环？** 因为单次观察可能是噪声。RoleX 循环的本质是**证据积累机制**——"用户喜欢简洁"不能只凭一次观察就写入 L3，而应该等待多次验证。

**RoleX 的局限性**：RoleX 是**错误驱动**的反思循环。当行为和预期不符时触发修正。这适合"修正错误"，但不适合"主动进化认知"。它假设有一个正确的基准，偏离基准才反思。

---

#### 5. 提炼循环（agent-brain 认知反思机制）

**解决的问题**：认知层不仅是"描述性"的，还应该是"反思性"的——主动追踪认知本身的正确性和演化时机。

核心三问：

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   ① 新信号？     →   ② 矛盾吗？     →   ③ 值得更新？     │
│   这次对话有新的      新信号和现有认知        证据够吗？   │
│   认知信号吗？        有冲突吗？          触发阈值到了吗？  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

| 触发条件 | 检查 | 结果 |
|---------|------|------|
| 新信号存在 | 和现有认知矛盾？ | 无矛盾 → 积累证据 |
| | | 有矛盾 → 标记"待验证" |
| 证据积累 ≥3 | 层级判断 | L3 → 自动更新 |
| | | L4 → 用户确认 |
| | | L5 → 明确确认 |
| 矛盾检测 | 矛盾类型 | 情境性 → 降级表述 |
| | | 实质性 → 待验证队列 |

**和 RoleX 的本质区别**：

| 维度 | RoleX 反思循环 | 提炼循环 |
|------|---------------|----------|
| **驱动方式** | 错误驱动（偏离基准才反思） | 认知驱动（主动追踪演化） |
| **触发时机** | 行为和预期不符时 | 每次深度对话结束 |
| **关注点** | "哪里出错了" | "认知是否需要进化" |
| **处理矛盾** | 修正偏差 | 标记待验证，不直接覆盖 |
| **更新策略** | 纠正错误 | 渐进式积累+确认 |

**为什么需要独立的反思机制？**

没有反思的认知层是"静态画像"——只是不断叠加记录。有反思的认知层是"动态理解"——不断校准认知的准确性。提炼循环解决的是：认知层不是一次性写完就完事的，它需要一套反思机制来确保"越来越懂你"不是错觉。

---

### 为什么这四个框架能融合？——3D 认知矩阵

三个框架解决的是不同维度的问题，它们**互相正交，不冲突**：

```
                    纵深轴 (五层架构)
                         ↑
                         │
                    L5   │   L4   │   L3   │   L2   │   L1
                    核心层│ 认知层 │ 行为层 │ 情境层 │ 状态层
                         │        │        │        │
            横切轴 ──────┼────────┼────────┼────────┼───────→ PromptX四分类
          (什么类型)     │        │        │        │
                    知识 │  方案  │  经验  │  参考  │
                         │        │        │        │
                         │        │        │        │
                    提炼轴 ──────┼────────┼────────┼────────┼──────→ RoleX循环
                  (多确定)        │        │        │        │
                           Encounter│Reflect│Experi │Realize │
                                    │       │ence   │        │
                         └─────────────────────────────────┘
```

| 轴 | 解决的问题 | 核心问题 |
|----|-----------|----------|
| **纵深轴** | 信息放在哪个深度？ | "多深" |
| **横切轴** | 信息属于什么类型？ | "什么类型" |
| **提炼轴** | 信息的成熟度多高？ | "多确定" |

**一个信息的完整描述**可以是：
- "用户第三次讨论转行" → L2（情境层）+ 经验（PromptX）+ Encounter（RoleX）
- 提炼后 → L3（行为层）+ 经验（PromptX）+ Realize（RoleX）

同一个事件在不同阶段有不同的"坐标"，但它们指向同一个实体。

---

### 知识层 ≠ 认知层

这是两个完全不同的职责域，混淆它们是大多数 Agent 记忆系统失败的原因：

| 维度 | 认知层 (cognitive-memory) | 知识层 (wiki-builder) |
|------|---------------------------|----------------------|
| **管什么** | "你是谁" | "你知道什么" |
| **内容** | 行为模式、价值观、核心特质 | 情境事件、行业知识、技术方案 |
| **变化频率** | 慢，重大变化需用户确认 | 快，随新信息持续更新 |
| **服务对象** | 决定 Agent **怎么跟你说话** | 决定 Agent **能帮你做什么** |
| **提炼程度** | 高，需要多次验证 | 可低，原文存档也可接受 |

**例子**：
- 认知层：`brain/cognition/behavior.md` 记录"用户做决策前需要3天冷静期"
- 知识层：`brain/wiki/entries/L2-2024-015.md` 记录"1月15日讨论了保险，对话记录如下..."

两个模块独立维护，但协同工作：认知层引用知识层的事件来提炼规律，知识层通过认知层的过滤器来决定哪些信息值得深度加工。

---

### 设计目的

**核心目标：打造可移植的 Agent 大脑**

> 你换了一个 Agent，不需要重新自我介绍。
> 你换了一个模型平台，记忆不绑定任何厂商。

这不只是技术方案，更是一种身份哲学：

```
┌─────────────────────────────────────────────────────────────┐
│                                                              │
│   今天用扣子处理工作 ──┐                                     │
│                        │    同一套记忆   同一个"你"          │
│   明天用 Claude Code ──┼──► 跨平台共享  跨Agent同步          │
│                        │                                     │
│   后天用 Cursor ───────┘                                     │
│                                                              │
│   记忆不绑定平台 ──"谁家模型好用迁谁家"                      │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**Git 同步是实现手段，不是目的。** 最终形态是：你有一个独立于任何 Agent 的"数字身份"，所有 Agent 都只是这个身份的接口。

---

### 适配原则

如果你要在自己的 Agent 环境实现这套方案，请记住每个框架的**核心意图**，不要死板复刻实现细节：

| 框架 | 核心意图 | 可裁剪的细节 |
|------|----------|--------------|
| **五层架构** | "区分信息深度，不同深度不同管理策略" | 没有语义搜索的平台可以跳过 L1 检索优化；只有文本文件的平台可以用文件名约定替代结构化存储 |
| **PromptX 四分类** | "区分记忆类型，不同类型不同生命周期" | 不一定要用 SQLite，向量数据库可以用 Markdown 替代；核心是分类逻辑，不是存储技术 |
| **RoleX 循环** | "渐进式验证，观察→验证→确认的证据积累" | 不一定要四阶段名称，但必须有"观察→验证→确认"的机制；Heartbeat 可以换成 Cron 或手动触发 |
| **LLM Wiki** | "原始资料和提炼知识分离，服务对象不同" | 不一定要用 Obsidian，但一定要有两层；Wiki 层给 Agent 看，Raw 层给人存档 |

**最低保障**：如果你只能实现一个能力，请实现**提炼机制**——把原始对话提炼成结构化结论，比存一堆对话日志有价值一百倍。

---

## 核心机制 [v2.2新增]

v2.2 引入两个互补的核心机制：**WAL 协议** 解决"长会话 / Context 压缩丢信息"，**两阶段工作流** 解决"新信号直接占 L3/L4 槽位"。两者都遵循"先粗后精、留有缓冲"的思想，是 agent-brain 应对日常使用痛点的关键升级。

> 这两个机制是**正交的**——WAL 管"会话内怎么不丢东西"，两阶段管"新信号怎么不污染认知层"。可独立启用、独立关闭。

---

### 核心机制 1：WAL 协议（Write-Ahead Log，先写后回）

#### 为什么需要 WAL

Agent 长期使用中三个最常见的"丢信息"场景：

| 场景 | 后果 |
|------|------|
| **Context 压缩** | 早期的关键决策、用户偏好被压缩掉，Agent "忘了"自己答应过什么 |
| **跨多次请求** | 长任务分多次执行时，上下文不连贯 |
| **会话后期回看** | 无法快速回到早期的某个承诺或决定 |

WAL 协议是**数据库领域经典模式**的 Agent 化变体——事务先写日志再执行，所有关键状态变更都有迹可循。落到 agent-brain 上就是：**先写 SESSION-STATE.md，再回复用户**。

#### SESSION-STATE.md 是什么

`brain/SESSION-STATE.md` 是**本次会话的工作区文件**：

- **会话开始时**：新建（或从上次恢复）
- **会话进行中**：每次重大决策 / 新信息 → **先** append 到 SESSION-STATE.md，**再**继续对话
- **会话结束时**：关键内容 → 提交到 `pending/` 或 `cognition/`，SESSION-STATE.md 清空或归档

**关键属性**：
- ⚡ **可丢弃**——SESSION-STATE.md 是工作区，不是最终沉淀
- 🔄 **可恢复**——下次会话开始时读它，能立刻回到上次状态
- 📝 **追加即可**——不需要精心结构，"时间 + 事件" 流水账足够
- 🚫 **不替代正式记忆**——只作为缓冲，重要内容必须 promote 到 cognition/

#### 写入顺序硬性约束

```text
会话开始：
  1. git pull
  2. 读取 brain/index.md（用户核心认知）
  3. 检查/创建 brain/SESSION-STATE.md（本次会话工作区）
  4. 再开始回复用户 ← 关键！不能跳过

会话进行中：
  - 重大决策 / 新信息 / 关键引用 → 立即 append 到 SESSION-STATE.md
  - 不依赖"我会记住"

会话结束：
  - SESSION-STATE.md 关键内容 → pending/ 或 cognition/
  - SESSION-STATE.md 清空（或保留为最近一次的工作区）
  - git commit
```

#### 写入什么 / 不写什么

| 类别 | 写 SESSION-STATE.md？ | 原因 |
|------|---------------------|------|
| 用户明确表达的新偏好 | ✅ 必须 | 后续会话要用 |
| 重大决策（选了 A 不选 B） | ✅ 必须 | 后续解释/回看 |
| 任务进度（"已完成 1/3"） | ✅ 必须 | 跨请求续接 |
| 关键引用 / 文件路径 | ✅ 必须 | 长会话后期找不到 |
| 临时调试信息 | ⚪ 可选 | 可能有用 |
| 闲聊/寒暄 | ❌ 不写 | 噪音 |
| 已完整记录的写入操作 | ❌ 不写 | log.md 已经有了 |

#### 写多长合适

**单条 append 控制在 1-3 行**。示例：

```markdown
## 14:32 [user] "我比较喜欢简短的回答，能加粗的地方加粗就行"
## 14:35 [decision] 输出格式定为：短答案+关键加粗，不堆细节
## 14:40 [task-progress] v2.2-minor 升级，已完成 Step 1-3
## 15:10 [ref] 关键设计稿 = david-brain/06-已沉淀/2026-06-07_v2.2-minor升级设计稿_WAL+两阶段.md
```

**不要写成完美结构**——这是工作区，**有 > 完美**。

#### 故障恢复场景

| 场景 | 怎么恢复 |
|------|----------|
| Context 压缩后丢失 | 读 SESSION-STATE.md，从最近决策点继续 |
| 用户突然说"你忘了我说过 X" | 翻 SESSION-STATE.md，定位到 X 的原始记录 |
| Agent 误删 / 改错了什么 | SESSION-STATE.md 里有"已决定 Y"的痕迹 |
| 多设备切换 | git pull 后 SESSION-STATE.md 是最新的工作区 |

#### WAL 的局限

- SESSION-STATE.md **不参与反幻觉校验**——它是工作区，不是认知层
- 不替代 `pending.md`（矛盾/待验证队列）——两者职责不同
- 不替代 `log.md`（操作日志）——SESSION-STATE.md 是会话内，log.md 是跨会话审计
- 不是所有 Agent 平台都支持文件读写——无文件能力的平台可降级为对话内 SESSION-STATE 提示

---

### 核心机制 2：先毛坯后蒸馏（两阶段工作流）

#### 为什么需要两阶段

v2.1 的"问题"：新信号进来时，Agent 直接写入 `cognition/` 某个层级（L3/L4），**一次观察就占了一个认知槽位**。结果：

- 真实信号和噪声信号混在一起
- L3 行为层塞满了一次性表达
- 评测员反馈"模板空壳、缺案例"——本应该是案例池的 cognition/ 反而是空壳

**两阶段工作流的解法**：新信号先入"毛坯区"（`pending/`），**不直接进 cognition/**；每日/定期 lint 时再 promote/demote。

#### pending/ 目录是什么

```
brain/pending/                       # 🆕 v2.2 毛坯区
├── README.md                        # 本说明文件
├── 2026-06-07-决策冷静期.md         # 按日/主题归类
├── 2026-06-07-周日推送偏好.md
└── ...
```

**每个文件 = 一个待验证主题**，包含：

- 原始观察（用户原话 / 行为记录）
- 初步假设（可能是什么模式）
- 证据计数（当前 1/3、2/3...）
- 关联引用（涉及哪些已有认知）

#### 与 pending.md 的区别

`brain/pending.md`（v2.1 已有）= **矛盾 / 待验证的 L2+ 队列**（Agent 已决定"这是认知层候选"但证据不足）

`brain/pending/`（v2.2 新增）= **所有新信号的毛坯区**（包括"可能不是认知层、但先记下再说"）

| 维度 | pending.md（v2.1） | pending/ 目录（v2.2） |
|------|---------------------|----------------------|
| 角色 | 已认定为候选，需补充证据 | 全部新信号的暂存区 |
| 存储 | 单文件 Markdown 列表 | 每主题一文件 |
| 来源 | cognitive-memory 的 on_observation | 任何新信号（用户表达/Agent 观察/外部输入） |
| 出口 | 证据达标 → promote 到 cognition | 主题明确 → 转 pending.md；不重要 → 归档 |

#### 两阶段流程

```text
【阶段 1：捕获（实时）】
  新信号进来
    ↓
  写到 brain/pending/{日期-主题}.md（毛坯）
    ↓
  证据计数 +1（同一主题再次出现）

【阶段 2：蒸馏（lint 时）】
  governance.lint() 扫描 pending/
    ↓
  判定 1：3 次相似观察 + 无反例 → promote 到 pending.md（待验证队列）
  判定 2：明确主题 + 用户确认 → 直接 promote 到 cognition/behavior.md
  判定 3：反例出现 / 长期无进展 → demote 或归档
  判定 4：一次性表达 / 噪声 → 归档，删 pending/ 文件
```

#### 写入 pending/ 的最小规范

```markdown
---
topic: 决策冷静期
date: 2026-06-07
evidence_count: 1/3
related: [behavior.md#决策风格]
status: 毛坯
---

## 观察记录

- **时间**: 2026-06-07 14:30
- **情境**: 讨论保险方案
- **原话**: "我需要再想想"
- **后续**: 用户 2 天后才回来决定

## 初步假设

用户做决策前需要冷静期，不是真的"想想"

## 待验证

- 是否每次重大决策都需要？
- 冷静期一般是几天？
```

**注意**：这就是一个 v2.2 的"案例池"——pending/ 文件本身就是评测员要的"真实新信号展示区"。

#### 手动 promote / demote

如果用户**明确确认**或**明确否定**某条观察，可以跳过 lint 等待：

```text
# 显式 promote（用户说"对，我就是这样"）
governance.promote(pending/2026-06-07-决策冷静期.md, target="L3")
  → 写入 behavior.md
  → 记录到 log.md
  → 归档 pending/ 文件

# 显式 demote（用户说"不是这样"）
governance.demote(pending/2026-06-07-决策冷静期.md, reason="用户否认")
  → 标注为"已否定"
  → 归档
```

#### 两阶段的配置开关

如果你的 Agent 习惯直接写 cognition/，可以**关闭两阶段**（保持 v2.1 行为）：

```json
{
  "modules": {
    "cognitive_memory": {
      "two_stage_workflow": {
        "enabled": true,        // false = 直接写 cognition/（v2.1 行为）
        "evidence_threshold": 3, // 几次观察后 promote
        "auto_lint": "daily"     // 何时扫描 pending/
      }
    }
  }
}
```

> 默认开启两阶段。如果你的 Agent 已经有成熟的提炼流程，可关闭。

---

### 两个机制如何协同

```text
会话开始：
  on_session_start 触发
    ↓
  1. 读 SESSION-STATE.md（WAL 恢复）    ← 核心机制 1
  2. 读 brain/index.md
    ↓
  开始回复用户

会话进行中：
  每次新信号：
    ↓
  1. 先 append 到 SESSION-STATE.md        ← WAL
  2. 写到 pending/{日期-主题}.md          ← 两阶段
  3. 再继续对话

会话结束：
  1. SESSION-STATE.md 关键内容 → pending/ 或 cognition/  ← 双向收尾
  2. governance.lint() 扫描 pending/  ← 两阶段蒸馏
  3. git commit
```

**关键洞察**：WAL 是**会话内**的"工作区 + 恢复层"；两阶段是**会话间**的"暂存 + 蒸馏"。前者保证"会话内不丢"，后者保证"会话间不污染"。

---

## 架构总览

```
┌─────────────────────────────────────────────────────────────┐
│                      Agent Brain v2                         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │cognitive-    │    │  wiki-       │    │  git-sync    │  │
│  │memory        │◄──►│  builder     │◄──►│              │  │
│  │(认知层)      │    │  (知识层)    │    │  (同步层)    │  │
│  └──────┬───────┘    └──────────────┘    └──────────────┘  │
│         │                                                   │
│  ┌──────┴───────┐    ┌──────────────┐    ┌──────────────┐  │
│  │memory-       │    │  ingest      │    │retrieval-    │  │
│  │governance    │    │  (摄入层)    │    │engine        │  │
│  │(治理层)      │    │              │    │(检索层)      │  │
│  └──────────────┘    └──────────────┘    └──────────────┘  │
│                                                             │
│                    ┌──────────────────┐                    │
│                    │   brain/ 目录     │                    │
│                    │   (记忆数据层)    │                    │
│                    └──────────────────┘                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 六大模块协作关系 [v2更新]

```
ingest (摄入) → wiki-builder (知识沉淀) → cognitive-memory (认知提炼) → git-sync (同步共享)
     │                  │                      │                      │
     ▼                  ▼                      ▼                      ▼
 原始文档 →         Wiki条目 →             认知沉淀 →            跨平台共享
 Markdown切片       知识积累              行为/价值观模式

                     ┌──────────────────────┐
                     │                      │
              ┌──────┴──────┐        ┌──────┴──────┐
              │             │        │             │
       retrieval-engine  memory-governance
         (怎么取)          (怎么保证对)
```

**v2 新增两个横切模块**：
- **retrieval-engine**：三路融合检索 + Token 预算，解决"怎么取"的问题
- **memory-governance**：反幻觉 + 证据积累 + Lint，解决"怎么保证对"的问题

### 提炼循环：认知层的反思引擎

提炼循环嵌入 cognitive-memory 模块，作为认知层的"反思引擎"：

```
                    ┌─────────────────────────────────────────┐
                    │         每次深度对话结束                │
                    └─────────────────────────────────────────┘
                                        │
                                        ▼
                    ┌─────────────────────────────────────────┐
                    │   ① 新信号检测                          │
                    │   这次对话有新的认知信号吗？             │
                    └─────────────────────────────────────────┘
                                        │
                         ┌──────────────┴──────────────┐
                         │                              │
                         ▼                              ▼
                    有新信号                        无新信号
                         │                              │
                         ▼                              ▼
         ┌───────────────────────────────┐      结束（不触发更新）
         │ ② 矛盾检测                    │
         │ 和现有认知有冲突吗？           │
         └───────────────────────────────┘
                         │
              ┌──────────┴──────────┐
              │                     │
              ▼                     ▼
         有矛盾                  无矛盾
              │                     │
              ▼                     ▼
        标记"待验证"           ③ 证据积累
              │                     │
              │                     ▼
              │         ┌─────────────────────────────┐
              │         │ 证据数 ≥ 3？               │
              │         └─────────────────────────────┘
              │                    │
              │         ┌─────────┴─────────┐
              │         │                   │
              │         ▼                   ▼
              │       是                   否
              │         │                   │
              │         ▼                   ▼
              │    ④ 更新决策          继续积累
              │         │
              │    ┌────┴────┬──────┐
              │    │         │      │
              │    ▼         ▼      ▼
              │   L3        L4     L5
              │  自动更新   用户确认 明确确认
              │
         矛盾处理策略:
         • 情境性矛盾 → 降级表述，添加情境限定
         • 实质性矛盾 → 进入待验证队列，不覆盖旧认知
         • 反例出现   → 立即降级，标记存疑
```

### 三层协作示例

**场景：用户第三次讨论转行**

```
认知层(cognitive-memory)
  │
  ├── 读 index.md → 发现"职业倦怠"标签有过2次提及
  │
  ▼
知识层(wiki-builder)
  │
  ├── 检索相关情境事件 L2-2024-012、L2-2024-008
  ├── 读取详细内容，发现共性：都在年末、都伴随"没意思"的表达
  │
  ▼
提炼结论
  │
  ├── 行为模式：年末周期性职业倦怠，非真实转行意图
  ├── 建议：不要认真对待当前表达，等待情绪周期过去
  └── 写入 pending.md 标记"待第3次验证"
```

**场景：新 Agent 首次启动**

```
git-sync
  │
  ├── 从 GitHub pull 最新记忆
  │
  ▼
认知层
  │
  ├── 读 index.md（<500字，快速建立基本认知）
  ├── 按需加载 behavior.md / cognition.md
  │
  ▼
知识层
  │
  └── 按需检索 wiki/entries/
```

---

## 文件结构

```
agent-brain/
├── SKILL.md                     ← 本文件，哲学+架构+配置
├── modules/
│   ├── cognitive-memory/        ← 认知记忆模块
│   │   ├── SKILL.md
│   │   ├── references/
│   │   │   ├── five-layer-framework.md  # 五层架构详解
│   │   │   ├── promptx-engram.md        # 四象限分类参考
│   │   │   └── rolex-cycle.md           # 认知循环参考
│   │   └── templates/
│   │       ├── index-template.md
│   │       └── memory-entry.md
│   ├── wiki-builder/            ← 知识库模块
│   │   └── SKILL.md
│   ├── git-sync/                ← 同步模块
│   │   └── SKILL.md
│   ├── ingest/                  ← 摄入模块（MinerU管线）
│   │   └── SKILL.md
│   ├── retrieval-engine/        ← [v2新增] 检索引擎模块
│   │   ├── SKILL.md
│   │   └── templates/
│   │       └── retrieval-strategy.md  # 检索策略模板
│   └── memory-governance/       ← [v2新增] 记忆治理模块
│       ├── SKILL.md
│       └── templates/
│           └── governance-config.md   # 治理配置模板
├── config/
│   └── config.template.json      ← 配置模板
└── brain/                       ← 记忆数据目录（Git管理）
    ├── index.md                 ← 主索引（始终加载）
    ├── cognition/
    │   ├── behavior.md          ← L3 行为模式
    │   ├── cognition.md         ← L4 价值观认知
    │   └── core.md              ← L5 核心特质
    ├── wiki/
    │   ├── index.md             ← 情境索引
    │   └── entries/             ← 情境详情
    ├── boundary.md              ← 边界设定
    ├── pending.md               ← 待验证队列
    ├── log.md                   ← 操作日志
    └── archive/                 ← [v2新增] 归档区
        ├── by-layer/            # 按层级归档
        └── by-date/             # 按时间归档
```

---

## 六大模块职责

### cognitive-memory（认知层）

**职责**：管"你是谁"——用户的身份特征、行为模式、价值观、核心特质。

**核心能力**：
- 五层架构分类（L1~L5）
- PromptX 横切分类（知识/方案/经验/参考）
- RoleX 提炼循环（Encounter → Reflect → Experience → Realize）
- 反幻觉校验
- [v2新增] 6个生命周期 Hook（自动捕获+压缩）
- [v2新增] 4层压缩管线（L0→L1→L2→L3+）
- [v2新增] Token 预算管理

**存储**：`brain/cognition/` + `brain/boundary.md`

**始终加载**：`brain/index.md`（<500字）

---

### wiki-builder（知识层）

**职责**：管"你知道什么"——情境事件、知识条目、交叉引用。

**核心能力**：
- 持久化知识积累
- 交叉引用建立
- 矛盾检测与标注
- 定期 Lint 健康检查

**存储**：`brain/wiki/`

**触发**：ingest 新资料、查询已有知识、矛盾发现时

---

### git-sync（同步层）

**职责**：管"怎么共享"——跨 Agent、跨设备同步记忆。

**核心能力**：
- GitHub 仓库读写
- Obsidian 本地预览
- 分层写入减少冲突
- 冲突自动标记

**存储**：`brain/`（整体 Git 管理）

**触发**：会话开始（pull）、会话结束（push）、定时同步

---

### ingest（摄入层）

**职责**：管"怎么入库"——将PDF/DOCX等原始文档转换为AI友好的Markdown知识。

**核心理念**：富文本格式在AI眼里就是"满身泥的萝卜"，需要先洗干净再喂给AI。

**核心能力**：
- MinerU提取（PDF/DOCX → Markdown + JSON + 图片）
- AI校正（修正OCR错误，修复格式错乱）
- 智能切片（按文档类型选择不同粒度）
- 三种运行模式（云端/ API/ 本地）

**存储**：`brain/01-知识库/`（归档后的切片文档）

**触发**：用户提交新文档、批量导入资料、文档入库请求

**与wiki-builder的协作**：
- ingest负责"怎么把文档变成Markdown"
- wiki-builder负责"怎么把Markdown变成知识"

---

### retrieval-engine（检索层）[v2新增]

**职责**：管"怎么取"——混合检索引擎，让记忆取得到、取得准、取得起。

**核心能力**：
- 三路融合检索：关键词（BM25风格）+ 语义（向量）+ 关联（图谱）
- RRF 融合排序：三路结果按 Reciprocal Rank Fusion 合并
- Token 预算管理：每次检索有 token 上限，先返回摘要再按需加载
- 检索策略表：6种查询类型自动匹配最优检索路径
- 平台适配层：扣子/Claude Code/Cursor/通用自动适配

**存储**：无独立存储，读取 `brain/` 目录下的所有文件

**触发**：任何需要检索记忆的场景

**与cognitive-memory的协作**：
- 新信号检测 → 检索相关已有认知，提供比对基础
- 矛盾检测 → 三路检索相关认知，确保不遗漏
- 证据积累 → 关联检索：找所有相关观察
- Lint 检查 → 关联检索：验证引用完整性

---

### memory-governance（治理层）[v2新增]

**职责**：管"怎么保证对"——反幻觉 + 证据积累 + Lint，可独立接入任何记忆系统的质量守门员。

**核心能力**：
- 治理 API：validate() / promote() / demote() / archive() / lint()
- 治理策略配置：证据阈值、确认要求、矛盾策略、衰减规则
- 治理仪表盘：记忆总量/分布、待验证/矛盾/孤儿统计、7天趋势
- 作为插件接入：AgentMemory / Mem0 / 纯文件系统均可接入
- 质量控制可复用：任何记忆系统都可以使用反幻觉四规则

**存储**：`brain/archive/`（归档区）+ `brain/governance-dashboard.md`（仪表盘）

**触发**：写入前（validate）、提升时（promote）、会话结束（lint_quick）、定期（lint_deep）

**与cognitive-memory的协作**：
- 写入前钩子：governance.validate() 校验后决定写入/拒绝/待验证
- 提升前钩子：governance.promote() 检查证据/冲突/权限
- 会话结束钩子：governance.lint(scope="recent") 快速检查
- 定期检查钩子：governance.lint(scope="all") 深度检查

---

## 环境适配指南

不同 Agent 环境实现 agent-brain 能力的替代方案：

| 能力 | 扣子Agent | Claude Code | Cursor/CodeBuddy | 通用方案 |
|------|-----------|-------------|-------------------|----------|
| **文件读写** | `edit_file`/`read_file` | 直接文件操作 | 直接文件操作 | 按平台API |
| **语义搜索** | `memory_search` | 内置语义搜索 | grep/ripgrep | 按平台能力 |
| **关键词搜索** | `read_file`+匹配 | ripgrep | 内置搜索 | 文件遍历 |
| **关联检索** | index.md交叉引用 | ripgrep+引用跳转 | Markdown链接 | 解析关联字段 |
| **定时任务** | Calendar+Heartbeat | cron/systemd | 无（需手动） | 按平台能力 |
| **Git操作** | `bash`（需云电脑） | 直接终端 | 内置终端 | 按平台能力 |
| **子任务** | `sessions_spawn` | 子进程 | 无 | 按平台能力 |
| **Obsidian预览** | 不支持 | 需本地运行 | 需本地运行 | 需本地运行 |
| **MinerU提取** | 云电脑CLI / API模式 | 本地CLI | 本地CLI | 按算力选择 |
| **治理校验** | 对话中Prompt | 脚本+LLM | 内联校验 | Prompt模板 |

### 各环境适配要点

**扣子Agent**
- 主存储：Coze 内置 `memory_search` + 文件操作
- Git 同步：需要云电脑或通过 API 调用
- Obsidian：仅预览，不支持本地编辑
- MinerU：
  - 云电脑模式：`mineru -p xxx.pdf -o ./output -b pipeline`（CLI已预装）
  - API模式：调用 mineru.net 在线API（需要API Key）

**Claude Code**
- 完整支持所有功能
- `.claude/` 可作为 brain/ 的符号链接或直接使用
- 内置 `Read`/`Write` 命令比 Coze 更灵活
- MinerU：本地CLI执行，配合项目目录使用

**Cursor/CodeBuddy**
- 内置终端支持完整 Git 操作
- 可直接编辑 `brain/` 目录
- Obsidian 插件可无缝集成
- MinerU：本地CLI执行，配合Obsidian使用

**其他Agent**
- 核心依赖：文件读写 + Git
- 可选增强：语义搜索、定时任务
- 最低保障：纯文本文件 + Git push/pull

---

## 配置模板

创建 `config/config.json`（或通过环境变量覆盖）：

```json
{
  "brain": {
    "path": "./brain",
    "index_file": "index.md"
  },
  "git": {
    "repo": "https://github.com/USER/agent-brain.git",
    "branch": "main",
    "author": {
      "name": "Agent Name",
      "email": "agent@brain.local"
    },
    "sync_on_start": true,
    "sync_on_end": true,
    "auto_push_interval": 3600
  },
  "obsidian": {
    "vault_path": "~/Obsidian/agent-brain",
    "enable_preview": true
  },
  "modules": {
    "cognitive_memory": {
      "enabled": true,
      "max_index_lines": 500,
      "require_user_confirm_for_l4": true
    },
    "wiki_builder": {
      "enabled": true,
      "auto_ingest": false,
      "lint_interval": 86400
    },
    "git_sync": {
      "enabled": true,
      "conflict_strategy": "mark_and_keep_both"
    },
    "ingest": {
      "enabled": true,
      "mode": "cli",
      "output_base": "./brain/01-知识库",
      "correction": {
        "enabled": true,
        "model": "auto"
      },
      "slicing": {
        "auto": true,
        "strategy": "auto"
      }
    },
    "retrieval_engine": {
      "enabled": true,
      "rrf_k": 60,
      "token_budget": {
        "retrieval_default": 2000,
        "file_index": 500,
        "file_behavior": 2000,
        "file_cognition": 1500,
        "file_core": 800
      },
      "strategy": {
        "default": "semantic",
        "fallback": "keyword"
      }
    },
    "memory_governance": {
      "enabled": true,
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
      }
    }
  },
  "agent": {
    "id": "coze-agent-david",
    "name": "扣子助手",
    "capabilities": ["file_ops", "git", "schedule", "semantic_search"]
  }
}
```

---

## 首次安装流程

### Step 1: 初始化 brain/ 目录

```bash
# 克隆或创建 brain 目录
git clone https://github.com/USER/agent-brain.git ./brain

# 或初始化空目录（如果是全新开始）
mkdir -p brain/{cognition,wiki/entries}
```

### Step 2: 创建基础文件

按照 `brain/` 目录结构创建以下文件：

**brain/index.md**
```markdown
# 认知记忆索引

## 文件位置
- L3-L5: `brain/cognition/`
- L2情境: `brain/wiki/index.md`
- 边界: `brain/boundary.md`

## 统计
- 最后更新: YYYY-MM-DD
- 条目总数: 0
```

**brain/cognition/behavior.md**
```markdown
# L3 行为模式

## 待填充
```

**brain/cognition/cognition.md**
```markdown
# L4 价值观认知

## 待填充
```

**brain/cognition/core.md**
```markdown
# L5 核心特质

## 待填充
```

### Step 3: 配置 GitHub 同步

```bash
cd brain
git remote add origin https://github.com/USER/agent-brain.git
git push -u origin main
```

### Step 4: 首次认知初始化

与用户对话，收集基本信息：

- **基本信息**：姓名、职业、当前项目
- **沟通偏好**：喜欢简洁还是详细、喜欢什么沟通风格
- **核心目标**：长期目标是什么、在意什么
- **边界设定**：什么话题敏感、什么雷区不能踩

写入 `brain/cognition/core.md` 和 `brain/boundary.md`。

### Step 5: 验证同步

```bash
# 在另一个 Agent 环境中
git clone https://github.com/USER/agent-brain.git
cat brain/index.md
```

确认两个环境看到相同的记忆。

---

## 日常使用流程

### 会话开始

```bash
# 1. 拉取最新记忆
git pull origin main

# 2. 读取主索引
cat brain/index.md

# 3. 根据需要加载详细文件
# - 涉及用户偏好 → brain/cognition/behavior.md
# - 涉及价值观判断 → brain/cognition/cognition.md
# - 涉及核心身份 → brain/cognition/core.md
# - 涉及具体事件 → brain/wiki/index.md → brain/wiki/entries/
```

### 会话中

遇到值得记忆的信息时：

```
1. encounter：原始观察记录
   → 写入 pending.md（如果待验证）
   → 写入 wiki/entries/L2-YYYY-XXX.md（如果是有意义的事件）

2. reflect：判断是否需要提炼
   - 同一模式出现 ≥3 次？
   - 能帮助未来更好地服务用户？

3. experience：实践中验证假设

4. realize：提炼写入认知层
   - L3 behavior.md（行为模式）
   - L4 cognition.md（价值观认知）
   - L5 core.md（核心特质，仅用户确认后）
```

### 会话结束

```bash
# 1. 更新索引（如果有新内容）
# - brain/index.md
# - brain/wiki/index.md

# 2. 追加日志
echo "## [日期] [Agent] [会话摘要]" >> brain/log.md

# 3. 推送更新
git add .
git commit -m "Update: [简短描述]"
git push origin main
```

### 定期任务

**Heartbeat（轻量扫描）**
- 检查 pending.md 中的待验证项
- 判断是否积累足够证据可提炼

**Calendar（深度提炼）**
- 执行 wiki-builder 的 Lint 检查
- 识别矛盾、过时内容
- 更新交叉引用

---

## 冲突解决策略

### 分层写入减少冲突

| 文件 | 写入频率 | 冲突风险 | 策略 |
|------|----------|----------|------|
| `index.md` | 低 | 中 | 合并更新 |
| `cognition/*.md` | 低 | 低 | 用户确认后写入 |
| `wiki/entries/*.md` | 中 | 中 | 按主题分区 |
| `pending.md` | 高 | 低 | 仅追加 |
| `log.md` | 高 | 低 | 仅追加 |

### 冲突处理流程

```bash
# 1. 检测冲突
git pull
# → CONFLICT: brain/cognition/behavior.md

# 2. 保留两份
git checkout --ours brain/cognition/behavior.md
git show :2:brain/cognition/behavior.md > brain/cognition/behavior.md.local
git show :3:brain/cognition/behavior.md > brain/cognition/behavior.md.remote

# 3. 标记待确认
echo "## 冲突待确认 - [日期]" >> brain/pending.md
echo "- ours: behavior.md.local" >> brain/pending.md
echo "- remote: behavior.md.remote" >> brain/pending.md

# 4. 提交
git add .
git commit -m "Merge with conflicts marked"
git push
```

---

## 关键设计原则

### 反幻觉校验四规则

记忆不是越多越好，错误的记忆比没有记忆更危险。

| 规则 | 说明 | 检查方法 |
|------|------|----------|
| **小样本校验** | 单次观察不能形成结论 | 需要 ≥3 次独立观察 |
| **情境依赖校验** | 判断需标注使用场景 | "在 X 情境下倾向于 Y" |
| **言行一致校验** | 说的和做的是否一致 | 对照行为记录验证 |
| **可逆表述校验** | 结论应该是可修正的 | "目前倾向于..."而非"用户就是..." |

### 提炼证据链

```
原始观察（Encounter）
    ↓ [第1次]
假设（待验证）
    ↓ [第2次相同模式]
假设强化
    ↓ [第3次相同模式]
初步结论 → 可写入 L3 behavior.md
    ↓ [跨情境验证]
L4/L5 认知 → 需用户确认
```

### 分层权限

| 层级 | 写入权限 | 确认要求 |
|------|----------|----------|
| L1 State | 无（不持久化） | N/A |
| L2 Situation | Agent 直接写入 | 可选确认 |
| L3 Behavior | Agent 提炼写入 | 推荐确认 |
| L4 Cognition | Agent 提炼写入 | **必须确认** |
| L5 Core | Agent 提炼写入 | **必须确认** |

### 知识库人工参与

wiki-builder 的 ingest 流程必须有人工参与：

```
Raw Source 进入
    ↓
LLM 读取并分析
    ↓
与用户确认关键要点（逐条讨论）
    ↓
写入 wiki 页面
    ↓
更新 index
    ↓
记录 log
```

不允许全自动 ingest——用户必须对知识库内容有最终知情权。

---

## 敏感信息隔离

### 设计原则

brain/ 目录设计为可公开分享，但敏感信息必须隔离存放。

### 文件隔离策略

| 文件类型 | 存放位置 | Git管理 | 说明 |
|----------|----------|---------|------|
| 真实配置 | `config/config.json` | ❌ 不纳入 | GitHub Token、API Key等 |
| 配置模板 | `config/config.template.json` | ✅ 可纳入 | 仅含占位符 |
| 认知层 | `brain/00-共享认知/` | ✅ 可纳入 | 不含敏感个人信息 |
| 原始资料 | `brain/02-原始资料/` | ❌ 不纳入 | PDF/PPTX等大文件 |
| 原始资料 | `brain/02-原始资料/` | ❌ 不纳入 | 敏感文档 |

### config.json 结构

```json
{
  "sensitive": {
    "github_token": "ghp_xxxxxxxxxxxx",
    "api_keys": ["key1", "key2"]
  },
  "git": {
    "repo": "https://github.com/USERNAME/agent-brain.git",
    "author": {
      "name": "Agent Name",
      "email": "agent@brain.local"
    }
  },
  "obsidian": {
    "vault_path": "~/Obsidian/brain"
  }
}
```

### Agent读取配置的优先级

```
环境变量 > config.json > 默认值
```

Agent应优先从环境变量读取敏感信息，其次读取config.json，最后使用默认值。

---

## 首次配置引导

### 引导流程

Agent应在用户首次使用agent-brain时，主动引导完成以下配置：

### Step 1: 创建GitHub仓库

**Agent询问**：
> "你需要为brain记忆库创建一个GitHub仓库。请告诉我你的GitHub用户名，我来帮你生成仓库地址。"

**执行**：
- 用户在GitHub创建空仓库 `agent-brain`
- 或Agent提供命令引导用户执行

### Step 2: 生成Personal Access Token

**Agent询问**：
> "为了让我能够推送代码到你的GitHub仓库，你需要生成一个Personal Access Token。你有现成的Token吗？如果没有，我教你生成：Settings → Developer settings → Personal access tokens → Generate new token"

**注意事项**：
- Token需要有 `repo` 权限
- 建议设置过期时间
- Token只显示一次，请妥善保存

### Step 3: 配置config.json

**Agent询问**：
> "请告诉我以下信息，我会帮你创建配置文件："

- 你的GitHub用户名是什么？
- 你想把知识库放在本地的哪个目录？（例如：`~/Obsidian/brain`）
- 你想用哪个Agent身份来使用这套记忆系统？（例如：小扣、CodeBuddy）

**Agent执行**：
```bash
cp config/config.template.json config/config.json
# 填充用户提供的真实信息
```

### Step 4: 设置Obsidian本地目录（可选）

**Agent询问**：
> "你想把Obsidian本地知识库放在哪里？如果暂时不需要本地同步，可以跳过这步。"

### Step 5: 首次Git同步

**Agent执行**：
```bash
cd brain
git init
git remote add origin https://github.com/USERNAME/agent-brain.git
git add .
git commit -m "Initial commit: agent-brain structure"
git push -u origin main
```

### Step 6: 认知初始化

**Agent询问**：
> "为了让我更懂你，需要进行认知初始化。请回答以下问题（或让我从对话中自己观察）："

- **基本信息**：姓名、常用称呼、职业
- **沟通偏好**：喜欢简洁还是详细、喜欢什么风格
- **核心目标**：当前最想解决的问题是什么
- **边界设定**：有什么雷区需要注意

**Agent执行**：
- 根据用户回答，填充 `brain/00-共享认知/` 下的文件
- L4/L5 内容需用户确认后再写入

---

## Agent自我适配

### 适配流程

每个Agent接入agent-brain时，应按以下顺序执行：

```
1. 读 index.md → 了解全局结构
2. 读 config.json → 了解自己的身份配置
3. 扫描 03-Agent空间/ → 了解其他Agent的工作状态
4. 在 03/下创建自己的工作目录 → 建立Agent身份
```

### Agent身份建立

```markdown
brain/03-Agent空间/[Agent名称]/
├── 当前任务.md      # 当前正在处理的任务
├── 对话摘要/        # 历史对话摘要
└── 状态.md          # Agent状态标记
```

### 环境适配表

| 能力 | 扣子Agent | Claude Code | Cursor | 其他Agent |
|------|-----------|-------------|--------|-----------|
| **文件读写** | edit_file/read_file | 直接文件操作 | 直接文件操作 | 按平台能力 |
| **Git操作** | bash（需云电脑） | 直接终端 | 内置终端 | 按平台能力 |
| **Obsidian预览** | 不支持本地 | 需本地运行 | Obsidian插件 | 需本地运行 |
| **语义搜索** | memory_search | 内置语义搜索 | grep/ripgrep | 按平台能力 |
| **敏感信息** | 环境变量注入 | .env文件 | .env文件 | 按平台能力 |

### 能力选择策略

Agent应根据自身能力选择适配方案：

1. **完整能力**：本地运行 + Git + Obsidian
   - Claude Code / Cursor / 本地Agent
   - 可使用全部功能

2. **中等能力**：Git + 文件操作
   - 扣子 + 云电脑
   - Git同步通过bash实现

3. **基础能力**：仅文件操作
   - 纯扣子Agent
   - Git同步依赖外部触发

### 跨Agent协作

Agent之间通过 `03-Agent空间/` 目录协作：

```
brain/03-Agent空间/
├── 小扣/              # 扣子Agent
│   ├── 当前任务.md    # 小扣正在做什么
│   └── 对话摘要/      # 小扣的对话记录
├── CodeBuddy/         # Claude Code
│   └── 当前任务.md
└── Kimi/              # Kimi
    └── 当前任务.md
```

**协作规则**：
- 开始新任务前，检查其他Agent的"当前任务.md"
- 避免重复工作
- 重要结论写入共享认知层，供所有Agent参考

---

## 公开分享考虑

### 可分享内容

以下内容可以纳入Git仓库公开分享：

| 目录/文件 | 内容 | 风险等级 |
|-----------|------|----------|
| `brain/00-共享认知/` | 用户画像、行为模式、价值观 | 🟡 低风险 |
| `brain/01-知识库/` | 知识沉淀、调研报告 | 🟢 无风险 |
| `brain/03-Agent空间/` | Agent工作目录结构 | 🟢 无风险 |
| `brain/04-待处理/` | 待办事项队列 | 🟢 无风险 |
| `brain/index.md` | 目录索引 | 🟢 无风险 |
| `brain/.gitignore` | Git忽略规则 | 🟢 无风险 |

### 不可分享内容

| 目录/文件 | 原因 | 处理方式 |
|-----------|------|----------|
| `config/config.json` | 包含Token/API Key | 已加入.gitignore |
| `02-原始资料/*.pdf` | 可能含敏感信息 | 已加入.gitignore |
| `02-原始资料/assets/` | 大文件 | 已加入.gitignore |

### 公开分享检查清单

分享前请确认：

- [ ] config/config.json 不存在或为空
- [ ] 00-共享认知/ 不含身份证号、银行卡号、密码
- [ ] 原始资料目录不包含敏感PDF
- [ ] .gitignore 已正确配置
- [ ] Token已撤销或未使用真实Token

### 大文件处理建议

对于需要版本控制的大文件（如PSD、Illustrator源文件），建议：

1. **Git LFS**：适合 <2GB 的大文件
   ```bash
   git lfs install
   git lfs track "*.pdf"
   ```

2. **单独云存储**：适合 >2GB 的大文件
   - Google Drive
   - 阿里云OSS
   - Dropbox

---

## References 索引

| 文件 | 何时加载 |
|------|---------|
| `modules/cognitive-memory/references/five-layer-framework.md` | 需要五层架构详解时 |
| `modules/cognitive-memory/references/promptx-engram.md` | 需要四象限分类参考时 |
| `modules/cognitive-memory/references/rolex-cycle.md` | 需要认知循环参考时 |
| `modules/cognitive-memory/templates/index-template.md` | 创建或更新索引时 |
| `modules/cognitive-memory/templates/memory-entry.md` | 创建新记忆条目时 |
| `modules/wiki-builder/SKILL.md` | 需要 wiki 知识库操作时 |
| `modules/git-sync/SKILL.md` | 需要 Git 同步操作时 |
| `modules/ingest/SKILL.md` | 需要文档摄入（MinerU）时 |
| `modules/retrieval-engine/SKILL.md` | [v2新增] 需要记忆检索时 |
| `modules/retrieval-engine/templates/retrieval-strategy.md` | [v2新增] 配置检索策略时 |
| `modules/memory-governance/SKILL.md` | [v2新增] 需要记忆治理/质量控制时 |
| `modules/memory-governance/templates/governance-config.md` | [v2新增] 配置治理策略时 |
| `brain/01-知识库/知识管理/止水老师知识库方法论.md` | 理解切片策略和方法论时 |
| `config/config.template.json` | 配置 agent-brain 时 |

---

*Agent Brain v2.2 — 让记忆成为你的第二大脑*

---

## v1→v2 变更日志 [v2新增]

| 变更 | 说明 | 模块 |
|------|------|------|
| +retrieval-engine | 混合检索引擎，三路融合（关键词+语义+关联）+ RRF排序 + Token预算 | retrieval-engine |
| +memory-governance | 记忆治理插件，反幻觉四规则+证据积累+Lint+仪表盘，可独立接入其他系统 | memory-governance |
| +auto-capture hooks | 6个生命周期Hook：on_session_start / on_new_observation / on_contradiction_detected / on_evidence_accumulated / on_session_end / on_periodic_lint | cognitive-memory |
| +4层压缩管线 | L0原始观察→L1压缩观察→L2跨会话合并→L3+长期认知，Token节约90% | cognitive-memory |
| +token预算 | 每文件上限（index<500, behavior<2000, cognition<1500, core<800）+检索预算2000+超限压缩 | 全局 |
| 架构图更新 | 从4模块扩展到6模块，retrieval-engine和memory-governance作为横切层 | SKILL.md |
| 模块职责更新 | cognitive-memory新增Hook+压缩+Token预算，新增retrieval-engine和memory-governance职责 | SKILL.md |
| 配置扩展 | config.json新增retrieval_engine和memory_governance配置段 | config |

## v2.0→v2.1 变更日志 [v2.1新增]

**设计原则**：取长补短（借鉴 Hindsight/Graphiti/Letta 精华）+ 轻量级（不引入 ML/AI/外部依赖）+ 工程化优先。

| 变更 | 说明 | 借鉴来源 | 改动行数 |
|------|------|----------|----------|
| **+双字段** `confidence_score` + `last_updated` | 治理 API 元数据层加 2 字段：promote→0.8 / demote→0.3 / validate pass→保持 / 90天无更新→自动 demote | Hindsight opinion network | ~60 行 |
| **+lint 实体合并** 改字符串相似度 | Levenshtein ≤ 3 字符 OR Jaccard ≥ 0.7（字符串）/ 0.5（关键词） | Graphiti MinHash+LSH | ~80 行（含算法） |
| **+on_session_end 轻整合** | 7天 05-待验证/ 文件 ≥ 5 个触发聚类，关键词重叠 ≥ 3 → 主题摘要 | Letta Sleeptime | ~20 行 |
| 配置 v2_1_fields + v2_1_lint 段 | governance config 新增两个 v2.1 段 | — | ~10 行 |
| 文档页脚升级 | v2.0 → v2.1 | — | ~5 行 |
| **总改动** | | | **≤ 200 行** |
| 归档区新增 | brain/archive/按层级和时间归档 | brain/ |

## v2.1→v2.2 变更日志 [v2.2新增]

**设计原则**：文档+模板升级，零核心代码改动，向后兼容（v2.1 配置文件仍可工作）。解决"Context 压缩丢信息"和"新信号直接占 L3/L4 槽位"两大痛点。

| 变更 | 说明 | 借鉴来源 | 改动范围 |
|------|------|----------|----------|
| **+WAL 协议** | 会话开始先读/写 `brain/SESSION-STATE.md` 再回复用户，Context 压缩后能恢复关键决策 | No1Lobster SESSION-STATE + ELM WAL | SKILL.md / 3 模块 / README / 新模板 ~250 行 |
| **+两阶段工作流** | 新信号先入 `brain/pending/` 毛坯区，证据 ≥ 3 次才 promote 到 cognition/ | No1Lobster "30 秒任务捕获 + 后蒸馏" | SKILL.md / cognitive-memory / memory-governance / 新模板 ~200 行 |
| **+SESSION-STATE.md 模板** | `templates/brain-init/SESSION-STATE.md` 含写入规范和示例 | — | 新增模板 ~50 行 |
| **+pending/ 目录模板** | `templates/brain-init/pending/README.md` + `.gitkeep` | — | 新增模板 ~120 行 |
| **+memory_governance.lint 新 scope** | `pending` / `session_state` 两种新检查范围 | — | memory-governance ~70 行 |
| **+on_new_observation 默认行为变更** | 新信号先写 pending/ 而非 cognition/，可通过 `two_stage_workflow.enabled = false` 回退 | — | cognitive-memory ~15 行 |
| **README 案例扩充** | 从 3 案例 → 5 案例（新增"WAL 救援长会话" + "pending 沉淀案例"） | — | README ~30 行 |
| **配置 v2_2_pending 段** | governance config 新增两阶段工作流配置 | — | config.template.json ~10 行 |
| **总改动** | 0 行 Node.js 代码，~750 行文档+模板 | | **~20% 文档增量** |
| **向后兼容** | 旧用户用 v2.1 配置仍能工作（WAL + 两阶段默认开启但可关闭） | | |
