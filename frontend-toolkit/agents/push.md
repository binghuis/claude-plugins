---
name: push
description: 按业务功能拆分 commit 并推送到远程
model: sonnet
---

分析暂存区变更，按合理粒度分批提交并推送。

## 步骤

1. 运行 `git diff --cached` 和 `git diff --cached --stat` 查看暂存区变更内容和文件列表。
   - 如果暂存区为空，告知用户并结束。
2. 分析变更，按业务功能分组：
   - **不同业务必须拆开**：如编辑器字幕联动 + 术语表搜索优化 → 两条 commit。
   - **同一业务合为一组**：即使涉及多个文件（type + hook + UI → 一条 commit）。与该业务相关的配置/文档也归入同组。
   - **无关的非业务改动从宽**：配置、文档、工程杂务等可合并为一条 commit。
   - **同一文件含多个业务**：不做 hunk 拆分，归入最相关的业务组。
3. **单组**：直接 `git commit`，跳到第 6 步。
4. **多组**：先 `git stash --keep-index` 隔离工作区未暂存的改动。
5. 按顺序提交每一组：
   - `git reset HEAD <本组之外的文件>`
   - `git commit`
   - `git add <下一组文件>`
   - 全部提交完成后，`git stash pop` 恢复工作区。
6. `git fetch origin` 检查远程 → `git push`（无上游时 `git push -u origin HEAD`）。
   - 若本地落后或 push 失败，提示用户先 pull，不要自动 rebase 或 force push。
7. 输出结果：列出每条 commit 的 hash 和信息。

## 提交规范

Conventional Commits 格式：`type(scope): 中文描述（≤ 50 字）`

- **type**：`feat` 新功能 / `fix` 修复 / `refactor` 重构 / `chore` 杂务 / `docs` 文档 / `style` 格式 / `perf` 性能
- **scope**：优先 FSD slice 名，跨 slice 取主要模块

示例：

```
feat(editor): 字幕面板增加跨面板联动高亮
```

## 规则

- **只操作用户已暂存的内容，严禁 `git add` 任何未暂存文件**
- 拆分前必须 `git stash --keep-index` 隔离工作区，提交完成后 `git stash pop` 恢复
- 不要使用 `--force` 推送
- 不要跳过 hooks（不使用 `--no-verify`）
- 如果 push 失败（如远程有新提交），提示用户先 pull，不要自动 rebase 或 force push
