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

Create `~/.config/git-idk.conf`:

```bash
BACKEND=ollama
OLLAMA_MODEL=qwen2.5
NUM_SUGGESTIONS=5
```

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
