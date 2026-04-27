Project Overview


This project applies machine learning to the NSL-KDD network intrusion dataset to classify network connections as normal or malicious. Beyond prediction accuracy, this analysis uses SHAP (SHapley Additive exPlanations) to explain why the model flags specific connections as attacks — translating model outputs into actionable security insights.
Business Question: Can we accurately detect malicious network connections, and can we explain the detection in terms a security analyst can act on?

Key Results

MetricValueAccuracy     95%

AUC Score               0.992

Attack Recall           94%

Attack Precision        96%

False Negatives         736 connections

False Positives         475 connections

Dataset
NSL-KDD — a benchmark network intrusion detection dataset containing 125,973 network connection records with 41 features capturing traffic behavior including protocol type, connection duration, byte counts, and error rates.
Attack categories in the dataset:

DoS (Denial of Service): neptune, smurf, pod, teardrop, back

Probe: portsweep, ipsweep, satan, nmap


R2L (Remote to Local): warezclient, warezmaster, guess_passwd, ftp_write


U2R (User to Root): buffer_overflow, rootkit, loadmodule, perl


For this model, labels are simplified to binary classification: normal vs. attack.

Approach


1. Data Preparation

Dropped metadata column (difficulty)

Label encoded categorical features: protocol_type, service, flag

Created binary target: 0 = normal, 1 = attack

2. Model Training

Model: Logistic Regression (chosen for interpretability and speed)


Split: 80/20 train/test with stratification

Scaling: StandardScaler applied before training

Why Logistic Regression first: Explainable coefficients establish a performance baseline before trying complex models

3. Evaluation

Classification report (precision, recall, F1)
Confusion matrix — understanding false negatives vs. false positives in a security context
ROC Curve — AUC of 0.992 confirms near-perfect class separation

4. SHAP Explainability

Global explanation: Summary plot showing which features drive attack predictions across 500 connections
Local explanation: Waterfall plot showing why one specific connection was flagged
False negative analysis: SHAP applied to a missed attack to explain why the model failed


Key Security Insights


FeatureDirectionSecurity Meaningsrv_serror_rate→ AttackHigh SYN error rate — consistent with SYN flood or port scannum_compromised→ AttackCompromised conditions detected in the connectionwrong_fragment→ AttackMalformed packets — common in evasion and DoS attackshot→ AttackSensitive file or directory access attemptscount→ AttackHigh volume of connections to same host — scanning behavior
False Negative Finding


One missed attack showed hot (sensitive file access) pushing strongly toward attack (+1.78 SHAP value), but 6 other features looked completely normal — causing the model to score it as normal traffic. This is consistent with a low-and-slow attack, where an adversary accesses sensitive resources while keeping all other connection behavior within normal ranges to avoid detection.

Limitations & Next Steps

Binary classification catches attacks but does not identify attack type

6% false negative rate means some attacks evade detection

Single-connection classification cannot detect behavioral patterns over time — a known limitation for advanced persistent threats

Next steps: Add Random Forest model for comparison, extend to multiclass classification, build an AI-powered analyst interface


Tools & Libraries

Python 3.12

Pandas, NumPy

Scikit-learn

SHAP

Matplotlib, Seaborn
