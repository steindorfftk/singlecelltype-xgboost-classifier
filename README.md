# 🧬 SingleCellType-XGBoost-Classifier  
**An XGBoost-based pipeline for cell type classification in single-cell RNA-seq data**

---

## 📘 Overview
This project implements a **cell type classification pipeline** using **XGBoost** on a large-scale **glioblastoma single-cell RNA-seq (scRNA-seq) atlas**.  
The dataset comprises **307k cells (samples)** and **13,112 genes (features)**, annotated into six distinct cell types:

- Tumor cells  
- Myeloid cells  
- Lymphoid cells  
- Oligodendrocytes  
- Mesenchymal cells  
- Endothelial cells  

All counts were **log₁₊ normalized (to 10,000)** and **scaled** (mean = 0, SD = 1).

---

## ⚙️ Pipeline Overview

### **Step 1 — Initial Hyperparameter Optimization**  
📓 *Notebook: `01_Pipeline_Step_1.ipynb`*

- **Data split:** 80% for training (hyperparameter tuning) and 20% for testing.  
- **Label encoding:** Performed prior to model training.  
- **Method:** Randomized hyperparameter search using XGBoost with cross-validation.  
- **Parameters optimized:** `learning_rate`, `max_depth`, `n_estimators`.  
- **Best combination (after 20 iterations):**
  - `learning_rate = 0.2296`
  - `max_depth = 7`
  - `n_estimators = 70`
- **Initial test results:**
  - Accuracy: **98.8%**  
  - Precision: **98.5%**  
  - Recall: **98.3%**  
  - F1-score: **98.3%**

---

### **Step 2 — 20-Fold Validation**  
📓 *Notebook: `02_Pipeline_Step_2.ipynb`*

- **Goal:** Validate model robustness and address class imbalance.  
- **Approach:**  
  - Each round used 2,000 samples per class (balanced subsets).  
  - Data split: 70% training / 30% validation.  
- **Results (mean ± SD across 20 folds):**
  - Precision: **98.3% ± 0.002%**  
  - Recall: **98.3% ± 0.002%**  
  - F1-score: **98.3% ± 0.002%**

---

### **Step 3 — Feature Selection**  
📓 *Notebook: `03_Pipeline_Step_3.ipynb`*

- **Objective:** Simplify the model while maintaining high performance.  
- **Method:**  
  - Ranked features using XGBoost’s `feature_importance_`.  
  - Iteratively removed half of the least important features per round.  
  - Each iteration evaluated with 20-fold validation.  
- **Findings:**  
  - Performance remained stable down to **102 features**.  
  - Further reduction to **51 features** dropped all metrics to ~96%.

---

### **Step 4 — Extended Hyperparameter Optimization**  
📓 *Notebook: `04_Pipeline_Step_4.ipynb`*

- **Goal:** Explore additional XGBoost hyperparameters with reduced feature set.  
- **Additional parameters tested:**  
  - `subsample`, `colsample_bytree`, `gamma`,  
    `min_child_weight`, `reg_alpha`, `reg_lambda`  
- **Method:** Randomized search with 3-fold cross-validation.  
- **Outcome:**  
  - No combination surpassed the original hyperparameters.  
  - Indicates performance saturation due to dataset noise and intrinsic biological variability.


---

## 🧩 Conclusions
- The XGBoost classifier achieved **remarkably high and stable performance** in identifying cell types across a large scRNA-seq dataset.  
- **Feature importance ranking** proved highly effective for dimensionality reduction, maintaining accuracy with as few as 102 genes.  
- Further hyperparameter optimization showed **diminishing returns**, suggesting the model had reached near-optimal performance within the data’s inherent noise limits.

---
