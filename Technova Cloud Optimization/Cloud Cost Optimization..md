☁️ Cloud Cost Optimization- Technova
-----
A Data-Driven Investigation into Cloud Waste and Strategic Spend Analyzing and reducing unnecessary cloud expenses using SQL to identify waste, inefficiency, and opportunities for savings.
TechNova Software Inc. is a fast-growing SaaS company located in Nairobi, Kenya.

## Table of Contents
----
* Context & Problem
* Project Overview
* Business Questions
* Objectives & Deliverables
* Data Source

## Context & Problem
Technova’s quarterly cloud bill is high, unpredictable, and close to exceeding budget.
Leadership doesn’t know:
* What’s causing the high bill
* Which teams are responsible
* How much is wasteful vs strategic
Meanwhile, an expensive AI initiative — Project Titan — must not be affected.
A 10% cost reduction is required without slowing innovation.

## Project Overview

This project analyses cloud financial and operational data to:
* Identify Bad Costs (waste, idle resources)
* Identify Good Costs (Project Titan spend)
* Provide a clear, data-driven optimisation plan

It follows a simple 3-step workflow:
* Data Profiling & Cleaning – Fix timestamps, costs, and inconsistencies.
* BAIIR Analysis – Baseline, Variance, RCA, Insights, Impact.
* Recommendations & Dashboard – SQL summary view + Power BI visualisation.

## Business Questions

1. What percentage of the bill is wasteful?
2. How much spend goes to Project Titan?
3. Which teams are over/under budget?
4. What root causes drive the high cloud bill?
5. Where can Technova save 10% without harming Project Titan?

## Objectives & Deliverables
* Objectives
  - Improve cost visibility
  - Quantify waste vs strategic spend
  - Identify root causes of overspending
  - Achieve 10% cost reduction safely

* Deliverables
  - Clean dataset
  - Data cleaning decision table
  - SQL BAIIR analysis
  - KPI outputs
  - Summary SQL View
  - Power BI dashboard
  - Final optimisation recommendations

## Data Source
The data was obtained from TechNova’s internal cloud billing system, exported as CSV files.

Datasets Used:
  * cost_and_usage_report.csv – Contains detailed line items of cloud charges and usage.
  * resource_tags.csv – Maps each resource to a team, project, and environment.
  * resource_configuration.csv – Includes resource status, instance type, and creation/decommission dates.
  * performance_metrics.csv – Contains average performance and utilization scores.
  * quarterly_budgets.csv – Defines budget allocations per team.
  * security_findings.csv – Records compliance and security-related data.

## PROFILING 
Before data cleaning, an initial profiling phase was conducted across all tables to understand data quality, structure, and potential issues. This included checks for primary keys, duplicates, null behaviour, data types and invalid/outlier values.
All decisions were recorded in Excel as part of the Data Profiling Decision Log.

1. cost_and_usage_report
	Checks Performed
	* Data Types, Row/Column Count, Frequency Distribution, Nulls
	* Verified primary key candidates (line_item_resource_id + line_item_usage_start_date)
	* Checked for duplicate rows

````Sql
--DATA TYPE PER COLUMN
SELECT COLUMN_NAME, DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'cost_and_usage_report'

--CHECK IF THERE ARE NULLS IN THE COLUMN
SELECT COUNT(*)
FROM [dbo].[cost_and_usage_report]
WHERE [line_item_usage_start_date] IS NULL

--TABLE 1 (cost_and_usage_report)
SELECT *
FROM cost_and_usage_report

-- ROW AND COLUMN COUNT
SELECT COUNT (*)
,COUNT([line_item_usage_start_date])
,COUNT([product_name])
,COUNT([line_item_resource_id])
,COUNT([line_item_usage_type])
,COUNT([line_item_usage_amount])
,COUNT([line_item_blended_cost])
FROM [dbo].[cost_and_usage_report]

-- FREQUENCY DISTRIBUTION
SELECT Product_name
		,COUNT(*) AS Total
FROM[dbo].[cost_and_usage_report] 
GROUP BY Product_name

-- (1) LINE ITEM USAGE START DATE COLUMN

--CHECK IF THERE ARE NULLS IN THE COLUMN
SELECT COUNT(*)
FROM [dbo].[cost_and_usage_report]
WHERE [line_item_usage_start_date] IS NULL 

--CHANGE DATATYPE USING TRY_CAST AND CHECK INCOSISTENCIES REASONS
SELECT [line_item_usage_start_date]
		,TRY_CAST ([line_item_usage_start_date] AS DATE) AS New_Date
FROM [dbo].[cost_and_usage_report]
WHERE TRY_CAST ([line_item_usage_start_date] AS DATE) IS NULL


--(2) ITEM BLENDED COST COLUMN

--CHECK IF THERE ARE NULLS IN THE COLUMN
SELECT COUNT(*)
FROM [dbo].[cost_and_usage_report]
WHERE [line_item_blended_cost] IS NULL 

--CHANGE DATATYPE USING TRY_CAST AND CHECK INCOSISTENCIES REASONS
SELECT line_item_blended_cost
,TRY_CAST ([line_item_blended_cost] AS DECIMAL(10,2)) 
FROM cost_and_usage_report
WHERE TRY_CAST ([line_item_blended_cost] AS DECIMAL(10,2)) IS NULL

---CHECKING FOR DUPLICATES
SELECT line_item_resource_id
		,line_item_usage_start_date
		,COUNT (*) as Duplicate
FROM cost_and_usage_report
GROUP BY line_item_resource_id, line_item_usage_start_date
HAVING COUNT (*) >1
---There are no duplicates
````

2. performance_metrics
Checks Performed
	* Data Types, Row/Column Count, Frequency Distribution
	* Checked for duplicate rows
	* Checked uniqueness of (resource_id, metric_timestamp)
	* Investigated null resource IDs
	* Assessed data completeness per timestamp
````sql

--DATA TYPES PER COLUMN
SELECT COLUMN_NAME, DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'resource_tags'

-- COUNT RECORDS FOR EACH COLUMN
SELECT COUNT(*) total_rows,
COUNT(line_item_resource_id) AS cnt_line_item_resource_id,
COUNT(resource_tag_team) AS cnt_line_item_usage_type,
COUNT(resource_tag_project) AS cnt_line_item_usage_amount,
COUNT(resource_tag_environment) AS cnt_line_item_blended_cost
FROM resource_tags

-- FREQUENCY DISTRIBUTION 
SELECT [resource_tag_team]
,COUNT (*)
FROM [dbo].[resource_tags]
GROUP BY [resource_tag_team]

SELECT [resource_tag_project]
,COUNT (*)
FROM [dbo].[resource_tags]
GROUP BY [resource_tag_project]

SELECT resource_tag_environment
		,COUNT (*)
FROM [dbo].[resource_tags]
GROUP BY resource_tag_environment

--JOINING  COST USAGE AND REPORT WITH RESOURCE TAGS AS BOTH TABLE CONTAIN PRIMARY KEY (COST AND USAGE AND REPORT) AND FOREIGN KEY (RESOURCE TAGS)
SELECT *
FROM cost_and_usage_report C
JOIN resource_tags r
ON c.[line_item_resource_id]= r.[line_item_resource_id]

--- TO FIND line_item_resource_id THAT IS IN RESOURCE TAG BUT NOT IN COST USAGE REPORT 
SELECT  line_item_resource_id
FROM [dbo].[resource_tags]
WHERE  line_item_resource_id NOT IN (
SELECT line_item_resource_id
FROM cost_and_usage_report)


---CHECKING FOR DUPLICATES
SELECT resource_id
		,metric_timestamp
		,COUNT (*) as Duplicate
FROM performance_metrics
GROUP BY resource_id, metric_timestamp
HAVING COUNT (*) >1
--There are duplicates values found on the nulls resources id 
-- We have resources that were not record on each date
````

3. resource_configuration
   Checks Performed
	* Data Types, Row/Column Count, Nulls
	* Checked for duplicate rows
 	* Validated uniqueness of resource_id

```sql

--DATA TYPES PER COLUMN
SELECT COLUMN_NAME, DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'resource_configuration'

--COUNT RECORDS FOR EACH COLUMN
SELECT	COUNT (*) TOTALROWS
		,COUNT ([resource_id]) 
		,COUNT([product_name])
		,COUNT([instance_type])
		,COUNT([decommission_date]) 
FROM [dbo].[resource_configuration]

-- CHECKS FOR NULLS 
SELECT *
FROM [dbo].[resource_configuration]
WHERE instance_type IS NULL AND  decommission_date IS NULL

-- % OF NON NULLS OF EACH COLUMN IN THE TABLE
SELECT	100 - (COUNT (resource_id) *100.0/ COUNT(*)) AS  [% of nulls resourceid]
		,100 - (COUNT (product_name) *100.0/ COUNT(*)) AS  [% of nulls productname]
		,100 - (COUNT (instance_type) *100.0/ COUNT(*)) AS  [% of nulls instance_type]  
		,100 - (COUNT (decommission_date) *100.0/ COUNT(*)) AS  [% of nulls decommision_date]
FROM [dbo].[resource_configuration]

SELECT *
FROM [dbo].[resource_configuration]
WHERE [decommission_date] IS NOT NULL and [instance_type] is not null

---CHECKING FOR DUPLICATES
SELECT resource_id
		,COUNT (*) AS Dup
FROM resource_configuration
GROUP BY resource_id
HAVING COUNT (*) > 1
---There are no duplicates
````

4. resource_tags
Checks Performed
	* Data Types, Row/Column Count, Nulls
 	* Checked uniqueness of line_item_resource_id
```sql

--DATA TYPES PER COLUMN
SELECT COLUMN_NAME, DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'performance_metrics'

--COUNT RECORDS FOR EACH COLUMN
SELECT	COUNT (*) TOTALROWS
		,COUNT ([resource_id]) 
		,COUNT([metric_timestamp])
		,COUNT([metric_name])
		,COUNT([average_value]) 
FROM [dbo].[performance_metrics]

-- CHECKS FOR NULLS 
SELECT *
FROM [dbo].[performance_metrics]
WHERE [resource_id] IS NULL
		OR [metric_timestamp] IS NULL
		OR [metric_name] IS NULL
		OR [average_value] IS NULL

-- % OF NON NULLS OF EACH COLUMN IN THE TABLE
SELECT 100 - COUNT(resource_id) * 100.0/ COUNT(*) AS [% of Null resource id]
		,100 - COUNT(metric_timestamp) * 100.0/ COUNT(*) AS [% of Null metric_timestamp]
		,100 - COUNT (metric_name)  * 100.0/ COUNT(*) AS [% of Null metric_name]
		,100 - COUNT (average_value)  * 100.0/ COUNT(*) AS [% of Null average_value]
FROM [dbo].[performance_metrics]
````

5. quarterly_budgets
Checks Performed
	* Data Types, Row/Column Count

````sql
--CHECK THE DATA TYPE
SELECT COLUMN_NAME, DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'quarterly_budgets'

-- COUNT THE COLUMN
SELECT	COUNT (*) Total_Rows
		,COUNT([quarter_start_date]) 
		,COUNT ([team_name])
		,COUNT ([quarterly_budgeted_amount])
FROM[dbo].[quarterly_budgets]

````
   
6. security_findings
Checks Performed
	* Data Types
	* Checked uniqueness of resource_id
 	* Ensured no duplicated security alerts per resource
```sql
SELECT *
FROM [dbo].[security_findings]

--CHECK THE DATA TYPE
SELECT COLUMN_NAME, DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'security_findings'

-- COUNT THE COLUMN
SELECT	COUNT (*) Total_Rows
		,COUNT([finding_id]) 
		,COUNT ([resource_id])
		,COUNT ([finding_description])
		,COUNT ([severity])
FROM [dbo].[security_findings]

````
## DECISION TABLE — DATA QUALITY & CLEANING STRATEGY
<a href="https://raw.githubusercontent.com/eocreates/SQL-Project/main/Technova%20Cloud%20Optimization/Descisiontable.jpg">
  <img src="https://raw.githubusercontent.com/eocreates/SQL-Project/main/Technova%20Cloud%20Optimization/Descisiontable.jpg" width="700">
</a>

## CLEANING
After completing data profiling, the next stage involved extensive data cleaning across date, numeric, and categorical fields. The goal is to standardise formats, correct inconsistencies, and enhance analytical usability while preserving data integrity.

Phase 1 — (CLEANING DATES AND TIMESTAMP COLUMS)
1. cost_and_usage_report — line_item_usage_start_date
Issue Identified
	* Some dates contained the suffix "th", preventing correct conversion to DATE data type.
Actions Taken
	* Identified invalid date formats using TRY_CAST.
	* Removed 'th' using REPLACE.
	* Created a new clean DATE column Usage_Date.
	* Updated the new column with the cleaned date.
```sql
SELECT line_item_usage_start_date
		,TRY_CAST (line_item_usage_start_date AS DATE)
FROM cost_and_usage_report
WHERE TRY_CAST (line_item_usage_start_date AS DATE) is null

--- REPLACE 'TH' with ""
SELECT line_item_usage_start_date
		,TRY_CAST( REPLACE(line_item_usage_start_date,'th','') AS DATE) Clean_Date
FROM cost_and_usage_report
WHERE TRY_CAST (line_item_usage_start_date AS DATE) is null

--NOW THE DATES HAVE BEEN CLEANED
-- ADD A NEW DATE COLUMN  TO THE TABLE
ALTER TABLE [dbo].[cost_and_usage_report]
ADD Usage_Date DATE

--- UPDATE THE CLEANED DATE INTO THE NEW DATE COLUMN (Usage Date)
UPDATE cost_and_usage_report
SET Usage_Date = TRY_CAST( REPLACE(line_item_usage_start_date,'th','') AS DATE)
```


2. performance_metrics — metric_timestamp
Issue Identified
	* Same “th” suffix issue affecting date conversion.
Actions Taken
	* Checked for invalid timestamp formats.
 	* Applied REPLACE to remove 'th'.
  	* Added a new DATE column metric_date.
   	* Populated the cleaned dates.
````Sql
--LOOKUP FOR THE INCORRECT DATE FORMATS
SELECT metric_timestamp
		,TRY_CAST(metric_timestamp as DATE)
FROM performance_metrics
WHERE TRY_CAST(metric_timestamp as DATE) IS NULL

-- REPLACE 'TH' WITH ''
SELECT metric_timestamp
		,TRY_CAST(REPLACE(metric_timestamp, 'th','') AS DATE) Cleaned_Date
FROM performance_metrics

-- ADD A NEW COLUMN
ALTER TABLE performance_metrics
ADD metric_date DATE

-- UPDATE THE NEW COLUMN 
UPDATE performance_metrics
SET metric_date = TRY_CAST(REPLACE(metric_timestamp, 'th','') AS DATE)
````

PHASE 2 (CLEANING NUMERIC COLUMNS) ITEM BLENDED COST COLUMN
1. cost_and_usage_report — line_item_blended_cost
	* Issues Identified
 	* Stored as string and contained the $ symbol.
  	* Also contained numeric outliers (999 and -0.130).
   
Actions Taken
	* Removed $ using REPLACE.
	* Cast the cleaned result to FLOAT.
	* Added new numeric column blended_cost.
	* Populate cleaned values.

```Sql
--- CLEANING ($ sign) and Data Type to Float
SELECT line_item_blended_cost
		 ,TRY_CAST(REPLACE(line_item_blended_cost, '$','')AS FLOAT)
FROM cost_and_usage_report

---- ADD A NEW COLUMN
ALTER TABLE cost_and_usage_report
ADD blended_cost FLOAT

-- UPDATE THE NEW COLUMN (BLENDED COST) TO THE CLEANED DECIMAL COLUMN
UPDATE cost_and_usage_report
SET blended_cost = TRY_CAST(REPLACE(line_item_blended_cost, '$','')AS float)

```
Outlier Handling
1. Outlier = 999
	Extremely higher than typical values.
	Removed from dataset.
```sql
DELETE FROM cost_and_usage_report
WHERE blended_cost = 999;
```
2. Outlier = -0.130
	Negative cost indicates refund, not an error.
	Added a new column: Cost_Type
	Categorised costs into Charge (positive) and Refund (negative).

```Sql
UPDATE cost_and_usage_report
SET Cost_type =
    CASE WHEN blended_cost < 0 THEN 'Refund'
         ELSE 'Charge'
    END;
````

Phase 3 — CLEANING CATEGORICAL FIELDS
1. resource_tags — resource_tag_team
	Issue Identified
	* Tags had inconsistent naming: user-frontend-team → user-frontend; payments-team → payments etc.
Actions Taken
	* Standardised team names using a CASE expression.
 	* Added a new column resource_team.
	* Populated it with cleaned, consistent labels.

```Sql
SELECT resource_tag_team 
		, CASE
			 WHEN 	resource_tag_team IN ('user-frontend-team') THEN 'user-frontend'
			 WHEN	resource_tag_team IN ('data-platform-team') THEN 'data-platform'
			 WHEN   resource_tag_team IN ( 'payments-team') THEN 'payments'
			 WHEN	resource_tag_team IN ('marketing-api-team') THEN 'marketing-api'
			 WHEN	resource_tag_team IN ('identity-team') THEN 'identity'
				 ELSE resource_tag_team	
					END
FROM resource_tags

---- ADD A NEW COLUMN
ALTER TABLE resource_tags
ADD resource_team VARCHAR (99)

-- UPDATE THE NEW COLUMN (resource_team) TO THE CLEANED VARCHAR COLUMN
UPDATE resource_tags
SET resource_team = CASE
			 WHEN 	resource_tag_team IN ('user-frontend-team') THEN 'user-frontend'
			 WHEN	resource_tag_team IN ('data-platform-team') THEN 'data-platform'
			 WHEN   resource_tag_team IN ( 'payments-team') THEN 'payments'
			 WHEN	resource_tag_team IN ('marketing-api-team') THEN 'marketing-api'
			 WHEN	resource_tag_team IN ('identity-team') THEN 'identity'
				 ELSE resource_tag_team	
					END
FROM resource_tags
````

2. resource_configuration — instance_type
Issue Identified
	* NULL values in instance_type meant the resource is not a compute server.
Actions Taken
	* Created a new column instance.
	* Replaced NULLs with 'non-server'.

````Sql

--- FLAGGING NON-SERVER ROWS (INSTANCE TYPE COLUMN) i.e CHANGING NULL TO NON SERVER
SELECT instance_type
		, CASE 
			WHEN instance_type is null THEN 'non-server'
			ELSE instance_type
			END
FROM resource_configuration


---- ADD A NEW COLUMN
ALTER TABLE resource_configuration
ADD instance VARCHAR (99)

-- UPDATE THE NEW COLUMN (Instance) TO THE CLEANED VARCHAR COLUMN
UPDATE resource_configuration
SET instance = CASE 
			WHEN instance_type is null THEN 'non-server'
			ELSE instance_type
			END
FROM resource_configuration
````
## Final Cleaning Notes & Outcomes

| Cleaning Area        | Issue Found                   | Action Taken                 | Result                           |
| -------------------- | ----------------------------- | ---------------------------- | -------------------------------- |
| Dates & Formats      | “th” suffix breaking CAST     | Cleaned & new columns added  | All dates now valid              |
| Numeric Cost         | `$` symbol and text data type | Converted to FLOAT           | Numeric ready for analysis       |
| Outliers             | 999 and -0.130                | Removed or categorised       | Dataset normalised               |
| Tags                 | Inconsistent naming           | Standardised via CASE        | Clean team mapping               |
| Server Types         | NULL instances                | Reclassified as “non-server” | Improved resource classification |
| Missing Resource IDs | Null resource_id              | Excluded                     | Accurate metrics per resource    |

