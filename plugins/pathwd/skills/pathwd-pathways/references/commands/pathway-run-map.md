# pathway-run map

Runs with: path_pathways(command: "pathway-run map", args: "<flags>") (read-only).

```
Usage: pathway-run map [options]

Map a pathway before running it (read-only, nothing runs): every step in order
with what its screen shows and asks for (fields, choices, question text) or
what it does on its own, and where the run goes next for each outcome — Fork
conditions as formula text, Yes/No and found/not-found branches, List Loops,
steps that loop back, sub-pathways, ends, unreachable steps. --record-id also
says whether the user could start it on that record now and which branch each
Fork on the starting record takes now. Only pathways the user sees in the web
app

Options:
  --pathway-id <id>          Pathway ID
  --pathway-name <name>      Pathway name (exact, case-insensitive)
  --record-id <id>           Also check this starting record as it is now
                             (launch checks, Fork branches on it)
  --sub-pathways             Also map every pathway its Launch Pathway steps
                             run
  --as-user <id|email|name>  Map as another account member (admin impersonation
                             session)
```

## Examples

- args: `--pathway-name "Win Job" --record-id 6a9056455ff33756afba5084`

## Notes

A read-only map of a pathway before running it: every step in order, what each screen will ask for, what each automatic step does on its own, and where the run goes next for each outcome. Nothing runs and nothing is saved. Use it to tell the user what a run involves, and to gather the answers before `pathway-run start`.

**Examples**

```
pathway-run map --pathway-name "Record Job Payment"
pathway-run map --pathway-name "Record Job Payment" --record-id 6a9056455ff33756afba5084
pathway-run map --pathway-id 25928 --record-id 6a9056455ff33756afba5084
pathway-run map --pathway-id 25886 --sub-pathways
```

- `--pathway-name` is the exact name (case doesn't matter); an unknown name lists close matches. Two pathways with the same name: use `--pathway-id`.
- `--record-id` adds whether the user could start it on that record now (the same checks as `pathway-run available`) and marks the branch each Fork on the starting record takes now (`[taken now]`). Steps before a Fork may change the record first, so a marked branch is today's answer, not a promise.
- Launch Pathway steps name the pathway they run; `--sub-pathways` maps those too.
- Only pathways the user sees in the web app can be mapped: one their roles hold (admins and Designers see every one).

**Output**

- Text: a header (`starts on a Job record · offered on: the Job record viewer`), a count line (`10 steps: 2 screens, 8 automatic · 3 branching · 1 loop · 2 ways to end · 0 unreachable`), then each step: `1. "Enter Payment Details" · Create Record · screen`, a `screen:` line (fields, `*` = required, question text, choices) or a `does:` line, and arrows (`found →`, `not found →`, `if [Enabled] = TRUE →`, `otherwise ■ end of pathway`, `↻` loop back). Then Loops, Ends, unreachable steps and problems.
- A step marked `sometimes` shows a screen only in some cases (an email that sends itself unless something is missing, a file pick that skips itself when there's one file).
- `Problems:` lists outcomes where the run would fail: a next step or branch not configured, one pointing at a step that doesn't exist, a Fork with no condition. With `--record-id`, the launch checks for that record come first.
- Step numbers are the Designer's order; a run starts at step 1 and follows the arrows, so it can skip steps.

**Common errors**

- `Pathway "X" not found.` with close matches, or `See pathway-run available --from dashboard, or --record-id <id> for a record.`
- `Ambiguous pathway name "X". Matches: …  Use --pathway-id.`
- `<user> can't see pathway <id> "<name>" — none of their roles holds the pathway, as in the web app.` (or: it has no steps / has validation errors).

**Related:** `pathway-run available`, `pathway-run start`, `execute-pathway` (for a pathway with no screens; with screens it runs nothing unless told to skip them). How each screen is answered: the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [pathway-screens.md](../pathway-screens.md).

Related commands: [`pathway-run available`](pathway-run-available.md), [`pathway-run show`](pathway-run-show.md), [`pathway-run list`](pathway-run-list.md), [`pathway-run log`](pathway-run-log.md).
