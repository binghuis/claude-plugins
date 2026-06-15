---
name: requirement-structuring
description: 把原始需求结构化为"确定/待澄清/可默认"三分清单
disable-model-invocation: true
---

在回答之前仔细思考并一步一步来；这个问题比看上去要难。

1. 识别业务需求；剥离视觉设计、交互呈现、技术实现。判据见 `references/requirement-classification.md`
2. UI 草图当意图参考：从中提取业务需求（含隐性规则），不澄清视觉细节。详见 `references/ui-extraction.md`
3. 对每条业务需求做三分判断（判据见 `references/certainty-criteria.md`）
4. 补隐性需求：异常 / 失败 / 空状态 / 极端输入 / 超时的业务反馈（"反馈什么"非"长什么样"）、逻辑分支缺口
5. 按 `references/output-schema.md` 产出
