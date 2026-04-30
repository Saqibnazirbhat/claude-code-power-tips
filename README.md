# Claude Code — 14 Power Tips & Workflows

A curated collection of practical workflows that take Claude Code from "neat AI tool" to **daily driver**. Whether this is your first week with Claude Code or you've been shipping with it for months, there's something here for you.

> Each tip is structured the same way:
> **What it is** → **Why you'd use it** → **How to do it**

---

## Table of Contents

| # | Tip | Best for |
|---|-----|----------|
| 1 | [Mobile App](#1-claude-code-has-a-mobile-app) | Beginner |
| 2 | [Teleport Sessions Across Devices](#2-teleport-sessions-between-mobile-web-desktop--terminal) | Beginner |
| 3 | [`/loop` and `/schedule`](#3-automate-claude-with-loop-and-schedule) | Intermediate |
| 4 | [Hooks](#4-use-hooks-to-deterministically-run-logic) | Intermediate |
| 5 | [Cowork Dispatch](#5-cowork-dispatch--remote-control-for-claude-desktop) | Beginner |
| 6 | [Chrome Extension for Frontend](#6-use-the-chrome-extension-for-frontend-work) | Beginner |
| 7 | [Desktop App for Web Servers](#7-let-claude-start--test-web-servers-via-desktop-app) | Beginner |
| 8 | [Fork Your Session](#8-fork-your-session) | Intermediate |
| 9 | [`/btw` for Side Queries](#9-use-btw-for-side-queries) | Beginner |
| 10 | [Git Worktrees](#10-use-git-worktrees-for-massive-parallelism) | Pro |
| 11 | [`/batch` for Massive Changesets](#11-use-batch-to-fan-out-massive-changesets) | Pro |
| 12 | [`--bare` for Faster SDK Startup](#12-use---bare-to-speed-up-sdk-startup-by-up-to-10x) | Pro |
| 13 | [`--add-dir` for Multi-Repo Work](#13-use---add-dir-to-give-claude-access-to-more-folders) | Intermediate |
| 14 | [Custom Agents](#14-use---agent-to-give-claude-a-custom-system-prompt--tools) | Pro |

---

## 1. Claude Code Has a Mobile App

**What it is:** A full Claude Code experience on your phone.

**Why you'd use it:** Make code changes, review PRs, or kick off a long-running task without opening your laptop.

**How to do it:**
- Download the **Claude app** for [iOS](https://apps.apple.com/app/claude/id6473753684) or **Android**
- Tap the **Code** tab on the left

> Beginner tip: This is the lowest-friction way to keep moving when you're away from your desk. Try it on your commute.

---

## 2. Teleport Sessions Between Mobile, Web, Desktop & Terminal

**What it is:** Your Claude Code session is **portable**. Start it anywhere, continue it anywhere else.

**Why you'd use it:** Begin debugging on your laptop, walk away, finish on your phone.

**How to do it:**
- Move a cloud session to your local machine:
  ```bash
  claude --teleport
  ```
  …or run `/teleport` from inside a session.
- Control a **local** session from your phone or browser:
  ```
  /remote-control
  ```
- For convenience, enable it permanently in `/config` → **"Enable Remote Control for all sessions"**.

---

## 3. Automate Claude with `/loop` and `/schedule`

**What it is:** Two of the most powerful primitives in Claude Code. They let Claude run **on a recurring schedule**, autonomously, for up to a week at a time.

**Why you'd use it:** Turn repetitive engineering chores into self-running workflows.

**How to do it:**

```bash
/loop 5m  /babysit              # auto-address review, rebase, ship PRs every 5 min
/loop 30m /slack-feedback       # open PRs from Slack feedback every 30 min
/loop     /post-merge-sweeper   # sweep up missed code-review comments
/loop 1h  /pr-pruner            # close stale, no-longer-needed PRs every hour
```

> Pro tip: Start by writing a **skill** (a reusable prompt/workflow), then wrap it in a `/loop`. Skills + loops compose beautifully.

---

## 4. Use Hooks to Deterministically Run Logic

**What it is:** Hooks are scripts that fire at fixed points in the agent lifecycle — guaranteed, not "the model will probably remember to."

**Why you'd use it:** Anything that **must** happen every time.

**Common hooks:**

| Hook | What it does |
|------|--------------|
| `SessionStart` | Dynamically load context every time Claude starts |
| `PreToolUse` | Log every bash command the model runs |
| `PermissionRequest` | Route permission prompts to WhatsApp / Slack for approval |
| `Stop` | Poke Claude to keep going whenever it stops |

**Docs:** <https://code.claude.com/docs/en/hooks>

---

## 5. Cowork Dispatch — Remote Control for Claude Desktop

**What it is:** A secure way to drive Claude Desktop remotely — with access to your **MCPs, browser, and computer** (with permission).

**Why you'd use it:** Catch up on Slack and email, manage files, or get things done on your laptop while you're away from it.

> "When I'm not coding, I'm dispatching." — every Cowork user, eventually.

---

## 6. Use the Chrome Extension for Frontend Work

**What it is:** A Chrome/Edge extension that gives Claude direct visibility into the browser.

**Why you'd use it:** This is **the** key principle for getting great frontend output:

> Give Claude a way to **verify** its output, and it will iterate until it's great.

If you ask any engineer to build a website but forbid them from opening a browser, the result will be rough. Same with Claude. Give it eyes.

**How to do it:** Install the Chrome extension for Chrome/Edge. (More reliable than equivalent MCPs.)

---

## 7. Let Claude Start & Test Web Servers via Desktop App

**What it is:** The Claude Desktop app bundles a way for Claude to **automatically run your web server** and test it in a built-in browser.

**Why you'd use it:** Same idea as #6 — close the feedback loop. Claude writes code, runs the server, tests in browser, iterates.

**Alternatives:**
- CLI / VS Code → use the Chrome extension
- Anywhere else → just use the Desktop app

---

## 8. Fork Your Session

**What it is:** Branch off an existing session to explore a different path, without losing your current state.

**Why you'd use it:** "I want to try a totally different approach without abandoning what I have."

**Two ways:**

```bash
# From inside a session
/branch

# From the CLI
claude --resume <session-id> --fork-session
```

---

## 9. Use `/btw` for Side Queries

**What it is:** Ask a quick side question **while the main agent keeps working**.

**Why you'd use it:** "Btw, what does this regex match?" without derailing your main task.

**How to do it:**
```
/btw <your question>
```

> Great for staying in flow while the agent handles the heavy lifting in the background.

---

## 10. Use Git Worktrees for Massive Parallelism

**What it is:** Run **dozens of Claudes** in parallel on the same repo — each in its own isolated working tree.

**Why you'd use it:** True parallel work. No branch-switching tax. No "wait, did I stash my changes?"

**How to do it:**

```bash
# Start a session in a fresh worktree
claude -w
```

Or click the **"worktree"** checkbox in the Claude Desktop app.

**Non-git VCS users:** Use the `WorktreeCreate` hook to plug in your own worktree logic.

**More:** <https://x.com/bcherny/status/2025007393290272904>

---

## 11. Use `/batch` to Fan Out Massive Changesets

**What it is:** `/batch` interviews you about a task, then fans the work out to **as many worktree agents as it takes** — dozens, hundreds, even thousands.

**Why you'd use it:**
- Large code migrations
- Repetitive refactors across many files / repos
- Anything embarrassingly parallel

**More:** <https://x.com/bcherny/status/2027534984534544489>

---

## 12. Use `--bare` to Speed Up SDK Startup by Up to 10×

**What it is:** A flag that **skips** the default search for local `CLAUDE.md`, settings, and MCPs.

**Why you'd use it:** For non-interactive runs (`claude -p`, the TypeScript SDK, the Python SDK) you usually want to specify exactly what loads — not auto-discover. Skipping discovery makes startup up to **10× faster**.

**How to do it:**

```bash
claude -p --bare \
  --system-prompt "..."  \
  --mcp-config "..."     \
  --settings "..."
```

> Heads-up: A future SDK version will flip the default to `--bare`. For now, opt in.

---

## 13. Use `--add-dir` to Give Claude Access to More Folders

**What it is:** Add extra directories to Claude's working scope without restarting in a different folder.

**Why you'd use it:** Working across multiple repositories — start Claude in repo A, give it visibility (and permissions) into repo B.

**How to do it:**

```bash
claude --add-dir ../other-repo
# or, mid-session:
/add-dir ../other-repo
```

**Make it permanent for your team:** Add `additionalDirectories` to `settings.json`.

---

## 14. Use `--agent` to Give Claude a Custom System Prompt & Tools

**What it is:** Custom **agents** — a powerful, often-overlooked primitive for shipping specialized Claudes.

**Why you'd use it:** Define a focused agent (its own system prompt, allowed tools, model choice) and call it on demand.

**How to do it:**

1. Create a new agent in `.claude/agents/`
2. Run it:
   ```bash
   claude --agent=<your-agent-name>
   ```

**Docs:** <https://code.claude.com/docs/en/sub-agents>

---

## Quick Reference Cheat Sheet

```bash
# Sessions
claude --teleport                       # move cloud session local
/teleport                               # same, from inside a session
/remote-control                         # control local session remotely
/branch                                 # fork current session
claude --resume <id> --fork-session     # fork from CLI

# Automation
/loop <interval> <command>              # recurring task
/schedule                               # one-time scheduled run

# Productivity
/btw <question>                         # side query, no derail
claude -w                               # start in a new worktree
/batch                                  # fan out massive changeset

# Multi-repo / SDK
claude --add-dir <path>                 # give access to another folder
claude -p --bare ...                    # fast non-interactive startup
claude --agent=<name>                   # run a custom agent
```

---

## Where to Go Next

- **Hooks docs** — <https://code.claude.com/docs/en/hooks>
- **Sub-agents docs** — <https://code.claude.com/docs/en/sub-agents>
- **Claude Code home** — <https://claude.com/claude-code>

---

> If you only remember one thing from this guide:
> **Give Claude a way to verify its output** — a browser, a test runner, a server it can curl — and it will iterate until the result is great.
