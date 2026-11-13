# session-context

Runs with: path_account(command: "session-context", args: "<flags>") (read-only).

```
Usage: session-context [options]

Show the session context in full: the account and its context, its users and
their roles, the pathways you can start from the dashboard with their context,
and your process context (guidance for your roles on the business objects you
can view or edit). It is given automatically at sign-in, on use-account and
before the first command of every session; run this to reload it, e.g. after
your context was compacted

Options:
```

## Examples

- args: `--account 1234`

## Notes

The session context of the current account, in full: the account and its context (what the business does and how it uses PATH), its active users with their roles, the pathways the user can start from the dashboard with their context, and the user's process context (what the designers wrote for the user's roles on the business objects they work with). It changes nothing.

**Examples**

```
session-context
session-context --account 1668
```

- The first command of every session, and `use-account`, already give the session context; this command prints it again in full. Run it when the context is no longer in view (after a context compaction, in a long conversation), when the user asks who works here or what the business does, or before planning work in an account.
- It is always printed in full, whatever was shown before. Other commands show each piece of designer context (a business object's, a field's, a pathway's) once per session and leave it out after that; `--with-context` on those commands repeats it.
- `--account` reads another account's session context for this call only; the saved account doesn't change (`use-account` changes it).

**Output**

```
Session context (loaded once per session; "path session-context" reloads it):

Account: Intersellar Windows and Doors DEMO (1668) · time zone America/Chicago · country US
  Account context:
    **Intersellar Windows and Doors**
    …

Users (22; every member also has All Users; 12 deactivated not listed):
  110  AB Admin <ashley@pathwd.com>: Accounting, Fulfillment, Path Admin, Path Designer, …
  108  AT Admin <andrew@pathwd.com>: Accounting, QBO Integration (disabled), Receiving, …

Dashboard pathways (19):
  25821  Create Tracking Labels
  26209  Create Commercial Project
  …

Process context (0):
  (none)
```

- `Account:` the account's name, id, time zone and country (a child account adds `child of account <id> (design inherited)`), then its Account context (left out when the designers wrote none).
- `Users`: active members only, by name, each `<id>  <name> <email>: <roles>` (All Users left out, a disabled role marked `(disabled)`); the heading counts the deactivated ones. `list-users` shows them all.
- `Dashboard pathways`: the same list as `pathway-run available --from dashboard`, each with its context.
- `Process context`: what the designers wrote for the user's roles, grouped by business object; `(none)` when they wrote none.
- On this connection a second part follows the output: PATH's core ideas and the list of guides.

**Common errors**

- `Missing --account <id>. Run "path list-accounts" to see available accounts.`: no account is saved yet; `list-accounts`, then `use-account <id>`.

**Related:** `use-account`, `list-accounts`, `get-account` (the account part alone), `list-users`, `pathway-run available`.

Related commands: [`list-accounts`](list-accounts.md), [`use-account`](use-account.md), [`get-account`](get-account.md), [`get-profile`](get-profile.md), [`get-notification-settings`](get-notification-settings.md), [`list-users`](list-users.md), [`list-roles`](list-roles.md), [`web-link`](web-link.md), [`clear-cache`](clear-cache.md).
