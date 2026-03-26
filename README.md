# Automated Bug Triage Using NLP

**Link to Jupyter Notebook:** [(https://github.com/krishnavss/bug-triage-capstone-final/blob/main/24.Final_Report.ipynb)]

## 1. The Problem
**Research Question:** Can we accurately predict the appropriate engineering
routing team (Component) for a newly submitted software bug based solely on
its natural language description?

**Business Justification:** Manual bug triage is a massive drain on senior
engineering resources. Technical leads waste countless hours reading,
interpreting, and routing tickets, which delays the resolution of critical
customer-facing issues. In large-scale, high-availability infrastructure
environments, automating the triage process can save thousands of
engineering hours annually, speed up incident response times, and allow
developers to focus strictly on resolving systemic issues rather than
administrative routing.

## 2. The Data
**Sources:** Historical, pre-labeled bug reports were sourced from the
Eclipse Bug Dataset. After rigorous data cleaning, deduplication, and
outlier removal, the final dataset used for this analysis contains 9,842
high-quality bug reports.

## 3. The Techniques
* **Data Cleaning & Exploratory Data Analysis (EDA):** * Handled missing values and removed duplicate bug reports to prevent
    data leakage.
  * Conducted word-count outlier analysis to filter out anomalous
    entries (descriptions with fewer than 3 words) that lacked sufficient
    context for NLP.
  * Visualized the distribution of the target variable (`Component`),
    revealing a severe class imbalance. Routing teams like 'UI' and
    'Debug' receive the vast majority of bug reports, while specialized
    teams receive significantly fewer.
* **Feature Engineering:** * Normalized the raw natural language text by converting it to
    lowercase, stripping punctuation, and applying TF-IDF vectorization to convert text
    into mathematical weightings.
* **Machine Learning Modeling:** * Trained and evaluated multiple classification models, including
    Logistic Regression, Random Forest, and a Support Vector Classifier
    (LinearSVC).
  * Utilized `GridSearchCV` to systematically tune hyperparameters and
    cross-validate model performance.

## 4. Modeling & Evaluation
Because the dataset exhibits severe class imbalance, standard "Accuracy" is
a misleading metric; a model could score highly simply by always guessing
the majority queue. Instead, models were evaluated using the **Macro
F1-Score**, which ensures minority queues are weighted equally in the
evaluation.

* **Baseline Logistic Regression:** Achieved a baseline Macro F1-Score of
  0.3677. While it performed well on highly represented classes (Debug F1:
  0.75, UI F1: 0.63), it struggled significantly with niche components.
* **Linear SVC (Tuned):** Support Vector Classifiers are highly effective
  for high-dimensional, sparse text data. Through hyperparameter tuning
  (C=1), this model optimized the decision boundaries and slightly
  outperformed the baseline with a **Macro F1-Score of 0.3702**, making
  it the strongest candidate.
* **Random Forest (Tuned):** Explored non-linear tree-based decisions via
  Grid Search cross-validation. As is common with sparse TF-IDF matrices,
  the tree-based architecture struggled to capture complex word
  associations efficiently, resulting in the lowest **Macro F1-Score
  of 0.3397**.

## 5. Summary of Findings & Actionable Insights
* **Vocabulary is a Strong Predictor:** The NLP pipeline successfully
  proved that the raw text of a bug description contains enough semantic
  signal to predict the correct engineering component, validating the
  business case for automated triage.
* **The "Minority Queue" Challenge:** While the models perform
  exceptionally well at routing tickets to large teams, they struggle
  with niche components that lack historical data.
* **Model Selection:** The Tuned Linear SVC outperformed tree-based
  models on the TF-IDF vectorized text, making it the most viable,
  lightweight candidate for production deployment.

## 6. Next Steps & Recommendations
1. **Deploy as a "Triage Assistant":** Rather than fully automating the
   routing immediately, deploy the model to suggest the top 3 most likely
   queues to the human triage lead. This builds operational trust in the
   system while immediately reducing cognitive load.
2. **Implement Threshold Routing:** Configure the system so that if the
   model's confidence score is above a strict threshold (e.g., 85%), it
   auto-routes the ticket. If it falls below, it holds the ticket for
   manual review.
3. **Data Augmentation:** To improve routing for smaller engineering teams,
   future iterations should synthesize historical data or leverage
   pre-trained large language models (LLMs) to better understand niche
   technical context with fewer examples.
