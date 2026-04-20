# frontend-toolkit

前端日常 skill 集合。

## Skills

- **[changelog](skills/changelog/SKILL.md)**：基于暂存区 diff 生成面向用户的中文 changelog 条目，按类别合并到 `CHANGELOG.md` 当天日期块
- **[code-review](skills/code-review/SKILL.md)**：审查当前变更，只报线上会出错、数据损坏或明显性能问题的核心隐患
- **[comment](skills/comment/SKILL.md)**：写/改代码后按清单评估注释需求，只补"为什么"，不补"做了什么"
- **[daily](skills/daily/SKILL.md)**：切到当日 `release/MM-DD` 分支，不存在则基于 `origin/main` 新建
- **[fix](skills/fix/SKILL.md)**：定位 bug 根因，给出方案 A/B，用户确认后再改代码
- **[fsd](skills/fsd/SKILL.md)**：改 `src/` 下文件时触发，遵守 Feature-Sliced Design 层级与导入规则
- **[push](skills/push/SKILL.md)**：按业务功能拆分用户暂存区改动并分批 commit 推送到远程，严禁触碰未暂存内容
- **[react-design](skills/react-design/SKILL.md)**：写/改 React 组件或 Hook 必读，严守单一职责与 UI/逻辑分离
- **[react-patterns](skills/react-patterns/SKILL.md)**：写或改 React/TSX 必读，覆盖 key 重置、禁 any/as、联合类型消灭非法状态、四态覆盖
- **[skill-gen](skills/skill-gen/SKILL.md)**：新建 skill：问清主场景后，弹窗收 name/模式/落盘位置，按规范产出 `SKILL.md`
- **[skill-improve](skills/skill-improve/SKILL.md)**：按 skill-gen 规范对照已有 skill 的 `SKILL.md`，列改动表经确认后一次改完
