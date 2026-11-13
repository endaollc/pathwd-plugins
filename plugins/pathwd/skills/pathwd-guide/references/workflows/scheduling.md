# Workflow: scheduling

> **Commands:** every command in this command is a PATH CLI command line: the command name and its flags. On PATH WD Remote MCP each tool runs the commands of one area: a command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`, and each command's page in [the command index](../commands.md) gives any command's syntax and the tool that runs it.

**Covers:** calendars and appointments: listing events, scheduling a record's appointment field, standalone events, and (with calendar permission) managing calendars and calendar groups. Commands: `list-calendars`, `list-calendar-events`, `set-appointment`, `clear-appointment`, `create-calendar-event`, `update-calendar-event`, `delete-calendar-event`, the calendar and calendar group commands. Each command's syntax and output: its command note.

The account and session: the [`path-session` guide](../session.md). Every command's syntax: the [`path-cli` guide](../commands.md).

## Appointments, Events and Appointment Fields

Every calendar item is one **calendar event**: a standalone event, a record's
**appointment** (an event that owns one of the record's appointment fields), or
a Google / Office 365 event. An appointment field's value on a record is only a
copy of its event — `update-record` rejects it ("can only be changed through
calendar events"). Schedule, move or clear a record's appointment with the
appointment commands below (they create, update or delete the event; the field
follows).

Dates on these commands are typed the way the web form reads them:
`"2026-04-13 09:00"`, `2026-04-13`, an ISO timestamp, `today`, `tomorrow`, `+3d`.
Times are in the user's time zone. **All-day** dates are whole days on the
account's calendar. A new event lasts an hour unless `--end` is given; a moved
`--start` keeps the event's length; multi day follows the dates.

## What to ask the user

**For listing appointments:**
- **Date range** — which dates to show (e.g. "today", "this week", "next Monday"). If not specified, default to today.

**For scheduling a record's appointment (all of these, before anything is saved):**
1. **The record** and which **appointment field** (if its business object has more than one)
2. **Date and time** (or "all day")
3. **Calendar**

**For a standalone appointment (all of these, before anything is saved):**
1. **Title** — what the appointment is for (e.g. "Delivery", "Site Visit")
2. **Date and time** — when (e.g. "next Monday at 9am", "April 15 at 2pm")

**Optional (use defaults if not mentioned — do NOT ask):**
- **Calendar** for a standalone event — default to the first available
- **Duration** — default 1 hour
- **Title** of a record appointment — defaults to the field's Event Description template, like the web
- **Location / description**

If the user hasn't specified an account ID, ask which account to use or run the [`path-session` guide](../session.md).

## Steps

### Listing Calendars and Appointment Fields

```
--account <ID> list-calendars

# The appointment fields events can be scheduled on (id, name, business object)
--account <ID> list-appointment-fields [--bo-name Job]
```

### Listing Events / Appointments

```
# Today's events (every origin: standalone, record appointments, Google/O365)
--account <ID> list-calendar-events \
  --start-date "2026-04-10" \
  --end-date "2026-04-10"

# This week, only some calendars
--account <ID> list-calendar-events \
  --start-date "2026-04-06" \
  --end-date "2026-04-12" \
  --calendar-ids 1,4

# Only one kind: standalone, record_appointment, provider (Google/O365)
--account <ID> list-calendar-events \
  --start-date "2026-04-06" --end-date "2026-04-12" --sources record_appointment

# Events scheduled on one record
--account <ID> list-calendar-events --record-id <recordId>

# …plus the appointments of records it links to and records linking to it
# (what the record's Appointments tab shows)
--account <ID> list-calendar-events --record-id <recordId> --include-related

# Search by text (title, location, organiser, linked record)
--account <ID> list-calendar-events --search "Delivery"
```

Use today's date as the reference point and calculate relative dates from there.

### Scheduling a Record's Appointment Field

```
# Schedule (creates the event; title from the field's Event Description template)
--account <ID> set-appointment <recordId> \
  --field "Install Date" --start "2026-04-13 09:00" --calendar "Install Crew"

# Move it (keeps the length), make it all day, span days, add details
--account <ID> set-appointment <recordId> --field "Install Date" --start "2026-04-14 13:00"
--account <ID> set-appointment <recordId> --field "Install Date" --all-day --start 2026-04-14 --end 2026-04-16
--account <ID> set-appointment <recordId> --field "Install Date" --location "12 Main St" --description "Gate code 4411"

# Clear it ("Not Set": deletes the event). Asks for confirmation: the first run deletes nothing and prints what it would delete and a code;
# only after the user explicitly says yes, run the same command again with --confirm <code>
--account <ID> clear-appointment <recordId> --field "Install Date"
```

`--field` takes the field id or name. Only the flags given change. `--calendar`
takes an id or name. `--no-all-day` turns all day off (8–9am on the same days);
`--no-multi-day` brings the end back to the start's day.

### Creating a Standalone Appointment

#### Step 1: Look Up Calendars

```
--account <ID> list-calendars
```

Use the first calendar if the user didn't specify one.

#### Step 2: Confirm With the User

> "I'm going to create an appointment:
>
> **Title:** Delivery
> **Date:** Monday, April 13, 2026 at 9:00 AM
> **Duration:** 1 hour
> **Calendar:** Main Calendar
>
> Does that look right?"

#### Step 3: Create the Appointment

```
--account <ID> create-calendar-event \
  --title "Delivery" --start "2026-04-13 09:00" --calendar "Main Calendar"
```

Add `--record <recordId> [--field <id|name>]` to create it as that record's
appointment instead (same as `set-appointment`). The raw form still works:
`--data '{"title": …, "start_date": "<ISO>", "calendar_id": 1, …}'` (flags
override its keys).

#### Step 4: Report Back

> "Done! I created the **Delivery** appointment for Monday, April 13 at 9:00 AM."

### Updating, Linking or Deleting Events

```
# Update an event (any origin the user may edit) — same flags as create
--account <ID> update-calendar-event <eventId> \
  --title "Updated Title" --start "2026-04-14 09:00"

# Attach an event to a record's empty appointment field (a field holds one event:
# a scheduled field refuses it), or detach it (the event stays, standalone; the field clears)
--account <ID> update-calendar-event <eventId> --record <recordId> --field "Install Date"
--account <ID> update-calendar-event <eventId> --unlink

# Delete an event (a record appointment's field is cleared too). Asks for confirmation: the first run deletes nothing and prints what it would delete and a code;
# only after the user explicitly says yes, run the same command again with --confirm <code>
--account <ID> delete-calendar-event <eventId>

# Inspect one event
--account <ID> get-calendar-event <eventId>
```

Google / Office 365 events marked read-only (`read-only` in the `from` column of `list-calendar-events`, `read-only: yes` in `get-calendar-event`) can't be changed from PATH.

### Managing Calendars and Calendar Groups

The web's Calendar → Manage Calendars tab. Who may do what (the CLI refuses
up front, as the web hides the buttons):

- **Create** a calendar or calendar group: Admins and Calendar Admins only.
  The creator becomes its single **owner**; everyone can view a new calendar.
- **Rename, recolor, set what All Users get, share, delete** a calendar — and
  a group's All Users level, sharing and delete: its owner, or an Admin /
  Calendar Admin. An Edit grant is not enough.
- A group's **name and calendars**: Edit on the group.
- Who it is shared with shows (in `get-calendar` / `get-calendar-group`) only
  to those who can manage it.

```
--account <ID> get-calendar "Install Crew"
--account <ID> create-calendar --name "Install Crew 2" --color "#1565C0"
--account <ID> update-calendar "Install Crew 2" --name "Install Crew B" --color "#2E7D32"

# What every member gets without another grant: view (default), edit, or none
--account <ID> update-calendar "Install Crew B" --all-users none

# Share: users (ids, emails or full names) and/or roles (ids or names)
--account <ID> share-calendar "Install Crew B" --roles "Installers" --access edit
--account <ID> share-calendar "Install Crew B" --users "jane@example.com,Bob Smith" --access view
--account <ID> share-calendar "Install Crew B" --users "Bob Smith" --access remove
# Hand ownership to one user (the previous owner keeps view/edit)
--account <ID> share-calendar "Install Crew B" --users "jane@example.com" --access owner

# Groups (a group's grants never change access to the calendars inside it)
--account <ID> list-calendar-groups
--account <ID> create-calendar-group --name "Installs" --calendars "Install Crew,Install Crew B"
--account <ID> update-calendar-group "Installs" --add-calendars "Service Lead 1" --remove-calendars "Install Crew"
--account <ID> share-calendar-group "Installs" --roles "Schedulers" --access edit
```

**Deleting** asks for confirmation every time. The first run deletes nothing:
it prints what would be deleted (the calendar and how many events; record
appointments on them are cleared) and a code. Show that to the user and wait
for an explicit yes, then re-run the same command with `--confirm <code>`.

```
--account <ID> delete-calendar "Install Crew B"
# keep the events: move them to another calendar the user can edit
--account <ID> delete-calendar "Install Crew B" --move-events-to "Install Crew"
# a calendar merged with Google / Office 365: also delete the events PATH created there (default: leave them)
--account <ID> delete-calendar "Install Crew B" --external-events delete
--account <ID> delete-calendar-group "Installs"
```

## CLI Commands Used

| Command | Purpose |
|---------|---------|
| `list-calendars` | List available calendars |
| `list-appointment-fields` | Appointment fields events can be scheduled on |
| `list-calendar-events` (alias `list-appointments`) | Events in a window / on a record (`--include-related`), by `--sources`, or `--search` |
| `set-appointment <recordId> --field …` | Schedule or change a record's appointment field |
| `clear-appointment <recordId> --field … [--confirm <code>]` | Clear a record's appointment field (asks for confirmation) |
| `create-calendar-event` (alias `create-appointment`) | Create a standalone event or (with `--record`) a record appointment |
| `update-calendar-event <id>` | Change an event; `--record`/`--field` link, `--unlink` |
| `delete-calendar-event <id> [--confirm <code>]` | Delete an event (asks for confirmation) |
| `get-calendar-event <id>` | Show one event |
| `get-calendar <id\|name>` | A calendar's color, owner, All Users level, and who it is shared with |
| `create-calendar --name [--color]` | Create a calendar (Admin / Calendar Admin) |
| `update-calendar <id\|name> [--name --color --all-users view\|edit\|none]` | Rename / recolor / set what All Users get (owner or Admin / Calendar Admin) |
| `share-calendar <id\|name> [--users] [--roles] --access view\|edit\|owner\|remove` | Share a calendar or hand over ownership |
| `delete-calendar <id\|name> [--move-events-to] [--external-events] [--confirm <code>]` | Delete a calendar (asks for confirmation) |
| `list-calendar-groups` / `get-calendar-group <id\|name>` | Calendar groups |
| `create-calendar-group --name --calendars` | Create a group (Admin / Calendar Admin) |
| `update-calendar-group <id\|name> [--name --calendars --add-calendars --remove-calendars --all-users]` | Rename / change a group's calendars |
| `share-calendar-group <id\|name> … --access …` | Share a group |
| `delete-calendar-group <id\|name> [--confirm <code>]` | Delete a group (asks for confirmation) |

## Key facts and confirmations

- Every command here runs through the PATH WD Remote MCP tool for its area (each command's page in [the command index](../commands.md) names it).
- An appointment field is never written with `update-record`: `set-appointment` / `clear-appointment` (or the event commands) schedule and clear it.
- Creating, moving and deleting events change real calendars: the user sees what will be created or changed and agrees first.
- Without other instructions: an hour long, the user's usual calendar (they confirm it), today for listings.
- Times read best as people say them ("Monday, April 13 at 9:00 AM"), on the user's clock, not as UTC timestamps.
- Relative dates ("next Monday", "this Friday") become actual dates from today's date, typed on the user's clock (the flags read them there).
- `delete-calendar`, `delete-calendar-group`, `delete-calendar-event` and `clear-appointment` print what they would delete and a code; `--confirm <code>` follows only the user's explicit yes to what was printed.
