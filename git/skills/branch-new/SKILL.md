---
name: branch-new
description: 先委派 branch-switch 试切已有分支；未命中或用户取消时让用户选基分支 + 按命名规范确认名，自动 stash 后新建
disable-model-invocation: true
---

优先快速回应而不是深入思考。如有疑问，直接回应。
upstream 已解除
## 流程

1. 用户输入为空：问「这个分支是干嘛的？」再继续
2. 委派 branch-switch 试切已有分支，原始输入透传过去（branch-switch 不会回调本 skill，物理上无环）
   - 命中并切成功 → 此 skill 结束
   - 未命中、或用户取消 → 进入步骤 3
3. 选基分支：`AskUserQuestion` 列候选让用户选
   - 候选生成：
     - `origin/<默认主分支>`（置顶推荐，默认分支用 `git remote show origin | sed -n 's/.*HEAD branch: //p'` 取）
     - 当前 HEAD（若非上面那条）
     - 最近活跃远程分支：`git for-each-ref --sort=-committerdate refs/remotes/origin --format='%(refname:short)' | grep -v HEAD | head -3` 取前 2-3 条去重后填进剩余位
   - 用户选「自定义」自行输入：跑 `git rev-parse --verify --quiet <ref>` 校验存在，不存在要求重输
4. 跑 `git for-each-ref --format='%(refname:short)' refs/heads refs/remotes/origin | head -50` 看现有命名风格，沿用前缀（feat/fix/chore 等）
5. 按意图生成英文 kebab-case 候选名（中文意图先翻成英文再 kebab）
6. `AskUserQuestion` 给 2-3 个候选让用户确认；若用户选「自定义」自行输入，跑 `git show-ref --verify --quiet refs/heads/<name>` 和 `git show-ref --verify --quiet refs/remotes/origin/<name>`，任一存在即视为重名，要求改名
7. 用户确认后：
   - 工作区有未提交改动（含 untracked）：`git stash push -u -m "branch-new: <from> → <name>"`
   - `git switch -c <name> <base>`（base 即步骤 3 选定的基分支）
   - 若上一步已 stash：在新分支上 `git stash pop`

## 边界

- `stash pop` 冲突：停下报告冲突文件，stash 不丢弃，让用户手动处理（不要 `stash drop`）

## 命名规范

仓库已有风格优先（见步骤 4）；无风格时按此默认：

- 形式：`<type>/<desc>`，desc 为 2-4 词的 kebab-case
- type 集合：`feat` `fix` `chore` `docs` `refactor` `test` `perf` `build` `ci` `style`，按意图选一个
- desc 字符集：`[a-z0-9-]`，全小写、连字符分词、不含下划线/点/中文/空格/大写
- 总长 ≤ 50 字符；不以 `-` 开头或结尾；不出现连续 `--`

| ✗ | ✓ |
|---|---|
| `Add-Login-Page` | `feat/login-page` |
| `feature_user_auth` | `feat/user-auth` |
| `登录页` | `feat/login` |
| `fix/` | `fix/null-token` |
| `feat/page--login` | `feat/login-page` |
