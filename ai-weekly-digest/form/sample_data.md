# Google Sheets setup — sample data

The automation reads **one Google Sheets spreadsheet** with **four tabs**. Each tab is a
flat table: row 1 is the header, every row below is one record. Dates use the
`YYYY-MM-DD` format.

> **Column names must match exactly** — including capitalisation and underscores. The
> `Calculate Metrics` Code node reads columns by name (`Date`, `Amount`, `Tickets`,
> `Avg_Response_hrs`, `Spend`, `Conversions`, …). Rename a column in the sheet and that
> metric silently becomes zero.

Put the spreadsheet ID (the long string in its URL) in `GOOGLE_SHEET_ID`.

**Ready-made demo data:** `Leads.csv`, `Revenue.csv`, `Support.csv`, `Ad Spend.csv` in this
folder are populated with two full weeks of data (current week `2026-05-04`–`2026-05-10`,
prior week `2026-04-27`–`2026-05-03`) and contain two built-in anomalies — revenue down
~31% and ad spend up ~56% (with conversions down and CPA more than doubled). In Google
Sheets: **File → Import → Upload** each CSV → **Insert new sheet(s)** — the tab is named
from the file, so the four tab names come out exactly right.

---

## Tab: `Leads`

The weekly metric is **total leads = number of rows in the reporting week**.

| Date       | Source    | Grade | Converted |
|------------|-----------|-------|-----------|
| 2026-05-05 | LinkedIn  | A     | Yes       |
| 2026-05-05 | Referral  | B     | No        |
| 2026-05-06 | Website   | A     | Yes       |
| 2026-05-08 | Google Ads| C     | No        |

- `Date` — when the lead came in (used to bucket the row into a week).
- `Source`, `Grade`, `Converted` — context only; passed to the model as a data sample for the summary.

---

## Tab: `Revenue`

The weekly metric is **total revenue = sum of the `Amount` column**.

| Date       | Amount  | Product       | Channel  |
|------------|---------|---------------|----------|
| 2026-05-05 | 1200.00 | Consulting    | Direct   |
| 2026-05-06 | 850.00  | Retainer      | Referral |
| 2026-05-09 | 2400.00 | Implementation| Direct   |

- `Amount` — numeric, no currency symbol or thousands separators (`1200.00`, not `$1,200`).
- `Product`, `Channel` — context only.

---

## Tab: `Support`

The weekly metrics are **total tickets = sum of `Tickets`**, **total resolved = sum of `Resolved`**,
and **average first-response time = mean of `Avg_Response_hrs`** across the week's rows.

| Date       | Tickets | Resolved | Avg_Response_hrs |
|------------|---------|----------|------------------|
| 2026-05-05 | 12      | 10       | 2.4              |
| 2026-05-06 | 8       | 8        | 1.8              |
| 2026-05-07 | 15      | 13       | 3.1              |

- One row per day (or per shift) with the day's counts. `Tickets` / `Resolved` are integers.
- `Avg_Response_hrs` — decimal hours (`2.4` = 2 hours 24 minutes).

---

## Tab: `Ad Spend`

The weekly metrics are **total spend = sum of `Spend`**, **total conversions = sum of `Conversions`**,
and **CPA = total spend ÷ total conversions**.

| Date       | Platform  | Spend  | Clicks | Conversions |
|------------|-----------|--------|--------|-------------|
| 2026-05-05 | Google    | 120.00 | 340    | 8           |
| 2026-05-06 | Meta      | 95.00  | 280    | 5           |
| 2026-05-08 | Google    | 140.00 | 410    | 11          |

- `Spend` — numeric, no currency symbol. `Clicks` / `Conversions` are integers.
- `Platform`, `Clicks` — context only.

---

## How the week is decided

The digest runs Monday morning and reports on the **week that just ended**: the previous
Monday 00:00 up to (but not including) this Monday 00:00. The week before that is the
**prior** week used for the percentage-change comparison. Any row whose `Date` falls
outside those two windows is ignored.

---

## Adding a new data source

Want a fifth section (e.g. *Website Traffic* or *Churn*)? The pattern repeats cleanly:

1. **Add a tab** to the same spreadsheet — first column `Date`, then one column for your
   metric (e.g. `Sessions`) plus any context columns.
2. **Duplicate the read node** — copy `Get Leads Data`, point `sheetName` at the new tab,
   connect it to the `Merge Sheets Data` node (add one more input).
3. **Extend `Calculate Metrics`** — add a block that sums/averages your metric for the
   current and prior weeks and pushes a new section into the returned object (copy any
   existing `section(...)` line).
4. **Duplicate a Summarize node** — copy `Summarize Leads`, give it its own OpenAI
   model node, point the prompt at your new section, and wire it into `Merge Summaries`.
5. **Reference it in `Build HTML Email`** — add the section to the `sections` array and to
   the Slack message. Done — no other changes needed.
