# Quick Start: The RAPID2 Sandbox

Get RAPID2 running in under five minutes using our synthetic Sandbox environment. This tutorial covers installation, running a baseline simulation, and plotting the resulting hydrographs.

---

## 1. Installation

RAPID2 is a modern Python package. To install it, we highly recommend using a Python 3.11 virtual environment to avoid dependency conflicts, then installing via `pip`:

```bash
python3 -m venv rapid_env
source rapid_env/bin/activate
pip install rapid2
```

## 2. Get the Sandbox Data

RAPID2 uses a synthetic 5-reach river network for testing, hosted on Zenodo (DOI: 10.5281/zenodo.17298742). Since we installed via `pip`, we can fetch and run the Sandbox download script directly from the repository source:

```bash
mkdir tst
cd tst
wget [https://raw.githubusercontent.com/c-h-david/rapid2/main/tst/tst_dwnl_Sandbox.sh](https://raw.githubusercontent.com/c-h-david/rapid2/main/tst/tst_dwnl_Sandbox.sh)
chmod +x tst_dwnl_Sandbox.sh
./tst_dwnl_Sandbox.sh
cd ..
```

Once the download completes, you will notice two main file types in your new `input/Sandbox/` directory:

* **`.parquet` files:** Fast, columnar data files storing your network connectivity (`con_Sandbox.parquet`) and Muskingum routing parameters (`kpr`, `xpr`).
* **`.nc4` files:** NetCDF4 files storing multidimensional scientific data, such as your external inflows (`Qex`) and initial states (`Q00`).

## 3. Run the Model

RAPID2 uses a YAML configuration file (a "namelist") to map all your inputs, outputs, and routing time steps. Execute the model by pointing the CLI to the Sandbox namelist you just downloaded:

```bash
rapid2 --namelist input/Sandbox/nml_Sandbox.yml
```

When the progress bar finishes, RAPID2 will have generated your simulated outflow data (`Qou`) and your final state file (`Qfi`) in the `output/Sandbox/` directory.

## 4. Visualize the Results

To see how our simulated outflow compares to observed data, we will use two bundled RAPID2 CLI tools.

First, sub-sample your high-resolution output (`Qou`) to match the daily (86,400 seconds) cadence of our observations, creating a Model Equivalent (`Qme`):

```bash
subsampleqout \
  -Qou output/Sandbox/Qou_Sandbox_19700101_19700110_TR_tst.nc4 \
  -obs input/Sandbox/obs_Sandbox.parquet \
  -dtO 86400 \
  -Qme output/Sandbox/Qme_Sandbox_tst.nc4
```

Next, generate SVG plots comparing the model equivalent (`Qme`) to the true observations (`Qob`):

```bash
hydrographs \
  -Qob input/Sandbox/Qob_Sandbox_19700101_19700110_TR.nc4 \
  -Qme output/Sandbox/Qme_Sandbox_tst.nc4 \
  -max 100 \
  -hyd output/Sandbox/hyd.svg
```

Check your `output/Sandbox/` folder for the newly generated `.svg` files—you have successfully run and visualized your first RAPID2 simulation!

