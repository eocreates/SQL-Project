-----
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
