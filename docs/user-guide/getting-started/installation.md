# Installation

## RAPID Code

RAPID is available on GitHub at [https://github.com/c-h-david/rapid2/](https://github.com/c-h-david/rapid2/).

The latest official release is available [here](https://github.com/c-h-david/rapid2/releases/latest).

You may also [Fork RAPID on GitHub](https://github.com/c-h-david/rapid2/fork).

The RAPID repository on GitHub includes all versions of the code that were published online since September 2010 with tracked changes.

## Quick Installation

For a quick installation, follow these steps:

### 1. Clone the Repository
```bash
git clone https://github.com/c-h-david/rapid2
cd rapid2
```

### 2. Install Using pip
```bash
pip3 install .
```

This will automatically install RAPID and all its Python dependencies.

## Manual Installation Steps

If you prefer a manual installation:

### 1. Verify Requirements
Ensure you have the required system components (see [Requirements](requirements.md)):
- `git`
- `python3.11`
- `pip3`

### 2. Download the Source Code
```bash
git clone https://github.com/c-h-david/rapid2
cd rapid2
```

### 3. Install Dependencies
```bash
pip3 install -r requirements.pip
```

### 4. Install RAPID
```bash
pip3 install .
```

## System-Level Installation (Debian/Ubuntu)

For Debian-based systems, you can install system packages first:

```bash
sudo apt-get install -y --no-install-recommends \
     $(grep -v -E '(^#|^$)' requirements.apt)
```

Then ensure Python 3.11 is the default:
```bash
sudo rm -f /usr/bin/python3
sudo ln -s /usr/bin/python3.11 /usr/bin/python3
```

## Verifying Installation

After installation, verify RAPID is properly installed:

```bash
rapid2 --help
```

You should see the help message for the RAPID command-line interface.

## Community Tools

Additional community-contributed tools:

- [Python wrapper for RAPID](https://github.com/erdc-cm/RAPIDpy)
- [ESRI ArcGIS toolbox for RAPID](https://github.com/Esri/python-toolbox-for-rapid)

## Citation Information

If you use RAPID, please consider citing it! See the [About](../../about/publications.md) section for citation details.

## Next Steps

After successful installation, learn how to run RAPID in the [Running](running.md) section.
