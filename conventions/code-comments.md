# Convention — Code comments

Code explains itself through clear names; a comment is the exception, not the habit.

## General rule

- Never write comments that narrate what the code does line by line or step by step.
- A comment is allowed only if it conveys something the code alone cannot express (e.g. the reason
  behind a non-obvious choice).
- If a clearer name makes the comment redundant, rename instead of commenting.

## Applied to tests

- Every test states in **a single comment** (one line, above the test) the requirement or rule it
  verifies (qualified id).
- No other comment inside the test body: names and assertions must be enough on their own.
