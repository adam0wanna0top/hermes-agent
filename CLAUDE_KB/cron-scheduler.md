# Cron Scheduler (`cron/`)

## Files
| File | Purpose |
|------|---------|
| `__init__.py` | Exports: create_job, get_job, list_jobs, remove_job, update_job, pause_job, resume_job, trigger_job, tick |
| `jobs.py` | Job CRUD stored in `~/.hermes/cron/jobs.json` |
| `scheduler.py` (993 lines) | `tick()` function — check and run due jobs |

## Job Definition
- Cron expressions (standard 5-field)
- Interval-based (every N minutes/hours)
- One-shot jobs (run once at specific time)
- Skill-based (run a skill on schedule)
- Pre-run scripts (shell commands before job execution)

## Scheduler (`scheduler.py`)
- `tick()` — main function called periodically
- Uses file-based locking (`fcntl`/`msvcrt`) to prevent concurrent execution
- Creates an `AIAgent` per job with `platform="cron"`
- Inactivity-based timeouts
- Pre-run script execution
- Delivery to messaging platforms (via live adapters or standalone)

## Job Storage
- JSON file: `~/.hermes/cron/jobs.json`
- Secure file permissions
- Schedule parsing with cron expression support

## Delivery Targets
Jobs can deliver output to:
- `origin` — back to where the job was created
- `local` — log files only
- `telegram:123456` — specific Telegram chat
- `discord:channel_id` — specific Discord channel
- Any platform:chat_id combination

## Integration
- `tools/cronjob_tools.py` — agent tools for cron management
- `gateway/run.py` — runs tick() periodically when gateway is active
- Can run standalone without gateway
