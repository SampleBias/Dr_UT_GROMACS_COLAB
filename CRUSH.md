# CRUSH.md - Dr_UT_GROMACS_COLAB

## Repository Overview
This repository contains Jupyter notebooks for running GROMACS molecular dynamics simulations on Google Colab. The codebase consists of Python notebooks that handle GROMACS installation, system equilibration, production simulations, and trajectory analysis.

## Build/Install Commands
- **Notebook Execution**: Open `.ipynb` files in Google Colab using the provided badges in README.md
- **GROMACS Installation**: Run `Build_to_Google_Drive.ipynb` to install GROMACS to Google Drive
- **Dependencies**: Installed automatically within notebooks (numpy, pandas, matplotlib, seaborn, biopython)

## Test Commands
- **Manual Testing**: Run notebooks cell-by-cell in Colab environment
- **Integration Testing**: Follow the full workflow from CHARMM-GUI preparation through production simulation
- **File Verification**: Check that output files (`.gro`, `.xtc`, `.edr`) are generated correctly

## Code Style Guidelines

### Import Organization
```python
# Standard library
import os
import re
import shutil
from functools import partial

# Third-party libraries
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from Bio.PDB import Superimposer, PDBParser

# Google Colab specific
from google.colab import drive, runtime
```

### Naming Conventions
- **Variables**: snake_case (e.g., `gromacs_vers`, `cache_gromacs`)
- **Functions**: snake_case (e.g., `superpose.py` script functions)
- **Files**: descriptive names with underscores for spaces
- **Constants**: UPPER_SNAKE_CASE (e.g., `STORAGE`, `gromacs_vers`)

### Error Handling
- Use explicit error checking for shell commands
- Check file existence before operations (`[[ -s "file" ]]`)
- Provide meaningful error messages with redirection to stderr
- Graceful fallbacks (prebuilt vs source compilation)

### Code Structure
- Notebook cells organized by functionality
- Use `%%bash` for shell operations with parameter markdown annotations
- Separate Python scripts for complex operations (e.g., `superpose.py`)
- Use `%%writefile` to create utility scripts within notebooks

### Dependencies
- **Core**: Python 3, GROMACS 2023.2
- **Scientific**: NumPy, Pandas, Matplotlib, Seaborn, BioPython
- **Platform**: Google Colab, Google Drive integration
- **File Formats**: PDB, GRO, XTC, EDR, TOP, MDP

### Best Practices
- Use absolute paths for file operations
- Cache large downloads to Google Drive
- Implement checkpointing for long-running simulations
- Provide clear documentation with markdown cells
- Use Colab form fields for user parameters
- Handle GPU optimization when available

## Environment Notes
- Designed specifically for Google Colab environment
- Leverages Google Drive for persistent storage
- GPU acceleration support via CUDA
- Automatic dependency management within notebooks