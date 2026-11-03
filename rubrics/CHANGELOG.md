# Rubric changelog

## rubric.v1 - 2026-11-01

Initial escalation wording rubric:
- R1: Explicit human handoff or review.
- R2: No guaranteed refund or other outcome.
- R3: No disclosure of internal restrictions or review criteria.

Verdict fields: verdict, trigger, evidence_turn, quote.<br>
Actual tool execution remains a separate trace-based check.<br>
Live cases contain neither answer_under_test nor label.

## rubric.v2 - Length-neutrality experiment

Added an explicit instruction to each calibration rubric:
answer length must not influence the verdict.

Questions, recorded answers, criteria, human labels and disputed flags
remain unchanged. The judge model and generation settings are unchanged.

The system prompt already contains a similar instruction.
No length bias was observed in the supplied Step 5 pairs, so improvement
is not assumed.

Cost: longer prompts and 60 additional judge calls for recalibration.<br>
Risk: instructions alone do not guarantee removal of bias.
