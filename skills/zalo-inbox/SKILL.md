---
name: zalo-inbox
description: Work a Zalo inbox and its team through ZaloDesk. Catch up on unread chats, find a customer's conversation, search past messages, reply in Zalo, and manage who on the team handles which Zalo accounts and customers (Phân quyền). Use for any request about Zalo messages, Zalo customers or groups, a Zalo Official Account (OA), or ZaloDesk staff access.
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

## Team (Phân quyền)

7. Call `list_team` before any change. Each member says what you may change (`can`, `may_report_to`, `handover_to`); stay inside it.
8. `zalo` is `{account_id: "all"}` or `{account_id: [conversation_id, …]}`; `oa` is `{oa_account_id: "assigned" | "team" | "all"}`.
   In `update_team_member`, `zalo`, `oa` and `permissions` replace the whole value, so send the full new value.
9. `update_routing` saves the whole config: start from `get_routing` and change only what the user asked.
10. Before any add, change, removal or routing save, tell the user exactly what will change and wait for their yes.
    Removing someone cuts their access at once; offer `handover_to` when they have customers.

Errors come back as plain sentences. Pass them on to the user. A ZaloDesk role without send permission can read but not send.
