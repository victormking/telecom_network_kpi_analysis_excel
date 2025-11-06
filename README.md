# 📶 Telecom Network KPI & Data Quality (Excel Project)

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
<p align="center">
  <img src="https://img.shields.io/badge/Microsoft_Excel-217346?style=for-the-badge&logo=microsoftexcel&logoColor=white" />
  <img src="https://img.shields.io/badge/Power_Query-00B294?style=for-the-badge&logo=powerbi&logoColor=white" />
  <img src="https://img.shields.io/badge/Power_Pivot-F2C811?style=for-the-badge&logo=powerbi&logoColor=black" />
  <img src="https://img.shields.io/badge/Windows_11-0078D6?style=for-the-badge&logo=windows11&logoColor=white" />
</p>

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
## 🎯 Project Objectives & Context  

This project recreates the **daily analytics workflow** of a **Network Data Analyst II** at a telecom company such as Segra — a role responsible for validating network coverage data, tracking SLA performance, and quantifying financial exposure from SLA breaches.  

### **Core Objectives**  
1. **Data Accuracy:** Verify the ON_NET / NEAR_NET building list ( > 500 k records in production ) for duplicates, orphan keys, and outdated entries.  
2. **Performance Monitoring:** Aggregate OcularIP-style metrics (*uptime %*, *latency ms*, *jitter ms*, *packet loss %*) to assess network stability by market and carrier.  
3. **Financial Accountability:** Estimate monthly SLA credits using contract thresholds + MRC (USD) exposure, helping reduce carrier credit payouts.  
4. **Operational Alignment:** Connect ticket categories with SLA breaches to pinpoint recurring root causes.  
5. **Repeatable Workflow:** Build a refreshable, **Excel (Power Query + PivotTables)** pipeline that scales to large CSVs without migrating to SQL or Power BI.  

### **Business Context**  
Telecom operators like Segra manage enterprise and carrier clients through ON_NET / NEAR_NET fiber footprints.  
Each SLA breach produces a **credit liability** and potential **customer churn risk**.  
By monitoring these metrics and maintaining clean network data, analysts directly drive both **revenue growth** and **cost savings**.  

> 🧮 **Protect revenue through SLA tracking** + 🌐 **Drive sales through clean ON_NET data**

---

## ⚙️ Methodology & Workflow (Phases 0 – 7)  

The workflow mirrors a mini ETL pipeline inside Excel — from raw CSVs to insights and dashboards.  

| **Phase #** | **Stage Name** | **Description / Key Actions** | **Output Sheet(s)** |
|:--:|:--|:--|:--|
| **0** | 🧾 Setup & Import | Load five CSVs (`buildings`, `circuits`, `carriers`, `ocularip_kpis`, `tts_tickets`) via Power Query and define data types / keys. | Raw tables |
| **1** | 🔍 Data Quality Checks | Identify duplicates, orphan foreign keys, and stale verifications (`> 365 days`). | `checks` |
| **2** | 🏗️ Coverage & Inventory | Summarize ON_NET vs NEAR_NET buildings; active circuits and MRC by carrier & service type. | `pvt_services`, `pvt_coverage` |
| **3** | 📊 SLA Fact Build | Join KPIs + Circuits + Commits → compute `_fail` flags for latency, jitter, loss. | `tbl_sla` |
| **4** | 💸 Credit Exposure | Apply credit policy (`credit_day = IF(any_fail, rate × mrc_usd, 0)`) and aggregate by carrier & market. | `tbl_sla_facts_final`, `pvt_credit` |
| **5** | 🧰 Tickets & Root Cause | Calculate avg resolution hours & top categories; link tickets to SLA fails. | `pvt_tts` |
| **6** | 📈 Visualization Build | Create 7 executive dashboards (coverage, services, KPI 30 d, credits, tickets, trends). | `viz_*` series |
| **7** | 🧠 Insights & Recommendations | Summarize findings on data quality, performance, financial exposure, and operations. | `/docs/business_insights.md`, `/viz/` |

**Pipeline Summary:**  
`buildings + circuits + carriers + ocularip_kpis + tts_tickets → Power Query joins → credit calculation → pivot dashboards → executive insights`

