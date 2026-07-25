# Bias Correction: The RAPID2 Sandbox

This tutorial demonstrates how to correct biases in external inflows using the Long-Term Inverse Routing (LTIR) methodology in RAPID2. 

> **Prerequisites:** We assume you have already completed the Quick Start tutorial (`quick-start.md`). Your Python virtual environment should be activated, and the Sandbox data must already be downloaded into the `input/Sandbox/` and `output/Sandbox/` directories.

## 1. Calculate the Scaling Factors

First, we need to compute the scaling factors using our "First Guess" (FG) flawed external inflow and our "True" (TR) observations. We'll use the `ltir_scl` tool to generate a scaling parameter file (`scl_Sandbox.parquet`):

```bash
ltir_scl \
  -con input/Sandbox/con_Sandbox.parquet \
  -bas input/Sandbox/bas_Sandbox_ascend.parquet \
  -Qex input/Sandbox/Qex_Sandbox_19700101_19700110_FG.nc4 \
  -Qob input/Sandbox/Qob_Sandbox_19700101_19700110_TR.nc4 \
  -scl output/Sandbox/scl_Sandbox.parquet
```

## 2. Apply Bias Correction to External Inflow

Now, we apply the computed scalars to the flawed First Guess inflow (`Qex_..._FG.nc4`) to create a new, Bias Corrected external inflow file (`Qex_..._BC.nc4`) using the `ltir_cor` tool:

```bash
ltir_cor \
  -prv input/Sandbox/Qex_Sandbox_19700101_19700110_FG.nc4 \
  -scl output/Sandbox/scl_Sandbox.parquet \
  -now output/Sandbox/Qex_Sandbox_19700101_19700110_BC.nc4
```

## 3. Run the Bias-Corrected Simulation

With our corrected inflow ready, we can run the model using the Bias Correction (BC) namelist:

```bash
rapid2 --namelist input/Sandbox/nml_Sandbox_BC.yml
```

> **Note:** This namelist is pre-configured to use the newly generated `Qex_..._BC.nc4` inflow file. It will output `Qou_Sandbox_19700101_19700110_BC_tst.nc4` and `Qfi_Sandbox_19700101_19700110_BC_tst.nc4` in your `output/Sandbox/` folder.

## 4. Subsample the Output

To compare our corrected model output with the true observations, we need to spatially and temporally subsample the high-resolution output (`Qou`) to match the daily cadence of our gauges, creating our Model Equivalent (`Qme`):

```bash
subsampleqout \
  -Qou output/Sandbox/Qou_Sandbox_19700101_19700110_BC_tst.nc4 \
  -obs input/Sandbox/obs_Sandbox.parquet \
  -dtO 86400 \
  -Qme output/Sandbox/Qme_Sandbox_19700101_19700110_BC_tst.nc4
```

## 5. Visualize the Improvement

Finally, let's plot the hydrographs to see how well the bias correction worked! We'll compare the new Model Equivalent (`Qme`) to the True observations (`Qob`):

```bash
hydrographs \
  -Qob input/Sandbox/Qob_Sandbox_19700101_19700110_TR.nc4 \
  -Qme output/Sandbox/Qme_Sandbox_19700101_19700110_BC_tst.nc4 \
  -max 100 \
  -hyd output/Sandbox/hyd_BC.svg
```

Check your `output/Sandbox/` folder for the newly generated `.svg` files (e.g., `hyd_BC_30.svg` and `hyd_BC_50.svg`). You will see that the red dashed line (model equivalent) now aligns beautifully with the black solid line (observations)!