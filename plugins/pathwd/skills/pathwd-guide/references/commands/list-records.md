# list-records

Runs with: path_find_records(command: "list-records", args: "<flags>") (read-only).

```
Usage: list-records [options]

List one business object's records: the ones you viewed recently (--bo-name /
--bo-id alone), or a folder's (--folder-name with --bo-name / --bo-id, or
--folder-id, whose folder must belong to the business object when one is
given). A business object is required: it never lists records across the whole
account. To search all of a business object's records by criteria, use
find-records. A folder prints as the web shows it: a table of id, name (the
identifier), milestones and the folder's columns with display values on your
clock, then the folder's, the business object's and the columns' designer
context (only what has context). Recent records: id, name, milestones and when
you viewed each. Like all context, each is shown once per session; later calls
leave it out (pass --with-context to repeat)

Options:
  --bo-name <name>      Business object name (required unless --bo-id or
                        --folder-id)
  --bo-id <id>          Business object ID (instead of --bo-name)
  --folder-name <name>  Folder name on the business object
  --folder-id <id>      Folder ID (must belong to --bo-name / --bo-id when
                        given)
  --page <n>            Folders only: fetch only this single page (disables
                        auto-pagination)
  --per-page <n>        Folders only: items per page (page size used while
                        paging) (default: "30")
  --user-filter <json>  Folders only: Run-time values for the user filters
                        (JSON array). Name a ticked row by "label" (the label
                        viewers see) or "uid" (the "uid:" line under the row in
                        get-filter folder|report <id>); a milestone "currently
                        in" user filter takes milestone names or ids from its
                        allowed list, e.g.
                        '[{"label":"Stage","values":["Lead","Qualified"]}]'
                        (entity_id and logic are filled in; INMILESTONE /
                        NOTINMILESTONE rows take one; [] = row not applied).
                        Other rows:
                        '[{"label":"Type","logic":"equal","values":["Retail"]}]';
                        raw overrides such as
                        '[{"entity_id":12,"logic":"equal","values":["John"]}]'
                        pass through
```

## Notes

One business object's records: those in a folder, or those the user viewed recently. A business object is always required; it never lists a whole business object (`find-records` does) or records across the account.

**Examples**

```
list-records --bo-name Job --folder-name "Active Jobs"
list-records --folder-id 20976
list-records --folder-id 20976 --user-filter '[{"label":"Stage","values":["Lead"]}]'
list-records --bo-name Job
```

- With a folder: every record in it, as the folder's filter and columns define them (auto-paged 30 at a time; `--page` for one page). `--user-filter` sets the folder's user filters.
- With only `--bo-name` / `--bo-id`: the records of that business object the user viewed recently.
- A folder given with a business object must belong to it.

**Output**

```
Folder "All Jobs" (20975) · Jobs
id                       | name                          | milestones                         | Primary Vendor    | Job Address
-------------------------|-------------------------------|------------------------------------|-------------------|------------------------------------
6af3943cd0fe4220c7ad0f68 | 26-1873: Test QBO Sync Fail   | Ordering, Awaiting Acknowledgments | Wonderful Windows | 4143 Test Ridge Dr Austin, TX 78704
6aeb62f03dca342ee05610de | 26-1864: TradeTest-CP         | Lead                               | Marvin - Signature | 111 Harbor Bluff Dr. Largo, FL 33770
[82 records]
```

- A folder looks like the web folder: `id` (for `get-record --record-id` and the other record commands), `name` (the identifier, `(archived)` after it when the folder includes archived records), `milestones`, then the folder's own columns in its order, as display text on the user's clock. Every column the web folder shows is listed, including fields the record viewer itself doesn't show (a folder shows those, as on the web); a cell is blank where the field has no value or the user's visibility rules leave it out on that record. Long cells end in `…`.
- Every record is listed (paged 30 at a time behind the scenes). With `--page`, one page and the footer `[31–60 of 82 records — --page 3 for more]`.
- Recent records (`--bo-name` alone): `id`, `name`, `milestones`, `viewed` (when the user last opened it, `2026-11-11 11:35` on their clock), under `Recently viewed <records>`.
- Under the table: `Folder context`, `Business object context` and `Field context` (the columns' fields), only what has context, once per session.

**Related:** `list-folders` (folder ids and names), `find-records` (records matching conditions), `run-report`.

Related commands: [`search`](search.md), [`find-records`](../../../pathwd-find-records/references/commands/find-records.md), [`get-record`](get-record.md), [`list-related-records`](list-related-records.md).
