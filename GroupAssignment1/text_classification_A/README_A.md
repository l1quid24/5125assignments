# A - Data Preparation (Text Classification Group Assignment)

## Dataset Topic
Five semantically related neurodegenerative categories (ML/NLP papers):
a) Alzheimer’s disease
b) Parkinson’s disease
c) Vascular dementia
d) Mild cognitive impairment (MCI)
e) Lewy body dementia (DLB)

## Data Source
Europe PMC REST API (Title + Abstract + metadata).  
Collected on: 2026-02-02

## Query Strategy
Each class query = (disease terms) AND (ML/NLP terms).
Common ML/NLP terms:
("machine learning" OR "deep learning" OR "neural network" OR NLP OR "natural language processing" OR "text mining")

Disease term sets:
- Alzheimer: (Alzheimer* OR "Alzheimer disease" OR AD)
- Parkinson: (Parkinson* OR PD)
- Vascular dementia: ("vascular dementia" OR "vascular cognitive impairment" OR VCI)
- MCI: ("mild cognitive impairment" OR MCI)
- Lewy body dementia: ("Lewy body dementia" OR DLB OR "dementia with lewy bodies")

## Preprocessing / Cleaning
- Merge text as either:
  - Title + Abstract (main)
  - Abstract only (verification)
- Normalize whitespace, remove unusual characters
- Regex tokenization
- Remove English stopwords (sklearn)
- Truncate to fixed-length records:
  - 100 words (main + abstract-only)
  - 20 words (difficulty / “harder prediction” experiment)

## Deduplication
- Primary key: DOI
- If DOI missing: (lowercased title + year)

## Sampling
- Target: 200 documents per category (random sampling with fixed seed)

## Train/Test Split
- Stratified split (by label)
- Test size = 15%
- Random seed = 42
- Note: 10-fold CV is performed later on the training set for model selection.

## Outputs
Generated CSV files in `outputs/`:
- `data_title_abstract_100w.csv`
- `data_abstract_only_100w.csv`
- `data_title_abstract_20w.csv`

Columns:
- doc_id, label, category, year, text, split, (optional metadata: source, journalTitle, doi, pubType)

## Feature Engineering, Modeling & Evaluation
Feature Engineering
Vectorization: TF-IDF (Term Frequency-Inverse Document Frequency).

N-grams: Unigrams + Bigrams (1, 2) to capture phrases like "Lewy body".

Vocabulary: Limited to top 5000 features.

Model Selection (10-Fold Cross-Validation):
We compared five algorithms on the training set:

Multinomial Naive Bayes

Support Vector Machine (Linear SVM) - Selected as Champion

Random Forest

K-Nearest Neighbors (KNN)

XGBoost

And the final champion model is SVM.

## Error Analysis
We upgraded the difficulty and use the AUC to calculate to measure the model's ability to distinguish between categories at varying levels of difficulty.

We examined whether the model exhibited temporal bias by overlaying metadata such as publication year.

We employed SHAP analysis to identify specific features (vocabulary) that contributed to prediction difficulty.

## File Structure
A_data_prep.ipynb: Scripts for fetching, cleaning, and splitting data.

B_modeling_and_analysis.ipynb: Scripts for TF-IDF, Model Training, Cross-Validation, and Error Analysis.

outputs/:

data_title_abstract_100w.csv: Main dataset.

data_title_abstract_20w.csv: Difficulty dataset.

README.md: Project documentation.

## How to Run
1) Open `A_data_prep.ipynb` in VS Code Jupyter
2) Run all cells top-to-bottom
3) Check the `outputs/` folder for generated CSV files