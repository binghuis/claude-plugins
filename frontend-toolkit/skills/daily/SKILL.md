---
name: daily
description: 当用户开启"当天开发当天上线"任务需要建新分支时使用，基于最新 origin/main 创建 release/MM-DD 分支
disable-model-invocation: true
---

## 步骤

1. `git status --porcelain` 非空 → 中止，让用户先 commit / stash
2. `git fetch origin main`
3. `git checkout -b release/$(date +%m-%d) origin/main`
4. 打印新分支名与所基 commit（`git log -1 --oneline origin/main`）

## 约束

- 始终以 `origin/main` 为基，不 checkout 本地 main
- 分支已存在 → 报错终止，提示用户直接切过去
- 不自动 stash、不 push、不建 upstream
