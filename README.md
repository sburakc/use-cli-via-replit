# Run Claude Code & OpenAI Codex on Replit (Persistently)

> Use Claude Code or OpenAI Codex directly in any Replit workspace — with persistent login, history, and sessions that survive restarts.

## What is this?

Replit gives you a full cloud development environment with a terminal — and that terminal can run *any* CLI tool, including AI coding assistants like **Claude Code** and **OpenAI Codex**.

Replit actually encourages this. They published a [video walkthrough](https://www.youtube.com/watch?v=VIDEO_ID) showing how to set up Claude Code on Replit, and their Secrets system (`CLAUDE_CONFIG_DIR`, `CODEX_HOME`) makes persistent config straightforward.

I've been using both tools on Replit as my daily development setup for months. This repo documents everything I've learned — including workarounds for edge cases that aren't covered in the official docs.

## Options on Replit

Replit offers multiple ways to code with AI. Each has its strengths:

| | Replit Agent | Claude Code | OpenAI Codex |
|---|---|---|---|
| **Best for** | No-code / low-code, rapid prototyping | Deep codebase work, MCP integrations | Fast iteration, GPT model access |
| **Setup** | Built-in, zero config | This guide (~5 min) | This guide (~3 min) |
| **Model** | Replit-optimized | Claude Opus / Sonnet (your choice) | GPT-4.1 / GPT-5.4 (your choice) |
| **Full codebase access** | Yes | Yes | Yes |
| **MCP / Plugins** | No | Yes | No |
| **Terminal access** | Managed | Full shell | Full shell |
| **Persistent sessions** | Built-in | Yes (with this guide) | Yes (with this guide) |

> Replit Agent is great for getting started quickly — especially if you prefer a visual, managed experience. Claude Code and Codex give you more control over models, tools, and workflow if you want a CLI-based approach.

## The Persistence Problem

Both Claude Code and Codex store their config in the home directory (`~/.claude/` and `~/.codex/`). On Replit, the home directory outside of `/home/runner/workspace/` is ephemeral — it resets on restart.

The fix is simple: redirect config to your persistent workspace using Replit Secrets. That's what these guides walk you through.

## Guides

- **[Claude Code Setup](CLAUDE_CODE_SETUP.md)** — Persistent login, MCP servers, plugins, and symlink fix for the plugin marketplace bug
- **[Codex Setup](CODEX_SETUP.md)** — Persistent login, history, and session resume

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
> You need any Replit plan that gives you a Shell. This setup works alongside any Replit plan — it's not a replacement for Replit, it's an additional tool you can use within it.

## Background

I built [DocusPocus](https://docuspocus.com), a creator-focused document sharing platform, entirely on Replit using Claude Code and Codex as my AI pair programmers. Replit's cloud environment made it possible to develop from anywhere, and these CLI tools gave me the flexibility to choose my preferred AI models.

After months of refinement — fixing plugin bugs, solving persistence issues, and documenting workarounds — I figured this knowledge shouldn't stay buried in my private docs.

## Acknowledgments

Thanks to [Replit](https://replit.com) for building a cloud dev environment that's flexible enough to run any CLI tool, and for actively supporting third-party AI integrations through their Secrets system and documentation.

## Contributing

Found an issue or have an improvement? PRs and issues are welcome.

## License

MIT — use it however you want.
