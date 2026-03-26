# 01 — Lead Intake & AI Scorer

Automated lead capture pipeline built with n8n and OpenAI. Receives leads from a web form, scores them with GPT-4o-mini (grade A/B/C), routes hot leads to Gmail + Telegram instantly, stores cold leads in Google Sheets, and sends a daily HTML digest at 6pm.

## Workflows

| File | Trigger | What it does |
|------|---------|--------------|
| `Lead Intake & AI Scorer.json` | Webhook (form submit) | Score → route → notify |
| `Lead Daily Summary Email.json` | Schedule (6pm Mon–Fri) | Read sheet → build HTML → send digest |

## Tech stack

- **n8n** (self-hosted, Docker)
- **OpenAI** GPT-4o-mini — lead scoring
- **Google Sheets** — lead storage
- **Gmail** — hot lead alerts + daily digest
- **Telegram** — instant hot lead notification

## Flow

```
Form submit
    │
    ▼
Webhook → Normalize → OpenAI Score
                            │
                    ┌───────┴────────┐
                 Grade A          Grade B/C
                    │                │
             Gmail + Telegram    Google Sheets
                    │                │
                    └───────┬────────┘
                            │
                      Log → All Leads sheet

[6pm daily]
Schedule → Read All Leads → Build HTML → Send Digest
```

## Setup

### 1. Google Sheets

Create a spreadsheet with two tabs:

**Tab 1 — `All Leads`**
```
Timestamp | Name | Email | Company | Source | Message | Grade | Reason | Intent
```

**Tab 2 — `Cold Leads`**
```
Timestamp | Name | Email | Company | Source | Message | Grade | Reason
```

Copy the Sheet ID from the URL:
`https://docs.google.com/spreadsheets/d/THIS_PART_HERE/edit`

### 2. Import workflows

In n8n: **Settings → Import** → select each JSON file separately.

### 3. Replace placeholders

Search both files for these and replace:

| Placeholder | Replace with |
|-------------|-------------|
| `YOUR_SHEET_ID` | Your Google Sheet ID |
| `YOUR_EMAIL@gmail.com` | Your Gmail address |
| `YOUR_TELEGRAM_CHAT_ID` | Your Telegram chat ID |

> **Getting your Telegram chat ID:** Message [@userinfobot](https://t.me/userinfobot) on Telegram — it replies with your chat ID instantly.

### 4. Add credentials in n8n

Each node that connects to an external service needs credentials:
- **OpenAI node** → add your OpenAI API key
- **Google Sheets nodes** → connect via Google OAuth
- **Gmail nodes** → connect via Google OAuth (same account)
- **Telegram node** → add your Bot Token (create one via [@BotFather](https://t.me/BotFather))

### 5. Connect the form

Open `form/index.html` and replace the webhook URL on line:
```js
const WEBHOOK_URL = 'https://YOUR-N8N-INSTANCE/webhook/lead-intake';
```

If running n8n locally, use [ngrok](https://ngrok.com) to expose it:
```bash
ngrok http 5678
# copy the https URL → paste into WEBHOOK_URL
```

### 6. Activate

- Turn on **both** workflows in n8n (toggle top right)
- Open `form/index.html` in a browser
- Submit a test lead with a message like: *"Hi, I need to automate my invoicing, budget is €2k, can we start next week?"*

**Expected result:**
- Gmail: hot lead alert email arrives within seconds
- Telegram: notification with lead details
- Google Sheets: row added to All Leads
- At 6pm: summary email with grade breakdown

## Customizing the AI scoring

The scoring logic lives in the **system prompt** of the OpenAI node. Edit it to match your business:

```
Grade A: mention of budget, timeline, or specific pain point
Grade B: general interest, no urgency
Grade C: spam, test submissions, no real message
```

For example a law firm might add:
```
Grade A also if: mentions "urgent", "court date", "contract", "deadline"
```

## Testing without the form

Send a test POST directly with curl:

```bash
curl -X POST https://YOUR-N8N-INSTANCE/webhook/lead-intake \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Jane Smith",
    "email": "jane@acme.com",
    "company": "Acme Corp",
    "source": "LinkedIn",
    "message": "Hi, we need to automate our onboarding process. Budget is around 3k euros, looking to start next month."
  }'
```

## Demo form

A styled contact form is included in `form/index.html`. Deploy free on GitHub Pages:

1. Push this repo to GitHub
2. Go to **Settings → Pages → Source → main branch**
3. Your form is live at `https://yourname.github.io/ai-automation-portfolio/01-lead-intake/form`
