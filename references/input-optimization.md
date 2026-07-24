# 输入侧 Token 优化（INPUT）

Agent 账单里 **input 常占 60–90%**，尤其是：读大文件、跑测试/构建、grep 海量结果、长对话历史。

## A. 基础设施层（Headroom）

| 手段 | 说明 |
|------|------|
| `headroom wrap <agent>` | 透明代理，自动压缩 tool output / logs / files |
| `headroom proxy` | 自定义 Base URL，零改代码 |
| `headroom mcp install` | Agent 主动调用 compress/retrieve |
| CCR | 压缩存本地，模型按需 retrieve 全文 |

**适用：** 构建日志、大型 JSON API 响应、1000+ 行文件 dump。

## B. 上下文选择（用户 + Agent 协作）

| 手段 | 做法 | 省 token 原理 |
|------|------|---------------|
| 精准 @ | 只 @ 单文件或子目录 | 不注入无关文件 |
| 禁止粘贴全文 | 「看 `src/foo.ts` 第 40 行」 | 让 Agent 自己 Read 必要段 |
| `.cursorignore` | 排除 node_modules、dist、.git、大 CSV | 索引/搜索不扫 junk |
| 分会话 | 新任务新 chat | 历史不滚雪球 |
| 摘要先行 | 先让 Agent 列 outline 再深入 | 避免一次读 50 文件 |

## C. 工具输出治理

| 手段 | 做法 |
|------|------|
| 日志 tail | `tail -n 100` / 只取 ERROR |
| 结构化过滤 | jq 取字段；grep 精确 pattern |
| 分页读取 | Read offset/limit，不全文件 |
| 避免重复读 | 同文件同会话不反复 Read |
| 并行 Read | 一轮读多文件，减少「读-想-再读」轮次 |

## D. 记忆与规则文件

| 手段 | 说明 |
|------|------|
| `/caveman-compress <file>` | CLAUDE.md/AGENTS.md 改 caveman 体，**每会话永久少 input** |
| 精简 Rule | 每条 always-on rule 每轮计费；合并、删 decorative |
| Skill 模块化 | 大段规范放 Skill，按需 @，不全塞 Rules |
| MemPalace / claude-mem | 历史外置；唤醒时只注入 ~170–500 token 相关片段 |

## E. MCP 与工具 schema

| 手段 | 说明 |
|------|------|
| `caveman-shrink` | 包装 MCP server，压缩 tool description |
| 减少工具数量 | 只暴露当前任务需要的 MCP tools |
| 短 description | tool 描述写「何时用」而非教程 |

## F. RAG / 检索

| 手段 | 说明 |
|------|------|
| 小 chunk + 合理 overlap | 避免单 chunk 过大 |
| 限制 top-k | 3–5 条通常够，10+ 易灌满 |
| 两阶段检索 | 先 title/summary 索引，再拉全文 |
| claude-mem 三层 | search → timeline → get_observations（批量 ID） |

## G. 反模式（浪费 input）

- 把整份 PR diff、整库 tree 贴进对话
- 每轮重复贴相同错误栈
- 开 20+ MCP servers「以防万一」
- 巨型 `AGENTS.md` 写满教程而非约束
- 截图代替可复制的 text/DOM

## 检查清单

- [ ] node_modules / dist 已在 ignore
- [ ] 记忆文件已 compress 或精简
- [ ] 大日志是否经 Headroom 或 tail
- [ ] 是否可用 @ 替代 paste
- [ ] 历史是否该开新会话
