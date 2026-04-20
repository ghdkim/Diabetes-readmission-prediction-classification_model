# README.md

## Project Title

**Med-Readmit: Scalable Diabetes Patient Readmission Prediction Using Custom PySpark Logistic Regression**

## Author

Gihyun Derek Kim

## Course

**MET CS777 – Big Data Analytics**

## Project Overview

This project predicts whether a diabetes patient will be readmitted to the hospital within 30 days using a custom-built logistic regression model implemented in PySpark. The model was developed without using Spark MLlib or other built-in machine learning libraries. The project uses distributed computing to process healthcare data at scale and evaluates the model using accuracy, precision, recall, F1-score, and ROC-AUC.

The dataset used is the UCI Diabetes 130-US Hospitals dataset, which contains over 100,000 hospital encounters.

## Problem Statement

Hospital readmissions are a key indicator of healthcare quality and are associated with increased costs and poorer patient outcomes. Predicting which patients are at risk of readmission allows healthcare providers to intervene early and improve care.

This project addresses the following research question:

Can clinical, demographic, and hospital encounter characteristics be used to predict 30-day hospital readmission for diabetes patients using a custom-built logistic regression model in a distributed computing environment?

---

## Dataset

Source: UCI Diabetes 130-US Hospitals for Years 1999–2008

Input file:
gs://cs777-bucket-derek/diabetic_data.csv

The project uses the UCI Diabetes 130-US Hospitals dataset, which contains over 100,000 hospital encounters from 1999 to 2008.

Each record represents a patient encounter and includes:

- Demographic information (age, gender)
- Clinical indicators (A1C results, glucose levels)
- Healthcare utilization (inpatient, outpatient, emergency visits)
- Admission and discharge details
- Treatment-related variables (medications, insulin usage)

Target Variable

The original target variable contains three categories:

- “<30” (readmitted within 30 days)
- “>30” (readmitted after 30 days)
- “NO” (not readmitted)

For this project, it was converted into a binary classification problem:

- 1: readmitted within 30 days
- 0: not readmitted within 30 days

The dataset is highly imbalanced, with approximately 11% positive cases and 89% negative cases.

---

## System Architecture

The system follows a distributed data processing pipeline:

- Data stored in Google Cloud Storage (GCS)
- Processed using PySpark on Google Cloud Dataproc
- Model trained using distributed RDD operations
- Evaluated using classification metrics

## Cluster Configuration

Google Cloud Dataproc (Standard cluster)

- 1 master node
- 2 worker nodes
- Machine type: n4-standard-2
- Disk size: 100 GB
- Image version: 2.3-debian12
- Jupyter Notebook: Enabled
- Component Gateway: Enabled
- Environment: Linux / UNIX

## Technology Stack

- PySpark (RDD-based processing)
- Google Cloud Dataproc
- Google Cloud Storage (GCS)
- Python
- NumPy
- Matplotlib

---

## Workflow

1. Data Preprocessing

- Load dataset from GCS
- Filter and clean records
- Handle missing values
- Convert categorical variables into numeric representations
  – Create binary target variable

2. Feature Engineering

- Clinical and demographic features
- Diagnosis grouping (diag_1_group)
- Interaction features:
- Treatment intensity
- Healthcare utilization
- Disease severity

3. Feature Scaling

- Standardization using training data statistics
- Ensures consistent feature contribution

4. Model Training

- Broadcast weights to worker nodes
- Compute gradients in parallel
- Aggregate gradients across partitions
- Update weights using gradient descent

5. Evaluation

The model was evaluated using:

- Accuracy
- Precision
- Recall
- F1-score
- ROC-AUC

Special emphasis was placed on recall due to the importance of identifying high-risk patients.

---

## Model

Custom logistic regression using PySpark RDDs (no MLlib)

Key steps:

- Data preprocessing
- Feature engineering
- Feature scaling
- Class weighting
- Gradient descent training

---

## Hyperparameters

learning_rate = 0.05  
epochs = 40  
l2_lambda = 0.001  
positive_weight = 8.0  
threshold = 0.50

---

## Final Results

Accuracy: 0.6214  
Precision: 0.1582  
Recall: 0.5536  
F1-score: 0.2461  
ROC-AUC: 0.6302

---

## Key Findings

- Class weighting significantly improves recall in imbalanced datasets
- Feature engineering and interaction features enhance model performance
- Threshold tuning is critical for balancing precision and recall
- Model performance stabilizes after 40 epochs

## Limitations

- Logistic regression is a linear model and cannot capture complex nonlinear relationships
- Important variables such as social determinants and patient behavior are not included
- High false positive rate reduces precision
- Performance is limited (ROC-AUC ~0.63)

---

## How to Run

### Dataproc

Run on Google Cloud Dataproc

1. Upload dataset to Google Cloud Storage:

   `gsutil cp diabetic_data.csv gs://your-bucket/`

2. Create a Dataproc cluster with the configuration listed above
3. Open Jupyter Notebook from the cluster
4. Run the notebook containing the model code

### Run Locally

1. Install dependencies:

`pip install pyspark numpy matplotlib`

2. Update file path:

`FULL_DATA_PATH = "diabetic_data.csv"`

3. Run the script or notebook

---

## Notes

- Built without MLlib
- Designed for scalability
- Tuned on small data, tested on full dataset

## Summary

This project demonstrates that a custom-built logistic regression model can be effectively implemented in a distributed environment to process large healthcare datasets and identify meaningful patterns related to hospital readmission. While performance is moderate, the approach highlights both the potential and the challenges of applying machine learning in real-world healthcare settings.

---

## References

[1] UCI Machine Learning Repository, “Diabetes 130-US Hospitals for Years 1999–2008 Data Set.” Available: https://archive.ics.uci.edu/ml/datasets/Diabetes+130-US+hospitals+for+years+1999-2008

[2] B. Strack, J. DeShazo, C. Gennings, et al., “Impact of HbA1c Measurement on Hospital Readmission Rates: Analysis of 70,000 Clinical Database Patient Records,” BioMed Research International, 2014.

[3] Benbassat J, Taragin M. Hospital Readmissions as a Measure of Quality of Health Care: Advantages and Limitations. Arch Intern Med. 2000;160(8):1074–1081. doi:10.1001/archinte.160.8.1074
