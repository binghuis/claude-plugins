# talent

AI 时代工程师人才评估 skill 集合。

## 场景

**HR 给你一份简历让你去面试**。你是面试官,不规划整个 loop,就**面这一场**。

skill 帮你做三件事:
- **筛简历**:看 PDF 决定值不值得面
- **面试前**:30 秒读懂候选人 + 出今天这一场的方案 + 一页可对照的 cheat sheet
- **面试后**:把飞书会议记录转成结构化反馈,粘贴给 HR

## Skills

- **[screen-resume](skills/screen-resume/SKILL.md)**:筛简历——输入 PDF,自动定级 + 评估 + 命中正/反信号 + 必看链接 + 面试重点
- **[interview-prep](skills/interview-prep/SKILL.md)**:面试**前**用——输入 PDF（+ 本轮类型可选），输出今天这一场的方案
- **[interview-debrief](skills/interview-debrief/SKILL.md)**:面试**后**用——输入飞书会议记录（+ PDF 可选），输出本场结构化反馈给 HR

## References

- **[talent-model](references/talent-model.md)**:AI 时代工程师能力维度与 proxy 信号(级别无关的基础词典)
- **[level-junior](references/level-junior.md)** / **[level-mid](references/level-mid.md)** / **[level-senior](references/level-senior.md)**:初/中/高级各档的能力标准——本级聚焦、核心问题、正/反信号、级别专属方法(动词扫描 / 三大核心证据)

## 工作流

```
筛简历(可选):
/talent:screen-resume <PDF>           → floor 判断 + 信号 + 一句话判断

值得面 ↓

面试前:
/talent:interview-prep <PDF>          → 方案 + cheat sheet
                                        ↓ 自动落盘 ./talent-records/[姓名]/prep-YYYY-MM-DD.md
面试中（飞书自动录音 + 转文字）        → 对话记录
                                        ↓
面试后:
/talent:interview-debrief <对话记录>  → 反馈 markdown → 粘贴给 HR
                                        ↑ 自动按姓名 glob 找 prep 做对照
                                        ↓ 自动落盘 ./talent-records/[姓名]/debrief-YYYY-MM-DD.md
```

## 本地落盘约定

`interview-prep` 和 `interview-debrief` 自动写 markdown 到 `./talent-records/[候选人姓名]/`(`screen-resume` 只在对话内输出,不落盘——筛简历是 lightweight 决策):

```
./talent-records/
├── 王小明/
│   ├── prep-2026-05-06.md
│   └── debrief-2026-05-06.md
├── 李四/
│   └── prep-2026-05-08.md
└── ...
```

| 行为 | 说明 |
|---|---|
| 候选人姓名作为目录 | 一个候选人所有材料聚合;同名候选人手动改目录区分 |
| 文件名固定模式 | `prep-YYYY-MM-DD.md` / `debrief-YYYY-MM-DD.md`;同人同天多场加 `-r2/-r3` 后缀 |
| **debrief 自动找 prep** | `interview-debrief` 跑时按姓名 glob `prep-*.md`,自动加载做"该拿到 vs 实际拿到"对照——不需要手动传路径 |
| 隐私 | `./talent-records/` 应加进 `.gitignore`,候选人 PII 不入版本控制 |

## screen-resume 输出包含

| 块 | 用途 |
|---|---|
| 级别推断 + 依据 | 自动从 PDF 推断,或用户传入覆盖 |
| **Floor 判断** | **过 floor / 边缘 / 不过 floor** 3 档——**只判 floor,不评 ceiling**(强烈推荐 / 录用是 hiring committee 的事) |
| 命中正/反信号 | PDF 原文引用 |
| 本级专属观察 | 初级学习曲线 / 中级动词分布 / 高级三大核心证据 |
| 必看链接 | GitHub / 博客——只列不点,守门人不可外包 |
| 面试重点 | PDF 看不出、需面试问的洞 |

## interview-prep 输出包含

输出文件分两部分,**面试中只看上半,calibration 锚在分隔线下,面试结束再看**——这是结构化面试效度的关键。

| 块 | 何时用 |
|---|---|
| 候选人快速画像 | 面试前读 |
| 本轮聚焦 | 面试前读 |
| 时段安排 | 面试前读 |
| 必问题清单 | 面试前读,面试中翻 |
| 反向验证索引 | 面试前读,面试中翻 |
| 一页 Cheat Sheet(只列追问触发,不列期待答案) | 面试中翻 |
| **独立 Scorecard**(空白) | **面试中盲打**——研究里效度核心来源 |
| ⚠️ 分隔线 | —— |
| **Calibration 锚:好答案 vs 弱答案** | **面试结束 + scorecard 填完后**才看,**不在面试中翻** |

## interview-debrief 输出包含

| 块 | 用途 |
|---|---|
| 该拿到 vs 实际拿到 | 对照 prep 方案,看本轮 gap（若 prep 已传） |
| 命中维度 | 强证据 / 弱证据 + 候选人原话引用 |
| 故事讲述（不入证据栏） | 候选人讲了项目但没展开判断的部分,**单独标记** |
| 疑点登记 | 含糊 / 矛盾 / 转移话题的点 |
| **Anchoring 偏差报告** | scorecard 盲打 vs 对话证据 vs calibration 锚的三方对照（若 scorecard 已传） |
| **本场判断** | **本场该过 / 本场存疑 / 本场不过** 3 档——只出本场判断,不出综合录用表态 |
| 硬底线警告 | 中级决策案例缺失 / 高级守门人缺失等触发条件 |
| 给 HR 的建议 | 下一轮重点验、不必重复、候选人状态提示 |

## 硬底线（按级别 → 触发后必写本场判断）

- **初级**:基础功底缺失 → 本场不过
- **中级**:决策案例缺失 → 本场不过(或本场存疑 + 标"高级执行者倾向")
- **高级**:守门人时刻缺失 → 本场不过(默认 pass)

## 设计哲学

3 个 skill 都对齐 best practice:
- **screen-resume 只判 floor**——简历不是 ceiling 评估的素材
- **interview-prep 强制盲打 scorecard**——结构化面试效度(0.42-0.51)的关键源头是独立打分,不是对照预期
- **interview-debrief 不出综合录用决定**——单场判断 + 偏差报告是面试官的产物;综合录用是 hiring committee 的事
