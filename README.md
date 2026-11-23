# Impact Verdict

English | [한국어](README.ko.md)

**A Claude skill that judges whether a promotion, feature, or price change actually worked when there was no randomized experiment.**

Give it the results of a launch. It lines up the comparison windows, checks whether the sample can support a verdict at all, and returns one of five fixed verdicts with the table behind it. Existing A/B testing skills assume a randomized test. This one is for the far more common case where you only have before and after.

## 1. Effect : a share that looked like 4% becomes 9%, and a "win" becomes "growth not proven"

Before

```
Free delivery orders were 4% of all orders this month. Low impact.
```

After

```
Verdict
- Behavior : Effective
- Profit : Growth not proven

| Hypothesis | Criterion | Measured | Verdict |
|---|---|---|---|
| Orders rise in treated cities | +10 points vs untreated | +18 points (treated +24%, untreated +6%) | Effective |
| Profit rises | Above 0 after delivery cost | -1% (within tolerance) | Growth not proven |

Window : City A from the 3rd, City B from the 11th, both to the 30th
Fake-start check : 28 days earlier, +2%, pass
Late reversals : profit -1% → -3% if refunds after the 30th are attributed back

Notes
- Share of orders is 9% when cut by each city's start date, not 4%
```

The first report counted the whole month for a treatment that started on different days in each city. The numbers are fictional.

## 2. Rules : seven checks before any verdict

| Check | What it does |
|---|---|
| Split the question | "Did behavior increase" and "did profit increase" get separate verdicts |
| Align windows | Treated group, comparison group, and denominator all start on the treatment start date |
| Comparison group | Untreated units in the same segment, minus the same window last year |
| Fake-start check | Rerun with a fake start date. Outside ±5% means Inconclusive |
| Late reversals | Refunds after the window are measured separately and shown side by side |
| Baseline stress | Recompute with another baseline and see if the conclusion holds |
| Contaminated traffic | Judge on the clean axis when visits spike and conversion collapses |

## 3. Verdicts : five fixed words, one per question

| Verdict | When |
|---|---|
| Effective | Passed the checks and beat the comparison group |
| Not effective | Passed the checks, no difference beyond tolerance |
| Inconclusive | Failed the fake-start check, thin sample, or broken groups |
| Growth not proven | Volume rose, profit did not |
| Reallocation | Treated units rose, the total did not |

## 4. Scope : after-the-fact judgment, not experiment design

- Use it for
  - Promotions, coupons, campaigns
  - Feature launches and page changes rolled out without a holdout
  - Price, fee, or policy changes
- Do not use it for
  - Designing a randomized A/B test
  - Sample size and significance calculations

## 5. Install : two commands, entered one at a time

Install as a plugin in Claude Code. Enter the commands one at a time. Pasting both lines together runs them as one command and fails.

```
/plugin marketplace add junhyuk-yoon/impact-verdict
```

Then install the plugin.

```
/plugin install impact-verdict@impact-verdict
```

Or copy the folder without the plugin system.

```bash
git clone https://github.com/junhyuk-yoon/impact-verdict.git
cp -r impact-verdict/skills/impact-verdict ~/.claude/skills/
```

For the Claude apps (claude.ai, desktop), zip the `skills/impact-verdict` folder and upload it in the Skills menu under Settings.

## 6. Usage : describe the situation in plain words

```
We ran a discount in three regions last month. Did it work?
```

```
Here are the before and after numbers for the new checkout page. Give me a verdict.
```

```
My analysis says +12% and finance says +3%. Which is right?
```

## 7. Language : the skill answers in the language you write in

- The skill file is written in English
- Output follows the language of your request
- All examples use fictional companies and numbers

## 8. Priority : your team's rules come first

- Thresholds and defaults in the skill are starting points that you can override
- An explicit instruction from you wins over the skill's default

## 9. License : MIT

---

This README follows the rules of [pyramid-writing](https://github.com/junhyuk-yoon/pyramid-writing). Every heading is a "name : conclusion" pair and ` : ` is the only separator.
