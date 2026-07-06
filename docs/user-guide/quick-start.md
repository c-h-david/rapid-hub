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

RAPID2 uses a synthetic 5-reach river network for testing, hosted on Zenodo (DOI: 10.5281/zenodo.21085813). Since we installed via `pip`, the Sandbox download tool is automatically available in your terminal:

```bash
dsandbox
```

Once the download completes, you will notice three main file types populated across your new `input/Sandbox/` and `output/Sandbox/` directories:

* **`.parquet` files:** Fast, columnar data files storing your network connectivity (`con`) and Muskingum routing parameters (`kpr`, `xpr`).
* **`.yml` files:** YAML configuration files (namelists) that instruct the model on which inputs, outputs, and parameters to use (`nml`).
* **`.nc4` files:** NetCDF4 files storing multidimensional scientific data, such as your external inflows (`Qex`), initial states (`Q00`), outputs (`Qou`), and final states (`Qfi`).

## 3. Run the Model

RAPID2 uses a YAML configuration file (a "namelist"). Execute the model by pointing the CLI to the Sandbox namelist you just downloaded:

```bash
rapid2 --namelist input/Sandbox/nml_Sandbox.yml
```

When the progress bar finishes, RAPID2 will have generated your simulated outflow data (`Qou`) and your final state file (`Qfi`) in the `output/Sandbox/` directory.

## 4. Visualize the Results

To see how our simulated outflow compares to observed data, we will use two bundled RAPID2 CLI tools.

First, spatially and temporally sub-sample your high-resolution output (`Qou`) to isolate the river reaches where observations exist (`obs`) and match their daily (`86400` seconds) cadence, creating a Model Equivalent (`Qme`) to observations:

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

