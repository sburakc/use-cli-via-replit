# OpenAI Codex CLI - Replit Persistent Setup Guide

**Last Updated:** March 2026

---

## EVERY STARTUP (Copy-Paste)

```bash
codex --dangerously-bypass-approvals-and-sandbox
```

That's it. If the first-time setup was done correctly, login, history, `resume`, state, and settings persist in the `codex-user/` directory.

---

## FIRST-TIME SETUP

Follow these steps in order.

### Step 1: Replit Secrets (Add via UI)

**Required Secret:**

| Key | Value |
|-----|-------|
| `CODEX_HOME` | `/home/runner/workspace/codex-user` |

> This tells Codex to use a persistent directory inside your workspace instead of the default `~/.codex` (which Replit wipes on restart).

**Optional Secret:**

| Key | When needed |
|-----|-------------|
| `OPENAI_API_KEY` | Only if you want to use an API key instead of ChatGPT login |

> If you log in with ChatGPT, you don't need `OPENAI_API_KEY`.

### Step 2: Add to `.gitignore`

```
codex-user/
```

> Keep auth, history, and local state out of your repo.

### Step 3: Create Persistent Directory

```bash
mkdir -p /home/runner/workspace/codex-user
```

> `CODEX_HOME` must point to an existing directory. Codex will error if it doesn't exist.

### Step 4: Install Codex CLI

```bash
npm install -g @openai/codex
```

### Step 5: First Login

```bash
codex --dangerously-bypass-approvals-and-sandbox
```

You'll be prompted with two options:
1. `Sign in with ChatGPT`
2. `API key` login

If you sign in with ChatGPT, credentials are stored persistently in `codex-user/auth.json`.

### Step 6: Verify

```bash
echo $CODEX_HOME
# Expected: /home/runner/workspace/codex-user

codex --version

codex resume --all
```

> `resume --all` removes the current-directory filter, showing sessions from all directories.

---

## DO I NEED A SYMLINK?

No.

Unlike Claude Code (which has a plugin marketplace bug requiring a symlink), Codex's core auth/history/resume flow works entirely through `CODEX_HOME`. No extra symlinks needed.

---

## EVERY NEW SHELL

Just this:

```bash
codex --dangerously-bypass-approvals-and-sandbox
```

If the Replit Secret is set correctly, every new shell automatically picks up `CODEX_HOME`.

---

## DIRECTORY STRUCTURE

```
/home/runner/workspace/
├── codex-user/                ← USER settings (gitignored)
│   ├── auth.json              ← Login credentials
│   ├── config.toml            ← User settings
│   ├── history.jsonl          ← Prompt history
│   ├── sessions/              ← Saved session/thread data
│   ├── state_*.sqlite         ← Session/state database
│   ├── logs_*.sqlite          ← Log database
│   ├── skills/                ← Skill cache/files
│   └── memories/              ← Memory data
└── .gitignore
```

---

## RESUME: HOW IT WORKS

```bash
codex resume              # List saved sessions
codex resume --last       # Continue the most recent session
codex resume --all        # Show sessions from all directories
```

If yesterday's session is missing, the most common reasons are:
1. That session was written under a different `CODEX_HOME`
2. It defaulted to `~/.codex` (which got wiped)
3. `resume` is filtering by current directory — try `--all`

---

## MIGRATING OLD `.codex` DATA

If Codex was previously used with the default directory, old data may be here:

```bash
/home/runner/.codex
```

To migrate (with Codex closed):

```bash
mkdir -p /home/runner/workspace/codex-user
cp -R /home/runner/.codex/. /home/runner/workspace/codex-user/
```

---

## TROUBLESHOOTING

### `CODEX_HOME points to "...", but that path does not exist`

Create the directory:
```bash
mkdir -p /home/runner/workspace/codex-user
```

### Login Keeps Asking

Common causes:
- `CODEX_HOME` Secret is not active
- New shell wasn't opened (Secrets load in new shells)
- Login was written to a different directory

Check: `echo $CODEX_HOME`

### Old Sessions Not Showing

Try:
```bash
codex resume --all
ls -la /home/runner/.codex
ls -la /home/runner/workspace/codex-user
```

### `codex: command not found`

```bash
export PATH="$(npm config get prefix)/bin:$PATH"
```

### Bubblewrap Warning

```
⚠ Codex could not find system bubblewrap at /usr/bin/bwrap.
```

This is **harmless**. Replit doesn't have system-level bubblewrap. Codex ships its own vendored copy. And if you're using `--dangerously-bypass-approvals-and-sandbox`, the sandbox is disabled entirely anyway.

### Full Reset

```bash
rm -rf /home/runner/workspace/codex-user
npm uninstall -g @openai/codex
npm install -g @openai/codex
mkdir -p /home/runner/workspace/codex-user
```

Then login again.

---

## SUMMARY

Minimum requirements:

1. Replit Secret: `CODEX_HOME=/home/runner/workspace/codex-user`
2. `mkdir -p /home/runner/workspace/codex-user`
3. `.gitignore` → `codex-user/`
4. `npm install -g @openai/codex`
5. `codex --dangerously-bypass-approvals-and-sandbox`

No symlink needed. After setup, every restart is just: `codex --dangerously-bypass-approvals-and-sandbox`
