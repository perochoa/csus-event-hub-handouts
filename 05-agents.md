# M5: Custom Agents (20 min)

**What we're doing:** building a specialist reviewer that can't "helpfully" rewrite our code.

## The idea

A custom agent (subagent) has its own system prompt, its own restricted set of tools, and its **own context window**, so it looks at your code with fresh eyes. A read-only reviewer can only point out problems; it can't go and fix them.

## Try it

Create `.claude/agents/code-reviewer.md`:

```markdown
---
# name: what you call the agent by ("use the code-reviewer agent").
name: code-reviewer
# description: how the main agent decides when to hand work to this one.
description: Reviews code changes for bugs, security issues, and convention violations. Use after code changes and before committing.
# tools: the only tools it gets. No Edit or Write, so it can't change files.
tools: Read, Grep, Glob, Bash
---
You are a senior reviewer for this repo.

Scope: review uncommitted changes (`git diff HEAD`). If there are none, review the most recent commit (`git show HEAD`). Review only that diff, plus whatever surrounding code you need to understand it.

Report each finding as: severity (blocker / major / nit), file:line, problem, suggested fix.
Check especially for SQL injection, missing tests, business logic in routes, and naive datetimes.
Never edit files. If the diff is clean, say so in one line.
```

This is your first agent, so the `.claude/agents/` folder is brand new, and a running session doesn't watch folders that showed up after it started. **Delete this conversation and start a new one** on the repo folder.

Ask "Which subagents can you use?" to check it's there. Claude hands work to an agent automatically when its description matches, or you can just ask for it by name.

## Exercise (12 min): code review

Pretend a teammate just pushed a new search feature to `feat/event-search`. In the app's terminal, commit your work, then put their commit on a review branch of your own, so your `CLAUDE.md` and reviewer stay put:

```bash
git add -A                                # stage everything, including your new .claude/ files
git commit -m "save work"                 # commit it, so switching branches can't lose it
git switch -c review-search               # start a new branch from here for the review
git cherry-pick origin/feat/event-search  # copy the teammate's commit onto your branch
```

Ask:

> Use the code-reviewer agent to review the latest commit.

Then:

1. Have the **main** agent: `fix every blocker the reviewer found.`
2. In the same conversation, run the reviewer on the fix:

   > Use the code-reviewer agent to review my uncommitted changes.

   Keep going until it reports no blockers. It will probably still list a few majors and nits. That's normal: a reviewer can always find something, and deciding which ones are worth fixing is your call.

**Before M6,** commit your fixes and go back to your own branch: run `git add -A`, then `git commit -m "review fixes"`, then `git switch -`. Your search fixes stay behind on `review-search`, and that's fine: like reviewing a teammate's PR, the fixes belong to their branch, not yours.
