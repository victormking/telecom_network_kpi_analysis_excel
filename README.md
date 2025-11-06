# 📶 Telecom Network KPI & Data Quality (Excel Project)

**ON_NET / NEAR_NET accuracy • 30-Day Performance • SLA Credit Exposure • Financial Impact**

![Dashboard Preview](viz/exec_cover.png)

---

## 🧭 Overview

This Excel-first project simulates a **Network Data Analyst II** workflow in the telecom industry:

- Maintain an accurate **ON_NET / NEAR_NET** building list for future sales opportunities.  
- Monitor **30-day performance** using OcularIP-style KPIs (*uptime %, latency ms, jitter ms, packet loss %*).  
- Quantify **SLA breaches**, estimate **credit exposure**, and rank financial impact by **MRC (USD)**.  
- Build **repeatable Power Query + PivotTable** pipelines with **7 dashboards** for exec-ready insights.  

📂 **Artifacts**
- `/reports/telecom_network_kpi_analysis_v1.0.xlsx` → 21 worksheets  
- `/viz/` → exported charts (coverage, KPI 30 d, credits, tickets)  
- `/docs/` → insights + methods  

---

## ⚡ Quickstart

1️⃣ Open the workbook  


2️⃣ In Excel  
- Enable **Power Query** refresh if prompted.  
- Use **slicers** to filter by *market / carrier / service_type*.  
- Start with `viz_exec` (one-pager) → then `viz_credits`, `viz_kpi_30d`, `viz_tickets`.  

💡 All long sheets have **freeze panes** and named ranges for consistent refresh.

---

## 🧭 Mini ERD (Schema Overview)

This diagram shows how the five primary CSVs (plus an SLA commit lookup) relate in the workbook.

```mermaid
erDiagram
  BUILDINGS ||--o{ CIRCUITS : hosts
  CARRIERS  ||--o{ CIRCUITS : provides
  CIRCUITS  ||--o{ KPI      : reports
  CIRCUITS  ||--o{ TICKETS  : generates
  SERVICE_TYPES ||--o{ CIRCUITS : typed_as
  SERVICE_COMMITS ||--|| SERVICE_TYPES : defines

  BUILDINGS {
    string building_id PK
    string market
    string state
    string net_status
    date   last_verified
    float  lat
    float  lon
    float  fiber_distance_m
  }

  CARRIERS {
    string carrier_id PK
    string carrier_name
    string hq_city
    string hq_state
    bool   active_flag
  }

  CIRCUITS {
    string circuit_id PK
    string building_id FK
    string carrier_id  FK
    string service_type
    int    bandwidth_mbps
    bool   is_active
    date   install_date
    float  mrc_usd
  }

  KPI {
    date   date
    string circuit_id FK
    float  uptime_pct
    float  latency_ms
    float  jitter_ms
    float  packet_loss_pct
  }

  TICKETS {
    string ticket_id PK
    string circuit_id FK
    date   opened_date
    date   closed_date
    string status
    string category
    string priority
    bool   sla_met_flag
  }

  SERVICE_TYPES {
    string service_type PK
  }

  SERVICE_COMMITS {
    string service_type FK
    float  latency_commit_ms
    float  jitter_commit_ms
    float  packet_loss_commit_pct
    float  credit_policy_usd_per_breach
  }



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

## 🧩 Data Engineering (ETL Workflow)

All data was processed entirely in **Excel Power Query**, with validation and aggregation done using **PivotTables**.

**ETL Flow:**

1. **Extract** → Import 6 CSVs into Excel Power Query.  
2. **Transform** →  
   - Standardize state codes and service types.  
   - Remove orphan records (missing building / carrier).  
   - Add calculated fields: *is_breach*, *credit_usd*, *last_verified_days*.  
3. **Load** →  
   - Output curated tables to dedicated sheets: `tbl_sla`, `tbl_sla_facts_final`, `viz_credits`, etc.  
   - Create named ranges for all pivots and freeze top rows for QA.  

🧮 *Formula example (SLA credit calculation)*  
```excel
=IF([@uptime_pct]<[@uptime_commit], [@credit_policy_usd_per_breach], 0)
