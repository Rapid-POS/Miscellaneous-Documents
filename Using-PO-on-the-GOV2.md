# PO on the GO v2 — User Guide

## Table of Contents
- [What This Tool Does](#what-this-tool-does)
- [Settings](#settings)
- [The Entry Sheet, at a Glance](#the-entry-sheet-at-a-glance)
- [Building a New Purchase Request](#building-a-new-purchase-request-start-to-finish)
- [Filling in the Header](#filling-in-the-header)
- [Adding and Editing Items](#adding-and-editing-items)
- [Gridded Items](#gridded-items)
- [Sending Your Request](#sending-your-request)
- [Starting Over](#starting-over)
- [The Dashboard](#the-dashboard)
- [Tips & Troubleshooting](#tips--troubleshooting)

---

## What This Tool Does

PO on the GO v2 builds a purchase request and sends it to Counterpoint through the Rapid API.

> **Important:** It does **not** create a purchase order and does **not** post anything. A purchase request is a saved, unposted document. Once sent, someone still needs to open it in Counterpoint, review it, and post it there. Nothing in this workbook touches inventory or vendor records — that only happens when the request is posted in Counterpoint.


---

## Settings

The **Settings** sheet holds the connection details that let this workbook talk to Counterpoint through the Rapid API. It's separate from the Entry sheet because it isn't part of building a request — it's what makes the workbook work at all.

### Connection Fields

| Field | Who Provides It | Notes |
|---|---|---|
| **Base URL** | RapidPOS | The address of your Rapid API endpoint. Provided by RapidPOS when the workbook is set up. |
| **API Key** | RapidPOS | Authenticates the workbook to your Rapid API instance. Provided by RapidPOS along with the Base URL. |
| **Username** | You | Your own Counterpoint login. Each person using this workbook enters their own. |
| **Password** | You | Your own Counterpoint password, paired with the username above. |
| **Workgroup ID** | You | Unique to you (or your workstation). Identifies this session to Counterpoint separately from other users working against the same environment. |

> **Note:** Base URL and API Key come from RapidPOS and identify *which* Counterpoint environment this workbook connects to. Username, Password, and Workgroup ID are entered by whoever is using the workbook and identify *who* is connecting. Don't share your Username, Password, or Workgroup ID with another user — everyone using the workbook should enter their own.

### Other Notes About Settings

- These fields are what every toolbar button on the Entry sheet relies on behind the scenes — Refresh All, Send Purchase Request, Load Purchase Request, and so on all authenticate using what's entered here.
- **Reset Workbook** (also on this sheet) does **not** clear any of these fields — a reset only clears request data and cached Counterpoint data, never your connection details. See [Starting Over](#starting-over).
- If you're not sure what to enter for Base URL or API Key, contact RapidPOS rather than guessing — these are specific to your environment and won't work if copied from another client's workbook.
- If Username, Password, or Workgroup ID is wrong or has changed, connector calls will fail with an authentication error. Update the field here and try again before assuming something else is broken.

---

## The Entry Sheet, at a Glance

Everything happens on the **Entry** sheet. From top to bottom:

| Section | Description |
|---|---|
| **Toolbar** | The buttons — every action starts here. |
| **Dashboard** | Vendor, dates, terms, discounts, and ship-to information. Collapsible (see [The Dashboard](#the-dashboard)). |
| **Status line** | Shows what just happened (a refresh finishing, item count, an error). Green = fine, red = needs attention. |
| **Line list** | One row per item: Quantity, Description, Unit, Status. This is what you're building. The Status column is filled in by *Send Purchase Request* (see [Sending Your Request](#sending-your-request)). |

Other tabs are supporting cast:
- **Settings** — connection details, plus the Reset Workbook button (see [Settings](#settings) and [Starting Over](#starting-over)).
- **Items, Vendors, Locations, Categories, SubCategories, VendorTerms, Grids, ShipVia** — cached copies of Counterpoint data, filled in by a refresh and replaced by the next one. Not edited by hand.
- **ShipTo** (hidden) — holds the ship-to address. It used to sit far down the Entry sheet, which capped how many lines a request could hold. The dashboard's totals had a matching cap for a while after that move; neither limit exists anymore, so nothing caps list length now.

### Toolbar Buttons

| Button | What It Does |
|---|---|
| **Refresh All** | Pulls everything from Counterpoint: full item catalog, vendors, locations, categories/subcategories, terms, ship-via codes, attributes, and grid dimensions. Run this first, or whenever data might be stale. Asks for confirmation before starting (item count + rough time estimate). |
| **Refresh Vendor Data** | Same refresh, scoped to the vendor already in the dashboard — faster when you already know the vendor. |
| **Vendor & Header** | Opens the form for vendor, dates, terms, discounts, and ship-to location (see [Filling in the Header](#filling-in-the-header)). Pulls vendor/location lists in automatically if not yet refreshed. |
| **Enter / Edit Item** | Click a line first, then this button, to add or edit an item (see [Adding and Editing Items](#adding-and-editing-items)). Double-clicking a line does the same without a toolbar trip. Item search lives inside this form. |
| **Send Purchase Request** | Validates the whole request, then sends it to Counterpoint as new or updated. Every line gets a Status. |
| **Clear All** | Wipes the sheet — vendor, header, ship-to, and every line — back to blank. |
| **Show/Hide Dashboard** | Collapses/expands the dashboard. Display only — doesn't affect what's saved. |

---

## Building a New Purchase Request, Start to Finish

1. **Refresh your data.**
   - Click **Refresh All** the first time, or if unsure the catalog is current. **Refresh Vendor Data** is quicker if you already know the vendor and needs no confirmation.
   - Refresh All checks item count first, then confirms before running (e.g., *"This will pull 6,833 items in 7 pages… Estimated time: a couple of minutes. Continue?"*). Cancel changes nothing. The estimate gets more accurate over time, since the workbook remembers per-page timing from the last refresh.
   - While running, the status line shows progress (e.g., page 3/7, 3,000 of 6,833 items), then the final count. Excel is busy until it finishes.
   - A refresh replaces the workbook's lookup sheets; a vendor-scoped refresh leaves only that vendor's items in the catalog. Refresh again without a vendor to restore the full catalog.

2. **Fill in the header.** Click **Vendor & Header** (see [Filling in the Header](#filling-in-the-header)).

3. **Add your items.** Click an empty row, then **Enter / Edit Item**, for each item (see [Adding and Editing Items](#adding-and-editing-items)).

4. **Check the list.** Each line shows quantity and description. A gridded item shows as one line with combined quantity; its size breakdown is stored underneath but not shown in the list.

5. **Send it.** Click **Send Purchase Request**. It validates everything first, then reports the new request number and marks every line's Status (see [Sending Your Request](#sending-your-request)).

6. **Post it in Counterpoint.** This workbook's job ends at "created." Open the request in Counterpoint to review and post.

---

## Filling in the Header

Click **Vendor & Header**. This form covers vendor, dates, and delivery/ship-to details.

### Required Fields
| Field | Notes |
|---|---|
| **Vendor** | From dropdown. Opening this form pulls back dropdown options in the vendor file for vendor/terms/ship-via/location from which the customer can choose to populate that field in the form. |
| **Backorders** | Yes or No. |
| **Delivery Location** | From dropdown. Also drives the ship-to address. |
| **Order Date, Delivery Date, Cancel Date** | All required, must be real dates. Cancel/Delivery can't be before Order Date. |
| **Terms** | From dropdown. |

### Optional Fields
Contact person, phone, email, account number, discount amount **or** percent (not both), misc charge, ship-via, and three comment fields. If the vendor file in Counterpoint has Contact 1 and Phone 1 defined that information is pulled into the form automatically but can be changed.

> **Discount Amount/Percent** is a one-time, whole-order discount off the entire request's subtotal — not per line. It's separate from the per-line discount on the Enter/Edit Item form (which only affects that line's cost). The two stack: line discounts come off first, then the whole-order discount comes off what's left. Discount percent has to be expressed in decimals versus whole numbers.

### Ship-To
Fills in automatically the moment you enter a Delivery Location and click OK — pulled from that location's record. If the location isn't found, ship-to fields are cleared rather than left stale.

### Other Notes
- **Commas rule:** No commas allowed in contact person, phone, email, account number, or order date — the form blocks you and asks you to remove them.
- **Purchase request # / PO #** shown is informational only — assigned by Counterpoint on send; not typeable.
- **New Order – Same Vendor** (bottom of form) starts the next request to the same vendor without re-entering the header (see [Starting Over](#starting-over)).

---

## Adding and Editing Items

Two ways in, both open the same form:

- **Double-click any row** — an existing line to edit, or an empty row to start new. Works anywhere across the row; fastest on a long request.
- **Click a line, then Enter / Edit Item** on the toolbar — natural when already at the toolbar.

A single click never opens the form — it only selects the row (for reading/copying a cell). The form determines which line you're on from the row itself, so editing always updates that line rather than duplicating it. For a gridded item, clicking any row in its block gets you the whole line.

Opening an existing line pre-fills the form, with **Delete Line** available and quantity (or the whole grid) already loaded.

### Finding the Item

Type an item number, vendor's item number, or description into the search box — all three are searched.

| Action | Result |
|---|---|
| **Press Enter** | Goes straight to the item if there's an exact match (description, cost, price, category, unit fill in immediately). If no match, Item Search opens. |
| **Click Lookup Item** | Always opens Item Search, even on an exact match — use this to browse. |
| **Empty box** | Always opens Item Search, listing the whole catalog. |

#### Using Item Search
- Results list by item number, then vendor item number, then description (vendor item number is often blank).
- Click a row to highlight, then **Select** — or double-click. Highlighting alone commits nothing.
- Change the text and click **Search Again** (or press Enter) to re-search without cancelling the line.

#### Creating a Brand-New Item

If the item genuinely isn't in Counterpoint, type the vendor's item number into the search box and click **Create New Item**.

**Create New Item** is only available when:

| Situation | Button State |
|---|---|
| Box empty | Greyed out |
| Text matches an existing item | Greyed out — select it instead |
| Text matches nothing | **Available** |

You then type description and unit by hand. The item isn't created in Counterpoint until the request is sent. Counterpoint assigns the real item number — the vendor's item number you typed is stored alongside it, not used as the item number.

**At send time, for each new item:**
1. The catalog is checked first — an exact match on item number or vendor item number, reuses the existing item instead of duplicating.
2. An inventory record is created at your delivery location (Counterpoint requires this).
3. The item is added to this workbook's catalog immediately — no refresh needed for later lines on the same request.

If any step fails, the status line explains why and nothing is sent.

Every row starts this form **blank** — nothing carries over from a previously entered line.

### Quantity

- **Plain item:** one Quantity box. Required, numeric, greater than zero.
- **Gridded item:** Quantity box replaced by a grid (one row per dimension-1 value, one column per dimension-2 value). Type quantities into the cells you're ordering; leave the rest blank. At least one cell needs a quantity. The line's total is the sum of the grid — clearing a cell removes that quantity, it won't linger.

### Other Fields

- **Cost** — required.
- **Delivery date, cancel date, discount amount/percent, comments 1–3** — optional, apply to that line only (independent of the header's).
- **Line Discount Amount is per unit, not per line** (Counterpoint's own convention). E.g., 1.00 on a line of 21 removes 21.00 total, not 1.00. To take a flat amount off a line, divide by quantity first, or use Discount Percent (works against extended cost, no arithmetic needed). Only one of the two can be entered — if both are filled, percent wins.
- **Price 1, Regular Price, Category, Subcategory, Brand** — reference-only for catalog items (greyed out); not part of what's sent. Blank for lines pulled in via Load Purchase Request.
- **Unit** — auto-filled/locked for catalog items; open to type for new items.
- **Vendor's Item No** — only required for brand-new items. For catalog items it's whatever's on file (often blank), which is fine.

### Deleting a Line

Editing an existing item shows a **Delete Line** button. Click it, confirm (irreversible) — the line (and full grid breakdown, if gridded) is removed and everything below shifts up.

---

## Gridded Items

A gridded item is tracked across two grid dimensions (e.g., colour and size) and ordered cell by cell. In Counterpoint, each dimension has a tag (e.g., COLOR) and values (RED, BLUE, GREEN).

On the Enter/Edit Item form, the **Gridded item** tick box sits above the quantity area; the grid itself lives in a fixed column on the right side of the form (always visible — greyed out for plain items, quantity box greys out for gridded ones).

### Setting Up the Grid

### Creating Purchase Request Line for Gridded Items already in Counterpoint

- Selecting it builds the grid from Counterpoint's dimensions on file — pre-filled and editable.
- Lists lock once the line is placed — to add a value, delete and re-add the line on a blank row.
- If value lists come up empty for a known gridded item, its dimensions aren't refreshed into this workbook yet — run **Refresh All** and re-pick the item.
- The grid area is a fixed size; the form only widens for gridded lines. Grids larger than the visible area get scrollbars (bottom for columns, side for rows).
- Counterpoint supports a third grid dimension, but this workbook only orders across two — the third is always left unused.
- **Delete Line** removes the header row and all detail rows in one go. There's no way to remove just one grid row — zero out quantities instead, or delete/recreate the whole line.

---

## Sending Your Request

**Send Purchase Request** validates before writing — sheet consistency first, then every existing-in-Counterpoint item. If anything's wrong, it reports everything and sends nothing (no request, no new items, sheet untouched).

### What It Catches
- A line with no quantity (previously these were silently dropped).
- A blank row mid-list (previously, everything below a blank row silently vanished — close gaps with Delete Line, not manual clearing).
- A new item missing a required field (description, unit, or a gridded item's dimension tag).
- The same vendor item number on two rows (would create the item twice).
- Items that can't be ordered — not on file, inactive, or no inventory record at the delivery location (previously rejected one line at a time, requiring repeated sends to find every bad line).

> **Refresh before you send.** The check relies on the cached catalog to spot duplicates and match new items — it won't send without a refresh this session.

### The Status Column

| Status | Meaning |
|---|---|
| **Created item [#]** | New item; number assigned by Counterpoint. |
| **Matched existing item [#]** | Entered as new, but the vendor item number was already on file — existing item reused. |
| **No quantity** / rejection reason | Line failed the check. |
| **Sent** | Written only after Counterpoint accepts the request. |

Statuses clear at the start of each send; deleting a line clears its status too.

### While It's Sending

- The status line and Excel's bottom bar show progress (e.g., *"Creating items 201 to 400 of 950…"*, *"Sending 1,240 lines to Counterpoint…"*), each message appearing before the step it describes.
- Excel is genuinely busy and unresponsive during each call to Counterpoint — this is normal, not a freeze. Save before sending a long request.
- **Esc** stops the run, but only takes effect between calls (not instantly). Any item already created is real; its Status shows how far the run got, and resending picks up from there without duplicating.
- A thousand-item request takes minutes. New items go up in batches of 200 (status line updates each batch); the final send is one call and the longest single wait. Any one call gets up to ten minutes.

### If the Request Number Is Already on the Sheet

Clicking Send first checks with Counterpoint whether that number still exists:

- **No longer exists (e.g., posted):** number is cleared automatically; request sends as brand-new — no prompt.
- **Still exists:** you're asked to update it:
  - **Yes** — replaces its lines and cells entirely (not a partial change).
  - **No** — cancels; nothing sent, sheet unchanged.

To force a brand-new request instead of updating a pending one, clear the request number (use Clear All) before sending.

---

## Starting Over

Three sizes of fresh start:

### New Order – Same Vendor
The smallest reset — for placing a second order with the same supplier. Found at the bottom of the Vendor & Header form.

- **Clears:** every line, request number, status line, whole-order discount amount/percent, misc charge, and the three comments.
- **Keeps:** vendor, vendor number, contact, phone, email, account number, terms, ship-via, backorders, delivery location, ship-to address, and the three dates.
- Also clears line-area formatting and unhides every row (like Clear All).
- Asks first, naming the vendor (e.g., *"Start a new request for 21st Century - PRIMETIME?"*) — No changes nothing.

### Clear All
Wipes vendor, dashboard, ship-to, and every line back to blank. Clears cell formatting and unhides rows. Doesn't touch the refreshed catalog. Use when the next request goes to a **different** vendor.

### Reset Workbook
Lives on the **Settings** sheet (not the Entry toolbar). Restores the workbook to its shipped state:

- Clears the current request (header, ship-to, lines, grid tags).
- Clears everything cached from Counterpoint (items, vendors, locations, categories/subcategories, terms, ship-via, attributes, grid dimensions).
- Unhides rows, clears odd formatting, resets request number to `(AUTO-ASSIGN)`.
- **Never touches** Settings sheet connection details (base URL, key, username, password, workgroup).

Asks twice, both defaulting to **No**; the second prompt quantifies what you'll lose (e.g., *"6,833 cached items, plus the vendors, locations, categories…"*). Use it to hand off the workbook, shrink the file before emailing, or clear untrusted data. File size only shrinks after you save post-reset.

---

## The Dashboard

Displays vendor, dates, terms, discounts, and ship-to details from Vendor & Header — read-only; always edit via that form, not the sheet directly.

### The Four Totals

| Figure | Calculation |
|---|---|
| **TOTAL QTY** | Every line's quantity summed (gridded lines included). |
| **TOTAL COST** | Lines at full cost, before any discount. |
| **NET AFTER DISCOUNT** | TOTAL COST minus line discounts minus whole-order discount — matches Counterpoint's subtotal. |
| **TOTAL** | NET AFTER DISCOUNT plus misc charge. |

All four cover the entire line list and count gridded lines via their grid.

> ⚠️ **Older workbooks:** these totals used to only sum roughly the first 1,000 rows, silently under-reporting anything beyond that.

**Check NET AFTER DISCOUNT before sending** — it mirrors Counterpoint's rules. A lower-than-expected figure usually means a per-unit discount was misapplied; a negative figure means discounts exceed the order's value (send will be rejected).

**Show/Hide Dashboard** collapses/expands it — visual only, no effect on what's sent.

---

## Tips & Troubleshooting

| Issue | Explanation / Fix |
|---|---|
| **"Nothing to send – no lines with a quantity."** | Every line has a zero/blank quantity. Add at least one real quantity. |
| **A gridded line's quantity looks wrong after editing** | Reopen Enter/Edit Item — the total always reflects the current grid, not the prior state. |
| **Request rejected on send** | Status line explains why; nothing was created, so fix and resend safely. |
| **A vendor/location isn't in the dropdown** | Run Refresh Vendor Data or Refresh All first. |
| **"Refresh All (or Refresh Vendor Data) first" on load** | Nothing refreshed yet this session — run one, then retry. |
| **"Create New Item" greyed out** | Box is empty, or text matches an existing item — select that item instead. |
| **New item got an unexpected item number** | Expected — Counterpoint assigns numbers. Your vendor item number is kept alongside it. |
| **Enter opened search instead of filling the item in** | No exact match — pick from the list or refine the search text. |
| **Meant to create new, got an existing item** | The vendor item number already belongs to a real item — use a different one if it's genuinely different. |
| **"Vendor's item number is required" on a catalog item** | Shouldn't happen — check you're not accidentally on the new-item path. |
| **Added line doesn't appear in the list** | Check if the row is genuinely empty (formula bar) before re-adding — avoids duplicates. |
| **Editing added a duplicate instead of updating** | Shouldn't happen (row-based identification) — delete the extra with Delete Line. |
| **Vendor item number/unit blank after a gridded line** | Fixed. For old affected lines, reopen and re-enter those two fields. |
| **"Row …: vendor item number … is also on row …"** | Two lines create the same new item — combine into one line (one grid for gridded items). |
| **Single-click no longer opens the form** | Deliberate — double-click any row (empty or not) to edit. |
| **"Delete line failed: …"** | Line wasn't removed; message explains why — nothing half-deleted. |
| **Resending a loaded request** | Re-check account number, email, and header discount — these don't come back from a load. |
| **"Catalog hasn't been refreshed" on send** | Run Refresh All / Refresh Vendor Data — the send check reads the cached catalog. |
| **Got a list of problems, nothing sent** | Expected — the check caught issues before creating anything. Dialog shows first 15; full detail is per-line in the Status column. |
| **Excel unresponsive during send** | Expected — waiting on Counterpoint. Read the last status message and wait; save *before* a long send. |
| **Stopped a send part way** | Check Status column — "Created item…" lines already exist in Counterpoint; resending reuses them, no duplicates. |
| **Lines missing from the request in Counterpoint** | Check for blank rows or no-quantity lines — now caught pre-send, but older workbook sends could drop them silently. |
| **Need another colour/size on a gridded line already added** | Value lists lock once placed. For existing Counterpoint items: delete and re-add the line, extend lists before OK. For unsent new items: delete and recreate from scratch. |
| **Grid empty for a known gridded item** | Its dimensions aren't refreshed in yet — run Refresh All and re-pick the item. |
| **Green triangle "Inconsistent Formula" on a Qty cell** | Harmless — each gridded line's block differs in size. Don't use "Copy Formula from Above." New lines have this warning disabled. |
| **"Refreshed … – no items found." (in red)** | Refresh succeeded but returned nothing — for a vendor-scoped refresh, check the vendor number. |
| **Enter/Edit Item's OK button off-screen** | Shouldn't happen — form re-centres and tall grids scroll. Try a taller Excel window if needed. |
| **Same item appears twice in a lookup sheet** | Fixed — Refresh All clears lookup sheets before rewriting. |
| **Discount took far more off than expected** | Discount Amount is per unit (e.g., 1.00 × 21 units = 21.00 off). Divide by quantity, or use Discount Percent. Watch NET AFTER DISCOUNT live. |
| **"A document discount of … is more than the … left on the request"** | Line discounts already zeroed/negatived the order — reduce a discount and resend. |
| **Counterpoint shows "Gross subtotal 0.00" on a discounted request** | That display reads net cost — a fully-discounted line shows 0.00 there. Check Subtotal/Total misc/Total instead. |
