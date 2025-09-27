# Benchmark SQL Engines

This repository hosts the consolidated results from a series of SQL engine benchmarks. The goal of this project is to provide reproducible performance comparisons across multiple SQL engines on identical workloads and data scale factors.

## Contents

The following files are included in this repository:

- `Combined_sf1_sf10_by_engine_query_run.xls` – Results from individual query runs at scale factor 1 (SF=1) and 10 (SF=10) across all engines, aggregated by engine, query, and run.
- `merged_dremio_sf1_sf10.xls` – Aggregate results for the **Dremio** engine at SF=1 and SF=10.
- `merged_drill_sf1_sf10.xls` – Aggregate results for the **Drill** engine at SF=1 and SF=10.
- `merged_presto-velox_sf1_sf10.xls` – Aggregate results for the **Presto‑Velox** engine at SF=1 and SF=10.
- `phoenix_sf1_first3runs_q01_q30_with_q22_placeholders.xls` – Results for the Phoenix engine for the first three runs of `q01.sql` through `q30.sql`. Since `q22.sql` did not run successfully under Phoenix, placeholder rows are included.
- `merged_all_engines_sf1_sf10.xls` – Consolidated results for all engines and both scale factors.
- `all_engines_sf1_sf10_merged.csv` – The same consolidated data in CSV format (UTF‑8). This file is easy to load into Pandas or other analysis tools.

Each file contains the following columns:

- **`engine`** – Name of the database engine (e.g., `clickhouse`, `datafusion`, `doris`, `dremio`, `duckdb`, `impala`, `phoenix`, `presto-velox`, `spark`, `trino`).
- **`query`** – Benchmark query identifier (`q01.sql`–`q30.sql`).
- **`run`** – Run number (1–3). Three runs were performed per query to reduce variance.
- **`sf1`** and **`sf10`** – Median runtime (in seconds) at scale factors 1 and 10, respectively. Missing values indicate a run did not complete successfully (e.g., Phoenix `q22.sql`).

## Installation & Reproduction

These benchmarks were executed **locally**. Each engine ran in its own Docker container; orchestration was done with Docker Compose and PowerShell scripts. To reproduce the results, follow these steps:

1. **Download and unpack the archive.** Obtain the MEGA archive linked below (see [Additional Resources](#additional-resources)). This archive contains:
   - `cli.zip`: a portable CLI environment with PowerShell scripts to run the benchmarks.
   - Engine-specific zip files (e.g., `doris-local.zip`, `drill-local.zip`, `phoenix-local.zip`, `presto-velox.zip`), each containing a Docker build context and configuration for that engine.
   - Pre-generated results CSVs (e.g., `results_dremio_sf1.csv`) and final combined results (`sf1-6engines.xlsx`, `sf10-6engines.xlsx`).

   Unzip all files into a single directory on your workstation (for example, `D:\benchmark`), preserving the folder structure. This ensures the scripts can locate the engine builds, data sets, and configuration files.

2. **Set up the environment.** Install Docker Desktop and verify it has enough memory and CPUs available for multiple containers. Change to the unpacked `benchmark_local` folder and review the provided `docker-compose.yml` for service definitions. Each engine may require slight adjustments; for example, Phoenix needs the correct `phoenix-client.jar`, and Drill requires a patched JDBC driver. If you wish to rebuild any images, use the Dockerfiles inside each engine’s zip folder.

3. **Prepare the data.** Generate Star Schema Benchmark (SSB) data sets for scale factors 1 and 10. Scripts in the MEGA archive (e.g., `build_ssb_data.sh`) show how to run `dsdgen` to produce data and convert it into CSV or Parquet. The central fact table is `lineorder` (sometimes called `lineitem`). At SF=1, this table contains roughly 6 million rows (≈1 GB compressed), and at SF=10 about 60 million rows (≈10 GB compressed). After generation, load the data into each engine using its bulk loading tools; example commands are provided in `run_sf1.sh` and `run_sf10.sh`.

4. **Run the queries.** Execute the 30 SSB queries three times each using the shell scripts in `benchmark_local`. The key scripts are:
   - `run_sf1.sh` – orchestrates loading SF=1 data and running all queries three times, capturing runtimes to CSV.
   - `run_sf10.sh` – similar but for SF=10.

   The scripts disable caching where possible, perform warm‑up runs, and log outputs. If a container crashes or a query fails (as happened with Phoenix’s `q22.sql`), adjust the script or container settings (e.g., increase memory, apply patch files) and re‑run. Troubleshooting notes are included in `error‑fix` documents in the archive.

5. **Merge the results.** Use the provided Python notebooks (`benchmark_local.ipynb`) or scripts (`merge_results.py`) to parse the raw logs and compute per‑query medians and geometric means. These scripts produce per‑engine `.xls` files and the consolidated `all_engines_sf1_sf10_merged.csv`.

6. **Analyse the data.** Load `all_engines_sf1_sf10_merged.csv` into Pandas or Excel and compute summary statistics. You can reproduce the charts shown in our conversations (geometric mean bars, scale‑up ratios, boxplots) with a few lines of Python. Refer to `benchmark_local.ipynb` for examples.

## Additional Resources
Get the archive: **[MEGA – Benchmark environment & results](https://mega.nz/folder/v8kzhZgB#8vTCaqSb8tHb_kQxFK6yHQ)**
The MEGA folder contains the full local benchmark environment, including scripts, zipped engines, and raw results. Key items in the archive:

- **Zipped engine packages**
  - `doris-local.zip`, `drill-local.zip`, `phoenix-local.zip`, `presto-velox.zip`, etc. – each contains Dockerfiles, custom configuration, and any patched libraries needed to run that engine.
  - `cli.zip` – a CLI environment with PowerShell scripts (`run_sf1.ps1`, `run_sf10.ps1`) used to execute benchmarks. Unzip this to your working directory and run from PowerShell to orchestrate containers and queries.

- **Data and results**
  - `results_dremio_sf1.csv`, `results_sf1_full_patched.csv`, `results_sf1_velox.csv`, etc. – intermediate results generated during runs, capturing per-query runtimes for specific engines.
  - `sf1-6engines.xlsx` and `sf10-6engines.xlsx` – Excel files summarising all six engines at SF=1 and SF=10, respectively. These may be useful for quick comparisons.
  - `Dremio_results_sf10.xlsx` and similar – per‑engine summary Excel files.

- **Scripts and notebooks**
  - `benchmark_local/run_sf1.sh` and `run_sf10.sh` – main driver scripts to prepare data, launch containers, and execute all 30 queries three times. They also handle log capture and error checking.
  - `benchmark_local/benchmark_local.ipynb` – a Jupyter notebook demonstrating how to merge results, compute medians and geometric means, and generate charts. Running this notebook will reproduce the plots discussed in our conversations.
  - `benchmark_local/docker-compose.yml` – defines the container services (one per engine) and network configuration. Adjust resource limits here if certain containers crash due to memory constraints.
  - `benchmark_local/dremio_smoke_test.sh` – a smoke test used to confirm Dremio connectivity and query execution before running the full benchmark.
  - **Audit query revisions** – updated SQL files with minor fixes for consistent semantics across engines (e.g., replacing unsupported functions or syntax). These revised queries are the ones executed during the benchmarks.
  - **Error‑fix notes** – text files describing the troubleshooting process: solving Phoenix `q22` failures, fixing Drill driver errors, resolving container start problems, and tuning memory settings in Docker Compose.

By downloading the archive and following the scripts in `benchmark_local`, you can reproduce the entire benchmark environment on your own machine.

## Usage

To load the merged CSV file in Python and inspect the data:

    import pandas as pd
    df = pd.read_csv("all_engines_sf1_sf10_merged.csv")
    print(df.head())

    # Compute simple averages per engine
    geo_sf1 = df.groupby('engine')['sf1'].mean()
    geo_sf10 = df.groupby('engine')['sf10'].mean()

    print("Average SF=1 runtimes:")
    print(geo_sf1.sort_values())

    print("\nAverage SF=10 runtimes:")
    print(geo_sf10.sort_values())

You can also create plots similar to those generated in our conversations by using Matplotlib or Seaborn (see `benchmark_local.ipynb` for examples). Key visualisations include geometric mean bar charts, scale‑up ratio bar charts, and boxplots of per‑query medians.

## Contributing

Contributions are welcome! Please open issues or pull requests if you have improvements, new benchmarks, or questions. When adding new data, ensure that your CSV or Excel files follow the same column structure and update this README accordingly.

---

Thank you for using this benchmark suite. We hope these results help you make informed decisions about SQL engine performance.
