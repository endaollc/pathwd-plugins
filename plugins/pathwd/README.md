# PATH WD plugin

Work with your PATH WD account from Claude, ChatGPT and Codex: find, create and update records, read and add messages, notes, files and emails, browse and build folders and reports, run pathways, manage tasks, notifications and calendars, and read record history. The assistant always works with your own PATH access, so it can only see and change what you can in the PATH web app.

Version 1.0.1.

## What's in the plugin

- **The PATH WD connector**: a remote MCP server at `https://mcp.pathwd.app/mcp`, run by PathWD. Its tools read and change your PATH WD data; read tools and the tools that add, change or remove data are separate, so your app can ask before each change. The plugin itself contains no code; it only tells your app where the connector is.
- **Four skills** with PATH's documentation, so the assistant knows how PATH works without looking it up:
  - **pathwd-guide**: PATH's concepts, your account and session, every connector tool and command, and records, messages, notes, files, emails, tasks, notifications and calendars.
  - **pathwd-find-records**: Logic Builder formulas, advanced searches, folders and reports.
  - **pathwd-history**: what changed on a record, and why a record is or isn't in a milestone.
  - **pathwd-pathways**: pathways, pathway runs and their screens.

The skills carry PATH docs version `434fd7ab6862`. The connector serves the same documentation through its `path_docs` tool and reports its own docs version; when PATH's docs change before the next plugin release, `path_docs` has the newer text.

## What you need

- **A PATH WD account** you can sign in to at [pathwd.app](https://pathwd.app).
- **Claude** (Claude Code, the Claude desktop app with Cowork, or claude.ai), **Codex** or **ChatGPT**.

## Install

### Claude Code

In Claude Code, run:

```
/plugin marketplace add endaollc/pathwd-plugins
/plugin install pathwd@pathwd
```

Or from a terminal:

```
claude plugin marketplace add endaollc/pathwd-plugins
claude plugin install pathwd@pathwd
```

Then restart Claude Code, or run `/reload-plugins`.

### Claude desktop app (Cowork) and claude.ai

1. Open **Customize** and go to **Plugins**.
2. Choose **Add**, then **Add marketplace**, and enter `endaollc/pathwd-plugins`.
3. Open the **Discover** tab, find **PATH WD** and choose **Add**.

Turn on **Sync automatically** for the marketplace to get new versions as they come out.

### Codex

Add the marketplace:

```
codex plugin marketplace add endaollc/pathwd-plugins
```

Then start `codex`, type `/plugins`, open the **pathwd** marketplace tab and install **PATH WD**. Start a new session to use it.

### ChatGPT

In the ChatGPT desktop app, open the **Plugins** tab, find **PATH WD** (from the `pathwd` marketplace added above), open its details and select the plus button. Connect the PATH WD connector when asked, then start a new chat.

## Sign in

The first time the assistant uses a PATH WD tool, your app asks you to connect the PATH WD connector. It opens PATH WD's sign-in page in your browser, where you sign in to PATH as you normally do and allow the connection.

- **Your PATH password is never typed into the chat.** Sign-in only happens on PATH WD's own pages in your browser.
- If you can open more than one PATH account, the assistant asks which one to use. Say "switch to account …" at any time to change it.
- To sign out, disconnect the PATH WD connector in your app's connector settings.

## Try it

- "Show me my open tasks"
- "What changed on Job 12345 this week?"
- "Find all Jobs where the department is Residential"
- "Run the Win Job pathway on Job 12345"

## Network destinations and data flows

The plugin has no code of its own. Your app (Claude, ChatGPT or Codex) connects to the PATH WD connector, and the connector talks to PATH:

| Destination | Who connects | What goes there |
|-------------|--------------|-----------------|
| `mcp.pathwd.app` (PATH WD connector, run by PathWD) | Your app | Sign-in (OAuth), and each tool call: the PATH command and its arguments, which can include record data you ask the assistant to read or write and files you attach. Results come back to your app. |
| `pathwd.app` (PATH WD web app) | Your browser | The PATH sign-in page you use when connecting. |
| `api.pathwd.app` (PATH WD API) | The connector | Every PATH read and change, made as you with your own PATH access. |
| `files.pathwd.app` (PATH WD file server) | The connector | Record files the assistant downloads or uploads for you. |
| `chatgpt.com`, `*.oaiusercontent.com` | The connector | In ChatGPT only: a file you attach in the chat and ask the assistant to upload to PATH is fetched from ChatGPT's file link. |

Download links the connector returns for PATH files point to `mcp.pathwd.app`, need your PATH sign-in and work for 24 hours. Nothing is sent anywhere else, and the skills are plain text read by your app.

## Update

- **Claude Code:** run `/plugin marketplace update pathwd`, then restart Claude Code. Turn on auto-update for the marketplace in `/plugin` to get new versions automatically.
- **Claude desktop app:** with **Sync automatically** on, new versions arrive on their own.
- **Codex:** run `codex plugin marketplace upgrade pathwd`.

## Support

Questions or problems? Use **Submit Support Ticket** at the bottom of the menu in PATH, or email [support@pathwd.com](mailto:support@pathwd.com). Include what you asked and the exact error message.

## Privacy

The connector acts only as you, with your own PATH access. See the [PathWD privacy policy](https://www.pathwd.com/privacy-policy).

## License

Proprietary. Free to use with a PATH WD account; see [LICENSE](LICENSE).
