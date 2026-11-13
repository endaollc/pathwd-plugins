# Pathway screens: how to answer each one

A pathway run (`pathway-run start`) runs every automatic step and stops at each screen that needs a person. This page lists every screen the runner can stop on and exactly how to answer it with `pathway-run submit` (answer and continue) and `pathway-run act` (do something on the screen without continuing: search, page, scan, edit a row). What each screen means for the business, and what answering it changes, is in the [`path-concepts` guide](../../pathwd-guide/references/concepts.md)'s pathways reference. Every step type, the automatic ones included, with what it does and what the headless runner does with it, is in [pathway-steps.md](pathway-steps.md).

## Reading a paused screen

A paused run prints the screen, then the commands it accepts right now:

```
━━ Waiting for input · step "Enter Deposit" [EditRecord] · screen: edit-record ━━
  Step context: Record the deposit the customer paid.
Enter Deposit
  on Job 26-0074 (6a9056455ff33756afba5084)
  Editable fields:
  [191676] Deposit Amount (Currency, required) = (blank)
  [191674] Deposit Method (Selection) = (blank)
        options: "Check", "Credit Card", "Wire"

Next:
  pathway-run submit --set "Deposit Amount=<value>"
  pathway-run submit   (save unchanged)
  pathway-run cancel
(run 3f2b8c1e-5d47-4a8e-9b1c-2e6f0a9d7c34)
```

- The **`Next:` lines are the screen's own syntax**: the keys and flags it takes at this moment (a table without a search box has no `search` line). The sections below list everything each screen can take.
- The **header line** names the step, its type and the screen (`screen: <surface>`, the name the sections below go by). The screen itself lists everything it takes: each field with its type, whether it is required, its current value, every option and its context; numbered results with their ids; table columns and rows; file types and the default; the keys it accepts. A run that ends prints `✔ Pathway "<name>" finished.`, `✗ Pathway "<name>" failed at step "<title>" (<id>): <why>` or `■ Pathway "<name>" cancelled.`, then a line with its request and screen counts and `pathway-run log --run <id>`.
- `pathway-run show` prints the paused screen again; `pathway-run cancel` ends the run (work already saved stays saved). `--run <id>` picks a run; the default is the most recent one.
- A refused answer (a required field left empty, an option that doesn't exist, a file type that isn't allowed) prints `✗ <why>` and the run stays on the same screen, so it can be answered again. Nothing on that screen was saved.
- Screens save real data when they are submitted. Answers come from the user: a value they haven't decided is a question for them, not a guess from context or defaults.

## How answers are written

| Flag | On | Takes |
|---|---|---|
| `--set "<key>=<value>"` | `submit`, `act` | A field (`"Deposit Amount=1500"`) or a screen key (`search=Acme`, `note="Called"`). Repeatable; applied in the order given. Keys ignore case, and spaces, `_` and `-` are the same (`file_type`, `"file type"`). Quote the pair when it has spaces. |
| `--select <items>` | `submit`, `act` | Listed items by number (`2`, `#2`) or id (record id, file id), comma-separated for several (`1,3`). |
| `--yes` / `--no` | `submit` (and `act` on Create Tracking Labels' remove) | A Yes/No question's answer; a confirmation. |
| `--file <path>` | `submit`, `act` | A file to upload or attach; repeatable. |
| `--skip` | `submit` | The screen's "continue without" button, where it has one. |
| `--data '<json>'` | `submit` | The raw result the web screen would return, bypassing the screen's own checks. Only for a screen the CLI can't answer otherwise (`quote-builder-picker`). |

- **`act` keys also work on `submit`**: `submit` applies them first, then continues. `pathway-run submit --set search=1401 --select 1` searches and picks in one command.
- Field values on forms are typed text, read as the web form reads it: `50` in a Percent field is 50%, dates are `YYYY-MM-DD` on the user's clock, selections are the option's text. Every type's rules are in `field-values.md` (this folder).
- **Files on PATH WD Remote MCP:** `--file`, `body_file`, `upload` and other paths are workspace paths (`uploads/<id>/<name>` for a file the user dropped in the chat): the [`path-cli` guide](../../pathwd-guide/references/commands.md)'s "Files on this connection" says how a file gets there. A file a screen saves (an export, a downloaded document) comes back as a download link.

### Scripted answers (`--answers`)

`pathway-run start` and `submit` take `--answers '<json array>'` (or a file holding it): answers for the screens to come, used in order, each only on the screen it fits. A run with no fitting answer pauses as usual; with `--strict` it fails instead (exit 4).

```
pathway-run start --pathway-name "Record Deposit" --record-id 6a9056455ff33756afba5084 --answers '[
  {"surface": "edit-record", "set": {"Deposit Amount": "1500", "Deposit Method": "Check"}},
  {"surface": "yes-or-no", "choice": "no"}
]'
```

| Key | Means |
|---|---|
| `surface` | The screen the answer is for (`edit-record`); another screen doesn't use it. |
| `step` | The step id, its title, or `/regex/` on the title. |
| `set` | `{"<key>": "<value>"}`, or `[["key","value"], …]` when a key repeats or order matters (several scans). |
| `select`, `choice` (`"yes"`/`"no"`), `files` (`["uploads/…/photo.jpg"]`), `skip`, `data` | As the flags above. |
| `search` | Shorthand for a first `["search", "<text>"]`. |

## Every screen

<!-- generated:screens -->
The pathway runner stops on 31 kinds of screen. A paused run names its screen in its header line (`screen: <surface>`).

| `surface` | Answered with |
|---|---|
| `bin-check-in` | `submit` and `act` |
| `bin-check-out` | `submit` and `act` |
| `complete-task` | `submit` |
| `create-document-from-template` | `submit` |
| `create-or-lookup` | `submit` and `act` |
| `create-record` | `submit` |
| `create-task` | `submit` |
| `create-tracking-labels` | `submit` and `act` |
| `edit-document-from-template` | `submit` and `act` |
| `edit-record` | `submit` |
| `edit-record-field` | `submit` |
| `edit-record-table` | `submit` and `act` |
| `edit-task` | `submit` |
| `export-summary` | `submit` |
| `import-summary` | `submit` |
| `loading-session` | `submit` and `act` |
| `lookup-record` | `submit` and `act` |
| `merge-file` | `submit` and `act` |
| `message` | `submit` |
| `po-receiving` | `submit` and `act` |
| `post-note` | `submit` |
| `quote-builder` | `submit` and `act` |
| `quote-builder-picker` | `submit --data` only (a web-only screen) |
| `scan-session` | `submit` and `act` |
| `scheduling` | `submit` and `act` |
| `select-file` | `submit` |
| `send-email` | `submit` and `act` |
| `signature` | `submit` |
| `stop-receiving` | `submit` and `act` |
| `upload-file` | `submit` |
| `yes-or-no` | `submit` |
<!-- /generated:screens -->

The sections below follow the order a person most often meets them.

### Edit Record (`edit-record`)

A form for one record: every editable field, or the fields the designer picked.

- `submit --set "<Field>=<value>" [--set …]` saves the changes and continues. Fields are named by name or id; only the fields the screen lists are accepted.
- `submit` with no `--set` saves the record unchanged and continues.
- Required fields must have a value (one already on the record counts); email and phone fields are checked as in the web form.
- Calculated, system, Notes, Tag and Appointment fields never appear here.

### Create Record (`create-record`)

A form for a new record of the step's business object.

- `submit --set "<Field>=<value>" …` creates the record and continues. Required fields need a value; default values are already filled in.
- When the user holds more than one tag in a tag group the step leaves open, the form asks which: `--set "<Tag group field>=<tag name>"` (one of the user's own tags). `pathway-run start --tag "<tag>"` settles it before the run.
- The record is created when the screen is submitted; cancelling later doesn't remove it.

### Edit Record Field (`edit-record-field`)

One field of the record, often a selection shown as buttons, or an appointment.

- Ordinary field: `submit --set "<Field>=<value>"` (also `--set value=<value>`). `submit` alone keeps the current value. A selection's options are all listed on the screen (`options: …`).
- **Appointment field:** the editor starts with the field's appointment, or 8–9 am today with the field's Event Description title when nothing is scheduled.
  - `submit --set start="2026-10-15 09:00" [--set end="2026-10-15 10:00"] --set calendar=<id or name> [--set title="…"] [--set "all day"=yes] [--set "multi day"=yes] [--set location="…"] [--set description="…"] [--set color=<color>]`
  - Moving `start` keeps the appointment's length unless `end` is given. A calendar and a title are required; the screen lists the calendars the user can schedule on.
  - `submit` alone saves the editor as shown (once it has a calendar).
  - `submit --skip` is "Continue without Scheduling" (not offered when the field is required). A cleared `start` or `end` is refused: a pathway step never deletes an appointment.

### Lookup (`lookup-record`)

A list of records to pick one from, sometimes prefilled with a search or limited to a folder.

- `act --set search="<text>"` searches (the listing then shows matching records, numbered); `act --set page=2` shows the next page of results.
- `submit --select <number or record id>` picks a record and continues; `submit --set search="<text>" --select 1` does both.
- When the step continues on a single result and the first listing has exactly one, the run picks it by itself and doesn't stop.
- Picking from a listing with no results is refused: search first.

### Create or Lookup (`create-or-lookup`)

A Lookup with a Create tab.

- Pick an existing record: as Lookup (`act --set search=…`, `submit --select <n or id>`).
- Create one instead: `submit --set mode=create --set "<Field>=<value>" …` (the Create tab's fields are listed under `Create tab fields:`). Field `--set`s without `--select` also mean create.
- The created record is saved when the screen is submitted.

### Yes / No question (`yes-or-no`)

- `submit --yes` or `submit --no` (also `--set answer=<button text>`). The screen shows what each button says; the run goes down that branch.

### Message (`message`)

- `submit` continues (the screen's button). It takes no keys.

### Post Note (`post-note`)

Adds a note to a Notes field, sometimes showing the note history.

- `submit --set note="<text>"`; a required note can't be left empty. When the step only shows the history, `submit` continues.

### Create Task (`create-task`)

- From a template the step offers: `submit --set template=<id or title> [--set due_on=+3d] [--set assign_to=<user>]`. The templates offered are listed on the screen (`Templates: <id> "<title>", …`); the template sets the title, assignee and options, and `--set`s change them.
- A custom task: `submit --set title="Call the customer" --set assign_to=<user id, email or full name> --set due_on=2026-10-20 [--set notes="…"]`.
- Options (`true`/`false`): `notify_me_when_done`, `notify_assignee_when_due`, `can_be_assigned_by_others`, `can_be_skipped`, `only_assignee_can_finish`, `edit_permission_required_to_finish`.
- `assign_to` takes an active user; `unassigned` (or blank) leaves it unassigned. `due_on` takes the date forms of `field-values.md`; blank clears it.
- When the step fixes the template, `template` isn't needed; the other keys still apply.

### Edit Task (`edit-task`)

An existing task on the record: its title, assignee, due date, notes and options.

- `submit --set assign_to=<user> [--set due_on=…] [--set notes="…"] [--set title="…"] [--set <option>=true|false]`; the same keys as Create Task, without `template`. `submit` alone saves it unchanged.
- A user who neither created the task nor is an Admin can only change the assignee (the screen says `Only assign_to can change: …`); any other key is refused.

### Complete Task (`complete-task`)

Marks the task the run was started from as complete.

- `submit` completes it; `submit --set notes="<text>"` adds completion notes. When the run wasn't started from an open task, nothing is completed.
- A run starts from a task with `pathway-run start --record-id <id> --task-id <id>`.

### Select File (`select-file`)

The record's files the step offers, numbered.

- `submit --select <n or file id>[,…]`; several only when the step allows multiple. `submit` alone selects nothing, which a step that requires a selection refuses.

### Upload File (`upload-file`)

- `submit --file <path> [--file <path>] [--set file_type=<id or name>] [--set comment="…"]`. One file unless the step allows several. The screen lists the allowed file types and the default; a type is needed when there's no default.
- `submit` with no file continues without uploading, unless an upload is required.

### Signature (`signature`)

- `submit --set agree=true` ticks "I agree to use an electronic signature"; `--set acknowledge=true` ticks the step's own acknowledgement when it has one. Both are required.
- `--file <signature.png>` is the signature image. Without it, a generated placeholder signature is saved, which is only meaningful for test runs: a real signature comes from the signer in the web app.
- The record needs an identifier value to be signed.

### Create Document from Template (`create-document-from-template`)

- `submit --set file_name="<name>" [--set file_type=<id or name>] [--set comment="…"]`. A file name is required; the file type defaults to the step's.

### Edit Document from Template (`edit-document-from-template`)

The record merged into a document template, editable before the file is made.

- `submit` makes the file from the merged content as it is: attached to the record when the step adds it to files, downloaded when the step downloads it.
- `submit --set body_file=<path to edited HTML>` (or `--set body="<html>"`) replaces the body; the header and footer stay as merged.
- `--set format=PDF|DOCX`, `--set file_name="…"`, `--set comment="…"`.
- Page setup, in inches; unset ones keep the merged page: `size=letter|legal|a4|custom` (with `custom`: `page_width` and `page_height`, 1–22), `orientation=portrait|landscape`, `margin_top`, `margin_right`, `margin_bottom`, `margin_left`, `header_distance`, `footer_distance` (0–4).
- `act --set show_html=true` prints the body's HTML under the screen: the text to edit into `body_file`.
- `act --set download=true` (same page keys) saves a one-time copy without continuing; it isn't added to the record.

### Merge Files (`merge-file`)

Merges the record's files into one PDF.

- `act --select 3,1,2` picks the files to merge, in that order.
- `act --set file_name="…"`, `--set download=true|false`, `--set add_to_files=true --set file_type=<id or name>`, as the step shows them.
- When the step allows uploads: `act --file <path> --set upload_file_type=<id or name>` adds a file to the list (then select it). The screen lists the file types it takes (`File types: …`).
- `submit` merges and continues; it is refused until the selection and options are complete.

### Send Email (`send-email`)

The step's email, prefilled from its template. Submitting **sends a real email**.

- `act --set to=a@example.com[,b@example.com] [--set cc=…] [--set bcc=…]`
- `act --set subject="…" --set body="…"` (or `--set body_file=<path to HTML>`); `act --set show_html=true` prints the body's HTML (signature included) to edit into `body_file`
- `act --select <n,…>` toggles the record's files listed as attachments; `act --file <path>` uploads a file and attaches it; `act --set remove_upload=<n>` drops an uploaded one.
- `submit` sends (the Send button); it is refused while To, Subject or Body is empty. The keys also work on `submit`.

### Edit Record Table (`edit-record-table`)

A table of related records, with what the designer allows: search, edit cells, select rows, add, archive, save.

- `act --set search="<text>"`, `act --set page=<n>`
- `act --set "<row>.<Field>=<value>"` edits a cell (`row` = the row number or record id): `--set "2.Quantity=4"`. Only editable columns.
- `act --select 1,3` selects rows (single or multiple, as the table allows)
- `act --set add=true` adds a blank row; `act --set archive=<row>` archives a row's record; `act --set save=true` saves edits (the Save button)
- `act --set row=<n>` shows a row's Row Details
- `submit` continues with the selected rows (some tables require a selection). Edits not yet saved are saved on Continue only when the table auto-saves.

### Scheduling (`scheduling`)

Items of a job scheduled onto an appointment. Rows: Available `a1`, `a2`, …; Scheduled `s1`, `s2`, … (an id, identifier or barcode also works).

- `act --set schedule=a1` schedules a tracking item (or all of a quantity row); `act --select a1,a3` schedules several
- `act --set all=a2` schedules a whole quantity row; `act --set "qty.a2=3"` schedules 3 of it
- `act --set undo=s1` undoes a scheduled row
- `submit` (Continue) runs the step's processing; when that fails the screen stays and says why.
- Each reference reads the listing shown before the command.

### Scan sessions (`scan-session`, `loading-session`, `po-receiving`, `stop-receiving`, `bin-check-in`, `bin-check-out`)

Delivery (`scan-session`), Loading, PO Receiving, Stop Receiving, Bin Check In and Bin Check Out. Every typed code is one scan, as with a handheld scanner.

- `act --set scan=<barcode>` (repeatable, in order) or `act --set scans=A,B,C`. A row whose barcode isn't its identifier shows it (`· barcode <code>`).
- `act --set mark=<row>`: the row's button — Received / Receive All (PO Receiving), Received (Stop Receiving, when the user may), Loaded / Load All (Loading), Delivered / Deliver All (Delivery), Check In (Bin Check In), Check Out (Bin Check Out)
- `act --set qty=<row>:<count>`: a quantity line's stepper and ✓ (PO Receiving, Loading, Delivery)
- `act --set undo=true` undoes the latest action; `act --set undo=<row>` presses a row's Undo
- `act --set row=<row>` shows a row's details; `act --set retry=F<n>` / `act --set dismiss=F<n>` retry or dismiss an entry on the Failed list
- Bin Check In / Out: `act --set scan=<bin barcode>` switches bins (the previous bin's processing runs)
- `submit` finishes the session and runs its processing. An action the screen can't do (an unknown row, marking without permission) is refused and the session isn't finished.
- An unknown or repeated barcode goes on the Failed list, as in the web app; the session still finishes. In Loading and Delivery a pallet bin's barcode loads or delivers every item on it; if any item on the pallet isn't scheduled on the session's appointment, nothing on it is processed and it fails as "Pallet Refused" (`retry=F<n>` once the items are off).

### Create Tracking Labels (`create-tracking-labels`)

Labels for order lines, on a stop of a shipment. Keys apply in the order given, so one command can build a label.

- Stop: `act --set stop=<n or record id>` (an existing stop), `act --set stop_name="<name>"` (a new stop, made on submit), `act --set stop=new|existing`, `act --set stop_search=<text>`, `act --set close_shipment=true|false`
- Wizard: `act --set search=<order number or text>`, `act --set order=<n or record id>` (with no search: a recent order), `act --set recent_line=<n>`, `act --set line=<n or record id>`, `act --set size=6in|2in --set qty=<n>`, `act --set description="…"`, `act --set add=true` (Add Label)
- Import mode starts with `act --set barcode=<scanned code>`; `add=true` is Add to List.
- `act --set back=true|scan|order|line`, `act --set start_over=true`
- Labels: `act --set retry=<#n>`, `act --set remove=<#n> --yes` (archives its tracking item), `act --set download=<#n>`
- `submit` is Download and Continue (Submit in Import mode).
- Example: `act --set search=1401 --set order=1 --set line=2 --set size=6in --set qty=2 --set add=true`

### Quote Builder (`quote-builder`)

The whole quote on one form: Ship-to and Bill-to, Scope of Work, Notable Items, Outside Salesperson, Inside Support (when shown), Customer, Contact, the line items with totals and tax, Terms and the deposit. In create mode the step makes the Quote on the job when the screen opens and the quote number is taken then; a resumed run reuses that quote. Each `act` saves one change and prints the whole form again.

- Text: `act --set scope="…"`, `act --set notable="…"`, `act --set terms="Net 10th"`, `act --set deposit=<amount>` (blank = no override of the required deposit). The form lists the values Terms takes (`Options: …`) and the tax categories (`Tax categories: …`).
- Links: `act --set link.<card>=<record id>` for `customer`, `contact`, `ship_to`, `bill_to`, `outside_salesperson`, `inside_support`; each card lists its suggested records with their ids ("Choices: …"). `act --set unlink.<card>=true` removes one.
- New records: `act --set create.contact.first_name=Jane --set create.contact.last_name=Doe` creates and links a contact (First Name, Last Name, Email, Cell Phone, Work Phone, Contact Type, Job Title; it also belongs to the quote's customer); `create.ship_to.<field>` / `create.bill_to.<field>` create an address (Street, City, State, Zip). The card shows the fields it offers ("New: …").
- Lines: `act --set add_item="Marvin|Casement window|M-100|1000|Taxable"` (vendor | description | quote # | price | taxable; quantity is 1; a description or a price is needed), `act --set item.2.sell=1200`, `act --set item.2.tax_category=Taxable` (also `vendor`, `description`, `quote_number`), `act --set remove_item=2`, `act --set move_item=3:1`. Item numbers are the list as printed.
- Manufacturer quotes: `act --set upload=<path>` uploads a vendor's quote PDF; it is parsed into a new line (the form prints once the line arrives, the parse fails, or after two minutes). `act --set remove_upload=<n>` drops one still waiting.
- `submit` finalizes: it takes no keys and is refused until Customer, Contact, Ship-to, Bill-to, Outside Salesperson, Terms and at least one item with a description and a price are set (the form lists what's missing). Finalizing builds the quote PDF (summary, then terms), saves it to the quote and completes the quote.
- The step has no unattended version: a headless run (`execute-pathway --skip-screens`) skips it.

### Quote Builder record picker (`quote-builder-picker`)

The web's popover for picking or creating a record on a Quote Builder card. A CLI run doesn't stop here: the Quote Builder screen links by record id (`act --set link.<card>=<id>`). If a run does wait on it, only `submit --data '{"kind":"select","record":{…}}'` answers it.

### Import summary (`import-summary`) and export summary (`export-summary`)

- `submit` continues. On an export summary, `submit --set download=true` also saves the exported file.

## When the CLI can't answer a screen

A screen type this CLI doesn't know fails with `Unknown surface "<x>" — the CLI runner needs updating`. A CLI run doesn't show in the web app, so the user starts the pathway again there; what earlier screens saved stays saved.
