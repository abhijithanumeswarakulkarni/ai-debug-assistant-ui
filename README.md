# 🛠️ AI Debug Assistant — UI

This is the frontend for **AI Debug Assistant**, a tool that takes error messages or stack traces and returns clear explanations, suggested fixes, and helpful external resources.  
Powered by Svelte + Vite, it communicates with a backend built on FastAPI and Groq-hosted LLMs.

🔗 [Backend Repo](https://github.com/abhijithanumeswarakulkarni/ai-debug-assistant-be)

---

## ✨ Features

- 📝 Paste any error or stack trace
- 💡 Get plain-English explanations with suggested code-level fixes
- 🔗 View related external resources (Stack Overflow, MDN, etc.)
- 🔁 Built-in clipboard copy and live backend ping to prevent cold starts

---

## 🧱 Stack

- **UI**: Svelte + Vite
- **Deployments**: Vercel

---

## 🚀 Getting Started

```bash
npm install
npm run dev
