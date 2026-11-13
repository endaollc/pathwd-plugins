# Workflow: records

> **Commands:** every command in this command is a PATH CLI command line: the command name and its flags. On PATH WD Remote MCP each tool runs the commands of one area: a command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`, and each command's page in [the command index](../commands.md) gives any command's syntax and the tool that runs it.

**Covers:** records: creating them, changing field values, finding them by name or by conditions, opening one, linking records, running a workflow step, archiving and unarchiving. Commands: `search`, `find-records`, `get-record`, `create-record`, `update-record`, `link-record` / `unlink-record`, `archive-record` / `unarchive-record`, `run-step`. Value forms: the [`path-cli` guide](../commands.md)'s [field-values.md](../cli/field-values.md); each command's syntax and output: its command note.

The account and session: the [`path-session` guide](../session.md). Every command's syntax: the [`path-cli` guide](../commands.md).

## What to ask the user

**For creating a record (all of these, before anything is saved):**
1. **What type of record** — the business object (e.g. "Job", "Invoice", "Customer")
2. **Field values** — at least one field value to set (e.g. "department is Residential", "name is John Smith")

**For updating a record (all of these, before anything is saved):**
1. **Which record** — a record ID or enough info to find it (e.g. "Job 12345", "the invoice for Smith")
2. **What to change** — at least one field value to update

**For finding records:**
1. **What to search for** — a name, number or other text (`search`), or conditions on field values (`find-records`, see "Finding Records")

**For linking records:**
1. **Which two records** — the source and target record
2. **What relationship** — how they're connected (usually inferred from the BO types)

**For archiving or unarchiving records:**
1. **Which records** — record IDs or enough info to find each one
2. **An explicit yes** after you've listed the records that will be archived

**Optional (use defaults if not mentioned — do NOT ask):**
- Whether to verify the result after (defaults to yes for create, no for update)

## Steps

### First: is there a pathway or task for this?

When the user wants something done on a particular record — create a quote for a job, schedule a delivery, send an invoice to accounting, close out a service call — open the record first (`get-record --record-id <id>`). Whatever kind of record it is, it comes with what can be done on it right now, each with its designer context:

- `Pathways you can start on this record (<n>):` the pathways its Pathways tab lets the user start (the same list as `pathway-run available --record-id <id>`), one `<id>  <name>` line each with its `context:` under it;
- `Open tasks on this record (<n>):` the tasks already on it, one `<id>  <name> — due …, assigned to …` line each with its context, and `completing it runs pathway <id> "<name>"` (with that pathway's context) when the task carries a pathway. `only the assignee can finish it` on the line means the user can't complete it.

Read each one's context (what it is for) and match it to the request, not just its name: a "Create Quote" pathway, or a "Prepare Quote" task whose pathway builds the quote, both fit "create a quote for this job". When one fits, offer it instead of doing the work by hand (creating records, editing fields): the pathway or task is the business's own process and does the related work too (links, follow-up tasks, emails, milestones). Run a pathway with the [`cli-pathways` workflow](../../../pathwd-pathways/references/workflows/pathways.md). Run a task's pathway by completing the task, which also closes it: `pathway-run start --pathway-id <pathway id> --record-id <id> --task-id <task id>`. If two could fit, say what each is for and ask. Context already shown this session comes back as `(shown earlier this session)`; `--with-context` repeats it.

For a new record, or a request not tied to a record, check the dashboard pathways in the session context (`session-context` shows them again).

Work directly only when nothing fits or the user asks for a plain field change, and say so in one line ("No pathway or task on this job creates quotes, so I'll build it directly").

### Creating a Record

#### Step 1: Look Up the Business Object and Fields

```
# Find the BO
--account <ID> list-business-objects

# Get fields to resolve names → IDs and check types
--account <ID> list-fields --bo-name <name>
```

Columns to read: `id` (the `field_id`), `name`, `type` (decides the stored value's form), `req` (`yes`: required on create), `from` (a linked field: nothing writes it here), `options` (a Selection's exact option texts; `get-field --field-id <id>` lists one that is cut with `…` in full, with the field's format and default).

#### Step 2: Build the Data Payload

The `create-record` command takes the fields as an array of field ids and **stored values**:

```json
{
  "fields": [
    { "field_id": 13220, "value": "Residential" },
    { "field_id": 13221, "value": "John Smith" },
    { "field_id": 13225, "value": 5000 },
    { "field_id": 13226, "value": 0.1 },
    { "field_id": 13227, "value": "2026-04-15T17:00:00.000Z" }
  ]
}
```

`--data` values are sent exactly as given, so each must already be in the form PATH stores. The [`path-cli` guide](../commands.md)'s [field-values.md](../cli/field-values.md) has every field type; the ones that differ from what a person types:

- **Percent**: a fraction — 10% is `0.1`, 8.25% is `0.0825` (`10` would be 1000%).
- **Date And Time**: a UTC ISO timestamp. A date-only field stores **noon on the user's clock**: Apr 15 2026 in US Central is `"2026-04-15T17:00:00.000Z"`. Midnight UTC (`T00:00:00.000Z`) shows as the day before in US time zones.
- **Time Span**: seconds — 1 h 30 min is `5400`.
- **Phone Number**: digits only — `"5125550126"`.
- **Number / Currency**: a number, not a string — `1250.5`.
- **Selection**: one option's exact text — `"Residential"`. A Selection holds one option; there is no array value.
- **Boolean**: `true` / `false`. **Text, Long Text, Email, URL**: a string.
- **Blank**: `""` for text-like fields, `null` for the rest. A field left out keeps its value.
- Calculated fields, Notes, Appointments, Tags, links and files aren't set this way (field-values.md says what is used instead).

The record is created as the acting user (the CLI adds `user_id`), with that user's tags.

#### Step 3: Confirm With the User

Before creating, present a plain language summary:

> "Here's what I'm going to create:
>
> **Type:** Job
> **Fields:**
> - Department: Residential
> - Customer Name: John Smith
> - Estimated Amount: $5,000
>
> Does that look right?"

#### Step 4: Create the Record

```
--account <ID> create-record \
  --bo-name <name> \
  --data '{"fields": [{"field_id": 13220, "value": "Residential"}]}'
```

#### Step 5: Verify

```
# The created record by field name, with display values
--account <ID> get-record --record-id <new_id>
```

`get-record` shows field names and display values on the user's clock; `--raw` shows the stored record: header lines (`milestones`, `steps`, `linked records` with relationship ids), then a table of `id | name | value` with each stored value as written in `--data` (strings quoted, numbers and `true`/`false`/`null` bare, dates in UTC). A value cut with `…` prints whole with `--raw --field-id <id>`.

### Updating a Record

#### Step 1: Find the Record

```
# By ID
--account <ID> get-record --record-id <id>

# By name or number
--account <ID> search --query "<text>"
```

Not found on the first search? See "When a record can't be found" below before asking.

#### Step 2: Look Up Fields

```
--account <ID> list-fields --bo-name <name>
```

`list-fields` gives each field's `id`, `type`, `req` and `options` columns, which the payload needs; `get-field --field-id <id>` adds its format, default value and every option. A field whose visibility rule depends on the record is listed, and `update-record` refuses a field the record viewer doesn't show on this record, so pick fields `get-record` shows. `get-record --raw` gives the field ids with their current stored values.

#### Step 3: Update

```
--account <ID> update-record \
  --record-id <id> \
  --data '{"fields": [{"field_id": 13220, "value": "Commercial"}]}'
```

### Finding Records

Two kinds of finding:

- **By name, number or other text** ("the Smith job", "Job 26-5543"): `search`, PATH's quick search. It matches text in the business object's quick-search fields and returns one page (15 by default; `--page` / `--per-page` for more).
- **By conditions on field values** ("invoices where the balance is over 1000", "jobs won last month", "retail jobs waiting on a deposit"): `find-records`, an advanced search with a Logic Builder formula over every record of one business object, nothing saved. The [`cli-find-records` workflow](../../../pathwd-find-records/references/workflows/find-records.md) has the steps; the [`path-cli` guide](../commands.md)'s [find-records-syntax.md](../../../pathwd-find-records/references/find-records-syntax.md) has the syntax.

```
# Quick search
--account <ID> search --query "Smith" --bo-name Jobs

# Include archived records (left out by default)
--account <ID> search --query "Smith" --bo-name Jobs --include-archived

# Records matching conditions
--account <ID> find-records --bo-name Invoice --logic '[Balance] > 1000'

# List records in a folder
--account <ID> list-records --folder-name "Active Jobs" --bo-name Jobs

# The Jobs the user viewed recently
--account <ID> list-records --bo-name Jobs

# Get a specific record
--account <ID> get-record --record-id <id>
```

`list-records` always needs a business object: alone it gives the records of that business object the user viewed recently; with a folder, the folder's records. It never lists a whole business object; `find-records` does that.

#### When a record can't be found

When the user means one particular record ("the Smith job", "the invoice for Katz Plumbing", "Job 26-5543") and the first search finds nothing that matches, don't give up and don't ask yet. Try other spellings and forms first, several searches in a row:

- **Spelling variants and typos:** doubled or dropped letters, swapped letters, sound-alikes (Smith / Smyth / Schmidt, Katz / Kats, McDonald / MacDonald / Mc Donald). Names often arrive by voice dictation, so try what the words sound like.
- **Parts of the name:** the surname alone, the first word of a company name, without suffixes (LLC, Inc, Co, & Sons), "and" ↔ "&", with and without punctuation, spaces or hyphens (O'Brien / OBrien, 26-5543 / 265543).
- **Abbreviations and full forms:** St / Street, Bros / Brothers, Intl / International; numbers as digits and as words.
- **Through a related record:** search the customer, contact or address the user named, then find the record on it with `list-related-records`.
- **Another type:** search without `--bo-name` when the type might be wrong (a "job" that is really a Project or Service), and `--include-archived` last (say so if the match is archived).

Stop as soon as one search gives a clear match. If several are close, list them (name or number and one detail each) and ask which. Only when the variants come up empty, tell the user what you searched for and ask for another detail (number, address, customer).

### Related Records (the record viewer's relationship cards and tabs)

```
# Every related list the record viewer shows you on this record:
# direct relationships (records it links to) and inverse tabs (records linking to it)
--account <ID> list-related-records --record-id <id>

# One list's records, with the record viewer's columns
--account <ID> list-related-records --record-id <id> --relationship-name "Quotes"
--account <ID> list-related-records --record-id <id> --relationship-id 55 --direction direct
```

- The overview is a table of `id` (the relationship id `--relationship-id` takes), `name` (the relationship, or an inverse tab's title), `dir` (`direct` / `inverse`), `where` (where the viewer shows it: `relationships`, `contacts`, `people`, `addresses`, `tab`), `bo` (the related business object) and `count`, with the relationships' and business objects' context under it.
- A list is a table of `id`, `name` and the record viewer's columns for that list, as display text; under it `Row details:`, `Summary:`, `Totals:`, `Linked record ids` (the record behind each lookup column) and each column's context. Open any record with `get-record --record-id <id>` (its `Related:` lines give the id of each record it links to).
- Only lists the record viewer shows the acting user: a hidden relationship, one failing its visibility rules, or one to a business object they can't view is refused.

### Designer context on records

`get-record`, `list-records --folder-id`, `list-related-records` and `run-report` print the context of what they show, under the record or table: the folder's or report's own (`Folder context`, `Report context`: what it is for, who uses it), the business object's (`Business object context`), each field's / column's (`Field context`, `Column context`: a report column's own context when it has one, otherwise its field's; a report's filter-only Custom Fields under `Custom Field context`) and each relationship's (`Relationship context`). Read it before changing a record: it is what the designer (or the folder's / report's owner) meant it for. Context is given once per session, with no exceptions: later calls, including a second view of the same folder or report, leave out what you were already shown (`<label>: (shown earlier this session)`). Pass `--with-context` to see it again. `list-records --folder-id <id>` prints the folder's records as a table: `id`, `name`, `milestones`, then the folder's own columns.

To make a new report (`create-report`), or to change a report's context, column context, filter and Custom Fields, use the [`cli-reports-skill` workflow](../../../pathwd-find-records/references/workflows/reports.md).

### Linking Records

```
# Find the relationship
--account <ID> list-relationships --bo-name <source_bo>

# Create the link
--account <ID> link-record \
  --record-id <source_record_id> \
  --related-record-id <target_record_id> \
  --relationship-id 55

# Clear the link (drops the pointer; aggregates on the unlinked target refresh)
--account <ID> unlink-record \
  --record-id <source_record_id> \
  --relationship-id 55
```

### Running a Workflow Step

Running a workflow step on a record triggers milestone criteria re-evaluation. If the step satisfies a milestone's include criteria, the record enters that milestone. This is how records progress through their lifecycle.

> **Note:** Milestones cannot be directly set — they are entered and exited based on their underlying criteria being true. Running the right workflow step is how you move a record through milestones.

```
# List available steps for the BO
--account <ID> list-workflow-steps --bo-name <name>

# Run a step on a record
--account <ID> run-step \
  --record-id <record_id> \
  --bo-name <name> \
  --step-id 12
```

### Archiving and Unarchiving Records

Archiving hides a record without deleting it. An archived record drops out of search, folders and reports (unless a folder is set to include archived records), and stops counting toward totals and counts on the records it's linked to. Its data, links and history are kept, and it can be unarchived at any time.

The CLI archives through the same **Archive Record** step a pathway uses, so it behaves exactly like archiving in the app.

#### Step 1: Find the Records

```
--account <ID> get-record --record-id <id>
# or
--account <ID> search --query "<text>" --bo-name <name>
```

#### Step 2: Confirm

List every record by its name or number, not just a count:

> "I'll archive these 3 Customers:
> - Smith Plumbing (CUST-1042)
> - Smith Plumbing LLC (CUST-1187)
> - Smith Plumbing Inc (CUST-1203)
>
> They'll be hidden from search, folders and reports, and stop counting toward totals on linked records. Nothing is deleted and they can be unarchived later. Go ahead?"

#### Step 3: Archive

```
# One record
--account <ID> archive-record --record-id <id>

# Several records in one go (archived one at a time)
--account <ID> archive-record --record-ids <id1>,<id2>,<id3>
```

`--bo-id <id>` skips looking up the record's business object. It only works with a single `--record-id`, and you rarely need it.

Each record gets one line: `Archived record …`, `Already archived: record …` or `Not archived: record … — <reason>`, with a count line (`[1 archived, 1 already archived, 1 failed]`) when there are several. Report all three groups. If some failed, the others were still archived: say which ones failed and why.

#### Unarchiving

Unarchiving brings a record back everywhere and makes it count toward linked totals again. Confirm which record first. The command takes one record at a time:

```
--account <ID> unarchive-record --record-id <id>
```

To find archived records, use a folder that includes archived records, or the record ID the user gives you.

### Tags: Why Someone Can't See a Record

Some accounts use **tags** to limit which records each user sees, e.g. a "Location" tag group with tags "Austin" and "Houston". Records carry a tag for each tag group, set from the tags of the user who created them. Users only see records whose tags match their own, everywhere in PATH: folders, reports, tables, recents and bookmarks.

```
--account <ID> list-tag-groups
```

Use this when a user says "my colleague can't see the job I created" or "why don't I see these records". List the tag groups, then compare the record's tag value (in `get-record`) with the user's tags (on the web's Users page, which Admins and User Admins open). A user who has several tags in a group sees records with any of them. Explain the mismatch in plain words ("the job is tagged Houston, and Maria only has the Austin tag"). This plugin can't change a user's tags; an Admin or User Admin does that in PATH's user settings, or with the PATH Designer plugin's `path-users` skill.

## CLI Commands Used

| Command | Purpose |
|---------|---------|
| `list-business-objects` | Find BO by name |
| `list-fields --bo-name <n>` | Get fields for payload building (`get-field --field-id <id>` for one in full) |
| `create-record --bo-name <n> --data <json>` | Create a new record |
| `update-record --record-id <id> --data <json>` | Update an existing record |
| `list-record-notes` / `add-record-note` / `update-record-note` / `delete-record-note --record-id <id> …` (the delete asks for confirmation) | A **Notes field**'s entries (each note separate, with author and time) — never set through `update-record`; see the [`cli-record-activity` workflow](record-activity.md) |
| `get-record --record-id <id>` | Get a single record |
| `list-records --bo-name <n> [--folder-name <f> \| --folder-id <id>]` | A folder's records (the folder's columns), or the business object's recently viewed ones |
| `list-related-records --record-id <id> [--relationship-id <id> \| --relationship-name <name>]` | The record viewer's related lists (direct relationships and inverse tabs), or one list's records with its columns and their context |
| `search --query <text>` | Quick search by name, number or other text (one page, 15 by default) |
| `find-records --bo-name <n> --logic '<formula>'` | Records matching conditions on their fields (the [`cli-find-records` workflow](../../../pathwd-find-records/references/workflows/find-records.md)) |
| `list-relationships --bo-name <n>` | Find relationships for linking |
| `link-record` | Link two records via a relationship |
| `unlink-record` | Clear a record's pointer on a relationship |
| `list-workflow-steps --bo-name <n>` | List available workflow steps |
| `run-step` | Run a workflow step on a record (triggers milestone evaluation) |
| `archive-record --record-id <id>` / `--record-ids <ids>` | Archive one or several records (Archive Record step) |
| `unarchive-record --record-id <id>` | Unarchive a record |
| `list-tag-groups` | List the account's tag groups (who-can-see-what) |

If no PATH CLI command covers what the user asks for, tell them it can't be done here ([`path-cli`](../commands.md) lists every command).

## Key facts and confirmations

- Every command here runs through the PATH WD Remote MCP tool for its area (each command's page in [the command index](../commands.md) names it).
- Users name business objects, fields and records; the commands resolve names to ids, so ids come from the account (`list-fields`, `search`), never from memory.
- Creating, updating, linking and archiving change real data: the user sees what will change, in plain words, and agrees first.
- Users know "the Department field" and "this Job", not `field_id` or `business_object_id`.
- `--data` values are stored values, sent as given: the field's type decides the form ([field-values.md](../cli/field-values.md) in the [`path-cli` guide](../commands.md)). A Selection takes one of its options exactly (the `options` column of `list-fields`, or `get-field --field-id <id>`); when the user's word doesn't match, they pick the option.
- Required fields (`req` = `yes` in `list-fields`) need a value on create; a missing one is asked for.
- A record a search didn't find isn't ambiguous yet: other spellings come first ("When a record can't be found"). Real ambiguity (two matching records, two possible fields) is settled with the user.
- Archiving takes records out of lists and searches and can be undone (`unarchive-record`); it is what "delete a record" means in PATH. The user sees the list of records to archive and confirms, never a broad search's results unseen.
- A link to a specific record opens it on the dashboard, in exactly this form: `https://pathwd.app/accounts/{accountId}/dashboard?record={recordId}`, e.g. `https://pathwd.app/accounts/1234/dashboard?record=6a1f0c2e9b4d3a7e5c8f1234` (`{recordId}` is the record's `id`; `web-link record <id>` prints it). No other path, host or query parameter opens it.
