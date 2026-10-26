# Beyond Autocomplete

Welcome to CSUS's first workshop! Today is all about **Claude Code**, an AI coding agent and developer workflow tool. We're going to be using the Claude Code desktop app for this workshop.

### What we cover today

Each module adds one layer that makes Claude better at working on a real codebase:

| Topic             | In one line                                                     |
| ----------------- | --------------------------------------------------------------- |
| Instruction files | Rules about the repo that Claude reads in every session         |
| Context           | Pointing Claude at exactly the right code and nothing else      |
| Skills            | Step-by-step procedures Claude loads when a task needs them     |
| Agents            | Specialists with their own prompt, tools, and context           |
| Hooks             | Scripts that always run at set moments, whatever the AI decides |
| MCP               | Tools and data outside the repo, like a Google Sheet            |

**Mental Model**:

- **Instructions** are what the AI should know.
- **Skills** are how to do a task.
- **Agents** are who does it.
- **Hooks** are what always happens, no matter what the AI decides.
- **MCP** is what it can reach outside the repo.

### The codebase: csus-event-hub

This repository is JUST the attendee handout. Please do not clone this repository, as it may affect some of the modules during the workshop.

We're going to be work on [perochoa/csus-event-hub](https://github.com/perochoa/csus-event-hub). It's a small FastAPI + SQLite API for club events, members, and RSVPs. It has a bunch of bugs in it on purpose, so don't worry if something looks off.

### Requirements

- **Python 3.11 or newer.** Check with `python3 --version` (Windows: `python --version`).
- **Git.** Check with `git --version`.
- An IDE of choice.

## Before We Start

Clone the main codebase into any directory of your choosing: [perochoa/csus-event-hub](https://github.com/perochoa/csus-event-hub) and follow the setup instructions from there.

```bash
cd <work-directory>
git clone https://github.com/perochoa/csus-event-hub.git
cd csus-event-hub
```

Use any IDE you like to view and and make changes to the codebase whenever needed.

Once you've setup the codebase, you can get started on module 00-setup.md
