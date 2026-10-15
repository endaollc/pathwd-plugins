# Folders and Tags

Folders are saved, filtered lists of records for one business object. Tags are a per-user filter that hides records everywhere, so two people can open the same folder and see different records. This page explains both and why a record is or isn't showing.

## Folders

A folder is a saved list of records that meet certain criteria. Folders are organized first by business object (Jobs, Services, Customers, Invoices, Quotes and so on), and under each business object a user sees the folders they have permission to view.

Typical examples:

- **Jobs**: Leads, Deposit Needed, Awaiting Receiving, Fulfillment, My Jobs In Full Quote Out
- **Services**: New, Inspection Scheduled, Awaiting Receiving
- **Invoices**: Not Yet Sent, Sent to Customer, Paid In Full
- **Quotes**: Ready to Send, Sent, Signed

Folders save people from searching long lists. They keep work organized and let people focus on records at the stage of the workflow they care about.

### How a folder decides what's in it

Membership is automatic. A record is in a folder when it meets the folder's criteria, and it moves in and out as the record changes. Nobody drags records in or out. Criteria can combine:

- **Milestones**: for example Lead, Full Quote Out or Ordering. Being in a milestone is itself decided by that milestone's criteria.
- **Workflow steps**: for example Needs Approval, Awaiting Payment, Installation Scheduled.
- **Field values**: for example Department is Residential, or Balance is more than 0.
- **Relationships**: for example the record has a Customer linked.
- **Archived status**: archived records are left out unless the folder is set to include them.

Several conditions are usually combined with "and" or "or", and designers can group them.

Designers create and edit the folders. Permissions control who can see a folder, who can change it and who can remove it. A new folder is visible to all users by default, and a designer can change that.

### Why a record is in a folder, or isn't

If a user asks "why isn't Job 1234 in the Deposit Needed folder?", walk through these in order:

1. **Is the record archived?** Archived records are hidden unless the folder includes them.
2. **Does it meet the criteria?** Check the field values, the milestones it is in, the workflow steps it has completed and any links the folder requires.
3. **Is it in the milestone the folder uses?** Milestones come from their own criteria, so a record missing a milestone is missing something that milestone requires.
4. **Is a tag hiding it?** A record tagged for another location is invisible to a user who doesn't hold that tag (see below).
5. **Is the user allowed to see this folder?** If not, the folder doesn't appear for them at all.
6. **Did a recent change not apply yet?** Changes can take a moment to settle, especially for totals from linked records.

The reverse works the same way: a record is in a folder because it currently meets the criteria, and it will drop out when a change means it no longer does.

### Columns, sorting and stickers

- **Columns**: each folder shows chosen fields as columns. A designer decides the columns. In folders using the default layout, columns are the fields flagged to show as columns, following the order of field groups. Fields from linked records can be columns too (for example the Job's customer name in a Quotes folder).
- **Sorting**: users can sort a folder ascending or descending by a column.
- **Stickers**: small visual indicators shown on rows when certain conditions are true, such as an overdue flag. Stickers can be specific to a folder.

### Finding and using folders

- The left-side panel of the dashboard lists folders.
- The navigation menu gives access to them from the top of the screen.
- **Bookmarked folders** appear on the dashboard for quick access. The star icon bookmarks a folder; the red Bookmarked button removes it. **Set Order** lets the user drag folders into their preferred order.

## Tags

Tags are a way to filter data for each user across all of PATH. They are most often used for something like Location or Department, so that people only see the records for their own location.

### How tags work

- A **tag group** is a category, such as Location.
- A **tag** is a value in that group, such as Austin or Houston.
- Each user is assigned one or more tags in each group.
- Each record carries a tag for each group. When a record is created, it takes the tags of the user who created it.

Users only see records whose tags match the tags they have been given. A user tagged Houston sees Houston records and not Austin ones, and the other way round. A user with both Austin and Houston tags sees records from both.

### Where tags apply

Tag filtering applies everywhere users view data:

- folders
- reports
- tables of related records
- recents
- bookmarks

A record hidden by tags is hidden in all of these at once. It is not just missing from one list.

### Why two users see different records

If two people open the same folder or report and see different results, the likeliest cause is tags. Their location tags differ, so the folder shows each only their own locations' records. Other possible causes are folder permissions (one person can't see the folder) and role-based limits on fields.

When a user says "I can't find Job 1234 but my coworker can", suggest checking tags: the Job may be tagged for a location the user isn't assigned to. A user can't get around a tag filter themselves. A tag change is made by whoever administers the account's users.

A user can see tag values as a text field on their own People record, which shows the tags they currently hold.

## Summary

- A folder is a saved filter on one business object; membership follows the criteria automatically.
- Tags then narrow what each user may see, whatever the folder says.
- A record shows up for you only if it meets the folder's criteria, isn't archived (unless included), and carries a tag you hold.
