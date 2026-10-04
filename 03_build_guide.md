# Build Guide: Hospital Operations Dashboard in Power BI Desktop

**Time needed:** about 60 to 90 minutes. Power BI Desktop is free and runs on Windows (Microsoft Store).
Do the steps in order and check your numbers at the end of each part.

## Part 1: Load and clean the data (10 min)
1. Save the `data` folder somewhere simple, for example `C:\Healthcare\`.
2. Open Power BI Desktop > **Get data > Blank query > Advanced Editor**.
3. Paste all of `01_power_query_clean.pq`. Change the file path on the `Source` line to match your folder. Click **Done**.
4. Rename the query to **fact_admissions** (right panel). Click **Close & Apply**.

Check: in Data view the table has **54,860 rows** and 14 columns.

## Part 2: Date table and relationship (10 min)
1. **Modeling > New table**. Paste the `Date =` block from `02_dax_measures.dax`.
2. Select the Date table > **Table tools > Mark as date table** > choose `Date`.
3. In Model view, drag `Date[Date]` onto `fact_admissions[admission_date]` (one-to-many).
4. In Data view, click `Date[Month]` > **Sort by column** > `Month Number`.
5. **Modeling > New table**. Paste `LOS Cap = GENERATESERIES(7, 28, 1)`.

## Part 3: Measures (15 min)
1. **Home > Enter data**, leave it empty, name it `_Measures`, click Load.
2. Select `_Measures`, then **Modeling > New measure**. Paste the measures from sections C to F one at a time.
3. Set formats: percentages as **Percentage (1 decimal)**; `Total Billing` and `Avg Billing per Admission` as **Currency**; `Avg Length of Stay` with **1 decimal**.

## Part 4: Check your numbers (5 min)
Put each measure in a card with no filters. They should match:

| Measure | Expected value |
|---|---|
| Total Admissions | 54,860 |
| Total Billing | about $1.404 billion |
| Avg Billing per Admission | about $25,595 |
| Avg Length of Stay | 15.5 days |
| Total Bed Days | 850,265 |
| Long Stay Admissions | 16,360 |
| Long Stay % | 29.8% |
| Long Stay Share of Bed Days | 50.1% |
| Emergency % | 32.9% |
| Bed Days Freed (cap 21) | 82,338 (9.7% of bed days) |
| Avg Beds Freed per Day (cap 21) | 45.1 |
| Bed Days Freed (cap 14) | 248,877 (29.3%) |

Admissions by year: 2019 = 7,280 | 2020 = 11,157 | 2021 = 10,795 | 2022 = 10,891 | 2023 = 10,918 | 2024 = 3,819.
If a number is different, check the Part 1 path and the relationship in Part 2 first.

## Part 5: Page 1 "Overview" (15 min)
- Four **cards**: Total Admissions, Total Billing, Avg Billing per Admission, Avg Length of Stay.
- **Line chart**: X = `Date[Year-Month]`, Y = Total Admissions.
- **Donut chart**: Legend = `admission_type`, Values = Total Admissions.
- **Bar chart**: Y = `insurance_provider`, X = Total Billing.
- **Slicers**: `Date[Year]`, `medical_condition`, `admission_type`.

## Page 2 "Length of Stay and Capacity" (20 min)
- Cards: Long Stay %, Long Stay Share of Bed Days.
- **Column chart**: X = `los_band`, Y = Total Admissions.
- **Bar chart**: Y = `medical_condition`, X = Avg Length of Stay (start the axis at 0 so you see how similar they are).
- **Matrix**: Rows = `medical_condition`, Columns = `admission_type`, Values = Avg Length of Stay. Add a colour scale (Cell elements > Background colour).
- **What-if block**: slicer on `LOS Cap[LOS Cap]` (Slider style) and cards for Bed Days Freed, Bed Days Freed %, Avg Beds Freed per Day.

## Page 3 "Condition Detail" (10 min, drill-through)
1. Add a blank page named **Condition Detail**.
2. In **Drill through**, drop `medical_condition` into the box.
3. Add cards (Total Admissions, Avg Length of Stay, Avg Billing per Admission), a column chart by `age_group`, and a column chart by `los_band`.
4. Back on Page 2, right-click a bar in the condition chart > **Drill through > Condition Detail**.

## Tooltip (5 min, optional)
Add a page, set **Page information > Allow use as tooltip**, page size = Tooltip. Put two cards in it (Avg Length of Stay, Avg Billing per Admission). In the condition bar chart > **Format > General > Tooltips > Type: Report page** and pick that page.

## Finish
1. Use one colour theme (View > Themes), give each page a title, and align the visuals.
2. Take a screenshot of each page and save as `images/dashboard_page1.png`, `images/dashboard_page2.png` and `images/dashboard_page3.png`.
3. **File > Save as** `Hospital_Operations_Dashboard.pbix`.
4. Optional: **Publish** to Power BI Service (free work/school account needed) and add the link to your README.

## Common problems
| Problem | Fix |
|---|---|
| "Couldn't find file" in Power Query | The path on the Source line is wrong; open the query's Advanced Editor and fix it |
| Month names sort alphabetically | Sort `Date[Month]` by `Month Number` (Part 2, step 4) |
| Matrix shows `(Blank)` or 54,860 everywhere | The relationship is missing or on the wrong column |
| `Bed Days Freed` does not change | The `LOS Cap` slicer is not on the page, so it defaults to 21 |
