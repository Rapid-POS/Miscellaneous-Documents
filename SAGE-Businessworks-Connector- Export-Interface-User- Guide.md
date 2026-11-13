# Sage BusinessWorks Connector — User Guide

This guide covers the RapidPOS accounting connector for **Sage BusinessWorks**.

> [!IMPORTANT]
> **Nothing is posted to BusinessWorks automatically.** BusinessWorks cannot be written to by other programs, so the connector creates import files and a person imports them. When the connector says a run succeeded, it means *a file is ready*, not that anything is in BusinessWorks yet.

### The process at a glance

| Step | Where | What happens |
|---|---|---|
| 1 | **CounterPoint** | Distributions and vouchers wait here |
| 2 | **Connector** | Makes the import files |
| 3 | **Import folder** | Files, plus a Message Center note |
| 4 | **BusinessWorks** | A person imports the files |
| 5 | **Validate** | Compare the two reports |

## Contents

1. [What the connector does](#1-what-the-connector-does)
2. [Your daily routine](#2-your-daily-routine)
3. [What is in each file](#3-what-is-in-each-file)
4. [When something goes wrong](#4-when-something-goes-wrong)
5. [Validation](#5-validation)

## 1. What the connector does

CounterPoint holds accounting data that is waiting to go to your accounting system: GL distributions (the debits and credits from sales, receivings and so on) and AP vouchers (vendor bills and credits). The connector turns that waiting data into files in BusinessWorks' own import format:

| File | What it holds | Imported with |
|---|---|---|
| `GL_Import_<batch>_<date and time>.csv` | General ledger journal entries | **GL > Transfer > Import Journal Entries** |
| `AP_Import_<date and time>.csv` | Vendor bills (invoices) and vendor credits | **AP > Transfer > Import Invoices/Adjustments** |
| `Vendor_Import_<date and time>.csv` | New or changed vendors | **AP > Transfer > Import Vendor Information** |

The files are written to one folder, the **import folder** (`C:\CPAccounting\BW\Export`), and a note appears in CounterPoint's Message Center for each one, so you know a file is waiting and where it is.

The date and time in a file name are when the file was made, as `yyyyMMddHHmmss`. For example, `AP_Import_20261102221500.csv` was made on 2 November 2026 at 10:15:00 PM.

## 2. Your daily routine

1. **Import the files in this order:**
   1. `Vendor_Import_…` with **AP > Transfer > Import Vendor Information** (only when there is one).
   2. `AP_Import_…` with **AP > Transfer > Import Invoices/Adjustments**.
   3. `GL_Import_…` with **GL > Transfer > Import Journal Entries**.
2. **Check the result** by comparing CounterPoint and BusinessWorks ([section 5](#5-validation)).
3. **Move the files to an archive folder, or delete them,** once they are imported, so the same file is not imported twice by mistake.

> [!NOTE]
> **Order matters.** The vendor file must go in **before** the AP file, because BusinessWorks rejects an invoice for a vendor it does not have. The GL file does not depend on the others.

## 3. What is in each file

### GL file

- One journal entry per **distribution date**. All the lines for one date make one entry.
- Every line is in the **General** journal (`GEN`).
- The **reference** is the CounterPoint GL batch number.
- The **description** of each line is `<event>-<package>-<activity type>-<document number>`, so you can trace a line back to the CounterPoint transaction that made it.
- Amounts are signed the way CounterPoint stores them: positive for debits, negative for credits.
- A file can hold at most **999 journal entries** (dates). A batch that spans more than that stops with an alert and must be split.
- The account number is the GL account piece of the CounterPoint account number. A profit center or other piece is **not** sent.

**What a line looks like**

```csv
1,07/21/26,GEN,5,1024032-PS-PSP-1181198,1104.000,1367.41
1,07/21/26,GEN,5,1024032-PS-PSP-1181198,1200.000,-934.44
```

*Illustration only, based on the journal entry in Figure 3. Each line is: entry number, date, journal, reference, description, account, amount. Every line for one date shares the same entry number.*

### AP file

The AP file has one line per voucher.

- A voucher with a positive total becomes an **invoice**. The line carries the vendor, invoice number, invoice date, amount, reference and due date, followed by the account distributions. If a voucher has no invoice date or due date, today's date is used.
- **Payment terms and discounts are not sent.** Only the due date goes in; the discount amount and date are left blank.
- A voucher can have at most **20 account distribution lines**. A voucher with more is skipped, an alert is raised, and it stays in CounterPoint for the next run.
- The reference is limited to **20 characters**; anything longer is cut off.

### Vendor file

- Only vendors that have a voucher in that run **and** have not been sent before are included.
- Each vendor line adds the vendor or updates it if BusinessWorks already has that ID. On an update, a field that is blank in the file is left unchanged in BusinessWorks.
- It carries the vendor ID, name, address, city, state, zip, phone, contact, federal ID, comments, fax and email.
- BusinessWorks accepts plain ASCII only, so accented letters are reduced to the base letter and other special characters become spaces. Phone numbers are sent only when they have 10 digits.

## 4. When something goes wrong

When you run the interface in CounterPoint, you may see this confirmation:
<img width="610" height="365" alt="image" src="https://github.com/user-attachments/assets/663a3fdc-1a7c-437a-9de4-1131b5d5fb1b" />

***Figure 1.** CounterPoint's warning when unposted G/L transactions are already on file.*

> [!WARNING]
> **Stop and check first.** Always double check the exported file when you see this message, or raise it with support, before you continue. Continuing without checking can create **duplicates**.

**What to do**

1. Choose **No** for now. Do not interface until you have checked.
2. Look in the import folder for the last GL file, and confirm in BusinessWorks whether it was already imported ([section 5](#5-validation) shows how).
3. If you are not sure, raise it with support before you proceed.

If the connector itself fails, a **Sage BusinessWorks … Alert** note appears in CounterPoint's Message Center, in the `ACCTG_SAGEBW` group.

## 5. Validation

Validate each GL import by comparing CounterPoint's report with BusinessWorks' report for the same date.

1. In CounterPoint, run the **Distributions by Account Number (Summary)** report for the date you interfaced (Figure 2).
2. Import the GL file in BusinessWorks.
3. In BusinessWorks, print the **Journal Entry Batch Report** (Figure 3).
4. Compare the two, using the table below.

<img width="935" height="642" alt="image" src="https://github.com/user-attachments/assets/68e4a5fd-7c4b-401c-9861-a11fd6b31978" />

***Figure 2.** CounterPoint: Distributions by Account Number (Summary).*

<img width="783" height="582" alt="image" src="https://github.com/user-attachments/assets/e4381a3c-669a-45e5-b12b-6d3aa1bca877" />

***Figure 3.** BusinessWorks: Journal Entry Batch Report after the import.*

**What to compare**

| | CounterPoint report (Figure 2) | BusinessWorks report (Figure 3) |
|---|---|---|
| **Date** | The date range at the top and the **Date** column (7/21/26). | **Post/Sys Date** (07/21/2026). |
| **Event** | **Event #** (1024032). | The start of each description (1024032-PS-PSP-1181198). |
| **Accounts** | **Account #** (1104-00X). | The account (1104.000). The format differs, so compare the account piece. |
| **Amounts** | **Debit amt** and **Credit amt** for each account. | **Debits** and **Credits** for each account. |
| **Journal and reference** | Not shown. | **Jnl** is GEN. **Ref No.** is the GL batch number (5). |

> [!NOTE]
> **Report totals.** In Figure 3 the report total reads 0.00 because the entry is dated in an earlier month than the print date. The report's own note says such entries are not included in its totals. Compare the individual lines instead.

> [!TIP]
> **Work date by date.** Process one date at a time where you can, and export the CounterPoint report every time. Validating each date this way helps make sure the process is accurate.
