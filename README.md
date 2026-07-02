# Claude Code : 28 Power Tips & Workflows

A practical, hands-on guide to Claude Code's most useful features. Each tip walks you through **what it is**, **when you'd reach for it**, and **exactly how to use it** — with example scenarios so you can copy-paste your way into a working setup.

The tips are ordered to be read top to bottom: **Foundations** (the handful of habits that shape every single session) come first, then **Daily Drivers**, then **Automation & Scale** for when you're ready to multiply your throughput.

> **New to Claude Code?** Just start at Tip #1 and work down through the Foundations (1–8). They unlock 80% of the daily value.
> **Already shipping with it?** Jump to the Automation & Scale section (17–28).

---

## Table of Contents

### Foundations — use these every session
| # | Tip | One-line summary |
|---|-----|------------------|
| 1 | [Plan Mode](#1-plan-before-you-build-with-plan-mode) | Agree on a plan before any edits |
| 2 | [CLAUDE.md Memory](#2-give-claude-lasting-memory-with-claudemd) | Teach Claude your project once |
| 3 | [Let Claude Write Its Own Rules & Prompts](#3-let-claude-write-its-own-rules--prompts) | Stop hand-crafting prompts — let Claude do it |
| 4 | [Extended Thinking](#4-make-claude-think-harder) | More reasoning for hard problems |
| 5 | [Verify-Driven Development](#5-give-claude-a-way-to-verify-its-work) | Give Claude a feedback loop (the #1 lever) |
| 6 | [Context Management](#6-manage-context-with-clear-compact--rewind) | Keep sessions sharp with `/clear`, `/compact`, `/rewind` |
| 7 | [`@` File Mentions](#7-point-claude-at-files-with-) | Pull exact files into context |
| 8 | [Images & Screenshots](#8-paste-images--screenshots) | Show Claude a mockup or a bug |

### Daily Drivers
| # | Tip | One-line summary |
|---|-----|------------------|
| 9 | [Custom Slash Commands](#9-turn-repeated-prompts-into-slash-commands) | Reusable prompts for the whole team |
| 10 | [Chrome Extension](#10-use-the-chrome-extension-for-frontend-work) | Give Claude a browser for frontend |
| 11 | [Desktop App Web Servers](#11-let-claude-start--test-web-servers-via-desktop-app) | Auto-run and test your dev server |
| 12 | [`/btw` Side Queries](#12-use-btw-for-side-queries) | Ask side questions, no derail |
| 13 | [Fork Your Session](#13-fork-your-session) | Branch off without losing state |
| 14 | [Mobile App](#14-claude-code-has-a-mobile-app) | Code from your phone |
| 15 | [Teleport Sessions](#15-teleport-sessions-between-mobile-web-desktop--terminal) | Move a session between devices |
| 16 | [Cowork Dispatch](#16-cowork-dispatch--remote-control-for-claude-desktop) | Drive Claude Desktop from anywhere |

### Automation & Scale
| # | Tip | One-line summary |
|---|-----|------------------|
| 17 | [Best Model + Parallel Claudes](#17-use-the-most-capable-model--run-several-in-parallel) | The creator's core workflow |
| 18 | [`/loop` and `/schedule`](#18-automate-claude-with-loop-and-schedule) | Run Claude on a recurring schedule |
| 19 | [Hooks](#19-use-hooks-to-deterministically-run-logic) | Run code at fixed lifecycle points |
| 20 | [`--add-dir`](#20-use---add-dir-to-give-claude-access-to-more-folders) | Multi-repo workflows |
| 21 | [MCP Servers](#21-connect-external-tools-with-mcp) | Wire in databases, APIs, and tools |
| 22 | [Subagents](#22-delegate-work-to-subagents) | Fan out research and parallel work |
| 23 | [Git Worktrees](#23-use-git-worktrees-for-massive-parallelism) | Run dozens of Claudes in parallel |
| 24 | [`/batch`](#24-use-batch-to-fan-out-massive-changesets) | Fan out huge changesets |
| 25 | [Custom Agents](#25-use---agent-to-give-claude-a-custom-system-prompt--tools) | Specialized agents on demand |
| 26 | [`--bare`](#26-use---bare-to-speed-up-sdk-startup-by-up-to-10x) | 10× faster SDK startup |
| 27 | [Dynamic Workflows](#27-dynamic-workflows--let-claude-build-its-own-harness) | Claude writes its own multi-agent harness on the fly |
| 28 | [Write the Loop, Not Just the Prompt](#28-write-the-loop-not-just-the-prompt) | Loop engineering — author the control loop, let Claude be the body |

---

# Foundations

## 1. Plan Before You Build with Plan Mode

**What it is**
A mode where Claude researches and proposes a plan **without touching any files**. You review and refine the plan together, then approve it before a single edit happens. This is the single highest-leverage habit in Claude Code — it's how the team that builds it works.

**When to use it**
- Any non-trivial task (a feature, a refactor, a bug you don't fully understand yet)
- When you want to catch a wrong approach *before* code is written
- Whenever you'd otherwise say "wait, that's not what I meant"

**How to use it — step by step**
1. Press **`Shift+Tab`** to cycle modes until you see **plan mode** (the modes cycle: normal → auto-accept → plan).
2. Describe what you want. Claude explores the code and returns a plan — no edits yet.
3. Push back: *"don't touch the auth layer,"* *"do it in two phases,"* *"use the existing helper instead."*
4. When the plan looks right, approve it. Claude switches to executing — and often one-shots the whole task.

> **Pro tip:** Plan mode → approve → auto-accept edits is the workflow Boris Cherny (Claude Code's creator) uses for most PRs. Pair it with extended thinking (Tip #4): *"think hard, then give me a plan."*

---

## 2. Give Claude Lasting Memory with CLAUDE.md

**What it is**
A `CLAUDE.md` file that Claude reads automatically at the start of every session. It's where you record how your project works, your conventions, and the mistakes you don't want repeated — so you stop re-explaining the same things.

**When to use it**
- Project-specific build/test/lint commands ("always run `pnpm test`, never `npm test`")
- Conventions Claude keeps getting wrong (file layout, naming, "no default exports")
- Anything you find yourself typing into the prompt more than twice

**How to use it — step by step**
1. Generate a starter file for your repo:
   ```
   /init
   ```
2. Edit `CLAUDE.md` to be **short, specific, and opinionated** — a long file dilutes attention. A good test: *"would removing this line cause Claude to make a mistake?"* If no, cut it.
3. Add a memory on the fly without leaving your prompt — start a line with `#`:
   ```
   # always use snake_case for database columns
   ```
   Claude appends it to the right memory file.
4. Layer memory by scope: `~/.claude/CLAUDE.md` (you, everywhere), `./CLAUDE.md` (project, checked into git), `./subdir/CLAUDE.md` (folder-specific).

> **Pro tip:** Check the project file into git so the whole team benefits — and see Tip #3 for the trick of letting Claude maintain this file *for* you.

---

## 3. Let Claude Write Its Own Rules & Prompts

**What it is**
Meta-prompting: instead of hand-crafting the perfect prompt or rule, you let **Claude** write it. Claude is excellent at distilling its own mistakes into precise rules and at turning a vague ask into a sharp prompt — so your job becomes *pointing*, not *explaining*. This is one of the most-cited habits of Claude Code's creator.

**When to use it**
- Right after Claude does something wrong — capture the lesson instead of re-correcting it forever
- When you catch yourself writing a long, elaborate prompt
- When creating a new slash command (Tip #9) or custom agent (Tip #25)

**How to use it — step by step**
1. **Turn mistakes into rules.** When Claude gets something wrong:
   ```
   update CLAUDE.md so you don't make this mistake again
   ```
   Claude writes a crisp, durable rule far faster than you would.
2. **Keep your own prompts short — point, don't explain.** The context is already there. A real example the creator uses:
   ```
   Grill me on these changes. Don't open a PR until I pass your test.
   ```
3. **Ask Claude to author its own artifacts:**
   ```
   write a slash command that reviews the current diff for security issues
   improve this prompt so it's clearer and more specific, then run it
   ```

> **Pro tip:** This compounds with Tips #2 and #9 — a self-maintaining CLAUDE.md plus Claude-authored slash commands means your setup keeps getting better with almost no manual prompt engineering.

---

## 4. Make Claude Think Harder

**What it is**
Extended thinking — give Claude a larger reasoning budget for hard problems, just by using trigger words in your prompt. More thinking means better plans, trickier debugging, and fewer wrong turns.

**When to use it**
- Designing architecture or weighing trade-offs
- Debugging something subtle or intermittent
- Any problem where a fast-but-wrong answer costs you more time than a slow-but-right one

**How to use it — step by step**
1. Add an escalating trigger word to your request:
   - `think` < `think hard` < `think harder` < `ultrathink` (roughly increasing reasoning budget).
2. Example:
   ```
   ultrathink about why this race condition only happens under load, then propose a fix
   ```
3. Claude shows its reasoning, then acts on it.

> **Pro tip:** Pair with Plan Mode (Tip #1) — "think hard, then give me a plan" is a powerful combo for big tasks.

---

## 5. Give Claude a Way to Verify Its Work

**What it is**
The single biggest quality lever in this whole guide. If Claude has a way to **check its own output** — a test runner, a browser, a dev server it can hit, a linter, a script — it will iterate until the work is genuinely good. The creator's words: a good feedback loop **2–3×'s the quality** of the final result.

**When to use it**
- Honestly, every non-trivial task. This is a mindset more than a feature.
- Especially anything with a visible result: UIs, APIs, data transforms, build output.

**How to use it — step by step**
1. **Tell Claude how to check itself**, and to keep going until it's green:
   ```
   run `pnpm test` after each change and don't stop until everything passes
   ```
2. **Give it eyes and hands** for the relevant domain:
   - Frontend → the Chrome extension (Tip #10) or Desktop's bundled browser (Tip #11)
   - Backend/API → "start the server and curl the endpoint to confirm the response shape"
3. **Make verification automatic** with a `Stop` hook (Tip #19) that re-runs until a condition holds.

> **Pro tip:** If you ask an engineer to build a website but forbid them from opening a browser, the result is rough. Same with Claude — *give it the browser.* Every other tip in this guide is, at heart, a different shape of this one idea.

---

## 6. Manage Context with `/clear`, `/compact` & `/rewind`

**What it is**
Tools to keep a session focused. Context isn't infinite, and a cluttered one makes Claude slower and less accurate. These commands let you reset, condense, or step back.

**When to use it**
- **`/clear`** — starting a genuinely new task; wipe the slate so old context doesn't bleed in
- **`/compact`** — mid-long-task; keep going but shrink the history to essentials
- **`/rewind`** — you went down a bad path and want to restore code/conversation to an earlier point

**How to use it — step by step**
1. Done with one task, starting another:
   ```
   /clear
   ```
2. Long session getting heavy but you need to continue:
   ```
   /compact
   ```
   (Optionally: `/compact keep the API design decisions` to steer what's preserved.)
3. To undo and jump back to an earlier checkpoint:
   ```
   /rewind
   ```
   (You can also double-tap **Esc** to jump back and edit an earlier message.)

> **Pro tip:** Reach for `/clear` far more often than you'd think — most "Claude is acting confused" moments are really "the context is stale."

---

## 7. Point Claude at Files with `@`

**What it is**
Type `@` to fuzzy-search and drop an exact file or directory into your prompt — no need to describe where something lives or paste its contents.

**When to use it**
- "Refactor `@src/auth/session.ts`" instead of "the session file in auth"
- Giving Claude a reference implementation to mirror
- Pulling a whole directory in as context

**How to use it — step by step**
1. In your prompt, type `@` and start typing a path or filename:
   ```
   make @src/api/users.ts follow the same error handling as @src/api/orders.ts
   ```
2. Pick from the autocomplete list.
3. Claude reads those exact files into context before acting.

> **Pro tip:** `@`-mentioning a clean example file is the fastest way to get Claude to match your existing patterns.

---

## 8. Paste Images & Screenshots

**What it is**
Claude Code is multimodal — you can give it images: UI mockups to build from, screenshots of bugs, error dialogs, or diagrams.

**When to use it**
- "Build this" from a design mockup
- Showing a visual bug that's hard to describe in words
- Pasting a stack trace or terminal screenshot

**How to use it — step by step**
1. Add an image to your prompt:
   - **Paste** from clipboard (great for screenshots)
   - **Drag and drop** a file into the terminal/app
   - Or reference a path: `look at ./design/mockup.png`
2. Describe what you want: *"build a React component matching this layout."*
3. Claude works from what it sees.

> **Pro tip:** Combine with the Chrome extension (Tip #10) so Claude can screenshot its *own* result and compare it to your mockup — verification (Tip #5) for pixels.

---

# Daily Drivers

## 9. Turn Repeated Prompts into Slash Commands

**What it is**
Custom slash commands — reusable prompt templates stored as Markdown files. Anything you ask many times a day becomes a one-word command, and Claude can invoke them too.

**When to use it**
- Repeated workflows: "review this diff," "write tests for this file," "draft a PR description"
- Standardizing how a team triggers common tasks
- Multi-step prompts you don't want to retype

**How to use it — step by step**
1. Create `.claude/commands/<name>.md` (project) or `~/.claude/commands/<name>.md` (personal):
   ```markdown
   ---
   description: Review the current diff for bugs
   ---
   Review the staged diff. Flag correctness bugs and security issues.
   Don't comment on style. Argument: $ARGUMENTS
   ```
2. Run it in any session:
   ```
   /review
   ```
3. Pass input with `$ARGUMENTS` (or `$1`, `$2` for positional):
   ```
   /review focus on the error handling
   ```

> **Pro tip:** Don't write these by hand — ask Claude to draft the command for you (Tip #3). Check `.claude/commands/` into git so commands are shared and version-controlled across the team.

---

## 10. Use the Chrome Extension for Frontend Work

**What it is**
A Chrome / Edge extension that lets Claude **see** what's in your browser — DOM, console errors, screenshots, network activity.

**Why it matters**
This is verification (Tip #5) for frontend work. Give Claude a way to see the live page, and it will iterate until the UI is right.

**When to use it**
Every time you touch frontend code. UI bugs, layout issues, "why isn't this button working", visual regressions.

**How to use it — step by step**
1. Install the **Claude Chrome extension** (works on Chrome and Edge).
2. Pin it to your toolbar.
3. Start a Claude Code session in the project root.
4. Open your dev site in Chrome.
5. Click the extension to **connect** the tab to your Claude session.
6. Now ask: *"The header overlaps the hero on mobile — fix it."* Claude will inspect the live page, edit CSS, refresh, verify, and iterate.

> **Pro tip:** It's purpose-built for Claude Code — fewer surprises and less setup than a generic browser MCP.

---

## 11. Let Claude Start & Test Web Servers via Desktop App

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
- **CLI / VS Code:** Use the Chrome extension (Tip #10).
- **Anywhere else:** The Desktop app is the path of least resistance.

---

## 12. Use `/btw` for Side Queries

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

## 13. Fork Your Session

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

## 14. Claude Code Has a Mobile App

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
   - Continue an existing cloud session you started elsewhere (see Tip #15)

**Example workflow**
> You push a PR before lunch. Over coffee, your CI fails. From your phone:
> open the Code tab → resume the PR's session → tell Claude "fix the failing test" → review the diff on your phone → approve → done before the latte's cold.

---

## 15. Teleport Sessions Between Mobile, Web, Desktop & Terminal

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

## 16. Cowork Dispatch — Remote Control for Claude Desktop

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

---

# Automation & Scale

## 17. Use the Most Capable Model — and Run Several in Parallel

**What it is**
Two habits straight from Claude Code's creator. First: default to the **biggest model with thinking on**, for everything. Even though it's slower per step, you steer it less and it's better at tool use — so it's usually *faster overall* than babysitting a smaller one. Second: run **multiple Claudes at once** on independent tasks.

**When to use it**
- Model: basically always. Reach for a smaller/faster model only for trivial, high-volume calls.
- Parallel: whenever you have two or more tasks that don't depend on each other.

**How to use it — step by step**
1. **Set the model** to the most capable one:
   ```
   /model
   ```
   (or `claude --model <name>` at launch) — then turn on thinking (Tip #4).
2. **Run several in parallel.** Open multiple terminal tabs and **number them 1–5** so you can keep track; give each its own task. Use git worktrees (Tip #23) so they don't collide on the same files.
3. **Let notifications tell you who needs you.** Enable system notifications so you only check in on the Claude that's waiting for input.
4. **Add cloud sessions** on claude.ai/code on top of your local ones for even more parallelism.

> **Pro tip:** The creator runs ~5 Claudes in his terminal plus 5–10 on the web at once, all on the biggest model. The bottleneck stops being the model and becomes *how many tasks you can keep in your head.*

---

## 18. Automate Claude with `/loop` and `/schedule`

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

> **Pro move:** Combine skills + loops + custom agents (Tip #25). The skill is the *what*, the loop is the *when*, the agent is the *how*.

---

## 19. Use Hooks to Deterministically Run Logic

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

> **Beginner-friendly first hook:** A `Stop` hook that re-runs "keep going until tests pass" is the deterministic backbone of verify-driven development (Tip #5). It solves the #1 frustration of "the agent gave up too early."

---

## 20. Use `--add-dir` to Give Claude Access to More Folders

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

## 21. Connect External Tools with MCP

**What it is**
The Model Context Protocol (MCP) lets Claude talk to external systems — databases, issue trackers, browsers, design tools, your company's internal APIs — through standardized server connections.

**When to use it**
- Querying a real database instead of guessing your schema
- Pulling ticket details from Jira/Linear/GitHub directly
- Giving Claude any capability beyond reading and editing local files

**How to use it — step by step**
1. Add a server from the CLI:
   ```bash
   claude mcp add <name> -- <command to start the server>
   ```
2. Or add it to `.mcp.json` in your project to share with the team:
   ```json
   {
     "mcpServers": {
       "postgres": { "command": "npx", "args": ["-y", "@some/postgres-mcp", "$DB_URL"] }
     }
   }
   ```
3. List and check connections:
   ```bash
   claude mcp list
   ```
4. In-session, Claude can now call that server's tools (and read its resources via `@`).

> **Pro tip:** Only enable the servers you actually need — each one adds tools and context. Trim aggressively for speed.

---

## 22. Delegate Work to Subagents

**What it is**
Ask Claude to spin up **subagents** — separate agent instances that handle a chunk of work (research, a wide search, an isolated edit) and report back a summary. This keeps your main session's context clean while work happens in parallel.

**When to use it**
- Broad "search the whole codebase for X" sweeps where you only want the conclusion
- Running several independent investigations at once
- Keeping a long task's context lean by offloading noisy exploration

**How to use it — step by step**
1. Just ask, naming the shape of the work:
   ```
   use a subagent to find every place we call the legacy payment API
   ```
2. For multiple parallel tracks:
   ```
   spin up subagents to (a) audit test coverage and (b) list unused dependencies
   ```
3. Each subagent works independently and returns a summary to your main session.

> **Pro tip:** Subagents (in-session, ad hoc) and custom agents (Tip #25, pre-configured files) complement each other — use custom agents for repeatable roles, subagents for one-off fan-out.

---

## 23. Use Git Worktrees for Massive Parallelism

**What it is**
Git worktrees let you have **multiple checkouts** of the same repo on different branches simultaneously, in different folders. Claude Code has first-class support for this — you can run **dozens of Claudes in parallel**, each on its own worktree. This is the mechanism behind the parallel workflow in Tip #17.

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

## 24. Use `/batch` to Fan Out Massive Changesets

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

## 25. Use `--agent` to Give Claude a Custom System Prompt & Tools

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
Combine with `/loop` (Tip #18) for autonomous workflows:
```bash
/loop 30m /run-agent pr-reviewer
```

**Docs:** <https://code.claude.com/docs/en/sub-agents>

---

## 26. Use `--bare` to Speed Up SDK Startup by Up to 10×

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

## 27. Dynamic Workflows — Let Claude Build Its Own Harness

**What it is**
The most powerful pattern in this guide. Instead of doing a task in one context window, Claude **writes its own orchestration program** (a small JavaScript file) that spawns and coordinates **many separate Claudes** — each with its own clean context, a narrow job, and (optionally) its own model and git worktree. Claude stops being a single worker and becomes the *manager of a team* it designed for your exact task.

This is the difference between a **static** workflow (a generic coordinator you wrote ahead of time with `claude -p` or the SDK) and a **dynamic** one (a custom harness Claude writes in the moment, tailored to the job).

**Why it matters**
A single long-running context drifts into three failure modes that dynamic workflows structurally prevent:
- **Agentic laziness** — quitting early ("did 20 of 50 items, done"). Fan-out gives each item its own agent, so nothing gets skipped.
- **Self-preferential bias** — grading its own work too kindly. A *separate* verifier agent can't be biased toward output it didn't produce.
- **Goal drift** — losing the original constraints after compaction. Each subagent gets a fresh, focused goal.

**When to use it**
- Long-running, **massively parallel**, or highly structured **adversarial** tasks.
- When you need **unbiased judging** (verify claims, grade outputs against a rubric, pick a winner).
- When each unit of work benefits from a **clean context** so they don't cross-contaminate.

**When NOT to use it**
- Ordinary coding tasks. Dynamic workflows use **significantly more tokens** — most tasks don't need a panel of five reviewers. Ask: *does this really need more compute?*

**How to use it — step by step**
1. **Trigger one** by just asking, or use the keyword **`ultracode`** to force Claude to build a workflow:
   ```
   ultracode: go through all 50 items in this security review and fix each in its own worktree
   ```
2. **Name the pattern** you want (this is the real skill — see the table below):
   ```
   use a workflow to fact-check every claim in my draft: one agent finds the claims,
   a subagent verifies each against the codebase, a second agent checks the source quality
   ```
3. **Constrain it** when needed — Claude can pick the model and isolation per agent:
   ```
   use a quick workflow, budget 10k tokens, cheap model for the fan-out and Opus for the judge
   ```
4. **Pair with `/goal` and `/loop`** for repeatable jobs (triage, research, verification):
   ```
   /loop /triage-queue   →   classify each ticket, dedupe, escalate the novel ones
   ```
5. **Save & share** — press **`s`** in the workflow menu to store it in `~/.claude/workflows`, or bundle the `.js` file in a skill (reference it from `SKILL.md`) so your team can reuse it as a template.

**The reusable patterns** (compose these together)

| Pattern | What it does | Example |
|---------|--------------|---------|
| **Fan-out & synthesize** | Split into many small pieces, one agent each, then merge | Profile 50 columns / rank 80 resumes |
| **Adversarial verification** | A second agent tries to *disprove* the first against a rubric | Fact-check every claim in a report |
| **Generate & filter** | Brainstorm many ideas, then filter to the best | Name a tool; design exploration |
| **Tournament** | Agents compete; pairwise judges pick a winner | Sort 1,000 tickets by severity |
| **Classify & route** | A classifier decides the task type → routes to the right agent/model | Sonnet vs Opus by complexity |
| **Loop until done** | Keep spawning agents until a stop condition holds | Hunt a flaky test; sweep logs |

**Example workflows to copy**
```
This test fails ~1 in 50 runs. Set up a workflow to reproduce it, form theories,
and adversarially test them in worktrees. /goal don't stop until one theory works.

Here's a folder of 80 resumes — use a workflow to rank them for the backend role
and double-check the top ten. Interview me with AskUserQuestion for a rubric.

Take my business plan and run a workflow where different agents tear it apart from
an investor's, a customer's, and a competitor's perspective.
```

> **Pro tip:** This is Tips #17 (parallel Claudes), #22 (subagents), and #23 (worktrees) fused into one self-assembling harness — but the unifying idea is still Tip #5: a dynamic workflow is a *verification machine* you let Claude build. Start with a **"quick workflow"** for a single adversarial check before reaching for a full multi-agent run.

---

## 28. Write the Loop, Not Just the Prompt

**What it is**
A mindset shift that Boris Cherny (Claude Code's creator) calls **loop engineering**. There are three ways to get work done: you can **write the code** yourself, **write the prompt** and take a one-shot answer, or **write the loop** — author the control structure and let Claude be the *body* that runs inside it. Your job moves up a level: you're no longer writing the solution or even the instruction, you're engineering the feedback loop that drives an agent toward a goal. The loop from the talk looks like this:

```js
// fix every bug report
do {
  claude("read #feedback, fix top report")   // act
  verify()                                    // check the work
} while (!goal("every report has a PR"))      // stop only when the goal holds
schedule("+10m")                              // re-trigger later
```

The interesting engineering isn't the prompt string — it's the three things wrapped around it: a **goal predicate** that defines "done," a **verify** step so nothing is trusted blindly, and a **schedule** so the loop persists over time.

**When to use it**
- A backlog that should be *drained*, not touched once — bug reports, review comments, failing tests, stale tickets
- Any task where "done" is a checkable condition (`every report has a PR`, `all tests green`, `queue is empty`)
- When you'd otherwise babysit Claude through the same act → check → repeat cycle by hand

**How to use it — step by step**
1. **Write the body as a skill.** Make one small, named workflow that does a *single* iteration (Tip #9, #18):
   ```markdown
   ---
   description: Read the top unresolved bug report and open a PR that fixes it
   ---
   Find the highest-priority unresolved bug report. Fix it. Open a PR.
   Run the test suite and don't finish until it's green.
   ```
2. **Give it a verify step** so the loop can't lie about progress (Tip #5) — a test run, a curl, a linter baked into the skill or enforced by a `Stop` hook (Tip #19).
3. **Define the goal — the stop condition.** This is the `while (!goal(...))` — the loop keeps going *only* until it's true:
   ```
   /goal every open bug report has a linked PR
   ```
4. **Wrap it in a loop with a cadence** — this is the `do { … } while` plus `schedule("+10m")` (Tip #18):
   ```
   /loop 10m /fix-top-bug
   ```
5. **Watch the first few turns** — confirm it *terminates* when the goal is met and doesn't spin on a stuck item.

**Mapping the pseudocode to real commands**

| In the slide | In Claude Code | Tip |
|--------------|----------------|-----|
| `claude("…")` | a skill / slash command (the body) | #9, #18 |
| `verify()` | test run, curl, linter, or `Stop` hook | #5, #19 |
| `!goal(…)` | `/goal <condition>` (the stop condition) | #18 |
| `do { … } while` + `schedule("+10m")` | `/loop 10m /skill` | #18 |

**Example workflow**
> Your team files bugs into a `#feedback` channel and you want every one to end up as a PR, without you triaging by hand.
> → Write a `/fix-top-bug` skill (act + verify) → set `/goal every report in #feedback has a PR` → run `/loop 10m /fix-top-bug` → each cycle Claude grabs the top report, fixes it, verifies, and pushes a PR, stopping only when the queue is drained.

> **Pro tip:** This is Tip #5 (verify) and Tip #18 (`/loop`) fused into a single idea — the durable skill isn't prompt-crafting, it's designing good **stop conditions, verification checks, and cadence**. Get those three right and the model handles every iteration in between. Reach for Tip #27's dynamic workflows when a single loop body should itself fan out to a team of agents.

---

## Quick Reference Cheat Sheet

```bash
# ─── Foundations (every session) ────────────────────
Shift+Tab                               # cycle to plan mode
/init                                   # generate a CLAUDE.md
# <rule>                                # quick-add a memory
think | think hard | ultrathink         # more reasoning budget
/clear                                  # wipe context for a new task
/compact                                # condense a long session
/rewind                                 # undo to a checkpoint (or Esc Esc)
@path/to/file                           # pull an exact file into context

# ─── Daily drivers ──────────────────────────────────
/<name> [args]                          # run a custom slash command
/btw <question>                         # side query, no derail
/branch                                 # fork current session
claude --teleport                       # move cloud session local
/remote-control                         # control local session remotely

# ─── Automation & scale ─────────────────────────────
/model                                  # pick the most capable model
/loop <interval> <command>              # recurring task (5m, 30m, 1h…)
/schedule                               # one-time scheduled run
claude --add-dir <path>                 # give access to another folder
claude mcp add <name> -- <cmd>          # connect an external tool (MCP)
claude -w                               # start in a new worktree
/batch                                  # fan out massive changeset
claude --agent=<name>                   # run a custom agent
claude -p --bare ...                    # fast non-interactive startup
ultracode: <task>                       # force Claude to build a dynamic workflow
"use a quick workflow to ..."           # lightweight one-off multi-agent check
/goal <condition>                       # set a loop's stop condition
/loop 10m /skill                        # loop engineering: act → verify → repeat
```

---

## Suggested Learning Path

**Week 1 — Foundations** (shapes *every* session)
1. Start tasks in **Plan Mode** with `Shift+Tab` (Tip #1)
2. Run `/init` and tend your **CLAUDE.md** (Tip #2)
3. Let Claude **write its own rules & prompts** (Tip #3)
4. Use `think` / `ultrathink` on hard problems (Tip #4)
5. Build the **verify-first** habit — give Claude a feedback loop (Tip #5)
6. Practice `/clear` and `/compact`, and pull files in with `@` (Tips #6, #7)

**Week 2 — Daily drivers**
7. Write your first **custom slash command** (Tip #9)
8. Install the **Chrome extension** and test in a browser (Tip #10)
9. Use `/btw` for side questions (Tip #12)
10. Install the **mobile app** and try `/teleport` (Tips #14, #15)

**Week 3 — Automation**
11. Pick the **best model + run a couple of Claudes in parallel** (Tip #17)
12. Write a skill, then wrap it in `/loop` (Tip #18)
13. Add a `Stop` hook so Claude doesn't quit early (Tip #19)
14. Connect an **MCP server** (Tip #21)

**Week 4 — Scale up**
15. Start a worktree session with `claude -w` (Tip #23)
16. Run a small `/batch` job (Tip #24)
17. Build a **custom agent** and delegate to **subagents** (Tips #25, #22)
18. Try a **dynamic workflow** — start with a "quick workflow" adversarial check, then a fan-out (Tip #27)
19. Practice **loop engineering** — wrap a verify-backed skill in `/loop` with a `/goal` stop condition (Tip #28)

---

## Where to Go Next

- **Hooks docs** — <https://code.claude.com/docs/en/hooks>
- **Sub-agents docs** — <https://code.claude.com/docs/en/sub-agents>
- **Claude Code home** — <https://claude.com/claude-code>

---

> **The one principle that ties it all together:**
> Give Claude a way to **verify** its output — a browser, a test runner, a server it can curl, a hook that checks the result — and it will iterate until the work is genuinely good. Every tip above is a different shape of that same idea.
