# 🍽️ Calories Counting — AI Nutrition Analyzer

![n8n](https://img.shields.io/badge/Built%20with-n8n-FF6D5A?style=flat-square&logo=n8n)
![Vibe Coding](https://img.shields.io/badge/Frontend-Vibe%20Coding-A855F7?style=flat-square)
![AI](https://img.shields.io/badge/AI-Google%20Gemini%20%26%20OpenAI-4285F4?style=flat-square)
![Status](https://img.shields.io/badge/Status-Published-brightgreen?style=flat-square)

## 1. 📋 Overview

**Calories Counting** is a real-world example of what becomes possible when you combine two modern AI-first approaches: **n8n workflow automation** and **Vibe Coding**.

> 💡 **The idea:** Instead of building everything from scratch with traditional development, the frontend was created using **Vibe Coding** — describing the idea in natural language and letting AI generate the full UI instantly. The backend logic, AI calls, and data processing were all handled through an **n8n workflow**. Two powerful tools, zero traditional coding, one complete product.

This combination means:
- 🎨 **Vibe Coding** handled the frontend — a beautiful, responsive web app generated from a simple description, published and live instantly by using https://bolt.new/
- ⚙️ **n8n** handled the backend — routing requests, calling AI models, and returning structured nutrition data
- 🤖 **Google Gemini & OpenAI** handled the intelligence — analyzing food photos and text descriptions

The result is a fully functional, production-ready nutrition analyzer built in a fraction of the time it would take with conventional development.

**Calories Counting** allows users to:

- 📷 Upload a **photo** of their meal
- ✍️ Manually enter **ingredients as text**
- 🎙️ Use **voice input** to describe their food

The app returns a detailed nutritional breakdown — including **calories, protein, carbs, fat, and fiber** — for each food item, with total nutrition calculations. Results are available in both **Arabic and English**.

---

## 🛠️ Built With

| Layer | Tool | Role |
|-------|------|------|
| ⚙️ **Backend** | [n8n](https://n8n.io/) | Workflow automation — handles all logic, AI routing, and data processing |
| 🎨 **Frontend** | [Vibe Coding / bolt.new](https://bolt.new/) | AI-generated UI — built by describing the idea, no manual coding |
| 🤖 **AI (Image)** | [Google Gemini 2.5 Flash](https://openrouter.ai/) via OpenRouter | Analyzes food photos and identifies nutrition |
| 🤖 **AI (Text)** | [OpenAI GPT](https://platform.openai.com/) | Analyzes text and voice food descriptions |

---

## 🚀 Live Demo

> **Try it now → [https://calories-counting-im-49ja.bolt.host](https://calories-counting-im-49ja.bolt.host)**
>
> Just open the link and start analyzing your meals immediately. No sign-up, no login, no setup required.
>
> 🎨 **Frontend:** Vibe Coding (AI-generated UI, published instantly)
> ⚙️ **Backend:** n8n (workflow automation, AI routing, data processing)

---

## 2. 🔄 Workflow Structure

### Diagram

```
                        ┌─────────────────────────────┐
                        │         WEBHOOK              │
                        │   Receives POST request      │
                        │  (image_base64 / text /      │
                        │        language)             │
                        └──────────────┬───────────────┘
                                       │
                                       ▼
                        ┌─────────────────────────────┐
                        │          IF NODE             │
                        │  Condition: Does             │
                        │  $json.body.image_base64     │
                        │       exist?                 │
                        └──────────┬──────────┬────────┘
                                   │          │
                   ┌───────────────┘          └──────────────────┐
                 TRUE                                          FALSE
          (Image uploaded)                             (Text/Voice input)
                   │                                             │
                   ▼                                             ▼
   ┌───────────────────────────┐              ┌────────────────────────────────┐
   │      HTTP REQUEST 1       │              │        HTTP REQUEST 2           │
   │  POST → OpenRouter API    │              │  POST → OpenAI API             │
   │  Model: Gemini 2.5 Flash  │              │  Model: GPT (text analysis)    │
   │  Sends: image + prompt    │              │  Sends: text/voice + prompt    │
   └─────────────┬─────────────┘              └──────────────┬─────────────────┘
                 │                                            │
                 ▼                                            ▼
   ┌───────────────────────────┐              ┌────────────────────────────────┐
   │   CODE IN JAVASCRIPT 1    │              │     CODE IN JAVASCRIPT 2       │
   │  Parse AI JSON response   │              │   Parse AI JSON response       │
   │  Clean markdown fences    │              │   Clean markdown fences        │
   └─────────────┬─────────────┘              └──────────────┬─────────────────┘
                 │                                            │
                 └─────────────────┬──────────────────────────┘
                                   │
                                   ▼
                    ┌──────────────────────────────┐
                    │     RESPOND TO WEBHOOK        │
                    │  Returns structured JSON:     │
                    │  { status, food[], total }    │
                    └──────────────────────────────┘
```

### Node Explanations

| Node | Type | Description |
|------|------|-------------|
| **Webhook** | Trigger | Listens for POST requests from the frontend. Receives `image_base64`, `text`, and `language` fields. |
| **If** | Condition | Checks if `$json.body.image_base64` exists. Routes to the image branch (true) or the text branch (false). |
| **HTTP Request 1** | HTTP Request | Calls the **OpenRouter API** (Gemini 2.5 Flash) for image-based analysis. Sends the image as a base64 data URL with a detailed nutrition prompt. |
| **HTTP Request 2** | HTTP Request | Calls the **OpenAI API** for text/voice-based analysis. Sends the user's food description with the same structured nutrition prompt. |
| **Code in JavaScript 1 & 2** | Code | Extracts `choices[0].message.content` from the AI response, strips any markdown fences, and parses the clean JSON. |
| **Respond to Webhook** | Output | Sends the final structured nutrition JSON back to the frontend. |

---

## 3. 🎯 Use Case

This workflow is designed for anyone who wants to **track their nutrition easily** without manually looking up food data. It is particularly useful for:

- People following a **diet or fitness plan**
- Users who want to know the **caloric content of Arabic meals**
- Anyone who prefers analyzing food by **photo rather than typing**
- Developers building **nutrition apps** on top of AI APIs

---

## 4. ⚙️ How It Works

1. **User opens the web app** and selects one of three input modes: Photo, Text, or Voice.
2. **The frontend sends a POST request** to the n8n Webhook with the input data and the user's preferred language (`ar` or `en`).
3. **The If node checks** whether an image was included in the request:
   - **If YES (image):** The workflow calls **Google Gemini 2.5 Flash** via OpenRouter, which supports multimodal (image + text) input. The image is sent as a base64-encoded JPEG.
   - **If NO (text/voice):** The workflow calls **OpenAI GPT** with the user's text description.
4. **Both AI models receive a detailed system prompt** instructing them to:
   - Identify all visible food items
   - Count quantities accurately
   - Estimate portion weights
   - Calculate calories, protein, carbs, fat, and fiber using standard nutritional values
   - Return results as **strict JSON only** — no markdown, no extra text
5. **The Code node** cleans and parses the AI's JSON response.
6. **The Webhook responds** with the structured nutrition data, which the frontend renders in a clean results view.

### Example Response Format

```json
{
  "status": "success",
  "food": [
    {
      "name": "Burger",
      "quantity": "1",
      "calories": 550,
      "protein": 28,
      "carbs": 45,
      "fat": 28
    }
  ],
  "total": {
    "calories": 550,
    "protein": 28,
    "carbs": 45,
    "fat": 28
  }
}
```

---

## 5. 🛠️ Setup

### Prerequisites

- [n8n](https://n8n.io/) instance (self-hosted or cloud)
- OpenRouter API key → [openrouter.ai](https://openrouter.ai)
- OpenAI API key → [platform.openai.com](https://platform.openai.com)
- A frontend app to call the webhook (or use tools like Postman for testing)

### Steps

1. **Import the workflow** — Download `Invoice.json` (the workflow file) and import it into your n8n instance via `Workflows → Import from file`.
2. **Set your API keys:**
   - In **HTTP Request 1** (OpenRouter): Replace the `Bearer` token in the Authorization header with your OpenRouter API key.
   - In **HTTP Request 2** (OpenAI): Replace the `Bearer` token with your OpenAI API key.
3. **Activate the workflow** — Toggle the workflow to **Published** in n8n.
4. **Copy the Webhook URL** — Found in the Webhook node settings. Use this URL as the API endpoint in your frontend.
5. **Connect your frontend** — Point your app's API calls to the n8n Webhook URL. Send a POST request with:

```json
{
  "image_base64": "<base64 string>",   // for photo mode
  "text": "2 burgers and fries",       // for text mode
  "language": "ar"                      // or "en"
}
```

---

## 6. ✅ Benefits

- **Vibe Coding + n8n combo** — Proves that a complete, production-ready app can be built without traditional coding, by combining an AI-generated frontend with an n8n automation backend.
- **Multi-modal input** — Supports photo, text, and voice — making it accessible for all users.
- **Bilingual output** — Returns results in Arabic or English based on user preference, making it ideal for Arab-world users.
- **Two AI models** — Uses the best model for each task: Gemini Flash (multimodal) for images and GPT (text) for descriptions.
- **Structured JSON output** — Clean, consistent response format that any frontend can easily parse and display.
- **No database needed** — Fully stateless; every request is self-contained.
- **Easy to extend** — The workflow can be expanded to support saving meal history, daily totals, or integration with fitness apps like MyFitnessPal.
- **Low cost** — Uses efficient models (Gemini 2.5 Flash) optimized for speed and affordability.

---

## 👤 Author

**Eng Mohamed Magdy Elghandour**

🤖 AI Automation Engineer


