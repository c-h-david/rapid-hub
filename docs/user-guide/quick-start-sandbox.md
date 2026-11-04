# Quick Start: The RAPID2 Sandbox

Get RAPID2 running in under five minutes using our synthetic Sandbox
environment. This tutorial covers installation, running a baseline simulation,
and plotting the resulting hydrographs.

## 1. Installation

RAPID2 is a modern Python package. To install it, we highly recommend using a
Python 3.11 virtual environment to avoid dependency conflicts, then installing
via `pip`:

=== "Bash"

    ```bash
    python3 -m venv rapid_env
    source rapid_env/bin/activate
    pip install rapid2
    rapid2 --version
    ```

=== "Powershell"

    ```powershell
    python3 -m venv rapid_env |
    source rapid_env/bin/activate |
    pip install rapid2 |
    rapid2 --version
    ```

=== "Python"

    ```python
    # Install rapid2 into virtual environment first using terminal
    import rapid2
    print(rapid2.__version__)
    ```

> This tutorial was written for `rapid2 2.0.0b5`. If you've installed a
> previous version of RAPID2, consider doing `pip install --upgrade rapid2`.
>
> RAPID2 was designed for `python3.11` but users have had success with `3.12`,
> `3.13`, and `3.14`. You can check the version you have installed with
> `python --version`.

## 2. Get the Sandbox Data

RAPID2 uses a synthetic experiment for testing which uses a river network with
5 reaches and 2 gauges (like our logo), hosted on Zenodo, and described in more
detail in
[`SANDBOX.md`](https://github.com/c-h-david/rapid2/blob/main/SANDBOX.md).
The Sandbox download tool should automatically be available in your terminal
from the installation via `pip`:

=== "Bash"

    ```bash
    rapid2 pull sandbox
    ```

=== "Powershell"

    ```powershell
    rapid2 pull sandbox
    ```

=== "Python"

    ```python
    rapid2.pull.sandbox()
    ```

Once the download completes, you will notice three main file types populated
across your new `input/Sandbox/` and `output/Sandbox/` directories:

- **`.parquet` files:** Fast, columnar data files storing network connectivity
  (`con`), Muskingum routing parameters (`kpr`, `xpr`), among other "static"
  files.
- **`.yml` files:** YAML configuration files (namelists) that instruct the
  model on which inputs and parameters to use and which outputs to create
  (`nml`).
- **`.nc4` files:** NetCDF4 files storing multidimensional scientific data,
  such as your external external inflows (`Qex`), initial discharge states
  (`Q00`), discharge outputs (`Qou`), final discharge states (`Qfi`), model
  equivalent to observations (`Qme`), or observations (`Qob`).

## 3. Run the Model

RAPID2 uses a YAML configuration file (a "namelist"). Execute the model by
pointing the CLI to the Sandbox namelist you just downloaded:

=== "Bash"

    ```bash
    rapid2 core run --namelist input/Sandbox/nml_Sandbox_OL.yml
    ```

=== "Powershell"

    ```bash
    rapid2 core run --namelist input/Sandbox/nml_Sandbox_OL.yml
    ```

=== "Python"

    ```python
    r2.core.run(
      Qex_ncf = './input/Sandbox/Qex_Sandbox_19700101_19700110_FG.nc4',
      Q00_ncf = './input/Sandbox/Q00_Sandbox_19700101_19700110_FG.nc4',
      con_pqt = './input/Sandbox/con_Sandbox.parquet',
      kpr_pqt = './input/Sandbox/kpr_Sandbox.parquet',
      xpr_pqt = './input/Sandbox/xpr_Sandbox.parquet',
      bas_pqt = './input/Sandbox/bas_Sandbox_ascend.parquet',
      IS_dtR = 900,
      Qou_ncf = './output/Sandbox/Qou_Sandbox_19700101_19700110_OL_tst.nc4',
      Qfi_ncf = './output/Sandbox/Qfi_Sandbox_19700101_19700110_OL_tst.nc4'
    )
    ```

When the progress bar finishes, RAPID2 will have generated your new simulated
outflow data (`Qou`) and your final state file (`Qfi`) in the `output/Sandbox/`
directory including the `_tst` suffix in the file names.

## 4. Visualize the Results

To see how our simulated outflow compares to observed data, we will use two
bundled RAPID2 CLI tools.

First, spatially and temporally sub-sample your high-resolution output (`Qou`)
to isolate the river reaches where observations exist (`obs`) and match their
daily (`86400` seconds) cadence, creating a Model Equivalent (`Qme`) to
observations:

=== "Bash"

    ```bash
    rapid2 prep sample \
      -Qou output/Sandbox/Qou_Sandbox_19700101_19700110_OL_tst.nc4 \
      -obs input/Sandbox/obs_Sandbox.parquet \
      -dtO 86400 \
      -Qme output/Sandbox/Qme_Sandbox_19700101_19700110_OL_tst.nc4
    ```

=== "Powershell"

    ```powershell
    rapid2 prep sample `
      -Qou output/Sandbox/Qou_Sandbox_19700101_19700110_OL_tst.nc4 `
      -obs input/Sandbox/obs_Sandbox.parquet `
      -dtO 86400 `
      -Qme output/Sandbox/Qme_Sandbox_19700101_19700110_OL_tst.nc4
    ```

=== "Python"

    ```python
    rapid2.prep.sample( `
      Qou = output/Sandbox/Qou_Sandbox_19700101_19700110_OL_tst.nc4,
      obs = input/Sandbox/obs_Sandbox.parquet,
      dtO = 86400,
      Qme = output/Sandbox/Qme_Sandbox_19700101_19700110_OL_tst.nc4
    )
    ```

Next, generate SVG plots comparing the model equivalent (`Qme`) to the true
observations (`Qob`), setting the maximum value of discharge on the vertical
axis (`125`):

=== "Bash"

    ```bash
    rapid2 eval graph \
      -Qob input/Sandbox/Qob_Sandbox_19700101_19700110_TR.nc4 \
      -Qme output/Sandbox/Qme_Sandbox_19700101_19700110_OL_tst.nc4 \
      -max 125 \
      -hyd output/Sandbox/hyd_Qou_OL.svg
    ```

=== "Powershell"

    ```powershell
    rapid2 eval graph `
      -Qob input/Sandbox/Qob_Sandbox_19700101_19700110_TR.nc4 `
      -Qme output/Sandbox/Qme_Sandbox_19700101_19700110_OL_tst.nc4 `
      -max 125 `
      -hyd output/Sandbox/hyd_Qou_OL.svg
    ```

=== "Python"

    ```python
    rapid2.eval.graph(
      Qob = input/Sandbox/Qob_Sandbox_19700101_19700110_TR.nc4,
      Qme = output/Sandbox/Qme_Sandbox_19700101_19700110_OL_tst.nc4,
      max = 125,
      hyd = output/Sandbox/hyd_Qou_OL.svg
    )
    ```

Check your `output/Sandbox/` folder for the newly generated `.svg` files: you
have successfully run and visualized your first RAPID2 simulation!

Now you can safely deactivate your virtual environment:

=== "Bash"

    ```bash
    deactivate
    ```
=== "Powershell"

    ```powershell
    deactivate
    ```
