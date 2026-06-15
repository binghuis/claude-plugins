# 编排：应用层职责（SDK 实现指南）

`requirement-structuring` 是**纯无状态函数**：一段需求文本 → 三分清单。
循环 / 问人 / 答案合并 / diff 由应用层负责。

## 为什么不放进 skill

单 skill 无循环 / 等待 / 状态：
- 模型在一轮内会自问自答（最致命）
- 收敛判断主观，可能早停或不停
- 牵连判断需要全局视野，模型易漏

应用层用代码强制约束，比 prompt 自律可靠得多。

## 应用层职责

| 职责 | 做法 |
|---|---|
| 控循环 | 读 structured output 的 `待澄清` 数组，空即收敛 |
| 问人 | 定义 `ask_pm` tool；UI 接住清单展示给用户，回答作为 tool result 回填 |
| 答案合并 | 把用户答案以追加段的形式拼回原始需求文本，下一轮重新跑 structuring |
| diff（PRD 新旧对比） | SDK 做文本 diff，把"变化部分"作为新需求文本传给 structuring |
| 收敛兜底 | 5 轮还没收敛 → escalate 给产品 |
| ID 稳定性 | skill 每轮独立分配 id，跨轮不稳；如需稳定 id 由 SDK 维护映射 |

后续职责（未独立 skill）：
- 拆分：等真案例再独立 skill `requirement-split`
- 智能牵连判断：等首次"大改后哪些已确定项受冲击"场景出现再设计

## 应用伪代码

```python
raw = original_prd
round_count = 0

while True:
    doc = call_structuring(raw)
    if not doc.待澄清:
        break
    answers = ui.ask_user(doc.待澄清)             # tool-based ask_pm
    raw = raw + "\n\n## 已澄清\n" + format_answers(answers)
    round_count += 1
    if round_count > 5:
        abort("需求本身没想清楚，escalate 给产品")

save(doc)   # 拆分留给后续 skill
```

每轮把"已澄清"段追加到原始需求 → skill 重新全量结构化 → 已被答过的问题不会再出现在 `待澄清` → 自然收敛。

## Structured output

每次调用强制输出 JSON，schema 对齐 `output-schema.md`：

```json
{
  "已确定": [{ "id": "R1", ... }, ...],
  "待澄清": [{ "id": "Q1", ... }, ...],
  "可默认": [{ "id": "D1", ... }, ...]
}
```

字段细节见 `output-schema.md`。应用读 `待澄清` 长度判断是否收敛。
