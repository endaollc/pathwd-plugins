---
name: path-logic-formula-syntax
description: "Use this skill to read, write, validate or save a PATH Logic Builder formula in the user app. Writes folder, folder-sticker and report filters; reads any target (milestone, task, Fork, sticker, visibility) with get-filter and parse-filter. Covers the syntax ([Field] = \"value\", AND/OR, ISANYOF, INMILESTONE, SETTING(), BLANK). E.g. \"what are the criteria for the Won milestone?\", \"make this folder show won jobs\". Other targets are Designer work."
---

> **How to run commands:** every command in this skill is a PATH CLI command string. Run it by calling the `path_cli` tool with the string as `command`, for example `path_cli(command: "list-business-objects")`. Never run these commands in a terminal. If the user isn't signed in yet, run the `path-session` skill first.

Logic Builder filters (pipeline `GroupFilter`s) are edited as **formula text**. This plugin is the user app, so it **reads any target** (`get-filter`, `parse-filter`) but **writes only folder, folder-sticker and report filters** (`set-filter`, `create-folder --logic`). Writing every other target (milestone, task, Fork, table-filter, pathway, sticker, visibility rules, logic settings, BO overrides) is Designer work, done by an Admin or Designer in PATH's Designer (or with the PATH Designer plugin's `path-logic-formula-syntax` skill, if it's installed). The CLI parses the text, resolves field / option / relationship / milestone / step / setting names against the business object the filter is rooted on, validates the pipeline with the API exactly as the builder modal does, and saves the saved-shape JSON — you never hand-write filter JSON.

If no PATH session is active, run the `path-session` skill first.

## Commands

| Command | Purpose |
|---|---|
| `parse-filter --bo-name <bo> --text '<formula>'` | Validate without saving. Prints the normalized formula and rule count; `--json` (global flag, before the command) prints the `GroupFilter`. `--report` unlocks entry dates + `?`; `--allow-blank` + `--allow-entry-dates` match a folder; `--allow-entry-dates` unlocks entry dates alone. `--bo <id>` = `--bo-id`. |
| `get-filter <target> <id> [--bo <id>] [--pathway <id>] [--sticker <id>]` | Print a stored filter as a formula (`(no filter)` when unset). Reads any target. `--json` prints the stored pipeline. |
| `set-filter <target> <id> --text '<formula>'` | **Folder, folder-sticker and report targets only** (with Edit on them); any other target is refused as Designer work. Parse against the target's root BO with the target's feature set, call `POST /v1/builder/validate`, then save the object with only its filter replaced (the rest of the pipeline — `version`, `business_object_ids`, `sorting`, `quick_search` … — is kept). `--clear` saves the filter as null. `--dry-run` parses + validates and prints the pipeline without saving. |
| `create-folder --bo-name <bo> --folder-name <n> --logic '<formula>' ...` | Create a **new** folder whose `filter_condition` is the formula (parsed with folder features: `?` + entry dates). `--dry-run` prints the request bodies without creating anything; `--logic-raw <json>` is the raw escape hatch. See the `cli-folders-skill` command for the rest of folder creation. |

Invocation: `[--account <id>] [--json] <command> ...`. Always single-quote the formula in the command string.

## Targets

`get-filter` reads every target below. `set-filter` writes only `folder`, `folder-sticker` and `report`.

| Target | id | Extra | Rooted on | Features |
|---|---|---|---|---|
| `milestone` | milestone id | | the milestone's BO | — |
| `task-complete` / `task-cancel` / `task-filter` | task id | `--bo` | the task's BO | — |
| `fork` | pathway step id | `--pathway` | the step's record BO | — |
| `table-filter` | pathway step id (Edit Record Table) | `--pathway` | the step's record BO | — (ANDed onto the table's rows at run time) |
| `pathway` | pathway id | | the starting BO | — |
| `sticker` | BO sticker id | | the sticker's BO | — |
| `folder` | folder id | | the folder's BO | `?` |
| `folder-sticker` | folder id | `--sticker <folder sticker id>` | the folder's BO | — |
| `report` | report id | | the report's BO | entry dates + `?` |
| `field-visibility` | field id | | the field's BO | — |
| `field-group-visibility` | field group id | | the group's BO | — |
| `relationship-source-visibility` | relationship id | | source BO | — |
| `relationship-target-visibility` | relationship id | | target BO | — |
| `target-field-visibility` | relationship column (target field setting) id | | source BO | — |
| `setting` | logic account setting id (read-only here) | | none — settings only | settings only |
| `bo-show-in-menu` | business object id (`list-business-objects`) | | none — settings only | settings only |

Side effects `set-filter` mirrors from the web forms (printed as `NOTE:` lines): folder and report user-filter selections whose rule is gone are dropped. Folder stickers save only on custom folders. Pathway saves re-send the pathway's groups (the API renumbers their order) and leave permissions alone.

## Workflow

**Existing object:**
1. Find the object: `list-folders`, `list-reports` (to read others: `list-workflow-milestones`, `list-workflow-tasks`, `list-pathways`, `list-fields`, `list-field-groups`, `list-relationships`).
2. `get-filter <target> <id>` to see the current rules.
3. Draft the new formula; check it with `parse-filter --bo-id <root BO>` (add `--report` for reports, `--allow-blank --allow-entry-dates` for folders).
4. For a folder, folder-sticker or report: `set-filter <target> <id> --text '...'` (try `--dry-run` first), then `get-filter` to confirm. For any other target, stop at step 3 and give the user the formula to set in PATH's Designer (or with the PATH Designer plugin, if installed).

**Logic account settings and BO Show in Menu overrides** are read here with `get-filter setting <id>` / `get-filter bo-show-in-menu <bo id>`; writing them (`create-setting --type logic`, `set-filter setting`, `set-filter bo-show-in-menu`) is Designer work (PATH's Designer, or the PATH Designer plugin if installed).

**New folder:**
1. Resolve the root BO (`list-business-objects`), then draft and check the formula the same way (`parse-filter`, `--allow-blank --allow-entry-dates` for a folder).
2. `create-folder --bo-name <bo> --folder-name <n> --logic '...' ...` (try `--dry-run` first), then `list-folders` or `get-filter` to confirm.

## Syntax reference

**Rules** are joined by `AND` or `OR` — one connective per level; mixing needs parentheses: `A AND (B OR C)`. Each `( … )` is a builder group. No `NOT`: use the negative forms.

**Comparisons**: `[Field] = value`, `!=` (`<>` accepted), `> >= < <=`. Right side: a literal, another field (`[Total] > [Customer.Credit Limit]`), `SETTING("name")`, or `CURRENTUSER()` (compared by full name).

**Names**
- `[Field]`; `[Relationship.Field]` uses the *relationship's* name; `[A.B.Field]` multi-hop. Only native fields of the reached object. `#123` stands in for any name by id (`[#123]`, `"#17410"`).
- Milestone/step names are quoted; prefix a relationship path to test a related record: `INMILESTONE("Customer.Active")`.
- Selection options by their text (`[Status] = "Won"`); `BLANK` = the *(Blank)* option (selection fields only; otherwise `ISEMPTY`).

**Values**: `"text"` (double inner quotes), numbers, `TRUE`/`FALSE`, `DATE("2026-01-15")` / `DATE("2026-01-15 14:30")` / `DATE(2026,1,15)`, spans `DAYS WEEKS MONTHS YEARS` (relative-date rules) and `DAYS WEEKS HOURS MINUTES SECONDS TIMESPAN(d,h,m,s)` (time span fields), `CURRENTUSER()`, `?` (folders/reports: user fills it in; stands alone).

**Operators per field kind** (the parser only accepts what the builder offers):
- number / currency: `= != > >= < <=`, `ISANYOF`, `ISNOTANYOF`, `ISEMPTY`, `ISNOTEMPTY`
- time span: `= != > >= < <=`, `ISEMPTY`, `ISNOTEMPTY`
- text / record-driven selection: `= !=`, `ISANYOF`, `ISNOTANYOF`, `CONTAINS`, `NOTCONTAINS`, `ISEMPTY`, `ISNOTEMPTY`
- preset selection / dropdown setting: `= !=`, `ISANYOF`, `ISNOTANYOF` (use `= BLANK` for "not set")
- yes/no: `= !=`
- date: `= != > >= < <=`, `ISANYTIME`, `ISEMPTY`, `BETWEENDATES`, `INTHELAST`, `INTHENEXT`, `MORETHANAGO`, `MORETHANFROMNOW`, `BETWEENRANGE`, periods
- compared with a field / setting / current user: only `= != > >= < <=`, `ISANYOF`, `ISNOTANYOF`, `CONTAINS`, `NOTCONTAINS`

**Functions**
- Lists/text: `ISEMPTY(f)`, `ISNOTEMPTY(f)`, `ISANYOF(f, v, ...)`, `ISNOTANYOF(f, v, ...)`, `CONTAINS(f, "t")`, `NOTCONTAINS(f, "t")`
- Dates: `ISANYTIME(d)`, `BETWEENDATES(d, start, end)`, `INTHELAST(d, DAYS(30))`, `INTHENEXT(d, WEEKS(2))`, `MORETHANAGO(d, MONTHS(6))`, `MORETHANFROMNOW(d, DAYS(7))`, `BETWEENRANGE(d, DAYS(-7), DAYS(7))` (negative = past)
- Periods (one date): `ISTODAY ISYESTERDAY ISTOMORROW ISTHISWEEK ISLASTWEEK ISNEXTWEEK ISTHISMONTH ISLASTMONTH ISNEXTMONTH ISTHISQUARTER ISLASTQUARTER ISNEXTQUARTER ISTHISYEAR ISLASTYEAR ISNEXTYEAR ISWEEKTODATE ISMONTHTODATE ISQUARTERTODATE ISYEARTODATE`
- Settings/user: `SETTING("name")` (boolean, text, number, dropdown, date_time, time_span settings), `CURRENTUSER()`
- Milestones: `INMILESTONE("m", ...)` ≡ `INANYMILESTONE`, `NOTINMILESTONE` ≡ `NOTINANYMILESTONE`, `INALLMILESTONES`, `NOMILESTONES(["Relationship"])`
- Steps: `HASSTEP("s", ...)` ≡ `HASANYSTEP`, `HASALLSTEPS`, `NOTHASSTEP` ≡ `NOTHASANYSTEP`, `NOTHASALLSTEPS` (missing at least one), `NOSTEPS(["Relationship"])`
- Report-only entry dates (a date left side): `MILESTONEFIRSTENTERED("m")`, `MILESTONELASTENTERED("m")`, `STEPFIRSTOCCURRED("s")`, `STEPLASTOCCURRED("s")` — e.g. `INTHELAST(MILESTONEFIRSTENTERED("Won"), DAYS(30))`

`get-filter` prints `UNSUPPORTED("...")` for a stored rule the language cannot express; `set-filter` rejects that text, so leave such filters to PATH's Designer (or rewrite the rule).

## Examples

```
parse-filter --bo-name Job --text '[Department] = "Retail" AND (INMILESTONE("Lead") OR HASSTEP("Job Won"))'
get-filter milestone 17103
set-filter folder 20976 --text '[Department] = ? AND NOTINMILESTONE("Closed")'
set-filter report 2496 --text 'INTHELAST(MILESTONEFIRSTENTERED("Won"), DAYS(30)) AND [Job Outside Salesperson.Person Name] = ?'
```

## Common errors

| Message | Fix |
|---|---|
| `Use parentheses when mixing AND and OR` | Group: `A AND (B OR C)` |
| `Unknown field "X"` | `list-fields`; relationship paths use relationship names; only native fields of the reached BO |
| `"X" is not an option of this field` | Use an option text from `get-field`, or `BLANK` |
| `<OP> can't be used with [X] (a … field)` | Operator not offered for that kind — see the table above |
| `? (fill in later) is only available in folder and report filters` | Put a value, or use the folder/report target |
| `Milestone and step dates are only available in folder and report filters` | Folder/report targets only (`--allow-entry-dates` or `--report` for parse-filter) |
| `The <target> target needs --bo <id>` | Tasks need `--bo`; steps need `--pathway`; folder stickers need `--sticker` |
| `ERROR [422]: ...` from set-filter | The API's `builder/validate` rejected the pipeline; nothing was saved |

Field formulas use a sister syntax that is set in PATH's Designer (or the PATH Designer plugin's `path-expression-syntax` skill, if installed).
