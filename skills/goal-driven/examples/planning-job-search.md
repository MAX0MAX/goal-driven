# Example: Planning - One-Month Job Search

## User Request

```text
Help me plan my job search for the next month.
```

## Clarification Notes

- The user wants an actionable plan, not generic career advice.
- The plan needs target role, location or remote preference, available weekly time, application volume, and review cadence.

## Goal Contract

```yaml
Goal Contract:
  goal: "Create a one-month job search plan that identifies target roles, weekly application and networking actions, review checkpoints, and evidence of progress."
  scope:
    in_scope:
      - "Define target role types, company filters, and location or remote preferences."
      - "Create a four-week cadence for applications, networking, resume tailoring, and follow-up."
      - "Define lightweight tracking fields for applications, conversations, responses, and next actions."
      - "Include weekly review checkpoints for adjusting targets and workload."
    out_of_scope:
      - "Guarantee interviews, offers, or hiring outcomes."
      - "Write every resume, cover letter, or outreach message unless separately requested."
      - "Apply to jobs on the user's behalf."
      - "Provide legal, visa, or immigration advice."
  success_criteria:
    - "The plan covers four weeks with specific recurring actions."
    - "The plan includes target role and company filters."
    - "The plan defines how progress will be tracked each week."
    - "The plan includes review checkpoints for changing strategy if results are weak."
  evidence:
    - criterion: "Four-week action plan"
      proof:
        - "Review the plan for weekly application, networking, tailoring, and follow-up actions."
    - criterion: "Target filters"
      proof:
        - "Check that role type, seniority, location or remote preference, and company filters are listed."
    - criterion: "Progress tracking"
      proof:
        - "Confirm the tracking fields include company, role, date applied, source, contact, status, follow-up date, and next action."
    - criterion: "Review checkpoints"
      proof:
        - "Confirm each week ends with a review step that adjusts target list, messaging, or workload based on results."
  guardrails:
    - "Escalate if target role, weekly availability, or location constraints are unknown."
    - "Do not promise employment outcomes."
    - "Do not recommend misrepresenting experience or applying where the user lacks required authorization."
    - "Keep the plan realistic for the user's stated weekly time budget."
```

## Why This Works

The contract makes a personal planning request measurable without pretending outcomes are controllable. It defines evidence through artifacts and cadence: target filters, weekly actions, tracking fields, and review checkpoints.

## Common Failure Mode

Avoid turning this into motivational advice. The contract should produce a concrete plan the user can execute and review week by week.
