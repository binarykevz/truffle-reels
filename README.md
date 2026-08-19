# 🧪 DramaBox Telegram Bot

Modular Telegram bot for searching and streaming DramaBox videos.
Written in **KevLang-Pure v2.1.0** (transpiles to TypeScript), runs on **Bun**, stores secrets in **Turso**, and **auto-deletes episodes after 30 minutes**.

## ✨ Features

- 🔐 **Turso storage** — bot token + API endpoints live in an edge SQLite DB, not in code
- ⏳ **Auto-delete** — every episode/search message self-deletes after 30 minutes
- 📦 **Batched episodes** — 20 per batch with a ➡️ Next button
- 🧪 **Modular KevLang-Pure** — one responsibility per `.kev` module
- 🚀 **Bun-first** — install, run, and deploy with Bun

## 🏗️ Architecture

```
src/
├── bot.kev          # Entry point — wires handlers to the Bot
├── db.kev           # Turso client + config table CRUD
├── seed.kev         # One-time seeder (env → Turso)
├── config.kev       # Loads config from Turso (env fallback)
├── api.kev          # DramaBox search + episode parsing (720p only)
├── helpers.kev      # Captions + deletion scheduler
└── handlers.kev     # /start, text search, callbacks, batching
```

## 📋 Prerequisites

- [Bun](https://bun.sh) ≥ 1.0
- KevLang-Pure CLI v2.1.0:
  ```bash
  bun add -g @itskevz/kevlang-pure@2.1.0
  ```
- A bot token from [@BotFather](https://t.me/BotFather)
- A Turso database (see below)

## 🗄️ Turso Setup

```bash
curl -sSfL https://get.tur.so/install.sh | bash
turso auth signup
turso db create dramabox-bot
turso db show dramabox-bot --url          # → TURSO_URL
turso db tokens create dramabox-bot       # → TURSO_TOKEN
```

## 🚀 Quick Start

```bash
git clone https://github.com/yourusername/dramabox-bot.git
cd dramabox-bot
bun install

cp .env.example .env    # fill in BOT_TOKEN, TURSO_URL, TURSO_TOKEN

bun run seed            # upload token + API URLs into Turso (one-time)
bun run dev             # transpile + run instantly
```

### Production

```bash
bun run build           # .kev → .ts (dist/)
bun run start           # bun run dist/bot.ts
```

## ⚙️ Configuration

Everything sensitive lives in Turso's `config` table:

| Key           | Value                                      |
|---------------|--------------------------------------------|
| `BOT_TOKEN`   | Telegram bot token                         |
| `SEARCH_API`  | DramaBox search endpoint                   |
| `EPISODE_API` | DramaBox allepisode endpoint               |

Tunables in `src/config.kev`:

```kev
MAX_SEARCH_RESULTS ← 5
EPISODES_PER_BATCH ← 20
DELETION_TIME_MS ← 30 × 60 × 1000
```

## ⏳ Auto-Delete Notes

- Works out-of-the-box in private chats.
- In groups/channels the bot needs **admin rights → Delete Messages**.
- Deletion failures are logged, never crash the bot.

## 🧬 KevLang-Pure v2.1.0

kevlang-pure is the latest release line of KevLang — same symbol-first syntax, transpiles to clean TypeScript:

| Symbol | Meaning            | Symbol | Meaning          |
|--------|--------------------|--------|------------------|
| `←`    | bind (let)         | `📦`   | import           |
| `↞`    | reassign           | `⊢`    | named imports    |
| `ƒ`    | function           | `🧫`    | new              |
| `⇠`    | return             | `🔬`   | destructure      |
| `❓`    | if                 | `🧬`   | object literal   |
| `⚠`    | else / catch       | `⊕`    | concat / add     |
| `∑ ∈`  | for-of             | `≡`    | strict equal     |
| `🛡`   | try                | `↻`    | continue         |

## 🐳 Docker

```dockerfile
FROM oven/bun:latest
WORKDIR /app
COPY . .
RUN bun install
RUN bun run build
CMD ["bun", "run", "start"]
```

```bash
docker build -t dramabox-bot .
docker run -d --env-file .env dramabox-bot
```

## 📦 Deploy

**Railway** — build: `bun install && bun run build`, start: `bun run start`, add the 3 env vars.
**Fly.io** — `fly launch && fly secrets set BOT_TOKEN=... TURSO_URL=... TURSO_TOKEN=... && fly deploy`

## 📝 License

MIT
