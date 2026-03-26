# n8n Automations

A collection of self-hosted n8n workflows for automating business processes with AI.

## Automations

| # | Folder | What it does |
|---|--------|--------------|
| 01 | [lead-intake](lead-intake/) | Capture leads from a web form, score with GPT-4o-mini, route hot leads to Gmail + Telegram, log all to Google Sheets, daily digest at 6pm |

## Infrastructure

All workflows run on a self-hosted n8n instance. You can spin one up locally with Docker — see the [official n8n docs](https://docs.n8n.io/hosting/) for setup options.