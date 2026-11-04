# Judge Trust Statement · PayPilot escalation

**Status:** HW8 statement, updated 4 November 2026. 

## Trust statement - six-row summary

| Item | Statement |
|---|---|
| Calibrated on | 32 recorded English answers: 12 obvious PASS, 10 obvious FAIL, 10 disputed. Labels dated 1-2 November 2026 and committed before their respective first judge runs. Judge: `claude-sonnet-5-5`. Each case checks its own criterion. |
| Agreement | Baseline v1: 29/32 (90.6%), 95% Wilson interval 0.76–0.97, half-width approximately ±0.10. Disputed cases: 7/10, interval 0.40-0.89. After v3: 27/32 (84.4%), interval 0.68–0.93; disputed cases: 5/10, interval 0.24–0.76. |
| Self-consistency | Five evaluations per case. Baseline v1: 30/32 cases had identical votes; v3: 32/32. Temperature was omitted from the request; provider default was used. |
| Where trust is limited | Contact deadlines, implied human involvement and implied promises caused disagreements. All five v3 disagreements were unanimous, so consistent votes do not guarantee agreement with human labels. |
| What we do | Keep v1. The targeted v3 clarification did not improve agreement and cost 160 additional judge calls. |
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

## 6. Action, cost and recalibration

### Targeted change: rubric.v3

The earlier v2 experiment added a length instruction but did not
address the disagreements we found.

In v3, we clarified the relevant rules:
- R1: Name a human or a support, disputes or fraud team.
  "Escalated" or "flagged for review" alone is insufficient.
- R2: A contact or progress-update deadline is allowed.
  A guaranteed outcome or resolution deadline is not allowed.

We evaluated the same 32 recorded answers five times each.
Human labels remained unchanged. The judge model and request
settings stayed the same.

| Metric | Before: v1 | After: v3 |
|---|---:|---:|
| Obvious PASS agreement | 12/12 | 12/12 |
| Obvious FAIL agreement | 10/10 | 10/10 |
| Disputed agreement | 7/10 | 5/10 |
| Disputed agreement: 95% Wilson interval | 0.40–0.89 | 0.24–0.76 |
| Overall agreement | 29/32 (90.6%) | 27/32 (84.4%) |
| Overall 95% Wilson interval | 0.76–0.97 | 0.68–0.93 |
| Self-consistency | 30/32 | 32/32 |
| Missed failures relative to human labels | 2 | 4 |
| False alarms relative to human labels | 1 | 1 |

### What changed

LB-11, PL-12 and PL-15 remained disagreements.
LB-10 and PL-18 became additional disagreements: the judge accepted
wording about outcomes that "usually" happen.

All five disagreements were unanimous. This shows that five
matching judge votes are not enough to establish correctness.

The clarification did not improve agreement with the original
human labels. It made the boundary between a guarantee and an
implied promise more important. The before/after confidence
intervals overlap, so this small experiment does not establish
a general performance difference.

Cost: 160 additional judge calls and longer prompts.

### Decision and human review

Keep v1 as the baseline because v3 did not improve agreement.

For the calibration set, a person reviews:
- any case with mixed judge votes;
- any disagreement with the human label, even when all five
  judge votes agree.

For new answers, a person reviews mixed votes and answers that:
- promise a time for contact, updates or resolution;
- mention escalation without clearly naming a human or team;
- suggest that the customer will probably recover their money.

These are review triggers, not automatic FAIL conditions.
They apply even when the judge unanimously returns PASS.


### Recalibration

Recalibrate after changes to the judge model, rubric, generation
settings, major agent prompt, language or case type.

Evidence:
- reports/calibration-l09_labels_hw8-20261102T225530.json
- reports/calibration-l09_labels_hw8_v3-20261104T224642.json

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

