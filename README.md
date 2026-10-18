# Chest X-ray classification

This repository contains an experimental notebook for classifying chest X-rays as **NORMAL** or **PNEUMONIA**, plus a static HTML results/interface preview.

> **Important scope note:** Despite the legacy repository name, the notebook does **not** use COVID-19 labels or a COVID-19 dataset. It is not a COVID-19 detector and must not be used to diagnose or guide medical care.

## Project files

- `chest-x-ray-medical-diagnosis-with-cnn-densenet.ipynb` — custom CNN and DenseNet121 experiments, with separate augmentation-free validation/test preprocessing and expanded evaluation.
- `index.html` — standalone interactive preview of the saved metrics and an image-upload UI mockup. It does not run model inference.

## Dataset

The notebook expects the Kaggle [Chest X-Ray Images (Pneumonia)](https://www.kaggle.com/datasets/paultimothymooney/chest-xray-pneumonia) directory layout at `../input/chest-xray-pneumonia/chest_xray/`, with `train`, `val`, and `test` folders containing `NORMAL` and `PNEUMONIA` subfolders.

The counts embedded in the original notebook outputs were:

| Split | NORMAL | PNEUMONIA | Total |
|---|---:|---:|---:|
| Train | 1,341 | 3,875 | 5,216 |
| Validation | 8 | 8 | 16 |
| Test | 234 | 390 | 624 |

The validation split is very small. The notebook uses per-image standardization, applies random augmentation only to training images, calculates inverse-frequency class weights, and reports metrics for both 0.50 and 0.70 probability thresholds.

## Previously saved test outputs

These figures are transcribed from the notebook's **pre-edit saved outputs**. They are shown for traceability, not as results of the corrected notebook: the prior code applied random augmentation to validation and test images and used reversed class weights. Re-run the updated notebook on the dataset to generate new results. The original per-image probability scores and model weights are not included, so threshold-independent ROC-AUC and average precision cannot be recovered from these saved outputs.

“Positive” means **PNEUMONIA**. Sensitivity is pneumonia recall; specificity is normal recall.

| Model | Threshold | Accuracy | Sensitivity | Specificity | Precision | F1 | Balanced accuracy |
|---|---:|---:|---:|---:|---:|---:|---:|
| Custom CNN | 0.50 | 64.42% | 100.00% | 5.13% | 63.73% | 77.84% | 52.56% |
| Custom CNN | 0.70 | 65.54% | 99.74% | 8.55% | 64.51% | 78.35% | 54.15% |
| DenseNet121 | 0.50 | 56.73% | 37.69% | 88.46% | 84.48% | 52.13% | 63.08% |
| DenseNet121 | 0.70 | 53.85% | 30.51% | 92.74% | 87.50% | 45.25% | 61.62% |

The saved DenseNet121 confusion matrix at threshold 0.50 (rows are actual classes, columns are predicted classes):

|  | Predicted NORMAL | Predicted PNEUMONIA |
|---|---:|---:|
| **Actual NORMAL** | 207 | 27 |
| **Actual PNEUMONIA** | 243 | 147 |

The low pneumonia sensitivity means many pneumonia cases were missed in these old outputs. Neither the scores nor the preview page establish clinical performance.

### Confusion matrix visualization

This Matplotlib heatmap is generated from the pre-edit DenseNet121 counts above; it is not a result from the corrected notebook.

![Pre-edit DenseNet121 test confusion matrix at threshold 0.50](Results%20Obtained/densenet121_saved_test_confusion_matrix.png)

When the notebook is run with the dataset and trained model, it also plots test X-rays labeled with their actual class, predicted class, and pneumonia probability. It saves that image montage and the current run's confusion matrices to `Results Obtained/test_sample_predictions.png` and `Results Obtained/test_confusion_matrices.png`.

## Research and references

- Kermany et al., [“Identifying Medical Diagnoses and Treatable Diseases by Image-Based Deep Learning”](https://doi.org/10.1016/j.cell.2018.02.010), *Cell* (2018). Relevant background for the pediatric chest-radiograph pneumonia dataset; this is not a COVID-19 study.
- Huang et al., [“Densely Connected Convolutional Networks”](https://doi.org/10.1109/CVPR.2017.243), CVPR (2017). Introduces DenseNet, the pretrained architecture used in one notebook experiment.
- Roberts et al., [“Common pitfalls and recommendations for using machine learning to detect and prognosticate for COVID-19 using chest radiographs and CT scans”](https://doi.org/10.1038/s42256-021-00307-0), *Nature Machine Intelligence* (2021). Discusses validation pitfalls in COVID-19 imaging research; it does not validate this repository’s model.

## Run and preview

1. Place the dataset in the directory layout above and install Jupyter, TensorFlow/Keras, NumPy, pandas, Matplotlib, Seaborn, and scikit-learn.
2. Open and run `chest-x-ray-medical-diagnosis-with-cnn-densenet.ipynb`. Training can be compute-intensive; the notebook downloads ImageNet weights for DenseNet121. The final evaluation cell displays the test confusion matrices and labeled test-image predictions with Matplotlib, and saves them under `Results Obtained/`.
3. Open `index.html` directly in a browser to explore the saved metric tables and confusion matrices. Selecting an image only previews it locally in the browser; it does not upload the image or produce a prediction.

This is an educational experiment, not a validated clinical system. The test set is small and class-imbalanced, the validation set is especially small, and external validation would be required before making any medical claims.
