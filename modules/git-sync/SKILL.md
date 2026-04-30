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
            ├── cognition/*.md → 较少修改，中等冲突
            │       │
            │       └── 自动合并失败时
            │               ├── 保留ours和remote
            │               └── 写入冲突标记文件
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
git diff --name-only --diff-filter=U

# 3. 对每个冲突文件
#    - 低频文件（cognition/*.md）：保留两份，人工合并
#    - 高频文件（pending.md, log.md）：使用 ours 或 remote
git checkout --ours brain/cognition/behavior.md
git show :2:brain/cognition/behavior.md > brain/cognition/behavior.md.ours
git show :3:brain/cognition/behavior.md > brain/cognition/behavior.md.remote

# 4. 标记冲突待解决
echo "## 冲突待确认 - $(date)" >> brain/pending.md
echo "- cognition/behavior.md: ours/remote 保留待合并" >> brain/pending.md

# 5. 提交
git add .
git commit -m "Merge with conflicts marked for manual resolution"
git push
```

### 分支管理

```bash
# 创建工作分支（可选，用于实验性修改）
git checkout -b feature/new-behavior-pattern

# 合并前先 rebase main
git rebase origin/main

# 合并到 main
git checkout main
git merge feature/new-behavior-pattern
git push origin main
```

---

## 分层写入策略

### 文件分类

| 文件类型 | 示例 | 写入频率 | 冲突风险 | 策略 |
|----------|------|----------|----------|------|
| **高频追加** | pending.md, log.md | 每次会话 | 极低 | 仅追加，不合并 |
| **中频追加** | wiki/entries/L2-*.md | 偶发 | 低 | 按 ID 分区 |
| **低频覆盖** | cognition/*.md | 很低 | 中 | 用户确认后写入 |
| **最低频** | index.md | 极低 | 低 | 手动合并 |

### 写入优先级

```
L5 Core → L4 Cognition → L3 Behavior → L2 Situation → L1 State
    ↑ 高优先级（需要确认）                低优先级（可直接写入）↓
```

---

## 配置项

### 环境变量

```bash
# 必需
export AGENT_BRAIN_GIT_REPO="https://github.com/USER/agent-brain.git"
export AGENT_BRAIN_GIT_BRANCH="main"
export AGENT_BRAIN_AUTHOR_NAME="Agent Name"
export AGENT_BRAIN_AUTHOR_EMAIL="agent@brain.local"

# 可选
export AGENT_BRAIN_SYNC_ON_START="true"
export AGENT_BRAIN_SYNC_ON_END="true"
export AGENT_BRAIN_AUTO_PUSH_INTERVAL="3600"  # 秒
```

### config/config.json

```json
{
  "git": {
    "repo": "https://github.com/USER/agent-brain.git",
    "branch": "main",
    "author": {
      "name": "Agent Name",
      "email": "agent@brain.local"
    },
    "sync_on_start": true,
    "sync_on_end": true,
    "auto_push_interval": 3600,
    "conflict_strategy": "mark_and_keep_both"
  }
}
```

---

## Obsidian 集成

### Vault 结构

```
~/Obsidian/agent-brain/          ← Vault 根目录
├── brain/                         ← Git 同步目录（符号链接或子模块）
│   ├── index.md
│   ├── cognition/
│   ├── wiki/
│   └── ...
└── .obsidian/                     ← Obsidian 配置
    ├── workspace.json
    └── plugins/
```

### 同步策略

**推荐工作流**：

```
Coze Agent（写入）→ GitHub → Obsidian（本地预览/编辑）
                                        ↓
                                  本地修改
                                        ↓
                              Git commit & push
                                        ↓
                              Coze Agent pull
```

### Obsidian 插件建议

- **Git**：官方插件，Vault 内直接 Git 操作
- **Templater**：快速创建标准格式条目
- **Dataview**：索引查询和统计
- **Quick Switcher++**：快速定位条目

---

## 多 Agent 协作

### 场景：多个 Agent 同时工作

```
Agent A（扣子）          Agent B（Claude Code）
    │                         │
    ├── git pull              ├── git pull
    │    ↓                    │    ↓
    │  无冲突                 │  无冲突
    │    ↓                    │    ↓
    ├── 工作                  ├── 工作
    │    ↓                    │    ↓
    ├── git push              ├── git push
    │                         │    ↓
    │                         │  冲突！
    │                         │    ↓
    │                         ├── 保留两份，标记待确认
    │                         │    ↓
    │                         └── git push
```

### 最佳实践

1. **会话开始必须 pull**：确保基于最新状态工作
2. **会话结束尽快 push**：减少其他人拉取到旧版本的风险
3. **大修改开分支**：实验性认知修改用分支
4. **冲突不要忽略**：标记清楚，人工确认

---

## 故障处理

### 常见问题

**Q: git push 失败，提示 non-fast-forward**

```bash
# 原因：远程有更新
git pull origin main --rebase
# 解决冲突后
git push origin main
```

**Q: 本地仓库损坏**

```bash
# 重新克隆（保留 pending.md 和 log.md 本地版本）
git clone https://github.com/USER/agent-brain.git ./brain-rescue
# 手动合并需要的文件
```

**Q: 太多冲突文件**

```bash
# 使用 merge strategies
git merge -X ours origin/main  # 自动选择 ours
git merge -X theirs origin/main  # 自动选择 theirs
# 仅用于低优先级文件
```

### 备份策略

```bash
# 每次重要会话后，本地备份
cp -r brain brain.backup.$(date +%Y%m%d)

# 或使用 GitHub 的 backup 功能
# Settings →.Repositories → Enable Wiki 和 Issues
```

---

## 日志格式

每次同步操作记录到 `brain/log.md`：

```markdown
## [日期] [Agent ID] sync | [操作类型]

- **操作**: pull/push/merge/conflict_resolution
- **Agent**: [Agent ID]
- **变更文件**: [文件列表]
- **冲突数**: [数量]
- **备注**: [简短说明]
```

---

*Git Sync Module v1.0 — 让记忆无处不在*
