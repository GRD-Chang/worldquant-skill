---
name: factor-backtest-skill
description: 安全、高效地批量回测 8 个 Alpha 表达式，内置验证、熔断和异常处理机制。
---

# 因子回测 Skill

## 📌 用途

当你有 **8 个 Alpha 表达式**需要批量回测时，使用此技能。它会：
- ✅ 验证表达式的合法性
- ✅ 提交批量回测任务
- ✅ 监控进度并处理异常
- ✅ 返回完整的回测结果

---

## 🔑 核心约束（不可违反）

### 1. Rule of 8
- 必须恰好传入 **8 个表达式**
- 少于或多于 8 个都会被平台拒绝

### 2. 加速配置
- 必须设置 `visualization=false`（大幅加速回测）

### 3. 中性化选择
`neutralization` 必须从以下 5 项中选一：

| 值 | 适用场景 |
|---|---|
| `SLOW` | 适用于低换手率信号（<50%年化）、长期趋势捕捉、基本面或价值策略 |
| `FAST` | 适用于高换手率信号（>70%年化）、短期趋势捕捉、高频数据或短期价格动量策略 |
| `SLOW_AND_FAST` | 适用于同时考虑短期和长期趋势的策略、多元化Alpha组合、平衡型投资策略 |
| `REVERSION_AND_MOMENTUM` | 适用于需要对冲短期均值回归和长期动量的策略、平衡型多空Alpha因子 |
| `CROWDING` | 适用于希望降低拥挤交易风险的策略、强动量市场环境、流行因子或拥挤交易策略 |

---

## 🔄 标准流程（4 步）

### 步骤 1：验证表达式
mcp：`validate_alpha_expressions([表达式1, ..., 表达式8])`
- **要求**：必须 100% 通过验证
- **失败处理**：修正错误 → 重新验证 → **严禁未全部通过就进入回测**

### 步骤 2：提交回测
mcp:
`create_multiSim(
    alpha_expressions=[表达式1, ..., 表达式8],
    ...
)`

### 步骤 3：监控进度
mcp: `check_multisimulation_status()`

**监控策略**：
- ⏱️ 回测时间较长，可每 5–10 分钟查询一次
- 🚨 **僵尸回测熔断**：超过 40 分钟仍 `in_progress` → 放弃当前任务，重新提交

### 步骤 4：获取结果
mcp: `get_multisimulation_result()`

---

## ⚠️ 异常处理

### 语法错误
**现象**：回测返回 `"No alpha ID found in completed simulation"`

**处理**：
1. 调用 `get_SimError_detail` 查看详细错误
2. 对照 `get_operators` 检查操作符签名
3. 修正表达式 → **重新走完整验证流程**
4. ❌ **严禁无修改直接重试**

### 回测卡死
**现象**：状态持续 `in_progress` 超过 40 分钟

**处理**：
1. 重新认证会话
2. 再次检查状态
3. 若仍卡死 → 放弃当前任务，创建新回测

---

## 🚫 禁止操作

- ❌ 传入非 8 个表达式
- ❌ 使用 `neutralization` 列表外的值
- ❌ 设置 `visualization=true`
- ❌ 语法错误后无修改重试
- ❌ 高频轮询状态（< 5 分钟）

---

## 📊 输入/输出

### 输入
```python
{
    "alpha_expressions": [8 个表达式],
    "decay": 5,                          # 可选
    "neutralization": "SLOW",            # 必须从 5 项中选
    "truncation": 0.0,                   # 可选
    "test_period": "P0Y0M"               # 可选
}
```

### 输出
```python
{
    "location": "multiSim 结果 URL",
    "alphas": [
        {
            "alpha_id": "...",
            "sharpe": 1.8,
            "fitness": 1.2,
            "turnover": 0.25,
            "ic": 0.05
        },
        ...
    ],
    "status": "completed" | "failed",
    "errors": []
}
```

---

## 🛠️ 常用 MCP 工具

**核心**：
- `validate_alpha_expressions` - 验证表达式
- `create_multiSim` - 批量回测
- `check_multisimulation_status` - 检查状态
- `get_multisimulation_result` - 获取结果

**分析**：
- `get_alpha_details` - 详细指标
- `get_alpha_pnl` - 收益曲线
- `get_alpha_yearly_stats` - 年度统计
- `check_correlation` - 相关性检查

**调试**：
- `get_SimError_detail` - 错误详情
- `get_operators` - 操作符列表

---