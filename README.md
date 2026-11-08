# ZaloDesk MCP

Read, search and reply to your Zalo chats from an AI agent.
[ZaloDesk](https://zalodesk.com) is a shared Zalo inbox for teams: personal Zalo accounts and
Zalo Official Accounts (OA) in one place. This repository holds its hosted MCP server's
connection details and a [Hermes Agent](https://hermes-agent.nousresearch.com) plugin.

Server: `https://app.zalodesk.com/api/mcp` (Streamable HTTP, OAuth sign-in with your ZaloDesk account).

## Tools

Inbox:

| Tool | What it does |
|---|---|
| `list_accounts` | The Zalo accounts you can use, by workspace |
| `list_conversations` | Recent conversations of an account, with unread counts and the last message |
| `read_messages` | The messages of one conversation, oldest first, with paging |
| `search_messages` | Search an account's messages for text |
| `send_message` | Send a text message in a conversation |
| `mark_read` | Mark a conversation read |
| `find_contact_by_phone` | Find a Zalo user by phone number, to message them |
| `list_friend_requests`, `accept_friend_request` | Friend requests on a personal Zalo account |
| `list_labels`, `set_label` | Labels (Nhãn) on a conversation |

Team (Phân quyền):

| Tool | What it does |
|---|---|
| `list_team` | Everyone on the team: their Zalo accounts and chats, OA access, role, permissions, who they report to |
| `add_team_member` | Invite someone by Google email, with their Zalo, chats and permissions; returns the sign-in link to give them |
| `update_team_member` | Change a member's Zalo, chats, role, permissions or reporting line; for a PC, team lead, seat limit and settings access |
| `remove_team_member` | Remove a member, handing their customers to someone first |
| `resend_invite` | Send an expired invite again |
| `get_routing`, `update_routing` | Who gets new chats and calls (Chia khách tự động, Cuộc gọi) |
| `assign_conversation` | Give a conversation to team members |
| `list_call_log` | Recent Zalo OA calls: who rang, who answered |
| `simulate_call` | Who would ring if this customer called a Zalo OA now; rings nobody |
| `move_company_zalo`, `remove_company_zalo` | Which PC runs a company Zalo, with no QR; drop one no desktop has any more |
| `list_ai_credit`, `set_ai_credit` | What each staff PC may spend from the owner's AI wallet, and has used |

You get what your ZaloDesk role allows. An employee sees only the chats assigned to them, and a
role without send permission can read but not send. The team tools are for the workspace owner;
routing is for the owner and Quản trị viên.

## Before you start

- A ZaloDesk account: sign in with Google at [app.zalodesk.com](https://app.zalodesk.com).
- Personal Zalo accounts are served by the ZaloDesk desktop app, so the computer running it must
  be on. Zalo OA accounts work at any time.

## Hermes Agent

```bash
hermes plugins install zalodesk
hermes plugins enable zalodesk
```

The first tool call opens the ZaloDesk sign-in in your browser. Sign-in runs
`npx mcp-remote@0.1.49`, so Node.js 20.18.1+ and npm are required.

## Claude Code

```bash
claude mcp add --transport http zalodesk https://app.zalodesk.com/api/mcp
```

## Claude, ChatGPT, Cursor and other clients

Add a remote MCP server (custom connector) with the URL `https://app.zalodesk.com/api/mcp`, then
sign in when asked.

## What the Hermes plugin does on your machine

- **Network calls.** Every tool call goes to `app.zalodesk.com`. Sign-in runs
  `npx -y mcp-remote@0.1.49`, downloaded from the npm registry.
- **Stored credentials.** mcp-remote keeps the OAuth tokens in `~/.mcp-auth`. Hermes never sees
  them. A sign-in lasts 30 days from the last use.
- **Sends real messages, changes your team and spends credit.** `send_message` sends from your
  Zalo account, the team tools add, change and remove people's access, and `set_ai_credit` lets a
  staff PC spend from your AI wallet. The plugin marks the server untrusted, so
  Hermes asks before every tool that is not read-only and refuses them in unattended runs.
- **No telemetry in the package.** ZaloDesk's server records which tool ran, its result code and
  how long it took, never message content.
- The tool set is served by the remote server at runtime and is not covered by the pinned commit.

## License

MIT
