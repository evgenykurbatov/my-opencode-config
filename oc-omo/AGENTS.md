# OpenCode + oh-my-openagent Setup Guide

## Goal
Reproduce the OpenCode + oh-my-openagent configuration on a new machine with the same model routing, agents, and provider setup.

## Prerequisites
- OpenCode >= 1.4.0 installed and on PATH
- Node.js + npm (for installing the plugin locally)
- API keys exported as environment variables (see `.env.example`)

## Files in this directory

| File | Purpose |
|------|---------|
| `opencode.jsonc` | Main OpenCode config (plugin registration) |
| `oh-my-openagent.json` | Agent model routing and fallback configuration |
| `tui.json` | TUI plugin settings |
| `package.json` | NPM dependencies for local plugin install |
| `.env.example` | Template for required environment variables |

## Step-by-step setup

### 1. Export API keys

Copy the values from `.env.example` into your shell profile or export them directly:

```bash
export MOONSHOT_API_KEY=<your_key>
export GOOGLE_GENERATIVE_AI_API_KEY=<your_key>
export ZHIPU_API_KEY=<your_key>
export OPENCODE_API_KEY=<your_key>
```

Verify OpenCode sees them:

```bash
opencode providers list
```

You should see entries for Moonshot AI, Google, Zhipu AI, Z.AI, and OpenCode Zen.

### 2. Install the oh-my-openagent plugin

OpenCode plugins should be installed locally in `~/.config/opencode/`:

```bash
# Ensure the config directory exists
mkdir -p ~/.config/opencode

# Install the plugin locally
cd ~/.config/opencode
npm install oh-my-openagent@latest

# Run the postinstall script to register the binary
node node_modules/oh-my-openagent/postinstall.mjs
```

### 3. Copy configuration files

Copy all config files from this directory to `~/.config/opencode/`:

```bash
cp opencode.jsonc   ~/.config/opencode/opencode.jsonc
cp oh-my-openagent.json ~/.config/opencode/oh-my-openagent.json
cp tui.json         ~/.config/opencode/tui.json
cp package.json     ~/.config/opencode/package.json
```

### 4. Verify the installation

Run the doctor command:

```bash
bunx oh-my-openagent doctor
```

Expected result:
- System: pass (OpenCode version detected)
- Configuration: pass
- TUI Plugin: pass
- Models: pass (11 agents, 8 categories)
- Tools: may warn about missing GitHub CLI (optional)

If the doctor reports "Loaded plugin is outdated", run:

```bash
cd ~/.config/opencode && npm install oh-my-openagent@latest
node node_modules/oh-my-openagent/postinstall.mjs
```

### 5. Start using it

```bash
opencode
```

Inside the chat:
- Type `ultrawork` or `ulw` in your prompt for full agent orchestration
- Press **Tab** for Prometheus planner mode, then `/start-work` to execute
- Use slash commands like `/init-deep`, `/refactor`, `/handoff`

## Important configuration notes

### Why `temperature: 1` on Kimi/Moonshot models?

Kimi/Moonshot models only accept `temperature=1`, including native Moonshot IDs (`moonshotai/kimi-k2.6`, `moonshotai/kimi-k2.5`) and wrapped Kimi routes such as `opencode-go/kimi-k2.6` or `opencode/kimi-k2.5`. OpenCode may send a different default otherwise, causing:

```
invalid temperature: only 1 is allowed for this model
```

Every model entry in `oh-my-openagent.json` whose model string contains `kimi` or `moonshotai/` must have `"temperature": 1` set explicitly, including entries inside `fallback_models`. Do NOT remove these.

### No Claude subscription

This config does NOT use Claude models. The primary models are:
- **Moonshot AI** (`moonshotai/kimi-k2.6`, `moonshotai/kimi-k2.5`) — main orchestration
- **OpenCode Zen** (`opencode/gpt-5.5`, `opencode/gpt-5-nano`) — GPT-native agents
- **Google** (`google/gemini-3.1-pro-preview`, `google/gemini-3-flash-preview`) — visual/artistry
- **Z.AI** (`zai-coding-plan/glm-5`) — fallbacks

If you later get a Claude subscription, you can change the primary models back to `opencode/claude-opus-4-7` or `opencode/claude-sonnet-4-6`.

### Provider priority

The fallback chains are ordered as:
1. Native provider (Moonshot, Google)
2. OpenCode Zen (`opencode/*`)
3. OpenCode Go (`opencode-go/*`)
4. Z.AI Coding Plan (`zai-coding-plan/*`)

## Troubleshooting

| Issue | Fix |
|-------|-----|
| `invalid temperature` | Ensure `"temperature": 1` is set on every `kimi` / `moonshotai/` entry, including `fallback_models` |
| `Plugin not found` | Run `npm install oh-my-openagent@latest` in `~/.config/opencode` |
| `TUI plugin entry unresolvable` | Ensure `tui.json` contains `"plugin": []` (empty array) |
| `Provider not found` | Verify env vars are exported and run `opencode providers list` |
| `Outdated plugin` | Re-run `npm install oh-my-openagent@latest` and postinstall |

## Model reference

| Agent | Primary Model | Family |
|-------|--------------|--------|
| Sisyphus | `moonshotai/kimi-k2.6` | Kimi (Claude-like) |
| Prometheus | `moonshotai/kimi-k2.6` | Kimi (Claude-like) |
| Atlas | `moonshotai/kimi-k2.6` | Kimi (Claude-like) |
| Metis | `moonshotai/kimi-k2.6` | Kimi (Claude-like) |
| Sisyphus-Junior | `moonshotai/kimi-k2.5` | Kimi (Claude-like) |
| Hephaestus | `opencode/gpt-5.5` | GPT-native |
| Oracle | `opencode/gpt-5.5` | GPT-native |
| Momus | `opencode/gpt-5.5` | GPT-native |
| Explore | `opencode/gpt-5-nano` | GPT-native (speed) |
| Librarian | `opencode-go/qwen3.5-plus` | Utility |
| Multimodal-Looker | `opencode/gpt-5.5` | GPT-native |

## Verification checklist

- [ ] OpenCode >= 1.4.0 installed
- [ ] All 4 API keys exported as environment variables
- [ ] `opencode providers list` shows 4+ providers
- [ ] `npm install oh-my-openagent@latest` ran in `~/.config/opencode`
- [ ] All 5 config files copied to `~/.config/opencode/`
- [ ] `bunx oh-my-openagent doctor` passes
- [ ] `opencode` starts without errors
