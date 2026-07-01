## How we handle late-arriving data

When late transactions arrive (e.g. last week's data lands today), we
**recompute only the affected dates** instead of rebuilding the whole history.

### Step-by-step flow

1. **Trigger** – a new file lands in storage -> We
   extract the distinct `transaction_date` values from the incoming batch
   (e.g. `2026-06-20`, `2026-06-21`, …).

2. **Core layer (`fact_transactions`)** – we load the raw transactions using
   `INSERT … ON CONFLICT (transaction_id) DO UPDATE`. If a transaction with
   the same business key already exists we update its attributes, otherwise
   we insert a new row. This makes the load **idempotent**: running the
   same file twice does not create duplicates and does not change the final
   numbers.

3. **Aggregate layer (`daily_sales`)** – for each affected date we
   **recompute the daily aggregate from scratch** inside a single database
   transaction:

   - Delete the old row for that date.
   - Insert a fresh row built from all transactions of that date.

   Because the whole operation runs inside a transaction, dashboards
   reading the table see either the old numbers or the new numbers, never
   an empty cell.

4. **Version column (optional, safer)** – instead of deleting the old row
   we insert a new one with `version = previous_version + 1`. A view on top
   of the table always picks `MAX(version)` per date. This way there is
   **zero** window where the dashboard could read incomplete data, even
   during a long re-computation.

### Fields that make this possible

| Table | Field | Why it matters |
|-------|-------|----------------|
| `fact_transactions` | `transaction_id` (business key) | Unique constraint for idempotent upsert |
| `fact_transactions` | `transaction_date` | Business date – the axis we aggregate on |
| `fact_transactions` | `ingestion_time` | Tells us *when* the row physically arrived, useful for monitoring |
| `daily_sales` | `date` | The day we are summarising |
| `daily_sales` | `updated_at` | Timestamp of the last recomputation – dashboards can display freshness |
| `daily_sales` | `version` (optional) | Enables “insert-only” strategy that avoids ever deleting rows |
