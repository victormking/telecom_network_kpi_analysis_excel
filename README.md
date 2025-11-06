# 📶 Telecom Network KPI & Data Quality (Excel Project)

**ON_NET / NEAR_NET accuracy • 30-Day Performance • SLA Credit Exposure • Financial Impact**

![Dashboard Preview](viz/exec_cover.png)

---

## 🧭 Overview

This Excel-first project simulates a **Network Data Analyst II** workflow in the telecom industry:

- Maintain an accurate **ON_NET / NEAR_NET building list** to guide future sales opportunities.  
- Monitor **30-day network performance** using OcularIP-style KPIs (*uptime %, latency ms, jitter ms, packet loss %*).  
- Quantify **SLA breaches** → estimate **credit exposure** and rank impact by **MRC (USD)**.  
- Build **repeatable Power Query + PivotTable** pipelines with **7 dashboards** for exec-ready insights.  

📂 **Artifacts**
- `/reports/telecom_network_kpi_analysis_v1.0.xlsx` → 21 worksheets  
- `/viz/` → exported charts (coverage, KPI 30 d, credits, tickets)  
- `/docs/` → insights + methods  

---

## ⚡ Quickstart

1️⃣ Clone → open the workbook:  

/reports/telecom_network_kpi_analysis_v1.0.xlsx

2️⃣ In Excel  
- Enable **Power Query** refresh if prompted.  
- Use **slicers** on dashboards to filter by *market / carrier / service_type*.  
- Begin with `viz_exec` (one-pager summary), then drill into `viz_credits`, `viz_kpi_30d`, and `viz_tickets`.  

💡 All long sheets have freeze panes and named ranges for consistent refresh.

---

## 🧱 Data Schema (5 Primary CSVs)

| File | Purpose | Key Columns |
|------|----------|-------------|
| **buildings.csv** | ON_NET / NEAR_NET sites | building_id, state, market, net_status, last_verified 📅 |
| **circuits.csv** | Service inventory | circuit_id, building_id, carrier_id, service_type, bandwidth_mbps, mrc_usd |
| **carriers.csv** | Carrier lookup | carrier_id, carrier_name, hq_city, active_flag |
| **ocularip_kpis.csv** | Performance metrics | date, circuit_id, uptime_pct, latency_ms, jitter_ms, packet_loss_pct |
| **tts_tickets.csv** | Support tickets | ticket_id, circuit_id, opened_date, closed_date, category, sla_met_flag |

📑 `table_commit` = SLA threshold by service (latency ≤ x ms etc.)

---

## 📊 Workbook Structure (21 Sheets Total)

**Raw Data (6)** → `buildings`, `circuits`, `carriers`, `ocularip_kpis`, `tts_tickets`, `table_commit`  
**Checks (1)** → `checks` (state codes, orphans, last_verified > 365 d)  
**Work Tabs (4)** → `pvt_coverage`, `pvt_services`, `pvt_kpi_perf`, `pvt_tts`  
**SLA Facts (2)** → `tbl_sla`, `tbl_sla_facts_final` (breach flags + credits)  
**Visuals (7)** → `viz_exec`, `viz_coverage`, `viz_services`, `viz_kpi_30d`, `viz_sla_heat`, `viz_credits`, `viz_tickets`

---

## 🔍 15 Business Questions & Where They’re Answered

| # | Question | Data Source | Method |
|:-:|-----------|-------------|---------|
| 1 | Buildings needing state verification > 365 days | `checks` | Count + flag list |
| 2 | % ON_NET vs NEAR_NET by market | `pvt_coverage` | Pivot % |
| 3 | Invalid state codes | `checks` | Validation (0 expected) |
| 4 | Orphan circuits (no carrier/building) | `checks` | Validation (0 expected) |
| 5 | Active circuits & MRC by carrier | `pvt_services` | Sum / avg |
| 6 | Avg bandwidth per service type | `pvt_services` | Calc field |
| 7 | Markets with lowest uptime (30 d) | `pvt_kpi_perf` | Pivot + rank |
| 8 | Carriers breaching latency commits most | `tbl_sla_facts_final` | Breach rate |
| 9 | Worst 10 circuits by uptime | `tbl_sla_facts_final` | Rank + owners |
| 10 | Est. SLA credits this month | `pvt_credits` | Policy × breach count |
| 11 | Markets driving most credits | `pvt_credits` | Contribution % |
| 12 | MoM credit trend | `pvt_credits` | Pivot chart 📈 |
| 13 | Most common ticket categories | `pvt_tts` | Top-N |
| 14 | Do SLA fails align with tickets | SLA ↔ TTS | Cross-check |
| 15 | YTD $ impact of SLA breaches | `tbl_sla_facts_final` | Sum credits |

---

## 💡 Key Insights

- 🏢 **Coverage:** NEAR_NET density varies widely by market and correlates with higher latency/ticket volume.  
- ⚙️ **Performance (30 d):** Most outages come from specific market × carrier pairs → targeted fixes yield big gains.  
- 💵 **SLA & Credits:** ~10 % of circuits drive > 60 % of credit expense → focus remediation there.  
- 📊 **Financial Impact:** High-MRC circuits with repeat breaches are priority #1 for ROI and customer retention.  

👉 See [`/docs/business_insights.md`](docs/business_insights.md) for expanded analysis.

---

## 🗂️ Folder Map

telecom_network_kpi_analysis_excel/
├─ data/ # raw CSV inputs
├─ reports/ # main Excel workbook
├─ viz/ # exported dashboard images
├─ docs/ # insights & methods
│ ├─ business_insights.md
│ └─ methods.md
└─ README.md


---

## 🤝 Who Uses What

| Team | Uses | Outcome |
|------|------|----------|
| Sales | ON_NET coverage map | Target new builds / quotes |
| NOC / Ops | Breach alerts | Reduce credits & trouble tickets |
| Finance | Credit trend + MRC context | Track cost savings |
| Strategy | Build vs Optimize decisions | ROI-driven growth |

---


