---
name: branch-switch
description: 按用户输入匹配本地+远程已有分支，命中则确认切过去；未命中按入口路径返回或提示，不创建分支
disable-model-invocation: true
---

优先快速回应而不是深入思考。如有疑问，直接回应。

## 流程

1. 用户输入为空：问「想切到哪个分支？」再继续
2. 输入按自然语言处理：抽出核心名词/动作作为匹配关键词（中文也照抽，并翻成英文同义词一并用于匹配，如「登录页」→ `login`/`signin`/`auth`）
3. `git fetch origin --prune`
4. `git branch -a` 全量分支清单，对每个关键词做大小写不敏感的子串/相似度匹配，命中并集；去重同名 local/remote，标注来源
5. 未命中：
   - 由 branch-new 委派进入（上下文可识别）→ 直接结束，控制权交回 branch-new
   - 用户直接 `/branch-switch` → 告诉用户「没匹配到分支，要新建敲 `/branch-new`」结束
6. 命中已有分支：
   - 命中 1 条：`AskUserQuestion` 二选一「切过去 / 取消」
   - 命中 ≥2 条：`AskUserQuestion` 列出候选让用户选一条（含「取消」）
   - 用户取消 → 结束（不再委派任何 skill）
   - 用户选某条 → 步骤 7
7. 切分支：
   - 工作区有未提交改动（含 untracked）：`git stash push -u -m "branch-switch: <from> → <name>"`，切完后在目标分支 `git stash pop`
   - 本地已存在：`git switch <name>`
   - 只在远程：`git switch <name>`（git ≥2.23 自动跟踪 `origin/<name>`）

## 边界

- 已在目标分支上：报告「已在 <name>」，不重复 switch；若有改动也不 stash
- 同名 local/remote 都存在且 SHA 不同：提示分歧，让用户确认切本地还是 reset 到远程，不擅自处理
- `stash pop` 冲突：停下报告冲突文件，stash 不丢弃，让用户手动处理（不要 `stash drop`）
