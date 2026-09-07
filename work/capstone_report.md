# Capstone Report — Content Refresh Prioritization

- **Author:** Mayur Kharche
- **Lane:** Content Refresh Prioritization
- **Repo:** https://github.com/mayurkharche01/Internship-starter-flyrank
- **Date:** September 2026


## 0. Abstract

This study asks whether a machine-learning model can improve the prioritization of content refresh opportunities compared with a transparent baseline. The analysis uses the anonymized FlyRank ML Internship dataset containing 30,000 rows and 44 source columns, with label-derived and identifier fields excluded from predictive use. A Random Forest classifier was evaluated using a client-held-out test design and compared with the Week-4 baseline on the same evaluation task. In the Week-5 notebook evaluation, the Random Forest measured ROC-AUC of 1.0000, Average Precision of 1.0000, and Precision@50 of 1.0000, compared with baseline values of 0.5045, 0.4999, and 0.3200 respectively. The resulting ranking is intended for decision-support: it helps a human reviewer prioritize content for investigation or refresh review, while the unusually strong model results require cautious interpretation and further validation before any production use.

## 1. Problem framing

### Decision supported

This project supports the decision of which content items should receive review attention first when editorial or analytical capacity is limited.

### Unit of analysis

The unit of analysis is an anonymized content/page record.

### Output

The workflow produces a ranking score that can be converted into a prioritized content-review queue.

### Human action

A FlyRank editor or analyst can use the ranking to:

1. review the highest-priority content first;
2. investigate whether the content appears to need attention;
3. decide whether a refresh is appropriate;
4. defer items where the evidence is weak or ambiguous; and
5. monitor approved actions after implementation.

The model does not automatically decide that content should be changed.

### Cost of a wrong call

A false positive can consume limited editorial time on content that did not require intervention.

A false negative can cause a potentially useful content-refresh opportunity to receive lower priority and therefore be reviewed later.

The ranking is therefore intended to improve allocation of review attention rather than automate editorial decisions.

### Why data and machine learning help

The Week-4 baseline provides a transparent starting point based on observable content and performance signals.

A machine-learning model can combine multiple signals and potential interactions between them instead of relying only on a fixed hand-written rule.

The purpose of the model is therefore to test whether the available signals provide a stronger prioritization signal than the transparent baseline.

This is a decision-support problem. The analysis does not claim to predict Google's algorithm and does not establish that a recommended content refresh will cause improved search performance.

## 2. Data safety

### Data used

The project uses the anonymized FlyRank ML Internship dataset:

`data/raw/content_refresh_anonymized.csv`

The Week-5 modeling notebook reported:

- 30,000 rows
- 44 source columns

The dataset is an anonymized internship dataset used for analytical and machine-learning development.

### Deliberately excluded fields

The target is derived from the trend information documented in the project pipeline:

`is_declining_label = (trend_direction == "down")`

Because `trend_direction` directly defines the target, it must not be used as a predictive feature.

`trend_pct` is also treated as leakage-sensitive because it directly describes the trend associated with the target.

Therefore, the following fields are deliberately excluded from predictive use:

- `trend_direction`
- `trend_pct`

Pseudonymous identifiers are used for grouping and tracing records where necessary, but they are not treated as predictive features.

In particular, client identifiers are used for the client-held-out validation design rather than as model predictors.

### Leakage risks considered

The main leakage risk is allowing information that directly or indirectly defines the target to enter the feature set.

The feature-leakage review therefore considered:

- label-derived fields;
- future information;
- trend variables;
- pseudonymous IDs; and
- features that could provide an identifier-based shortcut.

The target-derived fields `trend_direction` and `trend_pct` were kept outside the predictive feature set.

### Public-safety handling

The final report does not intentionally expose client names, private queries, client domains, private URLs, credentials, or other client-identifying information.

Pseudonymous IDs are treated as technical identifiers rather than meaningful business features.

The public-facing analysis focuses on the methodology, aggregate results, validation design, and decision-support recommendations.

## 3. Baseline

### Baseline approach

The Week-4 baseline is a transparent ranking score designed to identify content that may deserve review.

The baseline produces an interpretable output containing fields including:

- `rank`
- `content_id`
- `score`
- `reason_code`
- `action`
- `days_since_last_update`
- `impressions_90d`

The reason code and action provide a human-readable explanation for why an item appears in the queue.

For example, the generated output contains the reason code:

`STALE_VISIBLE`

with the associated action:

`REFRESH_REVIEW`

This should be interpreted as a reason to investigate the content, not as proof that refreshing it will improve performance.

### Why the baseline is fair

The baseline is a useful benchmark because it represents a transparent approach that can be understood and reproduced without a learned model.

The Random Forest therefore has to demonstrate improvement over an existing decision rule rather than simply producing a standalone metric.

### Baseline results

The Week-5 comparison reported the following baseline metrics:

| Metric | Week-4 Baseline |
|---|---:|
| ROC-AUC | 0.5045 |
| Average Precision | 0.4999 |
| Precision@50 | 0.3200 |

The baseline ROC-AUC of 0.5045 is close to 0.50, indicating limited discrimination under the evaluated task.

The baseline Average Precision was 0.4999.

The baseline Precision@50 was 0.3200.

These values form the benchmark for the Random Forest comparison.

## 4. Model / analysis

### Method

The Week-5 analysis uses a Random Forest classifier.

Random Forest was selected because it can capture nonlinear relationships and interactions among multiple content and performance-related features without requiring every relationship to be linear.

The model generates a prediction score that can be used to rank content for review.

### Target / proxy definition

The operational target is:

`is_declining_label = (trend_direction == "down")`

This target is a proxy for the content-prioritization task and should not be interpreted as a direct measure of future business value or as evidence that a content change will cause an improvement.

### Feature list

The Week-5 model workflow recorded the following feature groups:

- `competition_level`
- `content_type`
- `main_intent`
- `provider_used`
- `model_used`
- `age_tier`
- `freshness_tier`
- `word_count_tier`
- `char_count_tier`
- `impression_tier`
- `position_tier`

These features represent permitted content, intent, freshness, visibility, position, and related characteristics.

### Features left out deliberately

The model deliberately excludes target-derived fields including:

- `trend_direction`
- `trend_pct`

Pseudonymous identifiers are also not used as substantive predictive features.

Client identifiers are used for grouped evaluation rather than prediction.

### Modeling principle

The model is intended to rank records for human review.

It is not intended to automatically prescribe a content change or claim a causal relationship between a recommendation and future search performance.

## 5. Evaluation

### Split design

The Week-5 workflow uses a client-held-out evaluation design.

The reported test population contains:

- **6,163 testing rows**
- **7 testing clients**

The notebook also reports:

**PASS: No client appears in both train and test.**

Grouping by client reduces the risk that the evaluation is artificially inflated because records belonging to the same client appear in both training and testing.

### Model results

The Random Forest measured:

| Metric | Random Forest |
|---|---:|
| ROC-AUC | 1.0000 |
| Average Precision | 1.0000 |
| Precision | 1.0000 |
| Recall | 1.0000 |
| F1 | 1.0000 |
| Precision@50 | 1.0000 |

### Model versus baseline

| Metric | Week-4 Baseline | Random Forest | Absolute difference |
|---|---:|---:|---:|
| ROC-AUC | 0.5045 | 1.0000 | +0.4955 |
| Average Precision | 0.4999 | 1.0000 | +0.5001 |
| Precision@50 | 0.3200 | 1.0000 | +0.6800 |

The Random Forest therefore measured substantially higher performance than the baseline on the reported held-out evaluation.

### Error analysis

The reported Random Forest evaluation contains no observed classification errors under the metrics reported by the Week-5 notebook because Precision, Recall, and F1 were all measured at 1.0000.

This result should not be interpreted as evidence that future data will contain no errors.

Instead, the absence of observed errors is itself a reason for additional validation.

Useful next checks include:

- testing on a later time period;
- evaluating additional unseen clients;
- checking feature distributions;
- performing feature-ablation tests;
- testing for hidden leakage; and
- assessing ranking stability.

### Base-rate requirement

Precision@50 should be interpreted alongside the positive-class base rate of the evaluation set.

The final fresh-run target rate should be recorded here before submission so that the Precision@50 result is not interpreted without its class-prevalence context.

### Interpretation of the measured result

The current Week-5 notebook result is unusually strong because every reported Random Forest metric is 1.0000.

The appropriate conclusion is therefore that the model measured perfect performance on the defined held-out evaluation, not that the model will achieve perfect performance in production.

## 6. Interpretation

### What the analysis found

The Random Forest measured substantially stronger discrimination than the transparent Week-4 baseline on the reported held-out evaluation.

The result suggests that the permitted feature representation contains useful information for distinguishing records according to the operational decline target.

The model combines multiple characteristics rather than relying on a single manually defined rule.

### Feature interpretation

The feature set includes signals related to:

- content type;
- search intent;
- competition;
- provider/model context;
- content age;
- freshness;
- word count;
- character count;
- impressions; and
- position.

These should be interpreted as observed predictive signals rather than causal drivers.

For example, if freshness contributes strongly to the model ranking, the correct interpretation is that freshness is associated with the target in the modeled data. It does not prove that updating a page will cause its performance to improve.

### Baseline interpretation

The baseline provides an understandable operational comparison.

Its reason codes, such as `STALE_VISIBLE`, make it possible for a reviewer to understand why an item was prioritized.

This interpretability remains valuable even when the learned model measures stronger performance.

### Main surprise

The largest result is the perfect measured Random Forest performance.

ROC-AUC, Average Precision, Precision, Recall, F1, and Precision@50 were all reported as 1.0000.

This is unusually strong and therefore increases the need for validation rather than reducing it.

### Negative result

The Week-4 baseline ROC-AUC of 0.5045 was close to random discrimination.

This is a useful negative result because it establishes that the transparent baseline did not provide strong discrimination under the evaluated task.

### What the model does not establish

The model does not establish:

- causality;
- future production performance;
- guaranteed search improvement;
- guaranteed benefit from a content refresh;
- permanent performance across different clients or time periods; or
- knowledge of Google's ranking algorithm.

The strongest supported statement is that the Random Forest measured substantially higher performance than the baseline on the defined evaluation.

## 7. Recommendation

The model and action-playbook outputs should be used as a prioritized human-review workflow.

### Ranked actions

| Rank | Action | How an editor should use it | Confidence |
|---:|---|---|---|
| 1 | Review highest-priority stale and visible content | Inspect the content and determine whether a refresh is justified | High priority for review |
| 2 | Investigate other high-ranked items | Review the supporting signals before taking action | Directional |
| 3 | Review ambiguous or lower-confidence recommendations manually | Do not make an automated change solely from the model score | Human judgment required |
| 4 | Monitor approved changes | Measure observed outcomes after implementation | Required |
| 5 | Revalidate the model periodically | Check whether ranking performance remains stable across new data | Required |

### How a FlyRank editor could use the output tomorrow

A practical workflow would be:

1. Open the highest-ranked review candidates.
2. Check the associated reason code and supporting signals.
3. Inspect the content manually.
4. Determine whether the content is genuinely outdated or otherwise warrants investigation.
5. Approve, modify, defer, or reject the recommended action.
6. Record approved actions.
7. Monitor subsequent observed performance.
8. Feed those observations into future validation.

### Example

The action queue includes the reason code:

`STALE_VISIBLE`

with the action:

`REFRESH_REVIEW`

The correct operational interpretation is:

> Review this content for a possible refresh.

It is not:

> Refresh this content because the model guarantees improvement.

### Confidence and limitations

Confidence is high that the Random Forest measured higher performance than the baseline on the reported evaluation.

Confidence is lower that the perfect measured score will generalize unchanged to future production data.

The recommendation is therefore to use the ranking as **decision-support with human review**, not as an autonomous action system.

### Monitoring

The project includes:

`work/outputs/action_playbook_monitoring.csv`

This artifact can support follow-up monitoring after approved actions.

The key objective of monitoring is to determine whether recommendations remain useful when applied to new observations rather than assuming that historical model performance will automatically continue.

## 8. Reproducibility

### Repository

The project repository contains the notebooks, code, figures, outputs, and capstone report required to reproduce the analytical workflow.

### Fresh-clone setup

From a fresh clone:

```bash
git clone https://github.com/mayurkharche01/Internship-starter-flyrank.git
cd Internship-starter-flyrank
python -m venv .venv

## 9. Acknowledgments & data credit

## 9. Acknowledgments & data credit

Built on the [FlyRank ML Internship dataset](https://flyrank.ai).
---

> **Claims checklist before submitting:** observed / measured / directional / decision-support
> **Metrics vs. base rate:** report your task's base rate (majority-class %) next to any
> precision@K or accuracy — a high score can just be a high base rate. AUC / lift over
> baseline are the honest discrimination numbers.
> language everywhere · no causal claims without an experiment or causal design · no
> "predicted Google's algorithm" · no client-identifying details · numbers in this report
> match a fresh re-run.
