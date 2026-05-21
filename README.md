# HackClub CLI

A polished terminal chat client for the [Hack Club AI](https://ai.hackclub.com) proxy. Free access to GPT, Claude, Gemini and friends — wrapped in a fast, keyboard-first TUI with attachments, skills, MCP / Composio integrations, exports, and theming.

```
─────────────────────────────────────────────────────
You

  send me a haiku about hack club

  thinking

  The user wants a short, playful haiku...

Assistant

  Code in the moonlight
  hack clubbers tinkering on —
  small worlds blooming bright.
```

---

## Features

- **Streaming chat** with any model on the Hack Club AI proxy (GPT, Claude, Gemini, etc.)
- **Slash command menu** with live filtering (`/model`, `/attach`, `/export`, `/skills`, …)
- **Attachments** — files, folders, images, `.docx`. Folder ingestion is cached.
- **Skills** — one-shot expert prompts (`/skill:code`, `/skill:debug`, `/skill:explain`, …)
- **MCP / Composio** — call external integrations (Gmail, GitHub, Slack, Notion, etc.) from chat
- **Exports** — `/export` to download the conversation as Markdown or JSON
- **Themes** — `dark` and `light`
- **Compact mode** — smaller context, tighter UI
- **Token counter + context % gauge** in the status bar
- **Local cache** for indexed folders and tool listings

---

## Requirements

- macOS or Linux
- **Python 3.10+** (tested on 3.14)
- A **Hack Club AI key** — get one free at <https://ai.hackclub.com>
- *(optional)* A **Composio API key** for MCP integrations — get one at <https://app.composio.dev>

---

## Install

### 1. Clone the repo

```bash
git clone https://github.com/random-guy-05/hackclub-cli.git ~/Documents/hackclub-cli
cd ~/Documents/hackclub-cli
```

### 2. Create a virtualenv and install deps

```bash
python3 -m venv ~/.hackclub_venv
~/.hackclub_venv/bin/pip install -r requirements.txt
```

### 3. Export your API key(s)

Add to `~/.bashrc` (or `~/.zshrc`):

```bash
export HACKCLUB_API_KEY="sk-hc-v1-..."          # required
export COMPOSIO_API_KEY="ck_..."                 # optional — enables MCP/Composio tools
```

Reload your shell:

```bash
source ~/.bashrc
```

### 4. Add a launcher alias

Add this function to `~/.bashrc` (or `~/.zshrc`):

```bash
hackclub() {
  ~/.hackclub_venv/bin/python ~/Documents/hackclub-cli/hackclub_ai.py "$@"
}
```

Reload your shell, then run:

```bash
hackclub
```

That's it. On first launch you'll be asked to pick a workspace folder or use Playground mode.

---

## Usage

### Modes

| Mode | Description |
|---|---|
| **Playground** | Chat freely with no attached context |
| **Workspace** | Attach a folder; its files are indexed and added to context |

### Keyboard

| Key | Action |
|---|---|
| `Enter` | Send message |
| `Ctrl+J` | New line (multi-line prompts) |
| `↑` / `↓` | History (or slash-menu navigation) |
| `Tab` | Complete slash command |
| `PgUp` / `PgDn` | Scroll output |
| `Ctrl+G` | Jump to latest output |
| `Ctrl+C` / `/exit` | Quit |

### Slash commands

| Command | What it does |
|---|---|
| `/help` | List all commands |
| `/model [name\|#]` | Switch model (or open picker) |
| `/attach [path]` | Attach a file or folder |
| `/drop [#\|all]` | Remove attachment(s) |
| `/context` | List current attachments |
| `/skills` | List skill shortcuts |
| `/skill:<name>` | Run a skill prompt (e.g. `/skill:debug`) |
| `/system [text]` | View or set the system prompt |
| `/save [path]` | Save the last reply to a file |
| `/copy` | Copy the last reply to clipboard |
| `/export [json]` | Download chat to `~/Downloads` |
| `/mcp` | List connected MCP tools |
| `/cache [clear]` | Cache stats / clear cache |
| `/theme [dark\|light]` | Switch theme |
| `/compact [on\|off]` | Toggle compact mode |
| `/clear` | Clear chat history |
| `/exit` | Quit |

You can also run a one-shot prompt non-interactively:

```bash
hackclub "summarize the file ~/notes/today.md"
```

---

## MCP / Composio integration

The CLI ships with [Composio](https://composio.dev) MCP enabled out of the box. With `COMPOSIO_API_KEY` set, the assistant can call hundreds of integrations (Gmail, GitHub, Slack, Notion, Linear, etc.) directly from chat.

### Setup

1. Sign up at <https://app.composio.dev> and grab your API key.
2. `export COMPOSIO_API_KEY="ck_..."` in your shell config.
3. Connect the apps you want from the Composio dashboard.
4. In the CLI, just ask: *"send an email to bob@example.com saying I'm running late"* — the assistant will search for the right tool and execute it.

The status bar shows `mcp connected` when integrations are ready. Run `/mcp` to list all available tools.

### Custom MCP servers

Add additional MCP servers via `~/mcp.json`:

```json
{
  "mcpServers": {
    "myserver": {
      "command": "node",
      "args": ["/path/to/server.js"]
    },
    "remote": {
      "url": "https://example.com/mcp",
      "headers": { "Authorization": "Bearer ${MY_TOKEN}" }
    }
  }
}
```

Variables like `${MY_TOKEN}` are expanded from your environment.

---

## Environment variables

| Variable | Required | Default | Purpose |
|---|---|---|---|
| `HACKCLUB_API_KEY` | yes | — | Hack Club AI proxy key |
| `COMPOSIO_API_KEY` | no | — | Enables Composio MCP tools |
| `HACKCLUB_AI_BASE_URL` | no | `https://ai.hackclub.com/proxy/v1` | Override the API endpoint |
| `HC_DEFAULT_MODEL` | no | `~openai/gpt-mini-latest` | Default model on launch |
| `HC_MCP_CONFIG` | no | `~/mcp.json` | Path to MCP config file |
| `HC_MCP_CACHE_TTL` | no | `300` | MCP tool list cache TTL (seconds) |

---

## Files & data

| Path | Purpose |
|---|---|
| `~/Documents/hackclub-cli/hackclub_ai.py` | The CLI |
| `~/.hackclub_venv/` | Python virtualenv with dependencies |
| `~/.hackclub-ai-shell/prefs.json` | Saved preferences (model, theme, compact) |
| `~/.hackclub-ai-shell/history` | Prompt history |
| `~/.hackclub-ai-shell/cache/` | Indexed folder cache & MCP tool cache |
| `~/Downloads/hackclub-chat-*.md` | Exported conversations |

Nothing in this repo contains secrets — all keys are read from your shell environment.

---

## Troubleshooting

**`Missing HACKCLUB_API_KEY`** — Export the key in your shell config and re-source it.

**`mcp unavailable`** — Either `COMPOSIO_API_KEY` isn't set, or there's no `~/mcp.json`. Composio is optional.

**Arrow keys insert escape codes** — Make sure your terminal supports ANSI input. Most modern terminals do; the legacy macOS Terminal sometimes doesn't.

**Model says it can't access integrations** — Restart the CLI to pick up `COMPOSIO_API_KEY`. The status bar should say `mcp connected`.

**Stuck or laggy output** — Use `/compact on` to reduce UI work and shrink context.

---

## Updating

```bash
cd ~/Documents/hackclub-cli
git pull
~/.hackclub_venv/bin/pip install -r requirements.txt
```

---

## Uninstall

```bash
rm -rf ~/Documents/hackclub-cli ~/.hackclub_venv ~/.hackclub-ai-shell
# then remove the hackclub() function and exports from ~/.bashrc
```

---

## License

MIT — do whatever you want.
