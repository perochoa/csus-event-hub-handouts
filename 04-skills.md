# M4: Skills (15 min)

**What we're doing:** turning "the way we always do it" into a reusable procedure the agent picks up on its own.

## The idea

A skill is just a folder with a `SKILL.md` in it. The agent only loads the skill when the task matches its **description**, so the description has to say _when_ to use it.

## Try it

First, make sure your M3 fix is committed (`git status` says `nothing to commit`), then run `/clear` so nothing from the bug hunt carries over.

Then create `.claude/skills/write-endpoint-tests/SKILL.md`:

```markdown
---
# name: the skill's id. It's also the slash command, so /write-endpoint-tests runs it by hand.
name: write-endpoint-tests
# description: all Claude sees of the skill until it's used. Say what it does and when to use it.
description: Write pytest tests for a FastAPI endpoint in this repo. Use when asked to add, extend, or fix tests for a route.
---

# Writing endpoint tests

1. Read the route and the models it touches.
2. Read tests/conftest.py and reuse the existing fixtures.
3. Cover: happy path, validation error (422), not found (404), and every business rule in the route.
4. Run the file with the venv's Python (`.venv/bin/python -m pytest <file> -q`, Windows `.venv/Scripts/python -m pytest <file> -q`) and iterate until the tests themselves are correct.
5. If a test exposes a real bug, stop and report it. Never weaken a test to make it pass.
```

Claude Code picks up edits to skills on its own, but only in skill folders that already existed when the session started. This is your first skill, so tell it to look again:

> /reload-skills

Then type `/write` in the prompt box (don't send it) and check that your new skill shows up in the menu. Not there? Make sure the file is exactly `.claude/skills/write-endpoint-tests/SKILL.md` (capital `SKILL`), then run `/clear`.

## Exercise (7 min): testing

Ask:

> Write tests for the RSVP routes.

Keep an eye out for:

- [ ] The skill kicks in. The agent mentions it or follows its steps.
- [ ] The tests reuse the fixtures from `conftest.py`.
- [ ] Step 5 makes the agent **report** any real bug it finds instead of quietly writing a passing test around it. Did yours find one?

Found a bug? The agent left its test failing on purpose, so the bug is easy to see. Now ask:

> Fix the bug your failing test found.

Then run all the tests in the app's terminal (`.venv/bin/python -m pytest`, Windows `.venv/Scripts/python -m pytest`). Everything should pass before you move on, because M5 and M6 build on this code.
