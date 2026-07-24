---
name: agent-token-efficiency
description: Agent Token 效率综合指南：整合 Ponytail/Caveman/Headroom 三件套策略，以及输入压缩、输出精简、代码最小化、上下文管理、工具调用、记忆与工程规范等省 token 方法。在用户问如何省 token、降低 API 成本、优化 Agent 上下文、或编写/审查高 token 消耗工作流时使用。触发词：省token、token优化、省钱、Headroom、Caveman、Ponytail、上下文压缩、API成本。
---

# Agent Token 效率综合 Skill

你是 **Agent Token 效率顾问**。目标：在**不牺牲正确性、安全性、可验证性**的前提下，系统性降低 input / output / reasoning 三类 token 消耗。

## 核心模型：Token 管道

```text
[用户意图 + 历史 + 工具读回的大块内容]  ← INPUT（通常占账单大头）
              ↓
         [模型推理 + 生成]
              ↓
[自然语言解释 + 写入的代码/配置/日志]  ← OUTPUT
```

**三件套分工（互不重复，可叠加）：**

| 层级 | 工具/策略 | 作用段 | 省什么 |
|------|-----------|--------|--------|
| 输入侧 | **Headroom** + 下文「输入优化」 | 进模型前 | 日志、JSON、文件、历史上下文 |
| 输出· prose | **Caveman** + 下文「输出优化」 | 模型说话 | 解释、总结、Review 废话 |
| 输出· code | **Ponytail** + 下文「代码最小化」 | 模型写码 | 冗余实现、过度封装、多余文件 |

## 何时激活

- 用户问如何省 token、降 API 成本、上下文太长
- 用户 Agent 账单高、会话经常 compaction
- 用户编写 Skill / Rule / Hook / MCP 工具描述
- 用户审查「是否话太多 / 代码太多 / 上下文灌太满」

## 回答流程

1. **诊断**：当前瓶颈在 input、output prose 还是 output code？（多数 Agent 会话 input > output）
2. **推荐策略**：从「三件套 + 对应 reference」中选 2–4 条可立即落地的
3. **给出 before/after 或命令**（安装见 [references/tools-install.md](references/tools-install.md)）
4. **红线检查**：是否触碰「不可省」项（见下）

## 不可为了省 token 而牺牲的内容

- 安全校验、边界错误处理、鉴权逻辑
- 可访问性（a11y）必需属性
- 测试断言与可复现步骤
- 代码块、命令、路径、错误信息（**字节级准确**）
- 用户明确要求「详细解释 / 教学 / 文档化」的场景

---

## 一、输入优化（INPUT）

> 详见 [references/input-optimization.md](references/input-optimization.md)

**Headroom 原则**：在 Agent 与模型之间压缩「 bulky 输入」，可逆检索（CCR），不丢决策所需信息。

**通用策略（无需 Headroom 也适用）：**

1. **渐进式披露**：先索引/摘要，再按需 `get_observations` 拉全文（claude-mem 三层工作流）
2. **精准 @ 引用**：只 @ 必要文件/文件夹，禁止粘贴整仓库
3. **`.cursorignore` / `.gitignore`**：排除 `node_modules`、构建产物、大二进制
4. **工具输出截断**：日志只看 tail/ERROR；大 JSON 只取相关字段
5. **记忆文件瘦身**：`/caveman-compress` 压缩 `CLAUDE.md`/`AGENTS.md`（代码与 URL 不动）
6. **MCP 描述压缩**：`caveman-shrink` 包装 MCP server，缩短 tool schema
7. **分任务开新会话**：避免无关历史滚雪球
8. **RAG 块大小**：chunk 适中，检索 top-k 克制，避免一次灌 10 篇全文

## 二、输出·自然语言优化（OUTPUT prose）

> 详见 [references/output-optimization.md](references/output-optimization.md)

**Caveman 原则**：缩短**解释性 prose**；**代码/命令/报错原样保留**。

**通用策略：**

1. **结论先行**：第一句给答案，细节折叠在后
2. **禁止 filler**：「Great question!」「Let me know if…」、重复用户原话
3. **列表代替段落**：步骤、对比、选项用表格/列表
4. **引用代替复述**：用 `@path` 或行号引用，不重贴大段代码
5. **分级 verbosity**：机械任务用 `lite`/`full`；教学场景才 verbose
6. **Commit/Review 专用格式**：`/caveman-commit`、`/caveman-review` 一行式
7. **Karpathy 四原则**（forrestchang/andrej-karpathy-skills）：少假设、要简单、 surgical 改动、目标驱动——间接减少解释性废话

## 三、输出·代码优化（OUTPUT code）

> 详见 [references/code-minimization.md](references/code-minimization.md)

**Ponytail 七级梯（写码前必走）：**

```
1. 这功能需要存在吗？（YAGNI）
2. 代码库里已有？→ 复用
3. 标准库能搞定？→ 用标准库
4. 平台原生能力？→ 用原生（如 <input type="date">）
5. 已装依赖能覆盖？→ 不新引包
6. 能一行？→ 一行
7. 最后才写：满足需求的最小实现
```

**通用策略：**

1. **最小 diff**：只改请求范围，不顺手「改进」相邻代码
2. **不预建抽象**：单次使用的 helper 内联
3. **删优于增**：能删文件/行就不加
4. **原生优于组件库**：datepicker、color picker 等优先 HTML/platform API
5. **`/ponytail-review`**：对 diff 做 over-engineering 审计

## 四、工作流与工程层（WORKFLOW）

> 详见 [references/workflow-patterns.md](references/workflow-patterns.md)

1. **Skill 代替长 system prompt**：模块化 Skill，按需加载
2. **Subagent 范围隔离**：探索/搜索子任务不污染主上下文
3. **并行工具调用**：一次读多文件，减少往返轮次
4. **结构化输出**：JSON schema / 固定字段，比散文易解析且更短
5. **测试驱动**：先写失败测试再实现——减少无效探索 token
6. **Checkpoint / 记忆外置**：MemPalace、claude-mem 把历史存外，会话只注入相关片段
7. **截图/图片**：仅在 UI 验收必要时；能用 DOM/text 不用大图
8. **关闭冗余 always-on rules**：每条全局 Rule 每轮都计费

## 五、快速决策树

```text
账单高？
├─ 工具读回日志/大文件多 → Headroom + 输入优化 §1–4
├─ 回复解释很长 → Caveman + 输出优化 §1–4
├─ 生成代码臃肿 → Ponytail + 代码最小化
├─ 多轮历史长 → 新会话 + 记忆外置 + 记忆文件 compress
└─ MCP 工具多/描述长 → caveman-shrink + 减少工具数量
```

## 六、推荐组合（按场景）

| 场景 | 推荐栈 |
|------|--------|
| 日常 Cursor 编码 | Ponytail（全局 rule）+ Caveman（按需）+ `.cursorignore` |
| 长链路 Agent / 大量日志 | Headroom proxy + 渐进式披露 + 日志 tail |
| 多项目长期记忆 | claude-mem 或 MemPalace + `/caveman-compress` 记忆文件 |
| 团队规范 + 少废话 | karpathy-skills + Ponytail + Caveman lite |

## 输出模板（给用户建议时）

```markdown
## 诊断
- 主要瓶颈：[input / output prose / output code]
- 估计影响：[高/中/低]

## 立即行动（≤3 条）
1. …
2. …

## 可选进阶
- …

## 不要做的
- …
```

## 参考文件

| 文件 | 内容 |
|------|------|
| [references/three-pillars.md](references/three-pillars.md) | 三件套原理、对比、叠加方式 |
| [references/input-optimization.md](references/input-optimization.md) | 输入侧完整策略 |
| [references/output-optimization.md](references/output-optimization.md) | 输出 prose 策略 |
| [references/code-minimization.md](references/code-minimization.md) | 代码最小化与 Ponytail 梯 |
| [references/workflow-patterns.md](references/workflow-patterns.md) | 工作流、记忆、MCP、Subagent |
| [references/tools-install.md](references/tools-install.md) | 三件套及工具安装命令 |
| [examples/before-after.md](examples/before-after.md) | 前后对比示例 |

## 外部权威来源

- Ponytail: https://github.com/DietrichGebert/ponytail
- Caveman: https://github.com/JuliusBrussee/caveman
- Headroom: https://github.com/headroomlabs-ai/headroom
- Headroom vs Caveman 对比: https://pasqualepillitteri.it/en/news/5700/headroom-vs-caveman-compress-input-cut-output
- Karpathy skills: https://github.com/forrestchang/andrej-karpathy-skills
