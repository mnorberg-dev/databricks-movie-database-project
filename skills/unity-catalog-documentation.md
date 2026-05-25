# Unity Catalog Documentation Skill

## Purpose
This skill provides a systematic approach to creating properly documented tables and views in Unity Catalog that meet team standards for metadata, discoverability, and maintainability.

## When to Use This Skill
* Creating new tables or views in Unity Catalog
* Refactoring existing tables to improve documentation
* Setting up production data assets that others will use
* Migrating tables from other systems to Unity Catalog

## Documentation Requirements Checklist

### Table-Level Documentation
- [ ] Table COMMENT describing purpose and contents
- [ ] Data refresh frequency or update schedule
- [ ] Source data lineage (where data comes from)
- [ ] Owner/team responsible for the table
- [ ] Business purpose or use case

### Column-Level Documentation
- [ ] COMMENT on all non-obvious columns
- [ ] COMMENT on all business metrics or calculated fields
- [ ] COMMENT on any columns with specific formats or constraints
- [ ] COMMENT on foreign key relationships

### Additional Metadata (When Applicable)
- [ ] TBLPROPERTIES for important metadata (e.g., data classification, retention policy)
- [ ] Partitioning documented in table comment
- [ ] Data quality expectations documented

## Step-by-Step Procedure

### Step 1: Create the Table with Base COMMENT

```sql
CREATE TABLE IF NOT EXISTS catalog.schema.table_name
COMMENT 'Brief description of table purpose. Updated [frequency]. Source: [upstream tables/systems].'
AS
SELECT
  *
FROM
  source_table;
```

**Example:**
```sql
CREATE TABLE IF NOT EXISTS main.analytics.customer_metrics
COMMENT 'Daily customer engagement metrics aggregated from event stream. Updated daily at 6 AM UTC. Source: main.raw.events, main.dim.customers.'
AS
SELECT
  *
FROM
  temp_customer_metrics;
```

### Step 2: Add Column Comments

For each non-obvious column, add a COMMENT explaining:
* What the column contains
* How it's calculated (for metrics)
* Valid values or format (for codes/categories)
* Business meaning (for domain-specific fields)

```sql
ALTER TABLE catalog.schema.table_name 
ALTER COLUMN column_name 
COMMENT 'Description of what this column represents';
```

**Example:**
```sql
-- Document calculated metrics
ALTER TABLE main.analytics.customer_metrics 
ALTER COLUMN active_days 
COMMENT 'Number of distinct days with login activity in the trailing 30 days';

ALTER TABLE main.analytics.customer_metrics 
ALTER COLUMN revenue_tier 
COMMENT 'Customer revenue segment: bronze (<$1K), silver ($1K-$10K), gold (>$10K). Based on trailing 90-day spend.';

-- Document foreign keys
ALTER TABLE main.analytics.customer_metrics 
ALTER COLUMN customer_id 
COMMENT 'Foreign key to main.dim.customers';
```

### Step 3: Add Table Properties (Optional but Recommended)

Use TBLPROPERTIES for additional metadata:

```sql
ALTER TABLE catalog.schema.table_name 
SET TBLPROPERTIES (
  'quality_tier' = 'gold',
  'owner' = 'data-team@company.com',
  'pii_contains' = 'false',
  'retention_days' = '730'
);
```

### Step 4: Document Partitioning and Optimization

If the table is partitioned, include this in the table COMMENT:

```sql
COMMENT ON TABLE catalog.schema.table_name IS 
'Original description. Partitioned by date for query optimization. Partitions retained for 2 years.';
```

## Complete Example

Here's a fully documented table following all standards:

```sql
-- Create the table with comprehensive comment
CREATE TABLE IF NOT EXISTS main.analytics.daily_user_engagement
COMMENT 'Daily aggregation of user engagement metrics including logins, feature usage, and session duration. Updated daily at 6 AM UTC. Source: main.raw.app_events. Partitioned by event_date for query performance.'
PARTITIONED BY (event_date)
AS
SELECT 
  event_date,
  user_id,
  COUNT(DISTINCT session_id) AS session_count,
  SUM(CASE WHEN event_type = 'login' THEN 1 ELSE 0 END) AS login_count,
  SUM(session_duration_seconds) AS total_session_seconds,
  COUNT(DISTINCT feature_used) AS unique_features_used,
  MAX(last_activity_timestamp) AS last_activity
FROM
  main.raw.app_events
WHERE
  event_date >= CURRENT_DATE - INTERVAL 90 DAYS
GROUP BY
  event_date,
  user_id;

-- Add column-level documentation
ALTER TABLE main.analytics.daily_user_engagement 
ALTER COLUMN user_id 
COMMENT 'Foreign key to main.dim.users. Unique identifier for user account.';

ALTER TABLE main.analytics.daily_user_engagement 
ALTER COLUMN session_count 
COMMENT 'Number of distinct sessions initiated by the user on this date. Session timeout is 30 minutes of inactivity.';

ALTER TABLE main.analytics.daily_user_engagement 
ALTER COLUMN total_session_seconds 
COMMENT 'Cumulative session duration in seconds for all sessions on this date. Excludes idle time beyond 30 minutes.';

ALTER TABLE main.analytics.daily_user_engagement 
ALTER COLUMN unique_features_used 
COMMENT 'Count of distinct product features accessed during sessions on this date. Features are defined in main.dim.features.';

-- Add table properties
ALTER TABLE main.analytics.daily_user_engagement 
SET TBLPROPERTIES (
  'quality_tier' = 'gold',
  'owner' = 'analytics-team@company.com',
  'pii_contains' = 'false',
  'data_classification' = 'internal',
  'retention_days' = '730'
);
```

## Verification Checklist

After creating a documented table, verify:

- [ ] Run `DESCRIBE EXTENDED catalog.schema.table_name` to confirm table comment is set
- [ ] Run `DESCRIBE catalog.schema.table_name` to confirm column comments are visible
- [ ] Search for the table in Unity Catalog UI to verify it appears with full metadata
- [ ] Confirm someone unfamiliar with the table could understand its purpose from the documentation
- [ ] All business metrics have explanations of their calculation logic

## Common Patterns

### For Dimension Tables (Slowly Changing Dimensions)
```sql
COMMENT 'Dimension table for [entity]. Type 2 SCD with effective_from/effective_to dates. Current records have effective_to = NULL. Updated via [process/pipeline].'
```

### For Fact Tables
```sql
COMMENT 'Fact table capturing [events/transactions]. Grain: one row per [unit of analysis]. Updated [frequency]. Partitioned by [date_column] for performance.'
```

### For Aggregate/Rollup Tables
```sql
COMMENT '[Aggregation level] rollup of [source]. Pre-aggregated for dashboard/reporting performance. Refreshed [frequency]. Source: [upstream table].'
```

### For Staging/Landing Tables
```sql
COMMENT 'Staging table for raw data from [source system]. No transformations applied. Loaded [frequency]. Retained for [time period] before archival.'
```

## Tips for Better Documentation

1. **Be specific about timing**: "daily at 6 AM UTC" is better than "daily"
2. **Document the grain**: What does one row represent?
3. **Explain calculated fields**: Don't assume formulas are obvious
4. **Reference upstream sources**: Help users trace data lineage
5. **Update comments when logic changes**: Keep documentation in sync with reality
6. **Think about discoverability**: Use terms people will search for
7. **Document edge cases**: How are nulls handled? What about missing data?

## Integration with Temp Views

When building up to a table through temp views (per SQL standards), add the documentation at the final CREATE TABLE step:

```sql
-- Temp views can have simple comments
-- Step 1: Filter to valid events
CREATE OR REPLACE TEMP VIEW clean_events AS
SELECT
  *
FROM
  raw_events
WHERE
  event_type IS NOT NULL;

-- Step 2: Join with user dimensions
CREATE OR REPLACE TEMP VIEW enriched_events AS
SELECT
  e.*,
  u.user_tier
FROM
  clean_events AS e
LEFT JOIN
  dim_users AS u
  ON e.user_id = u.user_id;

-- Final table gets full documentation
CREATE TABLE IF NOT EXISTS main.analytics.processed_events
COMMENT 'Cleaned and enriched event data with user attributes. Updated hourly via scheduled pipeline. Source: main.raw.events, main.dim.users.'
AS
SELECT
  *
FROM
  enriched_events;

-- Then add column comments as needed
ALTER TABLE main.analytics.processed_events 
ALTER COLUMN user_tier 
COMMENT 'User subscription tier at time of event: free, premium, or enterprise';
```
