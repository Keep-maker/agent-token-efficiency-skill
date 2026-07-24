# 代码输出最小化（OUTPUT code · Ponytail）

## 七级梯（写任何代码前）

```
1. 需要吗？        → 不需要就不写（YAGNI）
2. 库里有了吗？    → import/复用现有 util
3. 标准库？        → 不引第三方
4. 平台原生？      → HTML5 / OS API / 框架内置
5. 已装依赖？      → 不 duplicate 功能
6. 一行够吗？      → 一行解决
7. 最小可工作实现  → 仅满足 spec，不 golf
```

## 安全红线（永不因省 token 删除）

- 信任边界的 validation / sanitization
- 数据丢失防护（事务、confirm、backup）
- 安全相关（auth、CSRF、secret 不入库）
-  accessibility 必需属性
- 用户未要求时的错误处理**可以** minimal，但**不能** silent fail 于关键路径

## 常见 over-build → 最小替代

| 请求 | Over-build | Ponytail 选择 |
|------|------------|---------------|
| 日期选择 | flatpickr + wrapper + CSS | `<input type="date">` |
| 颜色选择 | 自定义 color picker 组件 | `<input type="color">` |
| 配置 | 三层 config class + factory |  flat dict / env |
| 工具函数 | 新 util 文件 80 行 | 内联 5 行或复用现有 |
| API 层 | Repository + Service + DTO 全套 | 单文件 handler 直到需要拆 |

## 与「代码 golf」的区别

| | Ponytail | Golf |
|--|----------|------|
| 目标 | 必要代码最少 | 字符最少 |
| 可读性 | 保持 | 常牺牲 |
| 安全 | 100% 基准测试 safe | 可能删 guard |
| 度量 | LOC + tokens + cost | 字符数 |

## Ponytail 命令

| 命令 | 用途 |
|------|------|
| `/ponytail [lite\|full\|ultra\|off]` | 调强度 |
| `/ponytail-review` | 审 diff  over-engineering |
| `/ponytail-audit` | 全仓审计 |
| `/ponytail-debt` | 记录 deferred  shortcut |

## 与 Caveman 协作

- **同一次回复**：Caveman 缩解释；Ponytail 缩 diff
- **不冲突**：Caveman 不改代码字节；Ponytail 不管 prose 风格

## 检查清单

- [ ] 新依赖是否真必要？
- [ ] 能否用原生/标准库？
- [ ] 新文件是否可合并到现有文件？
- [ ] 抽象是否被第二次使用？
- [ ] diff 是否仅覆盖用户请求范围？
