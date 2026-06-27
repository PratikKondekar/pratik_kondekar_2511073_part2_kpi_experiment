# Hypothesis Test Notes — Onboarding & Activation Campaign Experiment

## 1. Metric Being Tested

**Paid Conversion Rate** — the proportion of users in each group who converted to a paid plan (`converted_to_paid = 1`).

## 2. Reason for Choosing This Metric

Paid conversion rate is the North Star metric for this experiment (see `README.md` and `outputs/recommendation_memo.md` for full justification). It is the metric most directly tied to revenue generation and is the ultimate measure of whether the new onboarding/activation campaign experience succeeds at turning signups into paying customers. All other funnel metrics (landing page visits, trial starts, onboarding completion) are supporting/diagnostic metrics that explain *why* conversion moved, but conversion itself is the business-critical outcome leadership needs to evaluate.

## 3. Null Hypothesis (H0)

> The paid conversion rate of the Treatment group is **less than or equal to** the paid conversion rate of the Control group.
>
> H0: p_treatment ≤ p_control

## 4. Alternate Hypothesis (H1)

> The paid conversion rate of the Treatment group is **greater than** the paid conversion rate of the Control group.
>
> H1: p_treatment > p_control

## 5. One-Tailed or Two-Tailed

**One-tailed test.** The business question is specifically whether the new campaign experience *improves* conversion — leadership is not interested in detecting a decrease for the purpose of this primary test (a decrease would simply mean "do not launch," which is already covered by failing to reject H0). The one-tailed framing matches the directional nature of the business decision.

> Note: For transparency, the underlying z-test calculation in the workbook (`analysis/experiment_analysis.xlsx`) was run as a standard two-proportion z-test; the reported p-value here is interpreted in a one-tailed context relative to the stated H1 (the two-tailed p-value of ~0.0011 already comfortably clears the one-tailed significance bar, so the conclusion does not change).

## 6. Significance Level Used

**Alpha = 0.05** (95% confidence) — the standard threshold for business experimentation decisions, balancing the risk of a false positive against the practicality of shipping product changes at a reasonable pace.

## 7. Test Statistic & Result

| Item | Value |
|---|---|
| Test type | Two-proportion z-test |
| Control conversions | 22 / 690 = 3.19% |
| Treatment conversions | 50 / 710 = 7.04% |
| Absolute lift | +3.85 percentage points |
| Relative lift | +120.9% |
| Z-statistic | 3.264 |
| P-value | 0.0011 |
| 95% CI of difference | [0.0156, 0.0615] |

See visual evidence in `screenshots/hypothesis_test_output.png`.

## 8. Decision Rule

If **p-value < 0.05**, reject the null hypothesis and conclude the Treatment group's conversion rate is significantly higher than Control's.

If **p-value ≥ 0.05**, fail to reject the null hypothesis — there would be insufficient evidence that Treatment outperforms Control.

## 9. Result & Interpretation Logic

The observed p-value (0.0011) is well below the 0.05 significance threshold, so **we reject the null hypothesis**. The data provides strong statistical evidence that the Treatment (new onboarding/activation campaign) experience increases the paid conversion rate compared to the Control (existing onboarding experience). The 95% confidence interval for the lift ([1.56pp, 6.15pp]) does not cross zero, reinforcing that this is very unlikely to be due to random chance alone.

## 10. Supporting Tests on Guardrail Metrics

To ensure the conversion lift isn't coming at an unacceptable cost, two additional two-proportion z-tests and one t-test were run (see `analysis/experiment_analysis.xlsx`, sheet "Statistical Tests"):

- **Support ticket rate:** Control 14.78% vs Treatment 24.79% — z = 4.69, p < 0.0001. **Statistically significant increase** — this is a meaningful guardrail concern.
- **Refund rate:** Control 0.00% vs Treatment 0.42% — z = 1.71, p = 0.087. Not statistically significant at α=0.05, but directionally worth monitoring since Control had zero refunds.
- **Engagement score:** Control 57.0 vs Treatment 62.9 — Welch's t-test, t = 7.93, p < 0.0001. **Statistically significant increase**, which is a positive guardrail signal (engagement improved, not declined).

## 11. Connection to the Business Decision

This hypothesis test result is the primary statistical evidence behind the recommendation in `outputs/recommendation_memo.md`. A statistically significant lift in conversion supports moving toward a "Launch" decision, but the significant increase in support ticket rate (a guardrail metric) means the recommendation is not a simple, unconditional "Launch" — it is weighed against operational cost and reviewed alongside segment-level results before a final call is made.
