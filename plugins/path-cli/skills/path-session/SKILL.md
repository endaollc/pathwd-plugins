---
name: path-session
description: Use this skill at the start of any task that will use the PATH CLI — i.e., before calling the path_cli tool for anything that needs a signed-in user or an account. It checks the sign-in, starts the browser sign-in if needed, and saves the chosen account ID so later commands don't need `--account`. Also use it to switch accounts, sign out, or refresh stale name lookups. Other PATH skills depend on it.
---

Start a PATH session by confirming the user is signed in and selecting an account. All PATH operations run through the plugin's MCP tools: `authenticate`, `auth_status`, and `path_cli`. Never shell out and never call the API directly.

## Steps

1. **Check sign-in** — Call `auth_status`. If it says "Signed in" and lists accounts, skip to step 3.

2. **Sign in** — Call `authenticate`. It opens PATH's sign-in page in the user's browser and returns a sign-in URL. Tell the user to finish signing in there (share the URL in case the browser didn't open), then call `auth_status` again until it reports "Signed in".
   - Never ask for the user's email or password. The password is entered on PATH's own page, never in chat.
   - Don't run `path_cli(command: "auth ...")`. It waits for the browser and times out inside the tool; `authenticate` is the plugin's sign-in.
   - If `auth_status` reports the sign-in failed, call `authenticate` again.

3. **Select account** — Show the accounts from `auth_status` and ask which one to use. Save the choice as the default:
   ```
   path_cli(command: "use-account <id>")
   ```
   This writes `PATH_ACCOUNT_ID` to `~/.path-cli/.env`, so later commands don't need `--account`.

4. **Confirm** — State the active account ID. From here on, `--account <id>` is only needed to override the saved default for a single call.

## Usage pattern for the rest of the session

```
path_cli(command: "list-business-objects")
path_cli(command: "create-folder --bo-name Equipment --folder-name 'New Folder'")
path_cli(command: "api get /business-objects")
```

To target a different account for one call, pass `--account <other-id>` on that call only.

If any command fails with an authentication error, the saved sign-in has expired: go back to step 2.

## If PATH can't be reached

When `auth_status` or a command says **"Couldn't reach PATH at <host>: <reason>"**, the plugin got no answer from PATH. It isn't a sign-in problem, so don't start a new sign-in. Tell the user the host and reason from the message, then:

- **The host isn't `api.pathwd.app`** (and the user didn't mean to point the plugin at another PATH host): `~/.path-cli/.env` has an old `PATH_API_BASE_URL` line. Ask the user to delete that line or set it to `PATH_API_BASE_URL=https://api.pathwd.app`, then try again.
- **"the address doesn't exist" or "couldn't be looked up"**: no internet connection, or the network can't look up PATH's address. Ask the user to check that https://pathwd.app opens in their browser.
- **"connection was refused", "timed out" or "can't be reached"**: a network or firewall issue. In Cowork, an Owner may need to allow `api.pathwd.app` under claude.ai → Organization settings → Capabilities → Code execution (network egress and its domain list).
- **"secure connection couldn't be verified"**: antivirus HTTPS scanning (Norton, for example) or a proxy is inspecting traffic. The plugin already trusts the computer's own certificates, which covers this, as long as Node.js is 22.19 or later; if the message says to update Node.js, ask the user to install the current LTS from nodejs.org, then fully quit and reopen Claude. Otherwise, ask them to exclude `api.pathwd.app` from their antivirus's HTTPS (encrypted connection) scanning, or have IT allow it.

Retry once after the user has fixed the cause. Don't keep retrying unchanged.

## Switching accounts

- **List accounts again:** `path_cli(command: "list-accounts")` shows every account the signed-in user can open, with IDs and names.
- **Which account is the default?** `path_cli(command: "use-account")` with no ID prints the saved default.
- **Switch the default:** `path_cli(command: "use-account <id>")`. Every later command uses the new account. Tell the user which account is now active.

## Signing out

`path_cli(command: "logout")` clears the saved sign-in and the saved default account. Only run it when the user asks to sign out. To use PATH again, start over at step 2.

## Results look stale

To keep lookups fast, the CLI remembers lists it uses to match names to IDs (business objects, fields, relationships, milestones, steps, folders, pathways, pathway groups, users and roles) for 10 minutes. Something created or renamed in the last few minutes, by the user in PATH or by another session, may not be found by name yet.

- **One command:** put `--no-cache` before the command name, e.g. `path_cli(command: "--no-cache list-fields --bo-name Job")`.
- **Everything:** `path_cli(command: "clear-cache")` empties the cache for all accounts. It changes no PATH data; the next commands simply fetch fresh lists.
- **One account:** `path_cli(command: "clear-cache --account <id>")` empties just that account's cache.

## Access and acting user

Every command runs with **real-world access**, always: only what the signed-in user could see and do in the PATH web app. There is no "unrestricted" mode, no `--mode` option on ordinary commands and no `mode` command. A denial reads "<user> can't <x> — <why>, as in the web app."

- **Super-user, per pathway run:** `--mode super-user` on `pathway-run start` or `execute-pathway` (or `"mode": "super-user"` in a `pathway-run test` scenario) lets that run start even when the pathway's visibility criteria don't match the record. Every other launch rule still applies. Only users with full Admin or Designer permission may use it. See the `cli-pathways` command.
- **`--as-user <id|email|name>`** acts as another member of the account, as the web's Impersonate does. Only account **Admins** may use it, never on yourself or a deactivated user. Other users are refused.

## Designer work and the shared sign-in

This plugin is the PATH user app. Building or changing the data model, pathways, settings, integrations, document templates, users and roles is Designer work, done by an Admin or Designer in **PATH's Designer**. If the PATH Designer plugin (the `path_designer` tool) is installed, both plugins share one sign-in (`~/.path-cli/.env`), so signing in or switching accounts here carries over, and the Designer plugin's own `path-session` skill works the same way.
