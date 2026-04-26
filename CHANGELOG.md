# Changelog

## 2026-04-26

### 优化
- skill-gen 任务深度标注规则改为「按任务类型选一行加在首行」（此前「与默认不匹配时加首行标注」，默认未定义易误判）
- skill-gen 正文风格「删」清单去掉与「正面范式优先」表重复的样式禁止条目

## 2026-04-25

### 新增
- 新增 docs skill：规范 FSD 切片 docs/ 的文档结构与同步流程，改代码时按 related 提示同步

### 优化
- code-review 输出改用通俗口语，新增「复现场景」和「解决方案」字段，问题更易读、更易动手修
- fsd skill 精修描述与正文，规则表述更紧凑、表格替代列表，使用时一眼能看清
- skill-gen 正文风格规则清单化、删除元信息，明确禁用纯强调用的加粗斜体
- skill-improve 改用深思考标注、步骤描述更紧凑，审查更稳更细
- docs skill 拆分被动改代码与显式建文档两路触发，「建文档场景」表新增设计决策类，判定更细
- skill-gen 移除强制走弹窗的交互硬规则，新建 skill 时交互方式更自由
- changelog skill 正文精修：明确受众为产品/测试，新增"一动作一条""标注行为变更""写场景不写规则"等写法约束，生成的条目更易复核回归

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
