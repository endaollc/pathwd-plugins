---
name: find-records
description: Use this skill when the user wants to find records matching conditions they describe in plain language — an advanced search, run now, nothing saved. E.g. "Show me all the jobs created last week where a quote was sent" or "Which retail jobs are waiting on a deposit?" Reads what the user can search on (with designer info), turns the request into a Logic Builder formula, runs find-records and shows the records; can save it as a folder or report.

Examples:
- User: "Show me all the jobs created last week where a quote was sent"
  Assistant: Uses the find-records skill — loads the Job catalog, picks the quote-sent steps from their info, runs ISLASTWEEK([Created Date]) AND HASSTEP(...), shows the jobs.

- User: "Any purchase orders still open from more than 30 days ago?"
  Assistant: Uses the find-records skill to search Purchase Orders by milestone and created date.
model: sonnet
color: blue
---

> **How to run commands:** every command in this command is a PATH CLI command string. Run it by calling the `path_cli` tool with the string as `command`, for example `path_cli(command: "list-business-objects")`. Never run these commands in a terminal. If the user isn't signed in yet, run the `path-session` skill first.

You run **advanced searches** for a PATH user: they describe the records they want in their own words; you turn that into a **Logic Builder formula**, run it with `find-records`, and show the results. Nothing is saved, so no confirmation is needed to run a search.

The user can only search what they could see in PATH's record viewer: hidden fields, fields in field groups hidden from them and hidden relationships don't exist for `find-records`, and a field whose visibility depends on the record only matches records that show it. Never work around this (no `api`, `list-fields` or `search` detours to reach a hidden field) — if what they ask for isn't in their catalog, tell them it isn't available to them.

All formula syntax (comparisons, `AND`/`OR`, functions, operators per field type, periods, entry dates) lives in the `path-logic-formula-syntax` skill — load it, don't guess syntax.

## Workflow

### 1. Pick the business object

The records being listed ("show me **jobs** …") are the business object. `list-business-objects` if unsure which one; ask if the words fit two.

### 2. Load everything you can search on — with its info

```
find-records --bo-name <BO> --catalog
```

The catalog lists, for this user only: the business object, its field groups, its fields (type, selection options, which are shown on some records only), the relationships a formula can follow, its milestones and its steps — **each with its designer info**. Read all of it: the info is how the designers explain what each field, milestone and step means and when it happens (for example, which step marks "a quote was sent"). Earlier-shown info is left out with an `info_elided` flag; it is still in your context — pass `--with-info` if your context was compacted.

When the request involves related records ("where the customer is in Austin", "whose quote was signed"), load the related business object's catalog too — `find-records --bo-name <related BO> --catalog` — its fields, milestones and steps are reached as `[Relationship.Field]`, `INMILESTONE("Relationship.Milestone")`, `HASSTEP("Relationship.Step")`. Relationships are followed from the record that holds the link only (the catalog lists those).

Don't use `list-fields` / `list-relationships` for this: they list fields that may be hidden from the user.

### 3. Translate the request

Map each phrase onto catalog names, using the info to choose:

| The user says | Usually |
|---|---|
| a status — "open", "waiting on a deposit", "won" | a milestone: `INMILESTONE("…")` |
| something happened — "a quote was sent", "was scheduled" | a step: `HASSTEP("…")`; several steps that all mean it: `HASSTEP("A", "B")` (any of them) |
| when it happened — "sent last week" | the step's/milestone's date: `ISLASTWEEK(STEPLASTOCCURRED("…"))`, `INTHELAST(MILESTONEFIRSTENTERED("…"), DAYS(30))` |
| "created last week" / "this month" / "this year" | a calendar period on the date field: `ISLASTWEEK([Created Date])`, `ISTHISMONTH(...)` |
| "in the last 7 days" / "older than 30 days" | `INTHELAST([d], DAYS(7))`, `MORETHANAGO([d], DAYS(30))` |
| a field value — "retail", "over $5,000" | the field and an option's exact text from the catalog: `[Department] = "Retail"`, `[Total] > 5000` |
| a related record's value | `[Relationship.Field] = …` |

Keep to what the user asked; don't add conditions. When two readings remain after the info (a Job step vs. a related Quote's milestone, say), pick the closest, say which you used, and offer the other — ask first only when the readings would give clearly different answers.

### 4. Run it

```
find-records --bo-name <BO> --logic '<formula>'
```

It prints `<total> <records> match: <formula>` (the formula as PATH reads it back), then the records: id, name, current milestones and the fields the formula uses. Options:
- `--columns "Field, Relationship"` — what to show (fields of the business object, or relationships to show the linked record)
- `--sort "Created Date desc"` — sort by a field every record shows
- `--include-archived` — only when the user asks for archived / closed-out records too (left out by default)
- `--count` — just the number; `--per-page <n>` / `--page <n>` / `--all` for paging

A parse error names the bad piece; fix it from the catalog and run again. `Unknown field` means the field isn't in the user's catalog — it may be hidden from them; say so rather than looking for another way in.

### 4b. Questions about groups — "which … the most", "how many per …", "total … by …"

When the answer is a count or total per something rather than a list of records, group the matches instead of listing them:

```
find-records --bo-name "Job Items" --logic 'INTHELAST([JI Job.Job Won Date], DAYS(90))' \
  --group-by "Vendor" --sum "Qty" --top 10
```

It runs on the server as a grouped report does, saving nothing, and prints one row per group: the group values, `Count` (records in the group) and each total, named like `Qty (total)`. Options:
- `--group-by "<field>, …"` — up to 3; `[Relationship.Field]` for a linked record's field; `"<date field> by month"` (day, week, month, quarter, year); `milestones` for the current milestones
- `--sum` / `--avg` / `--min` / `--max "<field>, …"` — number and currency fields
- `--sort "<column> [desc]"` — `Count`, a total (`Qty` or `Qty (total)`) or a group field; by default the first total, else Count, largest first
- `--top <n>` — just the first n groups

Pick the measure that answers the question ("sold the most" — quantity or sell? say which you used) and check the totals aren't all zero or blank before trusting them; if they are, the value likely lives on another business object (e.g. quote items rather than job items). Only fields and relationships every record shows the user can be grouped or totaled — one shown on some records only is refused; say so.

### 5. Show the results

Restate the search in plain language, then the formula, the count, and the records (names, key columns). For long lists show the first page and offer the rest. Follow-ups ("only the retail ones", "sort by newest") re-run the search with the adjusted formula.

### 6. Offer to save it

If they'll want this again, offer a **folder** (`cli-folders-skill`: `create-folder --logic '<same formula>'`) or a **report** (`cli-reports-skill`). A grouped search saves as a grouped report with the same columns — the group-by fields as Group columns, `count`, and each total as a total / average / minimum / maximum column — previewed with `create-report --preview` before it is saved. Saving is a change — confirm first, as those skills do.

## Important rules

1. **Use the path_cli tool** for every command.
2. **Only the user's own view** — never search on, mention or reach for fields hidden from them.
3. **Read the info** in the catalog before translating; it is the designers' meaning of each name.
4. **Resolve names from the catalog** — never guess field, milestone or step names, or ids.
5. **Plain language first**; the formula is there so the user can check it.
