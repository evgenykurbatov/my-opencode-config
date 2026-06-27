# My-OpenCode-Config


[GitHub](https://github.com/evgenykurbatov/my-opencode-config.git)


A bunch of configs for [OpenCode](https://opencode.ai)


Edit `my-opencode-config/.env`, then run as

    my-opencode-config/oc [config]

Following configs are available:
- **opencode** is vanilla OpenCode, copy this directory's content into `~/.config/opencode`
- **gemini** is using Google Gemini provider directly
- **oc-omo** is using [Oh-My-OpenAgent](https://github.com/code-yeongyu/oh-my-openagent) plugin with default settings
- **oc-slim** is for [Oh-My-OpenCode-Slim](https://github.com/alvinunreal/oh-my-opencode-slim) plugin with default settings
- **oc-slim-glm** is for [Oh-My-OpenCode-Slim](https://github.com/alvinunreal/oh-my-opencode-slim) plugin, using GLM-5.2 as Orchestrator, Oracle, Council, and Fixer
- **or-slim-glm** is for the same config as **oc-slim-glm** but using [OpenRouter](https://openrouter.ai/)
