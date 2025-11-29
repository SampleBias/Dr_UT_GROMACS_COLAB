# CRUSH.md - Dr_UT_GROMACS_COLAB

## Repository Overview
This repository contains Jupyter notebooks for running GROMACS molecular dynamics simulations on Google Colab. The codebase consists of Python notebooks that handle GROMACS installation, system equilibration, production simulations, and trajectory analysis with advanced GPU optimization.

## Build/Install Commands
- **Notebook Execution**: Open `.ipynb` files in Google Colab using provided badges in README.md
- **GROMACS Installation**: Run `Build_to_Google_Drive.ipynb` on CPU-only runtime to install GROMACS to Google Drive
- **Dependencies**: Installed automatically within notebooks (numpy, pandas, matplotlib, seaborn, biopython)
- **GPU Detection**: Automatic hardware detection and optimization in simulation notebooks

## Test Commands
- **Manual Testing**: Run notebooks cell-by-cell in Colab environment
- **GPU Performance Test**: Compare CPU vs GPU performance using 1ns test simulations
- **Integration Testing**: Follow full workflow from CHARMM-GUI preparation through production simulation
- **File Verification**: Check that output files (`.gro`, `.xtc`, `.edr`) are generated correctly
- **Memory Validation**: Monitor GPU memory usage during large system simulations

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
- **Variables**: snake_case (e.g., `gromacs_vers`, `cache_gromacs`, `GPU_OPTIMIZED_ARGS`)
- **Functions**: snake_case (e.g., `superpose.py` script functions)
- **Files**: descriptive names with underscores for spaces
- **Constants**: UPPER_SNAKE_CASE (e.g., `STORAGE`, `gromacs_vers`, `GPU_DETECTED`)
- **Environment Variables**: Upper case with descriptive names

### Error Handling
- Use explicit error checking for shell commands with `|| exit $?`
- Check file existence before operations (`[[ -s "file" ]]`)
- Provide meaningful error messages with redirection to stderr
- Graceful fallbacks (prebuilt vs source compilation)
- GPU detection with CPU fallback for compatibility
- Comprehensive checkpointing for long simulations

### GPU Optimization Standards
- **Intelligent Detection**: Automatic GPU memory assessment and configuration
- **Memory-Based Args**: 
  - >15GB VRAM: `"-bonded gpu -update gpu -ntmpi 1"`
  - 8-15GB VRAM: `"-bonded gpu -ntmpi 1"`
  - <8GB VRAM: `"-bonded gpu"`
- **Performance Flags**: Always use `GMX_CUDA_GRAPH=1` for enhancement
- **Fallback Strategy**: CPU optimization when GPU unavailable
- **Monitoring**: Real-time GPU utilization and performance metrics

### Code Structure
- Notebook cells organized by functionality with clear markdown sections
- Use `%%bash` for shell operations with parameter markdown annotations
- Separate Python scripts for complex operations (e.g., `superpose.py`)
- Use `%%writefile` to create utility scripts within notebooks
- Futuristic fonts and enhanced UI design for better user experience
- Comprehensive documentation with twice-detailed explanations

### Dependencies
- **Core**: Python 3, GROMACS 2023.2 with CUDA support
- **Scientific**: NumPy, Pandas, Matplotlib, Seaborn, BioPython
- **Platform**: Google Colab, Google Drive integration
- **File Formats**: PDB, GRO, XTC, EDR, TOP, MDP
- **GPU Libraries**: CUDA 11.x+, cuBLAS, cuFFT

### Best Practices
- Use absolute paths for file operations
- Cache large downloads to Google Drive
- Implement checkpointing for long-running simulations with automatic backup
- Provide clear documentation with markdown cells and visual indicators
- Use Colab form fields for user parameters with validation
- Handle GPU optimization intelligently based on available hardware
- Monitor performance and provide real-time feedback
- Use futuristic fonts (Orbitron, Rajdhani) for enhanced UI
- Implement comprehensive error handling and recovery procedures

### Performance Optimization
- **GPU Runtime Selection**: T4/V100/A100 based on availability and system requirements
- **Memory Management**: Dynamic allocation based on GPU memory detection
- **Thread Optimization**: Proper MPI thread configuration for GPU efficiency
- **Checkpoint Frequency**: 10ps intervals with Google Drive synchronization
- **Trajectory Compression**: XTC format for space-efficient storage

### Environment Notes
- Designed specifically for Google Colab environment with GPU support
- Leverages Google Drive for persistent storage and backup
- GPU acceleration support via CUDA with intelligent fallback
- Automatic dependency management within notebooks
- Hardware detection and optimization for both CPU and GPU execution
- Enhanced UI with futuristic design elements for better user experience