# Architecture

## Overview of Software Architecture

RAPID is implemented as a Python package with a modular architecture that separates river network processing, parameter handling, routing computations, and I/O operations. The system follows a pipeline architecture where data flows through distinct processing stages.

## High-Level System Architecture

```mermaid
graph LR
    A[Configuration<br/>YAML Namelist] --> B[Input Processing<br/>Files & Parameters]
    B --> C[Routing Engine<br/>Matrix Computation]
    C --> D[Output Generation<br/>NetCDF Files]
    
    style A fill:#e1f5fe
    style B fill:#f3e5f5
    style C fill:#fff3e0
    style D fill:#e8f5e8
```

RAPID follows a simple 4-stage pipeline:

1. **Configuration** - Parse YAML namelist
2. **Input Processing** - Read files and build network matrices  
3. **Routing Engine** - Perform matrix-based Muskingum routing
4. **Output Generation** - Write NetCDF result files

Each stage is detailed below with its internal components and data flow.

## Detailed Sub-System Architectures

### Input Processing Pipeline

```mermaid
graph TD
    A[YAML Namelist] --> B[nml_cfg: Parse Configuration]
    
    subgraph "File Inputs"
        C[River Connectivity CSV]
        D[Basin IDs CSV] 
        E[Muskingum Parameters CSV]
        F[External Inflow NetCDF]
        G[Initial Conditions NetCDF]
    end
    
    subgraph "Data Processing"
        H[con_vec: Read Connectivity]
        I[bas_vec: Read Basin IDs]
        J[k_x_vec: Load Parameters]
        K[Qex_mdt: Process Inflow Data]
    end
    
    subgraph "Network Construction"
        L[hsh_tbl: Create Hash Tables]
        M[net_mat: Build Network Matrix]
        N[ccc_mat: Compute Coefficients]
    end
    
    B --> H
    B --> I
    B --> J
    B --> K
    
    C --> H
    D --> I
    E --> J
    F --> K
    G --> K
    
    H --> L
    I --> L
    L --> M
    J --> N
    
    style A fill:#e1f5fe
    style B fill:#f3e5f5
```

### Routing Engine Pipeline

```mermaid
graph TD
    A[Network Matrix] --> D[rte_mat: Create Routing Matrices]
    B[Coefficient Matrices] --> D
    C[External Inflow Data] --> E[stp_cor: Time Step Sync]
    
    subgraph "Validation"
        F[chk_ids: Validate IDs]
        G[chk_top: Check Topology]
    end
    
    subgraph "Routing Computation"
        H[mus_rte: Muskingum Solver]
        I[Sparse Matrix Solver]
        J[Time Step Loop]
    end
    
    D --> F
    D --> G
    E --> H
    F --> H
    G --> H
    
    H --> I
    I --> J
    J --> H
    
    style H fill:#fff3e0
    style I fill:#fff3e0
    style J fill:#fff3e0
```

### Output Generation Pipeline

```mermaid
graph TD
    A[Routing Results] --> B[Output File Creation]
    
    subgraph "File Setup"
        C[Qou_new: Create Discharge Files]
        D[Qfi_new: Create Final State Files]
    end
    
    subgraph "Data Writing"
        E[Write Time Series]
        F[Write Final States]
    end
    
    subgraph "Output Files"
        G[Discharge Time Series NetCDF]
        H[Final State NetCDF]
    end
    
    B --> C
    B --> D
    A --> E
    A --> F
    
    C --> G
    D --> H
    E --> G
    F --> H
    
    style G fill:#e8f5e8
    style H fill:#e8f5e8
```

## System Components

### Core Modules

#### Configuration Management
- **[`nml_cfg.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/nml_cfg.py)** - YAML namelist parser and validator that loads configuration files and validates required parameters

#### River Network Processing
- **[`con_vec.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/con_vec.py)** - Reads river connectivity data from CSV files
- **[`bas_vec.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/bas_vec.py)** - Loads basin/sub-basin river ID mappings  
- **[`hsh_tbl.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/hsh_tbl.py)** - Creates hash tables for efficient river ID lookups
- **[`net_mat.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/net_mat.py)** - Constructs network connectivity matrices
- **[`chk_ids.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/chk_ids.py)** - Validates river ID consistency between datasets
- **[`chk_top.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/chk_top.py)** - Verifies upstream-downstream topology

#### Parameter Management
- **[`k_x_vec.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/k_x_vec.py)** - Loads Muskingum k and x parameters for each river reach
- **[`ccc_mat.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/ccc_mat.py)** - Computes Muskingum coefficient matrices (C1, C2, C3)

#### Routing Engine
- **[`rte_mat.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/rte_mat.py)** - Constructs routing matrices for the linear system
- **[`mus_rte.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/mus_rte.py)** - Performs matrix-based Muskingum routing calculations
- **[`stp_cor.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/stp_cor.py)** - Handles time step correspondence between input data and routing

#### I/O Operations
- **[`Qex_mdt.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/Qex_mdt.py)** - Reads external inflow metadata and data
- **[`Qou_new.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/Qou_new.py)** - Creates output discharge files
- **[`Qfi_new.py`]({{ extra.rapid_repo }}/blob/main/src/rapid2/Qfi_new.py)** - Creates final state files

### Main Application
- **[`_rapid2.py`]({{ extra.rapid_repo }}/blob/main/src/_rapid2.py)** - Main entry point that orchestrates the entire routing workflow

## Data Flow Architecture

The RAPID system follows this processing pipeline:

1. **Configuration Loading**: Parse YAML namelist and validate parameters
2. **Network Setup**: Load river connectivity and create network matrices
3. **Parameter Loading**: Read Muskingum parameters and create coefficient matrices
4. **System Matrix Assembly**: Construct linear system matrices for routing
5. **Input Processing**: Load external inflow data and initial conditions
6. **Validation**: Check ID consistency and network topology
7. **Output Setup**: Create output files with proper metadata
8. **Routing Loop**: Perform time-stepping Muskingum routing
9. **Results Storage**: Save discharge time series and final states

## Mathematical Foundation

### Core Algorithm Overview

```mermaid
graph LR
    A[River Network<br/>Topology] --> B[Matrix System<br/>Setup]
    B --> C[Muskingum<br/>Routing] 
    C --> D[Updated<br/>Discharge]
    
    style B fill:#fff3e0
    style C fill:#fff3e0
```

### Mathematical Components

RAPID implements a matrix-based version of the Muskingum routing method:

- **Network Matrix (N)**: Represents upstream-downstream river connections
- **Coefficient Matrices (C1, C2, C3)**: Derived from Muskingum k and x parameters  
- **Linear System**: Sparse matrix equation solved at each routing time step
- **Time Stepping**: Iterative solution through multiple sub-timesteps for stability

### Key Equation
At each time step, RAPID solves: **N × Q_new = C1 × Q_prev + C2 × Q_ext + C3 × Q_prev**

Where:
- Q_new = Updated discharge values
- Q_prev = Previous time step discharge  
- Q_ext = External lateral inflow

## Extension Points

### Adding New Input Formats
- Extend I/O modules to support additional file formats
- Implement new metadata parsers following existing patterns

### Custom Routing Methods
- Modify `mus_rte.py` or create new routing modules
- Adjust matrix construction in `rte_mat.py` for new methods

### Enhanced Validation
- Add new validation modules following `chk_*.py` patterns
- Integrate additional consistency checks into the main workflow

## Design Patterns

### Functional Programming
- Each module provides pure functions with clear inputs/outputs
- Minimal side effects and state management
- Extensive use of NumPy arrays and SciPy sparse matrices

### Pipeline Architecture
- Clear separation of concerns between processing stages
- Data transformation through sequential module calls
- Error handling at module boundaries

### Configuration-Driven
- All file paths and parameters specified in YAML configuration
- No hardcoded paths or parameters in the codebase
- Flexible input/output file specification

## Technology Stack

### Core Dependencies
- **NumPy**: Array operations and numerical computations
- **SciPy**: Sparse matrix operations and linear algebra
- **NetCDF4**: Scientific data file I/O
- **PyYAML**: Configuration file parsing

### Development Tools
- **Type Hints**: Full typing support for maintainability
- **Docstrings**: Comprehensive function documentation with examples
- **Testing**: Unit tests using doctest examples

---

For detailed API documentation and implementation details, please refer to the [source code](https://github.com/c-h-david/rapid2) and the individual module docstrings.
