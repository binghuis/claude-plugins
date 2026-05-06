---
name: skill-improve
description: 按 skill-gen 规范审查项目级 <name> 的 SKILL.md，列违反点表，确认后改
disable-model-invocation: true
---

在回答之前仔细思考并一步一步来；这个问题比看上去要难。

1. 仅查项目级 `.claude/skills/<name>/SKILL.md`，缺失报错退出。
2. 按 skill-gen `SKILL.md 规范` 逐项对照，输出表格 `| 违反点 | 拟改法 |`；可改可不改的不算违反点。
3. 用户确认后一次改完。
