# 结构化需求文档 spec

输出 JSON。顶层字段：

| 字段 | 类型 | 说明 |
|---|---|---|
| `meta` | object | 元信息 |
| `confirmed` | array | 确定的需求（R）|
| `clarifications` | array | 待澄清（Q）— 核心产出 |
| `defaults` | array | 按默认执行（D）|
| `boundaries` | object | 边界与约束 |
| `exceptions` | array | 异常与边界情况（E）|
| `tasks` | array | 拆解出的开发任务（T）|
| `uiReference` | object? | UI 草图（可空）|

## meta

- `feature`: string — 功能名
- `source`: string — 原始需求出处
- `status`: `草稿` \| `澄清中` \| `已对齐`

## confirmed (R)

执行者不用猜就能照做。

- `id`: `R\d+`
- `origin`: `原始` \| `AI补充` \| `已澄清确认`
- `content`: string

## clarifications (Q)

仅装会改变 **架构 / 范围 / 方向** 的 PM 拍板项；参数 / 文案 / 惯例归 `defaults`。

- `id`: `Q\d+`
- `question`: 哪里不清楚
- `impact`: 不澄清会导致什么
- `candidates`: string[]（可空）
- `owner`: `产品` \| `设计` \| `技术负责人`
- `status`: `待回答` \| `已确认` \| `已忽略`
- `related`: string[] — 关联 R/T/D/E

## defaults (D)

低风险默认值，PM 后续 review 可改。

- `id`: `D\d+`
- `decision`: 默认怎么做
- `assumption`: 假设是什么

## boundaries

- `inScope`: string[]
- `outOfScope`: string[]
- `prerequisites`: string[]
- `constraints`: string[]

## exceptions (E)

异常 / 失败 / 空状态 / 极端输入 / 超时的业务反馈。写**反馈什么**，不写**长什么样**。

- `id`: `E\d+`
- `scenario`: 异常场景
- `feedback`: 业务反馈

## tasks (T)

下游 skill 工作流可直接接手。

- `id`: `T\d+`
- `title`: 任务名
- `dependencies`: string[] — 依赖的 R/Q/D/E
- `blocked`: boolean

## uiReference

- `images`: string[]
- `note`: string

## 不变量

- 所有 id 全局唯一
- `tasks.dependencies` 若含 status=`待回答` 的 Q，必须 `blocked: true`
- 文档是活的：澄清推进时更新 `status`，已确认 Q 转入 `confirmed` 并标 `origin: 已澄清确认`

## 示例

```json
{
  "meta": {
    "feature": "手机号登录",
    "source": "产品口述 + 一张登录页草图",
    "status": "澄清中"
  },
  "confirmed": [
    { "id": "R1", "origin": "原始", "content": "用户可用手机号 + 验证码登录" },
    { "id": "R2", "origin": "原始", "content": "验证码 6 位数字，5 分钟有效" },
    { "id": "R3", "origin": "原始", "content": "连续输错 3 次锁定账户" },
    { "id": "R4", "origin": "AI补充", "content": "验证码发送有 60 秒冷却（来源：草图小字 60秒后重新获取）" }
  ],
  "clarifications": [
    {
      "id": "Q1",
      "question": "手机号登录是替换现有登录方式，还是新增一种？",
      "impact": "决定整体登录架构改动量",
      "candidates": ["A 替换", "B 新增（推测 B）"],
      "owner": "产品",
      "status": "待回答",
      "related": ["R1", "T1"]
    },
    {
      "id": "Q2",
      "question": "未注册的手机号如何处理？",
      "impact": "缺这个分支会导致未注册用户卡死",
      "candidates": ["A 拦截提示", "B 自动引导注册"],
      "owner": "产品",
      "status": "待回答",
      "related": ["T1"]
    },
    {
      "id": "Q3",
      "question": "账户锁定后如何解锁？",
      "impact": "决定有无解锁通道、是否需要额外接口",
      "candidates": ["A 超时自动", "B 客服人工", "C 用户找回流程"],
      "owner": "产品",
      "status": "待回答",
      "related": ["R3", "T5"]
    }
  ],
  "defaults": [
    { "id": "D1", "decision": "验证码输入框只接受数字、自动聚焦下一位", "assumption": "标准体验" },
    { "id": "D2", "decision": "登录失败提示 验证码错误，请重试", "assumption": "措辞默认" },
    { "id": "D3", "decision": "账户锁定时长 30 分钟", "assumption": "行业惯例" }
  ],
  "boundaries": {
    "inScope": ["仅做手机号+验证码登录的业务逻辑"],
    "outOfScope": ["第三方登录", "找回密码"]
  },
  "exceptions": [
    { "id": "E1", "scenario": "手机号格式非法", "feedback": "提交前拦截，给出业务提示" },
    { "id": "E2", "scenario": "验证码发送失败（网关异常）", "feedback": "允许重试，提示发送失败" },
    { "id": "E3", "scenario": "网络超时", "feedback": "允许重试" }
  ],
  "tasks": [
    { "id": "T1", "title": "手机号+验证码登录接口对接", "dependencies": ["Q1", "Q2"], "blocked": true },
    { "id": "T2", "title": "验证码发送/冷却逻辑", "dependencies": ["R2", "R4"], "blocked": false },
    { "id": "T3", "title": "错误次数计数 + 锁定逻辑", "dependencies": ["R3", "D3"], "blocked": false },
    { "id": "T4", "title": "各异常分支业务处理", "dependencies": ["E1", "E2", "E3"], "blocked": false },
    { "id": "T5", "title": "解锁通道", "dependencies": ["Q3"], "blocked": true }
  ],
  "uiReference": {
    "note": "产品登录页草图，作为元素和布局的意图参考，非最终设计规格，呈现以前端实现为准"
  }
}
```
