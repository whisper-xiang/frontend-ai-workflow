# AI 辅助前端工作流

轻量的前端开发流程，供 Cursor、Codex 使用。开发者给出最新需求，AI 更新完整需求文档和 TODO，按当前项目既有规范编码，跑完项目已有的 lint 与类型检查后勾选完成项。

日常只维护两类文档：需求文档和 TODO。任务只有未开始（`- [ ]`）和已完成（`- [x]`）。初版不做审批、变更台账、多人协作或自动调度。

行为以 [需求设计文档](AI%20辅助前端工作流%20·%20需求设计文档（MVP）.md) 为准，落地方式见 [MVP 设计方案](AI前端工作流-MVP设计方案_1.md)。

## 三个 skill

| Skill | 做什么 | 何时停 |
| --- | --- | --- |
| [req-init](.agents/skills/req-init/SKILL.md) | 把描述、文本、Markdown 或截图整理进当前有效需求 | 仅整理需求时，保存后停止 |
| [req-todo](.agents/skills/req-todo/SKILL.md) | 按任务规模检索规则与类似实现，列出可验证任务 | 仅拆任务时，保存后停止 |
| [req-change](.agents/skills/req-change/SKILL.md) | 修改、修复或移除已有功能时，同步需求、TODO，再按授权编码 | 仅同步文档时，在对应阶段停止 |

当前对话是入口，由目标项目的 `AGENTS.md` 选择路径。只调用 `req-init` 或 `req-todo` 时，不默认授权编码。

| 本次要求 | 路径 |
| --- | --- |
| 仅整理或更新需求 | req-init |
| 仅拆解或更新 TODO | 需求已是最新则 req-todo，否则 req-init → req-todo |
| 实现新需求 | req-init → req-todo → 编码与检查 → 勾选 |
| 修改、修复或移除并落实 | req-change |
| 继续已有 TODO | 需求与 TODO 仍适用则直接做指定任务；有新要求则转 req-change |

只有方案会额外改变业务行为、验收标准或任务边界，且开发者还没决定时，才需要询问。已明确的要求直接执行。

## 安装到业务项目

把三个 skill 目录复制到目标项目：

```text
.agents/skills/req-init/
.agents/skills/req-todo/
.agents/skills/req-change/
```

模板随 skill 放在各自目录内，skill 用相对路径读取：

- `.agents/skills/req-init/templates/requirement.md`
- `.agents/skills/req-todo/templates/todo.md`

目标项目已有 `AGENTS.md` 时保留原文，补上路由规则。建议写入：

1. 按请求确定授权到需求、TODO 还是实现，再选上表路径。仅整理或拆任务时在对应产出后停止；要求实现时连续推进，不在 skill 之间加确认。
2. 先检索适用规则、类似实现和拟复用接口，找到依据后开始，避免无关扫描。
3. 只有额外改变业务行为、验收标准或任务边界时询问。
4. 功能满足 TODO，且已运行项目已有的 lint、类型检查后勾选。缺少对应命令时，功能满足即勾选，并说明未执行的检查。已有命令但跑不起来时如实说明，不视为已通过。
5. 不再需要的任务删除或划线，不另建变更台账。

业务文档放在目标项目中，用相同文件名关联，不强制编号：

```text
docs/requirements/<需求名称>.md
docs/todo/<需求名称>.md
```

## 文档约定

需求文档包含目标、功能要求和验收标准。TODO 每条写清工作内容与完成条件；共用的改动范围和实现参考放在列表开头。

```markdown
# 需求名称 TODO

改动范围：相关页面及页面内组件。
实现参考：按需填写实际文件路径或符号。

- [ ] 任务一；对应完成条件。
- [ ] 任务二；对应完成条件。
```

功能不再需要时，从需求中删掉对应要求，删除或划去旧任务。若还要清代码，另写一条未开始的清理任务，做完再勾选。

## 作为 SkillBuddy 团队库

本仓库可直接添加到 SkillBuddy 团队库。根目录的 `team-library.yaml` 是清单，`id` 为 `frontend-ai-workflow`。三个 skill 的发布副本在 `skills/`，与 `.agents/skills/` 内容对应。SkillBuddy 只扫描 `skills/`，不会读取 `.agents/skills/`。

在 SkillBuddy 中填写本仓库的 Git 地址和分支即可。清单缺失或 `id`、`name`、`version` 不合法时，非空仓库不会被自动补齐。

## 仓库结构

```text
frontend-ai-workflow/
├── team-library.yaml
├── policies/organization.yaml
├── skills/
│   ├── req-init/
│   ├── req-todo/
│   └── req-change/
├── .agents/skills/
│   ├── req-init/
│   ├── req-todo/
│   └── req-change/
├── AI 辅助前端工作流 · 需求设计文档（MVP）.md
├── AI前端工作流-MVP设计方案_1.md
└── README.md
```
