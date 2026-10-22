# PATH CLI command reference

Every PATH CLI command with its exact flags, generated from the CLI itself. Run a command by passing it as the `command` string to the `path_cli` tool, e.g. `path_cli(command: "--account 1234 list-fields --bo-name Job")`.

## Global options

Put these before the command name.

| Option | Description |
|--------|-------------|
| `--json` | Output full JSON |
| `--fields <list>` | Comma-separated field selection |
| `--quiet` | Output IDs only |
| `--account <id>` | Account ID (overrides PATH_ACCOUNT_ID env var / saved default) |
| `--no-cache` | Bypass resolver cache for this invocation |
| `--as-user <id|email|name>` | Act as another member of the account, as the web's Impersonate does (account Admins only; not yourself or a deactivated user) |
| `--implicit-json` | JSON because the caller left the format to the default |
| `--with-info` | Repeat designer info (and the account block) that was already shown earlier this session — e.g. after your context was compacted |

## Commands

### `api`

```
Usage: api [options] <method> <path>

Raw API escape hatch

Arguments:
  method         HTTP method: get, post, put, patch, delete
  path           API path (e.g., /v1/business-objects)

Options:
  --data <json>  JSON request body
```

### `archive-record`

```
Usage: archive-record [options]

Archive a record (or several with --record-ids) via the Archive Record step

Options:
  --record-id <id>    Record ID
  --record-ids <ids>  Comma-separated record IDs
  --bo-id <id>        Business object ID (skips the lookup; only with a single
                      --record-id)
```

### `archive-record-file`

```
Usage: archive-record-file [options]

Archive a file on a record

Options:
  --record-id <id>  Record ID
  --file-id <id>    File ID
```

### `assign-task`

```
Usage: assign-task [options]

Assign a task on a record to a user

Options:
  --record-id <id>  Record ID
  --task-id <id>    Task ID (as listed by list-record-tasks)
  --user-id <id>    User ID to assign the task to
  --data <json>     Legacy JSON body {"task_id": .., "user_id": ..};
                    --task-id/--user-id win
```

### `auth`

```
Usage: auth [options]

Authenticate and store token (browser sign-in by default)

Options:
  --email <email>        User email (opt into the legacy password flow)
  --password <password>  User password (opt into the legacy password flow)
  --browser              Sign in via the browser (this is the default; the flag
                         is optional)
  --no-open              Print the sign-in URL instead of auto-launching a
                         browser (headless)
```

### `cancel-task`

```
Usage: cancel-task [options]

Cancel a task on a record

Options:
  --record-id <id>  Record ID
  --task-id <id>    Task ID
```

### `clear-appointment`

```
Usage: clear-appointment [options] <record-id>

Clear a record's appointment field (deletes the calendar event that owns it)

Arguments:
  record-id          Record id

Options:
  --field <id|name>  Appointment field on the record
```

### `clear-cache`

```
Usage: clear-cache [options]

Clear the resolver cache (all accounts, or one with --account)

Options:
  --account <id>  Clear cache for a specific account only
```

### `complete-task`

```
Usage: complete-task [options]

Mark a task as complete on a record

Options:
  --record-id <id>  Record ID
  --task-id <id>    Task ID
```

### `create-calendar-event`

```
Usage: create-calendar-event|create-appointment [options]

Create a calendar event (standalone, or a record appointment with
--record/--field)

Options:
  --title <text>        Event title
  --start <date>        Start: "YYYY-MM-DD HH:mm", YYYY-MM-DD, an ISO
                        timestamp, today, tomorrow, +3d (your time zone)
  --end <date>          End (same formats). Default: a moved start keeps the
                        length, a new event lasts an hour
  --calendar <id|name>  Calendar
  --all-day             All day: the dates are whole days on the account
                        calendar
  --no-all-day          Timed (turning all day off gives 8–9am on the same
                        days)
  --multi-day           Spans several days (default: follows the dates)
  --no-multi-day        One day (brings the end back to the start day)
  --color <color>       Event color, e.g. #c33 ("" for the calendar color)
  --location <text>     Location
  --description <text>  Description
  --record <id>         Schedule it on this record's appointment field
  --field <id|name>     The appointment field (default: the record has only
                        one)
  --data <json>         Raw JSON body for the event, passed through as-is
                        (start_date / end_date as ISO timestamps, UTC); flags
                        override its keys
```

### `create-custom-field`

```
Usage: create-custom-field [options] <report-id>

Add a filter-only Custom Field to a report: a calculation (--expression,
expression syntax) or a Time Between (--from/--to: today | field:<date field> |
milestone:<name> | step:<name>). Compare it in the report filter with
CUSTOMFIELD("name"); time spans compare in DAYS()/HOURS()...

Arguments:
  report-id                              Report ID

Options:
  --name <name>                          Custom Field name (unique among the report custom columns/fields)
  --expression <formula>                 Calculation, e.g. "[Total] - [Paid]"
  --from <source>                        Time Between start
  --to <source>                          Time Between end
  --from-occurrence <first|most_recent>  For milestone/step sources: first | most_recent (default most_recent)
  --to-occurrence <first|most_recent>    For milestone/step sources: first | most_recent (default most_recent)
  --info <text>                          The Custom Field's info: what it measures and why the filter compares it (at most 50,000 characters)
  --info-file <path>                     Read the info from a text or markdown file (instead of --info)
  --dry-run                              Print the column without saving
```

### `create-folder`

```
Usage: create-folder [options]

Create a folder with permissions (and its info: --info / --info-file)

Options:
  --bo-id <id>                     Business object ID
  --bo-name <name>                 Business object name (resolved to ID)
  --folder-name <name>             Folder name
  --roles <names>                  Comma-separated role names
  --role-permissions <perms>       Comma-separated: view,edit,delete
  --users <names>                  Comma-separated user full names
  --user-permissions <perms>       Comma-separated: view,edit,delete
  --logic <formula>                Logic Builder formula, e.g. '[Department] =
                                   "Residential" AND INMILESTONE("Won")' — see
                                   /path-logic-formula-syntax
  --logic-raw <json>               Raw GroupFilter JSON (escape hatch for edge
                                   cases)
  --map-relationship <name-or-id>  Relationship (name or ID) from this folder's
                                   BO to the account's Address BO; sets
                                   map_enabled and map_relationship_id
  --info <text>                    The folder's info: what it is for, who uses
                                   it, what its records have in common — shown
                                   with the folder to people and AI agents (at
                                   most 50,000 characters)
  --info-file <path>               Read the folder's info from a text or
                                   markdown file (instead of --info)
  --dry-run                        Parse/build the request bodies and print
                                   them, but create nothing
```

### `create-record`

```
Usage: create-record [options]

Create a record on a business object

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name (resolved to ID)
  --data <json>     JSON body with business_object_id (optional), fields, and
                    optional user_id
```

### `create-report`

```
Usage: create-report [options]

Create a report the way the web report builder does: columns (fields,
Relationship.Field, milestone/step dates, Milestones, Count,
CALC(<expression>), TIMEBETWEEN(<from>, <to>)), Group / Calculation columns, a
Logic Builder filter, Custom Fields, info. You become its owner; --users /
--roles share it. Needs Create Reports on the business object.

Options:
  --bo-id <id>                Business object ID
  --bo-name <name>            Business object name (resolved to ID)
  --name <name>               Report name (unique on the business object)
  --columns <specs>           Columns, in order, separated by commas: <source>
                              [| option …]. Sources: Field, Relationship.Field
                              ([…] for names with commas), milestone:<name>,
                              step:<name>, milestones, count,
                              CALC(<expression>), TIMEBETWEEN(<from>, <to>)
                              with from/to = today | field:<date field> |
                              milestone:<name> | step:<name>. Options:
                              name=<title>, info=<text>,
                              group[=day|week|month|quarter|year|<range>|<1d,
                              12h…>], total|average|minimum|maximum,
                              sort=asc|desc, width=<n>,
                              occurrence=first|most_recent, from-occurrence=,
                              to-occurrence=, group-expression=<TOTAL([x])…>,
                              format=<date format>, decimals=<n>, commas,
                              currency[=<symbol>], percent,
                              duration=days+hours+minutes+seconds
  --column <spec>             One more column (repeatable; same spec as
                              --columns, handy for CALC expressions with
                              commas)
  --custom-field <spec>       A filter-only Custom Field (repeatable):
                              CALC(<expression>) | name=<n> or
                              TIMEBETWEEN(<from>, <to>) | name=<n> — never
                              shown, compared in --logic with
                              CUSTOMFIELD("<n>")
  --type <type>               Report type: detail | grouped (the web picks it:
                              grouped once a column is a Group column); checked
                              against the columns
  --logic <formula>           Filter, a Logic Builder formula (see
                              /path-logic-formula-syntax); COLUMN("<computed
                              column>") and CUSTOMFIELD("<custom field>")
                              compare this report's own
  --include-archived          Include archived records
  --info <text>               The report's info: what it is for, who uses it,
                              how to read it — shown with it to people and AI
                              agents (at most 50,000 characters)
  --info-file <path>          Read the report's info from a text or markdown
                              file (instead of --info)
  --column-info <json>        Info on columns by name, e.g. '{"Margin":"Sell
                              minus cost, per job"}' (computed columns: say how
                              they are calculated)
  --roles <names>             Share with these roles (comma-separated)
  --role-permissions <perms>  Comma-separated: view,edit,delete (default view)
  --users <names>             Share with these users (comma-separated full
                              names)
  --user-permissions <perms>  Comma-separated: view,edit,delete (default view)
  --data <json>               Escape hatch: the create body as JSON (keys:
                              name, info, report_type, scheduling, columns,
                              filter_condition, include_archived,
                              show_user_filter, user_filter_condition_keys);
                              flags override it
  --dry-run                   Build and validate the body and print it, but
                              create nothing
  --preview                   Run the report as built and print its rows, but
                              save nothing (no --name needed): try a report
                              before creating it, then create it with the same
                              options
  --groups-only               With --preview on a grouped report: only the
                              group total rows
  --page <n>                  With --preview: page to show (default: "1")
  --per-page <n>              With --preview: rows per page (default: "50")
  --all                       With --preview: every row in one page
```

### `delete-calendar-event`

```
Usage: delete-calendar-event [options] <id>

Delete a calendar event (clears a record appointment field)

Arguments:
  id          Calendar event id

Options:
```

### `delete-custom-field`

```
Usage: delete-custom-field [options] <report-id> <field>

Remove a report Custom Field. Refused while a filter rule compares it — change
the filter first (set-filter report <id>).

Arguments:
  report-id   Report ID
  field       Custom Field name or column_key

Options:
```

### `delete-message`

```
Usage: delete-message [options]

Delete a message on a record

Options:
  --record-id <id>   Record ID
  --message-id <id>  Message ID
```

### `delete-record-file`

```
Usage: delete-record-file [options]

Delete a file from a record

Options:
  --record-id <id>  Record ID
  --file-id <id>    File ID
```

### `download-record-file`

```
Usage: download-record-file [options]

Download one file attached to a record (to a local path, or to stdout with
--stdout)

Options:
  --record-id <id>  Record ID
  --file-id <id>    The record file id ("id" in list-record-files) or the
                    stored file id ("file_id")
  --out <path>      Destination file, or an existing directory to save into
                    under the original file name (default: ./<file name>)
  --stdout          Write the file contents to stdout instead of saving
```

### `execute-pathway`

```
Usage: execute-pathway [options]

Execute a pathway headlessly on a record. Like a record's Pathways tab in the
web app, it only starts where the acting user could start it (role, shown on
records, starting business object, record access, the pathway's visibility
criteria)

Options:
  --pathway-id <id>      Pathway ID
  --pathway-name <name>  Pathway name (resolved to ID)
  --record-id <id>       Record ID to execute the pathway on
  --data <json>          Additional JSON body fields (e.g. {"task_id":123});
                         starting_record.id is set from --record-id
  --mode <mode>          real-world (default) | super-user (also when the
                         pathway's visibility criteria don't match the record;
                         Admin or Designer only, this run only)
```

### `feed-records`

```
Usage: feed-records [options]

The records your Feed and popups mention (the Feed page record list)

Options:
  --bo <id>   Only records of this business object
```

### `feed-unread-counts`

```
Usage: feed-unread-counts [options]

Unread Feed counts per business object (the menu badges) or per record

Options:
  --by <bo|record>  Group by business object or record (default: "bo")
```

### `find-records`

```
Usage: find-records [options]

Advanced search: the records of one business object that match a Logic Builder
formula (e.g. 'ISLASTWEEK([Created Date]) AND HASSTEP("Quote Sent")'), run on
the fly without saving a folder or report. Only what the record viewer shows
you can be searched: hidden fields, fields in hidden field groups and hidden
relationships are unknown to the formula, and a rule on a field whose
visibility depends on the record only matches records that show it. Archived
records are left out unless --include-archived. --catalog lists what you can
search on (fields with their options, relationships, milestones, steps) with
each one's designer info. --group-by groups the matches instead of listing them
(as a grouped report does, run on the fly): one row per group with its record
count and any --sum / --avg / --min / --max totals; only fields and
relationships every record shows you can be grouped or totaled. JSON is {
business_object, formula, total, page, per_page, columns, items: [{ id, record,
milestones, values }] }, or with --group-by { business_object, formula, total,
groups, columns, items: [{ <column>: text }] }. Formula syntax: the
path-logic-formula-syntax skill

Options:
  --bo-id <id>        Business object ID to search
  --bo-name <name>    Business object name (resolved to ID)
  --logic <formula>   The search as a Logic Builder formula
  --catalog           List what you can search on in this business object, with
                      designer info, instead of searching
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

### `get-account`

```
Usage: get-account [options]

Show the active account (or --account-id): its name, time zone, country and
info, the designers' description of the business. Read the info at the start of
a session; it says what the account's records and pathways are for.

Options:
  --account-id <id>  Account ID (default: the saved / --account account)
```

### `get-business-object`

```
Usage: get-business-object [options]

Get a single business object, with its info (the designer's description of what
it is for)

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name
```

### `get-calendar-event`

```
Usage: get-calendar-event [options] <id>

Get one calendar event by id

Arguments:
  id          Calendar event id

Options:
```

### `get-event-metadata`

```
Usage: get-event-metadata [options]

Get the metadata (and context) of a single history event (read-only)

Options:
  --event-id <id>  History event ID
```

### `get-field`

```
Usage: get-field [options]

Get a single field on a business object, with its info (the designer's
description of what the field holds and when to fill it)

Options:
  --bo-id <id>         Business object ID
  --bo-name <name>     Business object name
  --field-id <id>      Field ID
  --field-name <name>  Field name (case-insensitive)
```

### `get-filter`

```
Usage: get-filter [options] <target> <id>

Print a stored Logic Builder filter as formula text (milestone criteria, task
criteria, Fork conditions, folder/report/sticker filters, visibility rules).
--json prints the stored pipeline JSON.

Arguments:
  target          Where the filter lives (see Targets below)
  id              Id of the object holding the filter

Options:
  --bo <id>       Business object ID (task targets)
  --pathway <id>  Pathway ID (fork, table-filter)
  --sticker <id>  Folder sticker ID (folder-sticker)
```

### `get-history-by-record`

```
Usage: get-history-by-record [options]

Change history for a record as a flat event table (read-only). All sessions are
fetched unless --page is given.

Options:
  --record-id <id>        Record ID
  --from <date>           Only events at/after this time (ISO 8601)
  --to <date>             Only events at/before this time (ISO 8601)
  --action <action>       Filter by event action (e.g. updated)
  --category <list>       Filter by event category; comma-separated for several
                          (e.g. saved_changes,milestones)
  --page <n>              Fetch only this single page of sessions (disables
                          auto-pagination)
  --limit <n>             Sessions per page (max 100) (default: "100")
  --sessions              Session-grouped output (one row per session) instead
                          of the flat event table
  --metadata-keys <list>  Comma-separated metadata keys to show in the METADATA
                          column (default: all but plumbing)
  --views                 Include view-only sessions (record opened, nothing
                          changed); hidden by default like the History tab
  --oldest-first          Chronological order (default: newest session and
                          event first, like the History tab)
  --full                  Every field change and metadata entry in full, one
                          per line (rows grow taller instead of wider)
  --ids                   Add an EVENT column with each event id (for
                          get-event-metadata)
  --max-width <n>         Max characters for the CHANGES and METADATA cells;
                          cut between entries with "(+N more)" (0 = unlimited)
                          (default: "60")
```

### `get-history-session`

```
Usage: get-history-session [options] <activity-session-id>

Every event one activity session recorded — e.g. everything an inbound
integration request did: the record it created, fields set, pathway steps,
outbound calls — as the flat event table, oldest first (read-only)

Arguments:
  activity-session-id     Activity session id (sessionId in --json history; the
                          session line of an integration delivery)

Options:
  --newest-first          Newest event first (default: in the order they
                          happened)
  --full                  Every field change and metadata entry in full, one
                          per line
  --metadata-keys <list>  Comma-separated metadata keys to show in the METADATA
                          column (default: all but plumbing)
  --max-width <n>         Max characters for the CHANGES and METADATA cells (0
                          = unlimited) (default: "60")
```

### `get-notification-settings`

```
Usage: get-notification-settings [options]

Your notification settings: account-wide rows (task, feed, …), or with
--resolved every notification that can reach you and which channels you may
turn on

Options:
  --resolved  Per-notification rows with each channel's required / allowed /
              not-allowed state
```

### `get-profile`

```
Usage: get-profile [options]

Show your profile settings: name, email, phone, time zone and muted report
emails

Options:
```

### `get-record`

```
Usage: get-record [options]

Get a single record by ID, as the record viewer shows it to you. Also gives the
designer info of what it shows: business_object {id, name, info}, field_info
{<field name>: info} and relationship_info {<relationship name>: info} (only
entries that have info; a relationship field without its own shows the related
field's). In the record view, related_ids gives each related record's id (open
it with get-record). Text output ends with a compact info block. Lists of
related records (inverse relationship tabs, linked records):
list-related-records

Options:
  --record-id <id>  Record ID
  --raw             The stored record (fields keyed by id, UTC dates) instead
                    of the record viewer's names and display values (real-world
                    mode) / name enrichment
```

### `get-report`

```
Usage: get-report [options] <report-id>

Show a report's columns (with column_key / usage), its filter-only Custom
Fields, its filter as formula text, and the report's and each column's own info
(what it is for; JSON keys info and columns[].info, once per session like all
info)

Arguments:
  report-id   Report ID

Options:
```

### `link-record`

```
Usage: link-record [options]

Link two records via a relationship

Options:
  --record-id <id>          Source record ID
  --related-record-id <id>  Target record ID
  --relationship-id <id>    Relationship ID
```

### `list-accounts`

```
Usage: list-accounts [options]

List your accounts (not the ones you've been deactivated in)

Options:
```

### `list-appointment-fields`

```
Usage: list-appointment-fields [options]

List the appointment fields calendar events can be scheduled on

Options:
  --bo-id <id>      Only this business object
  --bo-name <name>  Only this business object (by name)
```

### `list-business-objects`

```
Usage: list-business-objects [options]

List business objects (--json adds each one's info: what the designer says it
is for)

Options:
```

### `list-calendar-events`

```
Usage: list-calendar-events|list-appointments [options]

List calendar events (all origins) in a date window

Options:
  --start-date <date>   Window start: YYYY-MM-DD (from the start of that day),
                        "YYYY-MM-DD HH:mm", today, tomorrow, +3d — your time
                        zone; an ISO timestamp with Z/offset as-is
  --end-date <date>     Window end (same formats; a bare day runs to the end of
                        that day, inclusive)
  --calendar-ids <ids>  Comma-separated calendar ids to include
  --record-id <id>      Only events scheduled on this record
  --include-related     With --record-id: also the appointments of records it
                        links to and records linking to it (the record's
                        Appointments tab)
  --sources <list>      Only these origins, comma-separated: standalone,
                        record_appointment, provider
  --search <text>       Search events by text instead of listing a window
  --page <n>            Page number (search only) (default: "1")
  --per-page <n>        Items per page (search only) (default: "25")
```

### `list-calendars`

```
Usage: list-calendars [options]

List calendars

Options:
```

### `list-feed`

```
Usage: list-feed [options]

List your dashboard Feed (newest first), like the web Feed page

Options:
  --unread          Only unread items
  --pinned          Only pinned items
  --bo <id|name>    Only items on this business object
  --record-id <id>  Only items on this record
  --title <text>    Title contains
  --content <text>  Content contains
  --page <n>        Page number (default: "1")
  --per-page <n>    Items per page (default: "25")
```

### `list-field-groups`

```
Usage: list-field-groups [options]

List field groups for a business object (--json adds each one's info)

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name
  --all             Fetch the full set via /init/field_groups
```

### `list-fields`

```
Usage: list-fields [options]

List fields for a business object (--json adds each field's info: what it holds
and when to fill it)

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name
  --page <n>        Fetch only this single page (default: all fields)
  --per-page <n>    Items per page (single-page mode) (default: "100")
  --all             Fetch all fields (this is now the default)
```

### `list-file-types`

```
Usage: list-file-types [options]

List a business object's file types, as Designer → Files → File Types lists
them (the system types Archived and Email Attachment are left out unless
--include-hidden)

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name (resolved to ID)
  --include-hidden  Also list the hidden system types (Archived, Email
                    Attachment)
```

### `list-folders`

```
Usage: list-folders [options]

List folders. Each folder carries its own info (what the folder is for, who
uses it; once per session like all info). Each folder_stickers[] entry carries
its info; a copy of a business object sticker with no info of its own also gets
bo_sticker_info (the business object sticker's info, which the copy shows).
Text output lists folder info and sticker info under the table

Options:
  --bo-id <id>      Filter by business object ID
  --bo-name <name>  Filter by business object name
  --page <n>        Fetch only this single page (default: all folders)
  --per-page <n>    Items per page (single-page mode) (default: "50")
  --all             Fetch all folders (this is now the default)
```

### `list-notifications`

```
Usage: list-notifications [options]

List notifications

Options:
  --page <n>      Page number (default: "1")
  --per-page <n>  Items per page (default: "15")
  --unread        Only show unread notifications
```

### `list-pathways`

```
Usage: list-pathways [options]

List pathways (--json adds each pathway's info: what the designer built it for)

Options:
```

### `list-popups`

```
Usage: list-popups [options]

List your popup notifications (the in-app popups)

Options:
  --unread        Only unread popups
  --page <n>      Page number (default: "1")
  --per-page <n>  Items per page (default: "25")
```

### `list-record-emails`

```
Usage: list-record-emails [options]

List emails on a record

Options:
  --record-id <id>  Record ID
  --page <n>        Fetch only this single page (default: all emails)
  --per-page <n>    Items per page (single-page mode) (default: "15")
  --all             Fetch all emails (this is now the default)
```

### `list-record-files`

```
Usage: list-record-files [options]

List every file attached to a record (optionally only the given file types),
with each file's type names

Options:
  --record-id <id>            Record ID
  --file-type <ids-or-names>  Only files carrying one of these file types
                              (comma-separated ids and/or names, names
                              case-insensitive)
```

### `list-record-messages`

```
Usage: list-record-messages [options]

List messages on a record

Options:
  --record-id <id>  Record ID
  --page <n>        Fetch only this single page (default: all messages)
  --per-page <n>    Items per page (single-page mode) (default: "15")
  --all             Fetch all messages (this is now the default)
```

### `list-record-tasks`

```
Usage: list-record-tasks [options]

List tasks on a record

Options:
  --record-id <id>  Record ID
```

### `list-records`

```
Usage: list-records [options]

List records in a folder (--folder-id / --folder-name), or your recent records.
JSON is { folder: {id, name, info}, business_object: {id, name, info},
field_info: [{field_id, name, info}], items } for a folder — folder.info is the
folder's own info (what it is for, who uses it), field_info lists the folder's
columns that have designer info (a relationship field without its own shows the
related field's) — and { business_objects: [{id, name, info, field_info}],
items } for recent records (one entry per business object present). items is
the record list as before. Text output prints the table, then a "Folder info:",
a "Business object info:" and a "Field info:" block (only what has info). Like
all info, each is shown once per session; later calls leave it out (pass
--with-info to repeat)

Options:
  --folder-id <id>      Folder ID to list records from
  --folder-name <name>  Folder name (requires --bo-id or --bo-name)
  --bo-id <id>          Business object ID (for --folder-name)
  --bo-name <name>      Business object name (for --folder-name)
  --page <n>            Fetch only this single page (disables auto-pagination)
  --per-page <n>        Items per page (page size used while paging) (default:
                        "30")
```

### `list-related-records`

```
Usage: list-related-records [options]

A record's related records as the record viewer shows them to you. Without
--relationship-*: every related list the record viewer shows — direct
relationships (the records this one links to: relationship cards, Contacts,
People, Addresses) and inverse relationship tabs (records that link to this
one), each with direction, relationship id, name, the related business object
{id, name, info}, the relationship's info and a count. With --relationship-id /
--relationship-name: that list's records with the record viewer's columns — an
inverse tab's Table Columns (fields, linked-record lookups, nested tables,
pathway buttons, milestones), a direct relationship's identifier, milestones
and linked fields (an address's Street / City / State / Zip). JSON: { record,
direction, relationship {id, name, info}, business_object {id, name, info},
columns [{kind, name, field_id?, relationship_id?, pathway_id?, info}], items
[{id, identifier, values, linked_ids?}] } — a column's info is the info of the
field / relationship / pathway it is based on (once per session, like all
info). Open any listed record with get-record --record-id <id>. Relationships
the record viewer doesn't show you (hidden, visibility rules, business objects
you can't view) are refused

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

### `list-relationships`

```
Usage: list-relationships [options]

List relationships for a business object (--json adds each one's info)

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name
  --page <n>        Fetch only this single page (default: all relationships)
  --per-page <n>    Items per page (single-page mode) (default: "50")
  --all             Fetch all relationships (this is now the default)
```

### `list-reports`

```
Usage: list-reports [options]

List reports. Each report carries its own info (what the report is for, how to
read it; once per session like all info); text output lists it under the table

Options:
  --page <n>      Fetch only this single page (default: all reports)
  --per-page <n>  Items per page (single-page mode) (default: "50")
  --all           Fetch all reports (this is now the default)
```

### `list-roles`

```
Usage: list-roles [options]

List roles

Options:
  --page <n>      Fetch only this single page (default: all roles)
  --per-page <n>  Items per page (single-page mode) (default: "50")
  --all           Fetch all roles (this is now the default)
```

### `list-tag-groups`

```
Usage: list-tag-groups [options]

List tag groups

Options:
```

### `list-user-tasks`

```
Usage: list-user-tasks [options]

List tasks assigned to the current user

Options:
```

### `list-users`

```
Usage: list-users [options]

List users

Options:
  --page <n>      Fetch only this single page (default: all users)
  --per-page <n>  Items per page (single-page mode) (default: "50")
  --all           Fetch all users (this is now the default)
```

### `list-workflow-milestones`

```
Usage: list-workflow-milestones [options]

List workflow milestones for a business object (--json adds each one's info)

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name
  --all             Fetch the full set via /init/milestones
```

### `list-workflow-steps`

```
Usage: list-workflow-steps [options]

List workflow steps for a business object (--json adds each one's info)

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name
```

### `list-workflow-tasks`

```
Usage: list-workflow-tasks [options]

List workflow tasks for a business object (--json adds each one's info)

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name
```

### `logout`

```
Usage: logout [options]

Clear stored auth tokens

Options:
```

### `mark-all-notifications-read`

```
Usage: mark-all-notifications-read [options]

Mark every unread Feed item read (the Feed page "Mark all as read"), or one
record's with --record-id

Options:
  --record-id <id>  Only this record's items
  --mediums <list>  Comma-separated: feed, popup, email, sms, mobile (default
                    feed) (default: "feed")
```

### `mark-notification-read`

```
Usage: mark-notification-read [options] <notification-id>

Mark one of your Feed items (or with --popup, a popup) as read

Arguments:
  notification-id  Notification id

Options:
  --popup          It is a popup notification
```

### `parse-filter`

```
Usage: parse-filter [options]

Validate a Logic Builder formula (e.g. '[Status] = "Won" AND
INMILESTONE("Lead")') against a business object without saving it. Prints the
normalized formula; --json prints the saved GroupFilter JSON.

Options:
  --bo-id <id>         Business object ID the rules are rooted on
  --bo-name <name>     Business object name (resolved to ID)
  --bo <id>            Same as --bo-id
  --text <formula>     Formula text
  --report             Report filter: permit
                       MILESTONEFIRSTENTERED/MILESTONELASTENTERED/STEPFIRSTOCCURRED/STEPLASTOCCURRED
                       and ? blank values
  --report-id <id>     Parse as this report's filter: implies --report, roots
                       on its business object and permits COLUMN("…") /
                       CUSTOMFIELD("…") for its custom columns and fields
  --allow-blank        Permit ? blank values (folder filters)
  --allow-entry-dates  Permit milestone/step entry-date rules
```

### `pathway-run`

```
Usage: pathway-run [options] [command]

Run a pathway from the command line like the web app does: automatic steps run,
server steps run on the server, and each screen that needs a person is shown as
text and answered with submit

Options:

Commands:
  start [options]                Start a pathway run, exactly as the web app
                                 runs it; stops at the first screen that needs
                                 input
  test [options] <scenarios...>  Run scripted scenario files (or folders of
                                 them) and check their expectations; exit 1 if
                                 any check fails. A scenario can create its own
                                 records first ("setup") and archives them
                                 afterwards.
  save-test [options]            Turn a run into a test scenario: what was
                                 entered on each screen becomes the answers,
                                 and what the run did (how it ended, steps
                                 reached and skipped, records created, changes
                                 to the starting record) becomes the
                                 expectations
  cleanup [options]              Archive a run's test records: the copy it
                                 started on (--copy-of) and every record it
                                 created
  available [options]            Which pathways a user could start from a
                                 launch point (a record's Pathways tab, the
                                 dashboard, a folder or a task), each with its
                                 designer info (what it is for: read it to
                                 decide whether a pathway does what the user
                                 asked), and with --all the reason for each one
                                 left out. Like all info, a pathway's is shown
                                 once per session (later calls leave it out;
                                 --with-info repeats it)
  show [options]                 Show a run: the screen it is waiting on, or
                                 how it ended
  submit [options]               Answer the paused screen (only what that
                                 screen allows) and continue the run
  act [options]                  A non-advancing action on the paused screen,
                                 e.g. a lookup search: --set search=Acme [--set
                                 page=2]
  cancel [options]               Cancel a paused run (like closing the web
                                 modal). Work already saved stays saved.
  list [options]                 List saved pathway runs, newest first
  log [options]                  A run's trace: steps, busy messages, every
                                 server request and reply
  delete [options]               Delete a saved run and its log from this
                                 computer
  help [command]                 display help for command
```

### `pathway-run start`

```
Usage: pathway-run start [options]

Start a pathway run, exactly as the web app runs it; stops at the first screen
that needs input

Options:
  --pathway-id <id>          Pathway ID
  --pathway-name <name>      Pathway name (resolved to ID)
  --record-id <id>           Starting record ID
  --copy-of <id>             Start on a fresh copy of this record (its editable
                             fields and links), made just for this run
  --copy-set <Field=value>   With --copy-of: a field value for the copy
                             (repeatable) (default: [])
  --task-id <id>             Launch by completing this task on --record-id
                             (Complete Task steps complete it)
  --folder-id <id>           Launch from this folder's header
  --from <point>             Where it is launched: record | dashboard | folder
                             | task (default: from the other flags)
  --mode <mode>              real-world (default: only launches the user could
                             make in the web app) | super-user (also when the
                             pathway's visibility criteria don't match the
                             record; Admin or Designer only, this run only)
  --as-user <id|email|name>  Run as another account member (admin impersonation
                             session)
  --tag <tag>                Pre-run tag choice for created records: "<tag
                             name|id>" or "<group id>=<tag>" (repeatable)
                             (default: [])
  --answers <file|json>      Scenario: JSON array of scripted answers, consumed
                             in order
  --strict                   Fail (exit 4) instead of pausing when a screen has
                             no usable scripted answer
```

### `pathway-run test`

```
Usage: pathway-run test [options] <scenarios...>

Run scripted scenario files (or folders of them) and check their expectations;
exit 1 if any check fails. A scenario can create its own records first
("setup") and archives them afterwards.

Arguments:
  scenarios   Scenario JSON files or folders

Options:
  --keep      Keep the records each test created, even when it passed (prints
              their ids)
```

### `pathway-run save-test`

```
Usage: pathway-run save-test [options]

Turn a run into a test scenario: what was entered on each screen becomes the
answers, and what the run did (how it ended, steps reached and skipped, records
created, changes to the starting record) becomes the expectations

Options:
  --run <id>     Run ID (default: the most recent run)
  --out <file>   Write the scenario here (default: print it)
  --name <name>  Test name
  --copy-start   Run the test on a fresh copy of the starting record each time
                 (a "setup" entry), so it can run again and again
```

### `pathway-run cleanup`

```
Usage: pathway-run cleanup [options]

Archive a run's test records: the copy it started on (--copy-of) and every
record it created

Options:
  --run <id>  Run ID (default: the most recent run)
```

### `pathway-run available`

```
Usage: pathway-run available [options]

Which pathways a user could start from a launch point (a record's Pathways tab,
the dashboard, a folder or a task), each with its designer info (what it is
for: read it to decide whether a pathway does what the user asked), and with
--all the reason for each one left out. Like all info, a pathway's is shown
once per session (later calls leave it out; --with-info repeats it)

Options:
  --record-id <id>           Record viewer of this record
  --task-id <id>             Completing this task (with --record-id)
  --folder-id <id>           This folder's header
  --from <point>             record | dashboard | folder | task (default: from
                             the other flags)
  --as-user <id|email|name>  Check for another account member (admin
                             impersonation session)
  --all                      Also list the pathways that are not available,
                             with why
```

### `pathway-run show`

```
Usage: pathway-run show [options]

Show a run: the screen it is waiting on, or how it ended

Options:
  --run <id>  Run ID (default: the most recent run)
```

### `pathway-run submit`

```
Usage: pathway-run submit [options]

Answer the paused screen (only what that screen allows) and continue the run

Options:
  --run <id>             Run ID (default: the most recent run)
  --set <key=value>      A field or form value, e.g. --set "Status=Won"
                         (repeatable) (default: [])
  --yes                  Answer a Yes/No question with Yes
  --no                   Answer a Yes/No question with No
  --select <items>       Pick listed item(s) by number or id, comma-separated
  --file <path>          Local file to upload / sign with (repeatable)
                         (default: [])
  --skip                 Use the screen's "continue without" option where it
                         has one
  --data <json>          Raw result exactly as the web screen would return it
                         (bypasses the screen's checks)
  --answers <file|json>  Scripted answers for the screens after this one
  --strict               Fail (exit 4) instead of pausing when a later screen
                         has no scripted answer
```

### `pathway-run act`

```
Usage: pathway-run act [options]

A non-advancing action on the paused screen, e.g. a lookup search: --set
search=Acme [--set page=2]

Options:
  --run <id>         Run ID (default: the most recent run)
  --set <key=value>  Action input (repeatable) (default: [])
  --select <items>   Pick / toggle listed item(s) by number or id,
                     comma-separated
  --file <path>      Local file to upload (repeatable) (default: [])
```

### `pathway-run cancel`

```
Usage: pathway-run cancel [options]

Cancel a paused run (like closing the web modal). Work already saved stays
saved.

Options:
  --run <id>  Run ID (default: the most recent run)
```

### `pathway-run list`

```
Usage: pathway-run list [options]

List saved pathway runs, newest first

Options:
  --limit <n>  How many (default: "20")
```

### `pathway-run log`

```
Usage: pathway-run log [options]

A run's trace: steps, busy messages, every server request and reply

Options:
  --run <id>  Run ID (default: the most recent run)
  --api       Include full request inputs and outputs
```

### `pathway-run delete`

```
Usage: pathway-run delete [options]

Delete a saved run and its log from this computer

Options:
  --run <id>  Run ID
```

### `pin-notification`

```
Usage: pin-notification [options] <notification-id>

Pin (or with --unpin, unpin) one of your Feed items

Arguments:
  notification-id  Notification id

Options:
  --unpin          Unpin instead
```

### `run-report`

```
Usage: run-report [options]

Run a report and print its rows (the web app's report view; in real-world mode
only reports the acting user can open). Also gives the report's own info
(report {id, name, info}: what it is for, how to read it), its root business
object {id, name, info} and each column's info — the column's own info when the
report has one on it, otherwise the info of the field the column shows (for a
column through relationships, the end field's, with the relationships on the
path and their info); count, milestone, step and calculated columns have only
their own, a Time Between / Time Span column on one field falls back to that
field's. Filter-only Custom Fields (compared by the filter, not shown) come
with their own info. --json keeps the server's { rows, meta } and adds report,
business_object, columns [{name, column_type, field_id?, relationship_path?,
info}] and custom_fields [{name, column_type, info}]; text output prints a
"Report info:", a "Business object info:", a "Column info:" and a "Custom Field
info" block under the rows (only what has info). Like all info, each is shown
once per session; later runs leave it out (pass --with-info to repeat)

Options:
  --report-id <id>      Report ID
  --report-name <name>  Report name (resolved to ID)
  --page <n>            Page number (default: "1")
  --per-page <n>        Rows per page (default: "50")
  --all                 Every row in one call (--page / --per-page are ignored)
  --user-filter <json>  Runtime filters for the report's user-filterable
                        columns, e.g.
                        '[{"entity_id":12,"logic":"equal","values":["John"]}]'
  --include-archived    Include archived records (overrides the report's
                        setting)
```

### `run-step`

```
Usage: run-step [options]

Run a workflow step on a record (triggers milestone evaluation)

Options:
  --record-id <id>  Record ID
  --step-id <id>    Workflow step ID
  --bo-id <id>      Business object ID (auto-resolved from the record if
                    omitted)
  --bo-name <name>  Business object name (resolved to ID)
```

### `search`

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

### `send-message`

```
Usage: send-message [options]

Post a message on a record

Options:
  --record-id <id>  Record ID
  --message <text>  Message text
```

### `send-record-email`

```
Usage: send-record-email [options]

Send an email from a record

Options:
  --record-id <id>  Record ID
  --data <json>     JSON body (to, subject, body, ...)
```

### `set-appointment`

```
Usage: set-appointment [options] <record-id>

Schedule or change a record's appointment field (creates or updates the
calendar event that owns it)

Arguments:
  record-id             Record id

Options:
  --field <id|name>     Appointment field on the record
  --title <text>        Event title
  --start <date>        Start: "YYYY-MM-DD HH:mm", YYYY-MM-DD, an ISO
                        timestamp, today, tomorrow, +3d (your time zone)
  --end <date>          End (same formats). Default: a moved start keeps the
                        length, a new event lasts an hour
  --calendar <id|name>  Calendar
  --all-day             All day: the dates are whole days on the account
                        calendar
  --no-all-day          Timed (turning all day off gives 8–9am on the same
                        days)
  --multi-day           Spans several days (default: follows the dates)
  --no-multi-day        One day (brings the end back to the start day)
  --color <color>       Event color, e.g. #c33 ("" for the calendar color)
  --location <text>     Location
  --description <text>  Description
```

### `set-filter`

```
Usage: set-filter [options] <target> <id>

Replace a stored Logic Builder filter from formula text. Parses against the
business object the filter is rooted on, validates the pipeline with the API
(as the Logic Builder does), then saves the object with only its filter
changed. The PATH CLI changes folder, folder-sticker and report filters (with
Edit on them); the other targets are Designer work for the PATH Designer CLI.

Arguments:
  target            Where the filter lives (see Targets below)
  id                Id of the object holding the filter

Options:
  --text <formula>  Formula text, e.g. '[Status] = "Won" AND HASSTEP("Send
                    Quote")'
  --clear           Remove the filter (saved as null, as the builder does when
                    emptied)
  --bo <id>         Business object ID (task targets)
  --pathway <id>    Pathway ID (fork, table-filter)
  --sticker <id>    Folder sticker ID (folder-sticker)
  --dry-run         Parse and validate, print the pipeline, but do not save
```

### `unarchive-record`

```
Usage: unarchive-record [options]

Unarchive a record

Options:
  --record-id <id>  Record ID
```

### `unarchive-record-file`

```
Usage: unarchive-record-file [options]

Unarchive a file on a record

Options:
  --record-id <id>  Record ID
  --file-id <id>    File ID
```

### `unlink-record`

```
Usage: unlink-record [options]

Unlink a record from its target on a given relationship

Options:
  --record-id <id>        Source record ID
  --relationship-id <id>  Relationship ID to clear
```

### `update-calendar-event`

```
Usage: update-calendar-event [options] <id>

Update a calendar event, or link/unlink it to a record appointment field

Arguments:
  id                    Calendar event id

Options:
  --title <text>        Event title
  --start <date>        Start: "YYYY-MM-DD HH:mm", YYYY-MM-DD, an ISO
                        timestamp, today, tomorrow, +3d (your time zone)
  --end <date>          End (same formats). Default: a moved start keeps the
                        length, a new event lasts an hour
  --calendar <id|name>  Calendar
  --all-day             All day: the dates are whole days on the account
                        calendar
  --no-all-day          Timed (turning all day off gives 8–9am on the same
                        days)
  --multi-day           Spans several days (default: follows the dates)
  --no-multi-day        One day (brings the end back to the start day)
  --color <color>       Event color, e.g. #c33 ("" for the calendar color)
  --location <text>     Location
  --description <text>  Description
  --record <id>         Attach the event to this record's appointment field
  --field <id|name>     The appointment field (default: the record has only
                        one)
  --unlink              Detach the event from its record (clears the record's
                        appointment field)
  --data <json>         Raw JSON body, passed through as-is (start_date /
                        end_date as ISO timestamps, UTC); flags override its
                        keys
```

### `update-custom-field`

```
Usage: update-custom-field [options] <report-id> <field>

Change a report Custom Field's name, definition (--expression, or --from/--to)
or info (--info / --info-file). Filter rules keep working: they reference the
field's key, not its name.

Arguments:
  report-id                              Report ID
  field                                  Custom Field name or column_key

Options:
  --name <name>                          New name
  --expression <formula>                 New calculation
  --from <source>                        New Time Between start
  --to <source>                          New Time Between end
  --from-occurrence <first|most_recent>  For milestone/step sources: first | most_recent (default most_recent)
  --to-occurrence <first|most_recent>    For milestone/step sources: first | most_recent (default most_recent)
  --info <text>                          New info for the Custom Field ("" clears it)
  --info-file <path>                     Read the new info from a text or markdown file (instead of --info)
```

### `update-folder`

```
Usage: update-folder [options]

Change a folder's info or settings, saved the way the folder form saves it
(everything else as stored). Needs Edit on the folder. --data keys:
folder_name, include_archived, columns, sorts, default_sort_field,
default_sort_direction, map_enabled, map_relationship_id, info, sticker_info
(criteria: set-filter folder; sticker criteria: set-filter folder-sticker)

Options:
  --folder-id <id>      Folder ID
  --folder-name <name>  Folder name (requires --bo-id or --bo-name)
  --bo-id <id>          Business object ID (for --folder-name)
  --bo-name <name>      Business object name (for --folder-name)
  --info <text>         Info: what it is for, who uses it, how to read it —
                        shown with it to people and AI agents ("" clears it; at
                        most 50,000 characters)
  --info-file <path>    Read the info from a text or markdown file (instead of
                        --info)
  --data <json>         JSON object of folder keys to change, e.g.
                        '{"folder_name":"Active
                        Jobs","info":"…","sticker_info":{"Late":"Past its due
                        date"}}' — sticker_info sets each named sticker's own
                        info (by sticker id or name; null clears it)
  --dry-run             Print the save body without saving
```

### `update-message`

```
Usage: update-message [options]

Update a message on a record

Options:
  --record-id <id>   Record ID
  --message-id <id>  Message ID
  --data <json>      JSON body with fields to update
```

### `update-notification-settings`

```
Usage: update-notification-settings [options]

Turn your notification channels on or off, or change your notification phone
number

Options:
  --set <key.medium=on|off>  e.g. task.email=off or notification:42.text=on
                             (repeatable; keys from get-notification-settings
                             [--resolved])
  --phone <number>           The phone number text notifications go to ("" to
                             remove)
  --data <json>              Raw request body: { settings: [...], phone_number?
                             } (as the web sends it)
```

### `update-profile`

```
Usage: update-profile [options]

Change your name or time zone, the way the profile settings modal saves them

Options:
  --first-name <name>  First name (also written to your Person record)
  --last-name <name>   Last name (also written to your Person record)
  --time-zone <zone>   account (the account's zone), local (this device's), or
                       an IANA zone such as America/Chicago
```

### `update-record`

```
Usage: update-record [options]

Update a record by ID

Options:
  --record-id <id>  Record ID
  --data <json>     JSON body with fields to update
```

### `update-record-file`

```
Usage: update-record-file [options]

Update a file on a record

Options:
  --record-id <id>  Record ID
  --file-id <id>    File ID
  --data <json>     JSON body with fields to update
```

### `update-report`

```
Usage: update-report [options]

Change a report's info or settings, saved the way the report form saves it
(columns, filter, scheduling as stored). Needs Edit on the report. --data keys:
name, include_archived, info, column_info (filter: set-filter report; Custom
Fields: create-custom-field / update-custom-field)

Options:
  --report-id <id>      Report ID
  --report-name <name>  Report name (resolved to ID)
  --info <text>         Info: what it is for, who uses it, how to read it —
                        shown with it to people and AI agents ("" clears it; at
                        most 50,000 characters)
  --info-file <path>    Read the info from a text or markdown file (instead of
                        --info)
  --data <json>         JSON object of report keys to change, e.g.
                        '{"name":"Open
                        Quotes","info":"…","column_info":{"Total":"Quoted price
                        incl. tax"}}' — column_info sets each named column's
                        own info (by column id, column_key or name; null clears
                        it, and the column shows its field's info again)
  --dry-run             Print the save body without saving
```

### `upload-record-file`

```
Usage: upload-record-file [options]

Upload one or more local files to a record

Options:
  --record-id <id>          Record ID
  --file <paths...>         Path(s) of the local file(s) to upload
  --file-type <id-or-name>  File type for the upload(s): an id or a name from
                            list-file-types on the record's BO
  --comment <text>          Comment to attach to each uploaded file
```

### `use-account`

```
Usage: use-account [options] [account-id]

Set the default account for subsequent commands (persists to ~/.path-cli/.env),
and show the account's info (what the business does, written by its designers)

Arguments:
  account-id  Account ID to use (omit to print the current default)

Options:
```

### `web-link`

```
Usage: web-link [options] <target> <id-or-name>

Print the PATH web app link to a record, folder or report (the same link the
app opens and shares)

Arguments:
  target          What to link to (choices: "record", "folder", "report")
  id-or-name      Record id; folder or report id or name

Options:
  --bo <id|name>  Business object, to pick a folder or report name that several
                  share
  --tab <tab>     Record only: open this tab (main, files, messages, email,
                  history, pathways, addresses, appointments,
                  contactsAndPeople)
```
