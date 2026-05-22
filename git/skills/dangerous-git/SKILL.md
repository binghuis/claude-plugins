---
name: dangerous-git
description: 起意 git 命令、或用户提「撤回合并 / 只在测试 / 强推 / dev=prod 同步」等意图时：对照危险清单讲后果/可逆性/替代，等确认才动手。
---

优先快速回应而不是深入思考。如有疑问，直接回应。

## 触发即停

清单内任一操作执行前:

1. 把要做什么、后果、代价、可逆性(reflog 能不能救)、替代方案告诉用户
2. 等用户明确确认("没拒绝"≠ 同意)
3. 才能跑

表格里的「后果」「替代」是该操作的固有风险锚(rebase 必变 SHA、stash clear 必不可逆),不是给用户的现成话术——讲给用户时按当时情景(已 push?给谁?协作者数?reflog 还在?工作树状态?)展开讲。

## 危险操作清单

| 操作 | 后果 | 替代 |
|---|---|---|
| `git revert <merge-commit>` / `-m 1` | 后续再 merge 同一源分支不会重带这批改动,静默丢功能 | 撤单文件 → `git checkout <merge>^1 -- <file>` 后提交;撤单 commit → 在源分支上 `git revert <commit>` 走正常 merge |
| `git push --no-verify` / `--no-gpg-sign` / `commit --no-verify` | 绕过 lint / 测试 / 签名,review 看不出 | 修好 hook 报的错 |
| `git -c core.hooksPath=...` / `-c commit.gpgsign=false` / `GIT_CONFIG_*=...` | 等效绕过钩子/签名,且命令里看不出"跳"的字眼 | 同上 |
| `git push --force` / `-f` / `+<branch>` 推 shared | 抹掉别人刚推的 commit | 改 `--force-with-lease`,先列会丢哪些 commit |
| `git push --force-with-lease` 推 shared | 仍会覆盖你本地没看到的部分 | diff `origin/<branch>..HEAD` 看会丢什么再推 |
| `git reset --hard <commit>`(HEAD 已 push) | 抹掉中间所有 commit | 用 `git revert <commit>` 反向提交 |
| `git rebase` 已 push 的分支 | SHA 全变,协作者本地分支失效 | 在目标分支上 cherry-pick |
| `git commit --amend` 已 push 的 commit | SHA 变,推就要 force | 直接新提交 |
| `git filter-branch` / `git filter-repo` | 所有 SHA 全变,等于换仓库 | 只在镜像分支跑 |
| `git branch -D <shared>` | 未推 commit 只能 reflog 救 | 删前 `git log` 确认已合入主线 |
| `git update-ref -d` / `git symbolic-ref` 改 shared ref | 绕过 `branch -D` 保护,无任何检查 | 先 `git log` 确认已合入,再走 `branch -D`(受 reflog 保护,不绕底层) |
| `git push origin --delete <shared>` / `:<shared>` | 协作者 `origin/<branch>` 失效,PR 状态异常 | 先确认 PR 已合并 + 协作者预告,不可逆 |
| `git push --mirror` / `--prune` | 按本地一次性清扫远端,独有 ref 全没 | 列出会删哪些 ref |
| `git tag -d <已发布>` + `push origin :refs/tags/<tag>` | 下游 CI / 包发布(npm/maven/docker)拉不到 | 发新版本号覆盖,别删 tag |
| `git clean -fd`(有未提交) | 未 add 的新文件直接消失,reflog 救不回 | 先 `git stash push -u`,列出会删什么 |
| `git reset --hard`(不带 ref)/ `checkout -- .` / `restore .` / `checkout -f` | 已跟踪文件未提交修改全没 | 同上 |
| `git worktree remove -f <path>`(有未提交) | worktree 里的改动一起没 | 同上 |
| `git stash clear` | reflog 也一起清,不可逆 | 用 `stash drop` 删单个 |
| `git reflog expire --expire=now --all` + `gc --prune=now` | 之前丢的 commit / branch / stash 全找不回 | 先 `git bundle create --all backup.bundle` 备份 |
