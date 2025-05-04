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

- Promote or unpromote top row to get correct headers
- Remove blank rows and unnecessary columns (irrelevant data)
- Reorder columns
- Rename columns
- Add average values to null values (if allowed)
- Correct typos and data entry errors (specially text e.g removing extra char)
- Check consistent data formatting or uniform capitalization (e.g for text USA vs United States of America)
- Remove leading and trailing whitespaces
- Remove punctuations and control characters
- One column should only store a piece of information (e.g split country and city as necessary)
- Or if data/business rules permit combine pieces of information (e.g firstName and lastName)
- Splitting columns (delimiter, number of char, position, lower/upper, digit/non-digit)
- Remove duplicate values
- Text casing (upper, lower, proper)
- Prefixing and suffixing
- Arithmetic and logarithmic transformations (square, log, square root etc.)
- Outliers (remove or add cielings and floors)
- Make sure correct data types are used
- Table and column names should be short and descriptive
- Date values (extract specific properties as necessary)
- Numerical values free from missing values, errors and outliers
	use absolute value, multipliying, adding scalar value, rounding
- Reshaping data - happens before loading into the model for better analysis
	* pivot (going to wide format - row values that can be categories turned into column)
        e.g convert some rows into columns for better presentation and categories
	* unpivot (going to long format - columns into values of a row)
        e.g for better catergories in columns and prevent nulls on rows
	* transpose (rows to columns - vice versa) 
        e.g products are in column rather than rows then column should be price, cost, profit margin  etc.
- Aggregating data (change in granurality) - Static Aggregation (before load) -	Power Query / ETL (Group By- sum, avg, median, min, max, count, custom)
	**Compared with Dynamic Aggregation (after load) -> DAX Measure (Data Analysis) Aggregations**
	You can duplicate tables -> use aggregations -> for better data structure for analysis and visualization
		
	**Why Do We Sometimes Duplicate Tables for Aggregations?**
	because if we already have fact and dimension tables connected via a star schema, why touch it?
	- Aggregated tables improve performance
	- They simplify specific reporting needs without overloading your main model
	- They pre-calculate summaries that would be costly or complex to recalculate dynamically in visuals with DAX
- Combining Data (APPEND) many separate files or too large to store in one file e.g  stack tables ike Jan + Feb sales
	- Add rows (vertical)
	- Make sure column names must be the same (if not null values will appear in columns)
- Merging Data (MERGE) tables have relationship between one or more column - star or snowflake - combine to make flat structure
	- Add columns (horizontal)
	- Through keys
	- Inner Join, LeftJoin, RightJoin, FullJoin
- Custom Columns (Make sure this are columns that should be static as loaded cannot be changed)
	- Add Columns > Custom Columns (Data Mashup)
	- Case sensitive, reference several columns, logical/conditional operators, numerical operators, &(text), comparison operators
	- Mind your data types (may result to errors)



### Stage 4: Data Modeling
Data Modeling (80% PowerQuery - change data before loading and 20% PowerBI - change data appearance e.g rounding usually here)

**What’s In It for Data Modeling (reporting level)?**
- Fact & Dim tables in a star schema are for detailed, granular data (like individual transactions, line items, etc.)
- But not every report needs that detail. Some reports need only summarized data
    Creating an aggregated table lets you:
    - Avoid complex DAX calculations
    - Improve performance — aggregations load and query faster
    - Prevent visuals from scanning millions of rows unnecessarily
    
    Why Not Just Use Star Schema Only?
	- Star schema handles the base data layer
	- Aggregated tables optimize for **SPECIFIC** reporting scenarios
	- Power BI’s engine (VertiPaq) is fast, but if you’re working with millions of rows, lots of slicers, and complex visuals, aggregated tables:
		- Reduce report load time
		- Avoid performance hits from complex DAX or filters

    ```Example Coca-Cola Use Case:
        You have:
        FactSales (50M rows of transactions)
        DimProduct
        DimDate
        DimCustomer

        But you want a report that shows:
        Total Sales per Region, per Year

        Instead of summing FactSales[Amount] dynamically in visuals (which would scan millions of rows) 
        You create an aggregated table:
        
        Region	Year	TotalSales
        East	2023	12,000,000
        West	2023	9,500,000
        
        Then connect DimRegion and DimDate to this aggregation
        → Faster reports
        → Cleaner, simpler visuals
        → Still linked to your star schema context```

**Goals:**
- Create relationships between tables
- Build a semantic model (Star Schema preferred)
- Create hierarchies and date tables

**Usually done by column extraction (take columns from table and break them into another table) - MAINTAIN KEYS in both table**
	Sample Workflow: 
    ```Clean datasets -> append (fact table) -> duplicate then create dim table from duplicate -> remove duplicate rows in dim table 
	-> remove columns in fact table that are in the dim table -> maintain keys for relationship```

**Data Modeling Schemas**
1. Dimensional Modeling (Kimball Model)
- facts - metric from a business, lots of rows but fewer columns
- dimensions - context surrounding a business process, short and wide, static or slowly changing, business concepts (date, payment type) or entities (salesperson, lender, property)
- fact and dimensions table combine creates star schema (used in data warehouses - PowerBI is optimized for star schema)
		
**Sample creation of star schema:**
	```Identify entities -> duplicate fact table -> make the duplicate as dim table -> remove columns already in dim to the fact -> maintain keys -> check relationship in model view```
		
2. Snowflake Schema - extension of star (relationships between dimensions)
- Fact (with SubCat key) -> SubCat (with Cat key) -> Cat (with Cat key) 
- less duplication than star schema
- updating records is more efficient
- less faster when it comes to visualization than star (as in cases where star joins dim + fact while snow join dim + dim + fact)
e.g Product Key , Name, SubCat, Cat

**Data modeling storage layer vs reporting layer**
- Data warehouse modeling (storage layer) - 	Structuring raw data for efficient, reliable storage and efficient querying
- PowerBI modeling (reporting layer) - Creating meaningful relationships, aggregations, and KPIs for analysis and presentation  (built for end-user interaction, drill-downs, slicers, and dynamic aggregations - that why Dim tables are very essential specially for visualization e.g filtering, slicers, etc.)
- PowerBI more like recreates relationships visually from datawarehouse as PowerBI using PowerQuery import and transform data it got from datawarehouse.
    
		
	```[ Raw Data Sources ]
		↓
	[ Data Ingestion (ETL/ELT — SQL, Python, Azure Data Factory, Glue, SSIS) ]
		↓
	[ Data Warehouse (SQL Server, Snowflake, Redshift, Azure Synapse, BigQuery) ]
		↓
	[ Data Marts (Sales Mart, Finance Mart, Marketing Mart) ]
		↓
	[ BI Tools (Power BI, QuickSight, Tableau) ]
		↳ Data Import (Power Query)
		↳ Data Modeling (Relationships)
		↳ DAX / Measures / Calculations
		↳ Dashboards & Reports```


**Date Dim Table** 
- After loading and cleaning your data in Power Query, but before creating DAX measures and visuals.
- Very essential specially for time intelligence reporting (filtering, custome calendar view, use for time-intelligence functions e.g DATEDIFF() for difference in days)
	
Creating:
	1. Host in database - so just pull it in datawarehouse but needs to update
	2. Store data in a file - need to update
	3. Create using DAX - MODELING TAB > Create table > CALENDAR() with specific or using MIN() and MAX(), or CALENDARAUTO()  --- THIS IS A TABLE NOT A COLUMN		

	e.g but there are other ways
    ```Month_Year = 
		DISTINCT( 
		SELECTCOLUMNS ( 
			CALENDAR (DATE (1950, 1, 1), 
				TODAY()), 
				"Month", MONTH([Date]), 
				"Year", YEAR([Date]) 
		) 
	)```
	
**Relationships (based on keys)**	
- PowerBI requires single coloumn relationships
- Propagate filters
	- Relationships allow filters to flow from one table to another — 
	- Typically from dimension tables (like DimDate) to fact tables (like FactSales). You can set the filter direction as single or both
	- Allow cross-table calculations
		With relationships in place, you can perform calculations that combine data from multiple tables using DAX 
		(like Total Sales by Customer Region, or Sales vs Target comparisons)
		
- Relationships control how filters flow from one table to another, which directly affects how your DAX measures and visual slicers behave.
e.g Relationship: DimCustomer[CustomerKey] ⟶ FactSales[CustomerKey]
- If you try to filter DimCustomer values based on a slicer or logic applied in FactSales
	- With a single-directional relationship, this won’t work.
	- Because filters don’t flow from FactSales to DimCustomer by default.
			
    Let’s say you want to count customers who made a sale over $400
	This requires filtering FactSales first and pushing that back to DimCustomer
	If the filter direction is single, this won’t work

	You’d either:
	1. Need to enable bi-directional filtering	
	2. Or handle it in DAX using TREATAS(), RELATEDTABLE(), USERELATIONSHIP()
		
- Best Practice:
	- Keep relationships single-direction wherever possible (from Dimension to Fact)
	- There are cases bi-directional is needed
		e.g Location (dim) - Sales (fact) - Product (dim)
			There's two slicers location and products
			If e.g you filter AU and in fact table AU sales contains only sweaters
			But slicer (product) still shows sweaters, t-shirt, etc. but AU only have sweater sales? product slicer should only show sweater right?
			To correct this add bi-directional in product <--> sales 
		
    - PowerBI does not allow two separate paths from from table to another (Bi-directional filters cannot allow two sepaarate paths between two tables because filters would get confused about which path to take when filtering between those tables — causing ambiguity, possible circular references, or inaccurate results.
	- Use bi-directional relationships sparingly, only when you need cross-table filters to flow both ways**
	- OR use filter measures as work around by creating filter measure in DAX (return 1 if atleast one value otherwise 0) see. DataCamp Identifying Performance Problems (Advance Data Modeling)
		```Slicer_MyFactTable = INT (NOT ISEMPTY('My Fact Table'))```
		- Add a visual filter to the slicer and set where Slicer_MyFactTable = 1
		- If unsure, simulate filter behavior with DAX functions like TREATAS(), RELATEDTABLE(), CALCULATETABLE()

**Role Playing Dimensions**
Dimensions that can filter related facts differently
e.g Multiple date columns in fact table --> you can create multiple date dim for each but classify the name properly
	
**Create Multiple Relationship on dimensions table to a fact table but only one is active**
e.g Multiple date columns in fact table --> many relationship to single date dim but only one active
You can create DAX measure to specify which relationship to use by USERELATIONSHIP() 	
    Use inactive relationship for specific measure:
		```Measure Name = CALCULATE (<Measurement function>, USERELATIONSHIP(<Dimension Key Column>, <Fact Key Column>)```			
			
**Hierarchies** 
- will be used for data analysis and visualization)
- Table View -> Right Click highest level of heirarchy -> Create Hierarchy -> Add other columns part of heirarchy -> optional: hide coloumns you added to heirarchy
**Formatting** 
- Necessary for  proper sorting, categories, etc.
- Table View - PowerBI e.g rounding and data categories usually done here, summarization (sum, avg, etc.), sorting, etc.
**Data Category**
- Necesseary for proper data recognition by PowerBI e.g Geographic data (Table View)	




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
