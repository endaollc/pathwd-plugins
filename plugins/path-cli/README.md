# PATH WD plugin for Claude

Work with PATH WD from Claude: find and update records, browse folders and reports, run pathways, manage tasks and calendars, send messages and upload files. Claude always works with your own PATH access, so it can only see and change what you can in the PATH web app.

The plugin gives Claude:

- **Skills and slash commands** (`/path-cli:cli-records`, `/path-cli:cli-pathways` and more) that teach it how PATH works
- **A small local server** with the tools Claude uses to talk to PATH: `authenticate`, `auth_status` and `path_cli`

## What you need

- **Node.js 22.19 or later** on your computer. Install the current LTS from [nodejs.org](https://nodejs.org), then fully quit and reopen Claude.
- **A PATH WD account** you can sign in to at [pathwd.app](https://pathwd.app).
- **Claude Code**, or the **Claude desktop app** (Cowork). The local server runs on your computer, so the PATH tools work in Claude Code and in Cowork. Claude chat on the web can't run local servers: there Claude gets the skills, but not the tools.

## Install

### Claude Code

In Claude Code, run:

```
/plugin marketplace add endaollc/pathwd-plugins
/plugin install path-cli@pathwd
```

Or from a terminal:

```
claude plugin marketplace add endaollc/pathwd-plugins
claude plugin install path-cli@pathwd
```

Then restart Claude Code.

### Claude desktop app (Cowork) and claude.ai

1. Open **Customize** and go to **Plugins**.
2. Choose **Add**, then **Add marketplace**, and enter `endaollc/pathwd-plugins`.
3. Open the **Discover** tab, find **PATH WD** and choose **Add**.

Turn on **Sync automatically** for the marketplace to get new versions as they come out (it's off by default).

The PATH tools run in Cowork. In regular chat, in the desktop app or on claude.ai, Claude gets the skills but not the tools.

## Sign in

Ask Claude to **"sign in to PATH"**. Claude opens PATH's sign-in page in your browser, and you sign in there as you normally do.

- **Never type your PATH password into the chat.** The plugin never asks for it; sign-in only ever happens in your browser.
- If you can open more than one PATH account, Claude asks which one to use. Say "switch to account …" at any time to change it.
- Your sign-in is saved on your computer in `~/.path-cli/.env`. To sign out, ask Claude to "sign out of PATH".

## Try it

- "Show me my open tasks"
- "What changed on Job 12345 this week?"
- "Find all Jobs where the department is Residential"
- "Run the Win Job pathway on Job 12345"

## Update

New versions are published here. To get the latest one:

- **Claude Code:** run `/plugin marketplace update pathwd`, then restart Claude Code. Turn on auto-update for the marketplace in `/plugin` to get new versions automatically.
- **Claude desktop app:** with **Sync automatically** on, new versions arrive on their own. Otherwise go to **Customize → Plugins**, sync the **pathwd** marketplace, then restart the app.

## Support

Questions or problems? Use **Submit Support Ticket** at the bottom of the menu in PATH, or email [support@pathwd.com](mailto:support@pathwd.com). Include what you asked Claude and the exact error message.

## Privacy

The plugin runs on your computer and talks only to PATH, using your own sign-in. See the [PathWD privacy policy](https://www.pathwd.com/privacy-policy).

## License

Proprietary. Free to use with a PATH WD account; see [LICENSE](LICENSE).
