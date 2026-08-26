---
description: Analyzes a request (however trivial) from a business standpoint and produces an analysis on file, the first step of the spec-driven workflow. Delegates to the sdd-analyst subagent.
argument-hint: "<request to analyze, free text>"
---

# /sdd-analyse — Business analysis

The first command of the spec-driven workflow.
It delegates the analysis of the request `$ARGUMENTS` to the `sdd-analyst` subagent.

## Role

- You (the main session) are the **orchestrator**: you are not the one writing the analysis.
- The analysis is produced by the `sdd-analyst` subagent.
- You act as the **broker** between the subagent and the human for questions.
- You keep the **knowledge base** → convention
  `${CLAUDE_PLUGIN_ROOT}/conventions/knowledge-base.md` (orchestrator role): read it and follow it.

## Steps

1. Get the current date in ISO-8601 format with `date +%Y-%m-%d`.
2. Launch the `sdd-analyst` subagent via Task, passing it the request (`$ARGUMENTS`) and the current
   date.
3. If the subagent returns questions for the human → apply the convention
   `${CLAUDE_PLUGIN_ROOT}/conventions/question-brokering.md` (orchestrator role): put the questions
   to the user, resume the same subagent with `SendMessage` and repeat until no questions are left.
4. When the subagent has finished, **commit** what it produced, following the convention
   `${CLAUDE_PLUGIN_ROOT}/conventions/commit.md`.
5. Report to the user, schematically:
   - the path of the analysis file produced
   - the type of analysis: new, fix or evolution
   - the knowledge base entries you created or updated, with their paths; omit the line if there
     are none

## Delegating to Gemini (on request)

If the user asks to **delegate the analysis to Gemini / Google Antigravity** → do not launch
`sdd-analyst`: delegate to the bridge subagent `sdd-gemini-runner` (role `sdd-analyst`) following the
convention `${CLAUDE_PLUGIN_ROOT}/conventions/gemini-antigravity-delegation.md`.
