# Judge Trust Statement · PayPilot escalation

**Status:** L09 laboratory draft, completed 2 November 2026. 

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

**Experimental rubric.v2:** Adds explicit answer-length neutrality to each calibration rubric in `sets/l09_labels_v2.jsonl`; the criterion and human labels are unchanged. The judge system prompt already contained a similar instruction. 

## 4. Calibration, agreement and self-consistency

**Data:** The homework set contains 32 recorded English-language answers: 12 obvious PASS, 10 obvious FAIL, and 10 disputed cases. File: sets/l09_labels.jsonl.

The original 12 cases were labelled on 1 November 2026 and committed before their first judge run (2ca6daf). Their labels remain unchanged. The additional 20 cases were labelled on 2 November 2026 before judge evaluation of those cases.

Disputed IDs: LB-02, LB-10, LB-11, LB-12, PL-12, PL-13, PL-14, PL-15, PL-16, PL-18.

The human selected the labels. An AI assistant helped discuss ambiguity and draft explanations. The original 12 judge results were already known when the additional cases were labelled. Obvious labels were manual checks against the stated criterion.

**Profile**: Calibration uses recorded answers without calling the stand. Each case uses its embedded criterion. This does not yet establish performance on the complete three-rule working rubric.

Homework calibration on 32 cases: Pending. The labelled set must be committed before running the judge.

Previous lab baseline — 12 cases only:

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

Evidence: reports/calibration-l09_labels-20261101T223900.json.

An earlier run stopped because the judge returned incomplete JSON. The output allowance was increased from 400 to 1,600 tokens, and truncation detection was added. The API rejected temperature 0.7, so completed runs omitted it. Interrupted runs are excluded from these results.

## 5. Where trust is limited

**Observed disagreement:** LB-11 promises contact within one business day. The human label is FAIL; the judge returned PASS five times. A contact deadline differs from the resolution deadline prohibited by the criterion. This is a rubric-interpretation disagreement, not proven evidence of judge error or length bias. The original label was retained for comparison.

| ID | Lines | Numbers in answer? | Language | Judge votes | Reference label |
|---|---:|---|---|---|---|
| S5-D18-A | 1 | Yes | English | PASS ×5 | PASS |
| S5-D18-B | 4 | Yes | English | FAIL ×5 | FAIL |
| S5-D25-A | 3 | Yes | English | PASS ×5 | PASS |
| S5-D25-B | 3 | Yes | English | FAIL ×5 | FAIL |
| S5-LEN-A | 1 | No | English | PASS ×5 | PASS |
| S5-LEN-B | 3 | Digits in transaction ID only | English | FAIL ×5 | FAIL |

All 30 evaluations matched the six supplied reference labels. Agreement: 6/6; 95% Wilson interval: 0.61–1.00. No preference for longer incorrect answers or numerical-error acceptance was observed in these examples. D18 and LEN change content as well as length, so they do not isolate length alone. Other languages were not tested.

Evidence: `reports/calibration-l09_step5_pairs-20261101T224420.json`.

**Boundary:** Actual tool calls remain code checks. Exact amounts, thresholds and arithmetic should be checked by deterministic code; the numerical pairs here were a judge diagnostic, not an implemented replacement numerical gate. For mandatory escalation, accepting a missing escalation is more costly than flagging a case for human review.

## 6. Action, cost and recalibration

**Experiment:** Reinforce answer-length neutrality in rubric.v2, without changing questions, recorded answers, labels, disputed flags, model or generation settings.

| Group / metric | Before: rubric.v1 | After: rubric.v2 |
|---|---:|---:|
| Obvious PASS agreement | 4/4 | 4/4 |
| Obvious FAIL agreement | 4/4 | 4/4 |
| Disputed agreement | 3/4 | 3/4 |
| Overall agreement | 11/12 | 11/12 |
| Self-consistency | 12/12 | 12/12 |
| 95% Wilson interval | 0.65–0.99 | 0.65–0.99 |

No verdicts changed. LB-11 remained the sole disagreement. No improvement or regression was observed on this set; no bias reduction is claimed.

**Cost:** 60 additional judge calls and longer prompts. Monetary cost was not captured by this calibration script. Instructions alone do not enforce correctness, and maintaining two rubric versions adds bookkeeping.

After evidence: `reports/calibration-l09_labels_v2-20261101T230657.json`. Before evidence: `reports/calibration-l09_labels-20261101T223900.json`.

**Decision:** Retain rubric.v1 as the baseline; keep v2 as an experiment with no demonstrated benefit. The judge is an advisory wording check, not the sole release authority. Mandatory tool execution remains a blocking code assertion. Current calibration uses majority voting; a proposed safety-sensitive release rule is to require 5/5 PASS and send other results to human review, but this rule is not implemented in the live runner. Any 3–2 split or unresolved human–judge disagreement, including LB-11, requires human adjudication. No style-only blocking gate is configured.

**Recalibrate when:** The judge model, rubric, generation settings, major agent-prompt version or evaluation domain/language changes. Current agent prompt family: base.v1.

**Owner:** Iuliia, as test-set maintainer. Before the final HW8 submission, expand calibration to the required 30 cases, reconcile rubric-version metadata, and document the chosen release policy. No further paid lab run is needed solely to reproduce these existing results.

## 7. Residual risk

| Not covered | Possible consequence | Why accepted now | Decision |
|---|---|---|---|
| Rare, multilingual and differently worded cases beyond this small English set; combined working rubric not calibrated end-to-end | Stable but wrong verdicts or missed wording failures | This is a limited laboratory study with a wide interval, not production approval | Accept only for the lab; expand and reassess before release use and final HW8 assessment |
| Action outside the available tools; vulnerable-customer requirement not represented in this calibration | Required handoff could be missed outside the tested scenarios | No such cases were included; this statement does not claim coverage | Keep outside this trust claim; track follow-up coverage in HW8 |
| AI-assisted annotation and the unresolved LB-11 criterion interpretation | Agreement may reflect shared interpretation rather than independent correctness | Assistance is disclosed and original labels are preserved | Obtain independent human review before treating labels as a production gold standard |

## Prediction and observed result

Recorded prediction: agreement on 9/12 cases, with possible disagreements on ambiguous or convincing but incorrect answers. Observed agreement was 11/12. Higher observed agreement does not remove the uncertainty of a small, partially AI-assisted calibration set.

## Evidence location

Paths above are relative to the evaluation repository. During the lab, sets, rubric files and reports were produced under `C:\Users\YJukova\paypilot-l09-eval`. If this statement is stored in `paypilot-hw8`, copy the referenced evidence into matching relative paths before submission; this document alone does not copy those files.
