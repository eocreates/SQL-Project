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
  <img src="https://raw.githubusercontent.com/eocreates/SQL-Project/main/Technova%20Cloud%20Optimization/Descisiontable.jpg" width="600">
</a>



   
