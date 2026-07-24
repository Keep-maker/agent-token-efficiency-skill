# 工作流与工程层 Token 优化

## 1. Skill / Rule 架构

| 模式 | 说明 |
|------|------|
| **按需 Skill** | 大段领域知识放 Skill，用时 `@skill`，非 always-on |
| **薄 Rules** | 全局 Rule 只保留 invariant（安全、风格 3 条） |
| **项目 vs 全局** | 项目 Skill 在 `.agents/skills/`；通用在 `~/.agents/skills/` |
| **disable-model-invocation** | 仅用户 `@` 时加载的重 Skill 可设此 flag |

## 2. 会话与记忆

| 模式 | 说明 |
|------|------|
| **任务边界开新 chat** | 无关上下文不继承 |
| **claude-mem** | Hook 捕获 → 压缩 → 下会话注入摘要；`mem-search` 三层检索 |
| **MemPalace** | wings/rooms 结构化；mine 一次、search 语义检索 |
| **thread 摘要** | 长会话手动「总结上文，开新 thread 继续」 |

## 3. 工具调用模式

| 模式 | 说明 |
|------|------|
| **并行 tool calls** | 多 Read/Grep 同一轮发出 |
| **先搜后读** | Grep/Glob 定位 → Read 片段 |
| **避免 retry 循环** | 同一失败命令不 blind retry 5 次 |
| **Shell 输出 discipline** | 大命令加 `\| tail` / `\| head` |

## 4. Subagent / 多 Agent

| 模式 | 说明 |
|------|------|
| **Explore subagent** | 广度搜索不回流原始 500 文件列表 |
| **固定 deliverable** | 「返回最多 10 条 finding + path」 |
| **Command/Subgraph**（LangGraph） | 状态隔离，父图只收结构化结果 |

## 5. 模型与推理

| 模式 | 说明 |
|------|------|
| **任务匹配模型** | 简单 refactor 用 fast model；架构才用 strong |
| **限制 thinking 泄露** | 部分模型 thinking token 另计；避免无谓 long CoT |
| **Structured output** | JSON mode / schema → 短且可解析 |

## 6. 仓库与 IDE

| 模式 | 说明 |
|------|------|
| `.cursorignore` | 同 .gitignore 思路，减索引 noise |
| 小 PR / 小 diff | 审查上下文更小 |
|  monorepo  scoped | 只打开子包 workspace |

## 7. 文档与产物

| 模式 | 说明 |
|------|------|
| **用户未要求不写 md** | 避免 Agent 主动写 SUMMARY.md |
| **canvas vs chat** | 大表放 canvas/artifact，chat 给链接 |
| **CI 日志** | 失败时才拉完整 log |

## 8. 质量门禁（省无效 token）

| 模式 | 说明 |
|------|------|
| **TDD**（obra/superpowers） | 少「写错→重写」循环 |
| **明确 DoD** | 「lint 过 + 单测绿」减少发散 |
| **Plan then execute** | 大改前先 5 行 plan，避免走错方向 |

## 9. 反模式汇总

- 每轮让 Agent「总结一下我们聊了什么」
- 10 个 always-on Skills 叠满
- 全仓库 `@Codebase` 问一个小函数
- 用图片问本可用 grep 解决的文本问题
- 同一配置在 CLAUDE.md + AGENTS.md + Rules 三处重复

## 10. 度量

| 工具 | 用途 |
|------|------|
| `/caveman-stats` | 会话 output 节省 |
| `headroom perf` / dashboard | input 压缩比 |
| `/ponytail-gain` | 基准 impact 参考 |
| Provider usage dashboard | 账单验证 |
