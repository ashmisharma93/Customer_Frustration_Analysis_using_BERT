# Customer Frustration Analysis using BERT

[![GitHub Repository](https://img.shields.io/badge/GitHub-Repository-black?logo=github)](https://github.com/ashmisharma93/Customer_Frustration_Analysis_using_BERT)

An NLP-based application that detects frustrated customers from product reviews using a fine-tuned BERT-mini model.

The project supports single-review prediction, bulk review analysis, confidence scores, and trigger-word highlighting through Streamlit.

---

## Problem Statement

Manually analyzing thousands of product reviews is time-consuming.

This project helps businesses:

- Identify frustrated customers
- Detect common product complaints
- Prioritize customer support responses
- Reduce manual review analysis

---

## Features

- Single review prediction
- Bulk CSV review analysis
- Frustrated / Not Frustrated classification
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
FRUSTRATED
Confidence: 99%
```

---

## Model Performance

| Metric    | Score  |
|-----------|--------|
| Accuracy  | 99.4%  |
| Precision | 98.99% |
| Recall    | 98.57% |
| F1 Score  | 98.78% |

> These results are based on keyword-generated labels, not human-annotated emotional sentiment data.

---

## Dataset

- **Total Reviews:** 14,337
- **Training Set:** 11,469 reviews
- **Test Set:** 2,868 reviews
- **Product Category:** Wireless earphones
- **Language:** English
- **Labeling:** Keyword-based — reviews containing predefined frustration-related keywords were labeled as frustrated

---

## Model Architecture

- **Model:** BERT-mini
- **Checkpoint:** `prajjwal1/bert-mini`
- **Task:** Binary text classification
- **Classes:** Frustrated / Not Frustrated
- **Epochs:** 3
- **Batch Size:** 16
- **Learning Rate:** 2e-5
- **Framework:** Hugging Face Transformers + PyTorch

BERT-mini was selected because it provides a good balance between accuracy, model size, and inference speed.

---

## Workflow

1. Load product reviews
2. Generate keyword-based labels
3. Tokenize and pad the review text
4. Fine-tune BERT-mini
5. Evaluate the model
6. Save the trained model
7. Use the model through the Streamlit application

---

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

```text
Customer_Frustration_Analysis_using_BERT/
│
├── User_frustration_app/
│   └── app.py
│
├── saved_model/
│   ├── config.json
│   ├── model.safetensors
│   ├── tokenizer_config.json
│   └── ...
│
├── User_Frustration_Project(2).ipynb
├── requirements.txt
├── README.md
└── .gitignore
```

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

The application will open at:

```text
[http://localhost:8501](http://localhost:8501)
```

---

## Limitations

- Labels are generated using keywords
- The model may miss subtle frustration
- Sarcasm and mixed sentiment may be difficult to classify
- The model is trained on wireless earphone reviews
- Current support is limited to English
- Human-labeled data is required for reliable production use

---

## Future Improvements

- Human-labeled training data
- Multi-class sentiment classification
- Aspect-based sentiment analysis
- Multi-language support
- REST API deployment
- Real-time frustration dashboard
- Human-in-the-loop feedback

---
## Author

**Ashmita Sharma**

- GitHub: [ashmisharma93](https://github.com/ashmisharma93)
- LinkedIn: [ashmitasharma93034](https://linkedin.com/in/ashmitasharma93034)