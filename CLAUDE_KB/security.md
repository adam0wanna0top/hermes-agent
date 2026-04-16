# Security

## SSRF Protection (`tools/url_safety.py`)
- Blocks private/internal IP addresses (10.x, 172.16-31.x, 192.168.x, 127.x, 169.254.x, ::1, fc00::/7)
- DNS rebinding protection: resolves hostname before checking
- Applied in: browser tool, web tools, webhook URLs

## Secret Redaction (`agent/redact.py`)
- Regex-based redaction of API keys, tokens, passwords in logs
- Patterns: `sk-*`, `sk-ant-*`, Bearer tokens, generic key patterns
- Applied in: `hermes_logging.py` (RedactingFormatter)

## Skill Security Scanning (`tools/skills_guard.py`)
- 80+ regex threat patterns across 10+ categories
- Scans external skills before installation
- Categories: prompt injection, data exfiltration, code execution, network access, file system access

## Dangerous Command Approval (`tools/approval.py`)
- Classifies terminal commands by risk level
- High-risk commands require user approval
- Applies to: terminal tool execution

## Memory Content Scanning
- Built-in memory tool scans for injection/exfiltration patterns
- Prevents agent from storing sensitive data in memory files

## Path Security (`tools/path_security.py`)
- Validates file paths for read/write operations
- Prevents directory traversal attacks

## Webhook Security (`gateway/platforms/webhook.py`)
- HMAC signature validation
- Rate limiting (30/min default)
- Idempotency cache (1h TTL)
- Body size limits (1MB)

## Gateway Pairing (`gateway/pairing.py`)
- 8-char codes from unambiguous alphabet
- 1-hour expiry, max 3 pending per platform
- Rate limiting: 1 per user per 10 min
- Lockout after 5 failed attempts

## Environment Variable Filtering (`tools/mcp_tool.py`)
- `_build_safe_env()` filters env vars passed to MCP stdio subprocesses
- Only whitelisted variables passed through

## Atomic File Writes (`utils.py`)
- All file writes use temp-file + fsync + os.replace
- Prevents partial corruption on crash
