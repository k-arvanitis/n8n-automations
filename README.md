# n8n Automations

![Last commit](https://img.shields.io/github/last-commit/k-arvanitis/n8n-automations)
![Repo size](https://img.shields.io/github/repo-size/k-arvanitis/n8n-automations)
![n8n](https://img.shields.io/badge/n8n-self--hosted-orange?logo=n8n)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4o--mini-412991?logo=openai)
![Docker](https://img.shields.io/badge/Docker-self--hosted-2496ED?logo=docker)

Production-style **n8n automation workflows** for real business processes — AI lead scoring,
a CRM pipeline with human-in-the-loop approval, automated weekly reporting, and a
WhatsApp RAG + lead-gen agent. Each project is self-contained with its own README, demo
screenshots, and setup guide.

<p align="center">
  <img src="whatsapp-assistant/screenshots/whatsapp-conversation.png" alt="WhatsApp RAG and lead-gen agent — live demo" width="320" />
  <br/>
  <em>Project 04 in action: the WhatsApp agent answers grounded support questions, then qualifies and captures a buying lead in the same thread.</em>
</p>

## Automations

| # | Project | What it does |
|---|---------|--------------|
| 01 | [lead-intake](lead-intake/) | Capture leads from a web form, score them with GPT-4o-mini, alert hot leads to Gmail + Telegram instantly, log all to Google Sheets, send a daily digest at 6pm |
| 02 | [crm-automation](crm-automation/) | Enrich & score leads against an ICP, route through a Slack human-in-the-loop approval, then auto-create a HubSpot contact + deal and draft a personalised cold outreach email |
| 03 | [ai-weekly-digest](ai-weekly-digest/) | Pull weekly metrics from Google Sheets, summarise each section with GPT-4o-mini, flag anomalies vs. the prior week, and send an HTML digest to Gmail + Slack every Monday |
| 04 | [whatsapp-assistant](whatsapp-assistant/) | A WhatsApp agent (Business Cloud API) that answers customer questions 24/7 with retrieval-grounded, memory-aware replies from GPT-4o-mini over a Qdrant knowledge base, **and** captures qualified leads to Google Sheets when a buyer shows intent |

## Infrastructure

All workflows run on a self-hosted n8n instance (Docker). The LLM provider is swappable —
OpenAI by default, with Anthropic / Groq / Gemini / Ollama dropping in via n8n's chat-model nodes.

## Contact

Built by Konstantinos Arvanitis — AI engineer & automation specialist.

- [LinkedIn](https://www.linkedin.com/in/konstantinos-arvanitis-0248b3246/)
- [GitHub](https://github.com/k-arvanitis)
- Email: konstantinos.arvanitis@outlook.com
