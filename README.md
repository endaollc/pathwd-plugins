# PATHwd plugins

The `pathwd` plugin marketplace from PATHwd, for Claude (Claude Code, the Claude desktop app with Cowork, claude.ai), Codex and ChatGPT.

| Plugin | What it does |
|--------|--------------|
| [PATHwd](plugins/pathwd/README.md) (`pathwd`, version 1.0.4) | Work with your PATHwd account through the PATHwd connector (`https://mcp.pathwd.app/mcp`), signed in as you: records, folders, reports, pathways, tasks, calendars, messages, files and history, with PATH's documentation as skills. |

## Add the marketplace

- **Claude Code:** `/plugin marketplace add endaollc/pathwd-plugins`, then `/plugin install pathwd@pathwd`.
- **Claude desktop app and claude.ai:** **Customize → Plugins → Add → Add marketplace**, enter `endaollc/pathwd-plugins`, then add **PATHwd** from **Discover**.
- **Codex:** `codex plugin marketplace add endaollc/pathwd-plugins`, then install **PATHwd** from `/plugins`.
- **ChatGPT:** until PATHwd is listed in ChatGPT's apps directory, add the connector `https://mcp.pathwd.app/mcp` as a custom connector (developer mode); see the plugin's README.

The plugin's [README](plugins/pathwd/README.md) covers sign-in, what the plugin sends where, updates and support.

This repository is published from PATHwd's source; changes made here directly are replaced by the next release.
