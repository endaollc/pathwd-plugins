# search

Runs with: path_find_records(command: "search", args: "<flags>") (read-only).

```
Usage: search [options]

Search records globally, like the web's search (archived records left out
unless --include-archived)

Options:
  --query <text>      Search query
  --bo-id <id>        Filter by business object ID
  --bo-name <name>    Filter by business object name (resolved to ID)
  --include-archived  Include archived records (the web search's Include
                      Archived toggle; off by default)
  --page <n>          Page number (default: "1")
  --per-page <n>      Items per page (default: "15")
```

## Examples

- args: `--query "Smith"`

## Notes

PATH's quick search, like the web's search box: records whose quick-search text matches the words. For conditions on field values ("balance over 1000", "won last month") use `find-records`.

**Examples**

```
search --query "Henderson"
search --query "26-0074" --bo-name Job
search --query "Smith Plumbing" --bo-name Customer --include-archived
search --query "Smith" --bo-name Job --page 2
```

**Output**

```
id                       | bo             | name                           | milestones
-------------------------|----------------|--------------------------------|-----------------------------
6af4b704e1333c2cca9714ba | Job            | 26-1881: ZZY order ZZYSC0001   | Fulfillment, Awaiting Receiving
6af4b704e1333c2cca9714b8 | Purchase Order | 26-1881-01: Amsco - 11/11/2026 | Awaiting Receiving, Imported
[1–15 of 95 records — --page 2 for more]
```

- Columns to read: `id` (what `get-record --record-id` and every other record command take), `bo` (the record's business object), `name` (its identifier; `(archived)` after it with `--include-archived`), `milestones` (the milestones it is in now).
- One page, **15** by default (`--per-page`, `--page`); the footer gives the total and the next page.
- Field values aren't listed: `get-record --record-id <id>` shows a record's fields by name.
- Archived records are left out unless `--include-archived`.
- Only business objects the user can view and that show in the web's menu are searched.

**When nothing matches:** try spelling variants, part of the name, without LLC/Inc, the number with and without its dash, and the related customer or address, before asking the user (the [`cli-records` workflow](../workflows/records.md), "When a record can't be found").

**Related:** `find-records`, `get-record`, `list-related-records`.

Related commands: [`find-records`](../../../pathwd-find-records/references/commands/find-records.md), [`list-records`](list-records.md), [`get-record`](get-record.md), [`list-related-records`](list-related-records.md).
