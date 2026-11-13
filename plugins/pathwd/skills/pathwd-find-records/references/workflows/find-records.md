# Workflow: find-records

> **Commands:** every command in this command is a PATH CLI command line: the command name and its flags. On PATH WD Remote MCP each tool runs the commands of one area: a command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`, and each command's page in [the command index](../../../pathwd-guide/references/commands.md) gives any command's syntax and the tool that runs it.

You run **advanced searches** for a PATH user: they describe the records they want in their own words; you turn that into a **Logic Builder formula**, run it with `find-records`, and show the results. Nothing is saved, so no confirmation is needed to run a search.

The user can only search what they could see in PATH's record viewer: hidden fields, fields in field groups hidden from them and hidden relationships don't exist for `find-records`, and a field whose visibility depends on the record only matches records that show it. Never work around this (no `list-fields` or `search` detours to reach a hidden field) — if what they ask for isn't in their catalog, tell them it isn't available to them.

The formula language for a search — names, operators per field type, every function, how negatives treat empty values, tested recipes and the common mistakes with their fixes — is in the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [find-records-syntax.md](../find-records-syntax.md). Saved folder and report filters (`?` blanks, user filters, report columns) are in the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md).

## Workflow

### 1. Pick the business object

The records being listed ("show me **jobs** …") are the business object. `list-business-objects` if unsure which one; ask if the words fit two.

### 2. Load everything you can search on — with its context

```
find-records --bo-name <BO> --catalog
```

The catalog lists, for this user only: the business object, its field groups, its fields (type, selection options, which are shown on some records only), the relationships a formula can follow, its milestones and its steps — **each with its designer context**. Read all of it: the context is how the designers explain what each field, milestone and step means and when it happens (for example, which step marks "a quote was sent"). Earlier-shown context is left out (`(shown earlier this session)`); it is still in your context — pass `--with-context` if your context was compacted.

When the request involves related records ("where the customer is in Austin", "whose quote was signed"), load the related business object's catalog too — `find-records --bo-name <related BO> --catalog` — its fields, milestones and steps are reached as `[Relationship.Field]`, `INMILESTONE("Relationship.Milestone")`, `HASSTEP("Relationship.Step")`. Relationships are followed from the record that holds the link only (the catalog lists those).

The catalog is the list of names a search can use; `list-fields` / `list-relationships` describe the business object for building records, not what this user can search on.

### 3. Translate the request

Map each phrase onto catalog names, using the context to choose:

| The user says | Usually |
|---|---|
| a status — "open", "waiting on a deposit", "won" | a milestone: `INMILESTONE("…")` |
| something happened — "a quote was sent", "was scheduled" | a step: `HASSTEP("…")`; several steps that all mean it: `HASSTEP("A", "B")` (any of them) |
| something did **not** happen / is **not** in a status — "no quote sent yet", "not closed" | the negative form, never `NOT(...)`: `NOTHASSTEP("A", "B")` (has none of them), `NOTINMILESTONE("…")`; for a field `!=`, `ISNOTANYOF(...)`, `NOTCONTAINS(...)` |
| when it happened — "sent last week" | the step's/milestone's date: `ISLASTWEEK(STEPLASTOCCURRED("…"))`, `INTHELAST(MILESTONEFIRSTENTERED("…"), DAYS(30))` |
| "created last week" / "this month" / "this year" | a calendar period on the date field: `ISLASTWEEK([Created Date])`, `ISTHISMONTH(...)` |
| "in the last 7 days" / "older than 30 days" | `INTHELAST([d], DAYS(7))`, `MORETHANAGO([d], DAYS(30))` |
| a field value — "retail", "over $5,000", "deposit over 50%" | the field and an option's text from the catalog: `[Department] = "Retail"`; numbers without `$`, commas or `%`: `[Estimated Sell Price] > 5000`; a percentage as shown: `[Deposit Percentage] > 50` |
| a related record's value | `[Relationship.Field] = …`, by the relationship's name in the catalog (`[Job Main Customer.Customer Name]`); a relationship alone isn't a field |
| has / has no linked record | a field the linked record always has: `ISNOTEMPTY([Job Main Customer.CUST_ID])` / `ISEMPTY(...)` |
| "not X" | the negative: `[Department] != "Retail"`, `ISNOTANYOF(...)`, `NOTCONTAINS(...)` match records whose field is empty too (never set or cleared); to leave the empty ones out add `AND [Department] != BLANK` (a pick list) or `AND ISNOTEMPTY([F])`. Through a relationship a record with no linked record never matches: add `OR ISEMPTY([Rel.ID field])` to keep it |
| every record | `ISANYTIME([Created Date])` |
| "my jobs", "assigned to me", "I'm the salesperson" | the field or relationship that names the person (pick it by its context), compared with `CURRENTUSER()`: `[Job Outside Salesperson.Person Name] = CURRENTUSER()` (by full name), or as one of a selection's options: `ISANYOF([Assigned To], CURRENTUSER())` |
| "has a … date" / "no … date yet" | `ISANYTIME([d])` / `ISEMPTY([d])` (dates have no `ISNOTEMPTY`) |

Every value goes in the formula: `?` (fill in later) is for saved folders and reports, not a search. Keep to what the user asked; don't add conditions. When two readings remain after the context (a Job step vs. a related Quote's milestone, say), pick the closest, say which you used, and offer the other — ask first only when the readings would give clearly different answers.

### 4. Run it

```
find-records --bo-name <BO> --logic '<formula>'
```

It prints `<total> <records> match: <formula>` (the formula as PATH reads it back), then the records: id, name, current milestones and the fields the formula uses. Options:
- `--columns "Field, Relationship"` — what to show: fields of the business object, or a relationship name to show the linked record (`Relationship.Field` isn't accepted here; `--group-by` takes it)
- `--sort "Created Date desc"` — one field of the business object itself that every record shows (no lists, no linked fields)
- `--include-archived` — only when the user asks for archived / closed-out records too (left out by default)
- `--count` — just the number; `--per-page <n>` / `--page <n>` / `--all` for paging

A parse error names the bad piece and points at it; fix it and run again (the syntax reference's "Common mistakes" table gives the fix for each message). `Unknown function "NOT"`: there is no `NOT(...)`; use the rule's negative form. `Unknown field`: a relationship used as a field (name a field on it), a path by business object name instead of relationship name, a reverse hop, or a spelling that isn't in the catalog: pick the catalog's name. A name the catalog doesn't list can't be searched on; say so plainly. To check a formula before showing results, run it with `--count`: that is the real search.

### 4b. Questions about groups — "which … the most", "how many per …", "total … by …"

When the answer is a count or total per something rather than a list of records, group the matches instead of listing them:

```
find-records --bo-name "Job Items" --logic 'INTHELAST([JI Job.Job Won Date], DAYS(90))' \
  --group-by "Vendor" --sum "Qty" --top 10
```

It runs on the server as a grouped report does, saving nothing, and prints one row per group: the group values, `Count` (records in the group) and each total, named like `Qty (total)`. Options:
- `--group-by "<field>, …"` — up to 3; `[Relationship.Field]` for a linked record's field (a relationship alone is refused); `"<date field> by month"` (day, week, month, quarter, year); `milestones` for the current milestones
- `--sum` / `--avg` / `--min` / `--max "<field>, …"` — number and currency fields
- `--sort "<column> [desc]"` — `Count`, a total (`Qty` or `Qty (total)`) or a group field; by default the first total, else Count, largest first
- `--top <n>` — just the first n groups

Pick the measure that answers the question ("sold the most" — quantity or sell? say which you used) and check the totals aren't all zero or blank before trusting them; if they are, the value likely lives on another business object (e.g. quote items rather than job items). Only fields and relationships every record shows the user can be grouped or totaled — one shown on some records only is refused; say so.

### 5. Show the results

Restate the search in plain language, then the formula, the count, and the records (names, key columns). For long lists show the first page and offer the rest. Follow-ups ("only the retail ones", "sort by newest") re-run the search with the adjusted formula.

### 6. Offer to save it

If they'll want this again, offer a **folder** (`cli-folders-skill`: `create-folder --logic '<same formula>'`) or a **report** (`cli-reports-skill`). A grouped search saves as a grouped report with the same columns — the group-by fields as Group columns, `count`, and each total as a total / average / minimum / maximum column — previewed with `create-report --preview` before it is saved. Saving is a change — confirm first, as those skills do.

## Key facts and confirmations

- Every command here runs through the PATH WD Remote MCP tool for its area (each command's page in [the command index](../../../pathwd-guide/references/commands.md) names it).
- Searches use only the fields the catalog lists for this user; for them, those are all the fields there are.
- The catalog's context is the designers' meaning of each name; the formula follows it.
- Field, milestone and step names come from the catalog, never from memory.
- Users hear the result in plain words; the formula is shown so they can check it.
- A link to a specific record opens it on the dashboard, in exactly this form: `https://pathwd.app/accounts/{accountId}/dashboard?record={recordId}`, e.g. `https://pathwd.app/accounts/1234/dashboard?record=6a1f0c2e9b4d3a7e5c8f1234` (`{recordId}` is the record's `id`; `web-link record <id>` prints it). No other path, host or query parameter opens it.
