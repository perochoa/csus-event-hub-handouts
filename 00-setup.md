# 00: Install and Setup (before the workshop, or 10 min with us)

**What we're doing:** getting the Claude desktop app onto your laptop, signed in, and opened on the app repo, so every module after this is just exercises.

Did this at home? Check the [you're good to go](#youre-good-to-go-when) list and skip to [M1](01-first-task.md). Otherwise, follow along with the presenter at 0:10.

## Before you start

The [intro](README.md#intro-000) covers the codebase and how to set it up. By the end of it you should have cloned [csus-event-hub](https://github.com/perochoa/csus-event-hub), with its tests passing. Doing this at home? Do the intro's requirements and repository setup first.

## Install the Claude app

Download it from [claude.com/download](https://claude.com/download) and install it like any other app: drag it into Applications on a Mac, or run the installer on Windows. Claude Code is built into it, in the **Code** tab, so there's nothing else to install.

## Sign in

Claude Code needs either a paid Claude plan (Pro, Max, Team, or Enterprise) or an API key. **The free plan doesn't include Claude Code**, so if that's what you have, use the CSUS key.

**Option A: you have a paid Claude plan.** Open the app and sign in with your Claude account. That's it. This option also unlocks the Google Drive demo in M7.

**Option B: you're using the API key from your CSUS paper slip.**

1. Open the app, create a free Claude account (or sign in to the one you have), and follow the setup steps until you reach the main screen:

   <img src="assets/signin-main-ui.png" alt="The Claude app's main screen after signing in" width="600">

2. Turn on developer mode: **Help → Troubleshooting → Enable Developer Mode**. On a Mac, **Help** is in the menu bar at the top of the screen. On Windows, it's under the **☰** menu in the top-left corner of the app.

   Windows:

   <img src="assets/dev-mode-windows.png" alt="Windows: ☰ menu, Help, Troubleshooting, Enable Developer Mode" width="300">

   Mac:

   <img src="assets/dev-mode-mac.png" alt="Mac: Help menu, Troubleshooting, Enable Developer Mode" width="600">

3. The app restarts, and a new **Developer** menu shows up next to Help. Choose **Developer → Configure Third-Party Inference…**

   Windows:

   <img src="assets/third-party-menu-windows.png" alt="Windows: ☰ menu, Developer, Configure Third-Party Inference" width="300">

   Mac:

   <img src="assets/third-party-menu-mac.png" alt="Mac: Developer menu, Configure Third-Party Inference" width="450">

4. A settings window opens on **Connection**. Open the dropdown that says **Gateway** and pick **Claude API**:

   <img src="assets/third-party-1.png" alt="The Configure third-party inference window, with Gateway selected under Connection" width="450">

   <img src="assets/third-party-2.png" alt="The Connection dropdown with Claude API highlighted" width="450">

5. Set **Credential kind** to **Static API key**, then paste the key from your slip into the API key field:

   <img src="assets/third-party-3.png" alt="The Credential kind dropdown with Static API key highlighted" width="450">

6. Click **Apply Changes**, then **Save & Restart**.
7. When the app comes back, the sign-in screen has a new option to start with the configuration you just entered. Pick that one.

**Never paste your key into Discord, a prompt, or a committed file.** The keys get revoked the night of the workshop anyway.

## Open the repo in the Code tab

1. Click **Code** at the top of the sidebar:

   <img src="assets/code-tab-controls.png" alt="The Cowork and Code switch at the top of the sidebar, with Code selected" width="276">

2. Start a **new session** (**+ New** at the top of the sidebar). Under the prompt box, set the environment to **This computer** (the top entry under **Local**). Then click the folder control next to it, choose **Open folder…**, and pick your `csus-event-hub` folder. Pick the repo folder itself, not the folder it's in. Then set the model to **Sonnet 5.5**:

   <img src="assets/session-controls.png" alt="The controls under the prompt box: This computer, csus-event-hub, and Sonnet 5.5" width="700">

## The app's terminal

We run every terminal command today in the app's own terminal, not a separate one. Open it with the **Terminal** button in the top-right corner of the session. It starts inside the repo:

<img src="assets/terminal-button.png" alt="The Terminal button in the top-right corner of a Code session" width="700">

Check the app can see the repo's Python by running the tests from there:

| Mac                          | Windows                          |
| ---------------------------- | -------------------------------- |
| `.venv/bin/python -m pytest` | `.venv/Scripts/python -m pytest` |

**Every test should pass**, the same as when you set the repo up. If `git` or `python` isn't found, you probably installed it while the app was open: quit the app completely and reopen it. If anything else fails, grab one of the floaters.

We never activate the venv because the app starts Claude with its own environment, so an activated venv wouldn't reach Claude anyway. Calling the venv's Python by its path works everywhere.

## You're good to go when

- the tests pass in the app's terminal
- you have a session open on the `csus-event-hub` folder
