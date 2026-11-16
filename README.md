# [🚀观看我的哔哩哔哩视频](https://space.bilibili.com/3493277319825652)

# OpenClaw Remote MiniMax Setup Skill

A reusable OpenClaw skill for deploying and validating **OpenClaw on a remote Linux server over SSH** with:

- **MiniMax M2.1**
- **MiniMax China API endpoint**
- **Telegram bot integration**
- real-world first-boot troubleshooting

This repository packages the exact deployment pattern distilled from a successful live setup, but all secrets and host-specific details have been removed.

## What this skill covers

The skill is designed for agents or operators who need a repeatable workflow for:

1. reading the official OpenClaw docs first
2. installing OpenClaw safely on a remote server
3. configuring `minimax/MiniMax-M2.1`
4. wiring up a Telegram bot
5. validating `gateway.mode=local`
6. fixing the classic `systemctl --user` persistence issue with `loginctl enable-linger`
7. verifying both the model path and the Telegram path

## Repository layout

```text
.
├── README.md
├── README_CN.md
├── dist/
│   └── openclaw-remote-minimax-setup.skill
└── skill/
    └── openclaw-remote-minimax-setup/
        ├── SKILL.md
        └── references/
            ├── config-template.md
            ├── failure-modes.md
            └── workflow.md
```

## Included troubleshooting knowledge

This skill explicitly captures several deployment pitfalls that are easy to miss:

- `openclaw config validate` can pass while the gateway still refuses first boot
- missing `gateway.mode=local` can block startup
- Telegram may return `chat not found` until the user presses **Start** in the bot DM
- a remote `systemctl --user` service can silently die after SSH disconnect unless **linger** is enabled

## How to use

### Option 1: Use the source skill folder

Copy the folder below into your OpenClaw skills directory or your own skill workspace:

```text
skill/openclaw-remote-minimax-setup/
```

### Option 2: Use the packaged `.skill` archive

Use the packaged file in `dist/` if your workflow prefers distributing a single artifact.

## Public-safety note

This repository is intentionally sanitized.

It does **not** contain:

- real server IPs
- usernames or passwords tied to a real deployment
- Telegram bot tokens
- MiniMax API keys
- gateway tokens

All examples use placeholders or generic values.

## Why this exists

A remote OpenClaw deployment often fails not because the install is hard, but because the last 10% is full of traps:

- runtime config gates
- provider/model mismatches
- Telegram first-contact behavior
- user-service persistence on VPS hosts

This skill turns those lessons into a repeatable deployment and debugging flow.

## License

This repository is provided as-is for operational reuse and adaptation.
