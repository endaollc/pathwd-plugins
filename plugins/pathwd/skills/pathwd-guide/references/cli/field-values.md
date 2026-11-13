# Field values: what each field type takes

How to write a value for every PATH field type, in the two places a value is written:

- **`create-record` / `update-record --data`** take the **stored value**, exactly as PATH keeps it. The CLI sends it unchanged: a Percent of 50% is `0.5`, a date is a UTC timestamp, a time span is seconds, a phone number is digits.
- **`pathway-run submit --set "<Field>=<text>"`** takes **typed text**, the way a person types into the web form. The CLI turns it into the stored value with the web form's own rules: `50` in a Percent field becomes `0.5`, `2026-10-15` becomes noon that day on the user's clock.

The same value written both ways:

| Field (type) | `--set` on a pathway screen | `--data` value for create-record / update-record |
|---|---|---|
| Deposit % (Percent), 50% | `--set "Deposit %=50"` | `{"field_id": 191650, "value": 0.5}` |
| Est Ship Date (Date And Time, date only), Oct 15 2026 | `--set "Est Ship Date=2026-10-15"` | `{"field_id": 191635, "value": "2026-10-15T17:00:00.000Z"}` (noon Central) |
| Install Time (Time Span), 1 h 30 min | `--set "Install Time=1h 30m"` | `{"field_id": 191710, "value": 5400}` |
| Customer Phone (Phone Number) | `--set "Customer Phone=(512) 555-0126"` | `{"field_id": 193754, "value": "5125550126"}` |
| Total Sell (Currency), $1,250.50 | `--set "Total Sell=$1,250.50"` | `{"field_id": 191640, "value": 1250.5}` |
| Department (Selection) | `--set "Department=residential"` (any case) | `{"field_id": 191620, "value": "Residential"}` (exact option text) |
| Tax Exempt (Boolean) | `--set "Tax Exempt=yes"` | `{"field_id": 191637, "value": true}` |

The field ids above are examples; each account has its own (`list-fields --bo-name <name>`).

## The `--data` payload

```
create-record --bo-name Job --data '{"fields":[{"field_id":191620,"value":"Residential"},{"field_id":191640,"value":1250.5}]}'
update-record --record-id 6a9056455ff33756afba5084 --data '{"fields":[{"field_id":191635,"value":"2026-10-15T17:00:00.000Z"}]}'
```

- `fields` is an **array** of `{"field_id": <number>, "value": <stored value>}`. An object (`{"fields":{}}`) fails.
- `field_id` is the numeric id; field names are not read in `--data`.
- `create-record` takes the business object from `--bo-name` / `--bo-id`; `update-record` reads it from the record. A `business_object_id` in `--data` isn't needed.
- `create-record` makes the record as the acting user (`user_id`); the record gets that user's tags.
- A field left out of `fields` keeps its value. Nothing is converted, defaulted or range-checked by the CLI: a wrong form (a string in a number field, `50` in a Percent field) is saved as sent, or refused by the server.
- Only fields the record viewer shows the acting user can be written; a hidden field is refused, as in the web app. A Notes field is refused (use `add-record-note`).
- The result names the record and the fields sent (`Updated record 6a9056455ff33756afba5084 (Job 26-0050): Est Ship Date`), then a `Milestones:` line with the milestones it is in after the save. `get-record --record-id <id>` shows the new values by field name.

### Where the ids and stored forms come from

- **`list-fields --bo-name <name>`**: a table of every field: `id`, `name`, `type` (the `field_type` below; a date-only Date And Time field shows `date_time (date only)`), `req`, `from` (a field shown from a linked record: not writable here) and `options` (a Selection's preset options). `get-field --field-id <id>` shows one field whole: its type, whether it is writable, its group and every option.
- **`get-record --record-id <id> --raw`**: an existing record as stored, a table of `id | name | value`, where `value` is the stored value as its JSON literal: strings quoted (`"Retail"`, `"2026-10-15T17:00:00.000Z"`), numbers, `true` / `false` and `null` bare, lists and objects as compact JSON. A value copies into `{"field_id": <id>, "value": <value>}` as printed. A value longer than 80 characters is cut with `…`; `get-record --raw --record-id <id> --field-id <field id>` prints it whole.

```
id     | name          | value
-------|---------------|--------------------------
191629 | Department    | "Trade"
191649 | Tax Rate      | 0.0825
191635 | Est Ship Date | "2026-10-15T17:00:00.000Z"
```

becomes `--data '{"fields":[{"field_id":191649,"value":0.09},{"field_id":191635,"value":"2026-10-20T17:00:00.000Z"}]}'` to set the tax rate to 9% and move the date.

## The `--set` answer

- `--set "<Field name or id>=<text>"`, repeatable, on Edit Record, Create Record, Create or Lookup (Create tab), Edit Record Field and Edit Record Table (`<row>.<Field>`) screens. Names match case-insensitively; only fields the screen shows are accepted.
- Required fields, email addresses and phone numbers are checked as the web form checks them; a refusal names the field and the run stays on the screen.
- What each screen accepts is in `pathway-screens.md` (this folder).

## Field types

<!-- generated:field-types -->
| Type (Designer name) | `field_type` | Writable | Pathway screen text (`--set`) | Stored value (`--data`) | Blank |
|---|---|---|---|---|---|
| Aggregate Count | `aggregate_count` | no | not typed: PATH calculates it | not writable: PATH recalculates it when the record is saved | — |
| Aggregate Number | `aggregate_number` | no | not typed: PATH calculates it | not writable: PATH recalculates it when the record is saved | — |
| Aggregate Text | `aggregate_text` | no | not typed: PATH calculates it | not writable: PATH recalculates it when the record is saved | — |
| Aggregate Time Span | `aggregate_time_span` | no | not typed: PATH calculates it | not writable: PATH recalculates it when the record is saved | — |
| Appointment | `appointment` | no | not one value: the appointment keys (`start`, `end`, `calendar`, …) of the Edit Record Field screen | not written with `--data`: `set-appointment` / `clear-appointment` / `create-calendar-event --record … --field …` | `clear-appointment` |
| Auto Number | `auto_number` | no | not typed: PATH numbers new records | not writable: PATH sets it when the record is created | — |
| Boolean | `boolean` | yes | `true`, `yes`, `y`, `1`, `on`, `checked` / `false`, `no`, `n`, `0`, `off`, `unchecked` | `true` / `false` | `null` |
| Calendar Event | `calender_event` | yes | any text, kept as typed | a string: `"Smith Plumbing"` | `""` |
| Currency | `currency` | yes | `1250`, `1,250.50`, `$1250` (commas, `$`, `%` and spaces are dropped) | a number: `1250.5` (not a string) | `null` |
| Date And Time | `date_time` | yes | a date as in "Dates" below, read on the user's clock | a UTC ISO timestamp: `"2026-10-15T14:30:00.000Z"`; date-only fields: noon on the user's clock (see "Dates") | `null` |
| Email | `email` | yes | an email address (checked as `name@domain.tld`) | a string: `"pat@example.com"` | `""` |
| Format | `format` | yes | any text, kept as typed | a string: `"Smith Plumbing"` | `""` |
| ID | `id` | yes | `1250`, `1,250.50`, `$1250` (commas, `$`, `%` and spaces are dropped) | a number: `1250.5` (not a string) | `null` |
| Long Text | `long_text` | yes | any text, kept as typed | a string: `"Smith Plumbing"` | `""` |
| Notes | `notes` | no | not on forms (a Post Note screen adds a note) | refused: `add-record-note` / `update-record-note` / `delete-record-note` | — |
| Number | `number` | yes | `1250`, `1,250.50`, `$1250` (commas, `$`, `%` and spaces are dropped) | a number: `1250.5` (not a string) | `null` |
| Number Expression | `calculation` | no | not typed: PATH calculates it | not writable: PATH recalculates it when the record is saved | — |
| Percent | `percent` | yes | `50` or `50%` means 50% | a fraction: `0.5` for 50%, `0.0825` for 8.25% | `null` |
| Phone Number | `phone_number` | yes | any form: `(512) 555-0126`; everything but digits is dropped; 7 or 10–12 digits | digits only, a string: `"5125550126"` | `""` |
| Selection | `selection` | yes | one option's text, any case; the screen lists the options | one option's exact text: `"Residential"` (never a list) | `null` (refused when the field is required and offers no blank) |
| Tag | `tag` | no | not on forms; a create form may ask which of the user's tags to use | not writable: a new record gets its creator's tags (`pathway-run start --tag` picks one) | — |
| Text | `text` | yes | any text, kept as typed | a string: `"Smith Plumbing"` | `""` |
| Text Expression | `manual_format` | no | not typed: PATH calculates it | not writable: PATH recalculates it when the record is saved | — |
| Time Span | `time_span` | yes | `5400` (seconds), `1h 30m`, `2d`, `PT1H30M`; units `w`, `d`, `h`, `m`, `s`, `wk`, `day`, `days`, `hr`, `hrs`, `hour`, `hours`, `min`, `mins`, `minute`, `minutes`, `sec`, `secs`, `second`, `seconds` | seconds, a number: `5400` for 1h 30m | `null` |
| Time Span Expression | `time_span_expression` | no | not typed: PATH calculates it | not writable: PATH recalculates it when the record is saved | — |
| URL | `url` | yes | any text, kept as typed | a string: `"Smith Plumbing"` | `""` |

Blank words on a pathway screen: `null`, `(blank)`, `blank` or nothing (`--set "Field="`).
<!-- /generated:field-types -->

A Number field formatted with a percent symbol (`get-field` shows its format) behaves like a Percent field: it is typed as `50` and stored as `0.5`.

## Dates

**Typed text (`--set`, and the date options of `create-task`, `pathway-run` screens):**

| Text | Means |
|---|---|
| `2026-10-15` | that day; on a date-and-time field, noon |
| `"2026-10-15 09:30"`, `"2026-10-15 9:30 pm"` | that day and time (24-hour, or with am/pm) |
| `14:30`, `2:30 pm` | today at that time |
| `today`, `tomorrow` | noon today / tomorrow |
| `now` | this moment (a date-only field: noon today) |
| `+3d`, `-2h`, `+30m`, `+1w` | that long from now (`d` days, `h` hours, `m` minutes, `w` weeks) |
| `2026-10-15T14:30:00Z`, `2026-10-15T09:30:00-05:00` | that instant, used as given |

Times are on the **user's clock**: the time zone in their PATH profile (`get-profile`), which is the account's zone unless they changed it. `10/15/2026`, `Oct 15` and other forms are refused: `Can't read "10/15/2026" as a date. Use YYYY-MM-DD, …`.

**Stored values (`--data`):** a UTC ISO timestamp, `"YYYY-MM-DDTHH:mm:ss.sssZ"`.

- **Date-only fields** (`date_time (date only)` in `list-fields`: the display format has no hour, e.g. `M/d/yyyy`) store **noon on the user's clock** that day, as the web's date picker does. In US Central time, Oct 15 2026 (daylight time, UTC-5) is `"2026-10-15T17:00:00.000Z"` and Jan 15 2026 (standard time, UTC-6) is `"2026-01-15T18:00:00.000Z"`. Noon keeps the day the same in every US time zone.
- Midnight UTC (`"2026-10-15T00:00:00.000Z"`) is the evening of **Oct 14** in every US time zone, so a date-only field written that way shows the day before.
- **Date-and-time fields** (a format with `h` / `H`, or no format) store the instant: 9:30 am Central on Oct 15 2026 is `"2026-10-15T14:30:00.000Z"`.
- `get-record` shows dates on the user's clock in the field's format; `--raw` shows the stored UTC value.

## Numbers, currency and percent

- Number and Currency store a plain number: `1250.5`. The currency symbol, commas and decimals are display formatting.
- **Percent: the one rule.** PATH stores a percent as a **fraction** and shows it as a percentage. Only the stored value uses the fraction; everything a person types uses the shown number:

  | Where | 50% is written | |
  |---|---|---|
  | `create-record` / `update-record --data`, and what `get-record --raw` shows | `0.5` | the stored value; writing `50` sets 5000% |
  | `pathway-run submit --set` | `50` or `50%` | typed text, as in the web form |
  | Logic Builder formulas (`find-records --logic`, folder and report filters) | `50` | the shown number: `[Deposit Percentage] = 50` matches 50%; `= 0.5` matches nothing, and `50%` is a syntax error |
  | `get-record`, `find-records` and report output | `50%` | display text |

  This holds for Percent fields and for Number fields (and Number Expressions) formatted with a percent symbol: Tax Rate 8.25% is stored `0.0825` and filtered as `[Tax Rate] = 8.25`.
- Calculated numbers (Number Expression, Aggregate Number, Aggregate Count, Auto Number) can't be written; change the fields they are calculated from.

## Time spans

A Time Span stores **seconds**: 1 hour is `3600`, 1 h 30 min is `5400`, 2 days is `172800`. Typed text takes seconds, units (`1h 30m`, `2d 4h`, `45 minutes`) or ISO durations (`PT1H30M`). The field's display format (hours, days, …) doesn't change what is stored.

## Phone numbers, email and URLs

- Phone Number stores **digits only** as a string: `"5125550126"`, `"15125550126"` with a country code. 7 or 10–12 digits; the web shows it as `(512) 555-0126`.
- Email stores the address as typed; a pathway form refuses one that isn't `name@domain.tld`.
- URL stores the text as typed.

## Selections

- A Selection field holds **one** option: its exact text, `"Residential"`. There is no multi-select Selection field and no array value.
- Preset options are in the `options` column of `list-fields` and listed whole by `get-field --field-id <id>`. A selection drawn from another business object's records offers the values of one of that business object's fields (`get-field` names it); the stored value is that text.
- A pathway screen may offer only some of a field's options; it lists them, and `--set` text is matched to them case-insensitively.
- Blank is `null`. A required Selection without a blank option can't be blanked on a pathway screen.

## Clearing a field

- `--data`: `""` for text-like fields (Text, Long Text, Email, Phone Number, URL), `null` for numbers, dates, time spans, checkboxes and selections: `{"fields":[{"field_id":191635,"value":null}]}`.
- `--set`: `--set "Est Ship Date="` or one of the blank words (`blank`, `null`, `(blank)`).
- A field left out of `--data`, or not named in `--set`, keeps its value.

## What isn't a field value

| To change | Use |
|---|---|
| A link to another record (customer on a job, a lookup field's source) | `link-record --record-id <holder> --related-record-id <target> --relationship-id <id>` / `unlink-record` |
| A field shown from a linked record (a relationship field) | the linked record itself, or the link |
| An address | Addresses are records of their own: create or find the Address record, then link it |
| A file | `upload-record-file`, `update-record-file`, `archive-record-file` |
| A Notes field | `add-record-note`, `update-record-note`, `delete-record-note` |
| An Appointment field | `set-appointment`, `clear-appointment`, `create-calendar-event --record <id> --field <field>` |
| A milestone | Milestones follow their criteria: a workflow step (`run-step`) or a pathway moves the record |
| Identifier, Created Date, Modified Date, Last Accessed | set by PATH |
| Tags on a record | set from the creating user's tags (`pathway-run start --tag` picks one when the user has several) |
