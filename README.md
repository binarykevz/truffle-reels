# 🧪 truffle reels Bot

A modular Telegram bot for searching and streaming DramaBox videos. Features **Turso database** for secure token storage, **batched episodes** (20 per page), and **auto-deleting messages** (30 min lifespan) for privacy. Built with **KevLang** and **Bun**.

## ✨ Features
- 🔐 **Turso Database**: Bot token and API keys are stored securely in an edge SQLite database (libSQL).
- ⏳ **Auto-Delete**: Episodes and search results automatically delete themselves 30 minutes after being sent to keep chats clean.
- 🔍 **Smart Search**: Search dramas by title/genre.
- 🎬 **Batch Episodes**: Loads 20 episodes at a time with a "Next 20" button.
- 🚀 **Fast & Lightweight**: Built on Bun runtime.

## 🗄️ Turso Setup

### 1. Install Turso CLI
```bash
curl -sSfL https://get.tur.so/install.sh | bash
