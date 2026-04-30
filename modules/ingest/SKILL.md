---
name: ingest
description: 文档Ingest管线——将PDF/DOCX等原始文档转换为AI友好的Markdown知识。触发词：ingest、提取文档、导入知识、文档入库、摄取文档。通过MinerU提取+AI校正+智能切片，构建可持续积累的知识库。
---

# Ingest 模块

**文档到知识的桥梁——让原始资料变成AI可用的知识**

---

## 一、为什么需要Ingest模块

### 原始文档是AI的"满身泥萝卜"

```
Word/PDF里的格式代码（颜色、字体、字号、段前段后）
→ 在AI眼中 = 浩如烟海的干扰符号
→ 像从土里拔出来的萝卜，满身是泥
```

**核心问题**：直接让AI读取PDF/Word，AI需要消耗大量token解析格式，效果差且成本高。

**解决方案**：先把萝卜洗干净 → 全部转换为Markdown

### Ingest的本质

Ingest不是简单的格式转换，而是**知识的预处理和结构化**：

| 阶段 | 做什么 | 产出 |
|------|--------|------|
| **提取** | PDF/DOCX → Markdown | 原始内容（可能带OCR错误） |
| **校正** | AI逐篇检查 | 修正后的Markdown |
| **切片** | 按知识类型切分 | 细粒度文件 |
| **归档** | JSON索引 + Markdown内容 | 可检索的知识库 |

---

## 二、核心管线设计

### 标准Ingest流程

```
┌─────────────────────────────────────────────────────────────┐
│                        输入阶段                              │
│         PDF / DOCX / 图片扫描件 / 网页存档                   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    MinerU 提取阶段                           │
│                                                              │
│  云端模式: mineru -p xxx.pdf -o ./output -b pipeline        │
│           (CLI本地执行，无需API Key)                         │
│                                                              │
│  API模式: mineru.net 在线API                                │
│           (需要API Key，适合无GPU环境)                        │
│                                                              │
│  输出: Markdown + JSON结构 + 图片目录                        │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    AI 校正阶段                               │
│                                                              │
│  • 修正OCR识别错误                                          │
│  • 修复格式错乱（如表格、公式）                              │
│  • 统一Markdown语法规范                                     │
│  • 保持原文核心信息完整                                      │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    智能切片阶段                              │
│                                                              │
│  论文 → 按章节/段落切片                                      │
│  试卷 → 按题目/板块切片                                      │
│  报告 → 整体或按章节切片                                     │
│  书籍 → 按章节/单元切片                                      │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    归档入库阶段                               │
│                                                              │
│  JSON索引 → 机器可检索                                       │
│  Markdown内容 → AI可理解                                     │
│  归档至 brain/01-知识库/                                     │
└─────────────────────────────────────────────────────────────┘
```

### 渐进式披露原则

> 文件夹层级设置得足够细 → AI只需看目录结构 → 按需进入文件

**好处**：
- AI只需读目录就能定位问题，不必读完所有内容
- 节省模型上下文，避免全量读取
- 支持按主题精准检索

---

## 三、三种运行模式

### 模式A：云端模式（推荐）

**适用场景**：云电脑有GPU或足够算力

**前置条件**：
- MinerU CLI已安装（`pip install mineru`）
- 云电脑有NVIDIA GPU（推荐）或足够CPU算力

**执行命令**：
```bash
export HOME=/root
mineru -p /path/to/document.pdf -o ./output -b pipeline
```

**输出结构**：
```
output/
├── content.md        # 主Markdown文件
├── content.json      # 结构化JSON（含标题层级、页码、坐标）
└── images/           # 图片目录
```

**优点**：
- 不需要API Key
- 处理速度快（本地GPU加速）
- 可批量处理

---

### 模式B：API模式

**适用场景**：无GPU环境、追求便捷

**前置条件**：
- MinerU API Key（https://mineru.net）

**执行方式**：
```python
import requests

response = requests.post(
    "https://api.mineru.net/v1/extract",
    headers={"Authorization": f"Bearer {API_KEY}"},
    files={"file": open("document.pdf", "rb")}
)
```

**优点**：
- 不需要本地算力
- 设置简单
- 适合临时处理

**缺点**：
- 需要API Key
- 有调用次数限制
- 网络依赖

---

### 模式C：本地模式（用户本地）

**适用场景**：用户本地配合Obsidian使用

**前置条件**：
- 用户本地安装MinerU CLI
- IDE（Cursor/Claude Code）直接访问本地文件

**工作流**：
```
Obsidian（看文件/浏览知识）
    ↕ 同一文件夹
IDE（执行ingest/处理文档）
    ↕
Markdown格式标准
```

**优点**：
- 完全本地化，数据不离开用户电脑
- 配合Obsidian双链能力
- 适合隐私敏感场景

---

## 四、智能切片策略

### 切片粒度原则

**核心思想**：切到最细粒度，按需组合

```
一本书 → 按册分 → 按单元分 → 按课分 → 单元导语独立文件
一套卷子 → 按板块分 → 按题目分 → 原题/题目/答案 分离
```

### 不同文档类型的切片策略

| 文档类型 | 切片策略 | 说明 |
|----------|----------|------|
| **学术论文** | 按章节切片 | 摘要/引言/方法/实验/结论独立文件 |
| **考试试卷** | 按题目切片 | 题目/答案分离，支持按板块检索 |
| **研究报告** | 按章节或整体 | 短报告整体，长报告按章 |
| **技术书籍** | 按章节/小节 | 保持目录层级，支持按主题检索 |
| **会议PPT** | 按幻灯片切片 | 每页独立文件，含备注 |
| **网页存档** | 按主题分块 | 保留原始链接，提取关键段落 |

### 切片后的目录结构示例

**论文切片**：
```
01-知识库/学术论文/
└── 2024-Neural-Architecture-Search/
    ├── 00-元信息.md
    ├── 01-摘要.md
    ├── 02-引言.md
    ├── 03-相关工作.md
    ├── 04-方法/
    │   ├── 04-1-问题定义.md
    │   ├── 04-2-搜索空间.md
    │   └── 04-3-搜索策略.md
    ├── 05-实验/
    └── 06-结论.md
```

**试卷切片**：
```
01-知识库/考试试卷/
└── 2024-北京-高考一模/
    ├── 00-元信息.md
    ├── 01a-默写/
    │   ├── 题目.md
    │   └── 答案.md
    ├── 01b-语用/
    ├── 02-社科文阅读/
    ├── 03-文学类阅读/
    │   ├── 03-现代文/
    │   │   ├── 原文.md
    │   │   ├── 题目.md
    │   │   └── 答案.md
    │   └── 03-诗歌/
    └── README.md
```

---

## 五、校正流程详解

### 为什么需要AI校正

MinerU提取的Markdown可能存在：
- OCR识别错误（尤其是扫描版PDF）
- 格式错乱（表格、公式、代码块）
- 语义不连贯（换行位置不当）

### 校正执行流程

```
MinerU输出
    │
    ├── 步骤1: 读取Markdown内容
    │
    ├── 步骤2: 识别潜在问题
    │   ├── OCR错误（低置信度词汇）
    │   ├── 格式错误（表格未对齐）
    │   └── 语义断裂（段落被错误切分）
    │
    ├── 步骤3: AI校正
    │   ├── 修正OCR错误（保持原意）
    │   ├── 修复格式（Markdown规范）
    │   └── 补充缺失内容（如页眉页脚）
    │
    └── 步骤4: 输出校正后Markdown
```

### 校正原则

| 原则 | 说明 | 示例 |
|------|------|------|
| **保持原意** | 修正格式，不改变语义 | "神经网络"不能改成"深度学习" |
| **最小修改** | 只改必要的错误 | 原文通顺则不改 |
| **标注存疑** | 无法确定时标注 | `[?OCR存疑]` |
| **保留版本** | 校正前后的版本都保留 | `raw/` + `corrected/` |

---

## 六、MinerU配置说明

### 安装 MinerU

```bash
export HOME=/root
pip install mineru
```

### 验证安装

```bash
export HOME=/root
mineru --version
```

### 基本用法

```bash
# 标准用法（pipeline模式）
export HOME=/root
mineru -p document.pdf -o ./output -b pipeline

# 指定输出格式
mineru -p document.pdf -o ./output -b pipeline --format markdown

# 批量处理
for f in *.pdf; do mineru -p "$f" -o "./output/$f" -b pipeline; done
```

### 输出说明

| 文件 | 说明 |
|------|------|
| `content.md` | 主内容Markdown |
| `content.json` | 结构化JSON（含页码、坐标） |
| `images/` | 图片目录 |

---

## 七、与wiki-builder的协作

### Ingest → WikiBuilder 流程

```
Ingest模块
    │
    ├── PDF/DOCX → Markdown（提取）
    │
    └── Markdown → 细粒度切片（切片）
            │
            ▼
WikiBuilder模块
    │
    ├── 读取切片内容
    │
    ├── 与用户确认关键要点
    │
    └── 更新wiki知识库
        ├── index.md
        ├── 实体页/概念页
        └── log.md
```

### 协作原则

| 模块 | 职责 | 产出 |
|------|------|------|
| **ingest** | 提取、校正、切片 | 可读的Markdown文件 |
| **wiki-builder** | 提炼、入库、建立引用 | 知识条目和交叉引用 |

**边界**：ingest负责"怎么把文档变成Markdown"，wiki-builder负责"怎么把Markdown变成知识"。

---

## 八、环境适配指南

### 不同Agent环境的适配方案

| 环境 | MinerU执行方式 | 说明 |
|------|----------------|------|
| **扣子+云电脑** | 云端模式（CLI） | MinerU已预装，直接使用 |
| **Claude Code** | 本地模式 | 直接在本地执行 |
| **Cursor** | 本地模式 | 配合Obsidian使用 |
| **无GPU云环境** | API模式 | 调用mineru.net API |

### MinerU可用性检查

```python
import subprocess

def check_mineru():
    """检查MinerU是否可用"""
    try:
        result = subprocess.run(
            ["mineru", "--version"],
            capture_output=True,
            text=True
        )
        if result.returncode == 0:
            return True, result.stdout.strip()
    except FileNotFoundError:
        return False, "MinerU not installed"
    return False, "Unknown error"
```

---

## 九、配置项说明

在 `config/config.json` 中配置：

```json
{
  "modules": {
    "ingest": {
      "enabled": true,
      "mode": "cli",           // "cli" | "api"
      "output_base": "./brain/01-知识库",
      "correction": {
        "enabled": true,
        "model": "auto"        // "auto" | "gpt-4" | "claude-3"
      },
      "slicing": {
        "auto": true,          // 自动切片
        "strategy": "auto"     // "auto" | "by-chapter" | "by-question"
      }
    }
  },
  "sensitive": {
    "mineru_api_key": ""       // API模式需要
  }
}
```

---

## 十、使用示例

### 示例1：ingest一篇论文

**用户说**："帮我ingest这篇论文"（附PDF）

**执行**：
1. 保存PDF到临时目录
2. 执行MinerU提取
3. AI校正Markdown
4. 按章节切片
5. 归档到 `brain/01-知识库/论文/`
6. 更新wiki-builder索引

**结果**：
```
brain/01-知识库/论文/
└── 2024-NAS-Survey/
    ├── 00-元信息.md
    ├── 01-摘要.md
    ├── 02-引言.md
    └── ...
```

### 示例2：ingest一套试卷

**用户说**："帮我处理这份高考卷"

**执行**：
1. PDF → MinerU提取
2. AI识别8大板块
3. 按板块切片（题目/答案分离）
4. 归档到 `brain/01-知识库/考试试卷/`

**结果**：
```
brain/01-知识库/考试试卷/
└── 2024-北京-高考一模/
    ├── 01a-默写/题目.md + 答案.md
    ├── 03-文学类阅读/...
    └── README.md
```

---

## References

| 文件 | 何时加载 |
|------|---------|
| `brain/01-知识库/知识管理/止水老师知识库方法论.md` | 理解切片策略和方法论时 |
| `modules/wiki-builder/SKILL.md` | 与wiki-builder协作时 |
| `config/config.template.json` | 配置MinerU参数时 |
