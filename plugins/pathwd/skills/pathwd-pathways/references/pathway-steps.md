# Pathway steps: every step type and what it does

A pathway is built from steps, and every step is one of PATH's step types: Edit Record, Fork, Send Email and so on. This page covers each type: its name in the Designer, whether it shows a screen, what it does to the record and to linked records, the main settings a designer picks for it, what the headless runner does with it, and how a `pathway-run` meets it. Designers title their steps, so a run or a map shows a step as "Enter Deposit" with its type beside it (`[EditRecord]`, `· Edit Record ·`).

How each screen is answered in a run is in [pathway-screens.md](pathway-screens.md). What a pathway is, where users start one and how a run flows, in business terms, is in the [`path-concepts` guide](../../pathwd-guide/references/concepts.md)'s [pathways.md](pathway-concepts.md).

## How steps run

- **Order.** A run starts at the first step and runs one step at a time. After a step, the run goes to the step's own branch when it has one (Fork, Yes or No Question, Relationship Lookup, Lookup Record's Not Found Step), else to the step's **Next Step** setting, else to the next step in the Designer's order. **Finish** as a next step ends the run.
- **The record a step works on.** Most steps have a **Record Step** setting (a "Step" picker): the pathway's starting record, or the record an earlier step produced. When the earlier step produced several records (a table's rows, a relationship lookup's matches), a step that takes one record takes the first. Inside a List Loop, a step whose Record Step is unset or names the loop works on the loop's current record.
- **What a step passes on.** Each step can pass records, and some pass files, to the steps after it. A step whose Record Step names an earlier step that produced no record stops the run with `Record not found for step <id>` (steps that take a list: `No records found for step <id>`). This is the usual way a skipped screen or a lookup that found nothing breaks a later step.
- **Failures.** A step that fails stops the run there: the run is failed, and everything earlier steps did stays done.
- **Saving.** Steps that change a record save it the way the web app saves one: calculated fields, milestones, tasks and actions can follow (the [`path-concepts` guide](../../pathwd-guide/references/concepts.md)'s [saving-records.md](../../pathwd-guide/references/concepts/saving-records.md)).

### Screens, automatic steps and the headless runner

- **Screen** steps stop and wait for a person in the web app. `pathway-run start` stops at the same screens; each is answered with `pathway-run submit` (and `pathway-run act` for actions that don't continue), as [pathway-screens.md](pathway-screens.md) describes.
- **Automatic** steps do their work on their own. Many screen types have a setting (Auto Create, Auto Set, Auto Send, …) that makes them automatic.
- **Sometimes** means the step shows its screen only in some cases. This is the same classification `pathway-run map` prints for each step.
- **The headless runner** runs a pathway with no screens. `execute-pathway`, scheduled pathways, actions that run a pathway, and integrations run pathways this way. On it, a Yes or No Question takes its No answer, and any other screen ends its step at that point: whatever the step does before showing its screen still happens, the rest doesn't, the step passes nothing on, and the run carries on with the step's Next Step. `execute-pathway` runs nothing when the pathway has screen steps a run can reach, unless `--skip-screens` is given; it then lists each one with what the headless runner does with it.

## Every step type

<!-- generated:step-types -->
PATH has 46 pathway step types; the Designer offers 43 of them. "Screen" means the step stops and shows a screen in the web app (and in `pathway-run`), "automatic" that it does its work on its own, "sometimes" that it shows a screen only in some cases. The headless runner is how `execute-pathway`, scheduled pathways and integrations run a pathway: with no screens.

| Step (Designer name) | Type | Shows a screen | On the headless runner |
|---|---|---|---|
| Archive Record | `archive` | automatic | runs |
| Assign Fields | `assign_fields` | automatic | runs |
| Complete Task | `complete_task` | automatic; with Edit Notes: screen | runs; with Edit Notes: skipped |
| Create Barcode | `create_barcode` | screen | skipped |
| Create Document From Template | `create_document_from_template` | screen; with Auto Generate: automatic | skipped; with Auto Generate: runs |
| Create or Lookup Record | `create_or_lookup_record` | screen | skipped |
| Create Record | `create_record` | screen; with Auto Create: automatic | skipped; with Auto Create: runs |
| Create Task | `create_task` | screen; with Auto-create: automatic | skipped; with Auto-create: runs |
| Custom Code | `custom_code` | automatic | runs |
| Deactivate Workflow Step | `deactivate_workflow_step` | automatic | runs |
| Delete File | `delete_file` | automatic | runs |
| Delete Record (not in the Designer) | `_delete_record` | — | not run: a run that reaches it fails, in the web app too |
| Display Folder | `display_folder` | automatic | runs |
| Display Record | `display_record` | automatic | runs |
| Download Document (not in the Designer) | `download_document` | — | not run: a run that reaches it fails, in the web app too |
| Edit Document From Template | `edit_document_from_template` | screen | skipped |
| Edit Record | `edit_record` | screen | skipped |
| Edit Record Field | `edit_record_field` | screen; with Auto Set: automatic | skipped; with Auto Set: runs |
| Edit Record Table | `edit_record_table` | screen | skipped |
| Edit Task | `edit_task` | screen | skipped |
| Execute Action | `execute_action` | automatic | runs |
| Export File | `export_file` | screen | still exports; only its summary is skipped |
| Fetch Record (not in the Designer) | `fetch_record` | — | not run: a run that reaches it fails, in the web app too |
| Fork | `fork` | automatic | runs |
| Get Current User | `get_current_user` | automatic | runs |
| Import File | `import_file` | screen | still imports; only its summary is skipped |
| Launch Pathway | `launch_pathway` | automatic | runs |
| Link Record | `link_record` | automatic | runs |
| List Loop | `list_loop` | automatic | runs |
| Lookup Record | `lookup_record` | screen; with Auto-lookup: automatic | skipped; with Auto-lookup: runs |
| Merge Files | `merge_files` | screen | skipped |
| Message | `message` | screen | skipped |
| Post Note | `post_note` | screen; with Add Note and Show Note History both off: automatic | skipped; with Add Note and Show Note History both off: runs |
| Quote Builder | `quote_builder` | screen | skipped |
| Reactivate Workflow Step | `reactivate_workflow_step` | automatic | runs |
| Relationship Lookup | `relationship_lookup_record` | automatic | runs |
| Run Workflow Step | `run_workflow_step` | automatic | runs |
| Save Record | `save_record` | automatic | runs |
| Scan Session | `scan_and_process` | screen | skipped |
| Scheduling | `scheduling` | screen | skipped |
| Select Files | `select_file` | screen; with Auto Continue on Single File: sometimes; with Auto Select File: automatic | skipped; with Auto Continue on Single File: continues with the file when exactly one matches, else skipped; with Auto Select File: runs |
| Send Email | `send_email` | screen; with Auto Send: sometimes | sends the email unreviewed when it has a To address, a subject and a body, else skipped |
| Signature | `signature` | screen | skipped |
| Unlink Record | `unlink_record` | automatic | runs |
| Upload File | `upload_file` | screen; with Auto upload: automatic | skipped; with Auto upload: runs |
| Yes or No Question | `yes_or_no_question` | screen | takes the No answer |
<!-- /generated:step-types -->

The sections below are grouped by what the steps are for.

## Records

### Create Record (`create_record`)

Screen; automatic with **Auto Create**. Creates one new record of the step's business object and passes it on.

- **Does:** the screen is a form for the new record; the record is created when it is submitted. With Auto Create, the record is created with no values typed in; the business object's default values still apply. The creator is the user running the pathway. The new record's tags come from the step's tag overrides, then a tag chosen earlier in the run, then the user's own tags; a tag picked on the form is used for the rest of the run, including pathways it launches.
- **Settings:** **Business Object** (what to create); **Auto Create**; **Field Selection** (the form's fields, each optionally **Required**, selection fields optionally limited to some options; no fields picked means every field); **Tag Overrides** (a tag group and the tag the new record gets).
- **Headless runner:** with Auto Create, the record is created. Without it, the step is skipped: nothing is created and later steps that use its record fail.
- **In `pathway-run`:** the `create-record` screen (Create Record in [pathway-screens.md](pathway-screens.md)); none with Auto Create.

### Create or Lookup Record (`create_or_lookup_record`)

Screen, always. Picks an existing record or creates a new one, and passes that record on.

- **Does:** two tabs. Lookup searches the business object like Lookup Record, inside the step's folder when it has one; picking a record changes nothing. Create is a Create Record form; the record is created when the screen is submitted, with the same creator and tag rules as Create Record.
- **Settings:** the Create Record settings without Auto Create; a **Lookup** panel with **Folder**, **Prefill search results**, **Auto-continue on single result** and **Use camera**. The business object is the one being created.
- **Headless runner:** skipped: nothing is picked or created, and later steps that use its record fail.
- **In `pathway-run`:** the `create-or-lookup` screen (Create or Lookup in [pathway-screens.md](pathway-screens.md)).

### Lookup Record (`lookup_record`)

Screen; automatic with **Auto-lookup**. Finds one existing record and passes it on. It changes nothing by itself.

- **Does:** on the screen the user searches the business object (inside the step's folder, when set) and picks a record. With Auto-lookup, the step searches on its own and takes the first result: the search term is the step's **Search term** (which can hold the values of a record from an earlier step), or, with no term, the folder's or business object's records. A search leaves archived records out. A term that comes out blank counts as not found.
- **Settings:** **Business Object**; **Folder** (a hard limit in both modes); **Prefill search term** with **Search from a step**, **Search Record Step** and **Search term**; **Allow auto-continue** with **Auto-lookup** and **Not Found Step**; **Prefill search results** (the screen starts with the term searched); **Auto-continue on single result**; **Use camera**.
- **Headless runner:** with Auto-lookup, the search runs as above. Without it, the step is skipped and passes nothing on.
- **In `pathway-run`:** the `lookup-record` screen (Lookup in [pathway-screens.md](pathway-screens.md)). With Auto-continue on single result, a first listing with exactly one record is picked without stopping.
- **Outcomes:** when Auto-lookup finds nothing, the run goes to the **Not Found Step**; with none set, it goes on to the next step with no record, and a later step that uses this step's record fails with `Record not found for step <id>`.

### Edit Record (`edit_record`)

Screen, always. A form for one record; submitting saves it.

- **Does:** shows the record's fields (all of them, or the designer's pick) with their current values and saves the submitted form to the record. Passes the saved record on.
- **Settings:** **Record Step**; **Field Selection** (fields, each optionally **Required**, selection fields optionally limited to some options; no fields picked means every editable field).
- **Headless runner:** skipped: nothing is saved. When its Record Step has no record, the run fails with `Record not found for step <id>` before the screen.
- **In `pathway-run`:** the `edit-record` screen (Edit Record in [pathway-screens.md](pathway-screens.md)).

### Edit Record Field (`edit_record_field`)

Screen; automatic with **Auto Set**. Writes one field of one record.

- **Does:** on the screen the user sets the field's value (a selection can be shown as buttons); with Auto Set the step writes the designer's value. Auto Set values can be fixed text (which can contain other fields' values), a preset value, **Current Date** (noon today) or **Current Date & Time** (now). An appointment field opens the appointment editor: saving places the appointment on the chosen calendar, and "Continue without Scheduling" writes nothing. Passes the updated record on.
- **Settings:** **Record Step**; the **Field**; **Required**; **Auto Set** and **Auto Set Value**; **Selection Buttons**; **Limit Selection Options**.
- **Headless runner:** with Auto Set, the value is written. Without it, the step is skipped. Either way, a step whose Record Step has no record fails the run.
- **In `pathway-run`:** the `edit-record-field` screen (Edit Record Field in [pathway-screens.md](pathway-screens.md)); none with Auto Set.

### Edit Record Table (`edit_record_table`)

Screen, always. A table of records the user can edit, add to, archive and select from.

- **Does:** the rows are all records of a business object, a folder's records, the records linked to an earlier step's record through a relationship, or the records an earlier step produced, narrowed by the step's filter. Depending on the settings the user edits cells and saves, adds rows (a new record is created at once, linked to the parent record when the rows come from a relationship), archives rows (after a confirmation), reorders and selects rows. On Continue, unsaved edits are saved only with **Auto Save on Complete**; otherwise they are dropped.
- **Passes on:** with selection off, every row in the order shown; with multi-select, the selected rows; with single select, the picked row first. Nothing when nothing is picked or the table is empty. A List Loop after it often works through these rows.
- **Settings:** **Records** (**Type**: Records, Folder, Relationship or Records from a Step, with its business object, folder, step and relationship); **Filter** and **Enable Search** (the table starts empty until a search, except for Records from a Step); **Table Columns**, **Table Row Details**, **Summary Fields**, **Default Sort**; **Allow Row Selection**, **Multi Select**, **Selection Required**, **Auto Continue When Only One Result** (only together with Selection Required), **Draggable Rows**; **Allow Create**, **Allow Save**, **Auto Save on Complete**, **Allow Delete** (archives), **Require Search Before Add**.
- **Headless runner:** skipped: nothing is edited, and the step passes nothing on.
- **In `pathway-run`:** the `edit-record-table` screen (Edit Record Table in [pathway-screens.md](pathway-screens.md)).
- **Outcomes:** `Please select at least one record to continue` when selection is required.

### Save Record (`save_record`)

Automatic. Saves a record again without changing any value.

- **Does:** recalculates the record on the server, as a save does (calculated fields, totals, milestones), and passes the recalculated record on.
- **Settings:** **Record Step**.
- **Headless runner:** runs.
- **Outcomes:** when its Record Step produced no record on this run, the step does nothing and the run continues (unlike most steps, it doesn't fail).

### Archive Record (`archive`)

Automatic. Archives a record.

- **Does:** archives the record (a record that is already archived gets a new archive date), archives its calendar events, and records "Archived" in its history. Totals on linked records are recalculated a moment later, as if the record had been unlinked. Passes the archived record on, so later steps can still use it.
- **Settings:** **Record Step**.
- **Headless runner:** runs.
- **Outcomes:** `Record not found for step <id>` when its Record Step has no record.

### Assign Fields (`assign_fields`)

Automatic. Copies field values from one record to another.

- **Does:** for each pair, copies the **From** record's field value onto the **To** record's field; an empty source field empties the target field. The To record is saved as any save is, with an "Updated" entry in its history. Passes the To record on.
- **Settings:** **From** and **To** (record steps); **Fields**: **From Field** → **To Field** pairs.
- **Headless runner:** runs.
- **Outcomes:** `Record not found for step <id>` when either record is missing.

### Get Current User (`get_current_user`)

Automatic. Finds the Person record of the user running the pathway and passes it on, so later steps can link to it or copy from it.

- **Settings:** none.
- **Headless runner:** runs, for the user the run is attributed to (for a scheduled or integration run that can be the account's system user).
- **Outcomes:** the run fails when the user has no Person record (`Person record not found for user ID …`).

## Linked records

### Link Record (`link_record`)

Automatic. Links one record to another through a relationship.

- **Does:** sets the record's link for the chosen relationship to the **Target Record**. A relationship holds one linked record per record, so a link it already had is replaced (the replaced record shows "Unlinked" in its history, and its totals are recalculated a moment later). Both records show "Linked" in their history. Fields and milestones that depend on the link are recalculated. Passes the linked record (not the target) on. Linking to the record already linked isn't an error.
- **Settings:** **Record Step**; **Relationship** (one of the record's own relationships); **Target Record** (a step).
- **Headless runner:** runs.
- **Outcomes:** `Record not found for step <id>` when either record is missing, including when no Target Record is set.

### Unlink Record (`unlink_record`)

Automatic. Clears a record's link for a relationship, whichever record it pointed to.

- **Does:** removes the link, records "Unlinked" on both records, recalculates what depends on it, and passes the record on. Unlinking an empty relationship changes nothing.
- **Settings:** **Record Step**; **Relationship**.
- **Headless runner:** runs.

### Relationship Lookup (`relationship_lookup_record`)

Automatic, with two outcomes. Finds the records linked to a record and passes them on. It changes nothing.

- **Does:** with **Relationship Type** Source, finds the one record the record links to through one of its own relationships; with Target, finds every record of another business object that links to this record. Archived records and records the user can't see aren't found.
- **Settings:** **Record Step**; **Relationship Type** (Source or Target); **Relationship**; **Found Step**; **Not Found Step**.
- **Headless runner:** runs.
- **Outcomes:** found goes to the Found Step; not found goes to the Not Found Step, and when none is set the run ends there (finished, not failed). A later Fork on a lookup that found nothing takes its False branch.

## Branches, loops and nested pathways

### Fork (`fork`)

Automatic, with two outcomes. Checks a Logic Builder condition on a record and branches.

- **Does:** checks the condition against the record as last saved, and goes to the **True Step** or the **False Step**. It changes nothing and passes the record on. An archived record, or no record at all (for example from a lookup that found nothing), takes the False branch.
- **Settings:** **Record Step**; the condition (**Logic Settings**); **True Step**; **False Step** (either can be Finish).
- **Headless runner:** runs.
- **In `pathway-run`:** `pathway-run map --record-id <id>` marks the branch a Fork on the starting record takes now (`[taken now]`).
- **Outcomes:** `Condition Setting config not found` with no condition; `Fork true step is not configured` / `Fork false step is not configured` when the branch taken has no step.

### Yes or No Question (`yes_or_no_question`)

Screen, always. Asks a question and branches on the answer.

- **Does:** shows the **Question** (which can show the values of the record from its Record Step, as last saved) with two buttons, and goes to the **Yes Step** or the **No Step**. Nothing is saved, and the step passes no record on.
- **Settings:** **Record Step** (optional); **Question**; **Yes Text** and **Yes Step**; **No Text** and **No Step** (button texts default to Yes and No).
- **Headless runner:** takes the No answer and goes to the No Step.
- **In `pathway-run`:** the `yes-or-no` screen (Yes / No question in [pathway-screens.md](pathway-screens.md)).
- **Outcomes:** `Invalid steps ID mapped to the configuration` when a branch has no step, also on the headless runner.

### Message (`message`)

Screen, always. Shows a message with one button.

- **Does:** shows the **Message Text**, which can hold the values of the record from its Record Step, as last saved. Nothing changes.
- **Settings:** **Record Step** (optional); **Message Text**; **Button Text** (default "Okay").
- **Headless runner:** skipped.
- **In `pathway-run`:** the `message` screen (Message in [pathway-screens.md](pathway-screens.md)).

### List Loop (`list_loop`)

Automatic; the steps inside it show their own screens. Repeats a block of steps once for each record of a list.

- **Does:** takes every record the **List Step** produced (a table's rows, a relationship lookup's matches, …) and runs the steps from **Start Step** to **End Step** once per record. Inside the block, a step with no Record Step, or one naming the loop, works on the current record; "the starting record" still means the pathway's own. After the last record, the run goes on from the End Step's Next Step, or the step after the End Step. With no records, the block doesn't run and the run goes on past the End Step. Passes the looped records on, with the block's changes; records produced inside the block can't be used after the loop.
- **Settings:** **List Step**; **Start Step**; **End Step**; **Show Progress** with its **Status Message** (shown as "<message> (3 of 7)" when there is more than one record).
- **Headless runner:** runs; the steps inside follow the headless rules on each pass.
- **Outcomes:** a step that fails on any pass fails the whole run. A branch inside the block that leads outside it fails the run with `Step not found for id <id>`; Finish inside the block ends only that pass. `Start step ID is not configured in ListLoop` / `End step ID is not configured in ListLoop`, and `Invalid step ordering: …` when the List Loop doesn't come before its Start Step or the Start Step before the End Step.

### Launch Pathway (`launch_pathway`)

Automatic; the launched pathway's screens appear in the same run. Runs another pathway in the middle of this one, then carries on.

- **Does:** runs the chosen pathway once, on the record from its Record Step (when that step produced several, the first); a pathway that starts without a record runs without one. The launched pathway's changes are visible to the steps after it; the records its own steps produced aren't. Display Record and Display Folder steps inside it count for the whole run. A launched pathway that has validation errors is skipped and the run carries on.
- **Settings:** **Record Step**; **Pathway** (pathways that start on the record's business object); **Auto-run** with **Auto-run status** (only the wording of the "working" message; the launched pathway's screens still appear).
- **Headless runner:** runs the launched pathway on the headless runner too, with the same rules.
- **In `pathway-run`:** the run prints `▸ Sub-pathway "<name>" (<id>) starts`, and its screens are answered like any other. `pathway-run map --sub-pathways` maps the launched pathways too.
- **Outcomes:** a failure inside the launched pathway fails the whole run; cancelling cancels both. Pathways nested more than 10 deep (list loops count) fail with `Pathway run exceeded maximum depth`. `No records found for step <id>` when the Record Step never produced records (for example a screen the headless runner skipped).

## Workflow, tasks and actions

### Run Workflow Step (`run_workflow_step`)

Automatic. Records a workflow step on a record.

- **Does:** records the chosen workflow step on the record as the user running the pathway, even when the step is already active, adds it to the record's history, and re-checks the record's milestones, which can move the record and set off tasks and actions (the [`path-concepts` guide](../../pathwd-guide/references/concepts.md)'s [workflow.md](../../pathwd-guide/references/concepts/workflow.md)). A workflow step that has been turned off is skipped without a change. Passes the record on.
- **Settings:** **Record Step**; **Workflow Step** (enabled ones).
- **Headless runner:** runs.

### Deactivate Workflow Step (`deactivate_workflow_step`)

Automatic. Undoes a workflow step on a record.

- **Does:** deactivates the most recent active run of the chosen workflow step (when it was recorded more than once, the older ones keep it active), records the change in the history, and re-checks milestones, so the record can leave a stage. Nothing changes when the step isn't active. Passes the record on.
- **Settings:** **Record Step**; **Workflow Step**.
- **Headless runner:** runs.

### Reactivate Workflow Step (`reactivate_workflow_step`)

Automatic. Restores a workflow step that was deactivated.

- **Does:** reactivates the most recent deactivated run of the chosen workflow step, records the change in the history, and re-checks milestones. Nothing changes when there is none. Passes the record on.
- **Settings:** **Record Step**; **Workflow Step**.
- **Headless runner:** runs.

### Create Task (`create_task`)

Screen; automatic with **Auto-create**. Creates a task on a record.

- **Does:** on the screen the user picks one of the task templates available in the record's current milestones (or the step's fixed template) and can change its title, assignee, due date, notes and options, or writes a custom task. With **Create from template** and **Auto-create**, the step creates the template's task with no screen; the assignee comes from the template. A template task isn't created when the template is turned off or the record already has a pending task from it; the step still succeeds. Passes the record on.
- **Settings:** **Record Step**; **Create from template** with **Task** (the template) and **Auto-create**; **Task Name** (without a template).
- **Headless runner:** with Auto-create, the task is created. Without it, the step is skipped and no task is created.
- **In `pathway-run`:** the `create-task` screen (Create Task in [pathway-screens.md](pathway-screens.md)).
- **Outcomes:** `This task template is turned off` from the screen.

### Edit Task (`edit_task`)

Screen, always. Edits a task created from a template on the record.

- **Does:** finds the record's task from the chosen template and shows its title, assignee, due date, notes and options; saving updates the task. The task's creator and admins can change everything; others can only change the assignee, and only when the task allows it.
- **Settings:** **Record Step**; **Task** (the template).
- **Headless runner:** the task is looked up, then the step is skipped: the task isn't changed.
- **In `pathway-run`:** the `edit-task` screen (Edit Task in [pathway-screens.md](pathway-screens.md)).
- **Outcomes:** `Record task not found` when the record has no task from that template (this fails the run, on the headless runner too); `Only the task creator can edit this task`; `Only the task creator can change who this task is assigned to`.

### Complete Task (`complete_task`)

Automatic; a notes screen with **Edit Notes**. Completes the task the pathway was started from.

- **Does:** when the run was started from a task (completing a task that carries a pathway; `pathway-run start --task-id`, `execute-pathway --data '{"task_id": …}'`) and that task is still pending on the run's record, completes it as the user running the pathway. With Edit Notes, a screen first shows the task's notes for editing; it appears only when there is such a task. Without a pending task from the run, nothing happens and the run carries on. It works on the starting record (or a List Loop's current record) and passes it on.
- **Settings:** **Edit Notes**.
- **Headless runner:** without Edit Notes, the task is completed. With Edit Notes, the step is skipped and the task stays open.
- **In `pathway-run`:** the `complete-task` screen (Complete Task in [pathway-screens.md](pathway-screens.md)).
- **Outcomes:** a pathway that starts without a record fails here with `Record not found for step <id>`, even when it wasn't started from a task.

### Execute Action (`execute_action`)

Automatic. Fires an action on a record (the [`path-concepts` guide](../../pathwd-guide/references/concepts.md)'s [actions.md](../../pathwd-guide/references/concepts/actions.md)).

- **Does:** fires the chosen action on the record, as the user running the pathway, with the files from the **File Step** when one is set. With **Wait for action to complete**, the step waits for the action and passes the record on as it is afterwards, and an action error fails the step. Without it (the default), the step carries on at once; the action finishes in the background, and its errors don't stop the run.
- **Settings:** **Record Step**; **Action**; **Wait for action to complete**; **File Step**.
- **Headless runner:** runs.
- **Outcomes:** `Execute Action needs a record: choose the record the action runs on in the step settings`.

### Custom Code (`custom_code`)

Automatic; shows a message when it fails. Runs account-specific processing on the server, built for the account.

- **Does:** runs the processing the step names on the record from **Record** and the records from **Record List**, showing the **Status Message** while it works. What it changes depends on the processing. It passes on the records the processing returns, else the record, as it is afterwards. When the processing reports a failure, a "Custom code failed" message with the reason and "The pathway will continue." appears, and the run carries on after Continue.
- **Settings:** **Record** and **Record List** (steps); **Custom Code Selector** (the processing); **Status Message**; **Wait for process to complete** (default on; when off, the step only learns whether the processing was accepted).
- **Headless runner:** runs. A reported failure shows nothing; the step passes nothing on and the run carries on.
- **In `pathway-run`:** a failure stops on a `message` screen.

## Files, documents, email and notes

### Upload File (`upload_file`)

Screen; automatic with **Auto upload**. Attaches files to a record.

- **Does:** on the screen the user uploads one or more files with a file type and an optional comment; they are attached to the record at once. With Auto upload, the step copies a file from the **Auto Upload Step** onto the record with the step's file types: the file that step produced, or, when it produced none, the first file on that step's record. Only one file is copied. Passes the record and the attached files on.
- **Settings:** **Record Step**; **Allow Multiple Files**; **File Types**; **Upload Required**; **Auto upload** with **Auto Upload Step**.
- **Headless runner:** with Auto upload, the file is copied. Without it, the step is skipped.
- **In `pathway-run`:** the `upload-file` screen (Upload File in [pathway-screens.md](pathway-screens.md)).
- **Outcomes:** `At least 1 file upload is required`.

### Select Files (`select_file`)

Screen; automatic with **Auto Select File**; sometimes with **Auto Continue on Single File**. Picks files already on a record for later steps.

- **Does:** lists the record's files, limited to the step's file types. The user picks one or several. With Auto Select File the step picks on its own: every matching file with Multi Select, else the newest. With Auto Continue on Single File, exactly one matching file is picked without a screen. With **Allow Download** the picked files are downloaded. When the **Save** settings name a record, the picked files are copied onto that record with the Save file type. Passes the record and the picked files on (Send Email attachments, Merge Files, Delete File and Import File use them).
- **Settings:** **Record Step**; **Auto Select File**; **Multi Select**; **Auto Continue on Single File**; **Selection Required**; **Allow Download**; **File Types**; **Show Search Bar**; **Save Record Step** with **File Type**.
- **Headless runner:** with Auto Select File, the files are picked and copied (no download). With Auto Continue on Single File and exactly one matching file, the step continues with it. Otherwise it is skipped and passes no files on.
- **In `pathway-run`:** the `select-file` screen (Select File in [pathway-screens.md](pathway-screens.md)).
- **Outcomes:** `You must select a file before continuing`; `File type is required when copying files to another record`.

### Delete File (`delete_file`)

Automatic. Removes from a record the files an earlier step produced.

- **Does:** takes the files the chosen step passed on and removes them from that step's record. It removes only files attached to that record, so it removes nothing after a step whose file isn't on the record (a document not added to the record's files). It does nothing after a step that passes no files (Signature, Import File, Send Email, a skipped screen). After Select Files it removes the picked files themselves.
- **Settings:** **Step** (the step whose files to remove).
- **Headless runner:** runs.
- **Outcomes:** `Record step ID is required for Delete File step` when no step is set.

### Merge Files (`merge_files`)

Screen, always. Combines files into one PDF.

- **Does:** lists the record's files that can be merged (images, PDFs, Word documents). The user picks them, puts them in order, and can upload more first. Continue makes one PDF; it is added to the record's files when **Add To Files** is ticked on the screen (with a file type), and downloaded when the step allows. Passes the record on, and the PDF when it was added to the record.
- **Settings:** **Record Step**; **Download**; **Allow intermediate uploads**; **Save with file type** with **File Type**; **Custom file name** (can hold field values).
- **Headless runner:** skipped: nothing is merged.
- **In `pathway-run`:** the `merge-file` screen (Merge Files in [pathway-screens.md](pathway-screens.md)).
- **Outcomes:** `You must add at least one file`; `Choose a file type for the merged file (Add To Files is ticked)`.

### Create Document From Template (`create_document_from_template`)

Screen; automatic with **Auto Generate**. Generates a document, such as a quote or contract, from a document template filled with the record's values.

- **Does:** on the screen the user gives a file name, a file type and an optional comment. With Auto Generate, the document is made with no screen and named from the template. The document is added to the record's files only with **Add to Files** and a file type; otherwise it is kept but not on the record. When added, it gets the step's **Comment** (or the one typed on the screen, which starts with it) as its file comment; a blank comment adds none. Passes the record and the document on (for Send Email attachments, Upload File's Auto upload, Delete File).
- **Settings:** **Record Step**; **Document Template**; **File Format** (PDF, DOCX, XLSX or CSV; PDF by default); **Auto Generate**; **Add to Files**; **File Type**; **Comment** (optional; only with Add to Files).
- **Headless runner:** with Auto Generate, the document is made. Without it, the step is skipped.
- **In `pathway-run`:** the `create-document-from-template` screen (Create Document from Template in [pathway-screens.md](pathway-screens.md)).
- **Outcomes:** `Document template with id <id> does not have a template file`.

### Edit Document From Template (`edit_document_from_template`)

Screen, always. Fills a document template with the record's values and opens it in an editor.

- **Does:** the user edits the merged document (after a read-only review with **Review Mode**); Continue makes the final PDF or Word file, adds it to the record's files with **Add to Files**, and downloads it with **Auto Download**. Passes the record and the file on.
- **Settings:** **Record Step**; **Document Template**; **File Format** (PDF or DocX, or left for the user to choose); **Auto Download**; **Add to Files**; **File Type**; **Review Mode**.
- **Headless runner:** the template is filled, then the step is skipped: no document is made.
- **In `pathway-run`:** the `edit-document-from-template` screen (Edit Document from Template in [pathway-screens.md](pathway-screens.md)).

### Import File (`import_file`)

Automatic import, then a summary screen. Creates records from a file.

- **Does:** runs the chosen import profile on the files the **File Step** produced (an upload, a picked file). Each row creates a new record of the profile's business object (rows never update existing records), with linked records the profile maps, and runs the profile's own pathway on it when it has one. Rows that fail are listed, not fatal. The summary shows how many records were created and the failed rows. The created records aren't passed on.
- **Settings:** **Profile**; **File Step**. It always works on the starting record.
- **Headless runner:** imports; only the summary is skipped.
- **In `pathway-run`:** the `import-summary` screen (Import summary in [pathway-screens.md](pathway-screens.md)).
- **Outcomes:** `No file found in step <id> to import` when the File Step produced no file (for example a screen the headless runner skipped); row errors such as `Row N: missing required field(s): …`.

### Export File (`export_file`)

Automatic export, then a summary screen. Exports records to a file.

- **Does:** exports the records from its Record Step with the chosen export profile. When exactly one record is exported, the file is added to its files; otherwise to the files of the List Loop's current record, or of the starting record. Passes the exported records and the file on (Send Email can attach it).
- **Settings:** **Record Step**; **Profile**.
- **Headless runner:** exports and attaches the file; only the summary is skipped.
- **In `pathway-run`:** the `export-summary` screen (export summary in [pathway-screens.md](pathway-screens.md)); `submit --set download=true` also saves the file.
- **Outcomes:** `No record found in step <id> to export`.

### Send Email (`send_email`)

Screen; sometimes with **Auto Send**. Sends an email, usually about the record, and logs it on the record.

- **Does:** fills **To**, **CC**, **BCC**, **Subject** and **Body** from the step's template with the record's values. On the screen the user reviews, edits, attaches files (the record's own and earlier steps') and sends; the user is copied by default. With Auto Send, the email goes out with no screen when it has a To address, a subject and a body after filling in, else the screen opens; an automatic send copies the user running the pathway. The email goes out from the user running the pathway and is logged in the record's emails. When no recipients are left the email isn't sent and the step still finishes. Passes the record on, never files.
- **Settings:** **Record Step** (optional); **To**, **CC**, **BCC**, **Subject**, **Body** (can hold field values); **Attachments Step** (whose files are attached); **Auto Send**.
- **Headless runner:** sends unreviewed whenever it has a To address (CC or BCC alone isn't enough), a subject and a body, whatever Auto Send says, with no email signature and only the Attachments Step's files; otherwise skipped.
- **In `pathway-run`:** the `send-email` screen (Send Email in [pathway-screens.md](pathway-screens.md)).

### Signature (`signature`)

Screen, always. Captures a signature.

- **Does:** a signature pad with the required "I agree to use an electronic signature" tick, and the designer's **Acknowledgement Text** tick when set. The signature is saved to the record's files as `signature.png` with the step's file type. Passes the record on, not the file.
- **Settings:** **Record Step**; **File Type**; **Show Restart Button**; **Custom Acknowledgement** with **Acknowledgement Text**; **Enable Download Button** with its text and file type.
- **Headless runner:** skipped: nothing is signed.
- **In `pathway-run`:** the `signature` screen (Signature in [pathway-screens.md](pathway-screens.md)).

### Post Note (`post_note`)

Screen; automatic with **Add Note** and **Show Note History** both off. Adds a note to a Notes field of the record.

- **Does:** with Add Note, a note typed on the screen is added to the chosen Notes field with the user's name and the time; **Show Note History** shows the earlier notes. Passes the record on.
- **Settings:** **Record Step**; **Title**; **Field** (a Notes field); **Add Note** with **Add Note Required**; **Show Note History**.
- **Headless runner:** skipped: no note is added.
- **In `pathway-run`:** the `post-note` screen (Post Note in [pathway-screens.md](pathway-screens.md)).

## Warehouse, scheduling and quotes

### Scan Session (`scan_and_process`)

Screen, always. A warehouse or delivery scanning screen; its **Mode** decides which.

- **Does:** opening the screen starts a scan session linked to the step's record: a stop (Stop Receiving), a Purchase Order (PO Receiving), an appointment (Loading, Delivery) or a bin (Bin Check-In, Bin Check-Out). Each scan or button press is saved at once. Continue finishes the session and runs the account's end-of-session processing, which updates quantities, statuses and linked records. Passes the scan session on.
- **Settings:** **Record Step** (labelled Stop, Purchase Order, Appointment or Bin Record); **Mode**; **Post Processing Custom Code** (the end-of-session processing); **Camera scan enabled**; **Peripheral scan enabled**.
- **Headless runner:** skipped: no session starts and nothing is scanned.
- **In `pathway-run`:** by Mode: `stop-receiving`, `po-receiving`, `loading-session`, `scan-session` (Delivery), `bin-check-in`, `bin-check-out` (Scan sessions in [pathway-screens.md](pathway-screens.md)).
- **Outcomes:** `This step is not fully configured for this account` / `Missing account setting: <name>`; a failed end-of-session keeps the screen open with its message.

### Scheduling (`scheduling`)

Screen, always. Schedules a Job's or Service's items onto an appointment.

- **Does:** lists the items Available and Scheduled for the appointment; each schedule, quantity change or undo is saved at once. Continue finishes the session and runs the account's end-of-session processing on it. Passes the session on.
- **Settings:** **Record Step** (the appointment); **Post Processing Custom Code** (the end-of-session processing).
- **Headless runner:** skipped: nothing is scheduled.
- **In `pathway-run`:** the `scheduling` screen (Scheduling in [pathway-screens.md](pathway-screens.md)).
- **Outcomes:** `This step is not fully configured for this account`; `Could not schedule item`; `Unable to complete the session`.

### Create Barcode (`create_barcode`)

Screen, always. Builds tracking labels for a shipment (`pathway-run map` calls it Create Tracking Labels).

- **Does:** the user picks or names a stop, then adds labels from orders and lines; each label creates its tracking item at once (in Import mode, an existing barcode is scanned instead). Removing a label archives its tracking item. "Download and Continue" (Submit in Import mode) completes the shipment and merges the labels into one file, and with **Close Shipment Automatically** (on the screen) also closes it. Passes the stop and the label file on.
- **Settings:** **Shipment Record Step** (optional); **Mode** (Create or Import); the label document templates and file types (Create); **Camera scan enabled** and **Peripheral scan enabled** (Import).
- **Headless runner:** skipped: no labels are made.
- **In `pathway-run`:** the `create-tracking-labels` screen (Create Tracking Labels in [pathway-screens.md](pathway-screens.md)).
- **Outcomes:** `Tracking items are not configured for this account (TrackingItemBO)`; `No existing stop selected — choose a stop or switch to a new one`; a failed automatic close shows `Close Shipment failed` and the step still continues.

### Quote Builder (`quote_builder`)

Screen, always. Builds a whole quote on one screen.

- **Does:** in Create mode, opening the screen creates the Quote on the parent record (Job, Service, Project or Commercial Project) and takes the next quote number then (a Final Contract with a **Quote Step** copies that step's quote: its fields, links, lines and manufacturer quotes); in Edit mode it works on an existing Quote. The **Starting** pathway runs once as the quote opens. Each link, line and text change is saved as it is made; every line needs a vendor, description and price. Before the document is built the **Post Build** pathway runs, then the **Post Edit** pathway (with the Edit phase on); a failed one stops there. With a Product / Labor Relationship set, each line is linked to the Quote on the one its vendor calls for (the `VendorBO` vendor's `VendorLaborField`) when its vendor is set or changed, and when the quote opens. Up to three summary pages: Overall (with Overall Summary), Product and Labor (each only its own lines; with its relationship set it shows once a line is linked on it). As Finalize opens (after those pathways) each summary page that has a file type is saved on the Quote as its own PDF ("<merged PDF name> Overall Summary.pdf" / "… Product Summary.pdf" / "… Labor Summary.pdf") under its type, kept while the page is unchanged and replaced when it changes; then the terms page is readied as a file on the Quote: its newest file of the **Terms File Type**, or, when it has none, the terms template made into a PDF and saved on the Quote under that type; that file is the terms page from then on (never remade). Finalizing builds the quote document (Overall Summary, Product Summary and the product lines' manufacturer quotes, Labor Summary and the labor lines' ones, the terms page for the quote type — none for a Quick Quote — then the additional files, each uploaded with a File Type the user picks) and saves it on the Quote, sets its Quote Type, links the parent's most recent quote, and records the **Completed Step** workflow step on the Quote. Passes the finished Quote on.
- **Settings:** **Mode**: Mode (Create or Edit), **Type** (Quick Quote, Full Quote or Final Contract), the record step (**Parent Record** in Create mode, **Quote Record** in Edit mode; no separate Record section), then the **Quote Step** under it (Final Contract in Create mode); **Product or Labor**: the choice (Product Only, Labor Only or Product and Labor), **Overall Summary**, **Overall Summary Template** (with Overall Summary), **Product Summary Template** and the optional **Product Relationship** (when the choice includes product), **Labor Summary Template** and the optional **Labor Relationship** (when it includes labor); **Terms**: **Full Quote Terms Template**, **Final Contract Terms Template**, **Terms File Type** (required for a Full Quote or Final Contract; the whole section is inactive for a Quick Quote); **File Types**: **Overall Summary File Type** (required with Overall Summary), **Product Summary File Type** (required when the choice includes product), **Labor Summary File Type** (required when it includes labor), the two manufacturer quote file types, **Additional Quote File Type** (optional: the default File Type of the Finalize additional-file upload), **Merged PDF File Type** (required); **Taxes**: **Tax Rate Source** (with **Address Step**), **Tax Rate Lookup Action** (an Address action, run in the background on a tax address without an Integration Tax Rate as the quote opens and when a new Ship-To is linked; the rate shimmers meanwhile), **Override Enabled**, **Override Field** (a Quote field; only with Override Enabled: the Build phase's pencil next to Total Tax edits the override tax rate saved there); **Options**: **Include Edit Document Phase**, **Show Inside Support Person**, **Completed Step**; **Table Columns** (up to three extra line columns); **Pathways** (Starting, Post Build, Post Edit). The step's title is always "Build Quote".
- **Headless runner:** skipped: no Quote is created. A step whose Record Step has no record fails the run.
- **In `pathway-run`:** the `quote-builder` screen (Quote Builder in [pathway-screens.md](pathway-screens.md)).
- **Outcomes:** `The step needs a merged PDF file type and a Completed step`; `The step needs a Full Quote Terms Template` (or Final Contract); `Post Build pathway <id> failed at "<step>": <reason>. Nothing was finalized; submit again to retry.`; `Unable to finalize the quote`.

## At the end of a run

### Display Record (`display_record`)

Automatic. Opens a record in the web app when the run finishes.

- **Does:** when the run finishes (not when it fails or is cancelled), the web app opens the record, on the chosen tab. When several ran, the last one counts; with a Display Folder too, the folder opens first, then the record. It changes nothing and passes nothing on.
- **Settings:** **Record Step**; **Starting Tab** (a standard tab such as Summary, Files, Messages, Email or History, or a relationship's tab).
- **Headless runner:** no effect (a missing record still fails the run).
- **In `pathway-run`:** a finished run prints `→ the web app would now open record <id> (<tab> tab)`.

### Display Folder (`display_folder`)

Automatic. Opens a folder in the web app when the run finishes.

- **Does:** like Display Record, for a folder; the last one counts.
- **Settings:** **Business Object**; **Folder**.
- **Headless runner:** no effect.
- **In `pathway-run`:** a finished run prints `→ the web app would now open folder <id>`.

## Not offered in the Designer

These types exist in PATH but the Designer can't add them, and PATH can't run them: a run that reaches one fails with `Implementation provider not configured for step type: "<type>"`, in the web app, in `pathway-run` and on the headless runner alike. `pathway-run map` marks them as steps PATH can't run.

### Delete Record (`_delete_record`)

Not offered in the Designer; a run that reaches it fails. Archive Record is the step that takes a record out of use.

### Download Document (`download_document`)

Not offered in the Designer; a run that reaches it fails. Create Document From Template and Edit Document From Template make documents.

### Fetch Record (`fetch_record`)

Not offered in the Designer; a run that reaches it fails. Other steps re-read records on their own where they need fresh values.
