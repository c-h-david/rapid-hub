# Running RAPID

## Getting Started with Execution

RAPID uses a YAML configuration file (namelist) to specify input files, parameters, and output locations. The basic command to run RAPID is:

```bash
rapid2 -nl namelist.yml
```

or

```bash
rapid2 --namelist namelist.yml
```

**Expected Output (when files exist):**
```
Namelist file: namelist.yml
Done
```

**Expected Output (when input files are missing):**
```
Namelist file: namelist.yml
ERROR - Unable to open ./input/Test/rapid_connect_Test.csv
```

## Namelist Configuration

RAPID requires a YAML namelist file that specifies all necessary input and output files. Here's an example configuration:

```yaml
---
# External inflow file (NetCDF4)
Qex_ncf: './input/Test/Qext_Test_20000101_20000102.nc4'

# Initial discharge state file (NetCDF4)
Q00_ncf: './input/Test/Qinit_Test_20000101_20000102.nc4'

# River network connectivity (CSV)
con_csv: './input/Test/rapid_connect_Test.csv'

# Muskingum k parameter (CSV)
kpr_csv: './input/Test/k_Test.csv'

# Muskingum x parameter (CSV)
xpr_csv: './input/Test/x_Test.csv'

# River basin IDs (CSV)
bas_csv: './input/Test/riv_bas_id_Test.csv'

# Routing time step (seconds)
IS_dtR: 900

# Output discharge file (NetCDF4)
Qou_ncf: './output/Test/Qout_Test_20000101_20000102_tst.nc4'

# Final discharge state file (NetCDF4)
Qfi_ncf: './output/Test/Qfinal_Test_20000101_20000102_tst.nc4'
```

## Required Input Files

### River Network Files (CSV format)
- **Connectivity file** (`con_csv`): Defines river network topology with upstream/downstream relationships
- **Basin IDs file** (`bas_csv`): Specifies which river reaches are included in the simulation
- **Parameter files** (`kpr_csv`, `xpr_csv`): Muskingum routing parameters for each river reach

### Hydrological Data (NetCDF4 format)
- **External inflow** (`Qex_ncf`): Lateral inflow from land surface models
- **Initial conditions** (`Q00_ncf`): Starting discharge values for all river reaches

## Output Files

RAPID produces the following outputs:

- **Discharge time series** (`Qou_ncf`): Routed discharge for all time steps
- **Final state** (`Qfi_ncf`): Final discharge values for model restart

## Command Line Options

RAPID accepts the following command line arguments:

- `-nl, --namelist`: Path to the YAML namelist file (required)

## Important Note About Input Data

The namelist example above uses sample file paths. To actually run RAPID, you need:

1. **Real input data files** (connectivity, parameters, external inflow, initial conditions)
2. **Sample datasets** are available from the links below
3. **Create output directories** before running RAPID

## Sample Data

Download sample input/output data to test your RAPID installation:

### San Antonio and Guadalupe River Basins (Texas)
Download all input and output data from David et al. (2011, JHM). These include NHDPlus river network connectivity, lateral inflow from the land surface (computed with Noah-MP) and gage measurements (from USGS NWIS) for a 4-year run (between 2004-01-01 and 2007-12-30).

[![10.5281/zenodo.16565](https://zenodo.org/badge/doi/10.5281/zenodo.16565.svg)](http://dx.doi.org/10.5281/zenodo.16565)

### SIM-France Domain
Download all input and output data from David et al. (2011, HP). These include SIM-France river network connectivity, inflow from the land surface (computed with ISBA), bilateral inflow from groundwater (computed with MODCOU) for a 10-year run (between 1995-08-01 and 2005-07-31).

[![10.5281/zenodo.30228](https://zenodo.org/badge/doi/10.5281/zenodo.30228.svg)](http://dx.doi.org/10.5281/zenodo.30228)

## RAPID Scripts

Additional scripts and tools to help with RAPID execution are available in the [RAPID repository](https://github.com/c-h-david/rapid2/) which contains the most current helper scripts and utilities.

## Further Documentation

For detailed tutorials and advanced usage, see the [Tutorials](../tutorials/training.md) section.

## Need Help?

If you need information on using RAPID that is not included here or in the RAPID publications, please don't hesitate to [ask on GitHub](https://github.com/c-h-david/rapid2/issues/new/choose) as feedback is enthusiastically appreciated and new users welcome!
