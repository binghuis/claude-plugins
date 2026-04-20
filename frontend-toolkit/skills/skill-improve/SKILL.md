---
name: skill-improve
description: 按 skill-gen 规范审查项目级 <name> 的 SKILL.md，列违反表经确认后改
disable-model-invocation: true
---

优先快速回应而不是深入思考。如有疑问，直接回应。

1. 定位 `<name>` 的 SKILL.md：仅查项目级 `.claude/skills/<name>/`，缺失报错退出。
2. 按 skill-gen "SKILL.md 规范" 一节逐项对照，输出表格 `| 违反点 | 拟改法 |`。
3. 用户确认后一次改完。
