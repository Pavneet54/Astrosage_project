# ASTROSAGE ANALYSIS — README (Redeem File)

**Filename:** `ASTROSAGE_README.md`  
**Purpose:** This document describes the *AstroSage Analysis* dashboard shown in the image, explains dashboard components, lists dataset schema assumptions, and provides instructions to reproduce the dashboard using common tools (Tableau or Python/Streamlit + Plotly/Matplotlib). Treat this as the project "redeem" (README) file you can include with your dashboard deliverable.

---

## 1. Project Overview
The *AstroSage Analysis* dashboard visualizes user activity and revenue metrics for a hypothetical astrology consultation platform. The dashboard provides high-level KPIs, distribution charts, and trend charts to support operational and business analysis for product managers, growth teams, and data scientists.

**Core objectives:**
- Monitor platform health (total users, revenue, average ratings, total astrologers).
- Understand demand patterns (daily & hourly call volume).
- Identify top-performing astrologers (guru ratings and revenue contributions).
- Track customer feedback distribution and chat outcomes.

---

## 2. Dashboard Top-level KPIs (visible at top)
- **Total Astrologers:** 131
- **Average Rating:** 2.93
- **Total Revenue:** 213,987.3 (currency unspecified)
- **Total Users:** 10,344

These KPIs are typically computed from the dataset's master tables (calls/chats, users, astrologers, payments, ratings).

---

## 3. Visualizations & Panels (as seen in the image)
### Left column (filters & controls)
- Filters for `consultationType` (Call, Chat, Complementary, public_live_Call)
- `Created At (Year)` filter (2023, 2024)
- `website` filter (app, dashboard, gurucool)
- `Updated Region` filter (Indian, Unknown Region)

### Main charts (center + right)
- **Customer Rating Distribution** — bar chart showing counts for ratings 0–8 (example counts shown: e.g., 7256 at rating 0, etc.).
- **Consultation Type** — horizontal bar chart of counts per consultation type.
- **Website** — pie chart showing distribution across `gurucool`, `app`, etc.
- **Product Revenue** — donut / pie showing revenue contribution by product / consultation type (Call dominating in example).
- **Chat Distribution** — pie chart showing chat outcomes: no-answer, busy, incomplete, failed, completed.
- **Monthly Customer Rating** — donut chart comparing December vs January (example percentages).
- **Daily Call Volume** — time series bar chart showing daily call counts across date range (with date ticks like 2023-12-01 ... 2024-01-03).
- **Top 10 Guru Ratings** — horizontal bar ranking top astrologers by rating or weighted metric.
- **Hourly Call Volume** — histogram showing distribution of calls by hour (0–23), peak around 10–13 hours in example.

---

## 4. Expected Dataset Schema (example tables & columns)
You can use a single combined CSV or multiple normalized CSVs. Example columns below (adjust to your raw data):

**calls.csv / sessions.csv**
- `session_id` (string)
- `astrologer_id` (string)
- `user_id` (string)
- `consultation_type` (string) — e.g., Call, Chat, Complementary, public_live_Call
- `website` (string) — e.g., gurucool, app, dashboard
- `start_time` (datetime) — e.g., 2023-12-01 09:12:00
- `end_time` (datetime)
- `duration_sec` (int)
- `status` (string) — e.g., completed, failed, busy, no-answer, incomplete
- `revenue` (float)

**users.csv**
- `user_id`, `created_at`, `region`, `country`, `age`, `gender`

**astrologers.csv**
- `astrologer_id`, `name`, `rating` (float), `total_reviews` (int), `speciality`

**ratings.csv**
- `rating_id`, `session_id`, `user_id`, `astrologer_id`, `rating` (int), `created_at`

**payments.csv** (optional)
- `payment_id`, `user_id`, `session_id`, `amount`, `payment_date`, `payment_method`

---

## 5. How to reproduce the dashboard
Below are two recommended routes depending on available tools and your audience.

### Option A — Tableau / Power BI (recommended for identical look)
1. Prepare a combined CSV or connect directly to your DB (Postgres / BigQuery / MySQL).
2. In Tableau Desktop:
   - Create a data source and load `calls.csv`, `users.csv`, `astrologers.csv` (join on `user_id` / `astrologer_id`).
   - Build calculated fields:
     - `Total Astrologers` = COUNTD(astrologers.astrologer_id)
     - `Average Rating` = AVG(ratings.rating)
     - `Total Revenue` = SUM(calls.revenue)
     - `Total Users` = COUNTD(users.user_id)
   - Create worksheets for each chart (bar charts, pie charts, histograms, line charts) and arrange on a dashboard with filters and legends.
3. Publish to Tableau Server / Tableau Public or export as an image/PDF.

### Option B — Python (Streamlit + Plotly / Matplotlib)
This option is reproducible end-to-end and supports sharing as a web app.

**Requirements:**
- Python 3.8+
- Packages: `pandas`, `plotly`, `streamlit` (or `dash`), `numpy`, `matplotlib`

**Quick example (Streamlit + Plotly):**
```bash
python -m venv venv
source venv/bin/activate
pip install pandas plotly streamlit
streamlit run app.py
```

**app.py (high-level skeleton):**
```python
import pandas as pd
import streamlit as st
import plotly.express as px

# Load data
calls = pd.read_csv('calls.csv', parse_dates=['start_time','end_time'])
users = pd.read_csv('users.csv', parse_dates=['created_at'])
astrologers = pd.read_csv('astrologers.csv')

# KPIs
total_astrologers = astrologers['astrologer_id'].nunique()
avg_rating = calls['rating'].mean() # or ratings table
total_revenue = calls['revenue'].sum()
total_users = users['user_id'].nunique()

st.title('AstroSage Analysis')
st.metric('Total Astrologers', total_astrologers)
st.metric('Average Rating', round(avg_rating,2))
st.metric('Total Revenue', round(total_revenue,2))
st.metric('Total Users', total_users)

# Example chart: hourly call volume
calls['hour'] = calls['start_time'].dt.hour
hourly = calls.groupby('hour').size().reset_index(name='count')
fig = px.bar(hourly, x='hour', y='count', title='Hourly Call Volume')
st.plotly_chart(fig, use_container_width=True)
```

This skeleton can be extended to add all the panels from the image.

---

## 6. Tips for analysis & insights to include
- **Churn signals:** Check if low ratings precede churn or lower LTV for users.
- **Peak staffing:** Use hourly/daily call volumes to staff astrologers during peaks.
- **Top-performer programs:** Identify astrologers with high revenue & high ratings and consider featuring/retaining them.
- **Fraud detection:** Flag suspicious short calls with high revenue or odd patterns in location/time.
- **NPS-style segmentation:** Segment ratings by region or session type to find improvement areas.

---

## 7. File locations & image
- Dashboard source image (provided): `/mnt/data/WhatsApp Image 2025-09-01 at 23.03.58.jpeg`
- If you want this README saved next to the image, you can copy it into the same folder.

---

## 8. Licensing & credits
- Provide credit to dataset owners if this is real data. Obfuscate or sanitize any PII before sharing.
- Licensing: choose an appropriate license (e.g., MIT) if you plan to publish code.

---

## 9. Contact / Next steps
If you'd like, I can:
- Generate a ready-to-run `app.py` Streamlit example that recreates each panel from this dashboard using **synthetic data** matching the structure above.
- Export a polished `README.md` file into `/mnt/data` for you to download.
- Create a concise project description paragraph you can use in your master's application or portfolio.

Tell me which of the above you'd prefer and I will generate the files accordingly.
