# SupportWire MCP Server

The official [Model Context Protocol](https://modelcontextprotocol.io) server for [SupportWire](https://supportwire.ai), the AI customer support platform with live chat and a shared inbox built in.

Connect it to Claude, ChatGPT, Cursor, Gemini CLI or any MCP client, then list and triage conversations, reply to customers, leave internal notes, manage customers, tasks and segments, and run your team settings in plain language, against your live SupportWire workspace.

This is a **hosted remote server**. There is nothing to install or run.

| | |
|---|---|
| Endpoint | `https://mcp.supportwire.ai/mcp` |
| Transport | Streamable HTTP |
| Auth | OAuth 2.0 with PKCE (browser sign-in on first connect, no API keys) |
| Permissions | Your SupportWire role. Tools act as you, so you can only do what you can do in the dashboard. |
| Included in | Every SupportWire plan, no extra charge. See [pricing](https://supportwire.ai/pricing). |

## Connect

### Claude Code

```bash
claude mcp add --transport http supportwire https://mcp.supportwire.ai/mcp
```

Or add it to your MCP config file:

```json
{
  "mcpServers": {
    "supportwire": {
      "type": "http",
      "url": "https://mcp.supportwire.ai/mcp"
    }
  }
}
```

### Claude Desktop and claude.ai

1. Open **Settings → Connectors** and click **Add custom connector**.
2. Name it `SupportWire` and paste `https://mcp.supportwire.ai/mcp`.
3. Click **Add**, then **Connect**, and approve access in the browser.

### ChatGPT

Add a custom MCP connector with the URL `https://mcp.supportwire.ai/mcp` and complete the sign-in when prompted.

### Cursor and VS Code

Add a new remote server with the URL `https://mcp.supportwire.ai/mcp`, or use the one-click install buttons on the [MCP docs page](https://developers.supportwire.ai/docs/mcp).

### Gemini CLI

```bash
gemini extensions install https://github.com/supportwire/supportwire-mcp
```

This repo ships a `gemini-extension.json` that points Gemini CLI at the hosted server.

### Other clients

Any client with remote MCP support: add a new remote server, paste `https://mcp.supportwire.ai/mcp`, and complete the sign-in when the browser opens.

### Verify the connection

Ask: *"List my open conversations in SupportWire."* Real conversations from your inbox mean you are connected.

## Authentication

On first connect your browser opens SupportWire. Sign in, pick the organization to connect, and approve the scopes. The client then holds an access token and calls tools as you (`actor=self`), so replies, notes and invites are attributed to you. No token or secret goes in your config file.

The server supports OAuth dynamic client registration and PKCE (S256), and publishes standard discovery metadata at `https://mcp.supportwire.ai/.well-known/oauth-authorization-server`.

Scopes: `conversations`, `messages`, `notes`, `contacts`, `segments`, `tasks`, `users` and `organization`, each as `:read` and `:write`.

Revoke access any time under **Settings → OAuth Apps** in SupportWire. Tools only see the organization you approved.

## Tools

36 tools in eight areas. Every tool carries MCP annotations (`readOnlyHint`, `destructiveHint`), so clients can tell reads from writes from deletes.

| Area | Tool | What it does |
|---|---|---|
| Conversations | `list_conversations` | List conversations with filters, sorting and cursor pagination |
| | `get_conversation` | Get full details of one conversation |
| | `create_conversation` | Start a conversation with a customer |
| | `update_conversation` | Change status, spam flag or pinned state |
| | `list_conversation_filter_options` | List the values the filters and sort accept |
| | `assign_conversation` | Assign a team member |
| | `unassign_conversation` | Remove the assignee |
| Messages | `list_messages` | List customer-facing messages in a conversation |
| | `create_message` | Send a customer-visible reply (HTML body) |
| | `update_message` | Replace one of your own message bodies |
| | `delete_message` | Permanently delete one of your own messages |
| Notes | `list_notes` | List internal, team-only notes on a conversation |
| | `create_note` | Add an internal note |
| Customers | `list_customers` | List customers with cursor pagination |
| | `get_customer` | Get one customer |
| | `create_customer` | Create a customer |
| | `update_customer` | Block, unblock, star or unstar a customer |
| Segments | `list_segments` | List saved customer filters |
| | `get_segment` | Get one segment |
| | `create_segment` | Create a segment |
| | `update_segment` | Change a segment's name, description or filter |
| | `delete_segment` | Permanently delete a segment (customers are unaffected) |
| Tasks | `list_tasks` | List internal to-dos, optionally linked to a conversation |
| | `get_task` | Get one task |
| | `create_task` | Create a task |
| | `update_task` | Change a task's title, description, status or due date |
| | `delete_task` | Permanently delete a task |
| Team | `list_team_members` | List team members and roles |
| | `get_team_member` | Get one team member |
| | `invite_team_member` | Add an existing SupportWire user to the organization with a role |
| | `update_team_member` | Change a role or archive a member |
| | `remove_team_member` | Permanently remove a member |
| Organization | `get_organization` | Get organization details |
| | `update_organization` | Change the display name (owner only) |
| | `get_organization_settings` | Read locale, AI and guest options, business hours and feature flags |
| | `update_organization_settings` | Change guest submission, the AI assistant, bot configuration and locale |

IDs are opaque: the assistant gets them from a prior tool call (for example `list_conversations` before `get_conversation`). List tools return one page at a time. Task tools need the Tasks module enabled for your organization. Team and organization changes need an admin or owner role.

## Example prompts

- "List my open conversations, newest first, and summarize the three oldest."
- "Reply to the first open conversation: we are looking into it and will update you today."
- "Assign the conversation with alice@example.com to me and add an internal note: customer prefers email follow-up."
- "Create a task to follow up with alice@example.com on Friday and link it to that conversation."
- "Mark the conversation with alice@example.com as spam."
- "Create a segment called Trial users and list who is in it."
- "Invite bob@example.com to the team as an agent."

## Learn more

- [SupportWire MCP overview](https://supportwire.ai/mcp)
- [MCP server docs](https://developers.supportwire.ai/docs/mcp)
- [Scopes](https://developers.supportwire.ai/docs/scopes)
- [Pricing](https://supportwire.ai/pricing)
- [Privacy policy](https://supportwire.ai/legal/privacy-policy) and [terms](https://supportwire.ai/legal/terms)
- [Contact](https://supportwire.ai/contact)

## License

Apache-2.0. See [LICENSE](LICENSE).
