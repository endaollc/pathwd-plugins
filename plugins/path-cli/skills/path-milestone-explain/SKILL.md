---
name: path-milestone-explain
description: Use this skill when the user asks why a specific record is, or is not, in a specific milestone. It checks the milestone's criteria one condition at a time against the record's field values, active steps, milestones, archived state and relationships, and shows which conditions pass and which fail. E.g. "why is Job 1347 in Lead?", "why isn't this record in Won?", "explain the milestone criteria for record X in milestone Y".
---

> **How to run commands:** every command in this skill is a PATH CLI command string. Run it by calling the `path_cli` tool with the string as `command`, for example `path_cli(command: "list-business-objects")`. Never run these commands in a terminal. If the user isn't signed in yet, run the `path-session` skill first.

Explain why a record satisfies (or doesn't satisfy) a single milestone's criteria, by walking the milestone's `filter_condition` tree against the record's current state.

The PATH CLI exposes every piece of data needed: the record, the milestone (with its full filter tree), the workflow steps (for names), and the fields (for names + types). You evaluate each leaf yourself in the conversation and present an annotated tree.

## What you need from the user

Required, both must be provided:
1. **Record reference** — either the internal record ID (24-char hex like `665f1c2e9b1d4a0012345678`) **or** the record's user-facing identifier (e.g. `JOB1347`, `1347`, `-1347: MAIN ST Job`). If it's an identifier, you resolve it to an internal ID per the "Resolve the record" section below.
2. **Milestone name** (e.g. "Lead", "Won", "Ready to Order").

If either is missing, ask before doing anything else. This skill always targets **one specific milestone** — never multiple.

If no PATH session is active, run the `path-session` skill first.

## Always refer to the record by its identifier

In every line of output, including the verdict header and any disambiguation prompts, refer to the record as:

```
<identifier> (<record_id>)
```

The identifier comes from the BO's `identifier_field_id` — that field's value on the record is the human-facing identifier (e.g. `-1347: MAIN ST Job`). Never show the internal record ID alone; never omit it either.

## Workflow

### 1. Resolve the record

If the user gave a **24-char hex internal ID**, skip to step 2.

Otherwise — they gave an identifier — find the record:

```
search --query "<user input>"
```

If the user already named the BO (e.g. "Job 1347"), narrow the search:
```
search --query "<user input>" --bo-id <BO_ID>
```
(Resolve BO name → id with `list-business-objects` if needed.)

Handle the result:
- **Zero hits** → tell the user, ask them to recheck the identifier or supply the internal ID.
- **One hit** → use that record's `id`.
- **Multiple hits** → for each candidate, look up its identifier via the BO's `identifier_field_id` (see step 3) and present a short list as `<identifier> (<record_id>) — <BO name>`. Ask the user to pick.

### 2. Fetch the record

```
get-record --record-id <RECORD_ID> --json
```

Capture from the response:
- `business_object_id` — needed to look up milestones/steps/fields for the right BO
- `milestones` (array of milestone IDs the record is currently in)
- `steps` (array of currently active step IDs)
- `fields` (object keyed by field ID — contains the identifier value)
- `relationship_fields` (object keyed by relationship ID)
- `archived_on` (truthy = archived)

### 3. Resolve the BO and the record's identifier string

```
get-business-object --bo-id <business_object_id> --json
```

From the BO response capture:
- `singular_name` / `plural_name` — used for the next three calls (`--bo-name`)
- `identifier_field_id` — the field whose value on the record is the human identifier

The display string for this record is `record.fields[identifier_field_id]`. Use it for every reference to the record in the output, in the format `<identifier> (<record_id>)`.

### 4. Fetch milestones, steps, and fields for that BO in parallel

```
list-workflow-milestones --bo-name <NAME> --json
list-workflow-steps --bo-name <NAME> --json
list-fields --bo-name <NAME> --json
```

You may also need:
```
list-relationships --bo-name <NAME> --json
```
…but only if the milestone's `filter_condition` contains any `relationship` leaves.

Build three lookup maps in conversation:
- `stepById` → `{id, name}`
- `fieldById` → `{id, name, field_type}`
- `relById` → `{id, name}` (if needed)

### 5. Find the target milestone

In the milestones array, find the one whose `name` matches the user's milestone (case-insensitive).

- If not found: list the milestone names that exist for the BO and ask the user to pick one.
- If found but `is_enabled: false`: still walk the tree, but call out that the milestone is disabled.
- If found but `filter_condition` is `null` or `filter_condition.filters` is empty: this is typically the **initial / default milestone** (see `is_initial: true`). Say so — every record enters it by default; there's nothing to evaluate.

### 6. Walk `filter_condition.filters` against the record

`filter_condition.filters` is a **group node**: `{ type: "group", condition: "and" | "or", filter_types: [...] }`. Walk it recursively.

For each leaf node, evaluate against the record and annotate the result. Use the operator semantics from the `cli-logic-builder` command (mirror them — don't invent new ones).

#### Leaf evaluation reference

| Leaf `type` | What you compare | How to evaluate |
|---|---|---|
| `field` | `record.fields[field_id]` vs `value` | Apply `logic` per field-type compat in the `cli-logic-builder` command. Render with the field's name. |
| `step` | `value` ∈ `record.steps` | `equal`/`in` → step ID is in `record.steps`. `not_equal` → step ID is NOT in `record.steps`. `not_in` → at least one listed step ID is NOT in `record.steps` ("does not have all of"). `empty` → `record.steps` is empty. `contains_all` → all listed step IDs are in `record.steps`. `contains_none` → none of the listed step IDs are in `record.steps`. |
| `milestone` | `value` ∈ `record.milestones` | Same shape as step. |
| `archived` | `record.archived_on` | `empty` → field is null/missing (record NOT archived). `is_not_empty` → field has a value (record IS archived). |
| `relationship` | `record.relationship_fields[relationship_id]` | `is_not_empty` → relationship has any linked record. `equal` → linked record ID matches `value`. |
| `record_id` | `record.id` | `equal` → matches `value`. `in` → `record.id` ∈ `value` (array or comma-separated string). |

#### Group evaluation

- `condition: "and"` → group passes if **every** child passes.
- `condition: "or"` → group passes if **any** child passes.
- Nested groups: recurse with the same rules.

The leaf shape sometimes has `value` as a scalar (e.g. one step ID) and sometimes as an array — coerce as needed. Step/milestone leaves in real data are often a flat list of single-ID `not_equal` leaves combined with `and` (the UI's way of saying "none of these steps").

### 7. Render the annotated tree

Lead with a one-line verdict, then the tree. Resolve every ID to a human name. Show the record's actual value alongside the comparison so the user can see exactly why each leaf passed or failed.

#### Output template (normal case — criteria agree with membership)

```
Record <identifier> (<record_id>)
Milestone "<milestone name>" (<milestone_id>)  —  is_enabled: <true|false>
Currently in this milestone: <YES | NO>   ← from record.milestones

<one- or two-sentence plain-English summary of why — no label, no preface>

<tree>
```

#### Output template (drift case — criteria disagree with membership)

```
Record <identifier> (<record_id>)
Milestone "<milestone name>" (<milestone_id>)  —  is_enabled: <true|false>
Currently in this milestone: <YES | NO>   ← from record.milestones
Criteria evaluation: <TRUE | FALSE>       ← only shown when it disagrees with the line above

⚠ Current state disagrees with milestone membership.

<one- or two-sentence plain-English summary of why — no label, no preface>

<tree>
```

Where `<identifier>` is `record.fields[BO.identifier_field_id]` — never the raw record ID alone.

The plain-English summary sits **directly under** the membership line (or the drift warning, if shown) and explains in normal language what the tree shows. Don't label it "Plain English" or "Summary" — just write the sentence. Keep it tight: one sentence is usually enough; two if there's both a positive and a negative condition driving the result.

**Always** show the `Currently in this milestone` line, the plain-English summary, and the tree. **Only** show the `Criteria evaluation` line and the drift warning when the evaluation result disagrees with `record.milestones` — i.e. record IS in the milestone but criteria evaluate FALSE, or record is NOT in the milestone but criteria evaluate TRUE. Otherwise omit those two lines entirely.

Tree formatting:
- Group nodes: `AND  →  TRUE` or `OR  →  FALSE`, indented children below.
- Leaf nodes: `✓` or `✗`, then a one-line plain-English description.
- Always show the record's actual value for field leaves: `Field "Department" = "Trade"  ✓ equals 'Trade'`.
- For step/milestone leaves, show whether the ID is present on the record: `Step "Quote Created" (362) NOT in record.steps  ✗ required equal`.

#### Example

```
Record 1347: MAIN ST Job (665f1c2e9b1d4a0087654321)
Milestone "Lead" (8820)  —  is_enabled: true
Currently in this milestone: YES

Job 1347 is in Lead because it has the "Job Created" step and none of the steps that would advance it out of Lead (any quote created/sent, qualified, won, lost, on hold, etc.).

AND  →  TRUE
  ✓ Step "Job Created" (9189)  contains_all  →  IS in record.steps
  ✓ Step "Full Quote Created" (9192)  contains_none  →  NOT in record.steps
  ✓ Step "Job Won" (9194)  contains_none  →  NOT in record.steps
  ✓ Step "Job Lost" (9195)  contains_none  →  NOT in record.steps
  …
```

When drift exists (criteria evaluation disagrees with membership), use the drift-case template above. Don't speculate about the cause — milestone evaluation in PATH is event-driven, and the user knows that.

## CLI commands used

| Command | Purpose |
|---|---|
| `search --query <text> [--bo-id <id>]` | Resolve a user-supplied identifier to an internal record ID |
| `get-record --record-id <id> --json` | Fetch the record (fields, milestones, steps, relationship_fields, archived_on) |
| `get-business-object --bo-id <id> --json` | Resolve BO name + `identifier_field_id` |
| `list-business-objects` | Resolve BO name → id when narrowing a search |
| `list-workflow-milestones --bo-name <n> --json` | Find the target milestone + its `filter_condition` |
| `get-filter milestone <milestone-id>` | The milestone's criteria as formula text, a readable summary to show alongside the tree |
| `list-workflow-steps --bo-name <n> --json` | Resolve step IDs in the tree to names |
| `list-fields --bo-name <n> --json` | Resolve field IDs to names + types |
| `list-relationships --bo-name <n> --json` | Only if the tree has `relationship` leaves |


## Rules

1. **One milestone per invocation.** If the user names two or more, ask which one to evaluate. Run the skill again for additional milestones.
2. **Always require both a record ID and a milestone name.** Ask if missing.
3. **Use the path_cli tool.** Everything the skill needs comes from the commands listed above.
4. **Resolve every ID to a name** in the output. Raw IDs are useless to the user.
5. **Show the record's actual values** alongside the comparison. The whole point of the skill is "why" — that requires showing the inputs, not just the verdict.
6. **Don't fix anything.** The skill is read-only and explanatory. If a leaf fails, just say which leaf and why; don't suggest running steps or editing fields unless the user asks. Changing the milestone's criteria (`set-filter milestone`) is Designer work: if the user wants it fixed, an Admin or Designer changes it in PATH's Designer (or with the PATH Designer plugin's `path-logic-formula-syntax` skill, if installed).
7. **Operator semantics come from the `cli-logic-builder` command.** If you encounter a leaf operator not documented there, stop and say so rather than guessing.
