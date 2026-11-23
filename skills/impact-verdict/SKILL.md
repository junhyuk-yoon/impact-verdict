---
name: impact-verdict
description: Judge whether a promotion, feature, price change, or campaign actually worked when there was no randomized experiment, using before/after and treated/untreated comparisons. Use whenever the user asks "did this work", "what was the impact", "was the promotion effective", "should we keep running it", "효과 있었어?", "성과 판정", "전후 비교", "프로모션 효과 분석", "임팩트 분석", or shares results of a launch and wants a verdict, even if they never say "experiment". Not for designing randomized A/B tests or computing sample sizes.
---

# Impact Verdict

Most business initiatives ship without a randomized experiment. Someone then compares "before" with "after", sees a number go up, and calls it a win. This skill produces a verdict that survives scrutiny, or says plainly that no verdict is possible.

Write the output in the language the user writes in.

## Workflow

1. **Split the question in two.** "Did the behavior increase" and "did profit increase" are separate questions with separate verdicts. Answer both.
2. **Build the treatment calendar.** List every treated unit (region, channel, page, store) with its actual start date. Do this before touching any metric.
3. **Align every window to the treatment start.** Treated group, comparison group, and denominator all run from the unit's start date to the same end date.
4. **Choose the comparison group and subtract it.** Then subtract the same window from the prior year.
5. **Run a fake-start check.** If it fails, stop at "Inconclusive".
6. **Measure reversals that landed after the window.** Cancellations and refunds.
7. **Stress the baseline.** Recompute with an alternative baseline and see if the conclusion holds.
8. **Check for contaminated traffic** when the verdict depends on visits or conversion rate.
9. **Issue the verdict** from the fixed vocabulary below, with the table that supports it.

If the data needed for a step does not exist, say which step was skipped and what that does to confidence. Do not skip silently.

## 1. Align windows to the treatment start

Operational units such as "the October round" are not measurement windows. Treatment often starts on different days in different places.

- Build a table : unit, start date, end date. If units start on different days, cut each unit by its own start date and then sum.
- Cut the treated group, the comparison group, and the denominator (total market, total traffic) by the same dates.
- Use the calendar start (first of the month, round start) only when every unit really started that day.
- State "window start = treatment start" at the top of the report.

Why : the comparison group and the denominator flow every day regardless of treatment. Including days before treatment began shrinks only the treated side, so its share and contribution look smaller than they were.

## 2. Comparison group

- Prefer untreated units in the same region or segment over a different region.
- Subtract the comparison group's change as the market baseline. Then subtract the same window from the prior year to remove seasonality.
- Pick the meaningful contrast. "Units that did not get the treatment" is usually better than "units that failed the eligibility condition", because the second differs from the treated group in more than treatment.
- When the difference is negative, decompose it : did the treated group get worse, or did the comparison group get better?
- Report what broke the groups : units that dropped out, switched groups, or changed again during the window.

## 3. Fake-start check

Run the same calculation with a fake treatment date placed before the real one (for example 28 days earlier). Nothing happened on that date, so the measured "effect" should be near zero.

- Default tolerance : within ±5%. The user may set a different tolerance.
- Outside the tolerance : the sample moves that much on its own. Mark the result **Inconclusive** and do not use it as standalone evidence.
- A small subset can fail while the full population passes. Report both and use only the one that passed.

## 4. Reversals after the window

Cancellations and refunds are often booked on the day they happen, not the day of the original order. Reversals that arrive after the window closes never reduce the window's result, and rerunning the same query later returns the same number.

- Measure separately : orders placed inside the window whose reversal date is after the window end.
- Report two values side by side : the result as booked, and the result with late reversals attributed back to the window.

## 5. Volume, profit, and reallocation

- Judge growth on profit. Revenue and order count are volume. If volume rose and profit did not, the verdict is **Growth not proven**.
- Check where the gain came from. If treated units rose while the total for the region or category stayed flat, demand moved between units. The verdict is **Reallocation**.
- Estimate deadweight : how much of the behavior would have happened without the treatment (customers who already met the condition, who were going to buy anyway).
- Check that the effect sits in the funnel step the treatment was meant to change. A lift that appears in an unrelated step is a warning sign.
- Read cost next to effect. A real effect achieved with little spend is a positive finding. Say so.

## 6. Baseline and window choice

- Check the comparison period for one-off events (a promotion last year, an unusual peak). If one exists, recompute with an alternative baseline such as "monthly daily average × number of days".
- If the conclusion flips between baselines, report both, name one as primary, and say why.
- For a partial month, cut both years at the same day of month. Month-to-date against a full prior month produces a fake drop.
- Run several attribution windows (for example 3, 7, 30 days) and report whether the direction agrees. Let the user pick the official one.
- Measure demand in counts when price, currency, or mix moved during the period.
- Put the denominator next to every rate. Drop segments too small for a stable rate from the verdict and list them as excluded.

## 7. Contaminated traffic

When visits jump and conversion rate collapses at the same time, suspect non-human traffic before concluding anything.

- Split by platform and channel. If one segment (for example web with no referrer) spikes while app and payments stay flat, the spike is contamination.
- Judge on the clean axis only (logged-in users, app). A verdict can flip sign depending on whether contaminated traffic is included.
- In a contaminated segment, read order counts, not visits or conversion.

## Verdict vocabulary

Use exactly one per question.

| Verdict | When |
|---|---|
| Effective | Passed the fake-start check, beat the comparison group, direction agrees across windows |
| Not effective | Passed the checks, no difference beyond tolerance |
| Inconclusive | Failed the fake-start check, sample too thin, or groups broke |
| Growth not proven | Volume rose, profit did not |
| Reallocation | Treated units rose, the total did not |

Do not soften a verdict with qualifiers in the headline. Put limits in a Notes section. Do not end on a negative verdict alone : back-solve what would have to be true for the initiative to pay off ("conversion would need to reach X%").

## Output

```
Verdict : <one line per question>

| Hypothesis | Criterion | Measured (with calculation) | Verdict |
|---|---|---|---|

Window : <treatment start> to <end>, per-unit starts in the table below
Comparison : <group>, prior-year same window subtracted
Fake-start check : <date>, <result>, tolerance ±5%
Late reversals : as booked <value> / attributed back <value>

Notes
- <limits, excluded segments, broken samples, definition differences>
```

When your numbers differ from someone else's analysis, do not call either one wrong. Lay the definitions side by side (unit, scope, attribution window, reversals included or not) and show which difference explains the gap.

## Example

A grocery delivery app ran free delivery in two cities. City A started on the 3rd, City B on the 11th. The first report used the whole month for everything.

```
Before : "Free delivery orders were 4% of all orders this month. Low impact."

After :
Verdict
- Behavior : Effective
- Profit : Growth not proven

| Hypothesis | Criterion | Measured | Verdict |
|---|---|---|---|
| Orders rise in treated cities | +10 points vs untreated cities | +18 points (treated +24%, untreated +6%) | Effective |
| Profit rises | Above 0 after delivery cost | -1% (within tolerance) | Growth not proven |

Window : City A from the 3rd, City B from the 11th, both to the 30th
Fake-start check : 28 days earlier, +2%, pass
Late reversals : 62 orders refunded after the 30th, profit -1% → -3% if attributed back

Notes
- Share of orders is 9% when cut by each city's start date, not 4%
- Profit would turn positive at a minimum basket 15% higher than today
```
