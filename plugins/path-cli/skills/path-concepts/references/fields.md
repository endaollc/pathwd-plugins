# Fields

A field is one piece of information on a record: a customer's name, a quote total, a job's install date. This page covers the kinds of fields users see, which ones can be typed into, and how fields are organized on the Record Viewer.

Every value stored on a record lives in a field. The set of fields on a record depends on its business object: Jobs have Job fields, Invoices have Invoice fields. Designers decide which fields exist; users fill them in or see them calculated.

## Kinds of fields

These are the names users see.

**Typed-in data**

- **Text**: a single line, such as a name or address line.
- **Long Text**: several lines of text, for descriptions and similar.
- **Number**: a plain numeric value, such as a quantity.
- **Currency**: a money amount, shown with a currency symbol and usually two decimals, such as $4,250.00.
- **Percent**: a percentage, such as a deposit percentage.
- **Boolean** (yes/no): a true/false toggle, like a checkbox.
- **Date & Time**: a date with an optional time.
- **Email**, **Phone Number**, **URL**: text that is treated as an email address, phone number or web link.
- **Notes**: an internal notes area.
- **Appointment**: a calendar event with start and end date and time, an all-day option, a multi-day option and a description. Appointments tie into the calendar.
- **Time Span**: a duration such as hours or days.

**Selection**

A Selection field is a dropdown where the user picks an option, and some allow several. The options can come from three places:

- a fixed list the designer typed in (for example Department: Residential, Commercial, Industrial);
- the records of another business object (pick a Customer or Product from the existing ones);
- the records in one of another object's folders.

**Computed fields (cannot be typed into)**

These are worked out by PATH from other data. You can see their values but can't type over them.

- **Text Expression**: text built from other fields, such as a display name made from customer name and job name.
- **Number Expression** (also called a Calculation): a number computed from other fields, such as Quote Total = quantity times unit price.
- **Time Span Expression**: a duration computed from other fields.
- **Auto Number**: a number that counts up by itself as records are created, such as invoice numbers.
- **Aggregate fields** (Aggregate Number, Aggregate Time Span, Aggregate Text, and a count): totals or summaries rolled up from linked records, such as a Job's total of all its Quotes. See the relationships page.
- **Identifier**: the record's built-in label.

Whenever PATH computes a value, trying to write to it has no effect. If a user wants a computed value to change, change what it depends on, such as the quantity or price fields, and PATH recalculates. If they want to change the formula itself, that is a designer's job. Computed values update when the record is saved; see the saving page.

## Required, hidden and read-only

- **Required**: the field must have a value before the record can be saved or a form can be submitted. Pathway screens often mark required fields.
- **Hidden**: the designer hid the field from the Record Viewer. It still holds data and can be used elsewhere, for example in a folder column.
- **Visibility by person or role**: designers can limit some fields to certain users or roles. Two users may therefore see different fields on the same record.
- **Highlighted**: a field can be shown in a color to draw attention to it.
- **Default value**: a new record may start with a field already filled in.
- **Read-only in practice**: computed fields (above), and system fields like Created Date, Modified Date and Last Accessed.

If a user says they can't edit a field, the usual reasons are that it's computed, a system field, or hidden or limited by role.

## Formats

Some fields display in a set format chosen by the designer:

- Currency shows a symbol, commas and a set number of decimal places.
- Numbers can show commas and decimals.
- Dates and times can show in a chosen style.
- Time spans show in hours, days and so on.

The format only changes how the value looks. When entering values, give them in a natural form ("5000", "10/14/2026", "Residential") and PATH handles display. For a Selection, pick from the options the field offers.

## Field groups: sections on the Record Viewer

Fields are organized into field groups, which appear as sections on the Record Viewer, such as "Details", "Financial Info", "Product Totals" or "Scheduling". A new business object starts with one group called Details. Field groups exist only to make fields easy to find. They don't change what a field does.

The order of groups and fields within them is set by the designer. This order also affects the columns shown in folders that use the default column layout.

The relationships section on a Record Viewer looks a bit like a field group but holds the controls for linking records.

## Fields elsewhere in PATH

Fields do more than hold data:

- **Folders and reports** filter, sort and show columns by field values. A field may be set to show as a folder column.
- **Search**: designers choose which fields are included in quick search and advanced search.
- **Milestones** use field values as criteria, for example "Deposit Paid = Yes".
- **Document templates** (quotes, contracts, invoices) pull in field values.
- **Fields from linked records**: a Quote can show fields that belong to its Job (see relationships).

## Talking about fields with users

Use the field's name as the user sees it: "Install Date", "Quote Total", "Department". If two fields sound alike, ask which one is meant. A field's name is unique within its business object, so "Name" on Customers is not the same as "Name" on Jobs.
