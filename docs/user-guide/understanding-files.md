# Understanding RAPID2 Files

RAPID2 separates its files into three clear categories: 1) static description of the river network and its parameters, 2) dynamic water data, and 3) model configuration. 

To keep things organized and mathematically clear, RAPID2 uses a strict 3-letter semantic naming system for its datasets and their associated Command Line Interface flags (e.g., `con` for connectivity, `Qex` for external inflow, and `nml` for namelist). 

Here is a quick guide to what goes inside each dataset and how to reference them.

## 1. Static Network Files (`.parquet`)

RAPID2 uses Apache Parquet for its static network files because they are incredibly fast and compact. You can think of them as simple, highly optimized spreadsheet tables. 

Below is a summary of the static files you will use to define the physical topology and routing parameters of your river network:

| Dataset | CLI Flags | Short Key | Variable(Type) | Description |
| :--- | :--- | :--- | :--- | :--- |
| **Connectivity** | `-con`, `--connectivity` | `con_pqt` | `riv(int32)`, `dwn(int32)` | Gives the relationship between upstream and downstream reaches in the full network. |
| **k Parameter** | `-kpr`, `--k_parameter` | `kpr_pqt` | `riv(int32)`, `kpr(float64)` | The Muskingum time parameter `k` (seconds) for each reach of the full network. |
| **x Parameter** | `-xpr`, `--x_parameter` | `xpr_pqt` | `riv(int32)`, `xpr(float64)` | The Muskingum non-dimensional parameter `x` for each reach of the full network. |
| **Basin Subset** | `-bas`, `--basin` | `bas_pqt` | `riv(int32)` | Defines the simulated subset of the full routing network; sorted from upstream to downstream. |
| **Observation Subset** | `-obs`, `--observations` | `obs_pqt` | `riv(int32)` | Defines the observed subset of the full routing network (e.g., stream gauges). |
| **Coordinates** | `-crd`, `--coordinates` | `crd_pqt` | `riv(int32)`, `lon(float64)`, `lat(float64)` | Geospatial longitude and latitude representing each river reach. |
| **Coupling** | `-cpl`, `--coupling` | `cpl_pqt` | `riv(int32)`, `skm(float64)`, `1bi(int32)`, `1bj(int32)` | Maps the Land Surface Model (LSM) grid to river catchments and in the network. |

> **Tip:** If you want to peak inside one of these files using a command-line tool like `parq`, it looks like a clean table. For example, `parq head input/Sandbox/con_Sandbox.parquet ` looks like this:
> 
> | riv | dwn |
> | --- | --- |
> | 10  | 30  |
> | 20  | 30  |
> | 30  | 50  |
> | 40  | 50  |
> | 50  | 0   |

## 2. Dynamic Water Files (`.nc4`)

Time-varying water data is stored in NetCDF4 files. Conceptually, these are grids of data organized across two primary dimensions: **time** and **location** (`rivid`).

All of these files contain standard coordinate metadata (`time`, `lon`, `lat`, `rivid`) to ensure your data is adequately described. Here are the primary variables you will interact with:

| Dataset | CLI Flags | Short Key | Main Variable (Dimensions) | Description |
| :--- | :--- | :--- | :--- | :--- |
| **External Inflow** | `-Qex`, `--external_inflow` | `Qex_ncf` | `Qext` (`time`, `rivid`) | The forcing data (m³/s) entering the network. |
| **Outflow Discharge** | `-Qou`, `--outflow` | `Qou_ncf` | `Qout` (`time`, `rivid`) | The primary routing results (m³/s) exiting each reach. |
| **Initial State** | `-Q00`, `--initial_outflow` | `Q00_ncf` | `Qout` (`time`, `rivid`) | An instantaneous snapshot of the water in the network at the exact start of a simulation. |
| **Final State** | `-Qfi`, `--final_outflow` | `Qfi_ncf` | `Qout` (`time`, `rivid`) | An instantaneous snapshot of the water in the network at the exact end of a simulation. |
| **Observations** | `-Qob`, `--observations` | `Qob_ncf` | `Qout` (`time`, `rivid`) | True observed discharge (m³/s), used for validation, bias correction, or data assimilation. |
| **Model Equivalent** | `-Qme`, `--model_equivalent` | `Qme_ncf` | `Qout` (`time`, `rivid`) | The simulated discharge sub-sampled in space and time to match the observations. |

> **Tip:** Because NetCDF4 files are multi-dimensional, they do not print neatly into terminal tables like Parquet files do, but command line tools like `ncdump` can help: `ncdump -h input/Sandbox/Qex_Sandbox_19700101_19700110_TR.nc4`. Alternatively, one can peek at the actual data is with a quick Python snippet using the netCDF4 library:

> ```python
> import netCDF4
> f = netCDF4.Dataset('input/Sandbox/Qex_Sandbox_19700101_19700110_TR.nc4')
> print(f.variables['Qext'].shape)
> print(f.variables['Qext'][0:3, 0:3])
> ```

## 3. The Configuration Namelist (`.yml`)

The namelist ties it all together. It is a simple YAML text file that acts as the control center for your simulation. You pass it to the model using `rapid2 --namelist your_namelist.yml`.

A typical namelist for a RAPID2 run contains the following required keys:

| Key | Type | Description |
| :--- | :--- | :--- |
| `Qex_ncf`, `Q00_ncf` | `Filepath` | Paths to your input NetCDF files (inflow and initial state). |
| `con_pqt`, `kpr_pqt`, `xpr_pqt` | `Filepath` | Paths to your static network Parquet files. |
| `bas_pqt` | `Filepath` | Path to your simulated basin subset Parquet file. |
| `IS_dtR` | `Integer` | The routing time step in seconds (e.g., `900`). |
| `Qou_ncf`, `Qfi_ncf` | `Filepath` | Paths where RAPID2 will save your generated outputs. |