# AI Coding 工作流 5 方对比

> 基于 Cohort 004 课程 + 全网搜索资料 (2026-06)

---

## 一、一句话定位

| 工作流 | 定位 | Stars |
|---|---|---|
| **Matt Pocock 模式** | 对话驱动、人机共识优先、轻量无框架 | 113K+ |
| **Superpowers** | Agent Skills 系统，社区共享、可组合 | 社区项目 |
| **OpenSpec** | SDD 框架，delta spec 追踪，brownfield 优先 | ~39K |
| **Spec Kit** | GitHub 官方 SDD，7 阶段流水线，重治理 | 108K+ |
| **GSD** | 上下文工程流，每阶段刷新窗口，原子化任务 | 63.7K+ |

---

## 二、工作流哲学

| | Matt Pocock | Superpowers | OpenSpec | Spec Kit | GSD |
|---|---|---|---|---|---|
| **哲学** | 先共识再实现 | 先给 Agent 技能再做事 | 先写 Spec 再实现 | Constitution 宪法 + 7 阶段 | 每 Phase 刷新上下文 |
| **驱动方式** | 对话驱动 (Grill Me) | Skill 驱动 | 文档驱动 (Spec) | 流水线驱动 | 上下文工程驱动 |
| **入口** | `/grill-me` → `/2prd` | `/superpowers:xxx` | `openspec init` | `/speckit.xxx` | `/gsd:discuss-phase N` |
| **人类角色** | Griller + Reviewer | Skill 设计者 | Spec Review 审批者 | 每阶段确认者 | Plan/Verify 决策者 |

---

## 三、文档体系

| | Matt Pocock | Superpowers | OpenSpec | Spec Kit | GSD |
|---|---|---|---|---|---|
| **核心产物** | PRD.md + Plan.md | Skill 文件 (.md) | changes/ 目录 | spec.md + plan.md + tasks.md | PROJECT.md + ROADMAP.md + STATE.md |
| **格式** | Markdown | Markdown (frontmatter) | YAML + Markdown | Markdown | XML 原子化任务 |
| **持久化** | 项目内 plans/ prd/ | .claude/skills/ | changes/ (项目内) | specs/ (项目内) | 项目根目录 |
| **跨项目复用** | 有限 (Skills 可复用) | ✅ 核心优势 — 社区共享 | 有限 | 105+ 社区扩展 | 有限 |

---

## 四、分阶段方式

| | Matt Pocock | Superpowers | OpenSpec | Spec Kit | GSD |
|---|---|---|---|---|---|
| **拆分哲学** | Tracer Bullets 垂直切片 | 无固定阶段 (Skill 组合) | Spec → Implement → Verify | Constitution → Specify → Clarify → Plan → Tasks → Analyze → Implement | Discuss → Plan → Execute → Verify → Complete |
| **阶段数** | 2-3 个 Phase | 灵活 | 3 步 | 7 步刚性流水线 | 5 步 + Quick Mode |
| **每阶段验证** | Phase 1 就打穿全栈 | 依赖 Skill 质量 | delta spec 对比 | Analyze 一致性检查 | Verify (用户验收) |
| **不可逆决策保护** | ✅ 核心原则 — 从可逆方案起步 | ❌ 无内置机制 | delta 追踪 | Constitution 宪章 | ❌ 弱 |

---

## 五、上下文管理

| | Matt Pocock | Superpowers | OpenSpec | Spec Kit | GSD |
|---|---|---|---|---|---|
| **核心策略** | Smart Zone / Dumb Zone 理论 | Subagent 上下文隔离 | 50KB spec 限制 | 无显式策略 | **每 Phase 刷新 context** |
| **/clear 态度** | 积极 /clear，每 Phase 新会话 | 按需 | 无特定 | 无特定 | 内置自动刷新 |
| **/compact 态度** | 反模式，最多 1 次 | 按需 | - | - | 替代方案 (刷新) |
| **Handoff** | ✅ 核心机制 | - | - | - | MILESTONE 机制 |
| **渐进式呈现** | ✅ Skills + Context Pointers | ✅ 核心设计 | - | - | 部分 (PROJECT.md → ROADMAP.md 层级) |

---

## 六、Agent 协作

| | Matt Pocock | Superpowers | OpenSpec | Spec Kit | GSD |
|---|---|---|---|---|---|
| **多 Agent** | 手动 Sub-Agent | ✅ 多 Agent 协作 | 有限 | 有限 | ✅ 波次并行执行 |
| **并行能力** | 无 (单 Agent 顺序) | ✅ | ❌ | ❌ | ✅ Wave-based |
| **平台锁定** | Claude Code 原生，Codex/Cursor 适配 | 5+ 平台 | 25+ 工具 | 30+ Agent | 10+ 平台 |
| **AFK 能力** | ✅ 核心卖点 | 部分 | 有限 | 有限 | ✅ 原子化任务 |

---

## 七、质量控制

| | Matt Pocock | Superpowers | OpenSpec | Spec Kit | GSD |
|---|---|---|---|---|---|
| **测试** | ✅ TDD (CLAUD.md "service 必须有测试") | 依赖 Skill 内容 | - | - | ❌ 未内置 |
| **Code Review** | ✅ Review Skill | ✅ code-review skills | ❌ | ❌ | Verify 阶段 |
| **质量门禁** | Type check + 测试通过 | 依赖 Skill | opsx:verify | Analyze + Implement 验证 | Verify 用户验收 |
| **失效恢复** | /handoff + /rewind | - | delta spec 回滚 | Constitution 预防 | 独立 Phase 隔离 |

---

## 八、适用场景

| | Matt Pocock | Superpowers | OpenSpec | Spec Kit | GSD |
|---|---|---|---|---|---|
| **单人/团队** | 单人最佳 | 单人 + 团队 | 团队 | 团队 + 组织 | 单人 + 小团队 |
| **项目规模** | 小～中型 | 小～中型 | 中～大型 | 中～大型 | 中型 |
| **Greenfield** | ✅ | ✅ | ❌ (主 brownfield) | ✅ (主 greenfield) | ✅ |
| **Brownfield** | ✅ (explore 优先) | ✅ | ✅ 核心设计 | ⚠️ 2026 年加强 | ⚠️ |
| **学习曲线** | 低 (对话自然) | 低～中 | 中 | 高 (7 阶段流水线) | 中 |
| **企业就绪** | ❌ (无合规认证) | ❌ | ❌ | ❌ (社区项目) | ❌ |

---

## 九、核心优劣

### Matt Pocock 模式
- **优势**: 最自然、学习成本最低、人机共识最扎实、可逆决策优先、Smart Zone 管理最强
- **劣势**: 无框架标准化、团队协作弱、不适合超大型项目、依赖 Claude Code 生态

### Superpowers
- **优势**: 社区共享生态最开放、Skills 可组合性强、多 Agent 支持最好
- **劣势**: 标准化程度低、无内置阶段管理、学习曲线陡（需要理解 Skill 体系）

### OpenSpec
- **优势**: Brownfield 设计最成熟、delta spec 追踪最强、跨平台最广 (25+)、YAML 格式标准化
- **劣势**: 文档驱动缺少对话共识阶段、质量控制弱、学习曲线中等

### Spec Kit
- **优势**: GitHub 官方背书、社区规模最大 (108K+)、7 阶段流水线最完善、Constitution 机制独特、105+ 扩展
- **劣势**: 重流程 (7 步是负担)、对话共识极弱、学习曲线最高、主要为 greenfield 设计

### GSD
- **优势**: 上下文管理理念最先进 (每 Phase 刷新)、原子化任务拆分最细、并行执行最强 (Wave-based)、被大厂工程师验证
- **劣势**: XML 格式不直观、质量控制弱 (无内置 TDD)、文档体系复杂、平台生态较窄

---

## 十、选择建议

```
你的场景                      → 推荐

单人 + 快速迭代 + 喜欢对话     → Matt Pocock 模式
团队协作 + 需要标准化          → OpenSpec
团队 + GitHub 生态 + 重治理    → Spec Kit
追求上下文效率 + 大任务拆分    → GSD
社区共享 + 多 Agent 编排       → Superpowers

组合拳 (推荐):
  Matt Pocock (Grill Me + 共识) + GSD (多 Phase 并行执行)
  Matt Pocock (PRD) + Spec Kit (Constitution + Tasks)
```
