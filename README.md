# AI 功能测试用例生成 Skill

基于 **原型图（截图/设计稿）** 与 **需求文档（PRD）** 自动生成功能测试用例的 [Cursor Agent Skill](https://cursor.com/docs/agent/skills)。输出 Markdown 文档、Excel 表格，并支持导出 XMind 脑图。

## 功能特性

- **双类型用例**：功能点用例（单模块 CRUD/校验/权限/状态）+ 业务链路用例（跨模块 E2E）
- **全面覆盖**：需求规则、UI 元素、状态迁移、角色权限矩阵均可追溯
- **八大设计方法**：等价类、边界值、场景法、错误推测、因果图、判定表、正交试验、状态迁移
- **可执行步骤**：步骤写到「页面 / 控件文案 / 输入值」粒度，测试同事不看原型也能执行
- **多格式输出**：`.md` + `.xlsx`（双 Sheet），按需转 XMind

## 适用场景

在 Cursor 对话中提供以下任一输入，并说明「生成测试用例」：

- 原型图 / 设计稿 / 交互稿截图
- PRD、功能说明、用户故事、验收标准
- 业务规则、接口文档

Agent 将按 Skill 工作流完成需求分析 → 测试点盘点 → 用例生成 → 文件导出。

## 目录结构

```
.
├── SKILL.md                 # Skill 主文档（工作流、输出格式、质量闸）
├── README.md                # 本文件
├── reference.md             # 参考文档索引
├── examples.md              # 完整示例（用户管理、订单审批等）
├── reference/               # 分主题参考文档
│   ├── methods.md           # 八大测试方法详解
│   ├── coverage.md          # 覆盖维度与风险节点
│   ├── chain-design.md      # 业务链路设计规范
│   ├── validation.md        # 校验目录与优先级建议
│   ├── test-data.md         # 测试数据模板
│   └── review-checklist.md  # 用例评审清单
└── scripts/                 # 格式转换脚本
    ├── md_testcases_to_excel.py   # Markdown → Excel
    ├── md_testcases_to_xmind.py   # Markdown → XMind
    └── excel_to_xmind.py          # Excel → XMind
```

## 快速开始

### 1. 安装 Skill

将本目录放入 Cursor Skills 路径，例如：

```
~/.cursor/skills/ai-functional test case/
```

或在 Cursor 中通过 Skill 配置引用 `SKILL.md`。

### 2. 在对话中使用

向 Agent 提供原型截图和/或 PRD，例如：

> 请根据附件原型图和 PRD，生成「用户管理」模块的功能测试用例。

Agent 将自动输出：

| 文件 | 说明 |
|------|------|
| `模块名-功能点测试用例.md` | 需求分析、测试点清单、功能点用例、追踪矩阵 |
| `模块名-业务链路测试用例.md` | 跨模块 E2E 链路用例（如有） |
| `模块名-功能测试用例.xlsx` | 合并 Excel（功能点 + 业务链路两个 Sheet） |

### 3. 安装脚本依赖

```bash
pip install openpyxl
```

XMind 导出脚本可能还需：

```bash
pip install xmind
```

## 格式转换脚本

以下脚本**不会**在生成用例时自动调用，需在对话中显式请求或手动执行。

### Markdown → Excel

```bash
python scripts/md_testcases_to_excel.py <testcases.md> [-o output.xlsx]
```

支持多个 `.md` 合并；自动识别功能点表与业务链路表，写入不同 Sheet。

### Markdown → XMind

```bash
python scripts/md_testcases_to_xmind.py <testcases1.md> [testcases2.md ...] [-o output.xmind] [--group-by 模块|优先级|类型]
```

### Excel → XMind

```bash
python scripts/excel_to_xmind.py <testcases.xlsx> [-o output.xmind] [--md] [--group-by 模块|优先级|类型]
```

- 每个 Excel Sheet 对应 XMind 一级分支
- `--md` 可同时输出合并 Markdown

## 输出规范摘要

### 功能点用例

- **ID**：`TC-001` 起，3 位递增，模块内连续不跳号
- **列**：模块 | ID | 标题 | 优先级 | 类型 | 前置条件 | 步骤 | 测试数据 | 预期结果 | 备注/覆盖点

### 业务链路用例

- **ID**：`E2E-001` 起，与 TC 编号独立
- **步骤格式**：`[模块|角色] 具体操作…`
- **链路名称**：`节点1 → 节点2 → 节点3`

### 覆盖要求（Coverage Contract）

- 每条需求规则 → 至少 1 正向 + 1 反向（关键规则）
- 每个可交互 UI 元素 → 至少 1 条用例
- 角色权限矩阵中每个 ❌ → 无权限拦截用例
- 输出须含：规则清单、测试点清单、需求追踪矩阵、覆盖率统计

## 参考文档

| 文档 | 内容 |
|------|------|
| [SKILL.md](SKILL.md) | 完整工作流与输出模板 |
| [examples.md](examples.md) | 用户管理、订单审批完整示例 |
| [reference/methods.md](reference/methods.md) | 八大测试方法 |
| [reference/coverage.md](reference/coverage.md) | 覆盖维度与风险节点 |
| [reference/chain-design.md](reference/chain-design.md) | 链路复杂度评分与设计 |
| [reference/validation.md](reference/validation.md) | 校验与优先级 |
| [reference/test-data.md](reference/test-data.md) | 测试数据预设 |
| [reference/review-checklist.md](reference/review-checklist.md) | 评审清单模板 |

## 示例预览

功能点用例（节选）：

| 模块 | ID | 标题 | 优先级 | 类型 |
|------|-----|------|--------|------|
| 用户管理 | TC-001 | 新增用户成功 | P0 | 功能 |
| 用户管理 | TC-002 | 新增用户-用户名为空 | P0 | 校验 |
| 用户管理 | TC-020 | 普通用户不可见操作按钮 | P0 | 权限 |

业务链路用例（节选）：

| 链路ID | 链路名称 | 优先级 |
|--------|----------|--------|
| E2E-001 | 新增用户→登录→创建订单→审批通过 | P0 |

完整示例见 [examples.md](examples.md)。

## 许可证

本 Skill 为个人/团队测试辅助工具，按需修改 `reference/` 下的参考文档以积累项目经验（Skill 支持「自我进化」：每次生成后可提炼新模式写入 reference）。
