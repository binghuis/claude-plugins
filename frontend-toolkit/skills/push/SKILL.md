---
name: push
description: 按业务功能拆分用户暂存区改动并分批 commit 推送到远程，严禁触碰未暂存内容
disable-model-invocation: true
---

优先快速回应而不是深入思考。如有疑问，直接回应。

## 步骤

1. 调用 `changelog` skill 生成条目，确认 `CHANGELOG.md` 已入暂存区
2. `git diff --cached` + `git diff --cached --stat` 查看暂存区；空 → 告知用户并结束
3. 按下表分组

   | 情况 | 规则 |
   |---|---|
   | 不同业务 | 拆开（字幕联动 + 术语表搜索 → 两条 commit） |
   | 同一业务多文件 | 合成一组（type + hook + UI → 一条），配置/文档归入同组 |
   | 无关的非业务改动 | 配置、文档、杂务合并为一条 |
   | 同一文件含多业务 | 不做 hunk 拆分，归入最相关组 |
   | `CHANGELOG.md` | 归入主要业务组；跨多组时单独 `docs(changelog)` |

4. 单组 → `git commit`，跳第 7 步
5. 多组 → 先 `git stash --keep-index` 隔离未暂存改动
6. 逐组提交：
   - `git reset HEAD <本组之外的文件>`
   - `git commit`
   - `git add <下一组文件>`
   - 完成后 `git stash pop` 恢复工作区
7. `git fetch origin` → `git push`（无上游加 `-u origin HEAD`）；失败或本地落后 → 提示用户先 pull
8. 输出每条 commit 的 hash 和 message

## Commit 规范

格式：`type(scope): 中文描述（≤ 50 字）`

| type | 含义 |
|---|---|
| feat | 新功能 |
| fix | 修复 |
| refactor | 重构 |
| perf | 性能 |
| chore | 杂务 |
| docs | 文档 |
| style | 格式 |

- **scope**：优先 FSD slice 名，跨 slice 取主要模块
- 示例：`feat(editor): 字幕面板增加跨面板联动高亮`

## 硬规则

- **只操作用户已暂存的内容，严禁 `git add` 任何未暂存文件**
- 多组提交前 `git stash --keep-index`，完成后 `git stash pop`
- 不要 `git push --force`
- 不要用 `--no-verify` 跳 hooks
- push 被拒 → 提示用户手动 pull，不要自动 rebase、不要 force push
