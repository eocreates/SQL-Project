----
Nature's Best Drink leakyCap RCA
----
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
 -----
## CONTEXT AND PROBLEM
 -----
In the same operational week at the Onitsha plant, Nature Best Juice Co. experienced an unusual spike in Leaky Cap defects across multiple lines, concentrated on July 2–3, 2025. The surge exceeded normal levels, disrupted production, and signaled potential issues in the capping process, materials, or controls that need urgent investigation.
 -----
Problem Statement
 -----
During the same operational week at the Onitsha plant, Nature Best Juice Co. experienced an unusual and sharp spike in Leaky Cap defects across multiple production lines. The issue was concentrated on Wednesday, July 2, 2025, and Thursday, July 3, 2025, significantly exceeding defect levels recorded on other days within the week. This sudden rise in defective caps disrupted normal production performance and highlighted a potential underlying issue within the capping process, materials, or operational controls that requires immediate investigation and resolution.
 -----
Who are the stakeholders
 -----
* Procurement Manager → suspects a bad batch from a supplier
* Quality Control Lead → needs clarity on whether this was a system-wide issue
* Line Managers → want to isolate the problem quickly to resume normal production
 -----
## BUSINESS IMPACT
 -----
From the 2 days of leaky cap issues (July 2–3):
* 3,355 leaky caps recorded, with thousands more in the following week
* Daily losses jumped to 34.3% compared to just 2.1% the previous week
* Financial losses totaled about ₦45,000 in 2 days, versus only ₦6,000 the week before
 -----
Data Exploration and Schema Design
 -----
We worked on a structured production dataset built around a star schema with a central fact table called "FactProductionEvent"
* DimDate -- This is the calendar table
* DimSupplier -- This table contains information about the companies that supply us with empty bottles
* DimCapBatch -- This table provides information about each delivery (batch) of bottle caps we receive.
-----
## ER DIAGRAM
----
The ER diagram is use to visualize table relationships and track foreign keys used during analysis.
