# Tableau Dashboard Guide – Hotel Booking Cancellations

Data source: `hotel_tableau.csv` (5,481 bookings x 33 columns, exported by the notebook).
Works in **Tableau Public** (free) or Tableau Desktop (free student licence). Tableau Public can only read files, which is why a CSV is used.

## 1. Connect and set up
1. Open Tableau -> **Connect -> Text file** -> choose `hotel_tableau.csv` -> go to **Sheet 1**.
2. Check data types (click the icon above each column):
   - `arrival_date` -> **Date**; `is_canceled`, `has_request`, `has_children` -> **Number (whole)**
   - `country_name` -> right-click -> **Geographic Role -> Country/Region** (for the map)
   - `risk_band`, `season`, `lead_group`, `market_segment` -> **String (dimensions)**
3. Set a manual sort order: `lead_group` (0-7, 8-30, 31-90, 91-180, 180+), `risk_band` (Low, Medium, High), `season` (Winter, Spring, Summer, Autumn).

## 2. Calculated fields (Analysis -> Create Calculated Field)
| Name | Formula |
|---|---|
| Cancellation Rate | `AVG([is_canceled])`  (format as % with 1 decimal) |
| Paid ADR | `IF [adr] > 0 THEN [adr] END` |
| Revenue at Risk (High) | `SUM(IF [risk_band] = "High" THEN [revenue_at_stake] END)` |
| Gap vs Overall | `AVG([is_canceled]) - { FIXED : AVG([is_canceled]) }` |
| Avg Predicted Risk | `AVG([pred_cancel_prob])` |

## 3. Sheets to build (each with a title and labelled axes)
| # | Sheet | How |
|---|---|---|
| 1 | **KPI tiles** | Four text sheets: `COUNTD(booking_id)`, Cancellation Rate, `AVG(Paid ADR)`, Revenue at Risk (High) |
| 2 | **Monthly demand and cancellation** | Columns `arrival_ym` (discrete); Rows `COUNT(booking_id)` as bars + Cancellation Rate as a line -> **Dual axis**, synchronise if needed |
| 3 | **Segment risk heat-map** | Rows `market_segment`; Columns `arrival_date_year`; Color = Cancellation Rate; Label = Cancellation Rate (diverging red-blue palette, centre at 0.5) |
| 4 | **Lead-time groups** | Columns `lead_group`; Rows Cancellation Rate; reference line = overall rate |
| 5 | **Guest origin map** | Double-click `country_name`; Color = Cancellation Rate; Size = `COUNT(booking_id)` |
| 6 | **ADR seasonality** | Columns `arrival_month_name` (sort by `month_no`); Rows `AVG(Paid ADR)`; Color = `season` |
| 7 | **Risk bands (model output)** | Columns `risk_band`; Rows Cancellation Rate (actual); Label = `COUNT(booking_id)`; shows the model separates risk |
| 8 | **Special requests (A/B)** | Columns `has_request`; Rows Cancellation Rate; Label = % |

## 4. Dashboard layout (Dashboard -> New -> size: Automatic or 1200 x 800)
```
+-------------------------------------------------------------+
| Title: Resort Hotel - Booking Cancellation Dashboard        |
+----------+----------+------------------+---------------------+
| KPI 1    | KPI 2    | KPI 3            | KPI 4               |
+----------+----------+------------------+---------------------+
| 2. Monthly demand + cancellation rate  | 5. Guest origin map |
+----------------------------------------+---------------------+
| 3. Segment x year  | 4. Lead-time  | 7. Risk bands          |
|    heat-map        |    groups     |                        |
+--------------------+---------------+------------------------+
| Filters: year | season | market_segment | customer_type | risk_band |
+-------------------------------------------------------------+
```
- Add filters (click the sheet's dropdown -> **Use as Filter**, or Show Filter) for `arrival_date_year`, `season`, `market_segment`, `customer_type`, `risk_band`; apply them to all sheets.
- Use a **Highlight/Filter action** (Dashboard -> Actions) so that clicking a segment updates the other charts.
- Optional second dashboard "Pricing": sheets 6 and 8 plus ADR by `customer_type` and `has_children`.

## 5. What the dashboard should show (for your PPT slide 8)
- Cancellation is highest for Online TA and Portuguese guests; the 2016 rate is far above 2015.
- Lead time groups form an inverted-U (31% -> 57% -> 42%).
- Model risk bands separate actual cancellation: Low 6.6%, Medium 61.5%, High 84.2%.
- Summer ADR is about double the rest of the year.

## 6. Save and export
- **Tableau Public:** File -> Save to Tableau Public (needs a free account) and paste the link in your report.
- **Screenshots:** Dashboard -> Export Image, for slide 8 of the PPT.
- Keep `hotel_tableau.csv` next to the workbook if you save a `.twbx` (packaged) file.
