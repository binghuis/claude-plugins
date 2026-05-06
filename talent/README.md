# talent

AI 时代工程师人才评估 skill 集合。

## 设计

| 层 | 内容 |
|---|---|
| 标准本体（WHO） | [talent-model](skills/talent-model/SKILL.md)——三档定价、级别画像、proxy 信号、反信号 |
| 场景化执行（HOW） | screen-resume-junior / mid / senior——从 PDF 简历挖各级别独有的信号 |

场景类 skill 用 `@../talent-model/SKILL.md` 引用标准本体，标准变了改一处，所有场景同步。未来扩展面试评估、晋升评审、内部转岗等都按同一模式接入。

## Skills

- **[talent-model](skills/talent-model/SKILL.md)**：AI 时代工程师人才评估的单一事实源——三档定价、级别画像、proxy 信号、反信号；被简历筛选/面试评估等场景引用
- **[screen-resume-junior](skills/screen-resume-junior/SKILL.md)**：按 talent-model 标准筛选初级（0-3 年）工程师 PDF 简历，重点看学习曲线和 AI 协作力
- **[screen-resume-mid](skills/screen-resume-mid/SKILL.md)**：按 talent-model 标准筛选中级（3-7 年）工程师 PDF 简历，重点看问题定义、审阅证据、跨模块系统思维
- **[screen-resume-senior](skills/screen-resume-senior/SKILL.md)**：按 talent-model 标准筛选高级（7+ 年）工程师 PDF 简历，重点看架构判断事件、领域深度、守门人时刻
