# ZaloDesk MCP

Read, search and reply to your Zalo chats from an AI agent, and manage which staff member handles
which customers.

[ZaloDesk](https://zalodesk.com) is a shared Zalo inbox for teams in Vietnam: personal Zalo accounts
and Zalo Official Accounts (OA) in one place, with roles and permissions per staff member. This
MCP server lets Claude, ChatGPT, Cursor, Hermes Agent and other MCP clients work that inbox.

- Server: `https://app.zalodesk.com/api/mcp`
- Transport: Streamable HTTP (remote, nothing to install)
- Sign-in: OAuth 2.1 with your ZaloDesk account (dynamic client registration, PKCE)
- Docs: [app.zalodesk.com/developers/mcp](https://app.zalodesk.com/developers/mcp)

## What you can ask

- "Which Zalo chats are unread? Summarise each and tell me who is waiting on a price."
- "Find the customer who sent 0912 345 678 last week and show me the conversation."
- "Search all my Zalo messages for 'hoàn tiền' and list the orders mentioned."
- "Reply to chị Lan: hàng đã gửi sáng nay, mã vận đơn là GHN123." (the agent confirms the text with you first)
- "Tag every conversation that asked about delivery with the label Giao hàng."
- "Message 0987 654 321 on Zalo: hello, this is the shop, your order is ready."
- "Accept the pending friend requests on the sales Zalo."
- "Add an@gmail.com to the team with access to the sales Zalo, reading but not sending."
- "Give the new customer from this chat to Minh."
- "Who answers Zalo OA calls right now, and who missed calls today?"

## Tools

25 tools. Only the tools that read are marked `readOnlyHint`, and the server tells the agent to
confirm every send and change with you first.

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

## Connect

The first tool call opens the ZaloDesk sign-in in your browser.

### Claude (claude.ai, Claude Desktop)

Settings → Connectors → Add custom connector, URL `https://app.zalodesk.com/api/mcp`.

### Claude Code

```bash
claude mcp add --transport http zalodesk https://app.zalodesk.com/api/mcp
```

### Cursor

`~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "zalodesk": { "url": "https://app.zalodesk.com/api/mcp" }
  }
}
```

### VS Code

`.vscode/mcp.json`:

```json
{
  "servers": {
    "zalodesk": { "type": "http", "url": "https://app.zalodesk.com/api/mcp" }
  }
}
```

### Hermes Agent

```bash
hermes plugins install zalodesk
hermes plugins enable zalodesk
```

Sign-in runs `npx mcp-remote@0.1.49`, so Node.js 20.18.1+ and npm are required.

### ChatGPT and other clients

Add a remote MCP server (custom connector) with the URL `https://app.zalodesk.com/api/mcp`. A
client that only runs local servers can use `npx -y mcp-remote https://app.zalodesk.com/api/mcp`
as the command.

### Terminal

[`zalodesk`](https://github.com/zalodesk/zalodesk-cli) on npm runs the same tools from a shell:
`npx zalodesk login`.

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

MIT for this repository (plugin files and docs). The ZaloDesk server and app are proprietary.
