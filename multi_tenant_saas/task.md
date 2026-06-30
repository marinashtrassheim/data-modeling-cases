# Scenario
You are building a DWH for a SaaS platform with 500+ clients (tenants).  
Each client must only see their own data.  
The data is structurally similar across tenants, but each tenant may have custom fields.

# Task
Compare three approaches:  
1. One schema, one table with `tenant_id`.  
2. One schema, separate tables per client.  
3. Separate schema / database per client.

What are the trade-offs for each?  
How would you enforce data isolation at the query level?  
How would you scale to 5000 clients?
