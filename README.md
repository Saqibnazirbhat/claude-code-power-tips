# Claude Code — 14 Power Tips & Workflows

A practical, hands-on guide to Claude Code's most useful features. Each tip walks you through **what it is**, **when you'd reach for it**, and **exactly how to use it** — with example scenarios so you can copy-paste your way into a working setup.

> **New to Claude Code?** Start with tips 1, 2, 5, 6, 7, and 9 — they unlock 80% of daily value.
> **Already shipping with it?** Jump to 3, 4, 10, 11, and 14 to scale your throughput.

---

## Table of Contents

| # | Tip | Best for | One-line summary |
|---|-----|----------|------------------|
| 1 | [Mobile App](#1-claude-code-has-a-mobile-app) | Beginner | Code from your phone |
| 2 | [Teleport Sessions](#2-teleport-sessions-between-mobile-web-desktop--terminal) | Beginner | Move a session between devices |
| 3 | [`/loop` and `/schedule`](#3-automate-claude-with-loop-and-schedule) | Intermediate | Run Claude on a recurring schedule |
| 4 | [Hooks](#4-use-hooks-to-deterministically-run-logic) | Intermediate | Run code at fixed lifecycle points |
| 5 | [Cowork Dispatch](#5-cowork-dispatch--remote-control-for-claude-desktop) | Beginner | Drive Claude Desktop from anywhere |
| 6 | [Chrome Extension](#6-use-the-chrome-extension-for-frontend-work) | Beginner | Give Claude a browser for frontend |
| 7 | [Desktop App Web Servers](#7-let-claude-start--test-web-servers-via-desktop-app) | Beginner | Auto-run and test your dev server |
| 8 | [Fork Your Session](#8-fork-your-session) | Intermediate | Branch off without losing state |
| 9 | [`/btw` Side Queries](#9-use-btw-for-side-queries) | Beginner | Ask side questions, no derail |
| 10 | [Git Worktrees](#10-use-git-worktrees-for-massive-parallelism) | Pro | Run dozens of Claudes in parallel |
| 11 | [`/batch`](#11-use-batch-to-fan-out-massive-changesets) | Pro | Fan out huge changesets |
| 12 | [`--bare`](#12-use---bare-to-speed-up-sdk-startup-by-up-to-10x) | Pro | 10× faster SDK startup |
| 13 | [`--add-dir`](#13-use---add-dir-to-give-claude-access-to-more-folders) | Intermediate | Multi-repo workflows |
| 14 | [Custom Agents](#14-use---agent-to-give-claude-a-custom-system-prompt--tools) | Pro | Specialized agents on demand |

---

## 1. Claude Code Has a Mobile App

**What it is**
A full Claude Code experience on iOS and Android. Not a watered-down "chat" app — actual coding sessions on your phone.

**When to use it**
- Reviewing or merging a PR while away from your laptop
- Triggering a long-running task (refactor, migration, test run) from anywhere
- Replying to a Slack ping with "I'll look at it now" and actually meaning it

**How to use it — step by step**
1. Download the **Claude** app:
   - **iOS:** App Store → search "Claude" by Anthropic
   - **Android:** Play Store → search "Claude"
2. Sign in with the same account you use for Claude Code on desktop.
3. Tap the **Code** tab in the left sidebar.
4. Either:
   - Start a new session (it'll run in the cloud), or
   - Continue an existing cloud session you started elsewhere (see Tip #2)

**Example workflow**
> You push a PR before lunch. Over coffee, your CI fails. From your phone:
> open the Code tab → resume the PR's session → tell Claude "fix the failing test" → review the diff on your phone → approve → done before the latte's cold.

---

## 2. Teleport Sessions Between Mobile, Web, Desktop & Terminal

**What it is**
Sessions are **portable**. You can pick one up on a different device — or remote-control your local laptop session from your phone.

**When to use it**
- Started on the train, want to finish on your laptop
- Started on your laptop, need to step away — keep going from your phone
- Pair-debugging where one person has the laptop and the other only has a browser

**How to use it — step by step**

**A) Move a cloud session to your local machine:**
```bash
claude --teleport
```
…or, from inside a running cloud session:
```
/teleport
```
The session now runs against your local working directory, with full access to your files and shell.

**B) Control a local session from your phone or browser:**
```
/remote-control
```
Run that from your local terminal session, then open the Claude mobile/web app to drive it.

**Make it permanent**
Open `/config` and tick **"Enable Remote Control for all sessions"** — every local session you start will be reachable from your phone automatically.

> **Gotcha:** When you teleport a cloud session local, the workspace is now your local directory. Make sure you're in the right folder before `claude --teleport`.

---

## 3. Automate Claude with `/loop` and `/schedule`

**What it is**
- `/loop <interval> <command>` — run something every N minutes/hours
- `/schedule` — run something once at a future time

Loops can run for up to **a week** at a time. They keep running even when you close the terminal (when configured for cloud).

**When to use it**
Anything repetitive: chasing reviewers, rebasing, sweeping comments, pruning stale PRs, generating reports.

**How to use it — examples that work today**

```bash
# Every 5 minutes: address review feedback, rebase, push PRs to merge
/loop 5m /babysit

# Every 30 minutes: convert Slack feedback into PRs
/loop 30m /slack-feedback

# Default cadence: clean up missed code-review comments
/loop /post-merge-sweeper

# Every hour: close stale, no-longer-needed PRs
/loop 1h /pr-pruner
```

**Building your own loop — the pattern**
1. **Write a skill** — a reusable named workflow (e.g. `/babysit`) that does *one* clear thing.
2. **Test it manually** — run `/babysit` once and make sure it does what you want.
3. **Wrap it in a loop** — `/loop 5m /babysit` to run it on a schedule.
4. **Watch the first few iterations** — make sure it stops, doesn't loop infinitely on a stuck PR, and respects rate limits.

> **Pro move:** Combine skills + loops + custom agents (Tip #14). The skill is the *what*, the loop is the *when*, the agent is the *how*.

---

## 4. Use Hooks to Deterministically Run Logic

**What it is**
Hooks are scripts/commands that fire at fixed points in the agent lifecycle. Unlike asking the model "remember to log this every time" (which it might forget), hooks **always** run.

**When to use it**
- Things that **must** happen every session (loading context, audit logging)
- Things that need a human in the loop (permission approval routing)
- Things that need to happen even if the model "forgets"

**Common hooks**

| Hook | Fires when… | Example use |
|------|-------------|-------------|
| `SessionStart` | A session begins | Load latest API spec, project context, or open Linear tickets into the prompt |
| `PreToolUse` | Before any tool call | Log every bash command for audit; block dangerous commands |
| `PermissionRequest` | Claude asks for permission | Forward the request to WhatsApp/Slack/email so you can approve from anywhere |
| `Stop` | The agent stops | Auto-resume with "keep going until X is true" so it doesn't quit early |

**How to use it — quick start**
1. Open your `settings.json` (project-level: `.claude/settings.json`).
2. Add a `hooks` block. Example:
   ```json
   {
     "hooks": {
       "PreToolUse": "echo \"$(date) :: $TOOL_NAME :: $TOOL_INPUT\" >> ~/.claude/audit.log"
     }
   }
   ```
3. Restart your session — the hook is now live.
4. Verify by tailing `~/.claude/audit.log`.

**Full reference:** <https://code.claude.com/docs/en/hooks>

> **Beginner-friendly first hook:** `Stop` → `claude "keep going until tests pass"`. Solves the #1 frustration of "the agent gave up too early."

---

## 5. Cowork Dispatch — Remote Control for Claude Desktop

**What it is**
A secure remote-control layer for the Claude Desktop app. Once connected, Claude on your laptop can use your **MCPs**, **browser**, and **computer** — and you can drive all of it from elsewhere, with permission prompts.

**When to use it**
- Catching up on Slack/email when you're not at your desk
- Reorganizing files, downloading reports, doing ops work remotely
- Letting Claude execute browser tasks (with you approving each one) from your phone

**How to use it — step by step**
1. Make sure **Claude Desktop** is installed and running on your laptop.
2. Set up Dispatch (sign in with your Claude account on the device you're driving from).
3. Pair the two — Dispatch will surface your laptop's Claude as a controllable target.
4. From your phone/web, send instructions; permission prompts surface back to you for approval.

**Mental model**
> Dispatch turns your laptop into a *Claude server*. You're the remote.

> "When I'm not coding, I'm dispatching." — anyone who's used it for a week.

---

## 6. Use the Chrome Extension for Frontend Work

**What it is**
A Chrome / Edge extension that lets Claude **see** what's in your browser — DOM, console errors, screenshots, network activity.

**Why it matters (the most important principle in this whole guide)**
> Give Claude a way to **verify** its output, and it will iterate until it's great.

If you ask any engineer to build a website but forbid them from opening a browser, the result will be rough. Same with Claude. **Give it eyes.**

**When to use it**
Every time you touch frontend code. UI bugs, layout issues, "why isn't this button working", visual regressions.

**How to use it — step by step**
1. Install the **Claude Chrome extension** (works on Chrome and Edge).
2. Pin it to your toolbar.
3. Start a Claude Code session in the project root.
4. Open your dev site in Chrome.
5. Click the extension to **connect** the tab to your Claude session.
6. Now ask: *"The header overlaps the hero on mobile — fix it."* Claude will inspect the live page, edit CSS, refresh, verify, and iterate.

**Why pick this over a generic browser MCP**
It's purpose-built for Claude Code — fewer surprises, more reliable connection, less setup.

---

## 7. Let Claude Start & Test Web Servers via Desktop App

**What it is**
The **Claude Desktop** app bundles the ability for Claude to spin up your dev server *and* drive a built-in browser to test it — no extension needed.

**When to use it**
You're building a web app and want a tight loop where Claude:
1. Edits code →
2. Starts/restarts the server →
3. Opens the page in the bundled browser →
4. Sees the result →
5. Fixes what's broken →
6. Repeats until it works.

**How to use it — step by step**
1. Open **Claude Desktop**.
2. Open your project folder.
3. Tell Claude: *"Start the dev server (`npm run dev`) and verify the homepage renders without console errors."*
4. Claude launches the server in a managed terminal, opens the bundled browser, checks for errors, and reports back.
5. Iterate from there.

**Alternatives if you don't want to use Desktop**
- **CLI / VS Code:** Use the Chrome extension (Tip #6).
- **Anywhere else:** The Desktop app is the path of least resistance.

---

## 8. Fork Your Session

**What it is**
A way to **branch off** an existing session. The original keeps its state; the fork starts from the same point but goes a different direction.

**When to use it**
- "I want to try a riskier refactor without losing my current progress."
- Exploring two implementations of the same feature.
- Running an experiment without polluting the main session.

**How to use it — two ways**

**A) From inside the running session:**
```
/branch
```

**B) From the CLI:**
```bash
claude --resume <session-id> --fork-session
```

You can find the session ID with `claude --list-sessions` (or whatever your CLI surfaces — check `claude --help`).

**Example workflow**
> You're 40 minutes into a complex migration. You want to try a totally different approach but don't want to lose what you have.
> → `/branch` → describe the new approach in the fork → if it works, keep it; if not, fall back to the original session.

---

## 9. Use `/btw` for Side Queries

**What it is**
A way to ask Claude a quick side question **without** interrupting the main task it's working on.

**When to use it**
- "Quick — what does this regex match?"
- "Btw, what's the syntax for a Python f-string?"
- "Remind me, which env var controls log level?"

**How to use it**
```
/btw what does \d{4}-\d{2}-\d{2} match?
```

The main task keeps progressing; you get an answer in-line.

> **Why this matters:** Without `/btw`, side questions either derail your agent or break your flow. `/btw` keeps both threads moving.

---

## 10. Use Git Worktrees for Massive Parallelism

**What it is**
Git worktrees let you have **multiple checkouts** of the same repo on different branches simultaneously, in different folders. Claude Code has first-class support for this — you can run **dozens of Claudes in parallel**, each on its own worktree.

**When to use it**
- Working on multiple features at once
- Long-running tasks (don't block the main checkout)
- True parallel automation (combine with `/loop` and `/batch`)

**How to use it — step by step**

**A) Start a new session in a fresh worktree (CLI):**
```bash
claude -w
```
This creates a new worktree on a new branch and starts Claude inside it. Your main checkout is untouched.

**B) Desktop app:**
Tick the **"worktree"** checkbox when starting a new session.

**C) Non-git VCS (Mercurial, Sapling, etc.):**
Use the `WorktreeCreate` hook to plug in your own checkout-creation logic.

**Example workflow — running 5 Claudes in parallel**
1. Open 5 terminals.
2. In each: `claude -w`
3. Give each a different task (feature A, feature B, bug fix, refactor, doc update).
4. They all work simultaneously, no branch-switching collisions.

**More:** <https://x.com/bcherny/status/2025007393290272904>

> **Gotcha:** Each worktree is a real folder. Don't forget to clean up unused ones (`git worktree remove <path>`) or your repo directory will balloon.

---

## 11. Use `/batch` to Fan Out Massive Changesets

**What it is**
A workflow that interviews you about a task, then **fans the work out to many worktree agents** — dozens, hundreds, even thousands — to crunch through it in parallel.

**When to use it**
- Codebase-wide migrations (e.g., React class components → hooks across 500 files)
- Bulk renames / API surface changes
- Repeated-pattern fixes across many repos
- Anything embarrassingly parallel

**How to use it — step by step**
1. From a Claude Code session, run:
   ```
   /batch
   ```
2. Answer the interview prompts:
   - What's the task?
   - What's the scope (files, repos, modules)?
   - How should success be verified per unit?
3. Claude plans the batch, spawns worktree agents, and runs them in parallel.
4. Review results — each unit comes back as its own diff/PR.

**Example**
> "Migrate all `console.log` calls in our 200-file repo to our new `logger.info` helper, preserving the original message and adding a module tag."
> → `/batch` → 200 worktree agents → 200 commits → review and merge.

**More:** <https://x.com/bcherny/status/2027534984534544489>

---

## 12. Use `--bare` to Speed Up SDK Startup by Up to 10×

**What it is**
A flag that **skips** Claude's default discovery step (looking for local `CLAUDE.md` files, settings, MCPs). For non-interactive runs, you usually want to specify what loads explicitly anyway.

**When to use it**
- `claude -p` one-shot prompts
- TypeScript / Python SDK calls
- CI pipelines, GitHub Actions, scripted automation

**When NOT to use it**
- Interactive sessions where you *want* the project's `CLAUDE.md` and settings auto-loaded.

**How to use it — step by step**

**Before (slow startup):**
```bash
claude -p "summarize the diff" < diff.patch
```

**After (`--bare`, much faster):**
```bash
claude -p --bare \
  --system-prompt "You are a concise PR reviewer." \
  --mcp-config ./mcp.json \
  --settings ./review-settings.json \
  "summarize the diff" < diff.patch
```

**Same in the SDK** — pass `bare: true` (or the equivalent option) in the SDK config object, then explicitly provide `systemPrompt`, `mcpConfig`, and `settings`.

> **Heads-up:** A future SDK version will flip the default to `--bare`. Opt in now and your code stays forward-compatible.

---

## 13. Use `--add-dir` to Give Claude Access to More Folders

**What it is**
A way to extend Claude's working scope to additional directories — so it can **read, edit, and run commands** in repos beyond the one you started in.

**When to use it**
- A frontend repo and a backend repo that need coordinated changes
- A monorepo where you start in `apps/web` but need to touch `packages/shared`
- Pulling reference code from a sibling repo

**How to use it — step by step**

**A) When starting a session:**
```bash
claude --add-dir ../api-server --add-dir ../shared-types
```

**B) Mid-session:**
```
/add-dir ../api-server
```

**C) Permanent (team-wide):**
Add to your project's `settings.json`:
```json
{
  "additionalDirectories": ["../api-server", "../shared-types"]
}
```
Now everyone on the team gets the same multi-repo scope automatically.

**Example workflow**
> You start Claude in `web-app/`. The bug is actually in `api-server/`'s response shape.
> → `/add-dir ../api-server` → Claude reads both, fixes the API, updates the web client to match, runs both test suites.

---

## 14. Use `--agent` to Give Claude a Custom System Prompt & Tools

**What it is**
A **custom agent** — your own pre-configured Claude with a specific system prompt, tool allowlist, model choice, and personality. Save it once, summon it forever.

**When to use it**
- A "PR reviewer" agent that only comments, never edits
- A "test writer" agent with a strict style guide baked in
- A "docs maintainer" agent restricted to `/docs`
- Anything you do repeatedly with the same setup

**How to use it — step by step**

1. Create the agent file:
   ```
   .claude/agents/pr-reviewer.md
   ```
2. Define it (frontmatter + system prompt):
   ```markdown
   ---
   name: pr-reviewer
   description: Reviews diffs for clarity, correctness, and test coverage. Read-only.
   model: Sonnet
   tools: [Read, Grep, Glob]
   ---

   You are a senior engineer doing a code review. Comment on:
   1. Bugs or correctness issues
   2. Missing test coverage
   3. Naming and clarity

   Do NOT modify files. Output a numbered list of findings.
   ```
3. Run it:
   ```bash
   claude --agent=pr-reviewer
   ```
4. Iterate — tweak the prompt, add/remove tools, until the agent behaves the way you want.

**Pro composition**
Combine with `/loop` (Tip #3) for autonomous workflows:
```bash
/loop 30m /run-agent pr-reviewer
```

**Docs:** <https://code.claude.com/docs/en/sub-agents>

---

## Quick Reference Cheat Sheet

```bash
# ─── Sessions ───────────────────────────────────────
claude --teleport                       # move cloud session local
/teleport                               # same, from inside a session
/remote-control                         # control local session remotely
/branch                                 # fork current session
claude --resume <id> --fork-session     # fork from CLI

# ─── Automation ─────────────────────────────────────
/loop <interval> <command>              # recurring task (5m, 30m, 1h…)
/schedule                               # one-time scheduled run

# ─── Productivity ───────────────────────────────────
/btw <question>                         # side query, no derail
claude -w                               # start in a new worktree
/batch                                  # fan out massive changeset

# ─── Multi-repo / SDK ───────────────────────────────
claude --add-dir <path>                 # give access to another folder
/add-dir <path>                         # same, mid-session
claude -p --bare ...                    # fast non-interactive startup
claude --agent=<name>                   # run a custom agent
```

---

## Suggested Learning Path

**Week 1 — Get comfortable**
1. Install the **mobile app** (Tip #1)
2. Try `/teleport` and `/remote-control` (Tip #2)
3. Install the **Chrome extension** (Tip #6)
4. Use `/btw` for side questions (Tip #9)

**Week 2 — Add automation**
5. Write your first **skill**, then wrap it in `/loop` (Tip #3)
6. Add a `Stop` hook so Claude doesn't quit early (Tip #4)
7. Try `--add-dir` for multi-repo work (Tip #13)

**Week 3 — Scale up**
8. Start a worktree session with `claude -w` (Tip #10)
9. Run a small `/batch` job (Tip #11)
10. Build a **custom agent** for a recurring task (Tip #14)

---

## Where to Go Next

- **Hooks docs** — <https://code.claude.com/docs/en/hooks>
- **Sub-agents docs** — <https://code.claude.com/docs/en/sub-agents>
- **Claude Code home** — <https://claude.com/claude-code>

---

> **The one principle that ties it all together:**
> Give Claude a way to **verify** its output — a browser, a test runner, a server it can curl, a hook that checks the result — and it will iterate until the work is genuinely good. Every tip above is a different shape of that same idea.
