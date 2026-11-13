# find-records

Runs with: path_find_records(command: "find-records", args: "<flags>") (read-only).

```
Usage: find-records [options]

Advanced search: the records of one business object that match a Logic Builder
formula (e.g. 'ISLASTWEEK([Created Date]) AND HASSTEP("Job Won")'), run on the
fly without saving a folder or report. Only what the record viewer shows you
can be searched: hidden fields, fields in hidden field groups and hidden
relationships are unknown to the formula, and a rule on a field whose
visibility depends on the record only matches records that show it. Archived
records are left out unless --include-archived. --catalog lists what you can
search on (fields with their options, relationships, milestones, steps) with
each one's designer context. --group-by groups the matches instead of listing
them (as a grouped report does, run on the fly): one row per group with its
record count and any --sum / --avg / --min / --max totals; only fields and
relationships every record shows you can be grouped or totaled. Text: the
formula as PATH read it with the match count, then a table of id, name,
milestones and the formula's fields (or --columns), display values; with
--group-by, a table of the groups. Formula syntax, recipes and common mistakes:
the path-cli skill's references/find-records-syntax.md

Options:
  --bo-id <id>        Business object ID to search
  --bo-name <name>    Business object name (resolved to ID)
  --logic <formula>   The search as a Logic Builder formula
  --catalog           List what you can search on in this business object, with
                      designer context, instead of searching
  --include-archived  Include archived records (left out by default)
  --columns <names>   Comma-separated field / relationship names to show
                      (default: the ones the formula uses)
  --sort <field>      Sort by a field of the business object; add " desc" for
                      descending (e.g. "Created Date desc")
  --count             Print only how many records match
  --page <n>          Page to show (default: "1")
  --per-page <n>      Records per page (max 100) (default: "25")
  --all               Every matching record (pages through them)
  --group-by <names>  Group the matching records instead of listing them:
                      comma-separated fields (at most 3; [Relationship.Field]
                      for a linked record's field; "<date field> by
                      day|week|month|quarter|year"; milestones for the current
                      milestones). Each group comes with its record count
  --sum <names>       With --group-by: total these number / currency fields in
                      each group
  --avg <names>       With --group-by: average these number / currency fields
                      in each group
  --min <names>       With --group-by: the smallest value of these fields in
                      each group
  --max <names>       With --group-by: the largest value of these fields in
                      each group
  --top <n>           With --group-by: only the first n groups (after sorting).
                      Groups sort by --sort "<column> [desc]" (Count, a total
                      like "Qty (total)", a group field); default the first
                      total, else Count, largest first
```

## Examples

- args: `--bo-name Job --logic 'INMILESTONE("Lead") AND INTHELAST([Created Date], DAYS(30))' --count`

## Notes

Advanced search over every record of one business object with a Logic Builder formula; nothing is saved. The full language, tested recipes and the common mistakes are in the [`path-cli` guide](../../../pathwd-guide/references/commands.md)'s [find-records-syntax.md](../find-records-syntax.md).

**Examples**

```
find-records --bo-name Job --catalog
find-records --bo-name Job --logic 'INMILESTONE("Lead", "Qualified")'
find-records --bo-name Job --logic 'INTHELAST([Created Date], DAYS(30)) AND [Department] = "Retail"' --count
find-records --bo-name Job --logic 'ISANYTIME([Created Date])' --group-by Department --sum "Total Sell With Tax"
find-records --bo-name Job --logic 'NOTINMILESTONE("Closed")' --columns "Job Name, Department, Job Main Customer" --sort "Created Date desc" --all
```

**Output**

```
82 Jobs match: ISTHISYEAR([Created Date])
id                       | name                                       | milestones                     | Created Date
-------------------------|--------------------------------------------|--------------------------------|------------------
6a9056455ff33756afba5084 | 26-0050: Henderson, Patricia - Demo Retail | Awaiting Receiving, Partially… | 8/27/26, 10:22 AM
6a9066405ff33756afba8858 | 26-0051: SD PostWipe Retail 01             | Quoting, Full Quote Out        | 8/27/26, 11:30 AM
[1–25 of 82 records — --page 2 for more, --all for every one]
```

- First line: `<n> <records> match: <formula as PATH read it>`. Read the echo back: `""` shows as `?` (a rule left out), several milestones as `INANYMILESTONE(...)`.
- Columns to read: `id` (opens the record: `get-record --record-id <id>`), `name` (the identifier), `milestones`, then one column per field the formula uses on the business object itself (or each `--columns` name), as **display text** on the user's clock (`$1,250.00`, `8.250%`, `8/27/26, 10:22 AM`). Long cells end in `…`.
- 25 a page; the footer says which records of how many and how to get the rest.
- `--count`: the first line alone.
- `--group-by`: the first line, then one row per group with `Count` and each total (`"<field> (total)"`), and a footer naming the sort (`[3 groups, by Total Sell With Tax (total) (largest first)]`).
- `--catalog`: what can be searched — fields with type and options, relationship fields, relationships, milestones, steps — then their designer context.

**Common errors**

- `Unknown field "X"`: a relationship named as a field (`[Job Main Customer]` → `[Job Main Customer.Customer Name]`), a path by business object name, a reverse hop, or a spelling not in `--catalog`.
- `Unknown function "NOT"`: there is no NOT; use the rule's negative (`NOTINMILESTONE`, `NOTHASSTEP`, `ISNOTANYOF`, `!=`).
- `Finish the rule with a comparison…`: a function written as an operator (`[F] CONTAINS "x"` → `CONTAINS([F], "x")`), or an unquoted formula (only its first word was read).
- `Unknown column "X"`: `--columns` takes the business object's fields or relationship names, not `Rel.Field`.
- No error, wrong count: a negative rule through a relationship (`[Rel.F] != "x"`) leaves out records with no linked record (add `OR ISEMPTY([Rel.ID field])`). On the record's own field a negative rule matches empty values (never set or cleared).

**Related:** `search` (by name or number), `list-records` (a folder's records), `run-report` (a saved report), `create-folder` / `create-report --logic` (save the same formula).

Related commands: [`search`](../../../pathwd-guide/references/commands/search.md), [`list-records`](../../../pathwd-guide/references/commands/list-records.md), [`get-record`](../../../pathwd-guide/references/commands/get-record.md), [`list-related-records`](../../../pathwd-guide/references/commands/list-related-records.md).
