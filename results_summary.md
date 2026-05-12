# Tổng hợp kết quả thực nghiệm

**Ngày:** 2026-05-12
**Task:** Multi-label classification - dự đoán loại refactoring được áp dụng lên các file test Java
**Metric:** F1-macro, AUC-PR (5-fold cross-validation)

---

## 1. Tổng quan Dataset

| Dataset | Labels | Ngưỡng lọc | Số dòng |
|---|---|---|---|
| `18labels_min50` | 18 | >= 50 True | 5,213 |
| `9labels_min100` | 9 | >= 100 True | 5,213 |
| `7labels_min200` | 7 | >= 200 True | 5,213 |

**Features:** 41 (22 test smells + 6 code metrics + 13 process metrics)

---

## 2. Các Approach

| Ký hiệu | Approach | Models | Notebook |
|---|---|---|---|
| BR | Binary Relevance | LightGBM, XGBoost, LogisticRegression | `02_binary_relevance.ipynb` |
| CC | Classifier Chains (Ensemble x5) | LightGBM, XGBoost, LogisticRegression | `03_classifier_chains.ipynb` |
| DL | MLP Multi-label | PyTorch MLP [256→128→64] + BCEWithLogitsLoss + pos_weight | `04_dl_multilabel.ipynb` |

---

## 3. Kết quả đầy đủ

### Dataset: `9labels_min100` (khuyến nghị - cân bằng giữa chất lượng và độ phủ)

| Approach | Model | F1-macro | AUC-PR |
|---|---|---|---|
| CC | **LightGBM** | **0.4083** | **0.4262** |
| CC | XGBoost | 0.4045 | 0.4251 |
| BR | XGBoost | 0.3852 | 0.4084 |
| BR | LightGBM | 0.3842 | 0.4095 |
| DL | MLP | 0.2083 | 0.1880 |
| BR | LogisticRegression | 0.1951 | 0.1396 |
| CC | LogisticRegression | 0.1853 | 0.1361 |

### Dataset: `7labels_min200` (nghiêm ngặt nhất - chất lượng dữ liệu cao nhất)

| Approach | Model | F1-macro | AUC-PR |
|---|---|---|---|
| CC | **LightGBM** | **0.4283** | **0.4551** |
| CC | XGBoost | 0.4198 | 0.4546 |
| BR | XGBoost | 0.4115 | 0.4388 |
| BR | LightGBM | 0.4027 | 0.4381 |
| DL | MLP | 0.2347 | 0.2101 |
| BR | LogisticRegression | 0.2198 | 0.1578 |
| CC | LogisticRegression | 0.2145 | 0.1590 |

### Dataset: `18labels_min50` (rộng nhất - nhiều labels nhất, hiệu suất thấp nhất)

| Approach | Model | F1-macro | AUC-PR |
|---|---|---|---|
| CC | **LightGBM** | **0.3295** | **0.3361** |
| CC | XGBoost | 0.3264 | 0.3453 |
| BR | XGBoost | 0.3159 | 0.3230 |
| BR | LightGBM | 0.3102 | 0.3144 |
| DL | MLP | 0.1334 | 0.1359 |
| BR | LogisticRegression | 0.1388 | 0.1043 |
| CC | LogisticRegression | 0.1348 | 0.1007 |

---

## 4. Nhận xét

### 4.1 CC liên tục vượt BR với boosting models
Classifier Chains cải thiện so với Binary Relevance ở cả LightGBM và XGBoost trên tất cả datasets, xác nhận rằng **các refactoring labels có tương quan với nhau** và việc capture label dependency mang lại giá trị thực sự.

| Base Model | BR (9labels) | CC (9labels) | Chênh lệch |
|---|---|---|---|
| LightGBM | 0.3842 | 0.4083 | +0.024 |
| XGBoost | 0.3852 | 0.4045 | +0.019 |

### 4.2 CC-LR thua BR-LR
Predictions của Logistic Regression không đủ confidence để làm feature bổ sung cho các labels tiếp theo trong chain - với base estimator yếu, chain không có tác dụng, thậm chí gây hại.

### 4.3 DL thua rõ ràng so với traditional ML
MLP chỉ đạt F1=0.21 so với F1=0.41 của CC-LightGBM trên `9labels_min100`. Với chỉ 5,213 dòng và class imbalance nặng, neural network không thể generalise hiệu quả.

### 4.4 Càng nhiều positive samples per label thì kết quả càng tốt

| Dataset | F1-macro tốt nhất |
|---|---|
| `7labels_min200` | 0.4283 |
| `9labels_min100` | 0.4083 |
| `18labels_min50` | 0.3295 |

---

## 5. Model tốt nhất

**`ClassifierChain-LightGBM` trên `7labels_min200`**
- F1-macro: **0.4283**
- AUC-PR: **0.4551**

