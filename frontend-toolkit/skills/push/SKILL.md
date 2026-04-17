---
name: push
description: 当用户要求提交并推送代码时使用，按业务功能拆分 commit 并推送到远程
disable-model-invocation: true
---

串行两个 subagent：

1. `changelog` — 生成变更记录
2. `push` — 拆分提交并推送

每次传的上下文只包含已暂存文件，不复述步骤；完成后转述 push 的结果。
