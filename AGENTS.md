# Agent Configuration: Temperature Rules

This document outlines critical temperature settings for OpenCode agents, specifically for the Kimi model family.

## The Temperature Constraint

Moonshot AI models and Kimi-family variants only accept a fixed temperature value. Any other value results in an immediate failure.

- **Required Value**: `1`
- **Error if missed**: `invalid temperature: only 1 is allowed for this model`
- **Affected Models**: Any model containing `kimi` or `moonshotai/` (e.g., `moonshotai/kimi-k2.6`, `opencode-go/kimi-k2.6`).

## Why Manual Overrides Are Required

The `oh-my-opencode-slim` plugin assigns low default temperatures (0.1 to 0.7) to different agent roles. These defaults trigger errors when paired with Kimi models. You must explicitly override them in your JSON configuration.

## Where to Define Temperature

### 1. oh-my-openagent.json
Define it inside each agent block:

```json
{
  "sisyphus": {
    "model": "moonshotai/kimi-k2.6",
    "temperature": 1
  }
}
```

### 2. oh-my-opencode-slim.json
Define it within the specific agent block inside a preset:

```json
{
  "presets": {
    "default": {
      "designer": {
        "model": "opencode-go/kimi-k2.6",
        "temperature": 1
      }
    }
  }
}
```

### 3. Fallback Arrays
Always include `"temperature": 1` whenever a Kimi model appears in `fallback_models`.

## Summary Rule
If the model string contains `kimi` or `moonshotai`, you must set `"temperature": 1`. No exceptions.
