<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

```markdown
# DAC_Ecell.ipynb — Electrochemical Cell Model for DAC Regeneration

This repository contains the notebook **`DAC_Ecell.ipynb`**, which implements a detailed electrochemical cell and equipment model for regenerating NaOH from Na₂CO₃ within a **direct air capture (DAC)** process. The model is used to evaluate energy requirements, operating windows, and design‑level sizing for a coupled absorber–electrolyser DAC system.

## 1. Context

This notebook supports the DAC regeneration case study described on **electrochemical NaOH regeneration**. It provides numerical results for:

- Electrochemical cell voltage and overpotential breakdown.  
- Specific electricity consumption (SEC) per mole and tonne of CO₂.  
- Mass and charge balances in acid/base tanks and across the membrane.  
- Conceptual sizing of the main hardware (cell stack, tanks, pumps, piping, gas separator, power supply).

The absorber side is represented by a separate (simplified) absorber model; this notebook focuses on the electrochemical regeneration step and its integration with the absorber cycle time.

## 2. Notebook Overview

The code is organised into five main sections:

### Section 1 – Property and Thermodynamic Models

- `IonDB`: database of ionic diffusion coefficients, charges, limiting conductivities, molecular weights, and partial molar volumes.  
- `Thermo`: equilibrium constants \(K_1, K_2, K_w\), Henry’s law constant for CO₂, solution heat capacities, and density/volume relations.  
- `ActivityCoeff`: ionic strength and activity coefficients using the Davies equation.  
- `Speciation`: carbonate speciation in acid and base tanks with optional activity‑corrected pH.  
- `Conductivity`: solution conductivity with ionic‑strength correction.  
- `CO2Degas`: kLa‑based degassing model for dissolved CO₂.  
- `ThermalModel`: lumped energy balance for the combined acid/base inventory.  
- `Electrodes`: Butler–Volmer kinetics and concentration overpotentials for anode and cathode.

### Section 2 – Nernst–Planck Membrane Model

- `NernstPlanckCEM`: 1D Nernst–Planck description of a cation‑exchange membrane with fixed charge density.  
  - Computes Na⁺/H⁺ fluxes (diffusion + migration), water drag, membrane conductivity, and Donnan + ohmic potential drops.  
  - Includes a current‑closure check (`verify_current`) for numerical consistency.

### Section 3 – Equipment Sizing Utilities

- `Materials`, `Hydraulics`, `CellStack`: approximate geometry and mass of the cell stack.  
- `Tank`: cylindrical HDPE/SS tanks with freeboard and wall‑thickness estimates.  
- `Pump`: centrifugal pump sizing from flow and pressure drop.  
- `PowerSupply`: DC power supply sizing (voltage, current, AC and DC power).  
- `GasSeparator`: flash drum for CO₂ removal from product gas.  
- `PipingManifold`: simple header/branch pipe sizing.  
- `Stream`: helper class to assemble inlet/outlet stream tables used in the thesis (S1–S6).

### Section 4 – Absorber Surrogate

- `AbsorberCase1`: simplified well‑mixed NaOH absorber model used here only to generate Na₂CO₃/NaOH feed conditions and an absorber cycle time.  
  - Tracks NaOH → Na₂CO₃ conversion, cumulative CO₂ captured, and number of gas passes until a target NaOH threshold is reached.

### Section 5 – Electrochemical Cell

- `ElectrochemicalCell`: core **variable‑volume, activity‑corrected** cell model with integrated equipment sizing.

Key features:

- ODEs written in terms of **moles**; tank volumes recomputed via partial molar volumes at each step.  
- Full carbonate speciation in the acid tank and NaOH build‑up in the base tank (including NaOH purity).  
- Detailed overpotential breakdown (activation, ohmic, concentration, membrane, Donnan).  
- Dynamic energy balance including resistive heat, reversible heat, and cooling via U·A.  
- Coupling to the Nernst–Planck membrane (Na⁺ transference, H⁺ back‑migration, water drag).  
- Integration with `solve_ivp` until a specified **acid pH stop** (e.g. pH = 5) is reached.  
- Post‑processing routines for performance metrics, balances, hydraulics, and equipment sizing.

The notebook also provides a small helper function `size_EC(...)` to explore combinations of cell area and cell count for a given total current.

## 3. Typical Usage

In the thesis, the following workflow is used:

1. Run `AbsorberCase1` to obtain a Na₂CO₃/NaOH feed composition and absorber cycle time for a given NaOH inventory and gas throughput.  
2. Initialise `ElectrochemicalCell(c_Na2CO3_feed, c_NaOH_residual, **kwargs)` using that feed.  
3. Adjust the applied current density `j_applied` until the EC cell cycle time (to pH_stop) matches the absorber cycle time within a specified tolerance.  
4. Use `solve()`, `compute_performance()`, `size_equipment()` and `build_streams()` to obtain:  
   - SEC (kJ/mol CO₂ and kWh/t CO₂).  
   - CO₂ recovery, NaOH purity, and mass/charge balances.  
   - Cell voltages and overpotential contributions over time.  
   - Preliminary tank, pump, piping, and gas separator sizes, plus stack mass and footprint.

The notebook cells are arranged to follow this workflow directly.

## 4. How to Run

### Requirements

- Python 3.x  
- Packages: `numpy`, `scipy`, `matplotlib` (pre‑installed in Google Colab).

### Steps

1. Open `DAC_Ecell.ipynb` in JupyterLab, VS Code, or Google Colab.  
2. Run all cells in order. The base case will:  
   - Define all classes and helper functions.  
   - Run the absorber surrogate and the electrochemical cell.  
   - Print a design report with key indicators and generate diagnostic plots.

3. To perform sensitivity studies for the thesis:  
   - Edit absorber parameters (`V`, `c0`, `Q`, thresholds) in the absorber section.  
   - Modify `ElectrochemicalCell.DEFAULT` (e.g. `N_cell`, `A_cell`, `j_applied`, `pH_stop`, `Q_flow_total`, `kLa`) or pass overrides directly when instantiating the class.  
   - Re‑run the main analysis cells and record SEC, recovery, and sizing results for inclusion in figures/tables.

## 5. Assumptions and Limitations

- Absorber is treated as a **perfectly mixed vessel** with empirical scaling of DIC uptake vs NaOH concentration (no axial gradients).  
- Membrane transport is 1D Nernst–Planck with a fixed charge density and simplified Donnan potentials.  
- Activity corrections use the **Davies equation**, which is approximate at high ionic strength.  
- Equipment sizing is **order‑of‑magnitude**, intended for comparative and scoping calculations rather than detailed mechanical design.  
- Gas, liquid, and thermal property models are simplified but internally consistent with the cited literature.

## 6. References

The implementation draws on the following sources cited in the thesis:

- Shu et al. (2020) – electrochemical DAC system and performance benchmarks.  
- Newman, J. and Thomas‑Alyea, K. (2004) – Electrochemical Systems.  
- Pintauro, P. and Bennion, D. (1984) – Membrane transport models.  
- Pitzer, K. (1991) – Activity Coefficients in Electrolyte Solutions.  
- Davies, C.W. (1962) – Ion Association.  
- Millero, F. (1971) – Partial molar volumes of electrolytes.  
- Walas, S. (1990) – Chemical Process Equipment.  
- Perry’s Chemical Engineers’ Handbook – hydraulic and equipment correlations.



