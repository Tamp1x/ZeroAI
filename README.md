# 🪐 Zero AI — Deep Space Assistant

Local AI assistant powered by [Ollama](https://ollama.com), with full system tool access, a web GUI, an interactive CLI, and a Telegram bot interface.

Runs **completely locally** — no cloud, no data sent anywhere except Telegram Bot API for the bot integration.

---

## ✨ Features

- **Three interfaces** — CLI, Web GUI, Telegram Bot
- **18 system tools** — file system, PowerShell, web search, browser, downloads
- **Multimodal** — send images to vision-capable models
- **Streaming responses** — token-by-token output in real time
- **Security modes** — Full Access or Confirmation Required (approve each tool call)
- **Per-user Telegram settings** — thinking mode, temperature, context size, language, and more
- **Telegram whitelist** — admin controls who can use the bot and with what permissions
- **Silent autostart** — bot runs in background at Windows startup, no visible window

---

## 🖥 Requirements

- Windows 10/11
- Python 3.10+
- [Ollama](https://ollama.com) installed and running (`ollama serve`)
- At least one model installed, e.g. `ollama run qwen2.5`

Install Python dependencies:

```bash
pip install -r requirements.txt
```

---

## 🚀 Quick Start

### CLI (Terminal Interface)

```bash
python run_cli.py
```

Interactive terminal with arrow-key command completion. Type `/` + Tab to browse commands.

### GUI (Web Interface)

```bash
python run_gui.py
```

Opens `http://localhost:5000` in your browser automatically.

### Telegram Bot

```bash
python setup_telegram.py
```

Step-by-step wizard — paste your bot token from [@BotFather](https://t.me/BotFather), add your Telegram user ID, configure autostart.

Then start the bot:

```bash
python run_telegram_bot.py       # with console output
pythonw run_telegram_bot.py      # hidden background (no window)
```

---

## 📁 Project Structure

```
zero-ai/
├── cli/
│   └── main.py              # CLI interface (prompt_toolkit + Rich)
├── core/
│   ├── agent.py             # AI agent with tool-calling loop
│   ├── ollama_client.py     # Ollama REST API client
│   ├── tools.py             # 18 system tools + executor
│   └── benchmark.py        # Token speed & resource benchmark
├── gui/
│   ├── server.py            # Flask + SSE streaming server
│   └── static/
│       ├── index.html
│       ├── app.js
│       └── style.css
├── telegram_bot/
│   ├── bot.py               # Telegram bot (python-telegram-bot)
│   ├── bot_config.py        # Config manager (token, users, settings)
│   └── autostart.py        # Windows autostart installer
├── run_cli.py
├── run_gui.py
├── run_telegram_bot.py      # Bot launcher with single-instance lock
├── setup_telegram.py        # Interactive bot setup wizard
└── requirements.txt
```

---

## 🛠 CLI Commands

| Command | Description |
|---------|-------------|
| `/help` | Show all commands |
| `/models` | List installed Ollama models |
| `/use` | Pick model with arrow keys |
| `/use <name>` | Switch to specific model |
| `/info` | Info about current model |
| `/running` | Show models loaded in memory |
| `/clear` | Clear chat history |
| `/cd <path>` | Change working directory |
| `/pwd` | Print current directory |
| `/history` | Show conversation history |
| `/tools` | List all AI tools |
| `/mode` | Toggle Full Access ↔ Confirmation mode |
| `/config` | Show/set parameters (ctx, temp, thinking) |
| `/benchmark` | Run performance benchmark |
| `/exit` | Quit |

**Pro tip:** type `/` then press **Tab** to get an interactive command menu with descriptions. Arrow keys to navigate, Enter to select.

---

## 🤖 Telegram Bot Commands

### User Commands
| Command | Description |
|---------|-------------|
| `/start` | Welcome message |
| `/help` | All commands |
| `/settings` | Open interactive settings panel |
| `/models` | List available models |
| `/model <name>` | Switch model |
| `/clear` | Clear chat history |
| `/status` | Model, mode, context usage |
| `/mode` | Your current access mode |
| `/cancel` | Stop current AI response |

### Admin Commands
| Command | Description |
|---------|-------------|
| `/adduser <id> [full\|readonly]` | Add user to whitelist |
| `/removeuser <id>` | Remove user |
| `/setmode <id> <full\|readonly>` | Change user permissions |
| `/users` | List all allowed users |
| `/autostart <on\|off>` | Toggle background autostart |

---

## ⚙️ Telegram Bot Settings (`/settings`)

Each user has their own settings, configurable via inline keyboard:

| Setting | Description | Default |
|---------|-------------|---------|
| 🧠 Thinking | Enable model reasoning/thinking mode | ✅ on |
| 🌡 Temperature | Randomness 0.0–2.0 | 0.7 |
| 📏 Context Size | Token context window | 4096 |
| ⚡ Live Streaming | Edit message while AI types | ✅ on |
| 🔧 Show Tools | Show tool execution notifications | ✅ on |
| 📊 Show Stats | Speed & token count after reply | ✅ on |
| 🌐 Language | Force response language (auto/ru/en/…) | auto |

---

## 🔒 Access Modes

| Mode | What AI can do |
|------|----------------|
| **Full Access** | All 18 tools — read, write, delete, shell, programs, web |
| **Read-Only** | Read files, search, web browsing only. Cannot write files, run PowerShell, launch programs, or download files |

In CLI — toggle with `/mode`.  
In Telegram — admin sets per-user with `/setmode <id> <full|readonly>`.

---

## 🔧 Available AI Tools

| Tool | Description |
|------|-------------|
| `create_file` | Create a new file |
| `read_file` | Read file contents |
| `write_file` | Overwrite file |
| `append_file` | Append to file |
| `delete_file` | Delete a file |
| `create_directory` | Create directory |
| `list_directory` | List directory contents |
| `delete_directory` | Delete directory recursively |
| `move` | Move or rename file/directory |
| `copy` | Copy file/directory |
| `file_info` | File metadata (size, dates) |
| `search_files` | Glob pattern file search |
| `get_cwd` | Get current working directory |
| `run_powershell` | Execute PowerShell command |
| `open_program` | Open app, file, or URL |
| `fetch_url` | Fetch and parse web page |
| `web_search` | DuckDuckGo search |
| `download_file` | Download file from URL |

---

## 🔇 Silent Autostart (Windows)

The Telegram bot can start automatically and silently when Windows boots.

Setup via wizard:
```bash
python setup_telegram.py
```
Or via Telegram: `/autostart on`

Uses `pythonw.exe` launched from a `.vbs` script in the Windows Startup folder — **no CMD or console window** appears. A single-instance lock (`bot.pid`) prevents duplicate processes.

To disable: `/autostart off` in Telegram, or delete `ZeroAI_Bot.vbs` from:
```
%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup\
```

---

## 📦 Dependencies

```
flask>=3.0
requests>=2.31
rich>=13.0
prompt_toolkit>=3.0
python-telegram-bot>=20.0
beautifulsoup4>=4.12
psutil>=5.9.0
```

---

## 📝 License

MIT — free to use, modify, and distribute.
