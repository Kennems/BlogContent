---
title: 'Claude Code Skills 完全指南'
date: 2026-07-19T17:30:00+08:00
lastmod: 2026-07-21T23:50:00+08:00
description: "Claude Code Skills 是扩展 Claude Code 能力的插件系统。本文记录了 111+ 个实用 Skills 的安装过程、功能介绍和使用场景，包括官方插件、社区热门技能和专项能力增强。"
image: img/cat.jpg
draft: false
categories: ["AI"]
tags: ["学习笔记", "AI", "Claude Code", "Skills", "效率工具"]
---

# Claude Code Skills 完全指南（111+ 个 Skills）

## 为什么要用 Skills？

Claude Code 本身已经很强大，但在实际开发中会遇到三个瓶颈：

1. **上下文不够**：一个复杂任务需要大量背景知识，但上下文窗口有限
2. **工作流缺失**：Claude Code 不知道你的团队用什么分支策略、怎么做 code review
3. **领域专精不足**：UI 设计、PPT 制作这类任务需要大量领域知识

Skills 就是解决这三个问题的插件系统。它本质上是一个包含 `SKILL.md` 的文件夹，通过 YAML frontmatter 定义触发条件。安装后，Claude Code 会根据你的意图自动选择合适的 Skill。

### 为什么不直接用 prompt？

| 方式 | 优点 | 缺点 |
|------|------|------|
| **直接 prompt** | 零成本、灵活 | 每次都要重复写、无法共享、容易遗漏 |
| **CLAUDE.md** | 项目级持久化 | 只能写规则，不能封装复杂工作流 |
| **Skills** | 可复用、可共享、按需加载 | 需要安装、有学习成本 |

**结论**：简单规则用 CLAUDE.md，复杂工作流用 Skills。

### 三级加载机制

Skills 采用懒加载设计，避免一次性塞满上下文：

| 层级 | 内容 | 加载时机 | 大小限制 |
|------|------|----------|----------|
| **元数据** | name + description | 始终在上下文 | ~100 词 |
| **SKILL.md 正文** | 详细指令 | Skill 触发时 | <500 行 |
| **附属资源** | scripts、references、assets | 按需加载 | 无限制 |

**为什么这样设计？** 元数据常驻是为了让 Claude Code 知道"有哪些 Skills 可用"，但正文只在需要时加载，节省 token。

## 安装方式

三种方式，按场景选择：

```bash
# 方式一：Claude Marketplace（推荐，官方审核）
claude plugin marketplace add <owner>/<repo>
claude plugin install <plugin-name>@<marketplace-name>

# 方式二：npx skills（社区 Skills，最常用）
npx skills add <owner>/<repo-name>

# 方式三：专用 CLI（少数 Skills 需要）
npm install -g ui-ux-pro-max-cli
uipro init --ai claude
```

**选择建议**：优先用 Marketplace（有安全审核），社区 Skills 用 `npx skills add`。

## Skill 选择决策树

面对 54+ 个 Skills，如何快速选到合适的？按这个流程走：

```
你要做什么？
├── 写代码
│   ├── 需要规划？ → planning-with-files
│   ├── 需要审查？ → code-review
│   ├── 需要调试？ → superpowers/systematic-debugging
│   └── 需要重构？ → code-simplifier
├── 做设计
│   ├── UI/UX？ → ui-ux-pro-max
│   ├── PPT？ → 见下方 PPT 选择表
│   └── 海报/艺术？ → canvas-design
├── 处理文档
│   ├── Word/PDF/Excel/PPT？ → docx/pdf/xlsx/pptx
│   └── 知识管理？ → graphify
├── 提升效率
│   ├── 省 token？ → rtk / caveman
│   ├── 跨会话记忆？ → claude-mem
│   └── 工程规范？ → agent-skills
└── 其他
    ├── 深度访谈？ → grillme
    └── 创建新 Skill？ → skill-creator
```

## 官方核心 Skills（12 个）

来自 Anthropic 官方 marketplace，质量有保障。

### 必装 Skills

| Skill | 功能 | 为什么选它 |
|-------|------|-----------|
| **skill-creator** | 创建、测试、优化其他技能 | 元技能，装了它才能造新 Skills |
| **code-review** | 5 个并行审查 Agent | 比单个 Agent 审查更全面，能发现更多问题 |
| **github** | GitHub API 完整访问（20+ 工具） | 比手动调 API 快 10 倍 |
| **code-simplifier** | 代码简化重构 | 专注于"减法"，不会顺手加功能 |
| **frontend-design** | 前端设计辅助 | 比通用 prompt 更懂 CSS/布局 |

### 文档处理 Skills

| Skill | 功能 |
|-------|------|
| **docx** | Word 文档处理（创建、编辑、模板） |
| **pdf** | PDF 处理（读取、合并、拆分、OCR） |
| **pptx** | PowerPoint 处理（创建、编辑、模板） |
| **xlsx** | Excel 电子表格处理 |

### 创意设计 Skills

| Skill | 功能 |
|-------|------|
| **algorithmic-art** | p5.js 算法艺术 |
| **canvas-design** | 视觉设计（海报、艺术） |
| **brand-guidelines** | Anthropic 品牌指南 |
| **theme-factory** | 主题样式工具 |

## 社区热门 Skills

### 任务规划：planning-with-files

**解决什么问题？** Claude Code 经常"跑偏"——做到一半忘记目标，或者重复已经做过的步骤。

**怎么解决？** 用 3 个文件强制结构化：

- `task_plan.md` — 任务阶段和进度（含复选框，Claude 必须勾选才能继续）
- `findings.md` — 调研结果（避免重复搜索）
- `progress.md` — 会话日志（跨会话可追溯）

**效果**：通过率从 6.7% 提升到 96.7%（官方测试数据）。

**安装**：`npx skills add OthmanAdi/planning-with-files`

### 编程原则：andrej-karpathy-skills

**为什么值得装？** Karpathy 的 4 条原则直接解决了 Claude Code 最常见的 4 个坏习惯：

| 原则 | 解决的问题 | 具体做法 |
|------|-----------|---------|
| **Think Before Coding** | 想到就写，不做规划 | 强制显式推理，陈述假设 |
| **Simplicity First** | 过度设计，加推测性功能 | 最小可行代码 |
| **Surgical Changes** | 顺手重构，改了不该改的 | 只碰必须碰的 |
| **Goal-Driven Execution** | 只给指令，不给成功标准 | 用结果定义任务 |

**安装**：`npx skills add andrej-karpathy/andrej-karpathy-skills`

### 开发工作流：superpowers（14 个技能）

一个 Skill 包含 14 个子技能，覆盖完整开发周期：

**核心技能**（最常用）：
- `test-driven-development` — TDD，先写测试再写代码
- `systematic-debugging` — 结构化调试，不瞎猜
- `brainstorming` — 头脑风暴，生成多个方案再选

**协作技能**：
- `dispatching-parallel-agents` — 并行派发多个 Agent
- `subagent-driven-development` — 用子 Agent 做具体实现
- `requesting-code-review` / `receiving-code-review` — 代码审查流程

**其他**：writing-plans、executing-plans、verification-before-completion、writing-skills、using-git-worktrees、finishing-a-development-branch

**安装**：`npx skills add obra/superpowers`

### UI/UX 设计：ui-ux-pro-max

**为什么选它？** 84 种 UI 风格 + 192 种调色板 + 74 种字体搭配，相当于一个随身设计系统。

**注意**：仓库名是 `nextlevelbuilder/ui-ux-pro-max-skill`（有 `-skill` 后缀）。

**安装**：
```bash
npm install -g ui-ux-pro-max-cli
uipro init --ai claude
```

### 网页访问：web-access（8.4k stars）

**解决什么问题？** Claude Code 默认无法访问网页，但很多任务需要查文档、搜 API。

**怎么解决？** 用 CDP（Chrome DevTools Protocol）模式，支持登录态，可以访问需要认证的页面。三层通道调度 + 浏览器 CDP + 并行分治。

**仓库**：[eze-is/web-access](https://github.com/eze-is/web-access)

**安装**：
```bash
# 方式一：Claude Marketplace（推荐）
claude plugin marketplace add eze-is/web-access
claude plugin install web-access@web-access

# 方式二：npx skills
npx skills add eze-is/web-access
```

## HTML PPT 生成 Skills

专门用于生成 HTML 演示文稿，比传统 PPT 更灵活（支持动画、响应式、代码高亮）。

### 按需求选择

| 你的需求 | 推荐 Skill | 为什么 | 安装 |
|---------|-----------|--------|------|
| 综合最佳 | **html-ppt-skill** | 36 主题、47 动画、演示者模式，功能最全 | `npx skills add lewislulu/html-ppt-skill` |
| 设计质量 | **huashu-design** | 20 种风格、MP4/GIF 导出，设计感最强 | `npx skills add alchaincyf/huashu-design` |
| Markdown 输入 | **ppt-svg-generator** | 5 种预设风格，从 Markdown 直接生成 | `npx skills add vigorX777/ppt-svg-generator` |
| PDF 导出 | **open-slide** | React 架构，部署友好 | `npx skills add 1weiho/open-slide` |
| PPTX 导出 | **Akxan/ppt-agent-skill** | 生成真正的 PPTX 文件 | `npx skills add Akxan/ppt-agent-skill` |
| 杂志风格 | **op7418/guizang-ppt-skill** | 中文杂志排版风格 | `npx skills add op7418/guizang-ppt-skill` |
| 麦肯锡风格 | **likaku/Mck-ppt-design-skill** | 咨询公司风格 | `npx skills add likaku/Mck-ppt-design-skill` |

**仓库链接**：
- [lewislulu/html-ppt-skill](https://github.com/lewislulu/html-ppt-skill) — 7.3k stars
- [alchaincyf/huashu-design](https://github.com/alchaincyf/huashu-design) — 21.8k stars
- [vigorX777/ppt-svg-generator](https://github.com/vigorX777/ppt-svg-generator) — 248 stars
- [1weiho/open-slide](https://github.com/1weiho/open-slide) — 5.9k stars

**完整索引**：[awesome-html-slide-skills](https://github.com/ToseaAI/awesome-html-slide-skills)

## 工程效率 Skills

### agent-skills（79.2k stars）— Google 工程师出品

**为什么值得装？** 24 个生产级工程技能，覆盖完整开发生命周期：

```
Define → Plan → Build → Verify → Review → Ship
```

**按阶段选择**：

| 阶段 | 包含的技能 |
|------|-----------|
| Define | api-and-interface-design, spec-driven-development |
| Plan | planning-and-task-breakdown, idea-refine |
| Build | incremental-implementation, frontend-ui-engineering |
| Verify | debugging-and-error-recovery, browser-testing-with-devtools |
| Review | code-review-and-quality, code-simplification |
| Ship | ci-cd-and-automation, shipping-and-launch |

**其他技能**：context-engineering, deprecation-and-migration, documentation-and-adrs, doubt-driven-development, git-workflow-and-versioning, observability-and-instrumentation, performance-optimization, security-and-hardening, source-driven-development, test-driven-development, using-agent-skills, interview-me

**安装**：`npx skills add addyosmani/agent-skills`

### claude-mem（87.8k stars）— 跨会话记忆

**解决什么问题？** Claude Code 每次对话都是"失忆"的，之前讨论的决策、调研的结论全部丢失。

**怎么解决？** 18 个技能实现记忆持久化，核心能力：知识存储（mem-search）、知识图谱（learn-codebase）、进度追踪（standup、timeline-report）。

**安装**：`npx skills add thedotmack/claude-mem`

### graphify（91.2k stars）— 代码知识图谱

**解决什么问题？** 大型代码库难以理解，文档分散在各处。

**怎么解决？** 将代码、文档、论文、图片、视频转换为可查询的知识图谱，支持语义搜索。

**安装**：`npx skills add Graphify-Labs/graphify`

## Token 优化 Skills

Claude Code 按 token 计费，这些 Skills 能显著降低成本：

| Skill | Stars | 效果 | 原理 | 安装 |
|-------|-------|------|------|------|
| **rtk** | 72.3k | 减少 60-90% token | 智能压缩输出 | `npx skills add rtk-ai/rtk` |
| **caveman** | 91.6k | 减少 65% token | "原始人"风格，去掉废话 | `npx skills add JuliusBrussee/caveman` |
| **headroom** | 60.9k | 减少 20% token | 压缩输出格式 | `npx skills add headroomlabs-ai/headroom` |

**仓库链接**：
- [rtk-ai/rtk](https://github.com/rtk-ai/rtk) — CLI proxy，单个 Rust 二进制文件
- [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman) — 为什么用很多 token 当少 token 能 trick
- [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom) — 压缩工具输出、日志、文件

**选择建议**：rtk 效果最好但可能影响可读性，caveman 适合不需要优雅输出的场景，headroom 最温和。

## 其他高价值 Skills

### taste-skill（65.1k stars）

**解决什么问题？** Claude 生成的内容"正确但无聊"——技术上没问题，但缺乏品味。

**怎么解决？** 赋予 AI "好品味"，防止生成模板化、无趣的内容。

**仓库**：[Leonxlnx/taste-skill](https://github.com/Leonxlnx/taste-skill)

**安装**：
```bash
# 方式一：npx skills（推荐）
npx skills add Leonxlnx/taste-skill

# 方式二：Claude Marketplace
claude plugin marketplace add Leonxlnx/taste-skill
claude plugin install taste-skill@Leonxlnx-taste-skill
```

### 学习资源

| Skill | Stars | 功能 | 仓库 |
|-------|-------|------|------|
| **awesome-claude-skills** | 68.4k | 1000+ 生产就绪 skills 汇总 | [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) |
| **claude-code-best-practice** | 63.2k | 从 vibe coding 到 agentic engineering | [shanraisshan/claude-code-best-practice](https://github.com/shanraisshan/claude-code-best-practice) |
| **learn-claude-code** | 71.8k | 从 0 到 1 构建类似 Claude Code 的 agent | [shareAI-lab/learn-claude-code](https://github.com/shareAI-lab/learn-claude-code) |

## 实战场景

### 场景 1：从零开发一个功能

```
你：帮我实现用户登录功能

Claude Code 会自动：
1. 调用 planning-with-files 制定计划
2. 调用 superpowers/test-driven-development 先写测试
3. 实现代码
4. 调用 code-review 审查
5. 调用 verification-before-completion 验证
```

### 场景 2：给老板做 PPT

```
你：帮我做一个关于 Q3 业绩的 PPT，麦肯锡风格

Claude Code 会：
1. 调用 Mck-ppt-design-skill 生成麦肯锡风格的 HTML PPT
2. 自动应用专业图表和布局
3. 输出可直接演示的 HTML 文件
```

### 场景 3：调试一个诡异的 bug

```
你：这个 API 偶尔返回 500，但本地无法复现

Claude Code 会：
1. 调用 systematic-debugging 结构化排查
2. 调用 observability-and-instrumentation 添加日志
3. 调用 browser-testing-with-devtools 模拟线上环境
```

## 最佳实践

### 1. 安装策略

**不要贪多**。装太多 Skills 会：
- 占用上下文（每个 Skill 的元数据常驻）
- 降低触发准确率（相似功能的 Skills 会冲突）
- 增加安全风险

**推荐优先级**：

| 优先级 | Skills | 理由 |
|--------|--------|------|
| **必装** | skill-creator, code-review, github, superpowers, planning-with-files | 覆盖 80% 的日常场景 |
| **推荐** | agent-skills, html-ppt-skill, ui-ux-pro-max | 特定领域深度需求 |
| **可选** | claude-mem, graphify, grillme | 按需安装 |

### 2. 使用技巧

- **触发词**：在 SKILL.md 的 description 中包含明确的触发词，提高触发准确率
- **渐进加载**：保持 SKILL.md 在 500 行以内，大内容放 references/
- **测试验证**：使用 skill-creator 的评估功能测试新技能

### 3. 安全注意事项

- **安全评估**：安装时注意查看 Security Risk Assessments
- **权限控制**：Skills 以完整 agent 权限运行，谨慎安装
- **来源可信**：优先选择知名作者或官方仓库的 skills

## 参考资源

- [Anthropic 官方 Skills 仓库](https://github.com/anthropics/skills)
- [awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) — 1000+ skills 汇总
- [awesome-html-slide-skills](https://github.com/ToseaAI/awesome-html-slide-skills) — HTML PPT skills 索引
- [agent-skills](https://github.com/addyosmani/agent-skills) — Google 工程师 24 个生产级技能
- [claude-mem](https://github.com/thedotmack/claude-mem) — 跨会话记忆持久化
- [graphify](https://github.com/Graphify-Labs/graphify) — 代码知识图谱
- [ui-ux-pro-max](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) — UI/UX 设计智能
- [planning-with-files](https://github.com/OthmanAdi/planning-with-files) — 文件式任务规划

---

## 附录：完整 Skills 清单（111 个）

### 一、Claude Marketplace 插件（12 个）

| 插件 | 版本 | 功能 |
|------|------|------|
| **andrej-karpathy-skills** | 1.0.0 | Karpathy 编程原则（4 大原则） |
| **code-review** | - | PR 代码审查（5 并行 Agent） |
| **code-simplifier** | 1.0.0 | 代码简化重构 |
| **frontend-design** | - | 前端设计辅助 |
| **github** | - | GitHub API 完整访问（20+ 工具） |
| **grillme** | - | 苏格拉底式深度访谈 |
| **planning-with-files** | 3.7.0 | 文件式任务规划（3-File 模式） |
| **ralph-loop** | 1.0.0 | 自循环执行 |
| **skill-creator** | - | 创建/优化技能（元技能） |
| **superpowers** | 6.1.1 | 14 个开发工作流技能库 |
| **ui-ux-pro-max** | 2.11.0 | UI/UX 设计智能（84 种风格） |
| **web-access** | 2.5.2 | 网页浏览和搜索（CDP 模式，8.4k⭐） |

---

### 二、.agents/skills/ 目录（51 个）

#### 🎯 工程效率类（24 个 - agent-skills）

| Skill | 功能 |
|-------|------|
| api-and-interface-design | API 和接口设计 |
| browser-testing-with-devtools | 浏览器测试（Chrome DevTools） |
| ci-cd-and-automation | CI/CD 自动化 |
| code-review-and-quality | 代码审查和质量 |
| code-simplification | 代码简化 |
| context-engineering | 上下文工程 |
| debugging-and-error-recovery | 调试和错误恢复 |
| deprecation-and-migration | 废弃和迁移 |
| documentation-and-adrs | 文档和决策记录 |
| doubt-driven-development | 怀疑驱动开发 |
| frontend-ui-engineering | 前端 UI 工程 |
| git-workflow-and-versioning | Git 工作流 |
| idea-refine | 想法精炼 |
| incremental-implementation | 增量实现 |
| interview-me | 访谈式需求提取 |
| observability-and-instrumentation | 可观测性 |
| performance-optimization | 性能优化 |
| planning-and-task-breakdown | 任务规划 |
| security-and-hardening | 安全加固 |
| shipping-and-launch | 发布和部署 |
| source-driven-development | 源码驱动开发 |
| spec-driven-development | 规格驱动开发 |
| test-driven-development | TDD |
| using-agent-skills | Skills 发现（元技能） |

#### 💾 记忆持久化类（18 个 - claude-mem）

| Skill | 功能 |
|-------|------|
| babysit | 监控 PR/审查直到合并 |
| cloud-sync | cmem.ai Pro 云同步 |
| claude-code-plugin-release | 插件发布工作流 |
| design-is | 设计审计（Dieter Rams 原则） |
| do | 执行计划 |
| how-it-works | claude-mem 工作原理 |
| knowledge-agent | 知识库构建 |
| learn-codebase | 学习代码库 |
| make-plan | 制定计划 |
| mem-search | 记忆搜索 |
| oh-my-issues | Issue 整理和聚类 |
| pathfinder | 架构路径发现 |
| smart-explore | 智能代码探索（tree-sitter） |
| standup | 站会报告 |
| timeline-report | 时间线报告 |
| weekly-digests | 周报生成 |
| what-the | 技术解释（通俗易懂） |
| wowerpoint | PPT 生成（kawaii 风格） |

#### 🎨 HTML PPT 类（8 个）

| Skill | 功能 |
|-------|------|
| html-ppt | HTML PPT Studio（36 主题+47 动画） |
| huashu-design | 花叔设计（高保真原型、MP4 导出） |
| ppt-svg-generator | Markdown→SVG→PPT |
| create-slide | 创建幻灯片（open-slide） |
| create-theme | 创建主题（open-slide） |
| current-slide | 当前幻灯片定位 |
| slide-authoring | 幻灯片编写指南 |
| apply-comments | 应用评论标记 |

#### 🧠 代码知识图谱（1 个）

| Skill | 功能 |
|-------|------|
| graphify | 代码→知识图谱（语义搜索） |

---

### 三、.claude/skills/ 符号链接（24 个）

| Skill | 功能 |
|-------|------|
| ask-matt | 询问哪个 skill 适合当前场景 |
| code-review | 代码审查（双轴：Standards + Spec） |
| codebase-design | 深度模块设计 |
| diagnosing-bugs | Bug 和性能回归诊断 |
| domain-modeling | 领域建模 |
| find-skills | 发现和安装 skills |
| grill-me | 苏格拉底式访谈（精简版） |
| grill-with-docs | 带文档的访谈（生成 ADR） |
| grilling | 苏格拉底式访谈（完整版） |
| handoff | 会话交接文档 |
| implement | 基于规格实现功能 |
| improve-codebase-architecture | 代码库架构改进 |
| prototype | 构建一次性原型 |
| research | 调研并生成 Markdown 报告 |
| resolving-merge-conflicts | 解决合并冲突 |
| setup-matt-pocock-skills | 配置工程 skills |
| tdd | 测试驱动开发 |
| teach | 教授新技能或概念 |
| to-spec | 将对话转为规格 |
| to-tickets | 将计划转为工单 |
| triage | Issue 分诊 |
| wayfinder | 大型工作规划 |
| web-access | 网页访问 |
| writing-great-skills | 编写优秀 skills 指南 |

---

### 四、.claude/skills/ Android 相关（24 个）

| Skill | 功能 |
|-------|------|
| adaptive | 自适应布局 |
| agp-9-upgrade | AGP 9 升级 |
| android-cli | Android CLI |
| android-intent-security | Android Intent 安全 |
| anthropic-agent-skills | Anthropic Agent Skills |
| appfunctions | 应用功能 |
| camerax | CameraX |
| display-glasses-with-jetpack-compose-glimmer | 显示眼镜（Jetpack Compose） |
| edge-to-edge | 边到边显示 |
| engage-sdk-integration | Engage SDK 集成 |
| migrate-xml-views-to-jetpack-compose | 迁移 XML 到 Compose |
| navigation-3 | Navigation 3 |
| perfetto-sql | Perfetto SQL |
| perfetto-trace-analysis | Perfetto 追踪分析 |
| play-billing-library-version-upgrade | Play Billing 升级 |
| r8-analyzer | R8 分析器 |
| styles | 样式 |
| testing-setup | 测试设置 |
| ui-ux-pro-max-skill | UI/UX 设计智能 |
| verified-email | 验证邮箱 |
| wear-compose-m3 | Wear Compose M3 |
| claude-plugins-official | Claude 官方插件 |
| anthropic-agent-skills | Anthropic Agent Skills |

---

### 五、配置的 Marketplaces（7 个）

| Marketplace | 来源 |
|-------------|------|
| anthropic-agent-skills | Anthropic 官方 Skills |
| claude-plugins-official | Anthropic 官方插件 |
| karpathy-skills | Karpathy 编程原则 |
| planning-with-files | 文件式任务规划 |
| superpowers-dev | 14 个开发工作流 |
| ui-ux-pro-max-skill | UI/UX 设计智能 |
| web-access | 网页访问 |

---

## 📊 统计总览

| 类别 | 数量 |
|------|------|
| **Claude Marketplace 插件** | 12 个 |
| **.agents/skills/** | 51 个 |
| **.claude/skills/ 符号链接** | 24 个 |
| **.claude/skills/ Android** | 24 个 |
| **总计** | **111 个 Skills** |

---

*本文基于 2026-07-21 的实际安装和调研整理，Skills 生态持续更新中。*
