# list-business-objects

Runs with: path_data_model(command: "list-business-objects", args: "<flags>") (read-only).

```
Usage: list-business-objects [options]

List business objects (id, name, plural), then each one's context: what the
designer says it is for

Options:
```

## Notes

The account's business objects: the kinds of record it keeps (Jobs, Quotes, Customers, Job Items, Purchase Orders), with each one's id and singular and plural names. Usually the first call for a question about how the account is set up.

**Examples**

```
list-business-objects
list-business-objects --fields id,name
```

- No flags: it always lists every business object the acting user has. Designers see all of them, as in the Designer; everyone else sees the business objects their roles may view.
- Other commands take a business object by name or id: `list-fields --bo-name Job` (singular or plural name, any case: `Job`, `jobs`, `"Job Item"`) or `list-fields --bo-id 5050`.
- "Shown in the menu" isn't the same as "exists": a business object left out of the menu (Job Items, on many accounts) is still listed here and can be read with `find-records`, `get-record` and `list-related-records`; `search` and the web's search box leave it out.

**Output**

```
id   | name           | plural
-----|----------------|----------------
5050 | Job            | Jobs
5052 | Quote          | Quotes
5044 | Address        | Addresses
[62 business objects]

Business object context:
  Address (5044): Addresses for location information
```

- Columns: `id`, `name` (singular) and `plural`. Other commands take the id or either name: `list-fields --bo-id 5050`, `list-fields --bo-name Jobs`.
- `Business object context:` under the table: each business object's designer description (often the best description of what its records mean). Long ones are cut and `get-business-object --bo-id <id>` prints them whole; like all context, each is shown once per session.

**Common errors**

- `Business object "Jobz" not found — did you mean "Jobs"?` (any command given a business object name): up to three close names, or, with nothing close, `(list-business-objects lists them)`.
- `Ambiguous business object name "…". Matches: …`: two business objects share the name; pass the id instead (`find-records --bo-id 5050`).

**Related:** `get-business-object` (one business object in detail), `list-fields`, `list-relationships`, `list-workflow-milestones`, `find-records --catalog` (what one business object can be searched on), `session-context` (the account's context).

Related commands: [`get-business-object`](get-business-object.md), [`list-fields`](list-fields.md), [`get-field`](get-field.md), [`list-field-groups`](list-field-groups.md), [`list-relationships`](list-relationships.md), [`list-workflow-milestones`](list-workflow-milestones.md), [`list-workflow-steps`](list-workflow-steps.md), [`list-workflow-tasks`](list-workflow-tasks.md), [`list-tag-groups`](list-tag-groups.md), [`list-file-types`](list-file-types.md), [`list-appointment-fields`](list-appointment-fields.md), [`parse-filter`](../../../pathwd-find-records/references/commands/parse-filter.md), [`get-filter`](../../../pathwd-find-records/references/commands/get-filter.md).
