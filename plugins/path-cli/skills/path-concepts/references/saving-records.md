# What Happens When a Record Is Saved

When someone saves a record, or a pathway does it for them, PATH does more than store the typed values. This page describes what the user can observe, so you can explain why other things changed.

## Calculated fields update

Calculated fields recompute on save: number and text expressions, time spans and counts, plus totals from linked records. If a user changes a quantity or price, the line total and the quote total change without being typed. If Quote Total depends on other calculated fields, those update in the right order, and the result is what the user sees after saving. These fields can't be typed into.

Only fields affected by the change need to recalculate. Saving without changing anything doesn't set off much.

## Milestones are re-checked

After a change, PATH checks the record's milestones again. As a result:

- The record may **enter** a new milestone (for example a Quote whose criteria for "Signed" are now met).
- It may **leave** a milestone it no longer qualifies for.
- Folders and reports that use those milestones then show the record differently.
- **Tasks and actions** that are tied to milestones may follow: a task can be created or completed, or a notification, email or text can go out.

If a user asks "why did my Job move?", the answer is usually that a field or step changed, the milestone was re-checked, and the record now meets (or no longer meets) its criteria.

## Linked records update

A change can ripple to other records:

- When a Quote's total changes, the **Job's Quote Total** (and anything built on it) updates.
- Each linked record that changes can also re-check its own milestones, so the Job may move to a new milestone because of what happened on the Quote.
- The ripple can pass through several levels of links, for example Quote to Job to Customer.
- This work may finish a moment **after** the save. If a total on another record looks stale right after saving, wait a moment and look again.

## Linking and unlinking

Linking or unlinking records updates **both sides**:

- The record that holds the link recalculates anything that uses the linked record, such as borrowed fields (the Job's Customer Name shown on a Quote).
- The record that is linked to recalculates its totals and counts: a newly linked Quote is added to the Job's quote total; an unlinked or relinked Quote is removed.
- If a Quote is moved from one Job to another, both Jobs' totals update.

## Archiving

Archiving a record takes it out of totals and lists:

- It disappears from folders, search and reports unless these include archived records.
- Totals and counts on the records it was linked to drop its contribution.
- Unarchiving brings it back into lists and adds it back to totals.

## Custom Logic

An account may have its own Custom Logic written for a business object. On save, Custom Logic can set fields on the record, for example filling in a default or flagging a status. Calculated fields that depend on what Custom Logic set are updated afterward too, so the saved record is consistent. Custom Logic is written by the account's designers. If a field changes that the user didn't edit, Custom Logic is one possible explanation.

## Pathways and saves

Pathway steps that edit a record save it the same way, and later steps see the updated, recalculated values. That is why a pathway screen can show a freshly computed total right after an earlier step changed a price.

## Tips for explaining results

- **A computed field didn't take my typed value**: it is calculated, so change its inputs instead.
- **A total is wrong or stale just after a change**: totals on other records can lag a moment, so recheck.
- **A record vanished from a folder after saving**: the change moved it out of the folder's criteria or milestone.
- **A task or notification appeared**: a milestone change triggered it.
- **Nothing happened on a no-change save**: PATH skips the rechecks when no value changed.
