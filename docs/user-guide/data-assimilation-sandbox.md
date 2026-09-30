# Data Assimilation: The RAPID2 Sandbox

This tutorial demonstrates how to dynamically correct river discharge
using the Kalman Filter Data Assimilation methodology in RAPID2.

> **Prerequisites:** We assume you have already completed the Quick Start
> tutorial (`quick-start-sandbox.md`). Your Python virtual environment
> should be activated, and the Sandbox data must already be downloaded
> into the `input/Sandbox/` and `output/Sandbox/` directories.
>
> This tutorial was written for `rapid2 2.0.0b4`.

## 1. Run the Data Assimilation Simulation

Unlike Bias Correction, which preprocesses external inflows before routing,
Data Assimilation integrates gauge observations dynamically while the routing
model runs. To execute it, we simply point RAPID2 to the DA namelist:

```bash
rapid2 --namelist input/Sandbox/nml_Sandbox_DA.yml

```

> **Note:** This namelist is pre-configured to use the "First Guess" (FG)
> flawed external inflows while simultaneously reading the "Truth" (TR)
> daily observations. It will output new `Qou_..._DA_tst.nc4` and
> `Qfi_..._DA_tst.nc4` files in your `output/Sandbox/` folder.

## 2. Subsample the Output

To accurately compare our assimilated model output with the daily gauge
observations, we must spatially and temporally subsample the high-resolution
output (`Qou`) to create our Model Equivalent (`Qme`):

```bash
subsampleqout \
  -Qou output/Sandbox/Qou_Sandbox_19700101_19700110_DA_tst.nc4 \
  -obs input/Sandbox/obs_Sandbox.parquet \
  -dtO 86400 \
  -Qme output/Sandbox/Qme_Sandbox_19700101_19700110_DA_tst.nc4

```

## 3. Visualize the Daily Scale (Model Equivalents)

Let's plot the daily hydrographs to see how the assimilation performed
against the daily observations:

```bash
hydrographs \
  -Qob input/Sandbox/Qob_Sandbox_19700101_19700110_TR.nc4 \
  -Qme output/Sandbox/Qme_Sandbox_19700101_19700110_DA_tst.nc4 \
  -max 125 \
  -hyd output/Sandbox/hyd_Qme_DA.svg

```

Open the generated SVG files. You will see that the red dashed line (model
equivalent) aligns almost perfectly with the black solid line (daily
observations). The Kalman filter successfully corrected the flawed inflow!

## 4. Visualize the 3-Hourly Scale (Full Outflow)

Data Assimilation applies corrections over an averaging window. While the
daily averages (`Qme`) match beautifully, the 3-hourly routing dynamics
will differ slightly from an idealized "True" run. We can visualize this
by comparing the high-resolution `Qou` files directly:

```bash
hydrographs \
  -Qob input/Sandbox/Qou_Sandbox_19700101_19700110_TR.nc4 \
  -Qme output/Sandbox/Qou_Sandbox_19700101_19700110_DA_tst.nc4 \
  -max 100 \
  -hyd output/Sandbox/hyd_Qou_DA.svg

```

Looking at these high-resolution SVGs, you will see how the continuous
Muskingum routing physics process the intermittent assimilation corrections.
It works perfectly at the daily observation scale, but preserves realistic
sub-daily variability!
