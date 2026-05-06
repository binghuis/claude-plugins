# meta

围绕 Claude Code 系统组件本身的元工具。当前覆盖 `SKILL.md` 的新建和审查；未来 `hook` / `command` / `marketplace` 类的脚手架也归此插件。与具体业务领域无关，给"自己写 skill 的人"用。

## Skills

- **[skill-gen](skills/skill-gen/SKILL.md)**：新建 skill：问清主场景后，弹窗收 name/模式/落盘位置，按规范产出 `SKILL.md`
- **[skill-improve](skills/skill-improve/SKILL.md)**：按 skill-gen 规范审查项目级 skill 的 `SKILL.md`，列违反点表经确认后一次改完
