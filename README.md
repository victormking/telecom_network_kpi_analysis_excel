# Telecom Network Data Quality & SLA KPI Analysis (Excel)

**Goal.** Build a clean, repeatable Excel workflow (Power Query + PivotTables) to:
- validate network data quality,
- monitor SLA performance (uptime, latency, jitter, loss),
- quantify **credit exposure** ($) by market/carrier,
- track **tickets & resolution** to guide root-cause actions.

**Why it matters (for a Network Data Analyst II):**
- The team maintains a 500k+ on-net/near-net list that fuels growth.
- Accuracy drives sales targeting; SLA reporting reduces cost of credits.
- This workbook is a practical template: refresh data → refresh pivots → export KPIs.

---

## Data & Schema

**Files (5):** `buildings.csv`, `circuits.csv`, `carriers.csv`, `ocularip_kpis.csv`, `tts_tickets.csv`  
**Helper:** `tbl_commits` (commit thresholds; DEFAULT row in this project)

`docs/schema.md` includes full column dictionary.

---

## Process (Phases)

0. **Setup** – Load 5 CSVs as tables via Power Query  
1. **Data Quality** – `checks` sheet (duplicates, orphan keys, stale building verifications)  
2. **Coverage & Inventory** – `pvt_services` for active circuits & MRC by carrier/service  
3. **SLA Facts** – Merge KPIs → Circuits → Carriers → Commits → add `_fail` flags & `any_fail`  
4. **Credit Exposure** – `credit_day = IF(any_fail, 0.05 * mrc_usd, 0)`  
5. **Tickets/Root Cause** – Pivot tickets (volume, category, SLA-related, resolution hours)  
6. **Executive Visuals** – 7 charts (below)  
7. **Insights & Recommendations** – see PDF in `/reports/`

---

## 15 Business Questions (selected answers)

1. Buildings needing re-verification (>365 days): **532** (see `checks`)  
2. % ON_NET vs NEAR_NET by market: see pivot in `pvt_services`  
5. Total active circuits & MRC by carrier: see `viz_1`  
7. Markets with lowest uptime %: `pvt_kpi_perf_30d` / `tbl_sla_facts_final`  
8. Carriers breaching latency commits most: `tbl_sla_facts_final`  
9. Bottom 10 circuits by uptime (30d): `viz_4`  
10–12. Estimated credits, credit by market, MoM trend: `viz_5`, `viz_7`  
13. Most common ticket categories: `viz_6`  
15. Total financial impact YTD: sum of `credit_day` in `tbl_sla_facts_final`

*(Full Q1–Q15 with details in the PDF.)*

---

## Visuals

1. **Active Circuits & MRC by Carrier**  
   ![viz](viz/01_services_by_carrier.png)

2. **Average Resolution Time (hours)**  
   ![viz](viz/02_avg_resolution_hours.png)

3. **Uptime % vs Latency Fail % by Carrier**  
   ![viz](viz/03_uptime_vs_latency_by_carrier.png)

4. **Bottom 10 Circuits by Avg Uptime (30d)**  
   ![viz](viz/04_bottom10_uptime.png)

5. **Estimated SLA Credits by Carrier (with MRC overlay)**  
   ![viz](viz/05_credits_by_carrier_mrc_overlay.png)

6. **Ticket Volume by Category**  
   ![viz](viz/06_tickets_by_category.png)

7. **Monthly Credit Trend (MoM)**  
   ![viz](viz/07_credit_trend_mom.png)

---

## Key Insights (high level)

- **Data Quality:** 532 buildings require re-verification; 0 orphan circuits/carriers.  
- **Performance:** Overall uptime is strong, but latency breaches concentrate in a subset of carriers/markets.  
- **Financial Exposure:** Estimated credits cluster by market; targeting those routes yields quick savings.  
- **Operations:** Ticket categories align with SLA fail patterns; focusing on the top 2 categories reduces churn risk.

---

## How to Refresh

1. Replace the 5 CSVs in `/data`.  
2. Open `reports/Segra_Telecom_KPI_Analysis_FINAL.xlsx`.  
3. **Data → Refresh All**.  
4. Charts update automatically; export PNGs from each `viz_*` sheet.

---

## Next Steps

- Expand `tbl_commits` by carrier/service for more granular targets.  
- Add market dimension to circuits (extend merge with buildings).  
- Publish a lightweight Power BI / Tableau companion for exec views.  
- Add a “data quality score” KPI per building/market.

---

## About Me

Victor King — Denver, CO  
M.S. in Sport Analytics (Syracuse, 2025)  
- LinkedIn: https://linkedin.com/in/victormking  
- GitHub: https://github.com/victormking


# Schema (short)

## buildings.csv
- building_id (PK), street_addr, city, state, lat, lon, net_status [ON_NET|NEAR_NET], fiber_distance_m, market, last_verified (date)

## circuits.csv
- circuit_id (PK), building_id (FK), carrier_id (FK), service_type, bandwidth_mbps, is_active (0/1), install_date, mrc_usd (currency)

## carriers.csv
- carrier_id (PK), carrier_name, hq_city, hq_state, contact_email, active_flag (0/1)

## ocularip_kpis.csv
- date, circuit_id (FK), uptime_pct, latency_ms, jitter_ms, packet_loss_pct

## tts_tickets.csv
- ticket_id, circuit_id (FK), opened_date, closed_date, status, category, priority, sla_met_flag (0/1)
- downtime_minutes (derived/assumed if included)

## commits (helper table in workbook)
- key, uptime_commit_pct, latency_commit_ms, jitter_commit_ms, loss_commit_pct
- DEFAULT row used in this project; can be extended by carrier/service

