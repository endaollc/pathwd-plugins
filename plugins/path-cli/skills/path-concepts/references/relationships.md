# Relationships and Linked Records

Records in PATH are connected to each other: a Quote belongs to a Job, a Job belongs to a Customer. This page explains how linking works, what the user sees, and how totals and counts roll up from linked records.

## Linking records

A relationship is a defined connection between two business objects, for example Quotes to Jobs, or Jobs to Customers. Linking is connecting two specific records across a relationship: "link Quote 5678 to Job 1234". A designer creates the relationships; users and pathways link and unlink records.

More than one relationship can exist between the same two business objects. A Job might have both a "Customer" and a "Billing Customer" relationship to Customers, and a Job could be linked to another Job as a sub-job, because a business object can relate to itself.

## Direction in business terms

Each relationship points one way, but it can be read in both directions.

- **The side that "belongs to"**: a Quote belongs to a Job. From the Quote you look "up" to the Job it is part of.
- **The other side "has many"**: a Job can have many Quotes. From the Job you look "down" to the Quotes that belong to it.

Typically the "many" side holds the link: each Quote points to the Job it belongs to, and one Job collects all its Quotes. The same pattern applies to other pairs, for example many Jobs pointing to one Customer, where the setup follows it.

In short: "a Quote belongs to a Job; a Job can have many Quotes." When the user says "the Quotes on this Job" they are looking down from the Job; when they say "the Job for this Quote" they are looking up.

## The relationships section on the Record Viewer

On a record's Record Viewer, the relationships it points to (the ones it "belongs to") have a section that looks like a field group. It shows which record is linked and gives controls to link a different one, or to remove the link. If you open a Quote that's linked to Job 1234, that section shows Job 1234 and lets you change it.

If nothing is linked yet, the section is empty and the user can pick a record to link. Milestones and later pathway steps can depend on a link being in place.

Designers can limit which users or roles can see a relationship.

## Relationship fields: borrowed information

A relationship field brings a field from the linked record onto this record. It is the same information, shown in another place.

Example: a Quote is linked to a Job. The designer chooses to show the Job's Customer Name, Job Address and Job Type on the Quote. Those appear in the Quote's relationship section, always showing the Job's current values, without anyone retyping them. If the Job's address changes, the Quote shows the new address.

Important points:

- You change the value on the record that owns it. To change the Job Address shown on a Quote, edit the Job (or the field on the Job's own Record Viewer).
- Relationship fields can be used much like the record's own fields: in milestone criteria, folder filters and columns, reports, calculations, and document templates. A quote document can print the Job's customer name.
- Relationship fields can be shown as columns in lists. If a user asks for "Quotes with their Job's customer", that is a relationship field.
- A relationship field exists only while the link exists. If a Quote isn't linked to a Job, its Job fields are empty.

## Related-record tables and tabs

Looking from the other side, a Job's Record Viewer can have a tab that lists every Quote linked to it, such as a "Quotes" tab, along with the Invoices, Purchase Orders or Services tabs a designer has set up. Each tab is a table of the linked records with the columns the designer chose. Depending on setup, the tab may let the user:

- add a new linked record (a new Quote straight from the Job);
- open a row to go to that record;
- archive a row from the table.

Some relationships can also pull in the other record's contacts, files or tasks, so a Job can show tasks or files from its linked records.

In pathways, steps can work with these inversely linked records, for example "edit the line items on this Quote" or "all Invoices for this Job".

## Totals and counts from linked records (aggregates)

An aggregate is a field on a record that summarizes the records linked to it. Examples:

- a Job's **Quote Total**: the sum of the totals of all its Quotes;
- a Customer's **Open Invoice Balance**: the total of balances on its Invoices;
- a Job's **Number of Purchase Orders**: a count of linked records;
- a total duration or a combined text from linked records.

Key points for users:

- Aggregate fields are computed, so you can't type into them. To change the total, change the linked records.
- They update when linked records are saved, linked, unlinked or archived. Sometimes this lands a moment after the save rather than instantly.
- Archived records don't count. Archiving a Quote removes its amount from the Job's total.
- Linking a Quote to a Job adds it to that Job's totals; unlinking removes it, and the Quote's old Job is recalculated too.
- They only look at records that are directly linked via a specific relationship, so a Job's total of "Quotes" counts Quotes and nothing else.

## Linking and unlinking: what the user should expect

- Linking or unlinking affects both records: the one holding the link (its borrowed fields change) and the one linked to (its totals, counts and tables change).
- Relationship fields shown on a record appear when linked and go blank when unlinked.
- Relinking to a different record moves the Quote's amounts from the old Job's totals to the new one's.

## Handy phrases

- "Link a Quote to a Job" = connect two records.
- "Belongs to" = the record you look up to.
- "Related Quotes tab" = the table on the Job of all linked Quotes.
- "Job's Customer Name on the Quote" = a relationship field.
- "Quote Total on the Job" = an aggregate.
