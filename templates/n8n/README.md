# n8n template — Reply when a LinkedIn prospect writes

Import `reply-when-a-linkedin-prospect-writes.json` in n8n (Workflows → Import from file).

What it does: Reach MCP posts a signed `message.received` event to the workflow the moment a prospect answers on LinkedIn; the workflow verifies the `Reach-Signature` (HMAC-SHA256 over `t.body`, 5-minute replay window), drafts a reply with an LLM, and sends it back through `POST /api/linkedin/{account_id}/messages` with the event id as `Idempotency-Key`, so a retried delivery never sends twice and the account's daily quota still applies.

Setup is described in the "Read me" sticky note inside the workflow. Built-in nodes only (Webhook, Code, Crypto, IF, Basic LLM Chain, OpenAI Chat Model, HTTP Request); no community node, no credential embedded.

Docs: https://www.reachmcp.com/docs/webhooks.md · REST: https://app.reachmcp.com/scalar
