# Code

## Source Code Organization

RAPID is organized as a Python package with the following structure:

```
rapid2/
├── src/
│   ├── _rapid2.py          # Main entry point
│   ├── _dgldas2.py         # GLDAS data processing utility
│   ├── _m3rivtoqext.py     # Model 3 river to external flow converter
│   ├── _zeroqinit.py       # Zero initial flow generator
│   └── rapid2/             # Core package modules
│       ├── nml_cfg.py      # Configuration management
│       ├── con_vec.py      # Connectivity processing
│       ├── bas_vec.py      # Basin vector processing
│       ├── hsh_tbl.py      # Hash table operations
│       ├── net_mat.py      # Network matrix construction
│       ├── k_x_vec.py      # Parameter vector processing
│       ├── ccc_mat.py      # Coefficient matrix computation
│       ├── rte_mat.py      # Routing matrix construction
│       ├── mus_rte.py      # Muskingum routing engine
│       └── [other modules...]
```

## Variable Naming Conventions

RAPID follows specific naming conventions inherited from its Fortran origins:

### Prefixes
- **`IV_`** - Integer Vector (1D numpy array of int32)
- **`ZV_`** - Real Vector (1D numpy array of float64)
- **`IM_`** - Integer Matrix (2D numpy array of int32)
- **`ZM_`** - Real Matrix (scipy sparse matrix)
- **`IS_`** - Integer Scalar (int32)
- **`JS_`** - Loop counter (int)

### Suffixes
- **`_tot`** - Total domain (all river reaches)
- **`_bas`** - Basin subset
- **`_ncf`** - NetCDF file
- **`_csv`** - CSV file
- **`_yml`** - YAML file

## Development Guidelines

### Code Quality Standards
- **Type Hints**: Functions include complete type annotations
- **Docstrings**: Functions have docstrings with examples
- **Error Handling**: Consistent error handling with meaningful messages

### Testing and Quality Assurance
- **Doctests**: Python docstring examples tested using `python3 -m doctest`
- **Type Checking**: Static and dynamic typing checked with `mypy --strict`
- **Code Linting**: Python code follows flake8 style guidelines
- **Documentation Linting**: Markdown files linted with pymarkdown
- **Configuration Linting**: YAML files validated with yamllint

---

For detailed implementation details and testing procedures, see the source files and [TESTING.md](https://github.com/c-h-david/rapid2/blob/main/TESTING.md) in the [RAPID repository](https://github.com/c-h-david/rapid2).
