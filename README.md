# GROMACS-on-Colab

This repository provides a suite of Jupyter notebooks for running molecular dynamics simulations on Google Colab using GROMACS. All simulations run on Google Colab's free GPU resources, and all inputs and outputs are stored on your Google Drive for persistence.

## Overview

This project consists of four main notebooks that work together to provide a complete molecular dynamics simulation workflow:

1. **`Build_to_Google_Drive.ipynb`** - Installs GROMACS and dependencies to your Google Drive
2. **`GROMACS_for_CHARMM-GUI.ipynb`** - Processes CHARMM-GUI system archives and equilibrates systems
3. **`GROMACS_for_production.ipynb`** - Runs production molecular dynamics simulations
4. **`Trajectory_analysis_tools.ipynb`** - Analyzes simulation trajectories

All inputs and outputs are stored on your Google Drive, allowing you to work across multiple Colab sessions.

---

## Prerequisites

Before starting, ensure you have:

1. **A Google account** with access to:
   - [Google Colab](https://colab.research.google.com/)
   - [Google Drive](https://drive.google.com/) with sufficient storage space (at least 5-10 GB recommended)

2. **CHARMM-GUI prepared systems** (if using CHARMM-GUI workflow):
   - System archives (`.tgz` files) from CHARMM-GUI Solution Builder or Membrane Builder
   - **Important**: You must enable "GROMACS compatible outputs" in CHARMM-GUI

3. **Basic understanding** of molecular dynamics simulations and GROMACS (helpful but not required)

---

## Installation Instructions

### Step 1: Install GROMACS to Google Drive

**This step must be completed first before using any other notebooks.**

1. **Open the installation notebook:**
   - Open [`Build_to_Google_Drive.ipynb`](Build_to_Google_Drive.ipynb) in Google Colab
   - Or use the "Open in Colab" badge if available

2. **Connect to Google Drive:**
   - The notebook will prompt you to mount your Google Drive
   - Click "Connect to Google Drive" and authorize access
   - Your Drive will be mounted at `/content/drive/MyDrive`

3. **Select runtime type:**
   - **Important**: Go to `Runtime → Change runtime type`
   - Select **"CPU only / Standard RAM"** (not GPU)
   - This ensures compatibility with all runtime types

4. **Run the installation:**
   - Click `Runtime → Run all` in the toolbar
   - Or run each cell sequentially by clicking the play button
   - The installation will:
     - Download and install GROMACS (version 2023.2)
     - Set up Miniconda environments for Python tools
     - Download CHARMM36 forcefield
     - Install `cgenff_charmm2gmx.py` utility
     - Cache everything to `{GoogleDrive}/gromacs-on-colab/`

5. **Wait for completion:**
   - Installation typically takes 10-30 minutes depending on whether prebuilt binaries are available
   - If prebuilt binaries aren't available, GROMACS will be compiled from source (takes longer)
   - All installations are cached to your Google Drive for future use

6. **Disconnect (optional):**
   - The notebook will automatically disconnect the runtime when finished
   - You can also manually disconnect via `Runtime → Disconnect and delete runtime`

**What gets installed:**
- GROMACS 2023.2 (cached to `{GoogleDrive}/gromacs-on-colab/gromacs-2023.2.tar.gz`)
- Miniconda3 with Python environments (cached to `{GoogleDrive}/gromacs-on-colab/`)
- CHARMM36 forcefield (cached to `{GoogleDrive}/gromacs-on-colab/charmm36-jul2022.tar.gz`)
- `cgenff_charmm2gmx.py` utility for ligand parameterization

---

## Detailed Workflow Instructions

### Workflow 1: Protein-Ligand Complex Simulation

This is the most common workflow for simulating a protein-ligand complex.

#### Step 1: Prepare Your Structures

1. **Obtain or prepare your protein structure:**
   - Download from [Protein Data Bank (PDB)](https://www.rcsb.org/) or [AlphaFold Database](https://alphafold.ebi.ac.uk/)
   - Ensure the structure is complete (no major gaps)
   - Save as `protein.pdb`

2. **Dock your ligand:**
   - Use [AutoDock Vina](https://vina.scripps.edu/) or similar docking software
   - Convert formats if needed using [Open Babel](https://github.com/openbabel/openbabel)
   - Save the docked complex as `docked_ligand.sdf` or `docked_ligand.pdb`

#### Step 2: Process in CHARMM-GUI

1. **Process the protein in CHARMM-GUI Solution Builder:**
   - Go to [CHARMM-GUI Solution Builder](https://www.charmm-gui.org/?doc=input/solution)
   - Upload your `protein.pdb` (the same structure used for docking)
   - Follow the wizard, accepting default settings
   - **Critical**: At the final step, **enable "GROMACS compatible outputs"**
   - Download the archive: `protein_CHARMM.tgz`

2. **Process the ligand in CHARMM-GUI Ligand Reader:**
   - Go to [CHARMM-GUI Ligand Reader](https://www.charmm-gui.org/?doc=input/ligandrm)
   - Upload your `docked_ligand.sdf` (the docking output)
   - Keep the default residue name: `LIG` (for custom ligands)
   - Download the archive: `docked_ligand_CHARMM.tgz`

3. **Upload to Google Drive:**
   - Upload both `.tgz` files to your Google Drive
   - Note the full paths, e.g.:
     - `{GoogleDrive}/CHARMM-GUI/protein_CHARMM.tgz`
     - `{GoogleDrive}/CHARMM-GUI/docked_ligand_CHARMM.tgz`

#### Step 3: Equilibrate the System

1. **Open the equilibration notebook:**
   - Open [`GROMACS_for_CHARMM-GUI.ipynb`](GROMACS_for_CHARMM-GUI.ipynb) in Google Colab

2. **Select GPU runtime:**
   - Go to `Runtime → Change runtime type`
   - Select **"GPU"** (T4, V100, or A100)
   - This accelerates the equilibration simulations

3. **Configure the notebook:**
   - In the "Configuration" section, fill in:
     - **`protein_archive`**: Path to your protein `.tgz` file
       - Example: `{GoogleDrive}/CHARMM-GUI/protein_CHARMM.tgz`
     - **`output_folder`**: Where to save the equilibrated system
       - Example: `{GoogleDrive}/GROMACS/my_protein_ligand_project`
     - **`ligand_archives`**: Path to your ligand `.tgz` file
       - Example: `{GoogleDrive}/CHARMM-GUI/docked_ligand_CHARMM.tgz`
     - **Advanced options** (optional):
       - `double_equilibration_length`: Extend equilibration time (default: False)
       - `remove_cmap_terms`: Remove CMAP terms for faster GPU performance but lower accuracy (default: False)

4. **Run the notebook:**
   - Click `Runtime → Run all`
   - The notebook will:
     - Extract and merge the protein and ligand systems
     - Generate GROMACS-compatible topologies
     - Run minimization and equilibration steps
     - Save the equilibrated system to your output folder

5. **Wait for completion:**
   - Equilibration typically takes 30-60 minutes
   - The output folder will contain:
     - `conf.gro` - Equilibrated coordinates
     - `topol.top` - System topology
     - `toppar/` - Forcefield parameters
     - `index.ndx` - Group definitions
     - `grompp.mdp` - Production simulation parameters
     - `restraint.gro` - Reference structure for restraints

#### Step 4: Run Production Simulation

1. **Open the production notebook:**
   - Open [`GROMACS_for_production.ipynb`](GROMACS_for_production.ipynb) in Google Colab

2. **Select GPU runtime:**
   - Go to `Runtime → Change runtime type`
   - Select **"GPU"** for best performance

3. **Configure the notebook:**
   - In the "Configuration" section, fill in:
     - **`project_folder`**: Path to your equilibrated system folder
       - Example: `{GoogleDrive}/GROMACS/my_protein_ligand_project`
     - **`simulation_duration_ns`**: How long to simulate (in nanoseconds)
       - Example: `10` for a 10 ns simulation
     - **`output_prefix`**: Unique name for this simulation
       - Example: `sim` or `production_run_1`
     - **Advanced options** (optional):
       - `rmsd_group`: Group to monitor for early stopping (e.g., `LIG`)
       - `rmsd_early_stop_threshold_A`: RMSD threshold in Angstroms (default: 12.0)

4. **Run the notebook:**
   - Click `Runtime → Run all`
   - The simulation will run in blocks (typically 1 ns each)
   - Each block is saved as a partial output file
   - The simulation automatically continues until the target duration is reached

5. **Monitor progress:**
   - Check the output for performance metrics
   - Partial trajectory files are saved after each block
   - The final trajectory is saved as `{output_prefix}_reference.xtc`

6. **Resume if interrupted:**
   - If the simulation is interrupted, simply run the notebook again
   - It will automatically detect and resume from the last checkpoint

#### Step 5: Analyze Trajectory (Optional)

1. **Open the analysis notebook:**
   - Open [`Trajectory_analysis_tools.ipynb`](Trajectory_analysis_tools.ipynb) in Google Colab

2. **Configure the notebook:**
   - **`project_folder`**: Path to your project folder with trajectory files
   - **`output_prefix`**: The prefix used in your production simulation

3. **Run analyses:**
   - The notebook provides various analysis tools:
     - Calculate centroid structures for specific time spans
     - Compute RMSD over time
     - Measure interatomic distances
     - Calculate interaction energies
   - Run the cells for the analyses you need

---

### Workflow 2: Protein-Only Simulation

For simulations without ligands, follow a simplified workflow:

1. **Prepare protein in CHARMM-GUI Solution Builder:**
   - Upload your protein structure
   - Enable GROMACS outputs
   - Download `protein_CHARMM.tgz`

2. **Equilibrate using `GROMACS_for_CHARMM-GUI.ipynb`:**
   - Set `protein_archive` to your `.tgz` file
   - Leave `ligand_archives` empty
   - Run the notebook

3. **Run production simulation:**
   - Use `GROMACS_for_production.ipynb` as described above

---

### Workflow 3: Membrane Protein Simulation

For membrane proteins:

1. **Use CHARMM-GUI Membrane Builder** instead of Solution Builder
2. **Follow the same workflow** as protein-ligand complexes
3. The notebook automatically detects membrane systems and adjusts parameters

---

## Notebook Details

### Build_to_Google_Drive.ipynb

**Purpose:** One-time installation of GROMACS and dependencies

**Runtime:** CPU only (Standard RAM)

**What it does:**
- Downloads/compiles GROMACS 2023.2
- Sets up Miniconda environments
- Downloads CHARMM36 forcefield
- Caches everything to Google Drive

**When to run:** Once, before using any other notebooks

**Estimated time:** 10-30 minutes

---

### GROMACS_for_CHARMM-GUI.ipynb

**Purpose:** Process CHARMM-GUI archives and equilibrate systems

**Runtime:** GPU recommended

**Inputs:**
- Protein archive from CHARMM-GUI Solution/Membrane Builder
- (Optional) Ligand archive(s) from CHARMM-GUI Ligand Reader

**Outputs:**
- Equilibrated system ready for production simulation
- All files saved to specified output folder

**Key features:**
- Merges protein and ligand systems
- Generates GROMACS topologies
- Runs minimization and equilibration
- Handles multiple ligands
- Supports membrane systems

**Estimated time:** 30-60 minutes

---

### GROMACS_for_production.ipynb

**Purpose:** Run production molecular dynamics simulations

**Runtime:** GPU required for performance

**Inputs:**
- Project folder from equilibration step
- Simulation duration (nanoseconds)
- Output prefix

**Outputs:**
- Production trajectory (`{prefix}_reference.xtc`)
- Checkpoint files for resuming
- Summary file with performance metrics

**Key features:**
- Automatic checkpointing and resuming
- Early stopping based on RMSD (optional)
- Progressive trajectory processing
- Saves partial outputs after each block

**Estimated time:** Depends on simulation length (typically 1-2 hours per nanosecond)

---

### Trajectory_analysis_tools.ipynb

**Purpose:** Analyze simulation trajectories

**Runtime:** CPU or GPU

**Inputs:**
- Project folder with trajectory files
- Output prefix from production simulation

**Available analyses:**
- Centroid structure calculation
- RMSD calculations
- Distance measurements
- Interaction energy analysis

---

## Important Notes and Tips

### Google Drive Path Format

When specifying paths in notebooks, use the format:
```
{GoogleDrive}/path/to/file
```

This will be automatically converted to `/content/drive/MyDrive/path/to/file` in Colab.

### Runtime Selection

- **CPU only**: Use for installation (`Build_to_Google_Drive.ipynb`)
- **GPU**: Use for equilibration and production simulations
- **GPU types**: T4 (free tier), V100, or A100 (Colab Pro)

### Storage Considerations

- Each simulation can generate several GB of data
- Trajectory files (`.xtc`) are compressed but can still be large
- Monitor your Google Drive storage space
- Consider downloading large files locally if needed

### Resuming Simulations

- Production simulations automatically save checkpoints
- If interrupted, simply re-run the production notebook
- It will detect and resume from the last checkpoint
- Partial trajectory files are preserved

### Performance Optimization

- Use GPU runtime for simulations (much faster than CPU)
- For very long simulations, consider breaking into multiple runs
- The `remove_cmap_terms` option can improve GPU performance but reduces accuracy
- Monitor the performance summary in production notebook output

### Troubleshooting

**Installation fails:**
- Ensure you're using CPU runtime for installation
- Check Google Drive has sufficient space
- Try running cells individually to identify the issue

**Equilibration fails:**
- Verify CHARMM-GUI archives are complete
- Check that GROMACS outputs were enabled in CHARMM-GUI
- Ensure protein and ligand coordinates are compatible

**Production simulation slow:**
- Ensure GPU runtime is selected
- Check GPU is being utilized (see performance summary)
- Consider using `remove_cmap_terms` for faster performance

**Trajectory analysis errors:**
- Verify trajectory files exist in project folder
- Check output prefix matches production simulation
- Ensure sufficient runtime time for analysis

---

## Example Commands and Paths

### Typical Google Drive Structure

```
MyDrive/
├── gromacs-on-colab/          (created by Build_to_Google_Drive.ipynb)
│   ├── gromacs-2023.2.tar.gz
│   ├── Miniconda3-*.tar.gz
│   └── charmm36-*.tar.gz
├── CHARMM-GUI/
│   ├── protein_CHARMM.tgz
│   └── ligand_CHARMM.tgz
└── GROMACS/
    └── my_project/
        ├── conf.gro
        ├── topol.top
        ├── toppar/
        ├── grompp.mdp
        ├── sim_reference.xtc
        └── sim_reference.summary
```

### Example Configuration Values

**For GROMACS_for_CHARMM-GUI.ipynb:**
```
protein_archive = "{GoogleDrive}/CHARMM-GUI/protein_CHARMM.tgz"
output_folder = "{GoogleDrive}/GROMACS/my_protein_ligand"
ligand_archives = "{GoogleDrive}/CHARMM-GUI/ligand_CHARMM.tgz"
```

**For GROMACS_for_production.ipynb:**
```
project_folder = "{GoogleDrive}/GROMACS/my_protein_ligand"
simulation_duration_ns = 10
output_prefix = "sim"
```

---

## Additional Resources

- [GROMACS Manual](https://manual.gromacs.org/)
- [CHARMM-GUI Documentation](https://www.charmm-gui.org/)
- [CHARMM36 Forcefield](https://mackerell.umaryland.edu/charmm_ff.shtml)
- [Google Colab Documentation](https://colab.research.google.com/notebooks/intro.ipynb)

---

## License

This project is licensed under the AGPL-3.0 or later license. See the [LICENSE](LICENSE) file for details.

---

## Support

For issues, questions, or contributions, please refer to the original repository or create an issue in the project repository.
