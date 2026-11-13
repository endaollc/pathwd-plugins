# list-file-types

Runs with: path_data_model(command: "list-file-types", args: "<flags>") (read-only).

```
Usage: list-file-types [options]

List a business object's file types, as Designer → Files → File Types lists
them (the system types Archived and Email Attachment are left out unless
--include-hidden)

Options:
  --bo-id <id>      Business object ID
  --bo-name <name>  Business object name (resolved to ID)
  --include-hidden  Also list the hidden system types (Archived, Email
                    Attachment)
```

Related commands: [`list-business-objects`](list-business-objects.md), [`get-business-object`](get-business-object.md), [`list-fields`](list-fields.md), [`get-field`](get-field.md), [`list-field-groups`](list-field-groups.md), [`list-relationships`](list-relationships.md), [`list-workflow-milestones`](list-workflow-milestones.md), [`list-workflow-steps`](list-workflow-steps.md), [`list-workflow-tasks`](list-workflow-tasks.md), [`list-tag-groups`](list-tag-groups.md), [`list-appointment-fields`](list-appointment-fields.md), [`parse-filter`](../../../pathwd-find-records/references/commands/parse-filter.md), [`get-filter`](../../../pathwd-find-records/references/commands/get-filter.md).
