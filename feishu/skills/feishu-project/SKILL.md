---
name: feishu-project
description: 查飞书项目需求 / 排期工时 —— feishu-project 授权（MQL 方言）。别的 skill 要需求或排期数据时引用；文档 / 日程在 lark-io skill
---

feishu-project 授权域的最小调用链。尖括号参数由调用方填，project_key / user_keys 由调用方给。文档 / 日程是另一套授权，见 `lark-io` skill。

## 查需求（MQL）

```text
search_by_mql(project_key=<project_key>, mql=<SQL>)
```

MQL 方言：反引号包表 / 字段；`status_time('<状态>') between '<起>' and '<止>'` 取状态变更时点。

## 查排期 / 工时

```text
list_schedule(project_key=<project_key>, user_keys=<成员>, start_time=<起>, end_time=<止>)
```

start_time / end_time 用 `YYYY-MM-DD`。
