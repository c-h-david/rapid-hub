# Mississippi Tutorial: Real-World Application

Welcome to the real-world application tutorial! In this guide, we walk through a complete, end-to-end data pipeline using the Mississippi River Basin. You will learn how to obtain external land surface model data, couple it to the river network, and run a full RAPID2 simulation.

## 1. Dataset Preparation

To run this simulation, you need the static files for the Mississippi River Basin. This includes the river network connectivity, Muskingum parameters, and coordinate files in the Parquet format, as well as the coupling files that map the land surface model to the river network. 

## 2. Downloading Forcing Data

RAPID2 relies on runoff data from land surface models to compute streamflow. You can use the built-in `dgldas2` command-line tool to automatically fetch GLDAS-2 data directly from NASA EarthData. This tool downloads and preprocesses the necessary gridded runoff data for your specified time period and model.

## 3. Coupling the Data

Once you have the raw land surface model data, it must be mapped to the river network. The `cpllsm` tool transforms the gridded runoff data into RAPID2's reach-based external inflow format. By providing the land surface model data, connectivity, coordinates, and coupling files, this tool generates the external inflow NetCDF file required for routing.

## 4. Generating Initial States

Before running the simulation, RAPID2 requires an initial state file containing the starting discharge for every river reach. For a cold start, you can use the `zeroqinit` tool to generate an initial outflow file filled with zeros, using your newly created external inflow file as a template for the network size.

## 5. Running the Simulation

With your static Parquet files, external inflow data, and initial state file prepared, you are ready to configure your namelist. Once your YAML configuration is set up with these file paths and your routing time step, you can execute the main `rapid2` command to run the simulation and generate your final discharge outputs.
