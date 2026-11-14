# De-identifying Clinical Text with spaCy NER

A proof-of-concept entity recogniser that masks identifier-like entities in clinical-style text, built for an MSc dissertation (University of Hull, December 2024). It compares a spaCy NER model with a DistilBERT token classifier and wraps the spaCy model in a small Gradio app.

> **Status: proof of concept on synthetic data.** The model is trained and validated on sentences generated from a fixed template, so its near-perfect validation scores do not show that it can de-identify real clinical notes. It is not a de-identification tool and must not be used on real patient data. See [Limitations](#limitations).

## What is in this repository

| File | Contents |
|---|---|
| `Coding.ipynb` | The full pipeline: data preparation, spaCy training and evaluation, Gradio app, DistilBERT comparison |
| `Dissertation report.pdf` | The MSc dissertation |
| `Presentation.pptx` | Project presentation slides |

## How it works

1. **Synthetic data.** A synthetic table of patient records (patient ID, age, gender, medical condition, treatment, outcome, insurance type, region, smoking status, admission type, hospital ID, and so on).
2. **Templated text.** Each row is turned into a sentence with a fixed template, for example: *"Patient {id} with {condition} was admitted to {hospital} in {region}…"*. A light synonym-swap augmentation is also defined.
3. **Programmatic annotation.** Entity spans are created by locating each field value inside the sentence it came from. There are 10 labels: `PATIENT_ID`, `HOSPITAL_ID`, `REGION`, `ADMISSION_TYPE`, `INSURANCE_TYPE`, `SMOKING_STATUS`, `GENDER`, `MEDICAL_CONDITION`, `TREATMENT`, `OUTCOME`. Annotation is automatic, not manual.
4. **spaCy NER.** A blank English pipeline with an NER component, trained for 10 epochs (dropout 0.2, compounding batch sizes) on an 80/20 train/validation split.
5. **Evaluation.** Per-entity precision, recall and F1, confusion matrix and ROC curves on the validation split.
6. **Gradio app.** A local text box that replaces detected entities with their labels.
7. **DistilBERT comparison.** A `DistilBertForTokenClassification` model trained for 3 epochs, then compared with spaCy on a few hand-written sentences.

## Results

**Validation split (same template as training).** Per-entity precision, recall and F1 are reported as 1.00 for all ten labels in the main evaluation (one later evaluation cell shows 0.88 for `TREATMENT`). Training loss falls from about 3,110 in epoch 1 to about 0.000005 by epoch 10, which indicates the model has learned the template rather than a general task. Treat these scores as a sanity check only.

**Out-of-template test (3 hand-written sentences, 10 labelled entities).** For example: *"Patient John Doe with hypertension was admitted to hospital_1234."*

| Model | Accuracy | Notes |
|---|---|---|
| spaCy NER | 0.10 | `MEDICAL_CONDITION` F1 0.50 (precision 1.00, recall 0.33); `PATIENT_ID`, `HOSPITAL_ID`, `REGION` and `SMOKING_STATUS` all 0.00 |
| DistilBERT | 0.00 | Not a like-for-like comparison, see below |

This is the more informative result: the model does not generalise beyond its training template. The test set is only three sentences, so the exact figures are indicative, not precise.

The dissertation abstract quotes an F1 of 0.94. The outputs saved in `Coding.ipynb` show the validation and out-of-template scores above, so treat the notebook outputs as the reference.

## Limitations

- **Synthetic, templated data.** Every training sentence follows the same structure, so validation data shares that structure.
- **The labels are not a PHI standard.** Several labels (medical condition, treatment, outcome, insurance type, smoking status) are clinical attributes, not identifiers. Real identifier types such as names, dates, phone numbers and addresses are not covered.
- **The DistilBERT comparison is not valid.** Its labels come from a placeholder rule that marks numeric tokens as `B-SENSITIVE` (the notebook comment says "for this demo"), so it is not trained on the same task as the spaCy model. No conclusion about spaCy versus DistilBERT should be drawn from it.
- **Tiny out-of-template test.** Three sentences cannot support a reliable estimate.
- **The dataset file is not included**, so the notebook cannot be run as-is from this repository.

## Suggested next steps

1. Generate realistic identifiers (names, dates, phone numbers, addresses, record numbers) with a library such as Faker, and use many varied note templates.
2. Hold out whole templates for testing, so the test measures generalisation instead of memorisation.
3. Compare against baselines: regular expressions, a pretrained general-purpose NER model, and pretrained clinical de-identification models.
4. Report entity-level metrics with recall first, since a missed identifier is the costly error.
5. Evaluate on realistic clinical text (for example, public sample notes with inserted surrogate identifiers).

## Running the notebook

The notebook was developed with Python 3.12.

```bash
pip install spacy scikit-learn pandas numpy matplotlib seaborn gradio transformers torch
jupyter notebook Coding.ipynb
```

It expects a file named `medical_data.csv` in the same folder. The file is not included. It should be a synthetic patient-records table with columns such as `Patient_ID`, `Age`, `Gender`, `Medical_Condition`, `Treatment`, `Outcome`, `Insurance_Type`, `Income`, `Region`, `Smoking_Status`, `Admission_Type`, `Hospital_ID` and `Length_of_Stay`.

<!-- TODO: add the dataset source or link here so the notebook can be reproduced. -->

## Disclaimer

The data is synthetic and used for learning and experimentation only. This project is not a production de-identification system and should not be applied to real patient data without rigorous privacy, legal and ethical review.

## Author

Keerthirajan Murugesan, MSc Artificial Intelligence and Data Science, University of Hull.
