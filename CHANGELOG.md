# Changelog

## 2026-04-25

### 优化
- code-review 输出改用通俗口语，新增「复现场景」和「解决方案」字段，问题更易读、更易动手修

## 2026-04-21

### 优化
- skill-gen 正文风格规则精修，明确"删/留"清单，并把"原因"收敛为规则覆盖不全时的条件项

## 2026-04-20

### 新增
- 新增 skill-gen，交互式新建 skill：问清主场景后弹窗收集 name / 模式 / 落盘位置，按规范产出 `SKILL.md`
- 新增 frontend-toolkit 插件说明页，统一列出全部 skill 的用途和入口
- 新增 skill-improve，对照 skill-gen 规范审查已有 skill 的 `SKILL.md`，列出改动表经确认后一次改完

### 优化
- 精修 changelog、code-review、comment、daily、fix、fsd、push、react-design、react-patterns 等 skill 的描述与流程，表达更简洁、触发条件更清晰、使用时更省心
- 市场更名为 `binghuis-plugins`，定位从"面向前端"调整为"个人维护"，现有用户需同步更新 `settings.json` 中的市场名与仓库地址
- 安装说明拆分用户级/项目级两种方式，用户级推荐 `/plugin marketplace add binghuis/claude-plugins` 一键接入
- skill-improve 描述点明"仅项目级"边界，菜单里一眼看出作用范围
