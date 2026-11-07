# ZaloDesk MCP

Read, search and reply to your Zalo chats from an AI agent.
[ZaloDesk](https://zalodesk.com) is a shared Zalo inbox for teams: personal Zalo accounts and
Zalo Official Accounts (OA) in one place. This repository holds its hosted MCP server's
connection details and a [Hermes Agent](https://hermes-agent.nousresearch.com) plugin.

Server: `https://app.zalodesk.com/api/mcp` (Streamable HTTP, OAuth sign-in with your ZaloDesk account).

## Tools

| Tool | What it does |
|---|---|
| `list_accounts` | The Zalo accounts you can use, by workspace |
| `list_conversations` | Recent conversations of an account, with unread counts and the last message |
| `read_messages` | The messages of one conversation, oldest first, with paging |
| `search_messages` | Search an account's messages for text |
| `send_message` | Send a text message in a conversation |

You get what your ZaloDesk role allows. An employee sees only the chats assigned to them, and a
role without send permission can read but not send.

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
- **Sends real messages.** `send_message` sends from your Zalo account. The plugin marks the
  server untrusted, so Hermes asks before every send and refuses sends in unattended runs.
- **No telemetry in the package.** ZaloDesk's server records which tool ran, its result code and
  how long it took, never message content.
- The tool set is served by the remote server at runtime and is not covered by the pinned commit.

## License

MIT
