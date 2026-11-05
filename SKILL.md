---
name: supportwire
description: Use the SupportWire MCP server to read and manage a customer support inbox: conversations, replies, internal notes, customers, segments, tasks, team members and organization settings. Use when the user mentions SupportWire, support conversations, tickets, replying to a customer, assigning or triaging the inbox, internal notes, or support tasks.
---

# SupportWire

The SupportWire MCP server (`https://mcp.supportwire.ai/mcp`) acts on the user's live support workspace with their own role and OAuth session (`actor=self`). Writes are real: replies reach customers, and deletes are permanent.

## Rules

- Never invent IDs. Get them from a prior call:
  - conversation ids from `list_conversations`
  - customer ids from `list_customers`
  - team member (membership) ids from `list_team_members`
  - task and segment ids from `list_tasks` and `list_segments`
  - valid filter and sort values from `list_conversation_filter_options`
- Message and note bodies (`body_html`) must be HTML. Wrap plain text in `<p>` tags.
- `create_message` is customer-visible. For team-only comments use `create_note`.
- `update_message` replaces the whole body, and you can only edit or delete your own messages.
- List tools return one page. If `page_metadata.has_more` is true, call again with `after` set to `page_metadata.after` and every other argument unchanged.
- Task tools need the Tasks module enabled for the organization.
- Team and organization changes need an admin or owner role. If a call fails with a permission error, say so instead of retrying.
- Confirm with the user before outward-facing or destructive actions: `create_message`, `delete_message`, `update_message`, `delete_task`, `delete_segment`, `update_team_member`, `remove_team_member`, `invite_team_member` and `update_organization_settings`.

## Common flows

- Triage: `list_conversations` → `get_conversation` → `list_messages` → `create_message` or `create_note`
- Assign: `list_team_members` → `assign_conversation`
- Follow up: `create_task` linked to a conversation
- New outreach: `list_customers` → `create_conversation`
- Audiences: `list_customers` → `create_segment`

## If it fails

- Empty results usually mean the wrong organization was picked at sign-in. Reconnect and choose the right one.
- "Unauthorized" means the session expired or was revoked. Reconnect.
