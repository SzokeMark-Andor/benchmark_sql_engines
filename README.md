# Benchmark SQL Engines

This repository hosts the consolidated results from a series of SQL engine benchmarks.  
The goal of this project is to provide reproducible performance comparisons across multiple SQL engines on identical workloads and data scale factors.

## Contents

The following files are available in the repository:

- `Combined_sf1_sf10_by_engine_query_run.xls`  
  Results from individual query runs at scale factor 1 (SF=1) and 10 (SF=10) across all engines, aggregated by engine, query, and run.

- `merged_dremio_sf1_sf10.xls`  
  Aggregate results for the **Dremio** engine at SF=1 and SF=10.

- `merged_drill_sf1_sf10.xls`  
  Aggregate results for the **Drill** engine at SF=1 and SF=10.

- `merged_presto-velox_sf1_sf10.xls`  
  Aggregate results for the **Presto‑Velox** engine at SF=1 and SF=10.

- `phoenix_sf1_first3runs_q01_q30_with_q22_placeholders.xls`  
  The Phoenix results for the first three runs of `q01.sql` through `q30.sql`.  
  Since `q22.sql` did not run successfully, placeholder rows are included for Phoenix’s `q22` runs.

- `merged_all_engines_sf1_sf10.xls`  
  A consolidated Excel file combining all engines and both scale factors.

- `all_engines_sf1_sf10_merged.csv`  
  The same consolidated data in CSV format (UTF‑8). This file is easier to use in data analysis workflows (e.g., Pandas, R).

Each file contains the following columns:

- **`engine`**: Name of the database engine (e.g., `clickhouse`, `datafusion`, `doris`, `dremio`, `duckdb`, `impala`, `phoenix`, `presto-velox`, `spark`, `trino`).
- **`query`**: Benchmark query identifier (`q01.sql`–`q30.sql`).
- **`run`**: Run number (1–3). Three runs were performed per query to reduce variance.
- **`sf1`** and **`sf10`**: Median runtime (in seconds) at scale factors 1 and 10, respectively.  
  Missing values indicate that a particular query did not run successfully (for example, Phoenix `q22.sql`).

## Usage

1. **Opening the data:**  
   You can open the `.xls` files with any spreadsheet software.  
   Alternatively, read the CSV in Python:

   ```python
   import pandas as pd
   df = pd.read_csv("all_engines_sf1_sf10_merged.csv")
   print(df.head())
