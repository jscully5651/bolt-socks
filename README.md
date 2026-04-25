# Bolt Socks — Order-to-Cash Process Analysis

## Engagement Overview

This repository supports a process mining engagement for **Bolt Socks**, a wholesale sock distributor supplying retail stores. The goal is to evaluate their end-to-end order-to-cash (O2C) process and identify opportunities to accelerate collections and improve cash flow.

## Business Problem

Bolt Socks is experiencing cash flow challenges stemming from delays in their order-to-cash cycle. The engagement focuses on answering:

- Where is time being lost between order placement and payment receipt?
- Does the actual process match how staff describe it?
- Which process variants (deviations from the standard path) correlate with slower payment?
- What changes would have the greatest impact on reducing days-to-payment?

## Repository Contents

| File | Description |
|------|-------------|
| `process_mining.ipynb` | Google Colab notebook — process discovery and timing analysis using PM4PY |
| `order_to_cash.csv` | Event log — 33,615 events across 5,047 orders (cases) |

## Data

The event log (`order_to_cash.csv`) contains three columns:

| Column | Type | Description |
|--------|------|-------------|
| `case_id` | integer | Unique order identifier |
| `activity` | string | Process step name |
| `timestamp` | datetime | When the activity occurred (`DD.MM.YYYY HH:MM:SS`) |

### Activities in the Event Log

| Activity | Occurrences |
|----------|-------------|
| Sales Order Created | 5,050 |
| Confirmed Delivery Date | 5,043 |
| Picking Done | 4,948 |
| Invoice Created | 4,740 |
| Payment Received | 4,535 |
| Delivery Completed | 3,855 |
| Shipment Sent | 3,651 |
| Customer pick-up | 600 |
| Purchase Order Created | 499 |
| Delivery Changed | 399 |
| Confirmation of Service | 200 |
| Request for Quotation | 95 |

## Described Process (As-Interviewed)

The following reflects the O2C process as described by Bolt Socks staff:

1. Customer places an order (online or via sales representative)
2. Credit assessment:
   - *Existing customers*: credit terms set immediately from prior history
   - *New customers*: third-party credit assessment requested, reviewed, and returned before terms are finalized
3. Sales prepares and transmits a formal sales order to the warehouse
4. Warehouse picks and packs the order (target: 24-hour turnaround)
5. Order shipped via US Express or available carrier
6. Delivery to customer confirmed
7. Sales issues an invoice
8. Customer pays within agreed terms → process closes
9. If payment not received on time → Finance initiates collections

## Key Observations (Described vs. Actual)

| Area | Finding |
|------|---------|
| Credit assessment | RFQ activity appears in only ~2% of cases — most orders skip formal credit review |
| Customer pick-up | 600 cases use pick-up instead of shipping — not reflected in the described process |
| Undocumented activities | `Purchase Order Created`, `Delivery Changed`, and `Confirmation of Service` appear in the data but are absent from the process description |
| Collections | No collections activity exists in the event log — ~515 cases end without `Payment Received` |
| Invoicing gap | ~310 orders never had an invoice created; ~205 invoiced orders were never paid |

## Notebook Structure

| Cell | Description |
|------|-------------|
| 1 | Install PM4PY |
| 2 | Import libraries |
| 3 | Upload CSV (Google Colab file picker) |
| 4 | Load data into DataFrame |
| 5 | Preview first 100 rows |
| 6 | Display schema and data types |
| 7 | Convert timestamps |
| 8 | Count total activities and cases |
| 9 | List distinct activities |
| 10 | Count distinct activities |
| 11 | Calculate median case throughput time (~24 days) |
| 12 | **Average time from Sales Order Created to Picking Done** |
| 13 | Format DataFrame for PM4PY; identify start and end activities |
| 14 | Discover and visualize DFG (Directly Follows Graph) |
| 15 | Discover and visualize Heuristics Net |

## Setup

This notebook is designed for Google Colab. To run it:

1. Open `process_mining.ipynb` in [Google Colab](https://colab.research.google.com/)
2. Run all cells in order
3. When prompted, upload `order_to_cash.csv`
