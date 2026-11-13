# Workflow: create-folder

> **Commands:** every command in this command is a PATH CLI command line: the command name and its flags. On PATHwd each tool runs the commands of one area: a command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`, and each command's page in [the command index](../../../pathwd-guide/references/commands.md) gives any command's syntax and the tool that runs it.

**Covers:** making a folder: a saved list of one business object's records that meet set conditions, shared with chosen roles or users. Commands: `create-folder`, `update-folder`, `set-filter folder`, the folder sticker commands, `list-folders`, `list-records`. Each command's syntax and output: its command note.

The account and session: the [`path-session` guide](../../../pathwd-guide/references/session.md). Every command's syntax: the [`path-cli` guide](../../../pathwd-guide/references/commands.md).

## What to ask the user

Before you do anything, make sure you have all four required pieces of information. If the user's request is missing any of them, ask naturally — don't create anything until you have them all.

**Needed before the folder is created (all four):**
1. **What type of records** — the business object (e.g. "Jobs", "Invoices", "Contacts")
2. **Folder name** — what to call it (e.g. "Active Residential Jobs")
3. **What records to show** — the conditions/rules for which records appear (e.g. "department is Residential and they've been won")
4. **Who can see it** — at least one role or user (e.g. "Sales team", "all users", "John Smith"). **This is not optional.** A folder without access permissions is useless — no one can see it. If the user doesn't mention who should have access, you MUST ask before creating anything.

**Optional (use defaults if not mentioned — do NOT ask):**
- **Columns** — a new folder shows the business object's default columns; create-folder has no column option. Other columns are chosen in the web app's folder form, or after creation with `update-folder --folder-id <id> --data '{"columns":[{"field_id":…}, …]}'` (which makes the folder's columns custom)
- **Sort order** — defaults to none
- **Include archived records** — defaults to no
- **Permission level** — defaults to view only

If the user hasn't specified an account ID, ask which account to use or run the [`path-session` guide](../../../pathwd-guide/references/session.md).

**Example of gathering missing info:**
> User: "Create a folder for Jobs called Won Residential"
> You: "Got it — a folder called Won Residential for Jobs. Two things I still need: What conditions should determine which jobs show up? And who should be able to see this folder — specific roles, specific users, or everyone?"

> User: "Create a folder for Jobs called Active Jobs that shows jobs where the department is Residential"
> You: "Almost there — who should have access to this folder? For example, specific roles like Sales or Admin, specific users, or all users?"

## Steps

### Step 1: Load the business object (always, before writing the filter or running create-folder)

Field names and types and the designers' context decide which fields the filter should use, so load them first:

```
# Find the BO
--account <ID> list-business-objects

# The BO itself, with its context
--account <ID> get-business-object --bo-name <name>

# Its fields, with each field's type and context
--account <ID> list-fields --bo-name <name>

# Its relationships (linked records), with their context
--account <ID> list-relationships --bo-name <name>

# The fields of every related BO a filter leaf reaches through a relationship
--account <ID> list-fields --bo-id <related BO id>

# Milestones / steps, if the request mentions them (e.g. "has been won", "delivery scheduled")
--account <ID> list-workflow-milestones --bo-name <name>
--account <ID> list-workflow-steps --bo-name <name>
```

Columns to read: `list-fields` gives `name`, `type` and `options` (a Selection's exact option texts; a list cut with `…` is printed whole by `get-field --field-id <id>`), with `Field context:` under the table; `list-relationships` gives `name` and `to` (the linked business object and its id, for `--bo-id` above), with `Relationship context:`; the milestone and step lists give `name` (what a formula uses), with their context under each table. `find-records --bo-name <name> --catalog` lists the same names at once, in the form a formula writes them (`[Job Main Customer.Customer Name]`).

Skip what you already loaded this session. If your context no longer has it (for example after compaction), run it again with the global `--with-context` option (`--account <ID> --with-context list-fields …`): context is shown once per session, so a plain re-run leaves it out.

Map each of the user's words to a real field, milestone or step. Read the context: it says what each field is for and often settles which one the user means. If two could fit, ask.

### Step 2: Build the Filter Logic

`create-folder --logic '<formula>'` takes a Logic Builder **formula** — the same language used everywhere else in PATH (milestone criteria, task criteria, report filters, etc.). Full syntax, operators, names and examples live in the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md) — load that skill and follow it rather than guessing at syntax here. Folders additionally allow `?` (fill in later) and milestone/step entry-date rules (`MILESTONEFIRSTENTERED`, etc.). There is no `NOT(...)`: negate a rule with its own negative form (`NOTHASSTEP`, `NOTINMILESTONE`, `ISNOTANYOF`, `NOTCONTAINS`, `!=`; the full table is in the syntax skill).

Validate before creating:
```
--account <ID> parse-filter --bo-name <name> --text '<formula>' --allow-blank --allow-entry-dates
```

`--logic-raw <json>` takes a stored filter as JSON, for a filter the formula language can't express. Rarely needed.

### Step 3: Confirm With the User

Before creating anything, present a plain language summary:

> "Here's what I'm going to create:
>
> **Folder:** "Active Residential Jobs"
> **Shows:** Jobs where Department is Residential AND milestone is Won
> **Access:** Sales role, Admin role (view only)
>
> Does that look right?"

### Step 4: Create the Folder

The `create-folder` command handles folder creation AND permissions in one step:

```
--account <ID> create-folder \
  --bo-name <name> \
  --folder-name "<folder name>" \
  --logic '[Department] = "Retail" AND INMILESTONE("Qualified")' \
  --roles "Sales,Admin" \
  --role-permissions "view"
```

Try `--dry-run` first to print the request bodies (folder + permissions) without creating anything, if you want to double-check before saving.

**Full options:**

| Option | Description |
|--------|-------------|
| `--bo-name <name>` | Business object name (or `--bo-id <id>`) |
| `--folder-name <name>` | Name for the folder (required) |
| `--logic <formula>` | Logic Builder formula — see the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md) |
| `--logic-raw <json>` | A stored filter as JSON (rarely needed) |
| `--dry-run` | Print the request bodies without creating anything |
| `--roles <names>` | Comma-separated role names to grant access |
| `--role-permissions <perms>` | Comma-separated: `view`, `edit`, `delete` (default: view) |
| `--users <names>` | Comma-separated user full names to grant access |
| `--user-permissions <perms>` | Comma-separated: `view`, `edit`, `delete` (default: view) |
| `--map-relationship <name-or-id>` | Relationship (name or ID) from this BO to the account's Address BO — enables Folder Map (`map_enabled` + `map_relationship_id`). Only valid if such a relationship exists. |
| `--context <text>` / `--context-file <path>` | The folder's context: one or two sentences on what the folder is for, who uses it and what its records have in common. Give every new folder one (from what the user told you; don't invent a purpose). People and AI agents see it whenever they open the folder |

**Changing a folder later:** `update-folder --folder-id <id> --context "<text>"` (or `--context-file`, or `--data '{"folder_name":"…","context":"…"}'`) saves it the way the folder form does, everything else kept; `--context ""` clears the context. A sticker's own context: `--data '{"sticker_context":{"<sticker name>":"…"}}'`. It needs Edit on the folder. Criteria go through `set-filter folder <id> --text '<formula>'`.

**Folder stickers:** a folder shows its business object's stickers (Standard); to give it a sticker of its own, `create-folder-sticker --folder-id <id> --name "Big Job" --icon flag --color orange --logic '[Total] > 10000'` (prints the new sticker's id; a Standard folder switches to Custom stickers and keeps every business object sticker). `update-folder-sticker` / `delete-folder-sticker --sticker <id or name>` change or delete one of its own; `hide-folder-sticker` / `show-folder-sticker --sticker <name>` hide or show a business object sticker on this folder only; `reset-folder-stickers` puts it back to Standard. `delete-folder-sticker`, and a `reset-folder-stickers` that would delete own stickers or show hidden ones again, first answer "NOT DELETED: this needs confirmation", say what they would delete and give a code: show the user exactly that, ask, and only after an explicit yes run the SAME command again with `--confirm <code>` (never without that yes, never a guessed code). `list-folders --bo-name <n>` lists each folder's stickers (id, own or inherited, hidden). All need Edit on the folder. Reports work the same way with `update-report --report-id <id> --context "<text>"`.

**Permission levels:**
- **View only** (default): `--role-permissions "view"`
- **Can edit**: `--role-permissions "view,edit"`
- **Full control**: `--role-permissions "view,edit,delete"`

If the user just says "give access" or "can see it", default to **view only**. If they say "can manage" or "full access", use **full control**.

**"All users" / "everyone" access:** If the user says "everyone can see it", look up the system "all-user" role via `list-roles` and pass it with `--roles`.

### Step 5: Verify (Optional)

If the user wants to test the folder:

```
# List folders to confirm it exists
--account <ID> list-folders --bo-name <name>

# Check records in the folder
--account <ID> list-records --folder-id <folder_id>
```

### Step 6: Report Back

Tell the user what was created:

> "Done! I created the folder **Active Residential Jobs** under Jobs.
> - Shows jobs where Department is Residential and milestone is Won
> - The Sales and Admin roles can view it
>
> Want me to check which records show up?"

## CLI Commands Used

| Command | Purpose |
|---------|---------|
| `list-business-objects` | Find BO by name |
| `get-business-object --bo-name <n>` | The BO and its context (load before building the filter) |
| `list-fields --bo-name <n>` | Fields with their types, options and context, for filter building (also for each related BO the filter reaches) |
| `list-workflow-milestones --bo-name <n>` | Get milestones for filter building |
| `list-workflow-steps --bo-name <n>` | Get steps for filter building |
| `list-relationships --bo-name <n>` | Get relationships for filter building |
| `parse-filter --bo-name <n> --text '<formula>'` | Validate the formula before creating (see the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md)) |
| `list-roles` | Find role names (for "all users" role lookup) |
| `create-folder` | Create folder with filter and permissions (`--dry-run` to preview) |
| `update-folder --folder-id <id> --context <text>` | Change the folder's context (or `--data` settings) later |
| `list-folders` | Verify folder was created |
| `list-records --folder-id <id>` | Test which records match |

If no PATH CLI command covers what the user asks for, tell them it can't be done here ([`path-cli`](../../../pathwd-guide/references/commands.md) lists every command).

## Key facts and confirmations

- Every command here runs through the PATHwd tool for its area (each command's page in [the command index](../../../pathwd-guide/references/commands.md) names it).
- Users name things (business objects, fields, roles, people); the commands resolve names to ids, so ids come from the account, never from memory.
- A new folder shows the business object's default columns; other columns are chosen in the web app's folder form or with `update-folder --data '{"columns":[…]}'`.
- Creating a folder is a change the user sees in their menu: they confirm the name, the rule in plain words and who gets access first.
- Users know "the rules for which records show up" and "who has access", not stored filters or field ids. The formula syntax is in the [`path-logic-formula-syntax` guide](../logic-formula-syntax.md).
- Access given without edit or delete rights is view-only.
- Folder names are unique per business object; a duplicate name fails and the user picks another.
- A rule on a Selection field uses one of the field's options (the `options` column of `list-fields`; `get-field --field-id <id>` lists every one); when the user's word doesn't match an option exactly, they pick the option.
- A request that could mean two folders (which business object, which people) is settled with the user before anything is created.
