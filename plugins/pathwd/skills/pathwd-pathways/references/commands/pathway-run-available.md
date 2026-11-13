# pathway-run available

Runs with: path_pathways(command: "pathway-run available", args: "<flags>") (read-only).

```
Usage: pathway-run available [options]

Which pathways a user could start from a launch point (a record's Pathways tab,
the dashboard, a folder or a task), each with its designer context (what it is
for: read it to decide whether a pathway does what the user asked). These are
the only pathway lists, as in the web app: --from dashboard for the
dashboard's, --record-id <id> for a record's. Like all context, a pathway's is
shown once per session (later calls leave it out; --with-context repeats it)

Options:
  --record-id <id>           Record viewer of this record
  --task-id <id>             Completing this task (with --record-id)
  --folder-id <id>           This folder's header
  --from <point>             record | dashboard | folder | task (default: from
                             the other flags)
  --as-user <id|email|name>  Check for another account member (admin
                             impersonation session)
```

## Examples

- args: `--from dashboard`
- args: `--record-id 6a9056455ff33756afba5084`

## Notes

Which pathways the user could start from one launch point, checked the way the web app decides what to offer: a record's Pathways tab, the dashboard, a folder's header, or completing a task. Nothing runs. These are the only pathway lists, as in the web app: `--from dashboard` is the dashboard's, `--record-id <id>` a record's (there is no list of every pathway in the account).

**Examples**

```
pathway-run available --record-id 6a9056455ff33756afba5084
pathway-run available --from dashboard
pathway-run available --from folder --folder-id 20975
pathway-run available --record-id 6a9056455ff33756afba5084 --task-id 9071
```

- The launch point comes from the flags: `--task-id` (with `--record-id`) is a task, `--folder-id` a folder, `--record-id` a record, none the dashboard. `--from` names it outright.
- A record launch checks the role, "show on records", the pathway's starting business object, that the user can open the record, and the pathway's visibility criteria against the record as it is now. Dashboard and folder launches start without a record, so a record id there is ignored.
- Only the pathways the user can start are listed. Why a particular one isn't offered on a record: `pathway-run map --pathway-name "<name>" --record-id <id>` (read-only) gives the launch checks that failed (below); `pathway-run start` refuses with the same checks.
- Each pathway's designer context says what it is for: read it to pick the pathway that does what the user asked. Context shown once in the session is left out later; `--with-context` repeats it.

**Output (JSON):** `{user {id, email}, launch_point, pathways: [{id, name, context?}]}`. A pathway whose context was left out has `context_elided: true`, listed under `context_shown_earlier`. The text form is one `<id>  <name>` line per pathway, with its context under it.

**Reading the launch checks** (in `pathway-run map --record-id` and `pathway-run start`'s refusal)

- `role permission`: none of the user's roles holds the pathway (admins hold every one). Not checked for a task launch.
- `visibility criteria … undecidable: …`: the criteria couldn't be worked out from the record; the web app treats that as not met.
- `task`, `task pending`, `task pathway`, `may complete task`: the task must be on the record, pending, carry this pathway, and the user must be allowed to finish it.
- A pathway with only the visibility criteria failing can still be started by a Designer with `pathway-run start --mode super-user`.

**Related:** `pathway-run map` (what it will ask), `pathway-run start`, `execute-pathway`, `get-record` (`available_pathways` for one record). Why a user can't see or do something in general: the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [access-and-visibility.md](../../../pathwd-guide/references/cli/access-and-visibility.md).

Related commands: [`pathway-run map`](pathway-run-map.md), [`pathway-run show`](pathway-run-show.md), [`pathway-run list`](pathway-run-list.md), [`pathway-run log`](pathway-run-log.md).
