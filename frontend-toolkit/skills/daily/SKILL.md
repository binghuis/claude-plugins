---
name: daily
description: 切到当日 release/MM-DD 分支，不存在则基于 origin/main 新建
disable-model-invocation: true
---

优先快速回应而不是深入思考。如有疑问，直接回应。

## 步骤

```sh
BR=release/$(date +%m-%d)
```

1. `git status --porcelain` 非空 → 中止，提示先 commit / stash
2. `git rev-parse --verify $BR` 成功 → `git checkout $BR`，打印分支名与 HEAD，结束
3. 否则 → `git fetch origin main` && `git checkout -b $BR origin/main`，打印分支名与所基 commit（`git log -1 --oneline origin/main`）

## 禁止

- 拿本地 main 作基（只用 `origin/main`）
- 对已存在分支做 reset / 重建 / rebase
- 自动 stash、push、建 upstream
