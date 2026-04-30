# Git Sync 模块

**跨 Agent、跨设备同步记忆的核心模块**

---

## 概述

git-sync 是 agent-brain 的"神经网络"——它负责让分布在不同 Agent、不同设备上的记忆保持同步。无论你在哪个环境使用 Agent，读取和写入的都是同一份记忆。

### 核心能力

- **GitHub 仓库同步**：中央仓库，所有 Agent 读写
- **Obsidian 本地预览**：本地查看和编辑
- **分层写入**：减少冲突，保留审计轨迹
- **冲突自动标记**：冲突时保留两份，由人工确认

---

## 工作流程

### 标准同步流程

```
会话开始
    │
    ├── git pull origin main
    │       ↓
    ├── 读取 brain/index.md
    │       ↓
    └── 加载认知层（如需要）
            │
            ▼
会话进行中（记忆沉淀）
            │
            ▼
会话结束
    ├── 更新 index（如有新内容）
    ├── 追加 log.md
    │
    └── git add . && git commit && git push
```

### 冲突处理流程

```
git pull
    │
    ├── 无冲突 → 直接工作
    │
    └── 有冲突
            │
            ├── pending.md → 仅追加，无冲突
            ├── log.md → 仅追加，无冲突
            ├── wiki/entries/ → 分区隔离，低冲突
            ├── 00-共享认知/*.md → 较少修改，中等冲突
            │
            └── index.md → 最低频修改，低冲突
```

---

## Git 操作命令

### 基础操作

```bash
# 初始化新仓库（首次安装）
cd brain/
git init
git remote add origin https://github.com/USER/agent-brain.git
git add .
git commit -m "Initial: agent-brain initialized"
git push -u origin main

# 克隆已有仓库
git clone https://github.com/USER/agent-brain.git ./brain

# 会话开始：拉取更新
git pull origin main --rebase

# 会话结束：推送更新
git add .
git commit -m "Update: [简短描述]"
git push origin main
```

### 冲突解决

```bash
# 1. 尝试自动合并
git pull origin main --rebase

# 2. 如有冲突，查看冲突文件
git status

# 3. 保留两份，手动合并
git checkout --ours brain/index.md
# 或
git checkout --theirs brain/index.md

# 4. 标记冲突已处理
git add .
git commit -m "Merge: resolved conflicts"
git push origin main
```

---

## 分层写入策略

| 文件 | 写入频率 | 冲突风险 | 策略 |
|------|----------|----------|------|
| `index.md` | 低 | 中 | 合并更新 |
| `00-共享认知/*.md` | 低 | 中 | 用户确认后写入 |
| `01-知识库/entries/*.md` | 中 | 中 | 按主题分区 |
| `pending.md` | 高 | 低 | 仅追加 |
| `log.md` | 高 | 低 | 仅追加 |

---

## 敏感信息处理

- **Token 存储**：`config/config.json`（不纳入 Git）
- **大文件处理**：PDF/PPTX 等存入 `02-原始资料/`（不纳入 Git）
- **本地预览**：通过 Obsidian vault 本地查看
