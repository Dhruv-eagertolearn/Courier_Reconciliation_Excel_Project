# Courier_Reconciliation_Excel_Project
Built an Excel-based courier billing reconciliation tool using SUMIF, INDEX/MATCH, and CEILING formulas. Calculated expected shipment charges from weight slabs and delivery zones, then compared them to courier invoices across 124 orders — flagging 79 overcharged and 23 undercharged orders, all with live, auditable formulas.
## Problem Statement
Company X is a large Indian e-commerce company that ships thousands of orders
daily through multiple courier partners. Courier charges depend on **shipment
weight** and **delivery zone** (distance between warehouse and customer). Since
X pays a significant amount to courier partners every month, they want to
verify whether courier partners are billing the **correct amount per order**.

**Objective:** For every order, independently calculate the expected courier
charge using X's own records, compare it against the amount actually billed
by the courier, and flag each order as correctly charged, overcharged, or
undercharged.

## Dataset
Five raw data sources (included in the workbook as separate tabs):
| File | Description |
|---|---|
| `Order Report` | Order ID, SKU, and quantity for every line item in every order |
| `SKU Master` | Gross weight (grams) of each product SKU |
| `Pincode Zones` | Warehouse-to-customer pincode combinations mapped to a delivery zone (a–e) |
| `Courier Invoice` | Courier's AWB number, order ID, charged weight, zone, shipment type, and billed amount |
| `Courier Rates` | Courier's rate card — fixed + additional charge per 0.5 kg slab, by zone and shipment type |

~124 orders, ~400 order line items.

## Approach
1. **Weight calculation** — total shipment weight per order computed from
   `Order Report × SKU Master` using `SUMIF`, then rounded up to the nearest
   0.5 kg slab using `CEILING` (courier bills in 0.5 kg multiples).
2. **Zone lookup** — delivery zone per order looked up via the
   warehouse–customer pincode pair against the `Pincode Zones` table using
   `INDEX/MATCH` on a concatenated key.
3. **Expected charge** — calculated from the weight slab, zone, and shipment
   type against the `Courier Rates` card (fixed rate for the first 0.5 kg +
   additional rate for each extra 0.5 kg slab; RTO charges added when the
   invoice specifies "Forward and RTO charges").
4. **Comparison** — expected charge vs. billed amount, classified as
   correctly charged / overcharged / undercharged.

**All calculations are live Excel formulas** (`SUMIF`, `INDEX`, `MATCH`,
`CEILING`, `VALUE`, `IFERROR`) referencing the raw data tabs directly —
nothing is hardcoded, so the workbook recalculates automatically if the
source data changes.

## Solution / Output
- **`Order Level Calculation`** tab — order-by-order comparison with 11
  columns: Order ID, AWB Number, Total Weight (X), Weight Slab (X), Total
  Weight (Courier), Weight Slab (Courier), Delivery Zone (X), Delivery Zone
  (Courier), Expected Charge, Billed Charge, and Difference.
- **`Summary`** tab — count and total amount for correctly charged,
  overcharged, and undercharged orders.

### Results
| | Count | Amount (Rs.) |
|---|---|---|
| Correctly charged | 22 | 1,826.90 |
| Overcharged | 79 | 4,426.60 |
| Undercharged | 23 | 575.10 |

## Tools Used
Microsoft Excel (formulas only — `SUMIF`, `INDEX`, `MATCH`, `CEILING`,
`VALUE`, `IFERROR`, `SEARCH`)

## Files
- `Courier_Reconciliation_Excel_Project.xlsx` — full workbook (problem
  statement, raw data, order-level calculation, and summary — all in one file)
