# git-idk

AI-powered commit message generator. Analyzes your staged changes and recent commit history, then suggests commit messages that match your project's style.

## Features

- **Multiple backends**: Claude CLI, Claude API, OpenAI, Gemini, Ollama
- **Style matching**: Learns from your recent commits (conventional commits, etc.)
- **Interactive**: Select, edit, retry with feedback, or enter your own message
- **Review mode**: Suggest alternative messages for existing commits (`-C`)
- **Auto-staging**: Automatically uses `-a` if nothing is staged

## Installation

```bash
# Clone and add to PATH
git clone https://github.com/youruser/git-idk.git
ln -s $(pwd)/git-idk/git-idk ~/.local/bin/git-idk

# Or just copy the script
cp git-idk /usr/local/bin/
chmod +x /usr/local/bin/git-idk
```

## Usage

```bash
# Basic usage (with staged changes)
git add .
git-idk

# Auto-stage all changes
git-idk -a

# Review an existing commit
git-idk -C HEAD~2
git-idk -C 3        # same as HEAD~3

# Use a specific backend
git-idk --ollama
git-idk --openai
git-idk --gemini
```

### Interactive Commands

After suggestions are displayed:

| Key | Action |
|-----|--------|
| `1`, `2`, `3`... | Select and commit with that message |
| `e` or `e 2` | Edit message (first, or specified number) |
| `d` | Show the diff |
| `r` | Retry with more suggestions |
| `r 5` | Retry with 5 suggestions |
| `r feedback` | Retry with feedback for the LLM |
| `r 5 feedback` | Both |
| `Enter` or `q` | Quit without committing |
| `any text` | Use that text as the commit message |

## Configuration

### Environment Variables

```bash
# Backend selection
GIT_IDK_BACKEND=ollama          # claude_cli, claude_api, openai, gemini, ollama

# Model overrides
GIT_IDK_CLAUDE_MODEL=sonnet
GIT_IDK_OPENAI_MODEL=gpt-4o
GIT_IDK_GEMINI_MODEL=gemini-1.5-pro
GIT_IDK_OLLAMA_MODEL=llama3.1

# API keys (for API backends)
ANTHROPIC_API_KEY=sk-...
OPENAI_API_KEY=sk-...
GEMINI_API_KEY=...

# Behavior
GIT_IDK_NUM_SUGGESTIONS=5
GIT_IDK_TITLE_COMMITS=20        # Recent commits to show for style
GIT_IDK_CONTEXT_COMMITS=0       # Recent commits with full diffs (expensive)
```

### Config File

Create `~/.config/git-idk.conf` — it's sourced as bash, so use unprefixed names. Pick whichever recipe fits and tweak.

**Fully local and private (Ollama, no API keys)**

```bash
BACKEND=ollama
OLLAMA_MODEL=qwen2.5:7b           # or llama3.2, codellama, deepseek-coder-v2...
NUM_SUGGESTIONS=5
```

**Remote Ollama server**

```bash
BACKEND=ollama
OLLAMA_API_URL=http://192.168.1.10:11434/api/chat
OLLAMA_MODEL=llama3.1:70b
```

**Claude CLI (default if installed — uses whatever auth `claude` already has)**

```bash
BACKEND=claude_cli
CLAUDE_CLI_MODEL=sonnet           # or haiku (default), opus
```

**Claude API directly** (set `ANTHROPIC_API_KEY` in your shell)

```bash
BACKEND=claude_api
CLAUDE_API_MODEL=claude-sonnet-4-6
```

**OpenAI** (set `OPENAI_API_KEY` in your shell)

```bash
BACKEND=openai
OPENAI_MODEL=gpt-4o               # or gpt-4o-mini (default, cheapest)
```

**OpenAI-compatible endpoint** (LM Studio, vLLM, OpenRouter, Groq...)

```bash
BACKEND=openai
OPENAI_API_URL=http://localhost:1234/v1/chat/completions
OPENAI_MODEL=local-model
```

```bash
# OpenRouter (set OPENAI_API_KEY=sk-or-... in your shell)
BACKEND=openai
OPENAI_API_URL=https://openrouter.ai/api/v1/chat/completions
OPENAI_MODEL=anthropic/claude-3.5-sonnet
```

**Google Gemini** (set `GEMINI_API_KEY` in your shell)

```bash
BACKEND=gemini
GEMINI_MODEL=gemini-2.5-flash     # or gemini-2.5-flash-lite (default), gemini-2.5-pro
```

**Corporate AI gateway** (Anthropic-compatible, with auth helper for SSO-style tokens)

```bash
BACKEND=ai_gateway
AI_GATEWAY_BASE_URL=https://ai-gateway.example.com   # /v1/messages is auto-appended
AI_GATEWAY_AUTH_HELPER=/usr/local/bin/ai-gateway-login   # prints token on stdout
AI_GATEWAY_MODEL=claude-haiku-4-5                    # optional
```

**Higher-quality suggestions** (more context, more tokens, slower)

```bash
NUM_SUGGESTIONS=5
NUM_TITLE_COMMITS=30              # show more of your commit-message style
NUM_CONTEXT_COMMITS=3             # include full diffs of N recent commits
```

You can combine a backend block with a behavior block — they're all just shell variables.

## Backends

### Claude CLI (default)

Uses the [Claude CLI](https://docs.anthropic.com/en/docs/claude-cli) if installed. No API key needed if you're logged in.

### Claude API

Direct API calls. Requires `ANTHROPIC_API_KEY`.

### OpenAI

Requires `OPENAI_API_KEY`. Also works with OpenAI-compatible endpoints via `GIT_IDK_OPENAI_URL`.

### Gemini

Requires `GEMINI_API_KEY`.

### Ollama

Local inference with [Ollama](https://ollama.ai/). Auto-pulls models if not present.

```bash
ollama serve  # Start the server
git-idk --ollama
```

## Options

```
-a, --all                 Stage all modified/deleted files
-C, --commit <ref>        Suggest messages for existing commit
-n, --num <N>             Number of suggestions (default: 3)
-t, --title-commits <N>   Recent commit titles for style (default: 15)
-c, --context-commits <N> Recent commits with full diffs (default: 0)
--model <model>           Override model for selected backend
--debug                   Show the prompt sent to the LLM
-h, --help                Show help
```

## License

GPL-3.0

## See Also

- [aicommits](https://github.com/nutlope/aicommits) - The original project which inspired this one
