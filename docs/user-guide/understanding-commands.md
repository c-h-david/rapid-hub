# Understanding RAPID2 Functions

RAPID2 provides a unified set of Python functions and a corresponding
Command Line Interface (CLI) dispatcher to streamline routing workflows.

> This note was written in anticipation of a future release.

## Command Reference

The table below maps each terminal command to its corresponding Python
function and describes its primary purpose. Tools marked as *Planned*
are under development and are not yet available.

For conciseness, we use`import rapid2 as r2` here.

| Terminal Command       | Python Import      | Purpose                       |
| :--------------------- | :----------------- | :-----------------------------|
| `rapid2 core run`      | `r2.core.run`      | Run the core model.           |
| `rapid2 core ltir`     | `r2.core.ltir`     | Apply inflow bias correction. |
| `rapid2 pull sandbox`  | `r2.pull.sandbox`  | Download Sandbox data.        |
| `rapid2 pull tutorial` | *Planned*          | Download Mississippi tutorial.|
| `rapid2 pull gldas2`   | `r2.pull.gldas2`   | Download raw GLDAS2 data.     |
| `rapid2 prep gldas2`   | `r2.prep.gldas2`   | Reformat GLDAS2 time & units. |
| `rapid2 prep couple`   | `r2.prep.couple`   | Map LSM grids to networks.    |
| `rapid2 prep sandbox`  | `r2.prep.sandbox`  | Generate synthetic inflow.    |
| `rapid2 prep coldinit` | `r2.prep.coldinit` | Generate cold-start states.   |
| `rapid2 prep warminit` | *Planned*          | Generate warm-start states.   |
| `rapid2 prep sample`   | `r2.prep.sample`   | Align NetCDF data to gauges.  |
| `rapid2 prep ltir`     | `r2.prep.ltir`     | Compute LTIR scalars.         |
| `rapid2 eval graph`    | `r2.eval.graph`    | Generate SVG hydrographs.     |
| `rapid2 eval cmpncf`   | `r2.eval.cmpncf`   | Compare NetCDF files.         |
| `rapid2 eval cmppqt`   | *Planned*          | Compare Parquet files.        |
| `rapid2 v1v2 static`   | `r2.v1v2.static`   | Upgrade RAPID1 CSV to Parquet.|
| `rapid2 v1v2 inflow`   | `r2.v1v2.inflow`   | Convert volumes to flow rates.|
| `rapid2 v1v2 namelist` | *Planned*          | Fortran namelist to YAML.     |

This maps to the directory structure in the code repository:

```text
src/rapid2/
├── base/        # Low-level mathematical and I/O building blocks
├── pull/        # Data download
├── prep/        # Data preparation
├── core/        # Routing, bias correction, and data assimilation
├── eval/        # Analysis and plotting
├── v1v2/        # Migration from legacy RAPID1 to RAPID2
└── cmds/        # Command Line Interface (CLI)
```

## Core Workflow Concepts

RAPID2 organizes functionality into command categories available through
both the Python API and the CLI:

- `pull` commands acquire source or synthetic datasets.
- `prep` commands transform, align, and configure input data for routing,
  including grid coupling and initial-state generation.
- `core` commands perform routing and bias correction.
- `eval` commands support comparison, visualization, and other analysis
  of model outputs.
- `v1v2` commands migrate legacy RAPID1 datasets and configuration files
  to RAPID2 formats.

In Python, these capabilities are exposed through the `rapid2` namespace,
such as `r2.prep.couple()`. The CLI provides corresponding dispatcher
commands, such as `rapid2 prep couple`. The CLI is intended to be a thin
interface to the same underlying Python functionality.
