# ⬡ hive

Multi-AI terminal chat. Talk to Claude and Codex from one prompt.

```
⬡ what's wrong with this probability model?
  asking both...
● Claude: The Brownian Bridge uses simple returns but volatility is from log returns...
◆ Codex: The main issue is norm_cdf maps directly to Kelly without calibration...
```

## What it does

Type a message, get answers from both Claude and Codex in parallel. Conversation history persists across messages so both agents maintain context. Drop into full interactive sessions when you need deeper work.

## Install

```bash
git clone https://github.com/YOUR_USER/hive.git
cd hive
chmod +x hive
ln -s "$(pwd)/hive" /usr/local/bin/hive
```

### Prerequisites

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code) (`claude`)
- [Codex CLI](https://github.com/openai/codex) (`codex`)
- Authenticated with both (API keys or browser login)

### Optional

- [Ensemble](https://github.com/michelhelsdingen/ensemble) for autonomous agent-to-agent collaboration (`collab` command)

## Usage

```bash
# Start in a project directory
hive ~/my-project

# Or current directory
hive
```

### Commands

| Command | What it does |
|---------|-------------|
| `<message>` | Ask **both** agents (default) |
| `c <message>` | Ask Claude only |
| `x <message>` | Ask Codex only |
| `cc` | Drop into full interactive Claude session |
| `xx` | Drop into full interactive Codex session |
| `collab <task>` | Launch autonomous agent-to-agent collab (requires Ensemble) |
| `history` | Show conversation so far |
| `clear` | Clear conversation history |
| `q` | Quit |

### Examples

```bash
⬡ review this function for bugs          # both answer
⬡ c explain the Kelly criterion math     # claude only
⬡ x what's the time complexity?          # codex only
⬡ collab audit the auth module           # agents work together autonomously
⬡ cc                                     # full interactive claude session
```

## How context works

Each message gets prepended with:

1. **Project context** — reads `CLAUDE.md` from the working directory
2. **Cross-session memory** — reads `MEMORY.md` from Claude's memory store
3. **Conversation history** — last 20 lines of the current session

This means both agents know what you've been discussing and what the project is about, even though each call is technically stateless.

History is saved to `.hive/conversation.md` in your project directory.

## Configuration

Environment variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `HIVE_CLAUDE_MODEL` | `claude-opus-4-6` | Claude model to use |
| `HIVE_CLAUDE_FLAGS` | `--print --model ...` | Claude CLI flags |
| `HIVE_CODEX_FLAGS` | `--full-auto --ephemeral` | Codex CLI flags |
| `HIVE_CONTEXT_LINES` | `20` | Lines of history to include as context |
| `HIVE_ENSEMBLE_DIR` | `~/ensemble` | Path to Ensemble installation |
| `HIVE_DIR` | `.hive/` in working dir | Where to store conversation history |

## Architecture

```
You ──► hive (bash) ──┬──► claude --print "context + message"
                      │
                      ├──► codex exec "context + message"
                      │
                      └──► ensemble run (autonomous collab)
                                │
                           Claude ◄──► Codex
                           (they talk to each other)
```

- **Default mode**: Both agents answer in parallel. You see both responses.
- **Interactive mode** (`cc`/`xx`): Full agent session with all tools, skills, and hooks.
- **Collab mode**: Agents collaborate autonomously via Ensemble's message bus.

## License

MIT
