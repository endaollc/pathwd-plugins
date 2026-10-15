# Pathways: What Each Screen Asks and What Answering It Does

A pathway is a guided, step-by-step process, such as "Select Winning Quote" or "New Full Quote", that walks a user through a piece of business in a fixed order. Some steps stop and show the user a screen; others do their work on their own. When Claude runs a pathway for a user, it meets the same screens the user would meet in the web app, and every answer changes real data the same way, so this page explains each screen in business terms.

## What a pathway is

A pathway is a series of steps that run in order. Designers build pathways in PATH Designer; end users run them. A typical pathway can:

- ask the user to fill in or change fields on a Job, Quote or Service;
- create new records and link them to existing ones;
- run workflow steps that move the record into a new milestone;
- create tasks, send emails, generate documents and run actions.

Pathways keep a process consistent. Each step captures information or sets off something the business relies on.

## Where users start a pathway

- **On a record (its Pathways tab).** The Record Viewer for a Job, Quote, Service or other record has a **Pathways** tab listing the pathways that can start on that record. They appear as buttons grouped under their pathway groups, with any pathway in no group under "Others". The tab appears only when at least one pathway is available. Clicking a pathway starts it on that record. This is the most common way to start one, for example "Select Winning Quote" on Job 1234.
- **From a related-records table.** A table of linked records on the Record Viewer can have a pathway column. Its button on each row starts that pathway on the row's record.
- **From the dashboard.** Many pathways start from the dashboard, without a record. On a computer they are listed across the top of the dashboard; on a phone they are under the dashboard's Pathways tab. They usually create or look up the record as their first steps. In PATH WD these include Create Job, Create New Retail Job, Create New Trade Job, Create New Service, Create Commercial Project, Create New Vendor, Start Receiving Scanning, Bin Check In, Bin Check Out, Bin Lookup, Start Loading Truck, Start Delivering Products and Create Tracking Labels.
- **From a folder.** Some pathways are offered in the header of a folder, for the kind of records that folder lists. These also start without a particular record.
- **From a task.** A task can carry a pathway. Completing that task runs the pathway, and the pathway often finishes the task as part of its work. See [workflow.md](workflow.md).

**Pathway groups** such as "Sales", "Operations" or "Admin" organize the lists. A pathway can be in several groups. Groups don't change what a pathway does.

## Why a pathway is or isn't offered on a record

For a pathway to appear on a record's Pathways tab, all of these must hold:

1. **Role permission.** One of the user's roles has been given the pathway. Admins see all pathways.
2. **It starts from this kind of record.** Every record pathway starts from one business object. A pathway that starts on Jobs never appears on a Quote, Service or Purchase Order.
3. **It's set to show on records.** Some pathways appear only on the dashboard or in folders.
4. **The record meets the pathway's visibility criteria.** These usually check the record's milestones or field values. For example, in PATH WD "Select Winning Quote" is offered on a Job only while it is in the Full Quote Out milestone, and "Deposit Bypass Approved" only while it is in Deposit Bypass Requested. Once the Job moves on, the pathway disappears because it no longer applies.
5. **The user can open the record.** Records hidden from the user (for example by tags) or archived can't be used to start a pathway.

When a user asks "why can't I run X on this Job?", the usual reason is that the Job isn't in the milestone the pathway expects, or that the user's role doesn't have it. Claude can only start pathways the user could start in the web app, and each screen offers only what the user would see there.

A pathway started from a task follows the task's rules instead. The task must still be open, and if it is marked "only the assignee can finish", only the assignee, the task's creator or an admin can run it.

## How a run flows

- **Steps run in order.** Each step finishes before the next one starts. Nothing runs in parallel.
- **Automatic vs interactive steps.** Automatic steps, such as saving, linking, running a workflow step or sending an automatic email, do their work without asking anything. Interactive steps pause the run and show a screen. The run continues only after the screen is answered.
- **The run remembers what earlier steps produced.** If step 2 created a Quote or step 3 looked up a Purchase Order, later steps act on that record.
- **Forks and Yes/No questions branch.** A **Fork** checks a condition on the record (for example "is a deposit recorded?") and sends the run down one of two branches with no screen. A **Yes/No question** asks the user and branches on the answer. Either way, the steps on the branch not taken are skipped.
- **List Loops repeat.** A List Loop takes a list of records produced earlier, such as the line items picked in a table or the records a lookup found, and runs a block of steps once for each. If the block contains screens, the user meets them once per record. A progress counter ("3 of 7") shows how far along the loop is.
- **A pathway can launch another pathway.** A Launch Pathway step starts a second pathway on a record. Its screens appear in the middle of the first run, and the first pathway continues once it ends.
- **Pathways that run on their own.** Some pathways run with no user: on a schedule, from an action, or during an inbound integration or data import. With nobody to answer, Yes/No questions count as "No" and other screens are skipped. A Job that changed overnight with nobody touching it was often changed by a scheduled pathway.

## The screens a person meets

For each screen below: what it looks like, what the user is deciding, and what happens to the data. Many of these steps can also be set up to run with no screen, so a step type may ask in one pathway and not in another. Designers title the screens, so a screen may say "Enter Contract Details" rather than its type.

### Edit Record

- **Looks like:** a form for one record (for example Job 1234), showing the record's current values. It may show every editable field, or only the fields the designer picked, in the designer's order. Some fields are marked required. Selection fields may offer only some of their options.
- **Deciding:** which values to change. Leaving a field alone keeps its current value, so continuing without changing anything is valid.
- **Effect:** on Continue, the changes are saved to the record. Saving then re-checks calculated fields and milestones, so the Job may move into a new milestone and tasks or actions may follow. Calculated fields can't be typed into.

### Edit Record Field (including "choose from options" and appointments)

- **Looks like:** a single field on the record with its current value, for example a Status, a date or a yes/no flag. For a selection field the options may be shown as buttons, and picking one is the whole answer. The field may be required.
- **Appointment fields** open an appointment editor instead: start and end, calendar, title, all day or multi-day, location and description. If the field isn't required, there is a "Continue without Scheduling" option.
- **Deciding:** the value for this one field, or, for an appointment, when and on which calendar it happens.
- **Effect:** the field is saved immediately, with the same milestone, task and action effects as any save. An appointment is placed on the chosen calendar. Some of these steps fill in a value automatically, such as today's date, and show no screen.

### Create Record

- **Looks like:** a blank form for a new record of one type, such as a new Quote, Service or Purchase Order, with default values already filled in and required fields marked. If the user holds more than one tag in a tag group that the pathway hasn't already decided, the form also asks which tag the new record should carry.
- **Deciding:** the details of the new record.
- **Effect:** on Continue, the record is created right away. It may start in its default milestone and run its initial workflow step, which can create tasks. Later steps usually link it to the starting record, for example a new Quote linked to the Job.

### Lookup (select a record)

- **Looks like:** a searchable list of records of one type, for example Purchase Orders or Customers. The list may be limited to one folder, may start with a search already filled in, may stay empty until the user searches, and may offer a barcode scanner.
- **Deciding:** which existing record the rest of the pathway should work on.
- **Effect:** picking a record changes nothing by itself, but every later step that uses "the record from the lookup" acts on it: links, edits, emails and documents. Picking the wrong Purchase Order means the wrong Purchase Order gets changed. When the search finds exactly one match, some lookups choose it automatically. Automatic lookups that find nothing may send the run down a "not found" branch.

### Create or Lookup

- **Looks like:** two tabs, Lookup and Create. The Lookup tab works like Lookup above, and the Create tab is a Create Record form.
- **Deciding:** use an existing record, or make a new one.
- **Effect:** choosing an existing record changes nothing. Creating makes a new record immediately. Check carefully for an existing match before creating, to avoid duplicates (for example a second Customer with the same name).

### Yes/No question

- **Looks like:** a question, often with the record's own values inside it, and two buttons. The buttons may have custom labels instead of "Yes" and "No", for example "Send Now" and "Later".
- **Deciding:** which branch the pathway takes.
- **Effect:** the answer isn't saved to a field, but it decides which steps run next. "No" usually skips a whole branch: the email isn't sent, the task isn't created, the Purchase Order isn't linked. Answer only from what the user said.

### Message

Information, which may include the record's current values, and one button (often "Okay"). Nothing is decided and nothing changes, but pass the content on to the user: designers use it for instructions and warnings.

### Signature

- **Looks like:** a signature pad with a required checkbox, "I agree to use an electronic signature", and sometimes a second acknowledgement written by the designer.
- **Deciding:** whether to sign and agree.
- **Effect:** the signature image is saved to the record's files and the signing is recorded on the record. A signature is a record of consent. Never sign on anyone's behalf unless the user has clearly said to, and never tick an acknowledgement the signer hasn't agreed to.

### Upload File

- **Looks like:** a file picker, either for one file or several, which may be required or optional. It also asks for a file type (for example a photo, acknowledgment or contract type), from a list the designer may have narrowed, and an optional comment.
- **Effect:** the files are attached to the record immediately, and later steps may email, merge or import them.

### Select File

- **Looks like:** a list of files already on the record, sometimes only files of certain types. The user picks one or several, and the choice may be required or optional.
- **Effect:** nothing changes. Later steps use the chosen files, for example as email attachments or as pages to merge.

### Create Document from Template

- **Looks like:** a short form for generating a document, such as a quote or contract, from a template filled with the record's values. It asks for a file name, a file type and an optional comment.
- **Effect:** the document is generated and, depending on the pathway, saved to the record's files and/or passed on to later steps (for example to be emailed). Some versions generate the document automatically with no screen.

### Merge Files

- **Looks like:** the record's files, from which the user picks the ones to combine and puts them in order. Depending on the pathway, the screen can also name the merged file, save it to the record's files with a file type, download it, or upload extra files first.
- **Effect:** one combined PDF is made and, if chosen, saved to the record.

### Send Email

- **Looks like:** an email composer with To, CC, BCC, subject and body already filled in from a template, using the record's values, plus the user's email signature. Files from the record can be attached and new files uploaded.
- **Deciding:** whether the recipients and wording are right.
- **Effect:** pressing Send sends a real email, often to the customer, and logs it on the record. Check the recipients before sending. Many emails are set to send automatically with no screen.

### Post Note

A note box, which may be required, sometimes with earlier notes shown above it. The note is added to the record with the user's name and the time.

### Create Task

- **Looks like:** a choice of task. The user can pick one of the task templates available in the record's current milestones, or write a custom task with a title, assignee, due date, notes and options such as "notify me when done" and "can be skipped". Some steps fix the template in advance.
- **Effect:** a real task is created on the record and assigned. The assignee may be notified and will see it in their task list.

### Edit Task

Shows an existing task's assignee, due date, notes and options; saving updates the task. A user who didn't create the task and isn't an admin can only change the assignee.

### Complete Task

When the pathway was started from a task, this step marks that task completed, sometimes after a notes box. If the pathway didn't start from an open task, nothing happens.

### Edit Record Table

- **Looks like:** a table of records, often the records linked to this one. Depending on the pathway, the user can edit cells, search, add new rows, archive rows, reorder rows, select rows, and press Save.
- **Deciding:** the edits, and which rows to carry forward.
- **Effect:** edits are saved when the user presses Save, or automatically on Continue if the pathway is set up that way. Added rows create real records, and archiving removes a record from normal use. The selected rows (or all rows when selection is off) are what the following steps, often a List Loop, act on. Continue may require at least one selected row.

### Scan sessions: Delivery, Loading, PO Receiving, Stop Receiving, Bin Check In and Bin Check Out

- **Looks like:** a warehouse or delivery screen listing the items expected for the appointment, Purchase Order, stop or bin. The user scans barcodes or presses each row's button ("Received", "Loaded", "Delivered", "Check In", "Check Out", or the "… All" version for a whole line), sets quantities, uses Undo, and can retry or dismiss scans that failed.
- **Effect:** each scan or button press is recorded on the spot, not when the screen closes. Continue finishes the session and runs the account's end-of-session processing, which updates quantities, statuses and related records. Every scan or press must match something that physically happened.

### Scheduling

- **Looks like:** for an appointment, two lists, Available and Scheduled. The user moves items or quantities onto the appointment, or undoes them.
- **Effect:** each choice is recorded as it is made, and Continue finishes the session and updates the related items.

### Create Tracking Labels

- **Looks like:** a label builder. The user picks or creates a stop or shipment, then for each label finds the order and line, sets size, quantity and description, and adds it. In Import mode the user scans an existing barcode instead.
- **Effect:** labels and their tracking items are created as they are added. Removing a label archives its tracking item. "Download and Continue" (or Submit) completes the shipment.

### Import summary and export summary

Results screens after an import or export: how many records were created or exported, failed rows, and for an export a download option. The work has already happened.

### Display Record and Display Folder

At the end of some pathways, the web app opens a record (sometimes on a particular tab) or a folder for the user. Nothing needs answering.

## Steps that run on their own

These happen without a screen. When they run, the run usually shows a short status such as "Saving Job" or "Creating Quote".

- **Save Record:** saves the record the pathway has been working on.
- **Assign Fields:** sets field values automatically, for example stamping a date or copying a value from the Job.
- **Run Workflow Step:** records a workflow step (such as "Job Won" or "Delivery Scheduled") on the record. Milestones are re-checked, which can move the record, create or close tasks and fire actions. See [workflow.md](workflow.md).
- **Deactivate Workflow Step** (and reactivate): undoes, or restores, a workflow step on the record, with the same re-check.
- **Link Record / Unlink Record:** connects or disconnects two records, for example a Quote to its Job or a Job to a Purchase Order.
- **Relationship lookup:** finds linked records, such as this Job's Customer.
- **Archive:** archives a record.
- **Create Task (automatic):** creates a task from a template with no form.
- **Send Email (automatic):** sends a template email with no composer.
- **Execute Action:** fires an action, such as a notification, email, text, task, integration or scheduled pathway. Some actions finish in the background a moment later. See [actions.md](actions.md).
- **Export File / Import File:** builds an export file, or creates records from an uploaded file.
- **Delete File:** removes files an earlier step produced.
- **Get Current User:** notes who is running the pathway.
- **Account-specific processing:** work built for PATH WD itself, such as receiving, scanning and scheduling updates, sometimes with its own status message.
- **Fork, List Loop and Launch Pathway:** the flow-control steps described above.

## Answering screens faithfully

- **Answer with what the user told you.** Use the values, choices and wording the user gave. If a screen asks for something the user didn't cover, stop and ask them. Don't fill it in yourself.
- **Don't guess on anything that changes real data.** Field values, which record to pick, Yes/No branches, email recipients, task assignees, scans and signatures all have real effects. Guess on none of them.
- **Keep current values unless asked to change them.** On Edit Record, leaving fields as they are is a safe default.
- **Know what "No" does.** It skips a whole branch. "Don't email the customer yet" means "No" to "Send confirmation email?"; silence means ask.
- **Pick lookup results carefully.** When results look alike, confirm by Job number, address or customer, not just the name.
- **Respect required fields and validation.** If a screen refuses an answer, report the reason instead of working around it.
- **Never sign or acknowledge on someone's behalf** without explicit instruction.

## Run state: paused, finished, failed, cancelled

- **Paused:** the run is waiting on a screen. Nothing more happens until the screen is answered or the run is cancelled.
- **Finished:** every step on the path the run took has completed.
- **Failed:** a step couldn't complete, for example because a required value was missing or the record wasn't in the expected state. Steps that completed before the failure stay done.
- **Cancelled:** the user stopped the run. **Cancelling doesn't undo anything.** Records already created, fields already saved, emails already sent and workflow steps already run all remain. Only the steps that hadn't run yet are skipped. Before cancelling partway, tell the user what has already happened. Running the pathway again later may repeat earlier steps, for example creating a second Quote.

## Common PATH WD pathways

These are standard PATH WD pathways started from a record's Pathways tab, by business object. An account may have renamed or added some, so check the record's own pathway list before assuming one exists.

- **Jobs:**
  - Leads and sales: Mark Lead Qualified, Schedule Retail Sales Visit, Record Job Outreach.
  - Quoting: New Quick Quote, New Full Quote, New Quick Contract, New Final Contract, Convert to Final Contract, Select Winning Quote, Revert Won Job to Quoting.
  - Outcome: Job Lost, Cancel Job, Re-open Job.
  - Deposits: Request Deposit Bypass, Deposit Bypass Approved, Deposit Bypass Denied.
  - Ordering: Create Purchase Order, Create Installation PO, Create Change Order, Create Shop Work Order, Check In Job Items.
  - Scheduling: Schedule Appointment, Confirm Soft Schedule, Scan Customer Pickup.
  - Billing and accounting: Create Job Invoice, Record Job Payment, Apply Job Payment, Send Job to Accounting, Mark As Bad Debt.
  - Other: Create A Service, Add Job Notes.
- **Quotes:**
  - Generating documents: Generate Quick Quote, Generate Quote, Generate Final Contract, Generate Installation Proposal.
  - Sending and signing: Send Quote to Customer, Transmit E-Sign Quote, Upload Signed Quote.
  - Editing: Edit Quote Items.
  - Winning: Select As Winning Quote.
- **Services:** Process New Service, Schedule Service Appt, New Service Quote, Create Service PO, Create Manuf. SR Case, Create Service Invoice, Record Service Payment, Close Service.
- **Purchase Orders:** Orders Placed, Process Acknowledgments, Record PO Receiving, Confirm Direct Delivery, Add Items to PO, Cancel Purchase Order, Send PO to Accounting.
- **Appointments:** Start Appointment, Load Products, Scan Delivered Products, Customer Delivery Sign-Off, Complete Appointment, Cancel Appointment.

Many pathways whose names start with "(auto)" run on their own, from other pathways or actions; users don't start them.

### What a real run looks like

Real PATH WD pathways are often long and mostly automatic. "Select As Winning Quote" on a Quote has about 75 steps. It:
- looks up the Quote's Job, Customer and other linked records and links new records to them;
- creates new records;
- loops over the quote's items;
- records several workflow steps;
- fires actions and launches other pathways.

Along the way it stops for only a few screens: a Yes/No question, a choice between an existing record and a new one, and an upload of the signed quote. A wrong answer on one of those screens carries through every automatic step after it. That is why each screen should be answered only from what the user said.
