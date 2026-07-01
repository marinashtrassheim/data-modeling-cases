# Scenario
Your pipeline loads transaction data daily and updates aggregates (daily_sales).  
Suddenly, you receive late-arriving data: transactions from last week only arrived today.

# Task
Design the pipeline and schema so that:  
1) Late-arriving data is correctly accounted for in historical aggregates.  
2) The pipeline is idempotent (can be re-run without duplication).  
3) Downstream consumers (dashboards) do not break during the re-computation.  
Which fields in the table help track this?
