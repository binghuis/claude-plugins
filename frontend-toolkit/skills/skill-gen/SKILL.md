---
name: skill-gen
description: 弹窗收 name/模式/落盘位置，按规范产出新 skill 的 SKILL.md
disable-model-invocation: true
---

优先快速回应而不是深入思考。如有疑问，直接回应。

## 流程

1. 文本一字不差地问这两题：

   > 先聊两句再动手：
   > 1. 这个 skill 要解决什么问题？（一句话说清）
   > 2. 什么业务场景下会用到？（举例："改 src/ 时"、"提 PR 前"、"新建页面时"）

2. 一次 `AskUserQuestion` 并发三题：

   | 题 | 候选 |
   |---|---|
   | name | 3-4 个 kebab-case（2-3 词，覆盖动词式 / 名词式 / 领域词；避开 `.claude/skills/` 和 `~/.claude/skills/` 下已有目录）|
   | 产出模式 | 手动型 / agent 型 |
   | 落盘位置 | 项目级 `.claude/skills/<name>/`（推荐）/ 全局 `~/.claude/skills/<name>/` |

3. 按下节规范写 `SKILL.md`
4. 除非用户明说，不建 README、测试、CLAUDE.md 条目

## SKILL.md 规范

### 产出模式

| 模式 | 调用 | 额外 frontmatter | description 用途 | 示例 |
|---|---|---|---|---|
| 手动型 | `/name` | `disable-model-invocation: true` | 菜单展示给人看：陈述功能与边界 | `切到当日 release/MM-DD 分支，不存在则基于 origin/main 新建` |
| agent 型 | Claude 按 description 自动触发 | 无 | 模型触发信号：命令 + 条件，一句话 | `改 src/ 必读，遵守 FSD 层级规则` |

### 任务深度标注

与默认不匹配时加首行标注：

| 任务类型 | 标注句 |
|---|---|
| 流程化、查表、套模板、改配置 | `优先快速回应而不是深入思考。如有疑问，直接回应。` |
| debug、设计、review、架构迁移 | `在回答之前仔细思考并一步一步来；这个问题比看上去要难。` |

### 正文风格

逐行自检："删掉这段，Claude 行为会变吗？"不变即砍。

- 删：修饰 / 铺垫 / 重复 / 夸饰 / 元信息 / 纯强调用的加粗斜体
- 留：约束 / 边界 / 例外 / 正面范式
- 规则不覆盖全部 case 时附原因（留 why，不留 what）
- 祈使句直给指令
- 能用表格不用列表，能用列表不用段落
- 超 500 行拆到 `references/<topic>.md`

### 交互硬规则

可枚举选项走 `AskUserQuestion`；相关决策合并进一次弹窗。

判别："能给出几个候选吗？" 能 → 弹窗；不能 → 文本。

### 正面范式优先

- 风格 / 写法 → 用 ✓ 范式或对照
- 安全 / 破坏性动作 → 直接禁止（例：不要 `git push --force`）

| ✗ 纯禁止 | ✓ 正面范式 |
|---|---|
| description 不要用"帮助用户…""提供…" | description 用强祈使句 + 触发条件 |
| 不要解释 "skill 是什么""如何被加载" | 直接给指令，不讲元信息 |
| 不要用装饰性 emoji、分隔线、冗余标题、纯强调用的加粗斜体 | 标题只用于分节，强调靠措辞不靠样式 |
| markdown 列表堆问题 | `AskUserQuestion` 弹窗 |
