---
pubDatetime: 2024-03-17
title: "正确获取 TG CHANNEL ID 的方式"
slug: '1710684716'
tags:
  - bot
  - telegram
description: ""
---

## 使用现成机器人获取

邀请 `@get_id_bot` 机器人进入对应群组, 自动发送 Channel ID，如果没有自动发送, 可以手动发送 `/my_id@get_id_bot` 获取 ID，记得获取后将机器人移除。

## 通过 API 获取

1. 新建机器人并获取 TOKEN
2. 邀请机器人进入 CHANNEL
3. 调用 `https://api.telegram.org/bot[BOT_TOKEN]/sendMessage?chat_id=@[channel_name]&text=[MESSAGE_TEXT]` 即可,具体内容需自行替换。如 `https://api.telegram.org/bot123:AAAA/sendMessage?chat_id=@public_chh&text=test`
4. 浏览器获取的 `result.chat.id` or `result.sender_chat.id` 都是 Channel ID
