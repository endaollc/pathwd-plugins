# get-calendar-event

Runs with: path_calendars(command: "get-calendar-event", args: "<flags>") (read-only).

```
Usage: get-calendar-event [options] <id>

Show one calendar event: title, when (your clock), calendar, the record and
appointment field it is on, source (standalone, or the synced provider and
organizer), read-only, location, participants, description, created

Arguments:
  id          Calendar event id

Options:
```

## Examples

- args: `6aa08c047f73f73dd53720c7`

## Notes

Shows one calendar event by its id: a standalone event, a record's appointment, or an event from a merged Google / Office 365 calendar.

**Examples**

```
get-calendar-event 6a986d9a67f01e74e7ea15f4
```

- The id is the event's 24-character id from `list-calendar-events`, not a number and not the record's id. A record's appointment: `list-calendar-events --record-id <record id>` finds its event.

**Output**

```
id: 6ae3873c1a07b3f61f062196
title: Retail Showroom Visit : 26-1857 - 6202 Woodview Ave
when: 2026-11-03 09:30–10:30 CST
calendar: Outside Sales (1626)
appointment: Scheduled Date and Time (field 191972) on Appointments record 6ae386fd7f7438f8494c04fb
location: 6202 Woodview Ave
description: Bring the casement samples.
created: 2026-10-29 12:49 by Todd Demo
```

- `when`: a timed event on the user's clock with the zone; an all-day event as days on the account's calendar (`2026-09-04 all day`).
- `appointment`: the record appointment field the event fills (field and record ids); changing the event changes that field (`set-appointment`, `update-calendar-event`). Absent on a standalone event.
- An event from a merged Google / Office 365 calendar: `source: outlook, organizer …` (and its status when not confirmed), `repeats: <rule>` for a recurring one, `read-only: yes` when it can't be changed or deleted from PATH. A synced HTML description shows as its text.
- Blank values are left out.

**Common errors**

- `ERROR [404]: Calendar event not found`: a wrong or deleted id (a numeric id or a record id isn't an event id).
- `ERROR [403]: You do not have view permission on this calendar`: the event's calendar isn't shared with the user.

**Related:** `list-calendar-events`, `update-calendar-event`, `delete-calendar-event`, `set-appointment`, `get-calendar`.

Related commands: [`list-calendars`](list-calendars.md), [`get-calendar`](get-calendar.md), [`list-calendar-groups`](list-calendar-groups.md), [`get-calendar-group`](get-calendar-group.md), [`list-calendar-events`](list-calendar-events.md).
