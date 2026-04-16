# Platform Adapters (`gateway/platforms/`)

> 26 files implementing 20+ messaging platform adapters.

## Base Class: BasePlatformAdapter (`base.py`, 2072 lines)

Abstract interface all adapters implement:
- Message handling: receive, send, truncate
- Retry with exponential backoff
- Image/audio/document caching (with SSRF protection)
- Typing indicators
- Media extraction (markdown images, local file paths)
- Message truncation (with code block awareness)
- Background task management via `asyncio.create_task()`
- Proxy support (SOCKS/HTTP)

## Platform Adapters

| Platform | File | Key Details |
|----------|------|-------------|
| Telegram | `telegram.py` (~3000 lines) | Bot API, stickers, topics, streaming via editMessageText |
| Discord | `discord.py` (~3100 lines) | discord.py library, threads, slash commands |
| Slack | `slack.py` | Bolt framework, socket mode, blocks |
| WhatsApp | `whatsapp.py` | Via Baileys bridge (Node.js subprocess) |
| Signal | `signal.py` | signal-cli REST API |
| Matrix | `matrix.py` | matrix-nio, E2EE support |
| Mattermost | `mattermost.py` | REST API + WebSocket |
| Email | `email.py` | IMAP receive + SMTP send |
| SMS | `sms.py` | Twilio API |
| DingTalk | `dingtalk.py` | Alibaba DingTalk robot |
| Feishu/Lark | `feishu.py` | Lark Open API |
| WeCom Bot | `wecom.py` | WeCom webhook mode |
| WeCom Callback | `wecom_callback.py` | Self-built app mode with encryption |
| WeChat | `weixin.py` | iLink Bot API |
| BlueBubbles | `bluebubbles.py` | iMessage via BlueBubbles |
| QQ Bot | `qqbot.py` | QQ Official Bot API v2 |
| Home Assistant | `homeassistant.py` | HA WebSocket API |
| API Server | `api_server.py` | OpenAI-compatible HTTP server |
| Webhook | `webhook.py` | Generic webhook receiver |

## API Server Adapter (`api_server.py`)
OpenAI-compatible HTTP server:
- `POST /v1/chat/completions` — chat completions
- `POST /v1/responses` — stateful with `previous_response_id`
- `GET /v1/models` — model listing
- `POST /v1/runs` — async run with SSE events
- `GET /health` — health check
- SQLite-backed `ResponseStore` for persistence
- Default port 8642

## Webhook Adapter (`webhook.py`)
Generic webhook receiver:
- HMAC signature validation
- Rate limiting (30/min default)
- Idempotency cache (1h TTL)
- Body size limits (1MB)
- Routes configured in `config.yaml`
- Default port 8644
