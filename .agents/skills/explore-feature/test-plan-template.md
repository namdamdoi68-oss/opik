# `test-plan.md` format

The happy-path checklist — the dev↔QA handoff artifact. Emit it exactly in this shape, filled
from the resolved scope. It is posted to Jira (canonical) and written to a scratch path locally to
drive spec generation. **Never committed to the repo.**

## Template

```markdown
# Test plan — <LEAD-TICKET>: <feature title>

**Scope:** <one line: the user-facing behavior this gates>
**Change surface:** PR(s) #<n>, ticket(s) <KEY[, KEY…]>
**Version stamp:** <version> (origin/main)   **Gate spec:** tests_end_to_end/e2e/tests/_release-gate/<lead-ticket>.spec.ts

## Happy path
- [ ] Step 1 — <user action> → <observable result>
- [ ] Step 2 — <user action> → <observable result>

## Preconditions / seed
- <state the test seeds via the SDK/bridge before the browser opens>

## Not covered (out of scope for the gate)
- <edge cases, error paths, perf — flagged for QA's deeper pass>

## Open questions for QA
- <anything the skill was unsure about — the explicit feedback prompt>
```

## Rules

- **Happy path only.** One flow, the thing a user does when everything works. Not error paths,
  not edge cases — those go under "Not covered" for QA.
- **Observable results.** Each step names what the *user sees* (a row appears, a badge turns
  green), not an internal state change — this is what the spec will assert.
- **"Not covered" and "Open questions" are mandatory sections**, even if short. They are the
  dev↔QA boundary and the feedback surface that improves this skill. An empty "Open questions"
  means "I'm confident"; say that rather than deleting the section.
- **Multi-ticket:** the lead ticket titles the plan; reference the others by key. In any text
  posted to Jira, use the underscore form for tickets this PR does **not** resolve (`OPIK_7168`)
  so the Jira scanner doesn't false-link them; keep the hyphen for resolved tickets.
