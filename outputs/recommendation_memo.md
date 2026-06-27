# Recommendation Memo — Onboarding & Activation Campaign Experiment

**To:** Leadership Team
**From:** Business Analyst
**Re:** Should the new onboarding/activation campaign (Treatment) be launched to all users?

---

## 1. Executive Summary

The Treatment experience (new onboarding & activation campaign) produced a statistically significant **120.9% relative lift in paid conversion rate** (3.19% → 7.04%, p = 0.0011) compared to the existing Control experience. Engagement scores and time-to-convert also improved meaningfully. However, the Treatment group also saw a statistically significant **68% relative increase in support ticket rate** (14.78% → 24.79%, p < 0.0001) and a markedly lower average revenue per converted user. Segment analysis shows the lift is not uniform — the West region and Basic plan users show materially weaker improvement than other segments.

**Recommendation: Launch only for selected segments**, while addressing the support ticket increase before a full rollout. See Section 8 for detail.

---

## 2. North Star Metric

**Paid Conversion Rate** (users converted to paid / total users) was selected as the North Star metric for this experiment. See `README.md` and `analysis/hypothesis_test_notes.md` for full justification — in short, it is the metric most directly tied to revenue and is the ultimate measure of whether the new onboarding experience turns signups into paying customers.

---

## 3. KPI Tree Explanation

The North Star (Paid Conversion Rate) breaks down into three primary drivers, each with two sub-drivers, plus four guardrail metrics monitored independently. See `outputs/kpi_tree.png` / `screenshots/kpi_tree_preview.png`.

- **Driver 1 — Top-of-Funnel Engagement** (Landing Page Visit Rate): broken down by Traffic Source and Device Type.
- **Driver 2 — Activation Quality** (Onboarding Completion Rate): broken down by Plan Type and Engagement Score.
- **Driver 3 — Conversion Experience** (Trial Start Rate): broken down by Region and Days to Convert.
- **Guardrails:** Refund Rate, Support Ticket Rate, Average Engagement Score, Revenue per Converted User.

This structure lets us trace *why* conversion moved (which funnel stage improved) rather than just observing that it moved, while the guardrails ensure the lift isn't coming at the expense of customer experience or revenue quality.

---

## 4. Experiment Result Summary

| Metric | Control | Treatment | Lift |
|---|---|---|---|
| User count | 690 | 710 | — |
| Landing page visit rate | 63.62% | 72.39% | +8.77pp |
| Trial start rate | 25.07% | 29.01% | +3.94pp |
| Onboarding completion rate | 15.65% | 21.13% | +5.48pp |
| **Paid conversion rate** | **3.19%** | **7.04%** | **+3.85pp (+120.9%)** |
| Avg revenue per user | $51.97 | $54.25 | +$2.28 |
| Avg revenue per converted user | $1,630.10 | $770.41 | −$859.69 |
| Refund rate | 0.00% | 0.42% | +0.42pp |
| Support ticket rate | 14.78% | 24.79% | +10.01pp |
| Avg engagement score | 57.0 | 62.9 | +5.9 |
| Avg days to convert | 8.9 days | 6.4 days | −2.5 days |

Every stage of the funnel improved under Treatment, and users who do convert do so faster and with higher engagement. Full detail in `outputs/experiment_summary.xlsx` and `analysis/experiment_analysis.xlsx`.

---

## 5. Hypothesis Test Interpretation

A two-proportion z-test on the primary metric (paid conversion rate) produced **p = 0.0011**, well under the 0.05 significance threshold — we reject the null hypothesis that Treatment performs no better than Control. The 95% confidence interval for the lift, [1.56pp, 6.15pp], does not cross zero, reinforcing this is a real effect and not noise. Full test detail in `analysis/hypothesis_test_notes.md` and `screenshots/hypothesis_test_output.png`.

---

## 6. Guardrail Analysis

Per Task 8, the recommendation is **not** based on conversion lift alone. Four guardrails were evaluated:

- **Support ticket rate** — increased significantly (14.78% → 24.79%, p < 0.0001). This is the most serious concern: nearly 1 in 4 Treatment users generated a support ticket, indicating the new experience may be confusing or creating friction somewhere in the funnel, even as it drives more conversions.
- **Refund rate** — increased from 0.00% to 0.42%. Not statistically significant (p = 0.087) given the small number of refund events, but worth monitoring since Control had zero refunds in this sample.
- **Average engagement score** — increased significantly (57.0 → 62.9, p < 0.0001). This is a positive signal; engaged users are not a concern.
- **Revenue per converted user** — dropped substantially ($1,630 → $770). This suggests Treatment is converting a higher *volume* of users, but with a lower-value mix (likely skewing toward Free/Basic plans rather than Premium — see segment analysis below). This is a real risk to revenue quality, not just a guardrail formality.

**Conclusion: Yes, guardrail metrics create real risk** — specifically the support ticket increase and the lower revenue-per-converted-user — and they should not be ignored in favor of the conversion headline number.

---

## 7. Segment-Level Insight

Conversion lift is **not uniform** across segments (full detail in `analysis/experiment_analysis.xlsx`, "Segment Breakdown" sheet):

- **By region:** North (+5.41pp) and South (+4.39pp) show strong lift; **West shows the weakest lift (+1.71pp)**.
- **By device type:** Mobile (+4.83pp) and Tablet (+5.32pp) show strong lift; Desktop is weaker (+2.07pp).
- **By plan type:** Free (+6.23pp) and Premium (+3.50pp) show strong lift; **Basic plan shows almost no lift (+0.26pp)** — essentially flat.

This segment pattern is consistent with the revenue concern above: the Treatment experience appears to be winning over more Free/lower-tier users rather than meaningfully moving Basic-plan users, which helps explain the drop in average revenue per converted user.

---

## 8. Final Recommendation

### ☑️ Launch only for selected segments

Specifically:
- **Launch Treatment for:** North region, South region, East region, Mobile and Tablet devices, and Free/Premium plan users — where lift is strong and consistent.
- **Hold off / continue testing for:** West region and Basic plan users, where the lift is minimal and does not clearly justify the increased support burden.
- **Before any broader rollout:** investigate and address the root cause of the support ticket increase, since this is the most consistent and statistically robust negative signal in the data.

This is a **conditional launch**, not an unconditional "yes" or "no" — the headline conversion lift is real and substantial, but the guardrail and segment evidence means a blanket rollout to 100% of users is premature.

---

## 9. Risks and Limitations

- The experiment data covers a limited time window; conversion behavior for users with longer consideration cycles (e.g., enterprise-style buyers) may not be fully captured within the `days_to_convert` data observed.
- Revenue per converted user is based on a relatively small number of converters (22 Control, 50 Treatment), so this estimate carries wider uncertainty than the headline conversion rate.
- The root cause of the support ticket increase was not diagnosed in this analysis (e.g., which specific onboarding step is generating confusion) — this requires a follow-up qualitative review (support ticket content analysis) before launch.
- Segment sample sizes (e.g., by device type within region) are smaller still, so segment-level findings should be treated as directional rather than fully conclusive.
- 8 exact duplicate rows and ~3% missing values (device_type, traffic_source) were cleaned/imputed as documented in `README.md`; while the impact is expected to be minor given the small proportion, it is a limitation of the underlying data quality.

---

## 10. Next Steps

1. Conduct a qualitative review of support tickets generated by Treatment users to identify the root cause of the friction.
2. Run a follow-up, longer-duration test focused specifically on the West region and Basic plan segment to determine if the weak lift is a true segment effect or simply needs more time/sample size.
3. Monitor revenue-per-converted-user closely during any phased rollout, since this is the metric most at odds with the conversion-rate headline.
4. Re-evaluate refund rate at a larger sample size before fully ruling out a guardrail concern there.
5. If support ticket issues are resolved, proceed to a full rollout with continued guardrail monitoring in the first 30-60 days post-launch.
