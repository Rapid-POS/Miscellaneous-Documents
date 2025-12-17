# Using the Column Description Report

A reference guide to tables and columns within Counterpoint

_Updated: December 16, 2025_

## An overview of the Counterpoint table structure  
  
Before accessing the Column Description Report, it’s helpful to know what tables exist in Counterpoint. 

**This is a quick list of table prefixes for the most commonly used tables:**
| Prefix | Description |
|-------|-------------|
| AR | Originally stood for Accounts Receivable, but now refers to all customer tables |
| DM | Data Mark; refers to aggregated data for certain reports |
| DX | Data Migration tables created by NCR to originally help move data from CP v7, now occasionally used for migrations from other POS systems |
| IM | Items and inventory |
| PO | Purchase Orders (examples: Purchasing, Receiving, Vendors, Purchasing adjustments, etc.) |
| PS | Point-of-Sale (examples: Tickets, Voids, Store, Station, Drawer, Sales Orders, etc.) |
| SY | System (examples: Company Control, Distributions, Gift Cards, Store Credit, Users, Workgroups, etc.) |
| USER | Tables created by Rapid (as opposed to the standard tables from NCR) |
| VI | Views |
  
One way to find a specific table in Counterpoint is to open the desired window, then right click on the header bar and choose “Table Information”:

![Accessing Table Information](./images/header-bar-table-information.png)  

![Accessing Table Information - Table Name](./images/header-bar-table-information-table-name.png)  

## How to Access the Column Description Report

To access the Column Description Report in Counterpoint, navigate to System > Utilities > Column Description Report

![Column Description Report Menu Button](./images/menu-button-system-utilities-column-description-report.png)  

Use the Tables Icon to select a specific table or tables for the report:

![Column Description Report Tables Icon](./images/column-description-report-tables-icon.png)  

![Column Description Report Select Tables](./images/column-description-report-select-tables.png)  

Use the Report Icon to set report options:

![Column Description Report Report Icon](./images/column-description-report-report-icon.png)  

For new users, start with the following table options, column options, and selected columns:

![Column Description Report Report Options](./images/column-description-report-report-options.png)  

After the table has been selected and the report options have been set, click the lightning icon to generate the report:

![Column Description Report Generate Report Lightning Icon](./images/column-description-report-generate-report-lightning-icon.png)  

For example:

![Column Description Report Example Using AR_CUST](./images/column-description-report-sample-report-AR_CUST.png)  

If this report is too wordy, reduce it by removing Show Column Description in report options:

![Column Description Report Options Without Column Descriptions](./images/column-description-report-report-options-without-column-descriptions.png)  

To know if the field is a lookup to another table, add these columns:

![Column Description Report Lookup Fields](./images/column-description-report-report-options-lookup-to-another-table.png)  

For example, on the AR_CUST Table, for each customer, the Sales Rep field is required (however, it is not a key field). The options available are pulled from the SY_USR lookup table, specifically the USR_ID field which is filtered for “Is Sales Rep” = Yes.  

![Column Description Report Lookup Fields Using AR_CUST](./images/column-description-report-report-options-lookup-to-another-table-AR_CUST-example.png)  

Note: A key is the unique identifier. Sometimes multiple fields are combined to create a “key” such as both store and order number makeup the key for sales orders.  
