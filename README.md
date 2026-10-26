# Beyond Autocomplete: Attendee Handout

Welcome to CSUS's first workshop! Today is all about **Claude Code**. The pitch: stop treating AI like a fancy autocomplete. We teach it our codebase, give it guardrails, and turn it into something closer to a teammate.

Keep this handout folder **outside** the repo you clone, because Claude reads anything inside the repo.

## Intro (0:00)

We go through this part together before any of the modules.

### What Claude Code is

Claude Code is an AI coding agent. Autocomplete suggests the next line. Claude Code takes a task in plain English, reads the code it needs, makes a plan, edits files across the repo, and runs commands like the tests to check its own work. It asks before it does anything risky, and you review every change before you keep it. Today we use it in the **Code** tab of the Claude desktop app.

### What we cover today

| Topic | In one line |
| --- | --- |
| Instruction files | Rules about the repo that Claude reads in every session |
| Context | Pointing Claude at exactly the right code and nothing else |
| Skills | Step-by-step procedures Claude loads when a task needs them |
| Agents | Specialists with their own prompt, tools, and context |
| Hooks | Scripts that always run at set moments, whatever the AI decides |
| MCP | Tools and data outside the repo, like a Google Sheet |

**The one idea that clicked for me:** the AI is only as good as the context and guardrails you give it. Context is probably the most important of the lot. Each module adds one layer, so pay attention to how the output gets better as the layers stack up.

### The codebase: csus-event-hub

The code we work on lives in [perochoa/csus-event-hub](https://github.com/perochoa/csus-event-hub): a small FastAPI + SQLite API for club events, members, and RSVPs, basically the kind of thing a club exec would hack together for a semester. Heads up: it's messy on purpose. Today we use Claude Code to explore it, fix it, test it, review it, and harden it, so don't panic when something looks off.

### Requirements

- **Python 3.11 or newer.** Check with `python3 --version` (Windows: `python --version`).
- **Git.** Check with `git --version`. If you've never committed on this laptop, tell Git who you are, since we commit in the modules:

  ```bash
  git config --global user.name "Your Name"
  git config --global user.email "you@example.com"
  ```

### Repository setup

Clone the repo somewhere outside this handout folder:

```bash
git clone https://github.com/perochoa/csus-event-hub.git
cd csus-event-hub
```

Then set it up:

| Step | Mac or Linux | Windows |
| --- | --- | --- |
| Create venv | `python3 -m venv .venv` | `python -m venv .venv` |
| Install | `.venv/bin/python -m pip install -e ".[dev]"` | `.venv/Scripts/python -m pip install -e ".[dev]"` |
| Create local env file | `cp .env.example .env` | `Copy-Item .env.example .env` |
| Run tests | `.venv/bin/python -m pytest` | `.venv/Scripts/python -m pytest` |

The tests should pass. We never activate the venv: every command calls its Python by path ([00](00-setup.md) explains why).

Next up: install the Claude desktop app, sign in, and open the repo in it with [00: Install and setup](00-setup.md). We do everything in the app's **Code** tab from then on, including the terminal commands.

**Creating the files in the modules:** use any editor you like, or let Claude do it: paste the code block into a prompt as "Create `<path>` with exactly this content:". Either way, read the file afterwards.

## Modules

| Clock | Module | What we practise |
| --- | --- | --- |
| 0:00 | [Intro](#intro-000) | The plan for today, the codebase, and its setup |
| 0:10 | [00 Install and setup](00-setup.md) | Getting Claude ready |
| 0:20 | [M1 First agent task](01-first-task.md) | Codebase onboarding |
| 0:30 | [M2 Project instructions](02-instructions.md) | Feature development |
| 0:45 | [M3 Giving useful context](03-context.md) | Debugging |
| 0:55 | [M4 Skills](04-skills.md) | Testing |
| 1:10 | Break 🍕 | |
| 1:20 | [M5 Custom agents](05-agents.md) | Code review |
| 1:35 | [M6 Hooks](06-hooks.md) | Guardrails |
| 1:50 | [M7 MCP servers](07-mcp.md) | External tooling |
| 2:00 | [Responsible use](#responsible-use) | |

**Something broken?** Put your hand up and one of the floaters will come help.

## My one-page cheat sheet

| Concept | What it does | Where it lives |
| --- | --- | --- |
| Project instructions | Always-loaded rules about the repo | `CLAUDE.md` at the repo root |
| Scoped instructions | Rules for one part of the repo | `CLAUDE.md` inside a subfolder |
| Skills | A packaged procedure, loaded on demand | `.claude/skills/<name>/SKILL.md` |
| Subagents | A specialist with its own prompt, tools, and context | `.claude/agents/<name>.md` |
| Hooks | Deterministic scripts at lifecycle events | `hooks` in `.claude/settings.json` |
| MCP servers | External tools and data | Connectors in the app, or `.mcp.json` |
| Context control | Point at exactly the right code | — |
| Safety controls | Decide what runs, undo mistakes | Permission settings |

**How I keep it straight in my head:**

- **Instructions** are what the AI should know.
- **Skills** are how to do a task.
- **Agents** are who does it.
- **Hooks** are what always happens, no matter what the AI decides.
- **MCP** is what it can reach outside the repo.

## Safety habits I stick to all day

1. **Commit before every agent task.** `git add -A`, then `git commit -m "checkpoint"` in the app's terminal gives me a clean spot to come back to if things go sideways.
2. **Read every diff before accepting it.** My name is on every line I commit, not the AI's.
3. **Undo is cheap.** Hit the stop button the moment the agent heads the wrong way, and `git reset --hard`, then `git clean -fd`, takes the code back to my last commit.

## Responsible use

This part matters more than any of the cool tricks:

- **Academic integrity comes first.** AI policies are different for every course and sometimes every assignment. Using these tools where the syllabus says no is academic misconduct, so check every course outline. What we learn today is for personal projects, hackathons, internships, and courses that allow it.
- **You own every line you commit.** If you couldn't explain a diff in an interview or a code review, don't merge it.
- **Learning still matters.** I use agents to go faster on stuff I already understand, and to explain stuff I don't. I don't use them to skip fundamentals I'll get tested on, because the exam won't have Claude in it.
- **Protect secrets and data.** Never paste API keys or private data into prompts. Hooks like the `.env` guard exist because "just tell it not to" isn't enough.
- **Watch for prompt injection through MCP.** A sketchy shared doc, web page, issue, or README can hide instructions aimed at your agent. Only connect servers you trust, and keep command approval on for anything destructive.
- **Verify, don't trust.** Agents will confidently hand you code that looks right and is wrong. Tests, review, and hooks are your safety net.
