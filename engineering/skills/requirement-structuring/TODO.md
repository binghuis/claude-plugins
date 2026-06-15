# TODO / 演进路线

原则：可靠性靠"跑"出来，不靠"设计"出来。先验证主干，再扩展难点。

---

## 架构现状

- `requirement-structuring` 是纯无状态函数：一段需求文本 → 三分清单
- 循环 / 问人 / 答案合并 / diff / 拆分 → 应用层 SDK（见 `references/orchestration.md`）
- 答案合并采用"追加文本"路径：把已澄清段拼回原始需求，下一轮全量重跑

---

## 第一优先级：SDK 编排原型（现在做）

不验通编排，skill 写得再细都不可靠。

- [ ] 用 Claude SDK 实现 `references/orchestration.md` 的伪代码骨架
- [ ] 定义 tool schema：`ask_pm`、`submit_doc`
- [ ] 定义 structured output JSON schema（对齐 `references/output-schema.md`）
- [ ] 拿 1 个真实需求跑通端到端：structuring → ask → 拼答案 → 再 structuring → 收敛
- [ ] 验证：模型有没有绕过 tool 自答？有没有早停 / 不停？追加澄清段后会不会自相矛盾？

---

## 第二优先级：判据实证（原型之上）

- [ ] 拿 3–5 个真实需求（脱敏）跑 SDK 原型
- [ ] 验证点 1：待澄清揪得准不准——该问的揪到没？不该问的有没有乱问？
- [ ] 验证点 2：三分判断稳不稳——同一需求多跑结果一致吗？
- [ ] 验证点 3：产出格式下游 ~20 个 skill 接得住吗？
- [ ] 根据跑出的问题改 `references/certainty-criteria.md` 和 `references/output-schema.md`

---

## 留到后面：业务内聚拆分

启动条件：
- [ ] 真实遇到"塞了多个独立业务"的大需求时，新建独立 skill `requirement-split`
- 大部分单需求本身内聚，一份就够，不必提前过度设计

---

## 留到后面：智能牵连判断 / 增量处理

当前 SDK 走"文本 diff + 整段重跑"，模型不知道"上一版"。够用就这样。

启动条件：
- [ ] 真实遇到一次大改后"许多已确定项需要重新审视"的场景，发现纯文本 diff 不够，再设计"牵连"能力
- 可能的形态：扩展 SDK 流程让 skill 接受"上一版 + 变化点"，或独立 skill `requirement-impact`

---

## 比技术更重要的提醒

本 skill 是 AI 工程化的发力点，是手段不是目的。最终意义是作为"活的证据"，证明那套 AI 研发范式能落地、能复制、能产生价值——撬动跟前端负责人 / CTO 的沟通。

- [ ] 不要让"打磨 skill"变成新的自我锁死。技术沉淀和职业跃迁两条线一起走
- [ ] 验证出初步成果后，主动作为"可复制的方法论"向上输出，而不是闷头继续打磨
