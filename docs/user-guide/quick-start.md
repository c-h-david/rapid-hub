# Quick Start

An accelerated guide to quickly understand, download, install, use, and apply RAPID to a sample use case.

## Quick Install

Get RAPID running in 3 simple steps.

```bash
git clone https://github.com/c-h-david/rapid2
cd rapid2/
pip3 install .
```

## Quick Use Case

Download the sandbox for RAPID.

```bash
cd tst/
./tst_dwnl_Sandbox.sh
cd ..
```

> Or get it manually at
>
> [![10.5281/zenodo.17298742](https://zenodo.org/badge/doi/10.5281/zenodo.17298742.svg)](https://doi.org/10.5281/zenodo.17298742)


## Quick Run

Run RAPID.

```bash
rapid2 -nl input/Sandbox/namelist_Sandbox.yml
```

That's it!

## Viewing Results

After a successful run, your output files will be written to the paths specified in the namelist configuration. For the Sandbox example, check:

- `./output/Sandbox/Qout_Sandbox_19700101_19700110.nc4` - discharge time series
- `./output/Sandbox/Qfinal_Sandbox_19700101_19700110.nc4` - final state for model restart

## Need More?

For step-by-step setup details, see the [User Guide](getting-started/installation.md).

You can also find additional information and join the conversation in our [GitHub Discussions](https://github.com/c-h-david/rapid2/discussions).
We warmly welcome new users and greatly value your feedback!

For short-term exchanges (messages deleted after 6 months), you can connect through the unofficial user community Slack channel.
