# Pharma Publication Reader Skill - Usage Guide

## Overview

The **pharma-publication-reader** skill transforms Claude into a specialized assistant for pharmaceutical modeling scientists. It systematically extracts and synthesizes PBPK, QSP, and pharmacokinetic modeling parameters from scientific publications.

## Key Features

### 1. Problem-Driven Extraction
The skill begins by understanding your specific modeling objective, ensuring that extracted data is relevant and prioritized correctly.

### 2. Comprehensive Data Categories
Systematic extraction across six key areas:
- **Physicochemical characterization** (MW, LogP, pKa, solubility, permeability)
- **ADME/DMPK data** (absorption, distribution, metabolism, excretion parameters)
- **Target product profile** (formulation, dosing, administration details)
- **Study design protocols** (experimental setup, sampling strategies)
- **Systemic vs. local action** (exposure metrics, tissue distribution)
- **Figures and tables** (quantitative data extraction)

### 3. Modeling-Focused Synthesis
Beyond simple extraction, the skill provides:
- Model-ready parameter tables
- Data quality assessment (high/medium/low confidence)
- Identification of data gaps
- Recommendations for model development
- Literature gaps and suggestions


## How to Use

### Installation
1. Upload the `pharma-publication-reader.skill` file to Claude
2. The skill will be automatically available in your Claude environment

### Basic Workflow

**Step 1: Upload your publication**
Upload a PDF, text, or document containing the scientific publication you want to analyze.

**Step 2: Trigger the skill**
Use phrases like:
- "Extract modeling data from this publication"
- "Summarize this paper for PBPK model development"
- "Get PK parameters from this study"
- "Analyze this publication for modeling inputs"

**Step 3: Define your modeling problem**
Claude will ask you three critical questions:
1. What type of model are you developing? (PBPK, PK/PD, QSP, etc.)
2. What is your specific modeling question? (DDI, pediatric dosing, formulation, etc.)
3. What species/system are you modeling? (human, animal, in vitro)

**Step 4: Receive structured extraction**
Claude will systematically extract relevant data organized by category, with source references (table/figure numbers, page numbers).

**Step 5: Get modeling insights**
Receive a synthesis including:
- Model-ready parameter table
- Data quality assessment
- Recommendations for model development
- Identified gaps and suggestions

## Example Use Cases

### Use Case 1: Human PK Prediction from Preclinical Data
```
You: "Help me extract modeling data from this publication. I need to predict human PK."

Claude: [Triggers skill]
"What type of model are you developing?"
- PBPK model

"What is your specific modeling question?"
- Predicting human PK from preclinical (rat and dog) data

"What species/system are you modeling?"
- Scaling from rat and dog to human adult

Claude will then prioritize:
- MW, LogP, pKa, fu,plasma
- Animal PK parameters (CL, Vd, F)
- In vitro CLint data
- Allometric scaling factors
```

### Use Case 2: Drug-Drug Interaction Assessment
```
You: "I need to build a DDI model for this compound."

Claude will focus on:
- Specific CYP isoform identification and fm values
- Ki or IC50 values for enzyme inhibition
- Victim and perpetrator PK parameters
- Time-dependent inhibition data if available
```

### Use Case 3: Formulation Performance Model
```
You: "Extract data for modeling different formulation performance."

Claude will prioritize:
- Dissolution profiles for each formulation
- Particle size distributions
- Bioavailability comparisons
- Food effect data
- Solubility across pH range
```

## Advanced Features

### Reference Materials
The skill includes two comprehensive reference documents:

**parameter_ranges.md**
- Typical ranges for all common PBPK parameters
- Unit conversions
- Data quality validation checks
- Species-specific considerations

**platform_requirements.md**
- Platform-specific parameter requirements
- Model-type specific extraction patterns
- Data format preferences
- Quality checks before model input

### Accessing References
While using the skill, you can ask Claude to:
- "What's the typical range for LogP values?"
- "What parameters does GastroPlus require?"
- "Show me the required format for Simcyp compound files"

Claude will read from the reference materials to provide accurate, standardized guidance.

## Best Practices

### 1. Be Specific About Your Modeling Goal
The more specific you are about your modeling objective, the more targeted and useful the extraction will be.

✅ Good: "I'm developing a PBPK model to predict brain penetration in humans from rat data"
❌ Less good: "Extract all the data from this paper"

### 2. Review Multiple Related Papers
For comprehensive model development, use the skill on multiple publications:
- Primary PK studies
- Metabolism and excretion studies
- Special population studies (if relevant)
- Formulation comparison studies

### 3. Verify Critical Parameters
While the skill is designed to be accurate and traceable, always verify critical parameters by cross-referencing the source tables/figures indicated.

### 4. Document Assumptions
When the skill identifies data gaps and suggests literature values or predictions, document these as assumptions in your modeling report.

### 5. Use for Literature Reviews
Beyond single publications, you can use this skill to systematically review multiple papers and compile parameters across studies.

## Output Format

The skill provides structured output in this order:

1. **Modeling Problem Statement** - Your defined objective
2. **Publication Metadata** - Citation information
3. **Executive Summary** - Brief overview of relevance
4. **Extracted Data** - Organized by category (A-F)
5. **Synthesis and Insights** - Modeling-focused analysis
6. **Model Input Parameter Table** - Ready for software input
7. **References** - All source tables/figures with page numbers

## Tips for Different Publication Types

### Preclinical ADME Papers
- Focus on extraction of disposition parameters
- Note species-specific differences
- Extract allometric scaling data

### Clinical PK Studies
- Prioritize human PK parameters
- Extract population characteristics
- Note any special population data

### Formulation Studies
- Capture dissolution profiles completely
- Extract particle size distributions
- Note relative bioavailability between formulations

### DDI Studies
- Identify all perpetrator and victim drugs
- Extract mechanism-based inhibition data
- Note dosing schedules for interaction studies

### Methods Papers
- Focus on analytical method details (LLOQ)
- Extract validation data
- Note any unique sample processing

## Troubleshooting

**Issue**: Claude doesn't trigger the skill
**Solution**: Use explicit trigger phrases like "extract modeling data" or "analyze for PBPK modeling"

**Issue**: Too much irrelevant information extracted
**Solution**: Be more specific about your modeling problem in Step 3

**Issue**: Missing parameters you need
**Solution**: After initial extraction, ask "Are there any [specific parameter] values in the paper?" Claude will do a focused re-search

**Issue**: Units seem inconsistent
**Solution**: Ask Claude to verify and standardize units using the reference materials

## Sharing and Collaboration

### For Your Team
The skill can be shared with colleagues who also use Claude. Each person can:
- Upload the .skill file to their Claude environment
- Use it independently for their projects
- Benefit from the same systematic extraction approach

### For Your Organization
Consider creating organization-specific reference materials that can be added to the `references/` folder:
- Company-specific parameter ranges
- Internal modeling standards
- Platform-specific templates
- Historical compound database summaries

## Feedback and Iteration

This skill was developed based on the core requirements of pharmaceutical modeling scientists. If you find aspects that could be improved:

1. Note specific scenarios where extraction wasn't optimal
2. Identify missing parameter categories
3. Suggest additional reference materials
4. Request platform-specific enhancements

The skill can be updated and re-packaged with improvements based on real-world usage.

## Support

For questions or issues with the skill:
- Review the SKILL.md file (included in the .skill package)
- Check the reference materials in the `references/` folder
- Ask Claude to explain specific extraction criteria
- Request clarification on parameter interpretations

## Version Information

**Version**: 1.0  
**Created**: November 2025  
**Purpose**: PBPK, QSP, and pharmacokinetic modeling support for pharmaceutical scientists  
**Platforms**: GastroPlus, Simcyp, PK-Sim, NONMEM, and other modeling software  
**License**: See LICENSE.txt in skill package

---

**Remember**: This skill makes Claude your specialized assistant for modeling literature extraction. The more clearly you communicate your modeling objectives, the more valuable and targeted the extraction will be.
