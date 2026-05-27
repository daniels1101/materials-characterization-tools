# Materials Characterization Tools

Python notebooks for analyzing experimental data from superconductor research. Covers two measurement techniques used in condensed matter physics labs: MPMS magnetometry and powder X-ray diffraction with Rietveld refinement.

Both notebooks run against real sample data included in the `data/` folder.

---

## Notebooks

### `mpms_analysis.ipynb` — MPMS Magnetometry

Processes raw `.dat` files from a Quantum Design MPMS3 magnetometer.

**What it does:**

- Parses the 44-line Quantum Design header and groups measurements by applied field
- Computes and plots normalized volume fraction susceptibility at two applied fields to show the Meissner shielding transition
- Fits the normal-state susceptibility to a Curie-Weiss model above $T_c$:

$$\chi(T) = \frac{C}{T - \theta_{\text{CW}}} + \chi_0$$

- Plots normalized susceptibility $\chi/|\chi|_{\text{max}}$ vs temperature at a single field and marks $T_c$ with a vertical line

**Sample data:** `data/ZrV2_sample.dat` — DC magnetometry of a ZrV$_2$ single crystal measured at 10 Oe and 10,000 Oe from 1.8 K to 20 K.

---

### `cell_volume_vs_temperature.ipynb` — Rietveld Refinement Analysis

Extracts unit cell volumes from a series of `.cif` files produced by Rietveld refinement (e.g. from TOPAS or FullProf) and tracks thermal contraction of the unit cell.

**What it does:**

- Parses `_cell_volume` from each `.cif` file using regex
- Pairs each volume with a temperature from a log file by trial number
- Fits a linear model $V(T) = a \cdot T + b$ to extract the volumetric thermal expansion rate $dV/dT$ with uncertainties from the covariance matrix
- Produces two plots: raw cell volume with linear fit, and volume normalized to the highest-temperature measurement

**Sample data:** `data/cif/` — 6 refinements of a ZrV$_2$ sample cooled from 278 K to 100 K. `data/refinement.log` — temperature log pairing each refinement to its measurement temperature.

**Result from sample data:** $dV/dT = 0.0111 \pm 0.0007$ Å$^3$/K over the 100–278 K range.

---

## Repository Structure

```
materials-characterization-tools/
├── mpms_analysis.ipynb
├── cell_volume_vs_temperature.ipynb
├── data/
│   ├── ZrV2_sample.dat       # MPMS magnetometry data
│   ├── refinement.log        # temperature log for Rietveld refinements
│   └── cif/
│       ├── 001_refinement.cif
│       ├── 002_refinement.cif
│       ├── 003_refinement.cif
│       ├── 004_refinement.cif
│       ├── 005_refinement.cif
│       └── 006_refinement.cif
├── requirements.txt
└── README.md
```

---

## Getting Started

**Requirements:** Python 3.8+

Install dependencies:

```bash
pip install numpy scipy pandas matplotlib
```

Then open either notebook and run all cells. The `data/` folder is already set up — no path changes needed.

To use your own data, edit the user input section at the top of each analysis cell. Each notebook has a clearly marked block like:

```python
# ── User inputs ───────────────────────────────────────────────
FILEPATH = 'data/ZrV2_sample.dat'
MASS_MG  = 19.9
DENSITY  = 6.23
```

---

## Data Format

**MPMS `.dat` files** follow the Quantum Design format: a 44-line header followed by comma-separated measurement data. The parser handles variable or multi-field measurements automatically.

**Rietveld `.cif` files** must contain a `_cell_volume` field. File names must start with an integer trial number followed by an underscore, e.g. `001_refinement.cif`.

**Log file** must have three whitespace-separated columns per line:

```
trial_number   label   temperature_K
```

---

## Background

These tools were written for research on superconducting materials.

The MPMS notebook was used to characterize the Meissner effect and normal-state susceptibility of superconducting candidates. The Rietveld notebook tracked unit cell contraction from powder X-ray diffraction data collected during variable-temperature measurements.