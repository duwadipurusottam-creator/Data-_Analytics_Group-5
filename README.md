## Files

### Datasets
- `Training Dataset.arff` — original UCI dataset (11,055 records, 30 features)
- `phishing_clean.arff` — after RenameNominalValues recoding (-1/0/1 → Phishing/Suspicious/Legitimate)
- `phishing_clean.csv` — CSV export for IBM Cognos dashboard
- `phishing_top9.arff` — reduced to 9 highest information-gain features

### Models
- `J48_full.model` — decision tree, all 30 features (95.88%)
- `J48_pruned.model` — minNumObj=100, 24 nodes (91.90%), used as report figure
- `J48_top9.model` — decision tree, 9 features (94.30%)
- `NaiveBayes.model` — probabilistic classifier (92.98%)
- `RandomForest.model` — 100-tree ensemble (97.27%) — recommended model

### Screenshots
Evidence of data preparation and classifier runs in Weka.

## Method
All models evaluated using 10-fold stratified cross-validation on 11,055 instances.
Feature ranking performed with InfoGainAttributeEval + Ranker.

## Results

| Model | Features | Accuracy | Phishing Recall | False Negatives |
|---|---|---|---|---|
| RandomForest | 30 | 97.27% | 0.961 | 191 |
| J48 | 30 | 95.88% | 0.942 | 283 |
| J48 | 9 | 94.30% | 0.920 | 393 |
| NaiveBayes | 30 | 92.98% | 0.904 | 471 |
| J48 (pruned) | 30 | 91.90% | 0.897 | 506 |

RandomForest recommended for deployment; J48 retained for interpretability.
