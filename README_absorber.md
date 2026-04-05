# CO₂ Absorber Column — 1D Counter-Current NaOH DAC Model

A physics-based, steady-state simulation of a packed absorber column for **Direct Air Capture (DAC)** of CO₂ using aqueous NaOH. The model is implemented as a single Python class (`Absorber`) and is exercised across three Jupyter notebooks covering the core model, sensitivity analysis, optimization, and validation.

---

## Repository Structure

| File | Purpose |
|------|---------|
| `Absorber_Column___Senstivity_Analysis.ipynb` | Core `Absorber` class definition + sensitivity analysis sweeps |
| `Absorber_Optimization.ipynb` | 2-D parameter space exploration and optimum identification |
| `Absorber_Validation.ipynb` | Automated test suite verifying physical correctness |

---

## Model Overview

The `Absorber` class solves a **1D, steady-state, counter-current** gas–liquid mass transfer problem along the column height `z`:

```
z = Z (top)   ←── liquid inlet (fresh NaOH)          gas outlet ──→
      │
      │   Packed bed (Mellapak, Pall, Sulzer, …)
      │
z = 0 (bot)  ──→ liquid outlet (carbonate-loaded)    gas inlet (air + CO₂) ←──
```

Gas flows upward (+z); liquid flows downward (−z).

### Physics & Sub-models

| Aspect | Method / Correlation |
|--------|---------------------|
| Gas-film mass transfer (`kG`) | Billet & Schultes (1999) |
| Liquid-film mass transfer (`kL`) | Higbie penetration theory (default) or user-supplied |
| Reaction enhancement factor (`E`) | Hatta number / infinite-enhancement limit |
| Wetted packing area (`a_w`) | Onda et al. (1968) |
| Column hydraulics / flooding | Billet & Schultes pressure drop / flooding model |
| Carbonate equilibria (pH, DIC) | Millero (1995) |
| CO₂ Henry's law | Sander (2015) |
| OH⁻ reaction kinetics (`k_OH`) | Pohorecki & Moniuk (2001) |
| Water evaporation | Latent-heat flux coupled to gas humidity |
| Column auto-sizing | Gas flooding fraction target (default 70 %) |

The governing ODEs are solved as a **boundary value problem (BVP)** using a shooting method: Brent's root-finding algorithm brackets and converges the unknown bottom-liquid DIC concentration `C_DIC(z=0)`.

### Packing Database

Six packings are pre-loaded:

| Name | Type | Specific area (m²/m³) |
|------|------|----------------------|
| Mellapak 125Y | Structured | 125 |
| Mellapak 250Y | Structured | 250 |
| Mellapak 500Y | Structured | 500 |
| Sulzer BX | Structured | 492 |
| Pall 25 mm | Random | 220 |
| Pall 50 mm | Random | 112 |

---

## Key Parameters

### `Absorber.__init__()` — Selected Arguments

| Parameter | Default | Description |
|-----------|---------|-------------|
| `Z` | `15.0` | Column height [m] |
| `D_col` | `None` | Diameter [m]; `None` triggers auto-sizing |
| `flood_fraction` | `0.70` | Target fraction of flooding velocity |
| `packing_name` | `None` | Name from `PACKING_DB`; overrides individual packing params |
| `a_p` | `250` | Packing specific area [m²/m³] |
| `y_CO2_in` | `420e-6` | Inlet CO₂ mole fraction (420 ppm ambient) |
| `G_vol` | `35.0` | Inlet gas volumetric flow [m³/h] |
| `RH_in` | `0.60` | Inlet relative humidity |
| `c_NaOH_in` | `2.0` | Inlet NaOH concentration [mol/L] |
| `L_vol_flow` | `40.0` | Liquid flow rate [L/min] |
| `T_G_in` | `293.15` | Inlet gas temperature [K] |
| `T_L_in` | `293.15` | Inlet liquid temperature [K] |
| `disc_z` | `401` | Number of axial discretization points |
| `kL_model` | `"Higbie"` | Liquid-film model (`"Higbie"` or override via `kL_override`) |
| `design_mode` | `False` | Auto-compute `L_vol_flow` for a target NaOH utilization |

### Key Performance Indicators (KPIs)

| Method | Returns | Unit |
|--------|---------|------|
| `capture_efficiency()` | η — fraction of inlet CO₂ absorbed | % |
| `removal_rate()` | Space-time yield (STY) | mol CO₂ / m³ / s |
| `NaOH_utilization()` | Fraction of NaOH converted to carbonate | — |
| `col.NTU` | Number of Transfer Units | — |
| `col.HTU` | Height of a Transfer Unit | m |

---

## Notebooks

### 1 · `Absorber_Column___Senstivity_Analysis.ipynb`

Contains the full `Absorber` class source code and the **sensitivity analysis** workflow:

- **Reference case** (`c_NaOH = 2 M`, `L = 40 L/min`, `Z = 15 m`, auto-sized D): prints a detailed column summary including geometry, flow, mass transfer coefficients, profiles, and NTU/HTU.
- **NaOH concentration sweep** over `[0.1, 0.5, 1.0, 2.0, 4.0, 6.0, 8.0]` mol/L, for both auto-sized and fixed-diameter (15 cm) columns.
- Produces tabulated summaries of η, STY, and NaOH utilisation, and exports two profile plots.

Key finding from the output: capture efficiency peaks near **1–2 M NaOH** and declines at higher concentrations due to viscosity / mass-transfer degradation.

### 2 · `Absorber_Optimization.ipynb`

Performs a **2-D parameter sweep** and formal optimization:

- **Contour sweep** over a 15 × 12 grid:
  - `c_NaOH_in`: 0.05 – 6 M (15 log-spaced points)
  - `L_vol_flow`: 5 – 80 L/min (12 points)
- Generates three contour plots (η, STY, NaOH utilization) with the optimum marked.
- Saves the raw grid data to `model_results/optimization/contour_data.npz`.
- Follows up with `scipy.optimize.minimize` for fine-grained local optimization.

Outputs saved to `model_results/optimization/`:
```
contour_data.npz
contour_eta_cNaOH_Lvol.png / .svg
contour_STY_cNaOH_Lvol.png / .svg
contour_util_cNaOH_Lvol.png / .svg
```

### 3 · `Absorber_Validation.ipynb`

An automated test suite with seven checks, all of which pass on the reference configuration:

| Test | What is checked | Tolerance |
|------|----------------|-----------|
| Mass balance — CO₂ | Gas-side removal equals liquid-side uptake | < 0.1 % |
| Mass balance — H₂O | Gas-side gain equals integrated flux | < 1 % |
| Limiting case — short column | η < 30 % for Z = 0.1 m | — |
| Limiting case — tall column | η > 99 % for Z = 30 m | — |
| Limiting case — dilute NaOH | η(0.01 M) < η(2 M) | — |
| Profile monotonicity | y_CO₂ ↓, C_DIC ↓, pH ↑ along z | — |
| NTU / HTU cross-check | `NTU × HTU = Z` and `NTU ≈ ln(y_in/y_out)` | < 1 % |
| BVP convergence | Boundary condition residual at z = Z | < 1 × 10⁻⁶ |
| Physical bounds | 0 ≤ η ≤ 100, 0 ≤ util ≤ 1, 0 < pH < 15, E ≥ 1 | — |

All tests report ✓ in the reference run.

---

## Dependencies

```
numpy
scipy
matplotlib
```

The notebooks import the `Absorber` class from a local module named `absorber` (i.e. `absorber.py`). The class source is embedded in `Absorber_Column___Senstivity_Analysis.ipynb`; extract and save it as `absorber.py` before running the other two notebooks.

---

## Quick Start

```python
from absorber import Absorber

# Build and solve a reference case
col = Absorber(c_NaOH_in=2.0, L_vol_flow=40.0, Z=15.0)
col.solve_column()

print(f"Capture efficiency : {col.capture_efficiency():.2f} %")
print(f"NaOH utilization   : {col.NaOH_utilization():.4f}")
print(f"NTU / HTU          : {col.NTU:.2f} / {col.HTU:.3f} m")
```

---

## References

- Seithümmer et al. (2025) *Chem. Ing. Tech.* 97(5), 554–559
- Ghaffari et al. (2023) *Ind. Eng. Chem. Res.* 62(19), 7566–7579
- Knuutila et al. (2010) *Chem. Eng. Sci.* 65, 6077–6088
- Sander (2015) *Atmos. Chem. Phys.* — CO₂ Henry's law
- Millero (1995) — Carbonate equilibria
- Billet & Schultes (1999) — Packed column hydraulics
- Onda et al. (1968) — Wetted area correlation
- Pohorecki & Moniuk (2001) — k_OH kinetics
