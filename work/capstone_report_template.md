# Search Performance Decline: A Client-Grouped Content Risk Analysis

- **Author:**
- **Lane:**
- **Repo:**
- **Date:**

> Copy this file to `work/capstone_report.md` and fill it in as you build. Sections 1–8
> mirror the Pass / Needs-Work rubric axes, so nothing here is optional. Sections 0 and 9
> are **paper sections**: your deployed research paper must carry both, and they're here so
> you never rebuild them from memory at ship time.

## 0. Abstract

This study asks whether aggregated search-performance signals can help identify content items that may experience an impressions decline. The analysis uses pseudonymized FlyRank internship search data, including daily content performance, query-level visibility, and client history information. A Random Forest classifier was evaluated using a client-grouped split, with 34 training clients and 12 previously unseen testing clients. The model achieved 0.786 test accuracy and 0.754 macro F1 on 31,686 test rows. The resulting analysis is intended as directional decision support for prioritizing content review and monitoring, not as evidence of causation or a claim about search-engine ranking mechanisms.

## 1. Problem framing

This analysis supports the decision of which content items should be prioritized for review or monitoring when they show signals associated with possible impressions decline.

The unit of analysis is the content item. The output is a model-based risk signal that can be used to rank content for human review.

A FlyRank editor could use the ranked output to review higher-priority content first, investigate changes in search-performance signals, and decide whether monitoring or further content review is appropriate.

A wrong call can result in review time being spent on content that does not need attention, while a missed high-risk item could delay useful review. ML helps by consistently combining multiple search-performance signals into a repeatable prioritization workflow.

The output is decision support only and does not automatically trigger content changes or establish causation.

## 2. Data safety
The analysis uses the full pseudonymized FlyRank internship warehouse release.

The main data sources are the daily content-performance table, the 90-day content-query table, and the client dimension table used to understand available history.

The analysis uses aggregated content-level signals. Client names, domains, private queries, credentials, and raw exports are deliberately excluded from the public work.

Pseudonymous client and content IDs are used only to join records and, for validation, to keep observations from the same client separated between training and testing. They are not used as predictive features.

Potential leakage was considered by ensuring that features represent information available before the outcome being evaluated. Label-derived fields and future outcome information are not used as model features.

The analysis is presented using public-safe, anonymized and aggregated information.

## 3. Baseline

The transparent baseline for this experiment is the majority-class rule: always predict the most common outcome in the training data.

This is a fair baseline because it provides a simple reference point that does not use the engineered search-performance features. The Random Forest model is therefore evaluated against a model-free rule on the same client-grouped test split.

The available notebook results report the Random Forest performance as 0.786 test accuracy and 0.754 macro F1. The exact majority-class baseline percentage is not recorded in the current report artifacts, so it is not stated here rather than estimated or invented.

A final comparison should report the baseline accuracy alongside the model metrics when that value is available from the reproducible evaluation output.

## 4. Model / analysis

The analysis uses a Random Forest classifier because the task combines several engineered search-performance signals and the goal is to produce a repeatable prioritization signal.

The feature set includes previous-period impressions, query visibility, query concentration signals such as top-query share, and ranking-position volatility.

The target is defined as an impressions decline: a content item is classified as declining when impressions in the latest period are below 80% of the previous period.

The analysis deliberately excludes client identifiers as predictive features and excludes label-derived or future-period information to reduce leakage risk.

The model uses a fixed random seed for reproducibility. Its output is treated as a prioritization signal for human review rather than an automatic decision.

## 5. Evaluation

The evaluation uses GroupShuffleSplit with client_hash_id as the grouping variable. This prevents observations from the same client appearing in both training and testing, providing a more realistic test of generalization to previously unseen clients.

The evaluation contains 34 training clients and 12 testing clients, with 31,686 test rows.

| Metric | Random Forest |
|---|---:|
| Test accuracy | 0.786 |
| Macro F1 | 0.754 |
| Weighted F1 | 0.786 |

The model's performance should be interpreted as measured performance on this particular held-out client split. The current report artifacts do not contain the exact majority-class baseline value, so no baseline number is estimated here.

The main error consideration is that performance can vary across unseen clients and content types. The model should therefore be used for prioritization and human review rather than as an automatic decision rule.

## 6. Interpretation

The analysis indicates that previous-period impressions, query visibility and concentration, and ranking-position volatility can be combined into a repeatable signal for prioritizing content review.

The model is not interpreted as proving that any individual signal causes impressions to decline. Instead, these features provide measurable patterns that can help identify content items that deserve closer inspection.

One practical finding is that ranking-position volatility and concentrated query visibility are useful monitoring signals alongside historical impressions. These signals can help an editor understand whether a content item has stable or concentrated search visibility.

The analysis does not claim that every high-risk item will decline, nor that changing a page will improve its performance. The output is therefore best treated as directional evidence and decision support.

The current artifacts do not include a complete feature-importance table, so no specific feature ranking is claimed beyond the signals already defined in the experiment.

## 7. Recommendation
The ranked output supports the following action priorities:

1. **Review high-risk content first** — prioritize content items flagged by the model for possible impressions decline.
2. **Investigate ranking-position volatility** — monitor content showing unstable ranking-position signals.
3. **Review query concentration** — investigate content whose visibility depends heavily on a small number of queries.
4. **Prefer sufficient historical evidence** — give higher review priority to content with enough previous-period impressions to support a meaningful signal.
5. **Keep a human in the loop** — use the model as a review-prioritization tool rather than automatically changing content.

### Confidence and limits

Confidence is moderate for the tested client-grouped experiment, but the result should be treated as directional. Performance may differ for clients or content outside the evaluation sample. The output supports prioritization and monitoring; it does not guarantee future declines or improvement after an intervention.

## 8. Reproducibility

The analysis was developed in a notebook-based workflow using Python, DuckDB and scikit-learn.

The workflow reads the pseudonymized warehouse data, creates aggregated content-level features, defines the impressions-decline label, trains the Random Forest model, and evaluates it using client-grouped validation.

The experiment uses a fixed random seed so that the model evaluation can be reproduced consistently.

The main reproducibility artifact is the capstone notebook committed under `work/notebooks/capstone.ipynb`.

The reported evaluation uses the client-grouped split with 34 training clients, 12 testing clients and 31,686 test rows.

No datasets, credentials, private queries, client-identifying information, or raw exports are committed to the public repository.

The current report does not claim a sealed or blind evaluation because a separately committed sealed-frame artifact and metrics JSON are not documented in the available capstone materials.

## 9. Acknowledgments & data credit

Built on the FlyRank ML Internship dataset.

Data source: [FlyRank](https://flyrank.ai)

---

> **Claims checklist before submitting:** observed / measured / directional / decision-support
> **Metrics vs. base rate:** report your task's base rate (majority-class %) next to any
> precision@K or accuracy — a high score can just be a high base rate. AUC / lift over
> baseline are the honest discrimination numbers.
> language everywhere · no causal claims without an experiment or causal design · no
> "predicted Google's algorithm" · no client-identifying details · numbers in this report
> match a fresh re-run.
