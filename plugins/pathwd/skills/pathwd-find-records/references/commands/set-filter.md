# set-filter

Runs with: path_manage_folders_reports(command: "set-filter", args: "<flags>") (change).

```
Usage: set-filter [options] <target> <id>

Replace a stored Logic Builder filter from formula text. Parses against the
business object the filter is rooted on, validates the pipeline with the API
(as the Logic Builder does), then saves the object with only its filter
changed. The PATH CLI changes folder, folder-sticker and report filters (with
Edit on them); the other targets are Designer work for the PATH Designer CLI.

Arguments:
  target                      Where the filter lives (see Targets below)
  id                          Id of the object holding the filter

Options:
  --text <formula>            Formula text, e.g. '[Department] = "Retail" AND
                              HASSTEP("Job Won")'
  --clear                     Remove the filter (saved as null, as the builder
                              does when emptied)
  --bo <id>                   Business object ID (task targets)
  --pathway <id>              Pathway ID (fork, table-filter)
  --sticker <id>              Folder sticker ID (folder-sticker)
  --user-filter <row>         folder / report: tick the row's User Filter
                              checkbox (repeatable). <row> = [Field] /
                              [Relationship.Field] when one rule compares it,
                              or the whole rule, e.g. 'ISNOTANYOF([Job Type],
                              "A", "B")'; prefix '"Label": ' to name it for
                              viewers (a get-filter "user filter" line is taken
                              as is). A milestone "currently in" rule
                              (INMILESTONE, NOTINMILESTONE, INANYMILESTONE,
                              NOTINANYMILESTONE, INALLMILESTONES) saves the
                              milestones viewers may pick: add ' allowed:
                              "Lead", "Won"' (names on the rule's business
                              object; must include the rule's own), else the
                              stored list is kept or starts as the rule's own
                              milestone(s). Without --text the stored rules are
                              kept
  --untick-user-filter <row>  folder / report: untick the row's User Filter
                              checkbox (repeatable; <row> as for --user-filter,
                              without allowed:); also removes a milestone row's
                              allowed list
  --dry-run                   Parse and validate, print the formula as it would
                              be saved, but do not save
```

## Examples

- args: `folder 88 --text '[Department] = "Residential"'`

## Notes

Replaces the criteria of a folder, a folder's own sticker or a report from a Logic Builder formula, and ticks or unticks user filters (criteria rows the viewer can change). Only the filter changes; the object is otherwise saved as stored. Every other filter (milestones, tasks, Fork steps, visibility rules, business object stickers) is Designer work.

**Examples**

```
set-filter folder 32519 --text '[Department] = "Retail" AND INMILESTONE("Ordering")' --dry-run
set-filter report 3830 --text 'INTHELAST([Created Date], DAYS(30)) AND [Department] = ?' --user-filter '"Department": [Department]'
set-filter folder 32519 --user-filter '"Milestone": INMILESTONE("Lead") allowed: "Lead", "Quoting", "Full Quote Out", "Ordering"'
set-filter report 7328 --untick-user-filter 'NOTINMILESTONE("Paid In Full")'
set-filter folder-sticker 21110 --sticker 3395 --text '[Department] = "Trade"'
set-filter report 3830 --clear
```

- Targets: `folder <folder id>`, `report <report id>`, `folder-sticker <folder id> --sticker <folder sticker id>` (ids from `list-folders`, `list-reports`). An inherited business object sticker's formula is Designer work.
- `--text` replaces the whole formula: read the current one first with `get-filter <target> <id>` and edit it. Folders and reports take `?` (the viewer fills it in) and milestone/step entry dates; reports also `COLUMN("…")` and `CUSTOMFIELD("…")` for their own computed columns and Custom Fields. Sticker formulas take neither. Syntax: the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md) and the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [find-records-syntax.md](../find-records-syntax.md).
- `--dry-run` parses and validates against the target, as the Logic Builder does, and saves nothing; it prints `Dry run — not saved: <target>`, the formula as it would be saved and its `user filter` lines.
- **User filters:** `--user-filter <row>` (repeatable) ticks a row: `[Field]` when one rule compares it, or the whole rule; `"Label": ` in front names it for viewers. A `get-filter` "user filter" line is taken as is. A milestone "currently in" rule (`INMILESTONE`, `NOTINMILESTONE`, `INANYMILESTONE`, `NOTINANYMILESTONE`, `INALLMILESTONES`) lists the milestones viewers may pick with ` allowed: "A", "B"` (it must include the rule's own). Without `--text` the stored rules stay; with it, ticks carry over to identical rows. Which rules can be ticked: the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md).
- `--clear` removes the whole filter (and its user filters).
- Needs Edit on the folder or report (owner, Admins, folder / report admins).

**Output**

```
Updated report 2477 "My Projects Pipeline" (BO 5048)
formula: ISANYTIME([Created Date]) AND [Project Outside Salesperson.Person Name] = CURRENTUSER()
user filter "Created Date": ISANYTIME([Created Date])
```

- The target, the formula as saved (`(no filter)` after `--clear`) and each `user filter "Label": <rule>` line.
- `NOTE:` lines say what changed on the side, e.g. `NOTE: user filters no longer in the criteria are dropped: Department`.

**Common errors**

- `<user> can't change report 3830 "Jobs Test Report" (BO 5050) — they don't have Edit permission on report "Jobs Test Report", as in the web app.` (a folder: `… on folder 32519`).
- `Report "Jobs Test Report" is inherited from the parent account and can't be changed here. …`: a child account's report that comes from its parent account; nothing is sent. In the web app its "Duplicate to my account" action copies it into this account (a folder: `Folder … is inherited from the parent account …`).
- A parse error naming an unknown field, milestone or function; `? (fill in later) is only available in folder and report filters`.
- `The formula is empty — use --clear to remove the filter.`
- `User filters not changed (nothing saved):` then the problem and the criteria's rules with `[user filter]` marks: the row named matches no rule, or can't be a user filter.
- `Sticker 2736 "High Priority" on folder 21110 is inherited from business object sticker 443; …` / `Folder 32519's stickers are Standard (inherited only); …`.
- `… is Designer work: change it with the PATH Designer CLI (set-filter there).`: a target other than these three.

**Related:** `get-filter`, `parse-filter`, `find-records --count` (try the formula), `create-folder`, `create-report`, `create-folder-sticker`, `run-report --user-filter`, `list-records --user-filter`.

Related commands: [`create-folder`](create-folder.md), [`update-folder`](update-folder.md), [`create-report`](create-report.md), [`update-report`](update-report.md), [`create-folder-sticker`](create-folder-sticker.md), [`update-folder-sticker`](update-folder-sticker.md), [`hide-folder-sticker`](hide-folder-sticker.md), [`show-folder-sticker`](show-folder-sticker.md), [`create-custom-field`](create-custom-field.md), [`update-custom-field`](update-custom-field.md), [`delete-folder-sticker`](delete-folder-sticker.md), [`reset-folder-stickers`](reset-folder-stickers.md), [`delete-custom-field`](delete-custom-field.md).
