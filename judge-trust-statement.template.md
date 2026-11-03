# Judge Trust Statement · <system and criterion>

**Status:** <draft/final and date>  
**Owner:** <name>

## Trust statement — six-row summary

| Item | Statement |
|---|---|
| Calibrated on | <case count, group counts, language, label dates, pre-run commit, judge model, scope> |
| Agreement | <matches/total, percentage, confidence interval, interval half-width ±X> |
| Self-consistency | <cases with identical votes/total, evaluations per case, actual temperature setting> |
| Where trust is limited | <observed disagreements, unstable cases and untested situations> |
| What we do | <chosen change, cost, before/after result, decision and human-review owner> |
| Recalibrate | <changes that require a new calibration> |

## 1. Context and requirement

**Requirement:** <source and required behaviour>

**Agent:** <model, prompt version and provider>  
**Judge:** <model and provider>

| Case | Expected action | Baseline: action observed / runs | Comparison: action observed / runs |
|---|---|---|---|
| <ID> | <action> | <count> | <count> |

Evidence: <report and trace paths>. State whether counts cover all runs.

## 2. Requirement split

| Requirement part | Evaluator | Level | Gate | Runs | Case |
|---|---|---|---|---|---|
| <checkable action or fact> | <code check> | <level> | <gate> | <runs> | <ID> |
| <remaining wording criterion> | <judge> | <level> | <gate> | <runs> | <IDs> |

Working set: <path>. Describe coverage gaps and whether live cases were run.
Distinguish repeated agent runs from repeated judge votes.

## 3. Rubric

**Version:** <version>

- Human follow-up: <explicit PASS and FAIL conditions>.
- Outcome promises: <explicit PASS and FAIL conditions>.
- Internal information: <explicit PASS and FAIL conditions>.

**Verdict fields:** verdict, trigger, evidence_turn, quote.
Specify allowed verdicts, exact-quote rules and how missing wording is represented.

Rubric file: <path>. Changelog: <path>.
State where the executable rubric is stored and whether calibration checks
individual criteria or the combined rubric.

## 4. Calibration, agreement and self-consistency

Dataset: <path>. Labels committed before first evaluation: <commit>.
Label dates: <dates>. Describe who labelled the data and any assistance.
Execution: <recorded answers or live agent; relevant profile if applicable>.

| Metric | Result |
|---|---|
| Case composition | <obvious PASS / obvious FAIL / disputed> |
| Agreement | <matches / total and percentage> |
| Confidence interval | <method, confidence level and limits> |
| Interval half-width | <±X; do not imply symmetry if the interval is asymmetric> |
| Obvious PASS agreement | <matches / total> |
| Obvious FAIL agreement | <matches / total> |
| Disputed agreement | <matches / total> |
| Self-consistency | <stable cases / total> |
| Evaluations per case | <count> |
| Case verdict | <aggregation rule> |
| Generation settings | <actual settings, including omitted parameters> |

Evidence: <report path>. Repeated votes are not independent new cases.

## 5. Where trust is limited

| Case | Human label | Judge votes / majority | Shared feature or disagreement |
|---|---|---|---|
| <ID> | <label> | <votes> | <explanation> |

| Human reference | Judge PASS | Judge FAIL |
|---|---|---|
| PASS | <agreements> | <false alarms> |
| FAIL | <missed failures> | <agreements> |

Define a miss and a false alarm relative to the human reference.
Separate interpretation disagreements from proven errors.

| Diagnostic case | Lines | Numbers present? | Language | Judge votes | Reference label |
|---|---|---|---|---|---|
| <ID> | <count> | <description> | <language> | <votes> | <label> |

Observed bias or absence of observed bias: <finding and limits>.
Evidence: <paths>.

## 6. Action, cost and recalibration

Change: <what changed and why>. Version: <version>.
Held constant: <questions, answers, criteria, labels, model and settings as applicable>.

| Group / metric | Before | After |
|---|---|---|
| Obvious PASS agreement | <result> | <result> |
| Obvious FAIL agreement | <result> | <result> |
| Disputed agreement | <result> | <result> |
| Overall agreement | <result> | <result> |
| Confidence interval | <limits> | <limits> |
| Self-consistency | <result> | <result> |
| Missed failures | <count> | <count> |
| False alarms | <count> | <count> |

Changed cases: <IDs and vote changes>.
Cost: <additional calls, measured money if available, maintenance>.
What worsened or remained unresolved: <finding>.
Decision: <retain/change/limit use and why>.
Review policy: <who reviews what; distinguish proposed from implemented>.
Recalibrate when: <triggers>.
Evidence: <before and after paths>.

## 7. Residual risk

| What we have not checked | What could go wrong | Why we accept this now | What we will do |
|---|---|---|---|
| <gap> | <consequence> | <reason and allowed scope> | <action, owner and deadline or review condition> |

Final scope of trust: <where this judge may and may not be used>.
