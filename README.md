# Hospital Operations Dashboard (Power BI)

**Business question:** How can a hospital free up beds and plan resources better by managing length of stay?

**Answer in one line:** Length of stay does not depend on diagnosis, admission type, insurance or age in this dataset, so the lever is the long-stay tail: stays over 21 days are 29.8% of admissions but **50.1% of all bed days**, and capping stays at 21 days would free about **45 beds per day**.

> Add your Power BI screenshots here after building the dashboard:
> `![Overview](images/dashboard_page1.png)`

## Dataset
Kaggle **Healthcare Dataset** (prasad22): 55,500 admission records, 15 columns, 2019-05-08 to 2024-05-07.
**Important:** this is a *synthetic* dataset generated with a random-data library, not real patient data. See Limitations.

## Tools and skills
Power BI Desktop | Power Query (M) | DAX (time intelligence, `CALCULATE`, `SUMX`, what-if parameter) | data modelling (date table, relationships) | slicers | drill-through | tooltips | data storytelling

## Project files
| File | Purpose |
|---|---|
| `data/healthcare_dataset_raw.csv` | Original file (55,500 rows) |
| `data/healthcare_cleaned.csv` | Cleaned table used by the dashboard (54,860 rows) |
| `powerbi/01_power_query_clean.pq` | Power Query script that does the cleaning |
| `powerbi/02_dax_measures.dax` | Date table, what-if table and 18 measures |
| `powerbi/03_build_guide.md` | Page-by-page build steps and expected values |
| `images/` | Analysis charts used below |
| `Hospital_Operations_Dashboard.pbix` | The Power BI report (add after you build it) |

## Data cleaning (Power Query)
| Step | Rows affected |
|---|---|
| Raw records | 55,500 |
| Exact duplicate rows removed | 534 |
| Negative billing amounts removed (invalid) | 106 |
| **Final records** | **54,860** |

Also: set data types, calculated `length_of_stay` (discharge minus admission), added `age_group` and `los_band`, and dropped patient names, doctor, hospital, room and blood type (names are personal data; doctor and hospital have about 40,000 different generated values each, too many to analyse).

## Key findings
- 54,860 admissions, total billing **$1.40 billion**, average bill **$25,595**, average stay **15.5 days**, **850,265 bed days**.
- Admissions are evenly split: Elective 33.6%, Urgent 33.5%, Emergency 32.9%.
- **Long stays drive capacity:** 16,360 stays (29.8%) lasted more than 21 days and used **50.1%** of all bed days.
- **What-if:** capping stays at 21 days frees 82,338 bed days (9.7%), about **45 beds per day**; a 14-day cap frees 29.3%, about 136 beds per day.
- **No segment explains stay length:** average stay is 15.4 to 15.7 days for every condition, 15.3 to 15.6 days for every age group, and 15.4 to 15.6 days for every admission type and insurer. Differences are under 2%.

![Long stays](images/analysis_01_long_stays.png)
![What-if](images/analysis_02_what_if_cap.png)
![By condition](images/analysis_03_los_by_condition.png)

## Recommendations
1. Focus on the **long-stay tail** (over 21 days), not on a particular diagnosis, since no diagnosis or payer stands out.
2. Set up discharge-planning reviews for any patient approaching day 21.
3. Use the what-if page to size the benefit: each 7-day reduction in the cap frees a large share of capacity (21 days: 9.7%, 14 days: 29.3%).
4. Plan staffing on admission volume, which is steady at about 10,900 a year in 2020 to 2023 with no strong seasonality.

## Limitations
- **Synthetic data.** Every category is close to uniformly random, which is why no group stands out, and billing does not rise with length of stay (correlation about 0), which would not happen in a real hospital. The dashboard demonstrates the method, not real clinical findings.
- 2019 (starts May) and 2024 (ends May) are partial years, so year-on-year comparisons for those years are not like-for-like.
- Hospital and doctor columns are not usable (about 40,000 distinct generated names), so no hospital-level comparison is made.
- Bed days are a simple sum of length of stay across all records, not a measure of occupancy at one hospital.
