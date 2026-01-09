# WorldQuant BRAIN Skills

<div align="center">

**基于 Claude Skills 的量化因子自动挖掘框架**

集成 WorldQuant BRAIN (cnhkmcp) + Claude Code/Gemini CLI/iFlow，实现因子研究全流程自动化

[English](#english) | [中文](#中文)

</div>

---

<a name="中文"></a>

## 中文

### 📚 目录

- [快速开始](#快速开始)
- [核心 Skills 介绍](#核心-skills-介绍)
  - [Alpha Research Recorder](#1-alpha-research-recorder)
  - [Factor Backtest](#2-factor-backtest)
  - [Knowledge Base Search](#3-knowledge-base-search)

---

### 快速开始

5 分钟快速上手指南：

#### 1. 安装与配置

```bash
# 克隆仓库
cd worldquant-skill

# 安装 WorldQuant BRAIN MCP 工具
pip install cnhkmcp

# 复制 skills 到对应目录
# - Claude Code: 复制到 ~/.claude/skills/
# - iFlow: 复制到 ~/.iflow/skills/
# - Gemini CLI: 复制到 ~/.gemini/skills/
```

#### 2. 核心 Skills 快速体验

**方式 1：交互式使用（Claude Code / Gemini CLI / iFlow）**

```bash
# 在 Claude Code 中
"使用 alpha-research-recorder skill，帮我记录这次研究会话"
"使用 factor_backtest skill，帮我回测这 8 个表达式"
"使用 knowledge_base_search skill，搜索降低 turnover 的方法"

# 在 Gemini CLI / iFlow 中（类似）
```

**方式 2：工作流集成（通过 .md 文件定义 SOP）**

```markdown
# factor-mining-workflow.md

## 目标
自动化因子挖掘流程

## 步骤

### 1. 搜索优化方法
使用 skill: knowledge_base_search
搜索降低 turnover 的方法

### 2. 记录研究会话
使用 skill: alpha-research-recorder
记录研究配置和约束

### 3. 批量回测
使用 skill: factor_backtest
回测 8 个 Alpha 表达式

## 在命令行工具中执行
# Claude Code / Gemini CLI / iFlow:
"执行 factor-mining-workflow.md 中定义的流程"
```

---

### 核心 Skills 介绍

本项目包含三个核心 Skills，专门为 WorldQuant BRAIN 平台的量化因子研究设计，支持**两种使用方式**：

- **交互式使用**：在 Claude Code、Gemini CLI、iFlow 等命令行工具中直接对话调用
- **工作流集成**：在命令行工具中通过 `.md` 文件定义 SOP（标准操作流程），让 Agent 按流程自动完成任务

#### 1. Alpha Research Recorder

**一句话总结**：自动记录和保存您的 Alpha 研究过程，让每一步都有迹可循。

**核心功能**：
- 根据记录类型（`session_meta` / `round` / `final_summary`）引用对应模板文件
- 接收结构化数据并验证必填字段
- 创建/更新 YAML 或 Markdown 日志文件

**使用场景**：
- 研究开始时记录配置和约束（`session_meta`）
- 单轮研究结束后记录完整过程（`round`）
- 研究完成后生成总结报告（`final_summary`）

**文件结构**：
```
logs/
└── 20260108_analyst_sonnet/
    ├── session_metadata.yml    # 会话配置
    ├── round_0001.yml          # 第1轮记录
    ├── round_0002.yml          # 第2轮记录
    └── final_summary.md        # 最终总结
```

**使用方式**：
```bash
"使用 alpha-research-recorder skill，帮我记录这次研究会话"
```

**详细文档**：[alpha-research-recorder/SKILL.md](skills/alpha-research-recorder/SKILL.md)

---

#### 2. Factor Backtest

**一句话总结**：批量回测 8 个 Alpha 表达式，自动验证并监控进度。

**核心功能**：
- 验证表达式的合法性
- 提交批量回测任务
- 监控进度并处理异常
- 返回完整的回测结果

**核心约束**：
- ⚠️ **Rule of 8**：必须恰好传入 8 个表达式
- ⚠️ **加速配置**：必须设置 `visualization=false`
- ⚠️ **中性化选择**：必须从 5 个选项中选择

**标准 4 步流程**：
1. 验证表达式（`validate_alpha_expressions`）
2. 提交回测（`create_multiSim`）
3. 监控进度（`check_multisimulation_status`）
4. 获取结果（`get_multisimulation_result`）

**使用方式**：
```bash
"使用 factor_backtest skill，帮我回测这 8 个表达式：[表达式列表]"
```

**详细文档**：[factor_backtest/SKILL.md](skills/factor_backtest/SKILL.md)

---

#### 3. Knowledge Base Search

**一句话总结**：智能搜索知识库，快速找到数据字段、优化方法和优质示例。

**核心功能**：
- 支持关键词搜索、语义理解、字段查找和示例推荐
- 涵盖数据集字段、优化经验、优质示例和平台机制
- 返回结构化、可追溯的搜索结果

**知识库结构**：
```
./Resources/
├── DATASET/                    # 数据集目录
├── good_alpha_examples/             # 优质示例
├── How_WorldQuant_BRAIN_Backtesting_Works.md  # 平台机制
├── alpha_optimization/              # 优化经验
└── regular_operators.csv           # 操作符清单
```

**使用方式**：
```bash
"使用 knowledge_base_search skill，搜索降低 turnover 的方法"
"使用 knowledge_base_search skill，查找 close 字段的信息"
```

**详细文档**：[knowledge_base_search/SKILL.md](skills/knowledge_base_search/SKILL.md)

---

<a name="english"></a>

## English

**A Quantitative Factor Auto-Mining Framework Based on Claude Skills**

Integrating WorldQuant BRAIN (cnhkmcp) + Claude Code/Gemini CLI/iFlow for automated factor research workflow

### 📚 Table of Contents

- [Quick Start](#quick-start)
- [Core Skills Overview](#core-skills-overview)
  - [Alpha Research Recorder](#1-alpha-research-recorder-1)
  - [Factor Backtest](#2-factor-backtest-1)
  - [Knowledge Base Search](#3-knowledge-base-search-1)

---

### Quick Start

5-minute quick start guide:

#### 1. Installation & Configuration

```bash
# Clone repository
git clone https://github.com/your-org/worldquant-skill.git
cd worldquant-skill

# Install WorldQuant BRAIN MCP tool
pip install cnhkmcp

# Copy skills to corresponding directories
# - Claude Code: copy to ~/.claude/skills/
# - iFlow: copy to ~/.iflow/skills/
# - Gemini CLI: copy to ~/.gemini/skills/
```

#### 2. Core Skills Quick Experience

**Method 1: Interactive Usage (Claude Code / Gemini CLI / iFlow)**

```bash
# In Claude Code
"Use alpha-research-recorder skill to record this research session"
"Use factor_backtest skill to backtest these 8 expressions"
"Use knowledge_base_search skill to search for turnover reduction methods"

# In Gemini CLI / iFlow (similar)
```

**Method 2: Workflow Integration (Define SOP via .md files)**

```markdown
# factor-mining-workflow.md

## Goal
Automated factor mining workflow

## Steps

### 1. Search optimization methods
Use skill: knowledge_base_search
Search for turnover reduction methods

### 2. Record research session
Use skill: alpha-research-recorder
Record research configuration and constraints

### 3. Batch backtest
Use skill: factor_backtest
Backtest 8 Alpha expressions

## Execute in command-line tools
# Claude Code / Gemini CLI / iFlow:
"Execute the workflow defined in factor-mining-workflow.md"
```

---

### Core Skills Overview

This project includes three core Skills designed specifically for quantitative factor research on the WorldQuant BRAIN platform, supporting **two usage methods**:

- **Interactive Usage**: Direct conversational invocation in Claude Code, Gemini CLI, iFlow, and other command-line tools
- **Workflow Integration**: Define SOPs (Standard Operating Procedures) through `.md` files in command-line tools, enabling Agents to complete tasks automatically according to the workflow

#### 1. Alpha Research Recorder

**One-line Summary**: Automatically record and save your Alpha research process, making every step traceable.

**Core Features**:
- Reference corresponding template files based on record type (`session_meta` / `round` / `final_summary`)
- Receive structured data and validate required fields
- Create/update YAML or Markdown log files

**Use Cases**:
- Record configuration and constraints at research start (`session_meta`)
- Record complete process after single research round (`round`)
- Generate summary report after research completion (`final_summary`)

**File Structure**:
```
logs/
└── 20260108_analyst_sonnet/
    ├── session_metadata.yml    # Session configuration
    ├── round_0001.yml          # Round 1 record
    ├── round_0002.yml          # Round 2 record
    └── final_summary.md        # Final summary
```

**Usage**:
```bash
"Use alpha-research-recorder skill to record this research session"
```

**Detailed Documentation**: [alpha-research-recorder/SKILL.md](skills/alpha-research-recorder/SKILL.md)

---

#### 2. Factor Backtest

**One-line Summary**: Batch backtest 8 Alpha expressions with automatic validation and progress monitoring.

**Core Features**:
- Validate expression legality
- Submit batch backtest tasks
- Monitor progress and handle exceptions
- Return complete backtest results

**Core Constraints**:
- ⚠️ **Rule of 8**: Must pass exactly 8 expressions
- ⚠️ **Acceleration Config**: Must set `visualization=false`
- ⚠️ **Neutralization Selection**: Must choose from 5 options

**Standard 4-Step Workflow**:
1. Validate expressions (`validate_alpha_expressions`)
2. Submit backtest (`create_multiSim`)
3. Monitor progress (`check_multisimulation_status`)
4. Get results (`get_multisimulation_result`)

**Usage**:
```bash
"Use factor_backtest skill to backtest these 8 expressions: [expression list]"
```

**Detailed Documentation**: [factor_backtest/SKILL.md](skills/factor_backtest/SKILL.md)

---

#### 3. Knowledge Base Search

**One-line Summary**: Intelligently search the knowledge base to quickly find data fields, optimization methods, and quality examples.

**Core Features**:
- Support keyword search, semantic understanding, field lookup, and example recommendations
- Cover dataset fields, optimization experience, quality examples, and platform mechanisms
- Return structured, traceable search results

**Knowledge Base Structure**:
```
./Resources/
├── DATASET/                    # Dataset directory
├── good_alpha_examples/             # Quality examples
├── How_WorldQuant_BRAIN_Backtesting_Works.md  # Platform mechanisms
├── alpha_optimization/              # Optimization experience
└── regular_operators.csv           # Operator list
```

**Usage**:
```bash
"Use knowledge_base_search skill to search for turnover reduction methods"
"Use knowledge_base_search skill to find information about close field"
```

**Detailed Documentation**: [knowledge_base_search/SKILL.md](skills/knowledge_base_search/SKILL.md)

---

### Contributing

Contributions welcome! Please follow these steps:
1. Fork this repository
2. Create feature branch (`git checkout -b feature/amazing-skill`)
3. Commit changes (`git commit -m 'feat: add amazing skill'`)
4. Push to branch (`git push origin feature/amazing-skill`)
5. Create Pull Request

### License

This project is licensed under the Apache 2.0 License. See [LICENSE](LICENSE) file for details.

---

<div align="center">

**[⬆ Back to Top](#worldquant-brain-skills)**

Made with ❤️ by the WorldQuant BRAIN community

</div>
