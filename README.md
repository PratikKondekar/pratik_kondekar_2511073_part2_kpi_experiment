# KPI Framework, Business Experiment Analysis & Decision Recommendation

**Student:** Pratik Kondekar
**Role:** Business Analyst (Subscription Digital Product Company)

---

## 1. Business Context

A subscription-based digital product company launched a new onboarding and activation campaign aimed at improving user conversion and early engagement. Users were randomly split into a **Control group** (existing onboarding experience) and a **Treatment group** (new campaign experience). Leadership needs a data-backed decision on whether to roll out the Treatment experience to all users.

### Business Problem Statement

1. **Decision to be made:** Whether to launch the new onboarding/activation campaign experience to 100% of users, replacing the existing onboarding flow.
2. **Who it impacts:** All new users going through signup/onboarding, the product/growth team that owns the experience, and the support team that handles post-signup tickets.
3. **Metric that should improve:** Paid Conversion Rate (the North Star metric for this experiment) — without unacceptably degrading guardrail metrics like support load, refunds, and revenue quality.
4. **Risks to monitor:** Increased support ticket volume, increased refund rate, lower revenue per converted user (a shift toward lower-value conversions), and uneven impact across regions/plans/devices.
5. **Evidence required before recommending:** A statistically significant lift in the primary metric (hypothesis test), guardrail metrics that do not show unacceptable regression, and consistent (or well-understood) results across key user segments.

---

## 2. Dataset Description

- **File:** `data/campaign_experiment_data.xlsx`
- **Rows:** 1,408 raw user-level records (1,400 after removing 8 exact duplicates)
- **Columns:** `user_id`, `signup_date`, `experiment_group` (Control/Treatment), `region`, `device_type`, `traffic_source`, `plan_type`, `visited_landing_page`, `started_trial`, `completed_onboarding`, `converted_to_paid`, `revenue_30d`, `support_tickets_30d`, `refund_requested`, `days_to_convert`, `engagement_score`.
- Group sizes after cleaning: **Control = 690**, **Treatment = 710**.

### Data Quality Checks Performed (Task 4)

| Check | Finding | Handling |
|---|---|---|
| Missing values | `device_type` (18 missing), `traffic_source` (24 missing) | Filled as `"Unknown"` |
| Group counts | Control = 690, Treatment = 710 (after dedup) | Confirmed reasonably balanced random split |
| Duplicate user IDs | 8 exact duplicate rows found | Removed entirely |
| Invalid binary values | 0 invalid values found in `visited_landing_page`, `started_trial`, `completed_onboarding`, `converted_to_paid`, `refund_requested` | No action needed — all values were valid 0/1 |
| Outliers in revenue | 4 high-revenue converters flagged via IQR method (upper bound ≈ $2,340) | Retained — these reflect legitimate premium-plan revenue, not data errors, but flagged for awareness |
| Segment distribution across groups | Verified region/device/traffic/plan distributions are reasonably balanced between Control and Treatment | See `analysis/experiment_analysis.xlsx`, "Group Balance Check" sheet |
| `days_to_convert` missing pattern | Missing only for non-converters (expected, since the field is undefined for users who never converted) | Confirmed as expected, not a data quality issue |

Full detail in `analysis/experiment_analysis.xlsx`, sheet "Data Quality Checks."

---

## 3. North Star Metric Selected

**Paid Conversion Rate** (`converted_to_paid` / total users).

- **Why this is the main success metric:** It is the metric most directly tied to revenue generation and is the ultimate measure of whether the new onboarding experience successfully turns signups into paying customers — everything upstream (visits, trials, onboarding completion) is a means to this end.
- **Why other metrics are supporting, not North Star:** Landing page visit rate, trial start rate, and onboarding completion rate are all *funnel diagnostics* — useful for understanding *why* conversion moved, but none of them alone represent business value the way a paid conversion does.
- **Connection to business growth:** Higher paid conversion directly increases recurring revenue and subscriber base, the core growth lever for a subscription business.
- **What could go wrong if optimized blindly:** Optimizing conversion rate alone, without guardrails, could mean acquiring lower-quality or lower-intent paying customers who churn quickly, generate excess support burden, or request refunds — exactly the risk surfaced in this experiment's results (see Section 6).

---

## 4. KPI Tree Summary

See `outputs/kpi_tree.png` and `screenshots/kpi_tree_preview.png` for the full visual.

- **North Star:** Paid Conversion Rate
- **Driver 1:** Top-of-Funnel Engagement (Landing Page Visit Rate) → sub-drivers: by Traffic Source, by Device Type
- **Driver 2:** Activation Quality (Onboarding Completion Rate) → sub-drivers: by Plan Type, by Engagement Score
- **Driver 3:** Conversion Experience (Trial Start Rate) → sub-drivers: by Region, by Days to Convert
- **Guardrails:** Refund Rate, Support Ticket Rate, Average Engagement Score, Revenue per Converted User

---

## 5. Experiment Analysis Approach

1. Cleaned and validated the raw dataset (`analysis/experiment_analysis.xlsx`, "Data Quality Checks").
2. Computed all required Control vs Treatment metrics (`outputs/experiment_summary.xlsx`, "Summary" sheet).
3. Broke down at least 3 metrics by 4 segments — region, device type, traffic source, plan type (`outputs/experiment_summary.xlsx`, "By [segment]" sheets).
4. Ran statistical significance tests on the primary metric and key guardrails (`analysis/experiment_analysis.xlsx`, "Statistical Tests").
5. Synthesized findings into a decision recommendation (`outputs/recommendation_memo.md`).

---

## 6. Hypothesis Test Summary

- **Metric tested:** Paid Conversion Rate
- **Test:** Two-proportion z-test, one-tailed (H1: Treatment > Control)
- **Result:** Control 3.19% vs Treatment 7.04% — z = 3.264, **p = 0.0011**
- **Decision:** Reject the null hypothesis — the lift is statistically significant at α = 0.05.

Full reasoning and guardrail test results in `analysis/hypothesis_test_notes.md`.

---

## 7. Guardrail Metrics Considered

| Guardrail | Control | Treatment | Statistically Significant? |
|---|---|---|---|
| Support ticket rate | 14.78% | 24.79% | ✅ Yes (p < 0.0001) — concerning increase |
| Refund rate | 0.00% | 0.42% | ❌ No (p = 0.087) — directionally worth monitoring |
| Average engagement score | 57.0 | 62.9 | ✅ Yes (p < 0.0001) — positive signal |
| Revenue per converted user | $1,630.10 | $770.41 | Not formally tested (small n), but a substantial and concerning drop |

**Conclusion:** Guardrail metrics do create real risk — primarily the support ticket increase and the drop in revenue per converted user — and the recommendation accounts for this rather than relying on the conversion lift alone.

---

## 8. Final Recommendation

**Launch only for selected segments.**

Strong, consistent lift in North, South, East regions, Mobile/Tablet devices, and Free/Premium plans supports launching there. West region and Basic plan users show minimal lift and do not yet justify the added support burden. The support ticket increase should be root-caused before any full rollout. Full detail and reasoning in `outputs/recommendation_memo.md`.

---

## 9. Assumptions and Limitations

- The two-proportion z-test on conversion rate was computed as a standard (two-tailed) z-test and interpreted in a one-tailed context relative to the stated hypotheses; this does not change the conclusion since the two-tailed p-value already clears the one-tailed threshold.
- Revenue outliers (4 high-value converters, IQR method) were retained as legitimate rather than removed, since the company's plan tiers can naturally produce high per-user revenue.
- Segment-level findings (e.g., West region, Basic plan) are based on smaller sub-samples and should be treated as directional rather than fully conclusive without a dedicated follow-up test.
- The root cause of the increased support ticket rate under Treatment was not diagnosed in this analysis — this is flagged as a required next step before a full launch.
- `days_to_convert` being missing for all non-converters was treated as expected/structural, not a data quality defect.

