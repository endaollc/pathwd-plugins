---
name: pathwd-history
description: "PATHwd record history and milestone explanations: what changed on a record, who changed it, when and through which pathway (history sessions and events, read-only), and why a record is or isn't in a milestone, with the milestone's criteria checked one condition at a time against the record. Covers questions like \"what changed on Job 12345 this week?\", \"who moved this record to Won?\" or \"why is this job still in Lead?\"."
---

# Record history and milestones in PATHwd

PATH records every change to a record as history: each event (saved changes, a milestone reached, a file uploaded, a link) belongs to an activity session, such as a visit to the record's card, a pathway run or an inbound integration request. On the PATHwd connector, a record's history is read with `get-history-by-record`, one session with `get-history-session` and one event's details with `get-event-metadata`. History is read-only.

A record's milestones follow from its milestone criteria: Logic Builder formulas over its field values, workflow steps, other milestones and linked records. Why a record is or isn't in a milestone is answered by reading the milestone's formula and checking each condition against the record.

The account, session and the connector's tools are described in the **pathwd-guide** skill; the Logic Builder syntax in the **pathwd-find-records** skill.

## Guides

- [path-milestone-explain](references/milestone-explain.md): Use when the user asks why a specific record is, or is not, in a specific milestone.
- [path-record-history](references/record-history.md): Use when the user wants to know what happened to a record or who changed what, when, and through which pathway, plus the sessions and events behind it.

## Commands

| Command | Tool | What it does |
|---------|------|--------------|
| [`get-event-metadata`](references/commands/get-event-metadata.md) | `path_record_activity` | Get the metadata (and context) of a single history event (read-only) |
| [`get-history-by-record`](references/commands/get-history-by-record.md) | `path_record_activity` | Change history for a record as a flat event table (read-only) |
| [`get-history-session`](references/commands/get-history-session.md) | `path_record_activity` | Every event one activity session recorded — e.g. everything an inbound integration request did: the record it created, fields set, pathway steps… |
