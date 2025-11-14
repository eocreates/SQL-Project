-----
☁️ TechNova Cloud Cost Optimization
-----
A Data-Driven Investigation into Cloud Waste and Strategic Spend Analyzing and reducing unnecessary cloud expenses using SQL to identify waste, inefficiency, and opportunities for savings.

## CONTEXT AND PROBLEM
TechNova Software Inc. is a fast-growing SaaS company. Recently, leadership noticed that cloud costs jumped 37% between July and August.

## Project Overview
TechNova Software Inc., a SaaS company based in Nairobi, Kenya, Management noticed that its cloud costs were rising rapidly despite stable customer growth. As the company approached its quarterly budget limits, leadership became concerned that a large portion of spending was being wasted on idle or redundant cloud resources.

The goal of this project was to analyze cloud cost data using SQL to uncover:
  1. Areas of inefficient spending
  2. Teams or projects exceeding budgets
  3. Cost drivers behind a 37% monthly cost increase

The analysis follows the BAIIR framework — Business Understanding, Acquisition & Cleaning, Integration, Insight Generation, and Recommendation.

Cloud spending is rising unpredictably while budgets remain unspen.
We need to uncover the cause of this spike, separate strategic costs (investments in Project Titan) from wasteful costs (idle or oversized resources), and recommend actions that improve efficiency without slowing innovation.

## Business Questions
* What percentage of the company’s cloud bill is wasted on idle or unused resources?
* Which teams or projects are responsible for the highest costs?
* Why did the cloud bill increase by 37% between July and August?
* Which parts of the infrastructure are strategic investments (e.g., Project Titan) and which are inefficient costs?
* How can the company reduce costs without affecting performance?

## Objectives and Deliverables
  * Objectives
    - Detect inefficient or idle cloud resources contributing to waste.
    - Compare actual vs. budgeted costs by team.    
  * Identify root causes of rising cloud expenses.
  * Support the Finance and Engineering teams in decision-making.

* Deliverables
- Clean, structured relational database in MySQL.
- SQL scripts for data cleaning, analysis, and visualization.
- Analytical summary with key insights and recommendations.
- A final consolidated SQL view (vw_Master_Report) for dashboards.

## Data Source
The data was obtained from TechNova’s internal cloud billing system, exported as CSV files.

Datasets Used:

cost_and_usage_report.csv – Contains detailed line items of cloud charges and usage.

resource_tags.csv – Maps each resource to a team, project, and environment.

resource_configuration.csv – Includes resource status, instance type, and creation/decommission dates.

performance_metrics.csv – Contains average performance and utilization scores.

quarterly_budgets.csv – Defines budget allocations per team.

security_findings.csv – Records compliance and security-related data.
