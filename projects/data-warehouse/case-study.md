# SQL Data Warehouse — Medallion Architecture (Bronze/Silver/Gold)

**In one line:** Consolidated two source systems (CRM + ERP) with conflicting formats and a broken sales formula into a single SQL Server star schema — with the sales figure recalculated rather than trusted as-is.

**Problem:** CRM and ERP each described the same customers and products differently — inconsistent gender codes (`F` vs `Female`), country abbreviations (`DE` vs `Germany`), ID fields with stray prefixes and hyphens — and the CRM sales table's own `sales_amount` column didn't reliably equal `quantity × price`. Reporting directly off the raw exports meant every downstream number inherited whichever inconsistency happened to be in the source that day.

**What I built:** A three-layer (Medallion) warehouse in SQL Server / T-SQL:
- **Bronze:** six source tables loaded as-is from CRM/ERP CSVs via `BULK INSERT`, full truncate-and-reload each run.
- **Silver:** the same six tables, cleansed — deduplicated, trimmed, `YYYYMMDD` integers cast to `DATE`, gender/country codes normalized, ID prefixes stripped, and `sales_amount` recalculated wherever it didn't match `quantity × price`.
- **Gold:** three business-ready star-schema views (`dim_customers`, `dim_products`, `fact_sales`) built purely with joins and `ROW_NUMBER()` surrogate keys — no additional loading, so the schema can change without touching stored data.

Both `bronze.load_bronze` and `silver.load_silver` are stored procedures with `TRY`/`CATCH` blocks and `PRINT` timing per table, so a failed run points at the table that broke instead of a blank error.

**Verified before trusting the output:**
- `sales_amount = quantity × price` is enforced at Silver-layer load time, not left for every downstream query to recompute correctly on its own.
- `dim_customers.gender` prefers the CRM value and only falls back to ERP where CRM has `n/a` — checked so the two sources don't silently overwrite each other in either direction.
- `dim_products` excludes rows where `prd_end_dt IS NOT NULL`, confirming the view holds only current products and won't double-count retired SKUs in product-performance queries.
- Customer/location IDs from ERP carry a `NAS` prefix and extra hyphens CRM doesn't use — stripped before the join, then spot-checked so customer records don't silently fail to match across systems.

| Layer | Contents | Key transformation |
|---|---|---|
| Bronze | 6 raw CRM/ERP tables | None — loaded as-is, truncate & reload |
| Silver | Same 6 tables, cleansed | Dedup, type casting, code normalization, sales recalculation |
| Gold | 3 star-schema views | Joins + `ROW_NUMBER()` surrogate keys, no physical load |

![Bronze/Silver/Gold architecture](https://github.com/NomanAslam12/sql-data-warehouse-project/raw/main/docs/data_architecture.drawio.png)

The Gold layer's star schema — `fact_sales` joined to `dim_customers` and `dim_products` — has the `sales_amount = quantity × price` rule annotated directly on the model, since that's the one calculation every downstream report depends on:

![gold star schema with sales calculation noted](https://github.com/NomanAslam12/sql-data-warehouse-project/raw/main/docs/data_model.drawio.png)

**Result:** a single `gold.fact_sales` / `gold.dim_customers` / `gold.dim_products` model that any BI tool can query directly for sales, customer, and product analysis — with the source systems' data-quality problems already resolved upstream instead of patched over in every report.

**Limitations:** batch, full truncate-and-reload only — no historization, so no way yet to see how a customer or product record changed over time. Built against a single latest snapshot of course-provided CRM/ERP data, not a live production feed. Incremental loading and window-function-based analysis are the planned next steps rather than something this version does.

*Tools: SQL Server, T-SQL, SSMS | [View repo on GitHub →](https://github.com/NomanAslam12/sql-data-warehouse-project) | [Data flow & integration diagrams →](https://github.com/NomanAslam12/sql-data-warehouse-project/tree/main/docs)*
