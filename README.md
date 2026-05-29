# FullWorkflow Skill — AI Agent 一键式全流程开发

一键完成从 brainstorming 到实现到验证的完整开发流程。自动判断复杂度并选择最优执行策略。

适用于任何支持 subagent 编排的 AI 编程助手（Claude Code、Cursor、Windsurf 等）。

## 工作流程

```
用户需求
  → Brainstorming → Spec Review（≤5 轮，3 级升级策略）
  → 复杂度分析
  → 实现（简单：TDD 直做 / 复杂：Writing Plans → Plan Review → 执行）
  → Code Review + Verification（并行，业务逻辑覆盖优先）
  → Rework（≤5 轮，3 级升级策略）
  → 生成中文变更总结文档
  → 完成（由用户决定是否 commit）
```

## 与普通开发流程的区别

| 特性 | 说明 |
|------|------|
| **业务逻辑覆盖审查** | Code Review 首先逐条对照 spec 中的业务规则，确保每条规则都有实现覆盖 |
| **3 级升级策略** | 所有 Review-Retry 循环从扁平重复改为 3 级策略：1-2 轮正常 → 3-4 轮换方案 → 5 轮 Responsible Exit |
| **深度检查清单** | Rework 第 3 轮起强制执行深度检查：重读 spec、读完整源文件、验证假设、换方案 |
| **业务规则覆盖验证** | Verification 不仅跑测试，还逐条对照 spec 确认每条业务规则有测试覆盖 |
| **主动性标准** | Implementer 被要求主动检查关联影响和边界情况，而非只做最低限度工作 |
| **Responsible Exit** | 循环超限时提交结构化交接报告（已验证事实、卡住的问题、已尝试方案、推荐方向），而非简单报错 |
| **自检守则** | Code Reviewer 必须遵守自检表，防止浅审、跳过复杂模块、或给出模糊建议 |
| **中文文档** | 流程结束后自动生成中文变更总结报告，遵循中文技术文档写作规范 |
| **无 Git 操作** | 全流程不执行任何 git commit/add/push，最终提交完全由用户决定 |
| **渐进式披露** | 所有 skill 文件在 subagent 中按需读取，不加载到主会话，保持主对话干净 |

## 核心设计

- **全 Subagent 模式** — 所有阶段在 subagent 中执行，主会话仅做编排，最小化上下文消耗
- **薄路由层** — 主 SKILL.md 只保留路由调度逻辑，所有 Phase 细节抽取为独立 prompt 模板
- **60% 红线** — 单个 subagent 预估上下文用量超过 60% → 自动拆分
- **三层 Review + 业务逻辑优先** — Spec Review → Plan Review → Code Review，Code Review 首要审查业务逻辑覆盖
- **3 级升级策略** — Review-Retry 循环不再扁平重复，失败 2 次后强制换方案，5 次后 Responsible Exit
- **关键决策可交互** — 任何 subagent 遇到无法自行决定的关键问题都可以询问用户
- **自包含** — 所有依赖内置，复制即可使用，无需联网安装

## 安装

### Claude Code

```bash
# macOS / Linux
cp -r skills/full-workflow ~/.claude/skills/

# Windows (PowerShell)
Copy-Item -Recurse skills/full-workflow "$env:USERPROFILE\.claude\skills\"
```

安装后使用 `/full-workflow` 触发。

### 其他 AI 编程助手

将 `skills/full-workflow/SKILL.md` 配置为你所用工具的 skill/prompt 文件，根据工具的 subagent 机制适配调用方式。

## 使用示例

```
/full-workflow 我需要给项目添加用户认证功能
```

skill 会自动：
1. 启动 brainstorming subagent 与你对话，生成设计 spec
2. 独立 Reviewer 审查 spec，有问题则 rework
3. 分析复杂度，选择最优执行策略
4. 复杂任务会先生成 plan，Reviewer 审查后再执行
5. 并行运行 Code Review（业务逻辑覆盖优先）和 Verification（业务规则覆盖验证）
6. 如有问题自动修复（3 级升级策略，最多 5 轮）
7. 生成中文变更总结报告
8. 所有文件变更保留在工作区，由你决定是否 commit

## 文件结构

```
skills/full-workflow/
├── SKILL.md                          # 主编排路由层（薄）
├── brainstorming-prompt.md           # Brainstorming subagent prompt
├── spec-review-prompt.md             # Spec Review subagent prompt
├── how-to-do-prompt.md               # 复杂度分析 subagent prompt
├── tdd-direct-prompt.md              # TDD 直通实现 subagent prompt
├── writing-plans-prompt.md           # Writing Plans subagent prompt
├── plan-review-prompt.md             # Plan Review subagent prompt
├── execute-prompt.md                 # 执行 subagent prompt
├── code-review-prompt.md             # Code Review subagent prompt（业务逻辑覆盖优先）
├── verification-prompt.md            # Verification subagent prompt（业务规则覆盖验证）
├── rework-prompt.md                  # Rework subagent prompt（深度检查清单 + 3 级升级）
├── documentation-prompt.md           # Documentation subagent prompt
└── referenced-skills/                # 内置依赖 skills
    ├── brainstorming/SKILL.md
    ├── writing-plans/SKILL.md
    ├── test-driven-development/SKILL.md
    ├── executing-plans/SKILL.md
    ├── subagent-driven-development/SKILL.md
    │   └── implementer-prompt.md     # Implementer prompt（含主动性标准）
    ├── dispatching-parallel-agents/SKILL.md
    ├── requesting-code-review/SKILL.md
    ├── verification-before-completion/SKILL.md
    └── chinese-documentation/SKILL.md
```

## 依赖

内置了 [superpowers](https://github.com/obra/superpowers) 的以下 skills，无需额外安装：

- brainstorming
- writing-plans
- test-driven-development
- executing-plans
- subagent-driven-development
- dispatching-parallel-agents
- requesting-code-review
- verification-before-completion
- chinese-documentation（中文技术文档写作规范）

## 致谢

- 执行策略和 subagent 编排模式来自 [obra/superpowers](https://github.com/obra/superpowers)

## License

MIT
