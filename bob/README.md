# nono bob

Sandbox profile and nono-awareness skill for running [IBM Bob Shell](https://bob.ibm.com/docs/shell) (`bob`) inside a [nono](https://nono.sh) security sandbox.

This pack assumes `bob` is already installed (`npm install -g bobshell`). It does not install or pin `bob` itself.

Install:

```bash
nono run --profile nolabs-ai/bob -- bob
```

If the pack is not already installed, nono will prompt to pull it.

## What's in the pack

- **`policy.json`** — sandbox profile loaded as `--profile nolabs-ai/bob`. Grants `$HOME/.bob` (all Bob state: settings, OAuth tokens, logs, global context), `/tmp/bob-$UID` (temp files), read access to nono packages/profiles, plus standard Node/Rust/Python/Nix runtime groups.
- **`skills/nono-sandbox/SKILL.md`** — sandbox-awareness skill, installed to `$HOME/.bob/skills/nono-sandbox/SKILL.md`, teaching Bob how to diagnose and respond to nono sandbox denials. Add it to `~/.bob/AGENTS.md` to load it automatically in every session.

## Notes

- All Bob state lives under `~/.bob/`: settings and OAuth tokens in `~/.bob/settings/`, rotating logs in `~/.bob/logs/shell/`, and global session context in `~/.bob/AGENTS.md`. The profile grants `$HOME/.bob` to cover all of these.
- Bob stores OAuth tokens directly in `~/.bob/settings/auth-secrets.json` — no OS keychain integration, so no Keychain grant is needed.
- MCP stdio servers are spawned as child processes and inherit the sandbox. Remote MCP servers communicate over HTTPS; `network.block` is false so outbound HTTPS works. If a specific remote MCP origin is denied, add it to `open_urls.allow_origins` in a profile draft.
- To load the nono-sandbox skill automatically, append a reference to `~/.bob/skills/nono-sandbox/SKILL.md` in your `~/.bob/AGENTS.md`.
