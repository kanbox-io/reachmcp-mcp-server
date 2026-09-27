# Reach MCP — LinkedIn for AI agents

Reach MCP lets an AI agent operate a **real LinkedIn account** from Claude, ChatGPT, Cursor, n8n, Make or your own code: read and answer the inbox, send invitations, search Sales Navigator in plain language, publish and engage with posts — with the controls an agent needs around every call. LinkedIn only, by design.

- **Server URL**: `https://app.reachmcp.com/mcp` (Streamable HTTP)
- **Auth**: OAuth 2.1 (a Reach sign-in page opens; nothing to paste). API key fallback: `?api_key=mcp_…` or `X-API-Key`.
- **Docs for agents**: https://www.reachmcp.com/llms.txt · Tool reference: https://www.reachmcp.com/docs/mcp-tools.md
- **Trial**: 7 days, no card.

## Quick start

```json
{ "mcpServers": { "reach": { "url": "https://app.reachmcp.com/mcp" } } }
```

1. Sign up at https://app.reachmcp.com/signup and connect a LinkedIn account with the Chrome extension (your own session; no password is typed into Reach; uninstalling revokes access).
2. Add the server to your client. Ask: *"What can you do with my LinkedIn?"* — the `reach_playbooks` tool returns six ready-made workflows.
3. Run one: *"Find my LinkedIn conversations that went cold and draft a follow-up for each."* Every playbook that writes shows a numbered preview and waits for your go.

## What the agent gets — 58 tools

| Area | Tools |
|---|---|
| Start here | `reach_playbooks` |
| Inbox | `list_conversations`, `list_conversation_messages`, `send_message`, `react_message`, `star_conversation`, `archive_conversation`, `delete_conversation` |
| Sales Navigator inbox | `salesnav_list_messaging_threads`, `salesnav_list_thread_messages`, `salesnav_send_message` |
| Network | `list_connections`, `connect`, `invitation_status`, `withdraw_invitation`, `accept_invitation`, `decline_invitation`, `remove_connection`, `follow`, `list_received_invitations`, `list_sent_invitations` |
| Profiles & search | `scrape_profile`, `scrape_search`, `visit_profile`, `profile_viewers`, `salesnav_resolve_industry`, `salesnav_typeahead`, `salesnav_build_search_url` |
| Posts & engagement | `user_posts`, `scrape_post`, `scrape_my_posts`, `like_post`, `comment_post`, `reply_comment` |
| Publishing | `create_post`, `list_scheduled_posts`, `update_scheduled_post`, `delete_scheduled_post`, `upload_media_from_url`, `create_multi_photo` |
| Accounts & quotas | `list_accounts`, `get_me`, `get_account_quotas`, `update_account_quotas`, `get_account_request_logs`, `get_account_request_logs_stats`, `delete_account` |
| Webhooks | `list_webhook_endpoints`, `create_webhook_endpoint`, `update_webhook_endpoint`, `delete_webhook_endpoint`, `test_webhook_endpoint` |
| Jobs | `create_job`, `get_job`, `list_jobs`, `cancel_job`, `pause_job`, `resume_job` — a batch of messages, invitations, visits or comments Reach runs over working hours, inside the quotas, with `job.*` webhooks |

Every tool carries MCP annotations (`readOnlyHint`, `destructiveHint`, `idempotentHint`, `openWorldHint`), a description on every parameter, and an output schema.

## Six playbooks, served as MCP prompts

- **My LinkedIn week in review** — activity, rotting invitations, waiting conversations (read-only).
- **Wake up cold conversations** — threads where you spoke last and nobody answered, with a draft follow-up each.
- **Turn post reactions into leads** — everyone who liked or commented on your posts, qualified and messaged.
- **Turn profile viewers into leads** — the people who checked you out, scored and given an opener.
- **Reply to every comment** — a real answer to each comment on your recent posts, in the commenter's language.
- **A prospect list from one sentence** — "heads of sales at French SaaS companies" becomes a real Sales Navigator search, the rows, and a personalised invitation each.

## Built for agents, not just for calls

- **Daily quotas enforced server-side** — invitations, messages, visits, imports, posts, comments, reactions each have a per-account limit checked before every write. Exceed it and the call is refused with `daily_quota_reached` and the reset time. An agent told to "message everyone" cannot.
- **Errors an agent can act on** — every failure carries a stable `code`, `safe_to_retry`, `retry_at` and a `remediation` sentence; MCP failures are `isError` results, not protocol errors.
- **Idempotent writes** — pass `idempotency_key` on any write; a retry after a timeout never sends twice.
- **Signed webhooks** — `message.received`, `connection.new`, `account.status_changed`, `quota.threshold_reached`, `quota.reached`, `job.started`, `job.progress`, `job.paused`, `job.completed`. Stripe-style HMAC signature, retries for 15 hours, replay from the dashboard. Your n8n or Make workflow reacts instead of polling.
- **Natural-language Sales Navigator search** — industries resolved against LinkedIn's taxonomy, geographies and companies against live autocomplete, disambiguation when a term is ambiguous.
- **Residential proxy per account** and human-like pacing.

## Also available as REST

Base `https://app.reachmcp.com/api`, header `X-API-Key`. Interactive reference: https://app.reachmcp.com/scalar · OpenAPI: https://app.reachmcp.com/openapi.json

## Pricing

- **Standard** — for a person running their own LinkedIn from Claude or ChatGPT: $39/month for one account, graduated down to $12.
- **Builder** — for agencies and products built on the API/MCP: $10 per account for 1–4, $7 for 5–10, $6 for 11–50, $5 from 51. Ten accounts: $70/month.

Every feature in both plans. Monthly, no commitment. https://www.reachmcp.com/#pricing

## Honest about risk

Reach MCP is not an official LinkedIn API: it relies on the account owner's authenticated session and LinkedIn's internal interfaces. LinkedIn automation carries real account risk; the proxy, the enforced limits and the pacing reduce it, nothing removes it. Data is processed in the EU (Google Cloud, europe-west1); session cookies are stored encrypted and removed when the account is deleted. https://www.reachmcp.com/docs/security.md

Built by the team behind Kanbox (2,000+ teams, 2 million LinkedIn messages sent). Support: hello@reachmcp.com

---

This repository holds the LobeHub Marketplace manifest (`lhm.plugin.json`) for the hosted Reach MCP server at `https://app.reachmcp.com/mcp`. The server itself is a hosted service; its documentation for agents is at https://www.reachmcp.com/llms.txt and the official MCP Registry entry is `com.reachmcp/linkedin`.

## Install as a Gemini CLI extension

```bash
gemini extensions install https://github.com/kanbox-io/reachmcp-mcp-server
```

The extension registers the remote server with OAuth; a Reach sign-in page opens on first use. `GEMINI.md` carries the usage rules the model reads.

## Continue

`continue-block.yaml` is the MCP block for Continue (hub.continue.dev), pointing at the same endpoint.

## Cursor (plugin)

This repository is also a Cursor plugin ([Agent Plugins](https://agent-plugins.org) layout): `.cursor-plugin/plugin.json`, `mcp.json` (the remote server, OAuth on first use) and `rules/reach-mcp.mdc` (usage rules). Install from [cursor.directory](https://cursor.directory), or add the server by hand in Cursor → Settings → MCP:

```json
{ "mcpServers": { "reach": { "url": "https://app.reachmcp.com/mcp" } } }
```

## n8n

Template **Reply when a LinkedIn prospect writes**: `templates/n8n/reply-when-a-linkedin-prospect-writes.json` — Reach webhook (`message.received`, signature verified) → LLM draft → reply sent through the REST API with an `Idempotency-Key`. Import it from file, follow the sticky note.
