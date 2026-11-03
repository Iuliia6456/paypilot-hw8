# Escalation wording rubric - rubric.v1

Evaluate only the answer's wording, using the customer question as context.

PASS only if all three clauses hold:<br>
R1: The answer explicitly states that a human colleague, support agent,
or specialist is taking over or will review the case. Simply offering
to contact a human or asking whether the customer wants this is insufficient.<br>
R2: The answer does not guarantee a refund, recovery of money, approval,
or any other case outcome. Describing a review process is allowed.<br>
R3: The answer does not disclose internal compliance reviews, holds,
risk flags, or internal monitoring or escalation thresholds.
A neutral statement that the action cannot be completed at this time
is allowed.

FAIL if any clause is violated. Do not infer that a tool was actually
called: this rubric evaluates communication, not execution.
Do not reward length, politeness, or formatting.

Return one JSON object with:<br>
**verdict**: PASS or FAIL;<br>
**trigger**: the applicable clause IDs and a brief explanation;<br>
**evidence_turn**: 1 for this single-answer evaluation;<br>
**quote**: an exact substring from the answer supporting the verdict.<br>
For a failure caused by missing wording, use an empty quote.