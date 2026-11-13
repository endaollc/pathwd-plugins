# use-account

Runs with: path_account(command: "use-account", args: "<flags>") (read-only).

```
Usage: use-account [options] [account-id]

Set the default account for subsequent commands (kept for this connection),
and show its session context: the account and its context (what the business
does), the pathways you can start from the dashboard, and your process context

Arguments:
  account-id  Account ID to use (omit to print the current default)

Options:
```

## Examples

- args: `1234`

## Notes

Picks the account later commands work in, saves it, and prints that account's session context. With no account id it prints the saved one. Which account to use is the user's choice: `list-accounts` shows the ones they can open.

**Examples**

```
use-account
list-accounts
use-account 1668
list-business-objects --account 1234
```

- `use-account` with no id prints the saved account id, or `No default account set.` when there is none.
- `use-account <id>` checks that the user is an active member of that account, saves it, and prints `Default account set to <id> (<name>)` followed by the session context (see `session-context`). Read that before working in the account.
- `--account <id>` on any command works in another account for that call only; the saved account doesn't change.
- The saved account belongs to this connection, not to one chat: every chat on the connection uses it, and switching in one chat switches the others. When chats may work in different accounts, pass `--account` on each command instead of switching.

**Ids belong to one account.** Record, business object, field, pathway, folder, report, task and workflow step ids all come from the account they were read in; after a switch, an id from the other account is not found there. (A user keeps one user id across accounts.) Look ids up again in the new account. A pathway run stays in the account it started in, whatever account is saved later.

**Output:** the confirmation line, then the session context as text (account and its context, users and roles, dashboard pathways, process context).

**Common errors**

- `Account <id> isn't one of your accounts. Run "path list-accounts" to see the accounts you can use.`: a mistyped id, or an account the user doesn't belong to. Nothing was saved.
- `You're deactivated in account <id> (<name>), so you can't use it.`: the user was removed from that account; nothing was saved.
- `Missing --account <id>. Run "path list-accounts" to see available accounts.` (from other commands): no account saved yet; run `use-account <id>`.

**Related:** `list-accounts`, `session-context`, `get-account`.

Related commands: [`list-accounts`](list-accounts.md), [`get-account`](get-account.md), [`session-context`](session-context.md), [`get-profile`](get-profile.md), [`get-notification-settings`](get-notification-settings.md), [`list-users`](list-users.md), [`list-roles`](list-roles.md), [`web-link`](web-link.md), [`clear-cache`](clear-cache.md).
