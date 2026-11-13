# Roles and permissions

What a PATH user may see and do comes from the **roles** they hold in the account, their **tags**, and what each folder, report and calendar is shared with. This page explains each permission in the web app's words. Why a particular record or action is out of reach, and how the commands report it: the [`path-cli` guide](../commands.md)'s [access-and-visibility.md](../cli/access-and-visibility.md).

## Roles

- A **role** is a named set of permissions ("Sales", "Installer", "Office Admin"). A user can hold several; their permissions add up.
- Every member also has **All Users**, the role that stands for everyone in the account. Sharing something with All Users shares it with every member.
- Roles, their permissions and who holds them are set by an Admin or a User Admin in the Designer.

## Account-wide permissions

| Permission | What it allows |
|---|---|
| **Admin** | Everything in the user app: every business object, folder, report and calendar; archiving and deleting; acting as another member (Impersonate). Admin alone doesn't open the Designer. |
| **Designer** | Opening PATH's Designer to change how the account is set up (business objects, fields, workflow, pathways, actions, templates, settings). Also allows super-user pathway runs, which start a pathway on a record its visibility criteria would hide it on. |
| **User Admin** | Managing users, roles and tags (the Admin role has it too). Also opens any activity session's history. |
| **Folder admin / Report admin** | Edit and delete every folder / every report, whoever owns it. |
| **Calendar Admin** | Creating calendars and calendar groups and managing every one of them (rename, recolor, share, delete), as an Admin can. |
| **Delete Records** | Archiving records on any business object, even without the Archive permission on it. |
| **Delete Files** | Deleting files from records (archiving a file needs no special permission). |

## Business object permissions

Each role sets, per business object:

| Permission | What it allows |
|---|---|
| **View** | Seeing the business object at all: its records, folders, reports, tasks and Feed items. Without it, the business object doesn't exist for the user. A business object with no setting for a role is viewable. |
| **Edit** | Creating records and changing their fields and links, adding notes. Pathway runs are exempt: a pathway the user may start does its own edits. |
| **Archive** | Archiving and unarchiving records. |
| **Create Folders** | Creating folders on the business object. |
| **Create Reports** | Creating reports on it; the creator owns the report. |

Whether a business object appears in the web app's menu (and in quick search) is a separate setting; a viewable business object can be left out of the menu and still be reached through linked records and folders.

## Fields, field groups and relationships

Designers can limit a field, a field group or a relationship to certain users or roles, or show it only when the record meets a rule (for example only once a Job is won). Two users can therefore see different fields on the same record, and a user can't read or change what the record viewer doesn't show them. See `fields.md`.

## Tags

Tags limit which records each user sees, everywhere in PATH at once: a record is visible only to users who hold its tags (for example a Location of Austin). See `folders-and-tags.md`.

## Folders, reports and calendars

| Thing | Access |
|---|---|
| **Folder** | View (it is in the user's menu), Edit (change its filter, columns, stickers and context) and Delete, given to roles or users. Folder admins have all. |
| **Report** | The **owner** (its creator) has everything and shares it with roles or users as View, Edit or Delete. Report admins have all. |
| **Calendar** | Each calendar has an owner and an **All Users** level (view, edit or none) for everyone it isn't shared with otherwise; roles and users can be given View or Edit. The owner, Admins and Calendar Admins manage it. |
| **Calendar group** | A named set of calendars with its own owner and sharing; sharing a group never changes access to the calendars in it. |

## Tasks

A task's own settings decide who may act on it: **Only Assignee Can Finish** (only the assignee, its creator or an Admin completes it), **Can Be Skipped** (it can be cancelled), **Can Be Assigned By Other Users** (anyone may change the assignee, not only its creator or an Admin) and **Edit Permission Required To Finish** (users who can't edit the record can't complete or cancel it).

## Impersonate

An Admin can act as another member (the web app's Impersonate) to see exactly what they see. Changes made while impersonating are recorded as that member's.

## Child accounts

A child account shares its parent account's design (business objects, fields, workflow, pathways) and has its own members and records. Folders and reports the parent shares appear in the child read-only: they are changed in the parent account, or copied into the child with **Duplicate to my account**. Calendars belong to the child.
