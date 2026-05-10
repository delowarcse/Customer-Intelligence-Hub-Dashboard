# Customer Intelligence Hub

An interactive analytics dashboard for customer segmentation, retention analysis, behavioral insights, and strategic decision-making -- built with Power BI-style visualization and Spark SQL on the backend.

---

## Overview

The Customer Intelligence Hub turns raw transactional data into actionable intelligence across four analytics pillars:

- **Segmentation** -- RFM-based (Recency, Frequency, Monetary) segmentation across six behavioral tiers with CLV benchmarking
- **Retention** -- Cohort retention curves, time-to-churn distribution, and churn reason breakdown
- **Behavior** -- Purchase frequency, channel preferences, journey funnel, and peak engagement analysis
- **Strategy** -- CAC vs CLV scatter analysis, revenue opportunity sizing, and 12-month predictive forecast

---

## Dashboard Tabs

| Tab | Description |
|---|---|
| Overview | 8 KPI cards, revenue trend vs target, acquisition vs churn, strategic insight callouts |
| Segmentation | RFM donut chart, segment performance table, CLV bar chart, recency vs frequency bubble matrix |
| Retention | Cohort retention curves (Jan-Apr 2026), churn reason breakdown, time-to-churn distribution |
| Behavior | Purchase frequency, day-of-week radar, journey funnel, channel split, peak purchase hours |
| Strategy | Revenue opportunity by segment, CAC vs CLV scatter, 12-month forecast with confidence band |

---

## Tech Stack

| Layer | Technology |
|---|---|
| Visualization | Chart.js 4.4.1, HTML/CSS, interactive JS |
| Query engine | Apache Spark SQL |
| Data format | Parquet (partitioned by year/month/region) |
| Optimization | Adaptive Query Execution (AQE) |
| Scale | 48M+ records across 6 tables |

---

## Spark SQL Features Demonstrated

- Window functions -- `RANK() OVER (PARTITION BY segment ORDER BY revenue DESC)`
- Running totals -- `SUM() OVER (ORDER BY month ROWS UNBOUNDED PRECEDING)`
- CTE-based aggregation with `LAG()` for month-over-month growth
- Partitioned broadcast joins using `/*+ BROADCAST(c) */` hint
- Cohort retention using `FIRST_VALUE() OVER (PARTITION BY cohort_month)`

### AQE Optimizations Applied

- Partition coalescing -- 200 shuffle partitions reduced to 42 post-join
- Broadcast join conversion -- 14 MB customers table auto-broadcast, eliminating shuffle
- Predicate pushdown -- year=2025 partition filter skips 7 of 8 partition folders
- Column pruning -- only 4 of 12 parquet columns read, reducing I/O by 67%

---

## Data Schema

```
orders          -- order_id, customer_id, order_date, amount, status
                   PARTITIONED BY (year, month)

customers       -- customer_id, segment, region, signup_date, clv
                   PARTITIONED BY (region)

events          -- event_id, customer_id, event_type, event_ts, session_id
                   PARTITIONED BY (event_date)

products        -- product_id, category, price, margin
sessions        -- session_id, customer_id, channel, duration
returns         -- return_id, order_id, reason, amount
```

---

## Key Metrics (Sample Dataset)

| Metric | Value |
|---|---|
| Total customers | 42,380 |
| Retention rate | 76.4% |
| Average CLV (12-mo) | $1,847 |
| Churn rate | 23.6% |
| Champions segment revenue share | 38% of total (12% of base) |
| At-Risk recoverable revenue | $2.1M |

---

## Getting Started

```bash
# Clone the repository
git clone https://github.com/your-username/customer-intelligence-hub.git
cd customer-intelligence-hub

# Install dependencies
npm install

# Configure Spark connection (edit config/spark.conf)
cp config/spark.conf.example config/spark.conf

# Launch dashboard
npm start
```

> Requires Apache Spark 3.3+, Node.js 18+, and a configured Spark cluster or local mode.

---

## Project Structure

```
customer-intelligence-hub/
├── dashboard/          # Frontend HTML/JS/CSS
│   ├── index.html
│   ├── charts.js
│   └── styles.css
├── queries/            # Spark SQL query library
│   ├── segmentation.sql
│   ├── retention.sql
│   ├── behavior.sql
│   └── strategy.sql
├── config/             # Spark and data source config
├── data/               # Sample parquet data (subset)
└── README.md
```
---

## Author

**Delowar Hossain**, PhD -- Senior Data Scientist  
Calgary, AB, Canada  

---

## License

MIT License. See [LICENSE](LICENSE) for details.
