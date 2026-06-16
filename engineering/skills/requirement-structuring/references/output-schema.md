# 结构化需求文档 spec

输出合法 JSON。所有 id 全局唯一。

下方示例块为 JSONC（注释仅说明字段语义），实际产出严格按 JSON，不带注释。

```jsonc
{
  "feature": "手机号登录",

  // 确定 R：PM 已拍板架构/范围/方向，执行者照做不用猜
  "confirmed": [
    { "id": "R1", "content": "用户可用手机号 + 验证码登录" },
    { "id": "R2", "content": "验证码 6 位数字，5 分钟有效" },
    { "id": "R3", "content": "连续输错 3 次锁定账户" },
    { "id": "R4", "content": "验证码发送有 60 秒冷却" }
  ],

  // 待澄清 Q：仅装会改变架构/范围/方向、需要 PM 拍板的项；参数/文案/惯例归 defaults
  "clarifications": [
    {
      "id": "Q1",
      "question": "未注册的手机号如何处理？",  // 哪里不清楚
      "impact": "缺这个分支会导致未注册用户卡死",  // 不澄清会导致什么
      "candidates": ["A 拦截提示", "B 自动引导注册"]
    }
  ],

  // 默认 D：低风险默认值，PM 后续 review 可改
  "defaults": [
    { "id": "D1", "decision": "验证码输入框只接受数字、自动聚焦下一位" },
    { "id": "D2", "decision": "登录失败提示 验证码错误，请重试" },
    { "id": "D3", "decision": "账户锁定时长 30 分钟" }
  ],

  // 任务 T：下游 skill 接手。dependencies 列所有 R/D/Q 直接依据；含 Q 即视为阻塞，由消费方推导
  "tasks": [
    { "id": "T1", "title": "手机号+验证码登录接口对接", "dependencies": ["R1", "Q1"] },
    { "id": "T2", "title": "验证码发送/冷却逻辑", "dependencies": ["R2", "R4"] },
    { "id": "T3", "title": "错误次数计数 + 锁定逻辑", "dependencies": ["R3", "D3"] }
  ]
}
```
