---
name: alpha-research-recorder
description: |
  Alpha 研究日志记录器 - 作为研究流程的数据持久化层。

  核心功能：
  1. 根据记录类型（session_meta/round/final_summary）引用对应的模板文件
  2. 接收结构化数据并验证必填字段
  3. 创建/更新 YAML 或 Markdown 日志文件

  使用方式：
  - 调用时指定 record_type 和 session_id
  - 查看模板文件了解完整字段结构：templates/{record_type}.template
  - 传入数据合并到模板，生成最终文件
---

# Alpha 研究记录器 Skill

## 技能定位

本技能是 **Alpha 研究流程中的数据持久化层**，负责将研究过程的结构化数据保存为可追溯的日志文件。

### 在研究流程中的地位

```
研究流程：
┌─────────────────┐      ┌─────────────────┐      ┌─────────────────┐
│ 知识检索/输入    │ ───▶ │  Alpha 生成/回测 │ ───▶ │   因子评估       │
│ (其他技能)       │      │  (其他技能)      │      │  (其他技能)      │
└────────┬────────┘      └────────┬────────┘      └────────┬────────┘
         │                        │                        │
         └────────────────────────┼────────────────────────┘
                                  ▼
                    ┌─────────────────────────┐
                    │  alpha-research-recorder │ ◀── 本技能
                    │  (数据持久化 + 文件管理)  │
                    └─────────────────────────┘
                                  │
                    ┌─────────────┴─────────────┐
                    ▼                           ▼
              session_metadata.yml        round_NNNN.yml
              (会话配置)                  (轮次记录)
                    │                           │
                    └─────────────┬─────────────┘
                                  ▼
                          final_summary.md
                         (最终总结报告)
```

---

## 记录类型

本技能支持三种独立的记录类型，**按需调用**，无固定顺序：

| 记录类型 | 文件格式 | 使用场景 | 输出文件 | 模板文件 |
|---------|---------|---------|---------|---------|
| **session_meta** | YAML | 研究开始时记录配置和约束 | `session_metadata.yml` | `templates/session_metadata.yml.template` |
| **round** | YAML | 单轮研究结束后记录完整过程 | `round_NNNN.yml` | `templates/round_NNNN.yml.template` |
| **final_summary** | Markdown | 研究完成后生成总结报告 | `final_summary.md` | `templates/final_summary.md.template` |

**调用方式**：
- ✅ 可以只调用 `session_meta`，不创建 round
- ✅ 可以创建多个 `round`，延迟创建 `final_summary`
- ✅ 可以中途补充或修改任何记录
- ❌ **禁止跳过 `session_meta` 直接创建 `round`**

---

## 参数规范

### 通用参数（所有记录类型共用）

| 参数 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|--------|------|
| `record_type` | `string` | 是 | - | 记录类型：`"session_meta"` / `"round"` / `"final_summary"` |
| `root_dir` | `string` | 否 | `"logs/"` | 研究日志根目录 |
| `session_id` | `string` | 是 | - | 会话唯一标识，格式：`YYYYMMDD_<主题>_<LLM>` |

### 数据参数（根据 record_type 引用对应模板）

每种记录类型的完整字段定义请查看对应的模板文件：

| 记录类型 | 模板文件路径 | 查看方式 |
|---------|-------------|---------|
| **session_meta** | `./templates/session_metadata.yml.template` | 包含注释说明哪些是必填字段 |
| **round** | `./templates/round_NNNN.yml.template` | 包含注释说明哪些是必填字段 |
| **final_summary** | `./templates/final_summary.md.template` | 包含注释说明哪些是必填字段 |

---


### 典型场景

#### 场景 1：研究开始时快速创建 session_meta

**技能操作**：
- 读取 `templates/session_metadata.yml.template`
- 将传入数据合并到模板
- 保留未填写的可选字段为空或注释
- 创建 `{root_dir}/{session_id}/session_metadata.yml`

---

#### 场景 2：研究进行中补充 session_meta

**技能操作**：
- 读取已存在的 `session_metadata.yml`
- 合并新数据到已有字段
- 不覆盖已填写的内容
- 更新文件

---

#### 场景 3：创建 round 记录

**步骤**：
1. 查看 `templates/round_NNNN.yml.template`
2. 识别 `[必填]` 字段
3. 传入数据

---

## 文件结构

```
{root_dir}/                        # 默认: logs/
└── {session_id}/                  # 例如: 20260108_analyst_sonnet/
    ├── session_metadata.yml       # 会话配置（可逐步完善）
    ├── round_0001.yml             # 第1轮（可逐步完善）
    ├── round_0002.yml             # 第2轮（可逐步完善）
    ├── ...                        # 更多轮次
    └── final_summary.md           # 最终总结（可选）
```

---

## 核心原则


### 1. 经济学分析优先 ⭐⭐⭐⭐⭐
以下字段在任何阶段都应优先填写（在模板中标注为 ⭐）：
- `economic_analysis.hypothesis`（经济学假设）
- `economic_interpretation`（经济学解释）
- `economic_insights`（经济学洞察）

### 2. 结构化优先
- ✅ 使用 YAML 确保数据可解析
- ✅ 避免自由文本，便于程序化提取
- ✅ 标准化字段命名

---

## 成功标准

| 维度 | 标准 | 验证方法 |
|------|------|---------|
| **可追溯性** | 每个决策都有明确记录 | 检查 `rationale`、`hypothesis` 字段 |
| **渐进性** | 支持分阶段填写 | 验证可选字段可后续补充 |
| **经济学深度** | 核心经济学字段完整 | 检查 3 个 `economic_*` 字段 |
| **可解析性** | YAML 可被程序提取 | 使用 `yaml.safe_load()` 验证 |
| **灵活性** | 支持按需调用 | 验证可单独创建任意记录类型 |

---

## 模板文件

### 模板文件索引

| 模板文件 | 记录类型 | 格式 | 行数 | 说明 |
|---------|---------|------|------|------|
| `session_metadata.yml.template` | session_meta | YAML | ~60 | 会话配置完整模板 |
| `round_NNNN.yml.template` | round | YAML | ~90 | 轮次记录完整模板 |
| `final_summary.md.template` | final_summary | Markdown | ~406 | 最终总结完整模板 |
| `README.md` | 使用文档 | Markdown | ~447 | 详细使用指南 |

**查看方式**：
```bash
# 查看特定模板
cat templates/session_metadata.yml.template

# 或在 IDE 中打开
open templates/round_NNNN.yml.template
```

---

### 数据流向

```
其他技能 ──────▶ alpha-research-recorder ──────▶ YAML/MD 文件
(生成数据)         (引用模板 + 数据合并)            (持久化存储)
                       │
                       ▼
                  查看模板文件
             templates/{type}.template
```