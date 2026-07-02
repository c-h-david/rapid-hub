# Migrating from RAPID1 to RAPID2

Welcome to RAPID2! This guide is designed for veteran users who are upgrading their legacy RAPID1 datasets and workflows to the new RAPID2 standards.

## Why the Change?

RAPID2 represents a massive architectural shift from legacy Fortran 90 to a modern, object-oriented Python 3 framework. 

As part of this modernization, RAPID2 has transitioned its static input files (such as connectivity, basin IDs, and Muskingum parameters) from standard Comma Separated Values to Apache Parquet. This shift drastically improves read speeds, enforces strict memory typing, and reduces file sizes for massive river networks.

---

## 1. Convert Legacy Static Files

You do not need to manually recreate your legacy static files. RAPID2 includes a dedicated command-line tool called `rapid1to2` to automatically cast your legacy files into the new Parquet format.

By providing the paths to your existing legacy files (such as connectivity, basin, k and x parameters, coordinates, and coupling), the tool will automatically generate corresponding Parquet files in the same directories. The connectivity file is mandatory for this tool, as it establishes the master river IDs for the network, while the other parameters are optional but recommended.

---

## 2. Convert Legacy External Inflow Volumes

In RAPID1, external inflows were often provided as accumulated volumes. RAPID2 expects external inflows to be provided as a flow rate in cubic meters per second.

You can convert your legacy volume NetCDF files into the new flow rate format using the `m3rivtoqext` command-line tool. The tool will automatically detect the time step duration from the file metadata and convert the volumes into rates for you.

---

## 3. Update Your Namelist

RAPID2 uses a strict YAML format for its namelist, completely replacing the old Fortran namelist text files. 

When creating your new namelist, ensure you point to the newly generated Parquet files rather than the old Comma Separated Value files, and ensure your external inflow variables match the new format. 

Once your namelist is updated, you are ready to run the model using the standard RAPID2 execution command. Welcome to the new ecosystem!
