---
name: lark-io
description: 读飞书文档正文 / 列日程 —— lark-mcp 授权（调用带 useUAT=true）。别的 skill 要文档或日程数据时引用；需求 / 排期在 feishu-project skill
---

lark-mcp 授权域的最小调用链。尖括号参数由调用方填，调用统一带 `useUAT=true`。需求 / 排期是另一套授权，见 `feishu-project` skill。

## 读文档正文（wiki → docx）

```text
wiki_v1_node_search(query=<标题关键词>, useUAT=true)               → 结果里挑<选取规则>那条，取 obj_token
docx_v1_document_rawContent(document_id=<obj_token>, useUAT=true)  → 正文
```

一次搜索常混多条（多版本 / 需求 / 发版预告），**必须按<选取规则>筛，别默认取第一条**。obj_token 从上一步结果取，喂给下一步的 document_id。

## 列日程

```text
Unix 秒：date -j -f '%Y-%m-%d %H:%M:%S' '<时刻>' +%s
calendar_v4_calendarEvent_list(calendar_id=primary, start_time=<起 秒>, end_time=<止 秒>, useUAT=true)
```

start_time / end_time 是 Unix 秒，先换算再传。
