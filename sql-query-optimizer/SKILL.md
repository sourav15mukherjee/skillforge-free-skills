---
name: sql-query-optimizer
description: >-
  Analyze and optimize SQL queries for performance. Use when the user asks
  to optimize a query, improve database performance, analyze an EXPLAIN plan,
  add indexes, rewrite slow queries, or has a database bottleneck. Supports
  PostgreSQL, MySQL, and SQLite.
version: "1.0.0"
tools:
  - Read
  - Bash
  - Grep
---

## SQL Query Optimizer

Analyze SQL queries for performance bottlenecks and produce optimized variants with index suggestions and execution plan interpretations.

## Workflow

1. **Collect context**
   Identify:
   - The SQL query (provided directly or found in source code)
   - The database engine: PostgreSQL (default), MySQL/MariaDB, or SQLite
   - Whether the user has an EXPLAIN/EXPLAIN ANALYZE output
   - Table schemas (CREATE TABLE statements or ORM models)
   - Approximate table sizes (millions of rows? thousands?)
   - Current indexes on relevant tables

2. **Pass 1 — Execution Plan Analysis**
   If EXPLAIN output is provided, interpret it:
   - **Seq Scans on large tables** — Missing index
   - **Nested Loop with high row estimates** — Cartesian product risk
   - **Sort with high cost** — Missing index for ORDER BY
   - **Hash Join on small tables** — Usually fine
   - **Bitmap Heap Scan** — Good for moderate selectivity
   - **Materialize** — Subquery not flattened
   - **Buffers: shared read > hit** — Poor cache utilization

   If no EXPLAIN is provided, generate one:
   ```sql
   -- PostgreSQL
   EXPLAIN (ANALYZE, BUFFERS, FORMAT TEXT) <query>;

   -- MySQL
   EXPLAIN ANALYZE <query>;
   ```

3. **Pass 2 — Query Rewrite Analysis**
   Check for common anti-patterns:

   | Anti-Pattern                  | Fix                                      |
   |-------------------------------|------------------------------------------|
   | SELECT *                      | Select only needed columns               |
   | Subquery in WHERE (correlated)| Rewrite as JOIN or EXISTS                |
   | OR across different columns   | UNION ALL or separate queries            |
   | Function on indexed column    | Expression index or computed column      |
   | LIKE '%prefix'                | Full-text index or trigram index         |
   | Implicit type cast            | Match types to avoid cast on indexed col |
   | NOT IN with nullable column   | Rewrite as NOT EXISTS                    |
   | COUNT(*) on large table       | Use count estimate or cached counter     |
   | DISTINCT without reason       | Check if duplicates are actually possible|
   | OFFSET for pagination         | Keyset pagination (cursor-based)         |
   | CTE in PostgreSQL < 12        | Materialized — may prevent optimization  |
   | HAVING without GROUP BY       | Move to WHERE                            |

4. **Pass 3 — Index Analysis**
   For each table in the query, check:
   - Is there an index covering the WHERE clause columns?
   - Is there a covering index for SELECT columns (index-only scan)?
   - Are JOIN columns indexed on both sides?
   - Is there a composite index in the right column order (equality first, then range)?
   - Are there redundant indexes that can be consolidated?

   Generate CREATE INDEX statements:
   ```sql
   -- Composite index for: WHERE user_id = ? AND status = ? ORDER BY created_at DESC
   CREATE INDEX CONCURRENTLY idx_orders_user_status_created
   ON orders (user_id, status, created_at DESC);
   ```

5. **Pass 4 — Engine-Specific Optimizations**
   Apply database-specific patterns:

   **PostgreSQL:**
   - Use JSONB indexing with GIN for JSON queries
   - Partial indexes for filtered queries
   - BRIN indexes for time-series data
   - LATERAL JOINs for correlated subqueries
   - UNLOGGED tables for ephemeral data

   **MySQL:**
   - Covering indexes with INCLUDE equivalent
   - FORCE INDEX hints when optimizer chooses wrong plan
   - Partition pruning strategies
   - Query cache considerations

   **SQLite:**
   - Partial indexes (WHERE clause)
   - WITHOUT ROWID tables for non-integer PKs
   - Expression indexes
   - PRAGMA optimizations (journal_mode, cache_size)

6. **Generate the report**
   Structure as:

   ## Query Optimization Report

   ### Original Query
   ```sql
   <original query>
   ```

   ### Bottlenecks Found
   1. [Severity] Description + line reference

   ### Optimized Query
   ```sql
   <rewritten query with comments>
   ```

   ### Recommended Indexes
   ```sql
   <CREATE INDEX statements>
   ```

   ### Expected Impact
   - Scan type change: Seq Scan → Index Scan
   - Estimated row reduction: 500K → 50
   - Index count: +1 new, -0 redundant

## Rules

- Always preserve query semantics — the optimized query must return identical results
- Show the original query side-by-side with changes highlighted
- Never add indexes without explaining the write overhead trade-off
- Use CONCURRENTLY for PostgreSQL index creation (no table lock)
- If multiple optimization paths exist, show the best one and mention alternatives
- Consider the table size — an optimization for 1M rows may differ from 1B rows
- Don't assume the user knows EXPLAIN output — annotate the important parts
- If the query is already optimal, say so and explain why
- Support parameterized queries (use placeholders, not literal values)

## Example Optimization

```sql
-- BEFORE: 2.3s on 1.2M row table
SELECT * FROM orders
WHERE user_id IN (SELECT id FROM users WHERE plan = 'premium')
AND created_at > NOW() - INTERVAL '30 days'
ORDER BY created_at DESC
LIMIT 20;

-- AFTER: 12ms
SELECT o.id, o.total, o.status, o.created_at
FROM orders o
INNER JOIN users u ON u.id = o.user_id
WHERE u.plan = 'premium'
  AND o.created_at > NOW() - INTERVAL '30 days'
ORDER BY o.created_at DESC
LIMIT 20;

-- Supporting index
CREATE INDEX CONCURRENTLY idx_orders_user_created
ON orders (user_id, created_at DESC)
WHERE created_at > NOW() - INTERVAL '90 days';
```
