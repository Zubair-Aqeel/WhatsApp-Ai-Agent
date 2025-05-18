# WhatsApp AI Agent — n8n Workflow 

This repository shows **how I turned WhatsApp into a mini AI-assistant** using the open-source automation tool **n8n** plus a few cloud APIs.  
All tokens and phone numbers are masked, so you can study the flow safely.


---

## 🌟 What the agent does (high-level)

1. **Listens** for new WhatsApp Business messages.  
2. **Detects** if the message is text, voice note, or image.  
3. **Transforms** the content:  
   * voice → text via Deepgram (speech-to-text)  
   * image → description via OpenAI Vision (GPT-4o-mini)  
4. **Chats** with an LLM (Groq/ChatGPT) to craft a smart reply, using short-term memory.  
5. **Responds** back to the user on WhatsApp—all inside one n8n workflow.

A thumbnail of the full workflow is in **workflow.png**.

---

## 🔧 How I built it

| Step | Tool / Node | Why |
|------|-------------|-----|
| 1 | **WhatsApp Trigger** (n8n-nodes-base.whatsAppTrigger) | Webhook supplied by Facebook’s WhatsApp Cloud API. |
| 2 | **Switch** node | Routes the flow based on message type. |
| 3a | **HTTP Request** to WhatsApp API | Downloads media (audio/image) using `Bearer <WHATSAPP_API_TOKEN>`. |
| 3b | **HTTP Request** to Deepgram | Sends the audio file (`POST /v1/listen`) for speech-to-text. |
| 3c | **OpenAI Vision** node | Sends the image bytes to GPT-4o-mini for a caption / analysis. |
| 4 | **Simple Memory** + **AI Agent** | Keeps the last 10 messages and calls Groq Chat Model for context-aware replies. |
| 5 | **WhatsApp Send** node | Sends the assistant’s reply back via WhatsApp Cloud API. |

### Key APIs used

| Provider | Endpoint | Purpose |
|----------|----------|---------|
| **Meta / WhatsApp Cloud API** | `/v17.0/PHONE_ID/messages` & media URLs | Receive + send WhatsApp messages. |
| **Deepgram** | `POST /v1/listen` | Speech-to-text for voice notes. |
| **OpenAI** | Vision (GPT-4o mini) | Analyze incoming images. |
| **Groq / OpenAI Chat** | Chat completion | Draft human-like replies. |

All credentials are stored in **n8n Credential Manager**—no real keys live in the JSON.

---

## 🚀 Quick-start (run it yourself)

1. **Clone** or download this repo.  
2. **Import** `whatsapp_agent_n8n.json` into your n8n instance (`Settings → Import workflow`).  
3. In n8n, open each credential placeholder and add your real tokens:  
   * WhatsApp Cloud API token & business phone ID  
   * Deepgram API key (or swap for Whisper)  
   * OpenAI / Groq API key  
4. Replace every `WEBHOOK_ID_HERE` placeholder with the UUID Meta gives you when you register the webhook.  
5. **Activate** the workflow, send a WhatsApp message to your test number, and watch the magic.

> **Tip:** always keep secrets in environment variables or n8n credentials, never hard-code.

---

## 🛡 Security & privacy

* The commit you’re reading is **fully sanitized**—no live tokens, phone numbers, or user data.  
* Phone IDs and webhook IDs are placeholders.  
* Before open-sourcing your own copy, scan with tools like `truffleHog` or `git-guardian`.

---

## 🤝 Contributing

Ideas, issues, or pull requests are welcome! Interesting additions:

* Swap Deepgram for open-source Whisper.  
* Plug in a different vision model.  
* Add error-handling and retry logic.


