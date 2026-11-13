# Access and visibility

Every PATH CLI command runs as the **acting user** (the signed-in user, or the member named by `--as-user`) and shows and does only what that user could in the web app. This page covers why a record, field, folder, report or pathway isn't listed, why an action is refused, and how to explain it to the user. The roles and permissions themselves, in the web app's words, are in the [`path-concepts` guide](../concepts.md)'s [permissions.md](../concepts/permissions.md).

## How a refusal reads

| Output | Meaning |
|---|---|
| `<user> can't <action> — <why>, as in the web app.` | The web app wouldn't let this user do it. `<why>` names the rule: `they don't have Edit permission on Jobs (business object 191)`, `only the note's author can edit or delete it`, `it hasn't been shared with them`, `none of their roles has the Delete Files permission`, `that is Designer work and none of their roles has the Designer permission`. |
| `<user email> can't start "<pathway>" from <point>` and the launch checks | A pathway's launch rules failed (role, where it's shown, the starting business object, record access, visibility criteria). `pathway-run map --pathway-name "<name>" --record-id <id>` gives them for one pathway without running it. |
| `ERROR [404]: Not Found` on a record id | No record with that id in this account for this user: a wrong id, a record number given as an id, another account's record, or a record the user's tags don't include. |
| `Business object "X" not found — did you mean "…"?` | No business object of that name that the user can view. The close names offered are ones they can. |
| `You're deactivated in account <id> (<name>), so you can't use it.` / `Account <id> isn't one of your accounts.` | The signed-in user isn't an active member of that account; `list-accounts` lists the ones they are. |

A refusal is the answer: say in plain words what the user can't do and why ("You don't have permission to archive Jobs; an admin can change your role"). There is no switch that lifts it, and the same request through other commands is refused the same way.

## Why isn't it there?

Work through these in order; the first one that applies is usually the answer.

1. **Another account.** Ids belong to one account, and the command ran in the saved account unless `--account` said otherwise. `get-account` names the account a command runs in.
2. **Archived.** Archived records are left out of `search`, `find-records`, folders and reports; `--include-archived` brings them back (`search`, `find-records`, `run-report`), and `get-record` opens an archived record by id (`[archived]` in its header).
3. **Tags.** Records carry tags (a location, a department) and each user holds tags; a user sees only records whose tags they hold, everywhere in PATH at once (search, folders, reports, related lists, tasks, the Feed). A record outside their tags can't be opened by them at all. Which tags a member holds is on the web's Users page (Admins and User Admins; `list-users` shows names and roles only); the record's tags show on its Tag fields in `get-record` for users who can see it. Only an admin of the account's users changes someone's tags.
4. **The business object.** A user without View on a business object sees none of its records, fields, folders, reports or tasks, and it isn't in `list-business-objects` for them. Quick search (`search`) covers only business objects that are viewable **and** shown in the web app's menu.
5. **Not in the folder or report.** A folder or report lists only records that meet its filter; `get-filter folder <id>` / `get-filter report <id>` prints it, and the [`path-concepts` guide](../concepts.md)'s [folders-and-tags.md](../concepts/folders-and-tags.md) explains the rest. A folder the user has no View permission on isn't in their menu (`list-folders` notes it); a report not shared with them can't be run.
6. **Not on the record for this user.** `get-record`, `list-related-records`, `list-record-notes` and `find-records` show only what the record viewer shows this user: designers can limit fields, field groups and relationships to some users or roles, or show them only when the record meets a rule, and a relationship to a business object they can't view isn't shown. For this user such a field or relationship isn't on the record: the answer is what the record viewer shows them, without guessing what else the record may hold.
7. **Not a pathway they can start here.** `pathway-run available --record-id <id>` lists what is offered (`--from dashboard|folder|task` for the other launch points); `pathway-run map --pathway-name "<name>" --record-id <id>` says which launch checks fail for the one they asked about.
8. **Just created by someone else.** Design changes (a new field, folder or pathway made in the web app during this session) show after `clear-cache`, or with `--no-cache` on one command. Records are always read live.

## What each action needs

| Area | Rule |
|---|---|
| Business objects | Only business objects with View exist for the user: listings, data model reads, records, related records, tasks, Feed items. |
| Search | Quick search covers business objects that are viewable and shown in the menu; `search --bo-id` on another business object is refused. `find-records` searches one business object's records on the fields the user sees. |
| Reading a record | What the record viewer shows: fields, field groups and relationships the designer limited to other users, roles or record rules are left out. Created, Modified and Last Accessed dates always show. |
| Folders | A folder's rows show its columns as the web folder does: a field the record viewer itself hides still shows there; a field limited to other users or roles, or by its visibility rule on that record, stays blank. |
| Creating and editing records | **Edit** on the business object, and every field written must be one the record viewer shows the user (on a new record, judged against the values being set). Linking and unlinking: Edit, and the relationship shown in the record viewer. |
| Archiving records | **Archive** on the business object, or the Delete Records permission. |
| Files | Reading, downloading and uploading: the record's business object viewable. Deleting a file: Admin or the Delete Files permission; archiving a file needs neither. |
| Notes fields | Only Notes fields the record viewer shows; adding a note needs Edit on the business object; editing or deleting a note: its author only. |
| Messages | Posting: the record viewable. Editing or deleting a message: its author only. |
| Emails | `send-record-email` sends from the acting user's own name and PATH address; the record's business object must be viewable. |
| Tasks | Completing: anyone who can see it, unless it is set so only the assignee can finish (then the assignee, its creator or an Admin), or needs Edit permission on the record to finish. Cancelling, reassigning and editing depend on the task's own settings and on being its creator or an Admin. A task with a pathway is completed by running the pathway. |
| History | A record's history: the record must open for the user. An activity session (`get-history-session`): Admin, Designer or User Admin, or the session's record must open for them. |
| Folders and reports | Create a folder: Create Folders on its business object. Create a report: Create Reports on it (the creator owns the report). Change a folder's or report's filter, columns or context: Edit on that folder or report; folder and report admins have all. |
| Calendars | Create, change or delete an event: Edit on its calendar (a grant, ownership, or the calendar's All Users level; Admins and Calendar Admins have all), and on a calendar it moves to. A record's appointment also needs Edit on the record's business object and the field shown to them. Read-only Google / Office 365 events can't be changed. |
| Calendar management | Creating calendars and calendar groups: Admin or Calendar Admin. Renaming, recoloring, sharing or deleting one: its owner, or an Admin / Calendar Admin. |
| Pathways | A run follows the pathway's launch rules (role, where it's shown, starting business object, record access, visibility criteria). Once running, its steps aren't held to the user's create, edit and archive permissions or the record viewer's field visibility, as in the web app. |
| Feed, profile, notification settings | Always the acting user's own. |
| Designer work | Business objects, fields, workflow, pathways, actions, templates, settings, users, roles and tags are set up in PATH's Designer by Admins and Designers; these commands only read the setup. |

## Acting as another user (`--as-user`)

- `--as-user <id|email|full name>` is the web app's Impersonate: the command runs with that member's access, so it shows what they see and refuses what they can't do. It is the way to answer "why can't Pat see Job 26-0074?": run `get-record` or `pathway-run available` as Pat.
- Only account **Admins** can use it; never on themselves (`--as-user can't be yourself …`) and never on a deactivated member (`<name> is deactivated; the web doesn't impersonate deactivated users.`). Anyone else gets `<user> can't use --as-user: acting as another user is the web's Impersonate, which only account Admins have.`
- What the command changes is done as that member: their name shows in the record's history.

## Super-user pathway runs

`pathway-run start --mode super-user` (and `execute-pathway --mode super-user`) starts a run even when the pathway's visibility criteria don't match the record. It needs a role with the Designer permission (`<user> can't use super-user mode: it needs a role with Designer permission …`). Every other launch rule still applies, and there is no other mode: the web app's rules always apply.

## Deactivated members

- A deactivated member keeps their records, tasks and history. `list-users` and the session context's user list leave them out and say how many. They can't be impersonated and can't use the account.
- Tasks still assigned to them stay open until someone reassigns them (`assign-task`).

## Related

- [errors.md](errors.md): every reason a refusal can give, and the other error messages.
- [ids-names-paging-limits.md](ids-names-paging-limits.md): ids, names, accounts and paging.
- The [`path-concepts` guide](../concepts.md): [permissions.md](../concepts/permissions.md) (roles and permissions), [folders-and-tags.md](../concepts/folders-and-tags.md) (tags), [fields.md](../concepts/fields.md) (field visibility).
- `pathway-run available`, `get-filter`, `list-users`.
