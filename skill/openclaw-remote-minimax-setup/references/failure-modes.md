# Failure modes

## 1. Gateway exits right after install

### Symptom

`openclaw config validate` passes, but `openclaw gateway start` fails and the service loops.

### Common cause

`gateway.mode` is unset.

### Fix

```bash
openclaw config set gateway.mode local
openclaw gateway start
openclaw gateway status
```

### Why this matters

A config can be schema-valid and still fail the first runtime gate.

---

## 2. Telegram send returns `chat not found`

### Symptom

Outbound `sendMessage` fails with Telegram `400: Bad Request: chat not found`.

### Common causes

- the user never opened the bot DM and pressed Start
- the target chat ID is wrong
- the bot token is wrong
- the bot was removed from the destination chat

### Fix

Tell the user to:

1. open the bot by username
2. press Start
3. send `hi`

Then retry the outbound send.

---

## 3. Bot worked during SSH session, then stopped replying later

### Symptom

The deployment seemed fine while the SSH session was active, but later the bot did not respond.

### Common cause

OpenClaw was installed as a `systemctl --user` service and lingering was not enabled. The user systemd manager was torn down after the SSH login ended.

### Fix

```bash
sudo loginctl enable-linger <user>
systemctl --user restart openclaw-gateway.service
```

Then verify:

```bash
loginctl show-user <user> -p Linger
systemctl --user is-active openclaw-gateway.service
```

Expected: `Linger=yes`.

---

## 4. MiniMax silently falls back or does not behave as expected

### Symptom

The deployment looks alive, but replies are not coming from MiniMax M2.1.

### Common causes

- invalid or expired API key
- provider block missing or malformed
- wrong model casing

### Fix

Verify all three:

- provider key is valid
- model path is exactly `minimax/MiniMax-M2.1`
- a local smoke test reports provider `minimax` and model `MiniMax-M2.1`

---

## 5. Service status is ambiguous

### Symptom

`openclaw status` or `gateway status` gives mixed signals because the service recently restarted.

### Fix

Use all of these together:

```bash
openclaw gateway status
openclaw status
journalctl --user -u openclaw-gateway.service -n 100 --no-pager
```

Do not trust a single success line if the journal shows repeated SIGTERM or start loops.
