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
Power BI Free:
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

    ```
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
    
		
	```
    [ Raw Data Sources ]
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
    ```
    Month_Year = 
		DISTINCT( 
		SELECTCOLUMNS ( 
			CALENDAR (DATE (1950, 1, 1), 
				TODAY()), 
				"Month", MONTH([Date]), 
				"Year", YEAR([Date]) 
		) 
	)
    ```
	
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
		```Measure Name = CALCULATE (<Measurement function>, USERELATIONSHIP(<Dimension Key Column>, <Fact Key Column>) ```			
			
**Hierarchies** 
- Will be used for data analysis and visualization
- Very useful to create Drilling-down visuals
- Table View -> Right Click highest level of heirarchy -> Create Hierarchy -> Add other columns part of heirarchy -> optional: hide coloumns you added to heirarchy
- Usually done to dim tables
- Update arrangement/order in Model View
**Formatting** 
- Necessary for  proper sorting, categories, etc.
- Table View - PowerBI e.g rounding and data categories usually done here, summarization (sum, avg, etc.), sorting, etc.
**Data Category**
- Necesseary for proper data recognition by PowerBI e.g Geographic data (Table View)	


**Best Practices for data modeling:**
- Use surrogate integer keys
- Hide columns not meant for reporting
- Validate relationships (cardinality and direction)

### Stage 5: DAX Measures and Calculations
**DAX** 
- Used after Power Query cleaning/transform data then load (calculated measures, time intelligence, filtered aggregations, rankings, kpis, conditionals)
- Calculated columns - calculated at data load and when data is refreshed (expand dataset WITHOUT EDITING THE SOURCE) - added to table as it evaluate each row 	
- Calculated measures - calculated at query time (as you intereact/filter) so, it's dynamic and more complex not stored in the table as it aggregate multiple rows
- Best practice to create a new table name = _Calculations so that measures are not lost among the tables and coloums
    e.g calc colums (price w/ tax column) vs calc measure (sum of price w/ tax)
- USE VAR and RETURN to simplify and induce readability for complex calc measures	

**DAX CONTEXT:**
- Calculated columns (row context)
    - Calculated Columns
	- Calculated Measure (Iterators e.g SUMX())
- Measures (filter context — use measures instead of calculated columns unless row-by-row context is required. - Applied before calculation is carried out)
    - Attribute in a row
	- Via Slicer
	- Through filter pane
    - Calculated Measure e.g using CALCULATE()
 	- Calculated filter will always override filters from visualization

**Time intelligence (YTD, MoM)**

### Stage 6: Visualization in Power BI Desktop
- Slicers
- Charts
- Bookmarks for story-telling
- Drill-throughs
    Drilling Options: (drill down turn on the drilling options)
	- Specific drill (by clicking e.g bar)
	- Expand to Next Level (for all values e.g Parent years -> Months but each months is combination of all years) and 
	- Expand All (show the next level without losing the parent grouping)
- Filtering visuals (by variables, top n, visual level, page level, report level, turning off interactions - specially for some cards)
- Tooltips for deeper analysis
- Reporting (Overview - EDA/Categories)
- Performance Tuning (check performance with PowerBi Performance Analyzer - View Tab)
    - DAX query slowness
		- Tune DAX operations
		- Improve Data Loading Performance
	- Visual display slowness	
		- Use less complicated visuals
		- Show less info in screen
		- Reduce number of visual on the page
- DAX CALC() measure is very useful workaround for visualizations

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

```
[Data Sources] ───► [Power Query ETL] ───► [Data Model] ───► [Report Visuals]
   SQL, Excel,        Transformations         Star Schema         Charts, KPIs
   APIs, etc.            (M Code)               (DAX)               Filters

                                      │
                                      ▼
                         [Power BI Service / Gateway]
                          └─ Scheduled Refresh
                          └─ Workspace Sharing
                          └─ App Deployment 
```

# Additional Notes:

**PowerBI Worflow:**
- Use PowerQuery to clean and transform data before loading to PowerBI
- Then we use TableView and DAX to create calculated columns, measures and apply business logics  for analysis and visuals


**How to handle incremental data using PowerBI:**
- Option 1: Append (Add to existing FactSales) in Power Query
    - Go to Power Query Editor
    - Use Get Data to import the new February file (CSV, Excel, SQL, etc.)
    - In Power Query:
        - Ensure column names and data types match FactSales
        - Append Queries → Append the February query to the existing FactSales query
        - Now FactSales contains both January + February
    - After that:
	    - Close & Apply to load the updated FactSales into the model
	    - All your visuals, measures, and relationships automatically pick up the new data

Bonus: If the Feb file is placed in the same folder every month, 
you can even set up a folder query in Power Query to auto-pick up new files as they arrive.

- Option 2: Overwrite or Replace FactSales Table If you treat each month as a full data dump (not incremental), you can replace the FactSales data source with a combined January + February dataset.
    - Replace the existing FactSales source file or table
    - Refresh the Power BI report
    - The visuals will reflect the updated data

This isn’t common for incremental data, but possible if that's how your files come.


**What About the Dimensions (Dim tables)?** Usually DimCustomer, DimProduct, and DimDate remain static.

Unless there are:
-New products
-New customers
-New date values

In that case:
-Either update the existing Dim tables
-Or set up append logic for Dim tables too (less common for DimDate though)

**Automating Incremental Files**
If you expect monthly files like this:

- Set up a Folder connection in Power Query
- Place all monthly files (January, February, March…) in a folder

Power Query will:
- Combine them automatically
- Standardize structure
- Load all months' data into FactSales

Now every time you drop a new file in the folder, hit “Refresh” in Power BI and your report updates.

```
[ January.csv ]  
[ February.csv ] 
        ↓  
    Power Query  
(Append / Combine)  
        ↓  
FactSales Table  
        ↓  
Data Model (with Dim tables)  
        ↓  
Reports / Dashboards
```


No need to rebuild visuals
No need to modify DAX (unless your business logic changes)
No need to re-create relationships
Just update the FactSales table — either by appending or replacing the source — and refresh the report.


**Does Power Query Apply the Same Cleaning Steps to New Data Automatically?**
Yes — if your query is set up correctly.

Here’s how it works:
- When you use Power Query to:
- Connect to a data source (January file)
- Apply cleaning/transformation steps (remove nulls, change types, split columns, etc.)
- Those steps are saved in the Applied Steps pane for that query.

If you append new data to that same query structure — those cleaning steps get applied to the new data too.

Example:
Let’s say your FactSales query has these Power Query steps:
Remove null rows
Split FullName column into FirstName and LastName
Change OrderDate to Date type
Filter out test sales data

Now, when you append February’s data into the same FactSales query (either directly or through a Folder connector 
that adds both January and February files together) — those same cleaning steps automatically run on the combined data.

That’s the beauty of Power Query — the transformations are query-wide, not file-specific.

**What If the New Data Has New Problems?**
Sometimes incremental files are messy in different ways:

- New unexpected null columns
- Different date formats
- New columns added
- Missing required fields

What you should do?
- Preview the February data separately in Power Query
- Apply the same cleaning steps or adjust them if necessary
- Make sure the structure matches the existing FactSales query
- Append the cleaned February data to FactSales query

Or

- If you’re using a Folder connector (combining multiple files):
- Set up your cleaning steps AFTER combining files
- That way, whether you add January, February, March, or April — the same cleaning logic applies to everything uniformly

Folder Example:
Imagine you have a folder like this:

```
SalesData/
 ├── January.csv
 ├── February.csv
 ├── March.csv

```

In Power Query:

- Connect to the folder
- Combine files → Power Query will generate a sample query (called Transform Sample File)
- Apply all your cleaning steps there

The steps get applied to every new file you drop into that folder

Best Practices:
- Set up consistent file templates for incremental data
- Use Folder connector for scalable, automated updates
- Apply cleaning steps after combining files
- Regularly preview new incremental data to catch unexpected issues
- Keep your Power Query steps clean, organized, and well-named



**Calculated Columns (DAX)**	

- Where it’s created - Inside Data Model (Table View / Model View) after data is loaded	
- When it’s calculated - After data load — dynamically evaluated and stored in the model	
- Language used - DAX (Data Analysis Expressions)	
- Performance - Adds overhead to the model size (since it lives in the model)	
- Depends on relationships / filter context? Yes — can access related tables via relationships	
- Use in measures / visual context - Can interact with DAX measures and slicers dynamically	



```Profit = [SalesAmount] - [Cost] (using related tables too)	Full Name = [First Name] & " " & [Last Name] (before loading data)```

**New Columns in Power Query (M)**
- Inside Power Query Editor during data cleaning/transformation
- Before data load — computed as data is loaded
- M Language (Power Query Formula Language)
- More efficient — handled before load; reduces model load
- No — operates row-by-row within the query, independent of relationships
- Static values — cannot be influenced by slicers or DAX calculations

**When to Use Which? (Best Practices)**

Scenario																									Recommended
You need to clean, shape, or derive columns before loading data												Power Query
You need to merge or transform columns using simple row-by-row logic										Power Query
You need a column dependent on relationships, calculated contextually, or influenced by slicers/filters		Calculated Column (DAX)
You need calculated values for use in relationships (e.g. calculated keys)									Calculated Column (DAX)
You care about performance and model size optimization														Prefer Power Query (where possible)
You’re creating complex, dynamic logic that depends on other tables' values									Calculated Column (DAX)


🎯 Practical Example:
Let’s say you have this data:


Product	Amount	Cost
Coke	100		70

In Power Query
Create a Profit column as Amount - Cost

Good if it’s a static, row-by-row calculation without relationships
✔️ Efficient, light on model

In Calculated Column (DAX)
Create a Profit Margin like:

Profit Margin = (Sales[Amount] - Sales[Cost]) / Sales[Amount]
OR use something involving related tables:

Profit Category = IF(Sales[Profit] > RELATED(Targets[ProfitGoal]), "Above Target", "Below Target")
✔️ Needed if it involves relationships or needs to react to slicers

📌 Quick Rule of Thumb:
“If it can be done in Power Query — do it there.
If it needs to interact with your model’s relationships or slicers — use DAX.”


**If There’s a Date in a Table — Should You Extract Month, Year, etc.?**
Technically you can — but it’s NOT the best practice. The right approach is to create a proper Date Dimension (Dim Date table) and use it for all your time-based analysis.

Why Not Just Extract Month, Year Columns Inside Fact Tables?
You could extract them as Calculated Columns or in Power Query… BUT:

It duplicates logic across tables
Harder to maintain and update if your date logic changes
Can't use built-in Time Intelligence DAX functions (like YTD, MTD, SAMEPERIODLASTYEAR) without a proper Date table
Causes inconsistent drill-downs and slicer behavior
Breaks clean star schema modeling principles

What’s the Right Way?

Create a centralized DimDate table
With one row per date
Columns for Year, Month, Quarter, Day, Week, etc.
Mark it as Date Table in Power BI
Link your Fact table’s DateKey to DimDate[Date]

Then — instead of extracting Month, Year inside FactSales → Just relate to DimDate and use those columns in visuals, filters, and slicers.

Better to stick with DimDate and avoid cal columns for Month/Year?	
Yes — unless it’s a special, case-specific derived date (like PromotionEndDate) unrelated to your Date table

Example Use Case: PromotionEndDate
Imagine this:

Promotion		PromotionStartDate	PromotionEndDate	Product	SalesAmount
Summer Blast	2024-06-01			2024-08-31	Coke	10,000
You’d normally link Sales[SaleDate] → DimDate[Date].

But if you need to analyze based on PromotionEndDate — that might:

Not follow your main calendar timeline (example: fiscal calendars, marketing calendars, etc.)
Be incomplete or irregular (like some promotions ending without a clean date)
Only be used for special case calculations (like days remaining, active promotion status)

In This Case → Create a Calculated Column
Example:
Let’s say you want to know the Promotion End Month:
PromotionEndMonth = FORMAT(Promotions[PromotionEndDate], "MMMM")

Or a Promotion Status:
IsActive = IF(TODAY() <= Promotions[PromotionEndDate], "Active", "Expired")

📌 Since this is specific to the Promotions table, and 
won’t be used for YTD, MTD, or time intelligence measures across FactSales or your model — it doesn’t belong in DimDate.

Key Decision Point:

Question																																
Is this date part of my core reporting timeline (sales, orders, inventory)? - Go with DimDate							
Is this for a specific use case (like PromotionEndDate, EventDeadline, DeliveryETA) not shared with other data? - Go with Calculated Column						
Will I need time intelligence functions (YTD, MTD, drill-downs) on this date?- Go with DimDate								
Do I need to connect it to other fact tables or slicers using time hierarchy? - Go with DimDate								