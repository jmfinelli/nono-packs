---
name: nono-sandbox
description: Diagnose nono sandbox denials and choose safe remediation when IBM Bob Shell is running inside nono.
---

# nono Sandbox

Use this skill when a `bob` tool call (bash, fs read/write, subprocess, or network access) fails with a permission, sandbox, filesystem, or network denial.

## Core Rule

nono enforces access at the OS level — Landlock on Linux, Seatbelt on macOS — before `bob` or any process it spawns ever starts. This is a hard boundary: nothing `bob` does internally can grant back an access nono denied.

## First Response

1. Identify the denied path, executable, URL, or capability from the tool output.
2. Check whether the action is inside the current project or one of `$HOME/.bob/settings` (config/auth), `$HOME/.bob/logs/shell` (logs), `/tmp/bob-$UID` (temp), or `$HOME/.bob` broadly — the paths this profile grants.
3. If the action is outside those paths, explain the exact boundary and ask for a nono profile change only when the access is genuinely needed.
4. Prefer a narrower alternative before asking for a broader profile grant.

## Common Fixes

- Keep Bob state under `$HOME/.bob/settings` (config and OAuth tokens), `$HOME/.bob/logs/shell` (rotating log files), and `$HOME/.bob/AGENTS.md` (global context) — this profile grants `$HOME/.bob` which covers all of these.
- MCP stdio servers are spawned as child processes; they inherit the sandbox. If an MCP server needs a path outside `$HOME/.bob` or the workdir, treat that as a genuine profile-expansion request.
- Remote MCP servers communicate over HTTPS; `network.block` is false so outbound HTTPS is permitted, but `open_urls` only explicitly allows `bob.ibm.com` and `status.bob.ibm.com`. If a remote MCP endpoint is denied, its origin needs adding to `open_urls.allow_origins`.
- `/tmp/bob-$UID` is granted for temporary files; use it instead of arbitrary `/tmp` paths.

## When to Ask the User

Ask the user before suggesting a profile expansion that grants access to:

- home-directory-wide writes
- credential stores
- browser profiles
- cloud-sync directories
- SSH/GPG material
- other repositories outside the current working directory
