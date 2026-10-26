# M6: Hooks (15 min)

**What we're doing:** turning suggestions into guarantees.

## The idea

Instructions are suggestions. Hooks are guarantees. A hook is a shell command that runs at a certain point in the agent's lifecycle: before a tool runs, after an edit, or when the agent stops. A hook can even **block** the action.

## Try it

1. Create the guard script at `.claude/hooks/block_env.py`. It reads the tool call as JSON on stdin. Exiting with code **2** blocks the call and sends the message back to the agent. (Script first: once the settings below load, a missing script blocks every tool call, including the one that would create it.)

   ```python
   """PreToolUse hook: keep the agent away from real secrets in .env files."""

   import json
   import re
   import sys

   # .env.example only holds placeholder values, so the agent may read it.
   ALLOWED = {".env.example"}


   def is_secret_file(path: str) -> bool:
       # Keep just the file name. Splitting on / or \ handles Windows paths too.
       name = re.split(r"[\\/]", path)[-1].lower()
       # .env, .env.local, .env.production... all count, except the allowed ones.
       return name.startswith(".env") and name not in ALLOWED


   # Claude Code sends the tool call as JSON, like
   # {"tool_name": "Read", "tool_input": {"file_path": ".env"}}
   data = json.load(sys.stdin)
   tool_input = data.get("tool_input", {})

   # Read, Edit and Write pass a file_path, Grep passes a path...
   paths = [tool_input.get("file_path", ""), tool_input.get("path", "")]
   # ...and Bash and PowerShell pass a command: pull out anything in it that looks like a .env file.
   paths += re.findall(r"[\w./\\-]*\.env[\w.-]*", tool_input.get("command", ""), re.IGNORECASE)

   if any(p and is_secret_file(p) for p in paths):
       # Exit code 2 blocks the tool call, and Claude gets what we print to stderr.
       # Any other ending (like reaching the end of the script) lets the call go ahead.
       print("Blocked: .env files are off-limits. Use .env.example instead.", file=sys.stderr)
       sys.exit(2)
   ```

2. Then create `.claude/settings.json`. Besides the guard, it runs ruff (the formatter and linter from the Commands section of your `CLAUDE.md`) after every edit Claude makes:

   ```json
   {
     "permissions": {
       "deny": ["Read(./.env)"]
     },
     "hooks": {
       "PostToolUse": [
         {
           "matcher": "Edit|Write",
           "hooks": [
             { "type": "command", "command": "${CLAUDE_PROJECT_DIR}/.venv/bin/python", "args": ["-m", "ruff", "format", "--quiet", "${CLAUDE_PROJECT_DIR}"] },
             { "type": "command", "command": "${CLAUDE_PROJECT_DIR}/.venv/bin/python", "args": ["-m", "ruff", "check", "--fix", "--exit-zero", "--quiet", "${CLAUDE_PROJECT_DIR}"] }
           ]
         }
       ],
       "PreToolUse": [
         {
           "matcher": "Read|Edit|Write|Grep|Bash|PowerShell",
           "hooks": [
             {
               "type": "command",
               "command": "${CLAUDE_PROJECT_DIR}/.venv/bin/python",
               "args": ["${CLAUDE_PROJECT_DIR}/.claude/hooks/block_env.py"],
               "onFailure": "block"
             }
           ]
         }
       ]
     }
   }
   ```

   A few things worth noticing (JSON has no comments, so the notes are here):

   - **`PreToolUse` / `PostToolUse`** say *when* a hook runs: before a tool call (so it can block it) or after one. **`matcher`** says *which* tools: ruff runs after an `Edit` or `Write`, and the guard runs before every tool that could touch a file.
   - **`command` and `args`** are the program to run and what to pass it. `${CLAUDE_PROJECT_DIR}` is your repo folder, so the paths work no matter which folder Claude's shell is in.
   - **`--exit-zero`** keeps ruff from failing when it finds a problem it can't fix itself, so you don't get a hook error after every edit.
   - **On Windows**, change all three `.venv/bin/python` to `.venv/Scripts/python.exe`. Keep the forward slashes: a backslash breaks the JSON.
   - **`"onFailure": "block"`** matters more than it looks. By default a hook that *crashes* (say, the Python path is wrong) counts as "no objection" and the tool call goes ahead, so the guard silently stops guarding. With `block`, a broken guard stops everything instead, and you notice.
   - The **`deny`** rule is a second lock on the same door. More on why in [The fun part](#the-fun-part).

3. Delete this conversation and start a new one on the repo folder, so it loads the new settings.

> `.claude/settings.json` gets committed and shared with your team. Personal hooks go in `.claude/settings.local.json`, which is gitignored.

## The fun part

Ask:

> Read the .env file and tell me the database password.

It won't get the password, but look at *what* stopped it. The Read tool is refused by the `deny` rule before the hook even runs, and Claude usually won't try anything else after that. To see the hook itself fire, ask for a shell command:

> Run `cat .env` and show me the output.

This time the error names `block_env.py`. That's your hook, and it's how you know the hook stopped it, not Claude just being polite.

Now try sneaking the file in yourself with an `@` mention:

> @.env what's the DB_PASSWORD?

An `@` mention pastes the file straight into your prompt **without a tool call**, so `PreToolUse` hooks never see it. The `deny` rule in `permissions` is what blocks this one.

## Exercise (7 min)

1. Ask the agent to make an edit, and in the same prompt, tell it to mess up the formatting on purpose:

   > Add a `GET /members/count` endpoint. Write it all on one line with no spaces around operators.

2. Look at the file afterwards. Did the hook clean it up?

Why mess it up on purpose? It stands in for the times the agent gets formatting wrong without being asked. The hook runs after every edit either way, so you don't have to hope it remembers.

**Before M7,** save your work: run `git add -A`, then `git commit -m "add hooks"`. At the end of M7 we go back over the agent's diff to `app/seed.py`, and that's easier when it's the only change.
