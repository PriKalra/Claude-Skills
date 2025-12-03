# Pharma Publication Reader - Quick Reference Card

## Quick Start
1. Upload publication (PDF or text)
2. Say: "Extract modeling data from this publication"
3. Answer 3 questions about your modeling goal
4. Receive structured extraction + model-ready parameters

## Trigger Phrases
- "Extract modeling data"
- "Summarize for PBPK modeling"
- "Get PK parameters"
- "Analyze for model development"
- "Extract [specific parameter] data"

## The 3 Key Questions
1. **Model type?** → PBPK, PK/PD, QSP, PopPK, PBTK
2. **Modeling objective?** → DDI, pediatric, formulation, tissue distribution
3. **Species/system?** → Human, animal (specify), in vitro, IVIVE

## Common Modeling Scenarios

### Human PK Prediction
**Ask for**: MW, LogP, pKa, fu,plasma, animal PK, CLint, scaling factors

### Drug-Drug Interaction
**Ask for**: CYP isoforms, fm values, Ki/IC50, perpetrator & victim PK

### Pediatric Dosing
**Ask for**: Adult PK, enzyme ontogeny, age-related physiology, safety margins

### Formulation Performance
**Ask for**: Dissolution profiles, particle size, solubility, BA by formulation

### Tissue Penetration
**Ask for**: Kp values, transporter data, tissue binding, tissue:plasma ratios

## What Gets Extracted

### Category A: Physicochemical
MW, LogP, pKa, solubility, permeability (Caco-2), fu,plasma, B/P ratio

### Category B: ADME/DMPK
**Absorption**: F%, Tmax, Cmax, ka, transporter data  
**Distribution**: Vd, Kp values, BBB penetration, protein binding  
**Metabolism**: CL values, CYP pathways, CLint, Vmax/Km, metabolites  
**Excretion**: CLr, biliary excretion, urinary/fecal recovery

### Category C: Target Product Profile
Formulation type, dose, route, frequency, dosing duration, indication

### Category D: Study Design
Study type, n=, demographics, dosing protocol, sampling times, analytical method

### Category E: Systemic vs Local
AUC, Cmax, Css, tissue concentrations, effect site, BBB/tumor penetration

### Category F: Figures & Tables
All quantitative data with sources (table/figure #, page #)

## Output Structure
1. Modeling Problem Statement
2. Publication Metadata
3. Executive Summary (2-3 sentences)
4. Extracted Data (Categories A-F)
5. Synthesis & Insights
6. Model-Ready Parameter Table
7. Source References

## Key Features

✓ **Problem-driven** - Prioritizes data based on YOUR modeling goal  
✓ **Traceable** - Every parameter linked to source (table/fig, page)  
✓ **Quantitative** - Includes units, variability, experimental conditions  
✓ **Quality-assessed** - Flags high/medium/low confidence data  
✓ **Gap-identified** - Notes missing parameters + suggests alternatives  
✓ **Model-ready** - Parameters formatted for direct software input

## Parameter Ranges (Quick Check)

| Parameter | Typical Range | Red Flags |
|-----------|--------------|-----------|
| MW | 150-900 g/mol | <100 or >1500 |
| LogP | -2 to 6 | >7 unusual |
| fu,plasma | 0.001-1.0 | >1.0 or negative |
| CL (human) | 0.01-20 mL/min/kg | >20 (exceeds blood flow) |
| Vd | 0.05-20 L/kg | <0.05 (too small) |
| F | 0-1.0 (0-100%) | >100% impossible |
| t½ | Minutes to days | Verify with Vd & CL |

## Platform-Specific Essentials

**GastroPlus**: MW, LogP, pKa, solubility, dose, formulation type  
**Simcyp**: + CYP fm values, population data, transporter kinetics  
**PK-Sim**: + Kp values, organ CLint, physiology-based parameters  
**NONMEM**: Dataset structure (ID, TIME, DV, AMT, EVID, CMT)

## Advanced Queries

**For specific data**: "Are there any [parameter] values in this paper?"  
**For references**: "What are typical ranges for [parameter]?"  
**For formats**: "What format does [software] need for [parameter]?"  
**For validation**: "Check if these parameters are consistent"

## Common Unit Conversions
- 1 mg/L = 1 μg/mL = 1000 ng/mL
- 1 L/h/kg = 16.67 mL/min/kg
- nM = (ng/mL) × 1000 / MW
- μM = (μg/mL) × 1000 / MW

## Data Quality Flags
🟢 **High**: Direct measurements, low variability, clear methods  
🟡 **Medium**: Derived values, limited replicates, some assumptions  
🔴 **Low**: Approximations, scaling needed, high uncertainty  
⚫ **Missing**: Not reported, literature search needed

## Tips for Best Results

1. **Be specific** about modeling objective upfront
2. **Upload clear** PDFs (not scanned images if possible)
3. **Ask follow-up** questions for specific parameters
4. **Cross-reference** critical values in source tables
5. **Document** assumptions for data gaps
6. **Use repeatedly** across multiple related publications

## Quick Troubleshooting

**Not triggering?** → Use explicit phrase "extract modeling data"  
**Too broad?** → Be more specific about modeling problem  
**Missing data?** → Ask "Any [specific parameter] mentioned?"  
**Wrong units?** → Request standardization to preferred units  
**Need format?** → Ask for platform-specific requirements

## Reference Materials Included

📚 **parameter_ranges.md** - Typical ranges, units, validation checks  
📚 **platform_requirements.md** - Software-specific requirements, formats

Ask Claude to reference these anytime: "What does [software] require?" or "What's the typical range for [parameter]?"

---

**Pro Tip**: Save your most common modeling scenarios as templates. Claude can learn your preferred extraction patterns and apply them consistently across publications.

**Remember**: The skill makes Claude your specialized modeling assistant. Clear communication of your objective = more targeted, useful extraction.
