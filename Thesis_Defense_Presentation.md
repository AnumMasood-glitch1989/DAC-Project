# Thesis Defense Presentation
## Electrochemical Direct Air Capture of CO₂: Integrated Absorber–Electrolyser Modelling, Optimization, and Techno-Economic Analysis

---

## SLIDE 1 — Title Slide

### Electrochemical Direct Air Capture of CO₂
#### Integrated Absorber–Electrolyser Modelling, Optimization, and Techno-Economic Analysis

**Candidate:** [Your Name]  
**Supervisor:** [Supervisor Name]  
**Department / University:** [Department, University]  
**Date:** [Defense Date]

---

## SLIDE 2 — Outline

1. Introduction & Motivation
2. Literature Review & Research Gap
3. Research Objectives
4. Methodology Overview
5. System Architecture (Block Flow Diagram)
6. Module 1 — Packed-Column NaOH Absorber Model
7. Module 2 — Electrochemical Cell (EC) Model
8. Module 3 — Integrated DAC System
9. Results — Absorber Column Performance
10. Results — Absorber Sensitivity Analysis
11. Results — Absorber Optimization
12. Results — Model Validation
13. Results — Electrochemical Cell Base Case
14. Results — EC Cell Sensitivity Analysis
15. Results — Integrated System Performance
16. Techno-Economic Analysis — CAPEX
17. Techno-Economic Analysis — OPEX & Revenue
18. Techno-Economic Analysis — Scale-Up Pathway
19. Discussion & Key Findings
20. Conclusions
21. Future Work
22. References
23. Q&A

---

## SLIDE 3 — Introduction & Motivation

### The Climate Challenge

- Atmospheric CO₂ has surpassed **420 ppm** — highest in 3 million years
- IPCC AR6: Net-zero by 2050 requires **carbon dioxide removal (CDR)** at gigatonne scale
- Direct Air Capture (DAC) is one of six essential CDR pathways (NASEM, 2019)

### Why Electrochemical DAC?

| Approach | Mechanism | Energy Source | Key Limitation |
|----------|-----------|---------------|----------------|
| Thermal swing (e.g., Carbon Engineering) | Ca-loop, 900°C calciner | Natural gas / heat | High-temperature heat required |
| Solid sorbent (e.g., Climeworks) | Amine-functionalized filter | Low-grade heat (80–120°C) | Sorbent degradation |
| **Electrochemical (this work)** | **NaOH/Na₂CO₃ pH swing** | **Electricity only** | **Low current density** |

### Key Advantages of Electrochemical Route

- **No fossil fuel combustion** — fully electrifiable, ideal for renewable integration
- **Ambient temperature & pressure** — no high-T equipment
- **Co-produces H₂** — potential revenue offset
- **Modular & scalable** — from lab bench to distributed units

---

## SLIDE 4 — Literature Review & Research Gap

### State of the Art

| Reference | Contribution | Limitation |
|-----------|-------------|------------|
| Shu et al. (2020) | First rigorous EC-DAC model; SEC_theory = 164 kJ/mol | Simplified membrane; no equipment sizing |
| Keith et al. (2018) | Carbon Engineering Ca-loop TEA ($94–232/tCO₂) | Thermal process, not electrochemical |
| Sabatino et al. (2021) | Comprehensive DAC technology review | No integrated absorber + EC model |
| Eisaman et al. (2012) | BPMED for ocean capture | Different chemistry (seawater) |

### Research Gap

> **No existing study provides an integrated, first-principles model coupling:**
> 1. A rigorous packed-column absorber (BVP with mass-transfer correlations)
> 2. A Nernst-Planck CEM electrochemical cell (Butler-Volmer + activity corrections)
> 3. Full equipment sizing and techno-economic analysis
> 4. Location-specific cost data (Pakistan, March 2026)

---

## SLIDE 5 — Research Objectives

1. **Develop** a physics-based 1D packed-column absorber model for CO₂–NaOH DAC with validated mass-transfer correlations
2. **Build** a dynamic electrochemical cell model with Nernst-Planck membrane transport, Butler-Volmer kinetics, and activity-corrected thermodynamics
3. **Integrate** the absorber and EC cell into a coupled DAC cycle with time-matched operation
4. **Optimize** absorber operating parameters (c_NaOH, L_vol_flow) via 2D contour analysis and Nelder-Mead optimization
5. **Perform** a comprehensive sensitivity analysis on both subsystems
6. **Validate** the absorber model against physical consistency checks
7. **Conduct** a techno-economic analysis (CAPEX/OPEX/ROI) with Pakistan-specific pricing

---

## SLIDE 6 — Methodology Overview

```
┌──────────────────────────────────────────────────────────────┐
│                    METHODOLOGY FRAMEWORK                      │
├──────────────────────────────────────────────────────────────┤
│                                                               │
│   ┌─────────────┐     ┌─────────────┐     ┌──────────────┐  │
│   │  ABSORBER    │     │  EC CELL     │     │   TECHNO-    │  │
│   │  MODEL       │────▶│  MODEL       │────▶│   ECONOMIC   │  │
│   │  (Steady-    │     │  (Dynamic    │     │   ANALYSIS   │  │
│   │   State BVP) │     │   ODE/IVP)   │     │  (CAPEX/OPEX)│  │
│   └──────┬──────┘     └──────┬──────┘     └──────────────┘  │
│          │                    │                                │
│   ┌──────▼──────┐     ┌──────▼──────┐                        │
│   │ SENSITIVITY  │     │ SENSITIVITY  │                        │
│   │ & OPTIM.     │     │ ANALYSIS     │                        │
│   └──────┬──────┘     └─────────────┘                        │
│          │                                                    │
│   ┌──────▼──────┐                                            │
│   │ VALIDATION   │                                            │
│   │ (7 Tests)    │                                            │
│   └─────────────┘                                            │
└──────────────────────────────────────────────────────────────┘
```

**Tools:** Python 3.x, NumPy, SciPy, Matplotlib  
**Solver:** `solve_ivp` (RK45, rtol = 10⁻⁸) with BVP shooting (Brent's method)

---

## SLIDE 7 — System Architecture (Block Flow Diagram)

### Electrochemical NaOH Regeneration DAC System

```
                        Air (420 ppm CO₂)
                              │
                              ▼
                    ┌─────────────────┐
                    │   PACKED COLUMN  │  ← Fresh NaOH (2 M)
                    │    ABSORBER      │
                    │   (Mellapak 250Y)│
                    │   D=29cm, Z=15m  │
                    │   η = 99.7%      │
                    └────────┬────────┘
                             │  Spent NaOH → Na₂CO₃ (0.95 M)
                             ▼
              ┌──────────────────────────────┐
              │   200 L TANK (Acid Side)      │
              │   Na₂CO₃ → CO₂↑ + Na⁺        │
              └──────────────┬───────────────┘
                             │
                   ┌─────────▼─────────┐
                   │  ELECTROCHEMICAL   │
                   │  CELL STACK        │
                   │  7 × 500 cm²       │
                   │  Nafion 117 CEM    │  ⟵ DC Power (23 W)
                   │  j = 92.4 A/m²     │
                   └─────────┬─────────┘
                             │
              ┌──────────────▼───────────────┐
              │   200 L TANK (Base Side)      │
              │   NaOH Product (1.72 M)       │─── Recycle to Absorber
              └──────────────────────────────┘
                             │
                    ┌────────▼────────┐
                    │  GAS SEPARATOR   │──── CO₂ Product (>99%)
                    │  + H₂ Recovery   │──── H₂ Byproduct
                    └─────────────────┘
```

**Cycle:** Absorber runs 331 h → EC Cell runs 331 h → NaOH recycled → Repeat

---

## SLIDE 8 — Module 1: Packed-Column Absorber Model

### Model Configuration

| Parameter | Value | Source |
|-----------|-------|--------|
| Packing | Mellapak 250Y (structured) | Sulzer |
| Specific area (a_p) | 250 m²/m³ | — |
| Void fraction (ε) | 0.97 | — |
| Packed height (Z) | 15.0 m | Design |
| Gas flow (G_vol) | 35.0 m³/h | Design |
| CO₂ inlet | 420 ppm (ambient) | — |
| NaOH inlet | 2.0 mol/L | Design |
| Liquid flow (L_vol) | 40 L/min | Design |
| Temperature | 20°C (isothermal base case) | — |
| Relative humidity | 60% | — |

### Governing ODEs (Counter-Current BVP)

**State vector:** Y_CO₂(z), C_DIC(z), Y_H₂O(z) — gas CO₂ ratio, liquid DIC, gas H₂O ratio

$$\frac{dY_{\text{CO}_2}}{dz} = -\frac{a_w \cdot N_{\text{CO}_2}}{G_{\text{inert}}}$$

$$\frac{dC_{\text{DIC}}}{dz} = -\frac{a_w \cdot N_{\text{CO}_2}}{u_L \cdot 1000}$$

$$\frac{dY_{\text{H}_2\text{O}}}{dz} = +\frac{a_w \cdot N_{\text{H}_2\text{O}}}{G_{\text{inert}}}$$

**Local CO₂ flux:**

$$N_{\text{CO}_2} = K_G \cdot \left( y_{\text{CO}_2} - y^* \right)$$

**Overall mass transfer coefficient:**

$$\frac{1}{K_G} = \frac{1}{k_G} + \frac{1}{H_{\text{eff}} \cdot E \cdot k_L}$$

---

## SLIDE 9 — Absorber Sub-Models

### Mass Transfer Correlations

| Sub-Model | Correlation | Reference |
|-----------|-------------|-----------|
| Gas-film MTC (k_G) | Billet & Schultes (1999) | C_BG = 0.0338 |
| Liquid-film MTC (k_L) | Higbie penetration theory | τ_exp = d_h / u_L |
| Wetted area (a_w) | Onda et al. (1968) | a_w/a_p = f(Re_L, Fr_L, We_L) |
| Henry's law (H_eff) | Sander (2015) + salting-out | H_eff = H_pure / 10^(0.11·I) |
| Reaction kinetics (k_OH) | Pohorecki & Moniuk (2001) | k_OH = 5.985×10¹³ exp(−55400/RT) |
| Enhancement factor (E) | DeCoursey approximation | E = f(Ha, E_∞) |
| Flooding velocity | Billet & Schultes (modified) | u_G,flood = C_v·√(g·ε³/a_p·ρ_G/ρ_L) |

### Hatta Number and Enhancement

$$Ha = \frac{\sqrt{k_{\text{OH}} \cdot c_{\text{OH}^-} \cdot D_{\text{CO}_2}}}{k_L}$$

At 2 M NaOH: **Ha = 55.4** → Pseudo-first-order regime (Ha >> 3)

$$E \approx Ha \quad \text{(when } Ha \gg 3 \text{ and } E_\infty \gg Ha\text{)}$$

### Column Auto-Sizing

Column diameter is automatically selected to satisfy:
- Flooding fraction ≤ 70%
- Liquid loading ≤ 40 m³/(m²·h)
- Minimum diameter ≥ 15 cm

**Result:** D = 29 cm, u_G = 14.7 cm/s, flood = 65.1%

---

## SLIDE 10 — Module 2: Electrochemical Cell Model

### Architecture: BPMED-Type pH Swing Cell

| Component | Specification |
|-----------|---------------|
| Cell type | Bipolar membrane electrodialysis (CEM) |
| Membrane | Nafion 117, d = 178 µm, IEC = 1.2 mol/L |
| Electrode area | 500 cm² per cell |
| Number of cells | 7 (series) |
| Gap (acid + base) | 3 mm each |
| Current density | 92.4 A/m² (9.2 mA/cm²) |
| Electrolyte flow | 40 L/min total |
| Tank volumes | 200 L acid + 200 L base |

### ODE System (9 State Variables in Moles)

| State | Variable | Description |
|-------|----------|-------------|
| y₁ | n_DIC | DIC moles in acid tank |
| y₂ | n_Na,a | Na⁺ moles in acid |
| y₃ | n_Na,b | Na⁺ moles in base |
| y₄ | n_OH,b | OH⁻ moles in base |
| y₅ | n_CO₂,g | CO₂ gas accumulated |
| y₆ | n_res | Residual NaOH in acid |
| y₇ | T | System temperature |
| y₈ | n_H₂O,a | Water moles in acid |
| y₉ | n_H₂O,b | Water moles in base |

### Cell Voltage Equation

$$E_{\text{cell}} = E_{\text{rev}} + \eta_{\text{an}} + \eta_{\text{cat}} + \eta_{\text{ohm}} + |V_{\text{mem}}| + \eta_{\text{conc,an}} + \eta_{\text{conc,cat}}$$

Where:
- $E_{\text{rev}} = \frac{RT}{F} \cdot 2.303 \cdot \Delta\text{pH}$ (activity-corrected)
- $\eta_{\text{an/cat}}$: Butler-Volmer overpotentials
- $\eta_{\text{ohm}} = j \cdot (d_a/\sigma_a + d_b/\sigma_b)$
- $V_{\text{mem}}$: Nernst-Planck ohmic + Donnan contributions

---

## SLIDE 11 — Nernst-Planck Membrane Transport

### CEM Membrane Model (Nafion 117)

**Donnan equilibrium at each face:**
$$c_{i,\text{mem}} = Q \cdot \frac{c_{i,\text{sol}}}{c_{\text{Na}^+} + c_{\text{H}^+}}$$

**Ion fluxes (diffusion + migration):**
$$J_i = -D_{i,\text{mem}} \frac{\Delta c_i}{d} + D_{i,\text{mem}} \cdot \bar{c}_i \cdot \frac{F}{RT} \left(-\frac{d\phi}{dx}\right)$$

**Current constraint:**
$$j = F \cdot (J_{\text{Na}^+} + J_{\text{H}^+})$$

**Key membrane parameters:**
| Parameter | Value |
|-----------|-------|
| D_Na,mem / D_Na,bulk | 0.10 (10%) |
| D_H,mem / D_H,bulk | 0.10 (10%) |
| Fixed charge Q | 1.2 mol/L |
| Electro-osmotic drag | n_drag = 3.0 mol H₂O / mol ion |
| σ_mem | 0.5–0.6 S/m |

**Transport numbers:** t_Na⁺ = 0.998–1.000 | t_H⁺ = 0.000–0.002

→ Excellent Na⁺ selectivity with minimal H⁺ back-migration

---

## SLIDE 12 — Results: Absorber Base Case

### Reference Case Performance

| KPI | Value |
|-----|-------|
| **CO₂ inlet** | 420 ppm |
| **CO₂ outlet** | 1.43 ppm |
| **Capture efficiency (η)** | **99.66%** |
| **STY (space-time yield)** | 2,585 µmol/m³/s |
| **NaOH utilization** | 0.03% |
| **NTU** | 5.685 |
| **HTU** | 2.638 m |
| **Hatta number (Ha)** | 55.4 |
| **Enhancement factor (E)** | 55.4 |
| **K_G** | 14.37 × 10⁻³ mol/m²/s/atm |
| **pH** | 14.23 (constant — NaOH barely consumed) |
| **Gas-film resistance** | Dominant (kG controls) |
| **Flooding fraction** | 65.1% |
| **Column diameter** | 29 cm (auto-sized) |

### Mass Balance Verification

| Balance | Gas-Phase | Liquid-Phase | Error |
|---------|-----------|-------------|-------|
| CO₂ | 1.6917 × 10⁻⁴ mol/s | 1.6917 × 10⁻⁴ mol/s | **4.2 × 10⁻⁶** ✓ |
| H₂O | 3.460 × 10⁻³ mol/s | 3.460 × 10⁻³ mol/s | **1.2 × 10⁻⁹** ✓ |

*16-panel diagnostic plot: y_CO₂, C_DIC, pH, N_CO₂, Ha, E, K_G, driving force, k_G, k_L, y_H₂O, N_H₂O, T_G/T_L, c_NaOH, ΔH_rxn, Re dimensionless numbers*

---

## SLIDE 13 — Results: Absorber Sensitivity Analysis

### Sensitivity 1: Liquid Flow Rate (L_vol_flow)

| L [L/min] | η [%] | STY [µmol/m³/s] | NaOH util [%] |
|-----------|-------|-----------------|---------------|
| 1 | 83.0 | **30,085** | 0.84 |
| 5 | 94.4 | **34,225** | 0.19 |
| 10 | 96.9 | **35,097** | 0.10 |
| 20 | 98.8 | 11,333 | 0.05 |
| **40** | **99.7** | **2,585** | **0.03** |
| 80 | 99.9 | 58 | 0.01 |

> **Key Finding:** STY peaks at low L (~10 L/min) due to compact column sizing; higher L gives higher η but vastly larger column (lower STY).

### Sensitivity 2: NaOH Concentration (Auto-Sized D)

| c_NaOH [M] | η [%] | STY [µmol/m³/s] |
|-------------|-------|-----------------|
| 0.1 | 99.1 | 2,571 |
| 0.5 | 99.6 | 2,583 |
| **1–2** | **99.6–99.7** | **2,584–2,585** |
| 4.0 | 97.4 | 2,526 |
| 6.0 | 94.0 | 2,437 |
| 8.0 | 86.5 | 2,242 |

> **Key Finding:** η peaks at 1–2 M NaOH then **declines** at higher concentrations due to ionic-strength effects on Henry's law (salting-out), increased viscosity, and reduced CO₂ diffusivity.

---

## SLIDE 14 — Results: Absorber Optimization

### 2D Contour Optimization: c_NaOH × L_vol_flow

**Grid:** 15 NaOH concentrations × 12 liquid flows = **180 simulations**

*Three contour plots: η, STY, and NaOH utilization over the (c_NaOH, L_vol) parameter space*

### Nelder-Mead Optimization Results

| Objective | Optimal c_NaOH [M] | η [%] | STY [µmol/m³/s] | NaOH util [%] |
|-----------|-------------------|-------|-----------------|---------------|
| **Max STY** | **2.09** | 99.66 | **2,585** | 0.024 |
| **Max Utilization** (η ≥ 95%) | **0.010** | 95.5 | 2,478 | **4.86** |

> **Trade-off:** STY is maximized at 2 M NaOH (high η, large excess of reactant). NaOH utilization is maximized at very dilute NaOH (0.01 M) where nearly all NaOH reacts — but at the cost of 4% lower capture efficiency.

> **Design recommendation:** Use c_NaOH = **2 M** for maximum CO₂ removal; adjust NaOH concentration only if reagent cost becomes dominant.

---

## SLIDE 15 — Results: Model Validation (7 Tests — All ✓)

| # | Test | Criterion | Result | Status |
|---|------|-----------|--------|--------|
| 1 | CO₂ mass balance | Gas = Liquid uptake (< 0.1%) | err = **4.2 × 10⁻⁶** | ✓ |
| 2a | Short column (Z=0.1m) | η < 30% | η = **3.78%** | ✓ |
| 2b | Tall column (Z=30m) | η > 99% | η = **100.00%** | ✓ |
| 2c | Dilute vs Concentrated NaOH | η(0.01M) < η(2M) | 95.5% < 99.7% | ✓ |
| 3 | Profile monotonicity | y_CO₂ ↓, C_DIC ↓, pH ↑ | All monotonic | ✓ |
| 4 | NTU/HTU cross-check | NTU × HTU = Z | 5.685 × 2.638 = **15.000 m** | ✓ |
| 5 | BVP convergence | BC residual < 10⁻⁶ | **0.00** | ✓ |
| 6 | Physical bounds | 0≤η≤100, 0≤util≤1, E≥1 | All satisfied | ✓ |

> The model demonstrates excellent numerical consistency and physically meaningful behavior across all test conditions.

---

## SLIDE 16 — Results: Electrochemical Cell Base Case

### Base Case: 7 × 500 cm² | j = 92.4 A/m² | pH_stop = 5.0

| Metric | Value |
|--------|-------|
| **Cycle time** | 331 h (13.8 days) — matched to absorber |
| **CO₂ recovered** | 183.3 mol (8.07 kg) |
| **CO₂ recovery** | **96.5%** |
| **NaOH produced** | 1.72 M (369 mol) |
| **NaOH purity** | 92.0% |
| **NaOH makeup** | 32.3 mol (1.29 kg/cycle) |
| **SEC (cell only)** | **149 kJ/mol** |
| **SEC (total system)** | **160 kJ/mol = 1,012 kWh/ton** |
| **2nd-law efficiency** | **37.5%** |
| **Cell voltage (avg)** | 0.711 V |
| **Power (cell)** | 23.0 W |
| **Total power** | 26.7 W |

### Voltage Breakdown (Average)

| Component | Voltage [V] | % of E_cell |
|-----------|-------------|-------------|
| E_rev (activity) | 0.290 | 40.8% |
| η_ohmic (electrolyte) | 0.157 | 22.0% |
| η_cathode (BV) | 0.123 | 17.3% |
| η_conc,anode | 0.058 | 8.2% |
| η_anode (BV) | 0.042 | 5.9% |
| V_membrane (NP) | 0.041 | 5.7% |
| η_conc,cathode | 0.001 | 0.1% |
| **Total** | **0.711** | **100%** |

> Reversible potential (41%) and ohmic losses (22%) dominate — gap reduction offers the largest efficiency gain.

---

## SLIDE 17 — Results: EC Cell Sensitivity Analysis

### Impact Ranking

| Parameter | Range | SEC Range [kJ/mol] | Impact |
|-----------|-------|-------------------|--------|
| **Current density j** | 50–300 A/m² | 120–251 | **STRONG** |
| **Electrode gap** | 1–6 mm | 128–181 | **STRONG** |
| **Temperature** | 5–50°C | 169–130 | **STRONG** |
| N_cell | 3–20 | 191–149 | Moderate* |
| Membrane IEC (Q) | 0.6–2.5 mol/L | 154–148 | Weak |
| Membrane thickness | 50–300 µm | 148–152 | **WEAK** |

*\*N ≥ 5 yields constant SEC; only affects cycle time/throughput*

### 2D Contour: j × Gap → SEC

| j \ Gap | 1 mm | 2 mm | 3 mm | 4 mm | 5 mm |
|---------|------|------|------|------|------|
| 50 A/m² | **111** | 116 | 120 | 124 | 129 |
| 100 A/m² | 130 | 142 | **154** | 165 | 177 |
| 200 A/m² | 160 | 183 | 205 | 227 | **249** |

> **Minimum SEC = 111 kJ/mol** achievable with j = 50 A/m² and 1 mm gap (but at 600 h cycle time)

> Ref: Shu (2020) — SEC_theory = 164 kJ/mol, SEC_expt = 374 kJ/mol. **Our model: 149 kJ/mol** (within 10% of theoretical minimum, 60% below experimental).

---

## SLIDE 18 — Results: Integrated System

### Cycle Summary

| Subsystem | Duration | CO₂ [mol] | CO₂ [kg] |
|-----------|----------|-----------|----------|
| Absorber | 331 h (13.8 d) | 190 (captured) | 8.36 |
| EC Cell | 331 h (13.8 d) | 183 (released) | 8.07 |
| **Net Recovery** | — | — | **96.5%** |

### Annual Performance

| Metric | Value |
|--------|-------|
| Cycles per year | 26.5 |
| **CO₂ output** | **0.58 kg/day → 213 kg/yr** |
| H₂ byproduct | 9.8 kg/yr |
| NaOH makeup | 34.2 kg/yr |
| Total power | 76.7 W (EC + blower) |
| Water consumed | 6.6 kg/cycle |

### Equipment Summary

| Item | Specification |
|------|--------------|
| Stack | 31 × 23 × 11 cm, 30 kg dry |
| Acid/Base tanks | D=59 cm × L=88 cm, HDPE (each) |
| Gas separator | D=41 cm × L=83 cm |
| PSU | 50 W rated, 5 V × 4.6 A |
| **Total footprint** | **1.08 m²** |
| **Total mass** | **60 kg (dry) / 482 kg (wet)** |

### Mass Balance Closure

| Species | In [mol] | Out [mol] | Balance |
|---------|----------|-----------|---------|
| Na⁺ | 402.0 | 402.0 | **100.000%** ✓ |
| DIC | 190.0 | 190.0 (liq 6.7 + gas 183.3) | **100.000%** ✓ |
| e⁻/CO₂ ratio | — | — | **2.00** (stoichiometric) ✓ |

---

## SLIDE 19 — CAPEX Analysis (Pakistan, March 2026)

### Total CAPEX: $6,542 (Rs. 18.32 Lakh)

| Category | Cost [USD] | % of Total |
|----------|-----------|------------|
| **Absorber** | $2,227 | 34% |
| — SS316 Shell (331 kg) | $1,124 | |
| — Mellapak 250Y Packing | $723 | |
| **EC Stack** | $920 | 14% |
| — Nafion 117 Membranes (0.35 m²) | $525 | |
| — Platinum Catalyst (0.20 g) | $13 | |
| **Balance of Plant** | $1,854 | 28% |
| — Pumps, PSU, Gas Separator | $880 | |
| — Instruments, Piping, Controls | $974 | |
| **Installation & Contingency** | $1,542 | 24% |

### Price References

All prices sourced and cited to March 2026:
- [R1] NEPRA electricity: Rs. 26.23/kWh
- [R2] Exchange: 280 PKR/USD
- [R3] NaOH: Rs. 222/kg
- [R4] Nafion 117: $1,500/m²
- [R5] Platinum: $63/g
- [R6] Mellapak: $730/m³
- [R7] SS316: Rs. 950/kg

---

## SLIDE 20 — OPEX & Revenue Analysis

### Annual OPEX: $1,389/yr (Rs. 3.89 Lakh/yr)

| Category | $/yr | % |
|----------|------|---|
| **Labor** (0.35 FTE) | $690 | 50% |
| **Replacement** (membrane, packing) | $279 | 20% |
| **Maintenance & Insurance** | $312 | 22% |
| **Electricity** (848 kWh/yr) | $79 | 6% |
| **Chemicals** (NaOH + H₂O) | $30 | 2% |

### Revenue Scenarios (213 kg CO₂/yr + 9.8 kg H₂/yr)

| Scenario | CO₂ Price | Rev [$/yr] | NPV (20yr) |
|----------|-----------|-----------|------------|
| **A: Food-Grade (Pakistan)** | Rs. 1,200/kg ($4,286/t) | **$954** | −$13,225 |
| B: CO₂ Export (MENA) | $320/t | $108 | −$26,231 |
| C: Carbon Credits (CDR) | $250/t | $93 | −$26,460 |
| D: Combined (A + Credits) | $4,286/t + $125/t | $981 | −$12,815 |

### Levelized Cost of CO₂ Capture (LCOC)

$$\text{LCOC} = \frac{\text{CAPEX}/n + \text{OPEX}_{\text{annual}}}{\text{CO}_2 \text{ annual}} = \frac{\$6{,}542/20 + \$1{,}389}{0.2135 \text{ t}} = \$8{,}059\text{/ton}$$

> At lab scale (1×), all scenarios are NPV-negative due to labor-dominated OPEX.

---

## SLIDE 21 — Scale-Up Pathway to Profitability

### Path to Breakeven

| Scale | CO₂ [t/yr] | CAPEX | OPEX | Revenue | LCOC [$/t] | Payback | NPV(20yr) |
|-------|-----------|-------|------|---------|------------|---------|-----------|
| **1×** | **0.21** | $6.5k | $1.4k | $0.95k | **$8,059** | >20 yr | −$13k ✗ |
| 5× | 1.1 | $16k | $3.4k | $4.8k | $3,941 | 8.6 yr | +$5k ✓ |
| 10× | 2.1 | $24k | $5.3k | $9.5k | $3,054 | 4.9 yr | +$41k ✓ |
| 50× | 10.7 | $64k | $17k | $48k | $1,876 | 2.0 yr | +$410k ✓ |
| 100× | 21.4 | $96k | $29k | $95k | **$1,584** | 1.4 yr | +$924k ✓ |
| 500× | 106.8 | $252k | $111k | $477k | **$1,154** | 0.7 yr | +$5.4M ✓ |

### ★ Breakeven Scale: ~2× (0.42 t CO₂/yr)

> Scaling benefits arise from:
> - Sub-linear CAPEX growth (~n^0.7 power law)
> - Fixed labor costs amortized over larger output
> - Higher equipment utilization

> At 100×: LCOC = **$1,584/ton** — competitive with imported food-grade CO₂ ($6,153/ton, Pakistan import average) and approaching global carbon credit pricing ($170–500/ton).

---

## SLIDE 22 — Key Findings & Discussion

### 1. Absorber Performance

- The NaOH packed-column absorber achieves **99.7% CO₂ capture** from ambient air at 420 ppm
- The reaction operates in the **pseudo-first-order regime** (Ha = 55) with gas-film resistance dominating
- NaOH concentration optimum lies at **~2 M** — higher concentrations suffer from salting-out effects
- The adiabatic case shows negligible temperature rise (ΔT_L < 0.05 K) — isothermal assumption is valid

### 2. Electrochemical Cell

- SEC = **149 kJ/mol** — within 10% of the Shu (2020) theoretical minimum (164 kJ/mol)
- This represents **60% improvement** over the experimental benchmark (374 kJ/mol)
- The largest SEC reduction comes from **electrode gap minimization** (1 mm → SEC = 111 kJ/mol)
- Membrane properties (thickness, IEC) have surprisingly **weak** impact on SEC

### 3. System Integration

- Absorber and EC cell cycle times are **self-consistently matched** at 331 h via iterative j-adjustment
- CO₂ recovery = **96.5%** (3.5% remains dissolved as DIC in acid effluent)
- The system co-produces **H₂** (e⁻/CO₂ = 2.0), providing an additional revenue stream

### 4. Economics

- Lab-scale unit is **not economically viable** (LCOC = $8,059/ton)
- Breakeven occurs at **~2× scale** in the Pakistan food-grade CO₂ market
- At 100× scale: LCOC drops to **$1,584/ton**, ROI = 1.4 yr payback
- The Pakistan food-grade CO₂ market ($4,286/ton retail) offers the most attractive economics

---

## SLIDE 23 — Comparison with Literature

| Metric | This Work | Shu (2020) Theory | Shu (2020) Expt | Keith (2018) CE |
|--------|-----------|-------------------|-----------------|-----------------|
| **SEC [kJ/mol]** | **149–160** | 164 | 374 | ~500* |
| **SEC [kWh/ton]** | **1,012** | 1,036 | 2,362 | ~1,400* |
| **CO₂ Recovery [%]** | **96.5** | ~100 (assumed) | 85 | ~95 |
| **Cell Voltage [V]** | **0.71** | 0.85 | ~2 | — |
| Process Type | EC (CEM) | EC (BPMED) | EC (BPMED) | Ca-loop (thermal) |
| CO₂ Source | Air (420 ppm) | Air | Air | Air |
| η₂nd [%] | **37.5** | ~43 | ~15 | ~20 |

*Keith (2018) values approximate — different process type (thermal)

### Advantages of This Work

1. **First integrated model** coupling rigorous absorber BVP with Nernst-Planck EC cell
2. **Activity-corrected** thermodynamics (Davies equation)
3. **Variable-volume** tank model with partial molar volumes
4. **Equipment sizing** from first principles
5. **Location-specific** TEA with cited, current pricing

---

## SLIDE 24 — Conclusions

### Technical Conclusions

1. ✅ A **physics-based 1D packed-column absorber** achieves 99.7% capture efficiency from ambient air with 2 M NaOH at Z = 15 m
2. ✅ The model is **validated** through 7 independent tests (mass balance, limiting cases, monotonicity, NTU/HTU, BVP convergence, physical bounds)
3. ✅ An **electrochemical cell** with Nernst-Planck membrane transport achieves SEC = 149 kJ/mol — **9% below theoretical benchmark**
4. ✅ The **integrated system** captures 213 kg CO₂/yr at 26.7 W continuous power
5. ✅ **Sensitivity analysis** identifies electrode gap and current density as dominant EC cell parameters; NaOH concentration (1–2 M) as the absorber optimum

### Economic Conclusions

6. ✅ Lab-scale CAPEX = **$6,542** — accessible for pilot demonstration
7. ✅ Breakeven at **2× scale** (0.42 t CO₂/yr) in Pakistan food-grade CO₂ market
8. ✅ At 100× scale: LCOC = **$1,584/ton**, payback = **1.4 years**, NPV = +$924k

### Thesis Contribution

> This thesis provides the **first fully integrated, first-principles model of an electrochemical NaOH-based DAC system**, spanning from molecular-level mass transfer correlations to project-level financial analysis, with a novel application to the Pakistan market context.

---

## SLIDE 25 — Future Work

### Short-Term (Next 6–12 Months)

1. **Experimental validation** — Build a bench-scale absorber (Z = 1–2 m) and single-cell electrolyser to validate model predictions
2. **Dynamic absorber model** — Extend to transient operation for batch cycling with the EC cell
3. **Bipolar membrane electrodialysis (BPMED)** — Replace single CEM with BPMED stack to eliminate cathode H₂ evolution and reduce SEC

### Medium-Term (1–3 Years)

4. **Process intensification** — Investigate hollow-fiber membrane contactors as an alternative to packed columns
5. **Renewable integration** — Couple with solar PV for off-grid operation in Pakistan (Sindh/Balochistan)
6. **Life cycle assessment (LCA)** — Quantify cradle-to-gate carbon intensity (kg CO₂-eq / kg CO₂ captured)

### Long-Term (3–5 Years)

7. **Pilot plant** — 10× scale demonstration (2 t CO₂/yr) at a Pakistan industrial site
8. **Carbon credit certification** — Obtain CDR certification (Puro.earth, Verra) for Pakistani carbon market
9. **System optimization** — Multi-objective optimization (cost, energy, recovery) using genetic algorithms

---

## SLIDE 26 — References

### Core Model References

1. Shu, Q. et al. (2020). "Electrochemical regeneration of spent alkaline absorbent from direct air capture." *Environ. Sci. Technol.* 54(14), 8990–8998.
2. Newman, J. & Thomas-Alyea, K. (2004). *Electrochemical Systems.* 3rd ed., Wiley.
3. Pintauro, P. & Bennion, D. (1984). "Mass transport of electrolytes in membranes." *Ind. Eng. Chem. Fundam.* 23(2), 230–234.
4. Pitzer, K. (1991). *Activity Coefficients in Electrolyte Solutions.* 2nd ed., CRC Press.

### Absorber Correlations

5. Billet, R. & Schultes, M. (1999). "Prediction of mass transfer columns with dumped and arranged packings." *Trans. IChemE* 77A, 498–504.
6. Onda, K. et al. (1968). "Mass transfer coefficients between gas and liquid phases in packed columns." *J. Chem. Eng. Jpn.* 1(1), 56–62.
7. Pohorecki, R. & Moniuk, W. (2001). "Kinetics of reaction between CO₂ and hydroxyl ions." *Chem. Eng. Sci.* 43(7), 1677–1684.
8. Sander, R. (2015). "Compilation of Henry's law constants for water as solvent." *Atmos. Chem. Phys.* 15, 4399–4981.

### Economic Data (All March 2026)

9. NEPRA / ProPakistani (2026) — Pakistan industrial electricity tariff
10. Kitco (2026) — Platinum spot price
11. IndexBox (2023/2026) — Pakistan CO₂ import price
12. Sylvera / Regreener (2026) — Carbon credit pricing

---

## SLIDE 27 — Thank You & Q&A

### Thank You

*Thank you for your time and attention.*

**Questions?**

---

### Supplementary Material Available

- Full source code: 5 Jupyter notebooks
- Raw simulation data: contour grids, sensitivity sweeps
- 16-panel diagnostic plots for absorber and EC cell
- Complete stream tables (S1–S6) and equipment specs

### Contact

[Your Email] | [University / Department]

---

## APPENDIX A — Nomenclature

| Symbol | Definition | Unit |
|--------|-----------|------|
| a_p | Packing specific area | m²/m³ |
| a_w | Wetted packing area | m²/m³ |
| C_DIC | Dissolved inorganic carbon | mol/L |
| D_CO₂ | CO₂ diffusivity (liquid) | m²/s |
| E | Enhancement factor | — |
| E_cell | Cell voltage | V |
| E_rev | Reversible potential | V |
| F | Faraday constant | 96,485 C/mol |
| G_inert | Inert gas molar flux | mol/m²/s |
| Ha | Hatta number | — |
| H_eff | Effective Henry's law constant | mol/L/atm |
| HTU | Height of transfer unit | m |
| j | Current density | A/m² |
| k_G | Gas-film MTC | mol/m²/s/atm |
| k_L | Liquid-film MTC | m/s |
| K_G | Overall gas-phase MTC | mol/m²/s/atm |
| k_OH | OH⁻ reaction rate constant | L/mol/s |
| N_CO₂ | Local CO₂ flux | mol/m²/s |
| NTU | Number of transfer units | — |
| R | Gas constant | 8.314 J/mol/K |
| SEC | Specific energy consumption | kJ/mol |
| STY | Space-time yield | mol/m³/s |
| η | Capture efficiency | % |
| η_F | Faradaic efficiency | — |

---

## APPENDIX B — Packing Database

| Packing | Type | a_p [m²/m³] | ε | d_p [m] | C_BG | C_BL | σ_c [N/m] |
|---------|------|------------|---|---------|------|------|-----------|
| Mellapak 125Y | Structured | 125 | 0.97 | 0.032 | 0.0338 | 1.334 | 0.075 |
| **Mellapak 250Y** | **Structured** | **250** | **0.97** | **0.016** | **0.0338** | **1.334** | **0.075** |
| Mellapak 500Y | Structured | 500 | 0.97 | 0.008 | 0.0338 | 1.334 | 0.075 |
| Sulzer BX | Structured | 492 | 0.90 | 0.017 | 0.0350 | 1.400 | 0.075 |
| Pall 25 mm | Random | 220 | 0.94 | 0.025 | 0.0410 | 1.440 | 0.075 |
| Pall 50 mm | Random | 112 | 0.95 | 0.050 | 0.0410 | 1.440 | 0.075 |

---

## APPENDIX C — Stream Table (Base Case)

| Stream | Description | T [°C] | V [L] | Key Species |
|--------|-------------|--------|-------|-------------|
| S1 | Acid Feed | 20.0 | 199.2 | 0.95 M Na₂CO₃, 0.10 M NaOH |
| S2 | Base Seed | 20.0 | 200.6 | 0.01 M NaOH |
| S3 | Acid Out | 21.3 | 171.1 | 0.04 M DIC, 0.002 M Na⁺ |
| S4 | NaOH Product | 21.3 | 214.5 | 1.72 M NaOH, 1.87 M Na⁺ |
| S5 | CO₂ Product | 21.3 | 4,429* | 183.3 mol CO₂ (8,067 g) |
| S6 | H₂ Recycle | 21.3 | 4,439* | 183.7 mol H₂ (370 g) |

*Gas volumes at T, P

---

## APPENDIX D — Full CAPEX Item List

| Item | Qty | Unit | $/unit | $ Total | Imported? |
|------|-----|------|--------|---------|-----------|
| Absorber Shell (SS316) | 331 kg | kg | $3.4 | $1,124 | No |
| Mellapak 250Y Packing | 0.99 m³ | m³ | $730 | $723 | Yes |
| Liquid Distributor | 1 | unit | $200 | $200 | No |
| Mist Eliminator | 1 | unit | $100 | $100 | No |
| Support Grid | 1 | unit | $80 | $80 | No |
| Nafion 117 Membranes | 0.35 m² | m² | $1,500 | $525 | Yes |
| Pt Catalyst | 0.20 g | g | $63 | $13 | Yes |
| Ti Mesh Electrodes | 1.8 kg | kg | $15 | $27 | Yes |
| Bipolar Plates | 6 | units | $20 | $120 | No |
| Gaskets & Hardware | 1 | set | $60 | $60 | No |
| End Plates (steel) | 28 kg | kg | $3.4 | $95 | No |
| Stack Frame | 1 | unit | $80 | $80 | No |
| HDPE Tanks (×3) | 3 | units | $18 | $54 | No |
| Mag-drive Pumps (×3) | 3 | units | $150 | $450 | No |
| DC PSU (50W) | 1 | unit | $80 | $80 | Yes |
| Gas Separator (membrane) | 1 | unit | $350 | $350 | Yes |
| Piping & Manifold | 1 | set | $120 | $120 | No |
| Valves & Instruments | 1 | set | $300 | $300 | No |
| Blower (35 m³/h) | 1 | unit | $150 | $150 | No |
| Control Panel / PLC | 1 | unit | $250 | $250 | No |
| Wiring & Electrical | 1 | set | $100 | $100 | No |
| Site Preparation | 1 | unit | $100 | $100 | — |
| Installation Labor | 1 | unit | $164 | $164 | — |
| Commissioning | 1 | unit | $200 | $200 | — |
| Import Duties (15%) | — | — | — | $258 | — |
| Contingency (15%) | — | — | — | $820 | — |
| **TOTAL** | | | | **$6,542** | |

---

*End of Presentation*
