# Changelog

## 2026-05-22

### 新增
- **flow 插件新增 /go 与 /continue 两个手动命令：/go 跳过澄清和规划直接执行下一步；/continue 回到上一轮被打断、未完成的任务，从断点继续做**
- **git 插件 branch-new / branch-switch / dangerous-git 三个 skill 入库：branch-new 自动 stash 后新建分支并切过去；branch-switch 切已有分支并保留 WIP；dangerous-git 在执行破坏性 git 命令前列后果/可逆性/替代，等用户拍板**

## 2026-05-09

### 新增
- frontend 插件 orval-react-query skill：调接口必读，给出 hook → 自写 useMutation → 原始函数 的退化阶梯，跳步要在注释里说清原因；并规范缓存默认值、失效时机和乐观更新边界

## 2026-05-08

### 新增
- talent 插件 interview-prep skill：输入候选人 PDF（+ 本轮类型可选），输出今天这场的面试方案——候选人画像 + 必问题 + 反向验证 + 好/弱答案样本 + 一页 cheat sheet
- talent 插件 interview-debrief skill：输入面试飞书会议记录（+ 候选人 PDF 可选），自动产出结构化反馈——档位 + 信心度 + 关键证据 + 故事讲述区分 + 疑点 + 给 HR 的下一轮建议

### 优化
- talent 插件 screen-resume 三档（junior/mid/senior）合并为单一 screen-resume，按 talent-model 自动定级 + 信号挖掘，输出 floor 判断（过/边缘/不过）+ 命中正/反信号 + 必看链接 + 面试重点（此前需手动选档；新版只判 floor，不做 ceiling 决策，"值不值得招"留给 hiring committee）
- push skill 推送被拒（本地落后）后自动 `git pull --rebase` 重推，冲突走 pause-for-conflict（此前提示用户手动 pull）
- changelog skill 加规则"不与用户确认，全流程走完"（此前 AI 在归类、合并等步骤可能停下来问，中断流程）

### 移除
- talent-model 独立 skill 下线，迁为 `talent/references/talent-model.md` 文档供其他 talent skill 引用（此前作为可调用 skill 输出标准本体）

## 2026-05-06

### 新增
- 新增 talent 插件，AI 时代工程师人才评估工具包：talent-model 给出三档定价/级别画像/proxy 信号/反信号；screen-resume-junior / mid / senior 三档分别从 PDF 简历挖学习曲线、问题定义、架构判断信号
- 新增 meta 插件，收纳 Claude Code 元工具 skill-gen / skill-improve（自 frontend-toolkit 迁出）

### 优化
- 原 frontend-toolkit 插件更名为 frontend，老用户需在 settings.json 把 `frontend-toolkit@binghuis-plugins` 改为 `frontend@binghuis-plugins`，并按需启用 `talent@binghuis-plugins` / `meta@binghuis-plugins`

## 2026-04-28

### 优化
- docs skill 写作原则明确多分支流转改用 Mermaid 渲染、禁用 ASCII（此前仅写「流程图」未指定形式）
- docs skill 删除「约束」章节模板及「改后违反约束代码让步」规则（此前要求生成约束清单，但前端切片硬约束极少，多产出当前行为描述，导致「代码让步」规则形同虚设）

## 2026-04-27

### 新增
- 恢复 fix skill（仅手动调用，`disable-model-invocation: true`）：手动让 Claude 走「分析 → 方案 A/B → 确认后改代码」的固定流程

### 优化
- skill-improve 违反点表收紧：可改可不改的不算违反点，避免凑数列出主观偏好

## 2026-04-26

### 优化
- skill-gen 任务深度标注规则改为「按任务类型选一行加在首行」（此前「与默认不匹配时加首行标注」，默认未定义易误判）
- skill-gen 正文风格「删」清单去掉与「正面范式优先」表重复的样式禁止条目
- code-review 硬规则新增「可改可不改的不报」，避免凑数输出风格、偏好类建议（此前可能会把非 bug 的改进项列入隐患）

### 移除
- 下线 fix skill（此前提供 bug 根因定位与方案 A/B 对比），frontend-toolkit 入口列表已同步删除

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
