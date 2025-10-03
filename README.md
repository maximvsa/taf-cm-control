Current Plan:

1. Define and articulate target route. Exact sequence of transforms, reagents, and protecting groups. Lock down IUPAC names, SMILES, InChI, stoichiometry, solvents, temperature, and catalysts of each step in reaction scheme. Store everything in a YAML file (route/route.yaml). (Programs: Python with ruamel.yaml for read/write; validation via pydantic schema in scripts/validate_route.py.)

2. Perform literature sanity check. Store precedent references and sources for each step in the YAML file (purely for reference; likely split off later). Add typical yields, selectivity, and flow-compatibility comments from literature for each step. Add status: verified | unconfirmed for each step after notes are gathered.

3. Perform atom mapping. Get robust atom maps for each step in the reaction scheme. Use one mapper as primary and another to cross-reference. Modify the step and re-perform the literature sanity check for any step where mapping disagrees with the mechanism. Save mapped reactions as .rxn. (Programs: rxnmapper as primary; Indigo Toolkit automapper as secondary; checks with CGRtools’ mapping validators; all orchestrated by scripts/map_steps.py.)

4. Draw mechanisms manually in ChemDraw, as "testable hypotheses," and for each step, label electron sources and electron sinks and identify potential side reactions. Save clean vector files. (Programs: ChemDraw or MarvinSketch to produce mechanisms/step_xx_mech.svg and mechanisms/step_xx_notes.md from scanned sketches or native files.)

5. Formally encode reactions as machine-readable rules. Store RXN/SMARTS templates or mapped RXN files in the project for future generation of graphics and deltas. (Programs: RDKit to author rules/Sxx_template.smarts and to canonicalize SMILES; CGRtools to read/write mapped .rxn.)

6. Build verification script. For each step, the script shall check mass/charge/valence balance, check atom-map bijection and conserved substructures, and optionally calculate ΔG estimators. Modify and reverify any step that fails verification. (Programs: Python scripts/verify_route.py using RDKit + CGRtools; optional thermochemistry via xTB (GFN2-xTB) through subprocess when --dg flag is passed.)

7. Implement CGRtools graphing. From mapped reactions, generate and serialize condensed graphs of reactions (CGRs). Keep both “difference” visualizations and numeric descriptors. (Programs: CGRtools to create *.cgr.json and RDKit/CGRtools to render figures/cgr/*.svg; driver script scripts/build_cgr.py.)

8. Depict the network. For each step, generate an SVG panel with RDKit MolDraw2D, then assemble an interactive or paginated layout: graph the backbone with NetworkX/Graphviz to place nodes/edges; each node/edge links to the per-step SVG; export the large poster only at the end (giant monoliths are a pain to iterate). (Programs: RDKit MolDraw2D → figures/steps/*.svg; NetworkX + Graphviz/dot or pygraphviz → figures/network/route.svg and route.html; driver script scripts/build_network.py.)

9. Depict mechanistic arrows and electron flow. Use a chemical editor for curly arrows (ChemDraw/Marvin/ACD). Export transparent SVG overlays and layer them over the RDKit drawings. Keep arrow artwork separate so re-renders don’t nuke it. (Programs: ChemDraw/Marvin to export figures/arrows/step_xx_arrows.svg; compositing handled by scripts/compose_svgs.py.)

10. Create coherent figure assembly pipeline. Create a Makefile that reads route/route.yaml, validates, generates mapped RXN, CGR, per-step SVGs, the network diagram, and the poster PDF. Targets: env, verify, cgr, steps, network, poster, all. (Programs: GNU Make; Python scripts above; Pandoc + LaTeX engine for final PDF poster assembly.)

11. Show the work. Create a formal report (PDF) summarizing each step’s conditions, mechanism sketch, mapping checksum, CGR image, and literature notes/yield assumptions (it's a receipt for reviewers). (Programs: Python scripts/make_report.py to write Markdown; Pandoc to render reports/route_report.pdf.)

Plan Notes:

Store reagents and conditions for each step in the .yaml → /route/route.yaml (Programs: Python export script).

Store atom maps in .rxn files → /maps/*.rxn and record mapper provenance and versions → /maps/PROVENANCE.md (Programs: rxnmapper, Indigo; Python wrapper).

Store CGRs in .json files → /cgr/*.cgr.json and CGR images → /figures/cgr/*.svg (Programs: CGRtools, RDKit).

Have /figures/steps/*.svg and /figures/network/{route.svg,route.html,positions.json} (Programs: RDKit, NetworkX, Graphviz).

Regularly update a notebook for validation (audit trails) → /notebooks/validation/*.ipynb (Programs: Jupyter, RDKit, CGRtools).

Mechanistic arrow overlays only in /figures/arrows/*.svg; never edit RDKit base SVGs (Programs: ChemDraw/Marvin).

Environment pinning in /env/conda-lock.yml with versions (Python 3.10.x, RDKit 2024.03.x, CGRtools 4.2.x, rxnmapper ≥0.3, Indigo Toolkit ≥1.5). Use mamba to create/lock.


And then, after all of that:

1. Create a process flow diagram of the route (add hemifumarate salting step).

2. Create a piping and instrumentation diagram of the entire thing.

3. Start developing the reactor model.





# (AI slop down below; to be edited later as project picks up and scope is refined)

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
