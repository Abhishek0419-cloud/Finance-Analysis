# Finance Analysis Dashboard (Power BI)

Interactive two-page Power BI dashboard on 50,000 banking transactions (Jan 2023 – Apr 2026). It tracks transaction amount, volume, fees and tax, with year-over-year comparison, and breaks results down by customer segment, state, transaction type, status and gender. A second page lists the underlying transactions, reached by drill-through.

**Tools:** Power BI (Power Query, DAX, drill-through, field parameters)

## Dashboard

**Overview page:** KPI cards with YoY change, amount by month, amount by transaction status, amount by customer segment, amount by state (top 10), a heat-map table by transaction type, and amount by gender.

![Overview](images/overview.png)

**Transactions page (drill-through):** transaction-level grid with customer, type, status, amount, fees and tax. Right-click any visual on the overview and drill through to see the records behind it.

![Transactions](images/transactions.png)

**Filters:** Year (single select), Dynamic Metric, Occupation and Merchant Category. The Dynamic Metric slicer switches the month, segment and state charts between Total Amount, Total Transactions, Total Fees and Total Tax.

## Key findings (2025)

- **Total amount ₹137.53M** across 14,944 transactions (+1.4% amount, -0.6% transactions vs 2024). Volume has been flat for three years: ₹137.07M (2023), ₹135.62M (2024), ₹137.53M (2025).
- **Three transaction types make up 70% of the amount:** Loan EMI (₹40.0M), Transfer (₹36.2M) and Deposit (₹20.3M).
- **Retail customers contribute 55% of the amount** (₹75.7M), followed by Premium (18.6%) and SME (15.4%). Wealth is the smallest at 4.4%.
- **Maharashtra is the top state with 15.9%** of the amount; the top 3 states (Maharashtra, Karnataka, Gujarat) account for 38.6%.
- **Gender split is close to even:** Female 52.8%, Male 47.2% of the amount.
- **About 14.6% of the amount is Failed or Pending** across the full period (Failed 10.4%, Pending 4.2%). Only 85.4% (₹389.0M of ₹455.5M) is successful.

## Data

| Table | Rows | Description |
|---|---|---|
| `finance_transactions` | 50,000 | Date, customer, type, channel, merchant category, amount, fees, tax, status, fraud flag and risk score |
| `customers` | 5,000 | Name, gender, occupation, segment, state, income, join date (3,983 customers have transactions) |

Model: `customers` (1) to `finance_transactions` (many) on `customer_id`, a `Calendar` table for time intelligence and a `Dynamic Metric` field-parameter table.

## Data cleaning (Power Query)

- Removed **69 duplicate rows** (same `transaction_id`): 50,069 to 50,000.
- Fixed channel typo `M@bile App` to `Mobile App` (765 rows) and trimmed text.
- Standardised currency to upper case and set data types (including dates).
- Converted 8 negative amounts to positive with `Number.Abs`. These are treated as data-entry issues; the total is small (₹71.6K).
- Filled 24 missing fee values with ₹14.52, the overall average fee.
- Merged the misspelled `fisrt_name` and `second_name` columns into `Full Name`.

## DAX highlights

- Measures for Total Amount, Total Transactions, Avg Transaction, Total Fees and Total Tax.
- Previous-year and YoY measures using `SAMEPERIODLASTYEAR`. They return blank unless exactly one year is selected (`HASONEVALUE`), so partial or multi-year selections do not show misleading growth.
- A field parameter for the dynamic metric, plus dynamic titles driven by the selected metric and year.

## Things to know

- **Total Amount, Fees and Tax include Failed and Pending transactions.** Fees and tax are also recorded on failed transactions (about 10% of total fees). Read the cards as gross values, and use the status chart for successful amounts only.
- **Deposits, refunds and interest credits are added together with withdrawals and EMIs**, so Total Amount means gross transaction value, not net flow.
- **The data looks synthetic.** For example, 18.7% of transactions happen before the customer's join date, so tenure-based analysis is not reliable.
- 2026 contains only Jan–Apr. The Year slicer compares it with the same months of 2025.
- 630 transactions (1.26%, ₹5.97M) are flagged as fraud, and 548 of them have a Success status. The fraud flag and risk score are in the data but are not yet used in the dashboard.
- Data is loaded from static CSV files, so the dashboard is not real-time. To refresh, place the CSVs in a folder and update the file path in Power Query (Transform data, Source).

## Repository contents

- `Finance_Analysis.pbix`: Power BI report
- `finance_transactions.csv`, `customers.csv`: source data
- `Business_Requirements.docx`: dashboard requirements
- `images/`: dashboard screenshots
