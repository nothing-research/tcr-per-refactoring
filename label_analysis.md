# Phân tích và Lọc Labels: per_refactoring_merged.csv

## 1. Tổng quan dataset

| Thuộc tính | Giá trị |
|---|---|
| Tổng số dòng | 5,213 |
| Tổng số cột | 97 |
| Tổng số labels (`is*`) | 50 |
| Nguồn | 59 dự án Java mã nguồn mở trên GitHub |

---

## 2. Phân phối toàn bộ 50 labels

| Label | True Count | True % |
|---|---|---|
| isAddAssertArgument | 34 | 0.65% |
| isAddClassAnnotation | 221 | 4.24% |
| isAddMethodAnnotation | 759 | 14.56% |
| isEncapsulateAttribute | 8 | 0.15% |
| isExtractAndMoveMethod | 41 | 0.79% |
| isExtractAttribute | 52 | 1.00% |
| isExtractClass | 60 | 1.15% |
| isExtractInterface | 2 | 0.04% |
| isExtractMethod | 298 | 5.72% |
| isExtractSubclass | 2 | 0.04% |
| isExtractSuperclass | 20 | 0.38% |
| isExtractVariable | 398 | 7.63% |
| isInlineAttribute | 3 | 0.06% |
| isInlineMethod | 54 | 1.04% |
| isInlineVariable | 153 | 2.93% |
| isInvertCondition | 0 | 0.00% |
| isMergeAttribute | 9 | 0.17% |
| isMergeClass | 0 | 0.00% |
| isMergeMethod | 0 | 0.00% |
| isMergeVariable | 8 | 0.15% |
| isModifyClassAnnotation | 122 | 2.34% |
| isModifyMethodAnnotation | 386 | 7.40% |
| isMoveAndInlineMethod | 8 | 0.15% |
| isMoveAttribute | 37 | 0.71% |
| isMoveClass | 51 | 0.98% |
| isMoveMethod | 97 | 1.86% |
| isPullUpAttribute | 24 | 0.46% |
| isPullUpMethod | 37 | 0.71% |
| isPushDownAttribute | 0 | 0.00% |
| isPushDownMethod | 2 | 0.04% |
| isRemoveClassAnnotation | 360 | 6.91% |
| isRemoveMethodAnnotation | 657 | 12.60% |
| isReplaceAnonymousWithLambda | 83 | 1.59% |
| isReplaceAttribute | 2 | 0.04% |
| isReplaceAttributeWithVariable | 32 | 0.61% |
| isReplaceLoopWithPipeline | 15 | 0.29% |
| isReplaceNOToperator | 20 | 0.38% |
| isReplacePipelineWithLoop | 0 | 0.00% |
| isReplaceReservedWords | 63 | 1.21% |
| isReplaceRuleWithAssertThrows | 25 | 0.48% |
| isReplaceTryCatchWithAssertThrows | 13 | 0.25% |
| isReplaceTryCatchWithRule | 8 | 0.15% |
| isReplaceVariableWithAttribute | 81 | 1.55% |
| isReplaceconditionalbyParameterizedTest | 23 | 0.44% |
| isReplacetestexpectedwithassertThrows | 30 | 0.58% |
| isSplitAttribute | 18 | 0.35% |
| isSplitClass | 0 | 0.00% |
| isSplitConditional | 4 | 0.08% |
| isSplitConditionalStatementinAssertions | 53 | 1.02% |
| isSplitVariable | 0 | 0.00% |

---

## 3. Lý do lọc labels

### Vấn đề: Label Imbalance nghiêm trọng

Với bài toán **multi-label binary classification**, mỗi label được train độc lập (Binary Relevance) hoặc theo chuỗi (Classifier Chains). Khi một label có quá ít positive samples:

- **Model không học được pattern** vì hầu như không có ví dụ dương để học.
- **Metric bị ảo**: Model predict toàn `False` vẫn đạt accuracy cao (ví dụ label có 2 True / 5213 dòng → accuracy 99.96% khi predict toàn False).
- **Cross-validation không ổn định**: Một số fold có thể không có positive sample nào, gây lỗi hoặc kết quả vô nghĩa.
- **Precision/Recall/F1 = 0** cho class `True`, không có giá trị nghiên cứu.

### Ngưỡng lọc: `True Count >= 50`

Chọn ngưỡng **50 positive samples** vì:
- Đảm bảo mỗi label có đủ ví dụ dương để học pattern thực sự.
- Với 5-fold cross-validation, mỗi fold trung bình có ít nhất 10 positive samples, đủ để tính metric ổn định.
- Đây là ngưỡng thực nghiệm phổ biến trong nghiên cứu imbalanced classification.

---

## 4. Kết quả lọc

### Labels bị loại bỏ (32 labels)

**Lý do: True Count = 0 (không có ý nghĩa)**
- `isInvertCondition`, `isMergeClass`, `isMergeMethod`, `isPushDownAttribute`, `isReplacePipelineWithLoop`, `isSplitClass`, `isSplitVariable`

**Lý do: True Count < 50 (quá ít data)**
- `isAddAssertArgument` (34), `isEncapsulateAttribute` (8), `isExtractAndMoveMethod` (41), `isExtractInterface` (2), `isExtractSubclass` (2), `isExtractSuperclass` (20), `isInlineAttribute` (3), `isMergeAttribute` (9), `isMergeVariable` (8), `isMoveAndInlineMethod` (8), `isMoveAttribute` (37), `isPullUpAttribute` (24), `isPullUpMethod` (37), `isPushDownMethod` (2), `isReplaceAttribute` (2), `isReplaceAttributeWithVariable` (32), `isReplaceLoopWithPipeline` (15), `isReplaceNOToperator` (20), `isReplaceRuleWithAssertThrows` (25), `isReplaceTryCatchWithAssertThrows` (13), `isReplaceTryCatchWithRule` (8), `isReplaceconditionalbyParameterizedTest` (23), `isReplacetestexpectedwithassertThrows` (30), `isSplitAttribute` (18), `isSplitConditional` (4)

### Labels được giữ lại (18 labels)

| Label | True Count | True % |
|---|---|---|
| isAddMethodAnnotation | 759 | 14.56% |
| isRemoveMethodAnnotation | 657 | 12.60% |
| isExtractVariable | 398 | 7.63% |
| isModifyMethodAnnotation | 386 | 7.40% |
| isExtractMethod | 298 | 5.72% |
| isAddClassAnnotation | 221 | 4.24% |
| isInlineVariable | 153 | 2.93% |
| isModifyClassAnnotation | 122 | 2.34% |
| isMoveMethod | 97 | 1.86% |
| isReplaceAnonymousWithLambda | 83 | 1.59% |
| isReplaceVariableWithAttribute | 81 | 1.55% |
| isReplaceReservedWords | 63 | 1.21% |
| isExtractClass | 60 | 1.15% |
| isSplitConditionalStatementinAssertions | 53 | 1.02% |
| isInlineMethod | 54 | 1.04% |
| isExtractAttribute | 52 | 1.00% |
| isMoveClass | 51 | 0.98% |
| isRemoveClassAnnotation | 360 | 6.91% |

---

## 5. Các phiên bản file output

Tất cả file giữ nguyên 5,213 rows và toàn bộ feature columns (test smells, code metrics, process metrics). Chỉ khác nhau ở ngưỡng lọc labels.

| File | Ngưỡng | Labels | Cols | Mục đích |
|---|---|---|---|---|
| `per_refactoring_merged.csv` | (không lọc) | 50 | 97 | File gốc, không chỉnh sửa |
| `per_refactoring_18labels_min50true.csv` | >= 50 True | 18 | 63 | Thử nghiệm đầy đủ, chấp nhận imbalance vừa |
| `per_refactoring_9labels_min100true.csv` | >= 100 True | 9 | 54 | Production-quality, đủ data để học ổn định |
| `per_refactoring_7labels_min200true.csv` | >= 200 True | 7 | 52 | Strict, chỉ labels mạnh nhất |

### Labels trong từng phiên bản

**`labels100` — 9 labels (>= 100 True):**
isAddMethodAnnotation (759), isRemoveMethodAnnotation (657), isExtractVariable (398), isModifyMethodAnnotation (386), isRemoveClassAnnotation (360), isExtractMethod (298), isAddClassAnnotation (221), isInlineVariable (153), isModifyClassAnnotation (122)

**`labels200` — 7 labels (>= 200 True):**
isAddMethodAnnotation (759), isRemoveMethodAnnotation (657), isExtractVariable (398), isModifyMethodAnnotation (386), isRemoveClassAnnotation (360), isExtractMethod (298), isAddClassAnnotation (221)

---

## 6. Lưu ý khi modeling

- Ngay cả 18 labels được giữ vẫn còn **imbalanced** (tỷ lệ True thấp nhất ~1%).
- Nên dùng `class_weight='balanced'` hoặc `scale_pos_weight` trong LightGBM/XGBoost.
- Đánh giá bằng **F1-macro** hoặc **AUC-PR** thay vì accuracy.
- Cân nhắc **SMOTE** hoặc **oversampling** nếu kết quả vẫn kém ở các label hiếm.
