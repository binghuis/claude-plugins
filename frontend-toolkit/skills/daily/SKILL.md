---
name: daily
description: 切到当日 release/MM-DD 分支；不存在则基于最新 origin/main 新建。用于当天开发当天上线
disable-model-invocation: true
---

## 步骤

```sh
BR=release/$(date +%m-%d)
```

1. **检查工作区**：`git status --porcelain` 非空 → 中止，提示用户先 commit / stash，不自动处理
2. **分支已存在**（`git rev-parse --verify $BR` 成功）→ `git checkout $BR`，打印分支名与当前 HEAD，结束
3. **分支不存在** → `git fetch origin main` → `git checkout -b $BR origin/main`，打印分支名与所基 commit（`git log -1 --oneline origin/main`）

## 约束

- 新建时基于远端最新 `origin/main`，不用本地 main
- 已存在的分支只切过去，不 reset、不重建、不 rebase
- 不自动 stash、不 push、不建 upstream
