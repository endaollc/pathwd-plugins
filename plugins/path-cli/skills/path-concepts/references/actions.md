# Actions

An action is a single automated job that PATH carries out for a record without anyone filling in a screen, such as sending a notification, emailing a customer or creating a task. Designers set up actions for each business object in PATH Designer, and the system fires them when something triggers them. End users don't run actions directly; they see the results.

## What actions do

Each action does one thing:

- **Send a notification** to specific users, to everyone in a role, or to a person linked to the record (for example the Job's salesperson). Notifications can arrive in the in-app feed, by email, by text, as a mobile push or as a pop-up.
- **Send an email** from a template filled with the record's values. Addresses can come from a field on the record (such as the customer's email), from specific users, or be fixed.
- **Send a text message (SMS)** the same way, to phone numbers from the record, from users or fixed numbers.
- **Create a task** on the record from one of the business object's task templates.
- **Run an outbound integration,** sending information from the record to another system the business uses.
- **Run a pathway in the background** on the record, with no screens (a "scheduled pathway").

## When actions fire

An action never fires just because someone edited a field. It needs one of these triggers:

1. **A pathway step.** A pathway's Execute Action step fires the action on the record the pathway is working on. Some pathways wait for the action to finish before continuing; others start it and move on, so its effect may show up a moment later.
2. **Entering a milestone.** A milestone can fire an action when a record enters it. For example, when a Job enters Deposit Bypass Requested, the people who approve bypasses might be notified.
3. **A schedule.** An action can be set to fire:
   - at a set date;
   - after a delay from when it was triggered;
   - relative to a date field on the record (on that date, or a number of days or hours before or after it, such as a follow-up three days after a quote was sent).

   Some scheduled actions repeat, for example a weekly status email. They stop at an end date, at a date taken from a field, or when the record enters certain milestones (such as when the Job closes), or they repeat indefinitely.

An action that a designer has turned off never fires, whatever the trigger.

## What users notice

Users see an action's results, not the action itself:

- a notification in their feed, an email, a text, a push notification or a pop-up;
- an email or text that reached a customer, which is often logged on the record;
- a new task in someone's task list;
- changes made by a background pathway, such as fields updated or a workflow step run, sometimes overnight or at a scheduled time;
- for integrations, often nothing visible in PATH; the data shows up in the other system.

Some notifications are marked required on certain channels. They arrive on that channel even if the user has turned that channel off in their own notification settings.

When a user asks "why did the customer get this email?" or "where did this task come from?", the answer is often an action that fired because the record entered a milestone, a pathway step ran, or a scheduled time arrived.

Scheduled actions keep their schedule on their own. Once a scheduled or repeating action is queued for a record, it fires at its time even if nobody is working on the record, until its stop condition is met.

## How actions differ from pathways and workflow steps

- **Pathway:** an ordered series of steps, many with screens, that guides a user through a process such as "Select Winning Quote". A pathway can fire actions along the way. See [pathways.md](pathways.md).
- **Workflow step:** an event recorded on the record, such as "Job Won". It moves the record between milestones but doesn't send or create anything by itself. See [workflow.md](workflow.md).
- **Action:** one configured automation, such as "send this email" or "create this task", fired by a pathway step, a milestone entry or a schedule. It has no screens and doesn't move the record between milestones by itself. A background pathway it starts can, though.

A common chain is: a user runs a pathway, the pathway runs a workflow step, the record enters a milestone, and that milestone fires an action that emails the customer and creates a task.
