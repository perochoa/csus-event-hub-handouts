# M7: MCP Servers (15 min)

**What we're doing:** giving the agent a new ability from outside the repo (your Google Drive), and learning when we don't actually need one.

## The idea

MCP (Model Context Protocol) is basically a standard plug for giving agents new tools: Google Drive, GitHub issues, a database, docs. **Only connect servers you trust.** Anything an MCP server sends back becomes context the agent might act on, and that includes text inside a doc someone else wrote.

Today we use the **Google Drive connector**. Anthropic hosts it, so there's nothing to install. You connect it once in the app, and your Code sessions pick it up.

## Connect Google Drive

> **Using the CSUS API key?** Connectors belong to a paid Claude account, and the API key setup doesn't use one, so you won't see Google Drive. Follow the **No Drive access?** steps below, and watch the Drive part on the projector.

1. Click **+** next to the prompt box, then **Add connectors**. (It's also under **Settings → Connectors**.)
2. Find **Google Drive** and click **Connect**.
3. Pick your Google account and approve the consent screen. Read what it's asking for: this is the access you're handing the agent.
4. Run `/clear`, then delete this conversation and start a new one on the repo folder, like in M5. Ask "Which Google Drive tools can you use?" to check it's connected.

Not there? Run `/status` and check you're signed in with your paid Claude account, not a key.

## Try it

Open [this link to a sample Google Sheet](https://docs.google.com/spreadsheets/d/1BSCGuSwKM5zFt-0WwN3H8mmPNREq2RPt45VdeSwW-v8/copy) called **Event Hub - Winter Events** and click **Make a copy** so it ends up in your own Drive. (Your copy is called "Copy of Event Hub - Winter Events". That's fine, the agent searches by name.)

Then ask:

> Find the "Winter Events" sheet in my Google Drive and read it. Add those events to `EVENTS` in app/seed.py, in the same format as the existing ones. Show me the diff before you write anything.

**No Drive access?** The same data is in the handouts: copy `assets/winter-events.csv` from the handout folder into your repo folder (drag it into the folder, or copy it in your file manager), then ask the same thing with `@winter-events.csv` in place of the sheet:

> Read @./winter-events.csv and add those events to EVENTS in app/seed.py, in the same format as the existing ones. Show me the diff before you write anything.

You'll get the same diff without any MCP server, which is a good preview of [CLI or MCP?](#cli-or-mcp) below. Delete the CSV from the repo folder when you're done, so it doesn't end up in your next commit.

Approve the Drive tool calls as they come up. The agent shows you the diff and waits: if it looks right, tell it to go ahead. Once it has edited `app/seed.py`, load the new data:

```bash
.venv/bin/python -m app.seed  # wipe the local database and refill it from app/seed.py
```

(Windows: `.venv/Scripts/python -m app.seed`.)

Now go the other way, from the repo out to Drive:

> Create a Google Doc in my Drive called "Event Hub: exec update" that lists every event in app/seed.py with its date, location, and capacity, sorted by date.

Open Drive in your browser and check the doc is there. Your club exec would honestly use this.

**No Drive access?** Ask for a file in the repo instead:

> Create exec-update.md in the repo folder that lists every event in app/seed.py with its date, location, and capacity, sorted by date.

Open it and check it, then delete it, like the CSV.

**Approve Drive calls the same way you approve commands.** The connector can also share and trash files. If the agent asks to do something in your Drive that you didn't ask for, deny it.

## CLI or MCP?

If you have the GitHub CLI (`gh`) installed, Claude can use GitHub with plain shell commands like `gh repo view` and `gh pr list`. You don't need an MCP server for that. Knowing when a CLI beats an MCP server is part of the skill. My rule of thumb: if there's a good CLI that Claude already knows, use it. Reach for MCP when there isn't one, or when the service needs a sign-in that a hosted connector handles for you. Google Drive has no standard CLI, which is why we used the connector today.
