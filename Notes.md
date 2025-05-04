# Learning PowerBI

## Understanding Power BI
Power BI is a Business Intelligence tool by Microsoft that allows users to:
1. Connect to various data sources
2. Transform data (Power Query)
3. Model data (Power BI Data Model)
4. Visualize data through reports and dashboards
5. Share insights via Power BI Service

## Power BI Workflow Overview
`Data Sources ➜ Power Query ➜ Data Modeling ➜ Visualization ➜ Power BI Service ➜ Collaboration`

## End to End Data Processing Solution with PowerBI

### Stage 1: Understanding Business Requirements
**Goal:** Clarify what the business wants to measure and why.

**Deliverables?**
- KPIs and metrics (e.g., Total Sales, Customer Churn Rate)
- Required dimensions (e.g., Time, Region, Product)
- Stakeholders (e.g., Sales Director, Finance Manager)
- Report format (dashboard or detailed report)

**Example:**
“The Sales Director wants to track monthly revenue trends by region and product category, with the ability to drill into store-level data.”

### Stage 2: Data Sourcing
**Task:**
- Connect using Power Query connectors
- Verify access permissions
- Profile and clean data (identify duplicates, nulls, anomalies)

**Default 1000 rows but can be changed (lower left) - no row lmits but it's data size limit**
Power BI Free:Data Size: 
* 1 GB per dataset and no workspace
Power BI Pro:Data Size: 
* 10 GB total storage for the workspace, including datasets, reports, and dashboards. 
* Each dataset within a workspace can be a maximum of 1 GB. 

**Data Profiling** 
- diagnose errors and inconsistency
- how transformation affected your dataset 
- quick analysis to help decide what transformations to use

PowerQuery > View Ribbon > Data Preview
* Column Distribution (distinct values and unique- once only)
* Column Profile (per column only but detailed stats(avg, max, min, std dev) , distribution, unique and distinct) 
* Column Quality (valid, error, missing)	

### Stage 3: Data Preparation (ETL with Power Query)


### Stage 4: Data Modeling
**Goals:**
- Create relationships between tables
- Build a semantic model (Star Schema preferred)
- Create hierarchies and date tables


**Best Practices:**
- Use surrogate integer keys
- Hide columns not meant for reporting
- Validate relationships (cardinality and direction)

### Stage 5: DAX Measures and Calculations
- Calculated columns (row context)
- Measures (filter context — use measures instead of calculated columns unless row-by-row context is required.)
- Time intelligence (YTD, MoM)

### Stage 6: Visualization in Power BI Desktop
- Slicers
- Charts
- Bookmarks for story-telling
- Drill-throughs and tooltips for deeper analysis
- Reporting (Overview - EDA/Categories)

### Stage 7: Publishing to Power BI Service
1. Click Publish in Power BI Desktop
2. Choose appropriate workspace
3. Verify datasets and visuals render correctly

### Stage 8: Configuring Refresh & Gateway
**Data Gateway:**
- Personal: For development/testing
- Standard (Enterprise): Production use

**Scheduled Refresh:**
Set frequency: daily/hourly/weekly

**Store credentials securely in Power BI Service**
**Use SQL views or stored procedures to abstract complex logic. (as necessary)**

### Stage 9: Testing and Validation
**Actions:**
- Validate metrics against source system
- Cross-check totals
- Simulate user filters (e.g., by Region, Date)
- Automate testing using Excel pivot tables or Power BI Test Datasets

### Stage 10: Deployment and Sharing
**Options:**
- Share via report link
- Publish to Power BI App
- Embed in Teams or SharePoint
- Use Deployment Pipelines (Dev ➜ Test ➜ Prod)

**RLS (Row-Level Security):**
`[Region] = USERPRINCIPALNAME()`
Define security roles in the Desktop model and map users in Power BI Service.

#### Stage 11: Dashboard Creation
**Dashboards (Power BI Service only):**
- Pin visuals from different reports
- Use for executive summaries
- Enable Q&A, alerts, and mobile view

### Final Outcome
- Dataset -	Clean, modeled dataset with DAX
- Reports	- Paginated or interactive reports
- Dashboards	- KPI-focused, mobile-ready summaries
- Documentation -	Data dictionary, architecture diagrams
- Refresh Setup -	Automated refresh with gateway
- Access Control -	RLS and workspace permissions

```[Data Sources] ───► [Power Query ETL] ───► [Data Model] ───► [Report Visuals]
   SQL, Excel,        Transformations         Star Schema         Charts, KPIs
   APIs, etc.            (M Code)               (DAX)               Filters

                                      │
                                      ▼
                         [Power BI Service / Gateway]
                          └─ Scheduled Refresh
                          └─ Workspace Sharing
                          └─ App Deployment```
