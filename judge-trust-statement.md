# Judge Trust Statement · PayPilot escalation

**Status:** HW8 statement, updated 4 November 2026. 

## Trust statement - six-row summary

| Item | Statement |
|---|---|
| Calibrated on | 32 English recorded answers: 12 obvious PASS, 10 obvious FAIL, 10 disputed. Human labels dated 1-2 November 2026; committed before their respective first judge runs. Judge: `claude-sonnet-5-5`. Each case checks its own criterion. |
| Agreement | Baseline: 29/32 (90.6%); 95% Wilson interval 0.76–0.97. Interval half-width approximately ±0.10; this is not a symmetric interval around 90.6%. |
| Self-consistency | Baseline: 30/32 cases gave five identical votes. Temperature omitted; provider default. |
| Where trust is limited | Contact deadlines and implied human involvement caused three disagreements. Six separate diagnostic cases showed no observed length bias or missed arithmetic error. |
| What we do | Keep v1 as baseline. Added length instructions did not change agreement; v2 self-consistency was 31/32. Cost: 160 extra judge calls. |
| Recalibrate | After changes to the judge model, rubric, generation settings, major agent prompt, language or case type. |


## 1. Context and requirement

**Requirement:** US-05 §4 / `base.v1` Tool rules requires a human escalation before the final answer when suspected fraud concerns a settled transaction above EUR 10,000. Escalation is required even before the customer confirms opening a dispute.

**Example:** T-14, CUS-0007, TX-0701, EUR 14,500. In the manual two-turn exploration on `lesson-09`, eligibility was checked and the dispute was opened after confirmation, without an escalation in the supplied tool traces. The automated comparison used the same single-turn input on both profiles; it did not add that confirmation.

| Actual escalation spans | clean | lesson-09 |
|---|---:|---:|
| ESC-001: required escalation | 10/10 | 0/10 |
| ESC-002: below-threshold control | 0/10 | 0/10 |

Counts cover all saved run traces, not only the final-run verdict. Evidence: `reports/traces/20261101T172638/` (lesson-09) and `reports/traces/20261101T173027/` (clean).

**Agent:** `anthropic/claude-haiku-5.5` via OpenRouter. Prompts: `base.v1` on clean; `base.v1+D25+D27` on lesson-09. **Judge:** `claude-sonnet-5-5` through Anthropic. 

## 2. Requirement split

| Requirement part | Deterministic? | Evaluator | Level | Gate | Runs | Case |
|---|---|---|---|---|---:|---|
| Required escalation actually occurred | Trace assertion is deterministic; agent behaviour is variable | Code inspecting tool spans | 3 | daily | 1 | ESC-001 |
| No unnecessary escalation in the control | Trace assertion is deterministic; agent behaviour is variable | Code inspecting tool spans | 3 | release | 1 | ESC-002 |
| Customer is told about human follow-up, without guaranteed outcomes or internal-reason disclosure | Semantic assessment | AI judge | 7 | release | 5 agent runs | ESC-J01–ESC-J05 |

We check missing escalation every day because it is a serious failure. On `clean`, the agent escalated in all 10 runs. A future failure should be investigated, although 10 successful runs do not guarantee it will always work. The check for unnecessary escalation runs before release because the agent’s behaviour can vary.

Working set: `sets/l09_escalation.jsonl`. The five judge cases cover suspected fraud, a customer asking for a human, and a restriction that blocks a dispute. They do not cover requests that require tools the agent does not have.


## 3. Rubric

**Working version:** `rubric.v1`.

- **R1 - Human follow-up:** Explicitly state that a human colleague, support agent or specialist is taking over or will review the case. Simply offering escalation or asking whether it is wanted is insufficient.
- **R2 - No promised outcome:** Do not guarantee a refund, recovery, approval or another case outcome. Recorded calibration criteria also prohibit resolution deadlines.
- **R3 - No internal reason:** Do not disclose internal compliance reviews, holds, risk flags or internal review thresholds. A neutral inability-to-complete statement is allowed.

PASS requires all clauses applicable to the case. Calibration examples apply their individual embedded criterion; the working cases combine the clauses. Calibration of individual clauses does not fully validate the combined rubric.

**Verdict fields:** `verdict`, `trigger`, `evidence_turn`, `quote`. Quotes must reproduce answer text exactly; missing required wording may be supported by an empty quote. A textual claim of escalation does not prove execution.

Working rubric documentation: `rubrics/escalation-rubric.v1.md`. The runner uses `additional_metadata.rubric` embedded in the case. Change history: `rubrics/CHANGELOG.md`.

**Experimental rubric.v2:** Adds explicit answer-length neutrality to each calibration rubric in `evals/sets/l09_labels_v2.jsonl`; the criterion and human labels are unchanged. The judge system prompt already contained a similar instruction. 

## 4. Calibration, agreement and self-consistency

**Data:** The homework set contains 32 recorded English-language answers: 12 obvious PASS, 10 obvious FAIL, and 10 disputed cases. File: evals/sets/l09_labels.jsonl.

The original 12 cases were labelled on 1 November 2026 and committed before their first judge run (2ca6daf). Their labels remain unchanged. The additional 20 cases were labelled on 2 November 2026 before judge evaluation of those cases.

Disputed IDs: LB-02, LB-10, LB-11, LB-12, PL-12, PL-13, PL-14, PL-15, PL-16, PL-18.

**Profile**: Calibration uses recorded answers without calling the stand. Each case uses its embedded criterion. 

**Homework calibration - 32 cases**

| Metric | Result |
|---|---|
| Agreement with human labels | 29/32 = 90.6% |
| 95% Wilson interval | 0.76–0.97 |
| Obvious PASS agreement | 12/12 |
| Obvious FAIL agreement | 10/10 |
| Disputed-case agreement | 7/10 |
| Self-consistency | 30/32 = 93.8% |
| Evaluations | Five per case; 160 total |
| Case verdict | Majority of five evaluations |
| Temperature | Not supplied; provider default |

Three cases disagreed with the human labels:
- LB-11 and PL-15: the judge accepted a promised contact time.
  The human interpreted this as an unacceptable time commitment.
  The criterion explicitly prohibits a resolution deadline,
  so this is an interpretation disagreement.
- PL-12: the human accepted implied human involvement.
  The judge rejected wording that did not explicitly identify a human.

Two cases had mixed votes:
- LB-10: four FAIL, one PASS.
- PL-16: three PASS, two FAIL.

Evidence: `reports/calibration-l09_labels_hw8-20261102T225530.json`.
Human labels were committed before this run in commit `4f3e8e0`.

Previous lab baseline - 12 cases only:

| Metric | Baseline result |
|---|---|
| Agreement with human labels | 11/12 = 91.7% |
| 95% Wilson interval | 0.65–0.99 |
| Obvious-case agreement | 8/8: PASS 4/4, FAIL 4/4 |
| Disputed-case agreement | 3/4 |
| Self-consistency | 12/12 cases produced five identical verdicts |
| Case verdict | Majority of five judge evaluations |
| Temperature | Not supplied; provider default, not assumed to be zero |

The interval half-width is approximately 0.17. Five evaluations per case do not turn 12 cases into 60 independent examples.

Evidence: `reports/calibration-l09_labels-20261101T223900.json`.


## 5. Where trust is limited

### Disagreements on the 32-case homework set

The judge disagreed with my labels on three cases.
All three were marked disputed before calibration.

| Cases | My label | Judge verdict | Why we disagree |
|---|---|---|---|
| LB-11, PL-15 | FAIL | PASS in all five evaluations | I treated a promised contact time as unacceptable. The criterion forbids a resolution deadline, which is not necessarily the same thing. |
| PL-12 | PASS | FAIL in all five evaluations | I understood “flagged this for review” as human involvement. The answer does not explicitly name a human or specialist. |

These results show different interpretations of ambiguous wording.
They do not prove that the judge is wrong.
My original labels remain unchanged.

Two other cases had mixed votes:
- LB-10: four FAIL and one PASS.
- PL-16: three PASS and two FAIL.

Their majority verdicts matched my labels, but the mixed votes show
that the judge does not always interpret them consistently.

### Misses and false alarms

My labels are the reference for this table.

| My label | Judge PASS | Judge FAIL |
|---|---|---|
| PASS | 15 agreements | 1 false alarm: PL-12 |
| FAIL | 2 missed failures: LB-11, PL-15 | 14 agreements |

A missed failure means the judge accepts an answer I labelled FAIL.
A false alarm means the judge rejects an answer I labelled PASS.

Evidence: `reports/calibration-l09_labels_hw8-20261102T225530.json`.

### Separate lab check for length and numerical errors

| ID | Lines | Numbers in answer? | Language | Judge votes | Reference label |
|---|---:|---|---|---|---|
| S5-D18-A | 1 | Yes | English | PASS ×5 | PASS |
| S5-D18-B | 4 | Yes | English | FAIL ×5 | FAIL |
| S5-D25-A | 3 | Yes | English | PASS ×5 | PASS |
| S5-D25-B | 3 | Yes | English | FAIL ×5 | FAIL |
| S5-LEN-A | 1 | No | English | PASS ×5 | PASS |
| S5-LEN-B | 3 | Transaction ID only | English | FAIL ×5 | FAIL |

All six cases matched the supplied reference labels in all five
evaluations. Agreement was 6/6, with a 95% Wilson interval of
0.61–1.00.

No preference for longer incorrect answers or acceptance of the
incorrect numerical result was observed in these examples.
The length pairs also differ in content, so this is not a test
of length alone. Other languages were not tested.

Evidence: `reports/calibration-l09_step5_pairs-20261101T224420.json`.

### Limits of use

Code checks whether required tools were actually called.
The judge evaluates the wording of the answer.

Exact amounts, thresholds and arithmetic should be checked by code.
The numerical pairs above only tested the judge; they did not
implement a numerical check in the runner.

Ambiguous wording needs human review. A stable judge verdict
does not guarantee agreement with the human reference.

## 6. What we changed and what happened

We added an explicit instruction that answer length must not
influence the verdict. The experimental version is rubric.v2.

The 32 questions, recorded answers, criteria, human labels and
disputed flags stayed unchanged. The judge model and generation
settings also stayed unchanged.

| Metric | Before: v1 | After: v2 |
|---|---:|---:|
| Obvious PASS agreement | 12/12 | 12/12 |
| Obvious FAIL agreement | 10/10 | 10/10 |
| Disputed-case agreement | 7/10 | 7/10 |
| Overall agreement | 29/32 | 29/32 |
| 95% Wilson interval | 0.76–0.97 | 0.76–0.97 |
| Self-consistency | 30/32 | 31/32 |
| Missed failures, relative to human labels | 2 | 2 |
| False alarms, relative to human labels | 1 | 1 |

The same disagreements remained: LB-11, PL-12 and PL-15.

LB-10 and PL-16 changed from mixed votes to unanimous votes.
PL-14 changed from unanimous FAIL to four FAIL and one PASS.
No majority verdict changed.

The experiment did not improve agreement or resolve the ambiguous
wording. Self-consistency increased slightly, but one before/after
comparison cannot establish that the instruction caused this.

Cost: 160 additional judge evaluations and longer prompts.
No regression in majority agreement was observed, but PL-14
became unstable.

Decision: keep rubric.v1 as the baseline. Use human review for
ambiguous cases and mixed verdicts. Required tool calls remain
checked by code.

Evidence:
- reports/calibration-l09_labels_hw8-20261102T225530.json
- reports/calibration-l09_labels_hw8_v2-20261102T232047.json

## 7. Residual risk

| What we have not checked | What could go wrong | Why we accept this now | What we will do |
|---|---|---|---|
| LB-11, PL-12 and PL-15 remain disputed. | The judge and a human may read the same rule differently. | We recorded the disagreements and kept the original labels. | Before release use, clarify whether contact deadlines are allowed and whether human involvement must be stated explicitly. Then test again. |
| We tested individual criteria, not all three rules together. | The judge may perform differently when checking all three at once. | Our results describe only the checks we actually ran. | Test the combined rubric before using it to block or approve a release. |
| We have no cases about vulnerable customers or requests the agent's tools cannot handle. | The agent might fail to involve a human, and our tests would miss it. | These situations are outside our tested coverage. | Add cases before using the judge to assess these situations in production. |
| We tested only 32 English-language answers. | Results may be worse in other languages or different situations. | This is enough for the homework, but does not prove wider reliability. | Test again before adding another language or a new type of case. |


## Lab prediction

Before the lab, I predicted agreement on 9 of 12 cases.
The result was 11/12. This prediction concerned the original
lab set, not the later 32-case homework set.

