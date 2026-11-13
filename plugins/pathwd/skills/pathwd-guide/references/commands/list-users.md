# list-users

Runs with: path_account(command: "list-users", args: "<flags>") (read-only).

```
Usage: list-users [options]

The account's active users: each one's name and role names, nothing else (every
user also has All Users). The same plain table in every format; JSON is an
array of { name, roles }. Use the name shown here wherever a command takes a
user (assign-task --user, create-task --assign-to, --as-user, share-calendar
--users); when two users share a name, give the email address instead. Needs
Admin or User Admin, as the web's Users page

Options:
  --include-inactive  Also list the inactive users, marked inactive in a status
                      column (Admin or User Admin)
```

## Notes

The account's active users by name, each with their role names: what the web's Users page lists, and nothing more (no ids, emails, tags or role settings). Like that page it needs Admin or User Admin. For a user's name to give another command, this is the list.

**Examples**

```
list-users
list-users --fields name
list-users --quiet
list-users --include-inactive
```

- Every member also has All Users; it isn't repeated on each row. A disabled role is marked `(disabled)`. Inactive (deactivated) users aren't listed; the footer counts them.
- `--include-inactive` lists the inactive users too, after the active ones, marked `inactive` in a `STATUS` column. It reads the web's Users page list, which the server gives only to Admins and User Admins.
- The name is first and last name (the email address for a user with no name). Every command that takes a user accepts it: `assign-task --user`, `create-task` / `edit-task --assign-to`, `--as-user`, `share-calendar --users`, `share-calendar-group --users`, `send-message --to`, `create-folder` / `create-report --users`. An email address works too.
- Two users with the same name: the footer names the shared name, and commands given that name refuse it and ask for the user's email address. Ask the user which person they mean and use that person's email.

**Output**

The same in every format:

- Text: a table `NAME | ROLES` (roles comma-separated), then a footer such as `[42 users; everyone also has All Users; 3 inactive not listed]`.
- JSON: an array of `{"name", "roles": [...]}`, one per line. Nothing else.
- `--quiet`: names, one per line. `--fields` picks `name` and/or `roles` (and `status` with `--include-inactive`); any other key is refused.
- With `--include-inactive`: `NAME | ROLES | STATUS` (`inactive` on inactive users, blank on active ones), footer `[22 active, 12 inactive users; everyone also has All Users]`; JSON rows add `"status": "inactive"` or `null`.

**Common errors**

- `<user> can't list the users — the web's Users page needs Admin or User Admin, as in the web app.`: the user isn't an Admin or User Admin. Their session context still names the account's active users.
- `<user> can't list the inactive users — the web's Users page needs Admin or User Admin, as in the web app.` (`--include-inactive`).

**Related:** `session-context` (the users and their roles, given at the start of a session), `list-roles`, `assign-task`, `create-task`, `share-calendar`. Why a user can or can't see something: the [`path-cli` guide](../commands.md)'s [access-and-visibility.md](../cli/access-and-visibility.md).

Related commands: [`list-accounts`](list-accounts.md), [`use-account`](use-account.md), [`get-account`](get-account.md), [`session-context`](session-context.md), [`get-profile`](get-profile.md), [`get-notification-settings`](get-notification-settings.md), [`list-roles`](list-roles.md), [`web-link`](web-link.md), [`clear-cache`](clear-cache.md), [`assign-task`](assign-task.md), [`create-task`](create-task.md), [`share-calendar`](share-calendar.md).
