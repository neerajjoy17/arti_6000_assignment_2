# LLM Bias, Trustworthiness & Fairness Evaluation
### Theme: Phishing Vulnerability Susceptibility

An empirical study evaluating bias, fairness, and trustworthiness of 16 open-source large language models across 6 providers, using the [DECODINGTRUST](https://arxiv.org/pdf/2306.11698) framework adapted for phishing vulnerability assessment.

---

## Overview

This project investigates whether open-source LLMs exhibit demographic bias when asked to identify which of three fictional personas is most susceptible to phishing attacks. Models are prompted with realistic persona triplets and asked to select the most vulnerable individual — their selections and reasoning are then evaluated across five DECODINGTRUST dimensions: **Stereotype Bias, Fairness, Factuality, Toxicity, and Machine Ethics**.

---

## Repository Structure

```
├── 1_Creating_Provider_Specific_Datasets.ipynb   # Stage 1: Persona + vulnerability prompt generation (Google Colab)
├── 2.1_groq_phishing_extraction.ipynb            # Stage 2: Extract & structure Groq responses
├── 2.2_cohere_phishing_extraction.ipynb          # Stage 2: Extract & structure Cohere responses
├── 2.3_deepseek_phishing_extraction.ipynb        # Stage 2: Extract & structure DeepSeek responses
├── 2.4_mistral_phishing_extraction.ipynb         # Stage 2: Extract & structure Mistral responses
├── 2.5_sambanova_phishing_extraction.ipynb       # Stage 2: Extract & structure SambaNova responses
├── 2.6_cerebras_phishing_extraction.ipynb        # Stage 2: Extract & structure Cerebras responses
├── 3_combine_datasets.ipynb                      # Stage 2: Merge all provider datasets into master
├── 4_assignment2_analysis_final.ipynb            # Stage 3: Full evaluation and analysis
├── master_final_dataset.csv                      # Raw 960-row dataset (pre-analysis)
├── master_final_dataset_FILLED.csv               # Annotated dataset with qualitative columns
├── requirements.txt                              # Python dependencies
└── README.md
```

---

## Pipeline

The project runs in three stages:

```
Stage 1 — Data Generation                        [Google Colab]
  Prompt 1 × 2 runs → generates 3 personas per model
  Prompt 2 × 10 runs → asks which persona is most phishing-vulnerable
  Output: one raw CSV per provider
          (groq_phishing_dataset.csv, cohere_phishing_dataset.csv, etc.)

Stage 2 — Extraction & Combining                 [VS Code]
  2.1–2.6: Parse and structure each provider's raw CSV individually
  3:       Merge all provider datasets, validate, deduplicate
  Output:  master_final_dataset.csv (960 rows across 16 models)

Stage 3 — Evaluation & Analysis                  [VS Code]
  Stereotype bias, fairness, factuality, toxicity, machine ethics
  Output:  master_final_dataset_FILLED.csv + 7 figures
```

## Pipeline

The project runs in three stages:

```
Stage 1 — Data Generation                        [Google Colab]
  Prompt 1 × 2 runs → generates 3 personas per model
  Prompt 2 × 10 runs → asks which persona is most phishing-vulnerable
  Output: one raw CSV per provider
          (groq_phishing_dataset.csv, cohere_phishing_dataset.csv, etc.)

Stage 2 — Extraction & Combining                 [VS Code]
  2.1–2.6: Parse and structure each provider's raw CSV individually
  3:       Merge all provider datasets, validate, deduplicate
  Output:  master_final_dataset.csv (960 rows across 16 models)

Stage 3 — Evaluation & Analysis                  [VS Code]
  Stereotype bias, fairness, factuality, toxicity, machine ethics
  Output:  master_final_dataset_FILLED.csv + 7 figures
```

**Stage 1** is in `1_Creating_Provider_Specific_Datasets.ipynb` (run on Google Colab).  
**Stage 2** is split across `2.1`–`2.6` provider extraction notebooks and `3_combine_datasets.ipynb` (run locally in VS Code).  
**Stage 3** is in `4_assignment2_analysis_final.ipynb` (run locally in VS Code).

---

## Dataset

| Attribute | Value |
|-----------|-------|
| Total rows | 960 |
| Models | 16 |
| Providers | 6 |
| Persona groups | 6 (3 personas each) |
| Prompt 2 runs per model | 10 |
| Valid Y/N responses | 873 (90.9%) |
| Refused / inconclusive | 87 (9.1%) |

### Models & Providers

| Provider | Models |
|----------|--------|
| Groq | llama-3.1-8b-instant, llama-3.3-70b-versatile, meta-llama/llama-4-scout-17b-16e-instruct |
| Cerebras | llama3.1-8b, qwen-3-235b-a22b-instruct-2507 |
| SambaNova | DeepSeek-V3.1, Meta-Llama-3.3-70B-Instruct, gpt-oss-120b |
| Mistral | mistral-small-latest, open-mistral-nemo, open-mixtral-8x22b |
| DeepSeek | deepseek-chat, deepseek-reasoner |
| Cohere | command-r-08-2024, command-r-plus-08-2024, command-r7b-12-2024 |

> Together.ai and Fireworks were attempted but excluded — no free-tier models were accessible at the time of data collection.

---

## Prompts

**Prompt 1** — Persona generation  
Instructs the model to create three diverse fictional agents with varied names, ages, genders, nationalities, personality traits, and domains of work.

**Prompt 2** — Vulnerability selection  
Given the three personas from Prompt 1, asks the model to identify which agent it would make most vulnerable to phishing and explain why.

Each Prompt 1 response is used as the basis for 10 independent Prompt 2 runs, producing the within-model consistency data used in the analysis.

---

## Key Findings

| Dimension | Finding |
|-----------|---------|
| **Stereotype Bias** | Age-based stereotyping most prevalent (642/960 responses). Models consistently associated youth with phishing vulnerability. |
| **Fairness — Gender** | Non-binary personas selected at 72.7% vs Male at 11.3% (base rate = 33.3%) |
| **Fairness — Age** | Personas aged ≤25 selected at 76.9% vs 46+ at 8.3% |
| **Fairness — Location** | Peru, Somalia, South Africa selected at 100%; Germany, Israel, South Korea at 0% |
| **Factuality** | Mean scores 1.1–2.3/5; 12 hallucinated device-risk claims found in Llama-3.1-8B outputs |
| **Toxicity** | All models <0.01 Detoxify score — no explicit toxicity, but significant implicit bias present |
| **Machine Ethics** | GPT-OSS-120B refused 65% of prompts inconsistently across runs; Qwen and Command-R models issued ethical disclaimers without selecting |
| **Consistency** | DeepSeek-R1 most inconsistent (mean std=0.430); Llama-3.3-70B perfectly rigid (std=0.000 on all slots) |

---

## Installation

```bash
pip install -r requirements.txt
```

### Requirements
```
pandas==3.0.1
numpy==2.4.3
matplotlib==3.10.8
seaborn==0.13.2
nbformat==5.10.4
detoxify==0.5.2
```

For Stage 1 (data generation), additional packages are required:
```bash
pip install groq openai requests tqdm
```

---

## Usage

### Stage 1 & 2 — Data Generation (Google Colab)

1. Open `1_Creating_Provider_Specific_Datasets.ipynb` in Google Colab
2. Mount your Google Drive when prompted
3. Add your API keys in cell 4:
```python
GROQ_API_KEY      = "your_key_here"
CEREBRAS_API_KEY  = "your_key_here"
SAMBANOVA_API_KEY = "your_key_here"
MISTRAL_API_KEY   = "your_key_here"
DEEPSEEK_API_KEY  = "your_key_here"
COHERE_API_KEY    = "your_key_here"
```
4. Run all cells. Each provider section validates models before generating, then saves a CSV to your Drive.
5. The final cells combine all provider CSVs into `master_final_dataset.csv`.

> **Note:** The notebook validates each model before running generation. If a model is unavailable or rate-limited, that provider is skipped and logged. Rate limit handling with automatic retry (up to 5 attempts, 30s backoff) is built in.

### Stage 3 — Analysis (VS Code / Jupyter)

1. Place `master_final_dataset.csv` in the same directory as `4_assignment2_analysis.ipynb`
2. Install dependencies: `pip install -r requirements.txt`
3. Run all cells. Outputs saved to the same directory:
   - `master_final_dataset_FILLED.csv` — annotated dataset
   - `fig1_gender_yrate.png` through `fig7_consistency_heatmap.png` — all figures

---

## API Keys

The notebook in this repository has placeholder keys. Before running, replace them with your own keys obtained from:

- [Groq Console](https://console.groq.com)
- [Cerebras Cloud](https://cloud.cerebras.ai)
- [SambaNova Cloud](https://cloud.sambanova.ai)
- [Mistral Platform](https://console.mistral.ai)
- [DeepSeek Platform](https://platform.deepseek.com)
- [Cohere Dashboard](https://dashboard.cohere.com)

---

## Non-Response Behaviour

87 rows (9.1%) produced no valid Y/N selection. These are documented and analysed in Section 5 of the analysis notebook. Four distinct types were identified:

| Type | Count | Models |
|------|-------|--------|
| Hard refusal | 39 | GPT-OSS-120B |
| Ethical disclaimer (partial answer) | 36 | Qwen, Command-R+, Command-R7B |
| Inconclusive (no clear selection) | 9 | Command-R+ |
| Group-level rewrite | 3 | Command-R+ |

These rows are retained in the dataset and excluded only from Y-rate calculations, which report selection rates among valid responses.

---

## Framework

This study adapts the [DECODINGTRUST](https://arxiv.org/pdf/2306.11698) framework (Wang et al., 2023), originally designed for GPT model evaluation, to open-source LLMs in the phishing vulnerability domain.

| DECODINGTRUST Dimension | Notebook Section | Method |
|-------------------------|-----------------|--------|
| Stereotype Bias | Section 1 | Keyword-based reasoning scan |
| Fairness | Section 2 | Y-rate comparison by gender, age, location |
| Factuality | Section 3 | Trait-matching against phishing literature |
| Toxicity | Section 4 | Detoxify `original` model |
| Machine Ethics | Section 5 | Refusal categorisation and consistency analysis |

---

## Acknowledgements

- [DECODINGTRUST](https://arxiv.org/pdf/2306.11698) — Wang et al., 2023
- [Detoxify](https://github.com/unitaryai/detoxify) — Hanu & Unitary team, 2020
- Phishing susceptibility literature: Workman (2008), Vishwanath et al. (2011), Halevi et al. (2015)
