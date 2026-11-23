# discord-gw

A read-only Discord listener for agents. One daemon (systemd user service `discord-gw`) keeps one gateway websocket open and saves the messages, edits, deletes, reactions and attachments of the chats in the config to a local SQLite store. It never sends a message, never reacts, never marks a chat read and never changes your status: the gateway ops are limited to heartbeat, identify and resume.

`slack-gw` (next folder) uses the same design and adds sending.

## Install

```sh
./install.sh                       # links bin/discord-gw into ~/.local/bin, installs the unit, creates the config
$EDITOR ~/.config/discord-gw/config.json
discord-gw login                   # takes the token from the logged-in browser (browser-harness)
systemctl --user enable --now discord-gw
discord-gw status
```

`~/.config/discord-gw/config.json`:

| Key | Meaning |
|---|---|
| `channels` | `{ "chat id": "name" }`, the chats to record |
| `default_channel` | the chat that `read` and `wait` use when you give no `--channel` |

Local files, never in git: `~/.config/discord-gw/auth.json` (the token, mode 600), `~/.local/state/discord-gw/discord.db` and `media/`.

## Commands

```sh
discord-gw status
discord-gw read [--since-msg ID | --last N | --around ID] [--channel C] [--json]
discord-gw wait (--since-msg ID | --cursor N) [--timeout 60m] [--batch 30s] [--others] [--channel C] [--json]
discord-gw thread MSG_ID           # a message, every reply to it, and the reactions
discord-gw media MSG_ID            # local files of the attachments
discord-gw login
```

`wait` exits 0 with new events, 5 at the timeout and 4 when the listener is down.
