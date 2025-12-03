# Pharma Publication Reader Skill - Package Summary

## What You've Received

A complete, production-ready Claude skill for systematic extraction of pharmaceutical modeling data from scientific publications. This skill transforms Claude into a specialized assistant for PBPK, QSP, and pharmacokinetic modeling work.

## Package Contents

### Core Skill File
📦 **pharma-publication-reader.skill** (main deliverable)
- Ready to upload and use in Claude
- Contains all components bundled together
- Validated and packaged using official skill creator tools

### Documentation Files
📄 **USAGE_GUIDE.md** - Complete usage instructions with examples
📄 **QUICK_REFERENCE.md** - One-page quick reference card
📄 **This summary** - Overview of the package

### Included Inside the .skill File

#### 1. SKILL.md (Main Instructions)
The core skill instructions that guide Claude through:
- **Step 1**: Problem definition (3 key questions about modeling objective)
- **Step 2**: Systematic extraction across 6 categories:
  - A. Physicochemical characterization
  - B. ADME/DMPK data
  - C. Target product profile
  - D. Study design protocols
  - E. Systemic vs. local action
  - F. Figures and tables
- **Step 3**: Synthesis and modeling insights
- Output structure and best practices
- Common modeling scenarios with priorities

#### 2. Reference Materials (references/ folder)

**parameter_ranges.md** (comprehensive reference, ~500 lines)
- Typical ranges for all common PBPK/PK parameters
- Physicochemical, binding, absorption, distribution, clearance parameters
- Unit conversions and data quality validation checks
- Species-specific considerations and allometric scaling
- Interspecies extrapolation factors

**platform_requirements.md** (comprehensive reference, ~600 lines)
- Platform-specific parameter requirements for:
  - GastroPlus / ADMET Predictor
  - Simcyp Simulator
  - PK-Sim / MoBi
  - NONMEM / Phoenix WinNonlin
  - Berkeley Madonna / MATLAB/Simulink
- Model-type specific extraction patterns
- Data format preferences for each platform
- Quality checks before model input

## Key Features

### 1. Problem-Driven Approach
Unlike generic extraction tools, this skill:
- Always starts by understanding YOUR specific modeling objective
- Tailors extraction priorities based on your needs
- Distinguishes between model inputs vs. validation data
- Provides modeling-specific synthesis, not just data lists

### 2. Systematic & Comprehensive
Covers all aspects a modeling scientist needs:
- Complete physicochemical characterization
- Full ADME profile with all sub-categories
- Formulation and administration details
- Study design for model validation
- Exposure metrics (systemic and local)
- Visual data from figures/tables

### 3. Traceable & Quantitative
Every extracted parameter includes:
- Value with units and variability (SD, SE, range)
- Source reference (table/figure number, page)
- Experimental conditions (species, dose, method)
- Calculation method where applicable

### 4. Quality-Assessed
Provides data confidence levels:
- **High**: Direct measurements, low variability
- **Medium**: Derived values, limited replicates  
- **Low**: Approximations, scaling needed
- **Missing**: Not reported (with suggestions)

### 5. Model-Ready Output
Delivers:
- Parameter table formatted for direct software input
- Identified data gaps with recommendations
- Suggestions for additional literature or predictions
- Validation strategies based on available data

## How This Skill Works

### The Workflow
```
User uploads publication
    ↓
Triggers skill with request phrase
    ↓
Claude asks 3 modeling problem questions
    ↓
Systematic extraction (6 categories)
    ↓
Synthesis + Model-ready parameter table
    ↓
Gap identification + Recommendations
```

### Progressive Disclosure Design
The skill uses efficient context management:
1. **Metadata** (always loaded): Name + description trigger the skill
2. **SKILL.md** (loaded when triggered): Core workflow instructions
3. **References** (loaded as needed): Platform requirements and parameter ranges

Claude only loads what it needs, when it needs it, keeping the context window efficient.

## Design Philosophy

### Built for Real Modeling Work
This skill was designed with deep understanding of:
- PBPK model development workflows
- Different modeling platforms and their requirements
- Common data gaps and how to address them
- The difference between parameter types (input vs. validation)
- The importance of units, variability, and traceability

### Follows Best Practices
The skill follows Anthropic's official skill creation guidelines:
- Concise instructions (challenges each sentence's token cost)
- Appropriate degrees of freedom (guidance where needed, flexibility where appropriate)
- Progressive disclosure (efficient context management)
- No extraneous files (only essential components)
- Validated and packaged using official tools

### Extensible Design
The reference materials can be extended:
- Add your organization's specific parameter ranges
- Include company-specific modeling standards
- Add more platform-specific requirements
- Include historical compound data patterns

## Common Use Cases

### 1. First-in-Human Dose Prediction
Extract preclinical PK data, scale to human predictions
**Priority**: MW, LogP, fu,plasma, animal PK, CLint, allometric factors

### 2. Drug-Drug Interaction Assessment
Identify mechanisms and quantify interaction potential
**Priority**: CYP isoforms, Ki/IC50, fm values, perpetrator PK

### 3. Pediatric Dose Selection
Extrapolate from adult PK to pediatric populations
**Priority**: Adult PK, enzyme ontogeny, age-dependent physiology

### 4. Formulation Performance
Compare bioavailability across formulations
**Priority**: Dissolution profiles, particle size, solubility, BA comparison

### 5. Special Population PK
Model hepatic/renal impairment effects
**Priority**: Elimination pathways, organ function markers, PK in impaired

### 6. Tissue Penetration
Predict drug concentrations at effect site
**Priority**: Kp values, transporter data, tissue binding, BBB penetration

## Installation & Usage

### Installation (Simple)
1. Open Claude (web or app)
2. Upload `pharma-publication-reader.skill` file
3. Skill is automatically available

### Basic Usage
```
1. Upload publication (PDF or document)
2. Say: "Extract modeling data from this publication"
3. Answer the 3 modeling problem questions
4. Receive structured extraction
```

### Advanced Usage
- Use across multiple publications for comprehensive parameter compilation
- Ask specific questions: "Are there any Kp,brain values?"
- Request platform-specific formats: "Format this for GastroPlus input"
- Validate parameters: "Check if these values are in typical ranges"

## Technical Details

### Skill Structure
```
pharma-publication-reader.skill (ZIP archive with .skill extension)
│
├── SKILL.md (Main instructions)
└── references/
    ├── parameter_ranges.md
    └── platform_requirements.md
```

### Size & Scope
- **SKILL.md**: ~450 lines of instructions
- **parameter_ranges.md**: ~500 lines of reference data
- **platform_requirements.md**: ~600 lines of platform-specific guidance
- **Total**: Comprehensive but efficiently designed (~1550 lines total)

### Validation
✅ Passed official skill validator checks:
- Proper YAML frontmatter (name + description)
- Valid directory structure
- No extraneous files
- Clear triggering description
- Appropriate resource organization

## Advantages Over Manual Extraction

| Manual Approach | With This Skill |
|----------------|-----------------|
| Inconsistent extraction | Systematic 6-category framework |
| Easy to miss parameters | Comprehensive checklist |
| No prioritization | Model-problem driven priorities |
| Time-consuming | Streamlined workflow |
| Hard to trace sources | Every value has source reference |
| No quality assessment | Built-in confidence rating |
| Format varies | Consistent model-ready output |
| Data gaps unclear | Explicit gap identification + suggestions |

## Future Enhancements (Possible)

If you find additional needs, the skill can be extended to include:
- More platform-specific requirements (e.g., additional modeling software)
- Species-specific parameter libraries
- Therapeutic area-specific extraction patterns (oncology, CNS, etc.)
- Integration with compound database schemas
- Automated unit standardization rules
- Parameter correlation checks (e.g., CL vs. hepatic blood flow)

## Sharing with Team

This skill can be shared with colleagues:
1. Send them the `pharma-publication-reader.skill` file
2. They upload it to their Claude environment
3. Each person can use independently
4. Maintains consistency across team's modeling work

## Support & Questions

To use the skill effectively:
1. Read the **USAGE_GUIDE.md** for complete instructions
2. Keep **QUICK_REFERENCE.md** handy for common scenarios
3. Ask Claude to explain any aspect of extraction criteria
4. Request clarification on parameter interpretations anytime

The skill is designed to be self-explanatory through its instructions, but Claude can always explain its reasoning and methods.

## Version Information

**Version**: 1.0  
**Release Date**: November 2025  
**Created For**: PBPK, QSP, and pharmacokinetic modeling scientists  
**Tested With**: Claude Sonnet 4.5  
**Validated**: Using Anthropic's official skill-creator tools  

## Acknowledgments

This skill incorporates best practices from:
- PBPK modeling workflows (GastroPlus, Simcyp, PK-Sim)
- Pharmaceutical ADME sciences
- Regulatory modeling guidelines (FDA, EMA)
- Quantitative systems pharmacology practices
- Population pharmacokinetic analysis standards

---

## Getting Started Now

**Immediate Next Steps:**

1. ✅ Upload `pharma-publication-reader.skill` to Claude
2. ✅ Open any scientific publication you're working with
3. ✅ Say "Extract modeling data from this publication"
4. ✅ Answer the 3 questions about your modeling goal
5. ✅ Receive your first systematic extraction!

**Keep Handy:**
- USAGE_GUIDE.md for detailed instructions
- QUICK_REFERENCE.md for common scenarios

**Remember:**
The more clearly you communicate your modeling objective, the more targeted and valuable the extraction will be. This skill makes Claude your specialized pharmaceutical modeling assistant.

---

🎉 **You're ready to transform your publication reading workflow!**
