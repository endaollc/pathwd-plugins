# get-record

Runs with: path_find_records(command: "get-record", args: "<flags>") (read-only).

```
Usage: get-record [options]

Get a single record by ID, as the record viewer shows it to you: field names
with display values under their field groups, related records with their ids,
then the designer context of what it shows (business object, fields,
relationships; once per session) and what can be done on it now: the pathways
its Pathways tab offers you (as pathway-run available --record-id) and the open
tasks on it (each with the pathway completing it runs). Before doing what the
user asked on this record, check whether one of them does it. --raw prints the
record as stored instead: a table of field id, name and stored value (the JSON
literal update-record --data takes), with its milestones, steps and linked
record ids. Lists of related records (inverse relationship tabs, linked
records): list-related-records

Options:
  --record-id <id>  Record ID
  --raw             The stored record: field id | name | stored value (UTC
                    dates, the values update-record --data takes) instead of
                    the record viewer's display values
  --field-id <id>   With --raw: print this one field's whole stored value (long
                    values are cut in the table)
```

## Examples

- args: `--record-id 6a9056455ff33756afba5084`

## Notes

One record as the record viewer shows it to the acting user.

**Examples**

```
get-record --record-id 6a9056455ff33756afba5084
get-record --record-id 6a9056455ff33756afba5084 --raw
get-record --record-id 6a9056455ff33756afba5084 --raw --field-id 191351
```

**Output (default)**

```
Job 26-1835: Trade Job Test (6ad13092e59d243cf0810058)
Milestones: Lead

[System]
Created Date: 10/15/26, 2:59 PM
Modified Date: 10/29/26, 12:46 PM

[Details]
Job Number: 26-1835
Department: Trade
Tax Rate: 8.250%

[Financial Details]
Total Sell With Tax: $0.00

Related:
  Job Address: 123 Main St Austin, TX 78701 (6adb9088a29a6293f946ad91)
  Job Main Customer: Hollis Creek Homes (6ace8c2cb2afe301a7d3dfe9)

Related lists: list-related-records --record-id 6ad13092e59d243cf0810058

Pathways you can start on this record (2):
  25927  Add Job Notes
  25939  New Full Quote
      context: Builds a full quote from the job's items.

Open tasks on this record (1):
  58248  Create A Quote — due 2026-11-14 09:00, assigned to Ann Lee
      completing it runs pathway 25939 "New Full Quote"

Run one: pathway-run start --pathway-id <id> --record-id 6ad13092e59d243cf0810058 (a task's pathway, by completing the task: add --task-id <task id>)
```

- Lines to read: the header (record, id, `[archived]` when archived), `Milestones`, then `Name: display value` lines under each `[field group]` as the record viewer groups them. Values are display text on the user's clock; blank fields are left out.
- Notes fields show their count and newest 3 notes; `list-record-notes` lists them all.
- `Related:` each linked record by identifier, with its record id for `get-record`.
- Designer context (business object, fields, relationships) follows when it has some, once per session.
- `Pathways you can start` (its Pathways tab for the user) and `Open tasks` (each with the pathway completing it runs): check these before doing what the user asked on the record by hand.
- Only what the user's record viewer shows is included; see [access-and-visibility.md](../cli/access-and-visibility.md) in the [`path-cli` guide](../commands.md).

**Output (`--raw`)**: the record as stored, for building `update-record --data` and checking criteria.

```
Record 6ad13092e59d243cf0810058 · 26-1835: Trade Job Test
business object: Jobs (5050)
milestones: Lead (17103)
steps: Job Created (17439)
created: 2026-10-15 14:59
modified: 2026-10-29 12:46
record email: JOB1835@demo.pathwd.app
linked records (relationship id name: record id):
  16472 Job Address: 6adb9088a29a6293f946ad91
  16480 Job Main Customer: 6ace8c2cb2afe301a7d3dfe9

id     | name               | value
-------|--------------------|--------------------------
191351 | Identifier         | "26-1835: Trade Job Test"
191629 | Department         | "Trade"
191649 | Tax Rate           | 0.0825
191635 | Est Ship Date      | "2026-10-15T17:00:00.000Z"
191677 | Deposit Collected  | false
191689 | Department Segment | null
[47 stored fields; values as stored, dates in UTC]
```

- Columns to read: `id` (the field id `update-record --data` takes), `name`, `value`: the stored value as its JSON literal (strings quoted, numbers and `true`/`false`/`null` bare, lists and objects as compact JSON), copied into `{"field_id": <id>, "value": <value>}` unchanged. Percent is a fraction, dates UTC, time spans seconds.
- A value longer than 80 characters is cut with `…`; `--raw --field-id <id>` prints that one whole.
- `milestones` / `steps` give names with ids (milestone criteria and `run-step` use them); `linked records` gives each relationship id with the linked record's id (`link-record` / `unlink-record` take the relationship id).
- A date-only field written as midnight UTC shows here as `…T00:00:00.000Z` and in the default view as the day before.

**Common errors:** a record id is 24 hex characters (`6a9056455ff33756afba5084`); a record number or identifier ("Job 26-0050") isn't one: find it with `search`.

**Related:** `list-related-records`, `list-record-notes`, `list-record-tasks`, `get-history-by-record`, `pathway-run available`, `web-link record`.

Related commands: [`search`](search.md), [`find-records`](../../../pathwd-find-records/references/commands/find-records.md), [`list-records`](list-records.md), [`list-related-records`](list-related-records.md), [`pathway-run available`](../../../pathwd-pathways/references/commands/pathway-run-available.md), [`update-record`](update-record.md).
