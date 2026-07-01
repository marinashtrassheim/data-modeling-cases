## Approach 1: One Schema, One Table with `tenant_id`

**How it works**  
A single set of tables (e.g., `fact_usage`, `dim_user`) shared by all tenants.  
Every row contains a `tenant_id` column to identify the owner.  
Custom fields are stored in a JSONB column (`custom_attributes`) to accommodate tenant‑specific data without adding unlimited nullable columns.

**Example**
```sql
CREATE TABLE fact_usage (
    tenant_id    VARCHAR(10) NOT NULL,
    event_id     BIGINT NOT NULL,
    user_id      BIGINT,
    event_time   TIMESTAMP,
    custom_data  JSONB,
    PRIMARY KEY (tenant_id, event_id)
) PARTITION BY LIST (tenant_id);
```

**Advantages**
Simplicity: Only one table to manage per entity, straightforward ETL pipelines.
Easy to extend: New tenants require no schema changes; just add a new partition and start inserting data.
Cost‑efficient: One set of indexes, one set of monitoring rules.
Internal analytics: Internal cross‑tenant reports (e.g., platform‑wide usage trends) are trivial – no need to union hundreds of tables.
**Disadvantages**
Isolation is not automatic: The database itself does not physically separate data; you must enforce isolation at the query level.
Large table size: With 500 active tenants, a fact table can grow to billions of rows. Without partitioning, full scans hurt performance.
Custom field complexity: JSONB is flexible but can lead to inconsistent keys, bloated storage, and harder SQL queries for analysts if they need to filter inside the JSON.
Noise in query plans: If RLS or application filtering is misconfigured, a tenant could accidentally see another’s data.


## Approach 2: One Schema, Separate Tables per Tenant

**How it works**
In the same schema, create a table set for each tenant using a naming convention, e.g., usage_T1, usage_T2, …, usage_T500.
Custom fields become ordinary columns in that tenant’s tables.

## Approach 3: Separate Schema / Database per Tenant

**How it works**
Each tenant gets their own logical container – either a separate schema (PostgreSQL) or a separate database entirely.
Inside that container, the table names are identical (e.g., usage, user).
Custom fields become regular columns within the tenant’s dedicated tables.


