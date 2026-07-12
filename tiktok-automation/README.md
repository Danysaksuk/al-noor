# TikTok Content Automation System

Daily pipeline: trend/topic research → script writing → voiceover → video
assembly with captions → human approval → scheduled TikTok posting.

Full architecture rationale, tool choices, and the growth/monetization
strategy are in the plan this was built from — see `docs/growth-playbook.md`
for the day-to-day operating guide.

> **What this sandbox could and couldn't do:** this session has no access to
> your actual VPS, TikTok account, or paid API keys, so nothing here is
> deployed yet. What's included is the full working scaffold — Docker stack,
> n8n pipeline skeleton, prompt templates, and the operating playbook — ready
> for you (or me, in a session with real credentials/server access) to deploy.

## Stack

| Component | Tool | Repo |
|---|---|---|
| Orchestration | n8n | https://github.com/n8n-io/n8n |
| Video assembly (script → footage → voice → captions) | MoneyPrinterTurbo | https://github.com/harry0703/MoneyPrinterTurbo |
| Scheduling + TikTok posting | Postiz | https://github.com/gitroomhq/postiz-app |
| Trend data (optional) | tiktok-trends-mcp | https://github.com/trendsmcp/tiktok-trends-mcp |
| Posting fallback at scale | Ayrshare | https://github.com/ayrshare/social-post-api-python |

n8n community workflow templates used as reference for the pipeline shape:
- https://n8n.io/workflows/3004-tiktok-video-automation-tool-highly-optimized-with-openai-and-replicate/
- https://n8n.io/workflows/3442-fully-automated-ai-video-generation-and-multi-platform-publishing/

## Setup order

1. **Provision a VPS** (Hetzner CX22 / DigitalOcean equivalent, ~$6-12/mo)
   with Docker + Docker Compose installed.
2. **Copy `.env.example` to `.env`** and fill in every value — generate
   strong random secrets for `POSTIZ_JWT_SECRET` and `POSTGRES_PASSWORD`.
3. **Build the MoneyPrinterTurbo image locally first** (it's not on a public
   registry): `git clone https://github.com/harry0703/MoneyPrinterTurbo && cd MoneyPrinterTurbo && docker build -t moneyprinterturbo:latest .`
4. **Bring up the stack**: `docker compose up -d` from this directory.
5. **n8n**: visit `http://<server>:5678`, log in with the basic-auth
   credentials from `.env`.
6. **Postiz**: visit `http://<server>:5000`, create an account, then connect
   your TikTok account through Postiz's OAuth flow (follow Postiz's own
   TikTok connection docs — this is what avoids doing your own TikTok
   developer-app review).
7. **MoneyPrinterTurbo**: confirm `http://<server>:8080` responds; render one
   test video with a hardcoded script (no LLM yet) to prove the render
   pipeline works before wiring it into n8n.
8. **Import the pipeline**: in n8n, Import from File → `n8n/workflows/tiktok-pipeline.json`.
   This is a skeleton — you'll need to:
   - Add your Anthropic/OpenAI credential to the two LLM nodes and paste in
     the full prompts from `prompts/topic-research.md` and
     `prompts/script-writing.md`.
   - Add your ElevenLabs API key as an HTTP header credential.
   - Add your Telegram bot token as a credential and set `TELEGRAM_CHAT_ID`.
   - Point the trend-pull node at your chosen source (TikTok Creative
     Center scrape, or the `tiktok-trends-mcp` server).
9. **Dry run**: trigger the workflow manually, approve/reject in Telegram,
   and confirm a **private/unlisted** post appears on TikTok via Postiz
   before ever enabling public posting.
10. **Enable the daily cron** only after one successful private dry run and
    one successful public test post.

## Niche

Decision pending at kickoff — see the plan's ranked recommendations
(personal-finance sub-niche is the top pick for RPM). Once locked, update
`{{niche}}` in both prompt templates in `prompts/`.

## Compliance notes (read before flipping this to fully unattended)

- TikTok's Creator Rewards Program disqualifies "AI-generated content
  without meaningful human direction" — keep the Telegram approval gate in
  the loop; don't rubber-stamp it.
- Do not swap Postiz/the official Content Posting API path for unofficial
  session-cookie upload bots — that violates TikTok's ToS and risks the
  account.
- Creator Rewards requires videos ≥60s, 10K followers, 100K views in the
  trailing 30 days, and an eligible account country — confirm eligibility
  before counting on this as the monetization path (affiliate/brand deals
  work as a parallel or fallback route on the same content).
