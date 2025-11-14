----
Nature's Best Drink leakyCap RCA
---

## 📚 Table of Contents
-----
- [CONTEXT AND PROBLEM](#context-and-problem)
- [BUSINESS IMPACT](#business-impact)
- [ER DIAGRAM](#er-diagram)
- [DATA CLEANING](#data-cleaning)
- [ANALYSIS](#analysis)
- [QUERIES AND LOGIC USED (VIEWS)](#queries-and-logic-used-views)
- [VISUALIZATIONS](#visualizations)
- [RECOMMENDATIONS](#recommendations)
- [WHY WE ARE CONFIDENT THE FIX WILL WORK](#why-we-are-confident-the-fix-will-work)

## CONTEXT AND PROBLEM
---
In the same operational week at the Onitsha plant, Nature Best Juice Co. experienced an unusual spike in Leaky Cap defects across multiple lines, concentrated on July 2–3, 2025. The surge exceeded normal levels, disrupted production, and signaled potential issues in the capping process, materials, or controls that need urgent investigation.

Problem Statement
---

During the same operational week at the Onitsha plant, Nature Best Juice Co. experienced an unusual and sharp spike in Leaky Cap defects across multiple production lines. The issue was concentrated on Wednesday, July 2, 2025, and Thursday, July 3, 2025, significantly exceeding defect levels recorded on other days within the week. This sudden rise in defective caps disrupted normal production performance and highlighted a potential underlying issue within the capping process, materials, or operational controls that requires immediate investigation and resolution.

Who are the stakeholders
 -----
* Procurement Manager → suspects a bad batch from a supplier
* Quality Control Lead → needs clarity on whether this was a system-wide issue
* Line Managers → want to isolate the problem quickly to resume normal production

## BUSINESS IMPACT
---
From the 2 days of leaky cap issues (July 2–3):
* 3,355 leaky caps recorded, with thousands more in the following week
* Daily losses jumped to 34.3% compared to just 2.1% the previous week
* Financial losses totaled about ₦45,000 in 2 days, versus only ₦6,000 the week before

Data Exploration and Schema Design
---
We worked on a structured production dataset built around a star schema with a central fact table called "FactProductionEvent"
* DimDate -- This is the calendar table
* DimSupplier -- This table contains information about the companies that supply us with empty bottles
* DimCapBatch -- This table provides information about each delivery (batch) of bottle caps we receive.

## ER DIAGRAM
---
The ER diagram is use to visualize table relationships and track foreign keys used during analysis.

<a href="https://raw.githubusercontent.com/eocreates/SQL-Project/main/Nature%20Best%20Cap%20Analysis/Report/481005514-94f76626-16ae-4a94-88d7-0950338b4ef4.png">
  <img src="https://raw.githubusercontent.com/eocreates/SQL-Project/main/Nature%20Best%20Cap%20Analysis/Report/481005514-94f76626-16ae-4a94-88d7-0950338b4ef4.png" width="400">
</a>

## DATA CLEANING
---
PHASE 1

STANDARDIZING NUMERICS COLUMNS

The cleaning are done on the following column where we noticed unusual Numerics values,symbols and text. Note that this cleaning was carried out before any analysis (RCA)

* Timestamp -- are critical for trend analysis, cleaning them first ensured that sorting, filtering and comparisons worked properly. We fixed malformed and inconsistent formats, recast from text to datetime.
* JuiceTemperatureC_In -- this column are neccessary for the determinations of the various temperatgure of the juice as at when produced. it contains some unusual rows like 'WAY TOO HOT','SENSOR_BROKEN','COLD!','HOT!'
* ActualCapTorque_Nm -- The actual "tightness" measured for the cap on this bottle, in Newton meters.
* AmbientTemperatureC_Line -- The air temperature around the production line when this bottle was processed, in degrees Celsius.

PHASE 2

## STANDARDIZING TEXT COLUMNS
---

* Defect Result(String): this column tells us the kind of problem (if any) was found with this bottle: "None" (no defect), "Underfilled" (not enough juice), "Leaky_Cap" (the cap leaks), or "Both" (both underfilled and leaky). so all other similar rows are group and categorize into this category
* LeakTestResult(String): The outcome of the test to see if the bottle leaks: "Pass" (it doesn't leak) or "Fail" (it leaks).

PHASE 3

REMOVING DUPLICATES

We tested key columns to identify duplicate values in the dataset. After analysis, we removed all duplicate rows. In total, over 32,000 duplicate records were deleted, ensuring the dataset is accurate, consistent, and ready for reliable analysis. Steps:

* Detect duplicates with COUNT(*)
* Create an index to speed up the update
* Use a CTE with ROW_NUMBER() to retain one copy of each duplicate set
* Delete all extra duplicate rows
* Code Used for Testing and Deleting all Duplicate



