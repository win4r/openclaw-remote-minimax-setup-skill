# Config template

Use this as a sanitized starting point for a remote OpenClaw host.

```json5
{
  gateway: {
    mode: "local",
  },

  env: {
    MINIMAX_API_KEY: "${MINIMAX_API_KEY}",
    TELEGRAM_BOT_TOKEN: "${TELEGRAM_BOT_TOKEN}",
  },

  agents: {
    defaults: {
      workspace: "~/.openclaw/workspace",
      model: {
        primary: "minimax/MiniMax-M2.1",
      },
      models: {
        "minimax/MiniMax-M2.1": { alias: "Minimax" },
      },
    },
  },

  channels: {
    telegram: {
      enabled: true,
      botToken: "${TELEGRAM_BOT_TOKEN}",
      dmPolicy: "allowlist",
      allowFrom: ["<telegram-user-id>"],
    },
  },

  models: {
    mode: "merge",
    providers: {
      minimax: {
        baseUrl: "https://api.minimaxi.com/anthropic",
        apiKey: "${MINIMAX_API_KEY}",
        api: "anthropic-messages",
        models: [
          {
            id: "MiniMax-M2.1",
            name: "MiniMax M2.1",
            reasoning: false,
            input: ["text"],
            cost: { input: 15, output: 60, cacheRead: 2, cacheWrite: 10 },
            contextWindow: 200000,
            maxTokens: 8192,
          },
        ],
      },
    },
  },
}
```

## Notes

- Keep secrets in environment placeholders in examples and public repos.
- The model reference is case-sensitive: `minimax/MiniMax-M2.1`.
- For a single-user Telegram bot, an allowlist is a good default.
- If the deployment is multi-user or group-oriented, adjust Telegram policy intentionally instead of removing controls blindly.
