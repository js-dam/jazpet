# jazpet
Software for PET-CT medical image analysis and quality assurance 

## JAZPET-RC: Automated NEMA/EARL PET Phantom Analysis

$\color{red}{\textsf{NB. Still quite rough! - Use at your own risk...}}$

JAZPET-RC is a Quarto-based Julia pipeline for the automated Quality Control (QC) analysis of PET scanner data using the standard NEMA/EARL body phantom. This vendor-neutral toolkit directly processes uncompressed DICOM volumes to compute Recovery Coefficients (RC), Signal-to-Noise Ratios (SNR), and background variability, instantly generating a publication-ready PDF report.

### Features

* **Automated Geometric Verification:** Locates the 6 standard NEMA spheres, calculates their center-of-mass, and supports manual 3D spatial offsets.
* **Robust Noise Estimation:** Automatically places 6 spherical background VOIs (37 mm diameter) exactly 55 mm below the primary sphere plane in the Z-axis to ensure consistent noise sampling.
* **Quantitative RC Metrics:** Calculates RC Phy, RC Mean, RC Max, RC Peak, and RC BG by comparing measured activity concentrations against mathematically decayed true activity.
* **Calibration Factor & SNR:** Computes the Mean, Standard Deviation (STD), SNR, and RC BG for each of the 6 background chambers, alongside pooled total metrics.
* **Visual QA:** Generates cross-sectional intensity profiles, volume-based recovery curves, and Coronal/Axial Maximum Intensity Projections (MIPs) displaying the original spheres and dashed-blue background VOIs.
* **Reproducible Reporting:** Compiles results into a LaTeX-typeset PDF report and exports raw data to CSV files.

### Prerequisites

* [Julia](https://julialang.org/downloads/) (v1.6 or higher recommended)
* [Quarto](https://quarto.org/docs/get-started/)
* A LaTeX distribution (e.g., TeX Live or TinyTeX) for PDF compilation.

#### Julia Dependencies
The script relies on several Julia packages. Install them via the Julia REPL:

```julia
using Pkg
Pkg.add(["DICOM", "DataFrames", "Plots", "CSV", "Glob", "Statistics", "Printf"])
```

### Setup and Configuration

1. **Organize DICOM Data:** 
   Place your uncompressed PET DICOM slices into a designated folder. Update the input paths in the configuration block of `jazpet_rc.qmd`.

2. **Set Physics Parameters:**
   Open the `.qmd` file and adjust the `--- CONFIGURATION ---` variables to match your specific scan parameters:
   
   ```julia
   folder = "scanner_name/"
   sr = "series_name"                  
   
   MEAS_ACT_SPH = 27.40e3       # Measured activity in spheres (kBq/mL)
   MEAS_ACT_BCK = 28.04e6/9575  # Measured activity in background (kBq/mL)
   SCAN_TIME_DIFF = 44          # Time difference between measurement and scan (min)
   HALF_LIFE = 109.7            # Isotope half-life (109.7 for F-18)
   ```
   *The pipeline automatically uses these inputs to calculate decay-corrected true activities.*

3. **Render the Report:**
   Run the following command in your terminal to execute the code and compile the PDF:
   ```bash
   quarto render jazpet_rc.qmd --to pdf
   ```

### Outputs

Upon execution, the toolkit generates the following files in your designated directory:
* **`jazpet_rc.pdf`**: The clinical QC report featuring tables, MIPs, intensity profiles, and recovery curves.
* **`fig1_*.png`, `fig2_*.png`, `fig3_*.png`**: High-resolution standalone images of the visual QA checks.
* **`RecCoef-*.csv`**: Raw tabular data of all computed recovery coefficients and background metrics.
* **`SphOff-*.csv`**: Record of the applied manual spatial offsets.
