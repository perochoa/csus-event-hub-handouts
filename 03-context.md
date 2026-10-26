# M3: Giving Useful Context (15 min)

**What we're doing:** pointing the agent at exactly the right code, error, and expected behaviour, and nothing else. Then measuring whether it actually made a difference.

## The idea

Think of context like a budget. A vague prompt makes the agent search, guess, and wander around. A precise prompt gets you a precise fix.

## Context tools

| Tool | Example | What it does |
| --- | --- | --- |
| `@` file reference | `@app/routes/rsvps.py` | Puts exactly that file in context |
| `/context` | any time | Shows how much of the context window is in use |
| `/clear` | between unrelated tasks | Wipes the context, so old context doesn't leak in |

## Exercise (10 min): same bug, two prompts

Here's the bug, straight up: **deleting an event leaves its RSVPs behind.** SQLite then reuses the deleted event's id, so if you delete the newest event and create a new one, the new event already has the old event's RSVPs, even though nobody has signed up for it. Call them ghost RSVPs.

We know exactly what's wrong, but the agent doesn't. We send it two prompts about the bug and compare what comes back:

- **Run A:** a lazy prompt. The agent has to guess what "broken" means.
- **Run B:** a precise prompt with the file, the steps, what we expected, and what we want back.

After each run, check what it did. `git status --short app/` in the app's terminal lists the `app/` files it changed.

- [ ] **Fixed the ghost RSVPs:** deleting an event now deletes its RSVPs too (the change is in `delete_event` in `app/services/events.py`).
- [ ] **Stayed on target:** `git status --short app/` lists only `app/services/events.py`.
- [ ] **Test reproduces the bug:** a new test deletes an event, creates a new one, and checks that the new one has no RSVPs.

Approve whatever the agent asks for, but don't add instructions. If it asks you a question, answer briefly.

### Run A

1. Make sure your M2 work is committed (`git status` says `nothing to commit`), then run `/clear`.
2. Send the lazy prompt, word for word:

   > RSVPs are broken, fix it.

3. Go through the checklist.
4. Throw the change away: run `git reset --hard`, then `git clean -fd`. Then run `/clear`.

### Run B

5. Send the precise prompt, word for word:

   > In @app/services/events.py, after I delete the newest event and create a new one, the new event already has the deleted event's RSVPs (`GET /rsvps?event_id=<new id>` isn't empty). Reproduce it with a failing test first, explain the root cause, then fix it. Don't change unrelated code.

6. Go through the checklist again. What did B get that A didn't?
7. Keep B's fix and save it. In the app's terminal, run `git add -A`, then `git commit -m "fix ghost rsvps"`. The next modules build on this code, so the fix should stay in it.
