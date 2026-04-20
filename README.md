# 🤖 AI Email Response System — n8n Workflow

An end-to-end **human-in-the-loop email automation** built with n8n. When a new email arrives, it is logged to Google Sheets, sent to a Slack channel for human review, and — upon approval — an AI agent generates and sends a professional reply automatically.

---

## 📌 What It Does

1. **Monitors Gmail** for new incoming emails (polls every minute)
2. **Extracts** sender, subject, body, and timestamp
3. **Logs** the email to a Google Sheets tracker with `Pending` status
4. **Builds a Slack message** with Approve ✅ and Reject ❌ links
5. **Sends to Slack** for a human reviewer to decide
6. **Waits** up to 20 minutes for the reviewer's decision via webhook
7. **If Approved** → AI Agent (Groq / LLaMA 3.3 70B) generates a professional reply → updates Sheet → sends email
8. **If Rejected** → updates Sheet with `Rejected` status, no email sent

---

## 🧰 Tech Stack

| Tool | Purpose |
|---|---|
| **n8n** | Workflow automation engine |
| **Gmail (OAuth2)** | Email trigger + sending replies |
| **Google Sheets (OAuth2)** | Email log and status tracker |
| **Slack API** | Human approval interface |
| **Groq API (LLaMA 3.3 70B)** | AI-generated email responses |
| **LangChain Agent (n8n)** | Orchestrates the AI reply generation |
| **Webhooks** | Resume workflow after Slack decision |

---

## 🗂️ Project Structure

```
n8n-ai-email-response-system/
├── README.md
├── workflow/
│   └── ai_email_response_system.json   # Import this into n8n
└── screenshots/
    └── workflow_overview.png           # Full canvas screenshot
```

---

## 🚀 How to Import & Run

### 1. Prerequisites
- A running n8n instance (cloud or self-hosted)
- The following accounts/credentials ready:
  - Gmail account with OAuth2
  - Google Sheets account with OAuth2
  - Slack Bot Token with `chat:write` permission
  - Groq API key (free at [console.groq.com](https://console.groq.com))

### 2. Import the Workflow
1. Open your n8n instance
2. Go to **Workflows → Import from File**
3. Select `workflow/ai_email_response_system.json`
4. Click **Import**

### 3. Configure Credentials
After importing, update the credentials for each node:

| Node | Credential Needed |
|---|---|
| Gmail Trigger | Gmail OAuth2 |
| Append to Sheet | Google Sheets OAuth2 |
| Build Slack Blocks | *(no credential — Code node)* |
| Slack Approval Request | Slack API Token |
| AI Agent (Groq) | Groq API Key |
| Update Sheet - Approved | Google Sheets OAuth2 |
| Update Sheet - Rejected | Google Sheets OAuth2 |
| Send Email *(disabled)* | Gmail OAuth2 |

### 4. Set Up Google Sheet
Create a Google Sheet with these columns in order:

```
Timestamp | Sender Email | Subject | Body | Status | Response Sent | ROW ID | Generated Response
```

Update the **Sheet ID** in all Google Sheets nodes to match your sheet.

### 5. Enable & Test
1. Enable the workflow (toggle in top-right)
2. Send a test email to your Gmail account
3. Check your Slack channel for the approval message
4. Click ✅ Approve — the AI reply will be generated and logged to your sheet
5. Enable the **Send Email** node when ready to go live (it is disabled by default for safe testing)

---

## 🔄 Workflow Diagram

```
Gmail Trigger
     ↓
Extract Fields
     ↓
Append to Google Sheet (Status: Pending)
     ↓
Build Slack Message (Approve / Reject links)
     ↓
Send to Slack
     ↓
Wait for Webhook (max 20 min)
     ↓
     ├── ✅ Approved → AI Agent (Groq) → Update Sheet → Send Email
     └── ❌ Rejected → Update Sheet (Status: Rejected)
```

---

## ⚠️ Notes

- The **Send Email** node is **disabled by default**. Enable it only after testing the full flow to avoid sending unintended emails.
- Slack link previews must have `unfurl_links` and `unfurl_media` set to `false` to prevent Slack's link crawler from accidentally triggering the approve/reject webhook.
- Webhook timeout is set to **20 minutes**. If no decision is made, the workflow execution expires.
- The AI system prompt instructs the agent to act as a customer support agent and sign off as *"Customer Support Team"*.

---

## 👤 Author

**Emon Karmoker**
AI Backend Developer | Python · FastAPI · LangGraph · n8n

- 📧 emonkarmaker101@gmail.com
- 🔗 [LinkedIn](https://www.linkedin.com/in/emon-karmoker-9308431b)
- 🐙 [GitHub](https://github.com/EmonKarmaker/)

---

## 📄 License

MIT License — free to use and modify.
