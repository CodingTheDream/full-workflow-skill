# 选择性提取 nopua 子元素融合到 full-workflow，而非整体替换

我们评估了用 nopua（认知方法论 skill）整体替换 full-workflow 现有 Review-retry 机制的可能性，结论是**不整体替换**，而是从 nopua 中选择性提取 7 个子元素融入 full-workflow 的不同阶段。原因：nopua 解决的是"失败时怎么想"（认知方法论），而 full-workflow 的实际痛点是"reviewer 缺少信息"（上下文不足）。两者不在同一层面，整体引入 nopua 会增加约 29KB 的 prompt 体积却不解决核心问题，但 nopua 中的具体子元素对 full-workflow 的特定薄弱环节有针对性改善。

## Considered Options

### 选项 A：整体引入 nopua，替换现有 Review-retry 机制
- **放弃原因**：nopua 是纯文本认知方法论（~29KB prompt），没有结构化编排能力。替换意味着失去 subagent 角色分离、并行 review、独立 rework 等结构性优势。且 nopua 的认知升级依赖 agent 自我监控失败次数，不如 full-workflow 的编排层显式控制可靠。

### 选项 B：选择性提取 nopua 子元素（最终选择）
- nopua 中与"上下文不足"不相关的内容（三信念、五步水之道、七智慧传统）不引入，避免无谓的 prompt 膨胀和上下文污染。
- 只提取能直接解决已识别痛点的具体子元素。

### 选项 C：维持现状
- **放弃原因**：三个已识别的痛点（找不到核心业务问题、缺乏全局视野、越改越窄）在当前架构下无法自愈。

## 7 项提取改动

| # | 提取元素 | nopua 来源 | 融入目标 | 解决的痛点 |
|---|---|---|---|---|
| 1 | Spec+Plan 作为 Reviewer 输入 | 非 nopua，独立发现 | code-review-prompt.md | Reviewer 找不到核心业务问题 |
| 2 | Honest Self-Check（精简 3-5 条） | 自检表 | code-review-prompt.md | Reviewer 偷懒/浅审 |
| 3 | Cognitive Elevation 3 级策略 | 认知升级表 | SKILL.md 所有循环逻辑 | 扁平重复同一策略 10 次 |
| 4 | 7-Point Clarity Checklist（改造版） | 认知升级 → 7 点检查 | rework-prompt.md | Rework 越改越窄 |
| 5 | Responsible Exit（结构化投降） | 投降机制 | SKILL.md escalation 逻辑 | 超限后用户接手困难 |
| 6 | Proactivity Spectrum（精简版） | 主动性光谱 | subagent implementer prompt | Implementer 做得表面 |
| 7 | Delivery Standard（改造版） | 交付自检清单 | verification-prompt.md | 验证只跑测试不检查边界 |

## Consequences

- **Prompt 体积增加可控**：7 项都是精简版/改造版，不是照搬 nopua 原文。预估每项增加 200-500 字，总计不超过 3KB。
- **不改变编排结构**：仍然是 subagent 角色分离 + 并行 review + 编排层控制循环，只改 prompt 内容和循环策略。
- **Cognitive Elevation 3 级策略可能需要调参**：3 级阈值（1-2 轮正常、3-4 轮换方案、5+ 轮归零）是初始值，实际效果需要观察后调整。
