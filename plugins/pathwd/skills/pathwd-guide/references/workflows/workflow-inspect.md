# Workflow: workflow-inspect

> **Commands:** every command in this command is a PATH CLI command line: the command name and its flags. On PATH WD Remote MCP each tool runs the commands of one area: a command line such as `--account 1234 list-fields --bo-name Job` runs as `path_data_model(command: "list-fields", args: "--account 1234 --bo-name Job")`, and each command's page in [the command index](../commands.md) gives any command's syntax and the tool that runs it.

**Covers:** how the account is set up, read-only: business objects, fields, field groups, relationships, workflow milestones, steps and tasks, folders. Commands: `list-business-objects`, `get-business-object`, `list-fields`, `list-field-groups`, `list-relationships`, `list-workflow-milestones`, `list-workflow-steps`, `list-workflow-tasks`, `list-folders`. Each command's syntax and output: its command note.

The account and session: the [`path-session` guide](../session.md). Every command's syntax: the [`path-cli` guide](../commands.md).

## Designer-only reads: PATH's Designer

This command covers the read-only structure that the user app shows. Setup that only the Designer shows — stickers, actions, notification rules, account settings, document templates, pathway steps, integrations, and Designer history — is seen in **PATH's Designer** by an Admin or Designer (or read with the PATH Designer plugin, if it's installed). If the user asks about those, point them there instead of guessing from the API. Changing anything about the structure is Designer work too.

## What to ask the user

**Required:**
1. **What they want to explore** — a specific BO, a field list, milestones, relationships, etc.

**Optional:**
- **Which business object** — if they're asking about fields, milestones, or relationships, they need to specify which BO. If they don't, ask.

If the user hasn't specified an account ID, ask which account to use or run the [`path-session` guide](../session.md).

## Steps

### Listing Business Objects

```
--account <ID> list-business-objects
```

A table of `id | name | plural`, with each business object's designer description under it (`Business object context:`, once per session). Present each BO with its name and description.

### Getting Business Object Details

```
--account <ID> get-business-object --bo-name <name>
```

`key: value` lines: `id`, `name`, `plural`, `identifier field`, `in menu`, `messages`, `email`, and the whole `context`.

### Listing Fields

```
--account <ID> list-fields --bo-name <name>
```

The table's columns: `id`, `name`, `type`, `req` (`yes` when required), `from` (the relationship a linked field shows a value through), `options` (a Selection's options, cut with `…` when long); `Field context:` under it. Present fields in a table showing:
- Field name
- Type (text, number, selection, etc.)
- Required or optional

For selection fields, mention the available options if relevant to the user's question. One field in full (every option, format, default, group, whether it is searchable, its whole context): `get-field --field-id <id>`.

### Listing Field Groups

```
--account <ID> list-field-groups --bo-name <name>
```

A table of `id | name`. Present groups with their names. Field groups are logical sections in the record editor (e.g. "General Info", "Financial", "Contact Details").

### Listing Relationships

```
--account <ID> list-relationships --bo-name <name>
```

A table of `id | name | to` (`to`: the business object linked to, with its id); `Relationship context:` under it. Only the relationships this business object holds are listed: links pointing at it from elsewhere are on the other business object's list. Present relationships showing:
- Relationship name
- Target BO (what it connects to)

### Listing Workflow Milestones

```
--account <ID> list-workflow-milestones --bo-name <name>
```

Milestones are the major phases a record goes through (e.g. "Lead", "Quoted", "Won", "Complete"). The table lists them in the designer's order: `id`, `name`, `flags` (`initial`, `disabled`), `creates` / `offers` (workflow task ids), `runs` (a pathway or action run on entry); `Milestone context:` under it. A milestone's entry criteria: `get-filter milestone <id>`. Present them in order.

### Listing Workflow Steps

```
--account <ID> list-workflow-steps --bo-name <name>
```

Steps are individual actions or checkpoints within a workflow (e.g. "Schedule Delivery", "Collect Payment"). The table: `id`, `name`, `flags` (`initial`, `disabled`); `Step context:` under it. Present them in order.

### Listing Workflow Tasks

```
--account <ID> list-workflow-tasks --bo-name <name>
```

Tasks are actions assigned to users as records move through the workflow. The table: `id`, `title`, `pathway` (the pathway completing it runs), `assign` (`creator`, `user` or `relationship`), `rules` (`task-complete`, `task-cancel`, `task-filter`: print one with `get-filter <rule> <id> --bo <business object id>`), `flags`. Which milestones create or offer a task: the `creates` / `offers` columns of `list-workflow-milestones`. Present them with their names and any trigger conditions.

### Listing Folders

```
--account <ID> list-folders --bo-name <name>
```

Folders are saved filtered views of records. A table of `id | name` (the footer names the business object), then `Folder context:` and the folders' stickers. Present them with their names.

### Full BO Overview

If the user asks for a complete picture of a BO, run multiple commands and present a summary:

```
--account <ID> get-business-object --bo-name <name>
--account <ID> list-field-groups --bo-name <name>
--account <ID> list-fields --bo-name <name>
--account <ID> list-relationships --bo-name <name>
--account <ID> list-workflow-milestones --bo-name <name>
--account <ID> list-workflow-steps --bo-name <name>
--account <ID> list-workflow-tasks --bo-name <name>
--account <ID> list-folders --bo-name <name>
```

Present the overview in organized sections:
- **Business Object** — name, description
- **Field Groups** — listed by name
- **Fields** — name, type, and whether required (`get-field` names one field's group)
- **Relationships** — name and target BO
- **Workflow Milestones** — in order
- **Workflow Steps** — in order
- **Workflow Tasks** — names and triggers
- **Folders** — names

## CLI Commands Used

| Command | Purpose |
|---------|---------|
| `list-business-objects` | List all BOs in the account |
| `get-business-object --bo-name <n>` | Get detailed info on a single BO |
| `list-fields --bo-name <n>` | List all fields on a BO |
| `list-field-groups --bo-name <n>` | List field groups on a BO |
| `list-relationships --bo-name <n>` | List relationships on a BO |
| `list-workflow-milestones --bo-name <n>` | List workflow milestones |
| `list-workflow-steps --bo-name <n>` | List workflow steps |
| `list-workflow-tasks --bo-name <n>` | List workflow tasks |
| `list-folders --bo-name <n>` | List folders for a BO |
| `list-file-types --bo-name <n>` | List a BO's file types |

## Key facts and confirmations

- Every command here runs through the PATH WD Remote MCP tool for its area (each command's page in [the command index](../commands.md) names it).
- Results read best as tables, lists and short sections in the web app's words, not ids and raw values.
- Each question needs only its own command: milestones don't need fields and relationships too.
- Milestones, steps and relationships are explained in business terms ("the stages a Job goes through").
- The list tables carry ids, types and options; `get-field --field-id <id>` gives one field in full, and `--fields <columns>` narrows a table to the columns asked for (`list-fields --bo-name Job --fields id,name,options`).
- Natural next questions (a field group's fields, how Jobs relate to Customers) can be offered after the answer.
