# ClawCloud Auto-Login

Automated ClawCloud keep-alive via GitHub Actions + Playwright.

## Commands

```bash
# Install dependencies
pip install playwright requests pynacl
playwright install chromium
playwright install-deps

# Run locally
python scripts/auto_login.py
```

## Architecture

- `scripts/auto_login.py` — Single-file automation script (Playwright browser automation)
- `.github/workflows/keep-alive.yml` — Cron job (every 5 days, UTC 01:00)
- `.claude/settings.json` — Claude Code hooks

## Key Classes (auto_login.py)

- `Telegram` — Bot notifications and 2FA code retrieval via `/code` command
- `SecretUpdater` — Auto-updates GitHub Secrets via API (requires PyNaCl)
- `AutoLogin` — Main login flow: ClawCloud → GitHub OAuth → region detection → keepalive

## Required Secrets

`GH_USERNAME`, `GH_PASSWORD`, `GH_SESSION`, `TG_BOT_TOKEN`, `TG_CHAT_ID`, `REPO_TOKEN`

## Gotchas

- All credentials come from GitHub Actions secrets / environment variables — never hardcode
- The script uses anti-detection measures (custom user agent, webdriver override)
- `GH_SESSION` cookie auto-updates on successful login if `REPO_TOKEN` is set
- Region is auto-detected from redirect URL after OAuth (e.g. `ap-southeast-1.console.claw.cloud`)
- 2FA supports both GitHub Mobile approval and TOTP via Telegram `/code 123456`
