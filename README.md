# Early Warning for Secondary School Maths

Which students are heading for a fail, and can we tell in week one rather than week thirty?

**Data.** UCI Student Performance (maths class). 395 students, two Portuguese secondary schools, 32 features covering demographics, family background, study habits and attendance. Grades run 0 to 20, pass mark is 10.

**Stack.** Python, pandas, scikit-learn, matplotlib, seaborn.

---

## The result

| | |
|---|---|
| Cross validated ROC AUC | **0.691** (sd 0.050, 30 fits) |
| Out-of-fold ROC AUC, all 395 students | **0.687** |
| Students who fail | 32.9% |
| Hit rate in the top 10% of the risk ranking | **65.0%**, which is **1.98x** better than picking at random |
| Hit rate in the top 25% | 55.6%, catching 42% of all eventual failures |

Strongest pre-term signals: past class failures, absences, whether the student already receives school support, and how often they go out.

---

## The point of the project

The obvious model predicts the final grade from everything in the file, including the first and second period grades. It scores **R squared 0.87**, which looks great on a CV and is worthless in a school. `G2` is the maths grade from a few weeks earlier. By the time it exists, the teacher already knows who is in trouble. A model that needs it is answering a question nobody asked.

Strip those two columns out and the same approach drops to **R squared 0.30**. That is the honest number for a prediction made before teaching starts, and it is the only version that could change what anyone does.

So the project rebuilds the task as an early warning screen: rank students by risk using only information available in week one, and hand the top of that list to the head of department.

## Two things I did differently

**Evaluation.** With 395 rows, a single held-out test set leaves about 99 students and the estimate swings badly. I ran it that way first and got a held-out AUC more than 0.1 below the cross validated figure, which says more about the split than the model. The notebook uses repeated stratified cross validation for model selection and out-of-fold predictions across all 395 students for the final numbers, so every student is scored by a model that never saw them.

**Operating point.** A probability threshold is the wrong unit. A school does not decide "support everyone below 0.62", it decides "we have capacity for twenty students". The notebook ranks by risk and reports a cumulative gains curve, so the decision becomes how far down the list capacity allows you to go.

## Honest limitations

395 students, two schools, one country, one year. Portuguese 0 to 20 grading does not map cleanly onto other systems. Study time and alcohol use are self-reported and self-reports about effort lean optimistic. The `schoolsup` variable is confounded, because schools assign extra support to students who are already struggling, so its negative coefficient is selection and not an effect. None of these relationships are causal.

An AUC of 0.69 sorts better than chance and nowhere near perfectly. Deploying something like this in a real school also raises a fairness question that a better AUC does not answer: a model that flags students partly on family background can turn into a label a child carries for years.

## Files

```
student-performance.ipynb    full analysis, outputs included
data/student-mat.csv         source data
figures/                     exported charts
metrics.json                 headline numbers
```

## Running it

```bash
pip install pandas numpy scikit-learn matplotlib seaborn jupyter
jupyter notebook student-performance.ipynb
```

**Source.** P. Cortez and A. Silva, *Using Data Mining to Predict Secondary School Student Performance*, UCI Machine Learning Repository.

---

Part of my data portfolio: https://ayeshabibi8027.github.io
Ayesha Bibi, MS Data Science, Air University Islamabad.
