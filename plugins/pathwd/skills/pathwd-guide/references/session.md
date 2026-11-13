# path-session

A PATH session is the account the user works in and its session context. Every PATH operation is a PATH CLI command, run by the connection's tool for its area (`path_account` for the account commands below; each command's page in [the command index](commands.md) names the tool for any command), with `request_file_upload` and `write_workspace_file` for files and `path_docs` for these guides. There is no raw API access.

## Steps

1. **Already signed in** — The user signed in to PATH when they connected PATHwd to this app, so there is no sign-in step. Every command runs as that user.
   - Never ask for the user's email or password, and don't run `auth` or `logout`: this connection refuses both.
   - If a command says the sign-in has expired or isn't valid, ask the user to reconnect PATHwd in the app they connected it from (its connector or MCP server settings), then try again.

2. **List accounts** — `list-accounts` shows every account the user can open. `use-account` with no ID prints the account already saved for this connection, if there is one.

3. **The account and its session context** — Which account to use is the user's choice (the saved one stays until they pick another).
   - **Another account:** `use-account <id>` saves it with this connection, so later commands don't need `--account`, and prints the session context.
   - **The saved account:** `session-context` prints its session context.
   The connection is shared by all of the user's chats, so the session context the first command of a session gives may already have been shown in an earlier chat; `session-context` prints it in full whenever it's needed.
   - Loading this guide (`path_docs(name: "path-session")`) starts a fresh PATH session for the conversation: context that commands would mark as shown earlier (in another chat, or in this one before a context compaction) is shown in full again.

4. **Confirm** — State the active account ID. From here on, `--account <id>` is only needed to override the saved default for a single call.

5. **Read the session context** — `use-account` prints the session context under the confirmation: the account with its Account context (the designers' description of the business: what the company does and how it uses PATH), the account's active users with their roles, the pathways the user can start from the dashboard with their context, and the user's process context (what the designers wrote for the user's roles on the business objects they work with). The first command of a session gives it too. Read it before acting and keep it in mind for the rest of the session. `session-context` prints it in full again (for example after your context was compacted); `get-account` shows the account part. If there's no Account context line, the account has none.

6. **PATH's concepts and the guides** — [`path-concepts`](concepts.md) explains what PATH and PATHwd terms mean (Jobs, milestones, workflow steps, tasks, pathways, folders) and how pathway screens are answered. The other guides and workflows cover each area step by step (the `path_docs` description lists them), and `path_docs(query: "<words>")` searches all of them.

## Usage pattern for the rest of the session

```
path_data_model(command: "list-business-objects")
path_manage_folders_reports(command: "create-folder", args: "--bo-name Equipment --folder-name 'New Folder'")
```

To target a different account for one call, pass `--account <other-id>` on that call only.

If any command fails with an authentication error, the connection's sign-in has expired: ask the user to reconnect PATHwd in their app (step 1).

## If PATH can't be reached

When a command says **"Couldn't reach PATH at <host>: <reason>"**, PATHwd's server got no answer from PATH. It isn't a sign-in problem and nothing on the user's computer causes it. Tell the user, try once more after a minute, and if it still fails, ask them to check that https://pathwd.app opens in their browser. "The server is busy; try again in a moment" means the same: wait a moment and retry once.

## Switching accounts

- **List accounts again:** `path_account(command: "list-accounts")` shows every account the signed-in user can open, with IDs and names.
- **Which account is the default?** `path_account(command: "use-account")` with no ID prints the saved default.
- **Switch the default:** `path_account(command: "use-account", args: "<id>")`. Every later command uses the new account. Tell the user which account is now active.

## Signing out

To sign out, the user disconnects PATHwd in the app they connected it from (its connector or MCP server settings). That ends the sign-in on the PATH server and deletes this connection's saved account and files. `logout` does nothing here (it is refused). Only bring this up when the user asks to sign out.

## Results look stale

To keep lookups fast, the CLI keeps the account's design metadata for the session: business objects, fields, field groups, relationships, milestones, workflow steps and tasks, folders, reports, stickers, pathways and their steps, calendars, users and roles. Records, and everything on them, are always read live. A session lasts until it has been idle for 2 hours. A design change made through the CLI clears the cache, and a name or id that isn't in it is looked up again with fresh data, so something just created is found by name. A change someone else made in the web app during the session (a renamed field, new options, a changed folder) shows after the cache is cleared:

- **One command:** put `--no-cache` before the command name, e.g. `path_data_model(command: "list-fields", args: "--no-cache --bo-name Job")`.
- **Everything:** `path_account(command: "clear-cache")` empties the cache for all accounts. It changes no PATH data; the next commands simply fetch fresh lists.
- **One account:** `path_account(command: "clear-cache", args: "--account <id>")` empties just that account's cache.

## Access and acting user

Every command runs with **real-world access**, always: only what the signed-in user could see and do in the PATH web app. There is no "unrestricted" mode, no `--mode` option on ordinary commands and no `mode` command. A denial reads "<user> can't <x> — <why>, as in the web app."

- **Super-user, per pathway run:** `--mode super-user` on `pathway-run start` or `execute-pathway` lets that run start even when the pathway's visibility criteria don't match the record. Every other launch rule still applies. Only users with a role that has the Designer permission may use it. See the [`cli-pathways` workflow](../../pathwd-pathways/references/workflows/pathways.md).
- **`--as-user <id|email|name>`** acts as another member of the account, as the web's Impersonate does. Only account **Admins** may use it, never on yourself or a deactivated user. Other users are refused.

## Designer work

This connection is the PATH user app. Building or changing the data model, pathways, settings, integrations, document templates, users and roles is Designer work, done by an Admin or Designer in **PATH's Designer**.

## Files

Commands run on PATHwd's server, in a private workspace for this connection, and only see files there. A file the user drops in the chat or has on their computer comes in through `attach_chat_file` (when the client passes it as a file input, as ChatGPT does) or `request_file_upload` (its curl command run where the file is, else its link for the user); use the `uploads/<id>/<name>` path either returns. A small file you write yourself (a test, a page of text) goes in with `write_workspace_file`. Files a command saves come back as download links in its result. The [`path-cli` guide](commands.md) has the details.
