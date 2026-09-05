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

## Descriptive Analysis — Cognos Dashboard

Six visualisation elements built on the five highest information-gain
features, plus overall class distribution. Filters applied on
`age_of_domain`, `Google_Index` and `Domain_registeration_length`.

### Findings

**Certificate and anchor behaviour dominate.** `SSLfinal_State` and
`URL_of_Anchor` produce sharp separations between phishing and legitimate
sites. `web_traffic` and `having_Sub_Domain` produce much milder splits,
visually confirming the information-gain ranking (0.499 and 0.477 versus
0.115 and 0.110).

**Anchor links: extremes carry the signal.** Sites with `URL_of_Anchor` =
Phishing are almost entirely phishing (3,240 vs ~40). However, "Suspicious"
anchor links are more common on legitimate sites (3,829 vs 1,505), so
detection rules should trigger on extreme values rather than treating the
middle category as a warning sign.

**Domain age shifts the base rate.** Unfiltered, the dataset is 44.3%
phishing (4,898 / 11,055). Filtered to domains under six months old
(`age_of_domain` = Phishing), this rises to 50.7% (2,632 / 5,189). Domain
age is a weak standalone predictor (information gain 0.011) but materially
changes the prior probability, supporting a lower flagging threshold for
newly registered domains.

### Recommendation
Prioritise SSL certificate validation and anchor-link analysis when writing
detection rules. Apply stricter thresholds to recently registered domains.
