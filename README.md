# Customer Frustration Analysis using BERT

[![GitHub Repository](https://img.shields.io/badge/GitHub-Repository-black?logo=github)](https://github.com/ashmisharma93/Customer_Frustration_Analysis_using_BERT)

An NLP-based application that detects frustrated customers from product reviews using a fine-tuned BERT-mini model.

The application allows users to analyze individual reviews or upload multiple reviews through a Streamlit interface. It also shows prediction confidence and highlights words that may indicate frustration.

A major part of this project was investigating the quality of the training labels. The initial labels were created using keywords, which gave very high accuracy. After manually checking a separate set of reviews, I found that the actual performance was much lower. This README documents both results.

---

## Live Demo

Try the deployed application here:

[Customer Frustration Analysis App](https://customer-frustration-bert.streamlit.app/)

---

## Problem Statement

Manually checking thousands of customer reviews can take a lot of time.

This project helps businesses:

- Identify frustrated customers
- Find common complaints
- Prioritize customer support cases
- Reduce manual review analysis

The project also looks at an important question: Does a high accuracy score actually mean that the model works well on real customer reviews?

---

## Features

- Single review prediction
- Bulk CSV review analysis
- Frustrated / Not Frustrated OR (Satisfied/ Not Satisfied) classification
- Confidence scores
- Frustration keyword highlighting
- Streamlit web interface
- Fast inference using BERT-mini

---

## Example

**Input:**

```text
These earphones keep disconnecting. Wasted my money. Worst purchase ever.
```

**Output:**

```text
Satisfied
Confidence: 99%
```

**A harder example**
The model can struggle with complaints that are written in a calm and factual way:
```text
Sound quality is okay but it keeps disconnecting randomly during calls.
```
**Output:**

```text
Not Satisfied
Confidence: 98.7%
```
This is a real limitation of the current model and is discussed further in the Results section.
---

## Model Performance

| Metric | Keyword-labeled test set | Manual validation (n=174) |
|---|---|---|
| Accuracy | 96.1% | **~74%** |
| Precision | 98.76% | 74.7% |
| Recall | 96.05% | 72.1% |
| F1 Score | 97.38% | 73.4% |

The 96.1% score comes from testing the model on labels created using the same keyword-based approach used during training.

The ~74% score comes from a separate set of reviews that were manually labeled by a human.

Because the manual labels do not come from the same keyword rule, they give a better idea of how the model performs on real-world examples.

---

## Dataset
- **Total Reviews:** 14,337
- **Products:** 10 wireless earphone models — concentrated, with 2 products (`Sennheiser CX 6.0BT`, `boAt Rockerz 255`) making up ~70% of all reviews
- **Training Set:** 9,337 reviews (9 products)
- **Test Set:** 5,000 reviews (`boAt Rockerz 255`, held out entirely — see Workflow)
- **Language:** English
- **Labeling:** Keyword-based with negation handling (e.g., "not bad" is correctly excluded) — reviews are not human-annotated for emotional sentiment

---

## Model Architecture

- **Model:** BERT-mini
- **Checkpoint:** `prajjwal1/bert-mini`
- **Task:** Binary text classification
- **Classes:** Frustrated / Not Frustrated (explicit `id2label` mapping)
- **Epochs:** 2
- **Batch Size:** 16
- **Learning Rate:** 2e-5
- **Framework:** Hugging Face Transformers + PyTorch
BERT-mini was selected for a good balance of accuracy, model size, and inference speed.

---

## Workflow

1. Load product reviews
2. Generate negation-aware, keyword-based labels
3. Run leave-one-product-out cross-validation (TF-IDF + Logistic Regression baseline) across all 10 products to check whether performance holds up on genuinely unseen products
4. Hold out the largest product (`boAt Rockerz 255`) as a fixed test set, based on that cross-validation evidence
5. Train TF-IDF + Logistic Regression / SVM baselines for comparison
6. Fine-tune BERT-mini on the remaining 9 products
7. Evaluate on the held-out product
8. Sample ~175 reviews from the test set, stratified by the model's own confidence, and hand-label them independently of the keyword rule
9. Score the model against the hand-labeled set to get a real-world performance estimate
10. Save the trained model and deploy via Streamlit


---
## Results

The most important finding from this project is the difference between the two accuracy scores:

- **Keyword-labeled accuracy: 96.1%**
- **Manual validation accuracy: ~74%**

There is a gap of around **22 percentage points**.

This happened because the model was trained using keyword-based labels. As a result, the model can learn patterns that are closely related to those keywords without necessarily understanding every type of customer frustration.

### Product-Level Validation

I also used **leave-one-product-out cross-validation** with a TF-IDF + Logistic Regression model.

The accuracy remained around **94.5%–95.7%** on the two largest products. Based on these results, `boAt Rockerz 255` was selected as the final held-out test product.

This means the final test product was not selected simply because it produced a good result. It was selected after checking performance across different products.

### Model Confidence

The model also showed an important confidence problem.

For predictions where the model was **90%+ confident** (or **10% or lower**), the actual accuracy was only **79.2%**.

So, when the model says it is highly confident, that confidence does not always match its actual accuracy.

### Common Failure Pattern

The model often misses complaints that are written calmly and do not contain strong emotional words.

For example:

```text
"The earphones keep disconnecting randomly during calls."
```

The model may classify this as **Not Frustrated**, even though the customer is clearly describing a product problem.

On the other hand, the model can detect some forms of sarcasm, such as:

```text
"Oh wonderful, another pair that stopped working in a week."
```

So the main problem is not simply the absence of emotional words. The model seems to struggle particularly with **mild, factual complaints**.

### Baseline Comparison

The simpler TF-IDF models performed relatively close to BERT-mini on the keyword-labeled test set:

| Model | Accuracy |
|---|---:|
| TF-IDF + Logistic Regression | 93.6% |
| TF-IDF + SVM | 93.2% |
| BERT-mini | **96.1%** |

The difference between the models is relatively small. This suggests that the task is strongly influenced by the words used in the reviews, and BERT did not provide a very large improvement over the simpler models.

### Manual Validation Limitation

The manual validation set contains **174 reviews** and was labeled by a single person.

Because the sample is relatively small, the **~74% accuracy** should be treated as an estimate rather than an exact measurement.

## Tech Stack

- Python
- Hugging Face Transformers
- PyTorch
- BERT-mini
- Pandas
- NumPy
- Scikit-learn
- Streamlit
- Jupyter Notebook

---


## Project Structure

## Project Structure

```text
Customer_Frustration_Analysis_using_BERT/
│
├── User_frustration_app/
│   ├── saved_model/
│   │   └── Fine-tuned BERT-mini model files (product-split, negation-fixed labels)
│   └── app.py
│
├── data/
│   └── AllProductReviews.csv
│
├── manual_labeling_TO_FILL.csv       # hand-labeled validation set (174 reviews)
├── manual_validation_results.csv     # merged scoring: manual labels vs. model predictions
├── .gitignore
├── README.md
├── new_nb.ipynb                      # full pipeline: labeling → split → LOGO validation → baselines → BERT training → manual validation
└── requirements.txt
```

- `app.py` — Streamlit application for customer frustration detection
- `saved_model/` — Fine-tuned BERT-mini model and tokenizer files
- `data/` — Source dataset
- `manual_labeling_TO_FILL.csv` / `manual_validation_results.csv` — the human-labeled validation evidence referenced in Results
- `new_nb.ipynb` — Complete, single source-of-truth notebook for the project
- `requirements.txt` — Required Python dependencies


---

## Installation and Usage

### 1. Clone the Repository
```bash
git clone https://github.com/ashmisharma93/Customer_Frustration_Analysis_using_BERT.git
cd Customer_Frustration_Analysis_using_BERT
```
 
### 2. Install Dependencies
```bash
pip install -r requirements.txt
```
 
### 3. Run the Application
```bash
cd User_frustration_app
streamlit run app.py
```
 
The application will open at `http://localhost:8501`.

---

## Limitations

- Training labels were created using keywords rather than human annotation
- Only 174 reviews were manually labeled
- The manual validation was done by one person
- The model can miss calm, factual complaints
- The model is overconfident on some predictions
- Sarcasm handling has not been tested systematically
- The dataset contains only wireless earphone reviews
- Two products make up around 70% of the dataset
- Generalization to other product categories has not been tested
- The model currently supports English only
_
---

## Future Improvements

- Create a larger human-labeled dataset
- Add a second annotator and measure agreement between annotators
- Retrain the model using human-labeled data
- Add multi-class sentiment classification
- Add aspect-based sentiment analysis to identify what the customer is unhappy about
- Add support for multiple languages
- Deploy the model as a REST API
- Build a real-time customer frustration dashboard
- Add human feedback to continuously improve the model
--- 

## Author

**Ashmita Sharma**

- GitHub: [ashmisharma93](https://github.com/ashmisharma93)
- LinkedIn: [ashmitasharma93034](https://linkedin.com/in/ashmitasharma93034)