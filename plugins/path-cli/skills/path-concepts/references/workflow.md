# Workflow: Milestones, Workflow Steps and Tasks

Workflow is how a record such as a Job, Quote or Service moves through its lifecycle, through three parts: milestones (the stages the record is in), workflow steps (events recorded on the record) and tasks (assignments people need to finish). Designers set up the workflow for each business object in PATH Designer. End users experience it as the milestones they see on a record, the tasks in their task list, and the pathways offered at each stage.

## How the pieces fit together

Part of the standard PATH WD Job workflow:

- **Lead:** where a new Job starts. Tasks such as "Need to Qualify Lead" belong to this stage.
- **Full Quote Out:** entered when the "Full Quote Sent to Customer" workflow step has happened, as long as none of "Job Won", "Job Lost", "Put On Hold" or "Cancelled" has. Tasks such as "Follow Up On Quote" belong to this stage.
- **Deposit Needed** and **Deposit Collected:** decided by the "Deposit Needed" and "Deposit Received" steps and the Job's Deposit Collected field. Tasks such as "Collect Required Deposit" belong here.

Workflow steps such as "Full Quote Sent to Customer", "Job Won", "Deposit Received" and "Delivery Scheduled" are the events that move the Job between milestones. The full list of standard Job milestones is in [path-wd.md](path-wd.md). An account may have adjusted its own, so check the record's real milestones before explaining them to a user.

Workflow and pathways are different things. A pathway is a guided process the user runs (see [pathways.md](pathways.md)). Workflow is the set of rules that decides which stage the record is in. The two connect in three ways:

- pathways often run workflow steps;
- milestones control which pathways are offered;
- tasks can carry pathways.

## Milestones

A milestone is a stage in a record's lifecycle, such as Lead, Full Quote Out, Ordering or Paid In Full. A user sees a record's milestones in the Record Viewer, and folders and reports often filter on them.

### Milestones are computed, not set by hand

Nobody picks a milestone from a list. Each milestone has criteria, and PATH places the record in every milestone whose criteria it meets. The criteria can check:

- **Workflow steps:** whether a step such as "Full Quote Sent to Customer" has happened on this record.
- **Field values:** for example, whether the Job's Deposit Collected box is checked.

Criteria can include conditions and exclude conditions. **Exclude conditions always win.** A Job with "Full Quote Sent to Customer" is in Full Quote Out, unless it also has "Job Won", "Job Lost", "Put On Hold" or "Cancelled", which Full Quote Out excludes.

**The default milestone** is where a record sits when it meets no other milestone's criteria. New records usually start there, for example a new Job in Lead.

To get a record into a milestone, change what the criteria check: run the workflow step (usually through a pathway) or fill in the field. When a user asks "why is Job 1234 still in Full Quote Out?", the answer is usually that "Job Won" (or "Job Lost", "Put On Hold" or "Cancelled") hasn't been recorded on it yet. The `path-milestone-explain` skill checks the criteria one condition at a time. Typing a milestone name into a field won't move it.

### Multiple milestones at once

A record can be in several milestones at the same time when it meets the criteria for each. Entering a new milestone doesn't, by itself, take the record out of earlier ones. A record leaves a milestone only when that milestone's own criteria stop being met, for example when an exclude condition becomes true or a workflow step is deactivated.

### When milestones are re-checked

- When a workflow step runs, is deactivated or is reactivated on the record.
- When field values on the record change and are saved.

If a designer changes a milestone's criteria later, existing records aren't re-checked automatically. An admin can use **Refresh Workflow** on a record to re-check it against the current setup. This is the fix when a record isn't showing the milestone it should.

### What entering a milestone does

When a record enters a milestone, several things can follow:

- **Tasks are created** from the milestone's automatic task list and assigned to people.
- **Pathways become available** whose visibility criteria depend on that milestone. Pathways for the previous stage may disappear. For example, "Select Winning Quote" is offered while the Job is in Full Quote Out and goes away once the Job leaves it.
- **A pathway may run on its own.** A milestone can run a pathway on entry, sometimes only the first time the record enters it.
- **An action may fire,** such as a notification, email, text or new task (see [actions.md](actions.md)).
- **Folders change.** The record appears in or drops out of folders that filter on the milestone.

Leaving a milestone can complete or cancel tasks whose criteria depend on it.

## Workflow steps

A workflow step is an event recorded on a record: something that happened, performed by a particular user at a particular date and time. Examples on a Job are "Lead Qualified", "Full Quote Sent to Customer", "Job Won", "Deposit Received", "Delivery Scheduled" and "Delivery Completed". The record keeps a history of its workflow steps with the date each one was entered.

### What "running a workflow step" means

Running a workflow step records that the event happened on this Job, now, by this user. PATH then re-checks the Job's milestones, which can move the Job to a new stage, create or close tasks, change which pathways are offered, and fire actions. Running "Job Won" on Job 1234, for example, takes it out of Full Quote Out and on toward the deposit and ordering stages.

Workflow steps get run in three ways:

- **Initial step:** some business objects run a step automatically when a record is created.
- **From a pathway:** the most common way. A pathway's Run Workflow Step does it as part of a guided process.
- **Manually by an admin,** from the record or through Refresh Workflow.

A step that has run can later be deactivated, reactivated or removed from the record's history. Deactivating a step (sometimes done by a pathway) re-checks milestones, so the record can move back out of a stage.

Because running a workflow step changes the record's stage and can set off tasks, emails and other automation, it shouldn't be done casually. Run it through the pathway designed for it.

## Tasks

A task is an assignment on a specific record for a specific person, for example "Schedule Installation" on Job 1234, assigned to the user, due Friday. Tasks appear in the assignee's task list on the dashboard and on the record.

### Where tasks come from

- **Automatically, on entering a milestone.** The designer lists tasks to create when a record enters a milestone. Nobody has to create them.
- **Chosen from a milestone's available tasks.** While a record is in a milestone, users can add tasks from a list of ready-made ones, often through a pathway's Create Task screen.
- **Created manually** by a user, with their own title.

There can be a short delay between a record entering a milestone and its automatic tasks appearing.

### Assigned and due

A task is assigned to a specific user, or to whoever holds a particular role on the record (for example the Job's salesperson). Its due date can be a fixed date, a set time after the task was created, or a date taken from a field on the record. The assignee can be notified through the feed, email, text, mobile push or a pop-up. Some tasks also notify their creator when they are done.

### Completed and cancelled

A task is pending, completed or cancelled.

- **Completed by a person:** the assignee marks it done. Some tasks allow only the assignee, the creator or an admin to finish them.
- **Completed automatically:** a task can have completion criteria, set up the same way as milestone criteria (workflow steps and field values). When the record meets them, the task completes itself. For example, "Collect Required Deposit" can complete once the deposit is recorded.
- **Cancelled automatically:** a task can have cancel criteria. For example, a delivery task may cancel itself if the Job is cancelled.
- **Skipped:** some tasks are marked as skippable, and the user can cancel them.

### Tasks that carry a pathway

Many tasks have a pathway attached. Running the task runs that pathway, and the pathway usually makes the task's completion criteria true (or completes the task directly), so finishing the pathway finishes the task. The usual way to handle a task like "Schedule Installation" is to run its pathway.
