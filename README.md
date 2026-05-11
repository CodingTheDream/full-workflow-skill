# FullWorkflow Skill — AI Agent 一键式全流程开发

一键完成从 brainstorming 到实现到验证的完整开发流程。自动判断复杂度并选择最优执行策略。

适用于任何支持 subagent 编排的 AI 编程助手（Claude Code、Cursor、Windsurf 等）。

## 工作流程

```
用户需求 → Brainstorming → 复杂度分析 → 实现 → 验证 → 完成
```

1. **Brainstorming** — 通过协作对话将想法转化为设计 spec
2. **How-to-do** — 基于 60% 上下文红线规则自动判断复杂度
3. **实现** — 根据复杂度自动选择最优执行策略：
   - 简单任务 → TDD 直做
   - 复杂任务 → Writing Plans → TDD / Executing Plans / Subagent-Driven / Parallel
4. **验证** — Code Review + Verification 并行执行，失败自动 rework（最多 3 次）

## 核心设计

- **全 Subagent 模式** — 所有阶段在 subagent 中执行，主会话仅做编排，最小化上下文消耗
- **60% 红线** — 单个 subagent 预估上下文用量超过 60% → 自动拆分
- **自动 Rework** — 验证失败自动派修复 subagent，无需人工干预
- **渐进式披露** — subagent 按需读取 skill 文件，不一次性加载全部内容
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
2. 分析复杂度，选择最优执行策略
3. 执行实现
4. 并行运行 Code Review 和 Verification
5. 如有问题自动修复，最终交付

## 文件结构

```
skills/full-workflow/
├── SKILL.md                          # 主编排逻辑
├── brainstorming-prompt.md           # Brainstorming subagent prompt
├── how-to-do-prompt.md               # 复杂度分析 subagent prompt
├── rework-prompt.md                  # Rework subagent prompt
└── referenced-skills/                # 内置依赖 skills
    ├── brainstorming/SKILL.md
    ├── writing-plans/SKILL.md
    ├── test-driven-development/SKILL.md
    ├── executing-plans/SKILL.md
    ├── subagent-driven-development/SKILL.md
    ├── dispatching-parallel-agents/SKILL.md
    ├── requesting-code-review/SKILL.md
    └── verification-before-completion/SKILL.md
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

## 致谢

- 执行策略和 subagent 编排模式来自 [obra/superpowers](https://github.com/obra/superpowers)

## License

MIT
