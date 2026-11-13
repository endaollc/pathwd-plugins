# Records and Business Objects

Everything in PATH is stored as a record, and every record belongs to a type called a business object. This page explains what those words mean and how to talk about them the way PATH WD users do.

## Business objects: the types of thing

A business object is a kind of thing the account tracks. In PATH WD the usual ones are Jobs, Quotes, Services, Customers, Invoices, Purchase Orders and Appointments. A business object is only the definition: it says which fields exist, how records of that type link to other types, which milestones and workflow steps they move through, which folders and reports exist, and which actions (emails, texts, notifications, pathways) are available.

Users see business objects as the main areas of the app. Each has a singular and plural name ("Job" and "Jobs"), an icon, and often an entry in the navigation menu. Some show a workflow status in the Record Viewer, and some have messaging turned on.

Each business object also has a few built-in fields that every record gets automatically:

- **Identifier**: the record's label, such as the job number or name shown in lists and links.
- **Created Date**: when the record was created.
- **Modified Date**: when it was last changed.
- **Last Accessed**: when someone last viewed it.

The Identifier, like other calculated fields, is worked out by PATH. You can't type over it (see the fields page).

Business objects are set up by the account's designers. End users work with the business objects that already exist; they don't add or remove them.

## Records: the individual things

A record is one specific item of a business object: "Job 1234", "Quote 5678", "Invoice 9001", "Customer: Acme Windows". If the business object is "Jobs", a record is one job.

## How users talk about records

Users almost never say "record". They say "this Job", "Job 1234", "the Smith quote" or "that invoice". When you talk to the user, use the business object's name. Say "I found Job 1234" rather than "I found a record". Use "record" only when you mean something generic across types, such as "a record in this folder" or "open the Record Viewer".

The screen where a single record is shown and edited is the **Record Viewer**.

## What a record holds

A record holds several kinds of information.

**Field values.** The actual data: customer name, job type, quote total, install date. Fields are covered on their own page.

**Milestones.** Milestones say where the record is in its life, for example Lead, Full Quote Out, Ordering, All Items Received, Paid In Full. A record enters a milestone when its criteria are met. Those criteria can depend on field values, workflow steps that have been completed, and linked records. A record can be in several milestones at once, one for each set of criteria it meets, and it leaves a milestone when it no longer meets that milestone's criteria. Which milestones a record is in is worked out by PATH, so you can't set them by hand. Milestones are also what folders and reports often filter on.

**Workflow steps.** Workflow steps are specific events done by a specific user at a specific date and time during the record's life (for example "Deposit Received" or "Installation Scheduled" on a Job). The record keeps the history of which steps it has reached and when. Pathways are the usual way steps get completed.

**Linked records.** A record can be linked to records of other types: a Quote to its Job, a Job to its Customer. See the relationships page.

**Stickers.** Small visual flags that show on a record or in a folder when certain conditions are true, such as an overdue marker. Designers define them.

**Activity.** Things that happen around the record:

- messages (updates and comments posted on it)
- emails sent or received
- files attached to it
- tasks assigned for it

Tasks are to-dos that belong to a record. They show up in the record's own activity and in each assigned person's task queue.

**Basic details.** Who created it, when it was created, when it was last changed, and whether it is archived.

## Creating and changing records

Records are created and edited in the Record Viewer, by a pathway, or by an automated process. When you change a field, PATH may update other things as a result: calculated fields, milestones, tasks and totals on linked records. The saving page covers this.

## Archiving versus deleting

Archiving is how records are normally taken out of play.

- An archived record still exists. It is kept, and it can be unarchived later to bring it back.
- While archived, it is hidden by default: it doesn't appear in search, folders or reports unless that folder or report is specifically set to include archived records.
- It stops counting in totals and counts on records it is linked to. If an archived Quote used to add to a Job's quote total, the total drops when the Quote is archived and rises again if the Quote is unarchived.

If the user says "delete" a Job, Quote or Invoice, they most likely want it out of their lists and totals, which is what archiving does. Confirm what they mean before doing anything that can't be undone, and prefer archiving because it can be reversed.

## Quick vocabulary

- **Business object**: a type, such as Jobs.
- **Record**: one item of that type, such as Job 1234.
- **Record Viewer**: the page where one record is shown.
- **Milestone**: a stage a record is in, decided by criteria.
- **Workflow step**: a recorded event in the record's life.
- **Task**: a to-do tied to a record.
- **Archive**: hide a record and take it out of totals, reversibly.
