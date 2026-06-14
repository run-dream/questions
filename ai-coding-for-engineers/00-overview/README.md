# AI Coding for Real Engineers — Cohort 004

> 课程学习笔记目录

---

## 📂 目录结构

```
ai-coding-for-engineers/
├── 00-overview/                  ← 课程概览（本文件）
├── 01-claude-code-basics/        ← Claude Code 基础操作（Lessons 8-16）
├── 02-context-management/        ← 上下文管理（Lessons 17-27）
├── 03-steering/                  ← Steering 引导（Lessons 28-35）
├── 04-prd-plan/                  ← PRD + Plan（Lessons 36-43）★ 核心
└── 05-implementation/            ← 实现（Lessons 44-45）
```

---

## 🗺️ 课程阶段总览

| **阶段** | **章节** | **核心内容** | **目录** |
|----------|---------|-------------|---------|
| Claude Code 基础 | Lessons 8-16 | 快捷键、Sub-Agent、权限、Bash 命令、IDE 集成 | `01-claude-code-basics/` |
| 上下文管理 | Lessons 17-27 | Smart/Dumb Zone、Compact/Clear/Handoff、Context Paranoia | `02-context-management/` |
| Steering 引导 | Lessons 28-35 | CLAUDE.md、Progressive Disclosure、Skills、Auto Memory | `03-steering/` |
| **PRD + Plan** | **Lessons 36-43** | **2PRD、Tracer Bullets、Vertical Slices、Plan 设计** | **`04-prd-plan/`** |
| 实现 | Lessons 44-45 | Phase 执行、Bug 修复、QA | `05-implementation/` |

---

## 📝 笔记使用说明

- **语言**：中文解释 + 英文技术术语
- **格式**：
  - `### <question>` 作为章节标题
  - Emoji 前缀的段落
  - 对比表格（Comparison Tables）
  - `---` 节分隔符
- **交叉引用**：笔记之间通过相对路径相互引用

---

## 🔗 完整文件列表

### 01-claude-code-basics/
- `1.claude-code-basics.md` — Claude Code 快捷键、Bash 命令决策树、终端交互
- `2.sub-agent.md` — Sub-Agent 模式、任务委派、并行执行
- `3.permissions.md` — 权限模型、文件系统访问、命令执行安全

### 02-context-management/
- `1.smart-dumb-zone.md` — Smart Zone / Dumb Zone 概念、LLM 上下文窗口衰减机制
- `2.compact-vs-clear-vs-handoff.md` — /compact、/clear、Handoff 三种上下文管理策略

### 03-steering/
- `1.memory-system.md` — 三层记忆架构（User/Project/Auto Memory）
- `2.agent-skills.md` — Agent Skills、Context Pointers、Progressive Disclosure
- `3.claude-md-guide.md` — CLAUDE.md 最佳实践、膨胀问题、结构指南

### 04-prd-plan/
- `1.prd-plan-workflow.md` — PRD + Plan 工作流概览、多会话拆分
- `2.tracer-bullets-vertical-slices.md` — Tracer Bullets 概念、Vertical Slices、Durable Decisions
- `3.prd-template-guide.md` — PRD 模板结构（Problem/Solution/User Stories）、2PRD Skill
- `4.plan-dos-and-donts.md` — Naive Plan vs Good Plan、Per-Phase 执行工作流

### 05-implementation/
- `1.phase-execution.md` — Phase 执行流程、Bug 修复、QA 策略

### 00-overview/
- `workflow-comparison.md` — Matt Pocock 模式 vs Superpowers vs OpenSpec vs Spec Kit vs GSD 五方对比

---

