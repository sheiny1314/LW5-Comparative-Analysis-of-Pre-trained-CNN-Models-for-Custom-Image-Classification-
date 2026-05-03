# LW5-Comparative-Analysis-of-Pre-trained-CNN-Models-for-Custom-Image-Classification-

📊 Performance Comparison Table
| Model | Train Accuracy | Train Loss | Test Accuracy | Test Loss | Precision | Recall | F1-Score | ROC | AUC |
|-------|---------------|------------|---------------|-----------|-----------|--------|----------|-----|-----|
| Pre-trained ResNet50 | 17.90% | 0.1010 | 20.03% | 0.1140 | 0.0964 | 0.1790 | 0.1059 | 0.1059 | 0.1059 |
| Pre-trained MobileNetV2 | 68.30% | 0.6925 | 68.65% | 0.6871 | 0.6878 | 0.6830 | 0.6830 | 0.6830 | 0.6830 |
| Pre-trained EfficientNetB0 | 3.50% | 0.0018 | 5.00% | 0.0034 | 0.0012 | 0.0350 | 0.0024 | 0.0024 | 0.0024 |
| Teachable Machine | 99.88% | 0.0032 | ~96.06% | 0.9606 | 0.9647 | 0.9610 | 0.9610 | 0.9989 | 0.9989 |
| CSC_120_Image_Classifier_LW3 | 58.46% | 1.3401 | 51.90% | 1.5955 | 0.0094 | 0.0116 | 0.0100 | 0.4743 | 0.4743 |
| CSC_120_Image_Classifier_Improved | 99.35% | 0.0234 | 86.50% | 0.7286 | 0.0107 | 0.0128 | 0.0110 | 0.4748 | 0.4748 |
| **good_model (Fine-tuned)** | **95.35%** | **0.4515** | **98.00%** | **0.3784** | **0.9957** | **0.9800** | **0.9876** | **0.9995** | **0.9995** |

> **Winner:** `good_model.keras` (Fine-tuned MobileNetV2) — Best across all metrics.

Final Reflection Answers
A. Model Performance
1. Which pre-trained model achieved the highest accuracy? Why?
MobileNetV2 (68.65%) — It uses depthwise separable convolutions (fewer params = less overfitting), while ResNet50 is too deep for the dataset size and EfficientNetB0 failed due to missing preprocessing.
2. Which model had the lowest performance? What could be the reason?
EfficientNetB0 (5.00%) — Missing efficientnet.preprocess_input() caused the model to receive incorrectly normalized input, making ImageNet weights useless.
3. How did loss values compare across models?
ResNet50 had deceptively low loss (0.1010) but terrible accuracy — it was confident but wrong. MobileNetV2 had balanced loss (~0.69). good_model had train loss (0.4515) > test loss (0.3784), indicating slight underfitting.
B. Evaluation Metrics
4. Why is accuracy not enough to evaluate a model?
Accuracy hides class imbalance and error types. In medical plants, misclassifying a toxic plant as edible (false negative) is dangerous. Precision, Recall, and F1-Score reveal the full picture.
5. Which model had the best F1-Score? What does it indicate?
good_model (0.9876) — Excellent balance between Precision (0.9957) and Recall (0.9800). Near-perfect harmonic mean, suitable for deployment.
6. How did Precision and Recall differ across models?

| Model           | Precision  | Recall     | Interpretation     |
| --------------- | ---------- | ---------- | ------------------ |
| ResNet50        | 0.0964     | 0.1790     | Random guessing    |
| MobileNetV2     | 0.6878     | 0.6830     | Balanced, moderate |
| EfficientNetB0  | 0.0012     | 0.0350     | Complete failure   |
| **good\_model** | **0.9957** | **0.9800** | **Excellent**      |


C. Confusion Matrix Analysis

7. Which classes were frequently misclassified?
Morphologically similar plants (e.g., Lantana camara vs Globe Amaranth) and classes with fewer samples (Muskmelon: 243 images). The "Test" artifact class (2 images) should be removed.

8. What patterns did you observe?
Strong diagonal dominance (correct classifications), sparse off-diagonal (few errors), and minor confusion clusters between visually similar species.
D. ROC and AUC
9. Which model had the highest AUC score?
good_model (0.9995), followed by Teachable Machine (0.9989).

10. What does AUC tell us?
AUC measures discriminative ability across all thresholds. 0.9995 means 99.95% probability of correctly ranking a positive vs negative sample — near-perfect class separability.

E. Explainability (Grad-CAM)

11. What did Grad-CAM reveal?
MobileNetV2 focused on leaf textures, veins, and flower structures — relevant botanical features. ResNet50 had scattered attention. EfficientNetB0 showed no meaningful focus.

12. Did the model focus on relevant regions?
Yes — good_model localized on leaf margins, petal arrangements, and stem textures while avoiding background noise.

13. Which model produced the most meaningful heatmaps?
MobileNetV2 (good_model) — Clear, consistent localization on discriminative plant parts with confidence-correlated intensity.

F. Model Comparison & Improvement

14. Which model for deployment? Why?
good_model (Fine-tuned MobileNetV2) — 98% accuracy, 0.9876 F1, 0.9995 AUC, ~14MB size, fast inference, and interpretable Grad-CAM.

15. How to further improve?
Data augmentation (rotation, zoom, brightness)
Class balancing (weighted loss for Muskmelon)
Ensemble with EfficientNetB3
Unfreeze 30-40 layers instead of 20
Remove "Test" artifact class
Test-time augmentation

G. Real-World Application

16. Real-world applications?
Mobile app for farmers, educational tool for students, pharmaceutical screening, conservation monitoring, and integration with your EcoTrack IoT system.

17. Risks of inaccurate deployment?
Toxic misidentification (poisoning), economic loss (ineffective remedies), legal liability, ecosystem damage, and user trust erosion.

18. Mobile/web app integration?

[Camera] → [224×224 Preprocessing] → [TensorFlow Lite] → [Prediction + Grad-CAM]
    → [Plant Database] → [Medicinal Properties / Toxicity Warning]
