# Agent Brain Skill

> 可移植的Agent大脑——让任何Agent装上就能共享同一套记忆、知识和同步能力

---

## 一句话介绍

**agent-brain-skill** 是一套可移植的Agent认知记忆系统。通过3D认知矩阵（五层架构 × PromptX四分类 × RoleX提炼循环），让你的AI助手不仅"记住对话"，更能"理解你这个人"。

---

## 核心理念

### 记忆不是存储问题，是认知问题

| 原则 | 说明 |
|------|------|
| **知识只编译一次** | 昨天的讨论结论、上周的决策背景，都自动沉淀为下一轮对话的起点 |
| **被动沉淀优于主动设定** | 最好的记忆不是"用户告诉我的"，而是"我观察到的" |
| **提炼是记忆的灵魂** | 记住一件事不等于理解一件事——原始记录是信息，提炼后才是知识 |
| **深层认知需要确认** | L4（价值观）和 L5（核心特质）的改动需要用户确认才能写入 |

---

## 理论溯源

本系统融合了四个经过实践检验的框架，构建"3D认知矩阵"：

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
```

| 框架 | 解决的问题 |
|------|-----------|
| **五层AI记忆架构** | 让AI从"记住对话"升级到"理解你这个人" |
| **PromptX Engram** | 记忆不是扁平的，不同类型有不同生命周期 |
| **Karpathy LLM Wiki** | 原始资料和提炼知识分离，服务对象不同 |
| **RoleX认知循环** | 记忆需要多轮观察→假设→验证→确认的渐进过程 |

---

## 适用平台

- **扣子Agent** — 通过Skill加载，即装即用
- **Claude Code** — 直接读取brain/目录
- **Cursor / CodeBuddy** — 内置终端支持完整功能
- **任何支持文件操作+Git的Agent**

---

## 与 web-access 的关系

| 能力 | 职责 |
|------|------|
| **web-access** | 管"怎么联网" — 获取外部信息 |
| **agent-brain** | 管"怎么记忆" — 沉淀内部认知 |

两者互补：web-access 让Agent看到更大的世界，agent-brain 让Agent记住你是谁。

---

## 快速开始

### 给Agent的核心描述

复制以下内容给你的Agent，它就知道怎么用了：

```
## Agent Brain 认知记忆系统

### 我是谁
你的用户有一个可移植的"数字大脑"，存储在GitHub仓库中。读取brain/目录即可了解用户是谁。

### 怎么连
1. 克隆私人仓库：`git clone https://github.com/YOUR_USERNAME/agent-brain.git ./brain`
2. 读取 `brain/index.md` 获取用户核心认知
3. 按需加载：`brain/cognition/`（行为/价值观/核心特质）

### 怎么记
遇到重要信息时：
- 初步观察 → `brain/wiki/entries/` 或 `brain/pending.md`
- 规律提炼 → `brain/cognition/behavior.md` 或 `cognition.md`
- L5核心特质变更 → 需用户确认后写入

### 怎么同步
- 会话开始：`git pull`
- 会话结束：`git add . && git commit && git push`

### 核心原则
- 始终加载 `brain/index.md`（<500字）
- 深层认知（L4/L5）变更需用户确认
- 提炼优于存储——把对话变成结论
```

### 安装步骤

```bash
# 1. 克隆本仓库
git clone https://github.com/Amasun93/agent-brain-skill.git

# 2. 加载 Skill
# 在扣子平台加载 SKILL.md，或在其他平台读取本目录

# 3. 创建你自己的私有GitHub仓库
# 例如：https://github.com/YOUR_USERNAME/agent-brain

# 4. 克隆你的私人仓库
git clone https://github.com/YOUR_USERNAME/agent-brain.git ./brain

# 5. 复制初始化模板到brain目录
# 从 agent-brain-skill/templates/brain-init/ 复制到 ./brain/

# 6. 连接初始化
cd brain
git remote add origin https://github.com/YOUR_USERNAME/agent-brain.git
git push -u origin main
```

---

## 目录结构

```
agent-brain-skill/
├── SKILL.md                     ← 主Skill文件（哲学+架构+配置）
├── modules/
│   ├── cognitive-memory/        ← 认知记忆模块（管"你是谁"）
│   │   ├── SKILL.md
│   │   ├── references/          # 五层框架/PromptX/RoleX 参考文档
│   │   └── templates/           # 索引和条目模板
│   ├── wiki-builder/            ← 知识库模块（管"你知道什么"）
│   │   └── SKILL.md
│   └── git-sync/                ← 同步模块（管"怎么共享"）
│       └── SKILL.md
├── config/
│   └── config.template.json      ← 配置模板（占位符）
└── templates/
    └── brain-init/              ← brain/目录初始化模板
        ├── index.md
        ├── log.md
        ├── .gitignore
        ├── 00-共享认知/          # L3-L5认知层模板
        ├── 01-知识库/
        ├── 02-原始资料/
        ├── 03-Agent空间/
        └── 04-待处理/
```

---

## 三大模块

| 模块 | 职责 | 存储位置 | 服务对象 |
|------|------|----------|----------|
| **cognitive-memory** | 管"你是谁" | `brain/cognition/` | 决定Agent**怎么跟你说话** |
| **wiki-builder** | 管"你知道什么" | `brain/wiki/` | 决定Agent**能帮你做什么** |
| **git-sync** | 管"怎么共享" | `brain/`整体 | 跨Agent、跨设备同步 |

---

## License

MIT License — 自由使用、修改、分发
