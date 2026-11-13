# PATH WD plugins

The `pathwd` plugin marketplace from PathWD, for Claude (Claude Code, the Claude desktop app with Cowork, claude.ai), Codex and ChatGPT.

| Plugin | What it does |
|--------|--------------|
| [PATH WD](plugins/pathwd/README.md) (`pathwd`, version 1.0.1) | Work with your PATH WD account through the PATH WD connector (`https://mcp.pathwd.app/mcp`), signed in as you: records, folders, reports, pathways, tasks, calendars, messages, files and history, with PATH's documentation as skills. |

## Add the marketplace

- **Claude Code:** `/plugin marketplace add endaollc/pathwd-plugins`, then `/plugin install pathwd@pathwd`.
- **Claude desktop app and claude.ai:** **Customize → Plugins → Add → Add marketplace**, enter `endaollc/pathwd-plugins`, then add **PATH WD** from **Discover**.
- **Codex:** `codex plugin marketplace add endaollc/pathwd-plugins`, then install **PATH WD** from `/plugins`.
- **ChatGPT desktop app:** install **PATH WD** from the **Plugins** tab once the marketplace is added.

The plugin's [README](plugins/pathwd/README.md) covers sign-in, what the plugin sends where, updates and support.

This repository is published from PathWD's source; changes made here directly are replaced by the next release.
