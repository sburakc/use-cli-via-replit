# Run Claude Code & OpenAI Codex on Replit (Persistently)

> **Stop paying $25+/mo for Replit Agent.** Use Claude Code or OpenAI Codex directly in any Replit workspace — with persistent login, history, and sessions that survive restarts.

I've been running both **Claude Code** and **OpenAI Codex CLI** on Replit for months as my daily development environment. After seeing the same questions pop up on Reddit and forums, I decided to share my setup.

## Why?

| | Replit Agent | Claude Code on Replit | Codex on Replit |
|---|---|---|---|
| **Cost** | $25–97/mo (Replit plan) | Free (Anthropic account) or API usage | Free (ChatGPT Plus) or API usage |
| **Model** | Replit's choice | Claude Opus / Sonnet (your choice) | GPT-4.1 / GPT-5.4 (your choice) |
| **Full codebase access** | Yes | Yes | Yes |
| **MCP / Plugins** | No | Yes | No |
| **Terminal access** | Limited | Full | Full |
| **Persistent sessions** | N/A | Yes (with this guide) | Yes (with this guide) |

## The Problem

Both Claude Code and Codex store their config in the home directory (`~/.claude/` and `~/.codex/`), which **Replit wipes on every restart**. This means you'd lose your login, history, and settings every time.

These guides solve that by redirecting config to your persistent `/home/runner/workspace/` directory.

## Guides

- **[Claude Code Setup](CLAUDE_CODE_SETUP.md)** — Full setup with persistent login, MCP servers, plugins, and symlink fix for the plugin marketplace bug
- **[Codex Setup](CODEX_SETUP.md)** — Full setup with persistent login, history, and session resume

## Quick Start

### Claude Code (5 minutes)

```bash
# 1. Set Replit Secret: CLAUDE_CONFIG_DIR = /home/runner/workspace/claude-user
# 2. Install
npm i -g @anthropic-ai/claude-code
# 3. Run & login (one-time)
claude --dangerously-skip-permissions
# 4. Every restart, just run:
claude --dangerously-skip-permissions
```

### OpenAI Codex (3 minutes)

```bash
# 1. Set Replit Secret: CODEX_HOME = /home/runner/workspace/codex-user
# 2. Install
npm install -g @openai/codex
# 3. Run & login (one-time)
codex --dangerously-bypass-approvals-and-sandbox
# 4. Every restart, just run:
codex --dangerously-bypass-approvals-and-sandbox
```

## Common Questions

**Q: What about the bubblewrap warning in Codex?**
> `⚠ Codex could not find system bubblewrap at /usr/bin/bwrap` — This is harmless. Replit doesn't have system-level bubblewrap installed, but Codex ships its own vendored copy. If you're using `--dangerously-bypass-approvals-and-sandbox`, the sandbox is disabled entirely anyway.

**Q: Can I run both Claude Code and Codex on the same Replit?**
> Yes. They use separate config directories (`claude-user/` and `codex-user/`) and don't interfere with each other.

**Q: Will my login persist across Replit restarts?**
> Yes — that's the whole point of this setup. Auth tokens, history, and sessions are stored in your workspace directory, which Replit preserves.

**Q: Do I still need a Replit paid plan?**
> You need any Replit plan that gives you a Shell (even the free tier works for public repls). You do NOT need the Replit Core/Agent plan.

## Background

I built [DocusPocus](https://docuspocus.com), a creator-focused document sharing platform, entirely on Replit using Claude Code and Codex as my AI pair programmers. This setup has been my daily driver since early 2026.

After months of refinement — fixing plugin bugs, solving persistence issues, and documenting workarounds — I figured this knowledge shouldn't stay buried in my private docs.

## Contributing

Found an issue or have an improvement? PRs and issues are welcome.

## License

MIT — use it however you want.
