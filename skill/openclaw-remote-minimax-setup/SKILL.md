---
name: openclaw-remote-minimax-setup
description: Deploy and configure OpenClaw on a remote Linux server over SSH with MiniMax M2.1 (China API) and a Telegram bot. Use when provisioning a fresh VPS, reproducing a working OpenClaw + MiniMax + Telegram setup, or debugging common first-boot failures such as `gateway.mode=local`, Telegram `chat not found`, and systemd user-service persistence after SSH disconnect.
---

# OpenClaw Remote MiniMax Setup

Set up a remote OpenClaw host in a repeatable, low-surprise way.

## Scope

Use this skill for remote Linux deployments where the user provides:

- SSH access
- A MiniMax API key
- A Telegram bot token
- A target model of `minimax/MiniMax-M2.1`

Prefer the official OpenClaw documentation first, then execute the deployment over SSH.

## Read these references when needed

- Read `references/workflow.md` for the full deployment flow.
- Read `references/config-template.md` before writing or patching the remote OpenClaw config.
- Read `references/failure-modes.md` when Telegram does not reply, the gateway exits after install, or the service dies after SSH disconnect.

## Core rules

- Do not publish or store user secrets in the skill, repo, or commit history.
- Replace all secrets in examples with environment placeholders.
- Validate the remote config before declaring success.
- Verify both the model path and the Telegram path.
- Treat a successful install as incomplete until the service survives SSH disconnect.

## Minimal workflow

1. Read the relevant OpenClaw docs for installation, providers, and Telegram.
2. Inspect the remote host: OS, sudo availability, `curl`, `node`, and `openclaw`.
3. Install OpenClaw safely.
   - Prefer fetching the official installer locally, reviewing it, copying it to the remote host, and executing it there.
4. Write a minimal config using the MiniMax China endpoint and Telegram bot token.
5. Run `openclaw config validate`.
6. Ensure `gateway.mode=local` before first service start.
7. Install and start the gateway service.
8. Enable persistence for `systemctl --user` deployments.
   - On remote VPS installs, run `sudo loginctl enable-linger <user>`.
9. Verify with:
   - `openclaw gateway status`
   - `openclaw status`
   - a local smoke test against `MiniMax-M2.1`
   - a Telegram send test
10. If outbound Telegram send says `chat not found`, tell the user to open the bot DM and press Start, then retry.

## Completion standard

Only call the deployment complete when all of the following are true:

- `openclaw gateway status` reports `Runtime: running`
- `openclaw status` shows Telegram `ON` and `OK`
- A local agent test returns through `MiniMax-M2.1`
- A Telegram test message can be delivered after the user has started the bot
- The service remains active after SSH disconnect because linger is enabled

## Output style

When reporting progress or completion:

- State what was installed
- State the configured model and provider endpoint
- State the Telegram bot username
- State the last blocking issue, if any
- End with the exact next user action if a manual Telegram step is still required
