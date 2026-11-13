# list-related-records

Runs with: path_find_records(command: "list-related-records", args: "<flags>") (read-only).

```
Usage: list-related-records [options]

A record's related records as the record viewer shows them to you. Without
--relationship-*: every related list the record viewer shows — direct
relationships (the records this one links to: relationship cards, Contacts,
People, Addresses) and inverse relationship tabs (records that link to this
one), as a table of relationship id, name, direction, where it shows, the
related business object and a count, then each one's context. With
--relationship-id / --relationship-name: that list's records with the record
viewer's columns — an inverse tab's Table Columns (fields, linked-record
lookups, nested tables, pathway buttons, milestones), a direct relationship's
identifier, milestones and linked fields (an address's Street / City / State /
Zip). An inverse tab also shows each row's Row Details, its Summary Fields
(values of this record) and its Row Details totals, each only where the record
viewer shows it to you. Text: a table (id, name, the list's columns), then Row
Details with a value, Summary / Totals, linked record ids and the designer
context of the business object, the relationship and each column (the field /
relationship / pathway it is based on; once per session, like all context).
Open any listed record with get-record --record-id <id>. Relationships the
record viewer doesn't show you (hidden, visibility rules, business objects you
can't view) are refused

Options:
  --record-id <id>            The record whose related records to list
  --relationship-id <id>      The relationship (from the overview)
  --relationship-name <name>  The relationship by name or inverse tab name
                              (case-insensitive)
  --direction <direction>     direct or inverse, when a name or a
                              self-relationship matches both
  --no-count                  Overview: skip the per-tab record counts (one
                              small request each)
```

## Notes

A record's linked records as its record viewer shows them to the acting user: the records it links to (relationship cards, Contacts, People, Addresses) and the tabs of records that link to it (a Job's Job Items, Quotes, Purchase Orders). Run it once without a relationship for the overview, then once per list.

**Examples**

```
list-related-records --record-id 6a9056455ff33756afba5084
list-related-records --record-id 6a9056455ff33756afba5084 --relationship-name "Job Items"
list-related-records --record-id 6a9056455ff33756afba5084 --relationship-id 16480
list-related-records --record-id 6a9056455ff33756afba5084 --relationship-name "Linked Jobs" --direction inverse
```

- **Direct** lists are the links this record holds: one record at most per relationship (count 0 or 1). **Inverse** lists are the tabs of records whose own relationship points at this record (any number). The same relationship id names both sides: 16576 is "JI Job" on a Job Item and the "Job Items" tab on its Job.
- `--relationship-name` matches the relationship's name or the tab name, ignoring case. A business object linked to itself ("Linked Jobs" Job → Job) appears both ways; add `--direction direct` or `--direction inverse`.
- `--no-count` skips the overview's per-tab counts (one small request per tab), for a record with many tabs.
- Every row of the list comes back at once; there is no paging.

**Output**

Overview:

```
Related lists of Jobs record 6ad13092e59d243cf0810058
id    | name              | dir     | where     | bo        | count
------|-------------------|---------|-----------|-----------|------
16472 | Job Address       | direct  | addresses | Addresses | 1
16480 | Job Main Customer | direct  | contacts  | Customers | 1
16510 | Quotes            | inverse | tab       | Quotes    | 13
[3 related lists]

List one: list-related-records --record-id 6ad13092e59d243cf0810058 --relationship-id <id>
```

- Columns to read: `id` (the relationship id `--relationship-id` takes), `name` (the relationship, or an inverse tab's title), `dir` (`direct` / `inverse`, for `--direction`), `where` (where the viewer shows a direct link: `relationships`, `contacts`, `people`, `addresses`; `tab` for an inverse tab), `bo` (the related business object), `count` (blank with `--no-count`). The relationships' and business objects' context follows.

One list:

```
Inverse tab "Quotes" (16510) · Quotes
id                       | name                   | Created Date        | Total Sell With Tax | Milestones
-------------------------|------------------------|---------------------|---------------------|-------------------------
6adbaee3a29a6293f9470b8c | Full Quote: 26-1835-01 | 10/23/2026 14:00:51 | $15,000.00          | Quote Doc Not Yet Generated
6ae0af8ad8abbba05a946ded | Full Quote: 26-1835-04 | 10/27/2026 9:04:58  | $137,138.95         | Quote Document Generated
[2 records]
```

- Columns to read: `id` (open the row with `get-record --record-id <id>`), `name` (the identifier, left out when a column already shows it), then the record viewer's columns for the list (an inverse tab's Table Columns; a direct link's identifier, milestones and linked fields such as an address's Street / City / State / Zip) as display text. `archived` shows `yes` on archived rows.
- Below the table, where the viewer shows them: `Row details:` (each row's Row Details that have a value), `Summary:` (this record's Summary Fields), `Totals:`, `Linked record ids` (the record behind each lookup column), then the context of the business object, the relationship and each column.

**Common errors**

- `Relationship 16577 isn't one <user>'s record viewer shows on this record (hidden, its visibility rules, or a business object they can't view). Shown: direct 16472 "Job Address", …`: the id or name isn't on this record's viewer for this user; pick one from the `Shown:` list. A relationship of another business object also lands here.
- `Relationship "Linked Jobs" matches both a direct relationship and an inverse tab — pass --direction direct or --direction inverse.`
- `<user> can't open this record — they don't have View permission on Jobs (business object 5050), as in the web app.`
- `ERROR [404]: Not Found`: the record id is wrong (a record number such as "Job 26-0050" isn't an id; find it with `search`).

**Related:** `get-record` (`related` / `related_ids` give the direct links at a glance), `link-record`, `unlink-record`, `list-relationships` (the relationships a business object holds), `find-records` (a path such as `[Job Main Customer.Customer Name]` searches across a link).

Related commands: [`search`](search.md), [`find-records`](../../../pathwd-find-records/references/commands/find-records.md), [`list-records`](list-records.md), [`get-record`](get-record.md).
