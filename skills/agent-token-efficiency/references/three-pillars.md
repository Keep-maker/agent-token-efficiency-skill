# 三件套：Ponytail · Caveman · Headroom

## 为什么叫「三件套」

三者解决 Agent 会话里**三个不同环节**的 token 浪费，机制不重叠，设计上鼓励**叠加使用**。

```text
        INPUT                    GENERATION                 OUTPUT
  ┌──────────────┐         ┌──────────────┐         ┌─────────────────┐
  │  Headroom    │ ──────► │    Model     │ ──────► │ Caveman (prose) │
  │  压缩读入内容  │         │   推理+生成   │         │ Ponytail (code) │
  └──────────────┘         └──────────────┘         └─────────────────┘
```

## Ponytail — 少写代码

| 项 | 说明 |
|----|------|
| **作用段** | 模型**生成/修改代码**的行为 |
| **机制** | Skill/Rule 注入「七级梯」：YAGNI → 复用 → 标准库 → 原生 → 依赖 → 一行 → 最小实现 |
| **典型收益** |  agentic 基准约 **-54% LOC、-22% tokens、-20% cost**（feature 任务） |
| **不动什么** | 自然语言解释风格；安全/校验/ a11y |
| **与 Caveman** | Caveman 缩**说什么**；Ponytail 缩**建什么**；代码字节 Caveman 不动 |

**Over-build 陷阱示例：**

- 要 date picker → 原生 `<input type="date">` 而非 flatpickr 封装
- 要 color picker → 原生 `<input type="color">` 而非 200+ 行组件

## Caveman — 少说废话

| 项 | 说明 |
|----|------|
| **作用段** | 模型**自然语言输出** |
| **机制** | Skill 约束：去 filler、片段句、结论先行；**代码/命令/报错 byte-exact** |
| **典型收益** | 输出 token 约 **-65%**（22–87% 区间）；`/caveman-compress` 记忆文件约 **-46% input**（后续每会话） |
| **不动什么** | 代码块、shell 命令、错误栈、URL、路径 |
| **诚实边界** | Skill 自身占 ~1–1.5k input/轮；极短任务可能 net-negative；主收益是**可读性+速度**，成本是 bonus |

**强度级别：** `lite` | `full`（默认）| `ultra` | `wenyan`

## Headroom — 少灌上下文

| 项 | 说明 |
|----|------|
| **作用段** | **进模型之前**的 context |
| **机制** | Proxy / MCP / Library：SmartCrusher(JSON)、CodeCompressor(AST)、Kompress( prose/logs)；**CCR 可逆检索** |
| **典型收益** | 输入 **-60% ~ -95%**；SRE 调试、代码搜索等场景可达 90%+ |
| **不动什么** | 模型可通过 `headroom_retrieve` 按需取回原文 |
| **与 Caveman** | Headroom=管**输入**；Caveman=管**输出 prose**；最常见误解是混为一谈 |

**部署形态：**

```bash
headroom wrap cursor      # 包一层 Agent，自动走 proxy
headroom proxy --port 8787  # 独立代理
headroom mcp install      # MCP: compress / retrieve / stats
```

## 叠加示例

**一次典型 coding 任务：**

1. Agent 读 5000 行 build log → **Headroom** 压到 ~300 行等价信息
2. 模型写 fix + 解释 → **Ponytail** 让 fix 只有必要 15 行；**Caveman** 让解释 3 句而非 3 段
3. 下轮读回同一文件 → 文件本身已小 + Headroom 再压 tool output

## 账单数学（直觉）

若一轮会话 **input 80% / output 20%**：

- 仅 Caveman（-65% output）→ 总账单约 **-13%**
- 仅 Headroom（-80% input）→ 总账单约 **-64%**
- 三者叠加 → 接近各环节收益之和（非简单相加，但显著）

**结论：** Agent 重工具读回时优先 Headroom；话痨时加 Caveman；代码臃肿时加 Ponytail。
