---
name: zalo-inbox
description: Work a Zalo inbox through ZaloDesk. Catch up on unread chats, find a customer's conversation, search past messages and reply in Zalo. Use for any request about Zalo messages, Zalo customers or groups, or a Zalo Official Account (OA).
---

# Zalo inbox through ZaloDesk

1. Start with `list_accounts`. Each Zalo account has an `account_id`, and `kind` is `personal` or `zalo_oa`.
   `available: false` means the computer running ZaloDesk for that personal account is off. Tell the user; retrying will not help.
2. To catch up, call `list_conversations` with `unread_only: true`, then `read_messages` for each conversation.
3. To find a customer, use `search_messages` with their name, phone number or a phrase, or look through `list_conversations`.
4. A `conversation_id` looks like `user:<id>` or `group:<id>`. Pass it back exactly as you got it.
5. Before `send_message`, show the user the conversation name and the exact text, and send only after they agree.
   Write in the language of the conversation, usually Vietnamese.
6. If a send times out, read the conversation before trying again: the message may already be there.

Errors come back as plain sentences. Pass them on to the user. A ZaloDesk role without send permission can read but not send.
