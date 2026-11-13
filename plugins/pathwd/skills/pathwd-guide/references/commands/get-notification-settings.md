# get-notification-settings

Runs with: path_account(command: "get-notification-settings", args: "<flags>") (read-only).

```
Usage: get-notification-settings [options]

Your notification settings: account-wide rows (task, feed, …), or with
--resolved every notification that can reach you and which channels you may
turn on

Options:
  --resolved          Per-notification rows with each channel's required /
                      allowed / not-allowed state
  --include-disabled  With --resolved: also the notifications whose rule is
                      switched off in the Designer (they send nothing until it
                      is switched on)
```

Related commands: [`list-accounts`](list-accounts.md), [`use-account`](use-account.md), [`get-account`](get-account.md), [`session-context`](session-context.md), [`get-profile`](get-profile.md), [`list-users`](list-users.md), [`list-roles`](list-roles.md), [`web-link`](web-link.md), [`clear-cache`](clear-cache.md).
