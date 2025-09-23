# Requirements

## System Requirements

RAPID requires the following basic system components:

- `git` - For source code management
- `python3.11` - Python interpreter (version 3.11 specifically)
- `pip3` - Python package installer

## Python Dependencies

RAPID depends on the following Python packages:

### Core Computational Libraries
- `numpy==2.1.1` - Numerical computing with arrays
- `scipy==1.14.1` - Scientific computing and sparse matrix operations
- `netCDF4==1.7.1.post2` - Reading and writing NetCDF files

### Data Access
- `earthaccess==0.14.0` - NASA Earth science data access

### Development Tools (Optional)
- `flake8==7.1.1` - Code linting
- `mypy==1.11.2` - Static type checking
- `yamllint==1.35.1` - YAML file validation

## Operating System Support

RAPID supports various computing platforms, including:
- Personal computers running Linux, macOS, or Windows
- High-performance computing clusters
- Cloud computing environments

### Recommended: Debian-based Systems

For building from scratch, Debian-based distributions are recommended. The repository includes `requirements.apt` with all necessary system packages that can be installed using:

```bash
sudo apt-get install -y --no-install-recommends \
     $(grep -v -E '(^#|^$)' requirements.apt)
```

## Additional Documentation

For the most up-to-date requirements and detailed installation guidance, please refer to the [RAPID repository](https://github.com/c-h-david/rapid2/) which contains additional documentation including:

- Complete dependency lists in `requirements.pip` and `requirements.apt`
- Platform-specific installation instructions
- Build and testing documentation

## Next Steps

Once you've verified your system meets the requirements, proceed to [Installation](installation.md).
