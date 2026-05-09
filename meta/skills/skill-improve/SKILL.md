---
name: skill-improve
description: 按 skill-gen 规范审查 <name> 的 SKILL.md，列违反点表，确认后改
disable-model-invocation: true
---

在回答之前仔细思考并一步一步来；这个问题比看上去要难。

1. 定位 SKILL.md：`git ls-files '**/skills/<name>/SKILL.md'`：零命中报错，多命中让用户挑。
2. 按 skill-gen `SKILL.md 规范` 逐项对照，输出表格 `| 违反点 | 拟改法 |`；可改可不改的不算违反点。
3. 用户确认后一次改完。
