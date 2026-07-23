# Migrating from RAPID to RAPID2

Welcome to RAPID2! This guide is designed for veteran users who are upgrading their legacy RAPID datasets and workflows to the new RAPID2 standards.

## Why the Change?

### Embracing Modern Standards

RAPID2 represents a massive architectural shift from legacy Fortran to a modern Python 3 framework. This redesign makes RAPID2 perfectly suited for modern cloud infrastructure, allowing you to run routing models in close proximity to massive NASA data production and archiving centers. However, if your workflows still rely on traditional High-Performance Computing (HPC) environments, rest assured that RAPID2 runs there just as smoothly.

Because of this architectural shift, native installation is finally a breeze! While we still proudly publish Docker images, you can now bypass the complex Fortran, PETSc, and MPI setup of the past with a simple pip command:

```bash
pip install rapid2
```

As part of this modernization, RAPID2 has transitioned its static input files (such as connectivity, basin IDs, and Muskingum parameters) from standard Comma Separated Values (`.csv`) to Apache Parquet (`.parquet`). This shift drastically improves read speeds, enforces strict memory typing, and reduces file sizes for massive river networks. Similarly, timeseries observations have migrated from legacy CSVs to highly efficient netCDF files. This introduces the concept of a "model equivalent" to seamlessly align observed and modeled data, streamlining goodness-of-fit calculations and hydrograph plotting.

### What's New to Get Excited About?
This transition goes beyond changing programming languages—it completely transforms what you can accomplish:

- **A Unified Ecosystem:** The legacy RAPID routing model and the Reproducible Routing Rituals (RRR) preprocessing toolbox have been seamlessly merged into a single, cohesive RAPID2 package.
- **Massive Code Condensation:** By leveraging Python's modern scientific stack, we've shrunk the codebase from a combined ~37,000 lines of code across RAPID1 (~24,000 lines, ~871,000 characters) and RRR (~13,000 lines) down to a lean, highly maintainable ~8,500 lines (~293,000 characters) in RAPID2.
- **Advanced Data Assimilation & Bias Correction:** We are fixing and refining the early developmental versions of data assimilation that were present in RAPID1. Furthermore, RAPID2 introduces entirely new Bias Correction capabilities that were never available in the legacy model!
- **A Developer-First Experience:** RAPID2 is drastically easier to test and contribute to. We've introduced crystal-clear guidelines on syntax, nomenclature, contribution mechanisms, coding style, and strict linting standards. Plus, we've built a small yet powerful synthetic "Sandbox" dataset that can be instantly leveraged for full-blown continuous integration testing!

### Passing the Torch to RAPID2

The legacy Fortran-based `rapid` has officially reached its End of Life (EOL). As of its 20-year anniversary on 26 January 2026, the original codebase was sunset and is no longer actively developed, maintained, or supported. All future development has transitioned to the new Python 3 framework of `rapid2`, which is officially hosted on [GitHub](https://github.com/c-h-david/rapid2).

## 1. Convert Legacy Static Files

You do not need to manually recreate your legacy static files. RAPID2 includes a dedicated command-line tool called `rapid1to2` to automatically cast your legacy CSV files into the new Parquet format.

By providing the paths to your existing legacy files, the tool will automatically generate corresponding Parquet files in the same directories. The connectivity file is mandatory for this tool, as it establishes the master river IDs for the network, while the other parameters are optional but recommended.

```bash
rapid1to2 \
    --connectivity input/Tutorial/rapid_connect_pfaf_74.csv \
    --basin input/Tutorial/riv_bas_id_pfaf_74_topo.csv \
    --k_parameter input/Tutorial/k_pfaf_74_nrm.csv \
    --x_parameter input/Tutorial/x_pfaf_74_nrm.csv \
    --coordinates input/Tutorial/coords_pfaf_74.csv \
    --coupling input/Tutorial/rapid_coupling_pfaf_74_GLDAS.csv
```

## 2. Convert Legacy External Inflow Volumes

In legacy RAPID, external inflows were often provided as accumulated volumes per timestep (`m3_riv`). RAPID2 simplifies this by expecting a flow rate in cubic meters per second (`Qext`).

You can convert your legacy volume NetCDF files into the new flow rate format using the `m3rivtoqext` command-line tool. The tool will automatically detect the time step duration from the file metadata and convert the volumes into rates for you.

```bash
m3rivtoqext \
    --external_volume input/Tutorial/m3_riv_pfaf_74_GLDAS_2.1_VIC_2010-01.nc4 \
    --external_inflow input/Tutorial/Qext_pfaf_74_GLDAS_2.1_VIC_2010-01.nc4
```

## 3. Update Your Namelist

RAPID2 uses a strict YAML format for its namelist, completely replacing the old Fortran namelist text files.

When creating your new namelist, ensure you point to the newly generated Parquet files rather than the old CSV files, and ensure your external inflow variables match the new `Qext` format.

Example `namelist_Tutorial.yml`:

```yaml
# -----------------------------------------------------------------------------
# Mandatory input files
# -----------------------------------------------------------------------------
Qex_ncf: './input/Tutorial/Qext_pfaf_74_GLDAS_2.1_VIC_2010-01.nc4'
Q00_ncf: './input/Tutorial/Qinit_pfaf_74_GLDAS_2.1_VIC_2010-01.nc4'

con_pqt: './input/Tutorial/con_pfaf_74.parquet'
kpr_pqt: './input/Tutorial/kpr_pfaf_74_nrm.parquet'
xpr_pqt: './input/Tutorial/xpr_pfaf_74_nrm.parquet'

bas_pqt: './input/Tutorial/bas_pfaf_74_topo.parquet'

# -----------------------------------------------------------------------------
# Mandatory values
# -----------------------------------------------------------------------------
IS_dtR: 900

# -----------------------------------------------------------------------------
# Mandatory output files
# -----------------------------------------------------------------------------
Qou_ncf: './output/Tutorial/Qout_pfaf_74_GLDAS_2.1_VIC_2010-01_tst.nc4'
Qfi_ncf: './output/Tutorial/Qfinal_pfaf_74_GLDAS_2.1_VIC_2010-01_tst.nc4'
```

Once your namelist is updated, you are ready to run the model using the standard RAPID2 execution command:

```bash
rapid2 --namelist input/Tutorial/namelist_Tutorial.yml
```

Welcome to the new ecosystem!
