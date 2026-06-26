# LittleSteps Healthcare — Patient Visit Analysis

## Introduction

This project was completed as part of a data internship take-home assignment for LittleSteps, an at-home healthcare startup. The objective was to clean and process raw nurse visit data, engineer key metrics, and surface actionable insights on visit durations and nurse travel times to support operational efficiency improvements.

---

## Repository Structure

```
littlesteps-analysis/
├── littlesteps_clean.ipynb               # Main analysis notebook
├── visits.csv                            # Raw dataset (1,000 records)
├── visits_cleaned.csv                    # Cleaned dataset (851 records)
├── plot_01_visit_duration_histogram.png  # Distribution of visit durations
├── plot_02_avg_duration_by_service.png   # Avg duration by service type
├── plot_03_avg_duration_by_location.png  # Avg duration by location zone
├── plot_04_boxplot_by_service.png        # Duration spread by service type
└── README.md
```

---

## Setup Instructions

### Requirements

Install the required libraries:

```bash
pip install pandas numpy matplotlib seaborn scipy
```

### How to Run

1. Download all files from this repository
2. Place `visits.csv` in the same folder as `littlesteps_clean.ipynb`
3. Open `littlesteps_clean.ipynb` in Jupyter Notebook
4. Click **Kernel → Restart & Run All**

All 4 chart PNG files will be saved automatically to the same folder when the notebook runs.

---

## Analytical Approach

**Part 1 — Data Preparation**
- Loaded raw dataset: 1,000 records across 8 columns
- Removed duplicate `visit_id` records
- Fixed typos in `service_type` (e.g. `Pyhcisal Therapy` → `Physical Therapy`) and `visit_location` (e.g. `Notrh` → `North`)
- Standardized two mixed date/time formats using `pd.to_datetime(format='mixed')`
- Dropped 100 rows with missing `visit_end_time` (cannot compute duration without end time)
- Filled 100 missing `nurse_notes` values with placeholder text
- Removed negative durations and outliers using a 3× IQR fence

**Part 2 — Analysis and Visualization**
- Engineered `visit_duration_minutes` from end time minus start time
- Engineered `travel_duration_minutes` as the gap between a nurse's consecutive visits (capped at 240 min to exclude shift breaks)
- Ran descriptive statistics, group comparisons, one-way ANOVA, and keyword extraction from nurse notes
- Produced 4 visualizations: histogram, two bar charts, and a box plot

---

## Key Findings

| Metric | Value |
|--------|-------|
| Clean records analysed | 851 |
| Average visit duration | 63.0 min |
| Median visit duration | 62.0 min |
| Average travel duration | 129.1 min |
| Longest service type | Medication Administration (64.3 min avg) |
| Shortest service type | General Check-up (59.9 min avg) |
| ANOVA result (zones) | p = 0.079 — no significant difference across zones |
| Highest travel nurse | N9783 (225.9 min avg) |
| Lowest travel nurse | N8068 (12.1 min avg) |
| Urgent visits | 154 (18.1% of all visits) |
| Urgent avg duration | 65.9 min vs 62.3 min non-urgent |

**Top 3 nurses by travel time:** N9783, N6322, N7802  
**Bottom 3 nurses by travel time:** N9428, N1415, N8068

---

## Operational Recommendations

1. **Use service-specific time slots** — Medication Administration runs 4.4 min longer than General Check-ups on average. Fixed slots cause daily over/under-scheduling.
2. **Assign nurses to fixed zones** — Top 3 nurses average 225 min travel vs overall 129 min. Zone-based assignment would reduce unnecessary cross-zone trips.
3. **Schedule urgent visits early in shifts** — Urgent cases run 3.6 min longer on average. Front-loading them prevents cascading delays.
4. **Make end-time logging mandatory** — 10% of records had no end time and could not be analysed. A mobile app prompt at visit completion would eliminate this gap.
5. **Rebalance nurse workloads** — Some nurses carry significantly more visits than others. Redistributing visits is more cost-effective than adding headcount.

---

## Assumptions and Challenges

**Assumptions:**
- Travel gaps over 240 minutes between visits were treated as shift breaks, not travel
- The first visit of each nurse's day has no travel time (no prior visit to measure from)
- A conservative 3× IQR fence was used for outlier removal to retain as much data as possible

**Challenges:**
- Raw data contained two different date/time formats requiring mixed parsing
- Multiple typos in categorical columns required manual correction before analysis
- 10% of records had missing end times — these were dropped, reducing the usable dataset from 1,000 to 851 records
