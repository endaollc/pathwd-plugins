---
name: path-concepts
description: "What PATH and PATH WD are and how they work, in the words of the web app. Use it to understand what a user means (Jobs, Quotes, Services, Purchase Orders, milestones, workflow steps, tasks, folders, tags, linked records, receiving, bins, scan sessions), to explain why something happened, and above all before answering pathway screens: what each screen is asking and what answering it changes. E.g. \"what does Full Quote Out mean?\", \"why did my Job move?\"."
---

PATH is a business system that runs a company's day-to-day work: sales, quoting, ordering, receiving, scheduling, delivery and installation, service, invoicing and payments. **PATH WD** is PATH set up for window and door dealers, and it is what every user of this plugin works in. These users know the PATH web app; this skill gives you the same understanding of it, so you can follow what they ask, explain what you see, and answer pathway screens the way they would.

Nothing here is about how PATH is built. Speak to users in the web app's words, the ones used here.

## The core ideas in one page

- **Business objects and records.** A business object is a kind of thing the dealer tracks: Jobs, Quotes, Services, Customers, Purchase Orders, Appointments, Invoices and so on. A record is one of them: "Job 1234", "the Smith quote". Users say "this Job", never "record", and so should you.
- **Fields.** The pieces of information on a record. Some are typed in; others, such as totals and identifiers, are calculated by PATH and can't be typed over. Fields are grouped into sections on the **Record Viewer**.
- **Linked records.** Records connect to each other: a Quote belongs to a Job, a Job to a Customer. A record can show its linked record's information, and totals can roll up from linked records (a Job's quote total).
- **Milestones.** The stages a record is in, such as Lead, Full Quote Out, Ordering or Paid In Full. PATH works them out from the record's workflow steps and field values; nobody sets them by hand. A record is often in several at once.
- **Workflow steps.** Events recorded on a record ("Job Won", "Delivery Scheduled"), by a person at a time. Recording one re-checks the milestones, which can move the record on.
- **Tasks.** To-dos on a record, assigned to a person with a due date. Most are created and completed automatically as milestones change, and many carry a pathway that does the work.
- **Pathways.** Guided step-by-step processes started from the Pathways tab of a record's Record Viewer, such as "Select Winning Quote" or "Record PO Receiving". Most steps are automatic. Some stop at a screen that asks the person something, and every answer changes real data.
- **Actions.** Automations such as notifications, emails, texts, new tasks and scheduled pathways, which fire when a record enters a milestone, from a pathway step, or on a schedule.
- **Folders and tags.** Folders are saved, filtered lists of one kind of record ("Deposit Needed", "My Jobs In Full Quote Out"). Tags, such as a location, limit which records each user sees anywhere in PATH, so two users can see different records.
- **Saving.** Saving a record recalculates its calculated fields, re-checks its milestones (tasks and actions may follow), and updates totals on linked records, sometimes a moment later.
- **Info.** Designers can write *info* on almost anything they set up: the account, business objects, fields, relationships, milestones, workflow steps, tasks, stickers, pathways and pathway steps. It is their own description of what the thing is for and how it's meant to be used, and the account's info describes the business itself. Whenever you look at a record, a folder, a report or a record's related lists, the info of its business object and of each field or column comes with it; a table column shows the info of the field it is based on. Each piece of info is shown once per session and marked as shown earlier after that (ask for it again if you no longer have it), except the columns of a folder, report or related list, which show it every time. Read it before you act on the thing it describes; where it differs from these files, the account's info wins.

## Running pathways for a user

This is where understanding matters most. Before answering a screen:

1. **Know where you are.** Read the screen's title and question and the record it is about. If the screen mentions a PATH WD term you don't recognise, look it up in `references/path-wd.md`.
2. **Know what the screen does.** `references/pathways.md` explains each kind of screen, what the person is deciding, and what answering changes.
3. **Read the designer's info.** A run shows the pathway's info when it starts, the step's info under the screen heading (`waiting_for.step_info`), and each field's info under the field (`info` on each field and table column in `detail`). That is what the designer meant the step and its fields for. Use it to decide what to ask the user and what a sensible answer looks like, for example "the amount actually paid, not the quote total". It never answers the screen on its own.
4. **Answer only from what the user told you.** If they haven't said, ask them. Field values, the record picked in a lookup, a Yes or No, email recipients, scans and signatures all change real data or send real messages. Never sign or acknowledge for someone without their clear say-so.
5. **Explain the outcome in business terms.** For example: "Job 1234 now has its winning quote and has left Full Quote Out."

## Reference files

Read only the file you need.

| File | Read it when |
|---|---|
| `references/path-wd.md` | You need what a PATH WD business object, term or abbreviation means. Also covers how the business objects connect, the Job, Quote, Purchase Order and Service lifecycles with their standard milestones, appointments, receiving, bins, scan sessions, OM and accounting sync. |
| `references/pathways.md` | You are running a pathway, or explaining one. Covers why a pathway is or isn't offered, how a run flows, every kind of screen, how to answer screens faithfully, run states, and the standard PATH WD pathways. |
| `references/workflow.md` | You need milestones, workflow steps or tasks: why a record is in a stage, what recording a step does, where tasks come from. |
| `references/actions.md` | Something was sent, created or run automatically and the user wants to know why. |
| `references/records-and-business-objects.md` | You need what a record holds, how users refer to records, or archiving versus deleting. |
| `references/fields.md` | You need the kinds of fields, why a field can't be edited, formats, or field groups. |
| `references/relationships.md` | You need linked records, information shown from a linked record, or totals and counts rolled up from linked records. |
| `references/folders-and-tags.md` | You need why a record is or isn't in a folder, or why two users see different records. |
| `references/saving-records.md` | A save changed more than the user typed, and you need to explain why. |

## The account is the final word

These files describe the standard PATH WD setup. An account may have renamed or added business objects, fields, milestones and pathways. When it matters, check what the account actually has (the `path-cli` skill lists the commands) and go by that.

Designers set all of this up in PATH Designer. This plugin uses PATH as the signed-in user does in the web app, and it doesn't change how the account is set up.
