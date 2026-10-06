# Understanding RAPID2 Commands

RAPID2 provides a unified Command Line Interface (CLI) dispatcher to
streamline your routing workflows. Instead of relying entirely on scattered
scripts, functions are organized logically by category and sub-command.

> This note was written in anticipation for a future release.

## Command Reference

Below is a summary of the dispatcher categories, their sub-commands,
the underlying tool they map to, and their primary purpose. Tools marked
as *Planned* are currently in development and not yet available.

| Category | Sub-command | Tool / Status | Purpose |
| :--- | :--- | :--- | :--- |
| `run`   |            | `run`          | Execute core matrix routing model.|
| `fetch` | `sandbox`  | `dsandbox`     | Download Sandbox synthetic files.|
| `fetch` | `tutorial` | *Planned*      | Download Mississippi tutorial files.|
| `fetch` | `gldas2`   | `dgldas2`      | Download raw remote data.|
| `prep`  | `gldas2`   | `dgldas2`      | Fix time and units of raw data.|
| `inflow`| `lsm`      | `cpllsm`       | Map LSM grids to river networks.|
| `inflow`| `sandbox`  | `sandboxqext`  | Generate synthetic wave inflow.|
| `init`  | `zero`     | `zeroqinit`    | Generate cold-start initial states.|
| `init`  | `mean`     | *Planned*      | Generate warm-start initial states.|
| `sample`| `spacetime`| `subsampleqout`| Align NetCDF data to match gauges.|
| `bias`  | `learn`    | `ltir_scl`     | Compute LTIR scalars to learn bias.|
| `bias`  | `correct`  | `ltir_cor`     | Apply scalars to correct inflow.|
| `plot`  | `hydro`    | `hydrographs`  | Generate SVG hydrograph plots.|
| `comp`  | `netcdf`   | `cmpncf`       | Compare NetCDF files for testing.|
| `comp`  | `parquet`  | *Planned*      | Compare Parquet files for testing.|
| `legacy`| `static`   | `rapid1to2`    | Upgrade RAPID1 CSV files to Parquet.|
| `legacy`| `inflow`   | `m3rivtoqext`  | Convert volumes to flow rates.|
| `legacy`| `namelist` | *Planned*      | Convert Fortran namelists to YAML.|

## Core Workflow Concepts

- **Execution**: The `run` command is your primary entry point for
  launching simulations after your input files are prepared.
- **Data Preparation**: Use `fetch`, `prep`, `inflow`, and `init` to
  download, massage, and configure your data before routing.
- **Post-Processing**: Commands under `sample`, `bias`, and `plot` help
  you analyze results, correct errors, and visualize hydrographs.
- **Utilities**: The `comp` and `legacy` categories are designed for
  testing models and migrating older datasets to the RAPID2 standard.
