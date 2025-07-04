# Product Classification for LKPP E-Catalog Using KNN

This project focuses on building and evaluating a K-Nearest Neighbors (KNN) model to automatically classify products into their respective storefronts (categories) on the Indonesian government's e-catalog platform (LKPP).

-----

## ⚙️ Methodology

The project followed a systematic pipeline to process textual data and train a robust classification model.

### 1\. Preprocessing & Feature Extraction

The raw product names underwent standard preprocessing (lowercasing, cleansing, tokenization, and stopword removal). To convert the clean text into numerical features, **Term Frequency-Inverse Document Frequency (TF-IDF)** was used. Key decisions in this phase include:

  * **N-gram Range (1, 2)**: This was chosen to allow the model to learn from both individual words (unigrams) and meaningful two-word phrases (bigrams). For example, the phrase "meja pingpong" (ping-pong table) provides more specific context than "meja" or "pingpong" alone, leading to better feature representation.
  * **L2 Normalization**: This was applied to the TF-IDF vectors to ensure each document's feature vector has a unit length. This prevents the model from being biased towards longer product names during the distance calculations in KNN.

### 2\. Data Splitting

A **stratified 80:20 train-test split** was implemented. Because the number of products varied significantly across the 18 storefronts (*imbalanced data*), stratification was essential to ensure that the proportion of products from each storefront, including the rare ones, was identical in both the training and testing sets. This allows for a fair and representative evaluation of the model's performance.

### 3\. Modeling and Hyperparameter Tuning

The core algorithm is **K-Nearest Neighbors (KNN)**, optimized using **Grid Search with 5-Fold Stratified Cross-Validation (SKCV)**. The search focused on finding the best combination of:

  * **Number of Neighbors (k)**: The number of nearest data points to consider for a prediction.
  * **Weighting Method**:
      * *Uniform*: All neighbors have an equal vote.
      * *Distance*: Closer neighbors have a stronger influence on the prediction. This method was chosen for testing because it is often more effective on imbalanced datasets, as it prioritizes the most similar items over distant ones.

-----

## 📊 Results and Evaluation

The hyperparameter tuning process determined that the most effective model configuration was:

  * **Number of Neighbors (k)**: **4**
  * **Weighting Method**: **distance**

When evaluated on the test set containing 9,942 unseen products, this optimal model achieved the following results:

| Metric                | Value      |
| --------------------- | ---------- |
| **Accuracy** | **90.62%** |
| **Macro Avg Precision** | 91.03%     |
| **Macro Avg Recall** | 91.19%     |
| **Macro Avg F1-Score** | **90.96%** |

The high F1-Score, which balances precision and recall, indicates that the model is robust and performs well even with the existing class imbalance.

-----

## 🔬 Error Analysis

While the overall performance was strong, the model struggled with **class overlap**, where products in different categories share similar names and attributes.

The lowest performance was observed in classifying products between the **"Peralatan Pendidikan" (Educational Equipment)** and **"Peralatan Perkantoran" (Office Equipment)** storefronts. An analysis of the misclassified items revealed that common products like "meja" (table), "kursi" (chair), "lemari" (cabinet), and products from the same brand (e.g., "Chitose") exist in both categories. This ambiguity makes it difficult for a distance-based algorithm like KNN to distinguish between them, resulting in a lower F1-score (0.79) for the Educational Equipment category.

**Confusion Matrix**
*(The matrix highlights the confusion between classes 11 (Educational) and 13 (Office).)*

**Word Cloud of Misclassified Products**
This visual confirms the overlap, showing the exact words that frequently caused classification errors between the two storefronts.
