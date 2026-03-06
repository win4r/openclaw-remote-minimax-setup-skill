# Workflow

## 1. Confirm the target outcome

Collect only the inputs that are actually required:

- server host or IP
- SSH username
- authentication method
- MiniMax API key
- Telegram bot token
- desired model: `minimax/MiniMax-M2.1`

Do not ask for extra preferences unless the user explicitly wants custom routing, multi-agent setup, or public gateway exposure.

## 2. Read the official docs first

Before touching the remote machine, check the relevant OpenClaw docs:

- getting started / install
- model provider configuration
- MiniMax provider notes
- Telegram channel behavior

Use the docs to confirm the expected config shape and current CLI behavior.

## 3. Inspect the remote host

Run quick checks first:

```bash
whoami
uname -a
command -v curl
command -v node || true
command -v openclaw || true
sudo -n true && echo SUDO_OK || echo SUDO_NEEDS_PASSWORD
```

This tells you whether you are doing a first install or patching an existing host.

## 4. Install OpenClaw safely

Prefer this pattern over `curl ... | bash` directly on the remote host:

1. Download the official installer locally.
2. Review it.
3. Copy it to the remote machine.
4. Execute it remotely.

That keeps the flow auditable and avoids shell-policy blocks in many environments.

## 5. Write the minimal working config

Use a config with these properties:

- `gateway.mode: local`
- `agents.defaults.model.primary: minimax/MiniMax-M2.1`
- `models.mode: merge`
- MiniMax provider using the China endpoint
- Telegram enabled with the bot token
- optional DM allowlist if the deployment is for one user only

See `config-template.md`.

## 6. Validate before first start

Run:

```bash
openclaw config validate
```

Do not assume a syntactically valid config is sufficient for runtime success. First-boot runtime checks can still fail.

## 7. Install and start the gateway

Recommended sequence:

```bash
openclaw gateway install
openclaw gateway start
openclaw gateway status
```

If the gateway fails immediately, check the journal and file log before changing anything else.

## 8. Fix persistence for remote `systemctl --user` services

On remote VPS installs, a user service can disappear after the SSH session ends unless lingering is enabled.

Run:

```bash
sudo loginctl enable-linger <user>
systemctl --user daemon-reload
systemctl --user restart openclaw-gateway.service
```

Then verify:

```bash
loginctl show-user <user> -p Linger
systemctl --user is-active openclaw-gateway.service
openclaw gateway status
```

Expected: `Linger=yes` and the gateway stays active.

## 9. Verify the model path

Run a tiny local agent call and confirm the provider/model in output:

```bash
openclaw agent --local --agent main --message "Reply with exactly OK." --json
```

Confirm the returned metadata shows:

- provider: `minimax`
- model: `MiniMax-M2.1`

## 10. Verify the Telegram path

Check:

```bash
openclaw status
```

Look for Telegram `ON` and `OK`.

Then test outbound send:

```bash
openclaw message send --channel telegram --target <chat_id> --message "test"
```

If Telegram returns `chat not found`, the user has not started the bot DM yet, the bot token is wrong, or the chat ID is wrong.

## 11. Close with a precise next step

If Telegram still needs a manual first-contact step, do not vaguely say "pairing failed".

Say exactly:

- open `@bot_username`
- press Start
- send `hi`

Then re-test.
