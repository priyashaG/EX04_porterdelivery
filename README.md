# Porter: Optimizing Delivery for the Digital Diner

An analysis of 197,428 Porter delivery orders (Oct 2014 to Feb 2015) to understand what drives delivery time across markets, hours, days, order protocols and partner supply. Every figure is computed from `data/dataset1.csv`; the Excel workbook uses live formulas over the cleaned data, so each number can be traced.

---

## Executive Summary

- **Orders analysed:** 197,194 (after cleaning)
- **Average delivery time:** 47.6 minutes (median 44.3, standard deviation 18.5)
- **Delivered within 30 / 45 / 60 minutes:** 13.2% / 51.7% / 80.1%
- **Slowest market:** Market 1 at 51.3 minutes (others 46.0 to 47.6); it is also the least predictable (std dev 21.4)
- **Busiest hours (1, 2, 3):** 46.7% of all orders, and the slowest deliveries (52.3 minutes vs 43.5 in other hours)
- **Slowest day:** Monday at 51.4 minutes (Wednesday fastest at 44.1); Saturday has the most orders
- **Order size** explains little of delivery time (R-squared 2.4%)

---

## Dataset and Cleaning

- `dataset1.csv`: 197,428 rows x 14 columns (market, timestamps, store, category, order protocol, items, prices, partner counts).
- Delivery time = `actual_delivery_time` - `created_at`, in minutes. Order hour and weekday come from `created_at`.
- **Removed:** 7 rows with no delivery time; 35 with a duration not positive or over 300 minutes (extreme values up to 6,231 minutes); 192 with invalid prices (subtotal <= 0 or minimum item price < 0). **197,194 rows remain.**
- **Kept:** 987 orders with no market, 995 with no protocol and 4,760 with no category are labelled Unknown. Negative partner counts were blanked; about 16,000 rows have no partner counts and are excluded from partner analyses only.
- High-cardinality category (74 values) is simplified to the 12 most common plus "other" where needed (above-average order value, variability).
- **Not in the data:** customer ID, target delivery time or SLA, kitchen/pickup/transit timings. Nothing in this repository claims results on those.
- Timestamps look UTC-like (orders peak at hours 1 to 3 and 19 to 23), so no "lunch" or "dinner" labels are used.

---

## Answers to the Case-Study Questions

### Basic

| Question | Answer |
| :--- | :--- |
| Q1 Order volume by market | M2 54,999; M4 47,537; M1 37,988; M3 23,273; M5 17,980; M6 14,430 (987 unknown) |
| Q2 Delivery time by store category | Slowest with 50+ orders: caribbean 56.2 min; fastest: kosher 41.0. Large categories: pizza 50.8, japanese 52.0, italian 50.3, fast 43.8 |
| Q3 Peak order hours | Hours 2 (36,929), 1 (28,151) and 3 (27,045): 46.7% of orders |
| Q4 Highest-volume days | Saturday 34,500, Sunday 33,568; Tuesday lowest (24,039) |
| Q5 Items vs subtotal | Correlation 0.56 (moderate positive) |
| Q6 Distribution and outliers | Median 44.3, 5th to 95th percentile 24.9 to 81.2 min; outlier fence 88.3 min; 6,238 orders (3.2%) above it |
| Q7 Delivered within 30 minutes | 13.2% (45 min: 51.7%; 60 min: 80.1%) |
| Q8 Volume vs busy partners | Orders per hour vs average busy partners: correlation 0.96 |
| Q9 Day-of-week effect on delivery time | Mon 51.4, Tue 46.3, Wed 44.1, Thu 47.0, Fri 44.7, Sat 50.0, Sun 48.4 |
| Q10 Top 5 slowest stores (50+ orders) | 50abc3e7... 80.1 min (52 orders); 0a7d7a81... 79.9 (99); b4288d9c... 76.6 (115); 2b38c2df... 76.1 (168); 310cc7ca... 70.6 (60) |

### Medium

| Question | Answer |
| :--- | :--- |
| M1 Order protocol efficiency | P5 fastest of the large protocols (45.5 min), P1 slowest (49.9); P6 is 60.2 min but only 792 orders; P7 has 19 orders |
| M2 Average order value by market | M4 2,900; M1 2,747; M2 2,635; M3 2,578; M6 2,516; M5 2,414 (currency not stated) |
| M3 On-shift partners vs delivery time in peak hours | 30 or fewer: 55.6 min; 31-59: 50.8; 60-92: 50.8; 93+: 52.3. Weak, not a steady decline (overall correlation 0.07) |
| M4 Items vs delivery time (regression) | Each extra distinct item adds 1.80 min (significant); total items has no significant effect (p = 0.27); R-squared 2.4% |
| M5 Delivery time through the week | Monday and Saturday slowest, Wednesday and Friday fastest (7.3-minute range) |
| M6 Price range vs subtotal | Correlation 0.51; average range (max - min item) 473 |
| M7 Category and above-average order value | Share above the 2,685 average: italian 63.8%, japanese 53.7%, thai 51.4%, pizza 48.2% ... sandwich 21.7%, fast 12.4% |
| M8 Std dev by market and category | Market 1 most variable (21.4 min), Market 2 least (16.7). By category 17.2 (mexican) to 20.5 (japanese) |
| M9 Partner availability by market | Busy/on-shift ratio: M1 0.97, M4 0.95, M6 0.93, M2 0.92, M3 0.91, M5 0.84; correlation with market delivery time 0.60 (6 markets only; M6 has partner data for 653 orders) |
| M10 First-time vs repeat customers | **Not possible:** no customer ID in the data |

---

## Recommendations

- **Peak-hour supply:** add partner capacity in hours 1 to 3, where 47% of orders arrive and delivery is about 9 minutes slower.
- **Market 1:** investigate why it is 4.5 minutes slower and the least predictable.
- **Slow stores:** review the 5 stores averaging 70 to 80 minutes.
- **Protocol 6:** check the integration behind this code (60 minutes on average).
- **Data:** capture customer ID, target time and stage timestamps to support the analyses this dataset cannot.

---

## Repository Structure

```text
├── data/
│   └── dataset1.csv
├── working_files/
│   └── Porter_Delivery_Latency_Working_File.xlsx
├── screenshots/
│   └── porter_delivery_dashboard.png
├── presentation/
│   └── Porter Delivery Analysis.pptx
└── README.md
```

### Excel workbook

| Sheet | Contents |
| :--- | :--- |
| Executive_Summary | KPIs, comparisons and cleaning log (live formulas) |
| Dashboard | KPI tiles and four charts |
| Basic_Questions | Q1 to Q10 |
| Medium_Questions | M1 to M10 |
| Notes_Definitions | Definitions, assumptions, limitations |
| Data | The 197,194 cleaned orders plus helper columns (delivery minutes, hour, weekday, category group) |

Computed outside Excel: only the list of 5 store IDs in Q10 (picked from stores with 50+ orders) and the category-group label. Everything else is a formula.

---

## Limitations

- **Association, not causation.** Results show what goes with slower delivery, not why.
- **No target time:** no SLA breach rate is reported; speed is described with minutes and the 30/45/60-minute shares.
- **Small groups are noisy:** protocol 6 and 7, rare categories and stores with few orders; Market 6 partner data is thin.
- **Hour labels:** timestamps look UTC-like, so local meal-time interpretation would be a guess.
