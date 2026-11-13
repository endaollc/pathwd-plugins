# path-logic-formula-syntax

> **Commands:** every command in this skill is a PATH CLI command line: the command name and its flags. On PATHwd each tool runs the commands of one area: a command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`, and each command's page in [the command index](../../pathwd-guide/references/commands.md) gives any command's syntax and the tool that runs it.

Logic Builder filters (pipeline `GroupFilter`s) are edited as **formula text**. This connection is the user app, so it **reads any target** (`get-filter`, `parse-filter`) but **writes only folder, folder-sticker and report filters** (`set-filter`, `create-folder --logic`, `create-report --logic`). Writing every other target (milestone, task, Fork, table-filter, pathway, sticker, visibility rules, logic settings, BO overrides) is Designer work, done by an Admin or Designer in PATH's Designer (or with the PATH Designer plugin's `path-logic-formula-syntax` skill, if it's installed). The CLI parses the text, resolves field / option / relationship / milestone / step / setting names against the business object the filter is rooted on, validates the pipeline with the API exactly as the builder modal does, and saves the saved-shape JSON — you never hand-write filter JSON.

The account and session (which account, its session context) are covered by the [`path-session` guide](../../pathwd-guide/references/session.md).

## Commands

| Command | Purpose |
|---|---|
| `parse-filter --bo-name <bo> --text '<formula>'` | Validate without saving. Prints `formula: <normalized formula>` and `rules: <count>`: read the formula back to see how PATH understood the text. `--report` unlocks entry dates + `?`; `--report-id <id>` also roots on that report's BO and allows its `COLUMN(...)`/`CUSTOMFIELD(...)`; `--allow-blank` + `--allow-entry-dates` match a folder; `--allow-entry-dates` unlocks entry dates alone. `--bo <id>` = `--bo-id`. |
| `get-filter <target> <id> [--bo <id>] [--pathway <id>] [--sticker <id>]` | Print a stored filter as a formula (`(no filter)` when unset). Reads any target. Folder / report: each ticked user filter follows as `user filter "Label": <rule>` (a milestone "currently in" rule ends with ` allowed: "A", "B"`), with the row's `uid:` on the line under it. |
| `set-filter <target> <id> --text '<formula>'` | **Folder, folder-sticker and report targets only** (with Edit on them); any other target is refused as Designer work. Parse against the target's root BO with the target's feature set, have PATH validate it, then save the object with only its filter replaced (the rest of the pipeline — `version`, `business_object_ids`, `sorting`, `quick_search` … — is kept). `--clear` saves the filter as null. `--dry-run` parses + validates and saves nothing: it prints `Dry run — not saved: <target>`, the formula as it would be saved and its `user filter` lines. Folder / report: `--user-filter '<row>'` / `--untick-user-filter '<row>'` tick or untick rules as user filters, with or without `--text` (see **User filters**). |
| `create-folder --bo-name <bo> --folder-name <n> --logic '<formula>' ...` | Create a **new** folder whose `filter_condition` is the formula (parsed with folder features: `?` + entry dates). `--dry-run` prints the request bodies without creating anything; `--logic-raw <json>` is the raw escape hatch. See the [`cli-folders-skill` workflow](workflows/folders.md) for the rest of folder creation. |
| `create-report --bo-name <bo> --name <n> --columns '<columns>' --logic '<formula>' ...` | Create a **new** report whose filter is the formula (report features: `?` + entry dates, and `COLUMN("…")` / `CUSTOMFIELD("…")` for the report's own calculated columns and `--custom-field`s). Validated like the Logic Builder; `--dry-run` prints the body. See the [`cli-reports-skill` workflow](workflows/reports.md) for the rest of report creation. |
| `find-records --bo-name <bo> --logic '<formula>'` (PATH CLI) | Run the formula **now** and list the matching records — an advanced search; nothing is saved. Only what the user sees in the record viewer can be used (hidden fields and relationships are unknown names; a field shown on some records matches only those), entry dates allowed, no `?`, no `COLUMN`/`CUSTOMFIELD`; relationships are followed forward only (from the record that holds the link); archived records are left out unless `--include-archived`. `--catalog` lists what the user can search on with each item's designer context. See the [`cli-find-records` workflow](workflows/find-records.md). |

Invocation: `[--account <id>] <command> ...`. Always single-quote the formula in the command string.

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
| `folder` | folder id | | the folder's BO | entry dates + `?` |
| `folder-sticker` | folder id | `--sticker <folder sticker id>` | the folder's BO | — |
| `report` | report id | | the report's BO | entry dates + `?` |
| `field-visibility` | field id | | the field's BO | — |
| `field-group-visibility` | field group id | | the group's BO | — |
| `relationship-source-visibility` | relationship id | | source BO | — |
| `relationship-target-visibility` | relationship id | | target BO | — |
| `target-field-visibility` | relationship column (target field setting) id | | source BO | — |
| `setting` | logic account setting id (read-only here) | | none — settings only | settings only |
| `bo-show-in-menu` | business object id (`list-business-objects`) | | none — settings only | settings only |

Side effects `set-filter` mirrors from the web forms (printed as `NOTE:` lines): folder and report user-filter ticks move to the rewritten rule that compares the same thing, and a tick whose rule is gone is dropped. A folder sticker's formula saves only on one of the folder's own stickers (an inherited one's is the business object sticker's: Designer work). A new folder sticker: `create-folder-sticker --folder-id <id> --name <n> --icon <icon> --color <c> --logic '<formula>'` (it switches a Standard folder to Custom stickers). Pathway saves re-send the pathway's groups (the API renumbers their order) and leave permissions alone.

### User filters (folders and reports)

A folder's or report's rule can be ticked as a **user filter** (the Logic Builder's "User Filter" checkbox): viewers then see it in the filter bar and can change its value. The formula text doesn't carry the ticks. `get-filter` prints each ticked rule after the formula as `user filter "Label": <rule>` (`user filter: <rule>` when it has no label). When you rewrite the formula, `set-filter` moves each tick to the new rule that compares the same field, milestone, step or column.

Tick or untick rules with `set-filter folder|report <id>`:

- `--user-filter '<row>'` ticks a rule. `--untick-user-filter '<row>'` unticks one. Both can be repeated.
- `<row>` is either the field the rule compares (`[Department]`, `[Job Outside Salesperson.Person Name]`), when only one rule compares that field, or the whole rule (`ISNOTANYOF([Department], "Trade", "Retail")`). Spacing doesn't matter.
- Put `"Label": ` in front to give the filter the name viewers see. Without a label, the rule keeps the label it has. `"": ` removes the label. A `user filter …` line copied from `get-filter` works as it is.
- Without `--text`, the stored rules are kept and only the ticks change. With `--text`, the ticks apply to the new formula. Can't be used with `--clear`.
- These rules can be ticked: a field compared with values (including `?`), a `CURRENTUSER()` rule on a **text** field (viewers see a text filter that starts with their own name), a `MILESTONEFIRSTENTERED` / `MILESTONELASTENTERED` / `STEPFIRSTOCCURRED` / `STEPLASTOCCURRED` date rule, a milestone "currently in" rule (`INMILESTONE`, `NOTINMILESTONE`, `INANYMILESTONE`, `NOTINANYMILESTONE`, `INALLMILESTONES`), and a report's `COLUMN(...)` / `CUSTOMFIELD(...)` rule. A rule that compares two fields, or a field with a setting, is refused. So are `NOMILESTONES` / `HASSTEP` rules and `CURRENTUSER()` on a pick list, number or date field.
- A milestone "currently in" user filter keeps its rule (viewers can't switch "currently in" to "not currently in"); viewers pick milestones from its **allowed list** — one for `INMILESTONE` / `NOTINMILESTONE`, one or more for the others. Add ` allowed: "Lead", "Quoted", "Won"` after the rule to set the list (milestone names on the rule's business object, `"Relationship.Name"` or plain; it must include the rule's own milestone(s), which viewers start with). Without it, a stored list is kept, else the list is the rule's own milestone(s). `get-filter` prints the list the same way. An unknown name, a list without the rule's own milestone(s), or `allowed:` on another kind of rule is refused. If `--text` moves a ticked rule to a milestone that isn't on its list, nothing is saved until you tick it again with a list that has it. Unticking removes the list.
- Running with user filters: `run-report --user-filter` / `list-records --folder-id <id> --user-filter` take `'[{"label":"Stage","values":["Lead","Qualified"]}]'` (names or ids from the allowed list; `[]` = the rule isn't applied).
- If any row matches no rule (or a field matches more than one), is refused, or is both ticked and unticked, nothing is saved. The error names each of these rows and lists the rules in the criteria.
- The folder or report shows its user filter bar (`show_user_filter`) whenever at least one rule is ticked.

```
# tick an existing Current User rule, labelled "Salesperson"
set-filter report 2497 --user-filter '"Salesperson": [Job Outside Salesperson.Person Name]'
# replace split per-value user filters with one ISNOTANYOF user filter
set-filter report 6932 --text 'ISNOTANYOF([Department], "Trade", "Retail")' --user-filter '"Department": ISNOTANYOF([Department], "Trade", "Retail")'
# untick it again
set-filter report 6932 --untick-user-filter '[Department]'
# milestone "currently in" user filter: viewers pick from Lead / Quoted / Won (one at a time)
set-filter folder 512 --user-filter '"Stage": INMILESTONE("Lead") allowed: "Lead", "Qualified", "Quoting"'
# run it with a pick
list-records --folder-id 512 --user-filter '[{"label":"Stage","values":["Qualified"]}]'
```

## Workflow

**Existing object:**
1. Find the object: `list-folders`, `list-reports` (to read others: `list-workflow-milestones`, `list-workflow-tasks`, `pathway-run available`, `list-fields`, `list-field-groups`, `list-relationships`).
2. `get-filter <target> <id>` to see the current rules.
3. Draft the new formula; check it with `parse-filter --bo-id <root BO>` (add `--report` for reports, `--allow-blank --allow-entry-dates` for folders).
4. For a folder, folder-sticker or report: `set-filter <target> <id> --text '...'` (try `--dry-run` first), then `get-filter` to confirm. For any other target, stop at step 3 and give the user the formula to set in PATH's Designer (or with the PATH Designer plugin, if installed).

**Logic account settings and BO Show in Menu overrides** are read here with `get-filter setting <id>` / `get-filter bo-show-in-menu <bo id>`; writing them (`create-setting --type logic`, `set-filter setting`, `set-filter bo-show-in-menu`) is Designer work (PATH's Designer, or the PATH Designer plugin if installed).

**New folder:**
1. Resolve the root BO (`list-business-objects`), then draft and check the formula the same way (`parse-filter`, `--allow-blank --allow-entry-dates` for a folder).
2. `create-folder --bo-name <bo> --folder-name <n> --logic '...' ...` (try `--dry-run` first), then `list-folders` or `get-filter` to confirm.

## Syntax reference

**Rules** are joined by `AND` or `OR` — one connective per level; mixing needs parentheses: `A AND (B OR C)`. Each `( … )` is a builder group.

**There is no `NOT(...)`.** `NOT(...)` and `NOT ...` fail with `Unknown function "NOT"`, `!` with `Unexpected character "!"`, and `HASSTEP(...) = FALSE` with `functions aren't compared to TRUE/FALSE`; each message names the negative form. Negate each rule with its own negative form:

| Instead of | Write |
|---|---|
| `NOT([f] = v)` | `[f] != v` (`<>` accepted) |
| `NOT(ISANYOF(f, a, b))` | `ISNOTANYOF(f, a, b)` |
| `NOT(CONTAINS(f, "t"))` | `NOTCONTAINS(f, "t")` |
| `NOT(ISEMPTY(f))` | `ISNOTEMPTY(f)`; a date: `ISANYTIME(d)`; a preset selection: `[f] != BLANK` |
| `NOT(INMILESTONE("a", "b"))` | `NOTINMILESTONE("a", "b")` (in none of them) |
| `NOT(INALLMILESTONES("a", "b"))` | `NOTINMILESTONE("a") OR NOTINMILESTONE("b")` |
| `NOT(HASSTEP("a", "b"))` | `NOTHASSTEP("a", "b")` (has none of them) |
| `NOT(HASALLSTEPS("a", "b"))` | `NOTHASALLSTEPS("a", "b")` (missing at least one) |
| `NOT(INTHELAST(d, DAYS(30)))` | `MORETHANAGO(d, DAYS(30)) OR ISEMPTY(d)` |
| `NOT(BETWEENDATES(d, s, e))` | `d < s OR d > e` |
| `NOT(A AND B)` / `NOT(A OR B)` | `(not A) OR (not B)` / `(not A) AND (not B)`, each rule negated as above |

**Negatives and empty values.** A negative rule on the record's own field (`!=`, `ISNOTANYOF`, `NOTCONTAINS`) matches records where the field is empty (never set or cleared): `[Department] != "Retail"` includes jobs with no Department. To leave the empty ones out, say so: `AND [Department] != BLANK` (a pick list), `AND ISNOTEMPTY([Field])` (other fields). Through a relationship, a record with no linked record does not match: add `OR ISEMPTY([Rel.ID field])` to keep it. A comparison (`MORETHANAGO` for "not recently", `<`, `>`) never matches an empty value: `MORETHANAGO(d, DAYS(30)) OR ISEMPTY(d)`. The [`path-cli` guide](../../pathwd-guide/references/commands.md)'s [find-records-syntax.md](find-records-syntax.md) ("Empty values") has the details.

A rule with no negative form (a period like `ISTHISMONTH`) is negated with the opposite comparison (`[d] < DATE("2026-11-01") OR [d] >= DATE("2026-12-01")`); if none fits, tell the user it can't be expressed rather than inventing a function. Example, "jobs created in the last 30 days with no quote or contract sent":

```
INTHELAST([Created Date], DAYS(30)) AND NOTHASSTEP("Quick Quote Sent to Customer", "Full Quote Sent to Customer", "Final Contract Sent to Customer", "Quick Contract Sent to Customer")
```

Not sure a function or form exists? For a search, run it with `find-records … --count` (the real search, by this user's names). For a folder or report filter, `set-filter … --dry-run` validates against the target without saving; `parse-filter` only checks the syntax.

**Comparisons**: `[Field] = value`, `!=` (`<>` accepted), `> >= < <=`. Right side: a literal, another field (`[Total Sell With Tax] > [Job Main Customer.Credit Limit]`, a relationship name then a field), `SETTING("name")`, or `CURRENTUSER()` (compared by full name, e.g. `[Salesperson.Person Name] = CURRENTUSER()`). Several fields on the right of one rule must all be on the same record: `ISANYOF([Owner], [Customer.Rep], [Customer.Manager])` works; `ISANYOF([Owner], [Rep], [Customer.Manager])` doesn't.

**Names**
- `[Field]`; `[Relationship.Field]` reaches any field on the linked record and always works; it uses the *relationship's* name (`[Job Main Customer.Customer Name]`, not `[Customer.Customer Name]`); `[A.B.Field]` multi-hop, one pair of brackets. A **relationship field** of the business object (a field it shows from a linked record) is also named by its own name: `[Customer Type]` = `[Job Main Customer.Customer Type]`, through exactly the relationship it is shown through; without one, use `[Relationship.Field]`. A name shared by the record's own field and a relationship field (or two relationship fields) is refused with how to write each (`[Rel.Field]` or `[#id]`). A relationship alone is not a field. Relationships are followed only from the record that holds the link. `#123` stands in for any name by id (`[#123]`, `"#17410"`).
- Milestone/step names are quoted; prefix a relationship path to test a related record: `INMILESTONE("Job Main Customer.Customer")` (relationship name, then the milestone).
- Selection options by their text (`[Department] = "Retail"`); `BLANK` = the *(Blank)* option (selection fields only; otherwise `ISEMPTY`). `CURRENTUSER()` can be one of the options in a selection field's `ISANYOF` / `ISNOTANYOF` list: `ISANYOF([Assigned To], CURRENTUSER(), "Unassigned")`.

**Values**: `"text"` in double quotes (a `"` inside is `""`; no backslash escapes; `""` alone is no value and is refused in new text: use `ISEMPTY([F])` / `ISNOTEMPTY([F])`), numbers without `$`, commas or `%` (a percentage as shown: `[Deposit Percentage] = 50`), `TRUE`/`FALSE`, `DATE("2026-01-15")` / `DATE("2026-01-15 14:30")` / `DATE(2026,1,15)` / `DATE(2026,1,15,14,30)` (3–6 numbers: year, month, day, then hour, minute, second; read on the account's clock; `=` matches the whole day), spans `DAYS WEEKS MONTHS YEARS` (relative-date rules) and `DAYS WEEKS HOURS MINUTES SECONDS TIMESPAN(d,h,m,s)` (time span fields), `CURRENTUSER()`, `?` (folders/reports: user fills it in; stands alone).

**Operators per field kind** (the parser only accepts what the builder offers). The field's type (`type` in the `find-records --catalog`, the `type` column of `list-fields`) decides its kind:
- number: `number`, `currency`, `percent`, `auto_number`, `calculation`, `aggregate_number`, `aggregate_count`, `id`
- time span: `time_span`, `aggregate_time_span`, `time_span_expression`
- date: `date_time`, `appointment` (an appointment compares on its start date and time)
- yes/no: `boolean`
- selection: `selection` — *preset* when it has its own option list, otherwise *record-driven* (its options come from records, such as users)
- text: every other type (text, email, phone number, address, tag, …)

- number / currency: `= != > >= < <=`, `ISANYOF`, `ISNOTANYOF`, `ISEMPTY`, `ISNOTEMPTY`
- time span: `= != > >= < <=`, `ISEMPTY`, `ISNOTEMPTY`
- text / record-driven selection: `= !=`, `ISANYOF`, `ISNOTANYOF`, `CONTAINS`, `NOTCONTAINS`, `ISEMPTY`, `ISNOTEMPTY`
- preset selection: `= !=`, `ISANYOF`, `ISNOTANYOF` (`= BLANK` = no option chosen, `!= BLANK` = one is)
- dropdown setting: `= !=`, `ISANYOF`, `ISNOTANYOF`; values are the setting's own options (no `BLANK`)
- yes/no: `= !=`
- date: `= != > >= < <=`, `ISANYTIME`, `ISEMPTY`, `BETWEENDATES`, `INTHELAST`, `INTHENEXT`, `MORETHANAGO`, `MORETHANFROMNOW`, `BETWEENRANGE`, periods. There is no `ISNOTEMPTY` for dates: "has a date" is `ISANYTIME([d])`
- compared with a field / setting / current user: only `= != > >= < <=`, `ISANYOF`, `ISNOTANYOF`, `CONTAINS`, `NOTCONTAINS`

**Functions**
- Lists/text: `ISEMPTY(f)`, `ISNOTEMPTY(f)` (not dates: `ISANYTIME(d)`), `ISANYOF(f, v, ...)`, `ISNOTANYOF(f, v, ...)`, `CONTAINS(f, "t")`, `NOTCONTAINS(f, "t")`
- Dates: `ISANYTIME(d)`, `BETWEENDATES(d, start, end)`, `INTHELAST(d, DAYS(30))`, `INTHENEXT(d, WEEKS(2))`, `MORETHANAGO(d, MONTHS(6))`, `MORETHANFROMNOW(d, DAYS(7))`, `BETWEENRANGE(d, DAYS(-7), DAYS(7))` (negative = past)
- Periods (one date): `ISTODAY ISYESTERDAY ISTOMORROW ISTHISWEEK ISLASTWEEK ISNEXTWEEK ISTHISMONTH ISLASTMONTH ISNEXTMONTH ISTHISQUARTER ISLASTQUARTER ISNEXTQUARTER ISTHISYEAR ISLASTYEAR ISNEXTYEAR ISWEEKTODATE ISMONTHTODATE ISQUARTERTODATE ISYEARTODATE`
- Settings/user: `SETTING("name")` (boolean, text, number, dropdown, date_time, time_span settings), `CURRENTUSER()`
- Milestones: `INMILESTONE("m", ...)` ≡ `INANYMILESTONE`, `NOTINMILESTONE` ≡ `NOTINANYMILESTONE`, `INALLMILESTONES`, `NOMILESTONES(["Relationship"])`
- Steps: `HASSTEP("s", ...)` ≡ `HASANYSTEP`, `HASALLSTEPS`, `NOTHASSTEP` ≡ `NOTHASANYSTEP`, `NOTHASALLSTEPS` (missing at least one), `NOSTEPS(["Relationship"])`
- Entry dates (folders, reports and `find-records`; a date left side): `MILESTONEFIRSTENTERED("m")`, `MILESTONELASTENTERED("m")`, `STEPFIRSTOCCURRED("s")`, `STEPLASTOCCURRED("s")` — e.g. `INTHELAST(MILESTONEFIRSTENTERED("Quoting"), DAYS(30))`. They compare with dates only (`DATE(...)`, spans, periods), not with fields, settings or `CURRENTUSER()`
- Report-only computed values (a left side compared with values only): `COLUMN("name")` = one of the report's Time Between / Custom Calculation columns, `CUSTOMFIELD("name")` = a filter-only Custom Field (`create-custom-field`). Each compares like a field of its result type — time spans with `DAYS()`/`HOURS()`… (`COLUMN("Days Open") > DAYS(3)`), numbers, dates (`INTHELAST(CUSTOMFIELD("Last Contact"), DAYS(30))`), booleans, text. Evaluated per detail record before grouping (groups with no matching records disappear). Needs the report's columns: `parse-filter --report-id <id>`, `get-filter`/`set-filter report <id>`.

`get-filter` prints `UNSUPPORTED("...")` for a stored rule the language cannot express; `set-filter` rejects that text, so leave such filters to PATH's Designer (or rewrite the rule).

## Examples

```
parse-filter --bo-name Job --text '[Department] = "Retail" AND (INMILESTONE("Lead") OR HASSTEP("Job Won"))'
get-filter milestone 17103
set-filter folder 20976 --text '[Department] = ? AND NOTINMILESTONE("Closed")'
set-filter report 2496 --text 'INTHELAST(MILESTONEFIRSTENTERED("Quoting"), DAYS(30)) AND [Job Outside Salesperson.Person Name] = ?'
```

## Common errors

| Message | Fix |
|---|---|
| `Use parentheses when mixing AND and OR` | Group: `A AND (B OR C)` |
| `Unknown function "NOT"` (or `NOTISANYOF`, `NOTINTHELAST` …) | No `NOT`: use the rule's negative form from the table above (`NOTHASSTEP`, `NOTINMILESTONE`, `ISNOTANYOF`, `!=` …) |
| `Unknown field "X"` | A relationship used as a field (name a field on it), a path by business object name instead of relationship name, a reverse hop, or a typo. For a search, the names are those `find-records --catalog` lists; for a folder or report, those of `get-business-object` / `list-relationships` |
| `"X" is not an option of this field` | Use an option's text (`find-records --catalog` lists them; `get-field` for a folder or report), or `BLANK` |
| `<OP> can't be used with [X] (a … field)` | Operator not offered for that kind — see the table above |
| `? (fill in later) is only available in folder and report filters` | Put a value, or use the folder/report target |
| `Milestone and step dates are only available in folder and report filters` | Folder and report targets and `find-records` only (`--allow-entry-dates` or `--report` for parse-filter) |
| `Milestone and step dates can only be compared with dates` | Compare an entry date with `DATE(...)`, a span or a period, not a field |
| `Every field compared in one rule must be on the same record` | Split it into one rule per record, joined with `OR` |
| `The <target> target needs --bo <id>` | Tasks need `--bo`; steps need `--pathway`; folder stickers need `--sticker` |
| `ERROR [422]: ...` from set-filter | The API's `builder/validate` rejected the pipeline; nothing was saved |

Field formulas use a sister syntax that is set in PATH's Designer (or the PATH Designer plugin's `path-expression-syntax` skill, if installed).
