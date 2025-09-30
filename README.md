# Plant-wide Constrained Control of a Minimal Continuous Tenofovir Alafenamide Hemifumarate Synthesis: A Simulation Study

# Problem Statement
Design and test a plant-wide control strategy for a minimal, continuous manufacturing train that converts inexpensive, stable starting materials into crystalline, non-solvated tenofovir alafenamide (TAF) hemifumarate (2:1). The process must robustly deliver the intended salt stoichiometry and crystal form under realistic disturbances and constraints, while meeting release specifications and minimizing solvent and energy usage.

# Target Material
Drug substance: Tenofovir alafenamide hemifumarate (2:1 TAF:fumaric acid).

Solid form: Non-solvated crystalline polymorph (single, predefined lattice form).

Analytical identifiers to confirm the form:

XRPD peaks at 2θ = 6.9 ± 0.2°, 8.6 ± 0.2°, 11.0 ± 0.2°, 15.9 ± 0.2°, 20.2 ± 0.2°

DSC onset ~131 ± 2 °C (single melt), no solvent loss event

Orthogonal ID by Raman/IR; optional confirmation by solid-state NMR

Form-purity criterion: ≥ 99.0% target polymorph (quantitative XRPD/Raman).

Stoichiometry impurity: Monofumarate ≤ 0.5% w/w (non-detect preferred).

No solvate/hydrate detected by TGA/Karl Fischer within method LOQ.

# Scope

Reaction/workup section: telescoped as needed to a neutral TAF base stream with controlled impurity profile.

Salt formation: fumaric acid addition at a 2:1 (TAF:fumaric) charge ratio window; tight control around 0.50 acid equivalents.

Crystallization: seeded, low-supersaturation operation (e.g., MSMPR cascade or PFR + hold) to grow the target polymorph and particle-size distribution.

Solid–liquid separation: continuous filtration or centrifugation.

Drying: vacuum/belt/fluid-bed dryer model tuned to avoid desolvation artifacts and form change.

Milling/classification: to final particle-size distribution without inducing amorphization or polymorph conversion.

Utilities/recycles: mother-liquor return, solvent recovery, and purge policies represented at flowsheet level.

Out-of-Scope (so reviewers don’t ding you)

Final tablet formulation and coating (optional appendix model only).

Clinical performance claims.

Any proprietary or patented route details not present in public literature (the chemistry is represented generically).

# Control Objectives

Right salt, right form, every hour: Maintain hemifumarate (2:1) and target polymorph ≥ 99% despite feed and utility disturbances.

Meet release specs: assay, impurity totals, residual solvents, moisture, and particle-size D10/D50/D90 within bands.

Honor constraints: temperature, pressure, composition, equipment torque/ΔP, dryer outlet LOD (loss-on-drying), and maximum milling energy.

Minimize pain: reduce solvent use, off-spec recycle, and time-to-steady-state after upsets.

# Measurables

Quality KPI: on-spec run fraction (time above spec) and first-pass yield.

Resilience KPI: worst-case deviation of polymorph purity and stoichiometry under ± disturbances; recovery time to spec after upset.

Efficiency KPI: solvent mass intensity, energy proxy (heater duty + dryer load), and off-spec recycle percentage.

Stability KPI: closed-loop robustness margins (gain/phase or Monte Carlo success rate).

# Disturbances To Be Simulated

Feed variability: ±5–10% in key starting-material purity; ±2–3% flow noise.

Utilities: ±3–5 °C jacket swings; vacuum level drift at dryer; intermittent filter blinding.

Operational: seed depletion/mismatch, antisolvent composition drift, small water ingress.

# State Estimation and Monitoring

Crystallizer inline identity/concentration: Raman (or ATR-FTIR) for polymorph ID and liquor concentration.

Particle size trend: chord-length or laser diffraction signal.

Dryer endpoint: NIR moisture or LOD model; thermogravimetric surrogate in simulation.

Observer: a moving-horizon estimator (or Kalman variant) fusing these signals with mass/energy balances to estimate unmeasured states (crystal hold-up, polymorph split, liquor composition).

# Control Structure

Regulatory layer: standard PID loops for flows/levels/temperatures/pressures.

Supervisory layer: a constrained optimizer (model predictive controller) that manipulates antisolvent rate, jacket temperatures, seed feed, and milling classifier setpoint to track:

polymorph purity ≥ setpoint,

supersaturation trajectory,

target particle-size median,

dryer outlet moisture ≤ spec,
while respecting equipment and safety limits.

Recipe logic: start-up, transition to steady seed, disturbance management, and safe shutdown.

# Specifications

Identity: matches reference XRPD and IR/Raman for non-solvated TAF hemifumarate (2:1).

Polymorph purity: ≥ 99.0%.

Stoichiometry: monofumarate ≤ 0.5% w/w.

Assay (as TAF base, anhydrous): 98.0–102.0%.

Impurities (process + related): set realistic total and single-max limits.

Residual solvents: within ICH-style limits.

Water content (LOD or KF): within limit (declare number).

Particle size: D10/D50/D90 bands supporting flow and compressibility.

# Assumptions

Reaction network and kinetic/thermodynamic parameters are sourced from public literature or anonymized surrogates; no replication of proprietary or claimed routes.

All trade names are used descriptively. The work is simulation-only and does not involve making, using, or selling patented compounds or processes.

# Primary Goal

Maximize on-spec production rate of non-solvated crystalline TAF hemifumarate (2:1)—as confirmed by the stated XRPD, DSC, and spectroscopic identifiers—while satisfying plant and safety constraints, minimizing solvent and energy use, and demonstrating robust operation against defined disturbances through a plant-wide constrained control strategy.
