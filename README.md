# Claude-Skills
this has scientific skills for claude
# Pharmaceutical Modeling Skills Suite

## Overview

This suite contains two complementary skills designed for pharmaceutical scientists working with PBPK modeling, QSP modeling, pharmacometrics, and toxicology assessments.

---

## Skill 1: PBPK Modelling Scientist

**[Download: pbpk-modelling.skill](computer:///mnt/user-data/outputs/pbpk-modelling.skill)**

### Purpose
Core PBPK modeling skill providing comprehensive workflows, equations, parameters, and simulations for physiologically-based pharmacokinetic modeling.

### Use When:
- Building PBPK models in GastroPlus, Simcyp, or custom frameworks
- Parameterizing absorption (ADAM/ACAT) models
- Performing DDI predictions
- Scaling to special populations (pediatric, geriatric, organ impairment)
- Conducting virtual population simulations
- Preparing regulatory submissions

### What's Included:

**SKILL.md - Core Guidance:**
- Complete PBPK modeling workflow (9 steps from model development to validation)
- Absorption modeling (ADAM, ACAT, formulation effects)
- Distribution modeling (perfusion vs. permeability-limited)
- Clearance modeling (hepatic, renal, biliary)
- DDI predictions (perpetrator and victim assessment)
- Special populations (pediatric ontogeny, geriatric changes, organ impairment)
- Virtual population simulations
- Software-specific guidance (GastroPlus, Simcyp, Python/R)
- Common pitfalls and troubleshooting

**References:**
1. **pbpk_equations.md** (2000+ lines) - Mathematical equations:
   - Partition coefficient methods (Poulin-Theil, Rodgers-Rowland, Berezhkovskiy)
   - Hepatic clearance models (well-stirred, parallel tube, dispersion)
   - Renal clearance equations
   - Absorption models (first-order, zero-order, transit compartments)
   - DDI prediction equations (static and dynamic PBPK)
   - Scaling factors (allometric, ontogeny, organ impairment)
   - PK metrics calculations

2. **parameter_values.md** (1500+ lines) - Reference data:
   - Physiological parameters (organ weights, blood flows, tissue composition)
   - CYP450 and UGT enzyme abundances
   - Transporter expression levels
   - IVIVE scaling factors (MPPGL, HPGL)
   - Age-related changes (pediatric, geriatric)
   - Drug property ranges
   - Population variability estimates

**Scripts:**
- **pbpk_simulator.py** - Functional Python PBPK model with ODE solver, PK calculations, and visualization

**Assets:**
- **model_report_template.md** - Comprehensive documentation template for regulatory submissions

### Key Features:
✓ Regulatory-focused (FDA/EMA guidance compliant)
✓ Platform-agnostic (works with commercial and open-source tools)
✓ Comprehensive equation library
✓ Reference parameter values with validation ranges
✓ Ready-to-use Python simulation code

---

## Skill 2: Pharmaceutical Literature Extraction

**[Download: pharma-literature-extraction.skill](computer:///mnt/user-data/outputs/pharma-literature-extraction.skill)**

### Purpose
Expert system for extracting ADME, PK/PD, toxicology, and mechanistic data from scientific literature with human-in-the-loop guidance, especially for figures and graphs.

### Use When:
- Extracting parameters from research papers for modeling
- Building compound databases from literature
- Preparing model input files
- Conducting literature reviews for PBPK/QSP
- Validating model predictions against clinical data
- Supporting regulatory submissions with literature evidence

### What's Included:

**SKILL.md - Extraction Workflow:**
- Expert role integration (Industrial Toxicologist, PBPK/QSP Scientist, Clinical Pharmacologist)
- Decision tree workflow with human consultation at every step
- **Human-in-the-loop protocol for figure extraction**
- Structured extraction templates for all ADME parameters:
  * Absorption (solubility, permeability, fa)
  * Distribution (Vd, fu, Kp, B/P ratio)
  * Metabolism (CLint, Km/Vmax, Ki, fm, metabolites)
  * Excretion (CLr, fe, biliary)
  * Clinical PK (AUC, Cmax, t½, PopPK parameters, covariates)
  * Toxicology (in vitro IC50, in vivo NOAEL, clinical safety)
- **Detailed figure handling protocols:**
  * Assessment and consultation with user
  * Qualitative vs. quantitative extraction decision tree
  * WebPlotDigitizer guidance
  * Data validation after digitization
- Data validation and gap analysis
- Multiple output formats (Excel, JSON, model-ready templates)
- Quality verification checklists

**References:**
1. **expected_ranges.md** (2500+ lines) - Validation ranges for:
   - All ADME parameters (physicochemical, absorption, distribution, metabolism, excretion)
   - Clinical PK metrics
   - Toxicology endpoints
   - Cross-validation rules
   - Species scaling factors
   - Confidence flagging system (✓/⚠️/✗)

2. **unit_conversions.md** (1500+ lines) - Comprehensive conversions:
   - Mass ↔ molar concentration
   - Clearance units
   - Volume units
   - Permeability units
   - AUC units
   - Metabolic parameters
   - pH-dependent calculations
   - Species scaling

**Scripts:**
- **validate_parameters.py** - Automated parameter validation with range checking and consistency verification

**Assets:**
- **webplotdigitizer_guide.md** - Complete step-by-step guide for extracting data from figures with screenshots, troubleshooting, and quality control

### Key Features:
✓ **Human-in-the-loop design** - Always consults user before extracting figures or complex data
✓ **Figure extraction expertise** - Comprehensive WebPlotDigitizer guidance
✓ Multi-domain coverage (PBPK, QSP, PopPK, Toxicology)
✓ Automated validation against expected ranges
✓ Source traceability and confidence scoring
✓ Platform-specific output formatting (GastroPlus, Simcyp, DILIsym, Monolix)
✓ Gap analysis and recommendations
✓ Model readiness assessment

---

## How These Skills Work Together

### Integrated Workflow:

```
LITERATURE PAPER
       ↓
[Pharma Literature Extraction Skill]
  - Extracts all ADME parameters
  - Validates against expected ranges
  - Handles figure digitization with user
  - Identifies gaps
       ↓
STRUCTURED PARAMETER DATASET
       ↓
[PBPK Modelling Skill]
  - Uses extracted parameters
  - Applies equations and scaling
  - Builds complete PBPK model
  - Validates predictions
  - Generates regulatory documentation
       ↓
QUALIFIED PBPK MODEL
```

### Example Use Case:

**Scenario:** You have a Phase 1 clinical trial paper and want to build a PBPK model for dose selection in pediatrics.

**Step 1: Literature Extraction**
1. Load pharma-literature-extraction skill
2. Provide paper to Claude
3. Claude scans and presents: "I found Tables 2-4 with PK data, Figure 3 with concentration-time curves..."
4. **You decide:** "Digitize Figure 3 for precise extraction"
5. Claude guides you through WebPlotDigitizer
6. You share digitized CSV
7. Claude validates, formats, and identifies gaps: "Missing metabolic pathway data"

**Step 2: PBPK Modeling**
1. Load pbpk-modelling skill
2. Use extracted parameters from Step 1
3. Claude helps parameterize full PBPK model
4. Apply pediatric ontogeny functions
5. Run simulations for different age groups
6. Generate model report using template

---

## Installation

Both skills are packaged as `.skill` files compatible with Claude's desktop/web interface (when skill upload is available).

**To use now:**
The comprehensive extraction templates, equations, and guidance from these skills can be referenced directly in conversations with Claude by describing your needs.

---

## Key Differences Between Skills

| Feature | PBPK Modelling | Literature Extraction |
|---------|---------------|---------------------|
| **Primary Use** | Building models | Getting data for models |
| **Main Output** | Simulations, predictions | Structured datasets |
| **User Interaction** | Consultative | Highly interactive (figure extraction) |
| **Content Type** | Equations, methods, workflows | Templates, validation, protocols |
| **When to Use** | Model development phase | Data collection phase |
| **Regulatory Focus** | Model documentation | Data traceability |

---

## Advanced Features

### Literature Extraction Skill

**Human-in-the-Loop Figure Handling:**
- Never extracts figures without explicit user guidance
- Offers 3 options for every figure:
  1. Qualitative description (trends only)
  2. Digitization guidance (step-by-step WebPlotDigitizer)
  3. Visible values only (if labeled)
- Validates digitized data against paper's reported values
- Detects and flags extraction errors automatically

**Multi-Domain Expertise:**
- Industrial toxicology parameters (IC50, NOAEL, DILI markers)
- QSP-specific parameters (mitochondrial toxicity, BSEP inhibition)
- Clinical pharmacology (PopPK parameters, covariates, variability)
- Comprehensive ADME (all standard parameters)

**Quality Control:**
- Automated range validation (✓/⚠️/✗ flagging)
- Internal consistency checks (e.g., CL = Dose/AUC)
- Cross-species plausibility
- Unit conversion verification
- Source traceability for every value

### PBPK Modelling Skill

**Comprehensive Workflows:**
- 9-step model development process
- Decision trees for model selection
- Parameterization hierarchy (measured → estimated → optimized)
- Validation and qualification protocols

**Software Coverage:**
- GastroPlus workflows
- Simcyp population libraries
- Python/R implementations
- Platform-agnostic principles

**Special Populations:**
- Pediatric ontogeny functions (CYP maturation)
- Geriatric physiological changes
- Renal/hepatic impairment scaling
- Allometric scaling across species

---

## Best Practices

### For Literature Extraction:

1. **Always Review Figures Together:** Let Claude identify figures, then decide extraction method
2. **Validate Extracted Data:** Use provided ranges and consistency checks
3. **Document Assumptions:** Note when predictions used vs. measured values
4. **Check Source Traceability:** Every parameter should link to table/figure/page
5. **Use Confidence Levels:** High/Medium/Low flags guide model decisions

### For PBPK Modeling:

1. **Start with Parameter Assessment:** Check what's available vs. required
2. **Follow Parameterization Hierarchy:** Measured > Estimated > Fitted
3. **Use Multiple Prediction Methods:** Compare Kp values from different methods
4. **Perform Sensitivity Analysis:** Identify influential parameters
5. **Validate Incrementally:** Simple model first, add complexity with validation

---

## Troubleshooting

### Literature Extraction Issues:

**Problem:** Claude extracted figure data without asking
- Make sure you're using the pharma-literature-extraction skill
- The skill is designed to ALWAYS consult before figure extraction

**Problem:** Parameter validation fails
- Check units carefully (common source of errors)
- Review expected_ranges.md for context
- Consider if compound has unusual properties

**Problem:** Missing critical parameters
- Use gap analysis recommendations
- Check if data is in supplementary materials
- Consider if prediction tools can fill gaps

### PBPK Modeling Issues:

**Problem:** Model predictions don't match observed data
- Check parameterization tier (measured vs. estimated)
- Verify units and conversions
- Review troubleshooting section in SKILL.md
- Check species scaling if using preclinical data

**Problem:** Unclear which equations to use
- Refer to pbpk_equations.md with full derivations
- Check software-specific guidance
- Compare to parameter_values.md for typical ranges

---

## Updates and Version History

### Version 1.0 (November 2025)
- Initial release of both skills
- Comprehensive PBPK modeling workflows
- Human-in-the-loop literature extraction
- Full equation library and parameter references
- WebPlotDigitizer integration
- Regulatory-compliant templates

---

## Support and Feedback

These skills were designed specifically for pharmaceutical modeling scientists with expertise in:
- PBPK modeling (GastroPlus, Simcyp, custom frameworks)
- QSP modeling (DILIsym, NAFLDsym)
- Pharmacometrics (NONMEM, Monolix)
- Industrial toxicology

For questions or suggestions about improving these skills, document your specific use cases and desired features.

---

## License and Usage

These skills are tools to enhance Claude's capabilities for pharmaceutical modeling work. They incorporate:
- Best practices from FDA/EMA regulatory guidance
- Standard equations from pharmacokinetic literature
- Typical parameter ranges from published sources
- Workflow patterns from industry experience

Always validate outputs and use professional judgment when applying to real-world drug development decisions.

---

## Quick Start Guide

**If you want to extract data from a paper:**
→ Use **pharma-literature-extraction.skill**
→ Expect interactive consultation on figures
→ Get structured, validated datasets

**If you want to build a PBPK model:**
→ Use **pbpk-modelling.skill**  
→ Reference equations and parameters
→ Follow regulatory-compliant workflows

**If you're doing both:**
→ Start with literature extraction to gather data
→ Then switch to PBPK modeling for simulations
→ Use both skills together for complete workflow

---

**Ready to start? Download the skills above and begin extracting or modeling!**
