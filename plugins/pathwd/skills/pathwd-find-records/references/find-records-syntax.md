# find-records: formula syntax, recipes and common mistakes

`find-records` is PATH's advanced search: every record of one business object that matches a Logic Builder formula, run on the spot, nothing saved. This page is the whole formula language as `find-records --logic` reads it, the flags, tested recipes, the mistakes that fail (with the fix), and the ones that **don't** fail but give a wrong answer. Folder and report filters use the same language; their extras (`?` blanks, user filters, report columns) are in the [`path-logic-formula-syntax` guide](logic-formula-syntax.md).

## The one-minute version

```
find-records --bo-name Job --logic 'INMILESTONE("Lead") AND ISANYOF([Department], "Retail", "Trade")'
find-records --bo-name Job --logic 'INTHELAST([Created Date], DAYS(30))' --count
find-records --bo-name Job --catalog
```

- A rule is `[Field] <op> value`: `[Department] = "Retail"`, `[Estimated Sell Price] > 5000`.
- Everything else is a **function call with the field first**: `CONTAINS([Job Name], "window")`, `INTHELAST([Created Date], DAYS(30))`, `ISANYOF([Department], "Retail", "Trade")`. Never `[Job Name] CONTAINS "window"`.
- Rules join with `AND` / `OR`. Mixing them needs parentheses: `A AND (B OR C)`.
- Text in **double quotes**. Fields in `[ ]`. Dates as `DATE("2026-10-01")`. No `$`, commas or `%` in numbers.
- There is **no `NOT`** and no `!`: every rule has its own negative (`NOTINMILESTONE`, `NOTHASSTEP`, `ISNOTANYOF`, `NOTCONTAINS`, `ISNOTEMPTY`, `!=`).
- **A negative rule on the record's own field matches empty values** (never set or cleared): `[Department] != "Retail"` includes jobs with no Department. Through a relationship a record with **no linked record** does not match (see "Empty values").
- Names come from `find-records --bo-name <bo> --catalog`: the fields, options, relationships, milestones and steps this user can search on. A guessed name (`[Status]`, `INMILESTONE("Won")`) fails when the account doesn't have it.
- Check a formula with `--count`: it runs the real search and prints `<n> <records> match: <formula as PATH read it>`.

## Names

- **Fields:** `[Department]`, matched ignoring case. The names are the ones `--catalog` lists.
- **A linked record's field:** `[<relationship name>.<field>]` reaches any field on the linked record, and always works. It uses the **relationship's** name from `--catalog`, not the business object's: `[Job Main Customer.Customer Name]`, never `[Customer.Customer Name]`. Several hops: `[Rel1.Rel2.Field]`, one pair of brackets.
- **Relationship fields:** a field the business object shows from a linked record (`--catalog` lists them under "Relationship fields", e.g. `[Customer Type] = [Job Main Customer.Customer Type]`) is also named by its own name: `[Customer Type] = "Builder"` searches the linked customer's Customer Type through exactly that relationship, the same as `[Job Main Customer.Customer Type]` (the echo shows that form). With no relationship field of that name, write `[<relationship name>.<field>]`. A name the record's own field and a relationship field share, or two relationship fields share, is refused; the error lists how to write each (`[Relationship.Field]` or `[#id]`).
- **A relationship is not a field.** `[Job Main Customer] = "…"` fails. "Has a linked customer" is a rule on a field the linked record always has, such as its auto-number: `ISNOTEMPTY([Job Main Customer.CUST_ID])`; "has none": `ISEMPTY([Job Main Customer.CUST_ID])`.
- **Relationships go one way:** from the record that holds the link (`--catalog` lists them). "Customers with a job in Lead" can't be asked from Customer; ask Jobs instead (`find-records --bo-name Job --logic 'INMILESTONE("Lead")' --group-by "Job Main Customer.Customer Name"`).
- **Milestones and steps** are quoted names: `INMILESTONE("Lead")`, `HASSTEP("Job Won")`; on a linked record: `INMILESTONE("Job Main Customer.Active")`. All names in one call must be on the same record.
- **Ids:** `[#191629]`, `INMILESTONE("#17103")` stand for a field or milestone by id, for a name containing `]` or a name two fields share.

## Field types: operators, values, empties

<!-- generated:field-kinds -->
| Kind | Field types (`--catalog` shows each field's type) | Operators | Values | Empty / has a value |
|---|---|---|---|---|
| text | Aggregate Text, Calendar Event, Email, Format, Long Text, Notes, Phone Number, Tag, Text, Text Expression, URL | `=` `ISANYOF` `ISNOTANYOF` `!=` `CONTAINS` `NOTCONTAINS` `ISEMPTY` `ISNOTEMPTY` | `"text"`: `=` and ISANYOF are exact and case-sensitive, CONTAINS ignores case | `ISEMPTY` / `ISNOTEMPTY` (never `= ""`) |
| number | Aggregate Count, Aggregate Number, Auto Number, Currency, ID, Number, Number Expression, Percent | `=` `ISANYOF` `ISNOTANYOF` `!=` `<` `<=` `>` `>=` `ISEMPTY` `ISNOTEMPTY` | `5000`, `-1`, `12.5` (no `$`, commas or `%`; a percentage as shown: `8.25` for 8.25%) | `ISEMPTY` / `ISNOTEMPTY`; aggregates are `0`, never empty |
| time span | Aggregate Time Span, Time Span, Time Span Expression | `=` `!=` `<` `<=` `>` `>=` `ISEMPTY` `ISNOTEMPTY` | `HOURS(2)`, `DAYS(1)`, `MINUTES(90)`, `TIMESPAN(1, 12)` | `ISEMPTY` / `ISNOTEMPTY` |
| date | Appointment, Date And Time | `=` `<` `<=` `>=` `>` `BETWEENDATES` `!=` `ISANYTIME` `ISEMPTY` `INTHELAST` `INTHENEXT` `MORETHANAGO / MORETHANFROMNOW` `BETWEENRANGE` the calendar periods | `DATE("2026-10-01")`, `DATE("2026-10-01 14:30")` on the account's clock; `=` matches the whole day | `ISEMPTY`; has a value: `ISANYTIME` |
| yes/no | Boolean | `=` `!=` | `TRUE` / `FALSE` | unset counts as `FALSE` |
| pick list (own options) | selection with its own options | `=` `!=` `ISANYOF` `ISNOTANYOF` | `"Option text"` (any case), `BLANK` | `= BLANK` / `!= BLANK` |
| pick list (from records) | selection drawn from records | `=` `ISANYOF` `ISNOTANYOF` `!=` `CONTAINS` `NOTCONTAINS` `ISEMPTY` `ISNOTEMPTY` | `"text"`, exact and case-sensitive | `ISEMPTY` / `ISNOTEMPTY` |
<!-- /generated:field-kinds -->

- **Percent** fields compare with the number shown: 50% is `[Deposit Percentage] = 50` (`0.5` matches nothing; `50%` is a syntax error). This is the reverse of `create-record` / `update-record --data`, which take the stored fraction (`0.5`): see "Percent: the one rule" in `field-values.md` (this folder).
- **Dates** are read on the **account's** clock. `[Created Date] = DATE("2026-10-01")` matches the whole day; `>=` / `<` compare instants. Nothing is shorter than a day in `INTHELAST` / `INTHENEXT` (`DAYS`, `WEEKS`, `MONTHS`, `YEARS`, at least 1); for the last few hours compare with a time: `[Created Date] >= DATE("2026-11-10 14:00")`. There is no `TODAY()` / `NOW()`: use `ISTODAY([Date])`, `INTHELAST(...)` or a period function.
- **Text** `=` and `ISANYOF` are exact and case-sensitive (`"retail"` doesn't match `Retail` in a text field; a pick list's own options match in any case). `CONTAINS` ignores case and has no wildcards. A `"` inside text is written `""`; there are no backslash escapes.
- **People:** `CURRENTUSER()` is the signed-in user's full name: `[Job Outside Salesperson.Person Name] = CURRENTUSER()`. It only matches a field that holds a person's name.
- **Calculated totals** (aggregates) are `0` when nothing is linked, never empty: `[Total Sell With Tax] = 0`, not `ISEMPTY(...)`.
- **Yes/no** fields take `TRUE` / `FALSE`; an unset one counts as `FALSE`.
- **Account settings:** `SETTING("Default Tax Rate")` on either side compares with an account setting's value.

## Functions

<!-- generated:functions -->
**Rules on a field**

| Function | True when / means | Negative (there is no NOT) |
|---|---|---|
| `ISEMPTY(value)` | True when the field has no value. | `ISNOTEMPTY(…)` |
| `ISNOTEMPTY(value)` | True when the field has a value. | `ISEMPTY(…)` |
| `ISANYOF(value, option, ...)` | True when the field equals any of the listed values. | `ISNOTANYOF(…)` |
| `ISNOTANYOF(value, option, ...)` | True when the field equals none of the listed values. | `ISANYOF(…)` |
| `CONTAINS(text, search)` | True when the text contains the search text (ignoring case). | `NOTCONTAINS(…)` |
| `NOTCONTAINS(text, search)` | True when the text does not contain the search text. | `CONTAINS(…)` |

**Rules on a date**

| Function | True when / means | Negative (there is no NOT) |
|---|---|---|
| `ISANYTIME(date)` | True when the date has any value. | — |
| `BETWEENDATES(date, start, end)` | True when the date falls between two dates. | — |
| `INTHELAST(date, timeSpan)` | True when the date is within the given span before now. | none: `MORETHANAGO([Date], DAYS(n)) OR ISEMPTY([Date])` |
| `INTHENEXT(date, timeSpan)` | True when the date is within the given span after now. | none: `MORETHANFROMNOW([Date], DAYS(n)) OR ISEMPTY([Date])` |
| `MORETHANAGO(date, timeSpan)` | True when the date is further in the past than the given span. | — |
| `MORETHANFROMNOW(date, timeSpan)` | True when the date is further in the future than the given span. | — |
| `BETWEENRANGE(date, fromSpan, toSpan)` | True when the date falls between two offsets from today — negative is past: BETWEENRANGE([Date], DAYS(-30), DAYS(7)). | — |

**Milestones**

| Function | True when / means | Negative (there is no NOT) |
|---|---|---|
| `INMILESTONE("milestone", ...)` | True while the record is in the milestone (or any of the milestones listed). | `NOTINMILESTONE(…)` |
| `INANYMILESTONE("milestone", ...)` | True while the record is in any of the milestones. Same as INMILESTONE. | `NOTINMILESTONE(…)` |
| `NOTINMILESTONE("milestone", ...)` | True while the record is not in the milestone (nor any listed). | `INMILESTONE(…)` |
| `NOTINANYMILESTONE("milestone", ...)` | True while the record is in none of the milestones. Same as NOTINMILESTONE. | `INMILESTONE(…)` |
| `INALLMILESTONES("milestone", ...)` | True while the record is in every one of the milestones. | — |
| `NOMILESTONES("relationship"?)` | True while the record is in no milestone. NOMILESTONES("Customer") checks the related record. | — |

**Workflow steps**

| Function | True when / means | Negative (there is no NOT) |
|---|---|---|
| `HASSTEP("step", ...)` | True while the record has the step (or any of the steps listed). | `NOTHASSTEP(…)` |
| `HASANYSTEP("step", ...)` | True while the record has any of the steps. Same as HASSTEP. | `NOTHASSTEP(…)` |
| `HASALLSTEPS("step", ...)` | True while the record has every one of the steps. | `NOTHASALLSTEPS(…)` |
| `NOTHASSTEP("step", ...)` | True while the record has none of the steps. | `HASSTEP(…)` |
| `NOTHASANYSTEP("step", ...)` | True while the record has none of the steps. Same as NOTHASSTEP. | `HASSTEP(…)` |
| `NOTHASALLSTEPS("step", ...)` | True while the record is missing at least one of the steps. | `HASALLSTEPS(…)` |
| `NOSTEPS("relationship"?)` | True while the record has no step. NOSTEPS("Customer") checks the related record. | — |

**Milestone and step dates (use them where a date goes)**

| Function | True when / means | Negative (there is no NOT) |
|---|---|---|
| `MILESTONEFIRSTENTERED("milestone")` | The date the record first entered the milestone. Use it with a date check: INTHELAST(MILESTONEFIRSTENTERED("Won"), DAYS(30)). | — |
| `MILESTONELASTENTERED("milestone")` | The date the record most recently entered the milestone. | — |
| `STEPFIRSTOCCURRED("step")` | The date the step first occurred on the record. | — |
| `STEPLASTOCCURRED("step")` | The date the step most recently occurred on the record. | — |

**Values (the right side of a comparison, or a span argument)**

| Function | True when / means | Negative (there is no NOT) |
|---|---|---|
| `DATE("yyyy-mm-dd")` | A date: DATE("2026-01-15"), DATE("2026-01-15 14:30"). | — |
| `DAYS(count)` | A number of days. | — |
| `WEEKS(count)` | A number of weeks. | — |
| `MONTHS(count)` | A number of calendar months (relative dates only). | — |
| `YEARS(count)` | A number of calendar years (relative dates only). | — |
| `HOURS(count)` | A number of hours (time span fields). | — |
| `MINUTES(count)` | A number of minutes (time span fields). | — |
| `SECONDS(count)` | A number of seconds (time span fields). | — |
| `TIMESPAN(days, hours, minutes, seconds)` | A time span from parts: TIMESPAN(1, 12) is one day and twelve hours. | — |
| `SETTING("setting")` | The value of an account setting. | — |
| `CURRENTUSER()` | The logged-in user (compared by full name). | — |

**Calendar periods** (one date argument; none has a negative form)

`ISTODAY([Date])` is today · `ISYESTERDAY([Date])` is yesterday · `ISTOMORROW([Date])` is tomorrow · `ISTHISWEEK([Date])` is this week · `ISLASTWEEK([Date])` is last week · `ISNEXTWEEK([Date])` is next week · `ISTHISMONTH([Date])` is this month · `ISLASTMONTH([Date])` is last month · `ISNEXTMONTH([Date])` is next month · `ISTHISQUARTER([Date])` is this quarter · `ISLASTQUARTER([Date])` is last quarter · `ISNEXTQUARTER([Date])` is next quarter · `ISTHISYEAR([Date])` is this year · `ISLASTYEAR([Date])` is last year · `ISNEXTYEAR([Date])` is next year · `ISWEEKTODATE([Date])` is week to date · `ISMONTHTODATE([Date])` is month to date · `ISQUARTERTODATE([Date])` is quarter to date · `ISYEARTODATE([Date])` is year to date

Every other name is unknown: there is no `NOT`, `IN`, `LIKE`, `BETWEEN`, `ISBLANK`, `ISNULL`, `STARTSWITH`, `ENDSWITH`, `TODAY`, `NOW`, `ME`.
<!-- /generated:functions -->

Spans for `INTHELAST`, `INTHENEXT`, `MORETHANAGO`, `MORETHANFROMNOW` and `BETWEENRANGE`: `DAYS(n)`, `WEEKS(n)`, `MONTHS(n)`, `YEARS(n)`. `HOURS`, `MINUTES`, `SECONDS` and `TIMESPAN` are values for Time Span fields only.

## Empty values

A negative rule on the record's own field (`!=`, `ISNOTANYOF`, `NOTCONTAINS`, also `!=` on numbers and dates) **matches records where the field is empty** — never set or cleared, both the same: `[Department] != "Retail"` includes every job with no Department.

Through a relationship, a record with **no linked record does not match** any rule but `ISEMPTY`: `[Job Main Customer.Customer Name] != "Henderson"` leaves out jobs with no customer linked. A linked customer whose name is empty does match.

Folders, reports and milestone criteria read their filters the same way.

| Asked | Write |
|---|---|
| Jobs not in Retail, unset included | `[Department] != "Retail"` |
| Jobs in neither Retail nor Trade, unset included | `ISNOTANYOF([Department], "Retail", "Trade")` |
| Jobs not in Retail, Department set | `[Department] != "Retail" AND [Department] != BLANK` (a pick list: `!= BLANK` means "has a value") |
| Flag isn't "Rush", Flag filled in | `[Flag] != "Rush" AND ISNOTEMPTY([Flag])` |
| Customer isn't Henderson, no customer included | `[Job Main Customer.Customer Name] != "Henderson" OR ISEMPTY([Job Main Customer.CUST_ID])` |
| Not won in the last 30 days, never won included | `MORETHANAGO([Job Won Date], DAYS(30)) OR ISEMPTY([Job Won Date])` (INTHELAST has no negative; a comparison like `MORETHANAGO`, `<` or `>` never matches an empty value) |

Yes/no fields are different: unset counts as `FALSE`.

## Recipes

Each was run with `--count` on a PATH WD account; the names are the PATH WD template's (Job, Department, Job Main Customer, …), so check them against `--catalog` in another account.

| Question | `--logic` |
|---|---|
| Every record | `ISANYTIME([Created Date])` |
| Created last week (Monday–Sunday) | `ISLASTWEEK([Created Date])` |
| Created in the last 30 days | `INTHELAST([Created Date], DAYS(30))` |
| Created in September 2026 | `BETWEENDATES([Created Date], DATE("2026-09-01"), DATE("2026-09-30 23:59"))` |
| Not won in the last 30 days, or never won | `MORETHANAGO([Job Won Date], DAYS(30)) OR ISEMPTY([Job Won Date])` |
| In Lead or Qualified | `INMILESTONE("Lead", "Qualified")` |
| Not in Closed | `NOTINMILESTONE("Closed")` |
| Won at some point / never won | `HASSTEP("Job Won")` / `NOTHASSTEP("Job Won")` |
| Entered Quoting in the last 90 days | `INTHELAST(MILESTONELASTENTERED("Quoting"), DAYS(90))` |
| First reached Quoting last month | `ISLASTMONTH(MILESTONEFIRSTENTERED("Quoting"))` |
| Has a customer linked / has none | `ISNOTEMPTY([Job Main Customer.CUST_ID])` / `ISEMPTY([Job Main Customer.CUST_ID])` |
| Customer's name contains "henderson" | `CONTAINS([Job Main Customer.Customer Name], "henderson")` |
| My jobs (I'm the outside salesperson) | `[Job Outside Salesperson.Person Name] = CURRENTUSER()` |
| Retail or Trade | `ISANYOF([Department], "Retail", "Trade")` |
| Department not set | `[Department] = BLANK` |
| Not Retail, unset included | `[Department] != "Retail"` |
| Name contains "window" | `CONTAINS([Job Name], "window")` |
| Total between $5,000 and $50,000 | `[Total Sell With Tax] >= 5000 AND [Total Sell With Tax] <= 50000` |
| Deposit collected / not collected | `[Deposit Collected] = TRUE` / `[Deposit Collected] = FALSE` |
| Lead jobs in Retail or Trade | `INMILESTONE("Lead") AND ([Department] = "Retail" OR [Department] = "Trade")` |

With flags:

| Question | Command |
|---|---|
| How many, archived included | `find-records --bo-name Job --logic 'ISTHISYEAR([Created Date])' --count --include-archived` (archived jobs can change a count a lot: 82 → 347 on a test account) |
| Totals per department | `find-records --bo-name Job --logic 'ISTHISYEAR([Created Date])' --group-by Department --sum "Total Sell With Tax"` |
| Jobs per month | `find-records --bo-name Job --logic 'ISTHISYEAR([Created Date])' --group-by "Created Date by month"` |
| Newest first, chosen columns | `find-records --bo-name Job --logic 'INMILESTONE("Lead")' --columns "Job Name, Department, Job Main Customer" --sort "Created Date desc"` |
| Every match, not one page | `find-records --bo-name Job --logic 'NOTINMILESTONE("Closed")' --all` |

## Common mistakes

The left column is what fails (or misleads); "What happens" is the parser's own message. Generated from a tested list, so the messages are exact.

<!-- generated:mistakes -->
| Meant | Written | What happens | Write instead |
|---|---|---|---|
| a field of the linked customer | `[Job Main Customer] = "Henderson"` | `Unknown field "Job Main Customer" — [Job Main Customer] is a relationship, not a field — name a field on it, e.g. [Job Main Customer.Customer Name]; "has a linked record" is ISNOTEMPTY([Job Main Customer.CUST_ID])` | `[Job Main Customer.Customer Name] = "Henderson"` |
| a linked field, by business object name | `[Customer.Customer Name] = "Henderson"` | `Unknown field "Customer.Customer Name" — no field or relationship path by that name — "Customer" is a business object, not a relationship — name the relationship, e.g. [Job Main Customer.Customer Name]` | `[Job Main Customer.Customer Name] = "Henderson"` |
| jobs with a customer linked | `ISNOTEMPTY([Job Main Customer])` | `Unknown field "Job Main Customer" — [Job Main Customer] is a relationship, not a field — name a field on it, e.g. [Job Main Customer.Customer Name]; "has a linked record" is ISNOTEMPTY([Job Main Customer.CUST_ID])` | `ISNOTEMPTY([Job Main Customer.CUST_ID])` |
| not in a milestone | `NOT INMILESTONE("Lead")` | `Unknown function "NOT" — there is no NOT; use NOTINMILESTONE(...) instead of NOT INMILESTONE(...)` | `NOTINMILESTONE("Lead")` |
| never had a step | `!HASSTEP("Job Won")` | `Unexpected character "!" — there is no ! (NOT); use NOTHASSTEP(...) instead of !HASSTEP(...)` | `NOTHASSTEP("Job Won")` |
| never had a step | `HASSTEP("Job Won") = FALSE` | `Unexpected "=" — functions aren't compared to TRUE/FALSE; use NOTHASSTEP(...) instead of HASSTEP(...) = FALSE` | `NOTHASSTEP("Job Won")` |
| text contains | `[Job Name] CONTAINS "window"` | `CONTAINS is a function, not an operator — write CONTAINS([Job Name], "window")` | `CONTAINS([Job Name], "window")` |
| one of several values | `[Department] IN ("Retail", "Trade")` | `There is no IN operator — write ISANYOF([Department], "Retail", "Trade")` | `ISANYOF([Department], "Retail", "Trade")` |
| one of several values | `ISANYOF([Department], ("Retail", "Trade"))` | `Values go straight into the function, no parentheses — e.g. ISANYOF([Department], "Retail", "Trade")` | `ISANYOF([Department], "Retail", "Trade")` |
| in the last 30 days | `[Created Date] INTHELAST 30 DAYS` | `INTHELAST is a function, not an operator — write INTHELAST([Created Date], DAYS(30))` | `INTHELAST([Created Date], DAYS(30))` |
| in the last 30 days | `INTHELAST([Created Date], 30)` | `Expected DAYS(n), WEEKS(n), MONTHS(n) or YEARS(n) — write the span as DAYS(30)` | `INTHELAST([Created Date], DAYS(30))` |
| in the last 24 hours | `INTHELAST([Created Date], HOURS(24))` | `Expected DAYS(n), WEEKS(n), MONTHS(n) or YEARS(n) — there is no window shorter than a day; use DAYS(1), or compare with a time: [Created Date] >= DATE("2026-01-15 08:00")` | `[Created Date] >= DATE("2026-11-10 14:00")` |
| a pick list with no value | `ISEMPTY([Department])` | `ISEMPTY can't be used with [Department] (a selection field) — use [Department] = BLANK` | `[Department] = BLANK` |
| every record (a date that is set) | `[Created Date] != BLANK` | `BLANK is only for pick lists — use ISANYTIME([Created Date])` | `ISANYTIME([Created Date])` |
| a date that is set | `ISNOTEMPTY([Job Won Date])` | `ISNOTEMPTY can't be used with [Job Won Date] (a date field) — use ISANYTIME([Job Won Date])` | `ISANYTIME([Job Won Date])` |
| after a date | `[Created Date] > "2026-10-01"` | `Expected a date such as DATE("2026-01-15") — write DATE("2026-10-01") (the account's time zone)` | `[Created Date] > DATE("2026-10-01")` |
| after a date | `[Created Date] > DATE("10/01/2026")` | `Dates look like DATE("2026-01-15") or DATE("2026-01-15 14:30")` | `[Created Date] > DATE("2026-10-01")` |
| today | `[Created Date] = TODAY()` | `There is no TODAY() or NOW(); use ISTODAY([Created Date]), INTHELAST([Created Date], DAYS(30)), MORETHANAGO([Created Date], DAYS(30)), or compare with DATE("2026-01-15")` | `ISTODAY([Created Date])` |
| a field | `Department = "Retail"` | `"Department" isn't a function — field names go in [ ], e.g. [Department] = "Retail"` | `[Department] = "Retail"` |
| text | `[Department] = 'Retail'` | `Unexpected character "'" — text and names go in straight double quotes: "Retail"` | `[Department] = "Retail"` |
| text | `[Department] = Retail` | `Text goes in double quotes — [Department] = "Retail"` | `[Department] = "Retail"` |
| equals | `[Department] == "Retail"` | `Compare with a single =, e.g. [Department] = "Retail"` | `[Department] = "Retail"` |
| both rules | `[Department] = "Retail" && [Deposit Collected] = TRUE` | `Join rules with AND (not & or &&)` | `[Department] = "Retail" AND [Deposit Collected] = TRUE` |
| AND with OR | `INMILESTONE("Lead") AND [Department] = "Retail" OR [Department] = "Trade"` | `Use parentheses when mixing AND and OR, e.g. A AND (B OR C)` | `INMILESTONE("Lead") AND ([Department] = "Retail" OR [Department] = "Trade")` |
| over $5,000 | `[Estimated Sell Price] > $5,000` | `Unexpected character "$" — numbers have no currency symbols or thousands separators: write 5000` | `[Estimated Sell Price] > 5000` |
| a percentage | `[Tax Rate] = 8.25%` | `Unexpected character "%" — a percent field holds the number shown: 50% is 50` | `[Tax Rate] = 8.25` |
| a checkbox | `[Deposit Collected] = "Yes"` | `Yes/no values are TRUE and FALSE without quotes — [Deposit Collected] = TRUE` | `[Deposit Collected] = TRUE` |
| a milestone | `INMILESTONE("Won")` | `Unknown milestone "Won" — there is a step "Job Won": HASSTEP("Job Won")` | `INMILESTONE("Qualified")` (a name from --catalog) |
| text with no value | `[Flag] = ""` | `"" is no value, so the rule would match every record — for an empty [Flag] use ISEMPTY([Flag]); for one with a value use ISNOTEMPTY([Flag])` | `ISEMPTY([Flag])` |
| customer isn't Henderson, no customer included | `[Job Main Customer.Customer Name] != "Henderson"` | **No error, wrong answer:** leaves out jobs with no customer linked: through a relationship, no linked record matches no rule but ISEMPTY (see "Empty values") | `[Job Main Customer.Customer Name] != "Henderson" OR ISEMPTY([Job Main Customer.CUST_ID])` |
| a price of 5,000 | `ISANYOF([Estimated Sell Price], 5,000)` | `Numbers have no thousands separators — write 5000 (separate values with ", ")` | `ISANYOF([Estimated Sell Price], 5000)` |
| won before now | `MORETHANAGO([Job Won Date], DAYS(0))` | `MORETHANAGO needs a span of at least 1, e.g. MORETHANAGO([Job Won Date], DAYS(1)); for any date at all use ISANYTIME([Job Won Date])` | `MORETHANAGO([Job Won Date], DAYS(1))` |
| my jobs | `[Deposit Collected] = CURRENTUSER()` | `CURRENTUSER() is the signed-in user's name, so it never matches [Deposit Collected] (a yes/no field) — compare a field that holds a person's name, e.g. [<Relationship>.<Name field>] = CURRENTUSER()` | `[Job Outside Salesperson.Person Name] = CURRENTUSER()` |
| a pick list value | `[Department] = 1` | `1 is not an option of [Department] — its options are "Retail" or "Trade"` | `[Department] = "Retail"` |
| a field of the linked customer | `[Customer Name] = "Henderson"` | `Unknown field "Customer Name" — did you mean [Job Main Customer.Customer Name]?` | `[Job Main Customer.Customer Name] = "Henderson"` |
| a field | `[Departmnt] = "Retail"` | `Unknown field "Departmnt" — did you mean [Department]?` | `[Department] = "Retail"` |
<!-- /generated:mistakes -->

## When a name isn't found

`Unknown field "X"` doesn't say why. In order:

The message adds the likely fix (only from names this user can search on): a relationship used as a field ("is a relationship, not a field — name a field on it, e.g. …"), a business object name used as a path ("name the relationship, e.g. …"), a relationship followed the wrong way ("write the rule on … instead"), or the closest names ("did you mean …?"). If it doesn't:

1. **Is it a relationship?** A relationship alone is not a field: name a field on it, `[Job Main Customer.Customer Name]`.
2. **Is the path by relationship name?** `[Customer.…]` uses the business object's name; `--catalog` lists the relationship names (`Job Main Customer`).
3. **Does it go the right way?** Relationships are followed only from the record that holds the link; ask from the other business object.
4. **Is it spelled as in `--catalog`?** Case doesn't matter, everything else does. A name `--catalog` doesn't list can't be searched on; say so plainly.
5. **Just created in the Designer?** The CLI reloads its metadata once after a parse error, so a brand-new field is found on that retry; a field renamed by someone else this session may need `clear-cache`.

`Unknown milestone "X"` / `Unknown step "X"`: milestones and steps are different lists in `--catalog` (a "Job Won" step is not a milestone).

## Reading the output

```
82 Jobs match: INANYMILESTONE("Lead", "Qualified")
id                       | name                           | milestones | Department
-------------------------|--------------------------------|------------|-----------
6a9056455ff33756afba5084 | 26-0050: Henderson, Patricia   | Lead       | Retail
6a9066405ff33756afba8858 | 26-0051: SD PostWipe Retail 01 | Qualified  | Trade
[1–25 of 82 records — --page 2 for more, --all for every one]
```

- The first line echoes the formula **as PATH read it**: `25 Jobs match: INANYMILESTONE("Lead", "Qualified")`. Read it back: `INMILESTONE` with several names prints as `INANYMILESTONE`, `HASSTEP` with several as `HASANYSTEP`, `TIMESPAN(0, 2, 30)` as `MINUTES(150)`, and `""` as `?` (a rule that was left out).
- The table: `id`, `name` (the record's identifier; `(archived)` after it with `--include-archived`), `milestones`, then the business object's own fields the formula uses (or `--columns`), 25 a page (`--per-page` up to 100, `--page`, `--all`). The footer says which records of how many and how to get the rest. Long cells end in `…`.
- Cells are **display text** (a person shows as `Todd Demo (todd@example.com)`, a percent as `8.250%`), which isn't always what a formula compares (`[… .Person Name] = "Todd Demo"`, `[Tax Rate] = 8.25`).
- `--count` prints the first line alone; `--group-by` prints it, then one row per group with `Count` and each total.
- Each record's `id` opens it: `get-record --record-id <id>`.

## Flags

| Flag | Takes | Notes |
|---|---|---|
| `--bo-name <name>` / `--bo-id <id>` | the business object, singular or plural, any case | required |
| `--logic '<formula>'` | the formula | required unless `--catalog` |
| `--catalog` | — | what this user can search on: fields with type and options, relationships, milestones, steps, each with designer context; `--logic` is ignored |
| `--count` | — | only the number (ignored with `--group-by`) |
| `--include-archived` | — | archived records are left out otherwise |
| `--columns "<a>, <b>"` | the business object's own fields, or a relationship name (shows the linked record) | `Rel.Field` isn't accepted here (use `--group-by`); ignored with `--count` |
| `--sort "<field> [desc]"` | one field of the business object itself | no lists, no linked fields, no `-field` |
| `--page <n>`, `--per-page <n>` | default 1 and 25; at most 100 a page | there is no `--limit` |
| `--all` | — | every match, 100 a request |
| `--group-by "<a>, <b>"` | up to 3: fields, `Rel.Field`, `"<date field> by day\|week\|month\|quarter\|year"`, `milestones` | groups instead of a list; a bare relationship is refused |
| `--sum`, `--avg`, `--min`, `--max "<fields>"` | number, currency, percent or time span fields | need `--group-by` |
| `--top <n>` | — | the first n groups after sorting (`--sort "Count desc"`, `--sort "<field> (total) desc"`) |
| `--account <id>`, `--fields <columns>`, `--quiet` | — | global options (`--quiet`: record ids only) |

## Quoting the formula in a command line

The formula is one argument. Wrap it in **single quotes**, so the double quotes inside stay: `--logic '[Department] = "Retail"'`.

- Unquoted, only the first word is read (`--logic [Department] = "Retail"` searches for `[Department]` and fails with `Finish the rule…`).
- Double quotes around the whole formula eat the inner ones (`--logic "[Department] = "Retail""` becomes `[Department] = Retail`).
- An **apostrophe** inside a single-quoted formula ends it early: `'CONTAINS([Customer Name], "O'Brien")'` fails with `Text is missing its closing quote`. Write it as `'CONTAINS([Customer Name], "O'\''Brien")'`, or use double quotes outside and `\"` inside: `"CONTAINS([Customer Name], \"O'Brien\")"`.
- Newlines between rules are fine.

## find-records and parse-filter

`parse-filter` checks a formula without running it, but it doesn't read names the way a search does, doesn't allow milestone and step dates without `--allow-entry-dates`, and nothing is checked by the server. `find-records … --count` is the faithful check for a search.
