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
| `Bolt Socks Order-to-Cash Process Map - Scully J .pdf` | Lucidchart swim lane diagram of the O2C process as described by staff |

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

The process map (`Bolt Socks Order-to-Cash Process Map - Scully J .pdf`) is organized into four swim lanes:

| Swim Lane | Steps |
|-----------|-------|
| **Customer** | Places order (online or phone/sales rep) |
| **Sales & Order Management** | Existing customer check → Set Credit Terms → Prepare Sales Order → Send Invoice to Customer |
| **New Customer Credit Processing** | Submit Credit Assessment Request → 3rd Party Reviews Credit History → Receive & Review Assessment Results |
| **Warehouse & Shipping** | Pick & Pack Order (24-hr target) → Ship via US Express or other carrier → Confirm Delivery |
| **Finance & Collections** | Payment Received in Window? → Yes: End / No: Initiate Collections Process → End |

**Intended sequence:**

1. Customer places an order (online or via sales representative)
2. Credit assessment decision:
   - *Existing customers*: credit terms set immediately from prior history
   - *New customers*: third-party credit assessment requested, reviewed, and returned before terms are finalized
3. Sales prepares a formal sales order and transmits it to the warehouse
4. Warehouse picks and packs the order (target: 24-hour turnaround)
5. Order shipped via US Express or available carrier
6. Delivery to customer confirmed
7. **Sales issues an invoice** (triggered by delivery confirmation)
8. Customer pays within agreed terms → process closes
9. If payment not received on time → Finance initiates collections → process closes

## Key Observations (Described vs. Actual)

| Area | Finding |
|------|---------|
| **Invoice timing — critical** | The process map shows invoicing is triggered *after* delivery confirmation. This means the payment clock doesn't start until after pick, pack, ship, and delivery — a structural delay that directly impacts cash flow |
| Credit assessment (hidden process) | The new customer credit assessment described in the process map generates no event log activity — it is a hidden process not captured in the ERP system. Its duration and outcomes are invisible to this analysis |
| Request for Quotation | `Request for Quotation` (95 cases) appears in the event log but was not discussed during staff interviews and is not represented in the process map. It is a separate, unrelated process — its purpose and ownership require further investigation |
| Customer pick-up | 600 cases involve `Customer pick-up` — a fulfillment variant entirely absent from the described process |
| Undocumented activities | `Purchase Order Created` (499), `Delivery Changed` (399), and `Confirmation of Service` (200) appear in the data but have no equivalent in the process map |
| Collections gap | No collections activity exists in the event log despite the described process including it — ~515 cases end without `Payment Received` with no recorded follow-up |
| Invoicing gap | ~310 orders with a `Sales Order Created` never had an `Invoice Created`; ~205 invoiced orders were never paid |
| Activity mapping | The event log activity `Confirmed Delivery Date` (5,043 occurrences) does not clearly correspond to any single step in the described process — it may represent scheduling rather than actual delivery confirmation |

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
