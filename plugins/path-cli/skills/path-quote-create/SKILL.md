---
name: path-quote-create
description: Use this skill when the user wants to create a new Quote in PATH WD, e.g. "create a quote", "start a quote on Job 12345", "make a Full Quote / Quick Quote / Final Contract / Commercial Bid / Quick Contract". It creates the Quote, links it to its Job, Project or Service, fills Customer, Contact and Address from the parent, adds Quote Items and checks the Terms, stopping when the data is ready. It does not generate quote documents.
---

> **How to run commands:** every command in this skill is a PATH CLI command string. Run it by calling the `path_cli` tool with the string as `command`, for example `path_cli(command: "list-business-objects")`. Never run these commands in a terminal. If the user isn't signed in yet, run the `path-session` skill first.

Create a Quote in PATH WD with the minimum data needed before generating quote documents. Strictly via direct API calls (the PATH CLI's `create-record` / `link-record` / `update-record`); no pathway execution.

## Pre-flight

Run the `path-session` skill first if a CLI session isn't already established. All commands below are run through the `path_cli` MCP tool.

A default account must already be set via `use-account` (or `PATH_ACCOUNT_ID` in `~/.path-cli/.env`).

## Preload all metadata before talking to the user

When this skill starts, **silently pre-fetch every piece of metadata you'll need before asking the user anything**. The reads are read-only and cheap; doing them up front means the conversation only happens once the agent already knows every field id, relationship id, folder id, and folder-record set. Run step 1 (below) to completion first, then collect inputs in a single batch, then execute steps 2+.

If a parent record ID is in the user's opening message, also run step 2 (verify the parent + capture its relationships and the linked Customer's Billing Address) during preload. If no parent ID is given, ask only that question, then preload, then ask the rest.

## Mandatory clarification before any API write

Before any **write** (`create-record`, `link-record`, `update-record`), confirm the user has named a **Quote Type**. If not, ask. It must be one of:

- `Quick Quote`
- `Full Quote`
- `Final Contract`
- `Commercial Bid`
- `Quick Contract`

PATH WD's "Service Quote" and "Installation Proposal" flavors are NOT separate Quote Type values — they are full/quick quotes attached to a Service or Project parent. If the user uses those names, infer the Quote Type and the parent type, and confirm.

## Required inputs (collect interactively)

| # | Input | Where used |
|---|---|---|
| 1 | Parent record — type ∈ {Job, Project, Service} + record ID | linked via `Quote Job` / `Quote Project` / `Quote Service` rel |
| 2 | Quote Type (above) | field `Quote Type` on Quote |
| 3 | Description (short cover-sheet summary) | field `Description` on Quote |
| 4 | Scope of Work (detailed body) | field `Scope of Work` on Quote |
| 5 | Primary Vendor — name | field `Primary Vendor` on Quote (resolves to a record in the `Vendors` folder) |
| 6 | Line items, ≥ 1 | each becomes a Quote Item record |
| 7 | Terms — name | field `Terms` on Quote (resolves to a record in the `Terms` folder); collected last |

**Per line item** (collect for each):

- description, qty, sell, cost, vendor (defaults to Primary Vendor if not specified), tax_category (e.g. `Taxable`, `Non-Taxable`, `Tax Included`)
- category — defaults to `Group`. Only override to `Unit` or `Discount` if the user explicitly says so for that line.

## Step-by-step

### 1. Resolve metadata (cache in conversation context) — RUN BEFORE PROMPTING THE USER

Do all of the lookups in this section before the first user-facing question. The point is to enter the conversation already holding every id you need, so the rest of the skill is just data entry.

Use `--all` for any list-* command — the gateway caches the paginated endpoints and only `--all` (which uses `/init/...`) returns everything reliably.

```
# BO IDs
get-business-object --bo-name Quote --json
get-business-object --bo-name "Quote Item" --json
get-business-object --bo-name "<parent type>" --json   # Job / Project / Service

# All Quote fields by name
list-fields --bo-name Quote --all --json
# All Quote Item fields by name
list-fields --bo-name "Quote Item" --all --json
```

Capture these field IDs (lookup by `name`):

- On Quote: `Description`, `Scope of Work`, `Quote Type`, `Primary Vendor`, `Terms`
- On Quote Item: `Description`, `Quantity`, `Sell`, `Cost`, `Vendor`, `Tax Category`

```
# Relationships for Quote and Quote Item
api get '/init/relationships' --json
```

Filter the response client-side to capture (by `name` and `business_object_id`):

- Outbound from Quote: `Quote Job`, `Quote Project`, `Quote Service`, `Quote Customer`, `Quote Contact`, `Quote Job Address`, `Quote Billing Address`, `Quote Outside Salesperson`, `Quote Inside Support Person`
- Outbound from Quote Item: `QI Quote`, `QI Category`
- Outbound from Job to Quote: `Most Recent Quote` (used for the inverse link)
- Outbound from Commercial Project to Quote: `Most Recent Bid`
- Project and Service have NO outbound rels to Quote — skip the inverse link for those parents.

**Resolve these folders by name from `list-folders --all`** (folder names are stable across PATH WD accounts; only IDs differ):

| Folder name | BO | Used for |
|---|---|---|
| `All Vendors` | Vendor | Primary Vendor + per-line Vendor lookup |
| `Terms` | Admin Item | Terms field on Quote |
| `Quote Item Tax Category` | Admin Item | Tax Category on Quote Item |
| `Quote Item Categories` | Admin Item | `QI Category` relationship target (`Group`, `Unit`, `Discount`) |

```
# IMPORTANT: --all is required. Without it, list-folders returns only page 1
# and the Quote-relevant folders may not appear.
list-folders --all --json

# Resolve the records in each folder so you can map display names -> record IDs
list-records --folder-id <All Vendors folder id> --json
list-records --folder-id <Terms folder id> --json
list-records --folder-id <Quote Item Tax Category folder id> --json
list-records --folder-id <Quote Item Categories folder id> --json
```

For each folder record, the display value is in `fields[<display field id>]`. The display field id = `select_business_object_field_id` on the corresponding selection field metadata captured earlier:

- **Vendor display field** = `select_business_object_field_id` from the `Primary Vendor` (or `Vendor`) field
- **Admin Item display field** = `select_business_object_field_id` from the `Terms` (or `Tax Category`) field

Capture both once and reuse for every folder-record lookup.

### 2. Verify the parent record

```
get-record --record-id <PARENT_ID> --json
```

Confirm `business_object_id` matches the expected parent BO. Capture the parent's relationship map for use in step 5 — the keys are relationship IDs.

### 3. Resolve Primary Vendor

Match the user's vendor name (case-insensitive substring) against the records pulled from the Vendors folder. If multiple matches, ask the user to pick. Keep both the **record ID** and the **display value**.

### 4. Create the Quote

The CLI's `create-record` expects `fields` as an **array of `{field_id, value}` objects** — NOT a map.

For `business_object_folder` selection fields (`Primary Vendor`, `Terms`, `Vendor` and `Tax Category` on Quote Items), pass the **display value** as the `value` (matching how PATH WD stores these — confirmed by inspecting an existing Quote record).

```
create-record --bo-name Quote --data '{
  "fields": [
    {"field_id": <Description-id>,    "value": "<short description>"},
    {"field_id": <Scope-of-Work-id>,  "value": "<long body>"},
    {"field_id": <Quote-Type-id>,     "value": "Full Quote"},
    {"field_id": <Primary-Vendor-id>, "value": "Marvin - Modern"}
  ]
}' --json
```

Capture the new Quote's `id` (string MongoDB ObjectID-style).

### 5. Link Quote → Parent

Use the relationship matching the parent type (resolved in step 1):

```
link-record \
  --record-id <QUOTE_ID> \
  --related-record-id <PARENT_ID> \
  --relationship-id <Quote-Job | Quote-Project | Quote-Service rel id>
```

### 6. Set parent's Most Recent Quote / Bid (Job and Commercial Project only)

```
# Job parent
link-record --record-id <PARENT_ID> --related-record-id <QUOTE_ID> --relationship-id <Most-Recent-Quote rel id>

# Commercial Project parent
link-record --record-id <PARENT_ID> --related-record-id <QUOTE_ID> --relationship-id <Most-Recent-Bid rel id>
```

Project and Service parents have no equivalent inverse — skip this step.

### 7. Auto-fill Quote relationships from the parent

For each pair below, find the related record on the parent (`get-record` on the parent → look at its `relationships` map) and link it onto the new Quote.

**Job parent:**

| Source on Job | Target rel on Quote |
|---|---|
| `Job Main Customer` | `Quote Customer` |
| `Job Main Contact` | `Quote Contact` |
| `Job Address` | `Quote Job Address` |
| `Job Outside Salesperson` | `Quote Outside Salesperson` |
| `Job Inside Support` | `Quote Inside Support Person` |

For `Quote Billing Address`: `get-record` on the Customer record (the value of `Job Main Customer` on the parent), then read its `Customer Billing Address` relationship (outbound from Customer BO → Address BO) and link that record onto the Quote. Capture the `Customer Billing Address` rel id from `/init/relationships` once and reuse.

**Service parent:** equivalent pattern using `Service Main Customer`, `Service Main Contact`, `Service Address`, `Original Salesperson`, etc.

**Project / Commercial Project parent:** project-specific pattern — look up `Project Customer`, `Project Address`, etc. and map likewise.

If a source relationship is empty on the parent, skip that target link and continue. Do **not** prompt the user to fill missing parent data.

```
link-record --record-id <QUOTE_ID> --related-record-id <RELATED_ID> --relationship-id <quote-side rel id>
```

### 8. Create Quote Items

For each line item the user provided:

a. Resolve the line's `vendor` against the Vendors folder (default to Primary Vendor if not specified).
b. Resolve the line's `tax_category` against the Quote Item Tax Category folder.
c. Resolve the line's `category` against the Quote Item Categories folder (Group / Unit / Discount). Default = `Group`.

Create the Quote Item:

```
create-record --bo-name "Quote Item" --data '{
  "fields": [
    {"field_id": <QI-Description-id>,  "value": "<line description>"},
    {"field_id": <QI-Quantity-id>,     "value": <qty>},
    {"field_id": <QI-Sell-id>,         "value": <sell>},
    {"field_id": <QI-Cost-id>,         "value": <cost>},
    {"field_id": <QI-Vendor-id>,       "value": "<vendor display name>"},
    {"field_id": <QI-Tax-Category-id>, "value": "<tax category display name>"}
  ]
}' --json
```

Then link it to the Quote, and link its category:

```
link-record --record-id <NEW_QI_ID> --related-record-id <QUOTE_ID> --relationship-id <QI-Quote rel id>
link-record --record-id <NEW_QI_ID> --related-record-id <CATEGORY_RECORD_ID> --relationship-id <QI-Category rel id>
```

(`CATEGORY_RECORD_ID` = the Group/Unit/Discount Admin Item record ID resolved in step 1.)

### 9. Verify Terms

Show the user the resolved Terms-folder records (display names). Ask which terms apply. Update the Quote:

```
update-record --record-id <QUOTE_ID> --data '{
  "fields": [
    {"field_id": <Terms-id>, "value": "<terms display name>"}
  ]
}'
```

The CLI auto-resolves `business_object_id` from the record itself, so it does not need to be in the payload.

### 10. Recap and stop

Print a summary like:

```
Created Quote <quote-id>
  Quote Type: <type>
  Parent:     <Job|Project|Service> <parent-id>
  Primary Vendor: <vendor>
  Terms:      <terms>
  Line items: <n>
    1. <description> — qty <q> @ $<sell> [<category>]
    ...
```

**Stop here.** Document generation is out of scope.

## Notes / Gotchas

- The `list-fields`, `list-relationships`, `list-field-groups`, `list-workflow-milestones`, `list-folders`, and similar paginated commands return **stale / page-1-only** results without `--all` due to a gateway-side cache key bug. Always pass `--all` when you need the complete set. Behind the scenes `--all` skips the cached BO-scoped endpoint and uses `/init/...` (which returns the full account-wide set and is filtered client-side).
- `create-record` and `update-record` run record CRUD via the pathway executor (`POST /v1/pathways/-1/execute` with `step_type` of `create_record` / `edit_record`). Field values are passed as an **array** of `{field_id, value}` objects (NOT a map). For `business_object_folder` selection fields, the **display value** (not the record ID) is the right thing to write. If a 422 comes back, retry with the record ID instead and report which form worked.
- `link-record` runs the pathway executor with `step_type: link_record`. The CLI's `--related-record-id` flag maps to the executor's `relationship_record_id` — no body-shape gotchas.
- `get-record` (without `--json` or `--raw`) renders fields and relationships as `Name (id): value` by joining `/init/fields` + `/init/relationships`. The skill always passes `--json` so it gets the raw ID-keyed map; if you need a quick human-readable peek mid-flow, drop the `--json` flag.
- Required-marked relationship-derived fields on Quote (`Billing State`, `State`, `Deposit Instructions`) auto-populate from the linked Address records — do **not** prompt for them. If `create-record` fails on those, ensure the parent's address relationships are populated first and the Quote → Address links succeed.
- A Quote created this way has no milestones entered. Downstream `Generate <variant> Quote` pathways may require specific milestones to be active before they will produce documents — that's the next skill's responsibility, not this one's.
- The skill never executes pathways. If the user asks "now generate the document", direct them to the `Generate Full Quote` / `Generate Quick Quote` / `Generate Final Contract` etc. pathway in the PATH UI, or to a future companion skill.
