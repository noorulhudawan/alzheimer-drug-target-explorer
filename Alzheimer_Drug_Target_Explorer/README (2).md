# Alzheimer's Drug Target Explorer

An exploratory Python workflow that collects Alzheimer's disease (AD) target data from public APIs, checks and cleans it, visualises it, and then examines experimental compound activity against the best-supported target, the **amyloid-beta precursor protein (APP)**.

> **Disclaimer:** This is a small exploratory data-analysis project. It does not test drugs or predict clinical effect. A strong binding value (low Ki) does not mean a compound treats Alzheimer's disease.

---

## Table of Contents
1. [Overview](#overview)
2. [Tech Stack](#tech-stack)
3. [Data Sources](#data-sources)
4. [Workflow](#workflow)
5. [Key Results](#key-results)
6. [Project Structure](#project-structure)
7. [Installation and Usage](#installation-and-usage)
8. [Output Files](#output-files)
9. [Biological Terms in Brief](#biological-terms-in-brief)
10. [Limitations](#limitations)
11. [Next Steps](#next-steps)
12. [Documentation](#documentation)

---

## Overview

The notebook `alzheimer_analysis.ipynb` answers four questions:

1. Which protein targets are linked to Alzheimer's disease?
2. Which target has the strongest disease evidence?
3. What experimental compound data exists for that target?
4. Which compounds bind it most tightly (lowest Ki)?

## Tech Stack

| Tool | Purpose |
|---|---|
| **Python 3** + Jupyter Notebook | Main language and environment |
| **requests** | Calls the ChEMBL REST API (GET) and Open Targets GraphQL API (POST) |
| **pandas** | Converts JSON to tables; cleaning, filtering, grouping, merging |
| **Matplotlib** | All charts, saved as 300 dpi PNG files |
| **json / pathlib** | Saving raw API responses and creating folders |

## Data Sources

| Source | Type | What it provides |
|---|---|---|
| [ChEMBL](https://www.ebi.ac.uk/chembl/) (EMBL-EBI) | REST API | Targets, compound-target activities, assays, papers, molecules |
| [Open Targets Platform](https://platform.opentargets.org/) | GraphQL API | Disease search and gene-disease association scores (0 to 1) |

Both APIs are public and need **no API key**.

## Workflow

![Workflow](images/workflow.png)

| Stage | Description |
|---|---|
| 1. Target search | ChEMBL keyword search "Alzheimer" returns 7 targets; cleaned, validated and charted |
| 2. Disease evidence | Open Targets: disease ID `MONDO_0004975`, 13,006 associated targets, top 10 retrieved |
| 3. Activity download | ChEMBL activities for human APP (`CHEMBL2487`): 20-record pilot, then 100 records; quality checks |
| 4. Filter and rank | Keep exact Ki values in nM (44 records, 33 compounds); inspect assays and source papers |
| 5. Integration | Top 5 compounds merged with molecule details and saved to CSV |

## Key Results

| Item | Result |
|---|---|
| ChEMBL targets for "Alzheimer" | 7 (5 human, 1 rat, 1 mouse) |
| Disease ID | `MONDO_0004975` |
| Associated targets (Open Targets) | 13,006 (top 10 retrieved) |
| Strongest disease-linked target | **APP**, score 0.874 (PSEN1 0.868 second) |
| APP activity records (ChEMBL) | 100 records, 55 unique compounds |
| Exact Ki (nM) records | 44 records, 33 compounds |
| Strongest binder | **CHEMBL74704**, Ki 0.11 nM (pChEMBL 9.96) |

![Top targets](images/targets.png)

![Lowest Ki values](images/ki.png)

**Top 5 compounds by lowest exact Ki**

| Compound | Name | Ki (nM) | pChEMBL | Assay | Mol. weight |
|---|---|---|---|---|---|
| CHEMBL74704 | n/a | 0.11 | 9.96 | CHEMBL649530 | 481.30 |
| CHEMBL76112 | n/a | 0.13 | 9.89 | CHEMBL649530 | 481.30 |
| CHEMBL300172 | CHRYSAMINE G | 0.14 | 9.85 | CHEMBL649530 | 482.45 |
| CHEMBL292910 | n/a | 0.17 | 9.77 | CHEMBL649529 | 556.35 |
| CHEMBL305634 | n/a | 0.19 | 9.72 | CHEMBL649530 | 481.30 |

**Interpretation:** APP, PSEN1 and PSEN2 (the amyloid pathway) lead the disease ranking. The best Ki values come from binding assays on amyloid aggregates described in papers on amyloid-plaque probes, so these compounds are best viewed as amyloid-binding probe candidates, not proven drugs. No clinical phase is recorded for any of the five.

## Project Structure

```
.
├── alzheimer_analysis.ipynb          # Main notebook
├── README.md
├── requirements.txt                  # See below
├── docs/
│   └── Alzheimer_Analysis_Report.pdf # Full technical report
├── images/                           # Figures used in this README
├── data/
│   └── raw/target_search.json        # Created by the notebook
└── outputs/                          # CSV and PNG files created by the notebook
```

> The notebook saves the raw JSON to `../data/raw`, so keep the notebook one folder below `data/`, or change the path in the code. CSV and PNG files are saved next to the notebook unless you edit the paths.

## Installation and Usage

```bash
# 1. Clone
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>

# 2. (Optional) virtual environment
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Start Jupyter and open the notebook
jupyter notebook alzheimer_analysis.ipynb
```

**`requirements.txt`**

```
requests
pandas
matplotlib
jupyter
```

**Important:**
- An internet connection is required.
- Run the cells **from top to bottom**. Variable names such as `target_df`, `activity_df` and `ki_df` are reused, so running cells out of order can fail or give different data.
- Results may change slightly when ChEMBL or Open Targets release new data versions.

## Output Files

| File | Content |
|---|---|
| `data/raw/target_search.json` | Raw ChEMBL target-search response |
| `alzheimer_associated_targets.csv` | Top 10 Open Targets associations |
| `top_5_app_compounds.csv` | Final table of 5 lowest-Ki APP compounds |
| `alzheimer_target_association.png` | Association-score bar chart |
| `app_activity_types.png` | Activity types recorded for APP |
| `top_ki_compounds.png` | Lowest exact Ki values |

## Biological Terms in Brief

| Term | Meaning |
|---|---|
| **Alzheimer's disease** | Progressive brain disease and the most common cause of dementia |
| **Amyloid-beta** | Sticky protein fragment that forms plaques in AD brains |
| **APP** | Parent protein from which amyloid-beta is cut |
| **Gamma-secretase** | Enzyme complex (PSEN1/PSEN2, nicastrin) that releases amyloid-beta |
| **ADAM10** | Alpha-secretase; cuts APP without producing amyloid-beta |
| **Target / ligand** | Protein a drug acts on / molecule that binds it |
| **Ki** | Inhibition constant; lower means tighter binding |
| **IC50 / EC50** | Concentration giving 50% inhibition / 50% of maximum effect |
| **pChEMBL** | -log10 of the molar value; 9 is about 1 nM; higher is stronger |
| **Binding (B) / functional (F) assay** | Measures attachment to target / effect in cells |

A full glossary is in the PDF report.

## Limitations

- Small samples: 7 ChEMBL targets, 10 of 13,006 Open Targets rows, 100 activity records (first page of results only).
- Different assay types (Ki, IC50, EC50, % inhibition) and conditions cannot be compared directly.
- Lowest Ki does not mean best drug; safety, brain penetration and clinical effect are untested.
- The ChEMBL keyword `score` is search relevance, not biological importance.
- 2 duplicate rows in the 100-record set were reported but not removed.
- In the 20-record pilot, values with relation `>` were treated as exact; the 100-record analysis corrects this by requiring relation `=`.

## Next Steps

- Page through all ChEMBL activity records and remove duplicates.
- Wrap API calls in functions with error handling and retries.
- Use separate variable names for each dataset.
- Compare other top targets (PSEN1, APOE, ADAM10, CDK5).
- Add chemical-structure comparison and functional (IC50) data.
- Read the source papers before selecting any compound for follow-up.

## Documentation

The full step-by-step report (methodology, data description, results, glossary and API queries) is in [`docs/Alzheimer_Analysis_Report.pdf`](docs/Alzheimer_Analysis_Report.pdf).

## License

Add a license of your choice (for example MIT) before publishing. Check the current data-use terms of ChEMBL and Open Targets before redistributing any downloaded data.
