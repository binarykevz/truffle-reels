# 🎬 DramaBox Telegram Bot

A Telegram bot for searching and streaming DramaBox short dramas, built entirely in **KevLang Pure 2.1.0** and running on **Bun**.

---

## ✨ Features

- 🔍 **Drama Search** — Search dramas by title or genre with quick-filter buttons
- 🎬 **Episode Streaming** — Fetch, decrypt, and stream episodes directly in Telegram
- 🎯 **720p Quality** — Automatically selects 720p resolution to stay within Telegram's 50MB upload limit
- 🔗 **Smart Fallback** — If video exceeds 50MB or upload fails, sends a playable link instead
- 🗄️ **Turso Database** — All API keys and endpoints stored securely in Turso (zero hardcoded secrets)
- ⏳ **Auto-Deletion** — All bot messages self-destruct after 30 minutes
- 🎨 **Stylish UI** — Compact grid keyboard showing all episodes with clean formatting
- ⚡ **Bun Runtime** — Fast startup and execution on Bun v1.3+

---

## 🏗️ Tech Stack

| Component | Technology |
|-----------|-----------|
| Language | KevLang Pure 2.1.0 |
| Runtime | Bun |
| Bot Framework | Grammy |
| Database | Turso (libSQL) |
| Video Source | DramaBox API |
| Decryption | DramaBox Decrypt API |

---

## 📁 Project Structure

├── src/
│ └── bot.kev # Main bot (single consolidated file)
├── .env # Environment variables (Turso + Bot token)
├── package.json
└── README.md

## 📋 Prerequisites

- [Bun](https://bun.sh) ≥ 1.0
- KevLang-Pure CLI v2.1.0:
  ```bash
  bun add -g kevlang-pure
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

KevLang Gotchas (Learned During Development)
No typeof operator — Use .length != null to check for arrays
No Math.ceil() — Avoid JS globals; use arithmetic instead
Use − not ⊖ for subtraction
No array indexing (arr[0]) — Use ∑ loops to find elements
Multi-line blocks only — Every { must be on its own line
No semicolons — Statements are separated by newlines
Top-level variables — Functions defined at top level can only access top-level variables (block-scoped variables inside .then() callbacks are not accessible outside)

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
