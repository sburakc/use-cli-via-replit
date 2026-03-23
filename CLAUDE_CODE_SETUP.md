# Claude Code - Replit Persistent Setup Guide

**Last Updated:** March 2026

---

## EVERY STARTUP (Copy-Paste)

```bash
claude --dangerously-skip-permissions
```

That's it. Login, MCP, plugins, history are all persistent — no re-setup needed.

---

## FIRST-TIME SETUP (Once Per Project)

Follow these steps in order.

### Step 1: Replit Secrets (Add via UI)

| Key | Value |
|-----|-------|
| `CLAUDE_CONFIG_DIR` | `/home/runner/workspace/claude-user` |

> This redirects Claude Code's config from `~/.claude/` (wiped on restart) to your persistent workspace.
> Do NOT use `.claude/` — that directory is for project settings (permissions).

**Optional Secret:**

| Key | When needed |
|-----|-------------|
| `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS` | Set to `1` to enable experimental agent teams feature |

### Step 2: Add to `.gitignore`

```
claude-user/
```

> Keep credentials and API keys out of your repo.

### Step 3: Install Claude Code

```bash
npm i -g @anthropic-ai/claude-code
```

Or with Bun (faster):
```bash
export PATH="$HOME/.bun/bin:$PATH"
bun install -g @anthropic-ai/claude-code
export PATH="/home/runner/workspace/.cache/.bun/bin:$PATH"
```

### Step 4: First Login

```bash
claude --dangerously-skip-permissions
```

A login link will appear — click it, paste the code. It won't ask again (stored in `claude-user/`).

### Step 5: Symlink (Plugin Marketplace Bug Fix)

Claude Code's plugin installer hardcodes `~/.claude/` instead of respecting `CLAUDE_CONFIG_DIR`. Fix with a symlink:

```bash
mkdir -p /home/runner/.claude/plugins
rm -rf /home/runner/.claude/plugins/marketplaces
ln -sfn /home/runner/workspace/claude-user/plugins/marketplaces /home/runner/.claude/plugins/marketplaces
```

> Without this, installing plugins via `/plugin` gives "Source path does not exist" error.

### Step 6: Add MCP Servers (Once, Persistent)

Exit Claude Code (`Ctrl+C`), then add your MCP servers:

```bash
# Supabase
claude mcp add supabase \
  -e SUPABASE_ACCESS_TOKEN=YOUR_SUPABASE_TOKEN \
  -- npx -y @supabase/mcp-server-supabase

# GitHub
claude mcp add github \
  -e GITHUB_PERSONAL_ACCESS_TOKEN=YOUR_GITHUB_PAT \
  -- npx -y @modelcontextprotocol/server-github

# BrightData (web scraping)
claude mcp add brightdata \
  -e API_TOKEN=YOUR_BRIGHTDATA_TOKEN \
  -- npx -y @brightdata/mcp

# Verify
claude mcp list
```

**Expected result:**
- Supabase: ~29 tools
- GitHub: ~26 tools
- BrightData: ~4 tools

### Step 7: Install Plugins

Start Claude Code, type `/plugin`:

1. `frontend-design` → Install for you (user scope)
2. `code-review` → Install for you (user scope)
3. `feature-dev` → Install for you (user scope)

Restart Claude Code after installing.

---

## EVERY NEW SHELL

Just this:

```bash
claude --dangerously-skip-permissions
```

If the Replit Secret is set correctly, every new shell automatically picks up `CLAUDE_CONFIG_DIR`.

---

## DIRECTORY STRUCTURE

```
/home/runner/workspace/
├── .claude/                    ← PROJECT settings (in git)
│   └── settings.local.json    ← Permission rules (Bash allow/deny)
├── claude-user/                ← USER settings (gitignored)
│   ├── .credentials.json      ← Login credentials
│   ├── history.jsonl           ← Chat history
│   ├── plugins/                ← Plugins + marketplace
│   ├── skills/                 ← Skill files
│   └── settings.json           ← User settings
└── /home/runner/.claude/plugins/marketplaces  ← Symlink → claude-user/plugins/marketplaces
```

---

## VERIFICATION COMMANDS

```bash
# Is CLAUDE_CONFIG_DIR active?
echo $CLAUDE_CONFIG_DIR
# Expected: /home/runner/workspace/claude-user

# Are MCPs loaded?
claude mcp list

# Inside Claude Code:
/mcp          # MCP status
/plugin       # Plugin status (Installed tab)
/skills       # Loaded skills
```

---

## TROUBLESHOOTING

### Plugin "Source path does not exist" Error

Symlink is missing. Repeat Step 5.

### Login Keeps Asking

`CLAUDE_CONFIG_DIR` Secret is not active. Open a new shell (Secrets load in new shells).

Check: `echo $CLAUDE_CONFIG_DIR`

### "Command not found" Error

For npm:
```bash
export PATH="$(npm config get prefix)/bin:$PATH"
```

For Bun:
```bash
export PATH="/home/runner/workspace/.cache/.bun/bin:$PATH"
```

### Auth Error - Full Reset

```bash
rm -rf /home/runner/workspace/claude-user
rm -rf ~/.claude
npm uninstall -g @anthropic-ai/claude-code
npm install -g @anthropic-ai/claude-code
```

Then restart from Step 4.

### MCP Token Refresh

```bash
claude mcp remove github
claude mcp add github \
  -e GITHUB_PERSONAL_ACCESS_TOKEN=YOUR_NEW_TOKEN \
  -- npx -y @modelcontextprotocol/server-github
```

---

## SUMMARY

Minimum requirements:

1. Replit Secret: `CLAUDE_CONFIG_DIR=/home/runner/workspace/claude-user`
2. `.gitignore` → `claude-user/`
3. `npm i -g @anthropic-ai/claude-code`
4. `claude --dangerously-skip-permissions` (first login)
5. Symlink for plugin marketplace bug (Step 5)
6. MCP servers (Step 6, optional)

After setup, every restart is just: `claude --dangerously-skip-permissions`
