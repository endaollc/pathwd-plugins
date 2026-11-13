# PATH WD

This file explains PATH WD, the standard setup of PATH for window and door dealers, in the business terms a dealer's staff use. It covers the business objects they work with (Jobs, Services, Quotes, Purchase Orders, Appointments, invoices and payments, and the warehouse and shipping objects), how those objects connect, and what the order tracking, receiving, warehouse, scheduling, loading and delivery screens are doing. Read it to understand what a user means and what a pathway screen is asking.

## What PATH and PATH WD are

**PATH** is a highly customizable ERP system. Each account defines its own business objects (Jobs, Customers, Invoices and so on), fields, relationships, milestones, workflow steps, tasks, folders, reports and pathways, so the system matches how that business works. PATH also connects to standard accounting software.

**PATH WD** is PATH set up for window and door dealers. It is the "off the shelf" configuration: a pre-built set of business objects, fields, relationships, workflow, actions, roles, folders, reports and pathways for the window and door industry. Every user of this plugin is on a PATH WD account.

Accounts can differ slightly from the standard setup. A business object or field may have a different name, an account may have extra fields, objects or pathways, and milestone and step names can vary. **The current account's own business objects, fields and pathways are always the final word.** Use this file to understand the intent, then confirm the details in the account.

### What PATH WD runs for a dealer

- **Sales:** lead tracking, quoting, customer and contact management.
- **Orders:** turning sold work into Jobs, product line items and vendor orders (Purchase Orders).
- **Purchasing and order tracking:** following what has been ordered from manufacturers, and what is shipping, through the manufacturer's Order Manager (OM) data.
- **Receiving, warehouse and logistics:** receiving product off trucks or against a PO, storing it in warehouse bins, and tracking every barcoded package.
- **Scheduling:** Appointments for deliveries, installations, sales visits, service inspections, service field work and customer pickups.
- **Delivery and installation:** loading delivery trucks, delivering to the jobsite, and recording what was delivered.
- **Service:** service work orders, covering warranty management, maintenance and customer service.
- **Money:** sales orders, invoices, payments, and keeping all of these in sync with the dealer's accounting system (QuickBooks).

## How users talk about PATH

- Users talk about specific things: "this Job", "Job 1234", "the Smith quote", "Invoice 5521", "the truck from Monday". They rarely say "record". In PATH, each of these is a **record** of a **business object**: a specific Job is a record of the Jobs business object.
- A record is opened in the **Record Viewer**, which shows its **fields** (grouped into field groups), its **linked records** (other records it is connected to, often shown as tabs), and its activity (messages, emails, files, tasks).
- **Folders** are saved lists of records of one business object, filtered by criteria (for example "Leads", "Deposit Needed" or "My Jobs In Full Quote Out").
- **Milestones** mark where a record is in its life (for example Lead, Full Quote Out, Paid In Full). **Workflow steps** are the individual events that happen to a record (for example "Delivery Scheduled"). **Tasks** are to-dos assigned to people.
- **Pathways** are guided, step-by-step processes a user runs on a record, such as selecting the winning quote, scheduling an appointment or receiving a Purchase Order. Pathway screens ask questions, show lists to pick from, or ask for barcode scans or quantities.
- **Archived** records are hidden by default. They do not appear in search, folders or reports unless set up to, and they do not count toward totals on other records.
- **People** are the users who log into PATH, one People record per user. People records are linked throughout PATH to show who owns, is assigned to, or did something (for example, who ran a scan session).

## The business objects

### Customers, contacts and addresses

- **Customers**: the businesses or households buying from the dealer. A Customer has a name, phone, email, website, Customer Type, payment Terms, Lead Source and notes.
- **Contacts**: individual people, with name, job title, email and phones. Salespeople are also linked as Contacts on Jobs, Services and Invoices.
- **Addresses**: locations, such as job (site) and billing addresses.

### Sales and jobs

- **Jobs**: the dealer's central record for a project or order, from lead through quoting, ordering, receiving, delivery, installation, invoicing and payment. Jobs have a Job Number and Job Name.
- **Quotes**: sales quotations and estimates for a Job.
- **Quote Line Items** (Quote Items): the individual lines on a Quote.
- **Job Items**: the individual product lines on a Job (or Service): the windows, doors, hardware and other items that are ordered, received, scheduled and delivered. Some Job Items are fee or accounting lines rather than physical products (see "OM vs non-OM job items").
- **Sales Orders**: the core sales transaction.

### Service

- **Services**: service-based work orders, a separate kind of work from a Job, with their own Job Items, Purchase Orders, Appointments, receiving, invoices and payments.

### Purchasing and vendors

- **Vendors**: the suppliers and manufacturers the dealer buys from, and the source of Order Manager order data.
- **Purchase Orders (POs)**: the dealer's orders to vendors. A PO records whether a deposit was received, and can be sent to accounting as a vendor bill.

### Scheduling

- **Appointments**: scheduled calendar events such as deliveries and installations. Each Appointment has an **Appointment Category** that says what kind of visit it is.
- **Appointment Jobs**: connects an Appointment to the Jobs and Services it covers, so one Appointment can cover several, and one Job can have many Appointments.
- **Appointment Items**: the specific Job Items (and quantities) scheduled on an Appointment, meaning what is going on this delivery or install.

### Money

- **Invoices**: billing documents, each with an unpaid balance.
- **Payments**: money received, with the payment form (method) and reference number.
- **Applied Payments**: how much of a Payment is applied to which Invoice (or Sales Order).

### Order Manager (OM) data from manufacturers

These records come into PATH automatically from the manufacturer's Order Manager system. Users rarely create them by hand.

- **Orders** and **Order Line Items**: background copies of the manufacturer's orders and their lines. Users do not normally work with them directly. PATH uses them to create and keep up to date the records users do work with: Purchase Orders and Job Items.
- **Receiving Trucks**: incoming shipments from the manufacturer to the dealer.
- **Receiving Truck Stops**: the stops on a truck's route. Users receive product one stop at a time.
- **Receiving Truck Line Items**: the product lines at each stop.
- **Tracking Items**: the individual physical packages (also called "pieces"), each with its own barcode label. These are what get scanned during receiving, bin check-in and check-out, loading and delivery.
- **Order Trucks**: link trucks and stops to the orders, Jobs, Services and POs they carry.

### Receiving

- **Receiving Histories**: one record per receiving event; its totals show how much has been received.
- **Receiving Items**: the item-level quantities received in a receiving event, each linked to a Job Item.

### Warehouse

- **Bin Locations**: a whole physical warehouse.
- **Bins**: individual storage spots inside a Bin Location. Each Bin has its own barcode so it can be scanned quickly. A Bin can be marked as a **Pallet**, which is a bin that moves as one unit when loading and delivering.
- **Bin Items**: "this Job Item's packages are in this Bin", one per Job Item per Bin.
- **Appointment Bin Items**: connect Bin Items to the Appointment they are being pulled for.

### Scanning

- **Scan Sessions**: one record each time someone runs a scanning process, with the session type, the user, and the Job, PO, truck, stop, bin or Appointment it was for.
- **Scan Session Actions**: one line per scan (or manually entered quantity) within a session.

### Accounting support records

- **Integration Items**: a log of each attempt to send something to, or bring something back from, the accounting system (for example "customer created", "sales order created", "PO created", "bill created"). Each shows whether it succeeded, a message, and an import status of Processing, Complete or Failed.
- **Admin Items**: account-level settings, such as whether the QuickBooks integration is enabled and the list of payment methods.

## How everything connects

Read "belongs to" as "is linked to one". The "many" side is usually shown as a tab on the parent's Record Viewer.

**Customers and people**
- A Customer has Contacts and Addresses.
- A Job belongs to a main Customer. It has a job address, a billing address, a main contact, an invoicing contact and an outside salesperson.
- A Service belongs to a main Customer. It has a service address, a billing address, a main contact, a caller and an original salesperson.

**Sales**
- A Job can have many Quotes, and each Quote has Quote Line Items.
- A Job has many Job Items. A Service can also have Job Items.
- A Job or Service can link to a Sales Order.
- A Job has a Primary Vendor. Job Items and Quote Line Items each link to their Vendor.

**Purchasing and OM**
- A Purchase Order belongs to one Job or one Service, never both. A Job or Service can have many POs.
- Job Items are ordered on Purchase Orders.
- A manufacturer Order relates to a PO, and its Order Line Items relate to Job Items.
- A Receiving Truck has Stops, each Stop has Truck Line Items, and each Truck Line Item has Tracking Items (packages).
- Each Truck Line Item relates to a Job Item, so each Job Item's barcoded packages can be traced back to the truck and stop that brought them.
- Order Trucks tie trucks and stops to the Orders, Jobs, Services and POs on them.

**Receiving and warehouse**
- A Receiving History belongs to a Job, Service or PO. It may also link to the Truck Stop it came off, and it has Receiving Items, one per Job Item received.
- A Bin belongs to a Bin Location (warehouse).
- A Bin Item ties one Job Item (and its Job or Service) to one Bin. The Tracking Items in that bin belong to the Bin Item.

**Scheduling**
- An Appointment covers one or more Jobs and/or Services through Appointment Jobs.
- An Appointment has Appointment Items, which are the Job Items and quantities scheduled on it.
- A scheduled Tracking Item (package) links to its Appointment Item and directly to the Appointment.
- Appointment Bin Items tie the bins being pulled from to the Appointment.

**Money**
- An Invoice belongs to a Job or a Service, and may link to a Sales Order.
- A Payment belongs to a Job or a Service and to a Customer.
- An Applied Payment ties one Payment to one Invoice (or Sales Order).
- A Job shows running money totals from all of these: quote totals, sales order totals and unpaid amounts, invoice totals and unpaid amounts, payments recorded, payments applied, unapplied payments, and the available payment amount.

## Lifecycle of a Job

A Job moves through **milestones**, the stages users see on the Job and filter folders by. PATH works out milestones from the Job's workflow steps and field values; nobody sets them by hand. A Job is usually in **several milestones at once**, for example "Ordering", "Deposit Collected" and "Unpaid Invoices", because each tracks a different side of the Job.

The names below are the standard PATH WD milestones. An account may have renamed, added or retired a few, so the account's own milestone list is the final word.

1. **Lead.** The Job is created for a Customer, with a job address, contacts, a salesperson, a Department and a Lead Source. Milestones: **Lead**, **Qualified**, **Sales Visit Scheduled** (a "Retail Sales Visit" Appointment). **Need Customer**, **Need Contact** and **Need Address** flag a Job that is missing one of those.
2. **Quoting.** One or more Quotes are created on the Job, each with Quote Items. The quote types are Quick Quote, Full Quote, Final Contract, Commercial Bid and Quick Contract. Milestones: **Quoting**, **Quick Quote Out**, **Full Quote Out**, **Final Contract Out**. When the customer accepts, one Quote is chosen as the winning quote (the "Select As Winning Quote" pathway), and its items carry forward into the order.
3. **Won, lost, on hold or cancelled.** A won Job records a Job Won Date and moves on to the deposit and ordering stages. Otherwise it may be **Lost** (with a Lost Category and explanation), **Put On Hold** (with an explanation) or **Job Cancelled**.
4. **Deposit.** Milestones: **Deposit Needed** and **Deposit Collected**. A salesperson can ask to skip the deposit: **Deposit Bypass Requested**, then **Deposit Bypass Approved** or **Deposit Bypass Denied**.
5. **Preparing and ordering.** Job Items are ordered from vendors on Purchase Orders. Milestones: **Preparation**, **Shop Drawings Needed**, **Installation Worksheet Needed**, **Shop Work Needed**, **Ready to Order**, **Ordering**, **Awaiting Acknowledgments** (waiting for vendors to confirm the orders). Once the manufacturer's Order Manager data arrives, the Job shows an OM order status.
6. **Receiving.** Product arrives on Receiving Trucks and is received by scanning packages, by receiving against the PO, or by checking packages straight into bins. Milestones: **Awaiting Receiving**, **Partially Received**, **All Items Received**.
7. **Scheduling and fulfillment.** Delivery and installation Appointments are created and the Job Items are scheduled onto them. Milestones:
   - **Fulfillment** and **Need to Schedule Installation**;
   - **Delivery Scheduled**, **Installation Scheduled**, **Delivery & Installation Scheduled**, **Screen Install Scheduled** and **Closeout Scheduled**;
   - **Customer Pickup Scheduled** and **Awaiting Customer Pickup**;
   - **Partially Fulfilled** and **All Items Fulfilled**.
8. **Billing.** Invoices are created and Payments recorded and applied to them. Milestones: **Need to Invoice**, **Unpaid Invoices**, **Paid In Full**, and **Bad Debt - Written Off** for a written-off balance.
9. **Closed.** The Job is finished: **Closed**.

Alongside these:
- **Accounting:** **Waiting for Accounting Sync** and **Accounting Sync Failed** show whether the Job has reached the accounting system (see Accounting integration).
- **Commercial work:** **Comm. Job** and **Waiting on Comm PO** mark Jobs on Commercial Projects.
- **Imported** marks Jobs brought in by import, and **Showroom Display** marks Jobs for showroom product.

**Non-conformance (NC).** A Job can also be flagged as non-conformance: a problem Job that needs review and approval. It has an NC count, who was responsible, whether it was approved, and when it was reviewed.

## Lifecycle of a Quote

A Quote belongs to a Job, a Project or a Service and holds Quote Items. Its milestones follow the quote document:
- **Quote Doc Not Yet Generated**, **Quote Document Generated** and **Ready to Send**;
- **Quick Quote Sent Out**, **Full Quote Sent Out**, **Commercial Bid Sent** and **Service Quote Sent Out**;
- **Sending E-Signature...** and **E-Signature Failed**;
- **Quote Signed**, **Service Quote Signed** and **Approval Received**.

Final contracts and installation proposals have their own stages:
- **Final Contract Created**, **Final Contract Generated**, **Final Contract Sent Out** and **Contract Signed**;
- **Installation Proposal Created**, **Installation Proposal Generated**, **Installation Proposal Sent Out** and **Installation Quote Signed**.

**Missing Tax Rate** flags a Quote that can't be totaled correctly yet.

## Lifecycle of a Purchase Order

A Purchase Order orders a Job's or Service's items from a Vendor.
- **Before it goes out:** **Waiting for Job Items**, **Needs Confirmation**, **Ready To Order**.
- **Confirmation:** **Awaiting Acknowledgments** until the vendor confirms. Then **Acknowledged**, or **Acknowledgment Processing Failed** if the confirmation couldn't be read.
- **Arrival:** **Upcoming Product ETA** and **ETA Overdue** track when product is due. Then **Awaiting Receiving**, **Awaiting Direct Delivery** (shipped straight to the site), **Partially Received** and **Received**.
- **Special kinds:** **Stock Items PO**, **Labor PO**, **Fee PO** and **No Order Needed**.
- **Cancelled**, plus the same accounting sync milestones as Jobs.

## Lifecycle of a Service

A Service is a work order for a Customer: a repair, warranty claim or follow-up visit. It has a service address, a billing address, a main contact, the caller and the original salesperson.

1. **Intake.** **New Service**, then **In Process**. A Service is classed as **Warranty** or **Chargeable**.
2. **Inspection.** **Need to Schedule**, then **Inspection Scheduled** (a "Service Inspection" Appointment), then **Need to Review Service Trip** after the visit.
3. **Quote, for chargeable work.** **Service Quote Out** or **Full Quote Out**, then **Signed Service Quote Received**. A salesperson can ask to skip the deposit: **Service Deposit Bypass Requested**.
4. **Manufacturer involvement.** When the manufacturer is responsible, the Service can pass through:
   - **Manufacturer to Resolve**, **Manuf. SR Case Open**, **Waiting on Manufacturer**, **Manuf. to Service** and **Manuf. SR Case Closed**;
   - **Manuf. Credit Required** and **Awaiting Manuf. Credit** when a credit is owed.
5. **Parts.** **Need to Order**, **Parts On Order**, **Awaiting Receiving** and **All Items Received**. A Service's Job Items, Purchase Orders, receiving and bins work the same way as a Job's.
6. **Field work.** **Field Work Scheduled** (a "Service Field Work" Appointment). Unlike a Job's, a Service's Appointment can be marked Complete, which records the matching "… Completed" step. Product can also be collected: **Awaiting Customer Pickup** and **Customer Pickup Complete**. **All Items Fulfilled** is reached when everything is delivered.
7. **Resolution and billing.** **All Issues Resolved**, then **Unpaid Invoices** and **Paid In Full**, then **Closed**. A Service can also be **Cancelled**, and it has the same accounting sync milestones as a Job.

## Appointments and scheduling

### Appointment categories

Every Appointment has an Appointment Category. The standard categories are:

- Delivery
- Delivery & Installation
- Installation
- Retail Sales Visit
- Service Inspection
- Service Field Work
- Customer Pickup

Any other value is treated as the general category "Appointment".

The category decides which workflow steps run on the linked Jobs and Services. The steps are named "{Category} Scheduled" and "{Category} Completed", for example "Installation Scheduled" and "Installation Completed".

- **Scheduling** an Appointment runs "{Category} Scheduled" on each linked Job/Service and undoes "{Category} Completed".
- **Unscheduling or cancelling** undoes both steps.
- **Completing** runs "{Category} Completed" and undoes "{Category} Scheduled". Completing is done this way on Services only. On Jobs, the delivery process itself triggers "Delivery Completed".

Because these steps drive milestones, scheduling or unscheduling an Appointment can move the Job or Service into or out of milestones and folders.

### Scheduling items onto an Appointment

Scheduling creates **Appointment Items**, one per Job Item, with the quantity going on that Appointment. A scheduling pathway typically works in one of two ways:

- **All items.** PATH schedules every Job Item that has been received and is not yet scheduled, using each item's received and not-yet-scheduled quantities. For OM items it also attaches the individual packages that are received and not already scheduled, loaded or delivered. A pathway may offer to **include or exclude hardware**. PATH recognizes hardware (such as screens and handles) from the item's description.
- **Assign.** The user picks items and enters a "Qty to Assign" for each. PATH adds those quantities to the Appointment's items, increasing any that are already there.

When an Appointment covers several Jobs or Services, each Job Item is scheduled once. While a large Appointment's items are being attached, the Appointment's **Progress** field rises from 0 to 100 percent.

When packages are scheduled, each Tracking Item is marked Scheduled and linked to its Appointment Item and Appointment.

## Order Management (OM): trucks, receiving, bins and scanning

### What OM is

**OM (Order Manager)** is the manufacturer's order and shipment system. PATH WD downloads order data and truck/shipment data from it automatically. When updated data arrives, PATH updates the existing records instead of creating duplicates. From this data PATH keeps Purchase Orders and Job Items current, and creates the trucks, stops, line items and barcoded packages the warehouse scans.

### Trucks and stops

- A **Receiving Truck** is an inbound shipment from the manufacturer. It has **Stops**, and each stop carries line items and their packages.
- Users receive product **by truck stop**. A stop is closed once all its packages are scanned. A stop can also be skipped if the packages are checked straight into bins instead.
- **Unscheduled trucks** are not real shipments. They are informational "coming soon" trucks that show which packages will ship on a real truck later. PATH skips its automatic processing for them, including automatic Job creation. Nobody receives an unscheduled truck.

### Tracking items (packages) and their states

A **Tracking Item** is one barcoded package or piece. Its **Tracking Qty** is how many physical items are inside that one package. For example, Tracking Qty 3 means three items in one box. All logistics quantities are built from Tracking Qty: received, loaded and delivered quantities are the sum of the Tracking Qty of the packages scanned. A Job Item's, PO's, Job's or Service's tracking quantity is the total Tracking Qty of all its packages.

Each package has four states, each either done or not:

| State | Meaning |
|---|---|
| Received | It has arrived at the dealer (scanned off a truck, against a PO, or into a bin). |
| Scheduled | It is assigned to an Appointment. |
| Loaded | It has been scanned onto the delivery truck. |
| Delivered | It has been delivered at the jobsite. |

A package is **in a bin** when it belongs to a Bin Item. There is no separate "in bin" field. A package can be received but not in any bin, sitting in the general receiving area.

**Placeholder barcodes.** Some packages arrive with a placeholder instead of their final barcode and show a "PLACEHOLDER" flag. The first time a matching barcode is scanned, PATH stores that exact barcode on the package and removes the flag. After that it scans like any other package.

### Receiving

There are three ways product gets received:

- **Truck Receiving** (from a Receiving Truck Stop). Scan each package off the truck. Each package is marked Received and a Receiving History is created, linked to the stop. Packages are **not** put into bins. They wait in the receiving area until checked into a bin later.
- **PO Receiving** (from a Purchase Order). Receive everything on the PO, both OM items (by scanning) and non-OM items (by entering quantities).
- **Bin Check In** (from a Bin, below). Scanning a package into a bin also receives it if it was not received yet, so truck receiving can be skipped entirely.

Every receiving event creates a Receiving History, linked to the Job, Service or PO and, when it came off a truck, to the truck stop. Its Receiving Items record how much of each Job Item was received. These feed the "received" totals on Job Items, Jobs, POs and Services.

### Warehouse bins

Scan the bin's barcode to pick the bin, then:

- **Bin Check In:** scan packages into the bin. Use it to receive straight into a bin, to move received packages from the receiving area into a bin, or to move packages from one bin to another. Packages for the same Job Item in the same bin are grouped under one Bin Item.
- **Bin Check Out:** scan packages out of the bin. They are not placed in another bin. When a bin's last package for a Job Item is removed, that Bin Item goes away.

A **Pallet** is a bin that moves as a unit. When loading or delivering, scanning the pallet's barcode processes every package on it at once.

### Loading and delivery

Both run from the Appointment.

- **Loading** puts scheduled items on the delivery truck. You can scan packages one by one (each is marked Loaded and automatically checked out of its bin), scan a pallet bin to load everything on it, or, for non-OM items, enter quantities. Loading does not change what is scheduled. That was decided when scheduling.
- **Delivery** records what reached the jobsite. You can scan packages off the truck (each is marked Delivered), scan a pallet bin to deliver everything on it, or, for non-OM items, enter a Delivered Qty on the Appointment Items. Delivery runs the Appointment's completion step (for example "Delivery Completed") on the linked Jobs.
- **Accounts that only load, or only deliver.** The account settings **OmLoading** and **OmDelivery** say whether the account runs each process. Without a setting, the Admin Item of the same name ("OM Loading", "OM Delivery") decides by its Enabled box. With neither, the process is on. When Delivery is off, Loading also marks everything delivered and checks pallet items out of their pallet. When Loading is off, Delivery lists every scheduled item, checks each one out of its bin, and marks it loaded as well. Undoing a scan undoes both sides.

### Scan sessions

Each scanning process creates a Scan Session, and each scan or entered quantity becomes a Scan Session Action. This is the audit trail: who scanned what, when, and for which Job, PO, truck, stop, bin or Appointment. The session types are:

| Session type | Started from |
|---|---|
| Truck Receiving | a Receiving Truck Stop |
| PO Receiving | a Purchase Order |
| Bin Check In | a Bin |
| Bin Check Out | a Bin |
| Scheduling | an Appointment |
| Loading | an Appointment |
| Delivery | an Appointment |

Manual quantity entry creates a Scan Session too, just without barcode scans.

## OM vs non-OM job items

Every Job Item falls into one of three groups. The difference decides how a pathway screen handles it.

- **OM job items** have barcoded packages (Tracking Items) from the manufacturer's Order Manager data. A Job Item is OM **only** because it has packages: its "OM Qty Pieces" count is above zero. Receiving, bin check in and out, loading and delivery are done by **scanning barcodes**, and scheduling attaches the individual packages. Their status is tracked package by package.
- **Non-OM job items** are physical products with no packages, because the manufacturer sent no tracking data ("OM Qty Pieces" is zero). There are no barcodes, so users **enter quantities by hand** for receiving, loading and delivery. Their status lives in quantity fields on the Job Item and its Appointment Items.
- **Fee or accounting lines** are marked "Not Tracked". They are not physical products and are never received, loaded or delivered.

"Not Tracked" is a different flag from OM. A non-OM physical product is **not** marked Not Tracked. When deciding whether an item is OM, look only at whether it has packages ("OM Qty Pieces" above zero), never at "Not Tracked".

In practice, a pathway screen that asks for a barcode scan is handling OM items. A screen that asks "how many?" is handling non-OM items. Fee lines should not appear in receiving, loading or delivery at all.

## Accounting integration

PATH WD syncs with the dealer's accounting system. The supported systems are QuickBooks Online (QBO) and QuickBooks Desktop/Enterprise (QBD). For a user, this means:

- Customers, Jobs, sales orders, purchase orders, vendor bills, invoices, payments and applied payments are kept in step between PATH and accounting. Each carries its matching accounting reference, so the same customer, invoice or payment can be matched on both sides.
- Records show their sync status in plain fields: on a Job, "Sent to Accounting" and "Invoiced in Accounting". When a sync fails, the reason is shown in an "Accounting Error" field on the Job or Customer.
- Each sync attempt is logged as an Integration Item, with success or failure, a message and a status of Processing, Complete or Failed. That is where to look when something did not reach accounting.
- Whether the integration is turned on, and which payment methods exist, are account settings (Admin Items).

## Glossary

| Term | Meaning |
|---|---|
| PO | Purchase Order, the dealer's order to a vendor; belongs to a Job or a Service, never both |
| SO | Sales Order |
| CO | Change order (the Job counts them) |
| NC | Non-conformance (problem job needing review and approval) |
| OM | Order Manager, the manufacturer's order and shipment data feed |
| OM job item | A Job Item with barcoded packages ("OM Qty Pieces" above zero) |
| Non-OM job item | A physical Job Item with no packages; quantities entered by hand |
| Not Tracked | Fee or accounting line, not a physical product |
| OM Qty Pieces | Number of barcoded packages on a Job Item |
| OM Order Status | The manufacturer's order status shown on a Job |
| Truck / Receiving Truck | An inbound shipment from the manufacturer |
| Stop / Truck Stop | One stop on a truck's route; receiving happens per stop |
| Unscheduled truck | An informational "coming later" truck, not a real shipment |
| Tracking Item / package / piece | One barcoded package |
| Placeholder | A package whose final barcode is set on first scan |
| Tracking Qty | Number of physical items inside one package; the basis for all logistics quantities |
| Received / Scheduled / Loaded / Delivered | The four package states |
| Bin | One storage spot in a warehouse, with its own barcode |
| Pallet | A bin that moves as one unit; scanning it loads or delivers everything on it |
| Bin Check In / Check Out | Scanning packages into or out of a bin |
| Appointment Category | Type of visit: Delivery, Delivery & Installation, Installation, Retail Sales Visit, Service Inspection, Service Field Work, Customer Pickup (otherwise "Appointment") |
| Qty to Assign | Quantity a user picks to schedule onto an Appointment |
| Applied Payment | A Payment applied to a specific Invoice or Sales Order |
| Paid in Full | Invoice step reached when the unpaid balance is $0.01 or less |
| Record Payment | Job/Service step that runs when a Payment is linked |
| QBO / QBD | QuickBooks Online / QuickBooks Desktop (Enterprise) |

Short prefixes in link names: "AP" (Applied Payment, as in "AP Invoice"), "RH" (Receiving History), "RI" (Receiving Item), "OT" (Order Truck) and "TI" (Tracking Item). In these names, "AP" does **not** mean accounts payable.
